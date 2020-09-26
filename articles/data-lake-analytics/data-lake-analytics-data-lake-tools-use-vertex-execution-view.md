---
title: Widok wykonywania wierzchołka w narzędziach Data Lake Tools for Visual Studio
description: W tym artykule opisano sposób użycia widoku wykonywania wierzchołka do przetworzenia Data Lake Analytics zadań.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: how-to
ms.date: 10/13/2016
ms.openlocfilehash: b8688af24e2b67f0e21de8344188b9a946f3258b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331951"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Korzystanie z widoku wykonywania wierzchołków w narzędziach Data Lake Tools for Visual Studio
Dowiedz się, jak za pomocą widoku wykonywania wierzchołków przeegzaminować Data Lake Analytics zadania.


## <a name="open-the-vertex-execution-view"></a>Otwórz widok wykonywania wierzchołka
Otwórz zadanie U-SQL w Data Lake narzędzia dla programu Visual Studio. Kliknij **Widok wykonywania wierzchołka** w lewym dolnym rogu. Może zostać wyświetlony monit o załadowanie profilów w pierwszej kolejności i może upłynąć trochę czasu w zależności od łączności sieciowej.

![Zrzut ekranu pokazujący widok wykonywania wierzchołka narzędzi Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Opis widoku wykonywania wierzchołków
Widok wykonywania wierzchołka ma trzy części:

![Zrzut ekranu pokazujący widok wykonywania wierzchołka z wyróżnionymi okienkami "selektor wierzchołków" i Wyśrodkuj i Wyśrodkuj w dół.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

**Selektor wierzchołka** po lewej stronie umożliwia wybranie wierzchołków według funkcji (takich jak 10 pierwszych danych odczytywanych lub wybór według etapów). Jednym z najczęściej używanych filtrów jest wyświetlenie **wierzchołków ścieżki krytycznej**. **Ścieżka krytyczna** to najdłuższy łańcuch wierzchołków zadania U-SQL. Zrozumienie ścieżki krytycznej jest przydatne w przypadku optymalizowania zadań przez sprawdzenie, który wierzchołek zajmuje najdłuższy czas.
  
![Zrzut ekranu pokazujący Górne okienko widoku wykonywania wierzchołka, które wyświetla "stan działania wszystkich wierzchołków".](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Górne okienko przedstawia **stan uruchomienia wszystkich wierzchołków**.
  
![Zrzut ekranu pokazujący dolne okienko widoku wykonywania wierzchołka, które wyświetla informacje o każdym wierzchołku.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

W środkowym okienku wyświetlane są informacje o każdym wierzchołku:
* Nazwa procesu: nazwa wystąpienia wierzchołka. Składa się z różnych części w fazie Stage | Wierzchołekname | VertexRunInstance. Na przykład, SV7_Split [62]. v1 to drugie uruchomione wystąpienie (. v1, zaczynające się od 0) o numerze wierzchołka 62 na etapie SV7_Split.
* Łączne dane odczytu/zapisu: dane zostały odczytane/wpisane przez ten wierzchołek.
* Stan stanu/zakończenia: końcowy stan po zakończeniu wierzchołka.
* Kod zakończenia/typ błędu: błąd, gdy wierzchołek nie powiódł się.
* Przyczyna utworzenia: Dlaczego wierzchołek został utworzony.
* Opóźnienie zasobów/opóźnienie procesu/opóźnienie kolejki: czas oczekiwania wierzchołka na zasoby, przetwarzanie danych i pozostawanie w kolejce.
* Identyfikator GUID procesu/Twórcy: identyfikator GUID bieżącego działającego wierzchołka lub jego twórcy.
* Wersja: N-ty wystąpienie działającego wierzchołka (system może zaplanować nowe wystąpienia wierzchołka z wielu powodów, na przykład tryb failover, nadmiarowość obliczeń itp.)
* Godzina utworzenia wersji.
* Czas rozpoczęcia procesu tworzenia/przetwarzania w kolejce/czas rozpoczęcia procesu/czas zakończenia procesu: po rozpoczęciu tworzenia procesu wierzchołka; gdy proces wierzchołka zacznie kolejkować; gdy zostanie uruchomiony określony proces wierzchołka; Po zakończeniu określonego wierzchołka.

## <a name="next-steps"></a>Następne kroki
* Aby rejestrować informacje diagnostyczne, zobacz [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics)
* Aby uzyskać informacje na temat bardziej złożonego zapytania, zobacz temat [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby wyświetlić szczegóły zadania, zobacz [Korzystanie z przeglądarki zadań i widoku zadań dla zadań usługi Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
