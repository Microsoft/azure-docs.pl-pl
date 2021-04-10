---
title: Skonfiguruj konto do przesyłania strumieniowego zawartości chronionej przez usługę PlayReady w trybie offline — Azure
description: W tym artykule pokazano, jak skonfigurować konto Azure Media Services do przesyłania strumieniowego PlayReady dla systemu Windows 10 w trybie offline.
services: media-services
keywords: ŁĄCZNIK, DRM, Widevine tryb offline, ExoPlayer, Android
documentationcenter: ''
author: IngridAtMicrosoft
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 58222b3d188cd9db4f092956c88dece7e5cf2696
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055947"
---
# <a name="offline-playready-streaming-for-windows-10"></a>Przesyłanie strumieniowe przy użyciu technologii PlayReady w trybie offline dla systemu Windows 10

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Wybierz używaną wersję Media Services:"]
> * [Wersja 3](../latest/drm-offline-playready-streaming-for-windows-10.md)
> * [Wersja 2](offline-playready-streaming-windows-10.md)

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services obsługiwać pobieranie/odtwarzanie w trybie offline przy użyciu ochrony DRM. W tym artykule omówiono obsługę Azure Media Services w przypadku klientów z systemem Windows 10/PlayReady w trybie offline. W następujących artykułach można zapoznać się z obsługą trybu offline dla urządzeń z systemami iOS/FairPlay i Android/Widevine:

- [Przesyłanie strumieniowe w trybie offline przy użyciu technologii FairPlay na potrzeby systemu iOS](media-services-protect-hls-with-offline-fairplay.md)
- [Widevine przesyłania strumieniowego w trybie offline dla systemu Android](offline-widevine-for-android.md)

## <a name="overview"></a>Omówienie

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

#1 zasobów:

* Adres URL pobierania progresywnego: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* LA_URL PlayReady (AMS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

#2 zasobów:

* Adres URL pobierania progresywnego: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* LA_URL PlayReady (on-Premium): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

Do testowania odtwarzania użyto aplikacji uniwersalnej systemu Windows w systemie Windows 10. W przypadku [uniwersalnych przykładów systemu Windows 10](https://github.com/Microsoft/Windows-universal-samples)istnieje przykład podstawowego odtwarzacza o nazwie [adaptacyjnego przesyłania strumieniowego](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Wystarczy dodać kod, aby wybrać pobrany film wideo i użyć go jako źródła zamiast adaptacyjnego źródła przesyłania strumieniowego. Zmiany są po kliknięciu przycisku programu obsługi zdarzeń:

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

 ![Odtwarzanie w trybie offline fMP4 chronionych technologii PlayReady](./media/offline-playready/offline-playready1.jpg)

Ponieważ film wideo jest objęty ochroną oprogramowania PlayReady, zrzut ekranu nie będzie w stanie dołączyć wideo.

Podsumowując, osiągnięto tryb offline na Azure Media Services:

* Transkodowanie zawartości i szyfrowanie PlayReady można wykonać w Azure Media Services lub innych narzędziach.
* Zawartość może być hostowana w usłudze Azure Media Services lub Azure Storage na potrzeby pobierania progresywnego.
* Dostawy licencji PlayReady mogą znajdować się w Azure Media Services lub w innym miejscu;
* Przygotowanej płynnej zawartości strumieniowej można nadal używać do przesyłania strumieniowego online za pośrednictwem ŁĄCZNIKa lub Wygładź przy użyciu oprogramowania PlayReady jako technologii DRM.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="next-steps"></a>Następne kroki

[Projekt hybrydowego systemu DRM](hybrid-design-drm-sybsystem.md)
