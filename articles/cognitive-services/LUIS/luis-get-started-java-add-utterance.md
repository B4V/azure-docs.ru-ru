---
title: Краткое руководство для Java — изменение модели и обучение приложения LUIS
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства по Java вы узнаете, как добавить примеры высказываний в приложение Home Automation и обучить это приложение. Примерами высказываний называют фразы пользователя на обычном языке, сопоставленные с тем или иным намерением. Предоставляя фразы для настроенных намерений, вы сообщаете LUIS ожидаемые варианты сообщений пользователя для каждого намерения.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 8500dfdd639b4f1de46a0b79b87a8564d338b6ab
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042030"
---
# <a name="quickstart-change-model-using-java"></a>Краткое руководство. Изменение модели с помощью Java 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Quickstart prerequisites for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [JDK SE](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (комплект разработчика Java, выпуск "Стандартный");
* [Библиотека JSON Google GSON](https://github.com/google/gson).

[!INCLUDE [Quickstart note about code repository](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Файл JSON с примерами высказываний.

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Создание простого примера кода

1. Добавьте зависимости Java в файл с именем `AddUtterances.java`.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=23-26 "Java Dependencies")]

2. Создайте класс `AddUtterances`. Этот класс содержит все приведенные ниже фрагменты кода.

    ```Java
    public class AddUtterances {
        // Insert code here
    }
    ```

3. Добавьте в класс константы LUIS. Скопируйте следующий код и укажите свой ключ разработки, идентификатор приложения и идентификатор версии.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=33-44 "LUIS-based IDs")]

4. Добавьте в класс AddUtterances метод для вызова API LUIS. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=46-168 "HTTP request to LUIS")]

5. Добавьте в класс AddUtterances метод для получения HTTP-ответа из API LUIS.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=170-202 "HTTP response from LUIS")]

6. Добавьте в класс AddUtterances метод обработки исключений. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=205-243 "Exception Handling")]

7. Добавьте в класс AddUtterances функцию main.

   [!code-java[Add main function](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=245-278 "Add main function")]

## <a name="build-code"></a>Компиляция кода

Скомпилируйте файл AddUtterance с зависимостями.

```CMD
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

## <a name="run-code"></a>Выполнение кода
Если вызвать `AddUtterance` без аргументов, фразы будут добавлены в приложение LUIS, но обучение по ним не будет выполнено.

```CMD
> java -classpath .;gson-2.8.2.jar AddUtterances
```

Эта командная строка отображает результаты вызова API добавления фраз. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов
Когда вы закончите работу с этим руководством, удалите все созданные при работе с ним файлы. 

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Создание приложения LUIS программным способом](luis-tutorial-node-import-utterances-csv.md) 