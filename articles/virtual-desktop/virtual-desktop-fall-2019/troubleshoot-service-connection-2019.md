---
title: Rozwiązywanie problemów z połączeniem usługi Windows Virtual Desktop 2019 — Azure
description: Jak rozwiązywać problemy podczas konfigurowania połączeń klienta w środowisku dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad5e740a2874d398f3947fe755024bedc897ec73
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085796"
---
# <a name="troubleshoot-windows-virtual-desktop-service-connections"></a>Rozwiązywanie problemów z połączeniami usługi pulpitu wirtualnego systemu Windows

>[!IMPORTANT]
>Ta zawartość dotyczy wersji 2019, która nie Azure Resource Manager obsługuje obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektami pulpitu wirtualnego systemu Windows wprowadzonymi w ramach aktualizacji wiosną 2020, zobacz [ten artykuł](../troubleshoot-service-connection.md).

Ten artykuł służy do rozwiązywania problemów z połączeniami klienta pulpitu wirtualnego systemu Windows.

## <a name="provide-feedback"></a>Wyraź opinię

Możesz przekazać nam swoją opinię i omówić usługę pulpitów wirtualnych systemu Windows z zespołem produktu i innymi członkami społeczności w [systemie Windows — społeczność Tech](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Użytkownik nawiązuje połączenie, ale nic nie jest wyświetlane (brak źródła danych)

Użytkownik może uruchomić Pulpit zdalny klientów i może się uwierzytelnić, jednak użytkownik nie zobaczy żadnych ikon w źródle odnajdywania sieci Web.

Upewnij się, że użytkownik zgłaszał problemy, został przypisany do grup aplikacji przy użyciu tego wiersza polecenia:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Upewnij się, że użytkownik loguje się przy użyciu poprawnych poświadczeń.

Jeśli klient sieci Web jest używany, upewnij się, że nie występują problemy z buforowanymi poświadczeniami.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview-2019.md).
- Aby rozwiązać problemy podczas tworzenia dzierżawy i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues-2019.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration-2019.md).
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
