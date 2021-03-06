---
title: Оперативные архивация и восстановление с помощью Azure Cosmos DB | Документация Майкрософт
description: Узнайте, как автоматически архивировать и восстанавливать базы данных с помощью Azure Cosmos DB.
keywords: архивация и восстановление, оперативная архивация
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: govindk
ms.openlocfilehash: 657b75e5e3bb5c35bb23221235e62298fc797046
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902677"
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Автоматическая оперативная архивация и восстановление с помощью Azure Cosmos DB
Azure Cosmos DB автоматически выполняет архивацию всех ваших данных с регулярными интервалами. Автоматическая архивация не влияет на производительность или доступность операций баз данных. Для обеспечения устойчивости в случае региональной аварии все архивные копии хранятся отдельно в другой службе хранилища, а также глобально реплицируются. Автоматические резервные копии предназначены для сценариев, при которых контейнер Cosmos DB случайно удаляется и затем требуется восстановление данных.  

В начале этой статьи приводится краткий обзор понятий избыточности и доступности данных в Cosmos DB, а затем рассматривается архивация. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Высокая доступность в Cosmos DB — обзор
Служба Cosmos DB создана по принципу [глобального распределения](distribute-data-globally.md). Она позволяет масштабировать пропускную способность между несколькими регионами Azure, выполнять отработку отказа на основе политик, а также предоставляет прозрачные API-интерфейсы с размещением в нескольких регионах. Azure Cosmos DB предлагает [доступность на уровне 99,99 % в соответствии с соглашением об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/cosmos-db) для всех учетных записей в пределах одного и нескольких регионов с нестрогой согласованностью и доступность для чтения на уровне 99,999 % для всех учетных записей базы данных в пределах нескольких регионов. Все операции записи в Azure Cosmos DB надежно фиксируются на локальных дисках с соблюдением кворума реплик в локальном центре обработки данных, прежде чем подтверждаются клиенту. Высокая доступность Cosmos DB основана на использовании локальных хранилищ и не зависит от технологий внешнего хранения. Кроме того, если ваша учетная запись базы данных связана с более чем одним регионом Azure, то ваши операции записи также реплицируются между другими регионами. Для масштабирования пропускной способности и доступа к данным с небольшой задержкой можно указать любое количество регионов чтения, связанных с учетной записью базы данных. В каждом регионе чтения (реплицированные) данные надежно хранятся в наборе реплик.  

Как показано на следующей схеме, отдельный контейнер Cosmos DB является [горизонтально секционированным](partition-data.md). "Секция" на этой схеме обозначается кружком, а высокая доступность каждой секции обеспечивается набором реплик. Это локальное распределение в одном регионе Azure (обозначается осью X). Затем каждая секция (с соответствующим набором реплик) глобально распределяется по нескольким регионам, связанным с учетной записью базы данных (например, на данном рисунке указаны три региона — восточная часть США, западная часть США и центральная Индия). Набор секций — это глобально распределенная сущность, состоящая из нескольких копий данных в каждом регионе (обозначается осью Y). Вы можете назначить приоритет регионам, связанным с учетной записью базы данных, и в случае аварии Cosmos DB прозрачно выполнит отработку отказа в следующий регион. Также можно вручную смоделировать отработку отказа, чтобы протестировать комплексную доступность приложения.  

На следующем рисунке показана высокая степень избыточности, обеспечиваемая Cosmos DB.

![Высокая степень избыточности, обеспечиваемая Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Высокая степень избыточности, обеспечиваемая Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Полная, автоматическая и оперативная архивация
Что, если вы удалили свой контейнер или базу данных? При использовании Cosmos DB не только ваши данные, но и архивные копии ваших данных остаются высоко избыточными и устойчивыми к региональным авариям. Сейчас такое автоматическое резервное копирование выполняется примерно каждые четыре часа, и последние две резервные копии всегда сохраняются. Если данные случайно удалены или повреждены, [обратитесь в службу поддержки Azure](https://azure.microsoft.com/support/options/) в течение восьми часов. 

Архивация не влияет на производительность или доступность операций баз данных. Cosmos DB выполняет архивацию в фоновом режиме, не используя подготовленные ЕЗ, не снижая производительность и не влияя на доступность базы данных. 

В отличие от данных, сохраненных внутри Cosmos DB, автоматические архивные копии хранятся в службе хранилища BLOB-объектов Azure. Для обеспечения эффективной передачи данных с небольшой задержкой моментальный снимок архивной копии передается в экземпляр хранилища BLOB-объектов Azure в том же регионе, который является текущим регионом записи вашей учетной записи базы данных Cosmos DB. Для обеспечения устойчивости к региональным авариям каждый моментальный снимок данных архивной копии в хранилище BLOB-объектов Azure повторно реплицируется в другой регион с помощью геоизбыточного хранилища (GRS). На следующей схеме показано, как весь контейнер Cosmos DB (в данном примере — все три основные секции в западной части США) архивируется в удаленную учетную запись хранилища BLOB-объектов в западной части США, а затем реплицируется через GRS в восточную часть США. 

На следующем рисунке показана периодически выполняемая полная архивация всех сущностей Cosmos DB в геоизбыточное хранилище Azure.

![Периодически выполняемая полная архивация всех сущностей Cosmos DB в геоизбыточное хранилище Azure](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Срок хранения моментального снимка
Как описано выше, Azure Cosmos DB делает моментальные снимки данных каждые четыре часа на уровне разделов. В любое время сохраняются только два последних моментальных снимка. Однако если удаляется база данных или контейнер, моментальные снимки для всех удаленных разделов контейнера или базы данных сохраняются в Azure Cosmos DB в течение 30 дней.

Чтобы использовать собственные моментальные снимки в API-интерфейсах SQL, воспользуйтесь следующими параметрами.

* Чтобы запланировать дополнительные резервные копии, используйте параметр экспорта в JSON в [инструменте переноса данных](import-data.md#export-to-json-file) в Azure Cosmos DB.

* Для периодического перемещения данных используйте [Фабрику данных Azure ](../data-factory/connector-azure-cosmos-db.md).

* Используйте [канал изменений](change-feed.md) Azure Cosmos DB для периодического считывания данных в процессе полного резервного копирования и отдельного считывания данных для поэтапных изменений и перемещения к конечному большому двоичному объекту. 

* Чтобы управлять "теплым" резервированием, можно периодически считывать данные из канала изменений и приостанавливать их запись в другую коллекцию. Таким образом, потребность в восстановлении данных отпадает, благодаря чему можно немедленно приступить к поиску ошибок в данных. 

> [!NOTE]
> Если вы решили подготовить пропускную способность для набора контейнеров на уровне базы данных, помните, что восстановление происходит на уровне учетной записи для всей базы данных. Если вы случайно удалили контейнер, запрос в службу поддержки необходимо отправить в течение восьми часов. Если в течение этого времени вы не обратитесь в поддержку, данные будет невозможно восстановить.

## <a name="restoring-a-database-from-an-online-backup"></a>Восстановление базы данных из оперативного архива

Если вы случайно удалите базу данных или контейнер, вы можете [отправить запрос в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) или [позвонить в службу поддержки Azure](https://azure.microsoft.com/support/options/), чтобы восстановить данные из последней автоматически созданной резервной копии. Поддержка предоставляется пользователям определенных планов, например Standard или Developer. На уровне Basic она не предоставляется. Дополнительные сведения о различных планах поддержки см. на [этой странице](https://azure.microsoft.com/support/plans/). 

Если необходимо восстановить базу данных после повреждения данных (включая удаление документов в контейнере), ознакомьтесь с разделом [Обработка поврежденных данных](#handling-data-corruption). Инструкции, приведенные в этом разделе, помогут вам выполнить дополнительные действия, чтобы предотвратить перезапись имеющихся резервных копий поврежденными данными. Чтобы восстановить определенный моментальный снимок архива, службе Cosmos DB требуется, чтобы данные были доступны в течение цикла архивации для данного снимка.

> [!NOTE]
> Восстановление коллекции или базы данных можно выполнить только после явного пользовательского запроса. Удалять или не удалять контейнер или базу данных сразу после согласования данных целиком зависит от пользователя. Если коллекции или базы данных не будут удалены, то затраты на единицы запросов, хранение и исходящий трафик увеличатся.

## <a name="handling-data-corruption"></a>Обработка поврежденных данных

Azure Cosmos DB сохраняет две последние резервные копии всех разделов в учетной записи базы данных. Эта модель хорошо работает при случайном удалении контейнера (коллекции документов, графа, таблицы) или базы данных, так как одну из последних версий можно восстановить. Однако в случае, когда пользователи могут повредить данные, Azure Cosmos DB может не знать об этом, и может произойти перезапись имеющихся резервных копий. 

Сразу после обнаружения повреждения пользователю следует удалить поврежденный контейнер (коллекцию, граф или таблицу), чтобы резервные копии не перезаписались поврежденными данными. И самое главное, пользователю необходимо обратиться в службу поддержки Майкрософт и создать особый запрос, указав в нем вторую степень серьезности. 

На изображении ниже показано создание запроса в службу поддержки на восстановление контейнера (коллекции, графа или таблицы) через портал Azure в случае случайного удаления или обновления данных в контейнере.

![Восстановление контейнера после ошибочного обновления или удаления данных в Cosmos DB](./media/online-backup-and-restore/backup-restore-support.png)

После завершения процесса восстановления для таких сценариев данные восстанавливаются в другую учетную запись (с суффиксом "-restored") и в другой контейнер. Это восстановление выполняется в другом месте, чтобы у клиента была возможность проверить данные и при необходимости переместить их. Восстановленный контейнер находится в том же регионе и имеет такие же единицы запросов и политики индексирования. Восстановленная учетная запись доступна для просмотра только пользователю, который является администратором или соадминистратором подписки.


> [!NOTE]
> Если восстановление данных выполнялось с целью исправления ошибки или просто тестирования, пользователю необходимо запланировать удаление восстановленной базы данных или контейнера сразу после выполнения задания, так как за используемую ими подготовленную пропускную способность будет взиматься дополнительная плата. 
## <a name="next-steps"></a>Дополнительная информация

Чтобы реплицировать базу данных в нескольких центрах обработки данных, ознакомьтесь со статьей [Как работает глобальное распределение данных в Azure Cosmos DB?](distribute-data-globally.md) 

Для обращения в службу поддержки Azure [отправьте запрос с портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

