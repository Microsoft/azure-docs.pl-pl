---
title: Azure Active Directory często zadawane pytania dotyczące dostępu warunkowego | Microsoft Docs
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące dostępu warunkowego w Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6842338bd27e4bea3436f0b249380ab773d60de6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77368090"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory często zadawane pytania dotyczące dostępu warunkowego

## <a name="which-applications-work-with-conditional-access-policies"></a>Które aplikacje współpracują z zasadami dostępu warunkowego?

Aby uzyskać informacje o aplikacjach, które współpracują z zasadami dostępu warunkowego, zobacz [aplikacje i przeglądarki korzystające z reguł dostępu warunkowego w programie Azure Active Directory](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Czy zasady dostępu warunkowego są wymuszane dla użytkowników współpracy B2B i Gości?

Zasady są wymuszane dla użytkowników współpracy między firmami (B2B). Jednak w niektórych przypadkach użytkownik może nie być w stanie spełnić wymagań zasad. Na przykład organizacja użytkownika-gościa może nie obsługiwać uwierzytelniania wieloskładnikowego. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Czy zasady usługi SharePoint Online mają zastosowanie również w usłudze OneDrive dla firm?

Tak. Zasady SharePoint Online dotyczą również usługi OneDrive dla firm.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Dlaczego nie mogę ustawić zasad bezpośrednio w aplikacjach klienckich, takich jak Word czy Outlook?

Zasady dostępu warunkowego określają wymagania dotyczące uzyskiwania dostępu do usługi. Jest wymuszany, gdy następuje uwierzytelnianie w usłudze. Zasady nie są ustawiane bezpośrednio w aplikacji klienckiej. Zamiast tego jest stosowany, gdy klient wywołuje usługę. Na przykład zestaw zasad w programie SharePoint ma zastosowanie do klientów wywołujących program SharePoint. Zestaw zasad dla programu Exchange ma zastosowanie do programu Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Czy zasady dostępu warunkowego są stosowane do kont usług?

Zasady dostępu warunkowego są stosowane do wszystkich kont użytkowników. Obejmuje to konta użytkowników, które są używane jako konta usług. Często konto usługi z nienadzorowanym systemem nie spełnia wymagań zasad dostępu warunkowego. Na przykład może być wymagane uwierzytelnianie wieloskładnikowe. Konta usług można wykluczać z zasad przy użyciu ustawień zarządzania zasadami dostępu warunkowego. 

## <a name="are-microsoft-graph-apis-available-for-configuring-conditional-access-policies"></a>Czy są dostępne Microsoft Graph interfejsów API do konfigurowania zasad dostępu warunkowego?

Obecnie nie. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Jakie są domyślne zasady wykluczania dla nieobsługiwanych platform urządzeń?

Obecnie zasady dostępu warunkowego są wykonywane wybiórczo dla użytkowników urządzeń z systemem iOS lub Android. Aplikacje na innych platformach urządzeń są domyślnie nieobjęte zasadami dostępu warunkowego dla urządzeń z systemami iOS i Android. Administrator dzierżawy może zdecydować się na zastąpienie zasad globalnych, aby nie zezwalać na dostęp do użytkowników na platformach, które nie są obsługiwane.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Jak działają zasady dostępu warunkowego dla usługi Microsoft Teams?

Usługa Microsoft Teams opiera się na usłudze Exchange Online i SharePoint Online w przypadku podstawowych scenariuszy produktywności, takich jak spotkania, kalendarze i udostępnianie plików. Zasady dostępu warunkowego, które są ustawione dla tych aplikacji w chmurze, mają zastosowanie do programu Microsoft Teams, gdy użytkownik loguje się bezpośrednio do usługi Microsoft Teams.

Usługa Microsoft Teams jest również obsługiwana osobno jako aplikacja w chmurze w ramach Azure Active Directory zasad dostępu warunkowego. Zasady dostępu warunkowego ustawione dla aplikacji w chmurze mają zastosowanie do programu Microsoft Teams po zalogowaniu się użytkownika. Jednak bez poprawnych zasad w innych aplikacjach, takich jak usługa Exchange Online i użytkownicy usługi SharePoint Online, nadal będą mogli bezpośrednio uzyskiwać dostęp do tych zasobów.

Klienci stacjonarni Microsoft Teams dla systemów Windows i Mac obsługują nowoczesne uwierzytelnianie. Nowoczesne uwierzytelnianie umożliwia logowanie oparte na bibliotece uwierzytelniania Azure Active Directory (ADAL) do Microsoft Office aplikacji klienckich na różnych platformach.

## <a name="next-steps"></a>Następne kroki

- Aby skonfigurować zasady dostępu warunkowego dla środowiska, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi dostępu warunkowego w Azure Active Directory](best-practices.md). 
