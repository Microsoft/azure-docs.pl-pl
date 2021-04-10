---
title: Przesyłanie strumieniowe plików wideo za pomocą Azure Media Services — .NET
description: Wykonaj kroki tego samouczka, aby utworzyć nowe konto Azure Media Services, zakodować plik i przesłać strumieniowo do Azure Media Player przy użyciu platformy .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: e7c35e079a6f4bf6c40992debace60e10631a8ba
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962052"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo — .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym samouczku pokazano, jak łatwo zakodować i rozpocząć przesyłanie strumieniowe wideo na wielu różnych przeglądarkach i urządzeniach przy użyciu Azure Media Services. Zawartość wejściową można określić przy użyciu adresów URL HTTP, adresów URL SAS lub ścieżek do plików znajdujących się w magazynie obiektów blob platformy Azure.
W przykładzie w tym temacie kodowana jest zawartość, która jest udostępniana za pośrednictwem adresu URL protokołu HTTPS. Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS.

Na końcu samouczka będziesz mieć możliwość przesyłania strumieniowego wideo.  

![Odtwarzanie wideo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz zainstalowanego programu Visual Studio, możesz pobrać program [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Utwórz konto Media Services](./account-create-how-to.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Będą one potrzebne w celu uzyskania dostępu do interfejsu API.

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub zawierające przykład przesyłania strumieniowego platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

Przykład znajduje się w folderze [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Otwórz [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) w pobranym projekcie. Zastąp wartości przy użyciu poświadczeń uzyskanych w sekcji z opisem [uzyskiwania dostępu do interfejsów API](./access-api-howto.md).

W przykładzie są wykonywane następujące akcje:

1. Tworzenie **przekształcenia** (najpierw następuje sprawdzenie, czy podane przekształcenie istnieje). 
2. Tworzy element **zawartości** wyjściowej, który jest używany jako dane wyjściowe **zadania** kodowania.
3. Tworzenie danych wejściowych **zadania** opartych na adresie URL protokołu HTTPS.
4. Przesłanie **zadania** kodowania przy użyciu danych przychodzących i wychodzących, które zostały utworzone wcześniej.
5. Sprawdzanie stanu zadania.
6. Tworzy **lokalizator przesyłania strumieniowego**.
7. Utworzenie adresów URL przesyłania strumieniowego.

Objaśnienia działania poszczególnych funkcji w przykładzie znajdziesz po sprawdzeniu kodu i przyjrzeniu się komentarzom w [tym pliku źródłowym](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Po uruchomieniu aplikacji zostaną wyświetlone adresy URL, których można użyć do odtwarzania wideo za pomocą różnych protokołów. 

1. Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację *EncodeAndStreamFiles*.
2. Wybierz protokół **HLS** firmy Apple — kończy się ciągiem *manifest(format=m3u8-aapl)*— i skopiuj adres URL przesyłania strumieniowego z konsoli.

![Zrzut ekranu przedstawiający dane wyjściowe z aplikacji EncodeAndStreamFiles w programie Visual Studio pokazujący trzy adresy URL przesyłania strumieniowego do użycia w Azure Media Player.](./media/stream-files-tutorial-with-api/output.png)

W przykładowym [kodzie źródłowym](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) możesz zobaczyć, jak jest zbudowany adres URL. Aby go utworzyć, musisz połączyć nazwę hosta punktu końcowego przesyłania strumieniowego i ścieżkę lokalizatora przesyłania strumieniowego.  

## <a name="test-with-azure-media-player"></a>Testowanie przy użyciu usługi Azure Media Player

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. 

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę internetową i przejdź do [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. W polu **Adres URL** wklej jedną z wartości adresu URL przesyłania strumieniowego otrzymanych po uruchomieniu aplikacji. 
 
     Adres URL możesz wkleić w formacie HLS, Dash, lub Smooth, a usługa Azure Media Player przełączy się na odpowiedni protokół przesyłania strumieniowego w celu automatycznego odtworzenia na urządzeniu.
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już żadnych zasobów w grupie zasobów, w tym Media Services i kont magazynu utworzonych dla tego samouczka, Usuń grupę zasobów.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Analizowanie kodu

Objaśnienia działania poszczególnych funkcji w przykładzie znajdziesz po sprawdzeniu kodu i przyjrzeniu się komentarzom w [tym pliku źródłowym](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

Samouczek [przekazywania, kodowania i strumieniowego przesyłania plików](stream-files-tutorial-with-api.md) zawiera bardziej zaawansowany przykład przesyłania strumieniowego wraz ze szczegółowymi objaśnieniami. 

### <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Wielowątkowość

Zestawy SDK usługi Azure Media Services 3 nie są bezpieczne wątkowo. Podczas pracy z aplikacją wielowątkową należy wygenerować nowy obiekt AzureMediaServicesClient dla każdego wątku.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md)
