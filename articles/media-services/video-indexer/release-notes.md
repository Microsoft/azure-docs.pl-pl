---
title: Informacje o wydaniu indeksatora wideo usługi Azure Media Services | Dokumenty firmy Microsoft
description: Aby być na bieżąco z najnowszymi rozwiązaniami, w tym artykule przedstawiono najnowsze aktualizacje indeksatora wideo usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: f7ce5f5086bcf2d577fb998df307ee684d536c15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870098"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Informacje o wydaniu indeksatora wideo usługi Azure Media Services

>Otrzymuj powiadomienia o tym, kiedy można ponownie odwiedzić `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` tę stronę, aby uzyskać aktualizacje, kopiując i wklejając ten adres URL: do czytnika kanałów RSS.

Aby być na bieżąco z najnowszymi wydarzeniami, w tym artykule znajdziesz informacje na temat:

* Najnowsze wydania
* Znane problemy
* Poprawki błędów
* Funkcje uznane za przestarzałe

## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="new-widget-parameters-capabilities"></a>Nowe funkcje parametrów widżetu

Widżet **Insights** `language` zawiera `control`nowe parametry: i .

Widżet **Odtwarzacz** ma nowy `locale` parametr. Zarówno `locale` `language` parametry, jak i parametry kontrolują język gracza.

Aby uzyskać więcej informacji, zobacz sekcję [typy widżetów.](video-indexer-embed-widgets.md#widget-types) 

### <a name="new-player-skin"></a>Nowa skórka gracza

Nowa skórka gracza z zaktualizowanym designem.

## <a name="january-2020"></a>Styczeń 2020 r.
 
### <a name="custom-language-support-for-additional-languages"></a>Obsługa języków niestandardowych dla dodatkowych języków

Indeksator wideo obsługuje teraz `ar-SY` niestandardowe modele językowe dla programów `en-UK`, i `en-AU` (tylko interfejs API).
 
### <a name="delete-account-timeframe-action-update"></a>Usuń aktualizację akcji ramy czasowe konta

Akcja Usuń konto usuwa teraz konto w ciągu 90 dni zamiast 48 godzin.
 
### <a name="new-video-indexer-github-repository"></a>Nowe repozytorium GitHub indeksatora wideo

Nowy Video Indexer GitHub z różnych projektów, wprowadzenie przewodników i przykładów kodu jest teraz dostępna:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Aktualizacja Swagger

Indeksator wideo ujednolicił **uwierzytelnianie** i **operacje** w jednej [specyfikacji OpenAPI indeksatora wideo (swagger).](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson) Deweloperzy mogą znaleźć interfejsy API w [portalu deweloperskim indeksatora wideo](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="update-transcript-with-the-new-api"></a>Aktualizowanie transkrypcji za pomocą nowego interfejsu API

Zaktualizuj określoną sekcję w transkrypcji przy użyciu interfejsu API [update-video-index.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update)

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Napraw konfigurację konta z portalu indeksatora wideo

Teraz można zaktualizować konfigurację połączenia usługi Media Services, aby samodzielnie pomóc w rozwiązywaniu takich problemów, jak: 

* niepoprawny zasób usługi Azure Media Services
* zmiany hasła
* Zasoby usługi Media Services zostały przeniesione między subskrypcjami  

Aby naprawić konfigurację konta, w portalu indeksatora wideo przejdź do pozycji Ustawienia > konto (jako właściciel).

### <a name="configure-the-custom-vision-account"></a>Konfigurowanie niestandardowego konta wizji

Skonfiguruj niestandardowe konto wizji na płatnych kontach za pomocą portalu indeksatora wideo (wcześniej było to obsługiwane tylko przez interfejs API). Aby to zrobić, zaloguj się do portalu indeksatora wideo, wybierz pozycję Dostosowanie modelu > animowane znaki > konfigurowanie. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Sceny, ujęcia i klatki kluczowe – teraz w jednym okienku wglądu

Sceny, ujęcia i klatki kluczowe są teraz łączone w jeden wgląd w łatwiejszy sposób konsumpcji i nawigacji. Po wybraniu żądanej sceny można zobaczyć, jakie ujęcia i klatki kluczowe składają się z. 

### <a name="notification-about-a-long-video-name"></a>Powiadomienie o długiej nazwie filmu

Gdy nazwa wideo jest dłuższa niż 80 znaków, indeksator wideo pokazuje błąd opisowy podczas przekazywania.

### <a name="streaming-endpoint-is-disabled-notification"></a>Punkt końcowy przesyłania strumieniowego jest wyłączony powiadomienie

Gdy punkt końcowy przesyłania strumieniowego jest wyłączony, indeksator wideo wyświetli błąd opisowy na stronie odtwarzacza.

### <a name="error-handling-improvement"></a>Poprawa obsługi błędów

Kod stanu 409 zostanie teraz zwrócony z interfejsów API [ponownego indeksu wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) i aktualizacji [indeksu wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) w przypadku, gdy film wideo jest aktywnie indeksowany, aby zapobiec przypadkowemu zastąpieniu bieżących zmian ponownego indeksowania.

## <a name="november-2019"></a>Listopad 2019 r.
 
* Obsługa koreańskich modeli języków niestandardowych

    Indeksator wideo obsługuje teraz niestandardowe`ko-KR`modele językowe w języku koreańskim ( ) zarówno w interfejsie API, jak i portalu. 
* Nowe języki obsługiwane dla mowy na tekst (STT)

    Interfejsy API indeksatora wideo obsługują teraz technologię STT w języku angielskim Levantine (ar-SY), angielskim dialekcie brytyjskim (en-GB) i angielskim dialekcie australijskim (en-AU).
    
    Do przesyłania wideo, zastąpiliśmy zh-HANS do zh-CN, oba są obsługiwane, ale zh-CN jest zalecane i bardziej dokładne.
    
## <a name="october-2019"></a>Październik 2019 r.
 
* Wyszukiwanie animowanych postaci w galerii

    Podczas indeksowania animowanych postaci można je teraz wyszukiwać w rozkwicie wideo konta. Aby uzyskać więcej informacji, zobacz [Rozpoznawanie animowanych znaków](animated-characters-recognition.md).

## <a name="september-2019"></a>Wrzesień 2019 r.
 
Wiele osiągnięć zapowiedzianych na IBC 2019:
 
* Animowane rozpoznawanie znaków (podgląd publiczny)

    Możliwość wykrywania grupowej reklamy rozpoznaje postacie w animowanych treściach dzięki integracji z niestandardową wizją. Aby uzyskać więcej informacji, zobacz [Animowane wykrywanie znaków](animated-characters-recognition.md).
* Identyfikacja w wielu językach (publiczna wersja zapoznawcza)

    Wykrywaj segmenty w wielu językach ścieżki dźwiękowej i twórz na ich podstawie wielojęzyczny zapis. Początkowe wsparcie: angielski, hiszpański, niemiecki i francuski. Aby uzyskać więcej informacji, zobacz [Automatyczne identyfikowanie i transkrybowanie zawartości wielojęzycznej](multi-language-identification-transcription.md).
* Wyodrębnianie nazwanych jednostek dla osób i lokalizacji

    Wyodrębnia marki, lokalizacje i osoby z mowy i tekstu wizualnego za pomocą przetwarzania języka naturalnego (NLP).
* Klasyfikacja typów strzałów redakcyjnych

    Oznaczanie zdjęć z typów redakcyjnych, takich jak zbliżenie, średni strzał, dwa strzały, wewnątrz, na zewnątrz itp. Aby uzyskać więcej informacji, zobacz [Wykrywanie typu zdjęcia redakcyjnego](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Rozszerzenie tematu - teraz obejmujące poziom 2
    
    Model wnioskowania tematu obsługuje teraz głębszą szczegółowość taksonomii IPTC. Przeczytaj szczegółowe informacje na temat nowych innowacji opartych na [sztucznej inteligencji w usłudze Azure Media Services.](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)

## <a name="august-2019"></a>Sierpień 2019 r.
 
### <a name="video-indexer-deployed-in-uk-south"></a>Indeksator wideo wdrożony w Wielkiej Brytanii Południowej

Teraz możesz utworzyć płatne konto indeksatora wideo w regionie Południowej Wielkiej Brytanii.

### <a name="new-editorial-shot-type-insights-available"></a>Dostępne nowe statystyki typu strzału redakcyjnego

Nowe tagi dodane do zdjęć wideo zapewnia redakcji "typy strzałów", aby zidentyfikować je z typowych zwrotów redakcyjnych używanych w przepływie pracy tworzenia treści, takich jak: ekstremalne zbliżenie, zbliżenie, szeroki, średni, dwa strzały, na zewnątrz, wewnątrz, lewej twarzy i prawej twarzy (Dostępne w JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Dostępne wyodrębnianie jednostek Nowe osoby i lokalizacje

Indeksator wideo identyfikuje nazwane lokalizacje i osoby za pomocą przetwarzania języka naturalnego (NLP) z ocr wideo i transkrypcji. Indeksator wideo używa algorytmu uczenia maszynowego do rozpoznawania, kiedy określone lokalizacje (na przykład Wieża Eiffla) lub osoby (na przykład John Doe) są wywoływane w filmie.

### <a name="keyframes-extraction-in-native-resolution"></a>Wyodrębnianie klatek kluczowych w rozdzielczości natywnej

Klatki kluczowe wyodrębnione przez video indexer są dostępne w oryginalnej rozdzielczości wideo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA do szkolenia niestandardowych modeli twarzy z obrazów

Twarze szkoleniowe z obrazów przeniesione z trybu podglądu do ga (dostępne za pośrednictwem interfejsu API i portalu).

> [!NOTE]
> Nie ma wpływu na ceny związane z przejściem "Podgląd do GA".

### <a name="hide-gallery-toggle-option"></a>Opcja przełączania Ukryj galerię

Użytkownik może ukryć kartę galerii z portalu (podobnie jak ukrywanie karty przykłady).
 
### <a name="maximum-url-size-increased"></a>Zwiększono maksymalny rozmiar adresu URL

Obsługa ciągu zapytania adresu URL 4096 (zamiast 2048) podczas indeksowania wideo.
 
### <a name="support-for-multi-lingual-projects"></a>Wsparcie dla projektów wielojęzycznych

Projekty można teraz tworzyć na podstawie filmów indeksowanych w różnych językach (tylko interfejs API).

## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="editor-as-a-widget"></a>Edytor jako widżet

Edytor AI indeksatora wideo jest teraz dostępny jako widżet, który ma być osadzony w aplikacjach klientów.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aktualizowanie niestandardowego modelu języka z pliku podpisów kodowych z portalu

Klienci mogą udostępniać formaty plików VTT, SRT i TTML jako dane wejściowe dla modeli językowych na stronie dostosowywania portalu.

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="video-indexer-deployed-to-japan-east"></a>Indeksator wideo wdrożony na wschodzie Japonii

Teraz można utworzyć płatne konto indeksatora wideo w regionie Japonia Wschodnia.

### <a name="create-and-repair-account-api-preview"></a>Interfejs API tworzenia i naprawy konta (wersja zapoznawcza)

Dodano nowy interfejs API, który umożliwia [aktualizację punktu końcowego lub klucza połączenia usługi Azure Media Service.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)

### <a name="improve-error-handling-on-upload"></a>Usprawnij obsługę błędów podczas przekazywania 

Opisowy komunikat jest zwracany w przypadku błędnej konfiguracji podstawowego konta usługi Azure Media Services.

### <a name="player-timeline-keyframes-preview"></a>Podgląd klatek kluczowych na osi czasu gracza 

Podgląd obrazu za każdym razem na osi czasu gracza.

### <a name="editor-semi-select"></a>Edytor półwyborczy

Teraz możesz zobaczyć podgląd wszystkich szczegółowych informacji, które są wybrane w wyniku wyboru określonego przedziału czasowego szczegółowych w edytorze.

## <a name="may-2019"></a>Maj 2019 r.

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualizowanie niestandardowego modelu języka z pliku podpisów kodowych

[Tworzenie niestandardowego modelu języka](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) i [aktualizowanie niestandardowych modeli językowych interfejsów](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API obsługuje teraz formaty plików VTT, SRT i TTML jako dane wejściowe dla modeli języków.

Podczas [wywoływania interfejsu API transkrypcji aktualizacji](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)wideo transkrypcja transkrypcji transkrypcja jest dodawana automatycznie. Model szkolenia skojarzony z filmem jest również aktualizowany automatycznie. Aby uzyskać informacje na temat dostosowywania i szkolenia modeli języków, zobacz [Dostosowywanie modelu języka za pomocą indeksatora wideo](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nowe formaty transkrypcji pobierania – TXT i CSV

Oprócz formatu podpisów kodowanych już obsługiwanych (SRT, VTT i TTML), video indexer obsługuje teraz pobieranie transkrypcji w formatach TXT i CSV.

## <a name="next-steps"></a>Następne kroki

[Omówienie](video-indexer-overview.md)
