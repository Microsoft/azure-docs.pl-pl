---
title: Grupy skoroszytów Azure Monitor
description: Jak używać grup w skoroszytach Azure Monitor.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: b6377cdcdb5816426eba62fdbef79eeb42659dcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82892084"
---
# <a name="how-to-use-groups-in-workbooks"></a>Jak używać grup w skoroszytach

Element grupy w skoroszycie umożliwia logiczne grupowanie zestawu kroków w skoroszycie.

Grupy w skoroszytach są przydatne w kilku kwestiach:

- Układ
  - W scenariuszach, w których elementy mają być zorganizowane w pionie, można utworzyć grupę elementów, które będą miały wszystkie stosy, i ustawić styl grupy jako szerokość procentową, a nie ustawienie szerokości procentowej dla wszystkich poszczególnych elementów.
- Widoczność
  - W scenariuszach, w których wiele elementów ma być ukrywanych i wyświetlanych razem, można ustawić widoczność całej grupy elementów, zamiast ustawiać ustawienia widoczności poszczególnych elementów. Może to być przydatne w szablonach korzystających z kart, jak można użyć grupy jako zawartości karty, a cała grupa może być ukryta/pokazana na podstawie parametru ustawionego przez wybraną kartę.
- Wydajność
  - W przypadkach, gdy masz bardzo duży szablon z wieloma sekcjami lub kartami, Możesz skonwertować każdą sekcję na własny szablon podrzędny i użyć grup do załadowania wszystkich szablonów podrzędnych w ramach szablonu najwyższego poziomu. Zawartość szablonów podrzędnych nie zostanie załadowana ani uruchomiona, dopóki użytkownik nie będzie mógł wyświetlić tych grup. Dowiedz się więcej o tym [, jak podzielić duży szablon na wiele szablonów](#how-to-split-a-large-template-into-many-templates).

## <a name="using-groups"></a>Korzystanie z grup

Aby dodać grupę do skoroszytu, wybierz pozycję *Dodaj* , a następnie *Dodaj grupę*.

![Wybierz pozycję Dodaj następnie grupę.](./media/workbooks-groups/add-group.png)

Poniżej znajduje się zrzut ekranu grupy w trybie odczytu z dwoma elementami wewnątrz: element tekstowy i element zapytania.  

![Grupa w trybie do czytania.](./media/workbooks-groups/groups-view.png)

Podczas edytowania skoroszytu można zobaczyć, że te dwa elementy znajdują się w elemencie grupy:

![Grupuj w trybie edycji. ](./media/workbooks-groups/groups-edit.png)

Na poniższym zrzucie ekranu Grupa jest w trybie edycji, pokazując, że zawiera dwa elementy (w obszarze kreskowanym). Każdy krok może być w trybie edycji lub odczytu niezależnie od siebie. Na przykład, krok tekstu jest w trybie edycji, gdy krok zapytania jest w trybie odczytu.

## <a name="scoping"></a>Zakresów

Obecnie Grupa jest traktowana jako nowy zakres w skoroszycie. Wszystkie parametry utworzone w grupie są widoczne tylko w grupie. Ma to również wartość true w przypadku scalania, ale tylko dane wewnątrz grupy lub na poziomie nadrzędnym.

## <a name="group-types"></a>Typy grup

Element grupy skoroszytów umożliwia dodanie grupy elementów do skoroszytu. Jako autor skoroszytu należy określić, który typ grupy będzie. Istnieją dwa typy grup:

- Modyfikować
  - Grupa w skoroszycie umożliwia dodawanie, usuwanie i Edytowanie zawartości elementów w grupie. Jest to najczęściej używane do celów układu i widoczności.
- Z szablonu
  - Grupa w skoroszycie ładuje zawartość innego szablonu według identyfikatora. Zawartość tego szablonu zostanie załadowana i scalona w skoroszycie w czasie wykonywania. W trybie edycji nie można modyfikować żadnej zawartości grupy, ponieważ po raz kolejny zostanie załadowany z szablonu.  

## <a name="load-types"></a>Ładowanie typów

Istnieje kilka różnych sposobów załadowania zawartości grupy. Jako autor skoroszytu możesz określić, kiedy i w jaki sposób zawartość grupy ma zostać załadowana.

### <a name="lazy-the-default"></a>Z opóźnieniem (wartość domyślna)

Grupa zostanie załadowana tylko wtedy, gdy element jest widoczny. Pozwala to na użycie grupy przez elementy tabulacji. Jeśli karta nie jest zaznaczona, Grupa nigdy nie staną się widoczne, w związku z czym zawartość nie zostanie załadowana.

W przypadku grup utworzonych na podstawie szablonu zawartość szablonu nie jest pobierana, a elementy w grupie nie są tworzone, dopóki Grupa nie staną się widoczne. Podczas pobierania zawartości użytkownik zobaczy pokrętła postępu dla całej grupy.

### <a name="explicit"></a>Jawna

W tym trybie przycisk jest wyświetlany, gdy grupa będzie się znajdować i żadna zawartość nie zostanie pobrana lub utworzona do momentu, gdy użytkownik jawnie kliknie przycisk w celu załadowania zawartości. Jest to przydatne w scenariuszach, w których zawartość może być kosztowna do obliczeń lub rzadko używanych. Autor może określić tekst, który ma być wyświetlany na przycisku.

Poniżej znajduje się zrzut ekranu przedstawiający jawne ustawienia ładowania pokazujący skonfigurowany przycisk "Załaduj więcej".

![Ustawienia jawne ładowania](./media/workbooks-groups/groups-explicitly-loaded.png)

Grupa przed załadowaniem w skoroszycie:

![Grupa jawna przed załadowaniem w skoroszycie](./media/workbooks-groups/groups-explicitly-loaded-before.png)

Grupa po załadowaniu w skoroszycie:

![Grupa jawna po załadowaniu w skoroszycie](./media/workbooks-groups/groups-explicitly-loaded-after.png)

### <a name="always"></a>Zawsze

W tym trybie zawartość grupy jest zawsze ładowana i tworzona zaraz po załadowaniu skoroszytu. Jest to najczęściej używane w przypadku używania grupy tylko do celów układu, gdzie zawartość będzie zawsze widoczna.

## <a name="using-templates-inside-a-group"></a>Używanie szablonów wewnątrz grupy

Gdy grupa jest skonfigurowana do ładowania z szablonu, zawartość zostanie domyślnie załadowana z opóźnieniem, tylko wtedy, gdy grupa jest widoczna.

Po załadowaniu szablonu do grupy skoroszyt próbuje scalić wszystkie parametry zadeklarowane w szablonie z parametrami już istniejącymi w grupie. Wszystkie parametry, które już istnieją w skoroszycie o identycznych nazwach, zostaną scalone z załadowanego szablonu. Jeśli wszystkie parametry w kroku parametru są scalane, cały krok parametrów zniknie.

### <a name="example-1-all-parameters-have-identical-names"></a>Przykład 1: wszystkie parametry mają identyczne nazwy

Rozważmy szablon, który ma dwa parametry w górnej części.

- `TimeRange` -parametr zakresu czasu.
- `Filter` -Parametr tekstowy.

![Edytowanie elementu parametrów: "parametry najwyższego poziomu"](./media/workbooks-groups/groups-top-level-params.png)

Następnie element grupy ładuje drugi szablon, który ma własne dwa parametry i krok tekstu, gdzie parametry są takie same:

![Edytowanie elementu parametru dla drugiego szablonu](./media/workbooks-groups/groups-merged-away.png)

Po załadowaniu drugiego szablonu do grupy, zduplikowane parametry zostaną scalone. Ze względu na to, że wszystkie parametry są scalane, krok parametrów wewnętrznych jest również scalany, co powoduje utworzenie grupy zawierającej tylko krok tekstu.

### <a name="example-2-one-parameter-has-an-identical-name"></a>Przykład 2: jeden parametr ma identyczną nazwę

Rozważmy szablon grupy, który ma dwa parametry u góry.

- `TimeRange` -parametr zakresu czasu.
- `FilterB` -Parametr tekstowy, należy zauważyć, że nie jest `Filter` tak samo jak w przypadku górnego szablonu.

![Edytowanie elementu grupy z wynikiem parametrów scalonych](./media/workbooks-groups/groups-wont-merge-away.png)

Po załadowaniu szablonu item's grupy `TimeRange` parametr zostanie scalony z grupy. Następnie skoroszyt będzie miał krok początkowy parametrów z `TimeRange` i `Filter` , a krok parametru grupy będzie zawierać tylko `FilterB`

![wynik parametrów, które nie zostaną scalone](./media/workbooks-groups/groups-wont-merge-away-result.png)

Jeśli załadowany szablon zawiera `TimeRange` i `Filter` (zamiast `FilterB` ), wynikowy skoroszyt będzie miał krok parametrów i grupę z tylko pozostałym krokiem tekstu.

## <a name="how-to-split-a-large-template-into-many-templates"></a>Jak podzielić duży szablon na wiele szablonów

Aby zwiększyć wydajność, korzystne jest rozbicie dużego szablonu do wielu mniejszych szablonów, które ładują zawartość z opóźnieniem lub na żądanie użytkownika. Powoduje to szybsze ładowanie, ponieważ szablon najwyższego poziomu może być dużo mniejszy.

W przypadku dzielenia szablonu na części należy efektywnie podzielić szablon na wiele szablonów (podrzędnych szablonów), które wszystkie działają indywidualnie. Dlatego jeśli szablon najwyższego poziomu zawiera `TimeRange` parametr, który jest używany przez inne kroki, szablon podrzędny będzie musiał również mieć krok parametrów, który definiuje parametr z dokładną nazwą. Dzięki temu szablony podrzędne działają niezależnie i umożliwiają ich ładowanie w większych szablonach w grupach.

Aby przekształcić większy szablon w kilka szablonów podrzędnych:

1.  Utwórz nową pustą grupę w górnej części skoroszytu po parametrach współużytkowanych. Ta nowa grupa stanie się ostatecznie szablonem podrzędnym.
2. Utwórz kopię kroku parametrów wspólnych, a następnie użyj elementu *Przenieś do grupy* , aby przenieść kopię do grupy utworzonej w kroku 1. Ten krok parametrów umożliwi pracę szablonu podrzędnego niezależnie od szablonu zewnętrznego i zostanie scalony po załadowaniu wewnątrz szablonu zewnętrznego.
    > [!NOTE]
    > Podszablon nie musi mieć technicznych parametrów, które mają zostać scalone, jeśli nie zamierzasz korzystać z szablonów podrzędnych. Jednak będzie to bardzo trudne do edytowania lub debugowania, jeśli trzeba to zrobić później.

3. Przenieś każdy element w skoroszycie, który ma znajdować się w szablonie podrzędnym, do grupy utworzonej w kroku 1.
4. Jeśli poszczególne kroki przenoszone w kroku 3 miały warunkowe widoczności, które staną się widocznością grupy zewnętrznej (na przykład używane na kartach). Usuń je z elementów znajdujących się w grupie i Dodaj je do samej grupy. Zapisz tutaj, aby uniknąć utraty zmian i/lub wyeksportować i zapisać kopię zawartości JSON.
5. Jeśli chcesz, aby ta grupa została załadowana z szablonu, możesz użyć przycisku *Edytuj* pasek narzędzi w grupie. Spowoduje to otwarcie tylko zawartości tej grupy jako skoroszytu w nowym oknie. Następnie można zapisać go zgodnie z potrzebami i zamknąć ten widok skoroszytu (nie zamykaj przeglądarki, tylko ten widok, aby wrócić do poprzedniego edytowanego skoroszytu).
6. Następnie można zmienić krok grupy w celu załadowania z szablonu i ustawienia pola Identyfikator szablonu do skoroszytu/szablonu utworzonego w kroku 5. Aby pracować z identyfikatorami skoroszytów, Źródło musi być IDENTYFIKATORem zasobu udostępnionego skoroszytu. Naciśnij pozycję *Załaduj* , a zawartość tej grupy zostanie teraz załadowana z tego szablonu podrzędnego, a nie została zapisana w tym zewnętrznym skoroszycie.

## <a name="next-steps"></a>Następne kroki
- [Przegląd skoroszytów](workbooks-overview.md)
- [Używanie wykryto ze skoroszytami](workbooks-jsonpath.md)