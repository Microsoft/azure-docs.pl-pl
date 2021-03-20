---
title: Dodawanie Gości B2B bez linku do zaproszenia lub poczty e-mail — Azure AD
description: Możesz pozwolić, aby użytkownik-Gość dodał innych użytkowników-Gości do usługi Azure AD bez wypełniania zaproszenia w Azure Active Directory współpracy B2B.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: b18279e44bc5a3fd668d2ec4af6be29229af1b6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87909804"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Dodawanie użytkowników-Gości współpracy B2B bez linku do zaproszenia lub wiadomości e-mail

Możesz teraz zaprosić użytkowników-Gości przez wysłanie [bezpośredniego linku](redemption-experience.md#redemption-through-a-direct-link) do udostępnionej aplikacji. W przypadku tej metody użytkownicy-Goście nie muszą już używać wiadomości e-mail z zaproszeniem, z wyjątkiem niektórych specjalnych przypadków. Użytkownik-Gość klika link aplikacji, przegląda i akceptuje warunki prywatności, a następnie bezproblemowo uzyskuje dostęp do aplikacji. Aby uzyskać więcej informacji, zobacz temat do realizacji [zaproszeń do współpracy B2B](redemption-experience.md).

Przed udostępnieniem tej nowej metody można zaprosić użytkowników-Gości bez konieczności wysyłania wiadomości e-mail z zaproszeniem przez dodanie osoby żądającej (z organizacji lub z organizacji partnerskiej) do roli katalogu **zapraszania gościa** , a następnie dodanie użytkowników-Gości do katalogu, grup lub aplikacji za pośrednictwem interfejsu użytkownika lub programu PowerShell. (Jeśli używasz programu PowerShell, możesz całkowicie pominąć wiadomość e-mail z zaproszeniem). Na przykład:

1. Użytkownik w organizacji hosta (na przykład WoodGrove) zaprasza jednego użytkownika z organizacji partnera (na przykład Sam@litware.com ) jako gość.
2. Administrator w organizacji hosta [konfiguruje zasady](delegate-invitations.md) zezwalające usłudze sam na identyfikację i dodawanie innych użytkowników z organizacji partnera (litware). (Sam należy dodać do roli **zapraszania gościa** ).
3. Teraz można dodać innych użytkowników z litware do katalogu WoodGrove, grup lub aplikacji bez konieczności realizowania zaproszeń. Jeśli sam ma odpowiednie uprawnienia wyliczenia w litware, odbywa się to automatycznie.
 
Ta oryginalna metoda nadal działa. Istnieje jednak mała różnica w zachowaniu. Jeśli używasz programu PowerShell, możesz zauważyć, że zaproszone konto gościa ma teraz stan **PendingAcceptance** zamiast natychmiastowego wyświetlania **zaakceptowanego**. Mimo że stan jest oczekujący, użytkownik-Gość nadal może się zalogować i uzyskać dostęp do aplikacji bez kliknięcia linku do zaproszenia e-mail. Stan Oczekujący oznacza, że użytkownik nie został jeszcze przeszedł przez [proces wyrażania zgody](redemption-experience.md#consent-experience-for-the-guest), gdy akceptuje warunki prywatności organizacji zapraszanej. Użytkownik-Gość widzi ten ekran zgody, gdy loguje się po raz pierwszy. 

W przypadku zaproszenia użytkownika do katalogu użytkownik-Gość musi uzyskać dostęp do adresu URL Azure Portal specyficznego dla dzierżawy zasobu bezpośrednio (na przykład https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) w celu wyświetlenia i zaakceptowania postanowień dotyczących prywatności.

## <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Realizacja zaproszenia do współpracy B2B](redemption-experience.md)
- [Delegate invitations for Azure Active Directory B2B collaboration (Delegowanie zaproszeń na potrzeby współpracy B2B w usłudze Azure Active Directory)](delegate-invitations.md)
- [Jak Pracownicy przetwarzający informacje dodają użytkowników współpracy B2B?](add-users-information-worker.md)

