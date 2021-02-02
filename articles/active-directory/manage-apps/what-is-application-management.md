---
title: Co to jest zarządzanie aplikacjami w Azure Active Directory
description: Omówienie korzystania z usługi Azure Active Directory (AD) jako systemu zarządzania tożsamościami i dostępem w chmurze i aplikacji lokalnych.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: kenwith
ms.reviewer: ''
ms.openlocfilehash: 247e824997fd95434246e49c78bf167f36e146c0
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258595"
---
# <a name="what-is-application-management"></a>Co to jest zarządzanie aplikacjami?

Usługa Azure AD to system zarządzania dostępem i tożsamościami. Zapewnia ona jedno miejsce na potrzeby przechowywania informacji o tożsamościach cyfrowych. Aplikacje możesz skonfigurować tak, aby korzystały z usługi Azure AD jako miejsca przechowywania informacji o użytkowniku. 

Usługa Azure AD musi być skonfigurowana do integracji z aplikacją. Innymi słowy, musi wiedzieć, które aplikacje używają go do tożsamości. Usługa Azure AD wie o tych aplikacjach i sposobie ich obsługi, nazywana zarządzaniem aplikacjami.

Zarządzasz aplikacjami na stronie **aplikacje dla przedsiębiorstw** znajdujące się w sekcji Zarządzanie w portalu Azure Active Directory.

![Opcja aplikacje dla przedsiębiorstw w sekcji Zarządzanie w portalu usługi Azure AD.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Co to jest system zarządzania tożsamościami i dostępem (IAM)?
Aplikacja to oprogramowanie, które jest używane do pewnego celu. Większość aplikacji wymaga od użytkowników zalogowania się.

Scentralizowany system tożsamości umożliwia przechowywanie informacji o użytkownikach, które mogą być używane przez wszystkie aplikacje. Systemy te są znane jako systemy zarządzania tożsamościami i dostępem (IAM). Azure Active Directory to system mapy IAM dla chmury firmy Microsoft.

>[!TIP]
>System IAM zapewnia pojedyncze miejsce do śledzenia tożsamości użytkowników. Usługa Azure AD to system IAM dla chmury firmy Microsoft.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Dlaczego warto zarządzać aplikacjami przy użyciu rozwiązania w chmurze?

Organizacje mają często setki aplikacji, których użytkownicy potrzebują do wykonania swojej pracy. Użytkownicy uzyskują dostęp do tych aplikacji z wielu urządzeń i lokalizacji. Nowe aplikacje są dodawane, opracowywane i zachód słońca przez cały czas. W przypadku wielu aplikacji i punktów dostępu ważne jest używanie rozwiązania do tworzenia tożsamości, które współdziała z nimi.

>[!TIP]
>Galeria aplikacji usługi Azure AD zawiera wiele popularnych aplikacji, które są już wstępnie skonfigurowane do pracy z usługą Azure AD jako dostawca tożsamości.

## <a name="how-does-azure-ad-work-with-apps"></a>Jak działa usługa Azure AD z aplikacjami?

Usługa Azure AD znajduje się w środku i zapewnia zarządzanie tożsamościami dla aplikacji w chmurze i lokalnych. 

![Diagram przedstawiający aplikacje federacyjne za pośrednictwem usługi Azure AD](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Zmniejsz koszty administracyjne, [automatyzując Inicjowanie obsługi użytkowników](../app-provisioning/user-provisioning.md) , dzięki czemu użytkownicy są automatycznie dodawani do usługi Azure AD po dodaniu ich do firmowego systemu kadr. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Jakie typy aplikacji można zintegrować z usługą Azure AD?

Usługi Azure AD można używać jako systemu tożsamości tylko w przypadku dowolnej aplikacji. Wiele aplikacji jest już wstępnie skonfigurowanych i można je skonfigurować z minimalnym nakładem pracy. Te wstępnie skonfigurowane aplikacje są publikowane w [galerii aplikacja usługi Azure AD](/azure/active-directory/saas-apps/). 

Możesz ręcznie skonfigurować większość aplikacji do logowania jednokrotnego, jeśli nie znajdują się one jeszcze w galerii. Usługa Azure AD udostępnia kilka opcji logowania jednokrotnego. Niektóre z najpopularniejszych to oparte na protokole SAML Logowanie jednokrotne i logowanie jednokrotne oparte na OIDC. Aby dowiedzieć się więcej na temat integrowania aplikacji w celu włączenia logowania jednokrotnego, zobacz [Opcje logowania](sso-options.md)jednokrotnego. 

Czy Twoja organizacja korzysta z aplikacji lokalnych? Można je zintegrować przy użyciu serwera proxy aplikacji. Aby dowiedzieć się więcej, zobacz [zapewnianie dostępu zdalnego do aplikacji lokalnych za pomocą serwera proxy aplikacji usługi Azure AD](application-proxy.md).

>[!TIP]
>Podczas tworzenia własnych aplikacji biznesowych możesz zintegrować je z usługą Azure AD, aby obsługiwać Logowanie jednokrotne. Aby dowiedzieć się więcej na temat tworzenia aplikacji dla usługi Azure AD, zobacz [Microsoft Identity platform](..//develop/v2-overview.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Zarządzanie ryzykiem przy użyciu zasad dostępu warunkowego

Sprzęganie logowania jednokrotnego usługi Azure AD przy użyciu [dostępu warunkowego](../conditional-access/concept-conditional-access-cloud-apps.md) zapewnia wysoki poziom zabezpieczeń na potrzeby uzyskiwania dostępu do aplikacji. Zasady dostępu warunkowego zapewniają szczegółową kontrolę nad aplikacjami na podstawie ustawionych przez siebie warunków. 

## <a name="improve-productivity-with-single-sign-on"></a>Zwiększanie produktywności dzięki logowaniu jednokrotnemu

Logowanie jednokrotne (SSO) zapewnia ujednolicone środowisko użytkownika między Microsoft 365 i wszystkimi innymi używanymi aplikacjami. Zacznij ciągle wprowadzać swoją nazwę użytkownika i hasło.

Aby dowiedzieć się więcej o logowaniu jednokrotnym, zobacz [co to jest logowanie jednokrotne](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Obsługa zarządzania i zgodności

Monitoruj aplikacje za pomocą raportów, które używają narzędzi zdarzenia zabezpieczeń i monitorowanie zdarzeń (SIEM). Raporty są dostępne z poziomu portalu lub interfejsów API. Można również programowo przeprowadzać inspekcję użytkowników z dostępem do aplikacji i usuwać prawa dostępu do nieaktywnych użytkowników za pośrednictwem przeglądów dostępu.

## <a name="manage-costs"></a>Zarządzanie kosztami

Dzięki migracji do usługi Azure AD można obniżyć koszty i rozwiązać problemy związane z zarządzaniem infrastrukturą lokalną. Usługa Azure AD oferuje również samoobsługowy dostęp do aplikacji, co pozwala zaoszczędzić czas pracy administratorów i użytkowników. Logowanie jednokrotne eliminuje hasła specyficzne dla aplikacji. Możliwość zalogowania się tylko raz pozwala zmniejszyć koszty związane z resetowaniem haseł aplikacji oraz utratą produktywności podczas pobierania haseł.

W przypadku aplikacji z zasobami ludzkimi lub innych aplikacji z dużym zestawem użytkowników można korzystać z aprowizacji aplikacji, aby ułatwić życie. Inicjowanie obsługi aplikacji automatyzuje proces dodawania i usuwania użytkowników. Aby dowiedzieć się więcej, zobacz [co to jest inicjowanie obsługi aplikacji?](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Następne kroki

- [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)
- [Wprowadzenie do integracji aplikacji](plan-an-application-integration.md)
- [Dowiedz się, jak zautomatyzować Inicjowanie obsługi administracyjnej](../app-provisioning/user-provisioning.md)