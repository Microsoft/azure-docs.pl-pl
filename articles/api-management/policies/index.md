---
title: Przykłady zasad usługi Azure API Management | Microsoft Docs
description: Dowiedz się więcej o zasadach dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b5b8c82aa420b62e3b6e68ee53352eb9f77988f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506693"
---
# <a name="api-management-policy-samples"></a>Przykłady zasad usługi API Management

[Zasady](../api-management-howto-policies.md) są zaawansowaną możliwością systemu, która pozwala wydawcy zmieniać zachowanie interfejsu API za pomocą konfiguracji. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. Poniższa tabela zawiera linki do przykładów i krótki opis każdego przykładu.

| Zasady ruchu przychodzącego | Opis |
| ---------------- | ----------- |
| [Dodawanie nagłówka Forwarded w celu umożliwienia interfejsowi API zaplecza tworzenia odpowiednich adresów URL](./set-header-to-enable-backend-to-construct-urls.md) | Przedstawia sposób dodawania nagłówka Forwarded do żądania przychodzącego w celu umożliwienia interfejsowi API zaplecza tworzenia odpowiednich adresów URL.                                                                                                        |
| [Dodawanie nagłówka zawierającego identyfikator korelacji](./add-correlation-id.md)                                                             | Przedstawia sposób dodawania nagłówka zawierającego identyfikator korelacji do żądania przychodzącego.                                                                                                                                        |
| [Dodawanie możliwości do usługi zaplecza i buforowanie odpowiedzi](./cache-response.md)                                             | Pokazuje sposób dodawania możliwości do usługi zaplecza. Na przykład akceptowania nazwy miejsca zamiast współrzędnych geograficznych w interfejsie API prognozy pogody.                                                                    |
| [Autoryzacja dostępu na podstawie oświadczeń JWT](./authorize-request-based-on-jwt-claims.md)                                              | Pokazuje sposób autoryzowania dostępu do konkretnych metod HTTP w interfejsie API opartym na oświadczeniach JWT.                                                                                                                                       |
| [Autoryzacja żądań za pomocą zewnętrznego obiektu autoryzującego](./authorize-request-using-external-authorizer.md)                                                   | Pokazuje sposób zabezpieczania dostępu do interfejsu API za pomocą zewnętrznego obiektu autoryzującego.                                                                                                                                                               |
| [Autoryzacja dostępu za pomocą tokenu OAuth usługi Google](./use-google-as-oauth-token-provider.md)                                            | Pokazuje sposób autoryzowania dostępu do punktów końcowych przy użyciu usługi Google jako dostawcy tokenów OAuth.                                                                                                                                    |
| [Filtrowanie adresów IP w przypadku używania usługi Application Gateway](./filter-ip-addresses-when-using-appgw.md) | Pokazuje sposób filtrowania protokołu IP w zasadach, gdy dostęp do wystąpienia usługi API Management jest uzyskiwany za pośrednictwem usługi Application Gateway
| [Generowanie sygnatury dostępu współdzielonego i przekazywanie żądania do usługi Azure Storage](./generate-shared-access-signature.md)                  | Pokazuje sposób generowania [sygnatury dostępu współdzielonego](../../storage/common/storage-sas-overview.md) przy użyciu wyrażeń i przekazywania żądania do usługi Azure Storage za pomocą zasad ponownego zapisywania identyfikatorów URI. |
| [Uzyskiwanie tokenu dostępu OAuth2 z usługi AAD i przekazywanie go do zaplecza](./use-oauth2-for-authorization.md)                             | Udostępnia przykład użycia protokołu OAuth2 do autoryzacji między bramą i zapleczem. Pokazuje sposób uzyskiwania tokenu dostępu z usługi AAD i przekazywania go do zaplecza.                                                    |
| [Uzyskiwanie tokenu X-CSRF z bramy SAP przy użyciu zasad żądania wysłania](./get-x-csrf-token-from-sap-gateway.md)                           | Pokazuje sposób implementowania wzorca X-CSRF używanego przez wiele interfejsów API. Ten przykład jest specyficzny dla bramy SAP.                                                                                                                           |
| [Kierowanie żądania na podstawie rozmiaru jego treści](./route-requests-based-on-size.md)                                            | Przedstawia kierowanie żądań na podstawie rozmiaru ich treści.                                                                                                                                                       |
| [Wysyłanie informacji o kontekście żądania do usługi zaplecza](./send-request-context-info-to-backend-service.md)                    | Pokazuje sposób wysyłania pewnych informacji dotyczących kontekstu do usługi zaplecza na potrzeby rejestrowania lub przetwarzania.                                                                                                                                |
| [Ustawianie czasu trwania pamięci podręcznej odpowiedzi](./set-cache-duration.md)                                                                          | Pokazuje sposób ustawiania czasu trwania pamięci podręcznej odpowiedzi za pomocą wartości maxAge nagłówka Cache-Control wysyłanego przez zaplecze.                                                                                                             |
| **Zasady ruchu wychodzącego** | **Opis** |
| [Filtrowanie zawartości odpowiedzi](./filter-response-content.md)                                                                         | Przedstawia sposób filtrowania elementów danych w ładunku odpowiedzi na podstawie produktu skojarzonego z żądaniem.                                                                                                        |
| **Zasady dla wystąpienia błędu** | **Opis** |
| [Rejestrowanie błędów w usłudze Stackify](./log-errors-to-stackify.md)                                                                           | Pokazuje sposób dodawania zasad rejestrowania błędów w celu wysyłania błędów do usługi Stackify na potrzeby rejestrowania.                                                                                                                                            |
