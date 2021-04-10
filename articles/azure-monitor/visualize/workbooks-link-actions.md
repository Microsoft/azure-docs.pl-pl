---
title: Akcje łącza Azure Monitor skoroszytów
description: Jak używać akcji łączy w skoroszytach Azure Monitor
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100616040"
---
# <a name="link-actions"></a>Akcje linków

Akcje linku są dostępne przez kroki linku do skoroszytu lub ustawienia kolumn [siatek](../visualize/workbooks-grid-visualizations.md), [tytułów](../visualize/workbooks-tile-visualizations.md)lub [wykresów](../visualize/workbooks-graph-visualizations.md).

## <a name="general-link-actions"></a>Ogólne akcje linków

| Akcja łącza | Akcja po kliknięciu |
|:------------- |:-------------|
| `Generic Details` | Pokazuje wartości wierszy w widoku kontekstu siatki właściwości. |
| `Cell Details` | Wyświetla wartość komórki w widoku kontekstu siatki właściwości. Przydatne, gdy komórka zawiera typ dynamiczny z informacjami (na przykład JSON z właściwościami żądania, takimi jak lokalizacja, wystąpienie roli itp.). |
| `Url` | Oczekiwana wartość komórki jest prawidłowym adresem URL http, a komórka będzie linkiem otwierającym ten adres URL na nowej karcie.|

## <a name="application-insights"></a>Application Insights

| Akcja łącza | Akcja po kliknięciu |
|:------------- |:-------------|
| `Custom Event Details` | Otwiera Application Insights szczegóły wyszukiwania z niestandardowym IDENTYFIKATORem zdarzenia ( `itemId` ) w komórce. |
| `* Details` | Podobnie jak w przypadku szczegółów zdarzenia niestandardowego, z wyjątkiem zależności, wyjątków, wyświetlania stron, żądań i śladów. |
| `Custom Event User Flows` | Otwiera środowisko Przepływy użytkownika Application Insights przestawiane na nazwę zdarzenia niestandardowego w komórce. |
| `* User Flows` | Podobnie jak w przypadku Przepływy użytkownika zdarzeń niestandardowych, z wyjątkiem wyjątków, wyświetleń stron i żądań. |
| `User Timeline` | Otwiera oś czasu użytkownika z IDENTYFIKATORem użytkownika ( `user_Id` ) w komórce. |
| `Session Timeline` | Otwiera środowisko wyszukiwania Application Insights dla wartości w komórce (na przykład Wyszukaj tekst "ABC", gdzie ABC jest wartością w komórce). |

`*` oznacza symbol wieloznaczny dla powyższej tabeli

## <a name="azure-resource"></a>Zasób platformy Azure

| Akcja łącza | Akcja po kliknięciu |
|:------------- |:-------------|
| `ARM Deployment` | Wdróż szablon Azure Resource Manager.  Po wybraniu tego elementu są wyświetlane dodatkowe pola, aby umożliwić autorowi skonfigurowanie szablonu, który Azure Resource Manager otworzyć, parametry szablonu itp., [zobacz Azure Resource Manager ustawienia linku wdrożenia](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | Tworzy regułę alertu dla zasobu.  |
| `Custom View` | Otwiera widok niestandardowy. Po wybraniu tego elementu są wyświetlane dodatkowe pola, aby umożliwić autorowi skonfigurowanie rozszerzenia widoku, nazwy widoku i parametrów używanych do otwierania widoku. [Zobacz widok niestandardowy](#custom-view-link-settings). |
| `Metrics` | Otwiera widok metryk.  |
| `Resource overview` | Otwórz widok zasobu w portalu na podstawie wartości identyfikatora zasobu w komórce. Autor może również opcjonalnie ustawić `submenu` wartość, która spowoduje otwarcie określonego elementu menu w widoku zasobów. |
| `Workbook (template)` | Otwórz szablon skoroszytu.  Po wybraniu tego elementu są wyświetlane dodatkowe pola, aby umożliwić autorowi skonfigurowanie szablonu do otwarcia itd.  |

## <a name="link-settings"></a>Ustawienia linku

W przypadku korzystania z modułu renderowania linków dostępne są następujące ustawienia:

![Zrzut ekranu ustawień łącza](./media/workbooks-link-actions/link-settings.png)

| Ustawienie | Wyjaśnienie |
|:------------- |:-------------|
| `View to open` | Umożliwia autorowi wybranie jednej z akcji wymienionych powyżej. |
| `Menu item` | Jeśli wybrano opcję "przegląd zasobów", jest to element menu w przeglądzie zasobu, który ma zostać otwarty. Można go użyć do otwarcia alertów lub dzienników aktywności zamiast "przegląd" dla zasobu. Wartości elementów menu różnią się w zależności od platformy Azure `Resourcetype` .|
| `Link label` | Jeśli ta wartość jest określona, zostanie wyświetlona w kolumnie siatka. Jeśli ta wartość nie jest określona, zostanie wyświetlona wartość komórki. Jeśli chcesz, aby wyświetlana była inna wartość, na przykład mapę cieplną lub ikona, nie używaj `Link` modułu renderowania, zamiast tego użyj odpowiedniego modułu renderowania i wybierz `Make this item a link` opcję. |
| `Open link in Context Blade` | Jeśli ta wartość jest określona, link zostanie otwarty jako podręczny widok "kontekstowy" po prawej stronie okna zamiast otwierać jako Widok pełny. |

W przypadku korzystania z tej `Make this item a link` opcji dostępne są następujące ustawienia:

| Ustawienie | Wyjaśnienie |
|:------------- |:-------------|
| `Link value comes from` | W przypadku wyświetlania komórki jako modułu renderowania za pomocą linku to pole określa miejsce, w którym znajduje się wartość "link", która ma zostać użyta w łączu, co umożliwia autorowi wybranie z listy rozwijanej innych kolumn w siatce. Na przykład komórka może być wartością mapę cieplną, ale chcesz, aby w ramach tego linku otworzyć przegląd zasobów dla identyfikatora zasobu w wierszu. W takim przypadku należy ustawić wartość link, która ma pochodzić z `Resource Id` pola.
| `View to open` | tak samo jak powyżej. |
| `Menu item` | tak samo jak powyżej. |
| `Open link in Context Blade` | tak samo jak powyżej. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Ustawienia linku wdrażania Azure Resource Manager

Jeśli wybrany typ łącza jest `ARM Deployment` autorem, należy określić dodatkowe ustawienia, aby otworzyć wdrożenie Azure Resource Manager. Istnieją dwie główne karty dla konfiguracji.

### <a name="template-settings"></a>Ustawienia szablonu

W tej sekcji zdefiniowano, z której powinien pochodzić szablon, oraz parametry używane do uruchamiania wdrożenia Azure Resource Manager.

| Element źródłowy | Wyjaśnienie |
|:------------- |:-------------|
|`Resource group id comes from` | Identyfikator zasobu służy do zarządzania wdrożonymi zasobami. Subskrypcja służy do zarządzania wdrożonymi zasobami i kosztami. Grupy zasobów są używane do organizowania wszystkich zasobów i zarządzania nimi. Jeśli ta wartość nie zostanie określona, wdrożenie zakończy się niepowodzeniem. Wybierz z `Cell` , `Column` , `Static Value` lub `Parameter` w [linkach źródła](#link-sources).|
|`ARM template URI from` | Identyfikator URI samego szablonu Azure Resource Manager. Identyfikator URI szablonu musi być dostępny dla użytkowników, którzy będą wdrażać szablon. Wybierz z `Cell` , `Column` , `Parameter` lub `Static Value`  w [linkach źródła](#link-sources). W przypadku startowych zapoznaj się z naszymi [szablonami szybkiego startu](https://azure.microsoft.com/resources/templates/).|
|`ARM Template Parameters` | Ta sekcja definiuje parametry szablonu, które są używane dla identyfikatora URI szablonu zdefiniowanego powyżej. Te parametry zostaną użyte do wdrożenia szablonu na stronie uruchomienia. Siatka zawiera przycisk Rozwiń pasek narzędzi, aby pomóc wypełnić parametry przy użyciu nazw zdefiniowanych w identyfikatorze URI szablonu i ustawić statyczne puste wartości. Tej opcji można użyć tylko wtedy, gdy w siatce nie ma parametrów, a identyfikator URI szablonu został ustawiony. Dolna sekcja to podgląd danych wyjściowych parametrów. Wybierz pozycję Odśwież, aby zaktualizować Podgląd przy użyciu bieżących zmian. Parametry są zazwyczaj wartościami, natomiast odwołania to elementy, które mogą wskazywać na wpisy tajne magazynu kluczy, do których użytkownik ma dostęp. <br/><br/> **Ograniczenie bloku podglądu szablonów** — nie Renderuj prawidłowo parametrów odwołań i będzie wyświetlana jako wartość null, dzięki czemu użytkownicy nie będą mogli poprawnie wdrożyć parametrów referencyjnych na karcie Podgląd szablonów.|

![Zrzut ekranu przedstawiający ustawienia szablonu Azure Resource Manager](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>Ustawienia środowiska użytkownika

Ta sekcja służy do konfigurowania elementów, które będą widoczne dla użytkowników przed uruchomieniem Azure Resource Manager wdrożenia.

| Element źródłowy | Wyjaśnienie |
|:------------- |:-------------|
|`Title from` | Tytuł używany w widoku uruchamiania. Wybierz z `Cell` , `Column` , `Parameter` lub `Static Value` w [linkach źródła](#link-sources).|
|`Description from` | Jest to tekst z promocji, używany do udostępnienia przydatnego opisu użytkownikom, gdy chcą wdrożyć szablon. Wybierz z `Cell` , `Column` , `Parameter` lub `Static Value`  w [linkach źródła](#link-sources). <br/><br/> **Uwaga:** Jeśli `Static Value` jest zaznaczone, pojawi się wielowierszowe pole tekstowe. W tym polu tekstowym można rozwiązać parametry za pomocą polecenia `{paramName}` . Ponadto można traktować kolumny jako parametry przez dołączenie `_column` po nazwie kolumny, takiej jak `{columnName_column}` . Na przykładowym obrazie można odwoływać się do kolumny, `VMName` pisząc `{VMName_column}` . Wartość po dwukropku to program [formatujący parametrów](../visualize/workbooks-parameters.md#parameter-options), w tym przypadku jest to `value` .|
|`Run button text from` | Etykieta używana na przycisku Uruchom (wykonaj), aby wdrożyć szablon Azure Resource Manager. Oto, co użytkownicy będą wybierać, aby rozpocząć wdrażanie szablonu Azure Resource Manager.|

![Zrzut ekranu przedstawiający ustawienia środowiska Azure Resource Manager UX](./media/workbooks-link-actions/ux-settings.png)

Po ustawieniu tych konfiguracji, gdy użytkownicy wybierzą łącze, otworzy widok przy użyciu środowiska użytkownika opisanego w ustawieniach środowiska. Jeśli użytkownik wybierze `Run button text from` szablon Azure Resource Manager przy użyciu wartości z [ustawień szablonu](#template-settings). Widok szablonu spowoduje otwarcie karty Podgląd szablonów dla użytkownika, aby sprawdzić szablon i parametry przed wdrożeniem.

![Zrzut ekranu przedstawiający widok Azure Resource Manager uruchamiania](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Ustawienia linku widoku niestandardowego

Służy do otwierania widoków niestandardowych w Azure Portal. Sprawdź wszystkie konfiguracje i ustawienia. Nieprawidłowe wartości spowodują błędy w portalu lub niepowodzenie otwarcia widoków. Istnieją dwa sposoby konfigurowania ustawień za pośrednictwem `Form` lub `URL` .

> [!NOTE]
> Widoków z menu nie można otwierać na karcie kontekstowej. Jeśli widok z menu jest skonfigurowany do otwierania na karcie kontekstowej, po wybraniu łącza nie będzie wyświetlana żadna karta kontekstowa.

### <a name="form"></a>Formularz

| Element źródłowy | Wyjaśnienie |
|:------------- |:-------------|
|`Extension name` | Nazwa rozszerzenia, które hostuje nazwę widoku.|
|`View name` | Nazwa widoku do otwarcia.|

#### <a name="view-inputs"></a>Wyświetl dane wejściowe

Istnieją dwa typy danych wejściowych, siatek i notacji JSON. Użyj `Grid` dla danych wejściowych karty prostej i wartości lub wybierz, `JSON` Aby określić zagnieżdżone dane wejściowe JSON.

- Siatka
    - `Parameter Name`: Nazwa parametru wejściowego widoku.
    - `Parameter Comes From`: Gdzie wartość parametru widoku powinna pochodzić z. Wybierz z `Cell` , `Column` , `Parameter` lub `Static Value` w [linkach źródła](#link-sources).
    > [!NOTE]
    > Jeśli `Static Value` jest zaznaczone, parametry można rozpoznać przy użyciu linku nawiasy `{paramName}` w polu tekstowym. Kolumny mogą być traktowane jako kolumny parametrów przez dołączenie `_column` po nazwie kolumny, takiej jak `{columnName_column}` .

    - `Parameter Value`: w zależności od `Parameter Comes From` tego będzie to lista rozwijana dostępnych parametrów, kolumn lub wartości statycznej.

    ![Zrzut ekranu ustawienia Edytuj kolumnę Pokaż ustawienia widoku niestandardowego z formularza.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Określ dane wejściowe karty w formacie JSON w edytorze. Podobnie jak w przypadku `Grid` trybu, parametrów i kolumn można odwoływać się za pomocą `{paramName}` parametrów i `{columnName_column}` dla kolumn. Wybranie pozycji `Show JSON Sample` spowoduje wyświetlenie oczekiwanych danych wyjściowych wszystkich rozwiązanych parametrów i kolumn użytkownika w celu wyświetlenia danych wejściowych.

    ![Zrzut ekranu przedstawiający otwarte ustawienia widoku niestandardowego z wyświetlaniem danych wejściowych w formacie JSON.](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>Adres URL

Wklej adres URL portalu zawierający rozszerzenie, nazwę widoku i wszelkie dane wejściowe potrzebne do otwarcia widoku. Po wybraniu tej opcji zostanie wyświetlona `Initialize Settings` wartość `Form` dla autora, aby dodać/zmodyfikować/usunąć wszystkie dane wejściowe widoku.

![Zrzut ekranu przedstawiający ustawienie kolumny Pokaż ustawienia widoku niestandardowego na podstawie adresu URL. ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Ustawienia linku skoroszytu (szablonu)

Jeśli wybrany typ łącza to `Workbook (Template)` , autor musi określić dodatkowe ustawienia, aby otworzyć właściwy szablon skoroszytu. Poniższe ustawienia zawierają opcje dotyczące sposobu, w jaki siatka znajdzie odpowiednią wartość dla każdego z tych ustawień.

| Ustawienie | Wyjaśnienie |
|:------------- |:-------------|
| `Workbook owner Resource Id` | Jest to identyfikator zasobu zasobu platformy Azure, którego właścicielem jest skoroszyt. Często jest to zasób Application Insights lub Log Analytics obszaru roboczego. Wewnątrz Azure Monitor może to być również ciąg literału `"Azure Monitor"` . Po zapisaniu skoroszytu jest to element, z którym zostanie połączony skoroszyt. |
| `Workbook resources` | Tablica identyfikatorów zasobów platformy Azure, która określa domyślny zasób używany w skoroszycie. Na przykład jeśli otwarty szablon zawiera metryki maszyny wirtualnej, wartości te byłyby identyfikatory zasobów maszyny wirtualnej.  Wiele razy, właściciel i zasoby są ustawione na te same ustawienia. |
| `Template Id` | Określ identyfikator szablonu do otwarcia. Jeśli jest to szablon społeczności z galerii (najbardziej typowe), poprzedź ścieżkę do szablonu `Community-` , na przykład `Community-Workbooks/Performance/Apdex` dla `Workbooks/Performance/Apdex` szablonu. Jeśli jest to link do zapisanego skoroszytu/szablonu, jest to pełny identyfikator zasobu platformy Azure tego elementu. |
| `Workbook Type` | Określ rodzaj szablonu skoroszytu do otwarcia. W najpopularniejszych przypadkach Użyj `Default` opcji lub, `Workbook` Aby użyć wartości w bieżącym skoroszycie. |
| `Gallery Type` | Określa typ galerii, który będzie wyświetlany w widoku "Galeria" szablonu, który zostanie otwarty. W najpopularniejszych przypadkach Użyj `Default` opcji lub, `Workbook` Aby użyć wartości w bieżącym skoroszycie. |
|`Location comes from` | Pole lokalizacji należy określić w przypadku otwierania określonego zasobu skoroszytu. Jeśli lokalizacja nie jest określona, wyszukiwanie zawartości skoroszytu jest znacznie wolniejsze. Jeśli znasz lokalizację, określ ją. Jeśli nie znasz lokalizacji lub otwierasz szablon, który nie ma określonej lokalizacji, pozostaw to pole jako "domyślne".|
|`Pass specific parameters to template` | Wybierz, aby przekazać określone parametry do szablonu. W przypadku wybrania tej wartości tylko określone parametry są przesyłane do szablonu. wszystkie parametry w bieżącym skoroszycie są przesyłane do szablonu, a w takim przypadku *nazwy* parametrów muszą być takie same w obu skoroszytach, aby ta wartość parametru działała.|
|`Workbook Template Parameters` | Ta sekcja zawiera definicje parametrów, które są przesyłane do szablonu docelowego. Nazwa powinna być zgodna z nazwą parametru w szablonie docelowym. Wybierz wartość z `Cell` , `Column` , `Parameter` , i `Static Value` . Nazwa i wartość nie mogą być puste, aby przekazać ten parametr do szablonu docelowego.|

Dla każdego z powyższych ustawień autor musi wybrać lokalizację, z której będą pochodzić wartości w połączonym skoroszycie. Zobacz [źródła linków](#link-sources)

Po otwarciu linku skoroszytu zostanie przeniesiony wszystkie wartości skonfigurowane z powyższych ustawień.

![Zrzut ekranu przedstawiający ustawienia linku skoroszytu](./media/workbooks-link-actions/workbook-link-settings.png)

![Zrzut ekranu ustawień parametrów szablonu skoroszytu](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Połącz źródła

| Element źródłowy | Wyjaśnienie |
|:------------- |:-------------|
| `Cell` | Spowoduje to użycie wartości w tej komórce w siatce jako wartości linku |
| `Column` | Po wybraniu zostanie wyświetlone inne pole umożliwiające autorowi wybranie innej kolumny w siatce.  Wartość tej kolumny dla wiersza zostanie użyta w wartości linku. Jest to często używane, aby włączyć każdy wiersz siatki, aby otworzyć inny szablon, przez ustawienie `Template Id` pola do `column` lub otworzyć ten sam szablon skoroszytu dla różnych zasobów, jeśli `Workbook resources` pole jest ustawione na kolumnę zawierającą identyfikator zasobu platformy Azure |
| `Parameter` | Po wybraniu zostanie wyświetlone inne pole, aby umożliwić autorowi wybranie parametru. Wartość tego parametru będzie używana dla wartości po kliknięciu linku |
| `Static value` | Po wybraniu zostanie wyświetlone inne pole, aby zezwolić typowi autora na wartość statyczną, która będzie używana w połączonym skoroszycie. Jest to często używane, gdy wszystkie wiersze w siatce będą używały tej samej wartości dla pola. |
| `Step` | Użyj wartości ustawionej w bieżącym kroku skoroszytu. Jest to typowy opis kroków zapytania i metryk, aby ustawić zasoby skoroszytu w połączonym skoroszycie do tych używanych *w kroku zapytania/metryki*, a nie w bieżącym skoroszycie |
| `Workbook` | Użyj wartości ustawionej w bieżącym skoroszycie. |
| `Default` | Użyj wartości domyślnej, która będzie używana, jeśli nie określono wartości. Jest to typowy dla typu galerii, gdzie domyślna Galeria zostanie ustawiona przez typ zasobu właściciela |

## <a name="next-steps"></a>Następne kroki

- [Kontroluj](../visualize/workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
- Dowiedz się, jak używać [grup w skoroszytach](../visualize/workbooks-groups.md).