---
title: 'ML Studio (klasyczny): Nauka danych dla początkujących — Azure'
description: Nauka danych dla początkujących to podstawowe pojęcia dotyczące 5 krótkich filmów wideo, począwszy od 5 pytań dotyczących analizy danych. Z Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: e9bbadc95be3479da82d91e9a4a4f712fbc32c57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520395"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Wideo 1: analiza danych dla początkujących — 5 pytań, na które analiza danych daje odpowiedzi

Zapoznaj się z szybkim wprowadzeniem do nauki danych z *nauki danych dla początkujących* w pięciu krótkich klipach wideo od najlepszych analityków danych. Te filmy wideo są podstawowe, ale użyteczne, niezależnie od tego, czy interesują Cię dane, czy pracujesz z analitykami danych.

To pierwsze wideo zawiera informacje o rodzajach pytań, na które mogą odpowiedzieć dane. Aby maksymalnie wykorzystać serię, Obejrzyj wszystko. [Przejdź do listy filmów wideo](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy wideo w tej serii
Analiza *danych dla początkujących* to krótkie wprowadzenie do analizy danych, które zajmują około 25 minut. Wypróbuj wszystkie pięć filmów wideo:

* Wideo 1: odpowiedzi na pytania naukowe dotyczące danych
* Wideo 2: [czy Twoje dane są gotowe do analizy danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 minuty 56 s)*
* Wideo 3: [zadawanie pytania, na które można odpowiedzieć, przy użyciu danych](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Wideo 4: [prognozowanie odpowiedzi za pomocą prostego modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Wideo 5: [kopiowanie pracy innych osób w celu przeprowadzenia analizy danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 minuty 18 sek.)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Transkrypcja: 5 pytań odpowiedzi naukowych
Witaj! Witamy w serii filmów wideo *nauka danych dla początkujących*.

Analiza danych może być zastraszanie, więc wprowadzamy podstawowe informacje bez żadnych równań lub programowania komputerowego żargon.

W pierwszym filmie wideo będziemy mówić o "5 pytaniach dotyczących analizy danych".

Nauka danych używa liczb i nazw (znanych również jako kategorie lub etykiety) do przewidywania odpowiedzi na pytania.

Może się to zdarzyć, ale *istnieje tylko pięć pytań dotyczących odpowiedzi na dane*:

* Czy to jest A czy B?
* Czy to brzmienia?
* Ile lub — ile?
* Jak jest to zorganizowane?
* Co mam robić dalej?

Każdy z tych pytań jest odpowiedzią przez osobną rodzinę metod uczenia maszynowego, zwanych algorytmami.

Warto traktować algorytm jako przepis i dane jako składniki. Algorytm informuje, jak łączyć i mieszać dane w celu uzyskania odpowiedzi. Komputery są podobne do programu Blender. Większość pracy z algorytmem jest dla Ciebie większa i bardzo szybka.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Pytanie 1: czy to jest A czy B? używa algorytmów klasyfikacji
Zacznijmy od pytania: czy to jest A czy B?

![Algorytmy klasyfikacji: czy to A lub B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Ta rodzina algorytmów jest nazywana klasyfikacją dwóch klas.

Jest to przydatne w przypadku każdego pytania, które ma tylko dwie możliwe odpowiedzi.

Na przykład:

* Czy ten stan kończy się niepowodzeniem w ciągu następnych 1 000 kilometrów: tak lub nie?
* Co przynosi klientom więcej klientów: $5 kupon lub rabat w wysokości 25%?

To pytanie może być również zwrotne w celu uwzględnienia więcej niż dwóch opcji: to A lub B lub C lub D, itp.?  Nazywa się to klasyfikacją wieloklasową i jest przydatna, jeśli masz kilka tysięcy — możliwych odpowiedzi. Klasyfikacja wieloklasowa wybiera najprawdopodobniej jedną.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Pytanie 2: czy to brzmienia? używa algorytmów wykrywania anomalii
Odpowiedzi na następne pytania można znaleźć na stronie: czy to brzmienia? Na to pytanie jest udzielona rodzina algorytmów o nazwie wykrywanie anomalii.

![Algorytmy wykrywania anomalii: czy to brzmienia?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Jeśli masz kartę kredytową, już korzystasz z wykrywania anomalii. Twoja firma z kartami kredytowymi analizuje Twoje wzorce zakupów, dzięki czemu mogą otrzymywać alerty o możliwym oszustwie. Opłaty za "brzmienia" mogą być zakupami w sklepie, w którym nie są zwykle kupowane ani kupowane nienormalnie.

To pytanie może być przydatne na wiele sposobów. Przykład:

* Jeśli masz samochód z miernikami ciśnienia, warto wiedzieć: czy ten miernik ciśnienia jest czytelny?
* Jeśli monitorujesz Internet, warto wiedzieć: czy ten komunikat jest typowy w Internecie?

Flagi wykrywania anomalii nieoczekiwane lub nietypowe zdarzenia lub zachowania. Zawiera wskazówki, gdzie szukać problemów.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Pytanie 3: ile? lub ile? używa algorytmów regresji
Uczenie maszynowe może również przewidzieć, jak dużo? lub ile? Rodzina algorytmów, która odpowiada na to pytanie, nosi nazwę regresji.

![Algorytmy regresji: ile? lub ile?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Algorytmy regresji tworzą przewidywania liczbowe, takie jak:

* Co będzie miało przyszłe wtorku temperatury?  
* Co to jest sprzedaż w czwartym kwartale?

Pomagają odpowiedzieć na pytania, które pytają o liczbę.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Pytanie 4: jak to jest zorganizowane? używa algorytmów klastrowania
Teraz ostatnie dwa pytania są nieco bardziej zaawansowane.

Czasami chcesz zrozumieć strukturę zestawu danych — w jaki sposób jest to zorganizowane? Na potrzeby tego pytania nie masz przykładów, dla których znasz już wyniki.

Istnieje wiele sposobów teasenia struktury danych. Jednym z metod jest klastrowanie. W celu łatwiejszej interpretacji dane są oddzielane do naturalnego "clumps". W przypadku klastrowania nie ma żadnej prawej odpowiedzi.

![Algorytmy klastrowania: jak jest to zorganizowane?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Typowe przykłady pytań dotyczących klastrowania:

* Które osoby przeglądające lubią te same typy filmów?
* Które modele drukarek nie działają w ten sam sposób?

Zrozumienie, jak zorganizowane są dane, można lepiej zrozumieć i przewidzieć zachowania oraz zdarzenia.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Pytanie 5: co mam zrobić? używa algorytmów uczenia wzmocnienie
Ostatnie pytanie — co mam zrobić teraz? — używa rodziny algorytmów o nazwie nauka wzmacniania.

Uczenie wzmacniające było inspiracją, jak mózgi szczurów i ludzi reagują na kara i nagrody. Te algorytmy uczyją się od wyników i decydują o następnej akcji.

Zwykle uczenie wzmacniające jest dobrym sposobem dla zautomatyzowanych systemów, które muszą podejmować wiele małych decyzji bez wytycznych ludzkich.

![Algorytmy uczenia wzmacniania: co należy zrobić dalej?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Pytania, na które odpowiedzi, są zawsze związane z tym, jakie działania powinny być wykonywane — zwykle przez maszynę lub robota. Oto przykłady:

* Jeśli mam system kontroli temperatury dla domu: Dostosuj temperaturę lub pozostaw to miejsce, w którym jest?  
* Jeśli jestem samochodem samodzielnym: w żółtym oświetleniu, hamulcu lub przyspieszeniu?  
* W przypadku próżniowej próżni: Zachowaj próżnię lub Wróć do stacji ładowania?

Algorytmy uczenia wzmacniające zbierają dane w miarę ich działania, uczenie się z wersji próbnej i błędu.

W ten sposób można odpowiedzieć na 5 pytań dotyczących danych.

## <a name="next-steps"></a>Następne kroki
* [Wypróbuj pierwszy eksperyment nauki o danych z Machine Learning Studio (klasyczny)](create-experiment.md)
* [Zapoznaj się z wprowadzeniem do Machine Learning na Microsoft Azure](../overview-what-is-azure-ml.md)