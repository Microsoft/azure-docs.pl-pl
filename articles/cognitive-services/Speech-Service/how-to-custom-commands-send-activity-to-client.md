---
title: Wyślij działanie poleceń niestandardowych do aplikacji klienckiej
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak wysyłać działania z aplikacji poleceń niestandardowych do aplikacji klienckiej z uruchomionym zestawem Speech SDK.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: fc62c87fd12457c60d3eb26cba6814aa1df76f87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839218"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Wyślij działanie poleceń niestandardowych do aplikacji klienckiej

W tym artykule dowiesz się, jak wysyłać działania z aplikacji poleceń niestandardowych do aplikacji klienckiej z uruchomionym zestawem Speech SDK.

Wykonaj następujące zadania:

- Definiowanie i wysyłanie niestandardowego ładunku JSON z aplikacji poleceń niestandardowych
- Odbieranie i wizualizacja zawartości niestandardowego ładunku JSON z aplikacji klienckiej zestawu SDK języka C# platformy UWP

## <a name="prerequisites"></a>Wymagania wstępne
> [!div class = "checklist"]
> * [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) lub nowszy. Ten przewodnik używa programu Visual Studio 2019
> * Klucz subskrypcji platformy Azure dla usługi Mowa: [Pobierz bezpłatnie](overview.md#try-the-speech-service-for-free) lub utwórz go w witrynie [Azure Portal](https://portal.azure.com)
> * [Utworzona wcześniej aplikacja Polecenia niestandardowe](quickstart-custom-commands-application.md)
> * Aplikacja kliencka z włączonym zestawem SDK: Porada [: integracja z aplikacją kliencką przy użyciu zestawu Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Skonfiguruj działanie wysyłania do klienta 
1. Otwórz wcześniej utworzoną aplikację poleceń niestandardowych
1. Wybierz polecenie **TurnOnOff** , wybierz pozycję **ConfirmationResponse** w obszarze reguła ukończenia, a następnie wybierz pozycję **Dodaj akcję** .
1. W obszarze **Nowa akcja — typ**wybierz pozycję **Wyślij działanie do klienta**
1. Skopiuj poniższy kod JSON do **zawartości działania**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
1. Kliknij przycisk **Zapisz** , aby utworzyć nową regułę z akcją wysyłania, **nauczenie** i **opublikowanie** zmiany

   > [!div class="mx-imgBorder"]
   > ![Wyślij regułę ukończenia działania](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integracja z aplikacją kliencką

W temacie [How to: Setup Application Client with Speech SDK (wersja zapoznawcza)](./how-to-custom-commands-setup-speech-sdk.md)utworzono aplikację kliencką platformy UWP z użyciem zestawu Speech SDK, który obsłużył polecenia takie jak `turn on the tv` , `turn off the fan` . Po dodaniu niektórych wizualizacji można zobaczyć wynik tych poleceń.

Aby dodać **pola z etykietą z tekstem wskazującym** lub **wyłączać**, Dodaj następujący blok XML StackPanel do `MainPage.xaml` .

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>Dodawanie bibliotek referencyjnych

Ze względu na to, że utworzono ładunek JSON, musisz dodać odwołanie do biblioteki [JSON.NET](https://www.newtonsoft.com/json) w celu obsługi deserializacji.

1. Klient z odpowiednim rozwiązaniem.
1. Wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**, a następnie wybierz pozycję **Przeglądaj** . 
1. Jeśli masz już zainstalowaną **Newtonsoft.jsna**, upewnij się, że jej wersja to co najmniej 12.0.3. Jeśli nie, przejdź do pozycji **Zarządzaj pakietami NuGet dla rozwiązania — aktualizacje**, Wyszukaj **Newtonsoft.jsna** , aby je zaktualizować. W tym przewodniku jest używana wersja 12.0.3.

    > [!div class="mx-imgBorder"]
    > ![Wyślij ładunek aktywności](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Upewnij się również, że pakiet NuGet **Microsoft. servicecore. UniversalWindowsPlatform** ma co najmniej 6.2.10. W tym przewodniku jest używana wersja 6.2.10.

W pliku "MainPage. XAML. cs" Dodaj

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>Obsługuj otrzymany ładunek

W programie `InitializeDialogServiceConnector` Zastąp `ActivityReceived` procedurę obsługi zdarzeń poniższym kodem. Zmodyfikowana `ActivityReceived` procedura obsługi zdarzeń wyodrębni ładunek z działania i odpowiednio zmieni stan wizualizacji telewizora lub wentylatora.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Wypróbowywanie działania

1. Uruchamianie aplikacji
1. Wybierz pozycję Włącz mikrofon
1. Wybierz przycisk Mów
1. Powiedz `turn on the tv`
1. Wizualny stan telewizora powinien zmienić na "on"
   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu pokazujący, że stan wizualizacji T V jest teraz włączony.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Konfigurowanie punktów końcowych sieci Web (wersja zapoznawcza)](./how-to-custom-commands-setup-web-endpoints.md)
