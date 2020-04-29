---
title: Azure Monitor skoroszytów z parametrami niestandardowymi
description: Uprość złożone raportowanie za pomocą wstępnie skompilowanych i niestandardowych skoroszytów z parametrami
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658273"
---
# <a name="interactive-workbooks"></a>Interakcyjne skoroszyty

Skoroszyty umożliwiają autorom tworzenie interaktywnych raportów i środowisk dla klientów. Interaktywność jest obsługiwana na wiele sposobów.

## <a name="parameter-changes"></a>Zmiany parametrów
Gdy użytkownik skoroszytu aktualizuje parametr, każda kontrolka, która używa parametru automatycznie odświeża i ponownie rysuje, w celu odzwierciedlenia nowego stanu. Poniżej przedstawiono, jak większość raportów Azure Portal obsługuje interaktywność. Skoroszyty umożliwiają to w bardzo prosty sposób z minimalnym nakładem pracy użytkowników.

Dowiedz się więcej [na temat parametrów w skoroszytach](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Kliknięcia wierszy siatki
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
5. `Run query`Aby wyświetlić wyniki
6. Kliknij ikonę _Ustawienia zaawansowane_ w stopce zapytania (ikona wygląda jak koła zębate). Spowoduje to otwarcie okienka ustawień zaawansowanych 
7. Sprawdź ustawienie:`When an item is selected, export a parameter`
    1. Pole do wyeksportowania:`Request`
    2. Nazwa parametru:`SelectedRequest`
    3. Wartość domyślna:`All requests`
    
    ![Obraz przedstawiający zaawansowany edytor z ustawieniami eksportu pól jako parametrów](./media/workbooks-interactive/advanced-settings.png)

8. Kliknij pozycję `Done Editing` (Dalej).
9. Dodaj kolejną kontrolkę zapytania, wykonując kroki 2 i 3.
10. Użyj edytora zapytań, aby wprowadzić KQL do analizy
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`, aby wyświetlić wyniki.
12. Zmień _wizualizację_ na`Area chart`
12. Kliknij wiersz w pierwszej siatce. Zwróć uwagę na to, jak wykres warstwowy poniżej filtruje wybrane żądanie.

Wynikowy Raport wygląda następująco w trybie edycji:

![Obraz przedstawiający tworzenie środowiska interaktywnego za pomocą kliknięć wierszy siatki](./media/workbooks-interactive//grid-click-create.png)

Na poniższej ilustracji przedstawiono bardziej rozbudowany raport interaktywny w trybie odczytu na podstawie tych samych zasad. Raport używa kliknięć siatki do eksportowania parametrów, które z kolei są używane w dwóch wykresach i bloku tekstu.

![Obraz przedstawiający tworzenie środowiska interaktywnego za pomocą kliknięć wierszy siatki](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Eksportowanie zawartości całego wiersza
Czasami warto wyeksportować całą zawartość zaznaczonego wiersza, a nie tylko określoną kolumnę. W takich przypadkach należy pozostawić `Field to export` Właściwość nieustawioną w kroku 7,1 powyżej. Skoroszyty eksportują całą zawartość wiersza jako plik JSON do parametru. 

W kontrolce odwołującej się do `todynamic` KQL Użyj funkcji, aby przeanalizować kod JSON i uzyskać dostęp do poszczególnych kolumn.

 ## <a name="grid-cell-clicks"></a>Kliknięcia komórki siatki
Skoroszyty umożliwiają autorom Dodawanie interaktywności za pośrednictwem specjalnego typu modułu renderowania kolumn siatki `link renderer`o nazwie a. Moduł renderujący linków konwertuje komórkę siatki na hiperłącze na podstawie zawartości komórki. Skoroszyty obsługują wiele rodzajów renderowania linków — w tym te, które umożliwiają otwieranie bloków przeglądów zasobów, podglądów zbioru właściwości, wyszukiwanie w usłudze App Insights, użycie, śledzenie transakcji itd.

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
5. `Run query`Aby wyświetlić wyniki
6. Kliknij pozycję _Ustawienia kolumny_ , aby otworzyć okienko ustawienia.
7. W sekcji _kolumny_ Ustaw:
    1. Renderowanie w postaci _przykładowej_ kolumny: `Link`, Wyświetl `Cell Details`, aby otworzyć:, etykieta linku:`Sample`
    2. _Count_ Moduł renderowania kolumn: `Bar`, paleta kolorów: `Blue`, wartość minimalna:`0`
    3. Moduł renderowania kolumn _żądania_ :`Automatic`
    4. Kliknij przycisk _Zapisz i Zamknij,_ aby zastosować zmiany
8. Kliknij jeden z `Sample` linków w siatce. Spowoduje to otwarcie okienka właściwości zawierającego szczegóły przykładowego żądania.

    ![Obraz przedstawiający tworzenie interakcyjnego środowiska przy użyciu komórki siatki](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Akcje modułu renderowania linków
| Akcja łącza | Akcja po kliknięciu |
|:------------- |:-------------|
| `Generic Details` | Pokazuje wartości wierszy w bloku kontekstu siatki właściwości |
| `Cell Details` | Pokazuje wartość komórki w bloku kontekstu siatki właściwości. Przydatne, gdy komórka zawiera typ dynamiczny z informacjami (na przykład JSON z właściwościami żądania, takimi jak lokalizacja, wystąpienie roli itp.). |
| `Cell Details` | Pokazuje wartość komórki w bloku kontekstu siatki właściwości. Przydatne, gdy komórka zawiera typ dynamiczny z informacjami (na przykład JSON z właściwościami żądania, takimi jak lokalizacja, wystąpienie roli itp.). |
| `Custom Event Details` | Otwiera Application Insights szczegóły wyszukiwania przy użyciu niestandardowego identyfikatora zdarzenia (itemId) w komórce |
| `* Details` | Podobnie jak w przypadku szczegółów zdarzenia niestandardowego, z wyjątkiem zależności, wyjątków, wyświetlania stron, żądań i śladów. |
| `Custom Event User Flows` | Otwiera środowisko Przepływy użytkownika Application Insights przestawiane na nazwę zdarzenia niestandardowego w komórce |
| `* User Flows` | Podobne do Przepływy użytkownika zdarzeń niestandardowych z wyjątkiem wyjątków, wyświetleń stron i żądań |
| `User Timeline` | Otwiera oś czasu użytkownika z IDENTYFIKATORem użytkownika (user_Id) w komórce |
| `Session Timeline` | Otwiera środowisko wyszukiwania Application Insights dla wartości w komórce (na przykład Wyszukaj tekst "ABC", gdzie ABC jest wartością w komórce) |
| `Resource overview` | Otwórz przegląd zasobów w portalu na podstawie wartości identyfikatora zasobu w komórce. |

## <a name="conditional-visibility"></a>Widoczność warunkowa
Skoroszyt umożliwia użytkownikom wyświetlanie lub ukrywanie określonych kontrolek na podstawie wartości parametrów. Dzięki temu Autorzy mają różne raporty na podstawie stanu danych wejściowych lub telemetrii użytkownika. Przykład pokazuje, że użytkownicy są tylko podsumowaniem, gdy są dobre, ale wyświetlają pełne szczegóły, gdy coś się nie stało.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Konfigurowanie interaktywności przy użyciu widoczności warunkowej
1. Postępuj zgodnie z instrukcjami `Setting up interactivity on grid row click` w sekcji, aby skonfigurować dwa interaktywne formanty.
2. Dodaj nowy parametr u góry:
    1. Nazwij`ShowDetails`
    2. Typ parametru:`Drop down`
    3. Wymagane:`checked`
    4. Pobierz dane z:`JSON`
    5. Dane wejściowe JSON:`["Yes", "No"]`
    6. Zapisz, aby zatwierdzić zmiany.
3. Ustaw wartość parametru na`Yes`
4. W kontrolce zapytania z wykresem warstwowym kliknij ikonę _Ustawienia zaawansowane_ (ikona koła zębatego)
5. Sprawdź ustawienie`Make this item conditionally visible`
    1. Ten element jest widoczny, `ShowDetails` Jeśli wartość `equals` parametru`Yes`
6. Kliknij pozycję _Zakończono edycję_ , aby zatwierdzić zmiany.
7. Kliknij pozycję _Zakończono edycję_ na pasku narzędzi skoroszytu, aby przejść do trybu odczytu.
8. Zmień wartość parametru `ShowDetails` na `No`. Zauważ, że wykres poniżej zniknie.

Na poniższym obrazie pokazano widoczny przypadek, `ShowDetails` w którym jest`Yes`

![Obraz przedstawiający widoczność warunkową, w której wykres jest widoczny](./media/workbooks-interactive/conditional-visibility.png)

Na poniższej ilustracji przedstawiono ukryty przypadek, w `ShowDetails` którym jest`No`

![Obraz przedstawiający widoczność warunkową, w której wykres jest ukryty](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Następne kroki


* [Rozpocznij](workbooks-visualizations.md) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
