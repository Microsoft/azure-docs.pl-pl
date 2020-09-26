---
title: 'ML Studio (klasyczny): Wyświetl & ponownie uruchom eksperymenty — Azure'
description: Zarządzaj przebiegami eksperymentów w Azure Machine Learning Studio (klasyczny). Możesz sprawdzić poprzednie uruchomienia eksperymentów w dowolnym momencie, aby zakwestionować, odwiedzać i ostatecznie potwierdzić lub uściślić poprzednie założenia.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 374320c0becfecc6691af91b36a1d80b087300e6
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361947"
---
# <a name="manage-experiment-runs-in-azure-machine-learning-studio-classic"></a>Zarządzanie przebiegami eksperymentów w Azure Machine Learning Studio (klasyczny)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Tworzenie modelu analizy predykcyjnej jest procesem iteracyjnym — w miarę modyfikowania różnych funkcji i parametrów eksperymentu wyniki są zbieżne do momentu, gdy masz nauczone, skuteczny model. Kluczem do tego procesu jest śledzenie różnych iteracji parametrów i konfiguracji eksperymentu.

Możesz sprawdzić poprzednie uruchomienia eksperymentów w dowolnym momencie, aby zakwestionować, odwiedzać i ostatecznie potwierdzić lub uściślić poprzednie założenia. Po uruchomieniu eksperymentu Machine Learning Studio (klasyczny) zachowuje historię przebiegu, w tym połączenia zestawów danych, modułów i portów oraz parametry. Ta historia przechwytuje również wyniki, informacje o czasie wykonywania, takie jak czasy uruchamiania i zatrzymywania, komunikaty dziennika i stan wykonania. W dowolnym momencie możesz odszukać dowolne z tych przebiegów, aby zapoznać się z chronologii eksperymentu i wyników pośrednich. Możesz nawet użyć poprzedniego przebiegu eksperymentu, aby uruchomić program w nowej fazie tworzenia zapytań i odnajdywania na ścieżce, aby utworzyć proste, złożone lub nawet kompleksowe rozwiązania modelowania.

> [!NOTE]
> Podczas przeglądania poprzedniego przebiegu eksperymentu ta wersja eksperymentu jest zablokowana i nie można jej edytować. Można jednak zapisać jego kopię, klikając pozycję **Zapisz jako** i podając nową nazwę kopii. Machine Learning Studio (klasyczny) otwiera nową kopię, którą można następnie edytować i uruchamiać. Ta kopia eksperymentu jest dostępna na liście **eksperymentów** wraz ze wszystkimi innymi eksperymentami.
> 
> 

## <a name="view-the-prior-run"></a>Wyświetl poprzednie uruchomienie
Jeśli masz otwarty eksperyment, który jest uruchamiany co najmniej raz, możesz wyświetlić poprzedni przebieg eksperymentu, klikając polecenie **poprzednie uruchomienie** w okienku właściwości.

Załóżmy na przykład, że tworzysz eksperyment i uruchamiasz jego wersje o 11:23, 11:42 i 11:55. W przypadku otwarcia ostatniego uruchomienia eksperymentu (11:55) i kliknięcia polecenia **poprzedni przebiegu**zostanie otwarta wersja uruchomiona o 11:42.

## <a name="view-the-run-history"></a>Wyświetlanie historii uruchamiania
Aby wyświetlić wszystkie poprzednie uruchomienia eksperymentu, kliknij pozycję **Wyświetl historię uruchamiania** w otwartym eksperymentie.

Załóżmy na przykład, że tworzysz eksperyment z modułem [regresja liniowa][linear-regression] i chcesz obserwować wpływ zmiany wartości **kursu szkoleniowego** na wyniki eksperymentu. Eksperyment jest uruchamiany wielokrotnie z różnymi wartościami dla tego parametru w następujący sposób:

| Wartość współczynnika uczenia | Godzina rozpoczęcia przebiegu |
| --- | --- |
| 0,1 |9/11/2014 4:18:58 pm |
| 0,2 |9/11/2014 4:24:33 PM |
| 0,4 |9/11/2014 4:28:36 pm |
| 0,5 |9/11/2014 4:33:31 PM |

Po kliknięciu przycisku **Wyświetl historię uruchamiania**zostanie wyświetlona lista wszystkich przebiegów:

![Przykładowa historia przebiegów](./media/manage-experiment-iterations/viewrunhistory.jpg)

Kliknij dowolny z tych przebiegów, aby wyświetlić migawkę eksperymentu w momencie jego uruchomienia. Konfiguracja, wartości parametrów, komentarze i wyniki są zachowywane w celu uzyskania pełnego rekordu tego uruchomienia eksperymentu.

> [!TIP]
> Aby udokumentować iteracje eksperymentu, można zmodyfikować tytuł przy każdym jego uruchomieniu, można zaktualizować **Podsumowanie** eksperymentu w okienku właściwości, a następnie dodać lub zaktualizować Komentarze do poszczególnych modułów w celu zapisania zmian. Tytuł, podsumowanie i komentarze modułu są zapisywane wraz z każdym przebiegiem eksperymentu.
> 
> 

Lista eksperymentów na karcie **eksperymenty** w Machine Learning Studio (klasyczny) zawsze wyświetla najnowszą wersję eksperymentu. Jeśli otworzysz poprzednie uruchomienie eksperymentu (przy użyciu **wcześniejszej** **historii uruchamiania lub wyświetlania**), możesz powrócić do wersji roboczej, klikając polecenie **Wyświetl historię uruchamiania** i wybierając iterację mającą **stan** **edytowalny**.

## <a name="run-a-previous-experiment"></a>Uruchom poprzedni eksperyment
Po kliknięciu przycisku **poprzedni przebieg** lub **Wyświetl historię uruchamiania** i Otwórz poprzedni przebieg, można wyświetlić gotowy eksperyment w trybie tylko do odczytu.

Jeśli chcesz rozpocząć iterację eksperymentu, rozpoczynając od sposobu skonfigurowania go dla poprzedniego przebiegu, możesz to zrobić, otwierając przebieg i klikając polecenie **Zapisz jako**. Spowoduje to utworzenie nowego eksperymentu z nowym tytułem, pustą historią uruchamiania oraz wszystkimi składnikami i wartościami parametrów poprzedniego przebiegu. Ten nowy eksperyment znajduje się na liście na karcie **eksperymenty** na stronie głównej Machine Learning Studio (klasycznej) i można go modyfikować i uruchamiać, inicjując nową historię uruchamiania dla tej iteracji eksperymentu. 

Załóżmy na przykład, że masz historię uruchamiania eksperymentu przedstawioną w poprzedniej sekcji. Chcesz obserwować, co się dzieje, gdy ustawisz parametr **rate uczenia** na 0,4, i spróbuj użyć różnych wartości dla **liczby parametrów szkolenia** .

1. Kliknij pozycję **Wyświetl historię uruchamiania** i Otwórz iterację eksperymentu, który uruchomiono o godzinie 4:28:36 pm (w którym wartość parametru jest ustawiana na 0,4).
2. Kliknij przycisk **Zapisz jako**.
3. Wprowadź nowy tytuł, a następnie kliknij przycisk **OK** . Zostanie utworzona nowa kopia eksperymentu.
4. Zmodyfikuj **liczbę parametrów szkolenia** .
5. Kliknij przycisk **Uruchom**.

Teraz możesz nadal modyfikować i uruchamiać tę wersję eksperymentu, tworząc nową historię uruchamiania, aby zarejestrować swoją pracę.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
