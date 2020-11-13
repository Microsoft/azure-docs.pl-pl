---
title: Konfigurowanie internetowych punktów końcowych (wersja zapoznawcza)
titleSuffix: Azure Cognitive Services
description: konfigurowanie internetowych punktów końcowych dla aplikacji polecenia niestandardowe
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 0e2406cd35fb2d4dd99da4f5139a9f0f80697912
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566252"
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
> * [Utworzona wcześniej aplikacja Polecenia niestandardowe](quickstart-custom-commands-application.md)
> * Aplikacja kliencka z włączonym zestawem SDK usługi Mowa: [Instrukcje: wysyłanie działania do aplikacji klienckiej](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Konfigurowanie internetowych punktów końcowych

1. Otwórz utworzoną wcześniej aplikację Polecenia niestandardowe. 
1. Przejdź do pozycji „Internetowe punkty końcowe”, a następnie kliknij pozycję „Nowy internetowy punkt końcowy”.

   > [!div class="mx-imgBorder"]
   > ![Nowy internetowy punkt końcowy](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Nazwa | UpdateDeviceState | Nazwa internetowego punktu końcowego. |
   | Adres URL | https://webendpointexample.azurewebsites.net/api/DeviceState | Adres URL punktu końcowego, z którym ma się komunikować aplikacja Polecenia niestandardowe. |
   | Metoda | POST | Dozwolone interakcje (np. GET, POST) z punktem końcowym.|
   | Nagłówki | Klucz: aplikacja, Wartość: 8 pierwszych cyfr identyfikatora aplikacji | Parametry nagłówka do uwzględnienia w nagłówku żądania.|

    > [!NOTE]
    > - Przykładowy internetowy punkt końcowy utworzony przy użyciu [funkcji platformy Azure](https://docs.microsoft.com/azure/azure-functions/), który łączy się z bazą danych zapisującą stan urządzenia telewizora i wentylatora
    > - Sugerowany nagłówek jest wymagany tylko w przypadku przykładowego punktu końcowego
    > - Aby upewnić się, że wartość nagłówka w naszym przykładowym punkcie końcowym jest unikatowa, użyj 8 pierwszych cyfr identyfikatora aplikacji
    > - W rzeczywistym świecie internetowym punktem końcowym może być punkt końcowy [centrum IOT](https://docs.microsoft.com/azure/iot-hub/about-iot-hub), który zarządza Twoimi urządzeniami

1. Kliknij pozycję **Zapisz**.

## <a name="call-web-endpoints"></a>Wywoływanie internetowych punktów końcowych

1. Przejdź do polecenia **TurnOnOff** , w regule ukończenia wybierz pozycję **ConfirmationResponse** , a następnie wybierz pozycję **Dodaj akcję**.
1. W obszarze **Nowa akcja — Typ** wybierz pozycję **Wywołaj internetowy punkt końcowy**
1. W obszarze **Edytuj akcję — Punkty końcowe** wybierz pozycję **UpdateDeviceState** , która jest utworzonym przez nas internetowym punktem końcowym.  
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
   > ![Zrzut ekranu, na którym jest wyświetlany ekran o powodzeniu działania do wykonania.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

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
- Odpowiedź w przypadku powodzenia\
Zapisz, przeprowadź szkolenie i przetestuj
   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający odpowiedź na powodzenie.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Odpowiedź w przypadku niepowodzenia\
Usuń jeden z parametrów zapytania, zapisz, przeprowadź ponowne szkolenie i przetestuj
   > [!div class="mx-imgBorder"]
   > ![Akcja wywoływania internetowych punktów końcowych w przypadku powodzenia](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integracja z aplikacją kliencką

W czasie szkolenia [Instrukcje: wysyłanie działania do aplikacji klienckiej (wersja zapoznawcza)](./how-to-custom-commands-send-activity-to-client.md) dodaliśmy akcję **Wyślij aktywność do klienta**. Aktywność jest wysyłana do aplikacji klienckiej, niezależnie od tego, czy akcja **Wywołaj internetowy punkt końcowy** zakończyła się pomyślnie.
Jednak w większości przypadków chcesz, aby aktywność była wysyłana do aplikacji klienckiej wyłącznie w przypadku, gdy wywołanie internetowego punktu końcowego zakończyło się powodzeniem. W tym przykładzie ma to miejsce po pomyślnym zaktualizowaniu stanu urządzenia.

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
   
Teraz aktywność zostanie wysłana do klienta tylko w przypadku, gdy żądanie do internetowego punktu końcowego zakończy się powodzeniem.

### <a name="create-visuals-for-syncing-device-state"></a>Tworzenie wizualizacji w celu synchronizowania stanu urządzenia
W pliku `MainPage.xaml` dodaj poniższy kod XML nad blokiem `"EnableMicrophoneButton"`.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Synchronizowanie stanu urządzeń 

W pliku `MainPage.xaml.cs` dodaj odwołanie `using Windows.Web.Http;`. Dodaj poniższy kod do klasy `MainPage`. Ta metoda spowoduje wysłanie żądania GET do przykładowego punktu końcowego i wyodrębnienie bieżącego stanu urządzenia dla aplikacji. Nie zapomnij zmienić parametru `<your_app_name>` na wartość użytą w **nagłówku** w internetowym punkcie końcowym aplikacji Polecenia niestandardowe

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
1. Kliknij pozycję Synchronizuj stan urządzenia.\
Jeśli w poprzedniej sekcji aplikacja została przetestowana z parametrem `turn on tv`, stan telewizora powinien zostać wyświetlony jako „włączony”.
    > [!div class="mx-imgBorder"]
    > ![Synchronizowanie stanu urządzeń](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Wybierz pozycję Włącz mikrofon
1. Wybierz przycisk Mów
1. Powiedz `turn on the fan`
1. Stan wizualny wentylatora powinien zmienić się na „włączony”
    > [!div class="mx-imgBorder"]
    > ![Włączanie wentylatora](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Eksportuj aplikację poleceń niestandardowych jako umiejętność zdalną](./how-to-custom-commands-integrate-remote-skills.md)

