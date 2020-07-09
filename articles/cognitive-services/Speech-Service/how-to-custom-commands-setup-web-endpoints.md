---
title: Konfigurowanie punktów końcowych sieci Web (wersja zapoznawcza)
titleSuffix: Azure Cognitive Services
description: Konfigurowanie punktów końcowych sieci Web dla poleceń niestandardowych
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 0197bb81fdba8bab20742d95aebaa2028bb90c18
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027685"
---
# <a name="set-up-web-endpoints"></a>Konfigurowanie internetowych punktów końcowych

W tym artykule dowiesz się, jak skonfigurować punkty końcowe sieci Web w aplikacji poleceń niestandardowych, która umożliwia wykonywanie żądań HTTP z aplikacji klienckiej. Zostaną wykonane następujące zadania:

- Konfigurowanie punktów końcowych sieci Web w aplikacji poleceń niestandardowych
- Wywoływanie punktów końcowych sieci Web w aplikacji poleceń niestandardowych
- Odbieranie odpowiedzi dla punktów końcowych sieci Web 
- Zintegruj odpowiedź punktów końcowych sieci Web w niestandardowym ładunku JSON, Wyślij i Wizualizuj ją z aplikacji klienckiej zestawu SDK języka C# platformy UWP

## <a name="prerequisites"></a>Wymagania wstępne
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Klucz subskrypcji platformy Azure dla usługi mowy: [Uzyskaj jeden bezpłatnie](get-started.md) lub utwórz go na [Azure Portal](https://portal.azure.com)
> * Wcześniej [utworzona aplikacja poleceń niestandardowych](quickstart-custom-commands-application.md)
> * Aplikacja kliencka [z](./how-to-custom-commands-setup-speech-sdk.md) włączonym zestawem SDK

## <a name="setup-web-endpoints"></a>Skonfiguruj punkty końcowe sieci Web

1. Otwórz wcześniej utworzoną aplikację poleceń niestandardowych. 
1. Przejdź do pozycji "punkty końcowe sieci Web", a następnie kliknij pozycję "nowy punkt końcowy sieci Web".

   > [!div class="mx-imgBorder"]
   > ![Nowy internetowy punkt końcowy](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Nazwa | UpdateDeviceState | Nazwa punktu końcowego sieci Web. |
   | Adres URL | https://webendpointexample.azurewebsites.net/api/DeviceState | Adres URL punktu końcowego, z którym aplikacja polecenia niestandardowego ma komunikować się. |
   | Metoda | POST | Dozwolone interakcje (takie jak GET, POST) z punktem końcowym.|
   | Nagłówki | Klucz: App, wartość: Weź pierwsze 8 cyfr Twojej aplikacji | Parametry nagłówka do uwzględnienia w nagłówku żądania.|

    > [!NOTE]
    > - Przykładowy punkt końcowy sieci Web utworzony przy użyciu [funkcji platformy Azure](https://docs.microsoft.com/azure/azure-functions/), który łączy się z bazą danych, która zapisuje stan urządzenia telewizora i wentylatoru
    > - Sugerowany nagłówek jest wymagany tylko w przypadku przykładowego punktu końcowego
    > - Aby upewnić się, że wartość nagłówka jest unikatowa w naszym przykładowym punkcie końcowym, weź pierwsze 8 cyfr Twojej aplikacji
    > - W świecie rzeczywistym punktem końcowym sieci Web może być punkt końcowy [Centrum IoT](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) , które zarządza urządzeniami.

1. Kliknij pozycję **Zapisz**.

## <a name="call-web-endpoints"></a>Wywołaj punkty końcowe sieci Web

1. Przejdź do **TurnOnOff** polecenie, wybierz pozycję **ConfirmationResponse** w obszarze reguła ukończenia, a następnie wybierz pozycję **Dodaj akcję**.
1. W obszarze **nowy typ akcji**wybierz pozycję **Wywołaj punkt końcowy sieci Web**
1. W obszarze **Edytuj akcję — punkty końcowe**wybierz pozycję **UpdateDeviceState**, która jest utworzonym przez siebie punktem końcowym sieci Web.  
1. W obszarze **Konfiguracja**należy umieścić następujące wartości: 
   > [!div class="mx-imgBorder"]
   > ![Parametry akcji dla wywołań punktów końcowych sieci Web](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Punkty końcowe | UpdateDeviceState | Punkt końcowy sieci Web, który ma zostać wywołany przez tę akcję. |
   | Parametry zapytania | element = {SubjectDevice} &&wartość = {OnOff} | Parametry zapytania do dołączenia do adresu URL punktu końcowego sieci Web.  |
   | Zawartość treści | Nie dotyczy | Treść żądania. |

    > [!NOTE]
    > - Sugestie parametrów zapytania są zbędne tylko dla przykładowego punktu końcowego

1. W obszarze **po powodzeniu akcja do wykonania**wybierz pozycję **Wyślij odpowiedź na mowę**.
    
    W **prostym edytorze**wprowadź `{SubjectDevice} is {OnOff}` .
   
   > [!div class="mx-imgBorder"]
   > ![Wywołaj akcję punktów końcowych sieci Web po powodzeniu](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Akcja do wykonania | Wyślij odpowiedź na mowę | Akcja do wykonania, jeśli żądanie do punktu końcowego sieci Web zakończy się pomyślnie |
   
   > [!NOTE]
   > - Można również bezpośrednio uzyskać dostęp do pól w odpowiedzi HTTP przy użyciu `{YourWebEndpointName.FieldName}` . Na przykład: `{UpdateDeviceState.TV}`

1. W przypadku **niepowodzenia akcja do wykonania**wybierz pozycję **Wyślij odpowiedź na mowę**

    W **prostym edytorze**wprowadź `Sorry, {WebEndpointErrorMessage}` .

   > [!div class="mx-imgBorder"]
   > ![Akcja wywołania punktów końcowych sieci Web w przypadku niepowodzenia](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Akcja do wykonania | Wyślij odpowiedź na mowę | Akcja do wykonania, jeśli żądanie do punktu końcowego sieci Web zakończy się niepowodzeniem |

   > [!NOTE]
   > - Parametr `{WebEndpointErrorMessage}` jest opcjonalny. Możesz go usunąć, jeśli nie chcesz ujawniać żadnego komunikatu o błędzie.
   > - W naszym przykładowym punkcie końcowym wysyłamy odpowiedź HTTP z szczegółowymi komunikatami o błędach dla typowych błędów, takich jak brakujące parametry nagłówka. 

### <a name="try-it-out-in-test-portal"></a>Wypróbuj go w portalu testowym
- W odpowiedzi na sukces \
Zapisywanie, uczenie i testowanie
   > [!div class="mx-imgBorder"]
   > ![Wywołaj akcję punktów końcowych sieci Web po powodzeniu](media/custom-commands/setup-web-endpoint-on-success-response.png)
- W przypadku niepowodzenia odpowiedzi \
Usuń jedno z parametrów zapytania, Zapisz, Przeszkol i Testuj
   > [!div class="mx-imgBorder"]
   > ![Wywołaj akcję punktów końcowych sieci Web po powodzeniu](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integracja z aplikacją kliencką

W [instrukcje: wysyłanie działania do aplikacji klienckiej (wersja zapoznawcza)](./how-to-custom-commands-send-activity-to-client.md)dodano akcję **Wyślij do klienta** . Działanie jest wysyłane do aplikacji klienckiej bez względu na to, czy **wywołanie akcji punktu końcowego sieci Web** zakończyło się pomyślnie.
Jednak w większości przypadków wysłanie działania do aplikacji klienckiej jest możliwe dopiero po pomyślnym nawiązaniu połączenia z punktem końcowym sieci Web. W tym przykładzie jest to po pomyślnym zaktualizowaniu stanu urządzenia.

1. Usuń akcję **Wyślij działanie do klienta** , która została wcześniej dodana.
1. Edytuj punkt końcowy sieci Web wywołania: 
    1. W obszarze **Konfiguracja**upewnij się, że **parametry zapytania** to`item={SubjectDevice}&&value={OnOff}`
    1. W przypadku **powodzenia**Zmień **akcję na wykonaj** , aby **wysłać działanie do klienta**
    1. Skopiuj poniższy kod JSON do **zawartości działania**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
    > [!div class="mx-imgBorder"]
    > ![Wyślij działanie po pomyślnym](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
Teraz wysyłasz działanie do klienta, gdy żądanie do punktu końcowego sieci Web zakończy się pomyślnie.

### <a name="create-visuals-for-syncing-device-state"></a>Tworzenie wizualizacji do synchronizowania stanu urządzenia
Dodaj następujący kod XML do `MainPage.xaml` `"EnableMicrophoneButton"` bloku.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Synchronizuj stan urządzenia 

W `MainPage.xaml.cs` , Dodaj odwołanie `using Windows.Web.Http;` . Dodaj następujący kod do `MainPage` klasy. Ta metoda spowoduje wysłanie żądania GET do przykładowego punktu końcowego i wyodrębnienie bieżącego stanu urządzenia dla aplikacji. Pamiętaj, aby zmienić `<your_app_name>` zawartość użytą w **nagłówku** w niestandardowym punkcie końcowym sieci Web

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

## <a name="try-it-out"></a>Wypróbowywanie działania

1. Uruchamianie aplikacji
1. Kliknij pozycję Synchronizuj stan urządzenia. \
Jeśli przetestowano aplikację z `turn on tv` sekcją w poprzedniej sekcji, zobaczysz telewizor jako "on".
    > [!div class="mx-imgBorder"]
    > ![Synchronizuj stan urządzenia](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Wybierz pozycję Włącz mikrofon
1. Wybierz przycisk Rozmowa
1. Wydać`turn on the fan`
1. Stan wizualny wentylatoru powinien zmienić na "on"
    > [!div class="mx-imgBorder"]
    > ![Włącz wentylator](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Włącz proces ciągłej integracji/ciągłego wdrażania dla aplikacji poleceń niestandardowych](./how-to-custom-commands-deploy-cicd.md)