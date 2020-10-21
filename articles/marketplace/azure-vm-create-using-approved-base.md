---
title: Tworzenie oferty maszyny wirtualnej platformy Azure (VM) na podstawie zatwierdzonej podstawy, Azure Marketplace
description: Dowiedz się, jak utworzyć ofertę maszyny wirtualnej na podstawie zatwierdzonej podstawy.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 34d64d70c657712bb44aa6331b53f014349a0dda
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284826"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Jak utworzyć maszynę wirtualną przy użyciu zatwierdzonej podstawy

W tym artykule opisano sposób korzystania z platformy Azure w celu utworzenia maszyny wirtualnej zawierającej wstępnie skonfigurowany, zatwierdzonego systemu operacyjnego. Jeśli to nie jest zgodne z rozwiązaniem, można [utworzyć i skonfigurować lokalną maszynę wirtualną](azure-vm-create-using-own-image.md) przy użyciu zatwierdzonego systemu operacyjnego, a następnie skonfigurować i przygotować ją do przekazania zgodnie z opisem w artykule [Przygotowywanie wirtualnego dysku twardego systemu Windows lub VHDX do przekazania do platformy Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!NOTE]
> Przed rozpoczęciem tej procedury zapoznaj się z [wymaganiami technicznymi](marketplace-virtual-machines.md#technical-requirements) dotyczącymi ofert maszyn wirtualnych platformy Azure, łącznie z wymaganiami dotyczącymi wirtualnego dysku twardego (VHD).

## <a name="select-an-approved-base-image"></a>Wybieranie zatwierdzonego obrazu podstawowego

Wybierz jeden z następujących obrazów systemu Windows lub Linux jako podstawy.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

System Azure oferuje zakres zatwierdzonych dystrybucji systemu Linux. Aby zapoznać się z bieżącą listą, zobacz System [Linux dotyczący dystrybucji zatwierdzony przez platformę Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

## <a name="create-vm-on-the-azure-portal"></a>Tworzenie maszyny wirtualnej na Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com/).
2. Wybierz pozycję **Maszyny wirtualne**.
3. Wybierz pozycję **+ Dodaj** , aby otworzyć ekran **Utwórz maszynę wirtualną** .
4. Wybierz obraz z listy rozwijanej lub wybierz pozycję **Przeglądaj wszystkie obrazy publiczne i prywatne** , aby przeszukać lub przejrzeć wszystkie dostępne obrazy maszyn wirtualnych.
5. Aby utworzyć maszynę wirtualną **generacji 2** , przejdź do karty **Zaawansowane** i wybierz opcję **generacji 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Wybierz pozycję Gen 1 lub Gen 2.":::

6. Wybierz rozmiar maszyny wirtualnej do wdrożenia.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Wybierz pozycję Gen 1 lub Gen 2.":::

7. Podaj inne wymagane szczegóły, aby utworzyć maszynę wirtualną.
8. Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje. Gdy zostanie wyświetlony komunikat **weryfikacja przebiegła pomyślnie** , wybierz pozycję  **Utwórz**.

Platforma Azure rozpocznie Inicjowanie obsługi określonej maszyny wirtualnej. Śledź postęp, wybierając kartę **Virtual Machines** w menu po lewej stronie. Po jego utworzeniu stan maszyny wirtualnej zmieni się na **uruchomiony**.


## <a name="configure-the-vm"></a>Konfigurowanie maszyny wirtualnej

W tej sekcji opisano, jak zmienić rozmiar, zaktualizować i uogólnić maszynę wirtualną platformy Azure. Te kroki są niezbędne, aby przygotować maszynę wirtualną do wdrożenia w portalu Azure Marketplace.

### <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

Zapoznaj się z poniższą dokumentacją, aby nawiązać połączenie z maszyną wirtualną z [systemem Windows](../virtual-machines/windows/connect-logon.md) lub [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) .

### <a name="install-the-most-current-updates"></a>Zainstaluj najnowsze aktualizacje

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Wykonaj dodatkowe sprawdzenia zabezpieczeń

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Wykonywanie konfiguracji niestandardowej i zaplanowanych zadań

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Następne kroki

- Zalecany następny krok: [Testowanie obrazu maszyny wirtualnej](azure-vm-image-test.md) w celu upewnienia się, że spełnia on wymagania dotyczące publikowania w witrynie Azure Marketplace. Jest on opcjonalny.
- Jeśli nie testujesz obrazu maszyny wirtualnej, Kontynuuj [generowanie identyfikatora URI sygnatury dostępu współdzielonego](azure-vm-get-sas-uri.md).
- Jeśli wystąpił problem podczas tworzenia nowego wirtualnego dysku twardego opartego na platformie Azure, zobacz [często zadawane pytania dotyczące maszyn wirtualnych dla witryny Azure Marketplace](azure-vm-create-faq.md).
