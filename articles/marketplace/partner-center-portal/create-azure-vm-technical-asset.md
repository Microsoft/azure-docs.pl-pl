---
title: Tworzenie zasobów technicznych maszyny wirtualnej platformy Azure
description: Dowiedz się, jak tworzyć i konfigurować zasoby techniczne dla oferty maszyny wirtualnej (VM) dla witryny Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/13/2020
ms.openlocfilehash: 532a2ce429899d3674a8f6d8308188e9b1f737b1
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407952"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Tworzenie zasobów technicznych maszyny wirtualnej platformy Azure

W tym artykule opisano sposób tworzenia i konfigurowania zasobów technicznych dla oferty maszyny wirtualnej (VM) dla witryny Azure Marketplace. Maszyna wirtualna zawiera dwa składniki: wirtualny dysk twardy (VHD) systemu operacyjnego i opcjonalne skojarzone dyski danych VHD:

* **Wirtualny dysk twardy systemu operacyjnego** — zawiera system operacyjny i rozwiązanie, które jest wdrażane wraz z ofertą. Proces przygotowywania wirtualnego dysku twardego różni się w zależności od tego, czy jest to maszyna wirtualna oparta na systemie Linux, Windows, czy niestandardowa.
* **Dyski VHD z danymi** — dedykowany magazyn trwały dla maszyny wirtualnej. Nie używaj wirtualnego dysku twardego systemu operacyjnego (na przykład dysku C:) do przechowywania informacji trwałych.

Obraz maszyny wirtualnej zawiera jeden dysk systemu operacyjnego i maksymalnie 16 dysków z danymi. Użyj jednego wirtualnego dysku twardego na dysk danych, nawet jeśli dysk jest pusty.

> [!NOTE]
> Niezależnie od używanego systemu operacyjnego, należy dodać tylko minimalną liczbę dysków z danymi wymaganą przez rozwiązanie. Klienci nie mogą usunąć dysków, które są częścią obrazu w czasie wdrażania, ale mogą zawsze dodawać dyski w trakcie wdrażania lub po nim.

> [!IMPORTANT]
> Każdy obraz maszyny wirtualnej w planie musi mieć taką samą liczbę dysków danych.

## <a name="fundamental-technical-knowledge"></a>Podstawowa wiedza techniczna

Projektowanie, kompilowanie i testowanie tych zasobów zabiera czas i wymaga znajomości wiedzy technicznej platformy Azure oraz technologii używanych do tworzenia oferty. Poza domeną rozwiązania Zespół inżynieryjny powinien znać następujące technologie firmy Microsoft:

* Podstawowe informacje o [usługach platformy Azure](https://azure.microsoft.com/services/)
* [Projektowanie i tworzenie architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
* Wiedza praktyczna [Virtual Machines platformy](https://azure.microsoft.com/services/virtual-machines/)Azure, [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage) i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking)
* Wiedza o pracy [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Wiedza o pracy w formacie [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Sugerowane narzędzia — opcjonalne

Rozważ użycie jednego z następujących środowisk skryptów, aby ułatwić zarządzanie maszynami wirtualnymi i dyskami VHD:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [Interfejs wiersza polecenia platformy Azure](https://code.visualstudio.com/)

Dodatkowo rozważ dodanie następujących narzędzi do środowiska deweloperskiego:

* [Eksplorator usługi Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Rozszerzenie: [narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Rozszerzenie: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Rozszerzenie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Zapoznaj się z dostępnymi narzędziami na stronie [usługi Azure narzędzia deweloperskie](https://azure.microsoft.com/product-categories/developer-tools/) i, jeśli używasz programu Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Tworzenie obrazu maszyny wirtualnej przy użyciu zatwierdzonej bazy

> [!NOTE]
> Aby utworzyć zasoby techniczne dotyczące maszyn wirtualnych przy użyciu obrazu utworzonego na własne miejsce, przejdź do obszaru [Tworzenie maszyny wirtualnej przy użyciu własnego obrazu](#create-a-vm-using-your-own-image).

Ta sekcja zawiera opis różnych aspektów korzystania z zatwierdzonego bazy, takich jak używanie Remote Desktop Protocol (RDP), Wybieranie rozmiaru maszyny wirtualnej, instalowanie najnowszych aktualizacji systemu Windows i uogólnianie obrazu wirtualnego dysku twardego.

Poniższe sekcje dotyczą głównie dysków VHD opartych na systemie Windows. Aby uzyskać więcej informacji na temat tworzenia wirtualnych dysków twardych opartych na systemie Linux, zobacz [Linux w przypadku dystrybucji potwierdzonych przez platformę Azure](../../virtual-machines/linux/endorsed-distros.md).

> [!WARNING]
> Postępuj zgodnie ze wskazówkami w tym temacie, aby utworzyć maszynę wirtualną zawierającą wstępnie skonfigurowany, zatwierdzony system operacyjny. Jeśli to nie jest zgodne z rozwiązaniem, można utworzyć i skonfigurować lokalną maszynę wirtualną przy użyciu zatwierdzonego systemu operacyjnego. Następnie można skonfigurować i przygotować go do przekazania zgodnie z opisem w artykule [Przygotowywanie wirtualnego dysku twardego systemu Windows lub dysku VHDX do przekazania do platformy Azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md).

### <a name="select-an-approved-base"></a>Wybierz zatwierdzoną bazę

Wybierz system operacyjny Windows lub Linux jako podstawę.

#### <a name="windows"></a>Windows

Wirtualny dysk twardy systemu operacyjnego na potrzeby obrazu maszyny wirtualnej z systemem Windows musi być oparty na podstawowym obrazie zaakceptowanym przez platformę Azure, który zawiera system Windows Server lub SQL Server. Aby rozpocząć, Utwórz maszynę wirtualną na podstawie jednego z następujących obrazów z Azure Portal:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2012 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2008 R2 z dodatkiem SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, standard, Web)
* [SQL Server 2012 z dodatkiem SP2](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, standard, Web)

> [!NOTE]
> Jeśli używasz bieżącego Azure Portal lub Azure PowerShell, zostaną zatwierdzone obrazy systemu Windows Server opublikowane 8 września 2014 i nowszych.

#### <a name="linux"></a>Linux

System Azure oferuje zakres zatwierdzonych dystrybucji systemu Linux. Aby zapoznać się z bieżącą listą, zobacz System [Linux dotyczący dystrybucji zatwierdzony przez platformę Azure](../../virtual-machines/linux/endorsed-distros.md).

### <a name="create-vm-in-the-azure-portal"></a>Tworzenie maszyny wirtualnej w Azure Portal

Wykonaj następujące kroki, aby utworzyć podstawowy obraz maszyny wirtualnej w [Azure Portal](https://ms.portal.azure.com/):

1. Zaloguj się do [Azure Portal](https://ms.portal.azure.com/) za pomocą konto Microsoft skojarzonej z subskrypcją platformy Azure, której chcesz użyć do opublikowania oferty maszyny wirtualnej.
2. Utwórz nową grupę zasobów i podaj **nazwę grupy zasobów**, **subskrypcję**i **lokalizację grupy zasobów**. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie zasobami](../../azure-resource-manager/resource-group-portal.md).
3. Wybierz pozycję **maszyny wirtualne** po lewej stronie, aby wyświetlić stronę szczegóły dotyczące maszyn wirtualnych.
4. Wybierz pozycję **+ Dodaj** , aby otworzyć **środowisko tworzenia maszyny wirtualnej**.
5. Wybierz obraz z listy rozwijanej lub kliknij pozycję **Przeglądaj wszystkie obrazy publiczne i prywatne,** aby wyszukać lub przejrzeć wszystkie dostępne obrazy maszyn wirtualnych.
6. Wybierz rozmiar maszyny wirtualnej do wdrożenia przy użyciu następujących zaleceń:
    * Jeśli planujesz opracowywanie wirtualnego dysku twardego, rozmiar nie ma znaczenia. Rozważ użycie jednej z mniejszych maszyn wirtualnych.
    * Jeśli planujesz opracowanie obrazu na platformie Azure, rozważ użycie jednego z zalecanych rozmiarów maszyn wirtualnych dla wybranego obrazu.

7. W sekcji **dyski** rozwiń sekcję **Zaawansowane** i dla opcji **Użyj dysków zarządzanych** ustaw wartość **nie**.
8. Podaj inne wymagane szczegóły, aby utworzyć maszynę wirtualną.
9. Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.

Platforma Azure rozpocznie Inicjowanie obsługi określonej maszyny wirtualnej. Postęp można śledzić, wybierając kartę **Virtual Machines** po lewej stronie. Po jego utworzeniu stan zmieni się na **uruchomiony**.

Jeśli wystąpią problemy podczas tworzenia nowego wirtualnego dysku twardego opartego na platformie Azure, zobacz [typowe problemy podczas tworzenia dysku VHD (FAQ)](common-issues-during-vhd-creation.md).

### <a name="connect-to-your-azure-vm"></a>Nawiązywanie połączenia z maszyną wirtualną platformy Azure

W tej sekcji wyjaśniono, jak nawiązać połączenie z maszyną wirtualną utworzoną na platformie Azure i zalogować się do niej. Po pomyślnym nawiązaniu połączenia można korzystać z maszyny wirtualnej tak, jakby była ona zalogowana lokalnie na serwerze hosta.

#### <a name="connect-to-a-windows-based-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows

Użyj klienta pulpitu zdalnego, aby nawiązać połączenie z maszyną wirtualną opartą na systemie Windows hostowaną na platformie Azure. Większość wersji systemu Windows natywnie zawiera obsługę protokołu RDP (Remote Desktop Protocol). W przypadku innych systemów operacyjnych można znaleźć więcej informacji o klientach w [pulpit zdalny klientach](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

W tym artykule szczegółowo przedstawiono sposób korzystania z wbudowanej obsługi protokołu RDP systemu Windows w celu nawiązania połączenia z maszyną wirtualną: [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](../../virtual-machines/windows/connect-logon.md).

> [!TIP]
> Podczas procesu mogą pojawić się ostrzeżenia dotyczące zabezpieczeń. Na przykład ostrzeżenia, takie jak "plik RDP pochodzi od nieznanego wydawcy" lub "nie można zweryfikować poświadczeń użytkownika". Można bezpiecznie zignorować te ostrzeżenia.

#### <a name="connect-to-a-linux-based-vm"></a>Nawiązywanie połączenia z maszyną wirtualną opartą na systemie Linux

Aby nawiązać połączenie z maszyną wirtualną z systemem Linux, musisz mieć klienta Secure Shell Protocol (SSH). W poniższych krokach [użyto bezpłatnego](https://www.ssh.com/ssh/putty/) terminala prywatnego.

1. Przejdź do witryny [Azure Portal](https://ms.portal.azure.com/).
2. Wyszukaj i wybierz pozycję **maszyny wirtualne**.
3. Wybierz maszynę wirtualną, z którą chcesz nawiązać połączenie.
4. Uruchom maszynę wirtualną, jeśli jeszcze nie działa.
5. Wybierz nazwę maszyny wirtualnej, aby otworzyć jej stronę **przeglądu** .
6. Zanotuj publiczny adres IP i nazwę DNS maszyny wirtualnej (jeśli te wartości nie są ustawione, musisz [utworzyć interfejs sieciowy](../../virtual-network/virtual-network-network-interface.md#create-a-network-interface))).
7. Otwórz aplikację.
8. W oknie dialogowym Konfiguracja konfiguracji wprowadź adres IP lub nazwę DNS maszyny wirtualnej.

    :::image type="content" source="media/avm-putty.png" alt-text="Ilustruje ustawienia terminalu. pola Nazwa hosta lub adres IP i port są wyróżnione.":::

9. Wybierz pozycję **Otwórz** , aby otworzyć wystawcy Terminal.
10. Po wyświetleniu monitu wprowadź nazwę konta i hasło do konta maszyny wirtualnej z systemem Linux.

Jeśli masz problemy z połączeniem, zapoznaj się z dokumentacją klienta SSH. Na przykład [rozdział 10: typowe komunikaty o błędach](https://www.ssh.com/ssh/putty/putty-manuals).

Aby uzyskać szczegółowe informacje, w tym o sposobie dodawania pulpitu do maszyny wirtualnej z systemem Linux z obsługą administracyjną, zobacz [Instalowanie i konfigurowanie pulpit zdalny do nawiązywania połączenia z maszyną wirtualną z systemem Linux na platformie Azure](../../virtual-machines/linux/use-remote-desktop.md).

## <a name="create-a-vm-using-your-own-image"></a>Tworzenie maszyny wirtualnej przy użyciu własnego obrazu

W tej sekcji opisano sposób tworzenia i wdrażania obrazu maszyny wirtualnej (VM) dostarczonej przez użytkownika. Można to zrobić przez udostępnienie obrazów dysków VHD systemu operacyjnego i danych z wirtualnego dysku twardego wdrożonego na platformie Azure.

> [!NOTE]
> Aby opcjonalnie użyć zatwierdzonego obrazu podstawowego, postępuj zgodnie z instrukcjami w temacie [Tworzenie obrazu maszyny wirtualnej przy użyciu zatwierdzonej bazy](#create-a-vm-image-using-an-approved-base).

1. Przekaż obrazy do konta usługi Azure Storage.
2. Wdróż obraz maszyny wirtualnej.
3. Przechwyć obraz maszyny wirtualnej.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Przekazywanie obrazów do konta usługi Azure Storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przekaż uogólniony wirtualny dysk twardy systemu operacyjnego i dyski danych do konta usługi Azure Storage.

### <a name="deploy-your-image"></a>Wdrażanie obrazu

Utwórz obraz przy użyciu Azure Portal lub Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Wdrażanie przy użyciu witryny Azure Portal

1. Na stronie głównej wybierz pozycję **Utwórz zasób**, Wyszukaj pozycję "wdrożenie szablonu" i wybierz pozycję **Utwórz**.
2. Wybierz opcję **Kompiluj własny szablon w edytorze**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Przedstawia stronę wdrożenia niestandardowego.":::

3. Wklej ten [szablon JSON](../partner-center-portal/azure-vm-image-certification.md) do edytora i wybierz pozycję **Zapisz**.
4. Podaj wartości parametrów dla wyświetlanych stron właściwości **wdrożenia niestandardowego** .

    | Parametr | Opis |
    | ------------ | ------------- |
    | Nazwa konta magazynu użytkownika | Zawartość z komórki 2 |
    | Nazwa kontenera magazynu użytkownika | Nazwa konta magazynu, w którym znajduje się uogólniony wirtualny dysk twardy |
    | Nazwa DNS dla publicznego adresu IP | Nazwa DNS publicznego adresu IP. Zdefiniuj nazwę DNS publicznego adresu IP w Azure Portal po wdrożeniu oferty. |
    | Nazwa użytkownika administratora | Nazwa użytkownika konta administratora dla nowej maszyny wirtualnej |
    | Hasło administratora | Hasło konta administratora dla nowej maszyny wirtualnej |
    | Typ systemu operacyjnego | System operacyjny maszyny wirtualnej: system Windows lub Linux |
    | Identyfikator subskrypcji | Identyfikator wybranej subskrypcji |
    | Location | Lokalizacja geograficzna wdrożenia |
    | Rozmiar maszyny wirtualnej | [Rozmiar maszyny wirtualnej platformy Azure](../../virtual-machines/windows/sizes.md), na przykład Standard_A2 |
    | Nazwa publicznego adresu IP | Nazwa publicznego adresu IP |
    | Nazwa maszyny wirtualnej | Nazwa nowej maszyny wirtualnej |
    | Nazwa Virtual Network | Nazwa sieci wirtualnej używanej przez MASZYNę wirtualną |
    | Nazwa karty sieciowej | Nazwa karty sieciowej z uruchomioną siecią wirtualną |
    | ADRES URL WIRTUALNEGO DYSKU TWARDEGO | Pełny adres URL dysku VHD systemu operacyjnego |
    |  |  |

5. Po podaniu tych wartości wybierz pozycję **Kup**.

Platforma Azure rozpocznie wdrażanie. Tworzy nową maszynę wirtualną z określonym niezarządzanym wirtualnym dyskiem twardym w określonej ścieżce konta magazynu. Postęp można śledzić w Azure Portal, wybierając pozycję **Virtual Machines** po lewej stronie portalu. Po utworzeniu maszyny wirtualnej stan zmieni się z Start na uruchomiony.

#### <a name="deploy-using-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Przechwytywanie obrazu maszyny wirtualnej

Wykonaj następujące instrukcje, które odnoszą się do Twojego podejścia:

* Azure PowerShell: [jak utworzyć niezarządzany obraz maszyny wirtualnej na podstawie maszyny wirtualnej platformy Azure](../../virtual-machines/windows/capture-image-resource.md)
* Interfejs wiersza polecenia platformy Azure: [jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego](../../virtual-machines/linux/capture-image.md)
* Interfejs API: [przechwytywanie Virtual Machines](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Skonfiguruj maszynę wirtualną

W tej sekcji opisano, jak zmienić rozmiar, zaktualizować i uogólnić maszynę wirtualną platformy Azure. Te kroki są niezbędne, aby przygotować maszynę wirtualną do wdrożenia w portalu Azure Marketplace.

### <a name="sizing-the-vhds"></a>Ustalanie wielkości dysków VHD

W przypadku wybrania jednej z maszyn wirtualnych wstępnie skonfigurowanych przy użyciu systemu operacyjnego (i opcjonalnie dodatkowych usług) masz już pobrany standardowy rozmiar maszyny wirtualnej platformy Azure. Zalecanym podejściem jest uruchomienie rozwiązania ze wstępnie skonfigurowanym systemem operacyjnym. Jeśli jednak instalujesz system operacyjny ręcznie, musisz zmienić rozmiar podstawowego wirtualnego dysku twardego w obrazie maszyny wirtualnej:

* W przypadku systemu Windows wirtualny dysk twardy systemu operacyjnego powinien być utworzony w formacie 127 – 128 GB stałego formatu dysku VHD.
* W przypadku systemu Linux ten wirtualny dysk twardy powinien zostać utworzony jako dysk VHD o stałym rozmiarze 30 – 50 GB.

Jeśli rozmiar fizyczny jest mniejszy niż 127 – 128 GB, wirtualny dysk twardy powinien być rozszerzalny (rozrzedzony/dynamiczny). Podstawowe obrazy systemu Windows i SQL Server, które zostały podane już spełniają te wymagania, dlatego nie zmieniają formatu ani rozmiaru dysku VHD.

Dyski danych mogą mieć rozmiar maksymalnie 1 TB. Przy wyborze rozmiaru należy pamiętać, że klienci nie mogą zmieniać rozmiarów wirtualnych dysków twardych w ramach obrazu w czasie wdrażania. Wirtualne dyski twarde z danymi powinny zostać utworzone jako dyski VHD o stałym formacie. Powinny również być rozwijane (rozrzedzony/dynamiczny). Dyski danych mogą początkowo być puste lub zawierać dane.

### <a name="install-the-most-current-updates"></a>Zainstaluj najnowsze aktualizacje

Podstawowe obrazy maszyn wirtualnych z systemem operacyjnym muszą zawierać najnowsze aktualizacje do daty ich opublikowania. Przed opublikowaniem utworzonego wirtualnego dysku twardego systemu operacyjnego należy zaktualizować system operacyjny i wszystkie zainstalowane usługi ze wszystkimi najnowszymi poprawkami zabezpieczeń i konserwacji.

W przypadku systemu Windows Server uruchom polecenie **Sprawdź dostępność aktualizacji** .

W przypadku dystrybucji systemu Linux aktualizacje są często pobierane i instalowane za pomocą narzędzia wiersza polecenia lub narzędzia graficznego. Na przykład Ubuntu Linux udostępnia polecenie [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) oraz narzędzie [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) do aktualizowania systemu operacyjnego.

### <a name="perform-additional-security-checks"></a>Wykonaj dodatkowe sprawdzenia zabezpieczeń

Zachowaj wysoki poziom zabezpieczeń obrazów rozwiązań w portalu Azure Marketplace. W poniższym artykule przedstawiono listę kontrolną konfiguracji i procedur zabezpieczeń, które pomagają: [zalecenia dotyczące zabezpieczeń obrazów w portalu Azure Marketplace](../../security/security-recommendations-azure-marketplace-images.md). Niektóre z tych zaleceń dotyczą obrazów opartych na systemie Linux, ale większość obrazów maszyn wirtualnych.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Wykonywanie konfiguracji niestandardowej i zaplanowanych zadań

Jeśli wymagana jest dodatkowa konfiguracja, użyj zaplanowanego zadania uruchamianego podczas uruchamiania, aby wprowadzić zmiany końcowe do maszyny wirtualnej po jej wdrożeniu. Należy również wziąć pod uwagę następujące zalecenia:

* Jeśli jest to zadanie uruchamiane po raz, zadanie powinno zostać usunięte po pomyślnym zakończeniu.
* Konfiguracje nie powinny polegać na dyskach innych niż C lub D, ponieważ tylko te dwa dyski zawsze są spełnione (dysk C jest dyskiem systemu operacyjnego, a dysk D jest tymczasowym dyskiem lokalnym).

Aby uzyskać więcej informacji na temat dostosowań systemu Linux, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="generalize-the-image"></a>Uogólnianie obrazu

Wszystkie obrazy w portalu Azure Marketplace muszą być wielokrotnego użytku w ogólny sposób. Aby to osiągnąć, musi być uogólniony wirtualny dysk twardy systemu operacyjnego, operacja, która usuwa wszystkie identyfikatory specyficzne dla wystąpienia i sterowniki oprogramowania z maszyny wirtualnej.

### <a name="windows"></a>Windows

Dyski systemu operacyjnego Windows są uogólnione za pomocą [narzędzia Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Jeśli później zaktualizujesz lub ponownie skonfigurujesz system operacyjny, musisz ponownie uruchomić program Sysprep.

> [!WARNING]
> Ponieważ aktualizacje mogą być uruchamiane automatycznie, po uruchomieniu programu Sysprep należy wyłączyć maszynę wirtualną do momentu wdrożenia. To zamknięcie spowoduje uniknięcie kolejnych aktualizacji w celu wprowadzenia zmian specyficznych dla wystąpienia do systemu operacyjnego lub zainstalowanych usług. Aby uzyskać więcej informacji na temat uruchamiania programu Sysprep, zobacz [procedura uogólniania wirtualnego dysku twardego](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)).

### <a name="linux"></a>Linux

Poniższy proces uogólni maszynę wirtualną z systemem Linux i ponownie wdraża ją jako oddzielną maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego](../../virtual-machines/linux/capture-image.md). Możesz zatrzymać, gdy dojdziesz do sekcji "Tworzenie maszyny wirtualnej z przechwyconego obrazu".

1. **Usuwanie agenta systemu Linux platformy Azure**

    1. Nawiązywanie połączenia z maszyną wirtualną z systemem Linux przy użyciu klienta SSH.
    2. W oknie SSH wprowadź następujące polecenie: `sudo waagent -deprovision+user` .
    3. Wpisz **Y** , aby kontynuować (parametr **-Force** można dodać do poprzedniego polecenia, aby uniknąć kroku potwierdzenia).
    d. Po zakończeniu wykonywania polecenia wpisz **Exit** , aby zamknąć klienta SSH.

2. **Zatrzymywanie maszyny wirtualnej**

    1. W Azure Portal wybierz grupę zasobów (RG), a następnie usuń przydział maszyny wirtualnej.
    2. Wirtualny dysk twardy jest teraz uogólniony i można utworzyć nową maszynę wirtualną przy użyciu tego wirtualnego dysku twardego.

## <a name="next-steps"></a>Następne kroki

Jeśli wystąpił problem podczas tworzenia nowego wirtualnego dysku twardego opartego na platformie Azure, zobacz [typowe problemy podczas tworzenia dysku VHD](common-issues-during-vhd-creation.md).

W przeciwnym razie:

* [Testowa maszyna wirtualna (VM) wdrożona z dysku VHD](azure-vm-image-certification.md) wyjaśnia, jak testować i przesyłać obrazy maszyn wirtualnych na potrzeby certyfikacji w portalu Azure Marketplace, w tym informacje o tym, gdzie uzyskać *Narzędzie do testowania certyfikacji dla certyfikowanych narzędzi platformy Azure* oraz jak korzystać z niego do certyfikowania obrazu maszyny wirtualnej.
