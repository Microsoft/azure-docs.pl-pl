---
title: Łączenie usługi w chmurze (klasycznej) z niestandardowym kontrolerem domeny | Microsoft Docs
description: Dowiedz się, jak połączyć role sieci Web/proces roboczy z niestandardową domeną usługi AD przy użyciu programu PowerShell i rozszerzenia domeny usługi AD
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8c2c8377944caa7ad28f6b379531e6d5bf44c9e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742509"
---
# <a name="connecting-azure-cloud-services-classic-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Łączenie ról platformy Azure Cloud Services (klasycznych) z niestandardowym kontrolerem domeny usługi Active Directory hostowanym na platformie Azure

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Najpierw skonfigurujemy Virtual Network (VNet) na platformie Azure. Następnie dodamy kontroler domena usługi Active Directory (hostowany na maszynie wirtualnej platformy Azure) do sieci wirtualnej. Następnie dodamy istniejące role usługi w chmurze do wstępnie utworzonej sieci wirtualnej, a następnie nawiążesz połączenie z kontrolerem domeny.

Przed rozpoczęciem należy pamiętać o kilku kwestiach:

1. Ten samouczek używa programu PowerShell, dlatego upewnij się, że masz Azure PowerShell zainstalowane i gotowe do użycia. Aby uzyskać pomoc dotyczącą konfigurowania Azure PowerShell, zobacz [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/).
2. Wystąpienia roli kontroler domeny usługi AD i sieć Web/proces roboczy muszą znajdować się w sieci wirtualnej.

Postępuj zgodnie z tym przewodnikiem krok po kroku, a jeśli napotkasz jakiekolwiek problemy, pozostaw komentarz na końcu artykułu. Ktoś powróci do Ciebie (tak, będziemy czytać komentarze).

Sieć, do której odwołuje się usługa w chmurze, musi być **klasyczną siecią wirtualną**.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Virtual Network można utworzyć na platformie Azure przy użyciu Azure Portal lub programu PowerShell. W tym samouczku jest używany program PowerShell. Aby utworzyć sieć wirtualną przy użyciu Azure Portal, zobacz [Tworzenie sieci wirtualnej](../virtual-network/quick-create-portal.md). W tym artykule opisano tworzenie sieci wirtualnej (Menedżer zasobów), ale należy utworzyć sieć wirtualną (klasyczną) dla usług Cloud Services. W tym celu w portalu wybierz pozycję **Utwórz zasób**, w polu **wyszukiwania** wpisz *sieć wirtualną* , a następnie naciśnij klawisz **Enter**. W wynikach wyszukiwania w obszarze **wszystko** wybierz pozycję **Sieć wirtualna**. W obszarze **Wybierz model wdrożenia** wybierz pozycję **klasyczny**, a następnie wybierz pozycję **Utwórz**. Następnie można wykonać kroki opisane w artykule.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Po zakończeniu konfigurowania Virtual Network należy utworzyć kontroler domeny usługi AD. Na potrzeby tego samouczka skonfigurujemy kontroler domeny usługi AD na maszynie wirtualnej platformy Azure.

W tym celu należy utworzyć maszynę wirtualną za pomocą programu PowerShell przy użyciu następujących poleceń:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Podnieś poziom swojej maszyny wirtualnej do poziomu kontrolera domeny
Aby skonfigurować maszynę wirtualną jako kontroler domeny usługi AD, należy zalogować się do maszyny wirtualnej i skonfigurować ją.

Aby zalogować się do maszyny wirtualnej, możesz pobrać plik RDP za pomocą programu PowerShell, używając następujących poleceń:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Po zalogowaniu się do maszyny wirtualnej Skonfiguruj maszynę wirtualną jako kontroler domeny usługi AD, postępując zgodnie z przewodnikiem krok po kroku dotyczącym [konfigurowania kontrolera domeny usługi AD klienta](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Dodaj usługę w chmurze do Virtual Network
Następnie musisz dodać wdrożenie usługi w chmurze do nowej sieci wirtualnej. W tym celu należy zmodyfikować cscfg usługi w chmurze, dodając odpowiednie sekcje do pliku cscfg przy użyciu programu Visual Studio lub wybranego edytora.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Następnie Skompiluj projekt usług w chmurze i Wdróż go na platformie Azure. Aby uzyskać pomoc dotyczącą wdrażania pakietu usług w chmurze na platformie Azure, zobacz [jak utworzyć i wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md) .

## <a name="connect-your-webworker-roles-to-the-domain"></a>Połącz role sieci Web/proces roboczy z domeną
Po wdrożeniu projektu usługi w chmurze na platformie Azure Połącz wystąpienia roli z niestandardową domeną usługi AD przy użyciu rozszerzenia domeny usługi AD. Aby dodać rozszerzenie domeny usługi AD do istniejącego wdrożenia usług w chmurze i dołączyć do domeny niestandardowej, wykonaj następujące polecenia w programie PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

To wszystko.

Usługi w chmurze powinny być połączone z niestandardowym kontrolerem domeny. Jeśli chcesz dowiedzieć się więcej na temat różnych opcji dotyczących konfigurowania rozszerzenia domeny usługi AD, użyj pomocy programu PowerShell. Poniżej przedstawiono kilka przykładów:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```



