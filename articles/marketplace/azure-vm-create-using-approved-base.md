---
title: Tworzenie oferty maszyny wirtualnej platformy Azure na podstawie zatwierdzonej bazy Azure Marketplace
description: Dowiedz się, jak utworzyć ofertę maszyny wirtualnej na podstawie zatwierdzonej bazy.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 19ae4b929964aaeb971bef75a2a620e40e4667f5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727172"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Jak utworzyć maszynę wirtualną przy użyciu zatwierdzonej bazy

W tym artykule opisano sposób użycia platformy Azure do utworzenia maszyny wirtualnej zawierającej wstępnie skonfigurowany, zatwierdzony system operacyjny. Jeśli nie jest to zgodne z Twoim rozwiązaniem, możesz utworzyć i skonfigurować lokalną maszynę wirtualną przy użyciu zatwierdzonego systemu operacyjnego. [](azure-vm-create-using-own-image.md)

> [!NOTE]
> Przed rozpoczęciem tej procedury [](marketplace-virtual-machines.md#technical-requirements) przejrzyj wymagania techniczne dotyczące ofert maszyn wirtualnych platformy Azure, w tym wymagania dotyczące wirtualnego dysku twardego (VHD).

## <a name="select-an-approved-base-image"></a>Wybieranie zatwierdzonego obrazu podstawowego

Wybierz jeden z następujących obrazów systemu Windows lub Linux jako bazę.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview) [2014,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview) [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Platforma Azure oferuje szereg zatwierdzonych dystrybucji systemu Linux. Aby uzyskać bieżącą listę, zobacz [Linux on distributions endorsed by Azure (System Linux w dystrybucjach zatwierdzonych przez platformę Azure).](../virtual-machines/linux/endorsed-distros.md)

## <a name="create-vm-on-the-azure-portal"></a>Tworzenie maszyny wirtualnej na Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com/).
2. Wybierz pozycję **Maszyny wirtualne**.
3. Wybierz **pozycję + Dodaj,** aby otworzyć **ekran Tworzenie maszyny wirtualnej.**
4. Wybierz obraz z listy rozwijanej lub wybierz pozycję **Przeglądaj** wszystkie obrazy publiczne i prywatne, aby wyszukać lub przejrzeć wszystkie dostępne obrazy maszyn wirtualnych.
5. Aby utworzyć **maszynę wirtualną 2.** generacji, przejdź do karty **Zaawansowane** i wybierz opcję **Gen 2.**

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Wybierz pozycję Gen 1 lub Gen 2.":::

6. Wybierz rozmiar maszyny wirtualnej do wdrożenia.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Wybierz zalecany rozmiar maszyny wirtualnej dla wybranego obrazu.":::

7. Podaj inne szczegóły wymagane do utworzenia maszyny wirtualnej.
8. Wybierz **pozycję Przejrzyj i utwórz,** aby przejrzeć wybrane opcje. Gdy pojawi **się komunikat Weryfikacja przekazana,** wybierz pozycję  **Utwórz**.

Platforma Azure rozpoczyna aprowizowanie określonej maszyny wirtualnej. Śledź postęp, wybierając **kartę Virtual Machines** w menu po lewej stronie. Po utworzeniu stan maszyny wirtualnej zmieni się na **Uruchomiona.**

## <a name="configure-the-vm"></a>Konfigurowanie maszyny wirtualnej

W tej sekcji opisano, jak rozmiarować, aktualizować i uogólniać maszynę wirtualną platformy Azure. Te kroki są niezbędne do przygotowania maszyny wirtualnej do wdrożenia na Azure Marketplace.

### <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

Zapoznaj się z następującą dokumentacją, aby nawiązać połączenie z maszyną wirtualną [z systemem Windows](../virtual-machines/windows/connect-logon.md) lub [Linux.](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm)

### <a name="install-the-most-current-updates"></a>Instalowanie najbardziej bieżących aktualizacji

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Przeprowadzanie dodatkowych kontroli zabezpieczeń

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Wykonywanie konfiguracji niestandardowej i zaplanowanych zadań

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Następne kroki

- Zalecany następny krok: [Przetestuj obraz maszyny wirtualnej,](azure-vm-image-test.md) aby upewnić się, że Azure Marketplace wymagania dotyczące publikowania. Jest to opcjonalne.
- Jeśli nie chcesz testować obrazu maszyny wirtualnej, zaloguj się do [Partner Center,](https://partner.microsoft.com/) aby opublikować obraz.
- Jeśli napotkano trudności podczas tworzenia nowego wirtualnego dysku twardego opartego na platformie Azure, zobacz Często zadawane pytania dotyczące maszyn [wirtualnych Azure Marketplace](azure-vm-create-faq.md).
