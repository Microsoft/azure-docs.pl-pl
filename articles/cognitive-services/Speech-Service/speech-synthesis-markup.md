---
title: Język znaczników syntezy mowy (SSML) — usługa rozpoznawania mowy
titleSuffix: Azure Cognitive Services
description: Używanie języka znaczników syntezy mowy do kontrolowania wymowy i prosodii w funkcji tekstu na mowę.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1d21691af4d52892f507695a56331816b14bf517
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588381"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Ulepszanie syntezy za pomocą języka znaczników syntezy mowy (SSML)

Język znaczników syntezy mowy (SSML, Speech Synthesis Markup Language) to język znaczników oparty na języku XML, który umożliwia deweloperom określanie sposobu konwertowania tekstu wejściowego na syntetyzowany mowę przy użyciu usługi zamiany tekstu na mowę. W porównaniu do zwykłego tekstu, język SSML umożliwia deweloperom dostrojenie wysokości, wymowy, szybkości mówienia, głośności i innych danych wyjściowych funkcji tekstu na mowę. Normalna interpunkcja, taka jak wstrzymanie po upływie okresu lub użycie poprawnej intonacji, gdy zdanie kończy się znakiem zapytania, jest automatycznie obsługiwane.

Implementacja usługi rozpoznawania mowy SSML jest oparta na języku organizacja World Wide Web Consortium Speech Synthesis Markup Language w wersji [1.0.](https://www.w3.org/TR/speech-synthesis)

> [!IMPORTANT]
> W przypadku rozliczeń znaki chińskie, japońskie i koreańskie są liczone jako dwa znaki. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="neural-and-custom-voices"></a>Neuronowe i niestandardowe głosy

Używaj neuronowego głosu przypominania ludzkiego lub twórz własny niestandardowy głos unikatowy dla twojego produktu lub marki. Aby uzyskać pełną listę obsługiwanych języków, ustawieniach regionalnych i głosach, zobacz [obsługa języka](language-support.md). Aby dowiedzieć się więcej na temat neuronowych i niestandardowych głosów, zobacz Omówienie funkcji [text-to-speech .](text-to-speech.md)


> [!NOTE]
> Głosy w różnych stylach i wysokość można słyszeć, czytając [przykładowy](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features)tekst, zamiana tekstu na mowę stronie .


## <a name="special-characters"></a>Znaki specjalne

Podczas korzystania z programu SSML należy pamiętać, że znaki specjalne, takie jak znaki cudzysłowu, apostrofy i nawiasy, muszą być znakami ucieczki. Aby uzyskać więcej informacji, [zobacz XML (XML) 1.0: Dodatek D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Obsługiwane elementy SSML

Każdy dokument SSML jest tworzony za pomocą elementów (lub tagów) SSML. Te elementy służą do dopasowywania wysokość, prozaii, woluminu i innych elementów. W poniższych sekcjach opisano sposób korzystania z poszczególnych elementów oraz informacje o tym, kiedy element jest wymagany lub opcjonalny.

> [!IMPORTANT]
> Pamiętaj o używaniu podwójnych cudzysłowów wokół wartości atrybutów. Standardy dla dobrze utworzonego, prawidłowego kodu XML wymagają, aby wartości atrybutów było ujęte w znaki podwójnego cudzysłowu. Na przykład `<prosody volume="90">` to dobrze uformowany, prawidłowy element, ale `<prosody volume=90>` nie. Program SSML może nie rozpoznawać wartości atrybutów, które nie znajdują się w cudzysłowie.

## <a name="create-an-ssml-document"></a>Tworzenie dokumentu SSML

`speak` jest elementem głównym i jest **wymagany dla** wszystkich dokumentów SSML. Element `speak` zawiera ważne informacje, takie jak wersja, język i definicja słownictwa znaczników.

**Składnia**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/Opcjonalne |
|-----------|-------------|---------------------|
| `version` | Wskazuje wersję specyfikacji SSML używaną do interpretowania znaczników dokumentu. Bieżąca wersja to 1.0. | Wymagane |
| `xml:lang` | Określa język dokumentu głównego. Wartość może zawierać małe litery, dwulitowy kod języka (na przykład ) lub kod języka i wielkie litery `en` kraj/region (na przykład `en-US` ). | Wymagane |
| `xmlns` | Określa URI dokumentu, który definiuje słownictwo znaczników (typy elementów i nazwy atrybutów) dokumentu SSML. Bieżący numer URI to http://www.w3.org/2001/10/synthesis . | Wymagane |

## <a name="choose-a-voice-for-text-to-speech"></a>Wybieranie głosu dla funkcji tekstu na mowę

Element `voice` jest wymagany. Służy do określania głosu, który jest używany do mówienia tekstu na mowę.

**Składnia**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `name` | Określa głos używany w przypadku danych wyjściowych funkcji tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [Obsługa języków](language-support.md#text-to-speech). | Wymagane |

**Przykład**

> [!NOTE]
> W tym przykładzie użyto `en-US-JennyNeural` głosu. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [Obsługa języka](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Używanie wielu głosów

W elemencie można określić wiele głosów dla danych wyjściowych funkcji `speak` tekst-mowa. Te głosy mogą być w różnych językach. Dla każdego głosu tekst musi być opakowany w `voice` element.

**Atrybuty**

| Atrybut | Opis | Wymagane/Opcjonalne |
|-----------|-------------|---------------------|
| `name` | Określa głos używany w przypadku danych wyjściowych funkcji tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [Obsługa języka](language-support.md#text-to-speech). | Wymagane |

> [!IMPORTANT]
> Wiele głosów jest niezgodnych z funkcją granic słowa. Aby można było używać wielu głosów, należy wyłączyć funkcję granicy słowa.

### <a name="disable-word-boundary"></a>Wyłączanie granicy wyrazów

W zależności od języka zestawu SDK rozpoznawania mowy właściwość zostanie ustawiona `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` `false` na wartość w wystąpieniu obiektu `SpeechConfig` .

# <a name="c"></a>[C#](#tab/csharp)

Aby uzyskać więcej <a href="/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty" target="_blank"> `SetProperty` </a>informacji, zobacz .

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Aby uzyskać więcej <a href="/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` </a>informacji, zobacz .

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Aby uzyskać więcej <a href="/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` </a>informacji, zobacz .

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Aby uzyskać więcej <a href="/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` </a>informacji, zobacz .

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aby uzyskać więcej <a href="/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#setproperty-string--string-" target="_blank"> `setProperty` </a>informacji, zobacz .

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Aby uzyskać więcej <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>informacji, zobacz .

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Aby uzyskać więcej <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>informacji, zobacz .

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        Good morning!
    </voice>
    <voice name="en-US-GuyNeural">
        Good morning to you too Jenny!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Dostosowywanie stylów mówienia

Domyślnie usługa rozpoznawania tekstu na mowę syntetyzuje tekst przy użyciu neutralnego stylu mowy dla głosów neuronowych. Możesz dostosować styl mówienia, aby wyrażać różne emocje, takie jak emocje, empatia i czucie, lub zoptymalizować głos pod kątem różnych scenariuszy, takich jak obsługa klienta, obsługa wiadomości i asystent głosowy, przy użyciu `mstts:express-as` elementu . Jest to opcjonalny element unikatowy dla usługi Mowa.

Obecnie w przypadku następujących głosów neuronowych obsługiwane są korekty stylu mówienia:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` (Wersja zapoznawcza)
* `zh-CN-XiaohanNeural` (Wersja zapoznawcza)
* `zh-CN-XiaomoNeural` (Wersja zapoznawcza)
* `zh-CN-XiaoxuanNeural` (Wersja zapoznawcza)
* `zh-CN-XiaoruiNeural` (Wersja zapoznawcza)

Można jeszcze bardziej zmienić natężenie stylu mówienia, aby lepiej dopasować go do twojego przypadku użycia. Możesz określić silniejszy lub bardziej miękki styl za pomocą funkcji , aby mowa stanie się bardziej ekspresyjna `styledegree` lub strojona. Obecnie dostosowania stylu mówienia są obsługiwane w przypadku głosów neuronowych w języku chińskim (mandaryńskim, uproszczonym).

Oprócz dostosowywania stylu wypowiedzi i stopnia stylu można również dostosować parametr tak, aby głos wyimał `role` inną płeć i wiek. Na przykład głos mężczyzn może podnieść wysokość i zmienić tonację, aby odimówić głos kobiet, ale nazwa głosu nie zostanie zmieniona. Obecnie obsługiwane są korekty ról dla tych głosów neuronowych w języku chińskim (mandaryńskim, uproszczonym):
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

Powyższe zmiany są stosowane na poziomie zdania, a style i role różnią się w zależności od głosu. Jeśli styl lub rola nie jest obsługiwana, usługa zwróci mowę w domyślny sposób neutralny. Możesz sprawdzić, jakie style i role są obsługiwane dla każdego głosu za pośrednictwem interfejsu API listy [głosów](rest-text-to-speech.md#get-a-list-of-voices) lub za pośrednictwem platformy Tworzenie zawartości dźwiękowej [kodu.](https://aka.ms/audiocontentcreation)

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
> W tej chwili obsługuje tylko neuronowe głosy w języku chińskim `styledegree` (mandaryńskiego, uproszczonym). `role` Obsługuje tylko zh-CN-XiaomoNeural i zh-CN-XiaoxuanNeural.

**Atrybuty**

| Atrybut | Opis | Wymagane/Opcjonalne |
|-----------|-------------|---------------------|
| `style` | Określa styl mówienia. Obecnie style mówienia są specyficzne dla głosu. | Wymagane w przypadku dostosowywania stylu mówienia dla głosu neuronowego. Jeśli używasz `mstts:express-as` , należy podać styl . Jeśli podano nieprawidłową wartość, ten element zostanie zignorowany. |
| `styledegree` | Określa natężenie stylu mówienia. **Akceptowane wartości:** od 0,01 do 2 włącznie. Wartość domyślna to 1, co oznacza wstępnie zdefiniowaną intensywny styl. Minimalna jednostka to 0,01, co powoduje nieco tendencję do stylu docelowego. Wartość 2 powoduje podwojenie domyślnego natężenia stylu.  | Opcjonalne (w tej chwili obsługuje tylko głosy neuronowe w języku `styledegree` chińskim (mandaryńskim, uproszczonym).|
| `role` | Określa odtwarzaną mówienia rolę. Głos będzie działać jako inny wiek i płeć, ale nazwa głosu nie zostanie zmieniona.  | Opcjonalne (w tej chwili obsługuje `role` tylko zh-CN-XiaomoNeural i zh-CN-XiaoxuanNeural).|

Użyj tej tabeli, aby określić, które style mówienia są obsługiwane dla każdego neuronowego głosu.

| Połączenia głosowe                   | Styl                     | Opis                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Wyraża formalny, ufny i autorytatywny ton dostarczania wiadomości |
|                         | `style="newscast-casual"` | Wyraża uniwersalny i nieformalny ton dla ogólnego dostarczania wiadomości        |
|                         | `style="narration-professional"` | Wyrażanie profesjonalnego, obiektywnego tonu podczas czytania zawartości        |
|                         | `style="customerservice"` | Wyraża przyjazny i pomocny ton dla działu obsługi klienta  |
|                         | `style="chat"`            | Wyraża zwykły i swobodny ton                         |
|                         | `style="cheerful"`        | Wyraża pozytywny i pozytywny ton                         |
|                         | `style="empathetic"`      | Wyraża wiedzę i rozumianie               |
| `en-US-JennyNeural`     | `style="customerservice"` | Wyraża przyjazny i pomocny ton dla działu obsługi klienta  |
|                         | `style="chat"`            | Wyraża zwykły i swobodny ton                         |
|                         | `style="assistant"`       | Wyraża ciepły i luźny ton dla asystentów cyfrowych    |
|                         | `style="newscast"`        | Wyraża wszechstronne i swobodne tony dla ogólnego dostarczania wiadomości   |
| `en-US-GuyNeural`       | `style="newscast"`        | Wyraża formalny i profesjonalny ton do narracji wiadomości |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Wyraża chłodne, zebrane i złożone nastawienie podczas mówienia. Ton, wysokość, prosody są znacznie bardziej jednolite w porównaniu z innymi typami mowy.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Wyraża formalny i profesjonalny ton na temat narracji wiadomości |
|                         | `style="customerservice"` | Wyraża przyjazny i pomocny ton dla działu obsługi klienta  |
|                         | `style="assistant"`       | Wyraża ciepły i luźny ton dla asystentów cyfrowych    |
|                         | `style="chat"`            | Wyraża zwykły i swobodny ton do pogawędki           |
|                         | `style="calm"`            | Wyraża chłodne, zebrane i złożone nastawienie podczas mówienia. Ton, wysokość, prosody są znacznie bardziej jednolite w porównaniu z innymi typami mowy.                                |
|                         | `style="cheerful"`        | Wyraża upbeat i wytrenowy ton o większej energii wywłaszowej i nasyconej                         |
|                         | `style="sad"`             | Wyraża zasypujące ton, z wyższą wysokość, mniejszą natężeniem i mniejszą energią nasypów. Typowe wskaźniki tej emocji to napychanie lub cykanie podczas mowy.            |
|                         | `style="angry"`           | Wyraża dźwięk i irytowany ton, z niższą wysokością, większą natężeniem i wyższą energią zębowym. Mówiący jest w stanie irate, displeased, and offended.       |
|                         | `style="fearful"`         | Wyraża wystraszony i denerwny ton, z wyższą wysokość, wyższą energią wydychaną i szybszym szybkością. Prelegent jest w stanie czasowości i niezręczności.                          |
|                         | `style="disgruntled"`     | Wyraża rozgorętny i skarżący się ton. Mowa o tej emocji powoduje niezadowolenie i pogardę.              |
|                         | `style="serious"`         | Wyraża rygorystyczny i poleceń ton. Prelegent często wydaje się bardziej przytłaczający i znacznie mniej zrelaksowany przy użyciu twardego rytmu.          |
|                         | `style="affectionate"`    | Wyraża ciepły i wpływowy ton z wyższą energią nachylienia i osuwu. Mówiący jest w stanie przyciągania uwagi odbiornika. "Osobowość" prelegenta jest często endearną z natury.          |
|                         | `style="gentle"`          | Wyraża wytłoki, grzeczności i tonu, z niższą energią na wywęchu i o mniejszej energii głosu         |
|                         | `style="lyrical"`         | Wyraża emocje w sposób pozytywny i tonacji         |
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Wyraża przyjazny i pomocny ton dla obsługi klienta  |
| `zh-CN-YunyeNeural`     | `style="calm"`            | Wyraża chłodne, zebrane i złożone nastawienie podczas mówienia. Ton, wysokość, prosody są znacznie bardziej jednolite w porównaniu z innymi typami mowy.    |
|                         | `style="cheerful"`        | Wyraża upbeat i wytrenowy ton o większej energii tonu i głosu                         |
|                         | `style="sad"`             | Wyraża zasypujące ton, z wyższą wysokością, mniejszą natężeniem i mniejszą energią na osłodę. Typowe wskaźniki tej emocji to najgorętszy lub cykający się podczas mowy.            |
|                         | `style="angry"`           | Wyraża dźwięk i irytowany ton, z niższą wysokością, większą natężeniem i wyższą energią zębowym. Prelegent jest w stanie irate, disped i offended.       |
|                         | `style="fearful"`         | Wyraża wystraszony i denerwowany ton, z wyższą toną, wyższą energią na wykładzie i szybszym szybkością. Stan prelegenta to czas i nieskomplikowość.                          |
|                         | `style="disgruntled"`     | Wyraża rozgorętny i skarżący się ton. Mowa o tej emocji powoduje niezadowolenie i pogardę.              |
|                         | `style="serious"`         | Wyraża rygorystyczny i poleceń tonu. Prelegent często brzmi bardziej złagodniająco i znacznie mniej złagodnia w przypadku twardego rytmu.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Wyrażanie upbeatnego i zatęchłego tonu z wyższą energią na wywęchu i o większej energii nasyconej                         |
|                         | `style="sad"`             | Wyraża zasypujące ton, z wyższą wysokość, mniejszą natężeniem i mniejszą energią nasypów. Typowe wskaźniki tej emocji to napychanie lub płakanie podczas mowy.            |
|                         | `style="angry"`           | Wyraża wytłoki i rozirytowany ton, z niższą wysokość, większą natężeniem i wyższą energią nasypów. Prelegent jest w stanie irate, disped i offended.       |
|                         | `style="fearful"`         | Wyraża wystraszony i denerwowany ton, z wyższą wysokość, wyższą energią nasyciową i szybszym szybkością. Stan prelegenta to czas i nieskomplikowość.                          |
|                         | `style="disgruntled"`     | Wyraża rozgorętny i skarżący się ton. Mowa o tej emocji powoduje niezadowolenie i pogardę.              |
|                         | `style="serious"`         | Wyraża rygorystyczny i poleceń tonu. Prelegent często brzmi bardziej złagodniająco i znacznie mniej złagodnia w przypadku jędrności.    |
|                         | `style="depressed"`       | Wyraża ton melancholic i despondent z niższą wysokość i energii    |
|                         | `style="embarrassed"`     | Wyraża nieistotny i niesłyszący ton, gdy prelegent jest niewygodny   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Wyraża upbeat i wytrenowy ton o większej energii nachylennej i nasyconej                         |
|                         | `style="sad"`             | Wyraża zasypujące ton, z wyższą wysokością, mniejszą natężeniem i mniejszą energią na osłodę. Typowe wskaźniki tej emocji to najgorętszy lub cykający się podczas mowy.            |
|                         | `style="angry"`           | Wyraża dźwięk i irytowany ton, z niższą wysokością, większą natężeniem i wyższą energią zębowym. Mówiący jest w stanie irate, disped i offended.       |
|                         | `style="fearful"`         | Wyraża wystraszony i denerwowany ton, z wyższą wysokość, wyższą energią nasypową i szybszym szybkością. Prelegent jest w stanie czasowości i niezręczności.                          |
|                         | `style="disgruntled"`     | Wyraża rozgorętny i skarżący się ton. Mowa o tej emocji powoduje niezadowolenie i pogardę.              |
|                         | `style="serious"`         | Wyraża rygorystyczny i poleceń ton. Prelegent często wydaje się bardziej przytłaczający i znacznie mniej zrelaksowany przy użyciu twardego rytmu.    |
|                         | `style="embarrassed"`     | Wyraża niepewny i niesłyszący ton, gdy prelegent jest niewygodny   |
|                         | `style="affectionate"`    | Wyraża ciepły i wpływowy ton z wyższą energią nachylienia i osuwu. Mówiący jest w stanie przyciągania uwagi odbiornika. "Osobowość" prelegenta jest często endearing z natury.          |
|                         | `style="gentle"`          | Wyraża wytłoki, grzeczności i tonu, z niższą energią nachyłu i pochyłu         |
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Wyraża upbeat i wytrenowy ton o większej energii tonu i głosu                         |
|                         | `style="angry"`           | Wyraża dźwięk i irytowany ton, z niższą wysokością, większą natężeniem i wyższą energią zębowym. Mówiący jest w stanie irate, displeased, and offended.       |
|                         | `style="fearful"`         | Wyraża wystraszony i denerwny ton, z wyższą wysokość, wyższą energią wydychaną i szybszym szybkością. Prelegent jest w stanie czasowości i niezręczności.                          |
|                         | `style="disgruntled"`     | Wyraża rozgorętny i skarżący się ton. Mowa o tej emocji powoduje niezadowolenie i pogardę.              |
|                         | `style="serious"`         | Wyraża rygorystyczny i poleceń tonu. Prelegent często brzmi bardziej złagodniająco i znacznie mniej złagodnia w przypadku jędrności.    |
|                         | `style="depressed"`       | Wyraża ton melancholic i despondent z niższą wysokość i energii    |
|                         | `style="gentle"`          | Wyraża wytłoki, grzeczności i tonu, z niższą energią na wywęchu i pochyłu         |
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Wyraża upbeat i wytrenowy ton o większej energii wywłaszowej i nasyconej                         |
|                         | `style="angry"`           | Wyraża wytłony i rozirytowany ton, z niższą wysokość, większą natężeniem i wyższą energią na wytłochu. Prelegent jest w stanie irate, disped i offended.       |
|                         | `style="fearful"`         | Wyraża wystraszony i denerwowany ton, z wyższą wysokość, wyższą energią nasyciową i szybszym szybkością. Stan prelegenta to czas i nieskomplikowość.                          |
|                         | `style="disgruntled"`     | Wyraża rozgorętny i skarżący się ton. Mowa o tej emocji powoduje niezadowolenie i pogardę.              |
|                         | `style="serious"`         | Wyraża rygorystyczny i poleceń tonu. Prelegent często brzmi bardziej złagodniająco i znacznie mniej złagodnia w przypadku jędrności.    |
|                         | `style="depressed"`       | Wyraża ton melancholic i despondent z niższą wysokość i energię    |
|                         | `style="gentle"`          | Wyraża wytłoki, grzecz i cycyt, z niższą energią na wywęchu i pochyłu         |
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Wyraża zasypujące ton, z wyższą wysokość, mniejszą natężeniem i mniejszą energią nasypów. Typowe wskaźniki tej emocji to napychanie lub płakanie podczas mowy.            |
|                         | `style="angry"`           | Wyraża dźwięk i irytowany ton, z niższą wysokością, większą natężeniem i wyższą energią zębowym. Mówiący jest w stanie irate, disped i offended.       |
|                         | `style="fearful"`         | Wyraża wystraszony i denerwny ton, z wyższą wysokość, wyższą energią wydychaną i szybszym szybkością. Prelegent jest w stanie czasowości i niezręczności.                          |

Użyj tej tabeli, aby sprawdzić obsługiwane role i ich definicje.

|Rola                     | Opis                |
|-------------------------|----------------------------|
|`role="Girl"`            | Głos odwzorowywuje się do dziewuchy. |
|`role="Boy"`             | Głos odwzorowywuje chłopca. |
|`role="YoungAdultFemale"`| Głos jest odimówieńcą dla dorosłych kobiet.|
|`role="YoungAdultMale"`  | Głos jest odimówieńcą dla dorosłych mężczyzn.|
|`role="OlderAdultFemale"`| Głos jest odimówieńcą starszej, dla dorosłych kobiet.|
|`role="OlderAdultMale"`  | Głos jest odimówień dla starszego dorosłych mężczyzn.|
|`role="SeniorFemale"`    | Głos odwzorowywuje się starszej kobietie.|
|`role="SeniorMale"`      | Głos odwzorowywuje starszego mężczyznę.|


**Przykład**

Ten fragment kodu SSML ilustruje sposób, w jaki element jest używany do zmiany `<mstts:express-as>` stylu mówienia na `cheerful` .

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

Ten fragment kodu SSML ilustruje sposób, w jaki atrybut jest używany do zmiany natężenia stylu Mówienia dla `styledegree` FragmensjoNeural.
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

Ten fragment kodu SSML ilustruje sposób, w jaki atrybut jest używany do zmiany roli `role` w przypadku FragmenomoNeural.
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

## <a name="add-or-remove-a-breakpause"></a>Dodawanie lub usuwanie przerwania/wstrzymania

Użyj elementu `break` , aby wstawić pauzy (lub przerwy) między wyrazami lub uniemożliwić wstrzymywanie automatycznie dodawane przez usługę tekst na mowę.

> [!NOTE]
> Użyj tego elementu, aby przesłonić domyślne zachowanie funkcji tekst-mowa (TTS) dla słowa lub frazy, jeśli syntetyzowana mowa dla tego słowa lub frazy brzmi nieskonstrukcyjnie. Ustaw `strength` wartość na , aby `none` zapobiec przerwaniu prosodycznemu, który jest automatycznie wstawiany przez usługę typu tekst na mowę.

**Składnia**

```xml
<break strength="string" />
<break time="string" />
```

**Atrybuty**

| Atrybut | Opis | Wymagane/Opcjonalne |
|-----------|-------------|---------------------|
| `strength` | Określa względny czas trwania wstrzymania przy użyciu jednej z następujących wartości:<ul><li>brak</li><li>x-weak</li><li>Słabe</li><li>medium (ustawienie domyślne)</li><li>Silne</li><li>x-strong</li></ul> | Opcjonalne |
| `time` | Określa bezwzględny czas trwania wstrzymania w sekundach lub milisekundach, ta wartość powinna być mniejsza niż 5000ms. Przykłady prawidłowych wartości to `2s` i `500ms` | Opcjonalne |

| Siły                      | Opis |
|-------------------------------|-------------|
| Brak lub jeśli nie podano żadnej wartości | 0 ms        |
| x-weak                        | 250 ms      |
| Słabe                          | 500 ms      |
| średni                        | 750 ms      |
| Silne                        | 1000 ms     |
| x-strong                      | 1250 ms     |

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Dodawanie wyciszenia

Użyj elementu `mstts:silence` , aby wstawić wstrzymywanie przed lub po tekście albo między dwoma sąsiednimi zdaniami.

> [!NOTE]
>Różnica między i polega na tym, że można dodać do dowolnego miejsca w tekście, ale wyciszenie działa tylko na początku lub na końcu tekstu wejściowego lub na granicy 2 sąsiadujących `mstts:silence` `break` `break` zdań.


**Składnia**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/Opcjonalne |
|-----------|-------------|---------------------|
| `type` | Określa lokalizację wyciszenia, które mają zostać dodane: <ul><li>`Leading` — na początku tekstu </li><li>`Tailing` — na końcu tekstu </li><li>`Sentenceboundary` — między sąsiednimi zdaniami </li></ul> | Wymagane |
| `Value` | Określa bezwzględny czas trwania wstrzymania w sekundach lub milisekundach, ta wartość powinna być mniejsza niż 5000ms. Przykłady prawidłowych wartości to `2s` i `500ms` | Wymagane |

**Przykład** W tym przykładzie `mtts:silence` jest używany do dodawania 200 ms wyciszenia między dwoma zdaniami.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="en-US-AriaNeural">
<mstts:silence  type="Sentenceboundary" value="200ms"/>
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way.
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time.
</voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Określanie akapitów i zdań

`p` Elementy i służą odpowiednio do oznaczania akapitów `s` i zdań. W przypadku braku tych elementów usługa funkcji tekstu na mowę automatycznie określa strukturę dokumentu SSML.

Element `p` może zawierać tekst i następujące elementy: , , , , `audio` , , , i `break` `phoneme` `prosody` `say-as` `sub` `mstts:express-as` `s` .

Element `s` może zawierać tekst i następujące elementy: , , , , `audio` , , i `break` `phoneme` `prosody` `say-as` `mstts:express-as` `sub` .

**Składnia**

```XML
<p></p>
<s></s>
```

**Przykład**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
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

## <a name="use-phonemes-to-improve-pronunciation"></a>Poprawianie wymowy za pomocą fonemów

Element `ph` służy do wymowy fejlowej w dokumentach SSML. Element `ph` może zawierać tylko tekst, żadne inne elementy. Zawsze zapewniaj mowę czytelną dla człowieka jako rezerwę.

Alfabety fonetyczne składają się z telefonów, które składają się z liter, cyfr lub znaków, czasami w kombinacji. Każdy telefon opisuje unikatowy dźwięk mowy. Jest to przeciwieńsze alfabetu łacińskiego, w którym każda litera może reprezentować wiele mówionych dźwięków. Rozważ różne wymowy litery "c" w wyrazach "candy" i "przestań" lub różne wymowy kombinacji litery "th" w wyrazach "thing" i "those".

> [!NOTE]
> Tag Phonemes nie jest obecnie obsługiwany w przypadku tych 5 głosów (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnNeural, lv-LV-EveritaNeural i mt-MT-GarceNeural).

**Składnia**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `alphabet` | Określa alfabet fonetyczny do użycia podczas syntezowania wymowy ciągu w `ph` atrybutze. Ciąg określający alfabet musi być określony małymi literami. Poniżej przedstawiono możliwe alfabety, które można określić.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Międzynarodowy alfabet fonetyczny</a></li><li>`sapi`&ndash; [Alfabet fonetyczny usługi rozpoznawania mowy](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Uniwersalny zestaw telefonów</a></li></ul><br>Alfabet ma zastosowanie tylko do `phoneme` elementu w elemencie . | Opcjonalne |
| `ph` | Ciąg zawierający telefony, które określają wymowę słowa w `phoneme` elemencie. Jeśli określony ciąg zawiera nierozpoznane telefony, usługa text-to-speech (TTS) odrzuca cały dokument SSML i nie generuje żadnego z danych wyjściowych mowy określonych w dokumencie. | Wymagane w przypadku używania fonemów. |

**Przykłady**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Używanie niestandardowego leksykonu w celu poprawy wymowy

Czasami usługa rozpoznawania tekstu na mowę nie może dokładnie wymawiać słowa. Na przykład nazwa firmy lub termin medycznych. Deweloperzy mogą definiować sposób odczytywania pojedynczych jednostek w programie SSML przy użyciu `phoneme` `sub` tagów i . Jeśli jednak musisz zdefiniować, jak wiele jednostek jest odczytywanych, możesz utworzyć niestandardowy leksykon przy użyciu `lexicon` tagu .

> [!NOTE]
> Niestandardowy leksykon obsługuje obecnie kodowanie UTF-8.

> [!NOTE]
> W tej chwili niestandardowe leksykony nie są obsługiwane w przypadku tych 5 głosów (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, lv-LV-EveritaNeural i mt-MT-GarceNeural).


**Składnia**

```XML
<lexicon uri="string"/>
```

**Atrybuty**

| Atrybut | Opis                               | Wymagane/Opcjonalne |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Adres zewnętrznego dokumentu PLS. | Wymagane.           |

**Użycie**

Aby zdefiniować sposób odczytywania wielu jednostek, można utworzyć niestandardowy leksykon, który jest przechowywany jako plik XML lub PLS. Poniżej przedstawiono przykładowy plik XML.

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

Element `lexicon` zawiera co najmniej jeden `lexeme` element. Każdy `lexeme` element zawiera co najmniej jeden element i co najmniej jeden element , i `grapheme` `grapheme` `alias` `phoneme` . Element `grapheme` zawiera tekst opisujący <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">ortografię </a>. Elementy `alias` służą do wskazywania wymowy akronimu lub skróconego terminu. Element `phoneme` zawiera tekst opisujący sposób wymawiania `lexeme` elementu .

Należy pamiętać, że nie można bezpośrednio ustawić wymowy frazy przy użyciu niestandardowego leksykonu. Jeśli musisz ustawić wymowę skrótu lub skróconego terminu, najpierw podaj , a następnie skojarz element `alias` `phoneme` z tym . `alias` Na przykład:

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

Możesz również bezpośrednio podać oczekiwane terminy z akronimem lub `alias` skrótem. Na przykład:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>Scotland Media Wave</alias>
  </lexeme>
```

> [!IMPORTANT]
> Element `phoneme` nie może zawierać białych spacji podczas korzystania z IPA.

Aby uzyskać więcej informacji na temat niestandardowego pliku leksykonu, zobacz [Pronunciation Lexicon Specification (PLS) Version 1.0 (Specyfikacja leksykonu wymowy w wersji 1.0).](https://www.w3.org/TR/pronunciation-lexicon/)

Następnie opublikuj niestandardowy plik leksykonu. Chociaż nie ma ograniczeń dotyczących miejsca przechowywania tego pliku, zalecamy używanie Azure Blob Storage [.](../../storage/blobs/storage-quickstart-blobs-portal.md)

Po opublikowaniu niestandardowego leksykonu możesz odwoływać się do niego z pliku SSML.

> [!NOTE]
> Element `lexicon` musi znajdować się wewnątrz `voice` elementu .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
          xmlns:mstts="http://www.w3.org/2001/mstts"
          xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

W przypadku korzystania z tego leksykonu niestandardowego "BTW" będzie odczytywany jako "Przy okazji". "Niegroźny" zostanie odczytany z dostarczonym IPA "b odtąd".

**Ograniczenia**
- Rozmiar pliku: maksymalny limit rozmiaru niestandardowego pliku leksykonu wynosi 100 KB, jeśli rozmiar przekracza ten rozmiar, żądanie syntezy nie powiedzie się.
- Odświeżanie pamięci podręcznej leksykonu: niestandardowe leksykony zostaną z cachedem z URI jako kluczem w usłudze TTS po jego pierwszym załadowaniu. Leksykon o tym samym URI nie zostanie ponownie załadowany w ciągu 15 minut, więc zmiana niestandardowego leksykonu musi odczekać co najwyżej 15 minut, aby zmiany zaszły.

**Zestawy ffontyczne usługi rozpoznawania mowy**

W powyższym przykładzie używamy międzynarodowych alfabetów fonetycznych, nazywanych również zestawem telefonów IPA. Sugerujemy, aby deweloperzy używali standardu IPA, ponieważ jest to standard międzynarodowy. W przypadku niektórych znaków IPA mają one wersję "prekompilowaną" i "rozłożoną" w przypadku reprezentowania za pomocą formatu Unicode. Leksykon niestandardowy obsługuje tylko rozłożone znaki Unicode.

Biorąc pod uwagę, że IPA nie jest łatwa do zapamiętania, usługa rozpoznawania mowy definiuje zestaw fejstyczny dla siedmiu języków `en-US` (, `fr-FR` , , , , , i `de-DE` `es-ES` `ja-JP` `zh-CN` `zh-TW` ).

Możesz użyć jako wartości atrybutu z niestandardowymi `sapi` `alphabet` leksykonami, jak pokazano poniżej:

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

Aby uzyskać więcej informacji na temat szczegółowego alfabetu fonetycznego usługi Mowa, zobacz [zestawy fonetyczne usługi mowa](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Dostosowywanie prosody

Element służy do określania zmian w schyłce, nachylieniu, zakresie, częstotliwości, czasie trwania i głośności danych wyjściowych funkcji `prosody` tekstu na mowę. Element `prosody` może zawierać tekst i następujące elementy: , , , , `audio` , , i `break` `p` `phoneme` `prosody` `say-as` `sub` `s` .

Ponieważ wartości atrybutów prosodycznych mogą się różnić w szerokim zakresie, aparat rozpoznawania mowy interpretuje przypisane wartości jako sugestię, jakie powinny być rzeczywiste wartości prosodyczne wybranego głosu. Usługa rozpoznawania tekstu na mowę ogranicza lub zastępuje wartości, które nie są obsługiwane. Przykłady nieobsługiwanych wartości to wysokość 1 MHz lub wolumin 120.

**Składnia**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `pitch` | Wskazuje linię bazową tekstu. Możesz wyrazić swoją opinię w następujący sposób:<ul><li>Wartość bezwzględna wyrażona jako liczba, po której następuje "Hz" (Hertz). Na przykład `<prosody pitch="600Hz">some text</prosody>`.</li><li>Wartość względna wyrażona jako liczba poprzedzona "+" lub "-" i po niej "Hz" lub "st", która określa ilość, która ma zmienić wysokość. Na przykład: `<prosody pitch="+80Hz">some text</prosody>` lub `<prosody pitch="-2st">some text</prosody>` . "st" wskazuje, że jednostka zmiany jest półtonem, czyli połową tonu (pół kroku) w standardowej skali diatonicznej.</li><li>Wartość stała:<ul><li>x-low</li><li>Niskie</li><li>średni</li><li>wysoka</li><li>x-high</li><li>default</li></ul></li></ul> | Opcjonalne |
| `contour` |Teraz System obsługuje zarówno neuronowe, jak i standardowe głosy. Kształt reprezentuje zmiany w schyłce. Te zmiany są reprezentowane jako tablica elementów docelowych w określonych pozycjach czasu w danych wyjściowych mowy. Każdy element docelowy jest definiowany przez zestawy par parametrów. Na przykład: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Pierwsza wartość w każdym zestawie parametrów określa lokalizację zmiany rzutu jako wartość procentową czasu trwania tekstu. Druga wartość określa ilość, która ma być podniesiena lub niższa, przy użyciu wartości względnej lub wartości wyliczenia dla wysokość (zobacz `pitch` ). | Opcjonalne |
| `range` | Wartość reprezentująca zakres nachylinia tekstu. Wyrażenia można wyrazić `range` przy użyciu tych samych wartości bezwzględnych, wartości względnych lub wartości wyliczenia, które są używane do opisywania wartości `pitch` . | Opcjonalne |
| `rate` | Wskazuje szybkość wypowiadania tekstu. Możesz wyrazić `rate` jako:<ul><li>Wartość względna wyrażona jako liczba, która działa jako mnożnik wartości domyślnej. Na przykład wartość *1* powoduje, że stawka nie zmienia się. Wartość *0,5 powoduje* zmniejszenie o połowę stawki. Wartość *3* powoduje tripling stawki.</li><li>Wartość stała:<ul><li>wolne x</li><li>Powolny</li><li>średni</li><li>szybki</li><li>x-fast</li><li>default</li></ul></li></ul> | Opcjonalne |
| `duration` | Okres, który powinien upłynąć, gdy usługa syntezy mowy (TTS) odczytuje tekst w sekundach lub milisekundach. Na przykład *2s* lub *1800ms*. Czas trwania obsługuje tylko standardowe głosy.| Opcjonalne |
| `volume` | Wskazuje poziom głośności wypowiadania głosu. Wolumin można wyrazić w następujący sposób:<ul><li>Wartość bezwzględna wyrażona jako liczba z zakresu od 0,0 do 100,0, od najspokojniejszego do *najgłębszego*.  Na przykład 75. Wartość domyślna to 100.0.</li><li>Wartość względna wyrażona jako liczba poprzedzona przez "+" lub "-", która określa ilość do zmiany woluminu. Na przykład +10 lub -5,5.</li><li>Wartość stała:<ul><li>Cichy</li><li>x-soft</li><li>Miękkie</li><li>średni</li><li>Głośno</li><li>x-loud</li><li>default</li></ul></li></ul> | Opcjonalne |

### <a name="change-speaking-rate"></a>Zmiana szybkości mówienia

Szybkość mówienia można zastosować do neuronowych i standardowych głosów na poziomie słowa lub zdania.

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

### <a name="change-volume"></a>Zmienianie woluminu

Zmiany głośności można zastosować do standardowych głosów na poziomie słowa lub zdania. Zmiany głośności można stosować tylko do neuronowych głosów na poziomie zdania.

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Zmiana wysokość

Zmiany wysokość można zastosować do standardowych głosów na poziomie słowa lub zdania. Natomiast zmiany wysokości mogą być stosowane tylko do neuronowych głosów na poziomie zdania.

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Zmiana zachwyt

> [!IMPORTANT]
> Zmiany w pochylić są teraz obsługiwane przy użyciu głosów neuronowych.

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
## <a name="say-as-element"></a>say-as, element

`say-as` jest opcjonalnym elementem, który wskazuje typ zawartości (taki jak liczba lub data) tekstu elementu. Zapewnia to wskazówki dla aparatu syntezy mowy dotyczące sposobu wymawiania tekstu.

**Składnia**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/Opcjonalne |
|-----------|-------------|---------------------|
| `interpret-as` | Wskazuje typ zawartości tekstu elementu. Aby uzyskać listę typów, zobacz tabelę poniżej. | Wymagane |
| `format` | Zawiera dodatkowe informacje o dokładnym formatowaniu tekstu elementu dla typów zawartości, które mogą mieć niejednoznaczne formaty. SSML definiuje formaty dla typów zawartości, które ich używają (zobacz tabelę poniżej). | Opcjonalne |
| `detail` | Wskazuje poziom szczegółowości, które mają być wypowiadane. Na przykład ten atrybut może zażądać, aby aparat syntezy mowy wymawiał znaki interpunkcje. Nie ma żadnych standardowych wartości zdefiniowanych dla `detail` . | Opcjonalne |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Poniżej przedstawiono obsługiwane typy zawartości dla `interpret-as` atrybutów `format` i . Uwzględnij `format` atrybut tylko `interpret-as` wtedy, gdy ustawiono wartość data i godzina.

| interpret-as | format | Interpretacja |
|--------------|--------|----------------|
| `address` | | Tekst jest wypowiadany jako adres. Aparat syntezy mowy wymawia:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Jako "Jestem w 150. sądzie północno-wschodnim Redmond w stanie Waszyngton". |
| `cardinal`, `number` | | Tekst jest wypowiadany jako liczba kardynacyjna. Aparat syntezy mowy wymawia:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Jako "Istnieją trzy alternatywy". |
| `characters`, `spell-out` | | Tekst jest wypowiadany jako pojedyncze litery (pisownia). Aparat syntezy mowy wymawia:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Jako "T E S T." |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | Tekst jest wypowiadany jako data. Atrybut `format` określa format daty *(d=dzień, m=miesiąc i y=rok).* Aparat syntezy mowy mówi:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Jako "Today is October 2019th two thousand sixteen" (Dzisiaj jest październikiem 2016 r.) |
| `digits`, `number_digit` | | Tekst jest wypowiadany jako sekwencja poszczególnych cyfr. Aparat syntezy mowy mówi:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Jako "1 2 3 4 5 6 7 8 9". |
| `fraction` | | Tekst jest wypowiadany jako liczba ułamkowa. Aparat syntezy mowy mówi:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Jako "trzy ósemki cala". |
| `ordinal` | | Tekst jest wypowiadany jako liczba porządkowa. Aparat syntezy mowy mówi:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Jako "Wybierz trzecią opcję". |
| `telephone` | | Tekst jest wypowiadany jako numer telefonu. Atrybut `format` może zawierać cyfry reprezentujące kod kraju. Na przykład "1" dla Stany Zjednoczone lub "39" dla Włoch. Aparat syntezy mowy może używać tych informacji do nakierowania wymowy numeru telefonu. Numer telefonu może również zawierać kod kraju, a jeśli tak, ma pierwszeństwo przed kodem kraju w `format` . Aparat syntezy mowy mówi:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Jako "My number is area code eight eight eight five five one two one two". |
| `time` | hms12, hms24 | Tekst jest wypowiadany jako czas. Atrybut określa, czy godzina jest określona przy użyciu `format` zegara 12-godzinnego (hms12) lub zegara 24-godzinnego (hms24). Użyj dwukropka, aby oddzielić liczby reprezentujące godziny, minuty i sekundy. Poniżej przedstawiono przykłady prawidłowego czasu: 12:35, 1:14:32, 08:15 i 02:50:45. Aparat syntezy mowy wymawia:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Jako "Pociąg odjeżdża o 4:00" |

**Użycie**

Element `say-as` może zawierać tylko tekst.

**Przykład**

Aparat syntezy mowy mówi następujący przykład: "Twoje pierwsze żądanie było dla jednego pokoju w dniu 200 października z wczesnych przylotów o 1205 pm".

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Dodawanie zarejestrowanego dźwięku

`audio` jest opcjonalnym elementem, który umożliwia wstawianie dźwięku MP3 do dokumentu SSML. Treść elementu audio może zawierać zwykły tekst lub znacznik SSML, który jest wypowiadany, jeśli plik dźwiękowy jest niedostępny lub nie można go odtwarzać. Ponadto element może `audio` zawierać tekst i następujące elementy: , , , , , `audio` , , i `break` `p` `s` `phoneme` `prosody` `say-as` `sub` .

Każdy dźwięk zawarty w dokumencie SSML musi spełniać następujące wymagania:

* Plik MP3 musi być hostowany w punkcie końcowym HTTPS dostępnym z Internetu. Wymagany jest protokół HTTPS, a domena hostowania pliku MP3 musi przedstawiać prawidłowy, zaufany certyfikat protokołu TLS/SSL.
* Mp3 musi być prawidłowym plikiem MP3 (MPEG v2).
* Szybkość transmisji bitów musi być 48 KB/s.
* Częstotliwość próbkowania musi być 16 000 Hz.
* Łączny łączny czas dla wszystkich plików tekstowych i dźwiękowych w jednej odpowiedzi nie może przekraczać dziewięćdziesiąt (90) sekund.
* Plik MP3 nie może zawierać żadnych informacji poufnych lub specyficznych dla klienta.

**Składnia**

```xml
<audio src="string"/></audio>
```

**Atrybuty**

| Atrybut | Opis                                   | Wymagane/opcjonalne                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Określa lokalizację/adres URL pliku dźwiękowego. | Wymagane w przypadku korzystania z elementu audio w dokumencie SSML. |

**Przykład**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
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

Element umożliwia dodawanie dźwięku w tle do dokumentów SSML (lub mieszanie pliku dźwiękowego z tekstem `mstts:backgroundaudio` na mowę). Za pomocą funkcji można zapętlić plik dźwiękowy w tle, zanikać na początku tekstu na mowę i zanikać na końcu tekstu `mstts:backgroundaudio` na mowę.

Jeśli podany dźwięk w tle jest krótszy niż tekst na mowę lub zanikanie, zostanie zapętleny. Jeśli jest on dłuższy niż tekst na mowę, zostanie zatrzymany po zakończeniu zanikanie.

Na dokument SSML dozwolony jest tylko jeden plik dźwiękowy w tle. Można jednak przeplatać tagi w elemencie , aby `audio` dodać dodatkowy dźwięk do dokumentu `voice` SSML.

**Składnia**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atrybuty**

| Atrybut | Opis | Wymagane/Opcjonalne |
|-----------|-------------|---------------------|
| `src` | Określa lokalizację/adres URL pliku dźwiękowego w tle. | Wymagane w przypadku korzystania z dźwięku w tle w dokumencie SSML. |
| `volume` | Określa wolumin pliku audio w tle. **Akceptowane wartości:** `0` na wartość `100` włącznie. Wartość domyślna to `1`. | Opcjonalne |
| `fadein` | Określa czas trwania "zanikanie" dźwięku w tle jako milisekund. Wartość domyślna to `0` , która jest odpowiednikiem nie zanikanie. **Akceptowane wartości:** `0` na wartość `10000` włącznie.  | Opcjonalne |
| `fadeout` | Określa czas trwania dźwięku w tle zanika w milisekundach. Wartość domyślna to `0` , która jest odpowiednikiem nie zanikanie. **Akceptowane wartości:** `0` na wartość `10000` włącznie.  | Opcjonalne |

**Przykład**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JennyNeural)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="bookmark-element"></a>Bookmark, element

Element bookmark umożliwia wstawianie niestandardowych znaczników w programie SSML w celu uzyskania przesunięcia każdego znacznika w strumieniu audio.
Nie będziemy odczytywać elementów zakładki.
Element bookmark może służyć do odwołania się do określonej lokalizacji w sekwencji tekstu lub tagu.

> [!NOTE]
> `bookmark` Element na razie działa `en-US-AriaNeural` tylko dla głosu.

**Składnia**

```xml
<bookmark mark="string"/>
```

**Atrybuty**

| Atrybut | Opis                                   | Wymagane/opcjonalne                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
|  `mark`   | Określa tekst odwołania `bookmark` elementu. | Wymagane. |

**Przykład**

Na przykład warto znać przesunięcie czasu każdego wyrazu kwiatu w następujący sposób:

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        We are selling <bookmark mark='flower_1'/>roses and <bookmark mark='flower_2'/>daisies.
    </voice>
</speak>
```

### <a name="get-bookmark-using-speech-sdk"></a>Uzyskiwanie zakładki przy użyciu zestawu SDK usługi Mowa

Możesz zasubskrybować `BookmarkReached` zdarzenie w zestawie SDK usługi Mowa, aby uzyskać przesunięcia zakładek.

> [!NOTE]
> `BookmarkReached` Zdarzenie jest dostępne tylko od wersji 1.16.0 zestawu Speech SDK.

`BookmarkReached` Zdarzenia są wywoływane, gdy dane wyjściowe audio stają się dostępne, co będzie szybsze niż odtwarzanie na urządzeniu wyjściowym.

* `AudioOffset` raportuje czas, który upłynął w wyjściowym dźwięku między początkiem syntezy a elementem zakładki. Jest to mierzone w jednostkach stu nanosekundowych (HNS) z 10 000 HNS odpowiednikiem 1 milisekundy.
* `Text` to tekst odwołania elementu zakładki, który jest ciągiem ustawionym w `mark` atrybutze .

# <a name="c"></a>[C#](#tab/csharp)

Aby uzyskać więcej <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkreached" target="_blank"> `BookmarkReached` </a>informacji, zobacz .

```csharp
synthesizer.BookmarkReached += (s, e) =>
{
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    Console.WriteLine($"Bookmark reached. Audio offset: " +
        $"{e.AudioOffset / 10000}ms, bookmark text: {e.Text}.");
};
```

W powyższym przykładzie SSML zdarzenie `BookmarkReached` zostanie wyzwolone dwa razy, a dane wyjściowe konsoli będą
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="c"></a>[C++](#tab/cpp)

Aby uzyskać więcej <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#bookmarkreached" target="_blank"> `BookmarkReached` </a>informacji, zobacz .

```cpp
synthesizer->BookmarkReached += [](const SpeechSynthesisBookmarkEventArgs& e)
{
    cout << "Bookmark reached. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "bookmark text: " << e.Text << "." << endl;
};
```

W powyższym przykładzie SSML zdarzenie `BookmarkReached` zostanie wyzwolone dwa razy, a dane wyjściowe konsoli będą
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="java"></a>[Java](#tab/java)

Aby uzyskać więcej <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkReached#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_BookmarkReached" target="_blank"> `BookmarkReached` </a>informacji, zobacz .

```java
synthesizer.BookmarkReached.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Bookmark reached. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("bookmark text: " + e.getText() + ".");
});
```

W powyższym przykładzie SSML zdarzenie `BookmarkReached` zostanie wyzwolone dwa razy, a dane wyjściowe konsoli będą
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="python"></a>[Python](#tab/python)

Aby uzyskać więcej <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#bookmark-reached" target="_blank"> `bookmark_reached` </a>informacji, zobacz .

```python
# The unit of evt.audio_offset is tick (1 tick = 100 nanoseconds), divide it by 10,000 to convert to milliseconds.
speech_synthesizer.bookmark_reached.connect(lambda evt: print(
    "Bookmark reached: {}, audio offset: {}ms, bookmark text: {}.".format(evt, evt.audio_offset / 10000, evt.text)))
```

W powyższym przykładzie SSML zdarzenie `bookmark_reached` zostanie wyzwolone dwa razy, a dane wyjściowe konsoli będą
```text
Bookmark reached, audio offset: 825ms, bookmark text: flower_1.
Bookmark reached, audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aby uzyskać więcej <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#bookmarkReached" target="_blank"> `bookmarkReached` </a>informacji, zobacz .

```javascript
synthesizer.bookmarkReached = function (s, e) {
    window.console.log("(Bookmark reached), Audio offset: " + e.audioOffset / 10000 + "ms, bookmark text: " + e.text);
}
```

W powyższym przykładzie SSML zdarzenie `bookmarkReached` zostanie wyzwolone dwa razy, a dane wyjściowe konsoli będą
```text
(Bookmark reached), Audio offset: 825ms, bookmark text: flower_1.
(Bookmark reached), Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Aby uzyskać więcej <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>informacji, zobacz .

```objectivec
[synthesizer addBookmarkReachedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisBookmarkEventArgs *eventArgs) {
    // The unit of AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to converted to milliseconds.
    NSLog(@"Bookmark reached. Audio offset: %fms, bookmark text: %@.", eventArgs.audioOffset/10000., eventArgs.text);
}];
```

W powyższym przykładzie SSML zdarzenie `BookmarkReached` zostanie wyzwolone dwa razy, a dane wyjściowe konsoli będą
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="swift"></a>[Swift](#tab/swift)

Aby uzyskać więcej <a href="/objectivec/cognitive-services/speech/spxspeechsynthesizer" target="_blank"> `addBookmarkReachedEventHandler` </a>informacji, zobacz .

---

## <a name="next-steps"></a>Następne kroki

* [Obsługa języków: głosy, ustawienia lokalne, języki](language-support.md)
