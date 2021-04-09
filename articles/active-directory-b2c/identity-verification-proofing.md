---
title: Sprawdzanie tożsamości i weryfikacja dla Azure AD B2C
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o naszych partnerach, którzy integrują się z usługą Azure AD B2C w celu zapewnienia weryfikacji tożsamości i rozwiązań weryfikacyjnych
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/23/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: d6e553e93d97f02c618fa33a41b939f90e9042c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027686"
---
# <a name="identity-verification-and-proofing-partners"></a>Weryfikacja tożsamości i partnerzy sprawdzający

Dzięki Azure AD B2C partnerom klienci mogą włączyć weryfikację tożsamości i sprawdzanie ich użytkowników końcowych przed zezwoleniem na rejestrację lub dostęp do konta. Weryfikacja tożsamości i sprawdzanie poprawności mogą sprawdzić dokument, informacje oparte na wiedzy oraz ich stan.

Diagram architektury wysokiego poziomu objaśnia przepływ.

![Diagram przedstawia przepływ weryfikacji tożsamości](./media/partner-gallery/third-party-identity-proofing.png)

Partnerzy firmy Microsoft z następującymi partnerami niezależnych dostawców oprogramowania.

| Partner niezależnego dostawcy oprogramowania | Wskazówki dotyczące opisu i integracji |
|:-------------------------|:--------------|
|![Zrzut ekranu przedstawiający logo Experian.](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) jest weryfikacją tożsamości i dostawcą sprawdzającym, który przeprowadza ocenę ryzyka na podstawie atrybutów użytkownika w celu zapobiegania oszustwom. |
|![Zrzut ekranu przedstawiający logo IDology.](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) to weryfikacja tożsamości i dostawca weryfikacji z rozwiązaniami weryfikacji identyfikatora, rozwiązaniami zapobiegania oszustwom, rozwiązaniami zgodności i innymi.|
|![Zrzut ekranu przedstawiający logo Jumio.](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) to usługa weryfikacji identyfikatora, która umożliwia automatyczne weryfikację identyfikatora w czasie rzeczywistym, chroniąc dane klientów. |
| ![Zrzut ekranu przedstawiający logo LexisNexis.](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) jest dostawcą profilowania i weryfikacji tożsamości, który weryfikuje tożsamość użytkownika i zapewnia kompleksową ocenę ryzyka opartą na urządzeniu użytkownika. |
| ![Zrzut ekranu przedstawiający logo Onfido](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) to identyfikator dokumentu oraz rozwiązanie do weryfikacji twarzy biometryczne, które umożliwia firmom zaspokajanie wymagań *klienta* i tożsamości w czasie rzeczywistym.  |

## <a name="additional-information"></a>Dodatkowe informacje

- [Zasady niestandardowe w usłudze Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)

## <a name="next-steps"></a>Następne kroki

Wybierz partnera w powyższych tabelach, aby dowiedzieć się, jak zintegrować swoje rozwiązanie z Azure AD B2C.
