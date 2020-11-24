---
title: Migrowanie Azure Monitor Application Insights zasobów klasycznych do zasobów opartych na obszarze roboczym | Microsoft Docs
description: Informacje o krokach wymaganych do uaktualnienia Azure Monitor Application Insights zasobów klasycznych do nowego modelu opartego na obszarze roboczym.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 709cff1326bb6393a14c594ea434a6c16fb80860
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536527"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>Migrowanie do zasobów Application Insights opartych na obszarze roboczym

Ten przewodnik przeprowadzi Cię przez proces migrowania klasycznego zasobu Application Insights do zasobów opartych na obszarze roboczym. Zasoby oparte na obszarze roboczym obsługują pełną integrację między Application Insights i Log Analytics. Zasoby oparte na obszarze roboczym wysyłają Application Insights dane telemetryczne do wspólnego Log Analytics obszaru roboczego, dzięki czemu można uzyskiwać dostęp do [najnowszych funkcji Azure monitor](#new-capabilities) podczas utrzymywania dzienników aplikacji, infrastruktury i platformy w pojedynczej skonsolidowanej lokalizacji.

Zasoby oparte na obszarze roboczym umożliwiają wspólną kontrolę dostępu opartą na rolach (Azure RBAC) w ramach Twoich zasobów i eliminuje konieczność wykonywania zapytań między aplikacjami i obszarami roboczymi.

**Zasoby oparte na obszarze roboczym są obecnie dostępne we wszystkich regionach komercyjnych i dla instytucji rządowych USA platformy Azure**

## <a name="new-capabilities"></a>Nowe możliwości

Application Insights oparte na obszarze roboczym pozwala korzystać ze wszystkich najnowszych możliwości Azure Monitor i Log Analytics w tym:

* [Klucze zarządzane przez klienta (CMK)](../platform/customer-managed-keys.md) zapewniają szyfrowanie dla danych za pomocą kluczy szyfrowania, do których tylko masz dostęp.
* [Link prywatny platformy Azure](../platform/private-link-security.md) umożliwia bezpieczne łączenie usług Azure PaaS z siecią wirtualną za pomocą prywatnych punktów końcowych.
* Udostępnienie [własnego magazynu (BYOS) dla programu Profiler, a Snapshot Debugger](./profiler-bring-your-own-storage.md) zapewnia pełną kontrolę nad zasadami szyfrowania w trybie spoczynku, zasadami zarządzania istnieniem i dostępem sieciowym dla wszystkich danych skojarzonych z Application Insights Profiler i Snapshot Debugger. 
* [Warstwy rezerwacji zdolności produkcyjnych](../platform/manage-cost-storage.md#pricing-model) pozwalają zaoszczędzić o 25% w porównaniu z ceną płatność zgodnie z rzeczywistym użyciem. 
* Szybsze pozyskiwanie danych przy użyciu pozyskiwania strumieniowego Log Analytics.

## <a name="migration-process"></a>Proces migracji

Podczas migracji do zasobów opartych na obszarze roboczym nie są przesyłane żadne dane z magazynu zasobów klasycznych do nowego magazynu opartego na obszarze roboczym. Wybranie migracji spowoduje zmianę lokalizacji, w której nowe dane są zapisywane w obszarze roboczym Log Analytics przy zachowaniu dostępu do klasycznych danych zasobów. 

Twoje klasyczne dane zasobów będą utrwalane i podlegają ustawieniom przechowywania dla klasycznego zasobu Application Insights. Wszystkie nowe dane pozyskiwane po migracji będą podlegać [ustawieniom przechowywania](../platform/manage-cost-storage.md#change-the-data-retention-period) skojarzonego obszaru roboczego log Analytics, który również obsługuje [różne ustawienia przechowywania według typu danych](../platform/manage-cost-storage.md#retention-by-data-type).
Proces migracji jest **trwały i nie można go cofnąć**. Po przeprowadzeniu migracji zasobu do Application Insights opartego na obszarze roboczym zawsze będzie to zasób oparty na obszarze roboczym. Jednak po przeprowadzeniu migracji można zmienić docelowy obszar roboczy tak często, jak jest to konieczne. 

> [!NOTE]
> Pozyskiwanie i przechowywanie danych dla zasobów Application Insights opartych na obszarze roboczym jest [rozliczane za pośrednictwem log Analytics obszaru roboczego](../platform/manage-cost-storage.md) , w którym znajdują się dane. W przypadku wybrania przechowywania danych przez ponad 90 dni przed rozpoczęciem migracji dane pobrane do klasycznego Application Insights zasobów i przechowywania danych będą nadal rozliczane w ramach tego zasobu Application Insights. [Dowiedz się więcej]( ./pricing.md#workspace-based-application-insights) o rozliczeniach dla zasobów Application Insights opartych na obszarze roboczym.

Jeśli nie musisz migrować istniejącego zasobu i chcesz utworzyć nowy zasób Application Insights oparty na obszarze roboczym, użyj [przewodnika tworzenia zasobów opartego na obszarze roboczym](create-workspace-resource.md).

## <a name="pre-requisites"></a>Wymagania wstępne 

- Obszar roboczy Log Analytics z trybem kontroli dostępu ustawionym na **`use resource or workspace permissions`** ustawienie. 

    - Zasoby Application Insights oparte na obszarze roboczym nie są zgodne z obszarami roboczymi ustawionymi na ustawienie dedykowane **`workspace based permissions`** . Aby dowiedzieć się więcej na temat kontroli dostępu do obszaru roboczego Log Analytics, zapoznaj się z tematem [log Analytics skonfigurować wskazówki dotyczące trybu kontroli dostępu](../platform/manage-access.md#configure-access-control-mode)

    - Jeśli nie masz jeszcze istniejącego obszaru roboczego Log Analytics, [zapoznaj się z dokumentacją dotyczącą tworzenia obszaru roboczego log Analytics](../learn/quick-create-workspace.md).
    
- Eksport ciągły nie jest obsługiwany w przypadku zasobów opartych na obszarze roboczym i musi być wyłączony.
Po zakończeniu migracji można użyć [ustawień diagnostycznych](../platform/diagnostic-settings.md) w celu skonfigurowania archiwizowania danych na koncie magazynu lub przesyłania strumieniowego do usługi Azure Event Hub.  

- Sprawdź bieżące ustawienia przechowywania w obszarze **Ogólne**  >  **użycie i szacowane koszty**  >  **przechowywania danych** dla obszaru roboczego log Analytics. To ustawienie będzie miało wpływ na to, jak długo nowe pozyskiwane dane są przechowywane po przeprowadzeniu migracji zasobu Application Insights. Jeśli dane Application Insights są obecnie przechowywane przez czas dłuższy niż domyślna 90 dni i chcesz zachować ten większy okres przechowywania, może być konieczne dostosowanie ustawień przechowywania obszaru roboczego.

## <a name="migrate-your-resource"></a>Migrowanie zasobu

Ta sekcja przeprowadzi Cię przez proces migrowania klasycznego zasobu Application Insights do nowego typu zasobów opartego na obszarze roboczym.

1. Z poziomu zasobów Application Insights wybierz pozycję **Właściwości** w obszarze **Konfiguruj** nagłówek na pasku menu po lewej stronie.

    ![Właściwości wyróżnione czerwoną ramką](./media/convert-classic-resource/properties.png)

2. Wybierz pozycję **`Migrate to Workspace-based`**.
    
     ![Przycisk migrowania zasobu](./media/convert-classic-resource/migrate.png)

3. Wybierz obszar roboczy Log Analytics, w którym chcesz przechowywać wszystkie przyszłe Application Insights dane telemetryczne.

     ![Interfejs użytkownika Kreatora migracji z opcją wyboru obszaru roboczego targe](./media/convert-classic-resource/migration.png)
    

Po przeprowadzeniu migracji zasobu zobaczysz odpowiednie informacje o obszarze roboczym w okienku **Przegląd** :

![Nazwa obszaru roboczego](./media/create-workspace-resource/workspace-name.png)

Kliknięcie niebieskiego linku spowoduje przejście do skojarzonego obszaru roboczego Log Analytics, w którym można wykorzystać nowe środowisko zapytań ujednoliconego obszaru roboczego.

## <a name="understanding-log-queries"></a>Informacje o zapytaniach dziennika

Firma Microsoft zapewnia pełną zgodność z poprzednimi wersjami Application Insights klasycznych zapytań zasobów, skoroszytów i alertów opartych na dziennikach w ramach Application Insights środowiska. 

Aby pisać zapytania względem [nowej struktury lub schematu tabeli opartej na obszarze roboczym](apm-tables.md), musisz najpierw przejść do obszaru roboczego log Analytics. 

Gdy wykonujesz zapytanie bezpośrednio z interfejsu użytkownika Log Analytics w obszarze roboczym, zobaczysz tylko te dane, które są pozyskiwane po migracji. Aby zobaczyć zarówno dane klasyczne Application Insights, jak i nowe dane pozyskiwane po migracji w ujednoliconym środowisku zapytań, użyj widoku zapytania dzienniki (analiza) z poziomu zmigrowanego zasobu Application Insights.

## <a name="programmatic-resource-migration"></a>Migracja zasobów programistycznych

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby uzyskać dostęp do Application Insights poleceń interfejsu wiersza polecenia platformy Azure w wersji zapoznawczej, należy najpierw wykonać następujące działania:

```azurecli
 az extension add -n application-insights
```

Jeśli polecenie nie zostanie uruchomione `az extension add` , zostanie wyświetlony komunikat o błędzie informujący o tym, że: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Teraz można wykonać następujące czynności, aby utworzyć zasób Application Insights:

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>Przykład

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Aby zapoznać się z pełną dokumentacją interfejsu wiersza polecenia platformy Azure dotyczącą tego przykładu, zapoznaj się z [dokumentacją interfejsu CLI platformy Azure](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-update)

### <a name="azure-powershell"></a>Azure PowerShell

`Update-AzApplicationInsights`Polecenie programu PowerShell nie obsługuje obecnie migrowania klasycznego zasobu Application Insights do obszaru roboczego. Aby utworzyć zasób oparty na obszarze roboczym przy użyciu programu PowerShell, możesz użyć poniższych szablonów Azure Resource Manager i wdrożyć program PowerShell.

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

#### <a name="template-file"></a>Plik szablonu

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Plik parametrów

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Modyfikowanie skojarzonego obszaru roboczego

Po utworzeniu zasobu Application Insights opartego na obszarze roboczym można zmodyfikować skojarzony obszar roboczy Log Analytics.

W okienku zasobów Application Insights wybierz pozycję **Właściwości**  >  **Zmień obszar roboczy**  >  **log Analytics obszary robocze**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="access-mode"></a>Tryb dostępu

**Komunikat o błędzie:** *wybrany obszar roboczy jest skonfigurowany z trybem dostępu opartym na obszarze roboczym. Może to mieć wpływ na niektóre funkcje APM. Wybierz inny obszar roboczy lub Zezwalaj na dostęp oparty na zasobach w ustawieniach obszaru roboczego. Ten błąd można zastąpić za pomocą interfejsu wiersza polecenia.* 

Aby zasób Application Insights oparty na obszarze roboczym działał prawidłowo, należy zmienić tryb kontroli dostępu dla docelowego obszaru roboczego Log Analytics do ustawienia **uprawnienia zasobu lub obszaru roboczego** . To ustawienie znajduje się w interfejsie użytkownika obszaru roboczego log Analytics w obszarze **Właściwości**  >  **Tryb kontroli dostępu**. Aby uzyskać szczegółowe instrukcje, zapoznaj się z tematem [log Analytics skonfigurować wskazówki dotyczące trybu kontroli dostępu](../platform/manage-access.md#configure-access-control-mode). Jeśli tryb kontroli dostępu zostanie ustawiony na wyłączne ustawienie **uprawnień obszaru roboczego wymagane** , migracja za pośrednictwem środowiska migracji portalu pozostanie zablokowana.

Jeśli nie możesz zmienić trybu kontroli dostępu ze względów bezpieczeństwa dla bieżącego docelowego obszaru roboczego, zalecamy utworzenie nowego obszaru roboczego Log Analytics do użycia podczas migracji. 

### <a name="continuous-export"></a>Eksport ciągły

**Komunikat o błędzie:** *eksport ciągły musi zostać wyłączony przed kontynuowaniem. Po migracji Użyj ustawień diagnostycznych do eksportowania.* 

Starsza Funkcja eksportu ciągłego nie jest obsługiwana w przypadku zasobów opartych na obszarze roboczym. Przed migracją należy wyłączyć eksport ciągły.

1. W widoku zasobów Application Insights w obszarze **Konfiguruj** nagłówek wybierz pozycję **eksport ciągły**.

    ![Element menu eksportu ciągłego](./media/convert-classic-resource/continuous-export.png)

2. Wybierz pozycję **Wyłącz**.

    ![Przycisk wyłączenia eksportu ciągłego](./media/convert-classic-resource/disable.png)

- Po wybraniu opcję Wyłącz możesz przejść z powrotem do interfejsu użytkownika migracji. Jeśli strona Edytuj eksport ciągły wyświetli monit z pytaniem, że ustawienia nie zostaną zapisane, możesz wybrać opcję OK dla tego monitu, ponieważ nie dotyczy to wyłączenia/włączenia eksportu ciągłego.

- Po pomyślnym przeprowadzeniu migracji zasobu Application Insights do obszaru roboczego można użyć ustawień diagnostycznych, aby zastąpić funkcje, które są używane do dostarczania ciągłego eksportu. Wybierz pozycję **Ustawienia diagnostyczne**  >  **Dodaj ustawienie diagnostyczne** z poziomu zasobu Application Insights. Można wybrać wszystkie tabele lub podzbiór tabel do zarchiwizowania na koncie magazynu lub do przesyłania strumieniowego do centrum zdarzeń platformy Azure. Aby uzyskać szczegółowe wskazówki na temat ustawień diagnostycznych, zapoznaj się z tematem [wskazówki dotyczące ustawień diagnostycznych Azure monitor](../platform/diagnostic-settings.md).

### <a name="retention-settings"></a>Ustawienia przechowywania

**Komunikat ostrzegawczy:** *dostosowane ustawienia przechowywania Application Insights nie będą miały zastosowania do danych wysyłanych do obszaru roboczego. Konieczne będzie ponowne skonfigurowanie tego programu osobno.*

Nie musisz wprowadzać żadnych zmian przed migracją, ale ten komunikat ma na celu wygenerowanie alertu informującego, że bieżące ustawienia przechowywania Application Insights nie są ustawione na domyślny 90-dniowy okres przechowywania. Ten komunikat ostrzegawczy oznacza, że warto zmodyfikować ustawienia przechowywania dla obszaru roboczego Log Analytics przed migracją i rozpoczęciem pozyskiwania nowych danych. 

Bieżące ustawienia przechowywania można sprawdzić dla log Analytics w obszarze **Ogólne**  >  **użycie i szacowane koszty**  >  **przechowywania danych** z poziomu interfejsu użytkownika log Analytics. To ustawienie będzie miało wpływ na to, jak długo nowe pozyskiwane dane są przechowywane po przeprowadzeniu migracji zasobu Application Insights.

## <a name="next-steps"></a>Następne kroki

* [Eksploruj metryki](../platform/metrics-charts.md)
* [Pisanie zapytań analitycznych](../log-query/log-query-overview.md)
