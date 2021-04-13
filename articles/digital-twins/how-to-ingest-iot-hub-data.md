---
title: Pozyskiwanie danych telemetrycznych z usługi IoT Hub
titleSuffix: Azure Digital Twins
description: Zobacz, jak pozyskiwanie komunikatów telemetrycznych urządzenia z IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a5e00ef81afc709a9072eedbb07983057f57eb08
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304306"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Pozyskiwanie danych telemetrycznych IoT Hub na platformie Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji jest oparta na danych z urządzeń IoT i innych źródeł. Typowym źródłem danych urządzenia do użycia w usłudze Azure Digital bliźniaczych reprezentacji jest [IoT Hub](../iot-hub/about-iot-hub.md).

Proces pozyskiwania danych w usłudze Azure Digital bliźniaczych reprezentacji polega na skonfigurowaniu zewnętrznego zasobu obliczeniowego, takiego jak funkcja, która została wykonana przy użyciu [Azure Functions](../azure-functions/functions-overview.md). Funkcja otrzymuje dane i używa [interfejsów API DigitalTwins](/rest/api/digital-twins/dataplane/twins) do ustawiania właściwości lub zdarzeń telemetrii na potrzeby [cyfrowego bliźniaczych reprezentacji](concepts-twins-graph.md) odpowiednio. 

Ten dokument zawiera instrukcje dotyczące tworzenia funkcji, która może pozyskać dane telemetryczne z IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem tego przykładu należy skonfigurować następujące zasoby jako wymagania wstępne:
* **Centrum IoT**. Aby uzyskać instrukcje, zobacz sekcję *tworzenie IoT Hub* w [tym IoT Hub przewodniku szybki start](../iot-hub/quickstart-send-telemetry-cli.md).
* **Wystąpienie usługi Azure Digital bliźniaczych reprezentacji** , które będzie odbierać dane telemetryczne urządzenia. Aby uzyskać instrukcje, zobacz [*How to: set up the Azure Digital bliźniaczych reprezentacji instance and Authentication*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Przykładowy scenariusz telemetrii

W tym temacie opisano sposób wysyłania komunikatów z IoT Hub do usługi Azure Digital bliźniaczych reprezentacji przy użyciu funkcji platformy Azure. Istnieje wiele możliwych konfiguracji i strategie dopasowania, których można użyć do wysyłania wiadomości, ale przykład tego artykułu zawiera następujące części:
* Urządzenie termostatu w IoT Hub ze znanym IDENTYFIKATORem urządzenia
* Cyfrowa dwuosiowa do reprezentowania urządzenia, o zgodnym IDENTYFIKATORze

> [!NOTE]
> W tym przykładzie zastosowano prostą zgodność identyfikatora między IDENTYFIKATORem urządzenia i odpowiadającym mu IDENTYFIKATORem cyfrowej sznurka, ale można zapewnić bardziej zaawansowane mapowania z urządzenia do jego sznurka (na przykład z tabelą mapowania).

Za każdym razem, gdy zdarzenie telemetrii temperatury jest wysyłane przez urządzenie termostatu, funkcja przetwarza dane telemetryczne i Właściwość *temperatura* cyfr cyfrowych. Ten scenariusz przedstawiono na poniższym diagramie:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagram IoT Hub urządzenie wysyłające dane telemetryczne dotyczące temperatury poprzez IoT Hub do funkcji na platformie Azure, która aktualizuje właściwość temperatury na sznurze w usłudze Azure Digital bliźniaczych reprezentacji." border="false":::

## <a name="add-a-model-and-twin"></a>Dodawanie modelu i reprezentacji bliźniaczej

W tej sekcji opisano konfigurowanie [cyfrowej sieci dwucyfrowej](concepts-twins-graph.md) w usłudze Azure Digital bliźniaczych reprezentacji, która będzie reprezentować urządzenie termostatu i zostanie zaktualizowane informacjami z IoT Hub.

Aby utworzyć sznurek termostatu, należy najpierw przekazać [model](concepts-models.md) termostatu do wystąpienia, który opisuje właściwości termostatu i będzie później używany do tworzenia sznurka.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Następnie należy **utworzyć jedną sznurek przy użyciu tego modelu**. Użyj poniższego polecenia, aby utworzyć dwuosiowy termostat o nazwie **thermostat67**, a następnie ustaw 0,0 jako początkową wartość temperatury.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

> [!Note]
> Jeśli używasz Cloud Shell w środowisku programu PowerShell, może być konieczne wypróbowanie znaków cudzysłowu w wbudowanych polach JSON, aby ich wartości były analizowane prawidłowo. Oto polecenie, aby utworzyć sznurek z tą modyfikacją:
>
> Utwórz sznurek:
> ```azurecli-interactive
> az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{\"Temperature\": 0.0,}' --dt-name {digital_twins_instance_name}
> ```

Po pomyślnym utworzeniu dwuosiowego dane wyjściowe interfejsu wiersza polecenia powinny wyglądać następująco:
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

W tej sekcji utworzysz funkcję platformy Azure, która umożliwia dostęp do usługi Azure Digital bliźniaczych reprezentacji i aktualizowanie bliźniaczych reprezentacji na podstawie zdarzeń telemetrii IoT, które odbiera. Wykonaj poniższe kroki, aby utworzyć i opublikować funkcję.

#### <a name="step-1-create-a-function-app-project"></a>Krok 1. Tworzenie projektu aplikacji funkcji

Najpierw utwórz nowy projekt aplikacji funkcji w programie Visual Studio. Aby uzyskać instrukcje, jak to zrobić, zobacz sekcję [**Tworzenie aplikacji funkcji w programie Visual Studio w**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) sekcji *instrukcje: Konfigurowanie funkcji do przetwarzania artykułu danych* .

#### <a name="step-2-fill-in-function-code"></a>Krok 2. wypełnienie kodu funkcji

Dodaj następujące pakiety do projektu:
* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [Microsoft. Azure. WebJobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

Zmień nazwę funkcji przykładowej *Function1. cs* wygenerowanej przez program Visual Studio z nowym projektem na *IoTHubtoTwins. cs*. Zastąp kod w pliku następującym kodem:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Zapisz kod funkcji.

#### <a name="step-3-publish-the-function-app-to-azure"></a>Krok 3. publikowanie aplikacji funkcji na platformie Azure

Opublikuj projekt przy użyciu funkcji *IoTHubtoTwins. cs* w aplikacji funkcji na platformie Azure.

Aby uzyskać instrukcje, jak to zrobić, zapoznaj się z sekcją [**publikowanie aplikacji funkcji na platformie Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) *: Konfigurowanie funkcji do przetwarzania artykułu danych* .

#### <a name="step-4-configure-the-function-app"></a>Krok 4. Konfigurowanie aplikacji funkcji

Następnie **Przypisz rolę dostępu** dla funkcji i **Skonfiguruj ustawienia aplikacji** tak, aby mogły uzyskiwać dostęp do wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Aby uzyskać instrukcje, jak to zrobić, zapoznaj się z sekcją [**Konfigurowanie dostępu zabezpieczeń do aplikacji funkcji**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) w artykule wykonywanie instrukcji *: Konfigurowanie funkcji do przetwarzania danych* .

## <a name="connect-your-function-to-iot-hub"></a>Połącz funkcję z IoT Hub

W tej sekcji skonfigurujesz funkcję jako miejsce docelowe zdarzenia dla danych urządzenia usługi IoT Hub. Zapewni to, że dane z urządzenia termostatu w IoT Hub będą wysyłane do funkcji platformy Azure w celu przetworzenia.

W [Azure Portal](https://portal.azure.com/)przejdź do wystąpienia IoT Hub utworzonego w sekcji [*wymagania wstępne*](#prerequisites) . W obszarze **zdarzenia** Utwórz subskrypcję funkcji.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, który pokazuje Dodawanie subskrypcji zdarzeń.":::

Na stronie **Tworzenie subskrypcji zdarzeń** Wypełnij pola w następujący sposób:
  1. W polu **Nazwa** wybierz dowolną nazwę dla subskrypcji zdarzenia.
  2. W obszarze **schemat zdarzenia** wybierz pozycję _Event Grid schemat_.
  3. W obszarze **nazwa tematu systemu** wybierz dowolną żądaną nazwę.
  1. W polu **Filtruj do typów zdarzeń** zaznacz pole wyboru dane _telemetryczne urządzenia_ i usuń zaznaczenie pozycji inne typy zdarzeń.
  1. W obszarze **Typ punktu końcowego** wybierz pozycję _Funkcja platformy Azure_.
  1. W przypadku **punktu końcowego** Użyj linku _Wybierz punkt końcowy_ , aby wybrać funkcję platformy Azure, która ma być używana dla punktu końcowego.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, aby utworzyć szczegóły subskrypcji zdarzeń":::

Na stronie _Wybierz funkcję platformy Azure_ , która zostanie otwarta, sprawdź lub Wypełnij poniżej szczegóły.
 1. **Subskrypcja**: Twoja subskrypcja platformy Azure.
 2. **Grupa zasobów**: Grupa zasobów.
 3. **Aplikacja funkcji**: Nazwa aplikacji funkcji.
 4. **Gniazdo**: _produkcja_.
 5. **Funkcja**: wybierz funkcję z wcześniejszych, *IoTHubtoTwins* z listy rozwijanej.

Zapisz szczegóły przy użyciu przycisku _Potwierdź wybór_ .            
      
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
