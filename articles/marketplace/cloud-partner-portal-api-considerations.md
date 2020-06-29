---
title: Zagadnienia dotyczące interfejsów API — Azure Marketplace
description: Przechowywanie wersji, obsługa błędów i problemy z autoryzacją w przypadku korzystania z interfejsów API portalu Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 11ec6f9a38c5fe957eba922f3136f4fa0b2c4995
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85516400"
---
# <a name="api-considerations"></a>Zagadnienia dotyczące interfejsów API

<a name="api-versioning"></a>Obsługa wersji interfejsu API
--------------

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany wymienione w [Portal Cloud partner dokumentacja interfejsu API](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie nadal działał po migracji do Centrum partnerskiego.

Może istnieć wiele wersji interfejsu API, które są dostępne w tym samym czasie. Klienci muszą wskazać, która wersja ma być używana przez podanie `api-version` parametru jako części ciągu zapytania.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Odpowiedź na żądanie z nieznaną lub nieprawidłową wersją interfejsu API to kod HTTP 400. Ten błąd zwraca kolekcję znanych wersji interfejsu API w treści odpowiedzi.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Errors
------

Interfejs API reaguje na błędy przy użyciu odpowiednich kodów stanu HTTP i opcjonalnie dodatkowe informacje w przypadku serializacji odpowiedzi jako dane JSON.
Po otrzymaniu błędu, szczególnie błędu klasy 400, nie należy ponawiać próby żądania przed ustaleniem przyczyny podstawowej. Na przykład w powyższej odpowiedzi przykładowo Popraw parametr wersji interfejsu API przed ponownym wysłaniem żądania.

<a name="authorization-header"></a>Nagłówek autoryzacji
--------------------

Dla wszystkich interfejsów API w tym odwołaniu należy przekazać nagłówek autoryzacji wraz z tokenem okaziciela uzyskanym z Azure Active Directory (Azure AD). Ten nagłówek jest wymagany do otrzymania prawidłowej odpowiedzi; Jeśli nie istnieje, `401 Unauthorized` zwracany jest błąd. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
