---
title: Pozyskiwanie danych telemetrycznych z usługi IoT Hub
titleSuffix: Azure Digital Twins
description: Zobacz, jak pozyskiwanie komunikatów telemetrycznych urządzenia z IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9ecc14aa9591d6e62dccd9899a80de44411928a1
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051092"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Pozyskiwanie danych telemetrycznych IoT Hub na platformie Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji jest oparta na danych z urządzeń IoT i innych źródeł. Typowym źródłem danych urządzenia do użycia w usłudze Azure Digital bliźniaczych reprezentacji jest [IoT Hub](../iot-hub/about-iot-hub.md).

Proces pozyskiwania danych w usłudze Azure Digital bliźniaczych reprezentacji polega na skonfigurowaniu zewnętrznego zasobu obliczeniowego, takiego jak funkcja, która została wykonana przy użyciu [Azure Functions](../azure-functions/functions-overview.md). Funkcja otrzymuje dane i używa [interfejsów API DigitalTwins](/rest/api/digital-twins/dataplane/twins) do ustawiania właściwości lub zdarzeń telemetrii na potrzeby [cyfrowego bliźniaczych reprezentacji](concepts-twins-graph.md) odpowiednio. 

Ten dokument zawiera instrukcje dotyczące tworzenia funkcji, która może pozyskać dane telemetryczne z IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem tego przykładu należy skonfigurować następujące zasoby jako wymagania wstępne:
* **Centrum IoT**. Aby uzyskać instrukcje, zobacz sekcję *tworzenie IoT Hub* w [tym IoT Hub przewodniku szybki start](../iot-hub/quickstart-send-telemetry-cli.md).
* **Funkcja** z odpowiednimi uprawnieniami do wywoływania swojego wystąpienia cyfrowej pojedynczej sieci. Aby uzyskać instrukcje, zobacz [*How to: set up a function in Azure for processing Data*](how-to-create-azure-function.md). 
* **Wystąpienie usługi Azure Digital bliźniaczych reprezentacji** , które będzie odbierać dane telemetryczne urządzenia. Aby uzyskać instrukcje, zobacz [*How to: set up the Azure Digital bliźniaczych reprezentacji instance and Authentication*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Przykładowy scenariusz telemetrii

W tym temacie opisano sposób wysyłania komunikatów z IoT Hub do usługi Azure Digital bliźniaczych reprezentacji przy użyciu funkcji platformy Azure. Istnieje wiele możliwych konfiguracji i strategie dopasowania, których można użyć do wysyłania wiadomości, ale przykład tego artykułu zawiera następujące części:
* Urządzenie termometru w IoT Hub ze znanym IDENTYFIKATORem urządzenia
* Cyfrowa dwuosiowa do reprezentowania urządzenia, o zgodnym IDENTYFIKATORze

> [!NOTE]
> W tym przykładzie zastosowano prostą zgodność identyfikatora między IDENTYFIKATORem urządzenia i odpowiadającym mu IDENTYFIKATORem cyfrowej sznurka, ale można zapewnić bardziej zaawansowane mapowania z urządzenia do jego sznurka (na przykład z tabelą mapowania).

Za każdym razem, gdy zdarzenie telemetrii temperatury jest wysyłane przez urządzenie termostatu, funkcja przetwarza dane telemetryczne i Właściwość *temperatura* cyfr cyfrowych. Ten scenariusz przedstawiono na poniższym diagramie:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagram przedstawiający wykres przepływu. Na wykresie urządzenie IoT Hub wysyła dane telemetryczne dotyczące temperatury poprzez IoT Hub do funkcji na platformie Azure, która aktualizuje właściwość temperatury na sznurze w usłudze Azure Digital bliźniaczych reprezentacji." border="false":::

## <a name="add-a-model-and-twin"></a>Dodaj model i sznurki

Możesz dodać/przekazać model przy użyciu poniższego polecenia CLI, a następnie utworzyć dwuosiowy przy użyciu tego modelu, który zostanie zaktualizowany informacjami z IoT Hub.

Model wygląda następująco:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Aby **przekazać ten model do wystąpienia usługi bliźniaczych reprezentacji**, Otwórz interfejs wiersza polecenia platformy Azure i uruchom następujące polecenie:

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Następnie należy **utworzyć jedną sznurek przy użyciu tego modelu**. Użyj następującego polecenia, aby utworzyć dwuosiową i ustawić 0,0 jako początkową wartość temperatury.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

Dane wyjściowe pomyślnego utworzenia dwuosiowego polecenia powinny wyglądać następująco:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Tworzenie funkcji

Ta sekcja używa tych samych kroków uruchamiania i szkieletu funkcji programu Visual Studio, które są [*następujące: Konfigurowanie funkcji do przetwarzania danych*](how-to-create-azure-function.md). Szkielet obsługuje uwierzytelnianie i tworzy klienta usługi, gotowy do przetwarzania danych i wywoływania interfejsów API Digital bliźniaczych reprezentacji w odpowiedzi na platformie Azure. 

W kolejnych krokach dodasz do niego konkretny kod służący do przetwarzania zdarzeń telemetrii IoT z IoT Hub.  

### <a name="add-telemetry-processing"></a>Dodaj przetwarzanie danych telemetrycznych
    
Zdarzenia telemetrii są dostarczane w formie komunikatów z urządzenia. Pierwszym krokiem w dodawaniu kodu przetwarzania danych telemetrycznych jest wyodrębnienie odpowiedniej części tego komunikatu urządzenia ze zdarzenia Event Grid. 

Różne urządzenia mogą być w inny sposób struktury swoich komunikatów, więc kod dla **tego kroku zależy od podłączonego urządzenia.** 

Poniższy kod przedstawia przykład prostego urządzenia, które wysyła dane telemetryczne jako kod JSON. Ten przykład jest w pełni omawiany w [*samouczku: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md). Poniższy kod umożliwia znalezienie identyfikatora urządzenia, które wysłało komunikat, a także wartości temperatury.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Find_device_ID_and_temperature":::

Następny przykład kodu przyjmuje identyfikator i wartość temperatury i używa ich do "poprawki" (do) tego sznurka.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Update_twin_with_device_temperature":::

### <a name="update-your-function-code"></a>Aktualizowanie kodu funkcji

Teraz, gdy rozumiesz kod z wcześniejszych przykładów, Otwórz funkcję z sekcji [*wymagania wstępne*](#prerequisites) w programie Visual Studio. (Jeśli nie masz funkcji, która została utworzona na platformie Azure, przejdź do linku w sekcji wymagania wstępne, aby utworzyć ją teraz).

Zamień kod funkcji na ten przykładowy kod.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Zapisz kod funkcji i Opublikuj aplikację funkcji na platformie Azure. Aby dowiedzieć się, jak to zrobić, zobacz temat [*publikowanie aplikacji funkcji*](./how-to-create-azure-function.md#publish-the-function-app-to-azure) w artykule [*jak skonfigurować funkcję na platformie Azure do przetwarzania danych*](how-to-create-azure-function.md).

Po pomyślnym opublikowaniu dane wyjściowe będą widoczne w oknie poleceń programu Visual Studio, jak pokazano poniżej:

```cmd
1>------ Build started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
1>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>------ Publish started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\Out\
2>Publishing C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\adtIngestFunctionSample - 20200911112545669.zip to https://adtingestfunctionsample20200818134346.scm.azurewebsites.net/api/zipdeploy...
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
========== Publish: 1 succeeded, 0 failed, 0 skipped ==========
```
Możesz również sprawdzić stan procesu publikowania w [Azure Portal](https://portal.azure.com/). Wyszukaj _grupę zasobów_ i przejdź do _dziennika aktywności_ i poszukaj w obszarze _Pobierz profil publikowania aplikacji sieci Web_ na liście i sprawdź, czy stan został zakończony pomyślnie.

:::image type="content" source="media/how-to-ingest-iot-hub-data/azure-function-publish-activity-log.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, który pokazuje stan procesu publikowania.":::

## <a name="connect-your-function-to-iot-hub"></a>Połącz funkcję z IoT Hub

Skonfiguruj miejsce docelowe zdarzenia dla danych centrum.
W [Azure Portal](https://portal.azure.com/)przejdź do wystąpienia IoT Hub utworzonego w sekcji [*wymagania wstępne*](#prerequisites) . W obszarze **zdarzenia** Utwórz subskrypcję funkcji.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, który pokazuje Dodawanie subskrypcji zdarzeń.":::

Na stronie **Tworzenie subskrypcji zdarzeń** Wypełnij pola w następujący sposób:
  1. W polu **Nazwa** Nazwij subskrypcję, którą chcesz.
  2. W obszarze **schemat zdarzenia** wybierz pozycję _Event Grid schemat_.
  3. W obszarze **typy zdarzeń** zaznacz pole wyboru dane _telemetryczne urządzenia_ i usuń zaznaczenie pozycji inne typy zdarzeń.
  4. W obszarze **Typ punktu końcowego** wybierz pozycję _Funkcja platformy Azure_.
  5. W obszarze **punkt końcowy** wybierz łącze _Wybierz punkt końcowy_ , aby utworzyć punkt końcowy.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, aby utworzyć szczegóły subskrypcji zdarzeń":::

Na stronie _Wybierz funkcję platformy Azure_ , która zostanie otwarta, sprawdź poniższe szczegóły.
 1. **Subskrypcja**: Subskrypcja platformy Azure
 2. **Grupa zasobów**: Grupa zasobów
 3. **Aplikacja funkcji**: Nazwa aplikacji funkcji
 4. **Gniazdo**: _produkcja_
 5. **Funkcja**: wybierz funkcję z listy rozwijanej.

Zapisz szczegóły, wybierając przycisk _Potwierdź wybór_ .            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, aby wybrać funkcję.":::

Wybierz przycisk _Utwórz_ , aby utworzyć subskrypcję zdarzeń.

## <a name="send-simulated-iot-data"></a>Wysyłanie symulowanych danych IoT

Aby przetestować nową funkcję transferu danych przychodzących, użyj symulatora urządzeń z [*samouczka: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md). Ten samouczek jest oparty na przykładowym projekcie pisanym w języku C#. Przykładowy kod znajduje się tutaj: [kompleksowe bliźniaczych reprezentacji na platformie Azure](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Będziesz używać projektu **DeviceSimulator** w tym repozytorium.

W kompleksowym samouczku wykonaj następujące czynności:
1. [*Rejestrowanie symulowanego urządzenia za pomocą IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Konfigurowanie i uruchamianie symulacji*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Weryfikowanie wyników

W przypadku korzystania z symulatora urządzenia powyżej wartość temperatury dwuosiowej sieci cyfrowej zostanie zmieniona. W interfejsie wiersza polecenia platformy Azure Uruchom następujące polecenie, aby wyświetlić wartość temperatury.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

Dane wyjściowe powinny zawierać wartość temperatury podobną do:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Aby wyświetlić zmianę wartości, należy wielokrotnie uruchomić polecenie zapytania powyżej.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z informacjami dotyczącymi transferu danych przychodzących i wychodzących przy użyciu usługi Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: integracja z innymi usługami*](concepts-integration.md)
