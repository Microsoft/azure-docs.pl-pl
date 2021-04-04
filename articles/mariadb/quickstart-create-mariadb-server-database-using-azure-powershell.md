---
title: 'Szybki Start: Tworzenie serwera Azure PowerShell-Azure Database for MariaDB'
description: W tym przewodniku szybki start opisano, jak utworzyć serwer Azure Database for MariaDB w grupie zasobów platformy Azure za pomocą programu PowerShell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 05082ffa891b72b472ed5433282198c61080f073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664668"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-using-powershell"></a>Szybki Start: Tworzenie serwera Azure Database for MariaDB przy użyciu programu PowerShell

W tym przewodniku szybki start opisano, jak utworzyć serwer Azure Database for MariaDB w grupie zasobów platformy Azure za pomocą programu PowerShell. Program PowerShell umożliwia interakcyjne tworzenie zasobów platformy Azure i zarządzanie nimi w skryptach.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Mimo że moduł AZ. MariaDb PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. MariaDb PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli używasz usługi Azure Database for MariaDB po raz pierwszy, musisz zarejestrować dostawcę zasobów **Microsoft. DBforMariaDB** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
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

## <a name="create-an-azure-database-for-mariadb-server"></a>Tworzenie serwera usługi Azure Database for MariaDB

Utwórz serwer Azure Database for MariaDB przy użyciu `New-AzMariaDbServer` polecenia cmdlet. Serwer umożliwia zarządzanie wieloma bazami danych. Zwykle dla każdego projektu lub użytkownika używana jest oddzielna baza danych.

Poniższa tabela zawiera listę często używanych parametrów i wartości przykładowe dla `New-AzMariaDbServer` polecenia cmdlet.

|        **Ustawienie**         | **Wartość przykładowa** |                                                                                                                                                             **Opis**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nazwa                       | mydemoserver     | Wybierz globalnie unikatową nazwę na platformie Azure, która identyfikuje serwer Azure Database for MariaDB. Nazwa serwera może zawierać tylko litery, cyfry i znak łącznika (-). Wszystkie wielkie litery, które są określone, są automatycznie konwertowane na małe litery podczas procesu tworzenia. Musi zawierać od 3 do 63 znaków. |
| ResourceGroupName          | myresourcegroup  | Podaj nazwę grupy zasobów platformy Azure.                                                                                                                                                                                                                                                                                            |
| SKU                        | GP_Gen5_2        | Nazwa jednostki SKU. Postępuj zgodnie z Konwencją **\_ \_ rdzeni wirtualnych obliczeń warstwy cenowej** . Aby uzyskać więcej informacji na temat parametru SKU, zobacz informacje przedstawione w tej tabeli.                                                                                                                                           |
| BackupRetentionDay         | 7                | Jak długo należy przechowywać kopię zapasową. Jednostka to dni. Zakres wynosi od 7 do 35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Enabled (Włączony)          | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe. Tej wartości nie można włączyć dla serwerów w warstwie cenowej podstawowa i nie można jej zmienić po utworzeniu serwera. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).                                                                                                      |
| Lokalizacja                   | westus           | Region świadczenia usługi Azure dla serwera.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Enabled (Włączony)          | Określa, czy protokół SSL ma być włączony na tym serwerze, czy nie. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | Pojemność magazynu serwera (w megabajtach). Prawidłowe StorageInMb to co najmniej 5120 MB i zwiększa się w przyrostach 1024 MB. Aby uzyskać więcej informacji na temat limitów rozmiaru magazynu, zobacz [Azure Database for MariaDB warstwy cenowe](./concepts-pricing-tiers.md).                                                                               |
| Wersja                    | 5.7              | Wersja główna MariaDB.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | Nazwa użytkownika w przypadku logowania administratora. Nie może być to **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Hasło użytkownika administrator w postaci bezpiecznego ciągu. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.                                       |

Wartość parametru **SKU** jest zgodna z Konwencją **\_ \_ rdzeni wirtualnych generacji obliczeń warstwy** , jak pokazano w poniższych przykładach.

- `-Sku B_Gen5_1` — warstwa podstawowa, 5. generacja, 1 rdzeń wirtualny. Ta opcja to najmniejsza dostępna jednostka SKU.
- `-Sku GP_Gen5_32` — warstwa ogólnego przeznaczenia, 5. generacja, 32 rdzenie wirtualne.
- `-Sku MO_Gen5_2` — warstwa zoptymalizowana pod kątem pamięci, 5. generacja, 2 rdzenie wirtualne.

Aby uzyskać informacje na temat prawidłowych wartości **jednostki SKU** według regionów i warstw, zobacz [Azure Database for MariaDB warstwy cenowe](./concepts-pricing-tiers.md).

W poniższym przykładzie jest tworzony serwer MariaDB w regionie **zachodnie stany USA** o nazwie **mydemoserver** w **grupie zasobów z** identyfikatorem **logowania administratora serwera.** Jest to serwer generacji 5 w warstwie cenowej ogólnego przeznaczenia z obsługą dwóch rdzeni wirtualnych i geograficznie nadmiarowych kopii zapasowych. Zanotuj hasło używane w pierwszym wierszu przykładu jako hasło dla konta administratora serwera MariaDB.

> [!TIP]
> Nazwa serwera jest mapowana na nazwę usługi DNS i musi być globalnie unikatowa na platformie Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Należy rozważyć użycie warstwy cenowej Basic, jeśli lekkie obliczenia i we/wy są odpowiednie dla obciążenia.

> [!IMPORTANT]
> Serwery utworzone w warstwie cenowej podstawowa nie mogą być później skalowane do ogólnego przeznaczenia ani zoptymalizowane pod kątem pamięci i nie mogą być replikowane geograficznie.

## <a name="configure-a-firewall-rule"></a>Konfigurowanie reguły zapory

Utwórz regułę zapory na poziomie serwera Azure Database for MariaDB przy użyciu `New-AzMariaDbFirewallRule` polecenia cmdlet. Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak `mysql` Narzędzie wiersza polecenia lub MariaDB Workbench, nawiązać połączenie z serwerem za pomocą zapory usługi Azure Database for MariaDB.

Poniższy przykład tworzy regułę zapory o nazwie **AllowMyIP** , która umożliwia nawiązywanie połączeń z określonego adresu IP (192.168.0.1). Zastąp adres IP lub zakres adresów IP odpowiadających lokalizacji, z której nawiązujesz połączenie.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Połączenia z usługą Azure Database for MariaDB korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W tym scenariuszu można nawiązać połączenie z serwerem tylko wtedy, gdy dział IT otworzy port 3306.

## <a name="configure-ssl-settings"></a>Konfigurowanie ustawień SSL

Domyślnie połączenia SSL między Twoim serwerem i aplikacjami klienckimi są wymuszane. To ustawienie domyślne zapewnia bezpieczeństwo danych _w ruchu_ przez szyfrowanie strumienia danych przez Internet. W tym przewodniku Szybki start wyłącz połączenia SSL dla Twojego serwera. Aby uzyskać więcej informacji, zobacz [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL (Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MariaDB)](./howto-configure-ssl.md).

> [!WARNING]
> Nie zaleca się wyłączania połączenia SSL w przypadku serwerów produkcyjnych.

Poniższy przykład wyłącza protokół SSL na serwerze Azure Database for MariaDB.

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu. Użyj poniższego przykładu, aby określić informacje o połączeniu. Zanotuj wartości **FullyQualifiedDomainName** i **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Nawiązywanie połączenia z serwerem za pomocą narzędzia wiersza polecenia MySQL

Połącz się z serwerem za pomocą `mysql` narzędzia wiersza polecenia. Aby pobrać i zainstalować narzędzie wiersza polecenia, zobacz artykuł dotyczący [pobierania społeczności MySQL](https://dev.mysql.com/downloads/shell/). Możesz również uzyskać dostęp do wstępnie zainstalowanej wersji `mysql` narzędzia wiersza polecenia w Azure Cloud Shell, wybierając przycisk **Wypróbuj** dla przykładu kodu w tym artykule. Innymi sposobami dostępu do Azure Cloud Shell jest wybranie przycisku **>_** w prawym górnym rogu Azure Portal lub przez odwiedzenie [Shell.Azure.com](https://shell.azure.com/).

1. Nawiąż połączenie z serwerem za pomocą `mysql` narzędzia wiersza polecenia.

   ```azurepowershell-interactive
   mysql -h <servername>.mariadb.database.azure.com -u myadmin@<servername> -p
   ```

1. Wyświetl stan serwera.

   ```sql
   mysql> status
   ```

   ```Output
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: *************
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 64793
   Server version: 5.6.42.0 MariaDB Server

   Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   /usr/bin/mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper

   Connection id:          64793
   Current database:
   Current user:           myadmin@myipaddress
   SSL:                    Cipher in use is ECDHE-RSA-AES256-GCM-SHA384
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.42.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 17 min 4 sec

   Threads: 19  Questions: 482  Slow queries: 0  Opens: 50  Flush tables: 3  Open tables: 12  Queries per second avg: 0.470
   --------------

   mysql>
   ```

Aby zapoznać się z dodatkowymi poleceniami, zobacz [MySQL 5.7 Reference Manual - Chapter 4.5.1 (Podręcznik programu MySQL 5.7 — Rozdział 4.5.1)](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-mariadb-workbench"></a>Nawiązywanie połączenia z serwerem przy użyciu usługi MariaDB Workbench

1. Uruchom aplikację MySQL Workbench na swoim komputerze klienckim. Aby pobrać i zainstalować program MySQL Workbench, zobacz [pobieranie MySQL Workbench](https://dev.mysql.com/downloads/workbench/).

1. W oknie dialogowym **Konfigurowanie nowego połączenia** wprowadź poniższe informacje na karcie **Parametry**:

   ![konfigurowanie nowego połączenia](./media/quickstart-create-mariadb-server-database-using-azure-powershell/setup-new-connection.png)

    |    **Ustawienie**    |            **Sugerowana wartość**            |                      **Opis**                       |
    | ----------------- | ----------------------------------------- | ---------------------------------------------------------- |
    | Nazwa połączenia   | Moje połączenie                             | Określ etykietę dla tego połączenia                        |
    | Connection Method (Metoda połączenia) | Standard (TCP/IP)                         | Użyj protokołu TCP/IP do nawiązania połączenia z usługą Azure Database for MariaDB |
    | Hostname (Nazwa hosta)          | `mydemoserver.mariadb.database.azure.com` | Nazwa serwera zanotowana wcześniej                           |
    | Port              | 3306                                      | Domyślny port dla MariaDB                                 |
    | Nazwa użytkownika          | myadmin@mydemoserver                      | Identyfikator logowania administratora serwera zanotowany wcześniej                |
    | Hasło          | *************                             | Użyj hasła do konta administratora, które zostało skonfigurowane wcześniej      |

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

Aby usunąć tylko serwer utworzony w tym przewodniku Szybki Start bez usuwania grupy zasobów, użyj `Remove-AzMariaDbServer` polecenia cmdlet.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Projektowanie Azure Database for MariaDB przy użyciu programu PowerShell](tutorial-design-database-using-powershell.md)