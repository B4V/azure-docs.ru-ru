---
title: Анализ видео с помощью Служб мультимедиа Azure | Документация Майкрософт
description: Используя инструкции этого руководства, вы сможете проанализировать видео с помощью Служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 5bb840be119f5eac380c44e2cf45b3f73a9d981e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985715"
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>Руководство. Анализ видео с помощью Служб мультимедиа Azure 

Это руководство содержит сведения об анализе видео с помощью Служб мультимедиа Azure. Имеется множество сценариев, в которых может потребоваться получить подробные сведения о видео- и аудиозаписях. Например, чтобы повысить удовлетворенность клиентов, организации могут выполнить преобразование речи в текст. Таким образом можно сделать записи клиентов доступным для поиска каталогом с индексами и панелями мониторинга. Затем они могут получить информативные бизнес-сведения, например список распространенных жалоб, источники этих жалоб и т. д.

В этом учебнике описаны следующие процедуры.    

> [!div class="checklist"]
> * Создание учетной записи служб мультимедиа
> * Доступ к API Служб мультимедиа.
> * Настройка примера приложения
> * Проверка кода, анализирующего указанное видео
> * Запуск приложения
> * Изучение выходных данных
> * Очистка ресурсов

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Вы можете скачать [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) бесплатно, если у вас нет Visual Studio.

## <a name="download-the-sample"></a>Скачивание примера приложения

Клонируйте репозиторий GitHub, содержащий пример .NET, на компьютер с помощью следующей команды:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Этот образец находится в папке [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Проверка кода, анализирующего указанное видео

В этом разделе рассматриваются функции, определенные в файле [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) проекта *AnalyzeVideos*.

Этот пример выполняет следующие действия:

1. Создает преобразование и задание, которое анализирует видео.
2. Создает входной ресурс и отправляет в него видео. Ресурс используется в качестве входных данных задания.
3. Создает выходной ресурс, который сохраняет выходные данные задания. 
4. Подтверждает задание.
5. Проверяет состояние задания.
6. Загружает файлы, полученные в результате выполнения задания. 

> [!NOTE]
> Используя наборы настроек анализаторов, установите 10 зарезервированных единиц кодирования S3 для своей учетной записи на портале Azure. Дополнительные сведения см. в [этой статье](../previous/media-services-scale-media-processing-overview.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>Начало использования API Служб мультимедиа с пакетом SDK для .NET

Чтобы начать использование API Служб мультимедиа с .NET, создайте объект **AzureMediaServicesClient**. Чтобы создать объект, введите учетные данные, необходимые клиенту для подключения к Azure с помощью Azure AD. В коде, который вы клонировали в начале статьи, функция **GetCredentialsAsync** создает объект ServiceClientCredentials с использованием учетных данных, предоставленных в локальном файле конфигурации. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Создание входного ресурса и отправка в него локального файла 

Функция **CreateInputAsset** создает входной [ресурс](https://docs.microsoft.com/rest/api/media/assets) и отправляет в него определенный локальный видеофайл. Этот ресурс используется в качестве входных данных для задания кодирования. В Службах мультимедиа версии 3 входные данные для задания могут быть либо ресурсом, либо содержимым, доступным в учетной записи Служб мультимедиа через URL-адрес HTTPS. Дополнительные сведения о кодировании из URL-адреса HTTPS см. в [этой статье](job-input-from-http-how-to.md).  

В Службах мультимедиа версии 3 для отправки файлов используются API службы хранилища. В следующих фрагментах кода .NET показано, как это сделать.

Следующая функция выполняет такие действия:

* создает каталог ресурсов; 
* получает доступный для записи [URL-адрес SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) для [контейнера ресурса в хранилище](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container);
* отправляет файл в контейнер в хранилище через URL-адрес SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Создание выходного ресурса для хранения результатов задания 

Выходной [ресурс](https://docs.microsoft.com/rest/api/media/assets) сохраняет результаты задания. Проект определяет функцию **DownloadResults**, которая скачивает результаты из выходного ресурса в папку output, чтобы вы могли просмотреть их.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Создание преобразования и задания, которое анализирует видео

При кодировании или обработке содержимого в Службах мультимедиа параметры кодировки часто задают в качестве набора инструкций. Затем необходимо отправить **задание**, чтобы применить этот набор к видео. Отправляя новые задания для каждого нового видео, вы применяете этот набор ко всем видео в своей библиотеке. Набор инструкций в Службах мультимедиа называется **Преобразование**. Дополнительные сведения см. в статье о [преобразованиях и заданиях](transform-concept.md). Пример кода, описанный в этом руководстве, определяет набор инструкций, которые анализируют указанное видео. 

#### <a name="transform"></a>Преобразование

При создании нового экземпляра [преобразования](https://docs.microsoft.com/rest/api/media/transforms) необходимо указать, что требуется создать в качестве выходных данных. Обязательный параметр — это объект **TransformOutput**, как показано в приведенном выше примере кода. Каждый объект **TransformOutput** содержит **предустановку** (Preset). **Предустановка** описывает пошаговые инструкции для операций обработки видео и звука, которые будут использоваться для создания нужного объекта **TransformOutput**. В этом примере используется предустановка **VideoAnalyzerPreset** и язык (ru_RU) передается в конструктор. Эта предустановка позволяет извлечь из видео множество аналитических сведений об аудио и видео. Если необходимо извлечь из видео множество аналитических сведений об аудио, используйте предустановку **AudioAnalyzerPreset**. 

При создании **преобразования** сначала проверьте, существует ли оно, с помощью метода **Get**, как показано в следующем коде.  В Службах мультимедиа версии 3 методы **Get**, отправленные к сущностям, возвращают значение **NULL**, если сущность не существует (проверка по имени без учета регистра).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Задание

Как было указано выше, объект [преобразования](https://docs.microsoft.com/rest/api/media/transforms) является набором инструкций, а [задание](https://docs.microsoft.com/rest/api/media/jobs) — фактическим запросом к Службам мультимедиа для применения этого **преобразования** к данному видео и аудио. **Задание** указывает такую информацию, как расположение входного и выходного видео. Вы можете указать расположение видео с помощью URL-адресов HTTP, URL-адресов SAS или ресурсов в учетной записи Службы мультимедиа. 

В этом примере задания входными данными задания является локальный видеофайл.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Ожидание завершения задания

Выполнение задания занимает некоторое время. По его завершению вы будете уведомлены. Получить уведомление о завершении [задания](https://docs.microsoft.com/rest/api/media/jobs) можно несколькими способами. Самый простой (который показан тут) — использовать опрос. 

Опрос не рекомендуется для приложений рабочей среды из-за потенциальной задержки. Его можно регулировать при чрезмерном использовании в учетной записи. Вместо этого разработчики должны использовать службу "Сетка событий".

Служба "Сетка событий" предназначена для обеспечения высокого уровня доступности, стабильной производительности и динамического масштабирования. С помощью службы "Сетка событий Azure" приложения могут ожидать передачи данных и реагировать на события, поступающие буквально из всех служб Azure и пользовательских источников. Простая реактивная обработка событий на основе HTTP позволяет создавать эффективные решения с использованием интеллектуальной фильтрации и маршрутизации событий. Дополнительные сведения см. в статье [Route Azure Media Services events to a custom web endpoint using CLI](job-state-events-cli-how-to.md) (Маршрутизация событий Служб мультимедиа в пользовательскую конечную точку с помощью CLI).

**Задание** обычно проходит через такие состояния: **Запланировано**, **В очереди**, **Обработка**, **Завершено** (конечное состояние). Если в задании обнаружена ошибка, вы получите состояние **Ошибка**. Если задание находится в процессе отмены, вы получите состояние **Выполнение отмены** и **Отменено** по завершении.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="download-the-result-of-the-job"></a>Скачивание результатов задания

С помощью следующей функции можно скачать результаты задания из выходного [ресурса](https://docs.microsoft.com/rest/api/media/assets) контейнера в папку output, чтобы проанализировать их. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Очистка ресурсов в учетной записи служб мультимедиа

Как правило, необходимо очистить все, кроме объектов, которые планируется использовать повторно. Обычно повторно используются преобразования и сохраненные потоковые локаторы. Если учетную запись требуется очистить после эксперимента, следует удалить ресурсы, которые не планируется использовать повторно. Например, следующий код удаляет задания.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Запуск примера приложения

Для запуска приложения *AnalyzeVideos* нажмите клавиши CTRL+F5.

При запуске программы задание создает эскизы каждого лица, обнаруженного в видео. Оно также создает файл insights.json.

## <a name="examine-the-output"></a>Изучение выходных данных

Выходной файл анализированных видео называется insights.json. Этот файл содержит аналитические сведения о видео. Описание элементов находятся в JSON-файле статьи об [аналитике мультимедиа](intelligence-concept.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны какие-либо ресурсы в группе ресурсов, включая Службы мультимедиа и учетные записи хранения, созданные для этого руководства, удалите группу ресурсов, созданную ранее. Для этого можно использовать средство **CloudShell**.

В **CloudShell** выполните следующую команду:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Многопоточность

Пакеты SDK для Служб мультимедиа Azure версии 3 не являются потокобезопасными. При работе с многопоточным приложением необходимо создать объект AzureMediaServicesClient для каждого потока.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство. Отправка, кодирование и потоковая передача видео с помощью API](stream-files-tutorial-with-api.md)
