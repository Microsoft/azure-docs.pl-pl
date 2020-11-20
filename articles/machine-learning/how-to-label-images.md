---
title: Obrazy tagów w projekcie etykietowania
title.suffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą narzędzi do tagowania danych szybko przygotować dane dla Machine Learning w Azure Machine Learning projekcie etykietowania.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: b138b3eb52a0bfb6dca2ec6fdf0b855223bab698
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984451"
---
# <a name="tag-images-in-a-labeling-project"></a>Obrazy tagów w projekcie etykietowania 

Gdy administrator projektu [tworzy projekt etykietowania](./how-to-create-labeling-projects.md#create-a-labeling-project) w Azure Machine Learning, można użyć narzędzia do etykietowania, aby szybko przygotować dane dla projektu Machine Learning. W tym artykule opisano:

> [!div class="checklist"]
> * Jak uzyskać dostęp do projektów etykietowania
> * Narzędzia do etykietowania
> * Jak używać narzędzi do określonych zadań etykietowania

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto Microsoft](https://account.microsoft.com/account) lub konto Azure Active Directory dla organizacji i projektu
* Dostęp na poziomie współautor do obszaru roboczego, który zawiera projekt etykietowania.

## <a name="sign-in-to-the-workspace"></a>Logowanie do obszaru roboczego

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. Wybierz subskrypcję i obszar roboczy, który zawiera projekt etykietowania.  Pobierz te informacje z administratora projektu.

1. Wybierz pozycję **dane z etykietami** po lewej stronie, aby znaleźć projekt.  

## <a name="understand-the-labeling-task"></a>Zrozumienie zadania etykietowania

W tabeli z etykietami danych wybierz **łącze etykieta** dla projektu.

Zobaczysz instrukcje, które są specyficzne dla Twojego projektu. Wyjaśniają one typ danych, na które się znajdują, jak należy podjąć decyzje i inne istotne informacje. Po odczytaniu tych informacji w górnej części strony wybierz pozycję **zadania**.  Lub w dolnej części strony wybierz pozycję **Rozpocznij etykietowanie**.

## <a name="common-features-of-the-labeling-task"></a>Typowe funkcje zadania etykietowania

We wszystkich zadaniach etykietowania obrazów należy wybrać odpowiedni tag lub Tagi z zestawu, który jest określony przez administratora projektu. Można wybrać pierwsze dziewięć tagów przy użyciu klawiszy liczbowych na klawiaturze.  

W zadaniach klasyfikacji obrazów można wybrać opcję wyświetlania wielu obrazów jednocześnie. Użyj ikon powyżej obszaru obrazu, aby wybrać układ. 

Aby jednocześnie zaznaczyć wszystkie wyświetlane obrazy, użyj **opcji Zaznacz wszystko**. Aby zaznaczyć poszczególne obrazy, użyj przycisku zaznaczania cyklicznego w prawym górnym rogu obrazu. Musisz wybrać co najmniej jeden obraz, aby zastosować tag. W przypadku wybrania wielu obrazów każdy wybrany tag zostanie zastosowany do wszystkich zaznaczonych obrazów.

W tym miejscu wybieramy układ dwóch przez dwa i zamierzamy zastosować tag "ssak" do obrazów obrazu i programu Orca. Obraz rekina został już oznaczony jako "Cartilaginous ryba", a Iguana nie został jeszcze oznaczony.

![Wiele układów i opcji obrazu](./media/how-to-label-images/layouts.png)

> [!Important] 
> Przełączaj układy tylko wtedy, gdy masz nową stronę bez etykietowania danych. Przełączenie układu czyści działanie tagowania strony w toku.

System Azure włącza przycisk **Prześlij** , gdy Otagowano wszystkie obrazy na stronie. Wybierz pozycję **Prześlij** , aby zapisać swoją służbę.

Po przesłaniu tagów dla danych, platforma Azure Odświeża stronę nowym zestawem obrazów z kolejki roboczej.

### <a name="assisted-machine-learning-preview"></a>Asystowana Uczenie maszynowe (wersja zapoznawcza) 

> [!IMPORTANT]
> Pomoc dotycząca uczenia maszynowego jest obecnie dostępna w publicznej wersji zapoznawczej.
> Wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mogą zostać wyzwolone algorytmy uczenia maszynowego. Jeśli te algorytmy są włączone w projekcie, mogą pojawić się następujące elementy:

* Po oznaczeniu pewnej ilości obrazów można zobaczyć **zadania klastrowane** w górnej części ekranu obok nazwy projektu.  Oznacza to, że obrazy są pogrupowane w taki sposób, aby przedstawić podobne obrazy na tej samej stronie.  Jeśli tak, przejdź do jednego z widoków wielu obrazów, aby skorzystać z grupowania.  

* W późniejszym czasie można zobaczyć, jakie **zadania są oznaczone** obok nazwy projektu.  Następnie zostaną wyświetlone obrazy z sugerowaną etykietą, która pochodzi z modelu klasyfikacji uczenia maszynowego. Żaden model uczenia maszynowego nie ma dokładności do 100%. Chociaż używamy tylko obrazów, dla których model jest pewny, te obrazy mogą nadal być nieprawidłowo oznaczone etykietami.  Gdy zobaczysz te etykiety, Popraw wszelkie niewłaściwe etykiety przed przesłaniem strony.  

* W przypadku modeli wykrywania obiektów mogą pojawić się pola ograniczenia i etykiety już obecne.  Popraw te, które są nieprawidłowe przed przesłaniem strony.

W szczególności przede wszystkim w projekcie etykietowania model uczenia maszynowego może być wystarczająco dokładny tylko, aby można było oznaczyć mały podzestaw obrazów. Gdy te obrazy są oznaczone etykietami, projekt etykietowania powróci do ręcznego etykietowania, aby zebrać więcej danych dla następnej rundy szkolenia modeli. W miarę upływu czasu model będzie bardziej wiedział o większej liczbie obrazów, co spowodowało bardziej szczegółowe zadanie zadań w dalszej części projektu.

## <a name="tag-images-for-multi-class-classification"></a>Tagowanie obrazów na potrzeby klasyfikacji z wieloma klasami

Jeśli projekt jest typu "Klasyfikacja obrazu wiele klas", przypiszesz jeden tag do całego obrazu. Aby zapoznać się ze wskazówkami w dowolnym momencie, przejdź do strony z **instrukcjami** i wybierz pozycję **Wyświetl szczegółowe instrukcje**.

Jeśli zauważysz, że po przypisaniu znacznika do obrazu wystąpi błąd, możesz go naprawić. Wybierz znak "**X**" na etykiecie, która jest wyświetlana poniżej obrazu, aby wyczyścić tag. Lub zaznacz obraz i wybierz inną klasę. Nowo wybrana wartość zastąpi poprzednio zastosowany tag.

## <a name="tag-images-for-multi-label-classification"></a>Tagowanie obrazów na potrzeby klasyfikacji z wieloma etykietami

Jeśli pracujesz nad projektem typu "Klasyfikacja obrazu z wieloma etykietami", zastosujesz jeden *lub więcej* tagów do obrazu. Aby wyświetlić wskazówki właściwe dla projektu, wybierz **instrukcje** i przejdź do szczegółów, aby **wyświetlić szczegółowe instrukcje**.

Wybierz obraz, który chcesz oznaczyć etykietą, a następnie wybierz tag. Tag zostanie zastosowany do wszystkich zaznaczonych obrazów, a następnie zostaną odwybrane. Aby zastosować więcej tagów, należy ponownie wybrać obrazy. Następujące animacje przedstawiają znakowanie wieloetykietowe:

1. **Opcja Zaznacz wszystko** służy do zastosowania tagu "Ocean".
1. Wybrano pojedynczy obraz i otagowano "Closeup".
1. Wybrano trzy obrazy i otagowano "szeroki kąt".

![Animacja pokazuje przepływ wieloetykietowy](./media/how-to-label-images/multilabel.gif)

Aby poprawić błąd, kliknij znak "**X**", aby wyczyścić pojedynczy tag, lub wybierz obrazy, a następnie wybierz tag, który czyści tag ze wszystkich zaznaczonych obrazów. Ten scenariusz jest przedstawiony tutaj. Kliknięcie pozycji "Ziemia" spowoduje wyczyszczenie tego tagu z dwóch zaznaczonych obrazów.

![Zrzut ekranu przedstawia wiele selektorów](./media/how-to-label-images/multiple-deselection.png)

Na platformie Azure zostanie włączony przycisk **Prześlij** tylko po zastosowaniu co najmniej jednego znacznika do każdego obrazu. Wybierz pozycję **Prześlij** , aby zapisać swoją służbę.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Tagowanie obrazów i określanie pól ograniczeń na potrzeby wykrywania obiektów

Jeśli projekt jest typu "Identyfikacja obiektu (pola ograniczające)", należy określić jedno lub więcej pól ograniczenia w obrazie i zastosować tag do każdego pola. Obrazy mogą zawierać wiele pól, z których każdy ma jeden tag. Użyj **widoku szczegółowe instrukcje** , aby określić, czy w projekcie są używane wiele pól ograniczenia.

1. Wybierz tag dla powiązanego pola, które ma zostać utworzone.
1. Wybierz **prostokątne** narzędzie ![ prostokątnego pola ](./media/how-to-label-images/rectangular-box-tool.png) i wybierz pozycję "R".
3. Kliknij i przeciągnij ukośnie na miejsce docelowe, aby utworzyć niedalekią ramkę. Aby dostosować pole ograniczenia, przeciągnij krawędzie lub rogi.

![Tworzenie pola ograniczenia](./media/how-to-label-images/bounding-box-sequence.png)

Aby usunąć pole ograniczenia, kliknij obiekt docelowy w kształcie X, który pojawia się obok pola ograniczenia po utworzeniu.

Nie można zmienić znacznika istniejącego pola ograniczenia. Jeśli wprowadzisz błąd przypisania znacznika, musisz usunąć pole ograniczenia i utworzyć nowe z prawidłowym tagiem.

Domyślnie można edytować istniejące pola ograniczenia. Narzędzie **blokowania/odblokowywania** regionów narzędzie do ![ blokowania/odblokowywania regionów ](./media/how-to-label-images/lock-bounding-boxes-tool.png) lub "L" przełącza takie zachowanie. Jeśli regiony są zablokowane, można zmienić tylko kształt lub lokalizację nowego pola ograniczenia.

Użyj narzędzia do manipulowania regionami **w regionach** narzędzi ![ ](./media/how-to-label-images/regions-tool.png) lub "M", aby dostosować istniejące pole ograniczenia. Przeciągnij krawędzie lub rogi, aby dostosować kształt. Kliknij wewnątrz, aby można było przeciągnąć całe pole ograniczenia. Jeśli nie możesz edytować regionu, prawdopodobnie przełączono narzędzie **blokowania/odblokowywania regionów** .

Użyj narzędzia **opartego na szablonie** narzędzia Box ![ ](./media/how-to-label-images/template-box-tool.png) lub "T", aby utworzyć wiele pól o tym samym rozmiarze. Jeśli obraz nie ma żadnych pól ograniczenia i uaktywniasz pola oparte na szablonach, narzędzie spowoduje utworzenie pól o 50 do 50 pikseli. W przypadku utworzenia pola ograniczenia, a następnie aktywowania pól opartych na szablonach wszystkie nowe pola ograniczenia będą rozmiarem ostatniego utworzonego pola. Po umieszczeniu można zmienić rozmiar pól opartych na szablonie. Zmienianie rozmiaru pola opartego na szablonie zmienia rozmiar tylko określonego pola.

Aby usunąć *wszystkie* pola związane z bieżącym obrazem, wybierz narzędzie **Usuń wszystkie regiony** narzędzia ![ Usuń regiony ](./media/how-to-label-images/delete-regions-tool.png) .

Po utworzeniu pól ograniczenia dla obrazu wybierz pozycję **Prześlij** , aby zapisać swoją służbę lub nie będzie można zapisać pracy w toku.

## <a name="tag-images-and-specify-polygons-for-image-segmentation"></a>Tworzenie tagów obrazów i określanie wielokątów dla segmentacji obrazów 

Jeśli projekt jest typu "segmentacja wystąpienia (Wielokąt)", należy określić jeden lub więcej wielokątów w obrazie i zastosować tag do każdego wielokąta. Obrazy mogą zawierać wiele wielokątów, z których każdy ma jeden tag. Użyj **widoku szczegółowe instrukcje** , aby określić, czy w projekcie są używane wiele wielokątów powiązanych.

1. Wybierz tag dla wielokąta, który ma zostać utworzony.
1. Wybierz narzędzie **Rysuj region wielokąta** , ![ rysując region wielokąta ](./media/how-to-label-images/polygon-tool.png) lub wybierz "P".
1. Kliknij dla każdego punktu w wielokąta.  Po zakończeniu kształtu kliknij dwukrotnie, aby zakończyć.

    :::image type="content" source="media/how-to-label-images/polygon.gif" alt-text="Tworzenie wielokątów dla klas Cat i Dog":::

Aby usunąć Wielokąt, kliknij obiekt docelowy w kształcie X, który pojawia się obok wielokąta po utworzeniu.

Jeśli chcesz zmienić tag dla wielokąta, wybierz narzędzie do **przenoszenia regionów** , kliknij Wielokąt i wybierz odpowiedni tag.

Można edytować istniejące wielokąty. Narzędzie **blokowania/odblokowywania regionów** ![ edytuje wielokąty za pomocą narzędzia do blokowania/odblokowywania regionów ](./media/how-to-label-images/lock-bounding-boxes-tool.png) lub "L" przełącza takie zachowanie. Jeśli regiony są zablokowane, można zmienić tylko kształt lub lokalizację nowego wielokąta.

Użyj narzędzia **Dodaj lub Usuń punkty wielokątów** ![ Dodaj lub Usuń punkty wielokątów ](./media/how-to-label-images/add-remove-points-tool.png) lub "U", aby dostosować istniejący wielokąt. Kliknij Wielokąt, aby dodać lub usunąć punkt. Jeśli nie możesz edytować regionu, prawdopodobnie przełączono narzędzie **blokowania/odblokowywania regionów** .

Aby usunąć *wszystkie* wielokąty w bieżącym obrazie, wybierz narzędzie **Usuń wszystkie regiony** narzędzia ![ Usuń wszystkie regiony ](./media/how-to-label-images/delete-regions-tool.png) .

Po utworzeniu wielokątów dla obrazu wybierz pozycję **Prześlij** , aby zapisać swoją służbę lub nie będzie można zapisać pracy w toku.

## <a name="finish-up"></a>Zakończenie

Gdy przesyłasz stronę oznakowanych danych, platforma Azure przypisze Ci nowe dane bez etykiet do użytkownika z kolejki służbowej. Jeśli nie ma więcej dostępnych danych bez etykiet, zostanie wyświetlony komunikat z linkiem do strony głównej portalu.

Gdy skończysz etykietowanie, wybierz swoją nazwę w prawym górnym rogu portalu etykietowania, a następnie wybierz pozycję **Wyloguj się**. Jeśli nie wylogujesz się, ostatecznie platforma Azure przekroczy czas, a następnie przypisze dane do innego Labeler.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [uczenie modeli klasyfikacji obrazów na platformie Azure](./tutorial-train-models-with-aml.md)


