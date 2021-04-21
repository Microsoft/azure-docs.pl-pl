---
title: Reguły akcji dla Azure Monitor alertów
description: Poznanie reguł akcji w Azure Monitor oraz sposobu ich konfigurowania i zarządzania nimi.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4f54ee7d21d52386bd18921aec33cabe02046852
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772563"
---
# <a name="action-rules-preview"></a>Reguły akcji (wersja zapoznawcza)

Reguły akcji umożliwiają dodawanie lub pomijanie grup akcji w wyzwłaszanych alertach. Pojedyncza reguła może obejmować różne zakresy zasobów docelowych, na przykład wszystkie alerty dotyczące określonego zasobu (na przykład określonej maszyny wirtualnej) lub alerty wyzłoszone dla dowolnego zasobu w subskrypcji. Opcjonalnie możesz dodać różne filtry, aby kontrolować, które alerty są objęte regułą, i zdefiniować dla nich harmonogram, na przykład aby obowiązywał tylko poza godzinami pracy lub w trakcie planowanego okna obsługi.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Dlaczego i kiedy należy używać reguł akcji?

### <a name="suppression-of-alerts"></a>Pomijanie alertów

Istnieje wiele scenariuszy, w których warto pominąć powiadomienia generowane przez alerty. Te scenariusze obejmują od pomijania podczas planowanego okna obsługi do pomijania poza godzinami pracy. Na przykład zespół odpowiedzialny za maszyny **ContosoVM** chce pominąć powiadomienia o alertach dotyczące nadchodzącego weekendu, ponieważ trwa planowana konserwacja maszyny **ContosoVM.**

Mimo że zespół może wyłączyć każdą regułę alertu skonfigurowaną ręcznie na maszyny **ContosoVM** (i włączyć ją ponownie po konserwacji), nie jest to prosty proces. Reguły akcji ułatwiają definiowanie pomijania alertów na dużą skalę z możliwością elastycznego konfigurowania okresu pomijania. W poprzednim przykładzie zespół może zdefiniować jedną regułę akcji na **contosovm,** która pomija wszystkie powiadomienia o alertach dla weekendu.

### <a name="actions-at-scale"></a>Akcje na dużą skalę

Mimo że reguły alertów ułatwiają definiowanie grupy akcji wyzwalanej po wygenerowaniu alertu, klienci często mają wspólną grupę akcji w zakresie operacji. Na przykład zespół odpowiedzialny za grupę zasobów **ContosoRG** prawdopodobnie zdefiniuje tę samą grupę akcji dla wszystkich reguł alertów zdefiniowanych w grupie **ContosoRG**.

Reguły akcji ułatwiają ten proces. Definiując akcje na dużą skalę, można wyzwolić grupę akcji dla dowolnego alertu wygenerowanego w skonfigurowanym zakresie. W poprzednim przykładzie zespół może zdefiniować jedną regułę akcji dla grupy **ContosoRG,** która wyzwoli tę samą grupę akcji dla wszystkich wygenerowanych w niej alertów.

> [!NOTE]
> Reguły akcji nie mają zastosowania do Azure Service Health alertów.

## <a name="configuring-an-action-rule"></a>Konfigurowanie reguły akcji

### <a name="portal"></a>[Portal](#tab/portal)

Dostęp do tej funkcji można uzyskać, wybierając pozycję **Zarządzaj akcjami** na stronie docelowej **Alerty** w Azure Monitor. Następnie wybierz pozycję **Reguły akcji (wersja zapoznawcza).** Dostęp do reguł można uzyskać, wybierając pozycję **Reguły akcji (wersja zapoznawcza)** na pulpicie nawigacyjnym strony docelowej dla alertów.

![Reguły akcji ze strony Azure Monitor docelowej](media/alerts-action-rules/action-rules-landing-page.png)

Wybierz **pozycję + Nowa reguła akcji.**

![Zrzut ekranu przedstawia stronę Zarządzanie akcjami z wyróżnionym przyciskiem Nowa reguła akcji.](media/alerts-action-rules/action-rules-new-rule.png)

Alternatywnie można utworzyć regułę akcji podczas konfigurowania reguły alertu.

![Zrzut ekranu przedstawiający stronę Tworzenie reguły z wyróżnionym przyciskiem Utwórz regułę akcji.](media/alerts-action-rules/action-rules-alert-rule.png)

Powinna zostać teraz wyświetlony strona przepływu do tworzenia reguł akcji. Skonfiguruj następujące elementy:

![Nowy przepływ tworzenia reguły akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Zakres

Najpierw wybierz zakres (subskrypcja platformy Azure, grupa zasobów lub zasób docelowy). Możesz również wybrać wiele zakresów w ramach jednej subskrypcji.

![Zakres reguły akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria&quot;></a>Kryteria filtrowania

Opcjonalnie możesz zdefiniować filtry, aby reguła miała zastosowanie do określonego podzestawu alertów lub do określonych zdarzeń dla każdego alertu (na przykład tylko &quot;Wyzpalone&quot; lub tylko &quot;Rozwiązane").

Dostępne są następujące filtry:

* **Ważność**  
Ta reguła będzie dotyczyć tylko alertów z wybranymi ważnościami.  
Na przykład **ważność = "Ważność 1"** oznacza, że reguła będzie dotyczyć tylko alertów o ważności 1.
* **Monitorowanie usługi**  
Ta reguła będzie dotyczyć tylko alertów pochodzących z wybranych usług monitorowania.  
Na przykład **monitor service = "Azure Backup"** oznacza, że reguła będzie dotyczyć tylko alertów kopii zapasowej (pochodzących z Azure Backup).
* **Typ zasobu**  
Ta reguła będzie dotyczyć tylko alertów dotyczących wybranych typów zasobów.  
Na przykład **typ zasobu = "Virtual Machines"** oznacza, że reguła będzie dotyczyć tylko alertów na maszynach wirtualnych.
* **Identyfikator reguły alertu**  
Ta reguła będzie dotyczyć tylko alertów pochodzących z określonej reguły alertu. Wartość powinna być identyfikatorem Resource Manager reguły alertu.  
Na przykład identyfikator reguły **alertu = "/subscriptions/SubId1/resourceGroups/RG1/providers/microsoft.insights/metricalerts/API-Latency"** oznacza, że ta reguła będzie dotyczyć tylko alertów pochodzących z reguły alertu metryki "Opóźnienie interfejsu API".  
_UWAGA — możesz uzyskać prawidłowy identyfikator reguły alertu, wymieniając reguły alertów z interfejsu wiersza polecenia lub otwierając określoną regułę alertu w portalu, klikając pozycję "Właściwości" i kopiując wartość "Identyfikator zasobu"._
* **Warunek monitorowania**  
Ta reguła będzie dotyczyć tylko zdarzeń alertów z określonym warunkiem monitora — **"Wyzgnione"** lub **"Rozwiązane".**
* **Opis**  
Ta reguła będzie dotyczyć tylko alertów, które zawierają określony ciąg w polu opisu alertu. To pole zawiera opis reguły alertu.  
Na przykład **opis zawiera "prod"** oznacza, że reguła będzie odpowiadać tylko alertom, które zawierają ciąg "prod" w opisie.
* **Kontekst alertu (ładunek)**  
Ta reguła będzie dotyczyć tylko alertów, które zawierają dowolną z co najmniej jednej konkretnej wartości w polach kontekstu alertu.  
Na przykład **kontekst alertu (ładunek) zawiera wartość "Computer-01"** oznacza, że reguła będzie dotyczyć tylko alertów, których ładunek zawiera ciąg "Computer-01".

> [!NOTE]
> Każdy filtr może zawierać maksymalnie pięć wartości.  
> Na przykład filtr usługi monitorowania może zawierać maksymalnie pięć monitorowanych nazw usług.




Jeśli ustawisz wiele filtrów w regułę, wszystkie z nich będą stosowane. Jeśli na przykład ustawisz typ zasobu **= "Virtual Machines" i** ważność **= "Ważność 0",** reguła będzie dotyczyć tylko alertów dotyczących ważności 0 na maszynach wirtualnych.

![Filtry reguł akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Pomijanie lub konfiguracja grupy akcji

Następnie skonfiguruj regułę akcji dla pomijania alertów lub obsługi grup akcji. Nie można wybrać obu tych opcji. Konfiguracja działa na wszystkich wystąpieniach alertów, które pasują do wcześniej zdefiniowanego zakresu i filtrów.

#### <a name="suppression"></a>Pomijanie

W przypadku wybrania **pomijania** skonfiguruj czas trwania pomijania akcji i powiadomień. Wybierz jedną z następujących opcji:
* **Od teraz (zawsze)**: pomija wszystkie powiadomienia przez czas nieokreślony.
* **W zaplanowanym czasie:** pomija powiadomienia w ramach ograniczonego czasu trwania.
* **W przypadku cyklu:** pomija powiadomienia cyklicznego harmonogramu dziennego, tygodniowego lub miesięcznego.

![Pomijanie reguł akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Grupa akcji

Jeśli w **przełączniku wybierzesz** grupę akcji, dodaj istniejącą grupę akcji lub utwórz nową.

> [!NOTE]
> Z regułą akcji można skojarzyć tylko jedną grupę akcji.

![Dodawanie lub tworzenie nowej reguły akcji przez wybranie grupy akcji](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Szczegóły reguły akcji

Na koniec skonfiguruj następujące szczegóły reguły akcji:
* Nazwa
* Grupa zasobów, w której została zapisana
* Opis

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Reguły akcji można tworzyć za pomocą interfejsu wiersza polecenia platformy Azure za pomocą [polecenia az monitor action-rule create.](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create)  Odwołanie `az monitor action-rule` to tylko jedna z wielu odwołań do interfejsu wiersza polecenia platformy Azure dla [Azure Monitor](/cli/azure/azure-cli-reference-for-monitor).

### <a name="prepare-your-environment"></a>Przygotowywanie środowiska

1. [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

   Jeśli wolisz, możesz również użyć Azure Cloud Shell, aby wykonać kroki opisane w tym artykule.  Azure Cloud Shell to interaktywne środowisko powłoki, z których korzystasz w przeglądarce.  Uruchom Cloud Shell przy użyciu jednej z tych metod:

   - Otwórz Cloud Shell, przechodząc do [https://shell.azure.com](https://shell.azure.com)

   - Wybierz przycisk **Cloud Shell** na pasku menu w prawym górnym rogu okna [Azure Portal](https://portal.azure.com)

1. Zaloguj się.

   Jeśli używasz lokalnej instalacji interfejsu wiersza polecenia, zaloguj się przy użyciu [polecenia az login.](/cli/azure/reference-index#az_login)  Wykonaj kroki wyświetlane w terminalu, aby ukończyć proces uwierzytelniania.

    ```azurecli
    az login
    ```

1. Instalowanie `alertsmanagement` rozszerzenia

   To `az monitor action-rule` polecenie jest eksperymentalnym rozszerzeniem podstawowego interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej na temat odwołań do rozszerzeń, [zobacz Używanie rozszerzenia z interfejsem wiersza polecenia platformy Azure.](/cli/azure/azure-cli-extensions-overview?)

   ```azurecli
   az extension add --name alertsmanagement
   ```

   Oczekiwane jest następujące ostrzeżenie.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Tworzenie reguł akcji przy użyciu interfejsu wiersza polecenia platformy Azure

Zobacz zawartość referencyjną interfejsu wiersza polecenia platformy Azure dla polecenia [az monitor action-rule create,](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) aby dowiedzieć się więcej o wymaganych i opcjonalnych parametrach.

Utwórz regułę akcji w celu pomijania powiadomień w grupie zasobów.

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

Utwórz regułę akcji, aby w każdy weekend pomijać powiadomienia dla wszystkich alertów dotyczących poziomu 4 na wszystkich maszyn wirtualnych w ramach subskrypcji.

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

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenariusz 1. Pomijanie alertów na podstawie ważności

Firma Contoso chce pominąć powiadomienia dla wszystkich alertów dotyczących poziomu 4 na wszystkich maszyn wirtualnych w ramach subskrypcji **ContosoSub** co weekend.

**Rozwiązanie:** Utwórz regułę akcji za pomocą:
* Zakres = **ContosoSub**
* Filtry
    * Ważność = **ważność 4**
    * Typ zasobu = **Virtual Machines**
* Pomijanie z cyklem ustawionym na co tydzień i **zaznaczoną sobotą** **i niedzielą**

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenariusz 2. Pomijanie alertów na podstawie kontekstu alertu (ładunku)

Firma Contoso chce przez czas nieokreślony pomijać powiadomienia dla wszystkich alertów dzienników generowanych dla komputera **Computer-01** w **contosoSub** w ramach konserwacji.

**Rozwiązanie:** Utwórz regułę akcji za pomocą:
* Zakres = **ContosoSub**
* Filtry
    * Monitor Service = **Log Analytics**
    * Kontekst alertu (ładunek) zawiera **komputer Computer-01**
* Pomijanie ustawione **na wartość Od teraz (zawsze)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenariusz 3. Grupa akcji zdefiniowana w grupie zasobów

Firma Contoso [zdefiniował alert metryki na poziomie subskrypcji](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Chce jednak zdefiniować akcje wyzwalane specjalnie dla alertów wygenerowanych z grupy zasobów **ContosoRG.**

**Rozwiązanie:** Utwórz regułę akcji za pomocą:
* Zakres = **ContosoRG**
* Brak filtrów
* Grupa akcji ustawiona na **Wartość ContosoActionGroup**

> [!NOTE]
> *Grupy akcji zdefiniowane w ramach reguł akcji i reguł alertów działają niezależnie, bez deduplikacji.* W scenariuszu opisanym wcześniej, jeśli grupa akcji jest zdefiniowana dla reguły alertu, jest wyzwalana w połączeniu z grupą akcji zdefiniowaną w regułę akcji.

## <a name="managing-your-action-rules"></a>Zarządzanie regułami akcji

### <a name="portal"></a>[Portal](#tab/portal)

Reguły akcji można wyświetlać i zarządzać nimi z widoku listy:

![Widok listy reguł akcji](media/alerts-action-rules/action-rules-list-view.png)

W tym miejscu można włączać, wyłączać lub usuwać reguły akcji na dużą skalę, zaznaczając pole wyboru obok nich. Po wybraniu reguły akcji zostanie otwarta jej strona konfiguracji. Strona pomaga zaktualizować definicję reguły akcji i włączyć ją lub wyłączyć.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Reguły akcji można wyświetlać i zarządzać nimi za pomocą [polecenia az monitor action-rule](/cli/azure/ext/alertsmanagement/monitor) z interfejsu wiersza polecenia platformy Azure.

Przed rozpoczęciem zarządzania regułami akcji za pomocą interfejsu wiersza polecenia platformy Azure przygotuj środowisko, korzystając z instrukcji podanych w tece [Konfigurowanie reguły akcji.](#configuring-an-action-rule)

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

Alerty dziennika, które [](./alerts-unified-log.md) tworzysz za pomocą opcji liczby wyników, generują pojedyncze wystąpienie alertu przy użyciu całego wyniku wyszukiwania (który może obejmować wiele komputerów). Jeśli w tym scenariuszu reguła akcji używa **filtru kontekstu alertu (ładunku),** działa w wystąpieniu alertu, o ile istnieje dopasowanie. W scenariuszu 2 opisanym wcześniej, jeśli wyniki wyszukiwania dla wygenerowanego alertu dziennika zawierają zarówno **komputer-01,** jak i **komputer-02,** całe powiadomienie zostanie pominięte. Dla komputera **Computer-02** w ogóle nie jest generowane żadne powiadomienie.

![Diagram przedstawia reguły akcji i alerty dzienników z wyróżnione pojedynczym wystąpieniem alertu.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Aby najlepiej używać alertów dzienników z regułami akcji, utwórz alerty dzienników z [opcją pomiaru](./alerts-unified-log.md) metryki. Ta opcja generuje oddzielne wystąpienia alertów na podstawie zdefiniowanego pola grupy. Następnie w scenariuszu 2 są generowane oddzielne wystąpienia alertów dla komputerów **Computer-01** **i Computer-02.** Ze względu na regułę akcji opisaną w tym scenariuszu tylko powiadomienie dla komputera **Computer-01** nie jest pomijane. Powiadomienie dla **komputera Computer-02** będzie nadal wyzłoszałe się w zwykły sposób.

![Reguły akcji i alerty dzienników (liczba wyników)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Często zadawane pytania

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Podczas konfigurowania reguły akcji chcę zobaczyć wszystkie możliwe nakładające się reguły akcji, aby uniknąć duplikowania powiadomień. Czy jest to możliwe?

Po zdefiniowaniu zakresu podczas konfigurowania reguły akcji można wyświetlić listę reguł akcji, które nakładają się na ten sam zakres (jeśli są). Takie nakładanie się może być jedną z następujących opcji:

* Dokładne dopasowanie: na przykład definiowana reguła akcji i nakładające się reguły akcji znajdują się w tej samej subskrypcji.
* Podzestaw: na przykład definiowana reguła akcji dotyczy subskrypcji, a nakładające się reguły akcji są w grupie zasobów w ramach subskrypcji.
* Nadzeskład: na przykład definiowana reguła akcji dotyczy grupy zasobów, a nakładające się reguły akcji znajdują się w subskrypcji zawierającej grupę zasobów.
* Część wspólną: na przykład definiowana reguła akcji dotyczy maszyn **VM1** i **VM2,** a nakładające się reguły akcji są na maszynach **VM2** **i VM3.**

![Zrzut ekranu przedstawia stronę Nowa reguła akcji z nakładającymi się regułami akcji wyświetlanymi w oknie Reguły akcji zdefiniowane w tym samym oknie zakresu.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Czy podczas konfigurowania reguły alertu jest możliwe określenie, czy istnieją już zdefiniowane reguły akcji, które mogą działać na zdefiniowanej przeze mnie regułę alertu?

Po zdefiniowaniu zasobu docelowego dla reguły alertu można wyświetlić listę reguł akcji, które działają  w tym samym zakresie (jeśli są) po wybraniu opcji Wyświetl skonfigurowane akcje w sekcji **Akcje.** Ta lista jest wypełniana na podstawie następujących scenariuszy dla zakresu:

* Dokładne dopasowanie: na przykład definiowana reguła alertu i reguła akcji znajdują się w tej samej subskrypcji.
* Podzestaw: na przykład definiowana reguła alertu dotyczy subskrypcji, a reguła akcji dotyczy grupy zasobów w ramach subskrypcji.
* Narzut: na przykład definiowana reguła alertu dotyczy grupy zasobów, a reguła akcji dotyczy subskrypcji zawierającej grupę zasobów.
* Część wspólną: na przykład definiowana reguła alertu dotyczy maszyn **VM1** i **VM2,** a reguła akcji dotyczy maszyn **VM2** **i VM3.**

![Nakładające się reguły akcji](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Czy mogę zobaczyć alerty, które zostały pominięte przez regułę akcji?

Na stronie [listy alertów](./alerts-managing-alert-instances.md)można wybrać dodatkową kolumnę o nazwie **Stan pomijania**. Jeśli powiadomienie dla wystąpienia alertu zostało pominięte, ten stan będzie pokazywany na liście.

![Pominięte wystąpienia alertów](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Jeśli istnieje reguła akcji z grupą akcji i inna z aktywnym pomijaniem w tym samym zakresie, co się stanie?

Pomijanie zawsze ma pierwszeństwo w tym samym zakresie.

### <a name="what-happens-if-i-have-a-resource-that-is-covered-by-two-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Co się stanie, jeśli mam zasób, który jest objęty dwiema regułami akcji? Czy otrzymuję jedno lub dwa powiadomienia? Na przykład maszyna **wirtualna VM2** w następującym scenariuszu:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

Dla każdego alertu na maszynach wirtualnych VM1 i VM3 grupa akcji AG1 zostanie wyzwolona raz. Dla każdego alertu **na maszynie wirtualnej VM2** grupa akcji AG1 zostanie wyzwolona dwukrotnie, ponieważ reguły akcji nie deduplikują akcji.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Co się stanie, jeśli zasób jest monitorowany w dwóch oddzielnych zasadach akcji, a jedna wywołuje akcję, a druga w celu pomijania? Na przykład maszyna **wirtualna VM2** w następującym scenariuszu:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

Dla każdego alertu na maszynie VM1 grupa akcji AG1 zostanie wyzwolona raz. Akcje i powiadomienia dla każdego alertu na maszynach wirtualnych VM2 i VM3 będą pomijane.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Co się stanie, jeśli mam regułę alertu i regułę akcji zdefiniowaną dla tego samego zasobu wywołującego różne grupy akcji? Na przykład maszyna **wirtualna VM1** w następującym scenariuszu:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

Dla każdego alertu na maszynie VM1 grupa akcji AG1 zostanie wyzwolona raz. Za każdym razem, gdy zostanie wyzwolona reguła alertu "rule1", zostanie również wyzwolona reguła AG2. Grupy akcji zdefiniowane w ramach reguł akcji i reguł alertów działają niezależnie, bez deduplikacji.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o alertach na platformie Azure](./alerts-overview.md)
