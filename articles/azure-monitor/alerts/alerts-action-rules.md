---
title: Reguły akcji dla alertów Azure Monitor
description: Informacje o regułach akcji w Azure Monitor są i sposobami ich konfigurowania i zarządzania nimi.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 07d179f557671a515a7933b64a25e6d41f75219b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045619"
---
# <a name="action-rules-preview"></a>Reguły akcji (wersja zapoznawcza)

Reguły akcji ułatwiają Definiowanie lub pomijanie akcji w dowolnym zakresie Azure Resource Manager (subskrypcja platformy Azure, Grupa zasobów lub zasób docelowy). Mają różne filtry, które ułatwiają zawężenie określonego podzestawu wystąpień alertów, na których ma być wykonywane działanie.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Dlaczego i kiedy należy używać reguł akcji?

### <a name="suppression-of-alerts"></a>Pomijanie alertów

Istnieje wiele scenariuszy, w których warto pominąć powiadomienia generowane przez alerty. Te scenariusze przedziały od pomijania podczas planowanego okna obsługi do pomijania w godzinach poza godzinami pracy. Na przykład zespół odpowiedzialny za  **ContosoVM** chce pominąć powiadomienia o alertach dla nadchodzącego weekendu, ponieważ **ContosoVM** jest w trakcie planowanej konserwacji.

Mimo że zespół może wyłączyć każdą regułę alertu, która została skonfigurowana w **ContosoVM** ręcznie (i włączyć ją ponownie po konserwacji), nie jest to prosty proces. Reguły akcji ułatwiają Definiowanie pomijania alertów na dużą skalę dzięki możliwości elastycznego konfigurowania okresu pomijania. W poprzednim przykładzie zespół może zdefiniować jedną regułę akcji w programie **ContosoVM** , która pomija wszystkie powiadomienia o alertach dla weekendu.

### <a name="actions-at-scale"></a>Akcje w skali

Chociaż reguły alertów ułatwiają zdefiniowanie grupy akcji, która jest wyzwalana po wygenerowaniu alertu, klienci często mają wspólną grupę akcji w zakresie operacji. Na przykład zespół odpowiedzialny za grupę zasobów **ContosoRG** prawdopodobnie określi tę samą grupę akcji dla wszystkich reguł alertów zdefiniowanych w ramach **ContosoRG**.

Reguły akcji ułatwiają uproszczenie tego procesu. Definiując akcje w skali, można wyzwolić grupę akcji dla dowolnego alertu, który jest generowany w skonfigurowanym zakresie. W poprzednim przykładzie zespół może zdefiniować jedną regułę akcji w programie **ContosoRG** , która będzie wyzwalać tę samą grupę akcji dla wszystkich alertów wygenerowanych w ramach tego elementu.

> [!NOTE]
> Reguły akcji nie mają obecnie zastosowania do alertów Azure Service Health.

## <a name="configuring-an-action-rule"></a>Konfigurowanie reguły akcji

### <a name="portal"></a>[Portal](#tab/portal)

Dostęp do tej funkcji można uzyskać, wybierając pozycję **Zarządzaj akcjami** na stronie miejsce docelowe **alertów** w Azure monitor. Następnie wybierz pozycję **reguły akcji (wersja zapoznawcza)**. Możesz uzyskać dostęp do reguł, wybierając pozycję **reguły akcji (wersja zapoznawcza)** z poziomu pulpitu nawigacyjnego na stronie docelowej dla alertów.

![Reguły akcji ze strony docelowej Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Wybierz pozycję **+ Nowa reguła akcji**.

![Zrzut ekranu przedstawia stronę Zarządzanie akcjami z wyróżnionym przyciskiem Nowa reguła akcji.](media/alerts-action-rules/action-rules-new-rule.png)

Alternatywnie można utworzyć regułę akcji podczas konfigurowania reguły alertu.

![Zrzut ekranu przedstawia stronę Tworzenie reguły z wyróżnionym przyciskiem Utwórz regułę akcji.](media/alerts-action-rules/action-rules-alert-rule.png)

Teraz powinna zostać wyświetlona strona Flow dotycząca tworzenia reguł akcji. Skonfiguruj następujące elementy:

![Nowy przepływ tworzenia reguły akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Zakres

Najpierw wybierz zakres (subskrypcję platformy Azure, grupę zasobów lub zasób docelowy). Możesz również wybrać kombinację zakresów w ramach jednej subskrypcji.

![Zakres reguły akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Kryteria filtrowania

Można dodatkowo zdefiniować filtry, aby zawęzić je do określonego podzestawu alertów.

Dostępne są następujące filtry:

* **Ważność**: opcja wyboru jednej lub więcej serwerów alertów. **Ważność = Sev1** oznacza, że reguła akcji ma zastosowanie do wszystkich alertów ustawionych na Sev1.
* **Monitorowanie usługi**: filtr oparty na źródłowej usłudze monitorowania. Ten filtr jest również wielokrotnego wyboru. Na przykład **monitorowanie Service = "Application Insights"** oznacza, że reguła akcji ma zastosowanie do wszystkich alertów opartych na Application Insights.
* **Typ zasobu**: filtr oparty na określonym typie zasobu. Ten filtr jest również wielokrotnego wyboru. Na przykład **Typ zasobu = "Virtual Machines"** oznacza, że reguła akcji ma zastosowanie do wszystkich maszyn wirtualnych.
* **Identyfikator reguły alertu**: opcja filtrowania określonych reguł alertów przy użyciu identyfikatora Menedżer zasobów reguły alertu.
* **Warunek monitorowania**: filtr wystąpień alertów, które zostały **wyzwolone** lub **rozpoznane** jako warunek monitora.
* **Opis**: wyrażenie regularne (wyrażenie regularne), które definiuje dopasowanie ciągu do opisu, zdefiniowane jako część reguły alertu. Na przykład **Opis zawiera "prod"** będzie pasować do wszystkich alertów zawierających ciąg "prod" w ich opisach.
* **Kontekst alertu (ładunek)**: dopasowanie wyrażenia regularnego definiujące dopasowanie ciągu do pól kontekstu alertu w ładunku alertu. Na przykład **kontekst alertu (ładunek) zawiera "Computer-01"** będzie pasować do wszystkich alertów, których ładunki zawierają ciąg "Computer-01".

Te filtry są stosowane razem ze sobą. Na przykład, jeśli ustawisz **Typ zasobu "= Virtual Machines** i **ważność" = Sev0**, wszystkie alerty **Sev0** są filtrowane tylko na maszynach wirtualnych.

![Filtry reguł akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Pomijanie lub Konfiguracja grupy akcji

Następnie skonfiguruj regułę akcji dla opcji pomijania alertów lub grupy akcji. Nie można wybrać obu tych opcji. Konfiguracja działa na wszystkich wystąpieniach alertów, które pasują do wcześniej zdefiniowanego zakresu i filtrów.

#### <a name="suppression"></a>Pomijanie

W przypadku wybrania opcji **pomijania** skonfiguruj czas trwania pomijania akcji i powiadomień. Wybierz jedną z następujących opcji:
* **Od teraz (zawsze)**: pomija wszystkie powiadomienia na czas nieokreślony.
* **W zaplanowanym czasie**: pomija powiadomienia w określonym czasie trwania.
* **Z cyklem**: pomija powiadomienia w cyklicznym harmonogramie codziennie, co tydzień lub co miesiąc.

![Pomijanie reguły akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Grupa akcji

W przypadku wybrania w przełączniku **grupy akcji** należy dodać istniejącą grupę akcji lub utworzyć nową.

> [!NOTE]
> Można skojarzyć tylko jedną grupę akcji z regułą działania.

![Dodaj lub Utwórz nową regułę akcji, wybierając grupę akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Szczegóły reguły akcji

Na koniec skonfiguruj następujące szczegóły dla reguły akcji:
* Nazwa
* Grupa zasobów, w której została zapisana
* Opis

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Reguły akcji można tworzyć za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu poleceń [AZ monitor Action-Rule Create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) .  `az monitor action-rule`Odwołanie to tylko jeden z wielu [odwołań interfejsu wiersza polecenia platformy Azure dla Azure monitor](/cli/azure/azure-cli-reference-for-monitor).

### <a name="prepare-your-environment"></a>Przygotowywanie środowiska

1. [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

   Jeśli wolisz, możesz również użyć Azure Cloud Shell, aby wykonać kroki opisane w tym artykule.  Azure Cloud Shell to interaktywne środowisko powłoki, które jest używane w przeglądarce.  Rozpocznij Cloud Shell przy użyciu jednej z następujących metod:

   - Otwórz Cloud Shell, przechodząc do [https://shell.azure.com](https://shell.azure.com)

   - Wybierz przycisk **Cloud Shell** na pasku menu w prawym górnym rogu [Azure Portal](https://portal.azure.com)

1. Zaloguj się.

   Jeśli używasz lokalnej instalacji interfejsu wiersza polecenia, zaloguj się za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) .  Wykonaj kroki wyświetlane w terminalu, aby ukończyć proces uwierzytelniania.

    ```azurecli
    az login
    ```

1. Zainstaluj `alertsmanagement` rozszerzenie

   `az monitor action-rule`Polecenie to eksperymentalne rozszerzenie interfejsu wiersza polecenia platformy Azure. Dowiedz się więcej na temat odwołań do rozszerzeń w [rozszerzeniu use przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name alertsmanagement
   ```

   Oczekiwane jest następujące ostrzeżenie.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Tworzenie reguł akcji przy użyciu interfejsu wiersza polecenia platformy Azure

Zobacz zawartość referencyjną interfejsu wiersza polecenia platformy Azure dla elementu [AZ monitor Action-Rule Create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) , aby poznać informacje o wymaganych i opcjonalnych parametrach.

Utwórz regułę akcji, aby pominąć powiadomienia w grupie zasobów.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

Utwórz regułę akcji, aby pominąć powiadomienia dla wszystkich alertów Sev4 na wszystkich maszynach wirtualnych w ramach subskrypcji dla każdego weekendu.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>Przykładowe scenariusze

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenariusz 1: pomijanie alertów na podstawie ważności

Firma Contoso chce pominąć powiadomienia dla wszystkich alertów Sev4 na wszystkich maszynach wirtualnych w ramach subskrypcji **ContosoSub** każdego weekendu.

**Rozwiązanie:** Utwórz regułę akcji przy użyciu:
* Zakres = **ContosoSub**
* Filtry
    * Ważność = **Sev4**
    * Typ zasobu = **Virtual Machines**
* Pomijanie z ustawionym cyklem co tydzień, **Sobota** i **Niedziela** — zaznaczone

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenariusz 2. pomijanie alertów na podstawie kontekstu alertu (ładunku)

Firma Contoso chce pominąć powiadomienia dla wszystkich alertów dziennika wygenerowanych dla **komputera-01** w **ContosoSub** , gdy odbywa się on w trakcie konserwacji.

**Rozwiązanie:** Utwórz regułę akcji przy użyciu:
* Zakres = **ContosoSub**
* Filtry
    * Monitoruj usługę = **log Analytics**
    * Kontekst alertu (ładunek) zawiera **komputer-01**
* Pomijanie z ustawioną **teraz (zawsze)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenariusz 3: Grupa akcji zdefiniowana w grupie zasobów

Firma Contoso określiła [alert dotyczący metryki na poziomie subskrypcji](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Jednak chce zdefiniować akcje wyzwalane w odniesieniu do alertów wygenerowanych z grupy zasobów **ContosoRG**.

**Rozwiązanie:** Utwórz regułę akcji przy użyciu:
* Zakres = **ContosoRG**
* Brak filtrów
* Grupa akcji ustawiona na **ContosoActionGroup**

> [!NOTE]
> *Grupy akcji zdefiniowane w regułach akcji i regułach alertów działają niezależnie, bez deduplikacji.* W scenariuszu opisanym wcześniej, jeśli grupa akcji jest zdefiniowana dla reguły alertu, jest wyzwalana w połączeniu z grupą akcji zdefiniowaną w regule akcji.

## <a name="managing-your-action-rules"></a>Zarządzanie regułami akcji

### <a name="portal"></a>[Portal](#tab/portal)

Możesz wyświetlać reguły akcji i zarządzać nimi z poziomu widoku listy:

![Widok listy reguł akcji](media/alerts-action-rules/action-rules-list-view.png)

W tym miejscu możesz włączać, wyłączać lub usuwać reguły akcji na dużą skalę, zaznaczając obok nich pole wyboru. Po wybraniu reguły akcji zostanie otwarta strona Konfiguracja. Strona pomaga zaktualizować definicję reguły akcji i włączać lub wyłączać ją.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Reguły akcji można wyświetlać i zarządzać nimi za pomocą polecenia [AZ monitor Action-Rule](/cli/azure/ext/alertsmanagement/monitor) w interfejsie użytkownika platformy Azure.

Przed zarządzaniem regułami akcji przy użyciu interfejsu wiersza polecenia platformy Azure Przygotuj środowisko przy użyciu instrukcji podanych w temacie [Konfigurowanie reguły akcji](#configuring-an-action-rule).

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>Najlepsze rozwiązania

Alerty dzienników tworzone za pomocą opcji [liczba wyników](./alerts-unified-log.md) generują pojedyncze wystąpienie alertu za pomocą całego wyniku wyszukiwania (co może obejmować wiele komputerów). W tym scenariuszu, jeśli reguła akcji używa filtru **kontekstu alertu (ładunku)** , działa on w wystąpieniu alertu, o ile jest to zgodne. W scenariuszu 2 opisanym wcześniej, jeśli wyniki wyszukiwania dla wygenerowanego alertu dziennika zawierają zarówno **komputer-01** , jak i **komputer-02**, całe powiadomienie jest pomijane. Dla **komputera-02** nie Wygenerowano powiadomienia.

![Diagram przedstawia reguły akcji i alerty dzienników z wyróżnionym pojedynczym wystąpieniem alertu.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Aby najlepiej używać alertów dziennika z regułami akcji, Utwórz alerty dzienników przy użyciu opcji [pomiar metryki](./alerts-unified-log.md) . Dla tej opcji generowane są osobne wystąpienia alertów na podstawie pola zdefiniowanej grupy. Następnie w scenariuszu 2 generowane są osobne wystąpienia alertów dla **komputerów-01** i **Computer-02**. Ze względu na regułę akcji opisaną w tym scenariuszu tylko powiadomienie dla **komputera-01** jest pomijane. Powiadomienie dla **komputera-02** nadal jest normalne.

![Reguły akcji i alerty dziennika (liczba wyników)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Często zadawane pytania

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Podczas konfigurowania reguły akcji chcę zobaczyć wszystkie możliwe nakładające się reguły akcji, aby uniknąć zduplikowanych powiadomień. Czy jest to możliwe?

Po zdefiniowaniu zakresu podczas konfigurowania reguły akcji można wyświetlić listę reguł akcji, które nakładają się na ten sam zakres (jeśli istnieje). Ta zakładka może być jedną z następujących opcji:

* Dokładne dopasowanie: na przykład reguła akcji, która jest definiowana, i nakładające się reguły akcji znajdują się w tej samej subskrypcji.
* Podzestaw: na przykład zdefiniowana reguła akcji znajduje się w subskrypcji, a nakładający się reguła działania znajduje się w grupie zasobów w ramach subskrypcji.
* Nadzbiór: na przykład zdefiniowana reguła akcji znajduje się w grupie zasobów, a nakładający się reguła działania znajduje się w subskrypcji zawierającej grupę zasobów.
* Część wspólna: na przykład reguła akcji, którą definiujesz, znajduje się w **VM1** i **VM2**, a nakładana reguła działania jest włączona na **VM2** i **VM3**.

![Zrzut ekranu przedstawia nową stronę reguły akcji z nakładającymi się regułami akcji wyświetlanymi w regułach akcji zdefiniowanych w tym samym oknie zakresu.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Czy podczas konfigurowania reguły alertu można sprawdzić, czy istnieją już zdefiniowane reguły akcji, które mogą działać na regule alertów, które definiujemy?

Po zdefiniowaniu zasobu docelowego dla reguły alertu można zobaczyć listę reguł akcji, które działają w tym samym zakresie (jeśli istnieje), wybierając opcję **Wyświetl skonfigurowane akcje** w sekcji **Akcje** . Ta lista jest wypełniana na podstawie następujących scenariuszy dla zakresu:

* Dokładne dopasowanie: na przykład zdefiniowana reguła alertu i reguła działania znajdują się w tej samej subskrypcji.
* Podzestaw: na przykład zdefiniowana reguła alertu znajduje się w subskrypcji, a reguła akcji znajduje się w grupie zasobów w ramach subskrypcji.
* Nadzbiór: na przykład zdefiniowana reguła alertu znajduje się w grupie zasobów, a reguła akcji znajduje się w subskrypcji zawierającej grupę zasobów.
* Część wspólna: na przykład zdefiniowana reguła alertu znajduje się w **VM1** i **VM2**, a reguła akcji dotyczy **VM2** i **VM3**.

![Nakładające się reguły akcji](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Czy mogę zobaczyć alerty, które zostały pominięte przez regułę akcji?

Na [stronie Lista alertów](./alerts-managing-alert-instances.md)można wybrać dodatkową kolumnę o nazwie **stan pomijania**. Jeśli powiadomienie dla wystąpienia alertu zostało pominięte, ten stan będzie wyświetlany na liście.

![Pominięte wystąpienia alertów](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Jeśli istnieje reguła akcji z grupą akcji i inna z pominięciem aktywnym w tym samym zakresie, co się dzieje?

Pomijanie zawsze ma pierwszeństwo w tym samym zakresie.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Co się stanie, jeśli mam zasób, który jest monitorowany w dwóch oddzielnych regułach akcji? Czy otrzymuję jedną lub dwie powiadomienia? Na przykład **VM2** w następującym scenariuszu:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

Dla każdego alertu w systemach VM1 i VM3 grupa akcji AG1 zostanie wyzwolona jednokrotnie. Dla każdego alertu w witrynie **VM2** grupa akcji AG1 zostanie wyzwolona dwa razy, ponieważ reguły akcji nie spowodują deduplikowania akcji.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Co się stanie, jeśli mam zasób monitorowany w dwóch osobnych regułach akcji i jedno wywołanie dla akcji, a drugie dla pomijania? Na przykład **VM2** w następującym scenariuszu:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

Dla każdego alertu w witrynie VM1 grupa akcji AG1 zostanie wyzwolona jednokrotnie. Akcje i powiadomienia dla każdego alertu w VM2 i VM3 zostaną pominięte.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Co się stanie, jeśli mam regułę alertu i regułę akcji zdefiniowaną dla tego samego zasobu wywołującego różne grupy akcji? Na przykład **VM1** w następującym scenariuszu:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

Dla każdego alertu w witrynie VM1 grupa akcji AG1 zostanie wyzwolona jednokrotnie. Zawsze, gdy zostanie wyzwolona reguła alertu "RULE1", zostanie ona również wyzwolona AG2. Grupy akcji zdefiniowane w regułach akcji i regułach alertów działają niezależnie, bez deduplikacji.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o alertach na platformie Azure](./alerts-overview.md)