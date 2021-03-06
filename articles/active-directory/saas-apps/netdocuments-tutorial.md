---
title: Руководство по интеграции Azure Active Directory с NetDocuments | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 2da548b0d3a13dfac5d3928d8d692ac8e083bf58
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421106"
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Учебник. Интеграция Azure Active Directory с NetDocuments

В этом руководстве описано, как интегрировать приложение NetDocuments с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением NetDocuments обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к NetDocuments.
- Вы можете включить автоматический вход пользователей в NetDocuments (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с NetDocuments, вам потребуется:

- подписка Azure AD;
- подписка NetDocuments с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление NetDocuments из коллекции.
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-netdocuments-from-the-gallery"></a>Добавление NetDocuments из коллекции
Чтобы настроить интеграцию NetDocuments с Azure AD, необходимо добавить NetDocuments из коллекции в список управляемых приложений SaaS.

**Чтобы добавить NetDocuments из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

1. В поле поиска введите **NetDocuments**.

    ![Создание тестового пользователя Azure AD](./media/netdocuments-tutorial/tutorial_netdocuments_search.png)

1. На панели результатов выберите **NetDocuments** и щелкните **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/netdocuments-tutorial/tutorial_netdocuments_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описаны настройка и проверка единого входа Azure AD в NetDocuments с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в NetDocuments соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в NetDocuments.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в NetDocuments.

Чтобы настроить и проверить единый вход Azure AD в NetDocuments, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя NetDocuments](#creating-a-netdocuments-test-user)** требуется для создания пользователя Britta Simon в NetDocuments, связанного с соответствующим представлением в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
1. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении NetDocuments.

**Чтобы настроить единый вход Azure AD в NetDocuments, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **NetDocuments** щелкните **Единый вход**.

    ![Настройка единого входа][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/netdocuments-tutorial/tutorial_netdocuments_samlbase.png)

1. В разделе **Домены и URL-адреса приложения NetDocuments** сделайте следующее:

    ![Настройка единого входа](./media/netdocuments-tutorial/tutorial_netdocuments_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса входа и URL-адреса ответа. Обратитесь к [службе поддержки NetDocuments](https://support.netdocuments.com/hc/), чтобы получить эти значения.
 
1. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/netdocuments-tutorial/tutorial_netdocuments_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/netdocuments-tutorial/tutorial_general_400.png)

1. В другом окне веб-браузера войдите на сайт NetDocuments вашей компании в качестве администратора.

1. Откройте страницу **Администратор**.

1. Щелкните **Добавление и удаление пользователей и групп**.
   
    ![Репозиторий](./media/netdocuments-tutorial/ic795047.png "Репозиторий")

1. Щелкните **Настройка дополнительных параметров аутентификации**.
    
    ![Настройка дополнительных параметров аутентификации](./media/netdocuments-tutorial/ic795048.png "Настройка дополнительных параметров аутентификации")

1. В диалоговом окне **Federated Identity** (Федеративное удостоверение) выполните следующие действия:
   
    ![Федеративное удостоверение](./media/netdocuments-tutorial/ic795049.png "Федеративное удостоверение")
   
    a. Для параметра **Federated identity server type** (Тип сервера федеративных удостоверений) выберите **Службы федерации Active Directory**.
   
    b. Щелкните **Выбрать файл**, чтобы отправить скачанный файл метаданных, который вы скачали с портала Azure.
   
    c. Последовательно выберите **ОК**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/netdocuments-tutorial/create_aaduser_01.png) 

1. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/netdocuments-tutorial/create_aaduser_02.png) 

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/netdocuments-tutorial/create_aaduser_03.png) 

1. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/netdocuments-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-netdocuments-test-user"></a>Создание тестового пользователя NetDocuments

Чтобы разрешить пользователям Azure AD вход в NetDocuments, их необходимо подготовить для NetDocuments.  
В случае с NetDocuments подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на сайт компании **NetDocuments** от имени администратора.

1. В верхнем меню щелкните **Администратор**.
   
    ![Администратор](./media/netdocuments-tutorial/ic795051.png "Администратор")

1. Щелкните **Добавление и удаление пользователей и групп**.
   
    ![Репозиторий](./media/netdocuments-tutorial/ic795047.png "Репозиторий")

1. В текстовом поле **Электронная почта** введите адрес электронной почты действующей учетной записи Azure Active Directory, которую вы хотите подготовить, а затем нажмите кнопку **Добавить пользователя**.
   
    ![Адрес электронной почты](./media/netdocuments-tutorial/ic795053.png "Адрес электронной почты")
   
   >[!NOTE]
   >Владелец учетной записи Azure Active Directory получит электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией. Вы можете использовать любые другие инструменты создания учетных записей пользователя NetDocuments или API, предоставляемые NetDocuments для подготовки учетных записей пользователя Azure Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к NetDocuments.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению NetDocuments, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **NetDocuments**.

    ![Настройка единого входа](./media/netdocuments-tutorial/tutorial_netdocuments_app.png) 

1. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент NetDocuments на панели доступа, вы автоматически войдете в приложение NetDocuments.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/netdocuments-tutorial/tutorial_general_01.png
[2]: ./media/netdocuments-tutorial/tutorial_general_02.png
[3]: ./media/netdocuments-tutorial/tutorial_general_03.png
[4]: ./media/netdocuments-tutorial/tutorial_general_04.png

[100]: ./media/netdocuments-tutorial/tutorial_general_100.png

[200]: ./media/netdocuments-tutorial/tutorial_general_200.png
[201]: ./media/netdocuments-tutorial/tutorial_general_201.png
[202]: ./media/netdocuments-tutorial/tutorial_general_202.png
[203]: ./media/netdocuments-tutorial/tutorial_general_203.png

