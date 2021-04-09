---
title: Przekazywanie, kodowanie i przesyłanie strumieniowe zawartości za pomocą portalu
description: W tym przewodniku szybki start przedstawiono sposób użycia portalu do przekazywania, kodowania i przesyłania strumieniowego zawartości za pomocą Azure Media Services.
ms.topic: quickstart
ms.date: 08/31/2020
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.openlocfilehash: 3f175ff8e7c809032f35cdea9dc3cffa8345b82c
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106827"
---
# <a name="quickstart-upload-encode-and-stream-content-with-portal"></a>Szybki Start: przekazywanie, kodowanie i przesyłanie strumieniowe zawartości za pomocą portalu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym przewodniku szybki start pokazano, jak używać Azure Portal do przekazywania, kodowania i przesyłania strumieniowego zawartości przy użyciu Azure Media Services.
  
## <a name="overview"></a>Omówienie

* Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto Media Services i przesłać plik multimediów cyfrowych o wysokiej jakości do **elementu zawartości**. 
    
    > [!NOTE]
    > Jeśli Twoje wideo zostało wcześniej przekazane do konta Media Services przy użyciu interfejsu API Media Services v3 lub zawartość została wygenerowana na podstawie danych wyjściowych na żywo, nie będą wyświetlane przyciski **Koduj**, **Analizuj** ani **Szyfruj** w Azure Portal. Użyj interfejsów API Media Services v3, aby wykonać te zadania.

    Zapoznaj się z następującymi tematami: 

  * [Przekazywanie do chmury i magazynowanie w niej](storage-account-concept.md)
  * [Koncepcja zasobów](assets-concept.md)
* Po przekazaniu pliku multimediów cyfrowych o wysokiej jakości do zasobu (dane wejściowe) można go przetworzyć (zakodować lub analizować). Przetworzona zawartość znajduje się w innym elemencie zawartości (wyjściowym). 
    * [Zakodowanie](encode-concept.md) przekazanego pliku do formatów, które mogą być odtwarzane w różnych przeglądarkach i urządzeniach.
    * [Analizowanie](analyze-video-audio-files-concept.md) przekazanego pliku. 

        Obecnie w przypadku korzystania z Azure Portal można wykonać następujące czynności: generowanie plików napisów TTML i WebVTT. Pliki w tych formatach mogą służyć do udostępniania plików audio i wideo osobom niepełnosprawnym. Możesz również wyodrębnić słowa kluczowe z zawartości.

        Aby uzyskać bogate środowisko umożliwiające wyodrębnienie szczegółowych informacji z plików wideo i audio, Użyj ustawień wstępnych Media Services v3 (zgodnie z opisem w [samouczku: analizowanie wideo przy użyciu Media Services v3](analyze-videos-tutorial.md)). <br/>Jeśli potrzebujesz bardziej szczegółowych informacji, użyj [Video Indexer](../video-indexer/index.yml) bezpośrednio.    
* Po przetworzeniu zawartości możesz dostarczyć zawartość multimedialną do graczy klienckich. Aby umożliwić odtwarzanie filmów wideo w danych wyjściowych, należy utworzyć **lokalizator przesyłania strumieniowego**. Podczas tworzenia **lokalizatora przesyłania strumieniowego** należy określić **zasady przesyłania strumieniowego**. **Zasady przesyłania strumieniowego** umożliwiają definiowanie protokołów przesyłania strumieniowego i opcji szyfrowania (jeśli istnieją) dla **lokalizatorów przesyłania strumieniowego**.
    
    Ponownego

    * [Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)
    * [Zasady przesyłania strumieniowego](streaming-policy-concept.md)
    * [Tworzenie pakietów i dostarczanie](encode-dynamic-packaging-concept.md)
    * [Filtry](filters-concept.md)
* Zawartość można chronić, szyfrując ją przy użyciu Advanced Encryption Standard (AES-128) lub/i jednego z trzech głównych systemów DRM: Microsoft PlayReady, Google Widevine i Apple FairPlay. [Zawartość zaszyfrowana za pomocą Azure Portal](drm-encrypt-content-how-to.md) szybki start przedstawia sposób konfigurowania ochrony zawartości.
        
## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[Tworzenie konta usługi Media Services](account-create-how-to.md)

## <a name="upload"></a>Przekazywanie

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/).
1. Znajdź i kliknij konto Media Services.
1. Wybierz pozycję **zasoby (nowe)**.
1. Naciśnij przycisk **Przekaż** w górnej części okna. 
1. Przeciągnij i upuść lub przejdź do pliku, który chcesz przekazać.

Po przejściu do okna elementy zawartości zobaczysz, że do listy dodano nowy element zawartości:

![Zrzut ekranu przedstawiający Azure Portal wyświetlenia okna elementów zawartości otwartego przez wybranie pozycji zasoby (nowe) i nowego elementu zawartości dodanej przez wybranie przycisku Przekaż.](./media/asset-create-asset-upload-portal-quickstart/upload.png)

## <a name="encode"></a>Kodowanie

1. Wybierz pozycję **zasoby (nowe)**.
1. Wybierz nowy element zawartości (dodany w ostatnim kroku).
1. Kliknij pozycję **Koduj** w górnej części okna.

    Naciśnięcie tego przycisku uruchamia zadanie kodowania. Po pomyślnym zakończeniu zostanie wygenerowany element wyjściowy, który zawiera zakodowaną zawartość.

Jeśli przejdziesz do okna elementów zawartości, zobaczysz, że zasób wyjściowy został dodany do listy:

![Zrzut ekranu przedstawiający okno elementy zawartości w Azure Portal pokazujący ignite.mp4 zasobów zakodowane w standardowym formacie nośnika, dodane do listy zasobów.](./media/asset-create-asset-upload-portal-quickstart/encode.png)

## <a name="monitor-the-job-progress"></a>Monitoruj postęp zadania

Aby wyświetlić stan zadania, przejdź do **zadania**. Zadanie zazwyczaj przechodzi przez następujące stany: zaplanowane, kolejkowane, przetwarzane, zakończone (stan końcowy). Jeśli zadanie napotka błąd, może być w stanie Error (Błąd).

![Stan](./media/asset-create-asset-upload-portal-quickstart/job-status.png)

## <a name="publish-and-stream"></a>Publikuj i przesyłaj strumieniowo

Aby opublikować element zawartości, musisz teraz dodać lokalizator przesyłania strumieniowego do Twojego elementu zawartości.

### <a name="streaming-locator"></a>Lokalizator przesyłania strumieniowego 

1. W sekcji **lokalizator przesyłania strumieniowego** naciśnij pozycję **+ Dodaj lokalizator przesyłania strumieniowego**.
    Spowoduje to opublikowanie elementu zawartości i wygenerowanie adresów URL przesyłania strumieniowego.

    > [!NOTE]
    > Aby można było zaszyfrować strumień, należy utworzyć zasady klucza zawartości i ustawić je w lokalizatorze przesyłania strumieniowego. Aby uzyskać szczegółowe informacje, zobacz [szyfrowanie zawartości przy użyciu Azure Portal](drm-encrypt-content-how-to.md).
1. W oknie **Dodawanie lokalizatora przesyłania strumieniowego** wybierz jedną ze wstępnie zdefiniowanych zasad przesyłania strumieniowego. Aby uzyskać szczegółowe informacje, zobacz [zasady przesyłania strumieniowego](streaming-policy-concept.md)

    ![Lokalizator przesyłania strumieniowego](./media/asset-create-asset-upload-portal-quickstart/streaming-locator.png)

Po opublikowaniu elementu zawartości można przesłać do niego strumień bezpośrednio w portalu. 

![Graj.](./media/asset-create-asset-upload-portal-quickstart/publish.png)

Lub skopiuj adres URL przesyłania strumieniowego i użyj go w odtwarzaczu klienta.

> [!NOTE]
> Upewnij się, że [punkt końcowy przesyłania strumieniowego](streaming-endpoint-concept.md) jest uruchomiony. Podczas pierwszego tworzenia konta usługi multimediów tworzony jest domyślny punkt końcowy przesyłania strumieniowego, który jest w stanie zatrzymania, dlatego należy go uruchomić, aby można było przesyłać strumieniowo zawartość.<br/>Opłaty są naliczane tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomienia.

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz wypróbować inne Przewodniki Szybki Start, należy zamieścić do utworzonych zasobów. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten przewodnik Szybki Start, i Usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

[Szyfrowanie zawartości przy użyciu portalu](drm-encrypt-content-how-to.md)
