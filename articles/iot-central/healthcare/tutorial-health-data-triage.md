---
title: Samouczek — tworzenie pulpitu nawigacyjnego Klasyfikacja danych kondycji przy użyciu usługi Azure IoT Central | Microsoft Docs
description: Samouczek — informacje na temat tworzenia pulpitu nawigacyjnego Klasyfikacja danych kondycji przy użyciu szablonów aplikacji platformy Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: d227d934eedd31342ce419576fffe7cea17efb1d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748154"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Samouczek: tworzenie pulpitu nawigacyjnego dostawcy Power BI

Podczas tworzenia rozwiązania do monitorowania ciągłego pacjenta można także utworzyć pulpit nawigacyjny dla zespołu opieki szpitalowej, aby wizualizować dane pacjenta. W tym samouczku dowiesz się, jak Power BI utworzyć pulpit nawigacyjny przesyłania strumieniowego w czasie rzeczywistym przy użyciu szablonu aplikacji do monitorowania ciągłego pacjenta IoT Central. Jeśli przypadek użycia nie wymaga dostępu do danych w czasie rzeczywistym, można użyć [pulpitu nawigacyjnego Power BI IoT Central](../core/howto-connect-powerbi.md), który ma uproszczony proces wdrażania. 

:::image type="content" source="media/dashboard-gif-3.gif" alt-text="Plik GIF pulpitu nawigacyjnego":::

Podstawowa architektura będzie zgodna z tą strukturą:

:::image type="content" source="media/dashboard-architecture.png" alt-text="Pulpit nawigacyjny Klasyfikacja dostawcy":::

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Eksportowanie danych z usługi Azure IoT Central do platformy Azure Event Hubs
> * Konfigurowanie zestawu danych przesyłania strumieniowego Power BI
> * Łączenie aplikacji logiki z usługą Azure Event Hubs
> * Przesyłanie strumieniowe danych do Power BI z aplikacji logiki
> * Tworzenie pulpitu nawigacyjnego w czasie rzeczywistym na potrzeby najważniejszych elementów pacjenta


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Szablon aplikacji do monitorowania ciągłego pacjenta platformy Azure IoT Central. Jeśli jeszcze tego nie zrobiono, możesz wykonać kroki, aby [wdrożyć szablon aplikacji](overview-iot-central-healthcare.md).

* [Przestrzeń nazw i centrum zdarzeń](../../event-hubs/event-hubs-create.md)usługi Azure Event Hubs.

* Aplikacja logiki, do której chcesz uzyskać dostęp do centrum zdarzeń. Aby uruchomić aplikację logiki przy użyciu wyzwalacza usługi Azure Event Hubs, musisz mieć [pustą aplikację logiki](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Konto usługa Power BI. Jeśli jeszcze tego nie zrobiono, możesz [utworzyć bezpłatne konto wersji próbnej dla usługa Power BI](https://app.powerbi.com/). Jeśli Power BI nie były używane wcześniej, może być pomocne przechodzenie do [Power BI](/power-bi/service-get-started).


## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Konfigurowanie ciągłego eksportowania danych do usługi Azure Event Hubs
Najpierw musisz skonfigurować ciągły eksport danych z szablonu aplikacji platformy Azure IoT Central do centrum zdarzeń platformy Azure w ramach subskrypcji. Możesz to zrobić, wykonując kroki opisane w tym samouczku IoT Central platformy Azure, aby [wyeksportować do Event Hubs](../core/howto-export-data.md). Na potrzeby tego samouczka należy tylko wyeksportować dane telemetryczne.


## <a name="create-a-power-bi-streaming-dataset"></a>Tworzenie zestawu danych przesyłania strumieniowego Power BI

1. Zaloguj się do swojego konta usługi Power BI.

1. W obszarze preferowany obszar roboczy Utwórz nowy zestaw danych przesyłania strumieniowego, wybierając przycisk **+ Utwórz** w prawym górnym rogu paska narzędzi. Należy utworzyć osobny zestaw danych dla każdego pacjenta, który ma być używany na pulpicie nawigacyjnym.

   :::image type="content" source="media/create-streaming-dataset.png" alt-text="Tworzenie zestawu danych przesyłania strumieniowego":::


1. Wybierz **interfejs API** dla źródła zestawu danych.

1. Wprowadź **nazwę** (na przykład nazwę pacjenta) dla zestawu danych, a następnie wypełnij wartości ze strumienia. Poniżej znajduje się przykład na podstawie wartości pochodzących z symulowanych urządzeń w szablonie aplikacji do monitorowania ciągłego pacjenta. Przykład ma dwóch pacjentów:

   * Teddy Silver, którzy mają dane z inteligentnego nawiasu kolanowego.
   * Yesenia Sanford, kto ma dane z poprawki inteligentnych elementów istotnych.

   :::image type="content" source="media/enter-dataset-values.png" alt-text="Wprowadzanie wartości zestawu danych":::

Aby dowiedzieć się więcej o zestawach danych przesyłania strumieniowego w Power BI, można odczytać ten dokument w usłudze [przesyłania strumieniowego w czasie rzeczywistym w Power BI](/power-bi/service-real-time-streaming).


## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Łączenie aplikacji logiki z usługą Azure Event Hubs

Aby połączyć aplikację logiki z usługą Azure Event Hubs, możesz postępować zgodnie z instrukcjami przedstawionymi w tym dokumencie na temat [wysyłania zdarzeń za pomocą usługi azure Event Hubs i Azure Logic Apps](../../connectors/connectors-create-api-azure-event-hubs.md#add-event-hubs-action). Oto kilka sugerowanych parametrów:

|Parametr|Wartość|
|---|---|
|Typ zawartości|application/json|
|Interwał|3|
|Częstotliwość|Second|

Po zakończeniu tego kroku projektant aplikacji logiki powinien wyglądać następująco:

>[!div class="mx-imgBorder"] 
>![Logic Apps nawiązuje połączenie z Event Hubs](media/eh-logic-app.png)

:::image type="content" source="media/enter-dataset-values.png" alt-text="Wprowadzanie wartości zestawu danych":::


## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Przesyłanie strumieniowe danych do Power BI z aplikacji logiki

Następnym krokiem będzie przeanalizować dane pochodzące z centrum zdarzeń w celu przesyłania strumieniowego do utworzonych wcześniej zestawów danych Power BI.

Zanim będzie to możliwe, należy zrozumieć ładunek JSON, który jest wysyłany z urządzenia do centrum zdarzeń. Można to zrobić przez przejrzenie tego [przykładowego schematu](../core/howto-export-data.md#telemetry-format) i zmodyfikowanie go w celu dopasowania go do schematu lub przy użyciu [Eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer) do inspekcji komunikatów. Jeśli używasz aplikacji do monitorowania ciągłego pacjenta, komunikaty będą wyglądać następująco:

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

1. Po sprawdzeniu ładunków w formacie JSON Wróć do projektanta aplikacji logiki i wybierz pozycję **+ nowy krok**. Wyszukaj i Dodaj **zmienną Initialize** w następnym kroku i wprowadź następujące parametry:

   |Parametr|Wartość|
   |---|---|
   |Nazwa|Nazwa interfejsu|
   |Typ|Ciąg|

   Wybierz pozycję **Zapisz**. 

1. Dodaj kolejną zmienną o nazwie **Body** z typem jako **ciąg**. Do aplikacji logiki zostaną dodane następujące akcje:

   :::image type="content" source="media/initialize-string-variables.png" alt-text="Inicjowanie zmiennych":::
    
1. Wybierz pozycję **+ nowy krok** i Dodaj akcję **Przeanalizuj dane JSON** . Zmień nazwę tego elementu na, aby **przeanalizować właściwości**. Dla zawartości wybierz **Właściwości** pochodzące z centrum zdarzeń. Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu** u dołu i wklej przykładowy ładunek z powyższej sekcji Właściwości.

1. Następnie wybierz akcję **Ustaw zmienną** i zaktualizuj zmienną **nazwy interfejsu** za pomocą **iothub-Interface-Name** z właściwości przeanalizowanego pliku JSON.

1. Dodaj kontrolkę **Split** jako następną akcję i wybierz zmienną **nazwa interfejsu** jako parametr on. Służy to do lejkowania danych do poprawnego zestawu danych.

1. W aplikacji IoT Central platformy Azure Znajdź nazwę interfejsu dla inteligentnych danych dotyczących kondycji poprawek oraz dane dotyczące kondycji inteligentnej, a następnie w widoku **Szablony urządzeń** . Utwórz dwa różne przypadki dla formantu **Switch** dla każdej nazwy interfejsu i odpowiednio zmień nazwę formantu. Możesz ustawić domyślną wielkość liter, aby użyć kontrolki **Przerwij** i wybrać stan, który ma być wyświetlany.

   :::image type="content" source="media/split-by-interface.png" alt-text="Podziel kontrolkę":::

1. W przypadku **poprawki "Najważniejsze znaczenie** " Dodaj akcję **Przeanalizuj dane JSON** . W polu zawartość wybierz **zawartość** pochodzącą z centrum zdarzeń. Skopiuj i wklej przykładowe ładunki w celu wygenerowania schematu.

1. Dodaj akcję **Ustaw zmienną** i zaktualizuj zmienną **treści** do **treści** z przeanalizowanego kodu JSON w kroku 7.

1. Dodaj kontrolkę **warunek** jako następną akcję i Ustaw warunek na **treść**, **zawiera**, **HeartRate**. Dzięki temu można upewnić się, że masz właściwy zestaw danych pochodzący z poprawki inteligentnej, przed wypełnieniem Power BI zestawu danych. Kroki 7-9 będą wyglądać następująco:

   :::image type="content" source="media/smart-vitals-pbi.png" alt-text="Warunki":::

1. W przypadku **prawdziwego** przypadku warunku Dodaj akcję, która wywołuje funkcję **Dodaj wiersze do zestawu danych** Power BI. Musisz zalogować się do Power BI. **Fałszywych** przypadków można ponownie użyć kontrolki **zakończenia** .

1. Wybierz odpowiedni **obszar roboczy**, **zestaw danych** i **tabelę**. Mapuj parametry, które zostały określone podczas tworzenia zestawu danych przesyłania strumieniowego w Power BI do przeanalizowanych wartości JSON, które pochodzą z centrum zdarzeń. Wypełnione akcje powinny wyglądać następująco:
 
   :::image type="content" source="media/add-rows-yesenia.png" alt-text="Dodaj wiersze do Power BI":::

1. W przypadku przełącznika "inteligentne" w **nawiasach klamrowych** Dodaj akcję **Przeanalizuj dane JSON** , aby przeanalizować zawartość, podobną do kroku 7. Następnie **Dodaj wiersze do zestawu danych** , aby zaktualizować zestaw danych Silver Teddy w Power BI.

   :::image type="content" source="media/knee-brace-pbi.png" alt-text="Zrzut ekranu pokazujący sposób dodawania wierszy do zestawów danych":::

1. Naciśnij pozycję **Zapisz** , a następnie uruchom aplikację logiki.


## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Tworzenie pulpitu nawigacyjnego w czasie rzeczywistym na potrzeby najważniejszych elementów pacjenta

Teraz wróć do Power BI i wybierz pozycję **+ Utwórz** , aby utworzyć nowy **pulpit nawigacyjny**. Nadaj pulpitowi nawigacyjnemu nazwę i naciśnij przycisk **Utwórz**.

Wybierz trzy kropki na górnym pasku nawigacyjnym, a następnie wybierz pozycję **+ Dodaj kafelek**.

:::image type="content" source="media/add-tile.png" alt-text="Dodaj kafelek do pulpitu nawigacyjnego":::

Wybierz typ kafelka, który chcesz dodać i dostosować swoją aplikację.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń zasoby, wykonując następujące czynności:

1. Z Azure Portal można usunąć utworzone centrum zdarzeń i zasoby Logic Apps.

1. W przypadku aplikacji IoT Central przejdź do karty Administracja i wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wskazówki dotyczące architektury monitorowania ciągłego pacjenta](concept-continuous-patient-monitoring-architecture.md)