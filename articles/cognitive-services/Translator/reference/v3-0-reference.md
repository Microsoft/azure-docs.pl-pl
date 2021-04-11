---
title: Dokumentacja usługi Translator w wersji 3.0
titleSuffix: Azure Cognitive Services
description: Dokumentacja dotycząca usługi Translator w wersji 3.0. Wersja 3 usługi Translator udostępnia nowoczesny internetowy interfejs API oparty na notacji JSON.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 8/11/2020
ms.author: lajanuar
ms.openlocfilehash: 2b391c5a435c2dd2f19a3f170bf7c84edd7143f2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063036"
---
# <a name="translator-v30"></a>Translator — wersja 3.0

## <a name="whats-new"></a>Co nowego

Wersja 3 usługi Translator udostępnia nowoczesny internetowy interfejs API oparty na notacji JSON. Zwiększa użyteczność i wydajność dzięki konsolidacji istniejących funkcji do mniejszej liczby operacji i udostępnia nowe funkcje.

 * Przekształć w celu przekonwertowania tekstu w jednym języku z jednego skryptu na inny.
 * Tłumaczenie na wiele języków w jednym żądaniu.
 * Wykrywanie, tłumaczenia i translitrowanie języka w jednym żądaniu.
 * Słownik do wyszukiwania alternatywnych tłumaczeń terminu, aby znaleźć zwroty i przykłady pokazujące terminy używane w kontekście.
 * Dokładniejsze wyniki wykrywania języka.

## <a name="base-urls"></a>Podstawowe adresy URL

Usługi Microsoft Translator są obsługiwane z wielu lokalizacji centrów danych. Obecnie znajdują się one w 10 [lokalizacje geograficzne Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Ameryka Północna:** Wschodnie stany USA, Południowo-środkowe stany USA, zachodnio-środkowe stany USA i zachodnie stany USA 2 
* **Azja i Pacyfik:** Korea Południowa, Japonia Wschodnia, Azja Południowo-Wschodnia i Australia Wschodnia
* **Europa:** Europa Północna, Europa Zachodnia

Żądania kierowane do usługi Microsoft Translator są w większości przypadków obsługiwane przez centrum danych, które znajdują się najbliżej lokalizacji, z której pochodzi żądanie. W przypadku awarii centrum danych żądanie może być kierowane poza lokalizację geograficzną platformy Azure.

Aby wymusić obsługę żądania przez określoną lokalizację geograficzną platformy Azure, Zmień globalny punkt końcowy w żądaniu interfejsu API na żądany geograficzny punkt końcowy:

|Opis|Lokalizacja geograficzna platformy Azure|Podstawowy adres URL (geograficzny punkt końcowy)|
|:--|:--|:--|
|Azure|Globalne (nieregionalne)|   api.cognitive.microsofttranslator.com|
|Azure|Stany Zjednoczone|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Azja i Pacyfik|    api-apc.cognitive.microsofttranslator.com|

<sup>1</sup> klienci z zasobem znajdującym się w Szwajcaria Północna lub Szwajcaria Zachodnia mogą zapewnić, że ich żądania interfejsu API tekstu są obsługiwane w Szwajcarii. Aby upewnić się, że żądania są obsługiwane w Szwajcarii, należy utworzyć zasób usługi Translator w obszarze zasobów "Szwajcaria Północna" lub "Szwajcaria Zachodnia", a następnie użyć niestandardowego punktu końcowego zasobu w żądaniach interfejsu API. Na przykład: Jeśli utworzysz zasób usługi Translator w Azure Portal z "regionem zasobów" jako "Szwajcaria Północna", a nazwa zasobu to "My-ch-n", niestandardowy punkt końcowy to " https://my-ch-n.cognitiveservices.azure.com ". A przykładowe żądanie przetłumaczenia to:
```curl
// Pass secret key and region using headers to a custom endpoint
curl -X POST " my-ch-n.cognitiveservices.azure.com/translator/text/v3.0/translate?to=fr" \
-H "Ocp-Apim-Subscription-Key: xxx" \
-H "Ocp-Apim-Subscription-Region: switzerlandnorth" \
-H "Content-Type: application/json" \
-d "[{'Text':'Hello'}]" -v
```
<sup>2</sup> niestandardowy Translator nie jest obecnie dostępny w Szwajcarii.

## <a name="authentication"></a>Authentication

Zasubskrybuj usługę translator lub [Cognitive Services wiele usług](https://azure.microsoft.com/pricing/details/cognitive-services/) na platformie Azure Cognitive Services i Użyj klucza subskrypcji (dostępnego w Azure Portal) do uwierzytelniania. 

Istnieją trzy nagłówki, za pomocą których można uwierzytelniać swoją subskrypcję. W tej tabeli opisano, jak są używane:

|Nagłówki|Opis|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Jeśli przekazujesz klucz tajny, Użyj usługi z subskrypcją Cognitive Services*.<br/>Wartość to klucz tajny platformy Azure dla Twojej subskrypcji w usłudze translator.|
|Autoryzacja|*Użyj usługi z subskrypcją Cognitive Services, jeśli przekazujesz token uwierzytelniania.*<br/>Wartość jest tokenem okaziciela: `Bearer <token>` .|
|OCP-APIM-Subscription-region|*Używany z Cognitive Servicesm zasobów usługi Service i translatora regionalnego.*<br/>Wartość to region zasobu usługi wielousługowej lub translatora regionalnego. Ta wartość jest opcjonalna w przypadku korzystania z zasobu usługi tłumaczenia globalnego.|

###  <a name="secret-key"></a>Klucz tajny
Pierwsza opcja polega na uwierzytelnianiu przy użyciu `Ocp-Apim-Subscription-Key` nagłówka. Dodaj `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` nagłówek do żądania.

#### <a name="authenticating-with-a-global-resource"></a>Uwierzytelnianie za pomocą zasobu globalnego

W przypadku korzystania z [zasobu usługi tłumaczenia globalnego](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)należy dołączyć jeden nagłówek, aby wywołać translator.

|Nagłówki|Opis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Wartość to klucz tajny platformy Azure dla Twojej subskrypcji w usłudze translator.|

Oto przykładowe żądanie wywołania translatora przy użyciu zasobu usługi tłumaczenia globalnego

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Uwierzytelnianie za pomocą zasobu regionalnego

W przypadku korzystania z [zasobu translatora regionalnego](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).
Istnieją 2 nagłówki, które należy wywołać translator.

|Nagłówki|Opis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Wartość to klucz tajny platformy Azure dla Twojej subskrypcji w usłudze translator.|
|OCP-APIM-Subscription-region| Wartość to region zasobu translatora. |

Oto przykładowe żądanie wywołania translatora przy użyciu zasobu translatora regionalnego

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Uwierzytelnianie za pomocą zasobu wielousługowego

W przypadku korzystania z zasobu usługi poznawczej. Dzięki temu można używać jednego klucza tajnego do uwierzytelniania żądań dla wielu usług. 

W przypadku korzystania z wielousługowego klucza tajnego należy dołączyć dwa nagłówki uwierzytelniania do żądania. Istnieją 2 nagłówki, które należy wywołać translator.

|Nagłówki|Opis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Wartość jest kluczem tajnym platformy Azure dla zasobu wielousługowego.|
|OCP-APIM-Subscription-region| Wartość to region zasobu wieloskładnikowego. |

Region jest wymagany w przypadku subskrypcji wielousługowego interfejsu API tekstu. Wybrany region jest jedynym regionem, którego można użyć do tłumaczenia tekstu przy użyciu klucza subskrypcji wielousługowej i musi być tym samym regionem wybranym podczas rejestrowania się w ramach subskrypcji wielousługowej za pośrednictwem Azure Portal.

Dostępne regiony to,,,,,,,,,, `australiaeast` `brazilsouth` `canadacentral` `centralindia` `centralus` `centraluseuap` `eastasia` `eastus` `eastus2` `francecentral` `japaneast` , `japanwest` , `koreacentral` , `northcentralus` , `northeurope` , `southcentralus` , `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2` `southafricanorth` ,,,,, i.

Jeśli przekażesz klucz tajny w ciągu zapytania za pomocą parametru `Subscription-Key` , należy określić region z parametrem zapytania `Subscription-Region` .

### <a name="authenticating-with-an-access-token"></a>Uwierzytelnianie przy użyciu tokenu dostępu
Alternatywnie możesz wymienić klucz tajny dla tokenu dostępu. Token ten jest dołączany do każdego żądania jako `Authorization` nagłówek. Aby uzyskać Token autoryzacji, należy `POST` wysłać żądanie do następującego adresu URL:

| Typ zasobu     | Adres URL usługi uwierzytelniania                                |
|-----------------|-----------------------------------------------------------|
| Globalnie          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regionalne lub wielousługowe | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Oto przykładowe żądania uzyskania tokenu danego klucza tajnego:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Pomyślne żądanie zwraca zakodowany token dostępu jako zwykły tekst w treści odpowiedzi. Prawidłowy token jest przesyłany do usługi Translator jako token okaziciela w autoryzacji.

```http
Authorization: Bearer <Base64-access_token>
```

Token uwierzytelniania jest ważny przez 10 minut. Token powinien być ponownie używany podczas wykonywania wielu wywołań translatora. Jeśli jednak program wysyła żądania do translatora w dłuższym okresie, wówczas program musi zażądać nowego tokenu dostępu w regularnych odstępach czasu (na przykład co 8 minut).

## <a name="virtual-network-support"></a>Obsługa sieci wirtualnej

Usługa Translator jest teraz dostępna z możliwościami Virtual Network (VNET) we wszystkich regionach chmury publicznej platformy Azure. Aby włączyć Virtual Network, zobacz [Konfigurowanie platformy Azure Cognitive Services sieci wirtualnych](../../cognitive-services-virtual-networks.md?tabs=portal). 

Po włączeniu tej funkcji musisz użyć niestandardowego punktu końcowego, aby wywołać translator. Nie można użyć globalnego punktu końcowego usługi Translator ("api.cognitive.microsofttranslator.com") i nie można uwierzytelnić się przy użyciu tokenu dostępu.

Niestandardowy punkt końcowy można znaleźć po utworzeniu [zasobu usługi Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) i umożliwieniu dostępu z wybranych sieci i prywatnych punktów końcowych.

|Nagłówki|Opis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Wartość to klucz tajny platformy Azure dla Twojej subskrypcji w usłudze translator.|
|OCP-APIM-Subscription-region| Wartość to region zasobu translatora. Ta wartość jest opcjonalna, jeśli zasób jest `global`|

Oto przykładowe żądanie wywołania translatora przy użyciu niestandardowego punktu końcowego

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>błędy

Standardowa odpowiedź na błąd to obiekt JSON z parą nazwa/wartość o nazwie `error` . Wartość jest również obiektem JSON z właściwościami:

  * `code`: Kod błędu zdefiniowany przez serwer.
  * `message`: Ciąg dający czytelny dla człowieka reprezentację błędu.

Na przykład klient z subskrypcją bezpłatnej wersji próbnej otrzyma następujący błąd po wyczerpaniu limitu przydziału:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Kod błędu to 6-cyfrowy numer łączący 3-cyfrowy kod stanu HTTP, a następnie 3-cyfrowy numer do dalszej kategoryzacji błędu. Typowe kody błędów to:

| Kod | Opis |
|:----|:-----|
| 400000| Jeden z danych wejściowych żądania jest nieprawidłowy.|
| 400001| Parametr "Scope" jest nieprawidłowy.|
| 400002| Parametr "Category" jest nieprawidłowy.|
| 400003| Brak specyfikatora języka lub jest on nieprawidłowy.|
| 400004| Brak specyfikatora skryptu docelowego ("do skryptu") lub jest on nieprawidłowy.|
| 400005| Brak tekstu wejściowego lub jest on nieprawidłowy.|
| 400006| Kombinacja języka i skryptu jest nieprawidłowa.|
| 400018| Brak specyfikatora skryptu źródłowego ("ze skryptu") lub jest on nieprawidłowy.|
| 400019| Jeden z określonych języków nie jest obsługiwany.|
| 400020| Jeden z elementów w tablicy tekstu wejściowego jest nieprawidłowy.|
| 400021| Brak parametru wersji interfejsu API lub jest on nieprawidłowy.|
| 400023| Jedna z określonych par języków jest nieprawidłowa.|
| 400035| Nieprawidłowy język źródłowy ("from").|
| 400036| Brak języka docelowego (pole "do") lub jest on nieprawidłowy.|
| 400042| Jedna z opcji określonych w polu "Opcje" jest nieprawidłowa.|
| 400043| Brak identyfikatora śledzenia klienta (pole ClientTraceId lub nagłówek X-ClientTranceId) lub jest on nieprawidłowy.|
| 400050| Tekst wejściowy jest zbyt długi. Wyświetlanie [limitów żądań](../request-limits.md).|
| 400064| Brak parametru "translation" lub jest on nieprawidłowy.|
| 400070| Liczba skryptów docelowych (parametr ToScript) nie jest zgodna z liczbą języków docelowych (do parametru).|
| 400071| Wartość jest nieprawidłowa dla typu texttype.|
| 400072| Tablica tekstu wejściowego ma zbyt wiele elementów.|
| 400073| Parametr skryptu jest nieprawidłowy.|
| 400074| Treść żądania nie jest prawidłowym kodem JSON.|
| 400075| Para językowa i kombinacja kategorii są nieprawidłowe.|
| 400077| Przekroczono maksymalny rozmiar żądania. Wyświetlanie [limitów żądań](../request-limits.md).|
| 400079| System niestandardowy żądany do tłumaczenia między językiem i a nie istnieje.|
| 400080| Narzędzie transliteracji nie jest obsługiwane dla języka lub skryptu.|
| 401000| Żądanie nie jest autoryzowane, ponieważ brakuje poświadczeń lub są one nieprawidłowe.|
| 401015| "Podane poświadczenia dotyczą Speech API. To żądanie wymaga poświadczeń dla interfejsu API tekstu. Użyj subskrypcji usługi Translator.|
| 403000| Operacja jest niedozwolona.|
| 403001| Operacja jest niedozwolona, ponieważ subskrypcja przekroczyła bezpłatny limit przydziału.|
| 405000| Metoda Request nie jest obsługiwana dla żądanego zasobu.|
| 408001| Żądany system tłumaczenia jest przygotowywany. Spróbuj ponownie za kilka minut.|
| 408002| Upłynął limit czasu żądania podczas oczekiwania na strumień przychodzący. Klient nie wygenerował żądania w czasie, gdy serwer został przygotowany do oczekiwania. Klient może powtórzyć żądanie bez modyfikacji w późniejszym czasie.|
| 415000| Brak nagłówka Content-Type lub jest on nieprawidłowy.|
| 429000, 429001, 429002| Serwer odrzucił żądanie z powodu przekroczenia limitów żądań przez klienta.|
| 500000| Wystąpił nieoczekiwany błąd. Jeśli błąd będzie się powtarzać, zgłoś ten błąd w polu Data/godzina błędu, identyfikator żądania z nagłówka odpowiedzi X-IdentyfikatorŻądania i identyfikator klienta z nagłówka żądania X-ClientTraceId.|
| 503000| Usługa jest tymczasowo niedostępna. Spróbuj ponownie. Jeśli błąd będzie się powtarzać, zgłoś ten błąd w polu Data/godzina błędu, identyfikator żądania z nagłówka odpowiedzi X-IdentyfikatorŻądania i identyfikator klienta z nagłówka żądania X-ClientTraceId.|

## <a name="metrics"></a>Metryki 
Metryki umożliwiają wyświetlanie informacji dotyczących użycia i dostępności usługi Translator w Azure Portal w obszarze metryki, jak pokazano na poniższym zrzucie ekranu. Aby uzyskać więcej informacji, zobacz [metryki danych i platformy](../../../azure-monitor/essentials/data-platform-metrics.md).

![Metryki translatora](../media/translatormetrics.png)

Ta tabela zawiera listę dostępnych metryk z opisem sposobu ich używania do monitorowania wywołań interfejsu API translacji.

| Metryki | Opis |
|:----|:-----|
| TotalCalls| Łączna liczba wywołań interfejsu API.|
| TotalTokenCalls| Łączna liczba wywołań interfejsu API za pośrednictwem usługi tokenu przy użyciu tokenu uwierzytelniania.|
| SuccessfulCalls| Liczba pomyślnych wywołań.|
| TotalErrors| Liczba wywołań z odpowiedzią na błędy.|
| BlockedCalls| Liczba wywołań, które przekroczyły limit szybkości lub limitu przydziału.|
| Błędy servererrors| Liczba wywołań z błędem wewnętrznym serwera (5XX).|
| ClientErrors| Liczba wywołań z błędem po stronie klienta (4XX).|
| Opóźnienie| Czas trwania żądania w milisekundach.|
| CharactersTranslated| Całkowita liczba znaków w żądaniu tekstu przychodzącego.|
