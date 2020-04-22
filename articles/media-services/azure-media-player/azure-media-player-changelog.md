---
title: Dziennik zmian w programie Azure Media Player
description: Dziennik zmian programu Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 15f8a3ac8c2777b3a878de92db495e559f64ad20
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726550"
---
# <a name="changelog"></a>Changelog #

## <a name="224-official-update-february-22-2019"></a>2.2.4 (Oficjalna aktualizacja 22 lutego 2019 r.) ##

### <a name="bug-fixes-224"></a>Poprawki błędów 2.2.4 ###

- [Poprawka błędu] [AMP] [Dostępność] Usunięto osiągnącą kartę fantomu, gdy pojawi się ekran błędu
- [Poprawka błędu] [AMP] Naprawiono skrót klawiszowy "M" dla IE11 i Edge
- [Poprawka błędu] [AMP] Poprawiono wyjątek dla podpisów CEA708
- [Poprawka błędu] [AMP] Naprawiono problem z zawieszaniem wideo w przeglądarce Edge

### <a name="changes-224"></a>Zmiany 2.2.4 ###

- [Zmień] [AMP] Gdy występuje błąd odszyfrowywania fragmentu, gracz ponawia próby bieżące i różne fragmenty, aby odzyskać odtwarzanie
- [Zmień] [AMP] Zwiększenie tolerancji AMP wobec nakładających się fragmentów wideo lub audio

## <a name="223-official-update-january-9-2019"></a>2.2.3 (Oficjalna aktualizacja 9 stycznia 2019) ##

### <a name="features"></a>Funkcje ###

- [Funkcja] [HLS] Dodano menu ścieżki dźwiękowej do odtwarzania Safari HLS

### <a name="bug-fixes-223"></a>Poprawki błędów 2.2.3 ###

- [Poprawka błędu] [AMP] [Dostępność] Podczas odtwarzania transmisji na żywo nie można wybrać przycisku "na żywo" za pomocą klawiatury
- [Poprawka błędu] [AMP] Naprawiono błędy fałszywie alarmowe 0x0400003 z powodu nieudanego testu MSE
- [Poprawka błędu] [AMP] Naprawiono błąd, który powodował, że film mógł się zawieszać podczas rozpoczynania transmisji na żywo.

### <a name="changes-223"></a>Zmiany 2.2.3 ###

- [Zmień] [AMP] Dodano więcej informacji w dzienniku, aby umożliwić lepszą diagnostykę
- [Zmień] [AMP] Gdy więcej niż jedna szybkość transmisji bitów jest dostępna w tej samej rozdzielczości ekranu, wszystkie szybkości transmisji bitów są dostępne do wyboru

## <a name="222-official-update"></a>2.2.2 (Oficjalna aktualizacja) ##

### <a name="bug-fixes-222"></a>Poprawki błędów 2.2.2 ###

- [Poprawka błędu] [AMP] Gdy gracz napotka przejściową awarię sieci, natychmiast przestaje odtwarzać
- [Poprawka błędu] [AMP] [Dostępność] Okno dialogowe błędu nie jest dostępne za pomocą klawiatury
- [Poprawka błędu] [AMP] Nieskończony pokrętło wyświetlane podczas odtwarzania dźwięku tylko zasób zamiast nieobsługiwał błąd

### <a name="changes-222"></a>Zmiany 2.2.2 ###

- [Zmień] [AMP] dodał zlokalizowane ciągi interfejsu użytkownika reklamy

## <a name="221-official-update"></a>2.2.1 (Oficjalna aktualizacja) ##

### <a name="features-221"></a>Cechy 2.2.1 ###

- [Funkcja] [CMAF] Dodano obsługę HLS CMAF

### <a name="bug-fixes"></a>Poprawki błędów ###

- [Poprawka błędu] [AMP] nierozliczone czasomierze w ponawianiu logiki dającej błędy odtwarzania
- [Poprawka błędu] [AMP] [Firefox] zakończył zdarzenie nie jest uruchamiany w Firefoksie i Chrome po zatrzymaniu programu na żywo
- [Poprawka błędu] [AMP] Formanty wyświetlane po setsource, nawet wtedy, gdy formanty są ustawione na false w opcjach odtwarzacza

### <a name="changes"></a>Zmiany ###

- [Zmień] [Podpisy na żywo] Zmieniono nazwę interfejsu API dla podpisów CEA z 608 na 708. Aby uzyskać więcej informacji, zobacz [Ustawienia podpisów CEA708](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0 (Oficjalne wydanie) ##

### <a name="features-220"></a>Cechy 2.2.0 ###

- [Funkcja] [Azurehtml5JS] [Flash] [LiveCaptions] Obsługa podpisów CEA 708 w technologii Azurehtml5JS i FlashSS dla zawartości jasnych i AES.

### <a name="bug-fixes-220"></a>Poprawki błędów 2.2.0 ###

- [Poprawka błędu] Wykrywanie wersji flash nie działa w Chrome / Edge

### <a name="changes-220"></a>Zmiany 2.2.0 ###

- [Zmień] [AMP] [Heurystyka] Zmieniono nazwę profilu heurystycznego z Szybki start na LowLatency
- [Zmień] [Flash] Zmiana w programie Flash player w celu wykrycia wersji, aby umożliwić odtwarzanie zawartości AES za pomocą nowej aktualizacji Adobe Flash.

## <a name="219-official-hotfix"></a>2.1.9 (Oficjalna poprawka) ##

### <a name="bug-fixes-219"></a>Poprawki błędów 2.1.9 ###

- [Poprawka błędu] [Na żywo] Wyjątek występujący podczas przejścia strumieni na żywo do wideo na żądanie/archiwa na żywo

### <a name="changes-219"></a>Zmiany 2.1.9 ###

- [Zmień] [Flash] [AES] Zmodyfikowano logikę techniczną flash, aby nie używać sharedbytearrays do deszyfrowania AES, ponieważ firma Adobe zablokowała użycie od programu Flash 30. Należy pamiętać, że odtwarzanie będzie działać tylko wtedy, gdy Adobe wdroży nową wersję programu Flash z powodu błędu w wersji 30. Zobacz [znane problemy,](azure-media-player-known-issues.md) aby uzyskać więcej informacji

## <a name="218-official-update"></a>2.1.8 (Oficjalna aktualizacja) ##

### <a name="bug-fixes-218"></a>Poprawki błędów 2.1.8 ###

- [Poprawka błędu] Spinner od czasu do czasu nie pokazuje post szukać i pre-play
- [Poprawka błędu] Gracz nie uruchamia się wyciszony po włączeniu opcji wyciszenia
- [Poprawka błędu] Suwak głośności jest wyświetlany, gdy formanty są ustawione na wartość false
- [Poprawka błędu] Odtwarzanie od czasu do czasu powtarzane, gdy użytkownik przeskakuje do krawędzi na żywo
- [Poprawka błędu] [Firefox] Gracz od czasu do czasu zgłasza wyjątek JavaScript przy obciążeniu
- [Poprawka błędu] [Dostępność] Przycisk Odtwarzania/ Wstrzymaj/Głośność traci kontur ostrości po wybraniu za pomocą kontrolek klawiatury
- [Poprawka błędu] Naprawiono wyciek pamięci na odtwarzaczu jest usuwany
- [Poprawka błędu] Wywołanie src() po błędzie gracza nie resetuje źródła
- [Poprawka błędu] [Na żywo] AMP jest w stanie ciągłego ładowania, gdy użytkownik kliknie przycisk Na żywo po zakończeniu emisji
- [Poprawka błędu] [Chrome] Odtwarzacz zawiesza się, a odtwarzanie kończy się niepowodzeniem, gdy przeglądarka jest zminimalizowana do tła.

### <a name="changes-218"></a>Zmiany 2.1.8 ###

- [Zmień] Zaktualizowano errror 0x0600001, aby wyświetlać zawartość AES w programie Flash 30, ponieważ obecnie nie jest ona obsługiwana. Zobacz [znane problemy,](azure-media-player-known-issues.md) aby uzyskać więcej informacji
- [Zmień] Dodano dodatkowe ponownych prób dla scenariuszy na żywo, gdy żądania manifestu 404 lub zwraca puste manifesty.

## <a name="217-official-update"></a>2.1.7 (Oficjalna aktualizacja) ##

### <a name="features-217"></a>Cechy 2.1.7 ###

- [Funkcja] [AzureHtml5JS] Dodano opcję konfiguracji do opróżniania starych danych w buforze źródła multimediów

### <a name="bug-fixes-217"></a>Poprawki błędów 2.1.7 ###

- [Poprawka błędu] [Dostępność] [Czytnik ekranu] Usunięto pusty nagłówek, który gracz uwzględnił, gdy tytuł nie jest ustawiony
- [Poprawka błędu] [UWA] AMP zgłasza wyjątek podczas odtwarzania w uniwersalnej aplikacji systemu Windows
- [Poprawka błędu] [OSX] setActiveTextTrack() nie działa w przeglądarce Safari na OSx
- [Poprawka błędu] [Na żywo] Kliknięcie krawędzi na żywo po usunięciu i ponownym zainicjowaniu gracza daje wyjątek
- [Poprawka błędu] [Skórka] Czas bieżący skrócony dla niektórych zasobów
- [Poprawka błędu] Poprawka [DRM] dołączona do obsługi odtwarzania w przeglądarkach obsługujących wiele drm CENC

### <a name="changes-217"></a>Zmiany 2.1.7 ###

- [Zmień] [Przykłady] [Dostępność] Dodano znacznik języka do wszystkich próbek

## <a name="216-official-update"></a>2.1.6 (Oficjalna aktualizacja) ##

### <a name="bug-fixes-216"></a>Poprawki błędów 2.1.6 ###

- [Poprawka błędu] AMP wyświetla nieprawidłowy czas trwania określonego zasobu
- [Poprawka błędu] [FairPlay-HLS] Błędy fairplay nie propagujące do interfejsu użytkownika
- [Poprawka błędu] Niestandardowe właściwości heurystyczne ignorowane w AMP 2.1.5.

### <a name="changes-216"></a>Zmiany 2.1.6 ###

- [Zmień] [FairPlayDRM] Usunięto limit czasu zarówno dla żądania certyfikatu, jak i żądania licencji dla FairPlay, aby zachować parytet z implementacjami PlayReady i Widevine
- [Zmień] Różne ulepszenia heurystyczne w walce z rozmazanymi treściami

### <a name="features-216"></a>Cechy 2.1.6 ###

- [Funkcja] Dodano obsługę formatu mpd-time-cmaf

## <a name="215-official-hotfix"></a>2.1.5 (Oficjalna poprawka) ##

### <a name="bug-fixes-215"></a>Poprawki błędów 2.1.5 ###

- [Poprawka błędu] [Podpisy] Styl VTT nie jest poprawnie renderowany przez gracza
- [Poprawka błędu] [Dostępność] Przycisk Na żywo nie ma etykiety aria

## <a name="214-official-update"></a>2.1.4 (Oficjalna aktualizacja) ##

### <a name="bug-fixes-214"></a>Poprawki błędów 2.1.4 ###

- [Poprawka błędu] [Dostępność] [Ostrość] Użytkownicy nie mogą skupić się na niestandardowych przyciskach dodanych po prawej stronie przycisku pełnoekranowego na pasku sterowania
- [Poprawka błędu] [IE11] [Pasek głośności] Tabbing do głośności pop-up sprawia, że cały ekran wideo flash w IE11, podczas gdy w trybie pełnoekranowym
- [Poprawka błędu] [Skóra| Flush] Przestrzeń wyświetlana między pasem sterującym a wyskakującym pasem głośności
- [Poprawka błędu] [AMP] [Podpisy] Stare osadzone ścieżki nie są czyszczone po zmianie źródła na istniejącym odtwarzaczu
- [Poprawka błędu] [Dostępność] [Narrator] Czytnik ekranu niepoprawnie odczytuje regulację głośności
- [Poprawka błędu] [Flashss] Od czasu do czasu nie uruchamia się w wydarzeniu play
- [Poprawka błędu] [AMP] [Ostrość] Odtwarzanie/pauza wymaga dwóch kliknięć, gdy gracz ma ostrość i jest w trybie pełnoekranowym
- [Poprawka błędu] [AMP] [Skórka] Nieprawidłowy czas trwania wyświetlany na pasku postępu dla określonego zasobu
- [Poprawka błędu] [Ogłoszenia] [Ad Butler] Analizator VAST nie obsługuje pliku VAST, który nie ma zdarzenia postępu
- [Poprawka błędu] [SDN] [AMP 2.1.1] Naprawiono błąd, który powodował obsługę wtyczek Hive SDN
- [Poprawka błędu] [Dostępność] Narrator odczytuje "Przycisk wyciszania północy", gdy użytkownik ma nacisk na przycisk głośności

### <a name="changes-214"></a>Zmiany 2.1.4 ###

- [Zmień] [Dostępność] [Technologia wspomagana] Przyciski mają teraz aria-live nieruchomości w celu poprawy doświadczenia z technologią wspomagających
- [Zmień] [Dostępność] [Przycisk Głośności| Narrator]Poprawiono dostępność przycisku głośności, modyfikując funkcje tabulacji i zachowanie suwaka. Te zmiany ułatwiają użytkownikom klawiatury modyfikowanie głośności odtwarzacza
- [Zmień] Wydłużenie limitu czasu menu kontekstowego braku aktywności z 3 do 5 sekund
- [Zmień] [Dostępność] [Jasność] Poprawiono współczynnik kontrastu jasności w menu rozwijanych w ustawieniach podpisów

## <a name="213-official-update"></a>2.1.3 (Oficjalna aktualizacja) ##

### <a name="bug-fixes-213"></a>Poprawki błędów 2.1.3 ###

- [Poprawka błędu] [Wtyczki| Nakładka tytułowa] Wtyczka Nakładki tytułowej zgłasza wyjątki JS z AMP v2. X+
- [Poprawka błędu] Zdarzenie Zestaw źródła jest wysyłane do konsoli JavaScript nawet wtedy, gdy rejestrowanie jest wyłączone
- [Poprawka błędu] [Skórka] Wskazówki dotyczące czasu odtwarzacza są renderowane poza kontekstem gracza po najechaniu kursorem na pasek czasu zakończenia
- [Poprawka błędu] [Dostępność] [Czytnik ekranu] Narrator odczytuje "Punkt orientacyjny regionu" lub "Punkt orientacyjny regionu odtwarzacza wideo", gdy widz koncentruje się na odtwarzaczu
- [Poprawka błędu] [AMP] Nie można wyłączyć konspektu odtwarzacza za pośrednictwem CSS
- [Poprawka błędu] [Dostępność] Nie można skupić się na całym odtwarzaczu, gdy użytkownik jest w trybie pełnoekranowym
- [Poprawka błędu] [Skórka] [Na żywo] Skóra nie reaguje na zlokalizowany tekst LIVE w języku japońskim
- [Poprawka błędu] [Skórka] Czas trwania i bieżący czas są odcinane, gdy strumień > 60 min -[Bug Fix][iPhone| Live]player pokazuje tekst dla bieżącego czasu / czasu trwania na pasku sterowania
- [Poprawka błędu] [AMP] Wywoływanie heurystyki odtwarzacza interfejsów API daje wyjątki JavaScript
- [Poprawka błędu] [Natywny html5|iOS] Videotag właściwość "playsinline" nie propaguje do odtwarzacza
- [Poprawka błędu] [iOS|iframe] Odtwarzacz nie może wejść na pełny ekran na iPhonie, jeśli odtwarzacz jest załadowany w ramce iframe
- [Poprawka błędu] [AMP] [Heurystyka] AMP zawsze działa z profilem hybrydowym, niezależnie od opcji odtwarzacza
- [Poprawka błędu] [AMP| Win8.1]rzuca, gdy jest hostowany w aplikacji Win8.1 z webview

### <a name="changes-213"></a>Zmiany 2.1.3 ###

- [Zmień] [AMP] Dodano informacje o punkcie końcowym sieci CDN w zdarzeniu FragmentDownloadComplete
- [Zmień] [AMP] [Na żywo] Ulepszone i zoptymalizowane opóźnienie transmisji strumieniowej na żywo

## <a name="212-official-hotfix"></a>2.1.2 (Oficjalna poprawka) ##

### <a name="bug-fixes-212"></a>Poprawki błędów 2.1.2 ####

- [Poprawka błędu] [Dostępność] [Narrator systemu Windows] Narrator odczytuje "Progress midnight", gdy użytkownik ma kontekst paska postępu, a bieżący czas to 0:00
- [Poprawka błędu] Rozmiar logo [Skin] jest zakodowany na czas w kodzie JavaScript
- [Dostępność] [Skróty klawiszowe] Skróty klawiszowe nie są włączone po kliknięciu odtwarzacza.

### <a name="changes-212"></a>Zmiany 2.1.2 ####

- [Zmień] [Rejestrowanie] Zaloguj adres URL manifestu, gdy odtwarzacz nie może załadować manifestu

### <a name="features-212"></a>Cechy 2.1.2 ###

- [Zmień] [Wydajność] [Optymalizacja] Poprawiono czas ładowania i uruchamiania graczy

## <a name="211-official-update"></a>2.1.1 (Oficjalna aktualizacja) ##

### <a name="bug-fixes-211"></a>Poprawki błędów 2.1.1 ####

- [Poprawka błędu] [iOS] Ustawienie autoodtwarzania na false daje nieskończony pokrętło w Safari dla systemu iOS
- [Poprawka błędu] Poszukiwanie czasu większego niż czas trwania zawartości daje nieskończoną pokrętło
- [Poprawka błędu] Skróty klawiszowe wymagają wielu kart klawiatury, aby uzyskać kontekst odtwarzacza do pracy
- [Poprawka błędu] Wideo zawiesza się na kilka sekund po zmianach rozmiaru odtwarzacza w niektórych zasobach
- [Poprawka błędu] Nieskończony spinner (po zakończeniu wyszukiwania), gdy użytkownik szybko wykonuje wiele poszukiwań
- [Poprawka błędu] Pasek sterowania nie jest ukryty podczas braku aktywności
- [Poprawka błędu] Otwarcie aplikacji internetowej hostowej AMP może spowodować dwukrotne załadowanie strony sieci Web
- [Poprawka błędu] Nieskończone podczas odtwarzania zawartości niektórych zasobów za pośrednictwem Flash Tech
- [Poprawka błędu] Więcej opcji menu nie jest wyświetlany z wtyczki 3rd party
- [Poprawka błędu] [Skóra| Tube][Na żywo] Dwie ikony na żywo są wyświetlane, gdy gracz znajduje się na krawędzi programu na żywo
- [Poprawka błędu] [Skórka] Nie można wyłączyć logo
- [Poprawka błędu] [Zawartość DD+] Ciągły pokrętło pojawia się dla zasobów zawierających ścieżkę audio Dolby Digital
- [Poprawka błędu] Najnowsze AMP zawiesza się podczas przełączania ścieżek języka audio podczas transmisji na żywo
- [Poprawka błędu] naprawiono zniknięcie tła dla pokrętła
- [Poprawka błędu] Nieskończony pokrętło w statycznych próbkach tokenów flash AES

### <a name="changes-211"></a>Zmiany 2.1.1 ####

- [Zmień] Dodano kod błędu dla Widevine Https wymagania: od Chrome v58, widevine `https://` treści muszą być ładowane / odtwarzane za pośrednictwem protokołu w przeciwnym razie odtwarzanie zakończy się niepowodzeniem.
- [Zmień] Dodano etykietę aria do ładowania przędzarki, dzięki czemu technologia wspomagające może opowiadać "ładowanie wideo" podczas ładowania zawartości  

## <a name="210-official-release"></a>2.1.0 (Oficjalne wydanie) ##

### <a name="features-210"></a>Cechy 2.1.0 ###

- [Funkcja] [AzureHtml5JS] Vod Ad Support dla przed- mid-post-rolki
- [Funkcja] [Beta] [AzureHtml5JS] Obsługa reklam na żywo dla przed- mid-post-rolki
- [Funkcja] Dodano nową opcję skórki - AMP-flush
- [Funkcja] Dodano ulepszone etykiety aria dla lepszej integracji z czytnikami ekranu / technologią wspomaganą
- [Funkcja] [Skórka] Skin pokazuje teraz wszystkie ikony i przyciski wyraźnie w trybie wysokiego kontrastu

### <a name="bug-fixes-210"></a>Poprawki błędów 2.1.0 ###

- [Poprawka błędu] Liczba poprawek ułatwień dostępu i interfejsu użytkownika
- [Poprawka błędu] AMP nie ładuje się poprawnie w IE9

### <a name="changes-210"></a>Zmiany 2.1.0 ###

- [Zmień] Zrestrukturyzowane elementy DOM w odtwarzaczu, aby dostosować reklamy pracy
- [Zmień] Przełączony z CSS na SCSS dla rozwoju skóry
- [Zmień] [Przykłady] Dodano przykład dla reklam VOD
- [Zmień] [Przykłady] Dodano próbkę dla szybkości odtwarzania
- [Zmień] [Przykłady] Dodano próbkę do flush skin

## <a name="200-beta-release"></a>2.0.0 (wersja beta) ##

- [Zmień]zaktualizowany do VJS5
- [funkcja] Dodano nowy płynny interfejs API do płynu responsywności odtwarzacza
- [Funkcja] Prędkość odtwarzania
- [Zmień] Przełączony z CSS na SCSS dla skóry

## <a name="183-official-hotfix-update"></a>1.8.3 (Oficjalna aktualizacja poprawki) ##

### <a name="bug-fixes-183"></a>Poprawki błędów 1.8.3 ###

- [Poprawka błędu] [AzureHtml5JS] Niektóre zasoby z ujemnym DTS nie będą odtwarzane w Chrome

## <a name="182-official-hotfix-update"></a>1.8.2 (Oficjalna aktualizacja poprawki) ##

### <a name="bug-fixes-182"></a>Poprawki błędów 1.8.2 ###

- [Poprawka błędu] [AzureHtml5JS] Wyższa szybkość transmisji bitów audio nie będzie odtwarzana za pośrednictwem usługi AzureHtml5JS

## <a name="181-official-update"></a>1.8.1 (Oficjalna aktualizacja) ##

### <a name="bug-fixes-181"></a>Poprawki błędów 1.8.1 ###

- [Poprawka błędu] [iOS] Napisy/napisy, które nie są wyświetlane w rodzimym odtwarzaczu
- [Poprawka błędu] [AMP] Nie odtwarzane adresy URL przesyłania strumieniowego z pomocą sieci CDN dołączone do tokenów uwierzytelniania
- [Poprawka błędu] [FairPlay] Kod błędu FairPlay brakujący Identyfikator techniczny (Bits [31-28] z ErrorCode) zobacz Kody błędów, aby uzyskać więcej informacji
- [Poprawka błędu] [Safari] [PlayReady] Zawartość PlayReady w Safari daje nieskończoną przędzarkę

### <a name="changes-181"></a>Zmiany 1.8.1 ###

- [Zmień] [Html5] Zmienianie natywnych dzienników technicznych html5 w celu zawierające zdarzenia z VideoTag

## <a name="180-official-update"></a>1.8.0 (Oficjalna aktualizacja) ##

### <a name="features-180"></a>Cechy 1.8.0 ###

- [Cechy] [DRM] Dodano obsługę FairPlay (więcej informacji znajdziesz w [witrynie Chronionej zawartości)](azure-media-player-protected-content.md)

### <a name="bug-fixes-180"></a>Poprawki błędów 1.8.0 ###

- [Poprawka błędu] [AMP] Poszukiwanie użytkownika nie wyzwala zdarzenia oczekiwania, gdy sieć jest ograniczona
- [Poprawka błędu] [Flashss] Wybór jakości w technologii flash zgłasza wyjątek
- [Poprawka błędu] [AMP] Dynamiczne wybieranie jakości jest wyświetlane w menu kontekstowym
- [Poprawka błędu] [Skórka] Trudno jest wybrać ostatnią pozycję menu menu kontekstowego

### <a name="changes-180"></a>Zmiany 1.8.0 ###

- [Zmień] Zaktualizowano odtwarzacz do aktualnych wymagań Chrome EME
- [Zmień] Domyślna techOrder zmieniona, aby pomieścić nowe tech-html5FairPlayHLS (zobacz [Zawartość chroniona, aby](azure-media-player-protected-content.md) uzyskać więcej informacji)
- [Zmień] [AzureHtml5JS] Włączone odtwarzanie MPEG-Dash w przeglądarce Safari
- [Zmień] [Przykłady] Zmieniono próbki multi-DRM, aby pomieścić FairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (Oficjalna aktualizacja poprawki) ##

### <a name="bug-fixes-174"></a>Poprawki błędów 1.7.4 ###

- [Poprawka błędu] [Chrome] Niebieski kontur pojawia się wokół uchwytu wyszukiwania, gdy użytkownik ma kontekst odtwarzacza
- [Poprawka błędu] [IE9] Wyjątek JavaScript zgłoszony po załadowaniu odtwarzacza w IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (Oficjalna aktualizacja poprawki) ##

### <a name="bug-fixes-173"></a>Poprawki błędów 1.7.3 ###

- [Poprawka błędu] [AzureHtml5JS] Limit czasu gracza w ograniczonych sieciach

### <a name="changes-173"></a>Zmiany 1.7.3 ###

- [Zmień] Włączanie webcrypto na krawędzi do odszyfrowywania zawartości AES
- [Zmień] Optymalizacja heurystyki AMP w celu uwzględnienia fragmentów w pamięci podręcznej
- [Zmień] [AzureHtml5JS] Optymalizacja heurystyki dzięki zmniejszeniu opóźnienia szacowania przepustowości

## <a name="172-official-hotfix-update"></a>1.7.2 (Oficjalna aktualizacja poprawki) ##

### <a name="features-172"></a>Cechy 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- [Funkcja] [AzureHtml5JS| Firefox] Włącz odtwarzanie Widevine z EME dla Firefoksa 47+
- [Funkcja] Dodaj zdarzenie dla gracza pozbywającego się
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Poprawki błędów 1.7.2 ###

- [Poprawka błędu] Zakodowane parametry zapytania adresu URL Akamai CDN nie zostały poprawnie zdekodowane
- [Poprawka błędu] Wyjątek jest zgłaszany na manifestPlayableWindowLength()
- [Poprawka błędu] Widz nie zawsze może kliknąć przycisk odtwarzania filmu po zakończeniu odtwarzania filmu, aby ponownie obejrzeć
- [Poprawka błędu] Responsywne zmiany rozmiaru nie są zgodne z szybkimi zmianami rozmiaru okna
- [Poprawka błędu] [Krawędź| IE] Responsywne zmiany rozmiaru niewcierający na obciążenie strony dla width=x, height=auto
- [Poprawka błędu] [Android| Chrome] Chrome z prośbą o uprawnienia do odtwarzania zawartości DRM, gdy zawartość nie jest szyfrowana
- [Poprawka błędu] [Dostępność] [Krawędź] Kontrolki klawiatury nie dobierają poprawnie elementów menu kontekstowego
- [Poprawka błędu] [Dostępność] Brak wyświetlanego obramowania w trybie wysokiego kontrastu
- [Poprawka błędu] [Flashss] Detektor zdarzeń myszy nie został usunięty po usunięciu przez gracza powoduje wyjątek
- [Poprawka błędu] [Flashss] Problem analizowania adresu URL manifestu z zakodowanymi spawami
- [Poprawka błędu] [iOS] Błąd typu podczas oceny tech.featuresVolumeControl

### <a name="changes-172"></a>Zmiany 1.7.2 ###

- [Zmień] [DRM] Przeniesiono kontrole DRM po ustawieniu źródła, aby sprawdzać tylko, kiedy zawartość jest szyfrowana
- [Zmień] [AES] Usunięto niezdefiniowany obiekt typu/zwykły z żądania dostarczenia klucza
- [Zmień] [Dostępność] Narrator systemu Windows odczytuje teraz "Media Player", gdy kontekst jest w odtwarzaczu, a nie właściwości

## <a name="171-official-hotfix-update"></a>1.7.1 (Oficjalna aktualizacja poprawki) ##

### <a name="features-171"></a>Cechy 1.7.1 ###

- [Funkcja] Dodano opcję dla profilu hybrydowego heurystycznego (ten profil jest ustawiony domyślnie)

### <a name="bug-fixes-171"></a>Poprawki błędów 1.7.1 ###

- [Poprawka błędu] Responsywny projekt nie działa zgodnie ze standardem HTML5 (szerokość= 100%, wysokość = auto)
- [Poprawka błędu] Wartości procentowe szerokości i wysokości nie zachowujących się zgodnie z oczekiwaniami w wersji 1.7.0

## <a name="170-official-update"></a>1.7.0 (Oficjalna aktualizacja) ##

### <a name="features-170"></a>Cechy 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- [Funkcja] [AzureHtml5JS] [Flashss] Dodano currentMediaTime(), aby uzyskać czas nośnika kodera bieżącego czasu w sekundach
- [Funkcja] [Flashss] Zaimplementowano interfejsy API danych telemetrycznych pobierania z plikami videoBufferData() i audioBufferData()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- [Funkcja] [Flashss] Dodano zdarzenie "downloadbitratechanged"
- [Funkcja] Czas ładowania poprawiony w porównaniu ze starszymi wersjami odtwarzacza
- [Funkcja] Błędy są rejestrowane w konsoli JavaScript

### <a name="bug-fixes-170"></a>Poprawki błędów 1.7.0 ###

- [Poprawka błędu] Zakodowany adres URL plakatu z parametrami ciągu zapytania nie wyświetlanymi w odtwarzaczu
- [Poprawka błędu] Wyjątek, gdy nie ładowane technologii i wzmacniacza interfejsu API. Player.poster() jest nazywany
- [Poprawka błędu] Wyjątek, gdy funkcje próbują uzyskać dostęp do odtwarzacza po usunięciu
- [Poprawka błędu] [Dostępność] Brak konspektu na temat skupienia się na pasku postępu szukaj głowy
- [Poprawka błędu] [Dostępność] Menu kontekstowe mają cień w trybie wysokiego kontrastu
- [Poprawka błędu] [iOS] rodzimy odtwarzacz WebVTT napisy nie działa
- [Poprawka błędu] [AzureHtml5JS] Błąd 0x0100002 powinien być wyświetlany podczas odtwarzania strumienia HTTP w witrynie HTTPS, który zamiast tego daje nieskończony pokrętło w wyniku mieszanej zawartości
- [Poprawka błędu] [AzureHtml5JS] Brak segmentu końcowego powodującego błąd sprawdzania kondycji pętli wyświetlający postrzegany nieskończony stan buforowania
- [Poprawka błędu] [AzureHtml5JS] Nieprawidłowa nazwa ścieżki audio w menu podczas używaniaManifestForLabel=false i trzyliterowe kody języka są używane
- [Poprawka błędu] [AzureHtml5JS| Chrome] Postrzegany nieskończony stan bufora na końcu zawartości spowodowany niedokładnością zmiennoprzecinkową w czasie trwania z JavaScript w Chrome
- [Poprawka błędu] [Flashss] Nieśmiertelny błąd przerywany chwilowo wyświetlany podczas tworzenia odtwarzacza flash
- [Poprawka błędu] [Flashss] Odtwarzanie nie kończy się, gdy strumienie wideo i audio używają różnych skal czasu z powodu zaokrąglania nieprecyzyjnie z "Fragment url (...) nie może wygenerować znaczników FLVTags"
- [Poprawka błędu] [Flashss] Problemy z analizowaniem adresów URL manifestu z zakodowanymi spacjami
- [Poprawka błędu] [Flashss] Brak wyboru, aby ustalić, czy wersja flash player >= 11.4, który powoduje błąd w odtwarzaniu zamiast spadać z powrotem do następnej technologii w techOrder
- [Poprawka błędu] [Flashss] [AES] Problemy z akceptowaniem tokenów AES z podkreśleniami w nim
- [Poprawka błędu] [Silverlightss| OSX] prefiks "//" prefiks manifestu zamiast protokołu (HTTP lub HTTPS) jest rozpoznawany jako lokalny plik dający nieskończony pokrętło

### <a name="changes-170"></a>Zmiany 1.7.0 ###

- [Zmień] [Flashss] Scalone skrypty SWF ("MSAdaptiveStreamingPlugin-osmf2.0.swf" i "StrobeMediaPlayback.2.0.swf") w jedną pliki SWF o nazwie "StrobeMediaPlayback.2.0.swf"
- [Zmień] [Flashss] Zaktualizowano propagację kodu błędu, aby uzyskać bardziej precyzyjne kody błędów (np. 404s teraz wynik 0x30200194 zamiast błędu ogólnego 0x30200000)

## <a name="163-official-hotfix-update"></a>1.6.3 (Oficjalna aktualizacja poprawki) ##

### <a name="bug-fixes-163"></a>Poprawki błędów 1.6.3 ###

- [Poprawka błędu] Wyjątek środowiska uruchomieniowego JavaScript, gdy program obsługi zdarzeń skrótów klawiszowych jest wykonywany po usunięciu odtwarzacza
- [Poprawka błędu] [Android] [AzureHtml5JS] Brak odtwarzania na urządzeniu przenośnym za pomocą sieci komórkowej
- [Poprawka błędu] Zaktualizowano Forge, aby działał jako pracownik sieci Web, aby zwolnić interfejs użytkownika

## <a name="162-official-hotfix-update"></a>1.6.2 (Oficjalna aktualizacja poprawki) ##

### <a name="features-162"></a>Cechy 1.6.2 ###

- [Funkcja] Dodano dodatkowe języki do lokalizacji (więcej informacji w dokumentacji)

### <a name="bug-fixes-162"></a>Poprawki błędów 1.6.2 ###

- [Poprawka błędu] [IE9-10] Klikanie obszarów wokół odtwarzacza zminimalizowane okno przeglądarki ze względu na błąd IE9/IE10, który minimalizuje na window.blur()
- [Poprawka błędu] [Flashss] Nie akceptujące tokenów AES z podkreśleniami

## <a name="161-official-hotfix-update"></a>1.6.1 (Oficjalna aktualizacja poprawki) ##

### <a name="bug-fixes-161"></a>Poprawki błędów 1.6.1 ###

- [Poprawka błędu] [Flashss| Edge,ie][Silverlightss| IE] Nie można skupić się na innych elementów interfejsu użytkownika dla wejść lub innych w IE / Edge
- [Poprawka błędu] Odtwarzanie AES kończy się niepowodzeniem, gdy kuźnia niezdefiniowana
- [Poprawka błędu] [Android] [AzureHtml5JS| Chrome] Ciągły pokrętło nie odtwarza treści podczas pętli kontroli kondycji
- [Poprawka błędu] [IE9] console.log() nie obsługiwane przez IE 9 powodując wyjątek

## <a name="160-official-update"></a>1.6.0 (Oficjalna aktualizacja) ##

### <a name="features-160"></a>Cechy 1.6.0 ###

- [Funkcja] 33% redukcja rozmiaru azuremediaplayer.min.js
- [Funkcja] [AzureHtml5JS| Edge][Nieprzetestowane] Wsparcie dla strumieni audio DD+ w edge (bez przełączania kodeka po wstępnym wyborze). Aplikacja musi wybrać prawidłowy strumień audio w tej chwili.
- [Funkcja] Kontrolki skrótów klawiszowych (więcej informacji znajdziesz w dokumentów)
- [Funkcja] Wskazówka czasu postępu najechać kursem, aby uzyskać dokładny czas
- [Funkcja] Zezwalaj na asynchroniczne wykrywanie wtyczek, jeśli metoda setupDone istnieje w wtyczce

### <a name="bug-fixes-160"></a>Poprawki błędów 1.6.0 ###

- [Poprawka błędu] Dziennik pamięci nie opróżniania na getMemoryLog (true)
- [Poprawka błędu] Pole wyboru szybkość transmisji bitów resetuje się po ruchu myszy, powodując problem z wybieraniem niższych szybkości transmisji bitów za pomocą kontroli myszy
- [Poprawka błędu] Mac Office w awarii aplikacji podczas wykonywania sprawdzania DRM
- [Poprawka błędu] Klasy CSS są łatwo przypadkowo zastąpione
- [Poprawka błędu] [Chrome] Aktualizacja identyfikacji z przeglądarki ciągów agenta użytkownika jest Edge
- [Poprawka błędu] [AzureHtml5JS] Przycisk Podpisy nie jest wyświetlany na pasku narzędzi w edge(Win10) lub Chrome(Mac)
- [Poprawka błędu] [Android] [AzureHtml5JS| Chrome] InvalidStateError wyjątek na endOfStream() połączenia na krótkie filmy
- [Poprawka błędu] [Firefox] Usuwanie ostrzeżenia DRM spowodowanego przez Firefoksa podczas sprawdzania możliwości przeglądarki
- [Poprawka błędu] [Html5] Napisy/Napisy nie są wyświetlane z progresywną zawartością mp4
- [Poprawka błędu] [Flashss] Wiadomości z pasującymi sygnaturami czasowymi były rejestrowane w odwrotnej kolejności
- [Poprawka błędu] [Dostępność] [Chrome| Firefox] Karta i wybierz kontrolki automatycznie wybrać pierwszy element menu
- [Poprawka błędu] [Dostępność] Tabulator do sterowania głośnością przycisku

### <a name="changes-160"></a>Zmiany 1.6.0 ###

- [Zmień] Użyj czasu odszyfrowywania AES przy wyborze poziomu jakości
- [Zmień] Aktualizowanie nagrywarki adresów URL w celu użycia HLS v4 przed HLS v3 dla strumieni multi-audio
- [Zmień] Ustaw nativeControlsForTouch na false jako domyślny (musi być false, aby działał poprawnie)

## <a name="150-official-update"></a>1.5.0 (Oficjalna aktualizacja) ##

### <a name="features-150"></a>Cechy 1.5.0 ###

- [Funkcja] Ulepszenia ogólnego bezpieczeństwa w sieci (zapobieganie iniekcji, XSS itp.)
- [Funkcja] Haki integracji wtyczki SDN dla sourceset zdarzenia i options.sdn
- [Funkcja] Solidność obsługi błędów 5XX i 4XX podczas odtwarzania

### <a name="bug-fixes-150"></a>Poprawki błędów 1.5.0 ###

- [Poprawka błędu] Aktualizacja minyfikacji CSS, aby używać kodów czcionek jednostki HTML dla przycisków zamiast Unicode
- [Poprawka błędu] [AzureHtml5JS] Zawartość multi-DRM zawsze wybierając token pierwszego elementu z protectionInfo powodując drugi DRM nie powiedzie się
- [Poprawka błędu] [AzureHtml5JS] Poszukiwanie nigdy nie kończy się podczas poszukiwania w obszarze z brakującymi segmentami.
- [Poprawka błędu] [AzureHtml5JS| Krawędź] Włącz poprzedzony eme w edge aktualizacji do odtwarzania PlayReady
- [Poprawka błędu] [AzureHtml5JS| Firefox] Update EME check to allow Firefox v42+ (with MSE) to fallback to Silverlight for protected content Firefox
- [Poprawka błędu] [Flashss] Aktualizuj plik error.message od numeru do szczegółowego ciągu

### <a name="changes-150"></a>Zmiany 1.5.0 ###

- [Zmień] Plakaty działają obecnie tylko jako bezwzględne adresy URL.

## <a name="140-official-update"></a>1.4.0 (Oficjalna aktualizacja) ##

### <a name="features-140"></a>Cechy 1.4.0 ###

- [Funkcja] [AzureHtml5JS| Chrome] Simple Widevine DRM wsparcie
- [Funkcja] [AzureHtml5JS] Solidność obsługi błędów 404/412 podczas odtwarzania

### <a name="bug-fixes-140"></a>Poprawki błędów 1.4.0 ###

- [Poprawka błędu] [Flashss] Ulepszenie sprawdzania poprawności parametrów

## <a name="130-official-update"></a>1.3.0 (Oficjalna aktualizacja) ##

### <a name="features-130"></a>Cechy 1.3.0 ###

- [Funkcja] [AzureHtml5JS] [Flashss] Przełączanie dźwięku tej samej zawartości kodeka Multi-Audio

### <a name="bug-fixes-130"></a>Poprawki błędów 1.3.0 ###

- [Poprawka błędu] [AzureHtml5JS| Chrome] Przerywany nieskończony pokrętło
- [Poprawka błędu] [AzureHtml5JS| IE][Windows Phone] Wyjątek powodujący problemy z odtwarzaniem w systemie Windows Phone
- [Poprawka błędu] [Flashss] Autoodtwarzanie ustawione na false kończy się niepowodzeniem dla dodatkowych wystąpień
- [Poprawka błędu] Problemy z rozmiarami menu interfejsu użytkownika

## <a name="120-official-update"></a>1.2.0 (Oficjalna aktualizacja) ##

### <a name="features-120"></a>Cechy 1.2.0 ###

- [Funkcja] [AzureHtml5JS| Firefox] Wsparcie po włączeniu mse
- [Funkcja] Nie wymaga już aplikacji do zapewnienia ścieżek dla rezerwowych plików binarnych technologii (swf, xap). Ścieżka jest względem skryptu programu Azure Media Player.

### <a name="bug-fixes-120"></a>Poprawki błędów 1.2.0 ###

- [Poprawka błędu] [AzureHtml5JS| Chrome] Gracz dryfuje za krawędzią na żywo, gdy gracz w tle
- [Poprawka błędu] [AzureHtml5JS| Krawędź] Pełny ekran nie działa
- [Poprawka błędu] [AzureHtml5JS] Rejestrowanie nie zostało włączone poprawnie po ustawieniu opcji
- [Poprawka błędu] [Flash] Zarówno "buforowanie", jak i buforowanie ikony wyświetlane podczas oczekiwania na zdarzenie
- [Poprawka błędu] Zezwalaj na kontynuowanie odtwarzania w przypadku niepowodzenia początkowego żądania przepustowości
- [Poprawka błędu] Odtwarzacz nie może załadować po zainicjowaniu z niezdefiniowanymi opcjami
- [Poprawka błędu] Podczas próby utylizacji odtwarzacza po jego usunięciu występuje wyjątek vdata
- [Poprawka błędu] Nieprawidłowo mapowane ikony paska jakości

## <a name="111-official-hotfix-update"></a>1.1.1 (Oficjalna aktualizacja poprawki) ##

### <a name="bug-fixes-111"></a>Poprawki błędów 1.1.1 ###

- [Poprawka błędu] Starszy problem pełnoekranowy IE
- [Poprawka błędu] Wtyczki nie są już zastępowane

## <a name="110-official-update"></a>1.1.0 (Oficjalna aktualizacja) ##

### <a name="features-110"></a>Cechy 1.1.0 ###

- [Funkcja] Aktualizowanie ciągów lokalizacji interfejsu użytkownika

### <a name="bug-fixes-110"></a>Poprawki błędów 1.1.0 ###

- [Poprawka błędu] Przycisk Big Play nie ma wystarczającego kontrastu
- [Poprawka błędu] Wskaźnik ostrości karty wizualnej
- [Poprawka błędu] Wybierz menu Bitrate teraz przy użyciu informacji o poprawnej rozdzielczości
- [Poprawka błędu] Więcej opcji menu teraz dynamicznie wielkości
- [Poprawka błędu] Różne problemy z interfejsem użytkownika

## <a name="100-official-release"></a>1.0.0 (Oficjalne wydanie) ##

### <a name="features-100"></a>Cechy 1.0.0 ###

- [Funkcja] Podstawowe testowanie ułatwień dostępu do sterowania tabulatorami, sterowania ostrością, czytnika ekranu, interfejsu użytkownika o wysokim kontraście
- [Funkcja] Zaktualizowany interfejs użytkownika
- [Funkcja] Rejestrowanie deweloperów
- [Funkcja] INTERFEJS API do dynamicznego ustawiania ścieżek podpisów/napisów
- [Funkcja] Podstawowe funkcje lokalizacji
- [Funkcja] Konsolidacja kodu błędu w technikach
- [Funkcja] Nowy kod błędu, gdy wtyczki (takie jak Flash lub Silverlight) nie są zainstalowane
- [Funkcja] [AzureHtml5JS] Wdrożone podstawowe zdarzenia diagnostyczne

### <a name="bug-fixes-100"></a>Poprawki błędów 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Poprawka błędu] [AzureHtml5JS] Zamrożenie odtwarzania na żywo na aktualizacjach MPD, gdy w sygnaturze czasowej występują małe nieprecyzyjne
- [Poprawka błędu] [AzureHtml5JS] Złagodził kilka problemów z odtwarzaniem na żywo
- [Poprawka błędu] [AzureHtml5JS] Opróżnianie buforów, gdy heurystyka rozmiaru okna jest wł., i przechodzi na ekran o wyższej rozdzielczości
- [Poprawka błędu] [AzureHtml5JS] Chrome teraz poprawnie pokazuje zakończone zdarzenie. Połączony z poprzedniwyznany problem *Chrome nie&euro;będzie poprawnie&euro;wysyłać â œendedâ zdarzenia podczas korzystania z AzureHtml5JS. Wystąpił problem w podstawowej przeglądarce.*
- [Poprawka błędu] [AzureHtml5JS] Wyłączone Safari dla tej technologii w celu rozwiązania *problemu z odtwarzaniem z OSX Yosemite z azurehtml5JS tech. Istnieją problemy z implementacją MSE. Tymczasowe łagodzenie: życie&euro;â&euro;œflashSÂ&euro;, â œsilverlightSSâ&euro;jako tech celu dla tych agentów użytkownika*
- [Poprawka błędu] [FlashSS] loadstart zwolniony po wystąpieniu błędu

## <a name="020-beta"></a>0.2.0 (beta) ##

### <a name="features-020"></a>Cechy 0.2.0 ###

- [Funkcja] Zakończone testy playready i AES na żądanie i na żywo - zobacz macierz zgodności
- [Funkcja] Obsługa nieciągłości
- [Funkcja] Obsługa sygnatur czasowych większych niż 2^53
- [Funkcja] Parametr kwerendy adresów URL jest nadal wysyłany do żądania manifestu
- [Funkcja] [Nieprzetestowane] Obsługa `QuickStart` profili i `HighQuality` heurystyki
- [Funkcja] [Nieprzetestowane] Udostępnianie informacji o strumieniu wideo dla szybkości transmisji bitów, szerokości i wysokości w serwisach AzureHtml5JS i FlashSS
- [Funkcja] [Nieprzetestowane] Wybierz szybkość transmisji bitów na platformie AzureHtml5JS i FlashSS (zobacz dokumentację interfejsu API)

### <a name="bug-fixes-020"></a>Poprawki błędów 0.2.0 ###

- [Bug Fix] duży przycisk odtwarzania teraz widoczny na WP8.1
- [Poprawka błędu] naprawiła wiele problemów z odtwarzaniem na żywo
- Przycisk [Poprawka błędu] działa teraz w interfejsie użytkownika
- [Poprawka błędu] zaktualizowana funkcja ładowania interfejsu użytkownika w trybie autoodtwarzania
- [Poprawka błędu] Problem z programem ładującego AMD i definiowanie konfliktów metod
- [Poprawka błędu] WP 8.1 Problem z ładowaniem aplikacji Cordova
- [Poprawka błędu] Chroniona zawartość zapytań platformy/tech obsługiwane ProtectionType, aby wybrać odpowiednią technologię do odtwarzania.  Naprawiono poprzedni znany problem_z zawartością PlayReady w Chrome (na pulpicie) / Safari 8 (na OSX Yosemite) obecnie nie należy do odtwarzacza Silverlight_'
- [Bug Fix] nieprzychylić wyjątek na WinServer 2012 R2 ze względu na Media Foundation nie jest zainstalowany na tym komputerze domyślnie.  Spróbuj użyć interfejsów API tagu wideo HTML, które nie są implementowane, w ten sposób wyrzucając błąd. Bieżące ograniczenie jest złapać ten błąd i zwrócić false zamiast wyrzucania błędu.
- [Bug Fix] zawsze uzyskać init segmentu po seek lub http awarii, aby zapobiec usterkom podczas odtwarzania
- [Poprawka błędu] wyłącz śledzenie symulowanego postępu i licze czasu, gdy wystąpił błąd.
- [Poprawka błędu] usuń menu prawym przyciskiem myszy
- [Poprawka błędu] Komunikat o błędzie [AzureHtml5JS] nie jest wyświetlany, gdy nieprawidłowy token ustawiony dla zawartości PlayReady
- [Poprawka błędu] [AzureHtml5JS] przejście na pełny ekran podczas odtwarzania na żywo nie uwzględniało heurystyki rozmiaru okna
- [Poprawka błędu] [Flashss] Usunięto komunikaty wyświetlane w programie Strobe Media Player, dzięki czemu wyświetlane są tylko komunikaty programu Azure Media Player
- [Poprawka błędu] [SilverlightSS] nie otrzymuje "poszukiwani" zdarzenia, gdy szukamy poza czasem trwania lub mniej niż 0

## <a name="010-beta-release"></a>0.1.0 (wersja beta) ##

Wstępna wersja wstępna

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)
