---
title: Краткое руководство. Создание базы знаний QnA Maker с использованием Node.js и REST
description: Из этого краткого руководства вы узнаете, как создать пример базы знаний QnA Maker программными средствами с использованием REST, чтобы отобразить ее на панели мониторинга Azure в вашей учетной записи API Cognitive Services.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: fdba785e33c16c397e2ffaeb4462ea2066a99126
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647550"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-nodejs"></a>Краткое руководство. Создание базы знаний в QnA Maker с помощью Node.js

В этом кратком руководстве описано, как программным способом создать пример базы знаний QnA Maker. Служба QnA Maker автоматически извлекает вопросы и ответы из частично структурированного содержимого, например со страниц с вопросами и ответами, [источников данных](../Concepts/data-sources-supported.md). Модель базы знаний определяется в коде JSON, отправляемом в теле запроса API. 

В этом кратком руководстве вызываются API службы QnA Maker:
* [Создание базы знаний](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff).
* [Получение сведений об операции](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails).

## <a name="prerequisites"></a>Предварительные требования

* [Node.js 6+](https://nodejs.org/en/download/)
* У вас должна быть [служба QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Чтобы получить этот ключ, выберите **Ключи** в разделе **Управление ресурсами** на информационной панели. 

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Создание файла базы знаний Node.js

Создайте файл с именем `create-new-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

Чтобы добавить необходимые зависимости в проект, в верхней части файла `create-new-knowledge-base.js` укажите следующие строки:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-the-required-constants"></a>Добавление необходимых констант
После указания зависимостей добавьте необходимые константы, чтобы получить доступ к QnA Maker. Замените значение переменной `subscriptionKey` собственным ключом QnA Maker.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=10-19 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Добавление определения модели базы знаний

После констант добавьте приведенное ниже определение модели базы знаний. После определения модель преобразуется в строку.

[!code-nodejs[Add the KB model definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=21-51 "Add the KB model definition")]

## <a name="add-supporting-functions"></a>Добавление вспомогательных функций

Далее необходимо добавить приведенные ниже вспомогательные функции.

1. Добавьте следующую функцию, чтобы распечатать файл JSON в доступном для чтения формате:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=53-56 "Add supporting functions, step 1")]

2. Добавьте следующую функцию, чтобы управлять HTTP-запросом:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=58-80 "Add supporting functions, step 2")]

## <a name="add-functions-to-create-kb"></a>Добавление функций для создания базы знаний

Добавьте приведенные ниже функции, чтобы отправить HTTP-запрос POST на создание базы знаний. `Ocp-Apim-Subscription-Key` — это ключ службы QnA Maker, используемый в процессе аутентификации. 

[!code-nodejs[POST Request to API](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=82-109 "POST Request to API")]

Этот вызов API возвращает ответ JSON, который содержит идентификатор операции. С помощью этого идентификатора определите, успешно ли создана база знаний. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-functions-to-determine-creation-status"></a>Добавление функций для определения состояния создания

Добавьте приведенную ниже функцию, чтобы отправить HTTP-запрос GET на проверку состояния операции. `Ocp-Apim-Subscription-Key` — это ключ службы QnA Maker, используемый в процессе аутентификации. 

[!code-nodejs[GET Request to API](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=111-135 "GET Request to API")]

Повторяйте вызов до успешного или неудачного выполнения: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-create-kb-function"></a>Добавление функции create-kb

Ниже приведена функция main, которая создает базу данных и повторяет проверки состояния. Так как создание базы знаний может занять некоторое время, необходимо повторять вызовы, чтобы проверить состояние, пока не вернется успешный или неудачный ответ.

[!code-nodejs[Add create-kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=137-167 "Add create-kb function")]

## <a name="run-the-program"></a>Запуск программы

В командной строке введите приведенную ниже команду, чтобы запустить программу. Она будет отправлять запрос к API службы QnA Maker для создания базы знаний. Затем она будет запрашивать результаты каждые 30 секунд. Каждый ответ будет выводиться в окне консоли.

```bash
node create-new-knowledge-base.js
```

Созданную базу знаний вы можете просмотреть на странице [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Мои базы знаний) на портале QnA Maker. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (Справочник по API REST QnA Maker (V4))
