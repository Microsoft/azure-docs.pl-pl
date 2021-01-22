---
title: Wprowadzenie do dostarczania wideo na żądanie przy użyciu witryny Azure Portal | Microsoft Docs
description: W tym samouczku przedstawiono kolejne kroki wdrażania podstawowej usługi do dostarczania zawartości wideo na żądanie za pomocą aplikacji usługi Azure Media Services w witrynie Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0cc3e26895f6bd11ba4f2e5f0b04e4dd36a4fcab
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695293"
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Wprowadzenie do dostarczania zawartości na żądanie przy użyciu witryny Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

W tym samouczku przedstawiono kolejne kroki wdrażania podstawowej usługi do dostarczania zawartości wideo na żądanie za pomocą aplikacji usługi Azure Media Services w witrynie Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka są niezbędne następujące elementy:

* Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Konto usługi Media Services. Aby utworzyć konto Media Services, zobacz [jak utworzyć konto Media Services](media-services-portal-create-account.md).

W tym samouczku opisano następujące zadania:

1. Uruchamianie punktu końcowego przesyłania strumieniowego.
2. Ładowanie pliku wideo.
3. Kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów.
4. Publikowanie elementu zawartości i uzyskiwanie adresów URL na potrzeby przesyłania strumieniowego i pobierania progresywnego.  
5. Odtwarzanie zawartości.

## <a name="start-the-streaming-endpoint"></a>Uruchamianie punktu końcowego przesyłania strumieniowego

Podczas pracy w usłudze Azure Media Services jednym z najbardziej typowych scenariuszy jest dostarczanie wideo za pośrednictwem przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów. Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów. Dzięki funkcji dynamicznego tworzenia pakietów można dostarczać zawartość MP4 z adaptacyjną szybkością transmisji bitów w formatach przesyłania strumieniowego „just-in-time”, które są obsługiwane przez usługę Media Services. Przykłady obejmują formaty Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming i Dynamic Adaptive Streaming over HTTP (DASH, nazywany także MPEG-DASH). Używając przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów w usłudze Media Services, można dostarczać wideo bez przechowywania wcześniej utworzonych pakietów z wersjami dla każdego z tych formatów przesyłania strumieniowego.

> [!NOTE]
> Po utworzeniu konta usługi Media Services do Twojego konta dodawany jest domyślny punkt końcowy przesyłania strumieniowego w stanie **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

Aby uruchomić punkt końcowy przesyłania strumieniowego:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz kolejno pozycje **Ustawienia**  >  **przesyłanie strumieniowe punkty końcowe**. 
3. Wybierz domyślny punkt końcowy przesyłania strumieniowego. Zostanie wyświetlone okno **szczegóły domyślnego punktu końcowego przesyłania strumieniowego** .
4. Wybierz ikonę **Uruchom**.
5. Wybierz ikonę **Zapisz**.

## <a name="upload-files"></a>Przekazywanie plików
Aby przesłać strumieniowo pliki wideo przy użyciu usługi Media Services, musisz przekazać źródłowe pliki wideo, zakodować je do wielokrotnych szybkości transmisji bitów, a następnie opublikować wynik. Pierwszy krok został omówiony w tej sekcji. 

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. Wybierz pozycję **Ustawienia**  >  **zasoby**. Następnie wybierz przycisk **Przekaż**.
   
    ![Przekazywanie plików](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Zostanie wyświetlone okno **Przekaż element zawartości wideo**.
   
   > [!NOTE]
   > W usłudze Media Services nie ma limitu rozmiaru pliku dla przekazywanych wideo.
   > 
   > 
3. Na swoim komputerze przejdź do wideo, które chcesz przekazać. Wybierz wideo, a następnie wybierz przycisk **OK**.  
   
    Rozpocznie się przekazywanie. Postęp będzie widoczny pod nazwą pliku.  

Po zakończeniu przekazywania nowy element zawartości będzie widoczny w okienku **Elementy zawartości**. 

## <a name="encode-assets"></a>Kodowanie elementów zawartości
Aby skorzystać z dynamicznego tworzenia pakietów, musisz zakodować swoje pliki źródłowe jako zestaw plików MP4 o różnych szybkościach transmisji bitów. Kroki kodowania zostały przedstawione w tej sekcji.

### <a name="encode-assets-in-the-portal"></a>Kodowanie elementów zawartości w portalu
Aby zakodować zawartość przy użyciu procesora Media Encoder Standard w witrynie Azure Portal:

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. Wybierz pozycję **Ustawienia**  >  **zasoby**. Wybierz element zawartości, który chcesz zakodować.
3. Wybierz przycisk **Koduj**.
4. W okienku **Kodowanie elementu zawartości** wybierz procesor **Media Encoder Standard** i ustawienia wstępne. Aby uzyskać informacje o ustawieniach wstępnych, zobacz [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Automatyczne generowanie drabiny szybkości transmisji bitów) i [Task presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Ustawienia wstępne zadań procesora Media Encoder Standard). Ważne jest, aby wybrać ustawienia wstępne, które będą najlepiej działać dla wejściowego pliku wideo. Na przykład: jeśli wejściowy plik wideo ma rozdzielczość 1920 &#215; 1080 pikseli, można użyć ustawienia wstępnego **Wielokrotna szybkość transmisji bitów H264 1080p**. Jeśli masz wideo w niskiej rozdzielczości (640 &#215; 360), ustawienie wstępne **Wielokrotna szybkość transmisji bitów H264 1080p** nie powinno być używane.
   
   Aby ułatwić sobie zarządzanie zasobami, możesz poddać edycji nazwę wyjściowego elementu zawartości i nazwę zadania.
   
   ![Kodowanie elementów zawartości](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Wybierz przycisk **Utwórz**.

### <a name="monitor-encoding-job-progress"></a>Monitorowanie postępu zadania kodowania
Aby monitorować postęp zadania kodowania, kliknij u góry strony pozycję **Ustawienia**, a następnie wybierz pozycję **Zadania**.

![Stanowiska](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publikowanie zawartości
Aby podać użytkownikowi adres URL, który może służyć do odtwarzania strumieniowego lub pobierania zawartości, najpierw musisz opublikować swój element zawartości przez utworzenie lokalizatora. Lokalizatory zapewniają dostęp do plików znajdujących się w elemencie zawartości. Usługa Azure Media Services obsługuje dwa typy lokalizatorów: 

* **Lokalizatory przesyłania strumieniowego (OnDemandOrigin)**. Lokalizatory przesyłania strumieniowego służą do adaptacyjnego przesyłania strumieniowego. Przykłady adaptacyjnego przesyłania strumieniowego to HLS, Smooth Streaming i MPEG-DASH. Aby utworzyć lokalizator przesyłania strumieniowego, element zawartości musi zawierać plik ism. 
* **Lokalizatory progresywne (sygnatura dostępu współdzielonego)**. Lokalizatory progresywne służą do dostarczania wideo przy użyciu pobierania progresywnego.

Aby utworzyć adres URL przesyłania strumieniowego HLS, Dołącz *(format = M3U8-AAPL)* do adresu URL:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)`

Aby utworzyć adres URL przesyłania strumieniowego do odtwarzania elementu zawartości Smooth Streaming, użyj następującego formatu adresu URL:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest`

Aby utworzyć adres URL przesyłania strumieniowego w protokole MPEG-DASH, dołącz do adresu URL ciąg *(format=mpd-time-csf)*:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)`

Adres URL sygnatury dostępu współdzielonego ma następujący format:

`{blob container name}/{asset name}/{file name}/{shared access signature}`

> [!NOTE]
> Lokalizatory utworzone w witrynie Azure Portal przed marcem 2015 r. mają dwuletnią datę wygaśnięcia.  
> 
> 

Aby zaktualizować datę wygaśnięcia lokalizatora, możesz użyć [interfejsu API REST](/rest/api/media/operations/locator#update_a_locator) lub [interfejsu API platformy .NET](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator). 

> [!NOTE]
> Po zaktualizowaniu daty wygaśnięcia lokalizatora sygnatury dostępu współdzielonego następuje zmiana adresu URL.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Aby opublikować element zawartości za pomocą portalu
1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. Wybierz pozycję **Ustawienia**  >  **zasoby**. Wybierz element zawartości, który chcesz opublikować.
3. Wybierz przycisk **Publikuj**.
4. Wybierz typ lokalizatora.
5. Wybierz pozycję **Dodaj**.
   
    ![Publikowanie wideo](./media/media-services-portal-vod-get-started/media-services-publish1.png)

Adres URL jest dodawany do listy **Opublikowane adresy URL**.

## <a name="play-content-from-the-portal"></a>Odtwarzanie zawartości z portalu
Swoje wideo możesz przetestować w odtwarzaczu zawartości w witrynie Azure Portal.

Wybierz wideo, a następnie wybierz przycisk **Odtwórz**.

![Odtwarzanie wideo w witrynie Azure Portal](./media/media-services-portal-vod-get-started/media-services-play.png)

Zagadnienia do rozważenia:

* Aby rozpocząć przesyłanie strumieniowe, uruchom domyślny punkt końcowy przesyłania strumieniowego.
* Upewnij się, że wideo zostało już opublikowane.
* Odtwarzacz multimedialny w witrynie Azure Portal odtwarza zawartość z domyślnego punktu końcowego przesyłania strumieniowego. Aby odtworzyć zawartość z punktu końcowego przesyłania strumieniowego innego niż domyślny, zaznacz i skopiuj adres URL, a następnie wklej go do innego odtwarzacza. Na przykład swoje wideo możesz przetestować w usłudze [Azure Media Player](https://aka.ms/azuremediaplayer).

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]