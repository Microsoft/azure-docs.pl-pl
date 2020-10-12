---
title: Wybierz opcję migracji VMware z migracją na serwer Azure Migrate | Microsoft Docs
description: Zawiera omówienie opcji migrowania maszyn wirtualnych VMware na platformę Azure przy użyciu migracji Azure Migrate serwera
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: e62b9cea80f1ed7f672135b93e52ba606a717a6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88950225"
---
# <a name="select-a-vmware-migration-option"></a>Wybierz opcję migracji VMware

Maszyny wirtualne programu VMware można migrować do platformy Azure za pomocą narzędzia migracji Azure Migrate Server. To narzędzie oferuje kilka opcji migracji maszyn wirtualnych VMware:

- Migracja przy użyciu replikacji bez wykorzystania agentów. Migrowanie maszyn wirtualnych bez konieczności instalowania na nich jakichkolwiek elementów.
- Migracja z agentem na potrzeby replikacji. Zainstaluj agenta na maszynie wirtualnej na potrzeby replikacji.


## <a name="compare-migration-methods"></a>Porównanie metod migracji

Użyj tych wybranych porównań, aby określić metodę, która ma zostać użyta. Można także zapoznać się z pełnymi wymaganiami dotyczącymi obsługi dla migracji [opartej](migrate-support-matrix-vmware-migration.md#agent-based-migration) [na agentach](migrate-support-matrix-vmware-migration.md#agentless-migration) .

**Ustawienie** | **Bez agenta** | **Na podstawie agenta**
--- | --- | ---
**Uprawnienia platformy Azure** | Musisz mieć uprawnienia do tworzenia projektu Azure Migrate i rejestrowania aplikacji usługi Azure AD utworzonych podczas wdrażania urządzenia Azure Migrate. | Musisz mieć uprawnienia współautora w ramach subskrypcji platformy Azure. 
**Replikacja** | Maksymalnie 300 maszyn wirtualnych może być replikowanych z vCenter Server.<br/> Jeśli masz więcej niż 50 maszyn wirtualnych do migracji, Utwórz wiele partii maszyn wirtualnych.<br/> Replikacja więcej jednocześnie będzie miała wpływ na wydajność.<br/><br/> W portalu można wybrać maksymalnie 10 maszyn na potrzeby replikacji. Aby replikować więcej maszyn, Dodaj w partiach 10.| Wydajność replikacji wzrasta przez skalowanie urządzenia replikacji.
**Wdrażanie urządzenia** | [Urządzenie Azure Migrate](migrate-appliance.md) jest wdrażane lokalnie. | [Urządzenie replikacji Azure Migrate](migrate-replication-appliance.md) jest wdrażane lokalnie.
**Zgodność Site Recovery** | Zgodność. | Nie można przeprowadzić replikacji z migracją serwera Azure Migrate, jeśli skonfigurowano replikację dla maszyny przy użyciu Site Recovery.
**Dysk docelowy** | Dyski zarządzane | Dyski zarządzane
**Limity dysku** | Dysk systemu operacyjnego: 2 TB<br/><br/> Dysk danych: 8 TB<br/><br/> Maksymalna liczba dysków: 60 | Dysk systemu operacyjnego: 2 TB<br/><br/> Dysk danych: 8 TB<br/><br/> Maksymalna liczba dysków: 63
**Przekazywanie dysków** | Nieobsługiwane | Obsługiwane
**Rozruch z interfejsem UEFI** | Nieobsługiwane | Migrowana maszyna wirtualna na platformie Azure zostanie automatycznie przekonwertowana na maszynę wirtualną rozruchową w systemie BIOS.<br/><br/> Dysk systemu operacyjnego powinien mieć maksymalnie cztery partycje, a woluminy powinny być sformatowane w systemie plików NTFS.

## <a name="compare-deployment-steps"></a>Porównanie kroków wdrożenia

Po przejrzeniu ograniczeń można zrozumieć kroki związane z wdrażaniem poszczególnych rozwiązań, które mogą pomóc zdecydować, którą opcję wybrać.

**Zadanie** | **Szczegóły** |**Bez agenta** | **Na podstawie agenta**
--- | --- | --- | ---
**Wdróż urządzenie Azure Migrate** | Lekkie urządzenie działające na maszynie wirtualnej VMware.<br/><br/> Urządzenie służy do odnajdywania i oceniania maszyn oraz do migrowania maszyn przy użyciu migracji bez agentów. | Wymagane.<br/><br/> Jeśli urządzenie zostało już skonfigurowane do oceny, można użyć tego samego urządzenia do migracji bez wykorzystania agentów. | Niewymagane.<br/><br/> Jeśli skonfigurowano urządzenie do oceny, można je pozostawić na miejscu lub usunąć, jeśli skończysz ocenianie.
**Korzystanie z narzędzia do oceny serwera** | Oceń maszyny za pomocą narzędzia do oceny serwera Azure Migrate:. | Przed przeprowadzeniem migracji maszyn można ocenić je, ale nie musisz. | Ocena jest opcjonalna | Ocena jest opcjonalna.
**Korzystanie z narzędzia do migracji serwera** | Dodaj narzędzie do migracji Azure Migrate Server w projekcie Azure Migrate. | Wymagane | Wymagane
**Przygotuj oprogramowanie VMware do migracji** | Skonfiguruj ustawienia na serwerach i maszynach wirtualnych VMware. | Wymagane | Wymagane
**Instalowanie usługi mobilności na maszynach wirtualnych** | Usługa mobilności jest uruchamiana na każdej maszynie wirtualnej, która ma zostać zreplikowana | Niewymagane | Wymagane
**Wdrażanie urządzenia replikacji** | [Urządzenie do replikacji](migrate-replication-appliance.md) jest używane na potrzeby migracji opartej na agentach. Łączy między usługą mobilności działającą na maszynach wirtualnych i migracją serwera. | Niewymagane | Wymagane
**Replikowanie maszyn wirtualnych**. Włącz replikację maszyny wirtualnej. | Skonfiguruj ustawienia replikacji i wybierz maszyny wirtualne do replikacji | Wymagane | Wymagane
**Uruchamianie migracji testowej** | Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. | Wymagane | Wymagane
**Uruchamianie pełnej migracji** | Migrowanie maszyn wirtualnych. | Wymagane | Wymagane



## <a name="next-steps"></a>Następne kroki

[Migrowanie maszyn wirtualnych VMware](tutorial-migrate-vmware.md) z migracją bez agenta.



