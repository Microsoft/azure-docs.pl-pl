---
title: Zaawansowane samouczki dotyczące usługi Media Encoder Premium Workflow
description: Ten dokument zawiera instruktaże pokazujące, jak wykonywać zaawansowane zadania z Media Encoder Premium Workflow oraz jak tworzyć złożone przepływy pracy z Projektant przepływu pracy.
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc
ms.reviewer: xpouyat; juliako
ms.openlocfilehash: 55d9d9191dc62d7397c5de944985d5653eff4e13
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269832"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Zaawansowane samouczki dotyczące usługi Media Encoder Premium Workflow

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Omówienie
Ten dokument zawiera instruktaże, które pokazują, jak dostosować przepływy pracy za pomocą  **Projektant przepływu pracy**. Rzeczywiste pliki przepływu pracy można znaleźć [tutaj](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>TREŚCI
Omówiono następujące tematy:

* [Kodowanie MXF w pojedynczej szybkości transmisji bitów](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Uruchamianie nowego przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Używanie danych wejściowych pliku multimedialnego](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Inspekcja strumieni multimediów](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Dodawanie kodera wideo dla programu. Generowanie plików MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Kodowanie strumienia audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multipleksowanie strumieni audio i wideo w kontenerze MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Zapisywanie pliku MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Tworzenie Media Services elementu zawartości z pliku wyjściowego](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Przetestowanie zakończonego przepływu pracy lokalnie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kodowanie MXF na wieloszybkościowe pliki MP4 — włączone dynamiczne pakowanie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Dodawanie jednego lub kilku dodatkowych danych wyjściowych MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurowanie nazw plików wyjściowych](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Dodawanie oddzielnej ścieżki audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Dodawanie pliku SMIL "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kodowanie MXF na skalowalne i ulepszone plany w formacie MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Omówienie przepływu pracy w celu usprawnienia
  * [Konwencje nazewnictwa plików](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publikowanie właściwości składnika w głównym przepływie pracy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Wygenerowane nazwy plików wyjściowych są zależne od opublikowanych wartości właściwości](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Dodawanie miniatur do danych wyjściowych w formacie wieloszybkościowej](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Omówienie przepływu pracy, w którym można dodać miniatury
  * [Dodawanie kodowania JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Postępowanie z konwersją przestrzeni kolorów](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Pisanie miniatur](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Wykrywanie błędów w przepływie pracy](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Zakończony przepływ pracy](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Przycinanie danych wyjściowych z szybkością transmisji bitów na podstawie czasu](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Omówienie przepływu pracy, aby rozpocząć dodawanie przycinania do](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Używanie elementu dostosowującego do strumienia](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Zakończony przepływ pracy](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Wprowadzenie do składnika skryptowego](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Wykonywanie skryptów w ramach przepływu pracy: Hello World](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Przycinanie danych wyjściowych z wieloszybkościową obsługą ramek](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Przegląd strategii, aby rozpocząć dodawanie przycinania do](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Korzystanie z XML listy klipów](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modyfikowanie listy klipów ze składnika skryptowego](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Dodawanie właściwości wygody ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a name="encoding-mxf-into-a-single-bitrate-mp4"></a><a id="MXF_to_MP4"></a>Kodowanie MXF w pojedynczej szybkości transmisji bitów
W tej sekcji pokazano, jak utworzyć pojedynczą szybkość transmisji bitów. Plik MP4 z AAC — zakodowany dźwięk z programu. Plik wejściowy MXF.

### <a name="starting-a-new-workflow"></a><a id="MXF_to_MP4_start_new"></a>Uruchamianie nowego przepływu pracy
Otwórz Projektant przepływu pracy i wybierz pozycję plik > nowy obszar roboczy > plan transkodowanie

Nowy przepływ pracy pokazuje trzy elementy:

* Podstawowy plik źródłowy
* Lista klipów XML
* Plik wyjściowy/zasób  

![Nowy przepływ pracy kodowania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nowy przepływ pracy kodowania*

### <a name="using-the-media-file-input"></a><a id="MXF_to_MP4_with_file_input"></a>Używanie danych wejściowych pliku multimedialnego
Aby można było zaakceptować plik nośnika wejściowego, jeden zaczyna się od dodania składnika danych wejściowych pliku multimedialnego. Aby dodać składnik do przepływu pracy, poszukaj go w polu wyszukiwania repozytorium i przeciągnij żądany wpis do okienka projektanta. Powtórz akcję dla wejściowego pliku multimedialnego i podłącz podstawowy składnik pliku źródłowego do pliku wejściowego numeru PIN z pliku multimedialnego.

![Dane wejściowe pliku połączonego nośnika](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Dane wejściowe pliku połączonego nośnika*

Początkowo Zidentyfikuj odpowiedni przykładowy plik, który ma być używany podczas projektowania niestandardowego przepływu pracy. Aby to zrobić, kliknij tło okienka projektanta i poszukaj właściwości podstawowy plik źródłowy w okienku właściwości po prawej stronie. Kliknij ikonę folderu i wybierz żądany plik do testowania przepływu pracy. Składnik wejściowy plików multimedialnych sprawdza plik i wypełnia jego numery PIN, aby odzwierciedlić szczegóły przykładowego pliku, który sprawdził.

![Wypełnione dane wejściowe pliku multimedialnego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Wypełnione dane wejściowe pliku multimedialnego*

Teraz, gdy dane wejściowe są wypełnione, następnym krokiem jest skonfigurowanie ustawień kodowania danych wyjściowych. Podobnie jak w przypadku skonfigurowania podstawowego pliku źródłowego, teraz Skonfiguruj Właściwość zmiennej folderu wyjściowego tuż poniżej.

![Skonfigurowane właściwości wejściowe i wyjściowe](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Skonfigurowane właściwości wejściowe i wyjściowe*

### <a name="inspecting-media-streams"></a><a id="MXF_to_MP4_streams"></a>Inspekcja strumieni multimediów
Często potrzebne jest, aby wiedzieć, jak wygląda strumień, jak przepływy przez przepływ pracy. Aby przeprowadzić inspekcję strumienia w dowolnym momencie przepływu pracy, po prostu kliknij dane wyjściowe lub wejściowy kod PIN na dowolnym składniku. W takim przypadku spróbuj kliknąć nieskompresowany numer PIN wideo z pliku multimedialnego. Zostanie otwarte okno dialogowe, które umożliwia inspekcję wideo wychodzącego.

![Sprawdzanie kodu PIN nieskompresowanego wideo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Sprawdzanie kodu PIN nieskompresowanego wideo*

W tym przypadku pokazuje, że film wideo zawiera 1920 x 1080 dane wejściowe z 24 klatek na sekundę w 4:2:2 próbkowanie wideo przez prawie 2 minuty.

### <a name="adding-a-video-encoder-for-mp4-file-generation"></a><a id="MXF_to_MP4_file_generation"></a>Dodawanie kodera wideo dla programu. Generowanie plików MP4
Teraz nieskompresowane wideo i wiele nieskompresowanych pinów wyjściowych audio jest dostępnych do użycia w danych wejściowych pliku multimedialnego. Aby można było zakodować wideo przychodzące, należy dodać składnik kodowania do przepływu pracy — w tym przypadku do wygenerowania. Pliki MP4.

Aby zakodować strumień wideo do H. 264, Dodaj składnik kodera wideo AVC do powierzchni projektanta. Ten składnik pobiera strumień wideo w postaci dekompresowania jako dane wejściowe i zapewnia skompresowany strumień wideo w formacie AVC dla wyjściowego kodu PIN.

![Niepołączony koder AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Niepołączony koder AVC*

Jego właściwości określają, jak dokładnie nastąpi kodowanie. Przyjrzyjmy się kilku ważniejszym ustawieniom:

* Szerokość i wysokość danych wyjściowych: Określa rozdzielczość zakodowanego wideo. W takim przypadku 640 x 360 jest dobrym ustawieniem.
* Szybkość klatek: gdy zostanie ustawiona na wartość Passthrough, po prostu przyjmie szybkość klatek źródłowych, można to zrobić mimo wszystko. Taka konwersja animacji nie jest kompensowana.
* Profil i poziom: określa profil AVC i poziom. Aby wygodnie uzyskać więcej informacji na temat różnych poziomów i profilów, kliknij ikonę znaku zapytania w składniku kodera wideo AVC, a na stronie Pomoc zostanie wyświetlona więcej szczegółów na temat każdego z poziomów. Na potrzeby tego przykładu Użyj profilu głównego na poziomie 3,2 (wartość domyślna).
* Tryb kontroli szybkości i szybkość transmisji bitów (KB/s): w tym scenariuszu należy wybrać stałą szybkość transmisji bitów (CBR) o 1200 KB/s
* Format wideo: zawiera informacje na temat VUI (informacje o użyteczności wideo), które są zapisywane w strumieniu H. 264 (informacje boczne, które mogą być używane przez dekoder w celu usprawnienia wyświetlania, ale nie są niezbędne do prawidłowego zdekodowania):
* NTSC (typowa dla Stanów Zjednoczonych lub Japonii, przy użyciu 30 fps)
* PAL (typowy dla Europy, z 25 fps)
* Tryb rozmiaru grupę GOP: Ustaw stały rozmiar grupę GOP w naszym celach z interwałem klucza wynoszącym 2 sekundy z zamkniętym GOPs. Ustawienie 2 sekund zapewnia zgodność z dynamicznym pakietem Azure Media Services.

Aby poprowadzić koder AVC, podłącz nieskompresowany numer PIN wideo z składnika danych wejściowych pliku multimedialnego do nieskompresowanego numeru PIN wideo z kodera AVC.

![Podłączony koder AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Połączony koder główny AVC*

### <a name="encoding-the-audio-stream"></a><a id="MXF_to_MP4_audio"></a>Kodowanie strumienia audio
W tym momencie oryginalny nieskompresowany strumień audio nadal musi być kompresowany. W celu kompresji strumienia audio Dodaj składnik AAC Encoder (Dolby) do przepływu pracy.

![Niepołączony koder AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Niepołączony koder AAC*

Jest to niezgodność: istnieje tylko jeden nieskompresowany kod PIN wejścia audio z kodera AAC, gdy więcej niż najprawdopodobniej dane wejściowe pliku multimedialnego będą dostępne dwa różne nieskompresowane strumienie audio: jeden dla lewego kanału audio i jeden po prawej stronie. (Jeśli chodzi o dźwięk przestrzenny, to sześć kanałów). Dlatego nie można bezpośrednio podłączyć dźwięku ze źródła danych wejściowych pliku multimedialnego do kodera audio AAC. Składnik AAC oczekuje tak zwanego strumienia audio "z przeplotem": pojedynczy strumień, który ma zarówno lewy, jak i prawy kanał z przeplotem. Po uzyskaniu informacji z pliku nośnika źródłowego, którego ścieżki audio znajdują się na pozycji źródłowej, można wygenerować ten przechodzący strumień audio z prawidłowymi przypisanymi stanowiskami głośników dla lewej i prawej strony.

Najpierw jeden chce generować strumień z przeplotem z wymaganych źródłowych kanałów audio. Składnik przeplotu strumienia audio obsługuje to dla nas. Dodaj go do przepływu pracy i Połącz dane wyjściowe audio z pliku multimedialnego.

![Przeplot z połączonego strumienia audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Przeplot z połączonego strumienia audio*

Teraz, gdy mamy strumień audio z przeplotem, nadal nie określono miejsca, do którego można przypisać lewy lub prawy element osoby mówiącej. Aby można było określić tę opcję, możemy wykorzystać przypisanie osoby mówiącej.

![Dodawanie przypisanej do stanowiska osoby mówiącej](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Dodawanie przypisanej do stanowiska osoby mówiącej*

Skonfiguruj przypisanie osoby mówiącej do użycia z strumieńem wejściowym stereo za pośrednictwem filtru wstępnego kodera "Custom" i ustawienia wstępnego kanału o nazwie "2,0 (L, R)". (Przypisujemy lewą pozycję głośników do kanału 1 i prawego stanowiska głośników do kanału 2).

Połącz dane wyjściowe osoby przypisujące stanowisko głośników z wejściem kodera AAC. Następnie poinformuj koder AAC o pracy ze wstępnie ustawionym kanałem "2,0 (L, R)", aby uzyskać informacje o rozproszeniu dźwięku stereo jako dane wejściowe.

### <a name="multiplexing-audio-and-video-streams-into-an-mp4-container"></a><a id="MXF_to_MP4_audio_and_fideo"></a>Multipleksowanie strumieni audio i wideo w kontenerze MP4
W naszym strumieniu wideo zakodowanym z AVC i AAC zakodowanym strumieniem audio można przechwycić oba elementy do. Kontener MP4. Proces mieszania różnych strumieni w jeden z nich nosi nazwę "multipleksing" (lub "Muxing"). W tym przypadku z przeplotem audio i strumieni wideo są przesyłane pojedynczo. Pakiet MP4. Składnik, który koordynuje ten element dla. Kontener MP4 jest nazywany multiplekserem ISO MPEG-4. Dodaj go do powierzchni projektanta i Połącz zarówno koder wideo AVC, jak i koder AAC z danymi wejściowymi.

![Połączony MPEG4 multiplekser](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Połączony MPEG4 multiplekser*

### <a name="writing-the-mp4-file"></a><a id="MXF_to_MP4_writing_mp4"></a>Zapisywanie pliku MP4
Podczas pisania pliku wyjściowego używany jest składnik danych wyjściowych. Możemy nawiązać połączenie z danymi wyjściowymi multipleksera ISO MPEG-4, aby dane wyjściowe były zapisywane na dysku. W tym celu należy połączyć numer PIN danych wyjściowych kontenera (MPEG-4) z wejściowym numerem PIN zapisu pliku wyjściowego.

![Dane wyjściowe połączonego pliku](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Dane wyjściowe połączonego pliku*

Użyta nazwa pliku jest określana na podstawie właściwości pliku. Chociaż ta właściwość może być stałe do danej wartości, najprawdopodobniej jedna chce ją ustawić za pomocą wyrażenia.

Aby przepływ pracy automatycznie określił Właściwość nazwy pliku wyjściowego z wyrażenia, kliknij przycisk obok nazwy pliku (obok ikony folderu). Z menu rozwijanego wybierz pozycję "wyrażenie". Spowoduje to wyświetlenie edytora wyrażeń. Najpierw Wyczyść zawartość edytora.

![Pusty Edytor wyrażeń](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Pusty Edytor wyrażeń*

Edytor wyrażeń umożliwia wprowadzanie dowolnych wartości literału, mieszanych z jedną lub większą liczbą zmiennych. Zmienne zaczynają się od znaku dolara. Po osiągnięciu klucza $, Edytor pokazuje pole listy rozwijanej z wyborem dostępnych zmiennych. W naszym przypadku użyjemy kombinacji zmiennej katalogu wyjściowego i zmiennej Nazwa podstawowego pliku wejściowego:

`${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4`

![Wypełniony Edytor wyrażeń](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Wypełniony Edytor wyrażeń*

> [!NOTE]
> Aby wyświetlić plik wyjściowy zadania kodowania na platformie Azure, należy podać wartość w edytorze wyrażeń.
>
>

Po potwierdzeniu wyrażenia przez naciśnięcie przycisku OK w oknie właściwości jest wyświetlana wartość właściwości pliku, która jest rozpoznawana w tym punkcie w czasie.

![Wyrażenie pliku rozwiązuje plik wyjściowy dir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Wyrażenie pliku rozwiązuje plik wyjściowy dir*

### <a name="creating-a-media-services-asset-from-the-output-file"></a><a id="MXF_to_MP4_asset_from_output"></a>Tworzenie Media Services elementu zawartości z pliku wyjściowego
Mimo że zapisano plik wyjściowy MP4, nadal musimy wskazać, że ten plik należy do wyjściowego elementu zawartości, który usługa Media Services generuje w wyniku wykonywania tego przepływu pracy. W tym celu zostanie użyty docelowy plik lub węzeł zasobu na kanwie przepływu pracy. Wszystkie pliki przychodzące do tego węzła tworzą część wygenerowanego Azure Media Services elementu zawartości.

Aby zakończyć przepływ pracy, podłącz składnik danych wyjściowych do wyjściowego pliku lub składnika zasobów.

![Zakończony przepływ pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Zakończony przepływ pracy*

### <a name="test-the-finished-workflow-locally"></a><a id="MXF_to_MP4_test"></a>Przetestowanie zakończonego przepływu pracy lokalnie
Aby przetestować przepływ pracy lokalnie, naciśnij przycisk odtwarzania na pasku narzędzi u góry. Po zakończeniu wykonywania przepływu pracy Sprawdź dane wyjściowe wygenerowane w skonfigurowanym folderze wyjściowym. Zobaczysz gotowy plik wyjściowy MP4, który został zakodowany z wejściowego pliku źródłowego MXF.

## <a name="encoding-mxf-into-mp4---multibitrate-dynamic-packaging-enabled"></a><a id="MXF_to_MP4_with_dyn_packaging"></a>Kodowanie MXF w formacie MP4-wielobitów włączonym do dynamicznego tworzenia pakietów
W tym instruktażu tworzony jest zestaw wielu plików MP4 o szybkości transmisji bitów z AAC zakodowanym dźwiękiem z jednego. Plik wejściowy MXF.

Gdy dane wyjściowe zasobów o różnej szybkości transmisji bitów są wymagane do użycia w połączeniu z funkcją dynamicznego tworzenia pakietów oferowaną przez Azure Media Services, należy wygenerować wiele plików MP4 o różnych grupę GOPach o dowolnej szybkości transmisji bitów i rozdzielczości. W tym celu [kodowanie MXF do jednej wskazówki dotyczącej szybkości transmisji bitów](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) zapewnia nam dobry punkt wyjścia.

![Uruchamianie przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Uruchamianie przepływu pracy*

### <a name="adding-one-or-more-additional-mp4-outputs"></a><a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Dodawanie jednego lub kilku dodatkowych danych wyjściowych MP4
Każdy plik MP4 w naszym wynikającym Azure Media Services jest obsługiwany przez inną szybkość transmisji bitów i rozwiązanie. Dodajmy co najmniej jeden plik wyjściowy MP4 do przepływu pracy.

Aby upewnić się, że wszystkie kodery wideo zostały utworzone przy użyciu tych samych ustawień, najlepiej jest zduplikować już istniejący koder wideo AVC i skonfigurować inną kombinację rozdzielczości i szybkości transmisji bitów (Dodaj jedną z 960 x 540 przy 25 klatkach na sekundę przy 2,5 MB/s). Aby zduplikować istniejący koder, skopiuj go na powierzchnię projektanta.

Połącz kod PIN nieskompresowanego wideo pliku multimedialnego danych wejściowych z naszym nowym składnikiem AVC.

![Połączono drugi koder AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Połączono drugi koder AVC*

Teraz dostosowuje konfigurację nowego kodera AVC do wyjściowego 960x540 o 2,5 MB/s. (Użyj jego właściwości "Szerokość wyjściowa", "wysokość wyjściowa" i "szybkość transmisji bitów (KB/s)").

Mając na względzie, że chcemy użyć wygenerowanego zasobu wraz z Azure Media Services "dynamicznym pakietem, punkt końcowy przesyłania strumieniowego musi być w stanie generować z tych plików MP4 HLS/pofragmentowane fragmenty MP4/PAUZ, które są dokładnie wyrównane do siebie w sposób, w jaki klienci przełączają się między różnymi szybkościami transmisji bitów i wideo. W takim przypadku musimy upewnić się, że we właściwościach obu koderów AVC rozmiar grupę GOP ("Grupa obrazów") dla obu plików MP4 ma wartość 2 sekund, co może być wykonywane przez:

* Ustawianie trybu grupę GOP rozmiaru na stały rozmiar grupę GOP i
* Interwał klatek kluczowych do dwóch sekund.
* Ustaw również formant grupę GOP IDR na zamknięty grupę GOP, aby upewnić się, że wszystkie GOPs są stałe bez zależności

Aby ułatwić zrozumienie tego przepływu pracy, Zmień nazwę pierwszego kodera AVC na "AVC Video Encoder 640 x 360 1200 KB/s" i drugi koder AVC "AVC Video Encoder 960x540 2500 KB/s".

Teraz Dodaj drugi multiplekser ISO MPEG-4 i drugi plik wyjściowy. Połącz multiplekser z nowym koderem AVC i upewnij się, że dane wyjściowe są kierowane do pliku wyjściowego. Następnie Połącz dane wyjściowe kodera audio AAC z nowym multiplekserem. Dane wyjściowe z kolei mogą następnie być połączone z docelowym plikiem/węzłem zasobów w celu dodania go do Media Services elementu zawartości, który zostanie utworzony.

![Drugie muxer i wyjście pliku wyjściowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Drugie muxer i wyjście pliku wyjściowego*

Aby zapewnić zgodność z Azure Media Services dynamicznym, skonfiguruj tryb fragmentu multipleksera w celu grupę GOP liczby lub czasu trwania i ustaw GOPs na fragment na 1. (Powinna to być wartość domyślna).

![Muxer — tryby fragmentów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer — tryby fragmentów*

Uwaga: można powtórzyć ten proces dla innych kombinacji szybkości transmisji bitów i rozdzielczości, które mają zostać dodane do danych wyjściowych elementu zawartości.

### <a name="configuring-the-file-output-names"></a><a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurowanie nazw plików wyjściowych
Do wyjściowego elementu zawartości został dodany więcej niż jeden plik. Jest to konieczne, aby upewnić się, że nazwy plików dla każdego pliku wyjściowego różnią się od siebie i mogą nawet stosować konwencję nazewnictwa plików, aby wyróżnić się od nazwy pliku, w którym się znajdują.

Nazwy wyjściowe plików można kontrolować za pomocą wyrażeń w projektancie. Otwórz okienko właściwości dla jednego z składników wyjściowych plików i Otwórz Edytor wyrażeń dla właściwości plik. Pierwszy plik wyjściowy został skonfigurowany za pośrednictwem następującego wyrażenia (zobacz Samouczek dotyczący przechodzenia z [MXF do pojedynczego wyjścia MP4 o szybkości transmisji bitów](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4`

Oznacza to, że nasza nazwa pliku jest określana przez dwie zmienne: katalog wyjściowy do zapisu i nazwa podstawowa pliku źródłowego. Dawniej jest uwidoczniony jako właściwość w katalogu głównym przepływu pracy, a drugi jest określany przez plik przychodzący. Katalog wyjściowy jest używany do testowania lokalnego; Ta właściwość zostanie przesłonięta przez aparat przepływu pracy, gdy przepływ pracy jest wykonywany przez procesor multimediów oparty na chmurze w Azure Media Services.
Aby nadać obu plików wyjściowych spójną nazwę wyjściową, Zmień pierwsze wyrażenie nazewnictwa plików na:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4`

a drugi do:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4`

Wykonaj pośredni przebieg testu, aby upewnić się, że oba pliki wyjściowe MP4 są prawidłowo generowane.

### <a name="adding-a-separate-audio-track"></a><a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Dodawanie oddzielnej ścieżki audio
Ponieważ będziemy widzieć później, gdy wygenerujemy plik. ISM, aby przejść do naszych plików wyjściowych MP4, będzie również wymagany plik MP4 tylko do przesyłania strumieniowego, który jest ścieżką audio dla naszych adaptacyjnych Streaming. Aby utworzyć ten plik, Dodaj dodatkowe muxer do przepływu pracy (multipleksera ISO-MPEG-4) i Połącz wyjściowy numer PIN kodera AAC z numerem PIN wejścia dla ścieżki 1.

![Dodano muxer audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Dodano muxer audio*

Utwórz trzeci składnik wyjściowy pliku do wyprowadzania strumienia wychodzącego z muxer i skonfiguruj wyrażenie nazewnictwa plików jako:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4`

![Muxer audio — Tworzenie pliku wyjściowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Muxer audio — Tworzenie pliku wyjściowego*

### <a name="adding-the-ism-smil-file"></a><a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Dodawanie. Plik SMIL
Aby dynamiczne pakowanie działało w połączeniu z obydwoma plikami MP4 (i MP4 tylko do nagrania dźwiękowego) w naszym Media Services, wymagany jest również plik manifestu (nazywany także plikiem "SMIL"). Ten plik wskazuje, Azure Media Services jakie pliki MP4 są dostępne dla pakowania dynamicznego i które mają być brane pod uwagę w przypadku przesyłania strumieniowego audio. Typowy plik manifestu dla zestawu MP4's z pojedynczym strumieniem audio wygląda następująco:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

Plik. ISM zawiera wewnątrz instrukcji switch, odwołanie do każdego z poszczególnych plików wideo MP4 i oprócz nich (lub więcej) odwołuje się do pliku MP4, który zawiera tylko dźwięk.

Generowanie pliku manifestu dla naszego zestawu MP4's można wykonać za pomocą składnika o nazwie "moduł zapisujący manifestu usługi AMS". Aby go użyć, przeciągnij go na powierzchnię i Połącz pinezki wyjściowe "Write Complete" z trzech składników wyjściowych plików na dane wejściowe modułu zapisywania manifestu usługi AMS. Następnie upewnij się, że dane wyjściowe składnika zapisywania manifestu AMS zostaną połączone z plikiem wyjściowym/zasobem.

Podobnie jak w przypadku innych składników danych wyjściowych plików, skonfiguruj nazwę danych wyjściowych. ISM z wyrażeniem:

`${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism`

Nasz zakończony przepływ pracy wygląda podobnie do poniższego:

![Zakończono przepływ pracy MP4 MXF do wieloszybkościowego działania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Zakończono przepływ pracy MP4 MXF do wieloszybkościowego działania*

## <a name="encoding-mxf-into-multibitrate-mp4---enhanced-blueprint"></a><a id="MXF_to__multibitrate_MP4"></a>Kodowanie MXF na skalowalne i ulepszone plany w formacie MP4
W [poprzednim przewodniku przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) zaobserwowano, w jaki sposób można SKONWERTOWAĆ pojedynczy MXFy zasób wejściowy do wyjściowego elementu zawartości z plikami MP4 o wysokiej szybkości transmisji bitów, plik MP4 z dźwiękiem i plik manifestu do użytku w połączeniu z Azure Media Services dynamicznym pakietem.

W tym instruktażu pokazano, w jaki sposób można rozszerzyć i zwiększyć część aspektów.

### <a name="workflow-overview-to-enhance"></a><a id="MXF_to_multibitrate_MP4_overview"></a>Omówienie przepływu pracy w celu usprawnienia
![Przepływ pracy MP4 o wieloszybkościowej transmisji bitów do usprawnienia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Przepływ pracy MP4 o wieloszybkościowej transmisji bitów do usprawnienia*

### <a name="file-naming-conventions"></a><a id="MXF_to__multibitrate_MP4_file_naming"></a>Konwencje nazewnictwa plików
W poprzednim przepływie pracy określono proste wyrażenie jako podstawę do generowania nazw plików wyjściowych. Mamy duplikaty, chociaż: wszystkie poszczególne składniki plików wyjściowych określiły takie wyrażenie.

Na przykład nasz składnik wyjściowy pliku dla pierwszego pliku wideo jest skonfigurowany przy użyciu tego wyrażenia:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4`

W przypadku drugiego wyjściowego wideo mamy wyrażenie takie jak:

`${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4`

Nie byłoby to przejrzyste, mniej podatne na błędy i bardziej wygodne, jeśli możemy usunąć część tego duplikatu i zwiększyć możliwości konfiguracji? Na szczęście możemy: możliwości wyrażenia projektanta w połączeniu z możliwością tworzenia właściwości niestandardowych w naszym głównym przepływie pracy zapewniają dodaną warstwę wygody.

Załóżmy, że będziemy dyskować konfigurację plików z szybkością transmisji bitów poszczególnych plików MP4. Te szybkości transmisji bitów mają na celu skonfigurowanie w jednym centralnym miejscu (w katalogu głównym grafu), z którego będą dostępne w celu skonfigurowania i dysku generowania nazw plików. Aby to zrobić, należy zacząć od opublikowania właściwości szybkość transmisji bitów z obu koderów AVC do katalogu głównego naszego przepływu pracy, dzięki czemu staną się dostępne zarówno z poziomu głównego, jak i z koderów AVC. (Nawet jeśli są wyświetlane w dwóch różnych plamach, istnieje tylko jedna wartość podstawowa).

### <a name="publishing-component-properties-onto-the-workflow-root"></a><a id="MXF_to__multibitrate_MP4_publishing"></a>Publikowanie właściwości składnika w głównym przepływie pracy
Otwórz pierwszy koder AVC, przejdź do właściwości szybkość transmisji bitów (KB/s), a następnie z listy rozwijanej wybierz pozycję Publikuj.

![Publikowanie właściwości szybkość transmisji bitów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publikowanie właściwości szybkość transmisji bitów*

Skonfiguruj okno dialogowe publikowanie w celu opublikowania w katalogu głównym wykresu przepływu pracy z opublikowaną nazwą "video1bitrate" i czytelną nazwą wyświetlaną "wideo 1 szybkość transmisji bitów". Skonfiguruj niestandardową nazwę grupy o nazwie "szybkość transmisji strumieniowej" i wybierz pozycję Publikuj.

![Publikowanie właściwości szybkość transmisji bitów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Okno dialogowe publikowania dla właściwości szybkość transmisji bitów*

Powtórz te same dla właściwości szybkość transmisji bitów drugiego kodera AVC i nadaj jej nazwę "video2bitrate" z nazwą wyświetlaną "szybkość transmisji wideo 2" w tej samej grupie niestandardowej "szybkość transmisji strumieniowej".

Jeśli teraz sprawdzimy właściwości główne przepływu pracy, zobaczymy naszą grupę niestandardową z dwiema opublikowanymi właściwościami. Oba odzwierciedlają wartość odpowiedniej szybkości transmisji bitów AVC.

![Właściwości publikowanej szybkości transmisji bitów w głównym przepływie pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Za każdym razem, gdy chcemy uzyskać dostęp do tych właściwości z kodu lub z wyrażenia, możemy to zrobić w następujący sposób:

* z kodu wbudowanego z składnika bezpośrednio do elementu głównego: Node. getPropertyAsString ('.. /video1bitrate ', null)
* w wyrażeniu: $ {ROOT_video1bitrate}

Uzupełnimy grupę "szybkość transmisji strumieniowych", publikując na niej naszą szybkość transmisji bitów audio. We właściwościach kodera AAC Wyszukaj ustawienie szybkość transmisji bitów i wybierz pozycję Publikuj z listy rozwijanej obok niej. Opublikuj w folderze głównym grafu o nazwie "audio1bitrate" i Wyświetl nazwę "szybkość transmisji bitów" audio 1 w naszej grupie niestandardowej "szybkość transmisji strumieniowej".

![Okno dialogowe publikowania dla szybkości transmisji bitów audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Okno dialogowe publikowania dla szybkości transmisji bitów audio*

![Wyniki filmów wideo i audio na serwerze głównym](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Wyniki filmów wideo i audio na serwerze głównym*

Zmiana dowolnych z tych trzech wartości powoduje również ponowne skonfigurowanie i zmiana wartości w odpowiednich składnikach, z którymi są one połączone (i w przypadku ich opublikowania).

### <a name="have-generated-output-file-names-rely-on-published-property-values"></a><a id="MXF_to__multibitrate_MP4_output_files"></a>Wygenerowane nazwy plików wyjściowych są zależne od opublikowanych wartości właściwości
Zamiast zakodowana naszych wygenerowanych nazw plików, możemy teraz zmienić wyrażenie filename dla każdego z składników danych wyjściowych, aby polegać na właściwościach bitów opublikowanych w głównym grafie. Rozpoczynając od pierwszego pliku wyjściowego, Znajdź właściwość pliku i zmodyfikuj wyrażenie podobne do tego:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4`

Różne parametry w tym wyrażeniu są dostępne i wprowadzane przez naciśnięcie znaku dolara na klawiaturze w oknie wyrażenia. Jednym z dostępnych parametrów jest nasza Właściwość video1bitrate, która została opublikowana wcześniej.

![Uzyskiwanie dostępu do parametrów w wyrażeniu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Uzyskiwanie dostępu do parametrów w wyrażeniu*

Wykonaj te same czynności dla plików wyjściowych dla drugiego wideo:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4`

i dla plików wyjściowych tylko audio:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4`

Jeśli teraz zmienimy szybkość transmisji bitów dla dowolnego pliku wideo lub audio, odpowiedni koder zostanie ponownie skonfigurowany, a konwencja nazw plików oparta na szybkości transmisji bitów zostanie wykorzystana automatycznie.

## <a name="adding-thumbnails-to-multibitrate-mp4-output"></a><a id="thumbnails_to__multibitrate_MP4"></a>Dodawanie miniatur do danych wyjściowych w formacie wieloszybkościowej
Rozpoczynając od przepływu pracy, który generuje [dane wyjściowe z przeprowadzeniem wieloszybkościowego wyszukiwania z danych wejściowych MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), będziemy teraz dodawać miniatury do danych wyjściowych.

### <a name="workflow-overview-to-add-thumbnails-to"></a><a id="thumbnails_to__multibitrate_MP4_overview"></a>Omówienie przepływu pracy, w którym można dodać miniatury
![Uruchamianie przepływu pracy MP4 z wieloszybkościową usługą](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Uruchamianie przepływu pracy MP4 z wieloszybkościową usługą*

### <a name="adding-jpg-encoding"></a><a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Dodawanie kodowania JPG
Serca naszej generacji miniatury będzie składnikiem kodera JPG, który umożliwia wyprowadzanie plików JPG.

![Koder JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Koder JPG*

Nie można jednak bezpośrednio połączyć naszego nieskompresowanego strumienia wideo z pliku multimedialnego do kodera JPG. Zamiast tego oczekuje się, że poszczególne ramki są przekazywane. Można to zrobić za pomocą składnika bramy klatek wideo.

![Podłączanie bramy Frames do kodera JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Podłączanie bramy Frames do kodera JPG*

Brama klatek raz na wiele sekund lub ramki umożliwia przekazanie ramki wideo. Interwał i przesunięcie czasu, w którym ma to miejsce, można skonfigurować we właściwościach.

![Właściwości bramy ramki wideo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Właściwości bramy ramki wideo*

Utwórzmy miniaturę co minutę przez ustawienie trybu na czas (w sekundach) i interwału do 60.

### <a name="dealing-with-color-space-conversion"></a><a id="thumbnails_to__multibitrate_MP4_color_space"></a>Postępowanie z konwersją przestrzeni kolorów
Mimo że może to być logicznie nieskompresowanymi pinezkami wideo bramy Frame, a dane wejściowe pliku multimedialnego mogą teraz być połączone, w razie potrzeby zostanie wyświetlone ostrzeżenie.

![Błąd wejściowego miejsca koloru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Błąd wejściowego miejsca koloru*

Dzieje się tak, ponieważ sposób, w jaki informacje o kolorach są reprezentowane w oryginalnym nieprzetworzonym nieskompresowanym strumieniu wideo, pochodzące z naszych MXFów, różni się od tego, co oczekuje koder JPG. Dokładniej mówiąc, oczekiwano "przestrzeni kolorów" RGB lub skali odcieni szarości. Oznacza to, że strumień wideo dla ruchu przychodzącego z bramą klatek wideo musi mieć zastosowaną konwersję na początku.

Przeciągnij na przepływ pracy konwertera przestrzeni kolorów — Intel i połącz go z bramą klatek.

![Łączenie convertor przestrzeni kolorów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Łączenie convertor przestrzeni kolorów*

W oknie właściwości wybierz wpis BGR 24 z listy ustawień wstępnych.

### <a name="writing-the-thumbnails"></a><a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Pisanie miniatur
W porównaniu z naszym wideo MP4 składnik programu JPG Encoder wyprowadza więcej niż jeden plik. W celu pomyślnego przeprowadzenia tej operacji można użyć składnika zapisywania plików JPG wyszukiwania sceny: przenosi przychodzące miniatury JPG i zapisuje je, a każda nazwa pliku jest określana przez inną liczbę. (Liczba zazwyczaj wskazuje liczbę sekund/jednostek w strumieniu, z której została narysowana miniatura).

![Wprowadzenie do edytora plików JPG przeszukiwania sceny](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Wprowadzenie do edytora plików JPG przeszukiwania sceny*

Skonfiguruj Właściwość ścieżki folderu danych wyjściowych za pomocą wyrażenia: `${ROOT_outputWriteDirectory}`

i Właściwość prefiksu filename z:

`${ROOT_sourceFileBaseName}_thumb_`

Prefiks określa sposób nazywania plików miniatur. Są one sufiksem o liczbie wskazującej położenie miniatury w strumieniu.

![Właściwości edytora plików JPG przeszukiwania sceny](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Właściwości edytora plików JPG przeszukiwania sceny*

Podłącz składnik zapisywania plików JPG przeszukiwania sceny do węzła wyjściowego plik/zasób.

### <a name="detecting-errors-in-a-workflow"></a><a id="thumbnails_to__multibitrate_MP4_errors"></a>Wykrywanie błędów w przepływie pracy
Połącz dane wejściowe konwertera przestrzeni kolorów z nieprzetworzonymi nieskompresowanymi danymi wyjściowymi wideo. Teraz wykonaj lokalne uruchomienie testu dla przepływu pracy. Istnieje dobry szansa, że przepływ pracy nagle zatrzyma się i wskaże czerwony kontur na składniku, który napotkał błąd:

![Błąd konwertera przestrzeni kolorów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Błąd konwertera przestrzeni kolorów*

Kliknij małą czerwoną ikonę "E" w prawym górnym rogu składnika konwertera przestrzeni kolorów, aby zobaczyć przyczynę nieudanej próby kodowania.

![Okno dialogowe błędu konwertera przestrzeni kolorów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Okno dialogowe błędu konwertera przestrzeni kolorów*

Zostanie ona wyświetlona, tak jak widać, że przychodzący Standard przestrzeni kolorów dla konwertera przestrzeni kolorów musi być rec601 dla naszej zażądanej konwersji YUV na RGB. Prawdopodobnie nasza strumień nie wskazuje jej rec601. (REC 601 jest standardem do kodowania analogowych sygnałów wideo w postaci cyfrowego wideo. Określa aktywny region obejmujący przykłady luminancji 720 i 360 chrominance próbek na wiersz. System kodowania kolorów jest znany jako YCbCr 4:2:2.)

Aby rozwiązać ten problem, poinformujemy o metadanych naszego strumienia, którego dotyczy zawartość rec601. Aby to zrobić, użyjemy składnika Aktualizator typu danych wideo, który zostanie umieszczony między źródłem nieprzetworzonym a składnikiem konwersji miejsca na kolory. Ten typ danych Aktualizator umożliwia ręczne aktualizowanie niektórych właściwości typu danych wideo. Skonfiguruj ją tak, aby wskazywała standardową przestrzeń kolorów "Rec 601". Powoduje to, że typ danych wideo Aktualizator, aby oznaczyć strumień przy użyciu przestrzeni kolorów "Rec 601", jeśli nie zdefiniowano jeszcze przestrzeni kolorów. (Nie przesłania żadnych istniejących metadanych, chyba że zaznaczono pole wyboru przesłonięcia).

![Aktualizowanie standardowego miejsca na kolory dla typu danych Aktualizator](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Aktualizowanie standardowego miejsca na kolory dla typu danych Aktualizator*

### <a name="finished-workflow"></a><a id="thumbnails_to__multibitrate_MP4_finish"></a>Zakończony przepływ pracy
Teraz, gdy przepływ pracy został zakończony, wykonaj kolejną czynność testową, aby zobaczyć, że przechodzi.

![Zakończono przepływ pracy dla danych wyjściowych z obsługą wieloplikowego i miniatur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Zakończono przepływ pracy dla danych wyjściowych z obsługą wieloplikowego i miniatur*

## <a name="time-based-trimming-of-multibitrate-mp4-output"></a><a id="time_based_trim"></a>Przycinanie danych wyjściowych z szybkością transmisji bitów na podstawie czasu
Rozpoczynając od przepływu pracy, który generuje [dane wyjściowe z rozdzielczością wieloszybkościową z danych wejściowych MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), będziemy teraz przystąpić do przycinania źródłowego wideo na podstawie sygnatur czasowych.

### <a name="workflow-overview-to-start-adding-trimming-to"></a><a id="time_based_trim_start"></a>Omówienie przepływu pracy, aby rozpocząć dodawanie przycinania do
![Uruchamianie przepływu pracy w celu dodania przycinania do](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Uruchamianie przepływu pracy w celu dodania przycinania do*

### <a name="using-the-stream-trimmer"></a><a id="time_based_trim_use_stream_trimmer"></a>Używanie elementu dostosowującego do strumienia
Składnik dostosowujący strumień umożliwia przycinanie początkowych i końcowych danych wejściowych strumienia strumieniowego na podstawie informacji o chronometrażu (sekundy, minuty,...). Element dostosowujący nie obsługuje przycinania na podstawie ramek.

![Obiekt dostosowujący do strumienia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Obiekt dostosowujący do strumienia*

Zamiast łączyć kodery AVC i przypisanie osoby mówiącej do danych wejściowych plików multimedialnych, umieścimy między tymi elementami dostosowującymi do strumienia. (Jeden dla sygnału wideo i jeden dla sygnału audio z przeplotem).

![Umieść obiekt dostosowujący do strumienia między](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Umieść obiekt dostosowujący do strumienia między*

Skonfigurujmy element dostosowujący w taki sposób, aby przetwarzać wideo i dźwięk z zakresu od 15 sekund do 60 sekund.

Przejdź do właściwości elementu dostosowującego strumień wideo i skonfiguruj zarówno godzinę rozpoczęcia (15 s), jak i godzinę zakończenia (60 s). Aby upewnić się, że elementy dostosowujące audio i wideo są zawsze skonfigurowane na te same wartości początkowe i końcowe, publikujemy je w katalogu głównym przepływu pracy.

![Opublikuj właściwość czasu rozpoczęcia z elementu dostosowującego do strumienia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Opublikuj właściwość czasu rozpoczęcia z elementu dostosowującego do strumienia*

![Okno dialogowe Właściwości publikowania dla czasu rozpoczęcia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Okno dialogowe Właściwości publikowania dla czasu rozpoczęcia*

![Okno dialogowe Właściwości publikowania dla czasu zakończenia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Okno dialogowe Właściwości publikowania dla czasu zakończenia*

Jeśli teraz sprawdzimy katalog główny naszego przepływu pracy, obie właściwości są w tym miejscu widoczne i można je skonfigurować.

![Opublikowane właściwości dostępne w katalogu głównym](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Opublikowane właściwości dostępne w katalogu głównym*

Teraz otwórz właściwości przycinania z elementu dostosowujący audio i skonfiguruj godziny rozpoczęcia i zakończenia z wyrażeniem, które odwołuje się do opublikowanych właściwości w katalogu głównym naszego przepływu pracy.

W przypadku czasu rozpoczęcia przycinania audio:

`${ROOT_TrimmingStartTime}`

i na czas zakończenia:

`${ROOT_TrimmingEndTime}`

### <a name="finished-workflow"></a><a id="time_based_trim_finish"></a>Zakończony przepływ pracy
![Zakończony przepływ pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Zakończony przepływ pracy*

## <a name="introducing-the-scripted-component"></a><a id="scripting"></a>Wprowadzenie do składnika skryptowego
Składniki inicjowane przez skrypty mogą wykonywać dowolne skrypty w fazach wykonywania przepływu pracy. Istnieją cztery różne skrypty, które mogą być wykonywane, z których każdy ma określoną charakterystykę, oraz ich własne miejsce w cyklu życia przepływu pracy:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

Dokumentacja składnika skryptowego zawiera więcej szczegółów dla każdego z powyższych. W [poniższej sekcji](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)składnik **realizeScript** Scripting służy do konstruowania cliplist XML na bieżąco podczas uruchamiania przepływu pracy. Ten skrypt jest wywoływany podczas instalacji składnika, która jest wykonywana tylko raz w jego cyklu życia.

### <a name="scripting-within-a-workflow-hello-world"></a><a id="scripting_hello_world"></a>Wykonywanie skryptów w ramach przepływu pracy: Hello World
Przeciągnij składnik skryptu na powierzchnię projektanta i zmień jego nazwę (na przykład "SetClipListXML").

![Dodawanie składnika skryptowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Dodawanie składnika skryptowego*

Podczas inspekcji właściwości składnika skryptowego zostaną wyświetlone cztery różne typy skryptów, z których każdy można skonfigurować w innym skrypcie.

![Właściwości składnika skryptowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Właściwości składnika skryptowego*

Wyczyść processInputScript i Otwórz edytor dla realizeScript. Teraz wszystko gotowe do rozpoczęcia tworzenia skryptów.

Skrypty są zapisywane w Groovy, dynamicznie skompilowanym języku skryptowym dla platformy Java, która zachowuje zgodność z językiem Java. W rzeczywistości większość kodu Java jest prawidłowym kodem Groovy.

Napiszmy prosty skrypt Hello World Groovy w kontekście naszego realizeScript. W edytorze wprowadź następujące elementy:

`node.log("hello world");`

Wykonaj teraz lokalne uruchomienie testu. Po wykonaniu tego działania Sprawdź, czy na karcie system w składniku skryptowym jest właściwość logs.

![Dane wyjściowe dziennika Hello World](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Dane wyjściowe dziennika Hello World*

Obiekt węzła, dla którego wywoływana jest metoda log, odnosi się do naszego bieżącego "węzła" lub składnika, w którym wykonujemy skrypty. Każdy składnik jako taki ma możliwość wyprowadzania danych rejestrowania, które są dostępne za pomocą karty system. W tym przypadku wyprowadzamy literał ciągu "Hello World". Ważne jest, aby zrozumieć, że może to być niecenne narzędzie do debugowania, które zapewnia wgląd w to, co robi skrypt.

W naszym środowisku skryptowym mamy również dostęp do właściwości w innych składnikach. Wypróbuj te metody:

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

W naszym oknie dziennika wyświetlane są następujące elementy:

![Dane wyjściowe dziennika do uzyskiwania dostępu do ścieżek węzłów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Dane wyjściowe dziennika do uzyskiwania dostępu do ścieżek węzłów*

## <a name="frame-based-trimming-of-multibitrate-mp4-output"></a><a id="frame_based_trim"></a>Przycinanie danych wyjściowych z wieloszybkościową obsługą ramek
Rozpoczynając od przepływu pracy, który generuje [dane wyjściowe z przeprowadzeniem wieloszybkościowego wyszukiwania z danych wejściowych MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), będziemy teraz przystąpić do przycinania źródłowego wideo na podstawie liczby klatek.

### <a name="blueprint-overview-to-start-adding-trimming-to"></a><a id="frame_based_trim_start"></a>Przegląd strategii, aby rozpocząć dodawanie przycinania do
![Przepływ pracy umożliwiający rozpoczęcie dodawania przycinania do](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Przepływ pracy umożliwiający rozpoczęcie dodawania przycinania do*

### <a name="using-the-clip-list-xml"></a><a id="frame_based_trim_clip_list"></a>Korzystanie z XML listy klipów
We wszystkich samouczkach poprzedniego przepływu pracy używamy składnika danych wejściowych pliku multimedialnego jako źródła danych wejściowych wideo. W tym konkretnym scenariuszu zamiast tego będzie używany składnik źródła listy klipów. Nie powinno to być preferowany sposób pracy; Źródła listy klipów należy używać tylko wtedy, gdy istnieje rzeczywisty powód do wykonania tej czynności (podobnie jak w przypadku korzystania z funkcji przycinania listy klipów).

Aby przełączać dane wejściowe z pliku multimedialnego do źródła listy klipów, przeciągnij składnik źródła listy klipów na powierzchnię projektu i Połącz kod PIN XML listy klipów z węzłem XML listy klipów w Projektancie przepływu pracy. Spowoduje to wypełnienie listy klipów wyjściowych przez numery PIN, zgodnie z naszym wejściowym wideo. Teraz można połączyć nieskompresowane wideo i nieskompresowane numery PIN ze źródła listy klipów z odpowiednimi koderami AVC i przeplotem strumienia audio. Teraz Usuń dane wejściowe pliku multimedialnego.

![Zamieniono dane wejściowe pliku multimedialnego ze źródłem listy klipów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Zamieniono dane wejściowe pliku multimedialnego ze źródłem listy klipów*

Składnik źródłowy listy klipów przyjmuje jako dane wejściowe "plik XML listy klipów". Po wybraniu pliku źródłowego do lokalnego przetestowania plik ten XML jest wypełniany automatycznie.

![Automatycznie wypełniona Właściwość XML listy klipów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automatycznie wypełniona Właściwość XML listy klipów*

Dokładniejsze spojrzenie na kod XML — wygląda następująco:

![Edytuj listę klipów — okno dialogowe](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Edytuj listę klipów — okno dialogowe*

Nie odzwierciedla to jednak możliwości XML listy klipów. Jedną z opcji jest dodanie elementu "Trim" w ramach źródła wideo i audio, takiego jak:

![Dodawanie elementu przycinania do listy klipów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Dodawanie elementu przycinania do listy klipów*

Jeśli zmodyfikujesz plik XML z listą klipów tak jak powyżej i wykonasz lokalny przebieg testowy, zobaczysz, że film wideo został poprawnie przycięty do 10 i 20 sekund w filmie wideo.

W przeciwieństwie do tego, co się stanie, gdy wykonasz lokalne uruchomienie, ten sam kod XML cliplist nie będzie miał tego samego skutku w przypadku zastosowania w przepływie pracy, który jest uruchamiany w Azure Media Services. Po rozpoczęciu usługi Azure Premium Encoder cliplist XML jest generowany za każdym razem, na podstawie pliku wejściowego, które zostało określone zadanie kodowania. Oznacza to, że wszelkie zmiany w kodzie XML zostałyby pominięte.

Aby wyeliminować cliplist XML, które są czyszczone po rozpoczęciu zadania kodowania, można je ponownie wygenerować na bieżąco po rozpoczęciu przepływu pracy. Takie akcje niestandardowe można wykonać za pomocą tego, co jest nazywane "składnikiem skryptowym". Aby uzyskać więcej informacji, zobacz [wprowadzenie do składnika skryptowego](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Przeciągnij składnik skryptu na powierzchnię projektanta i zmień jego nazwę na "SetClipListXML".

![Dodawanie składnika skryptowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Dodawanie składnika skryptowego*

Podczas inspekcji właściwości składnika skryptowego są wyświetlane cztery różne typy skryptów, każdy konfigurowalny do innego skryptu.

![Właściwości składnika skryptowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Właściwości składnika skryptowego*

### <a name="modifying-the-clip-list-from-a-scripted-component"></a><a id="frame_based_trim_modify_clip_list"></a>Modyfikowanie listy klipów ze składnika skryptowego
Zanim będziemy mogli ponownie napisać plik XML cliplist, który jest generowany podczas uruchamiania przepływu pracy, musimy mieć dostęp do właściwości i zawartości XML cliplist. Można to zrobić w następujący sposób:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Zarejestrowana lista klipów przychodzących](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Zarejestrowana lista klipów przychodzących*

Najpierw musimy określić, od którego punktu ma być przycinania wideo. Aby to umożliwić użytkownikowi mniej technicznym przepływie pracy, Opublikuj dwie właściwości w katalogu głównym grafu. Aby to zrobić, kliknij prawym przyciskiem myszy powierzchnię projektanta i wybierz pozycję "Dodaj właściwość":

* Pierwsza Właściwość: "ClippingTimeStart" typu: "TIMECODE"
* Druga Właściwość: "ClippingTimeEnd" typu: "TIMECODE"

![Dodaj okno dialogowe właściwości dla czasu rozpoczęcia wycinka](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Dodaj okno dialogowe właściwości dla czasu rozpoczęcia wycinka*

![Właściwości czasowe wycinania, które są publikowane w głównym przepływie pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Właściwości czasowe wycinania, które są publikowane w głównym przepływie pracy*

Skonfiguruj obie właściwości do odpowiedniej wartości:

![Skonfiguruj właściwości Start i End wycinka](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Skonfiguruj właściwości Start i End wycinka*

Teraz w naszym skrypcie możemy uzyskać dostęp do obu właściwości, takich jak:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Okno Dziennik przedstawiające początek i koniec wycinka](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Okno Dziennik przedstawiające początek i koniec wycinka*

Przeanalizujmy ciąg Timecode w wygodniejszy do użycia formularz, używając prostego wyrażenia regularnego:

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![Okno dziennika z danymi wyjściowymi przeanalizowanego Timecode](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Okno dziennika z danymi wyjściowymi przeanalizowanego Timecode*

Dzięki tym informacjom można teraz zmodyfikować plik XML cliplist, aby odzwierciedlał czas rozpoczęcia i zakończenia dla żądanego wycinka ramki.

![Kod skryptu służący do dodawania elementów przycinania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Kod skryptu służący do dodawania elementów przycinania*

Zostało to zrobione przez normalne operacje manipulowania ciągami. Zmodyfikowany plik XML z wynikiem modyfikacji jest zapisywana z powrotem do właściwości clipListXML w katalogu głównym przepływu pracy za pomocą metody "setProperty". W oknie dziennika po kolejnym uruchomieniu testu zostaną wyświetlone następujące elementy:

![Rejestrowanie listy wycinków z wynikiem](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Rejestrowanie listy wycinków z wynikiem*

Wykonaj Test-Run, aby zobaczyć, jak strumienie wideo i audio zostały obcięte. Podobnie jak w przypadku więcej niż jednego przebiegu testowego z różnymi wartościami dla punktów przycinania, należy zauważyć, że nie zostaną one jednak uwzględnione. Przyczyną tego jest to, że projektant, w przeciwieństwie do środowiska uruchomieniowego platformy Azure, nie przesłania pliku XML cliplist w każdym przebiegu. Oznacza to, że tylko po raz pierwszy ustawiono punkty w i out, spowoduje to przekształcenie pliku XML, a wszystkie inne, nasze klauzule Guard (if ( `clipListXML.indexOf("<trim>") == -1` )) uniemożliwią przepływowi pracy dodanie innego elementu przycinania, gdy już istnieje.

Aby nasz przepływ pracy był wygodny do przetestowania lokalnego, najlepiej dodać kod, który sprawdza, czy element Trim już istnieje. Jeśli tak, możemy ją usunąć przed kontynuowaniem, modyfikując plik XML z nowymi wartościami. Zamiast korzystać z operacji w postaci zwykłego ciągu, jest to raczej bezpieczniejsze do wykonania za pośrednictwem rzeczywistej analizy modelu obiektów XML.

Zanim będziemy mogli dodać taki kod, musimy najpierw dodać kilka instrukcji importu na początku naszego skryptu:

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

Po wykonaniu tej czynności możemy dodać wymagany kod czyszczący:

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

Ten kod przechodzi tuż powyżej punktu, w którym dodamy elementy przycinania do cliplist XML.

W tym momencie możemy uruchomić i zmodyfikować nasz przepływ pracy tak, jak chcemy, aby zmiany były stosowane kiedykolwiek.    

### <a name="adding-a-clippingenabled-convenience-property"></a><a id="frame_based_trim_clippingenabled_prop"></a>Dodawanie właściwości wygody ClippingEnabled
Ponieważ nie zawsze chcesz przystąpić do przycinania, Zakończmy pracę nad naszym przepływem pracy, dodając wygodną flagę logiczną, która wskazuje, czy chcesz włączyć przycinanie/przycinanie.

Tak jak wcześniej, Opublikuj nową właściwość w katalogu głównym naszego przepływu pracy o nazwie "ClippingEnabled" typu "BOOLEAN".

![Opublikowanie właściwości do włączania przycinania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Opublikowanie właściwości do włączania przycinania*

Korzystając z poniższej prostej klauzuli Guard, możemy sprawdzić, czy przycinanie jest wymagane i zdecydować, czy nasza lista klipów powinna być modyfikowana.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a name="complete-code"></a><a id="code"></a>Kompletny kod

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>Zobacz też
[Wprowadzenie kodowania Premium w Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Jak używać kodowania Premium w Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Kodowanie zawartości na żądanie za pomocą usługi Azure Media Service](media-services-encode-asset.md#media-encoder-premium-workflow)

[Formaty i kodeki usługi Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)

[Przykładowe pliki przepływu pracy](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Narzędzie Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
