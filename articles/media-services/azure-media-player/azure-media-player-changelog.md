---
title: Azure Media Player dziennika zmian
description: Azure Media Player dziennika zmian.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 09/23/2020
ms.openlocfilehash: c63a8efc4bbcf6e5a124d439e6f9f91397e2fa53
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315086"
---
# <a name="changelog"></a>Dziennik zmian

## <a name="236-official-update-september-21-2020"></a>2.3.6 (oficjalna aktualizacja: Wrzesień 21 2020)

### <a name="features-236"></a>2.3.6 funkcji

Dodano obsługę tylko audio dla azureHtml5JS Tech (PAUZa) obsługa opóźnionego rozpoczęcia transkrypcji na żywo zmiany języka

### <a name="bug-fixes-236"></a>Poprawki błędów 2.3.6

W przypadku korzystania z funkcji "playsinline" w przypadku odtwarzania HLS na urządzeniach firmy Apple kliknięcie przycisku "LIVE" spowoduje ponowne uruchomienie wideo obrazu plakatu AMP, czasami powoduje, że podczas odtwarzania tego elementu nie zdefiniowano przycisku woluminu, gdy jest używana klawiatura HLS FairPlay [Accessibility] 1 na pasku postępu [ułatwienia dostępu] fokus klawiatury czasami nie wraca do przycisku jakości wideo kontrolki [ułatwienia dostępu] nie są widoczne dla filmu wideo uniemożliwia narratorowi znalezienie ich

### <a name="changes-236"></a>Zmiany 2.3.6

Zwrócenie znaczących błędów dostarczania do aplikacji wywołujących

## <a name="235-official-update-june-1-2020"></a>2.3.5 (oficjalna Aktualizacja z czerwca 1 2020)

### <a name="bug-fixes-235"></a>Poprawki błędów 2.3.5

- U Odbiornik klucza ESC w okienku opcji jest dołączony do dokumentu
- U Zapobiegaj znikaniu interfejsu użytkownika odtwarzacza, jeśli pasek sterowania lub menu opcji zawiera fokus
- Na pasku sterowania jest wyświetlany nieprawidłowy czas zegara ściany, gdy jest włączone ustawienie wyświetlania czasu zegara ściany

### <a name="changes-235"></a>Zmiany 2.3.5

- Dodano komunikat o błędzie dla kodu błędu 0x00400005 i udokumentowany

## <a name="234-official-update-march-4-2020"></a>2.3.4 (oficjalna Aktualizacja z marca 4 2020)

### <a name="bug-fixes-234"></a>Poprawki błędów 2.3.4

- Nie można ustawić oprogramowania PlayReady overrideLicenseAcquistionUrl
- Nie można odtworzyć pewnej zawartości z nieciągłymi
- U Wartość atrybutu ID dla alertu czytnika ekranu musi być unikatowa
- U Podczas nawigowania po oknie dialogowym Ustawienia napisów, fokus jest przenoszony z okna dialogowego

### <a name="changes-234"></a>Zmiany 2.3.4

- Rejestruj długość zawartości po pomyślnym pobraniu, aby ułatwić analizowanie błędów odszyfrowywania 2.3.3 (oficjalna aktualizacja listopada 12 2019)

### <a name="features-234"></a>Funkcje 2.3.4

- Dodano obsługę wyświetlania czasu zegarowego wideo w postaci nakładki, a na pasku sterowania

### <a name="bug-fixes-234"></a>Poprawki błędów 2.3.4

- Przełącznik ścieżki audio działa, ale błąd danych wyjściowych w IE11 i Windows7 "obiekt nie obsługuje właściwości lub metody" Enabled ""
- Przełączanie ścieżki audio kończy się niepowodzeniem, gdy bufor jest w pełni załadowany
- Przełączanie ścieżki audio kończy się niepowodzeniem, gdy użytkownik wstrzymuje wideo i przełącza między ścieżkami audio bardzo szybko
- U Etykietki narzędzi, które nie zostały zdefiniowane dla kontrolki wideo w odtwarzaczu wideo
- Brak przycisków woluminów w języku HTML5 w zależności od momentu odebrania "loadstart"
- U Brak sposobu ustawienia tekstu alternatywnego obrazu plakatu
- U Nastąpi utrata fokusu aplikacji po wybraniu pozycji "gotowe" w oknie dialogowym Ustawienia napisów
- U Nieprawidłowe atrybuty ARIA są zdefiniowane dla elementu "Video" w obszarze "Podgląd segmentów"

### <a name="changes-234"></a>Zmiany 2.3.4

- Usunięto pustą etykietę/ścieżkę w przypadku odtwarzania HLS w systemach iOS i macOS Safari
- Zmniejszono liczbę 412s dla podpisów IMSC1
- Ostrzeżenie wyjściowe w konsoli dla 10 kolejnych pustych odpowiedzi IMSC1, aby ułatwić debugowanie na żywo

## <a name="232-official-update-october-9-2019"></a>2.3.2 (oficjalna Aktualizacja z października 9 2019)

### <a name="features-232"></a>Funkcje 2.3.2

-Dodano obsługę technologii PlayReady dla odtwarzania PAUZ dla przeglądarki Microsoft Edge

### <a name="bug-fixes-232"></a>Poprawki błędów 2.3.2

- Bieżąca szybkość odtwarzania nie jest wizualnie wyświetlana w menu szybkość odtwarzania, chyba że użytkownik ręcznie je ustawi
- U Okienko "Settings" nie jest zwinięte z kluczem "Esc"
- U Kod skrótu AMP "nie działa, gdy Narrator jest włączony

### <a name="changes-232"></a>Zmiany 2.3.2

- W przypadku przeglądarek, które nie obsługują kodera-dekoder audio E-AC3, ścieżki audio E-AC3 są ukryte w menu ścieżka audio
- W przypadku przeglądarek, które obsługują koder-dekoder audio E-AC3, domyślnie wybrana jest ścieżka audio E-AC3
- W przypadku przeglądarek, które nie obsługują przełączania do kodera-dekoder audio, ścieżki audio z innym kodekiem z wybranej ścieżki są ukryte w menu ścieżka audio

## <a name="231-official-update-august-12-2019"></a>2.3.1 (oficjalna Aktualizacja z sierpnia 12 2019)

### <a name="features-231"></a>Funkcje 2.3.1

- Sygnalizowanie zdarzenia po odebraniu pól emsg w przypadku odtwarzania przy użyciu KRESek — dodano obsługę funkcji śledzenia audio w formacie 3 w menu audio w przeglądarkach, które obsługują EC-3 i zezwalają na przełączanie ścieżki audio z AAC do EC3 i na odwrót tylko w przeglądarce brzegowej opartej na Chromimum

### <a name="bug-fixes-231"></a>Poprawki błędów 2.3.1

- Menu ścieżka audio jest uszkodzone po usunięciu ścieżek do-3
- Bieżący czas może być świetny niż czas trwania wideo
- Ustawianie szybkości odtwarzania za pośrednictwem initialSpeed nie działa
- Czasami po zakończeniu wyszukiwania gracz jest zablokowany
- Na urządzeniach brzegowych i IE na ekranie dotykowym, po powiększeniu do strony, naciśnięcie lub umieszczenie kursora myszy na elemencie SeekBar nie powoduje dokładnego uzyskania poprawnego segmentu wideo.
- U Aria etykieta dla opcji Play/Pause nie zawiera opis błędu dotyczącego nieznalezienia segmentu na żywo w usłudze Media Player
- U Role Aria używane na potrzeby odtwarzania/wstrzymywania muszą być zgodne z prawidłowymi wartościami (. VJS — tekst-Track-Display)
- U Niektóre role ARIA muszą być zawarte w określonych rodzicach
- U Brak etykietki narzędzia zdefiniowanej dla przycisku odtwarzania/wstrzymania w odtwarzaczu wideo IMSC1 podpisy mogą zniknąć po przeszukiwaniu bieżącego buforu wideo/audio

### <a name="changes-231"></a>Zmiany 2.3.1

- Po otrzymaniu segmentDecryptError i gracz jest już na żywo, teraz odtwarzacz odświeża manifest zamiast próbować następny segment
- Dodano więcej rejestrowania dla diagnostyki
- Zaktualizowana dokumentacja obejmująca obsługę FairPlay dla przeglądarki Safari dla systemu iOS
- Dodano przykład dla opcji IMSC1 ("srclang")
- Dodano uzupełnienie, textuzupełnienie, boxShadow zastąpień dla ścieżek tekstowych.
- Dodano kod błędu (0x0020025B) w celu odróżnienia tego, że pobranie segmentu nie powiodło się z powodu braku Internetu, a nie tylko wyrzucanie 0x00200259

## <a name="230-official-release-april-30-2019"></a>2.3.0 (oficjalne wydanie kwietnia 30 2019)

### <a name="features-230"></a>2.3.0 funkcji

- Dodano obsługę napisów IMSC1 dla ŁĄCZNIKa
- Dodano obsługę zasobów tylko wideo dla ŁĄCZNIKa
- Dodano interfejs API presentationTimeOffsetInSec

### <a name="bug-fixes-230"></a>Poprawki błędów 2.3.0

- Profil heurystyki AMP LowLatency zakłóca odtwarzanie wideo w systemie iOS "Wycisz" i "odciszenie" w przypadku niektórych języków mają nieprawidłowe tłumaczenia
- Wartość Aria-valuenow suwaka paska postępu jest czasami nieprawidłowa
- Wartość roli Aria wyświetlania ścieżki tekstowej jest niepoprawna

### <a name="changes-230"></a>Zmiany 2.3.0

- Dzienniki zawierają teraz rozmiar pobranych fragmentów nośnika
- Usunięto obsługę programu IE 9 i programu IE 10
- Zaktualizowany przykład CEA708, aby pokazać podpisy do lewej strony
- Uwzględnij MediaError. Message w dziennikach dla błędów odtwarzania

## <a name="224-official-update-february-22-2019"></a>2.2.4 (oficjalna aktualizacja lutego 22 2019) ##

### <a name="bug-fixes-224"></a>Poprawki w usterce 2.2.4 ###

- [Poprawka błędu] OGRANICZONY U Usunięto kartę dostępną Fantom po wyświetleniu ekranu błędu
- [Poprawka błędu] OGRANICZONY Naprawiono klawisz skrótu "for IE11 i Edge
- [Poprawka błędu] OGRANICZONY Rozwiązano wyjątek dla podpisów CEA708
- [Poprawka błędu] OGRANICZONY Rozwiązano problem dotyczący wstrzymania wideo dla przeglądarki Edge

### <a name="changes-224"></a>Zmiany 2.2.4 ###

- Stąp OGRANICZONY Gdy wystąpi błąd odszyfrowywania fragmentu, odtwarzacz ponawia bieżące i różne fragmenty w celu odzyskania odtwarzania
- Stąp OGRANICZONY Jeszcze więcej odporne na nakładające się fragmenty wideo lub audio

## <a name="223-official-update-january-9-2019"></a>2.2.3 (oficjalna aktualizacja stycznia 9 2019) ##

### <a name="features-223"></a>Funkcje 2.2.3 ###

- Ona HLS Dodano menu ścieżki audio dla odtwarzania Safari HLS

### <a name="bug-fixes-223"></a>Poprawki błędów 2.2.3 ###

- [Poprawka błędu] OGRANICZONY U Podczas odtwarzania emisji na żywo przycisk "Live" nie może być wybierany przy użyciu klawiatury
- [Poprawka błędu] OGRANICZONY Rozwiązano fałszywe pozytywne błędy 0x0400003 z powodu niepowodzenia testu MSE
- [Poprawka błędu] OGRANICZONY Rozwiązano problem polegający na tym, że wideo może zostać zamrożone podczas uruchamiania strumienia na żywo

### <a name="changes-223"></a>Zmiany 2.2.3 ###

- Stąp OGRANICZONY Dodano więcej informacji w dzienniku, aby umożliwić lepszą diagnostykę
- Stąp OGRANICZONY Gdy na tej samej rozdzielczości ekranu jest dostępna więcej niż jedna szybkość transmisji bitów, wszystkie szybkości transmisji bitów są dostępne do wyboru

## <a name="222-official-update"></a>2.2.2 (oficjalna aktualizacja) ##

### <a name="bug-fixes-222"></a>Poprawki błędów 2.2.2 ###

- [Poprawka błędu] OGRANICZONY Gdy gracz napotyka przejściową awarię sieci, natychmiast przestanie być odtwarzany
- [Poprawka błędu] OGRANICZONY U Okno dialogowe błędu nie jest dostępne przez klawiaturę
- [Poprawka błędu] OGRANICZONY Nieobsługiwany pokrętło wyświetlane podczas odtwarzania zawartości audio, a nie nieobsługiwanego błędu

### <a name="changes-222"></a>Zmiany 2.2.2 ###

- Stąp [AMP] dodano zlokalizowane ciągi dla interfejsu użytkownika anonsu

## <a name="221-official-update"></a>2.2.1 (aktualizacja oficjalna) ##

### <a name="features-221"></a>Funkcje 2.2.1 ###

- Ona [CMAF] Dodano obsługę HLS CMAF

### <a name="bug-fixes"></a>Poprawki błędów ###

- [Poprawka błędu] [AMP] nieoczyszczone czasomierze w logice ponowień, które reagują na błędy odtwarzania
- [Poprawka błędu] OGRANICZONY [Firefox] zakończone zdarzenie nie zostało uruchomione w przeglądarce Firefox i Chrome po zatrzymaniu programu na żywo
- [Poprawka błędu] OGRANICZONY Kontrolki wyświetlane po SetSource, nawet w przypadku, gdy w opcjach odtwarzacza ustawiono wartość false

### <a name="changes"></a>Zmiany ###

- Stąp [Podpisy dynamiczne] Zmieniono nazwę interfejsu API dla podpisów CEA od 608 do 708. Aby uzyskać więcej informacji, zobacz [Ustawienia napisów CEA708](/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0 (wydanie oficjalne) ##

### <a name="features-220"></a>2.2.0 funkcji ###

- Ona [Azurehtml5JS] Flash [LiveCaptions] Obsługa podpisów w programie CEA 708 w Azurehtml5JS i błysku Tech. w przypadku zawartości czystego i AES.

### <a name="bug-fixes-220"></a>Poprawki błędów 2.2.0 ###

- [Poprawka błędu] Wykrywanie wersji programu Flash nie działa w przeglądarce Chrome/Edge

### <a name="changes-220"></a>Zmiany 2.2.0 ###

- Stąp OGRANICZONY Heurystyki Zmieniono nazwę profilu heurystycznego z szybkiego startu na LowLatency
- Stąp Flash Zmień w programie Flash Player na potrzeby wykrywania wersji, aby umożliwić odtwarzanie zawartości AES przy użyciu nowej aktualizacji programu Adobe Flash.

## <a name="219-official-hotfix"></a>2.1.9 (oficjalna poprawka) ##

### <a name="bug-fixes-219"></a>Poprawki błędów 2.1.9 ###

- [Poprawka błędu] Żywa Wyjątek występujący, gdy strumienie na żywo przejścia do wideo na żądanie/archiwa na żywo

### <a name="changes-219"></a>Zmiany 2.1.9 ###

- Stąp Flash AES Zmodyfikowano logikę Tech. Flash, aby nie używać sharedbytearrays do odszyfrowywania AES, ponieważ w przypadku programu Flash nie zostało zablokowane użycie przez Adobe. Należy pamiętać, że odtwarzanie będzie możliwe tylko wtedy, gdy firma Adobe wdroży nową wersję programu Flash ze względu na błąd w V30. Aby uzyskać więcej informacji, zobacz [znane problemy](azure-media-player-known-issues.md)

## <a name="218-official-update"></a>2.1.8 (oficjalna aktualizacja) ##

### <a name="bug-fixes-218"></a>Poprawki błędów 2.1.8 ###

- [Poprawka błędu] Pokrętło czasami nie pokazuje publikowania i przed odtwarzaniem
- [Poprawka błędu] Gracz nie uruchamia się wyciszony, gdy opcja wyciszona jest włączona
- [Poprawka błędu] Suwak głośności jest wyświetlany, gdy kontrolki są ustawione na wartość FAŁSZ.
- [Poprawka błędu] Odtwarzanie czasami powtarza się, gdy użytkownik pominie do aktywnej krawędzi
- [Poprawka błędu] Firefox Gracz sporadycznie zgłasza wyjątek JavaScript podczas ładowania
- [Poprawka błędu] U Przycisk odtwarzania/wstrzymywania/woluminu utraci konspekt fokusu, gdy jest wybierany przy użyciu kontrolek klawiatury
- [Poprawka błędu] Trwały wyciek pamięci w odtwarzaczu został usunięty
- [Poprawka błędu] Wywołanie src () po błędzie odtwarzacza nie powoduje zresetowania źródła
- [Poprawka błędu] Żywa AMP jest w stanie ładowania stałego, gdy użytkownik kliknie przycisk na żywo po zakończeniu emisji
- [Poprawka błędu] Własnego Zawieszenie i odtwarzanie odtwarzacza kończy się niepowodzeniem, gdy przeglądarka zostanie zminimalizowana do tła.

### <a name="changes-218"></a>Zmiany 2.1.8 ###

- Stąp Zaktualizowano błąd 0x0600001, który ma być wyświetlany, gdy zawartość AES jest odtwarzana z programem Flash 30, ponieważ nie jest w tej chwili obsługiwana. Aby uzyskać więcej informacji, zobacz [znane problemy](azure-media-player-known-issues.md)
- Stąp Dodano dodatkowe próby dla scenariuszy na żywo, gdy manifestuje żądania 404 lub zwraca puste manifesty.

## <a name="217-official-update"></a>2.1.7 (oficjalna aktualizacja) ##

### <a name="features-217"></a>2.1.7 funkcji ###

- Ona [AzureHtml5JS] Dodano opcję konfiguracji w celu opróżnienia starych danych w buforze źródła nośnika

### <a name="bug-fixes-217"></a>Poprawki błędów 2.1.7 ###

- [Poprawka błędu] U [Czytnik ekranu] Usunięto pusty nagłówek odtwarzacza dołączonego, gdy nie ustawiono tytułu
- [Poprawka błędu] [UWA] AMP zgłasza wyjątek podczas odtwarzania w aplikacji uniwersalnej systemu Windows
- [Poprawka błędu] [OSX] setActiveTextTrack () nie działa w przeglądarce Safari na OSx
- [Poprawka błędu] Żywa Kliknięcie na żywo Edge po usunięciu i ponownym zainicjowaniu odtwarzacza
- [Poprawka błędu] Łuskę Bieżący czas został obcięty dla określonych zasobów
- [Poprawka błędu] [DRM] poprawka dołączona do obsługi odtwarzania w przeglądarkach, które obsługują wiele CENC DRM

### <a name="changes-217"></a>Zmiany 2.1.7 ###

- Stąp Badan U Dodano tag języka do wszystkich przykładów

## <a name="216-official-update"></a>2.1.6 (oficjalna aktualizacja) ##

### <a name="bug-fixes-216"></a>Poprawki błędów 2.1.6 ###

- [Poprawka błędu] AMP wyświetlanie nieprawidłowego czasu trwania dla określonego elementu zawartości
- [Poprawka błędu] [FairPlay-HLS] FairPlay błędy niepropagowania do interfejsu użytkownika
- [Poprawka błędu] Niestandardowe właściwości heurystyki są ignorowane w AMP 2.1.5.

### <a name="changes-216"></a>Zmiany 2.1.6 ###

- Stąp [FairPlayDRM] Usunięto limit czasu dla żądania certyfikatu i żądania licencji dla FairPlay, aby zachować zgodność z implementacjami PlayReady i Widevine
- Stąp Różne rozwiązania heurystyczne umożliwiające zwalczanie zamazanej zawartości

### <a name="features-216"></a>2.1.6 funkcji ###

- Ona Dodano obsługę formatu MPD-Time-CMAF

## <a name="215-official-hotfix"></a>2.1.5 (oficjalna poprawka) ##

### <a name="bug-fixes-215"></a>Poprawki błędów 2.1.5 ###

- [Poprawka błędu] Transkrypcji Style VTT nie są poprawnie renderowane przez odtwarzacz
- [Poprawka błędu] U Przycisk na żywo nie ma etykiety ARIA

## <a name="214-official-update"></a>2.1.4 (aktualizacja oficjalna) ##

### <a name="bug-fixes-214"></a>Poprawki błędów 2.1.4 ###

- [Poprawka błędu] U Fokus Użytkownicy nie mogą naciskać na karty niestandardowe dodane po prawej stronie przycisku Pełny ekran na pasku sterowania
- [Poprawka błędu] IE11 [Pasek głośności] Wyskakujące okienko do woluminu sprawia, że cały ekran wideo jest Flash w IE11 w trybie pełnoekranowym
- [Poprawka błędu] [Skórka | Opróżnianie] miejsce wyświetlane między paskiem sterowania a paskiem wyskakującym
- [Poprawka błędu] OGRANICZONY Transkrypcji Stare osadzone ścieżki nie są czyszczone, gdy źródło zostanie zmienione w istniejącym odtwarzaczu
- [Poprawka błędu] U Narrator Czytnik ekranu nie odczytuje prawidłowo kontrolki głośności
- [Poprawka błędu] [Błysk] Zdarzenia odtwarzania sporadycznie nie uruchamiają się z programu Flash Tech
- [Poprawka błędu] OGRANICZONY Fokus Funkcja Play/Pause wymaga dwóch kliknięć, gdy gracz ma fokus i jest w trybie pełnoekranowym
- [Poprawka błędu] OGRANICZONY Łuskę Nieprawidłowy czas wyświetlania na pasku postępu dla określonego zasobu
- [Poprawka błędu] Reklam [AD Butler] OGROMNY parser nie obsługuje OGROMNYch plików, które nie mają zdarzenia dotyczącego postępu
- [Poprawka błędu] SDN [AMP 2.1.1] Rozwiązano problem z obsługą wtyczki SDN Hive
- [Poprawka błędu] U Narrator odczytuje "przycisk Wycisz w północy", gdy użytkownik ma fokus przycisku woluminu

### <a name="changes-214"></a>Zmiany 2.1.4 ###

- Stąp U [Technologia pomocnicza] Przyciski mają teraz Właściwość Aria-Live do ulepszania środowiska z technologią pomocniczą
- Stąp U [Przycisk woluminu | Narrator] ulepszony dostęp do przycisku Volume, modyfikując funkcję tabulacji i zachowanie suwaka. Te zmiany ułatwiają użytkownikom klawiatury modyfikowanie woluminu odtwarzacza
- Stąp Zwiększono limit czasu menu kontekstowego nieaktywności z 3 na 5 sekund
- Stąp U Jasności Ulepszony współczynnik kontrastu jasności na menu rozwijanych w ustawieniach napisów

## <a name="213-official-update"></a>2.1.3 (aktualizacja oficjalna) ##

### <a name="bug-fixes-213"></a>Poprawki błędów 2.1.3 ###

- [Poprawka błędu] [Wtyczki | Nakładka tytułowa] wtyczka nakładania tytułów zgłasza wyjątki JS z AMP v2. X +
- [Poprawka błędu] Zdarzenie zestawu źródłowego jest wysyłane do konsoli JavaScript nawet wtedy, gdy rejestrowanie jest wyłączone
- [Poprawka błędu] Łuskę Wskazówki dotyczące czasu gracza są renderowane poza kontekstem odtwarzacza, gdy przesuwa się nad dowolnym paskiem czasu zakończenia
- [Poprawka błędu] U [Czytnik ekranu] Narrator odczytuje "dzielnica regionu" lub "dzielnica regionu odtwarzacza wideo", gdy Podgląd ma fokus w odtwarzaczu
- [Poprawka błędu] OGRANICZONY Nie można wyłączyć konspektu odtwarzacza za pośrednictwem CSS
- [Poprawka błędu] U Nie można ustawić fokusu na całym odtwarzaczu, gdy użytkownik jest w trybie pełnoekranowym
- [Poprawka błędu] Łuskę Żywa Skórka nie odpowiada na zlokalizowany tekst na żywo w języku japońskim
- [Poprawka błędu] Łuskę Czas trwania i bieżący czas są obcinane podczas przesyłania strumieniowego > 60 min-[Poprawka błędu] [iPhone | Live] odtwarzacz wyświetla tekst dla bieżącego czasu/czasu trwania na pasku sterowania
- [Poprawka błędu] OGRANICZONY Wywołanie interfejsów API heurystycznych algorytmów wywołujących wyjątków JavaScript
- [Poprawka błędu] [Natywne HTML5 | iOS] Właściwość Videotag "playsinline" nie jest propagowana do odtwarzacza
- [Poprawka błędu] [iOS | iframe] Odtwarzacz nie może wprowadzić pełnych elementów na telefonie iPhone, jeśli program Player jest załadowany w elemencie iframe
- [Poprawka błędu] OGRANICZONY Heurystyki AMP zawsze działa z profilem hybrydowym, niezależnie od opcji odtwarzacza
- [Poprawka błędu] [AMP | Win 8.1] zgłasza, gdy jest hostowany w aplikacji Win 8.1 z widokiem WebView

### <a name="changes-213"></a>Zmiany 2.1.3 ###

- Stąp OGRANICZONY Dodano informacje o punkcie końcowym usługi CDN w zdarzeniu FragmentDownloadComplete
- Stąp OGRANICZONY Żywa Ulepszone i zoptymalizowane opóźnienie przesyłania strumieniowego na żywo

## <a name="212-official-hotfix"></a>2.1.2 (oficjalna poprawka) ##

### <a name="bug-fixes-212"></a>Poprawki błędów 2.1.2 ####

- [Poprawka błędu] U [Narrator systemu Windows] Narrator odczytuje "postęp północy", gdy użytkownik ma kontekst paska postępu i bieżący czas to 0:00
- [Poprawka błędu] Rozmiar logo [skórka] jest zakodowany w kodzie JavaScript
- U Klawisze dostępu Klawisze skrótu nie są włączone, gdy gracz zostanie kliknięty.

### <a name="changes-212"></a>Zmiany w 2.1.2 ####

- Stąp Rejestrować Adres URL manifestu dziennika, gdy odtwarzacz nie może załadować manifestu

### <a name="features-212"></a>Funkcje 2.1.2 ###

- Stąp Skuteczności Optymalizacja Ulepszone czasy ładowania i uruchamiania odtwarzacza

## <a name="211-official-update"></a>2.1.1 (aktualizacja oficjalna) ##

### <a name="bug-fixes-211"></a>Poprawki błędów 2.1.1 ####

- [Poprawka błędu] Wykonane Ustawienie autoodtwarzania na false powoduje nieskończoną pokrętło w przeglądarce Safari dla systemu iOS
- [Poprawka błędu] Wyszukiwanie w czasie dłuższym niż czas trwania zawartości daje nieskończone pokrętło
- [Poprawka błędu] Klawisze skrótu wymagają wielu kart klawiatury, aby uzyskać kontekst pracy odtwarzacza
- [Poprawka błędu] Wideo jest zamrożone przez kilka sekund po zmianie rozmiarów odtwarzacza w określonych zasobach
- [Poprawka błędu] Nieskończone pokrętło (po zakończeniu poszukiwania), gdy użytkownik robi wiele szybko przeszukiwania
- [Poprawka błędu] Pasek sterowania nie jest ukryty podczas nieaktywności
- [Poprawka błędu] Otwarcie webapp, który hostuje AMP może spowodować, że strona sieci Web zostanie załadowana dwukrotnie
- [Poprawka błędu] Nieskończoność podczas odtwarzania zawartości niektórych zasobów za pośrednictwem programu Flash Tech
- [Poprawka błędu] Menu Więcej opcji nie jest wyświetlane z wtyczkami innych firm
- [Poprawka błędu] [Skórka | Rura] [Live] dwie ikony na żywo są wyświetlane, gdy gracz znajduje się na aktywnej krawędzi programu
- [Poprawka błędu] Łuskę Nie można wyłączyć logo
- [Poprawka błędu] [DD + Content] Wskaźnik ciągły jest wyświetlany dla zasobów zawierających ścieżkę audio Dolby Digital
- [Poprawka błędu] Najnowsza AMP zawieszania podczas przełączania ścieżek języka audio podczas transmisję strumieniową
- [Naprawa błędów] stałe tło Znikające dla pokrętła
- [Poprawka błędu] Nieskończone pokrętło w przypadku statycznych próbek tokenów AES Flash poprawki błędów

### <a name="changes-211"></a>Zmiany 2.1.1 ####

- Stąp Dodano kod błędu dla wymagania https Widevine: w przypadku programu Chrome v58 zawartość Widevine musi zostać załadowana/odtworzona przy użyciu `https://` protokołu, w przeciwnym razie odtwarzanie nie powiedzie się.
- Stąp Dodano etykietę Aria do ładowania pokrętła, dzięki czemu technologia wspomagająca może głosować "Ładowanie wideo" podczas ładowania zawartości  

## <a name="210-official-release"></a>2.1.0 (wydanie oficjalne) ##

### <a name="features-210"></a>2.1.0 funkcji ###

- Ona [AzureHtml5JS] Obsługa usługi AD w VOD
- Ona Wersji [AzureHtml5JS] Obsługa usługi AD na żywo na potrzeby przedśredniej
- Ona Dodano nową opcję karnacji — Metoda AMP-Flush
- Ona Dodano ulepszone etykiety ARIA dla lepszej integracji z czytnikami ekranu/technologią pomocniczą
- Ona Łuskę Karnacje teraz wyświetla wszystkie ikony i przyciski w trybie dużego kontrastu

### <a name="bug-fixes-210"></a>Poprawki błędów 2.1.0 ###

- [Poprawka błędu] Liczba poprawek ułatwień dostępu i interfejsu użytkownika
- [Poprawka błędu] Niewłaściwie załadowanie AMP w IE9

### <a name="changes-210"></a>Zmiany 2.1.0 ###

- Stąp Elementy modelu DOM z restrukturyzacją w odtwarzaczu w celu uwzględnienia pracy z reklamami
- Stąp Przełączono z CSS do języka SCSS na potrzeby tworzenia karnacji
- Stąp Badan Dodano przykład dla VOD ads
- Stąp Badan Dodano przykład dla szybkości odtwarzania
- Stąp Badan Dodano przykład dla skóry opróżniania

## <a name="200-beta-release"></a>2.0.0 (wydanie beta) ##

- [Zmiana] została zaktualizowana do VJS5
- ona Dodano nowy płyn API dla płynu czas odpowiedzi odtwarzacza
- Ona Szybkość odtwarzania
- Stąp Przełączono z arkusza CSS do języka SCSS dla karnacji

## <a name="183-official-hotfix-update"></a>1.8.3 (oficjalna aktualizacja poprawek) ##

### <a name="bug-fixes-183"></a>Poprawki błędów 1.8.3 ###

- [Poprawka błędu] [AzureHtml5JS] Niektóre zasoby z ujemną wartością DTS nie będą odtwarzane w przeglądarce Chrome

## <a name="182-official-hotfix-update"></a>1.8.2 (oficjalna aktualizacja poprawek) ##

### <a name="bug-fixes-182"></a>Poprawki błędów 1.8.2 ###

- [Poprawka błędu] [AzureHtml5JS] Wyższe szybkości transmisji bitów audio nie będą odtwarzane za pośrednictwem AzureHtml5JS

## <a name="181-official-update"></a>1.8.1 (oficjalna aktualizacja) ##

### <a name="bug-fixes-181"></a>Poprawki błędów 1.8.1 ###

- [Poprawka błędu] Wykonane Napisy/napisy nie są wyświetlane w macierzystym odtwarzaczu
- [Poprawka błędu] OGRANICZONY Sieci CDN — adresy URL przesyłania strumieniowego, które są dołączane przy użyciu tokenów uwierzytelniania
- [Poprawka błędu] FairPlay W kodzie błędu FairPlay brak Tech ID (BITS [31-28] kodu ErrorCode) zobacz Kody błędów, aby uzyskać więcej szczegółów
- [Poprawka błędu] Safari PlayReady Zawartość oprogramowania PlayReady w przeglądarce Safari z nieograniczoną pokrętłem

### <a name="changes-181"></a>Zmiany 1.8.1 ###

- Stąp HTML5 Zmień natywne dzienniki obsługi języka HTML5 (verbose), aby zawierały zdarzenia z VideoTag

## <a name="180-official-update"></a>1.8.0 (oficjalna aktualizacja) ##

### <a name="features-180"></a>1.8.0 funkcji ###

- Oferowanych ZASTOSOWANIE Dodano obsługę FairPlay (Aby uzyskać więcej informacji, zobacz [chroniona zawartość](azure-media-player-protected-content.md) )

### <a name="bug-fixes-180"></a>Poprawki błędów 1.8.0 ###

- [Poprawka błędu] OGRANICZONY Wyszukiwanie użytkownika nie wyzwala zdarzenia oczekiwania w przypadku ograniczenia sieci
- [Poprawka błędu] [Błysk] Wybieranie jakości w programie Flash — zgłaszanie wyjątku
- [Poprawka błędu] OGRANICZONY Dynamiczne Wybieranie jakości wyświetla w menu kontekstowym
- [Poprawka błędu] Łuskę Trudno jest wybrać ostatni element menu kontekstowego

### <a name="changes-180"></a>Zmiany 1.8.0 ###

- Stąp Zaktualizowano odtwarzacz do bieżących wymagań programu Chrome EME
- Stąp Domyślne techOrder zmienione w celu uwzględnienia nowych technologii Tech-html5FairPlayHLS (zobacz [chroniona zawartość](azure-media-player-protected-content.md) , aby uzyskać więcej informacji)
- Stąp [AzureHtml5JS] Włączono odtwarzanie w formacie MPEG-kreska w przeglądarce Safari
- Stąp Badan Zmieniono przykłady dla wieloskładnikowej technologii DRM w celu uwzględnienia FairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (oficjalna aktualizacja poprawek) ##

### <a name="bug-fixes-174"></a>Poprawki błędów 1.7.4 ###

- [Poprawka błędu] Własnego Niebieski kontur pojawia się wokół uchwytu wyszukiwania, gdy użytkownik ma kontekst odtwarzacza
- [Poprawka błędu] IE9 Zgłoszono wyjątek języka JavaScript podczas ładowania odtwarzacza w IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (oficjalna aktualizacja poprawek) ##

### <a name="bug-fixes-173"></a>Poprawki błędów 1.7.3 ###

- [Poprawka błędu] [AzureHtml5JS] Limit czasu odtwarzacza w sieciach z ograniczeniami

### <a name="changes-173"></a>Zmiany w sekcji 1.7.3 ###

- Stąp Włączanie usługi WebCrypto na krawędzi na potrzeby odszyfrowywania zawartości AES
- Stąp Optymalizacja heurystyki AMP do konta w przypadku buforowanych fragmentów
- Stąp [AzureHtml5JS] Optymalizuj heurystykę przez zmniejszenie opóźnienia szacowania przepustowości

## <a name="172-official-hotfix-update"></a>1.7.2 (oficjalna aktualizacja poprawek) ##

### <a name="features-172"></a>1.7.2 funkcji ###
<!---API needs onboarding. Removed link to API until remedied.--->
- Ona [AzureHtml5JS | Firefox] Włączanie odtwarzania Widevine za pomocą EME dla programu Firefox 47 +
- Ona Dodawanie zdarzenia do usuwania odtwarzacza
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Poprawki błędów 1.7.2 ###

- [Poprawka błędu] Zakodowane niepoprawnie Akamai parametry zapytania adresu URL usługi CDN
- [Poprawka błędu] Wyjątek zgłaszany w manifestPlayableWindowLength ()
- [Poprawka błędu] Po zakończeniu odtwarzania filmu wideo w przeglądarce nie zawsze można kliknąć pozycję Odtwórz wideo.
- [Poprawka błędu] Dopasowywanie rozmiaru nie jest zgodne z szybkimi zmianami rozmiaru okna
- [Poprawka błędu] [Krawędź | IE] odpowiada rozmiarowi strony, która nie ma wpływu na ładowanie stron o szerokości = x, Height = automatyczne
- [Poprawka błędu] [Android | Chrome] Chrome prosi o uprawnienia do odtwarzania zawartości DRM, gdy zawartość nie jest zaszyfrowana
- [Poprawka błędu] U Brzegu Kontrolki klawiatury nie wybierają poprawnie elementów menu kontekstowego
- [Poprawka błędu] U Brak wyświetlanego obramowania w trybie dużego kontrastu
- [Poprawka błędu] [Błysk] Po usunięciu z odtwarzacza nie został usunięty odbiornik zdarzeń z myszą. powoduje to wyjątek
- [Poprawka błędu] [Błysk] Problem z analizowaniem adresu URL manifestu z zakodowanymi spacjami
- [Poprawka błędu] Wykonane Błąd typu podczas oceny Tech. featuresVolumeControl

### <a name="changes-172"></a>Zmiany 1.7.2 ###

- Stąp ZASTOSOWANIE Przeniesiono testy funkcji DRM po ustawieniu źródła tylko do sprawdzenia, czy zawartość jest zaszyfrowana
- Stąp AES Usunięto niezdefiniowany treść typu/zwykły z żądania dostarczenia klucza
- Stąp U Program Windows Narrator odczytuje teraz "Media Player", gdy kontekst jest w odtwarzaczu zamiast właściwości

## <a name="171-official-hotfix-update"></a>1.7.1 (oficjalna aktualizacja poprawek) ##

### <a name="features-171"></a>1.7.1 funkcji ###

- Ona Dodano opcję dla hybrydowego profilu heurystycznego (ten profil jest ustawiany domyślnie)

### <a name="bug-fixes-171"></a>Poprawki błędów 1.7.1 ###

- [Poprawka błędu] Projekt odpowiadający nie działa zgodnie ze standardem HTML5 (szerokość = 100%, Wysokość = Auto)
- [Poprawka błędu] Wartości procentowe szerokości i wysokości nie zachowuje się w oczekiwany sposób w v 1.7.0

## <a name="170-official-update"></a>1.7.0 (oficjalna aktualizacja) ##

### <a name="features-170"></a>1.7.0 funkcji ###
<!---API needs onboarding. Removed link until remedied.--->
- Ona [AzureHtml5JS] [Błysk] Dodano currentMediaTime () w celu uzyskania czasu nośnika kodera bieżącego czasu (w sekundach)
- Ona [Błysk] Zaimplementowano pobieranie interfejsów API telemetrii z videoBufferData () i audioBufferData ()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- Ona [Błysk] Dodano zdarzenie "downloadbitratechanged"
- Ona Ulepszony czas ładowania w porównaniu ze starszymi wersjami odtwarzacza
- Ona Błędy są rejestrowane w konsoli JavaScript

### <a name="bug-fixes-170"></a>Poprawki błędów 1.7.0 ###

- [Poprawka błędu] Zakodowany adres URL plakatu z parametrami ciągu zapytania, które nie są wyświetlane w odtwarzaczu
- [Poprawka błędu] Wyjątek zgłoszony, gdy nie załadowano Tech i interfejsu API amp. Player. plakat () jest wywoływany
- [Poprawka błędu] Zgłoszono wyjątek, gdy funkcje próbują uzyskać dostęp do odtwarzacza po usunięciu
- [Poprawka błędu] U Brak konspektu na fokusie na pasku wyszukiwania paska postępu
- [Poprawka błędu] U Menu kontekstowe mają cień w trybie dużego kontrastu
- [Poprawka błędu] [iOS] macierzysty podpisy WebVTT nie działają
- [Poprawka błędu] [AzureHtml5JS] 0x0100002 błędu powinien być pokazywany podczas odtwarzania strumienia HTTP w witrynie HTTPS, która zamiast tego daje nieskończony pokrętło w wyniku użycia zawartości mieszanej
- [Poprawka błędu] [AzureHtml5JS] Brak segmentu końcowego powodującego błąd sprawdzania kondycji pętli podczas wyświetlania nieograniczonego stanu buforowania
- [Poprawka błędu] [AzureHtml5JS] Niepoprawna nazwa ścieżki audio w menu, gdy useManifestForLabel = false i trzy kody języka są używane
- [Poprawka błędu] [AzureHtml5JS | Program Chrome] odczuwał nieskończony stan buforu na końcu zawartości spowodowanej niedokładnością zmiennoprzecinkową w czasie trwania z JavaScript w przeglądarce Chrome
- [Poprawka błędu] [Błysk] Niekrytyczny tymczasowy błąd chwilowo wyświetlany podczas tworzenia programu Flash Player
- [Poprawka błędu] [Błysk] Odtwarzanie kończy się niepowodzeniem, gdy strumienie wideo i audio używają różnych osi czasu z powodu niepowodzenia zaokrąglania z "fragmentem adresu URL (...) nie można wygenerować FLVTags "
- [Poprawka błędu] [Błysk] Problemy podczas analizowania adresów URL manifestu z zakodowanymi spacjami
- [Poprawka błędu] [Błysk] Brak sprawdzenia, aby określić, czy program Flash Player w wersji >= 11,4, który powoduje błąd odtwarzania zamiast powracać do następnej technicznej w techOrder
- [Poprawka błędu] [Błysk] AES Problemy akceptujące tokeny AES ze znakami podkreślenia
- [Poprawka błędu] [Silverlight | OSX] "//" prefiks manifestu zamiast protokołu (HTTP lub HTTPS) jest rozpoznawany jako plik lokalny z nieograniczoną pokrętłem

### <a name="changes-170"></a>Zmiany 1.7.0 ###

- Stąp [Błysk] Scalone skrypty SWF ("MSAdaptiveStreamingPlugin-osmf 2.0. swf" i "StrobeMediaPlayback. 2.0. swf") w jednym pliku SWF o nazwie "StrobeMediaPlayback. 2.0. swf"
- Stąp [Błysk] Zaktualizowano propagację kodu błędu, aby uzyskać dokładniejsze kody błędów (np. 404s teraz spowoduje to 0x30200194 zamiast ogólnego błędu 0x30200000)

## <a name="163-official-hotfix-update"></a>1.6.3 (oficjalna aktualizacja poprawek) ##

### <a name="bug-fixes-163"></a>Poprawki błędów 1.6.3 ###

- [Poprawka błędu] Wyjątek środowiska uruchomieniowego JavaScript, gdy program obsługi zdarzeń klawisze skrótów jest wykonywany po usunięciu odtwarzacza
- [Poprawka błędu] Systemów [AzureHtml5JS] Nie Odtwarzaj na urządzeniu przenośnym przy użyciu sieci komórkowej
- [Poprawka błędu] Zaktualizowano fałszerstwo w celu uruchomienia jako proces roboczy sieci Web w celu zwolnienia interfejsu użytkownika

## <a name="162-official-hotfix-update"></a>1.6.2 (oficjalna aktualizacja poprawek) ##

### <a name="features-162"></a>Funkcje 1.6.2 ###

- Ona Dodano dodatkowe języki dla lokalizacji (zobacz dokumentację, aby uzyskać więcej szczegółów)

### <a name="bug-fixes-162"></a>Poprawki błędów 1.6.2 ###

- [Poprawka błędu] [IE9-10] Klikanie obszarów wokół zminimalizowanego okna przeglądarki odtwarzacza z powodu błędu IE9/programu IE10, który minimalizuje wartość Window. Blur ()
- [Poprawka błędu] [Błysk] Nie akceptuje tokenów AES z podkreśleniami

## <a name="161-official-hotfix-update"></a>1.6.1 (oficjalna aktualizacja poprawek) ##

### <a name="bug-fixes-161"></a>Poprawki błędów 1.6.1 ###

- [Poprawka błędu] [Błysk | Edge, IE] [Silverlight | IE] nie można ustawić fokusu dla innych elementów interfejsu użytkownika dla danych wejściowych lub innych w programie IE/Edge
- [Poprawka błędu] Odtwarzanie AES kończy się niepowodzeniem w przypadku niezdefiniowanego fałszowania
- [Poprawka błędu] Systemów [AzureHtml5JS | Chrome] ciągła pokrętło nie odtwarza zawartości z tyłu w pętli sprawdzania kondycji
- [Poprawka błędu] [IE9] Console. log () nie jest obsługiwany przez program IE 9 powodujący wyjątek

## <a name="160-official-update"></a>1.6.0 (oficjalna aktualizacja) ##

### <a name="features-160"></a>1.6.0 funkcji ###

- [Funkcja] 33% zmniejszenia rozmiaru azuremediaplayer.min.js
- Ona [AzureHtml5JS | Edge] [Nietestowana] obsługa strumieni DD + audio w programie Edge (bez przełączania do kodera-dekoder po początkowym wyborze). W tej chwili aplikacja musi wybrać poprawny strumień audio.
- Ona Kontrolki klawisza dostępu (zobacz dokumentację, aby uzyskać więcej szczegółów)
- Ona Wskaźnik postępu w czasie oczekiwania na dokładne wyszukiwanie
- Ona Zezwalaj na asynchroniczne wykrywanie wtyczek, jeśli metoda setupDone istnieje w dodatku plug-in

### <a name="bug-fixes-160"></a>Poprawki błędów 1.6.0 ###

- [Poprawka błędu] Nie opróżniaj dziennika pamięci w getMemoryLog (true)
- [Poprawka błędu] Pole wyboru szybkości transmisji bitów jest resetowane podczas przesuwania wskaźnika myszy powodującego problem z wybraniem mniejszej szybkości transmisji bitów za poorednictwem myszy
- [Poprawka błędu] W przypadku awarii pakietu Mac w aplikacji podczas przeprowadzania kontroli DRM
- [Poprawka błędu] Klasy CSS można łatwo przypadkowo zastąpić
- [Poprawka błędu] Własnego Identyfikacja aktualizacji z przeglądarki ciągu agenta użytkownika jest krawędzią
- [Poprawka błędu] [AzureHtml5JS] Przycisk napisów nie jest wyświetlany na pasku narzędzi w programie Edge (Win10) lub Chrome (Mac)
- [Poprawka błędu] Systemów [AzureHtml5JS | Chrome] InvalidStateError wyjątek w wywołaniu endOfStream () na krótkie wideo
- [Poprawka błędu] Firefox Usuwanie ostrzeżenia DRM powodowanego przez przeglądarkę Firefox podczas sprawdzania możliwości przeglądarki
- [Poprawka błędu] HTML5 Napisy/napisy nie są wyświetlane z progresywną zawartością MP4
- [Poprawka błędu] [Błysk] Komunikaty z pasującymi znacznikami czasu zostały zarejestrowane w odwrotnej kolejności
- [Poprawka błędu] U [Chrome | Firefox] Tab i Select Controls automatycznie wybiera pierwszy element menu
- [Poprawka błędu] U Klawisz kontrolny do sterowania woluminem

### <a name="changes-160"></a>Zmiany 1.6.0 ###

- Stąp Użyj czasu odszyfrowywania AES w wyborze poziomu jakości
- Stąp Zaktualizuj program do zapisywania adresów URL, aby używał HLS v4 przed HLS v3 dla strumieni wielodźwiękowych
- Stąp Ustaw wartość domyślną nativeControlsForTouch na false (musi mieć wartość false, aby poprawnie działać)

## <a name="150-official-update"></a>1.5.0 (oficjalna aktualizacja) ##

### <a name="features-150"></a>1.5.0 funkcji ###

- Ona Ulepszenia ogólnych zabezpieczeń sieci Web (zapobieganie iniekcjom, XSS itp.)
- Ona Punkty zaczepienia integracji wtyczki SDN dla zdarzeń sourceset i opcji. SDN
- Ona Niezawodna obsługa błędów 5XX i 4XX podczas odtwarzania

### <a name="bug-fixes-150"></a>Poprawki błędów 1.5.0 ###

- [Poprawka błędu] Zaktualizuj minifikacja CSS, aby używać kodów czcionek jednostki HTML dla przycisków zamiast Unicode
- [Poprawka błędu] [AzureHtml5JS] Zawartość z obsługą technologii DRM zawsze wybieraj token pierwszego elementu z protectionInfo, powodując niepowodzenie drugiego obiektu DRM
- [Poprawka błędu] [AzureHtml5JS] Wyszukiwanie w obszarze z brakującymi segmentami nigdy nie kończy się.
- [Poprawka błędu] [AzureHtml5JS | Edge] Włączanie prefiksu EME w aktualizacji usługi Edge dla odtwarzania oprogramowania PlayReady
- [Poprawka błędu] [AzureHtml5JS | Firefox] zaktualizuj EME Check, aby zezwolić przeglądarce Firefox v42 + (with MSE) na powrót do programu Silverlight na potrzeby zawartości chronionej
- [Poprawka błędu] [Błysk] Aktualizacja błędu. komunikat z liczby do szczegółowego ciągu

### <a name="changes-150"></a>Zmiany 1.5.0 ###

- Stąp Plakaty obecnie działają tylko jako bezwzględne adresy URL.

## <a name="140-official-update"></a>1.4.0 (oficjalna aktualizacja) ##

### <a name="features-140"></a>1.4.0 funkcji ###

- Ona [AzureHtml5JS | Chrome] prosta Widevine obsługa technologii DRM
- Ona [AzureHtml5JS] Niezawodna obsługa błędów 404/412 podczas odtwarzania

### <a name="bug-fixes-140"></a>Poprawki błędów 1.4.0 ###

- [Poprawka błędu] [Błysk] Ulepszenie weryfikacji parametrów

## <a name="130-official-update"></a>1.3.0 (oficjalna aktualizacja) ##

### <a name="features-130"></a>1.3.0 funkcji ###

- Ona [AzureHtml5JS] [Błysk] Przełączanie audio tego samego kodera-dźwiękowego

### <a name="bug-fixes-130"></a>Poprawki błędów 1.3.0 ###

- [Poprawka błędu] [AzureHtml5JS | Chrome] sporadycznie nieskończone pokrętło
- [Poprawka błędu] [AzureHtml5JS | IE] [Windows Phone] wyjątek powodujący problemy z odtwarzaniem Windows Phone
- [Poprawka błędu] [Błysk] Funkcja autoodtwarzania ustawiona na wartość false kończy się niepowodzeniem w przypadku dodatkowych wystąpień
- [Poprawka błędu] Problemy z ustalaniem wielkości menu interfejsu użytkownika

## <a name="120-official-update"></a>1.2.0 (oficjalna aktualizacja) ##

### <a name="features-120"></a>1.2.0 funkcji ###

- Ona [AzureHtml5JS | Firefox) obsługa, gdy jest włączona funkcja MSE
- Ona Nie wymagają już aplikacji, aby zapewnić ścieżki do rezerwowych plików binarnych (SWF, XAP). Ścieżka jest względna dla skryptu Azure Media Player.

### <a name="bug-fixes-120"></a>Poprawki błędów 1.2.0 ###

- [Poprawka błędu] [AzureHtml5JS | Chrome] dryfy odtwarzacza za aktywną krawędzią, gdy gracz w tle
- [Poprawka błędu] [AzureHtml5JS | Krawędź] pełny ekran nie działa
- [Poprawka błędu] [AzureHtml5JS] Rejestrowanie nie zostało prawidłowo włączone po ustawieniu w opcjach
- [Poprawka błędu] Flash Obie ikony "buforowanie" i "buforowanie" są wyświetlane podczas oczekiwania na zdarzenie
- [Poprawka błędu] Zezwalaj na kontynuowanie odtwarzania w przypadku niepowodzenia żądania przepustowości początkowej
- [Poprawka błędu] Nie można załadować odtwarzacza, gdy zostanie zainicjowany z niezdefiniowanymi opcjami
- [Poprawka błędu] Gdy podjęto próbę usunięcia odtwarzacza po jego usunięciu, wystąpi wyjątek VDATA
- [Poprawka błędu] Nieprawidłowo mapowane ikony paska jakości

## <a name="111-official-hotfix-update"></a>1.1.1 (oficjalna aktualizacja poprawek) ##

### <a name="bug-fixes-111"></a>Poprawki błędów 1.1.1 ###

- [Poprawka błędu] Starszy problem z pełnym ekranem programu IE
- [Poprawka błędu] Dodatki nie są już zastępowane

## <a name="110-official-update"></a>1.1.0 (oficjalna aktualizacja) ##

### <a name="features-110"></a>1.1.0 funkcji ###

- Ona Aktualizowanie ciągów lokalizacji interfejsu użytkownika

### <a name="bug-fixes-110"></a>Poprawki błędów 1.1.0 ###

- [Poprawka błędu] Przycisk dużego odtwarzania nie ma wystarczającego kontrastu
- [Poprawka błędu] Wskaźnik fokusu karty wizualnej
- [Poprawka błędu] Wybierz menu szybkość transmisji, używając poprawnych informacji o rozwiązaniu
- [Poprawka błędu] Menu Więcej opcji jest teraz dynamicznie skalowane
- [Poprawka błędu] Różne problemy z interfejsem użytkownika

## <a name="100-official-release"></a>1.0.0 (wydanie oficjalne) ##

### <a name="features-100"></a>1.0.0 funkcji ###

- Ona Podstawowe testowanie dostępności dla kontrolki karta, kontrola fokusu, czytnik ekranu, interfejs użytkownika o dużym kontraście
- Ona Zaktualizowany interfejs użytkownika
- Ona Rejestrowanie dev
- Ona Interfejs API umożliwiający dynamiczne Ustawianie napisów/śladów
- Ona Podstawowe funkcje lokalizacji
- Ona Błąd konsolidacji kodu w ramach konferencji Tech.
- Ona Nowy kod błędu dla sytuacji, gdy nie zainstalowano wtyczek (na przykład Flash lub Silverlight)
- Ona [AzureHtml5JS] Zaimplementowane podstawowe zdarzenia diagnostyczne

### <a name="bug-fixes-100"></a>Poprawki błędów 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Poprawka błędu] [AzureHtml5JS] Odtwarzanie na żywo w przypadku aktualizacji w dniu MPD w przypadku niewielkich niedokładności w znaczniku czasu
- [Poprawka błędu] [AzureHtml5JS] Wyeliminowano kilka problemów z odtwarzaniem na żywo
- [Poprawka błędu] [AzureHtml5JS] Bufory opróżniania, gdy są włączone heurystyke rozmiaru okna i przejdź do ekranu o wyższej rozdzielczości
- [Poprawka błędu] [AzureHtml5JS] W programie Chrome jest teraz poprawnie wyświetlana zakończona zdarzenie. Połączono z poprzednim znanym problemem *przeglądarki Chrome nie wyśle prawidłowo zakończonego zdarzenia po użyciu AzureHtml5JS. W podstawowej przeglądarce występuje problem.*
- [Poprawka błędu] [AzureHtml5JS] Wyłączono przeglądarkę Safari, aby rozwiązać *problem z odtwarzaniem za pomocą programu OSX Yosemite z AzureHtml5JS Tech. Istnieją problemy z implementacją skryptów. Tymczasowe ograniczenie: Wymuś błysk, Silverlight jako kolejność Tech dla tych agentów użytkownika*
- [Poprawka błędu] [Błysk] loadstart jest uruchamiany po wystąpieniu błędu

## <a name="020-beta"></a>0.2.0 (wersja beta) ##

### <a name="features-020"></a>0.2.0 funkcji ###

- Ona Zakończono testowanie dla oprogramowania PlayReady i AES dla na żądanie i na żywo — Zobacz macierz zgodności
- Ona Obsługa przerwań
- Ona Obsługa sygnatur czasowych większych niż 2 ^ 53
- Ona Parametr zapytania URL utrzymuje żądanie manifestu
- Ona Nieprzetestowanego Obsługa `QuickStart` profilów i `HighQuality` algorytmów heurystycznych
- Ona Nieprzetestowanego Udostępnianie informacji o strumieniu wideo dla szybkości transmisji bitów, szerokości i wysokości na AzureHtml5JS i błysku
- Ona Nieprzetestowanego Wybierz szybkość transmisji bitów w AzureHtml5JS i błysku (zobacz dokumentację interfejsu API)

### <a name="bug-fixes-020"></a>Poprawki błędów 0.2.0 ###

- [Poprawka błędu] duże przyciski odtwarzania widoczne teraz w programie WP 8.1
- [Poprawka błędu] Naprawiono wiele problemów z odtwarzaniem na żywo
- [Poprawka błędu] przycisk odciszenia działa teraz na interfejsie użytkownika
- [Poprawka błędu] zaktualizowane środowisko ładowania interfejsu użytkownika dla trybu autoodtwarzania
- [Poprawka błędu] Błąd modułu ładującego AMD oraz konflikty metod definiowania
- [Poprawka błędu] Problem z ładowaniem aplikacji oprogramowania Cordova w programie WP 8,1
- [Poprawka błędu] Chroniona kwerenda zawartości platforma/Tech. PROTECTIONTYPE do wybrania odpowiedniej Tech for playback.  Naprawia poprzedni znany problem dotyczący "_zawartości oprogramowania PlayReady w programie Chrome (Desktop)/Safari 8 (w systemie OSX Yosemite), który obecnie nie jest powrót do odtwarzacza Silverlight_"
- [Poprawka błędu] nieprzechwycony wyjątek w WinServer 2012 R2 z powodu niezainstalowanej domyślnie platforma Media Foundation na tym komputerze.  Próba użycia interfejsów API tagów wideo HTML, które nie zostały zaimplementowane, w rezultacie Zgłaszanie błędu. Bieżące środki zaradcze to przechwycenie tego błędu i zwrócenie wartości false zamiast zgłaszania błędu.
- [Poprawka błędu] zawsze pobieraj segment init po niepowodzeniu wyszukiwania lub http, aby zapobiec wystąpieniu błędów podczas odtwarzania
- [Poprawka błędu] Wyłącz śledzenie symulowanego postępu i timeupdates, gdy wystąpił błąd.
- [Poprawka błędu] Usuń menu prawego kliknięcia
- [Poprawka błędu] [AzureHtml5JS] komunikat o błędzie nie jest wyświetlany, gdy jest ustawiony nieprawidłowy token dla zawartości PlayReady
- [Poprawka błędu] [AzureHtml5JS] przechodzenie do trybu pełnoekranowego podczas odtwarzania na żywo nie pozostało na koncie heurystycznego rozmiaru okna
- [Poprawka błędu] [Błysk] Usunięto podMedia Player wyświetlane komunikaty, aby były wyświetlane tylko komunikaty Azure Media Player
- [Poprawka błędu] [Silverlight] nie pojawia się zdarzenie "odszukane" podczas wyszukiwania po upływie czasu trwania lub mniejszej niż 0

## <a name="010-beta-release"></a>0.1.0 (wydanie beta) ##

Początkowe wydanie wstępne

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)
