---
title: Testy porównawcze wydajności
titleSuffix: Azure Cognitive Search
description: Poznaj wydajność Wyszukiwanie poznawcze platformy Azure za pomocą różnych testów wydajnościowych
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 9f4473d6c8a584bf60e5c8fe2d69d6a56a55e71d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108317"
---
# <a name="azure-cognitive-search-performance-benchmarks"></a>Testy wydajnościowe usługi Azure Wyszukiwanie poznawcze

Wydajność Wyszukiwanie poznawcze platformy Azure zależy od [różnych czynników](search-performance-tips.md) , takich jak rozmiar usługi wyszukiwania i typy wysyłanych zapytań. Aby ułatwić oszacowanie rozmiaru usługi wyszukiwania wymaganej dla obciążenia, należy uruchomić kilka testów porównawczych, aby udokumentować wydajność różnych usług wyszukiwania i konfiguracji. Te testy porównawcze nie gwarantują pewnego poziomu wydajności usługi, ale mogą dać pomysł na wydajność, którą można oczekiwać.

Aby pokryć różne przypadki użycia, firma Microsoft prowadziła testy porównawcze dla dwóch głównych scenariuszy:

* **Wyszukiwanie handlu elektronicznego** — ten test porównawczy emuluje prawdziwy scenariusz handlu elektronicznego i opiera się na [CDON](https://cdon.com)firmowej handlu elektronicznego.
* **Wyszukiwanie dokumentów** — ten scenariusz składa się z wyszukiwania słów kluczowych w przypadku dokumentów pełnotekstowych z [semantycznej Scholar](http://s2-public-api-prod.us-west-2.elasticbeanstalk.com/corpus/download/). To Emuluje typowe rozwiązanie wyszukiwania dokumentów.

Chociaż te scenariusze odzwierciedlają różne przypadki użycia, każdy scenariusz jest inny, więc zawsze zalecamy testowanie wydajności poszczególnych obciążeń. Firma Microsoft opublikowała [rozwiązanie do testowania wydajności przy użyciu programu JMeter](https://github.com/Azure-Samples/azure-search-performance-testing) , dzięki czemu można uruchamiać podobne testy dla własnej usługi.

## <a name="testing-methodology"></a>Metodologia testowania

Aby przeprowadzić test wydajności Wyszukiwanie poznawcze platformy Azure, przeprowadzamy testy dla dwóch różnych scenariuszy w różnych warstwach i kombinacjach replik/partycji.

W celu utworzenia tych testów porównawczych użyto następującej metodologii:

1. Test rozpoczyna się przy `X` zapytaniach na sekundę (zapytań) przez 180 sekund. Jest to zazwyczaj 5 lub 10 zapytań.
2. ZAPYTAŃ następnie powiększony przez `X` i wykonane przez inne 180 sekund
3. Co 180 sekund test wzrósł o `X` zapytań do momentu, aż Średnie opóźnienie wzrosło powyżej 1000 MS lub mniej niż 99% zapytań.

Wykres poniżej przedstawia przykład wizualizacji, jak wygląda obciążenie zapytania testu:

![Przykładowy test](./media/performance-benchmarks/example-test.png)

Każdy scenariusz użył co najmniej 10 000 unikatowych zapytań, aby uniknąć nadmiernego pochylenia testów przez buforowanie.

> [!IMPORTANT]
> Te testy obejmują tylko obciążenia zapytań. Jeśli spodziewasz się, że volumne operacje indeksowania są bardzo duże, pamiętaj, że w testach oceny i wydajności. Przykładowy kod służący do symulowania indeksowania można znaleźć w tym [samouczku](tutorial-optimize-indexing-push-api.md).

### <a name="definitions"></a>Definicje

- **Maksymalna zapytań** — Maksymalna liczba zapytań poniżej jest oparta na najwyższej zapytań osiągniętej w teście, gdzie 99% zapytań zostało ukończonych pomyślnie bez ograniczenia i średni czas oczekiwania na 1000 ms.

- **Wartość procentowa** maksymalnego zapytań — procent wartości maksymalnej zapytań osiągniętej dla określonego testu. Na przykład jeśli dany test osiągnął maksymalnie 100 zapytań, 20% maksymalnej zapytań byłyby 20 zapytań.

- **Opóźnienie** — opóźnienie serwera dla zapytania; liczby te nie obejmują [opóźnienia podróży (RTT)](https://en.wikipedia.org/wiki/Round-trip_delay). Poniższe wartości są w milisekundach (MS).

### <a name="disclaimer"></a>Disclaimer

Kod używany do uruchamiania tych testów porównawczych jest dostępny [tutaj](https://github.com/Azure-Samples/azure-search-performance-testing/tree/main/other_tools). Warto zauważyć, że zaobserwowano nieco mniejsze poziomy zapytań z [rozwiązaniem do testowania wydajności JMeter](https://github.com/Azure-Samples/azure-search-performance-testing) niż w poniższych testach. Różnice można przypisać do różnic w stylu testów. Jest to ważne w przypadku, gdy jest to możliwe, aby testy wydajności były podobne do obciążeń produkcyjnych.

Te testy porównawcze nie gwarantują pewnego poziomu wydajności usługi, ale mogą dać pomysł na wydajność, której można oczekiwać na podstawie Twojego scenariusza.

Jeśli masz jakieś pytania lub wątpliwości, skontaktuj się z nami na serwisie azuresearch_contact@microsoft.com .

## <a name="benchmark-1-e-commerce-search"></a>Test porównawczy 1: wyszukiwanie handlu elektronicznego

:::row:::
   :::column span="1":::
      ![Logo CDON](./media/performance-benchmarks/cdon-logo-160px2.png)
   :::column-end:::
   :::column span="3":::
      Ten test porównawczy został utworzony w ramach partnerstwa z firmą handlu elektronicznego, [CDONem](https://cdon.com), największym Marketplace w regionie państw nordyckich z operacjami w Szwecji, Finlandii, Norwegii i Danii. Za pośrednictwem jego 1 500 handlowców CDON oferuje szeroką gamę zakresów obejmującą ponad 8 000 000 produktów. W 2020 CDON należało ponad 120 000 000 odwiedzających i 2 000 000 aktywnych klientów. Więcej informacji na temat korzystania z platformy Azure Wyszukiwanie poznawcze CDON w [tym artykule](https://pulse.microsoft.com/transform/na/fa1-how-cdon-has-been-using-technology-to-become-the-leading-marketplace-in-the-nordics/).
   :::column-end:::
:::row-end:::

Aby uruchomić te testy, używana jest migawka indeksu wyszukiwania produkcji CDON oraz tysięcy unikatowych zapytań z [witryny sieci Web](https://cdon.com).

### <a name="scenario-details"></a>Szczegóły scenariusza

- **Liczba dokumentów**: 6 000 000 
- **Rozmiar indeksu**: 20 GB
- **Schemat indeksu**: indeks szeroki z 250 pól łącznie, 25 pola z możliwością wyszukiwania i 200 pola do filtrowania
- **Typy zapytań**: zapytania wyszukiwania pełnotekstowego, w tym aspekty, filtry, porządkowanie i profile oceniania

### <a name="s1-performance"></a>Wydajność S1

#### <a name="queries-per-second"></a>Zapytania na sekundę

Na poniższym wykresie przedstawiono najwyższą liczbę zapytań, które może obsłużyć usługa przez dłuższy czas w odniesieniu do zapytań na sekundę (zapytań).

![Najwyższa łatwość utrzymania zapytań handlu elektronicznego S1](./media/performance-benchmarks/s1-ecom-qps.png)

#### <a name="query-latency"></a>Opóźnienie zapytania

Opóźnienia zapytań różnią się w zależności od obciążenia usługi i usług objętych wyższym obciążeniem będzie miało wyższy średni czas oczekiwania na zapytanie. W poniższej tabeli przedstawiono wartości percentyl, pięćdziesiąt, 75., 90, używany 95. i 99 w przypadku opóźnienia zapytań dla trzech różnych poziomów użycia.

| Wartość procentowa maksymalnej zapytań  | Średnie opóźnienie | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 104 MS  | 35 MS  | 115 MS   | 177 MS | 257 MS | 738 MS |
| 50%  | 140 MS  | 47 MS  | 144 MS   | 241 MS | 400 MS | 1175 MS |
| 80%  | 239 MS  | 77 MS  | 248 MS   | 466 MS | 763 MS | 1752 MS | 


### <a name="s2-performance"></a>Wydajność S2

#### <a name="queries-per-second"></a>Zapytania na sekundę

Na poniższym wykresie przedstawiono najwyższą liczbę zapytań, które może obsłużyć usługa przez dłuższy czas w odniesieniu do zapytań na sekundę (zapytań).

![Najwyższa łatwość utrzymania zapytań handlu elektronicznego S2](./media/performance-benchmarks/s2-ecom-qps.png)

#### <a name="query-latency"></a>Opóźnienie zapytania

Opóźnienia zapytań różnią się w zależności od obciążenia usługi i usług objętych wyższym obciążeniem będzie miało wyższy średni czas oczekiwania na zapytanie. W poniższej tabeli przedstawiono wartości percentyl, pięćdziesiąt, 75., 90, używany 95. i 99 w przypadku opóźnienia zapytań dla trzech różnych poziomów użycia.

| Wartość procentowa maksymalnej zapytań  | Średnie opóźnienie | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 56 MS | 21 MS | 68 MS  | 106 MS  | 132 MS | 210 MS | 
| 50%  | 71 MS  | 26 MS  | 83 MS   | 132 MS | 177 MS | 329 MS |
| 80%  | 140 MS  | 47 MS  | 153 MS   | 293 MS | 452 MS | 924 MS | 

### <a name="s3-performance"></a>Wydajność S3

#### <a name="queries-per-second"></a>Zapytania na sekundę

Na poniższym wykresie przedstawiono najwyższą liczbę zapytań, które może obsłużyć usługa przez dłuższy czas w odniesieniu do zapytań na sekundę (zapytań).

![Najwyższa łatwość utrzymania zapytań handlu elektronicznego S3](./media/performance-benchmarks/s3-ecom-qps.png)

W takim przypadku widzimy, że dodanie drugiej partycji znacznie zwiększa maksymalną zapytań, ale dodanie trzeciej partycji zapewnia zmniejszanie krańcowych zwracanych wartości. Mniejsze zwiększenie jest możliwe, ponieważ wszystkie dane są już ściągane do aktywnej pamięci S3's z zaledwie dwiema partycjami.

#### <a name="query-latency"></a>Opóźnienie zapytania

Opóźnienia zapytań różnią się w zależności od obciążenia usługi i usług objętych wyższym obciążeniem będzie miało wyższy średni czas oczekiwania na zapytanie. W poniższej tabeli przedstawiono wartości percentyl, pięćdziesiąt, 75., 90, używany 95. i 99 w przypadku opóźnienia zapytań dla trzech różnych poziomów użycia.

| Wartość procentowa maksymalnej zapytań  | Średnie opóźnienie | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 50 MS  | 20 MS  | 64 MS   | 83 MS | 98 MS | 160 MS |
| 50%  | 62 MS  | 24 MS  | 80 MS   | 107 MS | 130 MS | 253 MS |
| 80%  | 115 MS  | 38 MS  | 121 MS   | 218 MS | 352 MS | 828 MS |

## <a name="benchmark-2-document-search"></a>Test porównawczy 2: wyszukiwanie dokumentów

### <a name="scenario-details"></a>Szczegóły scenariusza

- **Liczba dokumentów**: 7 500 000
- **Rozmiar indeksu**: 22 GB
- **Schemat indeksu**: 23 pola; 8 z możliwością wyszukiwania, 10 filtrów/kroju
- **Typy zapytań**: wyszukiwanie słów kluczowych z aspektami i wyróżnianie trafień

### <a name="s1-performance"></a>Wydajność S1

#### <a name="queries-per-second"></a>Zapytania na sekundę

Na poniższym wykresie przedstawiono najwyższą liczbę zapytań, które może obsłużyć usługa przez dłuższy czas w odniesieniu do zapytań na sekundę (zapytań).

![Najwyższe możliwe do utrzymania wyszukiwanie w dokumentacji zapytań](./media/performance-benchmarks/s1-docsearch-qps.png)

#### <a name="query-latency"></a>Opóźnienie zapytania

Opóźnienia zapytań różnią się w zależności od obciążenia usługi i usług objętych wyższym obciążeniem będzie miało wyższy średni czas oczekiwania na zapytanie. W poniższej tabeli przedstawiono wartości percentyl, pięćdziesiąt, 75., 90, używany 95. i 99 w przypadku opóźnienia zapytań dla trzech różnych poziomów użycia.

| Wartość procentowa maksymalnej zapytań  | Średnie opóźnienie | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 67 MS  | 44 MS  | 77 MS   | 103 MS | 126 MS | 216 MS |
| 50%  | 93 MS  | 59 MS  | 110 MS   | 150 MS | 184 MS | 304 MS |
| 80%  | 150 MS  | 96 MS  | 184 MS   | 248 MS | 297 MS | 424 MS |

### <a name="s2-performance"></a>Wydajność S2

#### <a name="queries-per-second"></a>Zapytania na sekundę

Na poniższym wykresie przedstawiono najwyższą liczbę zapytań, które może obsłużyć usługa przez dłuższy czas w odniesieniu do zapytań na sekundę (zapytań).

![Najwyższe możliwe do utrzymania wyszukiwanie w dokumentacji zapytań S2](./media/performance-benchmarks/s2-docsearch-qps.png)

#### <a name="query-latency"></a>Opóźnienie zapytania

Opóźnienia zapytań różnią się w zależności od obciążenia usługi i usług objętych wyższym obciążeniem będzie miało wyższy średni czas oczekiwania na zapytanie. W poniższej tabeli przedstawiono wartości percentyl, pięćdziesiąt, 75., 90, używany 95. i 99 w przypadku opóźnienia zapytań dla trzech różnych poziomów użycia.

| Wartość procentowa maksymalnej zapytań  | Średnie opóźnienie | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 45 MS  | 31 MS  | 55 MS   | 73 MS | 84 MS | 109 MS |
| 50%  | 63 MS  | 39 MS  | 81 MS   | 106 MS | 123 MS | 163 MS |
| 80%  | 115 MS  | 73 MS  | 145 MS   | 191 MS | 224 MS | 291 MS |

## <a name="takeaways"></a>Wnioski

Za pomocą tych testów porównawczych możesz uzyskać pomysł dotyczący wydajności Wyszukiwanie poznawcze platformy Azure. Możesz również zobaczyć różnicę między usługami w różnych warstwach.

Niektóre z tych testów porównawczych są następujące:

* S2 może przeważnie obsłużyć co najmniej cztery razy wolumin zapytania jako S1
* Zdarzenie S2 ma zwykle małe opóźnienia niż S1 na porównywalnych woluminach zapytań
* Podczas dodawania replik usługa zapytań może obsłużyć zazwyczaj skalowanie liniowe (na przykład jeśli jedna replika może obsłużyć 10 zapytań, a pięć replik może zazwyczaj obsłużyć 50 zapytań)
* Im wyższe obciążenie usługi, tym wyższym średnim opóźnieniem będzie

Możesz również zobaczyć, że wydajność może się różnić w zależności od scenariuszy. Jeśli nie widzisz oczekiwanej wydajności, zapoznaj się ze [wskazówkami, aby uzyskać lepszą wydajność](search-performance-tips.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy widzisz testy wydajnościowe, możesz dowiedzieć się więcej na temat analizowania wydajności Wyszukiwanie poznawcze i kluczowych czynników wpływających na wydajność.

> [!div class="nextstepaction"]
> [Analizowanie wydajności](search-performance-analysis.md) 
>  [Porady dotyczące lepszej wydajności](search-performance-tips.md)