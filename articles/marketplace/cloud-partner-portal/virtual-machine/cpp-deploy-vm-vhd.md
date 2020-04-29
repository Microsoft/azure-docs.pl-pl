---
title: Wdrażanie maszyny wirtualnej z poziomu wirtualnych dysków twardych dla portalu Azure Marketplace
description: Wyjaśnia, jak zarejestrować maszynę wirtualną na podstawie wirtualnego dysku twardego wdrożonego na platformie Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: b02fda545ac135735186885d7db597885bf6cc21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147964"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Wdróż maszynę wirtualną na podstawie Twoich dysków VHD

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie zasobów technicznych maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) , aby zarządzać migrowanymi ofertami.

W tej sekcji opisano sposób wdrażania maszyny wirtualnej na podstawie wirtualnego dysku twardego (VHD) wdrożonego na platformie Azure.  Zawiera listę wymaganych narzędzi oraz sposób ich użycia w celu utworzenia obrazu maszyny wirtualnej użytkownika, a następnie wdrożenia go na platformie Azure przy użyciu skryptów programu PowerShell.

Po przekazaniu wirtualnych dysków twardych (VHD) — uogólniony wirtualny dysk twardy systemu operacyjnego i zero lub więcej dysków z danymi — na konto usługi Azure Storage możesz zarejestrować je jako obraz maszyny wirtualnej użytkownika. Następnie możesz przetestować ten obraz. Ponieważ wirtualny dysk twardy systemu operacyjnego jest uogólniony, nie można bezpośrednio wdrożyć maszyny wirtualnej, podając adres URL dysku VHD.

Aby dowiedzieć się więcej o obrazach maszyn wirtualnych, zobacz następujące wpisy w blogu:

- [Obraz maszyny wirtualnej](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Obraz maszyny wirtualnej programu PowerShell "jak to zrobić"](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Wymaganie wstępne: Zainstaluj niezbędne narzędzia

Jeśli jeszcze tego nie zrobiono, Zainstaluj Azure PowerShell i interfejs wiersza polecenia platformy Azure, wykonując następujące instrukcje:

- [Instalowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Kroki wdrażania

Aby utworzyć i wdrożyć obraz maszyny wirtualnej użytkownika, należy wykonać następujące czynności:

1. Utwórz obraz maszyny wirtualnej użytkownika, który wymaga przechwytywania i uogólniania obrazu. 
2. Utwórz certyfikaty i Zapisz je w nowym Azure Key Vault. Certyfikat jest wymagany do nawiązania bezpiecznego połączenia usługi WinRM z maszyną wirtualną.  Podano szablon Azure Resource Manager i skrypt Azure PowerShell. 
3. Wdróż maszynę wirtualną z obrazu maszyny wirtualnej użytkownika przy użyciu dostarczonego szablonu i skryptu.

Po wdrożeniu maszyny wirtualnej możesz przeprowadzić [certyfikowanie obrazu maszyny wirtualnej](./cpp-certify-vm.md).

1. Kliknij kolejno pozycje **Nowy** i Wyszukaj **wdrożenie szablonu**, a następnie wybierz opcję **Kompiluj własny szablon w edytorze**.  <br/>
   ![Kompiluj szablon wdrożenia wirtualnego dysku twardego w Azure Portal](./media/publishvm_021.png)

1. Skopiuj i wklej ten [szablon JSON](./cpp-deploy-json-template.md) do edytora, a następnie kliknij przycisk **Zapisz**. <br/>
   ![Zapisz szablon wdrożenia wirtualnego dysku twardego w Azure Portal](./media/publishvm_022.png)

1. Podaj wartości parametrów dla wyświetlanych stron właściwości **wdrożenia niestandardowego** .

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Konstruktora**              |   **Opis**                                                            |
   |  -------------              |   ---------------                                                            |
   | Nazwa konta magazynu użytkownika   | Nazwa konta magazynu, w którym znajduje się uogólniony wirtualny dysk twardy                    |
   | Nazwa kontenera magazynu użytkownika | Nazwa kontenera, w którym znajduje się uogólniony wirtualny dysk twardy                          |
   | Nazwa DNS dla publicznego adresu IP      | Nazwa DNS publicznego adresu IP. Nazwa DNS jest maszyną wirtualną, która zostanie zdefiniowana w witrynie Azure Portal po wdrożeniu oferty.  |
   | Nazwa użytkownika administratora             | Nazwa użytkownika konta administratora dla nowej maszyny wirtualnej                                  |
   | Hasło administratora              | Hasło konta administratora dla nowej maszyny wirtualnej                                  |
   | Typ systemu operacyjnego                     | System operacyjny maszyny wirtualnej `Windows` \| :`Linux`                                    |
   | Identyfikator subskrypcji             | Identyfikator wybranej subskrypcji                                      |
   | Lokalizacja                    | Lokalizacja geograficzna wdrożenia                                        |
   | Rozmiar maszyny wirtualnej                     | [Rozmiar maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), na przykład`Standard_A2` |
   | Nazwa publicznego adresu IP      | Nazwa publicznego adresu IP                                               |
   | Nazwa maszyny wirtualnej                     | Nazwa nowej maszyny wirtualnej                                                           |
   | Nazwa Virtual Network        | Nazwa sieci wirtualnej używanej przez MASZYNę wirtualną                                   |
   | Nazwa karty sieciowej                    | Nazwa karty sieciowej z uruchomioną siecią wirtualną               |
   | ADRES URL WIRTUALNEGO DYSKU TWARDEGO                     | Pełny adres URL dysku VHD systemu operacyjnego                                                     |
   |  |  |
            
1. Po podaniu tych wartości kliknij pozycję **Kup**. 

Platforma Azure rozpocznie wdrażanie: tworzy nową maszynę wirtualną z określonym niezarządzanym wirtualnym dyskiem twardym w określonej ścieżce konta magazynu.  Postęp można śledzić w Azure Portal, klikając pozycję **Virtual Machines** po lewej stronie portalu.  Po utworzeniu maszyny wirtualnej stan zmieni się z `Starting` na. `Running` 


### <a name="deploy-a-vm-from-powershell"></a>Wdrażanie maszyny wirtualnej przy użyciu programu PowerShell

Aby wdrożyć dużą maszynę wirtualną na podstawie utworzonego właśnie uogólnionego obrazu maszyny wirtualnej, użyj następujących poleceń cmdlet.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Następne kroki

Następnie utworzysz [obraz maszyny wirtualnej użytkownika](cpp-create-user-image.md) dla swojego rozwiązania.

