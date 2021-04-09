---
title: 'Samouczek: Projektowanie interfejsu wiersza polecenia platformy Azure Azure Database for MariaDB'
description: W tym samouczku wyjaśniono, jak utworzyć serwer oraz bazę danych usługi Azure Database for MariaDB i zarządzać nimi przy użyciu interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8f6f8d5a2cc9dc17d08486125fc2e44307c1be46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664488"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-azure-cli"></a>Samouczek: projektowanie usługi Azure Database for MariaDB za pomocą interfejsu wiersza polecenia platformy Azure

Usługa Azure Database for MariaDB jest usługą relacyjnej bazy danych w chmurze firmy Microsoft opartą na aparacie bazy danych MariaDB Community Edition. W tym samouczku dowiesz się, jak wykonywać następujące czynności, używając interfejsu wiersza polecenia platformy Azure:

> [!div class="checklist"]
> * Tworzenie usługi Azure Database for MariaDB
> * Konfigurowanie zapory serwera
> * Tworzenie bazy danych za pomocą [narzędzia wiersza polecenia MySQL](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)
> * Ładowanie przykładowych danych
> * Zapytania o dane
> * Aktualizowanie danych
> * Przywracanie danych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana. 

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w której zasób istnieje lub dla której są za niego naliczane opłaty. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account#az-account-set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
[Grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) można utworzyć za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Tworzenie serwera usługi Azure Database for MariaDB
Utwórz serwer usługi Azure Database for MariaDB, używając polecenia `az mariadb server create`. Serwer umożliwia zarządzanie wieloma bazami danych. Zwykle dla każdego projektu lub użytkownika używana jest oddzielna baza danych.

W poniższym przykładzie w regionie `westus` w grupie zasobów `myresourcegroup` jest tworzony serwer usługi Azure Database for MariaDB o nazwie `mydemoserver`. Serwer ma identyfikator logowania administratora o nazwie `myadmin`. Jest to serwer 5. generacji ogólnego przeznaczenia z 2 rdzeniami wirtualnymi. Zastąp zmienną `<server_admin_password>` swoją własną wartością.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```
Wartość parametru sku-name jest zgodna z konwencją {warstwa cenowa}\_{generacja obliczeniowa}\_{rdzenie wirtualne}, jak pokazano w przykładach poniżej:
+ `--sku-name B_Gen5_4` — warstwa podstawowa, 5. generacja, 4 rdzenie wirtualne.
+ `--sku-name GP_Gen5_32` — warstwa ogólnego przeznaczenia, 5. generacja, 32 rdzenie wirtualne.
+ `--sku-name MO_Gen5_2` — warstwa zoptymalizowana pod kątem pamięci, 5. generacja, 2 rdzenie wirtualne.

Aby ustalić poprawne wartości dla poszczególnych regionów i warstw, zapoznaj się z dokumentacją [warstw cenowych](./concepts-pricing-tiers.md).

> [!IMPORTANT]
> Nazwa logowania i hasło administratora serwera określone w tym miejscu będą wymagane do logowania do serwera i jego baz danych w późniejszej części tego przewodnika Szybki start. Zapamiętaj lub zapisz te informacje do wykorzystania w przyszłości.


## <a name="configure-firewall-rule"></a>Konfigurowanie reguły zapory
Utwórz regułę zapory na poziomie serwera usługi Azure Database for MariaDB, używając polecenia `az mariadb server firewall-rule create`. Reguła zapory na poziomie serwera pozwala aplikacji zewnętrznej, takiej jak narzędzie wiersza polecenia **mysql** lub program MySQL Workbench, na nawiązywanie połączeń z Twoim serwerem przez zaporę usługi Azure MariaDB.

Poniższy przykład powoduje utworzenie reguły zapory o nazwie `AllowMyIP`, która zezwala na połączenia z określonego adresu IP – 192.168.0.1. Zastąp ten adres adresem IP lub zakresem adresów IP odpowiednim dla urządzeń, z których będzie nawiązywane połączenie.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu.
```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **fullyQualifiedDomainName** i **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
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

## <a name="connect-to-the-server-using-mysql"></a>Nawiązywanie połączenia z serwerem za pomocą narzędzia wiersza polecenia mysql
Nawiąż połączenie z serwerem usługi Azure Database for MariaDB za pomocą [narzędzia wiersza polecenia mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html). W tym przykładzie polecenie to:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Tworzenie pustej bazy danych
Po nawiązaniu połączenia z serwerem Utwórz pustą bazę danych.
```sql
mysql> CREATE DATABASE mysampledb;
```

W wierszu polecenia uruchom poniższe polecenie, aby przełączyć połączenie na tę nowo utworzoną bazę danych:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Tworzenie tabel w bazie danych
Teraz, gdy wiesz, jak nawiązać połączenie z bazą danych Azure Database for MariaDB, wykonaj niektóre podstawowe zadania.

Najpierw utwórz tabelę i załaduj do niej dane. Utwórz tabelę z informacjami o spisie.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Ładowanie danych do tabel
Teraz, gdy masz tabelę, wstaw do niej dane. W otwartym oknie wiersza polecenia uruchom następujące zapytanie, aby wstawić wiersze danych.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

W utworzonej wcześniej tabeli masz teraz dwa wiersze przykładowych danych.

## <a name="query-and-update-the-data-in-the-tables"></a>Wykonywanie zapytania względem danych w tabelach i aktualizowanie tych danych
Wykonaj następujące zapytanie, aby pobrać informacje z tabeli bazy danych.
```sql
SELECT * FROM inventory;
```

Możesz także zaktualizować dane w tabelach.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Wiersz jest aktualizowany podczas pobierania danych.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Przywracanie bazy danych do określonego punktu w czasie
Załóżmy, że ta tabela została przypadkowo usunięta. W takiej sytuacji niełatwo jest odzyskać dane. Usługa Azure Database for MariaDB umożliwia powrót do dowolnego punktu w czasie w ciągu maksymalnie 35 ostatnich dni i przywrócenie tego punktu w czasie do nowego serwera. Przy użyciu tego nowego serwera można odzyskać usunięte dane. Poniższa procedura opisuje przywrócenie przykładowego serwera do punktu przed dodaniem tabeli.

Do wykonania przywrócenia potrzebne będą następujące informacje:

- Punkt przywracania: wybierz punkt w czasie przed zmianą serwera. Musi mieć wartość większą od lub równą wartości Najstarsza kopia zapasowa źródłowej bazy danych.
- Serwer docelowy: podaj nazwę nowego serwera, do którego chcesz wykonać przywrócenie
- Serwer źródłowy: podaj nazwę serwera, z którego chcesz wykonać przywrócenie
- Lokalizacja: nie można wybrać regionu; domyślnie wartość jest taka sama jak w przypadku serwera źródłowego

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

Polecenie `az mariadb server restore` wymaga podania następujących parametrów:

| Ustawienie | Sugerowana wartość | Opis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Grupa zasobów, w której istnieje serwer źródłowy.  |
| name | mydemoserver-restored | Nazwa nowego serwera utworzonego za pomocą polecenie przywracania. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Wybierz punkt w czasie, do którego ma zostać przeprowadzone przywrócenie. Ta data i godzina musi przypadać w okresie przechowywania kopii zapasowej serwera źródłowego. Użyj daty i godziny w formacie ISO8601. Na przykład możesz użyć własnej lokalnej strefy czasowej, takiej jak `2017-04-13T05:59:00-08:00`, lub użyć formatu UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Nazwa lub identyfikator serwera źródłowego, z którego ma zostać przeprowadzone przywrócenie. |

Przywrócenie serwera do określonego punktu w czasie powoduje utworzenie nowego serwera skopiowanego na podstawie oryginalnego serwera z określonego punktu w czasie. Wartości lokalizacji i warstwy cenowej dla przywróconego serwera są takie same, jak w przypadku serwera źródłowego.

Polecenie jest synchroniczne i zostanie zakończone po przywróceniu serwera. Po zakończeniu przywracania zlokalizuj nowy serwer, który został utworzony. Sprawdź, czy dane zostały przywrócone zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie serwera usługi Azure Database for MariaDB
> * Konfigurowanie zapory serwera
> * Tworzenie bazy danych za pomocą [narzędzia wiersza polecenia MySQL](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)
> * Ładowanie przykładowych danych
> * Zapytania o dane
> * Aktualizowanie danych
> * Przywracanie danych