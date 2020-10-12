---
title: Informacje o procesie przywracania maszyny wirtualnej platformy Azure
description: Dowiedz się, jak usługa Azure Backup przywraca usługi Azure Virtual Machines
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: f9e81c4fa40e5a1d984c163ffa5f37d8092f9032
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985336"
---
# <a name="about-azure-vm-restore"></a>Informacje o przywracaniu maszyny wirtualnej platformy Azure

W tym artykule opisano, jak [usługa Azure Backup](./backup-overview.md) przywraca maszyny wirtualne platformy Azure. Istnieje kilka opcji przywracania. Omawiamy różne scenariusze, które obsługują.

## <a name="concepts"></a>Pojęcia

- **Punkt odzyskiwania** (znany również jako **punkt przywracania**): punkt odzyskiwania to kopia oryginalnych danych, których kopia zapasowa jest tworzona.

- **Warstwa (migawka a magazyn)**: kopia zapasowa maszyny wirtualnej platformy Azure odbywa się w dwóch fazach:

  - W fazie 1 migawka została zapisana wraz z dyskiem. Jest to nazywane **warstwą migawek**. Przywracanie warstwy migawek jest szybsze (niż przywracanie z magazynu), ponieważ eliminuje czas oczekiwania na skopiowanie migawek do magazynu przed wyzwoleniem przywracania. Dlatego przywracanie z warstwy migawek jest również określane jako [natychmiastowe przywracanie](./backup-instant-restore-capability.md).
  - W fazie 2 migawka jest transferowana i przechowywana w magazynie zarządzanym przez usługę Azure Backup. Jest to nazywane **warstwą magazynu**.

- **Odzyskiwanie do oryginalnej lokalizacji (OLR)**: odzyskiwanie wykonane z punktu przywracania do ŹRÓDŁOWEJ maszyny wirtualnej platformy Azure z lokalizacji, w której wykonano kopie zapasowe, zastępując je stanem przechowywanym w punkcie odzyskiwania. Spowoduje to zastąpienie dysku systemu operacyjnego i dysków danych źródłowej maszyny wirtualnej.

- **Odzyskiwanie do lokalizacji alternatywnej (ALR)**: odzyskiwanie wykonane z punktu odzyskiwania do serwera innego niż oryginalny serwer, na którym zostały wykonane kopie zapasowe.

- **Przywracanie na poziomie elementu (ILR):** Przywracanie pojedynczych plików lub folderów w ramach maszyny wirtualnej z punktu odzyskiwania

- **Dostępność (typy replikacji)**: Azure Backup oferuje dwa typy replikacji, aby zapewnić wysoką dostępność magazynu/danych:
  - [Magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replikuje dane trzy razy (tworzy trzy kopie danych) w jednostce skalowania magazynu w centrum danych. Wszystkie kopie danych istnieją w tym samym regionie. Magazyn LRS to ekonomiczna opcja ochrony danych przed awariami sprzętu lokalnego.
  - [Magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) jest ustawieniem domyślnym i zalecaną opcją replikacji. Magazyn GRS replikuje dane do regionu pomocniczego (setki kilometrów od lokalizacji głównej danych źródłowych). GRS koszty więcej niż LRS, ale GRS zapewnia wyższy poziom trwałości danych, nawet jeśli wystąpi awaria regionalna.
  - [Magazyn strefowo nadmiarowy (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replikuje dane w [strefach dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview#availability-zones), gwarantując miejsce zamieszkania i odporność danych w tym samym regionie. ZRS nie ma żadnych przestojów. W związku z tym krytyczne obciążenia wymagające miejsca [zamieszkania](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)i nie mogą mieć żadnych przestojów, ale można utworzyć kopię zapasową w ZRS.

- **Przywracanie między regionami (CRR)**: ponieważ jedna z [opcji przywracania](./backup-azure-arm-restore-vms.md#restore-options), przywracanie między regionami (CRR) umożliwia przywracanie maszyn wirtualnych platformy Azure w regionie pomocniczym, który jest [sparowanym regionem platformy Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="restore-scenarios"></a>Scenariusze przywracania

![Scenariusze przywracania ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Scenariusz**                                                 | **Co jest gotowe**                                             | **Kiedy stosować**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Przywróć, aby utworzyć nową maszynę wirtualną](./backup-azure-arm-restore-vms.md) | Przywraca całą maszynę wirtualną do OLR (jeśli źródłowa maszyna wirtualna nadal istnieje) lub ALR | <li> Jeśli źródłowa maszyna wirtualna zostanie utracona lub uszkodzona, można przywrócić całą maszynę wirtualną  <li> Możesz utworzyć kopię maszyny wirtualnej  <li> Możesz wykonać drążenie przywracania na potrzeby inspekcji lub zgodności  <li> Ta opcja nie będzie działała dla maszyn wirtualnych platformy Azure utworzonych na podstawie obrazów z witryny Marketplace (oznacza to, że nie są one dostępne ze względu na wygaśnięcie licencji). |
| [Przywracanie dysków maszyny wirtualnej](./backup-azure-arm-restore-vms.md#restore-disks) | Przywracanie dysków dołączonych do maszyny wirtualnej                             |  Wszystkie dyski: Ta opcja powoduje utworzenie szablonu i przywrócenie dysku. Można edytować ten szablon z specjalnymi konfiguracjami (na przykład zestawy dostępności), aby spełnić wymagania, a następnie użyć szablonu i przywrócić dysk, aby ponownie utworzyć maszynę wirtualną. |
| [Przywracanie określonych plików na maszynie wirtualnej](./backup-azure-restore-files-from-vm.md) | Wybierz pozycję punkt przywracania, Przeglądaj, wybierz pozycję pliki i przywróć je do tego samego (lub zgodnego) systemu operacyjnego jako maszyny wirtualnej z kopią zapasową. |  Jeśli wiesz, które określone pliki mają być przywracane, Użyj tej opcji zamiast przywracania całej maszyny wirtualnej. |
| [Przywracanie zaszyfrowanej maszyny wirtualnej](./backup-azure-vms-encryption.md) | W portalu Przywróć dyski, a następnie utwórz maszynę wirtualną za pomocą programu PowerShell | <li> [Zaszyfrowana maszyna wirtualna z Azure Active Directory](../virtual-machines/windows/disk-encryption-windows-aad.md)  <li> [Zaszyfrowaną maszynę wirtualną bez usługi Azure AD](../virtual-machines/windows/disk-encryption-windows.md) <li> [Zaszyfrowaną maszynę wirtualną *z usługą Azure AD* migrowana do programu *bez usługi Azure AD*](../virtual-machines/windows/disk-encryption-faq.md#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [Przywracanie między regionami](./backup-azure-arm-restore-vms.md#cross-region-restore) | Tworzenie nowej maszyny wirtualnej lub przywracanie dysków do regionu pomocniczego (region sparowany na platformie Azure) | <li> **Pełna awaria**: w przypadku funkcji przywracania między regionami nie ma czasu oczekiwania na odzyskanie danych w regionie pomocniczym. Możesz inicjować przywracanie w regionie pomocniczym nawet przed zainicjowaniem awarii przez platformę Azure. <li> **Awaria częściowa**: przestoje mogą wystąpić w określonych klastrach magazynu, w których Azure Backup przechowuje dane kopii zapasowej, a nawet w sieci, łącząc Azure Backup i klastry magazynu skojarzone z danymi kopii zapasowych. W przypadku przywracania między regionami można wykonać przywracanie w regionie pomocniczym, używając repliki kopii zapasowej danych w regionie pomocniczym. <li> **Brak przestoju**: można prowadzić przechodzenie do szczegółów działalności biznesowej i odzyskiwania po awarii (BCDR) na potrzeby inspekcji lub zgodności z danymi regionu pomocniczego. Dzięki temu można wykonać przywracanie kopii zapasowej danych w regionie pomocniczym, nawet jeśli w regionie podstawowym nie ma pełnej lub częściowej awarii w celu zapewnienia ciągłości działania i odzyskiwania po awarii.  |

## <a name="next-steps"></a>Następne kroki

- [Często zadawane pytania dotyczące przywracania maszyny wirtualnej](./backup-azure-vm-backup-faq.md#restore)
- [Obsługiwane metody przywracania](./backup-support-matrix-iaas.md#supported-restore-methods)
- [Rozwiązywanie problemów z przywracaniem](./backup-azure-vms-troubleshoot.md#restore)
