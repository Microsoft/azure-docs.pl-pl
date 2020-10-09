---
title: 'Oceń polecania: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu do szacowania w Azure Machine Learning, aby oszacować dokładność prognoz modelu zalecanych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 0890e13acbba8dae31de28d7c78a81bd9b516853
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76312264"
---
# <a name="evaluate-recommender"></a>Ocena modułu poleceń

W tym artykule opisano sposób korzystania z modułu do szacowania w programie Azure Machine Learning Designer. Celem jest zmierzenie dokładności prognoz, które zostały wykonane przez model rekomendacji. Korzystając z tego modułu, można oszacować różne rodzaje zaleceń:  
  
-   Klasyfikacje przewidziane dla użytkownika i elementu    
-   Elementy zalecane dla użytkownika  
  
Podczas tworzenia prognoz przy użyciu modelu rekomendacji dla każdego z obsługiwanych typów prognoz są zwracane nieco inne wyniki. W module oceny zaleca się wywnioskowanie rodzaju przewidywania z formatu kolumn zestawu danych z oceną. Na przykład, wynikowy zestaw danych może zawierać:

- Potrójne oceny elementów użytkownika
- Użytkownicy i ich zalecane elementy

Moduł stosuje również odpowiednie metryki wydajności w oparciu o typ wykonywanej prognozowania. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Jak skonfigurować zalecaną ocenę

Moduł szacowania polecania porównuje dane wyjściowe przewidywania przy użyciu modelu rekomendacji z odpowiadającymi im danymi "rzeczywistości". Na przykład moduł [polecający ten wynik](score-svd-recommender.md) umożliwia tworzenie ocenionych zestawów danych, które można analizować za pomocą narzędzia do oceny.

### <a name="requirements"></a>Wymagania

Aby oszacować polecał, wymagane są następujące zestawy danych jako dane wejściowe. 
  
#### <a name="test-dataset"></a>Testowy zestaw danych

Zestaw danych testu zawiera dane "podstawowe prawdy" w postaci potrójnych elementów użytkownika.  

#### <a name="scored-dataset"></a>Wynikowy zestaw danych

Wynikowy zestaw danych zawiera przewidywania wygenerowane przez model rekomendacji.  
  
Kolumny w tym drugim zestawie danych zależą od rodzaju przewidywania wykonywanego podczas procesu oceniania. Na przykład, wynikowy zestaw danych może zawierać jedną z następujących wartości:

- Użytkownicy, elementy i oceny, które mogą dać użytkownikowi dla elementu
- Lista użytkowników i elementów zalecanych dla nich 

### <a name="metrics"></a>Metryki

Metryki wydajności dla modelu są generowane na podstawie typu danych wejściowych. Poniższe sekcje zawierają szczegółowe informacje.

## <a name="evaluate-predicted-ratings"></a>Oceń przewidywane klasyfikacje  

Gdy oceniasz przewidywane klasyfikacje, wynikowy zestaw danych (drugie dane wejściowe do oceny) musi zawierać trzykrotne oceny elementów użytkownika, które spełniają następujące wymagania:
  
-   Pierwsza kolumna zestawu danych zawiera identyfikatory użytkowników.    
-   Druga kolumna zawiera identyfikatory elementów.  
-   Trzecia kolumna zawiera odpowiednie oceny elementów użytkownika.  
  
> [!IMPORTANT] 
> Aby obliczanie powiodło się, nazwy kolumn muszą być `User` `Item` odpowiednio,, i `Rating` .  
  
Ocena polecania porównuje klasyfikacje w zestawie danych "Uziemienie", do przewidywanych klasyfikacji zestawu danych. Następnie oblicza średni błąd bezwzględny (MAE) i pierwiastek średniego błędu kwadratowego (RMSE).



## <a name="evaluate-item-recommendations"></a>Oceń zalecenia dotyczące elementów

Gdy oceniasz zalecenia dotyczące elementów, użyj oceny zestawu danych, który zawiera zalecane elementy dla każdego użytkownika:
  
-   Pierwsza kolumna zestawu danych musi zawierać identyfikator użytkownika.    
-   Wszystkie kolejne kolumny powinny zawierać odpowiadające im identyfikatory elementów uporządkowane według odpowiedniego elementu. 

Przed nawiązaniem połączenia z tym zestawem danych zalecamy posortowanie zestawu danych, tak aby najbardziej odpowiednie elementy były wcześniej dostępne.  

> [!IMPORTANT] 
> Aby można było wykonać ocenę problemu, nazwy kolumn muszą być `User` , `Item 1` , `Item 2` , `Item 3` i tak dalej.  
  
Funkcja oceniania zaleca obliczanie średniego znormalizowanego zysku z rabatem skumulowanym (NDCG) i zwraca go do wyjściowego zestawu danych.  
  
Ze względu na to, że w przypadku zalecanych elementów nie jest wiadomo, że w zestawie danych testowych są obecne wartości rzeczywiste "w rzeczywistości Aby oszacować, moduł oceniania polecania musi generować tylko zalecenia dotyczące elementów ze klasyfikacjami "Obudowa", w zestawie danych testowych.  
  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
