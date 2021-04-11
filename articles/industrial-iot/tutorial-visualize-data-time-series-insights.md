---
title: Wizualizowanie danych OPC UA w Azure Time Series Insights
description: W tym samouczku dowiesz się, jak wizualizować dane za pomocą Time Series Insights.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787816"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>Samouczek: Wizualizacja danych za pomocą Time Series Insights (TSI)

Moduł wydawcy OPC nawiązuje połączenie z serwerami OPC UA i publikuje dane z tych serwerów w celu IoT Hub. Procesor telemetrii w przemysłowej platformie IoT przetwarza te zdarzenia i przekazuje przykładowe kontekstowe do TSI i innych klientów.  

W tym przewodniku opisano sposób wizualizacji i analizy telemetrii OPC UA przy użyciu tego środowiska Time Series Insights.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wszystkie samouczki zawierają listę podsumowującą kroki do ukończenia
> * Każdy z tych punktów punktowanych jest wyrównany do klucza H2
> * Użyj tych zielonych pól wyboru w samouczku

## <a name="prerequisite"></a>Wymaganie wstępne

* Wdróż platformę IIoT, aby automatycznie utworzyć środowisko Time Series Insights
* Dane są publikowane w IoT Hub

## <a name="time-series-insights-explorer"></a>Eksplorator usługi Time Series Insights

Eksplorator Time Series Insights jest aplikacją internetową, której można użyć do wizualizacji danych telemetrycznych. Aby uzyskać adres URL aplikacji, Otwórz `.env` plik zapisany w wyniku wdrożenia.  Otwórz w przeglądarce adres URL w `PCS_TSI_URL` zmiennej.  

Przed rozpoczęciem korzystania z Eksploratora Time Series Insights należy przyznać dostęp do danych TSI użytkownikom uprawnionym do wizualizacji danych. Należy pamiętać, że w przypadku świeżego wdrożenia nie są domyślnie ustawione zasady dostępu do danych, dlatego nikt nie może zobaczyć danych. Zasady dostępu do danych należy ustawić w Azure Portal w środowisku Time Series Insights wdrożonym w grupie zasobów wdrożonej na platformie IIoT w następujący sposób:

   ![Time Series Insights Explorer 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

Wybierz zasady dostępu do danych:

   ![Time Series Insights Explorer 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

Przypisz wymaganych użytkowników:

   ![Time Series Insights Explorer 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


W Eksploratorze TSI Zanotuj nieprzypisane wystąpienia szeregów czasowych. Wystąpienie TSI odpowiada serii czas/wartość dla określonego punktu danych pochodzącego z opublikowanego węzła na serwerze OPC. Wystąpienie TSI (odpowiednio) punkt danych OPC UA jest jednoznacznie identyfikowane przez EndpointId, Identyfikator subskrypcji i NodeId. Modele wystąpień TSI są automatycznie wykrywane i wyświetlane w Eksploratorze na podstawie danych telemetrycznych pozyskanych z centrum zdarzeń procesora telemetrii platformy IIoT.

   ![Time Series Insights Explorer 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

Dane telemetryczne mogą być wizualizowane na wykresie przez kliknięcie prawym przyciskiem myszy wystąpienia TSI i wybranie wartości. Przedział czasu, który ma być używany na wykresie, można dostosować w prawym górnym rogu. Wartość wielu wystąpień może być wizualizacja w tym samym czasie.

Aby uzyskać więcej informacji, zobacz [Szybki Start: eksplorowanie Azure Time Series Insights w wersji zapoznawczej](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

## <a name="define-and-apply-a-new-model"></a>Definiowanie i stosowanie nowego modelu

Ponieważ wystąpienia telemetrii są teraz tylko w formacie nieprzetworzonym, należy je zależeć do odpowiednich 

Aby uzyskać szczegółowe informacje na temat modeli TSI, zobacz [model szeregów czasowych w wersji Zapoznawczej Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)

1. Krok 1. na karcie Model Eksploratora Utwórz nową hierarchię dla danych telemetrycznych. Hierarchia jest strukturą drzewa logicznego, która umożliwia użytkownikowi Wstawianie meta-informacji wymaganych do bardziej intuicyjnej nawigacji przez wystąpienia TSI. Użytkownik może tworzyć/usuwać/modyfikować szablony hierarchii, które mogą być później tworzone dla różnych wystąpień TSI.

   ![Krok 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. Krok 2 — Definiowanie nowego typu dla wartości. W naszym przykładzie obsługujemy tylko numeryczne typy danych

   ![Krok 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. Krok 3 — Wybierz nowe wystąpienie TSI, które ma zostać skategoryzowane w wcześniej zdefiniowanej hierarchii

   ![Krok 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. Krok 4 — Wypełnij właściwości wystąpień — nazwa, opis, wartość danych, a także pola hierarchii w celu dopasowania do struktury logicznej 

   ![Krok 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. Krok 5. Powtórz krok 5 dla wszystkich wystąpień TSI bez kategorii

   ![Krok 5](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. Krok 6. z powrotem na stronie głównej Eksploratora TSI zapoznaj się z hierarchią wystąpień skategoryzowanych i wybierz wartości punktów danych do przeanalizowania

   ![Step6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Połącz Time Series Insights z Power BI

Możesz również połączyć środowisko Time Series Insights, aby Power BI.  Aby uzyskać więcej informacji, zobacz [jak podłączyć TSI do Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) i [wizualizowania danych z TSI w programie Power BI](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi).


## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz już, jak wizualizować dane w TSI, możesz sprawdzić repozytorium przemysłowe IoT:

> [!div class="nextstepaction"]
> [Repozytorium GitHub platformy IIoT](https://github.com/Azure/iot-edge-opc-publisher)