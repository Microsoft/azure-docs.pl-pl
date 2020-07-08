---
title: PRZESTARZAŁE Zarządzanie klastrem usługi Azure Kubernetes za pomocą interfejsu użytkownika sieci Web
description: Korzystanie z interfejsu użytkownika sieci Web Kubernetes w Azure Container Service
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371141"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>PRZESTARZAŁE Używanie interfejsu użytkownika sieci Web Kubernetes z Azure Container Service

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego artykułu korzystającego z usługi Azure Kubernetes, zobacz [dostęp do pulpitu nawigacyjnego sieci Web Kubernetes w usłudze Azure Kubernetes Service (AKS)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym instruktażu przyjęto założenie, że [utworzono klaster Kubernetes przy użyciu Azure Container Service](container-service-kubernetes-walkthrough.md).


Przyjęto również założenie, że masz zainstalowany interfejs wiersza polecenia platformy Azure i `kubectl` Narzędzia.

Możesz sprawdzić, czy `az` Narzędzie jest zainstalowane, uruchamiając:

```azurecli
az --version
```

Jeśli nie masz `az` zainstalowanego narzędzia, w [tym miejscu](https://github.com/azure/azure-cli#installation)znajdują się instrukcje.

Możesz sprawdzić, czy `kubectl` Narzędzie jest zainstalowane, uruchamiając:

```console
kubectl version
```

Jeśli nie masz `kubectl` zainstalowanego programu, możesz uruchomić następujące polecenie:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Omówienie

### <a name="connect-to-the-web-ui"></a>Nawiązywanie połączenia z interfejsem użytkownika sieci Web
Interfejs użytkownika sieci Web Kubernetes można uruchomić, uruchamiając następujące elementy:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Powinno to spowodować otwarcie przeglądarki sieci Web skonfigurowanej do komunikacji z bezpiecznym serwerem proxy łączącym komputer lokalny z interfejsem użytkownika sieci Web Kubernetes.

### <a name="create-and-expose-a-service"></a>Tworzenie i Uwidacznianie usługi
1. W interfejsie użytkownika sieci Web Kubernetes kliknij przycisk **Utwórz** w prawym górnym oknie.

    ![Kubernetes — Tworzenie interfejsu użytkownika](./media/container-service-kubernetes-ui/create.png)

    Zostanie otwarte okno dialogowe, w którym można rozpocząć tworzenie aplikacji.

2. Nadaj mu nazwę `hello-nginx` . Użyj [ `nginx` kontenera z platformy Docker](https://hub.docker.com/_/nginx/) i Wdróż trzy repliki tej usługi sieci Web.

    ![Kubernetes pod utworzeniem okna dialogowego](./media/container-service-kubernetes-ui/nginx.png)

3. W obszarze **Usługa**wybierz pozycję **zewnętrzne** i wprowadź port 80.

    To ustawienie równoważy obciążenie ruchem do trzech replik.

    ![Okno dialogowe tworzenia usługi Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Kliknij przycisk **Wdróż** , aby wdrożyć te kontenery i usługi.

    ![Wdrożenie na platformie Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Wyświetlanie kontenerów
Po kliknięciu przycisku **Wdróż**w interfejsie użytkownika zostanie wyświetlony widok usługi podczas wdrażania:

![Stan Kubernetes](./media/container-service-kubernetes-ui/status.png)

Stan każdego obiektu Kubernetes można zobaczyć w okręgu po lewej stronie interfejsu użytkownika w obszarze **zasobników**. Jeśli jest to całkowicie pełny okrąg, obiekt nadal jest wdrażany. Gdy obiekt jest w pełni wdrożony, wyświetla zielony znacznik wyboru:

![Kubernetes wdrożone](./media/container-service-kubernetes-ui/deployed.png)

Gdy wszystko jest uruchomione, kliknij jeden z Twoich zasobników, aby wyświetlić szczegółowe informacje o działającej usłudze sieci Web.

![Kubernetes](./media/container-service-kubernetes-ui/pods.png)

W widoku **podst** . można zobaczyć informacje o kontenerach w obszarze, a także zasoby procesora i pamięci używane przez te kontenery:

![Zasoby Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Jeśli nie widzisz zasobów, może być konieczne odczekanie kilku minut na propagację danych monitorowania.

Aby wyświetlić dzienniki dla swojego kontenera, kliknij przycisk **Wyświetl dzienniki**.

![Dzienniki Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Wyświetlanie usługi
Oprócz uruchamiania kontenerów, interfejs użytkownika Kubernetes utworzył zewnętrzny, `Service` który inicjuje moduł równoważenia obciążenia, aby zapewnić ruch do kontenerów w klastrze.

W okienku nawigacji po lewej stronie kliknij pozycję **usługi** , aby wyświetlić wszystkie usługi (tylko jeden).

![Usługi Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

W tym widoku powinien zostać wyświetlony zewnętrzny punkt końcowy (adres IP), który został przydzielony do usługi.
Po kliknięciu tego adresu IP powinien zostać wyświetlony kontener Nginx uruchomiony za modułem równoważenia obciążenia.

![Widok Nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Zmienianie rozmiarów usługi
Oprócz wyświetlania obiektów w interfejsie użytkownika można edytować i aktualizować obiekty interfejsu API Kubernetes.

Najpierw kliknij pozycję **wdrożenia** w okienku nawigacji po lewej stronie, aby zobaczyć wdrożenie usługi.

Gdy jesteś w tym widoku, kliknij zestaw replik, a następnie kliknij przycisk **Edytuj** na górnym pasku nawigacyjnym:

![Kubernetes Edytuj](./media/container-service-kubernetes-ui/edit.png)

Edytuj `spec.replicas` pole `2` , a następnie kliknij przycisk **Aktualizuj**.

Powoduje to, że liczba replik do porzucenia dwa przez usunięcie jednego z nich.

 

