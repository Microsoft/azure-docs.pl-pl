---
title: 'Szybki Start: Tworzenie serwera — interfejs wiersza polecenia platformy Azure-Azure Database for MySQL-elastyczny serwer'
description: W tym przewodniku szybki start opisano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć Azure Database for MySQL elastyczny serwer w grupie zasobów platformy Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a63c6f074178794db38b47950e176dd729344a54
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492732"
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

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Tworzenie serwera elastycznego

Utwórz [grupę zasobów platformy Azure](../../azure-resource-manager/management/overview.md) za pomocą `az group create` polecenia, a następnie utwórz w tej grupie zasobów elastyczny serwer MySQL. Należy podać unikatową nazwę. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Utwórz elastyczny serwer przy użyciu `az mysql flexible-server create` polecenia. Serwer może zawierać wiele baz danych. Następujące polecenie tworzy serwer przy użyciu wartości domyślnych usługi i z [lokalnego kontekstu](/cli/azure/local-context)interfejsu wiersza polecenia platformy Azure: 

```azurecli-interactive
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

## <a name="create-a-database"></a>Tworzenie bazy danych
Uruchom następujące polecenie, aby utworzyć bazę danych, **newdatabase** , jeśli jeszcze jej nie utworzono.

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> Połączenia z usługą Azure Database for MySQL korzystają z portu 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany. W takim przypadku nie będzie można nawiązać połączenia z serwerem, chyba że dział informatyczny otworzy port 3306.

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **fullyQualifiedDomainName** i **administratorLogin**. Poniżej znajduje się przykład danych wyjściowych JSON: 

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

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Łączenie i testowanie połączenia przy użyciu interfejsu wiersza polecenia platformy Azure

Azure Database for MySQL elastyczny serwer umożliwia nawiązanie połączenia z serwerem MySQL za pomocą polecenia interfejsu CLI platformy Azure ```az mysql flexible-server connect``` . To polecenie umożliwia testowanie łączności z serwerem bazy danych, tworzenie szybkiej bazy danych i uruchamianie zapytań bezpośrednio na serwerze bez konieczności instalowania mysql.exe lub MySQL Workbench.  Można również użyć polecenia Uruchom w trybie interaktywnym do uruchamiania wielu zapytań.

Uruchom następujący skrypt, aby przetestować i zweryfikować połączenie z bazą danych ze środowiska deweloperskiego.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Przykład:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
W przypadku pomyślnego nawiązania połączenia powinny zostać wyświetlone następujące dane wyjściowe:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Jeśli nawiązanie połączenia nie powiodło się, wypróbuj następujące rozwiązania:
- Sprawdź, czy na komputerze klienckim jest otwarty port 3306.
- Jeśli nazwa użytkownika i hasło administratora serwera są poprawne
- Jeśli skonfigurowano regułę zapory dla komputera klienckiego
- Jeśli serwer został skonfigurowany z dostępem prywatnym w sieci wirtualnej, upewnij się, że komputer kliencki znajduje się w tej samej sieci wirtualnej.

Uruchom następujące polecenie, aby wykonać pojedyncze zapytanie przy użyciu ```--querytext``` argumentu, ```-q``` .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Przykład:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Aby dowiedzieć się więcej o korzystaniu z ```az mysql flexible-server connect``` polecenia, zapoznaj się z dokumentacją dotyczącą [połączeń i zapytań](connect-azure-cli.md) .

## <a name="connect-using-mysql-command-line-client"></a>Nawiązywanie połączenia przy użyciu klienta wiersza polecenia MySQL

Jeśli serwer elastyczny został utworzony przy użyciu dostępu prywatnego (Integracja z siecią wirtualną), należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do sieci wirtualnej utworzonej przy użyciu elastycznego serwera. Aby dowiedzieć się więcej, zobacz Konfigurowanie [dokumentacji dostępu prywatnego](how-to-manage-virtual-network-portal.md) .

Jeśli serwer elastyczny został utworzony przy użyciu dostępu publicznego (dozwolone adresy IP), można dodać lokalny adres IP do listy reguł zapory na serwerze. Zapoznaj się z dokumentacją dotyczącą [tworzenia reguł zapory lub zarządzania nimi](how-to-manage-firewall-portal.md) , aby uzyskać wskazówki krok po kroku.

Aby nawiązać połączenie z serwerem ze środowiska lokalnego, można użyć opcji [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) lub [MySQL Workbench](./connect-workbench.md) . Azure Database for MySQL elastyczny serwer obsługuje łączenie aplikacji klienckich z usługą MySQL przy użyciu protokołu Transport Layer Security (TLS), wcześniej znanego jako Secure Sockets Layer (SSL). TLS jest standardowym protokołem, który zapewnia szyfrowane połączenia sieciowe między serwerem bazy danych i aplikacjami klienckimi, co pozwala na przestrzeganie wymagań dotyczących zgodności. Aby nawiązać połączenie z serwerem elastycznym MySQL, trzeba będzie pobrać [publiczny certyfikat SSL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) dla weryfikacji urzędu certyfikacji. Aby dowiedzieć się więcej na temat łączenia się z szyfrowanymi połączeniami lub wyłączania protokołu SSL, zapoznaj się z dokumentacją dotyczącą [Azure Database for MySQL łączenia z serwerem elastycznym z zaszyfrowaną](how-to-connect-tls-ssl.md)

Poniższy przykład pokazuje, jak nawiązać połączenie z serwerem elastycznym przy użyciu interfejsu wiersza polecenia MySQL. Najpierw należy zainstalować wiersz polecenia MySQL, jeśli nie został on jeszcze zainstalowany. Zostanie pobrany certyfikat DigiCertGlobalRootCA wymagany dla połączeń SSL. Aby wymusić weryfikację certyfikatu TLS/SSL, użyj ustawienia--SSL-Mode = wymagane parametry połączenia. Przekaż ścieżkę lokalnego pliku certyfikatu do parametru--SSL-ca. Zastąp wartości rzeczywistą nazwą serwera i hasłem.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Jeśli udostępniasz elastyczny serwer przy użyciu **dostępu publicznego**, możesz również użyć [Azure Cloud Shell](https://shell.azure.com/bash) , aby nawiązać połączenie z elastycznym serwerem przy użyciu wstępnie zainstalowanego klienta MySQL, jak pokazano poniżej:

Aby można było używać Azure Cloud Shell do nawiązywania połączenia z serwerem elastycznym, należy zezwolić na dostęp sieciowy z Azure Cloud Shell do serwera elastycznego. Aby to osiągnąć, możesz przejść do bloku **Sieć** na Azure Portal dla serwera elastycznego MySQL, a następnie zaznaczyć pole wyboru w obszarze **Zapora** "Zezwalaj na dostęp publiczny z dowolnej usługi platformy Azure na platformie Azure do tego serwera", jak pokazano na poniższym zrzucie ekranu, a następnie kliknij przycisk Zapisz, aby zachować to ustawienie.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Zrzut ekranu pokazujący, jak zezwolić Azure Cloud Shell dostęp do elastycznego serwera MySQL na potrzeby konfiguracji sieci dostępu publicznego.":::
 
 
> [!NOTE]
> Sprawdzanie **zezwalania na dostęp publiczny z dowolnej usługi platformy Azure na platformie Azure do tego serwera** powinno być używane tylko do celów deweloperskich i testowych. Konfiguruje zaporę tak, aby zezwalała na połączenia z adresów IP przypisanych do dowolnej usługi lub zasobu platformy Azure, w tym połączeń z subskrypcji innych klientów.

Kliknij pozycję **Wypróbuj** , aby uruchomić Azure Cloud Shell i użyć poniższych poleceń, aby nawiązać połączenie z serwerem elastycznym. Użyj nazwy serwera, nazwy użytkownika i hasła w poleceniu. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Podczas nawiązywania połączenia z elastycznym serwerem przy użyciu Azure Cloud Shell należy użyć parametru--SSL = true, a nie--SSL-Mode = REQUIRED.
> Główną przyczyną jest to, że Azure Cloud Shell jest dostarczany ze wstępnie zainstalowanym klientem mysql.exe z dystrybucji MariaDB, która wymaga parametru--SSL, podczas gdy klient MySQL z dystrybucji firmy Oracle wymaga protokołu SSL.

Jeśli zostanie wyświetlony następujący komunikat o błędzie podczas nawiązywania połączenia z elastycznym serwerem po poleceniu wcześniej, pominięto ustawienie reguły zapory przy użyciu opcji "Zezwalaj na dostęp publiczny z dowolnych usług platformy Azure na platformie Azure na ten serwer" lub opcja nie została zapisana. Ponów ustawienie zapory i spróbuj ponownie.

BŁĄD 2002 (HY000): nie można nawiązać połączenia z serwerem MySQL na <servername> (115)

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

>[!div class="nextstepaction"]
> [Nawiązywanie połączenia i wykonywanie zapytań przy użyciu interfejsu wiersza polecenia](connect-azure-cli.md) 
>  platformy Azure [Nawiązywanie połączenia z serwerem elastycznym Azure Database for MySQL z połączeniami](how-to-connect-tls-ssl.md) 
>  szyfrowanymi [Tworzenie aplikacji sieci Web PHP (platformy laravel) za pomocą programu MySQL](tutorial-php-database-app.md)
