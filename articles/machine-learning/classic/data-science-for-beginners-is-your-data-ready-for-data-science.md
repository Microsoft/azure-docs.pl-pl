---
title: 'ML Studio (klasyczny): Ocena danych — Azure'
description: Cztery kryteria, które muszą spełniać dane, aby były gotowe do analizy danych. Ten film wideo zawiera konkretne przykłady ułatwiające ocenę podstawowych danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 2174044a1a913663e255b125f2ea89c312f5e9d3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307708"
---
# <a name="is-your-data-ready-for-data-science"></a>Czy Twoje dane są gotowe na analizę danych?
## <a name="video-2-data-science-for-beginners-series"></a>Wideo 2: Analiza danych dla początkujących serii
Dowiedz się, jak oszacować dane, aby upewnić się, że spełniają one podstawowe kryteria, aby były gotowe do analizy danych.

Aby maksymalnie wykorzystać serię, Obejrzyj wszystko. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy wideo w tej serii
*Nauka danych dla początkujących* to krótkie wprowadzenie do nauki danych w pięciu krótkich klipach wideo.

* Wideo 1: [5 pytań dotyczących analizy danych](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Wideo 2: czy Twoje dane są gotowe do analizy danych?
* Wideo 3: [zadawanie pytania, na które można odpowiedzieć, przy użyciu danych](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Wideo 4: [prognozowanie odpowiedzi za pomocą prostego modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Wideo 5: [kopiowanie pracy innych osób w celu przeprowadzenia analizy danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 minuty 18 sek.)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transkrypcja: czy Twoje dane są gotowe do analizy danych?
Witamy w "czy Twoje dane są gotowe do nauki o danych?". drugie wideo w serii *danych dla początkujących*.  

Zanim nauka danych będzie mogła dać Ci odpowiedzi, musisz udostępnić do programu wiele surowców o wysokiej jakości. Podobnie jak w przypadku Pizza, tym lepsze są składniki, które zaczynają się od, tym lepszy produkt końcowy. 

## <a name="criteria-for-data"></a>Kryteria dla danych
W nauce danych istnieją pewne składniki, które muszą być ściągane łącznie z:

* Niezbędne
* Połączone
* Zapewniają
* Wystarczająco dużo, aby można było korzystać z

## <a name="is-your-data-relevant"></a>Czy Twoje dane są istotne?
Dlatego pierwszy składnik — potrzebne są odpowiednie dane.

![Odpowiednie dane a dane nieistotne — Oceń dane](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Po lewej stronie tabela przedstawia poziom alkoholu krwiodawstwa o siedem osób przetestowanych poza paskiem Boston, czerwoną Batting Sox w swojej ostatniej grze oraz cenę mleka w najbliższym wygodnym sklepie.

Są to wszystkie doskonale wiarygodne dane. Tylko usterka nie ma znaczenia. Nie ma oczywistej relacji między tymi liczbami. Jeśli ktoś postanowił bieżącą cenę mleka i wybattingą czerwoną SOX, nie ma sposobu, aby można było odgadnąć zawartość alkoholu krwi.

Teraz spójrzmy na tabelę po prawej stronie. Tym razem należy mierzyć masę ciała każdej osoby oraz liczbę napojów, które miało.  Liczby w każdym wierszu są teraz istotne dla siebie nawzajem. Jeśli podałem swoją jednostkę masy i liczbę Margaritasów, możesz wprowadzić wartość w polu Moja zawartość alkoholu krwi.

## <a name="do-you-have-connected-data"></a>Czy masz połączone dane?
Następny składnik to połączone dane.

![Połączone dane a dane odłączone — kryteria danych, gotowe dane](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Poniżej znajdują się pewne istotne dane dotyczące jakości elementów do oceny: temperatury kratki, wagi Patty oraz klasyfikacji w lokalnym magazynie żywności. Zwróć uwagę na luki w tabeli po lewej stronie.

W większości zestawów danych brakuje niektórych wartości. Często mają one takie same przerwy, a istnieją sposoby ich obejścia. Ale jeśli brakuje zbyt dużej ilości danych, dane zaczynają wyglądać jak sery szwajcarskie.

Jeśli przyjrzyjsz się tabeli po lewej stronie, istnieje wiele brakujących danych, trudno jest wziąć pod uwagę dowolny rodzaj relacji między temperaturą kratownicy a pattyą. Ten przykład pokazuje rozłączone dane.

Tabela po prawej stronie jest pełna i kompletna — przykład połączonych danych.

## <a name="is-your-data-accurate"></a>Czy Twoje dane są dokładne?
Następny składnik jest dokładnością. Oto cztery cele do trafienia.

![Dokładne dane a niedokładne kryteria danych — dane](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Przyjrzyj się elementowi docelowemu w prawym górnym rogu. Istnieje ścisłe grupowanie w prawo wokół buhajów. Oczywiście jest to dokładne. Oddly w języku, w którym odbywa się analiza danych, wydajność na odpowiednim miejscu docelowym jest również dokładna.

Jeśli połączysz się z centrum tych strzałek, zobaczysz, że jest bardzo blisko oka buhajów. Strzałki są rozłożone w całym miejscu docelowym, dlatego są uznawane za niedokładne, ale są wyśrodkowane na całym świecie, dlatego są uważane za dokładne.

Teraz poszukaj w lewym górnym rogu. W tym miejscu strzałki trafią blisko siebie, ścisłe grupowanie. Są one dokładne, ale są niedokładne, ponieważ centrum jest wysunięte w oczy. Strzałki w lewym dolnym rogu są niedokładne i precyzyjne. Ta Archer wymaga większej liczby ćwiczeń.

## <a name="do-you-have-enough-data-to-work-with"></a>Czy masz wystarczające dane do pracy?
Na koniec składnik #4 są wystarczające dane.

![Czy masz wystarczające dane do analizy? Ocena danych](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Każdy punkt danych w tabeli należy traktować jako pociągnięcia pędzla. Jeśli masz tylko kilka z nich, malowanie może być rozmyte — trudno jest wiedzieć, co to jest.

Po dodaniu większej liczby pociągnięć pędzla rozpocznie się malowanie.

Gdy stanowią jedynie ułamek wystarczającą liczbę pociągnięć, wystarczy tylko tyle, aby podejmować pewne decyzje. Czy na pewno chcę odwiedzać? Wygląda na to, że wygląda na to, że jest to przejrzysta woda — tak, w której odbywa się wakacje.

Po dodaniu większej ilości danych obraz stanie się wyraźniejszy i można podjąć bardziej szczegółowe decyzje. Teraz możesz przeglądać trzy Hotele w lewym banku. Można zauważyć, że funkcje architektury są widoczne na pierwszym planie. W związku z tym możesz nawet wybrać opcję pozostawania w trzecim piętrie.

Dane, które są odpowiednie, połączone, dokładne i wystarczające, są dostępne dla wszystkich składników potrzebnych do analizy danych o wysokiej jakości.

Zapoznaj się z innymi czterema filmami wideo w *nauce danych dla początkujących* z Microsoft Azure Machine Learning Studio (klasyczne).

## <a name="next-steps"></a>Następne kroki
* [Wypróbuj pierwszy eksperyment nauki o danych z Machine Learning Studio (klasyczny)](create-experiment.md)
* [Zapoznaj się z wprowadzeniem do Machine Learning na Microsoft Azure](../overview-what-is-azure-ml.md)