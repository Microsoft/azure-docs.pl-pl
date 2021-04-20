---
title: Transmisja strumieniowa na żywo Media Services w wersji 3 Node.js
titleSuffix: Azure Media Services
description: Dowiedz się, jak przesyłać strumieniowo na żywo przy użyciu Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 749d2fc845f036a2802c80c161b3fc8c171c2555
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730540"
---
# <a name="tutorial-stream-live-with-media-services-using-nodejs-and-typescript"></a>Samouczek: przesyłanie strumieniowe na żywo za Media Services przy użyciu języka Node.js i TypeScript

> [!NOTE]
> Mimo że w tym samouczku Node.js przykłady, ogólne kroki [](/cli/azure/ams/live-event)są takie same dla interfejsu [API REST,](/rest/api/media/liveevents)interfejsu wiersza polecenia lub innych [obsługiwanych zestawów SDK.](media-services-apis-overview.md#sdks) 

W usłudze Azure Media Services [wydarzenia na żywo](/rest/api/media/liveevents) są odpowiedzialne za przetwarzanie zawartości transmisji strumieniowej na żywo. Wydarzenie na żywo udostępnia wejściowy punkt końcowy (adres URL pozyskiwania), który należy przekazać do kodera na żywo. Wydarzenie na żywo odbiera strumienie wejściowe na żywo z kodera na żywo i udostępnia je do przesyłania strumieniowego za pośrednictwem co najmniej jednego [punktu końcowego przesyłania strumieniowego](/rest/api/media/streamingendpoints). Wydarzenia na żywo oferują również punkt końcowy podglądu (adres URL podglądu), dzięki któremu można wyświetlać podgląd i weryfikować strumień przed dalszym przetwarzaniem i dostarczaniem. W tym samouczku pokazano, jak  za pomocą Node.js utworzyć typ przebiegu wydarzenia na żywo i emitować do niego strumień na żywo przy użyciu [programu OBS Studio.](https://obsproject.com/download)

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Pobierz przykładowy kod opisany w temacie.
> * Sprawdź kod, który konfiguruje i wykonuje transmisję strumieniową na żywo.
> * Obejrzyj wydarzenie, [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) na [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) stronie .
> * Wyczyść zasoby.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są niezbędne następujące elementy:

- Instalowanie [Node.js](https://nodejs.org/en/download/)
- Instalowanie [języka TypeScript](https://www.typescriptlang.org/)
- [Utwórz Media Services konto.](./create-account-howto.md)<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Będą one potrzebne do uzyskania dostępu do interfejsu API i skonfigurowania pliku zmiennych środowiskowych.
- Aby dowiedzieć się, jak korzystać z zestawu SDK klienta [Node.js, ](./configure-connect-nodejs-howto.md) należy najpierw Node.jsskonfigurować i nawiązać połączenie za pomocą Node.js klienta
- Zainstalowanie narzędzia Visual Studio Code lub Visual Studio.
- [Skonfiguruj środowisko Visual Studio Code do](https://code.visualstudio.com/Docs/languages/typescript) obsługi języka TypeScript.

## <a name="additional-settings-for-live-streaming-software"></a>Dodatkowe ustawienia oprogramowania do transmisji strumieniowej na żywo

- Aparat lub urządzenie (takie jak laptop), które jest używane do emisji wydarzenia.
- Koder oprogramowania lokalnego, który koduje strumień aparatu i wysyła go do usługi transmisji strumieniowej na żywo Media Services przy użyciu protokołu RTMP, zobacz zalecane lokalne kodery [na żywo.](encode-recommended-on-premises-live-encoders.md) Strumień musi być w formacie **RTMP** lub **Smooth Streaming**.  
- W tym przykładzie zaleca się rozpoczęcie od kodera oprogramowania, takiego jak bezpłatny program [Open Broadcast Software OBS Studio,](https://obsproject.com/download) aby ułatwić rozpoczęcie pracy.

W tym przykładzie przyjęto założenie, że program OBS Studio będzie używać do emisji rtmp do punktu końcowego pozysowania. Najpierw zainstaluj program OBS Studio.
Użyj następujących ustawień kodowania w programie OBS Studio:

- Koder: NVIDIA NVENC (jeśli jest dostępny) lub x264
- Kontrola szybkości: CBR
- Wartość transmisji bitów: 2500 Kb/s (lub coś rozsądnego dla twojego laptopa)
- Interwał ramki kluczowej: 2 s lub 1 s w przypadku małych opóźnień  
- Ustawienie wstępne: Niska jakość lub wydajność (NVENC) lub "bardzo szybko" przy użyciu x264
- Profil: wysoki
- Procesor GPU: 0 (automatycznie)
- Maksymalna liczba ramek B: 2

> [!TIP]
> Przed kontynuowaniem przejrzyj sekcję [Transmisja strumieniowa na żywo przy użyciu usługi Media Services v3](stream-live-streaming-concept.md).

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj następujące repozytorium Git Hub zawierające przykładową transmisję strumieniową Node.js na swoją maszynę przy użyciu następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Przykład transmisji strumieniowej na żywo znajduje się w folderze [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live).

W folderze [AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) skopiuj plik o nazwie "sample.env" do nowego pliku o nazwie ".env", aby zapisać ustawienia zmiennych środowiskowych zebrane w artykule Access Azure Media Services API with the Azure CLI (Uzyskiwanie dostępu do interfejsu API programu Azure Media Services za pomocą interfejsu wiersza polecenia platformy [Azure).](./access-api-howto.md)
Upewnij się, że plik zawiera kropkę (.) przed rozszerzeniem env, aby poprawnie współpracować z przykładowym kodem.

Plik [env zawiera](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env) klucz i klucz tajny aplikacji usługi AAD oraz informacje o nazwie konta i subskrypcji wymagane do uwierzytelnienia dostępu zestawu SDK do Media Services konta. Plik .gitignore jest już skonfigurowany tak, aby uniemożliwić opublikowanie tego pliku w repozytorium zdjętymi. Nie zezwalaj na wyciek tych poświadczeń, ponieważ są one ważnymi wpisami tajnymi dla Twojego konta.

> [!IMPORTANT]
> W tym przykładzie dla każdego zasobu jest używany unikatowy sufiks. Jeśli anulujesz debugowanie lub przerywasz działanie aplikacji bez jej uruchamiania, na Twoim koncie zostanie uruchomionych wiele wydarzeń na żywo. <br/>Pamiętaj, aby zatrzymać uruchomione wydarzenia na żywo. W przeciwnym razie będą naliczane **faktury.** Uruchom program aż do ukończenia, aby automatycznie wyczyścić zasoby. Jeśli program ulega awarii lub nieumyślnie zatrzymasz debuger i przerwiesz wykonywanie programu, sprawdź dokładnie portal, aby upewnić się, że nie zostały pozostawione żadne wydarzenia na żywo w stanach Uruchomiony lub Stan autonomiczny, które spowodowałyby niechciane opłaty rozliczeniowe.

## <a name="examine-the-typescript-code-for-live-streaming"></a>Badanie kodu TypeScript dla transmisji strumieniowej na żywo

W tej sekcji przeanalizowano funkcje zdefiniowane w pliku [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) projektu *Live.*

Przykład tworzy unikatowy sufiks dla każdego zasobu, dzięki czemu nie ma kolizji nazw w przypadku wielokrotnego uruchomienia przykładu bez czyszczenia.

### <a name="start-using-media-services-sdk-for-nodejs-with-typescript"></a>Rozpoczynanie korzystania z Media Services SDK dla Node.js typeScript

Aby rozpocząć korzystanie Media Services API z Node.js, należy najpierw dodać moduł SDK przy [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) użyciu menedżera pakietów npm

```bash
npm install @azure/arm-mediaservices
```

W package.jsjest to już skonfigurowane, więc wystarczy uruchomić instalację *npm* w celu załadowania modułów i zależności.

1. Otwórz wiersz **polecenia** i przejdź do katalogu przykładu.
1. Zmień katalog na folder AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Zainstaluj pakiety używane w pliku *packages.jspliku* .

    ```bash
    npm install 
    ```

1. Uruchom Visual Studio Code z *folderu AMSv3Samples.* (Jest to wymagane do uruchomienia z folderu, w którym znajduje się folder *vscode* *itsconfig.jsplików).*

    ```bash
    cd ..
    code .
    ```

Otwórz folder *Live* i otwórz plik *index.ts* w Visual Studio Code edytorze.
W pliku *index.ts* naciśnij klawisz F5, aby uruchomić debuger.

### <a name="create-the-media-services-client"></a>Tworzenie Media Services klienta

Poniższy fragment kodu przedstawia sposób tworzenia klienta Media Services w Node.js.
Zwróć uwagę, że w tym kodzie najpierw ustawiamy właściwość **longRunningOperationRetryTimeout** dla opcji AzureMediaServicesOptions na 2 sekundy, aby skrócić czas sondowania stanu długotrwałej operacji w punkcie końcowym usługi Azure Resource Management.  Ponieważ większość operacji na wydarzeniach na żywo będzie asynchroniczna i może zająć trochę czasu, należy skrócić ten interwał sondowania dla zestawu SDK z domyślnej wartości 30 sekund, aby skrócić czas ukończenia najważniejszych operacji, takich jak tworzenie wydarzeń na żywo, uruchamianie i zatrzymywanie, które są wywołaniami asynchronicznmi. Dwie sekundy to zalecana wartość w przypadku większości scenariuszy przypadków użycia.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Utwórz wydarzenie na żywo

W tej sekcji przedstawiono sposób tworzenia **przekazywanego** typu wydarzenia na żywo (o wartości parametru LiveEventEncodingType ustawionej na None). Aby uzyskać więcej informacji o innych dostępnych typach wydarzeń na żywo, zobacz [Typy wydarzeń na żywo.](live-event-outputs-concept.md#live-event-types) Oprócz przekazania można użyć wydarzenia na żywo transkodowania na żywo w celu kodowania w chmurze z adaptacyjną szybkością transmisji bitów 720P lub 1080P.
 
Niektóre rzeczy, które można określić podczas tworzenia wydarzenia na żywo, to:

* Protokół pozyscyjania dla wydarzenia na żywo (obecnie obsługiwane są protokoły RTMP(S) i Smooth Streaming uwierzytelniania).<br/>Nie można zmienić opcji protokołu, gdy wydarzenie na żywo lub skojarzone z nim dane wyjściowe na żywo są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobne wydarzenie na żywo dla każdego protokołu przesyłania strumieniowego.  
* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Można zdefiniować adresy IP, które mogą pozyskiwać pliki wideo w tym wydarzeniu na żywo. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).<br/>Jeśli nie określono żadnych adresów IP i nie ma definicji reguły, żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.<br/>Adresy IP muszą mieć jeden z następujących formatów: adres IpV4 z czterema cyframi lub zakres adresów CIDR.
* Podczas tworzenia zdarzenia możesz określić, aby było ono automatycznie rozpoczynane. <br/>Jeśli automatyczne uruchamianie zostanie ustawione na wartość true, wydarzenie na żywo rozpocznie się po utworzeniu. Oznacza to, że rozliczanie rozpoczyna się natychmiast po uruchomieniu wydarzenia na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia. Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing-concept.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
Dostępne są również tryby rezerwy w celu rozpoczęcia wydarzenia na żywo w niższym stanie alokacji, co przyspiesza przejście do stanu "Uruchomione". Jest to przydatne w sytuacjach, takich jak pule gorące, które muszą szybko rozsyłać kanały do streamerów.
* Aby adres URL do pozysków był predykcyjny i łatwiejszy w obsłudze w sprzętowym koderze na żywo, ustaw właściwość "useStaticHostname" na wartość true, a także użyj niestandardowego unikatowego identyfikatora GUID w parametrze "accessToken". Aby uzyskać szczegółowe informacje, zobacz [Adresy URL pozysłania](live-event-outputs-concept.md#live-event-ingest-urls)wydarzeń na żywo.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>Tworzenie zasobu w celu rekordu i archiwizacji wydarzenia na żywo

W tym bloku kodu utworzysz pusty element zawartości do użycia jako "taśma" do nagrywania archiwum wydarzeń na żywo.
Poznając te pojęcia, najlepiej jest myśleć o obiekcie "Asset" jak o taśmie, która była wstania do rejestratora taśm w starych czasach. "Dane wyjściowe na żywo" to rejestrator taśm. "Wydarzenie na żywo" to po prostu sygnał wideo, który trafia z tyłu maszyny.

Należy pamiętać, że zasób lub "taśma" można utworzyć w dowolnym momencie. Jest to po prostu pusty element zawartości, który należy podać do obiektu Live Output, czyli rejestratora taśm w tej analogii.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>Tworzenie danych wyjściowych na żywo

W tej sekcji utworzymy dane wyjściowe na żywo, które użyją nazwy zasobu jako danych wejściowych, aby stwierdzić, gdzie ma być rejestrowane wydarzenie na żywo. Ponadto skonfigurujemy okno zmiany czasu (DVR), które będzie używane podczas rejestrowania.
Przykładowy kod pokazuje, jak skonfigurować 1-godzinny okres zmiany czasu. Umożliwi to klientom odtwarzanie z dowolnego miejsca w ciągu ostatniej godziny wydarzenia.  Ponadto tylko ostatnia godzina wydarzenia na żywo pozostanie w archiwum. W razie potrzeby można wydłużyć ten okres do 25 godzin.  Należy również zauważyć, że można kontrolować nazewnictwo manifestów danych wyjściowych, które po opublikowaniu zostały użyte w manifestach HLS i DASH w ścieżkach URL.

Dane wyjściowe na żywo, czyli "rejestrator taśm" w naszej analogii, można również utworzyć w dowolnym momencie. Oznacza to, że możesz utworzyć dane wyjściowe na żywo przed uruchomieniem przepływu sygnału lub po nim. Jeśli musisz przyspieszyć czynności, często warto je utworzyć przed uruchomieniem przepływu sygnału.

Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania.  Usunięcie danych wyjściowych na żywo nie powoduje usunięcia bazowego zasobu ani zawartości w zasobie. Pomyśl o tym jak o wysuwu taśmy. Zasób z nagraniem będzie trwał tak długo, jak chcesz, a po wysuniu (co oznacza, że po usunięciu danych wyjściowych na żywo) będzie on natychmiast dostępny do wyświetlania na żądanie.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>Pobieranie adresów URL pozyskiwania

Po utworzeniu wydarzenia na żywo możesz uzyskać adresy URL, które należy podać koderowi na żywo. Koder używa tych adresów URL do wprowadzania strumienia na żywo przy użyciu protokołu RTMP

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>Pobieranie adresu URL podglądu

Użyj funkcji previewEndpoint do podglądu i sprawdź, czy dane wejściowe z kodera są faktycznie odbierane.

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że wideo przepływa do adresu URL podglądu.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Tworzenie wydarzeń i danych wyjściowych na żywo oraz zarządzanie nimi

Gdy strumień przepływa do wydarzenia na żywo, możesz rozpocząć wydarzenie przesyłania strumieniowego, publikując lokalizator przesyłania strumieniowego dla odtwarzaczy klienckich. Dzięki temu będzie ona dostępna dla osób oglądających za pośrednictwem punktu końcowego przesyłania strumieniowego.

Najpierw utwórz sygnał, tworząc "Wydarzenie na żywo".  Sygnał nie będzie przepływać, dopóki nie rozpoczniesz wydarzenia na żywo i nie połączysz kodera z wejściami.

Aby zatrzymać "rejestrator taśm", należy wywołać usuwanie na liveoutput. Nie powoduje to usunięcia **zawartości** archiwum na taśmie "Zasób", a jedynie "rejestratora taśm" i zatrzymania archiwizacji. Zasób jest zawsze przechowywany ze zarchiwizowana zawartością wideo, dopóki nie wywołasz jawnie wywołania delete samego zasobu. Po usunięciu danych liveOutput rejestrowana zawartość zasobu jest nadal dostępna do odtwarzania za pośrednictwem wszystkich już opublikowanych adresów URL lokalizatora przesyłania strumieniowego. Jeśli chcesz usunąć możliwość odtwarzania zarchiwizowanej zawartości przez klienta, musisz najpierw usunąć wszystkie lokalizatory z zasobu, a także opróżnić pamięć podręczną usługi CDN w ścieżce adresu URL, jeśli używasz sieci CDN do dostarczania. W przeciwnym razie zawartość będzie żyć w pamięci podręcznej usługi CDN w standardowym ustawieniu czasu do transmisji na żywo w sieci CDN (co może potrwać do 72 godzin).

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>Tworzenie lokalizatora przesyłania strumieniowego w celu publikowania manifestów HLS i DASH

> [!NOTE]
> Po utworzeniu Media Services zostanie do  Twojego konta dodany domyślny punkt końcowy przesyłania strumieniowego **w stanie** Zatrzymany. Aby rozpocząć przesyłanie strumieniowe [](encode-dynamic-packaging-concept.md) zawartości oraz korzystać z dynamicznego pakowania i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz przesyłać strumieniowo zawartość, musi mieć **stan** Uruchomiony.

Po opublikowaniu zasobu przy użyciu lokalizatora przesyłania strumieniowego wydarzenie na żywo (do długości okna DVR) będzie nadal dostępne do momentu wygaśnięcia lub usunięcia lokalizatora przesyłania strumieniowego, w zależności od tego, co nastąpi najpierw. W ten sposób udostępnisz wirtualną "taśmę" odbiorcom, którzy będą widzieć na żywo i na żądanie. Ten sam adres URL może służyć do obserwowania wydarzenia na żywo, okna DVR lub zasobu na żądanie po zakończeniu rejestrowania (po usunięciu danych wyjściowych na żywo).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>Kompilowanie ścieżek do manifestów HLS i DASH

Metoda BuildManifestPaths w przykładzie pokazuje, jak deterministycznie utworzyć ścieżki przesyłania strumieniowego do użycia na potrzeby dostarczania dash lub HLS do różnych platform klientów i odtwarzaczy.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>Oglądanie wydarzenia

Aby obejrzeć zdarzenie, skopiuj adres URL przesyłania strumieniowego, który został u ciebie, gdy uruchomiono kod opisany w te tematze Create a Streaming Locator (Tworzenie lokalizatora przesyłania strumieniowego). Możesz użyć wybranego odtwarzacza multimedialnego. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) można przetestować strumień pod adresem https://ampdemo.azureedge.net .

Po zatrzymaniu wydarzenia na żywo wydarzenie jest automatycznie konwertowane na zawartość na żądanie. Nawet po zatrzymaniu i usunięciu wydarzenia użytkownicy mogą przesyłać strumieniowo zarchiwizowana zawartość jako wideo na żądanie, o ile nie usuniesz zasobu. Nie można usunąć zasobu, jeśli jest on używany przez zdarzenie; Najpierw należy usunąć zdarzenie.

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Oczyszczanie zasobów na koncie usługi Media Services

Po uruchomieniu aplikacji na całej drodze automatycznie wyczyści ona wszystkie zasoby używane w funkcji o nazwie "cleanUpResources". Upewnij się, że aplikacja lub debuger działa aż do ukończenia, lub że możesz przeciekać zasoby i kończyć się uruchomionymi wydarzeniami na żywo na Twoim koncie. Sprawdź dokładnie tę Azure Portal, aby upewnić się, że wszystkie zasoby są wyczyszczone na Media Services konta.  

W przykładowym kodzie zapoznaj się z metodą **cleanUpResources,** aby uzyskać szczegółowe informacje.

> [!IMPORTANT]
> Pozostawienie uruchomionego wydarzenia na żywo wiąże się z naliczaniem opłat. Należy pamiętać, że jeśli projekt/program ulegnie awarii lub zostanie zamknięty z jakiegokolwiek powodu, wydarzenie na żywo może pozostać uruchomione w stanie naliczania opłat.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawanie pytań, opinie, uzyskiwanie aktualizacji

Zapoznaj się z [artykułem społeczności Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, dawania opinii i uzyskania aktualizacji Media Services.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Więcej dokumentacji dla deweloperów na temat Node.js na platformie Azure

- [Platforma Azure dla deweloperów języka JavaScript & Node.js JavaScript](/azure/developer/javascript/)
- [Media Services źródłowy w repozytorium @azure/azure-sdk-for-js Usługi Git Hub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentacja pakietu platformy Azure dla Node.js deweloperów](/javascript/api/overview/azure/)


## <a name="next-steps"></a>Następne kroki

[Przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md)