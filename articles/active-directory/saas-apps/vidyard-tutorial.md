---
title: Руководство по интеграции Azure Active Directory с Vidyard | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.openlocfilehash: 871942db15d6a3cff45584e33b2191e21d2281a0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426461"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Руководство по интеграции Azure Active Directory с Vidyard

В этом руководстве описано, как интегрировать Vidyard с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Vidyard обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Vidyard.
- Вы можете включить автоматический вход пользователей в Vidyard (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Vidyard, вам потребуется:

- подписка Azure AD;
- подписка Vidyard с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Vidyard из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-vidyard-from-the-gallery"></a>Добавление Vidyard из коллекции
Чтобы настроить интеграцию Azure AD с приложением Vidyard, необходимо добавить Vidyard из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Vidyard из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **Vidyard**, затем на панели результатов выберите **Vidyard** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Vidyard в списке результатов](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Vidyard с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Vidyard соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Vidyard.

Чтобы настроить и проверить единый вход Azure AD в Vidyard, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя Vidyard](#create-a-vidyard-test-user)** требуется для того, чтобы в Vidyard существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Vidyard.

**Чтобы настроить единый вход Azure AD в Vidyard, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Vidyard** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

1. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения Vidyard** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения Vidyard](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://secure.vidyard.com/sso/saml/<unique id>/consume`.

1. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа приложения Vidyard](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа, которые описываются далее в этом руководстве.

1. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/vidyard-tutorial/tutorial_general_400.png)

1. В разделе **Настройка Vidyard** щелкните **Настроить Vidyard**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Vidyard](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

1. В другом окне веб-браузера войдите на веб-сайт компании Vidyard Software в качестве администратора.

1. На панели мониторинга Vidyard выберите **Group** > **Security** (Группа > Безопасность).

    ![Настройка Vidyard](./media/vidyard-tutorial/configure1.png)

1. Откройте вкладку **New Profile** (Новый профиль).

    ![Настройка Vidyard](./media/vidyard-tutorial/configure2.png)

1. В разделе **SAML Configuration** (Настройка SAML) выполните следующие действия:

    ![Настройка Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Введите общее имя профиля в текстовом поле **Profile Name** (Имя профиля).

    b. Скопируйте значение в поле **SSO User Login Page** (Страница единого входа) и вставьте его в текстовое поле **URL-адрес для входа** в разделе **Домены и URL-адреса приложения Vidyard** на портале Azure.

    c. Скопируйте значение в поле **ACS URL** (URL-адрес ACS) и вставьте его в текстовое поле **URL-адрес ответа** в разделе **Домены и URL-адреса приложения Vidyard** на портале Azure.

    d. Скопируйте значение в поле **Issuer/Metadata URL** (URL-адрес издателя/метаданных) и вставьте его в текстовое поле **Идентификатор** в разделе **Домены и URL-адреса приложения Vidyard** на портале Azure.

    д. Откройте в Блокноте скачанный с портала Azure файл сертификата, а затем скопируйте и вставьте его содержимое в текстовое поле **X.509 Certificate** (Сертификат X.509).

    Е. В текстовое поле **SAML Endpoint URL** (URL-адрес конечной точки SAML) вставьте значение параметра **URL-адрес службы единого входа SAML**, скопированное с портала Azure.

    ж. Щелкните **Confirm** (Подтвердить).

1. На вкладке единого входа выберите **Assign** (Назначить) рядом с именем существующего профиля.

    ![Настройка Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Создав профиль единого входа, назначьте его какой-либо группе, к которой пользователям требуется доступ через Azure. Если пользователь не существует в группе, которой он был назначен, Vidyard автоматически создаст учетную запись пользователя и назначит роль в режиме реального времени.

1. Выберите группу вашей организации, которая отображается в списке **доступных для назначения групп**.

    ![Настройка Vidyard](./media/vidyard-tutorial/configure5.png)

1. Назначенные группы отображаются в разделе **Groups Currently Assigned** (Назначенные группы). Выберите роль для группы (в зависимости от типа вашей организации) и щелкните **Confirm** (Подтвердить).

    ![Настройка Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Дополнительные сведения см. в [этом документе](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/vidyard-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/vidyard-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/vidyard-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/vidyard-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-vidyard-test-user"></a>Создание тестового пользователя Vidyard

Цель этого раздела — создать пользователя с именем Britta Simon в Vidyard. Приложение Vidyard поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к приложению Vidyard будет создан пользователь (если он еще не создан).
>[!Note]
>Чтобы создать пользователя вручную, обратитесь в [службу технической поддержки Vidyard](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Vidyard.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Vidyard, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **Vidyard**.

    ![Ссылка на Vidyard в списке приложений](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Vidyard на панели доступа, вы автоматически войдете в приложение Vidyard.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

