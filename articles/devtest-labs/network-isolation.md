---
title: Izolacja sieci w Azure DevTest Labs
description: Informacje na temat izolacji sieci w Azure DevTest Labs.
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: fbd2725cd3677e03cadbe0ae1f060b141f5d212b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875964"
---
# <a name="network-isolation-in-devtest-labs"></a>Izolacja sieci w DevTest Labs

[Usługa Azure Virtual Network](../virtual-network/virtual-networks-overview.md) działa jako granica zabezpieczeń, izolowanie zasobów platformy Azure od publicznego Internetu. Możesz również dołączyć do sieci wirtualnej platformy Azure, aby umożliwić bezpieczne łączenie się z zasobami Premium. W DevTest Labs można [wyodrębnić wszystkie maszyny wirtualne](devtest-lab-configure-vnet.md) i [środowiska laboratorium do sieci](connect-environment-lab-virtual-network.md) , aby zapewnić, że zasoby laboratorium będą zgodne z zasadami sieci organizacji. 

Jako właściciel laboratorium można również całkowicie wyizolować laboratorium, co oznacza, że oprócz izolowania maszyn wirtualnych i środowisk do wybranej sieci można wyizolować konto magazynu laboratorium i magazyny kluczy utworzone w ramach subskrypcji. W tym artykule przedstawiono sposób tworzenia laboratorium izolowanego od sieci. 

Zapoznaj się również z następującymi artykułami:

- [Jak DevTest Labs używa konta magazynu laboratorium](encrypt-storage.md)
- [Jak DevTest Labs używa magazynów kluczy](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> Izolacja sieci jest obecnie obsługiwana tylko dla nowych celów tworzenia laboratoriów.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Procedura włączania izolacji sieci podczas tworzenia laboratorium

1. Podczas tworzenia laboratorium przejdź do karty **Sieć** .
1. Możesz wybrać sieć **domyślną** , która zostanie utworzona przez laboratorium, lub wybrać istniejącą sieć z listy rozwijanej. Będziesz mieć możliwość wyboru sieci, które znajdują się w tym samym regionie i subskrypcji, co w przypadku laboratorium. 

    > [!div class="mx-imgBorder"]
    > ![Tworzenie laboratorium](./media/network-isolation/create-lab.png)
1. Wybierz podsieć.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie podsieci](./media/network-isolation/create-lab-subnet.png)
1. W przypadku wybrania opcji izolowania zasobów laboratorium (konta magazynu Lab i magazynu kluczy) do sieci domyślnej nie są potrzebne żadne dalsze czynności, a laboratorium będzie obsługiwać izolowane zasoby.
 
    > [!div class="mx-imgBorder"]
    > ![Izolacja sieciowa](./media/network-isolation/isolate-lab-resources.png)
1. W przypadku wybrania opcji izolowania zasobów laboratorium (konta magazynu Lab i magazynu kluczy) do istniejącej sieci wybranej przez użytkownika, należy wykonać następujące kroki po utworzeniu programu Lab, aby upewnić się, że laboratorium nadal działa w trybie izolowanym. 
 
    > [!div class="mx-imgBorder"]
    > ![Izolowanie zasobów](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > Właściciel laboratorium musi wykonać te czynności przed skonfigurowaniem i/lub utworzeniem wszelkich zasobów w laboratorium.

### <a name="steps-to-follow-post-lab-creation"></a>Kroki prowadzące do tworzenia po utworzeniu laboratorium

1. Na stronie głównej laboratorium wybierz **grupę zasobów** na stronie **Przegląd** . Powinna zostać wyświetlona strona **grupy zasobów** dla grupy zasobów zawierającej laboratorium. 
 
   > [!div class="mx-imgBorder"]
   > ![Laboratorium contoso](./media/network-isolation/contoso-lab.png)
1. Wybierz konto usługi Azure Storage dla laboratorium. Konwencja nazewnictwa dla konta magazynu laboratorium to:<*labNameWithoutInvalidCharacters* > *<4 cyfr*>. Na przykład jeśli nazwa laboratorium to contosolab, nazwa konta magazynu może być acontosolab1234.
 
   > [!div class="mx-imgBorder"]
   > ![Firma Contoso test](./media/network-isolation/contoso-test.png)
1. Na koncie magazynu przejdź do obszaru zapory i sieci wirtualne i upewnij się, że jest zaznaczone pole wyboru Zezwalaj na dostęp do tego konta magazynu zaufanych usług firmy Microsoft. Ponieważ [DevTest Labs jest zaufaną usługą firmy Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services), ta opcja umożliwi normalne działanie laboratorium w trybie izolowanym od sieci. 

   > [!div class="mx-imgBorder"]
   > ![Zapory firmy Contoso Lab](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Następnie kliknij pozycję **+ Dodaj istniejącą sieć wirtualną**, wybierz sieć wirtualną i podsieć wybraną podczas tworzenia laboratorium i kliknij pozycję **Włącz**. 

   > [!div class="mx-imgBorder"]
   > ![Moja sieć wirtualna contoso](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Po pomyślnym włączeniu punktu końcowego usługi dla wybranej sieci wirtualnej kliknij pozycję **Dodaj**. 

   > [!div class="mx-imgBorder"]
   > ![Dodaj](./media/network-isolation/contoso-firewall-add.png)
 
Dzięki temu usługa Azure Storage zezwoli na połączenia przychodzące z dodanej sieci wirtualnej i umożliwi pomyślne działanie laboratorium w trybie izolowanym od sieci. 

Możesz również zautomatyzować te kroki, aby skonfigurować to ustawienie dla wielu laboratoriów. 

[Więcej informacji na temat zarządzania domyślnymi regułami dostępu do sieci dla usługi Azure Storage przy użyciu programu PowerShell i interfejsu wiersza polecenia](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=/azure/virtual-network/toc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Kwestie, które należy zapamiętać podczas korzystania z laboratorium w trybie izolowanym w sieci

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Uzyskiwanie dostępu do konta magazynu laboratorium poza laboratorium 

W ramach laboratorium izolowanego od sieci, w przypadku akcji, takich jak przekazywanie wirtualnego dysku twardego do konta magazynu laboratorium do tworzenia obrazów niestandardowych, właściciel laboratorium będzie musiał jawnie włączyć dostęp do konta magazynu z dozwolonego punktu końcowego. Aby to zrobić, możesz utworzyć maszynę wirtualną i bezpiecznie uzyskać dostęp do konta magazynu laboratorium z tej maszyny wirtualnej. 

[Dowiedz się więcej o uzyskiwaniu dostępu do konta magazynu prywatnie z poziomu maszyny wirtualnej](../private-link/create-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Eksportowanie danych użycia z laboratorium 

W ramach laboratorium izolowanego od sieci do [eksportowania prywatnych danych użycia dla laboratorium](personal-data-delete-export.md), właściciel laboratorium będzie jawnie musiał podać konto magazynu i wygenerować obiekt BLOB na koncie do przechowywania danych. 

Jeśli konto magazynu nie zostanie podane, ta operacja zakończy się niepowodzeniem w trybie izolowanym od sieci, ponieważ konto magazynu laboratorium nie będzie dostępne dla laboratorium do użycia w przypadku braku konta magazynu dostarczonego przez klienta. 

[Dowiedz się więcej na temat eksportowania danych użycia laboratorium na określonym koncie magazynu](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Następne kroki

[Automatyczne tworzenie lub modyfikowanie laboratoriów przy użyciu szablonów Azure Resource Manager i programu PowerShell](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
