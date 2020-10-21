---
title: Tworzenie oferty maszyny wirtualnej platformy Azure w witrynie Azure Marketplace przy użyciu własnego obrazu
description: Dowiedz się, jak opublikować ofertę maszyny wirtualnej w witrynie Azure Marketplace przy użyciu własnego obrazu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284806"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Jak utworzyć maszynę wirtualną przy użyciu własnego obrazu

W tym artykule opisano sposób tworzenia i wdrażania obrazu maszyny wirtualnej (VM) dostarczonej przez użytkownika.

> [!NOTE]
> Przed rozpoczęciem tej procedury zapoznaj się z [wymaganiami technicznymi](marketplace-virtual-machines.md#technical-requirements) dotyczącymi ofert maszyn wirtualnych platformy Azure, łącznie z wymaganiami dotyczącymi wirtualnego dysku twardego (VHD).

Aby zamiast tego użyć zatwierdzonego obrazu podstawowego, postępuj zgodnie z instrukcjami w temacie [Tworzenie obrazu maszyny wirtualnej na podstawie zatwierdzonej podstawy](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Konfigurowanie maszyny wirtualnej

W tej sekcji opisano, jak zmienić rozmiar, zaktualizować i uogólnić maszynę wirtualną platformy Azure. Te kroki są niezbędne, aby przygotować maszynę wirtualną do wdrożenia w portalu Azure Marketplace.

### <a name="size-the-vhds"></a>Rozmiar dysków VHD

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Zainstaluj najnowsze aktualizacje

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Wykonaj dodatkowe sprawdzenia zabezpieczeń

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Wykonywanie konfiguracji niestandardowej i zaplanowanych zadań

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>Przekazywanie wirtualnego dysku twardego do platformy Azure

Skonfiguruj i przygotuj maszynę wirtualną do przekazania zgodnie z opisem w artykule [Przygotowywanie wirtualnego dysku twardego systemu Windows lub dysku VHDX do przekazania do platformy Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) lub [tworzenia i przekazywania dysku VHD z systemem Linux](../virtual-machines/linux/create-upload-generic.md).

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>Wyodrębnij wirtualny dysk twardy z obrazu (w przypadku korzystania z usług kompilowania obrazów)

Jeśli używasz usługi tworzenia obrazów, takiej jak program [Packer](https://www.packer.io/), może być konieczne wyodrębnienie wirtualnego dysku twardego z obrazu. Nie ma żadnego bezpośredniego sposobu, aby to zrobić. Konieczne będzie utworzenie maszyny wirtualnej i wyodrębnienie wirtualnego dysku twardego z dysku maszyny wirtualnej.

### <a name="create-the-vm-on-the-azure-portal"></a>Tworzenie maszyny wirtualnej na Azure Portal

Wykonaj następujące kroki, aby utworzyć podstawowy obraz maszyny wirtualnej na [Azure Portal](https://ms.portal.azure.com/).

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com/).
2. Wybierz pozycję **Maszyny wirtualne**.
3. Wybierz pozycję **+ Dodaj** , aby otworzyć ekran **Utwórz maszynę wirtualną** .
4. Wybierz obraz z listy rozwijanej lub wybierz pozycję **Przeglądaj wszystkie obrazy publiczne i prywatne** , aby przeszukać lub przejrzeć wszystkie dostępne obrazy maszyn wirtualnych.
5. Aby utworzyć maszynę wirtualną **generacji 2** , przejdź do karty **Zaawansowane** i wybierz opcję **generacji 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Wybierz pozycję Gen 1 lub Gen 2.":::

6. Wybierz rozmiar maszyny wirtualnej do wdrożenia.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Wybierz pozycję Gen 1 lub Gen 2.":::

7. Podaj inne wymagane szczegóły, aby utworzyć maszynę wirtualną.
8. Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje. Gdy zostanie wyświetlony komunikat **weryfikacja przebiegła pomyślnie** , wybierz pozycję **Utwórz**.

Platforma Azure rozpocznie Inicjowanie obsługi określonej maszyny wirtualnej. Śledź postęp, wybierając kartę **Virtual Machines** w menu po lewej stronie. Po utworzeniu stanu zmian maszyny wirtualnej do **uruchomienia**.

### <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

Zapoznaj się z poniższą dokumentacją, aby nawiązać połączenie z maszyną wirtualną z [systemem Windows](../virtual-machines/windows/connect-logon.md) lub [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) .

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Następne kroki

- Zalecany następny krok: [Testowanie obrazu maszyny wirtualnej](azure-vm-image-test.md) w celu upewnienia się, że spełnia on wymagania dotyczące publikowania w witrynie Azure Marketplace. Jest on opcjonalny.
- Jeśli nie testujesz obrazu maszyny wirtualnej, Kontynuuj [generowanie identyfikatora URI sygnatury dostępu współdzielonego](azure-vm-get-sas-uri.md).
- Jeśli wystąpił problem podczas tworzenia nowego wirtualnego dysku twardego opartego na platformie Azure, zobacz [często zadawane pytania dotyczące maszyn wirtualnych dla witryny Azure Marketplace](azure-vm-create-faq.md).
