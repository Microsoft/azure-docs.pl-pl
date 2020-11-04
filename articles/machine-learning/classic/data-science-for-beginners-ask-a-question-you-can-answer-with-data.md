---
title: Pytanie o dane pytania może odpowiedzieć na ML Studio (klasyczny) — Azure
description: Dowiedz się, jak sformułować szczegółowe pytania dotyczące nauki danych w nauce danych dla początkujących filmów wideo 3. Zawiera porównanie pytań dotyczących klasyfikacji i regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: dbac7c8818300ffb09959969e6b79f949177f457
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307664"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Zadawanie pytań, na które można odpowiedzieć za pomocą danych
## <a name="video-3-data-science-for-beginners-series"></a>Wideo 3: Analiza danych dla początkujących serii
Dowiedz się, jak sformułować problem z analizą danych na pytanie w nauce danych dla początkujących filmów wideo 3. To wideo zawiera porównanie pytań dotyczących algorytmów klasyfikacji i regresji.

Aby maksymalnie wykorzystać serię, Obejrzyj wszystko. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy wideo w tej serii
*Nauka danych dla początkujących* to krótkie wprowadzenie do nauki danych w pięciu krótkich klipach wideo.

* Wideo 1: [5 pytań dotyczących analizy danych](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Wideo 2: [czy Twoje dane są gotowe do analizy danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 minuty 56 s)*
* Wideo 3: zadawanie pytania można odpowiedzieć na dane
* Wideo 4: [prognozowanie odpowiedzi za pomocą prostego modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Wideo 5: [kopiowanie pracy innych osób w celu przeprowadzenia analizy danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 minuty 18 sek.)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transkrypcja: zadawanie pytania, na które można odpowiedzieć z danymi
Witamy w trzecim filmie wideo w serii "Analiza danych dla początkujących".  

W tym temacie uzyskasz kilka porad dotyczących formułowania pytania, które można odpowiedzieć na dane.

Możesz uzyskać więcej informacji o tym filmie wideo, jeśli najpierw Oglądasz dwa wcześniejsze filmy wideo w tej serii: "5 pytań dotyczących analizy danych może odpowiedzieć na pytanie" i "czy Twoje dane są gotowe do nauki o danych?".

## <a name="ask-a-sharp-question"></a>Podawanie ostrych pytań
Porozmawiamy o tym, jak nauka danych jest procesem używania nazw (nazywanych również kategoriami lub etykietami) i numerami w celu przewidywania odpowiedzi na pytanie. Ale nie może to być tylko pytanie; musi to być *ostre pytanie.*

Nie trzeba otrzymywać odpowiedzi na pytanie niejasne z nazwą lub cyfrą. Konieczne jest wyraźne pytanie.

Wyobraź sobie, że znaleziono lampę magiczną z Genie, który będzie udzielić wiarygodnych gwarancji odpowiedzi na pytania. Ale jest to Mischievous Genie, który podejmie próbę uzyskania odpowiedzi jako niejasnej i mylącej, ponieważ mogą one się odejść. Chcesz przypiąć je z pytaniem, aby nie było możliwe, ale poinformuj Cię o tym, co chcesz wiedzieć.

Jeśli zadasz pytanie niejasne, np. "co się dzieje z moim giełdą?", Genie może odpowiedzieć, "cena zostanie zmieniona". To jest odpowiedź realistyczne, ale nie jest bardzo przydatna.

Ale jeśli chcesz zadać szczególną kwestię, na przykład "jakie ceny sprzedaży na moim giełdie będą naliczane w następnym tygodniu?", Genie nie może pomóc, ale uzyskać konkretnej odpowiedzi i przewidzieć cenę sprzedaży.

## <a name="examples-of-your-answer-target-data"></a>Przykłady odpowiedzi: dane docelowe
Po sformułowaniu pytania Sprawdź, czy w danych znajdują się przykłady odpowiedzi.

Jeśli chodzi o pytanie "jakie ceny sprzedaży na moim giełdie będą się znajdować w następnym tygodniu"? Następnie musimy upewnić się, że nasze dane obejmują historię cen giełdowych.

Jeśli nasze pytanie dotyczy "którego samochodu w mojej flotie nie uda się w pierwszej kolejności?" Następnie musimy upewnić się, że nasze dane zawierają informacje o poprzednich błędach.

![Przykładowe dane docelowe — przykłady odpowiedzi. Formułowanie pytania dotyczącego analizy danych.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Te przykłady odpowiedzi są nazywane elementem docelowym. Celem jest to, czego próbujemy przewidzieć przyszłe punkty danych, niezależnie od tego, czy jest to kategoria czy liczba.

Jeśli nie masz żadnych danych docelowych, musisz uzyskać pewne. Nie będziesz w stanie udzielić odpowiedzi na Twoje pytanie.

## <a name="reformulate-your-question"></a>Przeformułowanie pytania
Czasami możesz wyrazić swoje pytanie, aby uzyskać bardziej przydatną odpowiedź.

Pytanie "czy ten punkt danych A lub B?" przewiduje kategorię (lub nazwę lub etykietę) elementu. Aby odpowiedzieć na nie, używamy *algorytmu klasyfikacji*.

Pytanie "jak dużo?" lub "jak wiele?" przewidywana jest kwota. Aby odpowiedzieć, używamy *algorytmu regresji*.

Aby zobaczyć, jak możemy to przekształcić, przyjrzyjmy się pytaniu "w jakim scenariuszu wiadomości są najbardziej interesujące dla tego czytnika?". Pyta do prognozowania pojedynczego wyboru z wielu możliwości — innymi słowy "to jest A lub B lub C lub D?" -i użyj algorytmu klasyfikacji.

Jednak ten problem może być łatwiejszy w odpowiedzi na to, czy będzie się on znajdować na tej liście w tym czytelniku? Teraz możesz nadać każdemu artykułowi wartość numeryczną, a następnie łatwo zidentyfikować artykuł o najwyższej ocenie. To jest sformułowanie pytania klasyfikacji do pytania regresji lub tego, ile?

![Przeformułowanie pytania. Pytanie klasyfikacji a pytanie regresji.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Pytanie, w jaki sposób pytasz, że algorytm może dać odpowiedź.

Zobaczysz, że niektóre rodziny algorytmów, podobnie jak te, w naszym przykładzie historii wiadomości — są ściśle powiązane. Możesz sformułować swoje pytanie, aby użyć algorytmu, który zapewnia najbardziej przydatną odpowiedź.

Jednak najważniejsze pytanie — pytanie, na które można odpowiedzieć na dane. Upewnij się, że masz odpowiednie dane, aby je odpowiedzieć.

Porozmawiamy o niektórych podstawowych zasadach zadawania pytania, na które można odpowiedzieć za pomocą danych.

Upewnij się, że zapoznaj się z innymi filmami wideo w obszarze "Analiza danych dla początkujących" z Microsoft Azure Machine Learning Studio (klasyczne).

## <a name="next-steps"></a>Następne kroki
* [Wypróbuj pierwszy eksperyment nauki o danych z Machine Learning Studio (klasyczny)](create-experiment.md)
* [Zapoznaj się z wprowadzeniem do Machine Learning na Microsoft Azure](../overview-what-is-azure-ml.md)