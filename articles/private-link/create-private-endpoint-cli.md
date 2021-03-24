---
title: Szybki Start — tworzenie prywatnego punktu końcowego platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku szybki start dowiesz się, jak utworzyć prywatny punkt końcowy przy użyciu interfejsu wiersza polecenia platformy Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: f74a143859f0a6629c88f0dcb61a97697f49d0be
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889233"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Szybki Start: Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure

Zacznij korzystać z prywatnego linku platformy Azure przy użyciu prywatnego punktu końcowego, aby bezpiecznie połączyć się z aplikacją internetową platformy Azure.

W tym przewodniku szybki start utworzysz prywatny punkt końcowy dla aplikacji internetowej platformy Azure i wdrożono maszynę wirtualną w celu przetestowania połączenia prywatnego.  

Prywatne punkty końcowe można utworzyć dla różnych rodzajów usług platformy Azure, takich jak Azure SQL i Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Aplikacja internetowa platformy Azure z planem usługi App Service **PremiumV2** lub nowszym wdrożonym w ramach subskrypcji platformy Azure.  
    * Aby uzyskać więcej informacji i zapoznać się z przykładem, zobacz [Szybki Start: Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure](../app-service/quickstart-dotnetcore.md). 
    * Aby uzyskać szczegółowy samouczek dotyczący tworzenia aplikacji sieci Web i punktu końcowego, zobacz [Samouczek: łączenie się z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure](tutorial-private-endpoint-webapp-portal.md).
* Zaloguj się do Azure Portal i sprawdź, czy Twoja subskrypcja jest aktywna, uruchamiając `az login` .
* Sprawdź wersję interfejsu wiersza polecenia platformy Azure w terminalu lub oknie poleceń, uruchamiając polecenie `az --version` . Aby uzyskać najnowszą wersję, zapoznaj się z [najnowszymi informacjami o wersji](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Jeśli nie masz najnowszej wersji, zaktualizuj instalację, postępując zgodnie z [instrukcją instalacji systemu operacyjnego lub platformy](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [AZ Group Create](/cli/azure/group#az_group_create):

* O nazwie **CreatePrivateEndpointQS-RG**. 
* W lokalizacji **wschodniego** .

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
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
* W grupie zasobów **CreatePrivateEndpointQS-RG** .
* Lokalizacja **wschodniego**.

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
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
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Użyj [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) , aby utworzyć publiczny adres IP dla hosta bastionu:

* Utwórz strefę Standard nadmiarowy publiczny adres IP o nazwie **myBastionIP**.
* W **CreatePrivateEndpointQS-RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Użyj [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) , aby utworzyć podsieć bastionu:

* O nazwie **AzureBastionSubnet**.
* Prefiks adresu **10.0.1.0/24**.
* W sieci wirtualnej **myVNet**.
* W grupie zasobów **CreatePrivateEndpointQS-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Użyj [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) , aby utworzyć hosta bastionu:

* O nazwie **myBastionHost**.
* W **CreatePrivateEndpointQS-RG**.
* Skojarzone z publicznym adresem IP **myBastionIP**.
* Skojarzone z **myVNet** sieci wirtualnej.
* W lokalizacji **wschodniego** .

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
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
* W **CreatePrivateEndpointQS-RG**.
* W usłudze Network **myVNet**.
* W podsieci **myBackendSubnet**.
* **Win2019Datacenter** obrazu serwera.

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-private-endpoint"></a>Utwórz prywatny punkt końcowy

W tej sekcji utworzysz prywatny punkt końcowy.

Użyj [AZ webapp list](/cli/azure/webapp#az_webapp_list) , aby umieścić identyfikator zasobu aplikacji sieci Web, która została wcześniej utworzona w zmiennej powłoki.

Użyj [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) , aby utworzyć punkt końcowy i połączenie:

* O nazwie **myPrivateEndpoint**.
* W grupie zasobów **CreatePrivateEndpointQS-RG**.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* Połączenie o nazwie Moje **połączenie**.
* Twoje webapp **\<webapp-resource-group-name>** .

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Skonfiguruj prywatną strefę DNS

W tej sekcji utworzysz i skonfigurujesz prywatną strefę DNS za pomocą polecenia [AZ Network Private-DNS Zone Create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Aby utworzyć łącze sieci wirtualnej do strefy DNS, użyj [AZ Network Private-DNS link VNET Create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) .

Utworzysz grupę strefy DNS za pomocą [AZ Network Private-Endpoint systemu DNS Create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Strefa o nazwie **privatelink.azurewebsites.NET**
* W sieci wirtualnej **myVNet**.
* W grupie zasobów **CreatePrivateEndpointQS-RG**.
* Łącze DNS o nazwie **myDNSLink**.
* Skojarzone z **myPrivateEndpoint**.
* Grupa stref o nazwie Moja **strefa**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>Testowanie łączności z prywatnym punktem końcowym

W tej sekcji użyjesz maszyny wirtualnej utworzonej w poprzednim kroku, aby nawiązać połączenie z programem SQL Server w ramach prywatnego punktu końcowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 
 
2. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów** .

3. Wybierz pozycję **CreatePrivateEndpointQS-RG**.

4. Wybierz pozycję **myVM**.

5. Na stronie Przegląd dla **myVM** wybierz pozycję **Połącz** , a następnie **bastionu**.

6. Wybierz przycisk **bastionu Użyj** niebieska.

7. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

8. Po nawiązaniu połączenia Otwórz program Windows PowerShell na serwerze.

9. Wprowadź `nslookup <your-webapp-name>.azurewebsites.net`. Zamień **\<your-webapp-name>** na nazwę aplikacji sieci Web utworzonej w poprzednich krokach.  Zostanie wyświetlony komunikat podobny do następującego:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Prywatny adres IP **10.0.0.5** jest zwracany dla nazwy aplikacji sieci Web.  Ten adres znajduje się w podsieci sieci wirtualnej, która została wcześniej utworzona.

10. W bastionu połączenie z **myVM** Otwórz program Internet Explorer.

11. Wprowadź adres URL aplikacji sieci Web, **https:// \<your-webapp-name> . azurewebsites.NET**.

12. Jeśli aplikacja nie została wdrożona, zostanie wyświetlona domyślna strona aplikacji sieci Web:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Domyślna strona aplikacji sieci Web." border="true":::

13. Zamknij połączenie z usługą **myVM**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów 
Gdy skończysz korzystać z prywatnego punktu końcowego i maszyny wirtualnej, użyj polecenia [AZ Group Delete](/cli/azure/group#az_group_delete) , aby usunąć grupę zasobów i wszystkie jej zasoby:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:

* Sieć wirtualna i Host bastionu.
* Maszyna wirtualna.
* Prywatny punkt końcowy dla aplikacji internetowej platformy Azure.

Maszyna wirtualna została użyta do bezpiecznego testowania łączności z aplikacją sieci Web w prywatnym punkcie końcowym.

Aby uzyskać więcej informacji na temat usług, które obsługują prywatny punkt końcowy, zobacz:
> [!div class="nextstepaction"]
> [Dostępność linku prywatnego](private-link-overview.md#availability)
