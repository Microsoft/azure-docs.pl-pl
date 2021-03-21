---
title: Kodowanie i przesyłanie strumieniowe plików wideo za pomocą Node.JS
description: Przesyłanie strumieniowe plików wideo za pomocą Node.JS. Wykonaj kroki tego samouczka, aby utworzyć nowe konto Azure Media Services, zakodować plik i przesłać go strumieniowo do Azure Media Player.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: usługi Azure Media Services, Stream, Node.JS
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: b7efa37747e6d116b4cf26b4cf54377037f22cdd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212760"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Kodowanie i przesyłanie strumieniowe plików wideo za pomocą Node.JS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ten przewodnik Szybki start pokazuje, jak łatwo jest zakodować i rozpocząć strumieniowe przesyłanie wideo do różnych przeglądarek i urządzeń za pomocą usługi Azure Media Services. Wejściowy plik wideo można określić za pomocą adresów URL protokołu HTTPS, adresów URL sygnatury dostępu współdzielonego lub ścieżek do plików znajdujących się w usłudze Azure Blob Storage.

Po zakończeniu tego przewodnika Szybki Start zobaczysz następujące informacje:

- Jak kodować przy użyciu Node.JS
- Jak przesyłać strumieniowo przy użyciu Node.JS
- Jak przekazać plik z adresu URL HTTPS za pomocą Node.JS
- Jak używać HLS lub łącznika klienta pauzy z Node.JS

W przykładzie w tym artykule zawarto informacje o zawartości, która jest dostępna za pośrednictwem adresu URL HTTPS. Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS.

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

Otwórz [indeks. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) w pobranym projekcie. Zaktualizuj plik *Sample. env* w folderze głównym przy użyciu wartości i poświadczeń uzyskanych od [uzyskiwania dostępu do interfejsów API](./access-api-howto.md). Zmień nazwę *przykładowego pliku ENV* na *. env* (tak, tylko rozszerzenie).

W przykładzie są wykonywane następujące akcje:

1. Tworzy **transformację** z [ustawieniem wstępnym kodowania z uwzględnieniem zawartości](./content-aware-encoding.md). Najpierw sprawdza, czy istnieje określone przekształcenie.
1. Tworzy element **zawartości** wyjściowej, który jest używany przez **zadanie** kodowania do przechowywania danych wyjściowych
1. Opcjonalnie przekazuje plik lokalny przy użyciu zestawu SDK magazynu obiektów BLOB
1. Tworzy dane wejściowe **zadania** oparte na adresie URL HTTPS lub przekazanym pliku
1. Przesyła **zadanie** kodowania przy użyciu danych wejściowych i wyjściowych, które zostały utworzone wcześniej.
1. Sprawdza stan zadania
1. Pobiera dane wyjściowe zadania kodowania do folderu lokalnego
1. Tworzy **lokalizator przesyłania strumieniowego** do użycia w odtwarzaczu
1. Kompiluje adresy URL przesyłania strumieniowego dla HLS i ŁĄCZNIKa
1. Odtwarza zawartość z powrotem w aplikacji odtwarzacza — Azure Media Player

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Aplikacja pobiera zakodowane pliki. Utwórz folder, w którym mają zostać przetworzone pliki wyjściowe i zaktualizuj wartość zmiennej **outputFolder** w pliku [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L59) . Domyślnie jest ustawiona wartość "Temp".
1. Otwórz **wiersz polecenia**, przejdź do katalogu przykładowego.
1. Zmień katalog na folder AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Zainstaluj pakiety używane w *packages.js* pliku.

    ```bash
    npm install 
    ```

1. Zmień katalog na folder *StreamFilesSample* .

    ```bash
    cd StreamFilesSample
    ```

1. Uruchom Visual Studio Code z folderu *AMSv3Samples* . (Jest to wymagane do uruchomienia z folderu, w którym znajdują się folder *. programu vscode* i *tsconfig.jsw* plikach).

    ```bash
    cd ..
    code .
    ```

Otwórz folder dla programu *StreamFilesSample* i Otwórz plik *index. ts* w edytorze Visual Studio Code.
W pliku *index. TS* należy nacisnąć klawisz F5, aby uruchomić debuger.

## <a name="test-with-azure-media-player"></a>Testowanie przy użyciu usługi Azure Media Player

Użyj Azure Media Player, aby przetestować strumień. Możesz również użyć dowolnego odtwarzacza zgodnego z HLS lub ŁĄCZNIKa, takiego jak Shaka Player, HLS.js, Dash.js lub innych.

Powinien być w stanie kliknąć link wygenerowany w przykładzie i uruchomić uczestnika AMP z już załadowanym manifestem ŁĄCZNIKa.

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę internetową i przejdź do [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. W polu **adres URL** , wklej jeden z wartości adresu URL przesyłania strumieniowego uzyskanych podczas uruchamiania aplikacji. Możesz wkleić adres URL w HLS, myślniku lub gładkim formacie, a Azure Media Player będzie przełączać się do odpowiedniego protokołu przesyłania strumieniowego w celu automatycznego odtwarzania na urządzeniu.
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już żadnych zasobów w grupie zasobów, w tym Media Services i kont magazynu utworzonych dla tego samouczka, Usuń grupę zasobów.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Więcej dokumentacji dla deweloperów Node.js na platformie Azure

- [Platforma Azure dla & języka JavaScript Node.js deweloperów](/azure/developer/javascript/)
- [Media Services kodu źródłowego w @azure/azure-sdk-for-js repozytorium centrum git](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentacja pakietu platformy Azure dla deweloperów Node.js](/javascript/api/overview/azure/)

## <a name="see-also"></a>Zobacz też

- [Kody błędów zadań](/rest/api/media/jobs/get#joberrorcode).
- [npm Zainstaluj @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Platforma Azure dla & języka JavaScript Node.js deweloperów](/azure/developer/javascript/)
- [Media Services kodu źródłowego w @azure/azure-sdk-for-js repozytorium](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Następne kroki

> [Koncepcje Media Services](concepts-overview.md)
