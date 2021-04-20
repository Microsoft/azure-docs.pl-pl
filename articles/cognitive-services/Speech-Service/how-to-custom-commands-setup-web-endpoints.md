---
title: Konfigurowanie internetowych punktów końcowych
titleSuffix: Azure Cognitive Services
description: Konfigurowanie internetowych punktów końcowych dla Polecenia niestandardowe
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5f1d5318140dd14c5024e8dd3ad0def0afc7f378
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725912"
---
# <a name="set-up-web-endpoints"></a>Konfigurowanie internetowych punktów końcowych

W tym artykule dowiesz się, jak skonfigurować internetowe punkty końcowe w aplikacji Polecenia niestandardowe umożliwiającej wykonywanie żądań HTTP z aplikacji klienckiej. Wykonasz następujące zadania:

- Konfigurowanie internetowych punktów końcowych dla aplikacji Polecenia niestandardowe
- Wywoływanie internetowych punktów końcowych dla aplikacji Polecenia niestandardowe
- Odbieranie odpowiedzi z internetowych punktów końcowych
- Integrowanie odpowiedzi z internetowych punktów końcowych w niestandardowym ładunku JSON, wysyłanie i wizualizowanie ich z aplikacji klienckiej zestawu SDK usługi Mowa na platformie uniwersalnej systemu Windows w języku C#

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Klucz subskrypcji platformy Azure dla usługi Mowa: [Pobierz bezpłatnie](overview.md#try-the-speech-service-for-free) lub utwórz go w witrynie [Azure Portal](https://portal.azure.com)
> * Aplikacja Polecenia niestandardowe (zobacz [Tworzenie asystenta głosowego przy użyciu Polecenia niestandardowe](quickstart-custom-commands-application.md))
> * Aplikacja kliency z obsługą zestawu SPEECH SDK (zobacz [Integracja z aplikacją kliencyjną przy użyciu zestawu SPEECH SDK)](how-to-custom-commands-setup-speech-sdk.md)

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Wdrażanie zewnętrznego internetowego punktu końcowego przy użyciu aplikacji funkcji platformy Azure

Na potrzeby tego samouczka potrzebujesz punktu końcowego HTTP, który przechowuje stany dla wszystkich urządzeń, które są ustawione w poleceniu **TurnOnOff** Polecenia niestandardowe aplikacji.

Jeśli masz już internetowy punkt końcowy, który chcesz wywołać, przejdź do [następnej sekcji](#setup-web-endpoints-in-custom-commands). Alternatywnie następna sekcja zawiera szczegółowe informacje o domyślnym hostowanych punktach końcowych sieci Web, których można użyć, jeśli chcesz pominąć tę sekcję.

### <a name="input-format-of-azure-function"></a>Format danych wejściowych funkcji platformy Azure

Następnie wdrożysz punkt końcowy przy [użyciu](../../azure-functions/index.yml)Azure Functions .
Poniżej przedstawiono format zdarzenia Polecenia niestandardowe przekazywanego do funkcji platformy Azure. Użyj tych informacji podczas pisania aplikacji funkcji platformy Azure.

```json
{
  "conversationId": "string",
  "currentCommand": {
    "name": "string",
    "parameters": {
      "SomeParameterName": "string",
      "SomeOtherParameterName": "string"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "string",
      "SomeOtherGlobalParameterName": "string"
  }
}
```

    
W poniższej tabeli opisano kluczowe atrybuty tych danych wejściowych:
        
| Atrybut | Wyjaśnienie |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | Unikatowy identyfikator konwersacji. Należy pamiętać, że ten identyfikator może zostać wygenerowany przez aplikację kliencyjną. |
| **currentCommand** | Polecenie, które jest obecnie aktywne w konwersacji. |
| **Nazwa** | Nazwa polecenia. Atrybut `parameters` jest mapą z bieżącymi wartościami parametrów. |
| **currentGlobalParameters** | Mapa, na przykład `parameters` , ale używana dla parametrów globalnych. |


W przypadku funkcji platformy Azure **DeviceState** przykładowe Polecenia niestandardowe będzie wyglądać następująco. Będzie ona działać jako **dane wejściowe** dla aplikacji funkcji.
    
```json
{
  "conversationId": "someConversationId",
  "currentCommand": {
    "name": "TurnOnOff",
    "parameters": {
      "item": "tv",
      "value": "on"
    }
  }
}
```

### <a name="azure-function-output-for-a-custom-command-app"></a>Dane wyjściowe funkcji platformy Azure dla aplikacji polecenia niestandardowego

Jeśli dane wyjściowe funkcji platformy Azure są używane przez aplikację Polecenia niestandardowe, powinny pojawić się w następującym formacie. Aby [uzyskać szczegółowe informacje, zobacz Aktualizowanie](./how-to-custom-commands-update-command-from-web-endpoint.md) polecenia z internetowego punktu końcowego.

```json
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

### <a name="azure-function-output-for-a-client-application"></a>Dane wyjściowe funkcji platformy Azure dla aplikacji klienckiej

Jeśli dane wyjściowe funkcji platformy Azure są używane przez aplikację kliencyjną, dane wyjściowe mogą mieć postać wymaganą przez aplikację kliencową.

W przypadku naszego punktu końcowego **DeviceState** dane wyjściowe funkcji platformy Azure są używane przez aplikację kliencową, a Polecenia niestandardowe aplikację. Przykładowe dane wyjściowe funkcji platformy Azure powinny wyglądać następująco:
    
```json
{
  "TV": "on",
  "Fan": "off"
}
``` 

Te dane wyjściowe powinny być zapisywane w magazynie zewnętrznym, aby można było zachować stan urządzeń. Stan magazynu zewnętrznego będzie używany w poniższej sekcji Integracja z [aplikacją kliencową.](#integrate-with-client-application)


### <a name="deploy-azure-function"></a>Wdróż funkcję platformy Azure

Udostępniliśmy przykład, który można skonfigurować i wdrożyć jako Azure Functions aplikację. Aby utworzyć konto magazynu dla naszego przykładu, wykonaj następujące kroki.
 
1. Utwórz magazyn tabel, aby zapisać stan urządzenia. W Azure Portal utwórz nowy zasób typu **Konto magazynu według** nazwy **devicestate**.
1. Skopiuj wartość **Parametrów połączenia** z **wartości devicestate -> access keys**. Musisz dodać ten ciąg tajny do pobranego przykładowego kodu aplikacji funkcji.
1. Pobierz [przykładowy kod aplikacji funkcji.](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/custom-commands/quick-start)
1. Otwórz pobrane rozwiązanie w Visual Studio 2019 r. W **Connections.jsw programie** **zastąp STORAGE_ACCOUNT_SECRET_CONNECTION_STRING** kluczem tajnym z kroku 2.
1.  Pobierz kod **DeviceStateAzureFunction.**

Aby wdrożyć przykładową aplikację w Azure Functions, wykonaj następujące kroki.

1. [Wdrażanie](../../azure-functions/index.yml) aplikacji Azure Functions aplikacji.
1. Poczekaj na powodzenie wdrożenia i przejdź do wdrożonego zasobu na Azure Portal. 
1. Wybierz **pozycję Funkcje** w okienku po lewej stronie, a następnie wybierz pozycję **DeviceState.**
1.  W nowym oknie wybierz pozycję **Kod i testowanie,** a następnie wybierz **pozycję Pobierz adres URL funkcji.**
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Konfigurowanie internetowych punktów końcowych w Polecenia niestandardowe

Podłączmy funkcję platformy Azure do istniejącej Polecenia niestandardowe aplikacji.
W tej sekcji użyjemy istniejącego domyślnego punktu końcowego **DeviceState.** Jeśli utworzono własny internetowy punkt końcowy przy użyciu funkcji platformy Azure lub w inny sposób, użyj go zamiast domyślnego `https://webendpointexample.azurewebsites.net/api/DeviceState` .

1. Otwórz utworzoną wcześniej aplikację Polecenia niestandardowe.
1. Przejdź do internetowego **punktu końcowego** i kliknij pozycję **Nowy internetowy punkt końcowy.**

   > [!div class="mx-imgBorder"]
   > ![Nowy internetowy punkt końcowy](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Nazwa | UpdateDeviceState | Nazwa internetowego punktu końcowego. |
   | Adres URL | https://webendpointexample.azurewebsites.net/api/DeviceState | Adres URL punktu końcowego, z którym ma się komunikować aplikacja Polecenia niestandardowe. |
   | Metoda | POST | Dozwolone interakcje (np. GET, POST) z punktem końcowym.|
   | Nagłówki | Klucz: aplikacja, Wartość: 8 pierwszych cyfr identyfikatora aplikacji | Parametry nagłówka do uwzględnienia w nagłówku żądania.|

    > [!NOTE]
    > - Przykładowy internetowy punkt końcowy utworzony przy [użyciu Azure Functions](../../azure-functions/index.yml), który podłącza się do bazy danych, która zapisuje stan urządzenia odbiornika i odbiornika.
    > - Sugerowany nagłówek jest wymagany tylko dla przykładowego punktu końcowego.
    > - Aby upewnić się, że wartość nagłówka jest unikatowa w naszym przykładowym punkcie końcowym, weź 8 pierwszych cyfr **swojego parametru applicationId.**
    > - W świecie rzeczywistym internetowy punkt końcowy może być punktem końcowym centrum [IOT,](../../iot-hub/about-iot-hub.md) które zarządza urządzeniami.

1. Kliknij pozycję **Zapisz**.

## <a name="call-web-endpoints"></a>Wywoływanie internetowych punktów końcowych

1. Przejdź do polecenia **TurnOnOff**, w regule ukończenia wybierz pozycję **ConfirmationResponse**, a następnie wybierz pozycję **Dodaj akcję**.
1. W obszarze **Nowa akcja — Typ** wybierz pozycję **Wywołaj internetowy punkt końcowy**
1. W obszarze **Edytuj akcję — Punkty końcowe** wybierz pozycję **UpdateDeviceState**, która jest utworzonym przez nas internetowym punktem końcowym.  
1. W obszarze **Konfiguracja** umieść następujące wartości:
   > [!div class="mx-imgBorder"]
   > ![Parametry akcji wywoływania internetowych punktów końcowych](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Punkty końcowe | UpdateDeviceState | Internetowy punkt końcowy, który ma zostać wywołany w ramach tej akcji. |
   | Parametry zapytania | item={SubjectDevice}&&value={OnOff} | Parametry zapytania, które mają zostać dołączone do adresu URL internetowego punktu końcowego.  |
   | Zawartość treści | Brak | Zawartość treści żądania. |

    > [!NOTE]
    > - Sugerowane parametry zapytania są wymagane tylko w przypadku przykładowego punktu końcowego

1. W obszarze **W przypadku powodzenia — Akcja do wykonania** wybierz pozycję **Wyślij odpowiedź na mowę**.

    W obszarze **Prosty edytor** wprowadź wartość `{SubjectDevice} is {OnOff}`.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający ekran W przypadku powodzenia — akcja do wykonania.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Akcja do wykonania | Wyślij odpowiedź na mowę | Akcja do wykonania, jeśli żądanie internetowego punktu końcowego zakończy się pomyślnie |

   > [!NOTE]
   > - Możesz również uzyskać dostęp do pól w odpowiedzi HTTP bezpośrednio, używając parametru `{YourWebEndpointName.FieldName}`. Na przykład: `{UpdateDeviceState.TV}`

1. W obszarze **W przypadku niepowodzenia — Akcja do wykonania** wybierz pozycję **Wyślij odpowiedź na mowę**

    W obszarze **Prosty edytor** wprowadź wartość `Sorry, {WebEndpointErrorMessage}`.

   > [!div class="mx-imgBorder"]
   > ![Akcja wywoływania internetowych punktów końcowych w przypadku niepowodzenia](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Akcja do wykonania | Wyślij odpowiedź na mowę | Akcja do wykonania, jeśli żądanie internetowego punktu końcowego zakończy się niepowodzeniem |

   > [!NOTE]
   > - Parametr `{WebEndpointErrorMessage}` jest opcjonalny. Możesz go usunąć, jeśli nie chcesz uwidaczniać żadnego komunikatu o błędzie.
   > - W naszym przykładowym punkcie końcowym wysyłamy odpowiedź HTTP ze szczegółowymi komunikatami o błędach dotyczącymi typowych błędów, takich jak brakujące parametry nagłówka.

### <a name="try-it-out-in-test-portal"></a>Wypróbowywanie działania w portalu testowym
- W odpowiedzi Powodzenie zapisz, wytrenuj i przetestuj.
   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający odpowiedź w przypadku powodzenia.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Na stronie Odpowiedź na niepowodzenie usuń jeden z parametrów zapytania, zapisz, przetrenuj i przetestuj.
   > [!div class="mx-imgBorder"]
   > ![Akcja wywoływania internetowych punktów końcowych w przypadku powodzenia](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integracja z aplikacją kliencką

W [akcję Polecenia niestandardowe wyślij do aplikacji klienckiej](./how-to-custom-commands-send-activity-to-client.md)dodano **akcję Wyślij działanie do** klienta. Aktywność jest wysyłana do aplikacji klienckiej, niezależnie od tego, czy akcja **Wywołaj internetowy punkt końcowy** zakończyła się pomyślnie.
Zazwyczaj jednak działanie jest wysyłane do aplikacji klienckiej tylko wtedy, gdy wywołanie internetowego punktu końcowego powiedzie się. W tym przykładzie ma to miejsce po pomyślnym zaktualizowaniu stanu urządzenia.

1. Usuń dodaną wcześniej akcję **Wyślij aktywność do klienta**.
1. Przeprowadź edycję wywołania internetowego punktu końcowego:
    1. W obszarze **Konfiguracja** upewnij się, że **Parametry zapytania** to `item={SubjectDevice}&&value={OnOff}`
    1. W obszarze **W przypadku powodzenia** zmień wartość pola **Akcja do wykonania** na **Wyślij aktywność do klienta**
    1. Skopiuj poniższy kod JSON do pola **Zawartość aktywności**
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
Teraz aktywność jest wysyłana do klienta tylko wtedy, gdy żądanie do internetowego punktu końcowego powiedzie się.

### <a name="create-visuals-for-syncing-device-state"></a>Tworzenie wizualizacji w celu synchronizowania stanu urządzenia

Dodaj następujący kod XML `MainPage.xaml` do pliku powyżej bloku **EnableMicrophoneButton.**

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Synchronizowanie stanu urządzeń

W pliku `MainPage.xaml.cs` dodaj odwołanie `using Windows.Web.Http;`. Dodaj poniższy kod do klasy `MainPage`. Ta metoda spowoduje wysłanie żądania GET do przykładowego punktu końcowego i wyodrębnienie bieżącego stanu urządzenia dla aplikacji. Pamiętaj, aby zmienić nagłówek na używany w nagłówku w `<your_app_name>` **internetowym** punkcie końcowym polecenia niestandardowego.

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request.
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Czas to wypróbować

1. Uruchom aplikację.
1. Kliknij pozycję Synchronizuj stan urządzenia.\
Jeśli aplikacja została przetestowana przy użyciu programu `turn on tv` w poprzedniej sekcji, ekran tv będzie wyświetlony tak jak w **sekcji**.
    > [!div class="mx-imgBorder"]
    > ![Synchronizowanie stanu urządzeń](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Wybierz pozycję **Włącz mikrofon.**
1. Wybierz przycisk **Mów.**
1. Powiedzmy `turn on the fan` . Stan wizualny wentylatora powinien zmienić się na **na .**
    > [!div class="mx-imgBorder"]
    > ![Włączanie wentylatora](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Eksportowanie Polecenia niestandardowe jako umiejętności zdalnej](./how-to-custom-commands-integrate-remote-skills.md)
