---
title: Руководство. Настройка ресурсов для VaaS | Документация Майкрософт
description: В этом руководстве вы узнаете о настройке ресурсов для решения "проверка как услуга".
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: d9406032a55c0bbd73bf16ae2f0fa272dddd7698
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650876"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Руководство. Настройка ресурсов для VaaS

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Вам нужно будет создать решение. Решение "проверка как услуга" (VaaS) — это решение Azure Stack с определенными спецификациями оборудования. Вы используете решение, чтобы проверить, поддерживает ли ваше оборудование запуск Azure Stack. Следуйте указаниям, описанным в этом руководстве, чтобы с помощью решения подготовить службу для использования.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Подготовка к использованию решения VaaS путем настройки экземпляра AAD.
> * Создайте учетную запись хранения.

## <a name="configure-an-azure-ad-tenant"></a>Настройка клиента AAD

Клиент AAD является обязательным для проверки подлинности и регистрации VaaS. Партнер будет использовать клиентские функции управления доступом на основе ролей (RBAC) для контроля доступа к VaaS в партнерской организации.

Зарегистрируйте свой организационный каталог клиента AAD (а не каталог клиента AAD, используемый для Azure Stack) и задайте в нем политику для управления учетными записями пользователей. Дополнительные сведения см. в руководстве по [управлению каталогом Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-administer).

### <a name="create-a-tenant"></a>Создание клиента

Создайте отдельный клиент специально для VaaS и присвойте ему соответствующее описательное имя, например `ContosoVaaS@onmicrosoft.com`.

1. Создайте клиент AAD [на портале Azure](https://portal.azure.com) или примените имеющийся клиент. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Добавьте в клиент участников организации. Эти пользователи будут нести ответственность за использование службы для просмотра или планирования тестов. После регистрации вы определите уровни доступа пользователей.
 
    Разрешите пользователям в своем клиенте выполнять действия в VaaS, назначив одну из следующих ролей:

    | Имя роли | ОПИСАНИЕ |
    |---------------------|------------------------------------------|
    | Владелец. | Полный доступ ко всем ресурсам. |
    | Читатель | Просмотр всех ресурсов без возможности их создания или управления ими. |
    | Участник тестов | Создание тестовых ресурсов и управление ими. |

    Чтобы назначить роли в приложении **службы проверки Azure Stack**, сделайте следующее.

    1. Войдите на [портале Azure](https://portal.azure.com).
    2. Выберите **Все службы** > **Azure Active Directory** в разделе **идентификатора**.
    3. Выберите приложение **Корпоративные приложения** > **Служба проверки Azure Stack**.
    4. Выберите **Пользователи и группы**. Колонка **Служба проверки Azure Stack — пользователи и группы** содержит список пользователей с правом использовать это приложение.
    5. Выберите **+ Add user** (+ Добавить пользователя) для добавления пользователя из клиента и назначения ему роли.
   
    Если вы хотите изолировать ресурсы VaaS и действия в разных группах в организации, можно создать несколько каталогов клиента AAD.

### <a name="register-your-tenant"></a>Регистрация клиента

Этот процесс разрешает вашему клиенту использовать приложение AAD **службы проверки Azure Stack**.

1. Передайте в корпорацию Майкрософт по адресу [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) следующие данные о клиенте.

    | Данные | ОПИСАНИЕ |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Название организации | Официальное название организации. |
    | Имя каталога клиента Azure AD | Зарегистрированное имя каталога клиента AAD. |
    | Идентификатор каталога клиента AAD | Уникальный идентификатор GUID каталога клиента AAD, связанный с каталогом. Сведения о том, как найти идентификатор каталога клиента AAD, см. в разделе о [получении идентификатора клиента](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). |

2. Дождитесь подтверждения от команды проверки Azure Stack, чтобы проверить, может ли клиент использовать портал VaaS.

### <a name="consent-to-the-vaas-application"></a>Согласие с условиями использования приложения VaaS

Как администратор AAD предоставьте приложению VaaS AAD необходимые разрешения от имени своего клиента:

1. Войдите на [портал VaaS](https://azurestackvalidation.com/) с учетными данными глобального администратора нового клиента. 

2. Выберите **Моя учетная запись**.

3. При появлении запроса на предоставление VaaS перечисленных разрешений AAD примите условия, чтобы продолжить.

## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

Во время выполнения теста VaaS выводит журналы диагностики в учетную запись службы хранилища Azure. Кроме журналов тестирования учетную запись хранения можно также использовать для передачи пакетов расширения OEM для рабочего процесса "Проверка пакета".

Учетная запись службы хранилища Azure размещается в общедоступном облаке Azure, а не в среде Azure Stack.

1. На портале Azure выберите **Все службы** > **Хранилище** > **Учетные записи хранения**. В колонке **учетных записей хранения** выберите **Добавить**.

2. Выберите подписку, в которой будет создана учетная запись хранения.

3. Для параметра **Группа ресурсов** выберите **Создать**. Введите имя новой группы ресурсов.

4. Выберите имя для своей учетной записи хранения. Выбранное имя должно быть:
    - уникальным в Azure;
    - длиной от 3 до 24 символов;
    - содержать только строчные буквы и цифры.

5. Для своей учетной записи хранения выберите регион **Западная часть США**.

    Чтобы гарантировать, что за хранение журналов не будет взиматься плата, учетную запись службы хранилища Azure можно настроить для использования только одного региона — **Западная часть США**. Репликация данных и компонент "горячий уровень доступа" не обязательны для этих данных. Включение любой из этих компонентов значительно влияет на затраты.

6. Оставьте для параметров значения по умолчанию за исключением параметра **Тип учетной записи**:

    - Для поля **Модель развертывания** по умолчанию задается значение **Resource Manager**.
    - Для поля **Производительность** по умолчанию задается значение **Стандартная**.
    - Выберите в качестве **типа учетной записи** **хранилище BLOB-объектов**.
    - Для поля **Репликация** по умолчанию задается значение **Локально избыточное хранилище (LRS)**.
    - Для поля **Уровень доступа** по умолчанию задается значение **Горячий**.

7. Нажмите **Просмотр и создание**, чтобы просмотреть настройки учетной записи хранения и создать учетную запись.

## <a name="next-steps"></a>Дополнительная информация

Если в вашей среде запрещены входящие подключения, ознакомьтесь с руководством по развертыванию локального агента для выполнения теста на своем аппаратном обеспечении.

> [!div class="nextstepaction"]
> [Развертывание локального агента](azure-stack-vaas-local-agent.md)