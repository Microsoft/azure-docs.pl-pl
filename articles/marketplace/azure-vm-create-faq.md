---
title: Często zadawane pytania dotyczące maszyn wirtualnych w portalu Azure Marketplace
description: Często zadawane pytania występujące podczas tworzenia maszyny wirtualnej w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 131cd9b1de82213d89ee07f30aed25544f6fcb18
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284909"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Często zadawane pytania dotyczące maszyn wirtualnych w portalu Azure Marketplace

Te często zadawane pytania obejmują typowe problemy, które można napotkać podczas tworzenia oferty maszyny wirtualnej w portalu Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Jak mogę skonfigurować wirtualną sieć prywatną (VPN) do pracy z maszynami wirtualnymi?

Jeśli używasz modelu wdrażania Azure Resource Manager, masz trzy opcje:

- [Tworzenie bramy sieci VPN opartej na trasach za pomocą Azure Portal](../vpn-gateway/create-routebased-vpn-gateway-portal.md)
- [Tworzenie bramy sieci VPN opartej na trasach za pomocą Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Tworzenie bramy sieci VPN opartej na trasach za pomocą interfejsu wiersza polecenia](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Co to są zasady pomocy technicznej firmy Microsoft dotyczące uruchamiania oprogramowania serwerowego firmy Microsoft na maszynach wirtualnych opartych na platformie Azure?

Szczegóły można znaleźć w [pomocy technicznej oprogramowania serwera firmy Microsoft dla Microsoft Azure maszyn wirtualnych](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Jak zarządzać rozszerzeniem niestandardowego skryptu w maszynie wirtualnej?

Aby uzyskać szczegółowe informacje na temat używania niestandardowego rozszerzenia skryptu przy użyciu modułu Azure PowerShell, szablonów Azure Resource Manager i kroków rozwiązywania problemów w systemach Windows, zobacz [rozszerzenie niestandardowego skryptu dla systemu Windows](/azure/virtual-machines/extensions/custom-script-windows).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Czy obsługiwane są 32-bitowe aplikacje lub usługi w portalu Azure Marketplace?

Nie. Obsługiwane systemy operacyjne i standardowe usługi dla maszyn wirtualnych platformy Azure to wszystkie 64-bitowe. Chociaż większość 64-bitowych systemów operacyjnych obsługuje 32-bitowe wersje aplikacji w celu zapewnienia zgodności z poprzednimi wersjami, używanie aplikacji 32-bitowych jako części rozwiązania maszyny wirtualnej jest nieobsługiwane i zdecydowanie odradzane. Utwórz ponownie aplikację jako projekt 64-bitowy.

Więcej informacji można znaleźć w następujących artykułach:

- [Uruchamianie aplikacji 32-bitowych](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Obsługa 32-bitowych systemów operacyjnych na maszynach wirtualnych platformy Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Pomoc techniczna dotycząca oprogramowania serwerowego firmy Microsoft dla maszyn wirtualnych platformy Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Błąd: wirtualny dysk twardy jest już zarejestrowany w repozytorium obrazów jako zasób

Za każdym razem, gdy próbuję utworzyć obraz z moich wirtualnych dysków twardych, otrzymuję komunikat o błędzie "wirtualny dysk twardy jest już zarejestrowany w repozytorium obrazów jako zasób" w Azure PowerShell. Nie utworzono żadnego obrazu wcześniej ani nie znaleziono żadnego obrazu o tej nazwie na platformie Azure. Jak rozwiązać ten problem?

Ten problem zwykle występuje, gdy utworzono maszynę wirtualną z dysku VHD, który ma blokadę. Upewnij się, że maszyna wirtualna nie została przypisana z tego wirtualnego dysku twardego, a następnie spróbuj ponownie wykonać operację. Jeśli ten problem będzie się powtarzać, Otwórz bilet pomocy technicznej. Zobacz [Pomoc techniczna dla Centrum partnerskiego](support.md).

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z certyfikacją maszyn wirtualnych](azure-vm-create-certification-faq.md)
