---
title: Lokalizacje niestandardowe na platformie Azure z włączonym łukiem Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions
description: Korzystanie z lokalizacji niestandardowych do wdrażania usług Azure PaaS na klastrach Kubernetes z włączonym usługą Azure Arc
ms.openlocfilehash: ddda6420acd7126cb46b043f5c1bce67758342bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451157"
---
# <a name="custom-locations-on-azure-arc-enabled-kubernetes"></a>Lokalizacje niestandardowe na platformie Azure z włączonym łukiem Kubernetes

Jako rozszerzenie lokalizacji platformy Azure *niestandardowe lokalizacje* umożliwiają administratorom dzierżawy używanie klastrów Kubernetes z obsługą usługi Azure Arc jako lokalizacji docelowych na potrzeby wdrażania wystąpień usług platformy Azure. Przykłady zasobów platformy Azure obejmują wystąpienie zarządzane SQL z włączonym usługą Azure Arc i PostgreSQL.

Podobnie jak w przypadku lokalizacji platformy Azure, użytkownicy końcowi w dzierżawie z dostępem do lokalizacji niestandardowych mogą wdrażać zasoby przy użyciu prywatnych obliczeń firmy.

Przegląd koncepcyjny tej funkcji jest dostępny w [lokalizacjach niestandardowych — artykuł z Kubernetes Azure Arc](conceptual-custom-locations.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Zainstaluj lub Uaktualnij interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) do wersji >= 2.16.0.

- `connectedk8s` (wersja >= 1.1.0), `k8s-extension` (wersja >= 0.2.0) i `customlocation` (wersja >= 0.1.0) rozszerzenia interfejsu wiersza polecenia platformy Azure. Zainstaluj te rozszerzenia interfejsu wiersza polecenia platformy Azure, uruchamiając następujące polecenia:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Jeśli `connectedk8s` `k8s-extension` `customlocation` rozszerzenia i są już zainstalowane, można je zaktualizować do najnowszej wersji za pomocą następującego polecenia:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- Rejestracja dostawcy została zakończona `Microsoft.ExtendedLocation` .
    1. Wprowadź następujące polecenia:
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. Monitorowanie procesu rejestracji. Rejestracja może potrwać do 10 minut.
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

>[!NOTE]
>**Obsługiwane regiony dla lokalizacji niestandardowych:**
>* East US
>* West Europe

## <a name="enable-custom-locations-on-cluster"></a>Włączanie lokalizacji niestandardowych w klastrze

Aby włączyć tę funkcję w klastrze, wykonaj następujące polecenie:

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

> [!NOTE]
> 1. Funkcja lokalizacji niestandardowych jest zależna od funkcji Połącz z klastrem. Aby lokalizacje niestandardowe działały, należy włączyć obie funkcje.
> 2. `az connectedk8s enable-features` należy uruchomić na komputerze, na którym znajduje się `kubeconfig` plik wskazujący na klaster, na którym są włączone funkcje.

## <a name="create-custom-location"></a>Utwórz niestandardową lokalizację

1. Utwórz klaster Kubernetes z obsługą usługi Azure Arc.
    - Jeśli klaster nie został jeszcze połączony, Skorzystaj z naszego [przewodnika Szybki Start](quickstart-connect-cluster.md).
    - [Uaktualnij agentów](agent-upgrade.md#manually-upgrade-agents) do wersji >= 1.1.0.

1. Wdróż rozszerzenie klastra usługi platformy Azure, której wystąpienie ostatecznie zawarto na początku lokalizacji niestandardowej:

    ```azurecli
    az k8s-extension create --name <extensionInstanceName> --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
    ```

    > [!NOTE]
    > Serwer proxy wychodzący bez uwierzytelniania i serwera proxy wychodzącego z uwierzytelnianiem podstawowym jest obsługiwany przez rozszerzenie klastra z włączonym łukiem Data Services. Wychodzący serwer proxy, który oczekuje zaufanych certyfikatów, nie jest obecnie obsługiwany.

1. Pobierz identyfikator Azure Resource Manager klastra Kubernetes z obsługą usługi Azure ARC, do którego odwołuje się w kolejnych krokach `connectedClusterId` :

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Pobierz Azure Resource Manager identyfikator rozszerzenia klastra wdrożonego w klastrze Kubernetes z włączoną funkcją Azure ARC, do którego odwołuje się w kolejnych krokach `extensionId` :

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Utwórz lokalizację niestandardową, odwołując się do klastra Kubernetes z obsługą usługi Azure Arc i rozszerzenia:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> Bezpieczne nawiązywanie połączenia z klastrem przy użyciu funkcji [Połącz z klastrem](cluster-connect.md)