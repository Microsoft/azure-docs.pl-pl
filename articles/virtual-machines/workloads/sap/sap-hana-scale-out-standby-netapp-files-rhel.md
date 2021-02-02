---
title: SAP HANA skalowanie w poziomie przy użyciu funkcji wstrzymywania i Azure NetApp Files na RHEL | Microsoft Docs
description: Przewodnik wysokiej dostępności dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux z Azure NetApp Files dla aplikacji SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: radeltch
ms.openlocfilehash: 544847a06917d9cbe1413c678f471f51a10a9c58
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259003"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Wdróż system SAP HANA skalowalny w poziomie z aktywnym węzłem na maszynach wirtualnych platformy Azure przy użyciu Azure NetApp Files na Red Hat Enterprise Linux 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


W tym artykule opisano sposób wdrażania systemu SAP HANA o wysokiej dostępności w konfiguracji skalowalnej w poziomie w trybie wstrzymania w usłudze Azure Red Hat Enterprise Linux Virtual Machines (VM) przy użyciu [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) dla woluminów magazynu udostępnionego.  

W przykładowych konfiguracjach, poleceniach instalacji i tak dalej wystąpienie HANA ma wartość **03** , a identyfikator systemu Hana to **HN1**. Przykłady są oparte na platformie HANA 2,0 SP4 i Red Hat Enterprise Linux dla oprogramowania SAP 7,6. 

> [!NOTE]
> Ten artykuł zawiera odwołania do *głównego* terminu i serwera *podrzędnego*, warunki, które nie są już używane przez firmę Microsoft. Po usunięciu tych warunków z oprogramowania usuniemy je z tego artykułu.


Przed rozpoczęciem zapoznaj się z następującymi informacjami i dokumentami SAP:

* [Dokumentacja Azure NetApp Files][anf-azure-doc] 
* Uwaga dotycząca oprogramowania SAP [1928533] obejmuje:  
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure
* Test SAP [2015553]: zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure
* Uwaga SAP [2002167] ma zalecane ustawienia systemu operacyjnego dla Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2009879] SAP HANA wytycznych dotyczących Red Hat Enterprise Linux
* Test SAP [2178632]: zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure
* Test SAP [2191498]: zawiera wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure
* Test SAP [2243692]: zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure
* Uwaga dotycząca oprogramowania SAP [1999351]: zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla oprogramowania SAP
* Uwaga dotycząca oprogramowania SAP [1900823]: zawiera informacje o wymaganiach dotyczących magazynu SAP HANA
* [Strona typu wiki społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): zawiera wszystkie wymagane uwagi SAP dla systemu Linux
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* Ogólna dokumentacja RHEL
  * [Omówienie Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Informacje o wysokiej dostępności Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Przewodnik po sieci Red Hat Enterprise Linux](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Dokumentacja RHEL specyficzna dla platformy Azure:
  * [Zainstaluj SAP HANA na Red Hat Enterprise Linux do użycia w Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
* [NetApp aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]
* [Woluminy NFS 4.1 w usłudze Azure NetApp Files dla platformy SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-netapp)

## <a name="overview"></a>Omówienie

Jedną z metod osiągania wysokiej dostępności platformy HANA jest skonfigurowanie trybu failover dla hosta. Aby skonfigurować funkcję autoprzełączania do trybu failover hosta, należy dodać co najmniej jedną maszynę wirtualną do systemu HANA i skonfigurować je jako węzły w stanie wstrzymania. Gdy aktywny węzeł ulegnie awarii, węzeł w stanie wstrzymania zostanie automatycznie przełączone. W prezentowanej konfiguracji z maszynami wirtualnymi platformy Azure można uzyskać funkcję automatycznej pracy awaryjnej przy użyciu [systemu plików NFS na Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

> [!NOTE]
> Węzeł w stanie wstrzymania musi mieć dostęp do wszystkich woluminów bazy danych. Woluminy HANA muszą być zainstalowane jako woluminy NFSv4. Ulepszony mechanizm blokowania oparty na dzierżawach plików w protokole NFSv4 jest używany w przypadku `I/O` ogrodzenia. 

> [!IMPORTANT]
> Aby skompilować obsługiwaną konfigurację, należy wdrożyć dane platformy HANA i woluminy dzienników jako woluminy NFSv 4.1 i zainstalować je przy użyciu protokołu NFSv 4.1. Konfiguracja automatycznej pracy w trybie failover hosta HANA z węzłem wstrzymywania nie jest obsługiwana w przypadku NFSv3.

![Omówienie wysokiej dostępności SAP NetWeaver](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

Na powyższym diagramie, który następuje po SAP HANA zalecenia dotyczące sieci, trzy podsieci są reprezentowane w jednej sieci wirtualnej platformy Azure: 
* Do komunikacji z klientem
* Do komunikacji z systemem magazynu
* W przypadku wewnętrznej komunikacji między węzłami HANA

Woluminy usługi Azure NetApp znajdują się w osobnej podsieci [delegowanej do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

W tej przykładowej konfiguracji podsieci są następujące:  

  - `client` 10.9.1.0/26  
  - `storage` 10.9.3.0/26  
  - `hana` 10.9.2.0/26  
  - `anf` 10.9.0.0/26 (delegowana podsieć do Azure NetApp Files)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Konfigurowanie infrastruktury Azure NetApp Files 

Przed przystąpieniem do konfigurowania infrastruktury Azure NetApp Files zapoznaj się z [dokumentacją Azure NetApp Files][anf-azure-doc]. 

Azure NetApp Files jest dostępna w kilku [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Sprawdź, czy wybrany region platformy Azure oferuje Azure NetApp Files.  

Aby uzyskać informacje o dostępności Azure NetApp Files według regionów świadczenia usługi Azure, zobacz [Azure NetApp Files dostępność według regionów świadczenia usługi Azure][anf-avail-matrix].  

Przed wdrożeniem Azure NetApp Files Zażądaj dołączenia do Azure NetApp Files, przechodząc do [rejestracji w celu uzyskania instrukcji Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Wdrażanie zasobów Azure NetApp Files  

W poniższych instrukcjach przyjęto założenie, że [usługa Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)została już wdrożona. Azure NetApp Files zasoby i maszyny wirtualne, w których zostaną zainstalowane zasoby Azure NetApp Files, muszą być wdrożone w tej samej sieci wirtualnej platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure.  

1. Jeśli zasoby nie zostały jeszcze wdrożone, zażądaj dołączenia [do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).  

2. Utwórz konto NetApp w wybranym regionie świadczenia usługi Azure, postępując zgodnie z instrukcjami w temacie [Tworzenie konta NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).  

3. Skonfiguruj pulę pojemności Azure NetApp Files, postępując zgodnie z instrukcjami w temacie [konfigurowanie Azure NetApp Files puli pojemności](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  

   Architektura HANA przedstawiona w tym artykule używa pojedynczej puli pojemności Azure NetApp Files na poziomie *usługi Ultra Service* . W przypadku obciążeń HANA na platformie Azure zalecamy użycie [poziomu usługi](../../../azure-netapp-files/azure-netapp-files-service-levels.md)Azure NetApp Files *Ultra* lub *Premium* .  

4. Delegowanie podsieci do Azure NetApp Files, zgodnie z opisem w temacie [delegowanie podsieci do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Wdróż woluminy Azure NetApp Files, postępując zgodnie z instrukcjami w temacie [Tworzenie woluminu NFS dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  

   Gdy wdrażasz woluminy, pamiętaj o wybraniu wersji **nfsv 4.1** . Wdróż woluminy w wyznaczeniej [podsieci](/rest/api/virtualnetwork/subnets)Azure NetApp Files. Adresy IP woluminów NetApp platformy Azure są przypisywane automatycznie. 
   
   Należy pamiętać, że zasoby Azure NetApp Files i maszyny wirtualne platformy Azure muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure. Na przykład **HN1**-Data-Mnt00001, **HN1**-log-mnt00001, i tak dalej, są nazwami woluminów oraz NFS://10.9.0.4/**HN1**-Data-mnt00001, NFS://10.9.0.4/**HN1**-log-mnt00001 i tak dalej, są ścieżkami plików dla woluminów Azure NetApp Files.  

   * Volume **HN1**-Data-mnt00001 (NFS://10.9.0.4/**HN1**-Data-mnt00001)
   * Volume **HN1**-Data-mnt00002 (NFS://10.9.0.4/**HN1**-Data-mnt00002)
   * Volume **HN1**-log-mnt00001 (NFS://10.9.0.4/**HN1**-log-mnt00001)
   * Volume **HN1**-log-mnt00002 (NFS://10.9.0.4/**HN1**-log-mnt00002)
   * Volume **HN1**-shared (NFS://10.9.0.4/**HN1**-Shared)
   
   W tym przykładzie użyto oddzielnego woluminu Azure NetApp Files dla każdego woluminu danych i dziennika platformy HANA. Aby uzyskać bardziej zoptymalizowaną pod względem kosztów konfigurację w mniejszych lub nieproduktywnych systemach, można umieścić wszystkie instalacje danych na pojedynczym woluminie, a wszystkie dzienniki są instalowane na innym pojedynczym woluminie.  

### <a name="important-considerations"></a>Istotne zagadnienia

W miarę tworzenia SAP HANA Azure NetApp Files skalowanie w poziomie za pomocą scenariusza rozwiązaniu z węzłami należy pamiętać o następujących kwestiach:

- Minimalna Pula pojemności to 4 tebibajtów (TiB).  
- Minimalny rozmiar woluminu to 100 gibibajtach (GiB).
- Azure NetApp Files i wszystkie maszyny wirtualne, na których zostaną zainstalowane woluminy Azure NetApp Files, muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w [równorzędnych sieciach wirtualnych](../../../virtual-network/virtual-network-peering-overview.md) w tym samym regionie.  
- Wybrana Sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files.
- Przepływność woluminu Azure NetApp Files jest funkcją limitu przydziału woluminu i poziomu usługi, zgodnie z opisem w obszarze [poziom usług dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Gdy zmieniasz rozmiar woluminów NetApp platformy Azure, upewnij się, że przepływność spełnia wymagania systemowe HANA.  
- Za pomocą [zasad eksportu](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)Azure NetApp Files można kontrolować dozwolonych klientów, typ dostępu (odczyt i zapis, tylko do odczytu itd.). 
- Funkcja Azure NetApp Files nie jest jeszcze dostępna dla strefy. Obecnie funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie świadczenia usługi Azure. Weź pod uwagę potencjalne konsekwencje opóźnienia w niektórych regionach świadczenia usługi Azure.  

> [!IMPORTANT]
> W przypadku obciążeń SAP HANA małe opóźnienia są krytyczne. Skontaktuj się z przedstawicielem firmy Microsoft, aby upewnić się, że maszyny wirtualne i woluminy Azure NetApp Files są wdrożone w bliskiej bliskości.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Ustalanie wielkości dla bazy danych HANA na Azure NetApp Files

Przepływność woluminu Azure NetApp Files jest funkcją rozmiaru woluminu i poziomu usługi, zgodnie z opisem w obszarze [poziom usług dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Podczas projektowania infrastruktury dla oprogramowania SAP na platformie Azure należy pamiętać o minimalnych wymaganiach dotyczących magazynu przez oprogramowanie SAP, które przekładają się na minimalne charakterystyki przepływności:

- Do odczytu i zapisu na/Hana/log z 250 megabajtów na sekundę (MB/s) z rozmiarami we/wy 1 MB.  
- Działanie odczytu o rozmiarze co najmniej 400 MB/s dla/Hana/Data dla 16 MB i 64 MB pamięci we/wy.  
- Działanie zapisu o rozmiarze co najmniej 250 MB/s dla/Hana/Data z 16 MB i 64 MB. 

[Limity przepływności Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) na 1 TIB przydziału woluminu są następujące:
- Warstwa Premium Storage-64 MiB/s  
- Warstwa Ultra Storage — 128 MiB/s  

W celu spełnienia minimalnych wymagań dotyczących przepływności SAP dla danych i dziennika oraz wytycznych dla/Hana/Shared zaleca się następujące rozmiary:

| Wolumin | Rozmiar<br>Warstwa Premium Storage | Rozmiar<br>Warstwa Ultra Storage | Obsługiwany protokół NFS |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | 1xRAM na 4 węzły procesu roboczego | 1xRAM na 4 węzły procesu roboczego | v3 lub v 4.1 |

SAP HANA konfiguracja układu przedstawionego w tym artykule przy użyciu warstwy Azure NetApp Files Ultra Storage:

| Wolumin | Rozmiar<br>Warstwa Ultra Storage | Obsługiwany protokół NFS |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v 4.1 |
| /hana/shared | 2 TiB | v3 lub v 4.1 |

> [!NOTE]
> Zalecenia dotyczące ustalania wielkości Azure NetApp Files podane tutaj są celem spełnienia minimalnych wymagań zalecanych przez system SAP dla dostawców infrastruktury. W rzeczywistych wdrożeniach klientów i scenariuszach obciążeń te rozmiary mogą nie być wystarczające. Użyj tych zaleceń jako punktu wyjścia i adaptacji na podstawie wymagań określonego obciążenia.  

> [!TIP]
> Można dynamicznie zmieniać rozmiar Azure NetApp Files woluminów, bez konieczności *odinstalowywania* woluminów, zatrzymywania maszyn wirtualnych lub zatrzymywania SAP HANA. Takie podejście pozwala elastycznie spełnić zarówno oczekiwane, jak i nieprzewidziane wymagania dotyczące przepływności aplikacji.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Wdrażanie maszyn wirtualnych z systemem Linux za pomocą Azure Portal

Najpierw należy utworzyć woluminy Azure NetApp Files. Następnie wykonaj następujące czynności:
1. Utwórz [podsieci sieci wirtualnej platformy Azure](../../../virtual-network/virtual-network-manage-subnet.md) w [sieci wirtualnej platformy Azure](../../../virtual-network/virtual-networks-overview.md). 
1. Wdróż maszyny wirtualne. 
1. Utwórz dodatkowe interfejsy sieciowe i Dołącz interfejsy sieciowe do odpowiednich maszyn wirtualnych.  

   Każda maszyna wirtualna ma trzy interfejsy sieciowe odpowiadające trzem podsieciom sieci wirtualnej platformy Azure ( `client` `storage` i `hana` ). 

   Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure z wieloma kartami interfejsu sieciowego](../../linux/multiple-nics.md).  

> [!IMPORTANT]
> W przypadku obciążeń SAP HANA małe opóźnienia są krytyczne. Aby osiągnąć małe opóźnienia, skontaktuj się z przedstawicielem firmy Microsoft, aby upewnić się, że maszyny wirtualne i woluminy Azure NetApp Files są wdrożone w bliskiej bliskości. Podczas dołączania [nowego systemu SAP HANA](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) korzystającego z usługi SAP HANA Azure NetApp Files należy przesłać niezbędne informacje. 
 
W następnych instrukcjach przyjęto założenie, że utworzono już grupę zasobów, sieć wirtualną platformy Azure oraz trzy podsieci sieci wirtualnej platformy Azure `client` : `storage` i `hana` . Podczas wdrażania maszyn wirtualnych wybierz podsieć klienta, aby interfejs sieciowy klienta był interfejsem podstawowym na maszynach wirtualnych. Należy również skonfigurować jawną trasę do Azure NetApp Files delegowanej podsieci za pośrednictwem bramy podsieci magazynu. 

> [!IMPORTANT]
> Upewnij się, że wybrany system operacyjny to SAP-Certified for SAP HANA na określonych typach maszyn wirtualnych, których używasz. Aby uzyskać listę SAP HANA certyfikowane typy maszyn wirtualnych i wersje systemu operacyjnego dla tych typów, przejdź do witryny [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Kliknij Szczegóły wymienionego typu maszyny wirtualnej, aby uzyskać pełną listę obsługiwanych przez SAP HANA wydań systemu operacyjnego dla tego typu.  

1. Utwórz zestaw dostępności dla SAP HANA. Upewnij się, że ustawiono maksymalną domenę aktualizacji.  

2. Utwórz trzy maszyny wirtualne (**hanadb1**, **hanadb2**, **hanadb3**), wykonując następujące czynności:  

   a. Użyj Red Hat Enterprise Linux obrazu w galerii platformy Azure, który jest obsługiwany przez SAP HANA. W tym przykładzie użyto obrazu RHEL-SAP-HA 7,6.  

   b. Wybierz zestaw dostępności utworzony wcześniej dla SAP HANA.  

   c. Wybierz podsieć sieci wirtualnej platformy Azure. Wybierz opcję [przyspieszone sieci](../../../virtual-network/create-vm-accelerated-networking-cli.md).  

   Podczas wdrażania maszyn wirtualnych nazwa interfejsu sieciowego jest generowana automatycznie. W tych instrukcjach dla uproszczenia będziemy odnosić się do automatycznie generowanych interfejsów sieciowych dołączonych do podsieci sieci wirtualnej klienta platformy Azure, jako **hanadb1-Client**, **hanadb2-Client** i **hanadb3-Client**. 

3. Utwórz trzy interfejsy sieciowe — jeden dla każdej maszyny wirtualnej dla `storage` podsieci sieci wirtualnej (w tym przykładzie **hanadb1-Storage**, **hanadb2-** Storage i **hanadb3-Storage**).  

4. Utwórz trzy interfejsy sieciowe — jeden dla każdej maszyny wirtualnej dla `hana`  podsieci sieci wirtualnej (w tym przykładzie **hanadb1-Hana**, **hanadb2-Hana** i **hanadb3-Hana**).  

5. Dołącz nowo utworzone interfejsy sieci wirtualnej do odpowiednich maszyn wirtualnych, wykonując następujące czynności:  

    a. Przejdź do maszyny wirtualnej w [Azure Portal](https://portal.azure.com/#home).  

    b. W lewym okienku wybierz pozycję **Virtual Machines**. Odfiltruj nazwę maszyny wirtualnej (na przykład **hanadb1**), a następnie wybierz maszynę wirtualną.  

    c. W okienku **Przegląd** wybierz pozycję **Zatrzymaj** , aby cofnąć przydział maszyny wirtualnej.  

    d. Wybierz pozycję **Sieć**, a następnie Dołącz interfejs sieciowy. Z listy rozwijanej **Dołącz interfejs sieciowy** wybierz już utworzone interfejsy sieciowe dla `storage` `hana` podsieci i.  
    
    e. Wybierz pozycję **Zapisz**. 
 
    f. Powtórz kroki od b do e dla pozostałych maszyn wirtualnych (w naszym przykładzie  **hanadb2** i **hanadb3**).
 
    przykład Pozostaw teraz maszyny wirtualne w stanie zatrzymania. Następnie włączysz [przyspieszoną sieć](../../../virtual-network/create-vm-accelerated-networking-cli.md) dla wszystkich nowo dołączonych interfejsów sieciowych.  

6. Włącz przyspieszone sieci dla dodatkowych interfejsów sieciowych dla `storage` podsieci i, `hana` wykonując następujące czynności:  

    a. Otwórz [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) w [Azure Portal](https://portal.azure.com/#home).  

    b. Wykonaj następujące polecenia, aby włączyć przyspieszone sieci dla dodatkowych interfejsów sieciowych dołączonych do `storage` `hana` podsieci i.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Uruchom maszyny wirtualne, wykonując następujące czynności:  

    a. W lewym okienku wybierz pozycję **Virtual Machines**. Odfiltruj nazwę maszyny wirtualnej (na przykład **hanadb1**), a następnie wybierz ją.  

    b. W okienku **Przegląd** wybierz pozycję **Rozpocznij**.  

## <a name="operating-system-configuration-and-preparation"></a>Konfiguracja i przygotowanie systemu operacyjnego

Instrukcje w następnych sekcjach są poprzedzone jedną z następujących czynności:
* **[A]**: dotyczy wszystkich węzłów
* **[1]**: dotyczy tylko węzła 1
* **[2]**: dotyczy tylko węzła 2
* **[3]**: dotyczy tylko węzła 3

Skonfiguruj i przygotuj system operacyjny, wykonując następujące czynności:

1. **[A]** przechowywanie plików hosta na maszynach wirtualnych. Uwzględnij wpisy dla wszystkich podsieci. Do `/etc/hosts` tego przykładu dodano następujące wpisy.  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** Dodaj trasę sieciową, aby komunikacja do Azure NetApp Files odbywała się za pośrednictwem interfejsu sieciowego magazynu.  

   W tym przykładzie zostanie użyta `Networkmanager` Konfiguracja dodatkowej trasy sieciowej. W poniższych instrukcjach przyjęto założenie, że interfejs sieciowy magazynu to `eth1` .  
   Najpierw Ustal nazwę połączenia dla urządzenia `eth1` . W tym przykładzie nazwa połączenia dla urządzenia `eth1` to `Wired connection 1` .  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Następnie skonfiguruj dodatkową trasę do Azure NetApp Files delegowanej sieci za pośrednictwem programu `eth1` .  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Aby aktywować zmiany, uruchom ponownie maszynę wirtualną.  

3. **[A]** Zainstaluj pakiet klienta systemu plików NFS.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** Przygotuj system operacyjny do uruchamiania SAP HANA na platformie Azure NetApp z systemem plików NFS, zgodnie z opisem w [NetApp aplikacji SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]. Utwórz plik konfiguracji */etc/sysctl.d/NetApp-Hana.conf* dla ustawień konfiguracji NetApp.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 
    net.ipv4.tcp_slow_start_after_idle=0 
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Utwórz plik konfiguracji */etc/sysctl.d/MS-AZ.conf* z dodatkowymi ustawieniami optymalizacji.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

> [!TIP]
> Należy unikać ustawiania net.ipv4.ip_local_port_range i net.ipv4.ip_local_reserved_ports jawnie w plikach konfiguracji sysctl, aby umożliwić agentowi hosta SAP zarządzanie zakresami portów. Aby uzyskać więcej informacji, zobacz temat SAP Uwaga [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

5. **[A]** Dostosuj ustawienia sunrpc zgodnie z zaleceniami w [aplikacjach NetApp SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** konfiguracja Red Hat dla platformy Hana.

    Skonfiguruj RHEL zgodnie z opisem w temacie SAP Note [2292690], [2455582], [2593824] i <https://access.redhat.com/solutions/2447641> .

    > [!NOTE]
    > W przypadku instalowania platformy HANA 2,0 SP04 wymagane jest zainstalowanie pakietu `compat-sap-c++-7` zgodnie z opisem w artykule SAP uwaga [2593824], zanim będzie można zainstalować SAP HANA. 

## <a name="mount-the-azure-netapp-files-volumes"></a>Zainstaluj woluminy Azure NetApp Files

1. **[A]** Utwórz punkty instalacji dla woluminów bazy danych Hana.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** utwórz katalogi specyficzne dla węzła dla/usr/SAP na **HN1**— udostępnione.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Sprawdź ustawienie domeny NFS. Upewnij się, że domena jest skonfigurowana jako domyślna domena Azure NetApp Files, tj., **`defaultv4iddomain.com`** a mapowanie jest ustawione na wartość **nikt**.  

    > [!IMPORTANT]
    > Upewnij się, że na maszynie wirtualnej ustawiono domenę systemu plików NFS `/etc/idmapd.conf` zgodną z domyślną konfiguracją domeny na Azure NetApp Files: **`defaultv4iddomain.com`** . Jeśli istnieje niezgodność między konfiguracją domeny na kliencie NFS (tj. maszyną wirtualną) a serwerem NFS, tj. konfiguracją usługi Azure NetApp, wówczas uprawnienia do plików na woluminach NetApp platformy Azure, które są zainstalowane na maszynach wirtualnych, będą wyświetlane jako `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** Sprawdź poprawność `nfs4_disable_idmapping` . Powinna być ustawiona na wartość **Y**. Aby utworzyć strukturę katalogów, w której `nfs4_disable_idmapping` znajduje się, wykonaj polecenie instalacji. Nie będzie można ręcznie utworzyć katalogu w obszarze/sys/modules, ponieważ dostęp jest zarezerwowany dla jądra/sterowników.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Aby uzyskać więcej informacji na temat zmiany `nfs4_disable_idmapping` parametru, zobacz https://access.redhat.com/solutions/1749883 .

6. **[A]** Zainstaluj udostępnione woluminy Azure NetApp Files.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** Zainstaluj woluminy specyficzne dla węzła w **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Zainstaluj woluminy specyficzne dla węzła w **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Zainstaluj woluminy specyficzne dla węzła w **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Sprawdź, czy wszystkie woluminy Hana są zainstalowane przy użyciu protokołu NFS w wersji **NFSv4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>Instalacja  

W tym przykładzie w celu wdrożenia SAP HANA w konfiguracji skalowania w poziomie za pomocą węzła w stanie wstrzymania z platformą Azure użyto platformy HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Przygotowanie do instalacji platformy HANA

1. **[A]** przed instalacją Hana Ustaw hasło główne. Hasło główne można wyłączyć po zakończeniu instalacji. Uruchom jako `root` polecenie `passwd` .  

2. **[1]** Sprawdź, czy możesz zalogować się za pośrednictwem protokołu SSH do **hanadb2** i **hanadb3** bez monitowania o hasło.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** zainstaluj dodatkowe pakiety, które są wymagane dla platformy Hana 2,0 SP4. Aby uzyskać więcej informacji, zobacz temat SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Zmień własność SAP HANA `data` i `log` katalogów na **hn1** adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** Wyłącz zaporę tymczasowo, aby nie zakłócać instalacji platformy Hana. Po zakończeniu instalacji platformy HANA można ją ponownie włączyć. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>Instalacja platformy HANA

1. **[1]** Zainstaluj SAP HANA, postępując zgodnie z instrukcjami w [przewodniku instalacji i aktualizacji SAP HANA 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). W tym przykładzie instalujemy SAP HANA skalowanie w poziomie za pomocą wzorca, jednego procesu roboczego i jednego węzła w stanie wstrzymania.  

   a. Uruchom program **hdblcm** z katalogu oprogramowania instalacyjnego platformy Hana. Użyj `internal_network` parametru i przekaż przestrzeń adresową dla podsieci, która jest używana dla wewnętrznej komunikacji między węzłami Hana.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. W wierszu polecenia wprowadź następujące wartości:

     * Dla **opcji wybierz akcję**: wprowadź **1** (na potrzeby instalacji)
     * Aby uzyskać **dodatkowe składniki do instalacji**: wprowadź **2, 3**
     * Ścieżka instalacji: naciśnij klawisz ENTER (wartość domyślna to/Hana/Shared)
     * **Nazwa hosta lokalnego**: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * W obszarze **czy chcesz dodać hosty do systemu?**: wprowadź wartość **y**
     * Aby **dodać nazwy hostów rozdzielane przecinkami**: wpisz **hanadb2, hanadb3**
     * Dla **nazwy użytkownika głównego** [root]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * W przypadku ról dla hosta hanadb2: wprowadź **1**  (dla procesu roboczego)
     * Dla **grupy hostów trybu failover hosta** hanadb2 [domyślnie]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * **Numer partycji magazynu** dla hanadb2 hosta [<<assign automatically>>]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Dla **grupy procesów roboczych** dla hosta hanadb2 [domyślnie]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Dla **opcji wybierz role** dla hosta hanadb3: wprowadź **2** (na potrzeby wstrzymania)
     * Dla **grupy hostów trybu failover hosta** hanadb3 [domyślnie]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Dla **grupy procesów roboczych** dla hosta hanadb3 [domyślnie]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Dla **identyfikatora System SAP HANA**: wprowadź **HN1**
     * Dla **wystąpienia o numerze** [00]: wprowadź wartość **03**
     * Dla **grupy procesów roboczych hosta lokalnego** [domyślnie]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Dla **opcji wybierz użycie systemu/wprowadź indeks [4]**: wprowadź **4** (dla niestandardowych)
     * W przypadku **lokalizacji woluminów danych** [/Hana/Data/HN1]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * **Lokalizacja dzienników woluminów dziennika** [/Hana/log/HN1]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Aby **ograniczyć maksymalną alokację pamięci?** [n]: wprowadź **n**
     * **Nazwa hosta certyfikatu dla hosta hanadb1** [hanadb1]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * **Nazwa hosta certyfikatu dla hosta hanadb2** [hanadb2]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * **Nazwa hosta certyfikatu dla hosta hanadb3** [hanadb3]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * W polu **hasło administratora systemu (hn1adm)**: wprowadź hasło
     * Dla **systemu systemowej bazy danych hasło użytkownika (system)**: wprowadź hasło systemu
     * Potwierdź hasło użytkownika systemowej **bazy danych (system)**: wprowadź hasło systemu
     * **Czy po ponownym uruchomieniu komputera należy ponownie uruchomić system?** [n]: wprowadź **n** 
     * Na **pewno chcesz kontynuować (t/n)**: Sprawdź poprawność podsumowania i jeśli wszystko wygląda dobrze, wprowadź wartość **y**


2. **[1]** Sprawdź global.ini  

   Wyświetl global.ini i upewnij się, że konfiguracja wewnętrznego SAP HANA komunikacji między węzłami jest na miejscu. Sprawdź sekcję **komunikacja** . Powinna mieć przestrzeń adresową dla `hana` podsieci i `listeninterface` powinna być ustawiona na wartość `.internal` . Sprawdź sekcję **internal_hostname_resolution** . Powinny mieć adresy IP dla maszyn wirtualnych HANA należących do `hana` podsieci.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Dodaj mapowanie hosta, aby upewnić się, że adresy IP klientów są używane do komunikacji z klientem. Dodaj sekcję `public_host_resolution` i Dodaj odpowiednie adresy IP z podsieci klienta.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** ponownie uruchom SAP HANA, aby aktywować zmiany.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Sprawdź, czy interfejs klienta będzie używać adresów IP z `client` podsieci do komunikacji.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Aby uzyskać informacje na temat weryfikowania konfiguracji, zobacz artykuł SAP Uwaga [2183363-konfiguracja sieci wewnętrznej SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

5. **[A]** ponownie Włącz zaporę.  
   - Zatrzymaj platformę HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - Włącz ponownie zaporę
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - Otwórz wymagane porty zapory

       > [!IMPORTANT]
       > Utwórz reguły zapory, aby umożliwić komunikację między węzłami platformy HANA i ruchem klientów. Wymagane porty są wymienione w [portach TCP/IP wszystkich produktów SAP](https://help.sap.com/viewer/ports). Poniższe polecenia są tylko przykładem. W tym scenariuszu z użytym numerem systemu 03.

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - Uruchom platformę HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Aby zoptymalizować SAP HANA dla magazynu bazowego Azure NetApp Files, ustaw następujące parametry SAP HANA:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**na**
   - `async_write_submit_active`**na**
   - `async_write_submit_blocks`**wszystkie**

   Aby uzyskać więcej informacji, zobacz [NetApp aplikacji SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]. 

   Począwszy od systemów SAP HANA 2,0, można ustawić parametry w `global.ini` . Aby uzyskać więcej informacji, zobacz temat SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   W przypadku systemów SAP HANA 1,0 i wcześniejszych te parametry można ustawić podczas instalacji, zgodnie z opisem w artykule SAP Uwaga [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Magazyn używany przez Azure NetApp Files ma ograniczenie rozmiaru pliku wynoszący 16 terabajtów (TB). SAP HANA nie rozpoznaje niejawnie ograniczenia dotyczącego magazynu i nie utworzy automatycznie nowego pliku danych, gdy zostanie osiągnięty limit rozmiaru pliku wynoszący 16 TB. Ponieważ SAP HANA próbuje zwiększyć plik poza 16 TB, próba spowoduje błędy i ostatecznie w przypadku awarii serwera indeksów. 

   > [!IMPORTANT]
   > Aby zapobiec próbie zwiększenia przez SAP HANA plików danych przekraczających [limit 16 TB](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) podsystemu magazynowania, ustaw następujące parametry w `global.ini` .  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 Aby uzyskać więcej informacji, zobacz temat SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Należy pamiętać o zaistnieniu programu SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Testowanie pracy w trybie failover SAP HANA 

1. Symuluj awarię węzła w węźle procesu roboczego SAP HANA. Wykonaj następujące czynności: 

   a. Przed zasymulowaniem awarii węzła Uruchom następujące polecenia jako **hn1** adm, aby przechwycić stan środowiska:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Aby symulować awarię węzła, uruchom następujące polecenie jako element główny w węźle procesu roboczego, który jest **hanadb2** w tym przypadku:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Monitoruj system do ukończenia pracy w trybie failover. Po zakończeniu pracy w trybie failover Przechwyć stan, który powinien wyglądać następująco:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Gdy węzeł awaryjnego jądra, należy unikać opóźnień w trybie failover SAP HANA przez ustawienie `kernel.panic` na 20 sekund na *wszystkich* maszynach wirtualnych Hana. Konfiguracja odbywa się w programie `/etc/sysctl` . Uruchom ponownie maszyny wirtualne, aby aktywować zmianę. Jeśli ta zmiana nie zostanie wykonana, przejście w tryb failover może potrwać co najmniej 10 minut, gdy w węźle występuje awaryjnego jądra.  

2. Aby skasować serwer nazw, wykonaj następujące czynności:

   a. Przed testami Sprawdź stan środowiska, uruchamiając następujące polecenia jako **hn1** adm:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. Uruchom następujące polecenia jako **hn1** adm w aktywnym węźle głównym, który jest **hanadb1** w tym przypadku:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    **Hanadb3** węzła w stanie wstrzymania przejdzie jako węzeł główny. Poniżej znajduje się stan zasobu po zakończeniu testu trybu failover:  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Uruchom ponownie wystąpienie platformy HANA w systemie **hanadb1** (czyli na tej samej maszynie wirtualnej, na której serwer nazw został zamknięty). Węzeł **hanadb1** ponownie przywróci środowisko i zachowa jego rolę w stanie wstrzymania.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Po rozpoczęciu SAP HANA na **hanadb1** oczekiwany jest następujący stan:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Ponownie, Kasuj serwer nazw na aktualnie aktywnym węźle głównym (czyli w węźle **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Węzeł **hanadb1** wznowi rolę węzła głównego. Po zakończeniu testu trybu failover stan będzie wyglądać następująco:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Rozpocznij SAP HANA w **hanadb3**, który będzie gotowy do pełnienia rolę węzła w stanie gotowości.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Po rozpoczęciu SAP HANA **hanadb3** stan będzie wyglądać następująco:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* [Woluminy NFS 4.1 w usłudze Azure NetApp Files dla platformy SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-netapp)
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines][sap-hana-ha].
