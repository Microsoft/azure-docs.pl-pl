---
title: 'Samouczek: Nawiązywanie połączenia z serwerem Azure SQL przy użyciu prywatnego punktu końcowego platformy Azure — interfejs wiersza polecenia platformy Azure'
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak utworzyć serwer Azure SQL z prywatnym punktem końcowym przy użyciu interfejsu wiersza polecenia platformy Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 998b52a23894dcb7fa08b2c0fd42e4ef8e3678ae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554993"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Samouczek: Nawiązywanie połączenia z serwerem Azure SQL przy użyciu prywatnego punktu końcowego platformy Azure — interfejs wiersza polecenia platformy Azure

Prywatny punkt końcowy platformy Azure to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Dzięki temu zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się z prywatnymi prywatnymi zasobami.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz sieć wirtualną i hosta bastionu.
> * Tworzy maszynę wirtualną.
> * Utwórz serwer Azure SQL i prywatny punkt końcowy.
> * Przetestuj łączność z prywatnym punktem końcowym programu SQL Server.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zaloguj się do Azure Portal i sprawdź, czy Twoja subskrypcja jest aktywna, uruchamiając `az login` .
* Sprawdź wersję interfejsu wiersza polecenia platformy Azure w terminalu lub oknie poleceń, uruchamiając polecenie `az --version` . Aby uzyskać najnowszą wersję, zapoznaj się z [najnowszymi informacjami o wersji](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Jeśli nie masz najnowszej wersji, zaktualizuj instalację, postępując zgodnie z [instrukcją instalacji systemu operacyjnego lub platformy](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [AZ Group Create](/cli/azure/group#az_group_create):

* O nazwie **CreateSQLEndpointTutorial-RG**. 
* W lokalizacji **wschodniego** .

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Tworzenie sieci wirtualnej i hosta bastionu

W tej sekcji utworzysz sieć wirtualną, podsieć i hosta bastionu. 

Host bastionu zostanie użyty do nawiązania bezpiecznego połączenia z maszyną wirtualną w celu przetestowania prywatnego punktu końcowego.

Utwórz sieć wirtualną za pomocą [AZ Network VNET Create](/cli/azure/network/vnet#az_network_vnet_create)

* O nazwie **myVNet**.
* Prefiks adresu **10.0.0.0/16**.
* Podsieć o nazwie **myBackendSubnet**.
* Prefiks podsieci **10.0.0.0/24**.
* W grupie zasobów **CreateSQLEndpointTutorial-RG** .
* Lokalizacja **wschodniego**.

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Zaktualizuj podsieć, aby wyłączyć zasady sieci prywatnego punktu końcowego dla prywatnego punktu końcowego za pomocą usługi [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) , aby utworzyć publiczny adres IP dla hosta bastionu:

* Utwórz strefę Standard nadmiarowy publiczny adres IP o nazwie **myBastionIP**.
* W **CreateSQLEndpointTutorial-RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Użyj [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) , aby utworzyć podsieć bastionu:

* O nazwie **AzureBastionSubnet**.
* Prefiks adresu **10.0.1.0/24**.
* W sieci wirtualnej **myVNet**.
* W grupie zasobów **CreateSQLEndpointTutorial-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Użyj [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) , aby utworzyć hosta bastionu:

* O nazwie **myBastionHost**.
* W **CreateSQLEndpointTutorial-RG**.
* Skojarzone z publicznym adresem IP **myBastionIP**.
* Skojarzone z **myVNet** sieci wirtualnej.
* W lokalizacji **wschodniego** .

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Wdrożenie hosta usługi Azure bastionu może potrwać kilka minut.

## <a name="create-test-virtual-machine"></a>Utwórz testową maszynę wirtualną

W tej sekcji utworzysz maszynę wirtualną, która będzie używana do testowania prywatnego punktu końcowego.

Utwórz maszynę wirtualną za pomocą [AZ VM Create](/cli/azure/vm#az_vm_create). Po wyświetleniu monitu podaj hasło, które będzie używane jako poświadczenia dla maszyny wirtualnej:

* O nazwie **myVM**.
* W **CreateSQLEndpointTutorial-RG**.
* W usłudze Network **myVNet**.
* W podsieci **myBackendSubnet**.
* **Win2019Datacenter** obrazu serwera.

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-an-azure-sql-server"></a>Tworzenie serwera SQL Azure

W tej sekcji utworzysz program SQL Server i bazę danych.

Użyj [AZ SQL Server Create](/cli/azure/sql/server#az_sql_server_create) , aby utworzyć serwer SQL:

* Zamień **\<sql-server-name>** na unikatową nazwę serwera.
* Zamień **\<your-password>** na hasło.
* W **CreateSQLEndpointTutorial-RG**.
* W regionie **wschodnim** .

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Użyj [AZ SQL DB Create](/cli/azure/sql/db#az_sql_db_create) , aby utworzyć bazę danych:

* Nazywana **baza danych**.
* W **CreateSQLEndpointTutorial-RG**.
* Zamień **\<sql-server-name>** na unikatową nazwę serwera.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Utwórz prywatny punkt końcowy

W tej sekcji utworzysz prywatny punkt końcowy.

Użyj [AZ SQL Server list](/cli/azure/sql/server#az_sql_server_list) , aby umieścić identyfikator zasobu programu SQL Server w zmiennej powłoki.

Użyj [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) , aby utworzyć punkt końcowy i połączenie:

* O nazwie **myPrivateEndpoint**.
* W grupie zasobów **CreateSQLEndpointTutorial-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* Połączenie o nazwie Moje **połączenie**.

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Skonfiguruj prywatną strefę DNS

W tej sekcji utworzysz i skonfigurujesz prywatną strefę DNS za pomocą polecenia [AZ Network Private-DNS Zone Create](/cli/azure/ext/privatedns/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Aby utworzyć łącze sieci wirtualnej do strefy DNS, użyj [AZ Network Private-DNS link VNET Create](/cli/azure/ext/privatedns/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) .

Utworzysz grupę strefy DNS za pomocą [AZ Network Private-Endpoint systemu DNS Create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Strefa o nazwie **privatelink.Database.Windows.NET**
* W sieci wirtualnej **myVNet**.
* W grupie zasobów **CreateSQLEndpointTutorial-RG**.
* Łącze DNS o nazwie **myDNSLink**.
* Skojarzone z **myPrivateEndpoint**.
* Grupa stref o nazwie Moja **strefa**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>Testowanie łączności z prywatnym punktem końcowym

W tej sekcji użyjesz maszyny wirtualnej utworzonej w poprzednim kroku, aby nawiązać połączenie z programem SQL Server w ramach prywatnego punktu końcowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 
 
2. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów** .

3. Wybierz pozycję **CreateSQLEndpointTutorial-RG**.

4. Wybierz pozycję **myVM**.

5. Na stronie Przegląd dla **myVM** wybierz pozycję **Połącz** , a następnie **bastionu**.

6. Wybierz przycisk **bastionu Użyj** niebieska.

7. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

8. Po nawiązaniu połączenia Otwórz program Windows PowerShell na serwerze.

9. Wprowadź `nslookup <sqlserver-name>.database.windows.net`. Zamień **\<sqlserver-name>** na nazwę serwera SQL, który został utworzony w poprzednich krokach.  Zostanie wyświetlony komunikat podobny do następującego:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Prywatny adres IP **10.0.0.5** jest zwracany dla nazwy programu SQL Server.  Ten adres znajduje się w podsieci sieci wirtualnej, która została wcześniej utworzona.


10. Zainstaluj [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) w **myVM**.

11. Otwórz **SQL Server Management Studio**.

12. W obszarze **Połącz z serwerem** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Typ serwera | Wybierz pozycję **Aparat bazy danych**.|
    | Nazwa serwera | Wprowadź **\<sql-server-name> . Database.Windows.NET** |
    | Uwierzytelnianie | Wybierz pozycję **Uwierzytelnianie SQL Server**. |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika wprowadzoną podczas tworzenia serwera |
    | Hasło | Wprowadź hasło wprowadzone podczas tworzenia serwera |
    | Remember password (Zapamiętaj hasło) | Wybierz pozycję **Tak**. |

13. Wybierz pozycję **Połącz**.

14. Przeglądaj bazy danych z menu po lewej stronie.

15. Zdefiniować Utwórz lub Zbadaj informacje z **mysqldatabase**.

16. Zamknij połączenie bastionu z **myVM**. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów 

Gdy skończysz korzystać z prywatnego punktu końcowego, programu SQL Server i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono:

* Sieć wirtualna i Host bastionu.
* Maszyna wirtualna.
* Serwer SQL Azure z prywatnym punktem końcowym.

Maszyna wirtualna została użyta do bezpiecznego testowania łączności z programem SQL Server przez prywatny punkt końcowy.

W następnym kroku warto również zainteresować **aplikację internetową z prywatnym połączeniem z architekturą usługi Azure SQL Database** , która łączy aplikację sieci Web spoza sieci wirtualnej z prywatnym punktem końcowym bazy danych.
> [!div class="nextstepaction"]
> [Aplikacja internetowa z łącznością prywatną z usługą Azure SQL Database](/azure/architecture/example-scenario/private-web-app/private-web-app)