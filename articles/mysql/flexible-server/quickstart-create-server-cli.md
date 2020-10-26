---
title: 'Szybki Start: Tworzenie serwera — interfejs wiersza polecenia platformy Azure-Azure Database for MySQL-elastyczny serwer'
description: W tym przewodniku szybki start opisano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć Azure Database for MySQL elastyczny serwer w grupie zasobów platformy Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc
ms.openlocfilehash: 4be295ada476e4dc41a86d06908ef1d653a3bad8
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545025"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Szybki Start: Tworzenie Azure Database for MySQL elastycznego serwera przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start pokazano, jak za pomocą poleceń [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli) w [Azure Cloud Shell](https://shell.azure.com) utworzyć Azure Database for MySQL elastyczny serwer w ciągu pięciu minut. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!IMPORTANT] 
> Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej

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

Utwórz [grupę zasobów platformy Azure](../../azure-resource-manager/management/overview.md) za pomocą `az group create` polecenia, a następnie utwórz w tej grupie zasobów elastyczny serwer MySQL. Należy podać unikatową nazwę. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Utwórz elastyczny serwer przy użyciu `az mysql flexible-server create` polecenia. Serwer może zawierać wiele baz danych. Następujące polecenie tworzy serwer przy użyciu wartości domyślnych usługi i z [lokalnego kontekstu](/cli/azure/local-context)interfejsu wiersza polecenia platformy Azure: 

```azurecli
az mysql flexible-server create
```

Utworzony serwer ma następujące atrybuty: 
- Automatycznie wygenerowana nazwa serwera, nazwa użytkownika administratora, hasło administratora, nazwę grupy zasobów (jeśli nie została jeszcze określona w kontekście lokalnym) i w tej samej lokalizacji, w której znajduje się grupa zasobów 
- Ustawienia domyślne usługi dla pozostałych konfiguracji serwera: warstwa obliczeniowa (z możliwością przetwarzania), rozmiar obliczeń/jednostka SKU (B1MS), okres przechowywania kopii zapasowych (7 dni) i wersja programu MySQL (5,7)
- Domyślną metodą połączenia jest dostęp prywatny (Integracja sieci wirtualnej) z automatycznie wygenerowaną siecią wirtualną i podsiecią

> [!NOTE] 
> Nie można zmienić metody łączności po utworzeniu serwera. Na przykład jeśli wybrano opcję *dostęp prywatny (Integracja z siecią wirtualną)* podczas tworzenia, nie można zmienić *dostępu publicznego (dozwolone adresy IP)* po utworzeniu. Zdecydowanie zalecamy utworzenie serwera z dostępem prywatnym, aby bezpiecznie uzyskać dostęp do serwera przy użyciu integracji sieci wirtualnej. Dowiedz się więcej o prywatnym dostępie w [artykule pojęcia](./concepts-networking.md).

Jeśli chcesz zmienić ustawienia domyślne, zapoznaj się z [dokumentacją](/cli/azure/mysql/flexible-server) interfejsu wiersza polecenia platformy Azure, aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. 

Poniżej przedstawiono przykładowe dane wyjściowe: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Jeśli chcesz zmienić ustawienia domyślne, zapoznaj się z [dokumentacją](/cli/azure/mysql/flexible-server) interfejsu wiersza polecenia platformy Azure, aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. 

> [!NOTE]
> Połączenia z usługą Azure Database for MySQL korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W takim przypadku nie będzie można nawiązać połączenia z serwerem, chyba że dział informatyczny otworzy port 3306.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **fullyQualifiedDomainName** i **administratorLogin** . Poniżej znajduje się przykład danych wyjściowych JSON: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-using-mysql-command-line-client"></a>Nawiązywanie połączenia przy użyciu klienta wiersza polecenia MySQL

Ponieważ serwer elastyczny został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)* , należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do utworzonej sieci wirtualnej. 

Po utworzeniu maszyny wirtualnej można do niej wykonać protokół SSH i zainstalować popularne narzędzie klienckie, **[mysql.exe](https://dev.mysql.com/downloads/)** narzędzie wiersza polecenia.

W mysql.exe Połącz się przy użyciu poniższego polecenia. Zastąp wartości rzeczywistą nazwą serwera i hasłem. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli te zasoby nie są Ci potrzebne do pracy z innym przewodnikiem Szybki start lub samouczkiem, możesz je usunąć, uruchamiając następujące polecenie:

```azurecli-interactive
az group delete --name myresourcegroup
```

Jeśli po prostu chcesz usunąć jeden z nowo utworzonych serwerów, możesz uruchomić `az mysql server delete` polecenie.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Tworzenie aplikacji sieci Web PHP (platformy laravel) za pomocą programu MySQL](tutorial-php-database-app.md)