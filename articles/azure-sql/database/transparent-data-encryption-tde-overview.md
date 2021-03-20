---
title: Transparent Data Encryption
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Omówienie funkcji przezroczystego szyfrowania danych dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse Analytics. Ten dokument zawiera zalety i opcje konfiguracji, w tym zarządzane Bring Your Own Key i niejawne szyfrowanie danych przez usługę.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: 8fbbd7a2aabc9de417f1eefd2513edba3119bfc0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92791396"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Przezroczyste szyfrowanie danych dla SQL Database, wystąpienia zarządzanego SQL i usługi Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) pomaga chronić Azure SQL Database, wystąpienie zarządzane usługi Azure SQL i usługę Azure Synapse Analytics przed zagrożeniem złośliwego działania w trybie offline przez szyfrowanie danych przechowywanych w spoczynku. Ta technologia w czasie rzeczywistym szyfruje i odszyfrowuje magazynowaną bazę danych, skojarzone kopie zapasowe i pliki dzienników transakcji bez konieczności dokonywania jakichkolwiek zmian w aplikacji. Domyślnie program TDE jest włączony dla wszystkich nowo wdrożonych baz danych SQL i musi być ręcznie włączony dla starszych baz danych Azure SQL Database, wystąpienia zarządzanego Azure SQL. TDE dla usługi Azure Synapse Analytics musi być włączona ręcznie.

TDE wykonuje szyfrowanie we/wy czasu rzeczywistego i odszyfrowywanie danych na poziomie strony. Każda strona jest odszyfrowywana podczas wczytywania do pamięci, a następnie szyfrowana przed zapisaniem na dysku. TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego zwanego kluczem szyfrowania bazy danych (. W przypadku uruchamiania bazy danych zaszyfrowane szyfrowanie szyfrowania plików jest odszyfrowywane, a następnie używane do odszyfrowywania i ponownego szyfrowania pliki bazy danych w procesie aparatu bazy danych SQL Server. Program szyfrowania danych jest chroniony przez funkcję ochrony TDE. TDE funkcja ochrony jest certyfikatem zarządzanym przez usługę (niejawnym szyfrowaniem danych zarządzanym przez usługę) lub kluczem asymetrycznym przechowywanym w [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md) (nieprzezroczyste szyfrowanie danych zarządzane przez klienta).

W przypadku Azure SQL Database i usługi Azure Synapse funkcja ochrony TDE jest ustawiana na poziomie [serwera](logical-servers.md) i jest dziedziczona przez wszystkie bazy danych skojarzone z tym serwerem. W przypadku wystąpienia zarządzanego Azure SQL funkcja ochrony TDE jest ustawiana na poziomie wystąpienia i jest dziedziczona przez wszystkie zaszyfrowane bazy danych w tym wystąpieniu. Termin " *serwer* " dotyczy zarówno serwera, jak i wystąpienia w tym dokumencie, chyba że określono inaczej.

> [!IMPORTANT]
> Wszystkie nowo utworzone bazy danych w SQL Database są domyślnie szyfrowane przy użyciu funkcji przezroczystego szyfrowania danych zarządzanego przez usługę. Istniejące bazy danych SQL utworzone przed 2017mi i bazami danych SQL utworzonych przy użyciu funkcji przywracania, replikacji geograficznej i kopii bazy danych nie są szyfrowane domyślnie. Istniejące bazy danych wystąpienia zarządzanego SQL utworzone przed 2019 lutego nie są szyfrowane domyślnie. Bazy danych wystąpienia zarządzanego SQL utworzone za pośrednictwem przywracania dziedziczą stan szyfrowania ze źródła.

> [!NOTE]
> TDE nie można używać do szyfrowania systemowych baz danych, takich jak baza danych **Master** , w Azure SQL Database i wystąpienia zarządzanego Azure SQL. Baza danych **Master** zawiera obiekty, które są konieczne do wykonywania operacji TDE w bazach danych użytkowników. Zaleca się, aby nie przechowywać poufnych danych w systemowych bazach danych. Obecnie trwa wdrażanie [infrastruktury](transparent-data-encryption-byok-overview.md#doubleencryption) , która szyfruje systemową bazę danych, łącznie z serwerem głównym. 

## <a name="service-managed-transparent-data-encryption"></a>Zarządzane szyfrowanie danych przez usługę

Na platformie Azure domyślne ustawienie dla TDE polega na tym, że klucz szyfrowania danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera, a używany algorytm szyfrowania to AES 256. Jeśli baza danych znajduje się w relacji replikacji geograficznej, zarówno podstawowa, jak i pomocnicza baza danych są chronione kluczem nadrzędnego serwera bazy danych. Jeśli dwie bazy danych są połączone z tym samym serwerem, współużytkują także ten sam certyfikat wbudowany. Firma Microsoft automatycznie obraca te certyfikaty zgodnie z wewnętrznymi zasadami zabezpieczeń, a klucz główny jest chroniony przez wewnętrzny magazyn tajny firmy Microsoft. Klienci mogą weryfikować, SQL Database i zgodność z wystąpieniem zarządzanym SQL, z wewnętrznymi zasadami zabezpieczeń w niezależnych raportach inspekcji innych firm, które są dostępne w [Centrum zaufania firmy Microsoft](https://servicetrust.microsoft.com/).

Firma Microsoft bezproblemowo przenosi klucze i zarządza nimi w zależności od potrzeby replikacji geograficznej i przywracania.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Szyfrowanie danych przezroczystych zarządzanych przez klienta — Bring Your Own Key

TDE zarządzane przez klienta jest również określane jako obsługa Bring Your Own Key (BYOK) dla TDE. W tym scenariuszu funkcja ochrony TDE, która szyfruje plik szyfrowania danych, jest kluczem asymetrycznym zarządzanym przez klienta, który jest przechowywany w Azure Key Vault należącym do klienta i zarządzanym (zewnętrznym systemie zarządzania kluczami opartymi na chmurze platformy Azure) i nigdy nie opuszcza magazynu kluczy. Funkcja ochrony TDE może być [generowana przez Magazyn kluczy lub transferowana do magazynu kluczy](../../key-vault/keys/hsm-protected-keys.md) z urządzenia lokalnego modułu zabezpieczeń sprzętu (HSM). SQL Database, wystąpienie zarządzane SQL i usługa Azure Synapse muszą mieć przyznane uprawnienia do magazynu kluczy należącego do klienta w celu odszyfrowania i zaszyfrowania klucza szyfrowania danych. Jeśli uprawnienia serwera do magazynu kluczy zostaną odwołane, baza danych będzie niedostępna, a wszystkie dane są szyfrowane

Dzięki funkcji TDE z integracją Azure Key Vault użytkownicy mogą kontrolować zadania zarządzania kluczami, w tym kluczowe rotacje, uprawnienia do magazynu kluczy, kopie zapasowe kluczy, a także włączać inspekcje/raportowanie we wszystkich funkcjach Azure Key Vault ochrony TDE. Key Vault udostępnia centralne zarządzanie kluczami, wykorzystuje ściśle monitorowane sprzętowych modułów zabezpieczeń i umożliwia rozdzielenie obowiązków między zarządzaniem kluczami i danymi, aby zapewnić zgodność z zasadami zabezpieczeń.
Aby dowiedzieć się więcej na temat BYOK for Azure SQL Database i Azure Synapse, zobacz [przezroczyste szyfrowanie danych z integracją Azure Key Vault](transparent-data-encryption-byok-overview.md).

Aby rozpocząć korzystanie z usługi TDE z integracją Azure Key Vault, zapoznaj się z tematem jak [włączyć funkcję przezroczystego szyfrowania danych przy użyciu własnego klucza z Key Vault](transparent-data-encryption-byok-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Przenoszenie przezroczystej bazy danych chronionej przez szyfrowanie danych

Nie trzeba odszyfrowywać baz danych dla operacji na platformie Azure. Ustawienia TDE w źródłowej bazie danych lub podstawowej bazie danych są dziedziczone w sposób przezroczysty w miejscu docelowym. Uwzględnione operacje obejmują:

- Przywracanie geograficzne
- Przywracanie do punktu w czasie samoobsługowym
- Przywracanie usuniętej bazy danych
- Aktywna replikacja geograficzna
- Tworzenie kopii bazy danych
- Przywracanie pliku kopii zapasowej do wystąpienia zarządzanego usługi Azure SQL

> [!IMPORTANT]
> Pobieranie ręcznie kopii zapasowej bazy danych zaszyfrowanej przez TDE zarządzane przez usługę nie jest obsługiwane w wystąpieniu zarządzanym usługi Azure SQL, ponieważ certyfikat używany do szyfrowania jest niedostępny. Funkcja przywracania do punktu w czasie służy do przenoszenia tego typu bazy danych do innego wystąpienia zarządzanego SQL lub do przełączania się na klucz zarządzany przez klienta.

Podczas eksportowania bazy danych chronionej przez TDE wyeksportowana zawartość bazy danych nie jest zaszyfrowana. Ta wyeksportowana zawartość jest przechowywana w niezaszyfrowanych plikach BACPAC. Pamiętaj, aby odpowiednio chronić pliki BACPAC i włączyć TDE po zakończeniu importowania nowej bazy danych.

Na przykład jeśli plik BACPAC zostanie wyeksportowany z wystąpienia SQL Server, zaimportowana zawartość nowej bazy danych nie zostanie automatycznie zaszyfrowana. Podobnie, jeśli plik BACPAC zostanie zaimportowany do wystąpienia SQL Server, Nowa baza danych również nie będzie automatycznie szyfrowana.

Jedynym wyjątkiem jest wyeksportowanie bazy danych do i z SQL Database. TDE jest włączona w nowej bazie danych, ale sam plik BACPAC nadal nie jest szyfrowany.

## <a name="manage-transparent-data-encryption"></a>Zarządzanie przezroczystym szyfrowaniem danych

# <a name="the-azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

Zarządzanie TDEami w Azure Portal.

Aby skonfigurować TDE za pomocą Azure Portal, musisz mieć połączenie z właścicielem, współautorem platformy Azure lub programem SQL Security Manager.

Włącz i Wyłącz TDE na poziomie bazy danych. W przypadku wystąpienia zarządzanego Azure SQL Użyj języka Transact-SQL (T-SQL), aby włączyć i wyłączyć TDE w bazie danych. W przypadku Azure SQL Database i usługi Azure Synapse można zarządzać TDE bazy danych w [Azure Portal](https://portal.azure.com) po zalogowaniu się przy użyciu konta administratora lub współautora platformy Azure. Znajdź ustawienia TDE w bazie danych użytkownika. Domyślnie jest używane zarządzane szyfrowanie danych w ramach usługi. Certyfikat TDE jest generowany automatycznie dla serwera, który zawiera bazę danych.

![Zarządzane szyfrowanie danych przez usługę](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

Należy ustawić klucz główny TDE, znany jako funkcja ochrony TDE na poziomie serwera lub wystąpienia. Aby użyć usługi TDE z obsługą BYOK i chronić bazy danych za pomocą klucza z Key Vault, Otwórz ustawienia TDE na serwerze.

![Niewidoczne szyfrowanie danych z obsługą Bring Your Own Key](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Zarządzanie TDE przy użyciu programu PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Aby skonfigurować TDE za pomocą programu PowerShell, musisz mieć połączenie jako właściciel, współautor lub program SQL Security Manager platformy Azure.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Polecenia cmdlet dla Azure SQL Database i platformy Azure Synapse

Użyj następujących poleceń cmdlet dla Azure SQL Database i usługi Azure Synapse:

| Polecenie cmdlet | Opis |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Włącza lub wyłącza przezroczyste szyfrowanie danych dla bazy danych.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Pobiera stan przezroczystego szyfrowania danych dla bazy danych. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Sprawdza postęp szyfrowania bazy danych. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Dodaje klucz Key Vault do serwera. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Pobiera klucze Key Vault dla serwera  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Ustawia funkcję ochrony przezroczystego szyfrowania danych dla serwera. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Pobiera funkcję ochrony przezroczystego szyfrowania danych |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Usuwa klucz Key Vault z serwera. |
|  | |

> [!IMPORTANT]
> W przypadku wystąpienia zarządzanego Azure SQL użyj polecenia [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) języka T-SQL, aby włączyć i wyłączyć TDE na poziomie bazy danych, i sprawdź [przykładowy skrypt programu PowerShell](transparent-data-encryption-byok-configure.md) , aby zarządzać TDE na poziomie wystąpienia.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Zarządzanie TDE przy użyciu języka Transact-SQL.

Nawiąż połączenie z bazą danych za pomocą nazwy logowania, która jest administratorem lub członkiem roli **DBManager** w bazie danych Master.

| Polecenie | Opis |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | USTAWIENIE Włącz/wyłącz szyfrowanie powoduje szyfrowanie lub odszyfrowanie bazy danych |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Zwraca informacje o stanie szyfrowania bazy danych i skojarzonych z nimi kluczy szyfrowania bazy danych |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Zwraca informacje o stanie szyfrowania poszczególnych węzłów usługi Azure Synapse i skojarzonych z nimi kluczy szyfrowania bazy danych |
|  | |

Nie można przełączyć funkcji ochrony TDE na klucz z Key Vault przy użyciu języka Transact-SQL. Użyj programu PowerShell lub Azure Portal.

# <a name="rest-api"></a>[Interfejs API REST](#tab/azure-RESTAPI)

Zarządzanie TDE za pomocą interfejsu API REST.

Aby skonfigurować TDE za pomocą interfejsu API REST, należy nawiązać połączenie jako właściciel, współautor lub program SQL Security Manager platformy Azure.
Użyj następującego zestawu poleceń dla Azure SQL Database i usługi Azure Synapse:

| Polecenie | Opis |
| --- | --- |
|[Utwórz lub zaktualizuj serwer](/rest/api/sql/servers/createorupdate)|Dodaje Azure Active Directory tożsamość do serwera. (używane do udzielania dostępu do Key Vault)|
|[Utwórz lub zaktualizuj klucz serwera](/rest/api/sql/serverkeys/createorupdate)|Dodaje klucz Key Vault do serwera.|
|[Usuń klucz serwera](/rest/api/sql/serverkeys/delete)|Usuwa klucz Key Vault z serwera. |
|[Pobierz klucze serwera](/rest/api/sql/serverkeys/get)|Pobiera określony klucz Key Vault z serwera.|
|[Wyświetl listę kluczy serwera według serwera](/rest/api/sql/serverkeys/listbyserver)|Pobiera klucze Key Vault dla serwera. |
|[Utwórz lub zaktualizuj ochronę szyfrowania](/rest/api/sql/encryptionprotectors/createorupdate)|Ustawia funkcję ochrony TDE dla serwera.|
|[Pobierz ochronę szyfrowania](/rest/api/sql/encryptionprotectors/get)|Pobiera funkcję ochrony TDE dla serwera.|
|[Wyświetlanie listy funkcji ochrony przed szyfrowaniem według serwera](/rest/api/sql/encryptionprotectors/listbyserver)|Pobiera funkcje ochrony TDE dla serwera. |
|[Utwórz lub zaktualizuj konfigurację Transparent Data Encryption](/rest/api/sql/transparentdataencryptions/createorupdate)|Włącza lub wyłącza TDE dla bazy danych.|
|[Pobierz konfigurację Transparent Data Encryption](/rest/api/sql/transparentdataencryptions/get)|Pobiera konfigurację TDE dla bazy danych.|
|[Lista wyników konfiguracji Transparent Data Encryption](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Pobiera wynik szyfrowania dla bazy danych.|

## <a name="see-also"></a>Zobacz też

- SQL Server uruchomione na maszynie wirtualnej platformy Azure mogą również używać klucza asymetrycznego z Key Vault. Czynności konfiguracyjne różnią się od używania klucza asymetrycznego w SQL Database i wystąpieniu zarządzanym SQL. Aby uzyskać więcej informacji, zobacz [rozszerzalne zarządzanie kluczami za pomocą Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Aby uzyskać ogólny opis TDE, zobacz [przezroczyste szyfrowanie danych](/sql/relational-databases/security/encryption/transparent-data-encryption).
- Aby dowiedzieć się więcej na temat TDE z obsługą BYOK dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse, zobacz [przezroczyste szyfrowanie danych z obsługą Bring Your Own Key](transparent-data-encryption-byok-overview.md).
- Aby rozpocząć korzystanie z usługi TDE z obsługą Bring Your Own Key, zobacz Przewodnik po tym, jak [włączyć funkcję przezroczystego szyfrowania danych przy użyciu własnego klucza z Key Vault](transparent-data-encryption-byok-configure.md).
- Aby uzyskać więcej informacji na temat Key Vault, zobacz [bezpieczny dostęp do magazynu kluczy](../../key-vault/general/secure-your-key-vault.md).