---
title: Tworzenie oferty maszyny wirtualnej platformy Azure (VM) na podstawie zatwierdzonej podstawy, Azure Marketplace
description: Dowiedz się, jak utworzyć ofertę maszyny wirtualnej na podstawie zatwierdzonej podstawy.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 02/18/2021
ms.openlocfilehash: 7ed3f707ad69c60111bcc97e139c0825fd503d8e
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630115"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Jak utworzyć maszynę wirtualną przy użyciu zatwierdzonej podstawy

W tym artykule opisano sposób korzystania z platformy Azure w celu utworzenia maszyny wirtualnej zawierającej wstępnie skonfigurowany, zatwierdzonego systemu operacyjnego. Jeśli to nie jest zgodne z rozwiązaniem, można [utworzyć i skonfigurować lokalną maszynę wirtualną](azure-vm-create-using-own-image.md) przy użyciu zatwierdzonego systemu operacyjnego.

> [!NOTE]
> Przed rozpoczęciem tej procedury zapoznaj się z [wymaganiami technicznymi](marketplace-virtual-machines.md#technical-requirements) dotyczącymi ofert maszyn wirtualnych platformy Azure, łącznie z wymaganiami dotyczącymi wirtualnego dysku twardego (VHD).

## <a name="select-an-approved-base-image"></a>Wybieranie zatwierdzonego obrazu podstawowego

Wybierz jeden z następujących obrazów systemu Windows lub Linux jako podstawy.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

System Azure oferuje zakres zatwierdzonych dystrybucji systemu Linux. Aby zapoznać się z bieżącą listą, zobacz System [Linux dotyczący dystrybucji zatwierdzony przez platformę Azure](../virtual-machines/linux/endorsed-distros.md).

## <a name="create-vm-on-the-azure-portal"></a>Tworzenie maszyny wirtualnej na Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com/).
2. Wybierz pozycję **Maszyny wirtualne**.
3. Wybierz pozycję **+ Dodaj** , aby otworzyć ekran **Utwórz maszynę wirtualną** .
4. Wybierz obraz z listy rozwijanej lub wybierz pozycję **Przeglądaj wszystkie obrazy publiczne i prywatne** , aby przeszukać lub przejrzeć wszystkie dostępne obrazy maszyn wirtualnych.
5. Aby utworzyć maszynę wirtualną **generacji 2** , przejdź do karty **Zaawansowane** i wybierz opcję **generacji 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Wybierz pozycję Gen 1 lub Gen 2.":::

6. Wybierz rozmiar maszyny wirtualnej do wdrożenia.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Wybierz zalecany rozmiar maszyny wirtualnej dla wybranego obrazu.":::

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

- Zalecany następny krok: [Testowanie obrazu maszyny wirtualnej](azure-vm-image-test.md) w celu upewnienia się, że spełnia on wymagania dotyczące publikowania w witrynie Azure Marketplace. Jest to opcjonalne.
- Jeśli nie chcesz testować obrazu maszyny wirtualnej, zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/) , aby opublikować obraz.
- Jeśli wystąpił problem podczas tworzenia nowego wirtualnego dysku twardego opartego na platformie Azure, zobacz [często zadawane pytania dotyczące maszyn wirtualnych dla witryny Azure Marketplace](azure-vm-create-faq.md).