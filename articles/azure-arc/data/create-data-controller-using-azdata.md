---
title: Tworzenie kontrolera danych przy użyciu interfejsu wiersza polecenia platformy Azure (azdata)
description: Utwórz kontroler danych usługi Azure Arc w typowym wielowęzłowym klastrze Kubernetes, który został już utworzony przy użyciu interfejsu wiersza polecenia platformy Azure (azdata).
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a405a7a32b1f54732589fb14e34ff52db4992028
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761689"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Tworzenie kontrolera danych usługi Azure ARC przy użyciu interfejsu wiersza polecenia platformy Azure (azdata)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się z tematem [Tworzenie kontrolera danych usługi Azure ARC,](create-data-controller.md) Aby uzyskać informacje na temat przeglądu.

Aby utworzyć kontroler danych usługi Azure ARC przy użyciu interfejsu wiersza polecenia platformy Azure, należy zainstalować interfejs wiersza polecenia platformy Azure.

   [Instalowanie interfejsu wiersza polecenia platformy Azure](install-client-tools.md)

Niezależnie od wybranej platformy docelowej, należy ustawić następujące zmienne środowiskowe przed utworzeniem dla użytkownika administratora kontrolera danych. Te poświadczenia można podać innym osobom, które muszą mieć dostęp administratora do kontrolera danych, zgodnie z potrzebami.

**AZDATA_USERNAME** — wybrana nazwa użytkownika administratora kontrolera danych. Przykład: `arcadmin`

**AZDATA_PASSWORD** — wybrane hasło dla użytkownika administratora kontrolera danych. Hasło musi mieć długość co najmniej ośmiu znaków i zawierać znaki z trzech z następujących czterech zestawów: wielkie litery, małe litery, cyfry i symbole.

### <a name="linux-or-macos"></a>Linux lub macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Musisz nawiązać połączenie i uwierzytelnić się w klastrze Kubernetes i wybrać istniejący kontekst Kubernetes przed rozpoczęciem tworzenia kontrolera danych usługi Azure Arc. Sposób łączenia się z klastrem lub usługą Kubernetes jest różny. Zapoznaj się z dokumentacją dotyczącą dystrybucji lub usługi Kubernetes, która jest używana do nawiązywania połączenia z serwerem interfejsu API Kubernetes.

Możesz sprawdzić, czy masz bieżące połączenie Kubernetes i potwierdzić bieżący kontekst przy użyciu następujących poleceń.

```console
kubectl get namespace

kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Utwórz kontroler danych usługi Azure Arc

> [!NOTE]
> W poniższych przykładach można użyć innej wartości `--namespace` parametru polecenia azdata Arc DC Create, ale należy użyć tej nazwy przestrzeni nazw dla `--namespace parameter` wszystkich innych poleceń poniżej.

Postępuj zgodnie z odpowiednią sekcją w zależności od platformy docelowej, aby skonfigurować tworzenie.

[Tworzenie w usłudze Azure Kubernetes Service (AKS)](#create-on-azure-kubernetes-service-aks)

[Tworzenie w aparacie AKS na Azure Stack Hub](#create-on-aks-engine-on-azure-stack-hub)

[Tworzenie na AKS na Azure Stack HCL](#create-on-aks-on-azure-stack-hci)

[Tworzenie na platformie Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)

[Utwórz w systemie Red Hat OpenShift kontener platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)

[Utwórz na serwerze Kubernetesm Open Source, nadrzędnym (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)

[Tworzenie w usłudze AWS Elastic Kubernetes Service (EKS)](#create-on-aws-elastic-kubernetes-service-eks)

[Utwórz w usłudze Google Cloud Kubernetes Engine (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Tworzenie w usłudze Azure Kubernetes Service (AKS)

Domyślnie profil wdrażania AKS używa `managed-premium` klasy Storage. `managed-premium`Klasa Storage będzie działała tylko w przypadku maszyn wirtualnych, które zostały wdrożone przy użyciu obrazów maszyn wirtualnych z dyskami w warstwie Premium.

Jeśli zamierzasz używać `managed-premium` jako klasy magazynu, możesz uruchomić następujące polecenie, aby utworzyć kontroler danych. Zastąp symbole zastępcze w poleceniu nazwą grupy zasobów, IDENTYFIKATORem subskrypcji i lokalizacją platformy Azure.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Jeśli nie masz pewności, jaka Klasa magazynu ma być używana, należy użyć `default` klasy magazynu, która jest obsługiwana niezależnie od używanego typu maszyny wirtualnej. Nie zapewni to najszybszej wydajności.

Jeśli chcesz użyć `default` klasy Storage, możesz uruchomić następujące polecenie:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Po uruchomieniu polecenia Kontynuuj do [monitorowania stanu tworzenia](#monitoring-the-creation-status).

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Tworzenie w aparacie AKS na Azure Stack Hub

Domyślnie profil wdrożenia używa `managed-premium` klasy Storage. `managed-premium`Klasa magazynu będzie działała tylko w przypadku maszyn wirtualnych, które zostały wdrożone przy użyciu obrazów maszyn wirtualnych z dyskami Premium w centrum Azure Stack.

Można uruchomić następujące polecenie, aby utworzyć kontroler danych przy użyciu klasy magazynu Managed-Premium:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Jeśli nie masz pewności, jaka Klasa magazynu ma być używana, należy użyć `default` klasy magazynu, która jest obsługiwana niezależnie od używanego typu maszyny wirtualnej. W centrum Azure Stack dyski w warstwie Premium i dyski standardowe są obsługiwane przez tę samą infrastrukturę magazynu. W związku z tym oczekuje się, że zapewniają one taką samą ogólną wydajność, ale z różnymi limitami IOPS.

Jeśli chcesz użyć `default` klasy Storage, możesz uruchomić to polecenie.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Po uruchomieniu polecenia Kontynuuj do [monitorowania stanu tworzenia](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Tworzenie na AKS na Azure Stack HCL

Domyślnie profil wdrożenia używa klasy magazynu o nazwie `default` i typu usługi `LoadBalancer` .

Można uruchomić następujące polecenie, aby utworzyć kontroler danych przy użyciu `default` klasy magazynu i typu usługi `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Po uruchomieniu polecenia Kontynuuj do [monitorowania stanu tworzenia](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Tworzenie na platformie Azure Red Hat OpenShift (ARO)

Aby utworzyć kontroler danych na platformie Azure Red Hat OpenShift, należy wykonać następujące polecenia w odniesieniu do klastra, aby osłabić ograniczenia zabezpieczeń. Jest to tymczasowy wymóg, który zostanie usunięty w przyszłości.
> [!NOTE]
>   Użyj tej samej przestrzeni nazw tutaj i `azdata arc dc create` poniższego polecenia. Przykład to `arc` .

Najpierw pobierz niestandardowe ograniczenie kontekstu zabezpieczeń (SCC) z usługi [GitHub](https://github.com/microsoft/azure_arc/tree/master/arc_data_services/deploy/yaml) i zastosuj je do klastra.

Można uruchomić następujące polecenie, aby utworzyć kontroler danych:
> [!NOTE]
>   Użyj tej samej przestrzeni nazw w tym miejscu i w `oc adm policy add-scc-to-user` powyższych poleceniach. Przykład to `arc` .

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Po uruchomieniu polecenia Kontynuuj do [monitorowania stanu tworzenia](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Utwórz w systemie Red Hat OpenShift kontener platform (OCP)


> [!NOTE]
> Jeśli korzystasz z platformy OpenShift w systemie Red Hat na platformie Azure, zalecamy użycie najnowszej dostępnej wersji.

Aby utworzyć kontroler danych na platformie Red Hat OpenShift Container platform, należy wykonać następujące polecenia w odniesieniu do klastra, aby osłabić ograniczenia zabezpieczeń. Jest to tymczasowy wymóg, który zostanie usunięty w przyszłości.
> [!NOTE]
>   Użyj tej samej przestrzeni nazw tutaj i `azdata arc dc create` poniższego polecenia. Przykład to `arc` .

```console
oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
```

Należy również określić klasę magazynu, która ma być używana, uruchamiając następujące polecenie.

```console
kubectl get storageclass
```

Najpierw Zacznij od utworzenia nowego niestandardowego pliku profilu wdrożenia na podstawie profilu wdrożenia Azure-Arc-OpenShift, uruchamiając następujące polecenie. To polecenie spowoduje utworzenie katalogu `custom` w bieżącym katalogu roboczym i pliku niestandardowego profilu wdrożenia `control.json` w tym katalogu.

Użyj profilu `azure-arc-openshift` dla platformy kontenerów OpenShift.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```
Użyj profilu `azure-arc-azure-openshift` dla usługi Azure RedHat Otwórz Shift.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

Teraz ustaw żądaną klasę magazynu przez zastąpienie `<storageclassname>` w poniższym poleceniu nazwą klasy magazynu, która ma zostać użyta, co zostało określone przez uruchomienie `kubectl get storageclass` powyższego polecenia.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Domyślnie profil wdrożenia usługi Azure-Arc-OpenShift jest stosowany `NodePort` jako typ usługi. Jeśli używasz klastra OpenShift, który jest zintegrowany z usługą równoważenia obciążenia, możesz zmienić konfigurację tak, aby używała tego typu usługi przy użyciu następującego polecenia:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

W przypadku korzystania z OpenShift może być konieczne uruchomienie z domyślnymi zasadami zabezpieczeń w programie OpenShift lub zablokowanie środowiska więcej niż typowe. Opcjonalnie można wyłączyć niektóre funkcje, aby zminimalizować uprawnienia wymagane w czasie wdrażania i w czasie wykonywania, uruchamiając następujące polecenia.

To polecenie powoduje wyłączenie kolekcji metryk dotyczących zasobników. Jeśli ta funkcja zostanie wyłączona, nie będzie można zobaczyć metryk dotyczących Grafana na pulpitach nawigacyjnych. Wartość domyślna to „true”.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

To polecenie wyłącza kolekcje metryk o węzłach. Jeśli ta funkcja zostanie wyłączona, nie będzie można zobaczyć metryk dotyczących węzłów na pulpitach nawigacyjnych Grafana. Wartość domyślna to „true”.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

To polecenie umożliwia wyłączenie zrzutów pamięci na potrzeby rozwiązywania problemów.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

Teraz można przystąpić do tworzenia kontrolera danych przy użyciu poniższego polecenia.
> [!NOTE]
>   Użyj tej samej przestrzeni nazw w tym miejscu i w `oc adm policy add-scc-to-user` powyższych poleceniach. Przykład to `arc` .

> [!NOTE]
>   `--path`Parametr powinien wskazywać _katalog_ zawierający control.jsw pliku, a nie control.jsw samym pliku.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Po uruchomieniu polecenia Kontynuuj do [monitorowania stanu tworzenia](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Utwórz na serwerze Kubernetesm Open Source, nadrzędnym (kubeadm)

Domyślnie profil wdrażania kubeadm używa klasy magazynu o nazwie `local-storage` i typu usługi `NodePort` . Jeśli jest to możliwe, można pominąć poniższe instrukcje, które ustawiają żądaną klasę magazynu i typ usługi, i natychmiast uruchamiają `azdata arc dc create` poniższe polecenie.

Jeśli chcesz dostosować profil wdrożenia, aby określić konkretną klasę magazynu i/lub typ usługi, Zacznij od utworzenia nowego pliku niestandardowego profilu wdrożenia na podstawie profilu wdrażania kubeadm, uruchamiając następujące polecenie. To polecenie spowoduje utworzenie katalogu `custom` w bieżącym katalogu roboczym i pliku niestandardowego profilu wdrożenia `control.json` w tym katalogu.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

Dostępne klasy magazynu można wyszukać, uruchamiając następujące polecenie.

```console
kubectl get storageclass
```

Teraz ustaw żądaną klasę magazynu przez zastąpienie `<storageclassname>` w poniższym poleceniu nazwą klasy magazynu, która ma zostać użyta, co zostało określone przez uruchomienie `kubectl get storageclass` powyższego polecenia.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Domyślnie profil wdrażania kubeadm jest stosowany `NodePort` jako typ usługi. Jeśli używasz klastra Kubernetes, który jest zintegrowany z usługą równoważenia obciążenia, możesz zmienić konfigurację przy użyciu następującego polecenia.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

Teraz można przystąpić do tworzenia kontrolera danych przy użyciu poniższego polecenia.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Po uruchomieniu polecenia Kontynuuj do [monitorowania stanu tworzenia](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Tworzenie w usłudze AWS Elastic Kubernetes Service (EKS)

Domyślnie Klasa magazynu EKS jest `gp2` i typem usługi `LoadBalancer` .

Uruchom następujące polecenie, aby utworzyć kontroler danych przy użyciu podanego profilu wdrożenia EKS.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Po uruchomieniu polecenia Kontynuuj do [monitorowania stanu tworzenia](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Utwórz w usłudze Google Cloud Kubernetes Engine (GKE)

Domyślnie Klasa magazynu GKE jest `standard` i typem usługi `LoadBalancer` .

Uruchom następujące polecenie, aby utworzyć kontroler danych przy użyciu podanego profilu wdrożenia GKE.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Po uruchomieniu polecenia Kontynuuj do [monitorowania stanu tworzenia](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>Monitorowanie stanu tworzenia

Tworzenie kontrolera potrwa kilka minut. Postęp można monitorować w innym oknie terminalu przy użyciu następujących poleceń:

> [!NOTE]
>  W przykładowych poleceniach przyjęto założenie, że utworzono kontroler danych i Kubernetes przestrzeń nazw o nazwie `arc` . Jeśli użyto innej nazwy obszaru nazw/kontrolera danych, możesz zamienić ją `arc` na nazwę.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Możesz również sprawdzić stan tworzenia dowolnego określonego obszaru pod, uruchamiając polecenie podobne do poniższego. Jest to szczególnie przydatne w przypadku rozwiązywania problemów.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Rozwiązywanie problemów z tworzeniem

Jeśli wystąpią problemy z tworzeniem, zapoznaj się z [przewodnikiem rozwiązywania problemów](troubleshoot-guide.md).
