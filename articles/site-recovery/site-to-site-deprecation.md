---
title: Wycofanie odzyskiwania po awarii między lokacjami zarządzanymi przez klienta (z programem VMM) przy użyciu Azure Site Recovery | Microsoft Docs
description: Szczegółowe informacje o nadchodzącym zaniechaniu odzyskiwania po awarii między lokacjami należącymi do klienta przy użyciu funkcji Hyper-V i między lokacjami zarządzanymi przez program SCVMM a opcją alternatywną
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421845"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Wycofanie odzyskiwania po awarii między lokacjami zarządzanymi przez klienta (z programem VMM) przy użyciu Azure Site Recovery

W tym artykule opisano nadchodzący plan wycofania, odpowiednie implikacje i alternatywne opcje dostępne dla klientów w następującym scenariuszu:

Program DR między lokacjami należącymi do klienta zarządzanymi przez System Center Virtual Machine Manager (SCVMM) przy użyciu Site Recovery

> [!IMPORTANT]
> Aby uniknąć zakłóceń w środowisku programu, klienci są zachęcani do podjęcia czynności zaradczych. 

## <a name="what-changes-should-you-expect"></a>Jakie zmiany należy oczekiwać?

- Od marca 2020 r. otrzymasz powiadomienia Azure Portal & wiadomości e-mail z nadchodzącym zaniechaniem replikacji między lokacjami maszyn wirtualnych funkcji Hyper-V. Wycofanie zaplanowano na 2023 marca.

- Jeśli masz istniejącą konfigurację, nie będzie to miało wpływu na konfigurację.

- Gdy scenariusze są przestarzałe, chyba że klient korzysta z alternatywnych metod, istniejące replikacje mogą ulec zakłóceniu. Klienci nie będą mogli wyświetlać, zarządzać ani wykonywać żadnych operacji związanych z odzyskiwaniem po awarii za pomocą środowiska Azure Site Recovery w Azure Portal.
 
## <a name="alternatives"></a>Alternatywy 

Poniżej znajdują się alternatywy, które klient może wybrać, aby upewnić się, że nie ma to wpływu na strategię odzyskiwania po wycofaniu scenariusza. 

- Opcja 1 (zalecana): Wybierz, aby [rozpocząć korzystanie z platformy Azure jako elementu docelowego odzyskiwania po](hyper-v-vmm-azure-tutorial.md)awarii.


- Opcja 2: Wybierz, aby kontynuować replikację między lokacjami przy użyciu podstawowego [rozwiązania funkcji Hyper-V Replica](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), ale nie będziesz w stanie zarządzać konfiguracjami Dr przy użyciu Azure Site Recovery w Azure Portal. 


## <a name="remediation-steps"></a>Instrukcje rozwiązania problemu

W przypadku wybrania opcji 1 wykonaj następujące czynności:

1. [Wyłącz ochronę wszystkich maszyn wirtualnych skojarzonych z maszyną wirtualną](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Użyj opcji **Wyłącz replikację i Usuń** lub Uruchom skrypty wymienione, aby upewnić się, że ustawienia replikacji są czyszczone. 

2. [Wyrejestruj wszystkie serwery programu VMM](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) z konfiguracji replikacji między lokacjami.

3. [Przygotuj zasoby platformy Azure](tutorial-prepare-azure-for-hyperv.md) , aby umożliwić replikację maszyn wirtualnych.
4. [Przygotowywanie lokalnych serwerów funkcji Hyper-V](hyper-v-prepare-on-premises-tutorial.md)
5. [Konfigurowanie replikacji dla maszyn wirtualnych w chmurze programu VMM](hyper-v-vmm-azure-tutorial.md)
6. Opcjonalne, ale zalecane: [Uruchamianie](tutorial-dr-drill-azure.md) testowania odzyskiwania po awarii

W przypadku wybrania opcji 2 z funkcji Hyper-V Replica wykonaj następujące czynności:

1. W obszarze **chronione elementy**  >  **zreplikowane elementy**kliknij prawym przyciskiem myszy maszynę > **Wyłącz replikację**.
2. W obszarze **Wyłącz replikację**wybierz pozycję **Usuń**.

    Spowoduje to usunięcie zreplikowanego elementu z Azure Site Recovery (rozliczenia są zatrzymane). Konfiguracja replikacji na lokalnej maszynie wirtualnej **nie zostanie** wyczyszczona. 

## <a name="next-steps"></a>Następne kroki
Zaplanuj wycofanie i wybierz opcję alternatywną, która jest najbardziej odpowiednia dla infrastruktury i firmy. Jeśli masz jakieś zapytania dotyczące tego, skontaktuj się z pomoc techniczna firmy Microsoft

