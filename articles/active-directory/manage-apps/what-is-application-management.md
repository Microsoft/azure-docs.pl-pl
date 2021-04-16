---
title: Co to jest zarządzanie aplikacją w Azure Active Directory
description: Omówienie korzystania z Azure Active Directory (AD) jako systemu zarządzania tożsamościami i dostępem (IAM) dla aplikacji w chmurze i lokalnych.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: iangithinji
ms.reviewer: ''
ms.openlocfilehash: 8bdb8eb9cf176f8e190769e38c81d02ad2985196
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376396"
---
# <a name="what-is-application-management"></a>Co to jest zarządzanie aplikacjami?

Usługa Azure AD to system zarządzania dostępem i tożsamościami. Zapewnia ona jedno miejsce na potrzeby przechowywania informacji o tożsamościach cyfrowych. Aplikacje możesz skonfigurować tak, aby korzystały z usługi Azure AD jako miejsca przechowywania informacji o użytkowniku. 

Usługę Azure AD należy skonfigurować do integracji z aplikacją. Innymi słowy, musi wiedzieć, które aplikacje są za jej pomocą na potrzeby tożsamości. Aby usługa Azure AD była świadoma tych aplikacji i sposobu ich obsługi, jest to nazywane zarządzaniem aplikacjami.

Aplikacje można zarządzać na stronie **Aplikacje** dla przedsiębiorstw znajdującej się w sekcji Zarządzanie Azure Active Directory portal.

![Opcja Aplikacje dla przedsiębiorstw w sekcji Zarządzanie portalu usługi Azure AD.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Co to jest system zarządzania tożsamościami i dostępem (IAM)?
Aplikacja to oprogramowanie, które jest używane do pewnego celu. Większość aplikacji wymaga od użytkowników zalogowania się.

Scentralizowany system tożsamości zapewnia jedno miejsce do przechowywania informacji o użytkownikach, które mogą być następnie używane przez wszystkie aplikacje. Te systemy są nazywane systemami zarządzania tożsamościami i dostępem (IAM, Identity and Access Management). Azure Active Directory to system IAM dla chmury firmy Microsoft.

>[!TIP]
>System zarządzania dostępem i tożsamościami zapewnia jedno miejsce do śledzenia tożsamości użytkowników. Usługa Azure AD to system zarządzania dostępem i dostępem i dostępem w chmurze firmy Microsoft.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Dlaczego warto zarządzać aplikacjami przy użyciu rozwiązania w chmurze?

Organizacje mają często setki aplikacji, których użytkownicy potrzebują do wykonania swojej pracy. Użytkownicy uzyskują dostęp do tych aplikacji z wielu urządzeń i lokalizacji. Nowe aplikacje są przez cały czas dodawane, opracowywane i wymykane. W przypadku tak wielu aplikacji i punktów dostępu ważne jest, aby użyć rozwiązania tożsamości, które współpracuje z nimi wszystkimi.

>[!TIP]
>Galeria aplikacji usługi Azure AD zawiera wiele popularnych aplikacji, które są już wstępnie skonfigurowane do pracy z usługą Azure AD jako dostawcą tożsamości.

## <a name="how-does-azure-ad-work-with-apps"></a>Jak usługa Azure AD działa z aplikacjami?

Usługa Azure AD znajduje się w środku i zapewnia zarządzanie tożsamościami dla aplikacji w chmurze i lokalnych. 

![Diagram przedstawiający aplikacje federowane za pośrednictwem usługi Azure AD](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Zmniejsz koszty administracyjne, [automatyzując aprowizowanie](../app-provisioning/user-provisioning.md) użytkowników, dzięki czemu użytkownicy są automatycznie dodawana do usługi Azure AD po dodaniu ich do firmowego systemu HR. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Jakie typy aplikacji można zintegrować z usługą Azure AD?

Usługi Azure AD można używać jako systemu tożsamości dla dowolnej aplikacji. Wiele aplikacji jest już wstępnie skonfigurowanych i można je skonfigurować przy minimalnym nakładzie pracy. Te wstępnie skonfigurowane aplikacje są publikowane w [galerii aplikacja usługi Azure AD Aplikacji.](/azure/active-directory/saas-apps/) 

Większość aplikacji można ręcznie skonfigurować do logowania pojedynczego, jeśli nie są one jeszcze w galerii. Usługa Azure AD oferuje kilka opcji logowania jednokrotnego. Niektóre z najpopularniejszych to logowanie jednokrotne oparte na saml i logowanie jednokrotne oparte na OIDC. Aby dowiedzieć się więcej na temat integrowania aplikacji w celu włączenia logowania [jednokrotnego,](sso-options.md)zobacz opcje logowania jednokrotnego . 

Czy organizacja korzysta z aplikacji lokalnych? Można je zintegrować przy użyciu serwera proxy aplikacji. Aby dowiedzieć się więcej, zobacz [Zapewnianie](application-proxy.md)zdalnego dostępu do aplikacji lokalnych za pośrednictwem usługi Azure AD serwer proxy aplikacji .

>[!TIP]
>Podczas tworzenia własnych aplikacji biznesowych można zintegrować je z usługą Azure AD w celu obsługi logowania pojedynczego. Aby dowiedzieć się więcej na temat tworzenia aplikacji dla usługi Azure AD, zobacz [Platforma tożsamości firmy Microsoft.](..//develop/v2-overview.md)

## <a name="manage-risk-with-conditional-access-policies"></a>Zarządzanie ryzykiem przy użyciu zasad dostępu warunkowego

Sprzężenie logowania jednokrotnego usługi Azure AD z dostępem [warunkowym](../conditional-access/concept-conditional-access-cloud-apps.md) zapewnia wysoki poziom zabezpieczeń dostępu do aplikacji. Zasady dostępu warunkowego zapewniają szczegółową kontrolę aplikacji na podstawie ustawionych warunków. 

## <a name="improve-productivity-with-single-sign-on"></a>Zwiększanie produktywności dzięki logowaniu jednokrotnemu

Logowanie jednokrotne (SSO) zapewnia ujednolicone środowisko użytkownika między Microsoft 365 i wszystkimi innymi aplikacjami, których używasz. Żegnaj się, aby stale wprowadzać swoją nazwę użytkownika i hasło!

Aby dowiedzieć się więcej na temat logowania pojedynczego, zobacz [co to jest logowanie.](what-is-single-sign-on.md)

## <a name="address-governance-and-compliance"></a>Obsługa zarządzania i zgodności

Monitoruj aplikacje za pomocą raportów, które korzystają z narzędzi do monitorowania zdarzeń i zdarzeń zabezpieczeń (SIEM). Raporty są dostępne z poziomu portalu lub interfejsów API. Można również programowo przeprowadzać inspekcję użytkowników z dostępem do aplikacji i usuwać prawa dostępu do nieaktywnych użytkowników za pośrednictwem przeglądów dostępu.

## <a name="manage-costs"></a>Zarządzanie kosztami

Dzięki migracji do usługi Azure AD można obniżyć koszty i rozwiązać problemy związane z zarządzaniem infrastrukturą lokalną. Usługa Azure AD oferuje również samoobsługowy dostęp do aplikacji, co pozwala zaoszczędzić czas pracy administratorów i użytkowników. Logowanie jednokrotne eliminuje hasła specyficzne dla aplikacji. Możliwość zalogowania się tylko raz pozwala zmniejszyć koszty związane z resetowaniem haseł aplikacji oraz utratą produktywności podczas pobierania haseł.

W przypadku aplikacji skoncentrowanych na zasobach ludzkich lub innych aplikacji z dużym zestawem użytkowników możesz ułatwić sobie życie, aprowizując aplikacje. Aprowizowanie aplikacji automatyzuje proces dodawania i usuwania użytkowników. Aby dowiedzieć się więcej, zobacz [Co to jest aprowizowanie aplikacji?](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Następne kroki

- [Seria przewodników Szybki start na temat zarządzania aplikacją](view-applications-portal.md)
- [Wprowadzenie do integracji aplikacji](plan-an-application-integration.md)
- [Dowiedz się, jak zautomatyzować aprowizowanie](../app-provisioning/user-provisioning.md)