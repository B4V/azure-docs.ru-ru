---
title: Примеры сценариев Azure CLI для базы данных SQL | Документация Майкрософт
description: Примеры сценариев Azure CLI для создания серверов, эластичных пулов, баз данных, а также брандмауэров базы данных SQL Azure и управления ими.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: 29f0db5fad69322200e2e533bea8ce95e8d4f19d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062528"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Примеры Azure CLI для базы данных SQL Azure

В следующей таблице содержатся ссылки на примеры сценариев Azure CLI для базы данных SQL Azure.

| |  |
|---|---|
|**Создание отдельной базы данных и эластичного пула**||
| [Создание отдельной базы данных и настройка правила брандмауэра](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Этот сценарий Azure CLI создает отдельную базу данных SQL Azure и настраивает правило брандмауэра на уровне сервера. |
| [Создание эластичных пулов и перемещение баз данных в составе пулов](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Этот сценарий Azure CLI создает эластичные пулы SQL и перемещает базы данных SQL Azure в составе пулов, а также изменяет вычислительную мощность.|
|**Масштабирование отдельной базы данных и эластичного пула**||
| [Масштабирование отдельной базы данных](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Этот пример сценария интерфейса командной строки масштабирует отдельную базу данных SQL Azure до другого уровня вычислительной мощности после запроса на получение сведений о размере базы данных. |
| [Масштабирование эластичного пула](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Этот пример сценария интерфейса командной строки масштабирует эластичный пул SQL, изменяя его вычислительную мощность.  |
| [Создание управляемого экземпляра и управление им](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) | Эти сценарии интерфейса командной строки демонстрируют создание управляемого экземпляра и управление им с помощью Azure CLI. |
|||
