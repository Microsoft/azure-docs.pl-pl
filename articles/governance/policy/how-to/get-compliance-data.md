---
title: Uzyskiwanie danych dotyczących zgodności z zasadami
description: Azure Policy oceny i efekty określają zgodność. Dowiedz się, jak uzyskać szczegółowe informacje o zgodności zasobów platformy Azure.
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: e1a9a7fcbbcbd7f490b2f665b40c7ed922ec61ee
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864599"
---
# <a name="get-compliance-data-of-azure-resources"></a>Uzyskiwanie danych dotyczących zgodności zasobów platformy Azure

Jedną z największych zalet usługi Azure Policy jest wgląd w szczegółowe informacje i [](../../management-groups/overview.md) kontrolę nad zasobami w ramach subskrypcji lub grupy zarządzania subskrypcji. Tę kontrolkę można wykonywać na wiele różnych sposobów, np. zapobiegać utworzeniu zasobów w niewłaściwej lokalizacji, wymuszać wspólne i spójne użycie tagów lub inspekcję istniejących zasobów w celu zapewnienia odpowiednich konfiguracji i ustawień. We wszystkich przypadkach dane są generowane przez Azure Policy, aby umożliwić zrozumienie stanu zgodności środowiska.

Istnieje kilka sposobów uzyskiwania dostępu do informacji o zgodności wygenerowanych przez przypisania zasad i inicjatyw:

- Korzystanie z [Azure Portal](#portal)
- Za [pomocą skryptów wiersza](#command-line) polecenia

Zanim przyjrzymy się metodom zgłaszania zgodności, przyjrzyjmy się, kiedy informacje o zgodności są aktualizowane, oraz częstotliwości i zdarzeń, które wyzwalają cykl oceny.

> [!WARNING]
> Jeśli stan zgodności jest zgłaszany jako Nie zarejestrowano, sprawdź, czy dostawca zasobów **Microsoft.PolicyInsights** jest zarejestrowany i czy użytkownik ma odpowiednie uprawnienia kontroli dostępu na podstawie ról (RBAC) platformy Azure, zgodnie z opisem w tesłudze [azure RBAC](../overview.md#azure-rbac-permissions-in-azure-policy)permissions in Azure Policy .

## <a name="evaluation-triggers"></a>Wyzwalacze oceny

Wyniki ukończonego cyklu oceny są dostępne w ramach operacji i `Microsoft.PolicyInsights` dostawcy `PolicyStates` `PolicyEvents` zasobów. Aby uzyskać więcej informacji na temat operacji interfejsu API REST usługi Azure Policy Insights, [zobacz Azure Policy Insights.](/rest/api/policy/)

Oceny przypisanych zasad i inicjatyw są podejmowane w wyniku różnych zdarzeń:

- Zasady lub inicjatywa są nowo przypisywane do zakresu. Zastosowanie przypisania do zdefiniowanego zakresu trwa około 30 minut. Po zastosowaniu cykl oceny rozpoczyna się dla zasobów w tym zakresie względem nowo przypisanych zasad lub inicjatywy. W zależności od efektów używanych przez zasady lub inicjatywę zasoby są oznaczane jako zgodne, niezgodne lub wykluczone. Duża zasada lub inicjatywa oceniona pod względem dużego zakresu zasobów może zająć trochę czasu. W związku z tym nie ma wstępnie zdefiniowanych oczekiwań po zakończeniu cyklu oceny. Po jego zakończeniu zaktualizowane wyniki zgodności będą dostępne w portalu i zestawach SDK.

- Zasady lub inicjatywa już przypisana do zakresu zostaną zaktualizowane. Cykl oceny i harmonogram dla tego scenariusza są takie same jak w przypadku nowego przypisania do zakresu.

- Zasób jest wdrażany lub aktualizowany w zakresie za pomocą przypisania za pośrednictwem Azure Resource Manager, interfejsu API REST lub obsługiwanego zestawu SDK. W tym scenariuszu zdarzenie efektu (dołączenie, inspekcja, odmowa, wdrożenie) i zgodne informacje o stanie dla pojedynczego zasobu stają się dostępne w portalu i zestawach SDK około 15 minut później. To zdarzenie nie powoduje oceny innych zasobów.

- Subskrypcja (typ zasobu ) jest tworzona lub przenoszony w hierarchii grupy zarządzania z przypisaną definicją zasad `Microsoft.Resource/subscriptions` ukierunkowaną na typ zasobu subskrypcji. [](../../management-groups/overview.md) Ocena efektów obsługiwanych przez subskrypcję (audit, auditIfNotExist, deployIfNotExists, modify), rejestrowanie i wszelkie akcje korygowania trwa około 30 minut.

- Wyłączenie [zasad jest](../concepts/exemption-structure.md) tworzone, aktualizowane lub usuwane. W tym scenariuszu odpowiednie przypisanie jest oceniane dla zdefiniowanego zakresu zwolnienia.

- Standardowy cykl oceny zgodności. Co 24 godziny przypisania są automatycznie ponownie wyceny. Duże zasady lub inicjatywa wielu zasobów mogą zająć trochę czasu, więc nie ma wstępnie zdefiniowanych oczekiwań co do czasu ukończenia cyklu oceny. Po jego zakończeniu zaktualizowane wyniki zgodności będą dostępne w portalu i zestawach SDK.

- Dostawca [zasobów konfiguracja](../concepts/guest-configuration.md) gościa jest aktualizowany o szczegóły zgodności według zarządzanego zasobu.

- Skanowanie na żądanie

### <a name="on-demand-evaluation-scan"></a>Skanowanie oceny na żądanie

Skanowanie ewaluacyjnie subskrypcji lub grupy zasobów można rozpocząć przy użyciu interfejsu wiersza polecenia platformy Azure, usługi Azure PowerShell, wywołania interfejsu API REST lub za pomocą akcji GitHub Skanowanie pod Azure Policy [Compliance Scan.](https://github.com/marketplace/actions/azure-policy-compliance-scan)
To skanowanie jest procesem asynchronicznym.

#### <a name="on-demand-evaluation-scan---github-action"></a>Skanowanie oceny na żądanie — akcja usługi GitHub

Użyj akcji skanowanie pod Azure Policy [Compliance Scan,](https://github.com/marketplace/actions/azure-policy-compliance-scan) aby wyzwolić skanowanie oceny na żądanie z przepływu pracy usługi [GitHub](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) dla jednego lub wielu zasobów, grup zasobów lub subskrypcji i wyzwolić przepływ pracy na podstawie stanu zgodności zasobów. Można również skonfigurować przepływ pracy do uruchamiania w zaplanowanym czasie, aby w dogodnym czasie uzyskać najnowszy stan zgodności. Opcjonalnie ta akcja usługi GitHub może wygenerować raport o stanie zgodności skanowanych zasobów do dalszej analizy lub archiwizacji.

W poniższym przykładzie jest uruchamiane skanowanie pod poszukiwaniu zgodności dla subskrypcji. 

```yaml
on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Aby uzyskać więcej informacji i przykłady przepływów pracy, zobacz repozytorium Akcja usługi [GitHub Azure Policy Skanowania pod zgodność z przepisami.](https://github.com/Azure/policy-compliance-scan)

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Skanowanie oceny na żądanie — interfejs wiersza polecenia platformy Azure

Skanowanie zgodności rozpoczyna się za pomocą [polecenia az policy state trigger-scan.](/cli/azure/policy/state#az_policy_state_trigger_scan)

Domyślnie program `az policy state trigger-scan` rozpoczyna ocenę wszystkich zasobów w bieżącej subskrypcji. Aby rozpocząć ocenę określonej grupy zasobów, użyj **parametru grupa zasobów.** W poniższym przykładzie rozpoczyna się skanowanie pod poszukiwaniu zgodności w bieżącej subskrypcji dla grupy zasobów _MojaGZ:_

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

Możesz nie czekać na ukończenie procesu asynchronicznego przed kontynuowaniem pracy z **parametrem no-wait.**

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Skanowanie oceny na żądanie — Azure PowerShell

Skanowanie zgodności rozpoczyna się za pomocą polecenia cmdlet [Start-AzPolicyComplianceScan.](/powershell/module/az.policyinsights/start-azpolicycompliancescan)

Domyślnie program `Start-AzPolicyComplianceScan` rozpoczyna ocenę wszystkich zasobów w bieżącej subskrypcji. Aby rozpocząć ocenę określonej grupy zasobów, użyj **parametru ResourceGroupName.** W poniższym przykładzie rozpoczyna się skanowanie pod poszukiwaniu zgodności w bieżącej subskrypcji dla grupy zasobów _MojaGZ:_

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

Program PowerShell może poczekać na ukończenie wywołania asynchronicznego przed dostarczeniem danych wyjściowych wyników lub uruchomić je w tle jako [zadanie](/powershell/module/microsoft.powershell.core/about/about_jobs). Aby użyć zadania programu PowerShell do uruchomienia skanowania zgodności w tle, użyj parametru **AsJob** i ustaw wartość na obiekt, taki jak w `$job` poniższym przykładzie:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

Stan zadania można sprawdzić, sprawdzając obiekt `$job` . Zadanie ma typ `Microsoft.Azure.Commands.Common.AzureLongRunningJob` . Użyj `Get-Member` dla obiektu , aby wyświetlić dostępne właściwości i `$job` metody.

Podczas skanowania zgodności sprawdzane są następujące wyniki w danych `$job` wyjściowych obiektu:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Po zakończeniu skanowania zgodności właściwość **State** zmieni się na _Ukończono._

#### <a name="on-demand-evaluation-scan---rest"></a>Skanowanie ewaluacyjnie na żądanie — REST

W ramach procesu asynchronicznego punkt końcowy REST, aby rozpocząć skanowanie, nie czeka, aż skanowanie zostanie ukończone, aby odpowiedzieć. Zamiast tego udostępnia ona URI do wykonywania zapytań o stan żądanej oceny.

Każdy identyfikator URI interfejsu API REST zawiera używane zmienne, które musisz zastąpić własnymi wartościami:

- `{YourRG}` - Zastąp nazwą grupy zasobów
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

Skanowanie obsługuje ocenę zasobów w subskrypcji lub grupie zasobów. Rozpocznij skanowanie według zakresu za pomocą polecenia POST interfejsu API **REST** przy użyciu następujących struktur URI:

- Subskrypcja

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Grupa zasobów

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

Wywołanie zwraca stan **202 Zaakceptowane.** Nagłówek odpowiedzi zawiera właściwość **Location** w następującym formacie:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` jest generowany statycznie dla żądanego zakresu. Jeśli w zakresie jest już uruchomione skanowanie na żądanie, nowe skanowanie nie jest rozpoczynane. Zamiast tego nowe żądanie ma ten sam kod URI lokalizacji dla `{ResourceContainerGUID}`  stanu. Polecenie GET interfejsu API **REST** do **lokalizacji** URI zwraca **zaakceptowany 202** podczas oceny. Po zakończeniu skanowania w celu oceny zwracany jest **stan 200 OK.** Treść ukończonego skanowania to odpowiedź JSON o stanie:

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>Skanowanie oceny na żądanie — Visual Studio Code

Rozszerzenie Azure Policy dla Visual Studio jest w stanie uruchamiać skanowanie oceny dla określonego zasobu. To skanowanie jest procesem synchronicznym, w przeciwieństwie do Azure PowerShell i REST.
Aby uzyskać szczegółowe informacje i instrukcje, zobacz [Ocena na żądanie przy użyciu VS Code rozszerzenia](./extension-for-vscode.md#on-demand-evaluation-scan).

## <a name="how-compliance-works"></a>Jak działa zgodność

W przypisaniu zasób jest **niezgodny,** jeśli nie jest zgodny z zasadami lub regułami inicjatywy i nie jest _wykluczona._ W poniższej tabeli pokazano, jak różne efekty zasad działają z oceną warunku dla wynikowego stanu zgodności:

| Stan zasobu | Efekt | Ocena zasad | Stan zgodności |
| --- | --- | --- | --- |
| Nowa czy zaktualizowana? | Audit, Modify, AuditIfNotExist | Prawda | Niezgodne |
| Nowa czy zaktualizowana? | Audit, Modify, AuditIfNotExist | Fałsz | Zgodny |
| Exists | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | Prawda | Niezgodne |
| Exists | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | Fałsz | Zgodny |

> [!NOTE]
> Efekty DeployIfNotExist i AuditIfNotExist wymagają, aby instrukcja IF ma wartość TRUE, a warunek istnienia był wartością FALSE, aby był niezgodny. W przypadku wartości TRUE warunek IF wyzwala ocenę warunku istnienia dla powiązanych zasobów.

Załóżmy na przykład, że masz grupę zasobów — ContsoRG z niektórymi kontami magazynu (wyróżnioną na czerwono), które są widoczne dla sieci publicznych.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Diagram kont magazynu ujawnionych w sieciach publicznych w grupie zasobów Contoso R G." border="false":::
   Diagram przedstawiający obrazy dla pięciu kont magazynu w grupie zasobów Contoso R G.  Konta magazynu ( jeden i trzy) są niebieskie, a konta magazynu dwa, cztery i pięć są czerwone.
:::image-end:::

W tym przykładzie należy mieć na względzie zagrożenia bezpieczeństwa. Teraz, po utworzeniu przypisania zasad, jest ono oceniane dla wszystkich uwzględnionych i nieklucznych kont magazynu w grupie zasobów ContosoRG. Przeprowadza inspekcję trzech niezgodnych kont magazynu, w związku z tym zmieniając ich stany na **niezgodne.**

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Diagram zgodności konta magazynu w grupie zasobów Contoso R G." border="false":::
   Diagram przedstawiający obrazy dla pięciu kont magazynu w grupie zasobów Contoso R G. Pod kontami magazynu jeden i trzy mają teraz zielone znaczniki wyboru, a konta magazynu dwa, cztery i pięć mają teraz pod nimi czerwone znaki ostrzegawcze.
:::image-end:::

Oprócz **zgodnych** i **niezgodnych** zasady i zasoby mają cztery inne stany:

- **Wyklucz:** zasób znajduje się w zakresie przypisania, ale ma [zdefiniowane wyłączenie](../concepts/exemption-structure.md).
- **Konflikt:** istnieją co najmniej dwie definicje zasad z regułami powodujące konflikt. Na przykład dwie definicje dołączą ten sam tag z różnymi wartościami.
- **Nie rozpoczęto:** cykl oceny nie został uruchomiony dla zasad lub zasobu.
- **Nie zarejestrowano:** Azure Policy zasobów nie został zarejestrowany lub zalogowane konto nie ma uprawnień do odczytu danych zgodności.

Azure Policy używa pól **typu**, **nazwy** lub **rodzaju** w definicji, aby określić, czy zasób jest dopasowaniem. Gdy zasób jest zgodny, jest uznawany za stosowany i ma stan **Zgodne,** Niezgodne **lub** **Wykluczone.** Jeśli **typ**, **nazwa** lub **rodzaj** jest jedyną właściwością w definicji, wszystkie dołączone i wykluczone zasoby są uznawane za odpowiednie i są oceniane.

Wartość procentowa zgodności jest określana przez podzielenie zasobów **Zgodne** i **Wykluczone** przez _łączną liczbę zasobów._ _Łączna liczba_ zasobów jest definiowana jako suma zgodnych, **niezgodnych,** **wykluczeniowych** i **konfliktowych** zasobów. Ogólne numery zgodności są sumą odrębnych  zasobów, które są zgodne lub wykluczone, podzielone przez sumę wszystkich odrębnych zasobów.  Na poniższej ilustracji znajduje się 20 odrębnych zasobów, które mają zastosowanie, a tylko jeden z nich jest **niezgodny.**
Ogólna zgodność zasobów wynosi 95% (19 z 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Zrzut ekranu przedstawiający szczegóły zgodności zasad ze strony Zgodność." border="false":::

> [!NOTE]
> Zgodność z przepisami w Azure Policy to funkcja w wersji zapoznawczej. Właściwości zgodności z zestawu SDK i stron w portalu różnią się w przypadku włączonych inicjatyw. Aby uzyskać więcej informacji, zobacz [Zgodność z przepisami](../concepts/regulatory-compliance.md)

## <a name="portal"></a>Portal

Ten Azure Portal graficzne środowisko wizualizacji i zrozumienia stanu zgodności w środowisku. Na **stronie Zasady** opcja **Przegląd** zawiera szczegółowe informacje dotyczące dostępnych zakresów zgodności zasad i inicjatyw. Wraz ze stanem zgodności i licznikiem na przypisanie zawiera wykres przedstawiający zgodność z ostatnich siedmiu dni. Strona **Zgodność** zawiera większość tych samych informacji (z wyjątkiem wykresu), ale zapewnia dodatkowe opcje filtrowania i sortowania.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Zrzut ekranu przedstawiający stronę Zgodność, opcje filtrowania i szczegóły." border="false":::

Ponieważ zasady lub inicjatywa mogą być przypisane do różnych zakresów, tabela zawiera zakres dla każdego przypisania i typ przypisanej definicji. Podano również liczbę niezgodnych zasobów i niezgodnych zasad dla każdego przypisania. Wybranie zasad lub inicjatywy w tabeli umożliwia bardziej szczegółowe przyjrzenie się zgodności dla tego konkretnego przypisania.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Zrzut ekranu przedstawiający stronę Szczegóły zgodności, w tym liczniki i szczegóły zgodności zasobów." border="false":::

Lista zasobów na karcie **Zgodność zasobów** zawiera stan oceny istniejących zasobów dla bieżącego przypisania. Domyślnie karta ma wartość **Niezgodne,** ale można ją filtrować.
Zdarzenia (dołączanie, inspekcja, odmowa, wdrażanie, modyfikowanie) wyzwalane przez żądanie utworzenia zasobu są wyświetlane na **karcie Zdarzenia.**

> [!NOTE]
> W przypadku zasad aparatu AKS pokazany zasób jest grupą zasobów.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Zrzut ekranu przedstawiający kartę Zdarzenia na stronie Szczegóły zgodności." border="false":::

<a name="component-compliance"></a>W [przypadku zasobów trybu dostawcy](../concepts/definition-structure.md#resource-provider-modes) zasobów na karcie Zgodność zasobów wybranie zasobu  lub kliknięcie prawym przyciskiem myszy wiersza i wybranie pozycji Wyświetl szczegóły zgodności powoduje otwarcie szczegółów zgodności składnika.  Ta strona zawiera również karty, na których można zobaczyć zasady przypisane do tego zasobu, zdarzenia, zdarzenia składników i historię zmian.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Zrzut ekranu przedstawiający kartę Zgodność składników i szczegóły zgodności dla przypisania trybu dostawcy zasobów." border="false":::

Po powrocie na stronę zgodności zasobów kliknij prawym przyciskiem myszy wiersz zdarzenia, na podstawie których chcesz zebrać więcej szczegółów, a następnie wybierz polecenie **Pokaż dzienniki aktywności.** Zostanie otwarta strona dziennika aktywności, która jest wstępnie filtrowana do wyszukiwania ze szczegółami przypisania i zdarzeniami. Dziennik aktywności zawiera dodatkowy kontekst i informacje o tych zdarzeniach.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Zrzut ekranu przedstawiający dziennik aktywności dla Azure Policy i ocen." border="false":::

### <a name="understand-non-compliance"></a>Opis niezgodności

Jeśli zasób zostanie określony jako **niezgodny,** istnieje wiele możliwych przyczyn. Aby ustalić przyczynę  niezgodności zasobu lub znaleźć odpowiedzialną zmianę, zobacz [Określanie niezgodności](./determine-non-compliance.md).

## <a name="command-line"></a>Wiersz polecenia

Te same informacje dostępne w portalu można pobrać za pomocą interfejsu API REST (w tym z [usługą ARMClient),](https://github.com/projectkudu/ARMClient)interfejsem Azure PowerShell i interfejsem wiersza polecenia platformy Azure. Aby uzyskać szczegółowe informacje na temat interfejsu API REST, zobacz [Azure Policy](/rest/api/policy/) dokumentacja. Strony referencyjne interfejsu API REST mają zielony przycisk "Wypróbuj" dla każdej operacji, który umożliwia wypróbowanie jej bezpośrednio w przeglądarce.

Użyj klienta ARMClient lub podobnego narzędzia do obsługi uwierzytelniania na platformie Azure na przykładach interfejsu API REST.

### <a name="summarize-results"></a>Podsumowanie wyników

Za pomocą interfejsu API REST podsumowania można wykonywać według kontenera, definicji lub przypisania. Oto przykład podsumowania na poziomie subskrypcji przy użyciu podsumowania Azure Policy Insights [for Subscription](/rest/api/policy/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

Dane wyjściowe podsumowują subskrypcję. W przykładowych danych wyjściowych poniżej podsumowana zgodność znajduje się w obszarze **value.results.nonCompliantResources** i **value.results.nonCompliantPolicies.** To żądanie zawiera dodatkowe szczegóły, w tym każde przypisanie, które składa się na niezgodne numery, oraz informacje o definicji poszczególnych przypisań. Każdy obiekt zasad w hierarchii zawiera **queryResultsUri,** który może służyć do uzyskania dodatkowych szczegółów na tym poziomie.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Wykonywanie zapytań o zasoby

W powyższym przykładzie **value.policyAssignments.policyDefinitions.results.queryResultsUri** udostępnia przykładowy kod URI dla wszystkich niezgodnych zasobów dla określonej definicji zasad. Patrząc na **$filter,** ComplianceState jest równa (eq) do "NonCompliant", PolicyAssignmentId jest określony dla definicji zasad, a następnie PolicyDefinitionId. Przyczyną do uwzględnienia PolicyAssignmentId w filtrze jest, ponieważ PolicyDefinitionId może istnieć w kilku przypisań zasad lub inicjatywy o różnych zakresach. Określając zarówno policyAssignmentId i PolicyDefinitionId, możemy jawnie w wynikach, których szukamy. Wcześniej dla policyStates umyliśmy **latest**, który automatycznie ustawia **od** i do **okna** czasu z ostatnich 24 godzin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Przykładowa odpowiedź poniżej została przycięty do pojedynczego niezgodnego zasobu w celu zachowania zwięzłości. Szczegółowa odpowiedź zawiera kilka fragmentów danych dotyczących zasobu, zasad lub inicjatywy oraz przypisania. Zwróć uwagę, że można również zobaczyć, jakie parametry przypisania zostały przekazane do definicji zasad.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "ComplianceState": "NonCompliant",
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Wyświetlanie zdarzeń

Gdy zasób jest tworzony lub aktualizowany, generowany jest wynik oceny zasad. Wyniki są nazywane _zdarzeniami zasad_. Użyj następującego URI, aby wyświetlić ostatnie zdarzenia zasad skojarzone z subskrypcją.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
```

Wyniki powinny wyglądać podobnie do następujących:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących zdarzeń zasad, zobacz [artykuł z Azure Policy zdarzeniami.](/rest/api/policy/policyevents)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Grupa [poleceń interfejsu wiersza](/cli/azure/what-is-azure-cli) polecenia platformy Azure dla Azure Policy większość operacji, które są dostępne w interfejsie REST lub Azure PowerShell. Aby uzyskać pełną listę dostępnych poleceń, zobacz Azure CLI - Azure Policy Overview (Interfejs wiersza polecenia platformy [Azure — Azure Policy omówienie).](/cli/azure/policy)

Przykład: pobieranie podsumowania stanu dla najbardziej przypisanych zasad z największą liczbą niezgodnych zasobów.

```azurecli-interactive
az policy state summarize --top 1
```

Górna część odpowiedzi wygląda jak w tym przykładzie:

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

Przykład: pobieranie rekordu stanu dla ostatnio ocenianego zasobu (domyślnie jest to znacznik czasu w kolejności malejącej).

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

Przykład: pobieranie szczegółów dla wszystkich niezgodnych zasobów sieci wirtualnej.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

Przykład: Pobieranie zdarzeń związanych z niezgodnymi zasobami sieci wirtualnej, które wystąpiły po określonej dacie.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

Moduł Azure PowerShell dla Azure Policy jest dostępny na stronie Galeria programu PowerShell jako [Az.PolicyInsights.](https://www.powershellgallery.com/packages/Az.PolicyInsights) Za pomocą modułu PowerShellGet możesz zainstalować moduł przy użyciu polecenia (upewnij się, że zainstalowano `Install-Module -Name Az.PolicyInsights` [najnowsze Azure PowerShell):](/powershell/azure/install-az-ps)

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Moduł ma następujące polecenia cmdlet:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Przykład: pobieranie podsumowania stanu dla najbardziej przypisanych zasad z największą liczbą niezgodnych zasobów.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Przykład: pobieranie rekordu stanu dla ostatnio ocenianego zasobu (domyślnie jest to znacznik czasu w kolejności malejącej).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Przykład: pobieranie szczegółów dla wszystkich niezgodnych zasobów sieci wirtualnej.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Przykład: Pobieranie zdarzeń związanych z niezgodnymi zasobami sieci wirtualnej, które wystąpiły po określonej dacie, konwertowanie na obiekt CSV i eksportowanie do pliku.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

Dane wyjściowe obiektu `$policyEvents` wyglądają podobnie do następujących danych wyjściowych:

```output
Timestamp                  : 9/19/2020 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Pole **PrincipalOid** może służyć do uzyskania określonego użytkownika za pomocą Azure PowerShell cmdlet `Get-AzADUser` . Zastąp **element {principalOid}** odpowiedzią z poprzedniego przykładu.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Jeśli masz obszar roboczy usługi [Log Analytics](../../../azure-monitor/logs/log-query-overview.md) z rozwiązaniem usługi Activity Log Analytics powiązanym z Twoją subskrypcją, możesz również wyświetlić wyniki niezgodności z oceny nowych i zaktualizowanych zasobów przy użyciu prostych zapytań usługi Kusto i `AzureActivity` [](../../../azure-monitor/essentials/activity-log.md) `AzureActivity` tabeli. Szczegóły w dziennikach Azure Monitor, alerty można skonfigurować do obserwowania niezgodności.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Zrzut ekranu Azure Monitor przedstawiający Azure Policy akcje w tabeli AzureActivity." border="false":::

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady pod [Azure Policy przykładami.](../samples/index.md)
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, [jak programowo tworzyć zasady.](programmatically-create.md)
- Dowiedz się, jak [korygować niezgodne zasoby.](remediate-resources.md)
- Aby sprawdzić, czym jest grupa zarządzania, zobacz [Organize your resources with Azure management groups (Organizowanie zasobów przy użyciu grup zarządzania platformy Azure).](../../management-groups/overview.md)
