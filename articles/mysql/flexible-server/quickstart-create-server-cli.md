---
title: 'Szybki start: tworzenie serwera — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL — serwer elastyczny'
description: W tym przewodniku Szybki start opisano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć Azure Database for MySQL elastyczny serwer w grupie zasobów platformy Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7addfc3a0d91b85c4d63afa4ee6a55b5202c3855
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770241"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Szybki start: tworzenie serwera elastycznego Azure Database for MySQL interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start pokazano, jak za pomocą poleceń interfejsu wiersza polecenia platformy [Azure](/cli/azure/get-started-with-azure-cli) Azure Cloud Shell utworzyć Azure Database for MySQL elastyczny serwer w ciągu pięciu minut. [](https://shell.azure.com) Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!IMPORTANT] 
> Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Ta [Azure Cloud Shell](../../cloud-shell/overview.md) to bezpłatna interaktywna powłoka, za pomocą których można wykonać kroki opisane w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz **pozycję** Kopiuj, aby skopiować bloki kodu, wklej je do Cloud Shell i wybierz klawisz **Enter,** aby go uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i używać go lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne

Musisz zalogować się do swojego konta przy użyciu [polecenia az login.](/cli/azure/reference-index#az_login) **Zanotuj właściwość id,** która odnosi się do **identyfikatora subskrypcji** dla konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję w ramach swojego konta za [pomocą polecenia az account set.](/cli/azure/account#az_account_set) Zanotuj wartość **identyfikatora z** danych wyjściowych polecenia **az login,** która ma być wartością **argumentu** subskrypcji w poleceniu . Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [az account list](/cli/azure/account#az_account_list).

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Tworzenie serwera elastycznego

Utwórz [grupę zasobów platformy Azure za](../../azure-resource-manager/management/overview.md) pomocą polecenia , a następnie utwórz elastyczny serwer `az group create` MySQL w tej grupie zasobów. Należy podać unikatową nazwę. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myresourcegroup` w lokalizacji `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Utwórz serwer elastyczny za pomocą `az mysql flexible-server create` polecenia . Serwer może zawierać wiele baz danych. Następujące polecenie tworzy serwer przy użyciu wartości domyślnych i wartości usługi z lokalnego kontekstu interfejsu wiersza [polecenia platformy](/cli/azure/local-context)Azure: 

```azurecli-interactive
az mysql flexible-server create
```

Utworzony serwer ma poniższe atrybuty: 
- Automatycznie wygenerowana nazwa serwera, nazwa użytkownika administratora, hasło administratora, nazwa grupy zasobów (jeśli nie została jeszcze określona w kontekście lokalnym) i w tej samej lokalizacji co grupa zasobów 
- Wartości domyślne usługi dla pozostałych konfiguracji serwera: warstwa obliczeniowa (z możliwością serii), rozmiar obliczeniowy/jednostkę SKU (B1MS), okres przechowywania kopii zapasowych (7 dni) i wersja programu MySQL (5.7)
- Domyślną metodą łączności jest dostęp prywatny (integracja z siecią wirtualną) z automatycznie wygenerowaną siecią wirtualną i podsiecią

> [!NOTE] 
> Metody łączności nie można zmienić po utworzeniu serwera. Na przykład w przypadku wyboru dostępu prywatnego *(integracja* z siecią wirtualną) podczas tworzenia nie można zmienić dostępu na Dostęp publiczny *(dozwolone adresy IP)* po utworzeniu. Zdecydowanie zalecamy utworzenie serwera z dostępem prywatnym, aby bezpiecznie uzyskać dostęp do serwera przy użyciu integracji z siecią wirtualną. Więcej informacji na temat dostępu prywatnego znajduje się w [artykule pojęcia](./concepts-networking.md).

Jeśli chcesz zmienić jakiekolwiek wartości domyślne, zapoznaj się [](/cli/azure/mysql/flexible-server) z dokumentacją interfejsu wiersza polecenia platformy Azure, aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. 

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

Jeśli chcesz zmienić jakiekolwiek wartości domyślne, zapoznaj się [](/cli/azure/mysql/flexible-server) z dokumentacją interfejsu wiersza polecenia platformy Azure, aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. 

## <a name="create-a-database"></a>Tworzenie bazy danych
Uruchom następujące polecenie, aby utworzyć bazę danych **newdatabase,** jeśli nie została jeszcze utworzona.

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

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Nawiązywanie połączenia i testowanie go przy użyciu interfejsu wiersza polecenia platformy Azure

Azure Database for MySQL elastyczny serwer umożliwia nawiązywanie połączenia z serwerem mysql za pomocą polecenia interfejsu wiersza polecenia ```az mysql flexible-server connect``` platformy Azure. To polecenie umożliwia przetestowanie łączności z serwerem bazy danych, utworzenie szybkiej bazy danych startowej i uruchamianie zapytań bezpośrednio na serwerze bez konieczności instalowania aplikacji mysql.exe lub MySQL Workbench.  Można również użyć uruchom polecenie w trybie interaktywnym do uruchamiania wielu zapytań.

Uruchom następujący skrypt, aby przetestować i zweryfikować połączenie z bazą danych ze środowiska projektowego.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Przykład:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
Powinny zostać wyświetlony następujące dane wyjściowe dla pomyślnego połączenia:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Jeśli połączenie nie powiodło się, wypróbuj następujące rozwiązania:
- Sprawdź, czy port 3306 jest otwarty na komputerze klienckim.
- jeśli nazwa użytkownika i hasło administratora serwera są poprawne
- Jeśli skonfigurowano regułę zapory dla maszyny klienckiej
- Jeśli skonfigurowano serwer z dostępem prywatnym w sieci wirtualnej, upewnij się, że maszyna klienta znajduje się w tej samej sieci wirtualnej.

Uruchom następujące polecenie, aby wykonać pojedyncze zapytanie przy użyciu ```--querytext``` argumentu ```-q``` .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Przykład:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Aby dowiedzieć się więcej na temat ```az mysql flexible-server connect``` używania polecenia , zapoznaj się z dokumentacją [dotyczącą nawiązywania połączeń i](connect-azure-cli.md) wykonywania zapytań.

## <a name="connect-using-mysql-command-line-client"></a>Nawiązywanie połączenia przy użyciu klienta wiersza polecenia mysql

Jeśli serwer elastyczny został utworzony przy użyciu dostępu prywatnego (integracja z siecią wirtualną), musisz nawiązać połączenie z serwerem z zasobu w tej samej sieci wirtualnej co serwer. Możesz utworzyć maszynę wirtualną i dodać ją do sieci wirtualnej utworzonej za pomocą serwera elastycznego. Aby dowiedzieć się [więcej, zapoznaj](how-to-manage-virtual-network-portal.md) się z dokumentacją dotyczącą konfigurowania dostępu prywatnego.

Jeśli serwer elastyczny został utworzony przy użyciu dostępu publicznego (dozwolone adresy IP), możesz dodać lokalny adres IP do listy reguł zapory na serwerze. Zapoznaj [się z dokumentacją dotyczącą tworzenia reguł zapory lub zarządzania](how-to-manage-firewall-portal.md) nimi, aby uzyskać wskazówki krok po kroku.

Możesz użyć aplikacji [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) [lub MySQL Workbench,](./connect-workbench.md) aby nawiązać połączenie z serwerem ze środowiska lokalnego. Azure Database for MySQL serwer elastyczny obsługuje łączenie aplikacji klienckich z usługą MySQL przy użyciu protokołu Transport Layer Security (TLS), wcześniej znanego jako Secure Sockets Layer (SSL). TLS to standardowy w branży protokół, który zapewnia szyfrowane połączenia sieciowe między serwerem bazy danych i aplikacjami klienckimi, dzięki czemu można spełnić wymagania dotyczące zgodności. Aby nawiązać połączenie z serwerem elastycznym MySQL, konieczne będzie pobranie publicznego [certyfikatu SSL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) w celu weryfikacji urzędu certyfikacji. Aby dowiedzieć się więcej na temat nawiązywania połączenia za pomocą szyfrowanych połączeń lub wyłączania protokołu SSL, zobacz dokumentację Nawiązywanie połączenia z serwerem Azure Database for MySQL — elastyczny [serwer z zaszyfrowanymi połączeniami.](how-to-connect-tls-ssl.md)

W poniższym przykładzie pokazano, jak nawiązać połączenie z serwerem elastycznym przy użyciu interfejsu wiersza polecenia mysql. Najpierw zainstaluj wiersz polecenia mysql, jeśli nie został jeszcze zainstalowany. Pobierz certyfikat DigiCertGlobalRootCA wymagany dla połączeń SSL. Użyj ustawienia --ssl-mode=REQUIRED parametrów połączenia, aby wymusić weryfikację certyfikatu TLS/SSL. Przekaż ścieżkę pliku certyfikatu lokalnego do parametru --ssl-ca. Zastąp wartości rzeczywistą nazwą serwera i hasłem.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Jeśli serwer elastyczny został zaaprowizowany przy użyciu dostępu **publicznego,** możesz również użyć usługi [Azure Cloud Shell](https://shell.azure.com/bash) do nawiązania połączenia z serwerem elastycznym przy użyciu wstępnie zainstalowanego klienta mysql, jak pokazano poniżej:

Aby połączyć się Azure Cloud Shell serwerem elastycznym za pomocą usługi Azure Cloud Shell, należy zezwolić na dostęp sieciowy do serwera elastycznego. Aby to osiągnąć, możesz  przejść do bloku Sieć na serwerze elastycznym MySQL w usłudze Azure Portal i zaznacz pole wyboru w sekcji Zapora, która zawiera tekst "Zezwalaj na publiczny dostęp z dowolnej usługi platformy Azure na tym serwerze" jak pokazano na poniższym zrzucie ekranu, a następnie kliknij przycisk Zapisz, aby utrwalić to ustawienie. 

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Zrzut ekranu przedstawiający sposób zezwalania na Azure Cloud Shell serwera elastycznego MySQL w celu konfiguracji sieci dostępu publicznego.":::
 
 
> [!NOTE]
> Sprawdzanie **zezwalania na publiczny dostęp z dowolnej usługi platformy Azure** na tym serwerze powinno być używane tylko do testowania lub testowania. Konfiguruje ona zaporę tak, aby zezwalała na połączenia z adresów IP przydzielonych do dowolnej usługi lub zasobu platformy Azure, w tym połączeń z subskrypcji innych klientów.

Kliknij pozycję **Wypróbuj,** aby uruchomić Azure Cloud Shell i użyć następujących poleceń, aby nawiązać połączenie z serwerem elastycznym. Użyj nazwy serwera, nazwy użytkownika i hasła w poleceniu . 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Podczas nawiązywania połączenia z serwerem elastycznym przy użyciu Azure Cloud Shell należy użyć parametru --ssl=true, a nie --ssl-mode=REQUIRED.
> Główną przyczyną jest Azure Cloud Shell ze wstępnie zainstalowanym klientem programu mysql.exe z dystrybucji MariaDB, który wymaga parametru --ssl, a klient mysql z dystrybucji oracle wymaga parametru --ssl-mode.

Jeśli podczas nawiązywania połączenia z serwerem elastycznym po wcześniejszym poleceniu zostanie wyświetlony następujący komunikat o błędzie, oznacza to, że pominięto ustawienie reguły zapory przy użyciu wspomnianej wcześniej opcji "Zezwalaj na dostęp publiczny z dowolnej usługi platformy Azure na tym serwerze" lub opcja nie jest zapisywana. Ponów próbę ustawienia zapory i spróbuj ponownie.

BŁĄD 2002 (HY000): Nie można nawiązać połączenia z serwerem MySQL na <servername> (115)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli te zasoby nie są Ci potrzebne do pracy z innym przewodnikiem Szybki start lub samouczkiem, możesz je usunąć, uruchamiając następujące polecenie:

```azurecli-interactive
az group delete --name myresourcegroup
```

Jeśli chcesz usunąć jeden z nowo utworzonych serwerów, możesz uruchomić `az mysql server delete` polecenie .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Następne kroki

>[!div class="nextstepaction"]
> [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu interfejsu wiersza polecenia platformy Azure](connect-azure-cli.md) 
>  [Nawiązywanie połączenia Azure Database for MySQL — elastyczny serwer z zaszyfrowanymi połączeniami](how-to-connect-tls-ssl.md) 
>  [Tworzenie aplikacji internetowej w języku PHP (Laravel) przy użyciu programu MySQL](tutorial-php-database-app.md)
