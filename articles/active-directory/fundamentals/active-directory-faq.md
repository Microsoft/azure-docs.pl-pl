---
title: Często zadawane pytania — Azure Active Directory | Microsoft Docs
description: Często zadawane pytania i odpowiedzi dotyczące platformy Azure i Azure Active Directory, zarządzania hasłami i dostępu do aplikacji.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a8fcb14ac397791822c3a9065275dbfb2b3b7dd
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860426"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Często zadawane pytania dotyczące Azure Active Directory
Azure Active Directory (Azure AD) jest kompleksowym rozwiązaniem typu tożsamość jako usługa (IDaaS, Identity as a Service), które obejmuje wszystkie aspekty tożsamości, zarządzania dostępem i bezpieczeństwa.

Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Uzyskiwanie dostępu do platformy Azure i usługi Azure Active Directory
**P: Dlaczego otrzymuję pytanie "nie znaleziono żadnych subskrypcji" podczas próby dostępu do usługi Azure AD w Azure Portal?**

**Odpowiedź:** aby uzyskać dostęp do witryny Azure Portal, każdy użytkownik musi mieć uprawnienia w ramach subskrypcji platformy Azure. Jeśli nie masz płatnej Microsoft 365 lub subskrypcji usługi Azure AD, musisz aktywować bezpłatne [konto platformy Azure](https://azure.microsoft.com/free/
) lub subskrypcję płatną.

Aby uzyskać więcej informacji, zobacz:

* [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**P: Jaka jest relacja między usługą Azure AD, Microsoft 365 i platformą Azure?**

**Odpowiedź:** usługa Azure AD zawiera typowe funkcje związane z tożsamością i dostępem do wszystkich usług sieci Web. Bez względu na to, czy korzystasz z usługi Microsoft 365, Microsoft Azure, Intune, czy innych, jesteś już w usłudze Azure AD, aby pomóc w włączeniu funkcji logowania i zarządzania dostępem dla wszystkich tych usług.

Wszyscy użytkownicy skonfigurowani do używania usług sieci Web są zdefiniowani jako konta użytkowników w co najmniej jednym wystąpieniu usługi Azure AD. Możesz skonfigurować te konta dla bezpłatnych funkcji usługi Azure AD, np. dostępu do aplikacji w chmurze.

Płatne usługi Azure AD, takie jak Enterprise Mobility + Security uzupełniają inne usługi sieci Web, takie jak Microsoft 365 i Microsoft Azure z kompleksowymi rozwiązaniami w zakresie zarządzania i zabezpieczeń na poziomie przedsiębiorstwa.

---

**P: Jakie są różnice między właścicielem a administratorem globalnym?**

Odp **.:** Domyślnie osoba, która zarejestruje się w celu uzyskania subskrypcji platformy Azure, ma przypisaną rolę właściciela dla zasobów platformy Azure. Właściciel może korzystać z konto Microsoft lub konta służbowego z katalogu, z którym skojarzona jest subskrypcja platformy Azure.  Ta rola jest autoryzowana do zarządzania usługami w witrynie Azure Portal.

Jeśli inne osoby muszą logować się i uzyskiwać dostęp do usług za pomocą tej samej subskrypcji, możesz przypisać im odpowiednią [rolę wbudowaną](../../role-based-access-control/built-in-roles.md). Aby uzyskać dodatkowe informacje, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

Domyślnie osoba, która zarejestruje się w celu uzyskania subskrypcji platformy Azure, ma przypisaną rolę administratora globalnego dla katalogu. Administrator globalny ma dostęp do wszystkich funkcji katalogów usługi Azure AD. Usługa Azure AD ma inny zestaw ról administratora do zarządzania katalogiem i funkcjami związanymi z tożsamościami. Ci administratorzy będą mieli dostęp do różnych funkcji w Azure Portal. Rola administratora określa, co można zrobić, jak tworzyć lub edytować użytkowników, przypisywać role administracyjne do innych, resetować hasła użytkowników, zarządzać licencjami użytkowników lub zarządzać domenami.  Aby uzyskać dodatkowe informacje na temat administratorów katalogów usługi Azure AD i ich ról, zobacz [Przypisywanie użytkownika do ról administratorów w Azure Active Directory](active-directory-users-assign-role-azure-portal.md) i [Przypisywanie ról administratorów w programie Azure Active Directory](../roles/permissions-reference.md).

Ponadto usługi płatne w usłudze Azure AD, takie jak Enterprise Mobility + Security uzupełniają inne usługi sieci Web, takie jak Microsoft 365 i Microsoft Azure, dzięki kompleksowym rozwiązaniom zarządzania i zabezpieczeń na poziomie przedsiębiorstwa.

---
**Pytanie: czy istnieje raport, który pokazuje, kiedy wygaśnie moja licencja użytkownika usługi Azure AD?**

Odp **.:** Znaleziono.  Taki raport nie jest obecnie dostępny.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Wprowadzenie do hybrydowej usługi Azure AD


**Pytanie: jak opuścić dzierżawę, gdy dodano mnie do niej jako współpracownika?**

**Odpowiedź:** w przypadku dodania do dzierżawy innej organizacji jako współpracownika możesz przełączać się między dzierżawami za pomocą „przełącznika dzierżawy” w prawym górnym rogu.  Obecnie nie ma możliwości opuszczenia organizacji zapraszającej. Firma Microsoft pracuje nad dodaniem tej funkcji.  Do czasu udostępnienia tej funkcji rozwiązaniem może być poproszenie organizacji zapraszającej o usunięcie z dzierżawy.

---
**Pytanie: jak połączyć katalog lokalny z usługą Azure AD?**

**Odpowiedź:** możesz połączyć katalog lokalny z usługą Azure AD przy użyciu narzędzia Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**Pytanie: jak skonfigurować logowanie jednokrotne (SSO) między katalogiem lokalnym i aplikacjami w chmurze?**

**Odpowiedź:** wystarczy skonfigurować logowanie jednokrotne (SSO) między katalogiem lokalnym i usługą Azure AD. Jeśli tylko uzyskujesz dostęp do aplikacji w chmurze poprzez usługę Azure AD, usługa automatycznie wymusza od użytkowników odpowiednie uwierzytelnianie przy użyciu ich lokalnych poświadczeń.

Zaimplementowanie logowania jednokrotnego z lokalnego programu może być łatwo osiągnięte przy użyciu rozwiązań federacyjnych, takich jak Active Directory Federation Services (AD FS) lub przez skonfigurowanie synchronizacji skrótów haseł. Możesz łatwo wdrożyć obie opcje, korzystając z Kreatora konfiguracji Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**Pytanie: czy usługa Azure AD zawiera samoobsługowy portal dla użytkowników w organizacji?**

**Odpowiedź:** tak, usługa Azure AD zapewnia [Panel dostępu usługi Azure AD](https://myapps.microsoft.com) do samoobsługi użytkowników i dostępu do aplikacji. Jeśli jesteś klientem Microsoft 365, możesz znaleźć wiele tych samych funkcji w [portalu pakietu Office 365](https://portal.office.com).

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

---
**Pytanie: czy usługa Azure AD pomaga w zarządzaniu infrastrukturą lokalną?**

**Odpowiedź:** tak. Usługa Azure AD Premium zawiera program Azure AD Connect Health. Program Azure AD Connect Health pomaga monitorować i zdobywać informacje o lokalnej infrastrukturze do obsługi tożsamości oraz usługach synchronizacji.  

Aby uzyskać więcej informacji, zobacz [Monitorowanie lokalnej infrastruktury do obsługi tożsamości i usług synchronizacji w chmurze](../hybrid/whatis-azure-ad-connect.md).  

---
## <a name="password-management"></a>Zarządzanie hasłami
**P: Czy można użyć funkcji zapisywania zwrotnego haseł usługi Azure AD bez synchronizacji haseł? (W tym scenariuszu można korzystać z funkcji samoobsługowego resetowania haseł w usłudze Azure AD (SSPR) z funkcją zapisywania zwrotnego haseł i nie przechowywać haseł w chmurze?**

**Odpowiedź:** nie musisz synchronizować haseł usługi Active Directory z usługą Azure AD, aby korzystać z funkcji zapisywania zwrotnego. W środowisku federacyjnym logowanie jednokrotne usługi Azure AD opiera się na katalogu lokalnym w celu uwierzytelniania użytkownika. Ten scenariusz nie wymaga śledzenia lokalnego hasła w usłudze Azure AD.

---
**Pytanie: jak długo trwa zwrotne zapisanie hasła w lokalnym wystąpieniu usługi Active Directory?**

**Odpowiedź:** zapisywanie zwrotne haseł działa w czasie rzeczywistym.

Więcej informacji można znaleźć w temacie [Wprowadzenie do zarządzania hasłami](../authentication/tutorial-enable-sspr.md)

---
**Pytane: czy mogę użyć funkcji zapisywania zwrotnego haseł wobec haseł zarządzanych przez administratora?**

**Odpowiedź:** tak, jeśli włączysz funkcję zapisywania zwrotnego haseł, operacje na hasłach wykonywane przez administratora będą zwrotnie zapisywane w środowisku lokalnym.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Aby uzyskać więcej odpowiedzi na pytania związane z hasłami, zobacz [Często zadawane pytania dotyczące zarządzania hasłami](../authentication/active-directory-passwords-faq.md).
---
**P: co mogę zrobić, jeśli nie mogę zapamiętać mojego istniejącego hasła Microsoft 365/Azure AD podczas próby zmiany hasła?**

**Odpowiedź:** w takiej sytuacji istnieje kilka opcji.  Użyj funkcji samoobsługowego resetowania haseł (SSPR), jeśli jest dostępna.  To, czy funkcja samoobsługowego resetowania haseł działa, zależy od tego, jak została skonfigurowana.  Aby uzyskać więcej informacji, zobacz [Jak działa portal resetowania haseł](../authentication/howto-sspr-deployment.md).

W przypadku użytkowników Microsoft 365 administrator może zresetować hasło, wykonując kroki opisane w sekcji [resetowanie haseł użytkowników](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

W przypadku kont usługi Azure AD administratorzy mogą zresetować hasło w jeden z następujących sposobów:

- [Resetowanie kont w witrynie Azure Portal](active-directory-users-reset-password-azure-portal.md)
- [Korzystanie z programu PowerShell](/powershell/module/msonline/set-msoluserpassword)


---
## <a name="security"></a>Zabezpieczenia
**Pytanie: czy konta są blokowane po określonej liczbie nieudanych prób, czy jest stosowana bardziej zaawansowana strategia?**

Korzystamy z bardziej zaawansowanej strategii blokowania kont.  Jest ona oparta na adresie IP żądania i wprowadzonym haśle. Czas trwania blokady wydłuża się też w zależności od stopnia prawdopodobieństwa ataku.  

**P: niektóre (typowe) hasła są odrzucane z komunikatami "to hasło zostało użyte do wielu razy", czy dotyczy to haseł używanych w bieżącej usłudze Active Directory?**

Odnosi się to do haseł standardowych, takich jak wszystkie warianty "hasła" i "123456".

**Pytanie: czy żądanie logowania z podejrzanych źródeł (botnety, punkt końcowy sieci Tor) zostanie zablokowane w dzierżawie B2C, czy wymaga to dzierżawy w warstwie Podstawowa lub Premium?**

Oferujemy bramę, która filtruje żądania i zapewnia ochronę przed botnetami. Jest ona stosowana we wszystkich dzierżawach B2C.

## <a name="application-access"></a>Dostęp do aplikacji

**Pytanie: gdzie mogę znaleźć listę aplikacji, które są wstępnie zintegrowane z usługą Azure AD i jej funkcjami?**

**Odpowiedź:** usługa Azure AD ma ponad 2600 wstępnie zintegrowanych aplikacji od firmy Microsoft, dostawców usług aplikacji i partnerów. Wszystkie wstępnie zintegrowane aplikacje obsługują logowanie jednokrotne. Logowanie jednokrotne umożliwia uzyskiwanie dostępu do aplikacji przy użyciu poświadczeń organizacji. Niektóre aplikacje obsługują również zautomatyzowaną aprowizację i cofanie aprowizacji.

Aby uzyskać pełną listę wstępnie zintegrowanych aplikacji, zobacz [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

---
**Pytanie: co w przypadku, jeśli aplikacja, której potrzebuję, nie znajduje się w witrynie Azure AD Marketplace?**

**Odpowiedź:** przy użyciu usługi Azure AD Premium możesz dodać i skonfigurować dowolną aplikację. W zależności od możliwości aplikacji i preferencji możesz skonfigurować rejestrację jednokrotną i automatyczne Inicjowanie obsługi.  

Aby uzyskać więcej informacji, zobacz:

* [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory](../manage-apps/configure-saml-single-sign-on.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**Pytanie: jak użytkownicy logują się do aplikacji przy użyciu usługi Azure AD?**

**Odpowiedź:** usługa Azure AD oferuje użytkownikom kilka możliwości wyświetlania aplikacji i uzyskiwania do nich dostępu, np.:

* Panel dostępu usługi Azure AD
* Microsoft 365 uruchamiania aplikacji
* Bezpośrednie logowanie do aplikacji federacyjnych
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

Aby uzyskać więcej informacji, zobacz [środowisko użytkownika końcowego dla aplikacji](../manage-apps/end-user-experiences.md).

---
**Pytanie: jakie są różne sposoby włączania uwierzytelniania i logowania jednokrotnego do aplikacji przez usługę Azure AD?**

**Odpowiedź:** usługa Azure AD obsługuje wiele standardowych protokołów uwierzytelniania i autoryzacji, takich jak SAML 2.0, OpenID Connect, OAuth 2.0 i WS-Federation. Usługa Azure AD obsługuje również archiwizowanie haseł i automatyczne logowanie do aplikacji, które obsługują wyłącznie uwierzytelnianie oparte na formularzach.  

Aby uzyskać więcej informacji, zobacz:

* [Scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-vs-authorization.md)
* [Active Directory protokoły uwierzytelniania](/previous-versions/azure/dn151124(v=azure.100))
* [Logowanie jednokrotne dla aplikacji w usłudze Azure AD](../manage-apps/what-is-single-sign-on.md)

---
**P: Czy mogę dodać aplikacje uruchamiane lokalnie?**

**Odpowiedź:** serwer proxy aplikacji usługi Azure AD zapewnia prosty i bezpieczny dostęp do wybranych lokalnych aplikacji internetowych. Dostęp do tych aplikacji można uzyskiwać w taki sam sposób jak w przypadku aplikacji typu oprogramowanie jako usługa (SaaS) w usłudze Azure AD. Nie ma potrzeby stosowania sieci VPN ani zmiany infrastruktury sieciowej.  

Aby uzyskać więcej informacji, zobacz [Zapewnianie bezpiecznego dostępu zdalnego do aplikacji lokalnych](../manage-apps/application-proxy.md).

---
**Pytanie: jak wymagać uwierzytelniania wieloskładnikowego dla użytkowników uzyskujących dostęp do określonej aplikacji?**

Odp **.:** Dostęp warunkowy do usługi Azure AD umożliwia przypisywanie unikatowych zasad dostępu dla każdej aplikacji. W zasadach możesz wymagać korzystania z uwierzytelniania wieloskładnikowego w każdym przypadku lub wtedy, gdy użytkownicy nie są połączeni z siecią lokalną.  

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie dostępu do Microsoft 365 i innych aplikacji połączonych z Azure Active Directory](../conditional-access/overview.md).

---
**P: co to jest zautomatyzowana funkcja aprowizacji użytkowników dla aplikacji SaaS?**

**Odpowiedź:** usługa Azure AD umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w wielu popularnych aplikacjach SaaS w chmurze.

Aby uzyskać więcej informacji, zobacz [Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](../app-provisioning/user-provisioning.md) (Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory).

---
**Pytanie: czy mogę skonfigurować bezpieczne połączenie LDAP z usługą Azure AD?**

Odp **.:**  Znaleziono. Usługa Azure AD nie obsługuje protokołu Lightweight Directory Access Protocol (LDAP) ani Secure LDAP bezpośrednio. Możliwe jest jednak włączenie wystąpienia Azure AD Domain Services (Azure AD DS) w dzierżawie usługi Azure AD z odpowiednio skonfigurowanymi grupami zabezpieczeń sieci za pośrednictwem sieci platformy Azure, aby zapewnić łączność z protokołem LDAP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-configure-ldaps.md)
