---
title: Typowe polecenia programu PowerShell dla usługi Azure Virtual Machines
description: Typowe polecenia programu PowerShell umożliwiające rozpoczęcie tworzenia maszyn wirtualnych i zarządzania nimi na platformie Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: ecc3f5934e4dd37cc27a1715ea39f86619d581ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730210"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Typowe polecenia programu PowerShell do tworzenia maszyn wirtualnych platformy Azure i zarządzania nimi

W tym artykule opisano niektóre Azure PowerShell polecenia, których można użyć do tworzenia maszyn wirtualnych w ramach subskrypcji platformy Azure i zarządzania nimi.  Aby uzyskać bardziej szczegółową pomoc dotyczącą określonych przełączników i opcji wiersza polecenia, można użyć *polecenia* **Get-Help** .

 

Te zmienne mogą być przydatne w przypadku uruchamiania więcej niż jednego polecenia w tym artykule:

- $location — lokalizacja maszyny wirtualnej. Możesz użyć [Get-AzLocation](/powershell/module/az.resources/get-azlocation) , aby znaleźć [region geograficzny](https://azure.microsoft.com/regions/) , który działa dla Ciebie.
- $myResourceGroup — nazwa grupy zasobów zawierającej maszynę wirtualną.
- $myVM — Nazwa maszyny wirtualnej.

## <a name="create-a-vm---simplified"></a>Tworzenie maszyny wirtualnej — uproszczona

| Zadanie | Polecenie |
| ---- | ------- |
| Tworzenie prostej maszyny wirtualnej | [New-AzVM](/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> New-AzVM ma zestaw *uproszczonych* parametrów, gdzie wszystkie wymagane są pojedynczymi nazwami. Wartość parametru-name będzie używana jako nazwa wszystkich zasobów wymaganych do utworzenia nowej maszyny wirtualnej. Można określić więcej, ale jest to wymagane.|
| Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego | New-AzVm-ResourceGroupName $myResourceGroup-Name $myVM ImageName "Moja Image" — Lokalizacja $location  <BR></BR><BR></BR>Musisz już utworzyć własny [zarządzany obraz](capture-image-resource.md). Możesz użyć obrazu, aby utworzyć wiele identycznych maszyn wirtualnych. |



## <a name="create-a-vm-configuration"></a>Tworzenie konfiguracji maszyny wirtualnej

| Zadanie | Polecenie |
| ---- | ------- |
| Tworzenie konfiguracji maszyny wirtualnej |$vm = [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) -VMName $MyVM-VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfiguracja maszyny wirtualnej służy do definiowania lub aktualizowania ustawień dla maszyny wirtualnej. Konfiguracja zostanie zainicjowana przy użyciu nazwy maszyny wirtualnej i jej [rozmiaru](../sizes.md). |
| Dodaj ustawienia konfiguracji |$vm = [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) -VM $VM-Windows-ComputerName $MyVM-Credential $cred-ProvisionVMAgent-EnableAutoUpdate<BR></BR><BR></BR>Ustawienia systemu operacyjnego, w tym [poświadczenia](/powershell/module/microsoft.powershell.security/get-credential) , są dodawane do obiektu konfiguracji, który został wcześniej utworzony przy użyciu polecenia New-AzVMConfig. |
| Dodawanie interfejsu sieciowego |$vm = [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) -VM $VM-ID $nic. #C1<BR></BR><BR></BR>Maszyna wirtualna musi mieć [interfejs sieciowy](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json) , aby komunikować się w sieci wirtualnej. Możesz również użyć [Get-AzNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) , aby pobrać istniejący obiekt interfejsu sieciowego. |
| Określ obraz platformy |$vm = [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) -VM $VM-PublisherName "publisher_name"-oferta "publisher_offer"-sku "product_sku"-Version "Najnowsza"<BR></BR><BR></BR>[Informacje o obrazie](cli-ps-findimage.md) są dodawane do obiektu konfiguracji, który został wcześniej utworzony przy użyciu polecenia New-AzVMConfig. Obiekt zwrócony z tego polecenia jest używany tylko podczas ustawiania dysku systemu operacyjnego do korzystania z obrazu platformy. |
| Tworzenie maszyny wirtualnej |[New-AzVM](/powershell/module/az.compute/new-azvm) -ResourceGroupName $MyResourceGroup-Location $Location-VM $VM<BR></BR><BR></BR>Wszystkie zasoby są tworzone w [grupie zasobów](../../azure-resource-manager/management/manage-resource-groups-powershell.md). Przed uruchomieniem tego polecenia Uruchom polecenie New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface i Set-AzVMOSDisk. |
| Aktualizowanie maszyny wirtualnej |[Update-AzVM](/powershell/module/az.compute/update-azvm) -ResourceGroupName $MYRESOURCEGROUP-VM $VM<BR></BR><BR></BR>Pobierz bieżącą konfigurację maszyny wirtualnej za pomocą polecenia Get-AzVM, Zmień ustawienia konfiguracji dla obiektu maszyny wirtualnej, a następnie Uruchom to polecenie. |

## <a name="get-information-about-vms"></a>Uzyskaj informacje o maszynach wirtualnych

| Zadanie | Polecenie |
| ---- | ------- |
| Wyświetlanie listy maszyn wirtualnych w ramach subskrypcji |[Get-AzVM](/powershell/module/az.compute/get-azvm) |
| Wyświetlanie listy maszyn wirtualnych w grupie zasobów |Get-AzVM — ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Aby uzyskać listę grup zasobów w subskrypcji, użyj polecenie [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup). |
| Uzyskiwanie informacji o maszynie wirtualnej |Get-AzVM-ResourceGroupName $myResourceGroup-Name $myVM |

## <a name="manage-vms"></a>Zarządzanie maszynami wirtualnymi
| Zadanie | Polecenie |
| --- | --- |
| Uruchamianie maszyny wirtualnej |[Start-AzVM](/powershell/module/az.compute/start-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Zatrzymywanie maszyny wirtualnej |[Stop-AzVM](/powershell/module/az.compute/stop-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Uruchom ponownie uruchomioną maszynę wirtualną |[Restart-AzVM](/powershell/module/az.compute/restart-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Usuwanie maszyny wirtualnej |[Remove-AzVM](/powershell/module/az.compute/remove-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |


## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z podstawowymi krokami dotyczącymi tworzenia maszyny wirtualnej w programie Utwórz maszynę wirtualną z [systemem Windows przy użyciu Menedżer zasobów i programu PowerShell](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json).
