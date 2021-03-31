---
title: Handel o wysokiej częstotliwości przy użyciu Azure Stream Analytics
description: Sposób przeprowadzania szkolenia i oceniania modelu regresji liniowej w ramach zadania usługi Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: f632c916c3de61b94532e96be23da511ad5863ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593037"
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Symulacja transakcji o wysokiej częstotliwości za pomocą usługi Stream Analytics
Usługa Azure Stream Analytics umożliwia korzystanie z funkcji zdefiniowanych przez użytkownika (UDF) i agregatów zdefiniowanych przez użytkownika (UDA) napisanych w języku JavaScript. Połączenie tych możliwości z językiem SQL pozwala użytkownikom przeprowadzać zaawansowane analizy. Mogą one obejmować szkolenie i ocenianie w ramach uczenia maszynowego online oraz symulację procesów stanowych. W tym artykule opisano sposób przeprowadzania regresji liniowej w zadaniu usługi Azure Stream Analytics, które w sposób ciągły przeprowadza ocenianie i szkolenie w ramach scenariusza transakcji o wysokiej częstotliwości.

## <a name="high-frequency-trading"></a>Transakcje o wysokiej częstotliwości
Logiczny przepływ transakcji o wysokiej częstotliwości obejmuje:
1. Uzyskiwanie ofert giełdowych w czasie rzeczywistym.
2. Tworzenie modelu predykcyjnego na podstawie ofert w celu przewidywania wahań cen.
3. Składanie zleceń zakupu lub sprzedaży umożliwiających osiąganie zysków dzięki właściwym prognozom dotyczącym wahań cen. 

W związku z tym potrzebne są poniższe elementy:
* Kanał informacyjny z ofertami publikowanymi w czasie rzeczywistym.
* Model predykcyjny, który może korzystać z ofert publikowanych w czasie rzeczywistym.
* Symulacja transakcji prezentująca zysk lub stratę na podstawie algorytmu transakcji.

### <a name="real-time-quote-feed"></a>Kanał informacyjny ofert w czasie rzeczywistym
Firma IEX bezpłatnie udostępnia [oferty kupna i sprzedaży w czasie rzeczywistym](https://iextrading.com/developer/docs/#websockets) przy użyciu biblioteki socket.io. Można napisać prosty program konsolowy, który umożliwia otrzymywanie ofert w czasie rzeczywistym oraz ich wypychanie do usługi Azure Event Hubs jako źródła danych. Następujący kod to szkielet tego programu. Obsługa błędów została pominięta w celu skrócenia programu. W projekcie trzeba również uwzględnić następujące pakiety Nuget: SocketIoClientDotNet i WindowsAzure.ServiceBus.

```csharp
using Quobject.SocketIoClientDotNet.Client;
using Microsoft.ServiceBus.Messaging;
var symbols = "msft,fb,amzn,goog";
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");
socket.On(Socket.EVENT_MESSAGE, (message) =>
{
    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
});
socket.On(Socket.EVENT_CONNECT, () =>
{
    socket.Emit("subscribe", symbols);
});
```

Poniżej przedstawiono niektóre wygenerowane przykładowe zdarzenia:

```json
{"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}
```

>[!NOTE]
>Sygnatura czasowa zdarzenia w czasie uniksowym to **lastUpdated**.

### <a name="predictive-model-for-high-frequency-trading"></a>Model predykcyjny transakcji o wysokiej częstotliwości
Do celów demonstracyjnych użyjemy modelu liniowego, [opisanego przez Darryla Shena](https://docplayer.net/23038840-Order-imbalance-based-strategy-in-high-frequency-trading.html).

Nierównowaga wielkości zleceń (VOI, Volume Order Imbalance) to funkcja bieżącej wielkości i ceny zakupu/sprzedaży oraz wielkości i ceny zakupu/sprzedaży dla ostatniej najmniejszej możliwej zmiany ceny. W opracowaniu zidentyfikowano korelację między wartością VOI i przyszłymi wahaniami cen. Utworzono model liniowy między 5 ostatnimi wartościami VOI i zmianą cen w ramach ostatnich 10 najmniejszych możliwych zmian ceny. Model jest uczony przy użyciu regresji liniowej na danych z poprzedniego dnia. 

Uczony model jest następnie używany do prognozowania zmian cen ofert dla bieżącej sesji giełdowej w czasie rzeczywistym. Jeśli prognozowana zmiana ceny jest wystarczająco duża, następuje transakcja handlowa. W zależności od ustawienia progu w ciągu sesji giełdowej można oczekiwać tysięcy transakcji powiązanych z jednym rodzajem akcji.

![Definicja nierównowagi kolejności woluminów](./media/stream-analytics-high-frequency-trading/volume-order-imbalance-formula.png)

Teraz przedstawmy operacje szkolenia i prognozowania w zadaniu usługi Azure Stream Analytics.

Najpierw następuje wyczyszczenie danych. Czas uniksowy jest konwertowany na datę i godzinę przy użyciu instrukcji **DATEADD**. Instrukcja **TRY_CAST** umożliwia wymuszanie typów danych bez występowania błędów zapytania. W każdym przypadku dobrym rozwiązaniem jest rzutowanie pól wejściowych na oczekiwane typy danych, aby podczas modyfikowania lub porównywania pól nie wystąpiło nieoczekiwane zachowanie.

```SQL
WITH
typeconvertedquotes AS (
    /* convert all input fields to proper types */
    SELECT
        System.Timestamp AS lastUpdated,
        symbol,
        DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
        TRY_CAST(bidSize as bigint) AS bidSize,
        TRY_CAST(bidPrice as float) AS bidPrice,
        TRY_CAST(askSize as bigint) AS askSize,
        TRY_CAST(askPrice as float) AS askPrice,
        TRY_CAST(volume as bigint) AS volume,
        TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
        TRY_CAST(lastSalePrice as float) AS lastSalePrice
    FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
),
timefilteredquotes AS (
    /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
    /* clean up invalid data points */
    SELECT * FROM typeconvertedquotes
    WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
),
```

Następnie używamy funkcji **LAG** w celu pobrania wartości z ostatniej najmniejszej możliwej zmiany ceny. Pozycja **LIMIT DURATION** ma odgórnie wybraną wartość jednej godziny. Mając na uwadze częstotliwość ofert, można bezpiecznie przyjąć założenie, że w ciągu poprzedniej godziny można znaleźć poprzednią najmniejszą możliwą zmianę ceny.  

```SQL
shiftedquotes AS (
    /* get previous bid/ask price and size in order to calculate VOI */
    SELECT
        symbol,
        (bidPrice + askPrice)/2 AS midPrice,
        bidPrice,
        bidSize,
        askPrice,
        askSize,
        LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
        LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
        LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
        LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
    FROM timefilteredquotes
),
```

Następnie obliczamy wartość VOI. Na wszelki wypadek wartości puste są odfiltrowywane, jeśli poprzednia najmniejsza możliwa zmiana ceny nie istnieje.

```SQL
currentPriceAndVOI AS (
    /* calculate VOI */
    SELECT
        symbol,
        midPrice,
        (CASE WHEN (bidPrice < bidPricePrev) THEN 0
            ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
         END) -
        (CASE WHEN (askPrice < askPricePrev) THEN askSize
            ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
         END) AS VOI
    FROM shiftedquotes
    WHERE
        bidPrice IS NOT NULL AND
        bidSize IS NOT NULL AND
        askPrice IS NOT NULL AND
        askSize IS NOT NULL AND
        bidPricePrev IS NOT NULL AND
        bidSizePrev IS NOT NULL AND
        askPricePrev IS NOT NULL AND
        askSizePrev IS NOT NULL
),
```

Teraz ponownie używamy funkcji **LAG** w celu utworzenia sekwencji z 2 kolejnymi wartościami VOI, po których następuje 10 kolejnych wartości ceny średniej.

```SQL
shiftedPriceAndShiftedVOI AS (
    /* get 10 future prices and 2 previous VOIs */
    SELECT
        symbol,
        midPrice AS midPrice10,
        LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
        LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
        LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
        LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
        LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
        LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
        LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
        LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
        LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
        LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
        LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
        LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
```

Następnie przekształcamy dane w dane wejściowe dla modelu liniowego z dwoma zmiennymi. Ponownie odfiltrowujemy zdarzenia, dla których nie mamy wszystkich danych.

```SQL
modelInput AS (
    /* create feature vector, x being VOI, y being delta price */
    SELECT
        symbol,
        (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
        VOI1 AS x1,
        VOI2 AS x2
    FROM shiftedPriceAndShiftedVOI
    WHERE
        midPrice1 IS NOT NULL AND
        midPrice2 IS NOT NULL AND
        midPrice3 IS NOT NULL AND
        midPrice4 IS NOT NULL AND
        midPrice5 IS NOT NULL AND
        midPrice6 IS NOT NULL AND
        midPrice7 IS NOT NULL AND
        midPrice8 IS NOT NULL AND
        midPrice9 IS NOT NULL AND
        midPrice10 IS NOT NULL AND
        midPrice IS NOT NULL AND
        VOI1 IS NOT NULL AND
        VOI2 IS NOT NULL
),
```

Ponieważ usługa Azure Stream Analytics nie ma wbudowanej funkcji regresji liniowej, używamy agregacji **SUM** i **AVG** do obliczania współczynników w modelu liniowym.

![Formuła matematyczna regresji liniowej](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

```SQL
modelagg AS (
    /* get aggregates for linear regression calculation,
     http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
    SELECT
        symbol,
        SUM(x1 * x1) AS x1x1,
        SUM(x2 * x2) AS x2x2,
        SUM(x1 * y) AS x1y,
        SUM(x2 * y) AS x2y,
        SUM(x1 * x2) AS x1x2,
        AVG(y) AS avgy,
        AVG(x1) AS avgx1,
        AVG(x2) AS avgx2
    FROM modelInput
    GROUP BY symbol, TumblingWindow(hour, 24, -4)
),
modelparambs AS (
    /* calculate b1 and b2 for the linear model */
    SELECT
        symbol,
        (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
        (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
        avgy,
        avgx1,
        avgx2
    FROM modelagg
),
model AS (
    /* calculate a for the linear model */
    SELECT
        symbol,
        avgy - b1 * avgx1 - b2 * avgx2 AS a,
        b1,
        b2
    FROM modelparambs
),
```

Aby używać modelu z poprzedniej sesji do oceniania bieżącego zdarzenia, dołączymy oferty do modelu. Jednak zamiast klauzuli **JOIN** użyjemy klauzuli **UNION** w celu połączenia zdarzeń modelu i zdarzeń oferty. Następnie użyjemy funkcji **LAG** w celu sparowania zdarzeń z modelem z poprzedniej sesji i uzyskania dokładnie jednego dopasowania. Z powodu weekendu musimy uwzględnić trzy dni wstecz. W przypadku korzystania z prostej klauzuli **JOIN** otrzymalibyśmy trzy modele dla każdego zdarzenia oferty.

```SQL
shiftedVOI AS (
    /* get two consecutive VOIs */
    SELECT
        symbol,
        midPrice,
        VOI AS VOI1,        
        LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
VOIAndModel AS (
    /* combine VOIs and models */
    SELECT
        'voi' AS type,
        symbol,
        midPrice,
        VOI1,
        VOI2,
        0.0 AS a,
        0.0 AS b1,
        0.0 AS b2
    FROM shiftedVOI
    UNION
    SELECT
        'model' AS type,
        symbol,
        0.0 AS midPrice,
        0 AS VOI1,
        0 AS VOI2,
        a,
        b1,
        b2
    FROM model
),
VOIANDModelJoined AS (
    /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
    SELECT
        symbol,
        midPrice,
        VOI1 as x1,
        VOI2 as x2,
        LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
        LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
        LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
    FROM VOIAndModel
    WHERE type = 'voi'
),
```

Teraz możemy prognozować i generować sygnały kupna/sprzedaży na podstawie modelu, z wartością progu wynoszącą 0,02. Wartość transakcji 10 oznacza kupno, a wartość transakcji -10 — sprzedaż.

``` SQL
prediction AS (
    /* make prediction if there is a model */
    SELECT
        symbol,
        midPrice,
        a + b1 * x1 + b2 * x2 AS efpc
    FROM VOIANDModelJoined
    WHERE
        a IS NOT NULL AND
        b1 IS NOT NULL AND
        b2 IS NOT NULL AND
        x1 IS NOT NULL AND
        x2 IS NOT NULL
),
tradeSignal AS (
    /* generate buy/sell signals */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,     
        midPrice,
        efpc,
        CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
        DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
    FROM prediction
),
```

### <a name="trading-simulation"></a>Symulacja handlu
Po wygenerowaniu sygnałów transakcji chcemy sprawdzić, jak efektywna jest strategia handlowa, nie przeprowadzając rzeczywistych transakcji. 

Możemy to zrobić dzięki agregacji zdefiniowanej przez użytkownika z oknem powtarzanym z przeskokiem co minutę. Dzięki dodatkowemu grupowaniu według daty i klauzuli HAVING w oknie można uwzględniać tylko zdarzenia z jednego dnia. W przypadku okna powtarzanego obejmującego dwa dni data **GROUP BY** powoduje pogrupowanie na dzień poprzedni i bieżący. Klauzula **HAVING** umożliwia odfiltrowanie okien kończących się w bieżącym dniu z grupowaniem w poprzednim dniu.

```SQL
simulation AS
(
    /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,
        date,
        uda.TradeSimulation(tradeSignal) AS s
    FROM tradeSignal
    GROUP BY HoppingWindow(minute, 420, 1), symbol, date
    Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
)
```

Agregacja UDA w języku JavaScript inicjuje wszystkie akumulatory w funkcji `init`, oblicza przejście stanu przy użyciu każdego zdarzenia dodanego do okna i zwraca wyniki symulacji na końcu okna. Ogólny proces handlowy obejmuje:

- Kupowanie akcji w przypadku otrzymania sygnału kupna i braku pakietu akcji.
- Sprzedawanie akcji w przypadku otrzymania sygnału sprzedaży i posiadania pakietu akcji.
- Krótką sprzedaż w przypadku braku pakietu akcji. 

W przypadku odebrania sygnału w pozycji krótkiej należy złożyć zlecenie zakupu w celu pokrycia pozycji krótkiej. W tej symulacji utrzymujemy 10 udziałów na giełdie. Koszt transakcji jest stały i wynosi 8 USD.

```javascript
function main() {
    var TRADE_COST = 8.0;
    var SHARES = 10;
    this.init = function () {
        this.own = false;
        this.pos = 0;
        this.pnl = 0.0;
        this.tradeCosts = 0.0;
        this.buyPrice = 0.0;
        this.sellPrice = 0.0;
        this.buySize = 0;
        this.sellSize = 0;
        this.buyTotal = 0.0;
        this.sellTotal = 0.0;
    }
    this.accumulate = function (tradeSignal, timestamp) {
        if(!this.own && tradeSignal.trade == 10) {
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
        } else if(!this.own && tradeSignal.trade == -10) {
          // Sell to open
          this.own = true;
          this.pos = -1
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
          // Sell to close
          this.own = false;
          this.pos = 0;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
          // Sell to open
          this.own = true;
          this.pos = -1;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;        
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
          // Buy to close
          this.own = false;
          this.pos = 0;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;         
          this.buyTotal += SHARES * tradeSignal.midprice;
        }
    }
    this.computeResult = function () {
        var result = {
            "pnl": this.pnl,
            "buySize": this.buySize,
            "sellSize": this.sellSize,
            "buyTotal": this.buyTotal,
            "sellTotal": this.sellTotal,
            "tradeCost": this.tradeCost
            };
        return result;
    }
}
```

Na koniec dane wyjściowe są przekazywane do pulpitu nawigacyjnego usługi Power BI w celu utworzenia wizualizacji.

```SQL
SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
SELECT
    symbol,
    time,
    date,
    TRY_CAST(s.pnl as float) AS pnl,
    TRY_CAST(s.buySize as bigint) AS buySize,
    TRY_CAST(s.sellSize as bigint) AS sellSize,
    TRY_CAST(s.buyTotal as float) AS buyTotal,
    TRY_CAST(s.sellTotal as float) AS sellTotal
    INTO pnlDashboard
FROM simulation /* output trade simulation to PBI */
```

![Wizualizacja Power BI wykresu](./media/stream-analytics-high-frequency-trading/trades-power-bi-chart.png)

![Wizualizacja wykresu Power BI PNL](./media/stream-analytics-high-frequency-trading/pnl-power-bi-chart.png)


## <a name="summary"></a>Podsumowanie
Za pomocą średnio złożonego zapytania usługi Azure Stream Analytics można zaimplementować realistyczny model transakcji o wysokiej częstotliwości. Ze względu na brak wbudowanej funkcji regresji liniowej musieliśmy uprościć model z pięciu zmiennych wejściowych do dwóch. Jednak przy odpowiednim nakładzie pracy prawdopodobnie można również zaimplementować bardziej złożone algorytmy z większą liczbą wymiarów agregacji UDA w języku JavaScript. 

Warto zauważyć, że większość zapytań innych niż agregacja UDA w języku JavaScript można testować i debugować w programie Visual Studio przy użyciu [narzędzi Azure Stream Analytics dla programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Po napisaniu początkowego zapytania autor spędził mniej niż 30 minut, testując i debugując zapytanie w programie Visual Studio. 

Obecnie agregacji UDA nie można debugować w programie Visual Studio. Pracujemy nad udostępnieniem tej funkcji z możliwością przechodzenia przez kod języka JavaScript. Ponadto należy pamiętać, że nazwy pól uwzględnianych w agregacji UDA składają się tylko z małych liter. Nie było to oczywiste zachowanie podczas testowania zapytań. Jednak przy zgodności usługi Azure Stream Analytics na poziomie 1.1 zachowujemy wielkość liter nazw pól, aby zachowanie było bardziej naturalne.

Mam nadzieję, że ten artykuł będzie inspiracją dla wszystkich użytkowników usługi Azure Stream Analytics, którzy mogą za pomocą naszej usługi w sposób ciągły przeprowadzać zaawansowane analizy prawie w czasie rzeczywistym. Prześlij swoją opinię na temat zawartości tego artykułu, aby ułatwić nam implementowanie zapytań na potrzeby zaawansowanych scenariuszy analitycznych.
