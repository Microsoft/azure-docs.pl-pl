---
title: Dowiedz się Azure Policy usługi Azure Kubernetes Service
description: Dowiedz się, w jaki sposób Azure Policy używać usługi rego i Otwórz agenta zasad do zarządzania klastrami w usłudze Azure Kubernetes.
ms.date: 03/18/2020
ms.topic: conceptual
ms.openlocfilehash: f6c70d676914cf861ecc378efc4ec23a78879f6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187719"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Omówienie usługi Azure Policy dla usługi Azure Kubernetes Service

Azure Policy integruje się z [usługą Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS), aby stosować wymuszanie i zabezpieczenia w klastrach w scentralizowany, spójny sposób.
Rozszerzając użycie [strażnika](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated) v2, _elementu webhook kontrolera systemu Admission_ dla programu [Open Policy Agent](https://www.openpolicyagent.org/) (nieprzez), Azure Policy umożliwia zarządzanie stanem zgodności zasobów platformy Azure i klastrów AKS z jednego miejsca oraz tworzenie raportów na ich temat.

> [!NOTE]
> Azure Policy dla AKS jest w ograniczonej wersji zapoznawczej i obsługuje tylko wbudowane definicje zasad.

## <a name="overview"></a>Omówienie

Aby włączyć i używać Azure Policy dla AKS z klastrem AKS, wykonaj następujące czynności:

- [Włączanie funkcji w wersji zapoznawczej](#opt-in-for-preview)
- [Instalowanie dodatku Azure Policy](#installation-steps)
- [Przypisywanie definicji zasad dla AKS](#built-in-policies)
- [Zaczekaj na weryfikację](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Zgoda na wersję zapoznawczą

Przed zainstalowaniem Azure Policy dodatku lub włączenia dowolnych funkcji usługi subskrypcja musi umożliwić dostawcy zasobów **Microsoft. ContainerService** i **Microsoft. PolicyInsights** , a następnie zostać zatwierdzona w celu dołączenia do wersji zapoznawczej. Aby dołączyć do wersji zapoznawczej, wykonaj następujące kroki w Azure Portal lub w interfejsie wiersza polecenia platformy Azure:

- Azure Portal:

  1. Zarejestruj dostawców zasobów **Microsoft. ContainerService** i **Microsoft. PolicyInsights** . Aby uzyskać instrukcje, zobacz [dostawcy zasobów i ich typy](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

  1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

     :::image type="content" source="../media/rego-for-aks/search-policy.png" alt-text="Wyszukaj zasady w obszarze wszystkie usługi" border="false":::

  1. Wybierz pozycję **Dołącz podgląd** w lewej części strony Azure Policy.

     :::image type="content" source="../media/rego-for-aks/join-aks-preview.png" alt-text="Dołącz do zasad usługi AKS Preview" border="false":::

  1. Wybierz wiersz subskrypcji, która ma zostać dodana do wersji zapoznawczej.

  1. Wybierz przycisk **zgody** w górnej części listy subskrypcji.

- Interfejs wiersza polecenia platformy Azure:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataPlaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Dodatek Azure Policy

_Dodatek Azure Policy_ dla Kubernetes łączy usługę Azure Policy z kontrolerem strażnika. Dodatek, który jest instalowany w przestrzeni nazw _Azure-Policy_ , wprowadza następujące funkcje:

- Sprawdza przy użyciu Azure Policy do przypisań do klastra AKS
- Pobiera i buforuje Szczegóły zasad, w tym definicję zasad _rego_ , jako **configmaps**
- Uruchamia sprawdzanie zgodności pełnego skanowania w klastrze AKS
- Szczegóły inspekcji i zgodności raportów z powrotem do Azure Policy

### <a name="installing-the-add-on"></a>Instalowanie dodatku

#### <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem dodatku w klastrze AKS należy zainstalować rozszerzenie wersji zapoznawczej. Ten krok jest realizowany przy użyciu interfejsu wiersza polecenia platformy Azure:

1. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.62 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Klaster AKS musi mieć wersję _1,10_ lub nowszą. Aby sprawdzić poprawność wersji klastra AKS, użyj następującego skryptu:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Zainstaluj wersję _0.4.0_ rozszerzenia wiersza polecenia platformy Azure w wersji zapoznawczej dla AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Jeśli wcześniej zainstalowano rozszerzenie _AKS-Preview_ , zainstaluj wszystkie aktualizacje za pomocą `az extension update --name aks-preview` polecenia.

#### <a name="installation-steps"></a>Kroki instalacji

Po zakończeniu wymagań wstępnych Zainstaluj dodatek Azure Policy w klastrze AKS, którym chcesz zarządzać.

- Azure Portal

  1. Uruchom usługę AKS w Azure Portal, klikając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **usługi Kubernetes Services**.

  1. Wybierz jeden z klastrów AKS.

  1. Wybierz pozycję **zasady (wersja zapoznawcza)** po lewej stronie usługi Kubernetes.

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="Zasady z klastra AKS" border="false":::

  1. Na stronie głównej wybierz przycisk **Włącz dodatek** .

     :::image type="content" source="../media/rego-for-aks/enable-policy-add-on.png" alt-text="Włącz Azure Policy dla dodatku AKS" border="false":::

     > [!NOTE]
     > Jeśli przycisk **Włącz dodatek** jest wyszarzony, subskrypcja nie została jeszcze dodana do wersji zapoznawczej. Zapoznaj się z tematem zajrzyj do [wersji zapoznawczej](#opt-in-for-preview) w celu wykonania wymaganych kroków.

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Częstotliwość sprawdzania poprawności i raportowania

Dodatek jest sprawdzany przy użyciu Azure Policy do zmian przypisań zasad co 5 minut. W trakcie tego cyklu odświeżania dodatek usuwa wszystkie _configmaps_ w przestrzeni nazw _zasad platformy Azure_ , a następnie ponownie tworzy _configmaps_ do użycia przez strażnika.

> [!NOTE]
> _Administrator klastra_ może mieć uprawnienia do przestrzeni nazw _zasad platformy Azure_ , nie jest to zalecane ani obsługiwane w celu wprowadzania zmian w przestrzeni nazw. Wszelkie zmiany wprowadzane ręcznie są tracone podczas cyklu odświeżania.

Co 5 minut, dodatek wywołuje pełne skanowanie klastra. Po zebraniu szczegółowych informacji o pełnym skanowaniu i wszystkich ocenach w czasie rzeczywistym przez strażnika podjętych zmian w klastrze dodatek zgłasza wyniki z powrotem do Azure Policy w celu uwzględnienia informacji o [zgodności](../how-to/get-compliance-data.md) , takich jak wszystkie Azure Policy przypisania. W cyklu inspekcji są zwracane tylko wyniki aktywnych przypisań zasad.

## <a name="policy-language"></a>Język zasad

Azure Policy struktura języka dla zarządzania AKS jest zgodna z istniejącymi zasadami. Efekt _EnforceRegoPolicy_ służy do zarządzania klastrami AKS i zawiera _szczegółowe_ właściwości specyficzne dla pracy z nieprzez i strażnikiem v2. Aby uzyskać szczegółowe informacje i przykłady, zobacz efekt [EnforceRegoPolicy](effects.md#enforceregopolicy) .

Jako część właściwości _szczegóły. Policy_ w definicji zasad, Azure Policy przekazuje identyfikator URI zasad rego do dodatku. Rego to język, w którym NIEPRZEZ i strażnik może zweryfikować lub zmodyfikować żądanie do klastra Kubernetes. Dzięki obsłudze istniejącej normy Kubernetes Management Azure Policy umożliwia ponowne użycie istniejących reguł i sparowanie ich z Azure Policy na potrzeby ujednoliconego środowiska raportowania zgodności z chmurą. Aby uzyskać więcej informacji, zobacz [co to jest rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Wbudowane zasady

Aby znaleźć wbudowane zasady zarządzania AKS przy użyciu Azure Portal, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w Azure Portal. W lewym okienku wybierz pozycję **wszystkie usługi** , a następnie wyszukaj i wybierz pozycję **zasady**.

1. W lewym okienku strony Azure Policy wybierz pozycję **definicje**.

1. W polu listy rozwijanej Kategoria Użyj **opcji Zaznacz wszystko** , aby wyczyścić filtr, a następnie wybierz pozycję **Usługa Kubernetes**.

1. Wybierz definicję zasad, a następnie wybierz przycisk **Przypisz** .

> [!NOTE]
> Podczas przypisywania Azure Policy definicji AKS **zakres** musi zawierać zasób klastra AKS.

Alternatywnie można [użyć zasad do szybkiego](../assign-policy-portal.md) startu, aby znaleźć i przypisać zasady AKS. Wyszukaj definicję zasad Kubernetes zamiast przykładu "Inspekcja maszyn wirtualnych".

> [!IMPORTANT]
> Wbudowane zasady w kategorii **Kubernetes** są przeznaczone tylko do użytku z AKS.

## <a name="logging"></a>Rejestrowanie

### <a name="azure-policy-add-on-logs"></a>Azure Policy dzienniki dodatków

Jako kontroler Kubernetes/kontener, dodatek Azure Policy przechowuje dzienniki w klastrze AKS. Dzienniki są widoczne na stronie **wglądu** w klaster AKS. Aby uzyskać więcej informacji, zobacz [Omówienie wydajności klastra AKS za pomocą Azure monitor dla kontenerów](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Dzienniki strażnika

Aby włączyć dzienniki strażnika dla nowych żądań zasobów, wykonaj kroki opisane w temacie [Włączanie i przeglądanie dzienników węzła głównego Kubernetes w AKS](../../../aks/view-master-logs.md).
Oto przykładowe zapytanie umożliwiające wyświetlenie zdarzeń odrzuconych dla nowych żądań zasobów:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Aby wyświetlić dzienniki z kontenerów strażników, wykonaj kroki opisane w temacie [Włączanie i przeglądanie dzienników węzła głównego Kubernetes w AKS](../../../aks/view-master-logs.md) i sprawdź opcję _polecenia-apiserver_ w okienku **ustawień diagnostycznych** .

## <a name="remove-the-add-on"></a>Usuń dodatek

Aby usunąć dodatek Azure Policy z klastra AKS, użyj Azure Portal lub interfejsu wiersza polecenia platformy Azure:

- Azure Portal

  1. Uruchom usługę AKS w Azure Portal, klikając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **usługi Kubernetes Services**.

  1. Wybierz klaster AKS, w którym chcesz wyłączyć dodatek Azure Policy.

  1. Wybierz pozycję **zasady (wersja zapoznawcza)** po lewej stronie usługi Kubernetes.

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="Zasady z klastra AKS" border="false":::

  1. Na stronie głównej wybierz przycisk **Wyłącz dodatek** .

     :::image type="content" source="../media/rego-for-aks/disable-policy-add-on.png" alt-text="Wyłącz Azure Policy dla dodatku AKS" border="false":::

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
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
- Liczba zasad strażnika niezainstalowanych przez dodatek Azure Policy

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj [strukturę definicji usługi Azure Policy](definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).