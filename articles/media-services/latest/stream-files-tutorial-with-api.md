---
title: przekazywanie, kodowanie i przesyłanie strumieniowe z Media Services v3: Azure Media Services Description: samouczek pokazujący, jak przekazać plik, zakodować wideo i przesyłać strumieniowo zawartość z Azure Media Services v3.
usługi: Media-Services documentationcenter: "" Author: IngridAtMicrosoft Manager: femila Editor: ""

MS. Service: Media-Services MS. obciążenie: MS. temat: samouczek MS. Custom: MVC MS. Date: 03/17/2021 MS. Author: inhenkel
---

# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo z Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Mimo że w tym samouczku są używane przykłady [zestawu SDK dla platformy .NET](/dotnet/api/microsoft.azure.management.media.models.liveevent) , ogólne kroki są takie same dla [interfejsu API REST](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event)lub innych obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

Azure Media Services umożliwia Kodowanie plików multimedialnych do formatów, które są odtwarzane w różnych przeglądarkach i urządzeniach. Na przykład może zaistnieć potrzeba strumieniowego odtwarzania treści w formatach HLS lub MPEG DASH firmy Apple. Przed odtwarzaniem strumieniowym należy zakodować wysokiej jakości plik multimediów cyfrowych. Aby uzyskać pomoc dotyczącą kodowania, zobacz [koncepcji kodowania](encode-concept.md). W tym samouczku opisano przekazanie lokalnego pliku wideo oraz jego kodowanie. Możesz również kodować zawartość, która jest dostępna za pośrednictwem adresu URL HTTPS. Aby uzyskać więcej informacji, zobacz temat [Create a job input from an HTTP(s) URL](job-input-from-http-how-to.md) (Tworzenie danych wejściowych zadania przy użyciu adresu URL protokołów HTTP).

![Odtwórz wideo za pomocą Azure Media Player](./media/stream-files-tutorial-with-api/final-video.png)

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Pobierz przykładową aplikację opisaną w temacie.
> * Przejrzyj kod, który przekazuje, koduje i przesyła strumieniowo.
> * Uruchom aplikację.
> * Przetestuj adres URL przesyłania strumieniowego.
> * Wyczyść zasoby.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz zainstalowanego programu Visual Studio, możesz uzyskać [program Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Utwórz konto Media Services](./account-create-how-to.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Musisz użyć ich do uzyskania dostępu do interfejsu API.

## <a name="download-and-set-up-the-sample"></a>Pobieranie i Konfigurowanie przykładu

Sklonuj repozytorium GitHub, które ma przykład platformy .NET przesyłania strumieniowego do maszyny przy użyciu następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Przykład znajduje się w folderze [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Otwórz [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) w pobranym projekcie. Zastąp wartości przy użyciu poświadczeń uzyskanych w sekcji z opisem [uzyskiwania dostępu do interfejsów API](./access-api-howto.md).

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Sprawdzanie kodu operacji przekazywania, kodowania i odtwarzania strumieniowego

W tej sekcji są analizowane funkcje zdefiniowane w pliku [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) projektu *UploadEncodeAndStreamFiles*.

W przykładzie są wykonywane następujące akcje:

1. Tworzy nowe **przekształcenie** (najpierw sprawdza, czy istnieje określone przekształcenie).
2. Tworzy element **zawartości** wyjściowej, który jest używany jako dane wyjściowe **zadania** kodowania.
3. Tworzenie **zasobu** danych wejściowych oraz przekazanie do niego wybranego lokalnego pliku wideo. Zasób jest używany jako dane wejściowe zadania.
4. Przesyłanie zadania kodowania przy użyciu utworzonych danych wejściowych i wyjściowych.
5. Sprawdza stan zadania.
6. Tworzy **lokalizator przesyłania strumieniowego**.
7. Utworzenie adresów URL przesyłania strumieniowego.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane przez klienta do nawiązania połączenia z platformą Azure przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Tworzenie zasobu wejściowego i przekazywanie do niego pliku lokalnego

Funkcja **CreateInputAsset** tworzy nowy [zasób](/rest/api/media/assets) wejściowy i przekazuje do niego określony lokalny plik wideo. Ten element **zawartości** jest używany jako dane wejściowe zadania kodowania. W Media Services v3 dane wejściowe **zadania** mogą być **zasobem** lub zawartością udostępnianą dla konta Media Services za pośrednictwem adresów URL https. Aby dowiedzieć się, jak kodować przy użyciu adresu URL HTTPS, zobacz [ten](job-input-from-http-how-to.md) artykuł.

W usłudze Media Services 3 do przekazywania plików służą interfejsy API usługi Azure Storage. Przedstawia to poniższy fragment kodu dla platformy .NET.

Poniższa funkcja wykonuje następujące akcje:

* Tworzy element **zawartości**.
* Pobiera zapisywalny [adres URL sygnatury dostępu współdzielonego](../../storage/common/storage-sas-overview.md) do [kontenera zasobów w magazynie](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).

    W przypadku używania funkcji [ListContainerSas](/rest/api/media/assets/listcontainersas) zasobów do uzyskiwania adresów URL sygnatury dostępu współdzielonego należy zauważyć, że funkcja zwraca wiele adresów URL sygnatury dostępu współdzielonego, ponieważ istnieją dwa klucze konta magazynu dla każdego konta magazynu. Konto magazynu ma dwa klucze, ponieważ umożliwia bezproblemowe obracanie kluczy konta magazynu (na przykład zmiana jednego z nich, a następnie rozpoczęcie korzystania z nowego klucza i obracanie drugiego klucza). Pierwszy adres URL sygnatury dostępu współdzielonego reprezentuje Klucz1 magazynu i drugi klucz2 magazynu.
* Przekazuje plik do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Tworzenie zasobu wyjściowego na potrzeby przechowywania wyników zadania

Wyjściowy element [zawartości](/rest/api/media/assets) przechowuje wynik zadania kodowania. Projekt definiuje funkcję **DownloadResults**, która pobiera wyniki z zasobu wyjściowego do folderu wyjściowego, umożliwiając zapoznanie się z nimi.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Tworzenie przekształcenia i zadania kodującego przekazany plik

Podczas kodowania lub przetwarzania zawartości w Media Services jest to typowy wzorzec służący do konfigurowania ustawień kodowania jako przepisu. Następnie przesyła się **zadanie** w celu zastosowania tego przepisu do wideo. Przesyłając nowe zadania dla każdego nowego filmu wideo, stosujesz ten przepis do wszystkich filmów wideo w bibliotece. Przepis w Media Services jest nazywany **przekształceniem**. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](./transform-jobs-concept.md). Przykład opisany w tym samouczku definiuje przepis, który umożliwia kodowanie wideo w celu jego przesyłania strumieniowego do różnych urządzeń z systemami iOS i Android.

#### <a name="transform"></a>Przekształcanie

Podczas tworzenia nowego wystąpienia obiektu [Transform](/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**, jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Element Preset umożliwia kodowanie wejściowego wideo do automatycznie generowanej drabiny szybkości transmisji bitów (zestawu par „szybkość transmisji bitów-rozdzielczość”) na podstawie rozdzielczości wejściowej i szybkości transmisji bitów oraz tworzenie plików MP4 zgodnych ze standardem ISO, które zawierają wideo w formacie H.264 i dźwięk w formacie AAC, dla każdej pary „szybkość transmisji bitów-rozdzielczość”. Aby dowiedzieć się więcej na temat elementu Preset, zobacz informacje o [automatycznie generowanej drabinie szybkości transmisji bitów](encode-autogen-bitrate-ladder.md).

Możesz użyć wbudowanych elementów EncoderNamedPreset lub użyć niestandardowych ustawień wstępnych. Aby uzyskać więcej informacji, zobacz temat [How to customize encoder presets](transform-custom-presets-how-to.md) (Dostosowywanie ustawień wstępnych kodera).

Podczas tworzenia obiektu [Transform](/rest/api/media/transforms) należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie. W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Zadanie

Jak wspomniano powyżej, obiekt [Transform](/rest/api/media/transforms) jest przepisem, a obiekt [Job](/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. Obiekt **Job** określa informacje takie jak lokalizacja wejściowego pliku wideo oraz danych wyjściowych.

W tym przykładzie wejściowy plik wideo został przekazany z maszyny lokalnej. Jeśli chcesz dowiedzieć się, jak kodować przy użyciu adresu URL HTTPS, zobacz [ten](job-input-from-http-how-to.md) artykuł.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania zajmuje trochę czasu, a Ty chcesz otrzymać powiadomienie o tym fakcie. Poniższy przykład kodu pokazuje, jak sondować usługę pod kątem stanu [zadania](/rest/api/media/jobs). Sondowanie nie jest zalecanym najlepszym rozwiązaniem w przypadku aplikacji produkcyjnych ze względu na potencjalne opóźnienia. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid.

Usługę Event Grid zaprojektowano pod kątem wysokiej dostępności, stałego poziomu wydajności i dynamicznej skalowalności. Dzięki usłudze Event Grid Twoje aplikacje mogą nasłuchiwać zdarzeń pochodzących z praktycznie wszystkich usług platformy Azure i ze źródeł niestandardowych oraz reagować na nie. Prosta, reaktywna obsługa zdarzeń oparta na protokole HTTP pomaga w tworzeniu wydajnych rozwiązań za pośrednictwem inteligentnego filtrowania i routingu zdarzeń.  Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](monitoring/job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli zadanie napotka błąd, może być w stanie **Error (Błąd)**. Jeśli zadanie jest w trakcie jego anulowania, po zakończeniu zostanie **anulowane** i **usunięte** .

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Pobieranie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania następnym krokiem jest udostępnienie klientom w zasobie wyjściowym pliku wideo, który można odtwarzać. Można to zrobić w dwóch krokach: najpierw utworzyć [lokalizator przesyłania strumieniowego](/rest/api/media/streaminglocators), a drugi — utworzyć adresy URL przesyłania strumieniowego, które mogą być używane przez klientów.

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołania interfejsu API i obowiązuje do momentu jego usunięcia, chyba że zostanie skonfigurowany opcjonalny czas rozpoczęcia i zakończenia.

Podczas tworzenia [StreamingLocator](/rest/api/media/streaminglocators)należy określić żądany **StreamingPolicyName**. W tym przykładzie nastąpi przesyłanie strumieniowe w postaci nieoczyszczonej (lub nieszyfrowanej zawartości), co spowoduje użycie wstępnie zdefiniowanych zasad wyznaczania strumienia (**PredefinedStreamingPolicy. ClearStreamingOnly**).

> [!IMPORTANT]
> W przypadku korzystania z niestandardowych [zasad przesyłania strumieniowego](/rest/api/media/streamingpolicies) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji zasad przesyłania strumieniowego. Nie należy tworzyć nowych zasad przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.

Poniższy kod założono, że wywołujesz funkcję z unikatowym identyfikatorem lokalizatora.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Przykład przedstawiony w tym temacie dotyczy przesyłania strumieniowego, lecz tego samego wywołania można użyć do utworzenia lokalizatora przesyłania strumieniowego na potrzeby dostarczania wideo przy użyciu pobierania progresywnego.

### <a name="get-streaming-urls"></a>Pobieranie adresów URL przesyłania strumieniowego

Teraz, po utworzeniu [lokalizatora przesyłania strumieniowego](/rest/api/media/streaminglocators), możesz pobrać adresy URL przesyłania strumieniowego, jak pokazano w metodzie **GetStreamingURLs**. Aby utworzyć adres URL, musisz połączyć nazwę hosta [punktu końcowego przesyłania strumieniowego](/rest/api/media/streamingendpoints) i ścieżkę **lokalizatora przesyłania strumieniowego**. W tym przykładzie jest używany *domyślny* **punkt końcowy przesyłania strumieniowego**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* **punkt końcowy przesyłania strumieniowego** będzie zatrzymany, więc należy wywołać metodę **Start**.

> [!NOTE]
> W przypadku tej metody jest potrzebny element locatorName, który został użyty podczas tworzenia **lokalizatora przesyłania strumieniowego** dla zasobu wyjściowego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Ogólnie rzecz biorąc, należy wyczyścić wszystko z wyjątkiem obiektów, które są planowane do ponownego użycia (zazwyczaj spowoduje to ponowne użycie przekształceń i StreamingLocators itp.). Jeśli chcesz, aby Twoje konto było czyste po eksperymentie, Usuń zasoby, których nie planujesz ponownie używać. Na przykład poniższy kod usuwa zadanie, utworzone zasoby i zasady klucza zawartości:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

1. Naciśnij klawisze CTRL + F5, aby uruchomić aplikację *EncodeAndStreamFiles* .
2. Skopiuj jeden z adresów URL przesyłania strumieniowego z konsoli.

Ten przykład umożliwia wyświetlenie adresów URL, których można użyć do odtworzenia wideo za pomocą różnych protokołów:

![Przykładowe dane wyjściowe pokazujące adresy URL wideo przesyłania strumieniowego Media Services](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testowanie adresu URL przesyłania strumieniowego

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player.

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę internetową i przejdź do [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. W polu **adres URL** , wklej jeden z wartości adresu URL przesyłania strumieniowego uzyskanych podczas uruchamiania aplikacji.
3. Wybierz pozycję **Aktualizuj odtwarzacz**.

Azure Media Player może służyć do testowania, ale nie powinien być używany w środowisku produkcyjnym.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Wielowątkowość

Zestawy SDK Azure Media Services V3 nie są bezpieczne wątkowo. Podczas tworzenia aplikacji wielowątkowej należy wygenerować i użyć nowego obiektu AzureMediaServicesClient na wątek.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy już wiesz, jak przekazywać, kodować i przesyłać strumieniowo wideo, zobacz następujący artykuł: 

> [!div class="nextstepaction"]
> [Analizowanie wideo](analyze-videos-tutorial.md)
