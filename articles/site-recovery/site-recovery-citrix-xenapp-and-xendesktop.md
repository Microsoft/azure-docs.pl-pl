---
title: Konfigurowanie odzyskiwania po awarii Citrix XenDesktop/XenApp za pomocą Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii w ramach wdrożeń Citrix XenDesktop i XenApp przy użyciu Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 90d54a8ded99dd8ab43aed688036add6aede20ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86134846"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowego wdrożenia Citrix XenApp i XenDesktop



Citrix XenDesktop to rozwiązanie wirtualizacji pulpitu, które udostępnia komputery stacjonarne i aplikacje jako usługę OnDemand dla dowolnego użytkownika, wszędzie. Dzięki technologii FlexCast Delivery XenDesktop mogą szybko i bezpiecznie dostarczać aplikacje i komputery stacjonarne użytkownikom.
Obecnie program Citrix XenApp nie zapewnia możliwości odzyskiwania po awarii.

Dobre rozwiązanie do odzyskiwania po awarii powinno umożliwić modelowanie planów odzyskiwania na podstawie podanych powyżej złożonych architektur aplikacji oraz możliwość dodawania dostosowanych kroków w celu obsługi mapowań aplikacji między różnymi warstwami, dzięki czemu w przypadku awarii, które zaczynają znajdować się na niższych RTOach.

Ten dokument zawiera wskazówki krok po kroku dotyczące tworzenia rozwiązania do odzyskiwania po awarii dla lokalnych wdrożeń usługi Citrix XenApp na platformach Hyper-V i VMware vSphere. W tym dokumencie opisano również, jak przeprowadzić test pracy w trybie failover (sprawdzanie odzyskiwania po awarii) i nieplanowaną pracę w trybie failover na platformie Azure przy użyciu planów odzyskiwania, obsługiwanych konfiguracji i wymagań wstępnych.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz następujące kwestie:

1. [Replikowanie maszyny wirtualnej na platformę Azure](./vmware-azure-tutorial.md)
1. [Projektowanie sieci odzyskiwania](./concepts-on-premises-to-azure-networking.md)
1. [Testowanie pracy w trybie failover na platformie Azure](site-recovery-test-failover-to-azure.md)
1. [Przełączenie w tryb failover na platformie Azure](site-recovery-failover.md)
1. Jak [replikować kontroler domeny](site-recovery-active-directory.md)
1. Jak [replikować SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Wzorce wdrożenia

Farma Citrix XenApp i XenDesktop ma zwykle następujący wzorzec wdrożenia:

**Wzorzec wdrożenia**

Wdrożenie Citrix XenApp i XenDesktop z serwerem DNS usług AD, serwerem bazy danych SQL, kontrolerem dostarczania Citrix, serwerem witryny sklepu, XenApp Master (VDA), serwerem licencji Citrix XenApp

![Wzorzec wdrożenia 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

Na potrzeby tego artykułu wdrożenia Citrix na maszynach wirtualnych VMware zarządzanych przez vSphere 6,0/System Center VMM 2012 R2 zostały użyte do skonfigurowania programu DR.

### <a name="source-and-target"></a>Źródło i cel

**Scenariusz** | **Do lokacji dodatkowej** | **Na platformie Azure**
--- | --- | ---
**Hyper-V** | Poza zakresem | Tak
**VMware** | Poza zakresem | Tak
**Serwer fizyczny** | Poza zakresem | Tak

### <a name="versions"></a>Wersje
Klienci mogą wdrażać składniki XenApp jako Virtual Machines uruchomione w funkcji Hyper-V lub VMware lub jako serwery fizyczne. Azure Site Recovery może chronić wdrożenia fizyczne i wirtualne na platformie Azure.
Ponieważ na platformie Azure jest obsługiwana XenApp 7,7 lub nowsza, tylko wdrożenia z tymi wersjami mogą zostać przełączone do trybu failover na platformie Azure na potrzeby odzyskiwania po awarii lub migracji.

### <a name="things-to-keep-in-mind"></a>Rzeczy, o których należy pamiętać

1. Ochrona i odzyskiwanie lokalnych wdrożeń przy użyciu maszyn systemu operacyjnego serwera w celu dostarczania opublikowanych aplikacji XenApp i pulpitów opublikowanych XenApp.

2. Ochrona i odzyskiwanie lokalnych wdrożeń przy użyciu maszyn systemu operacyjnego komputerów stacjonarnych w celu zapewnienia infrastruktury VDI dla komputerów stacjonarnych, w tym systemu Windows 10, nie jest obsługiwane. Jest to spowodowane tym, że Site Recovery nie obsługuje odzyskiwania maszyn z OS'es Desktop.  Ponadto niektóre klienckie systemy operacyjne na komputerach klienckich (np. System Windows 7) nie jest jeszcze obsługiwany na potrzeby licencjonowania na platformie Azure. [Dowiedz się więcej](https://azure.microsoft.com/pricing/licensing-faq/) na temat licencjonowania dla komputerów stacjonarnych klienta/serwera na platformie Azure.

3.  Azure Site Recovery nie może replikować ani chronić istniejących lokalnych obiektów MCS lub klonów PVS.
Należy ponownie utworzyć te klony przy użyciu aprowizacji usługi Azure RM z poziomu kontrolera dostarczania.

4. Nie można chronić usługi system skaler przy użyciu Azure Site Recovery, ponieważ usługa FreeBSD jest oparta na systemie, a Azure Site Recovery nie obsługuje ochrony systemu operacyjnego FreeBSD. Po przejściu do trybu failover na platformie Azure należy wdrożyć i skonfigurować nowe urządzenie do skalowania w systemie Azure.


## <a name="replicating-virtual-machines"></a>Replikowanie maszyn wirtualnych

Aby umożliwić replikację i odzyskiwanie, należy chronić następujące składniki wdrożenia programu Citrix XenApp.

* Ochrona serwera DNS usługi AD
* Ochrona serwera SQL Database
* Ochrona kontrolera dostarczania Citrix
* Ochrona serwera witryny sklepu.
* Ochrona XenApp Master (VDA)
* Ochrona serwera licencji Citrix XenApp


**Replikacja serwera DNS usługi AD**

Zapoznaj się z artykułem [ochrona Active Directory i usługi DNS z Azure Site Recovery](site-recovery-active-directory.md) , aby uzyskać wskazówki dotyczące replikowania i konfigurowania kontrolera domeny na platformie Azure.

**Replikacja serwera SQL Database**

Zapoznaj się z artykułem [ochrona SQL Server z SQL Server odzyskiwaniem po awarii i Azure Site Recovery,](site-recovery-sql.md) Aby uzyskać szczegółowe wskazówki techniczne na temat zalecanych opcji ochrony serwerów SQL.

Postępuj zgodnie z [tymi wskazówkami](./vmware-azure-tutorial.md) , aby rozpocząć replikację innych składników maszyn wirtualnych do platformy Azure.

![Ochrona składników XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Ustawienia obliczeń i sieci**

Po włączeniu ochrony maszyn (stan jest wyświetlany jako "chroniony" w obszarze zreplikowane elementy) należy skonfigurować ustawienia obliczeń i sieci.
We właściwościach obliczeniowych obliczeniowych i > sieciowych można określić nazwę i docelowy rozmiar maszyny wirtualnej platformy Azure.
Zmodyfikuj nazwę, aby była zgodna z wymaganiami platformy Azure, jeśli zachodzi taka potrzeba. Możesz również wyświetlić i dodać informacje o sieci docelowej, podsieci i adresie IP, które zostaną przypisane do maszyny wirtualnej platformy Azure.

. Weź pod uwagę następujące kwestie:

* Możesz ustawić docelowy adres IP. Jeśli nie podasz adresu, maszyna w trybie failover będzie używać protokołu DHCP. Jeśli ustawisz adres, który nie jest dostępny w trybie failover, przełączenie w tryb failover nie będzie możliwe. Ten sam docelowy adres IP może być użyty do testowania trybu failover, jeśli adres jest dostępny w testowej sieci trybu failover.

* W przypadku serwera AD/DNS zachowywanie adresu lokalnego umożliwia określenie tego samego adresu co serwer DNS dla sieci wirtualnej platformy Azure.

Liczba kart sieciowych zależy od rozmiaru określonego dla docelowej maszyny wirtualnej, zgodnie z poniższym:

*   Jeśli liczba kart sieciowych w maszynie źródłowej jest mniejsza lub równa liczbie kart sieciowych dozwolonych dla rozmiaru maszyny docelowej, maszyna docelowa będzie mieć taką samą liczbę kart sieciowych jak maszyna źródłowa.
*   Jeśli liczba kart sieciowych dla źródłowej maszyny wirtualnej przekracza liczbę dozwolonych kart sieciowych dla rozmiaru maszyny docelowej, zostanie użyta maksymalna liczba kart dla rozmiaru maszyny docelowej.
* Na przykład, jeśli maszyna źródłowa ma dwie karty sieciowe, a rozmiar maszyny docelowej obsługuje cztery karty, maszyna docelowa będzie mieć dwie karty sieciowe. Jeśli maszyna źródłowa ma dwie karty sieciowe, ale rozmiar maszyny docelowej obsługuje tylko jedną kartę, maszyna docelowa będzie mieć tylko jedną kartę sieciową.
*   Jeśli maszyna wirtualna ma wiele kart sieciowych, wszystkie będą łączyć się z tą samą siecią.
*   Jeśli maszyna wirtualna ma wiele kart sieciowych, to pierwsza z nich wyświetlana na liście będzie domyślną kartą sieciową na maszynie wirtualnej platformy Azure.


## <a name="creating-a-recovery-plan"></a>Tworzenie planu odzyskiwania

Po włączeniu replikacji dla maszyn wirtualnych składników XenApp następnym krokiem jest utworzenie planu odzyskiwania.
Plan odzyskiwania grupuje maszyny wirtualne z podobnymi wymaganiami dotyczącymi trybu failover i odzyskiwania.  

**Procedura tworzenia planu odzyskiwania**

1. Dodaj maszyny wirtualne składnika XenApp w planie odzyskiwania.
2. Kliknij kolejno pozycje plany odzyskiwania — > + plan odzyskiwania. Podaj intuicyjną nazwę planu odzyskiwania.
3. W przypadku maszyn wirtualnych VMware: wybierz opcję Źródło jako serwer przetwarzania VMware, cel jako Microsoft Azure i model wdrażania jako Menedżer zasobów i kliknij pozycję Wybierz elementy.
4. W przypadku maszyn wirtualnych funkcji Hyper-V: wybierz pozycję źródło jako serwer VMM, miejsce docelowe jako Microsoft Azure i model wdrażania jako Menedżer zasobów i kliknij pozycję Wybierz elementy, a następnie wybierz pozycję Maszyny wirtualne wdrożenia XenApp.

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grup trybu failover

Plany odzyskiwania można dostosować, aby dodać grupy trybu failover dla określonej kolejności uruchamiania, skryptów lub akcji ręcznych. Do planu odzyskiwania należy dodać następujące grupy.

1. Grupa grupa1 trybu failover: DNS usługi AD
2. Group2 trybu failover: maszyny wirtualne SQL Server
2. Tryb failover Grupa 3: VDA Master Image VM
3. Group4 trybu failover: kontroler dostarczania i maszyny wirtualne serwera witryny sklepu


### <a name="adding-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania

Skrypty można uruchamiać przed lub po określonej grupie w planie odzyskiwania. Akcje ręczne można również dołączać i wykonywać podczas pracy w trybie failover.

Dostosowany plan odzyskiwania wygląda podobnie do poniższego:

1. Grupa grupa1 trybu failover: DNS usługi AD
2. Group2 trybu failover: maszyny wirtualne SQL Server
3. Tryb failover Grupa 3: VDA Master Image VM

   >[!NOTE]     
   >Kroki 4, 6 i 7 zawierające akcje ręczne lub skrypty mają zastosowanie tylko do lokalnego środowiska XenApp >z wykazami serwera MCS/PVS.

4. Akcja ręczna lub skryptu grupy 3: Zamknij główną maszynę wirtualną VDA.
Główna maszyna wirtualna VDA po przełączeniu w tryb failover na platformę Azure będzie w stanie uruchomienia. Aby utworzyć nowe katalogi serwera MCS przy użyciu hostingu platformy Azure, maszyna wirtualna VDA musi być w stanie zatrzymania (bez przydziału). Zamknij maszynę wirtualną z Azure Portal.

5. Group4 trybu failover: kontroler dostarczania i maszyny wirtualne serwera witryny sklepu
6. Grupa 3 ręcznie lub akcja skryptu 1:

    ***Dodawanie połączenia z hostem usługi Azure RM***

    Utwórz połączenie hosta platformy Azure na komputerze kontrolera dostarczania, aby udostępnić nowe katalogi MCS na platformie Azure. Wykonaj kroki opisane w tym [artykule](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Grupa 3 lub akcja skryptu 2:

    ***Ponowne tworzenie katalogów serwera MCS na platformie Azure***

    Istniejące klony serwera MCS lub PVS w lokacji głównej nie będą replikowane do platformy Azure. Należy ponownie utworzyć te klony przy użyciu zreplikowanego głównego VDA i aprowizacji platformy Azure z poziomu kontrolera dostarczania. Wykonaj kroki opisane w tym [artykule](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) , aby utworzyć katalogi serwera MCS na platformie Azure.

![Plan odzyskiwania dla składników XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >W razie potrzeby można użyć skryptów w [lokalizacji](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) do zaktualizowania systemu DNS przy użyciu nowych adresów IP maszyn wirtualnych w trybie failover w >lub dołączenia modułu równoważenia obciążenia na maszynie wirtualnej w trybie failover.


## <a name="doing-a-test-failover"></a>Przeprowadzenie testu pracy w trybie failover

Postępuj zgodnie z [tymi wskazówkami](site-recovery-test-failover-to-azure.md) , aby przeprowadzić test pracy w trybie failover.

![Plan odzyskiwania](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Przełączenie w tryb failover

Postępuj zgodnie z [tymi wskazówkami](site-recovery-failover.md) podczas pracy w trybie failover.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat replikowania wdrożeń Citrix XenApp i XenDesktop można [znaleźć](https://aka.ms/citrix-xenapp-xendesktop-with-asr) w tym oficjalnym dokumencie. Zapoznaj się ze wskazówkami, aby [replikować inne aplikacje](site-recovery-workload.md) przy użyciu Site Recovery.
