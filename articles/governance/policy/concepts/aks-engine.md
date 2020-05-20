---
title: Uczenie Azure Policy aparatu AKS
description: Dowiedz się, w jaki sposób Azure Policy CustomResourceDefinitions i Otwórz agenta zasad z strażnik v3, aby zarządzać klastrami z aparatem AKS.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: f1d6f08f07e015b1e7fe2886746bf9e8b43fdce0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654972"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Opis Azure Policy aparatu AKS

Azure Policy integruje się z [aparatem AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md), system, który zapewnia wygodne narzędzia umożliwiające szybkie uruchomienie samozarządzanego klastra Kubernetes na platformie Azure. Ta Integracja umożliwia wymuszanie skalowania i ochronę w przypadku klastrów samodzielnego zarządzania aparatem AKS w scentralizowany, spójny sposób. Rozszerzając użycie programu [Open Policy Agent](https://www.openpolicyagent.org/) (nieprzez) — [strażnik](https://github.com/open-policy-agent/gatekeeper) v3 (beta), _elementu webhook kontrolera systemu Admission_ dla Kubernetes, Azure Policy umożliwia zarządzanie stanem zgodności zasobów platformy Azure i klastrami z własnym programem AKS Engine z jednego miejsca.

> [!NOTE]
> Azure Policy dla aparatu AKS jest w publicznej wersji zapoznawczej i nie ma umowy SLA. Program strażnik V3 jest w wersji beta i jest obsługiwany przez społeczność programu Open Source. Usługa obsługuje tylko wbudowane definicje zasad, pule węzłów systemu Linux i pojedynczy klaster AKS Engine dla każdej grupy zasobów skonfigurowanej przy użyciu nazwy głównej usługi.

> [!IMPORTANT]
> Aby uzyskać pomoc techniczną dotyczącą Azure Policy aparatu AKS, aparatu AKS lub strażnika v3, Utwórz [nowy problem](https://github.com/Azure/aks-engine/issues/new/choose) w repozytorium GITHUB aparatu AKS.

## <a name="overview"></a>Omówienie

Aby włączyć i używać Azure Policy dla aparatu AKS z własnym zarządzanym klastrem Kubernetes na platformie Azure, wykonaj następujące czynności:

- [Wymagania wstępne](#prerequisites)
- [Instalowanie dodatku Azure Policy](#installing-the-add-on)
- [Przypisywanie definicji zasad dla aparatu AKS](#built-in-policies)
- [Zaczekaj na weryfikację](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem dodatku Azure Policy lub włączenia dowolnych funkcji usługi subskrypcja musi włączyć dostawcę zasobów **Microsoft. PolicyInsights** i utworzyć przypisanie roli dla jednostki usługi klastra. 

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

1. Tworzenie przypisania roli dla jednostki usługi klastra

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

## <a name="azure-policy-add-on"></a>Dodatek Azure Policy

_Dodatek Azure Policy_ dla Kubernetes łączy usługę Azure Policy z kontrolerem strażnika. Dodatek, który jest instalowany w przestrzeni nazw _polecenia-system_ , wprowadza następujące funkcje:

- Sprawdza przy użyciu Azure Policy do przypisań do klastra aparatu AKS
- Pobiera i instaluje Szczegóły zasad, szablony ograniczeń i ograniczenia
- Uruchamia sprawdzanie zgodności pełnego skanowania w klastrze aparatu AKS
- Szczegóły inspekcji i zgodności raportów z powrotem do Azure Policy

### <a name="installing-the-add-on"></a>Instalowanie dodatku

Po zakończeniu wymagań wstępnych można zainstalować dodatek Azure Policy. Instalacja może być wykonywana w cyklu tworzenia lub aktualizacji aparatu AKS lub jako niezależna akcja w istniejącym klastrze.

- Zainstaluj podczas procesu tworzenia lub aktualizacji

  Aby włączyć dodatek Azure Policy podczas tworzenia nowego klastra z własnym zarządzaniem lub jako aktualizacji istniejącego klastra, należy uwzględnić definicję klastra właściwości **dodatków** dla aparatu AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  Aby uzyskać więcej informacji na temat, zobacz [Definicja klastra aparatu AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Instalowanie w istniejącym klastrze z wykresami Helm

  Wykonaj następujące kroki, aby przygotować klaster i zainstalować dodatek:

  1. Zainstaluj strażnika w przestrzeni nazw _systemowej_ .

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Dodaj etykietę _kontrolki płaszczyzny_ do _polecenia-system_. Ta etykieta wyklucza inspekcję polecenia i _firmowych systemów_ i usług przez strażnika i dodatek Azure Policy.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Synchronizuj dane Kubernetes (przestrzeń nazw, pod, ruch przychodzący, usługa) z NIEPRZEZ.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Aby uzyskać więcej informacji, zobacz [nieprzez — replikowanie danych](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Dodaj repozytorium Azure Policy do Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Aby uzyskać więcej informacji, zobacz [Helm Chart-Przewodnik Szybki Start](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Zainstaluj dodatek przy użyciu wykresu Helm. Zastąp wartość `<subscriptionId>` identyfikatorem subskrypcji i `<aks engine cluster resource group>` grupą zasobów, w której znajduje się klaster KUBERNETES aparatu AKS.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Aby uzyskać więcej informacji na temat instalacji wykresu Helm dodatku, [Azure Policy Zobacz definicję wykresu Helm dodatku](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) w witrynie GitHub.

     > [!NOTE]
     > Ze względu na relacje między dodatkiem Azure Policy i identyfikatorem grupy zasobów Azure Policy obsługuje tylko jeden klaster aparatu AKS dla każdej grupy zasobów.

Aby sprawdzić, czy instalacja dodatku zakończyła się pomyślnie i czy _zasady na platformie Azure_ są uruchomione, uruchom następujące polecenie:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Częstotliwość sprawdzania poprawności i raportowania

Dodatek jest sprawdzany przy użyciu Azure Policy do zmian przypisań zasad co 5 minut. W trakcie tego cyklu odświeżania dodatek sprawdza zmiany. Te zmiany wyzwalają tworzenie, aktualizowanie lub usuwanie szablonów ograniczeń i ograniczeń.

> [!NOTE]
> _Administrator klastra_ może mieć uprawnienia do wprowadzania zmian w szablonach ograniczeń i ograniczeniach, ale nie jest to zalecane ani obsługiwane w celu wprowadzania zmian w szablonach ograniczeń ani ograniczeniach utworzonych przez Azure Policy. Wszelkie zmiany wprowadzane ręcznie są tracone podczas cyklu odświeżania.

Co 5 minut, dodatek wywołuje pełne skanowanie klastra. Po zebraniu szczegółowych informacji o pełnym skanowaniu i wszystkich ocenach w czasie rzeczywistym przez strażnika podjętych zmian w klastrze dodatek zgłasza wyniki z powrotem do Azure Policy w celu uwzględnienia informacji o [zgodności](../how-to/get-compliance-data.md) , takich jak wszystkie Azure Policy przypisania. W cyklu inspekcji są zwracane tylko wyniki aktywnych przypisań zasad. Wyniki inspekcji mogą być również widoczne jako naruszenia wymienione w polu Stan niepowodzenia ograniczenia.

## <a name="policy-language"></a>Język zasad

Struktura języka Azure Policy zarządzania aparatem AKS jest zgodna z istniejącymi zasadami. Efekt _EnforceOPAConstraint_ służy do zarządzania klastrami aparatu AKS i zawiera _szczegółowe_ właściwości specyficzne dla pracy z [platformą ograniczeń nieprzez](https://github.com/open-policy-agent/frameworks/tree/master/constraint) i strażnik v3. Aby uzyskać szczegółowe informacje i przykłady, zobacz efekt [EnforceOPAConstraint](effects.md#enforceopaconstraint) .

W ramach właściwości _szczegóły. constraintTemplate_ i _szczegóły. ograniczenia_ w definicji zasad Azure Policy przekazuje identyfikatory URI tych [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) do dodatku. Rego to język, w którym NIEPRZEZ i strażnik obsługuje walidację żądania do klastra Kubernetes. Dzięki obsłudze istniejącej normy Kubernetes Management Azure Policy umożliwia ponowne użycie istniejących reguł i sparowanie ich z Azure Policy na potrzeby ujednoliconego środowiska raportowania zgodności z chmurą. Aby uzyskać więcej informacji, zobacz [co to jest rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Wbudowane zasady

Aby znaleźć wbudowane zasady zarządzania klastrem aparatu AKS przy użyciu Azure Portal, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w Azure Portal. W lewym okienku wybierz pozycję **wszystkie usługi** , a następnie wyszukaj i wybierz pozycję **zasady**.

1. W lewym okienku strony Azure Policy wybierz pozycję **definicje**.

1. W polu listy rozwijanej Kategoria Użyj **opcji Zaznacz wszystko** , aby wyczyścić filtr, a następnie wybierz pozycję **Kubernetes**.

1. Wybierz definicję zasad, a następnie wybierz przycisk **Przypisz** .

> [!NOTE]
> Podczas przypisywania Azure Policy do definicji aparatu AKS **zakres** musi być grupą zasobów klastra aparatu AKS.

Alternatywnie, można [użyć zasad programu do szybkiego](../assign-policy-portal.md) startu i przypisania aparatu AKS. Wyszukaj definicję zasad aparatu AKS zamiast przykładowej "Inspekcja maszyn wirtualnych".

> [!IMPORTANT]
> Wbudowane zasady w kategorii **Kubernetes** są przeznaczone tylko do użytku z aparatem AKS.

## <a name="logging"></a>Rejestrowanie

### <a name="azure-policy-add-on-logs"></a>Azure Policy dzienniki dodatków

Jako kontroler Kubernetes/kontener, dodatek Azure Policy przechowuje dzienniki w klastrze aparatu AKS.

Aby wyświetlić Azure Policy dzienniki dodatków, użyj `kubectl` :

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Dzienniki strażnika

Strażnika pod, _strażnik-kontroler-Manager-0_, zazwyczaj znajduje się w `gatekeeper-system` `kube-system` przestrzeni nazw lub, ale może znajdować się w innej przestrzeni nazw w zależności od sposobu jego wdrożenia.

Aby wyświetlić dzienniki strażnika, użyj `kubectl` :

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Aby uzyskać więcej informacji, zobacz [debugowanie strażnika](https://github.com/open-policy-agent/gatekeeper#debugging) w dokumentacji nieprzez.

## <a name="remove-the-add-on"></a>Usuń dodatek

Aby usunąć dodatek Azure Policy i strażnika z klastra aparatu AKS, użyj metody, która jest wyrównana do sposobu instalacji dodatku:

- Jeśli jest zainstalowany, należy ustawić właściwość **Dodatki** w definicji klastra dla aparatu AKS:

  Wdróż ponownie definicję klastra w aparacie AKS po zmianie właściwości **Dodatki** dla _usługi Azure-Policy_ na wartość false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Jeśli program został zainstalowany z wykresami Helm:

  1. Usuń stare ograniczenia

     Obecnie mechanizm odinstalowywania usuwa jedynie system strażnika, nie usuwa żadnych zasobów _ConstraintTemplate_, _ograniczeń_ani _konfiguracji_ , które zostały utworzone przez użytkownika, ani nie usuwa ich towarzyszącego _CRDs_.

     Gdy strażnik jest uruchomiony, można usunąć niepożądane ograniczenia przez:

     - Usuwanie wszystkich wystąpień zasobu ograniczenia
     - Usuwanie zasobu _ConstraintTemplate_ , który powinien automatycznie czyścić _CRD_
     - Usunięcie zasobu _konfiguracji_ spowoduje usunięcie finalizatorów z synchronizowanych zasobów

  1. Odinstaluj dodatek Azure Policy
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Odinstaluj strażnika
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dane diagnostyczne zbierane przez Azure Policy dodatek

Dodatek Azure Policy dla Kubernetes zbiera ograniczone dane diagnostyczne klastra. Te dane diagnostyczne są istotnymi danymi technicznymi związanymi z oprogramowaniem i wydajnością. Jest on używany w następujący sposób:

- Zachowaj aktualność Azure Policy
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