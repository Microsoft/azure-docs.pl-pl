---
title: Macierz obsługi kopii zapasowych dysków na platformie Azure
description: Zawiera podsumowanie ustawień pomocy technicznej i ograniczenia kopii zapasowej na dysku platformy Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 7e390458884a2cdad0ba422c09165c825eb76d28
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223327"
---
# <a name="azure-disk-backup-support-matrix-in-preview"></a>Macierz obsługi kopii zapasowych na dysku platformy Azure (w wersji zapoznawczej)

>[!IMPORTANT]
>Kopia zapasowa Azure Disk jest w wersji zapoznawczej bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>[Wypełnij ten formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) , aby utworzyć konto w wersji zapoznawczej.

Aby chronić dyski platformy Azure, można użyć [Azure Backup](./backup-overview.md) . Ten artykuł podsumowuje dostępność regionów, obsługiwane scenariusze i ograniczenia.

## <a name="supported-regions"></a>Obsługiwane regiony

Kopia zapasowa Azure Disk jest dostępna w wersji zapoznawczej w następujących regionach: zachodnie stany USA, zachodnio-środkowe stany USA, Wschodnie stany USA 2, Korea środkowa, Korea Południowa, Japonia Zachodnia, Azja Wschodnia, Zjednoczone Emiraty Arabskie, Brazylia Południowa, Indie Środkowe. 

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

- Obecnie (w trakcie korzystania z wersji zapoznawczej) korzystanie z programu PowerShell i interfejsu wiersza polecenia platformy Azure w celu skonfigurowania kopii zapasowych i przywracania dysków nie jest obsługiwane.

- Podczas konfigurowania kopii zapasowej dysk wybrany do utworzenia kopii zapasowej i Grupa zasobów migawek, w której mają być przechowywane migawki, muszą być częścią tej samej subskrypcji. Nie można utworzyć migawki przyrostowej dla określonego dysku poza subskrypcją tego dysku. Dowiedz się więcej na temat [migawek przyrostowych](../virtual-machines/windows/disks-incremental-snapshots-portal.md#restrictions) dla dysku zarządzanego. Aby uzyskać więcej informacji na temat wybierania grupy zasobów migawek, zobacz  [Konfigurowanie kopii zapasowej](backup-managed-disks.md#configure-backup).

- W przypadku pomyślnej operacji tworzenia kopii zapasowej i przywracania wymagane są przypisania ról zarządzane przez magazyn kopii zapasowych. Używaj tylko definicji ról znajdujących się w dokumentacji. Korzystanie z innych ról, takich jak właściciel, współautor i tak dalej, nie jest obsługiwane. Mogą wystąpić problemy z uprawnieniami, jeśli zaczniesz konfigurować operacje tworzenia kopii zapasowej lub przywracania wkrótce po przypisaniu ról. Wynika to z faktu, że przypisanie roli trwa kilka minut.

- Dyski zarządzane umożliwiają zmianę warstwy wydajności podczas wdrażania lub później, bez zmiany rozmiaru dysku. Rozwiązanie do tworzenia kopii zapasowych na dysku Azure obsługuje zmiany warstwy wydajności na dysku źródłowym, którego kopia zapasowa jest tworzona. Podczas przywracania warstwa wydajności przywróconego dysku będzie taka sama jak na dysku źródłowym w momencie tworzenia kopii zapasowej. Postępuj zgodnie [z dokumentacją,](../virtual-machines/disks-performance-tiers-portal.md) aby zmienić warstwę wydajności dysku po operacji przywracania.

- Obsługa [linków prywatnych](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) dla dysków zarządzanych umożliwia ograniczenie eksportu i importu dysków zarządzanych w taki sposób, aby były one wykonywane tylko w ramach sieci wirtualnej platformy Azure. Usługa Kopia zapasowa Azure Disk obsługuje tworzenie kopii zapasowych dysków z włączonymi prywatnymi punktami końcowymi. Nie obejmuje to danych kopii zapasowej ani migawek, które mają być dostępne za pomocą prywatnego punktu końcowego.

- W trakcie okresu zapoznawczego nie można wyłączyć tworzenia kopii zapasowej, dlatego opcja **Zatrzymaj tworzenie kopii zapasowej i Zachowaj dane kopii w** programie nie jest obsługiwana. Można usunąć wystąpienie kopii zapasowej, które nie tylko zatrzyma kopię zapasową, ale również usunie wszystkie dane kopii zapasowej.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie kopii zapasowej Managed Disks platformy Azure](backup-managed-disks.md)
