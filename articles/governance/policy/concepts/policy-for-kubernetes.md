---
title: Dowiedz się Azure Policy Kubernetes
description: Dowiedz się, w jaki sposób Azure Policy rego i Otwórz agenta zasad, aby zarządzać klastrami z systemem Kubernetes na platformie Azure lub lokalnie.
ms.date: 09/29/2020
ms.topic: conceptual
ms.openlocfilehash: 3478a98ef98001ee8a2e3bb502bf289ed52285e7
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951540"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Opis usługi Azure Policy dla klastrów Kubernetes

Azure Policy rozszerza [strażnik](https://github.com/open-policy-agent/gatekeeper) v3, _element webhook kontrolera Admission_ dla programu [Open Policy Agent](https://www.openpolicyagent.org/) (nieprzez), aby zastosować wymuszanie i zabezpieczenia w klastrach w scentralizowany, spójny sposób. Azure Policy umożliwia zarządzanie stanem zgodności klastrów Kubernetes w jednym miejscu oraz ich raportowanie. Dodatek wprowadza następujące funkcje:

- Sprawdza, czy usługa Azure Policy przypisań zasad do klastra.
- Wdraża definicje zasad w klastrze jako [szablon ograniczenia](https://github.com/open-policy-agent/gatekeeper#constraint-templates) i zasoby niestandardowe [ograniczeń](https://github.com/open-policy-agent/gatekeeper#constraints) .
- Raportuje szczegóły inspekcji i zgodności z powrotem do usługi Azure Policy.

Azure Policy for Kubernetes obsługuje następujące środowiska klastra:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Platforma Kubernetes z włączoną usługą Azure Arc](../../../azure-arc/kubernetes/overview.md)
- [Aparat AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Dodatki dla aparatu AKS i Kubernetes z funkcją Arc są w **wersji zapoznawczej**. Azure Policy for Kubernetes obsługuje tylko pule węzłów systemu Linux i wbudowane definicje zasad. Wbudowane definicje zasad znajdują się w kategorii **Kubernetes** . Definicje zasad z ograniczeniami w wersji zapoznawczej ze skutkami **EnforceOPAConstraint** i **EnforceRegoPolicy** oraz pokrewną kategorią **usługi Kubernetes** są _przestarzałe_. Zamiast tego należy użyć trybu _inspekcji_ efektów i _Odmów_ przy użyciu dostawcy zasobów `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Omówienie

Aby włączyć i użyć Azure Policy z klastrem Kubernetes, wykonaj następujące czynności:

1. Skonfiguruj klaster Kubernetes i zainstaluj dodatek:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Platforma Kubernetes z włączoną usługą Azure Arc](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [Aparat AKS](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Aby zapoznać się z typowymi problemami z instalacją, zobacz [Rozwiązywanie problemów — dodatek Azure Policy](../troubleshoot/general.md#add-on-installation-errors).

1. [Opis języka Azure Policy Kubernetes](#policy-language)

1. [Przypisywanie wbudowanej definicji do klastra Kubernetes](#assign-a-built-in-policy-definition)

1. [Zaczekaj na weryfikację](#policy-evaluation)

## <a name="limitations"></a>Ograniczenia

Następujące ograniczenia ogólne mają zastosowanie do Azure Policy dodatku dla klastrów Kubernetes:

- Dodatek Azure Policy dla Kubernetes jest obsługiwany w Kubernetes w wersji **1,14** lub nowszej.
- Dodatek Azure Policy dla Kubernetes można wdrożyć tylko w pulach węzłów systemu Linux
- Obsługiwane są tylko wbudowane definicje zasad
- Maksymalna liczba niezgodnych rekordów na zasady dla klastra: **500**
- Maksymalna liczba niezgodnych rekordów na subskrypcję: **1 000 000**
- Instalacje strażnika poza dodatkiem Azure Policy nie są obsługiwane. Przed włączeniem dodatku Azure Policy Odinstaluj wszystkie składniki zainstalowane przez poprzednią instalację strażnika.
- [Przyczyny niezgodności](../how-to/determine-non-compliance.md#compliance-reasons) nie są dostępne dla `Microsoft.Kubernetes.Data` 
   [trybu dostawcy zasobów](./definition-structure.md#resource-provider-modes). Użyj [szczegółów składnika](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).
- [Wykluczenia](./exemption-structure.md) nie są obsługiwane w przypadku [trybów dostawcy zasobów](./definition-structure.md#resource-provider-modes).

Następujące ograniczenia mają zastosowanie tylko do Azure Policy dodatku dla AKS:

- Nie można jednocześnie włączyć [zasad zabezpieczeń AKS](../../../aks/use-pod-security-policies.md) na poziomie i dodatku Azure Policy dla AKS. Aby uzyskać więcej informacji, zobacz [AKS pod kątem zabezpieczeń](../../../aks/use-pod-security-on-azure-policy.md#limitations).
- Obszary nazw są automatycznie wykluczane przez Azure Policy dodatku do oceny: _polecenia-system_, _strażnik-system_i _AKS-Periscope_.

## <a name="recommendations"></a>Zalecenia

Poniżej przedstawiono ogólne zalecenia dotyczące używania dodatku Azure Policy:

- Aby można było uruchomić dodatek Azure Policy, wymagane są 3 składniki strażnika: 1 podelementy inspekcji w ramach i 2 replik. Te składniki zużywają więcej zasobów, ponieważ liczba zasobów Kubernetes i przypisań zasad zwiększa się w klastrze, który wymaga operacji inspekcji i wymuszania.

  - W przypadku mniej niż 500 zasobników w jednym klastrze z maksymalnie 20 ograniczeniami: 2 procesorów wirtualnych vCPU i 350 MB pamięci na składnik.
  - Ponad 500 zasobników w jednym klastrze z maksymalnie 40 ograniczeniami: 3 procesorów wirtualnych vCPU i 600 MB pamięci na składnik.

- System Windows [nie obsługuje kontekstów zabezpieczeń](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  W związku z tym niektóre definicje Azure Policy, na przykład niezezwalanie na uprawnienia główne, nie mogą być eskalacją w ramach systemu Windows i mają zastosowanie tylko do zasobników z systemem Linux.

Poniższe zalecenie dotyczy tylko AKS i dodatku Azure Policy:

- Użyj puli węzłów systemu z funkcją `CriticalAddonsOnly` zmiany harmonogramu, aby zaplanować zasobniki strażników. Aby uzyskać więcej informacji, zobacz [Korzystanie z pul węzłów systemowych](../../../aks/use-system-pools.md#system-and-user-node-pools).
- Zabezpiecz ruch wychodzący z klastrów AKS. Aby uzyskać więcej informacji, zobacz [Kontrola ruchu wychodzącego dla węzłów klastra](../../../aks/limit-egress-traffic.md).
- Jeśli klaster został `aad-pod-identity` włączony, w WĘŹLE NMI (tożsamość zarządzana) są modyfikowane węzły dołączenie iptables do przechwytywania wywołań do punktu końcowego metadanych wystąpienia platformy Azure. Ta konfiguracja oznacza, że wszystkie żądania wysłane do punktu końcowego metadanych są przechwytywane przez NMI, nawet jeśli nie są używane `aad-pod-identity` . AzurePodIdentityException CRD można skonfigurować w taki sposób `aad-pod-identity` , aby informował, że wszelkie żądania kierowane do punktu końcowego metadanych pochodzące z elementu pod, które pasują do etykiet zdefiniowanych w CRD, powinny być przekazywane z serwerem proxy bez żadnego przetwarzania w NMI. Systemowy `kubernetes.azure.com/managedby: aks` wymiarname z etykietą w _polecenia —_ przestrzeń nazw systemu powinna zostać wykluczona w `aad-pod-identity` ramach konfigurowania AzurePodIdentityException CRD. Aby uzyskać więcej informacji, zobacz temat [wyłączanie usługi AAD-pod-Identity dla określonego elementu lub aplikacji](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md).
  Aby skonfigurować wyjątek, Zainstaluj polecenie [MIC-Exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="install-azure-policy-add-on-for-aks"></a>Zainstaluj dodatek Azure Policy dla AKS

Przed zainstalowaniem dodatku Azure Policy lub włączenia dowolnych funkcji usługi subskrypcja musi włączyć dostawców zasobów **Microsoft. ContainerService** i **Microsoft. PolicyInsights** .

1. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Zarejestruj dostawców zasobów i funkcje w wersji zapoznawczej.

   - Azure Portal:

     Zarejestruj dostawców zasobów **Microsoft. ContainerService** i **Microsoft. PolicyInsights** . Aby uzyskać instrukcje, zobacz [dostawcy zasobów i ich typy](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

   - Interfejs wiersza polecenia platformy Azure:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Kubernetes Service provider
     az provider register --namespace Microsoft.ContainerService

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Jeśli zainstalowano ograniczone definicje zasad wersji zapoznawczej, Usuń dodatek z przyciskiem **Wyłącz** w klastrze AKS na stronie **zasady** .

1. Klaster AKS musi mieć wersję _1,14_ lub nowszą. Aby sprawdzić poprawność wersji klastra AKS, użyj następującego skryptu:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Zainstaluj wersję _2.12.0_ lub nowszą interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Po zakończeniu powyższych kroków wymagań wstępnych Zainstaluj dodatek Azure Policy w klastrze AKS, którym chcesz zarządzać.

- Azure Portal

  1. Uruchom usługę AKS w Azure Portal, wybierając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **usługi Kubernetes Services**.

  1. Wybierz jeden z klastrów AKS.

  1. Wybierz pozycję **zasady** po lewej stronie usługi Kubernetes.

  1. Na stronie głównej wybierz przycisk **Włącz dodatek** .

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Jeśli przycisk **Wyłącz dodatek** jest włączony i zostanie wyświetlony komunikat z ostrzeżeniem o migracji w wersji 2, zostanie zainstalowany dodatek V1 i należy go usunąć przed przypisaniem definicji zasad w wersji 2. _Przestarzały_ dodatek V1 zostanie automatycznie zamieniony na dodatek v2 od początku sierpnia,
     > 2020. Należy następnie przypisać nowe wersje systemu v2 definicji zasad. Aby przeprowadzić uaktualnienie teraz, wykonaj następujące kroki:
     >
     > 1. Sprawdź, czy klaster AKS ma zainstalowany dodatek V1, odwiedzając stronę **zasady** w klastrze AKS i ma "bieżący klaster używa dodatku Azure Policy w wersji 1..." Komunikat.
     > 1. [Usuń dodatek](#remove-the-add-on-from-aks).
     > 1. Wybierz przycisk **Włącz dodatek** , aby zainstalować wersję v2 dodatku.
     > 1. [Przypisywanie wersji systemu v2 wbudowanych definicji zasad w wersji 1](#assign-a-built-in-policy-definition)

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Aby sprawdzić, czy instalacja dodatku zakończyła się pomyślnie i czy są uruchomione wszystkie _Zasady platformy Azure_ i _strażników_ , uruchom następujące polecenie:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Na koniec sprawdź, czy najnowszy dodatek został zainstalowany, uruchamiając to polecenie interfejsu wiersza polecenia platformy Azure, zastępując `<rg>` ją nazwą grupy zasobów i `<cluster-name>` nazwą klastra AKS: `az aks show -g <rg> -n <cluster-name>` . Wynik powinien wyglądać podobnie do poniższego wyjścia i **konfiguracji. wersja** powinna być `v2` :

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

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Zainstaluj dodatek Azure Policy dla usługi Azure ARC z włączonym Kubernetes (wersja zapoznawcza)

Przed zainstalowaniem dodatku Azure Policy lub włączenia dowolnych funkcji usługi subskrypcja musi włączyć dostawcę zasobów **Microsoft. PolicyInsights** i utworzyć przypisanie roli dla jednostki usługi klastra.

1. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Aby włączyć dostawcę zasobów, postępuj zgodnie z instrukcjami w obszarze [dostawcy zasobów i typy](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) lub Uruchom interfejs wiersza polecenia platformy Azure lub Azure PowerShell polecenie:

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

1. Klaster Kubernetes musi mieć wersję _1,14_ lub nowszą.

1. Zainstaluj [Helm 3](https://v3.helm.sh/docs/intro/install/).

1. Klaster Kubernetes włączony dla usługi Azure Arc. Aby uzyskać więcej informacji, zobacz Dołączanie [klastra Kubernetes do usługi Azure Arc](../../../azure-arc/kubernetes/connect-cluster.md).

1. Mają w pełni kwalifikowany identyfikator zasobu platformy Azure dla klastra Kubernetes z włączoną funkcją Azure Arc.

1. Otwórz port dla dodatku. Dodatek Azure Policy używa tych domen i portów do pobierania definicji zasad i przypisań i raportowania zgodności klastra z powrotem do Azure Policy.

   |Obszar |Port |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Przypisz przypisanie roli modułu zapisywania danych usługi Policy Insights (wersja zapoznawcza) do klastra Kubernetes z włączoną funkcją Azure Arc. Zastąp ciąg `<subscriptionId>` identyfikatorem subskrypcji, `<rg>` a grupa zasobów klastra Kubernetes z włączoną funkcją Azure Arc i `<clusterName>` nazwą klastra Kubernetes z obsługą usługi Azure Arc. Śledź zwrócone wartości dla identyfikatora _AppID_, _hasła_i _dzierżawy_ na potrzeby kroków instalacji.

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

Po zakończeniu powyższych kroków wymagań wstępnych Zainstaluj dodatek Azure Policy w klastrze Kubernetes z obsługą usługi Azure ARC:

1. Dodaj Azure Policy repozytorium dodatków do Helm:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Zainstaluj dodatek Azure Policy przy użyciu wykresu Helm:

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

   Aby uzyskać więcej informacji na temat instalacji wykresu Helm dodatku, [Azure Policy Zobacz definicję wykresu Helm dodatku](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) w witrynie GitHub.

Aby sprawdzić, czy instalacja dodatku zakończyła się pomyślnie i czy są uruchomione wszystkie _Zasady platformy Azure_ i _strażników_ , uruchom następujące polecenie:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Zainstaluj dodatek Azure Policy dla aparatu AKS (wersja zapoznawcza)

Przed zainstalowaniem dodatku Azure Policy lub włączenia dowolnych funkcji usługi subskrypcja musi włączyć dostawcę zasobów **Microsoft. PolicyInsights** i utworzyć przypisanie roli dla jednostki usługi klastra.

1. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.62 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Aby włączyć dostawcę zasobów, postępuj zgodnie z instrukcjami w obszarze [dostawcy zasobów i typy](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) lub Uruchom interfejs wiersza polecenia platformy Azure lub Azure PowerShell polecenie:

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

   - Jeśli nie znasz identyfikatora aplikacji głównej usługi klastra, poszukaj go przy użyciu następującego polecenia.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Przypisz przypisanie roli modułu zapisywania danych usługi Policy Insights (wersja zapoznawcza) do identyfikatora aplikacji głównej (wartość _aadClientID_ z poprzedniego kroku) przy użyciu interfejsu wiersza polecenia platformy Azure. Zastąp wartość `<subscriptionId>` identyfikatorem subskrypcji i `<aks engine cluster resource group>` grupą zasobów, w której znajduje się klaster KUBERNETES aparatu AKS.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Po zakończeniu powyższych kroków wymagań wstępnych Zainstaluj dodatek Azure Policy. Instalacja może być wykonywana w cyklu tworzenia lub aktualizacji aparatu AKS lub jako niezależna akcja w istniejącym klastrze.

- Zainstaluj podczas procesu tworzenia lub aktualizacji

  Aby włączyć dodatek Azure Policy podczas tworzenia nowego klastra z własnym zarządzaniem lub jako aktualizacji istniejącego klastra, należy uwzględnić definicję klastra właściwości [dodatków](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) dla aparatu AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Aby uzyskać więcej informacji na temat, zobacz [Definicja klastra aparatu AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Instalowanie w istniejącym klastrze z wykresami Helm

  Wykonaj następujące kroki, aby przygotować klaster i zainstalować dodatek:

  1. Zainstaluj [Helm 3](https://v3.helm.sh/docs/intro/install/).

  1. Dodaj repozytorium Azure Policy do Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Aby uzyskać więcej informacji, zobacz [Helm Chart-Przewodnik Szybki Start](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Zainstaluj dodatek przy użyciu wykresu Helm. Zastąp wartość `<subscriptionId>` identyfikatorem subskrypcji i `<aks engine cluster resource group>` grupą zasobów, w której znajduje się klaster KUBERNETES aparatu AKS.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Aby uzyskać więcej informacji na temat instalacji wykresu Helm dodatku, [Azure Policy Zobacz definicję wykresu Helm dodatku](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) w witrynie GitHub.

     > [!NOTE]
     > Ze względu na relacje między dodatkiem Azure Policy i identyfikatorem grupy zasobów Azure Policy obsługuje tylko jeden klaster aparatu AKS dla każdej grupy zasobów.

Aby sprawdzić, czy instalacja dodatku zakończyła się pomyślnie i czy są uruchomione wszystkie _Zasady platformy Azure_ i _strażników_ , uruchom następujące polecenie:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Język zasad

Azure Policy struktura języka dla zarządzania Kubernetes jest zgodna z istniejącymi definicjami zasad. W [trybie dostawcy zasobów](./definition-structure.md#resource-provider-modes) `Microsoft.Kubernetes.Data` , [Inspekcja](./effects.md#audit) efektów i [Odmów](./effects.md#deny) są używane do zarządzania klastrami Kubernetes. _Inspekcja_ i _Odmów_ muszą podawać właściwości **szczegółów** charakterystyczne dla pracy z [nieprzez ograniczeń środowiska](https://github.com/open-policy-agent/frameworks/tree/master/constraint) i strażnik v3.

W ramach właściwości _szczegóły. constraintTemplate_ i _szczegóły. ograniczenia_ w definicji zasad Azure Policy przekazuje identyfikatory URI tych [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) do dodatku. Rego to język, w którym NIEPRZEZ i strażnik obsługuje walidację żądania do klastra Kubernetes. Dzięki obsłudze istniejącej normy Kubernetes Management Azure Policy umożliwia ponowne użycie istniejących reguł i sparowanie ich z Azure Policy na potrzeby ujednoliconego środowiska raportowania zgodności z chmurą. Aby uzyskać więcej informacji, zobacz [co to jest rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Przypisywanie wbudowanej definicji zasad

Aby przypisać definicję zasad do klastra Kubernetes, należy przypisać odpowiednie operacje przypisania zasad kontroli dostępu opartej na rolach (Azure RBAC). Współautor i **właściciel** **zasad zasobów** wbudowanej platformy Azure mają te operacje. Aby dowiedzieć się więcej, zobacz [uprawnienia usługi Azure RBAC w Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

Znajdź wbudowane definicje zasad służące do zarządzania klastrem za pomocą Azure Portal, wykonując następujące czynności:

1. Uruchom usługę Azure Policy w Azure Portal. W lewym okienku wybierz pozycję **wszystkie usługi** , a następnie wyszukaj i wybierz pozycję **zasady**.

1. W lewym okienku strony Azure Policy wybierz pozycję **definicje**.

1. W polu listy rozwijanej Kategoria Użyj **opcji Zaznacz wszystko** , aby wyczyścić filtr, a następnie wybierz pozycję **Kubernetes**.

1. Wybierz definicję zasad, a następnie wybierz przycisk **Przypisz** .

1. Ustaw **zakres** dla grupy zarządzania, subskrypcji lub grupy zasobów klastra Kubernetes, w którym będą stosowane przypisania zasad.

   > [!NOTE]
   > Podczas przypisywania Azure Policy definicji Kubernetes **zakres** musi zawierać zasób klastra. W przypadku klastra aparatu AKS **zakres** musi być grupą zasobów klastra.

1. Nadaj zasadom **nazwę** i **Opis** , których można użyć do łatwego identyfikowania.

1. Ustaw [wymuszanie zasad](./assignment-structure.md#enforcement-mode) na jedną z poniższych wartości.

   - **Włączone** — Wymuś zasady w klastrze. Odmówiono realizacji żądań Kubernetes z naruszeniami.

   - **Wyłączone** — nie Wymuszaj zasad w klastrze. Żądania odmowy Kubernetes z naruszeniami nie są odrzucane. Wyniki oceny zgodności są nadal dostępne. Podczas wdrażania nowych definicji zasad do uruchamiania klastrów, opcja _wyłączone_ jest przydatna do testowania definicji zasad, ponieważ żądania dopuszczenia z naruszeniami nie są odrzucane.

1. Wybierz pozycję **Dalej**.

1. Ustaw **wartości parametrów**

   - Aby wykluczyć przestrzenie nazw Kubernetes z oceny zasad, określ listę przestrzeni nazw w **wykluczeniach przestrzeni nazw**parametrów. Zaleca się wykluczenie: _polecenia-system_, _strażnik-system_i _Azure-Arc_.

1. Wybierz pozycję **Przejrzyj i utwórz**.

Alternatywnie możesz znaleźć i przypisać zasady Kubernetes przy użyciu [przystawki przypisywanie zasad —](../assign-policy-portal.md) szybki start dla portalu. Wyszukaj definicję zasad Kubernetes zamiast przykładu "Inspekcja maszyn wirtualnych".

> [!IMPORTANT]
> Wbudowane definicje zasad są dostępne dla klastrów Kubernetes w kategorii **Kubernetes**. Aby zapoznać się z listą wbudowanych definicji zasad, zobacz [Kubernetes Samples](../samples/built-in-policies.md#kubernetes).

## <a name="policy-evaluation"></a>Ocena zasad

Dodatek zaewidencjonuje za pomocą usługi Azure Policy, aby zmienić przydziały zasad co 15 minut.
W trakcie tego cyklu odświeżania dodatek sprawdza zmiany. Te zmiany wyzwalają tworzenie, aktualizowanie lub usuwanie szablonów ograniczeń i ograniczeń.

W klastrze Kubernetes, jeśli przestrzeń nazw ma jedną z następujących etykiet, żądania przyjęcia z naruszeniami nie są odrzucane. Wyniki oceny zgodności są nadal dostępne.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Administrator klastra może mieć uprawnienia do tworzenia i aktualizowania szablonów ograniczeń oraz zasobów ograniczeń instalowanych przez dodatek Azure Policy, ale nie są to obsługiwane scenariusze, ponieważ ręczne aktualizacje są zastępowane. Strażnik kontynuuje ocenę zasad, które istniały przed zainstalowaniem dodatku i przypisanie definicji zasad Azure Policy.

Co 15 minut, dodatek wywołuje pełne skanowanie klastra. Po zebraniu szczegółowych informacji o pełnym skanowaniu i wszystkich ocenach w czasie rzeczywistym przez strażnika podjętych zmian w klastrze dodatek zgłasza wyniki z powrotem do Azure Policy w celu uwzględnienia informacji o [zgodności](../how-to/get-compliance-data.md) , takich jak wszystkie Azure Policy przypisania. W cyklu inspekcji są zwracane tylko wyniki aktywnych przypisań zasad. Wyniki inspekcji mogą być również widoczne jako [naruszenia](https://github.com/open-policy-agent/gatekeeper#audit) wymienione w polu Stan niepowodzenia ograniczenia. Aby uzyskać szczegółowe informacje dotyczące _niezgodnych_ zasobów, zobacz [szczegóły składnika dla trybu dostawcy zasobów](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).

> [!NOTE]
> Każdy raport zgodności w Azure Policy dla klastrów Kubernetes obejmuje wszystkie naruszenia w ciągu ostatnich 45 minut. Sygnatura czasowa wskazuje, kiedy wystąpiło naruszenie.

## <a name="logging"></a>Rejestrowanie

Jako kontroler Kubernetes/kontener, zarówno _usługa Azure-Policy_ , jak i _strażnik_ , przechowują dzienniki w klastrze Kubernetes. Dzienniki mogą być uwidocznione na stronie **wglądu** w klaster Kubernetes.
Aby uzyskać więcej informacji, zobacz [monitorowanie wydajności klastra Kubernetes za pomocą Azure monitor dla kontenerów](../../../azure-monitor/insights/container-insights-analyze.md).

Aby wyświetlić dzienniki dodatków, użyj `kubectl` :

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Aby uzyskać więcej informacji, zobacz [debugowanie strażnika](https://github.com/open-policy-agent/gatekeeper#debugging) w dokumentacji strażnika.

## <a name="remove-the-add-on"></a>Usuń dodatek

### <a name="remove-the-add-on-from-aks"></a>Usuń dodatek z AKS

Aby usunąć dodatek Azure Policy z klastra AKS, użyj Azure Portal lub interfejsu wiersza polecenia platformy Azure:

- Azure Portal

  1. Uruchom usługę AKS w Azure Portal, wybierając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **usługi Kubernetes Services**.

  1. Wybierz klaster AKS, w którym chcesz wyłączyć dodatek Azure Policy.

  1. Wybierz pozycję **zasady** po lewej stronie usługi Kubernetes.

  1. Na stronie głównej wybierz przycisk **Wyłącz dodatek** .

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Usuń dodatek z usługi Azure ARC z włączonym Kubernetes

Aby usunąć Azure Policy dodatek i strażnik z klastra Kubernetes z obsługą usługi Azure ARC, uruchom następujące polecenie Helm:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>Usuń dodatek z aparatu AKS

Aby usunąć dodatek Azure Policy i strażnika z klastra aparatu AKS, użyj metody, która jest wyrównana do sposobu instalacji dodatku:

- Jeśli jest zainstalowany, należy ustawić właściwość **Dodatki** w definicji klastra dla aparatu AKS:

  Wdróż ponownie definicję klastra w aparacie AKS po zmianie właściwości **Dodatki** dla _usługi Azure-Policy_ na wartość false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Aby uzyskać więcej informacji, zobacz [aparat AKS — wyłączanie dodatku Azure Policy](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Jeśli program został zainstalowany z wykresami Helm, uruchom następujące polecenie Helm:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dane diagnostyczne zbierane przez Azure Policy dodatek

Dodatek Azure Policy dla Kubernetes zbiera ograniczone dane diagnostyczne klastra. Te dane diagnostyczne są istotnymi danymi technicznymi związanymi z oprogramowaniem i wydajnością. Jest on używany w następujący sposób:

- Kontynuuj aktualizowanie Azure Policy
- Utrzymywanie bezpiecznych, niezawodnych i wydajnych Azure Policy dodatków
- Ulepszanie Azure Policy dodatku poprzez zagregowaną analizę użycia dodatku

Informacje zbierane przez dodatek nie są danymi osobistymi. Obecnie są zbierane następujące szczegóły:

- Azure Policy wersji agenta dodatku
- Typ klastra
- Region klastra
- Grupa zasobów klastra
- Identyfikator zasobu klastra
- Identyfikator subskrypcji klastra
- System operacyjny klastra (przykład: Linux)
- Miasto klastra (przykład: Seattle)
- Województwo klastra (przykład: Waszyngton)
- Kraj lub region klastra (przykład: Stany Zjednoczone)
- Wyjątki/błędy napotykane przez dodatek Azure Policy podczas instalacji agenta podczas oceny zasad
- Liczba definicji zasad strażnika niezainstalowanych przez dodatek Azure Policy

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj [strukturę definicji usługi Azure Policy](definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).