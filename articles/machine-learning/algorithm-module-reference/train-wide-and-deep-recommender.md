---
title: Korzystanie z modułu szkolenia & głębokiego polecania
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu uczenie & głębokiego polecania w programie Azure Machine Learning Designer do uczenia modelu rekomendacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/12/2020
ms.openlocfilehash: d7dd7105ddb0d6503faefb996b84c0e53a62ce49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655380"
---
# <a name="train-wide--deep-recommender"></a>& głębokiego polecania szkolenia w szerokim zakresie
W tym artykule opisano, jak używać modułu **uczenie szczegółowe & głębokiego polecania** w programie Azure Machine Learning Designer do uczenia modelu rekomendacji. Ten moduł jest oparty na szerokiej & głębokiej uczeniu zaproponowanym przez firmę Google.

Moduł **Pociąga & głębokiego polecania** odczytuje zestaw danych trzech klasyfikacji elementów użytkownika i, opcjonalnie, niektóre funkcje użytkownika i elementu. Zwraca przeszkolony & głębokiego zalecenia.  Można następnie użyć przeszkolonego modelu, aby wygenerować przewidywania lub zalecenia dotyczące oceny przy użyciu modułu [oceny szerokiej i głębokiego polecania](score-wide-and-deep-recommender.md) .  

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>Więcej informacji o modelach rekomendacji i szerokiej & zalecanym  

Głównym celem systemu rekomendacji jest zalecanie co najmniej jednego *elementu* *użytkownikom* systemu. Przykładami elementu może być film, restauracji, książka lub utwór. Użytkownik może być osobą, grupą osób lub inną jednostką z preferencjami elementu.  

Istnieją dwa główne podejścia do zalecanych systemów. 

+ Pierwszy jest podejściem **opartym na zawartości** , które umożliwia korzystanie z funkcji zarówno dla użytkowników, jak i elementów. Użytkownicy mogą być opisywany przez właściwości, takie jak wiek i płeć, a elementy mogą być opisane przez właściwości, takie jak autor i producent. Typowe przykłady systemów rekomendacji opartych na zawartości można znaleźć w witrynach społeczności Matchmaking. 
+ Drugim podejściem jest **filtrowanie do współpracy**, które używa tylko identyfikatorów użytkowników i elementów i uzyskuje niejawne informacje o tych jednostkach z macierzy (rozrzedzonej) ocen określonych przez użytkowników do elementów. Możemy dowiedzieć się więcej o użytkownikach z elementów, które zostały ocenione, oraz od innych użytkowników, którzy oceniali te same elementy.  

& głębokiego polecania łączy te podejścia przy użyciu filtrowania do współpracy z podejściem opartym na zawartości. W związku z tym jest uważany za **zalecenie hybrydowe**. 

Jak to działa: Jeśli użytkownik jest relatywnie nowy w systemie, przewidywania są poprawiane poprzez użycie informacji o funkcji dla użytkownika, w tym odróżnieniu od dobrze znanego problemu "zimnego startu". Jednak po zebraniu wystarczającej liczby ocen od określonego użytkownika możliwe jest, aby w pełni spersonalizowane przewidywania na podstawie ich konkretnych ocen, a nie tylko na ich funkcjach. W związku z tym istnieje bezproblemowe przejście z rekomendacji opartych na zawartości do rekomendacji opartych na filtrowaniu do współpracy. Nawet jeśli funkcje użytkownika lub elementu są niedostępne, szeroki & głębokiego polecania będzie nadal działał w trybie filtrowania wspólnie.  

Więcej szczegółowych informacji o szerokim & zalecanym i podstawowym algorytmie probabilistyczne można znaleźć w odpowiednim dokumencie badawczym: [Wide & głębokie uczenie w zakresie systemów zalecanych](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-train-wide--deep-recommender"></a>Jak skonfigurować & głębokiego polecania szkolenia w zakresie uczenia  

+ [Przygotuj dane szkoleniowe](#prepare-data)
+ [Trenowanie modelu](#train-the-model)

### <a name="prepare-data"></a>Przygotowywanie danych

Przed próbą skorzystania z modułu należy koniecznie, aby dane były w formacie oczekiwanym przez model rekomendacji. Wymagany jest zestaw danych szkoleniowych o **trzech klasyfikacjach elementów użytkownika** , ale można również uwzględnić funkcje użytkownika i funkcje elementów (jeśli są dostępne) w oddzielnych zestawach danych.

#### <a name="required-dataset-of-user-item-ratings"></a>Wymagany zestaw danych dla klasyfikacji elementów użytkownika

Dane wejściowe używane do szkoleń muszą zawierać właściwy typ danych w prawidłowym formacie: 

+ Pierwsza kolumna musi zawierać identyfikatory użytkowników.
+ Druga kolumna musi zawierać identyfikatory elementów.
+ Trzecia kolumna zawiera klasyfikację dla pary elementu User-Item. Wartości klasyfikacji muszą być typu liczbowego. 

Na przykład typowy zestaw klasyfikacji elementów użytkownika może wyglądać następująco:

|UserId|MovieId|Klasyfikacja|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>Zestaw danych funkcji użytkownika (opcjonalnie)

Zestaw danych **funkcji użytkownika** musi zawierać identyfikatory użytkowników i używać tych samych identyfikatorów, które zostały podane w pierwszej kolumnie zestawu danych użytkownicy-elementy-klasyfikacje. Pozostałe kolumny mogą zawierać dowolną liczbę funkcji opisujących użytkowników.  

Na przykład typowy zestaw funkcji użytkownika może wyglądać następująco: 

|UserId|Wiek|Płeć|Licz|Lokalizacja|
|------------|--------------|-----------------------|---------------|------------|
|1|25|mężczyzna| Dramat    |Europa|
|223|40|kobieta|Romański|Azja|

#### <a name="item-features-dataset-optional"></a>Item Features DataSet (opcjonalnie)

Zestaw danych funkcji elementu musi zawierać identyfikatory elementów w swojej pierwszej kolumnie. Pozostałe kolumny mogą zawierać dowolną liczbę funkcji opisowych dla elementów.  

Na przykład typowy zestaw funkcji elementu może wyglądać następująco:  

|MovieId|Tytuł|Język oryginalny|Gatunkami|Year (Rok)|
|-------------|-------------|-------------------|-----------|---------------|
|68646|Godfather|Angielski|Dramat|1972|
|31381|Zostało usunięte z wiatru|Angielski|Historia|1939|

### <a name="train-the-model"></a>Trenowanie modelu

1.  Dodaj moduł szkoleń **i głębokiego polecania** do eksperymentu w projektancie, a następnie połącz go z zestawem danych szkoleniowych.  
  
2. Jeśli masz osobny zestaw danych funkcji użytkownika i/lub funkcji elementów, podłącz je do modułu **uczenie i szerokiego zalecenia** .  
  
    - **Zestaw danych funkcji użytkownika**: Połącz zestaw danych, który opisuje użytkowników do drugiego danych wejściowych.
    - **Element dataset Features**: Połącz zestaw danych, który opisuje elementy do trzeciej dane wejściowe.  
    
3.  **Epoki**: wskazuje, ile razy algorytm powinien przetwarzać całe dane szkoleniowe. 

    Im wyższy numer, tym bardziej odpowiednie szkolenia; jednak szkolenia są droższe i mogą spowodować przekroczenie.

4. **Rozmiar wsadu**: wpisz liczbę przykładów szkoleniowych użytych w jednym kroku szkoleniowym. 

     Ten parametr może mieć wpływ na szybkość uczenia. Większy rozmiar wsadu prowadzi do krótszego kosztu epoki, ale może zwiększyć czas zbieżności. A jeśli partia jest zbyt duża, aby dopasować procesor GPU/procesor CPU, może wystąpić błąd pamięci.

5.  **Optymalizator szerokiej części**: Wybierz jednego Optymalizatora, aby zastosować gradienty do szerokiej części modelu.

6.  **Stawka szkoleniowa dla szerokiego Optymalizatora**: wprowadź liczbę z zakresu od 0,0 do 2,0, która definiuje częstotliwość uczenia Optymalizatora częściowego.

    Ten parametr określa rozmiar kroków w każdym kroku szkoleniowym podczas przesuwania w kierunku minimalnej funkcji strat. Zbyt duża stawka szkoleniowa może spowodować przekroczenie wartości minimalne, a zbyt mała stawka szkoleniowa może spowodować problem ze spójnością.

7.  **Transgraniczny wymiar funkcji**: Wpisz wymiar, wprowadzając odpowiednie identyfikatory użytkownika i identyfikator elementu. 

    W szerokim & zalecanym rozwiązaniem jest przekształcenie między różnymi produktami i funkcjami identyfikatora elementu. Wynik przekroczenia zostanie zmieszany zgodnie z tą liczbą, aby zapewnić wymiar.

8.  **Optymalizator głębokiej części**: Wybierz jednego Optymalizatora, aby zastosować gradienty do głębokiej części modelu.

9.  **Szybkość uczenia głębokiego Optymalizatora**: wprowadź liczbę z zakresu od 0,0 do 2,0, która definiuje wskaźnik uczenia dla Optymalizatora części głębokiej.

10.  **Wymiar osadzania użytkownika**: wpisz liczbę całkowitą, aby określić wymiar osadzania identyfikatora użytkownika.

     W szerokiej & Szczegółowa zalecana jest możliwość tworzenia osadzania identyfikatorów użytkowników i osadzania identyfikatorów elementów zarówno dla szerokiej części, jak i częściowej.

11.  **Wymiar osadzania elementów**: wpisz liczbę całkowitą, aby określić wymiar osadzania identyfikatorów elementów.

12.  **Wymiar osadzania funkcji kategorii**: wprowadź liczbę całkowitą, aby określić wymiary osadzania funkcji kategorii.

     W głębokiej części szerokiej & głębokiej zalecanym wektorem osadzania jest nauka dla każdej funkcji kategorii. I te wektory osadzające współdzielą ten sam wymiar.

13.  **Ukryte jednostki**: wpisz liczbę ukrytych węzłów składnika głębokiego. Numery węzłów w każdej warstwie są oddzielone przecinkami. Na przykład, według typu "1 000 500 100", należy określić, że składnik szczegółowy ma trzy warstwy, z pierwszą warstwą do ostatniego odpowiednio ma 1000 węzłów, 500 węzły i węzły 100.

14.  **Funkcja aktywacji**: Wybierz jedną funkcję aktywacji zastosowana do każdej warstwy, wartość domyślna to ReLU.

15.  **Dropout**: wprowadź liczbę z zakresu od 0,0 do 1,0, aby określić prawdopodobieństwo porzucenia danych wyjściowych w poszczególnych warstwach podczas uczenia.

     Dropout to metoda uregulowania, która zapobiega obmontowania sieci neuronowych. Jedną z typowych decyzji dotyczących tej wartości jest rozpoczęcie od 0,5, która wygląda blisko optymalnego dla szerokiego zakresu sieci i zadań.

16.  **Normalizacja partii**: Wybierz tę opcję, aby użyć normalizacji partii po każdej ukrytej warstwie w składniku głębokiego.

     Normalizacja partii jest techniką do walki z wewnętrznym problemem Shift covariate podczas szkoleń w sieci. Ogólnie rzecz biorąc, może pomóc w zwiększeniu szybkości, wydajności i stabilności sieci. 

17.  Uruchamianie potoku.

## <a name="results"></a>Wyniki

Po zakończeniu przebiegu potoku, aby użyć modelu oceniania, Połącz [szeroką i polecaną zalecaną](train-wide-and-deep-recommender.md) [ocenę](score-wide-and-deep-recommender.md), aby przeanalizować wartości dla nowych przykładów wejściowych.

##  <a name="technical-notes"></a>Uwagi techniczne

Szerokie & głębokie, ściśle połączone modele liniowe i głębokie sieci neuronowych, aby połączyć zalety zapamiętywania i generalizacji. Szeroki składnik akceptuje zestaw nieprzetworzonych funkcji i transformacji funkcji do znają interakcji funkcji. I z mniejszą obsługą funkcji, Uogólnij składnik głęboki do niewidocznych kombinacji funkcji za pomocą osadzania funkcji o małym wymiarze. 

W implementacji szerokiej & głębokiego polecania moduł używa domyślnej struktury modelu. Szeroki składnik umożliwia osadzanie użytkowników, osadzanie elementów oraz transformację identyfikatorów użytkowników i identyfikatorów elementów jako dane wejściowe. W przypadku głębokiej części modelu wektor osadzania jest nauką dla każdej funkcji kategorii. Wraz z innymi wektorami funkcji liczbowych, te wektory są następnie przekazywane do sieci neuronowych głębokiego źródła danych. Część szeroka i szczegółowa są połączone przez zsumowanie ostatecznego szanse dziennika wyjściowego jako przewidywania, które na koniec prowadzi do jednej wspólnej funkcji strat dla wspólnego szkolenia.


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych](module-reference.md) dla Azure Machine Learning. 
