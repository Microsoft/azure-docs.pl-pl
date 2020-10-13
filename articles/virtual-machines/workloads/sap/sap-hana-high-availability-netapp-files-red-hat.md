---
title: Wysoka dostępność SAP HANA skalowanie w górę z ANF na RHEL | Microsoft Docs
description: Zapewnienie wysokiej dostępności SAP HANA z ANF na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2020
ms.author: radeltch
ms.openlocfilehash: ce24bf541c5a71c50bb34f5e42aa3452f01b871c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978173"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Wysoka dostępność SAP HANA skalowanie Azure NetApp Files na Red Hat Enterprise Linux

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

W tym artykule opisano sposób konfigurowania replikacji systemu SAP HANA w ramach wdrożenia skalowalnego w poziomie, gdy systemy plików HANA są instalowane za pośrednictwem systemu plików NFS, używając Azure NetApp Files (ANF). W przykładowych konfiguracjach i poleceniach instalacji, numer wystąpienia nr **03**i identyfikator systemu Hana **HN1** są używane. SAP HANA replikacja składa się z jednego węzła podstawowego i co najmniej jednego węzła pomocniczego.

Gdy kroki w tym dokumencie są oznaczone następującymi prefiksami, znaczenie jest następujące:

- **[A]**: krok ma zastosowanie do wszystkich węzłów
- **[1]**: krok ma zastosowanie tylko do Węzeł1
- **[2]**: krok ma zastosowanie tylko do Węzeł2
 
Przeczytaj najpierw następujące informacje i dokumenty SAP:

- Nota SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533), która ma:
    - Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP.
    - Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure.
    - Obsługiwane programy SAP i system operacyjny oraz kombinacje baz danych.
    - Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure.
- Uwaga dotycząca protokołu SAP [2015553](https://launchpad.support.sap.com/#/notes/2015553) zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
- W przypadku oprogramowania SAP Uwaga [405827](https://launchpad.support.sap.com/#/notes/405827) znajduje się lista zalecanych systemów plików dla środowiska Hana.
- Uwaga dla programu SAP [2002167](https://launchpad.support.sap.com/#/notes/2002167) są zalecane ustawienia systemu operacyjnego dla Red Hat Enterprise Linux.
- Uwaga dotycząca programu SAP [2009879](https://launchpad.support.sap.com/#/notes/2009879) SAP HANA wytycznych dotyczących Red Hat Enterprise Linux.
- Uwaga dotycząca oprogramowania SAP [2178632](https://launchpad.support.sap.com/#/notes/2178632) zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
- Uwaga dotycząca programu SAP [2191498](https://launchpad.support.sap.com/#/notes/2191498) ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
- Uwaga dotycząca oprogramowania SAP [2243692](https://launchpad.support.sap.com/#/notes/2243692) zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
- Uwaga dotycząca programu SAP [1999351](https://launchpad.support.sap.com/#/notes/1999351) zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
- [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
- [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
- [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide]
- [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
- [SAP HANA replikację systemu w klastrze Pacemaker.](https://access.redhat.com/articles/3004101)
- Ogólna dokumentacja RHEL
    - [Omówienie Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Administracja Add-On o wysokiej dostępności.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Informacje o wysokiej dostępności Add-On.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [Skonfiguruj SAP HANA replikację systemu w Scale-Up w klastrze Pacemaker, gdy systemy plików platformy HANA znajdują się w udziałach NFS](https://access.redhat.com/solutions/5156571)
- Dokumentacja RHEL specyficzna dla platformy Azure:
    - [Zasady obsługi klastrów RHEL o wysokiej dostępności — Microsoft Azure Virtual Machines jako elementy członkowskie klastra.](https://access.redhat.com/articles/3131341)
    - [Instalowanie i Konfigurowanie Red Hat Enterprise Linux 7,4 (i nowszych) High-Availability klastra w Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Zainstaluj SAP HANA na Red Hat Enterprise Linux do użycia w Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [Konfigurowanie SAP HANA skalowalności w poziomie klastra Pacemaker w przypadku systemu plików HANA w udziałach NFS](https://access.redhat.com/solutions/5156571)
- [NetApp aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)

## <a name="overview"></a>Omówienie

Tradycyjnie w środowisku skalowania wszystkie systemy plików dla SAP HANA są instalowane z magazynu lokalnego. Konfigurowanie wysokiej dostępności SAP HANA replikacji systemu w Red Hat Enterprise Linux jest opublikowany w przewodniku [konfigurowanie SAP HANA replikacji systemu na RHEL](./sap-hana-high-availability-rhel.md)

Aby osiągnąć SAP HANA wysoką dostępność systemu skalowalności w [Azure NetApp Files](../../../azure-netapp-files/index.yml) udziałach NFS, w klastrze musi być dostępna dodatkowa konfiguracja zasobów, aby umożliwić odzyskanie zasobów platformy Hana, gdy jeden węzeł utraci dostęp do udziałów NFS w ANF.  Klaster zarządza instalacjami systemu plików NFS, co pozwala na monitorowanie kondycji zasobów. Są wymuszane zależności między instalacjami systemu plików a zasobami SAP HANA.  

![SAP HANA skalowanie w poziomie HA na ANF](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

Systemy plików SAP HANA są instalowane w udziałach NFS przy użyciu Azure NetApp Files w każdym węźle. Systemy plików/Hana/Data,/Hana/log i/Hana/Shared są unikatowe dla każdego węzła. 

Zainstalowane w Węzeł1 (**hanadb1**)

- 10.32.2.4:/**hanadb1**-Data-mnt00001 na/Hana/Data
- 10.32.2.4:/**hanadb1**-log-mnt00001 na/Hana/log
- 10.32.2.4:/**hanadb1**-Shared-mnt00001 na/Hana/Shared

Zainstalowane w Węzeł2 (**hanadb2**)

- 10.32.2.4:/**hanadb2**-Data-mnt00001 na/Hana/Data
- 10.32.2.4:/**hanadb2**-log-mnt00001 na/Hana/log
- 10.32.2.4:/**hanadb2**-Shared-mnt00001 na/Hana/Shared

> [!NOTE]
> Systemy plików/Hana/Shared,/Hana/Data i/Hana/log nie są współdzielone między dwoma węzłami. Każdy węzeł klastra ma własne, oddzielne systemy plików.   

Konfiguracja replikacji systemu SAP HANA używa dedykowanej wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia:

- Konfiguracja frontonu: adres IP 10.32.0.10 dla hn1-DB
- Konfiguracja zaplecza: połączono z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią replikacji systemu HANA
- Port sondy: Port 62503
- Reguły równoważenia obciążenia: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (w przypadku korzystania z podstawowego modułu równoważenia obciążenia platformy Azure)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Konfigurowanie infrastruktury plików usługi Azure NetApp

Przed rozpoczęciem pracy z usługą konfigurowania infrastruktury Azure NetApp Files zapoznaj się z dokumentacją usługi Azure [NetApp](../../../azure-netapp-files/index.yml).

Azure NetApp Files jest dostępna w kilku [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Sprawdź, czy wybrany region platformy Azure oferuje Azure NetApp Files.

Aby uzyskać informacje o dostępności Azure NetApp Files według regionów świadczenia usługi Azure, zobacz [Azure NetApp Files dostępność według regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all).

Przed wdrożeniem Azure NetApp Files Zażądaj dołączenia do Azure NetApp Files, przechodząc do [rejestracji w celu uzyskania instrukcji Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

### <a name="deploy-azure-netapp-files-resources"></a>Wdrażanie zasobów Azure NetApp Files

W poniższych instrukcjach przyjęto założenie, że [usługa Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)została już wdrożona. Azure NetApp Files zasoby i maszyny wirtualne, w których zostaną zainstalowane zasoby Azure NetApp Files, muszą być wdrożone w tej samej sieci wirtualnej platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure.

1. Jeśli zasoby nie zostały jeszcze wdrożone, zażądaj dołączenia [do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Utwórz konto NetApp w wybranym regionie świadczenia usługi Azure, postępując zgodnie z instrukcjami w temacie [Tworzenie konta NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

3.  Skonfiguruj pulę pojemności Azure NetApp Files, postępując zgodnie z instrukcjami w temacie [konfigurowanie Azure NetApp Files puli pojemności](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

    Architektura HANA przedstawiona w tym artykule używa pojedynczej puli pojemności Azure NetApp Files na poziomie usługi *Ultra* Service. W przypadku obciążeń HANA na platformie Azure zalecamy użycie [poziomu usługi](../../../azure-netapp-files/azure-netapp-files-service-levels.md)Azure NetApp Files *Ultra* lub *Premium* .

4.  Delegowanie podsieci do Azure NetApp Files, zgodnie z opisem w temacie [delegowanie podsieci do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5.  Wdróż woluminy Azure NetApp Files, postępując zgodnie z instrukcjami w temacie [Tworzenie woluminu NFS dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

    Podczas wdrażania woluminów Pamiętaj, aby wybrać wersję NFSv 4.1. Wdróż woluminy w wyznaczeniej podsieci Azure NetApp Files. Adresy IP woluminów NetApp platformy Azure są przypisywane automatycznie.

    Należy pamiętać, że zasoby Azure NetApp Files i maszyny wirtualne platformy Azure muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure. Na przykład hanadb1-Data-mnt00001, hanadb1-log-mnt00001 i tak dalej, są nazwami woluminów oraz nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 i tak dalej, są ścieżkami plików dla woluminów Azure NetApp Files.
    
    Na **hanadb1**

    - Volume hanadb1-Data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volume hanadb1-Shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    Na **hanadb2**

    - Volume hanadb2-Data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volume hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volume hanadb2-Shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Istotne zagadnienia

Podczas tworzenia Azure NetApp Files na potrzeby SAP HANA systemów skalowalności należy pamiętać o następujących kwestiach:

- Minimalna Pula pojemności to 4 tebibajtów (TiB).
- Minimalny rozmiar woluminu to 100 gibibajtach (GiB).
- Azure NetApp Files i wszystkie maszyny wirtualne, na których zostaną zainstalowane woluminy Azure NetApp Files, muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w [równorzędnych sieciach wirtualnych](../../../virtual-network/virtual-network-peering-overview.md) w tym samym regionie.
- Wybrana Sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files.
- Przepływność woluminu Azure NetApp Files jest funkcją limitu przydziału woluminu i poziomu usługi, zgodnie z opisem w obszarze [poziom usług dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Gdy zmieniasz rozmiar woluminów NetApp platformy Azure, upewnij się, że przepływność spełnia wymagania systemowe HANA.
- Za pomocą [zasad eksportu](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)Azure NetApp Files można kontrolować dozwolonych klientów, typ dostępu (odczyt i zapis, tylko do odczytu itd.).
- Funkcja Azure NetApp Files nie jest jeszcze obsługiwana dla strefy. Obecnie funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie świadczenia usługi Azure. Weź pod uwagę potencjalne konsekwencje opóźnienia w niektórych regionach świadczenia usługi Azure.

> [!IMPORTANT]
> W przypadku obciążeń SAP HANA małe opóźnienia są krytyczne. Skontaktuj się z przedstawicielem firmy Microsoft, aby upewnić się, że maszyny wirtualne i woluminy Azure NetApp Files są wdrożone w sąsiedztwie.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Ustalanie wielkości bazy danych HANA na Azure NetApp Files

Przepływność woluminu Azure NetApp Files jest funkcją rozmiaru woluminu i poziomu usługi, zgodnie z opisem w obszarze [poziom usług dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md).

Podczas projektowania infrastruktury dla oprogramowania SAP na platformie Azure należy pamiętać o minimalnych wymaganiach dotyczących magazynu przez oprogramowanie SAP, które przekładają się na minimalne charakterystyki przepływności:

- Do odczytu i zapisu na/Hana/log z 250 megabajtów na sekundę (MB/s) z rozmiarami we/wy 1 MB.
- Działanie odczytu o rozmiarze co najmniej 400 MB/s dla/Hana/Data dla 16 MB i 64 MB pamięci we/wy.
- Działanie zapisu o rozmiarze co najmniej 250 MB/s dla/Hana/Data z 16 MB i 64 MB.

[Limity przepływności Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) na 1 TIB przydziału woluminu są następujące:

- Warstwa Premium Storage-64 MiB/s.
- Warstwa Ultra Storage — 128 MiB/s.

Aby spełnić wymagania dotyczące minimalnej przepływności SAP dla/Hana/Data i/Hana/log oraz wytyczne dla/Hana/Shared, zalecane rozmiary to:

|    Wolumin    | Rozmiar warstwy Premium Storage | Rozmiar warstwy Ultra Storage | Obsługiwany protokół NFS |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v 4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v 4.1          |
| /hana/shared |           1 x pamięci RAM            |          1 x pamięci RAM           |          v3 lub v 4.1    |


> [!NOTE]
> Zalecenia dotyczące ustalania wielkości Azure NetApp Files podane tutaj są celem spełnienia minimalnych wymagań zalecanych przez system SAP dla dostawców infrastruktury. W rzeczywistych wdrożeniach klientów i scenariuszach obciążeń te rozmiary mogą nie być wystarczające. Użyj tych zaleceń jako punktu wyjścia i adaptacji na podstawie wymagań określonego obciążenia.

> [!TIP]
> Można dynamicznie zmieniać rozmiar Azure NetApp Files woluminów, bez konieczności *odinstalowywania* woluminów, zatrzymywania maszyn wirtualnych lub zatrzymywania SAP HANA. Takie podejście pozwala elastycznie spełnić zarówno oczekiwane, jak i nieprzewidziane wymagania dotyczące przepływności aplikacji.

> [!NOTE]
> Wszystkie polecenia dotyczące instalowania/Hana/Shared w tym artykule przedstawiono dla woluminów NFSv 4.1/Hana/Shared.
> Jeśli woluminy/Hana/Shared zostały wdrożone jako woluminy NFSv3, nie zapomnij dostosować poleceń instalacji dla/Hana/Shared dla NFSv3.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Wdróż maszynę wirtualną z systemem Linux za pomocą Azure Portal 

Najpierw należy utworzyć woluminy Azure NetApp Files. Następnie wykonaj następujące czynności:

1.  Utwórz grupę zasobów.
2.  Utwórz sieć wirtualną.
3.  Utwórz zestaw dostępności. Ustaw maksymalną domenę aktualizacji.
4.  Utwórz moduł równoważenia obciążenia (wewnętrzny). Zalecamy użycie standardowej usługi równoważenia obciążenia.
    Wybierz sieć wirtualną utworzoną w kroku 2.
5.  Utwórz maszynę wirtualną 1 (**hanadb1**). 
6.  Utwórz maszynę wirtualną 2 (**hanadb2**).  
7.  Podczas tworzenia maszyny wirtualnej nie będziemy dodawać żadnego dysku, ponieważ wszystkie Twoje punkty instalacji będą mieć udziały NFS z Azure NetApp Files. 
8.  W przypadku korzystania z usługi równoważenia obciążenia w warstwie Standardowa wykonaj następujące czynności konfiguracyjne:
    1.  Najpierw Utwórz pulę adresów IP frontonu:
        1.  Otwórz moduł równoważenia obciążenia, wybierz pozycję **Pula adresów IP frontonu**, a następnie wybierz pozycję **Dodaj**.
        1.  Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **Hana-fronton**).
        1.  Ustaw **przypisanie** na **static** i wprowadź adres IP (na przykład **10.32.0.10**).
        1.  Wybierz przycisk **OK**.
        1.  Po utworzeniu nowej puli adresów IP frontonu Zanotuj adres IP puli.
    1.  Następnie Utwórz pulę zaplecza:
        1.  Otwórz moduł równoważenia obciążenia, wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
        1.  Wprowadź nazwę nowej puli zaplecza (na przykład **Hana-zaplecze**).
        1.  Wybierz pozycję **Dodaj maszynę wirtualną**.
        1.  Wybierz pozycję * * maszyna wirtualna * *.
        1.  Wybierz Maszyny wirtualne klastra SAP HANA i ich adresy IP.
        1.  Wybierz pozycję **Dodaj**.
    1.  Następnie utwórz sondę kondycji:
        1.  Otwórz moduł równoważenia obciążenia, wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
        1.  Wprowadź nazwę nowej sondy kondycji (na przykład **Hana-HP**).
        1.  Wybierz pozycję TCP jako protokół i port 625**03**. Pozostaw wartość **interwału** ustawioną na 5, a wartość **progowa złej kondycji** równa 2.
        1.  Wybierz przycisk **OK**.
    1.  Następnie utwórz reguły równoważenia obciążenia:
        1.  Otwórz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
        1.  Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **Hana-lb**).
        1.  Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Hana-fronton**, **Hana — zaplecze** i **Hana-HP**).
        1.  Wybierz pozycję **porty ha**.
        1.  Zwiększ **limit czasu bezczynności** do 30 minut.
        1.  Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.
        1.  Wybierz przycisk **OK**.

> [!NOTE] 
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie będzie wychodzące połączenie z Internetem, chyba że zostanie przeprowadzona dodatkowa konfiguracja zezwalająca na kierowanie do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).

9. Alternatywnie, jeśli scenariusz podyktuje przy użyciu podstawowego modułu równoważenia obciążenia, wykonaj następujące czynności konfiguracyjne:
    1.  Skonfiguruj moduł równoważenia obciążenia. Najpierw Utwórz pulę adresów IP frontonu:
        1.  Otwórz moduł równoważenia obciążenia, wybierz pozycję **Pula adresów IP frontonu**, a następnie wybierz pozycję **Dodaj**.
        1.  Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **Hana-fronton**).
        1.  Ustaw **przypisanie** na **static** i wprowadź adres IP (na przykład **10.32.0.10**).
        1.  Wybierz przycisk **OK**.
        1.  Po utworzeniu nowej puli adresów IP frontonu Zanotuj adres IP puli.
    1.  Następnie Utwórz pulę zaplecza:
        1.  Otwórz moduł równoważenia obciążenia, wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
        1.  Wprowadź nazwę nowej puli zaplecza (na przykład **Hana-zaplecze**).
        1.  Wybierz pozycję **Dodaj maszynę wirtualną**.
        1.  Wybierz zestaw dostępności utworzony w kroku 3.
        1.  Wybierz Maszyny wirtualne klastra SAP HANA.
        1.  Wybierz przycisk **OK**.
    1.  Następnie utwórz sondę kondycji:
        1.  Otwórz moduł równoważenia obciążenia, wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
        1.  Wprowadź nazwę nowej sondy kondycji (na przykład **Hana-HP**).
        1.  Wybierz pozycję **TCP** jako protokół i port 625**03**. Pozostaw wartość **interwału** ustawioną na 5, a wartość **progowa złej kondycji** równa 2.
        1.  Wybierz przycisk **OK**.
    1.  W przypadku SAP HANA 1,0 Utwórz reguły równoważenia obciążenia:
        1.  Otwórz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
        1.  Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład Hana-lb-3**03**15).
        1.  Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Hana-fronton**).
        1.  Pozostaw **Protokół** ustawiony na **TCP**i wprowadź port 3**03**15.
        1.  Zwiększ **limit czasu bezczynności** do 30 minut.
        1.  Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.
        1.  Wybierz przycisk **OK**.
        1.  Powtórz te kroki dla portu 3**03**17.
    1.  W przypadku SAP HANA 2,0 Utwórz reguły równoważenia obciążenia dla systemowej bazy danych:
        1.  Otwórz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
        1.  Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład Hana-lb-3**03**13).
        1.  Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Hana-fronton**).
        1.  Pozostaw **Protokół** ustawiony na **TCP**i wprowadź port 3**03**13.
        1.  Zwiększ **limit czasu bezczynności** do 30 minut.
        1.  Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.
        1.  Wybierz przycisk **OK**.
        1.  Powtórz te kroki dla portu 3**03**14.
    1.  W przypadku SAP HANA 2,0 najpierw utwórz reguły równoważenia obciążenia dla bazy danych dzierżawcy:
        1.  Otwórz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
        1.  Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład Hana-lb-3**03**40).
        1.  Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Hana-fronton**).
        1.  Pozostaw **Protokół** ustawiony na **TCP**i wprowadź port 3**03**40.
        1.  Zwiększ **limit czasu bezczynności** do 30 minut.
        1.  Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.
        1.  Wybierz przycisk **OK**.
        1.  Powtórz te kroki dla portów 3**03**41 i 3**03**42.

Aby uzyskać więcej informacji na temat wymaganych portów dla SAP HANA, zapoznaj się z rozdziałem [połączenia z bazami danych dzierżawy](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) w Przewodniku obsługi [bazy danych dzierżaw SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) lub Uwaga [2388694](https://launchpad.support.sap.com/#/notes/2388694).

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych protokołu TCP na maszynach wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie sygnatur czasowych protokołu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.IPv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md). Zobacz również artykuł SAP Uwaga [2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Zainstaluj wolumin Azure NetApp Files

1. **[A]** Utwórz punkty instalacji dla woluminów bazy danych Hana. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** Sprawdź ustawienie domeny NFS. Upewnij się, że domena jest skonfigurowana jako domyślna domena Azure NetApp Files, np. **defaultv4iddomain.com** , a mapowanie jest ustawione na wartość **nikt**.

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
    > Upewnij się, że ustawiono domenę systemu plików NFS w/etc/idmapd.conf na maszynie wirtualnej tak, aby była zgodna z domyślną konfiguracją domeny w Azure NetApp Files: **defaultv4iddomain.com**. Jeśli istnieje niezgodność między konfiguracją domeny na kliencie NFS (tj. maszyną wirtualną) a serwerem NFS, tj. konfiguracją usługi Azure NetApp, uprawnienia do plików na woluminach NetApp platformy Azure, które są zainstalowane na maszynach wirtualnych, będą wyświetlane jako nikt.
    

3. **[1]** Zainstaluj woluminy specyficzne dla węzła w Węzeł1 (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** Zainstaluj woluminy specyficzne dla węzła w Węzeł2 (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** Sprawdź, czy wszystkie woluminy Hana są zainstalowane przy użyciu protokołu NFS w wersji NFSv4.

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

6. **[A]** Sprawdź **nfs4_disable_idmapping**. Powinna być ustawiona na wartość **Y**. Aby utworzyć strukturę katalogów, w której znajduje się **nfs4_disable_idmapping** , wykonaj polecenie instalacji. Nie będzie można ręcznie utworzyć katalogu w obszarze/sys/modules, ponieważ dostęp jest zarezerwowany dla jądra/sterowników.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Aby uzyskać więcej informacji na temat zmiany **nfs_disable_idmapping** parametru, zobacz [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>Instalacja SAP HANA

1. **[A]** Skonfiguruj rozpoznawanie nazw hostów dla wszystkich hostów.

   Możesz użyć serwera DNS lub zmodyfikować plik/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts. Zastąp adres IP i nazwę hosta w następujących poleceniach:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL for Hana — konfiguracja

   Skonfiguruj RHEL zgodnie z opisem w poniższej uwadze SAP na podstawie wersji RHEL

   - [2292690 — SAP HANA DB: zalecane ustawienia systemu operacyjnego dla RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 — SAP HANA DB: zalecane ustawienia systemu operacyjnego dla RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 — Linux: uruchamianie aplikacji SAP skompilowanych z usługą w zatoce 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 — Linux: uruchamianie aplikacji SAP skompilowanych z usługą w zatoce 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 — Linux: uruchamianie aplikacji SAP skompilowanych z usługą w zatoce 9. x](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** Zainstaluj SAP HANA

   Rozpoczęto od platformy HANA 2,0 SPS 01, MDC jest opcją domyślną. Podczas instalowania systemu HANA system SYSTEMDB i dzierżawca z tym samym identyfikatorem SID zostanie utworzony razem. W niektórych przypadkach nie potrzebujesz domyślnej dzierżawy. Jeśli nie chcesz tworzyć początkowej dzierżawy wraz z instalacją, możesz użyć uwagi SAP [2629711](https://launchpad.support.sap.com/#/notes/2629711)

    Uruchom program **hdblcm** z dysku DVD platformy Hana. W wierszu polecenia wprowadź następujące wartości:  
    Wybierz pozycję Instalacja: wprowadź **1** (na potrzeby instalacji)  
    Wybierz dodatkowe składniki do instalacji: wprowadź **1**.  
    Wprowadź ścieżkę instalacji [/Hana/Shared]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Wprowadź nazwę hosta lokalnego [..]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Czy chcesz dodać do systemu dodatkowe hosty? (t/n) [n]: **n**  
    Wprowadź identyfikator systemu SAP HANA: wprowadź **HN1**.  
    Wprowadź numer wystąpienia [00]: wprowadź **03**  
    Wybierz tryb bazy danych/wprowadź indeks [1]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Wybieranie opcji użycie systemu/wprowadzanie indeksu [4]: wprowadź **4** (dla niestandardowych)  
    Wprowadź lokalizację woluminów danych [/Hana/data]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Wprowadź lokalizację woluminów dziennika [/Hana/log]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Ograniczyć maksymalną alokację pamięci? [n]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Wprowadź nazwę hosta certyfikatu dla hosta "..." [...]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Wprowadź hasło użytkownika agenta hosta SAP (sapadm): wprowadź hasło użytkownika agenta hosta  
    Potwierdź hasło użytkownika agenta hosta SAP (sapadm): Wprowadź ponownie hasło użytkownika agenta hosta, aby potwierdzić  
    Wprowadź hasło administratora systemu (hn1adm): wprowadź hasło administratora systemu  
    Potwierdź hasło administratora systemu (hn1adm): Wprowadź ponownie hasło administratora systemu, aby potwierdzić  
    Wprowadź katalog macierzysty administratora systemu [/usr/sap/HN1/home]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Wprowadź powłokę logowania administratora systemu [/bin/sh]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Wprowadź identyfikator użytkownika administratora systemu [1001]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Wprowadź identyfikator grupy użytkowników (sapsys) [79]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Wprowadź hasło użytkownika bazy danych (SYSTEM): wprowadź hasło użytkownika bazy danych  
    Potwierdź hasło użytkownika bazy danych (SYSTEM): Wprowadź ponownie hasło użytkownika bazy danych, aby je potwierdzić  
    Czy uruchomić ponownie system po ponownym uruchomieniu komputera? [n]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną  
    Czy chcesz kontynuować? (t/n): Sprawdź poprawność podsumowania. Wprowadź **y** , aby kontynuować  

4. **[A]** Uaktualnij agenta hosta SAP

   Pobierz najnowszą archiwum agenta hosta SAP z [centrum oprogramowania SAP](https://launchpad.support.sap.com/#/softwarecenter) i uruchom następujące polecenie, aby uaktualnić agenta. Zastąp ścieżkę do archiwum, aby wskazywało pobrany plik:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** Konfigurowanie zapory

   Utwórz regułę zapory dla portu sondowania modułu równoważenia obciążenia platformy Azure.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>Konfigurowanie replikacji systemu SAP HANA

Wykonaj kroki opisane w temacie Konfigurowanie [replikacji systemu SAP HANA](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) , aby skonfigurować SAP HANA replikację systemu. 

## <a name="cluster-configuration"></a>Konfiguracja klastra

W tej sekcji opisano niezbędne kroki, które należy wykonać, aby klaster działał bezproblemowo po zainstalowaniu SAP HANA w udziałach NFS przy użyciu Azure NetApp Files. 

### <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w temacie [Konfigurowanie Pacemaker Red Hat Enterprise Linux na](./high-availability-guide-rhel-pacemaker.md) platformie Azure, aby utworzyć podstawowy klaster Pacemaker dla tego serwera platformy Hana.

### <a name="configure-filesystem-resources"></a>Konfigurowanie zasobów systemu plików

W tym przykładzie każdy węzeł klastra ma własne systemy plików NFS w systemie/Hana/Shared,/Hana/Data i/Hana/log.   

1. **[1]** Przełącz klaster w tryb konserwacji.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** Utwórz zasoby systemu plików dla instalacji **hanadb1** .

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** Utwórz zasoby systemu plików dla instalacji **hanadb2** .

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` atrybut jest dodawany do operacji monitorowania, tak że każdy monitor wykonuje test odczytu/zapisu w systemie plików. Bez tego atrybutu operacja monitorowania sprawdza tylko, czy system plików jest zainstalowany. Może to być spowodowane problemem, ponieważ po utracie łączności system plików może pozostać zainstalowany pomimo braku dostępu.

    `on-fail=fence` atrybut jest również dodawany do operacji monitorowania. W przypadku tej opcji, jeśli operacja monitorowania zakończy się niepowodzeniem w węźle, ten węzeł zostanie natychmiast ogrodzony. Bez tej opcji domyślnym zachowaniem jest zatrzymanie wszystkich zasobów, które są zależne od zasobu, a następnie ponowne uruchomienie zasobu, a następnie uruchomienie wszystkich zasobów zależnych od zasobu, który zakończył się niepowodzeniem. To działanie może nie tylko zająć dużo czasu, gdy zasób SAPHana zależy od zasobu, ale również może zakończyć się niepowodzeniem. Nie można zatrzymać zasobu SAPHana, jeśli serwer systemu plików NFS zawierający pliki wykonywalne HANA jest niedostępny.

4. **[1]** Konfigurowanie ograniczeń lokalizacji

   Skonfiguruj ograniczenia lokalizacji, aby upewnić się, że zasoby, które zarządzają hanadb1 unikatowymi instalacjami, nie mogą działać na hanadb2 i na odwrót.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    `resource-discovery=never`Opcja jest ustawiona, ponieważ unikatowe instalacje dla każdego węzła mają ten sam punkt instalacji. Na przykład `hana_data1` używa punktu instalacji `/hana/data` , a `hana_data2` także punktu instalacji `/hana/data` . Może to spowodować fałszywe pozytywne dla operacji sondowania, gdy stan zasobu jest sprawdzany podczas uruchamiania klastra, co może spowodować niepotrzebne zachowanie funkcji odzyskiwania. Można to uniknąć przez ustawienie `resource-discovery=never`

5. **[1]** Konfigurowanie zasobów atrybutów

   Skonfiguruj zasoby atrybutów. Te atrybuty zostaną ustawione na wartość true, jeśli są zainstalowane wszystkie instalacje systemu plików NFS (/Hana/Data,/Hana/log i/Hana/Data) węzła, a w przeciwnym razie zostanie ustawiona wartość false.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** Konfigurowanie ograniczeń lokalizacji

   Skonfiguruj ograniczenia lokalizacji, aby upewnić się, że zasób atrybutu hanadb1's nigdy nie działa na hanadb2 i na odwrót.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** tworzenie ograniczeń porządkowania

   Skonfiguruj ograniczenia kolejności, tak aby zasoby atrybutów węzła były uruchamiane dopiero po zainstalowaniu wszystkich instalacji systemu plików NFS w węźle.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Jeśli konfiguracja obejmuje systemy plików, poza grupą `hanadb1_nfs` lub `hanadb2_nfs` , należy uwzględnić `sequential=false` opcję, tak aby nie występowały zależności między systemami plików. Wszystkie systemy plików muszą zaczynać się przed `hana_nfs1_active` , ale nie muszą być uruchamiane w żadnej kolejności względem siebie. Aby uzyskać więcej informacji, zobacz [Jak mogę skonfigurować replikację systemu SAP HANA w Scale-Up w klastrze Pacemaker, gdy systemy plików Hana znajdują się w udziałach NFS](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>Konfigurowanie zasobów klastra SAP HANA

1. Wykonaj kroki opisane w temacie [Tworzenie zasobów klastra SAP HANA](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) , aby utworzyć zasoby SAP HANA w klastrze. Po utworzeniu SAP HANA zasobów musimy utworzyć ograniczenie dla reguły lokalizacji między zasobami SAP HANA i systemem plików (instalacjami systemu plików NFS)

2. **[1]** Skonfiguruj ograniczenia między zasobami SAP HANA i instalacjami systemu plików NFS

   Ograniczenia reguły lokalizacji zostaną ustawione tak, aby zasoby SAP HANA mogły być uruchamiane w węźle tylko wtedy, gdy zostaną zainstalowane wszystkie instalacje systemu plików NFS.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Sprawdź stan klastra i wszystkich zasobów
   > [!NOTE]
   > Ten artykuł zawiera odwołania do warunku *podrzędnego*, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.
   
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

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano, jak można testować konfigurację. 

1. Przed rozpoczęciem testu upewnij się, że Pacemaker nie ma żadnej akcji zakończonej niepowodzeniem (za pośrednictwem stanu komputerów), nie ma ograniczeń dotyczących lokalizacji (na przykład pozostałości testu migracji) i czy replikacja systemu HANA jest stanem synchronizacji, na przykład z systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Weryfikowanie konfiguracji klastra dla scenariusza awarii, gdy węzeł utraci dostęp do udziału NFS (/Hana/Shared)

   Agenci zasobów SAP HANA zależą od plików binarnych przechowywanych w programie w `/hana/shared` celu wykonywania operacji w trybie failover. System plików  `/hana/shared` jest instalowany za pośrednictwem systemu plików NFS w przedstawionym scenariuszu.  
   Trudne jest symulowanie błędu, gdy jeden z serwerów utraci dostęp do udziału NFS. Test, który można wykonać, polega na ponownym zainstalowaniu systemu plików jako tylko do odczytu.
   To podejście sprawdza, czy klaster będzie w stanie przełączyć się w tryb failover, jeśli dostęp do niego `/hana/shared` zostanie utracony w aktywnym węźle.     


   **Oczekiwany wynik:** W przypadku `/hana/shared` systemu plików tylko do odczytu `OCF_CHECK_LEVEL` atrybut zasobu `hana_shared1` , który wykonuje operację odczytu/zapisu w systemie plików, nie powiedzie się, ponieważ nie będzie można zapisać niczego w systemie plików i przetworzyć w trybie failover zasobów platformy Hana.  Ten sam wynik jest oczekiwany, gdy węzeł HANA utraci dostęp do udziałów NFS. 

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

   /Hana/Shared można umieścić w trybie tylko do odczytu w aktywnym węźle klastra przy użyciu poniższego polecenia:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 uruchomi ponownie lub wyłączenie na podstawie akcji ustawionej na stonith ( `pcs property show stonith-action` ).  Gdy serwer (hanadb1) nie działa, zasób HANA jest przenoszony do hanadb2. Stan klastra można sprawdzić z hanadb2.

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

   Zalecamy dokładne przetestowanie konfiguracji klastra SAP HANA, wykonując także testy opisane w [instalatorze SAP HANA replikacji systemu na RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup).