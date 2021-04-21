---
title: 'Szybki start: tworzenie serwera — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — pojedynczy serwer'
description: W tym przewodniku Szybki start utworzysz serwer usługi Azure Database for PostgreSQL pomocą interfejsu wiersza polecenia platformy Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a595d677cf0964083526cb7e2c73471148be0fd4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778436"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Szybki start: tworzenie serwera Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start pokazano, jak za pomocą poleceń interfejsu wiersza polecenia platformy [Azure](/cli/azure/get-started-with-azure-cli) [Azure Cloud Shell](https://shell.azure.com) utworzyć pojedynczy serwer Azure Database for PostgreSQL w ciągu pięciu minut. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

    > [!TIP]
    >  Rozważ użycie prostszego [polecenia az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) interfejsu wiersza polecenia platformy Azure, które jest obecnie dostępne w wersji zapoznawczej. Wypróbuj przewodnik [Szybki start.](./quickstart-create-server-up-azure-cli.md)

- Wybierz identyfikator określonej subskrypcji w ramach swojego konta, używając [polecenia az account set.](/cli/azure/account)

    - Zanotuj wartość **identyfikatora** z danych wyjściowych **polecenia az login,** która ma być wartością **argumentu** subskrypcji w poleceniu . 

        ```azurecli
        az account set --subscription <subscription id>
        ```

    - Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać wszystkie subskrypcje, użyj [az account list](/cli/azure/account#az_account_list).

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia [az group create,](/cli/azure/group#az_group_create) a następnie utwórz serwer PostgreSQL w tej grupie zasobów. Należy podać unikatową nazwę. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Utwórz serwer [Azure Database for PostgreSQL za](overview.md) pomocą [polecenia az postgres server create.](/cli/azure/postgres/server) Serwer może zawierać wiele baz danych.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Poniżej znajdują się szczegółowe informacje dotyczące poprzednich argumentów: 

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Unikatowa nazwa identyfikująca Azure Database for PostgreSQL serwera. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków. Aby uzyskać więcej informacji, [zobacz Azure Database for PostgreSQL Naming Rules (Reguły nazewnictwa).](../azure-resource-manager/management/resource-name-rules.md#microsoftdbforpostgresql)
resource-group | myresourcegroup | Nazwa grupy zasobów platformy Azure.
location | westus | Lokalizacja platformy Azure dla serwera.
admin-user | myadmin | Nazwa użytkownika logowania administratora. Nie można go używać w **azure_superuser**, **administratora,** **administratora,** **głównego,** **gościa** ani **publicznego**.
admin-password | *bezpieczne hasło* | Hasło użytkownika administratora. Musi zawierać od 8 do 128 znaków z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.
sku-name|GP_Gen5_2| Nazwa warstwy cenowej i konfiguracji obliczeń. W skrócie postępuj zgodnie z konwencją {warstwa cenowa}_{generacja obliczeń}_{rdzenie wirtualne}. Aby uzyskać więcej informacji, [zobacz Azure Database for PostgreSQL cennika](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- Domyślna wersja serwera PostgreSQL to 9.6. Aby wyświetlić wszystkie obsługiwane wersje, zobacz Obsługiwane wersje główne [postgreSQL.](./concepts-supported-versions.md)
>- Aby wyświetlić wszystkie argumenty polecenia **az postgres server create,** zobacz [ten dokument referencyjny](/cli/azure/postgres/server#az_postgres_server_create).
>- Protokół SSL jest domyślnie włączony na serwerze. Aby uzyskać więcej informacji na temat protokołu SSL, zobacz [Konfigurowanie łączności SSL.](./concepts-ssl-connection-security.md)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera 
Domyślnie utworzony serwer nie jest publicznie dostępny i jest chroniony za pomocą reguł zapory. Reguły zapory można skonfigurować na serwerze za pomocą polecenia [az postgres server firewall-rule create,](/cli/azure/postgres/server/firewall-rule) aby udzielić środowisku lokalnemu dostępu do nawiązywania połączenia z serwerem. 

Poniższy przykład powoduje utworzenie reguły zapory o nazwie `AllowMyIP`, która zezwala na połączenia z określonego adresu IP – 192.168.0.1. Zastąp adres IP lub zakres adresów IP odpowiadający miejscu, z którego będziesz nawiązywać połączenie. Jeśli nie znasz swojego adresu IP, przejdź do [WhatIsMyIPAddress.com,](https://whatismyipaddress.com/) aby go uzyskać.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Aby uniknąć problemów z łącznością, upewnij się, że zapora sieciowa zezwala na port 5432. Azure Database for PostgreSQL używają tego portu. 

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, podaj informacje o hoście i poświadczenia dostępu.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **administratorLogin** i **fullyQualifiedDomainName.**

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Nawiązywanie połączenia z serwerem Azure Database for PostgreSQL przy użyciu narzędzia psql
Klient [psql](https://www.postgresql.org/docs/current/static/app-psql.html) to popularny wybór podczas nawiązywania połączenia z serwerami PostgreSQL. Połączenie z serwerem można nawiązać za pomocą narzędzia psql [z](../cloud-shell/overview.md)Azure Cloud Shell . Narzędzia psql można również używać w środowisku lokalnym, jeśli jest dostępne. Pusta baza danych **postgres** jest tworzona automatycznie przy użyciu nowego serwera PostgreSQL. Możesz użyć tej bazy danych do nawiązania połączenia z psql, jak pokazano w poniższym kodzie. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Jeśli wolisz użyć ścieżki URL do nawiązania połączenia z postgres, zakoduj adres URL znaku @ w nazwie użytkownika za pomocą . `%40` Na przykład parametrów połączenia dla psql będą:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli nie potrzebujesz tych zasobów w innym przewodniku Szybki start lub samouczku, możesz je usunąć, uruchamiając następujące polecenie. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Jeśli chcesz tylko usunąć jeden z nowo utworzonych serwerów, możesz uruchomić [polecenie az postgres server delete.](/cli/azure/postgres/server)

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)
