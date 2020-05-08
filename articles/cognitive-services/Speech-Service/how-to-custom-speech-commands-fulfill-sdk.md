---
title: Jak zrealizować polecenia z klienta przy użyciu zestawu Speech SDK
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak obsługiwać działania poleceń niestandardowych na kliencie przy użyciu zestawu Speech SDK.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871736"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Realizacja poleceń z klienta przy użyciu zestawu Speech SDK (wersja zapoznawcza)

Aby wykonać zadania za pomocą aplikacji poleceń niestandardowych, można wysłać niestandardowe ładunki do podłączonego urządzenia klienckiego.

W tym artykule przedstawiono następujące:

- Definiowanie i wysyłanie niestandardowego ładunku JSON z aplikacji poleceń niestandardowych
- Odbieranie i wizualizacja zawartości niestandardowego ładunku JSON z aplikacji klienckiej zestawu SDK języka C# platformy UWP

## <a name="prerequisites"></a>Wymagania wstępne
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Klucz subskrypcji platformy Azure dla usługi mowy: [Uzyskaj jeden bezpłatnie](get-started.md) lub utwórz go na [Azure Portal](https://portal.azure.com)
> * Wcześniej utworzona aplikacja poleceń niestandardowych: [Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)
> * Aplikacja kliencka z obsługą zestawu Speech SDK: [Szybki Start: Nawiązywanie połączenia z aplikacją polecenia niestandardowego za pomocą zestawu Speech SDK (wersja zapoznawcza)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Opcjonalne: szybkie rozpoczynanie pracy

W tym artykule opisano krok po kroku, jak umożliwić aplikacji klienckiej komunikowanie się z aplikacją poleceń niestandardowych. Jeśli wolisz szczegółowe, kompletny, gotowy do skompilowania kod źródłowy użyty w tym artykule jest dostępny w [przykładach zestawu Speech SDK](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Realizacja z ładunkiem JSON

1. Otwórz wcześniej utworzoną aplikację poleceń niestandardowych z [przewodników szybki start: Utwórz niestandardowe polecenie z parametrami](./quickstart-custom-speech-commands-create-parameters.md)
1. Zapoznaj się z sekcją **reguły uzupełniania** , aby upewnić się, że masz wcześniej utworzoną regułę, która reaguje z powrotem do użytkownika.
1. Aby wysłać ładunek bezpośrednio do klienta, Utwórz nową regułę z akcją Wyślij działanie

   > [!div class="mx-imgBorder"]
   > ![Wyślij regułę ukończenia działania](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Nazwa reguły | UpdateDeviceState | Nazwa opisująca przeznaczenie reguły |
   | Warunki | Wymagany parametr- `OnOff` i`SubjectDevice` | Warunki określające, kiedy można uruchomić regułę |
   | Akcje | `SendActivity`(zobacz poniżej) | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |

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
   > ![Wyślij ładunek aktywności](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>Tworzenie wizualizacji na potrzeby stanu lub wyłączenia urządzenia

W samouczku [Szybki Start: Nawiązywanie połączenia z aplikacją polecenia niestandardowego za pomocą zestawu Speech SDK](./quickstart-custom-speech-commands-speech-sdk.md)została utworzona aplikacja kliencka zestawu Speech SDK `turn on the tv`, `turn off the fan`która obsłuży polecenia takie jak,. Po dodaniu niektórych wizualizacji można zobaczyć wynik tych poleceń.

Dodaj pola z etykietami z tekstem **wskazującym** lub **wyłączonym** przy użyciu następującego kodu XML dodanego do`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Obsługuj konfigurowalny ładunek
### <a name="add-reference-libraries"></a>Dodawanie bibliotek referencyjnych

Ze względu na to, że utworzono ładunek JSON, musisz dodać odwołanie do biblioteki [JSON.NET](https://www.newtonsoft.com/json) w celu obsługi deserializacji.
- Klient z odpowiednim rozwiązaniem.
- Wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**, a następnie wybierz pozycję **Zainstaluj** . 
- Wyszukaj plik **Newtonsoft. JSON** na liście aktualizacji, zaktualizuj plik **Microsoft. Core. UniversalWindowsPlatform** do najnowszej wersji

> [!div class="mx-imgBorder"]
> ![Wyślij ładunek aktywności](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

W pliku "MainPage. XAML. cs" Dodaj
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>Obsługa odebranego ładunku

W `InitializeDialogServiceConnector`programie Zastąp `ActivityReceived` procedurę obsługi zdarzeń poniższym kodem. Zmodyfikowana `ActivityReceived` procedura obsługi zdarzeń wyodrębni ładunek z działania i odpowiednio zmieni stan wizualizacji telewizora lub wentylatoru.

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
1. Wybierz przycisk Rozmowa
1. Wydać`turn on the tv`
1. Wizualny stan telewizora powinien zmienić na "on"
   > [!div class="mx-imgBorder"]
   > ![Wyślij ładunek aktywności](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Dodawanie walidacji do parametrów polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-validations.md)
