---
title: Szybki start — tworzenie prywatnego punktu końcowego platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: Skorzystaj z tego przewodnika Szybki start, aby dowiedzieć się, jak utworzyć prywatny punkt końcowy przy użyciu interfejsu wiersza polecenia platformy Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 5088b4e50899a2643488103ba29a7e36a7f256ea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778359"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Szybki start: tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure

Rozpoczynanie pracy z Azure Private Link przy użyciu prywatnego punktu końcowego w celu bezpiecznego nawiązywania połączenia z aplikacją internetową platformy Azure.

W tym przewodniku Szybki start utworzysz prywatny punkt końcowy dla aplikacji internetowej platformy Azure i wdrożysz maszynę wirtualną w celu przetestowania połączenia prywatnego.  

Prywatne punkty końcowe można tworzyć dla różnych rodzajów usług platformy Azure, takich jak Azure SQL i Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Aplikacja internetowa platformy Azure z **planem PremiumV2 lub** wyższej usługi App Service wdrożonym w ramach subskrypcji platformy Azure.  
    * Aby uzyskać więcej informacji i przykład, zobacz [Szybki start: tworzenie aplikacji internetowej ASP.NET Core na platformie Azure.](../app-service/quickstart-dotnetcore.md) 
    * Aby uzyskać szczegółowy samouczek dotyczący tworzenia aplikacji internetowej i punktu końcowego, zobacz Samouczek: nawiązywanie połączenia z aplikacją [internetową przy użyciu prywatnego punktu końcowego platformy Azure.](tutorial-private-endpoint-webapp-portal.md)
* Zaloguj się do Azure Portal i sprawdź, czy Twoja subskrypcja jest aktywna, uruchamiając . `az login`
* Sprawdź wersję interfejsu wiersza polecenia platformy Azure w oknie terminalu lub polecenia, uruchamiając polecenie `az --version` . Aby uzyskać najnowszą wersję, zobacz najnowsze [informacje o wersji.](/cli/azure/release-notes-azure-cli?tabs=azure-cli)
  * Jeśli nie masz najnowszej wersji, zaktualizuj instalację, korzystając z przewodnika instalacji dla systemu [operacyjnego lub platformy](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [az group create](/cli/azure/group#az_group_create):

* O **nazwie CreatePrivateEndpointQS-rg**. 
* W lokalizacji **eastus.**

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Tworzenie sieci wirtualnej i hosta bastionu

W tej sekcji utworzysz sieć wirtualną, podsieć i hosta bastionu. 

Host bastionu będzie używany do bezpiecznego nawiązywania połączenia z maszyną wirtualną w celu przetestowania prywatnego punktu końcowego.

Tworzenie sieci wirtualnej za pomocą [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)

* Nazwane **myVNet.**
* Prefiks adresu **10.0.0.0/16.**
* Podsieć **o nazwie myBackendSubnet.**
* Prefiks podsieci **10.0.0.0/24.**
* W grupie **zasobów CreatePrivateEndpointQS-rg.**
* Lokalizacja **regionu eastus**.

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
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
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby utworzyć publiczny adres IP hosta bastionu:

* Utwórz standardowy strefowo nadmiarowy publiczny adres IP o nazwie **myBastionIP.**
* W **jęz. CreatePrivateEndpointQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Użyj [az network vnet subnet create,](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) aby utworzyć podsieć bastionu:

* O **nazwie AzureBastionSubnet.**
* Prefiks adresu **10.0.1.0/24.**
* W sieci **wirtualnej myVNet**.
* W grupie zasobów **CreatePrivateEndpointQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Użyj [az network bastion create,](/cli/azure/network/bastion#az_network_bastion_create) aby utworzyć hosta bastionu:

* O **nazwie myBastionHost.**
* W **jęz. CreatePrivateEndpointQS-rg**.
* Skojarzony z publicznym adresem IP **myBastionIP.**
* Skojarzone z siecią **wirtualną myVNet.**
* W **lokalizacji eastus.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Wdrożenie hosta może potrwać Azure Bastion kilka minut.

## <a name="create-test-virtual-machine"></a>Tworzenie testowej maszyny wirtualnej

W tej sekcji utworzysz maszynę wirtualną, która będzie używana do testowania prywatnego punktu końcowego.

Utwórz maszynę wirtualną za pomocą [az vm create](/cli/azure/vm#az_vm_create). Po wyświetleniu monitu podaj hasło, które będzie używane jako poświadczenia dla maszyny wirtualnej:

* O **nazwie myVM.**
* W **jęz. CreatePrivateEndpointQS-rg**.
* W sieci **myVNet**.
* W podsieci **myBackendSubnet**.
* Obraz serwera **Win2019Datacenter.**

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

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

W tej sekcji utworzysz prywatny punkt końcowy.

Użyj [az webapp list,](/cli/azure/webapp#az_webapp_list) aby umieścić identyfikator zasobu utworzonej wcześniej aplikacji internetowej w zmiennej powłoki.

Użyj [az network private-endpoint create,](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) aby utworzyć punkt końcowy i połączenie:

* O **nazwie myPrivateEndpoint.**
* W grupie zasobów **CreatePrivateEndpointQS-rg**.
* W sieci **wirtualnej myVNet**.
* W podsieci **myBackendSubnet**.
* Połączenie o nazwie **myConnection**.
* Twoja aplikacja internetowa **\<webapp-resource-group-name>** .

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

## <a name="configure-the-private-dns-zone"></a>Konfigurowanie prywatnej strefy DNS

W tej sekcji utworzysz i skonfigurujesz prywatną strefę DNS przy użyciu narzędzia [az network private-dns zone create.](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create)  

Użyjesz az [network private-dns link vnet create,](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) aby utworzyć połączenie sieci wirtualnej ze strefą DNS.

Utworzysz grupę stref DNS za pomocą az [network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Strefa o **nazwie privatelink.azurewebsites.net**
* W sieci **wirtualnej myVNet**.
* W grupie zasobów **CreatePrivateEndpointQS-rg**.
* Link DNS o **nazwie myDNSLink.**
* Skojarzone z **myPrivateEndpoint**.
* Grupa stref o **nazwie MyZoneGroup**.

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

W tej sekcji użyjesz maszyny wirtualnej utworzonej w poprzednim kroku, aby nawiązać połączenie z serwerem SQL za pomocą prywatnego punktu końcowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 
 
2. Wybierz **pozycję Grupy zasobów** w okienku nawigacji po lewej stronie.

3. Wybierz **pozycję CreatePrivateEndpointQS-rg.**

4. Wybierz **pozycję myVM.**

5. Na stronie przeglądu dla **myVM** wybierz pozycję **Połącz,** a następnie **pozycję Bastion.**

6. Wybierz niebieski przycisk **Użyj bastionu.**

7. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

8. Otwórz Windows PowerShell na serwerze po nawiązać połączenie.

9. Wprowadź `nslookup <your-webapp-name>.azurewebsites.net`. Zastąp **\<your-webapp-name>** nazwą aplikacji internetowej utworzonej w poprzednich krokach.  Zostanie wyświetlony komunikat podobny do poniższego:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Prywatny adres IP **10.0.0.5** jest zwracany dla nazwy aplikacji internetowej.  Ten adres znajduje się w podsieci utworzonej wcześniej sieci wirtualnej.

10. W połączeniu bastionu z **myVM** otwórz Internet Explorer.

11. Wprowadź adres URL aplikacji internetowej, a **następnie https:// \<your-webapp-name> .azurewebsites.net**.

12. Jeśli aplikacja nie została wdrożona, otrzymasz domyślną stronę aplikacji internetowej:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Domyślna strona aplikacji internetowej." border="true":::

13. Zamknij połączenie z **myVM.**

## <a name="clean-up-resources"></a>Czyszczenie zasobów 
Po użyciu prywatnego punktu końcowego i maszyny wirtualnej użyj narzędzia [az group delete,](/cli/azure/group#az_group_delete) aby usunąć grupę zasobów i wszystkie zasoby, które zawiera:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono:

* Sieć wirtualna i host bastionu.
* Maszyna wirtualna.
* Prywatny punkt końcowy aplikacji internetowej platformy Azure.

Maszyna wirtualna została użyta do bezpiecznego przetestowania łączności z aplikacją internetową w prywatnym punkcie końcowym.

Aby uzyskać więcej informacji na temat usług, które obsługują prywatny punkt końcowy, zobacz:
> [!div class="nextstepaction"]
> [Private Link dostępności](private-link-overview.md#availability)
