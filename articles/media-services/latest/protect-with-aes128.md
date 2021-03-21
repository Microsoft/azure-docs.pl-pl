---
title: Szyfruj wideo przy użyciu algorytmu AES-128: Azure Media Services opis: informacje na temat szyfrowania wideo przy użyciu szyfrowania AES 128-bitowego oraz sposobu korzystania z usługi Key Delivery w programie Azure Media Services.
usługi: Media-Services documentationcenter: "" Author: IngridAtMicrosoft Manager: femila Editor: ""

MS. Service: Media-Services MS. obciążeń: Media ms.tgt_pltfrm: na MS. devlang: na MS. temat: samouczek MS. Date: 03/17/2021 MS. Author: inhenkel

---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Samouczek: szyfrowanie wideo przy użyciu algorytmu AES-128 i korzystanie z usługi dostarczania kluczy

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Mimo że w samouczku są używane przykłady [zestawu SDK platformy .NET](/dotnet/api/microsoft.azure.management.media.models.liveevent) , ogólne kroki są takie same dla [interfejsów API REST](/rest/api/media/liveevents), interfejsu [wiersza polecenia](/cli/azure/ams/live-event)lub innych obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

Za pomocą Media Services można dostarczyć HTTP Live Streaming (HLS), MPEG-myślnik i Smooth Streaming zaszyfrowanych za pomocą algorytmu AES przy użyciu kluczy szyfrowania 128-bitowych. Media Services zapewnia również usługę dostarczania kluczy, która dostarcza klucze szyfrowania do autoryzowanych użytkowników. Jeśli chcesz, aby Media Services dynamicznie szyfrować wideo, należy skojarzyć klucz szyfrowania z lokalizatorem przesyłania strumieniowego, a także skonfigurować zasady kluczy zawartości. Gdy gracz prosi o przesłanie strumienia, Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu algorytmu AES-128. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik został autoryzowany do otrzymywania klucza, usługa ocenia zasady kluczy zawartości, które wybrano dla klucza.

Każdy element zawartości można szyfrować przy użyciu wielu typów szyfrowania (AES-128, PlayReady, Widevine, FairPlay). Zobacz [Streaming protocols and encryption types (Protokoły i typy szyfrowania przesyłania strumieniowego)](content-protection-overview.md#streaming-protocols-and-encryption-types), aby sprawdzić, które rozwiązania warto łączyć. Sprawdź również, [jak chronić za pomocą technologii DRM](protect-with-drm.md).

Dane wyjściowe z przykładu ten artykuł zawiera adres URL Azure Media Player, adres URL manifestu i token AES, które są konieczne do odtwarzania zawartości. Przykład ustawia okres ważności tokenu sieci Web JSON (JWT) na 1 godzinę. Możesz otworzyć przeglądarkę i wkleić otrzymany w ten adres URL, aby uruchomić stronę demonstracyjną Azure Media Player z adresem URL i tokenem wypełnionym dla Ciebie już w następującym formacie: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}``` .

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Pobierz przykład [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) opisany w artykule.
> * Rozpocznij korzystanie z interfejsów API Media Services przy użyciu zestawu SDK platformy .NET.
> * Utwórz zasób wyjściowy.
> * Utwórz transformację kodowania.
> * Prześlij zadanie.
> * Poczekaj na zakończenie zadania.
> * Tworzenie zasad klucza zawartości
> * Skonfiguruj zasady, aby używać ograniczeń tokenów JWT.
> * Utwórz lokalizator przesyłania strumieniowego.
> * Skonfiguruj lokalizator przesyłania strumieniowego, aby szyfrować wideo przy użyciu algorytmu AES (ClearKey).
> * Pobierz token testowy.
> * Utwórz adres URL przesyłania strumieniowego.
> * Wyczyść zasoby.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Następujące elementy są wymagane do wykonania czynności przedstawionych w samouczku.

* Zapoznanie się z artykułem [Content protection overview (Omówienie ochrony zawartości)](content-protection-overview.md).
* Zainstalowanie narzędzia Visual Studio Code lub Visual Studio.
* [Utwórz konto Media Services](./create-account-howto.md).
* Uzyskaj poświadczenia potrzebne do używania Media Services interfejsów API przez następujące [interfejsy API dostępu](./access-api-howto.md).

## <a name="download-code"></a>Pobieranie kodu

Sklonuj repozytorium GitHub zawierające pełny przykład dla platformy .NET opisany w tym artykule na swoją maszynę za pomocą następującego polecenia:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Przykład "Szyfruj przy użyciu algorytmu AES-128" znajduje się w folderze [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) .

> [!NOTE]
> Przykład tworzy unikatowe zasoby przy każdym uruchomieniu aplikacji. Zazwyczaj należy ponownie używać istniejących zasobów, takich jak transformacje i zasady (Jeśli istniejący zasób ma wymagane konfiguracje).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API Media Services z platformą .NET, Utwórz obiekt **AzureMediaServicesClient** . Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Tworzenie zasobu wyjściowego  

Wyjściowy element [zawartości](/rest/api/media/assets) przechowuje wynik zadania kodowania.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Pobieranie lub tworzenie obiektu Transform kodowania

Podczas tworzenia nowego wystąpienia obiektu [Transform](/rest/api/media/transforms) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr to obiekt **TransformOutput**, jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. Element **Preset** zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu **TransformOutput**. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Ustawienie wstępne koduje wejściowe wideo do generowanej automatycznie (par rozdzielczości szybkości transmisji bitów) w oparciu o rozdzielczość wejścia i szybkość transmisji bitów, a następnie tworzy pliki MP4 ISO z wideo H. 264 i AAC audio odpowiadające każdej parze rozdzielczości szybkości transmisji bitów.

Przed utworzeniem nowego [przekształcenia](/rest/api/media/transforms)należy najpierw sprawdzić, czy już istnieje przy użyciu metody **Get** , jak pokazano w poniższym kodzie. W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Przesyłanie zadania

Jak wspomniano powyżej, obiekt [Transform](/rest/api/media/transforms) jest przepisem, a obiekt [Job](/rest/api/media/jobs) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. **Zadanie** określa informacje na przykład o lokalizacji wejściowego wideo i lokalizacji danych wyjściowych.

W tym samouczku utworzysz dane wejściowe zadania na podstawie pliku, który jest pobierany bezpośrednio ze [źródłowego adresu URL https](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania trwa jakiś czas. Gdy tak jest, chcesz otrzymywać powiadomienia. Poniższy przykład kodu pokazuje, jak sondować usługę pod kątem stanu [zadania](/rest/api/media/jobs). Sondowanie nie jest zalecanym najlepszym rozwiązaniem w przypadku aplikacji produkcyjnych ze względu na potencjalne opóźnienia. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid. Aby uzyskać więcej informacji, zobacz temat [kierowanie zdarzeń do niestandardowego punktu końcowego sieci Web](monitoring/job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli w zadaniu wystąpi błąd, zostanie wyświetlony stan **błędu** . Jeśli zadanie jest w trakcie jego anulowania, po zakończeniu zostanie **anulowane** i **usunięte** .

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Tworzenie zasad klucza zawartości

Klucz zawartości zapewnia bezpieczny dostęp do elementów zawartości. Należy utworzyć **zasady klucza zawartości** , które umożliwiają skonfigurowanie sposobu dostarczania klucza zawartości do klientów końcowych. Klucz zawartości jest skojarzony z **lokalizatorem przesyłania strumieniowego**. Media Services zapewnia również usługę dostarczania kluczy, która dostarcza klucze szyfrowania do autoryzowanych użytkowników.

Gdy gracz prosi o przesłanie strumienia, Media Services używa określonego klucza do dynamicznego szyfrowania zawartości (w tym przypadku przy użyciu szyfrowania AES). Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik został autoryzowany do otrzymywania klucza, usługa ocenia zasady kluczy zawartości, które wybrano dla klucza.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania i ustawieniu zasad kluczy zawartości następnym krokiem jest udostępnienie klientom w wyjściowym elemencie zawartości pliku wideo, który można odtwarzać. Film wideo jest dostępny w dwóch krokach:

1. Utwórz [lokalizator przesyłania strumieniowego](/rest/api/media/streaminglocators).
2. Utworzenie adresów URL przesyłania strumieniowego, których klienci mogą używać.

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API. Obowiązuje do momentu jego usunięcia, chyba że zostanie skonfigurowany opcjonalny czas rozpoczęcia i zakończenia.

Podczas tworzenia [lokalizatora przesyłania strumieniowego](/rest/api/media/streaminglocators)należy określić żądany **StreamingPolicyName**. W tym samouczku używamy jednego z PredefinedStreamingPolicies, który informuje, Azure Media Services jak opublikować zawartość do przesyłania strumieniowego. W tym przykładzie zastosowano szyfrowanie kopert AES (to szyfrowanie jest również znane jako szyfrowanie ClearKey, ponieważ klucz jest dostarczany do klienta odtwarzania za pośrednictwem protokołu HTTPS, a nie licencji DRM).

> [!IMPORTANT]
> W przypadku korzystania z niestandardowej [StreamingPolicy](/rest/api/media/streamingpolicies)należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i ponownie użyć ich dla lokalizatorów przesyłania strumieniowego za każdym razem, gdy potrzebne są te same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji elementu StreamingPolicy. Nie należy tworzyć nowych StreamingPolicy dla każdego lokalizatora przesyłania strumieniowego.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Pobieranie tokenu testowego

W tym samouczku określamy zasady klucza zawartości z ograniczenie dotyczącym tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Media Services obsługuje tokeny w formacie [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) i to, co konfigurujemy w przykładzie.

ContentKeyIdentifierClaim jest używana w **zasadach klucza zawartości**, co oznacza, że token przedstawiony dla usługi dostarczania kluczy musi mieć identyfikator klucza zawartości. W przykładzie nie podano klucza zawartości podczas tworzenia lokalizatora przesyłania strumieniowego, system utworzył losowo jeden dla nas. W celu wygenerowania tokenu testowego musimy uzyskać ContentKeyId do umieszczenia w ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Kompilowanie adresu URL przesyłania strumieniowego DASH

Teraz, gdy [lokalizator przesyłania strumieniowego](/rest/api/media/streaminglocators) został utworzony, możesz uzyskać adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, należy połączyć nazwę hosta [StreamingEndpoint](/rest/api/media/streamingendpoints) i ścieżkę **lokalizatora przesyłania strumieniowego** . W tym przykładzie jest używany *domyślny* **punkt końcowy przesyłania strumieniowego**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* **punkt końcowy przesyłania strumieniowego** będzie zatrzymany, więc należy wywołać metodę **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Ogólnie rzecz biorąc, należy wyczyścić wszystko z wyjątkiem obiektów, które są planowane do ponownego użycia (zwykle spowoduje to ponowne użycie przekształceń, lokalizatorów przesyłania strumieniowego itp.). Jeśli chcesz, aby Twoje konto było czyste po eksperymentie, Usuń zasoby, których nie planujesz ponownie używać. Na przykład poniższy kod usuwa zadanie, utworzone zasoby i zasady klucza zawartości:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ochrona przy użyciu technologii DRM](protect-with-drm.md)
