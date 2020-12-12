---
title: Przygotowywanie maszyn wirtualnych do FCI
description: Przygotuj maszyny wirtualne platformy Azure tak, aby korzystały z wystąpienia klastra trybu failover (FCI) i SQL Server na platformie Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 52d6bc97245423a4add392ab05634d21bcf83a0d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358014"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Przygotowywanie maszyn wirtualnych do FCI (SQL Server na maszynach wirtualnych platformy Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule opisano sposób przygotowania maszyn wirtualnych platformy Azure do używania ich z wystąpieniem klastra trybu failover SQL Server (FCI). Ustawienia konfiguracji różnią się w zależności od rozwiązania magazynu FCI, dlatego upewnij się, że wybierasz poprawną konfigurację, aby odpowiadała Twojemu środowisku i firmie. 

Aby dowiedzieć się więcej, zobacz Omówienie [FCI z SQL Server na maszynach wirtualnych platformy Azure](failover-cluster-instance-overview.md) i [najlepszych rozwiązaniach klastra](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Wymagania wstępne 

- Subskrypcja platformy Microsoft Azure. Zacznij korzystać [bezpłatnie](https://azure.microsoft.com/free/). 
- Domena systemu Windows na maszynach wirtualnych platformy Azure lub lokalne centrum danych rozszerzone na platformę Azure z parowaniem sieci wirtualnej.
- Konto, które ma uprawnienia do tworzenia obiektów w usłudze Azure Virtual Machines i w Active Directory.
- Sieć wirtualna platformy Azure i podsieć z wystarczającą przestrzenią adresową IP dla tych składników:
   - Obie maszyny wirtualne
   - Adres IP klastra trybu failover systemu Windows
   - Adres IP dla każdego FCIu
- System DNS skonfigurowany w sieci platformy Azure, wskazujący kontrolery domeny.

## <a name="choose-an-fci-storage-option"></a>Wybierz opcję magazynu FCI

Ustawienia konfiguracji maszyny wirtualnej różnią się w zależności od opcji magazynu planowanej dla SQL Server wystąpienia klastra trybu failover. Przed przygotowaniem maszyny wirtualnej Przejrzyj [dostępne opcje magazynu FCI](failover-cluster-instance-overview.md#storage) i wybierz opcję, która najlepiej odpowiada Twojemu środowisku i potrzebom biznesowym. Starannie wybieraj odpowiednie opcje konfiguracji maszyny wirtualnej w tym artykule w zależności od wybranego magazynu. 

## <a name="configure-vm-availability"></a>Konfigurowanie dostępności maszyny wirtualnej 

Funkcja klaster trybu failover wymaga, aby maszyny wirtualne były umieszczone w [zestawie dostępności](../../../virtual-machines/linux/tutorial-availability-sets.md) lub w [strefie dostępności](../../../availability-zones/az-overview.md#availability-zones). W przypadku wybrania zestawów dostępności można użyć [grup umieszczania w sąsiedztwie](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) , aby znaleźć bliżej maszyn wirtualnych. W rzeczywistości grupy umieszczania w sąsiedztwie są warunkiem wstępnym korzystania z dysków udostępnionych platformy Azure. 

Starannie wybieraj opcję dostępność maszyny wirtualnej zgodną z zaznaczoną konfiguracją klastra: 

- **Azure Shared disks**: opcja dostępność jest różna, jeśli używasz dysków SSD Premium lub UltraDisk:
   - SSD w warstwie Premium: [zestaw dostępności](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) w różnych domenach błędów/aktualizacji dla dysków SSD Premium jest umieszczony wewnątrz [grupy umieszczania sąsiedztwa](../../../virtual-machines/windows/proximity-placement-groups-portal.md).
   - Ultra Disk: [strefa dostępności](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) , ale maszyny wirtualne muszą znajdować się w tej samej strefie dostępności, co zmniejsza dostępność klastra do 99,9%. 
- **Udziały plików w warstwie Premium**: [zestaw dostępności](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) lub [strefa dostępności](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **Bezpośrednie miejsca do magazynowania**: [zestaw dostępności](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

> [!IMPORTANT]
> Nie można ustawić ani zmienić zestawu dostępności po utworzeniu maszyny wirtualnej.

## <a name="create-the-virtual-machines"></a>Tworzenie maszyn wirtualnych

Po skonfigurowaniu dostępności maszyny wirtualnej możesz utworzyć maszyny wirtualne. Możesz zdecydować się na korzystanie z obrazu portalu Azure Marketplace, który jest już zainstalowany lub nie ma SQL Server. Jeśli jednak wybierzesz obraz dla SQL Server na maszynach wirtualnych platformy Azure, musisz odinstalować SQL Server z maszyny wirtualnej przed skonfigurowaniem wystąpienia klastra trybu failover. 

### <a name="considerations"></a>Zagadnienia do rozważenia

W klastrze trybu failover gościa maszyny wirtualnej platformy Azure zalecamy korzystanie z jednej karty sieciowej na serwer (węzeł klastra) i pojedynczej podsieci. Sieć platformy Azure ma fizyczną nadmiarowość, co sprawia, że dodatkowe karty sieciowe i podsieci nie są potrzebne w klastrze gościa maszyny wirtualnej Azure IaaS. Mimo że raport z weryfikacji klastra wyświetli ostrzeżenie, że węzły są dostępne tylko w ramach jednej sieci, to ostrzeżenie można zignorować w klastrach trybu failover gościa maszyny wirtualnej IaaS platformy Azure.

Umieść obie maszyny wirtualne:

- W tej samej grupie zasobów platformy Azure, w której znajduje się zestaw dostępności, jeśli używasz zestawów dostępności.
- W tej samej sieci wirtualnej co kontroler domeny.
- W podsieci, która ma wystarczającą przestrzeń adresową IP dla maszyn wirtualnych i wszystkich FCIs, które mogą być używane w klastrze.
- W obszarze zestaw dostępności platformy Azure lub strefa dostępności.

Maszynę wirtualną platformy Azure można utworzyć przy użyciu obrazu [z](sql-vm-create-portal-quickstart.md) zainstalowanym SQL Server lub [bez](../../../virtual-machines/windows/quick-create-portal.md) niego. W przypadku wybrania obrazu SQL Server należy ręcznie odinstalować wystąpienie SQL Server przed zainstalowaniem wystąpienia klastra trybu failover. 


## <a name="uninstall-sql-server"></a>Odinstaluj SQL Server

W ramach procesu tworzenia FCI należy zainstalować SQL Server jako wystąpienie klastrowane w klastrze trybu failover. *Jeśli wdrożono maszynę wirtualną z obrazem portalu Azure Marketplace bez SQL Server, możesz pominąć ten krok.* W przypadku wdrożenia obrazu z preinstalowanym SQL Server należy wyrejestrować maszynę wirtualną SQL Server z rozszerzenia agenta SQL IaaS, a następnie odinstalować SQL Server. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>Wyrejestruj z rozszerzenia programu SQL IaaS Agent

SQL Server obrazy maszyn wirtualnych z witryny Azure Marketplace są automatycznie rejestrowane przy użyciu rozszerzenia agenta SQL IaaS. Przed odinstalowaniem wstępnie zainstalowanego wystąpienia SQL Server należy najpierw [wyrejestrować każdą maszynę wirtualną SQL Server z rozszerzenia programu SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 

### <a name="uninstall-sql-server"></a>Odinstaluj SQL Server

Po wyrejestrowaniu z rozszerzenia można odinstalować SQL Server. Wykonaj następujące czynności na każdej maszynie wirtualnej: 

1. Połącz się z maszyną wirtualną przy użyciu protokołu RDP.

   Gdy po raz pierwszy łączysz się z maszyną wirtualną przy użyciu protokołu RDP, zostanie wyświetlony monit z pytaniem, czy chcesz zezwolić na odnajdywanie komputera w sieci. Wybierz pozycję **Tak**.

1. Jeśli używasz jednego z obrazów maszyn wirtualnych opartych na SQL Server, Usuń wystąpienie SQL Server:

   1. W obszarze **programy i funkcje** kliknij prawym przyciskiem myszy pozycję **Microsoft SQL Server 201_ (64-bitowe)** i wybierz polecenie **Odinstaluj/Zmień**.
   1. Wybierz pozycję **Usuń**.
   1. Wybierz wystąpienie domyślne.
   1. Usuń wszystkie funkcje w obszarze **usługi aparatu bazy danych**. Nie usuwaj żadnych elementów w obszarze **funkcje udostępnione**. Zobaczysz coś podobnego do poniższego zrzutu ekranu:

      ![Wybieranie funkcji](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. Wybierz pozycję **dalej**, a następnie wybierz pozycję **Usuń**.
   1. Po pomyślnym usunięciu wystąpienia ponownie uruchom maszynę wirtualną. 

## <a name="open-the-firewall"></a>Otwieranie zapory 

Na każdej maszynie wirtualnej Otwórz port TCP zapory systemu Windows, którego używa SQL Server. Domyślnie jest to port 1433. Można jednak zmienić port SQL Server w ramach wdrożenia maszyny wirtualnej platformy Azure, aby otworzyć port, którego SQL Server używa w danym środowisku. Ten port jest automatycznie otwierany na SQL Server obrazów wdrożonych z portalu Azure Marketplace. 

W przypadku korzystania z [modułu równoważenia obciążenia](failover-cluster-instance-vnn-azure-load-balancer-configure.md)należy również otworzyć port używany przez sondę kondycji. Domyślnie jest to port 59999. Może to być dowolny port TCP określony podczas tworzenia modułu równoważenia obciążenia. 

W tej tabeli przedstawiono informacje o portach, które mogą być konieczne do otwarcia, w zależności od konfiguracji FCI: 

   | Przeznaczenie | Port | Uwagi
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Normalny port dla domyślnych wystąpień SQL Server. Jeśli obraz został użyty z galerii, ten port zostanie automatycznie otwarty. </br> </br> **Używane przez**: wszystkie konfiguracje FCI. |
   | Sonda kondycji | TCP 59999 | Dowolny otwarty port TCP. Skonfiguruj [sondę kondycji](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) modułu równoważenia obciążenia i klaster, aby używać tego portu. </br> </br> **Używane przez**: FCI z usługą równoważenia obciążenia. |
   | Udział plików | UDP 445 | Port, którego używa Usługa udziału plików. </br> </br> **Używane przez**: FCI z udziałem plików w warstwie Premium. |

## <a name="join-the-domain"></a>Przyłączenie się do domeny

Musisz również dołączyć maszyny wirtualne do domeny. Można to zrobić za pomocą [szablonu szybkiego startu](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain). 

## <a name="review-storage-configuration"></a>Przejrzyj konfigurację magazynu

Maszyny wirtualne utworzone w portalu Azure Marketplace pochodzą z dołączonego magazynu. Jeśli planujesz skonfigurować magazyn FCI przy użyciu udziałów plików w warstwie Premium lub dysków udostępnionych platformy Azure, możesz usunąć dołączony magazyn, aby zaoszczędzić na kosztach, ponieważ lokalny magazyn nie jest używany w przypadku wystąpienia klastra trybu failover. Można jednak używać dołączonego magazynu do Bezpośrednie miejsca do magazynowania rozwiązań FCI, więc usunięcie ich w tym przypadku może być nieprzydatne. Zapoznaj się z rozwiązaniem magazynu FCI, aby ustalić, czy usunięcie dołączonego magazynu jest optymalne dla oszczędności kosztów. 


## <a name="next-steps"></a>Następne kroki

Teraz, po przygotowaniu środowiska maszyny wirtualnej, możesz skonfigurować wystąpienie klastra trybu failover. 

Wybierz jedną z następujących przewodników, aby skonfigurować środowisko FCI odpowiednie dla Twojej firmy: 
- [Konfigurowanie FCI za pomocą usługi Azure Shared disks](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Konfigurowanie FCI z udziałem plików w warstwie Premium](failover-cluster-instance-premium-file-share-manually-configure.md)
- [Konfigurowanie FCI z Bezpośrednie miejsca do magazynowania](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

Aby dowiedzieć się więcej, zobacz Omówienie [FCI z SQL Server na maszynach wirtualnych platformy Azure](failover-cluster-instance-overview.md) i [obsługiwanych konfiguracjach HADR Cluster](hadr-cluster-best-practices.md). 

Aby uzyskać dodatkowe informacje, zobacz: 
- [Technologie klastrów systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server wystąpienia klastra trybu failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
