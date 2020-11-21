---
title: Transkrypcja centrum wywołania — usługa mowy
titleSuffix: Azure Cognitive Services
description: Typowy scenariusz dla zamiany mowy na tekst to jego przepisywania duże ilości danych telefonii, które pochodzą z różnych systemów, takich jak interaktywna Odpowiedź głosowa (IVR). Korzystając z usługi mowy i ujednoliconego modelu mowy, firma może uzyskać zapisy wysokiej jakości z systemami przechwytywania audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 19d4cc388494e149b7f258a8e9f154041a3dd070
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021970"
---
# <a name="speech-service-for-telephony-data"></a>Usługa mowy dla danych telefonii

Dane telefoniczne generowane za pomocą telefonu stacjonarnego, telefonów komórkowych i odbiorników radiowych są zwykle niskiej jakości i są wydawane w zakresie 8 KHz, które tworzą wyzwania podczas konwertowania zamiany mowy na tekst. Najnowsze modele rozpoznawania mowy z usługi mowy w programie Excel pod adresem jego przepisywania te dane, nawet w przypadkach, gdy dane są trudne do zrozumienia przez człowieka. Modele te są przeszkolone z dużymi ilościami danych telefonicznych i zapewniają dokładność rozpoznawania najlepszych w rynku, nawet w środowiskach z zakłóceniami.

Typowy scenariusz dla zamiany mowy na tekst to jego przepisywania duże ilości danych telefonii, które mogą pochodzić z różnych systemów, takich jak interaktywna Odpowiedź głosowa (IVR). Te systemy mogą być stereofoniczne stereo lub mono oraz RAW z niewielką ilością operacji przetwarzania post na tym sygnale. Korzystając z usługi mowy i ujednoliconego modelu mowy, firma może uzyskać transkrypcje o wysokiej jakości, niezależnie od tego, które systemy są używane do przechwytywania audio.

Dane telefonii mogą służyć do lepszego zrozumienia potrzeb klientów, zidentyfikowania nowych szans marketingowych lub oceny wydajności agentów centrum połączeń. Po uzyskanego danych firma może używać danych wyjściowych do celów, takich jak ulepszona Telemetria, identyfikowanie kluczowych fraz lub analizowanie tonacji klienta.

Technologie opisane na tej stronie są wewnętrznie przez firmę Microsoft w przypadku różnych usług przetwarzania wywołań w czasie rzeczywistym i w trybie wsadowym.

Zapoznaj się z częścią technologii i pokrewnych funkcji oferowanych przez usługę Speech Service.

> [!IMPORTANT]
> Ujednolicony model usługi Speech Service jest szkolony z różnymi danymi i oferuje jednomodelowe rozwiązanie w wielu scenariuszach od dyktowania do analizy telefonii.

## <a name="azure-technology-for-call-centers"></a>Technologia platformy Azure dla centrów wywołań

Poza funkcjonalnym aspektem funkcji usługi mowy, ich podstawowym przeznaczeniem — w przypadku zastosowania do centrum wywołania — należy poprawić środowisko pracy klienta. W tej kwestii istnieją trzy puste domeny:

- Analiza po wywołaniu, która polega na przetwarzaniu wsadowym nagrań wywołań po wywołaniu.
- Analiza w czasie rzeczywistym, która przetwarza sygnał audio w celu wyodrębnienia różnych szczegółowych informacji, gdy odbywa się wywoływanie (z tonacjiem w widocznym przypadku użycia).
- Asystentów głosowych (botów), które umożliwiają przeprowadzenie dialogu między klientem a bot w celu rozwiązania problemu klienta bez udziału w agencie lub zastosowania protokołu sztucznej analizy (AI) do ułatwienia działania agenta.

Typowy diagram architektury implementacji scenariusza wsadowego przedstawiono na ilustracji poniżej ![ architektury transkrypcji centrum wywołania](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Składniki technologii analizy mowy

Niezależnie od tego, czy domena jest wywoływana po wywołaniu, czy w czasie rzeczywistym, platforma Azure oferuje zestaw codziennych i powstających technologii w celu poprawy obsługi klienta.

### <a name="speech-to-text-stt"></a>Zamiana mowy na tekst (monitora STT)

Funkcja [zamiany mowy na tekst](speech-to-text.md) jest najbardziej poszukiwaną funkcją w dowolnym rozwiązaniu obsługi centrum połączeń. Ze względu na to, że wiele procesów analizy z przedziału polega na uzyskanego tekstu, współczynnik błędów wyrazów (_wer_) ma największą ważność. Jednym z najważniejszych wyzwań związanych z transkrypcją centrum rozmów jest szum, który jest powszechnie rozpowszechniony w centrum wywołania (na przykład inne agenci mówiący w tle), bogaty zakres ustawień regionalnych i dialektów języka oraz niska jakość rzeczywistego sygnału telefonu. Funkcja Raportowanie błędów systemu Windows jest wysoce skorelowana z tym, jak dobrze są przeszkolone modele akustyczne i językowe dla danego ustawienia regionalnego, a tym samym możliwość dostosowania modelu do ustawień regionalnych jest ważna. Nasze najnowsze ujednolicone modele w wersji 4. x to rozwiązanie do dokładności i opóźnień transkrypcji. Nauczenie się z dziesiątki tysięcy godzin danych akustycznych i miliardów informacji leksykalnych, ujednolicone modele są najdokładniejszymi modelami na rynku do transkrypcja danych centrum połączeń.

### <a name="sentiment"></a>Opinia

Oceny, czy klient był dobrym doświadczeniem, jest jednym z najważniejszych obszarów analizy mowy w przypadku zastosowania do obszaru Centrum wywołania. Nasz [interfejs API transkrypcji usługi Batch](batch-transcription.md) oferuje analizę tonacji na wypowiedź. Można agregować zbiór wartości uzyskanych jako część transkrypcji wywołania, aby określić tonacji wywołań dla agentów i klienta.

### <a name="silence-non-talk"></a>Wyciszenie (bez rozmowy)

Nie jest to nietypowe w przypadku 35% wywołania pomocy technicznej, aby wywoływać czas braku rozmowy. Niektóre scenariusze, w przypadku których występuje niekomunikacja: agenci szukający wcześniejszej historii przypadków przy użyciu klienta, agenci, którzy umożliwiają im dostęp do pulpitu klienta i wykonują funkcje, klienci czekają na przeniesienie i tak dalej. Niezwykle ważne jest, aby określić, kiedy ciszy występuje w wywołaniu, ponieważ istnieje wiele ważnych sensitivities klientów, które występują w tym typie scenariuszy i gdzie występują w wywołaniu.

### <a name="translation"></a>Tłumaczenie

Niektóre firmy są eksperymentować z przekazywaniem przetłumaczonych transkrypcji od wywołań obsługi języka obcego, dzięki czemu menedżerowie ds. dostarczania mogą zrozumieć światowe środowisko klientów. Nasze możliwości [tłumaczenia](./speech-translation.md) są niezbyt nadmiarowe. Możemy przetłumaczyć dźwięk na dźwięk lub dźwięk na tekst dla dużej liczby ustawień regionalnych.

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

[Zamiana tekstu na mowę](text-to-speech.md) jest innym ważnym obszarem wdrażania botów, które współdziałają z klientami. Typowym profilem jest to, że klient mówi, że jego głos jest uzyskanego do tekstu, a tekst jest analizowany pod kątem intencji, odpowiedź jest obliczana na podstawie rozpoznanego celu, a następnie jest generowana przez klienta. Oczywiście wszystkie te czynności należy wykonać szybko — w tym przypadku małe opóźnienia są ważnym składnikiem w przypadku powodzenia tych systemów.

Nasze opóźnienia kompleksowe są znacznie niskie dla różnych technologii, takich jak [Zamiana mowy na tekst](speech-to-text.md), [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [bot Framework](https://dev.botframework.com/), [zamiany tekstu na mowę](text-to-speech.md).

Nasze nowe głosy są również odróżniane od głosów ludzkich. Możesz użyć naszych głosów, aby bot swój unikatowy charakter.

### <a name="search"></a>Wyszukiwanie

Kolejną zszywaniem funkcji analitycznych jest zidentyfikowanie interakcji w przypadku wystąpienia konkretnego zdarzenia lub doświadczenia. Zwykle jest to wykonywane z jednym z dwóch metod; Wyszukiwanie ad hoc, w którym użytkownik po prostu wpisuje frazę i system odpowiada, lub bardziej strukturalne zapytanie, gdzie analityk może utworzyć zestaw instrukcji logicznych, które identyfikują scenariusz w wywołaniu, a następnie każde wywołanie może być indeksowane względem tego zestawu zapytań. Dobrym przykładem wyszukiwania jest Powszechna Deklaracja zgodności "to wywołanie jest rejestrowane w celach jakościowych... ". W wielu firmach chcemy upewnić się, że ich agenci dostarczą tej odpowiedzialności klientom przed faktycznym zarejestrowaniem połączenia. Większość systemów analitycznych ma możliwość trendu zachowań znalezionych przez algorytmy zapytań i wyszukiwania, a to raportowanie trendów jest ostatecznie jedną z najważniejszych funkcji systemu analitycznego. Za pomocą [katalogu usług poznawczych](https://azure.microsoft.com/services/cognitive-services/directory/search/) kompleksowe rozwiązanie można znacznie rozszerzyć za pomocą funkcji indeksowania i wyszukiwania.

### <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Ten obszar jest jednym z trudniejszych aplikacji analitycznych, które korzystają z aplikacji AI i uczenia maszynowego. Podstawowym scenariuszem w tym przypadku jest wywnioskowanie intencji klienta. Dlaczego klient dzwoni? Co to jest problem z klientem? Dlaczego klient ma negatywny wpływ? Nasza [Usługa analizy tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) udostępnia zestaw analiz, dzięki którym można szybko uaktualnić kompleksowe rozwiązanie do wyodrębniania ważnych słów kluczowych lub fraz.

Teraz przyjrzyjmy się przetwarzaniu wsadowemu i potokom w czasie rzeczywistym na potrzeby rozpoznawania mowy na nieco bardziej szczegółowym poziomie.

## <a name="batch-transcription-of-call-center-data"></a>Transkrypcja partii danych centrum połączeń

W przypadku usługi jego przepisywania Bulk audio opracowano [interfejs API transkrypcji usługi Batch](batch-transcription.md). Interfejs API transkrypcji usługi Batch został opracowany z myślą o transkrypcja dużej ilości danych dźwiękowych asynchronicznie. W odniesieniu do danych w centrum połączeń jego przepisywania nasze rozwiązanie jest oparte na następujących filarach:

- **Dokładność** — za pomocą ujednoliconych modeli z czwartą generację oferuje przekroczenie jakości transkrypcji.
- **Opóźnienie** — rozumiemy, że podczas przeprowadzania zbiorczych transkrypcji, transkrypcje są zbędne szybko. Zadania transkrypcji zainicjowane za pośrednictwem [interfejsu API transkrypcji usługi Batch](batch-transcription.md) będą natychmiast umieszczane w kolejce, a po uruchomieniu zadania są wykonywane szybciej niż transkrypcja w czasie rzeczywistym.
- **Zabezpieczenia** — rozumiemy, że wywołania mogą zawierać poufne dane. Zagwarantowanie, że bezpieczeństwo jest jednym z naszych największych priorytetów. Nasza usługa uzyskała certyfikaty ISO, SOC, HIPAA i PCI.

Centra wywołań generują codziennie duże ilości danych audio. Jeśli firma przechowuje dane telefoniczne w centralnej lokalizacji, takiej jak usługa Azure Storage, można użyć [interfejsu API transkrypcji usługi Batch](batch-transcription.md) do asynchronicznego żądania i otrzymywania transkrypcji.

Typowe rozwiązanie używa tych usług:

- Usługa mowy służy do transkrypcja zamiany mowy na tekst. Standardowa subskrypcja (S0) usługi mowy jest wymagana do korzystania z interfejsu API transkrypcji partii. Bezpłatne subskrypcje (F0) nie będą działały.
- [Usługa Azure Storage](https://azure.microsoft.com/services/storage/) służy do przechowywania danych telefonicznych i transkrypcji zwróconych przez interfejs API transkrypcji partii. To konto magazynu powinno używać powiadomień, w odniesieniu do gdy dodawane są nowe pliki. Te powiadomienia są używane do wyzwalania procesu transkrypcji.
- [Azure Functions](../../azure-functions/index.yml) służy do tworzenia identyfikatora URI sygnatury dostępu współdzielonego (SAS) dla każdego rejestrowania i wyzwalania żądania HTTP post w celu rozpoczęcia transkrypcji. Ponadto Azure Functions służy do tworzenia żądań pobrania i usunięcia transkrypcji przy użyciu interfejsu API transkrypcji usługi Batch.

Wewnętrznie korzystamy z powyższych technologii do obsługi wywołań klientów firmy Microsoft w trybie wsadowym.
:::image type="content" source="media/scenarios/call-center-batch-pipeline.png" alt-text="Technologie używane do obsługi wywołań klientów firmy Microsoft w trybie wsadowym.":::

## <a name="real-time-transcription-for-call-center-data"></a>Transkrypcja w czasie rzeczywistym dla danych centrum wywołań

Niektóre firmy muszą transkrypcja konwersacje w czasie rzeczywistym. Transkrypcja w czasie rzeczywistym może służyć do identyfikowania słów kluczowych i wyzwalania wyszukiwania zawartości i zasobów związanych z konwersacją, monitorowania tonacji, w celu usprawnienia ułatwienia dostępu lub do przekazywania tłumaczeń dla klientów i agentów, którzy nie są natywnymi głośnikami.

W scenariuszach wymagających transkrypcji w czasie rzeczywistym zalecamy użycie [zestawu Speech SDK](speech-sdk.md). Obecnie funkcja zamiany mowy na tekst jest dostępna w [ponad 20 językach](language-support.md), a zestaw SDK jest dostępny w językach C++, C#, Java, Python, Node.js, zamierzenia C i JavaScript. Przykłady są dostępne w każdym języku w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Najnowsze wiadomości i aktualizacje znajdują się w temacie [Informacje o wersji](releasenotes.md).

Wewnętrznie korzystamy z powyższych technologii do analizy w czasie rzeczywistym wywołań klientów firmy Microsoft w miarę ich występowania, jak pokazano na poniższym diagramie.

![Architektura partii](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Słowo w IVRs

Usługę mowy można łatwo zintegrować w dowolnym rozwiązaniu za pomocą [zestawu Speech SDK](speech-sdk.md) lub [interfejsu API REST](./overview.md#reference-docs). Jednak transkrypcja centrum połączeń może wymagać dodatkowych technologii. Zwykle jest wymagane połączenie między systemem IVR i platformą Azure. Chociaż nie oferujemy takich składników, w tym artykule opisano, co wiąże się z połączeniem z IVR.

Kilka produktów usługi IVR lub usług telefonii (takich jak Genesys lub AudioCodes) oferuje możliwości integracji, których można użyć w celu włączenia przekazywania audio przychodzącego i wychodzącego do usługi platformy Azure. W zasadzie niestandardowa usługa platformy Azure może udostępnić określony interfejs do definiowania sesji połączeń telefonicznych (takich jak wywołanie uruchomienia lub wywołania końcowego) i uwidocznić interfejs API protokołu WebSocket, który będzie odbierać strumienie audio dla ruchu przychodzącego, który jest używany z usługą mowy. Odpowiedzi wychodzące, takie jak transkrypcja konwersacji lub połączenia z platformą bot, można wyszukiwać za pomocą usługi zamiany tekstu na mowę firmy Microsoft i zwrócić do IVR na potrzeby odtwarzania.

Innym scenariuszem jest bezpośrednia integracja z protokołem inicjowania sesji (SIP). Usługa platformy Azure nawiązuje połączenie z serwerem SIP, dzięki czemu uzyskuje strumień przychodzący i strumień wychodzący, który jest używany na potrzeby faz zamiany mowy na tekst i zamiany tekstu na mowę. Aby nawiązać połączenie z serwerem SIP, istnieją komercyjne oferty oprogramowania, takie jak Ozeki SDK, lub [zespoły wywołujące i zajmujące się interfejsem API](/graph/api/resources/communications-api-overview) (obecnie w wersji beta), które są przeznaczone do obsługi tego typu scenariusza dla wywołań audio.

## <a name="customize-existing-experiences"></a>Dostosowywanie istniejących środowisk

 Usługa Speech działa dobrze z wbudowanymi modelami. Można jednak dodatkowo dostosowywać i dostrajać środowisko dla danego produktu lub środowiska. Opcje dostosowania przestają być od modelu akustycznego na unikatowe czcionki głosowe dla marki. Po skompilowaniu modelu niestandardowego można używać go z dowolnym funkcją usługi mowy w trybie w czasie rzeczywistym lub wsadowym.

| Usługa rozpoznawania mowy | Model | Opis |
| -------------- | ----- | ----------- |
| Zamiana mowy na tekst | [Model akustyczny](./how-to-custom-speech-train-model.md) | Utwórz niestandardowy model akustyczny dla aplikacji, narzędzi lub urządzeń używanych w określonych środowiskach, takich jak samochód lub piętro fabryki, z których każdy ma określone warunki rejestrowania. Przykłady obejmują akcentowanie mowy, konkretne zakłócenia w tle lub użycie określonego mikrofonu do nagrywania. |
|                | [Model językowy](./how-to-custom-speech-train-model.md) | Utwórz niestandardowy model języka w celu usprawnienia transkrypcji słownictwa i gramatyki właściwych dla branży, takich jak Terminologia medyczna lub żargon IT. |
|                | [Model wymowy](./how-to-custom-speech-train-model.md) | Przy użyciu niestandardowego modelu wymowy można zdefiniować formularz fonetyczny i wyświetlić wyraz lub termin. Jest to przydatne do obsługi niestandardowych warunków, takich jak nazwy produktów lub akronimy. Wszystko, co musisz zrobić, to plik wymowy, który jest prostym `.txt` plikiem. |
| Zamiana tekstu na mowę | [Czcionka głosowa](./how-to-custom-voice-create-voice.md) | Niestandardowe czcionki głosowe umożliwiają utworzenie rozpoznawalnego, jednoczęściowego głosu dla marki. Rozpoczęcie pracy wymaga jedynie niewielkiej ilości danych. Im więcej danych zapewniasz, tym bardziej naturalna i przypominająca Twoja czcionka głosu będzie dźwiękowa. |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod jest dostępny w witrynie GitHub dla każdej funkcji usługi Speech Service. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz praca z modelami niestandardowymi. Użyj tych linków, aby wyświetlić zestaw SDK i przykłady REST:

- [Przykłady zamiany mowy na tekst i mowy (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Przykłady transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Dokumentacja dokumentacji

- [Zestaw SDK rozpoznawania mowy](./speech-sdk.md)
- [Zestaw Speech Devices SDK](speech-devices-sdk.md)
- [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
- [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)
- [Interfejs API REST: Transkrypcja i dostosowywanie partii](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)