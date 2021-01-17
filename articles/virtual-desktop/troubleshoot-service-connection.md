---
title: Rozwiązywanie problemów z połączeniem usługi Windows Virtual Desktop — Azure
description: Jak rozwiązywać problemy podczas konfigurowania połączeń usług w środowisku dzierżawy usług pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 42502864cfed177adfe487e9c59247579628fec8
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539078"
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

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>Użytkownik utraci istniejące źródło danych i nie jest wyświetlany żaden zasób zdalny (brak kanału informacyjnego)

Ten błąd jest zwykle wyświetlany po przeniesieniu przez użytkownika subskrypcji z jednej dzierżawy usługi Azure AD do innej. W związku z tym usługa utraci śledzenie swoich przypisań użytkowników, ponieważ są one nadal powiązane z poprzednią dzierżawą usługi Azure AD.

Aby rozwiązać ten problem, wystarczy zmienić przypisanie użytkowników do grup aplikacji.

Przyczyną może być również, że dostawca CSP utworzył subskrypcję, a następnie przeniósł ją do klienta. Aby rozwiązać ten problem, należy ponownie zarejestrować dostawcę zasobów.

1. Zaloguj się do witryny Azure Portal.
2. Przejdź do **subskrypcji**, a następnie wybierz swoją subskrypcję.
3. W menu po lewej stronie strony wybierz pozycję **dostawca zasobów**.
4. Znajdź i wybierz pozycję **Microsoft. DesktopVirtualization**, a następnie wybierz pozycję **zarejestruj ponownie**.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia środowiska pulpitu wirtualnego systemu Windows i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli środowiska i hosta](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Rozwiązywanie problemów związanych z agentem pulpitu wirtualnego systemu Windows lub łącznością sesji można znaleźć w temacie [Rozwiązywanie typowych problemów z systemem Windows Virtual Desktop Agent](troubleshoot-agent.md).
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
