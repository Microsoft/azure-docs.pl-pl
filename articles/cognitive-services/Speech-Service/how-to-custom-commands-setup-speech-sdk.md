---
title: Integracja z aplikacją kliencką przy użyciu zestawu SDK usługi Mowa
titleSuffix: Azure Cognitive Services
description: Jak wprowadzać żądania do opublikowanej aplikacji poleceń niestandardowych z zestawu Speech SDK działającego w aplikacji platformy UWP.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: fa3a6d16b79800043bdcd3f183dd86fa278dd1a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026031"
---
# <a name="integrate-with-a-client-application-using-speech-sdk"></a>Integracja z aplikacją kliencką przy użyciu zestawu Speech SDK

W tym artykule dowiesz się, jak wykonywać żądania do opublikowanej aplikacji poleceń niestandardowych z zestawu Speech SDK działającego w aplikacji platformy UWP. Aby można było nawiązać połączenie z aplikacją poleceń niestandardowych, potrzebne są:

- Publikowanie aplikacji poleceń niestandardowych i Pobieranie identyfikatora aplikacji (Identyfikator aplikacji)
- Tworzenie aplikacji klienckiej platforma uniwersalna systemu Windows (platformy UWP) przy użyciu zestawu Speech SDK, aby umożliwić rozmowę z aplikacją poleceń niestandardowych

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania tego artykułu jest wymagana aplikacja poleceń niestandardowych. Jeśli aplikacja poleceń niestandardowych nie została utworzona, można to zrobić, wykonując czynności opisane w przewodniku szybki start:
> [!div class = "checklist"]
> * [Tworzenie aplikacji poleceń niestandardowych](quickstart-custom-commands-application.md)

Wymagane są również:
> [!div class = "checklist"]
> * [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) lub nowszy. Ten przewodnik jest oparty na programie Visual Studio 2019.
> * Klucz subskrypcji platformy Azure dla usługi Speech Services. [Pobierz bezpłatnie](overview.md#try-the-speech-service-for-free) lub utwórz je na [Azure Portal](https://portal.azure.com)
> * [Włącz Programowanie na urządzeniu](/windows/uwp/get-started/enable-your-device-for-development)

## <a name="step-1-publish-custom-commands-application"></a>Krok 1. publikowanie aplikacji poleceń niestandardowych

1. Otwórz wcześniej utworzoną aplikację poleceń niestandardowych
1. Przejdź do pozycji **Ustawienia**, wybierz pozycję **zasób Luis**
1. Jeśli nie przypisano **zasobu przewidywania** , wybierz klucz predykcyjny zapytania lub Utwórz nowy.

    Klucz przewidywania zapytania jest zawsze wymagany przed opublikowaniem aplikacji. Aby uzyskać więcej informacji na temat zasobów LUIS, Utwórz odwołanie do [zasobu Luis](../luis/luis-how-to-azure-subscription.md)

1. Wróć do edycji poleceń, wybierz pozycję **Publikuj**

   > [!div class="mx-imgBorder"]
   > ![Publikowanie aplikacji](media/custom-commands/setup-speech-sdk-publish-application.png)

1. Skopiuj identyfikator aplikacji z powiadomienia o opublikowaniu do późniejszego użycia
1. Skopiuj klucz zasobu mowy do późniejszego użycia

## <a name="step-2-create-a-visual-studio-project"></a>Krok 2. Tworzenie projektu programu Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Krok 3. Dodawanie przykładowego kodu

W tym kroku dodamy kod XAML, który definiuje interfejs użytkownika aplikacji, a następnie doda implementację kodu w języku C#.

### <a name="xaml-code"></a>Kod XAML

Utwórz interfejs użytkownika aplikacji, dodając kod XAML.

1. W **Eksplorator rozwiązań** Otwórz `MainPage.xaml`

1. W widoku XAML projektanta Zastąp całą zawartość następującym fragmentem kodu:

   ```xml
   <Page
       x:Class="helloworld.MainPage"
       xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       xmlns:local="using:helloworld"
       xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
       xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
       mc:Ignorable="d"
       Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

       <Grid>
           <StackPanel Orientation="Vertical" HorizontalAlignment="Center"
                       Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
               <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"
                       Margin="0,10,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
                       Margin="0,10,10,0" Click="ListenButton_ButtonClicked"
                       Height="35"/>
               <StackPanel x:Name="StatusPanel" Orientation="Vertical"
                           RelativePanel.AlignBottomWithPanel="True"
                           RelativePanel.AlignRightWithPanel="True"
                           RelativePanel.AlignLeftWithPanel="True">
                   <TextBlock x:Name="StatusLabel" Margin="0,10,10,0"
                              TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                   <Border x:Name="StatusBorder" Margin="0,0,0,0">
                       <ScrollViewer VerticalScrollMode="Auto"
                                     VerticalScrollBarVisibility="Auto" MaxHeight="200">
                           <!-- Use LiveSetting to enable screen readers to announce
                                the status update. -->
                           <TextBlock
                               x:Name="StatusBlock" FontWeight="Bold"
                               AutomationProperties.LiveSetting="Assertive"
                               MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}"
                               Margin="10,10,10,20" TextWrapping="Wrap"  />
                       </ScrollViewer>
                   </Border>
               </StackPanel>
           </StackPanel>
           <MediaElement x:Name="mediaElement"/>
       </Grid>
   </Page>
   ```

Widok Projekt został zaktualizowany, aby pokazać interfejs użytkownika aplikacji.

### <a name="c-code-behind-source"></a>Źródło kodu w języku C#

Dodaj źródło związane z kodem, aby aplikacja działała zgodnie z oczekiwaniami. Źródło związane z kodem obejmuje:

- Wymagane `using` instrukcje dla `Speech` i `Speech.Dialog` przestrzeni nazw
- Prosta implementacja zapewniająca dostęp do mikrofonu przy użyciu sieci przewodowej do procedury obsługi przycisku
- Podstawowe pomocnicy interfejsu użytkownika do prezentowania komunikatów i błędów w aplikacji
- Punkt wyładunkowy dla ścieżki kodu inicjującego, który zostanie zapełniony później
- Pomocnik odtwarzania zamiany tekstu na mowę (bez obsługi przesyłania strumieniowego)
- Pusta procedura obsługi przycisku do rozpoczęcia nasłuchiwania, która zostanie wypełniona później

Dodaj źródło związane z kodem w następujący sposób:

1. W **Eksplorator rozwiązań** Otwórz plik źródłowy związany z kodem `MainPage.xaml.cs` (pogrupowany pod `MainPage.xaml` ).

1. Zastąp zawartość pliku następującym kodem: 

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.IO;
   using System.Text;
   using Windows.UI.Xaml;
   using Windows.UI.Xaml.Controls;
   using Windows.UI.Xaml.Media;

   namespace helloworld
   {
       public sealed partial class MainPage : Page
       {
           private DialogServiceConnector connector;

           private enum NotifyType
           {
               StatusMessage,
               ErrorMessage
           };

           public MainPage()
           {
               this.InitializeComponent();
           }

           private async void EnableMicrophone_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               bool isMicAvailable = true;
               try
               {
                   var mediaCapture = new Windows.Media.Capture.MediaCapture();
                   var settings =
                       new Windows.Media.Capture.MediaCaptureInitializationSettings();
                   settings.StreamingCaptureMode =
                       Windows.Media.Capture.StreamingCaptureMode.Audio;
                   await mediaCapture.InitializeAsync(settings);
               }
               catch (Exception)
               {
                   isMicAvailable = false;
               }
               if (!isMicAvailable)
               {
                   await Windows.System.Launcher.LaunchUriAsync(
                       new Uri("ms-settings:privacy-microphone"));
               }
               else
               {
                   NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
               }
           }

           private void NotifyUser(
               string strMessage, NotifyType type = NotifyType.StatusMessage)
           {
               // If called from the UI thread, then update immediately.
               // Otherwise, schedule a task on the UI thread to perform the update.
               if (Dispatcher.HasThreadAccess)
               {
                   UpdateStatus(strMessage, type);
               }
               else
               {
                   var task = Dispatcher.RunAsync(
                       Windows.UI.Core.CoreDispatcherPriority.Normal,
                       () => UpdateStatus(strMessage, type));
               }
           }

           private void UpdateStatus(string strMessage, NotifyType type)
           {
               switch (type)
               {
                   case NotifyType.StatusMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Green);
                       break;
                   case NotifyType.ErrorMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Red);
                       break;
               }
               StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text)
                   ? strMessage : "\n" + strMessage;

               if (!string.IsNullOrEmpty(StatusBlock.Text))
               {
                   StatusBorder.Visibility = Visibility.Visible;
                   StatusPanel.Visibility = Visibility.Visible;
               }
               else
               {
                   StatusBorder.Visibility = Visibility.Collapsed;
                   StatusPanel.Visibility = Visibility.Collapsed;
               }
               // Raise an event if necessary to enable a screen reader
               // to announce the status update.
               var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
               if (peer != null)
               {
                   peer.RaiseAutomationEvent(
                       Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
               }
           }

           // Waits for and accumulates all audio associated with a given
           // PullAudioOutputStream and then plays it to the MediaElement. Long spoken
           // audio will create extra latency and a streaming playback solution
           // (that plays audio while it continues to be received) should be used --
           // see the samples for examples of this.
           private void SynchronouslyPlayActivityAudio(
               PullAudioOutputStream activityAudio)
           {
               var playbackStreamWithHeader = new MemoryStream();
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
               playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
               playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

               byte[] pullBuffer = new byte[2056];

               uint lastRead = 0;
               do
               {
                   lastRead = activityAudio.Read(pullBuffer);
                   playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
               }
               while (lastRead == pullBuffer.Length);

               var task = Dispatcher.RunAsync(
                   Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                   mediaElement.SetSource(
                       playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                   mediaElement.Play();
               });
           }

           private void InitializeDialogServiceConnector()
           {
               // New code will go here
           }

           private async void ListenButton_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               // New code will go here
           }
       }
   }
   ```
    > [!NOTE]
    > Jeśli widzisz błąd: "typ obiektu" jest zdefiniowany w zestawie, do którego nie istnieje odwołanie
    > 1. Klient z odpowiednim rozwiązaniem.
    > 1. Wybierz opcję **Zarządzaj pakietami NuGet dla rozwiązania**, wybierz pozycję **aktualizacje** 
    > 1. Jeśli na liście aktualizacji jest wyświetlany **pakiet Microsoft. WebCore. UniversalWindowsPlatform** , zaktualizuj **pakiet Microsoft. WebCore. UniversalWindowsPlatform** do najnowszej wersji

1. Dodaj następujący kod do treści metody `InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Zastąp ciągi `YourApplicationId` , `YourSpeechSubscriptionKey` i `YourServiceRegion` własnymi wartościami dla swojej aplikacji, subskrypcji mowy i [regionu](regions.md)

1. Dołącz następujący fragment kodu na końcu treści metody `InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
   {
       NotifyUser(
           $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

       if (activityReceivedEventArgs.HasAudio)
       {
           SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
       }
   };

   // Canceled will be signaled when a turn is aborted or experiences an error condition
   connector.Canceled += (sender, canceledEventArgs) =>
   {
       NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
       if (canceledEventArgs.Reason == CancellationReason.Error)
       {
           NotifyUser(
               $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
       }
   };

   // Recognizing (not 'Recognized') will provide the intermediate recognized text
   // while an audio stream is being processed
   connector.Recognizing += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
   };

   // Recognized (not 'Recognizing') will provide the final recognized text
   // once audio capture is completed
   connector.Recognized += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
   };

   // SessionStarted will notify when audio begins flowing to the service for a turn
   connector.SessionStarted += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
   };

   // SessionStopped will notify when a turn is complete and
   // it's safe to begin listening again
   connector.SessionStopped += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
   };
   ```

1. Dodaj następujący fragment kodu do treści `ListenButton_ButtonClicked` metody w `MainPage` klasie

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. Na pasku menu wybierz kolejno opcje **plik**  >  **Zapisz wszystko** , aby zapisać zmiany.

## <a name="try-it-out"></a>Czas to wypróbować

1. Na pasku menu wybierz polecenie **Kompiluj**  >  **kompilację rozwiązania** , aby skompilować aplikację. Kod powinien zostać skompilowany bez błędów.

1. Wybierz **Debuguj**  >  **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja asystenta wirtualnego platformy UWP w języku C# — Szybki Start](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Wybierz pozycję **Włącz mikrofon**. Jeśli pojawi się żądanie uprawnienia dostępu, wybierz pozycję **tak**.

   ![Żądanie uprawnienia dostępu do mikrofonu](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Wybierz opcję **rozmowa** i zacznij mówić do angielskiej frazy lub zdania na mikrofon urządzenia. Twoja Zamiana jest przesyłana do kanału mowy Direct line i uzyskanego do tekstu, który pojawia się w oknie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: wysyłanie działania do aplikacji klienckiej (wersja zapoznawcza)](./how-to-custom-commands-send-activity-to-client.md)