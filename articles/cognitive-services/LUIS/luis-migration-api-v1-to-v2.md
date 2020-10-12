---
title: Migracja interfejsu API w wersji od 1 do v2
titleSuffix: Azure Cognitive Services
description: Program Endpoints i interfejsy API tworzenia Language Understanding w wersji 1 są przestarzałe. Skorzystaj z tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do punktu końcowego w wersji 2 i interfejsów API tworzenia.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: e1e9ac4ceef843712cc2e39f26ff0aca5341e201
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541326"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>Przewodnik migracji interfejsu API V1 do wersji 2 dla aplikacji LUIS
[Punkt końcowy](https://aka.ms/v1-endpoint-api-docs) w wersji 1 i interfejsy API [tworzenia](https://aka.ms/v1-authoring-api-docs) są przestarzałe. Skorzystaj z tego przewodnika, aby zrozumieć, jak przeprowadzić migrację do [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) w wersji 2 i interfejsów API [tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) .

## <a name="new-azure-regions"></a>Nowe regiony platformy Azure
LUIS zawiera nowe [regiony](https://aka.ms/LUIS-regions) dla interfejsów API Luis. LUIS udostępnia inny Portal dla grup regionów. Aplikacja musi być utworzona w tym samym regionie, w którym oczekuje się, aby wykonać zapytanie. Aplikacje nie migrują automatycznie regionów. Możesz wyeksportować aplikację z jednego regionu, a następnie zaimportować ją do innej, aby była dostępna w nowym regionie.

## <a name="authoring-route-changes"></a>Tworzenie zmian trasy
Trasa interfejsu API tworzenia została zmieniona z używania **trasy usługi** do korzystania z trasy **interfejsu API** .


| Wersja | route |
|--|--|
|1|/Luis/v1.0/**/Apps**|
|2|**interfejs API**/Luis//v2.0/Apps|


## <a name="endpoint-route-changes"></a>Zmiany trasy punktu końcowego
Interfejs API punktu końcowego ma nowe parametry ciągu zapytania, a także inną odpowiedź. Jeśli flaga verbose ma wartość true, wszystkie intencje, niezależnie od wyniku, są zwracane w tablicy o nazwie intencje (oprócz topScoringIntent).

| Wersja | Pobierz trasę |
|--|--|
|1|/Luis/V1/Application? ID = {appId} &q = {q}|
|2|/luis/v2.0/apps/{appId}? q = {q} [&timezoneOffset] [&verbose] [&sprawdzanie pisowni] [&przemieszczanie] [&Bing-pisownia-Check-Subscription-Key] [&log]|


odpowiedź na powodzenie punktu końcowego V1:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

odpowiedź na powodzenie punktu końcowego w wersji 2:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>Zarządzanie kluczami nie jest już w interfejsie API
Interfejsy API klucza punktu końcowego subskrypcji są przestarzałe, zwracając 410.

| Wersja | route |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

[Klucze punktów końcowych](luis-how-to-azure-subscription.md) platformy Azure są generowane w Azure Portal. Ten klucz jest przypisywany do aplikacji LUIS na stronie **[Publikowanie](luis-how-to-azure-subscription.md)** . Nie musisz znać rzeczywistej wartości klucza. LUIS używa nazwy subskrypcji do przypisania.

## <a name="new-versioning-route"></a>Nowa trasa obsługi wersji
Model V2 jest teraz zawarty w [wersji](luis-how-to-manage-versions.md). Nazwa wersji zawiera 10 znaków w marszrucie. Domyślna wersja to "0,1".

| Wersja | route |
|--|--|
|1|/Luis/v1.0/**/Apps/{AppID}/Entities**|
|2|/Luis/**API**/v2.0/Apps/{AppID}/**wersje**/{versionId}/Entities|

## <a name="metadata-renamed"></a>Zmieniono nazwę metadanych
Kilka interfejsów API, które zwracają metadane LUIS, mają nowe nazwy.

| Nazwa trasy w wersji 1 | Nazwa trasy w wersji 2 |
|--|--|
|PersonalAssistantApps |Asystenci|
|applicationcultures|kultury|
|applicationdomains|3.x|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Przykładowa" została zmieniona na "sugerował"
LUIS sugeruje wyrażenia długości z istniejącego [punktu końcowego wyrażenia długości](luis-how-to-review-endpoint-utterances.md) , który może wzmocnić model. W poprzedniej wersji ta nazwa była **Przykładowa**. W nowej wersji nazwa jest zmieniana z przykładu, aby **sugerował**. Jest to tzw. **[Przegląd punktu końcowego wyrażenia długości](luis-how-to-review-endpoint-utterances.md)** w witrynie sieci Web Luis.

| Wersja | route |
|--|--|
|1|przykład **/Luis/v1.0/**/Apps/{AppID}/Entities/{EntityId}/**sample**|
|1|przykład **/Luis/v1.0/**/Apps/{AppID}/Intents/{intentId}/**sample**|
|2|/Luis/**API**/v2.0/Apps/{AppID}/**wersje**/{versionId}/Entities/{EntityId}/**Sugeruj**|
|2|/Luis/**API**/v2.0/Apps/{AppID}/**wersje**/{versionId}/Intents/{intentId}/**Sugeruj**|


## <a name="create-app-from-prebuilt-domains"></a>Tworzenie aplikacji na podstawie wstępnie skompilowanych domen
[Prebudowane domeny](luis-how-to-use-prebuilt-domains.md) zapewniają wstępnie zdefiniowany model domeny. Prebudowane domeny umożliwiają szybkie tworzenie aplikacji LUIS dla wspólnych domen. Ten interfejs API umożliwia utworzenie nowej aplikacji na podstawie wstępnie skompilowanej domeny. Odpowiedź jest nowym identyfikatorem aplikacji.

|2. trasa|czasownik|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |Pobierz, Opublikuj|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>Importowanie aplikacji 1. x do 2. x
Plik JSON wyeksportowanej aplikacji 1. x ma pewne obszary, które należy zmienić przed zaimportowaniem do [LUIS][LUIS] 2,0.

### <a name="prebuilt-entities"></a>Wstępnie utworzone jednostki
[Wstępnie utworzone jednostki](luis-prebuilt-entities.md) uległy zmianie. Upewnij się, że używasz wstępnie skompilowanych jednostek w wersji 2. Obejmuje to użycie [datetimeV2](luis-reference-prebuilt-datetimev2.md)zamiast DateTime.

### <a name="actions"></a>Akcje
Właściwość Actions nie jest już prawidłowa. Powinien być pusty

### <a name="labeled-utterances"></a>Oznaczony wyrażenia długości
W wersji 1 dozwolona etykieta wyrażenia długości ma zawierać spacje na początku lub na końcu wyrazu lub frazy. Usunięto spacje.

## <a name="common-reasons-for-http-response-status-codes"></a>Typowe przyczyny kodów stanu odpowiedzi HTTP
Zobacz [kody odpowiedzi interfejsu API Luis](luis-reference-response-codes.md).

## <a name="next-steps"></a>Następne kroki

Użyj dokumentacji interfejsu API v2, aby zaktualizować istniejące wywołania REST do LUISego [punktu końcowego](https://go.microsoft.com/fwlink/?linkid=2092356) i interfejsów API [tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) .

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
