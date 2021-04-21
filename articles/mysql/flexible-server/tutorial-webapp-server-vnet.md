---
title: 'Samouczek: tworzenie Azure Database for MySQL serwera elastycznego (wersja zapoznawcza) i Azure App Service web app w tej samej sieci wirtualnej'
description: Przewodnik Szybki start dotyczący tworzenia Azure Database for MySQL serwera elastycznego (wersja zapoznawcza) przy użyciu aplikacji internetowej w sieci wirtualnej
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 13baf8f033338e242610d7b8c4eec14806cd5ec5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770025"
---
# <a name="tutorial-create-an-azure-database-for-mysql---flexible-server-preview-with-app-services-web-app-in-virtual-network"></a>Samouczek: tworzenie serwera Azure Database for MySQL — serwer elastyczny (wersja zapoznawcza) przy użyciu App Services web app w sieci wirtualnej

> [!IMPORTANT]
> Azure Database for MySQL — serwer elastyczny jest obecnie w publicznej wersji zapoznawczej.

W tym samouczku przedstawiono sposób tworzenia aplikacji Azure App Service web app z serwerem elastycznym MySQL (wersja zapoznawcza) w sieci [wirtualnej.](../../virtual-network/virtual-networks-overview.md)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
>[!div class="checklist"]
> * Tworzenie serwera elastycznego MySQL w sieci wirtualnej
> * Utwórz podsieć do delegowania do App Service
> * Tworzenie aplikacji internetowej
> * Dodawanie aplikacji internetowej do sieci wirtualnej
> * Nawiązywanie połączenia z usługą Postgres z aplikacji internetowej 

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej lokalnie. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się do swojego konta przy użyciu [polecenia az login.](/cli/azure/reference-index#az_login) Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp właściwość **subscription ID** z danych **wyjściowych az login** subskrypcji symbolem zastępczym identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Tworzenie serwera Azure Database for MySQL elastycznego

Utwórz prywatny serwer elastyczny w sieci wirtualnej za pomocą następującego polecenia:
```azurecli
az mysql flexible-server create --resource-group myresourcegroup --location westus2
```
Skopiuj wartości parametrów połączenia i nazwy nowo utworzonej sieci wirtualnej. To polecenie wykonuje następujące akcje, które mogą potrwać kilka minut:

- Utwórz grupę zasobów, jeśli jeszcze nie istnieje.
- Generuje nazwę serwera, jeśli nie zostanie podany.
- Utwórz nową sieć wirtualną dla nowego serwera MySQL. Zanotuj nazwę sieci wirtualnej i nazwę podsieci utworzone dla serwera, ponieważ musisz dodać aplikację internetową do tej samej sieci wirtualnej.
- Tworzy nazwę użytkownika administratora , hasło dla serwera, jeśli nie zostanie podane.
- Tworzy pustą bazę danych **o nazwie flexibleserverdb**

> [!NOTE]
> Zanotuj hasło, które zostanie wygenerowane automatycznie, jeśli nie zostanie podane. Jeśli zapomnisz hasła, musisz zresetować hasło za pomocą ``` az mysql flexible-server update``` polecenia

## <a name="create-subnet-for-app-service-endpoint"></a>Tworzenie podsieci dla punktu końcowego App Service końcowego
Teraz musimy mieć podsieć delegowaną do punktu końcowego App Service Web App. Uruchom następujące polecenie, aby utworzyć nową podsieć w tej samej sieci wirtualnej co serwer bazy danych. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
Zanotuj nazwę sieci wirtualnej i nazwę podsieci po tym poleceniu, ponieważ będzie ono potrzebne do dodania reguły integracji sieci wirtualnej dla aplikacji internetowej po jej utworzeniu. 

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

W tej sekcji utworzysz hosta aplikacji w App Service i połączysz tę aplikację z bazą danych MySQL. Upewnij się, że jesteś w katalogu głównym repozytorium kodu aplikacji w terminalu.

Utwórz aplikację App Service (proces hosta) za pomocą polecenia az webapp up

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - Dla argumentu --location użyj tej samej lokalizacji, co w przypadku bazy danych w poprzedniej sekcji.
> - Zastąp _&lt; nazwę aplikacji>_ unikatową nazwą na całej platformie Azure (punkt końcowy serwera jest https:// \<app-name> .azurewebsites.net). Dozwolone znaki <nazwa aplikacji> to A–Z, 0–9 i -. Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.
> - App Service Podstawowa nie obsługuje integracji z siecią wirtualną. Użyj wersji Standardowa lub Premium. 

To polecenie wykonuje następujące akcje, które mogą potrwać kilka minut:

- Utwórz grupę zasobów, jeśli jeszcze nie istnieje. (W tym poleceniu użyj tej samej grupy zasobów, w której wcześniej utworzono bazę danych).
- Utwórz plan App Service ```testappserviceplan``` w warstwie cenowej Podstawowa (B1), jeśli nie istnieje. --plan i --sku są opcjonalne.
- Utwórz aplikację App Service, jeśli nie istnieje.
- Włącz rejestrowanie domyślne dla aplikacji, jeśli nie zostało jeszcze włączone.
- Przekaż repozytorium przy użyciu wdrożenia zip z włączoną automatyzacją kompilacji.

## <a name="add-the-web-app-to-the-virtual-network"></a>Dodawanie aplikacji internetowej do sieci wirtualnej

Użyj **polecenia az webapp vnet-integration,** aby dodać regionalną integrację sieci wirtualnej do aplikacji internetowej. Zastąp _&lt; nazwę sieci wirtualnej>_ i nazwę _&lt; podsieci_ nazwą sieci wirtualnej i podsieci, z których korzysta serwer elastyczny.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Konfigurowanie zmiennych środowiskowych w celu połączenia z bazą danych

Po wdrożeniu kodu w usłudze App Service następnym krokiem jest połączenie aplikacji z serwerem elastycznym na platformie Azure. Kod aplikacji oczekuje znalezienia informacji o bazie danych w wielu zmiennych środowiskowych. Aby ustawić zmienne środowiskowe w App Service, należy utworzyć "ustawienia aplikacji" za pomocą ```az webapp config appsettings``` polecenia set.

```azurecli
az webapp config appsettings set --settings DBHOST="<mysql-server-name>.mysql.database.azure.com" DBNAME="flexibleserverdb" DBUSER="<username>" DBPASS="<password>"
```

- Zastąp _&lt; nazwę mysql-server-name>_, nazwę użytkownika _&lt;>_ i hasło _&lt;>_ nowo utworzonego polecenia serwera elastycznego.
- Zastąp _&lt;>_ _&lt; i hasło>_ poświadczeniami wygenerowanymi również przez polecenie.
- Nazwa grupy zasobów i aplikacji są rysowane na podstawie buforowanych wartości w pliku .azure/config.
- Polecenie tworzy ustawienia o nazwach DBHOST, DBNAME, DBUSER i DBPASS. Jeśli kod aplikacji używa innej nazwy dla informacji o bazie danych, użyj tych nazw dla ustawień aplikacji, jak wspomniano w kodzie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wyczyść wszystkie zasoby utworzone w samouczku za pomocą następującego polecenia. To polecenie usuwa wszystkie zasoby w tej grupie zasobów.

```azurecli
az group delete -n myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
