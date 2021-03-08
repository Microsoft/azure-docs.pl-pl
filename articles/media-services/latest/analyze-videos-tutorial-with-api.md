---
title: Analizowanie filmów wideo z Media Services v3
description: Dowiedz się, jak analizować wideo przy użyciu Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 4a050d838bae9b394f5f292698781a9a824af0bf
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454128"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Samouczek: analizowanie filmów wideo z Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Mimo że w tym samouczku są używane przykłady [zestawu SDK dla platformy .NET](/dotnet/api/microsoft.azure.management.media.models.liveevent) , ogólne kroki są takie same dla [interfejsu API REST](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event)lub innych obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

W tym samouczku przedstawiono sposób analizowania wideo za pomocą usługi Azure Media Services. Istnieje wiele scenariuszy, w przypadku których głęboka analiza zarejestrowanego wideo lub dźwięku może być przydatna. Na przykład organizacje mogą zamieniać mowę na tekst w celu przekształcenia nagrań rozmów działu obsługi klienta w katalog z możliwością wyszukiwania, indeksami i pulpitami nawigacyjnymi — aby dzięki jego wykorzystaniu zwiększyć zadowolenie klientów. Następnie mogą uzyskać wgląd w swoją firmę. Te informacje obejmują listę typowych zażaleń, źródeł takich zażaleń i innych przydatnych informacji.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Pobierz przykładową aplikację opisaną w temacie.
> * Bada kod, który analizuje określony film wideo.
> * Uruchom aplikację.
> * Sprawdź dane wyjściowe.
> * Wyczyść zasoby.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Zgodność, ochrona prywatności i zabezpieczenia
 
Ważną kwestią jest przestrzeganie wszystkich obowiązujących przepisów dotyczących używania Video Indexer i nie można używać Video Indexer lub żadnej innej usługi platformy Azure w sposób naruszający prawa innych lub mogą być szkodliwe dla innych osób. Przed przekazaniem jakichkolwiek filmów wideo, w tym wszelkich danych biometrycznych, do usługi Video Indexer na potrzeby przetwarzania i przechowywania, należy dysponować wszystkimi właściwymi prawami, w tym wszystkimi odpowiednimi komunikatami o zgodzie, z poszczególnych osób w filmie wideo. Aby dowiedzieć się o zgodności, ochronie prywatności i bezpieczeństwie w Video Indexer, [warunki Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)firmy Microsoft. W celu zachowania poufności informacji firmy Microsoft i obsługi danych należy zapoznać się z zasadami [zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement)firmy Microsoft, tematami dotyczącymi [usług online](https://www.microsoft.com/licensing/product-licensing/products) ("Ost") i [uzupełnieniem przetwarzania danych](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Dodatkowe informacje o ochronie prywatności, takie jak przechowywanie danych, usuwanie/niszczenie, są dostępne w pliku OST i [tutaj](../video-indexer/faq.md). Korzystając z Video Indexer, wyrażasz zgodę na związanie Cognitive Services warunkami, OST, DPA i zasad zachowania poufności informacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz zainstalowanego programu Visual Studio, Pobierz [program Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Utwórz konto Media Services](./create-account-howto.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Musisz użyć ich do uzyskania dostępu do interfejsu API.

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub zawierające przykład dla platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Przykład znajduje się w folderze [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Otwórz [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) w pobranym projekcie. Zastąp wartości poświadczeniami uzyskanymi w celu [uzyskania dostępu do interfejsów API](./access-api-howto.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Badanie kodu do analizy wybranego wideo

W tej sekcji są analizowane funkcje zdefiniowane w pliku [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) projektu *AnalyzeVideos*.

Przykład wykonuje następujące czynności:

1. Tworzy **przekształcenie** i **zadanie** analizujące wideo.
2. Tworzy **zasób** wejściowy i przekazuje do niego wideo. Zasób jest używany jako dane wejściowe zadania.
3. Tworzy zasób wyjściowy, w którym są przechowywane dane wyjściowe zadania.
4. Przesyła zadanie.
5. Sprawdza stan zadania.
6. Pobiera pliki, które powstały w wyniku uruchomienia zadania.

> [!NOTE]
> Jeśli używasz wstępnych ustawień analizatora wideo lub dźwięku, skorzystaj z witryny Azure Portal i ustaw na koncie 10 jednostek zarezerwowanych multimediów S3. Aby uzyskać więcej informacji, zobacz temat [Scale media processing (Skalowanie przetwarzania multimediów)](media-reserved-units-cli-how-to.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Tworzenie zasobu wejściowego i przekazywanie do niego pliku lokalnego 

Funkcja **CreateInputAsset** tworzy nowy [zasób](/rest/api/media/assets) wejściowy i przekazuje do niego określony lokalny plik wideo. Ten zasób służy jako dane wejściowe zadania kodowania. W usłudze Media Services 3 danymi wejściowymi zadania może być zasób lub zawartość udostępniona dla konta usługi Media Services za pośrednictwem adresów URL protokołu HTTPS. Aby dowiedzieć się, jak kodować przy użyciu adresu URL HTTPS, zobacz [ten](job-input-from-http-how-to.md) artykuł.  

W usłudze Media Services 3 do przekazywania plików służą interfejsy API usługi Azure Storage. Przedstawia to poniższy fragment kodu dla platformy .NET.

Następująca funkcja wykonuje następujące czynności:

* Tworzy element zawartości.
* Pobiera zapisywalny [adres URL sygnatury dostępu współdzielonego](../../storage/common/storage-sas-overview.md) do [kontenera zasobów w magazynie](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).

    W przypadku używania funkcji [ListContainerSas](/rest/api/media/assets/listcontainersas) zasobów do uzyskiwania adresów URL sygnatury dostępu współdzielonego należy zauważyć, że funkcja zwraca wiele adresów URL sygnatury dostępu współdzielonego, ponieważ istnieją dwa klucze konta magazynu dla każdego konta magazynu. Konto magazynu ma dwa klucze, ponieważ umożliwia bezproblemowe obracanie kluczy konta magazynu (na przykład zmiana jednego z nich, a następnie rozpoczęcie korzystania z nowego klucza i obracanie drugiego klucza). Pierwszy adres URL sygnatury dostępu współdzielonego reprezentuje Klucz1 magazynu i drugi klucz2 magazynu.
* Przekazuje plik do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Tworzenie zasobu wyjściowego do przechowywania wyników zadania

[Zasób](/rest/api/media/assets) wyjściowy przechowuje wynik zadania. Projekt definiuje funkcję **DownloadResults**, która pobiera wyniki z zasobu wyjściowego do folderu wyjściowego, umożliwiając zapoznanie się z nimi.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Tworzenie przekształcenia i zadania analizującego wideo

Podczas kodowania lub przetwarzania zawartości w Media Services jest to typowy wzorzec służący do konfigurowania ustawień kodowania jako przepisu. Następnie przesyła się **zadanie** w celu zastosowania tego przepisu do wideo. Przesyłając nowe zadania dla każdego nowego filmu wideo, stosujesz ten przepis do wszystkich filmów wideo w bibliotece. Przepis w Media Services jest nazywany **przekształceniem**. Aby uzyskać więcej informacji, zobacz [transformacje i zadania](./transforms-jobs-concept.md). Przykład opisany w tym samouczku umożliwia zdefiniowanie przepisu, który analizuje określone wideo.

#### <a name="transform"></a>Przekształcanie

Podczas tworzenia nowego wystąpienia obiektu [Transform](/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. **TransformOutput**  jest wymaganym parametrem. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania docelowego obiektu **TransformOutput**. W tym przykładzie używane jest ustawienie wstępne **VideoAnalyzerPreset** i język ("en-us") jest przenoszona do jego konstruktora ( `new VideoAnalyzerPreset("en-US")` ). To ustawienie wstępne umożliwia przeprowadzenie wielu analiz dźwięku i wideo tworzących plik wideo. Istnieje możliwość użycia ustawienia wstępnego **AudioAnalyzerPreset**, jeśli jest potrzebne przeprowadzenie wielu analiz dźwięku w pliku wideo.

Podczas tworzenia **przekształcenia** Sprawdź pierwsze, jeśli już istnieje przy użyciu metody **Get** , jak pokazano w poniższym kodzie. W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Zadanie

Jak wspomniano powyżej, obiekt [Transform](/rest/api/media/transforms) jest przepisem, a obiekt [Job](/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. **Zadanie** określa informacje na przykład o lokalizacji wejściowego wideo i lokalizacji danych wyjściowych. Istnieje możliwość określenia lokalizacji pliku wideo za pomocą adresów URL protokołu HTTPS, adresów URL sygnatury dostępu współdzielonego lub zasobów znajdujących się na koncie usługi Media Service.

W tym przykładzie dane wejściowe zadania to lokalny film wideo.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania trwa jakiś czas. Gdy tak jest, chcesz otrzymywać powiadomienia. Są dostępne różne opcje powiadamiania o ukończeniu [zadania](/rest/api/media/jobs). Najprostszą opcją (wyświetlaną tutaj) jest użycie sondowania.

Sondowanie nie jest zalecanym najlepszym rozwiązaniem w przypadku aplikacji produkcyjnych ze względu na potencjalne opóźnienia. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid.

Usługę Event Grid zaprojektowano pod kątem wysokiej dostępności, stałego poziomu wydajności i dynamicznej skalowalności. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń pochodzących z praktycznie wszystkich usług platformy Azure i ze źródeł niestandardowych oraz reagować na nie. Prosta, reaktywna obsługa zdarzeń oparta na protokole HTTP pomaga w tworzeniu wydajnych rozwiązań za pośrednictwem inteligentnego filtrowania i routingu zdarzeń. Aby uzyskać więcej informacji, zobacz temat [kierowanie zdarzeń do niestandardowego punktu końcowego sieci Web](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli w zadaniu wystąpi błąd, zostanie wyświetlony stan **błędu** . Jeśli zadanie jest w trakcie anulowania, otrzymujesz **Anulowanie** , a następnie **anulowane** po jego zakończeniu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Pobieranie wyniku zadania

Poniższa funkcja pobiera wyniki z wyjściowego [elementu zawartości](/rest/api/media/assets) do folderu "output", aby można było przejrzeć wyniki zadania.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Czyszczenie zasobów na koncie usługi Media Services

Ogólnie rzecz biorąc, należy wyczyścić wszystko z wyjątkiem obiektów, które są planowane do ponownego użycia (zazwyczaj spowoduje to ponowne użycie przekształceń i trwałe StreamingLocators). Jeśli chcesz, aby Twoje konto było czyste po eksperymentie, Usuń zasoby, których nie planujesz ponownie używać. Na przykład poniższy kod usuwa zasób zadania i wyjściowego:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Naciśnij klawisze CTRL + F5, aby uruchomić aplikację *AnalyzeVideos* .

Po uruchomieniu programu zadanie tworzy miniatury dla każdej twarzy wykrytej w wideo. Tworzy także plik insights.json.

## <a name="examine-the-output"></a>Sprawdzanie danych wyjściowych

Plik wyjściowy analizy wideo ma nazwę insights.json. Ten plik zawiera wyniki analiz wideo. Opis elementów znajdujących się w pliku json zawiera artykuł [Inteligentna analiza multimediów](./analyzing-video-audio-files-concept.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Wielowątkowość

Zestawy SDK Azure Media Services V3 nie są bezpieczne wątkowo. Podczas pracy z aplikacją wielowątkową należy wygenerować nowy obiekt AzureMediaServicesClient na wątek.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md)
