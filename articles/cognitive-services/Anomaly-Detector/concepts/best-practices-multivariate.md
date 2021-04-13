---
title: Najlepsze rozwiązania dotyczące korzystania z interfejsu API wieloczynnikowa wykrywania anomalii
titleSuffix: Azure Cognitive Services
description: Najlepsze rozwiązania dotyczące korzystania z interfejsu API wieloczynnikowa wykrywania anomalii w celu zastosowania wykrycia anomalii w danych szeregów czasowych.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: wykrywanie anomalii, uczenie maszynowe, algorytmy
ms.openlocfilehash: 7de25b4a099c706c05b32b52492096923033f822
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315613"
---
# <a name="multivariate-time-series-anomaly-detector-best-practices"></a>Wieloczynnikowa najlepszych rozwiązań dotyczących wykrywania anomalii szeregów czasowych

Ten artykuł zawiera wskazówki dotyczące zalecanych praktyk, które należy wykonać podczas korzystania z interfejsów API wykrywania anomalii wieloczynnikowa.

## <a name="how-to-prepare-data-for-training"></a>Jak przygotować dane do szkolenia

Aby korzystać z interfejsów API wieloczynnikowa wykrywania anomalii, musimy nauczyć nasz model przed użyciem wykrywania. Dane używane do szkoleń to partia szeregów czasowych, każda seria czasu powinna być w formacie CSV z dwiema kolumnami, sygnaturą czasową i wartością. Wszystkie serie czasowe powinny być spakowane w jednym pliku zip i przekazywane do usługi Azure Blob Storage. Domyślnie nazwa pliku zostanie użyta do reprezentowania zmiennej dla szeregów czasowych. Alternatywnie, dodatkowe meta.jsw pliku można umieścić w pliku zip, jeśli chcesz, aby nazwa zmiennej była inna niż nazwa pliku zip. Po wygenerowaniu [adresu URL sygnatury dostępu współdzielonego (Shared Access Signature)](../../../storage/common/storage-sas-overview.md)można używać go do uczenia się.

## <a name="data-quality-and-quantity"></a>Jakość i ilość danych

Interfejs API wieloczynnikowa wykrywania anomalii używa najnowocześniejszych sieci neuronowych, aby poznać normalne wzorce z danych historycznych i prognozować, czy przyszłe wartości to anomalie. Jakość i ilość danych szkoleniowych jest ważna, aby szkolić optymalny model. Ponieważ model uzyskuje normalne wzorce z danych historycznych, dane szkoleniowe powinny przedstawiać ogólny normalny stan systemu. Jeśli dane szkoleniowe są pełne, trudno jest zapoznać się z modelami. Ponadto model ma miliony parametrów i potrzebuje minimalnej liczby punktów danych, aby poznać optymalny zestaw parametrów. Ogólna reguła polega na tym, że należy podać co najmniej 15 000 punktów danych na zmienną, aby prawidłowo szkolić model. Im więcej danych, tym lepszy model.

Często wiele szeregów czasowych ma brakujące wartości, co może wpłynąć na wydajność modeli szkolonych. Brak współczynnika każdej serii czasowej powinna być kontrolowana w rozsądnej wartości. Brak szeregów czasowych mających 90% wartości zawiera niewiele informacji na temat normalnych wzorców systemu. Nawet gorsz, model może rozważyć wypełnienie wartości jako zwykłych wzorców, które zazwyczaj są segmentami prostymi lub wartościami stałymi. W przypadku nowych przepływów danych w programie dane mogą zostać wykryte jako anomalie.

Zalecany maksymalny próg braku wartości wynosi 20%, ale w pewnych okolicznościach może być dopuszczalny wyższy próg. Na przykład jeśli masz serię czasową z dokładnością o pojedynczej minucie i kolejną serią czasową z dokładnością co godzinę.  Każda godzina obejmuje 60 punktów danych na minutę danych i 1 punkt danych dla danych godzinowych, co oznacza, że brakujący współczynnik dla danych godzinowych to 98,33%. Jednak warto podać dane godzinowe z jedyną wartością, jeśli cykle czasowe nie zwykle zmieniają się zbyt wiele.

## <a name="parameters"></a>Parametry

### <a name="sliding-window"></a>Okno przewijania

Wykrywanie anomalii wieloczynnikowa pobiera segmenty punktów danych o długości `slidingWindow` jako dane wejściowe i decyduje o tym, czy następny punkt danych jest anomalią. Im większa długość próbki, tym więcej danych zostanie uwzględnionych w ramach decyzji. Należy pamiętać o dwóch kwestiach podczas wybierania odpowiedniej wartości dla `slidingWindow` : właściwości danych wejściowych i wymiany między czasem szkolenia/wnioskowania i ewentualnym ulepszeniu wydajności. `slidingWindow` składa się z liczby całkowitej z zakresu od 28 do 2880. Możesz zdecydować, ile punktów danych jest używanych jako dane wejściowe w zależności od tego, czy dane są okresowo, oraz częstotliwość próbkowania danych.

Gdy dane są okresowo, może zawierać 1-3 cykli jako dane wejściowe i gdy dane są próbkowane z wysoką częstotliwością (mała stopień szczegółowości), takim jak dane na poziomie minuty lub drugiego poziomu, możesz wybrać więcej danych jako dane wejściowe. Inny problem polega na tym, że dłuższe dane wejściowe mogą powodować dłuższe szkolenia/czas oczekiwania i nie ma gwarancji, że więcej punktów wejścia będzie prowadzić do wzrostu wydajności. Zbyt mało punktów danych, dzięki czemu model może utrudniać zbieżność do optymalnego rozwiązania. Na przykład trudno jest wykryć anomalie, gdy dane wejściowe zawierają tylko dwa punkty.

### <a name="align-mode"></a>Tryb wyrównany

Parametr `alignMode` jest używany do wskazania, w jaki sposób ma być wyrównane wiele szeregów czasowych w sygnaturach czasowych. Wynika to z faktu, że wiele szeregów czasowych ma brakujące wartości i muszą one być wyrównane do tych samych sygnatur czasowych przed dalszem przetwarzaniem. Dostępne są dwie opcje dla tego parametru `inner join` i `outer join` . `inner join` oznacza to, że będziemy raportować wyniki wykrywania sygnatur czasowych, na których **Każda seria czasowa** ma wartość, podczas gdy oznacza to, `outer join` że będziemy raportować wyniki wykrywania sygnatur czasowych dla **każdej serii czasowej** , która ma wartość.  **Ma `alignMode` także wpływ na sekwencję wejściową modelu**, dlatego należy wybrać odpowiedni `alignMode` dla danego scenariusza, ponieważ wyniki mogą być znacząco inne.

Tutaj pokazano przykład, aby wyjaśnić różne `alignModel` wartości.

#### <a name="series1"></a>Series1

|sygnatura czasowa | wartość|
----------| -----|
|`2020-11-01`| 1  
|`2020-11-02`| 2  
|`2020-11-04`| 4  
|`2020-11-05`| 5

#### <a name="series2"></a>Series2

sygnatura czasowa | wartość  
--------- | -
`2020-11-01`| 1  
`2020-11-02`| 2  
`2020-11-03`| 3  
`2020-11-04`| 4

#### <a name="inner-join-two-series"></a>Sprzężenie wewnętrzne — dwie serie
  
sygnatura czasowa | Series1 | Series2
----------| - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-04`| 4 | 4

#### <a name="outer-join-two-series"></a>Dwie serie sprzężenia zewnętrznego

sygnatura czasowa | series1 | series2
--------- | - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-03`| NA | 3
`2020-11-04`| 4 | 4
`2020-11-05`| 5 | NA

### <a name="fill-not-available-na"></a>Wypełnienie nie jest dostępne (NA)

Gdy zmienne są wyrównane do sygnatury czasowej przez sprzężenie zewnętrzne, `Not Available` `NA` w niektórych zmiennych może istnieć wartość (). Możesz określić metodę, aby wypełnić tę wartość NA wartości. Dostępne opcje to `fillNAMethod` ,, `Linear` `Previous` `Subsequent` ,  `Zero` i `Fixed` .

| Opcja     | Metoda                                                                                           |
| ---------- | -------------------------------------------------------------------------------------------------|
| Liniowe     | Wypełnij NA wartości przez interpolację liniową                                                           |
| Poprzednie   | Propaguj ostatnią poprawną wartość, aby wypełnić przerwy. Przykład: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
| Następnej | Użyj następnej prawidłowej wartości, aby wypełnić przerwy. Przykład: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
| Zero       | Wypełnij wartości NA wartość 0.                                                                           |
| Stałe      | Wypełnij wartości na wartość z określoną prawidłową wartością, która powinna zostać podana w `paddingValue` .          |

## <a name="model-analysis"></a>Analiza modelu

### <a name="training-latency"></a>Opóźnienie szkolenia

Uczenie wykrywania anomalii wieloczynnikowa może być czasochłonne. Szczególnie w przypadku korzystania z dużej liczby sygnatur czasowych używanych do uczenia się. W związku z tym zezwalamy na asynchroniczne części procesu szkoleniowego. Zazwyczaj użytkownicy przesyłają zadanie pouczenia za poorednictwem interfejsu API modelu uczenia. Następnie Pobierz stan modelu za pomocą `Get Multivariate Model API` . Tutaj pokazano, jak wyodrębnić czas pozostały do zakończenia szkolenia. W odpowiedzi interfejsu API modelu Get wieloczynnikowa istnieje element o nazwie `diagnosticsInfo` . W tym elemencie istnieje `modelState` element. Aby obliczyć czas pozostały, należy użyć `epochIds` i `latenciesInSeconds` . Epoka reprezentuje jeden kompletny cykl przez dane szkoleniowe. Każde 10 epoki spowoduje wyjście z informacji o stanie. W sumie firma Microsoft będzie pociągać za 100 epoki, opóźnienie wskazuje, jak długo trwa Epoka. Dzięki tym informacjom wiemy, że pozostały czas na przeprowadzenie uczenia modelu.

### <a name="model-performance"></a>Wydajność modelu

Wieloczynnikowa wykrywanie anomalii jako nienadzorowany model. Najlepszym sposobem na ocenę jest ręczne sprawdzenie wyników anomalii. W odpowiedzi na model Get wieloczynnikowa udostępniamy pewne podstawowe informacje na potrzeby analizowania wydajności modeli. W `modelState` elemencie zwróconym przez interfejs API Get wieloczynnikowa model można użyć i, `trainLosses` `validationLosses` Aby sprawdzić, czy model został przeszkolony zgodnie z oczekiwaniami. W większości przypadków dwie straty zmniejszą się stopniowo. Innym elementem informacji dla nas do analizowania wydajności modelu jest w programie `variableStates` . Lista Stanów zmiennych jest uporządkowana według `filledNARatio` kolejności malejącej. Im większy jest spadek wydajności, zwykle jest to konieczne `NA ratio` . `NA` może być spowodowany brakiem wartości lub niewyrównanymi zmiennymi z perspektywy sygnatury czasowej.

## <a name="next-steps"></a>Następne kroki

- [Przewodniki Szybki Start](../quickstarts/client-libraries-multivariate.md).
- [Dowiedz się więcej o podstawowych algorytmach, które wieloczynnikowa wykrywania anomalii](https://arxiv.org/abs/2009.02040)