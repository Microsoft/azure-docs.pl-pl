---
title: 'Szybki Start: Tworzenie serwera Azure PowerShell-Azure Database for MySQL'
description: W tym przewodniku szybki start opisano, jak utworzyć serwer Azure Database for MySQL w grupie zasobów platformy Azure za pomocą programu PowerShell.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 04/28/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: d12d447acb3b6bf2b6f84e9768e9f063a9a36b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542324"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-powershell"></a>Szybki Start: Tworzenie serwera Azure Database for MySQL przy użyciu programu PowerShell

W tym przewodniku szybki start opisano, jak utworzyć serwer Azure Database for MySQL w grupie zasobów platformy Azure za pomocą programu PowerShell. Program PowerShell umożliwia interakcyjne tworzenie zasobów platformy Azure i zarządzanie nimi w skryptach.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Mimo że moduł AZ. MySql PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MySql -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. MySql PowerShell jest on częścią przyszłej wersji modułu AZ PowerShell releases i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli używasz usługi Azure Database for MySQL po raz pierwszy, musisz zarejestrować dostawcę zasobów **Microsoft. DBforMySQL** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMySQL
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

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL

Utwórz serwer Azure Database for MySQL przy użyciu `New-AzMySqlServer` polecenia cmdlet. Serwer umożliwia zarządzanie wieloma bazami danych. Zwykle dla każdego projektu lub użytkownika używana jest oddzielna baza danych.

Poniższa tabela zawiera listę często używanych parametrów i wartości przykładowe dla `New-AzMySqlServer` polecenia cmdlet.

|        **Ustawienie**         | **Wartość przykładowa** |                                                                                                                                                             **Opis**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nazwa                       | mydemoserver     | Wybierz globalnie unikatową nazwę na platformie Azure, która identyfikuje serwer Azure Database for MySQL. Nazwa serwera może zawierać tylko litery, cyfry i znak łącznika (-). Wszystkie wielkie litery, które są określone, są automatycznie konwertowane na małe litery podczas procesu tworzenia. Musi zawierać od 3 do 63 znaków. |
| ResourceGroupName          | myresourcegroup  | Podaj nazwę grupy zasobów platformy Azure.                                                                                                                                                                                                                                                                                            |
| SKU                        | GP_Gen5_2        | Nazwa jednostki SKU. Postępuj zgodnie z Konwencją **\_ \_ rdzeni wirtualnych obliczeń warstwy cenowej** . Aby uzyskać więcej informacji na temat parametru SKU, zobacz informacje przedstawione w tej tabeli.                                                                                                                                           |
| BackupRetentionDay         | 7                | Jak długo należy przechowywać kopię zapasową. Jednostka to dni. Zakres wynosi od 7 do 35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Enabled (Włączony)          | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe. Tej wartości nie można włączyć dla serwerów w warstwie cenowej podstawowa i nie można jej zmienić po utworzeniu serwera. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).                                                                                                      |
| Lokalizacja                   | westus           | Region świadczenia usługi Azure dla serwera.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Enabled (Włączony)          | Określa, czy protokół SSL ma być włączony na tym serwerze, czy nie. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | Pojemność magazynu serwera (w megabajtach). Prawidłowe StorageInMb to co najmniej 5120 MB i zwiększa się w przyrostach 1024 MB. Aby uzyskać więcej informacji na temat limitów rozmiaru magazynu, zobacz [Azure Database for MySQL warstwy cenowe](./concepts-pricing-tiers.md).                                                                               |
| Wersja                    | 5.7              | Wersja główna MySQL.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | Nazwa użytkownika w przypadku logowania administratora. Nie może być to **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Hasło użytkownika administrator w postaci bezpiecznego ciągu. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.                                       |

Wartość parametru **SKU** jest zgodna z Konwencją **\_ \_ rdzeni wirtualnych generacji obliczeń warstwy** , jak pokazano w poniższych przykładach.

- `-Sku B_Gen5_1` — warstwa podstawowa, 5. generacja, 1 rdzeń wirtualny. Ta opcja to najmniejsza dostępna jednostka SKU.
- `-Sku GP_Gen5_32` — warstwa ogólnego przeznaczenia, 5. generacja, 32 rdzenie wirtualne.
- `-Sku MO_Gen5_2` — warstwa zoptymalizowana pod kątem pamięci, 5. generacja, 2 rdzenie wirtualne.

Aby uzyskać informacje na temat prawidłowych wartości **jednostki SKU** według regionów i warstw, zobacz [Azure Database for MySQL warstwy cenowe](./concepts-pricing-tiers.md).

W poniższym przykładzie jest tworzony serwer MySQL w regionie **zachodnie stany USA** o nazwie **mydemoserver** w grupie zasobów zasobu **zasobu** z identyfikatorem **logowania administratora serwera.** Jest to serwer generacji 5 w warstwie cenowej ogólnego przeznaczenia z obsługą dwóch rdzeni wirtualnych i geograficznie nadmiarowych kopii zapasowych. Zanotuj hasło używane w pierwszym wierszu przykładu, ponieważ jest to hasło do konta administratora serwera MySQL.

> [!TIP]
> Nazwa serwera jest mapowana na nazwę usługi DNS i musi być globalnie unikatowa na platformie Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Należy rozważyć użycie warstwy cenowej Basic, jeśli lekkie obliczenia i we/wy są odpowiednie dla obciążenia.

> [!IMPORTANT]
> Serwery utworzone w warstwie cenowej podstawowa nie mogą być później skalowane do ogólnego przeznaczenia ani zoptymalizowane pod kątem pamięci i nie mogą być replikowane geograficznie.

## <a name="configure-a-firewall-rule"></a>Konfigurowanie reguły zapory

Utwórz regułę zapory na poziomie serwera Azure Database for MySQL przy użyciu `New-AzMySqlFirewallRule` polecenia cmdlet. Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak `mysql` Narzędzie wiersza polecenia lub Workbench MySQL, łączyć się z serwerem za pomocą zapory usługi Azure Database for MySQL.

Poniższy przykład tworzy regułę zapory o nazwie **AllowMyIP** , która umożliwia nawiązywanie połączeń z określonego adresu IP (192.168.0.1). Zastąp adres IP lub zakres adresów IP odpowiadających lokalizacji, z której nawiązujesz połączenie.

```azurepowershell-interactive
New-AzMySqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Połączenia z usługą Azure Database for MySQL korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W tym scenariuszu można nawiązać połączenie z serwerem tylko wtedy, gdy dział IT otworzy port 3306.

## <a name="configure-ssl-settings"></a>Konfigurowanie ustawień SSL

Domyślnie połączenia SSL między Twoim serwerem i aplikacjami klienckimi są wymuszane. To ustawienie domyślne zapewnia bezpieczeństwo danych _w ruchu_ przez szyfrowanie strumienia danych przez Internet. W tym przewodniku Szybki start wyłącz połączenia SSL dla Twojego serwera. Aby uzyskać więcej informacji, zobacz [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL (Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MySQL)](./howto-configure-ssl.md).

> [!WARNING]
> Nie zaleca się wyłączania połączenia SSL w przypadku serwerów produkcyjnych.

Poniższy przykład wyłącza protokół SSL na serwerze Azure Database for MySQL.

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu. Użyj poniższego przykładu, aby określić informacje o połączeniu. Zanotuj wartości **FullyQualifiedDomainName** i **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mysql.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Nawiązywanie połączenia z serwerem za pomocą narzędzia wiersza polecenia MySQL

Połącz się z serwerem za pomocą `mysql` narzędzia wiersza polecenia. Aby pobrać i zainstalować narzędzie wiersza polecenia, zobacz artykuł dotyczący [pobierania społeczności MySQL](https://dev.mysql.com/downloads/shell/). Możesz również uzyskać dostęp do wstępnie zainstalowanej wersji `mysql` narzędzia wiersza polecenia w Azure Cloud Shell, wybierając przycisk **Wypróbuj** dla przykładu kodu w tym artykule. Innymi sposobami dostępu do Azure Cloud Shell jest wybranie przycisku **>_** w prawym górnym rogu Azure Portal lub przez odwiedzenie [Shell.Azure.com](https://shell.azure.com/).

1. Nawiąż połączenie z serwerem za pomocą `mysql` narzędzia wiersza polecenia.

   ```azurepowershell-interactive
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

1. Wyświetl stan serwera.

   ```sql
   mysql> status
   ```

    ```Output
    C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
    Enter password: *************
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65512
    Server version: 5.6.42.0 MySQL Community Server (GPL)

    Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.29, for Win64 (x86_64)

    Connection id:          65512
    Current database:
    Current user:           myadmin@myipaddress
    SSL:                    Not in use
    Using delimiter:        ;
    Server version:         5.6.42.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 1 hour 2 min 12 sec

    Threads: 7  Questions: 952  Slow queries: 0  Opens: 66  Flush tables: 3  Open tables: 16  Queries per second avg: 0.255
    --------------

    mysql>
    ```

Aby zapoznać się z dodatkowymi poleceniami, zobacz [MySQL 5.7 Reference Manual - Chapter 4.5.1 (Podręcznik programu MySQL 5.7 — Rozdział 4.5.1)](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-mysql-workbench"></a>Nawiązywanie połączenia z serwerem za pomocą programu MySQL Workbench

1. Uruchom aplikację MySQL Workbench na swoim komputerze klienckim. Aby pobrać i zainstalować program MySQL Workbench, zobacz [pobieranie MySQL Workbench](https://dev.mysql.com/downloads/workbench/).

1. W oknie dialogowym **Konfigurowanie nowego połączenia** wprowadź poniższe informacje na karcie **Parametry**:

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-powershell/setup-new-connection.png" alt-text="konfigurowanie nowego połączenia":::

    |    **Ustawienie**    |           **Sugerowana wartość**           |                      **Opis**                       |
    | ----------------- | --------------------------------------- | ---------------------------------------------------------- |
    | Nazwa połączenia   | Moje połączenie                           | Określ etykietę dla tego połączenia                        |
    | Connection Method (Metoda połączenia) | Standard (TCP/IP)                       | Użyj protokołu TCP/IP, aby nawiązać połączenie z usługą Azure Database for MySQL |
    | Hostname (Nazwa hosta)          | `mydemoserver.mysql.database.azure.com` | Nazwa serwera zanotowana wcześniej                           |
    | Port              | 3306                                    | Domyślny port dla programu MySQL                                 |
    | Nazwa użytkownika          | myadmin@mydemoserver                    | Identyfikator logowania administratora serwera zanotowany wcześniej                |
    | Hasło          | *************                           | Użyj hasła do konta administratora, które zostało skonfigurowane wcześniej      |

1. Aby sprawdzić, czy parametry są prawidłowo skonfigurowane, kliknij przycisk **Testuj połączenie** .

1. Wybierz połączenie, aby nawiązać połączenie z serwerem.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym przewodniku Szybki Start nie są potrzebne do korzystania z innego przewodnika Szybki start lub samouczka, możesz je usunąć, uruchamiając Poniższy przykład.

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie znajdujące się w niej zasoby.
> Jeśli zasoby spoza zakresu tego przewodnika Szybki Start istnieją w określonej grupie zasobów, zostaną również usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Aby usunąć tylko serwer utworzony w tym przewodniku Szybki Start bez usuwania grupy zasobów, użyj `Remove-AzMySqlServer` polecenia cmdlet.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Projektowanie Azure Database for MySQL przy użyciu programu PowerShell](tutorial-design-database-using-powershell.md)