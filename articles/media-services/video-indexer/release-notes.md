---
title: Azure Media Services Video Indexer informacje o wersji | Microsoft Docs
description: Aby być na bieżąco z najnowszymi zmianami, ten artykuł zawiera najnowsze aktualizacje Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.custom: references_regions
ms.date: 03/30/2021
ms.author: juliako
ms.openlocfilehash: b3602d421718cbd1de3509751491ec6db65b1b01
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532890"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer informacji o wersji

>Otrzymuj powiadomienia o tym, kiedy chcieć ponownie chcieć na tę stronę, kopiując i wklejając ten adres URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` do czytnika kanałów informacyjnych RSS.

Aby być na bieżąco z najnowszymi zmianami, ten artykuł zawiera informacje na temat:

* Najnowsze wersje
* Znane problemy
* Poprawki błędów
* Funkcje uznane za przestarzałe

## <a name="march-2021"></a>Marzec 2021 r.

### <a name="audio-analysis"></a>Analiza dźwięku 

Analiza dźwięku jest teraz dostępna w dodatkowym nowym pakiecie funkcji dźwiękowych w różnych cenach. Nowe ustawienie **wstępne podstawowej** analizy dźwięku udostępnia nieoczywioną opcję wyodrębniania transkrypcji, tłumaczenia i formatowania transkrypcji mowy oraz napisów wyjściowych. Ustawienie **wstępne Basic Audio** (Podstawowy dźwięk) spowoduje wydzielenie dwóch oddzielnych mierników na rachunku, w tym linii do transkrypcji i oddzielnej linii formatowania napisów i transkrypcji. Więcej informacji na temat cen można znaleźć na [Media Services cennika.](https://azure.microsoft.com/pricing/details/media-services/)

Nowo dodany pakiet jest dostępny podczas indeksowania lub ponownego indeksowania pliku, wybierając ustawienie wstępne Zaawansowane podstawowy dźwięk (w polu listy rozwijanej Indeksowanie wideo i  ->   audio). 

### <a name="new-developer-portal"></a>Nowy portal dla deweloperów 

Video Indexer ma nowy portal dla deweloperów, wypróbuj nowe interfejsy API usługi Video Indexer i znajdź wszystkie odpowiednie zasoby w jednym miejscu: repozytorium [GitHub,](https://github.com/Azure-Samples/media-services-video-indexer)stack [overflow,](https://stackoverflow.com/questions/tagged/video-indexer) [społeczność](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer) [](https://feedback.azure.com/forums/932041-cognitive-services?category_id=399016) techniczna Video Indexer z odpowiednimi wpisami w blogu, często zadawanych pytaniach na temat usługi [Video Indexer,](faq.md)opinie użytkowników w celu wyrażania opinii i sugerowania funkcji oraz [link "CodePen"](https://codepen.io/videoindexer) z przykładami kodu widżetów. [](https://api-portal.videoindexer.ai/) 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>Zaawansowane możliwości dostosowywania widżetu szczegółowych informacji 

Zestaw SDK jest teraz dostępny do osadzania Video Indexer szczegółowych informacji użytkownika we własnej usłudze oraz dostosowywania jego stylu i danych. Zestaw SDK obsługuje standardowy widżet szczegółowych Video Indexer szczegółowych informacji oraz w pełni dostosowywalny widżet szczegółowych informacji. Przykładowy kod jest dostępny [Video Indexer repozytorium GitHub.](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization) Dzięki tym zaawansowanym możliwościom dostosowywania deweloper rozwiązań może stosować niestandardowe style i korzystać z własnych danych AI klienta oraz prezentować je w widżecie szczegółowych informacji (z Video Indexer szczegółowych informacji lub bez niego). 

### <a name="video-indexer-deployed-in-the-us-north-central--us-west-and-canada-central"></a>Video Indexer wdrożone w regionach Północno-środkowe stany USA, Zachodnie stany USA i Kanada Środkowa 

Teraz możesz utworzyć płatne konto Video Indexer regionach Północno-środkowe stany USA, Zachodnie stany USA i Kanada Środkowa
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>Obsługa nowych języków źródłowych dla funkcji mowy na tekst (STT), tłumaczenia i wyszukiwania 

Video Indexer obsługuje teraz STT, tłumaczenie i wyszukiwanie w języku duńskim ("da-DK"), norweskim('nb-NO'), szwedzkim('sv-SE'), fińskim('fi-FI'), francuski ('fr-CA'), tajskim('th-TH'), arabskim ("ar-BH", "ar-EG", "ar-IQ", "ar-JO", "ar-KW", "ar-LB", "ar-OM", "ar-QA", "ar-S" i "ar-SY" oraz turecki('tr-TR'). Te języki są dostępne zarówno w interfejsie API, jak i Video Indexer internetowej. 
 
### <a name="search-by-topic-in-video-indexer-website"></a>Wyszukiwanie według tematu w Video Indexer internetowej 

Teraz możesz używać funkcji wyszukiwania w górnej części strony Video Indexer internetowej, aby wyszukiwać filmy wideo z określonymi tematami. [](https://www.videoindexer.ai/account/login) 

## <a name="february-2021"></a>Luty 2021 r.

### <a name="multiple-account-owners"></a>Wielu właścicieli kont 

Dodano rolę właściciela konta do Video Indexer. Można dodawać, zmieniać i usuwać użytkowników; zmienić swoją rolę. Aby uzyskać szczegółowe informacje na temat udostępniania konta, zobacz [Invite users (Zapraszanie użytkowników).](invite-users.md)

### <a name="audio-event-detection-public-preview"></a>Wykrywanie zdarzeń audio (publiczna wersja zapoznawcza)

> [!NOTE]
> Ta funkcja jest dostępna tylko na kontach w wersji próbnej. 

Video Indexer wykrywa teraz następujące efekty dźwiękowe w segmentach zawartości, które nie są związane z mową: "gunshot", "glass sha podszycie", "alarm", "siren", "dosyć", "dosyć" dla psów, "odśmiecanie się". 

Nowo dodana funkcja dźwięku wpływa na funkcję jest dostępna podczas indeksowania pliku, wybierając opcję Zaawansowane wstępne ustawienie wstępne dźwięku (w obszarze  ->   Indeksowanie wideo i dźwięku). Indeksowanie standardowe będzie obejmować tylko **ciszę i** **reakcję społeczności.** 

Typ **zdarzenia clapping,** który został uwzględniony w poprzednim modelu efektów dźwiękowych, jest teraz wyodrębniony jako część typu zdarzenia reakcji **społeczności.**

Jeśli zdecydujesz się wyświetlić **szczegółowe** informacje [](https://www.videoindexer.ai/) o wideo w Video Indexer sieci Web, efekty dźwiękowe będą wyświetlane na stronie.

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="Wykrywanie zdarzeń audio":::

### <a name="named-entities-enhancement"></a>Rozszerzenie nazwanych jednostek  

Wyodrębniona lista osób i lokalizacji została rozszerzona i zaktualizowana ogólnie. 

Ponadto model zawiera teraz osoby i lokalizacje w kontekście, które nie są znane, takie jak "Sam" lub "Home" w filmie wideo. 

## <a name="january-2021"></a>Styczeń 2021 r.

### <a name="video-indexer-is-deployed-on-us-government-cloud"></a>Video Indexer wdrożona w chmurze dla instytucji rządowych USA 

Teraz możesz utworzyć płatne konto w Video Indexer dla instytucji rządowych USA w regionach Wirginia i Arizonia. Video Indexer bezpłatnej wersji próbnej nie jest dostępna we wspomnianym regionie. Aby uzyskać więcej informacji, przejdź do Video Indexer Dokumentacji. 

### <a name="video-indexer-deployed-in-the-india-central-region"></a>Video Indexer wdrożone w regionie Indie Środkowe 

Teraz możesz utworzyć konto Video Indexer w regionie Indie Środkowe. 

### <a name="new-dark-mode-for-the-video-indexer-website-experience"></a>Nowy tryb ciemny dla Video Indexer witryny internetowej

Środowisko Video Indexer witryny internetowej jest teraz dostępne w trybie ciemnym. Aby włączyć tryb ciemny, otwórz panel ustawień i włącz opcję **Tryb ciemny.** 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="Ustawienie trybu ciemnego":::

## <a name="december-2020"></a>Grudzień 2020 r.

### <a name="video-indexer-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Indexer wdrożone w zachodnich i północno-wschodnich Regionach Afryki

Teraz możesz utworzyć płatne konto Video Indexer regionach Korea Zachodnia i Północna Korea Północna.

## <a name="october-2020"></a>Październik 2020 r.

### <a name="animated-character-identification-improvements"></a>Ulepszenia identyfikacji znaków animowanych  

Video Indexer obsługuje wykrywanie, grupowanie i rozpoznawanie znaków w animowanej zawartości dzięki integracji z Cognitive Services custom vision. Dodaliśmy znaczne ulepszenia tego algorytmu sztucznej inteligencji do wykrywania i rozpoznawania znaków, ponieważ w wyniku znacznie zwiększono dokładność wglądu w szczegółowe dane i zidentyfikowane znaki.

### <a name="planned-video-indexer-website-authenticatication-changes"></a>Planowane Video Indexer uwierzytelniania witryny internetowej

Począwszy od 1 marca 2021 r., nie będzie już można zarejestrować się i zalogować się do portalu deweloperów witryny [Video Indexer](https://www.videoindexer.ai/) [](video-indexer-use-apis.md) przy użyciu usługi Facebook lub LinkedIn.

Możesz zarejestrować się i zalogować przy użyciu jednego z tych dostawców: Azure AD, Microsoft i Google.

> [!NOTE]
> Konta Video Indexer połączone z usługami LinkedIn i Facebook nie będą dostępne po 1 marca 2021 r. 
> 
> Należy [zaprosić do](invite-users.md) konta usługi Azure AD, Microsoft lub Google adres e-mail, do Video Indexer, aby nadal mieć dostęp. Możesz dodać dodatkowego właściciela obsługiwanych dostawców zgodnie z opisem w [zaproszeniu](invite-users.md). <br/>
> Alternatywnie możesz utworzyć płatne konto i przeprowadzić migrację danych.

## <a name="august-2020"></a>Sierpień 2020 r.

### <a name="mobile-design-for-the-video-indexer-website"></a>Projektowanie aplikacji mobilnych dla witryny Video Indexer internetowej

Środowisko Video Indexer obsługuje teraz urządzenia przenośne. Środowisko użytkownika reaguje na dostosowanie do rozmiaru ekranu mobilnego (z wyjątkiem interfejsów użytkownika dostosowywania). 

### <a name="accessibility-improvements-and-bug-fixes"></a>Ulepszenia ułatwień dostępu i poprawki błędów 

W ramach wytycznych dotyczących ułatwień dostępu do zawartości internetowej WCAG środowisko witryny Video Indexer sieci Web jest dostosowane do klasy C w ramach standardów ułatwień dostępu firmy Microsoft. Rozwiązano kilka usterek i ulepszeń związanych z nawigacją za pomocą klawiatury, dostępem programowym i czytnikiem ekranu. 

## <a name="july-2020"></a>Lipiec 2020 r.

### <a name="ga-for-multi-language-identification"></a>Ga dla identyfikacji w wielu językach

Identyfikacja wielu języków jest przenoszony z wersji zapoznawczej do wersji zapoznawczej i gotowa do wydajnego użycia.

Przejście z wersji zapoznawczej do wersji ogólnie dostępnej nie ma wpływu na ceny.

### <a name="video-indexer-website-improvements"></a>Video Indexer witryny internetowej

#### <a name="adjustments-in-the-video-gallery"></a>Korekty w galerii wideo

Dodano nowy pasek wyszukiwania dla funkcji głębokiego wyszukiwania szczegółowych informacji z dodatkowymi możliwościami filtrowania. Wyniki wyszukiwania również zostały ulepszone.

Nowy widok listy z możliwością sortowania archiwum wideo i zarządzania nim przy użyciu wielu plików.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>Nowy panel ułatwiający wybór i konfigurację

Dodano panel boczny ułatwiający wybór i konfigurację użytkownika, co umożliwia proste i szybkie tworzenie i udostępnianie kont, a także konfigurowanie ustawień.

Panel boczny jest również używany na potrzeby preferencji użytkownika i pomocy.

## <a name="june-2020"></a>Czerwiec 2020 r.

### <a name="search-by-topics"></a>Wyszukiwanie według tematów

Teraz możesz używać interfejsu API wyszukiwania do wyszukiwania wideo z określonymi tematami (tylko interfejs API).

Tematy są dodawane jako część `textScope` parametru (parametr opcjonalny). Aby uzyskać [szczegółowe informacje,](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos) zobacz API (Interfejs API).  

### <a name="labels-enhancement"></a>Ulepszenie etykiet

Tager etykiet został uaktualniony i teraz zawiera więcej etykiet wizualnych, które można zidentyfikować.

## <a name="may-2020"></a>Maj 2020 r.

### <a name="video-indexer-deployed-in-the-east-us"></a>Video Indexer wdrożona w regionach Wschodnie usa

Teraz możesz utworzyć płatne konto Video Indexer regionie Wschodnie usa.
 
### <a name="video-indexer-url"></a>Video Indexer URL

Video Indexer regionalne punkty końcowe były ujednolicone, aby rozpoczynały się tylko od www. Element akcji nie jest wymagany.

Od teraz możesz sprawdzić, www.videoindexer.ai jest to osadzanie widżetów, czy logowanie się do Video Indexer internetowych.

Ponadto wus.videoindexer.ai zostanie przekierowany do www. Więcej informacji jest dostępnych w te [Video Indexer osadzania w aplikacjach.](video-indexer-embed-widgets.md)

## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="new-widget-parameters-capabilities"></a>Nowe możliwości parametrów widżetu

Widżet **Szczegółowe informacje** zawiera nowe parametry: i `language` `control` .

Widżet **Player** (Odtwarzacz) ma `locale` nowy parametr. Parametry `locale` i `language` sterują językiem odtwarzacza.

Aby uzyskać więcej informacji, zobacz [sekcję typy widżetów.](video-indexer-embed-widgets.md#widget-types) 

### <a name="new-player-skin"></a>Nowy wygląd odtwarzacza

Nowa pielęgnacja odtwarzacza została uruchomiona ze zaktualizowanym projektem.

### <a name="prepare-for-upcoming-changes"></a>Przygotowanie do nadchodzących zmian

* Obecnie następujące interfejsy API zwracają obiekt konta:

    * [Tworzenie konta płatnego](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Get-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Get-Accounts-Authorization](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [Get-Accounts-With-Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    Obiekt Account zawiera pole , które wskazują lokalizację witryny `Url` Video Indexer [internetowej](https://www.videoindexer.ai/).
W przypadku płatnych kont pole aktualnie prowadzi do wewnętrznego adresu `Url` URL, a nie do publicznej witryny internetowej.
W najbliższych tygodniach zmienimy ją i zwrócimy adres URL witryny [Video Indexer](https://www.videoindexer.ai/) dla wszystkich kont (wersja próbna i płatna).

    Nie używaj wewnętrznych adresów URL. Powinno się używać Video Indexer [interfejsów API](https://api-portal.videoindexer.ai/).
* Jeśli osadzasz adresy URL usługi Video Indexer w aplikacjach i adresy URL nie wskazują witryny sieci Web usługi Video Indexer ani punktu końcowego interfejsu API usługi [Video Indexer](https://www.videoindexer.ai/) ( ), ale zamiast na regionalny punkt końcowy (na przykład ), ponownie generuj adresy `https://api.videoindexer.ai` `https://wus2.videoindexer.ai` URL.

   Można to zrobić za pomocą jednego z tych kolwiek:

    * Zamienianie adresu URL na adres URL, który Video Indexer interfejsów API widżetu (na przykład widżet [szczegółowych informacji](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget))
    * Wygenerowanie nowego osadzonego adresu URL przy użyciu witryny Video Indexer sieci Web:
         
         Naciśnij **klawisz Play,** aby uzyskać dostęp do strony **&lt; / &gt;** filmu wideo> kliknij przycisk Osadź> skopiuj adres URL do aplikacji:
   
    Regionalne adresy URL nie są obsługiwane i zostaną zablokowane w najbliższych tygodniach.

## <a name="january-2020"></a>Styczeń 2020 r.
 
### <a name="custom-language-support-for-additional-languages"></a>Obsługa języków niestandardowych dla dodatkowych języków

Video Indexer obsługuje teraz niestandardowe modele językowe dla `ar-SY` `en-UK` , i `en-AU` (tylko interfejs API).
 
### <a name="delete-account-timeframe-action-update"></a>Usuwanie aktualizacji akcji ram czasowych konta

Akcja Usuń konto powoduje teraz usunięcie konta w ciągu 90 dni zamiast 48 godzin.
 
### <a name="new-video-indexer-github-repository"></a>Nowe Video Indexer GitHub

Dostępna jest Video Indexer GitHub z różnymi projektami, przewodniki z wprowadzeniem i przykłady kodu: https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Aktualizacja programu Swagger

Video Indexer ujednolicone **uwierzytelnianie** i **operacje** w jedną [Video Indexer OpenAPI Specification (Swagger).](https://api-portal.videoindexer.ai/api-details#api=Operations&operation) Deweloperzy mogą znaleźć interfejsy API w [Video Indexer Portal deweloperów.](https://api-portal.videoindexer.ai/)

## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="update-transcript-with-the-new-api"></a>Aktualizowanie transkrypcji przy użyciu nowego interfejsu API

Zaktualizuj określoną sekcję w transkrypcji przy użyciu interfejsu API [Update-Video-Index.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index)

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Naprawianie konfiguracji konta z Video Indexer portal

Teraz możesz zaktualizować konfigurację Media Services, aby samodzielnie rozwiązać problemy, takie jak: 

* nieprawidłowy Azure Media Services zasobu
* zmiany hasła
* Media Services zasoby zostały przeniesione między subskrypcjami  

Aby naprawić konfigurację konta, w portalu Video Indexer przejdź do karty Ustawienia > konto (jako właściciel).

### <a name="configure-the-custom-vision-account"></a>Konfigurowanie konta usługi Custom Vision

Skonfiguruj konto usługi Custom Vision na płatnych kontach przy użyciu Video Indexer portal (wcześniej było to obsługiwane tylko przez interfejs API). Aby to zrobić, zaloguj się do portalu Video Indexer, wybierz pozycję Dostosowywanie modelu i > animowane znaki > Konfiguruj. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Sceny, zrzuty ekranu i ramki kluczowe — teraz w jednym okienku szczegółowych informacji

Sceny, zrzuty i ramki kluczowe są teraz scalane w jeden wgląd w informacje, aby ułatwić korzystanie z nich i nawigację. Po wybraniu żądanej sceny można zobaczyć, z których ujęć i ramek kluczowych składa się. 

### <a name="notification-about-a-long-video-name"></a>Powiadomienie o długiej nazwie filmu wideo

Jeśli nazwa wideo jest dłuższa niż 80 znaków, Video Indexer opisowy błąd podczas przekazywania.

### <a name="streaming-endpoint-is-disabled-notification"></a>Powiadomienie o wyłączeniu punktu końcowego przesyłania strumieniowego

Gdy punkt końcowy przesyłania strumieniowego jest wyłączony, Video Indexer na stronie odtwarzacza będzie wyświetlany opisowy błąd.

### <a name="error-handling-improvement"></a>Ulepszenie obsługi błędów

Kod stanu 409 będzie teraz [](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) zwracany z [](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) interfejsów API ponownego indeksowania wideo i aktualizowania indeksu wideo w przypadku, gdy wideo jest aktywnie indeksowane, aby zapobiec zastępowaniu bieżących zmian ponownego indeksowania przez przypadek.

## <a name="november-2019"></a>Listopad 2019 r.
 
* Obsługa modeli języków niestandardowych w języku koreańskim

    Usługa Video Indexer obsługuje teraz niestandardowe modele językowe w języku koreańskim ( `ko-KR` ) zarówno w interfejsie API, jak i w portalu. 
* Nowe języki obsługiwane dla funkcji mowy na tekst (STT)

    Video Indexer API obsługują teraz STT w arabskim Altantine (ar-SY), dialekcie Angielskie Zjednoczone Królestwo (en-GB) i angielskim dialekcie australijskim (en-AU).
    
    W przypadku przekazywania wideo zastąpiliśmy zh-HANS do zh-CN, oba są obsługiwane, ale zalecane i dokładniejsze zh-CN.
    
## <a name="october-2019"></a>Październik 2019 r.
 
* Wyszukiwanie animowanych znaków w galerii

    Podczas indeksowania znaków animowanych można teraz wyszukiwać je w galerii wideo konta. Aby uzyskać więcej informacji, zobacz [Rozpoznawanie znaków animowanych.](animated-characters-recognition.md)

## <a name="september-2019"></a>Wrzesień 2019 r.
 
Wiele postępów ogłoszonych na IBC 2019:
 
* Rozpoznawanie znaków animowanych (publiczna wersja zapoznawcza)

    Możliwość wykrywania rozpoznawania znaków w animowanej zawartości za pomocą integracji z usługą Custom Vision. Aby uzyskać więcej informacji, zobacz [Wykrywanie znaków animowanych.](animated-characters-recognition.md)
* Identyfikacja wielu języków (publiczna wersja zapoznawcza)

    Wykrywanie segmentów w wielu językach w ścieżkach dźwiękowych i tworzenie wielojęzycznej transkrypcji na ich podstawie. Początkowa obsługa: angielski, hiszpański, niemiecki i francuski. Aby uzyskać więcej informacji, zobacz [Automatyczne identyfikowanie i transkrybowanie zawartości w wielu językach](multi-language-identification-transcription.md).
* Wyodrębnianie nazwanych jednostek dla osób i lokalizacji

    Wyodrębnia marki, lokalizacje i osoby z mowy i tekstu wizualnego za pośrednictwem przetwarzania języka naturalnego (NLP).
* Klasyfikacja typów zrzutów redakcyjnych

    Tagowanie zrzutów za pomocą typów redakcyjnych, takich jak zbliżenie, średni rzut, dwa rzuty, wewnątrz, na zewnątrz itp. Aby uzyskać więcej informacji, zobacz [Wykrywanie typu zdjęcia redakcyjnego](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Ulepszenie wnioskowania tematu — teraz obejmujące poziom 2
    
    Model wnioskowania tematu obsługuje teraz bardziej szczegółowość taksonomii IPTC. Przeczytaj szczegółowe informacje na [Azure Media Services nowych innowacjach opartych na AI.](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)

## <a name="august-2019"></a>Sierpień 2019 r.
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer wdrożone w Południowe Zjednoczone Królestwo

Teraz możesz utworzyć płatne konto Video Indexer w regionie Południowe Zjednoczone Królestwo.

### <a name="new-editorial-shot-type-insights-available"></a>Dostępne nowe szczegółowe informacje o typie zrzutu redakcyjnego

Nowe tagi dodane do ujęć wideo zapewniają redakcyjne "typy zrzutów", które identyfikują je za pomocą typowych fraz redakcyjnych używanych w przepływie pracy tworzenia zawartości, takich jak skrajne zbliżenie, zbliżenie, szerokie, średnie, dwa zdjęcia, na zewnątrz, wewnątrz, na lewą twarz i prawą twarz (dostępne w danych JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Dostępne są nowe wyodrębnianie jednostek Osoby i lokalizacje

Video Indexer identyfikuje nazwane lokalizacje i osoby za pośrednictwem przetwarzania języka naturalnego (NLP) na podstawie funkcji OCR i transkrypcji wideo. Video Indexer algorytm uczenia maszynowego rozpoznaje, kiedy w wideo są wywoływane określone lokalizacje (na przykład tower Eiffla) lub osoby (na przykład John Doe).

### <a name="keyframes-extraction-in-native-resolution"></a>Wyodrębnianie ramek kluczowych w rozdzielczości natywnej

Ramki kluczowe wyodrębnione przez Video Indexer są dostępne w oryginalnej rozdzielczości filmu wideo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>Ga for training custom face models from images (Ga for training custom face models from images ) (Ga for training custom face models from

Trenowania twarzy z obrazów przeniesionych z trybu podglądu do wersji zapoznawczej (dostępne za pośrednictwem interfejsu API i portalu).

> [!NOTE]
> Przejście z wersji zapoznawczej do wersji ogólnie dostępnej nie ma wpływu na ceny.

### <a name="hide-gallery-toggle-option"></a>Ukryj opcję przełączania galerii

Użytkownik może ukryć kartę galerii w portalu (podobnie jak w przypadku ukrywania karty przykładów).
 
### <a name="maximum-url-size-increased"></a>Zwiększenie maksymalnego rozmiaru adresu URL

Obsługa ciągu zapytania adresu URL 4096 (zamiast 2048) podczas indeksowania wideo.
 
### <a name="support-for-multi-lingual-projects"></a>Obsługa projektów wielojęzykowych

Projekty można teraz tworzyć na podstawie wideo indeksowanych w różnych językach (tylko interfejs API).

## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="editor-as-a-widget"></a>Edytor jako widżet

Edytor Video Indexer AI jest teraz dostępny jako widżet do osadzania w aplikacjach klienta.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aktualizowanie niestandardowego modelu językowego z pliku napisów z portalu

Klienci mogą udostępnić formaty plików VTT, SRT i TTML jako dane wejściowe dla modeli językowych na stronie dostosowywania portalu.

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer wdrożone w Japonii Wschodniej

Teraz możesz utworzyć konto Video Indexer w regionie Japonia Wschodnia.

### <a name="create-and-repair-account-api-preview"></a>Interfejs API tworzenia i naprawiania konta (wersja zapoznawcza)

Dodano nowy interfejs API, który umożliwia [zaktualizowanie punktu końcowego](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)lub klucza połączenia usługi Azure Media Service.

### <a name="improve-error-handling-on-upload"></a>Ulepszanie obsługi błędów podczas przekazywania 

Komunikat opisowy jest zwracany w przypadku błędnej konfiguracji konta Azure Media Services danych.

### <a name="player-timeline-keyframes-preview"></a>Oś czasu odtwarzacza (wersja zapoznawcza) 

Teraz możesz wyświetlić podgląd obrazu za każdym razem na osi czasu odtwarzacza.

### <a name="editor-semi-select"></a>Wybieranie częściowo edytora

Teraz możesz wyświetlić podgląd wszystkich szczegółowych informacji wybranych w wyniku wybrania określonego czasu wglądu w szczegółowe dane w edytorze.

## <a name="may-2019"></a>Maj 2019 r.

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualizowanie niestandardowego modelu językowego z pliku napisów

[Tworzenie niestandardowego modelu językowego](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) i aktualizowanie [niestandardowych](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) modeli językowych interfejsy API obsługują teraz formaty plików VTT, SRT i TTML jako dane wejściowe dla modeli językowych.

Podczas [wywoływania interfejsu API aktualizacji transkrypcji wideo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript)transkrypcja jest dodawana automatycznie. Model trenowania skojarzony z wideo jest również aktualizowany automatycznie. Aby uzyskać informacje na temat dostosowywania i trenowania modeli językowych, zobacz Dostosowywanie modelu językowego [za pomocą Video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nowe formaty transkrypcji pobierania — TXT i CSV

Oprócz formatu napisów, który jest już obsługiwany (SRT, VTT i TTML), program Video Indexer teraz obsługuje pobieranie transkrypcji w formatach TXT i CSV.

## <a name="next-steps"></a>Następne kroki

[Omówienie](video-indexer-overview.md)
