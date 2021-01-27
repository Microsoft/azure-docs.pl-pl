---
title: Azure Media Services szyfrowanie DRM i usługa dostarczania licencji: Azure Media Services opis: informacje na temat używania dynamicznej szyfrowania DRM i usługi dostarczania licencji w celu dostarczania strumieni zaszyfrowanych za pomocą licencji firmy Microsoft PlayReady, Google Widevine lub Apple FairPlay.
usługi: Media-Services documentationcenter: "" Author: IngridAtMicrosoft Manager: femila Editor: ""

MS. Service: Media-Services MS. obciążeń: Media ms.tgt_pltfrm: na MS. devlang: na MS. temat: samouczek MS. Date: 08/31/2020 MS. Author: inhenkel MS. Custom: seodec18

---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Samouczek: używanie dynamicznego szyfrowania DRM i usługi dostarczania licencji

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Mimo że w tym samouczku są używane przykłady [zestawu SDK dla platformy .NET](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) , ogólne kroki są takie same dla [interfejsu API REST](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event?view=azure-cli-latest)lub innych obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

Usługa Azure Media Services umożliwia dostarczanie strumieni zaszyfrowanych za pomocą licencji Microsoft PlayReady, Google Widevine i Apple FairPlay. Szczegółowe wyjaśnienie zawiera temat [Ochrona zawartości przy użyciu szyfrowania dynamicznego](content-protection-overview.md).

Media Services udostępnia również usługi do dostarczania licencji platformy PlayReady, Widevine i FairPlay. Gdy użytkownik zażąda zawartości chronionej za pomocą technologii DRM, aplikacja odtwarzacza żąda licencji z usługi licencjonowania Media Services. Jeśli aplikacja odtwarzacza zostanie autoryzowana, usługa licencjonowania Media Services wystawia licencję dla odtwarzacza. Licencja zawiera klucz odszyfrowujący, który może zostać użyty przez odtwarzacz klienta do odszyfrowania i strumieniowego przesyłania zawartości.

Ten artykuł jest oparty na przykładzie [szyfrowania przy użyciu technologii DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

Opisany w tym artykule przykład generuje następujący wynik:

![Wskaźnik AMS z chronionym przez DRM wideo w Azure Media Player](./media/protect-with-drm/ams_player.png)

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Utwórz transformację kodowania.
> * Ustawianie klucza podpisywania używanego na potrzeby weryfikacji tokenu.
> * Ustaw wymagania dotyczące zasad klucza zawartości.
> * Utwórz StreamingLocator z określonymi zasadami przesyłania strumieniowego.
> * Utwórz adres URL służący do odtwarzania pliku.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są niezbędne następujące elementy:

* Zapoznanie się z artykułem [Content protection overview (Omówienie ochrony zawartości)](content-protection-overview.md).
* Zapoznaj się z tematem [Projektowanie systemu ochrony zawartości z użyciem technologii wielowątkowości z kontrolą dostępu](design-multi-drm-system-with-access-control.md).
* Zainstalowanie narzędzia Visual Studio Code lub Visual Studio.
* Utworzenie nowego konta usługi Azure Media Services zgodnie z opisem w [tym przewodniku Szybki start](./create-account-howto.md).
* Uzyskanie poświadczeń potrzebnych do korzystania z interfejsów API usługi Media Services przez wykonanie kroków [uzyskiwania dostęp do interfejsów API](./access-api-howto.md).
* Ustaw odpowiednie wartości w pliku konfiguracyjnym aplikacji (appsettings.json).

## <a name="download-code"></a>Pobieranie kodu

Sklonuj repozytorium GitHub zawierające pełny przykład dla platformy .NET opisany w tym artykule na swoją maszynę za pomocą następującego polecenia:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Przykład szyfrowania za pomocą technologii DRM znajduje się w folderze [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Przykład tworzy unikatowe zasoby przy każdym uruchomieniu aplikacji. Zazwyczaj należy ponownie używać istniejących zasobów, takich jak transformacje i zasady (Jeśli istniejący zasób ma wymagane konfiguracje).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API Media Services z platformą .NET, Utwórz obiekt **AzureMediaServicesClient** . Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku tego artykułu funkcja **GetCredentialsAsync** tworzy obiekt ServiceClientCredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Tworzenie zasobu wyjściowego  

Wyjściowy element [zawartości](assets-concept.md) przechowuje wynik zadania kodowania.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Pobieranie lub tworzenie obiektu Transform kodowania

Podczas tworzenia nowego wystąpienia obiektu [Transform](transforms-jobs-concept.md) należy określić, jakie dane wyjściowe ma ono tworzyć. Wymagany parametr jest `transformOutput` obiektem, jak pokazano w poniższym kodzie. Każdy obiekt TransformOutput zawiera element **Preset**. Element Preset zawiera szczegółowe instrukcje operacji przetwarzania wideo i/lub dźwięku używanych do wygenerowania wymaganego obiektu TransformOutput. Przykład opisany w tym artykule używa wbudowanego elementu Preset o nazwie **AdaptiveStreaming**. Ustawienie wstępne koduje wejściowe wideo do automatycznie generowanej (par rozdzielczości szybkości transmisji bitów) w oparciu o rozdzielczość wejścia i szybkość transmisji bitów, a następnie tworzy pliki MP4 ISO z wideo H. 264 i AAC audio odpowiadające każdej parze rozdzielczości szybkości transmisji bitów. 

Przed utworzeniem nowego obiektu **Transform** należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie.  W przypadku usługi Media Services 3 metody **Get** wywołane dla obiektów zwracają **wartość null**, jeśli obiekt nie istnieje (sprawdzana jest nazwa bez uwzględniania wielkości liter).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Przesyłanie zadania

Jak wspomniano powyżej, obiekt **Transform** jest przepisem, a obiekt [Job](transforms-jobs-concept.md) to rzeczywiste żądanie skierowane do usługi Media Services i mające na celu zastosowanie obiektu **Transform** do określonej wejściowej zawartości wideo lub dźwiękowej. **Zadanie** określa informacje na przykład o lokalizacji wejściowego wideo i lokalizacji danych wyjściowych.

W tym samouczku utworzysz dane wejściowe zadania na podstawie pliku, który jest pobierany bezpośrednio ze [źródłowego adresu URL https](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Oczekiwanie na zakończenie zadania

Ukończenie zadania trwa jakiś czas. Gdy tak jest, chcesz otrzymywać powiadomienia. Poniższy przykład kodu pokazuje, jak sondować usługę pod kątem stanu **zadania**. Sondowanie nie jest zalecanym najlepszym rozwiązaniem w przypadku aplikacji produkcyjnych ze względu na potencjalne opóźnienia. Jeśli sondowanie będzie nadużywane w ramach konta, może zostać ograniczone. Deweloperzy zamiast niego powinni używać usługi Event Grid. Zobacz [Kierowanie zdarzeń do niestandardowego internetowego punktu końcowego](job-state-events-cli-how-to.md).

**Zadanie** zwykle przechodzi przez następujące stany: **Scheduled (Zaplanowane)**, **Queued (W kolejce)**, **Processing (Przetwarzane)**, **Finished (Zakończone)** (stan końcowy). Jeśli w zadaniu wystąpi błąd, zostanie wyświetlony stan **błędu** . Jeśli zadanie jest w trakcie jego anulowania, po zakończeniu zostanie **anulowane** i **usunięte** .

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Tworzenie zasad klucza zawartości

Klucz zawartości zapewnia bezpieczny dostęp do elementów zawartości. Należy utworzyć [zasady klucza zawartości](content-key-policy-concept.md) podczas szyfrowania zawartości przy użyciu funkcji DRM. Zasady umożliwiają skonfigurowanie sposobu dostarczania klucza zawartości do klientów końcowych. Klucz zawartości jest skojarzony z lokalizatorem przesyłania strumieniowego. Usługa Media Services udostępnia również usługę dostarczania kluczy, która dostarcza klucze szyfrowania i licencje do autoryzowanych użytkowników.

Należy ustawić wymagania (ograniczenia) dotyczące **zasad kluczy zawartości** , które muszą zostać spełnione, aby dostarczyć klucze z określoną konfiguracją. W tym przykładzie ustawimy następujące konfiguracje i wymagania:

* Konfigurowanie

    Licencje [PlayReady](playready-license-template-overview.md) i [Widevine](widevine-license-template-overview.md) są skonfigurowane tak, aby można było dostarczać je przy użyciu usługi dostarczania licencji w usłudze Media Services. Mimo że ta Przykładowa aplikacja nie konfiguruje licencji [FairPlay](fairplay-license-overview.md) , zawiera metodę, której można użyć do skonfigurowania FairPlay. Konfigurację FairPlay można dodać jako inną opcję.

* Ograniczenie

    Aplikacja ustawia ograniczenie typu tokenu token sieci Web JSON (JWT) dla zasad.

Gdy odtwarzacz żąda strumienia, usługa Media Services używa wybranego klucza do dynamicznego szyfrowania zawartości. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik został autoryzowany do otrzymywania klucza, usługa ocenia zasady kluczy zawartości, które wybrano dla klucza.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

Po zakończeniu kodowania i ustawieniu zasad kluczy zawartości następnym krokiem jest udostępnienie klientom w wyjściowym elemencie zawartości pliku wideo, który można odtwarzać. Film wideo jest dostępny w dwóch krokach:

1. Utwórz [lokalizator przesyłania strumieniowego](streaming-locators-concept.md).
2. Utworzenie adresów URL przesyłania strumieniowego, których klienci mogą używać.

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API. Obowiązuje do momentu jego usunięcia, chyba że zostanie skonfigurowany opcjonalny czas rozpoczęcia i zakończenia.

Podczas tworzenia **lokalizatora przesyłania strumieniowego** należy określić odpowiednią wartość `StreamingPolicyName` . W tym samouczku korzystamy z jednej ze wstępnie zdefiniowanych zasad przesyłania strumieniowego, która informuje Azure Media Services jak opublikować zawartość do przesyłania strumieniowego. W tym przykładzie ustawiamy element StreamingLocator.StreamingPolicyName na zasady „Predefined_MultiDrmCencStreaming”. Są stosowane szyfrowanie PlayReady i Widevine, a klucz jest dostarczany do klienta odtwarzania na podstawie skonfigurowanych licencji DRM. Jeśli chcesz także zaszyfrować strumień przy użyciu metody CBCS (FairPlay), użyj zasad „Predefined_MultiDrmStreaming”.

> [!IMPORTANT]
> W przypadku korzystania z niestandardowych [zasad przesyłania strumieniowego](streaming-policy-concept.md) należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i używać ich ponownie dla obiektów StreamingLocator zawsze, gdy są potrzebne takie same opcje szyfrowania i protokoły. Konto usługi Media Service jest objęte limitem przydziału dotyczącym liczby pozycji elementu StreamingPolicy. Nie należy tworzyć nowych StreamingPolicy dla każdego StreamingLocatoru.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Pobieranie tokenu testowego

W tym samouczku określamy zasady klucza zawartości z ograniczenie dotyczącym tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Media Services obsługuje tokeny w formatach [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) i to, co konfigurujemy w przykładzie.

Element ContentKeyIdentifierClaim jest używany w zasadach ContentKeyPolicy, co oznacza, że token przedstawiany usłudze dostarczania kluczy musi zawierać identyfikator ContentKey. W przykładzie nie podasz klucza zawartości podczas tworzenia lokalizatora przesyłania strumieniowego, system tworzy losowo jeden dla nas. W celu wygenerowania tokenu testowego musimy uzyskać ContentKeyId do umieszczenia w ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Tworzenie adresu URL przesyłania strumieniowego

Teraz, po utworzeniu obiektu [StreamingLocator](/rest/api/media/streaminglocators), możesz pobrać adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, należy połączyć nazwę hosta [StreamingEndpoint](/rest/api/media/streamingendpoints) i ścieżkę **lokalizatora przesyłania strumieniowego** . W tym przykładzie jest używany *domyślny* **punkt końcowy przesyłania strumieniowego**. Po pierwszym utworzeniu konta usługi Media Service ten *domyślny* **punkt końcowy przesyłania strumieniowego** będzie zatrzymany, więc należy wywołać metodę **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Po uruchomieniu aplikacji zobaczysz następujący ekran:

![Ochrona przy użyciu technologii DRM](./media/protect-with-drm/playready_encrypted_url.png)

Można otworzyć przeglądarkę i wkleić wynikowy adres URL, aby uruchomić stronę pokazu usługi Azure Media Player przy użyciu już wypełnionego adresu URL i tokenu.

## <a name="clean-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Ogólnie rzecz biorąc, należy oczyścić wszystko z wyjątkiem obiektów, które są planowane do ponownego użycia (zazwyczaj spowoduje to ponowne użycie przekształceń, StreamingLocators itd.). Jeśli chcesz, aby Twoje konto było czyste po eksperymentie, Usuń zasoby, których nie planujesz ponownie używać. Na przykład poniższy kod usuwa zadanie, utworzone zasoby i zasady klucza zawartości:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

Wyewidencjonuj

> [!div class="nextstepaction"]
> [Ochrona za pomocą algorytmu AES-128](protect-with-aes128.md)
