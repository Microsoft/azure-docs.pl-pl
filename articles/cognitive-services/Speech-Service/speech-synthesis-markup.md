---
title: Język znaczników syntezy mowy (SSML) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Korzystanie z języka znaczników syntezy mowy do kontrolowania wymowy i prozydyjno-reklamowej w zamian za tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: dc11d26c73c52b5e6c4d8e05cc27dd6ebce0c5d8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399818"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Poprawa syntezy za pomocą języka znaczników syntezy mowy (SSML)

Język znaczników syntezy mowy (SSML) to język znaczników oparty na języku XML, który umożliwia deweloperom określenie sposobu konwertowania tekstu wejściowego na mowę syntetyzowaną przy użyciu usługi zamiany tekstu na mowę. W porównaniu do zwykłego tekstu, SSML pozwala deweloperom dostosować tonowanie, wymowa, szybkość mówienia, głośność i więcej tekstu na mowę. Normalna interpunkcja, na przykład wstrzymywanie po upływie okresu lub używanie poprawnej intonacji, gdy zdanie kończy się znakiem zapytania, są automatycznie obsługiwane.

Implementacja usługi mowy SSML opiera się na języku [syntezy mowy 1.0](https://www.w3.org/TR/speech-synthesis)konsorcjum World Wide Web Consortium.

> [!IMPORTANT]
> Znaki chińskie, japońskie i koreańskie są liczone jako dwa znaki do rozliczeń. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Głosy standardowe, neuronowe i niestandardowe

Wybieraj spośród standardowych i nerwowych głosów lub stwórz własny, niestandardowy głos unikalny dla Twojego produktu lub marki. Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i lokalizacjach, a 5 głosów neuronowych jest dostępnych w czterech językach i lokalizacjach. Aby uzyskać pełną listę obsługiwanych języków, ustawień regionalnych i głosów (neuronowych i standardowych), zobacz [obsługa języka](language-support.md).

Aby dowiedzieć się więcej o głosach standardowych, neuronowych i niestandardowych, zobacz [Omówienie zamiany tekstu na mowę](text-to-speech.md).

## <a name="special-characters"></a>Znaki specjalne

Podczas korzystania z SSML należy pamiętać, że znaki specjalne, takie jak cudzysłowy, apostrofy i nawiasy muszą być zmienione. Aby uzyskać więcej informacji, zobacz [Rozszerzalny język znaczników (XML) 1.0: Dodatek D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Obsługiwane elementy SSML

Każdy dokument SSML jest tworzony z elementami SSML (lub znacznikami). Elementy te są używane do regulacji wysokości, prozody, głośności i innych. W poniższych sekcjach szczegółowo, jak każdy element jest używany i gdy element jest wymagane lub opcjonalne.  

> [!IMPORTANT]
> Nie zapomnij użyć cudzysłowów wokół wartości atrybutów. Standardy dla dobrze sformułowanych, prawidłowych wartości XML wymagają ujęcia wartości atrybutów w podwójny cudzysłów. Na przykład `<prosody volume="90">` jest dobrze sformułowany, prawidłowy element, ale `<prosody volume=90>` nie jest. SSML może nie rozpoznawać wartości atrybutów, które nie znajdują się w cudzysłowach.

## <a name="create-an-ssml-document"></a>Tworzenie dokumentu SSML

`speak`jest elementem głównym i jest **wymagany** dla wszystkich dokumentów SSML. Element `speak` zawiera ważne informacje, takie jak wersja, język i definicja słownictwa znaczników.

**Składni**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalnie |
|-----------|-------------|---------------------|
| `version` | Wskazuje wersję specyfikacji SSML używanej do interpretacji znaczników dokumentu. Bieżąca wersja to 1.0. | Wymagany |
| `xml:lang` | Określa język dokumentu głównego. Wartość może zawierać małe, dwuliterowe kody języka `en`(na przykład) lub kod języka i wielki `en-US`kraj/region (na przykład). | Wymagany |
| `xmlns` | Określa identyfikator URI dokumentu definiujący słownictwo znaczników (typy elementów i nazwy atrybutów) dokumentu SSML. Bieżący identyfikator http://www.w3.org/2001/10/synthesisURI to . | Wymagany |

## <a name="choose-a-voice-for-text-to-speech"></a>Wybieranie głosu dla zamiany tekstu na mowę

Element `voice` jest wymagany. Służy do określania głosu, który jest używany do zamiany tekstu na mowę.

**Składni**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalnie |
|-----------|-------------|---------------------|
| `name` | Identyfikuje głos używany do wyprowadzania tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [Obsługa języka](language-support.md#text-to-speech). | Wymagany |

**Przykład**

> [!NOTE]
> W tym przykładzie użyto `en-US-AriaRUS` głosu. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [Obsługa języka](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Używanie wielu głosów

W `speak` elemencie można określić wiele głosów dla wyjścia zamiany tekstu na mowę. Te głosy mogą być w różnych językach. Dla każdego głosu tekst musi być `voice` zawinięty w element. 

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalnie |
|-----------|-------------|---------------------|
| `name` | Identyfikuje głos używany do wyprowadzania tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [Obsługa języka](language-support.md#text-to-speech). | Wymagany |

> [!IMPORTANT]
> Wiele głosów jest niezgodnych z funkcją obwiedni słowa. Funkcja granicy słowa musi zostać wyłączona, aby używać wielu głosów.

### <a name="disable-word-boundary"></a>Wyłączanie granicy wyrazu

W zależności od języka zestawu SDK `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` mowy, `false` można ustawić `SpeechConfig` właściwość na wystąpienie obiektu.

# <a name="c"></a>[C #](#tab/csharp)

Aby uzyskać więcej <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Aby uzyskać więcej <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Aby uzyskać więcej <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Aby uzyskać więcej <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aby uzyskać więcej <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Obiektowy C](#tab/objectivec)

Aby uzyskać więcej <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Aby uzyskać więcej <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Dostosowywanie stylów mówienia

> [!IMPORTANT]
> Regulacja stylów mówienia będzie działać tylko z głosami neuronowymi.

Domyślnie usługa zamiany tekstu na mowę syntetyzuje tekst przy użyciu neutralnego stylu mówienia zarówno dla głosów standardowych, jak i neuronowych. Dzięki głosom neuronalnym możesz dostosować styl mówienia, aby wyrazić `<mstts:express-as>` radość, empatię lub sentyment z elementem. Jest to opcjonalny element unikatowy dla usługi mowy.

Obecnie korekty stylu mówienia są obsługiwane dla tych głosów neuronowych:
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`

Zmiany są stosowane na poziomie zdania, a styl różni się w zależności od głosu. Jeśli styl nie jest obsługiwany, usługa zwróci mowę w domyślnym neutralnym stylu mówienia.

**Składni**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalnie |
|-----------|-------------|---------------------|
| `style` | Określa styl mówienia. Obecnie style mówienia są specyficzne dla głosu. | Wymagane, jeśli dostosowanie stylu mówienia dla głosu nerwowego. W `mstts:express-as`przypadku używania stylu należy podać styl. Jeśli podana zostanie nieprawidłowa wartość, ten element zostanie zignorowany. |

Ta tabela służy do określenia, które style mówienia są obsługiwane dla każdego głosu neuronowego.

| Połączenia głosowe                   | Styl                     | Opis                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast"`        | Wyraża formalny i profesjonalny ton opowiadania wiadomości |
|                         | `style="customerservice"` | Wyraża przyjazny i pomocny ton dla obsługi klienta  |
|                         | `style="chat"`            | Wyraża swobodny i zrelaksowany ton                         |
|                         | `style="cheerful"`        | Wyraża pozytywny i szczęśliwy ton                         |
|                         | `style="empathetic"`      | Wyraża poczucie troski i zrozumienia               |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Wyraża formalny i profesjonalny ton opowiadania wiadomości |
|                         | `style="customerservice"` | Wyraża przyjazny i pomocny ton dla obsługi klienta  |
|                         | `style="assistant"`       | Wyraża ciepły i zrelaksowany ton dla cyfrowych asystentów    |
|                         | `style="lyrical"`         | Wyraża emocje w sposób melodyjny i sentymentalny         |

**Przykład**

Ten fragment kodu SSML ilustruje sposób, w `<mstts:express-as>` jaki element `cheerful`jest używany do zmiany stylu mówienia na .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Dodawanie lub usuwanie przerwy/pauzy

Użyj `break` elementu, aby wstawić pauzy (lub przerwy) między wyrazami lub zapobiec pauzy automatycznie dodawane przez usługę zamiany tekstu na mowę.

> [!NOTE]
> Ten element służy do zastępowania domyślnego zachowania zamiany tekstu na mowę (TTS) dla wyrazu lub frazy, jeśli syntetyzowana mowa dla tego wyrazu lub frazy brzmi nienaturalnie. `strength` Ustaw, `none` aby zapobiec prozodycznej przerwy, która jest automatycznie wstawiana przez usługę zamiany tekstu na mowę.

**Składni**

```xml
<break strength="string" />
<break time="string" />
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalnie |
|-----------|-------------|---------------------|
| `strength` | Określa względny czas trwania pauzy przy użyciu jednej z następujących wartości:<ul><li>brak</li><li>x-słaby</li><li>Słabe</li><li>medium (domyślnie)</li><li>Silne</li><li>x-silny</li></ul> | Optional (Opcjonalność) |
| `time` | Określa bezwzględny czas trwania pauzy w sekundach lub milisekundach. Przykładami prawidłowych `2s` wartości są i`500` | Optional (Opcjonalność) |

| Siły                      | Opis |
|-------------------------------|-------------|
| Brak lub jeśli nie podano żadnej wartości | 0 ms        |
| x-słaby                        | 250 ms      |
| Słabe                          | 500 ms      |
| średni                        | 750 ms      |
| Silne                        | 1000 ms     |
| x-silny                      | 1250 ms.     |

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Określanie akapitów i zdań

`p`i `s` elementy są używane do oznaczania akapitów i zdań, odpowiednio. W przypadku braku tych elementów usługa zamiany tekstu na mowę automatycznie określa strukturę dokumentu SSML.

Element `p` może zawierać tekst i `audio`następujące `break`elementy: `say-as` `sub`, `mstts:express-as`, `s` `phoneme`, `prosody`, , , , i .

Element `s` może zawierać tekst i `audio`następujące `break`elementy: `say-as` `mstts:express-as`, `sub`, `phoneme`, `prosody`, , i .

**Składni**

```XML
<p></p>
<s></s>
```

**Przykład**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Użyj fonemów, aby poprawić wymowę

Element `ph` jest używany do wymowy fonetycznej w dokumentach SSML. Element `ph` może zawierać tylko tekst, żadnych innych elementów. Zawsze dostarczaj mowę czytelną dla człowieka jako rezerwową.

Alfabety fonetyczne składają się z telefonów, które składają się z liter, cyfr lub znaków, czasami w połączeniu. Każdy telefon opisuje unikalny dźwięk mowy. Jest to w przeciwieństwie do alfabetu łacińskiego, gdzie każda litera może reprezentować wiele dźwięków mówionych. Rozważmy różne wymowy litery "c" w słowach "cukierki" i "zaprzestać", lub różne wymowy kombinacji liter "th" w słowach "thing" i "those".

**Składni**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalnie |
|-----------|-------------|---------------------|
| `alphabet` | Określa alfabet fonetyczny, który ma być używany podczas syntezy wymowy ciągu w atrybucie. `ph` Ciąg określający alfabet musi być określony literami małych liter. Poniżej przedstawiono możliwe alfabety, które można określić.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Międzynarodowy alfabet <span class="docon docon-navigate-external x-hidden-focus"></span> fonetyczny</a></li><li>`sapi`&ndash; [Alfabet fonetyczny usługi mowy](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; Uniwersalny zestaw telefonów</li></ul><br>Alfabet stosuje się `phoneme` tylko do elementu.. | Optional (Opcjonalność) |
| `ph` | Ciąg zawierający telefony, które określają wymowę `phoneme` wyrazu w elemencie. Jeśli określony ciąg zawiera nierozpoznane telefony, usługa zamiany tekstu na mowę (TTS) odrzuca cały dokument SSML i nie generuje żadnego wyjścia mowy określonego w dokumencie. | Wymagane w przypadku korzystania z fonemów. |

**Przykłady**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Użyj niestandardowego leksykonu, aby poprawić wymowę

Czasami TTS nie może dokładnie wymówić słowa, na przykład firmy lub zagranicznej nazwy. Deweloperzy mogą definiować odczyt tych jednostek w SSML przy użyciu `phoneme` i `sub` tag lub zdefiniować odczyt `lexicon` wielu jednostek, odwołując się do niestandardowego pliku leksykonu przy użyciu tagu.

**Składni**

```XML
<lexicon uri="string"/>
```

**Atrybuty**

| Atrybut | Opis                               | Wymagane / opcjonalnie |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Adres zewnętrznego dokumentu PLS. | Wymagany.           |

**Użycia**

Krok 1: Definiowanie niestandardowego leksykonu 

Odczyt jednostek można zdefiniować za pomocą listy niestandardowych elementów leksykonu przechowywanych jako plik xml lub pls.

**Przykład**

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

Każdy `lexeme` element jest elementem leksykonu. `grapheme`zawiera tekst opisujący ortograf `lexeme`. Formularz odczytu można `alias`podać jako . Ciąg telefonu może `phoneme` być dostarczony w elemencie.

Element `lexicon` zawiera co `lexeme` najmniej jeden element. Każdy `lexeme` element zawiera `grapheme` co najmniej jeden `grapheme` `alais`element `phoneme` i jeden lub więcej , i elementów. Element `grapheme` zawiera tekst opisujący <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">ortografię <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Elementy `alias` są używane do wskazania wymowy akronimu lub skróconego terminu. Element `phoneme` zawiera tekst opisujący `lexeme` sposób wymawiane.

Aby uzyskać więcej informacji na temat niestandardowego pliku leksykonu, zobacz [Specyfikacja leksykonu wymowy (PLS) wersja 1.0](https://www.w3.org/TR/pronunciation-lexicon/) w witrynie sieci Web W3C.

Krok 2: Przekaż niestandardowy plik leksykonu utworzony w kroku 1 online, możesz przechowywać go w dowolnym miejscu i sugerujemy przechowywanie go na platformie Microsoft Azure, na przykład [usługi Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Krok 3: Zapoznaj się z niestandardowym plikiem leksykonu w SSML

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BTW" będzie odczytywany jako "By the way". "Benigni" będzie odczytywany z pod warunkiem IPA "bɛʻniɪnji".  

**Ograniczenia**
- Rozmiar pliku: niestandardowy maksymalny limit rozmiaru pliku leksykonu wynosi 100 KB, jeśli przekroczy ten rozmiar, żądanie syntezy zakończy się niepowodzeniem.
- Odświeżanie pamięci podręcznej leksykonu: niestandardowy leksykon będzie buforowany z identyfikatorem URI jako kluczem w usłudze TTS po pierwszym załadowaniu. Leksykon z tym samym identyfikatorem URI nie zostanie ponownie załadowany w ciągu 15 minut, więc niestandardowa zmiana leksykonu musi czekać co najwyżej 15 minut, aby wejść w życie.

**Zestawy fonetyczne usługi mowy**

W powyższym przykładzie używamy Międzynarodowego Alfabetu Fonetycznego, znanego również jako zestaw telefoniczny IPA. Sugerujemy, aby deweloperzy korzystali z IPA, ponieważ jest to międzynarodowy standard. Biorąc pod uwagę, że IPA nie jest łatwe do zapamiętania, usługa`en-US`Mowy `fr-FR` `de-DE`definiuje `es-ES` `ja-JP`zestaw `zh-CN`fonetyczny dla siedmiu języków ( , , , , , , , i `zh-TW`).

Można użyć `sapi` jako vale dla `alphabet` atrybutu z niestandardowych leksykonów, jak pokazano poniżej:

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Aby uzyskać więcej informacji na temat szczegółowego alfabetu fonetycznego usługi mowy, zobacz [zestawy fonetyczne usługi mowy](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Dostosuj prozody

Element `prosody` jest używany do określania zmian na wysokości, kontur, zakres, szybkość, czas trwania i głośność dla tekstu do mowy wyjścia. Element `prosody` może zawierać tekst i `audio`następujące `break`elementy: `prosody` `say-as`, `sub`, `s` `p`, `phoneme`, , , , i .

Ponieważ prozodzowne wartości atrybutów mogą się różnić w szerokim zakresie, aparat rozpoznawania mowy interpretuje przypisane wartości jako sugestię, jakie powinny być rzeczywiste wartości prozodowe wybranego głosu. Usługa zamiany tekstu na mowę ogranicza lub zastępuje wartości, które nie są obsługiwane. Przykładami nieobsługiwałych wartości są rozstaw 1 MHz lub głośność 120.

**Składni**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalnie |
|-----------|-------------|---------------------|
| `pitch` | Wskazuje podziałki linii bazowej dla tekstu. Boisko można wyrazić jako:<ul><li>Wartość bezwzględna, wyrażona jako liczba, po której następuje "Hz" (Hertz). Na przykład 600 Hz.</li><li>Wartość względna, wyrażona jako liczba poprzedzona "+" lub "-", a następnie "Hz" lub "st", która określa kwotę do zmiany wysokości. Na przykład: +80 Hz lub -2st. "O" oznacza, że jednostka zmiany jest półton, który jest połową tonu (pół kroku) w standardowej skali diatonicznej.</li><li>Stała wartość:<ul><li>x-niski</li><li>Niskie</li><li>średni</li><li>wysoka</li><li>x-wysoki</li><li>default</li></ul></li></ul>. | Optional (Opcjonalność) |
| `contour` | Kontur nie jest obsługiwany dla głosów neuronowych. Kontur reprezentuje zmiany wysokości. Zmiany te są reprezentowane jako tablica obiektów docelowych w określonych pozycjach czasu w danych wyjściowych mowy. Każdy obiekt docelowy jest definiowany przez zestawy par parametrów. Przykład: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Pierwsza wartość w każdym zestawie parametrów określa lokalizację zmiany skoku jako procent czasu trwania tekstu. Druga wartość określa kwotę do podniesienia lub obniżenia skoku przy użyciu wartości względnej lub `pitch`wartości wyliczenia dla skoku (patrz ). | Optional (Opcjonalność) |
| `range` | Wartość reprezentująca zakres skoku tekstu. Można wyrazić `range` przy użyciu tych samych wartości bezwzględnych, wartości `pitch`względnych lub wartości wyliczenia, które są używane do opisywania . | Optional (Opcjonalność) |
| `rate` | Wskazuje szybkość mówienia tekstu. Możesz wyrazić `rate` jako:<ul><li>Wartość względna, wyrażona jako liczba, która działa jako mnożnik wartości domyślnej. Na przykład wartość *1* powoduje brak zmian w kursie. Wartość *0,5* powoduje zmniejszenie o połowę stawki. Wartość *3* powoduje potrojanie stawki.</li><li>Stała wartość:<ul><li>x-slow</li><li>Powolny</li><li>średni</li><li>szybki</li><li>x-szybki</li><li>default</li></ul></li></ul> | Optional (Opcjonalność) |
| `duration` | Okres, który powinien ugiąć się, podczas gdy usługa syntezy mowy (TTS) odczytuje tekst w sekundach lub milisekundach. Na przykład *2s* lub *1800ms*. | Optional (Opcjonalność) |
| `volume` | Wskazuje poziom głośności głosu mówiącego. Głośność można wyrazić jako:<ul><li>Wartość bezwzględna, wyrażona jako liczba w zakresie od 0,0 do 100,0, od *najcichszej* do *najgłośniejszej*. Na przykład 75. Wartość domyślna to 100,0.</li><li>Wartość względna, wyrażona jako liczba poprzedzona "+" lub "-", która określa kwotę do zmiany woluminu. Na przykład +10 lub -5,5.</li><li>Stała wartość:<ul><li>Cichy</li><li>x-miękki</li><li>Miękkie</li><li>średni</li><li>Głośno</li><li>x-loud</li><li>default</li></ul></li></ul> | Optional (Opcjonalność) |

### <a name="change-speaking-rate"></a>Zmiana wskaźnika mówienia

Szybkość mówienia może być stosowana do standardowych głosów na poziomie słowa lub zdania. Podczas gdy wskaźnik mówienia może być stosowany tylko do głosów nerwowych na poziomie zdania.

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Zmienianie głośności

Zmiany głośności mogą być stosowane do standardowych głosów na poziomie słowa lub zdania. Zmiany głośności można stosować tylko do głosów neuronowych na poziomie zdania.

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Zmiana skoku

Zmiany wysokości mogą być stosowane do standardowych głosów na poziomie słowa lub zdania. Podczas gdy zmiany tonów mogą być stosowane tylko do głosów neuronowych na poziomie zdania.

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Zmienianie konturu skoku

> [!IMPORTANT]
> Zmiany konturu skoku nie są obsługiwane za pomocą głosów neuronowych.

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>say-as element

`say-as`jest opcjonalnym elementem, który wskazuje typ zawartości (na przykład numer lub datę) tekstu elementu. Zapewnia to wskazówki dla aparatu syntezy mowy o tym, jak wymawiać tekst.

**Składni**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalnie |
|-----------|-------------|---------------------|
| `interpret-as` | Wskazuje typ zawartości tekstu elementu. Aby uzyskać listę typów, zobacz poniższą tabelę. | Wymagany |
| `format` | Zawiera dodatkowe informacje na temat dokładnego formatowania tekstu elementu dla typów zawartości, które mogą mieć niejednoznaczne formaty. SSML definiuje formaty typów zawartości, które z nich korzystają (patrz tabela poniżej). | Optional (Opcjonalność) |
| `detail` | Wskazuje poziom szczegółowości, który ma być używany. Na przykład ten atrybut może zażądać, aby aparat syntezy mowy wymawiał znaki interpunkcyjne. Dla `detail`programu . | Optional (Opcjonalność) |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Poniżej przedstawiono obsługiwane typy `interpret-as` `format` zawartości dla i atrybutów. Dołącz `format` atrybut tylko `interpret-as` wtedy, gdy jest ustawiony na datę i godzinę.

| interpret-as | format | Interpretacja |
|--------------|--------|----------------|
| `address` | | Tekst jest wymawiany jako adres. Silnik syntezy mowy wymawia:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Jako "Jestem na 150 dworze północno-wschodniej Redmond Washington." |
| `cardinal`, `number` | | Tekst jest wymawiany jako liczba kardynalna. Silnik syntezy mowy wymawia:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Jako "Istnieją trzy alternatywy." |
| `characters`, `spell-out` | | Tekst jest wymawiany jako poszczególne litery (przeliterowane). Silnik syntezy mowy wymawia:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Jako "T E S T." |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | Tekst jest wymawiany jako data. Atrybut `format` określa format daty (*d =day, m=month i y=year*). Silnik syntezy mowy wymawia:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Jak "Dzisiaj jest październik dziewiętnasty dwa tysiące szesnastu." |
| `digits`, `number_digit` | | Tekst jest wymawiany jako sekwencja pojedynczych cyfr. Silnik syntezy mowy wymawia:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Jako "1 2 3 4 5 6 7 8 9." |
| `fraction` | | Tekst jest wymawiany jako liczba ułamkowa. Silnik syntezy mowy wymawia:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Jako "trzy ósme cala". |
| `ordinal` | | Tekst jest wymawiany jako liczba porządkowa. Silnik syntezy mowy wymawia:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Jako "Wybierz trzecią opcję". |
| `telephone` | | Tekst jest wymawiany jako numer telefonu. Atrybut `format` może zawierać cyfry reprezentujące kod kraju. Na przykład "1" dla Stanów Zjednoczonych lub "39" dla Włoch. Aparat syntezy mowy może wykorzystać te informacje do kierowania wymową numeru telefonu. Numer telefonu może również zawierać kod kraju, a jeśli tak, ma `format`pierwszeństwo przed kodem kraju w pliku . Silnik syntezy mowy wymawia:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Jako "Mój numer jest numer kierunkowy osiem osiem osiem pięć pięć pięć jeden dwa jeden dwa." |
| `time` | hms12, hms24 | Tekst jest wymawiany jako czas. Atrybut `format` określa, czy czas jest określony przy użyciu zegara 12-godzinnego (hms12) lub zegara 24-godzinnego (hms24). Dwukropek służy do oddzielania liczb reprezentujących godziny, minuty i sekundy. Oto prawidłowe przykłady czasu: 12:35, 1:14:32, 08:15 i 02:50:45. Silnik syntezy mowy wymawia:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Jako "Pociąg odjeżdża o cztery A M." |

**Użycia**

Element `say-as` może zawierać tylko tekst.

**Przykład**

Silnik syntezy mowy mówi następujący przykład jako "Twoja pierwsza prośba była o jeden pokój w dniu dziewiętnastego października dwadzieścia dziesięć z wczesnym przybyciem na dwanaście trzydzieści pięć PM."
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Dodawanie nagranego dźwięku

`audio`jest opcjonalnym elementem, który umożliwia wstawienie dźwięku MP3 do dokumentu SSML. Treść elementu audio może zawierać zwykły tekst lub znaczniki SSML, które są wymawiane, jeśli plik audio jest niedostępny lub nie można go odtworzeć. Ponadto `audio` element może zawierać tekst i następujące `audio` `break`elementy: `s` `phoneme`, `prosody` `say-as`, `p` `sub`, , , , , i .

Każdy dźwięk zawarty w dokumencie SSML musi spełniać następujące wymagania:

* Mp3 musi być obsługiwany w punkcie końcowym HTTPS dostępnego w Internecie. Https jest wymagany, a domena hostująca plik MP3 musi przedstawić prawidłowy, zaufany certyfikat TLS/SSL.
* Plik MP3 musi być prawidłowym plikiem MP3 (MPEG v2).
* Szybkość transmisji bitów musi wynosić 48 kb/s.
* Częstotliwość próbkowania musi wynosić 16 000 Hz.
* Łączny całkowity czas dla wszystkich plików tekstowych i audio w jednej odpowiedzi nie może przekroczyć dziewięćdziesiąt (90) sekund.
* Mp3 nie może zawierać żadnych informacji specyficznych dla klienta ani innych poufnych informacji.

**Składni**

```xml
<audio src="string"/></audio>
```

**Atrybuty**

| Atrybut | Opis                                   | Wymagane / opcjonalnie                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Określa lokalizację/adres URL pliku audio. | Wymagane, jeśli używasz elementu audio w dokumencie SSML. |

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Dodawanie dźwięku w tle

Element `mstts:backgroundaudio` umożliwia dodawanie dźwięku w tle do dokumentów SSML (lub mieszanie pliku audio z zamianą tekstu na mowę). Dzięki `mstts:backgroundaudio` funkcji zapętlanie pliku audio w tle, zanikanie na początku zamiany tekstu na mowę i zanikanie na końcu zamiany tekstu na mowę.

Jeśli dostarczony dźwięk w tle jest krótszy niż zamiana tekstu na mowę lub zanikanie, zostanie ono zapętlone. Jeśli jest dłuższy niż tekst do mowy, zatrzyma się po zakończeniu zanikania.

Na każdy dokument SSML dozwolony jest tylko jeden plik audio w tle. Można jednak przeplatać znaczniki w `audio` `voice` elemencie, aby dodać dodatkowy dźwięk do dokumentu SSML.

**Składni**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atrybuty**

| Atrybut | Opis | Wymagane / opcjonalnie |
|-----------|-------------|---------------------|
| `src` | Określa lokalizację/adres URL pliku audio w tle. | Wymagane, jeśli w dokumencie SSML jest używany dźwięk w tle. |
| `volume` | Określa głośność pliku audio w tle. **Zaakceptowane wartości:** `0` włącznie. `100` Wartością domyślną jest `1`. | Optional (Opcjonalność) |
| `fadein` | Określa czas trwania dźwięku w tle "fade in" jako milisekundy. Wartość domyślna `0`to , co odpowiada brak zanikania. **Zaakceptowane wartości:** `0` włącznie. `10000`  | Optional (Opcjonalność) |
| `fadeout` | Określa czas trwania zanikania dźwięku w tle w milisekundach. Wartość domyślna `0`to , co odpowiada brak zanikania. **Zaakceptowane wartości:** `0` włącznie. `10000`  | Optional (Opcjonalność) |

**Przykład**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Następne kroki

* [Obsługa języków: głosy, ustawienia regionalne, języki](language-support.md)
