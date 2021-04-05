---
title: Unikaj nadmontowania & niezrównoważone dane za pomocą AutoML
titleSuffix: Azure Machine Learning
description: Zidentyfikuj popularne pułapek modeli ML i zarządzaj nimi za pomocą zautomatyzowanych rozwiązań do uczenia maszynowego Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: ba85bda1e322d3efd467527b48bd4cd90eb7ce8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96922619"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Zapobiegaj przestępowaniu i niezrównoważonym danych za pomocą zautomatyzowanego uczenia maszynowego

W przypadku kompilowania modeli uczenia maszynowego często występują nadmierne dopasowanie i niezrównoważone dane. Domyślnie Automatyczne Uczenie maszynowe Azure Machine Learning zapewnia wykresy i metryki ułatwiające zidentyfikowanie tych zagrożeń, a także implementuje najlepsze rozwiązania, aby pomóc je ograniczyć. 

## <a name="identify-over-fitting"></a>Identyfikowanie nadmiernego dopasowania

Nadmierne dopasowanie do uczenia maszynowego występuje, gdy model dopasowuje dane szkoleniowe, a w efekcie nie może precyzyjnie przewidzieć niewidocznych danych testowych. Innymi słowy, model ma po prostu Memorized określonych wzorców i szumów w danych szkoleniowych, ale nie jest wystarczająco elastyczny, aby wykonywać przewidywania dotyczące rzeczywistych danych.

Należy wziąć pod uwagę następujące przeszkolone modele i ich odpowiadające im uczenia i testy.

| Model | Dokładność szkolenia | Dokładność testu |
|-------|----------------|---------------|
| A | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Biorąc pod uwagę model **A**, istnieje powszechna koncepcja, że jeśli dokładność testu dla niewidocznych danych jest mniejsza niż dokładność szkolenia, model jest nadmiernie dopasowany. Jednak dokładność testu powinna być zawsze mniejsza od dokładności szkolenia, a rozróżnienie dla nadmiernego dopasowania a odpowiednio dopasowane, przydaje się do *znacznie* mniejszej dokładności. 

**W przypadku** porównywania modeli a i **B** model **a** jest lepszym modelem, ponieważ ma wyższą dokładność testu, a chociaż dokładność testu jest nieco mniejsza o 95%, nie jest to znacząca różnica, która sugeruje nadmierne dopasowanie. Nie możesz wybrać modelu **B** po prostu, ponieważ ścisłe nauczenie i testy są bliżej siebie.

Model **C** reprezentuje wyraźny przypadek nadmiernego dopasowania; dokładność uczenia jest bardzo wysoka, ale dokładność testu nie jest w dowolnym miejscu w dużej okolicy. To rozróżnienie jest subiektywne, ale pochodzi z wiedzy o problemie i danych oraz o tym, jakie wielkości błędów są dopuszczalne.

## <a name="prevent-over-fitting"></a>Zapobiegaj nadmiernemu dopasowaniu

W większości przypadków egregious model z nadmiernym dopasowaniem zakłada, że kombinacje wartości funkcji widoczne podczas uczenia będą zawsze powodowały dokładne te same dane wyjściowe dla elementu docelowego.

Najlepszym sposobem, aby uniknąć nadmiernego dopasowania, jest przestrzeganie najlepszych rozwiązań z zakresu, w tym:

* Użycie większej ilości danych szkoleniowych i wyeliminowanie statystycznej różnicy
* Zapobieganie wyciekowi docelowemu
* Korzystanie z mniejszej liczby funkcji
* **Optymalizacja pod kątem i przeparametrem**
* **Ograniczenia złożoności modelu**
* **Krzyżowe sprawdzanie poprawności**

W kontekście zautomatyzowanej ML, pierwsze trzy elementy powyżej są **implementacją najlepszych** rozwiązań. Ostatnie trzy elementy pogrubione to najlepsze w obsłużeniu **Automatyczne implementacje** , które domyślnie chronią przed nadmiernym dopasowaniem. W obszarze Ustawienia inne niż automatyczna ML warto uzyskać wszystkie sześć najlepszych rozwiązań, aby uniknąć nadmiernego dopasowania modeli.

### <a name="best-practices-you-implement"></a>Najlepsze rozwiązania zaimplementowane

Użycie **większej ilości danych** jest najprostszym i najlepszym sposobem, aby zapobiec nadmiernemu dopasowywaniu, a w miarę wzrostu zwiększy się dokładność. W przypadku korzystania z większej ilości danych stać się trudniejsze dla modelu, aby znają dokładne wzorce i wymusić osiągnięcie rozwiązań, które są bardziej elastyczne, aby pomieścić więcej warunków. Ważne jest również, aby rozpoznawać **odchylenie statystyczne**, aby upewnić się, że dane szkoleniowe nie obejmują izolowanych wzorców, które nie znajdują się w danych prognozowania na żywo. Ten scenariusz może być trudny do rozwiązania, ponieważ nie można nadmiernie zamontować między zestawem danych i zestawów testów, ale może istnieć nadmierne dopasowanie w porównaniu z danymi testu na żywo.

**Docelowy wyciek** jest podobnym problemem, gdzie nie można zobaczyć nadmiernego dopasowania między zestawami uczenia i testu, ale nie jest wyświetlany w czasie przewidywania. Docelowy wyciek występuje, gdy model "oszustwo" podczas uczenia się przez uzyskanie dostępu do danych, które nie powinny być zwykle w czasie przewidywania. Na przykład jeśli Twój problem jest przewidywany w poniedziałek, co cena asortymentu będzie w piątek, ale jedna z funkcji przypadkowo dodaliśmy dane z czwartki, które byłyby dane, model nie będzie miał w czasie przewidywania, ponieważ nie będzie widoczny w przyszłości. Wyciek docelowy jest łatwa do odrzucania, ale często charakteryzuje się dużą dokładnością dla danego problemu. Jeśli próbujesz przewidzieć cenę giełdową i przeszkolony model z dokładnością do 95%, prawdopodobnie docelowy wyciek należy do Twoich funkcji.

**Usuwanie funkcji** może być również pomocne w przypadku nadmiernego dopasowania, uniemożliwiając modelowi posiadanie zbyt wielu pól do użycia w celu znają określonych wzorców, co sprawia, że jest to bardziej elastyczne. Może być trudne zmierzenie ilości, ale jeśli można usunąć funkcje i zachować taką samą dokładność, prawdopodobnie model jest bardziej elastyczny i zmniejsza ryzyko nadmiernego dopasowania.

### <a name="best-practices-automated-ml-implements"></a>Implementacje zautomatyzowanych najlepszych rozwiązań

**Uregulowanie** to proces minimalizowania funkcji kosztu w celu karania złożonych i nadmiernie dopasowanych modeli. Istnieją różne rodzaje funkcji uregulowania, ale ogólnie rzecz biorąc, wszyscy mają kara rozmiar, wariancję i złożoność modelu. Zautomatyzowana ML używa L1 (Lasso), L2 (pierścieni) i ElasticNet (L1 i L2 jednocześnie) w różnych kombinacjach z różnymi ustawieniami parametrów modelu, które sterują nadmiernym dopasowywaniem. W prostych terminach, zautomatyzowany ML będzie różnicować, jak dużo modelu jest regulowany, i wybrać najlepszy wynik.

Zautomatyzowanej ML również implementuje jawne **ograniczenia złożoności modelu** , aby zapobiec nadmiernemu dopasowywaniu. W większości przypadków ta implementacja jest przeznaczona dla algorytmów drzewa lub lasu decyzyjnego, gdzie maksymalna głębokość drzewa dla poszczególnych drzew jest ograniczona, a łączna liczba drzew używanych w ramach technik lasów lub DB.

**Krzyżowe sprawdzanie poprawności (CV)** to proces tworzenia wielu podzbiorów danych pełnego szkolenia i uczenia modelu w każdym podzestawie. Pomysłem jest to, że model może uzyskać "Cieszymy" i mieć dużą dokładność z jednym podzbiorem, ale przy użyciu wielu podzestawów model nie będzie osiągał tej dużej dokładności za każdym razem. Podczas wykonywania operacji CV należy dostarczyć zestaw danych wstrzymania weryfikacji, określić swoje zgięcia CV (liczba podzbiorów), a zautomatyzowany ML będzie szkolić model i dostrajał parametry w celu zminimalizowania błędu w zestawie walidacji. Jedno zgięcie CV może być nadmiernie dopasowane, ale przy użyciu wielu z nich zmniejsza prawdopodobieństwo, że ostateczny model jest nadmiernie dopasowany. Wadą jest to, że CV skutkuje dłuższym czasem uczenia się, a tym samym większym kosztem, ponieważ zamiast uczenia się modelu jeden raz, przeszkoluje go raz dla każdego podzestawu *n* CV. 

> [!NOTE]
> Sprawdzanie krzyżowe nie jest domyślnie włączone; musi być skonfigurowany w ustawieniach zautomatyzowanej ML. Jednak po skonfigurowaniu krzyżowego sprawdzania poprawności i otrzymaniu zestawu danych sprawdzania poprawności proces jest zautomatyzowany. Dowiedz się więcej o [konfiguracji krzyżowego sprawdzania poprawności w obszarze automatycznej ml](how-to-configure-cross-validation-data-splits.md)

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identyfikowanie modeli z niezrównoważonymi danymi

Niezrównoważone dane często znajdują się w danych dla scenariuszy klasyfikacji uczenia maszynowego i odwołują się do danych, które zawierają nieproporcjonalny współczynnik obserwacji w każdej klasie. Takie niezrównoważone może prowadzić do fałszywego odczuwania pozytywnego skutku dokładności modelu, ponieważ dane wejściowe mają odchylenia względem jednej klasy, co skutkuje modelem szkolonym do naśladowania tej odchylenia. 

Dodatkowo automatyczne przebiegi w sieci automatycznie generują następujące wykresy, co może pomóc zrozumieć prawidłowość klasyfikacji modelu i zidentyfikować modele potencjalnie wpływać na dane niezrównoważone.

Wykres| Opis
---|---
[Zamieszanie macierzy](how-to-understand-automated-ml.md#confusion-matrix)| Oblicza prawidłowo sklasyfikowane etykiety pod kątem rzeczywistych etykiet danych. 
[Precyzja — odwoływanie](how-to-understand-automated-ml.md#precision-recall-curve)| Oblicza stosunek prawidłowych etykiet względem stosunku do znalezionych wystąpień etykiet danych 
[Krzywe ROC](how-to-understand-automated-ml.md#roc-curve)| Oblicza stosunek poprawnych etykiet względem stosunku do etykiet z fałszywą liczbą dodatnią.

## <a name="handle-imbalanced-data"></a>Obsługa niezrównoważonych danych 

W ramach tego celu uproszczenie przepływu pracy usługi Machine Learning funkcja **zautomatyzowanej wagi jest wbudowana w funkcje** , które ułatwiają rozwiązywanie niezrównoważonych danych, takich jak, 

- **Kolumna wagi**: automatyczna część ml obsługuje kolumnę odważników jako dane wejściowe, co sprawia, że wiersze w danych mają być ważone w górę lub w dół, których można użyć, aby zwiększyć lub zmniejszyć klasę "ważne".

- Algorytmy, które są używane przez zautomatyzowanej, wykrywają niezrównoważone, gdy liczba próbek w klasie mniejszości jest równa lub mniejsza niż 20% liczby próbek w klasie większości, gdzie Klasa drugorzędna odnosi się do tej klasy z największą liczbą próbek i większościową klasą, odnosi się do jednej z większości przykładów. Następnie AutoML uruchomi eksperyment z danymi podpróbkami, aby sprawdzić, czy użycie wag klasy mogłoby rozwiązać ten problem i zwiększyć wydajność. W przypadku zapewnienia lepszej wydajności w ramach tego eksperymentu stosowane jest rozwiązanie tego problemu.

- Użyj metryki wydajności, która zajmuje się lepszym użyciem niezrównoważonych danych. Na przykład AUC_weighted jest podstawową metryką, która oblicza wkład każdej klasy w oparciu o względną liczbę próbek reprezentujących tę klasę, dlatego jest bardziej niezawodna w odniesieniu do nierównowagi.

Następujące techniki są dodatkowymi opcjami obsługi niezrównoważonych danych **poza zautomatyzowaną ml**. 

- Ponowne próbkowanie do nawet nierównowagi klasy, przez zwiększenie próbkowania mniejszych klas lub próbkowanie w dół do większych klas. Te metody wymagają ekspertyzy do przetworzenia i analizowania.

- Przejrzyj metryki wydajności dla niezrównoważonych danych. Na przykład, wynik F1 jest średnią harmoniczną precyzji i odwołania. Precyzja mierzy stopień dokładności klasyfikatora, gdzie wyższa precyzja wskazuje mniejszą liczbę fałszywie dodatnich, podczas gdy funkcja odwoływania mierzy kompletność klasyfikatora, gdzie wyższe odwołanie wskazuje na mniejszą liczbę fałszywych wartości ujemnych.

## <a name="next-steps"></a>Następne kroki

Zobacz przykłady i Dowiedz się, jak tworzyć modele przy użyciu automatycznego uczenia maszynowego:

+ Postępuj zgodnie z [samouczkiem: automatyczne uczenie modelu regresji z Azure Machine Learning](tutorial-auto-train-models.md)

+ Skonfiguruj ustawienia dla eksperymentu automatycznego szkolenia:
  + W programie Azure Machine Learning Studio [wykonaj te kroki](how-to-use-automated-ml-for-ml-models.md).
  + Za pomocą zestawu SDK języka Python [wykonaj te kroki](how-to-configure-auto-train.md).


