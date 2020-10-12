---
title: Przegląd zabezpieczeń
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Dowiedz się więcej o zabezpieczeniach w Azure SQL Database i wystąpieniu zarządzanym Azure SQL, w tym o tym, jak różni się od SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 09/21/2020
ms.openlocfilehash: f0ebd511d0b706d1d2066ea87f45c89ae536da69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321428"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Omówienie funkcji zabezpieczeń wystąpienia zarządzanego w Azure SQL Database i SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym artykule przedstawiono podstawowe informacje dotyczące zabezpieczania warstwy danych aplikacji przy użyciu [Azure SQL Database](sql-database-paas-overview.md) i [wystąpienia zarządzanego Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md). Opisana poniżej strategia zabezpieczeń ma zastosowanie do opartego na warstwach podejścia do kompleksowej ochrony, jak pokazano na ilustracji poniżej, i przemieszcza się z zewnątrz w:

![Diagram warstwowych zabezpieczeń. Dane klienta są encased w warstwach zabezpieczeń sieci, zarządzania dostępem oraz ochrony przed zagrożeniami i informacjami.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Bezpieczeństwo sieci

Microsoft Azure SQL Database i wystąpienie zarządzane SQL zapewniają usługę relacyjnej bazy danych dla aplikacji w chmurze i dla przedsiębiorstw. Aby chronić dane klientów, zapory uniemożliwiają dostęp do sieci do serwera do momentu, gdy dostęp zostanie jawnie udzielony na podstawie adresu IP lub źródła ruchu w sieci wirtualnej platformy Azure.

### <a name="ip-firewall-rules"></a>Reguły zapory adresów IP

Reguły zapory adresów IP umożliwiają dostęp do baz danych na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, zobacz [Omówienie reguł zapory Azure SQL Database i usługi Azure Synapse Analytics](firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Reguły zapory sieci wirtualnej

[Punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md) zwiększają łączność sieci wirtualnej przez sieć szkieletową platformy Azure i umożliwiają Azure SQL Database identyfikowania podsieci sieci wirtualnej, z której pochodzi ruch. Aby zezwolić na dostęp do Azure SQL Database, użyj [tagów usługi](../../virtual-network/security-overview.md) SQL, aby zezwolić na ruch wychodzący za pomocą sieciowych grup zabezpieczeń.

[Reguły sieci wirtualnej](vnet-service-endpoint-rule-overview.md) umożliwiają Azure SQL Database tylko akceptowanie komunikacji wysyłanej z wybranych podsieci w sieci wirtualnej.

> [!NOTE]
> Kontrolowanie dostępu za pomocą reguł *zapory nie ma zastosowania do* **wystąpienia zarządzanego SQL**. Aby uzyskać więcej informacji o wymaganej konfiguracji sieci, zobacz [nawiązywanie połączenia z wystąpieniem zarządzanym](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>Zarządzanie dostępem

> [!IMPORTANT]
> Zarządzanie bazami danych i serwerami na platformie Azure jest kontrolowane przez przypisania roli konta użytkownika portalu. Aby uzyskać więcej informacji na temat tego artykułu, zobacz [Kontrola dostępu oparta na rolach w Azure Portal](../../role-based-access-control/overview.md).

### <a name="authentication"></a>Uwierzytelnianie

Uwierzytelnianie to proces potwierdzania tożsamości użytkownika. Azure SQL Database i wystąpienie zarządzane SQL obsługują dwa typy uwierzytelniania:

- **Uwierzytelnianie SQL**:

    Uwierzytelnianie SQL odnosi się do uwierzytelniania użytkownika podczas nawiązywania połączenia z usługą Azure SQL Database lub wystąpieniem zarządzanym usługi Azure SQL przy użyciu nazwy użytkownika i hasła. Podczas tworzenia serwera należy określić identyfikator logowania **administratora serwera** z nazwą użytkownika i hasłem. Korzystając z tych poświadczeń, **administrator serwera** może uwierzytelniać się w dowolnej bazie danych na tym serwerze lub wystąpieniu jako właściciel bazy danych. Następnie administrator serwera może utworzyć dodatkowe identyfikatory logowania i użytkowników SQL, które umożliwią użytkownikom łączenie się przy użyciu nazwy użytkownika i hasła.

- **Uwierzytelnianie Azure Active Directory**:

    Uwierzytelnianie Azure Active Directory jest mechanizmem łączenia się z [Azure SQL Database](sql-database-paas-overview.md), [wystąpienia zarządzanego usługi Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) i [analizy Synapse azure](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości w Azure Active Directory (Azure AD). Uwierzytelnianie za pomocą usługi Azure AD pozwala administratorom centralnie zarządzać tożsamościami i uprawnieniami użytkowników baz danych wraz z innymi usługami platformy Azure w jednej centralnej lokalizacji. Obejmuje to minimalizowanie magazynu haseł i umożliwia scentralizowane zasady rotacji haseł.

     Administrator serwera o nazwie **administrator Active Directory** musi zostać utworzony w celu korzystania z uwierzytelniania usługi Azure AD z SQL Database. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z SQL Database przy użyciu uwierzytelniania Azure Active Directory](authentication-aad-overview.md). Uwierzytelnianie usługi Azure AD obsługuje konta zarządzane i federacyjne. Konta federacyjne obsługują użytkowników i grupy systemu Windows dla domeny klienta federacyjnego z usługą Azure AD.

    Dostępne są dodatkowe opcje uwierzytelniania usługi Azure AD [Active Directory uniwersalnego uwierzytelniania dla połączeń SQL Server Management Studio, w](authentication-mfa-ssms-overview.md) tym [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) i [dostępu warunkowego](conditional-access-configure.md).

> [!IMPORTANT]
> Zarządzanie bazami danych i serwerami na platformie Azure jest kontrolowane przez przypisania roli konta użytkownika portalu. Aby uzyskać więcej informacji na temat tego artykułu, zobacz [Kontrola dostępu oparta na rolach w Azure Portal](../../role-based-access-control/overview.md). Kontrolowanie dostępu za pomocą reguł *zapory nie ma zastosowania do* **wystąpienia zarządzanego SQL**. Aby uzyskać więcej informacji na temat konfiguracji sieci, należy zapoznać się z poniższym artykułem dotyczącym [łączenia się z wystąpieniem zarządzanym](../managed-instance/connect-application-instance.md) .

## <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do uprawnień przypisanych do użytkownika w bazie danych w Azure SQL Database lub wystąpieniu zarządzanym usługi Azure SQL i określa, co użytkownik może zrobić. Uprawnienia są kontrolowane przez dodawanie kont użytkowników do [ról bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles) i przypisywanie uprawnień na poziomie bazy danych do tych ról lub udzielanie użytkownikowi określonych [uprawnień na poziomie obiektów](/sql/relational-databases/security/permissions-database-engine). Aby uzyskać więcej informacji, zobacz [logowania i użytkownicy](logins-create-manage.md)

Najlepszym rozwiązaniem jest utworzenie ról niestandardowych w razie konieczności. Dodaj użytkowników do roli o najniższych uprawnieniach wymaganych do wykonania funkcji zadań. Nie należy przypisywać uprawnień bezpośrednio do użytkowników. Konto administratora serwera jest członkiem wbudowanej roli db_owner, która ma rozległe uprawnienia i powinno być udzielane tylko kilku użytkownikom z zadaniami administracyjnymi. W przypadku aplikacji użyj funkcji [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) , aby określić kontekst wykonywania wywołanego modułu, lub Użyj [ról aplikacji](/sql/relational-databases/security/authentication-access/application-roles) z ograniczonymi uprawnieniami. Dzięki temu aplikacja, która łączy się z bazą danych, ma najniższe uprawnienia wymagane przez aplikację. Poniższe najlepsze rozwiązania wspierają także Rozdzielenie obowiązków.

### <a name="row-level-security"></a>Zabezpieczenia na poziomie wiersza

Row-Level zabezpieczenia umożliwiają klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (na przykład przynależności do grupy lub kontekstu wykonania). Zabezpieczenia Row-Level mogą być również używane do implementowania niestandardowych koncepcji zabezpieczeń opartych na etykietach. Aby uzyskać więcej informacji, zobacz [zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security).

![Diagram przedstawiający, że Row-Level zabezpieczenia — poszczególne wiersze bazy danych SQL są dostępne dla użytkowników za pośrednictwem aplikacji klienckiej.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Ochrona przed zagrożeniami

SQL Database i wystąpienie zarządzane SQL zabezpieczają dane klientów, zapewniając możliwości inspekcji i wykrywania zagrożeń.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Inspekcja SQL w Azure Monitor dziennikach i Event Hubs

SQL Database i inspekcja wystąpienia zarządzanego SQL śledzi działania bazy danych i pomaga zachować zgodność ze standardami zabezpieczeń, rejestrując zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage należącym do klienta. Inspekcja umożliwia użytkownikom monitorowanie trwających działań bazy danych, a także analizowanie i badanie działań historycznych w celu identyfikowania potencjalnych zagrożeń lub podejrzanych naruszeń oraz naruszenia zabezpieczeń. Aby uzyskać więcej informacji, zobacz Rozpoczynanie pracy z [inspekcją SQL Database](../../azure-sql/database/auditing-overview.md).  

### <a name="advanced-threat-protection"></a>Zaawansowana ochrona przed zagrożeniami

Zaawansowana ochrona przed zagrożeniami analizuje dzienniki, aby wykrywać nietypowe zachowanie i potencjalnie szkodliwe próby dostępu do baz danych lub korzystania z nich. Alerty są tworzone dla podejrzanych działań, takich jak iniekcja kodu SQL, potencjalne filtrowanie danych i ataki typu "wymuszanie" i "anomalie" w wzorcach dostępu do przechwytywania eskalacji uprawnień i użycia poświadczeń naruszeń. Alerty są wyświetlane w  [Azure Security Center](https://azure.microsoft.com/services/security-center/), w którym znajdują się szczegółowe informacje o podejrzanych działaniach, a także zalecenia dotyczące dalszych badań oraz działań związanych z ograniczeniami zagrożenia. Zaawansowaną ochronę przed zagrożeniami można włączyć dla każdego serwera w celu uzyskania dodatkowej opłaty. Aby uzyskać więcej informacji, zobacz [wprowadzenie do SQL Database zaawansowanej ochrony przed zagrożeniami](threat-detection-configure.md).

![Diagram przedstawiający monitorowanie wykrywania zagrożeń SQL dostęp do bazy danych SQL dla aplikacji internetowej z zewnętrznego ataku i złośliwego oprogramowania.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Ochrona informacji i szyfrowanie

### <a name="transport-layer-security-encryption-in-transit"></a>Transport Layer Security (szyfrowanie w trakcie przesyłania)

SQL Database i wystąpienie zarządzane SQL zabezpieczają dane klienta przez szyfrowanie danych w ruchu przy użyciu [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

SQL Database i wystąpienie zarządzane SQL wymusza szyfrowanie (SSL/TLS) przez cały czas dla wszystkich połączeń. Gwarantuje to, że wszystkie dane są szyfrowane "podczas przesyłania" między klientem i serwerem niezależnie od ustawienia **szyfrowania** lub **TrustServerCertificate** w parametrach połączenia.

Najlepszym rozwiązaniem jest zalecanie, aby w parametrach połączenia używanych przez aplikację określić połączenie szyfrowane i _**nie**_ ufać certyfikatowi serwera. Oznacza to, że aplikacja weryfikuje certyfikat serwera i w związku z tym uniemożliwia aplikacji atak na ataki typu średniego.

Na przykład podczas korzystania z sterownika ADO.NET jest to realizowane za pośrednictwem  **szyfrowania = true** i **TrustServerCertificate = false**. W przypadku uzyskania parametrów połączenia z Azure Portal będzie on miał poprawne ustawienia.

> [!IMPORTANT]
> Należy pamiętać, że niektóre sterowniki inne niż firmy Microsoft mogą nie używać protokołu TLS domyślnie lub korzystają ze starszej wersji protokołu TLS (<1,2), aby móc działać. W takim przypadku serwer nadal umożliwia nawiązanie połączenia z bazą danych. Zaleca się jednak, aby oszacować zagrożenia bezpieczeństwa, które umożliwiają łączenie takich sterowników i aplikacji z SQL Database, szczególnie w przypadku przechowywania poufnych danych.
>
> Aby uzyskać więcej informacji na temat protokołu TLS i łączności, zobacz [zagadnienia dotyczące protokołu TLS](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity) .

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (szyfrowanie w spoczynku)

[Transparent Data Encryption (TDE) dla Azure SQL Database i wystąpienia zarządzane SQL](transparent-data-encryption-tde-overview.md) dodaje warstwę zabezpieczeń, aby chronić dane przechowywane przed dostępem nieautoryzowanym lub w trybie offline do plików RAW lub kopii zapasowych. Typowe scenariusze obejmują kradzież centrum danych lub niezabezpieczone usuwanie sprzętu lub nośników, takich jak stacje dysków i taśmy kopii zapasowych.TDE szyfruje całą bazę danych przy użyciu algorytmu szyfrowania AES, który nie wymaga, aby deweloperzy aplikacji wprowadzali jakiekolwiek zmiany do istniejących aplikacji.

Na platformie Azure wszystkie nowo utworzone bazy danych są szyfrowane domyślnie, a klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera.  Obsługa i rotacja certyfikatów są zarządzane przez usługę i nie wymagają wprowadzania danych przez użytkownika. Klienci, którzy wolą przejąć kontrolę nad kluczami szyfrowania, mogą zarządzać kluczami w [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Zarządzanie kluczami za pomocą Azure Key Vault

[Bring Your Own Key](transparent-data-encryption-byok-overview.md) (BYOK) obsługa [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) umożliwia klientom Przejmowanie na własność zarządzania kluczami i rotacji przy użyciu [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md)systemu Azure — zewnętrzny system zarządzania kluczami. Jeśli dostęp do bazy danych jest odwołany, nie można odszyfrować bazy danych i odczytać jej do pamięci. Azure Key Vault zapewnia centralną platformę do zarządzania kluczami, wykorzystuje ściśle monitorowane sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń) i umożliwia rozdzielenie obowiązków między zarządzaniem kluczami i danymi, aby zapewnić zgodność z wymaganiami dotyczącymi zgodności.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (szyfrowanie w użyciu)

![Diagram przedstawiający podstawowe informacje o funkcji Always Encrypted. Baza danych SQL z blokadą jest dostępna tylko dla aplikacji zawierającej klucz.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) to funkcja przeznaczona do ochrony poufnych danych przechowywanych w określonych kolumnach bazy danych w celu uzyskania dostępu (na przykład numerów kart kredytowych, krajowych numerów identyfikacyjnych lub danych, _które muszą być znane_ ). Dotyczy to administratorów bazy danych lub innych użytkowników uprzywilejowanych, którzy mają uprawnienia dostępu do bazy danych w celu wykonywania zadań związanych z zarządzaniem, ale nie muszą uzyskiwać dostępu do określonych danych w zaszyfrowanych kolumnach. Dane są zawsze szyfrowane, co oznacza, że zaszyfrowane dane są odszyfrowywane tylko do przetwarzania przez aplikacje klienckie z dostępem do klucza szyfrowania. Klucz szyfrowania nigdy nie jest ujawniany do SQL Database lub wystąpienia zarządzanego SQL i może być przechowywany w [magazynie certyfikatów systemu Windows](always-encrypted-certificate-store-configure.md) lub w [Azure Key Vault](always-encrypted-azure-key-vault-configure.md).

### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych

![Diagram przedstawiający Dynamiczne maskowanie danych. Aplikacja biznesowa wysyła dane do bazy danych SQL, która maskuje dane przed wysłaniem ich z powrotem do aplikacji biznesowej.](./media/security-overview/azure-database-ddm.png)

Dynamiczne maskowanie danych ogranicza narażenie na dane poufne przez zamaskowanie ich dla użytkowników bez uprawnień. Dynamiczne maskowanie danych automatycznie odnajduje potencjalnie wrażliwe dane w Azure SQL Database i wystąpieniu zarządzanym SQL, a także udostępnia zalecenia umożliwiające podjęcie odpowiednich działań w celu zamaskowania tych pól przy minimalnym wpływie na warstwę aplikacji. Jego działanie polega na zaciemnianiu poufnych danych w wyznaczonych polach bazy danych w zestawie wyników zapytania, przy czym dane w bazie danych pozostają bez zmian. Aby uzyskać więcej informacji, zobacz [wprowadzenie do SQL Database i dynamiczne maskowanie danych wystąpienia zarządzanego SQL](dynamic-data-masking-overview.md).

## <a name="security-management"></a>Zarządzanie zabezpieczeniami

### <a name="vulnerability-assessment"></a>Ocena luk w zabezpieczeniach

[Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to prosta konfiguracja usługi, która umożliwia odnajdywanie, śledzenie i rozwiązywanie problemów dotyczących potencjalnych luk w zabezpieczeniach bazy danych w celu aktywnego ulepszania ogólnych zabezpieczeń bazy danych. Ocena luk w zabezpieczeniach jest częścią usługi Azure Defender dla oferty SQL, która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń SQL. Dostęp do oceny luk w zabezpieczeniach można uzyskać i zarządzać nim za pośrednictwem centralnej usługi Azure Defender dla programu SQL Portal.

### <a name="data-discovery-and-classification"></a>Odnajdowanie i klasyfikacja danych

Funkcja odnajdywania i klasyfikowania danych (obecnie dostępna w wersji zapoznawczej) oferuje zaawansowane funkcje wbudowane w Azure SQL Database i wystąpienie zarządzane SQL w celu odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Odkrywanie i klasyfikowanie najbardziej poufnych danych (Biznes/Finanse, opieka, dane osobowe itp.) może odgrywać rolę przestawną w usłudze Information Protection schemacie. Może to stanowić infrastrukturę dla następujących działań:

- Różne scenariusze zabezpieczeń, takie jak monitorowanie (inspekcja) i alerty dotyczące nietypowego dostępu do poufnych danych.
- Kontrolowanie dostępu do i ograniczanie bezpieczeństwa baz danych zawierających wysoce poufne dane.
- Pomoc przy spełnianiu standardów dotyczących prywatności danych i wymagań dotyczących zgodności z przepisami.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do odnajdywania i klasyfikowania danych](data-discovery-and-classification-overview.md).

### <a name="compliance"></a>Zgodność

Oprócz powyższych funkcji i funkcji, które mogą pomóc aplikacji spełnić różne wymagania dotyczące zabezpieczeń, Azure SQL Database również uczestniczą w regularnych inspekcjach i mają certyfikat z wieloma standardami zgodności. Aby uzyskać więcej informacji, zobacz [Centrum zaufania Microsoft Azure](https://www.microsoft.com/trust-center/compliance/compliance-overview) , w którym można znaleźć najbardziej aktualną listę SQL Database certyfikatów zgodności.

## <a name="next-steps"></a>Następne kroki

- Omówienie korzystania z nazw logowania, kont użytkowników, ról bazy danych i uprawnień w SQL Database i wystąpieniu zarządzanym SQL można znaleźć w temacie [Zarządzanie nazwami logowania i kontami użytkowników](logins-create-manage.md).
- Aby zapoznać się z omówieniem inspekcji bazy danych, zobacz [Inspekcja](../../azure-sql/database/auditing-overview.md).
- Aby zapoznać się z omówieniem wykrywania zagrożeń, zobacz [wykrywanie zagrożeń](threat-detection-configure.md).
