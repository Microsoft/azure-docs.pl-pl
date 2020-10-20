---
title: Compare Active Directory to Azure Active Directory (Porównanie usługi Active Directory z usługą Azure Active Directory)
description: Ten dokument zawiera porównanie Active Directory Domain Services (dodaje) do Azure Active Directory (AD). Przedstawiono w nim kluczowe koncepcje w obu rozwiązaniach dotyczących tożsamości i wyjaśniono, w jaki sposób jest to różne lub podobne.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 3c8cf84aa697512b6d1147bf853d30761792370b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207312"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Compare Active Directory to Azure Active Directory (Porównanie usługi Active Directory z usługą Azure Active Directory)

Azure Active Directory to kolejna ewolucja rozwiązań do zarządzania tożsamościami i dostępem w chmurze. Firma Microsoft wprowadziła Active Directory Domain Services w systemie Windows 2000, aby zapewnić organizacjom możliwość zarządzania wieloma lokalnymi składnikami infrastruktury i systemami przy użyciu jednej tożsamości na użytkownika.

Usługa Azure AD przyjmuje takie podejście do następnego poziomu, dostarczając organizacje z rozwiązaniem typu "tożsamość jako usługa" (IDaaS) dla wszystkich aplikacji w chmurze i lokalnie.

Większość administratorów IT zna koncepcje Active Directory Domain Services. W poniższej tabeli przedstawiono różnice i podobieństwa między Active Directory pojęcia i Azure Active Directory.

|Pojęcie|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**Użytkownicy**|||
|Inicjowanie obsługi: Użytkownicy | Organizacje tworzą użytkowników wewnętrznych ręcznie lub używają wbudowanego lub zautomatyzowanego systemu aprowizacji, takiego jak Microsoft Identity Manager, do integracji z systemem kadr.|Istniejące organizacje usługi AD używają [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) do synchronizowania tożsamości z chmurą.</br> Usługa Azure AD dodaje obsługę automatycznego tworzenia użytkowników z [systemów w chmurze](../saas-apps/workday-tutorial.md). </br>Usługa Azure AD może udostępniać tożsamości w [Standard scim włączonych](../app-provisioning/use-scim-to-provision-users-and-groups.md) aplikacjach SaaS w celu automatycznego dostarczania aplikacji z wymaganymi informacjami, aby umożliwić dostęp użytkownikom. |
|Inicjowanie obsługi administracyjnej: tożsamości zewnętrzne| Organizacje mogą tworzyć użytkowników zewnętrznych ręcznie jako zwykłych użytkowników w dedykowanym zewnętrznym lesie usługi AD, co prowadzi do ponoszenia kosztów administracyjnych zarządzania cyklem życia tożsamości zewnętrznych (Użytkownicy-Goście)| Usługa Azure AD zapewnia specjalną klasę tożsamości do obsługi tożsamości zewnętrznych. [Usługa Azure AD B2B](/azure/active-directory/b2b/) będzie zarządzać łączem do tożsamości użytkownika zewnętrznego, aby upewnić się, że są one poprawne. |
| Zarządzanie prawami i grupy| Administratorzy tworzą członków grup. Właściciele aplikacji i zasobów udzielą grupie dostępu do aplikacji lub zasobów.| [Grupy](./active-directory-groups-create-azure-portal.md) są również dostępne w usłudze Azure AD, a administratorzy mogą również używać grup do przyznawania uprawnień do zasobów. W usłudze Azure AD Administratorzy mogą ręcznie przypisać członkostwo do grup lub użyć zapytania, aby dynamicznie dołączać użytkowników do grupy. </br> Administratorzy mogą używać funkcji [zarządzania prawami](../governance/entitlement-management-overview.md) w usłudze Azure AD, aby zapewnić użytkownikom dostęp do kolekcji aplikacji i zasobów przy użyciu przepływów pracy oraz, w razie potrzeby, kryteriów opartych na czasie. |
| Zarządzanie administratorami|Organizacje będą używać kombinacji domen, jednostek organizacyjnych i grup w usłudze AD w celu delegowania praw administracyjnych do zarządzania katalogiem i zasobami, które kontroluje.| Usługa Azure AD udostępnia [wbudowane role](./active-directory-users-assign-role-azure-portal.md) z systemem kontroli dostępu opartej na rolach (Azure AD RBAC) usługi Azure AD z ograniczoną obsługą [tworzenia ról niestandardowych](../users-groups-roles/roles-custom-overview.md) w celu delegowania uprzywilejowanego dostępu do systemu tożsamości, aplikacji i zasobów, które kontroluje.</br>Zarządzanie rolami można rozszerzyć za pomocą [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) , aby zapewnić dostęp do ról uprzywilejowanych w trybie just-in-Time lub opartym na przepływie pracy. |
| Zarządzanie poświadczeniami| Poświadczenia w Active Directory opierają się na hasłach, uwierzytelnianiu certyfikatów i uwierzytelnianiu kart inteligentnych. Hasła są zarządzane przy użyciu zasad haseł, które są oparte na długości, ważności i złożoności hasła.|Usługa Azure AD używa inteligentnej [ochrony hasłem](../authentication/concept-password-ban-bad.md) w chmurze i lokalnie. Ochrona obejmuje inteligentną blokadę oraz blokowanie wspólnych i niestandardowych fraz haseł oraz podstawień. </br>Usługa Azure AD znacząco zwiększa bezpieczeństwo [dzięki użyciu uwierzytelniania wieloskładnikowego i technologii niezwiązanych](../authentication/concept-mfa-howitworks.md) z [hasłami](../authentication/concept-authentication-passwordless.md) , takich jak FIDO2. </br>Usługa Azure AD redukuje koszty pomocy technicznej, zapewniając użytkownikom [samoobsługowy system resetowania hasła](../authentication/concept-sspr-howitworks.md) . |
| **Aplikacje**|||
| Aplikacje infrastruktury|Active Directory stanowi podstawę dla wielu lokalnych składników infrastruktury, na przykład DNS, DHCP, IPSec, Wi-Fi, NPS i sieci VPN.|W nowej chmurze chmurowej usługa Azure AD jest nową płaszczyzną kontroli umożliwiającą uzyskiwanie dostępu do aplikacji w przeciwieństwie do kontrolek sieciowych. Gdy użytkownicy uwierzytelniają się[, dostęp warunkowy (CA)](../conditional-access/overview.md), program będzie kontrolować użytkowników, którzy będą mieli dostęp do aplikacji w ramach wymaganych warunków.|
| Tradycyjne i starsze aplikacje| Większość aplikacji lokalnych korzysta z protokołu LDAP, uwierzytelniania Windows-Integrated (NTLM i Kerberos) lub uwierzytelniania opartego na nagłówkach, aby kontrolować dostęp do użytkowników.| Usługa Azure AD może zapewnić dostęp do tych typów aplikacji lokalnych przy użyciu agentów [serwera proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md) działających lokalnie. Za pomocą tej metody usługa Azure AD może uwierzytelniać Active Directory użytkowników lokalnych przy użyciu protokołu Kerberos podczas migracji lub konieczności współistnienia ze starszymi aplikacjami. |
| Aplikacje SaaS|Active Directory nie obsługuje natywnie aplikacji SaaS i wymaga systemu federacyjnego, takiego jak AD FS.|Aplikacje SaaS obsługujące funkcje OAuth2, SAML i WS- \* Authentication można zintegrować z usługą Azure AD w celu uwierzytelniania. |
| Aplikacje biznesowe (LOB) z nowoczesnego uwierzytelniania|Organizacje mogą używać AD FS z Active Directory, aby obsługiwać aplikacje LOB wymagające nowoczesnego uwierzytelniania.| Aplikacje LOB wymagające nowoczesnego uwierzytelniania można skonfigurować do uwierzytelniania za pomocą usługi Azure AD. |
| Usługi warstwy średniej/demona|Usługi działające w środowiskach lokalnych zwykle używają kont usług AD lub kont usług zarządzanych przez grupę (gMSA) do uruchomienia. Następnie te aplikacje będą dziedziczyć uprawnienia konta usługi.| Usługa Azure AD zapewnia [zarządzane tożsamości](../managed-identities-azure-resources/index.yml) do uruchamiania innych obciążeń w chmurze. Cykl życia tych tożsamości jest zarządzany przez usługę Azure AD i jest powiązany z dostawcą zasobów, dlatego nie można go używać do innych celów w celu uzyskania dostępu tylne wejście.|
| **Urządzenia**|||
| Aplikacje mobilne|Active Directory nie obsługuje natywnie urządzeń przenośnych bez rozwiązań innych firm.| Rozwiązanie do zarządzania urządzeniami przenośnymi firmy Microsoft, Microsoft Intune, jest zintegrowane z usługą Azure AD. Microsoft Intune dostarcza informacje o stanie urządzenia do systemu tożsamości w celu ocenienia podczas uwierzytelniania. |
| Komputery stacjonarne z systemem Windows|Active Directory umożliwia dołączanie urządzeń z systemem Windows do domeny w celu zarządzania nimi przy użyciu zasady grupy, System Center Configuration Manager lub innych rozwiązań innych firm.|Urządzenia z systemem Windows można [przyłączyć do usługi Azure AD](../devices/index.yml). Dostęp warunkowy może sprawdzić, czy urządzenie jest dołączone do usługi Azure AD w ramach procesu uwierzytelniania. Urządzeniami z systemem Windows można także zarządzać za pomocą [Microsoft Intune](/intune/what-is-intune). W takim przypadku dostęp warunkowy będzie uwzględniać, czy urządzenie jest zgodne (na przykład Aktualności poprawek zabezpieczeń i podpisów wirusów) przed zezwoleniem na dostęp do aplikacji.|
| Serwery z systemem Windows| Active Directory zapewnia silne możliwości zarządzania lokalnymi serwerami systemu Windows przy użyciu zasady grupy lub innych rozwiązań do zarządzania.| Maszyny wirtualne z systemem Windows Server na platformie Azure mogą być zarządzane za pomocą [Azure AD Domain Services](../../active-directory-domain-services/index.yml). [Tożsamości zarządzane](../managed-identities-azure-resources/index.yml) mogą być używane, gdy maszyny wirtualne wymagają dostępu do katalogu lub zasobów systemu tożsamości.|
| Obciążenia dla systemu Linux/UNIX|Active Directory nie obsługuje natywnie systemów innych niż Windows bez rozwiązań innych firm, chociaż maszyny z systemem Linux można skonfigurować do uwierzytelniania za pomocą Active Directory jako obszaru protokołu Kerberos.|Maszyny wirtualne z systemem Linux/UNIX mogą używać [tożsamości zarządzanych](../managed-identities-azure-resources/index.yml) do uzyskiwania dostępu do systemu lub zasobów tożsamości. Niektóre organizacje migruje te obciążenia do technologii kontenera chmury, które mogą również używać tożsamości zarządzanych.|

## <a name="next-steps"></a>Następne kroki

- [Co to jest usługa Azure Active Directory?](./active-directory-whatis.md)
- [Porównaj Active Directory Domain Services samozarządzane, Azure Active Directory i zarządzane Azure Active Directory Domain Services](../../active-directory-domain-services/compare-identity-solutions.md)
- [Często zadawane pytania dotyczące Azure Active Directory](./active-directory-faq.md)
- [Co nowego w Azure Active Directory?](./whats-new.md)
