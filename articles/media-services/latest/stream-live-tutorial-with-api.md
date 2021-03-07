---
title: przesyłanie strumieniowe na żywo z Media Services v3: Azure Media Services Description: informacje na temat przesyłania strumieniowego na żywo za pomocą Azure Media Services v3.
usługi: Media-Services documentationcenter: "" Author: IngridAtMicrosoft Manager: femila Editor: ""

MS. Service: Media-Services MS. obciążeni: Media ms.tgt_pltfrm: na MS. devlang: na MS. temat: samouczek MS. Custom: "MVC, DevX-Track-CSharp" MS. Date: 06/13/2019 MS. Author: inhenkel

---

# <a name="tutorial-stream-live-with-media-services"></a>Samouczek: przesyłanie strumieniowe na żywo za pomocą Media Services

> [!NOTE]
> Mimo że w samouczku są używane przykłady [zestawu .NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) , ogólne kroki są takie same dla [interfejsów API REST](/rest/api/media/liveevents), interfejsu [wiersza polecenia](/cli/azure/ams/live-event)lub innych obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks). 

W usłudze Azure Media Services [wydarzenia na żywo](/rest/api/media/liveevents) są odpowiedzialne za przetwarzanie zawartości transmisji strumieniowej na żywo. Wydarzenie na żywo udostępnia wejściowy punkt końcowy (adres URL pozyskiwania), który należy przekazać do kodera na żywo. Wydarzenie na żywo odbiera strumienie wejściowe na żywo z kodera na żywo i udostępnia je do przesyłania strumieniowego za pośrednictwem co najmniej jednego [punktu końcowego przesyłania strumieniowego](/rest/api/media/streamingendpoints). Wydarzenia na żywo oferują również punkt końcowy podglądu (adres URL podglądu), dzięki któremu można wyświetlać podgląd i weryfikować strumień przed dalszym przetwarzaniem i dostarczaniem. W tym samouczku przedstawiono sposób użycia platformy .NET Core do tworzenia **kanału do przekazywania zawartości** wydarzeń na żywo.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Pobierz przykładową aplikację opisaną w temacie.
> * Przejrzyj kod, który wykonuje transmisję strumieniową na żywo.
> * Obejrzyj zdarzenie, [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) pod adresem [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) .
> * Wyczyść zasoby.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są niezbędne następujące elementy:

- Zainstalowanie narzędzia Visual Studio Code lub Visual Studio.
- [Utwórz konto Media Services](./create-account-howto.md).<br/>Upewnij się, że kopiujesz szczegóły dostępu do interfejsu API w formacie JSON lub przechowuj wartości, które są konieczne, aby połączyć się z kontem Media Services w formacie ENV użytym w tym przykładzie.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Musisz użyć ich do uzyskania dostępu do interfejsu API w tym przykładzie lub wprowadzić je do formatu pliku ENV. 
- Aparat lub urządzenie (na przykład laptop), które jest używane do emisji zdarzenia.
- Lokalny koder programowy, który koduje strumień aparatu i wysyła go do Media Services usługi przesyłania strumieniowego na żywo przy użyciu protokołu RTMP, znajduje się [w temacie zalecane lokalne kodery na żywo](recommended-on-premises-live-encoders.md). Strumień musi być w formacie **RTMP** lub **Smooth Streaming**.  
- Na potrzeby tego przykładu zaleca się rozpoczęcie od kodera programowego, takiego jak bezpłatne [otwarte oprogramowanie emisyjne obs Studio](https://obsproject.com/download) , aby ułatwić rozpoczęcie pracy. 

> [!TIP]
> Przed kontynuowaniem przejrzyj sekcję [Transmisja strumieniowa na żywo przy użyciu usługi Media Services v3](live-streaming-overview.md). 

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj następujące repozytorium centrum git zawierające przykład platformy .NET przesyłania strumieniowego na żywo do maszyny przy użyciu następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

Przykład transmisji strumieniowej na żywo znajduje się w folderze [Live](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live).

Otwórz [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/appsettings.json) w pobranym projekcie. Zastąp wartości poświadczeniami uzyskanymi w celu [uzyskania dostępu do interfejsów API](./access-api-howto.md).

Należy pamiętać, że można również użyć formatu pliku ENV w katalogu głównym projektu, aby ustawić zmienne środowiskowe tylko raz dla wszystkich projektów w repozytorium przykładów platformy .NET. Wystarczy skopiować przykładowy plik. env, podać informacje uzyskane na stronie dostępu do interfejsu API Media Services Azure Portal lub z poziomu interfejsu wiersza polecenia platformy Azure.  Zmień nazwę pliku Sample. env na "ENV", aby używać go we wszystkich projektach.
Plik. gitignore jest już skonfigurowany tak, aby uniknąć publikowania zawartości tego pliku w repozytorium z rozwidleniem. 

> [!IMPORTANT]
> Ten przykład używa unikatowego sufiksu dla każdego zasobu. Jeśli anulujesz debugowanie lub wygaśniesz aplikację bez jej uruchamiania, będziesz mieć wiele wydarzeń na żywo na koncie. <br/>Pamiętaj, aby zatrzymać uruchomione wydarzenia na żywo. W przeciwnym razie zostanie **naliczona** stawka.

## <a name="examine-the-code-that-performs-live-streaming"></a>Analizowanie kodu, który przeprowadza transmisję strumieniową na żywo

Ta sekcja bada funkcje zdefiniowane w pliku [program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) projektu *LiveEventWithDVR* .

Przykład tworzy unikatowy sufiks dla każdego zasobu, co sprawia, że nie występują kolizje nazw, jeśli przykład zostanie uruchomiony wielokrotnie bez czyszczenia.


### <a name="start-using-media-services-apis-with-net-sdk"></a>Rozpoczynanie korzystania z interfejsów API usługi Media Services przy użyciu zestawu .NET SDK

Aby rozpocząć korzystanie z interfejsów API usługi Media Services na platformie .NET, należy utworzyć obiekt **AzureMediaServicesClient**. Aby utworzyć obiekt, należy podać poświadczenia wymagane do nawiązania połączenia z platformą Azure przez klienta przy użyciu usługi Azure AD. W kodzie sklonowanym na początku artykułu funkcja **GetCredentialsAsync** tworzy obiekt serviceclientcredentials na podstawie poświadczeń podanych w lokalnym pliku konfiguracji (appsettings.json) lub za pośrednictwem pliku zmiennych środowiskowych ENV znajdującego się w katalogu głównym repozytorium.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Utwórz wydarzenie na żywo

W tej sekcji przedstawiono sposób tworzenia **przekazywanego** typu wydarzenia na żywo (o wartości parametru LiveEventEncodingType ustawionej na None). Aby uzyskać więcej informacji na temat innych dostępnych typów wydarzeń na żywo, zobacz [typy zdarzeń na żywo](live-events-outputs-concept.md#live-event-types). Poza przekazywaniem można używać na żywo zdarzenia Live Encoding dla kodowania w chmurze 720 lub 1080P z adaptacyjną szybkością transmisji bitów. 
 
Niektóre elementy, które można określić podczas tworzenia zdarzenia na żywo są następujące:

* Protokół pozyskiwania dla zdarzenia na żywo (obecnie są obsługiwane protokoły RTMP i Smooth Streaming).<br/>Nie można zmienić opcji protokołu, gdy działa zdarzenie na żywo lub skojarzone z nim wyjście na żywo. Jeśli potrzebujesz różnych protokołów, Utwórz oddzielne wydarzenie na żywo dla każdego protokołu przesyłania strumieniowego.  
* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Można zdefiniować adresy IP, które mogą pozyskiwać pliki wideo w tym wydarzeniu na żywo. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).<br/>Jeśli nie określono adresów IP i nie ma definicji reguły, adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.<br/>Adresy IP muszą znajdować się w jednym z następujących formatów: adres IpV4 z czterema numerami lub zakresem adresów CIDR.
* Podczas tworzenia zdarzenia możesz określić, aby uruchomić je ponownie. <br/>Jeśli automatyczne uruchamianie zostanie ustawione na wartość true, wydarzenie na żywo rozpocznie się po utworzeniu. Oznacza to, że rozliczanie zaczyna się zaraz po rozpoczęciu uruchamiania zdarzenia na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia. Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
Dostępne są również tryby gotowości, które umożliwiają uruchomienie zdarzenia na żywo w stanie "przydzielony" niższy koszt, co przyspiesza przejście do stanu "uruchomiona". Jest to przydatne w przypadku sytuacji, takich jak hotpools, które umożliwiają szybkie przekazywanie kanałów do usługi Streams.
* Aby adres URL pozyskiwania był predykcyjny i łatwiejszy w obsłudze w ramach sprzętowego kodera na żywo, ustaw właściwość "useStaticHostname" na wartość true. Aby uzyskać szczegółowe informacje, zobacz [adresy URL](live-events-outputs-concept.md#live-event-ingest-urls)pozyskiwania zdarzeń na żywo.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Pobieranie adresów URL pozyskiwania

Po utworzeniu zdarzenia na żywo możesz uzyskać adresy URL pozyskiwania do kodera na żywo. Koder używa tych adresów URL do wprowadzenia strumienia na żywo.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Pobieranie adresu URL podglądu

Użyj funkcji previewEndpoint do podglądu i sprawdź, czy dane wejściowe z kodera są faktycznie odbierane.

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że wideo przepływa do adresu URL wersji zapoznawczej.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Tworzenie wydarzeń i danych wyjściowych na żywo oraz zarządzanie nimi

Po przesłaniu strumienia do wydarzenia na żywo można rozpocząć wydarzenie przesyłania strumieniowego poprzez utworzenie zasobu, danych wyjściowych na żywo i lokalizatora przesyłania strumieniowego. Spowoduje to archiwizację strumienia i udostępni go użytkownikom za pośrednictwem punktu końcowego przesyłania strumieniowego.

Podczas uczenia tych koncepcji najlepiej traktować obiekt "Asset" jako taśmę, którą można wstawić do nagrywarki wideo w starym dniu. "Wyjście na żywo" jest maszyną rejestratora taśm. "Wydarzenie na żywo" to tylko sygnał wideo z tyłu maszyny.

Najpierw tworzymy sygnał, tworząc "wydarzenie na żywo".  Sygnał nie jest przepływa do momentu rozpoczęcia tego zdarzenia na żywo i połączenia kodera z danymi wejściowymi.

Taśmę można utworzyć w dowolnym momencie. Jest to tylko pusty "zasób", który można odstawić do obiektu danych wyjściowych na żywo, rejestratora taśm w tym trybie analogowym.

Rejestrator taśm można utworzyć w dowolnym momencie. Oznacza to, że można utworzyć dane wyjściowe na żywo przed rozpoczęciem przepływu sygnałów lub po. Jeśli chcesz przyspieszyć pracę, czasami warto ją utworzyć przed rozpoczęciem przepływu sygnałów.

Aby zatrzymać Rejestrator taśm, należy wywołać polecenie Delete w LiveOutput. Nie spowoduje to usunięcia zawartości na taśmie "zasób".  Element zawartości jest zawsze przechowywany z archiwalną zawartością wideo, dopóki nie zostanie jawnie wywołana wartość usunięcia w samym elemencie zawartości.

W następnej sekcji opisano tworzenie zasobu ("Taśma") i na żywo ("Rejestrator taśm").

#### <a name="create-an-asset"></a>Tworzenie zasobu

Utwórz zasób przeznaczony do użycia przez dane wyjściowe na żywo. W analogiczny sposób będzie to nasza taśma, na której rejestrujemy sygnał wideo na żywo. Osoby przeglądające będą mogły wyświetlać zawartość na żywo lub na żądanie z tej taśmy wirtualnej.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Tworzenie danych wyjściowych na żywo

Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania. Jest to "Rejestrator taśm" dla zdarzenia. Po usunięciu danych wyjściowych na żywo nie jest usuwany bazowy zasób ani zawartość elementu zawartości. Należy je traktować jako wysunięcie taśmy. Element zawartości z nagraniem będzie miał wartość niedawniej tak długo, jak chcesz, a gdy zostanie wysunięty (oznacza to, że po usunięciu danych wyjściowych na żywo) będzie on natychmiast dostępny do wyświetlania na żądanie. 

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

> [!NOTE]
> Po utworzeniu konta Media Services zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego w stanie **zatrzymanym** . Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z [dynamicznego tworzenia pakietów](dynamic-packaging-overview.md) i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi być w stanie **uruchomienia** .

Po opublikowaniu elementu zawartości przy użyciu lokalizatora przesyłania strumieniowego, zdarzenie na żywo (do okna DVR) będzie nadal widoczne do momentu wygaśnięcia lub usunięcia lokalizatora przesyłania strumieniowego, w zależności od tego, co nastąpi wcześniej. Jest to sposób, aby wirtualne nagrywanie na taśmie było dostępne dla odbiorców wyświetlanych na żywo i na żądanie. Ten sam adres URL może służyć do oglądania zdarzenia na żywo, DVR z okna lub zasobu na żądanie po zakończeniu nagrywania (po usunięciu danych wyjściowych na żywo).

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Jeśli zakończysz przesyłanie strumieniowe zdarzeń i chcesz wyczyścić zasoby, których zainicjowano wcześniej, postępuj zgodnie z następującą procedurą:

* Zatrzymaj wypychanie strumienia z kodera.
* Zatrzymaj wydarzenie na żywo. Po zatrzymaniu wydarzenia na żywo nie zostaną naliczone żadne opłaty. W razie potrzeby ponownego uruchomienia kanał będzie miał ten sam adres URL pozyskiwania, więc nie trzeba będzie ponownie konfigurować kodera.
* Można zatrzymać punkt końcowy przesyłania strumieniowego, chyba że chcesz nadal udostępniać archiwum zdarzenia na żywo w formie przesyłania strumieniowego na żądanie. Jeśli wydarzenie na żywo jest w stanie zatrzymania, nie spowoduje to ponoszenia żadnych opłat.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Oglądanie wydarzenia

Aby obserwować wydarzenie, skopiuj adres URL przesyłania strumieniowego, który został uzyskany podczas uruchamiania kodu opisanego w temacie Tworzenie lokalizatora przesyłania strumieniowego. Możesz użyć wybranego odtwarzacza multimedialnego. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) jest dostępny do testowania strumienia pod adresem https://ampdemo.azureedge.net .

Po zatrzymaniu wydarzenia na żywo wydarzenie jest automatycznie konwertowane na zawartość na żądanie. Nawet po zatrzymaniu i usunięciu zdarzenia użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość jako wideo na żądanie tak długo, jak nie usuniesz elementu zawartości. Nie można usunąć elementu zawartości, jeśli jest on używany przez zdarzenie. najpierw należy usunąć zdarzenie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie są już potrzebne żadne zasoby w grupie zasobów, w tym konto usługi Media Services i konta magazynu utworzone w ramach tego samouczka, usuń grupę zasobów utworzoną wcześniej.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Pozostawienie uruchomionego wydarzenia na żywo wiąże się z naliczaniem opłat. Należy pamiętać, że jeśli projekt/program ulegnie awarii lub zostanie zamknięty z jakiegokolwiek powodu, wydarzenie na żywo może pozostać uruchomione w stanie naliczania opłat.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

[Przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md)
 
