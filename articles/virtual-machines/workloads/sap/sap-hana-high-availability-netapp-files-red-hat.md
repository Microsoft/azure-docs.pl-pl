---
title: Wysoka dostępność aplikacji SAP HANA skalowanie w górę za pomocą anf w RHEL | Microsoft Docs
description: Ustanów wysoką dostępność SAP HANA anf na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 774344c4215088482b110de91f8951bae4a41d25
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365828"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Wysoka dostępność skalowania SAP HANA w górę dzięki Azure NetApp Files na Red Hat Enterprise Linux

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

W tym artykule opisano sposób konfigurowania replikacji systemu SAP HANA we wdrożeniu skalowania w górę, gdy systemy plików HANA są instalowane za pośrednictwem systemu plików NFS przy użyciu Azure NetApp Files (ANF). W przykładowych konfiguracjach i poleceniach instalacji są używane numer wystąpienia **03** i identyfikator systemu HANA **HN1.** SAP HANA Replikacja składa się z jednego węzła podstawowego i co najmniej jednego węzła pomocniczego.

Gdy kroki w tym dokumencie są oznaczone następującymi prefiksami, znaczenie jest następujące:

- **[A]**: Krok ma zastosowanie do wszystkich węzłów
- **[1]**: Krok dotyczy tylko węzła node1
- **[2]**: Krok dotyczy tylko węzła node2
 
Najpierw zapoznaj się z następującymi uwagami i dokumentami SAP:

- Uwaga [SAP 1928533,](https://launchpad.support.sap.com/#/notes/1928533)która ma:
    - Lista rozmiarów maszyn wirtualnych platformy Azure obsługiwanych w przypadku wdrażania oprogramowania SAP.
    - Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure.
    - Obsługiwane kombinacje oprogramowania SAP, systemu operacyjnego i bazy danych.
    - Wymagana wersja jądra SAP dla systemów Windows i Linux na Microsoft Azure.
- Uwaga sap [2015553](https://launchpad.support.sap.com/#/notes/2015553) zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
- Uwaga [SAP 405827](https://launchpad.support.sap.com/#/notes/405827) zawiera listę zalecanych systemów plików dla środowiska HANA.
- Uwaga SAP [2002167](https://launchpad.support.sap.com/#/notes/2002167) ma zalecane ustawienia systemu operacyjnego dla Red Hat Enterprise Linux.
- Uwaga sap [2009879](https://launchpad.support.sap.com/#/notes/2009879) SAP HANA wytyczne dotyczące Red Hat Enterprise Linux.
- Uwaga [sap 2178632](https://launchpad.support.sap.com/#/notes/2178632) zawiera szczegółowe informacje o wszystkich metrykach monitorowania zgłoszonych dla oprogramowania SAP na platformie Azure.
- Uwaga SAP [2191498](https://launchpad.support.sap.com/#/notes/2191498) ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
- Uwaga sap [2243692 zawiera](https://launchpad.support.sap.com/#/notes/2243692) informacje o licencjonowaniu sap w systemie Linux na platformie Azure.
- Uwaga [sap 1999351](https://launchpad.support.sap.com/#/notes/1999351) zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzonym rozszerzeniem monitorowania platformy Azure dla oprogramowania SAP.
- [Strona wiki sap Community zawiera](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) wszystkie wymagane uwagi dotyczące oprogramowania SAP dla systemu Linux.
- [Planowanie i Virtual Machines wdrożenia oprogramowania SAP w systemie Linux na platformie Azure][planning-guide]
- [Wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][deployment-guide]
- [Wdrażanie usługi Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
- [SAP HANA replikacji systemu w klastrze pacemaker.](https://access.redhat.com/articles/3004101)
- Ogólna dokumentacja systemu RHEL
    - [Omówienie Add-On dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Wysoka dostępność Add-On administracyjnej.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Informacje o Add-On wysokiej dostępności.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [Konfigurowanie SAP HANA replikacji systemu Scale-Up klastrze Pacemaker, gdy systemy plików HANA znajdują się w udziałach NFS](https://access.redhat.com/solutions/5156571)
- Dokumentacja systemu RHEL specyficzna dla platformy Azure:
    - [Zasady obsługi klastrów wysokiej dostępności RHEL — Microsoft Azure Virtual Machines jako elementy członkowskie klastra.](https://access.redhat.com/articles/3131341)
    - [Instalowanie i konfigurowanie klastra Red Hat Enterprise Linux 7.4 (lub nowszego) High-Availability klastrze Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Zainstaluj SAP HANA na Red Hat Enterprise Linux do użytku w programie Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [Konfigurowanie SAP HANA skalowania w górę klastra Pacemaker, gdy systemy plików HANA znajdują się w udziałach NFS](https://access.redhat.com/solutions/5156571)
- [Aplikacje NETApp SAP na Microsoft Azure przy użyciu Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
- [Woluminy NFS 4.1 w usłudze Azure NetApp Files dla platformy SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Omówienie

Tradycyjnie w środowisku skalowania w górę wszystkie systemy plików SAP HANA instalowane z magazynu lokalnego. Konfigurowanie wysokiej dostępności replikacji systemu SAP HANA na Red Hat Enterprise Linux jest publikowane w przewodniku Konfigurowanie SAP HANA replikacji systemu w [systemie RHEL](./sap-hana-high-availability-rhel.md)

Aby zapewnić wysoką dostępność systemu skalowania w górę w udziałach systemu plików NFS systemu [Azure NetApp Files,](../../../azure-netapp-files/index.yml) potrzebujemy dodatkowej konfiguracji zasobów w klastrze, aby zasoby platformy HANA zostały odzyskane, gdy jeden węzeł utraci dostęp do udziałów NFS w systemie ANF. SAP HANA  Klaster zarządza instalacji systemu plików NFS, umożliwiając mu monitorowanie kondycji zasobów. Zależności między instalacji systemu plików i zasobów SAP HANA są wymuszane.  

![SAP HANA wysokiej wysokiej jakości w górę w anf](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA plików są instalowane w udziałach NFS przy użyciu Azure NetApp Files w każdym węźle. Systemy plików /hana/data, /hana/log i /hana/shared są unikatowe dla każdego węzła. 

Zainstalowane na węźle node1 **(hanadb1**)

- 10.32.2.4:/**hanadb1**-data-mnt00001 na /hana/data
- 10.32.2.4:/**hanadb1**-log-mnt00001 na /hana/log
- 10.32.2.4:/**hanadb1**-shared-mnt00001 on /hana/shared

Zainstalowane w węźle node2 (**hanadb2**)

- 10.32.2.4:/**hanadb2**-data-mnt00001 na /hana/data
- 10.32.2.4:/**hanadb2**-log-mnt00001 na /hana/log
- 10.32.2.4:/**hanadb2**-shared-mnt00001 on /hana/shared

> [!NOTE]
> Systemy plików /hana/shared, /hana/data i /hana/log nie są współdzielone między dwoma węzłami. Każdy węzeł klastra ma własne, oddzielne systemy plików.   

Konfiguracja SAP HANA replikacji systemu korzysta z dedykowanej wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure do korzystania z wirtualnego adresu IP jest wymagany usługa równoważenia obciążenia. Na poniższej liście przedstawiono konfigurację usługi równoważenia obciążenia:

- Konfiguracja frontonu: adres IP 10.32.0.10 dla hn1-db
- Konfiguracja serwera końcowego: połączone z podstawowymi interfejsami sieciowym wszystkich maszyn wirtualnych, które powinny być częścią replikacji systemu HANA
- Port sondy: port 62503
- Reguły równoważenia obciążenia: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (w przypadku korzystania z podstawowego usługi Azure Load Balancer)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Konfigurowanie infrastruktury usługi Azure NetApp File

Zanim przejdziesz do skonfigurowania infrastruktury Azure NetApp Files, zapoznaj się z dokumentacją usługi Azure [NetApp Files.](../../../azure-netapp-files/index.yml)

Azure NetApp Files jest dostępna w kilku regionach [świadczenia usługi Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Sprawdź, czy wybrany region świadczenia usługi Azure oferuje Azure NetApp Files.

Aby uzyskać informacje o dostępności usługi Azure NetApp Files według regionu świadczenia usługi Azure, [zobacz Azure NetApp Files Dostępność według regionu świadczenia usługi Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)

Przed wdrożeniem aplikacji Azure NetApp Files do usługi Azure NetApp Files, przechodząc do tematu [Rejestrowanie, aby Azure NetApp Files instrukcje.](../../../azure-netapp-files/azure-netapp-files-register.md)

### <a name="deploy-azure-netapp-files-resources"></a>Wdrażanie Azure NetApp Files zasobów

W poniższych instrukcjach przyjęto założenie, że sieć wirtualna platformy Azure została [już wdrożona.](../../../virtual-network/virtual-networks-overview.md) Zasoby Azure NetApp Files i maszyny wirtualne, na których zostaną zainstalowane zasoby usługi Azure NetApp Files, muszą być wdrożone w tej samej sieci wirtualnej platformy Azure lub w wirtualnych sieciach równorzędnych platformy Azure.

1. Jeśli zasoby nie zostały jeszcze wdrożone, [zażądaj](../../../azure-netapp-files/azure-netapp-files-register.md)do Azure NetApp Files .

2. Utwórz konto usługi NetApp w wybranym regionie platformy Azure, zgodnie z instrukcjami w tece [Tworzenie konta usługi NetApp.](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)

3.  Skonfiguruj pulę Azure NetApp Files pojemności, zgodnie z instrukcjami w te Azure NetApp Files [puli pojemności.](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)

    Architektura platformy HANA przedstawiona w tym artykule używa jednej Azure NetApp Files pojemności na poziomie usługi *Ultra.* W przypadku obciążeń HANA na platformie Azure zalecamy użycie usługi Azure NetApp Files *Ultra* lub *Premium.* [](../../../azure-netapp-files/azure-netapp-files-service-levels.md)

4.  Deleguj podsieć do Azure NetApp Files, zgodnie z opisem w instrukcjach w tece [Delegowanie](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)podsieci do Azure NetApp Files .

5.  Wd Azure NetApp Files woluminów sieciowych, zgodnie z instrukcjami w tece [Create an NFS volume for Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)( Tworzenie woluminu NFS dla Azure NetApp Files .

    Podczas wdrażania woluminów należy wybrać wersję NFSv4.1. Wd wdrażaj woluminy w wyznaczonej Azure NetApp Files podsieci. Adresy IP woluminów usługi Azure NetApp są przypisywane automatycznie.

    Należy pamiętać, że zasoby Azure NetApp Files i maszyny wirtualne platformy Azure muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w wirtualnych sieciach równorzędnych platformy Azure. Na przykład hanadb1-data-mnt00001, hanadb1-log-mnt00001 i tak dalej to nazwy woluminów i nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 i tak dalej, są ścieżkami plików dla woluminów Azure NetApp Files woluminów.
    
    W **bazie danych hanadb1**

    - Wolumin hanadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Wolumin hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Wolumin hanadb1-shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    W **bazie danych hanadb2**

    - Wolumin hanadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Wolumin hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Wolumin hanadb2-shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Istotne zagadnienia

Podczas tworzenia aplikacji Azure NetApp Files dla SAP HANA skalowania w górę należy pamiętać o następujących względach:

- Minimalna pula pojemności to 4 tebibajty (TiB).
- Minimalny rozmiar woluminu to 100 gibibajtów (GiB).
- Azure NetApp Files i wszystkie maszyny wirtualne, na których zostaną zainstalowane woluminy Azure NetApp Files, muszą [](../../../virtual-network/virtual-network-peering-overview.md) znajdować się w tej samej sieci wirtualnej platformy Azure lub w wirtualnych sieciach równorzędnych w tym samym regionie.
- Wybrana sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files.
- Przepływność woluminu Azure NetApp Files jest funkcją limitu przydziału woluminu i poziomu usługi, zgodnie z dokumentem Poziom usługi dla Azure NetApp Files [.](../../../azure-netapp-files/azure-netapp-files-service-levels.md) Podczas zmiany rozmiaru woluminów platformy AZURE NetApp platformy HANA upewnij się, że wynikowa przepływność spełnia wymagania systemowe platformy HANA.
- Za pomocą Azure NetApp Files [eksportu](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)można kontrolować dozwolonych klientów, typ dostępu (odczyt-zapis, tylko do odczytu itd.).
- Funkcja Azure NetApp Files nie jest jeszcze w pełni świadoma stref. Obecnie funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie świadczenia usługi Azure. Należy pamiętać o potencjalnych implikacjach opóźnienia w niektórych regionach świadczenia usługi Azure.

> [!IMPORTANT]
> W SAP HANA obciążeniami krytyczne jest małe opóźnienie. We współpracy z przedstawicielem firmy Microsoft upewnij się, że maszyny wirtualne i woluminy Azure NetApp Files są wdrażane w pobliżu.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Rozmiaru bazy danych HANA na Azure NetApp Files

Przepływność woluminu Azure NetApp Files jest funkcją rozmiaru woluminu i poziomu usługi, zgodnie z dokumentem Poziom usługi dla Azure NetApp Files [.](../../../azure-netapp-files/azure-netapp-files-service-levels.md)

Podczas projektowania infrastruktury sap na platformie Azure należy pamiętać o minimalnych wymaganiach firmy SAP dotyczących magazynu, które przekładają się na charakterystyki minimalnej przepływności:

- Odczyt/zapis na /hana/dziennik 250 megabajtów na sekundę (MB/s) o rozmiarze 1 MB we/wy.
- Odczyt aktywności co najmniej 400 MB/s dla /hana/data dla 16 MB i 64 MB rozmiaru we/wy.
- Aktywność zapisu co najmniej 250 MB/s dla /hana/data o rozmiarze 16 MB i 64 MB we/wy.

Limity [Azure NetApp Files przepływności na](../../../azure-netapp-files/azure-netapp-files-service-levels.md) 1 TiB limitu przydziału woluminu są:

- Premium Storage warstwa — 64 MiB/s.
- Warstwa magazynowania Ultra — 128 MiB/s.

Aby spełnić wymagania dotyczące minimalnej przepływności sap dla /hana/data i /hana/log oraz wytyczne dotyczące /hana/shared, zalecane rozmiary to:

|    Wolumin    | Rozmiar Premium Storage warstwy | Rozmiar warstwy magazynowania Ultra | Obsługiwany protokół NFS |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          Wersja 4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          Wersja 4.1          |
| /hana/shared |           1 x pamięć RAM            |          1 x pamięć RAM           |          Wersja 3 lub 4.1    |


> [!NOTE]
> Zalecenia Azure NetApp Files rozmiarów określone w tym miejscu mają na celu spełnienie minimalnych wymagań, które firma SAP zaleca dla swoich dostawców infrastruktury. W rzeczywistych scenariuszach obciążeń i wdrożeń klientów te rozmiary mogą nie być wystarczające. Użyj tych zaleceń jako punktu wyjścia i dostosuj się do wymagań określonego obciążenia.

> [!TIP]
> Rozmiar woluminów Azure NetApp Files zmieniać dynamicznie, bez  konieczności odinstalowywania woluminów, zatrzymania maszyn wirtualnych ani SAP HANA. Takie podejście zapewnia elastyczność w celu spełnienia zarówno oczekiwanych, jak i nieprzewidzianych wymagań aplikacji w zakresie przepływności.

> [!NOTE]
> Wszystkie polecenia instalacji /hana/shared w tym artykule są prezentowane dla woluminów NFSv4.1 /hana/shared.
> Jeśli woluminy /hana/shared zostały wdrożone jako woluminy NFSv3, nie zapomnij dostosować poleceń instalacji dla woluminu /hana/shared dla systemu plików NFSv3.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Wdrażanie maszyny wirtualnej z systemem Linux za pośrednictwem Azure Portal 

Najpierw należy utworzyć woluminy Azure NetApp Files woluminów. Następnie wykonaj następujące czynności:

1.  Utwórz grupę zasobów.
2.  Utwórz sieć wirtualną.
3.  Utwórz zestaw dostępności. Ustaw maksymalną domenę aktualizacji.
4.  Tworzenie wewnętrznego (wewnętrznego) równoważenia obciążenia. Zalecamy użycie standardowego równoważenia obciążenia.
    Wybierz sieć wirtualną utworzoną w kroku 2.
5.  Utwórz maszynę wirtualną 1 **(hanadb1).** 
6.  Utwórz maszynę wirtualną 2 **(hanadb2).**  
7.  Podczas tworzenia maszyny wirtualnej nie będziemy dodawać żadnego dysku, ponieważ wszystkie nasze punkty instalacji będą w udziałach NFS z Azure NetApp Files. 

> [!IMPORTANT]
> Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, [zobacz Azure Load balancer Limitations (Ograniczenia usługi Azure Load Balancer).](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wd wdrażaj drugą kartę sieciową.    

> [!NOTE] 
> Jeśli maszyny wirtualne bez publicznych adresów IP zostaną umieszczone w puli zaplecza wewnętrznego (bez publicznego adresu IP) usługi równoważenia obciążenia platformy Azure w standardowych wersjach, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja zezwalania na routing do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz Public [endpoint connectivity for Virtual Machines using Azure usługa Load Balancer w warstwie Standardowa in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md)(Łączność z publicznym punktem końcowym dla klientów korzystających z usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności oprogramowania SAP).

8.  W przypadku korzystania ze standardowego równoważenia obciążenia wykonaj następujące kroki konfiguracji:
    1.  Najpierw utwórz pulę adresów IP frontonia:
        1.  Otwórz równoważenie obciążenia, wybierz **pulę adresów IP frontendu** i wybierz pozycję **Dodaj.**
        1.  Wprowadź nazwę nowej puli adresów IP frontendu (na przykład **hana-frontend).**
        1.  Ustaw **przypisanie na** **statyczny** i wprowadź adres IP (na przykład **10.32.0.10).**
        1.  Wybierz przycisk **OK**.
        1.  Po utworzeniu nowej puli adresów IP frontonia zanotuj adres IP puli.
    1.  Następnie utwórz pulę back-end:
        1.  Otwórz równoważenie obciążenia, wybierz **pozycję Pule zaplecza** i wybierz pozycję **Dodaj.**
        1.  Wprowadź nazwę nowej puli zaplecza (na przykład **hana-backend).**
        1.  Wybierz **pozycję Dodaj maszynę wirtualną.**
        1.  Wybierz pozycję **Maszyna wirtualna**.
        1.  Wybierz maszyny wirtualne klastra SAP HANA i ich adresy IP.
        1.  Wybierz pozycję **Dodaj**.
    1.  Następnie utwórz sondę kondycji:
        1.  Otwórz usługę równoważenia obciążenia, wybierz **pozycję Sondy kondycji** i wybierz pozycję **Dodaj.**
        1.  Wprowadź nazwę nowej sondy kondycji (na przykład **hana-hp).**
        1.  Wybierz TCP jako protokół i port 625 **03.** Zachowaj wartość **Interwał** ustawioną na 5, a **wartość** progowa W złej kondycji na 2.
        1.  Wybierz przycisk **OK**.
    1.  Następnie utwórz reguły równoważenia obciążenia:
        1.  Otwórz równoważenie obciążenia, wybierz **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
        1.  Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **hana-lb).**
        1.  Wybierz utworzony wcześniej adres IP frontonia, pulę zaplecza i sondę kondycji (na przykład **hana-frontend,** **hana-backend** i **hana-hp).**
        1.  Wybierz pozycję **Porty HA.**
        1.  Upewnij się, że **włączyć zmienny adres IP**.
        1.  Wybierz przycisk **OK**.


9. Alternatywnie, jeśli twój scenariusz decyduje o użyciu podstawowego równoważenia obciążenia, wykonaj następujące kroki konfiguracji:
    1.  Skonfiguruj usługę równoważenia obciążenia. Najpierw utwórz pulę adresów IP frontonia:
        1.  Otwórz równoważenie obciążenia, wybierz **pulę adresów IP frontendu** i wybierz pozycję **Dodaj.**
        1.  Wprowadź nazwę nowej puli adresów IP frontendu (na przykład **hana-frontend).**
        1.  Ustaw **przypisanie na** **statyczny** i wprowadź adres IP (na przykład **10.32.0.10).**
        1.  Wybierz przycisk **OK**.
        1.  Po utworzeniu nowej puli adresów IP frontonia zanotuj adres IP puli.
    1.  Następnie utwórz pulę back-end:
        1.  Otwórz równoważenie obciążenia, wybierz **pozycję Pule zaplecza** i wybierz pozycję **Dodaj.**
        1.  Wprowadź nazwę nowej puli zaplecza (na przykład **hana-backend).**
        1.  Wybierz **pozycję Dodaj maszynę wirtualną.**
        1.  Wybierz zestaw dostępności utworzony w kroku 3.
        1.  Wybierz maszyny wirtualne klastra SAP HANA klastra.
        1.  Wybierz przycisk **OK**.
    1.  Następnie utwórz sondę kondycji:
        1.  Otwórz usługę równoważenia obciążenia, wybierz **pozycję Sondy kondycji** i wybierz pozycję **Dodaj.**
        1.  Wprowadź nazwę nowej sondy kondycji (na przykład **hana-hp).**
        1.  Wybierz **TCP** jako protokół i port 625 **03.** Zachowaj wartość **Interwał** ustawioną na 5, a **wartość** progowa W złej kondycji na 2.
        1.  Wybierz przycisk **OK**.
    1.  W SAP HANA 1.0 utwórz reguły równoważenia obciążenia:
        1.  Otwórz równoważenie obciążenia, wybierz **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
        1.  Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład hana-lb-3 **03** 15).
        1.  Wybierz utworzony wcześniej adres IP frontonia, pulę zaplecza i sondę kondycji (na przykład **hana-frontend).**
        1.  Zachowaj protokół **ustawiony** na **TCP** i wprowadź port 3 **03** 15.
        1.  Zwiększ limit **czasu bezczynności do** 30 minut.
        1.  Upewnij się, że **włączyć zmienny adres IP**.
        1.  Wybierz przycisk **OK**.
        1.  Powtórz te kroki dla portu 3 **03** 17.
    1.  W SAP HANA 2.0 utwórz reguły równoważenia obciążenia dla systemowej bazy danych:
        1.  Otwórz równoważenie obciążenia, wybierz **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
        1.  Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład hana-lb-3 **03** 13).
        1.  Wybierz utworzony wcześniej adres IP frontonia, pulę zaplecza i sondę kondycji (na przykład **hana-frontend).**
        1.  Zachowaj protokół **ustawiony** na **TCP** i wprowadź port 3 **03** 13.
        1.  Zwiększ limit **czasu bezczynności do** 30 minut.
        1.  Upewnij się, że **włączyć zmienny adres IP**.
        1.  Wybierz przycisk **OK**.
        1.  Powtórz te kroki dla portu 3 **03** 14.
    1.  W SAP HANA 2.0 najpierw utwórz reguły równoważenia obciążenia dla bazy danych dzierżawy:
        1.  Otwórz równoważenie obciążenia, wybierz **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
        1.  Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład hana-lb-3 **03** 40).
        1.  Wybierz utworzony wcześniej adres IP frontonia, pulę zaplecza i sondę kondycji (na przykład **hana-frontend).**
        1.  Zachowaj wartość **protokołu** **TCP** i wprowadź port 3 **03** 40.
        1.  Zwiększ limit **czasu bezczynności do** 30 minut.
        1.  Upewnij się, że **włączyć zmienny adres IP**.
        1.  Wybierz przycisk **OK**.
        1.  Powtórz te kroki dla portów 3 **03** 41 i 3 **03** 42.

Aby uzyskać więcej informacji na temat wymaganych portów [](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) dla usługi SAP HANA, przeczytaj rozdział Połączenia z bazami danych dzierżaw w przewodniku Bazy danych dzierżaw usługi [SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) lub Uwaga [sap 2388694.](https://launchpad.support.sap.com/#/notes/2388694)

> [!IMPORTANT]
> Nie włączaj znaczników czasu TCP na maszyn wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie znaczników czasu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.ipv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe [informacje, zobacz Load Balancer kondycji](../../../load-balancer/load-balancer-custom-probe-overview.md). Zobacz też uwaga SAP [2382421.](https://launchpad.support.sap.com/#/notes/2382421)

## <a name="mount-the-azure-netapp-files-volume"></a>Zainstaluj wolumin Azure NetApp Files woluminu

1. **[A]** Utwórz punkty instalacji dla woluminów bazy danych HANA. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** Sprawdź ustawienie domeny NFS. Upewnij się, że domena jest skonfigurowana jako domyślna Azure NetApp Files, **tj.** defaultv4iddomain.com, a mapowanie jest **ustawione** na nikt .

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Upewnij się, że ustawiono domenę NFS w pliku /etc/idmapd.conf na maszynie wirtualnej, aby dopasować domyślną konfigurację domeny na maszynie Azure NetApp Files: **defaultv4iddomain.com**. W przypadku niezgodności między konfiguracją domeny na kliencie systemu plików NFS (tj. maszynie wirtualnej) i serwerem NFS, tj. konfiguracją usługi Azure NetApp, uprawnienia do plików na woluminach usługi Azure NetApp zainstalowanych na maszynach wirtualnych będą wyświetlane jako nikt.
    

3. **[1]** Zainstaluj woluminy specyficzne dla węzła na węźle node1 (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** Zainstaluj woluminy specyficzne dla węzła na węźle node2 (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** Sprawdź, czy wszystkie woluminy HANA są zainstalowane z protokołem NFS w wersji NFSv4.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** Sprawdź **nfs4_disable_idmapping**. Powinna być ustawiona wartość **Y**. Aby utworzyć strukturę katalogu, **w nfs4_disable_idmapping** znajduje się, wykonaj polecenie instalacji. Nie można ręcznie utworzyć katalogu w katalogu /sys/modules, ponieważ dostęp jest zarezerwowany dla jądra/sterowników.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Aby uzyskać więcej informacji na temat zmiany **nfs_disable_idmapping,** zobacz [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>SAP HANA instalacji

1. **[A]** Skonfiguruj rozpoznawania nazw hostów dla wszystkich hostów.

   Możesz użyć serwera DNS lub zmodyfikować plik /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts. Zastąp adres IP i nazwę hosta w następujących poleceniach:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL dla konfiguracji platformy HANA

   Skonfiguruj system RHEL zgodnie z opisem w poniższej notce dotyczącej systemu SAP na podstawie wersji systemu RHEL

   - [2292690 — SAP HANA DB: zalecane ustawienia systemu operacyjnego dla systemu RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 — SAP HANA DB: zalecane ustawienia systemu operacyjnego dla systemu RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 — Linux: Uruchamianie aplikacji SAP skompilowanych przy użyciu środowiska GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 — Linux: Uruchamianie aplikacji SAP skompilowanych przy użyciu środowiska GCC 7.x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 — Linux: Uruchamianie aplikacji SAP skompilowanych przy użyciu środowiska GCC 9.x](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** Zainstaluj SAP HANA

   W przypadku platformy HANA 2.0 SPS 01 rozwiązanie MDC jest opcją domyślną. Podczas instalowania systemu HANA zostanie utworzony system SYSTEMDB i dzierżawa o tym samym identyfikatorze SID. W niektórych przypadkach nie chcesz dzierżawy domyślnej. Jeśli nie chcesz tworzyć początkowej dzierżawy wraz z instalacją, możesz postępować zgodnie z uwagami sap [2629711](https://launchpad.support.sap.com/#/notes/2629711)

    Uruchom program **hdblcm** z dysku DVD HANA. Wprowadź następujące wartości w wierszu polecenia:  
    Wybierz instalację: wprowadź **1** (do zainstalowania)  
    Wybierz dodatkowe składniki do instalacji: wprowadź **1**.  
    Wprowadź ścieżkę instalacji [/hana/shared]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Wprowadź nazwę hosta lokalnego [..]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Czy chcesz dodać dodatkowe hosty do systemu? (y/n) [n]: **n**  
    Wprowadź SAP HANA systemowy: wprowadź **HN1**.  
    Wprowadź numer wystąpienia [00]: wprowadź **wartość 03**  
    Wybierz pozycję Tryb bazy danych/ Wprowadź indeks [1]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Wybierz pozycję Użycie systemu / Wprowadź indeks [4]: wprowadź **wartość 4** (jako wartość niestandardową)  
    Wprowadź lokalizację woluminów danych [/hana/data]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Wprowadź lokalizację woluminów dziennika [/hana/log]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Ograniczać maksymalną alokację pamięci? [n]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Wprowadź nazwę hosta certyfikatu dla hosta "..." [...]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Wprowadź hasło użytkownika programu SAP Host Agent (sapadm): wprowadź hasło użytkownika agenta hosta  
    Potwierdź hasło użytkownika programu SAP Host Agent (sapadm): wprowadź ponownie hasło użytkownika agenta hosta, aby potwierdzić  
    Wprowadź hasło administratora systemu (hn1adm): wprowadź hasło administratora systemu  
    Potwierdź hasło administratora systemu (hn1adm): wprowadź ponownie hasło administratora systemu, aby potwierdzić  
    Wprowadź katalog macierzysty administratora systemu [/usr/sap/HN1/home]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Wprowadź powłokę logowania administratora systemu [/bin/sh]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Wprowadź identyfikator użytkownika administratora systemu [1001]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Wprowadź identyfikator grupy użytkowników (sapsys) [79]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Wprowadź hasło użytkownika bazy danych (SYSTEM): wprowadź hasło użytkownika bazy danych  
    Potwierdź hasło użytkownika bazy danych (SYSTEM): wprowadź ponownie hasło użytkownika bazy danych, aby potwierdzić  
    Uruchom ponownie system po ponownym uruchomieniu komputera? [n]: naciśnij klawisz Enter, aby zaakceptować wartość domyślną  
    Czy chcesz kontynuować? (y/n): zweryfikuj podsumowanie. Wprowadź **y,** aby kontynuować  

4. **[A]** Uaktualnianie agenta hosta SAP

   Pobierz najnowsze archiwum agenta hosta SAP z centrum [oprogramowania SAP i](https://launchpad.support.sap.com/#/softwarecenter) uruchom następujące polecenie, aby uaktualnić agenta. Zastąp ścieżkę do archiwum, aby wskazać pobrany plik:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A] Konfigurowanie** zapory

   Utwórz regułę zapory dla portu sondy usługi Azure Load Balancer.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>Konfigurowanie SAP HANA replikacji systemu operacyjnego

Wykonaj kroki opisane w te SAP HANA [replikacji systemu,](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) aby skonfigurować SAP HANA replikacji systemu. 

## <a name="cluster-configuration"></a>Konfiguracja klastra

W tej sekcji opisano niezbędne kroki wymagane do bezproblemowego działania klastra SAP HANA w udziałach NFS przy użyciu Azure NetApp Files. 

### <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w [te tematu Setting up Pacemaker on Red Hat Enterprise Linux](./high-availability-guide-rhel-pacemaker.md) in Azure (Konfigurowanie programu Pacemaker na platformie Azure), aby utworzyć podstawowy klaster pacemaker dla tego serwera HANA.

### <a name="implement-the-python-system-replication-hook-saphanasr"></a>Implementowanie wpięć replikacji systemu języka Python SAPHanaSR

Jest to ważny krok w celu zoptymalizowania integracji z klastrem i ulepszenia wykrywania, gdy jest wymagane włączenie klastra w tryb failover. Zdecydowanie zaleca się skonfigurowanie zaczepienia SAPHanaSR w języku Python.    

1. **[A]** Zainstaluj "wpięcie replikacji systemu" platformy HANA. Węzły zaczepienia muszą być zainstalowane w obu węzłach usługi HANA DB.           

   > [!TIP]
   > Wpięcie języka Python można zaimplementować tylko dla platformy HANA 2.0.        

   1. Przygotuj wpięcie jako `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Zatrzymaj hanę na obu węzłach. Wykonaj jako <\> sid adm:  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Dostosuj `global.ini` w każdym węźle klastra.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** Klaster wymaga konfiguracji programu sudoers w każdym węźle klastra <sid \> adm. W tym przykładzie jest to osiągane przez utworzenie nowego pliku. Wykonaj polecenia jako `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```

3. **[A]** Uruchom SAP HANA obu węzłach. Wykonaj polecenie jako <\> sid adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1] Sprawdź** instalację wpięć. Wykonaj polecenie <\> sid adm w aktywnej lokacji replikacji systemu HANA.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

Aby uzyskać więcej informacji na temat implementacji SAP HANA replikacji systemu, zobacz Włączanie wpięcie dostawcy [SAP HA/DR.](https://access.redhat.com/articles/3004101#enable-srhook)  

### <a name="configure-filesystem-resources"></a>Konfigurowanie zasobów systemu plików

W tym przykładzie każdy węzeł klastra ma własne systemy plików NFS platformy HANA /hana/shared, /hana/data i /hana/log.   

1. **[1]** Umieść klaster w trybie konserwacji.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** Utwórz zasoby systemu plików dla **instalacji bazy danych hanadb1.**

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** Utwórz zasoby systemu plików dla **instalacji bazy danych hanadb2.**

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` Atrybut jest dodawany do operacji monitora, dzięki czemu każdy monitor wykonuje test odczytu/zapisu w systemie plików. Bez tego atrybutu monitor tylko sprawdza, czy system plików jest zainstalowany. Może to być problem, ponieważ po utracie łączności system plików może pozostać zainstalowany, mimo że jest niedostępny.

    `on-fail=fence` Atrybut jest również dodawany do operacji monitora. W przypadku tej opcji, jeśli operacja monitorowania w węźle zakończy się niepowodzeniem, ten węzeł zostanie natychmiast ogrodzony. Bez tej opcji domyślne zachowanie polega na zatrzymaniu wszystkich zasobów zależnych od zasobu, który zakończył się niepowodzeniem, a następnie ponownym uruchomieniu zasobu, który zakończył się niepowodzeniem, a następnie uruchomieniu wszystkich zasobów zależnych od zasobu, który zakończył się niepowodzeniem. To zachowanie nie tylko może zająć dużo czasu, gdy zasób SAPHana zależy od zasobu, który zakończył się niepowodzeniem, ale może również całkowicie się nie powieść. Zasób SAPHana nie może zatrzymać się pomyślnie, jeśli serwer NFS z plikami wykonywalnymi HANA jest niedostępny.

4. **[1]** Konfigurowanie ograniczeń lokalizacji

   Skonfiguruj ograniczenia lokalizacji, aby upewnić się, że zasoby, które zarządzają unikatowymi instalacjimi bazy danych hanadb1, nigdy nie mogą być uruchamiane w bazie danych hanadb2 i na odwrót.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    Opcja `resource-discovery=never` jest ustawiana, ponieważ unikatowe instalacji dla każdego węzła współużytkują ten sam punkt instalacji. Na przykład używa `hana_data1` punktu instalacji , a także punktu instalacji `/hana/data` `hana_data2` `/hana/data` . Może to spowodować wynik fałszywie dodatni dla operacji sondowania, gdy stan zasobu jest sprawdzany podczas uruchamiania klastra, a to z kolei może spowodować niepotrzebne zachowanie odzyskiwania. Można tego uniknąć, ustawiając `resource-discovery=never`

5. **[1]** Konfigurowanie zasobów atrybutów

   Konfigurowanie zasobów atrybutów. Te atrybuty zostaną ustawione na wartość true, jeśli wszystkie instalacji systemu plików NFS węzła (/hana/data, /hana/log i /hana/data) zostaną zainstalowane i w przeciwnym razie zostaną ustawione na wartość false.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** Konfigurowanie ograniczeń lokalizacji

   Skonfiguruj ograniczenia lokalizacji, aby upewnić się, że zasób atrybutu hanadb1 nigdy nie jest uruchamiany w bazie danych hanadb2 i na odwrót.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** Tworzenie ograniczeń kolejności

   Skonfiguruj ograniczenia kolejności tak, aby zasoby atrybutów węzła uruchamiały się dopiero po instalacji wszystkich instalacji systemu plików NFS węzła.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Jeśli konfiguracja obejmuje systemy plików poza grupą lub , należy dołączyć opcję , aby nie było żadnych zależności kolejności `hanadb1_nfs` `hanadb2_nfs` między `sequential=false` systemami plików. Wszystkie systemy plików muszą rozpoczynać się przed , ale nie muszą być rozpoczynane w żadnej kolejności `hana_nfs1_active` względem siebie. Aby uzyskać więcej informacji, zobacz Jak mogę konfigurowanie SAP HANA replikacji systemu Scale-Up w klastrze Pacemaker, gdy systemy plików HANA znajdują się w udziałach [NFS](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>Konfigurowanie SAP HANA klastra

1. Wykonaj kroki opisane w [SAP HANA zasobów klastra,](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) aby utworzyć SAP HANA zasobów w klastrze. Po SAP HANA zasobów należy utworzyć ograniczenie reguły lokalizacji między zasobami SAP HANA i systemami plików (NFS Mounts)

2. **[1] Skonfiguruj** ograniczenia między SAP HANA zasobów i instalacji systemu plików NFS

   Ograniczenia reguły lokalizacji zostaną ustawione tak, aby zasoby SAP HANA można było uruchomić w węźle tylko wtedy, gdy są zainstalowane wszystkie instalacji systemu plików NFS węzła.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Sprawdzanie stanu klastra i wszystkich zasobów
   > [!NOTE]
   > Ten artykuł zawiera odwołania do terminu *podrzędnego*, terminu, który nie jest już używany przez firmę Microsoft. Po usunięciu terminu z oprogramowania usuniemy go z tego artykułu.
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Konfigurowanie replikacji systemu z włączoną obsługą aktywnej/odczytu platformy HANA w klastrze Pacemaker

Począwszy od programu SAP HANA 2.0 SPS 01 SAP umożliwia konfiguracje aktywne/z obsługą odczytu na potrzeby replikacji systemu SAP HANA, w której systemy pomocnicze systemu replikacji systemu SAP HANA mogą być aktywnie używane dla obciążeń intensywnie wymagających odczytu. Do obsługi takiej konfiguracji w klastrze wymagany jest drugi wirtualny adres IP, który umożliwia klientom dostęp do pomocniczej bazy danych z włączoną obsługą odczytu SAP HANA danych. Aby zapewnić, że dostęp do dodatkowej lokacji replikacji będzie nadal możliwy po zakończeniu przejęcia, klaster musi przenieść wirtualny adres IP z pomocniczym zasobem SAPHana.

Dodatkowa konfiguracja wymagana do zarządzania replikacją systemu z włączoną obsługą funkcji HANA Active/Read w klastrze o wysokiej dostępności z systemem Red Hat z drugim wirtualnym adresem IP jest opisana w te tematach Configure [HANA Active/Read Enabled System Replication in Pacemaker cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster)(Konfigurowanie replikacji systemu z włączoną obsługą funkcji HANA Active/Read w klastrze Pacemaker).  

Przed przystąpieniem do dalszej pracy upewnij się, że masz w pełni skonfigurowany klaster wysokiej dostępności systemu Red Hat zarządzający SAP HANA bazą danych, zgodnie z opisem w powyższych segmentach dokumentacji.    


## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano sposób testowania konfiguracji. 

1. Przed rozpoczęciem testu upewnij się, że program Pacemaker nie ma żadnej akcji, która zakończyła się niepowodzeniem (za pośrednictwem stanu pcs), nie ma nieoczekiwanych ograniczeń lokalizacji (na przykład pozostawień testu migracji) i że replikacja systemu HANA jest stanem synchronizacji, na przykład z stanem systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Sprawdź konfigurację klastra pod względu na scenariusz awarii, gdy węzeł utraci dostęp do udziału NFS (/hana/shared)

   Agenci SAP HANA zasobów zależą od plików binarnych przechowywanych w programie w `/hana/shared` celu wykonywania operacji podczas pracy w trybu failover. System plików  `/hana/shared` jest zainstalowany za pośrednictwem systemu plików NFS w przedstawionym scenariuszu.  
   Symulowanie awarii jest trudne, gdy jeden z serwerów traci dostęp do udziału NFS. Test, który można wykonać, to ponowne zainstalować system plików jako tylko do odczytu.
   Takie podejście sprawdza, czy klaster będzie mógł przesieć w tryb failover, jeśli w aktywnym węźle zostanie `/hana/shared` utracony dostęp do klastra.     


   **Oczekiwany wynik:** W przypadku tworzenia jako systemu plików tylko do odczytu atrybut zasobu, który wykonuje operację odczytu/zapisu w systemie plików, nie powiedzie się, ponieważ nie będzie mógł zapisać niczego w systemie plików i wykona tryb failover zasobu `/hana/shared` `OCF_CHECK_LEVEL` `hana_shared1` HANA.  Ten sam wynik jest oczekiwany, gdy węzeł HANA utraci dostęp do udziałów NFS. 

   Stan zasobu przed rozpoczęciem testu:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   Możesz umieścić /hana/shared w trybie tylko do odczytu w aktywnym węźle klastra, używając poniższego polecenia:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   Baza danych hanadb1 zostanie ponownie uruchamiana lub wyzjemniana na podstawie akcji ustawionej na stonith ( `pcs property show stonith-action` ).  Gdy serwer (hanadb1) nie będzie już w stanie, zasób HANA zostanie przenieść do bazy danych hanadb2. Stan klastra można sprawdzić z bazy danych hanadb2.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   Zalecamy dokładne przetestowanie konfiguracji klastra SAP HANA, wykonując również testy opisane w teście Konfigurowanie SAP HANA replikacji systemu w [systemie RHEL.](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)

## <a name="next-steps"></a>Następne kroki

* [Planowanie Virtual Machines wdrażania oprogramowania SAP na platformie Azure][planning-guide]
* [Wdrażanie usługi Azure Virtual Machines sap][deployment-guide]
* [Wdrażanie usługi Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* [Woluminy NFS 4.1 w usłudze Azure NetApp Files dla platformy SAP HANA](./hana-vm-operations-netapp.md)