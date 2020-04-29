---
title: Dodawanie jednostek — LUIS
titleSuffix: Azure Cognitive Services
description: Utwórz jednostki, aby wyodrębnić dane z wyrażenia długości użytkownika w aplikacjach Language Understanding (LUIS). Wyodrębnione dane jednostki są używane przez aplikację kliencką do fullfil żądań klientów.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220909"
---
# <a name="add-entities-to-extract-data"></a>Dodawanie jednostek do wyodrębniania danych 

Utwórz jednostki, aby wyodrębnić dane z wyrażenia długości użytkownika w aplikacjach Language Understanding (LUIS). Wyodrębnione dane jednostki są używane przez aplikację kliencką do fullfil żądań klientów.

Jednostka reprezentuje słowo lub frazę w wypowiedź, które mają zostać wyodrębnione. Jednostki opisują informacje istotne dla zamiaru i czasami są niezbędne do wykonania zadania przez aplikację. Można utworzyć jednostki po dodaniu przykładu wypowiedź do zamiaru lub od (przed lub po) dodania przykładu wypowiedź do zamiaru.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Planowanie jednostek, a następnie Tworzenie i etykietowanie

Jednostki z możliwością uczenia maszynowego można utworzyć na podstawie przykładu wyrażenia długości lub utworzyć na stronie **jednostki** . 

Ogólnie rzecz biorąc, najlepszym rozwiązaniem jest poświęcanie czasu na planowanie jednostek przed utworzeniem jednostki, która jest wyszukiwana przez maszynę w portalu. Następnie utwórz jednostkę uczenia maszynowego na podstawie przykładu wypowiedź z jak najwięcej szczegółów w podskładnikach i deskryptorach i ograniczeniach, jak wiadomo w czasie. [Samouczek dotyczący tworzenia jednostki](tutorial-machine-learned-entity.md) pokazuje, jak używać tej metody. 

W ramach planowania jednostek, możesz mieć pewność, że potrzebujesz obiektów pasujących do tekstu (takich jak wstępnie skompilowane jednostki, jednostki wyrażeń regularnych lub jednostki listy). Można je utworzyć na podstawie strony **jednostki** , zanim zostaną one oznaczone jako przykładowe wyrażenia długości. 

Podczas etykietowania można oznaczyć poszczególne jednostki, a następnie skompilować do jednostki nadrzędnej. Można też rozpocząć od nadrzędnej maszyny i rozłożyć na jednostki podrzędne. 

> [!TIP] 
>Oznacz wszystkie słowa, które mogą wskazywać na jednostkę, nawet jeśli wyrazy nie są używane podczas wyodrębniania w aplikacji klienckiej. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Tworzenie jednostki przed lub z etykietami

W poniższej tabeli przedstawiono jednostki, w których można utworzyć lub dodać każdą jednostkę do aplikacji. 

|Typ jednostki|Gdzie można utworzyć jednostkę w portalu LUIS|
|--|--|
|Jednostka nauczona maszynowo|Szczegóły jednostek lub zamiaru|
|Jednostka listy|Szczegóły jednostek lub zamiaru|
|Jednostka wyrażenia regularnego|Jednostki|
|Jednostka Pattern.any|Jednostki|
|Wstępnie utworzona jednostka|Jednostki|
|Prebudowana jednostka domeny|Jednostki|

Wszystkie jednostki można utworzyć ze strony **jednostki** lub można utworzyć kilka jednostek w ramach etykietowania jednostki w przykładowym wypowiedź na stronie **szczegółów** elementu. Można _oznaczyć_ tylko jednostkę w przykładowym wypowiedź na stronie **szczegółów zamierzenia** . 

## <a name="create-a-machine-learned-entity"></a>Tworzenie jednostki o Poznaniu maszynowym

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Tworzenie jednostki dopasowania tekstu

Użycie jednostek dopasowania tekstu zapewnia kilka sposobów wyodrębnienia danych:

|Jednostki pasujące do tekstu|Przeznaczenie|
|--|--|
|[Jednostka listy](#add-list-entities-for-exact-matches)|Lista nazw kanonicznych wraz z synonimami jako alternatywnymi formularzami|
|Jednostka wyrażenia regularnego|dopasowanie tekstu przy użyciu jednostki wyrażenia regularnego|
|[Wstępnie utworzona jednostka](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|dopasowuje typowe typy danych, takie jak Number, email, Date|
|Prebudowana jednostka domeny|Dopasuj przy użyciu wybranych domen podmiotu|
|[Wzorzec. any](#add-a-patternany-entity)| Aby dopasować jednostki, które można łatwo pomylić z otaczającym tekstem|  

Wstępnie utworzone jednostki działają bez udostępniania niestandardowych danych szkoleniowych. Inne jednostki muszą podawać dane szkolenia klienta (takie jak elementy jednostki listy) lub wyrażenie (takie jak wyrażenie regularne lub wzorzec. any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Jak utworzyć nową jednostkę niestandardową

1. W portalu LUIS przejdź do sekcji **Zarządzanie** , a następnie strony **jednostki** . 
1. Wybierz pozycję **+ Utwórz**, a następnie wybierz typ jednostki. 
1. Kontynuuj Konfigurowanie jednostki, a następnie wybierz pozycję **Utwórz** po zakończeniu. 

### <a name="add-list-entities-for-exact-matches"></a>Dodaj jednostki listy dla dokładnych dopasowań

Jednostki listy reprezentują usunięty, zamknięty zestaw powiązanych wyrazów. Chociaż autor może zmienić listę, LUIS nie zostanie powiększony ani zmniejszony. Można również zaimportować do istniejącej jednostki listy przy użyciu formatu [list Entity. JSON (odwołanie-Entity-List. MD # example-JSON-to-list-Entity-on 

Na poniższej liście pokazano kanoniczną nazwę i synonimy. 

|Nazwa elementu listy kolorów|Kolory — synonimy|
|--|--|
|Red|Użycie Crismon, krew, Apple, ogień|
|Blue|, platforma Azure, kobalt|
|Zielony|Kelly, limonowa|

Użyj procedury, aby utworzyć jednostkę listy. Po utworzeniu jednostki listy nie musisz oznaczać przykładu wyrażenia długości w zamiarze. Elementy listy i synonimy są dopasowywane przy użyciu dokładnego tekstu. 

1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz pozycję **+ Utwórz**.

1. W oknie dialogowym **Tworzenie typu jednostki** wprowadź nazwę jednostki, na przykład `Colors` i wybierz pozycję **Lista**.
1. W oknie dialogowym **Tworzenie jednostki listy** , w polu **Dodaj nową podlistę.**....., wprowadź nazwę elementu listy, np. `Green`, a następnie Dodaj synonimy.

    > [!div class="mx-imgBorder"]
    > ![Utwórz listę kolorów jako jednostkę listy na stronie szczegółów jednostki.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Po zakończeniu dodawania elementów listy i synonimów wybierz pozycję **Utwórz**.

    Po zakończeniu pracy z grupą zmian w aplikacji Pamiętaj, aby **nauczyć** aplikację. Nie szkol aplikacji po pojedynczej zmianie. 

    > [!NOTE]
    > Ta procedura ilustruje tworzenie i etykietowanie jednostki listy na podstawie przykładowej wypowiedź na stronie **szczegółów intencji** . Możesz również utworzyć tę samą jednostkę na stronie **jednostki** .

## <a name="add-a-role-for-an-entity"></a>Dodawanie roli dla jednostki

Rola jest nazwanym podtypem jednostki na podstawie kontekstu. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Dodawanie roli w celu rozróżnienia różnych kontekstów

W poniższym wypowiedź znajdują się dwie lokalizacje, a każda z nich jest określona semantycznie przez słowa wokół niej, takie `to` jak `from`i: 

`Pick up the package from Seattle and deliver to New York City.`

W tej procedurze Dodaj `origin` role i `destination` do wstępnie skompilowanej jednostki geographyV2.

1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu.

1. Wybierz pozycję **+ Dodaj wstępnie utworzoną jednostkę**. Wybierz pozycję **geographyV2** , a następnie wybierz pozycję **gotowe**. Spowoduje to dodanie wstępnie skompilowanej jednostki do aplikacji.
    
    Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](reference-pattern-syntax.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem. 

1. Wybierz nowo dodaną wbudowaną jednostkę geographyV2 z listy **jednostki** jednostki. 
1. Aby dodać nową rolę, wybierz pozycję **+** dalej, aby **nie dodaliśmy żadnych ról**.
1. W polu tekstowym **Typ roli..** . Wprowadź nazwę roli `Origin` , a następnie wprowadź wartość. Dodaj drugą nazwę roli, `Destination` a następnie wprowadź. 

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Dodawanie roli pochodzenia do jednostki lokalizacji](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Rola jest dodawana do wstępnie skompilowanej jednostki, ale nie jest dodawana do żadnych wyrażenia długości przy użyciu tej jednostki. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Etykieta tekstu z rolą w przykładzie wypowiedź

1. Przejdź do strony szczegółów intencji, która ma przykład wyrażenia długości, który korzysta z roli. 
1. Aby oznaczyć rolę, wybierz etykietę jednostki (linię ciągłą w obszarze tekst) w przykładzie wypowiedź, a następnie wybierz pozycję **Wyświetl w palecie jednostek** z listy rozwijanej. 

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Wybieranie widoku w palecie jednostek](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Paleta jednostek zostanie otwarta po prawej stronie. 

1. Wybierz jednostkę, a następnie przejdź do dolnej części palety i wybierz rolę. 

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Wybieranie widoku w palecie jednostek](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Dodaj wzorzec. dowolna jednostka

[Wzorzec. wszystkie](luis-concept-entity-types.md) jednostki są prawidłowe tylko w [wzorcach](luis-how-to-model-intent-pattern.md), a nie na przykład wyrażenia długości. Ten typ jednostki ułatwia LUIS znalezienie końca jednostek o różnej długości i wyborze wyrazu. Ponieważ ta jednostka jest używana we wzorcu, LUIS wie, gdzie koniec jednostki znajduje się w szablonie wypowiedź.

### <a name="steps-to-create-a-patternany-entity"></a>Procedura tworzenia wzorca. dowolna jednostka

1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz pozycję **+ Utwórz**.

1. W oknie dialogowym **Wybierz typ jednostki** wprowadź nazwę jednostki w polu **Nazwa** , a następnie wybierz pozycję **wzorzec. dowolny** **Typ** , a następnie wybierz pozycję **Utwórz**.

    Po [utworzeniu wypowiedź wzorca](luis-how-to-model-intent-pattern.md) przy użyciu tej jednostki jednostka jest wyodrębniana z połączonym algorytmem zgodnym z maszyną i tekstem. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Utwórz szablon wzorca wypowiedź, aby użyć wzorca. dowolna jednostka

Aby użyć wzorca. dowolna jednostka, Dodaj wzorzec na stronie **wzorce** , w sekcji **Poprawianie wydajności aplikacji** z poprawną składnią nawiasów klamrowych, taką jak `Where is **{HumanResourcesFormTitle}** on the server?`.

Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](reference-pattern-syntax.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem. 

## <a name="do-not-change-entity-type"></a>Nie zmieniaj typu jednostki

LUIS nie pozwala na zmianę typu jednostki, ponieważ nie wie, co należy dodać lub usunąć, aby utworzyć tę jednostkę. Aby zmienić typ, lepiej jest utworzyć nową jednostkę poprawnego typu przy użyciu nieco innej nazwy. Po utworzeniu jednostki w każdym wypowiedź Usuń starą nazwę jednostki oznaczonej etykietą i Dodaj nową nazwę jednostki. Gdy wszystkie wyrażenia długości zostały ponownie oznaczone etykietami, usuń starą jednostkę. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Korzystanie ze wstępnie utworzonych modeli](howto-add-prebuilt-models.md) 

Dowiedz się więcej o usługach:
* Jak [nauczyć](luis-how-to-train.md) się
* [Testowanie](luis-interactive-test.md)
* Jak [opublikować](luis-how-to-publish-app.md)
* Modele
    * [Pojęcia](luis-concept-patterns.md)
    * [Obowiązuje](reference-pattern-syntax.md)
* [Repozytorium GitHub dla wstępnie utworzonych jednostek](https://github.com/Microsoft/Recognizers-Text)
* [Pojęcia dotyczące wyodrębniania danych](luis-concept-data-extraction.md)


 
