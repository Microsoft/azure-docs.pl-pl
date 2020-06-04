---
title: Wybierz opcję migracji VMware z migracją na serwer Azure Migrate | Microsoft Docs
description: Zawiera omówienie opcji migrowania maszyn wirtualnych VMware na platformę Azure przy użyciu migracji Azure Migrate serwera
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 196b80f600d9895e9fcb6471bfb5df5fb5bbe8d1
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324508"
---
# <a name="select-a-vmware-migration-option"></a>Wybierz opcję migracji VMware

Maszyny wirtualne programu VMware można migrować do platformy Azure za pomocą narzędzia migracji Azure Migrate Server. To narzędzie oferuje kilka opcji migracji maszyn wirtualnych VMware:

- Migracja przy użyciu replikacji bez wykorzystania agentów. Migrowanie maszyn wirtualnych bez konieczności instalowania na nich jakichkolwiek elementów.
- Migracja z agentem na potrzeby replikacji. Zainstaluj agenta na maszynie wirtualnej na potrzeby replikacji.




## <a name="compare-migration-methods"></a>Porównanie metod migracji

Użyj tych wybranych porównań, aby określić metodę, która ma zostać użyta. Można także zapoznać się z pełnymi wymaganiami dotyczącymi obsługi dla migracji [opartej](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) [na agentach](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) .

**Ustawienie** | **Bez agenta** | **Na podstawie agenta**
--- | --- | ---
**Uprawnienia platformy Azure** | Musisz mieć uprawnienia do tworzenia projektu Azure Migrate i rejestrowania aplikacji usługi Azure AD utworzonych podczas wdrażania urządzenia Azure Migrate. | Musisz mieć uprawnienia współautora w ramach subskrypcji platformy Azure. 
**Równoczesna replikacja** | Maksymalnie 100 maszyn wirtualnych może być replikowanych z vCenter Server.<br/> Jeśli masz więcej niż 50 maszyn wirtualnych do migracji, Utwórz wiele partii maszyn wirtualnych.<br/> Replikacja więcej jednocześnie będzie miała wpływ na wydajność. | NA
**Wdrażanie urządzenia** | [Urządzenie Azure Migrate](migrate-appliance.md) jest wdrażane lokalnie. | [Urządzenie replikacji Azure Migrate](migrate-replication-appliance.md) jest wdrażane lokalnie.
**Zgodność Site Recovery** | Zgodność. | Nie można przeprowadzić replikacji z migracją serwera Azure Migrate, jeśli skonfigurowano replikację dla maszyny przy użyciu Site Recovery.
**Dysk docelowy** | Dyski zarządzane | Dyski zarządzane
**Limity dysku** | Dysk systemu operacyjnego: 2 TB<br/><br/> Dysk danych: 4 TB<br/><br/> Maksymalna liczba dysków: 60 | Dysk systemu operacyjnego: 2 TB<br/><br/> Dysk danych: 8 TB<br/><br/> Maksymalna liczba dysków: 63
**Przekazywanie dysków** | Nieobsługiwane | Obsługiwane
**Rozruch z interfejsem UEFI** | Nieobsługiwane | Migrowana maszyna wirtualna na platformie Azure zostanie automatycznie przekonwertowana na maszynę wirtualną rozruchową w systemie BIOS.<br/><br/> Dysk systemu operacyjnego powinien mieć maksymalnie cztery partycje, a woluminy powinny być sformatowane w systemie plików NTFS.


## <a name="deployment-steps-comparison"></a>Porównanie kroków wdrożenia

Po przejrzeniu ograniczeń można zrozumieć kroki związane z wdrażaniem poszczególnych rozwiązań, które mogą pomóc zdecydować, którą opcję wybrać.

**Zadanie** | **Szczegóły** |**Bez agenta** | **Na podstawie agenta**
--- | --- | --- | ---
**Stopnia** | Oceń serwery przed migracją.  Ocena jest opcjonalna. Zalecamy, aby oceniać maszyny przed ich migracją, ale nie musisz. <br/><br/> W celu dokonania oceny Azure Migrate konfiguruje lekkie urządzenie w celu odnajdywania i oceniania maszyn wirtualnych. | W przypadku uruchomienia migracji bez agenta po dokonaniu oceny to samo urządzenie Azure Migrate skonfigurowane do oceny jest używane do migracji bez wykorzystania agentów.  |  Jeśli przeprowadzasz migrację opartą na agencie po dokonaniu oceny, urządzenie skonfigurowane do oceny nie jest używane podczas migracji opartej na agencie. Możesz opuścić urządzenie lub je usunąć, jeśli nie chcesz jeszcze przeanalizować i ocenić.
**Przygotuj serwery i maszyny wirtualne VMware do migracji** | Skonfiguruj kilka ustawień na serwerach i maszynach wirtualnych VMware. | Wymagane | Wymagane
**Dodawanie narzędzia migracji serwera** | Dodaj narzędzie do migracji Azure Migrate Server w projekcie Azure Migrate. | Wymagane | Wymagane
**Wdróż urządzenie Azure Migrate** | Skonfiguruj lekkie urządzenie na maszynie wirtualnej VMware na potrzeby odnajdywania i oceny maszyn wirtualnych. | Wymagane | Niewymagane.
**Instalowanie usługi mobilności na maszynach wirtualnych** | Zainstaluj usługę mobilności na każdej maszynie wirtualnej, która ma zostać zreplikowana | Niewymagane | Wymagane
**Wdrażanie urządzenia replikacji migracji serwera Azure Migrate** | Konfigurowanie urządzenia na maszynie wirtualnej VMware w celu odnajdywania maszyn wirtualnych i mostkowania między usługą mobilności działającą na maszynach wirtualnych i migracji Azure Migrate serwera | Niewymagane | Wymagane
**Replikowanie maszyn wirtualnych**. Włącz replikację maszyny wirtualnej. | Skonfiguruj ustawienia replikacji i wybierz maszyny wirtualne do replikacji | Wymagane | Wymagane
**Uruchamianie migracji testowej** | Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. | Wymagane | Wymagane
**Uruchamianie pełnej migracji** | Migrowanie maszyn wirtualnych. | Wymagane | Wymagane




## <a name="next-steps"></a>Następne kroki

[Migrowanie maszyn wirtualnych VMware](tutorial-migrate-vmware.md) z migracją bez agenta.



