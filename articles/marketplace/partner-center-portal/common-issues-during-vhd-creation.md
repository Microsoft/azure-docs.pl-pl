---
title: Typowe problemy podczas tworzenia dysku VHD (FAQ)
description: Często zadawane pytania dotyczące typowych problemów związanych z tworzeniem wirtualnego dysku twardego (VHD).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: f4d30cdb931d6523eba3aac003caeee38a8c024d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653532"
---
# <a name="common-issues-during-vhd-creation"></a>Typowe problemy podczas tworzenia wirtualnego dysku twardego

Te często zadawane pytania obejmują typowe problemy, które można napotkać podczas tworzenia wirtualnego dysku twardego (VHD) dla oferty maszyny wirtualnej platformy Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Jak mogę utworzyć maszynę wirtualną na podstawie Azure Portal przy użyciu dysku VHD w usłudze Premium Storage?

Portal Azure Marketplace obecnie nie obsługuje tworzenia ofert maszyn wirtualnych z obrazów w zarządzanym magazynie lub na platformie Azure Premium Storage. Aby uzyskać szczegółowe informacje, zobacz [Omówienie usługi Azure Managed disks](../../virtual-machines/managed-disks-overview.md).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Czy mogę używać maszyn wirtualnych generacji 2 dla ofert?

Nie, obsługiwane są tylko wirtualne dyski twarde generacji 1. Obecnie pracujemy nad zespołem Microsoft Azure platform, aby zbadać pomoc techniczną dla maszyn wirtualnych 2. generacji. Aby uzyskać szczegółowe informacje, zobacz [należy utworzyć maszynę wirtualną generacji 1 lub 2 w funkcji Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Jak mogę zmienić nazwę hosta?

Nie można. Po utworzeniu maszyny wirtualnej użytkownicy (w tym właściciele) nie mogą zaktualizować nazwy hosta.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Jak mogę zresetować usługę Pulpit zdalny lub hasło logowania?

W tych artykułach wyjaśniono, jak wykonać Resetowanie usług pulpitu zdalnego dla maszyn wirtualnych z systemem Windows i Linux:

* [Jak zresetować usługę Pulpit zdalny lub jej hasło logowania dla maszyny wirtualnej z systemem Windows](/azure/virtual-machines/troubleshooting/reset-rdp)
* [Jak zresetować hasło maszyny wirtualnej systemu Linux lub klucz SSH, napraw konfigurację SSH i Sprawdź spójność dysku przy użyciu rozszerzenia VMAccess](/azure/virtual-machines/extensions/vmaccess)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Jak mogę generować nowe certyfikaty SSH?

Generowanie certyfikatów jest wyjaśnione w [certyfikatach obrazu maszyny wirtualnej platformy Azure](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Jak mogę skonfigurować wirtualną sieć prywatną (VPN) do pracy z maszynami wirtualnymi?

Jeśli używasz modelu wdrażania Azure Resource Manager, masz trzy opcje:

* [Tworzenie bramy sieci VPN opartej na trasach za pomocą Azure Portal](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [Tworzenie bramy sieci VPN opartej na trasach za pomocą Azure PowerShell](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [Tworzenie bramy sieci VPN opartej na trasach za pomocą interfejsu wiersza polecenia](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Co to są zasady pomocy technicznej firmy Microsoft dotyczące uruchamiania oprogramowania serwerowego firmy Microsoft na maszynach wirtualnych opartych na platformie Azure?

Szczegóły można znaleźć w [pomocy technicznej oprogramowania serwera firmy Microsoft dla Microsoft Azure maszyn wirtualnych](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Czy z maszynami wirtualnymi są skojarzone unikatowe identyfikatory?

Tak, jeśli jest hostowana na platformie Azure. Platforma Azure przypisuje unikatowy identyfikator, nazywany [unikatowym identyfikatorem maszyny wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/), do każdego nowo utworzonego zasobu maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz unikatowy identyfikator maszyny wirtualnej platformy Azure. Ten identyfikator można także uzyskać za pomocą [interfejsu API listy](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Jak zarządzać rozszerzeniem niestandardowego skryptu w maszynie wirtualnej?

Aby uzyskać szczegółowe informacje na temat używania niestandardowego rozszerzenia skryptu przy użyciu modułu Azure PowerShell, szablonów Azure Resource Manager i kroków rozwiązywania problemów w systemach Windows, zobacz [rozszerzenie niestandardowego skryptu dla systemu Windows](/azure/virtual-machines/extensions/custom-script-windows).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Czy obsługiwane są 32-bitowe aplikacje lub usługi w portalu Azure Marketplace?

Ogólnie rzecz biorąc — nie. Obsługiwane systemy operacyjne i standardowe usługi dla maszyn wirtualnych platformy Azure to wszystkie 64-bitowe. Chociaż większość 64-bitowych systemów operacyjnych obsługuje 32-bitowe wersje aplikacji w celu zapewnienia zgodności z poprzednimi wersjami, używanie aplikacji 32-bitowych jako części rozwiązania maszyny wirtualnej jest nieobsługiwane i zdecydowanie odradzane. Utwórz ponownie aplikację jako projekt 64-bitowy.

Więcej informacji można znaleźć w następujących artykułach:

* [Uruchamianie aplikacji 32-bitowych](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Obsługa 32-bitowych systemów operacyjnych na maszynach wirtualnych platformy Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Pomoc techniczna dotycząca oprogramowania serwerowego firmy Microsoft dla maszyn wirtualnych platformy Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Błąd: wirtualny dysk twardy jest już zarejestrowany w repozytorium obrazów jako zasób

Za każdym razem, gdy próbuję utworzyć obraz z moich wirtualnych dysków twardych, otrzymuję komunikat o błędzie "wirtualny dysk twardy jest już zarejestrowany w repozytorium obrazów jako zasób" w Azure PowerShell. Nie utworzono żadnego obrazu wcześniej ani nie znaleziono żadnego obrazu o tej nazwie na platformie Azure. Jak rozwiązać ten problem?

Ten problem zwykle występuje, gdy utworzono maszynę wirtualną z dysku VHD, który ma blokadę. Upewnij się, że maszyna wirtualna nie została przypisana z tego wirtualnego dysku twardego, a następnie spróbuj ponownie wykonać operację. Jeśli ten problem będzie się powtarzać, Otwórz bilet pomocy technicznej. Zobacz [Pomoc techniczna dla Centrum partnerskiego](support.md).
