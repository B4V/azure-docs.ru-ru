---
title: Прием данных в обозревателе данных Azure
description: Сведения о различных способах приема данных в обозревателе данных Azure.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 94f96d949f2a05f71e9565fdcbc7b48ed2c2a5c5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972665"
---
# <a name="azure-data-explorer-data-ingestion"></a>Прием данных в обозревателе данных Azure

Прием данных — это процесс, используемый для загрузки записей данных из одного или нескольких источников для создания или обновления таблицы в обозревателе данных Azure. После принятия данные становятся доступными для запроса. На следующей схеме показан сквозной поток работы в обозревателе данных Azure, в том числе прием данных **(2)**.

![Поток данных в целом](media/ingest-data-overview/overall-data-flow.png)

Служба управления данными в обозревателе данных Azure, которая отвечает за прием данных, предоставляет следующие функциональные возможности:

1. **Извлечение данных**. Извлечение данных из внешних источников (концентраторов событий) или считывание запросов на прием из очереди Azure.

1. **Пакетная обработка**. Пакетная обработка данных, поступающих в ту же базу данных и таблицу, для оптимизации пропускной способности приема.

1. **Проверка**. Предварительная проверка и преобразование формата при необходимости.

1. **Управление данными**. Сопоставление схемы, упорядочение, индексирование, кодирование и сжатие данных.

1. **Точка сохранения в потоке приема**. Управление нагрузкой приема в модуле и обработка повторных попыток при временных сбоях.

1. **Фиксация приема данных**. Эта операция делает данные доступными для запроса.

> [!NOTE]
> Эффективная политика хранения получаемых данных является производной от политики хранения базы данных. Дополнительные сведения см. в статье о [политике хранения](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy). Для приема данных требуется разрешение **Принимающей таблицы** или **Принимающей базы данных**.

## <a name="ingestion-methods"></a>Методы приема

Обозреватель данных Azure поддерживает несколько методов приема, каждый из которых имеет собственные целевые сценарии, преимущества и недостатки. Обозреватель данных Azure предлагает соединители для общих служб, приема данных программным образом с помощью пакетов SDK и прямого доступа к модулю в целях изучения.

### <a name="ingestion-using-connectors"></a>Прием с помощью соединителей

Обозреватель данных Azure в настоящее время поддерживает соединитель концентратора событий, которым можно управлять с помощью мастера управления на портале Azure. Дополнительные сведения см. в статье [Краткое руководство. Прием данных из концентратора событий в обозреватель данных Azure](ingest-data-event-hub.md).

### <a name="programmatic-ingestion"></a>Прием данных программным образом

Обозреватель данных Azure предоставляет пакеты SDK, которые можно использовать для запросов и приема данных. Прием данных программным образом оптимизирован для снижения затрат на прием путем минимизации количества транзакций с хранилищем во время процесса приема и после него.

**Доступные пакеты SDK и проекты с открытым исходным кодом**:

Kusto предоставляет клиентский пакет SDK, с помощью которого можно принимать и запрашивать данные:

* [Пакет SDK для Python](https://docs.microsoft.com/azure/kusto/api/python/kusto-python-client-library)

* [ПАКЕТ SDK .NET](https://docs.microsoft.com/azure/kusto/api/netfx/about-the-sdk)

* [пакет SDK для Java](https://docs.microsoft.com/azure/kusto/api/java/kusto-java-client-library)

* [REST API](https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Методы приема данных программным образом**:

* Прием данных непосредственно в модуль обозревателя данных Azure (лучше всего подходит для просмотра и создания прототипов):

  * **Встроенный прием данных**. Команда управления (.ingest inline), содержащая данные в общем канале, предназначена для тестирования ad-hoc.

  * **Прием из запроса**. Команда управления (.set, .set-or-append, .set-or-replace), указывающая на результаты запроса, используется для создания отчетов или небольших временных таблиц.

  * **Прием из хранилища**. Команда управления (.ingest into) с данными, хранящимися во внешнем хранилище (например, в хранилище BLOB-объектов Azure), которая выполняет эффективный массовый прием данных.

* Прием данных с помощью службы управления данными обозревателя данных Azure (надежный прием данных с высокой пропускной способностью):

  * [**Пакетный прием данных**](https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (предоставляется пакетом SDK). Клиент отправляет данные в хранилище BLOB-объектов Azure (обозначено службой управления данными обозревателя данных Azure) и отправляет уведомление в очередь Azure. Это рекомендуемый метод для надежного, недорогостоящего приема большого объема данных.

**Задержка для различных методов**:

| Метод | Latency |
| --- | --- |
| **Встроенный прием** | Немедленно |
| **Прием из запроса** | Время запроса + время обработки |
| **Прием из хранилища** | Время загрузки + время обработки |
| **Прием с постановкой в очередь** | Время пакетной обработки + время обработки |
| |

Время обработки зависит от размера данных. Обычно обработка занимает меньше нескольких секунд. Время пакетной обработки по умолчанию — 5 минут.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Выбор наиболее подходящего метода приема

Прежде чем начать прием данных, задайте себе следующие вопросы.

* Где находятся мои данные? 
* Каков формат данных и можно ли его изменить? 
* Какие обязательные поля должны запрашиваться? 
* Каковы ожидаемые тома данных и скорость? 
* Сколько типов событий ожидается (показано в виде количества таблиц)? 
* Как часто в схеме событий ожидаются изменения? 
* Сколько узлов будут создавать данные? 
* Какова исходная ОС? 
* Каковы требования к задержке? 
* Можно ли использовать один из существующих управляемых конвейеров приема? 

Для организаций с существующей инфраструктурой, основанной на таких службах обмена сообщениями, как концентратор событий, наиболее подходящим решением будет использование соединителя. Прием с постановкой в очередь подходит для больших объемов данных.

## <a name="supported-data-formats"></a>Поддерживаемые форматы данных

Для всех методов приема, отличных от приема из очереди, данные необходимо отформатировать в один из поддерживаемых форматов, чтобы обозреватель данных Azure мог их проанализировать.

* CSV, TSV, PSV, SCSV, SOH
* JSON (разделенный по строкам, многострочный), Avro
* ZIP и GZIP 

> [!NOTE]
> При приеме данных типы данных определяются на основе целевых столбцов таблицы. Если запись является неполной или поле не может быть проанализировано в необходимом типе данных, соответствующие столбцы таблицы заполняются значениями NULL.

## <a name="schema-mapping"></a>Сопоставление схем

Сопоставление схем помогает детерминировано привязать поля исходных данных к столбцам таблицы назначения.

* [Сопоставление CSV](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#csv-mapping) (необязательно) работает со всеми форматами, состоящими из рядов, может передаваться в качестве параметра команды приема данных или быть [предварительно создано в таблице](https://docs.microsoft.com/azure/kusto/management/tables?branch=master#create-ingestion-mapping) и вызываться из параметра команды приема.
* [Сопоставление JSON](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#json-mapping) (обязательно) и [сопоставление Avro](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#avro-mapping) (обязательно) могут передаваться в качестве параметра команды приема данных или [быть предварительно созданы в таблице](https://docs.microsoft.com/azure/kusto/management/tables#create-ingestion-mapping) и вызываться из параметра команды приема.

## <a name="next-steps"></a>Дополнительная информация

[Краткое руководство. Прием данных из концентратора событий в обозреватель данных Azure](ingest-data-event-hub.md)

[Краткое руководство. Прием данных с помощью библиотеки Python в обозревателе данных Azure](python-ingest-data.md)

