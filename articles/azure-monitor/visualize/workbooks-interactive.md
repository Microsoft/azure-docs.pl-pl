---
title: Azure Monitor skoroszytów z parametrami niestandardowymi
description: Uprość złożone raportowanie za pomocą wstępnie skompilowanych i niestandardowych skoroszytów z parametrami
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 2cb284e1978ad6c890835318c51c6095891397cd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723014"
---
# <a name="interactive-workbooks"></a>Interakcyjne skoroszyty

Skoroszyty umożliwiają autorom tworzenie interaktywnych raportów i środowisk dla klientów. Interaktywność jest obsługiwana na wiele sposobów.

## <a name="parameter-changes"></a>Zmiany parametrów

Gdy użytkownik skoroszytu aktualizuje parametr, każda kontrolka, która używa parametru automatycznie odświeża i ponownie rysuje, w celu odzwierciedlenia nowego stanu. Poniżej przedstawiono, jak większość raportów Azure Portal obsługuje interaktywność. Skoroszyty umożliwiają to w prosty sposób z minimalnym nakładem pracy użytkowników.

Dowiedz się więcej [na temat parametrów w skoroszytach](workbooks-parameters.md)

## <a name="grid-tile-chart-selections"></a>Siatka, kafelek, wybory wykresu

Skoroszyty umożliwiają autorom konstruowanie scenariuszy, w których kliknięcie wiersza w siatce aktualizuje kolejne wykresy na podstawie zawartości wiersza.

Na przykład użytkownik może mieć siatkę wyświetlającą listę żądań i pewne statystyki, takie jak liczba awarii. Mogą je skonfigurować w taki sposób, aby klikał wiersz odpowiadający żądaniu, co spowoduje, że szczegółowe wykresy poniżej aktualizacji, aby odfiltrować tylko te żądania.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Konfigurowanie interakcji z wierszem siatki — kliknięcie

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
2. Użyj linku _Dodawanie zapytania_ , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz typ zapytania jako _Dziennik_, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy

    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc
    ```

5. `Run query` Aby wyświetlić wyniki
6. Wybierz ikonę _Ustawienia zaawansowane_ w stopce zapytania (ikona wygląda jak koła zębate). Spowoduje to otwarcie okienka ustawienia zaawansowane.
7. Sprawdź ustawienie: `When an item is selected, export a parameter` .
8. W obszarze zaznaczone ustawienie wybierz pozycję *Dodaj parametr* i wypełnij je poniższymi informacjami.
    1. Pole do wyeksportowania: `Request`
    2. Nazwa parametru: `SelectedRequest`
    3. Wartość domyślna: `All requests`
9. Wybieranie pozycji Zapisz

    ![Zrzut ekranu przedstawiający zaawansowany edytor z ustawieniami eksportu pól jako parametrów.](./media/workbooks-interactive/export-parameters-add.png)

10. Wybierz pozycję `Done Editing`.
11. Dodaj kolejną kontrolkę zapytania, wykonując kroki 2 i 3.
12. Użyj edytora zapytań, aby wprowadzić KQL do analizy.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. `Run query` , aby wyświetlić wyniki.
14. Zmień _wizualizację_ na `Area chart` .
15. Wybierz wiersz, który ma zostać wybrany w pierwszej siatce. Zwróć uwagę na to, jak wykres warstwowy poniżej filtruje wybrane żądanie.

Wynikowy Raport wygląda następująco w trybie edycji:

![Zrzut ekranu przedstawiający pierwsze dwa zapytania w trybie edycji.](./media/workbooks-interactive//interactivity-grid-create.png)

Na poniższej ilustracji przedstawiono bardziej rozbudowany raport interaktywny w trybie odczytu na podstawie tych samych zasad. Raport używa kliknięć siatki do eksportowania parametrów, które z kolei są używane w dwóch wykresach i bloku tekstu.

![Zrzut ekranu raportu przy użyciu kliknięć siatki w trybie odczytu.](./media/workbooks-interactive/interactivity-grid-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Eksportowanie zawartości całego wiersza

Czasami warto wyeksportować całą zawartość zaznaczonego wiersza, a nie tylko określoną kolumnę. W takich przypadkach należy pozostawić `Field to export` Właściwość nieustawioną w kroku 7,1 powyżej. Skoroszyty eksportują całą zawartość wiersza jako plik JSON do parametru.

W kontrolce odwołującej się do KQL Użyj `todynamic` funkcji, aby przeanalizować kod JSON i uzyskać dostęp do poszczególnych kolumn.

## <a name="grid-cell-clicks"></a>Kliknięcia komórki siatki

Skoroszyty umożliwiają autorom Dodawanie interaktywności za pośrednictwem specjalnego typu modułu renderowania kolumn siatki o nazwie a `link renderer` . Moduł renderujący linków konwertuje komórkę siatki na hiperłącze na podstawie zawartości komórki. Skoroszyty obsługują wiele rodzajów renderowania linków — w tym te, które umożliwiają otwieranie bloków przeglądów zasobów, podglądów zbioru właściwości, wyszukiwanie w usłudze App Insights, użycie, śledzenie transakcji itd.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Ustawianie interakcji za pomocą kliknięć komórek siatki

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
2. Użyj linku _Dodawanie zapytania_ , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz typ zapytania jako _Dziennik_, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy

    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```

5. `Run query` Aby wyświetlić wyniki
6. Wybierz pozycję _Ustawienia kolumny_ , aby otworzyć okienko ustawienia.
7. W sekcji _kolumny_ Ustaw:
    1. Renderowanie w postaci _przykładowej_ kolumny: `Link` , Wyświetl, aby otworzyć: `Cell Details` , etykieta linku:`Sample`
    2.  Moduł renderowania kolumn: `Bar` , paleta kolorów: `Blue` , wartość minimalna:`0`
    3. Moduł renderowania kolumn _żądania_ :`Automatic`
    4. Wybierz pozycję _Zapisz i Zamknij_ , aby zastosować zmiany

    ![Zrzut ekranu przedstawiający kartę Ustawienia kolumny.](./media/workbooks-interactive/column-settings.png)

8. Kliknij jeden z `Sample` linków w siatce. Spowoduje to otwarcie okienka z szczegółowymi informacjami o przykładowym żądaniu.

    ![Zrzut ekranu przedstawiający okienko Szczegóły przykładowego żądania.](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>Akcje modułu renderowania linków

| Akcja łącza | Akcja po kliknięciu |
|:------------- |:-------------|
| `Generic Details` | Pokazuje wartości wierszy na karcie kontekstowej siatki właściwości |
| `Cell Details` | Pokazuje wartość komórki na karcie kontekstowej siatki właściwości. Przydatne, gdy komórka zawiera typ dynamiczny z informacjami (na przykład JSON z właściwościami żądania, takimi jak lokalizacja, wystąpienie roli itp.). |
| `Cell Details` | Pokazuje wartość komórki na karcie kontekstowej siatki właściwości. Przydatne, gdy komórka zawiera typ dynamiczny z informacjami (na przykład JSON z właściwościami żądania, takimi jak lokalizacja, wystąpienie roli itp.). |
| `Custom Event Details` | Otwiera Application Insights szczegóły wyszukiwania z niestandardowym IDENTYFIKATORem zdarzenia ( `itemId` ) w komórce |
| `* Details` | Podobnie jak w przypadku szczegółów zdarzenia niestandardowego, z wyjątkiem zależności, wyjątków, wyświetlania stron, żądań i śladów. |
| `Custom Event User Flows` | Otwiera środowisko Przepływy użytkownika Application Insights przestawiane na nazwę zdarzenia niestandardowego w komórce |
| `* User Flows` | Podobne do Przepływy użytkownika zdarzeń niestandardowych z wyjątkiem wyjątków, wyświetleń stron i żądań |
| `User Timeline` | Otwiera oś czasu użytkownika z IDENTYFIKATORem użytkownika (user_Id) w komórce |
| `Session Timeline` | Otwiera środowisko wyszukiwania Application Insights dla wartości w komórce (na przykład Wyszukaj tekst "ABC", gdzie ABC jest wartością w komórce) |
| `Resource overview` | Otwórz przegląd zasobów w portalu na podstawie wartości identyfikatora zasobu w komórce. |

## <a name="conditional-visibility"></a>Widoczność warunkowa

Skoroszyt umożliwia użytkownikom wyświetlanie lub ukrywanie określonych kontrolek na podstawie wartości parametrów. Dzięki temu Autorzy mają różne raporty na podstawie stanu danych wejściowych lub telemetrii użytkownika. Przykład pokazuje, że użytkownicy są tylko podsumowaniem, gdy są dobre, ale wyświetlają pełne szczegóły, gdy coś się nie stało.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Konfigurowanie interaktywności przy użyciu widoczności warunkowej

1. Wykonaj kroki opisane w sekcji [Konfigurowanie interakcji z wierszem siatki kliknij pozycję](#setting-up-interactivity-on-grid-row-click) sekcja, aby skonfigurować dwa formanty interaktywne.
2. Dodaj nowy parametr u góry:
    1. Nazwa: `ShowDetails`
    2. Typ parametru: `Drop down`
    3. Wymagane: `checked`
    4. Pobierz dane z: `JSON`
    5. Dane wejściowe JSON: `["Yes", "No"]`
    6. Zapisz, aby zatwierdzić zmiany.

    ![Po wybraniu przycisku Dodaj parametr zostanie wyświetlone okienko Edytowanie parametru.](./media/workbooks-interactive/edit-parameter.png)

3. Ustaw wartość parametru na `Yes`

    ![Nad przyciskiem gotowe do edycji znajduje się lista rozwijana, która umożliwia ustawienie wartości parametru](./media/workbooks-interactive/set-parameter.png)

4. W kontrolce zapytania z wykresem warstwowym wybierz ikonę _Ustawienia zaawansowane_ (ikona koła zębatego)
5. Sprawdź ustawienie `Make this item conditionally visible`
    1. Ten element jest widoczny, jeśli `ShowDetails` wartość `equals` parametru `Yes`
6. Wybierz pozycję _gotowe do edycji_ , aby zatwierdzić zmiany.
7. Wybierz pozycję _gotowe edytowanie_ na pasku narzędzi skoroszytu, aby przejść do trybu odczytu.
8. Zmień wartość parametru `ShowDetails` na `No` . Zauważ, że wykres poniżej zniknie.

Na poniższym obrazie pokazano widoczny przypadek, w którym `ShowDetails` jest `Yes`

![Zrzut ekranu przedstawiający widoczność warunkową, w której wykres jest widoczny](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

Na poniższej ilustracji przedstawiono ukryty przypadek, w którym `ShowDetails` jest `No`

![Zrzut ekranu przedstawiający widoczność warunkową, w której wykres jest ukryty](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>Interaktywność z wieloma opcjami w siatkach i wykresach

Kroki zapytania i metryk mogą również eksportować jeden lub więcej parametrów, gdy jest zaznaczony wiersz (lub wiele wierszy).

![Zrzut ekranu przedstawiający ustawienia eksportu parametrów z wieloma parametrami. ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. W kroku zapytania wyświetlającym siatkę przejdź do pozycji Ustawienia zaawansowane.
2. Zaznacz `When items are selected, export parameters` pole wyboru. Zostaną wyświetlone dodatkowe kontrolki.
3. Zaznacz `allow selection of multiple values` pole wyboru.
    1. W wyświetlonej wizualizacji wartości parametrów z wybieraniem i wyeksportowanym parametrem będą tablicą wartości, na przykład w przypadku używania parametrów listy rozwijanej wieloselect.
    2. W przypadku usunięcia zaznaczenia Wizualizacja wyświetlania będzie uwzględniać tylko ostatni wybrany element. Tylko Eksportowanie pojedynczej wartości naraz.
4. Dla każdego parametru, który chcesz wyeksportować, użyj przycisku *Dodaj parametr* . Zostanie wyświetlone okno podręczne zawierające ustawienia dla parametru do wyeksportowania.

Po włączeniu pojedynczej opcji autor może określić, które pole oryginalnych danych ma zostać wyeksportowane. Pola obejmują nazwę parametru, typ parametru i wartość domyślną, która ma być używana, jeśli nic nie jest zaznaczone (opcjonalnie).

Gdy włączono wiele zaznaczeń, autor określa pola oryginalnych danych do wyeksportowania. Pola obejmują nazwę parametru, typ parametru, cudzysłów i ogranicznik. Wartości cytatu i ogranicznika są używane podczas zamieniania wartości ze strzałką na tekst, gdy zostanie on zastąpiony w zapytaniu. W obszarze wiele zaznaczeń, jeśli nie są zaznaczone żadne wartości, wartość domyślna to pusta tablica.

> [!NOTE]
> W przypadku wybrania opcji wyboru tylko unikatowe wartości zostaną wyeksportowane, nie będą wyświetlane wartości tablicy wyjściowej, takie jak "1, 1, 2, 1", jako wartości wyjściowe otrzymasz wartość "1, 2".

Ustawienie "pole do eksportowania" można pozostawić puste w ustawieniach eksportu. Jeśli to zrobisz, wszystkie dostępne pola w danych zostaną wyeksportowane jako obiekt skonwertowanej JSON klucza: value Pars. W przypadku siatek i tytułów będą to wszystkie pola siatki. W przypadku wykresów dostępne pola to x, y, serie i etykiety (w zależności od typu wykresu).

Chociaż domyślnym zachowaniem jest eksportowanie parametru jako tekstu, Jeśli wiesz, że pole jest subskrypcją lub IDENTYFIKATORem zasobu, użyj tego jako typu parametru eksportu. Pozwoli to na użycie parametru w miejscach, które wymagają tych typów parametrów.

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](./workbooks-overview.md#visualizations) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.
* [Kontroluj](./workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.