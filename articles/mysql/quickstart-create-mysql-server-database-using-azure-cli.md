---
title: 'Szybki Start: Tworzenie serwera — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL'
description: W tym przewodniku Szybki start opisano, jak utworzyć serwer usługi Azure Database for MySQL w grupie zasobów platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 4f774351fc0eaaf32069687a1943c72b74b6308a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96494328"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Szybki Start: Tworzenie serwera Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure

> [!TIP]
> Rozważ użycie prostszego polecenia [AZ MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI (obecnie w wersji zapoznawczej). Wypróbuj [Przewodnik Szybki Start](./quickstart-create-server-up-azure-cli.md).

W tym przewodniku szybki start pokazano, jak za pomocą poleceń [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli) w [Azure Cloud Shell](https://shell.azure.com) utworzyć serwer Azure Database for MySQL w ciągu pięciu minut. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ten przewodnik Szybki Start wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

 - Wybierz określoną subskrypcję na koncie za pomocą polecenia [AZ Account Set](/cli/azure/account) . Zanotuj wartość **identyfikatora** z polecenia **AZ login** Output to use jako wartość argumentu **Subscription** w poleceniu. Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [AZ Account List](/cli/azure/account#az-account-list).

   ```azurecli
   az account set --subscription <subscription id>
   ```

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL
Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia [AZ Group Create](/cli/azure/group) , a następnie utwórz serwer MySQL w tej grupie zasobów. Należy podać unikatową nazwę. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Utwórz serwer usługi Azure Database for MySQL za pomocą polecenia [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create). Serwer może zawierać wiele baz danych.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

Poniżej znajdują się szczegółowe informacje dotyczące argumentów: 

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Wprowadź unikatową nazwę serwera Azure Database for MySQL. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | myresourcegroup | Podaj nazwę grupy zasobów platformy Azure.
location | westus | Lokalizacja platformy Azure dla serwera.
admin-user | myadmin | Nazwa użytkownika w przypadku logowania administratora. Nie może być to **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.
admin-password | *bezpieczne hasło* | Hasło użytkownika administratora. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.
sku-name|GP_Gen5_2|Wprowadź nazwę warstwy cenowej i konfiguracji obliczeniowej. Zgodnie z konwencją {warstwa cenowa}_{generacja obliczeniowa}_{rdzenie wirtualne} w skrócie. Aby uzyskać więcej informacji, zobacz [warstwy cenowe](./concepts-pricing-tiers.md) .

>[!IMPORTANT] 
>- Domyślną wersją programu MySQL na serwerze jest 5,7. Obecnie dostępne są również wersje 5,6 i 8,0.
>- Aby wyświetlić wszystkie argumenty dla polecenia **AZ MySQL Server Create** , zobacz ten [dokument referencyjny](/cli/azure/mysql/server#az-mysql-server-create).
>- Protokół SSL jest domyślnie włączony na serwerze. Aby uzyskać więcej infroamtion na temat protokołu SSL, zobacz [Konfigurowanie łączności SSL](howto-configure-ssl.md)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera 
Domyślnie tworzony nowy serwer jest chroniony przy użyciu reguł zapory i nie jest dostępny publicznie. Regułę zapory można skonfigurować na serwerze za pomocą polecenia [AZ MySQL Server firewall-Rule Create](/cli/azure/mysql/server/firewall-rule) . Pozwoli to na nawiązywanie połączenia z serwerem lokalnie.

Poniższy przykład powoduje utworzenie reguły zapory o nazwie `AllowMyIP`, która zezwala na połączenia z określonego adresu IP – 192.168.0.1. Zastąp adres IP, z którego będziesz się łączyć. W razie konieczności można użyć zakresu adresów IP. Nie wiem, jak szukać adresu IP, a następnie przejdź do pozycji, [https://whatismyipaddress.com/](https://whatismyipaddress.com/) Aby uzyskać adres IP.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Połączenia z usługą Azure Database for MySQL korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W takim przypadku nie będzie można nawiązać połączenia z serwerem, chyba że dział informatyczny otworzy port 3306.

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

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Nawiązywanie połączenia z serwerem Azure Database for MySQL przy użyciu klienta wiersza polecenia MySQL
Możesz połączyć się z serwerem przy użyciu popularnego narzędzia klienckiego **[mysql.exe](https://dev.mysql.com/downloads/)** narzędzia wiersza polecenia z [Azure Cloud Shell](../cloud-shell/overview.md). Alternatywnie można użyć wiersza polecenia MySQL w środowisku lokalnym.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli te zasoby nie są Ci potrzebne do pracy z innym przewodnikiem Szybki start lub samouczkiem, możesz je usunąć, uruchamiając następujące polecenie: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Jeśli po prostu chcesz usunąć jeden z nowo utworzonych serwerów, możesz uruchomić polecenie [az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete).

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Tworzenie aplikacji PHP w systemie Windows za pomocą programu MySQL](../app-service/tutorial-php-mysql-app.md)