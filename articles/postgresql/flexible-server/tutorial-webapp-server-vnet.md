---
title: 'Samouczek: Tworzenie Azure Database for PostgreSQL-elastyczny serwer i Azure App Service aplikacji sieci Web w tej samej sieci wirtualnej'
description: Przewodnik Szybki Start dotyczący tworzenia Azure Database for PostgreSQL-elastyczny serwer z aplikacją sieci Web w sieci wirtualnej
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ab606e357bd911f4d7f266977bd14871f92744a0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546572"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Samouczek: Tworzenie serwera Azure Database for PostgreSQL-elastyczny przy użyciu aplikacji internetowej App Services w sieci wirtualnej

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

W tym samouczku pokazano, jak utworzyć aplikację internetową Azure App Service za pomocą serwera Azure Database for PostgreSQL-elastyczną (wersja zapoznawcza) w [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

W tym samouczku zostanie
>[!div class="checklist"]
> * Tworzenie elastycznego serwera PostgreSQL w sieci wirtualnej
> * Tworzenie aplikacji internetowej
> * Dodawanie aplikacji sieci Web do sieci wirtualnej
> * Nawiązywanie połączenia z usługą Postgres z poziomu aplikacji sieci Web 

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się na swoje konto za pomocą polecenia [AZ login](/cli/azure/authenticate-azure-cli) . Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp wartość właściwości **Identyfikator subskrypcji** w polu **AZ login** Output for the Subscription do symbolu zastępczego identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Utwórz serwer elastyczny PostgreSQL w nowej sieci wirtualnej

Utwórz prywatny, elastyczny serwer w sieci wirtualnej (VNET) przy użyciu następującego polecenia:
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
To polecenie wykonuje następujące akcje, co może potrwać kilka minut:

- Utwórz grupę zasobów, jeśli jeszcze nie istnieje.
- Generuje nazwę serwera, jeśli nie została dostarczona.
- Utwórz nową sieć wirtualną dla nowego serwera postgreSQL. Zanotuj nazwę sieci wirtualnej i nazwę podsieci utworzoną dla serwera, ponieważ musisz dodać aplikację sieci Web do tej samej sieci wirtualnej.
- Nie można utworzyć nazwy użytkownika administratora, hasła dla serwera.
- Tworzy pustą bazę danych o nazwie **Postgres**

> [!NOTE]
> - Zanotuj hasło, które zostanie wygenerowane, jeśli nie zostanie podane. Jeśli zapomnisz hasła, należy zresetować hasło przy użyciu ``` az postgres flexible-server update``` polecenia
> - Jeśli nie używasz App Service Environment, musisz włączyć opcję Zezwalaj na dostęp z adresów IP platformy Azure przy użyciu tego polecenia. 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```


## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej
W tej sekcji utworzysz hosta aplikacji w aplikacji App Service, Połącz tę aplikację z bazą danych Postgres, a następnie wdróż swój kod na tym hoście. Upewnij się, że jesteś w katalogu głównym repozytorium kodu aplikacji w terminalu.

Tworzenie aplikacji App Service (proces hosta) za pomocą polecenia AZ webapp up

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku B1 --name mywebapp
```

> [!NOTE]
> - W przypadku argumentu--Location Użyj tej samej lokalizacji co w przypadku bazy danych w poprzedniej sekcji.
> - Zastąp <App-Name> unikatową nazwą na wszystkich platformie Azure (punkt końcowy serwera to https:// \<app-name> . azurewebsites.NET). Dozwolone znaki dla nazwy <aplikacji> to A-Z, 0-9 i-. Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.

To polecenie wykonuje następujące akcje, co może potrwać kilka minut:

- Utwórz grupę zasobów, jeśli jeszcze nie istnieje. (W tym poleceniu zostanie użyta ta sama Grupa zasobów, w której wcześniej została utworzona baza danych).
- Utwórz plan App Service ```testappserviceplan``` w warstwie cenowej Basic (B1), jeśli nie istnieje. --jednostki SKU są opcjonalne.
- Utwórz aplikację App Service, jeśli nie istnieje.
- Włącz domyślne rejestrowanie dla aplikacji, jeśli nie została jeszcze włączona.
- Przekaż repozytorium przy użyciu wdrożenia ZIP z włączonym automatyzacją kompilacji.

## <a name="add-the-web-app-to-the-virtual-network"></a>Dodawanie aplikacji sieci Web do sieci wirtualnej
Użyj polecenia **AZ webapp VNET-Integration** , aby dodać integrację regionalnej sieci wirtualnej do WEBAPP. Zastąp <nazwę sieci wirtualnej> i <nazwę podsieci> z siecią wirtualną i nazwą podsieci, z której korzysta serwer elastyczny.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Konfigurowanie zmiennych środowiskowych w celu nawiązania połączenia z bazą danych
Po wdrożeniu kodu w App Service, następnym krokiem jest połączenie aplikacji z elastycznym serwerem na platformie Azure. Kod aplikacji oczekuje na znalezienie informacji o bazie danych w wielu zmiennych środowiskowych. Aby ustawić zmienne środowiskowe w App Service, tworzysz "Ustawienia aplikacji" przy użyciu ```az webapp config appsettings``` polecenia Set.

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- Zastąp ```postgres-server-name``` , ```username``` , ```password``` dla nowo utworzonego, elastycznego polecenia serwera.
- Zamień <username> i <password> z poświadczeniami, które zostały również wygenerowane przez polecenie.
- Nazwa grupy zasobów i aplikacji jest rysowana z wartości pamięci podręcznej w pliku. Azure/config.
- Polecenie tworzy ustawienia o nazwach ```DBHOST``` ,, ```DBNAME``` ```DBUSER``` i ```DBPASS``` . Jeśli kod aplikacji używa innej nazwy dla informacji o bazie danych, Użyj tych nazw dla ustawień aplikacji, jak wspomniano w kodzie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Oczyść wszystkie zasoby utworzone w samouczku za pomocą następującego polecenia. To polecenie usuwa wszystkie zasoby w tej grupie zasobów.

```azurecli
az group delete -n myresourcegroup
```


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)