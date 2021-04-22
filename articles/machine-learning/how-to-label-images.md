---
title: Tagowanie obrazów w projekcie etykietowania
title.suffix: Azure Machine Learning
description: Dowiedz się, jak używać narzędzi do tagowania danych, aby szybko przygotować dane do Machine Learning w projekcie Azure Machine Learning etykietowania.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: 8f1d648c38760865752c87624dfcb112933650c7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872627"
---
# <a name="tag-images-in-a-labeling-project"></a>Tagowanie obrazów w projekcie etykietowania 

Gdy administrator projektu utworzy [projekt etykietowania](./how-to-create-labeling-projects.md#create-a-data-labeling-project) danych w Azure Machine Learning, możesz użyć narzędzia do etykietowania, aby szybko przygotować dane do Machine Learning projektu. W tym artykule opisano:

> [!div class="checklist"]
> * Jak uzyskać dostęp do projektów etykietowania
> * Narzędzia do etykietowania
> * Jak używać narzędzi do określonych zadań etykietowania

## <a name="prerequisites"></a>Wymagania wstępne

* Konto [konto Microsoft](https://account.microsoft.com/account) lub Azure Active Directory dla organizacji i projektu
* Dostęp na poziomie współautora do obszaru roboczego zawierającego projekt etykietowania.

## <a name="sign-in-to-the-workspace"></a>Logowanie do obszaru roboczego

1. Zaloguj się do [Azure Machine Learning studio](https://ml.azure.com).

1. Wybierz subskrypcję i obszar roboczy, który zawiera projekt etykietowania.  Uzyskaj te informacje od administratora projektu.

1. Wybierz **pozycję Etykiety** danych po lewej stronie, aby znaleźć projekt.  

## <a name="understand-the-labeling-task"></a>Opis zadania etykietowania

W tabeli projektów etykietowania danych wybierz **link Etykieta** dla projektu.

Zostaną wyświetlony instrukcje specyficzne dla projektu. Wyjaśniają one typy danych, z którymi masz do czynienia, sposób podejmowania decyzji i inne istotne informacje. Po przeczytaniu tych informacji w górnej części strony wybierz pozycję **Zadania**.  Lub w dolnej części strony wybierz pozycję **Rozpocznij etykietowanie**.

## <a name="common-features-of-the-labeling-task"></a>Typowe funkcje zadania etykietowania

We wszystkich zadaniach etykietowania obrazów należy wybrać odpowiedni tag lub tagi z zestawu określonego przez administratora projektu. Pierwsze dziewięć tagów można wybrać przy użyciu klawiszy liczbowych na klawiaturze.  

W zadaniach klasyfikacji obrazów można wybrać jednoczesne wyświetlanie wielu obrazów. Użyj ikon powyżej obszaru obrazu, aby wybrać układ. 

Aby wybrać wszystkie wyświetlane obrazy jednocześnie, użyj **opcji Zaznacz wszystkie**. Aby wybrać poszczególne obrazy, użyj cyklicznego przycisku wyboru w prawym górnym rogu obrazu. Aby zastosować tag, musisz wybrać co najmniej jeden obraz. W przypadku wybrania wielu obrazów każdy wybrany tag zostanie zastosowany do wszystkich wybranych obrazów.

W tym miejscu wybraliśmy układ "dwa po dwa" i zajmijmy się zastosowaniem tagu "Chylić" do obrazów niedźwiedzia i orki. Obraz otagowany już jako "Cartilaginous gos", a iguana nie została jeszcze otagowana.

![Wiele układów obrazów i wybór](./media/how-to-label-images/layouts.png)

> [!Important] 
> Układy są przełączane tylko wtedy, gdy masz nową stronę danych bez etykiet. Przełączanie układów wyczyści pracę tagowania w toku na stronie.

Platforma Azure włącza **przycisk Prześlij** po otagowaniu wszystkich obrazów na stronie. Wybierz **pozycję Prześlij,** aby zapisać swoją pracę.

Po przesłaniu tagów dla danych platforma Azure odświeża stronę przy użyciu nowego zestawu obrazów z kolejki służbowej.

### <a name="assisted-machine-learning"></a>Wspomagane uczenie maszynowe

Algorytmy uczenia maszynowego mogą być wyzwalane. Jeśli te algorytmy są włączone w projekcie, mogą zostać wyświetlony następujące informacje:

* Po oznaczeniu niektórych obrazów etykietami  obok nazwy projektu może być widać zadania klastrowane w górnej części ekranu.  Oznacza to, że obrazy są grupowane w celu przedstawienia podobnych obrazów na tej samej stronie.  Jeśli tak, przełącz się do jednego z wielu widoków obrazów, aby skorzystać z grupowania.  

* W późniejszym momencie obok  nazwy projektu może zostać wyświetlony etykietą Zadania.  Następnie zostaną wyświetlone obrazy z sugerowaną etykietą pochodzącą z modelu klasyfikacji uczenia maszynowego. Żaden model uczenia maszynowego nie ma 100% dokładności. Chociaż używamy tylko obrazów, dla których model jest pewien, te obrazy mogą być nadal niepoprawnie oznaczane etykietami wstępnymi.  Gdy zobaczysz te etykiety, popraw wszelkie niewłaściwe etykiety przed przesłaniem strony.  

* W przypadku modeli identyfikacji obiektów mogą być już obecne pola i etykiety.  Popraw te, które są nieprawidłowe przed przesłaniem strony.

* W przypadku modeli segmentacji mogą być już obecne wielokąty i etykiety.  Popraw te, które są nieprawidłowe przed przesłaniem strony. 

Szczególnie na wczesnym etapie projektu etykietowania model uczenia maszynowego może być wystarczająco dokładny, aby wstępnie oznaczać mały podzbiór obrazów. Gdy te obrazy zostaną oznaczone etykietami, projekt etykietowania powróci do etykietowania ręcznego, aby zebrać więcej danych na następną rundę trenowania modelu. Z czasem model będzie mieć większą pewność co do większej proporcji obrazów, co spowoduje więcej zadań dotyczących etykiet wstępnych w dalszej części projektu.

## <a name="tag-images-for-multi-class-classification"></a>Tagowanie obrazów na potrzeby klasyfikacji z wieloma klasami

Jeśli projekt jest typu "Klasyfikacja obrazów wieloklasowa", przypiszesz pojedynczy tag do całego obrazu. Aby przejrzeć wskazówki w dowolnym momencie, przejdź do strony **Instrukcje** i wybierz **pozycję Wyświetl szczegółowe instrukcje.**

Jeśli po przypisaniu tagu do obrazu popełnisz błąd, możesz go naprawić. Wybierz znak **"X"** na etykiecie wyświetlanej poniżej obrazu, aby wyczyścić tag. Możesz też wybrać obraz i wybrać inną klasę. Nowo wybrana wartość zastąpi wcześniej zastosowany tag.

## <a name="tag-images-for-multi-label-classification"></a>Tagowanie obrazów na potrzeby klasyfikacji z wieloma etykietami

Jeśli pracujesz nad projektem typu "Klasyfikacja obrazów z wieloma etykietami", zastosujesz do obrazu co najmniej jeden tag.  Aby wyświetlić wskazówki specyficzne dla projektu, wybierz pozycję **Instrukcje** i przejdź do **tematu Wyświetlanie szczegółowych instrukcji**.

Wybierz obraz, który chcesz oznaczyć etykietą, a następnie wybierz tag. Tag jest stosowany do wszystkich wybranych obrazów, a następnie obrazy są odznaczane. Aby zastosować więcej tagów, należy ponownie wybrać obrazy. Następująca animacja przedstawia tagowanie z wieloma etykietami:

1. **Wybierz pozycję wszystkie,** aby zastosować tag "Ocean".
1. Wybrany jest pojedynczy obraz z tagiem "Closeup" (Zbliżenie).
1. Wybierane są trzy obrazy z tagiem "Szeroki kąt".

![Animacja przedstawia przepływ z wieloma etykietami](./media/how-to-label-images/multilabel.gif)

Aby naprawić błąd, kliknij znak **"X",** aby wyczyścić pojedynczy tag lub wybrać obrazy, a następnie wybierz tag, który wyczyści tag ze wszystkich wybranych obrazów. Ten scenariusz przedstawiono tutaj. Kliknięcie pozycji "Land" (Kraj) spowoduje wyczyszczenie tego tagu z dwóch wybranych obrazów.

![Zrzut ekranu przedstawiający wiele desekrów](./media/how-to-label-images/multiple-deselection.png)

Platforma Azure włączy przycisk **Prześlij** tylko po zastosowaniu co najmniej jednego tagu do każdego obrazu. Wybierz **pozycję Prześlij,** aby zapisać swoją pracę.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Tagowanie obrazów i określanie pól ograniczeń na potrzeby wykrywania obiektów

Jeśli projekt jest typu "Identyfikacja obiektu (pola ograniczone)", określ co najmniej jedno pole graniczujące na obrazie i zastosujesz tag do każdego pola. Obrazy mogą mieć wiele pól ograniczonych, z których każdy ma jeden tag. Użyj **opcji Wyświetl szczegółowe instrukcje,** aby określić, czy w projekcie jest używanych wiele pól ograniczonych.

1. Wybierz tag dla pola granicy, które zamierzasz utworzyć.
1. Wybierz narzędzie **Prostokątne pole** ![ narzędzie Prostokątne pole lub wybierz pozycję ](./media/how-to-label-images/rectangular-box-tool.png) "R".
3. Kliknij i przeciągnij po przekątnej obiekt docelowy, aby utworzyć nierówne pole granicy. Aby dostosować pole granicy, przeciągnij krawędzie lub narożniki.

![Tworzenie pola ograniczonego](./media/how-to-label-images/bounding-box-sequence.png)

Aby usunąć pole granicy, kliknij element docelowy w kształcie X, który zostanie wyświetlony obok pola granicy po utworzeniu.

Nie można zmienić tagu istniejącego pola granicy. Jeśli popełnisz błąd przypisania tagu, musisz usunąć pole granicy i utworzyć nowe z poprawnym tagiem.

Domyślnie można edytować istniejące pola ograniczone. To **zachowanie można przełączać** za pomocą narzędzia Do blokowania/odblokowywania regionów ![ ](./media/how-to-label-images/lock-bounding-boxes-tool.png) blokady/odblokowywania regionów lub "L". Jeśli regiony są zablokowane, można zmienić tylko kształt lub lokalizację nowego pola granicy.

Użyj narzędzia **do manipulowania regionami** To jest ikona narzędzia do manipulowania regionami — cztery strzałki wskazująca na zewnątrz od środka, w ![ górę, w prawo, w dół i w lewo.](./media/how-to-label-images/regions-tool.png) lub "M", aby dostosować istniejące pole granicy. Przeciągnij krawędzie lub narożniki, aby dostosować kształt. Kliknij wewnątrz, aby przeciągać całe pole granicy. Jeśli nie możesz edytować regionu, prawdopodobnie masz przełączone narzędzie **Regiony blokady/odblokowania.**

Użyj narzędzia **pola opartego** na szablonach Narzędzia pola szablonu lub "T", aby utworzyć wiele pól ograniczonych ![ o tym samym ](./media/how-to-label-images/template-box-tool.png) rozmiarze. Jeśli obraz nie ma pól ograniczonych i aktywujesz pola oparte na szablonach, narzędzie wyprodukuje pola o rozmiarze 50 na 50 pikseli. Jeśli utworzysz pole granicy, a następnie aktywujesz pola oparte na szablonie, wszystkie nowe pola będą mieć rozmiar ostatniego utworzonego pola. Rozmiar pól opartych na szablonach można zmieniać po umieszczeniu. Zmiana rozmiaru pola opartego na szablonie zmienia rozmiar tylko tego pola.

Aby usunąć *wszystkie* pola granic na bieżącym obrazie, wybierz narzędzie Usuń wszystkie **regiony** ![ Usuń ](./media/how-to-label-images/delete-regions-tool.png) regiony.

Po utworzeniu pól ograniczonych dla obrazu  wybierz pozycję Prześlij, aby zapisać swoją pracę lub praca w toku nie zostanie zapisana.

## <a name="tag-images-and-specify-polygons-for-image-segmentation"></a>Tagowanie obrazów i określanie wielokątów dla segmentacji obrazów 

Jeśli projekt ma typ "Segmentacja wystąpienia (wielokąt),należy określić co najmniej jeden wielokąt na obrazie i zastosować tag do każdego wielokąta. Obrazy mogą mieć wiele wielokątów granicznych, z których każdy ma jeden tag. Użyj **opcji Wyświetl szczegółowe instrukcje,** aby określić, czy w projekcie jest używanych wiele wielokątów powiązanych.

1. Wybierz tag dla wielokąta, który chcesz utworzyć.
1. Wybierz narzędzie **Rysowanie regionu wielokąta** ![ Narzędzie Rysowanie regionu wielokąta ](./media/how-to-label-images/polygon-tool.png) lub wybierz pozycję "P".
1. Kliknij każdy punkt wielokąta.  Po zakończeniu kształtowania kliknij dwukrotnie, aby zakończyć.

    :::image type="content" source="media/how-to-label-images/polygon.gif" alt-text="Tworzenie wielokątów dla kotów i psów":::

Aby usunąć wielokąt, kliknij element docelowy w kształcie X, który zostanie wyświetlony obok wielokąta po utworzeniu.

Jeśli chcesz zmienić tag wielokąta, wybierz narzędzie **Przenoszenie regionu,** kliknij wielokąt i wybierz poprawny tag.

Istniejące wielokąty można edytować. Narzędzie **Regiony blokady/odblokowania** Edytuj wielokąty za pomocą narzędzia regiony blokady/odblokowania lub ![ ](./media/how-to-label-images/lock-bounding-boxes-tool.png) "L" przełącza to zachowanie. Jeśli regiony są zablokowane, można zmienić tylko kształt lub lokalizację nowego wielokąta.

Użyj narzędzia **Dodaj lub usuń punkty wielokąta.** Jest to ikona narzędzia Dodaj lub usuń punkty ![ wielokąta.](./media/how-to-label-images/add-remove-points-tool.png) lub "U", aby dostosować istniejący wielokąt. Kliknij wielokąt, aby dodać lub usunąć punkt. Jeśli nie możesz edytować regionu, prawdopodobnie masz przełączone narzędzie **Regiony blokady/odblokowania.**

Aby usunąć *wszystkie wielokąty* na bieżącym obrazie, wybierz narzędzie **Usuń wszystkie regiony** Usuń wszystkie ![ ](./media/how-to-label-images/delete-regions-tool.png) regiony.

Po utworzeniu wielokątów dla obrazu wybierz pozycję **Prześlij,** aby zapisać swoją pracę lub praca w toku nie zostanie zapisana.

## <a name="finish-up"></a>Kończenie

Po przesłaniu strony otagowanych danych platforma Azure przypisuje nowe dane bez etykiet z kolejki służbowej. Jeśli nie ma już dostępnych danych bez etykiet, zostanie wyświetlony komunikat z tym komunikatem wraz z linkiem do strony głównej portalu.

Po oznaczeniu etykiet wybierz swoją nazwę w prawym górnym rogu portalu etykietowania, a następnie wybierz **pozycję wyloguj się.** Jeśli się nie wylogujesz, platforma Azure "wyloguje się" i przypisze dane do innego etykiety.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [trenować modele klasyfikacji obrazów na platformie Azure](./tutorial-train-models-with-aml.md)


