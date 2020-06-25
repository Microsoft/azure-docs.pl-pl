---
title: Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory | Microsoft Docs
description: W tym artykule opisano zalety integracji Azure Active Directory z aplikacjami lokalnymi, w chmurze i SaaS.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 06/05/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f826f5cc3e56dcf88ee110265724779a9d1f624
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84762925"
---
# <a name="application-management-with-azure-active-directory"></a>Zarządzanie aplikacjami za pomocą usługi Azure Active Directory

Azure Active Directory (Azure AD) upraszcza sposób zarządzania aplikacjami, zapewniając pojedynczy system tożsamości dla aplikacji w chmurze i lokalnych. Aplikacje lokalne i aplikacje biznesowe (LOB) można dodawać do usługi Azure AD za pomocą aplikacji lokalnych (SaaS). Następnie Użytkownicy logują się raz, aby bezpiecznie i bezproblemowo uzyskiwać dostęp do tych aplikacji wraz z pakietem Office 365 i innymi aplikacjami biznesowymi firmy Microsoft. Możesz zmniejszyć koszty administracyjne, [automatyzując Inicjowanie obsługi użytkowników](../app-provisioning/user-provisioning.md). Aby zapewnić bezpieczny dostęp do aplikacji, można także użyć usługi uwierzytelniania wieloskładnikowego i zasad dostępu warunkowego.

![Diagram przedstawiający aplikacje federacyjne za pośrednictwem usługi Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Dlaczego warto zarządzać aplikacjami przy użyciu rozwiązania w chmurze?

Organizacje mają często setki aplikacji, których użytkownicy potrzebują do wykonania swojej pracy. Użytkownicy uzyskują dostęp do tych aplikacji z wielu urządzeń i lokalizacji. Nowe aplikacje są dodawane, tworzone i wycofywane każdego dnia. W przypadku wielu aplikacji i punktów dostępu bardziej krytyczne jest użycie rozwiązania opartego na chmurze do zarządzania dostępem użytkowników do wszystkich aplikacji.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Jakie typy aplikacji można zintegrować z usługą Azure AD?

Istnieją cztery główne typy aplikacji, które można dodać do **aplikacji dla przedsiębiorstw** i zarządzać nimi za pomocą usługi Azure AD:

- **Aplikacje z galerii usługi Azure AD** — usługa Azure AD ma galerię zawierającą tysiące aplikacji, które zostały wstępnie zintegrowane na potrzeby logowania jednokrotnego w usłudze Azure AD. Niektóre z aplikacji używanych w Twojej organizacji prawdopodobnie znajdują się w galerii. [Dowiedz się więcej o planowaniu integracji aplikacji](plan-an-application-integration.md)lub zapoznaj się ze szczegółowymi krokami integracji dla poszczególnych aplikacji w [samouczkach aplikacji SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **Aplikacje lokalne z serwerem proxy aplikacji** — za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD można zintegrować lokalne aplikacje sieci Web z usługą Azure AD w celu obsługi logowania jednokrotnego. Następnie użytkownicy końcowi mogą uzyskiwać dostęp do lokalnych aplikacji sieci Web w taki sam sposób, w jaki uzyskują dostęp do pakietu Office 365 i innych aplikacji SaaS. [Dowiedz się, dlaczego należy używać serwera proxy aplikacji i sposobu jego działania](what-is-application-proxy.md).

- **Aplikacje niestandardowe** — podczas tworzenia własnych aplikacji biznesowych możesz zintegrować je z usługą Azure AD, aby obsługiwać Logowanie jednokrotne. Rejestrując aplikację w usłudze Azure AD, masz kontrolę nad zasadami uwierzytelniania aplikacji. Aby uzyskać więcej informacji, zobacz [wskazówki dla deweloperów](developer-guidance-for-integrating-applications.md).

- **Aplikacje spoza galerii** — przenoszenie własnych aplikacji Obsługa logowania jednokrotnego dla innych aplikacji przez dodanie ich do usługi Azure AD. Możesz zintegrować dowolny link internetowy lub dowolną aplikację, która renderuje pole nazwy użytkownika i hasła, obsługuje protokoły protokołu SAML lub OpenID Connect Connect lub obsługuje standard scim. Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego dla aplikacji spoza galerii](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Zarządzanie ryzykiem przy użyciu zasad dostępu warunkowego

Sprzęganie logowania jednokrotnego usługi Azure AD przy użyciu [dostępu warunkowego](../conditional-access/concept-conditional-access-cloud-apps.md) zapewnia wysoki poziom zabezpieczeń na potrzeby uzyskiwania dostępu do aplikacji. Funkcje zabezpieczeń obejmują ochronę tożsamości w skali chmury, kontrolę dostępu opartą na ryzyku, natywne uwierzytelnianie wieloskładnikowe i zasady dostępu warunkowego. Te funkcje pozwalają na stosowanie zasad szczegółowej kontroli w oparciu o aplikacje lub grupy, które potrzebują wyższego poziomu zabezpieczeń.

## <a name="improve-productivity-with-single-sign-on"></a>Zwiększanie produktywności dzięki logowaniu jednokrotnemu

Włączenie logowania jednokrotnego w aplikacjach i usłudze Office 365 zapewnia doskonałe środowisko logowania dla istniejących użytkowników dzięki ograniczeniu lub wyeliminowaniu monitów dotyczących logowania. Środowisko użytkownika wydaje się bardziej spójne i mniej rozpraszające bez wielu monitów i potrzeby zarządzania wieloma hasłami. Grupa biznesowa może zarządzać dostępem i zatwierdzać go za pośrednictwem samoobsługi i członkostwa dynamicznego. Zezwolenie odpowiednim osobom osób w firmie na zarządzanie dostępem do aplikacji zwiększa bezpieczeństwo systemu tożsamości.

Logowanie jednokrotne zwiększa bezpieczeństwo. *Bez logowania jednokrotnego* administratorzy muszą tworzyć i aktualizować konta użytkowników dla poszczególnych aplikacji, co zajmuje czas. Ponadto użytkownicy muszą śledzić wiele poświadczeń, aby uzyskiwać dostęp do swoich aplikacji. W rezultacie użytkownicy zwykle notują hasła lub używają innych rozwiązań do zarządzania hasłami, które wprowadzają zagrożenia bezpieczeństwa danych. [Przeczytaj więcej na temat rejestracji jednokrotnej](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Obsługa zarządzania i zgodności

Dzięki usłudze Azure AD można monitorować logowania do aplikacji za pomocą raportów korzystających z narzędzi do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM). Raporty są dostępne z poziomu portalu lub interfejsów API. Można również programowo przeprowadzać inspekcję użytkowników z dostępem do aplikacji i usuwać prawa dostępu do nieaktywnych użytkowników za pośrednictwem przeglądów dostępu.

## <a name="manage-costs"></a>Zarządzanie kosztami

Dzięki migracji do usługi Azure AD można obniżyć koszty i rozwiązać problemy związane z zarządzaniem infrastrukturą lokalną. Usługa Azure AD oferuje również samoobsługowy dostęp do aplikacji, co pozwala zaoszczędzić czas pracy administratorów i użytkowników. Logowanie jednokrotne eliminuje hasła specyficzne dla aplikacji. Możliwość zalogowania się tylko raz pozwala zmniejszyć koszty związane z resetowaniem haseł aplikacji oraz utratą produktywności podczas pobierania haseł.

## <a name="next-steps"></a>Następne kroki

- [Co to jest serwer proxy aplikacji?](what-is-application-proxy.md)
- [Szybki Start: Dodawanie aplikacji galerii do dzierżawy usługi Azure AD](add-application-portal.md)
