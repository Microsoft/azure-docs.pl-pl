---
title: Wdrażanie usługi Azure Arc Data Controller | Tryb łączenia bezpośredniego
description: Wyjaśnia, jak wdrożyć kontroler danych w trybie bezpośredniego połączenia.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: overview
ms.openlocfilehash: 220618f167237d5937766eb5e28b9b6569cef76a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031498"
---
#  <a name="deploy-azure-arc-data-controller--direct-connect-mode"></a>Wdrażanie usługi Azure Arc Data Controller | Tryb łączenia bezpośredniego

W tym artykule opisano sposób wdrażania kontrolera danych usługi Azure Arc w trybie bezpośredniej łączności w bieżącej wersji zapoznawczej tej funkcji. 

Obecnie można utworzyć kontroler danych usługi Azure Arc na podstawie Azure Portal. Inne narzędzia dla usług danych z obsługą usługi Azure Arc nie obsługują tworzenia kontrolera danych w trybie bezpośredniego połączenia. Aby uzyskać szczegółowe informacje, zobacz [znane problemy — usługi danych z obsługą usługi Azure ARC (wersja zapoznawcza)](known-issues.md).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>Ukończ wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały spełnione wymagania wstępne w temacie [Deploy Data Controller — Direct Connect Mode — wymagania wstępne](deploy-data-controller-direct-mode-prerequisites.md).

W tym artykule wyjaśniono, jak wykonać następujące zadania:

1. Utwórz rozszerzenie usługi danych z włączonym łukiem platformy Azure. 
1. Utwórz niestandardową lokalizację.
1. Wdróż kontroler danych z portalu.

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Tworzenie rozszerzenia usługi danych z włączonym usługą Azure Arc

Użyj interfejsu wiersza polecenia k8s-Extension, aby utworzyć rozszerzenie usług danych.

### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ustaw następujące zmienne środowiskowe, które zostaną następnie użyte w następnym kroku.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>Tworzenie rozszerzenia Arc Data Services

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

> [!NOTE]
> Instalacja rozszerzenia Arc Data Services może potrwać kilka minut.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Sprawdź, czy utworzono rozszerzenie usługi Arc Data Services

Można sprawdzić, czy rozszerzenie usługi danych z włączonymi Łukiemmi zostało utworzone w portalu lub przez bezpośrednie połączenie z klastrem Kubernetes z włączonym łukiem. 

#### <a name="azure-portal"></a>Azure Portal
1. Zaloguj się do Azure Portal i przejdź do grupy zasobów, w której znajduje się zasób połączonego klastra Kubernetes.
1. Wybierz klaster Kubernetes z włączonym łukiem (typ = "Kubernetes-Azure ARC"), w którym rozszerzenie zostało wdrożone.
1. W obszarze nawigacji po lewej stronie w obszarze **Ustawienia** wybierz pozycję rozszerzenia (wersja zapoznawcza).
1. Powinno zostać wyświetlone rozszerzenie, które zostało właśnie utworzone wcześniej w stanie "zainstalowane".

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Pulpit nawigacyjny rozszerzeń":::

#### <a name="kubectl-cli"></a>Interfejs wiersza polecenia polecenia kubectl

1. Połącz się z klastrem Kubernetes za pośrednictwem okna terminalu.
1. Uruchom poniższe polecenie i upewnij się, że została utworzona powyższa przestrzeń nazw (1), a (2) `bootstrapper` pod stanem "uruchomiona" przed przejściem do następnego kroku.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

Na przykład następujące polecenie pobiera elementy podst z `arc` przestrzeni nazw.

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>Tworzenie niestandardowej lokalizacji za pomocą rozszerzenia niestandardowej lokalizacji interfejsu wiersza polecenia

Lokalizacja niestandardowa to zasób platformy Azure, który jest odpowiednikiem przestrzeni nazw w klastrze Kubernetes.  Lokalizacje niestandardowe są używane jako obiekty docelowe do wdrażania zasobów na platformie Azure lub z niej. Dowiedz się więcej o lokalizacjach niestandardowych w [niestandardowych lokalizacjach w dokumentacji Kubernetes z włączoną funkcją Azure Arc](../kubernetes/conceptual-custom-locations.md).

### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus2euap
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>Sprawdź, czy utworzono niestandardową lokalizację

W terminalu uruchom poniższe polecenie, aby wyświetlić listę lokalizacji niestandardowych, a następnie sprawdź, czy **stan aprowizacji** powiódł się:

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Utwórz kontroler danych usługi Azure Arc

Po utworzeniu rozszerzenia i lokalizacji niestandardowej podejmij Azure Portal, aby wdrożyć kontroler danych usługi Azure Arc.

1. Zaloguj się do witryny Azure Portal.
1. Wyszukaj ciąg "Azure Arc Data Controller" w portalu Azure Marketplace i zainicjuj tworzenie przepływu.
1. W sekcji **wymagania wstępne** upewnij się, że jest zaznaczony klaster Kubernetes (tryb bezpośredni) z funkcją Azure Arc i przejdź do następnego kroku.
1. W sekcji **szczegóły kontrolera danych** wybierz subskrypcję i grupę zasobów.
1. Wprowadź nazwę kontrolera danych.
1. Wybierz profil konfiguracji oparty na dostawcy dystrybucji Kubernetes, który jest wdrażany.
1. Wybierz niestandardową lokalizację utworzoną w poprzednim kroku.
1. Podaj szczegóły dotyczące logowania i hasła administratora kontrolera danych.
1. Podaj szczegóły dotyczące ClientId, TenantId i klucz tajny klienta dla jednostki usługi, która będzie używana do tworzenia obiektów platformy Azure. Aby uzyskać szczegółowe instrukcje dotyczące tworzenia konta głównego usługi i ról, które wymagają przyznania konta, zobacz temat [przekazywanie metryk](upload-metrics-and-logs-to-azure-monitor.md) .
1. Kliknij przycisk **dalej**, przejrzyj stronę podsumowania, aby uzyskać szczegółowe informacje, a następnie kliknij pozycję **Utwórz**.

## <a name="monitor-the-creation"></a>Monitorowanie tworzenia

Po pomyślnym wdrożeniu Azure Portal stanu wdrożenia można sprawdzić stan wdrożenia kontrolera danych Arc w klastrze w następujący sposób:

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>Następne kroki

[Tworzenie grupy serwerów PostgreSQL w warstwie Hiperskala z obsługą usługi Azure Arc](create-postgresql-hyperscale-server-group.md)

[Tworzenie wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc](create-sql-managed-instance.md)
