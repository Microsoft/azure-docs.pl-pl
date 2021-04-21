---
title: Konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z Azure Files | Microsoft Docs
description: Jak skonfigurować sieć VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9608e3bdaab033d58796a3841e8cd92d7a8a81ef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777981"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z Azure Files
Możesz użyć połączenia sieci VPN typu punkt-lokacja (P2S), aby zainstalować udziały plików platformy Azure za pośrednictwem SMB spoza platformy Azure bez otwierania portu 445. Połączenie sieci VPN typu punkt-lokacja to połączenie sieci VPN między platformą Azure i indywidualnym klientem. Aby można było używać połączenia sieci VPN P2S z Azure Files sieci VPN P2S, należy skonfigurować połączenie sieci VPN P2S dla każdego klienta, który chce nawiązać połączenie. Jeśli masz wielu klientów, którzy muszą łączyć się z udziałami plików platformy Azure z sieci lokalnej, możesz użyć połączenia sieci VPN typu lokacja-lokacja (S2S) zamiast połączenia punkt-lokacja dla każdego klienta. Aby dowiedzieć się więcej, [zobacz Konfigurowanie sieci VPN typu lokacja-lokacja](storage-files-configure-s2s-vpn.md)do użycia z Azure Files .

Zdecydowanie zalecamy przeczytanie Azure Files [omówienie](storage-files-networking-overview.md) sieci przed kontynuowaniem tego artykułu, aby zapoznać się z pełną omówieniem opcji sieci dostępnych dla Azure Files.

Artykuł zawiera szczegółowe instrukcje konfigurowania sieci VPN typu punkt-lokacja w systemie Linux w celu instalacji udziałów plików platformy Azure bezpośrednio w środowisku lokalnym. Jeśli chcesz przekierowyć ruch sieciowy Azure File Sync sieci VPN, zobacz konfigurowanie ustawień serwera [proxy Azure File Sync](../file-sync/file-sync-firewall-and-proxy.md)zapory.

## <a name="prerequisites"></a>Wymagania wstępne
- Najnowsza wersja interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat sposobu instalowania interfejsu wiersza polecenia platformy Azure, zobacz Instalowanie interfejsu wiersza [Azure PowerShell wiersza polecenia](/cli/azure/install-azure-cli) i wybieranie systemu operacyjnego. Jeśli wolisz używać modułu Azure PowerShell w systemie Linux, możesz jednak skorzystać z poniższych instrukcji dotyczących interfejsu wiersza polecenia platformy Azure.

- Udział plików platformy Azure, który chcesz zainstalować lokalnie. Udziały plików platformy Azure są wdrażane w ramach kont magazynu, które są konstrukcjami zarządzania reprezentującymi udostępnioną pulę magazynu, w której można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Aby dowiedzieć się więcej na temat wdrażania udziałów plików i kont magazynu platformy Azure, [zobacz Tworzenie udziału plików platformy Azure.](storage-how-to-create-file-share.md)

- Prywatny punkt końcowy dla konta magazynu zawierającego udział plików platformy Azure, który chcesz zainstalować lokalnie. Aby dowiedzieć się więcej na temat tworzenia prywatnego punktu końcowego, zobacz [Konfigurowanie Azure Files sieci.](storage-files-networking-endpoints.md?tabs=azure-cli) 

## <a name="install-required-software"></a>Instalowanie wymaganego oprogramowania
Brama sieci wirtualnej platformy Azure może zapewniać połączenia sieci VPN przy użyciu kilku protokołów sieci VPN, w tym IPsec i OpenVPN. W tym przewodniku przedstawiono sposób używania protokołu IPsec i użycia pakietu strongSwan w celu zapewnienia obsługi w systemie Linux. 

> Zweryfikowane w systemie Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Wdrażanie sieci wirtualnej 
Aby uzyskać dostęp do udziału plików platformy Azure i innych zasobów platformy Azure ze środowiska lokalnego za pośrednictwem sieci VPN typu punkt-lokacja, musisz utworzyć sieć wirtualną lub sieć wirtualną. Połączenie sieci VPN typu P2S, które zostanie automatycznie tworzone, jest mostem między lokalną maszyną z systemem Linux i tą siecią wirtualną platformy Azure.

Poniższy skrypt utworzy sieć wirtualną platformy Azure z trzema podsieciami: jedną dla punktu końcowego usługi konta magazynu, jedną dla prywatnego punktu końcowego konta magazynu, która jest wymagana do uzyskania dostępu do lokalnego konta magazynu bez tworzenia niestandardowego routingu dla publicznego adresu IP konta magazynu, który może ulec zmianie, i jedną dla bramy sieci wirtualnej, która udostępnia usługę sieci VPN. 

Pamiętaj, aby `<region>` zastąpić wartości , i odpowiednimi `<resource-group>` `<desired-vnet-name>` wartościami dla swojego środowiska.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>Tworzenie certyfikatów do uwierzytelniania sieci VPN
Aby można było uwierzytelnić połączenia sieci VPN z lokalnych maszyn z systemem Linux w celu uzyskania dostępu do sieci wirtualnej, należy utworzyć dwa certyfikaty: certyfikat główny, który zostanie dostarczony do bramy maszyny wirtualnej, oraz certyfikat klienta, który zostanie podpisany przy użyciu certyfikatu głównego. Poniższy skrypt tworzy wymagane certyfikaty.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Wdrażanie bramy sieci wirtualnej
Brama sieci wirtualnej platformy Azure to usługa, z która będą łączyć się lokalne maszyny z systemem Linux. Wdrożenie tej usługi wymaga dwóch podstawowych składników: publicznego adresu IP, który będzie identyfikować bramę dla klientów w dowolnym miejscu na świecie, oraz utworzonego wcześniej certyfikatu głównego, który będzie używany do uwierzytelniania klientów.

Pamiętaj, `<desired-vpn-name-here>` aby zastąpić nazwę nazwą, która ma być nazwą tych zasobów.

> [!Note]  
> Wdrażanie bramy sieci wirtualnej platformy Azure może potrwać do 45 minut. Podczas wdrażania tego zasobu ten skrypt powłoki bash będzie blokował ukończenie wdrożenia.
>
> Połączenia P2S IKEv2/OpenVPN nie są obsługiwane w przypadku podstawowej **wersji** SKU. Ten skrypt odpowiednio używa dla bramy sieci wirtualnej sKU **VpnGw1.**

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>Konfigurowanie klienta sieci VPN
Brama sieci wirtualnej platformy Azure utworzy pakiet do pobrania z plikami konfiguracji wymaganymi do zainicjowania połączenia sieci VPN na lokalnej maszynie z systemem Linux. Poniższy skrypt umieszcza utworzone certyfikaty we właściwym miejscu i konfiguruje plik przy użyciu poprawnych wartości z pliku konfiguracji w pakiecie `ipsec.conf` do pobrania.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Zainstaluj udział plików platformy Azure
Po skonfigurowaniu sieci VPN typu punkt-lokacja możesz zainstalować udział plików platformy Azure. Poniższy przykład zainstaluje udział w sposób nietrwale. Aby zainstalować w sposób trwały, zobacz [Używanie udziału plików platformy Azure w systemie Linux.](storage-how-to-use-files-linux.md) 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Zobacz też
- [Azure Files omówienie sieci](storage-files-networking-overview.md)
- [Konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Windows do użycia z Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurowanie sieci VPN typu lokacja-lokacja (S2S) do użycia z Azure Files](storage-files-configure-s2s-vpn.md)