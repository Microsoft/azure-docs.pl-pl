---
title: Rozszerzenia klastra Kubernetes z obsługą usługi Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Wdrażanie i zarządzanie cyklem życia rozszerzeń na platformie Azure z włączoną obsługą Kubernetes
ms.openlocfilehash: 854d7418515d7927a3c0b4b8790ed4770af555ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312624"
---
# <a name="kubernetes-cluster-extensions"></a>Rozszerzenia klastrów Kubernetes

Funkcja rozszerzeń Kubernetes umożliwia korzystanie z następujących funkcji usługi Azure ARC z obsługą klastrów Kubernetes:

* Wdrażanie rozszerzenia klastra opartego na Azure Resource Manager.
* Zarządzanie cyklem życia na wykresach Helm rozszerzeń.

Przegląd koncepcyjny tej funkcji jest dostępny w obszarze [rozszerzenia klastra — artykuł Kubernetes z funkcją Azure Arc](conceptual-extensions.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Zainstaluj lub Uaktualnij interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) do wersji >= 2.16.0.
- `connectedk8s` (wersja >= 1.1.0) i `k8s-extension` (wersja >= 0.2.0) rozszerzenia interfejsu wiersza polecenia platformy Azure. Zainstaluj te rozszerzenia interfejsu wiersza polecenia platformy Azure, uruchamiając następujące polecenia:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    Jeśli `connectedk8s` rozszerzenie i `k8s-extension` jest już zainstalowane, można je zaktualizować do najnowszej wersji za pomocą następującego polecenia:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- Istniejący klaster z włączonym usługą Azure Arc Kubernetes.
    - Jeśli klaster nie został jeszcze połączony, Skorzystaj z naszego [przewodnika Szybki Start](quickstart-connect-cluster.md).
    - [Uaktualnij agentów](agent-upgrade.md#manually-upgrade-agents) do wersji >= 1.1.0.

## <a name="currently-available-extensions"></a>Aktualnie dostępne rozszerzenia

| Wewnętrzny | Opis |
| --------- | ----------- |
| [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | Zapewnia wgląd w wydajność obciążeń wdrożonych w klastrze Kubernetes. Zbiera metryki wykorzystania pamięci i procesora z kontrolerów, węzłów i kontenerów. |
| [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Zbiera informacje związane z zabezpieczeniami, takimi jak dane dziennika inspekcji z klastra Kubernetes. Zawiera zalecenia i alerty dotyczące zagrożeń na podstawie zebranych danych. |

## <a name="usage-of-cluster-extensions"></a>Użycie rozszerzeń klastra

### <a name="create-extensions-instance"></a>Utwórz wystąpienie rozszerzeń

Utwórz nowe wystąpienie rozszerzenia z `k8s-extension create` , przekazując wartości parametrów obowiązkowych. Poniższe polecenie tworzy Azure Monitor dla wystąpienia rozszerzenia kontenerów w klastrze Kubernetes z włączoną funkcją Azure ARC:

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Rozdzielczości**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * Usługa nie może zachować poufnych informacji przez ponad 48 godzin. Jeśli usługa Azure ARC z obsługą Kubernetes nie ma łączności sieciowej przez ponad 48 godzin i nie można określić, czy ma zostać utworzone rozszerzenie klastra, rozszerzenie przechodzi do `Failed` stanu. Raz w `Failed` stanie trzeba będzie ponownie uruchomić program, `k8s-extension create` Aby utworzyć nowy zasób platformy Azure z rozszerzeniem.
> * * Azure Monitor dla kontenerów to pojedyncze rozszerzenie (tylko jeden wymagany dla klastra). Przed zainstalowaniem tych samych elementów za pomocą rozszerzeń należy oczyścić wszystkie wcześniejsze instalacje wykresów Helm Azure Monitor dla kontenerów (bez rozszerzeń). Postępuj zgodnie z instrukcjami dotyczącymi [usuwania wykresu Helm `az k8s-extension create` przed uruchomieniem ](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-hybrid).

**Wymagane parametry**

| Nazwa parametru | Opis |
|----------------|------------|
| `--name` | Nazwa wystąpienia rozszerzenia |
| `--extension-type` | Typ rozszerzenia, które chcesz zainstalować w klastrze. Na przykład: Microsoft. AzureMonitor. Containers, Microsoft. azuredefender. Kubernetes | 
| `--scope` | Zakres instalacji rozszerzenia `cluster` lub `namespace` |
| `--cluster-name` | Nazwa zasobu Kubernetes z włączoną funkcją Azure ARC, na którym ma zostać utworzone wystąpienie rozszerzenia |
| `--resource-group` | Grupa zasobów zawierająca zasób Kubernetes z obsługą usługi Azure Arc |
| `--cluster-type` | Typ klastra, na którym ma zostać utworzone wystąpienie rozszerzenia. Tylko bieżąca `connectedClusters` , która odnosi się do usługi Azure ARC z włączoną obsługą Kubernetes, jest zaakceptowaną wartością |

**Parametry opcjonalne**

| Nazwa parametru | Opis |
|--------------|------------|
| `--auto-upgrade-minor-version` | Wartość logiczna określająca, czy wersja pomocnicza rozszerzenia zostanie uaktualniona automatycznie. Wartość domyślna: `true`.  Jeśli ten parametr ma wartość true, nie można ustawić `version` parametru, ponieważ wersja będzie aktualizowana dynamicznie. Jeśli ta wartość `false` jest ustawiona na, rozszerzenie nie zostanie uaktualnione, nawet w przypadku wersji poprawki. |
| `--version` | Wersja rozszerzenia do zainstalowania (określona wersja, do której ma zostać przypięte wystąpienie rozszerzenia). Nie można podać, jeśli funkcja autoupgrade-wersja pomocnicza jest ustawiona na wartość `true` . |
| `--configuration-settings` | Ustawienia, które mogą być przesyłane do rozszerzenia w celu kontrolowania jego funkcjonalności. Muszą one być przesyłane jako pary oddzielające spacje `key=value` po nazwie parametru. Jeśli ten parametr jest używany w poleceniu, `--configuration-settings-file` nie może być używany w tym samym poleceniu. |
| `--configuration-settings-file` | Ścieżka do pliku JSON, która ma pary klucza wartości, które mają być używane do przekazywania ustawień konfiguracji do rozszerzenia. Jeśli ten parametr jest używany w poleceniu, `--configuration-settings` nie może być używany w tym samym poleceniu. |
| `--configuration-protected-settings` | Tych ustawień nie można pobrać przy użyciu `GET` wywołań interfejsu API ani `az k8s-extension show` poleceń i są one używane do przekazywania poufnych ustawień. Muszą one być przesyłane jako pary oddzielające spacje `key=value` po nazwie parametru. Jeśli ten parametr jest używany w poleceniu, `--configuration-protected-settings-file` nie może być używany w tym samym poleceniu. |
| `--configuration-protected-settings-file` | Ścieżka do pliku JSON, która ma pary klucza wartości, które mają być używane do przekazywania poufnych ustawień do rozszerzenia. Jeśli ten parametr jest używany w poleceniu, `--configuration-protected-settings` nie może być używany w tym samym poleceniu. |
| `--release-namespace` | Ten parametr wskazuje przestrzeń nazw, w której ma zostać utworzona wersja. Ten parametr ma zastosowanie tylko wtedy `scope` , gdy parametr jest ustawiony na `cluster` . |
| `--release-train` |  Autorzy rozszerzeń mogą publikować wersje w różnych pociągach wydania, takich jak `Stable` , `Preview` itp. Jeśli ten parametr nie jest jawnie ustawiony, `Stable` jest używany jako domyślny. Nie można użyć tego parametru `autoUpgradeMinorVersion` , gdy parametr jest ustawiony na `false` . |
| `--target-namespace` | Ten parametr wskazuje przestrzeń nazw, w której zostanie utworzona wersja. Uprawnienie do konta systemowego utworzonego dla tego wystąpienia rozszerzenia zostanie ograniczone do tej przestrzeni nazw. Ten parametr ma zastosowanie tylko wtedy, gdy `scope` parametr jest ustawiony na `namespace` . |

### <a name="show-details-of-an-extension-instance"></a>Pokaż szczegóły wystąpienia rozszerzenia

Wyświetl szczegóły aktualnie zainstalowanego wystąpienia rozszerzenia z `k8s-extension show` , przekazując wartości parametrów obowiązkowych:

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Rozdzielczości**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Wyświetl listę wszystkich rozszerzeń zainstalowanych w klastrze

Wyświetl listę wszystkich rozszerzeń zainstalowanych w klastrze z `k8s-extension list` , przekazując wartości parametrów obowiązkowych.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Rozdzielczości**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="delete-extension-instance"></a>Usuń wystąpienie rozszerzenia

Usuń wystąpienie rozszerzenia w klastrze z `k8s-extension delete` , przekazując wartości parametrów obowiązkowych.

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> Zasób platformy Azure reprezentujący to rozszerzenie zostanie natychmiast usunięty. Wersja Helm w klastrze skojarzonym z tym rozszerzeniem jest usuwana tylko wtedy, gdy agenty działający w klastrze Kubernetes mają łączność sieciową i mogą ponownie uzyskać dostęp do usług platformy Azure w celu pobrania żądanego stanu.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat rozszerzeń klastra dostępnych obecnie dla usługi Azure Arc Kubernetes:
> [!div class="nextstepaction"]
> [Azure monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) 
>  [Usługa Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)