---
title: 'Szybki Start: korzystanie z & zawieszania do zarządzania bazą wiedzy QnA Maker'
description: W tym przewodniku szybki start przedstawiono sposób tworzenia, publikowania i wysyłania zapytań do bazy wiedzy przy użyciu interfejsów API REST.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 11/09/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: b619b258084b457621ad1a5650aaf621df9677a7
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376506"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Szybki Start: używanie zawieszania i REST do zarządzania bazą wiedzy

Ten przewodnik Szybki Start przeprowadzi Cię przez proces tworzenia, publikowania i wysyłania zapytań do bazy wiedzy. Usługa QnA Maker automatycznie wyodrębnia pytania i odpowiedzi z częściowo ustrukturyzowanej zawartości, na przykład często zadawanych pytań, ze [źródeł danych](../Concepts/knowledge-base.md). Model bazy wiedzy jest zdefiniowany w formacie JSON wysyłanym w treści żądania interfejsu API.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

* Bieżąca wersja programu [zwinięcie](https://curl.haxx.se/). Kilka przełączników wiersza polecenia są używane w przewodnikach Szybki Start, które zostały zanotowane w [dokumentacji programu zwinięcie](https://curl.haxx.se/docs/manpage.html).
* Aby użyć nazwy klucza i zasobu, musisz mieć [zasób QNA Maker](../How-To/set-up-qnamaker-service-azure.md). Podczas tworzenia zasobu wprowadzono **nazwę** zasobu, a następnie klucz został utworzony. Nazwa zasobu jest używana jako poddomena dla punktu końcowego. Aby pobrać nazwę klucza i zasobu, wybierz pozycję **Szybki Start** dla zasobu w Azure Portal. Nazwa zasobu to pierwsza poddomena adresu URL punktu końcowego:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> Poniższe przykłady BASH używają `\` znaku kontynuacji wiersza. Jeśli konsola lub terminal używa innego znaku kontynuacji wiersza, użyj tego znaku.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

* Bieżąca wersja programu [zwinięcie](https://curl.haxx.se/). Kilka przełączników wiersza polecenia są używane w przewodnikach Szybki Start, które zostały zanotowane w [dokumentacji programu zwinięcie](https://curl.haxx.se/docs/manpage.html).
* Aby użyć nazwy klucza i zasobu, musisz mieć [zasób QNA Maker](../How-To/set-up-qnamaker-service-azure.md). Podczas tworzenia zasobu wprowadzono **nazwę** zasobu, a następnie klucz został utworzony. Nazwa zasobu jest używana jako poddomena dla punktu końcowego. Aby pobrać nazwę klucza i zasobu, wybierz pozycję **Szybki Start** dla zasobu w Azure Portal. Nazwa zasobu to pierwsza poddomena adresu URL punktu końcowego:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1`

> [!CAUTION]
> Poniższe przykłady BASH używają `\` znaku kontynuacji wiersza. Jeśli konsola lub terminal używa innego znaku kontynuacji wiersza, użyj tego znaku.

---

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Aby utworzyć bazę wiedzy z interfejsami API REST i zwinięciem, musisz dysponować następującymi informacjami:

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu QnA Maker|`-h` param dla `Ocp-Apim-Subscription-Key` nagłówka|Uwierzytelnianie w usłudze QnA Maker|
|JSON opisujące bazę wiedzy|`-d` param|[Przykłady](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) kodu JSON|
|Rozmiar JSON w bajtach|`-h` param dla `Content-Size` nagłówka||

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu oraz wartości JSON i rozmiaru JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Odpowiedź zazwinięcie z QnA Maker obejmuje `operationId` , który jest wymagany do [uzyskania stanu operacji](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)


Aby utworzyć bazę wiedzy z interfejsami API REST i zwinięciem, musisz dysponować następującymi informacjami:

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu QnA Maker|`-h` param dla `Ocp-Apim-Subscription-Key` nagłówka|Uwierzytelnianie w usłudze QnA Maker|
|JSON opisujące bazę wiedzy|`-d` param|[Przykłady](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) kodu JSON|
|Rozmiar JSON w bajtach|`-h` param dla `Content-Size` nagłówka||

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu oraz wartości JSON i rozmiaru JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Odpowiedź zazwinięcie z QnA Maker obejmuje `operationId` , który jest wymagany do [uzyskania stanu operacji](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```
---

## <a name="get-status-of-operation"></a>Pobierz stan operacji

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Podczas tworzenia bazy wiedzy, ponieważ operacja jest asynchroniczna, odpowiedź zawiera informacje służące do określenia stanu.

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Identyfikator operacji|Trasa URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Klucz zasobu QnA Maker|`-h` param dla `Ocp-Apim-Subscription-Key` nagłówka|Uwierzytelnianie w usłudze QnA Maker|

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu i identyfikatora operacji.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Odpowiedź zwinięcie zawiera stan. W przypadku pomyślnego stanu operacji zostanie `resourceLocation` uwzględniony identyfikator bazy wiedzy.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```
# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)


Podczas tworzenia bazy wiedzy, ponieważ operacja jest asynchroniczna, odpowiedź zawiera informacje służące do określenia stanu.

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Identyfikator operacji|Trasa URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Klucz zasobu QnA Maker|`-h` param dla `Ocp-Apim-Subscription-Key` nagłówka|Uwierzytelnianie w usłudze QnA Maker|

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu i identyfikatora operacji.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Odpowiedź zwinięcie zawiera stan. W przypadku pomyślnego stanu operacji zostanie `resourceLocation` uwzględniony identyfikator bazy wiedzy.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

---

## <a name="publish-knowledge-base"></a>Publikowanie bazy wiedzy

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Przed wykonaniem zapytania w bazie wiedzy należy:
* Publikowanie bazy wiedzy
* Pobierz klucz punktu końcowego środowiska uruchomieniowego

To zadanie służy do publikowania bazy wiedzy. Pobieranie klucza punktu końcowego środowiska uruchomieniowego jest [osobnym zadaniem](#get-published-knowledge-bases-runtime-endpoint-key).

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu QnA Maker|`-h` param dla `Ocp-Apim-Subscription-Key` nagłówka|Uwierzytelnianie w usłudze QnA Maker|
|Identyfikator bazy wiedzy|Trasa URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu i identyfikatora bazy wiedzy.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Stan odpowiedzi to 204 bez wyników. Użyj `-v` parametru wiersza polecenia, aby wyświetlić pełne dane wyjściowe polecenia zwinięcie. Spowoduje to uwzględnienie stanu HTTP.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Przed wykonaniem zapytania w bazie wiedzy należy:
* Publikowanie bazy wiedzy
* Pobierz klucz punktu końcowego środowiska uruchomieniowego

To zadanie służy do publikowania bazy wiedzy. Pobieranie klucza punktu końcowego środowiska uruchomieniowego jest [osobnym zadaniem](#get-published-knowledge-bases-runtime-endpoint-key).

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu QnA Maker|`-h` param dla `Ocp-Apim-Subscription-Key` nagłówka|Uwierzytelnianie w usłudze QnA Maker|
|Identyfikator bazy wiedzy|Trasa URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu i identyfikatora bazy wiedzy.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Stan odpowiedzi to 204 bez wyników. Użyj `-v` parametru wiersza polecenia, aby wyświetlić pełne dane wyjściowe polecenia zwinięcie. Spowoduje to uwzględnienie stanu HTTP.

---

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Pobierz klucz punktu końcowego środowiska uruchomieniowego opublikowanej bazy wiedzy

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Przed wykonaniem zapytania w bazie wiedzy należy:
* Publikowanie bazy wiedzy
* Pobierz klucz punktu końcowego środowiska uruchomieniowego

To zadanie Pobiera klucz punktu końcowego środowiska uruchomieniowego. Publikowanie bazy wiedzy jest [osobnym zadaniem](#publish-knowledge-base).

Klucz punktu końcowego środowiska uruchomieniowego jest tym samym kluczem dla wszystkich baz wiedzy przy użyciu zasobu QnA Maker.

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu QnA Maker|`-h` param dla `Ocp-Apim-Subscription-Key` nagłówka|Uwierzytelnianie w usłudze QnA Maker|

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


Odpowiedź zwinięcie zawiera klucze punktu końcowego środowiska uruchomieniowego. Użyj tylko jednego z kluczy podczas wykonywania zapytania w celu uzyskania odpowiedzi z bazy wiedzy.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```
# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Przed wykonaniem zapytania w bazie wiedzy należy:
* Publikowanie bazy wiedzy
* Pobierz klucz punktu końcowego środowiska uruchomieniowego

To zadanie Pobiera klucz punktu końcowego środowiska uruchomieniowego. Publikowanie bazy wiedzy jest [osobnym zadaniem](#publish-knowledge-base).

Klucz punktu końcowego środowiska uruchomieniowego jest tym samym kluczem dla wszystkich baz wiedzy przy użyciu zasobu QnA Maker.

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu QnA Maker|`-h` param dla `Ocp-Apim-Subscription-Key` nagłówka|Uwierzytelnianie w usłudze QnA Maker|

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


Odpowiedź zwinięcie zawiera klucze punktu końcowego środowiska uruchomieniowego. Użyj tylko jednego z kluczy podczas wykonywania zapytania w celu uzyskania odpowiedzi z bazy wiedzy.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

---

## <a name="query-for-answer-from-published-knowledge-base"></a>Zapytanie o odpowiedź z opublikowanej bazy wiedzy

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Uzyskiwanie odpowiedzi ze wiedzy odbywa się z osobnego środowiska uruchomieniowego niż zarządzanie bazą wiedzy. Ponieważ jest to osobne środowisko uruchomieniowe, należy przeprowadzić uwierzytelnianie przy użyciu klucza czasu wykonywania.

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz środowiska uruchomieniowego QnA Maker|`-h` param dla `Authorization` nagłówka|Klucz jest częścią ciągu zawierającego wyraz `Endpointkey ` . Uwierzytelnianie w usłudze QnA Maker|
|Identyfikator bazy wiedzy|Trasa URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|Zapytanie JSON opisujące|`-d` param|[Parametry treści żądania](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) i [przykłady](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) kodu JSON|
|Rozmiar JSON w bajtach|`-h` param dla `Content-Size` nagłówka||

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu i identyfikatora bazy wiedzy.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Pomyślna odpowiedź obejmuje największą odpowiedź oraz inne informacje, które aplikacja kliencka, taka jak rozmowa bot, musi wyświetlić odpowiedź do użytkownika.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Uzyskiwanie odpowiedzi ze wiedzy odbywa się z osobnego środowiska uruchomieniowego niż zarządzanie bazą wiedzy. Ponieważ jest to osobne środowisko uruchomieniowe, należy przeprowadzić uwierzytelnianie przy użyciu klucza czasu wykonywania.

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu QnA Maker|`-h` param dla `Ocp-Apim-Subscription-Key` nagłówka|Uwierzytelnianie w usłudze QnA Maker|
|Identyfikator bazy wiedzy|Trasa URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|Zapytanie JSON opisujące|`-d` param|[Parametry treści żądania](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) i [przykłady](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) kodu JSON|
|Rozmiar JSON w bajtach|`-h` param dla `Content-Size` nagłówka||

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu i identyfikatora bazy wiedzy.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Pomyślna odpowiedź obejmuje największą odpowiedź oraz inne informacje, które aplikacja kliencka, taka jak rozmowa bot, musi wyświetlić odpowiedź do użytkownika.


---

## <a name="delete-knowledge-base"></a>Usuń bazę wiedzy

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Po zakończeniu pracy z bazą wiedzy usuń ją.

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu QnA Maker|`-h` param dla `Ocp-Apim-Subscription-Key` nagłówka|Uwierzytelnianie w usłudze QnA Maker|
|Identyfikator bazy wiedzy|Trasa URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu i identyfikatora bazy wiedzy.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Stan odpowiedzi to 204 bez wyników. Użyj `-v` parametru wiersza polecenia, aby wyświetlić pełne dane wyjściowe polecenia zwinięcie. Spowoduje to uwzględnienie stanu HTTP.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Po zakończeniu pracy z bazą wiedzy usuń ją.

|Informacje|Konfiguracja zwinięcie|Przeznaczenie|
|--|--|--|
|Nazwa zasobu QnA Maker|Adres URL|używane do konstruowania adresu URL|
|Klucz zasobu QnA Maker|`-h` param dla `Ocp-Apim-Subscription-Key` nagłówka|Uwierzytelnianie w usłudze QnA Maker|
|Identyfikator bazy wiedzy|Trasa URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Polecenie zwinięcie jest wykonywane z poziomu powłoki BASH. Edytuj to polecenie przy użyciu własnej nazwy zasobu, klucza zasobu i identyfikatora bazy wiedzy.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Stan odpowiedzi to 204 bez wyników. Użyj `-v` parametru wiersza polecenia, aby wyświetlić pełne dane wyjściowe polecenia zwinięcie. Spowoduje to uwzględnienie stanu HTTP.

---

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Tworzenie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) Dokumentacja referencyjna
* [Środowisko uruchomieniowe](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/) Dokumentacja referencyjna
* [Przykładowe skrypty BASH korzystające z zwinięcie](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)