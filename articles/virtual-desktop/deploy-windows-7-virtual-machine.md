---
title: Wdrażanie pulpitu wirtualnego systemu Windows w systemie Windows 7 — Azure
description: Jak skonfigurować i wdrożyć maszynę wirtualną z systemem Windows 7 na pulpicie wirtualnym systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 65ef7af1247fdb38a7c85c1a8134257942f5ef5b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447033"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Wdrażanie maszyny wirtualnej z systemem Windows 7 w usłudze Windows Virtual Desktop

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

Proces wdrażania maszyny wirtualnej z systemem Windows 7 (VM) na pulpicie wirtualnym systemu Windows jest nieco inny niż w przypadku maszyn wirtualnych z nowszymi wersjami systemu Windows. W tym przewodniku przedstawiono sposób wdrażania systemu Windows 7.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem postępuj zgodnie z instrukcjami w temacie [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell.md) w celu utworzenia puli hostów. Jeśli używasz portalu, postępuj zgodnie z instrukcjami podanymi w krokach od 1 do 9, aby [utworzyć pulę hostów przy użyciu Azure Portal](create-host-pools-azure-marketplace.md). Następnie wybierz pozycję **Przegląd + Utwórz** , aby utworzyć pustą pulę hostów.

## <a name="configure-a-windows-7-virtual-machine"></a>Konfigurowanie maszyny wirtualnej z systemem Windows 7

Po spełnieniu wymagań wstępnych można skonfigurować MASZYNę wirtualną z systemem Windows 7 do wdrożenia na pulpicie wirtualnym systemu Windows.

Aby skonfigurować MASZYNę wirtualną z systemem Windows 7 na pulpicie wirtualnym systemu Windows:

1. Zaloguj się do Azure Portal i Wyszukaj obraz systemu Windows 7 Enterprise lub Przekaż własny obraz dostosowany do systemu Windows 7 Enterprise (x64).
2. Wdróż jedną lub wiele maszyn wirtualnych z systemem Windows 7 Enterprise jako system operacyjny hosta. Upewnij się, że maszyny wirtualne zezwalają na Remote Desktop Protocol (RDP) (port TCP/3389).
3. Połącz się z hostem systemu Windows 7 Enterprise przy użyciu protokołu RDP i Uwierzytelnij się z poświadczeniami zdefiniowanymi podczas konfigurowania wdrożenia.
4. Dodaj konto używane podczas nawiązywania połączenia z hostem przy użyciu protokołu RDP z grupą "Pulpit zdalny użytkownika". Jeśli nie dodasz konta, możesz nie być w stanie nawiązać połączenia z maszyną wirtualną po dołączeniu jej do domeny Active Directory.
5. Przejdź do Windows Update na maszynie wirtualnej.
6. Zainstaluj wszystkie aktualizacje systemu Windows w ważnej kategorii.
7. Zainstaluj wszystkie aktualizacje systemu Windows w opcjonalnej kategorii (z wyjątkiem pakietów językowych). Ten proces służy do instalowania Remote Desktop Protocol 8,0 Update ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)), które należy wykonać w celu wykonania tych instrukcji.
8. Otwórz Edytor lokalnych zasad grupy i przejdź do **konfiguracji komputera**  >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **usługi pulpitu zdalnego**  >    >  **środowisku sesji zdalnej** hosta sesji pulpit zdalny.
9. Włącz zasady Remote Desktop Protocol 8,0.
10. Dołącz tę maszynę wirtualną do domeny Active Directory.
11. Uruchom ponownie maszynę wirtualną, uruchamiając następujące polecenie:

     ```cmd
     shutdown /r /t 0
     ```

12. Postępuj zgodnie z instrukcjami znajdującymi się [tutaj](/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo) , aby uzyskać token rejestracji.

      - Jeśli wolisz używać Azure Portal, możesz również przejść do strony Przegląd puli hostów, do której chcesz dodać maszynę wirtualną, i utworzyć token w tym miejscu.

13. [Pobierz agenta pulpitu wirtualnego systemu Windows dla systemu Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Pobierz Menedżera agentów usług pulpitu wirtualnego systemu Windows dla systemu Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Otwórz instalatora agenta pulpitu wirtualnego systemu Windows i postępuj zgodnie z instrukcjami. Po wyświetleniu monitu podaj klucz rejestracji utworzony w kroku 12.
16. Otwórz Menedżera agentów usług pulpitu wirtualnego systemu Windows i postępuj zgodnie z instrukcjami.
17. Opcjonalnie można zablokować port TCP/3389, aby usunąć bezpośredni dostęp Remote Desktop Protocol do maszyny wirtualnej.
18. Opcjonalnie upewnij się, że program .NET Framework ma co najmniej wersję 4.7.2. Aktualizacja platformy jest szczególnie ważna w przypadku tworzenia obrazu niestandardowego.

## <a name="next-steps"></a>Następne kroki

Wdrożenie pulpitu wirtualnego systemu Windows jest teraz gotowe do użycia. [Pobierz najnowszą wersję klienta pulpitu wirtualnego systemu Windows](https://aka.ms/wvd/clients/windows) , aby rozpocząć pracę.

Aby uzyskać listę znanych problemów i instrukcje dotyczące rozwiązywania problemów dotyczących systemu Windows 7 na pulpicie wirtualnym systemu Windows, zobacz artykuł dotyczący rozwiązywania problemów w temacie [Rozwiązywanie problemów z maszynami wirtualnymi systemu Windows 7 w systemie Windows](./virtual-desktop-fall-2019/troubleshoot-windows-7-vm.md)
