---
title: Заметки о выпуске обновления 3 для службы приложений в Azure Stack | Документация Майкрософт
description: Узнайте о том, что находится в пакете обновления 3 Службы приложений Azure в Azure Stack, об известных проблемах и откуда скачать обновление.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: anwestg
ms.reviewer: sethm
ms.openlocfilehash: 3e88e0a3337eafdd25c9c0cc655912a4cdbd3b68
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079537"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>Служба приложений Azure в заметках о выпуске обновления 3 Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этих заметках о выпуске описываются улучшения и исправления в обновлении 3 Службы приложений Azure в Azure Stack и известные проблемы. Известные проблемы можно разделить на проблемы, которые непосредственно относятся к процессу развертывания, обновления, и проблемы со сборкой (после установки).

> [!IMPORTANT]
> Прежде чем развертывать службу приложений Azure, примените обновление 1807 к интегрированной системе Azure Stack или разверните последний пакет средств разработки Azure Stack 1.3.
>
>

## <a name="build-reference"></a>Указание сборки

Номер сборки обновления 3 Службы приложений Azure в Azure Stack — **74.0.13698.31**

### <a name="prerequisites"></a>Предварительные требования

Перед началом развертывания ознакомьтесь со статьей [Подготовка к работе со службой приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md).

Прежде чем начать обновление Службы приложений Azure в Azure Stack до версии 1.3, убедитесь, все ли роли готовы в инструменте администрирования Службы приложений Azure на портале администрирования Azure Stack

![Состояние роли службы приложений](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Новые функции и исправления

Обновление 3 Службы приложений Azure в Azure Stack включает в себя следующие улучшения и исправления.

- Поддержка использования SQL Server Always On для базы данных поставщика ресурсов службы приложений Azure.

- Добавлен новый параметр среды в сценарий справки "Создание AADIdentityApp" для помощи и нацеливания на разные регионы AAD.

- Обновления для **клиента, администратора службы приложений, портала функций и средств Kudu**. Согласованы с версией пакета SDK для портала Azure Stack.

- Обновления основной службы для повышения надежности и отображения сообщений об ошибках упрощают диагностику распространенных проблем.

- **Реализованы обновления следующих исполняющих сред и инструментов**:
  - Добавлено ASP.Net Core 2.1.2
  - Добавлено NodeJS 10.0.0
  - Добавлено Zulu OpenJDK 8.30.0.1
  - Добавлены Tomcat 8.5.31 и 9.0.8
  - Добавлены PHP версии:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Добавлено Wincache 2.0.0.8
  - Обновлено Git для Windows для версии 2.17.1.2
  - Обновлено Kudu до 74.10611.3437
  
- **Обновления базовой операционной системы всех ролей**.
  - [Обновление стека обслуживания для Windows Server 2016 для x64-разрядных систем (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [Накопительное обновление для Windows Server 2016 для x64-разрядных систем от июля 2018 г. (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Инструкция после обновления (необязательно)

Для клиентов, желающих перейти на автономную базу данных для существующей Службы приложений Azure на развернутой Azure Stack, нужно выполнить следующие действия после завершения обновления Службы приложений Azure в Azure Stack 1.3.

> [!IMPORTANT]
> Эта процедура занимает примерно 5–10 минут.  Эта процедура включает в себя завершение существующих сеансов входа в базу данных.  Спланируйте время простоя, чтобы перенести и проверить работу Службы приложений Azure на основе Azure Stack после миграции.
>
>

1. Добавьте [базы данных службы приложений (appservice_hosting и appservice_metering) в группу доступности](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Включите автономную базу данных
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Преобразование базы данных с частичным наполнением.  Этот шаг приведет к простою, так как все активные сеансы будут завершены.

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

Проверка

1. Включите SQL Server

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Проверьте текущий режим работы
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Известные проблемы (после установки)

- Рабочим ролям не удается связаться с файловым сервером, если служба приложений развернута в существующей виртуальной сети и файловый сервер доступен только в частной сети.  Это также описано в документации по развертыванию Службы приложений Azure в Azure Stack.

Если вы решили выполнить развертывание в существующей виртуальной сети с использованием внутреннего IP-адреса для подключения к файловому серверу, необходимо добавить правило безопасности для исходящего трафика, разрешающее передачу трафика SMB между подсетью рабочей роли и файловым сервером. Для этого перейдите к группе безопасности сети WorkersNsg на портале администрирования и добавьте правило безопасности для исходящего трафика со следующими свойствами.
 * Источник: "Любой".
 * Диапазон исходных портов: *.
 * Назначение: "IP-адреса".
 * Диапазон IP-адресов назначения: диапазон IP-адресов вашего файлового сервера.
 * Диапазон конечных портов: 445.
 * Протокол: TCP.
 * Действие: "Разрешить".
 * Приоритет: 700.
 * Имя: Outbound_Allow_SMB445.

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Известные проблемы для облачных администраторов, работающих со службой приложений Azure в Azure Stack

Обратитесь к документации в разделе [Обновление 1807 для Azure Stack](azure-stack-update-1807.md)

## <a name="next-steps"></a>Дополнительная информация

- Обзор службы приложений Azure в Azure Stack см. в [этой статье](azure-stack-app-service-overview.md).
- Дополнительные сведения о том, как подготовиться к развертыванию службы приложений Azure в Azure Stack, см. в разделе [Подготовка к работе со службой приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md).
