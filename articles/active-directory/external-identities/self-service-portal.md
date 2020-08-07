---
title: Portal rejestracji samoobsługowej współpracy B2B — Azure AD
description: Współpraca B2B usługi Azure Active Directory wspiera relacje między firmami, umożliwiając partnerom biznesowym selektywne uzyskiwanie dostępu do Twoich aplikacji firmowych
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
ms.openlocfilehash: ff18d3d9cae6e887abbd9fb1845de62386062b2b
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909114"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Samoobsługowe rejestrowanie w ramach współpracy B2B w usłudze Azure AD

Klienci mogą wykonać wiele czynności dzięki wbudowanym funkcjom udostępnionym użytkownikom końcowym za pośrednictwem witryny [Azure Portal](https://portal.azure.com) oraz [panelu dostępu do aplikacji](https://myapps.microsoft.com). Może się jednak okazać, że w zależności od potrzeb organizacji konieczne będzie dostosowanie przepływu pracy dołączania dla użytkowników B2B.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Zarządzanie prawami usługi Azure AD na potrzeby rejestracji użytkowników gościa B2B

Jako zapraszana organizacja może nie wiedzieć przed czasem, kto chce mieć dostęp do Twoich zasobów. W przypadku użytkowników z firm partnerskich należy zalogować się do zasad, które kontrolujesz. Jeśli chcesz umożliwić użytkownikom z innych organizacji żądania dostępu, a po zainicjowaniu zatwierdzania z kontami gościa i przypisaniu do grup, aplikacji i witryn usługi SharePoint Online, możesz użyć [zarządzania uprawnieniami usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) , aby skonfigurować zasady [zarządzające dostępem użytkowników zewnętrznych](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Active Directory interfejs API zaproszenia B2B

Organizacje mogą używać [interfejsu API programu Microsoft Graph Manager](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) , aby tworzyć własne środowiska dołączania dla użytkowników-Gości. Jeśli chcesz oferować samoobsługowe rejestrowanie użytkowników systemu B2B, zalecamy korzystanie z funkcji [zarządzania prawami w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Jeśli jednak chcesz utworzyć własne środowisko, możesz użyć [interfejsu API tworzenia zaproszenia](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) , aby automatycznie wysyłać niestandardowe wiadomości e-mail z zaproszeniem bezpośrednio do użytkownika B2B, na przykład. Lub aplikacja może używać inviteRedeemUrl zwróconych w odpowiedzi na tworzenie, aby utworzyć własne zaproszenie (za pomocą wybranego przez siebie mechanizmu komunikacji) do zaproszonego użytkownika.

## <a name="next-steps"></a>Następne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Licencjonowanie funkcji współpracy B2B w usłudze Azure AD](licensing-guidance.md)
* [Współpraca B2B w usłudze Active Directory Azure — często zadawane pytania](faq.md)
