---
title: Azure Active Directory często zadawane pytania dotyczące dostępu warunkowego | Microsoft Docs
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące dostępu warunkowego w Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8043e85db1ee1aadc814e98db12cab10ec17e129
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92145238"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory często zadawane pytania dotyczące dostępu warunkowego

## <a name="which-applications-work-with-conditional-access-policies"></a>Które aplikacje współpracują z zasadami dostępu warunkowego?

Aby uzyskać informacje o aplikacjach, które współpracują z zasadami dostępu warunkowego, zobacz [aplikacje i przeglądarki korzystające z reguł dostępu warunkowego w programie Azure Active Directory](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Czy zasady dostępu warunkowego są wymuszane dla użytkowników współpracy B2B i Gości?

Zasady są wymuszane dla użytkowników współpracy między firmami (B2B). Jednak w niektórych przypadkach użytkownik może nie być w stanie spełnić wymagań zasad. Na przykład organizacja użytkownika-gościa może nie obsługiwać uwierzytelniania wieloskładnikowego. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Czy zasady usługi SharePoint Online mają zastosowanie również w usłudze OneDrive dla firm?

Tak. Zasady SharePoint Online dotyczą również usługi OneDrive dla firm. Aby uzyskać więcej informacji, zobacz artykuł, [zależności usługi dostępu warunkowego](service-dependencies.md) i Rozważ użycie zasad docelowej dla [aplikacji pakietu Office 365](concept-conditional-access-cloud-apps.md#office-365) .

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Dlaczego nie mogę ustawić zasad bezpośrednio w aplikacjach klienckich, takich jak Word czy Outlook?

Zasady dostępu warunkowego określają wymagania dotyczące uzyskiwania dostępu do usługi. Jest wymuszany, gdy następuje uwierzytelnianie w usłudze. Zasady nie są ustawiane bezpośrednio w aplikacji klienckiej. Zamiast tego jest stosowany, gdy klient wywołuje usługę. Na przykład zestaw zasad w programie SharePoint ma zastosowanie do klientów wywołujących program SharePoint. Zestaw zasad dla programu Exchange ma zastosowanie do programu Outlook. Aby uzyskać więcej informacji, zobacz artykuł, [zależności usługi dostępu warunkowego](service-dependencies.md) i Rozważ użycie zasad docelowej dla [aplikacji pakietu Office 365](concept-conditional-access-cloud-apps.md#office-365) .

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Czy zasady dostępu warunkowego są stosowane do kont usług?

Zasady dostępu warunkowego są stosowane do wszystkich kont użytkowników. Obejmuje to konta użytkowników, które są używane jako konta usług. Często konto usługi z nienadzorowanym systemem nie spełnia wymagań zasad dostępu warunkowego. Na przykład może być wymagane uwierzytelnianie wieloskładnikowe. Konta usług można wykluczyć z zasad przy użyciu [wykluczenia użytkownika lub grupy](concept-conditional-access-users-groups.md#exclude-users). 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Jakie są domyślne zasady wykluczania dla nieobsługiwanych platform urządzeń?

Obecnie zasady dostępu warunkowego są wykonywane wybiórczo dla użytkowników urządzeń z systemem iOS lub Android. Aplikacje na innych platformach urządzeń są domyślnie nieobjęte zasadami dostępu warunkowego dla urządzeń z systemami iOS i Android. Administrator dzierżawy może zdecydować się na zastąpienie zasad globalnych, aby nie zezwalać na dostęp do użytkowników na platformach, które nie są obsługiwane.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Jak działają zasady dostępu warunkowego dla usługi Microsoft Teams?

Usługa Microsoft Teams opiera się na usłudze Exchange Online i SharePoint Online w przypadku podstawowych scenariuszy produktywności, takich jak spotkania, kalendarze i udostępnianie plików. Zasady dostępu warunkowego, które są ustawione dla tych aplikacji w chmurze, mają zastosowanie do programu Microsoft Teams, gdy użytkownik loguje się bezpośrednio do usługi Microsoft Teams.

Usługa Microsoft Teams jest również obsługiwana osobno jako aplikacja w chmurze w zasadach dostępu warunkowego. Zasady dostępu warunkowego ustawione dla aplikacji w chmurze mają zastosowanie do programu Microsoft Teams po zalogowaniu się użytkownika. Jednak bez poprawnych zasad w innych aplikacjach, takich jak usługa Exchange Online i użytkownicy usługi SharePoint Online, nadal będą mogli bezpośrednio uzyskiwać dostęp do tych zasobów.

Klienci stacjonarni Microsoft Teams dla systemów Windows i Mac obsługują nowoczesne uwierzytelnianie. Nowoczesne uwierzytelnianie umożliwia logowanie oparte na bibliotece uwierzytelniania Azure Active Directory (ADAL) do Microsoft Office aplikacji klienckich na różnych platformach.

Aby uzyskać więcej informacji, zobacz artykuł, [zależności usługi dostępu warunkowego](service-dependencies.md) i Rozważ użycie zasad docelowej dla [aplikacji pakietu Office 365](concept-conditional-access-cloud-apps.md#office-365) .

## <a name="why-are-some-tabs-not-working-in-microsoft-teams-after-enabling-conditional-access-policies"></a>Dlaczego niektóre karty nie działają w programie Microsoft Teams po włączeniu zasad dostępu warunkowego?

Po włączeniu niektórych zasad dostępu warunkowego w dzierżawie w usłudze Microsoft Teams niektóre karty mogą przestać działać na komputerze klienckim zgodnie z oczekiwaniami. Jednak na kartach, których dotyczy ta funkcja, jest używany klient sieci Web Microsoft Teams. Takie karty mogą obejmować Power BI, formularze, VSTS, PowerApps i listę programu SharePoint.

Aby wyświetlić karty, których to dotyczy, należy użyć klienta sieci Web programu Teams w programie Edge, Internet Explorer lub Chrome z zainstalowanym rozszerzeniem konta systemu Windows 10. Niektóre karty są zależne od uwierzytelniania w sieci Web, które nie działa w programie Microsoft Teams Client, gdy jest włączony dostęp warunkowy. Firma Microsoft współpracuje z partnerami, aby umożliwić korzystanie z tych scenariuszy. Do dnia zostały włączone scenariusze obejmujące Planistę, program OneNote i strumień.

## <a name="next-steps"></a>Następne kroki

- Aby skonfigurować zasady dostępu warunkowego dla środowiska, zapoznaj się z artykułem [Planowanie wdrożenia dostępu warunkowego](plan-conditional-access.md). 
