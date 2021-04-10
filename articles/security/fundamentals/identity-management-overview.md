---
title: Funkcje zabezpieczeń platformy Azure, które pomagają zarządzać tożsamościami | Microsoft Docs
description: Dowiedz się więcej na temat podstawowych funkcji zabezpieczeń platformy Azure, które ułatwiają zarządzanie tożsamościami. Zobacz informacje na temat tematów, takich jak logowanie jednokrotne i zwrotny serwer proxy.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2021
ms.author: terrylan
ms.openlocfilehash: 1bb4f22c6ef114a224597ede2b053d076aba5997
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065841"
---
# <a name="azure-identity-management-security-overview"></a>Omówienie zabezpieczeń zarządzania tożsamościami platformy Azure

 Zarządzanie tożsamościami to proces uwierzytelniania i autoryzacji [podmiotów zabezpieczeń](/windows/security/identity-protection/access-control/security-principals). Obejmuje również kontrolowanie informacji o tych podmiotach zabezpieczeń (tożsamości). Podmioty zabezpieczeń (tożsamości) mogą obejmować usługi, aplikacje, użytkowników, grupy itp. Rozwiązania do zarządzania tożsamościami i dostępem firmy Microsoft pomagają chronić dostęp do aplikacji i zasobów w centrum danych firmy oraz w chmurze. Taka ochrona umożliwia stosowanie dodatkowych poziomów walidacji, takich jak Multi-Factor Authentication i zasady dostępu warunkowego. Monitorowanie podejrzanych działań poprzez zaawansowane raportowanie zabezpieczeń, inspekcje i alerty pomaga ograniczyć potencjalne problemy z zabezpieczeniami. [Azure Active Directory — wersja Premium](../../active-directory/fundamentals/active-directory-whatis.md) zapewnia Logowanie jednokrotne (SSO) do tysięcy aplikacji w chmurze jako usługi (SaaS) oraz dostęp do aplikacji sieci Web uruchamianych lokalnie.
 
Korzystając z zalet korzyści z używania Azure Active Directory (Azure AD), możesz:

* Utwórz jedną tożsamość dla każdego użytkownika w całym przedsiębiorstwie hybrydowym i zarządzaj nią, synchronizując użytkowników, grupy i urządzenia. 
* Zapewnianie dostępu logowania jednokrotnego do aplikacji, w tym tysięcy wstępnie zintegrowanych aplikacji SaaS.
* Zabezpieczyć dostęp do aplikacji przez wymuszenie uwierzytelniania wieloskładnikowego na podstawie zasad zarówno dla aplikacji lokalnych, jak i dla aplikacji w chmurze.
* Zapewnij bezpieczny dostęp zdalny do lokalnych aplikacji sieci Web za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD.

Celem tego artykułu jest przedstawienie omówienia podstawowych funkcji zabezpieczeń platformy Azure, które pomagają zarządzać tożsamościami. Udostępniamy również linki do artykułów, które zawierają szczegółowe informacje o każdej z tych funkcji, aby dowiedzieć się więcej.  

Artykuł koncentruje się na następujących podstawowych możliwościach usługi Azure Identity Management:

* Logowanie jednokrotne
* Zwrotny serwer proxy
* Uwierzytelnianie wieloskładnikowe
* Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)
* Monitorowanie zabezpieczeń, alerty i raporty oparte na uczeniu maszynowym
* Zarządzanie tożsamościami i dostępem klientów
* Rejestracja urządzenia
* Privileged Identity Management
* Identity Protection
* Hybrydowe zarządzanie tożsamościami/Azure AD Connect
* Przeglądy dostępu w usłudze Azure AD

## <a name="single-sign-on"></a>Logowanie jednokrotne

Logowanie jednokrotne oznacza, że można uzyskać dostęp do wszystkich aplikacji i zasobów potrzebnych do prowadzenia działalności, logując się tylko raz przy użyciu jednego konta użytkownika. Po zalogowaniu możesz uzyskać dostęp do wszystkich aplikacji, których potrzebujesz, bez konieczności uwierzytelniania (na przykład wpisz hasło).

Wiele organizacji korzysta z aplikacji SaaS, takich jak Microsoft 365, Box i Salesforce, aby zwiększyć produktywność użytkowników. W przeszłości pracownicy działu IT musieli indywidualnie utworzyć i zaktualizować konta użytkowników w każdej aplikacji SaaS, a użytkownicy musieli zapamiętać hasło dla każdej aplikacji SaaS.

Usługa Azure AD rozszerza lokalne środowiska Active Directory do chmury, umożliwiając użytkownikom używanie ich podstawowego konta organizacyjnego do logowania się nie tylko do urządzeń przyłączonych do domeny i zasobów firmy, ale także do wszystkich aplikacji sieci Web i SaaS potrzebnych do ich zadań.

Nie tylko użytkownicy, którzy nie muszą zarządzać wieloma zestawami nazw użytkowników i haseł, mogą udostępniać lub cofać dostęp do aplikacji automatycznie w oparciu o ich grupy organizacyjne i ich stan pracownika. Usługa Azure AD wprowadza zabezpieczenia i kontroli dostępu, za pomocą których można centralnie zarządzać dostępem użytkowników w aplikacjach SaaS.

Więcej informacji:

* [Omówienie logowania jednokrotnego](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Wideo na temat podstawowych uwierzytelnień](https://www.youtube.com/watch?v=fbSVgC8nGz4&feature=emb_title)
* [Seria szybkiego startu w zarządzaniu aplikacjami](../../active-directory/manage-apps/view-applications-portal.md)

## <a name="reverse-proxy"></a>Zwrotny serwer proxy

Usługa Azure serwer proxy aplikacji usługi Azure AD umożliwia publikowanie aplikacji lokalnych, takich jak witryny [programu SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) , [aplikacja sieci Web programu Outlook](/Exchange/clients/outlook-on-the-web/outlook-on-the-web)i aplikacje oparte na [usługach IIS](https://www.iis.net/)w sieci prywatnej i zapewnia bezpieczny dostęp użytkownikom spoza sieci. Serwer proxy aplikacji zapewnia dostęp zdalny i logowanie jednokrotne dla wielu typów lokalnych aplikacji sieci Web z tysiącami aplikacji SaaS obsługiwanych przez usługę Azure AD. Pracownicy mogą zalogować się do aplikacji z domu na swoich urządzeniach i uwierzytelniać się za pośrednictwem tego serwera proxy opartego na chmurze.

Więcej informacji:

* [Włączanie usługi Azure serwer proxy aplikacji usługi Azure AD](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Logowanie jednokrotne przy użyciu serwera proxy aplikacji](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Praca z dostępem warunkowym](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

Usługa Azure AD Multi-Factor Authentication to metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji i dodaje krytyczną drugą warstwę zabezpieczeń do logowania i transakcji użytkownika. Multi-Factor Authentication pomaga chronić dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostego procesu logowania. Zapewnia silne uwierzytelnianie za pomocą różnych opcji weryfikacji: połączeń telefonicznych, wiadomości SMS lub powiadomień aplikacji mobilnej oraz kodów weryfikacyjnych i tokenów OAuth innych firm.

Więcej informacji:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co to jest usługa Azure AD Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Jak działa usługa uwierzytelniania wieloskładnikowego w usłudze Azure AD](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="azure-rbac"></a>Kontrola dostępu na podstawie ról platformy Azure

Azure RBAC to system autoryzacji oparty na Azure Resource Manager, który zapewnia precyzyjne zarządzanie dostępem do zasobów na platformie Azure. Usługa Azure RBAC umożliwia precyzyjne sterowanie poziomem dostępu dostępnym dla użytkowników. Można na przykład ograniczyć użytkownika do zarządzania sieciami wirtualnymi i innym użytkownikom w celu zarządzania wszystkimi zasobami w grupie zasobów. Na platformie Azure można korzystać z kilku ról wbudowanych. Poniżej wymieniono cztery podstawowe role wbudowane. Pierwsze trzy są stosowane do wszystkich typów zasobów.

- [Właściciel](../../role-based-access-control/built-in-roles.md#owner) — ma pełny dostęp do wszystkich zasobów i jest uprawniony do udzielania dostępu innym osobom. 
- [Współautor](../../role-based-access-control/built-in-roles.md#contributor) — może tworzyć wszystkie typy zasobów platformy Azure i zarządzać nimi, ale nie może udzielać dostępu innym osobom.
- [Reader](../../role-based-access-control/built-in-roles.md#reader) — może wyświetlać istniejące zasoby platformy Azure.
- [Administrator dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator) — może zarządzać dostępem użytkowników do zasobów platformy Azure.

Więcej informacji:

* [Co to jest kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
* [Wbudowane role platformy Azure](../../role-based-access-control/built-in-roles.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorowanie zabezpieczeń, alerty i raporty oparte na uczeniu maszynowym

Monitorowanie zabezpieczeń, alerty i raporty oparte na uczeniu maszynowym, które identyfikują niespójne wzorce dostępu, mogą pomóc w ochronie Twojej firmy. Możesz użyć raportów dotyczących dostępu i użycia usługi Azure AD, aby uzyskać wgląd w integralność i bezpieczeństwo katalogu organizacji. Dzięki tym informacjom administrator katalogów może lepiej określić, gdzie mogą być dostępne zagrożenia bezpieczeństwa, aby mogły one odpowiednio zaplanować ograniczenia tych zagrożeń.

W Azure Portal raporty należą do następujących kategorii:

* **Raporty anomalii**: zawierają zdarzenia logowania, które okazały się nietypowe. Naszym celem jest świadome tego działania i umożliwienie określenia, czy zdarzenie jest podejrzane.
* **Raporty zintegrowanej aplikacji**: zapewniają wgląd w sposób używania aplikacji w chmurze w organizacji. Usługa Azure AD oferuje integrację z tysiącami aplikacji w chmurze.
* **Raporty o błędach**: wskazuje błędy, które mogą wystąpić podczas udostępniania kont dla aplikacji zewnętrznych.
* **Raporty dotyczące użytkownika**: wyświetla dane działania logowania urządzenia dla określonego użytkownika.
* **Dzienniki aktywności**: zawierają rekord wszystkich zdarzeń poddawanych inspekcji w ciągu ostatnich 24 godzin, ostatnich 7 dni lub ostatnich 30 dni, a także zmiany działania grupy oraz działania związane z resetowaniem i rejestracją.

Więcej informacji:

* [Wyświetlanie raportów dostępu i użycia](../../active-directory/reports-monitoring/overview-reports.md)
* [Wprowadzenie do raportowania Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)
* [Przewodnik po zgłoszeniu Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)

## <a name="consumer-identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem klientów

Azure AD B2C to usługa zarządzania tożsamościami o wysokiej dostępności dla aplikacji przeznaczonych dla klientów, która skaluje się do setek milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Konsumenci mogą zalogować się do wszystkich aplikacji za pośrednictwem dostosowywalnych środowisk przy użyciu istniejących kont społecznościowych lub przez utworzenie nowych poświadczeń.

W przeszłości deweloperzy aplikacji, którzy chcieli utworzyć konto w celu zarejestrowania klientów i podpisania ich w aplikacjach, zapiszą swój własny kod. Musieli korzystać z lokalnych baz danych lub systemów do przechowywania nazw i haseł użytkowników. Azure AD B2C oferuje organizacji lepszy sposób integrowania zarządzania tożsamościami konsumentów z aplikacjami za pomocą bezpiecznej, opartej na standardach platformy i dużego zestawu rozszerzalnych zasad.

W przypadku korzystania z Azure AD B2C klienci mogą zarejestrować się w aplikacjach przy użyciu istniejących kont społecznościowych (Facebook, Google, Amazon, LinkedIn) lub tworząc nowe poświadczenia (adres e-mail i hasło albo nazwę użytkownika i hasło).

Więcej informacji:

* [Co to jest usługa Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Usługa Azure Active Directory B2C - przegląd: rejestrowanie i logowanie użytkowników w Twoich aplikacjach](../../active-directory-b2c/overview.md)
* [Azure Active Directory B2C Preview: typy aplikacji](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Rejestracja urządzenia

Rejestracja urządzeń w usłudze Azure AD jest podstawą dla scenariuszy [dostępu warunkowego](../../active-directory/devices/device-management-azure-portal.md) opartego na urządzeniach. Po zarejestrowaniu urządzenia usługa rejestracji urządzeń w usłudze Azure AD udostępnia urządzenie tożsamością używaną do uwierzytelniania urządzenia podczas logowania użytkownika. Uwierzytelnionego urządzenia i atrybutów urządzenia można następnie użyć do wymuszania zasad dostępu warunkowego dla aplikacji hostowanych w chmurze i lokalnie.

W połączeniu z rozwiązaniem do zarządzania urządzeniami przenośnymi, takim jak usługa Intune, atrybuty urządzenia w usłudze Azure AD są aktualizowane przy użyciu dodatkowych informacji o urządzeniu. Następnie można utworzyć reguły dostępu warunkowego, które wymuszają dostęp z urządzeń, aby spełniały standardy zabezpieczeń i zgodności.

Więcej informacji:

* [Wprowadzenie do rejestracji urządzeń w usłudze Azure AD](../../active-directory/devices/device-management-azure-portal.md)
* [Automatyczna rejestracja urządzeń w usłudze Azure AD dla urządzeń przyłączonych do domeny systemu Windows](../../active-directory/devices/hybrid-azuread-join-plan.md)
* [Konfigurowanie automatycznej rejestracji urządzeń przyłączonych do domeny systemu Windows przy użyciu usługi Azure AD](../../active-directory/devices/hybrid-azuread-join-plan.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Za pomocą Azure AD Privileged Identity Management można zarządzać, kontrolować i monitorować uprzywilejowane tożsamości oraz uzyskiwać dostęp do zasobów w usłudze Azure AD, a także inne Usługi online firmy Microsoft, takie jak Microsoft 365 i Microsoft Intune.

Czasami użytkownicy muszą wykonywać operacje uprzywilejowane na platformie Azure lub w ramach zasobów Microsoft 365 lub w innych aplikacjach SaaS. Taka konieczność często oznacza, że organizacje muszą udzielić użytkownikom stałego uprzywilejowanego dostępu w usłudze Azure AD. Taki dostęp dotyczy zasobów hostowanych w chmurze, ponieważ organizacje nie mogą wystarczająco dobrze monitorować działania użytkowników z uprawnieniami administratora. Ponadto, jeśli naruszone zostanie konto użytkownika z dostępem uprzywilejowanym, oznacza to, że jedno naruszenie może mieć wpływ na ogólne zabezpieczenia chmury w organizacji. Azure AD Privileged Identity Management pomaga w ograniczeniu ryzyka.

Za pomocą Azure AD Privileged Identity Management można:

* Zobacz, którzy użytkownicy są administratorami usługi Azure AD.
* Włącz dostęp administracyjny do usług firmy Microsoft na żądanie, w trybie just-in-Time (JIT), takich jak Microsoft 365 i Intune.
* Otrzymuj raporty o historii dostępu administratora i zmianach w przypisaniach administratorów.
* Otrzymuj alerty dotyczące dostępu do roli uprzywilejowanej.

Więcej informacji:

* [Co to jest usługa Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Przypisywanie ról w katalogu usługi Azure AD w usłudze PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection to usługa zabezpieczeń, która zapewnia skonsolidowany wgląd w wykrywanie ryzyka i potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości w organizacji. Funkcja ochrony tożsamości korzysta z istniejących funkcji wykrywania anomalii usługi Azure AD, które są dostępne za pomocą raportów anomalii związanych z usługą Azure AD. W ramach programu Identity Protection wprowadzono również nowe typy wykrywania ryzyka, które mogą wykrywać anomalie w czasie rzeczywistym.

Więcej informacji:

* [Usługa Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)
* [Channel 9: usługa Azure AD i tożsamość show: wersja zapoznawcza usługi Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hybrydowe zarządzanie tożsamościami/Azure AD Connect

Rozwiązania firmy Microsoft do obsługi tożsamości obejmują zarówno funkcje lokalne, jak i chmurowe, tworząc jedną tożsamość użytkownika na potrzeby uwierzytelniania i autoryzacji w kontekście wszystkich zasobów, niezależnie od lokalizacji. Nazywamy to tożsamością hybrydową. Azure AD Connect to narzędzie firmy Microsoft, które umożliwia spełnienie wymagań związanych z tożsamością hybrydową. Dzięki temu użytkownicy mogą posługiwać się wspólną tożsamością dla platformy Microsoft 365, platformy Azure i aplikacji SaaS zintegrowanych z usługą Azure AD. Oferuje ono następujące funkcje:

* Synchronizacja
* Integracja AD FS i Federacji
* Przekazuj uwierzytelnianie
* Monitorowanie kondycji

Więcej informacji:

* [Oficjalny dokument tożsamości hybrydowej](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog zespołu usługi Azure AD](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Przeglądy dostępu w usłudze Azure AD

Przeglądy dostępu w usłudze Azure Active Directory (Azure AD) pozwalają organizacjom efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji przedsiębiorstwa i przypisaniami ról uprzywilejowanych.

Więcej informacji:

* [Przeglądy dostępu w usłudze Azure AD](../../active-directory/governance/access-reviews-overview.md)
* [Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu w usłudze Azure AD](../../active-directory/governance/access-reviews-overview.md)