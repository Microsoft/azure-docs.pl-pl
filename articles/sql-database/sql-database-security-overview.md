---
title: Przegląd zabezpieczeń
description: Dowiedz się więcej o zabezpieczeniach usługi Azure SQL Database i SQL Server, w tym o różnicach między chmurą a programem SQL Server lokalnie.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: 1d08770d81ade2d976142b2ce1fd94dae044cf32
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461399"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Omówienie funkcji zabezpieczeń usługi Azure SQL Database

W tym artykule opisano podstawy zabezpieczania warstwy danych aplikacji przy użyciu usługi Azure SQL Database. Opisana strategia zabezpieczeń jest zgodna z wielowarstwowym podejściem do obrony, jak pokazano na poniższym rysunku, i przesuwa się z zewnątrz w:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Bezpieczeństwo sieci

Usługa Microsoft Azure SQL Database zapewnia relacyjne usługi bazy danych dla aplikacji w chmurze i korporacyjnych. Aby pomóc w ochronie danych klienta, zapory uniemożliwiają dostęp sieciowy do serwera bazy danych, dopóki dostęp nie zostanie jawnie przyznany na podstawie adresu IP lub pochodzenia ruchu sieciowego usługi Azure Virtual.

### <a name="ip-firewall-rules"></a>Reguły zapory IP

Reguły zapory IP udzielają dostępu do baz danych na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, zobacz [Omówienie reguł bazy danych SQL azure i zapory usługi SQL Data Warehouse](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Reguły zapory sieci wirtualnej

[Punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) rozszerzają łączność sieci wirtualnej za pośrednictwem szkieletu platformy Azure i umożliwiają usługę Azure SQL Database identyfikowanie podsieci sieci wirtualnej, z której pochodzi ruch. Aby umożliwić ruchowi dostęp do usługi Azure SQL Database, użyj [tagów usługi](../virtual-network/security-overview.md) SQL, aby zezwolić na ruch wychodzący za pośrednictwem sieciowych grup zabezpieczeń.

[Reguły sieci wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md) umożliwiają usłudze Azure SQL Database akceptowanie tylko komunikacji, które są wysyłane z wybranych podsieci wewnątrz sieci wirtualnej.

> [!NOTE]
> Kontrolowanie dostępu za pomocą reguł zapory *nie* ma zastosowania do **wystąpienia zarządzanego**. Aby uzyskać więcej informacji na temat potrzebnej konfiguracji sieci, zobacz [łączenie się z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Zarządzanie dostępem

> [!IMPORTANT]
> Zarządzanie bazami danych i serwerami baz danych na platformie Azure jest kontrolowane przez przypisania ról konta użytkownika portalu. Aby uzyskać więcej informacji na temat tego artykułu, zobacz [kontrola dostępu oparta na rolach w witrynie Azure portal](../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentication

Uwierzytelnianie jest procesem udowodnienia, że użytkownik jest tym, za kogo się podają. Usługa Azure SQL Database obsługuje dwa typy uwierzytelniania:

- **Uwierzytelnianie SQL:**

    Uwierzytelnianie bazy danych SQL odnosi się do uwierzytelniania użytkownika podczas łączenia się z [usługą Azure SQL Database](sql-database-technical-overview.md) przy użyciu nazwy użytkownika i hasła. Podczas tworzenia serwera bazy danych należy określić login "Administrator serwera" przy użyciu nazwy użytkownika i hasła. Korzystając z tych poświadczeń, "administrator serwera" może uwierzytelnić się w dowolnej bazie danych na tym serwerze bazy danych jako właściciel bazy danych. Następnie administrator serwera może tworzyć dodatkowe logowania SQL i użytkowników, które umożliwiają użytkownikom łączenie się przy użyciu nazwy użytkownika i hasła.

- **Uwierzytelnianie usługi Azure Active Directory:**

    Uwierzytelnianie usługi Azure Active Directory to mechanizm łączenia się z [usługą Azure SQL Database](sql-database-technical-overview.md) i [magazynem danych SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD). Uwierzytelnianie usługi Azure AD umożliwia administratorom centralne zarządzanie tożsamościami i uprawnieniami użytkowników bazy danych wraz z innymi usługami firmy Microsoft w jednej centralnej lokalizacji. Obejmuje to minimalizację przechowywania haseł i umożliwia scentralizowane zasady rotacji haseł.

     Administrator serwera o nazwie **administrator usługi Active Directory** musi zostać utworzony w celu używania uwierzytelniania usługi Azure AD z bazą danych SQL. Aby uzyskać więcej informacji, zobacz [Łączenie się z bazą danych SQL przy użyciu uwierzytelniania usługi Azure Active Directory](sql-database-aad-authentication.md). Uwierzytelnianie usługi Azure AD obsługuje zarówno konta zarządzane, jak i federacyjne. Konta federacyjne obsługują użytkowników i grupy systemu Windows dla domeny klienta sfederowanej z usługą Azure AD.

    Dodatkowe dostępne opcje uwierzytelniania usługi Azure AD to [uwierzytelnianie uniwersalne usługi Active Directory dla](sql-database-ssms-mfa-authentication.md) połączeń programu SQL Server Management Studio, w tym [uwierzytelnianie wieloskładnikowe](../active-directory/authentication/concept-mfa-howitworks.md) i [dostęp warunkowy.](sql-database-conditional-access.md)

> [!IMPORTANT]
> Zarządzanie bazami danych i serwerami na platformie Azure jest kontrolowane przez przypisania ról konta użytkownika portalu. Aby uzyskać więcej informacji na temat tego artykułu, zobacz [kontrola dostępu oparta na rolach w witrynie Azure portal](../role-based-access-control/overview.md). Kontrolowanie dostępu za pomocą reguł zapory *nie* ma zastosowania do **wystąpienia zarządzanego**. Więcej informacji na temat potrzebnej konfiguracji sieci można znaleźć w poniższym artykule na temat [łączenia się z wystąpieniem zarządzanym.](sql-database-managed-instance-connect-app.md)

## <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do uprawnień przypisanych do użytkownika w bazie danych SQL Azure i określa, co użytkownik może zrobić. Uprawnienia są kontrolowane przez dodawanie kont użytkowników do [ról bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles) i przypisywanie uprawnień na poziomie bazy danych do tych ról lub przez przyznanie użytkownikowi pewnych [uprawnień na poziomie obiektu.](/sql/relational-databases/security/permissions-database-engine) Aby uzyskać więcej informacji, zobacz [Loginy i użytkownicy](sql-database-manage-logins.md)

Najlepszym rozwiązaniem jest tworzenie ról niestandardowych w razie potrzeby. Dodaj użytkowników do roli z najmniejszymi uprawnieniami wymaganymi do wykonywania funkcji zadania. Nie należy przypisywać uprawnień bezpośrednio użytkownikom. Konto administratora serwera jest członkiem wbudowanej roli db_owner, która ma szerokie uprawnienia i powinna być przyznawana tylko nielicznym użytkownikom z obowiązkami administracyjnymi. W przypadku aplikacji usługi Azure SQL Database użyj [polecenia WYKONAJ AS,](/sql/t-sql/statements/execute-as-clause-transact-sql) aby określić kontekst wykonywania wywoływanego modułu lub użyj [ról aplikacji](/sql/relational-databases/security/authentication-access/application-roles) z ograniczonymi uprawnieniami. Ta praktyka gwarantuje, że aplikacja, która łączy się z bazą danych ma najmniejsze uprawnienia wymagane przez aplikację. Przestrzeganie tych najlepszych praktyk sprzyja również rozdzieleniu obowiązków.

### <a name="row-level-security"></a>Zabezpieczenia na poziomie wiersza

Zabezpieczenia na poziomie wiersza umożliwia klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie cech użytkownika wykonującego kwerendę (na przykład członkostwa w grupie lub kontekstu wykonywania). Zabezpieczenia na poziomie wiersza mogą być również używane do implementowania niestandardowych koncepcji zabezpieczeń opartych na etykietach. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia na poziomie wierszy](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Ochrona przed zagrożeniami

Baza danych SQL zabezpiecza dane klientów, udostępniając funkcje inspekcji i wykrywania zagrożeń.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Inspekcja SQL w dziennikach i centrach zdarzeń usługi Azure Monitor

Inspekcja bazy danych SQL śledzi działania bazy danych i pomaga zachować zgodność ze standardami zabezpieczeń, rejestrując zdarzenia bazy danych w dzienniku inspekcji na koncie magazynu platformy Azure należącym do klienta. Inspekcja umożliwia użytkownikom monitorowanie bieżących działań bazy danych, a także analizowanie i badanie działań historycznych w celu zidentyfikowania potencjalnych zagrożeń lub podejrzewanych nadużyć i naruszeń zabezpieczeń. Aby uzyskać więcej informacji, zobacz Wprowadzenie do [inspekcji bazy danych SQL](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Zaawansowana ochrona przed zagrożeniami

Zaawansowana ochrona przed zagrożeniami analizuje dzienniki programu SQL Server w celu wykrycia nietypowych zachowań i potencjalnie szkodliwych prób uzyskania dostępu do baz danych lub ich wykorzystania. Alerty są tworzone dla podejrzanych działań, takich jak iniekcji SQL, potencjalne infiltracji danych i ataki siłowe lub anomalii w wzorcach dostępu, aby wychwytywać eskalacji uprawnień i naruszenia poświadczeń. Alerty są wyświetlane z [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/), gdzie są dostarczane szczegóły podejrzanych działań i zalecenia dotyczące dalszego badania podane wraz z akcji w celu złagodzenia zagrożenia. Za dodatkową opłatą można włączyć zaawansowaną ochronę przed zagrożeniami na serwerze. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do zaawansowanej ochrony przed zagrożeniami bazy danych SQL](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Ochrona i szyfrowanie informacji

### <a name="transport-layer-security-tls-encryption-in-transit"></a>TLS zabezpieczeń warstwy transportu (szyfrowanie w tranzycie)

Baza danych SQL zabezpiecza dane klientów, szyfrując dane w ruchu za pomocą [programu Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

Sql Server wymusza szyfrowanie (SSL/TLS) przez cały czas dla wszystkich połączeń. Dzięki temu wszystkie dane są szyfrowane "w tranzycie" między klientem a serwerem, niezależnie od ustawienia **Encrypt** lub **TrustServerCertificate** w ciągu połączenia.

Najlepszym rozwiązaniem jest zalecanie, aby w ciągu połączenia aplikacji określić połączenie szyfrowane i _**nie**_ ufać certyfikatowi serwera. Wymusza to aplikację, aby zweryfikować certyfikat serwera, a tym samym zapobiega aplikacji jest podatny na ataki typu man w środku typu.

Na przykład podczas korzystania ze sterownika ADO.NET jest to realizowane za pośrednictwem **Encrypt=True** i **TrustServerCertificate=False**. Jeśli uzyskasz parametry połączenia z witryny Azure portal, będzie miał poprawne ustawienia.

> [!IMPORTANT]
> Należy zauważyć, że niektóre sterowniki firmy innych niż Microsoft nie mogą domyślnie używać protokołu TLS lub polegać na starszej wersji protokołu TLS (<1.2) w celu działania. W tym przypadku SQL Server nadal umożliwia łączenie się z bazą danych. Zaleca się jednak, aby ocenić zagrożenia bezpieczeństwa umożliwiające takie sterowniki i aplikacji do łączenia się z bazy danych SQL, zwłaszcza jeśli przechowujesz poufne dane.
>
> Aby uzyskać więcej informacji na temat protokołu TLS i łączności, zobacz [zagadnienia dotyczące protokołu TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Przezroczyste szyfrowanie danych (szyfrowanie w spoczynku)

[Transparent Data Encryption (TDE) for Azure SQL Database](transparent-data-encryption-azure-sql.md) dodaje warstwę zabezpieczeń, aby chronić dane w stanie spoczynku przed nieautoryzowanym lub offline dostęp do plików raw lub kopii zapasowych. Typowe scenariusze obejmują kradzież centrum danych lub niezabezpieczoną utylizację sprzętu lub nośników, takich jak dyski i taśmy kopii zapasowych.TDE szyfruje całą bazę danych przy użyciu algorytmu szyfrowania AES, który nie wymaga od deweloperów aplikacji wprowadzania jakichkolwiek zmian w istniejących aplikacjach.

Na platformie Azure wszystkie nowo utworzone bazy danych SQL są domyślnie szyfrowane, a klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera.  Konserwacja i rotacja certyfikatów są zarządzane przez usługę i nie wymagają żadnych danych wejściowych od użytkownika. Klienci, którzy wolą przejąć kontrolę nad kluczami szyfrowania, mogą zarządzać kluczami w [usłudze Azure Key Vault](../key-vault/general/secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Zarządzanie kluczami za pomocą usługi Azure Key Vault

Obsługa [narzędzia Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK) dla [przejrzystego szyfrowania danych](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) umożliwia klientom przejęcie odpowiedzialności za zarządzanie kluczami i rotację za pomocą usługi Azure Key [Vault](../key-vault/general/secure-your-key-vault.md)— opartego na chmurze systemu zarządzania kluczami zewnętrznymi platformy Azure. Jeśli dostęp bazy danych do magazynu kluczy zostanie odwołany, nie można odszyfrować bazy danych i odczytać jej do pamięci. Usługa Azure Key Vault zapewnia centralną platformę zarządzania kluczami, wykorzystuje ściśle monitorowane sprzętowe moduły zabezpieczeń (HSM) i umożliwia rozdzielenie obowiązków między zarządzaniem kluczami a danymi w celu spełnienia wymagań dotyczących zgodności z zabezpieczeniami.

### <a name="always-encrypted-encryption-in-use"></a>Zawsze szyfrowane (szyfrowanie w użyciu)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Zawsze szyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine) to funkcja mająca na celu ochronę poufnych danych przechowywanych w określonych kolumnach bazy danych przed dostępem (na przykład numery kart kredytowych, krajowe numery identyfikacyjne lub dane na podstawie _potrzeby posiadania wiedzy)._ Obejmuje to administratorów baz danych lub innych uprzywilejowanych użytkowników, którzy są upoważnieni do dostępu do bazy danych do wykonywania zadań zarządzania, ale nie mają potrzeby biznesowych, aby uzyskać dostęp do określonych danych w zaszyfrowanych kolumnach. Dane są zawsze szyfrowane, co oznacza, że zaszyfrowane dane są odszyfrowywane tylko do przetwarzania przez aplikacje klienckie z dostępem do klucza szyfrowania.  Klucz szyfrowania nigdy nie jest narażony na działanie języka SQL i może być przechowywany w [Magazynie certyfikatów systemu Windows](sql-database-always-encrypted.md) lub w [usłudze Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

Funkcja dynamicznego maskowania danych w bazie danych SQL ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników. Dynamiczne maskowanie danych automatycznie odnajduje potencjalnie poufne dane w usłudze Azure SQL Database i udostępnia zalecenia dotyczące zaskarżenia tych pól, przy minimalnym wpływie na warstwę aplikacji. Jego działanie polega na zaciemnianiu poufnych danych w wyznaczonych polach bazy danych w zestawie wyników zapytania, przy czym dane w bazie danych pozostają bez zmian. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do dynamicznego maskowania danych bazy danych SQL](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Zarządzanie zabezpieczeniami

### <a name="vulnerability-assessment"></a>Ocena luk w zabezpieczeniach

[Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to łatwa w konfiguracji usługa, która może wykrywać, śledzić i pomagać w korygowania potencjalnych luk w zabezpieczeniach bazy danych w celu proaktywnego zwiększenia ogólnego bezpieczeństwa bazy danych. Ocena luk w zabezpieczeniach (VA) jest częścią oferty zaawansowanego bezpieczeństwa danych (ADS), która jest ujednoliconym pakietem dla zaawansowanych funkcji zabezpieczeń SQL. Ocena luk w zabezpieczeniach jest dostępna i zarządzana za pośrednictwem centralnego portalu SQL ADS.

### <a name="data-discovery--classification"></a>Odnajdowanie i klasyfikacja danych

Odnajdowanie danych & klasyfikacji (obecnie w wersji zapoznawczej) zapewnia zaawansowane możliwości wbudowane w usługę Azure SQL Database do odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Odkrywanie i klasyfikowanie najbardziej poufnych danych (biznesowych/ finansowych, opieki zdrowotnej, danych osobowych itp.) może odegrać kluczową rolę w twojej organizacyjnej ochronie informacji. Może to stanowić infrastrukturę dla następujących działań:

- Różne scenariusze zabezpieczeń, takie jak monitorowanie (inspekcja) i alerty o nietypowym dostępie do poufnych danych.
- Kontrolowanie dostępu do baz danych zawierających bardzo poufne dane i wzmacnianie ich.
- Pomoc przy spełnianiu standardów dotyczących prywatności danych i wymagań dotyczących zgodności z przepisami.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do odnajdowania danych & klasyfikacji](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Zgodność

Oprócz powyższych funkcji i funkcji, które mogą pomóc aplikacji spełnić różne wymagania dotyczące zabezpieczeń, usługa Azure SQL Database uczestniczy również w regularnych audytach i została certyfikowana zgodnie z wieloma standardami zgodności. Aby uzyskać więcej informacji, zobacz [Centrum zaufania platformy Microsoft Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) gdzie można znaleźć najnowszą listę certyfikatów zgodności bazy danych SQL.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniam użycia loginów, kont użytkowników, ról bazy danych i uprawnień w bazie danych SQL, zobacz [Zarządzanie loginami i kontami użytkowników](sql-database-manage-logins.md).
- Aby zapoznać się z omówieniem inspekcji baz danych, zobacz [Inspekcja bazy danych SQL](sql-database-auditing.md).
- Aby zapoznać się z omówieniami zagrożeń, zobacz [Wykrywanie zagrożeń bazy danych SQL](sql-database-threat-detection.md).
