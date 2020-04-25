---
title: Najlepsze rozwiązania dotyczące bezpiecznego dostępu administratora — Azure AD | Microsoft Docs
description: Upewnij się, że uprawnienia dostępu administracyjnego i administratora organizacji są bezpieczne. Dla architektów systemów i specjalistów IT, którzy konfigurują usługi Azure AD, Azure i Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 11/13/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 512efa959ccb78533845cd1f376318394b5c377b
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82129168"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD

Bezpieczeństwo większości lub wszystkich zasobów firmowych w nowoczesnej organizacji zależy od integralności uprzywilejowanych kont, które administrują systemami IT i zarządzają nimi. Złośliwe podmioty, w tym cybernetycznymi — osoby atakujące często kierują konta administratora i inne elementy uprzywilejowanego dostępu, aby próbować szybko uzyskać dostęp do poufnych danych i systemów przy użyciu ataków kradzieży poświadczeń. W przypadku usług w chmurze, zapobiegania i odpowiedzi są wspólne obowiązki dostawcy usług w chmurze i klienta. Aby uzyskać więcej informacji na temat najnowszych zagrożeń dla punktów końcowych i chmury, zobacz [Raport analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report). Ten artykuł może pomóc w opracowaniu planu w kierunku zamykania luk między bieżącymi planami i wskazówkami opisanymi tutaj.

> [!NOTE]
> Firma Microsoft dokłada starań, aby zapewnić najwyższy poziom zaufania, przejrzystości, zgodności ze standardami i zgodność z przepisami. Dowiedz się więcej o tym, w jaki sposób zespół reagowania na zdarzenia globalne firmy Microsoft zmniejsza skutki ataków na usługi Cloud Services oraz jak zabezpieczenia są wbudowane w produkty firmy Microsoft i usługi w chmurze w [Centrum zaufania firmy Microsoft — informacje o zabezpieczeniach](https://www.microsoft.com/trustcenter/security) i zgodności firmy Microsoft w [Centrum zaufania firmy Microsoft — zgodność](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
W przypadku większości organizacji zabezpieczenia zasobów firmy są zależne od integralności kont uprzywilejowanych, które administrują systemami IT i zarządzają nimi. Cybernetycznymi — atakujący koncentrują się na uprzywilejowanym dostępie do systemów infrastruktury (takich jak Active Directory i Azure Active Directory), aby uzyskać dostęp do poufnych danych organizacji. 

Tradycyjne podejścia, które koncentrują się na zabezpieczaniu punktów wejścia i wyjścia sieci jako podstawowy obwód zabezpieczeń, są mniej efektywne ze względu na wzrost użycia aplikacji SaaS i urządzeń osobistych w Internecie. Naturalnym sposobem zastąpienia obwodowych zabezpieczeń sieci w złożonych nowoczesnych przedsiębiorstwach jest uwierzytelnianie i kontrola autoryzacji w warstwie tożsamości organizacji.

Uprzywilejowane konta administracyjne są skutecznie kontrolujące ten nowy "obwód zabezpieczeń". Ochrona dostępu uprzywilejowanego jest niezwykle ważna niezależnie od tego, czy środowisko działa lokalnie, w chmurze, czy w ramach usług hostowanych w chmurze. Ochrona dostępu administracyjnego do określonych źródłami ataków wymaga wykonania kompletnego i przydatnegoego podejścia do izolowania systemów organizacji przed zagrożeniami. 

Zabezpieczanie uprzywilejowanego dostępu wymaga wprowadzenia zmian

* Procesy, praktyki administracyjne i zarządzanie wiedzą
* Składniki techniczne, takie jak obrona hosta, ochrona kont i zarządzanie tożsamościami

Ten dokument koncentruje się głównie na tworzeniu planu w celu zabezpieczania tożsamości i dostępu, które są zarządzane lub zgłaszane w usłudze Azure AD, Microsoft Azure, Office 365 i innych usługach w chmurze. W przypadku organizacji z lokalnymi kontami administracyjnymi zapoznaj się ze wskazówkami dotyczącymi lokalnego i hybrydowego dostępu z dostępem uprzywilejowanym, które są zarządzane przez Active Directory przy [zabezpieczaniu dostępu uprzywilejowanego](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Wskazówki zawarte w tym artykule odnoszą się głównie do funkcji Azure Active Directory, które znajdują się w Azure Active Directory — wersja Premium planach P1 i P2. Azure Active Directory — wersja Premium P2 jest zawarty w pakiecie EMS E5 i pakiet Microsoft 365 E5. W tych wskazówkach przyjęto, że organizacja posiada już Azure AD — wersja Premium licencje na P2. Jeśli nie masz tych licencji, niektóre wskazówki mogą nie być stosowane w organizacji. Ponadto w tym artykule termin Administrator globalny (lub Administrator globalny) jest równoznaczny z "administratorem firmy" lub "administratorem dzierżawy".

## <a name="develop-a-roadmap"></a>Opracowywanie planu 

Firma Microsoft zaleca, aby opracować i postępować zgodnie z planem w celu zabezpieczenia uprzywilejowanego dostępu przed osobami atakującymi cybernetycznymi. Możesz zawsze dostosować plan, aby uwzględnić istniejące możliwości i konkretne wymagania w organizacji. Każdy etap planu powinien podnieść koszt i trudności źródłami ataków dostępu uprzywilejowanego do zasobów lokalnych, chmurowych i hybrydowych. Firma Microsoft zaleca następujące cztery etapy planowania: ten zalecany plan planuje najbardziej efektywne i najszybsze implementacje w oparciu o środowisko firmy Microsoft z cybernetycznymią i implementacją odpowiedzi na ataki. Osie czasu dla tego planu są przybliżone.

![Etapy planu z liniami czasu](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Etap 1 (24-48 godzin): elementy krytyczne, które zalecamy od razu

* Etap 2 (2-4 tygodnie): eliminowanie najczęściej używanych technik ataków

* Etap 3 (1-3 miesięcy): budowanie widoczności i tworzenie pełnej kontroli działania administratora

* Etap 4 (sześć miesięcy i więcej): Kontynuuj tworzenie zabezpieczeń, aby zwiększyć ochronę platformy zabezpieczeń

Ta struktura przewodnika jest przeznaczona do maksymalizowania użycia technologii firmy Microsoft, które mogły już zostać wdrożone. Możesz również wykorzystać podstawowe i nadchodzące technologie zabezpieczeń oraz zintegrować narzędzia zabezpieczeń od innych dostawców, które zostały już wdrożone lub rozważają wdrażanie. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Etap 1: elementy krytyczne, które zalecamy od razu

![Najpierw etap 1 najważniejszych elementów do wykonania](./media/directory-admin-roles-secure/stage-one.png)

Etap 1 planu koncentruje się na kluczowych zadaniach, które są szybkie i łatwe do wdrożenia. Zalecamy wykonanie tych zaledwie kilku elementów w ciągu pierwszych 24-48 godzin w celu zapewnienia podstawowego poziomu zabezpieczeń uprzywilejowanego dostępu. Ten etap bezpiecznego planu dostępu uprzywilejowanego ma następujące akcje:

### <a name="general-preparation"></a>Ogólne przygotowanie

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Włącz Azure AD Privileged Identity Management

Jeśli jeszcze nie włączono Azure AD Privileged Identity Management (PIM), zrób to w dzierżawie produkcyjnej. Po włączeniu Privileged Identity Management będziesz otrzymywać wiadomości e-mail z powiadomieniami o zmianach roli uprzywilejowanego dostępu. Te powiadomienia zapewniają wczesne ostrzeżenie, gdy dodatkowi użytkownicy są dodawani do ról o wysokim poziomie uprawnień w katalogu.

Azure AD Privileged Identity Management jest zawarty w Azure AD — wersja Premium P2 lub EMS E5. Te rozwiązania pomagają chronić dostęp do aplikacji i zasobów w środowisku lokalnym i w chmurze. Jeśli nie masz jeszcze Azure AD — wersja Premium P2 lub EMS E5 i chcesz oszacować więcej funkcji, do których odwołuje się ten plan, zarejestruj się, aby uzyskać [Enterprise Mobility + Security bezpłatną 90-dniową wersję próbną](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Użyj tych prób licencji, aby wypróbować Azure AD Privileged Identity Management i Azure AD Identity Protection do monitorowania aktywności przy użyciu zaawansowanych raportów zabezpieczeń usługi Azure AD, inspekcji i alertów.

Po włączeniu Azure AD Privileged Identity Management:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta, które jest administratorem globalnym dzierżawy produkcyjnej.

2. Aby wybrać dzierżawcę, który ma być używany Privileged Identity Management, wybierz swoją nazwę użytkownika w prawym górnym rogu Azure Portal.

3. W menu Azure Portal wybierz pozycję **wszystkie usługi** i przefiltruj listę dla **Azure AD Privileged Identity Management**.

4. Otwórz Privileged Identity Management z listy **wszystkie usługi** i przypnij ją do pulpitu nawigacyjnego.

Pierwszą osobą, która ma zostać użyta Azure AD Privileged Identity Management w dzierżawie, automatycznie przypisze role **administrator zabezpieczeń** i **administrator ról uprzywilejowanych** w dzierżawie. Tylko Administratorzy ról uprzywilejowanych mogą zarządzać przypisaniami użytkowników w usłudze Azure AD. Ponadto po dodaniu Azure AD Privileged Identity Management zostanie wyświetlony Kreator zabezpieczeń, który przeprowadzi Cię przez początkowe środowisko odnajdywania i przypisywania. Możesz zamknąć kreatora bez wprowadzania żadnych dodatkowych zmian. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Zidentyfikuj i Kategoryzuj konta, które są w rolach o wysokim poziomie uprawnień 

Po włączeniu Azure AD Privileged Identity Management Wyświetl użytkowników, którzy znajdują się w roli katalogu Administrator globalny, administrator ról uprzywilejowanych, administrator programu Exchange Online i administrator usługi SharePoint Online. Jeśli nie masz usługi Azure AD PIM w dzierżawie, możesz użyć [interfejsu API programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Rozpocznij od roli administratora globalnego, ponieważ ta rola jest ogólna: użytkownik, któremu przypisano tę rolę administratora, ma takie same uprawnienia dla wszystkich usług w chmurze, dla których subskrypcja została zasubskrybowana, bez względu na to, czy przypisano tę rolę w centrum administracyjnym Microsoft 365, Azure Portal czy przy użyciu modułu usługi Azure AD dla programu Microsoft PowerShell. 

Usuń wszystkie konta, które nie są już potrzebne w tych rolach. Następnie należy przydzielić pozostałe konta przypisane do ról administratora:

* Przypisane do użytkowników administracyjnych i mogą być również używane do celów innych niż administracyjne (na przykład osobista poczta e-mail)
* Przypisane do użytkowników administracyjnych i wyznaczone wyłącznie do celów administracyjnych
* Współużytkowane przez wielu użytkowników
* W przypadku scenariuszy z dostępem awaryjnym w przypadku awarii
* Dla zautomatyzowanych skryptów
* Dla użytkowników zewnętrznych

#### <a name="define-at-least-two-emergency-access-accounts"></a>Zdefiniuj co najmniej dwa konta dostępu awaryjnego 

Upewnij się, że nie możesz zalogować się do sytuacji, w której można je przypadkowo zablokować z administrowania dzierżawą usługi Azure AD z powodu niemożności zalogowania się lub aktywowania istniejącego konta danego użytkownika jako administrator. Na przykład jeśli organizacja jest federacyjnym do lokalnego dostawcy tożsamości, ten dostawca tożsamości może być niedostępny, aby użytkownicy nie mogli logować się lokalnie. Możesz ograniczyć wpływ przypadkowego braku dostępu administracyjnego, przechowując dwa lub więcej kont dostępu awaryjnego w dzierżawie.

Konta dostępu awaryjnego pomagają organizacjom ograniczyć dostęp uprzywilejowany w istniejącym środowisku Azure Active Directory. Te konta są wysoce uprzywilejowane i nie są przypisane do określonych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnej dla scenariuszy "Break Glass", w których normalne konta administracyjne nie mogą być używane. Organizacje muszą zapewnić możliwość kontrolowania i zmniejszania użycia konta awaryjnego tylko do tego czasu, w którym jest to konieczne.

Oceń konta, które są przypisane lub kwalifikujące się do roli administratora globalnego. Jeśli nie widzisz żadnych kont tylko w chmurze korzystających z domeny *. onmicrosoft.com (zamierzonej w przypadku dostępu awaryjnego "Break Glass"), utwórz je. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami administracyjnymi dostępu awaryjnego w usłudze Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Włącz uwierzytelnianie wieloskładnikowe i Zarejestruj wszystkie inne konta administratora niefederacyjnego z wysokim poziomem uprawnień pojedynczego użytkownika

Wymagaj usługi Azure Multi-Factor Authentication (MFA) podczas logowania dla wszystkich użytkowników, którzy są trwale przypisani do co najmniej jednej roli administratora usługi Azure AD: Administrator globalny, administrator ról uprzywilejowanych, administrator programu Exchange Online i administrator usługi SharePoint Online. Skorzystaj z przewodnika, aby włączyć [uwierzytelnianie wieloskładnikowe (MFA) dla kont administratorów](../authentication/howto-mfa-userstates.md) i upewnić się, że wszyscy użytkownicy zostali zarejestrowani w [https://aka.ms/mfasetup](https://aka.ms/mfasetup)usłudze. Więcej informacji można znaleźć w sekcji Krok 2 i krok 3 przewodnika [Ochrona dostępu do danych i usług w pakiecie Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Etap 2: eliminowanie najczęściej używanych technik ataków

![Etap 2 — eliminowanie często używanych ataków](./media/directory-admin-roles-secure/stage-two.png)

Etap 2 planu koncentruje się na ograniczeniu najczęściej używanych technik ataków kradzieży i nadużycia poświadczeń i może być zaimplementowany przez około 2-4 tygodni. Ten etap zaawansowanej mapy dostępu uprzywilejowanego obejmuje następujące działania.

### <a name="general-preparation"></a>Ogólne przygotowanie

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Przeprowadzanie spisu usług, właścicieli i administratorów

Mając na celu zwiększenie możliwości ponoszenia urządzeń (BYOD) i zasad pracy z domu oraz rozwój łączności bezprzewodowej w firmach, należy monitorować, kto nawiązuje połączenie z siecią. Skuteczna Inspekcja zabezpieczeń często ujawnia urządzenia, aplikacje i programy działające w sieci, które nie są przez nią obsługiwane i dlatego potencjalnie nie są bezpieczne. Aby uzyskać więcej informacji, zobacz temat [Omówienie zarządzania i monitorowania zabezpieczeń platformy Azure](../../security/fundamentals/management-monitoring-overview.md). Upewnij się, że wszystkie poniższe zadania zostały uwzględnione w procesie spisu. 

* Zidentyfikuj użytkowników, którzy mają role administracyjne i usługi, którymi mogą zarządzać.
* Użyj usługi Azure AD PIM, aby dowiedzieć się, którzy użytkownicy w organizacji mają dostęp administratora do usługi Azure AD, w tym dodatkowe role poza tymi wymienionymi na etapie 1.
* Poza rolami zdefiniowanymi w usłudze Azure AD pakiet Office 365 zawiera zestaw ról administratora, które można przypisać do użytkowników w organizacji. Każda rola administratora jest mapowana na typowe funkcje biznesowe i umożliwia osobom w organizacji uprawnienia do wykonywania określonych zadań w [centrum administracyjnym Microsoft 365](https://admin.microsoft.com). Skorzystaj z centrum administracyjnego Microsoft 365, aby dowiedzieć się, którzy użytkownicy w organizacji mają dostęp administratora do pakietu Office 365, w tym za pośrednictwem ról, które nie są zarządzane w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Informacje o rolach administracyjnych pakietu office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) i [najlepszych rozwiązaniach dotyczących zabezpieczeń dla pakietu Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Wykonaj spis w innych usługach, na których opiera się organizacja, na przykład Azure, Intune lub Dynamics 365.
* Upewnij się, że konta administratorów (konta, które są używane do celów administracyjnych, a nie tylko dla codziennych kont użytkowników), mają dołączone do nich służbowe adresy e-mail i zostały zarejestrowane dla usługi Azure MFA lub w środowisku lokalnym.
* Poproszenie użytkowników o uzasadnienie biznesowe dla dostępu administracyjnego.
* Usuń dostęp administratora dla tych użytkowników i usług, które ich nie potrzebują.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identyfikowanie kont Microsoft w rolach administracyjnych, które muszą zostać przełączone na konta służbowe

Czasami początkowa Administratorzy globalni w organizacji ponownie korzystają z istniejących poświadczeń konto Microsoft po rozpoczęciu korzystania z usługi Azure AD. Te konta Microsoft powinny zostać zastąpione przez poszczególne konta oparte na chmurze lub zsynchronizowane. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Upewnij się, że konta użytkowników i przekazywanie poczty są przekazywane na konta administratora globalnego 

Konta administratora globalnego nie powinny mieć osobistych adresów e-mail, ponieważ osobiste konta e-mail są regularnie odwiedzane przez osoby atakujące cybernetycznymi. Aby pomóc w oddzieleniu ryzyka internetowego (ataki wyłudzające informacje, niezamierzone przeglądanie sieci Web) od uprawnień administracyjnych, Utwórz dedykowane konta dla każdego użytkownika z uprawnieniami administracyjnymi. 

Jeśli jeszcze tego nie zrobiono, Utwórz osobne konta dla użytkowników, aby wykonywać globalne zadania administracyjne, aby upewnić się, że nie otwierają one wiadomości e-mail ani nie uruchamiają programów skojarzonych z kontami administratorów. Upewnij się, że te konta są przekazywane pocztą e-mail do działającej skrzynki pocztowej.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Upewnij się, że hasła kont administracyjnych zostały ostatnio zmienione

Upewnij się, że wszyscy użytkownicy zarejestrowali się w swoich kontach administracyjnych i w ciągu ostatnich 90 dni zmieniły swoje hasła. Upewnij się również, że wszystkie konta udostępnione, w których wielu użytkowników wie, że hasła zostały ostatnio zmienione.

#### <a name="turn-on-password-hash-synchronization"></a>Włącz synchronizację skrótów haseł

Synchronizacja skrótów haseł jest funkcją służącą do synchronizowania skrótów skrótów haseł użytkowników z wystąpienia lokalnego Active Directory do wystąpienia usługi Azure AD opartego na chmurze. Nawet jeśli zdecydujesz się użyć Federacji z Active Directory Federation Services (AD FS) lub innych dostawców tożsamości, możesz opcjonalnie skonfigurować synchronizację skrótów haseł jako kopię zapasową w przypadku awarii infrastruktury lokalnej, takiej jak serwery usług AD lub ADFS, lub gdy stanie się ona tymczasowo niedostępna. Dzięki temu użytkownicy mogą logować się do usługi przy użyciu tego samego hasła, którego używają do logowania się do lokalnego wystąpienia usługi AD. Ponadto umożliwia ona usłudze Identity Protection wykrywanie złamanych poświadczeń, porównując te skróty haseł z hasłami znanymi jako zagrożone, jeśli użytkownik korzysta z tego samego adresu e-mail i hasła w innych usługach, które nie są połączone z usługą Azure AD.  Aby uzyskać więcej informacji, zobacz [implementowanie synchronizacji skrótów haseł przy użyciu synchronizacji Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Wymagaj uwierzytelniania wieloskładnikowego (MFA) dla użytkowników we wszystkich uprzywilejowanych rolach, a także dla narażonych użytkowników

Usługa Azure AD zaleca korzystanie z uwierzytelniania wieloskładnikowego (MFA) dla wszystkich użytkowników, w tym administratorów i wszystkich innych użytkowników, którzy mają znaczny wpływ na bezpieczeństwo konta (na przykład funkcjonariusze usług finansowych). Zmniejsza to ryzyko ataków spowodowanych przez złamane hasło.

Włącz:

* Uwierzytelnianie [wieloskładnikowe przy użyciu zasad dostępu warunkowego](../authentication/howto-mfa-getstarted.md) dla wszystkich użytkowników w organizacji.

W przypadku korzystania z funkcji Windows Hello dla firm wymagane jest spełnienie wymagań usługi MFA przy użyciu środowiska logowania do usługi Windows Hello. Aby uzyskać więcej informacji, zobacz Funkcja [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Konfigurowanie ochrony tożsamości 

Azure AD Identity Protection to narzędzie do monitorowania i raportowania oparte na algorytmach, które służy do wykrywania potencjalnych luk w zabezpieczeniach związanych z tożsamościami w organizacji. Można skonfigurować automatyczne odpowiedzi na te wykryte podejrzane działania i podjąć odpowiednie działania w celu ich rozwiązania. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection (Ochrona tożsamości w usłudze Azure Active Directory)](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Uzyskaj wynik zabezpieczony przez pakiet Office 365 (Jeśli korzystasz z pakietu Office 365)

Zapoznaj się z informacjami na temat usług Office 365, z których korzystasz (takich jak OneDrive, SharePoint i Exchange), a następnie sprawdź ustawienia i działania oraz porównuje je z linią bazową utworzoną przez firmę Microsoft. Wyniki są uzyskiwane w oparciu o sposób wyrównany do najlepszych rozwiązań w zakresie zabezpieczeń. Każdy użytkownik z uprawnieniami administratora (administratorem globalnym lub rolą administratora niestandardowego) dla subskrypcji pakietu Office 365 Business Premium lub Enterprise ma dostęp do bezpiecznego wyniku w [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Zapoznaj się ze wskazówkami dotyczącymi zabezpieczeń i zgodności pakietu Office 365 (Jeśli korzystasz z pakietu Office 365)

[Plan zabezpieczeń i zgodności](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) przedstawia podejście do tego, w jaki sposób klient pakietu Office 365 powinien skonfigurować pakiet Office 365 i korzystać z innych możliwości usług EMS. Następnie przejrzyj kroki 3-6, jak [chronić dostęp do danych i usług w pakiecie office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) oraz jak [monitorować zabezpieczenia i zgodność w pakiecie Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Skonfiguruj monitorowanie aktywności pakietu Office 365 (Jeśli korzystasz z pakietu Office 365)

Możesz monitorować sposób, w jaki użytkownicy w organizacji korzystają z usług Office 365, umożliwiając Identyfikowanie użytkowników, którzy mają konto administracyjne i którzy mogą nie potrzebować dostępu do pakietu Office 365 z powodu braku zalogowania się do tych portali. Aby uzyskać więcej informacji, zobacz [Raporty aktywności w centrum administracyjnym Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Ustalanie właścicieli planu reagowania na zdarzenia/sytuacje awaryjne

Skuteczne reagowanie na zdarzenia to złożone zobowiązanie. W związku z tym ustanowienie pomyślnej funkcji reagowania na zdarzenia wymaga istotnego planowania i zasobów. Ważne jest, aby stale monitorować cybernetycznymi ataki i ustanawiać procedury określania priorytetów obsługi zdarzeń. Efektywne metody zbierania, analizowania i raportowania danych są niezbędne do tworzenia relacji oraz do nawiązywania komunikacji z innymi grupami wewnętrznymi i właścicielami planu. Aby uzyskać więcej informacji, zobacz [Centrum zabezpieczeń firmy Microsoft](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Bezpieczne konta administracyjne z uprawnieniami lokalnymi, jeśli nie zostały jeszcze wykonane

Jeśli dzierżawa Azure Active Directory jest synchronizowana z lokalnym Active Directory, postępuj zgodnie ze wskazówkami w temacie [zabezpieczenia uprzywilejowanego dostępu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Etap 1. Obejmuje to Tworzenie oddzielnych kont administratorów dla użytkowników, którzy muszą przeprowadzać lokalne zadania administracyjne, wdrażać stacje robocze dostępu uprzywilejowanego dla administratorów Active Directory i tworzyć unikatowe hasła administratora lokalnego dla stacji roboczych i serwerów.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do platformy Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Ukończ spis subskrypcji

Użyj witryny Enterprise Portal i Azure Portal, aby identyfikować subskrypcje w organizacji, które obsługują aplikacje produkcyjne.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Usuń konta Microsoft z ról administratora

Konta Microsoft z innych programów, takich jak Xbox, Live i Outlook, nie powinny być używane jako konta administratorów dla subskrypcji organizacyjnych. Usuń stan administratora z wszystkich kont Microsoft i Zastąp ciąg Azure Active Directory (na przykład chris@contoso.com) konta służbowe.

#### <a name="monitor-azure-activity"></a>Monitorowanie aktywności platformy Azure

Dziennik aktywności platformy Azure zawiera historię zdarzeń na poziomie subskrypcji na platformie Azure. Zawiera ona informacje o tym, kto utworzył, zaktualizował i usunął zasoby oraz kiedy wystąpiły te zdarzenia. Aby uzyskać więcej informacji, zobacz [Inspekcja i odbieranie powiadomień o ważnych akcjach w ramach subskrypcji platformy Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do innych aplikacji w chmurze za pośrednictwem usługi Azure AD

#### <a name="configure-conditional-access-policies"></a>Konfigurowanie zasad dostępu warunkowego

Przygotuj zasady dostępu warunkowego dla aplikacji lokalnych i hostowanych w chmurze. Jeśli masz użytkowników dołączonych do urządzeń w miejscu pracy, uzyskaj więcej informacji [na temat konfigurowania dostępu warunkowego lokalnego przy użyciu Azure Active Directory rejestracji urządzeń](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Etap 3: budowanie widoczności i przejmij pełną kontrolę nad aktywnością administratora

![Etap 3 Przejmij kontrolę nad aktywnością administratora](./media/directory-admin-roles-secure/stage-three.png)

Etap 3 kompiluje środki zaradcze z etapu 2 i został zaprojektowany do wdrożenia w ciągu około 1-3 miesięcy. Ten etap zaawansowanej mapy dostępu uprzywilejowanego obejmuje następujące składniki.

### <a name="general-preparation"></a>Ogólne przygotowanie

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Wykonaj przegląd dostępu użytkowników w rolach administratorów

Większą liczbę użytkowników firmowych uzyskują uprzywilejowany dostęp za poorednictwem usług w chmurze, co może prowadzić do zwiększenia niezarządzanej platformy. Obejmuje to użytkowników, którzy stają się administratorami globalnymi dla pakietu Office 365, administratorów subskrypcji platformy Azure i użytkowników, którzy mają dostęp administratora do maszyn wirtualnych lub za pośrednictwem aplikacji SaaS. Zamiast tego organizacje powinny mieć wszystkich pracowników, szczególnie administratorów, obsługiwać codzienne transakcje biznesowe jako nieuprzywilejowanych użytkowników, a jedynie podejmować prawa administratora zgodnie z potrzebami. Ponieważ liczba użytkowników w rolach administracyjnych mogła wzrosnąć od momentu wstępnego wdrożenia, wykonaj przeglądy dostępu, aby zidentyfikować i potwierdzić każdego użytkownika, który kwalifikuje się do aktywacji uprawnień administratora. 

Wykonaj następujące czynności:

* Określ użytkowników, którzy są administratorami usługi Azure AD, włączaj dostęp administratora w czasie just-in-Time i zabezpieczenia oparte na rolach.
* Przekonwertuj użytkowników, którzy nie mają jasnego uzasadnienia dla uprzywilejowanego dostępu administratora do innej roli (jeśli nie masz kwalifikującej się roli, usuń je).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Kontynuuj wprowadzanie silniejszych uwierzytelnień dla wszystkich użytkowników 

Wymaganie kierownictwa w języku C, menedżerów wysokiego poziomu, kluczowych pracowników IT i zabezpieczeń oraz innych wysoce narażonych użytkowników w celu uzyskania nowoczesnego, silnego uwierzytelniania, takiego jak Azure MFA lub Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Używanie dedykowanych stacji roboczych do administrowania usługą Azure AD

Osoby atakujące mogą próbować uzyskać dostęp do kont uprzywilejowanych w celu uzyskania dostępu do danych i systemów organizacji, dzięki czemu mogą one zakłócać integralność i autentyczność danych za pośrednictwem złośliwego kodu, który zmienia logikę programu lub przeprowadza inspekcję podczas wprowadzania poświadczeń przez administratora. Stacje robocze z dostępem uprzywilejowanym (PAW, Privileged Access Workstation) zapewniają dedykowany system operacyjny do realizacji zadań poufnych, który jest zabezpieczony przed atakami internetowymi i wektorami zagrożenia. Oddzielenie poufnych zadań i kont od stacji roboczych i urządzeń wykorzystywanych na co dzień zapewnia bardzo wysoki poziom ochrony przed atakami mającymi na celu wyłudzenie informacji, lukami w zabezpieczeniach systemu operacyjnego i aplikacji oraz różnego rodzaju atakami związanymi z podszywaniem się pod użytkownika. Chroni także przed kradzieżą poświadczeń, na przykład w formie ataków wykorzystujących metodę rejestrowania naciskanych klawiszy oraz ataków typu Pass-the-Hash i Pass-The-Ticket. Wdrażając uprzywilejowane stacje robocze dostępu, można zmniejszyć ryzyko wprowadzenia poświadczeń administratora przez administratorów z wyjątkiem środowiska pulpitu, które zostało zaostrzone. Aby uzyskać więcej informacji, zobacz [stacje robocze dostępu uprzywilejowanego](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Przejrzyj Narodowy Instytut standardów i zaleceń dotyczących technologii związanych z obsługą zdarzeń 

Narodowy Instytut standardów i technologii (NIST) zawiera wytyczne dotyczące obsługi zdarzeń, szczególnie w przypadku analizowania danych dotyczących zdarzeń i określania odpowiedniej odpowiedzi dla każdego zdarzenia. Aby uzyskać więcej informacji, zobacz [Przewodnik dotyczący obsługi zdarzeń zabezpieczeń komputera (NIST) (SP 800-61, wersja 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementowanie Privileged Identity Management (PIM) na potrzeby JIT w dodatkowych rolach administracyjnych

Aby uzyskać Azure Active Directory, użyj funkcji [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) . Ograniczona czasowo aktywacja uprzywilejowanych ról działa przez umożliwienie:

* Aktywowanie uprawnień administratora w celu wykonania określonego zadania
* Wymuś uwierzytelnianie wieloskładnikowe w trakcie procesu aktywacji
* Korzystanie z alertów w celu informowania administratorów o zmianach poza pasmem
* Zezwól użytkownikom na zachowanie określonych uprawnień przez wstępnie skonfigurowany czas
* Zezwalaj administratorom zabezpieczeń na odnajdywanie wszystkich uprzywilejowanych tożsamości, wyświetlanie raportów inspekcji i tworzenie przeglądów dostępu w celu identyfikowania wszystkich użytkowników uprawnionych do aktywacji uprawnień administratora

Jeśli korzystasz już z Azure AD Privileged Identity Management, Dostosuj przedziały czasu dla uprawnień ograniczonych czasowo (na przykład okna obsługi).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Określanie zagrożeń dla protokołów logowania opartych na hasłach (w przypadku korzystania z usługi Exchange Online)

W przeszłości przyjęto, że kombinacje nazwy użytkownika/hasła zostały osadzone w obszarze urządzenia, konta e-mail, telefony itd. Jednak teraz z ryzykiem dla ataków cybernetycznymi w chmurze zalecamy zidentyfikowanie każdego potencjalnego użytkownika, który w przypadku naruszenia bezpieczeństwa poświadczeń może być spowodowany tym, że nie można zalogować się do swojej poczty e-mail za pomocą nazwy użytkownika/hasła, implementując wymagania dotyczące silnego uwierzytelniania i dostępu warunkowego. Można zablokować [starsze uwierzytelnianie przy użyciu dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication). Zapoznaj się ze szczegółami dotyczącymi [sposobu blokowania uwierzytelniania podstawowego](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) za pomocą usługi Exchange Online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Ukończ ocenę dla ról pakietu Office 365 (Jeśli korzystasz z pakietu Office 365)

Oceń, czy wszyscy użytkownicy Administratorzy znajdują się w poprawnych rolach (Usuń i ponownie Przypisz zgodnie z tą oceną).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Zapoznaj się z podejściem do zarządzania zdarzeniami zabezpieczeń używanym w pakiecie Office 365 i porównaj się z własną organizacją

Możesz pobrać ten raport z [zarządzania zdarzeniami zabezpieczeń w Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Kontynuuj Zabezpieczanie lokalnych kont administracyjnych uprzywilejowanych

Jeśli Azure Active Directory jest podłączony do Active Directory lokalnych, postępuj zgodnie ze wskazówkami w [przewodniku dotyczącego dostępu uprzywilejowanego zabezpieczeń](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): etap 2. Obejmuje to wdrażanie stacji roboczych z dostępem uprzywilejowanym dla wszystkich administratorów, wymagających uwierzytelniania wieloskładnikowego, przy użyciu wystarczającego administratora do konserwacji kontrolera domeny, obniżanie podatności na ataki domen, wdrażanie usługi ATA na potrzeby wykrywania ataków.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do platformy Azure

#### <a name="establish-integrated-monitoring"></a>Ustanów zintegrowane monitorowanie

[Azure Security Center](../../security-center/security-center-intro.md) zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogą być niezauważalne i współpracować z szeroką ekosystemem rozwiązań zabezpieczających.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Sporządzanie spisu kont uprzywilejowanych w hostowanej Virtual Machines

W większości przypadków nie trzeba przyznawać użytkownikom nieograniczonych uprawnień do wszystkich subskrypcji lub zasobów platformy Azure. Role administratora usługi Azure AD umożliwiają Rozdzielenie obowiązków w organizacji i udzielenie dostępu tylko użytkownikom, którzy muszą wykonywać określone zadania. Na przykład użyj ról administratora usługi Azure AD, aby umożliwić jednemu administratorowi zarządzanie tylko maszynami wirtualnymi w ramach subskrypcji, podczas gdy inna może zarządzać bazami danych SQL w ramach tej samej subskrypcji. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z Access Controlami opartymi na rolach w Azure Portal](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementacja usług PIM dla administratorów usługi Azure AD

Zarządzanie tożsamościami uprzywilejowanymi przy użyciu ról administratorów usługi Azure AD umożliwia zarządzanie dostępem do zasobów platformy Azure, kontrolowanie ich i monitorowanie. Korzystanie z usługi PIM chroni konta uprzywilejowane przed atakami cybernetycznymiymi przez zmniejszenie czasu ekspozycji na uprawnienia i zwiększenie widoczności do użycia w raportach i alertach. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem RBAC do zasobów platformy Azure za pomocą Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Użyj integracji dzienników platformy Azure w celu wysłania odpowiednich dzienników platformy Azure do systemów SIEM 

Integracja dzienników platformy Azure umożliwia integrowanie nieprzetworzonych dzienników z zasobów platformy Azure z istniejącymi systemami informacji o zabezpieczeniach i zarządzania zdarzeniami (SIEM) w organizacji. [Usługa Azure log Integration](../../security/fundamentals/azure-log-integration-overview.md) zbiera zdarzenia systemu Windows z dzienników Podgląd zdarzeń systemu Windows oraz zasobów platformy Azure z dzienników aktywności platformy Azure, alertów Azure Security Center i dzienników zasobów platformy Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do innych aplikacji w chmurze za pośrednictwem usługi Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementowanie aprowizacji użytkowników dla połączonych aplikacji

Usługa Azure AD umożliwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach w chmurze (SaaS), takich jak Dropbox, Salesforce, usługi ServiceNow i tak dalej. Aby uzyskać więcej informacji, zobacz [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS przy użyciu usługi Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrowanie ochrony informacji

Usługa MCAS umożliwia badanie plików i Ustawianie zasad na podstawie etykiet klasyfikacji Azure Information Protection, co zapewnia lepszą widoczność i kontrolę nad danymi w chmurze. Skanuj i Klasyfikuj pliki w chmurze i stosuj etykiety usługi Azure Information Protection. Aby uzyskać więcej informacji, zobacz [Azure Information Protection Integration](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Konfigurowanie dostępu warunkowego

Skonfiguruj dostęp warunkowy na podstawie grupy, lokalizacji i czułości aplikacji dla [aplikacji SaaS](https://azure.microsoft.com/overview/what-is-saas/) i aplikacji połączonych z usługą Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitoruj aktywność w połączonych aplikacjach w chmurze

Aby zapewnić ochronę dostępu użytkowników w połączonych aplikacjach, zalecamy skorzystanie z [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Zapewnia to ochronę dostępu przedsiębiorstwa do aplikacji w chmurze, a także Zabezpieczanie kont administratorów, umożliwiając:

* Poszerzanie widoczności i kontroli do aplikacji w chmurze
* Tworzenie zasad dostępu, działań i udostępniania danych
* Automatyczne identyfikowanie działań ryzykownych, nietypowych zachowań i zagrożeń
* Zapobieganie wyciekom danych
* Minimalizacja ryzyka i zautomatyzowanego zapobiegania zagrożeniom i wymuszania zasad

Agent Cloud App Security SIEM integruje Cloud App Security z serwerem SIEM w celu umożliwienia scentralizowanego monitorowania alertów i działań związanych z pakietem Office 365. Jest on uruchamiany na serwerze i pobiera alerty i działania z Cloud App Security i strumieniuje je na serwerze SIEM. Aby uzyskać więcej informacji, zobacz [Siem Integration](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Etap 4. kontynuowanie tworzenia obronności w celu uzyskania bardziej aktywnej stan zabezpieczeń

![Etap 4. przyjęcie proaktywnej stan zabezpieczeń](./media/directory-admin-roles-secure/stage-four.png)

Etap 4 planu planuje się na widoczność z etapu 3 i został zaprojektowany do wdrożenia w ciągu sześciu miesięcy i więcej. Wykonanie planu ułatwia tworzenie silnych, uprzywilejowanych zabezpieczeń dostępu od potencjalnych ataków, które są obecnie znane i dostępne. Niestety, zagrożenia bezpieczeństwa są ciągle rozwijane i zmieniane, dlatego zalecamy wyświetlanie zabezpieczeń jako procesu ciągłego ukierunkowanego na podnoszenie kosztów i zmniejszenie współczynnika sukcesu źródłami ataków dla danego środowiska.

Zabezpieczanie uprzywilejowanego dostępu jest najważniejszym pierwszym krokiem do ustanowienia gwarancji bezpieczeństwa dla zasobów firmy w nowoczesnej organizacji, ale nie jest to jedyna część całego programu zabezpieczającego, który może obejmować elementy, takie jak zasady, operacje, zabezpieczenia informacji, serwery, aplikacje, komputery, urządzenia, Sieć szkieletowa w chmurze i inne składniki zapewniają bieżące gwarancje bezpieczeństwa. 

Oprócz zarządzania kontami uprzywilejowanego dostępu zalecamy przeprowadzenie regularnego przeglądania następujących danych:

* Upewnij się, że administratorzy robią swoją codzienny biznes jako nieuprzywilejowani użytkownicy.
* Udzielaj dostępu uprzywilejowanego w razie konieczności i usuwaj go później (just-in-Time).
* Zachowywanie i przeglądanie działań inspekcji związanych z kontami uprzywilejowanymi.

Aby uzyskać więcej informacji na temat tworzenia kompletnego planu zabezpieczeń, zobacz [zasoby architektury IT w chmurze firmy Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Aby uzyskać więcej informacji na temat angażowania usług firmy Microsoft w celu uzyskania pomocy w każdym z tych tematów, skontaktuj się z przedstawicielem firmy Microsoft lub zapoznaj się z tematem [Ochrona przedsiębiorstwa przed cybernetycznymi](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Ten końcowy, ciągły etap zaawansowanej metody dostępu uprzywilejowanego obejmuje następujące składniki.

### <a name="general-preparation"></a>Ogólne przygotowanie

#### <a name="review-admin-roles-in-azure-active-directory"></a>Przeglądanie ról administratorów w Azure Active Directory 

Ustal, czy bieżące wbudowane role administratora usługi Azure AD są nadal aktualne i upewnij się, że użytkownicy są tylko w rolach i delegowaniu, których potrzebują w przypadku odpowiednich uprawnień. Za pomocą usługi Azure AD można wyznaczyć oddzielną administratorów do korzystania z różnych funkcji. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról administratorów w Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Przeglądanie użytkowników, którzy mają administrowanie urządzeniami przyłączonymi do usługi Azure AD

Aby uzyskać więcej informacji, zobacz [How to configure hybryded Azure Active Directory Devices Join](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>Przejrzyj członków [wbudowanych ról administratora pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Jeśli korzystasz z pakietu Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Sprawdź poprawność planu reagowania na zdarzenia

Aby ulepszyć plan, firma Microsoft zaleca regularne Weryfikowanie, czy plan działa zgodnie z oczekiwaniami:

* Przejdź do istniejącej mapy drogowej, aby zobaczyć, co zostało pominięte
* Na podstawie analizy postmortem należy skorygować istniejące lub zdefiniować nowe najlepsze rozwiązania
* Upewnij się, że zaktualizowany plan reagowania na zdarzenia i najlepsze rozwiązania są dystrybuowane w całej organizacji.


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Dodatkowe kroki dla organizacji zarządzających dostępem do platformy Azure 

Ustal, czy musisz [przenieść własność subskrypcji platformy Azure na inne konto](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Szkło ze znakami": co zrobić w przypadku awarii

![Konta na potrzeby dostępu do awaryjnego przerwania](./media/directory-admin-roles-secure/emergency.jpeg)

1. Powiadamiaj menedżerów kluczy i funkcjonariuszy ds. zabezpieczeń o stosownych informacjach dotyczących incydentu.

2. Przejrzyj element PlayBook ataku. 

3. Aby zalogować się do usługi Azure AD, uzyskaj dostęp do kombinacji nazwy użytkownika/hasła konta "Break Glass". 

4. Uzyskaj pomoc od firmy Microsoft, [otwierając żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Przyjrzyj się [raportom logowania do usługi Azure AD](../reports-monitoring/overview-reports.md). Może istnieć zwłoka między zdarzeniem a jego uwzględnieniem w raporcie.

6. W przypadku środowisk hybrydowych, jeśli federacyjny i serwer AD FS nie są dostępne, może być konieczne tymczasowe przełączenie z uwierzytelniania federacyjnego, aby można było używać funkcji synchronizacji skrótów haseł. Spowoduje to przywrócenie Federacji domeny z powrotem do uwierzytelniania zarządzanego do momentu udostępnienia serwera AD FS.

7. Monitoruj pocztę e-mail dla kont uprzywilejowanych.

8. Pamiętaj, aby zapisać kopie zapasowe odpowiednich dzienników dla potencjalnych śledczej i badań prawnych.

Aby uzyskać więcej informacji na temat sposobu, w jaki Microsoft Office 365 obsługuje zdarzenia związane z zabezpieczeniami, zobacz [zarządzanie zdarzeniami zabezpieczeń w Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Często zadawane pytania: często zadawane odpowiedzi dotyczące zabezpieczania dostępu uprzywilejowanego  

**P:** Co zrobić, jeśli jeszcze nie zaimplementowano żadnych bezpiecznych składników dostępu?

**Odpowiedź:** Zdefiniuj co najmniej dwa konta ze szkła, przypisz uwierzytelnianie wieloskładnikowe do kont administratorów uprzywilejowanych i oddziel konta użytkowników od kont administratorów globalnych.

**P:** Co to jest najpopularniejszy problem, który należy rozwiązać w pierwszej kolejności?

**Odpowiedź:** Upewnij się, że wymagasz silnego uwierzytelniania w przypadku wysoce narażonych osób.

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

**Odpowiedź:** Konta administratorów warstwy 0 (w tym konta, grupy i inne zasoby, które mają bezpośrednią lub pośrednią kontrolę administracyjną lasu usługi AD, domen lub kontrolerów domeny oraz wszystkie elementy zawartości) są używane tylko dla lokalnych kont usługi AD i nie są zwykle synchronizowane z usługą Azure AD w chmurze.

**P:** Jak zapewnić administratorom możliwość przypisywania losowego dostępu administratora w portalu?

**Odpowiedź:** Używaj kont bez uprawnień dla wszystkich użytkowników i większości administratorów. Zacznij od utworzenia podstawy organizacji, aby określić, które konta administratorów mają być uprzywilejowane. I monitoruj nowo utworzonych użytkowników administracyjnych.

## <a name="next-steps"></a>Następne kroki

* [Centrum zaufania firmy Microsoft dotyczące zabezpieczeń produktów](https://www.microsoft.com/trustcenter/security) — funkcje zabezpieczeń usług i produktów w chmurze firmy Microsoft

* [Centrum zaufania firmy Microsoft — zgodność z](https://www.microsoft.com/trustcenter/compliance/complianceofferings) kompleksowym zestawem zgodności usług w chmurze firmy Microsoft

* [Wskazówki dotyczące sposobu przeprowadzania oceny ryzyka](https://www.microsoft.com/trustcenter/guidance/risk-assessment) — zarządzanie wymaganiami dotyczącymi zabezpieczeń i zgodności usług w chmurze firmy Microsoft

### <a name="other-microsoft-online-services"></a>Inne usługi online firmy Microsoft

* [Zabezpieczenia Microsoft Intune](https://www.microsoft.com/trustcenter/security/intune-security) — usługa Intune zapewnia zarządzanie urządzeniami przenośnymi, zarządzanie aplikacjami mobilnymi oraz możliwości zarządzania komputerami z chmury.

* [Microsoft dynamics 365 Security](https://www.microsoft.com/trustcenter/security/dynamics365-security) — Dynamics 365 to rozwiązanie oparte na chmurze firmy Microsoft, które łączy funkcje zarządzania relacjami z klientami (CRM) i Enterprise Resource Planning (ERP).
