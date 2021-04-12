---
title: Portal rejestracji samoobsługowej współpracy B2B — Azure AD
description: Dowiedz się, jak dostosować przepływ pracy dołączania dla Azure Active Directory użytkowników B2B, aby dopasować się do potrzeb Twojej organizacji.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a5b800e78448afcc970010535ba12b543d3cc74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860511"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Samoobsługowe rejestrowanie w ramach współpracy B2B w usłudze Azure AD

Klienci mogą wykonać wiele czynności dzięki wbudowanym funkcjom udostępnionym użytkownikom końcowym za pośrednictwem witryny [Azure Portal](https://portal.azure.com) oraz [panelu dostępu do aplikacji](https://myapps.microsoft.com). Może się jednak okazać, że w zależności od potrzeb organizacji konieczne będzie dostosowanie przepływu pracy dołączania dla użytkowników B2B.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Zarządzanie prawami usługi Azure AD na potrzeby rejestracji użytkowników gościa B2B

Jako zapraszana organizacja może nie wiedzieć przed czasem, kto chce mieć dostęp do Twoich zasobów. W przypadku użytkowników z firm partnerskich należy zalogować się do zasad, które kontrolujesz. Jeśli chcesz umożliwić użytkownikom z innych organizacji żądania dostępu, a po zainicjowaniu zatwierdzania z kontami gościa i przypisaniu do grup, aplikacji i witryn usługi SharePoint Online, możesz użyć [zarządzania uprawnieniami usługi Azure AD](../governance/entitlement-management-overview.md) , aby skonfigurować zasady [zarządzające dostępem użytkowników zewnętrznych](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Active Directory interfejs API zaproszenia B2B

Organizacje mogą używać [interfejsu API programu Microsoft Graph Manager](/graph/api/resources/invitation) , aby tworzyć własne środowiska dołączania dla użytkowników-Gości. Jeśli chcesz oferować samoobsługowe rejestrowanie użytkowników systemu B2B, zalecamy korzystanie z funkcji [zarządzania prawami w usłudze Azure AD](../governance/entitlement-management-overview.md). Jeśli jednak chcesz utworzyć własne środowisko, możesz użyć [interfejsu API tworzenia zaproszenia](/graph/api/invitation-post?tabs=http) , aby automatycznie wysyłać niestandardowe wiadomości e-mail z zaproszeniem bezpośrednio do użytkownika B2B, na przykład. Lub aplikacja może używać inviteRedeemUrl zwróconych w odpowiedzi na tworzenie, aby utworzyć własne zaproszenie (za pomocą wybranego przez siebie mechanizmu komunikacji) do zaproszonego użytkownika.

## <a name="next-steps"></a>Następne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Cennik tożsamości zewnętrznych](external-identities-pricing.md)
* [Współpraca B2B w usłudze Active Directory Azure — często zadawane pytania](faq.md)
