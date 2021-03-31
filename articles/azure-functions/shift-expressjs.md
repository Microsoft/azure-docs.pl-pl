---
title: Przesunięcia z Express.js do Azure Functions
description: Dowiedz się, jak Refaktoryzacja Express.js punkty końcowe, aby Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87810228"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>Przesunięcia z Express.js do Azure Functions

Express.js jest jednym z najpopularniejszych struktur Node.js dla deweloperów sieci Web i pozostaje doskonałym wyborem do kompilowania aplikacji, które obsługują punkty końcowe interfejsu API.

Podczas migrowania kodu do architektury bezserwerowej, Refaktoryzacja Express.js punkty końcowe ma wpływ na następujące obszary:

- **Oprogramowanie pośredniczące**: Express.js funkcje są niezawodną kolekcją oprogramowania pośredniczącego. Wiele modułów oprogramowania pośredniczącego nie jest już wymaganych w przypadku Azure Functions i możliwości [API Management platformy Azure](../api-management/api-management-key-concepts.md) . Upewnij się, że możesz replikować lub zamienić każdą logikę obsłużoną przez podstawowe oprogramowanie pośredniczące przed migracją punktów końcowych

- **Różne interfejsy API**: interfejs API służący do przetwarzania żądań i odpowiedzi różni się między Azure Functions i Express.js. Poniższy przykład zawiera szczegółowe informacje dotyczące wymaganych zmian.

- **Domyślna trasa**: Domyślnie punkty końcowe Azure Functions są ujawniane w ramach `api` trasy. Reguły routingu można konfigurować za pomocą [ `routePrefix` programu w _host.js_ pliku](./functions-bindings-http-webhook-output.md#hostjson-settings).

- **Konfiguracja i konwencje**: aplikacja Functions używa _function.jsw_ pliku do definiowania czasowników HTTP, definiowania zasad zabezpieczeń i może konfigurować [dane wejściowe i wyjściowe](./functions-triggers-bindings.md)funkcji. Domyślnie nazwa folderu, w którym znajdują się pliki funkcji, definiuje nazwę punktu końcowego, ale można zmienić nazwę za pomocą `route` właściwości w [function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) pliku.

> [!TIP]
> Dowiedz się więcej za pomocą refaktoryzacji interaktywnego samouczka [Node.js i interfejsów API Express do interfejsów API Bezserwerowych z Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/).

## <a name="example"></a>Przykład

### <a name="expressjs"></a>Express.js

W poniższym przykładzie przedstawiono typowy Express.js `GET` punkt końcowy.

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

Gdy `GET` żądanie jest wysyłane do `/hello` , `HTTP 200` zwracana jest odpowiedź zawierająca `Success` . Jeśli punkt końcowy napotka błąd, odpowiedź jest `HTTP 500` ze szczegółowymi informacjami o błędzie.

### <a name="azure-functions"></a>Azure Functions

Azure Functions organizuje pliki konfiguracji i kodu w jeden folder dla każdej funkcji. Domyślnie nazwa folderu wymusza nazwę funkcji.

Na przykład funkcja o nazwie `hello` ma folder o następujących plikach.

``` files
| - hello
|  - function.json
|  - index.js
```

Poniższy przykład implementuje ten sam wynik, jak powyżej Express.js punkt końcowy, ale z Azure Functions.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

Podczas przechodzenia do funkcji są wprowadzane następujące zmiany:

- **Moduł:** Kod funkcji jest implementowany jako moduł JavaScript.

- **Obiekt kontekstu i odpowiedzi**: [`context`](./functions-reference-node.md#context-object) umożliwia komunikowanie się ze środowiskiem uruchomieniowym funkcji. Z poziomu kontekstu można odczytać dane żądania i ustawić odpowiedź funkcji. Kod synchroniczny wymaga wywołania `context.done()` do ukończenia wykonywania, podczas gdy `asyc` funkcje rozwiązują żądanie niejawnie.

- **Konwencja nazewnictwa**: nazwa folderu używana do przechowywania plików Azure Functions jest domyślnie używana jako nazwa punktu końcowego (można ją zastąpić w [function.jsna](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)).

- **Konfiguracja**: należy zdefiniować zlecenia HTTP w [function.jsw](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) pliku, takim jak `POST` lub `PUT` .

Poniższy _function.js_ pliku zawiera informacje o konfiguracji funkcji.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Definiując `get` w `methods` tablicy, funkcja jest dostępna dla `GET` żądań HTTP. Jeśli chcesz, aby interfejs API akceptował żądania obsługi `POST` , możesz `post` również dodać do tablicy.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej za pomocą refaktoryzacji interaktywnego samouczka [Node.js i interfejsów API Express do Bezserwerowych interfejsów API z Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/)