---
title: Jak zrealizować polecenia z klienta przy użyciu zestawu Speech SDK
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak obsługiwać działania poleceń niestandardowych na kliencie przy użyciu zestawu Speech SDK.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79367753"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Realizacja poleceń z klienta przy użyciu zestawu Speech SDK (wersja zapoznawcza)

Aby wykonać zadania za pomocą aplikacji poleceń niestandardowych, można wysłać niestandardowe ładunki do podłączonego urządzenia klienckiego.

W tym artykule przedstawiono następujące:

- Definiowanie i wysyłanie niestandardowego ładunku JSON z aplikacji poleceń niestandardowych
- Odbieranie i wizualizacja zawartości niestandardowego ładunku JSON z aplikacji klienckiej zestawu SDK języka C# platformy UWP

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Klucz subskrypcji platformy Azure dla usługi mowy
  - [Pobierz bezpłatnie](get-started.md) lub utwórz je na [Azure Portal](https://portal.azure.com)
- Wcześniej utworzona aplikacja poleceń niestandardowych
  - [Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)
- Aplikacja kliencka z włączonym zestawem SDK mowy
  - [Szybki Start: Nawiązywanie połączenia z aplikacją niestandardową za pomocą zestawu Speech SDK (wersja zapoznawcza)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Opcjonalne: szybkie rozpoczynanie pracy

W tym artykule opisano krok po kroku, jak umożliwić aplikacji klienckiej komunikowanie się z aplikacją poleceń niestandardowych. Jeśli wolisz szczegółowe, kompletny, gotowy do skompilowania kod źródłowy użyty w tym artykule jest dostępny w [przykładach zestawu Speech SDK](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Realizacja z ładunkiem JSON

1. Otwórz wcześniej utworzoną aplikację poleceń niestandardowych z programu [Speech Studio](https://speech.microsoft.com/)
1. Zapoznaj się z sekcją **reguły uzupełniania** , aby upewnić się, że masz wcześniej utworzoną regułę, która reaguje z powrotem do użytkownika.
1. Aby wysłać ładunek bezpośrednio do klienta, Utwórz nową regułę z akcją Wyślij działanie

   > [!div class="mx-imgBorder"]
   > ![Wyślij regułę ukończenia działania](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Ustawienie | Sugerowana wartość | Opis |
   | ------- | --------------- | ----------- |
   | Nazwa reguły | UpdateDeviceState | Nazwa opisująca przeznaczenie reguły |
   | Warunki | Wymagany parametr- `OnOff` i`SubjectDevice` | Warunki określające, kiedy można uruchomić regułę |
   | Akcje | `SendActivity`(zobacz poniżej) | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |

   > [!div class="mx-imgBorder"]
   > ![Wyślij ładunek aktywności](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Tworzenie wizualizacji na potrzeby stanu lub wyłączenia urządzenia

W [szybkim samouczku: Nawiązywanie połączenia z aplikacją polecenia niestandardowego za pomocą zestawu Speech SDK (wersja zapoznawcza)](./quickstart-custom-speech-commands-speech-sdk.md) została utworzona aplikacja kliencka zestawu Speech SDK, która obsługiwała polecenia takie jak `turn on the tv`, `turn off the fan`. Teraz Dodaj wizualizacje, aby zobaczyć wynik tych poleceń.

Dodaj pola z etykietami z tekstem **wskazującym** lub **wyłączonym** przy użyciu następującego kodu XML dodanego do`MainPage.xaml.cs`

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

Teraz, po utworzeniu ładunku JSON, można dodać odwołanie do biblioteki [JSON.NET](https://www.newtonsoft.com/json) w celu obsługi deserializacji.

> [!div class="mx-imgBorder"]
> ![Wyślij ładunek aktywności](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

W `InitializeDialogServiceConnector` obszarze Dodaj następujący kod do `ActivityReceived` programu obsługi zdarzeń. Dodatkowy kod wyodrębni ładunek z działania i odpowiednio zmieni stan wizualizacji telewizora lub wentylatora.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Dodawanie walidacji do parametrów polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-validations.md)
