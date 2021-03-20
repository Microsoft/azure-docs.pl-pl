---
title: Konwertowanie na wartości wskaźnika
titleSuffix: Azure Machine Learning
description: Użyj modułu Konwertuj do wartości wskaźnika w programie Azure Machine Learning Designer, aby przekonwertować kolumny kategorii na serię kolumn wskaźników binarnych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 81b3c113f46428327842c1555fdd1934e9ae8762
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420856"
---
# <a name="convert-to-indicator-values"></a>Konwertowanie na wartości wskaźnika
W tym artykule opisano moduł programu Azure Machine Learning Designer.

Za pomocą modułu **Konwertuj do wartości wskaźnika** w programie Azure Machine Learning Designer można konwertować kolumny zawierające wartości kategorii w serię kolumn wskaźników binarnych.  

Ten moduł również wyprowadza definicję przekształcenia użytego do konwersji na wartości wskaźnika. Można ponownie użyć tej transformacji na innych zestawach danych, które mają ten sam schemat, przy użyciu modułu [zastosowania przekształcenia](apply-transformation.md) .

## <a name="how-to-configure-convert-to-indicator-values"></a>Jak skonfigurować konwersję do wartości wskaźnika

1.  Znajdź **wartości wskaźnika Konwertuj na** i przeciągnij je do wersji roboczej potoku. Ten moduł można znaleźć w kategorii **Przekształcanie danych** .
    > [!NOTE]
    > Aby oznaczyć kolumny docelowe jako kategorii, można użyć modułu [Edytuj metadane](edit-metadata.md) przed modułem **Konwertuj do wartości Indiciator** .

1. Połącz moduł **Konwertuj na wartości wskaźnika** z zestawem danych zawierającym kolumny, które chcesz przekonwertować. 

1. Wybierz pozycję **Edytuj kolumnę** , aby wybrać co najmniej jedną kolumnę kategorii.

1. Wybierz opcję **Zastąp kategorii kolumny** , jeśli chcesz wyprowadzać **tylko** nowe kolumny logiczne. Domyślnie ta opcja jest wyłączona.
    

    > [!TIP]
    >  W przypadku wybrania opcji zastępowania kolumna źródłowa nie jest faktycznie usuwana ani modyfikowana. Zamiast tego nowe kolumny są generowane i prezentowane w wyjściowym zestawie danych, a kolumna źródłowa pozostaje dostępna w obszarze roboczym. Jeśli chcesz zobaczyć oryginalne dane, możesz użyć modułu [Dodaj kolumny](add-columns.md) w dowolnym momencie, aby ponownie dodać kolumnę źródłową.

1. Prześlij potok.

## <a name="results"></a>Wyniki

Załóżmy, że masz kolumnę z wynikami wskazującą, czy serwer ma wysokie, średnie lub niskie prawdopodobieństwo wystąpienia błędu.  

| Identyfikator serwera | Ocena niepowodzenia |
| --------- | ------------- |
| 10301     | Niski           |
| 10302     | Śred.        |
| 10303     | Wys.          |

Po zastosowaniu **konwersji do wartości wskaźnika** Projektant konwertuje pojedynczą kolumnę etykiet w wiele kolumn zawierających wartości logiczne:  

| Identyfikator serwera | Wynik niepowodzenia — niski | Wynik niepowodzenia — średni | Wynik niepowodzenia — wysoki |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Oto jak działa konwersja:  

-   W kolumnie **oceny niepowodzenia** opisującej ryzyko występuje tylko trzy możliwe wartości (wysoki, średni i niski) i brak wartości. W związku z tym tworzone są dokładnie trzy nowe kolumny.  

-   Nowe kolumny wskaźnika są nazwane na podstawie nagłówków kolumn i wartości kolumny źródłowej, przy użyciu tego wzorca: *\<source column>- \<data value>* .  

-   Powinna istnieć 1 w dokładnie jednej kolumnie wskaźnika i 0 we wszystkich innych kolumnach wskaźnika, ponieważ każdy serwer może mieć tylko jedną ocenę ryzyka.  

Teraz można użyć trzech kolumn wskaźnika jako funkcji w modelu uczenia maszynowego.

Moduł zwraca dwa dane wyjściowe:

- **Zestaw danych wyników**: zestaw danych z przekonwertowane kolumny wartości wskaźnika. Kolumny niewybrane do czyszczenia są również "przenoszone przez".
- **Przekształcanie wartości wskaźnika**: transformacja danych służąca do konwertowania na wartości wskaźnika, którą można zapisać w obszarze roboczym i zastosowana do nowych danych później.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Zastosuj operację zapisania wartości wskaźnika do nowych danych

Jeśli trzeba często powtarzać wartości wskaźników, możesz zapisać kroki manipulowania danymi jako *przekształcenie* , aby ponownie użyć tego samego zestawu danych. Jest to przydatne, jeśli trzeba często ponownie importować i czyścić dane, które mają ten sam schemat.

1. Dodaj moduł [przekształcenia Zastosuj](apply-transformation.md) do potoku.

1. Dodaj zestaw danych, który chcesz wyczyścić, i Połącz zestaw danych z portem wejściowym z prawej strony.

1. Rozwiń grupę **Przekształcanie danych** w okienku po lewej stronie projektanta. Znajdź zapisaną transformację i przeciągnij ją do potoku.

1. Połącz zapisaną transformację z lewym portem wejściowym [zastosowania transformacji](apply-transformation.md).

   W przypadku zastosowania zapisanej transformacji nie można wybrać kolumn do przekształcenia. Wynika to z faktu, że transformacja została zdefiniowana i jest automatycznie stosowana do typów danych określonych w pierwotnej operacji.

1. Prześlij potok.
 
## <a name="technical-notes"></a>Uwagi techniczne  

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

### <a name="usage-tips"></a>Porady dotyczące użycia

-   Tylko kolumny oznaczone jako kategorii mogą być konwertowane na kolumny wskaźników. Jeśli zobaczysz następujący błąd, prawdopodobnie jedna z wybranych kolumn nie jest kategorii:  

     Błąd 0056: kolumna o nazwie  \<column name> nie jest w dozwolonej kategorii.  

     Domyślnie większość kolumn ciągów jest obsługiwana jako funkcje ciągów, dlatego należy jawnie oznaczyć je jako kategorii przy użyciu polecenia [Edytuj metadane](edit-metadata.md).  

-   Nie ma żadnego limitu liczby kolumn, które można przekonwertować na kolumny wskaźników. Jednak ponieważ każda kolumna wartości może zwracać wiele kolumn wskaźników, można skonwertować i przejrzeć tylko kilka kolumn naraz.  

-   Jeśli kolumna zawiera brakujące wartości, dla brakującej kategorii zostanie utworzona oddzielna kolumna wskaźnika o tej nazwie: *\<source column> Brak*  

-   Jeśli kolumna konwertowana na wartości wskaźników zawiera liczby, muszą one być oznaczone jako kategorii, jak każda inna kolumna funkcji. Po wykonaniu tej czynności liczby są traktowane jako odrębne wartości. Na przykład jeśli masz kolumnę liczbową z wartościami MPG z zakresu od 25 do 30, zostanie utworzona nowa kolumna wskaźnika dla każdej wartości dyskretnej:  

    | Marka       | Autostrada MPG-25 | Autostrada MPG-26 | Autostrada MPG-27 | Autostrada MPG-28 | Autostrada MPG-29 | Autostrada MPG-30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Samochody firmy Contoso | 0               | 0               | 0               | 0               | 0               | 1               |

- Aby uniknąć dodawania zbyt wielu wymiarów do zestawu danych. Zalecamy, aby najpierw sprawdzić liczbę wartości w kolumnie oraz odpowiednio bin lub Quantize dane.  


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
