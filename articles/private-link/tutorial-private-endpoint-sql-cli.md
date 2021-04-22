---
title: 'Samouczek: nawiązywanie połączenia z serwerem Azure SQL przy użyciu prywatnego punktu końcowego platformy Azure — interfejs wiersza polecenia platformy Azure'
description: Z tego samouczka dowiesz się, jak utworzyć serwer Azure SQL z prywatnym punktem końcowym przy użyciu interfejsu wiersza polecenia platformy Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 5a66749a3e773328e6c6c02375e76998bd6531cd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871961"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Samouczek: nawiązywanie połączenia z serwerem Azure SQL przy użyciu prywatnego punktu końcowego platformy Azure — interfejs wiersza polecenia platformy Azure

Prywatny punkt końcowy platformy Azure to podstawowy blok Private Link platformie Azure. Umożliwia ona zasobom platformy Azure, na przykład maszynom wirtualnym, prywatną komunikację Private Link zasobami.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz sieć wirtualną i hosta bastionu.
> * Tworzy maszynę wirtualną.
> * Utwórz serwer Azure SQL i prywatny punkt końcowy.
> * Przetestuj łączność z prywatnym punktem końcowym programu SQL Server.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zaloguj się do Azure Portal i sprawdź, czy Twoja subskrypcja jest aktywna, uruchamiając . `az login`
* Sprawdź wersję interfejsu wiersza polecenia platformy Azure w terminalu lub oknie poleceń, uruchamiając polecenie `az --version` . Aby uzyskać najnowszą wersję, zobacz [najnowsze informacje o wersji.](/cli/azure/release-notes-azure-cli?tabs=azure-cli)
  * Jeśli nie masz najnowszej wersji, zaktualizuj instalację, korzystając z przewodnika instalacji dla systemu [operacyjnego lub platformy](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [az group create](/cli/azure/group#az_group_create):

* O **nazwie CreateSQLEndpointTutorial-rg.** 
* W lokalizacji **eastus.**

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Tworzenie sieci wirtualnej i hosta bastionu

W tej sekcji utworzysz sieć wirtualną, podsieć i hosta bastionu. 

Host bastionu będzie używany do bezpiecznego nawiązywania połączenia z maszyną wirtualną w celu przetestowania prywatnego punktu końcowego.

Tworzenie sieci wirtualnej za pomocą [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)

* Nosi **nazwę myVNet.**
* Prefiks adresu **10.0.0.0/16.**
* Podsieć **o nazwie myBackendSubnet.**
* Prefiks podsieci **10.0.0.0/24.**
* W grupie **zasobów CreateSQLEndpointTutorial-rg.**
* Lokalizacja **regionu eastus**.

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Zaktualizuj podsieć, aby wyłączyć zasady sieci prywatnego punktu końcowego dla prywatnego punktu końcowego za pomocą [az network vnet subnet update:](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby utworzyć publiczny adres IP dla hosta bastionu:

* Utwórz standardowy strefowo nadmiarowy publiczny adres IP o **nazwie myBastionIP.**
* W **createSQLEndpointTutorial-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Użyj [az network vnet subnet create,](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) aby utworzyć podsieć bastionu:

* O **nazwie AzureBastionSubnet.**
* Prefiks adresu **10.0.1.0/24.**
* W sieci **wirtualnej myVNet**.
* W grupie zasobów **CreateSQLEndpointTutorial-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Użyj [az network bastion create,](/cli/azure/network/bastion#az_network_bastion_create) aby utworzyć hosta bastionu:

* Nazwane **myBastionHost**.
* W **createSQLEndpointTutorial-rg**.
* Skojarzony z publicznym adresem IP **myBastionIP.**
* Skojarzone z siecią **wirtualną myVNet.**
* W **lokalizacji eastus.**

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Wdrożenie hosta Azure Bastion może potrwać kilka minut.

## <a name="create-test-virtual-machine"></a>Tworzenie testowej maszyny wirtualnej

W tej sekcji utworzysz maszynę wirtualną, która będzie używana do testowania prywatnego punktu końcowego.

Utwórz maszynę wirtualną za pomocą [az vm create](/cli/azure/vm#az_vm_create). Po wyświetleniu monitu podaj hasło, które będzie używane jako poświadczenia dla maszyny wirtualnej:

* Nazwana **myVM.**
* W **createSQLEndpointTutorial-rg**.
* W sieci **sieć myVNet**.
* W podsieci **myBackendSubnet**.
* Obraz serwera **Win2019Datacenter.**

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

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-an-azure-sql-server"></a>Tworzenie serwera Azure SQL serwera

W tej sekcji utworzysz serwer SQL i bazę danych.

Użyj [polecenia az sql server create,](/cli/azure/sql/server#az_sql_server_create) aby utworzyć serwer SQL:

* **\<sql-server-name>** Zamień na unikatową nazwę serwera.
* Zastąp **\<your-password>** hasłem.
* W **createSQLEndpointTutorial-rg**.
* W **regionie eastus.**

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Użyj [polecenia az sql db create,](/cli/azure/sql/db#az_sql_db_create) aby utworzyć bazę danych:

* Nazwane **myDataBase.**
* W **edycie CreateSQLEndpointTutorial-rg**.
* Zastąp **\<sql-server-name>** unikatową nazwą serwera.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

W tej sekcji utworzysz prywatny punkt końcowy.

Użyj [polecenia az sql server list,](/cli/azure/sql/server#az_sql_server_list) aby umieścić identyfikator zasobu serwera SQL w zmiennej powłoki.

Użyj [az network private-endpoint create,](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) aby utworzyć punkt końcowy i połączenie:

* O **nazwie myPrivateEndpoint.**
* W grupie zasobów **CreateSQLEndpointTutorial-rg**.
* W sieci **wirtualnej myVNet**.
* W podsieci **myBackendSubnet**.
* Połączenie o nazwie **myConnection**.

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

## <a name="configure-the-private-dns-zone"></a>Konfigurowanie prywatnej strefy DNS

W tej sekcji utworzysz i skonfigurujesz prywatną strefę DNS przy użyciu narzędzia [az network private-dns zone create](/cli/azure/network/private-dns/zone#az_network_private_dns_zone_create).  

Użyjesz az [network private-dns link vnet create,](/cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create) aby utworzyć połączenie sieci wirtualnej ze strefą DNS.

Utworzysz grupę stref DNS za pomocą az [network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Strefa o **nazwie privatelink.database.windows.net**
* W sieci **wirtualnej myVNet**.
* W grupie zasobów **CreateSQLEndpointTutorial-rg**.
* Link DNS o **nazwie myDNSLink.**
* Skojarzone z **myPrivateEndpoint**.
* Grupa stref o **nazwie MyZoneGroup**.

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

W tej sekcji użyjesz maszyny wirtualnej utworzonej w poprzednim kroku, aby nawiązać połączenie z serwerem SQL za pomocą prywatnego punktu końcowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 
 
2. Wybierz **pozycję Grupy zasobów** w okienku nawigacji po lewej stronie.

3. Wybierz **pozycję CreateSQLEndpointTutorial-rg.**

4. Wybierz **pozycję myVM.**

5. Na stronie przeglądu dla **myVM** wybierz pozycję **Połącz,** a następnie **pozycję Bastion.**

6. Wybierz niebieski przycisk **Użyj bastionu.**

7. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

8. Otwórz Windows PowerShell na serwerze po nawiązać połączenie.

9. Wprowadź `nslookup <sqlserver-name>.database.windows.net`. Zastąp **\<sqlserver-name>** nazwą serwera SQL utworzonego w poprzednich krokach.  Zostanie wyświetlony komunikat podobny do poniższego:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Prywatny adres IP **10.0.0.5** jest zwracany jako nazwa serwera SQL.  Ten adres znajduje się w podsieci utworzonej wcześniej sieci wirtualnej.


10. Zainstaluj [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) na **myVM.**

11. Otwórz **SQL Server Management Studio**.

12. W **polecej Połącz z serwerem** wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Typ serwera | Wybierz pozycję **Aparat bazy danych**.|
    | Nazwa serwera | Wprowadź **\<sql-server-name> .database.windows.net** |
    | Uwierzytelnianie | Wybierz pozycję **Uwierzytelnianie SQL Server**. |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika wprowadzona podczas tworzenia serwera |
    | Hasło | Wprowadź hasło wprowadzone podczas tworzenia serwera |
    | Remember password (Zapamiętaj hasło) | Wybierz pozycję **Tak**. |

13. Wybierz pozycję **Połącz**.

14. Przeglądaj bazy danych z menu po lewej stronie.

15. (Opcjonalnie) Utwórz informacje lub utwórz zapytanie dotyczące informacji **z bazy danych mysqldatabase.**

16. Zamknij połączenie bastionu z **myVM.** 

## <a name="clean-up-resources"></a>Czyszczenie zasobów 

Po użyciu prywatnego punktu końcowego, serwera SQL i maszyny wirtualnej usuń grupę zasobów i wszystkie zasoby, które zawiera: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono:

* Sieć wirtualna i host bastionu.
* Maszyna wirtualna.
* Azure SQL z prywatnym punktem końcowym.

Maszyna wirtualna została użyta do bezpiecznego przetestowania łączności z serwerem SQL w prywatnym punkcie końcowym.

Następnym krokiem może być również aplikacja internetowa z prywatną łącznością ze scenariuszem architektury bazy danych usługi **Azure SQL,** który łączy aplikację internetową poza siecią wirtualną z prywatnym punktem końcowym bazy danych.
> [!div class="nextstepaction"]
> [Aplikacja internetowa z prywatną łącznością z Azure SQL danych](/azure/architecture/example-scenario/private-web-app/private-web-app)