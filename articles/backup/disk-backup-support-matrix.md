---
title: Macierz obsługi kopii zapasowych dysków na platformie Azure
description: Zawiera podsumowanie ustawień pomocy technicznej i ograniczenia kopii zapasowej na dysku platformy Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 88ec26837cc8f69c1e84c77ea6b57ce16e462e0a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612861"
---
# <a name="azure-disk-backup-support-matrix"></a>Macierz obsługi kopii zapasowych dysków na platformie Azure

Aby chronić dyski platformy Azure, można użyć [Azure Backup](./backup-overview.md) . Ten artykuł podsumowuje dostępność regionów, obsługiwane scenariusze i ograniczenia.

## <a name="supported-regions"></a>Obsługiwane regiony

Kopia zapasowa dysku platformy Azure jest dostępna w następujących regionach: zachodnie stany USA, zachodnie stany USA 2, zachodnie stany USA, Wschodnie stany USA, Wschodnie stany USA 2, środkowe stany USA, Południowo-środkowe US, Północno-środkowe stany USA, Kanada środkowa, Szwajcaria Zachodnia Szwajcaria Północna Zachodnie Zjednoczone Królestwo Południowe Zjednoczone Królestwo Północna Republika Południowej Afryki Południowe Zjednoczone Emiraty Arabskie , Australia Wschodnia, Korea środkowa, Korea Południowa, Japonia Wschodnia, Japonia Zachodnia, Azja Wschodnia, Azja Południowo-Wschodnia, Indie Środkowe. 

Więcej regionów zostanie ogłoszonych, gdy staną się dostępne.

## <a name="limitations"></a>Ograniczenia

- Kopia zapasowa Azure Disk jest obsługiwana w przypadku usługi Azure Managed Disks, w tym dysków udostępnionych (udostępniona dysków SSD Premium). Dyski niezarządzane nie są obsługiwane. Obecnie to rozwiązanie nie obsługuje Ultra-disks, w tym udostępnionych dysków twardych, z powodu braku możliwości migawek.

- Usługa Kopia zapasowa Azure Disk obsługuje tworzenie kopii zapasowych dysku akcelerator zapisu. Jednak podczas przywracania dysk zostanie przywrócony jako normalny dysk. Na dysku można włączyć szybsze zapisywanie pamięci podręcznej po zainstalowaniu go na maszynie wirtualnej.

- Azure Backup umożliwia tworzenie kopii zapasowych usługi Azure Managed disks w warstwie funkcjonalnej z obsługą wielu kopii zapasowych dziennie. Kopie zapasowe nie są kopiowane do magazynu kopii zapasowych.

- Obecnie opcja Original-Location Recovery (OLR) do przywrócenia przez zastąpienie istniejących dysków źródłowych, z których wykonano kopie zapasowe, nie jest obsługiwana. Można przywrócić z punktu odzyskiwania, aby utworzyć nowy dysk w tej samej grupie zasobów co dysk źródłowy, z którego wykonano kopie zapasowe lub w innej grupie zasobów. Jest to tzw. Alternate-Location Recovery (ALR).

- Azure Backup dla Managed Disks używa migawek przyrostowych, które są ograniczone do 200 migawek na dysk. Aby umożliwić tworzenie kopii zapasowych na żądanie poza zaplanowanymi kopiami zapasowymi, zasady tworzenia kopii zapasowych ograniczają łączną liczbę kopii zapasowych do 180. Dowiedz się więcej o [przyrostowej migawce](../virtual-machines/disks-incremental-snapshots.md#restrictions) dla dysków zarządzanych.

- [Limity subskrypcji i usług](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits) platformy Azure mają zastosowanie do łącznej liczby migawek dysków na region na subskrypcję.

- Migawki do punktu w czasie z wieloma dyskami dołączonymi do maszyny wirtualnej nie są obsługiwane.

- Magazyn kopii zapasowych i dyski, których kopia zapasowa ma zostać utworzona, mogą znajdować się w tych samych lub różnych subskrypcjach. Jednak zarówno magazyn kopii zapasowych, jak i dysk, które mają być tworzone, muszą znajdować się w tym samym regionie.

- Dyski, których kopia zapasowa ma zostać utworzona, a grupa zasobów migawek, w której migawki są przechowywane lokalnie, muszą znajdować się w tej samej subskrypcji.

- Obsługiwane jest przywrócenie dysku z kopii zapasowej do tej samej lub innej subskrypcji. Jednak przywrócony dysk zostanie utworzony w tym samym regionie co migawka.

- Nie można chronić dysków szyfrowanych w programie ADE.

- Dyski zaszyfrowane przy użyciu kluczy zarządzanych przez platformę lub klucze zarządzane przez klienta są obsługiwane. Jednak przywracanie zakończy się niepowodzeniem dla punktów przywracania dysku, który jest szyfrowany przy użyciu kluczy zarządzanych przez klienta (CMK), jeśli klucz magazynu kluczy ustawienia szyfrowania dysków jest wyłączony lub usunięty.

- Obecnie nie można modyfikować zasad tworzenia kopii zapasowych i nie można zmienić grupy zasobów migawek przypisanej do wystąpienia kopii zapasowej podczas konfigurowania kopii zapasowej dysku.

- Obecnie Azure Portal środowisko konfigurowania kopii zapasowych dysków jest ograniczone do maksymalnie 20 dysków z tej samej subskrypcji.

- Usługa Kopia zapasowa Azure Disk obsługuje program PowerShell. Obecnie interfejs wiersza polecenia platformy Azure nie jest obsługiwany.

- Podczas konfigurowania kopii zapasowej dysk wybrany do utworzenia kopii zapasowej i Grupa zasobów migawek, w której mają być przechowywane migawki, muszą być częścią tej samej subskrypcji. Nie można utworzyć migawki przyrostowej dla określonego dysku poza subskrypcją tego dysku. Dowiedz się więcej na temat [migawek przyrostowych](../virtual-machines/disks-incremental-snapshots.md#restrictions) dla dysku zarządzanego. Aby uzyskać więcej informacji na temat wybierania grupy zasobów migawek, zobacz  [Konfigurowanie kopii zapasowej](backup-managed-disks.md#configure-backup).

- W przypadku pomyślnej operacji tworzenia kopii zapasowej i przywracania wymagane są przypisania ról zarządzane przez magazyn kopii zapasowych. Używaj tylko definicji ról znajdujących się w dokumentacji. Korzystanie z innych ról, takich jak właściciel, współautor i tak dalej, nie jest obsługiwane. Mogą wystąpić problemy z uprawnieniami, jeśli zaczniesz konfigurować operacje tworzenia kopii zapasowej lub przywracania wkrótce po przypisaniu ról. Wynika to z faktu, że przypisanie roli trwa kilka minut.

- Dyski zarządzane umożliwiają zmianę warstwy wydajności podczas wdrażania lub później, bez zmiany rozmiaru dysku. Rozwiązanie do tworzenia kopii zapasowych na dysku Azure obsługuje zmiany warstwy wydajności na dysku źródłowym, którego kopia zapasowa jest tworzona. Podczas przywracania warstwa wydajności przywróconego dysku będzie taka sama jak na dysku źródłowym w momencie tworzenia kopii zapasowej. Postępuj zgodnie [z dokumentacją,](../virtual-machines/disks-performance-tiers-portal.md) aby zmienić warstwę wydajności dysku po operacji przywracania.

- Obsługa [linków prywatnych](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) dla dysków zarządzanych umożliwia ograniczenie eksportu i importu dysków zarządzanych w taki sposób, aby były one wykonywane tylko w ramach sieci wirtualnej platformy Azure. Usługa Kopia zapasowa Azure Disk obsługuje tworzenie kopii zapasowych dysków z włączonymi prywatnymi punktami końcowymi. Nie obejmuje to danych kopii zapasowej ani migawek, które mają być dostępne za pomocą prywatnego punktu końcowego.

- Można usunąć wystąpienie kopii zapasowej, co spowoduje zatrzymanie tworzenia kopii zapasowej, a także usunięcie wszystkich danych kopii zapasowej. Obecnie nie można wyłączyć wykonywania kopii zapasowej, ponieważ opcja **Zatrzymaj wykonywanie kopii zapasowej i Zachowaj dane kopii zapasowej** nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie kopii zapasowej Managed Disks platformy Azure](backup-managed-disks.md)