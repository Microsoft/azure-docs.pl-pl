---
title: Co to jest zarządzanie aplikacjami w Azure Active Directory
description: Omówienie korzystania z usługi Azure Active Directory (AD) jako systemu zarządzania tożsamościami i dostępem w chmurze i aplikacji lokalnych.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a157396318075522d5b3263c9cf0f749eafc2476
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658812"
---
# <a name="what-is-application-management"></a>Co to jest zarządzanie aplikacjami?

Usługa Azure AD to system zarządzania dostępem i tożsamościami. Zapewnia ona jedno miejsce na potrzeby przechowywania informacji o tożsamościach cyfrowych. Aplikacje możesz skonfigurować tak, aby korzystały z usługi Azure AD jako miejsca przechowywania informacji o użytkowniku. 

Usługa Azure AD musi być skonfigurowana do integracji z aplikacją. Innymi słowy, musi wiedzieć, które aplikacje używają go jako systemu tożsamości. Proces zapewniania, że usługa Azure AD wie o tych aplikacjach oraz jak powinny je obsługiwać, jest znana jako Zarządzanie aplikacjami.

Zarządzasz aplikacjami w bloku **aplikacje dla przedsiębiorstw** , które znajdują się w sekcji Zarządzanie w portalu Azure Active Directory.

![Opcja aplikacje dla przedsiębiorstw w sekcji Zarządzanie w portalu usługi Azure AD.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Co to jest system zarządzania tożsamościami i dostępem (IAM)?
Aplikacja to oprogramowanie, które jest używane do pewnego celu. Większość aplikacji wymaga od użytkowników logowania się, aby aplikacja mogła zapewnić dostosowane środowisko dla danego użytkownika. Innymi słowy, aplikacja musi znać tożsamość użytkownika przy użyciu aplikacji. Ponieważ informacje o funkcjach, które należy zaoferować lub usunąć, nie są dostępne dla użytkownika.

Jeśli każda aplikacja śledzi użytkowników oddzielnie, wynik będzie silosem różnych nazw użytkownika i nazw logowania dla każdej aplikacji. Jedna aplikacja nie wie niczego o użytkowników w innych aplikacjach.

Scentralizowany system tożsamości rozwiązuje ten problem, zapewniając w jednym miejscu przechowywanie informacji o użytkownikach, które mogą być następnie używane przez wszystkie aplikacje. Systemy te są znane jako systemy zarządzania tożsamościami i dostępem (IAM). Azure Active Directory to system mapy IAM dla chmury firmy Microsoft.

>[!TIP]
>System IAM zapewnia pojedyncze miejsce do śledzenia tożsamości użytkowników. Usługa Azure AD to system IAM dla chmury firmy Microsoft.


## <a name="why-manage-applications-with-a-cloud-solution"></a>Dlaczego warto zarządzać aplikacjami przy użyciu rozwiązania w chmurze?

Organizacje mają często setki aplikacji, których użytkownicy potrzebują do wykonania swojej pracy. Użytkownicy uzyskują dostęp do tych aplikacji z wielu urządzeń i lokalizacji. Nowe aplikacje są dodawane, tworzone i wycofywane każdego dnia. W przypadku wielu aplikacji i punktów dostępu bardziej krytyczne jest użycie rozwiązania opartego na chmurze do zarządzania dostępem użytkowników do wszystkich aplikacji.

>[!TIP]
>Galeria aplikacji usługi Azure AD zawiera wiele popularnych aplikacji, które są już wstępnie skonfigurowane do pracy z usługą Azure AD jako dostawca tożsamości.

## <a name="how-does-azure-ad-work-with-applications"></a>Jak usługa Azure AD współpracuje z aplikacjami?

Usługa Azure AD upraszcza sposób zarządzania aplikacjami, zapewniając pojedynczy system tożsamości dla aplikacji w chmurze i lokalnych. Aplikacje lokalne i aplikacje biznesowe (LOB) można dodawać do usługi Azure AD za pomocą aplikacji lokalnych (SaaS). Następnie Użytkownicy logują się raz, aby bezpiecznie i bezproblemowo uzyskiwać dostęp do tych aplikacji wraz z Microsoft 365 i innymi aplikacjami biznesowymi firmy Microsoft. Możesz zmniejszyć koszty administracyjne, [automatyzując Inicjowanie obsługi użytkowników](../app-provisioning/user-provisioning.md). Aby zapewnić bezpieczny dostęp do aplikacji, można także użyć usługi uwierzytelniania wieloskładnikowego i zasad dostępu warunkowego.

![Diagram przedstawiający aplikacje federacyjne za pośrednictwem usługi Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Jakie typy aplikacji można zintegrować z usługą Azure AD?

Istnieją cztery główne typy aplikacji, które można dodać do **aplikacji dla przedsiębiorstw** i zarządzać nimi za pomocą usługi Azure AD:

- **Aplikacje z galerii usługi Azure AD** — usługa Azure AD ma galerię zawierającą tysiące aplikacji, które zostały wstępnie zintegrowane na potrzeby logowania jednokrotnego w usłudze Azure AD. Niektóre z aplikacji używanych w Twojej organizacji prawdopodobnie znajdują się w galerii. [Dowiedz się więcej o planowaniu integracji aplikacji](plan-an-application-integration.md)lub zapoznaj się ze szczegółowymi krokami integracji dla poszczególnych aplikacji w [samouczkach aplikacji SaaS](/azure/active-directory/saas-apps/).

- **Aplikacje lokalne z serwerem proxy aplikacji** — za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD można zintegrować lokalne aplikacje sieci Web z usługą Azure AD w celu obsługi logowania jednokrotnego. Następnie użytkownicy końcowi mogą uzyskiwać dostęp do lokalnych aplikacji sieci Web w taki sam sposób, w jaki uzyskują dostęp do Microsoft 365 i innych aplikacji SaaS, zobacz [zapewnianie dostępu zdalnego do aplikacji lokalnych za pomocą serwera proxy aplikacji usługi Azure AD](application-proxy.md).

- **Aplikacje niestandardowe** — podczas tworzenia własnych aplikacji biznesowych możesz zintegrować je z usługą Azure AD, aby obsługiwać Logowanie jednokrotne. Rejestrując aplikację w usłudze Azure AD, masz kontrolę nad zasadami uwierzytelniania aplikacji. Aby uzyskać więcej informacji, zobacz [wskazówki dla deweloperów](developer-guidance-for-integrating-applications.md).

- **Aplikacje spoza galerii** — przenoszenie własnych aplikacji Obsługa logowania jednokrotnego dla innych aplikacji przez dodanie ich do usługi Azure AD. Istnieje wiele sposobów integrowania aplikacji, ale niektóre z nich wymieniono poniżej. Aby uzyskać więcej informacji, zobacz [Konfigurowanie protokołu SAML logowania](configure-saml-single-sign-on.md)jednokrotnego.

>[!TIP]
>Usługę Azure AD można zintegrować z aplikacją, nawet jeśli nie została jeszcze wstępnie skonfigurowana i w galerii aplikacji. **Usługę Azure AD można zintegrować z dowolną** z następujących czynności:
> - Dowolny link sieci Web lub aplikacja, która renderuje **pole nazwy użytkownika i hasła**.
> - Wszystkie aplikacje, które obsługują **Protokoły protokołu SAML lub OpenID Connect Connect**.
> - Dowolna aplikacja obsługująca **system dla standardu Standard scim (międzydomenowego zarządzania tożsamościami)** .

## <a name="manage-risk-with-conditional-access-policies"></a>Zarządzanie ryzykiem przy użyciu zasad dostępu warunkowego

Sprzęganie logowania jednokrotnego usługi Azure AD przy użyciu [dostępu warunkowego](../conditional-access/concept-conditional-access-cloud-apps.md) zapewnia wysoki poziom zabezpieczeń na potrzeby uzyskiwania dostępu do aplikacji. Funkcje zabezpieczeń obejmują ochronę tożsamości w skali chmury, kontrolę dostępu opartą na ryzyku, natywne uwierzytelnianie wieloskładnikowe i zasady dostępu warunkowego. Te funkcje pozwalają na stosowanie zasad szczegółowej kontroli w oparciu o aplikacje lub grupy, które potrzebują wyższego poziomu zabezpieczeń.

## <a name="improve-productivity-with-single-sign-on"></a>Zwiększanie produktywności dzięki logowaniu jednokrotnemu

Włączenie logowania jednokrotnego w wielu aplikacjach i Microsoft 365 zapewnia doskonałe środowisko logowania dla istniejących użytkowników, zmniejszając lub eliminując komunikaty logowania. Środowisko użytkownika wydaje się bardziej spójne i mniej rozpraszające bez wielu monitów i potrzeby zarządzania wieloma hasłami. Grupa biznesowa może zarządzać dostępem i zatwierdzać go za pośrednictwem samoobsługi i członkostwa dynamicznego. Zezwolenie odpowiednim osobom osób w firmie na zarządzanie dostępem do aplikacji zwiększa bezpieczeństwo systemu tożsamości.

Logowanie jednokrotne zwiększa bezpieczeństwo. *Bez logowania jednokrotnego* administratorzy muszą tworzyć i aktualizować konta użytkowników dla poszczególnych aplikacji, co zajmuje czas. Ponadto użytkownicy muszą śledzić wiele poświadczeń, aby uzyskiwać dostęp do swoich aplikacji. W rezultacie użytkownicy zwykle notują hasła lub używają innych rozwiązań do zarządzania hasłami, które wprowadzają zagrożenia bezpieczeństwa danych. [Przeczytaj więcej na temat rejestracji jednokrotnej](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Obsługa zarządzania i zgodności

Dzięki usłudze Azure AD można monitorować logowania do aplikacji za pomocą raportów korzystających z narzędzi do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM). Raporty są dostępne z poziomu portalu lub interfejsów API. Można również programowo przeprowadzać inspekcję użytkowników z dostępem do aplikacji i usuwać prawa dostępu do nieaktywnych użytkowników za pośrednictwem przeglądów dostępu.

## <a name="manage-costs"></a>Zarządzanie kosztami

Dzięki migracji do usługi Azure AD można obniżyć koszty i rozwiązać problemy związane z zarządzaniem infrastrukturą lokalną. Usługa Azure AD oferuje również samoobsługowy dostęp do aplikacji, co pozwala zaoszczędzić czas pracy administratorów i użytkowników. Logowanie jednokrotne eliminuje hasła specyficzne dla aplikacji. Możliwość zalogowania się tylko raz pozwala zmniejszyć koszty związane z resetowaniem haseł aplikacji oraz utratą produktywności podczas pobierania haseł.

W przypadku aplikacji przeznaczonych dla ludzkich zasobów lub innych aplikacji z dużym zestawem użytkowników można korzystać z aprowizacji aplikacji w celu zautomatyzowania procesu inicjowania obsługi i anulowania aprowizacji użytkowników, zobacz artykuł [co to jest inicjowanie obsługi aplikacji?](../app-provisioning/user-provisioning.md).

## <a name="next-steps"></a>Następne kroki

- [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)
- [Wprowadzenie do integracji aplikacji](plan-an-application-integration.md)
- [Dowiedz się, jak zautomatyzować Inicjowanie obsługi administracyjnej](../app-provisioning/user-provisioning.md)