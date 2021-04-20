---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać problemy z tworzeniem definicji zasad, różnymi zestawami SDK i dodatkim dla rozwiązania Kubernetes.
ms.date: 04/19/2021
ms.topic: troubleshooting
ms.openlocfilehash: c4feae11c6d8d78a43bae9882405e292a18e90bd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725066"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Rozwiązywanie problemów z błędami podczas korzystania z Azure Policy

Podczas tworzenia definicji zasad, pracy z zestawami SDK lub Azure Policy dla dodatku [Kubernetes](../concepts/policy-for-kubernetes.md) mogą wystąpić błędy. W tym artykule opisano różne ogólne błędy, które mogą wystąpić, oraz zasugerowano sposoby ich usunięcia.

## <a name="find-error-details"></a>Znajdowanie szczegółów błędu

Lokalizacja szczegółów błędu zależy od tego, z jakim aspektem Azure Policy, z którym pracujesz.

- Jeśli pracujesz z zasadami niestandardowymi, przejdź do Azure Portal, aby uzyskać informacje zwrotne dotyczące [](../how-to/get-compliance-data.md) schematu z pytaniami lub przejrzyj wynikowe dane zgodności, aby zobaczyć, jak zasoby zostały ocenione.
- Jeśli pracujesz z dowolnym z różnych zestawów SDK, zestaw SDK zawiera szczegółowe informacje o tym, dlaczego funkcja zakończyła się niepowodzeniem.
- Jeśli pracujesz z dodatku dla usługi Kubernetes, zacznij od [rejestrowania](../concepts/policy-for-kubernetes.md#logging) w klastrze.

## <a name="general-errors"></a>Ogólne błędy

### <a name="scenario-alias-not-found"></a>Scenariusz: Nie znaleziono aliasu

#### <a name="issue"></a>Problem

W definicji zasad jest używany nieprawidłowy lub nieistniejący alias. Azure Policy używa [aliasów do](../concepts/definition-structure.md#aliases) mapowania na Azure Resource Manager właściwości.

#### <a name="cause"></a>Przyczyna

W definicji zasad jest używany nieprawidłowy lub nieistniejący alias.

#### <a name="resolution"></a>Rozwiązanie

Najpierw sprawdź, czy Resource Manager ma alias. Aby sprawdzić dostępne aliasy, przejdź do [Azure Policy dla Visual Studio Code](../how-to/extension-for-vscode.md) lub zestawu SDK.
Jeśli alias właściwości Resource Manager nie istnieje, utwórz bilet pomocy technicznej.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Scenariusz: Szczegóły oceny nie są aktualne

#### <a name="issue"></a>Problem

Zasób jest w stanie _Nie uruchomiliśmy_ lub szczegóły zgodności nie są aktualne.

#### <a name="cause"></a>Przyczyna

Zastosowanie nowych zasad lub przypisania inicjatywy trwa około 30 minut. Nowe lub zaktualizowane zasoby w zakresie istniejącego przypisania staną się dostępne w ciągu około 15 minut. Standardowe skanowanie zgodności odbywa się co 24 godziny. Aby uzyskać więcej informacji, zobacz [wyzwalacze oceny](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Rozwiązanie

Najpierw zaczekaj odpowiednią ilość czasu na zakończenie oceny, a wyniki zgodności staną się dostępne w Azure Portal lub zestawie SDK. Aby rozpocząć nowe skanowanie ewaluacyjnie przy użyciu Azure PowerShell api REST, zobacz [Skanowanie ewaluacyjnie na żądanie.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)

### <a name="scenario-compliance-isnt-as-expected"></a>Scenariusz: Zgodność nie jest zgodnie z oczekiwaniami

#### <a name="issue"></a>Problem

Zasób nie jest w stanie  oceny Zgodne _lub_ Niezgodne oczekiwanym dla zasobu.

#### <a name="cause"></a>Przyczyna

Zasób nie znajduje się w prawidłowym zakresie przypisania zasad lub definicja zasad nie działa zgodnie z założeniami.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać problemy z definicją zasad, wykonaj następujące czynności:

1. Najpierw zaczekaj odpowiednią ilość czasu na zakończenie oceny, a wyniki zgodności staną się dostępne w Azure Portal sdk. 

1. Aby rozpocząć nowe skanowanie ewaluacyjnie przy użyciu Azure PowerShell api REST, zobacz [Skanowanie ewaluacyjnie na żądanie.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)
1. Upewnij się, że parametry przypisania i zakres przypisania są prawidłowo ustawione.
1. Sprawdź [tryb definicji zasad](../concepts/definition-structure.md#mode):
   - Tryb powinien być dla `all` wszystkich typów zasobów.
   - Tryb powinien być `indexed` wtedy, gdy definicja zasad sprawdza tagi lub lokalizację.
1. Upewnij się, że zakres zasobu nie jest wykluczony [ani](../concepts/assignment-structure.md#excluded-scopes) [wykluczony.](../concepts/exemption-structure.md)
1. Jeśli zgodność przypisania zasad pokazuje zasoby, nie określono żadnych zasobów jako `0/0` mających zastosowanie w zakresie przypisania. Sprawdź zarówno definicję zasad, jak i zakres przypisania.
1. W przypadku niezgodnego zasobu, który powinien być zgodny, zobacz ustalanie przyczyn braku [zgodności.](../how-to/determine-non-compliance.md) Porównanie definicji z wartością ocenianej właściwości wskazuje, dlaczego zasób był niezgodne.
   - Jeśli wartość **docelowa** jest nieprawidłowa, popraw definicję zasad.
   - Jeśli **bieżąca wartość jest** nieprawidłowa, zweryfikuj ładunek zasobu za pomocą `resources.azure.com` .
1. Aby uzyskać informacje o innych typowych problemach i rozwiązaniach, zobacz [Rozwiązywanie problemów: Wymuszanie nie jest zgodnie z oczekiwaniami.](#scenario-enforcement-not-as-expected)

Jeśli nadal występuje problem ze zduplikowaną i dostosowaną wbudowaną definicją zasad  lub definicją niestandardową, utwórz bilet pomocy technicznej w obszarze Tworzenie zasad, aby prawidłowo rozsyłać problem.

### <a name="scenario-enforcement-not-as-expected"></a>Scenariusz: Wymuszanie nie jest zgodnie z oczekiwaniami

#### <a name="issue"></a>Problem

Zasób, na Azure Policy na który ma działać użytkownik, nie jest w trakcie działania i nie ma wpisu w dzienniku [aktywności platformy Azure.](../../../azure-monitor/essentials/platform-logs-overview.md)

#### <a name="cause"></a>Przyczyna

Przypisanie zasad zostało skonfigurowane dla ustawienia [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode) _Wyłączone._
Mimo **że tryb enforcementMode** jest wyłączony, efekt zasad nie jest wymuszany i w dzienniku aktywności nie ma wpisu.

#### <a name="resolution"></a>Rozwiązanie

Rozwiąż problemy z wymuszaniem przypisania zasad, wykonując następujące czynności:

1. Najpierw zaczekaj odpowiednią ilość czasu na zakończenie oceny, a wyniki zgodności staną się dostępne w Azure Portal lub zestawie SDK. 

1. Aby rozpocząć nowe skanowanie ewaluacyjną za pomocą Azure PowerShell lub interfejsu API REST, zobacz [Skanowanie oceny na żądanie](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Upewnij się, że parametry przypisania i zakres przypisania są ustawione prawidłowo oraz że **parametr enforcementMode jest** _włączony._
1. Sprawdź [tryb definicji zasad](../concepts/definition-structure.md#mode):
   - Tryb powinien być dla `all` wszystkich typów zasobów.
   - Tryb powinien być `indexed` wtedy, gdy definicja zasad sprawdza tagi lub lokalizację.
1. Upewnij się, że zakres zasobu nie jest [wykluczony ani](../concepts/assignment-structure.md#excluded-scopes) [wykluczony.](../concepts/exemption-structure.md)
1. Sprawdź, czy ładunek zasobu jest taki, jak logika zasad. Można to zrobić, [przechwytując](../../../azure-portal/capture-browser-trace.md) ślad archiwum HTTP (HAR) lub przeglądając właściwości Azure Resource Manager szablonu (szablonu USŁUGI ARM).
1. Aby uzyskać informacje o innych typowych problemach i rozwiązaniach, zobacz [Rozwiązywanie problemów: Zgodność nie jest zgodnie z oczekiwaniami.](#scenario-compliance-isnt-as-expected)

Jeśli nadal występuje problem ze zduplikowaną i dostosowaną wbudowaną definicją zasad  lub definicją niestandardową, utwórz bilet pomocy technicznej w obszarze Tworzenie zasad, aby prawidłowo rozsyłać problem.

### <a name="scenario-denied-by-azure-policy"></a>Scenariusz: odmowa przez Azure Policy

#### <a name="issue"></a>Problem

Tworzenie lub aktualizowanie zasobu jest odrzucone.

#### <a name="cause"></a>Przyczyna

Przypisanie zasad do zakresu nowego lub zaktualizowanego zasobu spełnia kryteria definicji zasad z [efektem Odmów.](../concepts/effects.md#deny) Nie można tworzyć ani aktualizować zasobów spełniających te definicje.

#### <a name="resolution"></a>Rozwiązanie

Komunikat o błędzie z przypisania zasad odmowy zawiera definicję zasad i identyfikatory przypisania zasad. W przypadku pominięcia informacji o błędzie w komunikacie są one również dostępne w [dzienniku aktywności](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log). Użyj tych informacji, aby uzyskać więcej szczegółów, aby zrozumieć ograniczenia zasobów i dostosować właściwości zasobów w żądaniu, aby dopasować je do dozwolonych wartości.

### <a name="scenario-definition-targets-multiple-resource-types"></a>Scenariusz: Definicja dotyczy wielu typów zasobów

#### <a name="issue"></a>Problem

Definicja zasad, która zawiera wiele typów zasobów, kończy się niepowodzeniem podczas tworzenia lub aktualizowania z następującym błędem:

```error
The policy definition '{0}' targets multiple resource types, but the policy rule is authored in a way that makes the policy not applicable to the target resource types '{1}'.
```

#### <a name="cause"></a>Przyczyna

Reguła definicji zasad ma co najmniej jeden warunki, które nie są oceniane przez docelowe typy zasobów.

#### <a name="resolution"></a>Rozwiązanie

Jeśli jest używany alias, upewnij się, że alias jest oceniany tylko pod względem typu zasobu, do którego należy, dodając warunek typu przed nim. Alternatywnie można podzielić definicję zasad na wiele definicji, aby uniknąć określania wielu typów zasobów jako docelowych.

## <a name="template-errors"></a>Błędy szablonu

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenariusz: Funkcje obsługiwane przez zasady przetwarzane przez szablon

#### <a name="issue"></a>Problem

Azure Policy obsługuje wiele funkcji szablonu usługi ARM i funkcji, które są dostępne tylko w definicji zasad. Resource Manager przetwarza te funkcje jako część wdrożenia, a nie jako część definicji zasad.

#### <a name="cause"></a>Przyczyna

Użycie obsługiwanych funkcji, takich jak lub , powoduje przetworzenie wyniku funkcji w czasie wdrażania, zamiast zezwalać funkcji na przetwarzanie definicji zasad `parameter()` `resourceGroup()` i Azure Policy aparatu.

#### <a name="resolution"></a>Rozwiązanie

Aby przekazać funkcję w ramach definicji zasad, należy wymykać cały ciąg w taki sposób, aby `[` właściwość wyglądała następująco: `[[resourceGroup().tags.myTag]` . Znak ucieczki Resource Manager traktować wartość jako ciąg podczas przetwarzania szablonu. Azure Policy następnie umieszcza funkcję w definicji zasad, co umożliwia jej dynamiczne działanie zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz [Składnia i wyrażenia w Azure Resource Manager szablonów .](../../../azure-resource-manager/templates/template-expressions.md)

## <a name="add-on-for-kubernetes-installation-errors"></a>Dodatek dla błędów instalacji kubernetes

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Scenariusz: Instalacja przy użyciu pakietu Helm kończy się niepowodzeniem z powodu błędu hasła

#### <a name="issue"></a>Problem

Polecenie `helm install azure-policy-addon` kończy się niepowodzeniem i zwraca jeden z następujących błędów:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Przyczyna

Wygenerowane hasło zawiera przecinek (), na którym jest rozdzielany `,` wykres Helm.

#### <a name="resolution"></a>Rozwiązanie

Po uruchomieniu w wartości hasła należy zmienić przecinek ( ) na `helm install azure-policy-addon` `,` ukośnik odwrotny ( `\` ).

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Scenariusz: Instalacja przy użyciu pakietu Helm kończy się niepowodzeniem, ponieważ nazwa już istnieje

#### <a name="issue"></a>Problem

Polecenie `helm install azure-policy-addon` kończy się niepowodzeniem i zwraca następujący błąd:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Przyczyna

Pakiet Helm o nazwie został już zainstalowany lub `azure-policy-addon` częściowo zainstalowany.

#### <a name="resolution"></a>Rozwiązanie

Postępuj zgodnie z [instrukcjami, aby Azure Policy dla dodatku Kubernetes,](../concepts/policy-for-kubernetes.md#remove-the-add-on)a następnie ponownie uruchomić `helm install azure-policy-addon` polecenie.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenariusz: tożsamości przypisane przez użytkownika maszyny wirtualnej platformy Azure są zastępowane przez tożsamości zarządzane przypisane przez system

#### <a name="issue"></a>Problem

Po przypisaniu inicjatyw zasad konfiguracji gościa w celu inspekcji ustawień wewnątrz maszyny tożsamości zarządzane przypisane przez użytkownika, które zostały przypisane do maszyny, nie są już przypisywane. Przypisywana jest tylko tożsamość zarządzana przypisana przez system.

#### <a name="cause"></a>Przyczyna

Definicje zasad, które były wcześniej używane w definicjach DeployIfNotExists konfiguracji gościa, upewniły się, że do maszyny przypisano tożsamość przypisaną przez system, ale również usunięto przypisania tożsamości przypisane przez użytkownika.

#### <a name="resolution"></a>Rozwiązanie

Definicje, które wcześniej powodowały ten problem, są wyświetlane jako _\[ Przestarzałe \]_ i są zastępowane definicjami zasad, które zarządzają wymagami wstępnymi bez usuwania tożsamości zarządzanych przypisanych przez użytkownika. Wymagany jest krok ręczny. Usuń wszystkie istniejące przypisania zasad, które są oznaczone jako _\[ \] Przestarzałe,_ i zastąp je zaktualizowaną inicjatywą zasad wymagań wstępnych i definicjami zasad, które mają taką samą nazwę jak oryginalna.

Aby uzyskać szczegółowy opis, zobacz wpis w blogu Important change released for Guest Configuration audit policies (Ważna zmiana wydana dla zasad inspekcji [konfiguracji gościa).](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Dodatek dla ogólnych błędów kubernetes

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Scenariusz: Dodatek nie może uzyskać dostępu do punktu końcowego Azure Policy z powodu ograniczeń ruchu wychodzącego

#### <a name="issue"></a>Problem

Dodatek nie może uzyskać połączenia z punktem końcowym Azure Policy i zwraca jeden z następujących błędów:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Przyczyna

Ten problem występuje, gdy ruch wychodzący klastra jest zablokowany.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że domeny i porty wymienione w następujących artykułach są otwarte:

- [Wymagane reguły sieciowego ruchu wychodzącego i w pełni kwalifikowane nazwy domen (FQNS) dla klastrów usługi AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Instalowanie Azure Policy dla usługi Kubernetes z włączoną obsługą Azure Arc (wersja zapoznawcza)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Scenariusz: Dodatek nie może uzyskać dostępu do punktu końcowego usługi Azure Policy z powodu konfiguracji aad-pod-identity

#### <a name="issue"></a>Problem

Dodatek nie może uzyskać połączenia z punktem końcowym Azure Policy i zwraca jeden z następujących błędów:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy w klastrze jest zainstalowana tożsamość _add-pod-identity,_ a zasobniki _kube-system_ nie są wykluczone w _aad-pod-identity_.

Zasobniki _składnika aad-pod-identity_ Tożsamość zarządzana na węźle (NMI) modyfikują tabelę iptable węzłów w celu przechwycenia wywołań do punktu końcowego metadanych wystąpienia platformy Azure. Ta konfiguracja oznacza, że wszystkie żądania do punktu końcowego metadanych są przechwytywane przez NMI, nawet jeśli zasobnik nie używa tożsamości _aad-pod-identity_.
Można skonfigurować definicję CustomResourceDefinition (CRD) elementu _AzurePodIdentityException_ w celu poinformowania elementu _aad-pod-identity,_ że wszelkie żądania do punktu końcowego metadanych pochodzące z zasobnika zgodne z etykietami zdefiniowanymi w crd powinny być proxied bez przetwarzania w usłudze NMI.

#### <a name="resolution"></a>Rozwiązanie

Wyklucz zasobniki systemowe, które mają etykietę w przestrzeni nazw `kubernetes.azure.com/managedby: aks` _kube-system_ w _aad-pod-identity,_ konfigurując identyfikator CRD _AzurePodIdentityException._

Aby uzyskać więcej informacji, zobacz [Disable the Azure Active Directory (Azure AD) pod identity for a specific pod/application (Wyłączanie](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)tożsamości zasobnika usługi Azure AD dla określonego zasobnika/aplikacji).

Aby skonfigurować wyjątek, postępuj zgodnie z poniższym przykładem:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Scenariusz: Dostawca zasobów nie jest zarejestrowany

#### <a name="issue"></a>Problem

Dodatek może dotrzeć do punktu końcowego Azure Policy, ale w dziennikach dodatku jest wyświetlany jeden z następujących błędów:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Przyczyna

Dostawca zasobów "Microsoft.PolicyInsights" nie jest zarejestrowany. Musi być zarejestrowany dla dodatku, aby uzyskać definicje zasad i zwrócić dane zgodności.

#### <a name="resolution"></a>Rozwiązanie

Zarejestruj dostawcę zasobów "Microsoft.PolicyInsights" w subskrypcji klastra. Aby uzyskać instrukcje, [zobacz Register a resource provider (Rejestrowanie dostawcy zasobów).](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

### <a name="scenario-the-subscription-is-disabled"></a>Scenariusz: subskrypcja jest wyłączona

#### <a name="issue"></a>Problem

Dodatek może na przykład uzyskać Azure Policy punktu końcowego usługi, ale wyświetlany jest następujący błąd:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Przyczyna

Ten błąd oznacza, że subskrypcja została określona jako problematyczna, a flaga funkcji została dodana w `Microsoft.PolicyInsights/DataPlaneBlocked` celu zablokowania subskrypcji.

#### <a name="resolution"></a>Rozwiązanie

Aby zbadać i rozwiązać ten problem, [skontaktuj się z zespołem funkcji](mailto:azuredg@microsoft.com).

## <a name="next-steps"></a>Następne kroki

Jeśli twojego problemu nie ma na liście w tym artykule lub nie możesz go rozwiązać, uzyskaj pomoc techniczną, odwiedzając jeden z następujących kanałów:

- Uzyskaj odpowiedzi od ekspertów za [pośrednictwem usługi Microsoft Q&A.](/answers/topics/azure-policy.html)
- Połącz się [@AzureSupport](https://twitter.com/azuresupport) z . Ten oficjalny Microsoft Azure w serwisie Twitter pomaga ulepszyć środowisko klienta, łącząc społeczność platformy Azure z właściwymi odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli nadal potrzebujesz pomocy, przejdź do witryny pomocy technicznej [platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Prześlij wniosek o pomoc techniczną.**
