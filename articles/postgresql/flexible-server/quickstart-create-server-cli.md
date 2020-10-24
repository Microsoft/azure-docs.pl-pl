---
title: 'Szybki Start: Tworzenie serwera — interfejs wiersza polecenia platformy Azure-Azure Database for PostgreSQL-elastyczny serwer'
description: W tym przewodniku szybki start opisano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć Azure Database for PostgreSQL elastyczny serwer w grupie zasobów platformy Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc
ms.openlocfilehash: b0c0fd1b540251b5a7dfefde5fc33adb2813bb8e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490071"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Szybki Start: Tworzenie Azure Database for PostgreSQL elastycznego serwera przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start pokazano, jak za pomocą poleceń [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) w [Azure Cloud Shell](https://shell.azure.com) utworzyć Azure Database for PostgreSQL elastyczny serwer w ciągu pięciu minut. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!IMPORTANT] 
> Azure Database for PostgreSQL elastyczny serwer jest obecnie w wersji zapoznawczej.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wklej je do Cloud Shell i wybierz **klawisz ENTER** , aby go uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne

Musisz zalogować się na swoje konto za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) . Zanotuj Właściwość **ID** , która odwołuje się do **identyfikatora subskrypcji** dla Twojego konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję na koncie za pomocą polecenia [AZ Account Set](/cli/azure/account#az-account-set) . Zanotuj wartość **identyfikatora** z polecenia **AZ login** Output to use jako wartość argumentu **Subscription** w poleceniu. Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [AZ Account List](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Tworzenie serwera elastycznego

Utwórz [grupę zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) za pomocą `az group create` polecenia, a następnie utwórz serwer elastyczny PostgreSQL wewnątrz tej grupy zasobów. Należy podać unikatową nazwę. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Utwórz elastyczny serwer przy użyciu `az postgres flexible-server create` polecenia. Serwer może zawierać wiele baz danych. Następujące polecenie tworzy serwer przy użyciu wartości domyślnych usługi i z [lokalnego kontekstu](/cli/azure/local-context)interfejsu wiersza polecenia platformy Azure: 

```azurecli
az postgres flexible-server create
```

Utworzony serwer ma następujące atrybuty: 
- Automatycznie wygenerowana nazwa serwera, nazwa użytkownika administratora, hasło administratora, nazwę grupy zasobów (jeśli nie została jeszcze określona w kontekście lokalnym) i w tej samej lokalizacji, w której znajduje się grupa zasobów 
- Ustawienia domyślne usługi dla pozostałych konfiguracji serwera: warstwa obliczeniowa (Ogólnego przeznaczenia), rozmiar obliczeń/jednostka SKU (D2s_v3-2 rdzeń wirtualny, 8 GB pamięci RAM), okres przechowywania kopii zapasowych (7 dni) i wersja PostgreSQL (12)
- Domyślną metodą połączenia jest dostęp prywatny (Integracja sieci wirtualnej) z automatycznie wygenerowaną siecią wirtualną i podsiecią

> [!NOTE] 
> Nie można zmienić metody łączności po utworzeniu serwera. Na przykład jeśli wybrano opcję *dostęp prywatny (Integracja z siecią wirtualną)* podczas tworzenia, nie można zmienić *dostępu publicznego (dozwolone adresy IP)* po utworzeniu. Zdecydowanie zalecamy utworzenie serwera z dostępem prywatnym, aby bezpiecznie uzyskać dostęp do serwera przy użyciu integracji sieci wirtualnej. Dowiedz się więcej o prywatnym dostępie w [artykule pojęcia](./concepts-networking.md).

Jeśli chcesz zmienić ustawienia domyślne, zapoznaj się z dokumentacją interfejsu wiersza polecenia platformy Azure <!--FIXME --> Aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. 

> [!NOTE]
> Połączenia z Azure Database for PostgreSQLą komunikację przez port 5432. Jeśli spróbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący przez port 5432 może być niedozwolony. W takim przypadku nie można nawiązać połączenia z serwerem, chyba że dział IT otworzy port 5432.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu.

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **fullyQualifiedDomainName** i **administratorLogin**.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>Nawiązywanie połączenia przy użyciu klienta wiersza polecenia PostgreSQL

Ponieważ serwer elastyczny został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)*, należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do utworzonej sieci wirtualnej. 

Po utworzeniu maszyny wirtualnej można do niej wykonać protokół SSH i zainstalować narzędzie wiersza polecenia **[PSQL](https://www.postgresql.org/download/)** .

W programie PSQL Połącz się przy użyciu poniższego polecenia. Zastąp wartości rzeczywistą nazwą serwera i hasłem. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli te zasoby nie są Ci potrzebne do pracy z innym przewodnikiem Szybki start lub samouczkiem, możesz je usunąć, uruchamiając następujące polecenie:

```azurecli-interactive
az group delete --name myresourcegroup
```

Jeśli po prostu chcesz usunąć jeden z nowo utworzonych serwerów, możesz uruchomić `az postgres flexible-server delete` polecenie.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Wdrażanie aplikacji Django przy użyciu App Service i PostgreSQL](tutorial-django-app-service-postgres.md)