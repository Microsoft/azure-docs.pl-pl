---
title: Tworzenie pulpitu nawigacyjnego Klasyfikacja danych kondycji przy użyciu usługi Azure IoT Central | Microsoft Docs
description: Dowiedz się, jak utworzyć pulpit nawigacyjny Klasyfikacja danych kondycji przy użyciu szablonów aplikacji platformy Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: ed06aef4d494fbdce5a07c5bc50bad9737ba5433
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86497050"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Samouczek: tworzenie pulpitu nawigacyjnego dostawcy Power BI



Podczas tworzenia rozwiązania do monitorowania ciągłego pacjenta można także utworzyć pulpit nawigacyjny dla zespołu opieki szpitalowej, aby wizualizować dane pacjenta. W tym samouczku dowiesz się, jak Power BI utworzyć pulpit nawigacyjny przesyłania strumieniowego w czasie rzeczywistym przy użyciu szablonu aplikacji do monitorowania ciągłego pacjenta IoT Central. Jeśli przypadek użycia nie wymaga dostępu do danych w czasie rzeczywistym, można użyć [pulpitu nawigacyjnego Power BI IoT Central](../core/howto-connect-powerbi.md), który ma uproszczony proces wdrażania. 

>[!div class="mx-imgBorder"]
>![Plik GIF pulpitu nawigacyjnego](media/dashboard-gif-3.gif)

Podstawowa architektura będzie zgodna z tą strukturą:

>[!div class="mx-imgBorder"] 
>![Pulpit nawigacyjny Klasyfikacja dostawcy](media/dashboard-architecture.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Eksportowanie danych z usługi Azure IoT Central do platformy Azure Event Hubs
> * Konfigurowanie zestawu danych przesyłania strumieniowego Power BI
> * Łączenie aplikacji logiki z usługą Azure Event Hubs
> * Przesyłanie strumieniowe danych do Power BI z aplikacji logiki
> * Tworzenie pulpitu nawigacyjnego w czasie rzeczywistym na potrzeby najważniejszych elementów pacjenta

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Szablon aplikacji do monitorowania ciągłego pacjenta platformy Azure IoT Central. Jeśli jeszcze tego nie zrobiono, możesz wykonać kroki, aby [wdrożyć szablon aplikacji](overview-iot-central-healthcare.md).

* [Przestrzeń nazw i centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)usługi Azure Event Hubs.

* Aplikacja logiki, do której chcesz uzyskać dostęp do centrum zdarzeń. Aby uruchomić aplikację logiki przy użyciu wyzwalacza usługi Azure Event Hubs, musisz mieć [pustą aplikację logiki](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow).

* Konto usługa Power BI. Jeśli jeszcze tego nie zrobiono, możesz [utworzyć bezpłatne konto wersji próbnej dla usługa Power BI](https://app.powerbi.com/). Jeśli Power BI nie były używane wcześniej, może być pomocne przechodzenie do [Power BI](https://docs.microsoft.com/power-bi/service-get-started).

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Konfigurowanie ciągłego eksportowania danych do usługi Azure Event Hubs
Najpierw musisz skonfigurować ciągły eksport danych z szablonu aplikacji platformy Azure IoT Central do centrum zdarzeń platformy Azure w ramach subskrypcji. Możesz to zrobić, wykonując kroki opisane w tym samouczku IoT Central platformy Azure, aby [wyeksportować do Event Hubs](https://docs.microsoft.com/azure/iot-central/core/howto-export-data). Na potrzeby tego samouczka należy tylko wyeksportować dane telemetryczne.

## <a name="create-a-power-bi-streaming-dataset"></a>Tworzenie zestawu danych przesyłania strumieniowego Power BI

1. Zaloguj się do swojego konta usługi Power BI.

2. W obszarze preferowany obszar roboczy Utwórz nowy zestaw danych przesyłania strumieniowego, wybierając przycisk **+ Utwórz** w prawym górnym rogu paska narzędzi. Należy utworzyć osobny zestaw danych dla każdego pacjenta, który ma być używany na pulpicie nawigacyjnym.

    >[!div class="mx-imgBorder"] 
    >![Tworzenie zestawu danych przesyłania strumieniowego](media/create-streaming-dataset.png)

3. Wybierz **interfejs API** dla źródła zestawu danych.

4. Wprowadź **nazwę** (na przykład nazwę pacjenta) dla zestawu danych, a następnie wypełnij wartości ze strumienia. Poniżej znajduje się przykład na podstawie wartości pochodzących z symulowanych urządzeń w szablonie aplikacji do monitorowania ciągłego pacjenta. Przykład ma dwóch pacjentów:

    * Teddy Silver, które zawierają dane z inteligentnego nawiasu kolanowego
    * Yesenia Sanford, kto ma dane z poprawki inteligentnych elementów istotnych

    >[!div class="mx-imgBorder"] 
    >![Wprowadzanie wartości zestawu danych](media/enter-dataset-values.png)

Aby dowiedzieć się więcej o zestawach danych przesyłania strumieniowego w Power BI, można odczytać ten dokument w usłudze [przesyłania strumieniowego w czasie rzeczywistym w Power BI](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Łączenie aplikacji logiki z usługą Azure Event Hubs
Aby połączyć aplikację logiki z usługą Azure Event Hubs, możesz postępować zgodnie z instrukcjami przedstawionymi w tym dokumencie na temat [wysyłania zdarzeń za pomocą usługi azure Event Hubs i Azure Logic Apps](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action). Oto kilka sugerowanych parametrów:

|Parametr|Wartość|
|---|---|
|Typ zawartości|application/json|
|Interwał|3|
|Częstotliwość|Second|

Po zakończeniu tego kroku projektant aplikacji logiki powinien wyglądać następująco:

>[!div class="mx-imgBorder"] 
>![Logic Apps nawiązuje połączenie z Event Hubs](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Przesyłanie strumieniowe danych do Power BI z aplikacji logiki
Następnym krokiem będzie przeanalizować dane pochodzące z centrum zdarzeń w celu przesyłania strumieniowego do utworzonych wcześniej zestawów danych Power BI.

1. Zanim będzie to możliwe, należy zrozumieć ładunek JSON, który jest wysyłany z urządzenia do centrum zdarzeń. Można to zrobić przez przejrzenie tego [przykładowego schematu](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry) i zmodyfikowanie go w celu dopasowania go do schematu lub przy użyciu [Eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer) do inspekcji komunikatów. Jeśli używasz aplikacji do monitorowania ciągłego pacjenta, komunikaty będą wyglądać następująco:

**Dane telemetryczne poprawek inteligentnych**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Inteligentne dane telemetryczne kolana**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Właściwości**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

2. Po sprawdzeniu ładunków w formacie JSON Wróć do projektanta aplikacji logiki i wybierz pozycję **+ nowy krok**. Wyszukaj i Dodaj **zmienną Initialize** w następnym kroku i wprowadź następujące parametry:

    |Parametr|Wartość|
    |---|---|
    |Nazwa|Nazwa interfejsu|
    |Typ|Ciąg|

    Wybierz pozycję **Zapisz**. 

3. Dodaj kolejną zmienną o nazwie **Body** z typem jako **ciąg**. Do aplikacji logiki zostaną dodane następujące akcje:

    >[!div class="mx-imgBorder"]
    >![Inicjowanie zmiennych](media/initialize-string-variables.png)
    
4. Wybierz pozycję **+ nowy krok** i Dodaj akcję **Przeanalizuj dane JSON** . Zmień nazwę tego elementu na, aby **przeanalizować właściwości**. Dla zawartości wybierz **Właściwości** pochodzące z centrum zdarzeń. Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu** u dołu i wklej przykładowy ładunek z powyższej sekcji Właściwości.

5. Następnie wybierz akcję **Ustaw zmienną** i zaktualizuj zmienną **nazwy interfejsu** za pomocą **iothub-Interface-Name** z właściwości przeanalizowanego pliku JSON.

6. Dodaj kontrolkę **Split** jako następną akcję i wybierz zmienną **nazwa interfejsu** jako parametr on. Służy to do lejkowania danych do poprawnego zestawu danych.

7. W aplikacji IoT Central platformy Azure Znajdź nazwę interfejsu dla inteligentnych danych dotyczących kondycji poprawek oraz dane dotyczące kondycji inteligentnej, a następnie w widoku **Szablony urządzeń** . Utwórz dwa różne przypadki dla formantu **Switch** dla każdej nazwy interfejsu i odpowiednio zmień nazwę formantu. Możesz ustawić domyślną wielkość liter, aby użyć kontrolki **Przerwij** i wybrać stan, który ma być wyświetlany.

    >[!div class="mx-imgBorder"] 
    >![Podziel kontrolkę](media/split-by-interface.png)

8. W przypadku **poprawki "Najważniejsze znaczenie** " Dodaj akcję **Przeanalizuj dane JSON** . W polu zawartość wybierz **zawartość** pochodzącą z centrum zdarzeń. Skopiuj i wklej przykładowe ładunki w celu wygenerowania schematu.

9. Dodaj akcję **Ustaw zmienną** i zaktualizuj zmienną **treści** do **treści** z przeanalizowanego kodu JSON w kroku 7.

10. Dodaj kontrolkę **warunek** jako następną akcję i Ustaw warunek na **treść**, **zawiera**, **HeartRate**. Dzięki temu można upewnić się, że masz właściwy zestaw danych pochodzący z poprawki inteligentnej, przed wypełnieniem Power BI zestawu danych. Kroki 7-9 będą wyglądać następująco:

    >[!div class="mx-imgBorder"] 
    >![Warunki](media/smart-vitals-pbi.png)

11. W przypadku **prawdziwego** przypadku warunku Dodaj akcję, która wywołuje funkcję **Dodaj wiersze do zestawu danych** Power BI. Musisz zalogować się do Power BI. **Fałszywych** przypadków można ponownie użyć kontrolki **zakończenia** .

12. Wybierz odpowiedni **obszar roboczy**, **zestaw danych**i **tabelę**. Mapuj parametry, które zostały określone podczas tworzenia zestawu danych przesyłania strumieniowego w Power BI do przeanalizowanych wartości JSON, które pochodzą z centrum zdarzeń. Wypełnione akcje powinny wyglądać następująco:

    >[!div class="mx-imgBorder"] 
    >![Dodaj wiersze do Power BI](media/add-rows-yesenia.png)

13. W przypadku przełącznika "inteligentne" w **nawiasach klamrowych** Dodaj akcję **Przeanalizuj dane JSON** , aby przeanalizować zawartość, podobną do kroku 7. Następnie **Dodaj wiersze do zestawu danych** , aby zaktualizować zestaw danych Silver Teddy w Power BI.

    >[!div class="mx-imgBorder"] 
    >![Warunki](media/knee-brace-pbi.png)

14. Naciśnij pozycję **Zapisz** , a następnie uruchom aplikację logiki.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Tworzenie pulpitu nawigacyjnego w czasie rzeczywistym na potrzeby najważniejszych elementów pacjenta
Teraz wróć do Power BI i wybierz pozycję **+ Utwórz** , aby utworzyć nowy **pulpit nawigacyjny**. Nadaj pulpitowi nawigacyjnemu nazwę i naciśnij przycisk **Utwórz**.

Wybierz trzy kropki na górnym pasku nawigacyjnym, a następnie wybierz pozycję **+ Dodaj kafelek**.

>[!div class="mx-imgBorder"] 
>![Dodaj kafelek do pulpitu nawigacyjnego](media/add-tile.png)

Wybierz typ kafelka, który chcesz dodać i dostosować swoją aplikację.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń zasoby, wykonując następujące czynności:

1. Z Azure Portal można usunąć utworzone centrum zdarzeń i zasoby Logic Apps.

2. W przypadku aplikacji IoT Central przejdź do karty Administracja i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się ze [wskazówkami dotyczącymi architektury monitorowania ciągłego pacjenta](concept-continuous-patient-monitoring-architecture.md).
