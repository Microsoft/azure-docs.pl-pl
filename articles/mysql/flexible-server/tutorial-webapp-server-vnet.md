---
title: 'Samouczek: Tworzenie Azure Database for MySQL elastyczny serwer (wersja zapoznawcza) i Azure App Service aplikacji sieci Web w tej samej sieci wirtualnej'
description: Przewodnik Szybki Start dotyczący tworzenia Azure Database for MySQL elastycznym serwerze (wersja zapoznawcza) przy użyciu aplikacji sieci Web w sieci wirtualnej
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9cbcbeb9d9e216a0b686cba258288db8439e0a9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90946656"
---
# <a name="tutorial-create-an-azure-database-for-mysql---flexible-server-preview-with-app-services-web-app-in-virtual-network"></a>Samouczek: Tworzenie serwera Azure Database for MySQL-elastyczny (wersja zapoznawcza) przy użyciu aplikacji internetowej App Services w sieci wirtualnej

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

W tym samouczku pokazano, jak utworzyć aplikację sieci Web Azure App Service przy użyciu elastycznego serwera MySQL (wersja zapoznawcza) w [sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się na swoje konto za pomocą polecenia [AZ login](https://docs.microsoft.com/cli/azure/reference-index#az-login) . Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp wartość właściwości **Identyfikator subskrypcji** w polu **AZ login** Output for the Subscription do symbolu zastępczego identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Utwórz Azure Database for MySQL elastyczny serwer

Utwórz prywatny, elastyczny serwer w sieci wirtualnej (VNET) przy użyciu następującego polecenia:
```azurecli
az mysql flexible-server create --resource-group myresourcegroup --location westus2
```
To polecenie wykonuje następujące akcje, co może potrwać kilka minut:

- Utwórz grupę zasobów, jeśli jeszcze nie istnieje.
- Generuje nazwę serwera, jeśli nie została dostarczona.
- Utwórz nową sieć wirtualną dla nowego serwera MySQL. Zanotuj nazwę sieci wirtualnej i nazwę podsieci utworzoną dla serwera, ponieważ musisz dodać aplikację sieci Web do tej samej sieci wirtualnej.
- Nie można utworzyć nazwy użytkownika administratora, hasła dla serwera.
- Tworzy pustą bazę danych o nazwie **flexibleserverdb**

> [!NOTE]
> Zanotuj hasło, które zostanie wygenerowane, jeśli nie zostanie podane. Jeśli zapomnisz hasła, należy zresetować hasło przy użyciu ``` az mysql flexible-server update``` polecenia

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

W tej sekcji utworzysz hosta aplikacji w aplikacji App Service i nawiążesz połączenie tej aplikacji z bazą danych MySQL. Upewnij się, że jesteś w katalogu głównym repozytorium kodu aplikacji w terminalu.

Tworzenie aplikacji App Service (proces hosta) za pomocą polecenia AZ webapp up

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku B1 --name mywebapp
```

> [!NOTE]
> - W przypadku argumentu--Location Użyj tej samej lokalizacji co w przypadku bazy danych w poprzedniej sekcji.
> - Zastąp _ &lt; ciąg App-Name>_ unikatową nazwą na wszystkich platformie Azure (punkt końcowy serwera to https:// \<app-name> . azurewebsites.NET). Dozwolone znaki dla nazwy <aplikacji> to A-Z, 0-9 i-. Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.

To polecenie wykonuje następujące akcje, co może potrwać kilka minut:

- Utwórz grupę zasobów, jeśli jeszcze nie istnieje. (W tym poleceniu zostanie użyta ta sama Grupa zasobów, w której wcześniej została utworzona baza danych).
- Utwórz plan App Service ```testappserviceplan``` w warstwie cenowej Basic (B1), jeśli nie istnieje. --jednostki SKU są opcjonalne.
- Utwórz aplikację App Service, jeśli nie istnieje.
- Włącz domyślne rejestrowanie dla aplikacji, jeśli nie została jeszcze włączona.
- Przekaż repozytorium przy użyciu wdrożenia ZIP z włączonym automatyzacją kompilacji.

## <a name="add-the-web-app-to-the-virtual-network"></a>Dodawanie aplikacji sieci Web do sieci wirtualnej

Użyj polecenia **AZ webapp VNET-Integration** , aby dodać integrację regionalnej sieci wirtualnej do WEBAPP. Zastąp wartość Virtual _ &lt; -name>_ _ &lt; i podsiecią_ nazwę sieci wirtualnej i nazwę podsieci, z której korzysta serwer elastyczny.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Konfigurowanie zmiennych środowiskowych w celu nawiązania połączenia z bazą danych

Po wdrożeniu kodu w App Service, następnym krokiem jest połączenie aplikacji z elastycznym serwerem na platformie Azure. Kod aplikacji oczekuje na znalezienie informacji o bazie danych w wielu zmiennych środowiskowych. Aby ustawić zmienne środowiskowe w App Service, tworzysz "Ustawienia aplikacji" przy użyciu ```az webapp config appsettings``` polecenia Set.

```azurecli
az webapp config appsettings set --settings DBHOST="<mysql-server-name>.mysql.database.azure.com" DBNAME="flexibleserverdb" DBUSER="<username>" DBPASS="<password>"
```

- Zastąp ciąg _ &lt; MySQL-Server-Name>_, _ &lt; username>_ i _ &lt; Password>_ dla nowo utworzonego polecenia elastycznego serwera.
- Zastąp _ &lt;>nazwy użytkownika_ i _ &lt; hasła>_ poświadczeniami, które zostały również wygenerowane przez polecenie.
- Nazwa grupy zasobów i aplikacji jest rysowana z wartości pamięci podręcznej w pliku. Azure/config.
- Polecenie tworzy ustawienia o nazwie dbhost, dbname, dbuser i dbpass. Jeśli kod aplikacji używa innej nazwy dla informacji o bazie danych, Użyj tych nazw dla ustawień aplikacji, jak wspomniano w kodzie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Oczyść wszystkie zasoby utworzone w samouczku za pomocą następującego polecenia. To polecenie usuwa wszystkie zasoby w tej grupie zasobów.

```azurecli
az group delete -n myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
