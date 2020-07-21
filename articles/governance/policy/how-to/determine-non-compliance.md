---
title: Ustalanie przyczyn niezgodności
description: Jeśli zasób nie jest zgodny, istnieje wiele możliwych przyczyn. Dowiedz się, co spowodowało niezgodność.
ms.date: 07/06/2020
ms.topic: how-to
ms.openlocfilehash: ee027ff0f2936dc3eb7153869c52d4e70c83918b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512031"
---
# <a name="determine-causes-of-non-compliance"></a>Ustalanie przyczyn niezgodności

Jeśli zasób platformy Azure jest określony jako niezgodny z regułą zasad, warto zrozumieć, która część reguły nie jest zgodna z zasobem. Warto również zrozumieć, jakie zmiany zmieniły wcześniej zgodne zasoby, aby nie były zgodne. Istnieją dwa sposoby znajdowania tych informacji:

- [Szczegóły zgodności](#compliance-details)
- [Historia zmian (wersja zapoznawcza)](#change-history)

## <a name="compliance-details"></a>Szczegóły zgodności

Jeśli zasób nie jest zgodny, szczegóły zgodności dla tego zasobu są dostępne na stronie **zgodność z zasadami** . Okienko Szczegóły zgodności zawiera następujące informacje:

- Szczegóły zasobu, takie jak nazwa, typ, lokalizacja i identyfikator zasobu
- Stan zgodności i sygnatura czasowa ostatniej oceny bieżącego przydziału zasad
- Lista _przyczyn_ braku zgodności zasobów

> [!IMPORTANT]
> Ponieważ szczegóły zgodności dla _niezgodnego_ zasobu przedstawiają bieżącą wartość właściwości tego zasobu, użytkownik musi mieć operację **odczytu** do **typu** zasobu. Na przykład jeśli _niezgodny_ zasób to **Microsoft. COMPUTE/virtualMachines** , użytkownik musi mieć operację **Microsoft. COMPUTE/virtualMachines/Read** . Jeśli użytkownik nie ma wymaganej operacji, zostanie wyświetlony komunikat o błędzie dostępu.

Aby wyświetlić szczegóły zgodności, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

1. Na stronie **Przegląd** lub **zgodność** wybierz zasady w **stanie zgodności** , które _nie są zgodne_.

1. Na karcie **zgodność zasobów** na stronie **zgodność z zasadami** kliknij prawym przyciskiem myszy lub wybierz wielokropek zasobu w **stanie zgodności** , który jest _niezgodny_. Następnie wybierz pozycję **Wyświetl szczegóły zgodności**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Wyświetl szczegóły zgodności" border="false":::

1. W okienku **szczegóły zgodności** są wyświetlane informacje z najnowszej wersji ewaluacyjnej zasobu do bieżącego przypisania zasad. W tym przykładzie można znaleźć wartość pola **Microsoft. SQL/Server/version** na _12,0_ , gdy oczekiwano definicji zasad _14,0_. Jeśli zasób nie jest zgodny z wieloma przyczynami, każda z nich jest wymieniona w tym okienku.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Okienko Szczegóły zgodności i przyczyny braku zgodności" border="false":::

   W przypadku definicji zasad **auditIfNotExists** lub **deployIfNotExists** szczegóły obejmują Właściwość **details. Type** i wszelkie opcjonalne właściwości. Aby uzyskać listę, zobacz [Właściwości auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) i [deployIfNotExists](../concepts/effects.md#deployifnotexists-properties). **Ostatni szacowany zasób** jest powiązanym zasobem z sekcji **szczegółów** definicji.

   Przykładowa częściowa definicja **deployIfNotExists** :

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Okienko Szczegóły zgodności-* ifNotExists" border="false":::

> [!NOTE]
> Aby chronić dane, gdy wartość właściwości jest _tajna_ , bieżąca wartość Wyświetla gwiazdki.

Te szczegóły wyjaśniają, dlaczego zasób jest obecnie niezgodny, ale nie powinien być wyświetlany, gdy zmiana została wprowadzona w zasobie, która spowodowała niezgodność. Aby uzyskać te informacje, zobacz sekcję [Zmień historię (wersja zapoznawcza)](#change-history) poniżej.

### <a name="compliance-reasons"></a>Przyczyny zgodności

Poniższa macierz odwzorowuje każdy możliwy _powód_ do [warunku](../concepts/definition-structure.md#conditions) odpowiedzialnego w definicji zasad:

|Przyczyna | Warunek |
|-|-|
|Bieżąca wartość musi zawierać wartość docelową jako klucz. |ContainsKey — lub **nie** notContainsKey |
|Bieżąca wartość musi zawierać wartość docelową. |zawiera lub **nie** notContains |
|Bieżąca wartość musi być równa wartości docelowej. |równa się lub **nie** notEquals |
|Bieżąca wartość musi być mniejsza niż wartość docelowa. |mniej lub **nie** greaterOrEquals |
|Bieżąca wartość musi być większa lub równa wartości docelowej. |greaterOrEquals lub **nie** mniej |
|Bieżąca wartość musi być większa niż wartość docelowa. |większe lub **nie** lessOrEquals |
|Bieżąca wartość nie może być większa niż wartość docelowa. |lessOrEquals lub **nie jest** większa |
|Bieżąca wartość musi istnieć. |istniejący |
|Bieżąca wartość musi należeć do wartości docelowej. |w programie **lub notIn** |
|Bieżąca wartość musi być taka sama jak wartość docelowa. |Podobnie jak **notLike** |
|Bieżąca wartość musi uwzględniać wielkość liter, dopasowując wartość docelową. |dopasowanie lub **notMatch** |
|Bieżąca wartość musi uwzględniać wielkość liter, dopasowując wartość docelową. |matchInsensitively lub **nie** notMatchInsensitively |
|Bieżąca wartość nie może zawierać wartości docelowej jako klucza. |notContainsKey lub **nie** ContainsKey —|
|Bieżąca wartość nie może zawierać wartości docelowej. |notContains lub **nie** zawiera |
|Bieżąca wartość nie może być równa wartości docelowej. |notEquals lub **nie** równa się |
|Bieżąca wartość nie może istnieć. |**nie** istnieje  |
|Bieżąca wartość nie może być wartością docelową. |notIn lub **nie** w |
|Bieżąca wartość nie może być taka sama jak wartość docelowa. |notLike lub **nie** Lubię |
|Bieżąca wartość nie może uwzględniać wielkości liter pasującej do wartości docelowej. |notMatch lub **nie jest** zgodny |
|Bieżąca wartość nie może uwzględniać wielkości liter odpowiadającej wartości docelowej. |notMatchInsensitively lub **nie** matchInsensitively |
|Żadne powiązane zasoby nie pasują do szczegółów efektu w definicji zasad. |Zasób typu zdefiniowanego w elemencie **then. details. Type** i powiązany z zasobem zdefiniowanym w instrukcji **if** w regule zasad nie istnieje. |

## <a name="compliance-details-for-guest-configuration"></a>Szczegóły zgodności dla konfiguracji gościa

W przypadku zasad _auditIfNotExistsymi_ w kategorii _Konfiguracja gościa_ może istnieć wiele ustawień ocenianych wewnątrz maszyny wirtualnej i trzeba będzie wyświetlić szczegółowe informacje na temat ustawień. Na przykład jeśli przeprowadzasz inspekcję listy zasad haseł, a tylko jeden z nich ma stan _niezgodny_, musisz wiedzieć, które zasady haseł nie są zgodne i dlaczego.

Użytkownik może również nie mieć dostępu do bezpośredniego logowania się do maszyny wirtualnej, ale należy zgłosić, dlaczego maszyna wirtualna nie jest _zgodna_.

### <a name="azure-portal"></a>Azure Portal

Zacznij od wykonania tych samych kroków w powyższej sekcji, aby wyświetlić szczegóły zgodności zasad.

W widoku Szczegóły zgodności kliknij pozycję Połącz **ostatnio obliczony zasób**.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Wyświetl szczegóły definicji auditIfNotExists" border="false":::

Na stronie **przypisanie gościa** są wyświetlane wszystkie dostępne szczegóły zgodności. Każdy wiersz w widoku reprezentuje ocenę, która została wykonana w ramach maszyny. W kolumnie **Przyczyna** zostanie wyświetlona fraza opisująca, dlaczego przypisanie gościa _nie jest zgodne_. Na przykład w przypadku inspekcji zasad haseł w kolumnie **Przyczyna** zostanie wyświetlony tekst zawierający bieżącą wartość dla każdego ustawienia.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Wyświetl szczegóły zgodności" border="false":::

### <a name="azure-powershell"></a>Azure PowerShell

Możesz również wyświetlić szczegóły zgodności z Azure PowerShell. Najpierw upewnij się, że moduł konfiguracji gościa jest zainstalowany.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Bieżący stan wszystkich przypisań gościa dla maszyny wirtualnej można wyświetlić przy użyciu następującego polecenia:

```azurepowershell-interactive
Get-AzVMGuestPolicyStatus -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Aby wyświetlić tylko frazę _przyczyny_ opisującą dlaczego maszyna wirtualna jest _niezgodna_, zwróć tylko Właściwość podrzędną przyczyna.

```azurepowershell-interactive
Get-AzVMGuestPolicyStatus -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Istnieje również możliwość wyprowadzania historii zgodności dla przypisań Gości w zakresie dla komputera. Dane wyjściowe tego polecenia zawierają szczegóły każdego raportu dla maszyny wirtualnej.

> [!NOTE]
> Dane wyjściowe mogą zwrócić dużą ilość danych. Zaleca się przechowywanie danych wyjściowych w zmiennej.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Aby uprościć ten widok, użyj parametru **ShowChanged** . Dane wyjściowe tego polecenia zawierają tylko raporty, które zostały wykonane ze zmianą stanu zgodności.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="change-history-preview"></a><a name="change-history"></a>Historia zmian (wersja zapoznawcza)

W ramach nowej **publicznej wersji zapoznawczej**historia zmian jest dostępna dla wszystkich zasobów platformy Azure, które obsługują [usuwanie w trybie pełnym](../../../azure-resource-manager/templates/complete-mode-deletion.md). Historia zmian zawiera szczegółowe informacje o tym, kiedy wykryto zmianę i _różnicą wizualną_ dla każdej zmiany. Wykrywanie zmian jest wyzwalane po dodaniu, usunięciu lub zmianie Azure Resource Manager właściwości.

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

1. Na stronie **Przegląd** lub **zgodność** wybierz zasady w dowolnym **stanie zgodności**.

1. Na karcie **zgodność zasobów** na stronie **zgodność z zasadami** wybierz zasób.

1. Na stronie **zgodność zasobów** wybierz kartę **historia zmian (wersja zapoznawcza)** . Zostanie wyświetlona lista wykrytych zmian (jeśli istnieją).

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Karta historia zmian Azure Policy na stronie zgodności zasobów" border="false":::

1. Wybierz jedną z wykrytych zmian. _Różnica wizualna_ dla zasobu jest wyświetlana na stronie **historia zmian** .

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Na stronie historii zmian Azure Policy Zmień wizualną różnicę historii" border="false":::

_Wizualna różnica_ aides w identyfikacji zmian w zasobie. Wykryte zmiany mogą nie być powiązane z bieżącym stanem zgodności zasobu.

Dane historii zmian są udostępniane przez [usługę Azure Resource Graph](../../resource-graph/overview.md). Aby zbadać te informacje poza Azure Portal, zobacz [pobieranie zmian zasobów](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
