---
title: Dodawanie jednostek — LUIS
description: Utwórz jednostki, aby wyodrębnić dane z wyrażenia długości użytkownika w aplikacjach Language Understanding (LUIS). Wyodrębnione dane jednostki są używane przez aplikację kliencką do fullfil żądań klientów.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5c6836c2d68036bf2b9c5abe191943537349b8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91540969"
---
# <a name="add-entities-to-extract-data"></a>Dodawanie jednostek do wyodrębniania danych

Utwórz jednostki, aby wyodrębnić dane z wyrażenia długości użytkownika w aplikacjach Language Understanding (LUIS). Wyodrębnione dane jednostki są używane przez aplikację kliencką do fullfil żądań klientów.

Jednostka reprezentuje słowo lub frazę w wypowiedź, które mają zostać wyodrębnione. Jednostki opisują informacje istotne dla zamiaru i czasami są niezbędne do wykonania zadania przez aplikację. Można utworzyć jednostki po dodaniu przykładu wypowiedź do zamiaru lub od (przed lub po) dodania przykładu wypowiedź do zamiaru.

## <a name="plan-entities-then-create-and-label"></a>Planowanie jednostek, a następnie Tworzenie i etykietowanie

jednostki uczenia maszynowego można tworzyć na podstawie przykładu wyrażenia długości lub z poziomu strony **jednostki** .

Ogólnie rzecz biorąc, najlepszym rozwiązaniem jest poświęcanie czasu na planowanie jednostek przed utworzeniem jednostki uczenia maszynowego w portalu. Następnie utwórz jednostkę uczenia maszynowego z przykładu wypowiedź z jak najwięcej szczegółów w podjednostkach i funkcjach, które znasz w danym momencie. [Samouczek dotyczący tworzenia jednostki](tutorial-machine-learned-entity.md) pokazuje, jak używać tej metody.

W ramach planowania jednostek, możesz mieć pewność, że potrzebujesz obiektów pasujących do tekstu (takich jak wstępnie skompilowane jednostki, jednostki wyrażeń regularnych lub jednostki listy). Można je utworzyć na podstawie strony **jednostki** , zanim zostaną one oznaczone jako przykładowe wyrażenia długości.

Podczas etykietowania można oznaczyć poszczególne jednostki, a następnie skompilować do nadrzędnej jednostki uczenia maszynowego. Można też rozpocząć od nadrzędnej jednostki uczenia maszynowego i rozłożyć na jednostki podrzędne.

> [!TIP]
>Oznacz wszystkie słowa, które mogą wskazywać na jednostkę, nawet jeśli wyrazy nie są używane podczas wyodrębniania w aplikacji klienckiej.

## <a name="when-to-create-an-entity"></a>Kiedy należy utworzyć jednostkę

Po zaplanowaniu jednostek należy utworzyć jednostki uczenia maszynowego i podjednostki. Może to wymagać dodania wstępnie utworzonych jednostek lub jednostek dopasowania tekstu w celu udostępnienia funkcji dla jednostek uczenia maszynowego. Wszystkie te czynności należy wykonać przed etykietami.

Po rozpoczęciu etykietowania przykładu wyrażenia długości można utworzyć jednostki, które są pouczenie maszynowe lub rozszerzając jednostki listy.

Skorzystaj z poniższej tabeli, aby zrozumieć, gdzie utworzyć lub dodać każdy typ jednostki do aplikacji.

|Typ jednostki|Gdzie można utworzyć jednostkę w portalu LUIS|
|--|--|
|Jednostka uczenia maszynowego|Szczegóły jednostek lub zamiaru|
|Jednostka listy|Szczegóły jednostek lub zamiaru|
|Jednostka wyrażenia regularnego|Jednostki|
|Jednostka Pattern.any|Jednostki|
|Wstępnie utworzona jednostka|Jednostki|
|Prebudowana jednostka domeny|Jednostki|

Wszystkie jednostki można utworzyć ze strony **jednostki** lub można utworzyć kilka jednostek w ramach etykietowania jednostki w przykładowym wypowiedź na stronie **szczegółów** elementu. Można _oznaczyć_ tylko jednostkę w przykładowym wypowiedź na stronie **szczegółów zamierzenia** .



## <a name="how-to-create-a-new-custom-entity"></a>Jak utworzyć nową jednostkę niestandardową

Ten proces działa w przypadku obiektów, które są obsługiwane przez maszynę, listy i jednostek wyrażeń regularnych.

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Wybierz stronę **jednostki** .
1. Wybierz pozycję **+ Utwórz**, a następnie wybierz typ jednostki.
1. Kontynuuj Konfigurowanie jednostki, a następnie wybierz pozycję **Utwórz** po zakończeniu.

## <a name="create-a-machine-learned-entity"></a>Tworzenie jednostki o Poznaniu maszynowym

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz pozycję **+ Utwórz**.
1. W oknie dialogowym **Tworzenie typu jednostki** wprowadź nazwę jednostki, a następnie wybierz opcję **Machined**, a następnie wybierz opcję. Aby dodać podjednostki, wybierz pozycję **Dodaj strukturę**. Wybierz przycisk **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający tworzenie jednostki uzyskanej na maszynie.](media/add-entities/machine-learned-entity-with-structure.png)

1. W obszarze **Dodawanie podjednostek** Dodaj podjednostkę, wybierając ją **+** w wierszu jednostki nadrzędnej.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Dodawanie podjednostek.](media/add-entities/machine-learned-entity-with-subentities.png)

1. Wybierz pozycję **Utwórz** , aby zakończyć proces tworzenia.

## <a name="add-a-feature-to-a-machine-learned-entity"></a>Dodaj funkcję do jednostki, której dotyczy dana maszyna

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz jednostkę, którą pouczysz się.
1. Dodaj funkcję, wybierając pozycję **+ Dodaj funkcję** w wierszu jednostki lub podjednostki.
1. Wybierz z listy istniejące jednostki i frazy.
1. Jeśli jednostka powinna zostać wyodrębniona tylko wtedy, gdy funkcja zostanie znaleziona, wybierz gwiazdkę `*` dla tej funkcji.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Dodawanie funkcji do jednostki.](media/add-entities/machine-learned-entity-schema-with-features.png)

## <a name="create-a-regular-expression-entity"></a>Tworzenie jednostki wyrażenia regularnego

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz pozycję **+ Utwórz**.

1. W oknie dialogowym **Tworzenie typu jednostki** wprowadź nazwę jednostki i wybierz opcję wyrażenie **regularne**, wprowadź wyrażenie regularne w polu **wyrażeń** regularnych i wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający tworzenie jednostki wyrażenia regularnego.](media/add-entities/add-regular-expression-entity.png)


<a name="add-list-entities"></a>

## <a name="create-a-list-entity"></a>Tworzenie jednostki listy

Jednostki listy reprezentują usunięty, zamknięty zestaw powiązanych wyrazów. Chociaż autor może zmienić listę, LUIS nie zostanie powiększony ani zmniejszony. Można również zaimportować do istniejącej jednostki listy przy użyciu [formatu Entity. JSON](reference-entity-list.md#example-json-to-import-into-list-entity).

Na poniższej liście pokazano kanoniczną nazwę i synonimy.

|Nazwa elementu listy kolorów|Kolory — synonimy|
|--|--|
|Red (Czerwony)|Użycie Crismon, krew, Apple, ogień|
|Blue (Niebieski)|, kobalt|
|Green (Zielony)|Kelly, limonowa|

Użyj procedury, aby utworzyć jednostkę listy. Po utworzeniu jednostki listy nie musisz oznaczać przykładu wyrażenia długości w zamiarze. Elementy listy i synonimy są dopasowywane przy użyciu dokładnego tekstu.
1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz pozycję **+ Utwórz**.

1. W oknie dialogowym **Tworzenie typu jednostki** wprowadź nazwę jednostki, na przykład `Colors` i wybierz pozycję **Lista**.
1. W oknie dialogowym **Tworzenie jednostki listy** , w polu **Dodaj nową podlistę.**....., wprowadź nazwę elementu listy, np. `Green` , a następnie Dodaj synonimy.

    > [!div class="mx-imgBorder"]
    > ![Utwórz listę kolorów jako jednostkę listy na stronie szczegółów jednostki.](media/how-to-add-entities/create-list-entity-of-colors.png)

1. Po zakończeniu dodawania elementów listy i synonimów wybierz pozycję **Utwórz**.

    Po zakończeniu pracy z grupą zmian w aplikacji Pamiętaj, aby **nauczyć** aplikację. Nie szkol aplikacji po pojedynczej zmianie.

    > [!NOTE]
    > Ta procedura ilustruje tworzenie i etykietowanie jednostki listy na podstawie przykładowej wypowiedź na stronie **szczegółów intencji** . Możesz również utworzyć tę samą jednostkę na stronie **jednostki** .

## <a name="add-a-role-for-an-entity"></a>Dodawanie roli dla jednostki

Rola jest nazwanym podtypem jednostki na podstawie kontekstu.

### <a name="add-a-role-to-distinguish-different-contexts"></a>Dodawanie roli w celu rozróżnienia różnych kontekstów

W poniższym wypowiedź znajdują się dwie lokalizacje, a każda z nich jest określona semantycznie przez słowa wokół niej, takie jak `to` i `from` :

`Pick up the package from Seattle and deliver to New York City.`

W tej procedurze Dodaj `origin` role i `destination` do wstępnie skompilowanej jednostki geographyV2.
1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu.

1. Wybierz pozycję **+ Dodaj wstępnie utworzoną jednostkę**. Wybierz pozycję **geographyV2** , a następnie wybierz pozycję **gotowe**. Spowoduje to dodanie wstępnie skompilowanej jednostki do aplikacji.

    Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](reference-pattern-syntax.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem.

1. Wybierz nowo dodaną wbudowaną jednostkę geographyV2 z listy **jednostki** jednostki.
1. Aby dodać nową rolę, wybierz pozycję **+** Dalej, aby **nie dodaliśmy żadnych ról**.
1. W polu tekstowym **Typ roli..** . Wprowadź nazwę roli, `Origin` a następnie wprowadź wartość. Dodaj drugą nazwę roli, `Destination` a następnie wprowadź.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Dodawanie roli pochodzenia do jednostki lokalizacji](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Rola jest dodawana do wstępnie skompilowanej jednostki, ale nie jest dodawana do żadnych wyrażenia długości przy użyciu tej jednostki.

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Etykieta tekstu z rolą w przykładzie wypowiedź

> [!TIP]
> Role mogą zostać zastąpione etykietami z podjednostkami jednostek uczenia maszynowego.

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Przejdź do strony szczegółów intencji, która ma przykład wyrażenia długości, który korzysta z roli.
1. Aby oznaczyć rolę, wybierz etykietę jednostki (linię ciągłą w obszarze tekst) w przykładzie wypowiedź, a następnie wybierz pozycję **Wyświetl w okienku jednostki** z listy rozwijanej.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu pokazuje wybrany element menu okienko jednostki.](media/add-entities/view-in-entity-pane.png)

    Paleta jednostek zostanie otwarta po prawej stronie.

1. Wybierz jednostkę, a następnie przejdź do dolnej części palety i wybierz rolę.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia lokalizację, w której ma zostać wybrana rola.](media/add-entities/select-role-in-entity-palette.png)

<a name="add-pattern-any-entities"></a>
<a name="add-a-patternany-entity"></a>
<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-patternany-entity"></a>Tworzenie wzorca. dowolna jednostka

**Wzorzec. Każda** jednostka jest dostępna [tylko ze](luis-how-to-model-intent-pattern.md)wzorcami.


## <a name="do-not-change-entity-type"></a>Nie zmieniaj typu jednostki

LUIS nie pozwala na zmianę typu jednostki, ponieważ nie wie, co należy dodać lub usunąć, aby utworzyć tę jednostkę. Aby zmienić typ, lepiej jest utworzyć nową jednostkę poprawnego typu przy użyciu nieco innej nazwy. Po utworzeniu jednostki w każdym wypowiedź Usuń starą nazwę jednostki oznaczonej etykietą i Dodaj nową nazwę jednostki. Gdy wszystkie wyrażenia długości zostały ponownie oznaczone etykietami, usuń starą jednostkę.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Korzystanie ze wstępnie utworzonych modeli](howto-add-prebuilt-models.md)

Dowiedz się więcej na następujące tematy:
* Jak [nauczyć](luis-how-to-train.md) się
* [Testowanie](luis-interactive-test.md)
* Jak [opublikować](luis-how-to-publish-app.md)
* Modele
    * [Pojęcia](luis-concept-patterns.md)
    * [Składnia](reference-pattern-syntax.md)
* [Repozytorium GitHub dla wstępnie utworzonych jednostek](https://github.com/Microsoft/Recognizers-Text)
* [Pojęcia dotyczące wyodrębniania danych](luis-concept-data-extraction.md)



