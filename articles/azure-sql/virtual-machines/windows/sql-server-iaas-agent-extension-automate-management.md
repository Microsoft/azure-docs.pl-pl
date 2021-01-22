---
title: Co to jest rozszerzenie agenta SQL Server IaaS?
description: W tym artykule opisano, jak rozszerzenie Agent SQL Server IaaS ułatwia Automatyzowanie zadań administracyjnych określonych przez SQL Server na maszynach wirtualnych platformy Azure. Obejmują one takie funkcje jak automatyczne tworzenie kopii zapasowych, automatyczne stosowanie poprawek, integracja Azure Key Vault, Zarządzanie licencjonowaniem, Konfiguracja magazynu oraz centralne zarządzanie wszystkimi wystąpieniami SQL Server maszyn wirtualnych.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 481a4ff21c361e4cf82a21d9e98357a4c8b7b1b4
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663676"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>Automatyzowanie zarządzania przy użyciu rozszerzenia agenta SQL Server IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server rozszerzenie IaaS Agent (SqlIaasExtension) działa na SQL Server na platformie Azure Virtual Machines do automatyzowania zadań zarządzania i administrowania. 

Ten artykuł zawiera Omówienie rozszerzenia. Aby zainstalować rozszerzenie SQL Server IaaS do SQL Server na maszynach wirtualnych platformy Azure, zapoznaj się z artykułami dotyczącymi [automatycznej instalacji](sql-agent-extension-automatic-registration-all-vms.md), [pojedynczych maszyn wirtualnych](sql-agent-extension-manually-register-single-vm.md)lub [maszyn wirtualnych](sql-agent-extension-manually-register-vms-bulk.md). 

## <a name="overview"></a>Omówienie

Rozszerzenie Agent SQL Server IaaS umożliwia integrację z Azure Portal i w zależności od trybu zarządzania powoduje odblokowanie wielu zalet funkcji dla SQL Server na maszynach wirtualnych platformy Azure: 

- **Zalety funkcji**: rozszerzenie odblokowuje szereg korzyści z funkcji automatyzacji, takich jak Zarządzanie portalem, elastyczność licencji, automatyczne tworzenie kopii zapasowych, automatyczne stosowanie poprawek i nie tylko. Szczegóły [można znaleźć](#feature-benefits) w dalszej części tego artykułu. 

- **Zgodność**: rozszerzenie oferuje uproszczoną metodę spełnienia wymagania w celu powiadomienia firmy Microsoft, że korzyść użycia hybrydowego platformy Azure została włączona zgodnie z warunkami określonymi w temacie. Ten proces wyklucza konieczność zarządzania formularzami rejestracji licencjonowania dla każdego zasobu.  

- **Bezpłatnie**: rozszerzenie we wszystkich trzech trybach zarządzania jest całkowicie bezpłatne. Brak dodatkowych kosztów skojarzonych z rozszerzeniem lub zmianami trybów zarządzania. 

- **Uproszczone zarządzanie licencjami**: rozszerzenie upraszcza zarządzanie licencjami SQL Server i umożliwia szybkie identyfikowanie SQL Server maszyn wirtualnych z korzyść użycia hybrydowego platformy Azure włączony przy użyciu [Azure Portal](manage-sql-vm-portal.md), programu PowerShell lub interfejsu wiersza polecenia platformy Azure: 

   # <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```



   ---


> [!IMPORTANT]
> Rozszerzenie SQL IaaS Agent zbiera dane w celu udzielenia klientom opcjonalnych korzyści w przypadku korzystania z SQL Server w ramach Virtual Machines platformy Azure. Firma Microsoft nie będzie używać tych danych do inspekcji licencjonowania bez wcześniejszej zgody klienta. Aby uzyskać więcej informacji, zobacz [dodatek SQL Server privacy](/sql/sql-server/sql-server-privacy#non-personal-data) .


## <a name="feature-benefits"></a>Zalety funkcji 

Rozszerzenie agenta SQL Server IaaS odblokowuje szereg korzyści z używania funkcji do zarządzania SQL Server maszyną wirtualną. 

Poniższa tabela zawiera szczegółowe informacje na temat tych korzyści: 


| Cechy | Opis |
| --- | --- |
| **Portal zarządzania** | Umożliwia odblokowanie [zarządzania w portalu](manage-sql-vm-portal.md), dzięki czemu można wyświetlić wszystkie SQL Server maszyny wirtualne w jednym miejscu, co pozwoli na włączenie i wyłączenie funkcji specyficznych dla bazy danych SQL bezpośrednio z poziomu portalu. <br/> Tryb zarządzania: uproszczony & pełny|  
| **Automatyczna kopia zapasowa** |Automatyzuje Planowanie kopii zapasowych dla wszystkich baz danych dla wystąpienia domyślnego lub [poprawnie zainstalowane](frequently-asked-questions-faq.md#administration) nazwane wystąpienie SQL Server na maszynie wirtualnej. Aby uzyskać więcej informacji, zobacz [zautomatyzowane tworzenie kopii zapasowych SQL Server w usłudze Azure Virtual Machines (Menedżer zasobów)](automated-backup-sql-2014.md). <br/> Tryb zarządzania: pełny|
| **Automatyczne stosowanie poprawek** |Konfiguruje okno obsługi, w którym mogą być wykonywane ważne aktualizacje zabezpieczeń systemu Windows i SQL Server na maszynę wirtualną, dzięki czemu można uniknąć aktualizacji w godzinach szczytu dla obciążenia. Aby uzyskać więcej informacji, zobacz [zautomatyzowane stosowanie poprawek dla SQL Server w usłudze Azure Virtual Machines (Menedżer zasobów)](automated-patching.md). <br/> Tryb zarządzania: pełny|
| **Integracja Azure Key Vault** |Umożliwia automatyczne instalowanie i Konfigurowanie Azure Key Vault na maszynie wirtualnej SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie integracji Azure Key Vault dla SQL Server na platformie Azure Virtual Machines (Menedżer zasobów)](azure-key-vault-integration-configure.md). <br/> Tryb zarządzania: pełny|
| **Wyświetl użycie dysku w portalu** | Umożliwia wyświetlenie graficznej reprezentacji użycia dysku dla plików danych SQL w Azure Portal.  <br/> Tryb zarządzania: pełny | 
| **Elastyczna Licencjonowanie** | Oszczędzaj koszty, [bezproblemowo](licensing-model-azure-hybrid-benefit-ahb-change.md) przechodząc od samodzielnej licencji (znanej również jako korzyść użycia hybrydowego platformy Azure) do modelu licencjonowania z płatność zgodnie z rzeczywistym użyciem i z powrotem. <br/> Tryb zarządzania: uproszczony & pełny| 
| **Elastyczna wersja/edycja** | Jeśli zdecydujesz się zmienić [wersję](change-sql-server-version.md) lub [wydanie](change-sql-server-edition.md) SQL Server, możesz zaktualizować metadane w ramach Azure Portal bez konieczności ponownego wdrażania całej SQL Server maszyny wirtualnej.  <br/> Tryb zarządzania: uproszczony & pełny| 


## <a name="management-modes"></a>Tryby zarządzania

Możesz zdecydować się na zarejestrowanie rozszerzenia SQL IaaS w trzech trybach zarządzania: 

- Tryb **lekki** kopiuje pliki binarne rozszerzenia do maszyny wirtualnej, ale nie instaluje agenta i nie uruchamia ponownie usługi SQL Server. Tryb uproszczony obsługuje tylko Zmienianie typu licencji i wydania SQL Server i zapewnia ograniczone Zarządzanie portalem. Tej opcji należy użyć w przypadku SQL Server maszyn wirtualnych z wieloma wystąpieniami lub w ramach wystąpienia klastra trybu failover (FCI). Tryb uproszczony jest domyślnym trybem zarządzania w przypadku korzystania z funkcji [automatycznego rejestrowania](sql-agent-extension-automatic-registration-all-vms.md) lub gdy nie określono typu zarządzania podczas ręcznej rejestracji. Nie ma to wpływu na pamięć ani procesor CPU podczas korzystania z trybu uproszczonego i nie ma skojarzonego kosztu. Zalecane jest, aby najpierw zarejestrować maszynę wirtualną SQL Server w trybie uproszczonym, a następnie przeprowadzić uaktualnienie do trybu pełnego w trakcie zaplanowanego okna obsługi. 

- Tryb **pełny** powoduje zainstalowanie agenta programu SQL IaaS na maszynie wirtualnej w celu dostarczenia wszystkich funkcji, ale wymaga ponownego uruchomienia uprawnień usługi SQL Server i administratora systemu. Służy do zarządzania maszyną wirtualną SQL Server przy użyciu jednego wystąpienia. Tryb pełny instaluje dwie usługi systemu Windows, które mają minimalny wpływ na pamięć i procesor CPU — można je monitorować za pomocą Menedżera zadań. Korzystanie z trybu pełnego zarządzania nie wiąże się z żadnymi kosztami. 

- Tryb **noagent** jest przeznaczony dla SQL Server 2008 i SQL Server 2008 R2 zainstalowanych w systemie Windows Server 2008. Użycie trybu noagent nie ma wpływu na pamięć ani procesor CPU. Nie jest dostępny żaden koszt związany z używaniem trybu zarządzania bez agenta, SQL Server nie zostanie uruchomiony ponownie, a agent nie jest zainstalowany na maszynie wirtualnej. 

Bieżący tryb SQL Server agenta IaaS można wyświetlić za pomocą Azure PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="installation"></a>Instalacja

Zarejestruj maszynę wirtualną SQL Server przy użyciu rozszerzenia agenta SQL Server IaaS, aby utworzyć _zasób_ **maszyny wirtualnej SQL** w ramach subskrypcji, czyli _oddzielny_ zasób od zasobu maszyny wirtualnej. Wyrejestrowanie SQL Serverej maszyny wirtualnej z rozszerzenia spowoduje usunięcie _zasobu_ usługi **SQL Virtual** Machine, ale nie spowoduje porzucenia rzeczywistej maszyny wirtualnej.

Wdrożenie SQL Server maszyny wirtualnej w portalu Azure Marketplace za pomocą Azure Portal automatycznie rejestruje maszynę wirtualną SQL Server z rozszerzeniem. Jeśli jednak zdecydujesz się na samoinstalowanie SQL Server na maszynie wirtualnej platformy Azure lub zainicjowanie obsługi administracyjnej maszyny wirtualnej platformy Azure na podstawie niestandardowego wirtualnego dysku twardego, musisz zarejestrować maszynę wirtualną SQL Server przy użyciu rozszerzenia SQL IaaS, aby odblokować zalety funkcji. 

Zarejestrowanie rozszerzenia w trybie uproszczonym spowoduje skopiowanie plików binarnych, ale nie zainstaluje agenta na maszynie wirtualnej. Agent jest instalowany na maszynie wirtualnej, gdy rozszerzenie zostanie uaktualnione do trybu zarządzania pełnego. 

Istnieją trzy sposoby rejestracji z rozszerzeniem: 
- [Automatycznie dla wszystkich bieżących i przyszłych maszyn wirtualnych w ramach subskrypcji](sql-agent-extension-automatic-registration-all-vms.md)
- [Ręczne dla pojedynczej maszyny wirtualnej](sql-agent-extension-manually-register-single-vm.md)
- [Ręcznie w przypadku wielu maszyn wirtualnych luzem](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Obsługa nazwanych wystąpień

SQL Server rozszerzenie agenta IaaS współpracuje z nazwanym wystąpieniem SQL Server, jeśli jest to jedyne wystąpienie SQL Server dostępne na maszynie wirtualnej. Nie można zainstalować rozszerzenia na maszynach wirtualnych o wielu wystąpieniach o nazwie SQL Server, jeśli na maszynie wirtualnej nie ma wystąpienia domyślnego. 

Aby użyć nazwanego wystąpienia SQL Server, Wdróż maszynę wirtualną platformy Azure, zainstaluj w niej pojedyncze wystąpienie o nazwie SQL Server, a następnie zarejestruj je przy użyciu [rozszerzenia SQL IaaS](sql-agent-extension-manually-register-single-vm.md).

Alternatywnie, aby użyć nazwanego wystąpienia z obrazem SQL Server portalu Azure Marketplace, wykonaj następujące kroki: 

   1. Wdróż maszynę wirtualną SQL Server z poziomu portalu Azure Marketplace. 
   1. [Wyrejestruj](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) maszynę wirtualną SQL Server z rozszerzenia programu SQL IaaS Agent. 
   1. Odinstaluj SQL Server całkowicie w SQL Server maszynie wirtualnej.
   1. Zainstaluj SQL Server z nazwanym wystąpieniem w ramach maszyny wirtualnej SQL Server. 
   1. [Zarejestruj maszynę wirtualną przy użyciu rozszerzenia programu SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>Weryfikuj stan rozszerzenia

Aby sprawdzić stan rozszerzenia, użyj Azure Portal lub Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal

Sprawdź, czy rozszerzenie jest zainstalowane w Azure Portal. 

Wybierz pozycję **wszystkie ustawienia** w okienku maszyna wirtualna, a następnie wybierz pozycję **rozszerzenia**. Powinna zostać wyświetlona lista rozszerzeń **SqlIaasExtension** .

![Stan rozszerzenia agenta SQL Server IaaS w Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

Można również użyć polecenia cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Poprzednie polecenie potwierdza, że Agent jest zainstalowany i zawiera ogólne informacje o stanie. Informacje o stanie automatycznej kopii zapasowej i poprawek można uzyskać, korzystając z następujących poleceń:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Ograniczenia

Rozszerzenie SQL IaaS Agent obsługuje tylko: 

- SQL Server maszyny wirtualne wdrożone za pośrednictwem Azure Resource Manager. SQL Server maszyny wirtualne wdrożone za pośrednictwem modelu klasycznego nie są obsługiwane. 
- SQL Server maszyny wirtualne wdrożone w chmurze publicznej lub Azure Government. Wdrożenia z innymi chmurami prywatnymi i publicznymi nie są obsługiwane. 


## <a name="frequently-asked-questions"></a>Często zadawane pytania 

**Czy należy zarejestrować moją SQL Serverą maszynę wirtualną z poziomu obrazu SQL Server w witrynie Azure Marketplace?**

Nie. Firma Microsoft automatycznie rejestruje maszyny wirtualne obsługiwane przez obrazy SQL Server w portalu Azure Marketplace. Rejestracja przy użyciu rozszerzenia jest wymagana tylko wtedy, gdy maszyna wirtualna *nie* została zainicjowana z obrazów SQL Server w portalu Azure Marketplace i SQL Server została zainstalowana samoobsługowo.

**Czy rozszerzenie usługi SQL IaaS Agent jest dostępne dla wszystkich klientów?** 

Tak. Klienci powinni rejestrować SQL Server maszyny wirtualne przy użyciu rozszerzenia, jeśli nie korzystają z obrazu SQL Server z witryny Azure Marketplace, a zamiast tego są samoinstalujące się SQL Server lub mają swój niestandardowy dysk VHD. Maszyny wirtualne należące do wszystkich typów subskrypcji (Direct, Enterprise Agreement i dostawca rozwiązań w chmurze) mogą być rejestrowane przy użyciu rozszerzenia programu SQL IaaS Agent.

**Jaki jest domyślny tryb zarządzania podczas rejestrowania w rozszerzeniu programu SQL IaaS Agent?**

Domyślny tryb zarządzania podczas rejestrowania w rozszerzeniu programu SQL IaaS Agent jest *lekki*. Jeśli właściwość Zarządzanie SQL Server nie jest ustawiona podczas rejestrowania z rozszerzeniem, tryb zostanie ustawiony jako uproszczony, a usługa SQL Server nie zostanie uruchomiona ponownie. Zaleca się, aby najpierw zarejestrować się w rozszerzeniu programu SQL IaaS Agent w trybie uproszczonym, a następnie przeprowadzić uaktualnienie do pełnej wersji w oknie obsługi. Analogicznie, domyślne zarządzanie jest również lekkie w przypadku korzystania z [funkcji automatycznej rejestracji](sql-agent-extension-automatic-registration-all-vms.md).

**Jakie są wymagania wstępne, które należy zarejestrować w rozszerzeniu programu SQL IaaS Agent?**

Nie ma wymagań wstępnych do zarejestrowania się w rozszerzeniu programu SQL IaaS Agent innym niż SQL Server zainstalowane na maszynie wirtualnej. Należy pamiętać, że jeśli rozszerzenie programu SQL IaaS Agent jest zainstalowane w trybie pełnym, usługa SQL Server zostanie uruchomiona ponownie, Dlatego zalecane jest wykonanie okna obsługi.

**Czy zostanie zarejestrowana przy użyciu rozszerzenia agenta SQL IaaS, Zainstaluj agenta na mojej maszynie wirtualnej?**

Tak, Rejestracja przy użyciu rozszerzenia agenta SQL IaaS w trybie pełnego zarządzania powoduje zainstalowanie agenta na maszynie wirtualnej. Zarejestrowanie w trybie uproszczonym lub brak agenta nie jest dozwolone. 

Rejestracja przy użyciu rozszerzenia agenta SQL IaaS w trybie uproszczonym powoduje jedynie skopiowanie *plików binarnych* rozszerzenia agenta SQL IaaS do maszyny wirtualnej, a agent nie jest instalowany. Te pliki binarne są następnie używane do instalowania agenta, gdy tryb zarządzania zostanie uaktualniony do wersji pełnej.


**Czy program zostanie zarejestrowany przy użyciu SQL Server ponownego uruchomienia rozszerzenia agenta SQL na mojej maszynie wirtualnej?**

Jest to zależne od trybu określonego podczas rejestracji. W przypadku określenia trybu uproszczonego lub noagent usługa SQL Server nie zostanie uruchomiona ponownie. Jednak określenie trybu zarządzania jako pełny spowoduje ponowne uruchomienie usługi SQL Server. Funkcja automatycznego rejestrowania rejestruje maszyny wirtualne SQL Server w trybie uproszczonym, chyba że wersja systemu Windows Server to 2008, w takim przypadku SQL Server maszyna wirtualna zostanie zarejestrowana w trybie noagent. 

**Jaka jest różnica między trybami zarządzania Lightweight i noagent podczas rejestrowania się w rozszerzeniu programu SQL IaaS Agent?** 

Tryb zarządzania bez agenta jest jedynym dostępnym trybem zarządzania dla SQL Server 2008 i SQL Server 2008 R2 w systemie Windows Server 2008. W przypadku wszystkich nowszych wersji systemu Windows Server dwa dostępne tryby zarządzania są lekkie i pełne. 

Tryb noagent wymaga, aby właściwości SQL Server wersji i wydania zostały ustawione przez klienta. Tryb uproszczony wysyła zapytanie do maszyny wirtualnej w celu znalezienia wersji i wydania wystąpienia SQL Server.

**Czy można zarejestrować się przy użyciu rozszerzenia SQL IaaS Agent bez określania typu licencji SQL Server?**

Nie. Typ licencji SQL Server nie jest właściwością opcjonalną podczas rejestrowania się w rozszerzeniu programu SQL IaaS Agent. Musisz ustawić typ licencji SQL Server na płatność zgodnie z rzeczywistym użyciem lub Korzyść użycia hybrydowego platformy Azure podczas rejestrowania się w rozszerzeniu SQL IaaS Agent we wszystkich trybach zarządzania (noagent, Lightweight i Full). Jeśli masz dowolną z bezpłatnych wersji SQL Server zainstalowanych, takich jak deweloper lub wersja ewaluacyjna, musisz zarejestrować się, korzystając z licencjonowania z opcją płatność zgodnie z rzeczywistym użyciem. Korzyść użycia hybrydowego platformy Azure jest dostępna tylko dla płatnych wersji SQL Server, takich jak wersje Enterprise i Standard.

**Czy mogę uaktualnić rozszerzenie SQL Server IaaS z trybu noagent do trybu pełnego?**

Nie. Uaktualnianie trybu zarządzania do wersji pełnej lub lekkiej nie jest dostępne dla trybu noagent. Jest to ograniczenie techniczne systemu Windows Server 2008. Musisz najpierw uaktualnić system operacyjny do wersji Windows Server 2008 R2 lub nowszej, a następnie można przeprowadzić uaktualnienie do trybu pełnego zarządzania. 

**Czy można uaktualnić rozszerzenie SQL Server IaaS z trybu uproszczonego do trybu pełnego?**

Tak. Uaktualnianie trybu zarządzania z lekkich do pełnych jest obsługiwane za pośrednictwem Azure PowerShell lub Azure Portal. Spowoduje to wyzwolenie ponownego uruchomienia usługi SQL Server.

**Czy można obniżyć SQL Server rozszerzenie IaaS z trybu pełnego do noagent lub Lightweight Management Mode?**

Nie. Obniżenie poziomu trybu zarządzania rozszerzeniami SQL Server IaaS nie jest obsługiwane. Trybu zarządzania nie można zmienić z trybu pełnego na Lightweight lub noagent i nie można go zmienić z trybu uproszczonego na tryb noagent. 

Aby zmienić tryb zarządzania z pełnego zarządzania, należy [wyrejestrować](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server maszynę wirtualną z rozszerzenia programu SQL IaaS Agent przez usunięcie _zasobu_ maszyny wirtualnej SQL i ponowne zarejestrowanie SQL Server maszynie wirtualnej z rozszerzeniem agenta SQL IaaS ponownie w innym trybie zarządzania.

**Czy można zarejestrować się przy użyciu rozszerzenia SQL IaaS Agent w Azure Portal?**

Nie. Rejestracja przy użyciu rozszerzenia agenta SQL IaaS nie jest dostępna w Azure Portal. Rejestrowanie przy użyciu rozszerzenia agenta SQL IaaS jest obsługiwane tylko w interfejsie wiersza polecenia platformy Azure lub w Azure PowerShell. 

**Czy można zarejestrować maszynę wirtualną za pomocą rozszerzenia programu SQL IaaS Agent przed zainstalowaniem SQL Server?**

Nie. Maszyna wirtualna musi mieć co najmniej jedno wystąpienie SQL Server (aparat bazy danych), aby można było pomyślnie zarejestrować się w rozszerzeniu agenta SQL IaaS. Jeśli na maszynie wirtualnej nie ma SQL Server wystąpienia, nowy zasób Microsoft. SqlVirtualMachine będzie w stanie niepowodzenia.

**Czy można zarejestrować maszynę wirtualną za pomocą rozszerzenia programu SQL IaaS Agent, jeśli istnieje wiele wystąpień SQL Server?**

Tak, pod warunkiem, że na maszynie wirtualnej jest domyślne wystąpienie. Rozszerzenie agenta SQL IaaS zarejestruje tylko jedno wystąpienie SQL Server (aparatu bazy danych). Rozszerzenie programu SQL IaaS Agent zarejestruje domyślne wystąpienie SQL Server w przypadku wielu wystąpień.

**Czy można zarejestrować SQL Server wystąpienie klastra trybu failover z rozszerzeniem agenta SQL IaaS?**

Tak. SQL Server wystąpienia klastra trybu failover na maszynie wirtualnej platformy Azure można zarejestrować przy użyciu rozszerzenia agenta SQL IaaS w trybie uproszczonym. Niemniej jednak SQL Server wystąpienia klastra trybu failover nie można uaktualnić do trybu pełnego zarządzania.

**Czy można zarejestrować moją maszynę wirtualną przy użyciu rozszerzenia agenta SQL IaaS, jeśli jest skonfigurowana zawsze włączona Grupa dostępności?**

Tak. Nie ma żadnych ograniczeń dotyczących rejestrowania wystąpienia SQL Server na maszynie wirtualnej platformy Azure przy użyciu rozszerzenia agenta SQL IaaS w przypadku uczestnictwa w konfiguracji grupy dostępności zawsze włączone.

**Jaki jest koszt rejestrowania w rozszerzeniu SQL IaaS agent lub z uaktualnieniem do trybu pełnej możliwości zarządzania?**

Brak. Nie istnieje opłata skojarzona z rejestracją przy użyciu rozszerzenia agenta SQL IaaS lub z użyciem jednego z trzech trybów zarządzania. Zarządzanie maszyną wirtualną SQL Server przy użyciu rozszerzenia jest całkowicie bezpłatne. 

**Jaki jest wpływ na wydajność korzystania z różnych trybów zarządzania?**

Nie ma to wpływu na użycie trybu *noagent* i *uproszczonego* zarządzania. W przypadku korzystania z trybu *pełnego* zarządzania z dwóch usług, które są zainstalowane w systemie operacyjnym, ma minimalny wpływ. Mogą one być monitorowane za pośrednictwem Menedżera zadań i widoczne w wbudowanej konsoli usług systemu Windows. 

Nazwy następujących usług:
- `SqlIaaSExtensionQuery` (Nazwa wyświetlana `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension` (Nazwa wyświetlana `Microsoft SQL Server IaaS Agent` )

**Jak mogę usunąć rozszerzenie?**

Usuń rozszerzenie, [wyrejestrując](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) maszynę wirtualną SQL Server z rozszerzenia programu SQL IaaS Agent. 

## <a name="next-steps"></a>Następne kroki

Aby zainstalować rozszerzenie SQL Server IaaS do SQL Server na maszynach wirtualnych platformy Azure, zapoznaj się z artykułami dotyczącymi [automatycznej instalacji](sql-agent-extension-automatic-registration-all-vms.md), [pojedynczych maszyn wirtualnych](sql-agent-extension-manually-register-single-vm.md)lub [maszyn wirtualnych](sql-agent-extension-manually-register-vms-bulk.md).

Aby uzyskać więcej informacji na temat uruchamiania SQL Server na platformie Azure Virtual Machines, zobacz temat [co to jest SQL Server na platformie azure Virtual Machines?](sql-server-on-azure-vm-iaas-what-is-overview.md).
