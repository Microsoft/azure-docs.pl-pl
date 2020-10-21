---
title: Pobierz dane zgodności zasad
description: Azure Policy oceny i efekty określają zgodność. Dowiedz się, jak uzyskać szczegóły zgodności zasobów platformy Azure.
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 36645d5eb50aaf571c608fc51127b47ac885777d
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320418"
---
# <a name="get-compliance-data-of-azure-resources"></a>Pobieranie danych zgodności zasobów platformy Azure

Jednym z największych korzyści z Azure Policy jest wgląd i kontrola nad zasobami w ramach subskrypcji lub [grupy zarządzania](../../management-groups/overview.md) subskrypcjami. Ta kontrolka może być przeprowadzana na wiele różnych sposobów, takich jak uniemożliwianie tworzenia zasobów w niewłaściwej lokalizacji, wymuszanie wspólnego i spójnego użycia tagów lub Inspekcja istniejących zasobów dla odpowiednich konfiguracji i ustawień. We wszystkich przypadkach dane są generowane przez Azure Policy, aby umożliwić zrozumienie stanu zgodności środowiska.

Istnieje kilka sposobów uzyskiwania dostępu do informacji o zgodności wygenerowanych przez przydziały zasad i inicjatyw:

- Korzystanie z [Azure Portal](#portal)
- Za pomocą skryptów [wiersza polecenia](#command-line)

Przed przystąpieniem do metod zgłaszania zgodności Sprawdź, czy informacje o zgodności są aktualizowane oraz częstotliwość i zdarzenia wyzwalające cykl oceniania.

> [!WARNING]
> Jeśli stan zgodności jest raportowany jako **niezarejestrowany**, sprawdź, czy dostawca zasobów **Microsoft. PolicyInsights** został zarejestrowany i czy użytkownik ma odpowiednie uprawnienia kontroli dostępu opartej na rolach (RBAC) na platformie Azure, zgodnie z opisem w temacie [uprawnienia usługi Azure RBAC w Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Wyzwalacze oceny

Wyniki kompletnego cyklu oceny są dostępne w ramach `Microsoft.PolicyInsights` dostawcy zasobów w ramach `PolicyStates` operacji i `PolicyEvents` . Aby uzyskać więcej informacji na temat operacji interfejsu API REST usługi Azure Policy Insights, zobacz [Azure Policy Insights](/rest/api/policy-insights/).

Oceny przypisanych zasad i inicjatyw odbywają się w wyniku różnych zdarzeń:

- Zasady lub inicjatywa są nowo przypisane do zakresu. Przypisanie do określonego zakresu zajmie około 30 minut. Po zastosowaniu cykl oceny rozpoczyna się dla zasobów należących do tego zakresu od nowo przypisanych zasad lub inicjatyw i w zależności od skutków używanych przez zasady lub inicjatywę, zasoby są oznaczane jako zgodne, niezgodne lub wykluczone. Duże zasady lub inicjatywy oceniane w odniesieniu do dużego zakresu zasobów mogą zająć dużo czasu. W związku z tym nie istnieje wstępnie zdefiniowane oczekiwanie po zakończeniu cyklu szacowania. Po jego zakończeniu zaktualizowane wyniki zgodności są dostępne w portalu i zestawach SDK.

- Zasady lub inicjatywa już przypisane do zakresu zostały zaktualizowane. Cykl oceny i czas dla tego scenariusza są takie same jak w przypadku nowego przypisania do zakresu.

- Zasób jest wdrażany lub aktualizowany w ramach zakresu z przypisaniem za pośrednictwem Azure Resource Manager, interfejsu API REST lub obsługiwanego zestawu SDK. W tym scenariuszu zdarzenie wpływu (dołączanie, inspekcja, odmowa, wdrożenie) i informacje o stanie zgodnym dla poszczególnych zasobów staną się dostępne w portalu i zestawach SDK około 15 minut później. To zdarzenie nie powoduje oceny innych zasobów.

- [Wykluczanie zasad](../concepts/exemption-structure.md) jest tworzone, aktualizowane lub usuwane. W tym scenariuszu odpowiednie przypisanie jest oceniane dla zdefiniowanego zakresu wykluczenia.

- Cykl oceny zgodności standardowej. Co 24 godziny, przydziały są automatycznie oceniane. Duże zasady lub inicjatywy wielu zasobów mogą zająć dużo czasu, dlatego nie istnieje wstępnie zdefiniowane oczekiwanie po zakończeniu cyklu szacowania. Po jego zakończeniu zaktualizowane wyniki zgodności są dostępne w portalu i zestawach SDK.

- Dostawca zasobów [konfiguracji gościa](../concepts/guest-configuration.md) został zaktualizowany o szczegóły zgodności przez zasób zarządzany.

- Skanowanie na żądanie

### <a name="on-demand-evaluation-scan"></a>Skanowanie oceny na żądanie

Skanowanie w celu oceny subskrypcji lub grupy zasobów można rozpocząć przy użyciu interfejsu wiersza polecenia platformy Azure, Azure PowerShell, wywołania interfejsu API REST lub za pomocą [akcji GitHub skanowania zgodności Azure Policy](https://github.com/marketplace/actions/azure-policy-compliance-scan).
To skanowanie jest procesem asynchronicznym.

#### <a name="on-demand-evaluation-scan---github-action"></a>Skanowanie oceny na żądanie — akcja GitHub

[Akcja skanowania zgodności Azure Policy](https://github.com/marketplace/actions/azure-policy-compliance-scan) służy do wyzwalania skanowania oceny na żądanie z [przepływu pracy](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) w usłudze GitHub w przypadku jednego lub wielu zasobów, grup zasobów lub subskrypcji, a także Brama przepływu pracy na podstawie stanu zgodności zasobów. Możesz również skonfigurować przepływ pracy do uruchamiania w zaplanowanym czasie, aby uzyskać najnowszy stan zgodności w dogodnym czasie. Opcjonalnie ta akcja usługi GitHub może generować raport dotyczący stanu zgodności skanowanych zasobów na potrzeby dalszej analizy lub do archiwizacji.

W poniższym przykładzie uruchomiono skanowanie zgodności dla subskrypcji. 

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

Aby uzyskać więcej informacji i przykładów przepływu pracy, zobacz [akcję GitHub dla repozytorium skanowania zgodności Azure Policy](https://github.com/Azure/policy-compliance-scan).

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Skanowanie oceny na żądanie — interfejs wiersza polecenia platformy Azure

Skanowanie zgodności jest uruchamiane za pomocą polecenia [AZ Policy State Trigger-Scan](/cli/azure/policy/state#az-policy-state-trigger-scan) .

Domyślnie program `az policy state trigger-scan` uruchamia ocenę dla wszystkich zasobów w bieżącej subskrypcji. Aby rozpocząć ocenę dla określonej grupy zasobów, użyj parametru **Resource-Group** . W poniższym przykładzie rozpocznie się skanowanie zgodności w bieżącej subskrypcji grupy zasobów _mojagz_ :

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

Można zrezygnować z zaczekania na zakończenie procesu asynchronicznego przed kontynuowaniem parametru **bez oczekiwania** .

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Skanowanie w celu oceny na żądanie — Azure PowerShell

Skanowanie zgodności zostało rozpoczęte przy użyciu polecenia cmdlet [Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan) .

Domyślnie program `Start-AzPolicyComplianceScan` uruchamia ocenę dla wszystkich zasobów w bieżącej subskrypcji. Aby rozpocząć ocenę dla określonej grupy zasobów, użyj parametru **ResourceGroupName** . W poniższym przykładzie rozpocznie się skanowanie zgodności w bieżącej subskrypcji grupy zasobów _mojagz_ :

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

Program PowerShell może oczekiwać na zakończenie wywołania asynchronicznego przed dostarczeniem wyników wyjściowych lub uruchomieniem go w tle jako [zadania](/powershell/module/microsoft.powershell.core/about/about_jobs). Aby użyć zadania programu PowerShell do uruchomienia skanowania zgodności w tle, użyj parametru **AsJob** i ustaw wartość na obiekt, np `$job` . w tym przykładzie:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

Stan zadania można sprawdzić, sprawdzając `$job` obiekt. Zadanie jest typu `Microsoft.Azure.Commands.Common.AzureLongRunningJob` . Użyj `Get-Member` obiektu, `$job` Aby wyświetlić dostępne właściwości i metody.

Gdy skanowanie zgodności jest uruchomione, sprawdzanie, czy `$job` obiekt zwraca wyniki, takie jak następujące:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Po zakończeniu skanowania zgodności Właściwość **State** zmieni się na _zakończone_.

#### <a name="on-demand-evaluation-scan---rest"></a>Skanowanie do oceny na żądanie — REST

Jako proces asynchroniczny punkt końcowy REST do uruchomienia skanowania nie czeka na zakończenie skanowania. Zamiast tego zapewnia identyfikator URI, aby wykonać zapytanie o stan żądana oceny.

Każdy identyfikator URI interfejsu API REST zawiera używane zmienne, które musisz zastąpić własnymi wartościami:

- `{YourRG}` -Zamień na nazwę grupy zasobów
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

Skanowanie obsługuje Obliczanie zasobów w ramach subskrypcji lub grupy zasobów. Rozpocznij skanowanie według zakresu za pomocą polecenia interfejsu API REST **post** przy użyciu następujących struktur identyfikatorów URI:

- Subskrypcja

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Grupa zasobów

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

Wywołanie zwraca stan **Zaakceptowany 202** . Uwzględniony w nagłówku odpowiedzi to właściwość **Location** o następującym formacie:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` jest generowana statycznie dla żądanego zakresu. Jeśli w zakresie jest już uruchomione skanowanie na żądanie, nowe skanowanie nie zostanie uruchomione. Zamiast tego nowe żądanie ma ten sam `{ResourceContainerGUID}` Identyfikator URI **lokalizacji** dla stanu. Polecenie API REST **Get** do identyfikatora URI **lokalizacji** zwraca **202 zaakceptowane** podczas obliczania. Po zakończeniu skanowania ewaluacyjnego zostanie zwrócony stan **200 OK** . Treść ukończonego skanowania jest odpowiedzią JSON o stanie:

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>Skanowanie w celu oceny na żądanie — Visual Studio Code

Rozszerzenie Azure Policy dla programu Visual Studio Code jest w stanie uruchomić skanowanie w poszukiwaniu określonego zasobu. To skanowanie jest procesem synchronicznym, w przeciwieństwie do metod Azure PowerShell i REST.
Aby uzyskać szczegółowe informacje i kroki, zobacz [ocenę na żądanie przy użyciu rozszerzenia vs Code](./extension-for-vscode.md#on-demand-evaluation-scan).

## <a name="how-compliance-works"></a>Jak działa zgodność

W przypisaniu zasób nie jest **zgodny** , jeśli nie przestrzega reguł zasad lub inicjatyw i nie jest _wykluczony_. W poniższej tabeli przedstawiono, w jaki sposób różne skutki zasad działają w przypadku oceny warunku dotyczącego stanu zgodności:

| Stan zasobu | Efekt | Ocena zasad | Stan zgodności |
| --- | --- | --- | --- |
| Nowa czy zaktualizowana? | Inspekcja, modyfikowanie, AuditIfNotExist | Prawda | Niezgodne |
| Nowa czy zaktualizowana? | Inspekcja, modyfikowanie, AuditIfNotExist | Fałsz | Zgodny |
| Exists | Deny, Audit, append, Modify, DeployIfNotExist, AuditIfNotExist | Prawda | Niezgodne |
| Exists | Deny, Audit, append, Modify, DeployIfNotExist, AuditIfNotExist | Fałsz | Zgodny |

> [!NOTE]
> Efekty DeployIfNotExist i AuditIfNotExist wymagają, aby instrukcja IF była prawdziwa, a warunek istnienia ma wartość FALSE, aby nie był zgodny. W przypadku wartości TRUE warunek IF wyzwala ocenę warunku istnienia dla powiązanych zasobów.

Załóżmy na przykład, że masz grupę zasobów — ContsoRG z pewnymi kontami magazynu (wyróżnioną kolorem czerwonym), które są dostępne w sieciach publicznych.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Diagram kont magazynu narażonych na sieci publiczne w grupie zasobów contoso R G." border="false":::
   Diagram przedstawiający obrazy pięciu kont magazynu w grupie zasobów contoso R G.  Konta magazynu jedno i trzy są niebieskie, natomiast konta magazynu dwa, cztery i pięć są czerwone.
:::image-end:::

W tym przykładzie należy zastanowić się nad zagrożeniem bezpieczeństwa. Po utworzeniu przypisania zasad zostanie ono ocenione dla wszystkich uwzględnionych i niewykluczonych kont magazynu w grupie zasobów ContosoRG. Przeprowadza inspekcję trzech niezgodnych kont magazynu, wskutek zmiany ich Stanów na **niezgodne.**

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Diagram kont magazynu narażonych na sieci publiczne w grupie zasobów contoso R G." border="false":::
   Diagram przedstawiający obrazy pięciu kont magazynu w grupie zasobów contoso R G. Konta magazynu jedno i trzy z nich mają teraz zielone znaczniki wyboru poniżej, natomiast konta magazynu dwa, cztery i pięć mają teraz czerwone znaki ostrzegawcze poniżej.
:::image-end:::

Oprócz **zgodnych** i **niezgodnych**zasad i zasobów są cztery inne stany:

- **Wykluczone**: zasób znajduje się w zakresie przypisania, ale ma [zdefiniowane wykluczenie](../concepts/exemption-structure.md).
- **Konflikt**: istnieją co najmniej dwie definicje zasad z regułami powodującymi konflikt. Na przykład dwie definicje dołączają ten sam tag z różnymi wartościami.
- **Nierozpoczęte**: cykl oceniania nie został uruchomiony dla zasad lub zasobów.
- **Nie zarejestrowano**: dostawca zasobów Azure Policy nie został zarejestrowany lub zalogowane konto nie ma uprawnień do odczytu danych zgodności.

Azure Policy używa pól **typu**, **nazwy**lub **rodzaju** w definicji, aby określić, czy zasób jest zgodny. Gdy zasób jest zgodny, jest uznawany za stosowany i ma status **zgodne**, **niezgodne**lub **wykluczone**. Jeśli **Typ**, **Nazwa**lub **rodzaj** jest jedyną właściwością w definicji, wszystkie uwzględnione i niewykluczone zasoby są uważane za stosowane i są oceniane.

Wartość procentowa zgodności jest określana przez podzielenie zasobów **zgodnych** i **wykluczonych** przez _Łączne zasoby_. _Łączna liczba zasobów_ jest definiowana jako suma **zgodnych**, **niezgodnych**, **zwolnionych**i **sprzecznych** zasobów. Ogólne numery zgodności są sumą odrębnych zasobów, które są **zgodne** lub **wykluczone** przez sumę wszystkich odrębnych zasobów. Na poniższej ilustracji przedstawiono 20 odrębnych zasobów, które mają zastosowanie i tylko jeden z nich jest **niezgodny**.
Ogólna zgodność zasobów wynosi 95% (19 z 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Diagram kont magazynu narażonych na sieci publiczne w grupie zasobów contoso R G." border="false":::

> [!NOTE]
> Zgodność z przepisami w Azure Policy jest funkcją w wersji zapoznawczej. Właściwości zgodności z zestawu SDK i stron w portalu różnią się w zależności od włączonych inicjatyw. Aby uzyskać więcej informacji, zobacz [zgodność z przepisami](../concepts/regulatory-compliance.md)

## <a name="portal"></a>Portal

Azure Portal przedstawia graficzne środowisko wizualizacji i zrozumienie stanu zgodności w danym środowisku. Na stronie **zasady** opcja **Przegląd** zawiera szczegółowe informacje dotyczące dostępnych zakresów na potrzeby zgodności obu zasad i inicjatyw. Wraz ze stanem zgodności i liczbą na przypisanie zawiera wykres pokazujący zgodność w ciągu ostatnich siedmiu dni. Strona **zgodność** zawiera wiele tych samych informacji (poza wykresem), ale zapewnia dodatkowe opcje filtrowania i sortowania.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Diagram kont magazynu narażonych na sieci publiczne w grupie zasobów contoso R G." border="false":::

Ze względu na to, że zasady lub inicjatywy mogą być przypisane do różnych zakresów, tabela zawiera zakres dla każdego przypisania i typ przypisanej definicji. Podano również liczbę niezgodnych zasobów i niezgodnych zasad dla każdego przypisania. Wybór zasad i inicjatywy w tabeli zapewnia dokładniejsze sprawdzenie zgodności dla danego przypisania.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Diagram kont magazynu narażonych na sieci publiczne w grupie zasobów contoso R G." border="false":::

Lista zasobów na karcie **zgodność zasobów** zawiera stan oceny istniejących zasobów dla bieżącego przypisania. Karta domyślnie nie jest **zgodna**, ale można ją filtrować.
Zdarzenia (append, Audit, Deny, Deploy, Modify) wyzwalane przez żądanie utworzenia zasobu są wyświetlane na karcie **zdarzenia** .

> [!NOTE]
> W przypadku zasad aparatu AKS wyświetlony zasób jest grupą zasobów.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Diagram kont magazynu narażonych na sieci publiczne w grupie zasobów contoso R G." border="false":::

<a name="component-compliance"></a> W przypadku zasobów [trybu dostawcy zasobów](../concepts/definition-structure.md#resource-provider-modes) na karcie **zgodność zasobów** wybierz zasób lub kliknij prawym przyciskiem myszy wiersz i wybierz polecenie **Wyświetl szczegóły zgodności** , aby otworzyć Szczegóły zgodności składnika. Ta strona zawiera również karty umożliwiające wyświetlanie zasad przypisanych do tego zasobu, zdarzeń, zdarzeń składników i historii zmian.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Diagram kont magazynu narażonych na sieci publiczne w grupie zasobów contoso R G." border="false":::

Wróć do strony zgodność zasobów, kliknij prawym przyciskiem myszy wiersz zdarzenia, dla którego chcesz zebrać więcej szczegółów, a następnie wybierz pozycję **Pokaż dzienniki aktywności**. Zostanie otwarta strona dziennika aktywności, która jest wstępnie filtrowana do wyszukiwania pokazującego szczegóły dotyczące przypisania i zdarzeń. Dziennik aktywności zawiera dodatkowy kontekst i informacje dotyczące tych zdarzeń.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Diagram kont magazynu narażonych na sieci publiczne w grupie zasobów contoso R G." border="false":::

### <a name="understand-non-compliance"></a>Zrozumienie braku zgodności

Jeśli zasób jest określony jako **niezgodny**, istnieje wiele możliwych przyczyn. Aby określić przyczynę braku zgodności zasobu lub aby znaleźć zmianę odpowiedzialną, zobacz [Określanie, które nie](./determine-non-compliance.md)są **zgodne** .

## <a name="command-line"></a>Wiersz polecenia

Te same informacje dostępne w portalu można pobrać przy użyciu interfejsu API REST (w tym z [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Aby uzyskać szczegółowe informacje na temat interfejsu API REST, zobacz informacje dotyczące [Azure Policy Insights](/rest/api/policy-insights/) . Strony referencyjne interfejsu API REST mają zielony przycisk "Wypróbuj go" dla każdej operacji, która pozwala na wypróbowanie jej w przeglądarce.

Użyj ARMClient lub podobnego narzędzia do obsługi uwierzytelniania na platformie Azure na potrzeby przykładów interfejsu API REST.

### <a name="summarize-results"></a>Podsumowanie wyników

W przypadku interfejsu API REST podsumowanie może być wykonywane przez kontener, definicję lub przypisanie. Oto przykład podsumowania na poziomie subskrypcji przy użyciu [podsumowania Azure Policy wgląd w subskrypcję](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

Dane wyjściowe podsumowują subskrypcję. W poniższym przykładzie danych wyjściowych Podsumowanie zgodności ma **wartość. results. nonCompliantResources** i **Value. results. nonCompliantPolicies**. To żądanie zawiera dalsze szczegóły, w tym każde przypisanie, które wprowadziło niezgodne numery i informacje o definicji dla każdego przypisania. Każdy obiekt zasad w hierarchii zawiera element **queryResultsUri** , którego można użyć do uzyskania dodatkowych szczegółów na tym poziomie.

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

### <a name="query-for-resources"></a>Zapytanie o zasoby

W powyższym przykładzie **wartość. policyAssignments. policyDefinitions. results. queryResultsUri** zawiera przykładowy identyfikator URI dla wszystkich niezgodnych zasobów dla określonej definicji zasad. Patrząc na **$Filter** wartość ComplianceState jest równa (EQ) do "niezgodne", PolicyAssignmentId jest określona dla definicji zasad, a następnie sama PolicyDefinitionId. Powodem dołączenia PolicyAssignmentId w filtrze jest fakt, że PolicyDefinitionId może istnieć w kilku przypisaniach zasad lub inicjatyw z różnymi zakresami. Określając zarówno PolicyAssignmentId, jak i PolicyDefinitionId, możemy być jawne w wynikach, których szukamy. Wcześniej w przypadku usługi PolicyStates, która była używana w **najnowszej wersji**, która automatycznie ustawia okno **od** i **do** godziny z ostatnich 24 godzin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Przykładowa odpowiedź poniżej została przycięta do jednego niezgodnego zasobu dla zwięzłości. Szczegółowa odpowiedź zawiera kilka danych dotyczących zasobu, zasad lub inicjatywy oraz przydziału. Należy zauważyć, że parametry przypisania zostały przekazano do definicji zasad.

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

Po utworzeniu lub zaktualizowaniu zasobu zostanie wygenerowane wyniki oceny zasad. Wyniki są nazywane _zdarzeniami zasad_. Użyj poniższego identyfikatora URI, aby wyświetlić ostatnie zdarzenia zasad skojarzone z subskrypcją.

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

Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących zdarzeń zasad, zobacz artykuł dotyczący [zdarzeń Azure Policy](/rest/api/policy-insights/policyevents) .

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Grupa poleceń [interfejsu wiersza polecenia platformy Azure](/cli/azure/what-is-azure-cli) dla Azure Policy obejmuje większość operacji, które są dostępne w trybie REST lub Azure PowerShell. Aby zapoznać się z pełną listą dostępnych poleceń, zobacz [interfejs wiersza polecenia platformy Azure — omówienie Azure Policy](/cli/azure/policy).

Przykład: pobieranie podsumowania stanu dla najwyżej przypisanych zasad o największej liczbie niezgodnych zasobów.

```azurecli-interactive
az policy state summarize --top 1
```

Górna część odpowiedzi wygląda podobnie do tego przykładu:

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

Przykład: Pobieranie rekordu stanu dla ostatnio obliczonego zasobu (wartość domyślna to sygnatura czasowa w kolejności malejącej).

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

Przykład: pobieranie zdarzeń związanych z niezgodnymi zasobami sieci wirtualnej, które wystąpiły po określonej dacie.

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

Moduł Azure PowerShell dla Azure Policy jest dostępny w Galeria programu PowerShell jako [AZ. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights). Za pomocą PowerShellGet można zainstalować moduł przy użyciu programu (Upewnij się, `Install-Module -Name Az.PolicyInsights` że zainstalowano najnowszą [Azure PowerShell](/powershell/azure/install-az-ps) ):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Moduł zawiera następujące polecenia cmdlet:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Przykład: pobieranie podsumowania stanu dla najwyżej przypisanych zasad o największej liczbie niezgodnych zasobów.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Przykład: Pobieranie rekordu stanu dla ostatnio obliczonego zasobu (wartość domyślna to sygnatura czasowa w kolejności malejącej).

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

Przykład: pobieranie zdarzeń związanych z niezgodnymi zasobami sieci wirtualnej, które wystąpiły po określonej dacie, konwertowanie na obiekt CSV i eksportowanie do pliku.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

Dane wyjściowe `$policyEvents` obiektu wyglądają następująco:

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

Pole **PrincipalOid** może służyć do uzyskiwania określonego użytkownika za pomocą polecenia cmdlet Azure PowerShell `Get-AzADUser` . Zastąp **{principalOid}** odpowiedzią uzyskaną z poprzedniego przykładu.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Jeśli masz [obszar roboczy log Analytics](../../../azure-monitor/log-query/log-query-overview.md) z `AzureActivity` [rozwiązania Activity Log Analytics](../../../azure-monitor/platform/activity-log.md) powiązanego z subskrypcją, możesz również wyświetlić wyniki niezgodności z oceny nowych i zaktualizowanych zasobów przy użyciu prostych zapytań Kusto i `AzureActivity` tabeli. Dzięki szczegółowym dziennikom Azure Monitor alerty można skonfigurować tak, aby oglądać niezgodność.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Diagram kont magazynu narażonych na sieci publiczne w grupie zasobów contoso R G." border="false":::

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
