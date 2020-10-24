---
title: 'Szybki Start: Tworzenie serwera-Azure PowerShell-Azure Database for PostgreSQL-pojedynczego serwera'
description: Przewodnik Szybki Start dotyczący tworzenia Azure Database for PostgreSQL-pojedynczego serwera przy użyciu Azure PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 91351c0b2982c6ee0e96cc1433c0fadf67e3bcc0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485430"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-powershell"></a>Szybki Start: Tworzenie serwera Azure Database for PostgreSQL-pojedynczego przy użyciu programu PowerShell

W tym przewodniku szybki start opisano, jak utworzyć serwer Azure Database for PostgreSQL w grupie zasobów platformy Azure za pomocą programu PowerShell. Program PowerShell umożliwia interakcyjne tworzenie zasobów platformy Azure i zarządzanie nimi w skryptach.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Mimo że moduł AZ. PostgreSql PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. PostgreSql PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli używasz usługi Azure Database for PostgreSQL po raz pierwszy, musisz zarejestrować dostawcę zasobów **Microsoft. DBforPostgreSQL** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określony identyfikator subskrypcji za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów o nazwie Moja **zasobów** w regionie **zachodnie stany USA** .

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Utwórz serwer Azure Database for PostgreSQL przy użyciu `New-AzPostgreSqlServer` polecenia cmdlet. Serwer umożliwia zarządzanie wieloma bazami danych. Zwykle dla każdego projektu lub użytkownika używana jest oddzielna baza danych.

Poniższa tabela zawiera listę często używanych parametrów i wartości przykładowe dla `New-AzPostgreSqlServer` polecenia cmdlet.

|        **Ustawienie**         | **Wartość przykładowa** |                                                                                                                                                             **Opis**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nazwa                       | mydemoserver     | Wybierz globalnie unikatową nazwę na platformie Azure, która identyfikuje serwer Azure Database for PostgreSQL. Nazwa serwera może zawierać tylko litery, cyfry i znak łącznika (-). Wszystkie wielkie litery, które są określone, są automatycznie konwertowane na małe litery podczas procesu tworzenia. Musi zawierać od 3 do 63 znaków. |
| ResourceGroupName          | myresourcegroup  | Podaj nazwę grupy zasobów platformy Azure.                                                                                                                                                                                                                                                                                            |
| SKU                        | GP_Gen5_2        | Nazwa jednostki SKU. Postępuj zgodnie z Konwencją ** \_ \_ rdzeni wirtualnych obliczeń warstwy cenowej** . Aby uzyskać więcej informacji na temat parametru SKU, zobacz informacje przedstawione w tej tabeli.                                                                                                                                           |
| BackupRetentionDay         | 7                | Jak długo należy przechowywać kopię zapasową. Jednostka to dni. Zakres wynosi od 7 do 35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Enabled (Włączony)          | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe. Tej wartości nie można włączyć dla serwerów w warstwie cenowej podstawowa i nie można jej zmienić po utworzeniu serwera. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).                                                                                                      |
| Lokalizacja                   | westus           | Region świadczenia usługi Azure dla serwera.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Enabled (Włączony)          | Określa, czy protokół SSL ma być włączony na tym serwerze, czy nie. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | Pojemność magazynu serwera (w megabajtach). Prawidłowe StorageInMb to co najmniej 5120 MB i zwiększa się w przyrostach 1024 MB. Aby uzyskać więcej informacji na temat limitów rozmiaru magazynu, zobacz [Azure Database for PostgreSQL warstwy cenowe](./concepts-pricing-tiers.md).                                                                               |
| Wersja                    | 9,6              | Wersja główna PostgreSQL.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | Nazwa użytkownika w przypadku logowania administratora. Nie może być to **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Hasło użytkownika administrator w postaci bezpiecznego ciągu. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.                                       |

Wartość parametru **SKU** jest zgodna z Konwencją ** \_ \_ rdzeni wirtualnych generacji obliczeń warstwy** , jak pokazano w poniższych przykładach.

- `-Sku B_Gen5_1` — warstwa podstawowa, 5. generacja, 1 rdzeń wirtualny. Ta opcja to najmniejsza dostępna jednostka SKU.
- `-Sku GP_Gen5_32` — warstwa ogólnego przeznaczenia, 5. generacja, 32 rdzenie wirtualne.
- `-Sku MO_Gen5_2` — warstwa zoptymalizowana pod kątem pamięci, 5. generacja, 2 rdzenie wirtualne.

Aby uzyskać informacje na temat prawidłowych wartości **jednostki SKU** według regionów i warstw, zobacz [Azure Database for PostgreSQL warstwy cenowe](./concepts-pricing-tiers.md).

W poniższym przykładzie jest tworzony serwer PostgreSQL w regionie **zachodnie stany USA** o nazwie **mydemoserver** w **grupie zasobów z** identyfikatorem **logowania administratora serwera.** Jest to serwer generacji 5 w warstwie cenowej ogólnego przeznaczenia z obsługą dwóch rdzeni wirtualnych i geograficznie nadmiarowych kopii zapasowych. Zanotuj hasło używane w pierwszym wierszu przykładu jako hasło dla konta administratora serwera PostgreSQL.

> [!TIP]
> Nazwa serwera jest mapowana na nazwę usługi DNS i musi być globalnie unikatowa na platformie Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Należy rozważyć użycie warstwy cenowej Basic, jeśli lekkie obliczenia i we/wy są odpowiednie dla obciążenia.

> [!IMPORTANT]
> Serwery utworzone w warstwie cenowej podstawowa nie mogą być później skalowane do ogólnego przeznaczenia ani zoptymalizowane pod kątem pamięci i nie mogą być replikowane geograficznie.

## <a name="configure-a-firewall-rule"></a>Konfigurowanie reguły zapory

Utwórz regułę zapory na poziomie serwera Azure Database for PostgreSQL przy użyciu `New-AzPostgreSqlFirewallRule` polecenia cmdlet. Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak `psql` Narzędzie wiersza polecenia lub PostgreSQL Workbench, nawiązać połączenie z serwerem za pomocą zapory usługi Azure Database for PostgreSQL.

Poniższy przykład tworzy regułę zapory o nazwie **AllowMyIP** , która umożliwia nawiązywanie połączeń z określonego adresu IP (192.168.0.1). Zastąp adres IP lub zakres adresów IP odpowiadających lokalizacji, z której nawiązujesz połączenie.

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Połączenia z Azure Database for PostgreSQLą komunikację przez port 5432. Jeśli spróbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący przez port 5432 może być niedozwolony. W tym scenariuszu można nawiązać połączenie z serwerem tylko wtedy, gdy dział IT otworzy port 5432.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu. Użyj poniższego przykładu, aby określić informacje o połączeniu. Zanotuj wartości **FullyQualifiedDomainName** i **AdministratorLogin**.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgres.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>Nawiązywanie połączenia z bazą danych PostgreSQL za pomocą narzędzia psql

Jeśli na Twoim komputerze klienckim jest zainstalowany program PostgreSQL, możesz użyć lokalnego wystąpienia narzędzia [psql](https://www.postgresql.org/docs/current/static/app-psql.html), aby nawiązać połączenie z serwerem Azure PostgreSQL. Możesz również uzyskać dostęp do wstępnie zainstalowanej wersji `psql` narzędzia wiersza polecenia w Azure Cloud Shell, wybierając przycisk **Wypróbuj** dla przykładu kodu w tym artykule. Innymi sposobami dostępu do Azure Cloud Shell jest wybranie przycisku **>_** w prawym górnym rogu Azure Portal lub przez odwiedzenie [Shell.Azure.com](https://shell.azure.com/).

1. Nawiąż połączenie z serwerem usługi Azure PostgreSQL za pomocą `psql` narzędzia wiersza polecenia.

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Na przykład następujące polecenie nawiązuje połączenie z domyślną bazą danych o nazwie **Postgres** na serwerze PostgreSQL `mydemoserver.postgres.database.azure.com` przy użyciu poświadczeń dostępu. Gdy zostanie wyświetlony monit o podanie hasła, wprowadź wybrane hasło `<server_admin_password>`.

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Jeśli wolisz używać ścieżki URL w celu nawiązania połączenia z usługą Postgres, adres URL Koduj znak @ w nazwie użytkownika za pomocą `%40` . Na przykład parametry połączenia dla PSQL byłyby, `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. Po nawiązaniu połączenia z serwerem utwórz pustą bazę danych za pomocą wiersza polecenia.

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. W wierszu polecenia wykonaj następujące polecenie, aby przełączyć połączenie na nowo utworzoną bazę danych **mypgsqldb**:

   ```sql
   \c mypgsqldb
   ```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Nawiązywanie połączenia z serwerem PostgreSQL za pomocą narzędzia pgAdmin

pgAdmin to narzędzie open source używane z bazą danych PostgreSQL. Aplikację pgAdmin można zainstalować z [witryny internetowej pgAdmin](https://www.pgadmin.org/). Wersja narzędzia pgAdmin, której używasz, może różnić się od narzędzia używanego w tym poradniku Szybki start. Przeczytaj dokumentację narzędzia pgAdmin, jeśli potrzebujesz dodatkowych wskazówek.

1. Otwórz aplikację pgAdmin na swoim komputerze klienckim.

1. Na pasku narzędzi wybierz pozycję **Object** (Obiekt), umieść wskaźnik myszy nad opcją **Create** (Utwórz), a następnie wybierz pozycję **Server** (Serwer).

1. W oknie dialogowym **Create — Server** (Tworzenie — Serwer) na karcie **General** (Ogólne) wprowadź unikatową przyjazną nazwę serwera, np. **mydemoserver**.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/9-pgadmin-create-server.png" alt-text="Karta Ogólne":::

1. W oknie dialogowym **Create - Server** (Tworzenie — Serwer) na karcie **Connection** (Połączenie) uzupełnij tabelę ustawień.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/10-pgadmin-create-server.png" alt-text="Karta Ogólne":::

    Parametr narzędzia pgAdmin |Wartość|Opis
    ---|---|---
    Nazwa lub adres hosta | Nazwa serwera | Wartość nazwy serwera, która została użyta wcześniej podczas tworzenia serwera usługi Azure Database for PostgreSQL. Przykładowy serwer to **mydemoserver.postgres.database.azure.com**. Użyj w pełni kwalifikowanej nazwy domeny (** \* . Postgres.Database.Azure.com**), jak pokazano w przykładzie. Jeśli nie pamiętasz nazwy serwera, postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby uzyskać informacje dotyczące połączenia.
    Port | 5432 | Port używany podczas łączenia z serwerem usługi Azure Database for PostgreSQL.
    Baza danych konserwacji | *postgres* | Domyślna nazwa bazy danych wygenerowana przez system.
    Nazwa użytkownika | Identyfikator logowania administratora serwera | Nazwa logowania administratora serwera, którą podano wcześniej podczas tworzenia serwera usługi Azure Database for PostgreSQL. Jeśli nie pamiętasz nazwy użytkownika, postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby uzyskać informacje dotyczące połączenia. Format to *username \@ servername*.
    Hasło | Hasło administratora | Hasło wybrane podczas tworzenia serwera wcześniej w tym przewodniku Szybki start.
    Role | Pozostaw puste. | Nie trzeba podawać nazwy roli w tym momencie. Pozostaw pole puste.
    Tryb SSL | *Wymagane* | Można ustawić tryb TLS/SSL na karcie SSL pgAdmin. Domyślnie wszystkie serwery Azure Database for PostgreSQL są tworzone z włączonym wymuszaniem protokołu TLS. Aby wyłączyć wymuszanie protokołu TLS, zobacz [Konfigurowanie wymuszania protokołu TLS](./concepts-ssl-connection-security.md#configure-enforcement-of-tls).

1. Wybierz pozycję **Zapisz**.

1. W okienku **Przeglądarka** z lewej strony rozwiń węzeł **Serwery**. Wybierz serwer, np. **mydemoserver**. Kliknij, aby się z nim połączyć.

1. Rozwiń węzeł serwera, a następnie rozwiń jego podrzędny element **Bazy danych**. Lista powinna obejmować istniejącą bazę danych *postgres* oraz wszelkie inne utworzone bazy danych. Za pomocą usługi Azure Database for PostgreSQL można utworzyć wiele baz danych na jednym serwerze.

1. Kliknij prawym przyciskiem myszy pozycję **Bazy danych**, wybierz menu **Utwórz**, a następnie wybierz pozycję **Baza danych**.

1. W polu **Database** (Baza danych) wpisz wybraną nazwę bazy danych, np. **mypgsqldb2**.

1. Z listy rozwijanej wybierz pozycję **Właściciel** dla bazy danych. Wybierz nazwę logowania administratora serwera, np. przykładową nazwę **my admin**.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/11-pgadmin-database.png" alt-text="Karta Ogólne":::

1. Wybierz pozycję **Zapisz**, aby utworzyć nową pustą bazę danych.

1. W okienku **Browser** (Przeglądarka) utworzona baza danych zostanie wyświetlona na liście baz danych w ramach pozycji nazwy serwera.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym przewodniku Szybki Start nie są potrzebne do korzystania z innego przewodnika Szybki start lub samouczka, możesz je usunąć, uruchamiając Poniższy przykład.

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie znajdujące się w niej zasoby.
> Jeśli zasoby spoza zakresu tego przewodnika Szybki Start istnieją w określonej grupie zasobów, zostaną również usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Aby usunąć tylko serwer utworzony w tym przewodniku Szybki Start bez usuwania grupy zasobów, użyj `Remove-AzPostgreSqlServer` polecenia cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Projektowanie Azure Database for PostgreSQL przy użyciu programu PowerShell](tutorial-design-database-using-powershell.md)