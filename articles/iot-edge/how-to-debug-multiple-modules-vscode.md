---
title: Отладка нескольких модулей для Azure IoT Edge в VS Code | Документация Майкрософт
description: Использование Visual Studio Code для отладки нескольких модулей с помощью Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 38c66129ac8244d18b0f94c1485c785015e29ab4
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049597"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Использование Visual Studio Code для отладки нескольких модулей с помощью Azure IoT Edge
В этой статье содержатся подробные инструкции по использованию [Visual Studio (VS) Code](https://code.visualstudio.com/) для отладки нескольких модулей на IoT Edge.

## <a name="prerequisites"></a>предварительным требованиям
Изучите руководство [Разработка решения IoT Edge с несколькими модулями в Visual Studio Code](tutorial-multiple-modules-in-vscode.md) и убедитесь, что на вашем устройстве IoT Edge установлено как минимум два модуля.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Многоцелевая и удаленная отладка в VS Code
С помощью VS Code и расширения Azure IoT Edge можно присоединить процесс модуля к контейнеру, независимо от того, запускается ли он на компьютере разработки или на удаленном физическом устройстве IoT Edge. Отладка нескольких модулей, которые выполняются в контейнерах, фактически включает несколько процессов в отдельных контейнерах. [Многоцелевая отладка](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) VS Code может использоваться при отладке нескольких модулей.

   > [!TIP]
   > Если контейнер модуля запущен на удаленном физическом устройстве IoT Edge, может потребоваться установить [Docker компьютер](https://docs.docker.com/machine/overview/), чтобы механизм Docker на компьютере разработки мог обмениваться информацией с удаленными хостами Docker.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Сборка модулей IoT Edge для целей отладки
1. Чтобы начать отладку многочисленных модулей, повторно выполните сборку образов Docker с помощью **Dockerfile.amd64.debug** и повторно разверните решение Edge. В проводнике VS Code перейдите к файлу `deployment.template.json`. Обновите URL-адреса образа, добавив `.debug` в конце. Вам потребуется `.debug` по крайней мере два образа модуля. Если вы работаете над решением предыдущего руководства, у вас должен быть модуль функций C# и модуль C#. Обновите эти два URL образа, добавив `.debug` и сохранив в конце этот файл. 
2. Перестройте решение. В палитре команд VS Code введите и выполните команду **Azure IoT Edge: Build IoT Edge solution**.
3. В обозревателе устройств Центра Интернета вещей Azure щелкните правой кнопкой мыши идентификатор устройства IoT Edge и выберите **Create deployment for Edge device** (Создать развертывание для устройства Edge). Выберите файл `deployment.json` в папке `config`. Ход развертывания можно отслеживать с помощью идентификатора развертывания во встроенном терминале VS Code.

Чтобы проверить состояние контейнера, откройте обозреватель кода Docker VS или выполните команду `docker ps` в окне терминала.

### <a name="start-debugging-c-function-in-vs-code"></a>Запуск отладки функции C# в VS Code
1. VS Code хранит информацию о конфигурации отладки в файле `launch.json`, расположенном в папке `.vscode` рабочей области. Этот файл `launch.json` создан при создании решения IoT Edge. Он автоматически обновляется при каждом добавлении нового модуля, который поддерживает отладку. Перейдите к представлению отладки и выберите соответствующий файл конфигурации для отладки функций модуля C#.
2. Перейдите на страницу `run.csx`. Добавьте в функцию точку останова.
3. Нажмите кнопку **Начать отладку** или клавишу **F5**, затем выберите процесс для присоединения.
4. В представлении отладки VS Code можно просмотреть переменные на панели слева. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>Запуск модуля отладки C# одновременно в VS Code
1. В палитру команд VS Code введите и выполните команду "Workspace: Duplicate Workspace in New Window". Новое окно VS Code начинается с того же рабочего пространства.
2. Перейдите в представление отладки и выберите соответствующий файл конфигурации отладки для удаленной отладки модуля C#.
3. Перейдите на страницу `program.cs`. Добавьте точку останова в модуль C#.
4. Нажмите кнопку **Начать отладку** или клавишу **F5**, затем выберите процесс для присоединения.
5. В представлении отладки VS Code можно просмотреть переменные на панели слева. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Просмотр переменных в нескольких окнах отладки
1. Теперь у вас есть по крайней мере два сеанса отладки, запущенных в двух окнах VS Code. Одна из точек останова должна быть выполненной.
2. Нажмите `F10` или щелкните кнопку "Шаг за шагом" на **панели инструментов Отладка**.
3. Точка останова в другом окне VS Code должна быть выполненной. 
4. Отслеживая два шага, можно видеть переменные из нескольких модулей в нескольких окнах отладки VS Code.

> [!NOTE]
> В предыдущем примере показаны способы отладки нескольких модулей с помощью Azure IoT Edge. Он основан на отладочной версии файла `Dockerfile.amd64.debug`, который при сборке образа контейнера включает в него отладчик командной строки .NET Core (VSDBG). Мы рекомендуем использовать для рабочей версии функции IoT Edge готовый или отдельно настроенный файл `Dockerfile` без отладчика VSDBG, когда вы завершите отладку функции C#.

## <a name="next-steps"></a>Дополнительная информация

После того как модуль создан, изучите [Deploy Azure IoT Edge modules from Visual Studio Code](how-to-deploy-modules-vscode.md) (Развертывание модулей Azure IoT Edge из кода Visual Studio)
