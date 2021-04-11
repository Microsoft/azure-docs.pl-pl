---
title: Przygotuj się do odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure za pomocą Azure Site Recovery
description: Dowiedz się, jak przygotować lokalne maszyny wirtualne funkcji Hyper-V na potrzeby odzyskiwania po awarii na platformie Azure przy użyciu Azure Site Recovery.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.custom: MVC
ms.openlocfilehash: 8704dd17f0386254f65d1c0e783c5f8786b8ae98
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580302"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Przygotowywanie lokalnych serwerów funkcji Hyper-V do odzyskiwania po awarii do platformy Azure

W tym artykule opisano sposób przygotowania lokalnej infrastruktury funkcji Hyper-V w celu skonfigurowania odzyskiwania po awarii Hyper-VMs na platformie Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).


Jest to drugi samouczek z serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych maszyn wirtualnych funkcji Hyper-V. W pierwszym samouczku [skonfigurujemy składniki platformy Azure, które](tutorial-prepare-azure.md) są związane z odzyskiwaniem po awarii funkcji Hyper-V.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zapoznaj się z wymaganiami funkcji Hyper-V i wymaganiami programu VMM, Jeśli hosty funkcji Hyper-V są zarządzane przez program System Center VMM.
> * Przygotuj program VMM, jeśli ma to zastosowanie.
> * Sprawdź dostęp do Internetu do lokalizacji platformy Azure.
> * Przygotuj maszyny wirtualne, aby można było uzyskać do nich dostęp po przejściu do trybu failover na platformie Azure.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułem w sekcji jak to zrobić w spisie treści Site Recovery.

## <a name="before-you-start"></a>Przed rozpoczęciem

Upewnij się, że przygotowano platformę Azure zgodnie z opisem w [pierwszym samouczku w tej serii](tutorial-prepare-azure.md).

## <a name="review-requirements-and-prerequisites"></a>Przegląd wymagań i wymagania wstępne

Upewnij się, że hosty i maszyny wirtualne funkcji Hyper-V spełniają wymagania.

1. [Sprawdź](hyper-v-azure-support-matrix.md#on-premises-servers) wymagania dotyczące serwera lokalnego.
2. [Sprawdź wymagania](hyper-v-azure-support-matrix.md#replicated-vms) dotyczące maszyn wirtualnych funkcji Hyper-V, które mają być replikowane na platformę Azure.
3. Sprawdź [Sieć](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)hosta funkcji Hyper-V; i obsługa [magazynu](hyper-v-azure-support-matrix.md#hyper-v-host-storage) hosta i gościa na lokalnych hostach funkcji Hyper-V.
4. Sprawdź obsługę [sieci](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [magazynu](hyper-v-azure-support-matrix.md#azure-storage) i [usług obliczeniowych](hyper-v-azure-support-matrix.md#azure-compute-features) platformy Azure po przejściu do trybu failover.
5. Lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać [wymagania dotyczące maszyn wirtualnych platformy Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Przygotuj program VMM (opcjonalnie)

Jeśli hosty funkcji Hyper-V są zarządzane przez program VMM, należy przygotować lokalny serwer programu VMM. 

- Upewnij się, że serwer VMM ma co najmniej jedną chmurę z co najmniej jedną grupą hostów. Na hoście funkcji Hyper-V, na którym są uruchomione maszyny wirtualne, powinny znajdować się w chmurze.
- Przygotuj serwer programu VMM do mapowania sieci.

### <a name="prepare-vmm-for-network-mapping"></a>Przygotowanie programu VMM do mapowania sieci

Jeśli używasz programu VMM, [mapowanie sieci](./hyper-v-vmm-network-mapping.md) jest mapowane między lokalnymi sieciami maszyn wirtualnych programu VMM i sieciami wirtualnymi platformy Azure. Mapowanie zapewnia, że maszyny wirtualne platformy Azure są podłączone do odpowiedniej sieci, gdy są tworzone po przejściu w tryb failover.

Przygotuj program VMM do mapowania sieci w następujący sposób:

1. Upewnij się, że masz [sieć logiczną programu VMM](/system-center/vmm/network-logical) skojarzoną z chmurą, w której znajdują się hosty funkcji Hyper-V.
2. Upewnij się, że [sieć maszyny wirtualnej](/system-center/vmm/network-virtual) jest połączona z siecią logiczną.
3. W programie VMM Podłącz maszyny wirtualne do sieci maszyn wirtualnych.

## <a name="verify-internet-access"></a>Weryfikowanie dostępu do Internetu

1. Na potrzeby tego samouczka najprostsza konfiguracja jest przeznaczona dla hostów funkcji Hyper-V i serwera programu VMM, aby mieć bezpośredni dostęp do Internetu bez użycia serwera proxy. 
2. Upewnij się, że hosty funkcji Hyper-V i serwer programu VMM, jeśli są odpowiednie, mogą uzyskać dostęp do wymaganych adresów URL poniżej.   
3. W przypadku kontrolowania dostępu według adresu IP upewnij się, że:
    - Reguły zapory oparte na adresach IP mogą łączyć się z [zakresami adresów IP centrów danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)i portem HTTPS (443).
    - Zezwalaj na zakresy adresów IP dla regionu platformy Azure Twojej subskrypcji.
    
### <a name="required-urls"></a>Wymagane adresy URL


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

W scenariuszu pracy awaryjnej możesz chcieć nawiązać połączenie z replikacją w sieci lokalnej.

Aby nawiązać połączenie z maszynami wirtualnymi z systemem Windows przy użyciu protokołu RDP po przejściu do trybu failover, Zezwalaj

1. Aby uzyskać dostęp przez Internet, włącz protokół RDP na lokalnej maszynie wirtualnej przed włączeniem trybu failover. Upewnij się, że dla profilu **publicznego** dodano reguły TCP i UDP oraz że protokół RDP jest dozwolony w aplikacjach dozwolonych przez **zaporę systemu Windows**  >   dla wszystkich profilów.
2. Aby uzyskać dostęp za pośrednictwem połączenia VPN typu lokacja-lokacja, włącz protokół RDP na maszynie lokalnej. Protokół RDP powinien być dozwolony w   ->  przypadku **aplikacji i funkcji dozwolonych** przez zaporę systemu Windows w przypadku sieci **z domeną i siecią prywatną** .
   Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135). Podczas wyzwalania trybu failover na maszynie wirtualnej nie powinno być żadnych oczekujących aktualizacji systemu Windows. Jeśli tak się stanie, nie będzie można zalogować się do maszyny wirtualnej do momentu ukończenia aktualizacji.
3. Na maszynie wirtualnej platformy Azure z systemem Windows po przejściu do trybu failover sprawdź **diagnostykę rozruchu**, aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli nie możesz się połączyć, upewnij się, że maszyna wirtualna jest uruchomiona, i przejrzyj te [porady dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Po przejściu w tryb failover możesz uzyskiwać dostęp do maszyn wirtualnych platformy Azure przy użyciu tego samego adresu IP, który jest replikowanym lokalnym maszyną wirtualną, lub innego adresu IP. [Dowiedz się więcej](concepts-on-premises-to-azure-networking.md) o konfigurowaniu adresowania IP na potrzeby pracy w trybie failover.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych](./hyper-v-azure-tutorial.md) 
>  funkcji Hyper-V [Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM](./hyper-v-vmm-azure-tutorial.md)
