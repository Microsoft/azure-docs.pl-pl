---
title: Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z Azure Files | Microsoft Docs
description: Jak skonfigurować sieć VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z usługą Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 74422318718e318a00d7bd7ebaf8e4093ef75aa6
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629278"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z usługą Azure Files
Za pomocą połączenia sieci VPN typu punkt-lokacja (P2S) można instalować udziały plików platformy Azure za pośrednictwem protokołu SMB spoza platformy Azure bez konieczności otwierania portu 445. Połączenie sieci VPN typu punkt-lokacja to połączenie sieci VPN między platformą Azure i pojedynczym klientem. Aby można było użyć połączenia sieci VPN P2S z Azure Files, należy skonfigurować połączenie sieci VPN P2S dla każdego klienta, który chce nawiązać połączenie. Jeśli masz wielu klientów, którzy muszą nawiązać połączenie z udziałami plików platformy Azure z sieci lokalnej, możesz użyć połączenia sieci VPN typu lokacja-lokacja (S2S) zamiast połączenia punkt-lokacja dla każdego klienta. Aby dowiedzieć się więcej, zobacz [Konfigurowanie sieci VPN typu lokacja-lokacja do użycia z usługą Azure Files](storage-files-configure-s2s-vpn.md).

Zdecydowanie zalecamy zapoznanie się z [omówieniem Azure Files sieci](storage-files-networking-overview.md) przed kontynuowaniem pracy z tym artykułem, aby uzyskać pełną dyskusję na temat opcji sieciowych dostępnych dla Azure Files.

W tym artykule szczegółowo opisano procedurę konfigurowania sieci VPN typu punkt-lokacja w systemie Linux w celu zainstalowania udziałów plików platformy Azure bezpośrednio w środowisku lokalnym. Jeśli chcesz kierować ruchem Azure File Sync przez sieć VPN, zobacz [konfigurowanie Azure File Sync serwera proxy i ustawień zapory](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Wymagania wstępne
- Najnowsza wersja interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia Azure PowerShell](/cli/azure/install-azure-cli) i wybieranie systemu operacyjnego. Jeśli wolisz używać modułu Azure PowerShell w systemie Linux, możesz jednak wyświetlić poniższe instrukcje dla interfejsu wiersza polecenia platformy Azure.

- Udział plików platformy Azure, który chcesz zainstalować lokalnie. Udziały plików platformy Azure są wdrażane w ramach kont magazynu, które są konstrukcjami zarządzanymi, które reprezentują udostępnioną pulę magazynów, w której można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Więcej informacji na temat wdrażania udziałów plików platformy Azure i kont magazynu można znaleźć w temacie [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).

- Prywatny punkt końcowy dla konta magazynu zawierającego udział plików platformy Azure, który ma zostać zainstalowany lokalnie. Aby dowiedzieć się więcej na temat tworzenia prywatnego punktu końcowego, zobacz [konfigurowanie Azure Files punktów końcowych sieci](storage-files-networking-endpoints.md?tabs=azure-cli). 

## <a name="install-required-software"></a>Zainstaluj wymagane oprogramowanie
Brama sieci wirtualnej platformy Azure może zapewnić połączenia sieci VPN przy użyciu kilku protokołów sieci VPN, w tym protokołu IPsec i OpenVPN. W tym przewodniku przedstawiono sposób korzystania z protokołu IPsec i używania pakietu klient strongswan w celu zapewnienia pomocy technicznej w systemie Linux. 

> Zweryfikowano z Ubuntu 18,10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Wdrażanie sieci wirtualnej 
Aby uzyskać dostęp do udziału plików platformy Azure i innych zasobów platformy Azure z lokalnego programu za pośrednictwem sieci VPN typu punkt-lokacja, należy utworzyć sieć wirtualną lub sieci wirtualnej. Połączenie sieci VPN P2S, które zostanie utworzone automatycznie, jest mostkiem między lokalną maszyną z systemem Linux a tą siecią wirtualną platformy Azure.

Poniższy skrypt spowoduje utworzenie sieci wirtualnej platformy Azure z trzema podsieciami: jedną dla punktu końcowego usługi konta magazynu, jedną dla prywatnego punktu końcowego konta magazynu, która jest wymagana do lokalnego dostępu do konta magazynu bez tworzenia routingu niestandardowego dla publicznego adresu IP konta magazynu, które może ulec zmianie, oraz jednego dla bramy sieci wirtualnej, która udostępnia usługę sieci VPN. 

Pamiętaj, aby zamienić `<region>` , `<resource-group>` i `<desired-vnet-name>` z odpowiednimi wartościami dla danego środowiska.

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

## <a name="create-certificates-for-vpn-authentication"></a>Tworzenie certyfikatów do uwierzytelniania za pośrednictwem sieci VPN
Aby połączenia sieci VPN z lokalnych maszyn z systemem Linux były uwierzytelniane w celu uzyskania dostępu do sieci wirtualnej, należy utworzyć dwa certyfikaty: certyfikat główny, który zostanie udostępniony bramy maszyny wirtualnej, i certyfikat klienta, który zostanie podpisany przy użyciu certyfikatu głównego. Poniższy skrypt tworzy wymagane certyfikaty.

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

## <a name="deploy-virtual-network-gateway"></a>Wdróż bramę sieci wirtualnej
Brama sieci wirtualnej platformy Azure to usługa, z którą będą łączyć się lokalne maszyny z systemem Linux. Wdrożenie tej usługi wymaga dwóch podstawowych składników: publiczny adres IP, który będzie identyfikować bramę klientom w dowolnym miejscu na świecie i certyfikat główny utworzony wcześniej, który będzie używany do uwierzytelniania klientów.

Pamiętaj, aby zamienić na `<desired-vpn-name-here>` nazwę, którą chcesz dla tych zasobów.

> [!Note]  
> Wdrożenie bramy sieci wirtualnej platformy Azure może potrwać do 45 minut. Gdy ten zasób jest wdrażany, skrypt skryptu bash zablokuje ukończenie wdrożenia.
>
> P2S IKEv2/OpenVPN połączenia nie są obsługiwane w przypadku **podstawowej** jednostki SKU. Ten skrypt używa odpowiednio jednostki SKU **VpnGw1** dla bramy sieci wirtualnej.

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

## <a name="configure-the-vpn-client"></a>Konfigurowanie klienta VPN
Brama sieci wirtualnej platformy Azure utworzy pakiet do pobrania z plikami konfiguracyjnymi wymaganymi do zainicjowania połączenia sieci VPN na lokalnym komputerze z systemem Linux. Poniższy skrypt umieści certyfikaty utworzone w prawidłowym miejscu i skonfiguruje `ipsec.conf` plik z prawidłowymi wartościami z pliku konfiguracji w pakiecie do pobrania.

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
Teraz po skonfigurowaniu sieci VPN typu punkt-lokacja można zainstalować swój udział plików platformy Azure. W poniższym przykładzie zostanie zainstalowany udział nietrwały. Aby trwale zainstalować, zobacz [Korzystanie z udziału plików platformy Azure w systemie Linux](storage-how-to-use-files-linux.md). 

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
- [Omówienie sieci Azure Files](storage-files-networking-overview.md)
- [Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Windows do użytku z usługą Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurowanie sieci VPN typu lokacja-lokacja (S2S) do użycia z usługą Azure Files](storage-files-configure-s2s-vpn.md)