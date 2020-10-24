---
title: Migrowanie ręczne z pulpitu wirtualnego systemu Windows (klasyczny) — Azure
description: Jak przeprowadzić migrację ręcznie z pulpitu wirtualnego systemu Windows (klasycznego) do pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd292fa6b4f613e0a5f5a80e0cd87675f529baf5
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516175"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Ręczne Migrowanie z pulpitu wirtualnego systemu Windows (klasyczny)

Pulpit wirtualny systemu Windows (klasyczny) tworzy środowisko usługi za pomocą poleceń cmdlet programu PowerShell, interfejsów API REST i obiektów usługi. "Obiekt" w środowisku usługi pulpitu wirtualnego systemu Windows to element, który tworzy pulpit wirtualny systemu Windows. Obiekty usługi obejmują dzierżawców, pule hostów, grupy aplikacji i hosty sesji.

Jednak pulpit wirtualny systemu Windows (klasyczny) nie jest zintegrowany z platformą Azure. Bez integracji z platformą Azure utworzone obiekty nie są automatycznie zarządzane przez Azure Portal, ponieważ nie są połączone z subskrypcją platformy Azure.

Najnowsza ważna Aktualizacja pulpitu wirtualnego systemu Windows oznacza zmianę w usłudze na pełną integrację z platformą Azure. Obiekty tworzone w programie Virtual Desktop systemu Windows są automatycznie zarządzane przez Azure Portal.

W tym artykule wyjaśnimy, dlaczego należy rozważyć Migrowanie do najnowszej wersji pulpitu wirtualnego systemu Windows. Następnie poinformujemy, jak ręcznie migrować z pulpitu wirtualnego systemu Windows (klasyczny) do najnowszej aktualizacji pulpitu wirtualnego systemu Windows.

## <a name="why-migrate"></a>Dlaczego warto przeprowadzić migrację?

Ważne aktualizacje mogą być niewygodne, szczególnie w przypadku konieczności ręcznego wykonywania tych czynności. Istnieje jednak kilka powodów, dla których nie można przeprowadzić automatycznej migracji:

- Istniejące obiekty usługi utworzone w klasycznej wersji nie mają żadnej reprezentacji na platformie Azure. Ich zakres nie wykracza poza usługę pulpitu wirtualnego systemu Windows.
- W przypadku najnowszej aktualizacji identyfikator aplikacji usługi został zmieniony, aby można było usunąć zgodę na aplikacje w taki sposób, jak w przypadku pulpitu wirtualnego systemu Windows (klasycznego). Nie będzie można tworzyć nowych obiektów platformy Azure przy użyciu pulpitu wirtualnego systemu Windows, chyba że zostaną uwierzytelnione przy użyciu nowego identyfikatora aplikacji.

Pomimo problemów, Migrowanie z wersji klasycznej jest nadal ważne. Oto co można zrobić po przeprowadzeniu migracji:

- Zarządzanie pulpitem wirtualnym systemu Windows za pomocą Azure Portal.
- Przypisywanie grup użytkowników Azure Active Directory (AD) do grup aplikacji.
- Aby rozwiązać problem z wdrożeniem, użyj ulepszonej funkcji Log Analytics.
- Użyj natywnej kontroli dostępu opartej na rolach (Azure RBAC) do zarządzania dostępem administracyjnym.

## <a name="when-should-i-migrate"></a>Kiedy należy przeprowadzić migrację?

W przypadku konieczności przeprowadzenia migracji należy wziąć pod uwagę bieżące i przyszłe sytuacje wdrożenia.

W szczególności istnieje kilka scenariuszy, w których zalecamy ręczne Migrowanie:

- Istnieje konfiguracja puli hostów testowych z niewielką liczbą użytkowników.
- Dostępna jest Konfiguracja puli hostów produkcyjnych z niewielką liczbą użytkowników, ale planuje się ostatecznie uzyskać do setek użytkowników.
- Istnieje prosta konfiguracja, która może być łatwo replikowana. Na przykład jeśli maszyny wirtualne korzystają z obrazu galerii.

> [!IMPORTANT]
> Jeśli używasz zaawansowanej konfiguracji wymagającej długotrwałej lub dużej liczby użytkowników, nie zalecamy ręcznej migracji.

## <a name="prepare-for-migration"></a>Przygotowanie do migracji

Przed rozpoczęciem należy upewnić się, że środowisko jest gotowe do migracji.

Oto co należy zrobić, aby rozpocząć proces migracji:

- Subskrypcja platformy Azure, w której utworzysz nowe obiekty usługi platformy Azure.
- Upewnij się, że masz przypisane następujące role:
    
    - Współautor
    - Administrator dostępu użytkowników
    
    Rola współautor umożliwia tworzenie obiektów platformy Azure w ramach subskrypcji, a rola Administrator dostępu użytkowników umożliwia przypisywanie użytkowników do grup aplikacji.

## <a name="how-to-migrate-manually"></a>Jak przeprowadzić migrację ręcznie

Teraz, gdy przygotowano do procesu migracji, czas na faktyczne Migrowanie.

Aby przeprowadzić migrację ręcznie z pulpitu wirtualnego systemu Windows (klasycznego) do pulpitu wirtualnego systemu Windows:

1. Postępuj zgodnie z instrukcjami w temacie [Tworzenie puli hostów przy użyciu Azure Portal,](create-host-pools-azure-marketplace.md) aby utworzyć wszystkie obiekty wysokiego poziomu z Azure Portal.
2. Aby przenieść maszyny wirtualne, których już używasz, postępuj zgodnie z instrukcjami w temacie [Rejestrowanie maszyn wirtualnych w puli hostów usług pulpitu wirtualnego systemu Windows](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) w celu ręcznego zarejestrowania ich w nowej puli hostów utworzonej w kroku 1.
3. Utwórz nowe grupy aplikacji usługi RemoteApp.
4. Publikowanie użytkowników lub grup użytkowników w nowych grupach aplikacji pulpitu i usługi RemoteApp.
5. Zaktualizuj zasady dostępu warunkowego, aby zezwolić na nowe obiekty, postępując zgodnie z instrukcjami w temacie [Konfigurowanie uwierzytelniania wieloskładnikowego](set-up-mfa.md).

Aby zapobiec przestojom, należy najpierw zarejestrować istniejące hosty sesji do pul hostów zintegrowanych Azure Resource Manager w małych grupach w danym momencie. Następnie wolno przełączeć użytkowników do nowych grup aplikacji zintegrowanych z Azure Resource Manager.

## <a name="next-steps"></a>Następne kroki

Po przeprowadzeniu migracji Sprawdź, jak działa pulpit wirtualny systemu Windows, sprawdzając [nasze samouczki](create-host-pools-azure-marketplace.md). Zapoznaj się z zaawansowanymi możliwościami zarządzania w obszarze [rozszerzanie istniejącej puli hostów](expand-existing-host-pool.md) i [Dostosowywanie właściwości protokołu RDP](customize-rdp-properties.md).

Aby dowiedzieć się więcej na temat obiektów usługi, sprawdź [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
