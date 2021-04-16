---
title: 'Szybki start: tworzenie serwera — Azure PowerShell — Azure Database for MariaDB'
description: W tym przewodniku Szybki start opisano, jak utworzyć serwer Azure Database for MariaDB w grupie zasobów platformy Azure przy użyciu programu PowerShell.
author: savjani
ms.author: pariks
ms.date: 05/26/2020
ms.topic: quickstart
ms.service: mariadb
ms.devlang: azurepowershell
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 7bfbd223cb337ff7d908032c769afbbf5cafa38f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531390"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-using-powershell"></a>Szybki start: tworzenie serwera Azure Database for MariaDB pomocą programu PowerShell

W tym przewodniku Szybki start opisano, jak utworzyć serwer Azure Database for MariaDB w grupie zasobów platformy Azure przy użyciu programu PowerShell. Za pomocą programu PowerShell można tworzyć zasoby platformy Azure i zarządzać nimi interaktywnie lub w skryptach.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł wymaga zainstalowania modułu Az programu PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Aby uzyskać więcej informacji na temat instalowania modułu Az programu PowerShell, zobacz [Instalowanie Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Gdy moduł Az.MariaDb programu PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu Az programu PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Gdy moduł Az.MariaDb programu PowerShell będzie ogólnie dostępny, stanie się częścią przyszłych wersji modułu Az programu PowerShell i będzie dostępny natywnie z poziomu Azure Cloud Shell.

Jeśli korzystasz z usługi Azure Database for MariaDB po raz pierwszy, musisz zarejestrować dostawcę zasobów **Microsoft.DBforMariaDB.**

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w ramach której mają być naliczane opłaty za zasoby. Wybierz identyfikator określonej subskrypcji za pomocą polecenia cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) przy użyciu polecenia cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów o **nazwie myresourcegroup** w **regionie Zachodnie stany** USA.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Tworzenie serwera usługi Azure Database for MariaDB

Utwórz serwer Azure Database for MariaDB za pomocą `New-AzMariaDbServer` polecenia cmdlet . Serwer umożliwia zarządzanie wieloma bazami danych. Zwykle dla każdego projektu lub użytkownika używana jest oddzielna baza danych.

W poniższej tabeli przedstawiono listę często używanych parametrów i przykładowych wartości dla `New-AzMariaDbServer` polecenia cmdlet .

|        **Ustawienie**         | **Wartość przykładowa** |                                                                                                                                                             **Opis**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nazwa                       | mydemoserver     | Wybierz globalnie unikatową nazwę na platformie Azure, która identyfikuje Azure Database for MariaDB serwera. Nazwa serwera może zawierać tylko litery, cyfry i znaki łącznika (-). Wszystkie określone wielkie litery są automatycznie konwertowane na małe litery podczas procesu tworzenia. Musi zawierać od 3 do 63 znaków. |
| ResourceGroupName          | myresourcegroup  | Podaj nazwę grupy zasobów platformy Azure.                                                                                                                                                                                                                                                                                            |
| SKU                        | GP_Gen5_2        | Nazwa jednostki SKU. W skrócie zgodnie z **konwencją \_ rdzenie \_** wirtualne generacji obliczeniowej warstwy cenowej. Aby uzyskać więcej informacji na temat parametru SKU, zobacz informacje poniżej tej tabeli.                                                                                                                                           |
| BackupRetentionDay         | 7                | Jak długo należy przechowywać kopię zapasową. Jednostka to dni. Zakres wynosi od 7 do 35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Enabled (Włączony)          | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe. Tej wartości nie można włączyć dla serwerów w warstwie cenowej Podstawowa i nie można jej zmienić po utworzeniu serwera. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).                                                                                                      |
| Lokalizacja                   | westus           | Region świadczenia usługi Azure dla serwera.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Enabled (Włączony)          | Określa, czy protokół SSL powinien być włączony dla tego serwera. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | Pojemność magazynu serwera (w megabajtach). Prawidłowa wartość StorageInMb wynosi co najmniej 5120 MB i zwiększa się o 1024 MB przyrostów. Aby uzyskać więcej informacji na temat limitów rozmiaru magazynu, [zobacz Azure Database for MariaDB warstwy cenowe](./concepts-pricing-tiers.md).                                                                               |
| Wersja                    | 5.7              | Wersja główna bazy danych MariaDB.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | Nazwa użytkownika w przypadku logowania administratora. Nie może być to **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Hasło użytkownika administratora w postaci bezpiecznego ciągu. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.                                       |

Wartość **parametru jednostki SKU** jest zgodna z konwencją rdzeni wirtualnych generacji obliczeniowej warstwy cenowej, jak pokazano w poniższych przykładach. **\_ \_**

- `-Sku B_Gen5_1` — warstwa podstawowa, 5. generacja, 1 rdzeń wirtualny. Ta opcja to najmniejsza dostępna jednostka SKU.
- `-Sku GP_Gen5_32` — warstwa ogólnego przeznaczenia, 5. generacja, 32 rdzenie wirtualne.
- `-Sku MO_Gen5_2` — warstwa zoptymalizowana pod kątem pamięci, 5. generacja, 2 rdzenie wirtualne.

Aby uzyskać informacje o prawidłowych **wartościach SKU** według regionu i warstw, zobacz [Azure Database for MariaDB warstwy cenowe](./concepts-pricing-tiers.md).

Poniższy przykład tworzy serwer MariaDB  w regionie Zachodnie stany USA o nazwie **mydemoserver** w grupie zasobów **myresourcegroup** z identyfikatorem logowania administratora **serwera myadmin**. Jest to serwer 5. generacji w warstwie cenowej ogólnego przeznaczenia z włączonymi 2 rdzeniami wirtualnych i geograficznie nadmiarowych kopii zapasowych. Udokumentuj hasło użyte w pierwszym wierszu przykładu, ponieważ jest to hasło do konta administratora serwera MariaDB.

> [!TIP]
> Nazwa serwera jest mapowana na nazwę usługi DNS i musi być globalnie unikatowa na platformie Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Rozważ użycie podstawowej warstwy cenowej, jeśli lekkie zasoby obliczeniowe i we/wy są odpowiednie dla Twojego obciążenia.

> [!IMPORTANT]
> Serwerów utworzonych w warstwie cenowej Podstawowa nie można później skalować do warstwy ogólnego przeznaczenia ani zoptymalizowane pod kątem pamięci i nie można ich replikować geograficznie.

## <a name="configure-a-firewall-rule"></a>Konfigurowanie reguły zapory

Utwórz regułę Azure Database for MariaDB na poziomie serwera przy użyciu `New-AzMariaDbFirewallRule` polecenia cmdlet . Reguła zapory na poziomie serwera umożliwia aplikacji zewnętrznej, takiej jak narzędzie wiersza polecenia lub aplikacja MariaDB Workbench, nawiązywanie połączenia z serwerem przez `mysql` zaporę Azure Database for MariaDB usługi.

Poniższy przykład tworzy regułę zapory o nazwie **AllowMyIP,** która zezwala na połączenia z określonego adresu IP, 192.168.0.1. Zastąp adres IP lub zakres adresów IP, które odpowiadają lokalizacji, z która jest nawiązywane połączenie.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Połączenia z usługą Azure Database for MariaDB korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W tym scenariuszu można nawiązać połączenie z serwerem tylko wtedy, gdy dział IT otworzy port 3306.

## <a name="configure-ssl-settings"></a>Konfigurowanie ustawień SSL

Domyślnie połączenia SSL między Twoim serwerem i aplikacjami klienckimi są wymuszane. To ustawienie domyślne zapewnia bezpieczeństwo _danych w ruchu_ przez szyfrowanie strumienia danych przez Internet. W tym przewodniku Szybki start wyłącz połączenia SSL dla Twojego serwera. Aby uzyskać więcej informacji, zobacz [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL (Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MariaDB)](./howto-configure-ssl.md).

> [!WARNING]
> Nie zaleca się wyłączania połączenia SSL w przypadku serwerów produkcyjnych.

Poniższy przykład wyłącza protokół SSL na Azure Database for MariaDB serwera.

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu. Skorzystaj z poniższego przykładu, aby określić informacje o połączeniu. Zanotuj wartości parametrów **FullyQualifiedDomainName** i **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Nawiązywanie połączenia z serwerem przy użyciu narzędzia wiersza polecenia mysql

Połącz się z serwerem za pomocą `mysql` narzędzia wiersza polecenia. Aby pobrać i zainstalować narzędzie wiersza polecenia, zobacz [MySQL Community Downloads (MySQL Community —](https://dev.mysql.com/downloads/shell/)pliki do pobrania). Możesz również uzyskać dostęp do wstępnie zainstalowanej wersji narzędzia wiersza polecenia w programie Azure Cloud Shell wybierając przycisk Wypróbuj w przykładowym `mysql` kodzie w tym artykule.  Inne sposoby uzyskiwania Azure Cloud Shell to wybranie  przycisku>_ na pasku narzędzi w prawym górnym rogu strony Azure Portal lub odwiedzenie [shell.azure.com](https://shell.azure.com/).

1. Połącz się z serwerem za pomocą `mysql` narzędzia wiersza polecenia.

   ```azurepowershell-interactive
   mysql -h <servername>.mariadb.database.azure.com -u myadmin@<servername> -p
   ```

1. Wyświetlanie stanu serwera.

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

## <a name="connect-to-the-server-using-mariadb-workbench"></a>Nawiązywanie połączenia z serwerem przy użyciu aplikacji MariaDB Workbench

1. Uruchom aplikację MySQL Workbench na swoim komputerze klienckim. Aby pobrać i zainstalować aplikację MySQL Workbench, zobacz [Download MySQL Workbench (Pobieranie aplikacji MySQL Workbench).](https://dev.mysql.com/downloads/workbench/)

1. W oknie dialogowym **Konfigurowanie nowego połączenia** wprowadź poniższe informacje na karcie **Parametry**:

   ![konfigurowanie nowego połączenia](./media/quickstart-create-mariadb-server-database-using-azure-powershell/setup-new-connection.png)

    |    **Ustawienie**    |            **Sugerowana wartość**            |                      **Opis**                       |
    | ----------------- | ----------------------------------------- | ---------------------------------------------------------- |
    | Nazwa połączenia   | Moje połączenie                             | Określanie etykiety dla tego połączenia                        |
    | Connection Method (Metoda połączenia) | Standard (TCP/IP)                         | Użyj protokołu TCP/IP do nawiązania połączenia z usługą Azure Database for MariaDB |
    | Hostname (Nazwa hosta)          | `mydemoserver.mariadb.database.azure.com` | Zanotowana wcześniej nazwa serwera                           |
    | Port              | 3306                                      | Domyślny port dla bazy danych MariaDB                                 |
    | Nazwa użytkownika          | myadmin@mydemoserver                      | Zanotuj wcześniej identyfikator logowania administratora serwera                |
    | Hasło          | *************                             | Użyj skonfigurowanego wcześniej hasła konta administratora      |

1. Aby sprawdzić, czy parametry są poprawnie skonfigurowane, kliknij przycisk **Testuj** połączenie.

1. Wybierz połączenie, aby nawiązać połączenie z serwerem.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym przewodniku Szybki start nie są potrzebne w innym przewodniku Szybki start lub samouczku, możesz je usunąć, uruchamiając poniższy przykład.

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie zawarte w niej zasoby.
> Jeśli zasoby spoza zakresu tego przewodnika Szybki start istnieją w określonej grupie zasobów, również zostaną usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Aby usunąć tylko serwer utworzony w tym przewodniku Szybki start bez usuwania grupy zasobów, użyj `Remove-AzMariaDbServer` polecenia cmdlet .

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Projektowanie aplikacji Azure Database for MariaDB użyciu programu PowerShell](tutorial-design-database-using-powershell.md)
