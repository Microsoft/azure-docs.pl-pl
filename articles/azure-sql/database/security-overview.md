---
title: Przegląd zabezpieczeń
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Dowiedz się więcej o zabezpieczeniach Azure SQL Database i Azure SQL Managed Instance, w tym o tym, czym różni się ona od SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: 084f9aae16cfbf495f05c90c8244b2b9b71cf624
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812988"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Omówienie możliwości Azure SQL Database i SQL Managed Instance zabezpieczeń
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym artykule przedstawiono podstawowe informacje dotyczące zabezpieczania warstwy danych aplikacji przy użyciu [Azure SQL Database,](sql-database-paas-overview.md) [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)i [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Opisana strategia zabezpieczeń jest zgodna z warstwowym podejściem do ochrony w głębi systemu, jak pokazano na poniższej ilustracji, i przenosi się z zewnątrz w:

![Diagram warstwowej ochrony w głębi systemu. Dane klienta są zamknięte w warstwach zabezpieczeń sieci, zarządzania dostępem oraz ochrony przed zagrożeniami i informacjami.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Bezpieczeństwo sieci

Microsoft Azure SQL Database, SQL Managed Instance i Azure Synapse Analytics usługę relacyjnej bazy danych dla aplikacji w chmurze i dla przedsiębiorstw. Aby ułatwić ochronę danych klienta, zapory uniemożliwiają dostęp sieciowy do serwera, dopóki dostęp nie zostanie jawnie udzielony na podstawie adresu IP lub źródła ruchu sieci wirtualnej platformy Azure.

### <a name="ip-firewall-rules"></a>Reguły zapory adresów IP

Reguły zapory bazujące na adresach IP przyznają dostęp do baz danych na podstawie adresu IP poszczególnych żądań. Aby uzyskać więcej informacji, zobacz Overview of Azure SQL Database and Azure Synapse Analytics firewall rules (Omówienie Azure Synapse Analytics [reguł zapory).](firewall-configure.md)

### <a name="virtual-network-firewall-rules"></a>Reguły zapory sieci wirtualnej

[Punkty końcowe usługi dla](../../virtual-network/virtual-network-service-endpoints-overview.md) sieci wirtualnej rozszerzają łączność sieci wirtualnej za pośrednictwem sieci szkieletowej platformy Azure i umożliwiają Azure SQL Database zidentyfikowanie podsieci sieci wirtualnej, z której pochodzi ruch. Aby zezwolić na ruch Azure SQL Database, użyj tagów [usługi](../../virtual-network/network-security-groups-overview.md) SQL, aby zezwolić na ruch wychodzący przez sieciowe grupy zabezpieczeń.

[Reguły sieci wirtualnej](vnet-service-endpoint-rule-overview.md) Azure SQL Database akceptować tylko komunikację, która jest wysyłana z wybranych podsieci w sieci wirtualnej.

> [!NOTE]
> Kontrolowanie dostępu za pomocą reguł zapory *nie ma* zastosowania do **SQL Managed Instance**. Aby uzyskać więcej informacji na temat wymaganej konfiguracji sieci, zobacz [Nawiązywanie połączenia z wystąpieniem zarządzanym](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>Zarządzanie dostępem

> [!IMPORTANT]
> Zarządzanie bazami danych i serwerami na platformie Azure jest kontrolowane przez przypisania ról konta użytkownika portalu. Aby uzyskać więcej informacji na temat tego artykułu, zobacz [Azure role-based access control (Kontrola](../../role-based-access-control/overview.md)dostępu oparta na rolach na platformie Azure) w Azure Portal .

### <a name="authentication"></a>Authentication

Uwierzytelnianie to proces potwierdzania, że użytkownik jest tym, za kogo się poda. Azure SQL Database i SQL Managed Instance obsługują dwa typy uwierzytelniania:

- **Uwierzytelnianie SQL:**

    Uwierzytelnianie SQL odnosi się do uwierzytelniania użytkownika podczas nawiązywania połączenia z Azure SQL Database lub Azure SQL Managed Instance przy użyciu nazwy użytkownika i hasła. Podczas **tworzenia serwera** należy określić identyfikator logowania administratora serwera z nazwą użytkownika i hasłem. Przy użyciu tych poświadczeń administrator **serwera może uwierzytelnić** się w dowolnej bazie danych na tym serwerze lub w wystąpieniu jako właściciel bazy danych. Następnie administrator serwera może utworzyć dodatkowe identyfikatory logowania SQL i użytkowników, co umożliwi użytkownikom łączenie się przy użyciu nazwy użytkownika i hasła.

- **Azure Active Directory uwierzytelniania:**

    Azure Active Directory to mechanizm łączenia się z usługami [Azure SQL Database,](sql-database-paas-overview.md) [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) [i Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD). Uwierzytelnianie usługi Azure AD umożliwia administratorom centralne zarządzanie tożsamościami i uprawnieniami użytkowników bazy danych wraz z innymi usługami platformy Azure w jednej centralnej lokalizacji. Obejmuje to minimalizację magazynu haseł i umożliwia korzystanie ze scentralizowanych zasad rotacji haseł.

     Aby można było używać uwierzytelniania usługi Azure AD z usługą **azure** ad w usłudze SQL Database, należy utworzyć administratora serwera o nazwie SQL Database. Aby uzyskać więcej informacji, zobacz Connecting to SQL Database By Using Azure Active Directory Authentication (Nawiązywanie połączenia z SQL Database [przy użyciu uwierzytelniania Azure Active Directory sieciowego).](authentication-aad-overview.md) Uwierzytelnianie usługi Azure AD obsługuje zarówno konta zarządzane, jak i federowane. Konta federowane obsługują użytkowników i grupy systemu Windows dla domeny klienta federacji z usługą Azure AD.

    Dodatkowe dostępne opcje uwierzytelniania usługi Azure AD to uniwersalne uwierzytelnianie usługi Active Directory dla połączeń [SQL Server Management Studio,](authentication-mfa-ssms-overview.md) w tym [uwierzytelnianie wieloskładnikowe](../../active-directory/authentication/concept-mfa-howitworks.md) i [dostęp warunkowy.](conditional-access-configure.md)

> [!IMPORTANT]
> Zarządzanie bazami danych i serwerami na platformie Azure jest kontrolowane przez przypisania ról konta użytkownika portalu. Aby uzyskać więcej informacji na temat tego artykułu, zobacz Kontrola dostępu oparta na rolach [na platformie Azure w Azure Portal](../../role-based-access-control/overview.md). Kontrolowanie dostępu za pomocą reguł zapory *nie ma* zastosowania do **SQL Managed Instance**. Aby uzyskać więcej [](../managed-instance/connect-application-instance.md) informacji na temat wymaganej konfiguracji sieci, zobacz następujący artykuł na temat nawiązywania połączenia z wystąpieniem zarządzanym.

## <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do uprawnień przypisanych do użytkownika w bazie danych Azure SQL Database lub Azure SQL Managed Instance i określa, co użytkownik może robić. Uprawnienia są kontrolowane przez dodawanie [](/sql/relational-databases/security/authentication-access/database-level-roles) kont użytkowników do ról bazy danych i przypisywanie uprawnień na poziomie bazy danych do tych ról lub przez przyznanie użytkownikowi pewnych uprawnień [na poziomie obiektu.](/sql/relational-databases/security/permissions-database-engine) Aby uzyskać więcej informacji, zobacz [Logins and users (Identyfikatory logowania i użytkownicy)](logins-create-manage.md)

Najlepszym rozwiązaniem jest utworzenie ról niestandardowych w razie potrzeby. Dodaj użytkowników do roli z najmniejszymi uprawnieniami wymaganymi do pracy. Nie przypisuj uprawnień bezpośrednio do użytkowników. Konto administratora serwera jest członkiem wbudowanej roli db_owner, która ma rozbudowane uprawnienia i powinna być przyznawana tylko kilku użytkownikom z uprawnieniami administracyjnymi. W przypadku aplikacji użyj polecenia [EXECUTE AS,](/sql/t-sql/statements/execute-as-clause-transact-sql) aby określić kontekst wykonywania nazywanego modułu, lub użyj ról [aplikacji](/sql/relational-databases/security/authentication-access/application-roles) z ograniczonymi uprawnieniami. Dzięki temu aplikacja łącząca się z bazą danych ma najmniejsze uprawnienia wymagane przez aplikację. Postępowania zgodnie z tymi najlepszymi rozwiązaniami sprzyja także rozdzieleniu obowiązków.

### <a name="row-level-security"></a>Zabezpieczenia na poziomie wiersza

Row-Level Security umożliwia klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (na przykład członkostwa w grupie lub kontekstu wykonywania). Row-Level Security można również użyć do zaimplementowania niestandardowych koncepcji zabezpieczeń opartych na etykietach. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security).

![Diagram pokazujący, Row-Level zabezpieczenia chronią pojedyncze wiersze bazy danych SQL przed dostępem użytkowników za pośrednictwem aplikacji klienckiej.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Ochrona przed zagrożeniami

SQL Database i SQL Managed Instance danych klientów, zapewniając możliwości inspekcji i wykrywania zagrożeń.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Inspekcja SQL w dziennikach Azure Monitor i Event Hubs

SQL Database i SQL Managed Instance śledzi działania bazy danych i pomaga zachować zgodność ze standardami zabezpieczeń, rejestrując zdarzenia bazy danych w dzienniku inspekcji na koncie magazynu platformy Azure należącym do klienta. Inspekcja umożliwia użytkownikom monitorowanie bieżących działań bazy danych, a także analizowanie i badanie działań historycznych w celu zidentyfikowania potencjalnych zagrożeń lub podejrzanych nadużycii i naruszeń zabezpieczeń. Aby uzyskać więcej informacji, zobacz Wprowadzenie do SQL Database [inspekcji.](../../azure-sql/database/auditing-overview.md)  

### <a name="advanced-threat-protection"></a>Zaawansowana ochrona przed zagrożeniami

Zaawansowana ochrona przed zagrożeniami analizuje dzienniki w celu wykrycia nietypowych zachowań i potencjalnie szkodliwych prób uzyskania dostępu do baz danych lub wykorzystania ich. Alerty są tworzone dla podejrzanych działań, takich jak iniekcja SQL, potencjalna infiltracja danych i ataki siłowe lub anomalie we wzorcach dostępu w celu przechwytowania eskalacji uprawnień i użycia poświadczeń z naruszonym użyciem. Alerty są przeglądane z Azure Security Center  [,](https://azure.microsoft.com/services/security-center/)gdzie znajdują się szczegółowe informacje o podejrzanych działaniach i zalecenia dotyczące dalszych badań wraz z działaniami w celu ograniczenia zagrożenia. Usługę Advanced Threat Protection można włączyć dla 1 serwera za dodatkową opłatą. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z SQL Database Advanced Threat Protection.](threat-detection-configure.md)

![Diagram przedstawiający monitorowanie dostępu do bazy danych SQL do bazy danych SQL dla aplikacji internetowej przez zewnętrznego atakującego i złośliwego niejawnego testera.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Ochrona i szyfrowanie informacji

### <a name="transport-layer-security-encryption-in-transit"></a>Transport Layer Security (szyfrowanie podczas przesyłania)

SQL Database, SQL Managed Instance i Azure Synapse Analytics dane klientów dzięki szyfrowaniu danych w ruchu za [pomocą Transport Layer Security (TLS).](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

SQL Database, SQL Managed Instance i Azure Synapse Analytics wymuszać szyfrowanie (SSL/TLS) przez cały czas dla wszystkich połączeń. Gwarantuje to, że wszystkie dane są szyfrowane "podczas przesyłania"  między klientem a serwerem niezależnie od ustawienia w ciągu połączenia szyfrowania lub certyfikatu **TrustServerCertificate.**

Zaleca się, aby w ciągu połączenia używanym przez aplikację określić __ zaszyfrowane połączenie i nie ufać certyfikatowi serwera. Wymusza to zweryfikowanie certyfikatu serwera przez aplikację i w związku z tym zapobiega narażeniu aplikacji na ataki typu "człowiek" w środkowym typie.

Na przykład w przypadku używania ADO.NET jest to realizowane za pośrednictwem **encrypt = true** i **TrustServerCertificate = False.** Jeśli uzyskasz parametrów połączenia z Azure Portal, będą one mieć poprawne ustawienia.

> [!IMPORTANT]
> Należy pamiętać, że niektóre sterowniki firmy innych niż Microsoft mogą nie używać domyślnie TLS lub polegać na starszej wersji TLS (<1.2) do działania. W takim przypadku serwer nadal umożliwia nawiązywanie połączenia z bazą danych. Zalecamy jednak ocenę zagrożeń bezpieczeństwa związanych z zezwoleniem takim sterownikom i aplikacjom na łączenie się z SQL Database, szczególnie w przypadku przechowywania poufnych danych.
>
> Aby uzyskać więcej informacji na temat TLS i łączności, zobacz [Zagadnienia dotyczące TLS](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (Szyfrowanie danych w spoczynku)

[Transparent Data Encryption (TDE)](transparent-data-encryption-tde-overview.md) dla usług SQL Database, SQL Managed Instance i Azure Synapse Analytics dodaje warstwę zabezpieczeń, która pomaga chronić dane w spoczynku przed nieautoryzowanym lub offline dostępem do nieprzetworzonych plików lub kopii zapasowych. Typowe scenariusze obejmują kradzież centrum danych lub niezabezpieczone usuwanie sprzętu lub nośnika, takiego jak dyski i taśmy kopii zapasowej.Szyfrowanie TDE szyfruje całą bazę danych przy użyciu algorytmu szyfrowania AES, który nie wymaga, aby deweloperzy aplikacji wprowadzali zmiany w istniejących aplikacjach.

Na platformie Azure wszystkie nowo utworzone bazy danych są domyślnie szyfrowane, a klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera.  Konserwacja i rotacja certyfikatów są zarządzane przez usługę i nie wymagają żadnych danych wejściowych od użytkownika. Klienci, którzy wolą przejąć kontrolę nad kluczami szyfrowania, mogą zarządzać kluczami w [Azure Key Vault](../../key-vault/general/security-features.md).

### <a name="key-management-with-azure-key-vault"></a>Zarządzanie kluczami za pomocą Azure Key Vault

[Bring Your Own Key](transparent-data-encryption-byok-overview.md) (BYOK) dla usługi [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) umożliwia klientom przejmuje na własność zarządzanie kluczami i rotację za pomocą usługi [Azure Key Vault —](../../key-vault/general/security-features.md)systemu zarządzania kluczami zewnętrznymi opartym na chmurze platformy Azure. Jeśli dostęp bazy danych do magazynu kluczy zostanie odwołany, nie można odszyfrować bazy danych i odczytać jej do pamięci. Azure Key Vault zapewnia centralną platformę zarządzania kluczami, korzysta ze ściśle monitorowanych sprzętowych modułów zabezpieczeń (HSM) i umożliwia rozdzielenie obowiązków między zarządzaniem kluczami i danymi w celu spełnienia wymagań dotyczących zgodności zabezpieczeń.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (szyfrowanie w użyciu)

![Diagram przedstawiający podstawy Always Encrypted funkcji. Dostęp do bazy danych SQL z blokadą uzyskuje tylko aplikacja zawierająca klucz.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) to funkcja przeznaczona do ochrony poufnych danych przechowywanych w określonych kolumnach bazy danych przed dostępem (na przykład numerów kart kredytowych, krajowych numerów identyfikacyjnych lub danych, które należy _znać)._ Dotyczy to również administratorów baz danych lub innych uprzywilejowanych użytkowników, którzy są autoryzowani do uzyskiwania dostępu do bazy danych w celu wykonywania zadań zarządzania, ale nie mają potrzeby biznesowego uzyskiwania dostępu do określonych danych w zaszyfrowanych kolumnach. Dane są zawsze szyfrowane, co oznacza, że zaszyfrowane dane są odszyfrowywać tylko do przetwarzania przez aplikacje klienckie z dostępem do klucza szyfrowania. Klucz szyfrowania nigdy nie jest narażony na SQL Database lub SQL Managed Instance i może być przechowywany w Magazynie certyfikatów systemu [Windows](always-encrypted-certificate-store-configure.md) lub w [Azure Key Vault](always-encrypted-azure-key-vault-configure.md).

### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych

![Diagram przedstawiający dynamiczne maskowanie danych. Aplikacja biznesowa wysyła dane do bazy danych SQL, która maskuje dane przed wysłaniem ich z powrotem do aplikacji biznesowej.](./media/security-overview/azure-database-ddm.png)

Dynamiczne maskowanie danych ogranicza ujawnianie danych poufnych, maskując je w przypadku użytkowników bez uprawnień. Dynamiczne maskowanie danych automatycznie wykrywa potencjalnie poufne dane w usługach Azure SQL Database i SQL Managed Instance oraz udostępnia zalecenia z możliwością działania dotyczące maskowania tych pól przy minimalnym wpływie na warstwę aplikacji. Jego działanie polega na zaciemnianiu poufnych danych w wyznaczonych polach bazy danych w zestawie wyników zapytania, przy czym dane w bazie danych pozostają bez zmian. Aby uzyskać więcej informacji, zobacz Get started with SQL Database and SQL Managed Instance dynamic data masking (Wprowadzenie do dynamicznego [maskowania danych).](dynamic-data-masking-overview.md)

## <a name="security-management"></a>Zarządzanie zabezpieczeniami

### <a name="vulnerability-assessment"></a>Ocena luk w zabezpieczeniach

[Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to łatwa do skonfigurowania usługa, która może odnajdywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych w celu proaktywnego zwiększenia ogólnych zabezpieczeń bazy danych. Ocena luk w zabezpieczeniach jest częścią oferty Azure Defender for SQL, która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń SQL. Dostęp do oceny luk w zabezpieczeniach można uzyskać i zarządzać nimi za pośrednictwem centrum Azure Defender dla portalu SQL.

### <a name="data-discovery-and-classification"></a>Odnajdowanie i klasyfikacja danych

Odnajdywanie i klasyfikacja danych (obecnie w wersji zapoznawczej) zapewnia zaawansowane funkcje wbudowane w systemy Azure SQL Database i SQL Managed Instance służące do odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Odnajdywanie i klasyfikowanie poufnych danych poufnych (firmowych/finansowych, opieki zdrowotnej, danych osobowych itp.) może odgrywać kluczową rolę w organizacji w zakresie ochrony informacji. Może to stanowić infrastrukturę dla następujących działań:

- Różne scenariusze zabezpieczeń, takie jak monitorowanie (inspekcja) i alerty dotyczące anomalii dostępu do poufnych danych.
- Kontrolowanie dostępu do baz danych zawierających wysoce poufne dane oraz ich wzmacnianie.
- Pomoc przy spełnianiu standardów dotyczących prywatności danych i wymagań dotyczących zgodności z przepisami.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do odnajdywania i klasyfikacji danych.](data-discovery-and-classification-overview.md)

### <a name="compliance"></a>Zgodność

Oprócz powyższych funkcji i funkcji, które mogą pomóc aplikacji spełnić różne wymagania dotyczące zabezpieczeń, Azure SQL Database uczestniczy również w regularnych inspekcjach i ma certyfikat zgodności z szeregiem standardów zgodności. Aby uzyskać więcej informacji, zobacz [centrum Microsoft Azure zaufania,](https://www.microsoft.com/trust-center/compliance/compliance-overview) w którym można znaleźć najbardziej aktualną listę certyfikatów SQL Database zgodności.

## <a name="next-steps"></a>Następne kroki

- Omówienie użycia identyfikatorów logowania, kont użytkowników, ról bazy danych i uprawnień w usługach SQL Database i SQL Managed Instance zawiera temat Zarządzanie nazwami logowania [i kontami użytkowników.](logins-create-manage.md)
- Aby uzyskać omówienie inspekcji bazy danych, zobacz [inspekcję](../../azure-sql/database/auditing-overview.md).
- Aby uzyskać omówienie wykrywania zagrożeń, zobacz [wykrywanie zagrożeń.](threat-detection-configure.md)