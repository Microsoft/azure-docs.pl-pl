---
title: Co to jest usługa Azure Active Directory? — Azure Active Directory | Microsoft Docs
description: Omówienie i pojęcia dotyczące Azure Active Directory, w tym terminologia, jakie licencje są dostępne, oraz listę powiązanych funkcji z linkami, aby uzyskać więcej informacji.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.date: 06/05/2020
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9e85960f3efc074eca18475c4a3e6c422c1990b
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090845"
---
# <a name="what-is-azure-active-directory"></a>Co to jest usługa Azure Active Directory?

Azure Active Directory (Azure AD) to usługa zarządzania tożsamościami i dostępem opartymi na chmurze firmy Microsoft, która ułatwia pracownikom logowanie się i dostęp do zasobów w programie:

- Zasoby zewnętrzne, takie jak Microsoft 365, Azure Portal i tysiące innych aplikacji SaaS.

- Zasoby wewnętrzne, takie jak aplikacje w sieci firmowej i intranecie, a także aplikacje w chmurze opracowane przez organizację. Aby uzyskać więcej informacji na temat tworzenia dzierżawy dla organizacji, zobacz [Szybki Start: Tworzenie nowej dzierżawy w Azure Active Directory](active-directory-access-create-new-tenant.md).

Aby uzyskać informacje na temat różnic między usługą Azure AD a Active Directory Domain Services, zobacz [porównanie Active Directory z Azure Active Directory](active-directory-compare-azure-ad-to-ad.md). Możesz również użyć różnych [Microsoft Cloud dla plakatów serii Enterprise Architects](/microsoft-365/solutions/cloud-architecture-models) , aby lepiej zrozumieć podstawowe usługi tożsamości na platformie Azure, usłudze Azure AD i Microsoft 365.

## <a name="who-uses-azure-ad"></a>Kto używa usługi Azure AD?

Usługa Azure AD jest przeznaczona dla następujących użytkowników:

- **Administratorzy IT.** Jako administrator IT możesz używać usługi Azure AD, aby kontrolować dostęp do aplikacji i ich zasobów, zależnie od wymagań biznesowych. Możesz na przykład użyć usługi Azure AD, aby wymagać uwierzytelniania wieloskładnikowego podczas uzyskiwania dostępu do ważnych zasobów organizacji. Ponadto możesz użyć usługi Azure AD do automatyzowania aprowizacji użytkowników między istniejącymi usługami AD systemu Windows Server i aplikacjami w chmurze, w tym Microsoft 365. Ponadto usługa Azure AD zapewnia zaawansowane narzędzia, które automatycznie ułatwiają ochronę tożsamości użytkowników i poświadczeń zgodnie z wymaganiami nadzoru nad dostępem. Aby rozpocząć, utwórz konto [bezpłatnej 30-dniowej wersji próbnej usługi Azure Active Directory w wersji Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Deweloperzy aplikacji.** Jako deweloper aplikacji możesz użyć usługi Azure AD jako podejścia opartego na standardach do dodawania logowania jednokrotnego do aplikacji, co pozwala na współdziałanie z istniejącymi poświadczeniami użytkownika. Usługa Azure AD udostępnia również interfejsy API, które mogą pomóc w tworzeniu spersonalizowanych środowisk aplikacji przy użyciu istniejących danych organizacji. Aby rozpocząć, utwórz konto [bezpłatnej 30-dniowej wersji próbnej usługi Azure Active Directory w wersji Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Aby uzyskać więcej informacji, możesz też przejść do strony [Usługa Azure Active Directory dla deweloperów](../develop/index.yml).

- **Subskrybenci produktów Microsoft 365, Office 365, Azure lub Dynamics CRM Online.** Jako subskrybent korzystasz już z usługi Azure AD. Każda dzierżawa produktów Microsoft 365, Office 365, Azure i Dynamics CRM Online staje się automatycznie dzierżawą usługi Azure AD. Możesz od razu zacząć zarządzać dostępem do zintegrowanych aplikacji w chmurze.

## <a name="what-are-the-azure-ad-licenses"></a>Co to są licencje usługi Azure AD?

Usługi Microsoft Online Business Services, takie jak Microsoft 365 lub Microsoft Azure, wymagają usługi Azure AD w celu zalogowania się i ułatwiają ochronę tożsamości. Jeśli subskrybujesz usługę biznesową online firmy Microsoft, automatycznie uzyskasz dostęp do usługi Azure AD z dostępem do wszystkich bezpłatnych funkcji.

Aby ulepszyć implementację usługi Azure AD, możesz również dodać płatne funkcje, uaktualniając do licencji na Azure Active Directory — wersja Premium P1 lub Premium P2. Płatne licencje na usługę Azure AD są tworzone na podstawie istniejącego bezpłatnego katalogu, zapewniając samoobsługowe, rozszerzone monitorowanie, raportowanie zabezpieczeń i bezpieczny dostęp dla użytkowników mobilnych.

>[!Note]
>Aby zapoznać się z cennikami tych licencji, zobacz [Azure Active Directory — cennik](https://azure.microsoft.com/pricing/details/active-directory/).
>
>Azure Active Directory — wersja Premium P1 i Premium P2 nie są obecnie obsługiwane w Chinach. Aby uzyskać więcej informacji na temat cennika usługi Azure AD, skontaktuj się z [Forum Azure Active Directory](https://azure.microsoft.com/support/community/?product=active-directory).

- **Azure Active Directory — wersja Bezpłatna.** Umożliwia zarządzanie użytkownikami i grupami, synchronizację katalogów lokalnych, podstawowe raporty, samoobsługowe zmiany haseł użytkowników w chmurze oraz logowanie jednokrotne na platformie Azure, Microsoft 365 i wiele popularnych aplikacji SaaS.

- **Azure Active Directory — wersja Premium P1.** Oprócz bezpłatnych funkcji P1 umożliwia również użytkownikom hybrydowym dostęp do zasobów lokalnych i w chmurze. Obsługuje ona również zaawansowaną administrację, taką jak grupy dynamiczne, samoobsługowe zarządzanie grupami, usługa Microsoft Identity Manager (lokalny pakiet do zarządzania tożsamościami i dostępem) oraz funkcje zapisu zwrotnego, które umożliwiają samoobsługowe resetowanie haseł przez użytkowników lokalnych.

- **Azure Active Directory — wersja Premium P2.** Oprócz funkcji wolnych i P1 firma P2 oferuje również [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) w celu zapewnienia użytkownikom dostępu warunkowego do aplikacji i krytycznych danych firmy, a [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) , aby ułatwić odnajdywanie, ograniczanie i monitorowanie administratorów oraz dostęp do zasobów oraz zapewnianie dostępu just in Time w razie potrzeby.

- **Licencje funkcji „Płatność zgodnie z rzeczywistym użyciem”.** Istnieje też możliwość uzyskania licencji dodatkowych funkcji, takich jak usługa Azure Active Directory Business-to-Customer (B2C). Usługa B2C może ułatwić zapewnianie rozwiązań do zarządzania tożsamościami i dostępem dla aplikacji udostępnianych klientom. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Active Directory B2C](../../active-directory-b2c/index.yml).

Aby uzyskać więcej informacji na temat kojarzenia subskrypcji platformy Azure z usługą Azure AD, zobacz [kojarzenie lub Dodawanie subskrypcji platformy Azure do Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) i aby uzyskać więcej informacji na temat przypisywania licencji użytkownikom, zobacz [How to: Assign and Remove Azure Active Directory licenses](license-users-groups.md).

## <a name="which-features-work-in-azure-ad"></a>Które funkcje działają w usłudze Azure AD?

Po wybraniu licencji usługi Azure AD uzyskasz dostęp do niektórych lub wszystkich następujących funkcji w organizacji:

|Kategoria|Opis|
|-------|-----------|
|Zarządzanie aplikacjami|Zarządzanie aplikacjami w chmurze i lokalnymi przy użyciu serwera proxy aplikacji, logowania jednokrotnego, portalu Moje aplikacje (nazywanego też panelem dostępu) i aplikacji typu oprogramowanie jako usługa (SaaS). Aby uzyskać więcej informacji, zobacz temat [Jak zapewnić bezpieczny, zdalny dostęp do aplikacji lokalnych](../manage-apps/application-proxy.md) i [dokumentację zarządzania aplikacjami](../manage-apps/index.yml).|
|Authentication|Zarządzanie samoobsługowym resetowaniem haseł, uwierzytelnianiem wieloskładnikowym, niestandardowymi listami zakazanych haseł i inteligentną blokadą usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [dokumentację uwierzytelniania w usłudze Azure AD](../authentication/index.yml).|
|Usługa Azure Active Directory dla deweloperów|Tworzenie aplikacji, które obsługują logowanie za pomocą wszystkich tożsamości firmy Microsoft oraz uzyskują tokeny w celu wywoływania programu Microsoft Graph, innych interfejsów API firmy Microsoft lub niestandardowych interfejsów API. Aby uzyskać więcej informacji, zobacz [Platforma tożsamości firmy Microsoft (Azure Active Directory dla deweloperów)](../develop/index.yml).|
|Działania między firmami (B2B)|Zarządzanie użytkownikami-gośćmi i partnerami zewnętrznymi przy zachowaniu kontroli nad danymi firmowymi. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Active Directory B2B](../external-identities/index.yml).|
|Działania między firmami i klientami (B2C)|Dostosowywanie i kontrolowanie sposobu tworzenia kont, logowania się i zarządzania profilami przez użytkowników podczas korzystania z aplikacji. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Active Directory B2C](../../active-directory-b2c/index.yml).|
|Dostęp warunkowy|Zarządzanie dostępem do aplikacji w chmurze. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dotyczącą dostępu warunkowego w usłudze Azure AD](../conditional-access/index.yml).|
|Zarządzanie urządzeniami|Zarządzanie sposobem uzyskiwania dostępu do danych firmowych przez urządzenia w chmurze lub lokalne. Aby uzyskać więcej informacji, zobacz [dokumentację zarządzania urządzeniami w usłudze Azure AD](../devices/index.yml).|
|Usługi domenowe|Przyłączanie maszyn wirtualnych platformy Azure do domeny bez używania kontrolerów domeny. Aby uzyskać więcej informacji, zobacz [dokumentację usług Azure AD Domain Services](../../active-directory-domain-services/index.yml).|
|Użytkownicy korporacyjni|Zarządzanie przypisywaniem licencji i dostępem do aplikacji oraz konfigurowanie delegatów za pomocą grup i ról administratora. Aby uzyskać więcej informacji, zobacz [dokumentację zarządzania użytkownikami usługi Azure Active Directory](../enterprise-users/index.yml).|
|Tożsamość hybrydowa|Używanie programów Azure Active Directory Connect i Connect Health w celu udostępniania pojedynczej tożsamości użytkownika na potrzeby uwierzytelniania i autoryzacji we wszystkich zasobach, niezależnie od lokalizacji (w chmurze lub lokalnie). Aby uzyskać więcej informacji, zobacz [dokumentację tożsamości hybrydowej](../hybrid/index.yml).|
|Nadzór nad tożsamościami|Zarządzanie tożsamością w organizacji za pośrednictwem mechanizmów kontroli dostępu pracowników, partnerów biznesowych, dostawców, usług i aplikacji. Można też przeprowadzać przeglądy dostępu. Aby uzyskać więcej informacji, zobacz [dokumentację zarządzania tożsamościami w usłudze Azure AD](../governance/identity-governance-overview.md) i temat [Przeglądy dostępu w usłudze Azure AD](../governance/access-reviews-overview.md).|
|Identity Protection|Wykrywanie potencjalnych luk w zabezpieczeniach wpływających na tożsamości w organizacji, konfigurowanie zasad w celu reagowania na podejrzane działania oraz podejmowanie odpowiednich działań w celu rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Usługa Azure AD Identity Protection](../identity-protection/index.yml).|
|Tożsamości zarządzane dla zasobów platformy Azure|Zapewnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD, która umożliwia uwierzytelnianie dowolnych usług uwierzytelniania obsługiwanych przez usługę Azure AD, włącznie z usługą Key Vault. Aby uzyskać więcej informacji, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).|
|Privileged Identity Management (PIM)|Kontrolowanie i monitorowanie dostępu w organizacji oraz zarządzanie nim. Ta funkcja obejmuje dostęp do zasobów w usłudze Azure AD i na platformie Azure oraz w innych usługach online firmy Microsoft, takich jak Microsoft 365 lub Intune. Aby uzyskać więcej informacji, zobacz [Usługa Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Raporty i monitorowanie|Uzyskiwanie szczegółowych informacji dotyczących zabezpieczeń i wzorców użycia w danym środowisku. Aby uzyskać więcej informacji, zobacz [Raporty i monitorowanie w usłudze Azure Active Directory](../reports-monitoring/index.yml).|

## <a name="terminology"></a>Terminologia

Aby lepiej zrozumieć usługę Azure AD i jej dokumentację, zalecamy zapoznanie się z poniższymi postanowieniami.

|Termin lub pojęcie|Opis|
|---------------|-----------|
|Tożsamość| Element, który może zostać uwierzytelniony. Tożsamość może być użytkownikiem z nazwą użytkownika i hasłem. Tożsamości obejmują również aplikacje lub inne serwery, które mogą wymagać uwierzytelnienia za poorednictwem kluczy tajnych lub certyfikatów.|
|Konto| Tożsamość, z którą są skojarzone dane. Nie możesz mieć konta bez tożsamości.|
|Konto usługi Azure AD| Tożsamość utworzona za pomocą usługi Azure AD lub innej usługi firmy Microsoft w chmurze, takiej jak Microsoft 365. Tożsamości są przechowywane w usłudze Azure AD i dostępne dla subskrypcji usług w chmurze organizacji. To konto jest też czasami nazywane kontem służbowym.|
|Administrator konta|Ta klasyczna rola administratora subskrypcji określa właściciela subskrypcji odpowiedzialnego za rozliczenia. Ta rola ma dostęp do [Centrum konta platformy Azure](https://account.azure.com/Subscriptions) i pozwala zarządzać wszystkimi subskrypcjami na koncie. Aby uzyskać więcej informacji, zobacz Role [administratora subskrypcji klasycznej, role platformy Azure i role administratorów usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrator usługi|Ta klasyczna rola administratora subskrypcji umożliwia zarządzanie wszystkimi zasobami platformy Azure, włącznie z dostępem. Ta rola ma takie same uprawnienia dostępu co użytkownik, któremu przypisano rolę właściciela w zakresie subskrypcji. Aby uzyskać więcej informacji, zobacz Role [administratora subskrypcji klasycznej, role platformy Azure i role administratorów usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Właściciel|Ta rola ułatwia zarządzanie wszystkimi zasobami platformy Azure, włącznie z dostępem. Ta rola jest oparta na nowszym systemie autoryzacji o nazwie kontrola dostępu oparta na rolach (Azure RBAC), która zapewnia szczegółowe zarządzanie dostępem do zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz Role [administratora subskrypcji klasycznej, role platformy Azure i role administratorów usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrator globalny usługi Azure AD|Ta rola administratora jest automatycznie przypisywana osobie, która utworzyła dzierżawę usługi Azure AD. Administratorzy globalni mogą wykonywać wszystkie funkcje administracyjne usługi Azure AD i wszystkie usługi, które sfederować do usługi Azure AD, takie jak Exchange Online, SharePoint Online i Skype dla firm Online. Może istnieć wielu administratorów globalnych, ale tylko administratorzy globalni mogą przypisywać użytkownikom role administratora (włącznie z przypisywaniem innych administratorów globalnych). Aby uzyskać więcej informacji o różnych rolach administratorów, zobacz [Administrator role permissions in Azure Active Directory](../roles/permissions-reference.md) (Uprawnienia ról administratorów w usłudze Azure Active Directory).|
|Subskrypcja platformy Azure| Używana do płacenia za usługi platformy Azure w chmurze. Możesz mieć wiele subskrypcji. Są one połączone z kartą kredytową.|
|Dzierżawa platformy Azure| Dedykowane i zaufane wystąpienie usługi Azure AD, które jest tworzone automatycznie, gdy organizacja rejestruje się w ramach subskrypcji usług w chmurze firmy Microsoft, takiej jak Microsoft Azure, Microsoft Intune lub Microsoft 365. Dzierżawa usługi Azure reprezentuje jedną organizację.|
|Pojedyncza dzierżawa| Dzierżawy platformy Azure, które uzyskują dostęp do innych usług w dedykowanym środowisku, są uznawane za jedną dzierżawę.|
|Wiele dzierżaw| Dzierżawy platformy Azure, które uzyskują dostęp do innych usług w środowisku współużytkowanym przez wiele organizacji, są uznawane za wiele dzierżaw.|
|Katalog usługi Azure AD|Każda dzierżawa platformy Azure ma dedykowany, zaufany katalog usługi Azure AD. Katalog usługi Azure AD zawiera użytkowników, grupy i aplikacje dzierżawy oraz jest używany do obsługi funkcji zarządzania tożsamościami i dostępem dla zasobów dzierżawy.|
|Domena niestandardowa|Każdy nowy katalog usługi Azure AD jest dostarczany z początkową nazwą domeny nazwa_domeny.onmicrosoft.com. Oprócz początkowej nazwy, do listy można dodać nazwy domen organizacji, włącznie z nazwami używanymi do prowadzenia działalności firmy i uzyskiwania dostępu do zasobów organizacji przez użytkowników. Dodanie niestandardowych nazw domen ułatwia tworzenie nazw użytkowników takich jak alain@contoso.com, które są znajome dla użytkowników.|
|Konto Microsoft (nazywane też kontem MSA)|Konta osobiste zapewniające dostęp do produktów firmy Microsoft i usług w chmurze ukierunkowanych na konsumenta, takich jak Outlook, OneDrive, Xbox LIVE lub Microsoft 365. Konto Microsoft jest tworzone i przechowywane w systemie kont tożsamości konsumentów firmy Microsoft.|

## <a name="next-steps"></a>Następne kroki

- [Rejestrowanie w usłudze Azure Active Directory — wersja Premium](active-directory-get-started-premium.md)

- [Kojarzenie subskrypcji platformy Azure z usługą Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Lista kontrolna dotycząca wdrażania funkcji usługi Azure Active Directory Premium P2](active-directory-deployment-checklist-p2.md)
