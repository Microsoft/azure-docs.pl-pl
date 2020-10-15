---
title: Utwórz zasoby techniczne dla oferty maszyny wirtualnej portalu Azure Marketplace
description: Dowiedz się, jak tworzyć i konfigurować zasoby techniczne dla oferty maszyny wirtualnej (VM) dla witryny Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: ec043c97da8056fee4af8f6ead84f4df05c0f304
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079024"
---
# <a name="create-technical-assets-for-an-azure-marketplace-virtual-machine-offer"></a>Utwórz zasoby techniczne dla oferty maszyny wirtualnej portalu Azure Marketplace

Podczas publikowania obrazów maszyn wirtualnych w portalu Azure Marketplace zespół platformy Azure sprawdza poprawność obrazu maszyny wirtualnej, aby zapewnić jego rozruch, bezpieczeństwo i zgodność z platformą Azure. Jeśli którykolwiek z testów wysokiej jakości zakończy się niepowodzeniem, opublikowanie zakończy się niepowodzeniem z komunikatem zawierającym błąd i możliwe [kroki naprawcze](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions).

W tym artykule opisano sposób tworzenia i konfigurowania zasobów technicznych dla oferty maszyny wirtualnej (VM) dla witryny Azure Marketplace. Maszyna wirtualna zawiera dwa składniki: wirtualny dysk twardy (VHD) systemu operacyjnego i opcjonalne skojarzone dyski danych VHD:

- **Wirtualny dysk twardy systemu operacyjnego**: zawiera system operacyjny i rozwiązanie, które jest wdrażane wraz z ofertą. Proces przygotowywania dysku VHD różni się w zależności od tego, czy jest to maszyna wirtualna oparta na systemie Linux, Windows, czy niestandardowa.

- **Dyski VHD z danymi**: dedykowany, trwały magazyn dla maszyny wirtualnej. Nie używaj wirtualnego dysku twardego systemu operacyjnego (na przykład dysku C:) do przechowywania informacji trwałych.

Obraz maszyny wirtualnej zawiera jeden dysk systemu operacyjnego i maksymalnie 16 dysków z danymi. Użyj jednego wirtualnego dysku twardego na dysk danych, nawet jeśli dysk jest pusty.

> [!NOTE]
> Niezależnie od używanego systemu operacyjnego, należy dodać tylko minimalną liczbę dysków z danymi wymaganą przez rozwiązanie. Klienci nie mogą usunąć dysków, które są częścią obrazu w czasie wdrażania, ale mogą zawsze dodawać dyski w trakcie wdrażania lub po nim.

> [!IMPORTANT]
> Każdy obraz maszyny wirtualnej w planie musi mieć taką samą liczbę dysków danych.

## <a name="fundamental-technical-knowledge"></a>Podstawowa wiedza techniczna

Projektowanie, kompilowanie i testowanie tych zasobów zabiera czas i wymaga znajomości wiedzy technicznej platformy Azure oraz technologii używanych do tworzenia oferty. Poza domeną rozwiązania Zespół inżynieryjny powinien znać następujące technologie firmy Microsoft:

- Podstawowe informacje o [usługach platformy Azure](https://azure.microsoft.com/services/)
- [Projektowanie i tworzenie architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
- Wiedza praktyczna [Virtual Machines platformy](https://azure.microsoft.com/services/virtual-machines/)Azure, [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage)i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking)
- Wiedza o pracy [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Wiedza o pracy w formacie [JSON](https://www.json.org/)

### <a name="optional-suggested-tools"></a>Opcjonalne sugerowane narzędzia

Rozważ użycie jednego z następujących środowisk skryptów, aby ułatwić zarządzanie maszynami wirtualnymi i dyskami VHD:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Interfejs wiersza polecenia platformy Azure](https://code.visualstudio.com/)

Dodatkowo rozważ dodanie następujących narzędzi do środowiska deweloperskiego:

- [Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>Tworzenie obrazu maszyny wirtualnej przy użyciu zatwierdzonej bazy

Aby utworzyć zasoby techniczne maszyny wirtualnej przy użyciu obrazu utworzonego na podstawie własnego miejsca, zobacz [Tworzenie obrazu maszyny wirtualnej przy użyciu zatwierdzonej podstawy](#create-a-vm-image-using-an-approved-base) poniżej.

Ta sekcja zawiera opis różnych aspektów korzystania z zatwierdzonego bazy, takich jak używanie Remote Desktop Protocol (RDP), Wybieranie rozmiaru maszyny wirtualnej, instalowanie najnowszych aktualizacji systemu Windows i uogólnianie obrazu wirtualnego dysku twardego.

Postępuj zgodnie ze wskazówkami w tym artykule, aby utworzyć maszynę wirtualną zawierającą wstępnie skonfigurowany, potwierdzony system operacyjny. Jeśli to nie jest zgodne z rozwiązaniem, można utworzyć i skonfigurować lokalną maszynę wirtualną przy użyciu zatwierdzonego systemu operacyjnego. Następnie można skonfigurować i przygotować go do przekazania zgodnie z opisem w artykule [Przygotowywanie wirtualnego dysku twardego systemu Windows lub dysku VHDX do przekazania do platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base-image"></a>Wybieranie zatwierdzonego obrazu podstawowego

Wybierz system operacyjny Windows lub Linux jako podstawę.

Wirtualny dysk twardy systemu operacyjnego na potrzeby obrazu maszyny wirtualnej musi opierać się na podstawie zatwierdzonej na platformie Azure obrazu podstawowego, który zawiera system Windows Server lub SQL Server.

Gdy wyślesz żądanie ponownego opublikowania obrazu przy użyciu aktualizacji, przypadek testowy weryfikacji liczby części może zakończyć się niepowodzeniem. W takim przypadku obraz nie zostanie zatwierdzony.

Ten błąd wystąpi, gdy użyto obrazu podstawowego, który należy do innego wydawcy i Zaktualizowano obraz. W takim przypadku nie będzie można opublikować Twojego obrazu.

Aby rozwiązać ten problem, Pobierz najnowszy obraz z witryny Azure Marketplace i wprowadź zmiany w tym obrazie. Zapoznaj się z następującymi tematami, aby wyświetlić zatwierdzone obrazy podstawowe, w których można wyszukać obraz:

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 R2 datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
- SQL Server 2019 ([Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview))
- SQL Server 2014 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))
- SQL Server 2012 z dodatkiem SP2 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))

#### <a name="linux"></a>Linux

System Azure oferuje zakres zatwierdzonych dystrybucji systemu Linux. Aby zapoznać się z bieżącą listą, zobacz System [Linux dotyczący dystrybucji zatwierdzony przez platformę Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-on-the-azure-portal"></a>Tworzenie maszyny wirtualnej na Azure Portal

Wykonaj następujące kroki, aby utworzyć podstawowy obraz maszyny wirtualnej na [Azure Portal](https://ms.portal.azure.com/):

1. Zaloguj się do [Azure Portal](https://ms.portal.azure.com/) za pomocą konto Microsoft skojarzonej z subskrypcją platformy Azure, której chcesz użyć do opublikowania oferty maszyny wirtualnej.
2. Utwórz nową grupę zasobów i podaj **nazwę grupy zasobów**, **subskrypcję**i **lokalizację grupy zasobów**. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie zasobami](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="Pokazuje początek tworzenia grupy zasobów.":::

3. Wybierz pozycję **maszyny wirtualne** w lewym okienku nawigacji, aby wyświetlić stronę szczegóły maszyn wirtualnych.
4. Wybierz pozycję **+ Dodaj** , aby otworzyć **środowisko tworzenia maszyny wirtualnej**.
5. Wybierz obraz z listy rozwijanej lub wybierz pozycję **Przeglądaj wszystkie obrazy publiczne i prywatne,** aby przeszukać lub przejrzeć wszystkie dostępne obrazy maszyn wirtualnych. Przykład:

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="Pokazuje początek tworzenia grupy zasobów.":::

6. Wybierz rozmiar maszyny wirtualnej do wdrożenia przy użyciu następujących zaleceń:
    1. Jeśli planujesz opracowywanie wirtualnego dysku twardego, rozmiar nie ma znaczenia. Rozważ użycie jednej z mniejszych maszyn wirtualnych.
    2. Jeśli planujesz opracowanie obrazu na platformie Azure, rozważ użycie jednego z zalecanych rozmiarów maszyn wirtualnych dla wybranego obrazu.

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="Pokazuje początek tworzenia grupy zasobów.":::

7. W sekcji **dyski** rozwiń sekcję **Zaawansowane** i dla opcji **Użyj dysków zarządzanych** ustaw wartość **nie**.

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="Pokazuje początek tworzenia grupy zasobów.":::

8. Podaj inne wymagane szczegóły, aby utworzyć maszynę wirtualną.
9. Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

Platforma Azure rozpocznie Inicjowanie obsługi określonej maszyny wirtualnej. Postęp można śledzić, wybierając kartę **Virtual Machines** po lewej stronie. Po jego utworzeniu stan zmieni się na **uruchomiony**.

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>Tworzenie maszyny wirtualnej generacji 2 na Azure Portal

Utwórz maszynę wirtualną generacji 2 (Gen2) w Azure Portal.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób**.
3. Wybierz pozycję **Zobacz wszystko** w witrynie Azure Marketplace po lewej stronie.
4. Wybierz obraz, który obsługuje Gen2.
5. Wybierz pozycję **Utwórz**.
6. Na karcie **Zaawansowane** w sekcji **generacja maszyny wirtualnej** wybierz opcję **generacji 2** .
7. Na karcie **podstawowe** w obszarze **szczegóły wystąpienia**przejdź do pozycji **rozmiar** i Otwórz blok **Wybieranie rozmiaru maszyny wirtualnej** .
8. Wybierz zalecany rozmiar [obsługiwanej maszyny wirtualnej i rozmiaru generacji 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) .
9. Przejdź przez [przepływ tworzenia Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) , aby zakończyć tworzenie maszyny wirtualnej.

    :::image type="content" source="media/vm/vm-generation.png" alt-text="Pokazuje początek tworzenia grupy zasobów.":::

## <a name="connect-to-your-azure-vm"></a>Nawiązywanie połączenia z maszyną wirtualną platformy Azure

W tej sekcji wyjaśniono, jak nawiązać połączenie i zalogować się do maszyny wirtualnej utworzonej na platformie Azure. Po pomyślnym nawiązaniu połączenia można korzystać z maszyny wirtualnej tak, jakby była ona zalogowana lokalnie na serwerze hosta.

### <a name="connect-to-a-windows-based-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows

Użyj klienta pulpitu zdalnego, aby nawiązać połączenie z maszyną wirtualną opartą na systemie Windows hostowaną na platformie Azure. Większość wersji systemu Windows natywnie zawiera obsługę protokołu RDP (Remote Desktop Protocol). W przypadku innych systemów operacyjnych można znaleźć więcej informacji o klientach w [pulpit zdalny klientach](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

W tym artykule szczegółowo przedstawiono sposób korzystania z wbudowanej obsługi protokołu RDP systemu Windows w celu nawiązania połączenia z maszyną wirtualną: [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Podczas procesu mogą pojawić się ostrzeżenia dotyczące zabezpieczeń. Na przykład ostrzeżenia, takie jak "plik RDP pochodzi od nieznanego wydawcy" lub "nie można zweryfikować poświadczeń użytkownika". Można bezpiecznie zignorować te ostrzeżenia.

### <a name="connect-to-a-linux-based-vm"></a>Nawiązywanie połączenia z maszyną wirtualną opartą na systemie Linux

Aby nawiązać połączenie z maszyną wirtualną z systemem Linux, musisz mieć klienta Secure Shell Protocol (SSH). W poniższych krokach [użyto bezpłatnego](https://www.ssh.com/ssh/putty/) wykorzystanego terminalu SSH.

1. Przejdź do witryny [Azure Portal](https://ms.portal.azure.com/).
2. Wyszukaj i wybierz pozycję Maszyny wirtualne.
3. Wybierz maszynę wirtualną, z którą chcesz nawiązać połączenie.
4. Uruchom maszynę wirtualną, jeśli jeszcze nie działa.
5. Wybierz nazwę maszyny wirtualnej, aby otworzyć jej stronę przeglądu.
6. Zanotuj publiczny adres IP i nazwę DNS maszyny wirtualnej (jeśli te wartości nie są ustawione, musisz [utworzyć interfejs sieciowy](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)).
7. Otwórz aplikację.
8. W oknie dialogowym Konfiguracja konfiguracji wprowadź adres IP lub nazwę DNS maszyny wirtualnej.

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="Pokazuje początek tworzenia grupy zasobów.":::

9. Wybierz pozycję **Otwórz** , aby otworzyć wystawcy Terminal.
10. Po wyświetleniu monitu wprowadź nazwę konta i hasło do konta maszyny wirtualnej z systemem Linux.

## <a name="configure-the-virtual-machine"></a>Skonfiguruj maszynę wirtualną

W tej sekcji opisano, jak zmienić rozmiar, zaktualizować i uogólnić maszynę wirtualną platformy Azure. Te kroki są niezbędne, aby przygotować maszynę wirtualną do wdrożenia w portalu Azure Marketplace.

### <a name="sizing-the-vhds"></a>Ustalanie wielkości dysków VHD

Poniższe reguły dotyczą ograniczeń rozmiaru dysku systemu operacyjnego. Po przesłaniu żądania upewnij się, że rozmiar dysku systemu operacyjnego jest w ramach ograniczenia dla systemu Linux lub Windows.

| System operacyjny | Zalecany rozmiar wirtualnego dysku twardego |
| --- | --- |
| Linux | od 30 do 1023 GB |
| Windows | od 30 do 250 GB |

Ponieważ maszyny wirtualne umożliwiają dostęp do podstawowego systemu operacyjnego, upewnij się, że rozmiar dysku VHD jest wystarczająco duży dla dysku VHD. Ponieważ dyski nie są rozwijane bez przestojów, Użyj rozmiaru dysku z zakresu od 30 do 50 &nbsp; GB.

| Rozmiar dysku VHD | Rzeczywisty rozmiar zajęty | Rozwiązanie |
| --- | --- | --- |
| >500 TB | nie dotyczy | Skontaktuj się z zespołem pomocy technicznej w celu zatwierdzenia wyjątku. |
| 250-500 TB | >200 GB różni się od rozmiaru obiektu BLOB | Skontaktuj się z zespołem pomocy technicznej w celu zatwierdzenia wyjątku. |

### <a name="install-the-most-current-updates"></a>Zainstaluj najnowsze aktualizacje

Podstawowe obrazy maszyn wirtualnych z systemem operacyjnym muszą zawierać najnowsze aktualizacje do daty ich opublikowania. Przed opublikowaniem utworzonego wirtualnego dysku twardego systemu operacyjnego należy zaktualizować system operacyjny i wszystkie zainstalowane usługi ze wszystkimi najnowszymi poprawkami zabezpieczeń i konserwacji.

- W przypadku systemu Windows Server uruchom polecenie Sprawdź dostępność aktualizacji.
- W przypadku dystrybucji systemu Linux aktualizacje są często pobierane i instalowane za pomocą narzędzia wiersza polecenia lub narzędzia graficznego. Na przykład Ubuntu Linux udostępnia polecenie [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) oraz narzędzie [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) do aktualizowania systemu operacyjnego.

#### <a name="perform-additional-security-checks"></a>Wykonaj dodatkowe sprawdzenia zabezpieczeń

Zachowaj wysoki poziom zabezpieczeń obrazów rozwiązań w portalu Azure Marketplace. Aby zapoznać się z listą kontrolną konfiguracji i procedur zabezpieczeń, zapoznaj się z tematem [zalecenia dotyczące zabezpieczeń obrazów portalu Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Niektóre z tych zaleceń dotyczą obrazów opartych na systemie Linux, ale większość obrazów maszyn wirtualnych.

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Wykonywanie konfiguracji niestandardowej i zaplanowanych zadań

Jeśli potrzebujesz dodatkowej konfiguracji, użyj zaplanowanego zadania uruchamianego podczas uruchamiania, aby wprowadzić ostateczne zmiany do maszyny wirtualnej po jej wdrożeniu. Rozważ również następujące elementy:

- Jeśli jest to zadanie uruchamiane po raz, zadanie powinno zostać usunięte po pomyślnym zakończeniu.
- Konfiguracje nie powinny polegać na dyskach innych niż C lub D, ponieważ tylko te dwa dyski zawsze są spełnione (dysk C jest dyskiem systemu operacyjnego, a dysk D jest tymczasowym dyskiem lokalnym).

Aby uzyskać więcej informacji na temat dostosowań systemu Linux, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Uogólnianie obrazu

Wszystkie obrazy w portalu Azure Marketplace muszą być wielokrotnego użytku w ogólny sposób. Aby to osiągnąć, musi być uogólniony wirtualny dysk twardy systemu operacyjnego, operacja, która usuwa wszystkie identyfikatory specyficzne dla wystąpienia i sterowniki oprogramowania z maszyny wirtualnej.

### <a name="for-windows"></a>W przypadku systemu Windows

Dyski systemu operacyjnego Windows są uogólnione za pomocą narzędzia [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Jeśli później zaktualizujesz lub ponownie skonfigurujesz system operacyjny, należy ponownie uruchomić program Sysprep.

> [!WARNING]
> Po uruchomieniu programu Sysprep należy wyłączyć maszynę wirtualną, dopóki nie zostanie wdrożona, ponieważ aktualizacje mogą być uruchamiane automatycznie. To zamknięcie spowoduje uniknięcie kolejnych aktualizacji w celu wprowadzenia zmian specyficznych dla wystąpienia do systemu operacyjnego lub zainstalowanych usług. Aby uzyskać więcej informacji na temat uruchamiania programu Sysprep, zobacz [procedura uogólniania dysku VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>W przypadku systemu Linux

Poniższy proces uogólni maszynę wirtualną z systemem Linux i ponownie wdraża ją jako oddzielną maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Możesz zatrzymać, gdy dojdziesz do sekcji o nazwie "Tworzenie maszyny wirtualnej z przechwyconego obrazu".

1. Usuwanie agenta systemu Linux platformy Azure

    1. Nawiązywanie połączenia z maszyną wirtualną z systemem Linux przy użyciu klienta SSH
    2. W oknie SSH wprowadź następujące polecenie: `sudo waagent –deprovision+user` .
    3. Wpisz Y, aby kontynuować (parametr-Force można dodać do poprzedniego polecenia, aby uniknąć kroku potwierdzenia).
    4. Po zakończeniu wykonywania polecenia wpisz exit, aby zamknąć klienta SSH.

2. Zatrzymywanie maszyny wirtualnej

    1. W Azure Portal wybierz grupę zasobów (RG), a następnie usuń przydział maszyny wirtualnej.
    2. Wirtualny dysk twardy jest teraz uogólniony i można utworzyć nową maszynę wirtualną przy użyciu tego wirtualnego dysku twardego.

## <a name="next-steps"></a>Następne kroki

- Jeśli wystąpił problem podczas tworzenia nowego wirtualnego dysku twardego opartego na platformie Azure, zobacz [typowe problemy podczas tworzenia dysku VHD](common-issues-during-vhd-creation.md).
- W przeciwnym razie [testowa maszyna wirtualna (VM) wdrożona z dysku VHD](azure-vm-image-certification.md) wyjaśnia, jak testować i przesyłać obrazy maszyn wirtualnych na potrzeby certyfikacji w portalu Azure Marketplace, w tym informacje o tym, gdzie uzyskać narzędzie do testowania certyfikacji dla certyfikowanych narzędzi platformy Azure oraz jak korzystać z niego w celu certyfikowania obrazu maszyny wirtualnej.
