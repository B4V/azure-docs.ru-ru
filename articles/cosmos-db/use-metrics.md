---
title: Мониторинг и отладка с помощью метрик в Azure Cosmos DB | Документация Майкрософт
description: Сведения об отладке распространенных проблем и мониторинге базы данных с помощью метрик в Azure Cosmos DB.
keywords: Метрики
services: cosmos-db
author: kanshiG
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: 5c9dded95fe3ae36a716544368e3dc44c9b86afe
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365498"
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Мониторинг и отладка с помощью метрик в Azure Cosmos DB

Azure Cosmos DB предоставляет метрики пропускной способности, хранилища, согласованности, доступности и задержки. На [портале Azure](https://portal.azure.com) можно просмотреть общее представление этих метрик. Более детальные метрики доступны в клиентских пакетах SDK и [журналах диагностики](./logging.md).

В этой статье рассматриваются распространенные варианты использования Azure Cosmos DB, а также представлены сведения об анализе и отладке проблем в этой базе данных на основе метрик. Сбор метрик осуществляется каждые пять минут. Они хранятся в течение семи дней.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Сведения о количестве успешных запросов и запросов, приводящих к ошибкам

Чтобы приступить к работе, перейдите на [портал Azure](https://portal.azure.com) и откройте колонку **Метрики**. В этой колонке найдите диаграмму **Число запросов, которые превысили емкость, за минуту**. На этой диаграмме показано общее количество запросов за минуту, разбитых по коду состояния. Дополнительные сведения о кодах состояния HTTP для Azure Cosmos DB см. в [этой статье](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Наиболее распространенный код ошибки — 429 (ограничение частоты/регулирование). Он означает, что частота запросов к Azure Cosmos DB превышает подготовленную пропускную способность. Чтобы решить эту проблему, чаще всего следует [увеличить число единиц запроса](./set-throughput.md) определенной коллекции.

![Число запросов в минуту](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Определение распределения пропускной способности по секциям

При использовании масштабируемого приложения важно иметь достаточное количество ключей секции. Чтобы определить распределение пропускной способности для любого секционированного контейнера, перейдите в колонку **Метрики** на [портале Azure](https://portal.azure.com). На вкладке **Пропускная способность** на диаграмме **Max consumed RU/second by each physical partition** (Максимальное число потребленных единиц запроса каждой физической секцией за секунду) отображаются показатели хранилища. На графике ниже приведен пример неравномерного распределения данных, о чем свидетельствует секция слева. 

![Высокая интенсивность использования секции в 15:05](media/use-metrics/metrics-17.png)

Неравномерное распределение пропускной способности может привести к образованию секций с *высокой нагрузкой*, из-за чего могут возникать отрегулированные запросы и может потребоваться повторное секционирование. Дополнительные сведения о секционировании в Azure Cosmos DB см. в [этой статье](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Определение распределения ресурсов хранения по секциям

При использовании масштабируемого приложения важно иметь достаточное количество секций. Чтобы определить распределение пропускной способности для любого секционированного контейнера, перейдите в колонку "Метрики" на [портале Azure](https://portal.azure.com). На вкладке "Пропускная способность" на диаграмме Max consumed RU/second by each physical partition (Максимальное число потребленных единиц запроса каждой физической секцией за секунду) отображаются показатели хранилища. На графике ниже приведен пример неравномерного распределения данных, о чем свидетельствует секция слева. 

![Пример неравномерного распределения данных](media/use-metrics/metrics-07.png)

Чтобы определить, какой ключ секции искажает распределение, щелкните секцию на диаграмме. 

![Ключ секции, искажающий распределение](media/use-metrics/metrics-05.png)

После определения ключа секции, искажающего распределение, вы можете повторно разбить контейнер с большим числом распределенных ключей секции. Дополнительные сведения о секционировании в Azure Cosmos DB см. в [этой статье](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Сравнение размера данных и размера индекса

В базе данных Azure Cosmos DB общий использованный объем хранилища зависит от размера данных и размера индекса. Обычно размер индекса — это часть объема данных. В колонке "Метрики" [портала Azure](https://portal.azure.com) на вкладке "Хранилище" можно просмотреть сведения о потреблении хранилища на основе данных и индекса. 

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Чтобы сэкономить пространство индекса, вы можете настроить [политику индексирования](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Устранение причины медленного выполнения запросов

В пакетах SDK для API SQL можно просмотреть статистику выполнения запросов в базе данных Azure Cosmos DB. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 "SELECT * FROM c WHERE c.city = 'Seattle'", 
 new FeedOptions 
 { 
 PopulateQueryMetrics = true, 
 MaxItemCount = -1, 
 MaxDegreeOfParallelism = -1, 
 EnableCrossPartitionQuery = true 
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id 
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

Свойство *QueryMetrics* предоставляет подробные сведения о длительности выполнения каждого компонента запроса. Основная причина длительного выполнения запросов заключается в сканировании (запрос не может использовать индексы). Эту проблему можно решить, задав более эффективные условия для фильтра.

## <a name="next-steps"></a>Дополнительная информация

Узнав принципы мониторинга и отладки неисправностей на основе метрик портала Azure, вы, возможно, захотите более подробно изучить вопрос, как улучшить производительность базы данных. Для этого ознакомьтесь со следующими статьями:

* [Проверка производительности и масштабирования с помощью Azure Cosmos DB](performance-testing.md)
* [Советы по повышению производительности для Azure Cosmos DB](performance-tips.md)
