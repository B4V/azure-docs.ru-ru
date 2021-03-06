---
title: Руководство по развертыванию заданий Azure Stream Analytics с помощью CI/CD и Azure DevOps Services
description: В этой статье описывается развертывание заданий Stream Analytics с помощью CI/CD и Azure DevOps Services.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 07/10/2018
ms.openlocfilehash: 0f729725a04b19a513ca92953e997b51e4558884
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986271"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>Руководство. Развертывание заданий Azure Stream Analytics с помощью CI/CD и Azure Pipelines
В этом руководстве описано, как настроить непрерывную интеграцию и развертывание для задания Azure Stream Analytics с помощью CI/CD и Azure Pipelines. 

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Добавление проекта в систему управления версиями
> * Создание конвейера сборки в Azure Pipelines
> * Создание конвейера выпуска в Azure Pipelines
> * Автоматическое развертывание и обновление приложения

## <a name="prerequisites"></a>Предварительные требования
Чтобы начать, у вас должны быть следующие компоненты:

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Установите [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) и рабочие нагрузки **разработки Azure** или **службы хранения и обработки данных**.
* Создайте [проект Stream Analytics в Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Создайте организацию [Azure DevOps](https://visualstudio.microsoft.com/team-services/).

## <a name="configure-nuget-package-dependency"></a>Настройка зависимостей пакета NuGet
Для автоматического построения и развертывания на любом компьютере необходимо использовать пакет NuGet `Microsoft.Azure.StreamAnalytics.CICD`. Он предоставляет MSBuild, локальное выполнение и средства развертывания, которые поддерживают процесс непрерывной интеграции и развертывания проектов Stream Analytics Visual Studio. Дополнительные сведения см. на странице [Средства Stream Analytics CI/CD](stream-analytics-tools-for-visual-studio-cicd.md).

Добавьте **packages.config** в каталог проекта.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>Публикация решения Visual Studio в новом репозитории Azure Repos Git

Поместите исходные файлы приложения в проект Azure DevOps, чтобы создавать сборки.  

1. Создайте новый локальный репозиторий Git, выбрав **Добавить в систему управления версиями**, а затем**Git** в строке состояния в правом нижнем углу Visual Studio. 

2. В представлении **Синхронизация** в **Team Explorer** нажмите кнопку **Опубликовать репозиторий Git** в разделе **Принудительная отправка в Azure DevOps Services**.

   ![Принудительная отправка репозитория Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. Проверьте адрес электронной почты и в раскрывающемся списке **Домен Azure DevOps Services** выберите организацию. Введите имя репозитория и выберите **Опубликовать репозиторий**.

   ![Принудительная отправка репозитория Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    При публикации репозитория в вашей организации создается новый проект с тем же именем, что и у локального репозитория. Чтобы создать репозиторий в существующем проекте, щелкните **Расширенный** рядом с **именем репозитория** и выберите нужный проект. Код можно просматривать в браузере, выбрав **Просмотреть на веб-сайте**.
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Настройка непрерывной поставки с помощью Azure DevOps
Конвейер сборки в Azure Pipelines описывает рабочий процесс, состоящий из шагов сборки, которые выполняются последовательно. Дополнительные сведения [о конвейерах сборки в Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav). 

Конвейер выпуска Azure Pipelines описывает рабочий процесс, развертывающий пакет приложения в кластере. При совместном использовании конвейер сборки и конвейер выпуска выполняют весь рабочий процесс от получения файлов с исходным кодом до запуска приложения в кластере. Дополнительные сведения [о конвейерах выпуска Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts).

### <a name="create-a-build-pipeline"></a>Создание конвейера сборки
Откройте браузер и перейдите к только что созданному проекту [Azure DevOps](https://app.vsaex.visualstudio.com/). 

1. На вкладке **Сборка и выпуск** выберите **Сборки**, а затем **+ создать**.  Выберите **Git Azure DevOps Services** и щелкните **Продолжить**.
    
    ![Выбор источника](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. В разделе**Выбор шаблона** щелкните **Пустой процесс**, чтобы запустить пустой конвейер.
    
    ![Выбор шаблона сборки](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. В разделе **Триггеры** включите непрерывную интеграцию, установив флажок рядом с состоянием триггера **Включить непрерывную интеграцию**.  Щелкните **Сохранить и поместить в очередь**, чтобы выполнить сборку вручную. 
    
    ![Состояние триггера](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. Сборки также активируются после принудительного запуска или возврата. Чтобы проверить ход сборки, перейдите на вкладку **Сборки**.  Убедившись, что сборка запускается успешно, необходимо создать конвейер выпуска, который развертывает приложение в кластер. Щелкните правой кнопкой мыши троеточие рядом с конвейером сборки и выберите пункт **Правка**.

5.  В **Задачах**, введите "Размещен" в качестве значения параметра **Очередь агента**.
    
    ![Выбор очереди агентов](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. На **этапе 1**, щелкните **+** и добавьте задачу **NuGet**.
    
    ![Добавление задачи NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. Разверните **Расширенный** и добавьте `$(Build.SourcesDirectory)\packages` в **каталог назначения**. Сохраните остальные значения конфигурации NuGet по умолчанию.

   ![Настройка задача NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. На **этапе 1**, щелкните **+** и добавьте задачу **MSBuild**.

   ![Добавление задачи MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. Измените **Аргументы MSBuild** на следующие.

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Настройка задачи MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. На **этапе 1**, щелкните **+** и добавьте задачу **развертывания группы ресурсов Azure**. 
    
    ![Добавление задачи развертывания группы ресурсов Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. Разверните **сведения об Azure** и заполните конфигурацию, указав следующее:
    
    |**Параметр**  |**Рекомендуемое значение**  |
    |---------|---------|
    |Подписка  |  Выберите свою подписку.   |
    |Действие  |  Создание или изменение группы ресурсов   |
    |Группа ресурсов  |  Введите имя группы ресурсов.   |
    |Шаблон  | [Путь решения] \bin\Debug\Deploy\\[имя_проекта]. JobTemplate.json   |
    |Параметры шаблона  | [Путь решения] \bin\Debug\Deploy\\[имя_проекта]. JobTemplate.parameters.json   |
    |Переопределение параметров шаблона  | Введите параметры шаблона для переопределения в текстовом поле. Например: storageName fabrikam – adminUsername $(vmusername) – adminPassword $(password) – azureKeyVaultName $(fabrikamFibre). Это свойство является необязательным, но если ключевые параметры не переопределяются сборка выдаст ошибки.    |
    
    ![Настройка свойств](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. Щелкните **Сохранить и поместить в очередь**, чтобы проверить конвейер сборки.
    
    ![Настройка параметров переопределения](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>Сбой процесса сборки
Может произойти ошибка развертывания параметров со значением NULL, если не выполнялось переопределение параметров шаблона в задаче конвейера сборки **Развертывание группы ресурсов Azure**. Чтобы устранить эту ошибку, вернитесь к конвейеру сборки и переопределите параметры со значением NULL.

   ![Сбой процесса сборки](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Фиксация и отправка изменений для инициации выпуска
Отправьте некоторые изменения кода в Azure DevOps, чтобы проверить работу конвейера непрерывной интеграции.    

При написании кода в Visual Studio изменения отслеживаются автоматически. Зафиксировать изменения в локальном репозитории Git можно, щелкнув значок ожидающих изменений в строке состояния справа внизу.

1. В представлении **Изменения** в Team Explorer добавьте сообщение с описанием обновления и зафиксируйте изменения.

    ![Фиксирование и отправка изменений](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. Щелкните значок неопубликованных изменений в строке состояния или "представлении синхронизации" в Team Explorer. Выберите **Отправить**, чтобы обновить код в Azure DevOps.

    ![Фиксирование и отправка изменений](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

При отправке изменений в Azure DevOps Services автоматически запускается сборка.  После успешного создания конвейера сборки автоматически создается выпуск и начинается обновление задания в кластере.

## <a name="clean-up-resources"></a>Очистка ресурсов

Ставшие ненужными группу ресурсов, задание потоковой передачи и все связанные ресурсы можно удалить. При удалении задания будет прекращена тарификация за единицы потоковой передачи, потребляемые заданием. Если вы планируете использовать это задание в будущем, вы можете остановить и перезапустить его позже при необходимости. Если вы не собираетесь использовать это задание дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, выполнив следующие шаги:

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса.  
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Дополнительная информация о том как использовать инструменты Azure Stream Analytics для Visual Studio для настройки процесса непрерывной интеграции и развертывания конвейера CI/CD см. в статье.

> [!div class="nextstepaction"]
> [Непрерывная интеграция и разработка с помощью инструментов Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
