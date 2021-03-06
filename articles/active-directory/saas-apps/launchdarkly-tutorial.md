---
title: Руководство. Интеграция Azure Active Directory с LaunchDarkly | Документы Майкрософт
description: Сведения о том, как настроить единый вход между Azure Active Directory и LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 54bf459f6acd7649f3ad1a546bef1d0429393161
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420765"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Руководство. Интеграция Azure Active Directory с LaunchDarkly

В этом руководстве описано, как интегрировать LaunchDarkly с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением LaunchDarkly обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к LaunchDarkly.
- Вы можете включить автоматический вход пользователей в LaunchDarkly (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением LaunchDarkly, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа LaunchDarkly.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление LaunchDarkly из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-launchdarkly-from-the-gallery"></a>Добавление LaunchDarkly из коллекции
Чтобы настроить интеграцию LaunchDarkly с Azure AD, нужно добавить LaunchDarkly из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LaunchDarkly из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **LaunchDarkly**, выберите **LaunchDarkly** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![LaunchDarkly в списке результатов](./media/launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в LaunchDarkly с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD нужно знать, какой пользователь в LaunchDarkly соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в LaunchDarkly.

Чтобы настроить и проверить единый вход Azure AD в LaunchDarkly, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя LaunchDarkly](#create-a-launchdarkly-test-user)** требуется для того, чтобы в LaunchDarkly существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении LaunchDarkly.

**Чтобы настроить единый вход Azure AD в LaunchDarkly, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **LaunchDarkly** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

1. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения LaunchDarkly** сделайте следующее:

    ![Сведения о домене и URL-адресах для единого входа в приложение LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. В текстовом поле **Идентификатор (идентификатор сущности)** введите URL-адрес: `app.launchdarkly.com`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`.
    
    > [!NOTE]
    > Значение URL-адреса ответа приведено для примера. Вы замените это значение на фактический URL-адрес ответа, который описывается далее в этом учебнике.

1. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах для единого входа в приложение LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://app.launchdarkly.com`

1. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/launchdarkly-tutorial/tutorial_general_400.png)
    
1. В разделе **Настройка LaunchDarkly** щелкните **Настроить LaunchDarkly**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа** из раздела **Краткий справочник**.

    ![Конфигурация LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

1. В другом окне веб-браузера войдите на сайт LaunchDarkly вашей организации в качестве администратора.

1. Выберите **Параметры учетной записи** в области навигации слева.

    ![Конфигурация LaunchDarkly](./media/launchdarkly-tutorial/configure1.png)

1. Откройте вкладку **Безопасность**.

    ![Конфигурация LaunchDarkly](./media/launchdarkly-tutorial/configure2.png)

1. Щелкните **ENABLE SSO** (Включить единый вход) и затем **EDIT SAML CONFIGURATION** (Изменить конфигурацию SAML).

    ![Конфигурация LaunchDarkly](./media/launchdarkly-tutorial/configure3.png)

1. В разделе **Edit your SAML configuration** (Изменение конфигурации SAML) сделайте следующее:

    ![Конфигурация LaunchDarkly](./media/launchdarkly-tutorial/configure4.png)

    a. Скопируйте **SAML consumer service URL** (URL-адрес службы объекта-получателя SAML) для своего экземпляра и вставьте его в текстовое поле "URL-адрес ответа" в разделе **Домен и URL-адреса приложения LaunchDarkly** на портале Azure.

    b. В текстовом поле **URL-адрес для входа** вставьте значение **URL-адрес службы единого входа**, скопированное на портале Azure.

    c. Откройте в Блокноте скачанный с портала Azure сертификат, скопируйте его содержимое и вставьте его в поле **Сертификат X.509**. Кроме того, можно отправить сертификат напрямую, выбрав **upload one** (Отправить).

    d. Нажмите кнопку **Сохранить**

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/launchdarkly-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/launchdarkly-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/launchdarkly-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/launchdarkly-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-launchdarkly-test-user"></a>Создание тестового пользователя приложения LaunchDarkly

Цель этого раздела — создать пользователя с именем Britta Simon в LaunchDarkly. Приложение LaunchDarkly поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь создается при попытке получить доступ к приложению LaunchDarkly (если он еще не создан).
>[!Note]
>Чтобы создать учетную запись пользователя вручную, обратитесь в [службу поддержки LaunchDarkly](mailto:support@launchdarkly.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к LaunchDarkly.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в LaunchDarkly, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **LaunchDarkly**.

    ![Ссылка на LaunchDarkly в списке "Приложения"](./media/launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент LaunchDarkly на панели доступа, вы автоматически войдете в приложение LaunchDarkly.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/launchdarkly-tutorial/tutorial_general_203.png

