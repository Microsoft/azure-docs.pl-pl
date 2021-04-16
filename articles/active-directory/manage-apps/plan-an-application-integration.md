---
title: Wprowadzenie do integracji aplikacji Azure Active Directory z aplikacjami
description: Ten artykuł zawiera wprowadzenie do integracji usług Azure Active Directory (AD) z aplikacjami lokalnymi i aplikacjami w chmurze.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2021
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 37916e5e356e7c5ad6e685ac0838363fe2579496
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374985"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Przewodnik integrowania Azure Active Directory z aplikacjami

Ten temat zawiera podsumowanie procesu integracji aplikacji z usługą Azure Active Directory (AD). Każda z poniższych sekcji zawiera krótkie podsumowanie bardziej szczegółowego tematu, dzięki czemu można zidentyfikować, które części tego przewodnika z wprowadzeniem są dla Ciebie istotne.

Aby pobrać szczegółowe plany wdrażania, zobacz [Następne kroki.](#next-steps)

## <a name="take-inventory"></a>Zrób spis
Przed zintegrowaniem aplikacji z usługą Azure AD ważne jest, aby wiedzieć, gdzie się znajdujesz i dokąd chcesz przejść.  Poniższe pytania mają ułatwić przemyślenie projektu integracji aplikacji usługi Azure AD.

### <a name="application-inventory"></a>Spis aplikacji
* Gdzie znajdują się wszystkie aplikacje? Kto jest ich właścicielem?
* Jakiego rodzaju uwierzytelniania wymagają aplikacje?
* Kto potrzebuje dostępu do jakich aplikacji?
* Czy chcesz wdrożyć nową aplikację?
  * Czy zostanie on skompilowany w domu i wdrożony w wystąpieniu obliczeniowym platformy Azure?
  * Czy będziesz używać aplikacji dostępnej w galerii aplikacji platformy Azure?

### <a name="user-and-group-inventory"></a>Spis użytkowników i grup
* Gdzie znajdują się konta użytkowników?
  * Lokalna usługa Active Directory
  * Azure AD
  * W oddzielnej bazie danych aplikacji, która jest właścicielem
  * W niezaakcjowanych aplikacjach
  * Wszystkie powyższe
* Jakie uprawnienia i przypisania ról mają obecnie poszczegnieni użytkownicy? Czy musisz przejrzeć ich dostęp, czy masz pewność, że twoje przypisania ról i dostępu użytkowników są teraz odpowiednie?
* Czy grupy są już ustanowione w lokalna usługa Active Directory?
  * Jak są zorganizowane grupy?
  * Kim są członkowie grupy?
  * Jakie uprawnienia/przypisania ról aktualnie mają grupy?
* Czy przed zintegrowaniem należy wyczyścić bazy danych użytkowników/grup?  (Jest to ważne pytanie. Odśmiecanie pamięci, odśmiecanie pamięci).

### <a name="access-management-inventory"></a>Spis zarządzania dostępem
* Jak obecnie zarządzać dostępem użytkowników do aplikacji? Czy trzeba to zmienić?  Czy rozważyliśmy inne sposoby zarządzania dostępem, takie jak na przykład za pomocą [kontroli RBAC na](../../role-based-access-control/role-assignments-portal.md) platformie Azure?
* Kto potrzebuje dostępu do jakich danych?

Być może nie masz z góry odpowiedzi na wszystkie te pytania, ale to nie problem.  Ten przewodnik może ułatwić udzielenie odpowiedzi na niektóre z tych pytań i podejmowanie świadomych decyzji.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Znajdowanie niezaakcjowanych aplikacji w chmurze za pomocą Cloud Discovery

Jak wspomniano powyżej, mogą wystąpić aplikacje, które do tej pory nie były zarządzane przez organizację.  W ramach procesu tworzenia spisu można znaleźć niezaakcjowane aplikacje w chmurze. Zobacz [Konfigurowanie Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrowanie aplikacji z usługą Azure AD
W poniższych artykułach omówiono różne sposoby integracji aplikacji z usługą Azure AD i przedstawiono wskazówki.

* [Określanie, której usługi Active Directory użyć](../fundamentals/active-directory-whatis.md)
* [Korzystanie z aplikacji w galerii aplikacji platformy Azure](what-is-single-sign-on.md)
* [Lista samouczków integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Możliwości aplikacji, których nie ma w galerii usługi Azure AD

Możesz dodać dowolną aplikację, która już istnieje w organizacji, lub dowolną aplikację innej firmy od dostawcy, który nie jest jeszcze częścią galerii usługi Azure AD. W zależności od [umowy licencyjnej](https://azure.microsoft.com/pricing/details/active-directory/)dostępne są następujące możliwości:

- Integracja samoobsługowa dowolnej aplikacji, która obsługuje dostawców [tożsamości SAML (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) (inicjowanych przez sp lub inicjowanych przez dostawców tożsamości)
- Integracja samoobsługowa dowolnej aplikacji internetowej z opartą na języku HTML stroną logowania przy użyciu [logowania jednokrotnego opartego na hasłach](sso-options.md#password-based-sso)
- Połączenie samoobsługowe aplikacji, które korzystają z protokołu [SCIM (System for Cross-Domain Identity Management) do aprowowania użytkowników](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Możliwość dodawania linków do dowolnej aplikacji w aplikacji uruchamiania lub uruchamiania aplikacji usługi [Office 365](https://support.microsoft.com/office/meet-the-microsoft-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) [Moje aplikacje](https://myapplications.microsoft.com/)

Jeśli szukasz wskazówek dla deweloperów dotyczących sposobu integrowania aplikacji niestandardowych z usługą Azure AD, zobacz Scenariusze uwierzytelniania [dla usługi Azure AD.](../develop/authentication-vs-authorization.md) Podczas tworzenia aplikacji, która używa nowoczesnego protokołu, takiego jak [OpenId Connect/OAuth,](../develop/active-directory-v2-protocols.md) do uwierzytelniania użytkowników, można zarejestrować go za pomocą platformy tożsamości firmy Microsoft przy użyciu Rejestracje aplikacji w Azure Portal. [](../develop/quickstart-register-app.md)

### <a name="authentication-types"></a>Typy uwierzytelniania
Każda z aplikacji może mieć inne wymagania dotyczące uwierzytelniania. W usłudze Azure AD certyfikaty podpisywania mogą być używane z aplikacjami, które używają protokołu SAML 2.0, usługi WS-Federation lub protokołów OpenID Connect i logowania się za pomocą hasła. Aby uzyskać więcej informacji na temat typów uwierzytelniania aplikacji, zobacz Managing [Certificates for Federated Single Sign-On in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) and Password based single sign on (Zarządzanie certyfikatami dla federowanych kont usługi single Sign-On w usługach Azure Active Directory i Password based single sign on ( Zarządzanie certyfikatami federacyjną na podstawie [hasła).](what-is-single-sign-on.md)

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Włączanie logowania jednokrotnego za pomocą aplikacja usługi Azure AD proxy
Dzięki Microsoft Azure AD serwer proxy aplikacji dostęp do aplikacji znajdujących się wewnątrz sieci prywatnej można zapewnić bezpiecznie, z dowolnego miejsca i na dowolnym urządzeniu. Po zainstalowaniu łącznika serwera proxy aplikacji w środowisku można go łatwo skonfigurować za pomocą usługi Azure AD.

### <a name="integrating-custom-applications"></a>Integrowanie aplikacji niestandardowych
Jeśli chcesz dodać aplikację niestandardową do galerii aplikacji platformy Azure, zobacz Publikowanie aplikacji w galerii [aplikacji usługi Azure AD.](../develop/v2-howto-app-gallery-listing.md)

## <a name="managing-access-to-applications"></a>Zarządzanie dostępem do aplikacji
W poniższych artykułach opisano sposoby zarządzania dostępem do aplikacji po ich zintegrowania z usługą Azure AD przy użyciu łączników usługi Azure AD i usługi Azure AD.

* [Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD](what-is-access-management.md)
* [Automatyzowanie za pomocą łączników usługi Azure AD](../app-provisioning/user-provisioning.md)
* [Assigning users to an application](./assign-user-or-group-access-portal.md) (Przypisywanie użytkowników do aplikacji)
* [Assigning groups to an application](./assign-user-or-group-access-portal.md) (Przypisywanie grup do aplikacji)
* [Udostępnianie kont](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje, możesz pobrać Azure Active Directory wdrożenia z usługi [GitHub.](../fundamentals/active-directory-deployment-plans.md) W przypadku aplikacji z galerii możesz pobrać plany wdrażania dla logowania pojedynczego, dostępu warunkowego i aprowizowania użytkowników za [pośrednictwem Azure Portal](https://portal.azure.com).

Aby pobrać plan wdrożenia z Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **pozycję Aplikacje dla przedsiębiorstw**  |  **Wybierz** plan wdrożenia  |  **aplikacji.**
