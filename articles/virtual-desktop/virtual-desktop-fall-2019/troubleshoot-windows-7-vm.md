---
title: Maszyny wirtualne z systemem Windows 7 Windows Virtual Desktop (klasyczne) — Azure
description: Jak rozwiązywać problemy dotyczące maszyn wirtualnych z systemem Windows 7 w środowisku klasycznym systemu Windows (klasycznego).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 99c1c76092714c58fd13d9ce51ef850971d80c86
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444126"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop-classic"></a>Rozwiązywanie problemów z maszynami wirtualnymi systemu Windows 7 w systemie Windows Virtual Desktop (klasyczny)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows.

Ten artykuł służy do rozwiązywania problemów występujących podczas konfigurowania maszyn wirtualnych hosta sesji usług pulpitu wirtualnego systemu Windows.

## <a name="known-issues"></a>Znane problemy

System Windows 7 na pulpitach wirtualnych systemu Windows nie obsługuje następujących funkcji:

- Zwirtualizowane aplikacje (RemoteApp)
- Przekierowanie strefy czasowej
- Automatyczne skalowanie DPI

Pulpit wirtualny systemu Windows umożliwia wirtualizację pełnych pulpitów dla systemu Windows 7.

Chociaż automatyczne skalowanie DPI nie jest obsługiwane, można ręcznie zmienić rozdzielczość na maszynie wirtualnej, klikając prawym przyciskiem myszy ikonę w Pulpit zdalny klienta i wybierając pozycję **rozdzielczość**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Błąd: nie można uzyskać dostępu do grupy użytkowników Pulpit zdalny

Jeśli pulpit wirtualny systemu Windows nie może odnaleźć użytkownika lub poświadczeń użytkowników w Pulpit zdalny grupie użytkowników, może zostać wyświetlony jeden z następujących komunikatów o błędach:

- "Ten użytkownik nie jest członkiem grupy użytkowników Pulpit zdalny"
- "Musisz mieć przyznane uprawnienia, aby zalogować się za Usługi pulpitu zdalnego"

Aby naprawić ten błąd, Dodaj użytkownika do grupy użytkowników Pulpit zdalny:

1. Otwórz witrynę Azure Portal.
2. Wybierz maszynę wirtualną, na której zostanie wyświetlony komunikat o błędzie.
3. Wybierz pozycję **Uruchom polecenie**.
4. Uruchom następujące polecenie z `<username>` zamienionym nazwą użytkownika, który chcesz dodać:

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```