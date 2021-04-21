---
title: 'Szybki start: tworzenie serwera — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — serwer elastyczny'
description: W tym przewodniku Szybki start opisano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć Azure Database for PostgreSQL Elastyczny serwer w grupie zasobów platformy Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 320457365a36825564154c36ad843ef665fc2d2c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791557"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Szybki start: tworzenie serwera elastycznego Azure Database for PostgreSQL pomocą interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start pokazano, jak za pomocą poleceń interfejsu wiersza polecenia platformy [Azure](/cli/azure/get-started-with-azure-cli) [Azure Cloud Shell](https://shell.azure.com) utworzyć Azure Database for PostgreSQL elastyczny serwer w ciągu pięciu minut. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!IMPORTANT] 
> Azure Database for PostgreSQL serwer elastyczny jest obecnie w wersji zapoznawczej.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Ta [Azure Cloud Shell](../../cloud-shell/overview.md) to bezpłatna interaktywna powłoka, za pomocą których można wykonać kroki opisane w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz **pozycję** Kopiuj, aby skopiować bloki kodu, wklej je do Cloud Shell i wybierz klawisz **Enter,** aby go uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i używać go lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne

Musisz zalogować się do swojego konta przy użyciu [polecenia az login.](/cli/azure/reference-index#az_login) **Zanotuj właściwość id,** która odwołuje się do **identyfikatora subskrypcji** konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję w ramach swojego konta za pomocą [polecenia az account set.](/cli/azure/account#az_account_set) Zanotuj wartość **identyfikatora** z danych wyjściowych **polecenia az login,** która będzie wartością **argumentu** subskrypcji w poleceniu . Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Tworzenie serwera elastycznego

Utwórz [grupę zasobów platformy Azure za](../../azure-resource-manager/management/overview.md) pomocą polecenia , a następnie utwórz elastyczny serwer `az group create` PostgreSQL w tej grupie zasobów. Należy podać unikatową nazwę. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Utwórz serwer elastyczny za pomocą `az postgres flexible-server create` polecenia . Serwer może zawierać wiele baz danych. Następujące polecenie tworzy serwer przy użyciu wartości domyślnych i wartości usługi z lokalnego kontekstu interfejsu wiersza [polecenia platformy Azure:](/cli/azure/local-context) 

```azurecli
az postgres flexible-server create
```

Utworzony serwer ma poniższe atrybuty: 
- Automatycznie wygenerowana nazwa serwera, nazwa użytkownika administratora, hasło administratora, nazwa grupy zasobów (jeśli nie została jeszcze określona w kontekście lokalnym) i w tej samej lokalizacji co grupa zasobów 
- Wartości domyślne usługi dla pozostałych konfiguracji serwera: warstwa obliczeniowa (Ogólnego przeznaczenia), rozmiar obliczeniowy/jednostkę SKU (D2s_v3 — 2 rdzenie wirtualne, 8 GB pamięci RAM), okres przechowywania kopii zapasowych (7 dni) i wersja PostgreSQL (12)
- Domyślną metodą łączności jest dostęp prywatny (integracja z siecią wirtualną) z automatycznie wygenerowaną siecią wirtualną i podsiecią

> [!NOTE] 
> Nie można zmienić metody łączności po utworzeniu serwera. Jeśli na przykład podczas tworzenia wybrano opcję Dostęp prywatny *(integracja* z siecią wirtualną), nie można zmienić opcji na Dostęp publiczny *(dozwolone adresy IP)* po utworzeniu. Zdecydowanie zalecamy utworzenie serwera z dostępem prywatnym w celu bezpiecznego uzyskiwania dostępu do serwera przy użyciu integracji z siecią wirtualną. Więcej informacji na temat dostępu prywatnego znajduje się [w artykule z pojęciami](./concepts-networking.md).

Jeśli chcesz zmienić ustawienia domyślne, zapoznaj się z dokumentacją interfejsu wiersza polecenia platformy Azure <!--FIXME --> Aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. 

> [!NOTE]
> Połączenia z Azure Database for PostgreSQL komunikują się za pośrednictwem portu 5432. Jeśli spróbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący przez port 5432 może być niedozwolone. W takim przypadku nie można nawiązać połączenia z serwerem, chyba że dział IT otworzy port 5432.

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

Ponieważ serwer elastyczny został utworzony z dostępem prywatnym *(integracja* z siecią wirtualną), musisz nawiązać połączenie z serwerem z zasobu w tej samej sieci wirtualnej co serwer. Możesz utworzyć maszynę wirtualną i dodać ją do utworzonej sieci wirtualnej. 

Po utworzeniu maszyny wirtualnej możesz na maszynie nawiązyć sieć SSH i zainstalować narzędzie wiersza polecenia **[psql.](https://www.postgresql.org/download/)**

Za pomocą narzędzia psql połącz się przy użyciu poniższego polecenia. Zastąp wartości rzeczywistą nazwą serwera i hasłem. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli te zasoby nie są Ci potrzebne do pracy z innym przewodnikiem Szybki start lub samouczkiem, możesz je usunąć, uruchamiając następujące polecenie:

```azurecli-interactive
az group delete --name myresourcegroup
```

Jeśli chcesz usunąć jeden z nowo utworzonych serwerów, możesz uruchomić `az postgres flexible-server delete` polecenie .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Wdrażanie aplikacji Django przy użyciu App Service i PostgreSQL](tutorial-django-app-service-postgres.md)
