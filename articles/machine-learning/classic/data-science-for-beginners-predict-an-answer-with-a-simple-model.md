---
title: 'ML Studio (klasyczny): przewidywania odpowiedzi z modelami regresji — Azure'
description: Jak utworzyć prosty model regresji, aby przewidzieć cenę w nauce danych dla początkujących wideo 4. Obejmuje regresję liniową z danymi docelowymi.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: c6d78b10b98abe33744673459b3d31d8941aac89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520412"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Prognozowanie odpowiedzi za pomocą prostego modelu


## <a name="video-4-data-science-for-beginners-series"></a>Wideo 4: Analiza danych dla początkujących serii
Dowiedz się, jak utworzyć prosty model regresji, aby przewidzieć cenę rombu w nauce danych dla początkujących filmów wideo 4. Narysujemy model regresji z danymi docelowymi.

Aby maksymalnie wykorzystać serię, Obejrzyj wszystko. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy wideo w tej serii
*Nauka danych dla początkujących* to krótkie wprowadzenie do nauki danych w pięciu krótkich klipach wideo.

* Wideo 1: [5 pytań dotyczących analizy danych](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Wideo 2: [czy Twoje dane są gotowe do analizy danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 minuty 56 s)*
* Wideo 3: [zadawanie pytania, na które można odpowiedzieć, przy użyciu danych](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Wideo 4: prognozowanie odpowiedzi za pomocą prostego modelu
* Wideo 5: [kopiowanie pracy innych osób w celu przeprowadzenia analizy danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 minuty 18 sek.)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transkrypcja: przewidywanie odpowiedzi przy użyciu prostego modelu
Witamy w czwartym filmie wideo w serii "analizy danych dla początkujących". W tym kroku utworzymy prosty model i przetworzymy prognozę.

*Model* to uproszczony scenariusz dotyczący naszych danych. Pokażę Ci, co oznaczamy.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Zbierz odpowiednie, dokładne, połączone i wystarczające dane
Załóżmy, że chcemy kupować dla rombu. Mam pierścień, który należy do mojego babcia z ustawieniem dla rombu w wysokości 1,35 w wysokości 1 i chcę uzyskać pomysł, jaki będzie koszt. Chcę korzystać z Notatnika i pióra w sklepie biżuterii i zapisać ceny wszystkich diamentów w przypadku i ile ważą w Carats. Począwszy od pierwszego rombu — jest to 1,01 Carats i $7 366.

Teraz przechodzą i wykonuję te czynności dla wszystkich innych diamentów w sklepie.

![Kolumny danych rombu](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Zwróć uwagę, że lista zawiera dwie kolumny. Każda kolumna ma inną grubość atrybutu w Carats i cenie — a każdy wiersz jest pojedynczym punktem danych, który reprezentuje pojedynczy romb.

W tym miejscu utworzyliśmy mały zestaw danych — tabelę. Zwróć uwagę, że spełnia on kryteria jakości:

* Dane są **istotne** — waga jest ostatecznie związana z ceną
* Jest to **dokładne** — sprawdzono podwójne ceny, które zapisujemy
* Jest on **połączony** — nie ma pustych miejsc w żadnej z tych kolumn
* I, jak zobaczymy, **wystarczy uzyskać wystarczającą ilość** danych, aby odpowiedzieć na nasze pytanie

## <a name="ask-a-sharp-question"></a>Podawanie ostrych pytań
Teraz zajmiemy się naszymi kwestiami: "jak dużo będzie kosztować zakup rombu 1,35 w karatach"?

Nasza lista nie zawiera diamentów w wysokości 1,35, dlatego będziemy musieli użyć reszty naszych danych w celu uzyskania odpowiedzi na pytanie.

## <a name="plot-the-existing-data"></a>Wykreśl istniejące dane
Pierwszy element, który zrobimy, narysuje poziomą linię liczbową o nazwie oś, aby wykresować wagi. Zakres wag wynosi od 0 do 2, więc narysujemy linię, która obejmuje ten zakres, i umieść Takty dla każdego półrocza.

Następnie narysujemy oś pionową, aby zarejestrować cenę i połączyć ją z osią wagi poziomej. Będzie to w jednostkach dolarów. Teraz mamy zestaw osi współrzędnych.

![Osie wagowe i cenowe](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Teraz zajmiemy się tymi danymi i przełączymy je do *wykresu punktowego*. Jest to doskonały sposób wizualizacji liczbowych zestawów danych.

Dla pierwszego punktu danych Eyeball pionową linię o 1,01 Carats. Następnie Eyeball linię poziomą o $7 366. Gdzie się znajdują, narysujemy kropkę. Reprezentuje on pierwszy romb.

Teraz przejdziemy przez każdy romb na tej liście i wykonamy tę samą czynność. W tym momencie otrzymujesz następujące informacje: wiele kropek, po których jeden dla każdego rombu.

![Wykres punktowy](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Rysowanie modelu za pomocą punktów danych
Teraz, Jeśli zobaczysz kropki i Squint, kolekcja wygląda jak w przypadku systemu plików FAT. Możemy skorzystać z naszego znacznika i narysować prostą linię.

Rysując linię, utworzyliśmy *model*. Pomyśl o tym, jak to zrobić w świecie rzeczywistym i uproszczony jego wersję. Teraz kreskówki jest niewłaściwy — wiersz nie przechodzi przez wszystkie punkty danych. Jest to przydatne uproszczenie.

![Liniowa regresja liniowa](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Fakt, że wszystkie kropki nie przechodzą dokładnie przez wiersz, jest prawidłowy. Analityki danych wyjaśnią to, mówiąc, że istnieje model, który jest wierszem, a następnie każda kropka ma z nim *szum* lub *wariancję* . Istnieje podstawowe powiązanie, a następnie istnieje Gritty, Real świecie, który dodaje szum i niepewność.

Ponieważ próbujemy odpowiedzieć na pytanie, *ile?* jest to tzw. *regresja*. I ponieważ korzystamy z prostej linii, jest to *regresja liniowa*.

## <a name="use-the-model-to-find-the-answer"></a>Użyj modelu, aby znaleźć odpowiedź
Teraz mamy już model i prosimy o to, abyśmy z naszym pytaniem: ile będzie kosztowały diamenty w 1,35 Karat?

Aby odpowiedzieć na nasze pytanie, Eyeball 1,35 Carats i Rysuj linię pionową. Gdy przecina linię modelu, Eyeball linię poziomą do osi dolara. Trafień w prawo o 10 000. Wysięgnik! To jest odpowiedź: koszty diamentów w 1,35 w karatach o $10 000.

![Znajdź odpowiedź w modelu](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Utwórz Interwał ufności
Jest to naturalne, aby zastanawiać się, jak precyzyjne jest to prognozowanie. Warto dowiedzieć się, czy z poziomu rombu 1,35 w karatach jest bardzo blisko $10 000, czy też do większej lub mniejszej ilości. Aby to zrobić, Narysujmy kopertę wokół linii regresji, która zawiera większość kropek. Ta koperta jest nazywana naszym *interwałem ufności*: mamy pewność, że ceny mieszczą się w tej kopercie, ponieważ w przeszłości większość z nich ma. Możemy rysować dwa więcej linii poziomych, od których linia 1,35 w karatach przecina górną i dolną część tej koperty.

![Interwał ufności](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Teraz możemy powiedzieć coś o naszym przedziale zaufania: możemy powiedzieć, że cena w karo 1,35 w karatach jest około $10 000, ale może być niska jako $8 000 i może być tak wysoka jak $12 000.

## <a name="were-done-with-no-math-or-computers"></a>Gotowe, bez żadnych zapisów matematycznych i komputerów
Przeprowadzimy Cię przez analityków danych i udałomy się to zrobić po prostu przez rysowanie:

* Prosimy o pytanie, że możemy odpowiedzieć na dane
* Utworzyliśmy *model* przy użyciu *regresji liniowej*
* Wprowadziliśmy *prognozę* z *interwałem pewności*

Nie używamy jednak obliczeń matematycznych ani komputerów.

Teraz, jeśli mamy więcej informacji, takich jak...

* Wytnij romb
* Wariacje kolorów (w jaki sposób zamykany jest romb)
* Liczba dołączeń w karo

... następnie będziemy mieć więcej kolumn. W takim przypadku matematyczne staną się pomocne. Jeśli masz więcej niż dwie kolumny, trudno jest rysować kropki na papierze. Zapis matematyczny pozwala dopasować ten wiersz lub płaszczyznę do danych bardzo dobrze.

Ponadto, jeśli zamiast tylko kilku diamentów, mamy 2000 lub 2 000 000, a następnie można wykonać te działania znacznie szybciej z komputerem.

Dzisiaj zawarto informacje na temat wykonywania regresji liniowej oraz przewidywania przy użyciu danych.

Upewnij się, że zapoznaj się z innymi filmami wideo w obszarze "Analiza danych dla początkujących" z Microsoft Azure Machine Learning Studio (klasyczne).

## <a name="next-steps"></a>Następne kroki
* [Wypróbuj pierwszy eksperyment nauki o danych z Machine Learning Studio (klasyczny)](create-experiment.md)
* [Zapoznaj się z wprowadzeniem do Machine Learning na Microsoft Azure](../overview-what-is-azure-ml.md)