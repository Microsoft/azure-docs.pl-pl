---
title: Tworzenie zasobów technicznych maszyny wirtualnej platformy Azure
description: Dowiedz się, jak tworzyć i konfigurować zasoby techniczne dla oferty maszyny wirtualnej dla portalu Azure Marketplace.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 46c59e8b4e60fbe17887ea0fc375b6da758ebf50
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457403"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Tworzenie zasobów technicznych maszyny wirtualnej platformy Azure

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami twojej maszyny wirtualnej platformy Azure z portalu cloud partner do centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami w [twórz zasoby techniczne dla oferty maszyny wirtualnej dla portalu cloud partner w](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) celu zarządzania ofertami.

W tym artykule opisano sposób tworzenia i konfigurowania zasobów technicznych dla oferty maszyny wirtualnej dla portalu Azure Marketplace. Maszyna wirtualna zawiera dwa składniki: wirtualny dysk twardy systemu operacyjnego (VHD) i opcjonalne skojarzone dyski VHD dysków danych:

* **VHD systemu operacyjnego** — zawiera system operacyjny i rozwiązanie, które wdraża z ofertą. Proces przygotowywania dysku wirtualnego różni się w zależności od tego, czy jest to maszyna wirtualna oparta na systemie Linux, Windows czy niestandardowa.
* **Dyski wirtualne dysków danych** — dedykowane, trwałe przechowywanie maszyny Wirtualnej. Nie używaj vhd systemu operacyjnego (na przykład dysku C:) do przechowywania trwałych informacji.

Obraz maszyny Wirtualnej zawiera jeden dysk systemu operacyjnego i maksymalnie 16 dysków z danymi. Użyj jednego dysku VHD na dysk danych, nawet jeśli dysk jest pusty.

> [!NOTE]
> Niezależnie od używanego systemu operacyjnego dodaj tylko minimalną liczbę dysków danych potrzebnych do rozwiązania. Klienci nie mogą usuwać dysków, które są częścią obrazu w czasie wdrażania, ale zawsze mogą dodawać dyski podczas lub po wdrożeniu.

> [!IMPORTANT]
> Każdy obraz maszyny Wirtualnej w planie musi mieć taką samą liczbę dysków z danymi.

## <a name="fundamental-technical-knowledge"></a>Podstawowa wiedza techniczna

Projektowanie, tworzenie i testowanie tych zasobów wymaga czasu i wymaga wiedzy technicznej zarówno platformy Azure, jak i technologii używanych do tworzenia oferty. Oprócz domeny rozwiązania zespół inżynierów powinien posiadać wiedzę na temat następujących technologii firmy Microsoft:

* Podstawowa wiedza o [usługach platformy Azure](https://azure.microsoft.com/services/)
* Jak [projektować i projektować aplikacje platformy Azure](https://azure.microsoft.com/solutions/architecture/)
* Wiedza robocza na temat [maszyn wirtualnych platformy Azure,](https://azure.microsoft.com/services/virtual-machines/) [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage) i sieci [azure](https://azure.microsoft.com/services/?filter=networking)
* Wiedza robocza na temat [usługi Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Wiedza robocza [json](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Sugerowane narzędzia – opcjonalne

Rozważ użycie jednego z następujących środowisk skryptów ułatwiających zarządzanie maszynami wirtualnymi i wirtualnymi:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Interfejs wiersza polecenia platformy Azure](https://code.visualstudio.com/)

Ponadto należy rozważyć dodanie następujących narzędzi do środowiska programistycznego:

* [Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Rozszerzenie: [narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Rozszerzenie: [Upiększyć](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Rozszerzenie: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Przejrzyj dostępne narzędzia na stronie [Narzędzia deweloperskie platformy Azure,](https://azure.microsoft.com/product-categories/developer-tools/) a jeśli używasz programu Visual Studio, [portal Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Tworzenie obrazu maszyny Wirtualnej przy użyciu zatwierdzonej bazy

> [!NOTE]
> Aby utworzyć zasoby techniczne maszyny wirtualnej przy użyciu obrazu utworzonego na własnym terenie, przejdź do [tworzenia maszyny wirtualnej przy użyciu własnego obrazu](#create-a-vm-using-your-own-image).

W tej sekcji opisano różne aspekty korzystania z zatwierdzonej bazy, takie jak korzystanie z protokołu RDP (Remote Desktop Protocol), wybieranie rozmiaru maszyny Wirtualnej, instalowanie najnowszych aktualizacji systemu Windows i uogólnianie obrazu VHD.

W poniższych sekcjach skupiono się głównie na vedach opartych na systemie Windows. Aby uzyskać więcej informacji na temat tworzenia vhd opartych na systemie Linux, zobacz [Linux w dystrybucjach zatwierdzonych przez platformę Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

> [!WARNING]
> Postępuj zgodnie ze wskazówkami w tym temacie, aby użyć platformy Azure do utworzenia maszyny Wirtualnej zawierającej wstępnie skonfigurowany, zatwierdzony system operacyjny. Jeśli nie jest to zgodne z rozwiązaniem, można utworzyć i skonfigurować lokalną maszynę wirtualną przy użyciu zatwierdzonego systemu operacyjnego. Następnie można skonfigurować i przygotować go do przesłania zgodnie z opisem w [temacie Przygotowanie dysku VHD lub VHDX systemu Windows do przekazania na platformę Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base"></a>Wybierz zatwierdzoną bazę

Wybierz jako bazę system operacyjny Windows lub Linux.

#### <a name="windows"></a>Windows

Dysk VHD systemu operacyjnego dla obrazu maszyny Wirtualnej z systemem Windows musi być oparty na obrazie podstawowym zatwierdzonym przez platformę Azure, który zawiera system Windows Server lub SQL Server. Aby rozpocząć, należy utworzyć maszynę wirtualną z jednego z następujących obrazów z witryny Azure portal:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!NOTE]
> Jeśli używasz bieżącego portalu Azure lub usługi Azure PowerShell, obrazy systemu Windows Server opublikowane 8 września 2014 r. i nowsze zostaną zatwierdzone.

#### <a name="linux"></a>Linux

Platforma Azure oferuje szereg zatwierdzonych dystrybucji systemu Linux. Aby uzyskać aktualną listę, zobacz [Linux w sprawie dystrybucji zatwierdzonych przez platformę Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-in-the-azure-portal"></a>Tworzenie maszyny wirtualnej w witrynie Azure portal

Wykonaj następujące kroki, aby utworzyć podstawowy obraz maszyny Wirtualnej w [witrynie Azure portal:](https://ms.portal.azure.com/)

1. Zaloguj się do [witryny Azure portal](https://ms.portal.azure.com/) za pomocą konta Microsoft skojarzonego z subskrypcją platformy Azure, której chcesz użyć do opublikowania oferty maszyny Wirtualnej.
2. Utwórz nową grupę zasobów i podaj **nazwę grupy zasobów,** **subskrypcję**i **lokalizację grupy zasobów**. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie zasobami](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Wybierz **maszyny wirtualne** po lewej stronie, aby wyświetlić stronę szczegóły maszyn wirtualnych.
4. Wybierz **+ Dodaj,** aby otworzyć **środowisko Tworzenie maszyny wirtualnej**.
5. Wybierz obraz z listy rozwijanej lub kliknij przycisk **Przeglądaj wszystkie obrazy publiczne i prywatne,** aby wyszukać lub przeglądać wszystkie dostępne obrazy maszyn wirtualnych.
6. Wybierz rozmiar maszyny Wirtualnej do wdrożenia, korzystając z następujących zaleceń:
    * Jeśli planujesz rozwijać VHD lokalnie, rozmiar nie ma znaczenia. Należy rozważyć użycie jednej z mniejszych maszyn wirtualnych.
    * Jeśli planujesz opracować obraz na platformie Azure, należy rozważyć użycie jednego z zalecanych rozmiarów maszyn wirtualnych dla wybranego obrazu.

7. W sekcji **Dyski** rozwiń sekcję **Zaawansowane** i ustaw opcję **Użyj dysków zarządzanych** na **Nie**.
8. Podaj inne wymagane szczegóły do utworzenia maszyny Wirtualnej.
9. Wybierz **opcję Przejrzyj + utwórz,** aby przejrzeć swoje wybory. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**.

Platforma Azure rozpoczyna inicjowanie obsługi administracyjnej określonej maszyny wirtualnej. Jego postęp można śledzić, wybierając kartę **Maszyny wirtualne** po lewej stronie. Po jego utworzeniu stan zmieni się na **Uruchomiony**.

Jeśli napotkasz trudności z utworzeniem nowego dysku wirtualnego opartego na platformie Azure, zobacz [Typowe problemy podczas tworzenia dysków VHD (często zadawane pytania).](https://aka.ms/VHDcreationIssues)

### <a name="connect-to-your-azure-vm"></a>Łączenie się z maszyną wirtualną platformy Azure

W tej sekcji wyjaśniono, jak połączyć się z maszyną wirtualną utworzoną na platformie Azure i zalogować się do maszyny Wirtualnej. Po pomyślnym nawiązaniu połączenia można pracować z maszyną wirtualną tak, jakby użytkownik był zalogowany lokalnie na serwerze hosta.

#### <a name="connect-to-a-windows-based-vm"></a>Łączenie się z maszyną wirtualną z systemem Windows

Użyj klienta pulpitu zdalnego, aby połączyć się z maszyną wirtualną z systemem Windows hostowanym na platformie Azure. Większość wersji systemu Windows natywnie zawiera obsługę protokołu pulpitu zdalnego (RDP). W przypadku innych systemów operacyjnych można znaleźć więcej informacji o klientach na [klientach pulpitu zdalnego](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

W tym artykule opisano, jak korzystać z wbudowanej pomocy technicznej protokołu RDP systemu Windows do łączenia się z [maszyną wirtualną: Jak połączyć się i zalogować się na maszynie wirtualnej platformy Azure z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Podczas procesu mogą pojawić się ostrzeżenia dotyczące zabezpieczeń. Na przykład ostrzeżenia, takie jak "Plik rdp pochodzi od nieznanego wydawcy" lub "Nie można zweryfikować poświadczeń użytkownika". Można bezpiecznie zignorować te ostrzeżenia.

#### <a name="connect-to-a-linux-based-vm"></a>Łączenie się z maszyną wirtualną z systemem Linux

Aby połączyć się z maszyną wirtualną opartą na systemie Linux, potrzebujesz klienta protokołu bezpiecznej powłoki (SSH). Poniższe kroki korzystają z bezpłatnego terminala [PuTTY](https://www.ssh.com/ssh/putty/) SHH.

1. Przejdź do [witryny Azure portal](https://ms.portal.azure.com/).
2. Wyszukaj i wybierz **maszyny wirtualne**.
3. Wybierz maszynę wirtualną, z którą chcesz się połączyć.
4. Uruchom maszynę wirtualną, jeśli nie jest jeszcze uruchomiona.
5. Wybierz nazwę maszyny Wirtualnej, aby otworzyć jej stronę **Przegląd.**
6. Zwróć uwagę na publiczny adres IP i nazwę DNS maszyny Wirtualnej (jeśli te wartości nie są ustawione, należy [utworzyć interfejs sieciowy](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)).
7. Otwórz aplikację PuTTY.
8. W oknie dialogowym Konfiguracja PuTTY wprowadź adres IP lub nazwę DNS maszyny wirtualnej.

    :::image type="content" source="media/avm-putty.png" alt-text="Ilustruje ustawienia terminala PuTTY. wyróżniona nazwa hosta lub adres IP i pola Port.":::

9. Wybierz **Otwórz,** aby otworzyć terminal PuTTY.
10. Po wyświetleniu monitu wprowadź nazwę konta i hasło konta maszyny Wirtualnej systemu Linux.

Jeśli masz problemy z połączeniem, zapoznaj się z dokumentacją klienta SSH. Na przykład [rozdział 10: Typowe komunikaty o błędach](https://www.ssh.com/ssh/putty/putty-manuals).

Aby uzyskać szczegółowe informacje, w tym jak dodać pulpit do aprowizowanego urządzenia wirtualnego z systemem Linux, zobacz [Instalowanie i konfigurowanie pulpitu zdalnego w celu łączenia się z maszyną wirtualną z systemem Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop).

## <a name="create-a-vm-using-your-own-image"></a>Tworzenie maszyny wirtualnej przy użyciu własnego obrazu

W tej sekcji opisano sposób tworzenia i wdrażania obrazu maszyny wirtualnej dostarczonej przez użytkownika. Można to zrobić, zapewniając obrazy VHD systemu operacyjnego i dysku danych z wirtualnego dysku twardego (VHD) wdrożonego pod platformą Azure.

> [!NOTE]
> Aby opcjonalnie użyć zatwierdzonego obrazu podstawowego, postępuj zgodnie z instrukcjami w [aplikacji Utwórz obraz maszyny Wirtualnej przy użyciu zatwierdzonej bazy](#create-a-vm-image-using-an-approved-base).

1. Przekaż swoje obrazy na konto usługi Azure Storage.
2. Wdrażanie obrazu maszyny Wirtualnej.
3. Przechwytywanie obrazu maszyny Wirtualnej.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Przekazywanie obrazów do konta magazynu platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przekaż uogólniony system operacyjny VHD i dysk danych VHD do konta magazynu platformy Azure.

### <a name="deploy-your-image"></a>Wdrażanie obrazu

Utwórz obraz przy użyciu witryny Azure portal lub programu Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Wdrażanie przy użyciu witryny Azure Portal

1. Na stronie głównej wybierz pozycję **Utwórz zasób**, wyszukaj hasło "Wdrażanie szablonu" i wybierz pozycję **Utwórz**.
2. Wybierz **pozycję Zbuduj własny szablon w edytorze**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Ilustruje stronę wdrożenia niestandardowego.":::

3. Wklej ten [szablon JSON](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) do edytora i wybierz pozycję **Zapisz**.
4. Podaj wartości parametrów wyświetlanych stron właściwości **wdrożenia niestandardowego.**

    | Parametr | Opis |
    | ------------ | ------------- |
    | Nazwa konta magazynu użytkownika | Zawartość z komórki 2 |
    | Nazwa kontenera magazynu użytkownika | Nazwa konta magazynu, w którym znajduje się uogólniony dysk VHD |
    | Nazwa DNS publicznego adresu IP | Publiczna nazwa DNS IP. Zdefiniuj nazwę DNS publicznego adresu IP w witrynie Azure portal po wdrożeniu oferty. |
    | Nazwa użytkownika administratora | Nazwa użytkownika konta administratora dla nowej maszyny Wirtualnej |
    | Hasło administratora | Hasło konta administratora dla nowej maszyny Wirtualnej |
    | Typ systemu operacyjnego | System operacyjny VM: Windows lub Linux |
    | Identyfikator subskrypcji | Identyfikator wybranej subskrypcji |
    | Lokalizacja | Lokalizacja geograficzna wdrożenia |
    | Rozmiar maszyny wirtualnej | [Rozmiar maszyny Wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), na przykład Standard_A2 |
    | Nazwa publicznego adresu IP | Nazwa publicznego adresu IP |
    | Nazwa maszyny wirtualnej | Nazwa nowej maszyny Wirtualnej |
    | Nazwa sieci wirtualnej | Nazwa sieci wirtualnej używanej przez maszynę wirtualną |
    | Nazwa karty sieciowej | Nazwa karty interfejsu sieciowego z siecią wirtualną |
    | VHD URL | Pełny adres URL dysku systemu operacyjnego VHD |
    |  |  |

5. Po podasz te wartości wybierz pozycję **Kup**.

Platforma Azure rozpocznie wdrażanie. Tworzy nową maszynę wirtualną z określonym niezarządzanym wirtualnym dyskiem wirtualnym w określonej ścieżce konta magazynu. Postęp w portalu Azure można śledzić, wybierając **maszyny wirtualne** po lewej stronie portalu. Po utworzeniu maszyny Wirtualnej stan zmieni się z Rozpoczęcie na Uruchamianie.

#### <a name="deploy-using-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Przechwytywanie obrazu maszyny Wirtualnej

Skorzystaj z następujących instrukcji, które odpowiadają twojemu podejściu:

* Program Azure PowerShell: [jak utworzyć niezarządzany obraz maszyny Wirtualnej z maszyny Wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Narzędzie CLI platformy Azure: [jak utworzyć obraz maszyny wirtualnej lub dysku VHD](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Maszyny wirtualne — przechwytywanie](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Konfigurowanie maszyny wirtualnej

W tej sekcji opisano sposób rozmiaru, aktualizowania i uogólniania maszyny Wirtualnej platformy Azure. Te kroki są niezbędne do przygotowania maszyny Wirtualnej do wdrożenia w portalu Azure Marketplace.

### <a name="sizing-the-vhds"></a>Zmiana rozmiaru VHD

Jeśli wybrano jedną z maszyn wirtualnych wstępnie skonfigurowanych z systemem operacyjnym (i opcjonalnie dodatkowe usługi), masz już wybrany standardowy rozmiar maszyny Wirtualnej platformy Azure. Rozpoczęcie rozwiązania za pomocą wstępnie skonfigurowanego systemu operacyjnego jest zalecanym rozwiązaniem. Jeśli jednak system operacyjny jest instalowyny ręcznie, należy rozmiar podstawowego dysku VHD na obrazie maszyny Wirtualnej:

* W przypadku systemu Windows dysk VHD systemu operacyjnego powinien być tworzony jako dysk VHD o stałym formacie 127–128 GB.
* W przypadku systemu Linux ten dysk VHD powinien być tworzony jako dysk VHD o stałym formacie 30–50 GB.

Jeśli rozmiar fizyczny jest mniejszy niż 127–128 GB, dysk VHD powinien być rozszerzalny (rozrzedzony/dynamiczny). Podstawowe obrazy systemu Windows i programu SQL Server, które są już dostarczane spełniają te wymagania, więc nie zmieniaj formatu ani rozmiaru dysku VHD.

Dyski danych mogą mieć rozmiar nawet 1 TB. Decydując się na rozmiar, należy pamiętać, że klienci nie można zmienić rozmiar VHD w obrazie w momencie wdrożenia. Dyski VHD dysku danych powinny być tworzone jako dyski VHD o stałym formacie. Powinny być również rozszerzalne (rzadkie/dynamiczne). Dyski danych mogą początkowo być puste lub zawierać dane.

### <a name="install-the-most-current-updates"></a>Instalowanie najbardziej aktualnych aktualizacji

Podstawowe obrazy maszyn wirtualnych systemu operacyjnego muszą zawierać najnowsze aktualizacje do daty ich opublikowania. Przed opublikowaniem utworzonego systemu operacyjnego VHD upewnij się, że zaktualisz system operacyjny i wszystkie zainstalowane usługi za pomocą najnowszych poprawek zabezpieczeń i konserwacji.

W systemie Windows Server uruchom polecenie **Sprawdź aktualizacje.**

W przypadku dystrybucji systemu Linux aktualizacje są często pobierane i instalowane za pomocą narzędzia wiersza polecenia lub narzędzia graficznego. Na przykład Ubuntu Linux udostępnia polecenie [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) i narzędzie [Menedżera aktualizacji](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) do aktualizowania systemu operacyjnego.

### <a name="perform-additional-security-checks"></a>Wykonywanie dodatkowych kontroli bezpieczeństwa

Utrzymuj wysoki poziom zabezpieczeń obrazów rozwiązań w portalu Azure Marketplace. Poniższy artykuł zawiera listę kontrolną konfiguracji zabezpieczeń i procedur, które pomogą Ci: [Zalecenia dotyczące zabezpieczeń dla usług Azure Marketplace Images](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Niektóre z tych zaleceń są specyficzne dla obrazów opartych na systemie Linux, ale większość dotyczy dowolnego obrazu maszyny Wirtualnej.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Wykonywanie niestandardowych konfiguracji i zaplanowanych zadań

Jeśli potrzebna jest dodatkowa konfiguracja, użyj zaplanowanego zadania, które jest uruchamiane podczas uruchamiania, aby wprowadzić ostateczne zmiany w maszynie wirtualnej po jej wdrożeniu. Należy również wziąć pod uwagę następujące zalecenia:

* Jeśli jest to zadanie jednorazowe, zadanie powinno zostać usunięte po pomyślnym zakończeniu.
* Konfiguracje nie powinny polegać na dyskach innych niż C lub D, ponieważ tylko te dwa dyski są zawsze gwarantowane (dysk C jest dyskiem systemu operacyjnego, a dysk D jest tymczasowym dyskiem lokalnym).

Aby uzyskać więcej informacji na temat dostosowań systemu Linux, zobacz [Rozszerzenia i funkcje maszyn wirtualnych dla systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Uogólnianie obrazu

Wszystkie obrazy w portalu Azure Marketplace muszą być wielokrotnegoużyć w sposób ogólny. Aby to osiągnąć, system operacyjny VHD musi być uogólniony, operacja, która usuwa wszystkie identyfikatory specyficzne dla wystąpienia i sterowniki oprogramowania z maszyny Wirtualnej.

### <a name="windows"></a>Windows

Dyski systemu operacyjnego Windows są uogólnione za pomocą [narzędzia sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Jeśli następnie zaktualizujesz lub ponownie skonfigurujesz system operacyjny, należy ponownie uruchomić sysprep.

> [!WARNING]
> Ponieważ aktualizacje mogą działać automatycznie, po uruchomieniu sysprep, wyłączyć maszynę wirtualną, dopóki nie zostanie wdrożony. To zamknięcie pozwoli uniknąć kolejnych aktualizacji przed wprowadzaniem zmian specyficznych dla wystąpienia w systemie operacyjnym lub zainstalowanych usługach. Aby uzyskać więcej informacji na temat uruchamiania sysprep, zobacz [Kroki, aby uogólnić VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

Poniższy proces uogólnia maszynę wirtualną z systemem Linux i ponownie wdroży ją jako oddzielną maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz [Jak utworzyć obraz maszyny wirtualnej lub dysku VHD](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Możesz zatrzymać się po osiągnięciu sekcji "Utwórz maszynę wirtualną z przechwyconego obrazu".

1. **Usuwanie agenta systemu Azure Linux**

    1. Połącz się z maszyną wirtualną z systemem Linux za pomocą klienta SSH.
    2. W oknie SSH wprowadź następujące `sudo waagent -deprovision+user`polecenie: .
    3. Wpisz **Y,** aby kontynuować (można dodać parametr **-force** do poprzedniego polecenia, aby uniknąć kroku potwierdzenia).
    d. Po zakończeniu polecenia wpisz **Exit,** aby zamknąć klienta SSH.

2. **Zatrzymywanie maszyny wirtualnej**

    1. W witrynie Azure portal wybierz grupę zasobów (RG) i odznacz maszynę wirtualną.
    2. Dysk VHD jest teraz uogólniony i można utworzyć nową maszynę wirtualną za pomocą tego dysku wirtualnego.

## <a name="next-steps"></a>Następne kroki

Jeśli napotkano trudności z utworzeniem nowego dysku VHD opartego na platformie Azure, zobacz [Typowe problemy podczas tworzenia dysków VHD.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues)

W przeciwnym razie:

* [Certyfikuj obraz maszyny Wirtualnej,](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri) wyjaśniając, jak przetestować i przesłać obraz maszyny Wirtualnej do certyfikacji usługi Azure Marketplace, w tym gdzie uzyskać *narzędzie testowe certyfikacji dla platformy Azure* certified i jak go używać do certyfikacji obrazu maszyny wirtualnej.
