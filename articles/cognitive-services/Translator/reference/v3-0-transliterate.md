---
title: Metoda transliteracji translatora
titleSuffix: Azure Cognitive Services
description: Konwertuj tekst w jednym języku z jednego skryptu na inny skrypt za pomocą metody transliteracji w usłudze translator.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 0d5a7f8df0c722ca52780ba254e9af9608f26b54
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895428"
---
# <a name="translator-30-transliterate"></a>Translator 3,0: transliteracja

Konwertuje tekst w jednym języku z jednego skryptu na inny skrypt.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie do:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

<table width="100%">
  <th width="20%">Parametr zapytania</th>
  <th>Opis</th>
  <tr>
    <td>api-version</td>
    <td>*Wymagany parametr*.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi być `3.0` .</td>
  </tr>
  <tr>
    <td>language</td>
    <td>*Wymagany parametr*.<br/>Określa język tekstu do przekonwertowania z jednego skryptu na drugi. Możliwe języki są wymienione w `transliteration` zakresie uzyskanym przez wykonywanie zapytań do usługi pod kątem [obsługiwanych języków](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Wymagany parametr*.<br/>Określa skrypt używany przez tekst wejściowy. Wyszukaj [obsługiwane języki](./v3-0-languages.md) przy użyciu `transliteration` zakresu, aby znaleźć skrypty wejściowe dostępne dla wybranego języka.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Wymagany parametr*.<br/>Określa skrypt wyjściowy. Wyszukaj [obsługiwane języki](./v3-0-languages.md) przy użyciu `transliteration` zakresu, aby znaleźć skrypty wyjściowe dostępne dla wybranej kombinacji języka i skryptu wejściowego.</td>
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
    <td>*Wymagany nagłówek żądania*.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json` .</td>
  </tr>
  <tr>
    <td>Długość zawartości</td>
    <td>*Wymagany nagłówek żądania*.<br/>Długość treści żądania.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcjonalne*.<br/>Wygenerowany przez klienta identyfikator GUID służący do unikatowej identyfikacji żądania. Należy pamiętać, że ten nagłówek można pominąć, jeśli w ciągu zapytania zostanie uwzględniony identyfikator śledzenia, przy użyciu parametru zapytania o nazwie `ClientTraceId` .</td>
  </tr>
</table> 

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON z właściwością ciągu o nazwie `Text` , która reprezentuje ciąg do przekonwertowania.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Obowiązują następujące ograniczenia:

* Tablica może zawierać co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może być dłuższa niż 1 000 znaków, w tym spacje.
* Cały tekst zawarty w żądaniu nie może zawierać więcej niż 5 000 znaków, w tym spacji.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź to tablica JSON z jednym wynikiem dla każdego elementu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `text`: Ciąg, który jest wynikiem konwersji ciągu wejściowego na skrypt wyjściowy.
  
  * `script`: Ciąg określający skrypt używany w danych wyjściowych.

Przykładowa odpowiedź JSON to:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>Nagłówki odpowiedzi

<table width="100%">
  <th width="20%">Nagłówki</th>
  <th>Opis</th>
  <tr>
    <td>X-IdentyfikatorŻądania</td>
    <td>Wartość wygenerowana przez usługę w celu zidentyfikowania żądania. Służy do rozwiązywania problemów.</td>
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
    <td>429</td>
    <td>Serwer odrzucił żądanie z powodu przekroczenia limitów żądań przez klienta.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś go z: Data i godzina błędu, identyfikator żądania z nagłówka odpowiedzi `X-RequestId` oraz identyfikator klienta z nagłówka żądania `X-ClientTraceId` .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, zgłoś go z: Data i godzina błędu, identyfikator żądania z nagłówka odpowiedzi `X-RequestId` oraz identyfikator klienta z nagłówka żądania `X-ClientTraceId` .</td>
  </tr>
</table> 

Jeśli wystąpi błąd, żądanie zwróci także odpowiedź na błąd JSON. Kod błędu to 6-cyfrowy numer łączący 3-cyfrowy kod stanu HTTP, a następnie 3-cyfrowy numer do dalszej kategoryzacji błędu. Typowe kody błędów można znaleźć na [stronie referencyjnej translatora v3](./v3-0-reference.md#errors). 

## <a name="examples"></a>Przykłady

Poniższy przykład pokazuje, jak konwertować dwa japońskie ciągi na łacińskie japońskie.

Ładunek JSON dla żądania w tym przykładzie:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Jeśli używasz zwinięcie w oknie wiersza polecenia, które nie obsługuje znaków Unicode, weź następujący ładunek JSON i Zapisz go w pliku o nazwie `request.txt` . Pamiętaj, aby zapisać plik z `UTF-8` kodowaniem.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```