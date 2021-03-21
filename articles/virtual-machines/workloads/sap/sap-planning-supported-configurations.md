---
title: 'SAP na platformie Azure: obsługiwane scenariusze z maszynami wirtualnymi platformy Azure'
description: Platforma Azure Virtual Machines obsługiwane scenariusze z obciążeniem SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 274db058f9f291d720fc350bb23f7bfdde2791e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670940"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Obsługiwane scenariusze obciążenia SAP na maszynie wirtualnej na platformie Azure
Projektowanie architektury SAP NetWeaver, Business One `Hybris` lub S/4HANA system na platformie Azure otwiera wiele różnych możliwości dla różnych architektur i narzędzi, które mogą być używane do skalowalnego, wydajnego i wysokiej dostępności wdrożenia. Jednak zależnie od używanego systemu operacyjnego lub DBMS istnieją ograniczenia. Ponadto nie wszystkie scenariusze, które są obsługiwane lokalnie, są obsługiwane w taki sam sposób na platformie Azure. Ten dokument przeprowadzi Cię przez obsługiwane konfiguracje bez wysokiej dostępności i konfiguracje wysokiej dostępności oraz architektury korzystające wyłącznie z maszyn wirtualnych platformy Azure. W przypadku scenariuszy obsługiwanych z [dużymi wystąpieniami Hana](./hana-overview-architecture.md)zapoznaj się z artykułem [obsługiwane scenariusze dotyczące dużych wystąpień platformy Hana](./hana-supported-scenario.md). 


## <a name="2-tier-configuration"></a>Konfiguracja 2-warstwowa
Konfiguracja systemu SAP 2-warstwowa jest traktowana jako wbudowana w połączeniu z warstwą systemu SAP DBMS i warstwy aplikacji, która działa na tym samym serwerze lub jednostce maszyn wirtualnych. Druga warstwa jest traktowana jako warstwa interfejsu użytkownika. W przypadku konfiguracji 2-warstwowej warstwa aplikacji systemów DBMS i SAP udostępnia zasoby maszyny wirtualnej platformy Azure. W związku z tym należy skonfigurować różne składniki w taki sposób, aby nie konkurują się z zasobami. Należy również zachować ostrożność, aby nie subskrybować zasobów maszyny wirtualnej. Taka konfiguracja nie zapewnia wysokiej dostępności, poza umowami dotyczącymi [poziomu usług platformy Azure](https://azure.microsoft.com/support/legal/sla/) dla różnych składników platformy Azure.

Graficzna reprezentacja takiej konfiguracji może wyglądać następująco:

![Prosta konfiguracja 2-warstwowa](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Takie konfiguracje są obsługiwane w systemach Windows, Red Hat, SUSE i Oracle Linux dla systemów DBMS SQL Server, Oracle, DB2, maxDB i SAP ASE w przypadku środowisk produkcyjnych i nieprodukcyjnych. W przypadku SAP HANA jako systemu DBMS taka konfiguracja jest obsługiwana tylko w przypadku przypadków nieprodukcyjnych. Obejmuje to również przypadek wdrożenia [dużych wystąpień platformy Azure Hana](./hana-overview-architecture.md) .
W przypadku wszystkich kombinacji systemu operacyjnego/DBMS obsługiwanych na platformie Azure ten typ konfiguracji jest obsługiwany. Należy jednak określić konfigurację systemu DBMS i składników SAP w taki sposób, że składniki systemów DBMS i SAP nie konkurują o pamięć i zasoby procesora CPU, a tym samym przekraczają fizyczne dostępne zasoby. Należy to zrobić, ograniczając pamięć, którą może przydzielić system DBMS. Należy również ograniczyć pamięć rozszerzoną systemu SAP w wystąpieniach aplikacji. Należy również monitorować użycie procesora CPU przez maszynę wirtualną w celu upewnienia się, że składniki nie maksymalizują zasobów procesora CPU. 

> [!NOTE]
> W przypadku produkcyjnych systemów SAP zalecamy dodatkowe konfiguracje wysokiej dostępności i ostatecznego odzyskiwania po awarii, zgodnie z opisem w dalszej części tego dokumentu.


## <a name="3-tier-configuration"></a>Konfiguracja 3-warstwowa
W takich konfiguracjach warstwa aplikacji SAP i warstwa DBMS są oddzielane do różnych maszyn wirtualnych. Zwykle jest to potrzebne w przypadku większych systemów i z przyczyn bardziej elastycznej od zasobów warstwy aplikacji SAP. W najprostszej konfiguracji nie ma wysokiej dostępności poza [umowami dotyczącymi poziomu usług platformy Azure](https://azure.microsoft.com/support/legal/sla/) dla różnych składników platformy Azure. 

Graficzna reprezentacja wygląda następująco:

![Diagram, który pokazuje prostą konfigurację 3-warstwową.](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Ten typ konfiguracji jest obsługiwany w systemie Windows, Red Hat, SUSE i Oracle Linux w systemach DBMS systemów SQL Server, Oracle, DB2, SAP HANA, maxDB i SAP ASE w przypadku środowisk produkcyjnych i nieprodukcyjnych. Jest to domyślna konfiguracja wdrożenia dla [dużych wystąpień platformy Azure Hana](./hana-overview-architecture.md). W celu uproszczenia nie rozróżnienie między wystąpieniami okna dialogowego SAP Central Services i SAP w warstwie aplikacji SAP. W tej prostej konfiguracji 3-warstwowej nie ma żadnej ochrony o wysokiej dostępności dla usług SAP Central.

> [!NOTE]
> W przypadku produkcyjnych systemów SAP zalecamy dodatkowe konfiguracje wysokiej dostępności i ostatecznego odzyskiwania po awarii, zgodnie z opisem w dalszej części tego dokumentu.


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Wiele wystąpień systemu DBMS na maszynę wirtualną lub jednostkę dużego wystąpienia HANA
Ten typ konfiguracji umożliwia hostowanie wielu wystąpień systemu DBMS na maszynę wirtualną platformy Azure lub w jednostce dużego wystąpienia HANA. Motywacja może mieć mniejsze systemy operacyjne, aby zachować i zmniejszyć koszty. Inne motywacje mogą mieć większą elastyczność i lepszą wydajność dzięki udostępnieniu zasobów większej lub dużej jednostce wystąpienia platformy HANA między wieloma wystąpieniami systemu DBMS. Do tej pory te konfiguracje były widoczne głównie dla systemów nieprodukcyjnych.

Taka konfiguracja może wyglądać następująco:

![Wiele wystąpień systemu DBMS w jednej jednostce](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Ten typ wdrożenia systemu DBMS jest obsługiwany przez:

- SQL Server w systemie Windows
- IBM DB2. Znajdowanie szczegółowych informacji w artykule [wiele wystąpień (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Dla firmy Oracle. Aby uzyskać szczegółowe informacje, zobacz [uwagi dotyczące pomocy technicznej sap #1778431](https://launchpad.support.sap.com/#/notes/1778431) i powiązane uwagi SAP
- W przypadku SAP HANA, jest obsługiwana wiele wystąpień na jednej maszynie wirtualnej, a rozwiązanie SAP wywołuje tę metodę wdrażania MCOS. Aby uzyskać szczegółowe informacje, zobacz artykuł SAP [wiele SAP HANA systemów na jednym hoście (MCOS)] (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Uruchamianie wielu wystąpień bazy danych na jednym hoście, należy się upewnić, że różne wystąpienia nie konkurują o zasoby, a tym samym przekraczają limity zasobów fizycznych maszyny wirtualnej. Jest to szczególnie istotne w przypadku pamięci, w której należy zwolnić pamięć, którą mogą przydzielić każdy z wystąpień, które udostępniają maszynę wirtualną. Może to również dotyczyć zasobów procesora CPU, których mogą użyć różne wystąpienia bazy danych. Wszystkie wymienione systemy DBMS mają konfiguracje umożliwiające ograniczenie alokacji pamięci i zasobów procesora na poziomie wystąpienia.
Aby można było obsłużyć taką konfigurację dla maszyn wirtualnych platformy Azure, oczekuje się, że dyski lub woluminy, które są używane dla plików dziennika danych i dzienników/ponawiania baz danych zarządzanych przez różne wystąpienia, są oddzielone. Lub innymi słowy pliki dzienników danych lub dzienników/ponawiania baz danych zarządzanych przez różne wystąpienie systemu DBMS nie powinny udostępniać tych samych dysków ani woluminów. 

Konfiguracja dysków dla dużych wystąpień platformy HANA została skonfigurowana i jest szczegółowo opisana w temacie [obsługiwane scenariusze dla dużych wystąpień platformy Hana](./hana-supported-scenario.md#single-node-mcos). 

> [!NOTE]
> W przypadku produkcyjnych systemów SAP zalecamy dodatkowe konfiguracje wysokiej dostępności i ostatecznego odzyskiwania po awarii, zgodnie z opisem w dalszej części tego dokumentu. Maszyny wirtualne z wieloma wystąpieniami systemu DBMS nie są obsługiwane przez konfiguracje wysokiej dostępności opisane w dalszej części tego dokumentu.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Wiele wystąpień okna dialogowego SAP w jednej maszynie wirtualnej
W wielu przypadkach wiele wystąpień okna dialogowego zostało wdrożonych na serwerach bez systemu operacyjnego lub nawet na maszynach wirtualnych działających w chmurach prywatnych. Przyczyną takich konfiguracji było dostosowanie niektórych wystąpień okna dialogowego SAP do określonych obciążeń, funkcjonalności biznesowej lub typów obciążeń. Powodem braku izolowania tych wystąpień do osobnych maszyn wirtualnych była nakład pracy z konserwacją i eksploatacją systemu operacyjnego. Lub w licznych przypadkach koszty w przypadku dostawcy usług hostingowych lub operatora maszyny wirtualnej żądają miesięcznej opłaty za maszynę wirtualną, która jest zarządzana. Na platformie Azure scenariusz udostępniania wielu wystąpień okna dialogowego SAP w ramach jednej maszyny wirtualnej w Stanach Zjednoczonych jest obsługiwany na potrzeby produkcji i nieprodukcyjnej w systemach operacyjnych Windows, Red Hat, SUSE i Oracle Linux. Parametr jądra SAP PHYS_MEMSIZE, dostępny w systemie Windows i nowoczesnych jądra systemu Linux, należy ustawić, jeśli na jednej maszynie wirtualnej są uruchomione wiele wystąpień serwera aplikacji SAP. Zaleca się również ograniczenie rozszerzania pamięci rozszerzonej SAP w systemach operacyjnych, takich jak system Windows, w którym zaimplementowano automatyczne przyrosty pamięci rozszerzonej SAP. Można to zrobić przy użyciu parametru profilu SAP `em/max_size_MB` .

W konfiguracji 3-warstwowej, w której wiele wystąpień okna dialogowego SAP jest uruchamianych na maszynach wirtualnych platformy Azure, może wyglądać następująco:

![Diagram przedstawiający konfigurację 3-warstwową, w której wiele wystąpień okna dialogowego SAP jest uruchamianych na maszynach wirtualnych platformy Azure.](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

W celu uproszczenia nie rozróżnienie między wystąpieniami okna dialogowego SAP Central Services i SAP w warstwie aplikacji SAP. W tej prostej konfiguracji 3-warstwowej nie ma żadnej ochrony o wysokiej dostępności dla usług SAP Central. W przypadku systemów produkcyjnych nie zaleca się pozostawiania usług SAP centralnych, które nie są chronione. W przypadku określonych elementów o nazwie wiele identyfikatorów SID dotyczących wystąpień oprogramowania SAP Central i wysokiej dostępności takich konfiguracji z obsługą wiele identyfikatorów SID Zobacz dalsze sekcje tego dokumentu.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Ochrona wysokiej dostępności dla warstwy systemu SAP DBMS
Podczas wdrażania systemów produkcyjnych SAP należy wziąć pod uwagę typ aktywnego stanu gotowości konfiguracje o wysokiej dostępności. W szczególności w przypadku SAP HANA, w których dane muszą zostać załadowane do pamięci, zanim będzie można uzyskać pełną wydajność i skalowalność, Naprawianie usługi platformy Azure nie jest idealnym rozwiązaniem dla wysokiej dostępności. 

Ogólnie rzecz biorąc, firma Microsoft obsługuje tylko konfiguracje wysokiej dostępności i pakiety oprogramowania, które są opisane w sekcji obciążenie SAP w docs.microsoft.com. Tę samą instrukcję można odczytać w artykule SAP uwagi [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Firma Microsoft nie będzie zapewniać wsparcia dla innych platform oprogramowania innych firm, które nie są udokumentowane przez firmę Microsoft w połączeniu z obciążeniem SAP. W takich przypadkach dostawca usług o wysokiej dostępności jest podmiotem pomocniczym do konfiguracji wysokiej dostępności, która musi być zaangażowana przez klienta w proces pomocy technicznej. Wyjątki są wymienione w tym artykule. 

Ogólnie rzecz biorąc, firma Microsoft obsługuje ograniczoną konfigurację wysokiej dostępności na maszynach wirtualnych platformy Azure lub w jednostkach dużych wystąpień HANA. W przypadku scenariuszy z dużymi wystąpieniami HANA zapoznaj się z dokumentem [obsługiwane scenariusze dotyczące dużych wystąpień usługi Hana](./hana-supported-scenario.md).

W przypadku maszyn wirtualnych platformy Azure następujące konfiguracje wysokiej dostępności są obsługiwane na poziomie systemu DBMS:

- SAP HANA replikację systemu opartą na systemie Linux Pacemaker na SUSE i Red Hat. Zobacz szczegółowe artykuły:
    - [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
    - [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)
- SAP HANA skalowalne w poziomie konfiguracje n + m używające [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) na SUSE i Red Hat. Szczegóły znajdują się w następujących artykułach:
    - [Wdróż system SAP HANA skalowalny w poziomie za pomocą węzła Standby na maszynach wirtualnych platformy Azure przy użyciu Azure NetApp Files na SUSE Linux Enterprise Server}](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Wdróż system SAP HANA skalowalny w poziomie z aktywnym węzłem na maszynach wirtualnych platformy Azure przy użyciu Azure NetApp Files na Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- SQL Server klaster trybu failover na podstawie usług plików Scale-Out systemu Windows. Mimo że zalecenie dotyczące systemów produkcyjnych to użycie usługi SQL Server zawsze włączone zamiast klastrowania. SQL Server zawsze włączona zapewnia lepszą dostępność przy użyciu oddzielnego magazynu. Szczegóły zostały opisane w tym artykule: 
    - [Konfigurowanie SQL Server wystąpienia klastra trybu failover na maszynach wirtualnych platformy Azure](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- Usługa SQL Server zawsze włączona jest obsługiwana w systemie operacyjnym Windows dla SQL Server na platformie Azure. Jest to domyślne zalecenie dla wystąpień SQL Server produkcyjnych na platformie Azure. Szczegóły są opisane w następujących artykułach:
    - [Wprowadzenie SQL Server zawsze dostępnych grup dostępności na maszynach wirtualnych platformy Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).
    - [Skonfiguruj zawsze włączona Grupa dostępności na maszynach wirtualnych platformy Azure w różnych regionach](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
    - [Skonfiguruj moduł równoważenia obciążenia dla zawsze włączonej grupy dostępności na platformie Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).
- Funkcja Oracle Data Guard dla systemu Windows i Oracle Linux. Szczegóły dotyczące Oracle Linux można znaleźć w tym artykule:
    - [Implementowanie funkcji Oracle Data Guard na maszynie wirtualnej platformy Azure z systemem Linux](../oracle/configure-oracle-dataguard.md)
- Program IBM DB2 HADR Cluster na SUSE i RHEL szczegółowej dokumentacji dotyczącej SUSE i RHEL za pomocą Pacemaker jest dostępny tutaj:
    - [Wysoka dostępność programu IBM DB2 LUW na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Pacemaker](./dbms-guide-ha-ibm.md)
    - [Wysoka dostępność programu IBM Db2 LUW na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux Server](./high-availability-guide-rhel-ibm-db2-luw.md)
- Konfiguracja oprogramowania SAP ASE i SAP maxDB zgodnie z opisem w następujących dokumentach:
    - [Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP](./dbms_guide_sapase.md)
    - [Wdrażanie oprogramowania SAP MaxDB, liveCache i Content Server na maszynach wirtualnych platformy Azure](./dbms_guide_maxdb.md)
- Scenariusze wysokiej dostępności dla dużych wystąpień platformy HANA są szczegółowo opisane w:
    - [Obsługiwane scenariusze dla dużych wystąpień platformy HANA — HSR z STONITH w celu zapewnienia wysokiej dostępności](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [Obsługiwane scenariusze dla dużych wystąpień HANA — automatycznie przełączenia do trybu failover hosta (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> W przypadku żadnego z opisanych powyżej scenariuszy obsługiwane są konfiguracje wielu wystąpień systemu DBMS na jednej maszynie wirtualnej. W każdym z tych przypadków można wdrożyć tylko jedno wystąpienie bazy danych na maszynę wirtualną i chronić je za pomocą opisanych metod wysokiej dostępności. Ochrona wielu wystąpień DBMS w ramach tego samego klastra trybu failover systemu Windows lub Pacemaker **nie** jest obsługiwana w tym momencie. Ponadto funkcja Oracle Data Guard jest obsługiwana tylko w przypadku pojedynczych wystąpień na maszynę wirtualną. 

Różne systemy baz danych umożliwiają hostowanie wielu baz danych w ramach jednego wystąpienia systemu DBMS. Tak jak w przypadku SAP HANA wiele baz danych może być hostowanych w wielu kontenerach baz danych (MDC). W przypadku, gdy te konfiguracje wielu baz danych działają w ramach jednego zasobu klastra trybu failover, te konfiguracje są obsługiwane. Konfiguracje, które nie są obsługiwane, są sytuacje, w których wymagane jest wiele zasobów klastra. Podobnie jak w przypadku konfiguracji, w których zdefiniowano wiele SQL Server grup dostępności, w ramach jednego wystąpienia SQL Server.


![Konfiguracja systemu DBMS HA](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

Zależnie od systemu operacyjnego lub systemów operacyjnych DBMS składniki, takie jak moduł równoważenia obciążenia platformy Azure, mogą lub nie muszą być wymagane w ramach architektury rozwiązania. 

W odniesieniu do maxDB Konfiguracja magazynu musi się różnić. W maxDB pliki danych i dziennika muszą znajdować się w magazynie udostępnionym dla konfiguracji o wysokiej dostępności. Tylko w przypadku maxDB magazyn udostępniony jest obsługiwany na potrzeby wysokiej dostępności. W przypadku wszystkich innych różnych stosów magazynów w systemie DBMS dla każdego węzła obsługiwane są tylko konfiguracje dysków.

Istnieją inne struktury wysokiej dostępności, które są znane i są również uruchamiane na Microsoft Azure. Jednak firma Microsoft nie przetestuje tych platform. Jeśli chcesz skompilować konfigurację wysokiej dostępności z tymi platformami, musisz skontaktować się z dostawcą tego oprogramowania, aby:

- Opracowywanie architektury wdrażania
- Wdrożenie architektury
- Obsługa architektury

> [!IMPORTANT]
> Microsoft Azure Marketplace oferuje różne urządzenia miękkie, które udostępniają rozwiązania magazynu w oparciu o magazyn natywny platformy Azure. Te urządzenia miękkie mogą służyć do tworzenia udziałów NFS, a teoretycznie mogą być używane w ramach wdrożeń skalowalnych w poziomie SAP HANA, w których wymagany jest węzeł w stanie wstrzymania. Ze względu na różne przyczyny żadne z tych urządzeń nie jest obsługiwane przez firmę Microsoft i oprogramowanie SAP na platformie Azure. W tym momencie wdrożenia systemu DBMS w udziałach SMB nie są obsługiwane. Wdrożenia systemu DBMS w udziałach NFS są ograniczone do udziałów NFS 4,1 w [Azure NetApp Files](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Wysoka dostępność dla usługi SAP Central
Usługa SAP Central Services to druga single point of failure konfiguracji oprogramowania SAP. W związku z tym należy również włączyć ochronę tych centralnych procesów usług. Oferta jest obsługiwana i udokumentowana w przypadku takich operacji, jak:

- Serwer klastra trybu failover systemu Windows korzystający z usług plików skalowalnych w poziomie systemu Windows dla sapmnt i globalnego katalogu transportowego. Szczegóły zostały opisane w artykule:
    - [Klastrowanie wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików na platformie Azure](./sap-high-availability-guide-wsfc-file-share.md)
    - [Przygotowanie infrastruktury platformy Azure pod kątem wysokiej dostępności SAP przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień oprogramowania SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Serwer klastra trybu failover systemu Windows korzystający z udziału SMB na podstawie [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) dla sapmnt i globalnego katalogu transportowego. Szczegóły znajdują się w artykule:
    - [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie Windows z Azure NetApp Files (SMB) dla aplikacji SAP](./high-availability-guide-windows-netapp-files-smb.md)
- Serwer klastra trybu failover systemu Windows oparty na oprogramowanie SIOS `Datakeeper` . Chociaż jest to udokumentowane przez firmę Microsoft, potrzebna jest relacja pomocy technicznej z oprogramowanie SIOS, dzięki czemu można korzystać z pomocy technicznej oprogramowanie SIOS w przypadku korzystania z tego rozwiązania. Szczegóły zostały opisane w artykule:
    - [Klastrowanie wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra na platformie Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [Przygotowanie infrastruktury platformy Azure dla oprogramowania SAP HA przy użyciu klastra trybu failover systemu Windows i dysku udostępnionego dla oprogramowania SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- Pacemaker w systemie SUSE system operacyjny, który umożliwia tworzenie udziału NFS o wysokiej dostępności przy użyciu dwóch maszyn wirtualnych SUSE i `drdb` na potrzeby replikacji plików. Szczegóły zostały udokumentowane w artykule
    - [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server dla aplikacji SAP](./high-availability-guide-suse.md)
    - [Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- System operacyjny Pacemaker SUSE wykorzystujący udziały NFS udostępniane przez [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Szczegóły są udokumentowane w
    - [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP](./high-availability-guide-suse-netapp-files.md)
- Pacemaker w systemie operacyjnym Red Hat z udziałem NFS hostowanym w `glusterfs` klastrze. Szczegółowe informacje można znaleźć w artykułach
    - [Platforma Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux](./high-availability-guide-rhel.md)
    - [`GlusterFS` na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)
- Pacemaker w systemie operacyjnym Red Hat z udziałem NFS hostowanym na [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Szczegóły zostały opisane w artykule
    - [Platforma Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux z Azure NetApp Files dla aplikacji SAP](./high-availability-guide-rhel-netapp-files.md)

Z wymienionych rozwiązań potrzebna jest relacja pomocy technicznej z oprogramowanie SIOSą do obsługi `Datakeeper` produktu oraz do zaangażowania się z oprogramowanie SIOS bezpośrednio w przypadku problemów. Zależnie od sposobu licencjonowania systemu Windows, Red Hat i/lub SUSE OS, może być również konieczne posiadanie kontraktu pomocy technicznej z dostawcą systemu operacyjnego w celu zapewnienia pełnej obsługi wymienionych konfiguracji o wysokiej dostępności.

Konfiguracja może być również wyświetlana na przykład:

![Konfiguracja systemu DBMS i ASCS HA](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

Po prawej stronie grafiki zostanie wyświetlona usługa SAP Central o wysokiej dostępności. Oprócz tego, że usługi SAP Central są chronione za pomocą platformy klastra trybu failover, która może działać w trybie awaryjnym w przypadku wystąpienia problemu, istnieje konieczność zapewnienia wysokiej dostępności systemu plików NFS lub udziału SMB lub dysku udostępnionego systemu Windows, aby upewnić się, że sapmnt i Globalny katalog transportu są dostępne niezależnie od istnienia pojedynczej maszyny wirtualnej. Dodatkowe rozwiązania, takie jak serwer klastrów trybu failover systemu Windows i Pacemaker, wymagają, aby moduł równoważenia obciążenia platformy Azure mógł bezpośrednio lub ponownie skierować ruch do węzła w dobrej kondycji.

Na wyświetlonej liście nie ma wzmianki o Oracle Linux systemie operacyjnym. Oracle Linux nie obsługuje Pacemaker jako platformy klastra. Jeśli chcesz wdrożyć system SAP na Oracle Linux i potrzebujesz struktury wysokiej dostępności dla Oracle Linux, musisz współpracować z dostawcami innych firm. Jeden z dostawców jest oprogramowanie SIOS z pakietem ochrony dla systemu Linux, który jest obsługiwany przez oprogramowanie SAP na platformie Azure. Więcej informacji można znaleźć w artykule SAP Uwaga [#1662610 — szczegóły pomocy technicznej dla programu oprogramowanie SIOS Protection Suite dla systemu Linux](https://launchpad.support.sap.com/#/notes/1662610) , aby uzyskać więcej informacji.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Obsługiwane magazyny w przypadku scenariuszy usług SAP centralnych wymienionych powyżej
Ponieważ tylko podzbiór typów magazynu platformy Azure zapewnia wysoką dostępność systemu plików NFS lub SMB, że jakość na potrzeby użycia w naszym scenariuszu klastra usług SAP Central Services ma listę obsługiwanych typów magazynu

- Serwer klastra trybu failover systemu Windows z serwerem plików skalowalnym w poziomie systemu Windows można wdrożyć na wszystkich natywnych typach usługi Azure Storage, z wyjątkiem Azure NetApp Files. Zaleca się jednak korzystanie z Premium Storage ze względu na wyższą umowę dotyczącą poziomu usług w przepływności i operacje we/wy na sekundę.
- Serwer klastra trybu failover systemu Windows z protokołem SMB on Azure NetApp Files jest obsługiwany przez Azure NetApp Files. Udziały SMB w usługach plików platformy Azure **nie** są obsługiwane w tym momencie.
- Serwer klastra trybu failover systemu Windows z dyskiem udostępnionym systemu Windows oparty na oprogramowanie SIOS `Datakeeper` można wdrożyć na wszystkich natywnych typach usługi Azure Storage, z wyjątkiem Azure NetApp Files. Zaleca się jednak korzystanie z Premium Storage ze względu na wyższą umowę dotyczącą poziomu usług w przepływności i operacje we/wy na sekundę.
- Program SUSE lub Red Hat Pacemaker korzystający z udziałów NFS na Azure NetApp Files jest obsługiwany przez Azure NetApp Files. 
- SUSE Pacemaker przy użyciu `drdb` konfiguracji między dwiema maszynami wirtualnymi jest obsługiwany przy użyciu natywnych typów magazynu platformy Azure, z wyjątkiem Azure NetApp Files. Zaleca się jednak korzystanie z Premium Storage ze względu na wyższą umowę dotyczącą poziomu usług w przepływności i operacje we/wy na sekundę.
- Red Hat Pacemaker używany `glusterfs` do udostępniania udziału NFS jest obsługiwany przy użyciu natywnych typów magazynu platformy Azure, z wyjątkiem Azure NetApp Files. Zaleca się jednak korzystanie z Premium Storage ze względu na wyższą umowę dotyczącą poziomu usług w przepływności i operacje we/wy na sekundę.

> [!IMPORTANT]
> Microsoft Azure Marketplace oferuje różne urządzenia miękkie, które udostępniają rozwiązania magazynu w oparciu o magazyn natywny platformy Azure. Te urządzenia miękkie mogą służyć do tworzenia udziałów w systemie plików NFS lub SMB, jak również teoretycznie mogą być używane w klastrach trybu failover z usługami SAP Central. Te rozwiązania nie są bezpośrednio obsługiwane dla obciążeń SAP przez firmę Microsoft. Jeśli zdecydujesz się użyć takiego rozwiązania do utworzenia udziału w systemie plików NFS lub SMB, obsługa konfiguracji usługi SAP Central musi być świadczona przez inną firmę będącą właścicielem oprogramowania w urządzeniu magazynującym magazynu.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Klastry trybu failover z obsługą wieloidentyfikatorowego protokołu SAP Central Services
Aby zmniejszyć liczbę maszyn wirtualnych potrzebnych w dużych landscapesach SAP, system SAP umożliwia uruchamianie wystąpień usług SAP Central w wielu różnych systemach SAP w konfiguracji klastra trybu failover. Wyobraź sobie, że masz 30 lub więcej systemów produkcyjnych NetWeaver lub 4HANA. Bez klastrowania z wieloma SIDmi te konfiguracje wymagają 60 lub więcej maszyn wirtualnych w 30 lub więcej konfiguracjach klastra trybu failover z systemem Windows lub Pacemaker. Oprócz potrzeb klastrów trybu failover systemu DBMS. Wdrożenie wielu usług SAP Central w dwóch węzłach w konfiguracji klastra trybu failover może znacząco obniżyć liczbę maszyn wirtualnych. Jednak wdrożenie wielu wystąpień usług SAP Central w jednej konfiguracji klastra z dwoma węzłami ma także pewne wady. Problemy dotyczące pojedynczej maszyny wirtualnej w konfiguracji klastra dotyczą wielu systemów SAP. Konserwacja w systemie operacyjnym gościa, w której działa Konfiguracja klastra, wymaga większej koordynacji, ponieważ wpływają na wiele systemów SAP w środowisku produkcyjnym. Narzędzia, takie jak SAP LaMa, nie obsługują klastrowania wieloidentyfikatorowego w procesie klonowania systemu.

Na platformie Azure jest obsługiwana konfiguracja klastra z obsługą wiele identyfikatorów SID dla systemu operacyjnego Windows z ENSA1 i ENSA2. Zalecenie nie łączy starszej architektury usługi replikacji w kolejce (ENSA1) z nową architekturą (ENSA2) w jednym klastrze z obsługą wielu identyfikatorów SID. Szczegółowe informacje o takiej architekturze zostały udokumentowane w artykułach

- [Rozwiązanie SAP ASCS/SCS o wysokiej dostępności z użyciem usługi Windows Server Failover Clustering i dysku udostępnionego na platformie Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [Rozwiązanie SAP ASCS/SCS o wysokiej dostępności z użyciem klastra trybu failover systemu Windows Server i udziału plików na platformie Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

W przypadku SUSE jest również obsługiwany klaster z obsługą wiele identyfikatorów SID oparty na Pacemaker. Do tej pory konfiguracja jest obsługiwana dla:

- Maksymalnie pięć wystąpień SAP ASCS/SCS
- Stara architektura lodu serwera replikacji w kolejce (ENSA1)
- Konfiguracja klastra z dwoma węzłami Pacemaker

Konfiguracja jest udokumentowana w [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure SUSE Linux Enterprise Server na platformie Microsoft dla aplikacji SAP Przewodnik dotyczący wiele identyfikatorów SID](./high-availability-guide-suse-multi-sid.md)

Klaster z obsługą identyfikatorów SID z przykładowym schematem serwera replikacji

![Diagram przedstawiający klaster z obsługą wiele identyfikatorów SID z serwerem replikacji z kolejki.](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA scenariusze skalowalne w poziomie
SAP HANA scenariusze skalowalne w poziomie są obsługiwane dla podzestawu certyfikowanych maszyn wirtualnych platformy Azure, które wymieniono w [katalogu SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Wszystkie maszyny wirtualne oznaczone jako "yes" w kolumnie "Clustering" mogą być używane do skalowania w poziomie OLAP lub S/4HANA. Konfiguracje bez wstrzymywania są obsługiwane w przypadku typów usługi Azure Storage: 

- Premium Storage platformy Azure, w tym akcelerator zapisu platformy Azure dla woluminu/Hana/log
- [Dysk w warstwie Ultra](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

SAP HANA skalowalne w poziomie konfiguracje dla OLAP lub S/4HANA z węzłami w stanie wstrzymania są obsługiwane wyłącznie w systemie plików NFS udostępnianym przez Azure NetApp Files.

Aby uzyskać więcej informacji na temat dokładnej konfiguracji magazynu z węzłem wstrzymywania lub bez niego, zapoznaj się z artykułami:

- [Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA](./hana-vm-operations-storage.md) 
- [Wdróż system SAP HANA skalowalny w poziomie z aktywnym węzłem na maszynach wirtualnych platformy Azure przy użyciu Azure NetApp Files na SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Wdróż system SAP HANA skalowalny w poziomie z aktywnym węzłem na maszynach wirtualnych platformy Azure przy użyciu Azure NetApp Files na Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [Uwaga dotycząca pomocy technicznej SAP #2080991](https://launchpad.support.sap.com/#/notes/2080991)

Aby uzyskać szczegółowe informacje o dużych wystąpieniach platformy HANA obsługiwane konfiguracje skalowalne w poziomie, zastosowana została następująca dokumentacja:

- [Scenariusze obsługiwane w przypadku dużych wystąpień platformy HANA w trybie rezerwy](./hana-supported-scenario.md#scale-out-with-standby)
- [Obsługiwane scenariusze dotyczące skalowania w poziomie dużych wystąpień platformy HANA bez rezerwy](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Scenariusz odzyskiwania po awarii
Obsługiwane są różne scenariusze odzyskiwania po awarii. Architekturę awaryjną definiujemy jako architekturę, która powinna kompensować wszystkie regiony platformy Azure, które wychodzą z siatki. Oznacza to, że potrzebujemy celu odzyskiwania po awarii, aby być innym regionem świadczenia usługi Azure jako obiektem docelowym w celu uruchomienia środowiska SAP krajobrazu. Oddzielamy metody i konfiguracje w warstwie systemu DBMS i w warstwie innej niż DBMS. 

### <a name="dbms-layer"></a>Warstwa DBMS
W przypadku warstwy DBMS konfiguracje są obsługiwane przy użyciu natywnych mechanizmów replikacji systemu DBMS, takich jak zawsze włączone, Oracle Data Guard, DB2 HADR Cluster, funkcja SAP ASE zawsze włączone lub replikacja systemowa HANA. Należy zastanowić się, że strumień replikacji w takich przypadkach jest asynchroniczny, a nie synchronicznie jak w przypadku typowych scenariuszy wysokiej dostępności, które są wdrażane w ramach jednego regionu świadczenia usługi Azure. Typowy przykład takiej obsługiwanej konfiguracji odzyskiwania po awarii w systemie DBMS został opisany w artykule [SAP HANA dostępność w różnych regionach platformy Azure](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions). Druga ilustracja w tej sekcji opisuje scenariusz z platformą HANA jako przykład. W takim scenariuszu można wdrożyć główne bazy danych obsługiwane przez aplikacje SAP.

Jest obsługiwane, aby użyć mniejszej maszyny wirtualnej jako wystąpienia docelowego w regionie odzyskiwania po awarii, ponieważ ta maszyna wirtualna nie jest w pełni obciążony ruchem. Należy pamiętać o następujących kwestiach:

- Mniejsze typy maszyn wirtualnych nie pozwalają na to, że wiele dysków jest dołączonych niż mniejsze maszyny wirtualne
- Mniejsze maszyny wirtualne mają mniejszą przepływność sieci i magazynu
- Zmiana rozmiarów w różnych rodzinach maszyn wirtualnych może być problemem, gdy różne maszyny wirtualne są zbierane w jednym zestawie dostępności platformy Azure lub gdy zmiana rozmiarów ma miejsce między rodziną serii M i rodziną maszyn wirtualnych Mv2
- Użycie procesora CPU i pamięci dla wystąpienia bazy danych może odbierać strumień zmian z minimalnym opóźnieniem i wystarczającą ilością zasobów procesora i pamięci, aby zastosować te zmiany z minimalnym opóźnieniem danych  

Więcej szczegółowych informacji na temat ograniczeń dotyczących różnych rozmiarów maszyn wirtualnych można znaleźć [tutaj](../../sizes.md) 

Inna obsługiwana metoda wdrażania obiektu docelowego odzyskiwania po awarii polega na tym, że na maszynie wirtualnej uruchomionej nieprodukcyjnego wystąpienia SAP nie jest zainstalowane drugie wystąpienie systemu DBMS. Może to być nieco bardziej trudne, ponieważ należy ustalić, jakie zasoby dotyczące pamięci, zasobów procesora, przepustowości sieci i magazynu są potrzebne dla konkretnych wystąpień docelowych, które powinny działać jako główne wystąpienie w scenariuszu odzyskiwania po awarii. Szczególnie w przypadku platformy HANA zdecydowanie zaleca się skonfigurowanie wystąpienia, które działa jako element docelowy odzyskiwania po awarii na hoście udostępnionym, dzięki czemu dane nie są wstępnie załadowane do wystąpienia docelowego odzyskiwania po awarii.

Scenariusze odzyskiwania po wystąpieniu dużego wystąpienia HANA sprawdź następujące dokumenty:

- [Pojedynczy węzeł z odzyskiwaniem po awarii przy użyciu replikacji magazynu](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [Pojedynczy węzeł z DR (Multipurpose) przy użyciu replikacji magazynu](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Pojedynczy węzeł z DR (Multipurpose) przy użyciu replikacji magazynu](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Wysoka dostępność dzięki replikacji HSR i DR z replikacją magazynu](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [Skalowanie za pomocą odzyskiwania po awarii przy użyciu replikacji magazynu](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [Pojedynczy węzeł z odzyskiwaniem po awarii przy użyciu HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [HSR z jednym węzłem do odzyskiwania po awarii (koszt zoptymalizowany)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [Wysoka dostępność i odzyskiwanie po awarii za pomocą HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [Wysoka dostępność i odzyskiwanie po awarii za pomocą HSR (zoptymalizowane pod kątem kosztów)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Skalowanie w poziomie za pomocą narzędzia DR using HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> Użycie [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) nie zostało przetestowane dla wdrożeń DBMS w ramach obciążenia SAP. W związku z tym nie jest to obsługiwane w przypadku systemów SAP w tym punkcie w czasie. Inne metody replikacji przez firmę Microsoft i SAP nie są wyświetlane na liście nie są obsługiwane. Używanie oprogramowania innej firmy do replikowania warstwy DBMS systemów SAP między różnymi regionami świadczenia usługi Azure musi być obsługiwane przez dostawcę oprogramowania i nie będzie obsługiwane w ramach kanałów pomocy technicznej firmy Microsoft i SAP. 
 
## <a name="non-dbms-layer"></a>Warstwa systemu innego niż DBMS
W przypadku warstwy aplikacji SAP oraz udziałów lub lokalizacji magazynu, które są zbędne, klienci mogą korzystać z dwóch głównych scenariuszy:

- Cele odzyskiwania po awarii w drugim regionie platformy Azure nie są używane do celów produkcyjnych ani nieprodukcyjnych. W tym scenariuszu maszyny wirtualne, które działają jako miejsce docelowe odzyskiwania po awarii, są idealnym rozwiązaniem, a obraz i zmiany w obrazach produkcyjnej warstwy aplikacji SAP są replikowane do regionu odzyskiwania po awarii. Funkcja, która może wykonać takie zadanie, jest [Azure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md). Azure Site Recovery obsługiwać scenariusze replikacji z platformy Azure na platformę Azure, takie jak to. 
- Cele odzyskiwania po awarii są maszynami wirtualnymi, które są używane przez systemy nieprodukcyjne. Cały poziom oprogramowania SAP jest rozłożony na dwa różne regiony platformy Azure z systemami produkcyjnymi zwykle w jednym regionie i w systemach nieprodukcyjnych w innym regionie. W wielu wdrożeniach klientów klient ma system nieprodukcyjny, który jest równoważny z systemem produkcyjnym. Klient ma wstępnie zainstalowane wystąpienia aplikacji produkcyjnych w systemach nieprodukcyjnych warstwy aplikacji. W przypadku przejścia w tryb failover wystąpienia nieprodukcyjne zostaną zamknięte, wirtualne nazwy maszyn wirtualnych są przenoszone do maszyn wirtualnych innych niż produkcyjne (po przypisaniu nowych adresów IP w systemie DNS) i wstępnie zainstalowanych wystąpień produkcyjnych.

### <a name="sap-central-services-clusters"></a>Klastry usług SAP Central
Klastry usług SAP Central Services używające dysków udostępnionych (Windows), udziałów SMB (Windows) lub udziałów NFS są trudniejsze do replikacji. Po stronie systemu Windows replikacja magazynu systemu Windows jest możliwym rozwiązaniem. W systemie Linux rsync to rozwiązanie, którego to dotyczy.



## <a name="non-supported-scenario"></a>Nieobsługiwany scenariusz
Istnieje lista scenariuszy, które nie są obsługiwane w przypadku obciążeń SAP w ramach architektury platformy Azure. **Nieobsługiwane** oznacza, że oprogramowanie SAP i Microsoft nie będzie w stanie obsłużyć tych konfiguracji i musi odroczyć na ewentualną inną firmę, która dostarczyła oprogramowania do ustanowienia takich architektur. Dwie z kategorii są:

- Urządzenia nietrwałe magazynu: istnieje wiele urządzeń nieelastycznych magazynu oferowanych w witrynie Azure Marketplace. Niektórzy dostawcy udostępniają własną dokumentację dotyczącą sposobu używania tych urządzeń do magazynowania na platformie Azure związanych z oprogramowaniem SAP. Obsługa konfiguracji i wdrożeń związanych z takimi urządzeniami magazynującymi magazynu musi być świadczona przez dostawcę tych urządzeń. Ten fakt jest również omawiany w [uwagi dotyczące pomocy technicznej SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Platformy wysokiej dostępności: tylko klaster trybu failover z systemem Pacemaker i Windows Server są obsługiwane platformy o wysokiej dostępności dla obciążeń SAP na platformie Azure. Jak wspomniano wcześniej, rozwiązanie oprogramowanie SIOS `Datakeeper` zostało opisane i udokumentowane przez firmę Microsoft. Niemniej jednak składniki oprogramowanie SIOS `Datakeeper` muszą być obsługiwane za pomocą oprogramowanie SIOS jako dostawcy dostarczającego te składniki. W przypadku oprogramowania SAP wymieniono również inne platformy certyfikowane wysokiej dostępności w różnych uwagach dotyczących oprogramowania SAP. Niektóre z nich były certyfikowane przez dostawcę innych firm dla platformy Azure. Niemniej jednak Obsługa konfiguracji przy użyciu tych produktów musi być świadczona przez dostawcę produktu. Różni dostawcy mają inną integrację z procesami obsługi SAP. Przed podjęciem decyzji o użyciu produktu w konfiguracjach SAP wdrożonych na platformie Azure należy wyjaśnić, jaki proces wsparcia działa najlepiej dla danego dostawcy.
- Udostępnione klastry dysków, w których pliki bazy danych znajdują się na dyskach udostępnionych nie są obsługiwane z wyjątkiem maxDB. W przypadku wszystkich innych baz danych obsługiwane rozwiązanie ma mieć osobne lokalizacje magazynu zamiast udziału SMB lub NFS lub dysku udostępnionego do konfigurowania scenariuszy wysokiej dostępności

Inne scenariusze, które nie są obsługiwane, są takie jak:

- Scenariusze wdrażania, które wprowadzają duże opóźnienia sieci między warstwą aplikacji SAP a warstwą SAP DBMS w typowej architekturze SAP, jak pokazano w NetWeaver, S/4HANA i na `Hybris` przykład. Obejmuje to następujące działania:
    - Wdrażanie jednej z warstw lokalnie, a druga warstwa jest wdrażana na platformie Azure
    - Wdrażanie warstwy aplikacji SAP systemu w innym regionie świadczenia usługi Azure niż w warstwie DBMS
    - Wdrożenie jednej warstwy w centrach danych, które znajdują się na platformie Azure i drugiej warstwie na platformie Azure, z wyjątkiem sytuacji, w których takie wzorce architektury są dostarczane przez usługę natywną platformy Azure
    - Wdrażanie wirtualnych urządzeń sieciowych między warstwą aplikacji SAP a warstwą DBMS
    - Korzystanie z magazynu hostowanego w centrach danych, które znajdują się na platformie Azure Datacenter dla warstwy systemu SAP DBMS lub globalnego katalogu transportu SAP
    - Wdrożenie dwóch warstw z dwoma różnymi dostawcami w chmurze. Na przykład wdrożenie warstwy DBMS w infrastrukturze chmury firmy Oracle i warstwie aplikacji na platformie Azure
- Konfiguracje klastrów Pacemaker platformy HANA z obsługą wiele wystąpień
- Konfiguracje klastrów systemu Windows z dyskami udostępnionymi za pomocą usługi SOFS lub SMB w systemie ANF dla baz danych SAP obsługiwanych w systemie Windows. Zamiast tego zalecamy użycie natywnej replikacji o wysokiej dostępności dla określonych baz danych i użycie oddzielnych stosów pamięci masowej
- Wdrożenie baz danych SAP, które są obsługiwane w systemie Linux z plikami bazy danych znajdującymi się w udziałach NFS na ANF z wyjątkiem SAP HANA
- Wdrażanie systemu Oracle DBMS na dowolnym innym systemie operacyjnym gościa niż Windows i Oracle Linux. Zobacz również temat [Pomoc techniczna SAP uwagi #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Scenariusze, które nie zostały przetestowane i w związku z tym nie mają doświadczenia z listą takich jak:

- Azure Site Recovery replikowanie maszyn wirtualnych z warstwą DBMS. W związku z tym zalecamy użycie natywnej funkcji replikacji asynchronicznej bazy danych dla potencjalnej konfiguracji odzyskiwania po awarii
 

## <a name="next-steps"></a>Następne kroki
Przeczytaj następne kroki w temacie [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver](./planning-guide.md)




  