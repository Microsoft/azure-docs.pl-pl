---
title: Translator Translate, metoda
titleSuffix: Azure Cognitive Services
description: Poznaj parametry, nagłówki i komunikaty treści dla metody Translate Azure Cognitive Services Translator do tłumaczenia tekstu.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 7c92fb7f5de5542af08d1335bea745557a330f0d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107506010"
---
# <a name="translator-30-translate"></a>Translator 3.0: Tłumaczenie

Tłumaczy tekst.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie do:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przekazane w ciągu zapytania to:

### <a name="required-parameters"></a>Wymagane parametry

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>api-version</td>
    <td><em>Wymagany parametr</em>.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi być <code>3.0</code> .</td>
  </tr>
  <tr>
    <td>na wartość</td>
    <td><em>Wymagany parametr</em>.<br/>Określa język tekstu wyjściowego. Język docelowy musi być jednym z <a href="./v3-0-languages.md">obsługiwanych języków zawartych</a> w <code>translation</code> zakresie. Na przykład użyj funkcji <code>to=de</code> , aby przetłumaczyć na język niemiecki.<br/>Istnieje możliwość jednoczesnego tłumaczenia na wiele języków, powtarzając parametr w ciągu zapytania. Na przykład użyj funkcji , <code>to=de&to=it</code> aby przetłumaczyć na język niemiecki i włoski.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Parametry opcjonalne

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>z </td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa język tekstu wejściowego. Sprawdź, z których języków można się tłumaczyć, szukając <a href="./v3-0-languages.md">obsługiwanych języków przy</a> użyciu <code>translation</code> zakresu. Jeśli parametr nie zostanie określony, zostanie zastosowane automatyczne wykrywanie języka <code>from</code> w celu określenia języka źródłowego. <br/><br/>Podczas korzystania z funkcji słownika dynamicznego należy użyć parametru zamiast <code>from</code> autowysekcji. <a href="/azure/cognitive-services/translator/dynamic-dictionary"></a></td>
  </tr>  
  <tr>
    <td>textType</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa, czy tłumaczony tekst jest zwykły, czy HTML. Każdy kod HTML musi być dobrze uformowanym, kompletnym elementem. Możliwe wartości to: <code>plain</code> (wartość domyślna) lub <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Opcjonalny parametr</em>.<br/>Ciąg określający kategorię (domenę) tłumaczenia. Ten parametr służy do uzyskania tłumaczeń z dostosowanego systemu sbudowaną za <a href="../customization.md">pomocą Custom Translator</a>. Dodaj identyfikator kategorii ze szczegółów Custom Translator <a href="/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projektu</a> do tego parametru, aby użyć wdrożonego dostosowanego systemu. Wartość domyślna to: <code>general</code> .</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa sposób, w jaki w tłumaczeniach mają być traktowane wulgaryzmy. Możliwe wartości to: <code>NoAction</code> (wartość domyślna) <code>Marked</code> lub <code>Deleted</code> . Aby zrozumieć sposoby traktowania wulgaryzmów, zobacz <a href="#handle-profanity">Obsługa wulgaryzmów</a>.</td>
  </tr>
  <tr>
    <td>wulgaryzmyMarker</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa sposób oznaczania wulgaryzmów w tłumaczeniach. Możliwe wartości to: <code>Asterisk</code> (wartość domyślna) lub <code>Tag</code> . Aby zrozumieć sposoby traktowania wulgaryzmów, zobacz <a href="#handle-profanity">Obsługa wulgaryzmów.</a></td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa, czy należy uwzględnić projekcję wyrównania z tekstu źródłowego na tekst przetłumaczony. Możliwe wartości to: <code>true</code> lub <code>false</code> (wartość domyślna). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa, czy należy uwzględnić granice zdań dla tekstu wejściowego i przetłumaczonego tekstu. Możliwe wartości to: <code>true</code> lub <code>false</code> (wartość domyślna).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa język rezerwowy, jeśli nie można zidentyfikować języka tekstu wejściowego. Automatyczne wykrywanie języka jest stosowane, gdy <code>from</code> parametr zostanie pominięty. Jeśli wykrywanie zakończy się niepowodzeniem, <code>suggestedFrom</code> zostanie przyjęto język.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa skrypt tekstu wejściowego.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa skrypt przetłumaczonego tekstu.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa, że usługa może tworzyć powroty do systemu ogólnego, gdy system niestandardowy nie istnieje. Możliwe wartości to: <code>true</code> (wartość domyślna) lub <code>false</code> .<br/><br/><code>allowFallback=false</code> określa, że tłumaczenie powinno używać tylko systemów przeszkolonych <code>category</code> dla określonego przez żądanie. Jeśli tłumaczenie języka X na język Y wymaga połączenia za pośrednictwem języka przestawnego E, wszystkie systemy w łańcuchu (X->E i E->Y) muszą być niestandardowe i mieć tę samą kategorię. Jeśli żaden system nie zostanie znaleziony z określoną kategorią, żądanie zwróci kod stanu 400. <code>allowFallback=true</code> określa, że usługa może tworzyć powroty do systemu ogólnego, gdy system niestandardowy nie istnieje.
</td>
  </tr>
</table> 

Nagłówki żądań obejmują:

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>Nagłówki uwierzytelniania</td>
    <td><em>Wymagany nagłówek żądania</em>.<br/>Zobacz <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Wymagany nagłówek żądania</em>.<br/>Określa typ zawartości ładunku.<br/> Akceptowana wartość to <code>application/json; charset=UTF-8</code> .</td>
  </tr>
  <tr>
    <td>Długość zawartości</td>
    <td><em>Wymagany nagłówek żądania</em>.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Opcjonalny element</em>.<br/>Identyfikator GUID wygenerowany przez klienta w celu unikatowego zidentyfikowania żądania. Możesz pominąć ten nagłówek, jeśli uwzględnisz identyfikator śledzenia w ciągu zapytania przy użyciu parametru zapytania o nazwie <code>ClientTraceId</code> .</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON z właściwością ciągu o nazwie , która `Text` reprezentuje ciąg do przetłumaczenia.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Obowiązują następujące ograniczenia:

* Tablica może mieć najwyżej 100 elementów.
* Cały tekst zawarty w żądaniu nie może przekraczać 10 000 znaków, w tym spacji.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź to tablica JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `detectedLanguage`: obiekt opisujący wykryty język za pomocą następujących właściwości:

      * `language`: ciąg reprezentujący kod wykrytego języka.

      * `score`: wartość zmiennoprzecinkowa wskazująca pewność wyniku. Wynik jest od zera do jednego, a niski wynik oznacza niską pewność.

    Właściwość `detectedLanguage` jest obecna w obiekcie wynikowym tylko wtedy, gdy jest wymagane automatyczne wykrywanie języka.

  * `translations`: tablica wyników tłumaczenia. Rozmiar tablicy odpowiada liczbie języków docelowych określonej za pomocą `to` parametru zapytania. Każdy element w tablicy zawiera:

    * `to`: ciąg reprezentujący kod języka języka docelowego.

    * `text`: ciąg zawierający przetłumaczony tekst.

    * `transliteration`: obiekt podający przetłumaczony tekst w skrypcie określonym przez `toScript` parametr .

      * `script`: ciąg określający skrypt docelowy.   

      * `text`: ciąg dający przetłumaczony tekst w skrypcie docelowym.

    Obiekt `transliteration` nie jest uwzględniany, jeśli transliteracja nie ma miejsca.

    * `alignment`: obiekt z pojedynczą właściwością ciągu o nazwie `proj` , która mapuje tekst wejściowy na przetłumaczony tekst. Informacje o wyrównaniu są udostępniane tylko wtedy, gdy parametr żądania `includeAlignment` to `true` . Wyrównanie jest zwracane jako wartość ciągu w następującym formacie: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]` .  Dwukropek oddziela indeks początek i koniec, kreska oddziela języki, a spacja oddziela wyrazy. Jeden wyraz może być wyrównany do zera, jeden lub wiele wyrazów w innym języku, a wyrównane słowa mogą być niesączne. Jeśli żadne informacje o wyrównaniu nie są dostępne, element wyrównania będzie pusty. Zobacz [temat Obtain alignment information (Uzyskiwanie](#obtain-alignment-information) informacji o wyrównaniu), aby uzyskać przykład i ograniczenia.

    * `sentLen`: obiekt zwracający granice zdań w tekstach wejściowych i wyjściowych.

      * `srcSentLen`: tablica liczb całkowitych reprezentująca długości zdań w tekście wejściowym. Długość tablicy to liczba zdań, a wartości to długość każdego zdania.

      * `transSentLen`: tablica liczb całkowitych reprezentująca długości zdań w przetłumaczonym tekście. Długość tablicy to liczba zdań, a wartości to długość każdego zdania.

    Granice zdań są uwzględniane tylko wtedy, gdy parametr żądania `includeSentenceLength` to `true` .

  * `sourceText`: obiekt z pojedynczą właściwością ciągu o nazwie , który udostępnia tekst `text` wejściowy w domyślnym skrypcie języka źródłowego. `sourceText` Właściwość jest obecna tylko wtedy, gdy dane wejściowe są wyrażone w skrypcie, który nie jest zwykłym skryptem języka. Jeśli na przykład dane wejściowe zostały napisane w języku arabskim w alfabetze łacińskim, oznaczałoby to, że ten sam tekst arabski `sourceText.text` zostałby przekonwertowany na skrypt Pisany.

Przykładowe odpowiedzi JSON znajdują się w [sekcji przykładów.](#examples)

## <a name="response-headers"></a>Nagłówki odpowiedzi

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
    <tr>
    <td>X-RequestId</td>
    <td>Wartość wygenerowana przez usługę w celu zidentyfikowania żądania. Służy do rozwiązywania problemów.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>Określa typ systemu, który był używany do tłumaczenia dla każdego języka "na" żądanego tłumaczenia. Wartość jest rozdzielaną przecinkami listą ciągów. Każdy ciąg wskazuje typ:<br/><ul><li>Niestandardowy — żądanie zawiera system niestandardowy i co najmniej jeden system niestandardowy został użyty podczas tłumaczenia.</li><li>Zespół — wszystkie inne żądania</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Poniżej przedstawiono możliwe kody stanu HTTP zwracane przez żądanie. 

<table width="100%">
  <th width="20%">Kod stanu</th>
  <th>Opis</th>
  <tr>
    <td>200</td>
    <td>Powodzenie.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Brakuje jednego z parametrów zapytania lub jest on nieprawidłowy. Popraw parametry żądania przed ponowieniem próby.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nie można uwierzytelnić żądania. Sprawdź, czy poświadczenia są określone i prawidłowe.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Żądanie nie jest autoryzowane. Sprawdź komunikat o błędzie szczegóły. Często oznacza to, że zostały użyte wszystkie bezpłatne tłumaczenia z subskrypcją wersji próbnej.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Nie można spełnić żądania, ponieważ brakuje zasobu. Sprawdź komunikat o błędzie szczegóły. W przypadku korzystania z niestandardowego systemu często oznacza to, że niestandardowy system tłumaczenia nie jest jeszcze <code>category</code> dostępny do obsługi żądań. Żądanie należy ponoć po upływie okresu oczekiwania (np. 1 minuta).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Serwer odrzucił żądanie, ponieważ klient przekroczył limity żądań.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś go z: datą i godziną błędu, identyfikatorem żądania z nagłówka odpowiedzi i identyfikatorem klienta <code>X-RequestId</code> z nagłówka żądania <code>X-ClientTraceId</code> .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer jest tymczasowo niedostępny. Ponów próbę żądania. Jeśli błąd będzie się powtarzać, zgłoś go z: datą i godziną błędu, identyfikatorem żądania z nagłówka odpowiedzi i identyfikatorem klienta <code>X-RequestId</code> z nagłówka żądania <code>X-ClientTraceId</code> .</td>
  </tr>
</table> 

Jeśli wystąpi błąd, żądanie zwróci również odpowiedź z błędem JSON. Kod błędu to 6-cyfrowy numer, który łączy 3-cyfrowy kod stanu HTTP, po którym następuje 3-cyfrowy numer w celu dalszej kategoryzacji błędu. Typowe kody błędów można znaleźć na stronie referencyjnej usługi [Translator w wersji 3.](./v3-0-reference.md#errors) 

## <a name="examples"></a>Przykłady

### <a name="translate-a-single-input"></a>Tłumaczenie pojedynczych danych wejściowych

W tym przykładzie pokazano, jak przetłumaczyć jedno zdanie z języka angielskiego na chiński uproszczony.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Treść odpowiedzi to:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Tablica zawiera jeden element, który zapewnia tłumaczenie pojedynczego `translations` fragmentu tekstu w danych wejściowych.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Tłumaczenie pojedynczych danych wejściowych przy użyciu automatycznego wykrywania języka

W tym przykładzie pokazano, jak przetłumaczyć jedno zdanie z języka angielskiego na chiński uproszczony. Żądanie nie określa języka wejściowego. Zamiast tego jest używane automatyczne wykrywanie języka źródłowego.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Treść odpowiedzi to:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
Odpowiedź jest podobna do odpowiedzi z poprzedniego przykładu. Ponieważ zażądano automatycznego wykrywania języka, odpowiedź zawiera również informacje o języku wykrytym dla tekstu wejściowego. Automatyczne wykrywanie języka działa lepiej w przypadku dłuższego tekstu wejściowego.

### <a name="translate-with-transliteration"></a>Tłumaczenie za pomocą transliteracji

Rozszerzmy poprzedni przykład, dodając transliterację. Następujące żądanie prosi o tłumaczenie w języku chińskim napisanego w alfabetze łacińskim.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Treść odpowiedzi jest:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Wynik tłumaczenia zawiera teraz właściwość , która umożliwia tłumaczenie tekstu przy `transliteration` użyciu znaków łacińskich.

### <a name="translate-multiple-pieces-of-text"></a>Tłumaczenie wielu fragmentów tekstu

Tłumaczenie wielu ciągów jednocześnie jest po prostu kwestią określenia tablicy ciągów w treści żądania.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Odpowiedź zawiera tłumaczenie wszystkich fragmentów tekstu w dokładnie takiej samej kolejności jak w żądaniu.
Treść odpowiedzi to:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Tłumaczenie na wiele języków

W tym przykładzie pokazano, jak przetłumaczyć te same dane wejściowe na kilka języków w jednym żądaniu.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Treść odpowiedzi jest:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Obsługa wulgaryzmów

Zwykle usługa Translator zachowuje wulgaryzmy, które są obecne w źródle w tłumaczeniu. Stopień wulgaryzmów i kontekst, który sprawia, że słowa są wulgarne, różnią się między kulturami i w związku z tym stopień wulgaryzmów w języku docelowym może być wzmacniany lub zmniejszany.

Jeśli chcesz uniknąć wulgaryzmów w tłumaczeniu, niezależnie od obecności wulgaryzmów w tekście źródłowym, możesz użyć opcji filtrowania wulgaryzmów. Ta opcja umożliwia wybranie, czy mają zostać usunięte wulgaryzmy, czy mają być oznaczane wulgaryzmy odpowiednimi tagami (co daje możliwość dodania własnego przetwarzania po przetwarzaniu), czy nie chcesz, aby nie było podejmowane żadne działania. Akceptowane wartości to `ProfanityAction` `Deleted` , i `Marked` `NoAction` (wartość domyślna).

<table width="100%">
  <th width="20%">WulgaryzmyAkcja</th>
  <th>Akcja</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Jest to zachowanie domyślne. Wulgaryzmy będą przekazywać dane ze źródła do obiektu docelowego.<br/><br/>
    <strong>Przykładowe źródło (japoński)</strong>: 彼ャkazykaでpozytywu<br/>
    <strong>Przykładowe tłumaczenie (angielski):</strong>jest jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Wulgarne słowa zostaną usunięte z danych wyjściowych bez zastępowania.<br/><br/>
    <strong>Przykładowe źródło (japoński)</strong>: 彼ャkazykaでpozytywu<br/>
    <strong>Przykładowe tłumaczenie (angielski):</strong>jest a.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Wulgarne słowa są zastępowane znacznikiem w danych wyjściowych. Znacznik zależy od <code>ProfanityMarker</code> parametru .<br/><br/>
W <code>ProfanityMarker=Asterisk</code> przypadku wyrazów wulgarne słowa są zastępowane przez <code>***</code> :<br/>
    <strong>Przykładowe źródło (japoński)</strong>: 彼ャkazykaでpozytywu<br/>
    <strong>Przykładowe tłumaczenie (angielski):</strong>jest \* \* \* .<br/><br/>
W <code>ProfanityMarker=Tag</code> przypadku elementu słowa wulgarne są otoczone wulgaryzmami tagów XML i &lt; &gt; &lt; /wulgaryzmami &gt; :<br/>
    <strong>Przykładowe źródło (japoński)</strong>: 彼ャkazykaでpozytywu<br/>
    <strong>Example Translation (english)</strong>: He is a &lt; profanity &gt; jackass &lt; /profanity &gt; .
  </tr>
</table> 

Na przykład:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
To polecenie zwraca następującą treść:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Porównaj z:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

To ostatnie żądanie zwraca:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Tłumaczenie zawartości za pomocą znaczników i decydowanie o tym, co jest tłumaczone

Często tłumaczy się zawartość, która zawiera znaczniki, takie jak zawartość ze strony HTML lub zawartość z dokumentu XML. Uwzględnij parametr zapytania `textType=html` podczas tłumaczenia zawartości za pomocą tagów. Ponadto czasami przydaje się wykluczanie określonej zawartości z tłumaczenia. Atrybutu można użyć do `class=notranslate` określenia zawartości, która powinna pozostać w jej oryginalnym języku. W poniższym przykładzie zawartość wewnątrz pierwszego elementu nie zostanie przetłumaczona, a zawartość w drugim `div` `div` elemencie zostanie przetłumaczona.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Oto przykładowe żądanie ilustrujące.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Odpowiedź jest:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Uzyskiwanie informacji o wyrównaniu

Wyrównanie jest zwracane jako wartość ciągu w następującym formacie dla każdego wyrazu źródła. Informacje dotyczące każdego wyrazu są oddzielone spacjami, w tym w przypadku języków (skryptów) rozdzielonych spacjami, takich jak chiński:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Przykładowy ciąg wyrównania: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Innymi słowy dwukropek oddziela indeks startowy i końcowy, kreska oddziela języki, a spacja oddziela wyrazy. Jeden wyraz może być wyrównany do zera, jeden lub wiele wyrazów w innym języku, a wyrównane wyrazy mogą być niesączne. Jeśli żadne informacje o wyrównaniu nie są dostępne, element wyrównania będzie pusty. W takim przypadku metoda nie zwraca żadnego błędu.

Aby uzyskać informacje o wyrównaniu, `includeAlignment=true` określ wartość w ciągu zapytania.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true&quot; -H &quot;Ocp-Apim-Subscription-Key: <client-secret>&quot; -H &quot;Content-Type: application/json; charset=UTF-8&quot; -d &quot;[{'Text':'The answer lies in machine translation.'}]"
```

Odpowiedź jest:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Informacje o wyrównaniu zaczynają się od , co oznacza, że pierwsze trzy znaki w tekście źródłowym ( ) są mapowane na pierwsze dwa znaki w `0:2-0:1` `The` przetłumaczonym tekście ( `La` ).

#### <a name="limitations"></a>Ograniczenia
Uzyskiwanie informacji o wyrównaniu jest eksperymentalną funkcją, która umożliwia tworzenie prototypów badań i obsługę potencjalnych mapowań fraz. Możemy przestać wspierać tę opcję w przyszłości. Poniżej podano niektóre z owalnych ograniczeń, w przypadku których wyrównania nie są obsługiwane:

* Wyrównanie nie jest dostępne dla tekstu w formacie HTML, tj. textType=html
* Wyrównanie jest zwracane tylko dla podzbioru par języków:
  - Angielski do/z dowolnego innego języka z wyjątkiem języka chińskiego tradycyjnego, cantonskiego (tradycyjnego) lub serbskiego (cyrylica).
  - z japońskiego na koreański lub z koreańskiego do japońskiego.
  - z japońskiego na chiński uproszczony i chiński uproszczony na japoński. 
  - z chińskiego uproszczonego na chiński tradycyjny i chiński tradycyjny na chiński uproszczony. 
* Wyrównanie nie będzie odbierane, jeśli zdanie jest tłumaczeniem w stanie canned. Przykładowe tłumaczenie w canned to "This is a test", "I love you" and other high frequency sentences (To jest test), "I love you" (Lubię Cię) i inne zdania o wysokiej częstotliwości.
* Wyrównanie nie jest dostępne w przypadku zastosowania dowolnego podejścia, aby zapobiec translacji zgodnie z opisem w [tym miejscu](../prevent-translation.md)

### <a name="obtain-sentence-boundaries"></a>Uzyskiwanie granic zdań

Aby otrzymywać informacje o długości zdań w tekście źródłowym i przetłumaczonym tekście, określ `includeSentenceLength=true` ciąg w ciągu zapytania.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

Odpowiedź jest:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Tłumaczenie przy użyciu słownika dynamicznego

Jeśli znasz już tłumaczenie, które chcesz zastosować do wyrazu lub frazy, możesz podać je jako znacznik w żądaniu. Słownik dynamiczny jest bezpieczny tylko dla odpowiednich rzeczowników, takich jak nazwiska i nazwy produktów.

Do dostarczania znaczników jest używana następująca składnia.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Rozważmy na przykład angielskie zdanie "Wyraz wordomatic to wpis słownika". Aby zachować słowo _wordomatic_ w translacji, wyślij żądanie:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Wynik jest:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Ta funkcja działa w taki sam sposób w przypadku funkcji `textType=text` lub z . `textType=html` Ta funkcja powinna być używana oszczędnie. Odpowiednim i znacznie lepszym sposobem dostosowywania tłumaczenia jest użycie Custom Translator. Custom Translator w pełni wykorzystuje kontekst i prawdopodobieństwa statystyczne. Jeśli masz lub możesz pozwolić sobie na utworzenie danych szkoleniowych, które pokazują Twoją pracę lub frazę w kontekście, możesz uzyskać znacznie lepsze wyniki. [Dowiedz się więcej o Custom Translator](../customization.md).
