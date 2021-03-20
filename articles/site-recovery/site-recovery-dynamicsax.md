---
title: Odzyskiwanie po awarii systemu Dynamics AX z Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla systemu Dynamics AX przy użyciu Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: dfa3c108d00aeba9c7d42e96e7a40736a087a508
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86133812"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowej aplikacji Dynamics AX   




 Dynamics AX to jedno z najpopularniejszych rozwiązań ERP używanych przez przedsiębiorstwa do standaryzacji procesów, zarządzania zasobami i uproszczenia zgodności. Ponieważ aplikacja ma krytyczne znaczenie dla organizacji, w przypadku awarii aplikacja powinna działać w minimalnym czasie.

Obecnie w systemie Dynamics AX nie są dostępne żadne wbudowane możliwości odzyskiwania po awarii. System Dynamics AX składa się z wielu składników serwera, takich jak serwer obiektów aplikacji systemu Windows, Azure Active Directory, Azure SQL Database, SharePoint Server i Reporting Services. W celu ręcznego zarządzania odzyskiwaniem po awarii każdego z tych składników jest to nie tylko kosztowne, ale również podatne na błędy.

W tym artykule wyjaśniono, jak można utworzyć rozwiązanie odzyskiwania po awarii dla aplikacji systemu Dynamics AX przy użyciu [Azure Site Recovery](site-recovery-overview.md). Obejmuje on również planowane/nieplanowane testy trybu failover za pomocą jednego kliknięcia planu odzyskiwania, obsługiwane konfiguracje i wymagania wstępne.



## <a name="prerequisites"></a>Wymagania wstępne

Implementacja odzyskiwania po awarii dla aplikacji Dynamics AX przy użyciu Site Recovery wymaga następujących wymagań wstępnych:

• Skonfigurowanie lokalnego wdrożenia systemu Dynamics AX.

• Utwórz magazyn Site Recovery w ramach subskrypcji platformy Azure.

• Jeśli platforma Azure jest witryną odzyskiwania, uruchom narzędzie do oceny gotowości maszyn wirtualnych platformy Azure na maszynach wirtualnych. Muszą one być zgodne z usługami Virtual Machines i Site Recovery platformy Azure.

## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

Na potrzeby tworzenia tego artykułu korzystamy z maszyn wirtualnych VMware z systemem Dynamics AX 2012 R3 w systemie Windows Server 2012 R2 Enterprise. Ponieważ Site Recovery replikacji jest niezależny od aplikacji, oczekujemy, że zalecenia podane w tym miejscu są przechowywane w następujących scenariuszach.

### <a name="source-and-target"></a>Źródło i cel

**Scenariusz** | **Do lokacji dodatkowej** | **Na platformie Azure**
--- | --- | ---
**Hyper-V** | Tak | Tak
**VMware** | Tak | Tak
**Serwer fizyczny** | Tak | Tak

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Włączanie odzyskiwania po awarii aplikacji Dynamics AX przy użyciu Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Ochrona aplikacji systemu Dynamics AX
Aby włączyć pełną replikację i odzyskiwanie aplikacji, każdy składnik programu Dynamics AX musi być chroniony.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Skonfiguruj Active Directory i replikację DNS

Aby aplikacja systemu Dynamics AX działała, Active Directory jest wymagana w lokacji odzyskiwania po awarii. Zalecamy stosowanie następujących dwóch opcji w zależności od złożoności środowiska lokalnego klienta.

**Opcja 1**

Klient ma niewielką liczbę aplikacji i jeden kontroler domeny dla całej lokacji lokalnej i planuje przełączenie w tryb failover całej lokacji. Zalecamy używanie replikacji Site Recoveryj do replikowania maszyny kontrolera domeny do lokacji dodatkowej (dotyczy to zarówno scenariuszy typu lokacja-lokacja, jak i między lokacjami do platformy Azure).

**Opcja 2**

Klient ma dużą liczbę aplikacji i jest uruchomiony Las Active Directory i planuje awaryjne przełączenie kilku aplikacji jednocześnie. Zalecamy skonfigurowanie dodatkowego kontrolera domeny w lokacji odzyskiwania po awarii (lokacji dodatkowej lub na platformie Azure).

 Aby uzyskać więcej informacji, zobacz [udostępnianie kontrolera domeny w lokacji odzyskiwania po awarii](site-recovery-active-directory.md). W pozostałej części tego dokumentu przyjęto założenie, że kontroler domeny jest dostępny w lokacji odzyskiwania po awarii.

### <a name="2-set-up-sql-server-replication"></a>2. Skonfiguruj SQL Server replikację
Aby uzyskać wskazówki techniczne dotyczące zalecanej opcji ochrony warstwy SQL, zobacz [replikowanie aplikacji przy użyciu SQL Server i Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Włącz ochronę dla maszyn wirtualnych klienta i serwera obiektów aplikacji programu Dynamics AX
Wykonaj odpowiednie Site Recovery konfiguracji w zależności od tego, czy maszyny wirtualne są wdrażane w [funkcji Hyper-V](./hyper-v-azure-tutorial.md) , czy w [oprogramowaniu VMware](./vmware-azure-tutorial.md).

> [!TIP]
> Zalecamy skonfigurowanie częstotliwości bezawarii na 15 minut.
>

Poniższa migawka przedstawia stan ochrony maszyn wirtualnych składnika Dynamics w ramach scenariusza ochrony między platformami programu VMware.

![Chronione elementy](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurowanie sieci
**Konfigurowanie ustawień obliczeniowych i sieciowych maszyny wirtualnej**

W przypadku maszyn wirtualnych klienta i serwera obiektów aplikacji programu Dynamics AX Skonfiguruj ustawienia sieciowe w Site Recovery tak, aby sieci maszyn wirtualnych były dołączone do odpowiedniej sieci odzyskiwania po awarii po przejściu do trybu failover. Upewnij się, że sieć odzyskiwania po awarii dla tych warstw obsługuje routing do warstwy SQL.

Możesz wybrać maszynę wirtualną w replikowanych elementach, aby skonfigurować ustawienia sieci, jak pokazano na poniższej migawce:

* W przypadku serwerów obiektów aplikacji wybierz odpowiedni zestaw dostępności.

* Jeśli używasz statycznego adresu IP, określ adres IP, który ma być pobierany przez maszynę wirtualną w polu tekstowym **docelowy adres IP** .

    ![Ustawienia sieciowe](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Utwórz plan odzyskiwania

Możesz utworzyć plan odzyskiwania w Site Recovery, aby zautomatyzować proces trybu failover. Dodaj warstwę aplikacji i warstwę sieci Web w planie odzyskiwania. Zamów je w różnych grupach, tak aby fronton został zamknięty przed warstwą aplikacji.

1. Wybierz magazyn Site Recovery w ramach subskrypcji, a następnie wybierz kafelek **plany odzyskiwania** .

2. Wybierz pozycję **+ plan odzyskiwania** i określ nazwę.

3. Wybierz **Źródło** i **cel**. Celem może być platforma Azure lub lokacja dodatkowa. W przypadku wybrania platformy Azure należy określić model wdrażania.

    ![Tworzenie planu odzyskiwania](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Wybierz serwer obiektów aplikacji i maszyny wirtualne klienta dla planu odzyskiwania, a następnie wybierz ✓.

    ![Wybierz elementy](./media/site-recovery-dynamics-ax/selectvms.png)

    Przykład planu odzyskiwania:

    ![Szczegóły planu odzyskiwania](./media/site-recovery-dynamics-ax/recoveryplan.png)

Możesz dostosować plan odzyskiwania dla aplikacji Dynamics AX, dodając następujące kroki. W poprzedniej migawce przedstawiono cały plan odzyskiwania po dodaniu wszystkich kroków.


* **SQL Server kroki trybu failover**: Aby uzyskać informacje na temat kroków odzyskiwania specyficznych dla programu SQL Server, zobacz temat [replikacja aplikacji z SQL Server i Azure Site Recovery](site-recovery-sql.md).

* **Grupa trybu failover 1**: praca awaryjna maszyn wirtualnych serwera obiektów aplikacji.
Upewnij się, że wybrany punkt odzyskiwania jest możliwie zbliżony do bazy danych PIT, ale nie z wyprzedzeniem.

* **Skrypt**: Dodaj moduł równoważenia obciążenia (tylko E-A).
Dodaj skrypt (za pośrednictwem Azure Automation) po dodaniu grupy maszyn do serwera obiektów aplikacji, aby dodać do niej moduł równoważenia obciążenia. Do wykonania tego zadania można użyć skryptu. Aby uzyskać więcej informacji, zobacz [jak dodać moduł równoważenia obciążenia na potrzeby odzyskiwania po awarii aplikacji wielowarstwowej](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Grupa trybu failover 2**: praca awaryjna maszyn wirtualnych programu Dynamics AX. Przełączenie w tryb failover maszyn wirtualnych warstwy sieci Web w ramach planu odzyskiwania.


### <a name="perform-a-test-failover"></a>Wykonaj test pracy w trybie failover

Aby uzyskać więcej informacji dotyczących Active Directory podczas testowego przełączania do trybu failover, zobacz Przewodnik uzupełniający "Active Directory odzyskiwania po awarii".

Aby uzyskać więcej informacji dotyczących programu SQL Server podczas testowego przełączania do trybu failover, zobacz [replikowanie aplikacji z SQL Server i Azure Site Recovery](site-recovery-sql.md).

1. Przejdź do Azure Portal i wybierz swój magazyn Site Recovery.

2. Wybierz plan odzyskiwania utworzony dla programu Dynamics AX.

3. Wybierz pozycję **Test pracy w trybie failover**.

4. Wybierz sieć wirtualną, aby uruchomić proces testowego przełączania do trybu failover.

5. Po utworzeniu środowiska pomocniczego można wykonać odpowiednie walidacje.

6. Po zakończeniu walidacji wybierz opcję **walidacje ukończone** i wyczyszczenie testowego środowiska pracy w trybie failover.

Aby uzyskać więcej informacji na temat przeprowadzania testowej pracy w trybie failover, zobacz [test pracy w trybie failover na platformie Azure w Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Przełączenie w tryb failover

1. Przejdź do Azure Portal i wybierz swój magazyn Site Recovery.

2. Wybierz plan odzyskiwania utworzony dla programu Dynamics AX.

3. Wybierz pozycję **tryb failover**, a następnie wybierz pozycję **tryb failover**.

4. Wybierz sieć docelową, a następnie wybierz pozycję **✓** , aby uruchomić proces przełączania do trybu failover.

Aby uzyskać więcej informacji na temat przełączenia w tryb failover, zobacz [tryb failover w Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Wykonywanie powrotu po awarii

Aby uzyskać informacje specyficzne dla SQL Server podczas powrotu po awarii, zobacz [replikowanie aplikacji z SQL Server i Azure Site Recovery](site-recovery-sql.md).

1. Przejdź do Azure Portal i wybierz swój magazyn Site Recovery.

2. Wybierz plan odzyskiwania utworzony dla programu Dynamics AX.

3. Wybierz pozycję **tryb failover**, a następnie wybierz pozycję **tryb failover**.

4. Wybierz pozycję **Zmień kierunek**.

5. Wybierz odpowiednie opcje: synchronizacja danych i Tworzenie maszyny wirtualnej.

6. Wybierz pozycję **✓** , aby rozpocząć proces powrotu po awarii.


Aby uzyskać więcej informacji na temat powrotu po awarii, zobacz [maszyny wirtualne VMware powrotu po awarii z platformy Azure do lokacji lokalnej](./vmware-azure-failback.md).

## <a name="summary"></a>Podsumowanie
Za pomocą Site Recovery można utworzyć kompletny zautomatyzowany plan odzyskiwania po awarii dla aplikacji Dynamics AX. W przypadku przerwy w działaniu, w ciągu kilku sekund można zainicjować pracę w trybie failover z dowolnego miejsca i rozpocząć pracę aplikacji w ciągu kilku minut.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o ochronie obciążeń przedsiębiorstwa za pomocą Site Recovery, zobacz [jakie obciążenia można chronić?](site-recovery-workload.md)
