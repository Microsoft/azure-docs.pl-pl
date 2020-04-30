---
title: 'Szybki Start: Tworzenie serwera — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL'
description: W tym przewodniku Szybki start opisano, jak utworzyć serwer usługi Azure Database for MySQL w grupie zasobów platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/09/2019
ms.custom: mvc
ms.openlocfilehash: acf5f3cdf761e1773d6e9384a4ceb99a645ed7cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74773521"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Szybki Start: Tworzenie serwera Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure

> [!TIP]
> Rozważ użycie prostszego polecenia [AZ MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI (obecnie w wersji zapoznawczej). Wypróbuj [Przewodnik Szybki Start](./quickstart-create-server-up-azure-cli.md).

W tym przewodniku Szybki start opisano, jak utworzyć serwer usługi Azure Database for MySQL w grupie zasobów platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure w czasie około pięciu minut. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w której zasób istnieje lub dla której są za niego naliczane opłaty. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account#az-account-set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz [grupę zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL
Utwórz serwer usługi Azure Database for MySQL za pomocą polecenia **[az mysql server create](/cli/azure/mysql/server#az-mysql-server-create)**. Serwer umożliwia zarządzanie wieloma bazami danych. Zwykle dla każdego projektu lub użytkownika używana jest oddzielna baza danych.

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Wybierz unikatową nazwę, która identyfikuje serwer usługi Azure Database for MySQL. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | myresourcegroup | Podaj nazwę grupy zasobów platformy Azure.
sku-name | GP_Gen5_2 | Nazwa jednostki SKU. Zgodnie z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne} w skrócie. Zobacz poniższą tabelę, aby uzyskać więcej informacji na temat parametru sku-name.
backup-retention | 7 | Jak długo należy przechowywać kopię zapasową. Jednostka to dni. Zakres wynosi od 7 do 35. 
geo-redundant-backup | Disabled (Wyłączony) | Określa, czy dla tego serwera powinny być włączone kopie zapasowe geograficznie nadmiarowe. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).
location | westus | Lokalizacja platformy Azure dla serwera.
ssl-enforcement | Enabled (Włączony) | Określa, czy dla serwera powinien być włączony protokół SSL. Dozwolone wartości: Enabled (włączone) i Disabled (wyłączone).
storage-size | 51200 | Pojemność magazynu serwera (w megabajtach). Rozmiar magazynu powinien wynosić co najmniej 5120 MB i można go zwiększać o wielokrotność 1024 MB. Zobacz dokument dotyczący [warstw cenowych](./concepts-pricing-tiers.md), aby uzyskać więcej informacji na temat limitu rozmiaru magazynu. 
Wersja | 5.7 | Wersja główna MySQL.
admin-user | myadmin | Nazwa użytkownika w przypadku logowania administratora. Nie może być to **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.
admin-password | *bezpieczne hasło* | Hasło użytkownika administratora. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.


Wartość parametru sku-name jest zgodna z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne}, jak pokazano w przykładach poniżej:
+ `--sku-name B_Gen5_1` — warstwa podstawowa, 5. generacja, 1 rdzeń wirtualny. Ta opcja to najmniejsza dostępna jednostka SKU.
+ `--sku-name GP_Gen5_32` — warstwa ogólnego przeznaczenia, 5. generacja, 32 rdzenie wirtualne.
+ `--sku-name MO_Gen5_2` — warstwa zoptymalizowana pod kątem pamięci, 5. generacja, 2 rdzenie wirtualne.

Aby ustalić poprawne wartości dla poszczególnych regionów i warstw, zapoznaj się z dokumentacją [warstw cenowych](./concepts-pricing-tiers.md).

W poniższym przykładzie serwer MySQL 5.7 o nazwie `mydemoserver` zostanie utworzony w grupie zasobów `myresourcegroup` w regionie Zachodnie stany USA przy użyciu identyfikatora logowania administratora serwera `myadmin`. Jest to serwer **4. generacji** **ogólnego przeznaczenia** z 2 **rdzeniami wirtualnymi**. Zastąp zmienną `<server_admin_password>` swoją własną wartością.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

> [!NOTE]
> Jeśli niewielkie zasoby obliczeniowe i we/wy są wystarczające dla Twojego obciążenia, warto rozważyć użycie warstwy cenowej Podstawowa. Pamiętaj, że serwerów utworzonych w warstwie cenowej Podstawowa nie można później przeskalować do warstwy Ogólnego przeznaczenia lub Zoptymalizowana pod kątem pamięci. Przejdź na [stronę cennika](https://azure.microsoft.com/pricing/details/mysql/), aby uzyskać więcej informacji.
> 

## <a name="configure-firewall-rule"></a>Konfigurowanie reguły zapory
Utwórz regułę zapory na poziomie serwera usługi Azure Database for MySQL za pomocą polecenia **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create)**. Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak narzędzie wiersza polecenia **mysql.exe** lub program MySQL Workbench, na nawiązywanie połączeń z Twoim serwerem przez zaporę usługi Azure MySQL. 

Poniższy przykład powoduje utworzenie reguły zapory o nazwie `AllowMyIP`, która zezwala na połączenia z określonego adresu IP – 192.168.0.1. Zastąp ten adres adresem IP lub zakresem adresów IP odpowiednim dla urządzeń, z których będzie nawiązywane połączenie. 

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Połączenia z usługą Azure Database for MySQL korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W takim przypadku nie będzie można nawiązać połączenia z serwerem, chyba że dział informatyczny otworzy port 3306.
> 


## <a name="configure-ssl-settings"></a>Konfigurowanie ustawień SSL
Domyślnie połączenia SSL między Twoim serwerem i aplikacjami klienckimi są wymuszane. Zapewnia to bezpieczeństwo danych „w ruchu” przez szyfrowanie strumienia danych przesyłanych przez Internet. Aby uprościć ten przewodnik Szybki start, wyłącz połączenia SSL dla Twojego serwera. Nie zaleca się wyłączania połączenia SSL w przypadku serwerów produkcyjnych. Aby uzyskać więcej informacji, zobacz [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL (Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MySQL)](./howto-configure-ssl.md).

W poniższym przykładzie jest wyłączane wymuszanie protokołu SSL na Twoim serwerze MySQL.
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **fullyQualifiedDomainName** i **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Nawiązywanie połączenia z serwerem za pomocą narzędzia wiersza polecenia mysql.exe
Nawiąż połączenia z serwerem za pomocą narzędzia wiersza polecenia **mysql.exe**. Program MySQL możesz pobrać [stąd](https://dev.mysql.com/downloads/) i zainstalować go na swoim komputerze. 

Wpisz kolejne polecenia: 

1. Nawiąż połączenie z serwerem za pomocą narzędzia wiersza polecenia **mysql**:
   ```bash
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

2. Wyświetl stan serwera:
   ```sql
   mysql> status
   ```
   Jeśli wszystko pójdzie dobrze, narzędzie wiersza polecenia powinno zwrócić następujący tekst:

```dos
C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> Aby zapoznać się z dodatkowymi poleceniami, zobacz [MySQL 5.7 Reference Manual - Chapter 4.5.1 (Podręcznik programu MySQL 5.7 — Rozdział 4.5.1)](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Nawiązywanie połączenia z serwerem za pomocą narzędzia z graficznym interfejsem użytkownika MySQL Workbench
1. Uruchom aplikację MySQL Workbench na swoim komputerze klienckim. Aplikację MySQL Workbench możesz pobrać i zainstalować [stąd](https://dev.mysql.com/downloads/workbench/).

2. W oknie dialogowym **Konfigurowanie nowego połączenia** wprowadź poniższe informacje na karcie **Parametry**:

   ![konfigurowanie nowego połączenia](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **Ustawienie** | **Sugerowana wartość** | **Opis** |
|---|---|---|
|   Nazwa połączenia | Moje połączenie | Podaj etykietę dla tego połączenia (może to być dowolny tekst) |
| Metoda połączenia | wybierz opcję Standardowa (TCP/IP) | Użyj protokołu TCP/IP do nawiązania połączenia z bazą danych platformy Azure Database dla programu MySQL |
| Nazwa hosta | mydemoserver.mysql.database.azure.com | Wcześniej zanotowana nazwa serwera. |
| Port | 3306 | Używany jest domyślny port dla programu MySQL. |
| Nazwa użytkownika | myadmin@mydemoserver | Identyfikator logowania administratora serwera zanotowany przez Ciebie wcześniej. |
| Hasło | **** | Użyj hasła do konta administratora, które zostało ustawione wcześniej. |

Kliknij przycisk **Testuj połączenie**, aby sprawdzić, czy wszystkie parametry zostały prawidłowo skonfigurowane.
Teraz możesz kliknąć połączenie, aby pomyślnie nawiązać połączenie z serwerem.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli te zasoby nie są Ci potrzebne do pracy z innym przewodnikiem Szybki start lub samouczkiem, możesz je usunąć, uruchamiając następujące polecenie: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Jeśli po prostu chcesz usunąć jeden z nowo utworzonych serwerów, możesz uruchomić polecenie **[az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete)**.
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Projektowanie bazy danych MySQL za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-design-database-using-cli.md)
