---
title: Azure Virtual Machines HA for SAP NW w systemie Windows z Azure NetApp Files (SMB) | Microsoft Docs
description: Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie Windows z Azure NetApp Files (SMB) dla aplikacji SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: radeltch
ms.openlocfilehash: a4c4631a0a1263e5a5398c44a8570f92571102e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045840"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie Windows z Azure NetApp Files (SMB) dla aplikacji SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

W tym artykule opisano sposób wdrażania, konfigurowania maszyn wirtualnych, instalowania platformy klastra i instalowania systemu SAP NetWeaver 7,50 o wysokiej dostępności na maszynach wirtualnych z systemem Windows przy użyciu [protokołu SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) na [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

Warstwa bazy danych nie jest szczegółowo omówiona w tym artykule. Przyjęto założenie, że [Sieć wirtualna](../../../virtual-network/virtual-networks-overview.md) platformy Azure została już utworzona.  

Przeczytaj najpierw następujące informacje i dokumenty SAP:

* [Dokumentacja Azure NetApp Files][anf-azure-doc] 
* Test SAP [1928533][1928533], który zawiera:  
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows w systemie Microsoft Azure
* Uwaga dotycząca protokołu SAP [2015553][2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2178632][2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [1999351][1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* W przypadku oprogramowania SAP Uwaga [2287140](https://launchpad.support.sap.com/#/notes/2287140) przedstawiono wymagania wstępne dotyczące funkcji urzędu certyfikacji obsługiwanej przez SAP w protokole SMB 3. x.
* Uwaga dotycząca rozwiązania SAP [2802770](https://launchpad.support.sap.com/#/notes/2802770) zawiera informacje dotyczące rozwiązywania problemów dotyczących wolnego AL11 transakcji SAP w systemie Windows 2012 i 2016.
* Uwaga dotycząca programu SAP [1911507](https://launchpad.support.sap.com/#/notes/1911507) zawiera informacje o przezroczystej funkcji trybu failover dla udziału plików w systemie Windows Server z protokołem SMB 3,0.
* Uwaga dotycząca oprogramowania SAP [662452](https://launchpad.support.sap.com/#/notes/662452) ma zalecenia (dezaktywowanie 8,3 generacji nazw) w celu rozwiązywania niskiej wydajności systemu plików/błędów podczas uzyskiwania dostępu do danych.
* [Instalowanie rozwiązania SAP NetWeaver o wysokiej dostępności w klastrze trybu failover systemu Windows i udziału plików dla wystąpień SAP ASCS/SCS na platformie Azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Architektura Azure Virtual Machines wysoka dostępność i scenariusze dla oprogramowania SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
* [Dodaj port sondy w konfiguracji klastra ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Instalacja wystąpienia programu (A) SCS w klastrze trybu failover](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Tworzenie woluminu SMB dla usługi Azure NetApp Files](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)
* [NetApp aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]

> [!IMPORTANT]
> Uwaga: należy pamiętać, że instalacja systemu SAP z SWPM na udziale SMB, hostowana na woluminie SMB [Azure NetApp Files][anf-azure-doc] , może zakończyć się niepowodzeniem z powodu błędu instalacji, na przykład "warningPerm nie jest zdefiniowana". Aby uniknąć tego błędu, użytkownik, pod którym jest wykonywane SWPM kontekstu, wymaga podwyższonego poziomu uprawnień "Administrator domeny" podczas instalacji systemu SAP.  

## <a name="overview"></a>Omówienie

System SAP opracował nowe podejście i alternatywę dla udostępnionych dysków klastra, na potrzeby klastrowania wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows. Zamiast korzystać z udostępnionych dysków klastra, jeden może używać udziału plików SMB do wdrażania plików hosta globalnego SAP. Azure NetApp Files obsługuje SMBv3 (wraz z NFS) z listą ACL systemu plików NTFS przy użyciu Active Directory. Azure NetApp Files jest automatycznie wysoka dostępność (ponieważ jest to usługa PaaS). Te funkcje sprawiają, że Azure NetApp Files doskonałe rozwiązanie do hostowania udziału plików SMB dla systemu SAP Global.  
Obsługiwane są zarówno [usługi domenowe Azure Active Directory (AD)](../../../active-directory-domain-services/overview.md) , jak i [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) . Istniejących Active Directory kontrolerów domeny można użyć z Azure NetApp Files. Kontrolery domeny mogą znajdować się na platformie Azure jako maszyny wirtualne lub lokalnie za pośrednictwem ExpressRoute lub sieci VPN S2S. W tym artykule będziemy używać kontrolera domeny na maszynie wirtualnej platformy Azure.  
Wysoka dostępność (HA) dla usług SAP NetWeaver Central Services wymaga magazynu udostępnionego. Aby osiągnąć to w systemie Windows, do dawna była konieczna kompilacja klastra SOFS lub użycie udostępnionego dysku klastra s/w takich jak oprogramowanie SIOS. Teraz można osiągnąć rozwiązanie SAP NetWeaver HA przy użyciu magazynu udostępnionego, wdrożonego na Azure NetApp Files. Używanie Azure NetApp Files dla magazynu udostępnionego eliminuje konieczność SOFS lub oprogramowanie SIOS.  

> [!NOTE]
> Klastrowanie wystąpień SAP ASCS/SCS za pomocą udziału plików jest obsługiwane w przypadku oprogramowania SAP NetWeaver 7,40 (i nowszych) przy użyciu jądra SAP 7,49 (i nowszych).  

![Architektura architektury SAP ASCS/SCS z udziałem SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Wymagania wstępne dotyczące udziału plików SMB są następujące:
* Protokół SMB 3,0 (lub nowszy).
* Możliwość ustawienia Active Directory list kontroli dostępu (ACL) dla Active Directory grup użytkowników i obiektu komputera $ Computer.
* Udział plików musi mieć włączoną obsługę HA.

Udział w usługach SAP Central w tej architekturze referencyjnej jest oferowany przez Azure NetApp Files:

![Architektury SAP ASCS/SCS HA z informacjami o udziale SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Utwórz i zainstaluj wolumin SMB dla Azure NetApp Files

Wykonaj następujące kroki, jak przygotowanie do korzystania z Azure NetApp Files.  

1. Postępuj zgodnie z instrukcjami, aby [zarejestrować się w usłudze Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md)  
2. Utwórz konto usługi Azure NetApp, wykonując kroki opisane w temacie  [Tworzenie konta NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)  
3. Skonfiguruj pulę pojemności, postępując zgodnie z instrukcjami podanymi w temacie [Konfigurowanie puli pojemności](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
4. Zasoby Azure NetApp Files muszą znajdować się w delegowanej podsieci. Postępuj zgodnie z instrukcjami w temacie [delegowanie podsieci do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) , aby utworzyć delegowaną podsieć.  

   > [!IMPORTANT]
   > Przed utworzeniem woluminu SMB należy utworzyć połączenia Active Directory. Zapoznaj się z [wymaganiami dotyczącymi połączeń Active Directory](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections).  

5. Utwórz połączenie Active Directory, zgodnie z opisem w temacie [Tworzenie połączenia Active Directory](../../../azure-netapp-files/create-active-directory-connections.md#create-an-active-directory-connection)  
6. Utwórz wolumin SMB Azure NetApp Files SMB, postępując zgodnie z instrukcjami w temacie [Dodawanie woluminu SMB](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)  
7. Zainstaluj wolumin SMB na maszynie wirtualnej z systemem Windows.

> [!TIP]
> Instrukcje dotyczące sposobu instalowania woluminu Azure NetApp Files można znaleźć w przypadku nawigowania w [witrynie Azure Portal](https://portal.azure.com/#home) do obiektu Azure NetApp Files, kliknij blok **woluminy** , a następnie **instrukcje instalacji**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Przygotowywanie infrastruktury dla oprogramowania SAP HA przy użyciu klastra trybu failover systemu Windows 

1. [Ustaw reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716).
2. [Dodaj maszyny wirtualne z systemem Windows do domeny](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
3. [Dodaj wpisy rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [Konfigurowanie klastra trybu failover systemu Windows Server dla wystąpienia oprogramowania SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. W przypadku korzystania z systemu Windows Server 2016 zalecamy skonfigurowanie [monitora chmury platformy Azure](/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Zainstaluj wystąpienie SAP ASCS na obu węzłach

W oprogramowaniu SAP wymagane jest następujące oprogramowanie:
   * Narzędzie instalacji Menedżera aprowizacji oprogramowania SAP (SWPM) w wersji SPS25 lub nowszej.
   * Jądro SAP 7,49 lub nowsze
   * Utwórz nazwę hosta wirtualnego (nazwę sieci klastra) dla klastrowanego wystąpienia SAP ASCS/SCS, zgodnie z opisem w temacie [Tworzenie nazwy hosta wirtualnego dla wystąpienia klastra SAP ASCS/SCS](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Klastrowanie wystąpień SAP ASCS/SCS za pomocą udziału plików jest obsługiwane w przypadku oprogramowania SAP NetWeaver 7,40 (i nowszych) przy użyciu jądra SAP 7,49 (i nowszych).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Zainstaluj wystąpienie ASCS/SCS w pierwszym węźle klastra ASCS/SCS

1. Zainstaluj wystąpienie SAP ASCS/SCS w pierwszym węźle klastra. Uruchom narzędzie instalacji SAP SWPM, a następnie przejdź do: **Product**  >  **DBMS** > Installation > Application Server ABAP (lub Java) > High-Availability system > ASCS/SCS, > pierwszym węźle klastra.  

2. Wybierz opcję **klaster udziałów plików** jako konfigurację udziału klastra w SWPM.  
3. Po wyświetleniu monitu w kroku **Parametry klastra systemowego SAP** wprowadź nazwę hosta dla Azure NETAPP Files udziału SMB, który został już utworzony jako **Nazwa hosta udziału plików**.  W tym przykładzie nazwą hosta udziału SMB jest **anfsmb-9562**. 

   > [!IMPORTANT]
   > Jeśli narzędzie sprawdzania wymagań wstępnych w programie SWPM pokazuje warunek funkcji ciągłej dostępności nie jest spełniony, można rozwiązać ten problem, postępując zgodnie z instrukcjami w oknie [opóźniony komunikat o błędzie podczas próby dostępu do folderu udostępnionego, który już nie istnieje w systemie Windows](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

   > [!TIP]
   > Jeśli narzędzie sprawdzania wymagań wstępnych w programie SWPM pokazuje, że nie jest spełniony warunek zmiany rozmiaru, można dostosować rozmiar wymiany, przechodząc do obszaru mój komputer>właściwości systemu>ustawienia wydajności> zaawansowaną zmianę> pamięci wirtualnej>.  

4. Skonfiguruj zasób klastra SAP, `SAP-SID-IP` port sondy przy użyciu programu PowerShell. Tę konfigurację należy wykonać na jednym z węzłów klastra SAP ASCS/SCS, zgodnie z opisem w temacie [Konfigurowanie portu sondowania](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Instalowanie wystąpienia ASCS/SCS w drugim węźle klastra ASCS/SCS

1. Zainstaluj wystąpienie SAP ASCS/SCS w drugim węźle klastra. Uruchom narzędzie instalacji SAP SWPM, a następnie przejdź do **produktu**  >  system **DBMS** > Installation > Application Server ABAP (lub Java) > High-Availability > ASCS/SCS > dodatkowym węźle klastra.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Instalowanie wystąpienia systemu DBMS i serwerów aplikacji SAP

Ukończ instalację oprogramowania SAP, instalując następujące polecenie:

   * Wystąpienie systemu DBMS  
   * Podstawowy serwer aplikacji SAP  
   * Dodatkowy serwer aplikacji SAP  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testowanie trybu failover wystąpienia SAP ASCS/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Przechodzenie w tryb failover z węzła klastra A do węzła klastra B i z powrotem
W tym scenariuszu testowym będziemy odwoływać się do węzła klastra sapascs1 jako węzeł A i do węzła klastra sapascs2 jako węzeł B.

1. Sprawdź, czy zasoby klastra działają w węźle A. ![ Rysunek 1. zasoby klastra trybu failover systemu Windows Server uruchomione w węźle A przed testem trybu failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Uruchom ponownie węzeł klastra A. Zasoby klastra SAP zostaną przeniesione do węzła klastra B. ![ Rysunek 2. zasoby klastra trybu failover systemu Windows Server uruchomione w węźle B po przejściu do trybu failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Test zapisu blokady

1. Sprawdź, czy serwer replikacji w kolejce SAP (wykres WYWOŁUJĄCYCH) jest aktywny  
2. Zaloguj się do systemu SAP, wykonaj transSU01 transakcji i Otwórz identyfikator użytkownika w trybie zmiany. Spowoduje to wygenerowanie wpisu blokady SAP.  
3. Gdy użytkownik jest zalogowany w systemie SAP, Wyświetl wpis blokady, przechodząc do pozycji Transaction ST12.  
4. Przełączenie w tryb failover zasobów ASCS z węzła klastra A do węzła klastra B.  
5. Należy sprawdzić, czy wpis blokady wygenerowany przed przełączeniem do trybu failover zasobów klastra SAP ASCS/SCS jest zachowywany.  

![Rysunek 3. wpis blokady jest zachowywany po teście trybu failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z trybem failover w kolejce w ASCS z wykres wywołujących](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Następne kroki

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak ustanowić wysoką dostępność i plan odzyskiwania po awarii dla oprogramowania SAP 
* HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
