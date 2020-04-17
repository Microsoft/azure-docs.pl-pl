---
title: Typowe polecenia programu PowerShell dla maszyn wirtualnych platformy Azure
description: Typowe polecenia programu PowerShell, aby rozpocząć tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 3d08693b6b07b5a2f1fb40d0c4758ab43729bd76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456366"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Typowe polecenia programu PowerShell do tworzenia maszyn wirtualnych platformy Azure i zarządzania nimi

W tym artykule omówiono niektóre polecenia programu Azure PowerShell, których można używać do tworzenia maszyn wirtualnych i zarządzania nimi w ramach subskrypcji platformy Azure.  Aby uzyskać bardziej szczegółową pomoc dotyczącą określonych przełączników i opcji wiersza polecenia, można użyć *polecenia* **Get-Help** .

 

Te zmienne mogą być przydatne, jeśli uruchomisz więcej niż jedno polecenie w tym artykule:

- $location — lokalizacja maszyny wirtualnej. Za pomocą [funkcji Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) można znaleźć [region geograficzny,](https://azure.microsoft.com/regions/) który działa dla Ciebie.
- $myResourceGroup — nazwa grupy zasobów zawierającej maszynę wirtualną.
- $myVM — nazwa maszyny wirtualnej.

## <a name="create-a-vm---simplified"></a>Tworzenie maszyny Wirtualnej — uproszczone

| Zadanie | Polecenie |
| ---- | ------- |
| Tworzenie prostej maszyny Wirtualnej | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Nazwa $myVM <BR></BR><BR></BR> New-AzVM ma zestaw *uproszczonych* parametrów, gdzie wszystko, co jest wymagane jest pojedyncza nazwa. Wartość -Name będzie używana jako nazwa dla wszystkich zasobów wymaganych do utworzenia nowej maszyny Wirtualnej. Można określić więcej, ale jest to wszystko, co jest wymagane.|
| Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Lokalizacja $location  <BR></BR><BR></BR>Musisz już utworzyć własny [obraz zarządzany](capture-image-resource.md). Za pomocą obrazu można tworzyć wiele identycznych maszyn wirtualnych. |



## <a name="create-a-vm-configuration"></a>Tworzenie konfiguracji maszyny Wirtualnej

| Zadanie | Polecenie |
| ---- | ------- |
| Tworzenie konfiguracji maszyny Wirtualnej |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfiguracja maszyny Wirtualnej służy do definiowania lub aktualizowania ustawień maszyny Wirtualnej. Konfiguracja jest inicjowana z nazwą maszyny Wirtualnej i jej [rozmiarem](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Dodawanie ustawień konfiguracji |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -Nazwa komputera $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Ustawienia systemu operacyjnego, w tym [poświadczenia,](https://technet.microsoft.com/library/hh849815.aspx) są dodawane do obiektu konfiguracji utworzonego wcześniej przy użyciu programu New-AzVMConfig. |
| Dodawanie interfejsu sieciowego |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic. Identyfikator<BR></BR><BR></BR>Maszyna wirtualna musi mieć [interfejs sieciowy](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) do komunikowania się w sieci wirtualnej. Można również użyć [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) do pobierania istniejącego obiektu interfejsu sieciowego. |
| Określanie obrazu platformy |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[Informacje o obrazie](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) są dodawane do obiektu konfiguracji utworzonego wcześniej przy użyciu funkcji New-AzVMConfig. Obiekt zwrócony z tego polecenia jest używany tylko wtedy, gdy dysk systemu operacyjnego jest używany do używania obrazu platformy. |
| Tworzenie maszyny wirtualnej |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Wszystkie zasoby są tworzone w [grupie zasobów](../../azure-resource-manager/management/manage-resource-groups-powershell.md). Przed uruchomieniem tego polecenia uruchom program New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface i Set-AzVMOSDisk. |
| Aktualizowanie maszyny Wirtualnej |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Pobierz bieżącą konfigurację maszyny Wirtualnej przy użyciu polecenia Get-AzVM, zmień ustawienia konfiguracji obiektu maszyny Wirtualnej, a następnie uruchom to polecenie. |

## <a name="get-information-about-vms"></a>Uzyskaj informacje o maszynach wirtualnych

| Zadanie | Polecenie |
| ---- | ------- |
| Wyświetlanie listy maszyn wirtualnych w ramach subskrypcji |[Get-AzVM ( Get-AzVM )](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Wyświetlanie listy maszyn wirtualnych w grupie zasobów |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Aby uzyskać listę grup zasobów w ramach subskrypcji, użyj programu [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Uzyskiwanie informacji o maszynie wirtualnej |Get-AzVM -ResourceGroupName $myResourceGroup -Nazwa $myVM |

## <a name="manage-vms"></a>Zarządzanie maszynami wirtualnymi
| Zadanie | Polecenie |
| --- | --- |
| Uruchamianie maszyny wirtualnej |$myResourceGroup nazwa [start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName -Nazwa $myVM |
| Zatrzymywanie maszyny wirtualnej |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Nazwa $myVM |
| Ponowne uruchamianie maszyny wirtualnej |[Ponowne uruchomienie-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Nazwa $myVM |
| Usuwanie maszyny wirtualnej |$myResourceGroup $myResourceGroup nazwa [$myVM Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName |


## <a name="next-steps"></a>Następne kroki
* Zobacz podstawowe kroki tworzenia maszyny wirtualnej w temacie Tworzenie maszyny [wirtualnej z systemem Windows przy użyciu Menedżera zasobów i programu PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

