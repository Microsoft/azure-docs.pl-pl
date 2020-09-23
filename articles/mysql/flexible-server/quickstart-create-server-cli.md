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
ms.openlocfilehash: bae6e9f04eced02130ae628d5308a87a1baaa8fa
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947778"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Szybki Start: Tworzenie Azure Database for MySQL elastycznego serwera przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start pokazano, jak za pomocą poleceń [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) w [Azure Cloud Shell](https://shell.azure.com) utworzyć Azure Database for MySQL elastyczny serwer w ciągu pięciu minut. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!IMPORTANT] 
> Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wklej je do Cloud Shell i wybierz **klawisz ENTER** , aby go uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Wymagania wstępne

Musisz zalogować się na swoje konto za pomocą polecenia [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) . Zanotuj Właściwość **ID** , która odwołuje się do **identyfikatora subskrypcji** dla Twojego konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję na koncie za pomocą polecenia [AZ Account Set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) . Zanotuj wartość **identyfikatora** z polecenia **AZ login** Output to use jako wartość argumentu **Subscription** w poleceniu. Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [AZ Account List](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Tworzenie serwera elastycznego

Utwórz [grupę zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) za pomocą `az group create` polecenia, a następnie utwórz w tej grupie zasobów elastyczny serwer MySQL. Należy podać unikatową nazwę. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Utwórz elastyczny serwer przy użyciu `az mysql flexible-server create` polecenia. Serwer może zawierać wiele baz danych. Następujące polecenie tworzy serwer przy użyciu wartości domyślnych usługi i z [lokalnego kontekstu](https://docs.microsoft.com/cli/azure/local-context?view=azure-cli-latest)interfejsu wiersza polecenia platformy Azure: 

```azurecli
az mysql flexible-server create
```

Utworzony serwer ma następujące atrybuty: 
- Automatycznie wygenerowana nazwa serwera, nazwa użytkownika administratora, hasło administratora, nazwę grupy zasobów (jeśli nie została jeszcze określona w kontekście lokalnym) i w tej samej lokalizacji, w której znajduje się grupa zasobów 
- Ustawienia domyślne usługi dla pozostałych konfiguracji serwera: warstwa obliczeniowa (z możliwością przetwarzania), rozmiar obliczeń/jednostka SKU (B1MS), okres przechowywania kopii zapasowych (7 dni) i wersja programu MySQL (5,7)
- Domyślną metodą połączenia jest dostęp prywatny (Integracja sieci wirtualnej) z automatycznie wygenerowaną siecią wirtualną i podsiecią

> [!NOTE] 
> Nie można zmienić metody łączności po utworzeniu serwera. Na przykład jeśli wybrano opcję *dostęp prywatny (Integracja z siecią wirtualną)* podczas tworzenia, nie można zmienić *dostępu publicznego (dozwolone adresy IP)* po utworzeniu. Zdecydowanie zalecamy utworzenie serwera z dostępem prywatnym, aby bezpiecznie uzyskać dostęp do serwera przy użyciu integracji sieci wirtualnej. Dowiedz się więcej o prywatnym dostępie w [artykule pojęcia](./concepts-networking.md).

Jeśli chcesz zmienić ustawienia domyślne, zapoznaj się z dokumentacją interfejsu wiersza polecenia platformy Azure <!--FIXME --> Aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. 

> [!NOTE]
> Połączenia z usługą Azure Database for MySQL korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W takim przypadku nie będzie można nawiązać połączenia z serwerem, chyba że dział informatyczny otworzy port 3306.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **fullyQualifiedDomainName** i **administratorLogin**.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "name": "Standard_B1ms",
    "size": null,
    "tier": "Burstable"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-using-mysql-command-line-client"></a>Nawiązywanie połączenia przy użyciu klienta wiersza polecenia MySQL

Ponieważ serwer elastyczny został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)*, należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do utworzonej sieci wirtualnej. 

Po utworzeniu maszyny wirtualnej można do niej wykonać protokół SSH i zainstalować popularne narzędzie klienckie, **[mysql.exe](https://dev.mysql.com/downloads/)** narzędzie wiersza polecenia.

W mysql.exe Połącz się przy użyciu poniższego polecenia. Zastąp wartości rzeczywistą nazwą serwera i hasłem. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

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