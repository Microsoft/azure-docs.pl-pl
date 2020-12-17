---
title: Obsługa dużych komunikatów przy użyciu fragmentów
description: Dowiedz się, jak obsługiwać duże rozmiary komunikatów przy użyciu fragmentów w zautomatyzowanych zadaniach i przepływach pracy utworzonych przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
author: DavidCBerry13
ms.author: daberry
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 1b23c92ec70b80a6cd08fc42a05ffec1e5b43b31
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656771"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Obsługa dużych komunikatów z fragmentacją w Azure Logic Apps

W przypadku obsługi komunikatów Logic Apps ogranicza zawartość komunikatów do maksymalnego rozmiaru. Ten limit ułatwia zmniejszenie obciążenia utworzonego przez przechowywanie i przetwarzanie dużych komunikatów. Aby obsłużyć komunikaty większe niż ten limit, *Logic Apps może podzielić dużą wiadomość na mniejsze* wiadomości. W ten sposób można nadal transferować duże pliki przy użyciu Logic Apps w określonych warunkach. Podczas komunikowania się z innymi usługami za pośrednictwem łączników lub protokołu HTTP Logic Apps może zużywać duże wiadomości, ale *tylko* w fragmentach. Ten stan oznacza, że łączniki muszą również obsługiwać rozdzielenie lub podstawową wymianę komunikatów HTTP między Logic Apps i tymi usługami muszą używać fragmentowania.

W tym artykule pokazano, jak można skonfigurować rozdzielenie w celu obsługi komunikatów, które są większe niż limit. Wyzwalacze aplikacji logiki nie obsługują fragmentacji ze względu na zwiększone obciążenie związane z wymianą wielu komunikatów. 

## <a name="what-makes-messages-large"></a>Co sprawia, że komunikaty są duże?

Komunikaty są "duże" na podstawie obsługi tych komunikatów. Dokładny limit rozmiaru dużych komunikatów różni się między Logic Apps i łącznikami. Zarówno Logic Apps, jak i łączniki nie mogą bezpośrednio zużywać dużych komunikatów, które muszą być częścią. Limit rozmiaru komunikatu Logic Apps można znaleźć w temacie [Logic Apps limitów i konfiguracji](../logic-apps/logic-apps-limits-and-config.md).
Dla każdego limitu rozmiaru komunikatu dla każdego łącznika Sprawdź [szczegółowe informacje techniczne dotyczące łącznika](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Obsługa komunikatów fragmentarycznych dla Logic Apps

Logic Apps nie może bezpośrednio użyć danych wyjściowych z fragmentów komunikatów, które są większe niż limit rozmiaru wiadomości. Tylko akcje obsługujące fragmentację mogą uzyskiwać dostęp do zawartości wiadomości w tych danych wyjściowych. Dlatego Akcja, która obsługuje duże komunikaty, *musi spełniać następujące* kryteria:

* Natywnie Obsługuj rozdzielenie, gdy ta akcja należy do łącznika. 
* W konfiguracji środowiska uruchomieniowego tej akcji włączono obsługę fragmentów. 

W przeciwnym razie podczas próby uzyskania dostępu do danych wyjściowych dużej zawartości zostanie wyświetlony błąd czasu wykonywania. Aby włączyć rozdzielenie, zobacz [Konfigurowanie obsługi fragmentów](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Obsługa komunikatów fragmentarycznych dla łączników

Usługi, które komunikują się z Logic Apps mogą mieć własne limity rozmiaru komunikatów. Limity te są często mniejsze niż limit Logic Apps. Na przykład przy założeniu, że łącznik obsługuje dzielenie, łącznik może traktować komunikat o rozmiarze 30 MB jako duży, natomiast Logic Apps nie. Aby zachować zgodność z limitem tego łącznika, Logic Apps dzieli każdy komunikat większy niż 30 MB na mniejsze fragmenty.

W przypadku łączników, które obsługują rozdzielenie, podstawowy protokół fragmentaryczny jest niewidoczny dla użytkowników końcowych. Jednak nie wszystkie łączniki obsługują rozdzielenie, więc te łączniki generują błędy w czasie wykonywania, gdy komunikaty przychodzące przekraczają limity rozmiaru łączników.

> [!NOTE]
> W przypadku akcji, które korzystają z fragmentów, nie można przekazać treści wyzwalacza ani używać wyrażeń, takich jak `@triggerBody()?['Content']` w tych akcjach. Zamiast tego dla zawartości pliku tekstowego lub JSON można spróbować użyć [  akcji](../logic-apps/logic-apps-perform-data-operations.md#compose-action) [tworzenia lub utworzyć zmienną](../logic-apps/logic-apps-create-variables-store-values.md) do obsługi tej zawartości. Jeśli treść wyzwalacza zawiera inne typy zawartości, takie jak pliki multimedialne, należy wykonać inne czynności, aby obsłużyć tę zawartość.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Konfigurowanie fragmentowania przy użyciu protokołu HTTP

W przypadku ogólnych scenariuszy HTTP można rozdzielić pobieranie dużych zawartości i przeciążać je za pośrednictwem protokołu HTTP, dzięki czemu aplikacja logiki i punkt końcowy mogą wymieniać duże wiadomości. Należy jednak rozfragmentować komunikaty w sposób, który Logic Apps oczekiwać. 

Jeśli punkt końcowy włączył fragmentowanie do pobrania lub przeładowania, akcje HTTP w aplikacji logiki automatycznie rozłączyją duże wiadomości. W przeciwnym razie należy skonfigurować obsługę fragmentów w punkcie końcowym. Jeśli nie jesteś własnością lub nie kontrolujesz punktu końcowego lub łącznika, możesz nie mieć możliwości skonfigurowania fragmentu.

Ponadto, jeśli akcja HTTP nie umożliwia jeszcze rozdzielenie, należy również skonfigurować fragmentowanie we `runTimeConfiguration` Właściwości akcji. Tę właściwość można ustawić wewnątrz akcji, bezpośrednio w edytorze widoku kodu zgodnie z opisem w dalszej części, lub w projektancie Logic Apps, zgodnie z opisem w tym miejscu:

1. W prawym górnym rogu akcji http wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Ustawienia**.

   ![Na akcji Otwórz menu Ustawienia](./media/logic-apps-handle-large-messages/http-settings.png)

2. W obszarze **transfer zawartości** ustaw opcję **Zezwalaj** na dzielenie na **włączone**.

   ![Włączanie fragmentacji](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Aby kontynuować konfigurowanie fragmentów dla plików do pobrania lub do przeładowania, wykonaj następujące sekcje.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Pobierz zawartość w fragmentach

Wiele punktów końcowych automatycznie wysyła duże wiadomości w fragmentach, gdy są pobierane za pośrednictwem żądania HTTP GET. Aby pobrać fragmenty komunikatów z punktu końcowego za pośrednictwem protokołu HTTP, punkt końcowy musi obsługiwać częściowe żądania zawartości lub *pobrane fragmenty*. Gdy aplikacja logiki wysyła żądanie HTTP GET do punktu końcowego w celu pobrania zawartości, a punkt końcowy odpowiada za pomocą kodu stanu "206", odpowiedź zawiera zawartość fragmentaryczną. Logic Apps nie może kontrolować, czy punkt końcowy obsługuje częściowe żądania. Jeśli jednak aplikacja logiki Pobiera pierwszą odpowiedź "206", aplikacja logiki automatycznie wysyła wiele żądań w celu pobrania całej zawartości.

Aby sprawdzić, czy punkt końcowy może obsługiwać częściową zawartość, Wyślij żądanie główne. To żądanie pozwala określić, czy odpowiedź zawiera `Accept-Ranges` nagłówek. W ten sposób, jeśli punkt końcowy obsługuje pobrane fragmenty, ale nie wysyła zawartości z fragmenty, można *zasugerować* tę opcję przez ustawienie `Range` nagłówka w żądaniu HTTP GET. 

W tych krokach opisano szczegółowy proces Logic Apps używany do pobierania zawartości z punktu końcowego do aplikacji logiki:

1. Aplikacja logiki wysyła żądanie HTTP GET do punktu końcowego.

   Nagłówek żądania może opcjonalnie zawierać `Range` pole, które opisuje zakres bajtów do żądania fragmentów zawartości.

2. Punkt końcowy odpowiada za pomocą kodu stanu "206" i treści wiadomości HTTP.

    Szczegóły dotyczące zawartości w tym fragmencie pojawiają się w `Content-Range` nagłówku odpowiedzi, w tym informacje ułatwiające Logic Apps określenie początku i końca fragmentu oraz łączny rozmiar całej zawartości przed rozdzieleniem.

3. Aplikacja logiki automatycznie wysyła żądania HTTP GET.

    Aplikacja logiki wysyła kolejne żądania GET do momentu pobrania całej zawartości.

Na przykład ta definicja akcji przedstawia żądanie HTTP GET ustawiające `Range` nagłówek. Nagłówek *sugeruje* , że punkt końcowy powinien odpowiedzieć z zawartością fragmentaryczną:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

Żądanie GET ustawia nagłówek "Range" na "Byte = 0-1023", który jest zakresem bajtów. Jeśli punkt końcowy obsługuje żądania dotyczące częściowej zawartości, punkt końcowy odpowie z fragmentem zawartości z żądanego zakresu. W oparciu o punkt końcowy, dokładny format pola nagłówka "Range" może się różnić.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Przekaż zawartość w fragmentach

Aby przekazać fragmentaryczną zawartość z akcji HTTP, Akcja musi mieć włączoną obsługę fragmentarycznych fragmentów przez `runtimeConfiguration` Właściwość akcji. To ustawienie umożliwia działanie uruchamiania protokołu tworzenia fragmentów. Aplikacja logiki może następnie wysłać początkowy komunikat POST lub PUT do docelowego punktu końcowego. Gdy punkt końcowy odpowie na podstawie sugerowanego rozmiaru fragmentu, aplikacja logiki będzie zgodna przez wysyłanie żądań poprawek HTTP zawierających fragmenty zawartości.

W tych krokach opisano szczegółowy proces Logic Apps używany do przekazywania zawartości z aplikacji logiki do punktu końcowego:

1. Aplikacja logiki wysyła początkowe żądanie HTTP POST lub PUT z pustą treścią wiadomości. Nagłówek żądania zawiera te informacje o zawartości, którą aplikacja logiki chce przekazać w fragmentach:

   | Pole nagłówka żądania Logic Apps | Wartość | Typ | Opis |
   |---------------------------------|-------|------|-------------|
   | **x-MS-Transfer-Mode** | podzielony | Ciąg | Wskazuje, że zawartość jest przekazywana w fragmentach |
   | **x-MS-Content-Length** | <*Długość zawartości*> | Liczba całkowita | Cały rozmiar zawartości w bajtach przed fragmentem |
   ||||

2. Punkt końcowy odpowiada za pomocą kodu stanu sukcesu "200" i informacji dodatkowych:

   | Pole nagłówka odpowiedzi punktu końcowego | Typ | Wymagane | Opis |
   |--------------------------------|------|----------|-------------|
   | **x-MS-fragment rozmiaru** | Liczba całkowita | Nie | Sugerowany rozmiar fragmentu w bajtach |
   | **Lokalizacja** | Ciąg | Tak | Lokalizacja adresu URL, w której mają zostać wysłane komunikaty poprawek HTTP |
   ||||

3. Aplikacja logiki tworzy i wysyła komunikaty poprawek protokołu HTTP z monitami o następujące informacje:

   * Fragment zawartości oparty na **rozmiarze x-MS-fragmentu** lub w pewnym rozmiarze wewnętrznie obliczanym do momentu przekazanie całej zawartości **x-MS-Content-Length**

   * Szczegóły tego nagłówka dotyczące fragmentu zawartości wysłanego w każdej wiadomości poprawki:

     | Pole nagłówka żądania Logic Apps | Wartość | Typ | Opis |
     |---------------------------------|-------|------|-------------|
     | **Zakres zawartości** | <*zakresu*> | Ciąg | Zakres bajtów bieżącego fragmentu zawartości, łącznie z wartością początkową, wartością końcową i łącznym rozmiarem zawartości, na przykład: "bajty = 0-1023/10100" |
     | **Typ zawartości** | <*Typ zawartości*> | Ciąg | Typ zawartości fragmentarycznej |
     | **Długość zawartości** | <*Długość zawartości*> | Ciąg | Długość rozmiaru w bajtach bieżącego fragmentu |
     |||||

4. Po każdym żądaniu poprawki punkt końcowy potwierdza odbiór dla każdego fragmentu, odpowiadając na kod stanu "200" i następujące nagłówki odpowiedzi:

   | Pole nagłówka odpowiedzi punktu końcowego | Typ | Wymagane | Opis |
   |--------------------------------|------|----------|-------------|
   | **Zakres** | Ciąg | Tak | Zakres bajtów dla zawartości otrzymanej przez punkt końcowy, na przykład: "bajty = 0-1023" |   
   | **x-MS-fragment rozmiaru** | Liczba całkowita | Nie | Sugerowany rozmiar fragmentu w bajtach |
   ||||

Na przykład ta definicja akcji przedstawia żądanie HTTP POST dotyczące przekazywania fragmentarycznej zawartości do punktu końcowego. We właściwości akcji właściwość `runTimeConfiguration` `contentTransfer` ustawia `transferMode` `chunked` :

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
