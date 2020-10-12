---
title: Prezentowanie notesu Jupyter jako pokazu slajdów w wersji zapoznawczej Azure Notebooks
description: Dowiedz się, jak skonfigurować komórki dla trybu pokazu slajdów w notesie Jupyter, a następnie przedstawić pokaz slajdów przy użyciu rozszerzenia wzrostu.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 2fe337361436ecfc8eabf2855ad633b891db69d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85834050"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Uruchamianie pokazu slajdów notesu w podglądzie Azure Notebooks

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks jest wstępnie skonfigurowany przy użyciu rozszerzenia pokazu slajdów Jupyter/IPython (wzrost), które umożliwia prezentowanie notesu bezpośrednio jako pokazu slajdów. W pokazie slajdów komórki są zwykle wyświetlane pojedynczo przy użyciu rozmiaru czcionki, który jest odpowiedni do przedprezentowania na dużych ekranach i nadal można uruchomić kod zamiast przełączać się do oddzielnego komputera demonstracyjnego.

Na poniższej ilustracji przedstawiono standardowy widok notesu, w którym można zobaczyć wszystkie rozłożenia i komórki kodu:

![Notes w widoku standardowym](media/slideshow/slideshow-notebook-view.png)

Po rozpoczęciu pokazu slajdów pierwsza komórka zostanie powiększona, aby wypełnić przeglądarkę, gdzie **X** w górnym rogu opuszcza pokaz slajdów **?** w lewym dolnym rogu są wyświetlane skróty klawiaturowe, a strzałki w prawej dolnej części Przechodź między slajdami:

![Notes w trybie pokazu slajdów](media/slideshow/slideshow-slide-view.png)

Przygotowywanie notesu dla pokazu slajdów obejmuje dwie podstawowe działania:

1. Ponieważ komórki o promocji są renderowane z dużymi czcionkami, część zawartości może nie być widoczna w pokazie slajdów. W ten sposób zwykle ograniczasz ilość tekstu w danej komórce; Nagłówek z czterema do sześciu wierszy zazwyczaj działa najlepiej. Jeśli masz więcej tekstu, Podziel te informacje na wiele komórek.

2. Skonfiguruj zachowanie każdej komórki w pokazie slajdów, korzystając z paska narzędzi komórka pokazu slajdów. Typy komórek określają zachowanie przycisków nawigacji.

## <a name="the-anatomy-of-a-slideshow"></a>Anatomię pokazu slajdów

Jeśli zajmiesz się losowym notesem i użyjesz go dla pokazu slajdów, zazwyczaj znajdziesz, że wszystkie komórki są Jumbled razem, a część zawartości jest ukryta w dolnej części okna przeglądarki. Aby zastosować efektywną prezentację, należy przypisać do każdej komórki typ pokazu slajdów:

1. W menu **Widok** wybierz pozycję pokaz **slajdów paska narzędzi komórki**  >  **Slideshow**:

    ![Włączanie paska narzędzi pokazu slajdów](media/slideshow/slideshow-view-cell-toolbar.png)

1. Lista rozwijana **Typ slajdu** pojawia się w prawym górnym rogu każdej komórki w notesie:

    ![Pasek narzędzi pokazu slajdów komórek](media/slideshow/slideshow-cell-toolbar.png)

1. Dla każdej komórki wybierz jeden z pięciu typów:

    ![Typy pokazów komórek](media/slideshow/slideshow-cell-slide-types.png)

    | Typ slajdu | Zachowanie |
    | --- | --- |
    | -(nie ustawiono) | Zostanie wyświetlona komórka z poprzednią komórką, która często nie jest pożądanym efektem pokazu slajdów. |
    | Odtwarzanie | Komórka jest głównym slajdem, za pomocą strzałki w lewo i w prawo kontrolki nawigacji. |
    | Slajd podrzędny | Komórka jest "poniżej" głównego slajdu, przechodzenie do użycia strzałki w dół kontrolki nawigacji. Strzałka w górę wraca do podstawowego slajdu. Slajdy podrzędne są używane dla dodatkowego materiału, który można pominąć w ścieżce głównej prezentacji, ale jest on dostępny w razie potrzeby. |
    | Fragment | Zawartość komórki pojawia się w kontekście poprzedniego slajdu lub slajdu podrzędnego przy użyciu strzałki nawigacji w dół (fragment jest usuwany przy użyciu strzałki w górę). Możesz użyć fragmentu z komórką kodu, aby ten kod pojawił się w obrębie slajdu lub można użyć wielu fragmentów, aby punktory tekstu pojawiły się po jednym (Zobacz przykład w następnej sekcji). Ponieważ fragmenty są kompilowane na bieżącym slajdzie, nadmiarowe fragmenty nie będą widoczne w dolnej części okna przeglądarki. |
    | Pomiń | Komórka nie jest wyświetlana w pokazie slajdów. |
    | Uwagi | Komórka zawiera notatki prelegenta, które nie są wyświetlane w pokazie slajdów. |

1. Początkowo warto wybrać opcję **slajdu** dla każdej komórki. Następnie możesz uruchomić pokaz slajdów i wprowadzić odpowiednie korekty.

### <a name="example-fragment-cells-for-bullet-items"></a>Przykład: komórki fragmentu dla elementów punktorów

Aby punktory dla slajdu pojawiły się po jednym, umieść nagłówek slajdu w komórce o promocji z typem **slajdu** , a następnie umieść każdy punktor w oddzielnej komórce o promocji z typem **fragmentu** :

![Przykład tworzenia wielu komórek z promocji dla elementów punktorów](media/slideshow/slideshow-fragments.png)

Ponieważ pokaz slajdów renderuje fragmenty o większej liczbie pionowych odstępów niż w przypadku, gdy wszystkie punktory znajdują się w tej samej komórce, może nie być możliwe użycie tylu elementów punktorów.

## <a name="run-the-slideshow"></a>Uruchamianie pokazu slajdów

1. Jeśli edytujesz wszystkie komórki z promocji, upewnij się, że są one uruchamiane w celu renderowania kodu HTML. w przeciwnym razie pojawiają się one w *postaci* promocji w pokazie slajdów.

1. Po skonfigurowaniu **typu slajdu** dla każdej komórki wybierz komórkę, w której ma zostać uruchomiony pokaz slajdów, a następnie wybierz przycisk **pokazu naciśnij i wyjdź** na głównym pasku narzędzi:

    ![Przycisk Przejdź do pokazu slajdów na głównym pasku narzędzi](media/slideshow/slideshow-start.png)

1. Aby poruszać się między slajdami oraz fragmentami, użyj strzałek w lewo i w prawo w kontrolce nawigacji. Tekst w kontrolce pokazuje liczbę reprezentującą *slajd. Sub-slajdu*.

    ![Kontrolka nawigacji pokazu slajdów](media/slideshow/slideshow-navigation-control.png)

1. Aby poruszać się między slajdami i slajdami podrzędnymi, a także fragmentami, użyj strzałek w górę i w dół, jeśli są włączone:

    ![Kontrolki nawigacji pokazu slajdów dla slajdów podrzędnych](media/slideshow/slideshow-navigation-control-subslide.png)

1. W komórce kodu Użyj przycisku Odtwórz, aby uruchomić kod; dane wyjściowe pojawiają się na slajdzie:

    ![Przycisk odtwarzania, aby uruchomić komórkę kodu](media/slideshow/slideshow-run-code-cell.png)

    ![Dane wyjściowe komórki kodu pojawiają się w pokazie slajdów](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Dane wyjściowe komórki są uważane za część komórki w pokazie slajdów. Jeśli zostanie uruchomiona komórka w widoku notesu lub pokazu slajdów, dane wyjściowe są również wyświetlane w innym widoku. Aby wyczyścić dane wyjściowe, **Cell**Użyj  >  **bieżącego polecenia wyprowadzania**danych wyjściowych komórki  >  **Clear** (dla bieżącej komórki) lub **komórki**  >  **Wyczyść wszystkie dane wyjściowe**  >  **Clear** (dla wszystkich komórek).

1. Gdy skończysz korzystać z pokazu slajdów, użyj **X** , aby powrócić do widoku notesu.

## <a name="next-steps"></a>Następne kroki

- [Instrukcje: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Instrukcje: Instalowanie pakietów z poziomu notesu](install-packages-jupyter-notebook.md)
- [Instrukcje: korzystanie z plików danych](work-with-project-data-files.md)
- [Instrukcje: dostęp do zasobów danych](access-data-resources-jupyter-notebooks.md)
