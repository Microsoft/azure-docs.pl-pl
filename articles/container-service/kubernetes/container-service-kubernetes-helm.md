---
title: PRZESTARZAŁE Wdrażanie kontenerów za pomocą Helm na platformie Azure Kubernetes
description: Używanie narzędzia Helm pakowanie do wdrażania kontenerów w klastrze Kubernetes w Azure Container Service
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76271086"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>PRZESTARZAŁE Wdrażanie kontenerów w klastrze Kubernetes przy użyciu programu Helm

> [!TIP]
> Zaktualizowaną wersję tego artykułu korzystającego z usługi Azure Kubernetes Service można znaleźć [w temacie Install Applications with Helm in Azure Kubernetes Service (AKS)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) to narzędzie do tworzenia pakietów typu "open source", które ułatwia Instalowanie i zarządzanie cyklem życia aplikacji Kubernetes. Podobnie jak w przypadku menedżerów pakietów systemu Linux, takich jak apt-get i yum, Helm służy do zarządzania wykresami Kubernetes, które są pakietami wstępnie skonfigurowanych zasobów Kubernetes. W tym artykule przedstawiono sposób pracy z usługą Helm w klastrze Kubernetes wdrożonym w Azure Container Service.

Helm ma dwa składniki: 
* **Interfejs wiersza polecenia Helm** jest klientem działającym na komputerze lokalnym lub w chmurze  

* **Jest to** serwer, który działa w klastrze Kubernetes i zarządza cyklem życia aplikacji Kubernetes 
 
## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie klastra Kubernetes](container-service-kubernetes-walkthrough.md) w Azure Container Service

* [Instalowanie i Konfigurowanie `kubectl` ](../container-service-connect.md) na komputerze lokalnym

* [Instalowanie Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) na komputerze lokalnym

## <a name="helm-basics"></a>Helm — podstawy 

Aby wyświetlić informacje o klastrze Kubernetes, który jest instalowany, i wdrożyć aplikacje w programie, wpisz następujące polecenie:

```bash
kubectl cluster-info 
```
![polecenia kubectl — informacje o klastrze](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Po zainstalowaniu Helm Zainstaluj program do logowania do klastra Kubernetes, wpisując następujące polecenie:

```bash
helm init --upgrade
```
Po pomyślnym zakończeniu zostanie wyświetlone dane wyjściowe podobne do następujących:

![Instalacja przez Instalatora](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Aby wyświetlić wszystkie wykresy Helm dostępne w repozytorium, wpisz następujące polecenie:

```bash 
helm search 
```

Zobaczysz dane wyjściowe podobne do następujących:

![Wyszukiwanie Helm](./media/container-service-kubernetes-helm/helm-search.png)
 
Aby zaktualizować wykresy w celu uzyskania najnowszych wersji, wpisz:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Wdrażanie wykresu kontrolera danych wejściowych Nginx 
 
Aby wdrożyć wykres kontrolera transferu danych przychodzących Nginx, wpisz pojedyncze polecenie:

```bash
helm install stable/nginx-ingress 
```
![Wdróż kontroler transferu danych przychodzących](./media/container-service-kubernetes-helm/nginx-ingress.png)

Jeśli wpiszesz `kubectl get svc` , aby wyświetlić wszystkie usługi działające w klastrze, zobaczysz, że adres IP jest przypisany do kontrolera transferu danych przychodzących. (Podczas gdy przypisanie jest w toku, zobaczysz `<pending>`. Wykonanie tej czynności trwa kilka minut. 

Po przypisaniu adresu IP przejdź do wartości zewnętrznego adresu IP, aby zobaczyć, że zaplecze nginx jest uruchomione. 
 
![Adres IP ruchu przychodzącego](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Aby wyświetlić listę wykresów zainstalowanych w klastrze, wpisz:

```bash
helm list 
```

Można skrócić polecenie do `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Wdrażanie wykresu MariaDB i klienta

Teraz Wdróż wykres MariaDB i klienta MariaDB w celu nawiązania połączenia z bazą danych.

Aby wdrożyć wykres MariaDB, wpisz następujące polecenie:

```bash
helm install --name v1 stable/mariadb
```

gdzie `--name` jest tagiem używanym w wersjach.

> [!TIP]
> Jeśli wdrożenie nie powiedzie się `helm repo update` , uruchom polecenie i spróbuj ponownie.
>
 
 
Aby wyświetlić wszystkie wykresy wdrożone w klastrze, wpisz:

```bash 
helm list
```
 
Aby wyświetlić wszystkie wdrożenia uruchomione w klastrze, wpisz:

```bash
kubectl get deployments 
``` 
 
 
Aby na koniec uzyskać dostęp do klienta programu, wpisz:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Aby nawiązać połączenie z klientem, wpisz następujące polecenie, zastępując `v1-mariadb` je nazwą wdrożenia:

```bash
sudo mysql –h v1-mariadb
```
 
 
Możesz teraz używać standardowych poleceń SQL do tworzenia baz danych, tabel itp. Na przykład `Create DATABASE testdb1;` tworzy pustą bazę danych. 
 
 
 
## <a name="next-steps"></a>Następne kroki

* Więcej informacji o zarządzaniu wykresami Kubernetes można znaleźć w [dokumentacji Helm](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

