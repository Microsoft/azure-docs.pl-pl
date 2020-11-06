---
title: Ograniczenie interfejsu API REST usługi Azure App Configuration
description: Strony referencyjne do ustalenia ograniczenia przepustowości podczas korzystania z interfejsu API REST usługi Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 26464138b7739bb476d9d0dd1078194473ecd23e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424268"
---
# <a name="throttling"></a>Ograniczanie przepływności

Magazyny konfiguracji mają limity dotyczące żądań, które mogą być używane przez program. Wszystkie żądania, które przekraczają przydzielony przydział dla magazynu konfiguracji, otrzymają odpowiedź HTTP 429 (zbyt wiele żądań).

Ograniczanie przepustowości jest podzielone na różne zasady przydziału:

- **Łączne żądania** — całkowita liczba żądań
- **Całkowita przepustowość** — dane wychodzące w bajtach
- **Magazyn** — łączny rozmiar magazynu danych użytkownika w bajtach

## <a name="handling-throttled-responses"></a>Obsługa odpowiedzi z ograniczeniami

Po osiągnięciu limitu szybkości dla danego przydziału serwer będzie odpowiadał na dalsze żądania tego typu z kodem stanu _429_ . Odpowiedź _429_ będzie zawierać nagłówek _ponawiania próby-MS_ zapewniający klientowi sugerowany czas oczekiwania (w milisekundach), aby zezwolić na uzupełnienie limitu przydziału żądania.

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

W powyższym przykładzie klient przekroczył swój dozwolony limit przydziału i zaleca się spowolnienie i poczeka 10 milisekund przed podjęciem dalszych żądań. Klienci powinni również rozważyć wycofywania progresywne.

## <a name="other-retry"></a>Inne ponowienie

Usługa może identyfikować sytuacje inne niż ograniczenie wydajności, które wymagają ponowienia próby klienta (np. Usługa 503 jest niedostępna). We wszystkich takich przypadkach `retry-after-ms` zostanie podany nagłówek odpowiedzi. Aby zwiększyć niezawodność, zaleca się, aby klient przestrzegał sugerowanego interwału i wykonał ponowną próbę.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
