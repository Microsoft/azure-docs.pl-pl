---
title: Red Hat Enterprise Linux Przenieś własne obrazy platformy Azure do własnych subskrypcji | Microsoft Docs
description: Dowiedz się więcej na temat przenoszenia własnych obrazów subskrypcji dla Red Hat Enterprise Linux na platformie Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 8896ed3eed291e6ec18c45df60e6079ada769b28
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993341"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux Przenieś własne obrazy z subskrypcji na platformę Azure

Obrazy Red Hat Enterprise Linux (RHEL) są dostępne na platformie Azure za pośrednictwem modelu "płatność zgodnie z rzeczywistym użyciem" lub "Przenieś własną subskrypcję" (BYOS) (Red Hat Gold Image). Ten artykuł zawiera omówienie obrazów Red Hat Gold na platformie Azure.

>[!NOTE]
> RHEL BYOS Gold są dostępne w publicznych chmurach platformy Azure (komercyjnych) i Azure Government. Nie są one dostępne na platformie Azure (Chiny) ani w chmurach usługi Azure Blackforest.

## <a name="important-points-to-consider"></a>Ważne kwestie, które należy wziąć pod uwagę

- Obrazy Red Hat Gold dostępne w tym programie to gotowe do produkcji obrazy RHEL podobne do obrazów RHEL z opcją płatność zgodnie z rzeczywistym użyciem w witrynie Azure Marketplace.
- Obrazy są zgodne z bieżącymi zasadami opisanymi w [Red Hat Enterprise Linux obrazów na platformie Azure](./redhat-images.md).
- Standardowe zasady pomocy technicznej mają zastosowanie do maszyn wirtualnych utworzonych na podstawie tych obrazów.
- Maszyny wirtualne obsługiwane z obrazów Red Hat Gold nie mają opłat RHEL związanych z obrazami z opcją płatność zgodnie z rzeczywistym użyciem.
- Obrazy są nieuprawnione. Aby rejestrować i subskrybować maszyny wirtualne w celu uzyskania aktualizacji z firmy Red Hat, należy użyć narzędzia Red Hat Subscription-Manager.
- Obecnie nie można dynamicznie przełączać modeli rozliczeń BYOS i płatność zgodnie z rzeczywistym użyciem dla obrazów systemu Linux. Aby przełączyć model rozliczeń, należy ponownie wdrożyć maszynę wirtualną z odpowiedniego obrazu.

>[!NOTE]
> Obrazy generacji 2 RHEL BYOS nie są obecnie dostępne w ramach oferty portalu Marketplace. Jeśli potrzebujesz obrazu generacji 2 RHEL BYOS, odwiedź pulpit nawigacyjny dostępu do chmury w usłudze Red Hat Subscription Management. Aby uzyskać więcej informacji, zobacz [dokumentację Red Hat](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Wymagania i warunki dostępu do obrazów Red Hat Gold

1. Zapoznaj się z przepisami [programu Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) . Włączaj subskrypcje Red Hat dla dostępu do chmury w [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). Musisz mieć dostęp do subskrypcji platformy Azure, które mają zostać zarejestrowane na potrzeby dostępu do chmury.

1. Jeśli subskrypcje Red Hat, dla których włączono dostęp w chmurze, spełniają wymagania dotyczące uprawnień, subskrypcje platformy Azure są automatycznie włączane na potrzeby dostępu do obrazu Gold.

### <a name="expected-time-for-image-access"></a>Oczekiwany czas dostępu do obrazu

Po zakończeniu czynności związanych z włączeniem dostępu do chmury Red Hat zweryfikuje Twoje uprawnienia do obrazów Red Hat Gold. Jeśli sprawdzanie poprawności zakończy się pomyślnie, uzyskasz dostęp do obrazów Gold w ciągu trzech godzin.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Użyj obrazów Red Hat Gold z Azure Portal

1. Po otrzymaniu przez subskrypcję platformy Azure dostępu do obrazów Red Hat Gold można je zlokalizować w [Azure Portal](https://portal.azure.com). Przejdź do pozycji **Tworzenie zasobu**  >  **Zobacz wszystko**.

1. W górnej części strony zobaczysz, że masz prywatne oferty.

    ![Oferty prywatne w witrynie Marketplace](./media/rhel-byos-privateoffers.png)

1. Wybierz łącze purpurowe lub przewiń w dół do dołu strony, aby wyświetlić oferty prywatne.

1. Dalsze Inicjowanie obsługi w interfejsie użytkownika nie różni się od innych istniejących obrazów Red Hat. Wybierz wersję RHEL i postępuj zgodnie z monitami, aby zainicjować obsługę administracyjną maszyny wirtualnej. Ten proces umożliwia również zaakceptowanie warunków obrazu w ostatnim kroku.

>[!NOTE]
>Te kroki do tej pory nie pozwalają na wdrażanie programistyczne obrazu Red Hat Gold. Dodatkowy krok jest wymagany, zgodnie z opisem w sekcji "dodatkowe informacje".

Pozostała część tego dokumentu koncentruje się na metodzie interfejsu wiersza polecenia w celu aprowizacji i akceptowania warunków na obrazie. Interfejs użytkownika i interfejsu wiersza polecenia są w pełni wymienne w miarę jak końcowy wynik (zainicjowana RHEL Gold Image VM).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Korzystanie z obrazów Red Hat Gold z poziomu interfejsu wiersza polecenia platformy Azure

Poniższe instrukcje przeprowadzą Cię przez proces wdrażania wstępnego dla maszyny wirtualnej RHEL przy użyciu interfejsu wiersza polecenia platformy Azure. W tych instrukcjach przyjęto założenie, że masz [zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Upewnij się, że używasz wszystkich małych liter w odwołaniach do wydawcy, oferty, planu i obrazu dla wszystkich poniższych poleceń.

1. Sprawdź, czy jesteś w żądanej subskrypcji.

    ```azurecli
    az account show -o=json
    ```

1. Utwórz grupę zasobów dla maszyny wirtualnej z systemem Red Hat Gold.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Zaakceptuj warunki obrazu.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Te postanowienia muszą zostać zaakceptowane *raz dla każdej subskrypcji platformy Azure, dla jednostki SKU obrazu*.

1. Obowiązkowe Sprawdź poprawność wdrożenia maszyny wirtualnej za pomocą następującego polecenia:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. Inicjowanie obsługi administracyjnej maszyny wirtualnej przez uruchomienie tego samego polecenia, jak pokazano w poprzednim przykładzie bez `--validate` argumentu.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. Sprawdź, czy masz nieuprawniony obraz do maszyny wirtualnej. Aby wykonać ten krok, uruchom polecenie `sudo yum repolist` . W przypadku RHEL 8 Użyj `sudo dnf repolist` . W danych wyjściowych zostanie wyświetlony monit o zarejestrowanie maszyny wirtualnej z systemem Red Hat przy użyciu Subscription-Manager.

>[!NOTE]
>W RHEL 8 `dnf` i `yum` są zamienne. Aby uzyskać więcej informacji, zobacz [Przewodnik administratora programu RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Korzystanie z obrazów Red Hat Gold z programu PowerShell

Poniższy skrypt jest przykładem. Zastąp grupę zasobów, lokalizację, nazwę maszyny wirtualnej, informacje o logowaniu i inne zmienne z wybraną konfiguracją. Informacje o wydawcy i planie muszą być pisane małymi literami.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Szyfrowanie Red Hat Enterprise Linux dołączenia do własnych złotych obrazów subskrypcji

Red Hat Enterprise Linux BYOS Gold można zabezpieczyć przy użyciu [Azure Disk Encryption](../../linux/disk-encryption-overview.md). Aby można było włączyć szyfrowanie, *należy* zarejestrować subskrypcję. Aby uzyskać więcej informacji na temat rejestrowania obrazu RHEL BYOS Gold, zobacz [jak zarejestrować i subskrybować system w witrynie Red Hat Customer Portal przy użyciu programu Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273). Jeśli masz aktywną subskrypcję Red Hat, możesz również przeczytać temat [Tworzenie kluczy aktywacji w portalu Red Hat Customer Portal](https://access.redhat.com/articles/1378093).

Azure Disk Encryption nie są obsługiwane w [obrazach niestandardowych Red Hat](../../linux/redhat-create-upload-vhd.md). Dodatkowe Azure Disk Encryption wymagania i wymagania wstępne są udokumentowane w [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Aby uzyskać instrukcje dotyczące stosowania Azure Disk Encryption, zobacz [Azure Disk Encryption scenariusze dotyczące maszyn wirtualnych z systemem Linux](../../linux/disk-encryption-linux.md) i pokrewnych artykułów.

## <a name="additional-information"></a>Dodatkowe informacje

- Jeśli spróbujesz zainicjować obsługę administracyjną maszyny wirtualnej w subskrypcji, która nie jest włączona dla tej oferty, zostanie wyświetlony następujący komunikat:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    W takim przypadku skontaktuj się z firmą Microsoft lub Red Hat, aby włączyć subskrypcję.

- Jeśli zmodyfikujesz migawkę z obrazu RHEL BYOS i spróbujesz opublikować ten obraz niestandardowy w [galerii obrazów udostępnionych](../../linux/shared-image-galleries.md), musisz podać informacje o planie zgodne z oryginalnym źródłem migawki. Na przykład polecenie może wyglądać następująco:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Zanotuj parametry planu w wierszu końcowym.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) nie są obsługiwane w obrazach niestandardowych.

- Jeśli używasz automatyzacji do aprowizacji maszyn wirtualnych z obrazów RHEL BYOS, musisz dostarczyć parametry planu podobne do tego, co zostało pokazane w przykładowych poleceniach. Na przykład, jeśli używasz Terraform, podajesz informacje o planie w [bloku planu](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Następne kroki

- Więcej szczegółów na temat usługi Red Hat Cloud Access jest dostępnych w [dokumentacji chmury publicznej Red Hat](https://access.redhat.com/public-cloud)
- Instrukcje krok po kroku i szczegółowe informacje o programie dla dostępu do chmury można znaleźć w [dokumentacji usługi Red Hat Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Aby dowiedzieć się więcej na temat infrastruktury aktualizacji Red Hat, zobacz [Azure Red Hat Update Infrastructure](./redhat-rhui.md).
- Aby dowiedzieć się więcej na temat wszystkich obrazów Red Hat na platformie Azure, zobacz [stronę z dokumentacją](./redhat-images.md).
- Aby uzyskać informacje na temat zasad pomocy technicznej systemu Red Hat dla wszystkich wersji programu RHEL, zobacz stronę [cyklu życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
- Dodatkowe dokumenty dotyczące obrazów RHEL Gold można znaleźć w [dokumentacji Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
