---
title: Speech Markup Language (SSML) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Używanie języka znaczników syntezy mowy do kontrolowania wymowy i Prosody w zamiany tekstu na mowę.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 5d11f343a55d30e5d14d6f4ae0ddb1a74d9c61fa
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631979"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Ulepszanie syntezy przy użyciu języka znaczników syntezy mowy (SSML)

Język SSML (Speech Syntezing Language) to język znaczników oparty na języku XML, który umożliwia deweloperom określenie sposobu konwersji tekstu wejściowego na funkcję syntezy mowy przy użyciu usługi zamiany tekstu na mowę. W porównaniu do zwykłego tekstu, SSML umożliwia deweloperom precyzyjne dostosowanie wartości, wymowy, natężenia głosu, ilości i większej liczby danych wyjściowych zamiany tekstu na mowę. Normalne znaki interpunkcyjne, takie jak Wstrzymywanie po upływie okresu lub używanie poprawnych intonation po zakończeniu zdania z znakiem zapytania, są automatycznie obsługiwane.

Implementacja usługi mowy SSML opiera się organizacja World Wide Web Consortium na [języku znaczników funkcji rozpoznawania mowy w wersji 1,0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Znaki w języku chińskim, japońskim i koreańskim są liczone jako dwa znaki do rozliczania. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standardowe, neuronowych i niestandardowe głosy

Wybieraj spośród standardowych i neuronowychych głosów lub twórz własny niestandardowy głos unikatowy dla danego produktu lub marki. 75 standardowych głosów jest dostępnych w więcej niż 45 językach i ustawieniach regionalnych oraz 5 neuronowych głosów jest dostępnych w czterech językach i ustawieniach regionalnych. Aby zapoznać się z pełną listą obsługiwanych języków, ustawień regionalnych i głosów (neuronowych i standard), zobacz temat [Obsługa języków](language-support.md).

Aby dowiedzieć się więcej na temat standardowych, neuronowych i niestandardowych głosów, zobacz [Omówienie zamiany tekstu na mowę](text-to-speech.md).


> [!NOTE]
> Możesz usłyszeć głosy w różnych stylach i postawić do czytania przykładowy tekst przy użyciu [strony Zamiana tekstu na mowę](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features).


## <a name="special-characters"></a>Znaki specjalne

Korzystając z SSML, należy pamiętać, że znaki specjalne, takie jak cudzysłowy, apostrofy i nawiasy muszą być zmienione. Aby uzyskać więcej informacji, zobacz [XML (XML) 1,0: Dodatek D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Obsługiwane elementy SSML

Każdy dokument SSML jest tworzony z elementami SSML (lub tagami). Te elementy służą do dostosowywania wysokości, Prosody, objętości i innych. W poniższych sekcjach szczegółowo opisano, jak każdy element jest używany oraz kiedy element jest wymagany lub opcjonalny.  

> [!IMPORTANT]
> Nie zapomnij użyć podwójnych cudzysłowów wokół wartości atrybutów. Standardy dla poprawnie sformułowanych, prawidłowy kod XML wymaga, aby wartości atrybutów były ujęte w podwójny cudzysłów. Na przykład, `<prosody volume="90">` jest poprawnie sformułowanym, prawidłowym elementem, ale `<prosody volume=90>` nie jest. SSML nie rozpoznaje wartości atrybutów, które nie znajdują się w cudzysłowie.

## <a name="create-an-ssml-document"></a>Tworzenie dokumentu SSML

`speak` jest elementem głównym i jest **wymagany** dla wszystkich dokumentów SSML. `speak`Element zawiera ważne informacje, takie jak wersja, język i definicja słownictwa znaczników.

**Składnia**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `version` | Wskazuje wersję specyfikacji SSML użytej do interpretacji znacznika dokumentu. Bieżąca wersja to 1,0. | Wymagane |
| `xml:lang` | Określa język dokumentu głównego. Wartość może zawierać małe litery, dwuliterowy kod języka (na przykład `en` ) lub kod języka oraz wielkie kraje/region (na przykład `en-US` ). | Wymagane |
| `xmlns` | Określa identyfikator URI dokumentu, który definiuje słownictwo znaczników (typy elementów i nazwy atrybutów) dokumentu SSML. Bieżący identyfikator URI to http://www.w3.org/2001/10/synthesis . | Wymagane |

## <a name="choose-a-voice-for-text-to-speech"></a>Wybieranie głosu dla zamiany tekstu na mowę

`voice`Element jest wymagany. Służy do określania głosu używanego do zamiany tekstu na mowę.

**Składnia**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `name` | Identyfikuje głos używany do wyprowadzania tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz temat [Obsługa języków](language-support.md#text-to-speech). | Wymagane |

**Przykład**

> [!NOTE]
> Ten przykład używa `en-US-AriaRUS` głosu. Aby uzyskać pełną listę obsługiwanych głosów, zobacz temat [Obsługa języków](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Użyj wielu głosów

W obrębie `speak` elementu można określić wiele głosów dla danych wyjściowych zamiany tekstu na mowę. Głosy te mogą znajdować się w różnych językach. Dla każdego głosu tekst musi być opakowany w `voice` element. 

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `name` | Identyfikuje głos używany do wyprowadzania tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz temat [Obsługa języków](language-support.md#text-to-speech). | Wymagane |

> [!IMPORTANT]
> Wiele głosów jest niezgodnych z funkcją granicy słowa. Funkcja granic wyrazów musi być wyłączona, aby można było używać wielu głosów.

### <a name="disable-word-boundary"></a>Wyłącz granicę słowa

W zależności od języka zestawu Speech SDK ustawisz `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` Właściwość na `false` wystąpienie `SpeechConfig` obiektu.

# <a name="c"></a>[C#](#tab/csharp)

Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Obiektowy C](#tab/objectivec)

Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

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

## <a name="adjust-speaking-styles"></a>Dopasuj style mówienia

> [!IMPORTANT]
> Dopasowanie stylów wymawiania będzie działało tylko z głosymi neuronowych.

Domyślnie usługa zamiany tekstu na mowę służy do syntezowania tekstu przy użyciu neutralnego stylu mowy dla głosów standardowych i neuronowych. Za pomocą głosów neuronowych można dostosować styl mówiący do języka Express różne emocji, takie jak cheerfulness, empatię i Calm, lub optymalizować głos dla różnych scenariuszy, takich jak Customer Service, newscasting i Voice Assistant, przy użyciu `mstts:express-as` elementu. Jest to opcjonalny element unikatowy dla usługi mowy.

Obecnie w przypadku tych głosów neuronowych są obsługiwane zmiany stylu mowy:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` Przeglądania
* `zh-CN-XiaohanNeural` Przeglądania
* `zh-CN-XiaomoNeural` Przeglądania
* `zh-CN-XiaoxuanNeural` Przeglądania
* `zh-CN-XiaoruiNeural` Przeglądania

Intensywność stylu mówienia można zmienić w celu lepszego dopasowania do Twojego przypadku użycia. Możesz określić silniejszy lub miękki styl, `styledegree` Aby zwiększyć mowę lub Subdued. 

Obecnie w przypadku tych głosów neuronowych są obsługiwane zmiany stylu mowy:
* `zh-CN-XiaoxiaoNeural`

Oprócz dostosowywania stylu i stopnia odmowy można również dostosować `role` parametr tak, aby głos był imitacją innego wieku i płci. Na przykład głos męski może zwiększyć gęstość i zmienić intonation na imitację głosu żeńskiego.

Obecnie w przypadku tych głosów neuronowych są obsługiwane dostosowania dotyczące odtwarzania ról:
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

Powyższe zmiany są stosowane na poziomie zdania, a style i role są odtwarzane w zależności od głosu. Jeśli styl lub funkcja odtwarzania roli nie jest obsługiwana, usługa zwróci mowę w sposób domyślny. Możesz zobaczyć, jakie style i dla każdego głosu są obsługiwane przez [interfejs API listy głosowej](rest-text-to-speech.md#get-a-list-of-voices) lub za pomocą platformy [tworzenia zawartości audio](https://aka.ms/audiocontentcreation) bez obsługi kodu.

**Składnia**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> W tej chwili `styledegree` obsługuje tylko wartość zh-CN-XiaoxiaoNeural. `role` obsługuje tylko zh-CN-XiaomoNeural i zh-CN-XiaoxuanNeural.

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `style` | Określa styl wymawiania. Obecnie style wymawiające są specyficzne dla głosu. | Wymagane, jeśli ustawienie stylu wymawiania dla głosu neuronowych. Jeśli używasz `mstts:express-as` , należy podać styl. Jeśli podano nieprawidłową wartość, ten element zostanie zignorowany. |
| `styledegree` | Określa intensywność stylu wymawiania. **Akceptowane wartości**: od 0,01 do 2 włącznie. Wartość domyślna to 1, co oznacza intensywność stylu wstępnie zdefiniowanego. Jednostką minimalną jest 0,01, która skutkuje niewielkim tendencją dla stylu docelowego. Wartość 2 powoduje dwustronne zwiększenie intensywności stylu domyślnego.  | Opcjonalnie (w momencie `styledegree` obsługuje tylko wartość zh-CN-XiaoxiaoNeural).|
| `role` | Określa rolę głosu. Głos będzie pełnić rolę innego wieku i płci.  | Opcjonalne (w tej chwili `role` obsługuje tylko wartość zh-CN-XiaomoNeural i zh-CN-XiaoxuanNeural).|

Użyj tej tabeli, aby określić, które style wymawiające są obsługiwane dla każdego głosu neuronowych.

| Połączenia głosowe                   | Styl                     | Opis                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Wyraża formalny, pewny i wiarygodny dźwięk na potrzeby dostarczania wiadomości |
|                         | `style="newscast-casual"` | Wyraża wszechstronny i swobodny sygnał na potrzeby dostarczania ogólnych wiadomości        |
|                         | `style="customerservice"` | Wyraża przyjazne i pomocne oddzwonić dla obsługi klienta  |
|                         | `style="chat"`            | Wyraża swobodny i swobodny sygnał                         |
|                         | `style="cheerful"`        | Wyraża pozytywne i szczęśliwe Tony                         |
|                         | `style="empathetic"`      | Wyraża świadomość Caring i zrozumienie               |
| `en-US-JennyNeural`     | `style="customerservice"` | Wyraża przyjazne i pomocne oddzwonić dla obsługi klienta  |
|                         | `style="chat"`            | Wyraża swobodny i swobodny sygnał                         |
|                         | `style="assistant"`       | Wyraża dźwięk ciepły i swobodny dla asystentów cyfrowych    |
|                         | `style="newscast"`        | Wyraża wszechstronny i swobodny sygnał na potrzeby dostarczania ogólnych wiadomości   |
| `en-US-GuyNeural`       | `style="newscast"`        | Wyrażanie formalnego i profesjonalnego tonu dla wiadomości z narracją |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Wyraża położeniu chłodną, zebraną i złożoną. Tony, gęstość, Prosody jest znacznie bardziej jednorodne w porównaniu z innymi rodzajami mowy.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Wyrażanie formalnego i profesjonalnego tonu dla wiadomości z narracją |
|                         | `style="customerservice"` | Wyraża przyjazne i pomocne oddzwonić dla obsługi klienta  |
|                         | `style="assistant"`       | Wyraża dźwięk ciepły i swobodny dla asystentów cyfrowych    |
|                         | `style="chat"`            | Wyraża swobodny i swobodny sygnał dla Chit-Chat           |
|                         | `style="calm"`            | Wyraża położeniu chłodną, zebraną i złożoną. Tony, gęstość, Prosody jest znacznie bardziej jednorodne w porównaniu z innymi rodzajami mowy.                                |
|                         | `style="cheerful"`        | Wyraża puls i r o wyższej skoku i energii Vocal                         |
|                         | `style="sad"`             | Wyraża ton sorrowful z większą ilością, mniejszą intensywnością i niższą vocalą energię. Typowymi wskaźnikami tego rozpoznawania emocji będzie whimpers lub Crying podczas mowy.            |
|                         | `style="angry"`           | Wyraża ton Angry i wywołuje taką reakcję, z niższą wysokością, wyższą intensywnością i wyższą energią Vocal. Prelegent jest w stanie Irate, wypełniania i nieprawidłowym.       |
|                         | `style="fearful"`         | Wyraża dźwięk obawialiśmy i nerwowy z większą opłatą, wyższą vocalą i szybszym tempem. Prelegent jest w stanie tenseness i uneasiness.                          |
|                         | `style="disgruntled"`     | Wyraża disdainful i oddzwonka. Funkcja mowy tego rozpoznawania emocji wyświetla nieprawdziwa i nietymczasową.              |
|                         | `style="serious"`         | Wyraża sygnał Strict i Command. Głośnik często wydaje się większym stopniu i mniej swobodny dzięki firmie erze.          |
|                         | `style="affectionate"`    | Przedstawia gęstość ciepłą i Affectionate z większą ilością i Vocal energią. Prelegent jest w stanie zwrócić uwagę na odbiornik. "Osobowość" prelegenta często jest endearinga.          |     
|                         | `style="gentle"`          | Wyraża łagodne, łagodnego i przyjemne tony, z niższą ilością i Vocal energią         |   
|                         | `style="lyrical"`         | Wyraża emocji w melodic i Sentimental sposób         |   
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Wyraża przyjazne i pomocne oddzwonić dla obsługi klienta  | 
| `zh-CN-YunyeNeural`     | `style="calm"`            | Wyraża położeniu chłodną, zebraną i złożoną. Tony, gęstość, Prosody jest znacznie bardziej jednorodne w porównaniu z innymi rodzajami mowy.    | 
|                         | `style="cheerful"`        | Wyraża puls i r o wyższej skoku i energii Vocal                         |
|                         | `style="sad"`             | Wyraża ton sorrowful z większą ilością, mniejszą intensywnością i niższą vocalą energię. Typowymi wskaźnikami tego rozpoznawania emocji będzie whimpers lub Crying podczas mowy.            |
|                         | `style="angry"`           | Wyraża ton Angry i wywołuje taką reakcję, z niższą wysokością, wyższą intensywnością i wyższą energią Vocal. Prelegent jest w stanie Irate, wypełniania i nieprawidłowym.       |
|                         | `style="fearful"`         | Wyraża dźwięk obawialiśmy i nerwowy z większą opłatą, wyższą vocalą i szybszym tempem. Prelegent jest w stanie tenseness i uneasiness.                          |
|                         | `style="disgruntled"`     | Wyraża disdainful i oddzwonka. Funkcja mowy tego rozpoznawania emocji wyświetla nieprawdziwa i nietymczasową.              |
|                         | `style="serious"`         | Wyraża sygnał Strict i Command. Głośnik często wydaje się większym stopniu i mniej swobodny dzięki firmie erze.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Wyraża puls i r o wyższej skoku i energii Vocal                         |
|                         | `style="sad"`             | Wyraża ton sorrowful z większą ilością, mniejszą intensywnością i niższą vocalą energię. Typowymi wskaźnikami tego rozpoznawania emocji będzie whimpers lub Crying podczas mowy.            |
|                         | `style="angry"`           | Wyraża ton Angry i wywołuje taką reakcję, z niższą wysokością, wyższą intensywnością i wyższą energią Vocal. Prelegent jest w stanie Irate, wypełniania i nieprawidłowym.       |
|                         | `style="fearful"`         | Wyraża dźwięk obawialiśmy i nerwowy z większą opłatą, wyższą vocalą i szybszym tempem. Prelegent jest w stanie tenseness i uneasiness.                          |
|                         | `style="disgruntled"`     | Wyraża disdainful i oddzwonka. Funkcja mowy tego rozpoznawania emocji wyświetla nieprawdziwa i nietymczasową.              |
|                         | `style="serious"`         | Wyraża sygnał Strict i Command. Głośnik często wydaje się większym stopniu i mniej swobodny dzięki firmie erze.    |
|                         | `style="depressed"`       | Wyraża ton melancholic i despondent z niższą wysokością i energią    |
|                         | `style="embarrassed"`     | Wyraża niepewne i wątpliwości ton, gdy prelegent jest niewygodny   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Wyraża puls i r o wyższej skoku i energii Vocal                         |
|                         | `style="sad"`             | Wyraża ton sorrowful z większą ilością, mniejszą intensywnością i niższą vocalą energię. Typowymi wskaźnikami tego rozpoznawania emocji będzie whimpers lub Crying podczas mowy.            |
|                         | `style="angry"`           | Wyraża ton Angry i wywołuje taką reakcję, z niższą wysokością, wyższą intensywnością i wyższą energią Vocal. Prelegent jest w stanie Irate, wypełniania i nieprawidłowym.       |
|                         | `style="fearful"`         | Wyraża dźwięk obawialiśmy i nerwowy z większą opłatą, wyższą vocalą i szybszym tempem. Prelegent jest w stanie tenseness i uneasiness.                          |
|                         | `style="disgruntled"`     | Wyraża disdainful i oddzwonka. Funkcja mowy tego rozpoznawania emocji wyświetla nieprawdziwa i nietymczasową.              |
|                         | `style="serious"`         | Wyraża sygnał Strict i Command. Głośnik często wydaje się większym stopniu i mniej swobodny dzięki firmie erze.    |
|                         | `style="embarrassed"`     | Wyraża niepewne i wątpliwości ton, gdy prelegent jest niewygodny   |
|                         | `style="affectionate"`    | Przedstawia gęstość ciepłą i Affectionate z większą ilością i Vocal energią. Prelegent jest w stanie zwrócić uwagę na odbiornik. "Osobowość" prelegenta często jest endearinga.          |     
|                         | `style="gentle"`          | Wyraża łagodne, łagodnego i przyjemne tony, z niższą ilością i Vocal energią         |   
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Wyraża puls i r o wyższej skoku i energii Vocal                         |
|                         | `style="angry"`           | Wyraża ton Angry i wywołuje taką reakcję, z niższą wysokością, wyższą intensywnością i wyższą energią Vocal. Prelegent jest w stanie Irate, wypełniania i nieprawidłowym.       |
|                         | `style="fearful"`         | Wyraża dźwięk obawialiśmy i nerwowy z większą opłatą, wyższą vocalą i szybszym tempem. Prelegent jest w stanie tenseness i uneasiness.                          |
|                         | `style="disgruntled"`     | Wyraża disdainful i oddzwonka. Funkcja mowy tego rozpoznawania emocji wyświetla nieprawdziwa i nietymczasową.              |
|                         | `style="serious"`         | Wyraża sygnał Strict i Command. Głośnik często wydaje się większym stopniu i mniej swobodny dzięki firmie erze.    |
|                         | `style="depressed"`       | Wyraża ton melancholic i despondent z niższą wysokością i energią    |
|                         | `style="gentle"`          | Wyraża łagodne, łagodnego i przyjemne tony, z niższą ilością i Vocal energią         |  
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Wyraża puls i r o wyższej skoku i energii Vocal                         |
|                         | `style="angry"`           | Wyraża ton Angry i wywołuje taką reakcję, z niższą wysokością, wyższą intensywnością i wyższą energią Vocal. Prelegent jest w stanie Irate, wypełniania i nieprawidłowym.       |
|                         | `style="fearful"`         | Wyraża dźwięk obawialiśmy i nerwowy z większą opłatą, wyższą vocalą i szybszym tempem. Prelegent jest w stanie tenseness i uneasiness.                          |
|                         | `style="disgruntled"`     | Wyraża disdainful i oddzwonka. Funkcja mowy tego rozpoznawania emocji wyświetla nieprawdziwa i nietymczasową.              |
|                         | `style="serious"`         | Wyraża sygnał Strict i Command. Głośnik często wydaje się większym stopniu i mniej swobodny dzięki firmie erze.    |
|                         | `style="depressed"`       | Wyraża ton melancholic i despondent z niższą wysokością i energią    |
|                         | `style="gentle"`          | Wyraża łagodne, łagodnego i przyjemne tony, z niższą ilością i Vocal energią         |   
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Wyraża ton sorrowful z większą ilością, mniejszą intensywnością i niższą vocalą energię. Typowymi wskaźnikami tego rozpoznawania emocji będzie whimpers lub Crying podczas mowy.            |
|                         | `style="angry"`           | Wyraża ton Angry i wywołuje taką reakcję, z niższą wysokością, wyższą intensywnością i wyższą energią Vocal. Prelegent jest w stanie Irate, wypełniania i nieprawidłowym.       |
|                         | `style="fearful"`         | Wyraża dźwięk obawialiśmy i nerwowy z większą opłatą, wyższą vocalą i szybszym tempem. Prelegent jest w stanie tenseness i uneasiness.                          |

Użyj tej tabeli, aby określić, które role są obsługiwane przez każdy głos neuronowych.

| Połączenia głosowe                   | Rola                       | Opis                                                 |
|-------------------------|----------------------------|-------------------------------------------------------------|
| `zh-CN-XiaomoNeural`    | `role="YoungAdultFemale"`  | Głos naśladuje na młodych dorosłych samic.                 |
|                         | `role="OlderAdultMale"`    | Głos naśladuje do starszej osoby dorosłej.                   |
|                         | `role="Girl"`              | Dźwięk naśladuje dziewczynka.                               |
|                         | `role="Boy"`               | Głos naśladuje Boy.                                |
| `zh-CN-XiaoxuanNeural`  | `role="YoungAdultFemale"`  | Głos naśladuje na młodych dorosłych samic.                 |
|                         | `role="OlderAdultFemale"`  | Głos naśladuje do starszej osoby dorosłej.                 |
|                         | `role="OlderAdultMale"`    | Głos naśladuje do starszej osoby dorosłej.                   |

**Przykład**

Ten fragment kodu SSML ilustruje, w jaki sposób `<mstts:express-as>` element jest używany do zmiany stylu wymawiania na `cheerful` .

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

Ten fragment kodu SSML ilustruje, jak `styledegree` atrybut jest używany do zmiany intensywności stylu wymawiania dla XiaoxiaoNeural.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

Ten fragment kodu SSML ilustruje, w jaki sposób `role` atrybut jest używany do zmiany roli Play dla XiaomoNeural.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Dodawanie lub usuwanie przerwy/wstrzymania

Użyj `break` elementu, aby wstawić pauzy (lub przerwy) między wyrazami lub zapobiec automatycznemu dodawaniu przez usługę zamiany tekstu na mowę.

> [!NOTE]
> Użyj tego elementu, aby przesłonić domyślne zachowanie zamiany tekstu na mowę (TTS) dla wyrazu lub frazy, jeśli wydawane przez nich słowo lub fraza jest odtwarzane jako nienaturalne. Ustaw `strength` `none` , aby nie granicę prozodyczną przerwania, które jest automatycznie wstawiane przez usługę zamiany tekstu na mowę.

**Składnia**

```xml
<break strength="string" />
<break time="string" />
```

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `strength` | Określa względny czas trwania pauzy przy użyciu jednej z następujących wartości:<ul><li>brak</li><li>x — słabe</li><li>lekko</li><li>Średni (domyślnie)</li><li>najwyższy</li><li>x — Strong</li></ul> | Opcjonalne |
| `time` | Określa bezwzględny czas przerwy w sekundach lub milisekund, ta wartość powinna być ustawiona poniżej 5000ms. Przykłady prawidłowych wartości to `2s` i `500ms` | Opcjonalne |

| Naprężeni                      | Opis |
|-------------------------------|-------------|
| Brak lub nie podano wartości | 0 MS        |
| x — słabe                        | 250 MS      |
| lekko                          | 500 ms      |
| średni                        | 750 ms      |
| najwyższy                        | 1000 MS     |
| x — Strong                      | 1250 MS     |

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Dodaj wyciszenie

Użyj `mstts:silence` elementu, aby wstawić pauzy przed tekstem lub po nim albo między 2 sąsiednimi zdaniami. 

> [!NOTE]
>Różnica między `mstts:silence` i `break` jest, którą `break` można dodać do dowolnego miejsca w tekście, ale ciszenie działa tylko na początku lub na końcu tekstu wejściowego lub na granicy dwóch sąsiadujących zdań.  


**Składnia**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `type` | Określa lokalizację, w której ma zostać dodany wyciszenie: <ul><li>Wiodące — na początku tekstu </li><li>Ślad — na końcu tekstu </li><li>Sentenceboundary — między sąsiednimi zdaniami </li></ul> | Wymagane |
| `Value` | Określa bezwzględny czas przerwy w sekundach lub milisekund, ta wartość powinna być ustawiona poniżej 5000ms. Przykłady prawidłowych wartości to `2s` i `500ms` | Wymagane |

**Przykład** W tym przykładzie `mtts:silence` służy do dodawania 200 MS wyciszenia między dwoma zdaniami.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">  
<voice name="en-US-AriaNeural"> 
<mstts:silence  type="Sentenceboundary" value="200ms"/> 
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way. 
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time. 
</voice> 
</speak> 
```

## <a name="specify-paragraphs-and-sentences"></a>Określ akapity i zdania

`p``s`elementy i są używane do określenia odpowiednio akapitów i zdań. W przypadku braku tych elementów usługa zamiany tekstu na mowę automatycznie określa strukturę dokumentu SSML.

`p`Element może zawierać tekst i następujące elementy:,,,,,, `audio` `break` `phoneme` `prosody` `say-as` `sub` `mstts:express-as` , i `s` .

`s`Element może zawierać tekst i następujące elementy:,,,,, `audio` `break` `phoneme` `prosody` `say-as` `mstts:express-as` i `sub` .

**Składnia**

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

## <a name="use-phonemes-to-improve-pronunciation"></a>Aby ulepszyć wymowę, użyj fonemów

`ph`Element jest używany do wymowy fonetycznej w dokumentach SSML. `ph`Element może zawierać tylko tekst, nie inne elementy. Zawsze dostarczaj mowę do odczytu przez człowieka jako rezerwowe.

Alfabety fonetyczne składają się z telefonów, które składają się z liter, cyfr lub znaków, czasami w połączeniu. Każdy telefon opisuje unikatowy dźwięk mowy. Jest to w przeciwieństwie do alfabetu łacińskiego, gdzie jakakolwiek litera może reprezentować wiele dźwięków wypowiadanych. Rozważ różne wymowy literę "c" w słowach "Candy" i "zaprzestanie" albo różne wymowy kombinacji liter "TH" w słowach "rzecz" i "te".

> [!NOTE]
> Tag fonemów nie jest obsługiwany w przypadku tych 5 głosów (et-EE-AnuNeural, GA-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural i MT-MT-GarceNeural).

**Składnia**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `alphabet` | Określa alfabet fonetyczny do użycia podczas syntezowania wymowy ciągu w `ph` atrybucie. Ciąg określający alfabet musi być określony małymi literami. Poniżej przedstawiono możliwe litery, które można określić.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Międzynarodowy alfabet <span class="docon docon-navigate-external x-hidden-focus"></span> fonetyczny</a></li><li>`sapi`&ndash; [Mowa — alfabet fonetyczny](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Uniwersalny zestaw telefonu</a></li></ul><br>Alfabet ma zastosowanie tylko do `phoneme` elementu w elemencie.. | Opcjonalne |
| `ph` | Ciąg zawierający telefony, które określają wymowę wyrazu w `phoneme` elemencie. Jeśli określony ciąg zawiera nierozpoznane telefony, usługa zamiany tekstu na mowę (TTS) odrzuci cały dokument SSML i nie wygeneruje żadnych danych wyjściowych mowy określonych w dokumencie. | Wymagany, jeśli jest używany fonemów. |

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

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Korzystanie z leksykonów niestandardowych w celu usprawnienia wymowy

Czasami usługa zamiany tekstu na mowę nie może dokładnie wymówić wyrazu. Na przykład nazwa firmy lub termin medyczny. Deweloperzy mogą definiować, jak pojedyncze jednostki są odczytywane w SSML przy użyciu `phoneme` `sub` tagów i. Jeśli jednak chcesz zdefiniować sposób odczytywania wielu jednostek, możesz utworzyć niestandardową Leksykon przy użyciu `lexicon` znacznika.

> [!NOTE]
> Leksykon niestandardowy obecnie obsługuje kodowanie UTF-8. 

> [!NOTE]
> Leksykon niestandardowy nie jest obsługiwany w przypadku tych 5 głosów (et-EE-AnuNeural, GA-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural i MT-MT-GarceNeural).


**Składnia**

```XML
<lexicon uri="string"/>
```

**Atrybuty**

| Atrybut | Opis                               | Wymagane/opcjonalne |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Adres zewnętrznego dokumentu innych obszarów roboczych. | Wymagane.           |

**Użycie**

Aby zdefiniować sposób odczytywania wielu jednostek, możesz utworzyć niestandardową Leksykon, który jest przechowywany jako plik XML lub innych obszarów roboczych. Poniżej znajduje się przykładowy plik XML.

```xml
<?xml version="1.0" encoding="UTF-8"?>
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

`lexicon`Element zawiera co najmniej jeden `lexeme` element. Każdy `lexeme` element zawiera co najmniej jeden `grapheme` element i jeden lub więcej `grapheme` `alias` elementów,, i `phoneme` . `grapheme`Element zawiera tekst opisujący <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthography <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. `alias`Elementy są używane do wskazania wymowy akronimu lub skróconego terminu. `phoneme`Element zawiera tekst opisujący sposób `lexeme` wymawiania.

Należy pamiętać, że nie można bezpośrednio ustawić wymowy frazy przy użyciu leksykonu niestandardowego. Jeśli musisz ustawić wymowę dla akronimu lub skróconego terminu, najpierw Podaj `alias` wartość, a następnie skojarz ją `phoneme` z `alias` . Na przykład:

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>ScotlandMV</alias> 
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme> 
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

Można również bezpośrednio podać oczekiwany `alias` termin dla akronimu lub skracania. Na przykład:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>Scotland Media Wave</alias> 
  </lexeme>
```

> [!IMPORTANT]
> W `phoneme` przypadku używania IPA element nie może zawierać białych znaków.

Aby uzyskać więcej informacji na temat niestandardowego pliku leksykonu, zobacz [Specyfikacja leksykonu wymowy (innych obszarów roboczych) w wersji 1,0](https://www.w3.org/TR/pronunciation-lexicon/).

Następnie opublikuj niestandardowy plik leksykonu. Chociaż nie mamy ograniczeń dotyczących lokalizacji tego pliku, zalecamy korzystanie z [usługi Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-portal.md).

Po opublikowaniu słownika niestandardowego można odwołać się do niego z SSML.

> [!NOTE]
> `lexicon`Element musi znajdować się wewnątrz `voice` elementu.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

W przypadku korzystania z tego słownika niestandardowego "BTW" zostanie odczytana jako "w sposób". "Benigni" zostanie odczytany przy użyciu podanej IPA "bɛ ˈ ni ː NJI".  

**Ograniczenia**
- Rozmiar pliku: niestandardowy limit rozmiaru pliku leksykonu to 100KB, jeśli ten rozmiar zostanie przekroczony, żądanie syntezy zakończy się niepowodzeniem.
- Odświeżanie pamięci podręcznej leksykonu: niestandardowy Leksykon zostanie zapisany w pamięci podręcznej przy użyciu identyfikatora URI w usłudze TTS podczas pierwszego ładowania. Leksykon z tym samym identyfikatorem URI nie zostanie ponownie załadowany w ciągu 15 minut, więc zmiana leksykonu niestandardowego musi odczekać od 15 minut, aby zaczęła obowiązywać.

**Zestawy fonetyczne usługi mowy**

W powyższym przykładzie używamy międzynarodowego alfabetu fonetycznego, nazywanego również numerem telefonu IPA. Sugerujemy, aby deweloperzy korzystali z IPA, ponieważ jest to standard międzynarodowy. W przypadku niektórych znaków IPA mają one wersję "prekomponowad" i "dekomponowad", gdy jest reprezentowana przy użyciu standardu Unicode. Leksykon niestandardowy obsługuje tylko rozłożone znaki Unicode.

Biorąc pod uwagę, że IPA nie jest łatwy do zapamiętania, usługa mowy definiuje zestaw fonetyczny dla siedmiu języków (,,,,, `en-US` `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` i `zh-TW` ).

Można użyć `sapi` jako wartość dla `alphabet` atrybutu z niestandardowymi leksykonami, jak pokazano poniżej:

```xml
<?xml version="1.0" encoding="UTF-8"?>
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

Aby uzyskać więcej informacji na temat szczegółowej litery fonetycznej usługi rozpoznawania mowy, zobacz [zestawy fonetyczne usługi Speech](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Dostosuj Prosody

`prosody`Element służy do określania zmian wysokości, rozkładu, zakresu, szybkości, czasu trwania i objętości dla danych wyjściowych zamiany tekstu na mowę. `prosody`Element może zawierać tekst i następujące elementy:,,,,,, `audio` `break` `p` `phoneme` `prosody` `say-as` `sub` , i `s` .

Ponieważ wartości atrybutów granicę prozodyczną mogą się różnić w różnych zakresach, aparat rozpoznawania mowy interpretuje przypisane wartości jako sugestię rzeczywistych wartości granicę prozodyczną wybranego głosu. Usługa zamiany tekstu na mowę lub zastępuje wartości, które nie są obsługiwane. Przykłady nieobsługiwanych wartości to wysokość 1 MHz lub objętość 120.

**Składnia**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `pitch` | Wskazuje gęstość linii bazowej dla tekstu. Możesz wyrazić gęstość jako:<ul><li>Wartość bezwzględna wyrażona jako liczba, po której następuje "Hz" (Hz). Na przykład `<prosody pitch="600Hz">some text</prosody>`.</li><li>Wartość względna, wyrażona jako liczba poprzedzona znakiem "+" lub "-", po której następuje "Hz" lub "St", która określa ilość, aby zmienić gęstość. Na przykład: `<prosody pitch="+80Hz">some text</prosody>` lub `<prosody pitch="-2st">some text</prosody>` . "St" wskazuje, że jednostka zmiany to semitone, czyli połowę tonu (pół kroku) w standardowej skali Diatonic.</li><li>Stała wartość:<ul><li>x — niska</li><li>małą</li><li>średni</li><li>wysoka</li><li>x — wysoka</li><li>default</li></ul></li></ul> | Opcjonalne |
| `contour` |Rozkład obsługuje teraz zarówno neuronowych, jak i standardowe głosy. Rozkład reprezentuje zmiany w wysokości. Te zmiany są reprezentowane jako tablica obiektów docelowych w określonych miejscach w danych wyjściowych mowy. Każdy element docelowy jest definiowany przez zestawy par parametrów. Na przykład: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Pierwsza wartość w każdym zestawie parametrów określa lokalizację zmiany w postaci procentu czasu trwania tekstu. Druga wartość określa wielkość, aby podnieść lub obniżyć gęstość, przy użyciu wartości względnej lub wartości wyliczenia dla skoku (zobacz `pitch` ). | Opcjonalne |
| `range` | Wartość, która reprezentuje zakres skoku dla tekstu. Można wyrazić `range` przy użyciu tych samych wartości bezwzględnych, wartości względnych lub wartości wyliczenia, które są używane do opisywania `pitch` . | Opcjonalne |
| `rate` | Wskazuje stawkę głosu tekstu. Można wyrazić `rate` jako:<ul><li>Wartość względna wyrażona jako liczba, która działa jako mnożnik wartości domyślnej. Na przykład wartość *1* powoduje brak zmian w szybkości. Wartość *0,5* skutkuje halving szybkością. Wartość *3* powoduje przekroczenie stawki.</li><li>Stała wartość:<ul><li>x — powolne</li><li>opóźnienie</li><li>średni</li><li>szybki</li><li>x — Fast</li><li>default</li></ul></li></ul> | Opcjonalne |
| `duration` | Okres, który powinien upłynąć, gdy usługa synteza mowy (TTS) odczytuje tekst w sekundach lub milisekundach. Na przykład *2S* lub *1800ms*. Czas trwania obsługuje tylko głosy standardowe.| Opcjonalne |
| `volume` | Wskazuje poziom głośności głosu. Wolumin można wyrazić jako:<ul><li>Wartość bezwzględna wyrażona jako liczba z zakresu od 0,0 do 100,0, od *najcisza* do *głośnie*. Na przykład 75. Wartość domyślna to 100,0.</li><li>Wartość względna wyrażona jako liczba poprzedzona znakiem "+" lub "-", która określa wielkość zmiany woluminu. Na przykład + 10 lub-5,5.</li><li>Stała wartość:<ul><li>automatycznie</li><li>x — niewygładzone</li><li>rezerwacje</li><li>średni</li><li>głośn</li><li>x-głośne</li><li>default</li></ul></li></ul> | Opcjonalne |

### <a name="change-speaking-rate"></a>Zmień częstotliwość mówienia

Stawkę głosu można zastosować do neuronowych głosów i głosów standardowych na poziomie wyrazu lub zdania. 

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Zmień wolumin

Zmiany woluminów mogą być stosowane do głosów standardowych na poziomie wyrazu lub zdania. Zmiany woluminów mogą być stosowane tylko do głosów neuronowych na poziomie zdania.

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

### <a name="change-pitch"></a>Zmień wysokość

Zmiany wysokości mogą być stosowane do głosów standardowych na poziomie wyrazu lub zdania. Zmiany szerokości mogą być stosowane tylko do głosów neuronowych na poziomie zdania.

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Zmień rozkład wysokości

> [!IMPORTANT]
> Zmiany rozkładu pochylenia są teraz obsługiwane z głosymi neuronowych.

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room? 
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>"Powiedz" jako element

`say-as` jest elementem opcjonalnym, który wskazuje typ zawartości (na przykład liczbę lub datę) tekstu elementu. Zapewnia to wskazówkę dotyczącą aparatu syntezy mowy dotyczącą sposobu wymawiania tekstu.

**Składnia**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `interpret-as` | Wskazuje typ zawartości tekstu elementu. Aby zapoznać się z listą typów, zobacz poniższą tabelę. | Wymagane |
| `format` | Zawiera dodatkowe informacje na temat precyzyjnego formatowania tekstu elementu dla typów zawartości, które mogą mieć niejednoznaczne formaty. SSML definiuje formaty dla typów zawartości, które ich używają (patrz tabela poniżej). | Opcjonalne |
| `detail` | Wskazuje poziom szczegółowości, który ma być wypowiadany. Na przykład ten atrybut może zażądać, aby aparat syntezy mowy wymawiał znaki interpunkcyjne. Nie zdefiniowano standardowych wartości dla `detail` . | Opcjonalne |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Poniżej przedstawiono obsługiwane typy zawartości dla `interpret-as` `format` atrybutów i. Uwzględnij `format` atrybut tylko wtedy, gdy `interpret-as` jest ustawiony na datę i godzinę.

| Interpretuj jako | format | Interpretacja |
|--------------|--------|----------------|
| `address` | | Tekst jest wymawiany jako adres. Aparat syntezy mowy mówi:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Podobnie jak w przypadku 150th sąd północno-wschodnia (Waszyngton). |
| `cardinal`, `number` | | Tekst jest wymawiany jako numer kardynalny. Aparat syntezy mowy mówi:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Jak "Istnieją trzy alternatywy". |
| `characters`, `spell-out` | | Tekst jest wymawiany jako pojedyncze litery (wypisane). Aparat syntezy mowy mówi:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Jako "T E S T". |
| `date` | DMY, MDR, YMD, YDM, ym, my, MD, DM, d, m, y | Tekst jest wymawiany jako Data. Ten `format` atrybut określa format daty (*d = Day, m = month i y = Year*). Aparat syntezy mowy mówi:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Jako "dzisiaj jest Nineteenth październik 2016." |
| `digits`, `number_digit` | | Tekst jest wymawiany jako sekwencja pojedynczych cyfr. Aparat syntezy mowy mówi:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Jako "1 2 3 4 5 6 7 8 9". |
| `fraction` | | Tekst jest wymawiany jako liczba ułamkowa. Aparat syntezy mowy mówi:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Jako "trzy osiem cala". |
| `ordinal` | | Tekst jest wymawiany jako numer porządkowy. Aparat syntezy mowy mówi:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Jako "Wybierz trzecią opcję". |
| `telephone` | | Tekst jest wymawiany jako numer telefonu. Ten `format` atrybut może zawierać cyfry, które reprezentują kod kraju. Na przykład "1" dla Stany Zjednoczone lub "39" dla Włoch. Aparat syntezy mowy może korzystać z tych informacji w celu podzielenia wymowy numeru telefonu. Numer telefonu może zawierać również kod kraju, a jeśli tak, ma pierwszeństwo przed kodem kraju w `format` . Aparat syntezy mowy mówi:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Jako "mój numer jest kodem obszaru 8 8 8 5 5 5 1 2 1 2." |
| `time` | hms12, hms24 | Tekst jest wymawiany jako czas. Ten `format` atrybut określa, czy czas jest określony przy użyciu zegara 12-godzinnego (hms12), czy 24-godzinnego zegara (hms24). Użyj dwukropka do oddzielenia liczb reprezentujących godziny, minuty i sekundy. Poniżej przedstawiono prawidłowe przykłady czasu: 12:35, 1:14:32, 08:15 i 02:50:45. Aparat syntezy mowy mówi:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Jako "pozostała część" uczenia w czterech A M ". |

**Użycie**

`say-as`Element może zawierać tylko tekst.

**Przykład**

Aparat syntezy mowy mówi Poniższy przykład jako "pierwsze żądanie było w jednym pokoju od Nineteenth października 20 10 z wczesnym nadejściem o godzinie 12 35 PM".
 
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

## <a name="add-recorded-audio"></a>Dodaj zarejestrowane audio

`audio` to opcjonalny element, który umożliwia wstawianie audio MP3 do dokumentu SSML. Treść elementu audio może zawierać zwykły tekst lub SSML znaczników, które są wymawiane, jeśli plik dźwiękowy jest niedostępny lub nieosiągalny. Ponadto `audio` element może zawierać tekst i następujące elementy:,,,,,, `audio` `break` `p` `s` `phoneme` `prosody` `say-as` , i `sub` .

Wszystkie audio zawarte w dokumencie SSML muszą spełniać następujące wymagania:

* MP3 musi być hostowany w punkcie końcowym HTTPS dostępnym z Internetu. Wymagany jest protokół HTTPS, a domena hostującym plik MP3 musi mieć prawidłowy zaufany certyfikat TLS/SSL.
* Plik MP3 musi być prawidłowym plikiem MP3 (MPEG v2).
* Szybkość transmisji bitów musi wynosić 48 KB/s.
* Częstotliwość próbkowania musi wynosić 16 000 Hz.
* Łączny całkowity czas dla wszystkich plików tekstowych i dźwiękowych w pojedynczej odpowiedzi nie może przekroczyć 90 (90) sekund.
* MP3 nie może zawierać żadnych informacji poufnych ani innych.

**Składnia**

```xml
<audio src="string"/></audio>
```

**Atrybuty**

| Atrybut | Opis                                   | Wymagane/opcjonalne                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Określa lokalizację/adres URL pliku audio. | Wymagany w przypadku używania elementu audio w dokumencie SSML. |

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

## <a name="add-background-audio"></a>Dodaj dźwięk w tle

`mstts:backgroundaudio`Element umożliwia dodanie dźwięku w tle do dokumentów SSML (lub mieszanie pliku dźwiękowego z funkcją zamiany tekstu na mowę). Za pomocą programu możesz `mstts:backgroundaudio` zapętlać plik audio w tle, stopniowo zanikać na początku tekstu na mowę i stopniowo przechodzić na koniec zamiany tekstu na mowę.

Jeśli podany dźwięk w tle jest krótszy niż tekst-do-mowę lub zanikanie, spowoduje to zapętlenie. Jeśli jest dłuższa niż Zamiana tekstu na mowę, zostanie zatrzymana po zakończeniu zanikania.

Dozwolony jest tylko jeden plik audio w tle dla dokumentu SSML. Można jednak przeplatać `audio` Tagi w obrębie `voice` elementu, aby dodać dodatkowe audio do dokumentu SSML.

**Składnia**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `src` | Określa lokalizację/adres URL pliku dźwiękowego w tle. | Wymagany w przypadku używania dźwięku w tle w dokumencie SSML. |
| `volume` | Określa wolumin pliku dźwiękowego w tle. **Akceptowane wartości**: `0` do `100` włącznie. Wartość domyślna to `1`. | Opcjonalne |
| `fadein` | Określa czas trwania "zanikania" dźwięku w tle (w milisekundach). Wartość domyślna to `0` , która jest równoważna brak zanikania w. **Akceptowane wartości**: `0` do `10000` włącznie.  | Opcjonalne |
| `fadeout` | Określa czas zanikania dźwięku w tle w milisekundach. Wartość domyślna to `0` , która jest równoważna brak zanikania. **Akceptowane wartości**: `0` do `10000` włącznie.  | Opcjonalne |

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

* [Obsługa języków: głosy, zmienne lokalne, Języki](language-support.md)
