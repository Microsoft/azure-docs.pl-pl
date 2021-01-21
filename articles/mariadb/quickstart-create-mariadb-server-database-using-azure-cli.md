---
title: 'Szybki Start: Tworzenie serwera — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB'
description: W tym przewodniku Szybki start opisano, jak utworzyć serwer usługi Azure Database for MariaDB w grupie zasobów platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 2242b0842bda9587915fc949046b8fa7effb725c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662164"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Szybki Start: Tworzenie serwera Azure Database for MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure może służyć do tworzenia zasobów platformy Azure i zarządzania nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku Szybki start opisano, jak utworzyć serwer usługi Azure Database for MariaDB w grupie zasobów platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure w czasie około pięciu minut.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

Jeśli masz wiele subskrypcji, wybierz tę subskrypcję, która zawiera zasób, lub subskrypcję, w której są naliczane opłaty. Aby wybrać określony identyfikator subskrypcji na Twoim koncie, użyj polecenia [az account set](/cli/azure/account#az-account-set):

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`:

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Tworzenie serwera usługi Azure Database for MariaDB

Utwórz serwer usługi Azure Database for MariaDB za pomocą polecenia [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create). Serwer umożliwia zarządzanie wieloma bazami danych. Zwykle dla każdego projektu lub użytkownika używana jest oddzielna baza danych.

Ustawienie | Wartość przykładowa | Opis
---|---|---
name | **mydemoserver** | Wprowadź unikatową nazwę, która identyfikuje Twój serwer usługi Azure Database for MariaDB. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | **myresourcegroup** | Wprowadź nazwę grupy zasobów platformy Azure.
sku-name | **GP_Gen5_2** | Nazwa jednostki SKU. Zgodnie z Konwencją Konwencji *cenowej* \_ rdzeni wirtualnych *generacji obliczeń* \_  w postaci skróconej. Aby uzyskać więcej informacji na temat parametru **sku-name**, zobacz sekcję poniżej tabeli.
backup-retention | **7** | Jak długo należy przechowywać kopię zapasową. Jednostka to dni. Zakres: 7–35. 
geo-redundant-backup | **Disabled** | Określa, czy dla tego serwera powinny być włączone geograficznie nadmiarowe kopie zapasowe. Dozwolone wartości: **włączone**, **wyłączone**.
location | **westus** | Lokalizacja platformy Azure dla serwera.
ssl-enforcement | **Włączono** | Określa, czy dla tego serwera powinien być włączony protokół SSL. Dozwolone wartości: **włączone**, **wyłączone**.
storage-size | **51200** | Pojemność magazynu serwera (w megabajtach). Prawidłowy rozmiar magazynu to 5120 MB (minimum) rosnący z przyrostem 1024 MB. Aby uzyskać więcej informacji na temat limitów rozmiaru magazynu, zobacz [Warstwy cenowe](./concepts-pricing-tiers.md). 
Wersja | **10,2** | Wersja główna aparatu MariaDB.
admin-user | **myadmin** | Nazwa użytkownika w przypadku logowania administratora. Parametr **admin-user** nie może mieć wartości **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.
admin-password | *Twoje hasło* | Hasło użytkownika administratora. Twoje hasło musi zawierać od 8 do 128 znaków. Musi ono zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.

Wartość parametru sku-name jest zgodna z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne}, jak pokazano w przykładach poniżej:
+ `--sku-name B_Gen5_1` — warstwa podstawowa, 5. generacja, 1 rdzeń wirtualny. Ta opcja to najmniejsza dostępna jednostka SKU.
+ `--sku-name GP_Gen5_32` — warstwa ogólnego przeznaczenia, 5. generacja, 32 rdzenie wirtualne.
+ `--sku-name MO_Gen5_2` — warstwa zoptymalizowana pod kątem pamięci, 5. generacja, 2 rdzenie wirtualne.

Aby uzyskać informacje o prawidłowych wartościach według regionu i dla warstw, zobacz [Warstwy cenowe](./concepts-pricing-tiers.md).

Poniższy przykład tworzy serwer o nazwie **mydemoserver** w regionie Zachodnie stany USA. Serwer jest w grupie zasobów **myresourcegroup** i ma identyfikator logowania administratora serwera **myadmin**. Serwer jest serwerem Gen 5 w warstwie cenowej Ogólnego przeznaczenia i ma 2 rdzenie wirtualne. Nazwa serwera jest mapowana na nazwę usługi DNS i musi być globalnie unikatowa na platformie Azure. Zastąp `<server_admin_password>` własnym hasłem administratora serwera.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> Jeśli niewielkie zasoby obliczeniowe i we/wy są wystarczające dla Twojego obciążenia, warto rozważyć użycie warstwy cenowej Podstawowa. Pamiętaj, że serwerów utworzonych w warstwie cenowej Podstawowa nie można później przeskalować do warstwy Ogólnego przeznaczenia lub Zoptymalizowana pod kątem pamięci. Przejdź na [stronę cennika](https://azure.microsoft.com/pricing/details/mariadb/), aby uzyskać więcej informacji.

## <a name="configure-a-firewall-rule"></a>Konfigurowanie reguły zapory

Utwórz regułę zapory na poziomie serwera usługi Azure Database for MariaDB za pomocą polecenia [az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create). Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak narzędzie wiersza polecenia mysql lub program MySQL Workbench, na nawiązywanie połączeń z Twoim serwerem przez zaporę usługi Azure Database for MariaDB.

Poniższy przykład powoduje utworzenie reguły zapory o nazwie `AllowMyIP`, która zezwala na połączenia z określonego adresu IP – 192.168.0.1. Zastąp adres IP lub zakres adresów IP, który odnosi się do lokalizacji, z której się łączysz.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Połączenia z usługą Azure Database for MariaDB korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W takim przypadku możesz połączyć się z własnym serwerem tylko wtedy, gdy Twój dział IT otworzy port 3306.

## <a name="configure-ssl-settings"></a>Konfigurowanie ustawień SSL

Domyślnie połączenia SSL między Twoim serwerem i aplikacjami klienckimi są wymuszane. To domyślne ustawienie zapewnia bezpieczeństwo danych „w ruchu” przez szyfrowanie strumienia danych przesyłanych przez Internet. W tym przewodniku Szybki start wyłącz połączenia SSL dla Twojego serwera. Nie zaleca się wyłączania połączenia SSL w przypadku serwerów produkcyjnych. Aby uzyskać więcej informacji, zobacz [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL (Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MariaDB)](./howto-configure-ssl.md).

Poniższy przykład powoduje wyłączenie protokołu SSL wymuszane na Twoim serwerze usługi Azure Database for MariaDB:

```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu. Aby uzyskać informacje o połączeniu, uruchom następujące polecenie:

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **fullyQualifiedDomainName** i **administratorLogin**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>Nawiązywanie połączenia z serwerem przy użyciu narzędzia wiersza polecenia mysql

Nawiąż połączenie ze swoim serwerem za pomocą narzędzia wiersza polecenia mysql. Możesz [pobrać](https://dev.mysql.com/downloads/) to narzędzie wiersza polecenia i zainstalować je na swoim komputerze. Dostęp do narzędzia wiersza polecenia możesz również uzyskać, wybierając przycisk **Wypróbuj** w kodzie przykładowym znajdującym się w tym artykule. Innym sposobem uzyskania dostępu do narzędzia wiersza polecenia jest wybranie przycisku **>_** na prawym górnym pasku narzędzi w witrynie Azure Portal, aby otworzyć **usługę Azure Cloud Shell**.

Aby nawiązać połączenie z serwerem, używając narzędzia wiersza polecenia mysql:

1. Nawiąż połączenie z serwerem:

   ```azurecli-interactive
   mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   ```

2. Wyświetl stan serwera w wierszu polecenia `mysql>`:

   ```sql
   status
   ```

   Powinien zostać wyświetlony tekst podobny do poniższego:

   ```cmd
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: ***********
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 65512
   Server version: 5.6.39.0 MariaDB Server

   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

   Connection id:          64681
   Current database:
   Current user:           myadmin@40.118.201.21
   SSL:                    Cipher in use is AES256-SHA
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 1 day 3 hours 28 min 50 sec

   Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
   --------------

   mysql>
   ```

> [!TIP]
> Aby zapoznać się z dodatkowymi poleceniami, zobacz [MySQL 5.7 Reference Manual - Chapter 4.5.1 (Podręcznik programu MySQL 5.7 — Rozdział 4.5.1)](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Nawiązywanie połączenia z serwerem za pomocą aplikacji MySQL Workbench

1. Otwórz aplikację MySQL Workbench na swoim komputerze klienckim. [Pobierz](https://dev.mysql.com/downloads/workbench/) i zainstaluj aplikację, jeśli nie jest ona jeszcze zainstalowana.

2. W oknie dialogowym **Konfigurowanie nowego połączenia** na karcie **Parametry** wprowadź poniższe informacje:

   ![Konfigurowanie nowego połączenia](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

   | Ustawienie | Sugerowana wartość | Opis |
   |---|---|---|
   | Nazwa połączenia | **Połączenie demonstracyjne** | Wprowadź etykietę dla tego połączenia (nazwa połączenia może być dowolna) |
   | Connection Method (Metoda połączenia) | **Standard (TCP/IP)** | Użyj protokołu TCP/IP do nawiązania połączenia z usługą Azure Database for MariaDB |
   | Hostname (Nazwa hosta) | **mydemoserver.mariadb.database.azure.com** | Zanotowana wcześniej nazwa serwera. |
   | Port | **3306** | Domyślny port dla usługi Azure Database for MariaDB. |
   | Nazwa użytkownika | **mydemoserver administratora \@** | Zanotowany wcześniej identyfikator logowania administratora serwera. |
   | Hasło | *Twoje hasło* | Użyj skonfigurowanego wcześniej hasła konta administratora. |

3. Aby sprawdzić, czy wszystkie parametry zostały skonfigurowane poprawnie, wybierz pozycję **Testuj połączenie**.

4. Wybierz połączenie, aby pomyślnie nawiązać połączenie z serwerem.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz zasobów, które były używane w tym przewodniku Szybki start lub samouczku, możesz je usunąć, uruchamiając następujące polecenie: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Jeśli chcesz usunąć tylko serwer, który został utworzony w tym przewodniku Szybki start, uruchom polecenie [az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete):

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Projektowanie bazy danych MariaDB za pomocą interfejsu wiersza polecenia platformy Azure](tutorial-design-database-cli.md)
