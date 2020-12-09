---
title: Zabezpieczanie praktyk dostępu dla administratorów w usłudze Azure AD | Microsoft Docs
description: Upewnij się, że uprawnienia dostępu administracyjnego i administratora organizacji są bezpieczne. Dla architektów systemów i specjalistów IT, którzy konfigurują usługi Azure AD, Azure i Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a7304ffbb7718205ead48e27989d4a169375997
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861990"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD

Bezpieczeństwo zasobów firmy zależy od integralności uprzywilejowanych kont, które administrują systemami IT. Cybernetycznymi — atakujący wykorzystują ataki kradzieży poświadczeń w celu uzyskania dostępu do poufnych danych na kontach administratorów i innym dostępie uprzywilejowanym.

W przypadku usług w chmurze, zapobiegania i odpowiedzi są wspólne obowiązki dostawcy usług w chmurze i klienta. Aby uzyskać więcej informacji na temat najnowszych zagrożeń dla punktów końcowych i chmury, zobacz [Raport analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report). Ten artykuł może pomóc w opracowaniu planu w kierunku zamykania luk między bieżącymi planami i wskazówkami opisanymi tutaj.

> [!NOTE]
> Firma Microsoft dokłada starań, aby zapewnić najwyższy poziom zaufania, przejrzystości, zgodności ze standardami i zgodność z przepisami. Dowiedz się więcej o tym, w jaki sposób zespół reagowania na zdarzenia globalne firmy Microsoft zmniejsza skutki ataków na usługi Cloud Services oraz jak zabezpieczenia są wbudowane w produkty firmy Microsoft i usługi w chmurze w [Centrum zaufania firmy Microsoft — informacje o zabezpieczeniach](https://www.microsoft.com/trustcenter/security) i zgodności firmy Microsoft w [Centrum zaufania firmy Microsoft — zgodność](https://www.microsoft.com/trustcenter/compliance).

Tradycyjnie zabezpieczenia organizacyjne koncentrują się na wejściu i punktach wyjścia sieci jako obwodu zabezpieczeń. Jednak aplikacje SaaS i urządzenia osobiste w Internecie wprowadziły takie podejście mniej efektywne. W usłudze Azure AD zamienimy obwód zabezpieczeń sieci z uwierzytelnianiem w warstwie tożsamości organizacji, z użytkownikami przypisanymi do uprzywilejowanych ról administracyjnych w formancie. Ich dostęp musi być chroniony, niezależnie od tego, czy środowisko działa lokalnie, w chmurze czy w środowisku hybrydowym.

Zabezpieczanie uprzywilejowanego dostępu wymaga wprowadzenia zmian:

* Procesy, praktyki administracyjne i zarządzanie wiedzą
* Składniki techniczne, takie jak obrona hosta, ochrona kont i zarządzanie tożsamościami

Zabezpiecz dostęp uprzywilejowany w sposób, który jest zarządzany i raportowany w usługach firmy Microsoft. Jeśli masz lokalne konta administratorów, zapoznaj się ze wskazówkami dotyczącymi lokalnego i hybrydowego dostępu uprzywilejowanego w Active Directory na [zabezpieczenia uprzywilejowanego dostępu](/windows-server/identity/securing-privileged-access/securing-privileged-access).

> [!NOTE]
> Wskazówki zawarte w tym artykule odnoszą się głównie do funkcji Azure Active Directory, które znajdują się w Azure Active Directory — wersja Premium planach P1 i P2. Azure Active Directory — wersja Premium P2 jest zawarty w pakiecie EMS E5 i pakiet Microsoft 365 E5. W tych wskazówkach przyjęto, że organizacja posiada już Azure AD — wersja Premium licencje na P2. Jeśli nie masz tych licencji, niektóre wskazówki mogą nie być stosowane w organizacji. Ponadto w tym artykule termin Administrator globalny (lub Administrator globalny) ma takie samo znaczenie jak "administrator firmy" lub "Administrator dzierżawy".

## <a name="develop-a-roadmap"></a>Opracowywanie planu

Firma Microsoft zaleca, aby opracować i postępować zgodnie z planem w celu zabezpieczenia uprzywilejowanego dostępu przed osobami atakującymi cybernetycznymi. Możesz zawsze dostosować plan, aby uwzględnić istniejące możliwości i konkretne wymagania w organizacji. Każdy etap planu powinien podnieść koszt i trudności źródłami ataków dostępu uprzywilejowanego do zasobów lokalnych, chmurowych i hybrydowych. Firma Microsoft zaleca cztery poniższe etapy. Najpierw Zaplanuj najwydajniejsze i najszybsze implementacje. Ten artykuł może być twoim przewodnikiem w oparciu o doświadczenia firmy Microsoft związane z implementacją cybernetycznymi i odpowiedzią na ataki. Osie czasu dla tego planu są przybliżami.

![Etapy planu z liniami czasu](./media/security-planning/roadmap-timeline.png)

* Etap 1 (24-48 godzin): elementy krytyczne, które zalecamy od razu

* Etap 2 (2-4 tygodnie): eliminowanie najczęściej używanych technik ataków

* Etap 3 (1-3 miesięcy): budowanie widoczności i tworzenie pełnej kontroli działania administratora

* Etap 4 (sześć miesięcy i więcej): Kontynuuj tworzenie zabezpieczeń, aby zwiększyć ochronę platformy zabezpieczeń

Ta struktura przewodnika jest przeznaczona do maksymalizowania użycia technologii firmy Microsoft, które mogły już zostać wdrożone. Weź pod uwagę możliwość sprzedaży do wszystkich narzędzi zabezpieczeń od innych dostawców wdrożonych lub rozważających wdrażanie.

## <a name="stage-1-critical-items-to-do-right-now"></a>Etap 1: elementy krytyczne do zrobienia teraz

![Najpierw etap 1 najważniejszych elementów do wykonania](./media/security-planning/stage-one.png)

Etap 1 planu koncentruje się na kluczowych zadaniach, które są szybkie i łatwe do wdrożenia. Zalecamy wykonanie tych zaledwie kilku elementów w ciągu pierwszych 24-48 godzin w celu zapewnienia podstawowego poziomu zabezpieczeń uprzywilejowanego dostępu. Ten etap bezpiecznego planu dostępu uprzywilejowanego ma następujące akcje:

### <a name="general-preparation"></a>Ogólne przygotowanie

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Włącz Azure AD Privileged Identity Management

Zalecamy włączenie Azure AD Privileged Identity Management (PIM) w środowisku produkcyjnym usługi Azure AD. Po włączeniu usługi PIM będziesz otrzymywać wiadomości e-mail z powiadomieniami o zmianach roli uprzywilejowanego dostępu. Powiadomienia zapewniają wczesne ostrzeżenie, gdy dodatkowi użytkownicy są dodawani do ról o wysokim poziomie uprawnień.

Azure AD Privileged Identity Management jest zawarty w Azure AD — wersja Premium P2 lub EMS E5. Aby pomóc w ochronie dostępu do aplikacji i zasobów lokalnie i w chmurze, zarejestruj się, aby uzyskać [Enterprise Mobility + Security bezpłatny 90-dniową wersję próbną](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Azure AD Privileged Identity Management i Azure AD Identity Protection monitorują aktywność zabezpieczeń przy użyciu raportów, inspekcji i alertów usługi Azure AD.

Po włączeniu Azure AD Privileged Identity Management:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta, które jest administratorem globalnym organizacji produkcyjnej usługi Azure AD.

2. Aby wybrać organizację usługi Azure AD, w której chcesz korzystać z Privileged Identity Management, wybierz swoją nazwę użytkownika w prawym górnym rogu Azure Portal.

3. W menu Azure Portal wybierz pozycję **wszystkie usługi** i przefiltruj listę dla **Azure AD Privileged Identity Management**.

4. Otwórz Privileged Identity Management z listy **wszystkie usługi** i przypnij ją do pulpitu nawigacyjnego.

Upewnij się, że pierwsza osoba, która ma korzystać z programu PIM w organizacji, jest przypisana do ról **administrator zabezpieczeń** i **administrator ról uprzywilejowanych** . Tylko Administratorzy ról uprzywilejowanych mogą zarządzać przypisaniami użytkowników w usłudze Azure AD. Kreator zabezpieczeń PIM przeprowadzi Cię przez początkowe środowisko odnajdywania i przypisywania. Możesz zamknąć kreatora bez wprowadzania żadnych dodatkowych zmian.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Zidentyfikuj i Kategoryzuj konta, które są w rolach o wysokim poziomie uprawnień

Po włączeniu Azure AD Privileged Identity Management Wyświetl użytkowników, którzy znajdują się w następujących rolach usługi Azure AD:

* Administrator globalny
* Administrator ról uprzywilejowanych
* Administrator programu Exchange
* Administrator programu SharePoint

Jeśli nie masz Azure AD Privileged Identity Management w organizacji, możesz użyć [interfejsu API programu PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember). Zacznij od roli administratora globalnego, ponieważ Administrator globalny ma takie same uprawnienia dla wszystkich usług w chmurze, dla których Twoja organizacja subskrybuje subskrypcję. Te uprawnienia są przyznawane niezależnie od tego, gdzie zostały przypisane: w centrum administracyjnym Microsoft 365, Azure Portal lub w module usługi Azure AD dla programu Microsoft PowerShell.

Usuń wszystkie konta, które nie są już potrzebne w tych rolach. Następnie należy przydzielić pozostałe konta przypisane do ról administratora:

* Przypisane do użytkowników administracyjnych, ale również używane w celach nieadministracyjnych (na przykład osobista poczta e-mail)
* Przypisane do użytkowników administracyjnych i używane tylko do celów administracyjnych
* Współużytkowane przez wielu użytkowników
* W przypadku scenariuszy z dostępem awaryjnym w przypadku awarii
* Dla zautomatyzowanych skryptów
* Dla użytkowników zewnętrznych

#### <a name="define-at-least-two-emergency-access-accounts"></a>Zdefiniuj co najmniej dwa konta dostępu awaryjnego

Istnieje możliwość, że użytkownik zostanie przypadkowo zablokowany z ich roli. Na przykład jeśli federacyjny dostawca tożsamości jest niedostępny, użytkownicy nie mogą zalogować się ani aktywować istniejącego konta administratora. Można przygotować się do przypadkowego braku dostępu, przechowując dwa lub więcej kont dostępu awaryjnego.

Konta dostępu awaryjnego pomagają ograniczyć dostęp uprzywilejowany w ramach organizacji usługi Azure AD. Te konta są wysoce uprzywilejowane i nie są przypisane do określonych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnej w przypadku scenariuszy "Break Glass", w których normalne konta administracyjne nie mogą być używane. Upewnij się, że masz kontrolę i zmniejszyć użycie konta awaryjnego tylko do tego czasu, w którym jest to konieczne.

Oceń konta, które są przypisane lub kwalifikujące się do roli administratora globalnego. Jeśli nie widzisz żadnych kont tylko w chmurze korzystających z \* domeny. onmicrosoft.com (w przypadku dostępu awaryjnego "Break Glass"), utwórz je. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami administracyjnymi dostępu awaryjnego w usłudze Azure AD](security-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Włącz uwierzytelnianie wieloskładnikowe i Zarejestruj wszystkie inne konta administratora niefederacyjnego z wysokim poziomem uprawnień pojedynczego użytkownika

Wymagaj usługi Azure AD Multi-Factor Authentication (MFA) przy logowaniu dla wszystkich użytkowników, którzy są trwale przypisani do co najmniej jednej roli administratora usługi Azure AD: Administrator globalny, administrator ról uprzywilejowanych, administrator programu Exchange i administrator programu SharePoint. Skorzystaj z przewodnika, aby włączyć [uwierzytelnianie wieloskładnikowe (MFA) dla kont administratorów](../authentication/howto-mfa-userstates.md) i upewnić się, że wszyscy użytkownicy zostali zarejestrowani w usłudze [https://aka.ms/mfasetup](https://aka.ms/mfasetup) . Więcej informacji można znaleźć w sekcji Krok 2 i krok 3 przewodnika [Ochrona dostępu do danych i usług w Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>Etap 2: eliminowanie często używanych ataków

![Etap 2 — eliminowanie często używanych ataków](./media/security-planning/stage-two.png)

Etap 2 planu koncentruje się na ograniczeniu najczęściej używanych technik ataków kradzieży i nadużycia poświadczeń i może być zaimplementowany przez około 2-4 tygodni. Ten etap zaawansowanej mapy dostępu uprzywilejowanego obejmuje następujące działania.

### <a name="general-preparation"></a>Ogólne przygotowanie

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Przeprowadzanie spisu usług, właścicieli i administratorów

Zwiększenie "przyłączenie własnego urządzenia" i korzystanie z zasad domowych oraz rozwój łączności bezprzewodowej sprawiają, że jest on krytyczny do monitorowania, kto nawiązuje połączenie z siecią. Inspekcja zabezpieczeń może ujawnić urządzenia, aplikacje i programy w sieci, które nie są obsługiwane przez organizację i która reprezentuje wysokie ryzyko. Aby uzyskać więcej informacji, zobacz temat [Omówienie zarządzania i monitorowania zabezpieczeń platformy Azure](../../security/fundamentals/management-monitoring-overview.md). Upewnij się, że wszystkie poniższe zadania zostały uwzględnione w procesie spisu.

* Zidentyfikuj użytkowników, którzy mają role administracyjne i usługi, którymi mogą zarządzać.
* Użyj usługi Azure AD PIM, aby dowiedzieć się, którzy użytkownicy w organizacji mają dostęp administratora do usługi Azure AD.
* Poza rolami zdefiniowanymi w usłudze Azure AD Microsoft 365 zawiera zestaw ról administratora, które można przypisać do użytkowników w organizacji. Każda rola administratora jest mapowana na typowe funkcje biznesowe i umożliwia osobom w organizacji uprawnienia do wykonywania określonych zadań w [centrum administracyjnym Microsoft 365](https://admin.microsoft.com). Skorzystaj z centrum administracyjnego Microsoft 365, aby dowiedzieć się, którzy użytkownicy w organizacji mają dostęp administratora do Microsoft 365, w tym za pośrednictwem ról, które nie są zarządzane w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Informacje o rolach administratorów Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) i [rozwiązaniach dotyczących zabezpieczeń dla pakietu Office 365](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Wykonaj spis w usługach, na których opiera się organizacja, na przykład Azure, Intune lub Dynamics 365.
* Upewnij się, że Twoje konta są używane do celów administracyjnych:

  * Masz dołączone do nich działające adresy e-mail
  * Zarejestrowano w usłudze Azure AD Multi-Factor Authentication lub korzystać z lokalnej usługi MFA
* Poproszenie użytkowników o uzasadnienie biznesowe dla dostępu administracyjnego.
* Usuń dostęp administratora dla tych użytkowników i usług, które ich nie potrzebują.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identyfikowanie kont Microsoft w rolach administracyjnych, które muszą zostać przełączone na konta służbowe

Jeśli pierwotni Administratorzy globalni ponownie korzystają z istniejących poświadczeń konto Microsoft po rozpoczęciu korzystania z usługi Azure AD, Zastąp konta Microsoft własnymi lub synchronizowanymi kontami w chmurze.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Upewnij się, że konta użytkowników i przekazywanie poczty są przekazywane na konta administratora globalnego

Osobiste konta e-mail są regularnie odwiedzane przez osoby atakujące cybernetycznymi, co stanowi zagrożenie, że osobiste adresy e-mail nie są akceptowalne dla kont administratorów globalnych. Aby pomóc w oddzieleniu ryzyka internetowego od uprawnień administracyjnych, Utwórz dedykowane konta dla każdego użytkownika z uprawnieniami administracyjnymi.

* Należy utworzyć osobne konta dla użytkowników, aby wykonywać globalne zadania administracyjne
* Upewnij się, że administratorzy globalni nie otwierają przypadkowo wiadomości e-mail ani nie uruchamiają programów z kontami administratorów
* Upewnij się, że te konta są przekazywane pocztą e-mail do działającej skrzynki pocztowej

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Upewnij się, że hasła kont administracyjnych zostały ostatnio zmienione

Upewnij się, że wszyscy użytkownicy zarejestrowali się w swoich kontach administracyjnych i w ciągu ostatnich 90 dni zmieniły swoje hasła. Sprawdź również, czy hasła zostały ostatnio zmienione przez wszystkie konta udostępnione.

#### <a name="turn-on-password-hash-synchronization"></a>Włącz synchronizację skrótów haseł

Azure AD Connect synchronizuje skrót skrótu hasła użytkownika z lokalnej Active Directory do organizacji usługi Azure AD opartej na chmurze. Możesz użyć synchronizacji skrótów haseł jako kopii zapasowej, jeśli używasz Federacji z Active Directory Federation Services (AD FS). Ta kopia zapasowa może być przydatna, jeśli lokalne serwery Active Directory lub AD FS są tymczasowo niedostępne.

Synchronizacja skrótów haseł umożliwia użytkownikom logowanie się do usługi przy użyciu tego samego hasła, które są używane do logowania się do lokalnego wystąpienia Active Directory. Synchronizacja skrótów haseł umożliwia usłudze Identity Protection wykrywanie złamanych poświadczeń przez porównanie skrótów haseł z hasłami znanymi jako zagrożone. Aby uzyskać więcej informacji, zobacz [implementowanie synchronizacji skrótów haseł przy użyciu synchronizacji Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników w uprzywilejowanych rolach i narażonych użytkowników

Usługa Azure AD zaleca korzystanie z uwierzytelniania wieloskładnikowego (MFA) dla wszystkich użytkowników. Należy pamiętać, że użytkownicy, którzy mają znaczny wpływ, w przypadku naruszenia bezpieczeństwa konta (na przykład funkcjonariuszy finansów). Usługa MFA zmniejsza ryzyko ataku ze względu na złamane hasło.

Włącz:

* Uwierzytelnianie [wieloskładnikowe przy użyciu zasad dostępu warunkowego](../authentication/howto-mfa-getstarted.md) dla wszystkich użytkowników w organizacji.

W przypadku korzystania z funkcji Windows Hello dla firm wymagane jest spełnienie wymagań usługi MFA przy użyciu funkcji logowania do usługi Windows Hello. Aby uzyskać więcej informacji, zobacz Funkcja [Windows Hello](/windows/uwp/security/microsoft-passport).

#### <a name="configure-identity-protection"></a>Konfigurowanie ochrony tożsamości

Azure AD Identity Protection to narzędzie do monitorowania i raportowania oparte na algorytmach, które wykrywa potencjalne luki w zabezpieczeniach, które mają wpływ na tożsamości w organizacji. Można skonfigurować automatyczne odpowiedzi na te wykryte podejrzane działania i podjąć odpowiednie działania w celu ich rozwiązania. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection (Ochrona tożsamości w usłudze Azure Active Directory)](../identity-protection/overview-identity-protection.md).

#### <a name="obtain-your-microsoft-365-secure-score-if-using-microsoft-365"></a>Uzyskaj Microsoft 365 zabezpieczonych wyników (Jeśli używasz Microsoft 365)

Nastąpi przeprowadzenie oceny ustawień i działań dla usług Microsoft 365, z których korzystasz, i porównuje je z linią bazową ustanowioną przez firmę Microsoft. Wyniki są uzyskiwane w oparciu o sposób wyrównany do zasad zabezpieczeń. Każdy, kto ma uprawnienia administratora do subskrypcji usługi Microsoft 365 Business Standard lub Enterprise, może uzyskać dostęp do bezpiecznego wyniku pod adresem [https://securescore.office.com](https://securescore.office.com/) .

#### <a name="review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365"></a>Zapoznaj się ze wskazówkami dotyczącymi zabezpieczeń i zgodności Microsoft 365 (Jeśli używasz Microsoft 365)

[Plan zabezpieczeń i zgodności](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) przedstawia podejście dla klienta pakietu Office 365 w celu skonfigurowania pakietu Office 365 i włączenia innych możliwości usług EMS. Następnie zapoznaj się z krokami 3-6, jak [chronić dostęp do danych i usług w Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) oraz jak [monitorować zabezpieczenia i zgodność w programie Microsoft 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-microsoft-365-activity-monitoring-if-using-microsoft-365"></a>Skonfiguruj monitorowanie działania Microsoft 365 (jeśli jest używany Microsoft 365)

Monitoruj organizację dla użytkowników korzystających z usługi Microsoft 365, aby identyfikować pracowników, którzy mają konto administratora, ale mogą nie potrzebować dostępu Microsoft 365, ponieważ nie logują się do tych portali. Aby uzyskać więcej informacji, zobacz [Raporty aktywności w centrum administracyjnym Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Ustalanie właścicieli planu reagowania na zdarzenia/sytuacje awaryjne

Ustanowienie pomyślnej możliwości reakcji na zdarzenie wymaga znacznego zaplanowania i zasobów. Należy stale monitorować cybernetycznymi ataki i ustalać priorytety obsługi zdarzeń. Zbieraj, analizuj i Raportuj dane zdarzenia, aby tworzyć relacje i ustanawiać komunikację z innymi grupami wewnętrznymi i właścicielami planu. Aby uzyskać więcej informacji, zobacz [Centrum zabezpieczeń firmy Microsoft](https://technet.microsoft.com/security/dn440717).

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Bezpieczne konta administracyjne z uprawnieniami lokalnymi, jeśli nie zostały jeszcze wykonane

Jeśli Twoja organizacja Azure Active Directory jest synchronizowana z lokalnym Active Directory, postępuj zgodnie ze wskazówkami w temacie [zabezpieczenia uprzywilejowany dostęp do planu](/windows-server/identity/securing-privileged-access/securing-privileged-access): ten etap obejmuje:

* Tworzenie oddzielnych kont administratorów dla użytkowników, którzy muszą przeprowadzać lokalne zadania administracyjne
* Wdrażanie stacji roboczych uprzywilejowanego dostępu dla administratorów Active Directory
* Tworzenie unikatowych haseł administratora lokalnego dla stacji roboczych i serwerów

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do platformy Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Ukończ spis subskrypcji

Użyj witryny Enterprise Portal i Azure Portal, aby identyfikować subskrypcje w organizacji, które obsługują aplikacje produkcyjne.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Usuń konta Microsoft z ról administratora

Konta Microsoft z innych programów, takich jak Xbox, Live i Outlook, nie powinny być używane jako konta administratorów dla subskrypcji organizacji. Usuń stan administratora z wszystkich kont Microsoft i Zastąp ciąg usługą Azure AD (na przykład chris@contoso.com ) kontami służbowymi. W celach administracyjnych zależą od kont, które są uwierzytelniane w usłudze Azure AD, a nie w innych usługach.

#### <a name="monitor-azure-activity"></a>Monitorowanie aktywności platformy Azure

Dziennik aktywności platformy Azure zawiera historię zdarzeń na poziomie subskrypcji na platformie Azure. Zawiera ona informacje o tym, kto utworzył, zaktualizował i usunął zasoby oraz kiedy wystąpiły te zdarzenia. Aby uzyskać więcej informacji, zobacz [Inspekcja i odbieranie powiadomień o ważnych akcjach w ramach subskrypcji platformy Azure](../../azure-monitor/platform/alerts-activity-log.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do innych aplikacji w chmurze za pośrednictwem usługi Azure AD

#### <a name="configure-conditional-access-policies"></a>Konfigurowanie zasad dostępu warunkowego

Przygotuj zasady dostępu warunkowego dla aplikacji lokalnych i hostowanych w chmurze. Jeśli masz użytkowników dołączonych do urządzeń w miejscu pracy, uzyskaj więcej informacji [na temat konfigurowania dostępu warunkowego lokalnego przy użyciu Azure Active Directory rejestracji urządzeń](../../active-directory-b2c/overview.md).

## <a name="stage-3-take-control-of-admin-activity"></a>Etap 3: Przejmij kontrolę nad aktywnością administratora

![Etap 3: Przejmij kontrolę nad aktywnością administratora](./media/security-planning/stage-three.png)

Etap 3 kompiluje środki zaradcze z etapu 2 i powinien zostać wdrożony w ciągu około 1-3 miesięcy. Ten etap zaawansowanej mapy dostępu uprzywilejowanego obejmuje następujące składniki.

### <a name="general-preparation"></a>Ogólne przygotowanie

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Wykonaj przegląd dostępu użytkowników w rolach administratorów

Więcej użytkowników firmowych uzyskuje dostęp uprzywilejowany za poorednictwem usług w chmurze, co może prowadzić do niezarządzanego dostępu. Obecnie użytkownicy mogą stać się administratorami globalnymi Microsoft 365, administratorami subskrypcji platformy Azure lub mieć dostęp administratora do maszyn wirtualnych lub za pośrednictwem aplikacji SaaS.

Organizacja powinna mieć wszystkich pracowników, którzy obsługują zwykłe transakcje biznesowe jako nieuprzywilejowani użytkownicy, a następnie udzielać praw administratora tylko w razie konieczności. Ukończ przeglądy dostępu, aby zidentyfikować i potwierdzić użytkowników, którzy są uprawnieni do aktywacji uprawnień administratora.

Zalecamy wykonanie następujących czynności:

1. Określ użytkowników, którzy są administratorami usługi Azure AD, włączaj dostęp administratora w czasie just-in-Time i zabezpieczenia oparte na rolach.
2. Przekonwertuj użytkowników, którzy nie mają jasnego uzasadnienia dla uprzywilejowanego dostępu administratora do innej roli (jeśli nie masz kwalifikującej się roli, usuń je).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Kontynuuj wprowadzanie silniejszych uwierzytelnień dla wszystkich użytkowników

Wymagaj, aby wysoce narażony użytkownicy mieli nowoczesne, silne uwierzytelnianie, takie jak Azure AD MFA lub Windows Hello. Przykłady wysoce narażonych użytkowników obejmują:

* C-Suite Executives
* Kierownicy wysokiego poziomu
* Krytyczne dla działu IT i bezpieczeństwa

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Używanie dedykowanych stacji roboczych do administrowania usługą Azure AD

Osoby atakujące mogą próbować kierować kontami uprzywilejowanymi, aby mogły zakłócać integralność i autentyczność danych. Często używają złośliwego kodu, który zmienia logikę programu, lub poddaje administratorowi uprawnienia do podania poświadczeń. Stacje robocze z dostępem uprzywilejowanym (PAW, Privileged Access Workstation) zapewniają dedykowany system operacyjny do realizacji zadań poufnych, który jest zabezpieczony przed atakami internetowymi i wektorami zagrożenia. Oddzielenie tych wrażliwych zadań i kont z codziennych stacji roboczych użytkowania i urządzeń zapewnia silną ochronę przed:

* Ataki wyłudzania informacji
* Luki w zabezpieczeniach i systemach operacyjnych
* Ataki personifikacji
* Ataki kradzieży poświadczeń, takie jak rejestrowanie naciśnięć klawiszy, przekazywanie skrótów i przekazywanie biletów

Wdrażając stacje robocze dostępu uprzywilejowanego, można zmniejszyć ryzyko wprowadzenia przez administratorów poświadczeń w środowisku klasycznym, które nie zostało zaostrzone. Aby uzyskać więcej informacji, zobacz [stacje robocze dostępu uprzywilejowanego](/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Przejrzyj Narodowy Instytut standardów i zaleceń dotyczących technologii związanych z obsługą zdarzeń

Narodowy Instytut standardów i technologii (NIST) zawiera wytyczne dotyczące obsługi zdarzeń, szczególnie w przypadku analizowania danych dotyczących zdarzeń i określania odpowiedniej odpowiedzi dla każdego zdarzenia. Aby uzyskać więcej informacji, zobacz [Przewodnik dotyczący obsługi zdarzeń zabezpieczeń komputera (NIST) (SP 800-61, wersja 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementowanie Privileged Identity Management (PIM) na potrzeby JIT w dodatkowych rolach administracyjnych

Aby uzyskać Azure Active Directory, użyj funkcji [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) . Ograniczona czasowo aktywacja uprzywilejowanych ról działa przez umożliwienie:

* Aktywuj uprawnienia administratora, aby wykonać określone zadanie
* Wymuś uwierzytelnianie wieloskładnikowe w trakcie procesu aktywacji
* Korzystanie z alertów w celu informowania administratorów o zmianach poza pasmem
* Zezwól użytkownikom na zachowanie uprzywilejowanego dostępu przez wstępnie skonfigurowany czas
* Zezwalaj administratorom zabezpieczeń na:

  * Odkryj wszystkie uprzywilejowane tożsamości
  * Wyświetl raporty inspekcji
  * Utwórz przeglądy dostępu, aby zidentyfikować wszystkich użytkowników, którzy mają uprawnienia do aktywacji uprawnień administratora

Jeśli korzystasz już z Azure AD Privileged Identity Management, Dostosuj przedziały czasu dla uprawnień ograniczonych czasowo (na przykład okna obsługi).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Określanie zagrożeń dla protokołów logowania opartych na hasłach (w przypadku korzystania z usługi Exchange Online)

Zalecamy zidentyfikowanie wszystkich potencjalnych użytkowników, którzy mogą mieć negatywny wpływ na organizację, jeśli ich poświadczenia zostały naruszone. W przypadku tych użytkowników należy wprowadzić silne wymagania dotyczące uwierzytelniania i korzystać z dostępu warunkowego usługi Azure AD, aby uniemożliwić im logowanie do poczty e-mail przy użyciu nazwy użytkownika i hasła. Można zablokować [starsze uwierzytelnianie przy użyciu dostępu warunkowego](../conditional-access/block-legacy-authentication.md), a [uwierzytelnianie podstawowe można zablokować](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) za pośrednictwem usługi Exchange Online.

#### <a name="complete-a-roles-review-assessment-for-microsoft-365-roles-if-using-microsoft-365"></a>Wykonaj ocenę oceny ról Microsoft 365 (jeśli jest używana Microsoft 365)

Oceń, czy wszyscy użytkownicy Administratorzy znajdują się w poprawnych rolach (Usuń i ponownie Przypisz zgodnie z tą oceną).

#### <a name="review-the-security-incident-management-approach-used-in-microsoft-365-and-compare-with-your-own-organization"></a>Zapoznaj się z podejściem do zarządzania zdarzeniami zabezpieczeń używanym w Microsoft 365 i porównaj się z własną organizacją

Możesz pobrać ten raport z [zarządzania zdarzeniami zabezpieczeń w Microsoft 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Kontynuuj Zabezpieczanie lokalnych kont administracyjnych uprzywilejowanych

Jeśli Azure Active Directory jest podłączony do Active Directory lokalnych, postępuj zgodnie ze wskazówkami w [przewodniku dotyczącego dostępu uprzywilejowanego zabezpieczeń](/windows-server/identity/securing-privileged-access/securing-privileged-access): etap 2. Na tym etapie:

* Wdróż stacje robocze dostępu uprzywilejowanego dla wszystkich administratorów
* Wymaganie uwierzytelniania wieloskładnikowego
* Aby obsłużyć konserwację kontrolera domeny, należy użyć wystarczającej wartości administratora, co zmniejsza podatność na ataki domen
* Wdróż zaawansowaną ocenę zagrożeń na potrzeby wykrywania ataków

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do platformy Azure

#### <a name="establish-integrated-monitoring"></a>Ustanów zintegrowane monitorowanie

[Azure Security Center](../../security-center/security-center-introduction.md):

* Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure
* Pomaga wykrywać zagrożenia, które w przeciwnym razie mogą być niezauważalne
* Współpracuje z szeroką gamę rozwiązań zabezpieczeń

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Sporządzanie spisu kont uprzywilejowanych w hostowanej Virtual Machines

Zazwyczaj nie trzeba udzielać użytkownikom nieograniczonego dostępu do wszystkich subskrypcji lub zasobów platformy Azure. Za pomocą ról administratorów usługi Azure AD Udziel dostępu tylko użytkownikom, którzy muszą wykonywać swoje zadania. Role administratora usługi Azure AD umożliwiają jednemu administratorowi zarządzanie tylko maszynami wirtualnymi w ramach subskrypcji, a inne mogą zarządzać bazami danych SQL w ramach tej samej subskrypcji. Aby uzyskać więcej informacji, zobacz [co to jest kontrola dostępu oparta na rolach na platformie Azure](../../active-directory-b2c/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementacja usług PIM dla administratorów usługi Azure AD

Zarządzanie tożsamościami uprzywilejowanymi przy użyciu ról administratorów usługi Azure AD umożliwia zarządzanie dostępem do zasobów platformy Azure, kontrolowanie ich i monitorowanie. Korzystanie z programu PIM chroni przed obniżeniem czasu ekspozycji uprawnień i zwiększeniem widoczności do użycia w raportach i alertach. Aby uzyskać więcej informacji, zobacz [co to jest Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Użyj integracji dzienników platformy Azure w celu wysłania odpowiednich dzienników platformy Azure do systemów SIEM

Integracja dzienników platformy Azure umożliwia integrowanie nieprzetworzonych dzienników z zasobów platformy Azure z istniejącymi systemami informacji o zabezpieczeniach i zarządzania zdarzeniami (SIEM) w organizacji. [Integracja dzienników platformy Azure](/previous-versions/azure/security/fundamentals/azure-log-integration-overview) zbiera zdarzenia systemu Windows z dzienników Podgląd zdarzeń systemu Windows i zasobów platformy Azure z:

* Dzienniki aktywności platformy Azure
* Alerty usługi Azure Security Center
* Dzienniki zasobów platformy Azure

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do innych aplikacji w chmurze za pośrednictwem usługi Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementowanie aprowizacji użytkowników dla połączonych aplikacji

Usługa Azure AD umożliwia automatyzację tworzenia i obsługi tożsamości użytkowników w aplikacjach w chmurze, takich jak Dropbox, Salesforce i usługi ServiceNow. Aby uzyskać więcej informacji, zobacz [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS przy użyciu usługi Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrowanie ochrony informacji

Microsoft Cloud App Security umożliwia badanie plików i Ustawianie zasad na podstawie Azure Information Protection etykiet klasyfikacji, co zapewnia lepszy wgląd w dane w chmurze i kontrolę nad nimi. Skanuj i Klasyfikuj pliki w chmurze i stosuj etykiety usługi Azure Information Protection. Aby uzyskać więcej informacji, zobacz [Azure Information Protection Integration](/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurowanie dostępu warunkowego

Skonfiguruj dostęp warunkowy na podstawie grupy, lokalizacji i czułości aplikacji dla [aplikacji SaaS](https://azure.microsoft.com/overview/what-is-saas/) i aplikacji połączonych z usługą Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitoruj aktywność w połączonych aplikacjach w chmurze

Zalecamy używanie [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) , aby zapewnić, że dostęp użytkowników jest również chroniony w połączonych aplikacjach. Ta funkcja zabezpiecza dostęp przedsiębiorstwa do aplikacji w chmurze i zabezpiecza konta administratorów, umożliwiając:

* Poszerzanie widoczności i kontroli do aplikacji w chmurze
* Tworzenie zasad dostępu, działań i udostępniania danych
* Automatyczne identyfikowanie działań ryzykownych, nietypowych zachowań i zagrożeń
* Zapobieganie wyciekom danych
* Minimalizacja ryzyka i zautomatyzowanego zapobiegania zagrożeniom i wymuszania zasad

Agent Cloud App Security SIEM integruje Cloud App Security z serwerem SIEM w celu umożliwienia scentralizowanego monitorowania alertów i działań Microsoft 365. Jest on uruchamiany na serwerze i pobiera alerty i działania z Cloud App Security i strumieniuje je na serwerze SIEM. Aby uzyskać więcej informacji, zobacz [Siem Integration](/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses"></a>Etap 4. kontynuacja tworzenia zabezpieczeń

![Etap 4: przyjęcie aktywnego stan zabezpieczeń](./media/security-planning/stage-four.png)

Etap 4 planu powinien być zaimplementowany przez sześć miesięcy i więcej. Ukończ swój plan, aby wzmocnić swoje uprzywilejowane zabezpieczenia przed potencjalnymi atakami, które są znane dzisiaj. W przypadku zagrożeń bezpieczeństwa w przyszłości zalecamy wyświetlanie zabezpieczeń jako procesu trwającego w celu podniesienia kosztów i skrócenia częstotliwości sukcesu źródłami ataków dla danego środowiska.

Zabezpieczanie uprzywilejowanego dostępu jest ważne, aby ustanowić gwarancje bezpieczeństwa dla zasobów firmy. Należy jednak być częścią kompletnego programu zabezpieczającego, który zapewnia bieżące gwarancje bezpieczeństwa. Ten program powinien zawierać takie elementy, jak:

* Zasady
* Operacje
* Bezpieczeństwo informacji
* Serwery
* Aplikacje
* Komputery
* Urządzenia
* Sieć szkieletowa chmury

Podczas zarządzania kontami uprzywilejowanego dostępu zalecamy stosowanie następujących zasad:

* Upewnij się, że administratorzy robią swoją codzienny biznes jako nieuprzywilejowani użytkownicy
* Udziel uprzywilejowanego dostępu tylko wtedy, gdy jest to konieczne, i usuń go później (just-in-Time)
* Zachowywanie dzienników aktywności inspekcji odnoszących się do kont uprzywilejowanych

Aby uzyskać więcej informacji na temat tworzenia kompletnego planu zabezpieczeń, zobacz [zasoby architektury IT w chmurze firmy Microsoft](https://almbok.com/office365/microsoft_cloud_it_architecture_resources). Aby współpracować z usługami firmy Microsoft, aby pomóc w zaimplementowaniu dowolnej części planu, skontaktuj się z przedstawicielem firmy Microsoft lub zapoznaj się z tematem [Tworzenie krytycznych zabezpieczeń cybernetycznymi w celu ochrony przedsiębiorstwa](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Ten końcowy, ciągły etap zaawansowanej metody dostępu uprzywilejowanego obejmuje następujące składniki.

### <a name="general-preparation"></a>Ogólne przygotowanie

#### <a name="review-admin-roles-in-azure-ad"></a>Przeglądanie ról administratorów w usłudze Azure AD

Ustal, czy obecnie wbudowane role administratora usługi Azure AD są ciągle aktualne i upewnij się, że użytkownicy korzystają tylko z ról, których potrzebują. Za pomocą usługi Azure AD można przypisać oddzielnych administratorów, aby obsługiwały różne funkcje. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról administratorów w Azure Active Directory](permissions-reference.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Przeglądanie użytkowników, którzy mają administrowanie urządzeniami przyłączonymi do usługi Azure AD

Aby uzyskać więcej informacji, zobacz [How to configure hybryded Azure Active Directory Devices Join](../devices/hybrid-azuread-join-plan.md).

#### <a name="review-members-of-built-in-microsoft-365-admin-roles"></a>Przejrzyj członków [wbudowanych ról administratorów Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Pomiń ten krok, jeśli nie używasz Microsoft 365.
‎
#### <a name="validate-incident-response-plan"></a>Sprawdź poprawność planu reagowania na zdarzenia

Aby ulepszyć plan, firma Microsoft zaleca regularne Weryfikowanie, czy plan działa zgodnie z oczekiwaniami:

* Przejdź do istniejącej mapy drogowej, aby zobaczyć, co zostało pominięte
* Na podstawie analizy postmortem należy skorygować istniejące lub zdefiniować nowe rozwiązania
* Upewnij się, że zaktualizowane plany i praktyki reagowania na incydenty są dystrybuowane w całej organizacji.


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do platformy Azure 

Ustal, czy musisz [przenieść własność subskrypcji platformy Azure na inne konto](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Szkło ze znakami": co zrobić w przypadku awarii

![Konta na potrzeby dostępu do awaryjnego przerwania](./media/security-planning/emergency.jpeg)

1. Powiadamiaj menedżerów kluczy i urzędy zabezpieczeń o informacjach o zdarzeniu.

2. Przejrzyj element PlayBook ataku.

3. Uzyskaj dostęp do kombinacji nazwy użytkownika i hasła konta "Break Glass", aby zalogować się do usługi Azure AD.

4. Uzyskaj pomoc od firmy Microsoft, [otwierając żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Przyjrzyj się [raportom logowania do usługi Azure AD](../reports-monitoring/overview-reports.md). Może wystąpić jakiś czas między zdarzeniem a jego uwzględnieniem w raporcie.

6. W przypadku środowisk hybrydowych w przypadku, gdy infrastruktura lokalna i serwer AD FS nie są dostępne, można tymczasowo przełączać się z uwierzytelniania federacyjnego, aby używały synchronizacji skrótów haseł. Ten przełącznik przywraca Federacji domeny z powrotem do uwierzytelniania zarządzanego, dopóki serwer AD FS nie będzie dostępny.

7. Monitoruj pocztę e-mail dla kont uprzywilejowanych.

8. Pamiętaj, aby zapisać kopie zapasowe odpowiednich dzienników dla potencjalnych śledczej i badań prawnych.

Aby uzyskać więcej informacji na temat sposobu, w jaki Microsoft Office 365 obsługuje zdarzenia związane z zabezpieczeniami, zobacz [zarządzanie zdarzeniami zabezpieczeń w Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-answers-for-securing-privileged-access"></a>Często zadawane pytania: odpowiedzi na potrzeby zabezpieczania dostępu uprzywilejowanego  

**P:** Co zrobić, jeśli jeszcze nie zaimplementowano żadnych bezpiecznych składników dostępu?

**Odpowiedź:** Zdefiniuj co najmniej dwa konta ze szkła, przypisz uwierzytelnianie wieloskładnikowe do kont administratorów uprzywilejowanych i oddziel konta użytkowników od kont administratorów globalnych.

**P:** Co to jest najpopularniejszy problem, który należy rozwiązać w pierwszej kolejności?

**Odpowiedź:** Upewnij się, że jest wymagane najmocniejsze uwierzytelnianie dla bardzo narażonych osób.

**P:** Co się stanie, jeśli naszych uprzywilejowanych administratorów została zdezaktywowana?

**Odpowiedź:** Utwórz konto administratora globalnego, które jest zawsze aktualne.

**P:** Co się stanie, jeśli istnieje tylko jeden administrator globalny i nie można się z nimi skontaktować?

**Odpowiedź:** Użyj jednego z kont ze szlifem, aby uzyskać natychmiastowy dostęp uprzywilejowany.

**P:** Jak mogę chronić administratorów w mojej organizacji?

**Odpowiedź:** Administratorzy zawsze wykonują codzienne działalności jako standardowe "nieuprzywilejowanych" użytkowników.

**P:** Jakie są najlepsze rozwiązania dotyczące tworzenia kont administratorów w usłudze Azure AD?

**Odpowiedź:** Zarezerwuj uprzywilejowany dostęp dla określonych zadań administracyjnych.

**P:** Jakie narzędzia istnieją do zmniejszenia stałego dostępu administratora?

**Odpowiedź:** Privileged Identity Management (PIM) i role administratorów usługi Azure AD.

**P:** Jaka jest pozycja firmy Microsoft na potrzeby synchronizowania kont administratorów z usługą Azure AD?

**Odpowiedź:** Konta administratorów warstwy 0 są używane tylko w przypadku lokalnych kont usługi AD. Takie konta nie są zwykle synchronizowane z usługą Azure AD w chmurze. Konta administratorów warstwy 0 obejmują konta, grupy i inne zasoby, które mają bezpośrednią lub pośrednią kontrolę administracyjną lokalnego lasu Active Directory, domen, kontrolerów domeny i zasobów.

**P:** Jak zapewnić administratorom możliwość przypisywania losowego dostępu administratora w portalu?

**Odpowiedź:** Używaj kont bez uprawnień dla wszystkich użytkowników i większości administratorów. Zacznij od utworzenia podstawy organizacji, aby określić, które konta administratorów mają być uprzywilejowane. I monitoruj nowo utworzonych użytkowników administracyjnych.

## <a name="next-steps"></a>Następne kroki

* [Centrum zaufania firmy Microsoft dotyczące zabezpieczeń produktów](https://www.microsoft.com/trustcenter/security) — funkcje zabezpieczeń usług i produktów w chmurze firmy Microsoft

* [Centrum zaufania firmy Microsoft — zgodność z](https://www.microsoft.com/trustcenter/compliance/complianceofferings) kompleksowym zestawem zgodności usług w chmurze firmy Microsoft

* [Wskazówki dotyczące oceny ryzyka](https://www.microsoft.com/trustcenter/guidance/risk-assessment) — zarządzanie wymaganiami dotyczącymi zabezpieczeń i zgodności usług w chmurze firmy Microsoft

### <a name="other-microsoft-online-services"></a>Inne usługi online firmy Microsoft

* [Zabezpieczenia Microsoft Intune](https://www.microsoft.com/trustcenter/security/intune-security) — usługa Intune zapewnia zarządzanie urządzeniami przenośnymi, zarządzanie aplikacjami mobilnymi oraz możliwości zarządzania komputerami z chmury.

* [Microsoft dynamics 365 Security](https://www.microsoft.com/trustcenter/security/dynamics365-security) — Dynamics 365 to rozwiązanie oparte na chmurze firmy Microsoft, które łączy funkcje zarządzania relacjami z klientami (CRM) i Enterprise Resource Planning (ERP).
