---
title: Создание контроллера входящего HTTP-трафика со статическим IP-адресом в Службе Azure Kubernetes (AKS)
description: Сведения об установке и настройке контроллера входящего трафика NGINX со статическим общедоступным IP-адресом в кластере Службы Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 0ffa1541439890a0591b52c1fdbc717c7d5aa5ff
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362564"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Создание контроллера входящего трафика со статическим общедоступным IP-адресом в Службе Azure Kubernetes (AKS)

Контроллер входящего трафика является программным компонентом, предоставляющим для служб Kubernetes обратный прокси-сервер, настраиваемую маршрутизацию трафика и обработку подключений TLS для последующей передачи. Ресурсы входящего трафика Kubernetes используются при настройке правил входящего трафика и маршрутов для отдельных служб Kubernetes. Применяя контроллер и правила входящего трафика, для маршрутизации трафика в несколько служб в кластере Kubernetes можно использовать один IP-адрес.

В этой статье описывается, как установить и настроить [контроллер входящего трафика NGINX][nginx-ingress] в кластере Службы Azure Kubernetes (AKS). Контроллер входящего трафика настраивается со статическим общедоступным IP-адресом. Для автоматического создания и настройки сертификатов [Let's Encrypt][lets-encrypt] используется проект [cert-manager][cert-manager]. Наконец, в кластере AKS запущено два приложения, каждое из которых доступно по одному IP-адресу.

Также можно:

- [Создать базовый контроллер входящего трафика с внешним сетевым подключением.][aks-ingress-basic]
- [Включить надстройку маршрутизации приложений HTTP.][aks-http-app-routing]
- [Создать контроллер входящего трафика, который использует внутреннюю, частную сети и IP-адрес.][aks-ingress-internal]
- [Создать контроллер входящего трафика с динамическим общедоступным IP-адресом и настроить шифрование для автоматического создания TLS-сертификатов.][aks-ingress-tls]

## <a name="before-you-begin"></a>Перед началом работы

В этой статье для установки контроллера входящего трафика NGINX cert-manager и примера веб-приложения используется Helm. Поэтому необходимо инициализировать Helm в кластере AKS и использовать учетную запись службы для Tiller. Убедитесь, что вы используете последний выпуск Helm. Убедитесь, что вы используете последний выпуск Helm. Инструкции по обновлению см. в документации [по установке Helm][helm-install]. Дополнительную информацию о настройке и использовании Helm см. в статье [Использование Helm со службой Azure Kubernetes][use-helm].

Для этой статьи требуется Azure CLI версии 2.0.41 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Создание контроллера входящего трафика

По умолчанию контроллер входящего трафика NGINX создается с новым назначением общедоступного IP-адреса. Этот общедоступный IP-адрес является статическим только на время жизненного цикла контроллера входящего трафика и теряется, если контроллер удаляется и создается повторно. Общее требование конфигурации — предоставить контроллеру входящего трафика NGINX имеющийся статический общедоступный IP-адрес. Статический общедоступный IP-адрес останется, если контроллер входящего трафика будет удален. Такой подход позволяет согласованно использовать имеющиеся записи DNS и сетевые конфигурации на протяжении жизненного цикла приложений.

Если необходимо создать статический общедоступный IP-адрес, сначала нужно получить имя группы ресурсов кластера AKS с помощью команды [az aks show][az-aks-show].

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Создайте общедоступный IP-адрес с помощью метода *статического* выделения, используя команду [az network public-ip create][az-network-public-ip-create]. В следующем примере создается общедоступный IP-адрес *myPublicIP* в группе ресурсов AKS, о которой говорилось на предыдущих шагах.

```azurecli
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Разверните диаграмму *nginx ingress* с помощью Helm. Добавьте параметр `--set controller.service.loadBalancerIP` и укажите собственный общедоступный IP-адрес, созданный на предыдущем шаге. Для обеспечения дополнительной избыточности развертываются две реплики контроллеров входящего трафика NGINX с использованием параметра `--set controller.replicaCount`. Чтобы максимально эффективно использовать реплики контроллера входящего трафика, убедитесь, что в кластере AKS используется несколько узлов.

> [!TIP]
> Следующие примеры устанавливают контроллер входящего трафика и сертификаты в пространстве имен `kube-system`. При необходимости вы можете указать другое пространство имен для своей среды. Кроме того, если в кластере AKS не включено RBAC, добавьте `--set rbac.create=false` в команду.

```console
helm install stable/nginx-ingress \
    --namespace kube-system \
    --set controller.service.loadBalancerIP="40.121.63.72"  \
    --set controller.replicaCount=2
```

При создании службы распределения нагрузки Kubernetes входящего контроллера NGINX назначается статический IP-адрес, как показано в следующем примере выходных данных.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Так как правила входящего трафика не созданы, при переходе к общедоступному IP-адресу по умолчанию будет отображаться страница контроллеров входящего трафика NGINX с ошибкой "404 — страница не найдена". В следующих шагах настраиваются правила входящего трафика.

## <a name="configure-a-dns-name"></a>Настройка DNS-имени

Чтобы сертификаты HTTPS работали правильно, настройте для IP-адреса контроллера входящего трафика полное доменное имя. Укажите в этом скрипте IP-адрес контроллера входящего трафика и уникальное имя, которое вы хотите использовать в качестве полного доменного имени.

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Теперь к контроллеру входящего трафика можно получить доступ по полному доменному имени.

## <a name="install-cert-manager"></a>Установка cert-manager

Контроллер входящего трафика NGINX поддерживает обработку подключений TLS для последующей передачи. Получить и настроить сертификаты для HTTPS можно несколькими способами. В этой статье демонстрируется использование проекта [cert-manager][cert-manager], который обеспечивает автоматические функции создания сертификатов [Lets Encrypt][lets-encrypt] и управления ими.

> [!NOTE]
> В этой статье используется среда для Let's Encrypt `staging`. В производственных развертываниях используйте `letsencrypt-prod` и `https://acme-v02.api.letsencrypt.org/directory` в определениях ресурсов и при установке диаграммы Helm.

Чтобы установить контроллер cert-manager в кластере с поддержкой RBAC, используйте следующую команду `helm install`: При необходимости измените `--namespace` на что-то иное вместо *kube-system*:

```console
helm install stable/cert-manager \
  --namespace kube-system \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Если кластер не поддерживает RBAC, используйте следующую команду:

```console
helm install stable/cert-manager \
  --namespace kube-system \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Дополнительные сведения о конфигурации cert-manager см. в описании [проекта cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Создание издателя кластера ЦС

Для выдачи сертификатов средству cert-manager нужен ресурс [Issuer][cert-manager-issuer] или [ClusterIssuer][cert-manager-cluster-issuer]. Эти ресурсы Kubernetes имеют аналогичную функциональность, однако `Issuer` работает в отдельном пространстве имен, а `ClusterIssuer` — во всех. Дополнительные сведения см. в документации по [издателю cert-manager][cert-manager-issuer].

Создайте издатель кластера, например `cluster-issuer.yaml`, используя приведенный ниже пример манифеста. Измените адрес электронной почты на действительный адрес вашей организации:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Чтобы создать издатель, используйте команду `kubectl apply -f cluster-issuer.yaml`.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="create-a-certificate-object"></a>Создание объекта сертификата

Далее нужно создать ресурс сертификата. Этот ресурс сертификата определяет необходимый сертификат X.509. Дополнительные сведения см. в описании [сертификатов cert-manager][cert-manager-certificates].

Создайте ресурс сертификата, например `certificates.yaml`, используя приведенный ниже пример манифеста. Обновите *dnsNames* и *домены*, указав для них DNS-имя, которое вы создали на предыдущем шаге. Если вы используете только внутренний контроллер входящего трафика, укажите внутреннее имя DNS для службы.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Чтобы создать ресурс сертификата, используйте команду `kubectl apply -f certificates.yaml`.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="run-demo-applications"></a>Запуск демонстрационных версий приложений

Контроллер входящего трафика и решение по управлению сертификатами настроены. Теперь запустите две демонстрационные версии приложения в своем кластере AKS. В этом примере для развертывания двух экземпляров простого приложения Hello World применяется Helm.

Чтобы установить примеры диаграмм Helm, добавьте репозиторий примеров Azure в свою среду Helm таким образом:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Создайте первую демонстрационную версию приложения из диаграммы Helm с помощью такой команды:

```console
helm install azure-samples/aks-helloworld
```

Установите второй экземпляр демонстрационной версии приложения. Укажите новый заголовок для второго экземпляра, чтобы оба приложения визуально отличались. Также задайте уникальное имя службы:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Создание маршрута для входящего трафика

Теперь оба приложения запущены на кластере Kubernetes, несмотря на то что они настроены с помощью службы типа `ClusterIP`. Таким образом приложения не доступны через Интернет. Чтобы сделать их доступными, создайте ресурс входящего трафика Kubernetes. Ресурс входящего трафика настраивает правила, по которым осуществляется маршрутизация трафика к одному из двух приложений.

В следующем примере трафик по адресу `https://demo-aks-ingress.eastus.cloudapp.azure.com/` направляется в службу `aks-helloworld`. Трафик по адресу `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` направляется в службу `ingress-demo`. Обновите *узлы* и *узел*, указав DNS-имя, которое вы создали на предыдущем шаге.

Создайте файл `hello-world-ingress.yaml` и скопируйте в него следующий пример кода YAML:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Создайте ресурс входящего трафика, используя команду `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Проверка конфигурации входящего трафика

Откройте в браузере адрес с полным доменным именем вашего контроллера входящего трафика Kubernetes, например *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Поскольку в этих примерах используется `letsencrypt-staging`, браузер не доверяет выданному сертификату SSL. Чтобы продолжить работу с приложением, примите предупреждение. Информация о сертификате свидетельствует о том, что этот сертификат *Fake LE Intermediate X1* выдан Let's Encrypt. Этот поддельный сертификат указывает на то, что `cert-manager` правильно обработал запрос и получил сертификат от поставщика.

![Промежуточный сертификат Let's Encrypt](media/ingress/staging-certificate.png)

Когда вы меняете настройки Let's Encrypt, чтобы использовать `prod` вместо `staging`, применяется доверенный сертификат, выданный Let's Encrypt, как показано в следующем примере:

![Сертификат Let's Encrypt](media/ingress/certificate.png)

В браузере отображается демонстрационная версия приложения:

![Первый пример приложения](media/ingress/app-one.png)

Теперь добавьте к FQDN путь */hello-world-two*, например *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. Отобразится второе демонстрационное приложение с пользовательским заголовком:

![Второй пример приложения](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье для установки компонентов обработки входящего трафика, сертификатов и примеров приложений используется Helm. При развертывании диаграммы Helm создается несколько ресурсов Kubernetes. К ним относятся элементы pod, развертывания и службы. Чтобы очистить их, необходимо сначала удалить ресурсы сертификатов.

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Теперь получите список выпусков Helm с помощью команды `helm list`. Найдите диаграммы *nginx-ingress*, *cert-manager* и *aks-helloworld*, как показано в следующем примере выходных данных.

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Tue Oct 16 17:44:28 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
alliterating-peacock    1           Tue Oct 16 18:03:11 2018    DEPLOYED    cert-manager-v0.3.4     v0.3.2      kube-system
mollified-armadillo     1           Tue Oct 16 18:04:53 2018    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Tue Oct 16 18:04:56 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Удалить выпуски командой `helm delete`. В следующем примере удаляются развертывание контроллера входящего трафика NGINX, диспетчер сертификатов и два примера приложений hello world для AKS.

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Затем удалите репозиторий Helm, используемый для приложения hello world для AKS.

```console
helm repo remove azure-samples
```

Удалите маршрут входящего трафика, направлявший трафик в пример приложения.

```console
kubectl delete -f hello-world-ingress.yaml
```

Наконец, удалите статический общедоступный IP-адрес, созданный для контроллера входящего трафика. Укажите имя группы ресурсов кластера *MC_*, полученное на первом шаге в этой статье, например *MC_myResourceGroup_myAKSCluster_eastus*.

```azurecli
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Дополнительная информация

В данной статье упоминаются некоторые внешние компоненты для AKS. Чтобы узнать больше об этих компонентах, см. следующие страницы проекта:

- [Интерфейс командной строки Helm][helm-cli]
- [Контроллер входящего трафика NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Также можно:

- [Создать базовый контроллер входящего трафика с внешним сетевым подключением.][aks-ingress-basic]
- [Включить надстройку маршрутизации приложений HTTP.][aks-http-app-routing]
- [Создать контроллер входящего трафика, который использует внутреннюю, частную сети и IP-адрес.][aks-ingress-internal]
- [Создать контроллер входящего трафика с динамическим общедоступным IP-адресом и настроить шифрование для автоматического создания TLS-сертификатов.][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
