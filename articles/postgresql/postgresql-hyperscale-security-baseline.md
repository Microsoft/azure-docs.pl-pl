---
title: Linia bazowa zabezpieczeń Azure dla Azure Database for PostgreSQL-ze skalowaniem
description: Linia bazowa zabezpieczeń Azure Database for PostgreSQL-ze skalą zawiera wskazówki i zasoby związane z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 256b061e11ac77af9d229e3bd8f96b033e925f6d
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284514"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---hyperscale"></a>Linia bazowa zabezpieczeń Azure dla Azure Database for PostgreSQL-ze skalowaniem

Ta linia bazowa zabezpieczeń stosuje wskazówki dotyczące [usługi Azure Security test w wersji 1.0](../security/benchmarks/overview-v1.md) do Azure Database for PostgreSQL-preskalowania. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące Azure Database for PostgreSQL-preskalowania. 

> [!NOTE]
> **Kontrolki** nie mają zastosowania do Azure Database for PostgreSQL-ze skalowaniem lub dla których odpowiedzialności są wykluczane przez firmę Microsoft. Aby dowiedzieć się, w jaki sposób usługa Azure Database for PostgreSQL-CreateMapping została całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zapoznaj się z **[plikiem mapowania pełnego zabezpieczenia Azure Database for PostgreSQL-ze skalą](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/azure-database-for-postgresql-hyperscale-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: bezpieczeństwo sieci](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: zapora serwera Azure Database for PostgreSQL zapobiega wszystkim dostępowi do węzła koordynatora w skali (Citus) do momentu określenia komputerów, które mają uprawnienia. Zapora przyznaje dostęp do serwera na podstawie źródłowego adresu IP każdego żądania. Aby skonfigurować zaporę, należy utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Reguły zapory można tworzyć na poziomie serwera.

- [Jak skonfigurować reguły zapory w Azure Database for PostgreSQL-Citus](concepts-hyperscale-firewall-rules.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. DBforPostgreSQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforPostgreSQL 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbforpostgresql-1-1.md)]

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla ustawień sieciowych i zasobów sieciowych skojarzonych z wystąpieniami Azure Database for PostgreSQL przy użyciu Azure Policy. Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. Network", aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci wystąpień Azure Database for PostgreSQL.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Przykłady Azure Policy dla sieci](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Jak utworzyć Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: na potrzeby rejestrowania inspekcji płaszczyzny kontroli, Włącz ustawienia diagnostyki dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla zasobów platformy Azure.

Dodatkowo dzienniki pozyskiwania za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez funkcję Citus. W ramach Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont magazynu do przechowywania długoterminowego/archiwizowania. Alternatywnie możesz włączyć i zarejestrować dane na platformie Azure wskaźnikowej lub zdarzeniu zabezpieczeń innych firm (SIEM). 

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Metryki w obszarze skalowanie (Citus)](concepts-hyperscale-monitoring.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: funkcja do skalowania (Citus) udostępnia metryki dla każdego węzła w grupie serwerów. Metryki zapewniają wgląd w zachowanie obsługi zasobów. Każda Metryka jest emitowana z częstotliwością jednej minuty i ma do 30 dni historii.

W przypadku inspekcji płaszczyzny kontroli Włącz rejestrowanie ustawień diagnostycznych dziennika aktywności platformy Azure i Wyślij dzienniki do obszaru roboczego Log Analytics, centrum zdarzeń platformy Azure lub konta usługi Azure Storage w celu archiwizacji. Korzystając z danych dziennika aktywności platformy Azure, można określić "co, kto i kiedy" dla operacji zapisu (PUT, POST, DELETE) wykonanych na poziomie płaszczyzny kontroli dla zasobów platformy Azure.

Dodatkowo dzienniki pozyskiwania za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez funkcję Citus. W ramach Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont magazynu do przechowywania długoterminowego/archiwizowania. Alternatywnie możesz włączyć i zarejestrować dane na platformie Azure wskaźnikowej lub zdarzeniu zabezpieczeń innych firm (SIEM). 

- [Metryki w obszarze skalowanie (Citus)](concepts-hyperscale-monitoring.md)

- [Jak włączyć ustawienia diagnostyczne dla dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: w Azure monitor, w obszarze roboczym log Analytics używanym do przechowywania dzienników Citus, Ustaw okres przechowywania zgodnie z regulacjami zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

- [Jak ustawić parametry przechowywania dzienników dla obszarów roboczych Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Przechowywanie dzienników zasobów na koncie usługi Azure Storage](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs#send-to-azure-storage)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników z wystąpień Citus (deskala) na potrzeby nietypowego zachowania. Użyj Log Analytics Azure Monitor, aby przejrzeć dzienniki i wykonywać zapytania dotyczące danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

- [Aby uzyskać więcej informacji na temat Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak wykonywać niestandardowe zapytania w Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: możesz włączyć ustawienia diagnostyczne dla funkcji Citus (Przeskaluj) i wysłać dzienniki do obszaru roboczego log Analytics. Można skonfigurować i odebrać alert oparty na metrykach monitorowania dla usług platformy Azure. Użyj Log Analytics Azure Monitor, aby przejrzeć dzienniki i wykonywać zapytania dotyczące danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

Dołącz obszar roboczy Log Analytics do usługi Azure o, ponieważ zapewnia ona rozwiązanie do automatycznej reakcji aranżacji zabezpieczeń (). Pozwala to na tworzenie i używanie automatycznych rozwiązań elementy PlayBook w celu korygowania problemów z zabezpieczeniami.

- [Metryki w obszarze skalowanie (Citus)](howto-hyperscale-alert-on-metric.md)

- [Jak skonfigurować ustawienia diagnostyczne dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: przechowywanie spisu kont użytkowników, które mają dostęp administracyjny do płaszczyzny kontroli (np. Azure Portal) wystąpień Citus. Ponadto należy zachować spis kont administracyjnych, które mają dostęp do płaszczyzny danych (w samej bazie danych) wystąpień Citus.

Funkcja Citus) nie obsługuje wbudowanej kontroli dostępu opartej na rolach, ale można tworzyć role niestandardowe na podstawie określonych operacji dostawcy zasobów.

Ponadto aparat PostgreSQL używa ról w celu kontrolowania dostępu do obiektów bazy danych, a nowo utworzona grupa serwerów w postaci Citusej zawiera kilka wstępnie zdefiniowanych ról. Aby zmodyfikować uprawnienia użytkownika, użyj standardowych poleceń PostgreSQL przy użyciu narzędzia, takiego jak PgAdmin lub PSQL.

- [Informacje o rolach niestandardowych dla subskrypcji platformy Azure](../role-based-access-control/custom-roles.md) 

- [Informacje o operacjach dostawcy zasobów Azure Database for PostgreSQL](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql) 

- [Informacje na temat zarządzania dostępem Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-security#access-management)

- [Jak tworzyć użytkowników w Azure Database for PostgreSQL-Citus](howto-hyperscale-create-users.md)

- [Jak nawiązać połączenie z PostgreSQL-Citus) przy użyciu PSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#connect-to-the-database-using-psql)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Azure Active Directory (Azure AD) nie ma koncepcji domyślnych haseł. Inne zasoby platformy Azure wymagające hasła wymuszają utworzenie hasła przy użyciu wymagań dotyczących złożoności i minimalnej długości hasła, które różnią się w zależności od usługi. Użytkownik jest odpowiedzialny za aplikacje innych firm i usługi Marketplace, które mogą korzystać z domyślnych haseł.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących używania dedykowanych kont administracyjnych, które są używane do uzyskiwania dostępu do wystąpień Citus. Konta administratorów służące do zarządzania zasobem platformy Azure są powiązane z Azure Active Directory (Azure AD), istnieją również konta administratora serwera lokalnego, które istnieją w grupie serwerów Citus (w celu zarządzania uprawnieniami dostępu do bazy danych). Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych w usłudze Azure AD.

- [Informacje o tożsamości i dostępie Azure Security Center](../security-center/security-center-identity-access.md)

- [Jak tworzyć użytkowników w Azure Database for PostgreSQL-Citus](howto-hyperscale-create-users.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Aby uzyskać dostęp do Azure Portal Włącz uwierzytelnianie wieloskładnikowe Azure Active Directory (Azure AD), a następnie postępuj zgodnie z zaleceniami Azure Security Center zarządzaniem tożsamościami i dostępem.

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorować tożsamość i dostęp w Azure Security Center](../security-center/security-center-identity-access.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych 

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="37-alert-on-account-login-behavior-deviation"></a>3,7: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku.

Użyj funkcji wykrywania ryzyka usługi Azure AD, aby wyświetlać alerty i raporty na temat ryzykownego zachowania użytkowników.

- [Jak wdrożyć Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Omówienie wykrywania ryzyka usługi Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby umożliwić dostęp do portalu i Azure Resource Manager tylko z określonych logicznych grup zakresów adresów IP lub krajów/regionów.

- [Jak skonfigurować nazwane lokalizacje na platformie Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji do zarządzania zasobami PostgreSQL. Usługa Azure AD chroni dane przy użyciu silnego szyfrowania danych przechowywanych i przesyłanych. Usługa Azure AD również Sole, skróty i bezpieczne przechowywanie poświadczeń użytkownika.

Użytkownicy w grupie serwerów Citus (moja skala) nie mogą być bezpośrednio powiązane z kontami usługi Azure AD. Aby zmodyfikować uprawnienia użytkownika do dostępu do obiektu bazy danych, użyj standardowych poleceń PostgreSQL z narzędziami, takimi jak PgAdmin lub PSQL.

- [Modyfikowanie uprawnień dla ról użytkowników](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users#how-to-modify-privileges-for-user-role)

- [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: przegląd i uzgadnianie dostępu dla obu użytkowników, którzy mają dostęp do lokalnej bazy danych, a także za pomocą usługi Azure Active Directory (Azure AD) do zarządzania zasobami PostgreSQL.

W przypadku użytkowników mających dostęp do zarządzania zasobami platformy Azure dla bazy danych Przejrzyj dzienniki usługi Azure AD, aby ułatwić odnajdywanie starych kont. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, uzyskiwać dostęp do aplikacji firmowych, które mogą być używane w celu uzyskania dostępu do funkcji Moje skalowanie (Citus) i przypisań ról. Dostęp użytkowników powinien być regularnie przeglądany, na przykład co 90 dni, aby upewnić się, że tylko Ci użytkownicy mają stały dostęp.

- [Przeglądanie użytkowników PostgreSQL i przypisanych ról](https://www.postgresql.org/docs/current/database-roles.html)

- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: w Azure Active Directory (Azure AD) masz dostęp do źródeł działań związanych z logowaniem do usługi Azure AD, inspekcją i ryzykiem dzienników zdarzeń, które umożliwiają integrację z dowolnym narzędziem Siem/monitoring.

Proces ten można usprawnić, tworząc ustawienia diagnostyczne dla kont użytkowników usługi Azure AD i wysyłając dzienniki inspekcji i dzienniki logowania do obszaru roboczego Log Analytics. Żądane alerty można skonfigurować w obszarze roboczym Log Analytics.

- [Jak zintegrować dzienniki aktywności platformy Azure z usługą Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: odchylenia zachowania alertu dotyczącego logowania na koncie

**Wskazówki**: Użyj funkcji ochrony tożsamości i wykrywania ryzyka Azure Active Directory (Azure AD), aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania na poziomie usługi Azure AD. Automatyczne odpowiedzi można włączyć za pomocą wskaźnikowego platformy Azure, aby zaimplementować odpowiedzi na zabezpieczenia organizacji.

Możesz również pozyskiwanie dzienników na platformie Azure — wskaźnik do dalszych badań.

- [Omówienie Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak wyświetlić ryzykowne logowania w usłudze Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak dołączyć wskaźnik na platformie Azure](../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: obecnie niedostępne; Skrytka klienta nie jest jeszcze obsługiwana dla funkcji Citus.

- [Lista obsługiwanych usług Skrytka klienta](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu wystąpień Citus (lub powiązanych), które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: implementowanie oddzielnych subskrypcji i/lub grup zarządzania na potrzeby tworzenia, testowania i produkcji. Użyj kombinacji ról administracyjnych i reguł zapory, aby wyizolować i ograniczyć dostęp sieciowy do wystąpień Azure Database for PostgreSQL.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Informacje o regułach zapory w Azure Database for PostgreSQL-ze skalowaniem (Citus)](concepts-hyperscale-firewall-rules.md)

- [Informacje o rolach w funkcji skalowania automatycznego (Citus)](howto-hyperscale-create-users.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: połączenia aplikacji klienckich z węzłem koordynatora Citus () wymagają Transport Layer Security (TLS) 1,2. Wymuszanie połączeń TLS między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją.

W przypadku wszystkich serwerów Azure Database for PostgreSQL, które są obsługiwane za pomocą Azure Portal, wymuszanie połączeń TLS jest domyślnie włączone.

W niektórych przypadkach aplikacje innych firm wymagają pliku certyfikatu lokalnego wygenerowanego na podstawie pliku certyfikatu zaufanego urzędu certyfikacji (. cer), aby bezpiecznie nawiązać połączenie.

- [Jak skonfigurować protokół TLS w Azure Database for PostgreSQL-Citus](concepts-hyperscale-ssl-connection-security.md)

- [Aplikacje, które wymagają weryfikacji certyfikatu na potrzeby łączności TLS](concepts-hyperscale-ssl-connection-security.md)

**Odpowiedzialność**: Współużytkowane

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. DBforPostgreSQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforPostgreSQL 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.dbforpostgresql-4-4.md)]

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów 

**Wskazówki**: Użyj kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure, aby kontrolować dostęp do płaszczyzny kontroli Citus (np. Azure Portal). Kontrola RBAC platformy Azure nie ma wpływu na uprawnienia użytkowników w ramach bazy danych.

Aby zmodyfikować uprawnienia użytkownika na poziomie bazy danych, użyj standardowych poleceń PostgreSQL przy użyciu narzędzia, takiego jak PgAdmin lub PSQL.

- [Jak skonfigurować usługę Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Jak skonfigurować dostęp użytkowników przy użyciu programu SQL dla Azure Database for PostgreSQL](howto-hyperscale-create-users.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: co najmniej raz dziennie Azure Database for PostgreSQL funkcja Citus na potrzeby tworzenia migawek kopii zapasowych plików danych i dziennika transakcji bazy danych. Kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w okresie przechowywania. (Okres przechowywania jest obecnie 35 dni dla wszystkich klastrów). Wszystkie kopie zapasowe są szyfrowane przy użyciu szyfrowania AES 256-bitowego. Oferta PostgreSQL (Citus) używa kluczy zarządzanych przez firmę Microsoft na potrzeby szyfrowania.

- [Informacje o szyfrowaniu kopii zapasowych usługi Azure PostgreSQL-Citus)](concepts-hyperscale-backup.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wykonywane w wystąpieniach produkcyjnych funkcji Citus) i innych krytycznych lub związanych z nimi zasobów.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Zarządzanie lukami w zabezpieczeniach](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: obecnie niedostępne; Azure Security Center nie obsługuje jeszcze oceny luk w zabezpieczeniach dla Azure Database for PostgreSQL-Citus.

- [Pokrycie funkcji dla usług Azure PaaS Services w Azure Security Center](../security-center/features-paas.md)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich zasobów (w tym wystąpień Citus) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Jak wyświetlić subskrypcje platformy Azure](/powershell/module/az.accounts/get-azsubscription)

- [Opis kontroli RBAC platformy Azure](../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do wystąpień Citus i innych powiązanych zasobów, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie jest to konieczne, do organizowania i śledzenia wystąpień Citus oraz powiązanych zasobów. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../cost-management-billing/manage/create-subscription.md)

- [Tworzenie grup zarządzania](../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Użyj usługi Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów

- Dozwolone typy zasobów

Ponadto Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj usługi Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:

- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Ponadto Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak tworzyć zapytania za pomocą usługi Azure Graph](../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj usługi Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, korzystając z następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współpracy użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management". Może to uniemożliwiać tworzenie i wprowadzanie zmian w zasobach w środowisku wysokiego poziomu zabezpieczeń, takich jak wystąpienia Citus () zawierające informacje poufne.

- [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: bezpieczna konfiguracja](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla wystąpień Citus () przy użyciu Azure Policy. Użyj Azure Policy, aby utworzyć zasady niestandardowe do inspekcji lub wymuszania konfiguracji sieci wystąpień Azure Database for PostgreSQL.

Ponadto Azure Resource Manager ma możliwość eksportowania szablonu w JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają/przekroczą wymagania dotyczące zabezpieczeń organizacji.

- [Jak wyświetlić dostępne aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

- [Eksport jednego i kilku zasobów do szablonu w Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj zasad platformy Azure [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.  Ponadto możesz użyć szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganych przez organizację. 

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Przegląd szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Jeśli używasz niestandardowych definicji zasad platformy Azure dla wystąpień Citus i powiązanych zasobów, użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

- [Jak przechowywać kod w usłudze Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentacja Azure Repos](/azure/devops/repos/)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj zasad platformy Azure [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.  Ponadto możesz użyć szablonów Azure Resource Manager, aby zachować konfigurację zabezpieczeń zasobów platformy Azure wymaganych przez organizację. 

- [Zrozumienie efektów Azure Policy](../governance/policy/concepts/effects.md)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md)

- [Przegląd szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. DBforPostgreSQL", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj zasad platformy Azure [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymuszać konfiguracje dla wystąpień Azure Database for PostgreSQL i powiązanych zasobów.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../governance/policy/tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: Azure Database for PostgreSQL-Citus nie obsługuje obecnie zarządzanych tożsamości. Podczas tworzenia serwera Azure Database for PostgreSQL należy podać poświadczenia dla użytkownika administratora. W interfejsie Azure Portal można tworzyć dodatkowe role użytkowników.

- [Tworzenie Azure Database for PostgreSQL-ze skalowaniem (Citus)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#create-a-hyperscale-citus-server-group)

- [Tworzenie dodatkowych ról użytkownika](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users#how-to-create-additional-user-roles)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń ułatwia również przenoszenie odnalezionych poświadczeń do bezpieczniejszych lokalizacji, takich jak usługa Azure Key Vault.

- [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Obrona złośliwego oprogramowania](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure — na przykład na potrzeby skalowania (Citus) — ale nie jest ono uruchamiane w treści klienta.

Przed przeskanowaniem zawartość przekazywana do zasobów platformy Azure, które nie są obliczeniowe, takich jak App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL itp. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [test dotyczący zabezpieczeń platformy Azure: odzyskiwanie danych](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: Azure Database for PostgreSQL — funkcja Citus — umożliwia automatyczne tworzenie kopii zapasowych każdego węzła i przechowywanie ich w magazynie lokalnie nadmiarowy. Za pomocą kopii zapasowych można przywrócić klaster z Citus w określonym czasie.

- [Jak utworzyć kopię zapasową i przywrócić w Azure Database for PostgreSQL-Citus](concepts-hyperscale-backup.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. DBforPostgreSQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforPostgreSQL 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbforpostgresql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: co najmniej raz dziennie Azure Database for PostgreSQL wykonuje kopie zapasowe migawek plików danych i dziennika transakcji bazy danych. Kopie zapasowe umożliwiają przywrócenie serwera do dowolnego punktu w czasie w okresie przechowywania. Okres przechowywania jest obecnie 35 dni dla wszystkich klastrów. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES.

W regionach platformy Azure, które obsługują strefy dostępności, migawki kopii zapasowych są przechowywane w trzech strefach dostępności. Tak długo, jak co najmniej jedna strefa dostępności jest w trybie online, klaster Citus jest dostępnych.

- [Jak utworzyć kopię zapasową i przywrócić w Azure Database for PostgreSQL-Citus](concepts-hyperscale-backup.md)

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. DBforPostgreSQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforPostgreSQL 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbforpostgresql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: w Azure Database for PostgreSQL przywracanie klastra ze skalą ze skalowaniem (Citus) powoduje utworzenie nowego klastra z kopii zapasowych pierwotnych węzłów. Klaster można przywrócić do dowolnego punktu w czasie w ciągu ostatnich 35 dni. Proces przywracania powoduje utworzenie nowego klastra w tym samym regionie, subskrypcji i grupie zasobów platformy Azure co wersja oryginalna. Nowa konfiguracja klastra jest taka sama jak konfiguracja oryginalnej klastra: taka sama liczba węzłów, liczba rdzeni wirtualnych, rozmiar magazynu i role użytkowników.

Ustawienia zapory i parametry serwera PostgreSQL nie są zachowywane z oryginalnej grupy serwerów; są one resetowane do wartości domyślnych. Zapora uniemożliwi wszystkie połączenia. Po przywróceniu trzeba ręcznie dostosować te ustawienia.

- [Jak utworzyć kopię zapasową i przywrócić w Azure Database for PostgreSQL-Citus](concepts-hyperscale-backup.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: nie można przywrócić usuniętych klastrów Citus (). Jeśli usuniesz klaster, wszystkie węzły należące do klastra zostaną usunięte i nie będzie można go odzyskać. Aby chronić zasoby klastra przed przypadkowym usunięciem lub nieoczekiwanymi zmianami, Administratorzy mogą korzystać z blokad zarządzania.

- [Jak utworzyć kopię zapasową i przywrócić w Azure Database for PostgreSQL-Citus](concepts-hyperscale-backup.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: reagowanie na zdarzenia](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówka**: Utwórz przewodnik odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu. 

- [Jak skonfigurować automatyzację przepływu pracy w programie Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub metryki używanej do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które doprowadziło do alertu. 

Dodatkowo jasno Oznacz subskrypcje (na przykład produkcyjny, nieprodukcyjny) i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemu w regularnych erze. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami. 

- [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych klienta został uzyskany przez nielegalną lub nieautoryzowaną osobę.  Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane. 

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. 

- [Jak skonfigurować eksport ciągły](../security-center/continuous-export.md) 

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w programie Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Logic Apps" na temat alertów zabezpieczeń i zaleceń. 

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../security-center/workflow-automation.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz test [porównawczy zabezpieczeń platformy Azure: testy penetracji i czerwone ćwiczenia zespołu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [W tym miejscu znajdziesz więcej informacji na temat strategii i wykonywania trójwymiarowych operacji tworzenia zespołu i testowania aplikacji na żywo w witrynie Microsoft.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpowiedzialność**: Współużytkowane

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](/azure/security/benchmarks/security-baselines-overview)
