---
title: Typowe problemy związane z tworzeniem wirtualnych dysków twardych (FAQ) dla portalu Azure Marketplace
description: Często zadawane pytania dotyczące tworzenia dysków VHD i związanych z nimi problemów.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: dsindona
ms.openlocfilehash: df219ad7428eed5283c7a30a232308dca84fd25f
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146972"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Typowe problemy podczas tworzenia dysku VHD (FAQ)

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie często zadawanych pytań dotyczących dysku VHD](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation) , aby zarządzać przemigrowanymi

Poniższe często zadawane pytania obejmują typowe problemy występujące podczas tworzenia wirtualnego dysku twardego (VHD) i tworzenie maszyn wirtualnych dla ofert maszyn wirtualnych. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Jak utworzyć maszynę wirtualną na podstawie Azure Portal przy użyciu wirtualnego dysku twardego, który został przekazany do usługi Premium Storage?

Portal Azure Marketplace obecnie nie obsługuje tworzenia ofert maszyn wirtualnych na podstawie obrazów znajdujących się w zarządzanym magazynie lub na platformie Azure Premium Storage.  Aby uzyskać więcej informacji na temat tych opcji magazynu, zobacz [Omówienie usługi Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Czy można używać maszyn wirtualnych generacji 2 dla ofert?

Nie, obsługiwane są tylko wirtualne dyski twarde generacji 1.  Obecnie pracujemy nad zespołem Microsoft Azure platform, aby zbadać pomoc techniczną dla maszyn wirtualnych 2. generacji.  Aby uzyskać więcej informacji o różnicach, zobacz [czy należy utworzyć maszynę wirtualną generacji 1 lub 2 w funkcji Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Jak zmienić nazwę hosta?

Nie możesz.  Po utworzeniu maszyny wirtualnej użytkownicy (w tym właściciele) nie mogą zaktualizować nazwy hosta.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Jak zresetować usługę Pulpit zdalny lub hasło logowania?

W poniższych artykułach opisano sposób wykonywania operacji resetowania usług pulpitu zdalnego dla maszyn wirtualnych opartych na systemie Windows i Linux:   

- [Jak zresetować usługę Pulpit zdalny lub jej hasło logowania dla maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Jak zresetować hasło maszyny wirtualnej systemu Linux lub klucz SSH, napraw konfigurację SSH i Sprawdź spójność dysku przy użyciu rozszerzenia VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Jak generować nowe certyfikaty SSH?

Generowanie certyfikatów zostało wyjaśnione w artykule [Pobieranie identyfikatora URI sygnatury dostępu współdzielonego dla obrazu maszyny wirtualnej](./cpp-get-sas-uri.md) w dalszej części [Tworzenie zasobów technicznych dla oferty maszyny wirtualnej](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Jak skonfigurować wirtualną sieć prywatną (VPN) do pracy z maszynami wirtualnymi?

Jeśli używasz modelu wdrażania Azure Resource Manager, masz trzy typowe opcje konfigurowania sieci VPN:
- [Tworzenie bramy sieci VPN opartej na trasach za pomocą Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Tworzenie bramy sieci VPN opartej na trasie przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Tworzenie bramy sieci VPN opartej na trasach za pomocą interfejsu wiersza polecenia](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Co to są zasady pomocy technicznej firmy Microsoft dotyczące uruchamiania oprogramowania serwerowego firmy Microsoft na maszynach wirtualnych opartych na platformie Azure?

Te zasady pomocy technicznej są szczegółowo opisane w artykule [Obsługa oprogramowania serwerowego firmy Microsoft dla Microsoft Azure maszyn wirtualnych](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Czy z maszynami wirtualnymi są skojarzone unikatowe identyfikatory?

Tak, jeśli jest hostowana na platformie Azure.  Platforma Azure przypisuje unikatowy identyfikator o nazwie unikatowy identyfikator maszyny wirtualnej platformy Azure do każdego nowo utworzonego zasobu maszyny wirtualnej.  Aby uzyskać więcej informacji, zapoznaj się z wpisem na blogu [unikatowy identyfikator maszyny wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Ten identyfikator można również uzyskać programowo za pomocą [interfejsu API listy](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Jak zarządzać rozszerzeniem niestandardowego skryptu w ramach maszyny wirtualnej?

W poniższym artykule szczegółowo opisano sposób używania niestandardowego rozszerzenia skryptu przy użyciu modułu Azure PowerShell, szablonów Azure Resource Manager i szczegółów dotyczących rozwiązywania problemów w systemach Windows: [rozszerzenie niestandardowego skryptu dla systemu Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Czy obsługiwane są 32-bitowe aplikacje lub usługi w portalu Azure Marketplace?

Ogólnie rzecz biorąc — nie.  Obsługiwane systemy operacyjne i standardowe usługi dla maszyn wirtualnych platformy Azure to wszystkie 64-bitowe.  Jednak z punktu widzenia technicznej większość 64-bitowych systemów operacyjnych obsługuje uruchamianie 32-bitowych wersji aplikacji w celu zapewnienia zgodności z poprzednimi wersjami.  Jednak korzystanie z aplikacji 32-bitowych jako części rozwiązania maszyny wirtualnej nie jest obsługiwane i dlatego jest *zdecydowanie odradzane*.  Zamiast tego należy ponownie skompilować aplikację jako projekt 64-bitowy.

Aby uzyskać więcej informacji zobacz następujące artykuły:
- [Uruchamianie aplikacji 32-bitowych](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Obsługa 32-bitowych systemów operacyjnych na maszynach wirtualnych platformy Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Pomoc techniczna dotycząca oprogramowania serwerowego firmy Microsoft dla maszyn wirtualnych platformy Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Za każdym razem, gdy próbuję utworzyć obraz z moich wirtualnych dysków twardych, otrzymuję błąd `.VHD is already registered with image repository as the resource` w programie PowerShell. Nie utworzono żadnego obrazu wcześniej ani nie znaleziono żadnego obrazu o tej nazwie na platformie Azure. Jak mogę rozwiązać ten problem?

Ten problem występuje zwykle, gdy użytkownik zainicjowano maszynę wirtualną z dysku VHD, który ma blokadę.  Sprawdź, czy maszyna wirtualna nie została przypisana z tego wirtualnego dysku twardego, a następnie spróbuj ponownie wykonać operację.  Jeśli ten problem będzie się powtarzać, Otwórz bilet pomocy technicznej, zgodnie z opisem w temacie [obsługa Portal Cloud partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

