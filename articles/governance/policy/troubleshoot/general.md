---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać problemy z tworzeniem definicji zasad, różnymi zestawami SDK i dodatkiem dla Kubernetes.
ms.date: 10/30/2020
ms.topic: troubleshooting
ms.openlocfilehash: 74b622dd41fb28e845a35780e5d06588189ec029
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146283"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Rozwiązywanie problemów z błędami przy użyciu Azure Policy

Podczas tworzenia definicji zasad, pracy z zestawem SDK lub konfigurowania [Azure Policy dla dodatku Kubernetes](../concepts/policy-for-kubernetes.md) można napotkać błędy. W tym artykule opisano różne ogólne błędy, które mogą wystąpić i sposoby ich rozwiązywania.

## <a name="finding-error-details"></a>Znajdowanie szczegółów błędu

Lokalizacja szczegółów błędu zależy od akcji, która powoduje błąd.

- Podczas pracy z zasadami niestandardowymi Wypróbuj ją w Azure Portal, aby uzyskać Zaznaczanie błędów opinię na temat schematu lub przejrzeć wynikowe [dane zgodności](../how-to/get-compliance-data.md) , aby zobaczyć, jak są oceniane zasoby.
- Podczas pracy z różnymi zestawami SDK zestaw SDK zawiera szczegółowe informacje o tym, dlaczego działanie funkcji nie powiodło się.
- Podczas pracy z dodatkiem dla Kubernetes Rozpocznij od [rejestrowania](../concepts/policy-for-kubernetes.md#logging) w klastrze.

## <a name="general-errors"></a>Błędy ogólne

### <a name="scenario-alias-not-found"></a>Scenariusz: nie znaleziono aliasu

#### <a name="issue"></a>Problem

Azure Policy używa [aliasów](../concepts/definition-structure.md#aliases) do mapowania na Azure Resource Manager właściwości.

#### <a name="cause"></a>Przyczyna

W definicji zasad użyto nieprawidłowego lub nieistniejącego aliasu.

#### <a name="resolution"></a>Rozwiązanie

Najpierw sprawdź, czy właściwość Menedżer zasobów ma alias. Użyj [rozszerzenia Azure Policy dla Visual Studio Code](../how-to/extension-for-vscode.md), [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)lub SDK, aby wyszukać dostępne aliasy. Jeśli alias dla właściwości Menedżer zasobów nie istnieje, Utwórz bilet pomocy technicznej.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Scenariusz: Szczegóły oceny nieaktualne

#### <a name="issue"></a>Problem

Zasób jest w stanie "nie uruchomiono" lub szczegóły zgodności nie są aktualne.

#### <a name="cause"></a>Przyczyna

Zastosowanie nowych zasad lub przydziału inicjatywy trwa około 30 minut. Nowe lub zaktualizowane zasoby w ramach istniejącego przypisania stają się dostępne około 15 minut później. Standardowe skanowanie pod kątem zgodności odbywa się co 24 godziny. Aby uzyskać więcej informacji, zobacz [wyzwalacze oceny](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Rozwiązanie

Najpierw poczekaj na ukończenie odpowiedniej ilości czasu, aby Ocena została ukończona, a wyniki zgodności staną się dostępne w Azure Portal lub SDK. Aby rozpocząć nowe skanowanie w celu oceny przy użyciu Azure PowerShell lub interfejsu API REST, zobacz [skanowanie na żądanie](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-not-as-expected"></a>Scenariusz: zgodność nie jest zgodna z oczekiwaniami

#### <a name="issue"></a>Problem

Zasób nie jest w stanie oceny, jest _zgodny_ lub _niezgodny_ , oczekiwany dla tego zasobu.

#### <a name="cause"></a>Przyczyna

Zasób nie jest w poprawnym zakresie przypisania zasad lub definicja zasad nie działa zgodnie z oczekiwaniami.

#### <a name="resolution"></a>Rozwiązanie

Wykonaj następujące kroki, aby rozwiązać problemy z definicją zasad:

1. Najpierw poczekaj na ukończenie odpowiedniej ilości czasu, aby Ocena została ukończona, a wyniki zgodności staną się dostępne w Azure Portal lub SDK. Aby rozpocząć nowe skanowanie w celu oceny przy użyciu Azure PowerShell lub interfejsu API REST, zobacz [skanowanie na żądanie](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Sprawdź, czy parametry przypisania i zakres przypisania są ustawione prawidłowo.
1. Sprawdź [tryb definicji zasad](../concepts/definition-structure.md#mode):
   - Tryb "All" dla wszystkich typów zasobów.
   - Tryb "Indexed", jeśli definicja zasad sprawdza pod kątem tagów lub lokalizacji.
1. Sprawdź [, czy zakres](../concepts/exemption-structure.md)zasobu nie jest [wykluczony](../concepts/assignment-structure.md#excluded-scopes) ani wykluczony.
1. Jeśli zgodność z przypisaniem zasad zawiera `0/0` zasoby, nie zostały określone żadne zasoby, które mają zastosowanie w zakresie przypisania. Sprawdź zarówno definicję zasad, jak i zakres przypisania.
1. W przypadku niezgodnego zasobu, który powinien być zgodny, sprawdź [określenie przyczyn braku zgodności](../how-to/determine-non-compliance.md). Porównanie definicji z obliczoną wartością właściwości wskazuje, dlaczego zasób nie jest zgodny.
   - Jeśli **wartość docelowa** jest nieprawidłowa, Popraw definicję zasad.
   - Jeśli **Bieżąca wartość** jest niepoprawna, zweryfikuj ładunek zasobu za pomocą `resources.azure.com` .
1. Sprawdź [Rozwiązywanie problemów: wymuszanie nie jest zgodne z oczekiwaniami](#scenario-enforcement-not-as-expected) w przypadku innych typowych problemów i rozwiązań.

Jeśli nadal masz problem z zduplikowaną i dostosowaną definicją zasad lub definicją niestandardową, Utwórz bilet pomocy technicznej w obszarze **Tworzenie zasad** w celu poprawnego kierowania problemu.

### <a name="scenario-enforcement-not-as-expected"></a>Scenariusz: wymuszanie nie jest zgodne z oczekiwaniami

#### <a name="issue"></a>Problem

Zasób, na który oczekuje się, Azure Policy nie jest i nie ma wpisu w [dzienniku aktywności platformy Azure](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Przyczyna

Przypisanie zasad zostało skonfigurowane do [wymuszania](../concepts/assignment-structure.md#enforcement-mode) _wyłączenia_ . Gdy tryb wymuszania jest wyłączony, efekt zasad nie jest wymuszany, a w dzienniku aktywności nie ma wpisu.

#### <a name="resolution"></a>Rozwiązanie

Wykonaj następujące kroki, aby rozwiązać problemy z wymuszeniem przypisania zasad:

1. Najpierw poczekaj na ukończenie odpowiedniej ilości czasu, aby Ocena została ukończona, a wyniki zgodności staną się dostępne w Azure Portal lub SDK. Aby rozpocząć nowe skanowanie w celu oceny przy użyciu Azure PowerShell lub interfejsu API REST, zobacz [skanowanie na żądanie](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Sprawdź, czy parametry przypisania i zakres przypisania są ustawione prawidłowo i czy **wymuszanie** jest _włączone_ . 
1. Sprawdź [tryb definicji zasad](../concepts/definition-structure.md#mode):
   - Tryb "All" dla wszystkich typów zasobów.
   - Tryb "Indexed", jeśli definicja zasad sprawdza pod kątem tagów lub lokalizacji.
1. Sprawdź [, czy zakres](../concepts/exemption-structure.md)zasobu nie jest [wykluczony](../concepts/assignment-structure.md#excluded-scopes) ani wykluczony.
1. Sprawdź, czy ładunek zasobu jest zgodny z logiką zasad. Można to zrobić przez [przechwycenie śledzenia Har](../../../azure-portal/capture-browser-trace.md) lub przejrzenie właściwości szablonu ARM.
1. Sprawdź [Rozwiązywanie problemów: zgodność nie jest zgodna z oczekiwaniami](#scenario-compliance-not-as-expected) w przypadku innych typowych problemów i rozwiązań.

Jeśli nadal masz problem z zduplikowaną i dostosowaną definicją zasad lub definicją niestandardową, Utwórz bilet pomocy technicznej w obszarze **Tworzenie zasad** w celu poprawnego kierowania problemu.

### <a name="scenario-denied-by-azure-policy"></a>Scenariusz: odmowa przez Azure Policy

#### <a name="issue"></a>Problem

Odmowa utworzenia lub zaktualizowania zasobu.

#### <a name="cause"></a>Przyczyna

Przypisanie zasad do zakresu, w którym znajduje się nowy lub zaktualizowany zasób, spełnia kryteria definicji zasad z efektem [odmowy](../concepts/effects.md#deny) . Brak tworzenia lub aktualizowania zasobów spotkań z tymi definicjami.

#### <a name="resolution"></a>Rozwiązanie

Komunikat o błędzie z przypisania zasad Odmów obejmuje definicję zasad i identyfikatory przypisań zasad. Jeśli informacje o błędzie w wiadomości nie zostaną pominięte, są również dostępne w [dzienniku aktywności](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Korzystając z tych informacji, można uzyskać więcej szczegółowych informacji, aby zrozumieć ograniczenia zasobów i dostosować właściwości zasobów w żądaniu w celu dopasowania do wartości dozwolonych.

## <a name="template-errors"></a>Błędy szablonów

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenariusz: obsługiwane przez zasady funkcje przetwarzane przez szablon

#### <a name="issue"></a>Problem

Azure Policy obsługuje kilka funkcji i funkcji szablonu Azure Resource Manager (szablon ARM), które są dostępne tylko w definicji zasad. Menedżer zasobów przetwarza te funkcje jako część wdrożenia, a nie jako część definicji zasad.

#### <a name="cause"></a>Przyczyna

Używanie obsługiwanych funkcji, takich jak `parameter()` lub `resourceGroup()` , skutkuje przetworzonym wynikiem funkcji w czasie wdrażania zamiast opuszczania funkcji dla definicji zasad i aparatu Azure Policy do przetworzenia.

#### <a name="resolution"></a>Rozwiązanie

Aby przekazać funkcję w ramach definicji zasad, należy wypróbować cały ciąg, `[` tak aby Właściwość wyglądała tak, jak wygląda `[[resourceGroup().tags.myTag]` . Znak ucieczki powoduje, Menedżer zasobów traktować wartość jako ciąg podczas przetwarzania szablonu. Azure Policy następnie umieści funkcję w definicji zasad, umożliwiając jej dynamiczne działanie zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz [składnia i wyrażenia w szablonach Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Dodatek dla błędów instalacji Kubernetes

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Scenariusz: Instalacja za pomocą wykresu Helm kończy się niepowodzeniem przy haśle

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Polecenie kończy się niepowodzeniem z jedną z następujących komunikatów:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Przyczyna

Wygenerowane hasło zawiera przecinek ( `,` ), na którym jest podzielenie wykresu Helm.

#### <a name="resolution"></a>Rozwiązanie

`,`W przypadku korzystania `helm install azure-policy-addon` z ukośnika odwrotnego () w polu wartość hasła należy wprowadzić znak ucieczki () `\` .

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Scenariusz: Instalacja za pomocą wykresu Helm kończy się niepowodzeniem, ponieważ nazwa już istnieje

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Polecenie kończy się niepowodzeniem z następującym komunikatem:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Przyczyna

Wykres Helm o nazwie `azure-policy-addon` został już zainstalowany lub częściowo zainstalowany.

#### <a name="resolution"></a>Rozwiązanie

Postępuj zgodnie z instrukcjami, aby [usunąć Azure Policy dodatku Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on), a następnie ponownie uruchom `helm install azure-policy-addon` polecenie.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenariusz: tożsamości przypisane przez użytkownika maszyn wirtualnych platformy Azure są zastępowane tożsamościami zarządzanymi przez system

#### <a name="issue"></a>Problem

Po przypisaniu inicjatyw zasad konfiguracji gościa do ustawień inspekcji wewnątrz maszyn, zarządzane tożsamości przypisane przez użytkownika przypisane do maszyny nie są już przypisane. Przypisywana jest tylko tożsamość zarządzana przypisana przez system.

#### <a name="cause"></a>Przyczyna

Definicje zasad wcześniej używane w definicjach DeployIfNotExists konfiguracji gościa zapewniają, że tożsamość przypisana przez system jest przypisana do komputera, ale również z przypisanymi przez użytkownika przypisaniami tożsamości.

#### <a name="resolution"></a>Rozwiązanie

Definicje, które wcześniej powodowały ten problem, są wyświetlane jako \[ przestarzałe \] i zastąpione przez definicje zasad, które zarządzają wymaganiami wstępnymi bez usuwania tożsamości zarządzanej przypisanej przez użytkownika. Wymagany jest krok ręczny. Usuń wszystkie istniejące przypisania zasad, które są oznaczone jako \[ przestarzałe \] , i zastąp je za pomocą zaktualizowanej inicjatywy zasad i definicji zasad, które mają taką samą nazwę jak oryginalna.

Aby uzyskać szczegółowe opisy, zobacz następujący wpis w blogu:

[Ważna zmiana dotycząca zasad inspekcji konfiguracji gościa](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Dodatek dla ogólnych błędów Kubernetes

### <a name="scenario-add-on-doesnt-work-with-aks-clusters-on-version-119-preview"></a>Scenariusz: dodatek nie działa z klastrami AKS w wersji 1,19 (wersja zapoznawcza)

#### <a name="issue"></a>Problem

Klastry w wersji 1,19 zwracają ten błąd za pośrednictwem kontrolera strażnika i elementów webhook zasad:

```
2020/09/22 20:06:55 http: TLS handshake error from 10.244.1.14:44282: remote error: tls: bad certificate
```

#### <a name="cause"></a>Przyczyna

AKS clusers w wersji 1,19 (wersja zapoznawcza) nie jest jeszcze zgodna z dodatkiem Azure Policy.

#### <a name="resolution"></a>Rozwiązanie

Unikaj używania Kubernetes 1,19 (wersja zapoznawcza) z dodatkiem Azure Policy. Dodatek może być używany z wszystkimi obsługiwanymi ogólnie dostępnymi wersjami, takimi jak 1,16, 1,17 lub 1,18.

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-egress-restrictions"></a>Scenariusz: dodatek nie może nawiązać połączenia z punktem końcowym usługi Azure Policy ze względu na ograniczenia ruchu wychodzącego

#### <a name="issue"></a>Problem

Dodatek nie może nawiązać połączenia z punktem końcowym usługi Azure Policy i zwraca jeden z następujących błędów:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Przyczyna

Te problemy występują, gdy ruch wychodzący klastra jest zablokowany.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że domeny i porty w następujących artykułach są otwarte:

- [Wymagane reguły sieci wychodzącej i nazwy FQDN dla klastrów AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Zainstaluj dodatek Azure Policy dla usługi Azure ARC z włączonym Kubernetes (wersja zapoznawcza)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-aad-pod-identity-configuration"></a>Scenariusz: dodatek nie może nawiązać połączenia z punktem końcowym usługi Azure Policy ze względu na konfigurację AAD-pod-Identity

#### <a name="issue"></a>Problem

Dodatek nie może nawiązać połączenia z punktem końcowym usługi Azure Policy i zwraca jeden z następujących błędów:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy w klastrze jest zainstalowana wartość _Add-pod-Identity_ i _polecenia —_ nie są one wykluczone w _usłudze AAD-pod-Identity_ .

Identyfikator zarządzania (NMI) w _usłudze AAD-pod-Identity_ (usługa) — Azure Site Identity — Modyfikuj węzły dołączenie iptables do przechwytywania wywołań do punktu końcowego metadanych wystąpienia platformy Azure. Ta konfiguracja oznacza, że wszystkie żądania wykonane w punkcie końcowym metadanych są przechwytywane przez NMI, nawet jeśli nie korzystają z usługi _AAD-pod-Identity_ .
**AzurePodIdentityException** CRD można skonfigurować w taki sposób, aby informować usługi _AAD-pod-Identity_ , że wszystkie żądania kierowane do punktów końcowych metadanych pochodzące z elementu, które pasują do etykiet zdefiniowanych w CRD, powinny być serwerem proxy bez żadnego przetwarzania w NMI.

#### <a name="resolution"></a>Rozwiązanie

Wyklucz systemowy `kubernetes.azure.com/managedby: aks` Wymiarowanie systemowe z etykietą w _polecenia — systemowa_ przestrzeń nazw w _usłudze AAD-pod-Identity-tożsamość_ przez skonfigurowanie **AzurePodIdentityException** CRD.

Aby uzyskać więcej informacji, zobacz temat [wyłączanie usługi AAD pod tożsamością dla określonego elementu/aplikacji](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Aby skonfigurować wyjątek, zobacz ten przykład:

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

### <a name="scenario-the-resource-provider-isnt-registered"></a>Scenariusz: dostawca zasobów nie jest zarejestrowany

#### <a name="issue"></a>Problem

Dodatek może nawiązać połączenie z punktem końcowym usługi Azure Policy, ale widzi następujący błąd:

```
The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See https://aka.ms/policy-register-subscription for how to register subscriptions.
```

#### <a name="cause"></a>Przyczyna

`Microsoft.PolicyInsights`Dostawca zasobów nie jest zarejestrowany i musi być zarejestrowany dla dodatku, aby można było pobrać definicje zasad i zwrócić dane zgodności.

#### <a name="resolution"></a>Rozwiązanie

Zarejestruj `Microsoft.PolicyInsights` dostawcę zasobów. Aby uzyskać instrukcje, zobacz [Rejestrowanie dostawcy zasobów](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscript-is-disabled"></a>Scenariusz: indeks dolny jest wyłączony

#### <a name="issue"></a>Problem

Dodatek może nawiązać połączenie z punktem końcowym usługi Azure Policy, ale widzi następujący błąd:

```
The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.
```

#### <a name="cause"></a>Przyczyna

Ten błąd oznacza, że subskrypcja została określona jako problematyczna i flaga funkcji `Microsoft.PolicyInsights/DataPlaneBlocked` została dodana w celu zablokowania subskrypcji.

#### <a name="resolution"></a>Rozwiązanie

Skontaktuj się z zespołem funkcji `azuredg@microsoft.com` , aby zbadać i rozwiązać ten problem. 

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów za pośrednictwem usługi [Microsoft Q&A](/answers/topics/azure-policy.html).
- Connect with [@AzureSupport](https://twitter.com/azuresupport) — oficjalne konto Microsoft Azure, aby usprawnić obsługę klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
- Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną** .
