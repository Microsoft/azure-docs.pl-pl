---
title: Przewodnik dotyczący kontrolowania zachowania zamykania systemu Windows w Azure Lab Services | Microsoft Docs
description: Procedura automatycznego zamykania bezczynnej maszyny wirtualnej z systemem Windows i usuwania polecenia zamykania systemu Windows.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: c6021131787dde4fe23ec4caad107bda2e20158a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541564"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Przewodnik kontrolowania zachowania zamykania systemu Windows

Azure Lab Services zapewnia kilka kontroli kosztów, aby upewnić się, że maszyny wirtualne z systemem Windows nie działają nieoczekiwanie:
 - [Ustawianie harmonogramu](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Ustawianie przydziałów dla użytkowników](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Włączanie automatycznego zamykania po rozłączeniu](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Nawet z tymi kontrolami kosztów istnieją sytuacje, w których maszyna wirtualna z systemem Windows może być nieoczekiwanie nadal uruchamiana; w związku z tym należy odliczyć od przydziału studenta:

- **Okno RDP pozostaje otwarte**
  
    Gdy student nawiązuje połączenie z maszyną wirtualną przy użyciu protokołu RDP, może przypadkowo opuścić otwarte okno RDP.  Dopóki okno RDP pozostanie otwarte, ustawienie **automatyczne wyłączenie przy rozłączeniu** nie zacznie obowiązywać, ponieważ zostanie wyzwolone dopiero po rozłączeniu sesji RDP.

- **Polecenie zamykania systemu Windows służy do wyłączania maszyny wirtualnej**
  
    Student może użyć polecenia zamykania systemu Windows lub innych mechanizmów zamykania dostępnych w systemie Windows, aby wyłączyć maszynę wirtualną zamiast używać [Azure Lab Services przycisku Zatrzymaj](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  W takim przypadku, z perspektywy Azure Lab Services, maszyna wirtualna jest nadal używana.
    
W tym przewodniku przedstawiono kroki umożliwiające automatyczne zamknięcie bezczynnej maszyny wirtualnej z systemem Windows i usunięcie polecenia Windows Shutdown z menu **Start** .  

> [!NOTE]
> Maszyna wirtualna może również zostać nieoczekiwanie odliczona od przydziału, gdy student rozpocznie swoją maszynę wirtualną, ale nigdy nie łączy się z nim przy użyciu protokołu RDP.  Ten *przewodnik nie dotyczy obecnie tego* scenariusza.  Zamiast tego należy zwrócić uwagę na uczniów, aby natychmiast połączyć się z maszyną wirtualną przy użyciu protokołu RDP po jej uruchomieniu. lub należy zatrzymać maszynę wirtualną.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Usuń polecenie zamykania systemu Windows z menu Start

Ustawienia **lokalnego zasady grupy** systemu Windows umożliwiają również usunięcie polecenia shutdown z menu **Start** .

Aby usunąć polecenie zamknięcia, można nawiązać połączenie z maszyną wirtualną szablonu i wykonać poniższy skrypt programu PowerShell.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Lub można wykonać następujące czynności ręcznie przy użyciu szablonu maszyny wirtualnej:

1. Naciśnij klawisz Windows, wpisz **gpedit**, a następnie wybierz pozycję **Edytuj zasady grupy (Panel sterowania)**.

1. Przejdź do pozycji **Konfiguracja komputera > Szablony administracyjne > menu Start i pasek zadań**.  

    ![Edytor lokalnych zasad grupy](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Kliknij prawym przyciskiem myszy pozycję **Usuń i Zapobiegaj dostępowi do poleceń Zamknij, uruchom ponownie, Uśpij i Hibernuj**, a następnie kliknij pozycję **Edytuj**.

1. Wybierz ustawienie **włączone** , a następnie kliknij przycisk **OK**:
 
   ![Ustawienie zamykania](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Zauważ, że polecenie shutdown nie jest już wyświetlane w menu **Start** systemu Windows; pojawia się tylko polecenie **Disconnect** .

    ![Shutdown — polecenie](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z artykułem dotyczącym przygotowania maszyny wirtualnej z szablonem systemu Windows: [Przewodnik po konfigurowaniu komputera z szablonem systemu Windows w Azure Lab Services](how-to-prepare-windows-template.md)
