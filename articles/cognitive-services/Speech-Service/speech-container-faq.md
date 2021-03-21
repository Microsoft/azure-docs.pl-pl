---
title: Kontenery usługi mowy — często zadawane pytania
titleSuffix: Azure Cognitive Services
description: Instalowanie i uruchamianie kontenerów mowy. Funkcja zamiany mowy na tekst przekształca strumienie audio do tekstu w czasie rzeczywistym, które mogą być używane przez aplikacje, narzędzia lub urządzenia. Zamiana tekstu na mowę polega na konwertowaniu tekstu wejściowego na mowę, przypominającą człowieka.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 16158b4ecfb46ea9092fe9eeb31cc4dee259b1ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573748"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Kontenery usługi mowy — często zadawane pytania

W przypadku korzystania z usługi mowy z kontenerami należy polegać na tej kolekcji często zadawanych pytań przed eskalacją do pomocy technicznej. W tym artykule znajdują się pytania o różnym stopniu, od ogólnej do technicznych. Aby rozwinąć odpowiedź, kliknij pytanie.

## <a name="general-questions"></a>Pytania ogólne

<details>
<summary>
<b>Jak działają kontenery mowy i jak mam je skonfigurować?</b>
</summary>

**Odpowiedź:** Podczas konfigurowania klastra produkcyjnego należy wziąć pod uwagę kilka rzeczy. Po pierwsze skonfigurowanie jednego języka, wielu kontenerów na tym samym komputerze nie powinno być duże. Jeśli występują problemy, może to być problem związany z sprzętem — należy najpierw przyjrzeć się zasobowi, czyli Specyfikacje procesora i pamięci.

Rozważ chwilę, `ja-JP` kontener i najnowszy model. Model akustyczny jest najbardziej wymaganym PROCESORem, podczas gdy model języka wymaga najwięcej pamięci. Podczas testu porównawczego należy wykonać około 0,6 rdzeni procesora, aby przetworzyć pojedyncze żądanie zamiany mowy na tekst, gdy dźwięk jest przepływany w czasie rzeczywistym (na przykład z mikrofonu). Jeśli tworzysz dźwięk szybciej niż w czasie rzeczywistym (na przykład z pliku), to użycie może być podwojone (rdzenie 1,2 x). Tymczasem pamięć wymienioną poniżej jest pamięcią operacyjną do dekodowania mowy. *Nie uwzględnia on* rzeczywistego pełnego rozmiaru modelu języka, który będzie znajdował się w pamięci podręcznej plików. To jest `ja-JP` dodatkowy 2 GB; w przypadku `en-US` , może być większy (6-7 GB).

Jeśli masz maszynę, na której jest dostateczna ilość pamięci i próbujesz wdrożyć w niej wiele języków, możliwe jest, że pamięć podręczna plików jest pełna, a system operacyjny jest zmuszony do modelowania stron w i wychodzącym. W przypadku uruchomionego transkrypcji, który może być katastrofalne i może prowadzić do spowolnienia i innych implikacji wydajności.

Ponadto wstępnie pakujemy pliki wykonywalne dla maszyn z zestawem instrukcji [Advanced Vector Extension (AVX2)](speech-container-howto.md#advanced-vector-extension-support) . Maszyna z zestawem instrukcji AVX512 będzie wymagała generowania kodu dla tego obiektu docelowego, a uruchamianie 10 kontenerów dla 10 języków może tymczasowo obsłużyć procesor CPU. Komunikat taki jak ten zostanie wyświetlony w dzienniku platformy Docker:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Można ustawić liczbę dekoderów, które mają być umieszczone wewnątrz *jednego* kontenera przy użyciu `DECODER MAX_COUNT` zmiennej. Dlatego należy zacząć od jednostki SKU (procesor CPU/pamięć) i można zasugerować, jak najlepiej uzyskać najlepszą z nich. Doskonały punkt początkowy dotyczy zalecanych specyfikacji zasobów maszyn hosta.

<br>
</details>

<details>
<summary>
<b>Czy można pomóc w planowaniu pojemności i szacowaniu kosztów Premium kontenerów zamiany mowy na tekst?</b>
</summary>

**Odpowiedź:** W przypadku pojemności kontenera w trybie przetwarzania wsadowego każdy dekoder może obsłużyć 2-3-krotne w czasie rzeczywistym, z dwoma rdzeniami procesora, dla jednego rozpoznawania. Nie zalecamy zachowywania więcej niż dwóch współbieżnych rozpoznawania na wystąpienie kontenera, ale zaleca się uruchomienie większej liczby wystąpień kontenerów w celu zapewnienia niezawodności/dostępności za modułem równoważenia obciążenia.

Chociaż może istnieć każde wystąpienie kontenera działające z więcej dekoderami. Można na przykład skonfigurować 7 dekoderów na każde wystąpienie kontenera na osiem maszyn Core (co więcej niż 2 razy), dając 15X przepływność. Istnieje parametr, `DECODER_MAX_COUNT` który ma być świadomy. W przypadku wystąpienia ekstremalnych problemów z niezawodnością i opóźnieniami znacznie wzrasta przepływność. W przypadku mikrofonu zostanie osiągnięty czas rzeczywisty. Ogólne użycie powinno mieć co najmniej jeden rdzeń dla jednego rozpoznawania.

W przypadku scenariusza przetwarzania 1 K godz./dzień w trybie przetwarzania wsadowego w przypadku ekstremalnych przypadków 3 maszyny wirtualne mogły obsłużyć je w ciągu 24 godzin, ale nie są gwarantowane. Aby obsłużyć dni, przełączenia w tryb failover, aktualizacja i dostarczenie minimalnej kopii zapasowej/BCP, zalecamy 4-5 maszyn zamiast 3 na klaster oraz 2 klastrów.

Na potrzeby sprzętu używamy standardowej maszyny wirtualnej platformy Azure `DS13_v2` jako odniesienia (każdy rdzeń musi mieć 2,6 GHz lub lepszą) z włączonym zestawem instrukcji AVX2.

| Wystąpienie  | vCPU | Pamięć RAM    | Magazyn tymczasowy | Płatność zgodnie z rzeczywistym użyciem przy użyciu AHB | Rezerwacja roczna z AHB (% oszczędności) | 3-letni zarezerwowany dla AHB (% oszczędności) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598/godz.            | $0.3528/godz. (~ 41%)                 | $0.2333/godz. (~ 61%)                  |

W oparciu o informacje o projekcie (dwa klastry z 5 maszyn wirtualnych, aby obsłużyć 1 K godzinowe/dniowe przetwarzanie wsadowe audio), roczny koszt sprzętu to:

> 2 (klastry) * 5 (maszyny wirtualne na klaster) * $0.3528/godz. * 365 (dni) * 24 (godz.) = $31K/Year

Podczas mapowania na maszynę fizyczną ogólne oszacowanie to 1 vCPU = 1 fizyczny rdzeń procesora CPU. W rzeczywistości 1vCPU jest bardziej wydajne niż pojedynczy rdzeń.

W przypadku Premium wszystkie te dodatkowe czynniki są odtwarzane:

- Na jakim typie jest fizyczny procesor CPU i ile rdzeni
- Liczba uruchomionych procesorów jednocześnie w tym samym polu/maszynie
- Jak są konfigurowane maszyny wirtualne
- Jak jest używane wielowątkowość funkcji Hyper-Threading/wielowątkowość
- Jak jest udostępniana pamięć
- System operacyjny itd.

Zwykle nie jest to dobrze dostrojone jako środowisko platformy Azure. Biorąc pod uwagę inne koszty, należy powiedzieć, że bezpieczne oszacowanie wynosi 10 fizycznych rdzeni procesora = 8 Azure vCPU. Chociaż popularne procesory mają tylko osiem rdzeni. W przypadku wdrażania na Premium koszt będzie większy niż korzystanie z maszyn wirtualnych platformy Azure. Należy również wziąć pod uwagę stawkę amortyzacji.

Koszt usługi jest taki sam jak usługa online: $1/godzina dla zamiany mowy na tekst. Koszt usługi mowy to:

> $1 * 1000 * 365 = $365K

Koszt konserwacji płatnej przez firmę Microsoft zależy od poziomu usługi i zawartości usługi. Jest to różne od $29.99/miesiąc w przypadku podstawowego poziomu do setek tysięcy w przypadku usługi Onsite. Niesztywna liczba to $300/godzina w przypadku usługi/utrzymania. Koszt osób nie jest uwzględniony. Inne koszty związane z infrastrukturą (takie jak magazyn, sieci i moduły równoważenia obciążenia) nie są uwzględniane.

<br>
</details>

<details>
<summary>
<b>Dlaczego brakuje interpunkcji z transkrypcji?</b>
</summary>

**Odpowiedź:** `speech_recognition_language=<YOUR_LANGUAGE>` W przypadku korzystania z klienta węgla należy jawnie skonfigurować w żądaniu.

Na przykład:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Oto dane wyjściowe:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Czy mogę użyć niestandardowego modelu akustycznego i modelu języka z kontenerem mowy?</b>
</summary>

Obecnie można przekazać tylko jeden identyfikator modelu, niestandardowy model języka lub niestandardowy model akustyczny.

**Odpowiedź:** Podejmowana jest  decyzja o obsłudze jednocześnie obu modeli akustycznych i językowych. Ta funkcja będzie obowiązywać do momentu utworzenia ujednoliconego identyfikatora w celu zmniejszenia liczby podziałów interfejsu API. Dlatego nie jest to obecnie obsługiwane.

<br>
</details>

<details>
<summary>
<b>Czy można wyjaśnić te błędy z niestandardowego kontenera zamiany mowy na tekst?</b>
</summary>

**Błąd 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Odpowiedź 1:** W przypadku szkoleń z najnowszym modelem niestandardowym obecnie nie obsługujemy tego programu. W przypadku szkolenia ze starszą wersją powinno być możliwe korzystanie z programu. Nadal pracujemy nad obsługą najnowszych wersji.

Zasadniczo kontenery niestandardowe nie obsługują modeli akustycznych opartych na Halide lub ONNX (co jest ustawieniem domyślnym w portalu szkoleń niestandardowych). Wynika to z nieszyfrowanych modeli niestandardowych i nie chcemy ujawniać modeli ONNX. modele językowe są dobrane. Klient będzie musiał jawnie wybrać starszy model inny niż ONNX do niestandardowego szkolenia. Nie wpłynie to na dokładność. Rozmiar modelu może być większy (100 MB).

> Model pomocy technicznej > 20190220 (ujednolicony v 4.5)

**Błąd 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Odpowiedź 2:** Musisz podać poprawną nazwę głosu w żądaniu, w której jest rozróżniana wielkość liter. Zapoznaj się z pełnym mapowaniem nazw usług.

**Błąd 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Odpowiedź 3:** Reed utworzyć zasób mowy, a nie zasób Cognitive Services.


<br>
</details>

<details>
<summary>
<b>Jakie protokoły interfejsu API są obsługiwane, REST lub WS?</b>
</summary>

**Odpowiedź:** W przypadku zamiany mowy na tekst i niestandardowych kontenerów zamiany mowy na tekst obsługujemy obecnie tylko protokół WebSocket. Zestaw SDK obsługuje tylko wywoływanie w usłudze WS, ale nie REST. Istnieje plan dodawania obsługi REST, ale nie na chwilę. Zawsze zapoznaj się z oficjalną dokumentacją, zobacz [punkty końcowe przewidywania zapytań](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Czy CentOS jest obsługiwana dla kontenerów mowy?</b>
</summary>

**Odpowiedź:** CentOS 7 nie jest jeszcze obsługiwana przez zestaw SDK języka Python, również Ubuntu 19,04 nie jest obsługiwana.

Pakiet zestawu Speech SDK dla języka Python jest dostępny dla tych systemów operacyjnych:
- **Windows** — x64 i x86
- **Mac** -macOS X w wersji 10,12 lub nowszej
- **Linux** -Ubuntu 16,04, Ubuntu 18,04, Debian 9 w x64

Aby uzyskać więcej informacji na temat konfiguracji środowiska, zobacz [Konfiguracja platformy języka Python](quickstarts/setup-platform.md?pivots=programming-language-python). Na razie zalecana wersja to Ubuntu 18,04.

<br>
</details>

<details>
<summary>
<b>Dlaczego otrzymuję błędy podczas próby wywołania punktów końcowych przewidywania LUIS?</b>
</summary>

Używam kontenera LUIS we wdrożeniu IoT Edge i próbujemy wywołać punkt końcowy przewidywania LUIS z innego kontenera. Kontener LUIS nasłuchuje na porcie 5001, a używany adres URL to:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

Wystąpił błąd:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Widzę żądanie w dziennikach kontenera LUIS, a komunikat brzmi:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Co to oznacza? Czego brakuje? W [tym miejscu](https://github.com/Azure-Samples/cognitive-services-speech-sdk)przedstawiono przykład dla zestawu Speech SDK. Scenariusz polega na tym, że wykrywamy dźwięk bezpośrednio z mikrofonu komputera i próbuje określić zamiar na podstawie przeszkolonej aplikacji Luis. Przykładem, z którym łączy się dokładnie, jest. Działa dobrze z usługą LUIS w chmurze. Użycie zestawu Speech SDK miało na celu zaoszczędzenie nam konieczności oddzielenia jawnego wywołania do interfejsu API zamiany mowy na tekst, a następnie drugiego wywołania LUIS.

W związku z tym wszystko, co próbuję zrobić, jest przełączane z scenariusza używania LUIS w chmurze do używania kontenera LUIS. Nie można Wyobraź sobie, że zestaw Speech SDK działa dla jednego z nich, nie będzie działać.

**Odpowiedź:** Zestawu Speech SDK nie należy używać w odniesieniu do kontenera LUIS. W przypadku używania kontenera LUIS należy użyć zestawu SDK LUIS lub interfejsu API REST LUIS. Zestaw mowy SDK powinien być używany w odniesieniu do kontenera mowy.

Chmura jest inna niż kontener. Chmura może składać się z wielu kontenerów zagregowanych (nazywanych czasami Micro Services). Istnieje kontener LUIS, a następnie istnieje kontener mowy — dwa oddzielne kontenery. Kontener mowy obsługuje tylko mowę. Kontener LUIS wykonuje tylko LUIS. W chmurze, ponieważ oba kontenery są wdrażane i są w złej wydajności, aby Klient zdalny przeszedł do chmury, zrób mowę, Wróć, a następnie przejdź do chmury ponownie i LUIS, udostępniamy funkcję umożliwiającą klientowi przejście na mowę, pozostawanie w chmurze, a następnie powrót do klienta. W ten sposób nawet w tym scenariuszu zestaw SDK usługi Speech jest kierowany do kontenera usługi mowy w chmurze z dźwiękiem, a następnie funkcja zamiany kontenera chmurowego na LUIS w kontenerze chmury z tekstem. Kontener LUIS nie ma koncepcji akceptowania audio (nie ma sensu, aby kontener LUIS do akceptowania przesyłania strumieniowego audio LUIS jest usługą opartą na tekście). W przypadku Premium nie mamy pewności, że nasz klient wdrożył oba kontenery, nie ma potrzeby organizowania między kontenerami w lokalu naszych klientów i jeśli oba kontenery są wdrożone na Premium, pod warunkiem, że są one bardziej lokalne dla klienta, nie jest to obciążenie, aby najpierw korzystać z funkcji SR, z powrotem do klienta, a następnie pobrałem ten tekst i przejdź do LUIS.

<br>
</details>

<details>
<summary>
<b>Dlaczego są uzyskiwane błędy z macOS, kontenera mowy i zestawu SDK języka Python?</b>
</summary>

Gdy wyślemy plik *. wav* do uzyskanego, wynik wraca z:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Wiemy, że protokół WebSocket został prawidłowo skonfigurowany.

**Odpowiedź:** W takim przypadku należy zapoznać się z [tym problemem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310)w serwisie GitHub. W [tym miejscu mamy zaproponować](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)obejście.

Węgiel został naprawiony w wersji 1,8.


<br>
</details>

<details>
<summary>
<b>Jakie są różnice w punktach końcowych kontenerów mowy?</b>
</summary>

Czy można pomóc wypełnić poniższe metryki testu, w tym funkcje, które należy przetestować, oraz sposób testowania zestawu SDK i interfejsów API REST? Szczególnie różnice w "interaktywne" i "konwersacja", które nie były widoczne z istniejącego dokumentu/próbki.

| Punkt końcowy                                                | Test funkcjonalny                                                   | SDK | Interfejs API REST |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Synteza tekstu (zamiany tekstu na mowę)                                  |     | Tak      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services punkt końcowy protokołu WebSocket Premium w wersji 1        | Tak | Nie       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Cognitive Services w punkcie końcowym protokołu WebSocket Premium w wersji 1  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Punkt końcowy protokołu WebSocket usługi poznawczej Premium w wersji 1 |     |          |

**Odpowiedź:** Jest to fuzja:
- Osoby próbujące punkt końcowy dyktowania dla kontenerów (nie mam pewności, jak mają ten adres URL)
- 1 punkt końcowy jednostki<sup>krótkoterminowej</sup> znajduje się w kontenerze.
- 1 punkt końcowy jednostki usługi<sup>St</sup> zwraca mowę. fragmenty komunikatów zamiast `speech.hypothesis` komunikatów, które są zwracane przez 3 punkty końcowe części<sup>pulpitu zdalnego</sup> dla punktu końcowego dyktowania.
- Węgiel korzysta ze wszystkich `RecognizeOnce` (tryb interaktywny)
- Węgiel z potwierdzeniem, że dla `speech.fragment` komunikatów wymagających nie są zwracane w trybie interaktywnym.
- Węgiel mający potwierdzenia pożaru w kompilacjach wydania (zabijanie procesu).

Obejście to przełączenie do korzystania z ciągłego rozpoznawania w kodzie lub (szybsze) łączenie się z interaktywnym lub ciągłym punktami końcowymi w kontenerze.
Dla kodu Ustaw punkt końcowy na `host:port` /Speech/Recognition/Interactive/cognitiveservices/v1

Aby zapoznać się z różnymi trybami, zobacz Tryby mowy — Zobacz poniżej:

## <a name="speech-modes---interactive-conversation-dictation"></a>Tryby mowy — interaktywny, Konwersacja, Dyktowanie

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Odpowiednia poprawka jest dostępna w zestawie SDK 1,8, który ma pomoc techniczną Premium (wybierz właściwy punkt końcowy, więc nie będzie to możliwe niż usługa online). W międzyczasie istnieje przykład do ciągłego rozpoznawania, dlaczego nie wskazujemy go?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Którego trybu używać dla różnych plików audio?</b>
</summary>

**Odpowiedź:** Oto [Szybki Start przy użyciu języka Python](./get-started-speech-to-text.md?pivots=programming-language-python). Inne języki połączone z witryną docs można znaleźć.

Po prostu Wyjaśnij, jak interaktywny, Konwersacja i dyktowanie; jest to zaawansowany sposób określania konkretnego sposobu obsługi żądania mowy przez naszą usługę. Niestety, w przypadku kontenerów Premium musimy określić pełny identyfikator URI (ponieważ obejmuje to komputer lokalny), więc te informacje są wyciekiem z abstrakcji. Pracujemy nad zespołem zestawu SDK, aby zwiększyć jego użyteczność w przyszłości.

<br>
</details>

<details>
<summary>
<b>Jak możemy przeprowadzić test porównawczy dla niedalekiej miary transakcji/drugiego/rdzeni?</b>
</summary>

**Odpowiedź:** Poniżej znajdują się pewne wartości przybliżone, których można oczekiwać od istniejącego modelu (zostaną one zmienione w celu lepszego w przypadku, w którym firma Microsoft będzie dostarczana ogólnie):

- W przypadku plików ograniczenie przepustowości będzie w zestawie Speech SDK, na 2. Pierwsze pięć sekund dźwięku nie jest ograniczone. Dekoder jest w stanie wykonać około trzykrotnie czasu rzeczywistego. W takim przypadku ogólne użycie procesora CPU zostanie zbliżone do 2 rdzeni dla jednego rozpoznawania.
- W przypadku mikrofonu będzie on w czasie rzeczywistym. Ogólne użycie powinno mieć co najwyżej 1 rdzeń do jednego rozpoznawania.

Wszystkie te możliwości można zweryfikować z dzienników platformy Docker. Faktycznie zrzucamy wiersz za pomocą statystyk sesji i fraz/wypowiedź, które zawierają numery RTF.

<br>
</details>

<details>
<summary>
<b>Jak mogę wykonać wiele kontenerów na tym samym hoście?</b>
</summary>

Dokument mówi, aby udostępnić inny port, który to zrobić, ale kontener LUIS nadal nasłuchuje na porcie 5000?

**Odpowiedź:** Spróbuj `-p <outside_unique_port>:5000` . Na przykład `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Pytania techniczne

<details>
<summary>
<b>Jak można uzyskać interfejsy API inne niż partia do obsługi dźwięku &lt; 15 sekund?</b>
</summary>

**Odpowiedź:** `RecognizeOnce()` tryb interaktywny przetwarza tylko do 15 sekund audio, ponieważ tryb jest przeznaczony dla poleceń mowy, gdzie wyrażenia długości powinien być krótki. Jeśli używasz `StartContinuousRecognition()` do dyktowania lub konwersacji, nie ma żadnego limitu 15 sekund.


<br>
</details>

<details>
<summary>
<b>Jakie są zalecane zasoby, procesor CPU i pamięć RAM; Liczba współbieżnych żądań 50</b>
</summary>

Ile współbieżnych żądań będzie obsługiwał 4 rdzenie, 4 GB pamięci RAM? Jeśli firma Microsoft musi na przykład obsłużyć żądania współbieżności 50, ile rdzeni i pamięci RAM są zalecane?

**Odpowiedź:** W czasie rzeczywistym 8 z najnowszym `en-US` , dlatego zalecamy użycie większej liczby kontenerów platformy Docker niż 6 współbieżnych żądań. Pobiera crazier więcej niż 16 rdzeni i staje się wrażliwy na niejednolity węzeł dostępu do pamięci (NUMA). W poniższej tabeli opisano minimalne i zalecane alokacje zasobów dla każdego kontenera mowy.

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

| Kontener      | Minimum             | Zalecane         |
|----------------|---------------------|---------------------|
| Zamiana mowy na tekst | 2 rdzeń, 2 GB pamięci | 4 rdzenie, 4 GB pamięci |

# <a name="custom-speech-to-text"></a>[Custom Speech do tekstu](#tab/cstt)

| Kontener             | Minimum             | Zalecane         |
|-----------------------|---------------------|---------------------|
| Custom Speech do tekstu | 2 rdzeń, 2 GB pamięci | 4 rdzenie, 4 GB pamięci |

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

| Kontener      | Minimum             | Zalecane         |
|----------------|---------------------|---------------------|
| Zamiana tekstu na mowę | 1 rdzeń, 2 GB pamięci | 2 rdzeń, 3 GB pamięci |

# <a name="custom-text-to-speech"></a>[Niestandardowa Zamiana tekstu na mowę](#tab/ctts)

| Kontener             | Minimum             | Zalecane         |
|-----------------------|---------------------|---------------------|
| Niestandardowa Zamiana tekstu na mowę | 1 rdzeń, 2 GB pamięci | 2 rdzeń, 3 GB pamięci |

***

- Każdy rdzeń musi mieć co najmniej 2,6 GHz lub szybszy.
- W przypadku plików ograniczenie przepustowości będzie w zestawie Speech SDK, a wartość 2 (pierwsze 5 sekund dźwięku nie jest ograniczana).
- Dekodera jest w stanie wykonać około 2 – 3. czasu rzeczywistego. W takim przypadku ogólne użycie procesora CPU będzie bliski dwa rdzenie na potrzeby jednego rozpoznawania. Dlatego nie zalecamy zachowywania więcej niż dwóch aktywnych połączeń na wystąpienie kontenera. Najważniejsza może być umieszczenie około 10 dekoderów w czasie, w którym znajduje się osiem rdzeni, takich jak `DS13_V2` . W przypadku kontenera w wersji 1,3 i nowszych istnieje parametr, który można wypróbować `DECODER_MAX_COUNT=20` .
- W przypadku mikrofonu zostanie osiągnięty czas rzeczywisty. Ogólne użycie powinno mieć co najmniej jeden rdzeń dla jednego rozpoznawania.

Należy wziąć pod uwagę łączną liczbę godzin korzystania z dźwięku. Jeśli liczba jest duża, aby zwiększyć niezawodność/dostępność, sugerujemy uruchomienie większej liczby wystąpień kontenerów na jednym lub wielu polach za modułem równoważenia obciążenia. Aranżację można wykonać przy użyciu Kubernetes (K8S) i Helm lub z funkcją tworzenia platformy Docker.

Przykładowo, aby obsłużyć 1000 godzin/24 godziny, próbowaliśmy skonfigurować 3-4 maszyn wirtualnych z 10 wystąpieniami/dekoderami na maszynę wirtualną.

<br>
</details>

<details>
<summary>
<b>Czy kontener mowy obsługuje interpunkcję?</b>
</summary>

**Odpowiedź:** Mamy wielką literę (ITN) dostępną w kontenerze Premium. Interpunkcja jest zależna od języka i nie jest obsługiwana w przypadku niektórych języków, w tym chińskim i japońskim.

Mamy  niejawną i podstawową obsługę interpunkcji dla istniejących kontenerów, ale jest ona `off` domyślnie. Oznacza to, że możesz uzyskać `.` znak w Twoim przykładzie, ale nie `。` znak. Aby włączyć tę niejawną logikę, Skorzystaj z przykładu, jak to zrobić w języku Python przy użyciu zestawu mowy SDK (podobnie jak w przypadku innych języków):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Dlaczego otrzymuję błędy 404 podczas próby OPUBLIKOWANia danych w kontenerze zamiany mowy na tekst?</b>
</summary>

Oto przykład wpisu HTTP:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Odpowiedź:** Nie obsługujemy interfejsu API REST w kontenerze zamiany mowy na tekst, ale obsługujemy tylko usługi WebSockets za pomocą zestawu Speech SDK. Zawsze zapoznaj się z oficjalną dokumentacją, zobacz [punkty końcowe przewidywania zapytań](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Dlaczego kontener działa jako użytkownik inny niż główny? Jakie problemy mogą wystąpić w związku z tym?</b>
</summary>

**Odpowiedź:** Należy pamiętać, że domyślny użytkownik wewnątrz kontenera nie jest użytkownikiem głównym. Zapewnia to ochronę przed procesami ucieczki kontenera i uzyskiwaniem uprawnień eskalacji w węźle hosta. Domyślnie niektóre platformy, takie jak platforma kontenera OpenShift, są już wykonywane przez uruchamianie kontenerów przy użyciu wstępnie przypisanego identyfikatora użytkownika. W przypadku tych platform użytkownik niebędący użytkownikiem głównym musi mieć uprawnienia do zapisu w dowolnym zewnętrznym woluminie zmapowanym, który wymaga zapisu. Na przykład folder rejestrowania lub folder pobierania modelu niestandardowego.
<br>
</details>

<details>
<summary>
<b>Dlaczego podczas korzystania z usługi zamiany mowy na tekst pojawia się ten błąd?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Odpowiedź:** Zwykle zdarza się to, gdy strumieniowe źródło danych audio jest szybsze niż może to zrobić kontener rozpoznawania mowy. Bufory klienta są wypełniane, a anulowanie jest wyzwalane. Musisz kontrolować współbieżność i format RTF, w którym przesyłasz dźwięk.

<br>
</details>

<details>
<summary>
<b>Czy można wyjaśnić te błędy kontenerów zamiany tekstu na mowę z przykładów języka C++?</b>
</summary>

**Odpowiedź:** Jeśli wersja kontenera jest starsza niż 1,3, należy użyć tego kodu:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Starsze kontenery nie mają wymaganego punktu końcowego dla węgla do pracy z `FromHost` interfejsem API. Jeśli kontenery używane dla wersji 1,3, należy użyć tego kodu:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Poniżej znajduje się przykład użycia `FromEndpoint` interfejsu API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 `SetSpeechSynthesisVoiceName`Funkcja jest wywoływana, ponieważ kontenery ze zaktualizowanym aparatem zamiany tekstu na mowę wymagają nazwy głosu.

<br>
</details>

<details>
<summary>
<b>Jak mogę użyć wersji 1.7 zestawu Speech SDK z kontenerem mowy?</b>
</summary>

**Odpowiedź:** Kontener mowy zawiera trzy punkty końcowe dla różnych zastosowań, są one definiowane jako tryby mowy — Zobacz poniżej:

## <a name="speech-modes"></a>Tryby mowy

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Są one przeznaczone do różnych celów i są używane inaczej.

[Przykłady](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)języka Python:
- W przypadku pojedynczego rozpoznawania (tryb interaktywny) z niestandardowym punktem końcowym (czyli `SpeechConfig` z parametrem punktu końcowego), zobacz `speech_recognize_once_from_file_with_custom_endpoint_parameters()` .
- W celu zapewnienia ciągłego rozpoznawania (tryb konwersacji) i po prostu zmodyfikować, aby użyć niestandardowego punktu końcowego, jak powyżej, zobacz `speech_recognize_continuous_from_file()` .
- Aby włączyć dyktowanie w przykładach, takich jak powyżej (tylko wtedy, gdy jest to naprawdę potrzebne), bezpośrednio po utworzeniu `speech_config` , Dodaj kod `speech_config.enable_dictation()` .

W języku C#, aby włączyć Dyktowanie, wywołaj `SpeechConfig.EnableDictation()` funkcję.

### <a name="fromendpoint-apis"></a>`FromEndpoint` Programowania
| Język | Szczegóły interfejsu API |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Obecnie nie jest obsługiwana lub nie jest ona zaplanowana. |

<br>
</details>

<details>
<summary>
<b>Jak mogę użyć zestawu Speech SDK z kontenerem mowy?</b>
</summary>

**Odpowiedź:** Istnieje nowy `FromHost` interfejs API. Nie zastępuje to ani nie modyfikuje żadnych istniejących interfejsów API. Po prostu dodaje alternatywny sposób tworzenia konfiguracji mowy przy użyciu niestandardowego hosta.

### <a name="fromhost-apis"></a>`FromHost` Programowania

| Język | Szczegóły interfejsu API |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Nie jest obecnie obsługiwana. |

> Parametry: Host (obowiązkowy), klucz subskrypcji (opcjonalnie, jeśli można korzystać z usługi bez niej).

Format hosta jest `protocol://hostname:port` `:port` opcjonalny (patrz poniżej):
- Jeśli kontener działa lokalnie, nazwa hosta to `localhost` .
- Jeśli kontener jest uruchomiony na serwerze zdalnym, użyj nazwy hosta lub adresu IPv4 tego serwera.

Przykłady parametrów hosta dla zamiany mowy na tekst:
- `ws://localhost:5000` -niezabezpieczone połączenie z kontenerem lokalnym przy użyciu portu 5000
- `ws://some.host.com:5000` -niezabezpieczone połączenie z kontenerem uruchomionym na serwerze zdalnym

Przykłady języka Python z powyższych, ale używają `host` parametru zamiast `endpoint` :

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kontenery Cognitive Services](speech-container-howto.md)