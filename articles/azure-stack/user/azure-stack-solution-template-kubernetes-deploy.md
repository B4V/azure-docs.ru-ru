---
title: Развертывание Kubernetes в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть Kubernetes в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 62eb28c6cdb2dd6c1ddff1487826c01ef3bf6e3e
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091595"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>Развертывание Kubernetes в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

> [!Note]  
> Система Kubernetes доступна в Azure Stack в предварительной версии.

В статье рассматривается развертывание и подготовка ресурсов для автономного кластера Kubernetes с помощью шаблона Azure Resource Manager в виде единой координируемой операции. Вам потребуется собрать все необходимые сведения об установке Azure Stack, создать шаблон и выполнить развертывание в облаке. Учтите, что шаблон не соответствует управляемой службе AKS, представляемой в глобальной среде Azure.

## <a name="kubernetes-and-containers"></a>Kubernetes и контейнеры

Вы можете установить Kubernetes с помощью шаблонов Azure Resource Manager, созданных модулем ACS в Azure Stack. [Kubernetes](https://kubernetes.io) — это система с открытым кодом для автоматизации развертывания, масштабирования и управления приложениями в контейнерах. [Контейнер](https://www.docker.com/what-container) содержится в образе наподобие виртуальной машины. В отличие от виртуальной машины, в образ контейнера помещены ресурсы, необходимые только для запуска приложения, например код, среда выполнения для этого кода, определенные библиотеки и параметры.

Kubernetes можно использовать для следующих целей:

- Разработка обновляемых приложений с высокой масштабируемостью, которые можно развернуть в считаные секунды. 
- Упрощение структуры приложения и повышение его надежности с помощью различных приложений Helm. [Helm](https://github.com/kubernetes/helm) — это средство упаковки с открытым исходным кодом, которое помогает установить приложения Kubernetes и управлять их жизненным циклом.
- Простой мониторинг и диагностика работоспособности приложений с помощью функций масштабирования и обновления.

Вам будут выставляться счета только за использование вычислительных ресурсов, необходимых для узлов, поддерживающих кластер. Дополнительные сведения см. в статье [Потребление ресурсов и выставление счетов в Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback).

## <a name="prerequisites"></a>Предварительные требования 

Прежде чем начать работу, убедитесь в наличии необходимых разрешений и в готовности Azure Stack.

1. Проверьте, можете ли вы создавать приложения в клиенте Azure Active Directory (Azure AD). Эти разрешения потребуются для развертывания Kubernetes.

    Инструкции по проверке разрешений см. в разделе [Check Azure Active Directory permissions](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions) (Проверка разрешений Azure Active Directory).

1. Создайте пару открытого и закрытого ключей SSH для входа на виртуальную машину Linux в Azure Stack. Открытый ключ потребуется при создании кластера.

    Инструкции по создания ключа см. в разделе [SSH Key Generation](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation) (Создание ключа SSH).

1. Убедитесь в наличии действующей подписки на портале клиента Azure Stack, а также достаточного количества общедоступных IP-адресов для добавления новых приложений.

    Кластер не может быть развернут в подписке **администратора** Azure Stack. Необходимо использовать подписку **пользователя**. 

1. Если в Marketplace нет кластера Kubernetes, обратитесь к администратору Azure Stack.

## <a name="create-a-service-principal-in-azure-ad"></a>Создание субъекта-службы в Azure AD

1. Войдите на глобальный [портал Azure](http://portal.azure.com).

1. Убедитесь, что при входе используется клиент Azure AD, связанный с экземпляром Azure Stack. Вы можете переключиться для входа, щелкнув значок фильтра на панели инструментов Azure.

    ![Выбор клиента AD](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Создадим приложение Azure AD.

    a. Последовательно выберите элементы **Azure Active Directory** > **+ Регистрация приложений** > **Регистрация нового приложения**.

    b. Введите **имя** приложения.

    c. Выберите **Веб-приложение или API**.

    d. Введите `http://localhost` в поле **URL-адрес для входа**.

    c. Нажмите кнопку **Создать**.

1. Запишите значение **идентификатора приложения**. Идентификатор потребуется при создании кластера. Этот идентификатор называется **идентификатором клиента для субъекта-службы**.

1. Последовательно выберите **Параметры** > **Ключи**.

    a. Введите **описание**.

    b. Выберите для параметра **Срок действия** значение **Срок действия неограничен**.

    c. Щелкните **Сохранить**. Запишите строку ключа. Строка ключа потребуется при создании кластера. Этот ключ называется **секретом клиента субъекта-службы**.


## <a name="give-the-service-principal-access"></a>Предоставление доступа субъекту-службе

Предоставьте субъекту-службе доступ к вашей подписке, чтобы субъект мог создать ресурсы.

1.  Войдите на [портал Azure Stack](https://portal.local.azurestack.external/).

1. Выберите **Все службы** > **Подписки**.

1. Выберите подписку, созданную вашим оператором для использования кластера Kubernetes.

1. Последовательно выберите **Управление доступом (IAM)** > **+ Добавить**.

1. Выберите роль **Участник**.

1. Выберите имя приложения, созданное для субъекта-службы. Возможно, потребуется ввести имя в поле поиска.

1. Выберите команду **Сохранить**.

## <a name="deploy-a-kubernetes"></a>Развертывание Kubernetes

1. Откройте [портал Azure Stack](https://portal.local.azurestack.external).

1. Выберите **+Создать ресурс** > **Служба вычислений** > **Кластер Kubernetes**. Нажмите кнопку **Создать**.

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Основы

1. В колонке "Создание кластера Kubernetes" выберите **Основные сведения**.

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Выберите идентификатор **подписки**.

1. Введите имя новой группы ресурсов или выберите существующую. Имя ресурса должно содержать буквенно-цифровые символы. Оно вводится в нижнем регистре.

1. Выберите **расположение** группы ресурсов. Это регион, выбранный для установки Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Параметры кластера Kubernetes

1. В колонке "Создание кластера Kubernetes" выберите **Kubernetes Cluster Settings** (Параметры кластера Kubernetes).

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. Введите **имя пользователя администратора виртуальной машины Linux**. Это имя пользователя для виртуальных машин Linux, которые являются частью кластера Kubernetes и динамического административного представления.

1. Введите **открытый ключ SSH**, используемый для авторизации на всех компьютерах Linux, созданных как часть кластера Kubernetes и динамического административного представления.

1. Введите **префикс DNS главного профиля**, уникальный для региона. Это уникальное для региона имя, например `k8s-12345`. Рекомендуем выбрать то же имя, что и для группы ресурсов.

    > [!Note]  
    > Для каждого кластера используйте новый уникальный префикс DNS главного профиля.

1. Выберите **Kubernetes Master Pool Profile Count** (Счетчик профилей главного пула Kubernetes). Счетчик содержит количество узлов в главном пуле. Здесь можно указать значение от 1 до 7, но оно должно быть нечетным.

1. Выберите **The VMSize of the Kubernetes master VMs** (Размер главных виртуальных машин Kubernetes).

1. Выберите **Kubernetes Node Pool Profile** (Счетчик профилей узлов Kubernetes). Счетчик содержит число агентов в кластере. 

1. Выберите **профиль хранилища**. Можно выбрать **Blob Disk** (Диск больших двоичных объектов) или **Managed Disk** (Управляемый диск). Так указывается размер виртуальных машин узлов Kubernetes. 

1. Введите **идентификатор клиента субъекта-службы**. Он используется поставщиком облачных служб Azure для Kubernetes. Идентификатор клиента определяется как идентификатор приложения во время создания субъекта-службы.

1. Введите **секрет клиента субъекта-службы**, полученный при создании субъекта-службы.

1. Введите **версию поставщика облачных служб Azure для Kubernetes**. Это версия поставщика Azure для Kubernetes. Azure Stack выпускает специальную сборку Kubernetes для каждой версии Azure Stack.

### <a name="3-summary"></a>3. Сводка

1. Выберите "Сводка". В колонке отобразится сообщение проверки параметров конфигурации кластера Kubernetes.

    ![Развертывание шаблона решений](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Проверьте настройки.

3. Щелкните **ОК**, чтобы развернуть кластер.

> [!TIP]  
>  Если у вас есть вопросы о развертывании, вы можете разместить свой вопрос или поискать ответы на вопрос на [форуме Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="connect-to-your-cluster"></a>Подключение к кластеру

Теперь все готово для подключения к кластеру. Главный кластер можно найти в группе кластерных ресурсов. Он называется `k8s-master-<sequence-of-numbers>`. Подключитесь к главному кластеру, используя клиент SSH. Управлять кластером можно с помощью **kubectl**, клиента командной строки Kubernetes. Инструкции см. на сайте [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Можно также использовать диспетчер пакетов **Helm** для установки и развертывания приложений в кластер. Инструкции по установке и использованию Helm с кластером см. на сайте [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Дополнительная информация

[Добавление Kubernetes в Marketplace (для оператора Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes в Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
