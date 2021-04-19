---
title: Wybierz opcję migracji programu VMware z opcją Azure Migrate Server Migration
description: Omówienie opcji migrowania maszyn wirtualnych VMware na platformę Azure za pomocą Azure Migrate Server Migration
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 22a0629d50ee8181ffcbfe7dad32ab76fb3e68fd
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714168"
---
# <a name="select-a-vmware-migration-option"></a>Wybieranie opcji migracji oprogramowania VMware

Maszyny wirtualne VMware można migrować na platformę Azure przy użyciu Azure Migrate Server Migration. To narzędzie oferuje kilka opcji migracji maszyn wirtualnych VMware:

- Migracja przy użyciu replikacji bez agenta. Migrowanie maszyn wirtualnych bez konieczności instalowania na nich czegokolwiek.
- Migracja z agentem w celu replikacji. Zainstaluj agenta na maszynie wirtualnej w celu replikacji.


## <a name="compare-migration-methods"></a>Porównanie metod migracji

Użyj tych wybranych porównań, aby ułatwić podjęcie decyzji, której metody użyć. Można również zapoznać się z pełnymi wymaganiami w zakresie obsługi [migracji](migrate-support-matrix-vmware-migration.md#agentless-migration) bez agenta i migracji opartej [na agentach.](migrate-support-matrix-vmware-migration.md#agent-based-migration)

**Ustawienie** | **Bez agenta** | **Oparte na agentach**
--- | --- | ---
**Uprawnienia platformy Azure** | Potrzebne są uprawnienia do tworzenia Azure Migrate projektu i rejestrowania aplikacji usługi Azure AD utworzonych podczas wdrażania Azure Migrate wirtualnego. | Potrzebujesz uprawnień współautora w subskrypcji platformy Azure. 
**Replikacja** | Jednocześnie można replikować maksymalnie 500 maszyn wirtualnych z vCenter Server. W portalu można wybrać maksymalnie 10 maszyn jednocześnie do replikacji. Aby replikować więcej maszyn, dodaj w partiach po 10.| Pojemność replikacji zwiększa się przez skalowanie urządzenia replikacji.
**Wdrażanie urządzenia** | Urządzenie [Azure Migrate jest](migrate-appliance.md) wdrażane lokalnie. | Urządzenie [Azure Migrate replikacji](migrate-replication-appliance.md) jest wdrażane lokalnie.
**Site Recovery zgodności** | Zgodny. | Nie można replikować za pomocą Azure Migrate Server Migration, jeśli replikacja dla maszyny została ustawiona przy użyciu Site Recovery.
**Dysk docelowy** | Dyski zarządzane | Dyski zarządzane
**Limity dysków** | Dysk systemu operacyjnego: 2 TB<br/><br/> Dysk danych: 32 TB<br/><br/> Maksymalna liczba dysków: 60 | Dysk systemu operacyjnego: 2 TB<br/><br/> Dysk danych: 32 TB<br/><br/> Maksymalna liczba dysków: 63
**Dyski z danymi passthrough** | Nieobsługiwane | Obsługiwane
**Rozruch UEFI** | Obsługiwane. | Obsługiwane. 
**Połączenia** | Publiczny Internet <br/> Usługa ExpressRoute z komunikacja równorzędna firmy Microsoft <br/> <br/> [Dowiedz się,](./replicate-using-expressroute.md) jak używać prywatnych punktów końcowych do replikacji za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute lub połączenia sieci VPN typu lokacja-lokacja. |Publiczny Internet <br/> ExpressRoute z prywatną komunikacji równorzędnej <br/> Usługa ExpressRoute z komunikacja równorzędna firmy Microsoft <br/> Sieć VPN typu lokacja-lokacja

## <a name="compare-deployment-steps"></a>Porównanie kroków wdrażania

Po przejrzeniu ograniczeń zrozumienie kroków związanych z wdrażaniem poszczególnych rozwiązań może pomóc w podjęciu decyzji o tym, którą opcję wybrać.

**Zadanie** | **Szczegóły** |**Bez agenta** | **Oparte na agentach**
--- | --- | --- | ---
**Wdrażanie urządzenia usługi Azure Migrate** | Lekkie urządzenie, które działa na maszynie wirtualnej VMware.<br/><br/> Urządzenie służy do odnajdywania i oceniania maszyn oraz migrowania maszyn przy użyciu migracji bez agenta. | Wymagane.<br/><br/> Jeśli urządzenie zostało już ustawione do oceny, możesz użyć tego samego urządzenia do migracji bez agenta. | Niewymagane.<br/><br/> Jeśli urządzenie zostało już ustawione do oceny, możesz pozostawić je na miejscu lub usunąć, jeśli ocena zostanie już wykonana.
**Korzystanie z narzędzia do oceny serwera** | Ocenianie maszyn za pomocą Azure Migrate:Server Assessment. | Ocena jest opcjonalna. | Ocena jest opcjonalna.
**Korzystanie z narzędzia do migracji serwera** | Dodaj narzędzie Azure Migrate Server Migration w Azure Migrate projektu. | Wymagane | Wymagane
**Przygotowywanie oprogramowania VMware do migracji** | Konfigurowanie ustawień na serwerach VMware i na komputerach wirtualnych. | Wymagane | Wymagane
**Instalowanie usługa mobilności maszyn wirtualnych** | usługa mobilności na każdej maszynie wirtualnej, którą chcesz replikować | Niewymagane | Wymagane
**Wdrażanie urządzenia replikacji** | Urządzenie [replikacji](migrate-replication-appliance.md) jest używane do migracji opartej na agentach. Nawiązuje ona połączenie między usługa mobilności uruchomionymi na maszynach wirtualnych i migracją serwera. | Niewymagane | Wymagane
**Replikowanie maszyn wirtualnych.** Włącz replikację maszyny wirtualnej. | Konfigurowanie ustawień replikacji i wybieranie maszyn wirtualnych do replikacji | Wymagane | Wymagane
**Uruchamianie migracji testowej** | Uruchom migrację testów, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. | Wymagane | Wymagane
**Uruchamianie pełnej migracji** | Migrowanie maszyn wirtualnych. | Wymagane | Wymagane



## <a name="next-steps"></a>Następne kroki

[Migrowanie maszyn wirtualnych VMware przy użyciu](tutorial-migrate-vmware.md) migracji bez agenta.



