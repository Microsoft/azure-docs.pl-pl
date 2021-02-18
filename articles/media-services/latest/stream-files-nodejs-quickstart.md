---
title: Przesyłanie strumieniowe plików wideo za pomocą Azure Media Services-Node.js
description: Wykonaj kroki tego samouczka, aby utworzyć nowe konto Azure Media Services, zakodować plik i przesłać go strumieniowo do Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: 0eb334111a8f5ffc63d0f858966e4e65f99d3b16
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095953"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo — Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym samouczku pokazano, jak łatwo zakodować i rozpocząć przesyłanie strumieniowe wideo na wielu różnych przeglądarkach i urządzeniach przy użyciu Azure Media Services. Wejściowy plik wideo można określić za pomocą adresów URL protokołu HTTPS, adresów URL sygnatury dostępu współdzielonego lub ścieżek do plików znajdujących się w usłudze Azure Blob Storage.

W przykładzie w tym artykule zawarto informacje o zawartości, która jest dostępna za pośrednictwem adresu URL HTTPS. Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS.

Na końcu samouczka dowiesz się, jak przekazywać, kodować i przesyłać strumieniowo wideo przy użyciu HLS lub ŁĄCZNIKa klienta.

![Odtwarzanie wideo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj [Node.js](https://nodejs.org/en/download/)
- [Utwórz konto Media Services](./create-account-howto.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Będą one potrzebne w celu uzyskania dostępu do interfejsu API.
- Zapoznaj się [z tematem Konfigurowanie i nawiązywanie połączenia Node.js](./configure-connect-nodejs-howto.md) od początku, jak korzystać z zestawu SDK klienta Node.js

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub zawierające przykład Node.js przesyłania strumieniowego do maszyny przy użyciu następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Przykład znajduje się w folderze [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) .

Otwórz [indeks. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) w pobranym projekcie. Zaktualizuj plik Sample. env w folderze głównym przy użyciu wartości i poświadczeń uzyskanych od [uzyskiwania dostępu do interfejsów API](./access-api-howto.md). Zmień nazwę przykładowego pliku ENV na. env. 

W przykładzie są wykonywane następujące akcje:

1. Tworzenie **przekształcenia** (najpierw następuje sprawdzenie, czy podane przekształcenie istnieje). 
2. Tworzy element **zawartości** wyjściowej, który jest używany jako dane wyjściowe **zadania** kodowania.
1. Opcjonalnie umożliwia przekazanie pliku lokalnego przy użyciu zestawu SDK magazynu obiektów BLOB.
1. Tworzy dane wejściowe **zadania** oparte na adresie URL HTTPS lub przekazanym pliku
1. Przesyła **zadanie** kodowania z [ustawieniem predefiniowanym kodowania zawartości](./content-aware-encoding.md)przy użyciu danych wejściowych i wyjściowych, które zostały utworzone wcześniej.
1. Sprawdzanie stanu zadania.
1. Pobiera dane wyjściowe zadania kodowania do folderu lokalnego.
1. Tworzy **lokalizator przesyłania strumieniowego** do użycia w odtwarzaczu.
1. Kompiluje adresy URL przesyłania strumieniowego dla HLS i ŁĄCZNIKa.
1. Odtwarza zawartość z powrotem w aplikacji odtwarzacza — Azure Media Player.

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

1. Aplikacja pobiera zakodowane pliki. Utwórz folder, w którym chcesz umieścić pliki wyjściowe i zaktualizować wartość zmiennej **outputFolder** w pliku [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L59) . Domyślnie jest ustawiona wartość "Temp".
1. Otwórz **wiersz polecenia**, przejdź do katalogu przykładowego i wykonaj następujące polecenia.
1. Zmień katalog na folder AMSv3Samples
    ```bash
    cd AMSv3Samples
    ```

1. Zainstaluj pakiety używane w packages.jsna
    ```bash
    npm install 
    ```

1. Zmień katalog na folder StreamFilesSample
    ```bash
    cd StreamFilesSample
    ```

1. Uruchom Visual Studio Code z folderu AMSv3Samples. Jest to wymagane do uruchomienia z folderu, w którym znajdują się folder ". programu vscode" i tsconfig.jsw plikach

    ```bash
    cd ..
    code .
    ```

Otwórz folder dla programu StreamFilesSample i Otwórz plik index. TS w edytorze Visual Studio Code.
W pliku index. TS należy nacisnąć klawisz F5, aby uruchomić debuger.


## <a name="test-with-azure-media-player"></a>Testowanie przy użyciu usługi Azure Media Player

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. Możesz również użyć dowolnego odtwarzacza zgodnego z HLS lub ŁĄCZNIKa, takiego jak Shaka Player, HLS.js, Dash.js lub innych.

Powinien być w stanie kliknąć link generowany w przykładzie i uruchomić uczestnika AMP z już załadowanym manifestem ŁĄCZNIKa.

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

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Więcej dokumentacji dla deweloperów Node.js na platformie Azure
- [Platforma Azure dla & języka JavaScript Node.js deweloperów](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services kodu źródłowego w @azure/azure-sdk-for-js repozytorium centrum git](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentacja pakietu platformy Azure dla deweloperów Node.js](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="see-also"></a>Zobacz też

- [Kody błędów zadań](/rest/api/media/jobs/get#joberrorcode).
- [npm Zainstaluj @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Platforma Azure dla & języka JavaScript Node.js deweloperów](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services kodu źródłowego w @azure/azure-sdk-for-js repozytorium](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Koncepcje Media Services](concepts-overview.md)
