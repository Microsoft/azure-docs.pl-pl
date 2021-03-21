---
title: Pola niestandardowe w Azure Monitor (wersja zapoznawcza) | Microsoft Docs
description: Funkcja pól niestandardowych Azure Monitor umożliwia tworzenie własnych pól z możliwością wyszukiwania z rekordów w Log Analytics obszarze roboczym, które są dodawane do właściwości zebranego rekordu.  W tym artykule opisano proces tworzenia pola niestandardowego i zawiera szczegółowy przewodnik z przykładowym wydarzeniem.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: e7ddeaf1273a9e3b5608336b24c07fad3c3f4525
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031042"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Tworzenie pól niestandardowych w obszarze roboczym Log Analytics w Azure Monitor (wersja zapoznawcza)

> [!NOTE]
> W tym artykule opisano sposób analizowania danych tekstowych w obszarze roboczym Log Analytics w miarę ich zbierania. Zalecamy analizowanie danych tekstowych w filtrze zapytania po zebraniu zgodnie ze wskazówkami opisanymi w temacie [Analizowanie danych tekstowych w Azure monitor](./parse-text.md). Zapewnia kilka korzyści w porównaniu z użyciem pól niestandardowych.

> [!IMPORTANT]
> Pola niestandardowe zwiększają ilość danych zebranych w obszarze roboczym Log Analytics, co może zwiększyć koszt. Aby uzyskać szczegółowe informacje [, zobacz Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor](./manage-cost-storage.md#pricing-model) .

Funkcja **pól niestandardowych** Azure monitor pozwala na rozbudowanie istniejących rekordów w obszarze roboczym log Analytics przez dodanie własnych pól do przeszukiwania.  Pola niestandardowe są automatycznie wypełniane na podstawie danych wyodrębnionych z innych właściwości w tym samym rekordzie.

![Na diagramie przedstawiono oryginalny rekord skojarzony ze zmodyfikowanym rekordem w obszarze roboczym Log Analytics z parami wartości właściwości dodanymi do oryginalnej właściwości w zmodyfikowanym rekordzie.](media/custom-fields/overview.png)

Na przykład Przykładowy rekord poniżej ma użyteczne dane, które zostały ukryte w opisie zdarzenia. Wyodrębnienie tych danych do oddzielnej właściwości sprawia, że są one dostępne dla takich akcji jak sortowanie i filtrowanie.

![Wyodrębnianie próbek](media/custom-fields/sample-extract.png)

> [!NOTE]
> W wersji zapoznawczej masz ograniczone do 100 pól niestandardowych w obszarze roboczym.  Ten limit zostanie rozszerzony, gdy ta funkcja osiągnie dostępność ogólnie.

## <a name="creating-a-custom-field"></a>Tworzenie pola niestandardowego
Podczas tworzenia pola niestandardowego, Log Analytics musi zrozumieć, które dane mają być używane do wypełniania wartości.  Używa technologii firmy Microsoft Research o nazwie FlashExtract, aby szybko identyfikować te dane.  Zamiast wymagać podania jawnych instrukcji, Azure Monitor Poznaj informacje o danych, które mają zostać wyodrębnione z dostarczonych przykładów.

Poniższe sekcje zawierają procedurę tworzenia pola niestandardowego.  W dolnej części tego artykułu znajduje się przewodnik po wyodrębnianiu próbki.

> [!NOTE]
> Pole niestandardowe jest wypełniane, ponieważ rekordy spełniające określone kryteria są dodawane do obszaru roboczego Log Analytics, więc będą wyświetlane tylko w przypadku rekordów zebranych po utworzeniu pola niestandardowego.  Pole niestandardowe nie zostanie dodane do rekordów, które znajdują się już w magazynie danych, gdy zostanie utworzony.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Krok 1 — identyfikowanie rekordów, które będą miały pole niestandardowe
Pierwszym krokiem jest zidentyfikowanie rekordów, które będą uzyskać pole niestandardowe.  Zaczynasz od [standardowego zapytania dziennika](./log-query-overview.md) , a następnie wybierz rekord, który będzie działać jako model, który Azure monitor się uczyć.  Po określeniu, że dane mają zostać wyodrębnione do pola niestandardowego, zostanie otwarty **Kreator wyodrębniania pól** , w którym można sprawdzić poprawność i uściślić kryteria.

1. Przejdź do **dzienników** i Użyj [zapytania, aby pobrać rekordy](./log-query-overview.md) , które będą miały pole niestandardowe.
2. Wybierz rekord, który Log Analytics będzie używany do działania jako model wyodrębniania danych w celu wypełnienia pola niestandardowego.  Użytkownik zidentyfikuje dane, które mają zostać wyodrębnione z tego rekordu, a Log Analytics będzie używać tych informacji do określenia logiki w celu wypełnienia pola niestandardowego dla wszystkich podobnych rekordów.
3. Rozwiń Właściwości rekordu, kliknij wielokropek z lewej strony górnej części rekordu, a następnie wybierz pozycję **Wyodrębnij pola z**.
4. Zostanie otwarty **Kreator wyodrębniania pól** , a wybrany rekord zostanie wyświetlony w **głównej przykładowej** kolumnie.  Pole niestandardowe zostanie zdefiniowane dla tych rekordów z tymi samymi wartościami we właściwościach, które są wybrane.  
5. Jeśli zaznaczenie nie ma dokładnie tego, czego chcesz, wybierz dodatkowe pola, aby zawęzić kryteria.  Aby zmienić wartości pól dla kryteriów, należy anulować i wybrać inny rekord pasujący do żądanych kryteriów.

### <a name="step-2---perform-initial-extract"></a>Krok 2. wykonanie początkowego wyodrębnienia.
Po zidentyfikowaniu rekordów, które będą miały pole niestandardowe, należy zidentyfikować dane, które mają zostać wyodrębnione.  Log Analytics będzie używać tych informacji do identyfikowania podobnych wzorców w podobnych rekordach.  W kroku po tym będzie można sprawdzić poprawność wyników i podać dalsze szczegóły dotyczące Log Analytics do użycia w analizie.

1. Zaznacz tekst w przykładowym rekordzie, który ma zostać wypełniony pola niestandardowego.  Następnie zostanie wyświetlone okno dialogowe umożliwiające podanie nazwy i typu danych dla pola oraz wykonanie początkowego wyodrębnienia.  Znaki **\_ CF** zostaną automatycznie dołączone.
2. Kliknij pozycję **Wyodrębnij** , aby przeprowadzić analizę zebranych rekordów.  
3. W sekcjach **Podsumowanie** i **wyniki wyszukiwania** są wyświetlane wyniki wyodrębniania, aby można było sprawdzić jego dokładność.  W obszarze **Podsumowanie** są wyświetlane kryteria służące do identyfikowania rekordów i liczby dla każdej z zidentyfikowanych wartości danych.  **Wyniki wyszukiwania** zawierają szczegółową listę rekordów spełniających kryteria.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Krok 3 — Weryfikowanie dokładności wyodrębniania i Tworzenie pola niestandardowego
Po wykonaniu początkowego wyodrębnienia Log Analytics wyświetli wyniki na podstawie danych, które zostały już zebrane.  Jeśli wyniki wyglądają prawidłowo, możesz utworzyć pole niestandardowe bez dalszej pracy.  Jeśli nie, możesz udoskonalić wyniki, aby Log Analytics mógł ulepszyć logikę.

1. Jeśli jakiekolwiek wartości w początkowym ekstrakcie nie są poprawne, kliknij ikonę **Edytuj** obok niedokładnego rekordu i wybierz opcję **Modyfikuj to wyróżnienie** , aby zmodyfikować zaznaczenie.
2. Wpis jest kopiowany do sekcji **dodatkowe przykłady** poniżej **głównego przykładu**.  Możesz dostosować tutaj wyróżnienie, aby pomóc Log Analytics zrozumieć wybór, który powinien zostać wykonany.
3. Kliknij pozycję **Wyodrębnij** , aby użyć tych nowych informacji do oszacowania wszystkich istniejących rekordów.  Wyniki mogą być modyfikowane w przypadku rekordów innych niż te, które zostały zmodyfikowane na podstawie tej nowej analizy.
4. Kontynuuj dodawanie poprawek do momentu, aż wszystkie rekordy w ekstrakcie poprawnie zidentyfikują dane w celu wypełnienia nowego pola niestandardowego.
5. Kliknij przycisk **Zapisz wyodrębnienie** , gdy masz zadowalające wyniki.  Pole niestandardowe jest teraz zdefiniowane, ale nie zostanie jeszcze dodane do żadnych rekordów.
6. Poczekaj na zebranie nowych rekordów spełniających określone kryteria, a następnie ponownie uruchom przeszukiwanie dzienników. Nowe rekordy powinny mieć pole niestandardowe.
7. Użyj pola niestandardowego, jak każda inna Właściwość rekordu.  Można jej użyć do agregowania i grupowania danych, a nawet do tworzenia nowych szczegółowych informacji.

## <a name="viewing-custom-fields"></a>Wyświetlanie pól niestandardowych
Możesz wyświetlić listę wszystkich pól niestandardowych w grupie zarządzania z menu **Ustawienia zaawansowane** w obszarze roboczym Log Analytics w Azure Portal.  Wybierz pozycję **dane** , a następnie **pola niestandardowe** , aby wyświetlić listę wszystkich pól niestandardowych w obszarze roboczym.  

![Niestandardowe pola](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Usuwanie pola niestandardowego
Istnieją dwa sposoby usunięcia pola niestandardowego.  Pierwsza to opcja **usuwania** dla każdego pola podczas wyświetlania kompletnej listy, jak opisano powyżej.  Druga metoda polega na pobraniu rekordu i kliknięciu przycisku z lewej strony pola.  Menu będzie zawierać opcję usunięcia pola niestandardowego.

## <a name="sample-walkthrough"></a>Przewodnik po przykładzie
W poniższej sekcji przedstawiono pełny przykład tworzenia pola niestandardowego.  Ten przykład wyodrębnia nazwę usługi w zdarzeniach systemu Windows, która wskazuje na zmianę stanu usługi.  Jest to zależne od zdarzeń utworzonych przez menedżera kontroli usług podczas uruchamiania systemu na komputerach z systemem Windows.  Jeśli chcesz postępować zgodnie z tym przykładem, musisz [zbierać informacje o zdarzeniach w dzienniku systemu](../agents/data-sources-windows-events.md).

Wprowadź następujące zapytanie, aby zwrócić wszystkie zdarzenia z Menedżera sterowania usługami, które mają identyfikator zdarzenia 7036, czyli zdarzenie wskazujące, że usługa jest uruchamiana lub zatrzymywana.

![Zrzut ekranu przedstawia zapytanie dotyczące źródła i identyfikatora zdarzenia.](media/custom-fields/query.png)

Następnie wybieramy i rozszerzamy każdy rekord z IDENTYFIKATORem zdarzenia 7036.

![Rekord źródłowy](media/custom-fields/source-record.png)

Definiujemy pola niestandardowe, klikając wielokropek obok właściwości Top.

![Wyodrębnij pola](media/custom-fields/extract-fields.png)

Zostanie otwarty **Kreator wyodrębniania pól** , a pola **EventLog** i **EventID** są zaznaczone w **głównej przykładowej** kolumnie.  Oznacza to, że pole niestandardowe zostanie zdefiniowane dla zdarzeń z dziennika systemu z IDENTYFIKATORem zdarzenia 7036.  Jest to wystarczające, aby nie trzeba było wybierać żadnych innych pól.

![Główny przykład](media/custom-fields/main-example.png)

Wyróżnimy nazwę usługi we właściwości **RenderedDescription** i Użyj **usługi** , aby zidentyfikować nazwę usługi.  Pole niestandardowe zostanie wywołane **Service_CF**. Typ pola w tym przypadku jest ciągiem, dlatego można pozostawić niezmieniony.

![Tytuł pola](media/custom-fields/field-title.png)

Zobaczymy, że nazwa usługi jest prawidłowo identyfikowana dla niektórych rekordów, ale nie dla innych.   **Wyniki wyszukiwania** pokazują, że nie wybrano części nazwy dla **karty wydajności WMI** .  **Podsumowanie** pokazuje, że jeden rekord identyfikuje **Instalatora modułów** zamiast **Instalatora modułów systemu Windows**.  

![Zrzut ekranu przedstawiający fragmenty nazwy usługi wyróżnionej w okienku wyników wyszukiwania i jedną niepoprawną nazwę usługi wyróżnioną w podsumowaniu.](media/custom-fields/search-results-01.png)

Zaczynamy od rekordu **karty wydajności usługi WMI** .  Kliknij ikonę edycji, a następnie **zmodyfikuj to wyróżnienie**.  

![Modyfikuj wyróżnienie](media/custom-fields/modify-highlight.png)

Zwiększamy wyróżnienie, aby uwzględnić słowo **WMI** , a następnie ponownie uruchomić wyodrębnianie.  

![Dodatkowy przykład](media/custom-fields/additional-example-01.png)

Widzimy, że wpisy dla **karty wydajności WMI** zostały poprawione, a log Analytics również używały tych informacji w celu poprawienia rekordów dla **Instalatora modułu systemu Windows**.

![Zrzut ekranu przedstawiający pełną nazwę usługi wyróżnioną w okienku wyników wyszukiwania oraz poprawne nazwy usług wyróżnione w podsumowaniu.](media/custom-fields/search-results-02.png)

Teraz można uruchomić zapytanie, które weryfikuje **Service_CF** jest tworzone, ale nie zostało jeszcze dodane do żadnych rekordów. Wynika to z faktu, że pole niestandardowe nie działa z istniejącymi rekordami, więc musimy czekać, aż zostaną zebrane nowe rekordy.

![Liczba początkowa](media/custom-fields/initial-count.png)

Po upływie pewnego czasu zostaną zebrane nowe zdarzenia, zobaczymy, że pole **Service_CF** jest teraz dodawane do rekordów, które pasują do naszych kryteriów.

![Wyniki końcowe](media/custom-fields/final-results.png)

Teraz możemy używać pola niestandardowego, takiego jak jakakolwiek inna Właściwość rekordu.  Aby to zilustrować, tworzymy zapytanie, które grupuje według nowego pola **Service_CF** , aby sprawdzić, które usługi są najbardziej aktywne.

![Grupuj według zapytania](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [zapytań dzienników](./log-query-overview.md) do kompilowania zapytań przy użyciu pól niestandardowych dla kryteriów.
* Monitoruj [niestandardowe pliki dziennika](../agents/data-sources-custom-logs.md) , które można analizować za pomocą pól niestandardowych.