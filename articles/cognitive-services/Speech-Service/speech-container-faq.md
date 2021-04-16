---
title: Kontenery usługi Mowa — często zadawane pytania
titleSuffix: Azure Cognitive Services
description: Instalowanie i uruchamianie kontenerów mowy. Przesyłanie mowy na tekst transkrybuje strumienie audio na tekst w czasie rzeczywistym, z których mogą korzystać lub wyświetlać aplikacje, narzędzia lub urządzenia. Zamiana tekstu na mowę konwertuje tekst wejściowy na mowę syntetyzowany podobny do ludzkiego.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 28a044f42d0774d940521964b68b38a0f35bcdbb
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387959"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Kontenery usługi Mowa — często zadawane pytania

W przypadku korzystania z usługi Mowa z kontenerami należy polegać na tej kolekcji często zadawanych pytań przed eskalacją do pomocy technicznej. Ten artykuł zawiera pytania w różnym stopniu, od ogólnych do technicznych. Aby rozwinąć odpowiedź, kliknij pytanie.

## <a name="general-questions"></a>Pytania ogólne

<details>
<summary>
<b>Jak działają kontenery mowy i jak je skonfigurować?</b>
</summary>

**Odpowiedź:** Podczas konfigurowania klastra produkcyjnego należy wziąć pod uwagę kilka rzeczy. Po pierwsze skonfigurowanie jednego języka, wielu kontenerów na tym samym komputerze, nie powinno być dużym problemem. Jeśli występują problemy, może to być problem związany ze sprzętem— więc najpierw przyjrzymy się zasobowi, czyli Specyfikacje procesora CPU i pamięci.

Rozważmy na chwilę kontener `ja-JP` i najnowszy model. Model akustyczny jest najbardziej wymagającym elementem z procesorem CPU, podczas gdy model językowy wymaga najwięcej pamięci. Podczas testów porównawczych użycia przetwarzanie pojedynczego żądania mowy na tekst, gdy dźwięk przepływa w czasie rzeczywistym (np. z mikrofonu), wymaga około 0,6 rdzenia procesora CPU. W przypadku szybszego podawania dźwięku niż w czasie rzeczywistym (na przykład z pliku) użycie może się podwoić (1,2 x rdzenie). W międzyczasie wymieniona poniżej pamięć jest pamięcią operacyjną do dekodowania mowy. Nie *uwzględnia* rzeczywistego pełnego rozmiaru modelu językowego, który będzie znajdować się w pamięci podręcznej plików. W tym przypadku dodatkowe 2 GB; w przypadku usługi `ja-JP` może to być więcej `en-US` (6–7 GB).

Jeśli masz maszynę, na której pamięci jest mało, a próbujesz wdrożyć na nim wiele języków, może to oznaczać, że pamięć podręczna plików jest pełna, a system operacyjny musi stronicować modele do i na zewnątrz. W przypadku uruchomionej transkrypcji może to być katastrofalne i może prowadzić do spowolnień i innych implikacji wydajności.

Ponadto wstępnie pakujemy pliki wykonywalne dla maszyn z zestawem instrukcji [rozszerzenia Advanced Vector Extension (AVX2).](speech-container-howto.md#advanced-vector-extension-support) Maszyna z zestawem instrukcji AVX512 będzie wymagać generowania kodu dla tego obiektu docelowego, a uruchomienie 10 kontenerów dla 10 języków może tymczasowo wyczerpać użycie procesora CPU. W dziennikach platformy Docker zostanie wyświetlony komunikat podobny do tego:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Za pomocą zmiennej można ustawić liczbę dekoderów w jednym  `DECODER MAX_COUNT` kontenerze. Dlatego w zasadzie należy zacząć od twojej sku (procesor/pamięć) i zasugerować, jak najlepiej z niego korzystać. Doskonałym punktem początkowym jest odwoływanie się do zalecanych specyfikacji zasobów maszyny hosta.

<br>
</details>

<details>
<summary>
<b>Czy możesz pomóc w planowaniu pojemności i szacowaniu kosztów kontenerów funkcji mowy na tekst?</b>
</summary>

**Odpowiedź:** W przypadku pojemności kontenera w trybie przetwarzania wsadowego każdy dekoder może obsługiwać 2–3x w czasie rzeczywistym z dwoma rdzeniami procesora CPU w celu pojedynczego rozpoznawania. Nie zalecamy utrzymywania więcej niż dwóch współbieżnych rozpoznań na wystąpienie kontenera, ale zalecamy uruchamianie większej liczby wystąpień kontenerów ze względu na niezawodność/dostępność za usługą równoważenia obciążenia.

Chociaż każde wystąpienie kontenera może być uruchomione z większą liczby dekoderów. Na przykład możemy skonfigurować 7 dekoderów na wystąpienie kontenera na maszynie z 8 rdzeniami (po więcej niż 2x każdy), co daje 15- razy większą przepływność. Istnieje param, `DECODER_MAX_COUNT` o których należy pamiętać. W skrajnym przypadku występują problemy z niezawodnością i opóźnieniami, a przepływność znacznie wzrosła. W przypadku mikrofonu będzie on 1 razy w czasie rzeczywistym. Ogólne użycie powinno być na poziomie około jednego rdzenia dla pojedynczego rozpoznawania.

W przypadku scenariusza przetwarzania 1 K godz./dzień w trybie przetwarzania wsadowego w skrajnym przypadku 3 maszyny wirtualne mogą obsłużyć ten proces w ciągu 24 godzin, ale nie jest to gwarantowane. Aby obsłużyć szczytowe dni, tryb failover, aktualizację i zapewnić minimalną kopię zapasową/BCP, zalecamy 4–5 maszyn zamiast 3 na klaster i 2 klastry.

W przypadku sprzętu jako odniesienia używamy standardowej maszyny wirtualnej platformy Azure (każdy rdzeń musi mieć częstotliwość 2,6 GHz lub lepszą z włączonym zestawem instrukcji `DS13_v2` AVX2).

| Wystąpienie  | Procesory wirtualne | Pamięć RAM    | Magazyn tymczasowy | Płatność zgodnie z użytkownikiem przy użyciu AHB | Rezerwa na 1 rok z AHB (% oszczędności) | 3-letnia rezerwacja z AHB (% oszczędności) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | 0,598 USD/godzinę            | 0,3528 USD/godzinę (ok. 41%)                 | 0,2333 USD/godzinę (~61%)                  |

Na podstawie odwołania do projektu (dwa klastry po 5 maszyn wirtualnych do obsługi 1 K/dzień przetwarzania wsadowego audio) koszt sprzętu na 1 rok będzie wynosił:

> 2 (klastry) * 5 (maszyny wirtualne na klaster) * 0,3528 USD/godzinę * 365 (dni) * 24 (godziny) = 31 tys. USD/rok

Podczas mapowania na maszynę fizyczną ogólne oszacowanie wynosi 1 procesor wirtualny = 1 rdzeń procesora fizycznego. W rzeczywistości procesor 1vCPU jest bardziej wydajny niż jeden rdzeń.

W przypadku pracy w przypadku pracy w sposób on-factors wezmą pod uwagę wszystkie te dodatkowe czynniki:

- Informacje na temat typu procesora fizycznego i jego mocy
- Ile procesorów CPU działa razem na tym samym urządzeniu/maszynie
- Jak są ustawiane maszyny wirtualne
- Jak jest używana hiperwątkowa/wielowątkowa
- Jak jest udostępniana pamięć
- System operacyjny itp.

Zwykle nie jest on tak dobrze dostosowany jak środowisko platformy Azure. Biorąc pod uwagę inne obciążenie, można powiedzieć, że bezpieczne oszacowanie to 10 rdzeni fizycznego procesora CPU = 8 procesorów wirtualnych platformy Azure. Chociaż popularne procesory CPU mają tylko osiem rdzeni. W przypadku wdrożenia on-one koszt będzie wyższy niż w przypadku korzystania z maszyn wirtualnych platformy Azure. Należy również wziąć pod uwagę stawkę amortyzacji.

Koszt usługi jest taki sam jak w przypadku usługi online: 1 USD/godzinę dla funkcji mowy na tekst. Koszt usługi Mowa wynosi:

> 1 USD * 1000 * 365 = 365 tys. USD

Koszt konserwacji zapłacony firmie Microsoft zależy od poziomu usługi i zawartości usługi. Różni się ona od 29,99 USD/miesiąc dla poziomu podstawowego do setek tysięcy, jeśli są zaangażowane usługi w miejscu pracy. Wartość usługi/konserwacji wynosi 300 USD/godzinę. Koszt osób nie jest uwzględniany. Inne koszty infrastruktury (takie jak magazyn, sieci i usługi równoważenia obciążenia) nie są uwzględniane.

<br>
</details>

<details>
<summary>
<b>Dlaczego w transkrypcji brakuje znaków interpunkcji?</b>
</summary>

**Odpowiedź:** Należy `speech_recognition_language=<YOUR_LANGUAGE>` jawnie skonfigurować w żądaniu, jeśli są one przy użyciu klienta emisji dwutlenku węgla.

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
<b>Czy mogę użyć niestandardowego modelu akustycznego i modelu językowego z kontenerem mowy?</b>
</summary>

Obecnie możemy przekazać tylko jeden identyfikator modelu , niestandardowy model językowy lub niestandardowy model akustyczny.

**Odpowiedź:** Decyzje o *tym, aby* nie obsługiwać jednocześnie modeli akustycznych i językowych, została podjęta. Będzie to nadal obowiązywać do momentu utworzenia ujednoliconego identyfikatora w celu zmniejszenia przerw w działaniach interfejsu API. Niestety, nie jest to obecnie obsługiwane.

<br>
</details>

<details>
<summary>
<b>Czy możesz wyjaśnić te błędy z niestandardowego kontenera funkcji mowy na tekst?</b>
</summary>

**Błąd 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Odpowiedź 1:** Jeśli trenujesz przy użyciu najnowszego modelu niestandardowego, obecnie go nie obsługujemy. W przypadku trenowania przy użyciu starszej wersji powinno być możliwe użycie. Nadal pracujemy nad obsługą najnowszych wersji.

Zasadniczo kontenery niestandardowe nie obsługują modeli akustycznych opartych na rozwiązaniach Metalde i ONNX (co jest ustawieniem domyślnym w niestandardowym portalu szkoleniowym). Jest to spowodowane tym, że modele niestandardowe nie są szyfrowane i nie chcemy ujawniać modeli ONNX; Modele językowe są w porządku. Klient będzie musiał jawnie wybrać starszy model spoza modelu ONNX na potrzeby szkolenia niestandardowego. Nie będzie to mieć wpływu na dokładność. Rozmiar modelu może być większy (o 100 MB).

> Model pomocy > 20190220 (wersja 4.5 Ujednolicona)

**Błąd 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Odpowiedź 2:** W żądaniu należy podać poprawną nazwę głosu, w której jest zróżnicowa wielkość liter. Zapoznaj się z pełnym mapowaniem nazwy usługi.

**Błąd 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Odpowiedź 3:** Ponownie tworzymy zasób rozpoznawania mowy, a nie zasób Cognitive Services zasobów.


<br>
</details>

<details>
<summary>
<b>Jakie protokoły interfejsu API są obsługiwane, REST lub WS?</b>
</summary>

**Odpowiedź:** W przypadku kontenerów funkcji mowy na tekst i niestandardowej funkcji mowy na tekst obecnie obsługujemy tylko protokół oparty na protokole websocket. Zestaw SDK obsługuje tylko wywoływanie w programie WS, ale nie rest. Istnieje plan dodania obsługi REST, ale na razie nie ETA. Zawsze zapoznaj się z oficjalną dokumentacją, zobacz [punkty końcowe przewidywania zapytań](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Czy centOS jest obsługiwany w przypadku kontenerów mowy?</b>
</summary>

**Odpowiedź:** CentOS 7 nie jest jeszcze obsługiwany przez zestaw SDK języka Python, a ubuntu 19.04 nie jest obsługiwane.

Pakiet zestawu Speech SDK dla języka Python jest dostępny dla tych systemów operacyjnych:
- **Windows** — x64 i x86
- **Mac** — macOS X w wersji 10.12 lub nowszej
- **Linux** — Ubuntu 16.04, Ubuntu 18.04, Debian 9 na x64

Aby uzyskać więcej informacji na temat konfigurowania środowiska, zobacz [Konfiguracja platformy Python](quickstarts/setup-platform.md?pivots=programming-language-python). Na razie zalecana jest wersja Ubuntu 18.04.

<br>
</details>

<details>
<summary>
<b>Dlaczego występują błędy podczas próby wywołania punktów końcowych przewidywania usługi LUIS?</b>
</summary>

Korzystam z kontenera usługi LUIS w IoT Edge i próbuję wywołać punkt końcowy przewidywania usługi LUIS z innego kontenera. Kontener usługi LUIS nasłuchuje na porcie 5001, a adres URL, z których korzystam, jest taki:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

Wyświetlany błąd to:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Widzę żądanie w dziennikach kontenera usługi LUIS i pojawia się komunikat:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Co to oznacza? Czego brakuje? I was following the example for the Speech SDK( I was following the example for the Speech SDK, from [here](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Scenariusz jest taki, że wykrywamy dźwięk bezpośrednio z mikrofonu komputera i próbujemy określić intencję na podstawie wytrenowanej aplikacji LUIS. Przykład, z który został połączony z usługą , właśnie to robi. I dobrze współpracuje z usługą w chmurze LUIS. Użycie zestawu SDK rozpoznawania mowy wydaje się nie wymagać oddzielnego jawnego wywołania interfejsu API funkcji mowy na tekst, a następnie drugiego wywołania usługi LUIS.

W związku z tym, co próbuję zrobić, to przełączyć się ze scenariusza korzystania z usługi LUIS w chmurze na używanie kontenera usługi LUIS. Nie mogę sobie wyobrazić, że zestaw SPEECH SDK działa dla jednego z nich, ale nie będzie działać dla drugiego.

**Odpowiedź:** Zestawu SDK usługi Mowa nie należy używać względem kontenera usługi LUIS. W przypadku korzystania z kontenera usługi LUIS należy użyć zestawu SDK usługi LUIS lub interfejsu API REST usługi LUIS. Zestaw SDK rozpoznawania mowy powinien być używany względem kontenera mowy.

Chmura jest inna niż kontener. Chmura może składać się z wielu zagregowanych kontenerów (czasami nazywanych mikrousługami). Dlatego istnieje kontener usługi LUIS, a następnie kontener usługi Mowa — dwa oddzielne kontenery. Kontener mowy zawiera tylko mowę. Kontener usługi LUIS obsługuje tylko usługi LUIS. Ponieważ wiadomo, że oba kontenery są wdrażane w chmurze, a klienci zdalni nie mają złej wydajności, mogą przejść do chmury, wykonać mowę, wrócić, a następnie ponownie przejść do chmury i wykonać usługę LUIS, zapewniamy funkcję, która umożliwia klientowi korzystanie z usługi Mowa, pozostawanie w chmurze, korzystanie z usługi LUIS, a następnie powrót do klienta. W związku z tym nawet w tym scenariuszu zestaw SDK usługi Mowa przechodzi do kontenera usługi Mowa w chmurze z dźwiękami, a następnie kontener chmury mowy rozmawia z kontenerem chmury usługi LUIS przy użyciu tekstu. Kontener usługi LUIS nie ma koncepcji akceptowania dźwięku (kontener usługi LUIS nie ma sensu akceptować dźwięku przesyłanego strumieniowo — USŁUGA LUIS jest usługą opartą na tekście). W środowisku lokalnym nie mamy pewności, że nasz klient wdrożył oba kontenery. Nie zakładamy orkiestracji między kontenerami w środowisku lokalnym naszych klientów, a jeśli oba kontenery są wdrażane lokalnie, ze względu na to, że są one bardziej lokalne dla klienta, nie jest to obciążenie związane z powrotem z usługą SR, z powrotem do klienta, a następnie do usługi LUIS.

<br>
</details>

<details>
<summary>
<b>Dlaczego występują błędy związane z systemem macOS, kontenerem mowy i zestawem SDK języka Python?</b>
</summary>

Gdy wysyłamy plik *wav* do transkrypcji, wynik jest dostarczany z:

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

Wiemy, że obiekt websocket został poprawnie skonfigurowany.

**Odpowiedź:** Jeśli tak jest, zobacz ten problem w [usłudze GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310) Mamy tutaj proponowane [rozwiązanie.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)

Emisja dwutlenku węgla rozwiązała ten problem w wersji 1.8.


<br>
</details>

<details>
<summary>
<b>Jakie są różnice w punktach końcowych kontenera usługi Mowa?</b>
</summary>

Czy możesz pomóc wypełnić następujące metryki testowe, w tym funkcje do przetestowania oraz sposób testowania zestawu SDK i interfejsów API REST? Szczególnie różnice w "interakcyjnych" i "konwersacji", których nie widzę w istniejącym oknie doc/sample.

| Punkt końcowy                                                | Test funkcjonalny                                                   | SDK | Interfejs API REST |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Syntetyzowany tekst (tekst na mowę)                                  |     | Tak      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services punktu końcowego websocket dyktowania w wersji 1        | Tak | Nie       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Punkt Cognitive Services interakcyjny websocket w wersji 1  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Punkt końcowy websocket dla konwersacji w wersji 1 usług Cognitive Services |     |          |

**Odpowiedź:** Jest to połączenie:
- Osoby próbujące uzyskać punkt końcowy dyktowania dla kontenerów (nie wiem, jak dotarli do tego adresu URL)
- 1<sup>punkt końcowy</sup> strony jest tym, który jest w kontenerze.
- Punkt końcowy 1<sup>strony</sup> zwraca komunikaty speech.fragment zamiast komunikatów zwracanych przez punkty końcowe części 3 usług pulpitu zdalnego dla `speech.hypothesis` punktu końcowego dyktowania.<sup></sup>
- Przewodnik Szybki start dla technologii Carbon używa wszystkich `RecognizeOnce` elementów (tryb interaktywny)
- Carbon z asercją, że komunikaty wymagające ich nie `speech.fragment` są zwracane w trybie interaktywnym.
- Emisja dwutlenku węgla z asercjami jest zwalniana w kompilacjach wydania (zabicia procesu).

Obejście to przełączenie na używanie ciągłego rozpoznawania w kodzie lub (szybsze) połączenie z interaktywnymi lub ciągłymi punktami końcowymi w kontenerze.
Dla kodu ustaw punkt końcowy na `host:port` /speech/recognition/interactive/cognitiveservices/v1

Aby uzyskać informacje na temat różnych trybów, zobacz Tryby mowy — zobacz poniżej:

## <a name="speech-modes---interactive-conversation-dictation"></a>Tryby mowy — interaktywny, konwersacja, dyktowanie

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Poprawna poprawka jest dochodząca do zestawu SDK 1.8, który ma obsługę w trybie online (wybierze odpowiedni punkt końcowy, więc nie będzie gorsza niż usługa online). W międzyczasie istnieje próbka ciągłego rozpoznawania, dlaczego na to nie wskażemy?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Którego trybu należy użyć dla różnych plików dźwiękowych?</b>
</summary>

**Odpowiedź:** Oto przewodnik Szybki start [korzystający z języka Python.](./get-started-speech-to-text.md?pivots=programming-language-python) Inne języki, do których link jest połączony, można znaleźć w witrynie docs.

Tylko w celu wyjaśnienia interakcyjnego, konwersacji i dyktowania; Jest to zaawansowany sposób określania konkretnego sposobu, w jaki nasza usługa będzie obsługiwać żądanie mowy. Niestety w przypadku kontenerów lokalnych musimy określić pełny adres URI (ponieważ zawiera maszynę lokalną), więc te informacje wyciekły z abstrakcji. Współpracujemy z zespołem SDK, aby w przyszłości było to bardziej użyteczne.

<br>
</details>

<details>
<summary>
<b>Jak można testem porównawczym wątkłej miary transakcji/sekundy/rdzenia?</b>
</summary>

**Odpowiedź:** Oto niektóre z wstępnych liczb, których można oczekiwać od istniejącego modelu (zmieni się to na lepsze w tym, który zostanie przez nas wyemitowany):

- W przypadku plików ograniczanie będzie 2 razy w zestawie SDK rozpoznawania mowy. Pierwsze pięć sekund dźwięku nie jest ograniczane. Dekoder jest w stanie zrobić około 3x czasu rzeczywistego. W tym celu ogólne użycie procesora CPU będzie zbliżone do 2 rdzeni w przypadku pojedynczego rozpoznawania.
- W przypadku mikrofonu będzie to 1x w czasie rzeczywistym. Ogólne użycie powinno być na poziomie około 1 rdzenia dla pojedynczego rozpoznawania.

Wszystko to można zweryfikować z dzienników platformy Docker. W rzeczywistości zrzucamy wiersz ze statystykami sesji i fraz/wypowiedzi, które obejmują numery RTF.

<br>
</details>

<details>
<summary>
<b>Jak mogę uruchamiać wiele kontenerów na tym samym hoście?</b>
</summary>

W doc mówi, aby uwidocznić inny port, co robię, ale kontener usługi LUIS nadal nasłuchuje na porcie 5000?

**Odpowiedź:** Wypróbuj `-p <outside_unique_port>:5000` . Na przykład `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Pytania techniczne

<details>
<summary>
<b>Jak uzyskać interfejsy API inne niż wsadowe do obsługi dźwięku &lt; przez 15 sekund?</b>
</summary>

**Odpowiedź:** `RecognizeOnce()` w trybie interaktywnym przetwarza tylko do 15 sekund dźwięku, ponieważ tryb jest przeznaczony dla poleceń mowy, gdzie wypowiedzi powinny być krótkie. Jeśli używasz funkcji `StartContinuousRecognition()` dyktowania lub konwersacji, nie ma limitu 15 sekund.


<br>
</details>

<details>
<summary>
<b>Jakie są zalecane zasoby, procesor i pamięć RAM; dla 50 równoczesnych żądań?</b>
</summary>

Ile równoczesnych żądań będzie obsługiwać 4-rdzeniowe, 4 GB pamięci RAM? Jeśli musimy obsługiwać na przykład 50 równoczesnych żądań, ile rdzeni i pamięci RAM jest zalecanych?

**Odpowiedź:** W czasie rzeczywistym 8 z naszą najnowszą , dlatego zalecamy używanie większej liczby kontenerów `en-US` platformy Docker niż 6 współbieżnych żądań. Jest ona bardziej wrażliwa na więcej niż 16 rdzeni i staje się wrażliwa na nieunifikowy dostęp do pamięci (NUMA). W poniższej tabeli opisano minimalną i zalecaną alokację zasobów dla każdego kontenera mowy.

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

| Kontener      | Minimum             | Zalecane         |
|----------------|---------------------|---------------------|
| Zamiana mowy na tekst | 2 rdzenie, 2 GB pamięci | 4-rdzeniowe, 4 GB pamięci |

# <a name="custom-speech-to-text"></a>[Niestandardowa mowa na tekst](#tab/cstt)

| Kontener             | Minimum             | Zalecane         |
|-----------------------|---------------------|---------------------|
| Niestandardowa mowa na tekst | 2 rdzenie, 2 GB pamięci | 4-rdzeniowe, 4 GB pamięci |

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

| Kontener      | Minimum             | Zalecane         |
|----------------|---------------------|---------------------|
| Zamiana tekstu na mowę | 1 rdzeń, 2 GB pamięci | 2-rdzeniowe, 3 GB pamięci |

# <a name="custom-text-to-speech"></a>[Niestandardowa wiadomość tekstowa na mowę](#tab/ctts)

| Kontener             | Minimum             | Zalecane         |
|-----------------------|---------------------|---------------------|
| Niestandardowa wiadomość tekstowa na mowę | 1 rdzeń, 2 GB pamięci | 2-rdzeniowe, 3 GB pamięci |

***

- Każdy rdzeń musi mieć częstotliwość co najmniej 2,6 GHz lub większą.
- W przypadku plików ograniczanie będzie dostępne w zestawie SDK rozpoznawania mowy i będzie mieć 2x (pierwsze 5 sekund dźwięku nie jest ograniczane).
- Dekoder jest w stanie zrobić około 2–3x czasu rzeczywistego. W tym celu ogólne użycie procesora CPU będzie zbliżone do dwóch rdzeni w przypadku pojedynczego rozpoznawania. Dlatego nie zalecamy przechowywania więcej niż dwóch aktywnych połączeń na wystąpienie kontenera. Skrajną stroną byłoby umieścić około 10 dekoderów w 2x czasie rzeczywistym na ośmiordzeniowej maszynie, `DS13_V2` np. . W przypadku kontenera w wersji 1.3 lub nowszej istnieje param, który możesz wypróbować, ustawiając . `DECODER_MAX_COUNT=20`
- W przypadku mikrofonu będzie on 1x w czasie rzeczywistym. Ogólne użycie powinno być na poziomie około jednego rdzenia dla pojedynczego rozpoznawania.

Weź pod uwagę łączną liczbę godzin dźwięku. Jeśli liczba jest duża, aby zwiększyć niezawodność/dostępność, zalecamy uruchomienie większej liczby wystąpień kontenerów w jednym polu lub w wielu polach za usługą równoważenia obciążenia. Aranżację można wykonać przy użyciu platformy Kubernetes (K8S) i narzędzia Helm lub narzędzia Docker Compose.

Na przykład w celu obsługi 1000 godzin/24 godzin próbowaliśmy skonfigurować 3–4 maszyny wirtualne z 10 wystąpieniami/dekoderami na maszynę wirtualną.

<br>
</details>

<details>
<summary>
<b>Czy kontener rozpoznawania mowy obsługuje znaki interpunkcją?</b>
</summary>

**Odpowiedź:** Mamy dostępną literę (ITN) w kontenerze on-prem. Interpunkcja jest zależna od języka i nie jest obsługiwana w przypadku niektórych języków, w tym chińskiego i japońskiego.

Mamy *niejawną* i podstawową obsługę znaków interpunkcji dla istniejących kontenerów, ale jest `off` to domyślnie. Oznacza to, że można pobrać `.` znak w przykładzie, ale nie `。` znak. Aby włączyć tę niejawną logikę, oto przykład tego, jak to zrobić w języku Python przy użyciu naszego zestawu SPEECH SDK (będzie to podobne w innych językach):

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
<b>Dlaczego występują błędy 404 podczas próby post danych w kontenerze mowy na tekst?</b>
</summary>

Oto przykład żądania HTTP POST:

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

**Odpowiedź:** Nie obsługujemy interfejsu API REST w każdym kontenerze funkcji mowy na tekst— obsługujemy tylko zestawy WebSocket za pośrednictwem zestawu SPEECH SDK. Zawsze zapoznaj się z oficjalną dokumentacją, zobacz [punkty końcowe przewidywania zapytań](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Dlaczego kontener jest uruchomiony jako użytkownik niebędący użytkownikiem głównym? Jakie problemy mogą wystąpić z tego powodu?</b>
</summary>

**Odpowiedź:** Należy pamiętać, że domyślnym użytkownikiem w kontenerze jest użytkownik niebędący użytkownikiem głównym. Zapewnia to ochronę przed procesami ucieczki kontenera i uzyskiwaniem eskalowanych uprawnień w węźle hosta. Domyślnie niektóre platformy, takie jak OpenShift Container Platform, już to robią, uruchamiając kontenery przy użyciu dowolnie przypisanego identyfikatora użytkownika. W przypadku tych platform użytkownik niebędący użytkownikiem głównym musi mieć uprawnienia do zapisu na dowolnym woluminie mapowanych zewnętrznie, który wymaga zapisu. Na przykład folder rejestrowania lub folder pobierania modelu niestandardowego.
<br>
</details>

<details>
<summary>
<b>Dlaczego w przypadku korzystania z usługi mowy na tekst występuje ten błąd?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Odpowiedź:** Zwykle dzieje się tak, gdy kanał dźwiękowy jest strumieniem danych szybszym niż kontener rozpoznawania mowy może go zabrać. Bufory klienta zapełniają się, a anulowanie jest wyzwalane. Musisz kontrolować współbieżność i RTF, z których wysyłasz dźwięk.

<br>
</details>

<details>
<summary>
<b>Czy na przykładach języka C++ można wyjaśnić te błędy kontenera z tekstem na mowę?</b>
</summary>

**Odpowiedź:** Jeśli wersja kontenera jest starsza niż 1.3, należy użyć tego kodu:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Starsze kontenery nie mają wymaganego punktu końcowego, aby usługa Carbon działała z interfejsem `FromHost` API. Jeśli kontenery są używane dla wersji 1.3, należy użyć tego kodu:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Poniżej znajduje się przykład użycia interfejsu `FromEndpoint` API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 Funkcja `SetSpeechSynthesisVoiceName` jest wywoływana, ponieważ kontenery ze zaktualizowanym aparatem funkcji tekstu na mowę wymagają nazwy głosu.

<br>
</details>

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Cognitive Services kontenerów](speech-container-howto.md)
