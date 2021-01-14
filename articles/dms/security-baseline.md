---
title: Podstawa zabezpieczeń platformy Azure dla Azure Database Migration Service
description: Linia bazowa zabezpieczeń Azure Database Migration Service zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: dms
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cee6f06588e340c03d6f2d7566d1af3627293118
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202491"
---
# <a name="azure-security-baseline-for-azure-database-migration-service"></a>Podstawa zabezpieczeń platformy Azure dla Azure Database Migration Service

Ta linia bazowa zabezpieczeń stosuje wskazówki z programu [Azure Security test w wersji 2,0](../security/benchmarks/overview.md) do Azure Database Migration Service. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Azure Database Migration Service. **Kontrolki** nie mają zastosowania do Azure Database Migration Service zostały wykluczone.

Aby dowiedzieć się, jak Azure Database Migration Service całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Azure Database Migration Service pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementowanie zabezpieczeń dla ruchu wewnętrznego

**Wskazówki**: podczas wdrażania zasobów Azure Database Migration Service należy utworzyć lub użyć istniejącej sieci wirtualnej. Upewnij się, że wszystkie sieci wirtualne platformy Azure są zgodne z zasadami segmentacji przedsiębiorstwa, które są dostosowane do ryzyka biznesowego. Każdy system, który może ponieść wyższy poziom ryzyka dla organizacji, powinien być odizolowany w ramach własnej sieci wirtualnej i dostatecznie zabezpieczony za pomocą sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) i/lub zapory platformy Azure.

Azure Database Migration Service domyślnie używa protokołu TLS 1,2. W razie potrzeby w celu zapewnienia zgodności z poprzednimi wersjami dla migrowanego źródła danych obsługa protokołu TLS 1,0 lub TLS 1,1 można włączyć w bloku konfiguracja usługi Azure Database Migration Service.

Za pomocą wskaźnikowego platformy Azure można wykrywać użycie starszych, niezabezpieczonych protokołów, takich jak SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, niepodpisane powiązania LDAP oraz słabe szyfry w protokole Kerberos.

- [Jak utworzyć sieciową grupę zabezpieczeń z regułami zabezpieczeń](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Jak wdrożyć i skonfigurować zaporę platformy Azure](../firewall/tutorial-firewall-deploy-portal.md) 

- [Skorzystaj z dokumentacji wstępnej wymagania dotyczącej portów, które mają być otwarte dla Azure Database Migration Service tym miejscu](pre-reqs.md#prerequisites-common-across-migration-scenarios)

- [Skoroszyt niezabezpieczonych protokołów na platformie Azure](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ns-2-connect-private-networks-together"></a>NS-2: łączenie sieci prywatnych razem

**Wskazówki**: Jeśli przypadek użycia migracji obejmuje ruch między sieciami, możesz użyć usługi Azure ExpressRoute lub wirtualnej sieci prywatnej (VPN) platformy Azure do tworzenia prywatnych połączeń między centrami danych platformy Azure i infrastrukturą lokalną w środowisku z różnymi lokalizacjami. Połączenia ExpressRoute nie przechodzą przez publiczny Internet i oferują większą niezawodność, większe szybkości i krótsze opóźnienia niż typowe połączenia z Internetem. W przypadku połączeń sieci VPN typu punkt-lokacja i sieci VPN typu lokacja-lokacja można połączyć lokalne urządzenia lub sieci z siecią wirtualną przy użyciu dowolnej kombinacji tych opcji sieci VPN i usługi Azure ExpressRoute. Aby połączyć co najmniej dwie sieci wirtualne na platformie Azure, użyj komunikacji równorzędnej sieci wirtualnej. Ruch sieciowy między równorzędnymi sieciami wirtualnymi jest prywatny i jest przechowywany w sieci szkieletowej platformy Azure.

- [Co to są modele łączności ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Omówienie usługi Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Ustanów dostęp w sieci prywatnej do usług platformy Azure

**Wskazówki**: Jeśli ma to zastosowanie, użyj prywatnego linku platformy Azure, aby umożliwić prywatny dostęp do usług źródłowych i docelowych, takich jak Azure SQL Server lub innych wymaganych usług podczas migracji.  W sytuacjach, w których łącze prywatne platformy Azure nie jest jeszcze dostępne, użyj punktów końcowych usługi Azure Virtual Network.  Prywatne łącze i punkty końcowe usługi Azure zapewniają bezpieczny dostęp do usług za pośrednictwem zoptymalizowanej trasy za pośrednictwem sieci szkieletowej platformy Azure bez przekroczenia Internetu.  

Ponadto upewnij się, że wymagania wstępne są spełnione przed zainicjowaniem obsługi Azure Database Migration Service w sieci wirtualnej, w tym portów komunikacyjnych, które muszą być dozwolone. 

- [Omówienie wymagań wstępnych dotyczących używania usługi Azure Database Migration Service](pre-reqs.md)

   

- [Opis linku prywatnego platformy Azure](../private-link/private-link-overview.md)

- [Informacje o punktach końcowych usługi Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="identity-management"></a>Zarządzanie tożsamością

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standaryzacja usługi Azure Active Directory jako centralnego systemu tożsamości i uwierzytelniania

**Wskazówki**: Azure Database Migration Service używa Azure Active Directory (Azure AD) jako domyślnej usługi zarządzania tożsamościami i dostępem. Należy przeprowadzić standaryzację usługi Azure AD, aby zarządzać zarządzaniem tożsamościami i dostępem w organizacji w programie: Microsoft Cloud zasoby, takie jak Azure Portal, Azure Storage, Azure Virtual Machine (Linux i Windows), Azure Key Vault, PaaS i SaaS.

Zasoby organizacji, takie jak aplikacje na platformie Azure lub formowe zasoby sieciowe.

Zabezpieczanie usługi Azure AD powinno mieć wysoki priorytet w zakresie zabezpieczeń w chmurze w organizacji. Usługa Azure AD zapewnia wskaźnik bezpieczeństwa tożsamości, który pomaga ocenić stan zabezpieczeń tożsamości w porównaniu z zaleceniami dotyczącymi najlepszych rozwiązań firmy Microsoft. Skorzystaj ze wskaźnika, aby ocenić, jak ściśle Twoja konfiguracja spełnia zalecenia dotyczące najlepszych rozwiązań, i ulepszać stan zabezpieczeń.

Usługa Azure AD obsługuje tożsamości zewnętrzne, które umożliwiają użytkownikom bez konto Microsoft logowania się do aplikacji i zasobów przy użyciu tożsamości zewnętrznej.

- [Dzierżawa w usłudze Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Używanie zewnętrznych dostawców tożsamości w aplikacji](/azure/active-directory/b2b/identity-providers) 

- [Co to jest wskaźnik bezpieczeństwa tożsamości w usłudze Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Bezpieczne i automatyczne zarządzanie tożsamościami aplikacji

**Wskazówki**: usługa Azure Database Migration Service wymaga od użytkowników utworzenia identyfikatora aplikacji (zasady usługi) i klucza uwierzytelniania w Azure Active Directory (Azure AD) w celu migracji do Azure SQL Database wystąpienia zarządzanego w trybie online. Ten identyfikator aplikacji wymaga roli współautor na poziomie subskrypcji (co nie jest zalecane, ponieważ rola współautor nadmiernego dostępu zostanie udzielona) lub można utworzyć role niestandardowe z określonymi uprawnieniami wymaganymi przez usługi Azure Database Migration Service.

Zaleca się usunięcie tego identyfikatora aplikacji po zakończeniu migracji.

- [Jak identyfikator aplikacji jest używany w trybie migracji online](tutorial-sql-server-managed-instance-online.md)

- [Role niestandardowe dla SQL Server migracji do usługi Azure SQL Managed instance w trybie online](resource-custom-roles-sql-db-managed-instance.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Korzystanie z logowania jednokrotnego usługi Azure AD na potrzeby dostępu do aplikacji

**Wskazówki**: Azure Database Migration Service jest zintegrowany z Azure Active Directory do zarządzania tożsamościami i dostępem do zasobów platformy Azure, aplikacji w chmurze i aplikacji lokalnych. Obejmuje to tożsamości przedsiębiorstwa, takie jak pracownicy, a także tożsamości zewnętrzne, takie jak partnerzy, dostawcy i dostawcy. Umożliwia to logowanie jednokrotne w celu zarządzania i bezpiecznego dostępu do danych i zasobów organizacji w środowisku lokalnym i w chmurze. Połącz wszystkich użytkowników, aplikacje i urządzenia z usługą Azure AD w celu zapewnienia bezproblemowego, bezpiecznego dostępu, lepszego wglądu i większej kontroli.

- [Informacje na temat logowania jednokrotnego aplikacji za pomocą usługi Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Używanie kontrolek silnego uwierzytelniania dla całego dostępu opartego na usłudze Azure Active Directory

**Wskazówki**: Azure Database Migration Service używa Azure Active Directory, które obsługują kontrolę silnego uwierzytelniania przez uwierzytelnianie wieloskładnikowe i silne metody bezhasła.
Uwierzytelnianie wieloskładnikowe — Włącz uwierzytelnianie wieloskładnikowe usługi Azure AD i postępuj zgodnie z zaleceniami Azure Security Center zarządzania tożsamościami i dostępem, aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi konfiguracji uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe można wymusić na wszystkich, wybrać opcję Użytkownicy lub na poziomie użytkownika na podstawie warunków logowania i czynników ryzyka.

Uwierzytelnianie bez hasła — dostępne są trzy opcje uwierzytelniania bez hasła: Windows Hello dla firm, aplikacja Microsoft Authenticator i lokalne metody uwierzytelniania, takie jak karty inteligentne.

W przypadku użytkowników z uprawnieniami administratora i uprzywilejowanego upewnij się, że używasz najwyższego poziomu uwierzytelniania, a następnie zastąp odpowiednie zasady silnego uwierzytelniania innym użytkownikom.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Wprowadzenie do opcji uwierzytelniania bez hasła w przypadku usługi Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Domyślne zasady haseł usługi Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Eliminowanie nieprawidłowych haseł przy użyciu ochrony hasłem Azure Active Directory](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorowanie anomalii kont i wyświetlanie alertów

**Wskazówki**: Azure Database Migration Service jest zintegrowany z Azure Active Directory, w którym znajdują się następujące źródła danych: logowania — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.

Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Te źródła danych można zintegrować z Azure Monitor, wskaźnikiem produktów platformy Azure lub systemami SIEM innych firm.

Usługa Azure Security Center może również wysyłać alerty dotyczące określonych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czy przestarzałe konta w subskrypcji.

Usługa Azure Advanced Threat Protection (ATP) to rozwiązanie z zakresu zabezpieczeń, które może używać sygnałów usługi Active Directory do identyfikowania, wykrywania i badania zaawansowanych zagrożeń, naruszonych tożsamości i złośliwych działań wewnętrznych.

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Identyfikowanie użytkowników usługi Azure AD oflagowanych na skutek ryzykownego działania](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Jak monitorować działania użytkowników związane z tożsamościami i dostępem w usłudze Azure Security Center](../security-center/security-center-identity-access.md) 

- [Alerty w module analizy zagrożeń usługi Azure Security Center](../security-center/alerts-reference.md) 

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Ograniczanie dostępu do zasobów platformy Azure na podstawie warunków

**Wskazówki**: Azure Database Migration Service używa usługi Azure AD do uwierzytelniania, w którym dostęp warunkowy jest dostępny w celu uzyskania bardziej szczegółowej kontroli dostępu na podstawie warunków zdefiniowanych przez użytkownika, takich jak nazwy logowania użytkowników z określonych zakresów adresów IP, będą musieli używać uwierzytelniania wieloskładnikowego na potrzeby logowania. Szczegółowe zasady zarządzania sesjami uwierzytelniania mogą być również używane dla różnych przypadków użycia.

- [Omówienie dostępu warunkowego platformy Azure](../active-directory/conditional-access/overview.md) 

- [Typowe zasady dostępu warunkowego](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurowanie zarządzania sesją uwierzytelniania z użyciem dostępu warunkowego](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminowanie niezamierzonego ujawnienia poświadczeń

**Wskazówki**: Azure Database Migration Service umożliwia klientom wdrażanie/uruchamianie kodu lub konfiguracji albo trwałych danych potencjalnie przy użyciu tożsamości/wpisów tajnych, dlatego zaleca się wdrożenie skanera poświadczeń w celu identyfikowania poświadczeń w kodzie lub konfiguracjach lub utrwalonych danych. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

Jeśli korzystasz z usługi GitHub, możesz użyć funkcji skanowania natywnego klucza tajnego, aby identyfikować poświadczenia lub inną postać wpisów tajnych w kodzie.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Skanowanie wpisów tajnych usługi GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="privileged-access"></a>Dostęp uprzywilejowany

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: dostęp uprzywilejowany](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regularne przeglądanie i uzgadnianie dostępu użytkowników

**Wskazówki**: usługa Azure Database Migration Service wymaga od użytkowników utworzenia identyfikatora aplikacji (zasady usługi) i klucza uwierzytelniania w Azure Active Directory (Azure AD) w celu migracji do Azure SQL Database wystąpienia zarządzanego w trybie online. Zaleca się usunięcie tego identyfikatora aplikacji po zakończeniu migracji.

- [Jak identyfikator aplikacji jest używany w trybie migracji online](tutorial-sql-server-managed-instance-online.md)

- [Role niestandardowe dla SQL Server migracji do usługi Azure SQL Managed instance w trybie online](resource-custom-roles-sql-db-managed-instance.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurowanie dostępu awaryjnego w usłudze Azure AD

**Wskazówki**: poza Azure Database Migration Service, jako ogólne rozwiązanie zapobiegające przypadkowemu zablokowaniu organizacji usługi Azure AD, skonfiguruj konto dostępu awaryjnego, aby uzyskać dostęp, gdy normalne konta administracyjne nie mogą być używane. Konta dostępu awaryjnego są zwykle wysoce uprzywilejowane i nie powinny być przypisane do konkretnych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych lub nagłych, w których normalne konta administracyjne nie mogą być używane.

Należy upewnić się, że poświadczenia (takie jak hasło, certyfikat lub karta inteligentna) dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom, które są upoważnione do ich używania tylko w sytuacji awaryjnej.

- [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatyzowanie zarządzania uprawnieniami 

**Wskazówki**: Azure Database Migration Service jest zintegrowany z Azure Active Directory do zarządzania zasobami. Użyj funkcji zarządzania prawami usługi Azure AD, aby zautomatyzować przepływy pracy żądania dostępu, w tym przydziały dostępu, przeglądy i wygaśnięcie. Obsługiwane jest również podwójne lub wieloetapowe zatwierdzenie.

- [Co to są przeglądy dostępu do usługi Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Co to jest zarządzanie prawami w usłudze Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Korzystanie ze stacji roboczych z dostępem uprzywilejowanym

**Wskazówka**: Zabezpieczone, izolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa poufnych ról, takich jak administratorzy, deweloperzy i operatorzy usług o kluczowym znaczeniu. Używaj wysoce bezpiecznych stacji roboczych użytkowników i/lub Azure bastionu na potrzeby zadań administracyjnych. Użyj usługi Azure Active Directory, zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender i/lub usługi Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. Zabezpieczone stacje robocze mogą być zarządzane centralnie, aby wymusić bezpieczną konfigurację, w tym silne uwierzytelnianie, oprogramowanie i sprzętowe punkty odniesienia, ograniczony dostęp logiczny i sieciowy.

- [Informacje na temat stacji roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/.md) 

- [Wdrażanie stacji roboczej z dostępem uprzywilejowanym](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>DU-7: Przestrzeganie podejścia wystarczającego zakresu administracji (zasada stosowania najniższych uprawnień) 

**Wskazówki**: Azure Database Migration Service jest zintegrowany z kontrolą dostępu opartą na ROLACH (RBAC) na platformie Azure w celu zarządzania swoimi zasobami. Usługa Azure RBAC umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Dla niektórych zasobów istnieją wstępnie zdefiniowane wbudowane role i te role można zinwentaryzować lub wykonywać względem nich zapytania za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, program Azure PowerShell lub witryna Azure Portal. Uprawnienia przypisywane do zasobów z użyciem kontroli dostępu opartej na rolach platformy Azure powinny być zawsze ograniczone do tego, co jest wymagane przez te role. To uzupełnia podejście dostępu just in time (JIT) usługi Azure AD Privileged Identity Management (PIM) i powinno być okresowo weryfikowane.

Używaj wbudowanych ról do przydzielenia uprawnień i utwórz własną rolę tylko wtedy, gdy jest to wymagane.

- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md)

- [Jak skonfigurować kontrolę RBAC na platformie Azure](../role-based-access-control/role-assignments-portal.md) 

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Szyfrowanie poufnych informacji podczas przesyłania

**Wskazówki**: Azure Database Migration Service szyfruje dane podczas przesyłania ze źródeł skonfigurowanych przez klienta do wystąpienia usługi migracji bazy danych domyślnie przy użyciu protokołu TLS 1,2 lub nowszego. Można wyłączyć tę opcję, jeśli serwer źródłowy nie obsługuje połączenia TLS 1,2, chociaż nie jest to zdecydowanie zalecane. Transfer danych z wystąpienia usługi migracji bazy danych do wystąpienia docelowego jest zawsze szyfrowany.  

Poza Azure Database Migration Service można korzystać z funkcji kontroli dostępu, dlatego dane przesyłane są chronione przed atakami "poza pasmem" (np. przechwytywanie ruchu) przy użyciu szyfrowania, aby upewnić się, że osoby atakujące nie mogą łatwo odczytać lub zmodyfikować danych. Upewnij się, że dla ruchu HTTP wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS w wersji 1.2 lub nowszej.
W przypadku zarządzania zdalnego należy użyć protokołu SSH (dla systemu Linux) lub protokołu RDP/TLS (w systemie Windows) zamiast z nieszyfrowanym protokołem. Przestarzałe wersje protokołu SSL/TLS/SSH, protokoły i słabe szyfry powinny być wyłączone.

W podstawowej infrastrukturze platforma Azure zapewnia domyślnie dane dotyczące ruchu danych między centrami danych platformy Azure.

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informacje o zabezpieczeniach protokołu TLS](/security/engineering/solving-tls1-problem) 

- [Podwójne szyfrowanie danych na platformie Azure podczas przesyłania](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Współużytkowane

## <a name="asset-management"></a>Zarządzanie zasobami

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: zarządzanie elementami zawartości](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>ZZ-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

**Wskazówki**: Upewnij się, że zespoły ds. zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu usługi Azure Security Center. 

W zależności od istniejącej struktury obowiązków zespołu ds. zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu ds. zabezpieczeń lub zespołu lokalnego. Niemniej jednak informacje na temat zabezpieczeń i ryzyka muszą być zawsze agregowane centralnie w ramach danej organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (główna grupa zarządzania) lub do zakresu w postaci grup zarządzania lub określonych subskrypcji. 

Do uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnika zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie grup zarządzania platformy Azure](../governance/management-groups/overview.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu elementów zawartości i metadanych

**Wskazówki**: Zastosuj znaczniki do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

Azure Database Migration Service nie zezwala na uruchamianie aplikacji ani Instalowanie oprogramowania w jego zasobach. 

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center zarządzanie spisem zasobów](../security-center/asset-inventory.md) 

- [Przewodnik po decyzjach dotyczących nazewnictwa i tagowania zasobów](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używanie tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy do inspekcji i ograniczania usług, które użytkownicy mogą inicjować w Twoim środowisku. Usługa Azure Resource Graph umożliwia wykonywanie zapytań dotyczących zasobów i odnajdywanie ich w ramach subskrypcji. Za pomocą usługi Azure Monitor można tworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Menedżera zasobów platformy Azure przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../role-based-access-control/conditional-access-azure-management.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="logging-and-threat-detection"></a>Rejestrowanie i wykrywanie zagrożeń

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>RZ-2: włączanie wykrywania zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

**Wskazówki**: Usługa Azure AD udostępnia następujące dzienniki użytkowników, które mogą być wyświetlane w ramach raportowania usługi Azure AD lub zintegrowane z usługą Azure Monitor, Azure Sentinel lub innymi narzędziami SIEM/monitorowania pod kątem bardziej zaawansowanych przypadków użycia związanych z monitorowaniem i analizą:

Logowania — raporty aktywności logowania zawierają informacje na temat użycia zarządzanych aplikacji i działań użytkownika związane z logowaniem.

Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Działania umieszczane w dziennikach inspekcji to na przykład zmiany wprowadzone w dowolnych zasobach usługi Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Usługa Azure Security Center może również wysyłać alerty dotyczące określonych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia, czy przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń moduł ochrony przed zagrożeniami usługi Azure Security Center może również zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (maszyn wirtualnych, kontenerów, usługi App Service), zasobów danych (bazy danych SQL i magazynu) oraz warstw usługi platformy Azure. Ta funkcja zapewnia wgląd w anomalie kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w usłudze Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Włączanie usługi Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](/azure/security-center/threat-protection)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania działań sieci platformy Azure

**Wskazówki**: Włączanie i zbieranie dzienników zasobów sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń), dzienników przepływów sieciowej grupy zabezpieczeń, dzienników zapory platformy Azure i dzienników zapory aplikacji sieci Web (WAF) na potrzeby analizy zabezpieczeń, aby obsługiwać badania zdarzeń, łowiectwo zagrożeń oraz generowanie alertów zabezpieczeń. Dzienniki przepływów można wysłać do obszaru roboczego Log Analytics Azure Monitor, a następnie użyć Analiza ruchu w celu uzyskania szczegółowych informacji.

Azure Database Migration Service nie tworzy ani nie przetwarza dzienników zapytań DNS, które trzeba włączyć.

- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Dzienniki i metryki usługi Azure Firewall](../firewall/logs-and-metrics.md) 

- [Jak włączyć i używać Analiza ruchu](../network-watcher/traffic-analytics.md) 

- [Monitorowanie za pomocą Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) 

- [Rozwiązania do monitorowania sieci platformy Azure w Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

**Wskazówki**: scentralizowany magazyn rejestrowania i analiza w celu włączenia korelacji. Dla każdego źródła dziennika upewnij się, że masz przypisanego właściciela danych, wskazówki dotyczące dostępu, lokalizację magazynu, narzędzia używane do przetwarzania i uzyskiwania dostępu do danych oraz wymagania dotyczące przechowywania danych.

Upewnij się, że masz integrację dzienników aktywności platformy Azure z centralnym logowaniem. Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

Wiele organizacji decyduje się na korzystanie z platformy Azure na potrzeby danych "gorąca", które są często używane, i usługi Azure Storage dla danych "zimnych", które są rzadziej używane.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Przygotowanie — aktualizowanie procesu reagowania na zdarzenia na platformie Azure

**Wskazówka**: Upewnij się, że organizacja ma procesy reagowania na zdarzenia związane z bezpieczeństwem, zaktualizował te procesy na platformie Azure i regularnie wykonuje je w celu zapewnienia gotowości.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) 

- [Przewodnik po odpowiedziach na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Przygotowanie — powiadomienie o zdarzeniu konfiguracji

**Wskazówka**: Skonfiguruj informacje kontaktowe dotyczące zdarzenia zabezpieczeń w usłudze Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nieuprawnioną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia. 

- [Jak ustawić kontakt zabezpieczeń usługi Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Wykrywanie i analiza — tworzenie zdarzeń na podstawie alertów o wysokiej jakości

**Wskazówka**: Upewnij się, że masz proces tworzenia alertów o wysokiej jakości i mierzenia jakości alertów. Dzięki temu można wyciągać wnioski z przeszłych zdarzeń i określić priorytety alertów dla analityków, dzięki czemu nie będą oni tracić czasu na wyniki fałszywie dodatnie. 

Alerty o wysokiej jakości można tworzyć na podstawie doświadczeń z przeszłych zdarzeń, sprawdzonych źródeł społeczności oraz narzędzi przeznaczonych do generowania i czyszczenia alertów przez połączenie i skorelowanie różnorodnych źródeł sygnałów. 

Azure Security Center oferuje alerty wysokiej jakości dla wielu zasobów platformy Azure. Można użyć łącznika danych usługi ASC do przesyłania strumieniowego alertów do usługi Azure Sentinel. Usługa Azure Sentinel umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

Eksportuj alerty i zalecenia usługi Azure Security Center przy użyciu funkcji eksportowania, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksportuj alerty i zalecenia ręcznie lub w stały, ciągły sposób.

- [Jak skonfigurować eksportowanie](../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorowanie usługi Azure Security Center**: Yes

**Odpowiedzialność**: Klient

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>RZ-4: wykrywanie i analiza — badanie zdarzenia

**Wskazówki**: Upewnij się, że analitycy mogą wykonywać zapytania i używać różnorodnych źródeł danych podczas badania potencjalnych zdarzeń w celu uzyskania pełnego obrazu tego, co się stało. Należy zbierać różne dzienniki, aby śledzić działania potencjalnej osoby atakującej w ramach całego łańcucha zagrożeń, aby uniknąć efektu martwego pola.  Należy również upewnić się, że szczegółowe informacje i wnioski są rejestrowane dla innych analityków i na potrzeby przyszłych badań.  

Źródła danych do badania obejmują scentralizowane źródła dzienników, które są już zbierane w ramach usług i działających systemów, ale mogą również obejmować:

- Dane sieciowe — użyj dzienników przepływów sieciowych grup zabezpieczeń, usługi Azure Network Watcher i usługi Azure Monitor do przechwytywania dzienników przepływu sieci i innych informacji analitycznych. 

- Migawki uruchomionych systemów: 

    - Użyj funkcji migawek maszyny wirtualnej platformy Azure, aby utworzyć migawkę dysku działającego systemu. 

    - Użyj natywnej możliwości zrzutu pamięci systemu operacyjnego, aby utworzyć migawkę pamięci uruchomionego systemu.

    - Użyj funkcji migawki w ramach usług platformy Azure lub w ramach własnego oprogramowania, aby utworzyć migawki uruchomionych systemów.

Usługa Azure Sentinel zapewnia szeroką analizę danych dla praktycznie każdego źródła dzienników i portalu zarządzania przypadkami do zarządzania pełnym cyklem życia zdarzeń. Informacje analityczne podczas badania mogą być powiązane ze zdarzeniami pod kątem śledzenia i raportowania. 

- [Tworzenie migawek dysku maszyny z systemem Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Tworzenie migawek dysku maszyny z systemem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informacje diagnostyczne pomocy technicznej oraz kolekcja zrzutów pamięci platformy Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Badanie zdarzeń za pomocą usługi Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Wykrywanie i analiza — określanie priorytetów zdarzeń

**Wskazówki**: Zapewnij kontekst analitykom, na których zdarzeniach należy skupić się w pierwszej kolejności w oparciu o poziom ważności alertów i poufność aktywów. 

Usługa Azure Security Center przypisuje poziom ważności do każdego alertu, aby pomóc w ustaleniu, które alerty powinny być zbadane w pierwszej kolejności. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analizie używanych do wystawiania alertu, a także poziom pewności, że istniało złośliwe zamiar w odniesieniu do działania, które prowadziło do alertu.

Ponadto oznacz zasoby przy użyciu tagów i utwórz system nazewnictwa, aby zidentyfikować i sklasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zawieranie, eliminowanie i odzyskiwanie — automatyzacja obsługi zdarzeń

**Wskazówka**: Automatyzuj ręczne powtarzające się zadania w celu przyspieszenia czasu reakcji i zmniejszenia obciążenia dla analityków. Wykonanie ręcznych zadań trwa dłużej, spowalnia każde zdarzenie i zmniejsza liczbę zdarzeń, które może obsłużyć analityk. Zadania wykonywane ręcznie zwiększają również zmęczenie analityków, co zwiększa ryzyko wystąpienia błędu ludzkiego, który powoduje opóźnienia, a także pogarsza zdolność analityków do skoncentrowania się na złożonych zadaniach. Korzystając z funkcji automatyzacji przepływów pracy w usłudze Azure Security Center i Azure Sentinel, można automatycznie wyzwalać akcje lub uruchamiać element playbook w odpowiedzi na przychodzące alerty zabezpieczeń. Element playbook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w usłudze Security Center](../security-center/workflow-automation.md)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurowanie automatycznych reakcji na zagrożenia w usłudze Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="posture-and-vulnerability-management"></a>Stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: stan zabezpieczeń i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Przeprowadzanie regularnej symulacji ataków

**Wskazówka**: W razie potrzeby przeprowadź test penetracyjny lub działania typu „red team” na zasobach platformy Azure i zapewnij korektę wszystkich krytycznych ustaleń dotyczących zabezpieczeń.
Postępuj zgodnie z regułami testowania penetracji w chmurze firmy Microsoft, aby upewnić się, że testy penetracyjne nie naruszają zasad firmy Microsoft. Korzystaj ze strategii firmy Microsoft i wykonywania testów typu „red team” i testów na żywo w zarządzanej przez firmę Microsoft infrastrukturze, usługach i aplikacjach w chmurze.

- [Testy penetracyjne na platformie Azure](../security/fundamentals/pen-testing.md)

- [Reguły testów penetracyjnych zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Testy typu „red team” w chmurze firmy Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="governance-and-strategy"></a>Ład i strategia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ład i strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiowanie strategii zarządzania elementami zawartości i ochrony danych 

**Wskazówka**: Zadbaj o to, aby udokumentować i udostępnić wyraźną strategię monitorowania i ochrony systemów i danych. Ustalaj priorytety odnajdywania, oceny, ochrony i monitorowania danych oraz systemów o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Standard klasyfikacji danych zgodny z ryzykiem biznesowym

-   Wgląd organizacji zabezpieczeń w czynniki ryzyka i spis elementów zawartości 

-   Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 

-   Bezpieczeństwo elementów zawartości w całym cyklu życia

-   Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

-   Korzystanie z natywnych i zewnętrznych funkcji ochrony danych platformy Azure

-   Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie przesyłania i w czasie spoczynku

-   Odpowiednie standardy kryptograficzne

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazynowanie danych platformy Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie elementami zawartości](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Test porównawczy zabezpieczeń platformy Azure — ochrona danych](/azure/security/benchmarks/security-controls-v2-data-protection)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa 

**Wskazówka**: Ustanów strategię całego przedsiębiorstwa w zakresie segmentacji dostępu do elementów zawartości przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych mechanizmów kontroli.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie dla różnych typów kontroli, w tym zabezpieczeń sieci, modeli tożsamości i dostępu, a także modeli uprawnień/dostępu i procesów ludzkich.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównywanie segmentacji sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stanem zabezpieczeń

**Wskazówka**: Nieustannie mierz i ograniczaj ryzyko dla poszczególnych elementów zawartości i środowiska, w którym są hostowane. Ustalaj priorytety elementów zawartości o wysokiej wartości i wysoce narażonych na ataki obszarów, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Test porównawczy zabezpieczeń platformy Azure — stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Dopasuj role organizacji, obowiązki i odpowiedzialność

**Wskazówka**: Zadbaj o to, aby udokumentować i udostępnić wyraźną strategię dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu wyraźnego określenia odpowiedzialności za decyzje dotyczące zabezpieczeń, informując wszystkie osoby o współużytkowanym modelu odpowiedzialności i informując zespoły techniczne o technologii do zabezpieczania chmury.

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 1 — ludzie: informowanie zespołów o podróży zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: informowanie zespołów o technologii zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiowanie strategii zabezpieczeń sieci

**Wskazówka**: Ustanów metodę zabezpieczeń sieci platformy Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

-   Model segmentacji sieci wirtualnej zgodny ze strategią segmentacji przedsiębiorstwa

-   Strategia korygowania w różnych scenariuszach zagrożeń i ataków

-   Strategia internetowa i dotycząca brzegowego ruchu przychodzącego i wychodzącego

-   Strategia międzyłączności w chmurze hybrydowej i środowisku lokalnym

-   Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, architektura sieci referencyjnej)

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Test porównawczy zabezpieczeń platformy Azure — zabezpieczenia sieci](/azure/security/benchmarks/security-controls-v2-network-security)

- [Omówienie zabezpieczeń sieci platformy Azure](../security/fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dotyczącej tożsamości i dostępu uprzywilejowanego

**Wskazówka**: Ustanów metody obsługi tożsamości i dostępu uprzywilejowanego na platformie Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

-   Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

-   Ochrona użytkowników z wysokim poziomem uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie tożsamością](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Test porównawczy zabezpieczeń platformy Azure — dostęp uprzywilejowany](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń zarządzania tożsamościami platformy Azure](../security/fundamentals/identity-management-overview.md)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

**Wskazówka**: Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i eliminować zagrożenia oraz spełnić wymagania dotyczące zgodności. Ustalaj priorytety, dostarczając analitykom alerty wysokiej jakości i bezproblemowe środowisko, aby umożliwić im skoncentrowanie się na zagrożeniach, a nie na integracji i ręcznych działaniach. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Rola i obowiązki organizacji w zakresie operacji zabezpieczeń (SecOps) 

-   Dobrze zdefiniowany proces reagowania na zdarzenia zgodnie z NIST lub inną platformą branżową 

-   Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

-   Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu rozwiązania SIEM, natywnych możliwości platformy Azure i innych źródeł 

-   Plan komunikacji i powiadomień z klientami, dostawcami i publicznymi zainteresowanymi stronami

-   Używanie natywnych i zewnętrznych platform Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, postępowania dowodowe oraz korygowanie i eliminowanie ataków

-   Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — reagowanie na zdarzenia](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 4 — proces. Aktualizowanie procesów reagowania na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Przewodnik dotyczący decyzji w zakresie platformy wdrażania Azure, rejestrowania i raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie na platformie Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
