---
title: Konfigurowanie przesyłania strumieniowego w trybie offline PlayReady
description: W tym artykule przedstawiono sposób konfigurowania konta Azure Media Services v3 na potrzeby przesyłania strumieniowego PlayReady dla systemu Windows 10 w trybie offline.
services: media-services
keywords: ŁĄCZNIK, DRM, Widevine tryb offline, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: aecae72b0bea07a0d8e240b3dcae7ee9b9662f95
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640721"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Tryb online PlayReady Streaming dla systemu Windows 10 z Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services obsługiwać pobieranie/odtwarzanie w trybie offline przy użyciu ochrony DRM. W tym artykule omówiono obsługę Azure Media Services w przypadku klientów z systemem Windows 10/PlayReady w trybie offline. W następujących artykułach można zapoznać się z obsługą trybu offline dla urządzeń z systemami iOS/FairPlay i Android/Widevine:

- [Przesyłanie strumieniowe w trybie offline przy użyciu technologii FairPlay na potrzeby systemu iOS](offline-fairplay-for-ios.md)
- [Widevine przesyłania strumieniowego w trybie offline dla systemu Android](offline-widevine-for-android.md)

> [!NOTE]
> W przypadku funkcji DRM w trybie offline jest naliczana tylko jedna prośba o licencję podczas pobierania zawartości. Nie są naliczane opłaty za żadne błędy.

## <a name="background-on-offline-mode-playback"></a>Odtwarzanie w trybie offline

W tej sekcji przedstawiono niektóre w tle odtwarzania w trybie offline, szczególnie dlatego:

* W niektórych krajach/regionach dostępność i/lub przepustowość Internetu są nadal ograniczone. Użytkownicy mogą wybrać opcję wcześniejszego pobrania, aby móc oglądać zawartość w wysokiej rozdzielczości, aby zapewnić zadowalające środowisko wyświetlania. W takim przypadku problem nie jest dostępny w sieci, a jego przepustowość jest ograniczona. Dostawcy OTT/OVP żądają obsługi trybu offline.
* Jak przedstawiono na konferencji udziałowców Netflix 2016 Q3, pobieranie zawartości to "funkcja" z prośbą o "oft" i "jest ona otwarta" zgodnie z opisem w Reed Hastings, dyrektor generalny Netflix.
* Niektórzy dostawcy zawartości mogą nie zezwalać na dostarczanie licencji DRM poza granicami kraju/regionu. Jeśli użytkownik musi podróżować za granicą i nadal chce obserwować zawartość, wymagane jest pobranie do trybu offline.
 
Wyzwanie, z którego korzystamy w implementacji trybu offline, jest następujące:

* Plik MP4 jest obsługiwany przez wielu graczy, narzędzi koderów, ale nie ma powiązania między kontenerem MP4 i DRM.
* W długim czasie CFF z CENC jest sposobem na to, aby to zrobić. Jednak obecnie ekosystem pomocy technicznej Tools/Player nie jest jeszcze dostępny. Potrzebujemy już rozwiązania.
 
Pomysłem jest: Format pliku o płynnej transmisji strumieniowej ([PIFF](/iis/media/smooth-streaming/protected-interoperable-file-format)) z wielokrotna H264/AAC ma powiązanie z usługą PLAYREADY (AES-128). Indywidualny, płynny plik. ISMV (przy założeniu, że dźwięk jest muxed w filmie wideo) to fMP4 i może służyć do odtwarzania. Jeśli płynna zawartość przesyłana strumieniowo odbywa się za pomocą szyfrowania PlayReady, każdy plik. ISMV jest chronionym MP4. Możemy wybrać plik. ISMV z preferowaną szybkością transmisji bitów i zmienić jego nazwę na. mp4 do pobrania.

Dostępne są dwie opcje hostingu plików MP4 chronionych przy użyciu usługi PlayReady do pobrania progresywnego:

* Jeden z tych plików można umieścić w tym samym elemencie zawartości kontenera/nośnika i wykorzystać Azure Media Services punkt końcowy przesyłania strumieniowego na potrzeby pobierania progresywnego.
* Jeden z nich może korzystać z usługi SAS Locator do pobierania progresywnego bezpośrednio z magazynu Azure, pomijając Azure Media Services.
 
Można użyć dwóch typów dostarczania licencji PlayReady:

* Usługa dostarczania licencji PlayReady w Azure Media Services;
* Serwery licencji PlayReady hostowane w dowolnym miejscu.

Poniżej znajdują się dwa zestawy zasobów testowych, pierwszej z nich przy użyciu dostarczania licencji PlayReady w AMS, a drugi przy użyciu mojego serwera licencji PlayReady hostowanego na maszynie wirtualnej platformy Azure:

## <a name="asset-1"></a>#1 zasobów

* Adres URL pobierania progresywnego: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* LA_URL PlayReady (AMS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

## <a name="asset-2"></a>#2 zasobów

* Adres URL pobierania progresywnego: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* LA_URL PlayReady (lokalnie): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

Do testowania odtwarzania użyto uniwersalnej aplikacji systemu Windows w systemie Windows 10. W przypadku [uniwersalnych przykładów systemu Windows 10](https://github.com/Microsoft/Windows-universal-samples)istnieje przykład podstawowego odtwarzacza o nazwie [adaptacyjnego przesyłania strumieniowego](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Wystarczy dodać kod, aby wybrać pobrany film wideo i użyć go jako źródła zamiast adaptacyjnego źródła przesyłania strumieniowego. Zmiany są po kliknięciu przycisku programu obsługi zdarzeń:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![Odtwarzanie w trybie offline fMP4 chronionych technologii PlayReady](./media/offline-playready-for-windows/offline-playready1.jpg)

Ponieważ film wideo jest objęty ochroną oprogramowania PlayReady, zrzut ekranu nie będzie w stanie dołączyć wideo.

Podsumowując, osiągnięto tryb offline na Azure Media Services:

* Transkodowanie zawartości i szyfrowanie PlayReady można wykonać w Azure Media Services lub innych narzędziach.
* Zawartość może być hostowana w usłudze Azure Media Services lub Azure Storage na potrzeby pobierania progresywnego.
* Dostawy licencji PlayReady mogą znajdować się w Azure Media Services lub w innym miejscu;
* Przygotowanej płynnej zawartości strumieniowej można nadal używać do przesyłania strumieniowego online za pośrednictwem ŁĄCZNIKa lub Wygładź przy użyciu oprogramowania PlayReady jako technologii DRM.
