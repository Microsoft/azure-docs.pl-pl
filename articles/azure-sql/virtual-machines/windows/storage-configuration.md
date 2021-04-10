---
title: Konfigurowanie magazynu dla maszyn wirtualnych SQL Server | Microsoft Docs
description: W tym temacie opisano, jak platforma Azure konfiguruje magazyn dla SQL Server maszyn wirtualnych podczas aprowizacji (Azure Resource Manager model wdrażania). Wyjaśniono również, jak można skonfigurować magazyn dla istniejących maszyn wirtualnych SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 982bd9239c5e95c9b7af09b5f54c5a09067ca7c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565430"
---
# <a name="configure-storage-for-sql-server-vms"></a>Konfigurowanie magazynu dla maszyn wirtualnych SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule przedstawiono sposób konfigurowania magazynu dla SQL Server na platformie Azure Virtual Machines.

SQL Server maszyny wirtualne wdrożone za pośrednictwem obrazów z witryny Marketplace są automatycznie zgodne z domyślnymi [najlepszymi rozwiązaniami](performance-guidelines-best-practices-storage.md) , które można zmodyfikować podczas wdrażania. Niektóre z tych ustawień konfiguracji można zmienić po wdrożeniu. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z ustawień konfiguracji automatycznego magazynu, maszyna wirtualna wymaga następujących cech:

* Udostępnione za pomocą [obrazu galerii SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) lub zarejestrowane przy użyciu [rozszerzenia SQL IaaS]().
* Używa [modelu wdrażania Menedżer zasobów](../../../azure-resource-manager/management/deployment-models.md).
* Używa warstwy [Premium dysków SSD](../../../virtual-machines/disks-types.md).

## <a name="new-vms"></a>Nowe maszyny wirtualne

W poniższych sekcjach opisano sposób konfigurowania magazynu dla nowych maszyn wirtualnych SQL Server.

### <a name="azure-portal"></a>Azure Portal

Podczas aprowizacji maszyny wirtualnej platformy Azure przy użyciu obrazu galerii SQL Server wybierz pozycję **Zmień konfigurację** na karcie **Ustawienia SQL Server** , aby otworzyć stronę Konfiguracja magazynu zoptymalizowanego pod kątem wydajności. Możesz pozostawić wartości domyślne lub zmodyfikować typ konfiguracji dysku, który najlepiej odpowiada Twoim potrzebom, na podstawie obciążenia. 

![Zrzut ekranu, który podświetla kartę Ustawienia SQL Server i opcję Zmień konfigurację.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Wybierz typ obciążenia, które wdrażasz SQL Server na potrzeby **optymalizacji magazynu**. W przypadku opcji **ogólnej** optymalizacji domyślnie istnieje jeden dysk z danymi o pojemności 5000 maks. liczba IOPS i ten sam dysk jest używany do przechowywania danych, dzienników transakcji i magazynu tempdb. 

Wybranie opcji **przetwarzanie transakcyjne** (OLTP) lub **Magazyn danych** spowoduje utworzenie oddzielnego dysku dla danych, oddzielnego dysku dla dziennika transakcji i użycie lokalnego SSD dla bazy tempdb. Nie istnieją różnice między **przetwarzaniem transakcyjnym** i **magazynem danych**, ale zmieniają [konfigurację rozłożenia i flagi śledzenia](#workload-optimization-settings). Wybranie usługi Premium Storage ustawia buforowanie do *odczytu* dla dysku danych i *Brak* dla dysku dziennika jako SQL Server najlepszych rozwiązań dotyczących [wydajności maszyn wirtualnych](performance-guidelines-best-practices.md). 

![SQL Server konfigurację magazynu maszyny wirtualnej podczas aprowizacji](./media/storage-configuration/sql-vm-storage-configuration.png)

Konfiguracja dysku jest w pełni dostosowywana, co pozwala na skonfigurowanie topologii magazynu, typu dysku i liczby IOPs potrzebnych dla SQL Server obciążenia maszyny wirtualnej. Istnieje również możliwość korzystania z UltraSSD (wersja zapoznawcza) jako opcji dla **typu dysku** , jeśli maszyna wirtualna SQL Server znajduje się w jednym z obsługiwanych regionów (Wschodnie stany USA 2, Azja Południowo-Wschodnia i Europa Północna) i włączono [dla niej Ultra disks](../../../virtual-machines/disks-enable-ultra-ssd.md).  

Ponadto można ustawić buforowanie dla dysków. Maszyny wirtualne platformy Azure mają wielowarstwową technologię buforowania o nazwie [BLOB cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) , gdy jest używana z [dyskami w warstwie Premium](../../../virtual-machines/disks-types.md#premium-ssd). Pamięć podręczna obiektów BLOB używa kombinacji pamięci RAM maszyny wirtualnej i lokalnego dysku SSD na potrzeby buforowania. 

Buforowanie dysków dla SSD w warstwie Premium może być *tylko do odczytu*, *ReadWrite* lub *none*. 

- Buforowanie *tylko do odczytu* jest wysoce korzystne dla SQL Server plików danych przechowywanych w Premium Storage. Buforowanie w trybie *tylko* do odczytu umożliwia opóźnienie w przypadku małych odczytów, dużą liczbę operacji we/wy odczytu i przepływność, co oznacza, że operacje odczytu są wykonywane z pamięci podręcznej, która jest w pamięci Te odczyty są znacznie szybsze niż odczyt z dysku danych, który pochodzi z usługi Azure Blob Storage. Usługa Premium Storage nie zlicza odczytów obsługiwanych z pamięci podręcznej do operacji we/wy na dysku. W związku z tym, Twoje zastosowanie ma możliwość osiągnięcia wyższej całkowitej liczby operacji we/wy na sekundę. 
- *Nie należy* używać konfiguracji pamięci podręcznej dla dysków hostującym SQL Server pliku dziennika, ponieważ plik dziennika jest pisany sekwencyjnie i nie korzysta z buforowania *tylko do odczytu* . 
- Buforowanie *ReadWrite* nie powinno być używane do hostowania plików SQL Server, ponieważ SQL Server nie obsługuje spójności danych z pamięcią podręczną *ReadWrite* . Zapisy pojemności pamięci podręcznej obiektów BLOB *tylko do odczytu* i opóźnień nieco zwiększają się, jeśli zapisy przechodzą *przez warstwy* pamięci podręcznej obiektów BLOB. 


   > [!TIP]
   > Upewnij się, że konfiguracja magazynu jest zgodna z ograniczeniami narzuconymi przez wybrany rozmiar maszyny wirtualnej. Wybranie parametrów magazynu, które przekraczają limit wydajności dla rozmiaru maszyny wirtualnej, spowoduje ostrzeżenie: `The desired performance might not be reached due to the maximum virtual machine disk performance cap` . Zmniejsz liczbę operacji we/wy przez zmianę typu dysku lub zwiększenie ograniczenia wydajności przez zwiększenie rozmiaru maszyny wirtualnej. Nie spowoduje to zatrzymania aprowizacji. 


Na podstawie wybranych opcji platforma Azure wykonuje następujące zadania konfiguracji magazynu po utworzeniu maszyny wirtualnej:

* Tworzy i dołącza dysków SSD Premium do maszyny wirtualnej.
* Konfiguruje dyski danych, które mają być dostępne dla SQL Server.
* Konfiguruje dyski danych w puli magazynów na podstawie określonych wymagań dotyczących rozmiaru i wydajności (IOPS i przepływności).
* Kojarzy pulę magazynów z nowym dyskiem na maszynie wirtualnej.
* Optymalizuje ten nowy dysk w oparciu o określony typ obciążenia (magazynowanie danych, przetwarzanie transakcyjne lub ogólne).

Aby zapoznać się z pełnym przewodnikiem dotyczącym tworzenia SQL Server maszyny wirtualnej w Azure Portal, zobacz [samouczek aprowizacji](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

W przypadku używania następujących szablonów Menedżer zasobów są domyślnie dołączone dwa dyski z danymi Premium bez konfiguracji puli magazynu. Można jednak dostosować te szablony, aby zmienić liczbę dysków danych w warstwie Premium dołączonych do maszyny wirtualnej.

* [Tworzenie maszyny wirtualnej przy użyciu zautomatyzowanej kopii zapasowej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Tworzenie maszyny wirtualnej z automatyczną poprawką](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Tworzenie maszyny wirtualnej z integracją AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Szablon szybkiego startu

Poniższy szablon szybkiego startu umożliwia wdrożenie maszyny wirtualnej SQL Server przy użyciu funkcji optymalizacji magazynu. 

* [Tworzenie maszyny wirtualnej przy użyciu optymalizacji magazynu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Tworzenie maszyny wirtualnej przy użyciu UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Istniejące maszyny wirtualne

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących maszyn wirtualnych SQL Server można zmodyfikować niektóre ustawienia magazynu w Azure Portal. Otwórz [zasób usługi SQL Virtual Machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)i wybierz pozycję **Przegląd**. Na stronie Przegląd SQL Server jest wyświetlane bieżące użycie magazynu dla maszyny wirtualnej. Na tym wykresie są wyświetlane wszystkie dyski, które znajdują się na maszynie wirtualnej. Dla każdego dysku jest wyświetlana w czterech sekcjach:

* Dane SQL
* Dziennik SQL
* Inne (magazyn inny niż SQL)
* Dostępne

Aby zmodyfikować ustawienia magazynu, wybierz pozycję **Konfiguruj** w obszarze **Ustawienia**. 

![Zrzut ekranu, który podświetla opcję Konfiguruj i sekcję użycie magazynu.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

Można zmodyfikować ustawienia dysku dla dysków, które zostały skonfigurowane podczas procesu tworzenia maszyny wirtualnej SQL Server. Wybranie pozycji **rozszerzająca dysk** otwiera stronę modyfikacja dysku, umożliwiając zmianę typu dysku, a także dodanie dodatkowych dysków. 

![Konfigurowanie magazynu dla istniejącej maszyny wirtualnej SQL Server](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>Automatyczne zmiany

Ta sekcja zawiera informacje o zmianach konfiguracji magazynu, które usługa Azure automatycznie wykonuje podczas SQL Server aprowizacji lub konfiguracji maszyny wirtualnej w Azure Portal.

* Platforma Azure konfiguruje pulę magazynów z magazynu wybranego z maszyny wirtualnej. Następna sekcja tego tematu zawiera szczegółowe informacje o konfiguracji puli magazynu.
* Funkcja automatycznej konfiguracji magazynu zawsze używa dysków danych w [warstwie Premium dysków SSD](../../../virtual-machines/disks-types.md) P30. W związku z tym istnieje 1:1 mapowanie między wybraną liczbą terabajtów a liczbą dysków danych podłączonych do maszyny wirtualnej.

Aby uzyskać informacje o cenach, zobacz stronę z [cennikiem usługi Storage](https://azure.microsoft.com/pricing/details/storage) na karcie **Disk Storage** .

### <a name="creation-of-the-storage-pool"></a>Tworzenie puli magazynów

Platforma Azure używa następujących ustawień do utworzenia puli magazynów na SQL Server maszynach wirtualnych.

| Ustawienie | Wartość |
| --- | --- |
| Rozmiar paska |256 KB (magazynowanie danych); 64 KB (transakcyjna) |
| Rozmiary dysków |1 TB każdego |
| Pamięć podręczna |Odczyt |
| Rozmiar alokacji |rozmiar jednostki alokacji systemu plików NTFS 64 KB |
| Odzyskiwania | Odzyskiwanie proste (bez odporności) |
| Liczba kolumn |Liczba dysków z danymi do 8<sup>1</sup> |


<sup>1</sup> po utworzeniu puli magazynów nie można zmienić liczby kolumn w puli magazynów.


### <a name="workload-optimization-settings"></a>Ustawienia optymalizacji obciążenia

W poniższej tabeli opisano trzy dostępne opcje typu obciążenia oraz ich odpowiednie optymalizacje:

| Typ obciążenia | Opis | Optymalizacje |
| --- | --- | --- |
| **Ogólne** |Ustawienie domyślne obsługujące większość obciążeń |Brak |
| **Przetwarzanie transakcyjne** |Optymalizuje magazyn dla tradycyjnych obciążeń OLTP bazy danych |Flaga śledzenia 1117<br/>Flaga śledzenia 1118 |
| **Magazynowanie danych** |Optymalizuje magazyn do obciążeń analitycznych i sprawozdawczych |Flaga śledzenia 610<br/>Flaga śledzenia 1117 |

> [!NOTE]
> Typ obciążenia można określić tylko podczas inicjowania obsługi administracyjnej maszyny wirtualnej SQL Server, wybierając ją w kroku konfiguracji magazynu.

## <a name="enable-caching"></a>Enable caching 

Zmień zasady buforowania na poziomie dysku. Można to zrobić za pomocą Azure Portal, [programu PowerShell](/powershell/module/az.compute/set-azvmdatadisk)lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/vm/disk). 

Aby zmienić zasady buforowania w Azure Portal, wykonaj następujące kroki:

1. Zatrzymaj usługę SQL Server. 
1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
1. Przejdź do maszyny wirtualnej, wybierz pozycję **dyski** w obszarze **Ustawienia**. 
   
   ![Zrzut ekranu przedstawiający blok Konfiguracja dysku maszyny wirtualnej w Azure Portal.](./media/storage-configuration/disk-in-portal.png)

1. Wybierz odpowiednie zasady buforowania dla dysku z listy rozwijanej. 

   ![Zrzut ekranu przedstawiający konfigurację zasad buforowania dysku w Azure Portal.](./media/storage-configuration/azure-disk-config.png)

1. Gdy zmiana zacznie obowiązywać, należy ponownie uruchomić maszynę wirtualną SQL Server i uruchomić usługę SQL Server. 


## <a name="enable-write-accelerator"></a>Włącz akcelerator zapisu

Przyspieszenie zapisu to funkcja dyskowa, która jest dostępna tylko dla Virtual Machines serii M (maszyny wirtualne). Przyspieszenie zapisu polega na zwiększeniu opóźnienia operacji we/wy na platformie Azure Premium Storage, gdy wymagane jest opóźnienie jednocyfrowe we/wy ze względu na duże obciążenia OLTP lub środowiska magazynu danych. 

Zatrzymaj wszystkie działania SQL Server i zamknij usługę SQL Server przed wprowadzeniem zmian w zasadach przyspieszenia zapisu. 

Jeśli dyski są rozłożone, Włącz przyspieszenie zapisu poszczególnych dysków, a maszyna wirtualna platformy Azure powinna zostać ZAMKNIĘTA przed wprowadzeniem jakichkolwiek zmian. 

Aby włączyć przyspieszenie zapisu przy użyciu Azure Portal, wykonaj następujące kroki:

1. Zatrzymaj usługę SQL Server. Jeśli dyski są rozłożone, Zamknij maszynę wirtualną. 
1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
1. Przejdź do maszyny wirtualnej, wybierz pozycję **dyski** w obszarze **Ustawienia**. 
   
   ![Zrzut ekranu przedstawiający blok Konfiguracja dysku maszyny wirtualnej w Azure Portal.](./media/storage-configuration/disk-in-portal.png)

1. Wybierz opcję pamięć podręczna z **Akcelerator zapisu** dysku z listy rozwijanej. 

   ![Zrzut ekranu przedstawiający zasady pamięci podręcznej zapisu akceleratora.](./media/storage-configuration/write-accelerator.png)

1. Gdy zmiana zacznie obowiązywać, uruchom maszynę wirtualną i usługę SQL Server. 

## <a name="disk-striping"></a>Rozkładanie dysku

Aby uzyskać większą przepływność, można dodać dodatkowe dyski danych i użyć pasków dyskowych. Aby określić liczbę dysków z danymi, Przeanalizuj przepływność i przepustowość wymaganą dla plików danych SQL Server, w tym dziennika i tempdb. Limity przepływności i przepustowości różnią się w zależności od rozmiaru maszyny wirtualnej. Aby dowiedzieć się więcej, zobacz [rozmiar maszyny wirtualnej](../../../virtual-machines/sizes.md)


* W przypadku systemu Windows 8/Windows Server 2012 lub nowszego Użyj funkcji [miejsca do magazynowania](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) z następującymi wskazówkami:

  1. Ustaw przeplot (rozmiar paska) na 64 KB (65 536 bajtów), aby uniknąć wpływu na wydajność ze względu na niezgodność partycji. Ta wartość musi być ustawiona przy użyciu programu PowerShell.

  2. Ustaw liczbę kolumn = liczba dysków fizycznych. Użyj programu PowerShell podczas konfigurowania więcej niż 8 dysków (nie Menedżer serwera interfejsie użytkownika).

Na przykład poniższy program PowerShell tworzy nową pulę magazynów o rozmiarze przeplotu do 64 KB i liczbę kolumn równą ilości dysku fizycznego w puli magazynów:

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

  * W przypadku systemu Windows 2008 R2 lub starszego można użyć dysków dynamicznych (woluminów rozłożonych systemu operacyjnego), a rozmiar paska jest zawsze 64 KB. Ta opcja jest przestarzała w przypadku systemu Windows 8/Windows Server 2012. Aby uzyskać więcej informacji, zobacz Instrukcja obsługi w [usłudze dysk wirtualny przechodzi do interfejsu API zarządzania magazynem systemu Windows](https://docs.microsoft.com/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).
 
  * Jeśli używasz funkcji [bezpośrednie miejsca do magazynowania (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) z [wystąpieniami klastra trybu failover SQL Server](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure), musisz skonfigurować jedną pulę. Mimo że w tej pojedynczej puli można utworzyć różne woluminy, będą one miały te same właściwości, takie jak te same zasady buforowania.
 
  * Określ liczbę dysków skojarzonych z pulą magazynów na podstawie oczekiwań obciążenia. Należy pamiętać, że różne rozmiary maszyn wirtualnych dopuszczają różne liczby dołączonych dysków danych. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json).


## <a name="next-steps"></a>Następne kroki

Aby poznać inne tematy związane z uruchamianiem SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).
