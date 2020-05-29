---
title: Przykłady zakrytych w usłudze CIS Microsoft Azure przykładowe kontrolki testu
description: Rekomendacja zaleceń usługi CIS Microsoft Azure przykłady planów testów porównawczych do Azure Policy.
ms.date: 05/12/2020
ms.topic: sample
ms.openlocfilehash: b6029e147af49cfb91078c6228615c32ad2db5fe
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167234"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Rekomendacja zaleceń przykładowego planu testów usługi CIS Microsoft Azure

W tym artykule szczegółowo przedstawiono sposób, w jaki plan platformy Azure Microsoft Azure fundacje planów testów porównawczych jest mapowany na zaleceń usługi CIS Microsoft Azure dotyczącej testów porównawczych. Aby uzyskać więcej informacji na temat zaleceń, zobacz artykuł dotyczący [testów usług CIS Microsoft Azure](https://www.cisecurity.org/benchmark/azure/).

Poniżej przedstawiono mapowania zaleceń dotyczących usługi **CIS Microsoft Azure testy porównawcze 1.1.0** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania rekomendacji.
Liczne zamapowane zalecenia są implementowane z inicjatywy [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz pozycję ** \[ wersja zapoznawcza inspekcja w wersji zapoznawczej usługi \] CIS Microsoft Azure 1.1.0** .

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich uprzywilejowanych użytkowników

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które ułatwiają monitorowanie, kiedy uwierzytelnianie wieloskładnikowe nie jest włączone na kontach Azure Active Directory uprzywilejowanych.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- W ramach usługi MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich użytkowników bez uprawnień

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która ułatwia monitorowanie, kiedy uwierzytelnianie wieloskładnikowe nie jest włączone na kontach Azure Active Directory bez uprawnień.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 Upewnij się, że nie ma żadnych użytkowników-Gości

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które pomagają monitorować konta gościa, które mogą wymagać usunięcia.

- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1,23 upewnij się, że nie są tworzone role niestandardowego właściciela subskrypcji

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które pomagają monitorować niestandardowe role właściciela subskrypcji, które mogą wymagać usunięcia.

- Role niestandardowego właściciela subskrypcji nie powinny istnieć

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2,1 Upewnij się, że wybrano warstwę cenową standardowa

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga monitorować sieci i maszyny wirtualne, na których nie włączono warstwy Security Center Standard.

- Należy wybrać Security Center warstwy cenowej Standard

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2,2 Upewnij się, że "Automatyczna obsługa administracyjna agenta monitorowania" jest ustawiona na "on"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że Automatyczna obsługa administracyjna agenta log Analytics jest włączona.

- Automatyczna obsługa administracyjna agenta monitorowania Log Analytics powinna być włączona w ramach subskrypcji

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj aktualizacje systemu" nie jest "wyłączone"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że aktualizacje systemu są zainstalowane na maszynach wirtualnych.

- Należy zainstalować aktualizacje systemu na maszynach

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj luki w zabezpieczeniach systemu operacyjnego" nie jest wyłączone

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która ułatwia monitorowanie luk w zabezpieczeniach maszyn wirtualnych unremediated.

- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 Upewnij się, że domyślne ustawienie zasad ASC "Monitor Endpoint Protection nie jest" wyłączone "

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że program Endpoint Protection jest włączony na maszynach wirtualnych.

- Monitoruj brakujące Endpoint Protection w Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj szyfrowanie dysków" nie jest wyłączone

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że dyski maszyny wirtualnej są szyfrowane.

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2,7 upewnij się, że domyślne ustawienie zasad ASC "monitorowanie sieciowych grup zabezpieczeń" nie jest "wyłączone"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga chronić maszyny wirtualne dostępne z Internetu.

- Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2,9 upewnij się, że domyślne ustawienie zasad ASC "Włączanie monitorowania zapory nowej generacji (zapory następnej generacji)" nie jest wyłączone

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) chroniące podsieci i maszyny wirtualne przed zagrożeniami przez ograniczenie dostępu. Zasady Security Center, do których odwołują się te usługi CIS Microsoft Azure zalecenia porównawcze zostały zastąpione przez dwa nowe zalecenia. Zasady, do których odwołuje się poniżej, dotyczą nowych zaleceń.

- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń
- Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 upewnij się, że domyślne ustawienie zasad ASC "Ocena luk w zabezpieczeniach monitora" nie jest wyłączone

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić wykrywanie i korygowanie luk w zabezpieczeniach.

- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2,11 upewnij się, że domyślne ustawienie zasad ASC "Monitoruj szyfrowanie obiektu BLOB Storage" nie jest wyłączone

Szyfrowanie usługi Azure Storage jest włączone dla wszystkich nowych oraz istniejących kont magazynu i nie można go wyłączyć. (Jest to domyślna funkcja platformy Azure; nie istnieje przypisanie zasad).

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 upewnij się, że domyślne ustawienie zasad ASC "Monitoruj dostęp do sieci JIT" nie jest wyłączone

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która ułatwia kontrolowanie dostępu do maszyn wirtualnych.

- Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2,13 upewnij się, że domyślne ustawienie zasad ASC "Monitoruj adaptacyjne aplikacji listy dozwolonych" nie jest "wyłączone"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że na maszynach wirtualnych jest włączona funkcja adaptacyjnych kontroli aplikacji.

- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2,14 upewnij się, że domyślne ustawienie zasad ASC "Monitoruj inspekcję SQL" nie jest "wyłączone"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że Inspekcja programu SQL Server jest włączona.

- Inspekcja w programie SQL Server powinna być włączona

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 upewnij się, że domyślne ustawienie zasad ASC "Monitoruj szyfrowanie SQL" nie jest wyłączone

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że transparent Data Encryption jest włączona w bazach danych SQL.

- Należy włączyć Transparent Data Encryption baz danych SQL

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2,16 upewnij się, że ustawiono "adresy e-mail dla kontaktu z zabezpieczeniami"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić prawidłowe włączenie powiadomień o zabezpieczeniach

- Należy podać adres e-mail kontaktu z zabezpieczeniami dla Twojej subskrypcji

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2,17 upewnij się, że ustawiono numer telefonu kontaktu zabezpieczeń

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić prawidłowe włączenie powiadomień o zabezpieczeniach

- Należy podać numer telefonu kontaktu z zabezpieczeniami dla Twojej subskrypcji

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2,18 upewnij się, że dla alertów o wysokiej ważności jest ustawiona wartość "on".

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić prawidłowe włączenie powiadomień o zabezpieczeniach

- Należy włączyć powiadomienia e-mail o alertach o wysokiej ważności

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2,19 upewnij się, że jako "Wyślij wiadomość e-mail do właścicieli subskrypcji" ustawiono wartość "on"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić prawidłowe włączenie powiadomień o zabezpieczeniach

- Powiadomienie e-mail dla właściciela subskrypcji w celu uzyskania alertów o wysokiej ważności powinno być włączone

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 Upewnij się, że "wymagany bezpieczny transfer" jest ustawiony na wartość "Enabled"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która ułatwia monitorowanie kont magazynu, które zezwalają na niezabezpieczone połączenia.

- Należy włączyć bezpieczny transfer na konta magazynu

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 upewnij się, że domyślna reguła dostępu do sieci dla kont magazynu jest ustawiona na Odmów

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która ułatwia monitorowanie kont magazynu, które zezwalają na nieograniczony dostęp.

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3,8 upewnij się, że w obszarze "zaufane usługi firmy Microsoft" włączono dostęp do konta magazynu

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która ułatwia monitorowanie kont magazynu, które nie zezwalają na dostęp z zaufanych usług firmy Microsoft.

- Konta magazynu powinny zezwalać na dostęp z zaufanych usług firmy Microsoft

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 Upewnij się, że wartość "inspekcja" jest ustawiona na wartość "on"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że Inspekcja programu SQL Server jest włączona. 

- Inspekcja w programie SQL Server powinna być włączona

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 Upewnij się, że element "AuditActionGroups" w zasadach "Audits" dla programu SQL Server jest skonfigurowany prawidłowo

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że Inspekcja programu SQL Server jest prawidłowo skonfigurowana.

- Ustawienia inspekcji SQL powinny mieć skonfigurowane grupy akcji do przechwytywania działań krytycznych

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 Upewnij się, że przechowywanie "inspekcja" jest większe niż 90 dni

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że dzienniki programu SQL Server są przechowywane przez co najmniej 90 dni.

- Serwery SQL powinny być skonfigurowane z okresem przechowywania inspekcji większym niż 90 dni.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 Upewnij się, że w programie SQL Server ustawiono wartość "on" (Zaawansowane zabezpieczenia danych).

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że zaawansowane zabezpieczenia danych są włączone na serwerach wystąpień zarządzanych SQL Database i SQL.

- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 Upewnij się, że ustawienie "typy wykrywania zagrożeń" ma wartość "All"

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które ułatwiają zapewnienie, że Zaawansowana ochrona przed zagrożeniami została prawidłowo skonfigurowana na serwerach wystąpień zarządzanych SQL Database i SQL.

- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server
- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "All" w zaawansowanych ustawieniach zabezpieczeń danych wystąpienia zarządzanego SQL

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 Upewnij się, że ustawiono wartość "Wyślij alerty do"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że zaawansowane powiadomienia o zabezpieczeniach danych są prawidłowo włączone.

- Zaawansowane ustawienia zabezpieczeń danych dla programu SQL Server powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń
- Zaawansowane ustawienia zabezpieczeń danych dla wystąpienia zarządzanego SQL powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 upewnij się, że "usługa poczty E-mail i współadministratory" jest włączona

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że zaawansowane powiadomienia o zabezpieczeniach danych są prawidłowo włączone.

- Powiadomienia e-mail dla administratorów i właścicieli subskrypcji powinny być włączone w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server
- Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w ustawieniach zaawansowanych danych wystąpienia zarządzanego SQL

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 upewnij się, że skonfigurowano administratora Azure Active Directory

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że Azure Active Directory administrator jest zainicjowany dla serwerów SQL.

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 upewnij się, że "szyfrowanie danych" jest ustawione na wartość "on" na SQL Database

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że transparent Data Encryption jest włączona w bazach danych SQL.

- Należy włączyć Transparent Data Encryption baz danych SQL

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 upewnij się, że funkcja ochrony TDE programu SQL Server jest zaszyfrowana z BYOK (Użyj własnego klucza)

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które ułatwiają zapewnienie, że funkcja ochrony zaszyfrowanych danych jest zaszyfrowana dla SQL Database i serwerów wystąpienia zarządzanego SQL, które są szyfrowane przy użyciu własnego klucza.

- Funkcja ochrony programu SQL Server TDE powinna być szyfrowana przy użyciu własnego klucza
- Funkcja ochrony TDE wystąpienia zarządzanego SQL powinna być szyfrowana przy użyciu własnego klucza

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4,11 upewnij się, że ustawienie "Wymuszaj połączenie SSL" ma wartość "ENABLED" dla serwera bazy danych MySQL

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że serwery baz danych MySQL wymuszają połączenia TLS/SSL.

- Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4,12 upewnij się, że parametr serwera "log_checkpoints" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że serwery baz danych PostgreSQL są dziennikami punktów kontrolnych.

- Punkty kontrolne dziennika powinny być włączone dla serwerów baz danych PostgreSQL

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4,13 upewnij się, że ustawienie "Wymuszaj połączenie SSL" ma wartość "ENABLED" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że serwery bazy danych PostgreSQL wymuszają połączenia TLS/SSL.

- Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4,14 upewnij się, że parametr serwera "log_connections" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że serwery bazy danych PostgreSQL będą rejestrować połączenia.

- Należy włączyć połączenia dzienników dla serwerów baz danych PostgreSQL

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4,15 upewnij się, że parametr serwera "log_disconnections" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że serwery bazy danych PostgreSQL będą rejestrować odłączenia.

- Odłączenia powinny być rejestrowane dla serwerów baz danych PostgreSQL.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4,16 upewnij się, że parametr serwera "log_duration" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że serwery bazy danych PostgreSQL będą rejestrować czas trwania ukończonych instrukcji.

- Czas trwania dziennika powinien być włączony dla serwerów baz danych PostgreSQL

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4,17 upewnij się, że parametr serwera "connection_throttling" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga w ograniczeniu ataków bezproblemowych na serwerach bazy danych PostgreSQL.

- Dla serwerów baz danych PostgreSQL powinna być włączona funkcja ograniczania połączeń

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4,19 upewnij się, że skonfigurowano administratora Azure Active Directory

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że Azure Active Directory administrator jest zainicjowany dla serwerów SQL. Porównawcze fundacje Microsoft Azure WNP obejmuje to zalecenie; jest to jednak duplikat [zalecenia 4,8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Upewnij się, że profil dziennika istnieje

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że istnieje profil dziennika dla wszystkich subskrypcji platformy Azure. 

- Subskrypcje platformy Azure powinny mieć profil dziennika aktywności

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Upewnij się, że czas przechowywania dziennika aktywności wynosi 365 dni lub więcej

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że dzienniki aktywności są przechowywane przez co najmniej jeden rok.

- Dziennik aktywności powinien być zachowywany przez co najmniej jeden rok.

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Upewnij się, że profil inspekcji przechwytuje wszystkie działania

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że profil dziennika jest prawidłowo skonfigurowany.

- Profil dziennika Azure Monitor powinien zbierać dzienniki dla kategorii "Write", "Delete" i "Action"

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Upewnij się, że profil dziennika przechwytuje dzienniki aktywności dla wszystkich regionów łącznie z globalnym

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że profil dziennika jest prawidłowo skonfigurowany.

- Azure Monitor powinna zbierać dzienniki aktywności ze wszystkich regionów

## <a name="516-ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>5.1.6 Upewnij się, że konto magazynu zawierające kontener z dziennikami aktywności jest szyfrowane za pomocą BYOK (Użyj własnego klucza)

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że konta magazynu zawierające dzienniki aktywności są szyfrowane za pomocą BYOK.

- Konto magazynu zawierające kontener z dziennikami aktywności musi być zaszyfrowane za pomocą BYOK

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 upewnij się, że rejestrowanie dla magazynu kluczy platformy Azure ma włączoną funkcję "Enabled"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że dzienniki diagnostyczne są włączone dla magazynów kluczy.

- Dzienniki diagnostyczne w Key Vault powinny być włączone

## <a name="521-ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>5.2.1 Upewnij się, że alert dziennika aktywności istnieje dla tworzenia przypisania zasad

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że istnieją określone alerty dziennika aktywności.

- Dla określonych operacji zasad powinien istnieć alert dziennika aktywności

## <a name="522-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>5.2.2. Upewnij się, że alert dziennika aktywności istnieje dla tworzenia lub aktualizowania sieciowej grupy zabezpieczeń

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że istnieją określone alerty dziennika aktywności.

- Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych

## <a name="523-ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>5.2.3 Upewnij się, że alert dziennika aktywności istnieje dla usunięcia sieciowej grupy zabezpieczeń

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że istnieją określone alerty dziennika aktywności.

- Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych

## <a name="524-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>ppkt 5.2.4 Upewnij się, że alert dziennika aktywności istnieje dla reguły tworzenia lub aktualizowania sieciowej grupy zabezpieczeń

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że istnieją określone alerty dziennika aktywności.

- Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych

## <a name="525-ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>5.2.5 Upewnij się, że alert dziennika aktywności istnieje dla reguły usuwania sieciowej grupy zabezpieczeń

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że istnieją określone alerty dziennika aktywności.

- Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych

## <a name="526-ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>5.2.6 Upewnij się, że alert dziennika aktywności istnieje dla rozwiązania do tworzenia lub aktualizacji zabezpieczeń

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że istnieją określone alerty dziennika aktywności.

- Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń

## <a name="527-ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>5.2.7 upewnij się, że dla rozwiązania do usuwania zabezpieczeń istnieje alert dziennika aktywności

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że istnieją określone alerty dziennika aktywności.

- Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń

## <a name="528-ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>5.2.8 upewnij się, że alert dziennika aktywności istnieje dla reguły zapory tworzenia lub aktualizowania lub usuwania SQL Server

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że istnieją określone alerty dziennika aktywności.

- Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych

## <a name="529-ensure-that-activity-log-alert-exists-for-update-security-policy"></a>5.2.9 upewnij się, że alert dziennika aktywności istnieje dla zasad zabezpieczeń aktualizacji

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że istnieją określone alerty dziennika aktywności.

- Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń

## <a name="61-ensure-that-rdp-access-is-restricted-from-the-internet"></a>6,1 Upewnij się, że dostęp do protokołu RDP jest ograniczony z Internetu

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że dostęp do protokołu RDP jest ograniczony.

- Dostęp RDP z Internetu powinien być zablokowany

## <a name="62-ensure-that-ssh-access-is-restricted-from-the-internet"></a>6,2 Upewnij się, że dostęp SSH jest ograniczony z Internetu

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że dostęp SSH jest ograniczony.

- Dostęp SSH z Internetu powinien być zablokowany

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6,5 Upewnij się, że Network Watcher jest "Enabled"

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że Network Watcher jest włączona dla wszystkich regionów, w których wdrożono zasoby. Te zasady wymagają tablicy parametrów, która określa wszystkie odpowiednie regiony. Wartością domyślną w tej definicji inicjatywy zasad jest "Wschód".

- Network Watcher powinna być włączona

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 Upewnij się, że "dysk systemu operacyjnego" jest szyfrowany

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że szyfrowanie dysków jest włączone na maszynach wirtualnych.

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 Upewnij się, że "dyski danych" są zaszyfrowane

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że szyfrowanie dysków jest włączone na maszynach wirtualnych.

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7,3 Upewnij się, że "niedołączone dyski" są zaszyfrowane

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić zaszyfrowanie niedołączonych dysków.

- Niedołączone dyski powinny być szyfrowane

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7,4 Upewnij się, że zainstalowano tylko zatwierdzone rozszerzenia

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga upewnić się, że są zainstalowane tylko zatwierdzone rozszerzenia maszyn wirtualnych. Te zasady wymagają tablicy parametrów, która określa wszystkie zatwierdzone rozszerzenia maszyny wirtualnej. Ta definicja inicjatywy zasad zawiera sugerowane wartości domyślne, które klienci powinni zweryfikować. 

- Należy zainstalować tylko zatwierdzone rozszerzenia maszyn wirtualnych

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 Upewnij się, że są stosowane najnowsze poprawki systemu operacyjnego dla wszystkich Virtual Machines

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że aktualizacje systemu są zainstalowane na maszynach wirtualnych.

- Należy zainstalować aktualizacje systemu na maszynach

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 Upewnij się, że program Endpoint Protection dla wszystkich Virtual Machines jest zainstalowany

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że program Endpoint Protection jest włączony na maszynach wirtualnych.

- Monitoruj brakujące Endpoint Protection w Azure Security Center

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8,4 Upewnij się, że magazyn kluczy jest możliwy do odzyskania

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić możliwość odzyskania obiektów magazynu kluczy w przypadku przypadkowego usunięcia.

- Key Vault obiektów powinno być możliwe do odzyskania

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 włączenie kontroli dostępu opartej na rolach (RBAC) w ramach usług Azure Kubernetes Services

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że kontrola dostępu oparta na rolach służy do zarządzania uprawnieniami zarządzanymi w klastrach usługi Kubernetes

- Access Control oparte na rolach (RBAC) powinny być używane w usługach Kubernetes Services

## <a name="91-ensure-app-service-authentication-is-set-on-azure-app-service"></a>9,1 Upewnij się, App Service uwierzytelnianie jest ustawione na Azure App Service

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga w zapewnieniu uwierzytelniania żądań App Service aplikacji.

- Należy włączyć uwierzytelnianie w aplikacji interfejsu API
- Należy włączyć uwierzytelnianie w aplikacji funkcji
- Należy włączyć uwierzytelnianie w aplikacji sieci Web

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 Upewnij się, że aplikacja internetowa przekierowuje cały ruch HTTP do protokołu HTTPS w Azure App Service

Ten plan przypisuje definicję [Azure Policy](../../../policy/overview.md) , która pomaga zapewnić, że aplikacje sieci Web są dostępne tylko za pośrednictwem bezpiecznych połączeń.

- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9,3 Upewnij się, że aplikacja sieci Web używa najnowszej wersji szyfrowania TLS

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które pomagają zapewnić, że aplikacje sieci Web używają najnowszej wersji protokołu TLS.

- Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API
- Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji
- Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9,4 Upewnij się, że aplikacja sieci Web ma "certyfikaty klienta (przychodzące certyfikaty klienta") "on"

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które ułatwiają zapewnienie, że tylko klienci z prawidłowymi certyfikatami będą mogli uzyskać dostęp do aplikacji sieci Web.

- Upewnij się, że aplikacja interfejsu API ma wartość "certyfikaty klienta (przychodzące certyfikaty klienta") "on"
- Upewnij się, że aplikacja funkcji ma wartość "certyfikaty klienta (przychodzące certyfikaty klienta") "on"
- Upewnij się, że aplikacja sieci WEB ma "certyfikaty klienta (przychodzące certyfikaty klienta") "on"

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9,5 Upewnij się, że rejestracja w Azure Active Directory jest włączona na App Service

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które ułatwiają zapewnienie, że aplikacje sieci Web używają tożsamości zarządzanej.

- Upewnij się, że w aplikacji interfejsu API jest włączony rejestr z Azure Active Directory
- Upewnij się, że Azure Active Directory jest włączona na aplikacja funkcji
- Upewnij się, że w aplikacji sieci WEB jest włączony rejestr z Azure Active Directory

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9,6 Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które pomagają zagwarantować, że aplikacje sieci Web używają najnowszej wersji programu .NET Framework.

- Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API
- Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacja funkcji
- Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9,7 upewnij się, że "wersja PHP" jest najnowsza, jeśli zostanie użyta do uruchomienia aplikacji sieci Web

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które pomagają zagwarantować, że aplikacje sieci Web używają najnowszej wersji środowiska php.

- Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API
- Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji funkcji
- Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji sieci WEB

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9,8 upewnij się, że "wersja języka Python" jest najnowsza, jeśli zostanie użyta do uruchomienia aplikacji sieci Web

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które pomagają zagwarantować, że aplikacje sieci Web używają najnowszej wersji języka Python.

- Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API
- Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji funkcji
- Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9,9 upewnij się, że "wersja Java" jest najnowsza, jeśli zostanie użyta do uruchomienia aplikacji sieci Web

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które pomagają zagwarantować, że aplikacje sieci Web używają najnowszej wersji środowiska Java.

- Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API
- Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji funkcji
- Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji sieci Web

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9,10 upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji sieci Web

Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) , które pomagają zagwarantować, że aplikacje sieci Web używają najnowszej wersji protokołu HTTP.

- Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji interfejsu API
- Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji funkcji
- Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji sieci Web

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontroli w ramach planu porównawczego usługi CIS Microsoft Azure Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat planu, lub odwiedź Azure Policy w Azure Portal, aby przypisać inicjatywę:

> [!div class="nextstepaction"]
> [Plan porównawczy usług CIS Microsoft Azure — omówienie](./index.md) 
>  [Plan porównawczy usług CIS Microsoft Azure — kroki wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).