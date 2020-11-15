---
title: Link prywatny — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — pojedynczy serwer
description: Informacje o konfigurowaniu prywatnego linku do Azure Database for PostgreSQL-pojedynczego serwera z poziomu interfejsu wiersza polecenia platformy Azure
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8aaebdd37f835201ef549e3f97e0c0b657e4fe9
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636218"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Tworzenie prywatnego linku do Azure Database for PostgreSQL-pojedynczego serwera przy użyciu interfejsu wiersza polecenia i zarządzanie nim

Prywatny punkt końcowy to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Umożliwia ona korzystanie z zasobów platformy Azure, takich jak Virtual Machines (VM), w celu komunikacji z prywatnymi zasobami łączy prywatnych. W tym artykule dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć maszynę wirtualną w usłudze Azure Virtual Network i Azure Database for PostgreSQL pojedynczym serwerze przy użyciu prywatnego punktu końcowego platformy Azure.

> [!NOTE]
> Funkcja Link prywatny jest dostępna tylko dla serwerów Azure Database for PostgreSQL w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer bazy danych znajduje się w jednej z tych warstw cenowych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby krokowo poprowadzić ten przewodnik, musisz:

- [Serwer Azure Database for PostgreSQL i baza danych](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem dowolnego zasobu należy utworzyć grupę zasobów, która będzie hostować Virtual Network. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Ten przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *westeurope* :

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz Virtual Network za pomocą [AZ Network VNET Create](/cli/azure/network/vnet). W tym przykładzie tworzony jest domyślny Virtual Network o nazwie *myVirtualNetwork* z jedną podsiecią o nazwie Moja *podsieć* :

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Wyłącz zasady prywatnego punktu końcowego podsieci 
Platforma Azure wdraża zasoby w podsieci w sieci wirtualnej, dlatego należy utworzyć lub zaktualizować podsieć w celu wyłączenia [zasad sieci](../private-link/disable-private-endpoint-network-policy.md)prywatnych punktów końcowych. Zaktualizuj konfigurację podsieci o nazwie Moja *podsieć* za pomocą elementu [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej 
Utwórz maszynę wirtualną za pomocą AZ VM Create. Po wyświetleniu monitu podaj hasło, które będzie używane jako poświadczenia logowania dla maszyny wirtualnej. Ten przykład tworzy maszynę wirtualną o nazwie *myVm* : 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

 Zanotuj publiczny adres IP maszyny wirtualnej. Użyjesz tego adresu do nawiązania połączenia z maszyną wirtualną z Internetu w następnym kroku.

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Tworzenie Azure Database for PostgreSQL-pojedynczego serwera 
Utwórz Azure Database for PostgreSQL za pomocą polecenia AZ Postgres Server Create. Należy pamiętać, że nazwa serwera PostgreSQL musi być unikatowa w obrębie platformy Azure, więc Zastąp wartość symbolu zastępczego własnymi unikatowymi wartościami, które zostały użyte powyżej: 

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

## <a name="configure-the-private-dns-zone"></a>Konfigurowanie strefy Prywatna strefa DNS 
Utwórz strefę Prywatna strefa DNS dla domeny serwera PostgreSQL i Utwórz link powiązania z Virtual Network. 

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
> Nazwa FQDN w ustawieniu DNS klienta nie jest rozpoznawana jako prywatny adres IP skonfigurowany. Konieczne będzie skonfigurowanie strefy DNS dla skonfigurowanej nazwy FQDN, jak pokazano [poniżej](../dns/dns-operations-recordsets-portal.md).

> [!NOTE]
> W niektórych przypadkach Azure Database for PostgreSQL i podsieć wirtualna znajdują się w różnych subskrypcjach. W takich przypadkach należy zapewnić następujące konfiguracje:
> - Upewnij się, że w subskrypcji jest zarejestrowany dostawca zasobów **Microsoft. DBforPostgreSQL** . Aby uzyskać więcej informacji, zapoznaj się z [dostawcami zasobów](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną *myVm* z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wpisz *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz pozycję **Pobierz plik RDP**. Platforma Azure tworzy plik Remote Desktop Protocol ( *RDP* ) i pobiera go na komputer.

1. Otwórz *pobrany plik RDP* .

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta** , aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Wybierz pozycję **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Dostęp do serwera PostgreSQL z maszyny wirtualnej do prywatnego

1. W Pulpit zdalny *myVM* Otwórz program PowerShell.

2. Wprowadź  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

   Zostanie wyświetlony komunikat podobny do tego:

   ```azurepowershell
   Server:  UnKnown
   Address:  168.63.129.16
   Non-authoritative answer:
   Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
   Address:  10.1.3.4
   ```

3. Przetestuj połączenie prywatne linku dla serwera PostgreSQL przy użyciu dowolnego dostępnego klienta. Poniższy przykład używa [usługi Azure Data Studio](/sql/azure-data-studio/download?view=sql-server-ver15&preserve-view=true) do wykonania operacji.

4. W obszarze **nowe połączenie** wprowadź lub wybierz następujące informacje:

   | Ustawienie | Wartość |
   | ------- | ----- |
   | Typ serwera| Wybierz pozycję **PostgreSQL**.|
   | Nazwa serwera| Wybierz *mydemopostgresserver.privatelink.Postgres.Database.Azure.com* |
   | Nazwa użytkownika | Wprowadź nazwę użytkownika, username@servername która jest dostępna podczas tworzenia serwera PostgreSQL. |
   |Hasło |Wprowadź hasło podane podczas tworzenia serwera PostgreSQL. |
   |Protokół SSL|Wybierz pozycję **wymagane**.|
   ||

5. Wybierz pozycję Połącz.

6. Przeglądaj bazy danych z menu po lewej stronie.

7. Zdefiniować Utwórz lub Zbadaj informacje z serwera postgreSQL.

8. Zamknij połączenie pulpitu zdalnego z myVm.

## <a name="clean-up-resources"></a>Czyszczenie zasobów 
Gdy nie jest już potrzebne, można użyć polecenie AZ Group Delete, aby usunąć grupę zasobów i wszystkie jej zasoby: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o tym, [co to jest prywatny punkt końcowy platformy Azure](../private-link/private-endpoint-overview.md)
