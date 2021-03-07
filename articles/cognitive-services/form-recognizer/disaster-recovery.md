---
title: Wskazówki dotyczące odzyskiwania po awarii dla aparatu rozpoznawania formularzy platformy Azure
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą interfejsu API kopiowania modelu utworzyć kopię zapasową zasobów aparatu rozpoznawania formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 0343402d92498bff56250027086cbf2ceb258f0f
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427168"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Tworzenie kopii zapasowych i odzyskiwanie modeli aparatu rozpoznawania formularzy

Podczas tworzenia zasobu aparatu rozpoznawania formularza w Azure Portal należy określić region. Od tego czasu zasób i wszystkie jego operacje pozostają skojarzone z tym określonym regionem serwera platformy Azure. Jest to rzadki, ale nie możliwe, aby napotkać problem z siecią, który trafi na cały region. Jeśli Twoje rozwiązanie musi być zawsze dostępne, należy zaprojektować je do pracy awaryjnej w innym regionie lub podzielić obciążenie między dwa lub więcej regionów. Oba podejścia wymagają co najmniej dwóch zasobów aparatu rozpoznawania formularzy w różnych regionach i możliwość synchronizowania modeli niestandardowych w różnych regionach.

Interfejs API kopiowania umożliwia korzystanie z tego scenariusza przez umożliwienie kopiowania modeli niestandardowych z jednego konta aparatu rozpoznawania formularzy lub do innych, które mogą istnieć w dowolnym obsługiwanym regionie geograficznym. W tym przewodniku pokazano, jak używać interfejsu API REST kopiowania z zwinięciem. Możesz również użyć usługi żądania HTTP, takiej jak program Poster, aby wydać żądania.

## <a name="business-scenarios"></a>Scenariusze biznesowe

Jeśli Twoja aplikacja lub firma zależy od użycia niestandardowego modelu aparatu rozpoznawania formularzy, zalecamy skopiowanie modelu na inne konto aparatu rozpoznawania formularza w innym regionie. Jeśli wystąpi awaria regionalna, można uzyskać dostęp do modelu w regionie, w którym został skopiowany.

##  <a name="prerequisites"></a>Wymagania wstępne

1. Dwie formy rozpoznawania zasobów platformy Azure w różnych regionach świadczenia usługi Azure. Jeśli ich nie masz, przejdź do Azure Portal i <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" Utwórz nowy formularz aparatu rozpoznawania, " target="_blank"> Utwórz nowy zasób aparatu rozpoznawania </a> .
1. Klucz subskrypcji, adres URL punktu końcowego i Identyfikator subskrypcji Twojego zasobu aparatu rozpoznawania formularza. Te wartości można znaleźć na karcie **Przegląd** zasobu na Azure Portal.


## <a name="copy-api-overview"></a>Przegląd interfejsu API kopiowania

Proces kopiowania modelu niestandardowego składa się z następujących kroków:

1. Najpierw należy wydać żądanie autoryzacji kopiowania do zasobu docelowego &mdash; , który jest zasobem, który otrzyma skopiowany model. Powracasz do adresu URL nowo utworzonego modelu docelowego, który otrzyma skopiowane dane.
1. Następnie Wyślij żądanie Copy do zasobu źródłowego zasobu zawierającego model, który &mdash; ma zostać skopiowany. Otrzymasz zwrotny adres URL, który można wysłać do zapytania, aby śledzić postęp operacji.
1. Będziesz używać poświadczeń zasobów źródłowych do wysyłania zapytań do adresu URL postępu do momentu sukcesu operacji. Możesz również wysłać zapytanie do nowego identyfikatora modelu w zasobie docelowym, aby uzyskać stan nowego modelu.

> [!CAUTION]
> Interfejs API kopiowania aktualnie nie obsługuje identyfikatorów modelu dla [złożonych modeli](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/Compose). Redagowanie modelu jest funkcją w wersji zapoznawczej w wersji 2.1 — Preview. 2 wersja zapoznawcza. 

## <a name="generate-copy-authorization-request"></a>Generuj żądanie autoryzacji kopiowania

Poniższe żądanie HTTP pobiera autoryzację kopiowania z zasobu docelowego. Musisz wprowadzić punkt końcowy i klucz zasobu docelowego jako nagłówki.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Otrzymasz `201\Created` odpowiedź z `modelId` wartością w treści. Ten ciąg jest IDENTYFIKATORem nowo utworzonego (pustego) modelu. `accessToken`Jest to konieczne, aby interfejs API skopiował dane do tego zasobu, a `expirationDateTimeTicks` wartość to wygaśnięcie tokenu. Zapisz wszystkie trzy z tych wartości w bezpiecznej lokalizacji.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>Rozpocznij operację kopiowania

Poniższe żądanie HTTP uruchamia operację kopiowania dla zasobu źródłowego. Musisz wprowadzić punkt końcowy i klucz zasobu źródłowego jako nagłówki. Należy zauważyć, że adres URL żądania zawiera identyfikator modelu źródłowego, który ma zostać skopiowany.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Treść żądania musi mieć następujący format. Należy wprowadzić identyfikator zasobu i nazwę regionu zasobu docelowego. Identyfikator zasobu można znaleźć na karcie **Właściwości** zasobu w Azure Portal. nazwę regionu można znaleźć na karcie **klucze i punkt końcowy** . Wymagany jest również Identyfikator modelu, token dostępu i wartość wygaśnięcia skopiowane z poprzedniego kroku.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> Interfejs API kopiowania w sposób przezroczysty obsługuje funkcję [AEK/CMK](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) . Nie wymaga to żadnej szczególnej obróbki, ale należy pamiętać, że jeśli kopiujesz między niezaszyfrowanym zasobem a zaszyfrowanym zasobem, musisz dołączyć nagłówek żądania `x-ms-forms-copy-degrade: true` . Jeśli ten nagłówek nie jest uwzględniony, operacja kopiowania zakończy się niepowodzeniem i zwróci `DataProtectionTransformServiceError` .

Otrzymasz `202\Accepted` odpowiedź z nagłówkiem Operation-Location. Ta wartość jest adresem URL, który będzie używany do śledzenia postępu operacji. Skopiuj ją do tymczasowej lokalizacji dla następnego kroku.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>Typowe błędy

|Błąd|Rozwiązanie|
|:--|:--|
| 400/złe żądanie z `"code:" "1002"` | Wskazuje błąd walidacji lub nieprawidłowo sformułowane żądanie kopiowania. Typowe problemy obejmują: a) nieprawidłowy lub zmodyfikowany `copyAuthorization` ładunek. b) wygasła wartość `expirationDateTimeTicks` tokenu ( `copyAuhtorization` ładunek jest ważny przez 24 godziny). c) jest nieprawidłowy lub nieobsługiwany `targetResourceRegion` . d) nieprawidłowy lub źle sformułowany `targetResourceId` ciąg.
|

## <a name="track-copy-progress"></a>Śledź postęp kopiowania

Śledź postęp, wykonując zapytania dotyczące interfejsu API **wyników Get Copy model** względem źródłowego punktu końcowego zasobu.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Odpowiedź będzie się różnić w zależności od stanu operacji. Poszukaj `"status"` pola w treści JSON. W przypadku automatyzowania tego wywołania interfejsu API w skrypcie zalecamy wykonanie zapytania o operację co sekundę.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>Typowe błędy

|Błąd|Rozwiązanie|
|:--|:--|
|"błędy": [{"Code": "AuthorizationError",<br>"Message": "niepowodzenie autoryzacji z powodu <br>brakujące lub nieprawidłowe oświadczenia autoryzacji. "}]   | Występuje, gdy `copyAuthorization` ładunek lub zawartość są modyfikowane na podstawie tego, co zostało zwrócone przez `copyAuthorization` interfejs API. Upewnij się, że ładunek jest identyczny z dokładną zawartością, która została zwrócona z wcześniejszego `copyAuthorization` wywołania.|
|"błędy": [{"Code": "AuthorizationError",<br>"komunikat": "nie można pobrać autoryzacji <br>metadane. Jeśli ten problem będzie nadal występował, użyj innego <br>Model docelowy do kopiowania. "}] | Wskazuje, że `copyAuthorization` ładunek jest ponownie używany z żądaniem kopiowania. Żądanie Copy, które powiodło się, nie będzie zezwalać na żadne dalsze żądania, które używają tego samego `copyAuthorization` ładunku. Jeśli zostanie zgłoszony oddzielny błąd (taki jak wymienione poniżej), a następnie ponów próbę kopiowania z tym samym ładunkiem autoryzacji, ten błąd zostanie wygenerowany. Rozwiązanie to wygenerowanie nowego `copyAuthorization` ładunku, a następnie ponowne wydanie żądania kopiowania.|
|"błędy": [{"Code": "DataProtectionTransformServiceError",<br>"komunikat": "żądanie transferu danych jest niedozwolone <br>w przypadku obniżenia poziomu do mniej bezpiecznego schematu ochrony danych. Zapoznaj się z dokumentacją lub skontaktuj się z administratorem usługi <br>Aby uzyskać szczegółowe informacje "}]    | Występuje w przypadku kopiowania między `AEK` włączonym zasobem a `AEK` niewłączonym zasobem. Aby umożliwić kopiowanie zaszyfrowanego modelu do elementu docelowego jako niezaszyfrowany, określ `x-ms-forms-copy-degrade: true` Nagłówek z żądaniem kopiowania.|
|"błędy": [{"Code": "ResourceResolverError",<br>"komunikat": "nie można pobrać informacji o zasobie poznawczym o identyfikatorze"... ". Upewnij się, że zasób jest prawidłowy i istnieje w określonym regionie "westus2".. "}] | Wskazuje, że zasób platformy Azure wskazany przez `targetResourceId` nie jest prawidłowym zasobem poznawczym lub nie istnieje. Sprawdź i wydaj ponownie żądanie Copy, aby rozwiązać ten problem.|


### <a name="optional-track-the-target-model-id"></a>Obowiązkowe Śledzenie identyfikatora modelu docelowego 

Możesz również użyć interfejsu API **uzyskiwania niestandardowego modelu** do śledzenia stanu operacji, badając model docelowy. Wywołaj ten interfejs API przy użyciu identyfikatora modelu docelowego skopiowanego w pierwszym kroku.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

W treści odpowiedzi zobaczysz informacje o modelu. Sprawdź `"status"` pole pod kątem stanu modelu.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>zazwinięcie przykładowego kodu

Poniższe fragmenty kodu wykorzystują zwinięcie, aby wywołania interfejsu API były opisane w powyższych krokach. Nadal musisz podać identyfikatory modelu i informacje o subskrypcji dotyczące własnych zasobów.

### <a name="generate-copy-authorization-request"></a>Generuj żądanie autoryzacji kopiowania

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>Rozpocznij operację kopiowania

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>Śledź postęp kopiowania

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku pokazano, jak za pomocą interfejsu API kopiowania utworzyć kopię zapasową własnych modeli w ramach pomocniczego zasobu aparatu rozpoznawania. Następnie zapoznaj się z dokumentacją interfejsu API, aby zobaczyć, co jeszcze można zrobić z aparatem rozpoznawania formularzy.
* [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
