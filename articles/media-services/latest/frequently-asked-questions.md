---
title: Azure Media Services v3 — często zadawane pytania
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Media Services wersji 3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3ebff5a40528e9e3ea0e75c4b51529638de34b5d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505770"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 — często zadawane pytania

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Media Services wersji 3.

## <a name="general"></a>Ogólne

### <a name="does-media-services-store-any-customer-data-outside-of-the-service-region"></a>Czy Media Services przechowywać dane klienta poza regionem usługi?

- Klienci dołączą własne konta magazynu do swoich kont Azure Media Services.  Wszystkie dane zasobów są przechowywane na skojarzonych kontach magazynu, a klient kontroluje lokalizację i typ replikacji tego magazynu.
- Dodatkowe dane skojarzone z kontem Media Services (w tym klucze szyfrowania zawartości, klucze weryfikacji tokenu, adresy URL JobInputHttp i inne metadane jednostki) są przechowywane w magazynie firmy Microsoft w ramach regionu wybranego dla konta Media Services.
    - Ze względu na [wymagania dotyczące miejsca zamieszkania danych](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) w Brazylii Południowo-Wschodnia Azja, dodatkowe dane konta są przechowywane w strefie nadmiarowej i znajdują się w jednym regionie. W przypadku Azja Południowo-Wschodnia wszystkie dodatkowe dane konta są przechowywane w Singapurze i w Brazylii Południowej, dane są przechowywane w Brazylii.
    - W regionach innych niż Brazylia Południowa i Południowo-Wschodnia dane konta mogą być również przechowywane w magazynie należącym do firmy Microsoft w [sparowanym regionie](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).
- Azure Media Services to usługa regionalna i nie zapewnia [wysokiej dostępności](media-services-high-availability-encoding.md) ani replikacji danych. Klienci, którzy potrzebują tych funkcji, są zdecydowanie zachęcani do tworzenia rozwiązań przy użyciu kont Media Services w wielu regionach.  Przykład przedstawiający sposób tworzenia rozwiązania wysokiej dostępności dzięki Media Services wideo na żądanie jest dostępny jako przewodnik.

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Jakie są ograniczenia Azure Portal dla Media Services v3?

Za pomocą [Azure Portal](https://portal.azure.com/) można zarządzać zdarzeniami na żywo v3, wyświetlać zasoby v3 i zadania, uzyskiwać informacje o uzyskiwaniu dostępu do interfejsów API, szyfrować zawartość. <br/>W przypadku wszystkich innych zadań zarządzania (na przykład zarządzania transformami i zadaniami lub analizowania zawartości v3) należy użyć [interfejsu API REST](/rest/api/media/accountfilters), [interfejsu wiersza polecenia](/cli/azure/ams)lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

Jeśli Twoje wideo zostało wcześniej przekazane do konta Media Services przy użyciu interfejsu API Media Services v3 lub zawartość została wygenerowana na podstawie danych wyjściowych na żywo, nie będą wyświetlane przyciski **Koduj**, **Analizuj** ani **Szyfruj** w Azure Portal. Użyj interfejsów API Media Services v3, aby wykonać te zadania.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Jakie role platformy Azure mogą wykonywać akcje na Azure Media Services zasobach? 

Zobacz [Kontrola dostępu oparta na rolach (Azure RBAC) dla kont Media Services](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Jak mogę strumieniowo z urządzeniami Apple iOS?

Upewnij się, że masz **(format = M3U8-AAPL)** na końcu ścieżki (po części **/manifest** adresu URL), aby poinformować serwer źródłowy przesyłania strumieniowego, aby ZWRACAŁ zawartość http Live Streaming (HLS) do użycia na urządzeniach natywnych Apple iOS. Aby uzyskać szczegółowe informacje, zobacz [dostarczanie zawartości](dynamic-packaging-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Jak mogę skonfigurować jednostki zarezerwowane multimediów?

W przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez Media Services v3 lub Video Indexer, zalecamy udostępnienie konta przy użyciu 10 jednostek zarezerwowanych multimediów (MRUs). Jeśli potrzebujesz więcej niż 10 MRUs S3, Otwórz bilet pomocy technicznej przy użyciu [Azure Portal](https://portal.azure.com/).

Aby uzyskać szczegółowe informacje, zobacz [Skalowanie przetwarzania multimediów](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Jaka jest zalecana metoda przetwarzania wideo?

Za pomocą [transformacji](/rest/api/media/transforms) można skonfigurować typowe zadania związane z kodowaniem lub analizowaniem filmów wideo. Każde przekształcenie opisuje przepis lub przepływ pracy zadań do przetwarzania plików wideo lub audio. [Zadanie](/rest/api/media/jobs) to rzeczywiste żądanie Media Services, aby zastosować transformację do wejściowej zawartości wideo lub audio. Po utworzeniu przekształcenia można przesłać zadania za pomocą interfejsów API Media Services lub dowolnego z opublikowanych zestawów SDK. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Wideo zostało przekazane, zakodowane i opublikowane. Dlaczego film wideo nie jest odtwarzany podczas próby przesyłania strumieniowego?

Jedną z najczęstszych przyczyn jest to, że nie masz punktu końcowego przesyłania strumieniowego, z którego próbujesz odtworzyć stan działania.

### <a name="how-does-pagination-work"></a>Jak działa stronicowanie?

W przypadku korzystania z stronicowania należy zawsze używać następnego linku do wyliczania kolekcji i nie zależy od określonego rozmiaru strony. Aby uzyskać szczegółowe informacje i przykłady, zobacz [filtrowanie, porządkowanie, stronicowanie](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Jakie funkcje nie są jeszcze dostępne w wersji Azure Media Services v3?

Aby uzyskać szczegółowe informacje, zobacz [Przewodnik migracji](migrate-v-2-v-3-migration-introduction.md).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Jaki jest proces przechodzenia konta Media Services między subskrypcjami?  

Aby uzyskać szczegółowe informacje, zobacz [Przechodzenie do konta Media Services między subskrypcjami](media-services-account-concept.md).

## <a name="live-streaming"></a>Transmisja strumieniowa na żywo 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Jak mogę zatrzymać strumień na żywo po zakończeniu emisji?

Można to rozejść po stronie klienta lub po stronie serwera.

#### <a name="client-side"></a>Po stronie klienta

Aplikacja sieci Web powinna monitować użytkownika o zakończenie emisji w trakcie zamykania przeglądarki. Jest to zdarzenie przeglądarki, które może obsłużyć aplikacja sieci Web.

#### <a name="server-side"></a>Po stronie serwera

Możesz monitorować zdarzenia na żywo, subskrybując zdarzenia Azure Event Grid. Aby uzyskać więcej informacji, zobacz [schemat zdarzeń EventGrid](media-services-event-schemas.md#live-event-types).

Można:

* [Zasubskrybuj](reacting-to-media-services-events.md) zdarzenia [Microsoft. Media. LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) na poziomie strumienia i monitoruj, że żadne ponowne połączenia nie są dostępne przez pewien czas, aby zatrzymać i usunąć wydarzenie na żywo.
* [Zasubskrybuj](reacting-to-media-services-events.md) zdarzenia [pulsu](media-services-event-schemas.md#liveeventingestheartbeat) poziomu śledzenia. Jeśli wszystkie ścieżki mają przychodzącą szybkość transmisji bitów do 0 lub Ostatnia sygnatura czasowa nie rośnie, można bezpiecznie zamknąć wydarzenie na żywo. Zdarzenia pulsu są dostępne co 20 sekund dla każdej ścieżki, więc może być to bit.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Jak mogę Wstawianie pęknięć/filmów wideo i obrazów w strumieniu na żywo?

Kodowanie na żywo w programie Media Services V3 nie obsługuje jeszcze wstawiania obrazów wideo lub obrazu podczas przesyłania strumieniowego na żywo. 

Możesz użyć [lokalnego kodera](recommended-on-premises-live-encoders.md) do przełączania źródłowego wideo. Wiele aplikacji zapewnia możliwość przełączania źródeł, w tym Wirecast Telestream, przełączników Studio (w systemach iOS) i OBS Studio (bezpłatna aplikacja).

## <a name="content-protection"></a>Ochrona zawartości

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Czy należy używać szyfrowania kluczowego AES-128 lub systemu DRM?

Klienci często zastanawiają się, czy powinni używać szyfrowania AES, czy systemu DRM. Główną różnicą między tymi dwoma systemami jest szyfrowanie AES, klucz zawartości jest przesyłany do klienta za pośrednictwem protokołu TLS, dzięki czemu klucz jest szyfrowany podczas przesyłania, ale bez dodatkowego szyfrowania ("w czyszczeniu"). W związku z tym klucz używany do odszyfrowywania zawartości jest dostępny dla odtwarzacza klienta i może być wyświetlany w wyniku śledzenia sieci na kliencie w postaci zwykłego tekstu. Szyfrowanie za pomocą klucza AES-128 jest odpowiednie do przypadków użycia, w których przeglądarka jest zaufaną stroną (na przykład w przypadku szyfrowania wideo firmowych dystrybuowanych przez pracowników).

Systemy DRM, takie jak PlayReady, Widevine i FairPlay, zapewniają dodatkowy poziom szyfrowania na klucz używany do odszyfrowywania zawartości w porównaniu z kluczem czystym AES-128. Klucz zawartości jest szyfrowany do klucza chronionego przez środowisko uruchomieniowe DRM poza szyfrowaniem na poziomie transportu udostępnionym przez protokół TLS. Ponadto odszyfrowywanie jest obsługiwane w bezpiecznym środowisku na poziomie systemu operacyjnego, co utrudnia złośliwemu użytkownikowi ataki. Zalecamy używanie technologii DRM w przypadku przypadków użycia, w których przeglądarka może nie być zaufaną stroną i potrzebujesz najwyższego poziomu zabezpieczeń.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Jak mogę pokazać wideo tylko użytkownikom z określonym uprawnieniem bez korzystania z usługi Azure AD?

Nie musisz używać żadnego określonego dostawcy tokenów, takiego jak Azure Active Directory (Azure AD). Za pomocą szyfrowania klucza asymetrycznego można utworzyć własnego dostawcę [JWT](https://jwt.io/) (tzw. Secure Token Service lub STS). W niestandardowej usłudze STS można dodawać oświadczenia na podstawie logiki biznesowej.

Upewnij się, że wystawcy, odbiorcy i oświadczenia wszystkie pasują dokładnie do tych, które są używane w tokenach JWT, i `ContentKeyPolicyRestriction` Wartość użyta w `ContentKeyPolicy` .

Aby uzyskać więcej informacji, zobacz [Ochrona zawartości przy użyciu Media Services szyfrowania dynamicznego](content-protection-overview.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Jak i gdzie uzyskać token JWT przed użyciem go w celu zażądania licencji lub klucza?

W przypadku produkcji należy mieć usługę Secure Token Service (czyli usługę sieci Web), która wystawia token JWT przy żądaniu HTTPS. Dla testu można użyć kodu pokazanego w `GetTokenAsync` metodzie zdefiniowanej w [program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

Gracz wysyła żądanie, po uwierzytelnieniu użytkownika w usłudze STS dla takiego tokenu i przypisuje go jako wartość tokenu. Możesz użyć [interfejsu API Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

Przykład uruchamiania usługi STS z kluczem symetrycznym lub kluczem asymetrycznym można znaleźć w [narzędziu JWT](https://aka.ms/jwt). Przykład odtwarzacza oparty na Azure Media Player użyciu takiego tokenu JWT można znaleźć w [narzędziu Azure Media Test Tool](https://aka.ms/amtest). (Rozwiń łącze **player_settings** , aby zobaczyć dane wejściowe tokenu).

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Jak mogę autoryzować żądania przesyłania strumieniowego wideo przy użyciu szyfrowania AES?

Poprawne podejście polega na użyciu usługi bezpiecznego tokenu. W programie STS, w zależności od profilu użytkownika, Dodaj różne oświadczenia (takie jak "użytkownik Premium", "użytkownik podstawowy", "bezpłatna wersja próbna użytkownika"). Przy użyciu różnych oświadczeń w tokenie JWT użytkownik może zobaczyć inną zawartość. Dla różnych zawartości lub zasobów `ContentKeyPolicyRestriction` będzie miała odpowiadająca `RequiredClaims` wartość.

Użyj Azure Media Services interfejsów API do konfigurowania dostarczania licencji/kluczy i szyfrowania zasobów (jak pokazano w [tym przykładzie](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Aby uzyskać więcej informacji, zobacz:

- [Omówienie ochrony zawartości](content-protection-overview.md)
- [Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Czy należy używać protokołu HTTP, czy HTTPS?
Aplikacja odtwarzacza ASP.NET MVC musi obsługiwać następujące elementy:

* Uwierzytelnianie użytkowników za pośrednictwem usługi Azure AD, która jest objęta protokołem HTTPS.
* Wymiana JWT między klientem a usługą Azure AD, która jest objęta protokołem HTTPS.
* Pozyskiwanie licencji DRM przez klienta, które musi znajdować się w protokole HTTPS, jeśli dostarczanie licencji jest zapewnione przez Media Services. Pakiet produktów PlayReady nie wymaga protokołu HTTPS do dostarczania licencji. Jeśli Twój serwer licencji PlayReady znajduje się poza Media Services, możesz użyć protokołu HTTP lub HTTPS.

Aplikacja odtwarzacza ASP.NET używa protokołu HTTPS zgodnie z najlepszymi rozwiązaniami, więc Media Player znajduje się na stronie w obszarze protokołu HTTPS. Jednak protokół HTTP jest preferowany do przesyłania strumieniowego, dlatego należy wziąć pod uwagę następujące problemy związane z zawartością mieszaną:

* Przeglądarka nie zezwala na zawartość mieszaną. Ale wtyczki, takie jak Silverlight i wtyczka OSMF, obsługują gładkie i PAUZy. Zawartość mieszana to problem z bezpieczeństwem z powodu zagrożenia związanego z możliwością iniekcji złośliwego kodu JavaScript, co może stanowić zagrożenie dla danych klientów. Przeglądarki blokują tę funkcję domyślnie. Jedynym sposobem obejścia tego problemu jest to, że po stronie serwera (pochodzenia) można zezwalać na wszystkie domeny (niezależnie od protokołu HTTPS lub HTTP). To prawdopodobnie nie jest dobrym pomysłem.
* Unikaj mieszanej zawartości. Zarówno aplikacja odtwarzacza, jak i Media Player powinny używać protokołu HTTP lub HTTPS. W przypadku odtwarzania zawartości mieszanej Technologia Silverlight wymaga wyczyszczenia ostrzeżenia o zawartości mieszanej. Błyskowa techniczna obsługuje zawartość mieszaną bez ostrzeżenia o zawartości mieszanej.
* Jeśli punkt końcowy przesyłania strumieniowego został utworzony przed 2014 sierpnia, nie będzie obsługiwał protokołu HTTPS. W takim przypadku Utwórz i Użyj nowego punktu końcowego przesyłania strumieniowego dla protokołu HTTPS.

### <a name="what-about-live-streaming"></a>Co z przesyłaniem strumieniowym na żywo?

Można użyć dokładnie tego samego projektu i implementacji, aby chronić przesyłanie strumieniowe na żywo w Media Services przez traktowanie zasobu skojarzonego z programem jako elementu zawartości VOD. Aby zapewnić ochronę przy użyciu technologii wielowątkowości na żywo, Zastosuj tę samą konfigurację/przetwarzanie do zasobu, tak jakby był to zasób VOD przed skojarzeniem elementu zawartości z danymi wyjściowymi na żywo.

### <a name="what-about-license-servers-outside-media-services"></a>Co o serwerach licencji poza Media Services?

Często klienci zainwestowali w farmę serwerów licencji we własnym centrum danych lub w jednym hostowanym przez dostawców usług DRM. Za pomocą Media Services Content Protection można działać w trybie hybrydowym. Zawartość może być hostowana i dynamicznie chroniona w Media Services, natomiast licencje DRM są dostarczane przez serwery poza Media Services. W takim przypadku należy wziąć pod uwagę następujące zmiany:

* Usługa STS musi wydać tokeny, które są akceptowalne i mogą być weryfikowane przez farmę serwerów licencji. Na przykład serwery licencji Widevine udostępniane przez Axinom wymagają określonego tokenu JWT, który zawiera komunikat o uprawnieniach. Musisz mieć usługę STS, aby wystawić taki token JWT. 
* Nie trzeba już konfigurować usługi dostarczania licencji w Media Services. Podczas konfigurowania programu należy podać adresy URL pozyskiwania licencji (dla oprogramowania PlayReady, Widevine i FairPlay) `ContentKeyPolicy` .

> [!NOTE]
> Widevine to usługa świadczona przez firmę Google, która podlega postanowieniom dotyczącym usług i zasadom zachowania poufności informacji w firmie Google.

## <a name="media-services-v2-vs-v3"></a>Media Services V2 a v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Czy mogę używać Azure Portal do zarządzania zasobami v3?

Obecnie można użyć [Azure Portal](https://portal.azure.com/) , aby:

* Zarządzanie [zdarzeniami na żywo](live-events-outputs-concept.md) w Media Services v3. 
* Wyświetl [zasoby](assets-concept.md)v3 (nie Zarządzaj). 
* [Uzyskaj informacje na temat uzyskiwania dostępu do interfejsów API](./access-api-howto.md). 

W przypadku wszystkich innych zadań zarządzania (na przykład [transformacji i zadań](transforms-jobs-concept.md) oraz [ochrony zawartości](content-protection-overview.md)) należy użyć [interfejsu API REST](/rest/api/media/), interfejsu [wiersza polecenia platformy Azure](/cli/azure/ams)lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Czy istnieje koncepcja AssetFile w wersji 3?

`AssetFile`Koncepcja została usunięta z interfejsu API Media Services, aby oddzielić Media Services od zależności zestawu SDK magazynu. Teraz usługa Azure Storage, nie Media Services, utrzymuje informacje, które należą do zestawu SDK magazynu. 

Aby uzyskać więcej informacji, zobacz [Migrowanie do Media Services v3](migrate-v-2-v-3-migration-introduction.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Gdzie działa szyfrowanie magazynu po stronie klienta?

Zalecamy teraz korzystanie z szyfrowania magazynu po stronie serwera (domyślnie włączone). Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage platformy Azure dla danych przechowywanych w spoczynku](../../storage/common/storage-service-encryption.md).

## <a name="offline-streaming"></a>Przesyłanie strumieniowe w trybie offline

### <a name="fairplay-streaming-for-ios"></a>FairPlay Streaming dla systemu iOS

Poniższe często zadawane pytania zapewniają pomoc w rozwiązywaniu problemów z FairPlay przesyłania strumieniowego w trybie offline dla systemu iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Dlaczego dźwięk jest odtwarzany tylko w trybie offline, ale nie wideo?

Takie zachowanie wydaje się być projektem przykładowej aplikacji. Jeśli istnieje alternatywna ścieżka audio (w przypadku programu HLS) w trybie offline, zarówno system iOS 10, jak i iOS 11 domyślnie są alternatywną ścieżką audio. Aby zrekompensować to zachowanie w trybie offline w trybie online, Usuń alternatywną ścieżkę audio ze strumienia. Aby to zrobić na Media Services, Dodaj filtr manifestu dynamicznego **tylko-false**. Innymi słowy, adres URL HLS ma koniec **. ISM/manifest (format = M3U8-AAPL, audio-Only = false)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Dlaczego nadal Odtwarzaj tylko dźwięk bez wideo w trybie offline po dodaniu tylko audio = FAŁSZ?

W zależności od projektu klucza pamięci podręcznej dla usługi Content Delivery Network zawartość może być buforowana. Przeczyść pamięć podręczną.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Czy w systemie iOS 11 oprócz systemu iOS 10 jest obsługiwany tryb offline FPS?

Tak. Tryb offline FPS jest obsługiwany w systemach iOS 10 i iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Dlaczego nie mogę znaleźć dokumentu "odtwarzanie w trybie offline za pomocą FairPlay streaming i HTTP Live Streaming" w zestawie SDK serwera FPS?

Ponieważ zestaw FPS SDK serwera w wersji 4, ten dokument został scalony w przewodniku programowania strumieniowego FairPlay.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Co to jest struktura plików pobierana/w trybie offline na urządzeniach z systemem iOS?

Pobrana struktura plików na urządzeniu z systemem iOS wygląda podobnie do poniższego zrzutu ekranu. `_keys`Folder przechowuje pobrane licencje FPS z jednym plikiem magazynu dla każdego hosta usługi licencjonowania. `.movpkg`Folder przechowuje zawartość audio i wideo. 

Pierwszy folder o nazwie kończącej się znakiem łącznika, po którym następuje cyfra, zawiera zawartość wideo. Wartość liczbowa to Szczytowa przepustowość odwzorowania wideo. Drugi folder o nazwie kończącej się znakiem łącznika, po którym następuje 0, zawiera zawartość audio. Trzeci folder o nazwie `Data` zawiera główną listę odtwarzania zawartości fps. Na koniec boot.xml zawiera pełny opis `.movpkg` zawartości folderu. 

![Struktura plików trybu offline dla przykładowej aplikacji FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Oto przykładowy plik boot.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Widevine Streaming dla systemu Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Jak można dostarczać trwałe licencje (z włączonymi trybem offline) dla niektórych klientów i nietrwałych licencji (wyłączone w trybie offline) dla innych użytkowników? Czy muszę duplikować zawartość i używać oddzielnych kluczy zawartości?

Ponieważ Media Services wersja 3 umożliwia zasobom posiadanie wielu `StreamingLocator` wystąpień, można:

* Jedno `ContentKeyPolicy` wystąpienie z `license_type = "persistent"` , `ContentKeyPolicyRestriction` z funkcją Claim on `"persistent"` i jego `StreamingLocator` .
* Inne `ContentKeyPolicy` wystąpienie z `license_type="nonpersistent"` , `ContentKeyPolicyRestriction` z elementem Claim on `"nonpersistent` "i" `StreamingLocator` .
* Dwa `StreamingLocator` wystąpienia o różnych `ContentKey` wartościach.

W zależności od logiki biznesowej niestandardowej usługi STS różne oświadczenia są wystawiane w tokenie JWT. Za pomocą tokenu można uzyskać tylko odpowiednią licencję i można odtworzyć tylko odpowiedni adres URL.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Co to jest mapowanie między poziomami zabezpieczeń Widevine i Media Services DRM?

"Przegląd architektury DRM" Widevine "definiuje trzy poziomy zabezpieczeń. Jednak [dokumentacja Azure Media Services na szablonie licencji Widevine](widevine-license-template-overview.md) zawiera pięć poziomów zabezpieczeń (wymagania dotyczące niezawodności klientów na potrzeby odtwarzania). W tej sekcji opisano sposób mapowania poziomów zabezpieczeń.

Oba zestawy poziomów zabezpieczeń są definiowane przez firmę Google Widevine. Różnica jest na poziomie użycia: architektura lub interfejs API. W interfejsie API Widevine są używane pięć poziomów zabezpieczeń. `content_key_specs`Obiekt, który zawiera `security_level` , jest deserializowany i przeszedł do usługi dostarczania globalnego Widevine przez usługę licencji Widevine Azure Media Services. W poniższej tabeli przedstawiono mapowanie między dwoma zestawami poziomów zabezpieczeń.

| **Poziomy zabezpieczeń zdefiniowane w architekturze Widevine** |**Poziomy zabezpieczeń używane w interfejsie API Widevine**|
|---|---| 
| **Poziom zabezpieczeń 1**: cała zawartość, Kryptografia i kontrola są wykonywane w ramach zaufanego środowiska wykonawczego (tee). W niektórych modelach implementacji przetwarzanie zabezpieczeń może być wykonywane w różnych mikroukładach.|**security_level = 5**: Kryptografia, dekodowanie i cała obsługa nośnika (skompresowana i nieskompresowana) musi być obsługiwana w ramach tee sprzętowego.<br/><br/>**security_level = 4**: Kryptografia i dekodowanie zawartości należy wykonać w ramach tee sprzętowego.|
**Poziom zabezpieczeń 2**: Kryptografia (ale nie przetwarzanie wideo) jest wykonywana w tee. Odszyfrowane bufory są zwracane do domeny aplikacji i przetwarzane przez oddzielnego sprzętu wideo lub oprogramowania. Jednak na poziomie 2 informacje kryptograficzne są nadal przetwarzane tylko w ramach TEE.| **security_level = 3**: materiał klucza i operacje kryptograficzne muszą być wykonywane w ramach tee sprzętowego. |
| **Poziom zabezpieczeń 3**: na urządzeniu nie ma tee. Odpowiednie środki można podjąć w celu ochrony informacji kryptograficznych i odszyfrowanej zawartości w systemie operacyjnym hosta. Implementacja poziomu 3 może również obejmować sprzętowy aparat kryptograficzny, ale zwiększa tylko wydajność, a nie zabezpieczenia. | **security_level = 2**: wymagane jest Kryptografia oprogramowania i niesłonięty dekoder.<br/><br/>**security_level = 1**: wymagana jest Kryptografia białego okna oparta na oprogramowaniu.|

#### <a name="why-does-content-download-take-so-long"></a>Dlaczego pobieranie zawartości trwa tak długo?

Istnieją dwa sposoby poprawy szybkości pobierania:

* Umożliwia włączenie usługi Content Delivery Network w celu uzyskania większej liczby użytkowników niż punkt końcowy źródła/przesyłania strumieniowego na potrzeby pobierania zawartości. Jeśli użytkownik trafi punkt końcowy przesyłania strumieniowego, każdy segment HLS lub fragment KRESKi jest dynamicznie spakowany i szyfrowany. Pomimo tego, że opóźnienie jest skalowane w milisekundach dla każdego segmentu lub fragmentu, jeśli masz wideo o długim czasie, skumulowane opóźnienie może być duże i spowodować dłuższe pobieranie.
* Udostępnij użytkownikom opcję selektywnego pobierania warstw jakości wideo i ścieżek audio zamiast całej zawartości. W trybie offline nie ma żadnego punktu na pobranie wszystkich warstw jakości. Istnieją dwa sposoby osiągnięcia tego celu:

  * Kontrolowane przez klienta: aplikacja odtwarzacza automatycznie wybiera lub wybiera użytkownika, warstwę jakości wideo i ścieżki audio do pobrania.
  * Kontrolowane przez usługę: można użyć funkcji manifestu dynamicznego w Azure Media Services, aby utworzyć filtr (globalny), który ogranicza HLS listy odtwarzania lub PAUZy do jednej warstwy jakości wideo i wybrane ścieżki audio. Następnie adres URL pobierania przedstawiony dla użytkowników będzie zawierać ten filtr.

## <a name="next-steps"></a>Następne kroki

[Media Services wersja 3 — Omówienie](media-services-overview.md)