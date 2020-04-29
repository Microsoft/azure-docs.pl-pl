---
title: PRZESTARZAŁE Monitorowanie klastra usługi Azure Kubernetes — Zarządzanie operacjami
description: Monitorowanie klastra Kubernetes w Azure Container Service przy użyciu Log Analytics
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 02d04076ccc41d243a493838667f5e8cc6bfa5ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371158"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>PRZESTARZAŁE Monitorowanie klastra Azure Container Service przy użyciu Log Analytics

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego artykułu korzystającego z usługi Azure Kubernetes, zobacz [Azure monitor for Containers](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym instruktażu przyjęto założenie, że [utworzono klaster Kubernetes przy użyciu Azure Container Service](container-service-kubernetes-walkthrough.md).

Przyjęto również założenie, że `az` masz zainstalowany Interfejs `kubectl` wiersza polecenia platformy Azure i narzędzia.

Możesz sprawdzić, czy `az` narzędzie jest zainstalowane, uruchamiając:

```azurecli
az --version
```

Jeśli nie masz zainstalowanego `az` narzędzia, w [tym miejscu](https://github.com/azure/azure-cli#installation)znajdują się instrukcje.
Alternatywnie możesz użyć [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), który ma interfejs wiersza polecenia `az` platformy Azure i `kubectl` narzędzia już zainstalowane.

Możesz sprawdzić, czy `kubectl` narzędzie jest zainstalowane, uruchamiając:

```console
kubectl version
```

Jeśli nie masz `kubectl` zainstalowanego programu, możesz uruchomić następujące polecenie:

```azurecli
az acs kubernetes install-cli
```

Aby przetestować, jeśli masz klucze Kubernetes zainstalowane w narzędziu polecenia kubectl, możesz uruchomić:

```console
kubectl get nodes
```

Jeśli powyższe polecenie wykroczy błędy, musisz zainstalować klucze klastra Kubernetes w narzędziu polecenia kubectl. Można to zrobić za pomocą następującego polecenia:

```azurecli
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Monitorowanie kontenerów za pomocą Log Analytics

Log Analytics to oparte na chmurze rozwiązanie do zarządzania działem IT firmy Microsoft, które ułatwia zarządzanie infrastrukturą lokalną i chmurową oraz jej ochronę.Rozwiązanie kontenera to rozwiązanie w Log Analytics, które ułatwia wyświetlanie spisu kontenerów, wydajności i dzienników w jednej lokalizacji. Można przeprowadzać inspekcję, rozwiązywać problemy z kontenerami, wyświetlając dzienniki w scentralizowanej lokalizacji i wyszukując zakłócenia zużywające nadmiar kontenera na hoście.

![](media/container-service-monitoring-oms/image1.png)

Aby uzyskać więcej informacji na temat rozwiązania kontenera, zapoznaj się z [log Analytics rozwiązania kontenera](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Instalowanie Log Analytics w Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Uzyskaj identyfikator i klucz obszaru roboczego
Aby Agent Log Analytics mógł komunikować się z usługą, należy ją skonfigurować przy użyciu identyfikatora obszaru roboczego i klucza obszaru roboczego. Aby uzyskać identyfikator i klucz obszaru roboczego, należy utworzyć konto w <https://mms.microsoft.com>usłudze.
Postępuj zgodnie z instrukcjami, aby utworzyć konto. Po zakończeniu tworzenia konta możesz uzyskać identyfikator i klucz, klikając blok **log Analytics** , a następnie nazwę obszaru roboczego. Następnie w obszarze **Ustawienia zaawansowane**, **połączone źródła**, a następnie **serwery z systemem Linux**znajdziesz potrzebne informacje, jak pokazano poniżej.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Zainstaluj agenta Log Analytics przy użyciu elementu daemonset
DaemonSets są używane przez Kubernetes do uruchamiania pojedynczego wystąpienia kontenera na każdym hoście w klastrze.
Są one idealne do uruchamiania agentów monitorowania.

Oto [plik elementu DAEMONSET YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Zapisz ją w pliku o nazwie `oms-daemonset.yaml` i Zastąp wartości symbolu zastępczego dla `WSID` i `KEY` z identyfikatorem i kluczem obszaru roboczego w pliku.

Po dodaniu identyfikatora i klucza obszaru roboczego do konfiguracji elementu daemonset można zainstalować agenta Log Analytics w klastrze za pomocą narzędzia wiersza `kubectl` polecenia:

```console
kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Instalowanie agenta Log Analytics przy użyciu klucza tajnego Kubernetes
Aby chronić identyfikator obszaru roboczego Log Analytics i klucz, możesz użyć klucza tajnego Kubernetes jako części pliku elementu daemonset YAML.

- Skopiuj skrypt, plik tajny szablonu i plik elementu daemonset YAML (z [repozytorium](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) i upewnij się, że znajdują się one w tym samym katalogu.
  - skrypt generowania wpisu tajnego — secret-gen.sh
  - Secret Template-Secret-Template. YAML
    - Elementu daemonset YAML File-omsagent-ds-Secret. YAML
- Uruchom skrypt. Skrypt będzie pytał o identyfikator obszaru roboczego Log Analytics i klucz podstawowy. Wstaw, a skrypt utworzy plik tajny YAML, aby można było go uruchomić.

  ```console
  sudo bash ./secret-gen.sh
  ```

  - Utwórz wpisy tajne pod, wykonując następujące czynności:

     ```console
     kubectl create -f omsagentsecret.yaml
     ```

  - Aby sprawdzić, uruchom następujące polecenie:

  ```console
  kubectl get secrets
  ```

  ```output
  NAME                  TYPE                                  DATA      AGE
  default-token-gvl91   kubernetes.io/service-account-token   3         50d
  omsagent-secret       Opaque                                2         1d
  root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
  Name:           omsagent-secret
  Namespace:      default
  Labels:         <none>
  Annotations:    <none>

  Type:   Opaque

  Data
  ====
  WSID:   36 bytes
  KEY:    88 bytes
  ```

  - Utwórz zestaw demonów omsagent, uruchamiając następujące elementy:
  
  ```console
  kubectl create -f omsagent-ds-secrets.yaml
  ```

### <a name="conclusion"></a>Podsumowanie
Gotowe. Po kilku minutach powinno być możliwe wyświetlenie danych przepływających do pulpitu nawigacyjnego Log Analytics.
