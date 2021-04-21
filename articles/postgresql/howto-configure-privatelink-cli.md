---
title: Private Link — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak skonfigurować link prywatny dla usługi Azure Database for PostgreSQL — pojedynczy serwer z interfejsu wiersza polecenia platformy Azure
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 410da33b531524f4a6458df13a89807fedd739a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773337"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Tworzenie aplikacji i zarządzanie nimi Private Link dla Azure Database for PostgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia

Prywatny punkt końcowy to podstawowy blok blokowy linku prywatnego na platformie Azure. Dzięki temu zasoby platformy Azure, takie Virtual Machines (maszyny wirtualne), mogą komunikować się prywatnie z zasobami linków prywatnych. W tym artykule dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć maszynę wirtualną w usłudze Azure Virtual Network i jeden Azure Database for PostgreSQL z prywatnym punktem końcowym platformy Azure.

> [!NOTE]
> Funkcja łącza prywatnego jest dostępna tylko dla Azure Database for PostgreSQL w warstwach cenowych Ogólnego przeznaczenia lub Zoptymalizowane pod kątem pamięci. Upewnij się, że serwer bazy danych znajduje się w jednej z tych warstw cenowych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby przejść przez ten przewodnik, potrzebne są:

- Serwer [Azure Database for PostgreSQL i baza danych](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem zasobu należy utworzyć grupę zasobów do hostowania Virtual Network. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W tym przykładzie grupę zasobów o *nazwie myResourceGroup tworzy* się w *lokalizacji westeurope:*

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz nową Virtual Network za pomocą [az network vnet create](/cli/azure/network/vnet). W tym przykładzie jest domyślną Virtual Network *o nazwie myVirtualNetwork* z jedną podsiecią *o nazwie mySubnet:*

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Wyłączanie zasad prywatnego punktu końcowego podsieci 
Platforma Azure wdraża zasoby w podsieci w sieci wirtualnej, więc musisz utworzyć lub zaktualizować podsieć, aby wyłączyć zasady sieci prywatnego [punktu końcowego.](../private-link/disable-private-endpoint-network-policy.md) Zaktualizuj konfigurację podsieci o nazwie *mySubnet za* pomocą [az network vnet subnet update:](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej 
Utwórz maszynę wirtualną za pomocą az vm create. Po wyświetleniu monitu podaj hasło, które będzie używane jako poświadczenia logowania dla maszyny wirtualnej. W tym przykładzie podano tworzenie maszyny *wirtualnej o nazwie myVm:* 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

 Zanotuj publiczny adres IP maszyny wirtualnej. Użyjesz tego adresu do nawiązania połączenia z maszyną wirtualną z Internetu w następnym kroku.

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Tworzenie serwera Azure Database for PostgreSQL — pojedynczy serwer 
Utwórz Azure Database for PostgreSQL za pomocą polecenia az postgres server create. Pamiętaj, że nazwa serwera PostgreSQL musi być unikatowa na platformie Azure, dlatego zastąp wartość symbolu zastępczego własnymi unikatowymi wartościami użytymi powyżej: 

```azurecli-interactive
# Create a server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

## <a name="create-the-private-endpoint"></a>Tworzenie prywatnego punktu końcowego 
Utwórz prywatny punkt końcowy dla serwera PostgreSQL w Virtual Network: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforPostgreSQL/servers" --query "id" -o tsv) \    
    --group-id postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurowanie strefy Prywatna strefa DNS danych 
Utwórz domenę Prywatna strefa DNS Zone for PostgreSQL i utwórz link skojarzenia z Virtual Network. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Nazwa FQDN w ustawieniu DNS klienta nie jest rozpoznawana jako skonfigurowany prywatny adres IP. Należy skonfigurować strefę DNS dla skonfigurowanej nazwy FQDN, jak pokazano [poniżej.](../dns/dns-operations-recordsets-portal.md)

> [!NOTE]
> W niektórych przypadkach Azure Database for PostgreSQL i podsieć sieci wirtualnej znajdują się w różnych subskrypcjach. W takich przypadkach należy zapewnić następujące konfiguracje:
> - Upewnij się, że w obu subskrypcjach zarejestrowano dostawcę zasobów **Microsoft.DBforPostgreSQL.** Aby uzyskać więcej informacji, zobacz [dostawcy zasobów.](../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną *wirtualną myVm* z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wpisz *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz pozycję **Pobierz plik RDP**. Platforma Azure tworzy Remote Desktop Protocol *(rdp)* i pobiera go na komputer.

1. Otwórz plik *downloaded.rdp.*

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może być konieczne wybranie opcji **Więcej opcji** Użyj innego konta , aby określić poświadczenia wprowadzone podczas tworzenia maszyny  >  wirtualnej.

1. Wybierz pozycję **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Prywatny dostęp do serwera PostgreSQL z maszyny wirtualnej

1. W Pulpit zdalny *myVM* otwórz program PowerShell.

2. Wprowadź  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

   Zostanie wyświetlony komunikat podobny do tego:

   ```azurepowershell
   Server:  UnKnown
   Address:  168.63.129.16
   Non-authoritative answer:
   Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
   Address:  10.1.3.4
   ```

3. Przetestuj połączenie łącza prywatnego dla serwera PostgreSQL przy użyciu dowolnego dostępnego klienta. W poniższym przykładzie użyto [narzędzia Azure Data Studio](/sql/azure-data-studio/download) do wykonać operację.

4. W **nowe połączenie**, wprowadź lub wybierz te informacje:

   | Ustawienie | Wartość |
   | ------- | ----- |
   | Typ serwera| Wybierz **pozycję PostgreSQL.**|
   | Nazwa serwera| Wybierz *mydemopostgresserver.privatelink.postgres.database.azure.com* |
   | Nazwa użytkownika | Wprowadź nazwę użytkownika username@servername podaną podczas tworzenia serwera PostgreSQL. |
   |Hasło |Wprowadź hasło podane podczas tworzenia serwera PostgreSQL. |
   |Protokół SSL|Wybierz **pozycję Wymagane.**|
   ||

5. Wybierz pozycję Połącz.

6. Przeglądaj bazy danych z menu po lewej stronie.

7. (Opcjonalnie) Tworzenie informacji z serwera postgreSQL i wykonywanie dotyczących ich zapytań.

8. Zamknij połączenie pulpitu zdalnego z myVm.

## <a name="clean-up-resources"></a>Czyszczenie zasobów 
Gdy grupa zasobów i wszystkie jej zasoby nie będą już potrzebne, można je usunąć za pomocą funkcji az group delete: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [tym, co to jest prywatny punkt końcowy platformy Azure](../private-link/private-endpoint-overview.md)
