---
title: Metoda tłumaczenia translatora
titleSuffix: Azure Cognitive Services
description: Informacje o parametrach, nagłówkach i komunikatach treści dla metody tłumaczenia w usłudze Azure Cognitive Services translator w celu tłumaczenia tekstu.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: d46fef0159b983f2685be40e2a0ab5471b96883b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895445"
---
# <a name="translator-30-translate"></a>Translator 3,0: tłumaczenie

Tłumaczy tekst.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie do:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

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
    <td><em>Wymagany parametr</em>.<br/>Określa język tekstu wyjściowego. Język docelowy musi być jednym z <a href="./v3-0-languages.md">obsługiwanych języków</a> zawartych w <code>translation</code> zakresie. Na przykład użyj, <code>to=de</code> Aby przetłumaczyć na język niemiecki.<br/>Możliwe jest przetłumaczenie na wiele języków jednocześnie przez powtarzanie parametru w ciągu zapytania. Na przykład użyj, <code>to=de&to=it</code> Aby przetłumaczyć na język niemiecki i włoski.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Parametry opcjonalne

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>wniosek</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa język tekstu wejściowego. Znajdź Języki dostępne do przetłumaczenia, wyszukując <a href="./v3-0-languages.md">obsługiwane języki</a> przy użyciu <code>translation</code> zakresu. Jeśli <code>from</code> parametr nie jest określony, automatyczne wykrywanie języka jest stosowane w celu określenia języka źródłowego. <br/><br/>W <code>from</code> przypadku korzystania z funkcji <a href="/azure/cognitive-services/translator/dynamic-dictionary">słownika dynamicznego</a> należy użyć parametru zamiast automatycznego wykrywania.</td>
  </tr>  
  <tr>
    <td>texttype</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa, czy przetłumaczony tekst jest zwykłym tekstem czy tekstem HTML. Każdy kod HTML musi być poprawnie sformułowanym elementem kompletnym. Możliwe wartości to: <code>plain</code> (ustawienie domyślne) lub <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Opcjonalny parametr</em>.<br/>Ciąg określający kategorię (domenę) tłumaczenia. Ten parametr służy do uzyskiwania tłumaczeń z dostosowanego systemu utworzonego przy użyciu <a href="../customization.md">translatora niestandardowego</a>. Dodaj identyfikator kategorii z <a href="/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">szczegółów projektu</a> usługi tłumaczenia niestandardowego do tego parametru, aby użyć wdrożonego systemu. Wartość domyślna to: <code>general</code> .</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa, jak mają być traktowane w tłumaczeniach. Możliwe wartości to: <code>NoAction</code> (ustawienie domyślne) <code>Marked</code> lub <code>Deleted</code> . Aby zrozumieć sposoby traktowania Niemniej Niemniej, zobacz <a href="#handle-profanity">Obsługa Niewulgarności</a>.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa, jak w tłumaczeniach należy oznaczyć liczbę nietrwałych. Możliwe wartości to: <code>Asterisk</code> (ustawienie domyślne) lub <code>Tag</code> . Aby zrozumieć sposoby traktowania Niemniej Niemniej, zobacz <a href="#handle-profanity">Obsługa Niewulgarności</a>.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa, czy dołączać rzutowanie tekstu źródłowego do przetłumaczonego tekstu. Możliwe wartości to: <code>true</code> lub <code>false</code> (wartość domyślna). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa, czy mają być dołączane granice zdania dla tekstu wejściowego i przetłumaczonego tekstu. Możliwe wartości to: <code>true</code> lub <code>false</code> (wartość domyślna).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Opcjonalny parametr</em>.<br/>Określa język rezerwowy, jeśli nie można zidentyfikować języka tekstu wejściowego. Funkcja automatycznego wykrywania języka jest stosowana, gdy <code>from</code> parametr zostanie pominięty. Jeśli wykrywanie nie powiedzie się, <code>suggestedFrom</code> zostanie przyjęty język.</td>
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
    <td><em>Opcjonalny parametr</em>.<br/>Określa, że usługa może przewracać do ogólnego systemu, gdy nie istnieje system niestandardowy. Możliwe wartości to: <code>true</code> (ustawienie domyślne) lub <code>false</code> .<br/><br/><code>allowFallback=false</code> Określa, że tłumaczenie ma używać tylko systemów wyszkolonych dla <code>category</code> określonego przez żądanie. Jeśli tłumaczenie języka X na język t wymaga łańcucha za pośrednictwem języka Pivot E, wszystkie systemy w łańcuchu (X->E i E->Y) będą musiały być niestandardowe i mieć tę samą kategorię. Jeśli nie zostanie znaleziony żaden system z określoną kategorią, żądanie zwróci kod stanu 400. <code>allowFallback=true</code> Określa, że usługa może przewracać do ogólnego systemu, gdy nie istnieje system niestandardowy.
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
    <td><em>Wymagany nagłówek żądania</em>.<br/>Określa typ zawartości ładunku.<br/> Zaakceptowana wartość to <code>application/json; charset=UTF-8</code> .</td>
  </tr>
  <tr>
    <td>Długość zawartości</td>
    <td><em>Wymagany nagłówek żądania</em>.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Opcjonalne</em>.<br/>Wygenerowany przez klienta identyfikator GUID służący do unikatowej identyfikacji żądania. Możesz pominąć ten nagłówek, jeśli dołączysz identyfikator śledzenia w ciągu zapytania przy użyciu parametru zapytania o nazwie <code>ClientTraceId</code> .</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON z właściwością ciągu o nazwie `Text` , która reprezentuje ciąg do przetłumaczenia.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Obowiązują następujące ograniczenia:

* Tablica może zawierać maksymalnie 100 elementów.
* Cały tekst zawarty w żądaniu nie może zawierać więcej niż 10 000 znaków, w tym spacji.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź to tablica JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `detectedLanguage`: Obiekt opisujący wykryty język przez następujące właściwości:

      * `language`: Ciąg reprezentujący kod wykrytego języka.

      * `score`: Wartość zmiennoprzecinkowa wskazująca wiarygodność w wyniku. Wynik jest z przedziału od zera do jednego, a niski Wynik wskazuje na niski poziom pewności.

    `detectedLanguage`Właściwość jest obecna tylko w obiekcie wynikowym, gdy żąda się automatycznego wykrywania języka.

  * `translations`: Tablica wyników tłumaczenia. Rozmiar tablicy jest zgodny z liczbą języków docelowych określonych za pomocą `to` parametru zapytania. Każdy element w tablicy zawiera:

    * `to`: Ciąg reprezentujący kod języka dla języka docelowego.

    * `text`: Ciąg zawierający przetłumaczony tekst.

    * `transliteration`: Obiekt dający przetłumaczony tekst w skrypcie określonym przez `toScript` parametr.

      * `script`: Ciąg określający skrypt docelowy.   

      * `text`: Ciąg zawierający przetłumaczony tekst w skrypcie docelowym.

    `transliteration`Obiekt nie jest uwzględniany w przypadku, gdy nie odbywa się przerobić.

    * `alignment`: Obiekt z właściwością pojedynczego ciągu o nazwie `proj` , która mapuje tekst wejściowy na przetłumaczony tekst. Informacje o wyrównaniu są podawane tylko wtedy, gdy parametr request `includeAlignment` ma wartość `true` . Wyrównanie jest zwracane jako wartość ciągu w następującym formacie: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]` .  Dwukropek oddziela indeks początkowy i końcowy, myślnik oddziela Języki, a spacja oddziela słowa. Jedno słowo może być wyrównane z zerem, jednym lub wieloma wyrazami w innym języku, a wyrównane słowa mogą nie być ciągłe. Gdy żadne informacje o wyrównaniu nie są dostępne, element wyrównania będzie pusty. Zobacz [Uzyskiwanie informacji o wyrównaniu](#obtain-alignment-information) dla przykładu i ograniczeń.

    * `sentLen`: Obiekt zwracający granice zdania w tekstach wejściowych i wyjściowych.

      * `srcSentLen`: Tablica liczb całkowitych reprezentująca długości zdań w tekście wejściowym. Długość tablicy jest liczbą zdań, a wartości to długość każdego zdania.

      * `transSentLen`: Tablica liczb całkowitych reprezentująca długości zdań w przetłumaczonym tekście. Długość tablicy jest liczbą zdań, a wartości to długość każdego zdania.

    Granice zdania są uwzględniane tylko wtedy, gdy parametr request `includeSentenceLength` ma wartość `true` .

  * `sourceText`: Obiekt z właściwością pojedynczego ciągu o nazwie `text` , która daje tekst wejściowy w domyślnym skrypcie języka źródłowego. `sourceText` Właściwość jest obecna tylko wtedy, gdy dane wejściowe są wyrażane w skrypcie, który nie jest zwykłym skryptem dla danego języka. Na przykład jeśli dane wejściowe były zapisywane w języku łacińskim w skrypcie łaciński, to ten `sourceText.text` sam tekst arabski konwertowany do arabskiej skryptu.

Przykład odpowiedzi JSON znajduje się w sekcji [przykładów](#examples) .

## <a name="response-headers"></a>Nagłówki odpowiedzi

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
    <tr>
    <td>X-IdentyfikatorŻądania</td>
    <td>Wartość wygenerowana przez usługę w celu zidentyfikowania żądania. Służy do rozwiązywania problemów.</td>
  </tr>
  <tr>
    <td>X-MT-system</td>
    <td>Określa typ systemu, który został użyty do translacji dla każdego języka "do", który zażądał tłumaczenia. Wartość jest rozdzielaną przecinkami listą ciągów. Każdy ciąg wskazuje typ:<br/><ul><li>Żądanie niestandardowe zawiera system niestandardowy i co najmniej jeden niestandardowy system został użyty podczas tłumaczenia.</li><li>Zespół — wszystkie inne żądania</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Oto możliwe kody stanu HTTP zwracane przez żądanie. 

<table width="100%">
  <th width="20%">Kod stanu</th>
  <th>Opis</th>
  <tr>
    <td>200</td>
    <td>Powodzenie.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Brakuje jednego z parametrów zapytania lub jest on nieprawidłowy. Popraw parametry żądania przed ponowną próbą.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Nie można uwierzytelnić żądania. Sprawdź, czy poświadczenia są określone i prawidłowe.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Żądanie nie ma autoryzacji. Sprawdź komunikat o błędzie szczegóły. Często oznacza to, że używane są wszystkie bezpłatne tłumaczenia udostępnione w ramach wersji próbnej.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Nie można spełnić żądania, ponieważ brakuje zasobu. Sprawdź komunikat o błędzie szczegóły. W przypadku korzystania z niestandardowych <code>category</code> , często oznacza to, że niestandardowy system tłumaczenia nie jest jeszcze dostępny do obsłużynia żądań. Żądanie powinno być ponowione po okresie oczekiwania (np. 1 minuta).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Serwer odrzucił żądanie z powodu przekroczenia limitów żądań przez klienta.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś go z: Data i godzina błędu, identyfikator żądania z nagłówka odpowiedzi <code>X-RequestId</code> oraz identyfikator klienta z nagłówka żądania <code>X-ClientTraceId</code> .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, zgłoś go z: Data i godzina błędu, identyfikator żądania z nagłówka odpowiedzi <code>X-RequestId</code> oraz identyfikator klienta z nagłówka żądania <code>X-ClientTraceId</code> .</td>
  </tr>
</table> 

Jeśli wystąpi błąd, żądanie zwróci także odpowiedź na błąd JSON. Kod błędu to 6-cyfrowy numer łączący 3-cyfrowy kod stanu HTTP, a następnie 3-cyfrowy numer do dalszej kategoryzacji błędu. Typowe kody błędów można znaleźć na [stronie referencyjnej translatora v3](./v3-0-reference.md#errors). 

## <a name="examples"></a>Przykłady

### <a name="translate-a-single-input"></a>Tłumaczenie pojedynczego danych wejściowych

Ten przykład pokazuje, jak przetłumaczyć pojedyncze zdanie z języka angielskiego na chiński uproszczony.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Treść odpowiedzi:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations`Tablica zawiera jeden element, który zapewnia tłumaczenie pojedynczego fragmentu tekstu w danych wejściowych.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Przetłumacz pojedyncze dane wejściowe za pomocą automatycznego wykrywania języka

Ten przykład pokazuje, jak przetłumaczyć pojedyncze zdanie z języka angielskiego na chiński uproszczony. Żądanie nie określa języka wejściowego. Zamiast tego użyto automatycznego wykrywania języka źródłowego.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Treść odpowiedzi:

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
Odpowiedź jest podobna do odpowiedzi z poprzedniego przykładu. Ponieważ zażądano autowykrywania języka, odpowiedź zawiera również informacje dotyczące języka wykrytego dla tekstu wejściowego. Funkcja autowykrywania języka działa lepiej z dłuższym tekstem wejściowym.

### <a name="translate-with-transliteration"></a>Tłumaczenie przy użyciu Przekształć

Przyciągnijmy poprzedni przykład, dodając translitrę. Poniższe żądanie prosi o przekształcenie w języku chińskim zapisaną w skrypcie łacińskim.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Treść odpowiedzi:

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

Wyniki tłumaczenia zawierają teraz `transliteration` Właściwość, która zwraca przetłumaczony tekst, używając znaków łacińskich.

### <a name="translate-multiple-pieces-of-text"></a>Tłumaczenie wielu fragmentów tekstu

Tłumaczenie wielu ciągów jednocześnie jest kwestią określenia tablicy ciągów w treści żądania.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Odpowiedź zawiera tłumaczenie wszystkich fragmentów tekstu w dokładnie takiej samej kolejności jak w żądaniu.
Treść odpowiedzi:

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

Ten przykład pokazuje, jak przetłumaczyć te same dane wejściowe do kilku języków w jednym żądaniu.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Treść odpowiedzi:

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

### <a name="handle-profanity"></a>Obsługa niewulgarności

Zwykle usługa translator zachowa nieobecność, która jest obecna w źródle w tłumaczeniu. Stopień niepewności i kontekst, który sprawia, że wyrazy nie różnią się między kulturami, a w efekcie stopień niewystarczającego poziomu braków w języku docelowym może być wzmacniany lub zmniejszany.

Jeśli chcesz uniknąć niewulgarności w tłumaczeniu, niezależnie od obecności w tekście źródłowym, możesz użyć opcji filtrowania niepotrzebne. Opcja pozwala wybrać, czy ma być wyświetlana wartość nietrwałych nietrwałych, niezależnie od tego, czy mają być oznaczane wulgarne przy użyciu odpowiednich tagów (co umożliwia dodanie własnego przetwarzania końcowego) lub nie ma żadnej akcji. Akceptowane wartości `ProfanityAction` to `Deleted` `Marked` i `NoAction` (domyślnie).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Akcja</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Jest to zachowanie domyślne. Niedostępność zostanie przekazana z lokalizacji źródłowej do docelowej.<br/><br/>
    <strong>Przykładowe źródło (japoński)</strong>: 彼はジャッカスです.<br/>
    <strong>Przykładowe tłumaczenie (angielski)</strong>: jest to Jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Wyrazy wulgarne zostaną usunięte z danych wyjściowych bez zastępowania.<br/><br/>
    <strong>Przykładowe źródło (japoński)</strong>: 彼はジャッカスです.<br/>
    <strong>Przykładowe tłumaczenie (angielski)</strong>: jest to.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Wyrazy wulgarne są zastępowane przez znacznik w danych wyjściowych. Znacznik zależy od <code>ProfanityMarker</code> parametru.<br/><br/>
W przypadku <code>ProfanityMarker=Asterisk</code> , wulgarne słowa są zastępowane <code>***</code> :<br/>
    <strong>Przykładowe źródło (japoński)</strong>: 彼はジャッカスです.<br/>
    <strong>Przykładowe tłumaczenie (angielski)</strong>: jest to \* \* \* .<br/><br/>
W przypadku <code>ProfanityMarker=Tag</code> , wulgarne słowa są otoczone przez tagi XML &lt; wulgarności &gt; i &lt; /profanity &gt; :<br/>
    <strong>Przykładowe źródło (japoński)</strong>: 彼はジャッカスです.<br/>
    <strong>Przykładowe tłumaczenie (angielski)</strong>: jest to &lt; &gt; niejackassa &lt; /profanitya &gt; .
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

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Tłumaczenie zawartości przy użyciu znaczników i decydowanie o tym, co jest tłumaczone

Często tłumaczy zawartość, która obejmuje znaczniki, takie jak zawartość ze strony HTML lub zawartość z dokumentu XML. Uwzględnij parametr zapytania `textType=html` podczas tłumaczenia zawartości ze znacznikami. Ponadto czasami warto wykluczyć określoną zawartość z tłumaczenia. Możesz użyć atrybutu, `class=notranslate` Aby określić zawartość, która powinna pozostać w oryginalnym języku. W poniższym przykładzie zawartość wewnątrz pierwszego `div` elementu nie zostanie przetłumaczona, podczas gdy zawartość drugiego `div` elementu zostanie przetłumaczona.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Oto przykładowe żądanie do zilustrowania.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Odpowiedź:

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

Wyrównanie jest zwracane jako wartość ciągu następującego formatu dla każdego wyrazu źródła. Informacje dla każdego wyrazu są oddzielone spacjami, w tym dla języków (skryptów), takich jak chiński:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Przykładowy ciąg wyrównania: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Inaczej mówiąc, dwukropek oddziela indeks początkowy i końcowy, myślnik oddziela Języki, a spacja oddziela słowa. Jedno słowo może być wyrównane z zerem, jednym lub wieloma wyrazami w innym języku, a wyrównane słowa mogą nie być ciągłe. Gdy żadne informacje o wyrównaniu nie są dostępne, element wyrównania będzie pusty. Metoda nie zwraca żadnego błędu w tym przypadku.

Aby uzyskać informacje o wyrównaniu, określ `includeAlignment=true` wartość w ciągu zapytania.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true&quot; -H &quot;Ocp-Apim-Subscription-Key: <client-secret>&quot; -H &quot;Content-Type: application/json; charset=UTF-8&quot; -d &quot;[{'Text':'The answer lies in machine translation.'}]"
```

Odpowiedź:

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

Informacje o wyrównaniu zaczynają się od `0:2-0:1` , co oznacza, że pierwsze trzy znaki w źródłowym tekście ( `The` ) są mapowane na pierwsze dwa znaki w przetłumaczonym tekście ( `La` ).

#### <a name="limitations"></a>Ograniczenia
Uzyskiwanie informacji o wyrównaniu to eksperymentalna funkcja, która umożliwiła Tworzenie prototypów badań i środowisk z potencjalnymi mapowaniami fraz. Firma Microsoft może zrezygnować z obsługi tego w przyszłości. Poniżej przedstawiono niektóre z istotnych ograniczeń, w których wyrównania nie są obsługiwane:

* Wyrównanie nie jest dostępne dla tekstu w formacie HTML tzn., texttype = HTML
* Wyrównanie jest zwracane tylko dla podzestawu par języka:
  - Angielski do/z dowolnego innego języka, z wyjątkiem chińskiego tradycyjnego, kantoński (tradycyjny) lub serbski (cyrylica).
  - od wersji japońskiej do koreańskiej lub z koreańskiej do japońskiej.
  - od japońskiego do chińskiego uproszczonego i chińskiego uproszczonego w języku japońskim. 
  - od chińskiej uproszczonej na chiński tradycyjny i chiński tradycyjny do chińskiego uproszczonego. 
* Nie otrzymasz wyrównania, jeśli zdanie jest przekształceniem w konserwie. Przykładem przeprowadzonego tłumaczenia jest "to jest test", "mam miłość" i inne zdania o wysokiej częstotliwości.
* Wyrównanie nie jest dostępne w przypadku zastosowania któregokolwiek z metod, aby zapobiec translacji zgodnie z opisem w [tym miejscu](../prevent-translation.md)

### <a name="obtain-sentence-boundaries"></a>Uzyskaj granice zdania

Aby uzyskać informacje o długości zdania w tekście źródłowym i przetłumaczonym tekście, określ `includeSentenceLength=true` wartość w ciągu zapytania.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

Odpowiedź:

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

Jeśli znasz już tłumaczenie, które chcesz zastosować do wyrazu lub frazy, możesz podać je jako znacznik w żądaniu. Słownik dynamiczny jest bezpieczny tylko dla właściwych rzeczowników, takich jak imiona i nazwiska oraz nazwy produktów.

Znacznik do dostarczenia używa następującej składni.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Rozważmy na przykład zdanie w języku angielskim "słowo wordomatic jest wpisem słownika". Aby zachować słowo _wordomatic_ w tłumaczeniu, Wyślij żądanie:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Wynik:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Ta funkcja działa tak samo jak w programie `textType=text` lub `textType=html` . Ta funkcja powinna być stosowana oszczędnie. Odpowiednim i znacznie lepszym sposobem dostosowywania tłumaczenia jest użycie translatora niestandardowego. W przypadku translatora niestandardowego w pełni stosowane są prawdopodobieństwa kontekstowe i statystyczne. Jeśli masz lub możesz stworzyć dane szkoleniowe, które pokazują swoją służbę lub frazę w kontekście, uzyskasz dużo lepszych wyników. [Dowiedz się więcej o usłudze translator niestandardowym](../customization.md).