---
title: Transparent Data Encryption
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Omówienie funkcji Transparent Data Encryption dla Azure SQL Database, Azure SQL Managed Instance i Azure Synapse Analytics. Dokument zawiera informacje o jego korzyściach i opcjach konfiguracji, w tym o zarządzanym przez usługę przezroczystym szyfrowaniu danych i Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: 160066f9599388256c7c821732a1e06fec49bdf5
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749046"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Transparent Data Encryption dla SQL Database, SQL Managed Instance i Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) pomaga chronić Azure SQL Database, Azure SQL Managed Instance i Azure Synapse Analytics przed zagrożeniami złośliwych działań w trybie offline przez szyfrowanie danych w spoczynku. Ta technologia w czasie rzeczywistym szyfruje i odszyfrowuje magazynowaną bazę danych, skojarzone kopie zapasowe i pliki dzienników transakcji bez konieczności dokonywania jakichkolwiek zmian w aplikacji. Domyślnie funkcja TDE jest włączona dla wszystkich nowo wdrożonych baz danych SQL i musi być ręcznie włączona dla starszych baz danych Azure SQL Database, Azure SQL Managed Instance. Funkcja TDE musi być ręcznie włączona dla Azure Synapse Analytics.

Szyfrowanie TDE wykonuje szyfrowanie i odszyfrowywanie we/wy w czasie rzeczywistym danych na poziomie strony. Każda strona jest odszyfrowywana podczas wczytywania do pamięci, a następnie szyfrowana przed zapisaniem na dysku. Szyfrowanie TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego nazywanego kluczem szyfrowania bazy danych (DEK, Database Encryption Key). Podczas uruchamiania bazy danych szyfrowany klucz szyfrowania danych jest odszyfrowywany, a następnie używany do odszyfrowywania i ponownego szyfrowania plików bazy danych w SQL Server aparatu bazy danych. DEK jest chroniony przez ochronę TDE. Ochrona TDE to certyfikat zarządzany przez usługę (transparent data encryption zarządzany przez usługę) lub klucz asymetryczny przechowywany w usłudze [Azure Key Vault](../../key-vault/general/security-overview.md) (transparent data encryption zarządzany przez klienta).

W Azure SQL Database i Azure Synapse funkcji ochrony TDE jest ustawiana [](logical-servers.md) na poziomie serwera i jest dziedziczona przez wszystkie bazy danych skojarzone z tym serwerem. W Azure SQL Managed Instance funkcji ochrony TDE jest ustawiana na poziomie wystąpienia i jest dziedziczona przez wszystkie zaszyfrowane bazy danych w tym wystąpieniu. Termin *serwer dotyczy* zarówno serwera, jak i wystąpienia w tym dokumencie, chyba że określono inaczej.

> [!IMPORTANT]
> Wszystkie nowo utworzone bazy danych w SQL Database są domyślnie szyfrowane przy użyciu funkcji Transparent Data Encryption zarządzanej przez usługę. Istniejące bazy danych SQL utworzone przed majem 2017 r. i bazy danych SQL utworzone za pomocą przywracania, replikacji geograficznej i kopiowania bazy danych nie są domyślnie szyfrowane. Istniejące SQL Managed Instance utworzone przed lutym 2019 r. nie są domyślnie szyfrowane. SQL Managed Instance baz danych utworzonych za pomocą przywracania dziedziczą stan szyfrowania ze źródła.

> [!NOTE]
> Szyfrowania TDE nie można używać do szyfrowania systemowych baz danych, takich jak baza **danych master,** w Azure SQL Database i Azure SQL Managed Instance. Baza **danych** master zawiera obiekty potrzebne do wykonywania operacji TDE na bazach danych użytkowników. Nie zaleca się przechowywania żadnych poufnych danych w systemowych bazach danych. [Obecnie jest](transparent-data-encryption-byok-overview.md#doubleencryption) wycofywowane szyfrowanie infrastruktury, które szyfruje systemowe bazy danych, w tym master. 

## <a name="service-managed-transparent-data-encryption"></a>Przezroczyste szyfrowanie danych zarządzane przez usługę

Na platformie Azure domyślnym ustawieniem dla opcji TDE jest ochrona DEK za pomocą wbudowanego certyfikatu serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera, a używany algorytm szyfrowania to AES 256. Jeśli baza danych znajduje się w relacji replikacji geograficznej, podstawowa i pomocnicza baza danych z replikacją geograficzną są chronione przez klucz serwera nadrzędnego podstawowej bazy danych. Jeśli dwie bazy danych są połączone z tym samym serwerem, współużytkują również ten sam wbudowany certyfikat. Firma Microsoft automatycznie obraca te certyfikaty zgodnie z wewnętrznymi zasadami zabezpieczeń, a klucz główny jest chroniony przez wewnętrzny magazyn kluczy tajnych firmy Microsoft. Klienci mogą zweryfikować SQL Database i SQL Managed Instance z zasadami zabezpieczeń wewnętrznych w raportach inspekcji niezależnych firm zewnętrznych dostępnych w [Centrum zaufania Firmy Microsoft.](https://servicetrust.microsoft.com/)

Firma Microsoft bezproblemowo przenosi również klucze i zarządza nimi w razie potrzeby replikacji geograficznej i przywracania.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Przezroczyste szyfrowanie danych zarządzane przez klienta — Bring Your Own Key

Zarządzanie TDE przez klienta jest również określane jako obsługa Bring Your Own Key (BYOK) dla TDE. W tym scenariuszu funkcji ochrony TDE, która szyfruje klucz szyfrowania kluczy, jest zarządzany przez klienta klucz asymetryczny, który jest przechowywany w magazynie kluczy należącym do klienta i zarządzanym przez klienta Azure Key Vault (zewnętrznym systemie zarządzania kluczami opartym na chmurze platformy Azure) i nigdy nie opuszcza magazynu kluczy. Funkcje ochrony TDE mogą [być](../../key-vault/keys/hsm-protected-keys.md) generowane przez magazyn kluczy lub przenoszone do magazynu kluczy z lokalnego urządzenia sprzętowego modułu zabezpieczeń (HSM). SQL Database, SQL Managed Instance i Azure Synapse należy przyznać uprawnienia do magazynu kluczy należącego do klienta w celu odszyfrowania i zaszyfrowania klucza szyfrowania danych. Jeśli uprawnienia serwera do magazynu kluczy zostaną odwołane, baza danych będzie niedostępna, a wszystkie dane będą szyfrowane

Dzięki funkcji TDE z integracją Azure Key Vault użytkownicy mogą kontrolować zadania zarządzania kluczami, w tym rotacje kluczy, uprawnienia magazynu kluczy, kopie zapasowe kluczy i włączyć inspekcję/raportowanie dla wszystkich funkcji ochrony TDE przy użyciu Azure Key Vault funkcji. Key Vault centralne zarządzanie kluczami, wykorzystuje ściśle monitorowane moduły HSM i umożliwia rozdzielenie obowiązków między zarządzanie kluczami i danymi w celu zapewnienia zgodności z zasadami zabezpieczeń.
Aby dowiedzieć się więcej na temat usług BYOK Azure SQL Database i Azure Synapse, zobacz Transparent data encryption with Azure Key Vault integration (Przezroczyste szyfrowanie danych Azure Key Vault [integracji).](transparent-data-encryption-byok-overview.md)

Aby rozpocząć korzystanie z funkcji TDE Azure Key Vault integracji, zapoznaj się z przewodnikiem Włączanie przezroczystego szyfrowania danych przy użyciu własnego klucza z [Key Vault](transparent-data-encryption-byok-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Przenoszenie bazy danych chronionej za pomocą technologii Transparent Data Encryption

Nie musisz odszyfrowywać baz danych dla operacji na platformie Azure. Ustawienia TDE źródłowej bazy danych lub podstawowej bazy danych są w sposób niewidoczny dziedziczone w docelowej bazie danych. Uwzględnione operacje obejmują:

- Przywracanie geograficzne
- Samoobsługowe przywracanie do punktu w czasie
- Przywracanie usuniętej bazy danych
- Aktywna replikacja geograficzna
- Tworzenie kopii bazy danych
- Przywracanie pliku kopii zapasowej do Azure SQL Managed Instance

> [!IMPORTANT]
> Ręczne tworzenie kopii zapasowej bazy danych zaszyfrowanej za pomocą technologii TDE zarządzanej przez usługę nie jest obsługiwane w usłudze Azure SQL Managed Instance, ponieważ certyfikat używany do szyfrowania nie jest dostępny. Użyj funkcji przywracania do punktu w czasie, aby przenieść ten typ bazy danych do innej SQL Managed Instance lub przełączyć się na klucz zarządzany przez klienta.

Podczas eksportowania bazy danych chronionej przez szyfrowanie TDE wyeksportowana zawartość bazy danych nie jest szyfrowana. Wyeksportowana zawartość jest przechowywana w niezaszyfrowanych plikach BACPAC. Pamiętaj, aby odpowiednio chronić pliki BACPAC i włączyć funkcję TDE po zakończeniu importowania nowej bazy danych.

Jeśli na przykład plik BACPAC jest eksportowany z SQL Server, zaimportowana zawartość nowej bazy danych nie jest automatycznie szyfrowana. Podobnie jeśli plik BACPAC zostanie zaimportowany do SQL Server, nowa baza danych również nie zostanie automatycznie zaszyfrowana.

Jedynym wyjątkiem jest eksportowanie bazy danych do i z SQL Database. Funkcja TDE jest włączona w nowej bazie danych, ale sam plik BACPAC nadal nie jest szyfrowany.

## <a name="manage-transparent-data-encryption"></a>Zarządzanie przezroczystym szyfrowaniem danych

# <a name="the-azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

Zarządzanie TDE w Azure Portal.

Aby skonfigurować usługę TDE za pośrednictwem Azure Portal, musisz mieć połączenie jako właściciel, współautor lub menedżer zabezpieczeń SQL platformy Azure.

Włącz i wyłącz funkcję TDE na poziomie bazy danych. Na Azure SQL Managed Instance języka Transact-SQL (T-SQL), aby włączyć i wyłączyć program TDE w bazie danych. W Azure SQL Database i Azure Synapse można zarządzać TDE dla bazy danych w usłudze [Azure Portal](https://portal.azure.com) po zalogowaniu się przy użyciu konta administratora lub współautora platformy Azure. Znajdź ustawienia TDE w bazie danych użytkownika. Domyślnie jest używane przezroczyste szyfrowanie danych zarządzane przez usługę. Certyfikat TDE jest generowany automatycznie dla serwera, który zawiera bazę danych.

![Przezroczyste szyfrowanie danych zarządzane przez usługę](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

Klucz główny TDE, znany jako ochrony TDE, należy ustawić na poziomie serwera lub wystąpienia. Aby korzystać z funkcji TDE z obsługą funkcji BYOK i chronić bazy danych przy użyciu klucza z Key Vault, otwórz ustawienia funkcji TDE na serwerze.

![Przezroczyste szyfrowanie danych z obsługą Bring Your Own Key danych](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Zarządzanie TDE przy użyciu programu PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł poleceń Azure Resource Manager PowerShell jest nadal obsługiwany, ale cały przyszły rozwój jest dla modułu Az.Sql. Aby uzyskać informacje na temat tych poleceń cmdlet, [zobacz AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenty poleceń w module Az i modułach AzureRm są w znacznym stopniu identyczne.

Aby skonfigurować usługę TDE za pomocą programu PowerShell, musisz mieć połączenie jako właściciel, współautor platformy Azure lub menedżer zabezpieczeń SQL.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Polecenia cmdlet dla Azure SQL Database i Azure Synapse

Użyj następujących polecenia cmdlet dla Azure SQL Database i Azure Synapse:

| Polecenie cmdlet | Opis |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Włącza lub wyłącza funkcję Transparent Data Encryption dla bazy danych.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Pobiera stan przezroczystego szyfrowania danych dla bazy danych. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Sprawdza postęp szyfrowania bazy danych. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Dodaje Key Vault klucza do serwera. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Pobiera klucze Key Vault serwera  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Ustawia funkcji ochrony transparent data encryption dla serwera. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Pobiera ochrony przezroczystego szyfrowania danych |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Usuwa klucz Key Vault z serwera. |
|  | |

> [!IMPORTANT]
> Aby Azure SQL Managed Instance, użyj polecenia ALTER [DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) języka T-SQL, aby włączyć i wyłączyć usługę TDE na poziomie bazy danych, a następnie sprawdź przykładowy skrypt programu [PowerShell](transparent-data-encryption-byok-configure.md) do zarządzania TDE na poziomie wystąpienia.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Zarządzanie TDE przy użyciu języka Transact-SQL.

Połącz się z bazą danych przy użyciu identyfikatora logowania, który jest administratorem lub członkiem roli **dbmanager** w bazie danych master.

| Polecenie | Opis |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | USTAWIENIE SZYFROWANIA WŁ./WYŁ. szyfruje lub odszyfrowuje bazę danych |
| [Sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Zwraca informacje o stanie szyfrowania bazy danych i skojarzonych z nią kluczach szyfrowania bazy danych |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Zwraca informacje o stanie szyfrowania każdego węzła Azure Synapse i skojarzonych z nim kluczy szyfrowania bazy danych |
|  | |

Nie można przełączyć ochrony TDE na klucz z Key Vault za pomocą języka Transact-SQL. Użyj programu PowerShell lub Azure Portal.

# <a name="rest-api"></a>[Interfejs API REST](#tab/azure-RESTAPI)

Zarządzanie TDE przy użyciu interfejsu API REST.

Aby skonfigurować usługę TDE za pomocą interfejsu API REST, musisz mieć połączenie jako właściciel, współautor platformy Azure lub menedżer zabezpieczeń SQL.
Użyj następującego zestawu poleceń dla Azure SQL Database i Azure Synapse:

| Polecenie | Opis |
| --- | --- |
|[Tworzenie lub aktualizowanie serwera](/rest/api/sql/servers/createorupdate)|Dodaje Azure Active Directory do serwera. (służy do udzielania dostępu do Key Vault)|
|[Tworzenie lub aktualizowanie klucza serwera](/rest/api/sql/serverkeys/createorupdate)|Dodaje Key Vault klucza do serwera.|
|[Usuwanie klucza serwera](/rest/api/sql/serverkeys/delete)|Usuwa klucz Key Vault z serwera. |
|[Uzyskiwanie kluczy serwera](/rest/api/sql/serverkeys/get)|Pobiera określony klucz Key Vault z serwera.|
|[Lista kluczy serwera według serwera](/rest/api/sql/serverkeys/listbyserver)|Pobiera klucze Key Vault serwera. |
|[Tworzenie lub aktualizowanie funkcji ochrony szyfrowania](/rest/api/sql/encryptionprotectors/createorupdate)|Ustawia ochrony TDE dla serwera.|
|[Uzyskiwanie funkcji ochrony szyfrowania](/rest/api/sql/encryptionprotectors/get)|Pobiera ochrony TDE dla serwera.|
|[Lista funkcji ochrony szyfrowania według serwera](/rest/api/sql/encryptionprotectors/listbyserver)|Pobiera funkcji ochrony TDE dla serwera. |
|[Tworzenie lub aktualizowanie Transparent Data Encryption konfiguracji](/rest/api/sql/transparentdataencryptions/createorupdate)|Włącza lub wyłącza funkcję TDE dla bazy danych.|
|[Uzyskiwanie Transparent Data Encryption konfiguracji](/rest/api/sql/transparentdataencryptions/get)|Pobiera konfigurację TDE dla bazy danych.|
|[Lista Transparent Data Encryption wyników konfiguracji](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Pobiera wynik szyfrowania dla bazy danych.|

## <a name="see-also"></a>Zobacz też

- SQL Server uruchomione na maszynie wirtualnej platformy Azure mogą również używać klucza asymetrycznego z Key Vault. Kroki konfiguracji różnią się od używania klucza asymetrycznego w SQL Database i SQL Managed Instance. Aby uzyskać więcej informacji, zobacz [Extensible key management by using Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)(Rozszerzalne zarządzanie kluczami przy użyciu SQL Server).
- Aby uzyskać ogólny opis technologii TDE, zobacz [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption).
- Aby dowiedzieć się więcej o technologii TDE z obsługą byOK dla Azure SQL Database, Azure SQL Managed Instance i Azure Synapse, zobacz Transparent data encryption with Bring Your Own Key support (Przezroczyste szyfrowanie danych z obsługą [Bring Your Own Key).](transparent-data-encryption-byok-overview.md)
- Aby rozpocząć korzystanie z funkcji TDE z obsługą Bring Your Own Key, zobacz przewodnik z wytycznymi Włączanie funkcji [Transparent Data Encryption](transparent-data-encryption-byok-configure.md)przy użyciu własnego klucza z Key Vault .
- Aby uzyskać więcej informacji na Key Vault, zobacz [Bezpieczny dostęp do magazynu kluczy](../../key-vault/general/security-overview.md).