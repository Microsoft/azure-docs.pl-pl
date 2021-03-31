---
title: Przenoszenie magazynu Azure Site Recovery do innego regionu
description: Opisuje sposób przenoszenia magazynu Recovery Services (Azure Site Recovery) do innego regionu platformy Azure
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: a8cb5ec782b5932c13e321b2ba2d6513597fef52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87422644"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Przenoszenie magazynu Recovery Services i konfiguracji Azure Site Recovery do innego regionu platformy Azure

Istnieją różne scenariusze, w których warto przenieść istniejące zasoby platformy Azure z jednego regionu do innego. Przykłady służą do zarządzania, przyczyn ładu lub z powodu fuzji i przejęć firmy. Jednym z pokrewnych zasobów można przenieść po przeniesieniu maszyn wirtualnych platformy Azure do konfiguracji odzyskiwania po awarii. 

Nie ma pierwszej klasy metody przenoszenia istniejącej konfiguracji odzyskiwania po awarii z jednego regionu do innego. Wynika to z faktu, że Twój region docelowy został skonfigurowany na podstawie regionu źródłowej maszyny wirtualnej. W przypadku podjęcia decyzji o zmianie regionu źródłowego wcześniej istniejące konfiguracje regionu docelowego nie mogą być ponownie używane i muszą zostać zresetowane. W tym artykule opisano proces krok po kroku umożliwiający ponowne skonfigurowanie konfiguracji odzyskiwania po awarii i przeniesienie jej do innego regionu.

W tym dokumencie zawarto następujące:

> [!div class="checklist"]
> * Sprawdź wymagania wstępne dotyczące przenoszenia.
> * Zidentyfikuj zasoby, które były używane przez Azure Site Recovery.
> * Wyłącz replikację.
> * Usuń zasoby.
> * Skonfiguruj Site Recovery w oparciu o nowy region źródłowy dla maszyn wirtualnych.

> [!IMPORTANT]
> Obecnie nie istnieje pierwszy sposób przenoszenia magazynu Recovery Services i konfiguracji odzyskiwania po awarii, podobnie jak w przypadku innego regionu. Ten artykuł przeprowadzi Cię przez proces wyłączania replikacji i konfigurowania jej w nowym regionie.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed przystąpieniem do przenoszenia maszyn wirtualnych platformy Azure do innego regionu należy usunąć i usunąć konfigurację odzyskiwania po awarii. 

  > [!NOTE]
  > Jeśli nowy region docelowy dla maszyny wirtualnej platformy Azure jest taki sam jak region docelowy odzyskiwania po awarii, możesz użyć istniejącej konfiguracji replikacji i przenieść ją. Wykonaj kroki opisane w sekcji [przenoszenie maszyn wirtualnych platformy Azure IaaS do innego regionu platformy Azure](azure-to-azure-tutorial-migrate.md).

- Upewnij się, że podejmowana jest świadoma decyzja i że zainteresowane strony są świadome. Maszyna wirtualna nie będzie chroniona przed awariami, dopóki nie zakończy się przeniesienie maszyny wirtualnej.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Zidentyfikuj zasoby, które były używane przez Azure Site Recovery
Zalecamy wykonanie tego kroku przed przejściem do kolejnego. Podczas replikowania maszyn wirtualnych łatwiej jest identyfikować odpowiednie zasoby.

Dla każdej zreplikowanej maszyny wirtualnej platformy Azure przejdź do **pozycji elementy chronione**  >  **zreplikowane**  >  **Właściwości** elementów i zidentyfikuj następujące zasoby:

- Docelowa grupa zasobów
- Konto magazynu pamięci podręcznej
- Docelowe konto magazynu (w przypadku niezarządzanej maszyny wirtualnej platformy Azure opartej na dyskach) 
- Sieć docelowa


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Wyłącz istniejącą konfigurację odzyskiwania po awarii

1. Przejdź do magazynu Recovery Services.
2. W obszarze **chronione elementy**  >  **zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę i wybierz pozycję **Wyłącz replikację**.
3. Powtórz ten krok dla wszystkich maszyn wirtualnych, które chcesz przenieść.

> [!NOTE]
> Usługa mobilności nie zostanie odinstalowana z serwerów chronionych. Należy odinstalować je ręcznie. Jeśli planujesz ponownie chronić serwer, możesz pominąć Odinstalowywanie usługi mobilności.

## <a name="delete-the-resources"></a>Usuwanie zasobów

1. Przejdź do magazynu Recovery Services.
2. Wybierz pozycję **Usuń**.
3. Usuń wszystkie inne [zidentyfikowane wcześniej](#identify-the-resources-that-were-used-by-azure-site-recovery)zasoby.
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do nowego regionu docelowego

Wykonaj kroki opisane w tych artykułach w zależności od wymagań związanych z przenoszeniem maszyn wirtualnych platformy Azure do regionu docelowego:

- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](azure-to-azure-tutorial-migrate.md)
- [Przenoszenie maszyn wirtualnych platformy Azure do Strefy dostępności](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Skonfiguruj Site Recovery w oparciu o nowy region źródłowy dla maszyn wirtualnych

Skonfiguruj odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure, które zostały przeniesione do nowego regionu, wykonując kroki opisane w temacie [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure](azure-to-azure-tutorial-enable-replication.md).
