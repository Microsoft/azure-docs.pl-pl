---
title: Monitorowanie przepływów danych mapowania
description: Jak wizualnie monitorować mapowanie przepływów danych w Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/19/2020
ms.openlocfilehash: 77dda42b27aa6f5fb505fe65667876523cb3f5d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88650897"
---
# <a name="monitor-data-flows"></a>Monitorowanie przepływów danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po ukończeniu kompilowania i debugowania przepływu danych chcesz zaplanować wykonywanie przepływu danych zgodnie z harmonogramem w kontekście potoku. Potoku można zaplanować z Azure Data Factory przy użyciu wyzwalaczy. Można też użyć opcji Wyzwól teraz z konstruktora potoku Azure Data Factory, aby wykonać pojedyncze uruchomienie w celu przetestowania przepływu danych w kontekście potoku.

Podczas wykonywania potoku można monitorować potok i wszystkie działania zawarte w potoku, w tym działanie przepływu danych. Kliknij ikonę monitora w okienku interfejsu użytkownika z lewej strony Azure Data Factory. Zobaczysz ekran podobny do przedstawionego poniżej. Wyróżnione ikony umożliwiają przejście do działań w potoku, w tym działania przepływu danych.

![Monitorowanie przepływu danych](media/data-flow/mon001.png "Monitorowanie przepływu danych")

Na tym poziomie są wyświetlane statystyki, w tym również czasy i Stany uruchomienia. Identyfikator przebiegu na poziomie działania różni się od identyfikatora uruchomienia na poziomie potoku. Identyfikator uruchomienia na poprzednim poziomie dotyczy potoku. Wybranie okularów zapewnia szczegółowe informacje o wykonywaniu przepływu danych.

![Monitorowanie przepływu danych](media/data-flow/monitoring-details.png "Monitorowanie przepływu danych")

Gdy jesteś w widoku monitorowanie węzła graficznego, zobaczysz uproszczoną wersję widoku wykresu przepływu danych.

![Monitorowanie przepływu danych](media/data-flow/mon003.png "Monitorowanie przepływu danych")

Oto przegląd wideo dotyczący monitorowania wydajności przepływów danych z ekranu monitorowania ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Wyświetlanie planów wykonywania przepływu danych

Gdy przepływ danych jest wykonywany w platformie Spark, Azure Data Factory określa optymalne ścieżki kodu na podstawie całości przepływu danych. Ponadto ścieżki wykonywania mogą wystąpić w różnych węzłach skalowalnych w poziomie i partycjach danych. W związku z tym wykres monitorowania reprezentuje projekt przepływu, biorąc pod uwagę ścieżkę wykonywania transformacji. Po wybraniu poszczególnych węzłów można zobaczyć "grupowania", które reprezentują kod, który został wykonany razem w klastrze. Czasy i liczby, które zobaczysz, reprezentują te grupy, w przeciwieństwie do poszczególnych kroków w projekcie.

![Monitorowanie przepływu danych](media/data-flow/mon004.png "Monitorowanie przepływu danych")

* Po wybraniu obszaru Otwórz w oknie monitorowanie, statystyki w dolnym okienku wyświetlają chronometraż i liczby wierszy dla każdego ujścia oraz przekształcenia, które doprowadziły do danych ujścia na potrzeby transformacji.

* Po wybraniu poszczególnych przekształceń otrzymujesz dodatkową opinię na temat panelu po prawej stronie, w którym znajdują się statystyki partycji, liczby kolumn, skośność (jak równomiernie są dystrybuowane dane między partycjami) i kurtoza (jak są to dane).

* Po wybraniu ujścia w widoku węzła można zobaczyć elementy powiązane kolumny. Istnieją trzy różne metody, w których kolumny są gromadzone w całym przepływie danych pod kątem terenu. Są to:

  * Obliczone: używasz kolumny do przetwarzania warunkowego lub wewnątrz wyrażenia w przepływie danych, ale nie wystawić go w ujścia
  * Pochodny: kolumna jest nową kolumną wygenerowaną w przepływie, czyli nieobecną w źródle
  * Zamapowana: kolumna pochodzi ze źródła i mapuje ją do pola ujścia
  * Stan przepływu danych: bieżący stan wykonania
  * Czas uruchamiania klastra: ilość czasu na uzyskanie środowiska obliczeniowego JIT platformy Spark na potrzeby wykonywania przepływu danych
  * Liczba przekształceń: ile kroków transformacji jest wykonywanych w przepływie
  
![Monitorowanie przepływu danych](media/data-flow/monitornew.png "Monitorowanie przepływu danych nowe")

## <a name="total-sink-processing-time-vs-transformation-processing-time"></a>Łączny czas przetwarzania ujścia w porównaniu z czasem przetwarzania transformacji

Każdy etap transformacji obejmuje łączny czas wykonywania tego etapu przy każdym łącznym czasie wykonania partycji. Po kliknięciu ujścia zostanie wyświetlony stan "czas przetwarzania ujścia". Ta godzina obejmuje łączny czas transformacji *oraz* czas operacji we/wy, przez jaki zajęło się zapisanie danych w magazynie docelowym. Różnica między czasem przetwarzania ujścia a łącznym przekształceniem to czas we/wy zapisu danych.

Możesz również zobaczyć szczegółowy chronometraż dla każdego kroku przekształcenia partycji, jeśli otworzysz dane wyjściowe JSON z działania przepływu danych w widoku monitorowanie potoków ADF. Plik JSON zawiera czas milisekund dla każdej partycji, natomiast widok monitorowania środowiska użytkownika jest zagregowanym chronometrażem dodanych partycji:

```
 {
     "stage": 4,
     "partitionTimes": [
          14353,
          14914,
          14246,
          14912,
          ...
         ]
}
```

### <a name="post-processing-time"></a>Czas przetwarzania końcowego

Gdy wybierzesz ikonę przekształcenia ujścia na mapie, panel slajdu po prawej stronie wyświetli dodatkowy punkt danych o nazwie "czas przetwarzania" w dolnej części. Jest to ilość czasu poświęcanego na wykonanie zadania w klastrze Spark *po* załadowaniu i zapisaniu danych. Ten czas może obejmować zamykanie pul połączeń, zamykanie sterowników, usuwanie plików, łączenie plików itp. Gdy wykonujesz akcje w przepływie, takie jak "Przenieś pliki" i "dane wyjściowe do pojedynczego pliku", prawdopodobnie zobaczysz wzrost wartości czasu przetwarzania.
  
## <a name="monitor-icons"></a>Monitoruj ikony

Ta ikona oznacza, że dane przekształcenia zostały już zapisane w pamięci podręcznej, więc czasy i ścieżki wykonywania zostały wzięte pod uwagę:

![Monitorowanie przepływu danych](media/data-flow/mon005.png "Monitorowanie przepływu danych")

W transformację widoczne są również zielone kółka koła. Reprezentują one liczbę zlewów, do których przepływa dane.
