---
title: Informacje o wersji Azure Media Services Video Indexer | Microsoft Docs
description: Aby zachować aktualność najnowszych zmian, ten artykuł zawiera najnowsze aktualizacje Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 02/16/2021
ms.author: juliako
ms.openlocfilehash: 618617d3602e45ebb15314c7cc5f6898a73bb71f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203729"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Informacje o wersji Azure Media Services Video Indexer

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` do czytnika źródła danych RSS.

Aby zachować aktualność najnowszych zmian, ten artykuł zawiera informacje na temat:

* Najnowsze wersje
* Znane problemy
* Poprawki błędów
* Funkcje uznane za przestarzałe

## <a name="march-2021"></a>Marzec 2021

Analiza dźwięku jest teraz dostępna w dodatkowych nowych pakietach funkcji audio w innym punkcie cenowym. Nowe podstawowe ustawienia wstępne analizy **audio** zapewniają opcję niskiego kosztu, aby wyodrębnić transkrypcję i napisy danych wyjściowych. Podstawowe ustawienie wstępne **audio** spowoduje utworzenie dwóch oddzielnych mierników na rachunku, w tym wierszy transkrypcji i oddzielnego wiersza do formatowania napisów i napisów. Aby uzyskać więcej informacji na temat cen, zobacz stronę z [cennikiem Media Services](https://azure.microsoft.com/pricing/details/media-services/) .

Nowo dodany pakiet jest dostępny podczas indeksowania lub ponownego indeksowania pliku przez wybranie **opcji Zaawansowane**  ->  **podstawowe** ustawienie wstępne audio (w polu listy rozwijanej **wideo i indeksowania audio** ).

## <a name="february-2021"></a>Luty 2021 r.

### <a name="multiple-account-owners"></a>Wielu właścicieli kont 

Rola właściciela konta została dodana do Video Indexer. Możesz dodawać, zmieniać i usuwać użytkowników. Zmień ich rolę. Aby uzyskać szczegółowe informacje na temat udostępniania konta, zobacz [Zapraszanie użytkowników](invite-users.md).

### <a name="audio-event-detection-public-preview"></a>Wykrywanie zdarzeń audio (publiczna wersja zapoznawcza)

> [!NOTE]
> Ta funkcja jest dostępna tylko na kontach wersji próbnej. 

Video Indexer teraz wykrywa następujące efekty audio w segmentach niebędących literami mowy zawartości: gunshot, szkło rozbicie, alarm, Siren, wybuch, Dog kora, wyróżniony, Laughter, reakcje na zjawisko (cheering, clapping i booing) i wyciszenie. 

Nowo dodana funkcja wpływająca na dźwięk jest dostępna podczas indeksowania pliku przez wybranie **opcji Zaawansowane**  ->  **Zaawansowane** ustawienia predefiniowane audio (w obszarze wideo i indeksowanie audio). Standardowe indeksowanie będzie obejmować tylko **wyciszenie** i **odporność**. 

Typ zdarzenia **clapping** , który został uwzględniony w poprzednim modelu efektów audio, jest teraz rozpakowywany częścią typu zdarzenia **reakcji** na działanie.

Po wybraniu opcji wyświetlania **szczegółowych** informacji wideo w witrynie internetowej [Video Indexer](https://www.videoindexer.ai/) efekty audio są wyświetlane na stronie.

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="Wykrywanie zdarzeń audio":::

### <a name="named-entities-enhancement"></a>Rozszerzanie nazwanych jednostek  

Wyodrębniona lista osób i lokalizacji została rozszerzona i zaktualizowana ogólnie. 

Ponadto model zawiera teraz osoby i lokalizacje w kontekście, które nie są sławę, takie jak "sam" lub "Home" w filmie wideo. 

## <a name="january-2021"></a>Styczeń 2021 r.

### <a name="video-indexer-is-deployed-on-us-government-cloud"></a>Video Indexer jest wdrażana w chmurze dla instytucji rządowych Stanów Zjednoczonych 

Teraz możesz utworzyć konto płatne Video Indexer w chmurze dla instytucji rządowych Stanów Zjednoczonych w regionach Wirginia i Arizona. Oferta bezpłatnej wersji próbnej Video Indexer nie jest dostępna w wymienionym regionie. Aby uzyskać więcej informacji, przejdź do dokumentacji Video Indexer. 

### <a name="video-indexer-deployed-in-the-india-central-region"></a>Video Indexer wdrożone w regionie Indie Środkowe 

Teraz można utworzyć konto płatne Video Indexer w regionie Indie Środkowe. 

### <a name="new-dark-mode-for-the-video-indexer-website-experience"></a>Nowy tryb ciemny dla środowiska witryny sieci Web Video Indexer

Środowisko Video Indexer witryny sieci Web jest teraz dostępne w trybie ciemnym. Aby włączyć tryb ciemny, Otwórz panel Ustawienia i Włącz opcję **tryb ciemny** . 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="Ustawienie trybu ciemnego":::

## <a name="december-2020"></a>Grudzień 2020 r.

### <a name="video-indexer-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Indexer wdrożone w Szwajcaria Zachodnia i Szwajcaria Północna

Teraz można utworzyć konto płatne Video Indexer w regionach Szwajcaria Zachodnia i Szwajcaria Północna.

## <a name="october-2020"></a>Październik 2020 r.

### <a name="animated-character-identification-improvements"></a>Ulepszenia identyfikacji znaków animowanych  

Video Indexer obsługuje wykrywanie, grupowanie i rozpoznawanie znaków w animowanej zawartości poprzez integrację z Cognitive Services Custom Vision. Dodaliśmy znaczną poprawę tego algorytmu AI w rozpoznawaniu wykrywania i znaków, ponieważ wyniki analizy wyników i zidentyfikowane znaki są znacznie ulepszone.

### <a name="planned-video-indexer-website-authenticatication-changes"></a>Planowane zmiany authenticatication witryny sieci Web Video Indexer

Od 1 marca 2021, nie będzie już można zarejestrować się i zalogować się do [portalu deweloperów](video-indexer-use-apis.md) [witryny sieci Web Video Indexer](https://www.videoindexer.ai/) za pomocą serwisu Facebook lub LinkedIn.

Będziesz w stanie zarejestrować się i zalogować się przy użyciu jednego z następujących dostawców: Azure AD, Microsoft i Google.

> [!NOTE]
> Konta Video Indexer połączone z serwisem LinkedIn i serwisem Facebook nie będą dostępne po 1 marca 2021. 
> 
> Należy [zaprosić](invite-users.md) do konta Video Indexer usługi Azure AD, Microsoft lub Google, aby nadal mieć dostęp. Można dodać dodatkowego właściciela obsługiwanych dostawców, zgodnie z opisem w [zaproszeniu](invite-users.md). <br/>
> Alternatywnie możesz utworzyć konto płatne i przeprowadzić migrację danych.

## <a name="august-2020"></a>Sierpień 2020 r.

### <a name="mobile-design-for-the-video-indexer-website"></a>Projektowanie aplikacji mobilnych dla witryny sieci Web Video Indexer

Środowisko witryny sieci Web Video Indexer obsługuje teraz obsługę urządzeń przenośnych. Środowisko użytkownika odpowiada na dostosowanie rozmiaru ekranu urządzenia przenośnego (z wyłączeniem interfejsów użytkownika dostosowania). 

### <a name="accessibility-improvements-and-bug-fixes"></a>Ulepszenia ułatwień dostępu i poprawki błędów 

W ramach WCAG (wskazówki dotyczące dostępności zawartości sieci Web) środowisko witryny internetowej Video Indexer jest wyrównane z klasy C w ramach standardów ułatwień dostępu firmy Microsoft. Rozwiązano kilka usterek i ulepszeń związanych z nawigowaniem po klawiaturze, dostępem programistycznym i czytnikiem ekranu. 

## <a name="july-2020"></a>Lipiec 2020 r.

### <a name="ga-for-multi-language-identification"></a>GA dla identyfikacji wielu języków

Identyfikacja w wielu językach jest przenoszona z wersji zapoznawczej na GA i gotowa do wydajnego użycia.

Nie ma wpływu na ceny związane z przejściem "wersja zapoznawcza do wersji zapoznawczej".

### <a name="video-indexer-website-improvements"></a>Udoskonalenia Video Indexer witryny sieci Web

#### <a name="adjustments-in-the-video-gallery"></a>Korekty w galerii wideo

Dodano nowy pasek wyszukiwania dla wyszukiwania głębokiego wglądu z dodatkowymi możliwościami filtrowania. Wyniki wyszukiwania zostały również udoskonalone.

Nowy widok listy z możliwością sortowania i zarządzania archiwum wideo z wieloma plikami.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>Nowy panel ułatwiający wybór i konfigurację

Dodano panel boczny w celu ułatwienia wyboru i konfiguracji użytkownika, co umożliwia proste i szybkie tworzenie i udostępnianie kont oraz Konfigurowanie ustawień.

Panel boczny jest również używany w preferencjach i pomocy użytkownika.

## <a name="june-2020"></a>Czerwiec 2020 r.

### <a name="search-by-topics"></a>Wyszukaj według tematów

Teraz możesz użyć interfejsu API wyszukiwania do wyszukiwania filmów wideo z określonymi tematami (tylko interfejsy API).

Tematy są dodawane jako część `textScope` parametru opcjonalnego. Aby uzyskać szczegółowe informacje, zobacz [interfejs API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Search-Videos) .  

### <a name="labels-enhancement"></a>Ulepszanie etykiet

Etykieta moduł tagujący została uaktualniona i teraz zawiera więcej etykiet wizualizacji, które można zidentyfikować.

## <a name="may-2020"></a>Maj 2020 r.

### <a name="video-indexer-deployed-in-the-east-us"></a>Video Indexer wdrożone w regionie Wschodnie stany USA

Teraz możesz utworzyć konto płatne Video Indexer w regionie Wschodnie stany USA.
 
### <a name="video-indexer-url"></a>Adres URL Video Indexer

Video Indexer regionalne punkty końcowe były bardzo ujednolicone do uruchamiania tylko z usługą www. Element akcji nie jest wymagany.

Od teraz na, dowiesz się, www.videoindexer.ai niezależnie od tego, czy zamierzasz osadzać widżety czy logować się do Video Indexer aplikacji sieci Web.

Wus.videoindexer.ai również zostanie przekierowana do sieci www. Więcej informacji można znaleźć w temacie [osadź Video Indexer widżety w aplikacjach](video-indexer-embed-widgets.md).

## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="new-widget-parameters-capabilities"></a>Nowe możliwości parametrów widżetu

Widżet **Insights** zawiera nowe parametry: `language` i `control` .

Widżet **odtwarzacza** ma nowy `locale` parametr. Oba `locale` i `language` Parametry kontrolują język odtwarzacza.

Aby uzyskać więcej informacji, zobacz sekcję [typy elementów widget](video-indexer-embed-widgets.md#widget-types) . 

### <a name="new-player-skin"></a>Nowa skórka odtwarzacza

Nowa skórka odtwarzacza została uruchomiona z zaktualizowanym projektem.

### <a name="prepare-for-upcoming-changes"></a>Przygotuj się na nadchodzące zmiany

* Obecnie następujące interfejsy API zwracają obiekt konta:

    * [Utwórz — konto płatne](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Paid-Account)
    * [Pobierz konto](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account)
    * [Get-accounts-Authorization](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-Authorization)
    * [Get-accounts-with-token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-With-Token)
 
    Obiekt konta ma `Url` pole wskazujące lokalizację [witryny sieci Web Video Indexer](https://www.videoindexer.ai/).
W przypadku płatnych kont `Url` pole jest obecnie wskazywane na wewnętrzny adres URL zamiast publicznej witryny sieci Web.
W najbliższych tygodniach zmienimy ją i zwrócimy adres URL [witryny internetowej Video Indexer](https://www.videoindexer.ai/) dla wszystkich kont (wersja próbna i płatna).

    Nie używaj wewnętrznych adresów URL, należy używać [Video Indexer publicznych interfejsów API](https://api-portal.videoindexer.ai/).
* Jeśli osadzasz Video Indexer adresy URL w aplikacjach, a adresy URL nie wskazują [witryny Video Indexer](https://www.videoindexer.ai/) lub punktu końcowego interfejsu API Video Indexer ( `https://api.videoindexer.ai` ), ale nie do regionalnego punktu końcowego (na przykład `https://wus2.videoindexer.ai` ), ponownie Wygeneruj adresy URL.

   Można to zrobić za pomocą jednej z:

    * Zamienianie adresu URL na adres URL wskazujący interfejsy API widżetu Video Indexer (na przykład [widżet Insights](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Insights-Widget))
    * Aby wygenerować nowy osadzony adres URL przy użyciu witryny sieci Web Video Indexer:
         
         Naciśnij klawisz **Play** , aby przejść do strony wideo — > kliknij przycisk **&lt; / &gt; Osadź** , > Skopiuj adres URL do aplikacji:
   
    Regionalne adresy URL nie są obsługiwane i zostaną zablokowane w najbliższych tygodniach.

## <a name="january-2020"></a>Styczeń 2020 r.
 
### <a name="custom-language-support-for-additional-languages"></a>Obsługa języków niestandardowych w dodatkowych językach

Video Indexer teraz obsługuje niestandardowe modele języka dla `ar-SY` , `en-UK` i `en-AU` (tylko interfejsy API).
 
### <a name="delete-account-timeframe-action-update"></a>Usuń aktualizację przedziału czasu konta

Akcja Usuń konto teraz usuwa konto w ciągu 90 dni, a nie 48 godzin.
 
### <a name="new-video-indexer-github-repository"></a>Nowe repozytorium usługi GitHub Video Indexer

Dostępna jest nowa Video Indexer GitHub z różnymi projektami, przewodniki z wprowadzeniem i przykłady kodu: https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Aktualizacja struktury Swagger

Video Indexer ujednolicone **uwierzytelnianie** i **operacje** w ramach jednej [specyfikacji Video Indexer openapi (Swagger)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson). Deweloperzy mogą znaleźć interfejsy API w [portalu dla deweloperów Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="update-transcript-with-the-new-api"></a>Aktualizowanie transkrypcji przy użyciu nowego interfejsu API

Zaktualizuj określoną sekcję w transkrypcji przy użyciu interfejsu API [Update-Video-index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) .

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Napraw konfigurację konta z poziomu portalu Video Indexer

Teraz możesz aktualizować Media Services konfigurację połączenia, aby samoobsługowo rozwiązywać problemy, takie jak: 

* Nieprawidłowy zasób Azure Media Services
* zmiany hasła
* Media Services zasoby zostały przeniesione między subskrypcjami  

Aby naprawić konfigurację konta, w portalu Video Indexer przejdź do Ustawienia Karta konto > (jako właściciel).

### <a name="configure-the-custom-vision-account"></a>Skonfiguruj niestandardowe konto wizji

Skonfiguruj niestandardowe konto wizji na płatnych kontach przy użyciu portalu Video Indexer (wcześniej było to obsługiwane tylko przez interfejs API). W tym celu zaloguj się do portalu Video Indexer, wybierz pozycję dostosowanie modelu > animowane znaki > Skonfiguruj. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Sceny, zrzuty i klatki kluczowe — teraz w jednym okienku szczegółowych informacji

Sceny, zrzuty i ramki kluczowe są teraz scalane w jeden wgląd w celu łatwiejszego użycia i nawigacji. Po wybraniu żądanej sceny można zobaczyć, jakie zrzuty i klatki kluczowe zawiera. 

### <a name="notification-about-a-long-video-name"></a>Powiadomienie dotyczące długiej nazwy wideo

Jeśli nazwa filmu wideo jest dłuższa niż 80 znaków, Video Indexer pokazuje opisowy błąd podczas przekazywania.

### <a name="streaming-endpoint-is-disabled-notification"></a>Powiadomienie o punkcie końcowym przesyłania strumieniowego jest wyłączone

Gdy punkt końcowy przesyłania strumieniowego jest wyłączony, Video Indexer wyświetli opisowy błąd na stronie odtwarzacza.

### <a name="error-handling-improvement"></a>Poprawa obsługi błędów

Kod stanu 409 będzie teraz zwracany przez [ponowne indeksowanie wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) i aktualizowanie interfejsów API [indeksu wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) w przypadku, gdy wideo jest aktywnie indeksowane, aby zapobiec zastępowaniu bieżących zmian w indeksie.

## <a name="november-2019"></a>Listopad 2019 r.
 
* Obsługa koreańskich niestandardowych modeli języków

    Indeksator wideo obsługuje teraz niestandardowe modele językowe w języku koreańskim ( `ko-KR` ) zarówno w interfejsie API, jak i w portalu. 
* Nowe języki obsługiwane w przypadku zamiany mowy na tekst (monitora STT)

    Interfejsy API Video Indexer obsługują teraz monitora STT w języku arabskim Levantine (AR-SY), dialekt angielskiej Brytanii (en-GB) i angielski dialekt australijski (en-AU).
    
    W przypadku przekazywania wideo zamieniono polecenie zh-HANS na zh-CN, obie są obsługiwane, ale zaleca się, aby Metoda zh-CN była zalecana i bardziej dokładna.
    
## <a name="october-2019"></a>Październik 2019 r.
 
* Wyszukaj animowane znaki w galerii

    W przypadku indeksowania animowanych znaków można teraz wyszukiwać je w postaci szpaltowej wideo na koncie. Aby uzyskać więcej informacji, zobacz [rozpoznawanie znaków animowanych](animated-characters-recognition.md).

## <a name="september-2019"></a>Wrzesień 2019 r.
 
Wiele zaliczek zapowiedziano w IBC 2019:
 
* Rozpoznawanie znaków animowanych (publiczna wersja zapoznawcza)

    Możliwość wykrywania grup AD rozpoznaje znaki w animowanej zawartości, poprzez integrację z niestandardową wizją. Aby uzyskać więcej informacji, zobacz [wykrywanie znaków animowanych](animated-characters-recognition.md).
* Identyfikacja w wielu językach (publiczna wersja zapoznawcza)

    Wykrywaj segmenty w wielu językach w ścieżce audio i twórz na ich podstawie wielojęzyczne transkrypcje. Wstępna pomoc techniczna: angielski, hiszpański, niemiecki i francuski. Aby uzyskać więcej informacji, zobacz [Automatyczne identyfikowanie i transkrybowanie zawartości w wielu językach](multi-language-identification-transcription.md).
* Wyodrębnianie jednostek nazwanych dla osób i lokalizacji

    Wyodrębnianie marek, lokalizacji i osób z mowy i tekstu wizualnego za pośrednictwem przetwarzania języka naturalnego (NLP).
* Klasyfikacja typu zrzutu redakcyjnego

    Tagowanie zrzutów z typami redakcyjnymi, takimi jak zamknięcie, średnie zrzuty, dwa zrzuty, wewnętrzne, zewnętrzne itd. Aby uzyskać więcej informacji, zobacz [wykrywanie typów zrzutów redakcyjnych](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Temat inferencing — teraz obejmujący poziom 2
    
    Temat inferencing model obsługuje teraz bardziej szczegółowy stopień szczegółowości taksonomii IPTC. Przeczytaj wszystkie szczegółowe informacje o [Azure Media Services nowych innowacyjności opartych na AI](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Sierpień 2019 r.
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer wdrożony w programie Południowe Zjednoczone Królestwo

Teraz możesz utworzyć konto płatne Video Indexer w regionie Południowo-południe.

### <a name="new-editorial-shot-type-insights-available"></a>Dostępne są nowe szczegółowe informacje o typie zrzutu redakcyjnego

Nowe Tagi dodawane do zrzutów wideo udostępniają redakcyjne "typy zrzutów", aby identyfikować je za pomocą wspólnych fraz redakcyjnych używanych w przepływie pracy tworzenia zawartości, takich jak: Extreme Closeup, Closeup, Wide, medium, dwa zrzuty, na zewnątrz, w lewo i w prawo (dostępne w formacie JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Dostępne są nowe osoby i lokalizacje

Video Indexer identyfikuje nazwane lokalizacje i osoby za pośrednictwem przetwarzania języka naturalnego (NLP) na podstawie OCR i transkrypcji wideo. Video Indexer używa algorytmu uczenia maszynowego do rozpoznawania, gdy określone lokalizacje (na przykład Eiffel Tower) lub osoby (na przykład Jan Nowak) są wywoływane w filmie wideo.

### <a name="keyframes-extraction-in-native-resolution"></a>Wyodrębnianie klatek kluczowych w rozdzielczości natywnej

Ramki kluczowe wyodrębnione przez Video Indexer są dostępne w oryginalnej rozdzielczości wideo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>Zaplanowanie szkolenia niestandardowych modeli czołowych z obrazów

Uczenie się zdjęć z obrazów przenoszonych z trybu podglądu do GA (dostępne za pośrednictwem interfejsu API i portalu).

> [!NOTE]
> Nie ma wpływu na ceny związane z przejściem "wersja zapoznawcza do wersji zapoznawczej".

### <a name="hide-gallery-toggle-option"></a>Ukryj opcję przełączania galerii

Użytkownik może wybrać opcję ukrycia karty Galeria w portalu (podobnie jak w przypadku ukrycia karty przykłady).
 
### <a name="maximum-url-size-increased"></a>Zwiększono maksymalny rozmiar adresu URL

Obsługa ciągu zapytania URL o 4096 (zamiast 2048) na indeksowaniu wideo.
 
### <a name="support-for-multi-lingual-projects"></a>Obsługa projektów wielojęzycznych

Projekty można teraz tworzyć w oparciu o wideo indeksowane w różnych językach (tylko interfejsy API).

## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="editor-as-a-widget"></a>Edytor jako widżet

Edytor Video Indexer AI jest teraz dostępny jako widżet, który ma zostać osadzony w aplikacjach klienta.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aktualizowanie niestandardowego modelu języka z pliku napisów w portalu

Klienci mogą udostępniać formaty plików VTT, narzędzia SRT i TTML jako dane wejściowe dla modeli języka na stronie dostosowywania portalu.

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer wdrożone na Japonia Wschodnia

Teraz można utworzyć konto płatne Video Indexer w regionie Japonia Wschodnia.

### <a name="create-and-repair-account-api-preview"></a>Tworzenie i naprawa interfejsu API konta (wersja zapoznawcza)

Dodano nowy interfejs API, który umożliwia [zaktualizowanie punktu końcowego lub klucza połączenia usługi Azure Media Service](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Ulepsz obsługę błędów podczas przekazywania 

Komunikat opisowy jest zwracany w przypadku nieprawidłowych konfiguracji bazowego konta Azure Media Services.

### <a name="player-timeline-keyframes-preview"></a>Klatki kluczowe programu Player — wersja zapoznawcza 

Możesz teraz zobaczyć podgląd obrazu za każdym razem na osi czasu odtwarzacza.

### <a name="editor-semi-select"></a>Wybór częściowo edytora

Teraz można wyświetlić podgląd wszystkich szczegółowych informacji, które są wybrane w wyniku wybrania konkretnego przedziału czasu wglądu w edytorze.

## <a name="may-2019"></a>Maj 2019 r.

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualizowanie niestandardowego modelu języka z pliku napisów

[Tworzenie niestandardowego modelu języka](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) i [Aktualizowanie niestandardowych modeli języka](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) interfejsy API obsługują teraz formaty plików VTT, narzędzia SRT i ttml jako dane wejściowe dla modeli języka.

Podczas wywoływania [interfejsu API aktualizowania transkrypcji wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)transkrypcja jest dodawana automatycznie. Model szkoleniowy skojarzony z filmem wideo jest również aktualizowany automatycznie. Aby uzyskać informacje na temat dostosowywania i uczenia modeli języka, zobacz [Dostosowywanie modelu języka za pomocą Video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nowe formaty pobierania transkrypcji — TXT i CSV

Oprócz obsługiwanego formatu napisów (narzędzia SRT, VTT i TTML), Video Indexer teraz obsługuje pobieranie transkrypcji w formatach TXT i CSV.

## <a name="next-steps"></a>Następne kroki

[Omówienie](video-indexer-overview.md)
