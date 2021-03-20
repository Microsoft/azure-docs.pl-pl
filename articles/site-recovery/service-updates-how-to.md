---
title: Aktualizacje i uaktualnienia składników w Azure Site Recovery
description: Zawiera omówienie aktualizacji usług Azure Site Recovery i uaktualnień składników.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: ramamill
ms.openlocfilehash: a1ea8b6fb9800d796670161288be0d86ce6ffc42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89424943"
---
# <a name="service-updates-in-site-recovery"></a>Aktualizacje usługi w Site Recovery

Ten artykuł zawiera omówienie aktualizacji [Azure Site Recovery](site-recovery-overview.md) i opisuje sposób uaktualniania składników Site Recovery.

Site Recovery regularnie publikuje aktualizacje usługi. Aktualizacje obejmują nowe funkcje, ulepszenia obsługi, aktualizacje składników i poprawki błędów. Aby móc korzystać z najnowszych funkcji i poprawek, zalecamy korzystanie z najnowszych wersji składników Site Recovery. 
 
 
## <a name="updates-support"></a>Obsługa aktualizacji

### <a name="support-statement-for-azure-site-recovery"></a>Deklaracja wsparcia dla Azure Site Recovery

Zalecamy zawsze uaktualnienie do najnowszej wersji składników:

**W przypadku każdej nowej wersji "n" wydanego składnika Azure Site Recovery wszystkie wersje poniżej "n-4" są uznawane za nieobsługiwane**. 

> [!IMPORTANT]
> Oficjalne wsparcie dotyczy uaktualniania programu z wersji > N-4 do N wersji. Jeśli na przykład korzystasz z wersji N-6, musisz najpierw przeprowadzić uaktualnienie do wersji N-4, a następnie uaktualnić do N.


### <a name="links-to-currently-supported-update-rollups"></a>Linki do obecnie obsługiwanych pakietów zbiorczych aktualizacji

 Zapoznaj się z najnowszym pakietem zbiorczym aktualizacji (wersja N) w [tym artykule](site-recovery-whats-new.md). Należy pamiętać, że Site Recovery zapewnia obsługę wersji N-4.



## <a name="component-expiry"></a>Wygaśnięcie składnika

Site Recovery powiadamia o wygasłych składnikach (lub bliskich wygaśnięcia) poczty e-mail (Jeśli subskrybujesz powiadomienia e-mail) lub na pulpicie nawigacyjnym magazynu w portalu.

- Ponadto gdy aktualizacje są dostępne, w widoku infrastruktura dla danego scenariusza w portalu, obok składnika zostanie wyświetlony przycisk **Aktualizuj dostępne** . Ten przycisk przekierowuje do linku do pobrania najnowszej wersji składnika.
-  Powiadomienia pulpitu nawigacyjnego dla magazynów nie są dostępne w przypadku replikowania maszyn wirtualnych funkcji Hyper-V. 

Powiadomienia e-mail są wysyłane w następujący sposób.

**Godzina** | **Częstotliwość**
--- | ---
60 dni przed wygaśnięciem składnika | Raz w tygodniu
Następne 53 dni | Raz w tygodniu
Ostatnie 7 dni | raz dziennie
Po wygaśnięciu | Raz w tygodniu


### <a name="upgrading-outside-official-support"></a>Uaktualnianie poza oficjalną pomocą techniczną

Jeśli różnica między wersją składnika a najnowszą wersją jest większa niż 4, jest to uznawane za pomoc techniczną. W takim przypadku należy przeprowadzić uaktualnienie w następujący sposób: 

1. Uaktualnij aktualnie zainstalowany składnik do bieżącej wersji plus cztery. Na przykład jeśli Twoja wersja jest 9,16, a następnie przeprowadź uaktualnienie do 9,20.
2. Następnie przejdź do następnej zgodnej wersji. W naszym przykładzie po uaktualnieniu 9,16 do 9,20 należy przeprowadzić uaktualnienie do 9,24. 

Wykonaj ten sam proces dla wszystkich odpowiednich składników.

### <a name="support-for-latest-operating-systemskernels"></a>Obsługa najnowszych systemów operacyjnych/jądra

> [!NOTE]
> Jeśli zaplanowano okno obsługi i w nim znajduje się ponowny rozruch, zalecamy najpierw uaktualnić składniki Site Recovery, a następnie kontynuować pracę z pozostałą częścią zaplanowanych działań w oknie obsługi.

1. Przed uaktualnieniem systemu operacyjnego/wersji jądra Sprawdź, czy wersja docelowa jest obsługiwana Site Recovery. 

    - Obsługa [maszyn wirtualnych platformy Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) .
    - Obsługa oprogramowania [VMware/serwera fizycznego](vmware-physical-azure-support-matrix.md#replicated-machines)
    - Obsługa [funkcji Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) .
2. Przejrzyj [dostępne aktualizacje](site-recovery-whats-new.md) , aby dowiedzieć się, co chcesz uaktualnić.
3. Uaktualnij do najnowszej wersji Site Recovery.
4. Uaktualnij system operacyjny/jądro do wymaganych wersji.
5. Ponowne uruchomienie.


Ten proces zapewnia, że system operacyjny komputera/jądro zostanie uaktualnione do najnowszej wersji, a najnowsze Site Recovery zmiany, które są konieczne do obsługi nowej wersji, są ładowane na komputerze.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Odzyskiwanie po awarii maszyn wirtualnych platformy Azure na platformę Azure

W tym scenariuszu zdecydowanie zalecamy [włączenie aktualizacji automatycznych](azure-to-azure-autoupdate.md). Można zezwolić Site Recovery na zarządzanie aktualizacjami w następujący sposób:

- Podczas procesu włączania replikacji.
- Ustawiając ustawienia aktualizacji rozszerzenia w magazynie.

Jeśli chcesz ręcznie zarządzać aktualizacjami, możesz wybrać jedną z następujących opcji:

1. Gdy dostępna jest nowa aktualizacja agenta, Site Recovery udostępnia powiadomienie w magazynie w górnej części strony. W magazynie > **zreplikowane elementy** kliknij to powiadomienie w górnej części ekranu: 
    
    **Dostępna jest nowa aktualizacja agenta Site Recovery replikacji. Kliknij, aby zainstalować >** <br/><br/>Wybierz Maszyny wirtualne, dla których chcesz zastosować aktualizację, a następnie kliknij przycisk **OK**.

2. Na stronie Przegląd odzyskiwania po awarii maszyny wirtualnej znajdziesz pole "stan agenta", które będzie miało wpływ na "uaktualnienie krytyczne", jeśli Agent wygaśnie. Kliknij go i postępuj zgodnie z instrukcjami, aby ręcznie uaktualnić maszynę wirtualną.

## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Odzyskiwanie maszyn wirtualnych VMware/serwerów fizycznych na platformę Azure

1. W oparciu o bieżącą wersję i [instrukcję pomocy technicznej](#support-statement-for-azure-site-recovery)Zainstaluj najpierw aktualizację na lokalnym serwerze konfiguracji, korzystając z [tych instrukcji](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). 
2. Jeśli masz skalowalne w poziomie serwery procesów, zaktualizuj je dalej, korzystając z [tych instrukcji](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Aby zaktualizować agenta mobilności na każdej chronionej maszynie, zapoznaj się z [tym](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) artykułem.

### <a name="reboot-after-mobility-service-upgrade"></a>Uruchom ponownie po uaktualnieniu usługi mobilności

Zaleca się ponowne uruchomienie po każdym uaktualnieniu usługi mobilności, aby upewnić się, że wszystkie najnowsze zmiany są ładowane na maszynie źródłowej.

Nie jest wymagany ponowny rozruch, chyba że różnica między wersją agenta podczas ostatniego ponownego rozruchu a bieżącą wersją jest większa niż 4.

W przykładzie w tabeli pokazano, jak to działa.

|**Wersja agenta (ostatni ponowny rozruch)** | **Uaktualnienie do** | **Obowiązkowy ponowny rozruch?**|
|---------|---------|---------|
|9,16 |  9,18 | Nieobowiązkowe|
|9,16 | 9,19 | Nieobowiązkowe|
| 9,16 | 9,20 | Nieobowiązkowe
 | 9,16 | 9,21 | Obowiązkowy.<br/><br/> Uaktualnij program do wersji 9,20, a następnie uruchom ponownie przed uaktualnieniem do wersji 9,21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Odzyskiwanie po awarii maszyn wirtualnych funkcji Hyper-V na platformę Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Między lokacją funkcji Hyper-V i platformą Azure

1. Pobierz aktualizację dla dostawcy Site Recovery Microsoft Azure.
2. Zainstaluj dostawcę na każdym serwerze funkcji Hyper-V zarejestrowanym w Site Recovery. Jeśli używasz klastra, Uaktualnij go we wszystkich węzłach klastra.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Między lokalną lokacją programu VMM i platformą Azure
1. Pobierz aktualizację dla dostawcy Site Recovery Microsoft Azure.
2. Zainstaluj dostawcę na serwerze programu VMM. Jeśli program VMM został wdrożony w klastrze, zainstaluj go we wszystkich węzłach klastra.
3. Zainstaluj najnowszego agenta Microsoft Azure Recovery Services na wszystkich hostach funkcji Hyper-V lub w węzłach klastra.


## <a name="between-two-on-premises-vmm-sites"></a>Między dwiema lokalnymi lokacjami programu VMM
1. Pobierz najnowszą aktualizację dla dostawcy Site Recovery Microsoft Azure.
2. Zainstaluj najnowszego dostawcę na serwerze programu VMM zarządzającym pomocniczą lokacją odzyskiwania. Jeśli program VMM został wdrożony w klastrze, zainstaluj go we wszystkich węzłach klastra.
3. Po zaktualizowaniu lokacji odzyskiwania zainstaluj dostawcę na serwerze programu VMM, który zarządza lokacją główną.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z naszą stroną [aktualizacji platformy Azure](https://azure.microsoft.com/updates/?product=site-recovery) , aby śledzić nowe aktualizacje i wydania.
