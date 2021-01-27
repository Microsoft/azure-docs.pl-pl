---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać problemy z tworzeniem definicji zasad, różnymi zestawami SDK i dodatkiem dla Kubernetes.
ms.date: 12/01/2020
ms.topic: troubleshooting
ms.openlocfilehash: 6f31f6e6f8d24f83f44dc14112f1bdc90c8af859
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897075"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Rozwiązywanie problemów z używaniem Azure Policy

Podczas tworzenia definicji zasad, pracy z zestawami SDK lub konfigurowania [Azure Policy dla dodatku Kubernetes](../concepts/policy-for-kubernetes.md) można napotkać błędy. W tym artykule opisano różne ogólne błędy, które mogą wystąpić, i przedstawiono sposoby ich rozwiązywania.

## <a name="find-error-details"></a>Znajdź szczegóły błędu

Lokalizacja szczegółów błędu zależy od tego, jakiego aspektu Azure Policy pracujesz.

- Jeśli pracujesz z zasadami niestandardowymi, przejdź do Azure Portal, aby uzyskać informacje na temat schematu lub sprawdzić wynikowe [dane zgodności](../how-to/get-compliance-data.md) , aby zobaczyć, jak są oceniane zasoby.
- Jeśli pracujesz z dowolnym z różnych zestawów SDK, zestaw SDK zawiera szczegółowe informacje o tym, dlaczego działanie funkcji nie powiodło się.
- Jeśli pracujesz z dodatkiem do Kubernetes, Zacznij od [rejestrowania](../concepts/policy-for-kubernetes.md#logging) w klastrze.

## <a name="general-errors"></a>Błędy ogólne

### <a name="scenario-alias-not-found"></a>Scenariusz: nie znaleziono aliasu

#### <a name="issue"></a>Problem

W definicji zasad użyto nieprawidłowego lub nieistniejącego aliasu. Azure Policy używa [aliasów](../concepts/definition-structure.md#aliases) do mapowania na Azure Resource Manager właściwości.

#### <a name="cause"></a>Przyczyna

W definicji zasad użyto nieprawidłowego lub nieistniejącego aliasu.

#### <a name="resolution"></a>Rozwiązanie

Najpierw sprawdź, czy właściwość Menedżer zasobów ma alias. Aby wyszukać dostępne aliasy, przejdź do [Azure Policy rozszerzenia Visual Studio Code](../how-to/extension-for-vscode.md) lub zestawu SDK. Jeśli alias dla właściwości Menedżer zasobów nie istnieje, Utwórz bilet pomocy technicznej.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Scenariusz: Szczegóły oceny są nieaktualne

#### <a name="issue"></a>Problem

Zasób jest w stanie *nieuruchomionym* lub szczegóły zgodności nie są aktualne.

#### <a name="cause"></a>Przyczyna

Zastosowanie nowego przydziału zasad lub inicjatywy trwa około 30 minut. Nowe lub zaktualizowane zasoby z zakresu istniejącego przypisania stają się dostępne przez około 15 minut. Standardowe skanowanie zgodności odbywa się co 24 godziny. Aby uzyskać więcej informacji, zobacz [wyzwalacze oceny](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Rozwiązanie

Najpierw poczekaj na zakończenie oceny i uzyskaj wynik zgodności, aby uzyskać dostęp do Azure Portal lub zestawu SDK. Aby rozpocząć nowe skanowanie w celu przeprowadzania oceny przy użyciu Azure PowerShell lub interfejsu API REST, zobacz [skanowanie na żądanie](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-isnt-as-expected"></a>Scenariusz: zgodność nie jest zgodna z oczekiwaniami

#### <a name="issue"></a>Problem

Zasób nie jest w _zgodnym_ lub _niezgodnym_ stanie oceny oczekiwanym dla zasobu.

#### <a name="cause"></a>Przyczyna

Zasób nie jest w poprawnym zakresie przypisania zasad lub definicja zasad nie działa zgodnie z oczekiwaniami.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać problem z definicją zasad, wykonaj następujące czynności:

1. Najpierw poczekaj na zakończenie oceny i uzyskaj zgodność z wynikami, aby uzyskać dostęp do Azure Portal lub zestawu SDK. 

1. Aby rozpocząć nowe skanowanie w celu przeprowadzania oceny przy użyciu Azure PowerShell lub interfejsu API REST, zobacz [skanowanie na żądanie](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Upewnij się, że parametry przypisania i zakres przypisania są ustawione prawidłowo.
1. Sprawdź [tryb definicji zasad](../concepts/definition-structure.md#mode):
   - Tryb powinien dotyczyć `all` wszystkich typów zasobów.
   - Tryb powinien mieć miejsce, `indexed` Jeśli definicja zasad sprawdza Tagi lub lokalizację.
1. Upewnij się, że zakres zasobu nie jest [wykluczony](../concepts/assignment-structure.md#excluded-scopes) [ani](../concepts/exemption-structure.md)wykluczony.
1. Jeśli zgodność z przypisaniem zasad zawiera `0/0` zasoby, nie zostały określone żadne zasoby, które mają zastosowanie w zakresie przypisania. Sprawdź zarówno definicję zasad, jak i zakres przypisania.
1. W przypadku niezgodnego zasobu, który powinien być zgodny, zobacz [Określanie przyczyn braku zgodności](../how-to/determine-non-compliance.md). Porównanie definicji z obliczoną wartością właściwości wskazuje, dlaczego zasób nie jest zgodny.
   - Jeśli **wartość docelowa** jest nieprawidłowa, Popraw definicję zasad.
   - Jeśli **Bieżąca wartość** jest niepoprawna, zweryfikuj ładunek zasobu za pomocą `resources.azure.com` .
1. Aby poznać inne typowe problemy i rozwiązania, zobacz [Rozwiązywanie problemów: wymuszanie nie jest zgodne z oczekiwaniami](#scenario-enforcement-not-as-expected).

Jeśli nadal masz problem z zduplikowaną i dostosowaną definicją zasad lub definicją niestandardową, Utwórz bilet pomocy technicznej w obszarze **Tworzenie zasad** w celu poprawnego kierowania problemu.

### <a name="scenario-enforcement-not-as-expected"></a>Scenariusz: wymuszanie nie jest zgodne z oczekiwaniami

#### <a name="issue"></a>Problem

Zasób, którego oczekujesz, Azure Policy na działanie nie działa, a w [dzienniku aktywności platformy Azure](../../../azure-monitor/platform/platform-logs-overview.md)nie ma wpisu.

#### <a name="cause"></a>Przyczyna

Przypisanie zasad zostało skonfigurowane dla ustawienia [**wymuszania**](../concepts/assignment-structure.md#enforcement-mode) _wyłączone_. Gdy **wymuszanie** jest wyłączone, efekt zasad nie jest wymuszany, a w dzienniku aktywności nie ma wpisu.

#### <a name="resolution"></a>Rozwiązanie

Rozwiązywanie problemów z wymuszeniem przypisania zasad, wykonując następujące czynności:

1. Najpierw poczekaj na zakończenie oceny i uzyskaj zgodność, aby uzyskać dostęp do Azure Portal lub zestawu SDK. 

1. Aby rozpocząć nowe skanowanie w celu przeprowadzania oceny przy użyciu Azure PowerShell lub interfejsu API REST, zobacz [skanowanie na żądanie](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Upewnij się, że parametry przypisania i zakres przypisania są ustawione poprawnie i że **wymuszanie** jest _włączone_.
1. Sprawdź [tryb definicji zasad](../concepts/definition-structure.md#mode):
   - Tryb powinien dotyczyć `all` wszystkich typów zasobów.
   - Tryb powinien mieć miejsce, `indexed` Jeśli definicja zasad sprawdza Tagi lub lokalizację.
1. Upewnij się, że zakres zasobu nie jest [wykluczony](../concepts/assignment-structure.md#excluded-scopes) [ani](../concepts/exemption-structure.md)wykluczony.
1. Sprawdź, czy ładunek zasobu jest zgodny z logiką zasad. Można to zrobić przez [przechwycenie śladu archiwum http (Har)](../../../azure-portal/capture-browser-trace.md) lub przejrzenie właściwości szablonu Azure Resource Manager (szablon ARM).
1. Aby poznać inne typowe problemy i rozwiązania, zobacz [Rozwiązywanie problemów: zgodność nie jest zgodna z oczekiwaniami](#scenario-compliance-isnt-as-expected).

Jeśli nadal masz problem z zduplikowaną i dostosowaną definicją zasad lub definicją niestandardową, Utwórz bilet pomocy technicznej w obszarze **Tworzenie zasad** w celu poprawnego kierowania problemu.

### <a name="scenario-denied-by-azure-policy"></a>Scenariusz: odmowa przez Azure Policy

#### <a name="issue"></a>Problem

Odmowa utworzenia lub zaktualizowania zasobu.

#### <a name="cause"></a>Przyczyna

Przypisanie zasad do zakresu nowego lub zaktualizowanego zasobu spełnia kryteria definicji zasad z efektem [odmowy](../concepts/effects.md#deny) . Zasoby, które są zgodne z tymi definicjami, nie mogą zostać utworzone lub zaktualizowane.

#### <a name="resolution"></a>Rozwiązanie

Komunikat o błędzie z przypisania zasad Odmów obejmuje definicję zasad i identyfikatory przypisań zasad. Jeśli informacje o błędzie w wiadomości nie zostaną pominięte, są również dostępne w [dzienniku aktywności](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Korzystając z tych informacji, można uzyskać więcej szczegółowych informacji, aby zrozumieć ograniczenia zasobów i dostosować właściwości zasobów w żądaniu w celu dopasowania do wartości dozwolonych.

## <a name="template-errors"></a>Błędy szablonów

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenariusz: obsługiwane przez zasady funkcje przetwarzane przez szablon

#### <a name="issue"></a>Problem

Azure Policy obsługuje wiele funkcji szablonów ARM i funkcji, które są dostępne tylko w definicji zasad. Menedżer zasobów przetwarza te funkcje jako część wdrożenia, a nie jako część definicji zasad.

#### <a name="cause"></a>Przyczyna

Używanie obsługiwanych funkcji, takich jak `parameter()` lub `resourceGroup()` , skutkuje przetworzonym wynikiem funkcji w czasie wdrażania zamiast zezwalać na przetwarzanie funkcji dla definicji zasad i aparatu Azure Policy.

#### <a name="resolution"></a>Rozwiązanie

Aby przekazać funkcję za pomocą jako część definicji zasad, należy wypróbować cały ciąg, `[` tak aby Właściwość wyglądała tak, jak wygląda `[[resourceGroup().tags.myTag]` . Znak ucieczki powoduje, Menedżer zasobów traktować wartość jako ciąg podczas przetwarzania szablonu. Azure Policy następnie umieszcza funkcję w definicji zasad, co umożliwia jej dynamiczne działanie zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zobacz [składnia i wyrażenia w szablonach Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Dodatek dla błędów instalacji Kubernetes

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Scenariusz: Instalacja za pomocą wykresu Helm kończy się niepowodzeniem z powodu błędu hasła

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Polecenie kończy się niepowodzeniem i zwraca jeden z następujących błędów:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Przyczyna

Wygenerowane hasło zawiera przecinek ( `,` ), na którym jest podzielny wykres Helm.

#### <a name="resolution"></a>Rozwiązanie

Po uruchomieniu `helm install azure-policy-addon` , ucieczki przecinek ( `,` ) w wartości hasło z ukośnikiem odwrotnym ( `\` ).

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Scenariusz: Instalacja za pomocą wykresu Helm kończy się niepowodzeniem, ponieważ nazwa już istnieje

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Polecenie kończy się niepowodzeniem i zwraca następujący błąd:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Przyczyna

Wykres Helm o nazwie `azure-policy-addon` został już zainstalowany lub częściowo zainstalowany.

#### <a name="resolution"></a>Rozwiązanie

Postępuj zgodnie z instrukcjami, aby [usunąć Azure Policy dodatku Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on), a następnie ponownie uruchom `helm install azure-policy-addon` polecenie.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenariusz: tożsamości przypisane przez użytkownika maszyn wirtualnych platformy Azure są zastępowane tożsamościami zarządzanymi przez system

#### <a name="issue"></a>Problem

Po przypisaniu inicjatyw zasad konfiguracji gościa do inspekcji ustawień wewnątrz maszyny, zarządzane tożsamości przypisane przez użytkownika przypisane do maszyny nie są już przypisane. Przypisywana jest tylko tożsamość zarządzana przypisana przez system.

#### <a name="cause"></a>Przyczyna

Definicje zasad, które były wcześniej używane w definicjach DeployIfNotExists konfiguracji gościa, zapewniały, że tożsamość przypisana przez system jest przypisana do komputera, ale również zostały usunięte przypisania tożsamości przypisane przez użytkownika.

#### <a name="resolution"></a>Rozwiązanie

Definicje, które wcześniej powodowały ten problem, są wyświetlane jako *[przestarzałe]* i zostały zastąpione przez definicje zasad, które zarządzają wymaganiami wstępnymi bez usuwania tożsamości zarządzanych przypisanych przez użytkownika. Wymagany jest krok ręczny. Usuń wszystkie istniejące przypisania zasad, które są oznaczone jako *[przestarzałe]*, i zastąp je uaktualnioną zasadą wymagań wstępnych i definicjami zasad, które mają taką samą nazwę jak oryginalna.

Aby uzyskać szczegółowe opisy, zobacz wpis w blogu [ważna zmiana dotycząca zasad inspekcji konfiguracji gościa](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316).

## <a name="add-on-for-kubernetes-general-errors"></a>Dodatek dla ogólnych błędów Kubernetes

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Scenariusz: dodatek nie może nawiązać połączenia z punktem końcowym usługi Azure Policy ze względu na ograniczenia ruchu wychodzącego

#### <a name="issue"></a>Problem

Dodatek nie może nawiązać połączenia z punktem końcowym usługi Azure Policy i zwraca jeden z następujących błędów:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Przyczyna

Ten problem występuje, gdy ruch wychodzący klastra jest zablokowany.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że domeny i porty wymienione w następujących artykułach są otwarte:

- [Wymagane reguły sieci wychodzące i w pełni kwalifikowane nazwy domen (FQDN) dla klastrów AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Zainstaluj dodatek Azure Policy dla usługi Azure Arc Kubernetes (wersja zapoznawcza)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Scenariusz: dodatek nie może nawiązać połączenia z punktem końcowym usługi Azure Policy ze względu na konfigurację AAD-pod-Identity

#### <a name="issue"></a>Problem

Dodatek nie może nawiązać połączenia z punktem końcowym usługi Azure Policy i zwraca jeden z następujących błędów:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy w klastrze jest zainstalowana wartość _Add-pod-Identity_ i _polecenia-systemowe_ nie są wykluczone w _usłudze AAD-pod-Identity_.

Identyfikator zarządzania (NMI) w _usłudze AAD-pod-Identity_ (usługa) — dołączenie iptables umożliwia modyfikację węzłów w celu przechwycenia wywołań do punktu końcowego metadanych wystąpienia platformy Azure. Ta konfiguracja oznacza, że wszystkie żądania wykonywane w punkcie końcowym metadanych są przechwytywane przez NMI, nawet jeśli nie korzystają z usługi _AAD-pod-Identity_.
*AzurePodIdentityException* CUSTOMRESOURCEDEFINITION (CRD) można skonfigurować tak, aby informować usługi _AAD-pod-Identity_ , że wszystkie żądania kierowane do punktu końcowego metadanych, które pochodzą ze zgodnych etykiet zdefiniowanych w CRD, powinny być serwerem proxy bez żadnego przetwarzania w NMI.

#### <a name="resolution"></a>Rozwiązanie

Wyklucz systemowe nazwy systemu, które mają `kubernetes.azure.com/managedby: aks` etykietę w _polecenia-systemową_ przestrzeń nazw w _usłudze AAD-pod-Identity_ , konfigurując *AzurePodIdentityException* CRD.

Aby uzyskać więcej informacji, zobacz temat [wyłączanie tożsamości usługi Azure Active Directory (Azure AD) pod kątem określonego elementu/aplikacji](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Aby skonfigurować wyjątek, wykonaj następujące czynności:

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

Dodatek może nawiązać połączenie z punktem końcowym usługi Azure Policy, ale dzienniki dodatków zawierają jeden z następujących błędów:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Przyczyna

Dostawca zasobów "Microsoft. PolicyInsights" nie jest zarejestrowany. Aby można było pobrać definicje zasad i zwrócić dane dotyczące zgodności, musi być on zarejestrowany.

#### <a name="resolution"></a>Rozwiązanie

Zarejestruj dostawcę zasobów "Microsoft. PolicyInsights" w subskrypcji klastra. Aby uzyskać instrukcje, zobacz [Rejestrowanie dostawcy zasobów](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscription-is-disabled"></a>Scenariusz: subskrypcja jest wyłączona

#### <a name="issue"></a>Problem

Dodatek może nawiązać połączenie z punktem końcowym usługi Azure Policy, ale jest wyświetlany następujący błąd:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Przyczyna

Ten błąd oznacza, że subskrypcja została określona jako problematyczna i flaga funkcji `Microsoft.PolicyInsights/DataPlaneBlocked` została dodana w celu zablokowania subskrypcji.

#### <a name="resolution"></a>Rozwiązanie

Aby zbadać i rozwiązać ten problem, [skontaktuj się z zespołem funkcji](mailto:azuredg@microsoft.com).

## <a name="next-steps"></a>Następne kroki

Jeśli Twój problem nie jest wymieniony w tym artykule lub nie możesz go rozwiązać, uzyskaj pomoc techniczną, odwiedzając jeden z następujących kanałów:

- Uzyskaj odpowiedzi od ekspertów za pośrednictwem usługi [Microsoft Q&A](/answers/topics/azure-policy.html).
- Połącz się z [@AzureSupport](https://twitter.com/azuresupport) . Ten oficjalny Microsoft Azure zasób w serwisie Twitter pomaga zwiększyć komfort pracy klienta, łącząc społeczność platformy Azure z właściwymi odpowiedziami, wsparciem i ekspertami.
- Jeśli nadal potrzebujesz pomocy, przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Prześlij żądanie pomocy technicznej**.
