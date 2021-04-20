---
title: Wysyłanie Polecenia niestandardowe do aplikacji klienckiej
titleSuffix: Azure Cognitive Services
description: Z tego artykułu dowiesz się, jak wysyłać działania z aplikacji Polecenia niestandardowe do aplikacji klienckiej z uruchomionym zestawem SPEECH SDK.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52e0b750f02044afafe233a76e4f43755d9ed303
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725102"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Wysyłanie Polecenia niestandardowe do aplikacji klienckiej

Z tego artykułu dowiesz się, jak wysyłać działania z aplikacji Polecenia niestandardowe do aplikacji klienckiej z uruchomionym zestawem SPEECH SDK.

Należy wykonać następujące zadania:

- Definiowanie i wysyłanie niestandardowego ładunku JSON z Polecenia niestandardowe aplikacji
- Odbieranie i wizualizowanie niestandardowej zawartości ładunku JSON z aplikacji klienckiej zestawu SDK rozpoznawania mowy platformy UWP w języku C#

## <a name="prerequisites"></a>Wymagania wstępne
> [!div class = "checklist"]
> * [Visual Studio 2019 r.](https://visualstudio.microsoft.com/downloads/) lub wyższym. W tym przewodniku Visual Studio 2019 r.
> * Klucz subskrypcji platformy Azure dla usługi Mowa: [Pobierz bezpłatnie](overview.md#try-the-speech-service-for-free) lub utwórz go w witrynie [Azure Portal](https://portal.azure.com)
> * [Utworzona wcześniej aplikacja Polecenia niestandardowe](quickstart-custom-commands-application.md)
> * Aplikacja kliency z włączonym zestawem SDK rozpoznawania mowy: [How-to: Integrate with a client application using Speech SDK (Jak zintegrować z aplikacją kliencową przy użyciu zestawu Speech SDK)](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Setup Send activity to client (Konfigurowanie wysyłania działania do klienta) 
1. Otwórz Polecenia niestandardowe wcześniej utworzoną aplikację
1. Wybierz **polecenie TurnOnOff,** wybierz pozycję **ConfirmationResponse w** obszarze reguły ukończenia, a następnie wybierz pozycję Dodaj **akcję**
1. W **obszarze Nowy typ akcji** wybierz pozycję Wyślij działanie do **klienta**
1. Skopiuj poniższy tekst JSON do strony **Zawartość działania**
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
1. Kliknij **przycisk** Zapisz, aby utworzyć nową regułę z akcją Wyślij działanie, **przeszukaj** **i opublikuj** zmianę

   > [!div class="mx-imgBorder"]
   > ![Reguła ukończenia działania Wysyłania](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integracja z aplikacją kliencką

W oknie [How-to: Setup client application with Speech SDK (Preview) (Jak](./how-to-custom-commands-setup-speech-sdk.md)skonfigurować aplikację kliencyjną przy użyciu zestawu SPEECH SDK (wersja zapoznawcza) ) utworzono aplikację kliencową platformy uniwersalnej systemu Windows z zestawem SDK rozpoznawania mowy obsługą poleceń, takich jak `turn on the tv` , `turn off the fan` . Po dodaniu niektórych wizualizacji można zobaczyć wynik tych poleceń.

Aby dodać pola z etykietami z tekstem **wskazującym** na lub **wyłączyć,** dodaj następujący blok XML StackPanel do elementu `MainPage.xaml` .

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

Ponieważ ładunek JSON został utworzony, musisz dodać odwołanie do biblioteki [JSON.NET,](https://www.newtonsoft.com/json) aby obsłużyć deserializację.

1. Przekieruj odpowiednie rozwiązanie do klienta.
1. Wybierz **pozycję Zarządzaj pakietami NuGet dla rozwiązania**, wybierz pozycję **Przeglądaj** 
1. Jeśli program został już **Newtonsoft.jsna komputerze**, upewnij się, że jego wersja to co najmniej 12.0.3. Jeśli nie, przejdź do **tematu Zarządzanie** pakietami NuGet dla rozwiązania — aktualizacje , wyszukaj **Newtonsoft.js,** aby je zaktualizować. Ten przewodnik korzysta z wersji 12.0.3.

    > [!div class="mx-imgBorder"]
    > ![Ładunek działania wysyłania](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Upewnij się również, że pakiet NuGet **Microsoft.NETCore.UniversalWindowsPlatform** ma co najmniej 6.2.10. Ten przewodnik korzysta z wersji 6.2.10.

W pliku "MainPage.xaml.cs" dodaj

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>Obsługa odebranego ładunku

W `InitializeDialogServiceConnector` programie zastąp `ActivityReceived` program obsługi zdarzeń następującym kodem. Zmodyfikowana procedura obsługi zdarzeń wyodrębni ładunek z działania i odpowiednio zmieni stan wizualny odbiornika `ActivityReceived` lub odbiornika.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

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
1. Stan wizualny tv powinien zmienić się na "on"
   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający, że stan wizualizacji języka T V jest teraz wł.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [How to: set up web endpoints ( Jak skonfigurować internetowe punkty końcowe)](./how-to-custom-commands-setup-web-endpoints.md)
