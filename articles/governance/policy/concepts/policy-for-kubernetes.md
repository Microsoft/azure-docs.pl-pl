---
title: Informacje Azure Policy dla kubernetes
description: Dowiedz się, Azure Policy korzysta z usług Rego i Open Policy Agent do zarządzania klastrami z systemem Kubernetes na platformie Azure lub lokalnie.
ms.date: 03/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ca33c3a937b0a155928f20469830388a95a08e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107506027"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Opis usługi Azure Policy dla klastrów Kubernetes

Azure Policy program [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) w wersji 3, element _webhook_ kontrolera danych dla programu [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), aby stosować w klastrach w sposób scentralizowany i spójny wymuszanie i zabezpieczenia na dużą skalę. Azure Policy umożliwia zarządzanie stanem zgodności klastrów Kubernetes i zgłaszanie ich z jednego miejsca. Dodatek wprowadza następujące funkcje:

- Sprawdza, Azure Policy usługa sprawdza, czy w usłudze są przypisania zasad do klastra.
- Wdraża definicje zasad w klastrze jako [szablon ograniczeń i](https://github.com/open-policy-agent/gatekeeper#constraint-templates) [niestandardowe](https://github.com/open-policy-agent/gatekeeper#constraints) zasoby ograniczenia.
- Raporty dotyczące inspekcji i zgodności z powrotem Azure Policy usługi.

Azure Policy dla usługi Kubernetes obsługuje następujące środowiska klastra:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Platforma Kubernetes z włączoną usługą Azure Arc](../../../azure-arc/kubernetes/overview.md)
- [Aparat usługi AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Dodatki dla aparatu AKS i usługi Kubernetes z obsługą usługi Arc są dostępne w wersji **zapoznawczej.** Azure Policy dla kubernetes obsługuje tylko pule węzłów systemu Linux i wbudowane definicje zasad. Wbudowane definicje zasad znajdują się w **kategorii Kubernetes.** Ograniczone definicje zasad w wersji zapoznawczej z **efektami EnforceETAConstraint** i **EnforceRegoPolicy** oraz powiązanymi kategoriami **usługi Kubernetes Service** są _przestarzałe._ Zamiast tego użyj efektów audit _i_ _deny w_ trybie dostawcy `Microsoft.Kubernetes.Data` zasobów.

## <a name="overview"></a>Omówienie

Aby włączyć i Azure Policy z klastrem Kubernetes, należy podjąć następujące czynności:

1. Skonfiguruj klaster Kubernetes i zainstaluj dodatek:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Platforma Kubernetes z włączoną usługą Azure Arc](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [Aparat usługi AKS](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Aby uzyskać informacje o typowych problemach z instalacją, zobacz [Rozwiązywanie Azure Policy dodatku](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors).

1. [Understand the Azure Policy language for Kubernetes](#policy-language)

1. [Przypisywanie wbudowanej definicji do klastra Kubernetes](#assign-a-built-in-policy-definition)

1. [Oczekiwanie na walidację](#policy-evaluation)

## <a name="limitations"></a>Ograniczenia

Następujące ogólne ograniczenia dotyczą Azure Policy dla klastrów Kubernetes:

- Azure Policy dodatek dla kubernetes jest obsługiwany na platformie Kubernetes w wersji **1.14** lub wyższej.
- Azure Policy dodatek dla rozwiązania Kubernetes można wdrożyć tylko w pulach węzłów systemu Linux
- Obsługiwane są tylko wbudowane definicje zasad
- Maksymalna liczba niezgodnych rekordów na zasady na klaster: **500**
- Maksymalna liczba niezgodnych rekordów na subskrypcję: **1 milion**
- Instalacje strażnika poza Azure Policy nie są obsługiwane. Odinstaluj wszystkie składniki zainstalowane przez poprzednią instalację programu Gatekeeper przed włączeniem Azure Policy dodatku.
- [Przyczyny niezgodności są niedostępne](../how-to/determine-non-compliance.md#compliance-reasons) w trybie `Microsoft.Kubernetes.Data` 
   [dostawcy zasobów.](./definition-structure.md#resource-provider-modes) Użyj [szczegółów składnika](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).
- [Wyjątki nie](./exemption-structure.md) są obsługiwane w [trybach dostawcy zasobów.](./definition-structure.md#resource-provider-modes)

Następujące ograniczenia dotyczą tylko Azure Policy dodatku dla AKS:

- [Nie można włączyć](../../../aks/use-pod-security-policies.md) zasad zabezpieczeń zasobnika usługi AKS Azure Policy dodatku dla usługi AKS. Aby uzyskać więcej informacji, zobacz [AKS pod security limitation (Ograniczenie zabezpieczeń zasobnika AKS).](../../../aks/use-azure-policy.md)
- Przestrzenie nazw automatycznie Azure Policy dodatek do oceny: _kube-system,_ _gatekeeper-system_ i _aks-periscope_.

## <a name="recommendations"></a>Zalecenia

Poniżej przedstawiono ogólne zalecenia dotyczące używania Azure Policy dodatek:

- Dodatek Azure Policy wymaga uruchomienia trzech składników programu Gatekeeper: 1 zasobnik inspekcji i 2 repliki zasobników elementów webhook. Te składniki zużywają więcej zasobów w przypadku zwiększenia liczby zasobów kubernetes i przypisań zasad w klastrze, co wymaga operacji inspekcji i wymuszania.

  - W przypadku mniej niż 500 zasobników w jednym klastrze z maksymalnie 20 ograniczeniami: 2 procesory wirtualne i 350 MB pamięci na składnik.
  - W przypadku ponad 500 zasobników w jednym klastrze z maksymalnie 40 ograniczeniami: 3 procesory wirtualne i 600 MB pamięci na składnik.

- Zasobniki systemu Windows [nie obsługują kontekstów zabezpieczeń](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  W związku z tym niektóre definicje Azure Policy, takie jak nie zezwalanie na uprawnienia administratora, nie mogą być eskalowane w zasobnikach systemu Windows i dotyczą tylko zasobników systemu Linux.

Poniższe zalecenie dotyczy tylko dodatku Azure Policy AKS:

- Użyj puli węzłów systemu z `CriticalAddonsOnly` taintem, aby zaplanować zasobniki programu Gatekeeper. Aby uzyskać więcej informacji, zobacz [Using system node pools (Używanie pul węzłów systemowych).](../../../aks/use-system-pools.md#system-and-user-node-pools)
- Zabezpieczanie ruchu wychodzącego z klastrów usługi AKS. Aby uzyskać więcej informacji, zobacz [Control egress traffic for cluster nodes (Sterowanie ruchem wychodzącym dla węzłów klastra).](../../../aks/limit-egress-traffic.md)
- Jeśli klaster został `aad-pod-identity` włączony, zasobniki Tożsamość zarządzana na węźle (NMI) modyfikują tabelę iptable węzłów w celu przechwycenia wywołań punktu końcowego metadanych wystąpienia platformy Azure. Ta konfiguracja oznacza, że każde żądanie do punktu końcowego metadanych jest przechwytywane przez usługę NMI, nawet jeśli zasobnik nie używa elementu `aad-pod-identity` . Definicję CRD elementu AzurePodIdentityException można skonfigurować w taki sposób, aby wszelkie żądania do punktu końcowego metadanych pochodzące z zasobnika, który pasuje do etykiet zdefiniowanych w crd, były proxied bez przetwarzania w usłudze `aad-pod-identity` NMI. Zasobniki systemowe z etykietą w przestrzeni nazw kube-system powinny zostać wykluczone przez skonfigurowanie `kubernetes.azure.com/managedby: aks`  `aad-pod-identity` crd elementu AzurePodIdentityException. Aby uzyskać więcej informacji, [zobacz Disable aad-pod-identity for aad-pod-identity for a specific pod or application (Wyłączanie tożsamości zasobnika aad-pod-identity dla określonego zasobnika lub aplikacji).](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)
  Aby skonfigurować wyjątek, zainstaluj [wyjątek mikrofonu YAML.](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)

## <a name="install-azure-policy-add-on-for-aks"></a>Instalowanie Azure Policy add-on dla AKS

Przed zainstalowaniem Azure Policy lub włączeniem dowolnej funkcji usługi Subskrypcja musi włączyć dostawców zasobów **Microsoft.PolicyInsights.**

1. Musisz mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Zarejestruj dostawców zasobów i funkcje w wersji zapoznawczej.

   - Azure Portal:

     Zarejestruj dostawców **zasobów Microsoft.PolicyInsights.** Aby uzyskać instrukcje, zobacz [Dostawcy zasobów i typy](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

   - Interfejs wiersza polecenia platformy Azure:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Jeśli zainstalowano ograniczone definicje zasad w wersji zapoznawczej, usuń dodatek z przyciskiem **Wyłącz** w klastrze usługi AKS na **stronie** Zasady.

1. Klaster AKS musi być w wersji _1.14_ lub wyższej. Użyj następującego skryptu, aby zweryfikować wersję klastra usługi AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Zainstaluj wersję _2.12.0 lub_ nowszą interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

Po ukończeniu powyższych kroków wymagań wstępnych zainstaluj Azure Policy w klastrze usługi AKS, którym chcesz zarządzać.

- Azure Portal

  1. Uruchom usługę AKS w chmurze, Azure Portal pozycję **Wszystkie** usługi, a następnie wyszukując i wybierając **pozycję Usługi Kubernetes.**

  1. Wybierz jeden z klastrów usługi AKS.

  1. Wybierz **pozycję** Zasady po lewej stronie strony usługi Kubernetes.

  1. Na stronie głównej wybierz przycisk Włącz **dodatek.**

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Jeśli przycisk **Wyłącz dodatek** jest włączony i zostanie wyświetlony komunikat z ostrzeżeniem o migracji w wersji 2, dodatek w wersji 1 zostanie zainstalowany i musi zostać usunięty przed przypisaniem definicji zasad w wersji 2. _Przestarzały dodatek_ w wersji 1 zostanie automatycznie zastąpiony przez dodatek w wersji 2, począwszy od 24 sierpnia.
     > 2020. Następnie należy przypisać nowe wersje definicji zasad w wersji 2. Aby uaktualnić teraz, wykonaj następujące kroki:
     >
     > 1. Sprawdź, czy klaster usługi AKS ma zainstalowany  dodatek w wersji 1, odwiedzając stronę Zasady w klastrze usługi AKS i "Bieżący klaster używa Azure Policy dodatku w wersji 1..." Komunikat.
     > 1. [Usuń dodatek](#remove-the-add-on-from-aks).
     > 1. Wybierz przycisk **Włącz dodatek,** aby zainstalować wersję 2 dodatku.
     > 1. [Przypisywanie wersji 2 wbudowanych definicji zasad w wersji 1](#assign-a-built-in-policy-definition)

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Aby sprawdzić, czy instalacja dodatku powiodła się i czy zasobniki _azure-policy_ i _gatekeeper_ są uruchomione, uruchom następujące polecenie:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Na koniec sprawdź, czy jest zainstalowany najnowszy dodatek, uruchamiając to polecenie interfejsu wiersza polecenia platformy Azure, zastępując pozycję nazwą grupy zasobów, a nazwą klastra `<rg>` `<cluster-name>` usługi AKS: `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>` . Wynik powinien wyglądać podobnie do następujących danych wyjściowych, **a plik config.version** powinien wyglądać następująco: `v2`

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Instalowanie Azure Policy dla Azure Arc Kubernetes (wersja zapoznawcza)

Przed zainstalowaniem dodatku usługi Azure Policy lub włączeniem dowolnej funkcji usługi subskrypcja musi włączyć dostawcę **zasobów Microsoft.PolicyInsights** i utworzyć przypisanie roli dla jednostki usługi klastra.

1. Musisz mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Aby włączyć dostawcę zasobów, wykonaj kroki opisane w [tesłudze: Dostawcy zasobów](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) i typy albo uruchom interfejs wiersza polecenia platformy Azure lub Azure PowerShell polecenie:

   - Interfejs wiersza polecenia platformy Azure

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Klaster Kubernetes musi być w wersji _1.14_ lub wyższej.

1. Zainstaluj [program Helm 3.](https://v3.helm.sh/docs/intro/install/)

1. Klaster Kubernetes jest włączony dla Azure Arc. Aby uzyskać więcej informacji, zobacz [dołączanie klastra Kubernetes do Azure Arc](../../../azure-arc/kubernetes/quickstart-connect-cluster.md).

1. Mieć w pełni kwalifikowany identyfikator zasobu platformy Azure dla Azure Arc Kubernetes z włączoną obsługą.

1. Otwórz porty dla dodatku. Dodatek Azure Policy używa tych domen i portów do pobierania definicji zasad i przypisań oraz zgodności raportów klastra z powrotem do Azure Policy.

   |Domena |Port |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Przypisz przypisanie roli "Funkcja zapisujący dane usługi Policy Insights (wersja zapoznawcza)" Azure Arc klastra Kubernetes z włączoną obsługą usługi. Zastąp identyfikatorem subskrypcji, nazwą Azure Arc klastra Kubernetes z włączoną obsługą usługi, a nazwą klastra `<subscriptionId>` `<rg>` Kubernetes z włączoną obsługą `<clusterName>` usługi Azure Arc. Śledź zwrócone wartości dla parametrów _appId,_ _password_ i _tenant,_ aby wykonać kroki instalacji.

   - Interfejs wiersza polecenia platformy Azure

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Przykładowe dane wyjściowe powyższych poleceń:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Po ukończeniu powyższych kroków wymagań wstępnych zainstaluj Azure Policy w klastrze Kubernetes z Azure Arc obsługą funkcji:

1. Dodaj Azure Policy dodatku do helm:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Zainstaluj Azure Policy przy użyciu pakietu Helm:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   Aby uzyskać więcej informacji na temat instalacji dodatku Helm Chart, zobacz definicję dodatku [Helm chart](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) Azure Policy w witrynie GitHub.

Aby sprawdzić, czy instalacja dodatku powiodła się i czy zasobniki _azure-policy_ i _gatekeeper_ są uruchomione, uruchom następujące polecenie:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Instalowanie Azure Policy dla aparatu AKS (wersja zapoznawcza)

Przed zainstalowaniem dodatku usługi Azure Policy lub włączeniem dowolnej funkcji usługi subskrypcja musi włączyć dostawcę zasobów **Microsoft.PolicyInsights** i utworzyć przypisanie roli dla jednostki usługi klastra.

1. Musisz zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.62 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Aby włączyć dostawcę zasobów, wykonaj kroki opisane w tesłudze [Resource providers and types](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) (Dostawcy zasobów i typy) lub uruchom interfejs wiersza polecenia platformy Azure lub Azure PowerShell polecenia:

   - Interfejs wiersza polecenia platformy Azure

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Utwórz przypisanie roli dla jednostki usługi klastra.

   - Jeśli nie znasz identyfikatora aplikacji jednostki usługi klastra, odszukaj go za pomocą następującego polecenia.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Przypisz przypisanie roli "Autor danych usługi Policy Insights (wersja zapoznawcza)" do identyfikatora aplikacji jednostki usługi klastra (wartość _aadClientID_ z poprzedniego kroku) przy użyciu interfejsu wiersza polecenia platformy Azure. Zastąp identyfikatorem subskrypcji, a grupę zasobów, w którym znajduje się samodzielnie zarządzany klaster `<subscriptionId>` `<aks engine cluster resource group>` Kubernetes aparatu AKS.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Po ukończeniu powyższych kroków wymagań wstępnych zainstaluj Azure Policy dodatek. Instalacja może być podczas cyklu tworzenia lub aktualizowania aparatu usługi AKS lub jako niezależna akcja w istniejącym klastrze.

- Instalowanie podczas cyklu tworzenia lub aktualizacji

  Aby włączyć dodatek Azure Policy podczas tworzenia nowego klastra samodzielnego lub w ramach aktualizacji istniejącego [](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) klastra, dołącz definicję klastra właściwości dodatków dla aparatu usługi AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Aby uzyskać więcej informacji na ten temat, zobacz przewodnik zewnętrzny definicji [klastra aparatu usługi AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Instalowanie w istniejącym klastrze przy użyciu pakietu Helm

  Aby przygotować klaster i zainstalować dodatek, należy wykonać następujące czynności:

  1. Zainstaluj [program Helm 3.](https://v3.helm.sh/docs/intro/install/)

  1. Dodaj Azure Policy do helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Aby uzyskać więcej informacji, zobacz [Helm Chart — Przewodnik Szybki start.](https://helm.sh/docs/using_helm/#quickstart-guide)

  1. Zainstaluj dodatek za pomocą pakietu Helm. Zastąp identyfikatorem subskrypcji, a grupę zasobów, w którym znajduje `<subscriptionId>` `<aks engine cluster resource group>` się samodzielnie zarządzany klaster Kubernetes aparatu AKS.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Aby uzyskać więcej informacji na temat instalacji dodatku Helm Chart, zobacz definicję dodatku [Helm chart](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) Azure Policy w witrynie GitHub.

     > [!NOTE]
     > Ze względu na relację między Azure Policy i identyfikatorem grupy zasobów usługa Azure Policy obsługuje tylko jeden klaster aparatu usługi AKS dla każdej grupy zasobów.

Aby sprawdzić, czy instalacja dodatku powiodła się i czy zasobniki _azure-policy_ i _gatekeeper_ są uruchomione, uruchom następujące polecenie:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Język zasad

Struktura Azure Policy do zarządzania platformą Kubernetes jest zgodna z istniejącymi definicjami zasad. W [trybie dostawcy zasobów](./definition-structure.md#resource-provider-modes) programu efekty inspekcji i odmowy są używane do zarządzania `Microsoft.Kubernetes.Data` klastrami Kubernetes. [](./effects.md#audit) [](./effects.md#deny) _Inspekcja_ _i odmowa_ muszą zawierać **właściwości** szczegółów specyficzne dla pracy z [usługami OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) i Gatekeeper v3.

W ramach właściwości _details.constraintTemplate_ i _details.constraint_ w definicji zasad program Azure Policy przekazuje do dodatku wartości URI tych właściwości [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD). Rego to język, który obsługuje opa i program Gatekeeper w celu zweryfikowania żądania do klastra Kubernetes. Dzięki obsługi istniejącego standardu zarządzania na platformie Kubernetes usługa Azure Policy umożliwia ponowne użycie istniejących reguł i sparowanie ich z usługą Azure Policy w celu zapewnienia ujednoliconego raportowania zgodności w chmurze. Aby uzyskać więcej informacji, [zobacz Co to jest Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Przypisywanie wbudowanej definicji zasad

Aby przypisać definicję zasad do klastra Kubernetes, musisz mieć przypisane odpowiednie operacje przypisywania zasad kontroli dostępu na podstawie ról (RBAC) platformy Azure. Te operacje są wykonywane we wbudowanych rolach **platformy** Azure Współautor **zasad** zasobów i Właściciel. Aby dowiedzieć się więcej, zobacz [Uprawnienia RBAC platformy Azure w Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

Znajdź wbudowane definicje zasad do zarządzania klastrem przy użyciu Azure Portal, aby wykonać następujące czynności:

1. Uruchom usługę Azure Policy w Azure Portal. Wybierz **pozycję Wszystkie usługi** w okienku po lewej stronie, a następnie wyszukaj i wybierz pozycję **Zasady.**

1. W lewym okienku strony Azure Policy wybierz pozycję **Definicje**.

1. W polu listy rozwijanej Kategoria użyj opcji **Zaznacz wszystko,** aby wyczyścić filtr, a następnie wybierz **pozycję Kubernetes**.

1. Wybierz definicję zasad, a następnie wybierz **przycisk Przypisz.**

1. Ustaw zakres **na** grupę zarządzania, subskrypcję lub grupę zasobów klastra Kubernetes, w którym będzie stosowane przypisanie zasad.

   > [!NOTE]
   > Podczas przypisywania Azure Policy dla definicji kubernetes zakres **musi** zawierać zasób klastra. W przypadku klastra aparatu usługi AKS zakres **musi** być grupą zasobów klastra.

1. Nadaj przypisaniu **zasad nazwę** i **opis,** których można użyć do jego łatwego zidentyfikowania.

1. Ustaw [wymuszanie zasad](./assignment-structure.md#enforcement-mode) na jedną z poniższych wartości.

   - **Włączone** — wymuszanie zasad w klastrze. Żądania naruszeń dotyczące naruszeń naruszeń są odmówień żądań naruszeń na kubernetes.

   - **Wyłączone** — nie wymuszaj zasad w klastrze. Żądania naruszeń dotyczące naruszeń naruszeń na stronie kubernetes nie są odmówień. Wyniki oceny zgodności są nadal dostępne. Podczas wycofywania nowych definicji zasad w uruchomionych klastrach opcja Wyłączone jest przydatna do testowania definicji _zasad,_ ponieważ żądania wejścia z naruszeniami nie są odmowami.

1. Wybierz opcję **Dalej**.

1. Ustawianie **wartości parametrów**

   - Aby wykluczyć przestrzenie nazw kubernetes z oceny zasad, określ listę przestrzeni nazw w parametrze **Wykluczenia przestrzeni nazw**. Zalecane jest wykluczenie: _kube-system,_ _gatekeeper-system i_ _azure-arc._

1. Wybierz pozycję **Przejrzyj i utwórz**.

Możesz też skorzystać z przewodnika Szybki start Przypisywanie zasad — [Portal,](../assign-policy-portal.md) aby znaleźć i przypisać zasady kubernetes. Wyszukaj definicję zasad platformy Kubernetes zamiast przykładowej "audit vms".

> [!IMPORTANT]
> Wbudowane definicje zasad są dostępne dla klastrów Kubernetes w kategorii **Kubernetes**. Aby uzyskać listę wbudowanych definicji zasad, zobacz [Przykłady dla środowiska Kubernetes.](../samples/built-in-policies.md#kubernetes)

## <a name="policy-evaluation"></a>Ocena zasad

Dodatek jest sprawdzany w usłudze Azure Policy zmian w przypisaniach zasad co 15 minut.
Podczas tego cyklu odświeżania dodatek sprawdza zmiany. Te zmiany wyzwalają tworzenie, aktualizacje lub usuwanie szablonów ograniczeń i ograniczeń.

Jeśli w klastrze Kubernetes przestrzeń nazw ma jedną z następujących etykiet, żądania wejścia z naruszeniami nie są odmowami. Wyniki oceny zgodności są nadal dostępne.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Administrator klastra może mieć uprawnienia do tworzenia i aktualizowania zasobów ograniczeń i szablonów ograniczeń instalowanych przez dodatek programu Azure Policy, ale nie są to obsługiwane scenariusze, ponieważ aktualizacje ręczne są zastępowane. Program Gatekeeper kontynuuje ocenę zasad, które istniały przed zainstalowaniem dodatku i przypisaniem Azure Policy definicji zasad.

Co 15 minut dodatek wywołuje pełne skanowanie klastra. Po zebraniu szczegółów pełnego skanowania i wszelkich ocen w czasie rzeczywistym przez strażnika prób zmian w klastrze dodatek [](../how-to/get-compliance-data.md) zgłasza wyniki z powrotem do programu Azure Policy w celu uwzględnienia szczegółów zgodności, takich jak dowolne Azure Policy przypisania. Podczas cyklu inspekcji są zwracane tylko wyniki aktywnych przypisań zasad. Wyniki inspekcji mogą być również widoczne jako [naruszenia](https://github.com/open-policy-agent/gatekeeper#audit) wymienione w polu stanu ograniczenia, które zakończyło się niepowodzeniem. Aby uzyskać szczegółowe informacje _na temat niezgodnych_ zasobów, zobacz [Component details for Resource Provider modes (Szczegóły składników dla trybów dostawcy zasobów).](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)

> [!NOTE]
> Każdy raport zgodności w Azure Policy dla klastrów Kubernetes zawiera wszystkie naruszenia w ciągu ostatnich 45 minut. Znacznik czasu wskazuje, kiedy wystąpiło naruszenie.

Inne zagadnienia:

- Jeśli subskrypcja klastra jest zarejestrowana w Azure Security Center, Azure Security Center kubernetes są automatycznie stosowane w klastrze.

- Po zastosowaniu zasad odmowy w klastrze z istniejącymi zasobami kubernetes wszystkie istniejące zasoby niezgodne z nowymi zasadami będą nadal działać. Gdy niezgodny zasób zostanie ponownie przeplanowany na innym węźle, program Gatekeeper blokuje tworzenie zasobów.

- Jeśli klaster ma zasady odmowy, które weryfikują zasoby, podczas tworzenia wdrożenia użytkownik nie zobaczy komunikatu o odrzuceniu. Rozważmy na przykład wdrożenie kubernetes, które zawiera zestawy replik i zasobniki. Gdy użytkownik wykonuje polecenie `kubectl describe deployment $MY_DEPLOYMENT` , nie zwraca komunikatu o odrzuceniu w ramach zdarzeń. Zwraca jednak `kubectl describe replicasets.apps $MY_DEPLOYMENT` zdarzenia skojarzone z odrzuceniem.

## <a name="logging"></a>Rejestrowanie

Zarówno kontroler/kontener platformy Kubernetes, jak i zasobniki _azure-policy_ i _gatekeeper_ przechowują dzienniki w klastrze Kubernetes. Dzienniki mogą być widoczne na **stronie Szczegółowe informacje** klastra Kubernetes. Aby uzyskać więcej informacji, zobacz [Monitor your Kubernetes cluster performance with Azure Monitor for containers (Monitorowanie](../../../azure-monitor/containers/container-insights-analyze.md)wydajności klastra Kubernetes za pomocą Azure Monitor kontenerów).

Aby wyświetlić dzienniki dodatków, użyj funkcji `kubectl` :

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Aby uzyskać więcej informacji, zobacz [Debugging Gatekeeper (Debugowanie programu Gatekeeper)](https://github.com/open-policy-agent/gatekeeper#debugging) w dokumentacji programu Gatekeeper.

## <a name="troubleshooting-the-add-on"></a>Rozwiązywanie problemów z dodatku

Aby uzyskać więcej informacji na temat rozwiązywania problemów z dodatku dla rozwiązania Kubernetes, zobacz sekcję [Kubernetes](../troubleshoot/general.md#add-on-for-kubernetes-general-errors) w artykule Azure Policy rozwiązywania problemów.

## <a name="remove-the-add-on"></a>Usuwanie dodatku

### <a name="remove-the-add-on-from-aks"></a>Usuwanie dodatku z aks

Aby usunąć Azure Policy z klastra usługi AKS, użyj interfejsu wiersza Azure Portal platformy Azure:

- Azure Portal

  1. Uruchom usługę AKS w chmurze, Azure Portal pozycję **Wszystkie usługi,** a następnie wyszukując i wybierając **pozycję Usługi Kubernetes.**

  1. Wybierz klaster usługi AKS, w którym chcesz Azure Policy dodatek.

  1. Wybierz **pozycję** Zasady po lewej stronie strony usługi Kubernetes.

  1. Na stronie głównej wybierz przycisk Wyłącz **dodatek.**

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Usuwanie dodatku z Azure Arc kubernetes

Aby usunąć dodatek Azure Policy i program Gatekeeper z klastra Kubernetes z włączonym Azure Arc, uruchom następujące polecenie helm:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>Usuwanie dodatku z aparatu AKS

Aby usunąć Azure Policy i program Gatekeeper z klastra aparatu usługi AKS, użyj metody zgodnej ze sposobem instalacji dodatku:

- W przypadku instalacji przez ustawienie **właściwości addons** w definicji klastra dla aparatu usługi AKS:

  Po zmianie właściwości **addons** dla _azure-policy_ na wartość false ponownie wdeń definicję klastra do aparatu usługi AKS:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Aby uzyskać więcej informacji, zobacz [AKS Engine - Disable Azure Policy Add-on (Aparat usługi AKS — Azure Policy dodatek .](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on)

- W przypadku zainstalowania z pakietami Helm Charts uruchom następujące polecenie helm:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dane diagnostyczne zbierane przez Azure Policy dodatek

Dodatek Azure Policy dla usługi Kubernetes zbiera ograniczone dane diagnostyczne klastra. Te dane diagnostyczne są istotnymi danymi technicznymi związanymi z oprogramowaniem i wydajnością. Jest on używany w następujący sposób:

- Bądź Azure Policy dodatku
- Zapewnij Azure Policy, niezawodność i działanie dodatku
- Ulepszanie Azure Policy dodatek — dzięki zagregowanej analizie użycia dodatku

Informacje zbierane przez dodatek nie są danymi osobowy. Obecnie zbierane są następujące szczegóły:

- Azure Policy agenta dodatku
- Typ klastra
- Region klastra
- Grupa zasobów klastra
- Identyfikator zasobu klastra
- Identyfikator subskrypcji klastra
- System operacyjny klastra (przykład: Linux)
- Miasto klastra (przykład: Seattle)
- Stan klastra lub prowincja (przykład: Waszyngton)
- Kraj lub region klastra (przykład: Stany Zjednoczone)
- Wyjątki/błędy napotkane przez Azure Policy podczas instalacji agenta podczas oceny zasad
- Liczba definicji zasad strażnika, które Azure Policy dodatek

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady pod [Azure Policy przykładami.](../samples/index.md)
- Przejrzyj [strukturę definicji usługi Azure Policy](definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, [jak programowo tworzyć zasady.](../how-to/programmatically-create.md)
- Dowiedz się, jak [uzyskać dane zgodności.](../how-to/get-compliance-data.md)
- Dowiedz się, jak [korygować niezgodne zasoby.](../how-to/remediate-resources.md)
- Aby sprawdzić, czym jest grupa zarządzania, zobacz [Organize your resources with Azure management groups (Organizowanie zasobów za pomocą grup zarządzania platformy Azure).](../../management-groups/overview.md)
