---
title: Migrowanie maszyn lokalnych przy użyciu Azure Site Recovery
description: W tym artykule opisano sposób migrowania maszyn lokalnych na platformę Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b978190776aee3c89d3beadde76d20c4327b012f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80388920"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrowanie maszyn lokalnych do platformy Azure


W tym artykule opisano sposób migrowania maszyn lokalnych na platformę Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md). 

> [!TIP]
> Teraz należy używać Azure Migrate do migrowania maszyn lokalnych na platformę Azure, a nie usługi Azure Site Recovery. [Dowiedz się więcej](../migrate/migrate-services-overview.md).


Ten samouczek pokazuje, jak przeprowadzić migrację lokalnych maszyn wirtualnych i serwerów fizycznych na platformę Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Skonfiguruj środowisko źródłowe i docelowe na potrzeby migracji
> * Konfigurowanie zasad replikacji
> * Włączanie replikacji
> * Uruchamianie testowania migracji w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami
> * Uruchamianie jednokrotnego przejścia w tryb failover na platformie Azure


> [!TIP]
> Teraz można migrować serwery lokalne na platformę Azure przy użyciu usługi Azure Migrate. [Dowiedz się więcej](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Przed rozpoczęciem

Urządzenia eksportowane przez sterowniki parawirtualne nie są obsługiwane.


## <a name="prepare-azure-and-on-premises"></a>Przygotowywanie platformy Azure i lokalnie

1. Przygotuj platformę Azure zgodnie z opisem w [tym artykule](tutorial-prepare-azure.md). Chociaż w tym artykule opisano kroki przygotowania do odzyskania po awarii, kroki są również prawidłowe dla migracji.
2. Przygotuj lokalne serwery [programu VMware](vmware-azure-tutorial-prepare-on-premises.md) lub [funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md). W przypadku migrowania maszyn fizycznych nie jest konieczne przygotowanie żadnych elementów. Po prostu Sprawdź [Macierz obsługi](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Wybieranie celu ochrony

Wybierz, co chcesz replikować, i miejsce, do którego chcesz przeprowadzać replikację.
1. Kliknij pozycję **Magazyny usługi Recovery Services** > magazyn.
2. W menu zasób kliknij **Site Recovery** > **Przygotuj** > **cel ochrony**infrastruktury.
3. W obszarze **Cel ochrony** wybierz elementy do migracji.
    - **VMware**: wybierz kolejno pozycje **Na platformę Azure** > **Tak, przy użyciu funkcji VMWare vSphere Hypervisor**.
    - **Komputer fizyczny**: wybierz kolejno pozycje **Na platformę Azure** > **Niezwirtualizowane/inne**.
    - **Hyper-V**: wybierz kolejno pozycje **Azure** > **Tak, przy użyciu funkcji Hyper-V**. Jeśli program VMM zarządza maszynami wirtualnymi funkcji Hyper-V, wybierz pozycję **Tak**.


## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

**Scenariusz** | **Szczegóły**
--- | --- 
VMware | Skonfiguruj [środowisko źródłowe](vmware-azure-set-up-source.md)i skonfiguruj [serwer konfiguracji](vmware-azure-deploy-configuration-server.md).
Maszyna fizyczna | [Skonfiguruj](physical-azure-set-up-source.md) środowisko źródłowe i serwer konfiguracji.
Funkcja Hyper-V | Konfigurowanie [środowiska źródłowego](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Skonfiguruj [środowisko źródłowe](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) dla funkcji Hyper-V wdrożonej przy użyciu programu System Center VMM.

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij pozycję **Przygotuj** > **miejsce docelowe**infrastruktury i wybierz subskrypcję platformy Azure, której chcesz użyć.
2. Określ model wdrażania usługi Resource Manager.
3. Site Recovery sprawdza zasoby platformy Azure.
    - Jeśli migrujesz maszyny wirtualne VMware lub serwery fizyczne, Site Recovery sprawdza, czy masz sieć platformy Azure, w której będą znajdować się maszyny wirtualne platformy Azure po utworzeniu po przejściu do trybu failover.
    - W przypadku migrowania maszyn wirtualnych funkcji Hyper-V Site Recovery sprawdza, czy masz zgodne konto usługi Azure Storage i sieć.
4. W przypadku migrowania maszyn wirtualnych funkcji Hyper-V zarządzanych przez program System Center VMM Skonfiguruj [mapowanie sieci](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

**Scenariusz** | **Szczegóły**
--- | --- 
VMware | Skonfiguruj [zasady replikacji](vmware-azure-set-up-replication.md) dla maszyn wirtualnych VMware.
Maszyna fizyczna | Skonfiguruj [zasady replikacji](physical-azure-disaster-recovery.md#create-a-replication-policy) dla maszyn fizycznych.
Funkcja Hyper-V | Konfigurowanie [zasad replikacji](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Skonfiguruj [zasady replikacji](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) dla funkcji Hyper-V wdrożonej przy użyciu programu System Center VMM.

## <a name="enable-replication"></a>Włączanie replikacji

**Scenariusz** | **Szczegóły**
--- | --- 
VMware | [Włącz replikację](vmware-azure-enable-replication.md) dla maszyn wirtualnych VMware.
Maszyna fizyczna | [Włącz replikację](physical-azure-disaster-recovery.md#enable-replication) dla maszyn fizycznych.
Funkcja Hyper-V | [Włączanie replikacji](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Włącz replikację](hyper-v-vmm-azure-tutorial.md#enable-replication) dla funkcji Hyper-V wdrożonej przy użyciu programu System Center VMM.


## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej

Uruchom [testowanie trybu failover](tutorial-dr-drill-azure.md), aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.


## <a name="migrate-to-azure"></a>Migracja na platformę Azure

Uruchom tryb failover dla maszyn, które chcesz migrować.

1. W obszarze **Ustawienia** > **zreplikowane elementy** kliknij maszynę > **tryb failover**.
2. W obszarze **tryb failover** wybierz **punkt odzyskiwania** , do którego ma zostać przełączona praca awaryjna. Wybierz najnowszy punkt odzyskiwania.
3. Ustawienie klucza szyfrowania nie ma znaczenia w przypadku tego scenariusza.
4. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery spróbuje zamknąć maszyny wirtualne przed przejściem do trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy w trybie failover można wykonać na stronie **zadań** .
5. Sprawdź, czy maszyna wirtualna Azure jest wyświetlana na platformie Azure zgodnie z oczekiwaniami.
6. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną > **Zakończ migrację**. Spowoduje to wykonanie następujących czynności:

   - Kończy proces migracji, kończy replikację lokalnej maszyny wirtualnej i kończy Site Recovery rozliczeń za maszynę wirtualną.
   - W tym kroku oczyszczane są dane replikacji. Nie są jednak usuwane migrowane maszyny wirtualne.

     ![Kończenie migracji](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Nie anuluj trybu failover po rozpoczęciu przełączania**: replikacja maszyny wirtualnej zostanie zatrzymana przed uruchomieniem trybu failover. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego przeprowadzenie zajmuje około 8–10 minut. Dłuższe czasy testowania trybu failover można zauważyć w przypadku serwerów fizycznych, maszyn VMware z systemem Linux, maszyn wirtualnych VMware, które nie mają włączonej usługi DHCP, oraz maszyn wirtualnych VMware, które nie mają następujących sterowników rozruchowych: storvsc, VMBus, storflt, Intelide, ATAPI.

## <a name="after-migration"></a>Po migracji

Po zmigrowaniu maszyn do platformy Azure należy wykonać kilka czynności.

Niektóre czynności można zautomatyzować w ramach procesu migracji przy użyciu wbudowanej funkcji skryptów automatyzacji w [planach odzyskiwania](site-recovery-runbook-automation.md).   


### <a name="post-migration-steps-in-azure"></a>Czynności wykonywane na platformie Azure po migracji

- Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web. 
- Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
- [Agent maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) umożliwia zarządzanie interakcją maszyny wirtualnej z usługą Azure Fabric Controller. Jest to wymagane w przypadku niektórych usług platformy Azure, takich jak Azure Backup, Site Recovery i Azure Security.
    - Podczas migrowania maszyn VMware i serwerów fizycznych razem z usługą mobilności na maszynach z systemem Windows jest instalowany dostępny agent maszyny wirtualnej platformy Azure. Na maszynach wirtualnych z systemem Linux zaleca się zainstalowanie agenta po włączeniu trybu failover.
    - W przypadku migrowania maszyn wirtualnych platformy Azure do regionu pomocniczego przed migracją należy zainicjować agenta maszyny wirtualnej platformy Azure na maszynie wirtualnej.
    - Jeśli migrujesz maszyny wirtualne funkcji Hyper-V do platformy Azure, Zainstaluj agenta maszyny wirtualnej platformy Azure na maszynie wirtualnej platformy Azure po migracji.
- Usuń ręcznie z maszyny wirtualnej wszelkie programy typu dostawca/agent usługi Site Recovery. W przypadku migrowania maszyn wirtualnych VMware lub serwerów fizycznych odinstaluj usługę mobilności z maszyny wirtualnej.
- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](azure-to-azure-quickstart.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokuj i ogranicz dostęp do ruchu przychodzącego za pomocą funkcji [administrowania na czas]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time) usługi Azure Security Center.
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Wdróż usługę [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/ ).
- Na potrzeby monitorowania i zarządzania:
    - Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.

### <a name="post-migration-steps-on-premises"></a>Czynności wykonywane lokalnie po migracji

- Przenieś ruch aplikacji do aplikacji uruchomionej na zmigrowanym wystąpieniu maszyny wirtualnej platformy Azure.
- Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
- Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
- Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure.




## <a name="next-steps"></a>Następne kroki

W tym samouczku przeprowadzono migrację lokalnych maszyn wirtualnych do maszyn wirtualnych platformy Azure. Now

> [!div class="nextstepaction"]
> [Skonfiguruj odzyskiwanie po awarii](azure-to-azure-replicate-after-migration.md) w regionie pomocniczym platformy Azure dla maszyn wirtualnych platformy Azure.

  
