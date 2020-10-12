---
title: Tworzenie, zmienianie lub usuwanie sieci wirtualnej TAP — interfejs wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć, zmienić lub usunąć sieć wirtualną NACIŚNIĘCIe przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e3a56e4a6eb1fb6eb633021178ef78f8ac7287d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87488843"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Współpraca z siecią wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure Virtual Network TAP (punkt dostępu terminalu) umożliwia ciągłą przesyłanie strumieniowego ruchu sieciowego maszyny wirtualnej do narzędzia do zbierania pakietów sieciowych lub narzędzi analitycznych. Moduł zbierający lub narzędzie analityczne jest dostarczany przez partnera [wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances/) . Aby uzyskać listę rozwiązań partnerskich, które są sprawdzane pod kątem współpracy z siecią wirtualną, zobacz [rozwiązania partnerskie](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Utwórz zasób TAP sieci wirtualnej

Zapoznaj się z [wymaganiami wstępnymi](virtual-network-tap-overview.md#prerequisites) przed utworzeniem zasobu TAP sieci wirtualnej. Poniższe polecenia można uruchomić w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia platformy Azure (CLI) na komputerze. Azure Cloud Shell to bezpłatna interaktywna powłoka, która nie wymaga instalacji interfejsu wiersza polecenia platformy Azure na komputerze. Musisz zalogować się do platformy Azure przy użyciu konta, które ma odpowiednie [uprawnienia](virtual-network-tap-overview.md#permissions). Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Sieć wirtualna TAP jest obecnie dostępna jako rozszerzenie. Aby zainstalować rozszerzenie, należy je uruchomić `az extension add -n virtual-network-tap` . Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz też uruchomić polecenie `az login` , aby utworzyć połączenie z platformą Azure.

1. Pobierz identyfikator subskrypcji do zmiennej, która jest używana w kolejnym kroku:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Ustaw identyfikator subskrypcji, który będzie używany do tworzenia zasobów TAP sieci wirtualnej.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Zarejestruj ponownie identyfikator subskrypcji, którego będziesz używać do tworzenia zasobu sieci wirtualnej. Jeśli podczas tworzenia zasobu TAP wystąpi błąd rejestracji, uruchom następujące polecenie:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Jeśli miejsce docelowe dla sieci wirtualnej naciśnij pozycję interfejs sieciowy w urządzeniu wirtualnym sieci dla modułu zbierającego lub narzędzia analitycznego —

   - Pobierz konfigurację adresu IP interfejsu sieciowego sieciowego urządzenia wirtualnego do zmiennej, która jest używana w późniejszym kroku. Identyfikator jest punktem końcowym, który będzie agregować ruch dla NACISKu. Poniższy przykład pobiera identyfikator *ipconfig1* IP dla interfejsu sieciowego o nazwie *myNetworkInterface*w grupie zasobów o nazwie Moja *zasobów*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Utwórz sieć wirtualną naciśnij w regionie westcentralus Azure, używając identyfikatora konfiguracji adresu IP jako miejsca docelowego i opcjonalnej właściwości portu. Port Określa port docelowy w konfiguracji protokołu IP interfejsu sieciowego, w którym będzie odbierany ruch TAP:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Jeśli lokalizacja docelowa dla sieci wirtualnej jest WYBIERAna jako wewnętrzny moduł równoważenia obciążenia platformy Azure:
  
   - Pobierz konfigurację adresu IP frontonu wewnętrznego modułu równoważenia obciążenia platformy Azure do zmiennej, która jest używana w późniejszym kroku. Identyfikator jest punktem końcowym, który będzie agregować ruch dla NACISKu. Poniższy przykład pobiera identyfikator *frontendipconfig1ego* adresu IP frontonu dla modułu równoważenia obciążenia o nazwie *myInternalLoadBalancer*w grupie zasobów o nazwie Moja *zasobów*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Utwórz sieć wirtualną naciśnij, używając identyfikatora konfiguracji adresu IP frontonu jako lokalizacji docelowej i opcjonalnej właściwości portu. Port Określa port docelowy w konfiguracji adresu IP frontonu, w którym będzie odbierany ruch dla TAP:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Potwierdź utworzenie sieci wirtualnej TAP:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Dodawanie konfiguracji TAP do interfejsu sieciowego

1. Pobierz identyfikator istniejącej sieci wirtualnej. Poniższy przykład pobiera sieć wirtualną TAP o nazwie *myTap* w grupie zasobów o nazwie Moja *resourceName*:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Utwórz konfigurację TAP w interfejsie sieciowym monitorowanej maszyny wirtualnej. Poniższy przykład tworzy konfigurację TAP dla interfejsu sieciowego o nazwie *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Potwierdź utworzenie konfiguracji TAP:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Usuń konfigurację TAP w interfejsie sieciowym

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Wyświetlanie listy naciśnięć sieci wirtualnych w ramach subskrypcji

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Usuwanie sieci wirtualnej NACIŚNIĘCIe w grupie zasobów

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
