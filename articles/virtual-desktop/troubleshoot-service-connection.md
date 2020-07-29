---
title: Rozwiązywanie problemów z połączeniem usługi Windows Virtual Desktop — Azure
description: Jak rozwiązywać problemy podczas konfigurowania połączeń klienta w środowisku dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 875f39653c5240e3a1b571b531eb2bb08c4811d0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289847"
---
# <a name="windows-virtual-desktop-service-connections"></a>Połączenia usługi pulpitu wirtualnego systemu Windows

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).

Ten artykuł służy do rozwiązywania problemów z połączeniami klienta pulpitu wirtualnego systemu Windows.

## <a name="provide-feedback"></a>Wyraź opinię

Możesz przekazać nam swoją opinię i omówić usługę pulpitów wirtualnych systemu Windows z zespołem produktu i innymi członkami społeczności w [systemie Windows — społeczność Tech](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Użytkownik nawiązuje połączenie, ale nic nie jest wyświetlane (brak źródła danych)

Użytkownik może uruchomić Pulpit zdalny klientów i może się uwierzytelnić, jednak użytkownik nie zobaczy żadnych ikon w źródle odnajdywania sieci Web.

1. Upewnij się, że użytkownik zgłaszał problemy, został przypisany do grup aplikacji przy użyciu tego wiersza polecenia:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Upewnij się, że użytkownik loguje się przy użyciu poprawnych poświadczeń.

3. Jeśli klient sieci Web jest używany, upewnij się, że nie występują problemy z buforowanymi poświadczeniami.

4. Jeśli użytkownik jest częścią grupy użytkowników programu Azure Active Directory (AD), upewnij się, że grupa użytkowników jest grupą zabezpieczeń, a nie z grupą dystrybucyjną. Pulpit wirtualny systemu Windows nie obsługuje grup dystrybucyjnych usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia środowiska pulpitu wirtualnego systemu Windows i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli środowiska i hosta](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
