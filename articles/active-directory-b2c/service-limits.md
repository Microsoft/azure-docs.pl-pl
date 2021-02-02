---
title: Limity i ograniczenia usługi Azure AD B2C
titleSuffix: Azure AD B2C
description: Informacje o limitach i ograniczeniach usługi dla usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/02/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 92652c33e261f08e0cb1c8eb57c66d363801f34e
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475070"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Limity i ograniczenia usługi Azure Active Directory B2C

Ten artykuł zawiera ograniczenia użycia i inne limity usługi dla usługi Azure Active Directory B2C (Azure AD B2C).

## <a name="end-userconsumption-related-limits"></a>Limity dotyczące użytkowników końcowych/zużycia

Następujące limity usług związanych z użytkownikiem końcowym dotyczą wszystkich protokołów uwierzytelniania i autoryzacji obsługiwanych przez Azure AD B2C, w tym SAML, Open ID Connect, OAuth2 i ROPC.

|Kategoria |Limit    |
|---------|---------|
|Liczba żądań na adres IP dla dzierżawy Azure AD B2C       |6000/5 min          |
|Łączna liczba żądań na Azure AD B2C dzierżawcę     |12 000/min          |

Liczba żądań może się różnić w zależności od liczby odczytów katalogów i zapisów występujących podczas podróży użytkownika Azure AD B2C. Na przykład prosta podróż logowania, która odczytuje z katalogu, składa się z 1 żądania. Jeśli podróż logowania musi także aktualizować katalog, ta operacja jest traktowana jako dodatkowe żądanie.

## <a name="azure-ad-b2c-configuration-limits"></a>Limity konfiguracji Azure AD B2C

W poniższej tabeli wymieniono limity konfiguracji administracyjnej w usłudze Azure AD B2C.

|Kategoria  |Limit  |
|---------|---------|
|Liczba aplikacji na Azure AD B2C dzierżawcę   |250           |
|Liczba zakresów na aplikację        |1000          |
|Liczba [atrybutów niestandardowych](user-profile-attributes.md#extension-attributes)   na użytkownika <sup>1</sup>       |100         |
|Liczba adresów URL przekierowania na aplikację       |100         |
|Liczba adresów URL wylogowania na aplikację        |1          |
|Limit ciągu dla atrybutu      |250 znaków          |
|Liczba dzierżawców B2C na subskrypcję      |20         |
|Poziomy [dziedziczenia](custom-policy-overview.md#inheritance-model) w zasadach niestandardowych     |10         |
|Liczba zasad dla dzierżawy Azure AD B2C      |200          |
|Maksymalny rozmiar pliku zasad      |400 KB          |

<sup>1</sup> Zobacz również [limity i ograniczenia usługi Azure AD](../active-directory/enterprise-users/directory-service-limits-restrictions.md).

## <a name="next-steps"></a>Następne kroki

- Poznaj [wskazówki dotyczące ograniczania Microsoft Graph](/graph/throttling) 
- Informacje o [różnicach w sprawdzaniu Azure AD B2C aplikacji](../active-directory/develop/supported-accounts-validation.md)













