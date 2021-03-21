---
title: Interfejs API REST usługi Azure App Configuration — nagłówki
description: Strony referencyjne dla nagłówków używanych z interfejsem API REST konfiguracji aplikacji platformy Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932578"
---
# <a name="headers"></a>Nagłówki

Ten artykuł zawiera linki do stron referencyjnych dla nagłówków używanych w interfejsie API REST usługi Azure App Configuration.

## <a name="request-headers"></a>Nagłówki żądań

W poniższej tabeli opisano typowe nagłówki żądań używane w konfiguracji aplikacji platformy Azure.

| Nagłówek | Opis | Przykład |
|--|--|--|
| **Autoryzacja** | Służy do [uwierzytelniania](./rest-api-authentication-index.md) żądania do usługi. Zobacz [sekcję 14,8](https://tools.ietf.org/html/rfc2616#section-14.8) | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **Zaakceptowanie** | Informuje serwer, który typ nośnika zostanie zaakceptowany przez klienta w odpowiedzi HTTP. Zobacz [sekcję 14,1](https://tools.ietf.org/html/rfc2616#section-14.1) | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Accept-DateTime** | Żąda, aby serwer zwracał swoją zawartość jako reprezentację poprzedniego stanu. Wartość tego nagłówka jest żądaną datą i godziną tego stanu. Zobacz [dokument RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Typ zawartości** | Zawiera typ nośnika zawartości w treści żądania HTTP. Zobacz [sekcję 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Data** | Data i godzina wystawienia żądania HTTP. Ten nagłówek jest używany w [uwierzytelnianiu HMAC](./rest-api-authentication-hmac.md). Zobacz [sekcję 14,18](https://tools.ietf.org/html/rfc2616#section-14.18) | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Host** | Określa dzierżawcę, dla którego żądanie zostało wystawione. Ten nagłówek jest używany w [uwierzytelnianiu HMAC](./rest-api-authentication-hmac.md). Zobacz [sekcję 14,23](https://tools.ietf.org/html/rfc2616#section-14.23) | `Host: contoso.azconfig.io` |
| **If-Match** | Używane do realizacji żądania HTTP warunkowego. To żądanie powinno zakończyć się powodzeniem tylko wtedy, gdy element ETag zamierzonego zasobu jest zgodny z wartością tego nagłówka. Wartość "*" pasuje do dowolnego elementu ETag. Zobacz [sekcję 14,24](https://tools.ietf.org/html/rfc2616#section-14.24) | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | Używane do realizacji żądania HTTP warunkowego. To żądanie powinno zakończyć się powodzeniem tylko wtedy, gdy element ETag zamierzonego zasobu nie jest zgodny z wartością tego nagłówka. Wartość "*" pasuje do dowolnego elementu ETag. Zobacz [sekcję 14,26](https://tools.ietf.org/html/rfc2616#section-14.26) | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Synchronizacja — token** | Służy do włączania spójności w czasie rzeczywistym podczas sekwencji żądań. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-MS-Client-Request-ID** | Unikatowy identyfikator dostarczony przez klienta używany do śledzenia rundy żądania. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-MS-Content-SHA256** | SHA256 podsumowanie treści żądania HTTP. Ten nagłówek jest używany w [uwierzytelnianiu HMAC](./rest-api-authentication-hmac.md). | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-MS-Date** | Ten nagłówek może zostać ustawiony i użyty zamiast `Date` nagłówka, jeśli nie można uzyskać dostępu do nagłówka daty. Ten nagłówek jest używany w [uwierzytelnianiu HMAC](./rest-api-authentication-hmac.md). | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-MS-Return-Client-Request-ID** | Używane w połączeniu z `x-ms-client-request-id` nagłówkiem. Jeśli wartość tego nagłówka jest równa "true", serwer zostanie polecić zwrócenie wartości `x-ms-client-request-id` nagłówka żądania. | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>Nagłówki odpowiedzi

Serwer może zawierać następujące nagłówki HTTP w odpowiedziach.

| Nagłówek | Opis | Przykład |
|--|--|--|
| **Typ zawartości** | Zawiera typ nośnika zawartości w treści odpowiedzi HTTP. Zobacz [sekcję 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Element ETag** | Nieprzezroczysty token reprezentujący stan danego zasobu. Może być używany w operacjach warunkowych. Zobacz [sekcję 14,19](https://tools.ietf.org/html/rfc2616#section-14.19) | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Ostatnio modyfikowane** | Opisuje, kiedy żądany zasób został ostatnio zmodyfikowany. Sformatowana jako [http-Date](https://tools.ietf.org/html/rfc2616#section-3.3.1). Zobacz [sekcję 14,29](https://tools.ietf.org/html/rfc2616#section-14.29) | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **Link** | Zawiera linki do zasobów związanych z odpowiedzią. Ten nagłówek jest używany na potrzeby stronicowania przy użyciu _następnego_ linku. Zobacz [dokument RFC 5988](https://tools.ietf.org/html/rfc5988) | `Link: </kv?after={token}>; rel="next"` |
| **Pamiątki — Data i godzina** | Wskazuje, że zawartość znajdująca się w odpowiedzi reprezentuje poprzedni stan. Wartość tego nagłówka jest datą i godziną tego stanu. Zobacz [dokument RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **retry-After-MS** | Zapewnia sugerowany okres (w milisekundach) oczekiwania przez klienta przed ponowną próbą żądania zakończonego niepowodzeniem. | `retry-after-ms: 10` |
| **x-MS-Request-ID** | Unikatowy identyfikator wygenerowany przez serwer, który jest używany do śledzenia żądania w ramach usługi. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW — uwierzytelnianie** | Służy do sprawdzania klientów w celu uwierzytelnienia i zapewnienia przyczyny niepowodzenia próby uwierzytelnienia. Zobacz [sekcję 14,47](https://tools.ietf.org/html/rfc2616#section-14.47) | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
