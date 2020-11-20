---
title: Element PlayBook do rozwiązywania typowych wymagań dotyczących zabezpieczeń
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: W tym artykule przedstawiono typowe wymagania dotyczące zabezpieczeń i najlepsze rozwiązania w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 09/21/2020
ms.reviewer: ''
ms.openlocfilehash: b309ce01595e2e62bea7f78ae728d83bc7d9b9be
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992169"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database-and-azure-sql-managed-instance"></a>Element PlayBook do rozwiązywania typowych wymagań dotyczących zabezpieczeń w przypadku wystąpienia zarządzanego Azure SQL Database i usługi Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ten artykuł zawiera najlepsze rozwiązania dotyczące rozwiązywania typowych wymagań dotyczących zabezpieczeń. Nie wszystkie wymagania są stosowane do wszystkich środowisk i należy skonsultować się z bazami danych i zespołem ds. zabezpieczeń, na których funkcjach ma zostać wdrożona.

## <a name="solving-common-security-requirements"></a>Rozwiązywanie typowych wymagań dotyczących zabezpieczeń

Ten dokument zawiera wskazówki dotyczące sposobu rozwiązywania typowych wymagań dotyczących zabezpieczeń dla nowych lub istniejących aplikacji przy użyciu Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL. Jest on zorganizowany przez obszary zabezpieczeń wysokiego poziomu. Aby uzyskać informacje na temat rozwiązywania określonych zagrożeń, zapoznaj się z sekcją [typowe zagrożenia bezpieczeństwa i potencjalne środki zaradcze](#common-security-threats-and-potential-mitigations) . Mimo że niektóre z przedstawionych rekomendacji mają zastosowanie w przypadku migrowania aplikacji ze swojego miejsca lokalnego na platformę Azure, scenariusze migracji nie są skoncentrowane na tym dokumencie.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Oferty wdrażania Azure SQL Database omówione w tym przewodniku

- [Azure SQL Database](./index.yml): [pojedyncze bazy danych](single-database-overview.md) i [Pule elastyczne](elastic-pool-overview.md) na [serwerach](logical-servers.md)
- [Wystąpienie zarządzane Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)

### <a name="deployment-offers-not-covered-in-this-guide"></a>Oferty wdrażania nie zostały omówione w tym przewodniku

- Azure Synapse Analytics (dawniej Azure SQL Data Warehouse)
- Maszyny wirtualne Azure SQL (IaaS)
- SQL Server

### <a name="audience"></a>Grupy odbiorców

Zamierzone odbiorcy tego przewodnika są klientami mającymi pytania dotyczące zabezpieczania Azure SQL Database. Role zainteresowane tym najlepszym rozwiązaniem obejmują m.in.:

- Architekty zabezpieczeń
- Menedżerowie zabezpieczeń
- Funkcjonariusze ds. zgodności
- Funkcjonariusze ds. prywatności
- Inżynierowie ds. zabezpieczeń

### <a name="using-this-guide"></a><a id="using"></a> Korzystanie z tego przewodnika

Ten dokument jest przeznaczony jako pomocnik do istniejącej dokumentacji [zabezpieczeń Azure SQL Database](security-overview.md) .

O ile nie określono inaczej, zalecamy zapoznanie się z najlepszymi rozwiązaniami wymienionymi w poszczególnych sekcjach, aby osiągnąć odpowiedni cel lub wymaganie. Aby spełnić określone standardy zgodności lub najlepszych rozwiązań w zakresie zabezpieczeń, należy je wymienić w sekcji wymagania lub cele, tam gdzie ma to zastosowanie. Oto standardy i regulacje dotyczące zabezpieczeń, do których odwołuje się ten dokument:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): cm-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Access Control, Kryptografia
- [Praktyki Microsoft Operational Security Assurance (OSA)](https://www.microsoft.com/securityengineering/osa/practices): praktyka #1-6 i #9
- [Specjalna publikacja z biuletynu NIST 800-53](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

Planujemy kontynuowanie aktualizowania zaleceń i najlepszych rozwiązań wymienionych w tym miejscu. Podaj dane wejściowe lub korekty dla tego dokumentu, korzystając z linku **opinii** znajdującego się w dolnej części tego artykułu.

## <a name="authentication"></a>Authentication

Uwierzytelnianie to proces potwierdzania tożsamości użytkownika. Azure SQL Database i wystąpienie zarządzane SQL obsługują dwa typy uwierzytelniania:

- Uwierzytelnianie SQL
- Uwierzytelnianie za pomocą usługi Azure Active Directory

> [!NOTE]
> Uwierzytelnianie Azure Active Directory może nie być obsługiwane w przypadku wszystkich narzędzi i aplikacji innych firm.

### <a name="central-management-for-identities"></a>Centralne zarządzanie tożsamościami

Centralne zarządzanie tożsamościami oferuje następujące korzyści:

- Zarządzaj kontami grup i Kontroluj uprawnienia użytkowników bez duplikowania logowań między serwerami, bazami danych i wystąpieniami zarządzanymi.
- Uproszczone i elastyczne zarządzanie uprawnieniami.
- Zarządzanie aplikacjami w odpowiedniej skali.

**Jak zaimplementować**:

- Użyj uwierzytelniania Azure Active Directory (Azure AD) w celu scentralizowanego zarządzania tożsamościami.

**Najlepsze rozwiązania**:

- Utwórz dzierżawę usługi Azure AD i [Utwórz użytkowników](../../active-directory/fundamentals/add-users-azure-active-directory.md) , aby reprezentować użytkowników ludzkich, i Utwórz jednostki [usługi](../../active-directory/develop/app-objects-and-service-principals.md) , aby reprezentować aplikacje, usługi i narzędzia automatyzacji. Nazwy główne usług są równoważne z kontami usług w systemach Windows i Linux.

- Przypisywanie praw dostępu do zasobów do podmiotów zabezpieczeń usługi Azure AD za pośrednictwem przypisywania grup: Tworzenie grup usługi Azure AD, przyznawanie dostępu do grup i Dodawanie pojedynczych członków do grup. W bazie danych Utwórz użytkowników zawartej bazy danych, którzy mapują grupy usługi Azure AD. Aby przypisać uprawnienia wewnątrz bazy danych, należy umieścić użytkowników skojarzonych z grupami usługi Azure AD w rolach bazy danych z odpowiednimi uprawnieniami.
  - Zapoznaj się z artykułami, [Konfigurowanie i Zarządzanie uwierzytelnianiem Azure Active Directory przy użyciu programu SQL](authentication-aad-configure.md) i [Używanie usługi Azure AD do uwierzytelniania przy użyciu języka SQL](authentication-aad-overview.md).
  > [!NOTE]
  > W wystąpieniu zarządzanym SQL można także utworzyć nazwy logowania mapowane do podmiotów zabezpieczeń usługi Azure AD w bazie danych Master. Zobacz [Create Login (Transact-SQL)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- Korzystanie z grup usługi Azure AD upraszcza zarządzanie uprawnieniami oraz właściciela grupy, a właściciel zasobu może dodawać i usuwać członków z grupy.

- Utwórz oddzielną grupę dla administratorów usługi Azure AD dla każdego serwera lub wystąpienia zarządzanego.

  - Zapoznaj się z tym artykułem, [Aby udostępnić administratorowi Azure Active Directory dla serwera](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

- Monitoruj zmiany członkostwa w grupach usługi Azure AD przy użyciu raportów działania inspekcji usługi Azure AD.

- W przypadku wystąpienia zarządzanego do utworzenia administratora usługi Azure AD jest wymagany oddzielny krok.
  - Zapoznaj się z artykułem, [zainicjuj Azure Active Directory administratorem wystąpienia zarządzanego](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

> [!NOTE]
>
> - Uwierzytelnianie usługi Azure AD jest rejestrowane w dziennikach inspekcji usługi Azure SQL, ale nie w dziennikach logowania usługi Azure AD.
> - Uprawnienia usługi Azure RBAC przyznane na platformie Azure nie mają zastosowania do uprawnień wystąpienia zarządzanego Azure SQL Database lub SQL. Takie uprawnienia muszą zostać utworzone/zmapowane ręcznie przy użyciu istniejących uprawnień SQL.
> - Po stronie klienta uwierzytelnianie usługi Azure AD wymaga dostępu do Internetu lub za pośrednictwem trasy zdefiniowanej przez użytkownika (UDR) do sieci wirtualnej.
> - Token dostępu usługi Azure AD jest buforowany po stronie klienta, a jego okres istnienia zależy od konfiguracji tokenu. Zapoznaj się z artykułem [konfigurowalne okresy istnienia tokenu w Azure Active Directory](../../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Aby uzyskać wskazówki dotyczące rozwiązywania problemów z uwierzytelnianiem w usłudze Azure AD, zobacz następujący Blog: [Rozwiązywanie problemów z usługą Azure AD](https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991).

### <a name="azure-ad-multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe w usłudze Azure AD

> Wymienione w: rozwiązanie OSA #2, ISO Access Control (AC)

Usługa Azure AD Multi-Factor Authentication zapewnia dodatkowe zabezpieczenia, wymagając więcej niż jednej formy uwierzytelniania.

**Jak zaimplementować**:

- [Włącz Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) w usłudze Azure AD przy użyciu dostępu warunkowego i Użyj uwierzytelniania interakcyjnego.

- Alternatywą jest włączenie Multi-Factor Authentication dla całej domeny usługi Azure AD lub AD.

**Najlepsze rozwiązania**:

- Aktywuj dostęp warunkowy w usłudze Azure AD (wymaga subskrypcji Premium).
  - Zapoznaj się z artykułem [dostęp warunkowy w usłudze Azure AD](../../active-directory/conditional-access/overview.md).  

- Utwórz grupy usługi Azure AD i Włącz zasady Multi-Factor Authentication dla wybranych grup przy użyciu dostępu warunkowego usługi Azure AD.
  - Zapoznaj się z artykułem [Planowanie wdrożenia dostępu warunkowego](../../active-directory/conditional-access/plan-conditional-access.md).

- Multi-Factor Authentication można włączyć dla całej usługi Azure AD lub dla całej Active Directory federacyjnej z usługą Azure AD.

- Użyj trybu uwierzytelniania interakcyjnego usługi Azure AD dla Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL, w przypadku których hasło jest wymagane interaktywnie, a następnie Multi-Factor Authentication:
  - Użyj uwierzytelniania uniwersalnego w programie SSMS. Zobacz artykuł [używanie uwierzytelniania wieloskładnikowego usługi Azure AD z Azure SQL Database, wystąpieniem zarządzanym SQL, Azure Synapse (Obsługa programu SSMS dla Multi-Factor Authentication)](authentication-mfa-ssms-overview.md).
  - Użyj interakcyjnego uwierzytelniania obsługiwanego w narzędziach SQL Server Data Tools (SSDT). Zapoznaj się z artykułem [Azure Active Directory support w programie SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Użyj innych narzędzi SQL obsługiwanych Multi-Factor Authentication.
    - Kreator programu SSMS obsługujący eksportowanie/wyodrębnianie/wdrażanie bazy danych  
    - [sqlpackage.exe](/sql/tools/sqlpackage): opcja "/UA"
    - [Narzędzie sqlcmd](/sql/tools/sqlcmd-utility): opcja-G (Interactive)
    - [Narzędzie bcp](/sql/tools/bcp-utility): opcja-G (Interactive)

- Zaimplementuj aplikacje, aby nawiązać połączenie z usługą Azure SQL Database lub wystąpieniem zarządzanym usługi Azure SQL przy użyciu uwierzytelniania interaktywnego z obsługą Multi-Factor Authentication.
  - Zapoznaj się z artykułem [Azure SQL Database za pomocą usługi Azure AD Multi-Factor Authentication](active-directory-interactive-connect-azure-sql-db.md).
  > [!NOTE]
  > Ten tryb uwierzytelniania wymaga tożsamości opartych na użytkownikach. W przypadkach, gdy używany jest model zaufanej tożsamości, który pomija indywidualne uwierzytelnianie użytkowników usługi Azure AD (np. przy użyciu tożsamości zarządzanej dla zasobów platformy Azure), Multi-Factor Authentication nie ma zastosowania.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Minimalizacja użycia uwierzytelniania opartego na hasłach dla użytkowników

> Wymienione w: rozwiązanie OSA #4, ISO Access Control (AC)

Metody uwierzytelniania oparte na hasłach są słabsze w postaci uwierzytelniania. Poświadczenia mogą być naruszone lub odrzucane.

**Jak zaimplementować**:

- Użyj zintegrowanego uwierzytelniania usługi Azure AD, które eliminuje korzystanie z haseł.

**Najlepsze rozwiązania**:

- Użyj uwierzytelniania logowania jednokrotnego przy użyciu poświadczeń systemu Windows. Sfederować lokalną domenę usługi AD z usługą Azure AD i używaj zintegrowanego uwierzytelniania systemu Windows (w przypadku komputerów przyłączonych do domeny z usługą Azure AD).
  - Zapoznaj się z artykułem program [SSMS obsługujący zintegrowane uwierzytelnianie usługi Azure AD](authentication-aad-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Minimalizacja użycia uwierzytelniania opartego na hasłach dla aplikacji

> Wymienione w: rozwiązanie OSA #4, ISO Access Control (AC)

**Jak zaimplementować**:

- Włącz tożsamość zarządzaną platformy Azure. Można również użyć uwierzytelniania zintegrowanego lub opartego na certyfikatach.

**Najlepsze rozwiązania**:

- Korzystaj z [zarządzanych tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).
  - [Tożsamość zarządzana przypisana przez system](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)
  - [Tożsamość zarządzana przypisana przez użytkownika](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Korzystanie z Azure SQL Database z Azure App Service z tożsamością zarządzaną (bez zmian w kodzie)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Używanie uwierzytelniania opartego na certyfikatach dla aplikacji.
  - Zobacz ten [przykład kodu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token).

- Użyj uwierzytelniania usługi Azure AD dla zintegrowanej domeny federacyjnej i komputera przyłączonego do domeny (patrz sekcja powyżej).
  - Zobacz [przykładową aplikację do uwierzytelniania zintegrowanego](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Ochrona haseł i wpisów tajnych

W przypadkach, w których nie można uniknąć haseł, upewnij się, że są one zabezpieczone.

**Jak zaimplementować**:

- Użyj Azure Key Vault do przechowywania haseł i wpisów tajnych. Jeśli ma to zastosowanie, użyj Multi-Factor Authentication dla Azure SQL Database z użytkownikami usługi Azure AD.

**Najlepsze rozwiązania**:

- Jeśli nie jest możliwe uniknięcie haseł lub wpisów tajnych, należy przechowywać hasła użytkowników i wpisy tajne aplikacji w Azure Key Vault i zarządzać dostępem za poorednictwem zasad dostępu Key Vault.

- Różne platformy programistyczne aplikacji mogą również oferować mechanizmy specyficzne dla platformy służące do ochrony kluczy tajnych w aplikacji. Na przykład: [aplikacja ASP.NET Core](/aspnet/core/security/app-secrets?tabs=windows&view=aspnetcore-2.1).

### <a name="use-sql-authentication-for-legacy-applications"></a>Używanie uwierzytelniania SQL dla starszych aplikacji

Uwierzytelnianie SQL dotyczy uwierzytelnienia użytkownika podczas nawiązywania połączenia z usługą Azure SQL Database lub wystąpieniem zarządzanym SQL przy użyciu nazwy użytkownika i hasła. Należy utworzyć identyfikator logowania na każdym serwerze lub w zarządzanym wystąpieniu, a użytkownik utworzony w każdej bazie danych.

**Jak zaimplementować**:

- Użyj uwierzytelniania SQL.

**Najlepsze rozwiązania**:

- Jako administrator serwera lub wystąpienia utwórz identyfikatory logowania i użytkowników. W przypadku korzystania z użytkowników zawartej bazy danych z hasłami wszystkie hasła są przechowywane w bazie danych Master.
  - Zobacz artykuł, [kontrolowanie i udzielanie dostępu do bazy danych do SQL Database, wystąpienia zarządzanego SQL i usługi Azure Synapse Analytics](logins-create-manage.md).

## <a name="access-management"></a>Zarządzanie dostępem

Zarządzanie dostępem (nazywane również autoryzacją) to proces kontroli dostępu i uprawnień autoryzowanych użytkowników oraz zarządzania nimi do Azure SQL Database lub wystąpienia zarządzanego SQL.

### <a name="implement-principle-of-least-privilege"></a>Implementacja reguły najniższych uprawnień

> Wymienione w: FedRamp Controls AC-06, NIST: AC-6, OSA Practice #3

Zasada najniższych uprawnień, które użytkownicy nie powinni mieć więcej uprawnień niż jest to konieczne do wykonania swoich zadań. Aby uzyskać więcej informacji, zapoznaj się z artykułem [wystarczającym do administrowania](/powershell/scripting/learn/remoting/jea/overview).

**Jak zaimplementować**:

Przypisz tylko niezbędne [uprawnienia](/sql/relational-databases/security/permissions-database-engine) , aby wykonać wymagane zadania:

- W bazach danych SQL:
  - Użyj szczegółowych uprawnień i ról baz danych zdefiniowanych przez użytkownika (lub ról serwera w wystąpieniu zarządzanym):
    1. Tworzenie wymaganych ról
       - [UTWÓRZ ROLĘ](/sql/t-sql/statements/create-role-transact-sql)
       - [UTWÓRZ ROLĘ SERWERA](/sql/t-sql/statements/create-server-role-transact-sql)
    1. Tworzenie wymaganych użytkowników
       - [UTWÓRZ UŻYTKOWNIKA](/sql/t-sql/statements/create-user-transact-sql)
    1. Dodawanie użytkowników jako członków do ról
       - [ZMIEŃ ROLĘ](/sql/t-sql/statements/alter-role-transact-sql)
       - [ZMIEŃ ROLĘ SERWERA](/sql/t-sql/statements/alter-server-role-transact-sql)
    1. Następnie przypisz uprawnienia do ról.
       - [GRANT](/sql/t-sql/statements/grant-transact-sql)
  - Upewnij się, że nie przypiszesz użytkowników do niepotrzebnych ról.

- W Azure Resource Manager:
  - Użyj wbudowanych ról, jeśli są dostępne lub role niestandardowe platformy Azure i przypisz niezbędne uprawnienia.
    - [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md)
    - [Role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md)

**Najlepsze rozwiązania**:

Następujące najlepsze rozwiązania są opcjonalne, ale spowodują lepsze zarządzanie i obsługę strategii zabezpieczeń:

- Jeśli to możliwe, Zacznij od najmniejszego możliwego zestawu uprawnień i zacznij dodawać uprawnienia jedno po drugim, jeśli istnieje rzeczywista konieczność (i uzasadnienie) — w przeciwieństwie do podejścia odwrotnego: pobieranie uprawnień krok po kroku.

- Nie należy przypisywać uprawnień do poszczególnych użytkowników. Zamiast tego użyj spójnie ról (ról bazy danych lub serwera). Role ułatwiają użytkownikom raportowanie i rozwiązywanie problemów. (Kontrola RBAC platformy Azure obsługuje tylko przypisanie uprawnień za pośrednictwem ról).

- Tworzenie i używanie ról niestandardowych z wymaganymi dokładnymi uprawnieniami. Typowe role, które są używane w ramach ćwiczeń:
  - Wdrażanie zabezpieczeń
  - Administrator
  - Deweloper
  - Personel pomocy technicznej
  - Audytor
  - Zautomatyzowane procesy
  - Użytkownik końcowy
  
- Wbudowane role są używane tylko wtedy, gdy uprawnienia ról pasują dokładnie do odpowiednich uprawnień użytkownika. Można przypisać użytkowników do wielu ról.

- Należy pamiętać, że uprawnienia w aparacie bazy danych mogą być stosowane w następujących zakresach (mniejszym zakresie, mniejszym wpływem przyznanych uprawnień):
  - Serwer (specjalne role w bazie danych Master) na platformie Azure
  - baza danych
  - Schemat
    - Najlepszym rozwiązaniem jest użycie schematów do przyznawania uprawnień w bazie danych. (Zobacz również: [projektowanie schematu: zalecenia dotyczące projektowania schematu z bezpieczeństwem](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Obiekt (tabela, widok, procedura, itp.)

  > [!NOTE]
  > Nie zaleca się stosowania uprawnień na poziomie obiektu, ponieważ ten poziom dodaje niezbędną złożoność do ogólnej implementacji. Jeśli zdecydujesz się użyć uprawnień na poziomie obiektu, powinny one być jasno udokumentowane. To samo dotyczy uprawnień na poziomie kolumny, które są jeszcze mniej zalecane z tego samego powodu. Należy również pamiętać, że domyślnie [odmowa](/sql/t-sql/statements/deny-object-permissions-transact-sql) poziomu tabeli nie przesłania dotacji na poziomie kolumny. Będzie to wymagało aktywowania [konfiguracji serwera zgodności typowych kryteriów](/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) .

- Przeprowadzaj regularne kontrole przy użyciu [oceny luk w zabezpieczeniach](/sql/relational-databases/security/sql-vulnerability-assessment) , aby przetestować zbyt wiele uprawnień.

### <a name="implement-separation-of-duties"></a>Zaimplementuj Rozdzielenie obowiązków

> Wymienione w: FedRamp: AC-04, NIST: AC-5, ISO: A. 6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

Rozdzielenie obowiązków, nazywane także rozdzieleniem obowiązków, zawiera opis wymagania podziału zadań poufnych na wiele zadań, które są przypisane do różnych użytkowników. Rozdzielenie obowiązków pomaga uniknąć naruszeń danych.

**Jak zaimplementować**:

- Określ wymagany poziom rozdzielenia obowiązków. Przykłady:
  - Między środowiskami deweloperskim i testowym i produkcyjnym
  - Zadania związane z bezpieczeństwem (DBA) o zabezpieczeniach i zadania dotyczące zarządzania bazami danych — zadania dla deweloperów.
    - Przykłady: audytor, tworzenie zasad zabezpieczeń dla zabezpieczeń na poziomie roli, implementowanie SQL Database obiektów z uprawnieniami DDL.

- Zidentyfikuj kompleksową hierarchię użytkowników (i zautomatyzowanych procesów), które uzyskują dostęp do systemu.

- Utwórz role zgodnie z wymaganymi grupami użytkowników i przypisz uprawnienia do ról.
  - W przypadku zadań na poziomie zarządzania w Azure Portal lub za pośrednictwem programu PowerShell — Automatyzacja Użyj ról platformy Azure. Znajdź wbudowaną rolę zgodną z wymaganiem lub Utwórz rolę niestandardową platformy Azure przy użyciu dostępnych uprawnień
  - Utwórz role serwera dla zadań na całym serwerze (tworzenie nowych nazw logowania, baz danych) w wystąpieniu zarządzanym.
  - Utwórz role bazy danych dla zadań na poziomie bazy danych.

- W przypadku niektórych poufnych zadań należy rozważyć utworzenie specjalnych procedur przechowywanych podpisanych przez certyfikat w celu wykonywania zadań w imieniu użytkowników. Jedną z ważnych zalet procedur składowanych podpisanych cyfrowo jest to, że w przypadku zmiany procedury uprawnienia przyznane do poprzedniej wersji procedury zostaną natychmiast usunięte.
  - Przykład: [Samouczek: podpisywanie procedur składowanych przy użyciu certyfikatu](/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate)

- Zaimplementuj Transparent Data Encryption (TDE) z kluczami zarządzanymi przez klienta w Azure Key Vault, aby umożliwić Rozdzielenie obowiązków między właścicielem danych i właścicielem zabezpieczeń.
  - Zapoznaj się z artykułem [Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania za pomocą usługi Azure Storage z Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md).

- Aby upewnić się, że administrator DBA o niewidoczne dane, które są uważane za bardzo poufne i nadal mogą wykonywać zadania w ramach programu DBA, można użyć Always Encrypted z separacją ról.
  - Zapoznaj się z artykułami [Omówienie zarządzania kluczami dla Always Encrypted](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), [inicjowania obsługi kluczy z separacją ról](/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)i [rotacji kluczy głównych kolumn z separacją ról](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation).

- W przypadkach, w których korzystanie z Always Encrypted nie jest możliwe, lub co najmniej nie ma poważniejszych kosztów i wysiłków, które mogą nawet renderować system blisko niezdatnego do użytku, można wprowadzać naruszenia i złagodzić je za pomocą kontrolek kompensacyjnych, takich jak:
  - Interwencja człowieka w procesach.
  - Dziennik inspekcji — Aby uzyskać więcej informacji na temat inspekcji, zobacz [Inspekcja krytycznych zdarzeń zabezpieczeń](#audit-critical-security-events).

**Najlepsze rozwiązania**:

- Upewnij się, że różne konta są używane w środowisku deweloperskim/testowym i produkcyjnym. Różne konta ułatwiają przestrzeganie rozdzielania systemów testowych i produkcyjnych.

- Nie należy przypisywać uprawnień do poszczególnych użytkowników. Zamiast tego użyj spójnie ról (ról bazy danych lub serwera). Posiadanie ról pomaga znacznie w raportowaniu i rozwiązywaniu problemów z uprawnieniami.

- Użyj wbudowanych ról, gdy uprawnienia dokładnie pasują do wymaganych uprawnień — Jeśli Unia wszystkich uprawnień z wielu wbudowanych ról prowadzi do 100% dopasowania, możesz również przypisać wiele ról jednocześnie.

- Tworzenie i używanie ról zdefiniowanych przez użytkownika, gdy wbudowane role udzielą zbyt wielu uprawnień lub nie są niewystarczające uprawnienia.

- Przypisania ról można także wykonać tymczasowo, znane także jako dynamiczne separacje obowiązków (DSD), w ramach kroków zadania agenta SQL w języku T-SQL lub przy użyciu usługi Azure PIM dla ról platformy Azure.

- Upewnij się, że przetwarzający nie mają dostępu do kluczy szyfrowania lub magazynów kluczy, a administratorzy zabezpieczeń z dostępem do kluczy nie mają z kolei dostępu do bazy danych. Korzystanie z [rozszerzalnego zarządzania kluczami (EKM)](/sql/relational-databases/security/encryption/extensible-key-management-ekm) może ułatwić osiągnięcie tego rozbarwień. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) może służyć do implementowania EKM.

- Zawsze upewnij się, że masz dziennik inspekcji dla akcji związanych z zabezpieczeniami.

- Możesz pobrać definicję wbudowanych ról platformy Azure, aby wyświetlić używane uprawnienia i utworzyć rolę niestandardową w oparciu o fragmenty i kumulacje tych elementów za pośrednictwem programu PowerShell.

- Ze względu na to, że każdy członek roli bazy danych db_owner może zmienić ustawienia zabezpieczeń, takie jak Transparent Data Encryption (TDE) lub zmienić cel SLO, członkostwo należy udzielić z opieką. Istnieje jednak wiele zadań, które wymagają uprawnień db_owner. Zadanie, takie jak zmiana ustawień bazy danych, takie jak zmiana opcji DB. Inspekcja odgrywa kluczową rolę w dowolnym rozwiązaniu.

- Nie można ograniczyć uprawnień db_owner i w związku z tym uniemożliwić wyświetlanie danych użytkownika przez konto administracyjne. Jeśli w bazie danych znajdują się wysoce poufne dane, Always Encrypted może służyć do bezpiecznego zapobiegania wyświetlaniu go przez db_owners lub innych administratorów.

> [!NOTE]
> Osiągnięcie rozdzielenia obowiązków (SoD) jest wyzwaniem dla zadań związanych z zabezpieczeniami lub rozwiązywania problemów. Inne obszary, takie jak role użytkowników końcowych i deweloperskich, są łatwiejsze do oddzielenia. Większość kontroli związanych z zgodnością umożliwia korzystanie z alternatywnych funkcji kontroli, takich jak inspekcje, gdy inne rozwiązania nie są praktyczne.

W przypadku czytelników, którzy chcą szczegółowe w SoD, zalecamy użycie następujących zasobów:

- Dla Azure SQL Database i wystąpienia zarządzanego SQL:  
  - [Kontrolowanie i udzielanie dostępu do bazy danych](logins-create-manage.md)
  - [Separacja przez silnik obowiązków dla deweloperów aplikacji](/previous-versions/sql/sql-server-2008/cc974525(v=sql.100))
  - [Rozdzielenie obowiązków](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Procedury składowane podpisywania](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- W przypadku usługi Azure Resource Management:
  - [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md)
  - [Role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md)
  - [Używanie Azure AD Privileged Identity Management do uzyskiwania dostępu z podwyższonym poziomem uprawnień](https://www.microsoft.com/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Wykonywanie zwykłych przeglądów kodu

> Wymienione w: PCI: 6.3.2, SOC: SDL-3

Podział obowiązków nie jest ograniczony do danych w bazie danych, ale zawiera kod aplikacji. Złośliwy kod może potencjalnie obejść kontrolę zabezpieczeń. Przed wdrożeniem niestandardowego kodu w środowisku produkcyjnym należy zapoznać się z tym, co jest wdrażane.

**Jak zaimplementować**:

- Użyj narzędzia bazy danych, takiego jak Azure Data Studio, które obsługuje kontrolę źródła.

- Zaimplementuj proces wdrożenia rozdzielonego kodu.

- Przed zatwierdzeniem do głównej gałęzi, osoba (inna niż autor kodu) musi zbadać kod pod kątem potencjalnego podniesienia uprawnień ryzyka, a także złośliwe modyfikacje danych chroniące przed oszustwem i nieautoryzowanym dostępem. Można to zrobić przy użyciu mechanizmów kontroli źródła.

**Najlepsze rozwiązania**:

- Normalizacja: ułatwia zaimplementowanie standardowej procedury, która ma być stosowana w przypadku aktualizacji kodu.

- Ocena luk w zabezpieczeniach zawiera reguły sprawdzające nadmierne uprawnienia, użycie starych algorytmów szyfrowania i inne problemy z zabezpieczeniami w schemacie bazy danych.

- Dalsze kontrole można przeprowadzić w środowisku kontroli jakości lub testowania przy użyciu zaawansowanej ochrony przed zagrożeniami, która skanuje kod, który jest narażony na wstrzyknięcie kodu SQL.

- Przykłady elementów do wyszukania:
  - Tworzenie użytkownika lub zmiana ustawień zabezpieczeń z poziomu zautomatyzowanego wdrożenia programu SQL Server Code-Update.
  - Procedura składowana, która w zależności od dostarczonych parametrów aktualizuje wartość pieniężną w komórce w sposób niezgodny.

- Upewnij się, że osoba przeprowadzająca przegląd jest osobą niebędącą autorem kodu źródłowego i wiedząc w przeglądach kodu i bezpiecznym kodowaniu.

- Upewnij się, że wszystkie źródła zmian kodu są znane. Kod może być w skryptach T-SQL. Mogą to być polecenia ad hoc, które mają być wykonywane lub wdrażane w formie widoków, funkcji, wyzwalaczy i procedur składowanych. Może być częścią definicji zadań programu SQL Agent (kroki). Można go również wykonać z poziomu pakietów SSIS, Azure Data Factory i innych usług.

## <a name="data-protection"></a>Ochrona danych

Ochrona danych to zestaw funkcji służących do ochrony ważnych informacji przed naruszeniem przez szyfrowanie lub zamieszanie.

> [!NOTE]
> Firma Microsoft zaświadczy Azure SQL Database i wystąpienie zarządzane SQL jako zgodne ze standardem FIPS 140-2 poziom 1. Jest to wykonywane po sprawdzeniu ścisłego użycia dozwolonych algorytmów FIPS 140-2 Level 1 oraz zweryfikowanych wystąpień FIPS 140-2 poziomu 1 tych algorytmów, w tym spójność z wymaganymi długościami kluczy, zarządzaniem kluczami, generowaniem kluczy i magazynem kluczy. To zaświadczanie ma na celu umożliwienie naszym klientom reagowania na potrzebę lub wymóg korzystania z zweryfikowanych wystąpień poziomu FIPS 140-2 na poziomie 1 podczas przetwarzania danych lub dostarczania systemów lub aplikacji. Definiujemy warunki "zgodność ze standardem FIPS 140-2 Level 1" i "zgodności ze standardem FIPS 140-2 Level 1" używanym w powyższej instrukcji, aby wykazać, że zamierzone zastosowanie do USA i kanadyjskie w Kanadzie użycie innego terminu "FIPS 140-2 Level 1".

### <a name="encrypt-data-in-transit"></a>Szyfruj dane podczas przesyłania

> Wymienione w: rozwiązanie OSA #6, Rodzina formantów ISO: Kryptografia

Chroni dane, gdy dane są przenoszone między klientem i serwerem. Zapoznaj się z [zabezpieczeniami sieciowymi](#network-security).

### <a name="encrypt-data-at-rest"></a>Szyfrowanie danych magazynowanych

> Wymienione w: rozwiązanie OSA #6, Rodzina formantów ISO: Kryptografia

Szyfrowanie w czasie spoczynku jest ochroną kryptograficzną danych, gdy jest ona utrwalana w plikach bazy danych, dziennika i kopii zapasowej.

**Jak zaimplementować**:

- [Szyfrowanie przezroczystej bazy danych (TDE)](transparent-data-encryption-tde-overview.md) z kluczami zarządzanymi przez usługę jest domyślnie włączone dla wszystkich baz danych utworzonych po 2017 w Azure SQL Database i wystąpieniu zarządzanym SQL.
- W przypadku wystąpienia zarządzanego, jeśli baza danych została utworzona na podstawie operacji przywracania przy użyciu serwera lokalnego, zostanie wyliczone ustawienie TDE oryginalnej bazy danych. Jeśli oryginalna baza danych nie ma włączonej TDE, zalecamy ręczne włączenie TDE dla wystąpienia zarządzanego.

**Najlepsze rozwiązania**:

- Nie przechowuj danych, które wymagają szyfrowania w bazie danych Master. Bazy danych Master nie można zaszyfrować za pomocą TDE.

- Klucze zarządzane przez klienta w programie Azure Key Vault, jeśli potrzebujesz zwiększonej przejrzystości i szczegółowej kontroli nad ochroną TDE. Azure Key Vault umożliwia Odwoływanie uprawnień w dowolnym momencie w celu wyrenderowania niedostępności bazy danych. Możesz centralnie zarządzać ochroną TDE, a także z innymi kluczami lub obrócić funkcję ochrony TDE zgodnie z własnym harmonogramem, korzystając z Azure Key Vault.

- Jeśli używasz kluczy zarządzanych przez klienta w programie Azure Key Vault, postępuj zgodnie z artykułami [wskazówki dotyczące KONFIGUROWANIA TDE z Azure Key Vault](transparent-data-encryption-byok-overview.md#recommendations-when-configuring-akv) i [konfigurowania geograficznego odzyskiwania przy użyciu Azure Key Vault](transparent-data-encryption-byok-overview.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Ochrona poufnych danych używanych przez nieautoryzowanych użytkowników z wysokim poziomem uprawnień

Używane dane to dane przechowywane w pamięci systemu bazy danych podczas wykonywania zapytań SQL. Jeśli baza danych przechowuje poufne dane, organizacja może być wymagana w celu zapewnienia, że użytkownicy z wysokim poziomem uprawnień nie będą mogli wyświetlać poufnych danych w bazie danych. Użytkownicy o wysokim poziomie uprawnień, tacy jak operatorzy firmy Microsoft lub przetwarzający w organizacji, powinni mieć możliwość zarządzania bazą danych, ale uniemożliwiają wyświetlanie i potencjalnie jest wykradzenie poufnych danych z pamięci procesu SQL lub przez wykonywanie zapytań do bazy danych.

Zasady, które określają, które dane są poufne i czy poufne dane muszą być szyfrowane w pamięci i nie są dostępne dla administratorów w postaci zwykłego tekstu, są specyficzne dla organizacji i przepisów dotyczących zgodności, które należy stosować do programu. Sprawdź powiązane wymagania: [zidentyfikuj i Oznacz poufne dane](#identify-and-tag-sensitive-data).

**Jak zaimplementować**:

- Użyj [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) , aby zapewnić, że poufne dane nie są ujawniane w postaci zwykłego tekstu w Azure SQL Database lub w wystąpieniu zarządzanym SQL, nawet w pamięci/w użyciu. Always Encrypted chroni dane pochodzących od administratorów bazy danych (przetwarzający) i administratorów chmury (lub nieuprawnionych uczestników, którzy mogą personifikować użytkowników z wysokim poziomem uprawnień, ale nieautoryzowanymi użytkownikami) i oferują większą kontrolę nad tym, kto może uzyskiwać dostęp do danych.

**Najlepsze rozwiązania**:

- Always Encrypted nie zastępuje szyfrowania danych przechowywanych w czasie spoczynku (TDE) lub przesyłania (SSL/TLS). Always Encrypted nie należy używać w przypadku danych niewrażliwych w celu zminimalizowania wpływu wydajności i funkcjonalności. Używanie Always Encrypted w połączeniu z TDE i Transport Layer Security (TLS) jest zalecane do zapewnienia kompleksowej ochrony danych w czasie, w trakcie przesyłania i użytkowania.

- Oceń wpływ szyfrowania zidentyfikowanych kolumn danych poufnych przed wdrożeniem Always Encrypted w produkcyjnej bazie danych. Ogólnie rzecz biorąc, Always Encrypted zmniejsza funkcjonalność zapytań dotyczących zaszyfrowanych kolumn i ma inne ograniczenia, wymienione w [szczegółach Always Encrypted-funkcji](/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). W związku z tym, może być konieczne ponowne zaprojektowanie aplikacji w celu ponownego zaimplementowania funkcji, nie obsługuje zapytania, po stronie klienta lub/i refaktoryzacji schematu bazy danych, w tym definicji procedur przechowywanych, funkcji, widoków i wyzwalaczy. Istniejące aplikacje mogą nie współpracować z zaszyfrowanymi kolumnami, jeśli nie są zgodne z ograniczeniami i ograniczeniami Always Encrypted. Podczas gdy ekosystem narzędzi firmy Microsoft, produktów i usług, które obsługują Always Encrypted rośnie, niektóre z nich nie działają z zaszyfrowanymi kolumnami. Szyfrowanie kolumny może również wpływać na wydajność zapytań, w zależności od charakterystyki obciążenia.

- Zarządzaj kluczami Always Encrypted z separacją ról, jeśli używasz Always Encrypted do ochrony danych przed złośliwym przetwarzający. W przypadku separacji ról Administrator zabezpieczeń tworzy klucze fizyczne. W ramach tej bazy danych tworzone są obiekty metadanych klucza głównego kolumny i klucz szyfrowania kolumny opisujące klucze fizyczne w bazie. W trakcie tego procesu administrator zabezpieczeń nie musi mieć dostępu do bazy danych, a administrator DBA nie potrzebuje dostępu do kluczy fizycznych w postaci zwykłego tekstu.
  - Zapoznaj się z artykułem [Zarządzanie kluczami z separacją ról](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) w celu uzyskania szczegółowych informacji.

- Przechowuj klucze główne kolumn w Azure Key Vault w celu ułatwienia zarządzania. Unikaj używania magazynu certyfikatów systemu Windows (i ogólnie rozproszonych rozwiązań magazynu kluczy, takich jak centralne rozwiązania do zarządzania kluczami), które sprawiają, że zarządzanie kluczami jest trudne.

- Ostrożnie zastanów się nad kompromisami dotyczącymi używania wielu kluczy (klucza głównego kolumny lub kluczy szyfrowania kolumn). Zachowaj niewielką liczbę kluczy, aby zmniejszyć koszty zarządzania kluczami. Klucz główny kolumny i jeden klucz szyfrowania kolumn na bazę danych są zwykle wystarczające w środowiskach o stabilnych Stanach (nie w połowie rotacji kluczy). Dodatkowe klucze mogą być potrzebne, jeśli istnieją różne grupy użytkowników, z których każdy korzysta z różnych kluczy i uzyskuje dostęp do innych danych.  

- Obróć klucze główne kolumn zgodnie z wymaganiami dotyczącymi zgodności. Jeśli trzeba również obrócić klucze szyfrowania kolumn, należy rozważyć użycie szyfrowania online w celu zminimalizowania przestojów aplikacji.
  - Zapoznaj się z artykułem [zagadnienia dotyczące wydajności i dostępności](/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations).

- Użyj deterministycznego szyfrowania, jeśli obliczenia (równość) dla danych muszą być obsługiwane. W przeciwnym razie użyj losowego szyfrowania. Unikaj używania deterministycznych szyfrowania dla zestawów danych z niską entropią lub zestawów danych z publicznie znaną dystrybucją.

- Jeśli obawiasz się od podmiotów trzecich uzyskujących dostęp do danych bez zgody użytkownika, upewnij się, że wszystkie aplikacje i narzędzia, które mają dostęp do kluczy i danych w postaci zwykłego tekstu, działają poza chmurą Microsoft Azure. Bez dostępu do kluczy strona trzecia nie będzie miała żadnego sposobu odszyfrowania danych, chyba że zostaną pominięte.

- Always Encrypted nie obsługuje w łatwy sposób udzielania tymczasowego dostępu do kluczy (i chronionych danych). Na przykład, jeśli trzeba udostępnić klucze administratorowi usługi DBA, aby umożliwić ADMINISTRATORom przeprowadzanie niektórych operacji czyszczenia danych poufnych i szyfrowanych. Jedynym sposobem na niezawodność odwołania dostępu do danych z usługi DBA będzie obrócenie zarówno kluczy szyfrowania kolumn, jak i kluczy głównych kolumn chroniących dane, co jest kosztowną operacją.

- Aby uzyskać dostęp do wartości w postaci zwykłego tekstu w zaszyfrowanych kolumnach, użytkownik musi mieć dostęp do klucza głównego kolumny (CMK), który chroni kolumny, które są skonfigurowane w magazynie kluczy, w którym znajduje się CMK. Użytkownik musi również mieć zdefiniowaną **definicję klucza głównego kolumny widok** i wyświetlić wszystkie uprawnienia bazy danych **definicji klucza szyfrowania kolumn** .

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Kontrola dostępu użytkowników aplikacji do poufnych danych przy użyciu szyfrowania

Szyfrowanie może służyć do zapewnienia, że tylko określeni użytkownicy aplikacji, którzy mają dostęp do kluczy kryptograficznych, mogą wyświetlać lub aktualizować dane.

**Jak zaimplementować**:

- Użyj szyfrowania na poziomie komórki (CLE). Zapoznaj się z artykułem [Szyfruj kolumnę danych](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) , aby uzyskać szczegółowe informacje.
- Należy używać Always Encrypted, ale należy pamiętać o jego ograniczeniach. Ograniczenia są wymienione poniżej.

**Najlepsze praktyki**

W przypadku korzystania z CLE:

- Kontroluj dostęp do kluczy za poorednictwem uprawnień SQL i ról.

- Użyj algorytmu AES (zalecany algorytm AES 256) do szyfrowania danych. Algorytmy, takie jak RC4, DES i TripleDES, są przestarzałe i nie powinny być używane ze względu na znane luki w zabezpieczeniach.

- Aby uniknąć używania algorytmu 3DES, należy chronić klucze symetryczne za pomocą kluczy asymetrycznych/certyfikatów (nie hasła).

- Należy zachować ostrożność podczas migrowania bazy danych przy użyciu szyfrowania Cell-Level za pośrednictwem eksportu/importu (pliki BACPAC).
  - Zapoznaj się z artykułem [zalecenia dotyczące używania szyfrowania na poziomie komórki w Azure SQL Database](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database) na temat zapobiegania utracie kluczy podczas migrowania danych oraz innych wskazówek dotyczących najlepszych rozwiązań.

Należy pamiętać, że Always Encrypted jest przeznaczony głównie do ochrony poufnych danych używanych przez użytkowników z wysokim poziomem uprawnień Azure SQL Database (operatorzy chmury, przetwarzający) — zobacz [Ochrona poufnych danych używanych przez nieautoryzowanych użytkowników](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Należy pamiętać o następujących kwestiach dotyczących ochrony danych przed użytkownikami aplikacji przy użyciu Always Encrypted:

- Domyślnie wszystkie sterowniki klientów firmy Microsoft obsługujące Always Encrypted utrzymują globalną (jedną dla aplikacji) pamięć podręczną kluczy szyfrowania kolumn. Gdy sterownik klienta uzyskuje klucz szyfrowania kolumny w postaci zwykłego tekstu, kontaktując się z magazynem kluczy zawierającym klucz główny kolumny, klucz szyfrowania kolumny w postaci zwykłego tekstu jest buforowany. Dzięki temu izolowanie danych od użytkowników jest trudne. Jeśli aplikacja personifikuje użytkowników końcowych podczas korzystania z magazynu kluczy (takiego jak Azure Key Vault), po wykonaniu zapytania przez użytkownika w pamięci podręcznej przy użyciu klucza szyfrowania kolumny kolejne zapytanie, które wymaga tego samego klucza, ale jest wyzwalane przez innego użytkownika, będzie używać klucza buforowanego. Sterownik nie wywoła magazynu kluczy i nie sprawdza, czy drugi użytkownik ma uprawnienia dostępu do klucza szyfrowania kolumny. W efekcie użytkownik może zobaczyć zaszyfrowane dane, nawet jeśli użytkownik nie ma dostępu do kluczy. Aby zapewnić izolację użytkowników w aplikacji wieloużytkownika, można wyłączyć buforowanie kluczy szyfrowania kolumn. Wyłączenie buforowania spowoduje wystąpienie dodatkowych wydajności, ponieważ sterownik będzie musiał skontaktować się z magazynem kluczy dla każdej operacji szyfrowania lub odszyfrowywania danych.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Ochrona danych przed nieautoryzowanym wyświetlaniem przez użytkowników aplikacji podczas zachowywania formatu danych

Inna technika zapobiegania wyświetlaniu danych przez nieautoryzowanych użytkowników polega na zablokowaniu lub zablokowaniu danych przy zachowaniu typów i formatów danych w celu zapewnienia, że aplikacje użytkowników będą mogły nadal obsługiwać i wyświetlać dane.

**Jak zaimplementować**:

- Używaj [dynamicznego maskowania danych](/sql/relational-databases/security/dynamic-data-masking) do zasłaniania kolumn tabeli.

> [!NOTE]
> Always Encrypted nie działa z dynamiczną maską danych. Nie można zaszyfrować i zamaskować tej samej kolumny, co oznacza, że konieczne jest określenie priorytetów ochrony danych używanych w programie oraz maskowanie danych dla użytkowników aplikacji za pośrednictwem dynamicznego maskowania danych.

**Najlepsze rozwiązania**:

> [!NOTE]
> Dynamiczne maskowanie danych nie może służyć do ochrony danych przed użytkownikami z wysokim poziomem uprawnień. Zasady maskowania nie mają zastosowania do użytkowników z dostępem administracyjnym, takim jak db_owner.

- Nie Zezwalaj użytkownikom aplikacji na uruchamianie zapytań ad hoc (ponieważ mogą one być w stanie obejść dynamiczną maskowanie danych).  
  - Zapoznaj się z artykułem, [pomijając maskowanie przy użyciu technik wnioskowania lub pełnego wymuszania](/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) w celu uzyskania szczegółowych informacji.  

- Użyj odpowiednich zasad kontroli dostępu (za pośrednictwem uprawnień SQL, ról, z poziomu zabezpieczeń), aby ograniczyć uprawnienia użytkowników do wprowadzania aktualizacji w zamaskowanych kolumnach. Utworzenie maski w kolumnie nie zapobiega aktualizacji tej kolumny. Użytkownicy, którzy otrzymali maskowane dane podczas wykonywania zapytania dotyczącego kolumny maskowane, mogą aktualizować dane, jeśli mają uprawnienia do zapisu.

- Dynamiczne maskowanie danych nie zachowuje właściwości statystycznych maskowanych wartości. Może to mieć wpływ na wyniki zapytania (na przykład zapytania zawierające predykaty filtrowania lub sprzężenia w zamaskowanych danych).

## <a name="network-security"></a>Bezpieczeństwo sieci

Zabezpieczenia sieci odnoszą się do kontroli dostępu i najlepszych rozwiązań w celu zabezpieczania danych podczas przesyłania do Azure SQL Database.

### <a name="configure-my-client-to-connect-securely-to-sql-databasesql-managed-instance"></a>Konfigurowanie mojego klienta do bezpiecznego nawiązywania połączenia z wystąpieniem zarządzanym SQL Database/SQL

Najlepsze rozwiązania dotyczące zapobiegania wykorzystaniu przez komputery klienckie i aplikacje znanych luk w zabezpieczeniach (na przykład przy użyciu starszych protokołów TLS i mechanizmów szyfrowania) do łączenia się z usługą Azure SQL Database i wystąpieniem zarządzanym SQL.

**Jak zaimplementować**:

- Upewnij się, że komputery klienckie łączące się z usługą Azure SQL Database i wystąpieniem zarządzanym SQL używają  [Transport Layer Security (TLS)](security-overview.md#transport-layer-security-encryption-in-transit).

**Najlepsze rozwiązania**:

- Skonfiguruj wszystkie swoje aplikacje i narzędzia, aby połączyć się z SQL Database z włączonym szyfrowaniem
  - Szyfrowanie = włączone, TrustServerCertificate = wyłączone (lub równoważne z sterownikiem innym niż Microsoft).

- Jeśli aplikacja używa sterownika, który nie obsługuje protokołu TLS lub obsługuje starszą wersję protokołu TLS, należy zastąpić sterownik, jeśli jest to możliwe. Jeśli nie jest to możliwe, należy dokładnie oszacować zagrożenia dla bezpieczeństwa.

- Ogranicz wektory ataków poprzez luki w zabezpieczeniach protokołu SSL 2,0, SSL 3,0, TLS 1,0 i TLS 1,1, wyłączając je na komputerach klienckich łączących się z [ustawieniami rejestru Azure SQL Database na Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings#tls-10).

- Sprawdź mechanizmy szyfrowania dostępne na kliencie: [mechanizmy szyfrowania w protokole TLS/SSL (Dostawca SSP Schannel)](/windows/desktop/SecAuthN/cipher-suites-in-schannel). W [celu skonfigurowania kolejności mechanizmów szyfrowania TLS](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)należy wyłączyć algorytm 3DES.

- Dla Azure SQL Database i wystąpienia zarządzanego SQL szyfrowanie jest wymuszane zarówno dla typu połączenia serwera proxy, jak i przekierowania. W przypadku wystąpienia zarządzanego usługi Azure SQL należy użyć typu połączenia **serwera proxy** (domyślnie), ponieważ to wymusza szyfrowanie po stronie serwera. Typ połączenia **przekierowania** obecnie nie obsługuje wymuszania szyfrowania i jest dostępny tylko dla prywatnych połączeń IP.

- Aby uzyskać więcej informacji, zobacz [Azure SQL Database architektura łączności — zasady połączeń](connectivity-architecture.md#connection-policy).

### <a name="minimize-attack-surface"></a>Minimalizuj obszar ataków

Zminimalizuj liczbę funkcji, które mogą zostać zaatakowane przez złośliwego użytkownika. Implementowanie kontroli dostępu do sieci dla Azure SQL Database.

> Wymienione w: rozwiązanie OSA #5

**Jak zaimplementować**:

W SQL Database:

- Ustawienie Zezwalaj na dostęp do usług platformy Azure na poziomie serwera
- Używaj punktów końcowych usługi sieci wirtualnej i reguł zapory sieci wirtualnej.
- Użyj prywatnego linku (wersja zapoznawcza).

W wystąpieniu zarządzanym SQL:

- Postępuj zgodnie z zaleceniami [zawartymi w temacie wymagania sieciowe](../managed-instance/connectivity-architecture-overview.md#network-requirements).

**Najlepsze rozwiązania**:

- Ograniczanie dostępu do Azure SQL Database i wystąpienia zarządzanego SQL przez połączenie w prywatnym punkcie końcowym (na przykład przy użyciu prywatnej ścieżki danych):
  - Wystąpienie zarządzane może być izolowane wewnątrz sieci wirtualnej, aby uniemożliwić dostęp zewnętrzny. Aplikacje i narzędzia znajdujące się w tej samej lub równorzędnej sieci wirtualnej w tym samym regionie mogą uzyskać do nich bezpośredni dostęp. Aplikacje i narzędzia, które znajdują się w innym regionie, mogą korzystać z połączenia sieci wirtualnej lub komunikacji równorzędnej usługi ExpressRoute w celu nawiązania połączenia. Klient powinien używać sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia dostępu przez port 1433 tylko do zasobów, które wymagają dostępu do wystąpienia zarządzanego.
  - W przypadku SQL Database Użyj funkcji [link prywatny](../../private-link/private-endpoint-overview.md) , która zapewnia dedykowany prywatny adres IP dla serwera w sieci wirtualnej. Aby ograniczyć dostęp do serwerów, można także użyć [punktów końcowych usługi sieci wirtualnej z regułami zapory sieci wirtualnej](vnet-service-endpoint-rule-overview.md) .
  - Użytkownicy mobilni powinni użyć połączeń sieci VPN typu punkt-lokacja, aby nawiązać połączenie za pośrednictwem ścieżki danych.
  - Użytkownicy połączeni z siecią lokalną powinni używać połączenia sieci VPN typu lokacja-lokacja lub ExpressRoute w celu nawiązania połączenia za pośrednictwem ścieżki danych.

- Możesz uzyskać dostęp do Azure SQL Database i wystąpienia zarządzanego SQL, łącząc się z publicznym punktem końcowym (na przykład przy użyciu publicznej ścieżki danych). Należy wziąć pod uwagę następujące najlepsze rozwiązania:
  - W przypadku serwera w SQL Database Użyj [reguł zapory adresów IP](firewall-configure.md) , aby ograniczyć dostęp tylko do autoryzowanych adresów IP.
  - W przypadku wystąpienia zarządzanego SQL należy użyć sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia dostępu przez port 3342 tylko do wymaganych zasobów. Aby uzyskać więcej informacji, zobacz [bezpieczne używanie wystąpienia zarządzanego z publicznymi punktami końcowymi](../managed-instance/public-endpoint-overview.md).

> [!NOTE]
> Publiczny punkt końcowy wystąpienia zarządzanego SQL nie jest domyślnie włączony i musi być jawnie włączony. Jeśli zasady firmy nie zezwalają na korzystanie z publicznych punktów końcowych, należy użyć [Azure Policy](../../governance/policy/overview.md) , aby zapobiec włączaniu publicznych punktów końcowych w pierwszym miejscu.

- Skonfiguruj składniki sieci platformy Azure:
  - Postępuj zgodnie z [najlepszymi rozwiązaniami dotyczącymi zabezpieczeń sieci](../../security/fundamentals/network-best-practices.md).
  - Planowanie Virtual Network konfiguracji zgodnie z najlepszymi rozwiązaniami opisanymi na [platformie Azure Virtual Network często zadawanych pytań](../../virtual-network/virtual-networks-faq.md) i planowanie.
  - Utwórz segment sieci wirtualnej w wielu podsieciach i przypisz zasoby podobnej do tej samej podsieci (na przykład zasoby frontonu i zaplecza).
  - Używaj [sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../../virtual-network/network-security-groups-overview.md) do sterowania ruchem między podsieciami w ramach granicy sieci wirtualnej platformy Azure.
  - Włącz [usługę Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) dla subskrypcji, aby monitorować ruch sieciowy przychodzący i wychodzący.

### <a name="configure-power-bi-for-secure-connections-to-sql-databasesql-managed-instance"></a>Konfigurowanie Power BI bezpiecznych połączeń z wystąpieniem zarządzanym SQL Database/SQL

**Najlepsze rozwiązania**:

- W przypadku Power BI Desktop, jeśli to możliwe, użyj ścieżki danych prywatnych.

- Upewnij się, że Power BI Desktop nawiązuje połączenie przy użyciu protokołu TLS 1.2 przez ustawienie klucza rejestru na komputerze klienckim zgodnie z ustawieniami rejestru [Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings) .

- Ogranicz dostęp do danych dla określonych użytkowników przy użyciu [zabezpieczeń na poziomie wiersza z Power BI](/power-bi/service-admin-rls).

- W przypadku usługi Power BI należy użyć [lokalnej bramy danych](/power-bi/service-gateway-onprem), zachowując [ograniczenia i zagadnienia](/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-sql-databasesql-managed-instance"></a>Konfigurowanie App Service bezpiecznych połączeń z wystąpieniem zarządzanym SQL Database/SQL

**Najlepsze rozwiązania**:

- W przypadku prostej aplikacji sieci Web łączenie za pośrednictwem publicznego punktu końcowego wymaga ustawienia **Zezwalaj na używanie usług platformy Azure** .

- [Zintegruj swoją aplikację z usługą Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md) na potrzeby łączności ze ścieżką danych prywatnych z wystąpieniem zarządzanym. Opcjonalnie możesz również wdrożyć aplikację internetową za pomocą [środowisk App Service (ASE)](../../app-service/environment/intro.md).

- W przypadku aplikacji internetowej korzystającej z zintegrowanej aplikacji sieci Web platformy ASE lub sieci wirtualnej, która nawiązuje połączenie z bazą danych w SQL Database, można użyć [punktów końcowych usługi sieci wirtualnej i reguł zapory sieci wirtualnej](vnet-service-endpoint-rule-overview.md) , aby ograniczyć dostęp z określonej sieci wirtualnej i podsieci. Następnie ustaw opcję Zezwalaj na wyłączanie **usług platformy Azure** . Możesz również połączyć środowisko ASE z wystąpieniem zarządzanym w wystąpieniu zarządzanym SQL w ramach prywatnej ścieżki danych.  

- Upewnij się, że aplikacja sieci Web jest skonfigurowana zgodnie z artykułem, [najlepsze rozwiązania dotyczące zabezpieczania aplikacji sieci Web i usług platformy jako usługi (PaaS) przy użyciu Azure App Service](../../security/fundamentals/paas-applications-using-app-services.md).

- Zainstaluj [zaporę aplikacji sieci Web (WAF)](../../web-application-firewall/ag/ag-overview.md) , aby chronić aplikację sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach.

### <a name="configure-azure-virtual-machine-hosting-for-secure-connections-to-sql-databasesql-managed-instance"></a>Skonfiguruj Hosting maszyn wirtualnych platformy Azure pod kątem bezpiecznych połączeń z wystąpieniem zarządzanym SQL Database/SQL

**Najlepsze rozwiązania**:

- Użyj kombinacji reguł zezwalania i odmowy na sieciowych grup zabezpieczeń maszyn wirtualnych platformy Azure w celu kontrolowania regionów, do których można uzyskać dostęp z maszyny wirtualnej.

- Upewnij się, że maszyna wirtualna została skonfigurowana zgodnie z artykułem, [najlepsze rozwiązania w zakresie zabezpieczeń dotyczące obciążeń IaaS na platformie Azure](../../security/fundamentals/iaas.md).

- Upewnij się, że wszystkie maszyny wirtualne są skojarzone z określoną siecią wirtualną i podsiecią.

- Oceń, czy potrzebujesz domyślnej trasy 0.0.0.0/Internet zgodnie ze wskazówkami [dotyczącymi wymuszonego tunelowania](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling).
  - Jeśli tak — na przykład podsieć frontonu — a następnie Zachowaj trasę domyślną.
  - Jeśli nie — na przykład warstwa środkowa lub podsieć zaplecza — następnie Włącz tunelowanie wymuszone, tak aby żaden ruch nie przechodził przez Internet do lokalnego (a. k. w wielu lokalizacjach).

- Zaimplementuj [opcjonalne trasy domyślne](../../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) , jeśli używasz komunikacji równorzędnej lub łączenia się z lokalnymi.

- Zaimplementuj [trasy zdefiniowane przez użytkownika](../../virtual-network/virtual-networks-udr-overview.md#user-defined) , jeśli chcesz wysłać cały ruch w sieci wirtualnej do sieciowego urządzenia wirtualnego na potrzeby inspekcji pakietów.

- [Punkty końcowe usługi sieci wirtualnej](vnet-service-endpoint-rule-overview.md) umożliwiają bezpieczny dostęp do usług PaaS, takich jak usługa Azure Storage, za pośrednictwem sieci szkieletowej platformy Azure.

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Ochrona przed atakami rozproszonymi typu "odmowa usługi" (DDoS)

Ataki rozproszonego typu "odmowa usługi" (DDoS) są podejmowane przez złośliwego użytkownika w celu wysłania zalewania ruchu sieciowego do Azure SQL Database w celu przeprowadzenia przeciążenia infrastruktury platformy Azure i spowodowania odrzucenia prawidłowych logowań i obciążeń.

> Wymienione w: rozwiązanie OSA #9

**Jak zaimplementować**:

Ochrona DDoS jest automatycznie włączana w ramach platformy Azure. Obejmuje ona zawsze włączone monitorowanie ruchu i łagodzenie w czasie rzeczywistym ataków na poziomie sieci na publicznych punktach końcowych.

- Użyj [Azure DDoS Protection](../../virtual-network/ddos-protection-overview.md) , aby monitorować publiczne adresy IP skojarzone z zasobami wdrożonymi w sieciach wirtualnych.

- Użyj [zaawansowanej ochrony przed zagrożeniami dla Azure SQL Database](threat-detection-overview.md) , aby wykrywać ataki typu "odmowa usługi" (DOS) do baz danych.

**Najlepsze rozwiązania**:

- Postępuj zgodnie z zaleceniami opisanymi w [minimalizacji podatności na ataki](#minimize-attack-surface) , aby zminimalizować DDoS zagrożeń.

- Alert **SQL** Zaawansowana ochrona przed zagrożeniami pozwala wykrywać ataki z wykorzystaniem wymuszenia. W niektórych przypadkach alert może nawet odróżnić obciążenia testowania penetracji.

- W przypadku aplikacji do hostowania maszyn wirtualnych platformy Azure łączących się z SQL Database:
  - Postępuj zgodnie z zaleceniem, aby ograniczyć dostęp za poorednictwem punktów końcowych dostępnych z Internetu w Azure Security Center.
  - Za pomocą zestawów skalowania maszyn wirtualnych można uruchamiać wiele wystąpień aplikacji na maszynach wirtualnych platformy Azure.
  - Wyłącz protokół RDP i SSH z Internetu, aby zapobiec atakom typu "rozpuścić".

## <a name="monitoring-logging-and-auditing"></a>Monitorowanie, rejestrowanie i inspekcja

Ta sekcja dotyczy możliwości wykrywania nietypowych działań wskazujących nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Opisano w nim również najlepsze rozwiązania dotyczące konfigurowania inspekcji bazy danych w celu śledzenia i przechwytywania zdarzeń bazy danych.

### <a name="protect-databases-against-attacks"></a>Ochrona baz danych przed atakami

Zaawansowana ochrona przed zagrożeniami umożliwia wykrywanie potencjalnych zagrożeń i reagowanie na nie w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku nietypowych działań.

**Jak zaimplementować**:

- Użyj [zaawansowanej ochrony przed zagrożeniami dla programu SQL](threat-detection-overview.md#alerts) , aby wykrywać nietypowe i potencjalnie szkodliwe próby dostępu do baz danych lub ich wykorzystywania, w tym:
  - Atak iniekcji kodu SQL.
  - Kradzież/wyciek poświadczeń.
  - Nadużycie uprawnień.
  - Eksfiltracji danych.

**Najlepsze rozwiązania**:

- Skonfiguruj [usługę Azure Defender dla programu SQL](azure-defender-for-sql.md)   dla określonego serwera lub wystąpienia zarządzanego. Możesz również skonfigurować usługę Azure Defender for SQL dla wszystkich serwerów i wystąpień zarządzanych w ramach subskrypcji, przełączając się do [Azure Security Center warstwy Standardowa](../../security-center/security-center-pricing.md).

- Aby przeprowadzić pełne badanie, zaleca się włączenie [inspekcji SQL Database](../../azure-sql/database/auditing-overview.md). Za pomocą inspekcji można śledzić zdarzenia bazy danych i zapisywać je w dzienniku inspekcji na koncie usługi Azure Storage lub w obszarze roboczym usługi Azure Log Analytics.

### <a name="audit-critical-security-events"></a>Inspekcja krytycznych zdarzeń zabezpieczeń

Śledzenie zdarzeń bazy danych ułatwia zrozumienie działania bazy danych. Możesz uzyskać wgląd w niezgodności i anomalie, które mogą wskazywać na problemy biznesowe lub podejrzane naruszenia zabezpieczeń. Pozwala również na przestrzeganie standardów zgodności.

**Jak zaimplementować**:

- Włącz inspekcję [SQL Database](../../azure-sql/database/auditing-overview.md) lub [inspekcjonowanie wystąpienia zarządzanego](../managed-instance/auditing-configure.md) , aby śledzić zdarzenia bazy danych i zapisywać je w dzienniku inspekcji na koncie usługi Azure Storage, log Analytics obszarze roboczym (wersja zapoznawcza) lub Event Hubs (wersja zapoznawcza).

- Dzienniki inspekcji można zapisywać na koncie usługi Azure Storage, w obszarze roboczym Log Analytics do użycia przez dzienniki Azure Monitor lub do centrum zdarzeń w celu użycia przy użyciu centrum zdarzeń. Można skonfigurować dowolną kombinację tych opcji, a dzienniki inspekcji będą zapisywane w każdym z nich.

**Najlepsze rozwiązania**:

- Konfigurując [inspekcję SQL Database](../../azure-sql/database/auditing-overview.md) na serwerze lub [inspekcji wystąpienia zarządzanego](../managed-instance/auditing-configure.md) w celu inspekcji zdarzeń, wszystkie istniejące i nowo utworzone bazy danych na tym serwerze zostaną poddane inspekcji.
- Domyślnie zasady inspekcji obejmują wszystkie akcje (zapytania, procedury składowane oraz pomyślne i nieudane logowania) do baz danych, co może spowodować duże ilości dzienników inspekcji. Zalecane jest, aby klienci mogli [skonfigurować inspekcję dla różnych typów akcji i grup akcji przy użyciu programu PowerShell](./auditing-overview.md#manage-auditing). Skonfigurowanie tego elementu pomoże kontrolować liczbę działań poddawanych inspekcji i zminimalizować ryzyko utraty zdarzeń. Niestandardowe konfiguracje inspekcji umożliwiają klientom przechwytywanie tylko wymaganych danych inspekcji.
- Dzienniki inspekcji mogą być używane bezpośrednio w [Azure Portal](https://portal.azure.com/)lub z skonfigurowanej lokalizacji magazynu.

> [!NOTE]
> Włączenie inspekcji do Log Analytics będzie powodować naliczanie kosztów na podstawie stawek za pozyskiwanie. Zapoznaj się z powiązanym kosztem przy użyciu tej [opcji](https://azure.microsoft.com/pricing/details/monitor/)lub Rozważ przechowywanie dzienników inspekcji na koncie usługi Azure Storage.

**Dalsze zasoby**:

- [Inspekcja SQL Database](../../azure-sql/database/auditing-overview.md)
- [Inspekcja SQL Server](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="secure-audit-logs"></a>Bezpieczne dzienniki inspekcji

Ogranicz dostęp do konta magazynu w celu obsługi rozdzielenia obowiązków i oddzielenia baz danych od audytorów.

**Jak zaimplementować**:

- Podczas zapisywania dzienników inspekcji w usłudze Azure Storage upewnij się, że dostęp do konta magazynu jest ograniczony do minimalnych zasad zabezpieczeń. Kontroluj, kto ma dostęp do konta magazynu.
- Aby uzyskać więcej informacji, zobacz [autoryzowanie dostępu do usługi Azure Storage](../../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Najlepsze rozwiązania**:

- Kontrolowanie dostępu do celu inspekcji jest najważniejszym pojęciem oddzielenia DBA od audytorów.

- Podczas inspekcji dostępu do poufnych danych należy rozważyć zabezpieczenie danych za pomocą szyfrowania danych, aby uniknąć wycieku informacji do audytora. Aby uzyskać więcej informacji, zapoznaj się z sekcją [Ochrona poufnych danych używanych przez nieautoryzowanych użytkowników](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Zarządzanie zabezpieczeniami

W tej sekcji opisano różne aspekty i najlepsze rozwiązania dotyczące zarządzania bazami danych stan zabezpieczeń. Zawiera najlepsze rozwiązania dotyczące zapewniania, że bazy danych są skonfigurowane tak, aby spełniały standardy zabezpieczeń, do odnajdowania i klasyfikowania i śledzenia dostępu do potencjalnie poufnych danych w bazach danych.

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Upewnij się, że bazy danych są skonfigurowane pod kątem zgodności z najlepszymi rozwiązaniami w zakresie zabezpieczeń

Proaktywne ulepszanie zabezpieczeń bazy danych dzięki wykrywaniu i korygowaniem potencjalnych luk w zabezpieczeniach baz danych.

**Jak zaimplementować**:

- Włącz [ocenę luk](/sql/relational-databases/security/sql-vulnerability-assessment) w zabezpieczeniach SQL (VA), aby skanować bazę danych pod kątem problemów z zabezpieczeniami i automatycznie uruchamiać okresowo w bazach danych.

**Najlepsze rozwiązania**:

- Początkowo należy uruchomić VA w bazach danych i iterować przez korygowaniem testy niezakończone niepowodzeniem, które sprzeciwiają się najlepszym praktykom dotyczącym zabezpieczeń. Skonfiguruj linie bazowe dla akceptowalnych konfiguracji do momentu, aż skanowanie zakończy się, lub wszystkie testy przebiegły _prawidłowo_.  

- Skonfiguruj okresowe cykliczne skanowania do uruchomienia raz w tygodniu i skonfiguruj osobę, która będzie otrzymywać wiadomości e-mail z podsumowaniem.

- Zapoznaj się z podsumowaniem VA po każdym co tydzień. W przypadku jakichkolwiek luk w zabezpieczeniach Oceń odchylenie od poprzedniego wyniku skanowania i sprawdź, czy sprawdzanie powinno być rozwiązane. Sprawdź, czy istnieje uzasadniony powód zmiany konfiguracji.

- Rozpoznaj testy i linie bazowe aktualizacji, jeśli są odpowiednie. Twórz elementy biletów do rozwiązywania akcji i śledź je do momentu ich rozwiązania.

**Dalsze zasoby**:

- [Ocena luk w zabezpieczeniach SQL](/sql/relational-databases/security/sql-vulnerability-assessment)
- [Usługa oceny luk w zabezpieczeniach SQL ułatwia identyfikowanie luk w zabezpieczeniach bazy danych](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Zidentyfikuj i Oznacz poufne dane

Odnajdź kolumny, które potencjalnie zawierają poufne dane. To, co jest traktowane jako poufne dane, zależy od klienta, rozporządzenia zgodności itp., i musi być oceniane przez użytkowników odpowiedzialnych za te dane. Klasyfikowanie kolumn w celu korzystania z zaawansowanych scenariuszy inspekcji i ochrony opartej na czułości.

**Jak zaimplementować**:

- Używaj funkcji [odnajdywania i klasyfikowania danych SQL](data-discovery-and-classification-overview.md) , aby odnajdywać, klasyfikować, oznaczać i chronić poufne dane w bazach danych.
  - Zapoznaj się z zaleceniami klasyfikacji, które są tworzone przez zautomatyzowane odnajdywanie na pulpicie nawigacyjnym odnajdywanie i Klasyfikacja danych SQL. Zaakceptuj odpowiednie klasyfikacje, takie jak dane poufne są trwale oznakowane przy użyciu etykiet klasyfikacji.
  - Ręcznie Dodaj klasyfikacje dla wszelkich dodatkowych pól danych poufnych, które nie zostały odnalezione przez mechanizm zautomatyzowany.
- Aby uzyskać więcej informacji, zobacz [odnajdywanie i Klasyfikacja danych SQL](/sql/relational-databases/security/sql-data-discovery-and-classification).

**Najlepsze rozwiązania**:

- Regularnie Monitoruj pulpit nawigacyjny klasyfikacji pod kątem dokładnej oceny stanu klasyfikacji bazy danych. Raport o stanie klasyfikacji bazy danych można wyeksportować lub wydrukować, aby udostępnić go w celu zapewnienia zgodności i inspekcji.

- Stale monitoruj stan zalecanych danych poufnych w ocenie luk w zabezpieczeniach SQL. Śledź zasadę odnajdowania danych poufnych i zidentyfikuj wszelkie dryfy w zalecanych kolumnach dla klasyfikacji.  

- Użyj klasyfikacji w sposób dostosowany do konkretnych potrzeb organizacji. Dostosuj zasady Information Protection (etykiety czułości, typy informacji, logika odnajdywania) w zasadach [Information Protection SQL](../../security-center/security-center-info-protection-policy.md) w Azure Security Center.

### <a name="track-access-to-sensitive-data"></a>Śledź dostęp do poufnych danych

Monitoruj osoby, które uzyskują dostęp do danych poufnych i przechwytywania zapytań dotyczących poufnych danych w dziennikach inspekcji.

**Jak zaimplementować**:

- Należy użyć kombinacji usług SQL Audit i Data Classification.
  - W dzienniku [inspekcji SQL Database](../../azure-sql/database/auditing-overview.md) można śledzić dostęp do danych poufnych. Możesz również wyświetlić informacje, takie jak dane, do których uzyskano dostęp, a także etykietę czułości. Aby uzyskać więcej informacji, zobacz [odnajdywanie i klasyfikowanie danych](data-discovery-and-classification-overview.md) oraz [Inspekcja dostępu do poufnych danych](data-discovery-and-classification-overview.md#audit-sensitive-data).

**Najlepsze rozwiązania**:

- Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi sekcji Inspekcja i Klasyfikacja danych:
  - [Inspekcja krytycznych zdarzeń zabezpieczeń](#audit-critical-security-events)
  - [Zidentyfikuj i Oznacz poufne dane](#identify-and-tag-sensitive-data)

### <a name="visualize-security-and-compliance-status"></a>Wizualizowanie stanu zabezpieczeń i zgodności

Korzystaj z ujednoliconego systemu zarządzania zabezpieczeniami infrastruktury, który wzmacnia stan zabezpieczeń centrów danych (w tym bazy danych w SQL Database). Wyświetl listę zaleceń dotyczących bezpieczeństwa baz danych i stanu zgodności.

**Jak zaimplementować**:

- Monitoruj zalecenia dotyczące zabezpieczeń powiązane z programem SQL i aktywne zagrożenia w [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Typowe zagrożenia bezpieczeństwa i potencjalne środki zaradcze

Ta sekcja ułatwia znalezienie środków zabezpieczeń w celu ochrony przed niektórymi nosicielami ataków. Oczekuje się, że większość środków zaradczych można osiągnąć, wykonując jedną lub więcej z powyższych wytycznych dotyczących zabezpieczeń.

### <a name="security-threat-data-exfiltration"></a>Zagrożenie bezpieczeństwa: eksfiltracji danych

Data eksfiltracji to nieautoryzowane kopiowanie, transferowanie lub pobieranie danych z komputera lub serwera. Zobacz definicję dla [eksfiltracji danych](https://en.wikipedia.org/wiki/Data_exfiltration) w witrynie Wikipedia.

Połączenie z serwerem za pośrednictwem publicznego punktu końcowego przedstawia ryzyko eksfiltracji danych, ponieważ wymaga to od klientów otwarcia ich zapór na publicznych adresach IP.  

**Scenariusz 1**: aplikacja na maszynie wirtualnej platformy Azure nawiązuje połączenie z bazą danych w Azure SQL Database. Nieautoryzowany aktor uzyskuje dostęp do maszyny wirtualnej i naruszyć ją. W tym scenariuszu eksfiltracji danych oznacza, że zewnętrzna jednostka korzystająca z nieautoryzowanej maszyny wirtualnej nawiązuje połączenie z bazą danych, kopiuje dane osobowe i zapisuje je w magazynie obiektów blob lub innym SQL Database w innej subskrypcji.

**Scenariusz 2**: Rouge dba. Ten scenariusz jest często wywoływany przez klientów z uwzględnieniem zabezpieczeń z branż objętych regulacją. W tym scenariuszu użytkownik o wysokim poziomie uprawnień może skopiować dane z Azure SQL Database do innej subskrypcji niekontrolowanej przez właściciela danych.

**Potencjalne środki zaradcze**:

Obecnie Azure SQL Database i wystąpienie zarządzane SQL oferują następujące techniki ograniczania zagrożeń związanych z eksfiltracji danych:

- Użyj kombinacji reguł zezwalania i odmowy na sieciowych grup zabezpieczeń maszyn wirtualnych platformy Azure w celu kontrolowania regionów, do których można uzyskać dostęp z maszyny wirtualnej.
- Jeśli używasz serwera w SQL Database, ustaw następujące opcje:
  - Zezwalaj na wyłączenie usług platformy Azure.
  - Zezwalaj tylko na ruch z podsieci zawierającej maszynę wirtualną platformy Azure przez skonfigurowanie reguły zapory sieci wirtualnej.
  - Użyj [linku prywatnego](../../private-link/private-endpoint-overview.md)
- W przypadku wystąpienia zarządzanego przez program SQL Domyślnie jest używany prywatny dostęp do adresów IP w przypadku nieautoryzowanej maszyny wirtualnej eksfiltracji. Włącz funkcję delegowania podsieci w podsieci, aby automatycznie ustawiać najbardziej restrykcyjne zasady w podsieci wystąpienia zarządzanego SQL.
- Problem z nieautoryzowanym DBAm jest bardziej narażony na wystąpienie zarządzane SQL, ponieważ ma większy obszar powierzchni i wymagania dotyczące sieci są widoczne dla klientów. Najlepszym rozwiązaniem jest zastosowanie wszystkich praktyk w tym przewodniku zabezpieczeń, aby zapobiec nieautoryzowanemu scenariuszowi w pierwszym miejscu (nie tylko w przypadku eksfiltracji danych). Always Encrypted jest jedną z metod ochrony poufnych danych przez ich szyfrowanie i pozostawienie klucza niedostępnego dla administratorów usługi.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Aspekty zabezpieczeń dotyczące ciągłości działania i dostępności

Większość standardów zabezpieczeń zapewnia dostępność danych w kontekście ciągłości działania, osiągając implementację nadmiarowości i możliwości pracy awaryjnej w celu uniknięcia pojedynczych punktów awarii. W przypadku scenariuszy awaryjnych jest to typowa Metoda zachowywania kopii zapasowych plików danych i dziennika.Poniższa sekcja zawiera ogólne omówienie możliwości wbudowanych w platformę Azure. Dostępne są również dodatkowe opcje, które można skonfigurować w celu spełnienia określonych wymagań:

- Platforma Azure oferuje wbudowaną wysoką dostępność: [wysoką dostępność z użyciem SQL Database i wystąpienia zarządzanego SQL](high-availability-sla.md)

- Warstwa Krytyczne dla działania firmy obejmuje grupy trybu failover, pełne i różnicowe kopie zapasowe dzienników oraz kopie zapasowe dostępne w momencie przywracania:  
  - [Automatyczne kopie zapasowe](automated-backups-overview.md)
  - [Odzyskiwanie bazy danych przy użyciu zautomatyzowanych kopii zapasowych bazy danych — Przywracanie do punktu w czasie](recovery-using-backups.md#point-in-time-restore)

- Można skonfigurować dodatkowe funkcje zapewniania ciągłości biznesowej, takie jak strefa konfiguracji nadmiarowej i automatycznej pracy awaryjnej w różnych georegionyach platformy Azure: 
    - [Konfiguracja nadmiarowa stref o wysokiej dostępności dla warstw usług Krytyczne dla działania firmy & Premium](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)
    - [Nadmiarowa Konfiguracja stref o wysokiej dostępności dla Ogólnego przeznaczenia warstwy usług](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
    - [Przegląd ciągłości biznesowej](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj [się z omówieniem możliwości zabezpieczeń Azure SQL Database](security-overview.md)