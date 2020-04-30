---
title: Tworzenie wirtualnego dysku twardego zgodnego z platformą Azure dla portalu Azure Marketplace
description: W tym artykule wyjaśniono, jak utworzyć dysk VHD dla oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 642c6964aaad8d6e8750fca67efb11eb3feaf19d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147008"
---
# <a name="create-an-azure-compatible-vhd"></a>Tworzenie wirtualnego dysku twardego zgodnego z platformą Azure

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie zasobów technicznych maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) , aby zarządzać migrowanymi ofertami.

W tym artykule szczegółowo opisano kroki wymagane do utworzenia wirtualnego dysku twardego (VHD) dla oferty maszyny wirtualnej w portalu Azure Marketplace.  Zawiera również najlepsze rozwiązania dotyczące różnych aspektów, takich jak korzystanie z Remote Desktop Protocol (RDP), Wybieranie rozmiaru maszyny wirtualnej, instalowanie najnowszych aktualizacji systemu Windows i uogólnianie obrazu dysku VHD.  W poniższych sekcjach głównie koncentrują się na dyskach VHD opartych na systemie Windows; Aby uzyskać więcej informacji na temat tworzenia wirtualnych dysków twardych opartych na systemie Linux, zobacz [Linux w przypadku dystrybucji potwierdzonych przez platformę Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Zdecydowanie zalecamy przestrzeganie wskazówek przedstawionych w tym temacie, aby utworzyć maszynę wirtualną zawierającą wstępnie skonfigurowany, zatwierdzony system operacyjny.  Jeśli nie jest to zgodne z rozwiązaniem, można utworzyć i skonfigurować lokalną maszynę wirtualną przy użyciu zatwierdzonego systemu operacyjnego.  Następnie można skonfigurować i przygotować go do przekazania zgodnie z opisem w artykule [Przygotowywanie wirtualnego dysku twardego systemu Windows lub dysku VHDX do przekazania do platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Wybierz zatwierdzoną bazę
Wirtualny dysk twardy systemu operacyjnego na potrzeby obrazu maszyny wirtualnej musi opierać się na podstawie zatwierdzonej na platformie Azure obrazu podstawowego, który zawiera system Windows Server lub SQL Server.
Aby rozpocząć, Utwórz maszynę wirtualną na podstawie jednego z następujących obrazów znajdujących się w Microsoft Azure Portal:

-    Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 z dodatkiem SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-    [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, standard, Web)
-    [SQL Server 2012 z dodatkiem SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, standard, Web)

> [!TIP]
> Jeśli używasz bieżącego Azure Portal lub programu PowerShell, obrazy systemu Windows Server opublikowane 8 września 2014 i nowsze są zatwierdzone.

Alternatywnie platforma Azure oferuje zakres zatwierdzonych dystrybucji systemu Linux.  Aby zapoznać się z bieżącą listą, zobacz System [Linux dotyczący dystrybucji zatwierdzony przez platformę Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Tworzenie maszyny wirtualnej w Azure Portal 

W [Azure Portal](https://ms.portal.azure.com/)firmy Microsoft Utwórz podstawowy obraz, wykonując poniższe kroki.

1. Zaloguj się do portalu przy użyciu konto Microsoft dla subskrypcji platformy Azure, w której chcesz opublikować ofertę maszyny wirtualnej.
2. Utwórz nową grupę zasobów i podaj **nazwę grupy zasobów**, **subskrypcję**i **lokalizację grupy zasobów**.  Aby uzyskać więcej wskazówek, zobacz [Zarządzanie grupami zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Kliknij pozycję **maszyny wirtualne** na lewym pasku menu, aby wyświetlić stronę szczegóły maszyn wirtualnych. 
4. Na tej nowej stronie kliknij pozycję **+ Dodaj** , aby wyświetlić blok **obliczenia** .  Jeśli nie widzisz typu maszyny wirtualnej na ekranie wstępnym, możesz wyszukać nazwę podstawowej maszyny wirtualnej, na przykład:

    ![Blok obliczeń nowej maszyny wirtualnej](./media/publishvm_014.png)

5. Po wybraniu odpowiedniego obrazu wirtualnego podaj następujące wartości:
   * W bloku **podstawowe** wprowadź **nazwę** maszyny wirtualnej, od 1-15 znaków alfanumerycznych. (W tym przykładzie `DemoVm009`używamy).
   * Wprowadź **nazwę użytkownika** i silne **hasło**, które zostaną użyte do utworzenia konta lokalnego na maszynie wirtualnej.  (W `adminUser` tym miejscu).  Hasło musi mieć długość od 3 do 8-123 znaków i spełniać trzy z czterech następujących wymagań dotyczących złożoności: małe litery, wielkie litery, cyfry i znaki specjalne. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące nazwy użytkownika i hasła](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Wybierz utworzoną grupę zasobów (tutaj `DemoResourceGroup`).
   * Wybierz **lokalizację** centrum danych platformy Azure ( `West US`tutaj).
   * Kliknij przycisk **OK** , aby zapisać te wartości. 

6. Wybierz rozmiar maszyny wirtualnej do wdrożenia przy użyciu następujących zaleceń:
   * Jeśli planujesz opracowywanie wirtualnego dysku twardego, rozmiar nie ma znaczenia. Rozważ użycie jednej z mniejszych maszyn wirtualnych.
   * Jeśli planujesz opracowanie obrazu na platformie Azure, rozważ użycie jednego z zalecanych rozmiarów maszyn wirtualnych dla wybranego obrazu.
   * Aby uzyskać informacje o cenach, zapoznaj się z selektorem **zalecane warstwy cenowe** , który jest wyświetlany w portalu. Zostaną wyświetlone trzy zalecane rozmiary udostępniane przez wydawcę. (W tym przypadku Wydawca to Microsoft).

   ![Blok rozmiaru nowej maszyny wirtualnej](./media/publishvm_015.png)

7. W bloku **Ustawienia** ustaw opcję **Użyj dysku zarządzanego** na wartość **nie**.  Pozwala to na ręczne zarządzanie nowym dyskiem VHD. (W bloku **Ustawienia** można także zmienić inne zmiany opcji magazynu i sieci, na przykład wybierając opcję **Premium (SSD)** w obszarze **typ dysku**.)  Kliknij przycisk **OK** , aby kontynuować.

    ![Blok ustawień nowej maszyny wirtualnej](./media/publishvm_016.png)

8. Kliknij pozycję **Podsumowanie**, aby przejrzeć wybrane opcje. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **OK**.

    ![Blok podsumowania nowej maszyny wirtualnej](./media/publishvm_017.png)

Platforma Azure rozpocznie Inicjowanie obsługi określonej maszyny wirtualnej.  Postęp można śledzić, klikając kartę **Virtual Machines** po lewej stronie.  Po jego utworzeniu stan zmieni się na **uruchomiony**.  W tym momencie można [nawiązać połączenie z maszyną wirtualną](./cpp-connect-vm.md).


## <a name="next-steps"></a>Następne kroki

Jeśli wystąpił problem podczas tworzenia nowego wirtualnego dysku twardego opartego na platformie Azure, zobacz [typowe problemy podczas tworzenia dysku VHD](./cpp-common-vhd-creation-issues.md).  W przeciwnym razie należy [nawiązać połączenie z maszynami wirtualnymi](./cpp-connect-vm.md) utworzonymi na platformie Azure. 
