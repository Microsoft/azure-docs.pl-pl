---
title: Użycie metody oceny w trybie offline — Personalizacja
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak za pomocą oceny w trybie offline mierzyć efektywność aplikacji i analizować pętlę uczenia.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 627f511bb12c16c8f54935d1f782cb7c2c962163
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132759"
---
# <a name="offline-evaluation"></a>Ocena w trybie offline

Ocena w trybie offline to metoda, która umożliwia testowanie i ocenianie skuteczności usługi personalizacji bez zmiany kodu lub wpływu na środowisko użytkownika. W ocenie w trybie offline są używane wcześniejsze dane wysyłane z aplikacji do interfejsów API rangi i nagradzania w celu porównania, jak różne Range zostały wykonane.

Ocena w trybie offline jest wykonywana w zakresie dat. Zakres może kończyć się najpóźniej jako bieżący czas. Początek zakresu nie może być większy niż liczba dni określona do [przechowywania danych](how-to-settings.md).

Ocena w trybie offline może pomóc odpowiedzieć na następujące pytania:

* Jak obowiązuje stopień personalizacji w celu pomyślnego personalizacji?
    * Jakie są średnie nagrody osiągnięte przez personalizację usługi Machine Learning w trybie online?
    * Jak program personalizuje porównuje ze skutecznością działania aplikacji?
    * Jaka byłaby porównywalna skuteczność losowego wyboru dla personalizacji?
    * Co byłoby porównywalną skutecznością różnych zasad nauki określonych ręcznie?
* Które funkcje kontekstu przyczyniają się do pomyślnego personalizacji?
* Które funkcje działań mają wpływ na więcej lub mniej na pomyślne personalizację?

Ponadto można użyć oceny w trybie offline w celu odnalezienia bardziej zoptymalizowanych zasad uczenia, których można użyć w celu poprawienia wyników w przyszłości.

Oceny w trybie offline nie zapewniają wskazówek dotyczących procentu zdarzeń do użycia podczas eksploracji.

## <a name="prerequisites-for-offline-evaluation"></a>Wymagania wstępne dotyczące oceny w trybie offline

Poniżej przedstawiono ważne zagadnienia dotyczące oceny reprezentatywnej w trybie offline:

* Ma wystarczającą ilość danych. Zalecana minimalna wartość to co najmniej 50 000 zdarzeń.
* Zbieraj dane z okresów ze reprezentatywnym zachowaniem i ruchem użytkowników.

## <a name="discovering-the-optimized-learning-policy"></a>Odnajdywanie zoptymalizowanych zasad uczenia

Personalizowanie może korzystać z procesu oceny w trybie offline w celu automatycznego odnajdywania bardziej optymalnych zasad uczenia.

Po przeprowadzeniu oceny w trybie offline można zobaczyć porównywalną efektywność personalizowania przy użyciu nowych zasad w porównaniu z bieżącymi zasadami online. Następnie można zastosować te zasady uczenia, aby działały natychmiast w personalizacji, pobierając je i przekazując w panelu modele i zasady. Można go również pobrać do przyszłej analizy lub użycia.

Bieżące zasady uwzględnione w ocenie:

| Ustawienia uczenia | Przeznaczenie|
|--|--|
|**Zasady online**| Bieżące zasady uczenia używane w programie Personalizacja |
|**Punkt odniesienia**|Wartość domyślna aplikacji (zgodnie z pierwszą akcją wysłaną w wywołaniach rangi)|
|**Zasady losowe**|Zachowanie rangi urojonej, które zawsze zwraca losowo wybór akcji z dostarczonych.|
|**Zasady niestandardowe**|Dodatkowe zasady uczenia zostały przekazane podczas uruchamiania oceny.|
|**Zoptymalizowane zasady**|Jeśli Ocena została rozpoczęta z opcją odnalezienia zoptymalizowanych zasad, zostanie również porównana i będzie można ją pobrać lub wprowadzić zasady uczenia online, zastępując bieżące.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Zrozumienie znaczenia wyników oceny w trybie offline

Po uruchomieniu oceny w trybie offline bardzo ważne jest przeanalizowanie _granic ufności_ wyników. Jeśli są one szerokie, oznacza to, że aplikacja nie otrzymała wystarczającej ilości danych, aby oszacować nagrody były precyzyjne lub znaczące. Ponieważ system zbiera więcej danych, a oceny w trybie offline są wykonywane przez dłuższy czas, interwały zaufania stają się węższe.

## <a name="how-offline-evaluations-are-done"></a>Jak są wykonywane oceny w trybie offline

Oceny w trybie offline są wykonywane przy użyciu metody o nazwie **Counterfactual Evaluation**.

Personalizacja jest tworzona w oparciu o założenie, że zachowanie użytkowników (i w ten sposób jest niemożliwy do przewidzenia z mocą wsteczną) (personalizowanie nie może wiedzieć, co się stało, jeśli użytkownik wykazał coś innego niż zobaczysz) i dowiedzieć się, jak to nastąpi.

Jest to proces koncepcyjny używany do oceny:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

Ocena w trybie offline używa tylko zaobserwowanego zachowania użytkownika. Ten proces odrzuca duże ilości danych, zwłaszcza jeśli aplikacja wykonuje wywołania o dużej liczbie akcji.


## <a name="evaluation-of-features"></a>Ocena funkcji

Oceny w trybie offline mogą dostarczyć informacji o tym, ile określonych funkcji dla akcji lub kontekstu jest wag w celu uzyskania wyższych korzyści. Informacje są obliczane przy użyciu oceny względem danego okresu i danych oraz mogą się różnić w zależności od czasu.

Zalecamy zapoznanie się z ocenami funkcji i pytaniami:

* Jakie inne, dodatkowe funkcje mogą być zawarte w aplikacji lub systemie wraz z bardziej wydajnymi liniami?
* Jakie funkcje można usunąć ze względu na niską efektywność? Funkcje niskiej efektywności zwiększają _szum_ do uczenia maszynowego.
* Czy istnieją jakieś funkcje, które są przypadkowo uwzględniane? Przykłady są następujące: informacje identyfikowane przez użytkownika, zduplikowane identyfikatory itp.
* Czy istnieją jakieś niepożądane funkcje, które nie powinny być używane do personalizacji ze względu na kwestie prawne lub osoby odpowiedzialne za użycie? Czy istnieją funkcje, które mogą być serwerem proxy (czyli ściśle dublowane lub skorelowane z) niepożądanymi funkcjami?


## <a name="next-steps"></a>Następne kroki

[Konfigurowanie personalizacji](how-to-settings.md) 
 [Uruchamianie ocen w trybie offline](how-to-offline-evaluation.md) [Informacje na temat działania narzędzia Personalizacja](how-personalizer-works.md)
