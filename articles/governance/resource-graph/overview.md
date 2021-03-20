---
title: Omówienie usługi Azure Resource Graph
description: Dowiedz się, w jaki sposób usługa Azure Resource Graph umożliwia tworzenie złożonych zapytań dotyczących zasobów na dużą skalę między subskrypcjami i dzierżawcami.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: b5df124d07b8ecfb20f5dec08830d8156e8df2cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919145"
---
# <a name="what-is-azure-resource-graph"></a>Co to jest usługa Azure Resource Graph?

Azure Resource Graph to usługa platformy Azure, która została zaprojektowana w celu zwiększenia możliwości zarządzania zasobami platformy Azure, zapewniając wydajną i skuteczną eksplorację zasobów, dzięki czemu można wykonywać zapytania na dużą skalę w ramach danego zestawu subskrypcji, aby efektywnie zarządzać środowiskiem. Te zapytania zapewniają następujące funkcje:

- Zdolność do wysyłania zapytań do zasobów przy użyciu złożonego filtrowania, grupowania i sortowania według właściwości zasobu.
- Możliwość iteracyjnego eksplorowania zasobów na podstawie wymagań związanych z zarządzaniem.
- Zdolność do oceny wpływu stosowania zasad na ogromne środowisko chmury.
- Możliwość [szczegółowości zmian wprowadzonych do właściwości zasobów](./how-to/get-resource-changes.md) (wersja zapoznawcza).

W tej dokumentacji każda funkcja zostanie szczegółowo omówiona.

> [!NOTE]
> Wykres zasobów platformy Azure umożliwia pasek wyszukiwania Azure Portal, nowe środowisko przeglądania "wszystkie zasoby" i Azure Policye [](../policy/how-to/determine-non-compliance.md#change-history) 
>  _różnic wizualnych_ historii zmian. Jest ona przeznaczona do ułatwienia klientom zarządzania środowiskami w dużej skali.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Jak usługa Resource Graph uzupełnia usługę Azure Resource Manager

Menedżer zasobów obecnie obsługuje zapytania dotyczące podstawowych pól zasobów, w tym w odniesieniu do nazwy zasobu, identyfikatora, typu, grupy zasobów, subskrypcji i lokalizacji. Menedżer zasobów również udostępnia funkcje do wywoływania poszczególnych dostawców zasobów w celu uzyskania szczegółowych właściwości jednego zasobu naraz.

Za pomocą usługi Azure Resource Graph możesz uzyskać dostęp do tych właściwości, które zwracają dostawców zasobów, bez konieczności wykonywania poszczególnych wywołań do każdego dostawcy zasobów. Aby uzyskać listę obsługiwanych typów zasobów, przejrzyj informacje o [typie tabeli i zasobu](./reference/supported-tables-resources.md). Alternatywny sposób wyświetlania obsługiwanych typów zasobów znajduje się w [przeglądarce schematów programu Graph zasobów platformy Azure](./first-query-portal.md#schema-browser).

Za pomocą usługi Azure Resource Graph można:

- Dostęp do właściwości zwracanych przez dostawców zasobów bez konieczności wykonywania pojedynczych wywołań do każdego dostawcy zasobów.
- Wyświetlenie ostatnich 14 dni historii zmian w zasobie, aby zobaczyć, jakie właściwości zostały zmienione i kiedy. (wersja zapoznawcza)

> [!NOTE]
> Jako funkcja w _wersji zapoznawczej_ niektóre `type` obiekty mają dodatkowe dostępne właściwości inne niż Menedżer zasobów. Aby uzyskać więcej informacji, zobacz [właściwości rozszerzone (wersja zapoznawcza)](./concepts/query-language.md#extended-properties).

## <a name="how-resource-graph-is-kept-current"></a>Sposób przechowywania grafu zasobów

Gdy zasób platformy Azure zostanie zaktualizowany, wykres zasobów zostanie powiadomiony o Menedżer zasobów zmian.
Następnie Wykres zasobów aktualizuje swoją bazę danych. Wykres zasobów wykonuje także regularne _pełne skanowanie_. To skanowanie gwarantuje, że dane grafu zasobów są aktualne w przypadku braku powiadomień lub gdy zasób jest aktualizowany poza Menedżer zasobów.

> [!NOTE]
> Wykres zasobów używa `GET` do najnowszego interfejsu API, który nie jest w wersji zapoznawczej dla każdego dostawcy zasobów do zbierania właściwości i wartości. W związku z tym oczekiwana właściwość może być niedostępna. W niektórych przypadkach użyta wersja interfejsu API została zastąpiona, aby zapewnić bardziej aktualne lub szeroko używane właściwości w wynikach. Pełną listę w danym środowisku zawiera przykład [pokazujący wersję interfejsu API dla każdego typu zasobu](./samples/advanced.md#apiversion) .

## <a name="the-query-language"></a>Język zapytań

Teraz, gdy znasz już usługę Azure Resource Graph, przyjrzyjmy się szczegółowe do tworzenia zapytań.

Ważne jest zrozumienie, że język zapytań usługi Azure Resource Graph opiera się na języku zapytań [Kusto Query Language](/azure/data-explorer/data-explorer-overview) używanym przez usługę Azure Data Explorer.

Po pierwsze, aby poznać szczegółowe informacje dotyczące operacji i funkcji, które mogą być używane z usługą Azure Resource Graph, zobacz [język zapytań usługi Resource Graph](./concepts/query-language.md). Aby przejrzeć zasoby, zobacz [badanie zasobów](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Uprawnienia w usłudze Azure Resource Graph

Aby korzystać z grafu zasobów, musisz dysponować odpowiednimi prawami w [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md) z co najmniej dostępem do odczytu do zasobów, które chcesz zbadać. Bez uprawnień do obiektu lub grupy obiektów platformy Azure na poziomie co najmniej `read` wyniki nie będą zwracane.

> [!NOTE]
> Wykres zasobów używa subskrypcji dostępnych dla podmiotu zabezpieczeń podczas logowania. Aby wyświetlić zasoby nowej subskrypcji dodane podczas aktywnej sesji, podmiot zabezpieczeń musi odświeżyć kontekst. Ta akcja odbywa się automatycznie podczas wylogowywania się i z powrotem.

Interfejs wiersza polecenia platformy Azure i Azure PowerShell używają subskrypcji, do których użytkownik ma dostęp. W przypadku bezpośredniego korzystania z interfejsu API REST Lista subskrypcji jest udostępniana przez użytkownika. Jeśli użytkownik ma dostęp do dowolnych subskrypcji na liście, wyniki zapytania są zwracane dla subskrypcji, do których użytkownik ma dostęp. Takie zachowanie jest takie samo jak podczas wywoływania [grup zasobów — lista](/rest/api/resources/resourcegroups/list) \- Pobiera grupy zasobów, do których masz dostęp, bez wskazywania, że wynik może być częściowy. Jeśli na liście subskrypcji nie ma żadnych subskrypcji, do których użytkownik ma odpowiednie prawa, odpowiedź jest _403_ (niedostępna).

> [!NOTE]
> W wersji **zapoznawczej** interfejsu API REST `2020-04-01-preview` Lista subskrypcji może być ommitted.
> Gdy `subscriptions` `managementGroupId` właściwości i nie są zdefiniowane w żądaniu, _zakres_ jest ustawiany dla dzierżawy. Aby uzyskać więcej informacji, zobacz [zakres zapytania](./concepts/query-language.md#query-scope).

## <a name="throttling"></a>Ograniczanie przepływności

Jako bezpłatna usługa, zapytania do grafu zasobów są ograniczone, aby zapewnić najlepszą wydajność i czas odpowiedzi dla wszystkich klientów. Jeśli Twoja organizacja chce używać interfejs API programu Graph zasobów w przypadku dużych i częstych zapytań, należy użyć portalu "opinia" na [stronie portalu grafu zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Podaj swój przypadek biznesowy i zaznacz pole wyboru "Firma Microsoft może wysłać wiadomość e-mail o opinię", aby zespół mógł się z Tobą skontaktować.

Wykres zasobów ogranicza zapytania na poziomie użytkownika. Odpowiedź usługi zawiera następujące nagłówki HTTP:

- `x-ms-user-quota-remaining` (int): pozostały przydział zasobów dla użytkownika. Ta wartość jest mapowana na liczbę zapytań.
- `x-ms-user-quota-resets-after` (hh: mm: SS): czas trwania do momentu zresetowania zużycia przydziału użytkownika

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące żądań z ograniczeniami](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Uruchamianie pierwszego zapytania

Eksplorator grafu zasobów platformy Azure, część Azure Portal, umożliwia uruchamianie zapytań wykresów zasobów bezpośrednio w programie Azure Portal. Przypnij wyniki jako wykresy dynamiczne, aby zapewnić dynamiczne informacje w czasie rzeczywistym do przepływu pracy portalu. Aby uzyskać więcej informacji, zobacz [pierwsze zapytanie z Eksploratorem zasobów platformy Azure](./first-query-portal.md).

Wykres zasobów obsługuje interfejs wiersza polecenia platformy Azure, Azure PowerShell, zestaw Azure SDK dla języka Python i nie tylko. Zapytanie ma taką samą strukturę dla każdego języka. Dowiedz się, jak włączyć Wykres zasobów przy użyciu:

- [Azure Portal i Eksplorator wykresu zasobów](./first-query-portal.md) 
- [Interfejs wiersza polecenia platformy Azure](./first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](./first-query-powershell.md#add-the-resource-graph-module)
- [Python](./first-query-python.md#add-the-resource-graph-library)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [języku zapytań](./concepts/query-language.md).
- Zobacz język używany w [zapytaniach początkowych](./samples/starter.md).
- Zobacz zaawansowane zastosowania w [zaawansowanych zapytaniach](./samples/advanced.md).
