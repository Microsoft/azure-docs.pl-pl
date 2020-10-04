---
title: 'Szybki Start: Tworzenie serwera — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — pojedynczy serwer'
description: W tym przewodniku szybki start utworzysz serwer Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: df7c27fb0aca6a9b903d29ea4414832fb677b3eb
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91705266"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Szybki Start: Tworzenie serwera Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start pokazano, jak za pomocą poleceń [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) w [Azure Cloud Shell](https://shell.azure.com) utworzyć pojedynczy serwer Azure Database for PostgreSQL w ciągu pięciu minut. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

> [!TIP]
> Rozważ użycie prostszego polecenia [AZ Postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI, które jest obecnie dostępne w wersji zapoznawczej. Wypróbuj [Przewodnik Szybki Start](./quickstart-create-server-up-azure-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne
Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się do konta za pomocą polecenia [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) . Zanotuj Właściwość **ID** , która odwołuje się do **identyfikatora subskrypcji** dla Twojego konta platformy Azure. 

```azurecli-interactive
az login
```

Wybierz określony identyfikator subskrypcji na koncie przy użyciu polecenia  [AZ Account Set](/cli/azure/account) . Zanotuj wartość **identyfikatora** z polecenia **AZ login** Output, aby użyć jako wartości argumentu **subskrypcji** w poleceniu. 

```azurecli
az account set --subscription <subscription id>
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać wszystkie subskrypcje, użyj [AZ Account List](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) , a następnie utwórz serwer PostgreSQL w ramach tej grupy zasobów. Należy podać unikatową nazwę. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Utwórz [serwer Azure Database for PostgreSQL](overview.md) za pomocą polecenia [AZ Postgres Server Create](/cli/azure/postgres/server) . Serwer może zawierać wiele baz danych.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Poniżej znajdują się szczegółowe informacje dotyczące powyższych argumentów: 

**Ustawienie** | **Wartość przykładowa** | **Opis**
---|---|---
name | mydemoserver | Unikatowa nazwa identyfikująca serwer Azure Database for PostgreSQL. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
resource-group | myresourcegroup | Nazwa grupy zasobów platformy Azure.
location | westus | Lokalizacja platformy Azure dla serwera.
admin-user | myadmin | Nazwa użytkownika dla logowania administratora. Nie może to być **azure_superuser**, **admin**, **administrator**, **root**, **Guest**ani **Public**.
admin-password | *bezpieczne hasło* | Hasło administratora. Musi zawierać od 8 do 128 znaków z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry i znaki inne niż alfanumeryczne.
sku-name|GP_Gen5_2| Nazwa warstwy cenowej i konfiguracji obliczeniowej. Postępuj zgodnie z Konwencją {warstwa cenowa}_{Generation COMPUTE}_{rdzeni wirtualnych} w postaci skróconej. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- Domyślna wersja PostgreSQL na serwerze to 9,6. Aby wyświetlić wszystkie obsługiwane wersje, zobacz [obsługiwane wersje główne PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).
>- Aby wyświetlić wszystkie argumenty dla polecenia **AZ Postgres Server Create** , zobacz [ten dokument referencyjny](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create).
>- Protokół SSL jest domyślnie włączony na serwerze. Aby uzyskać więcej informacji na temat protokołu SSL, zobacz [Konfigurowanie łączności SSL](./concepts-ssl-connection-security.md).

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera 
Domyślnie utworzony serwer nie jest publicznie dostępny i jest chroniony przy użyciu reguł zapory. Reguły zapory można skonfigurować na serwerze za pomocą polecenia [AZ Postgres Server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) , aby zapewnić dostęp do środowiska lokalnego do łączenia się z serwerem. 

Poniższy przykład powoduje utworzenie reguły zapory o nazwie `AllowMyIP`, która zezwala na połączenia z określonego adresu IP – 192.168.0.1. Zastąp adres IP lub zakres adresów IP, które odnoszą się do lokalizacji, z której będziesz się łączyć. Jeśli nie znasz adresu IP, przejdź do [whatismyipaddress.com](https://whatismyipaddress.com/) , aby go pobrać.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Aby uniknąć problemów z łącznością, upewnij się, że Zapora sieci zezwala na port 5432. Serwery Azure Database for PostgreSQL używają tego portu. 

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, podaj informacje o hoście i poświadczenia dostępu.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **administratorLogin** i **fullyQualifiedDomainName** .

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

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Nawiązywanie połączenia z serwerem Azure Database for PostgreSQL przy użyciu PSQL
Klient [PSQL](https://www.postgresql.org/docs/current/static/app-psql.html) to popularny wybór służący do nawiązywania połączeń z serwerami PostgreSQL. Możesz nawiązać połączenie z serwerem za pomocą PSQL z [Azure Cloud Shell](../cloud-shell/overview.md). Możesz również użyć PSQL w środowisku lokalnym, jeśli jest dostępny. Pusta baza danych, **Postgres**, jest tworzona automatycznie przy użyciu nowego serwera PostgreSQL. Tej bazy danych można użyć do nawiązania połączenia z usługą PSQL, jak pokazano w poniższym kodzie. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Jeśli wolisz używać ścieżki URL w celu nawiązania połączenia z usługą Postgres, adres URL Koduj znak @ w nazwie użytkownika za pomocą `%40` . Na przykład parametry połączenia dla PSQL:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli nie potrzebujesz tych zasobów dla innego przewodnika Szybki start lub samouczka, możesz je usunąć, uruchamiając następujące polecenie. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Jeśli chcesz po prostu usunąć jeden z nowo utworzonych serwerów, możesz uruchomić polecenie [AZ Postgres Server Delete](/cli/azure/postgres/server) .

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)
> 
> [Wdrażanie aplikacji sieci Web Django za pomocą PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Nawiązywanie połączenia z aplikacją Node.JS](./connect-nodejs.md)

