---
title: Przewodnik dotyczący kontrolowania zachowania zamykania systemu Windows w Azure Lab Services | Microsoft Docs
description: Procedura automatycznego zamykania bezczynnej maszyny wirtualnej z systemem Windows i usuwania polecenia zamykania systemu Windows.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3c20bc2bb79faf53c4f3fbd113c18c5c6d923e59
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334025"
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

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Automatyczne rozłączanie i zamykanie protokołu RDP dla bezczynnej maszyny wirtualnej

System Windows udostępnia ustawienia **lokalnego zasady grupy** , których można użyć do ustawienia limitu czasu automatycznego rozłączenia sesji RDP, gdy stanie się ona bezczynna.  Sesja jest określana jako bezczynna, gdy *nie* ma żadnych danych wejściowych mouse\keyboard.  Wszystkie długotrwałe działania, które nie obejmują danych wejściowych mouse\keyboard, powodują, że maszyna wirtualna jest w stanie bezczynności.  Obejmuje to wykonywanie długich zapytań, przesyłania strumieniowego wideo, kompilowania itp.  W zależności od potrzeb klasy można ustawić limit czasu bezczynności, tak aby był wystarczająco długi, aby obsługiwał te typy działań.  Na przykład w razie potrzeby można ustawić limit czasu bezczynności na 1 lub więcej godzin.

Oto doświadczenie ucznia podczas konfigurowania **limitu bezczynności sesji** w połączeniu z ustawieniem [**automatyczne zamykanie przy rozłączaniu**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) :
 1. Student nawiązuje połączenie z maszyną wirtualną z systemem Windows przy użyciu protokołu RDP.
 2. Gdy student opuści okno protokołu RDP, a maszyna wirtualna jest w stanie bezczynności w określonym **limicie czasu bezczynności** (na przykład 5 minut), Student zobaczy następujące okno dialogowe:

    ![Okno dialogowe limit czasu bezczynności](./media/how-to-windows-shutdown/idle-time-expired.png)

1. Jeśli student *nie kliknie przycisku* **OK**, sesja RDP zostanie automatycznie rozłączona po upływie 2 minut.
2. Po rozłączeniu sesji RDP, gdy zostanie osiągnięty określony przedział czasu dla ustawienia **automatycznego zamknięcia przy rozłączeniu** , maszyna wirtualna zostanie automatycznie zamknięta przez Azure Lab Services.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Ustaw limit czasu bezczynności sesji RDP na maszynie wirtualnej szablonu

Aby ustawić limit czasu bezczynności sesji RDP, można nawiązać połączenie z szablonową maszyną wirtualną i wykonać poniższy skrypt programu PowerShell.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Lub można wykonać następujące czynności ręcznie przy użyciu szablonu maszyny wirtualnej:

1. Naciśnij klawisz Windows, wpisz **gpedit**, a następnie wybierz pozycję **Edytuj zasady grupy (Panel sterowania)**.

1. Przejdź do pozycji **Konfiguracja komputera > Szablony administracyjne > składniki systemu Windows > usługi pulpitu zdalnego > pulpit zdalny Host sesji > limity czasu sesji**.  

    ![Zrzut ekranu pokazujący "Edytor lokalnych zasad grupy" z wybraną opcją "limity czasu sesji".](./media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Kliknij prawym przyciskiem myszy pozycję **Ustaw limit czasu dla aktywnych, ale bezczynnych sesji usługi pulpitu zdalnego**, a następnie kliknij pozycję **Edytuj**.

1. Wprowadź poniższe ustawienia, a następnie kliknij przycisk **OK**:
   1. Wybierz pozycję **Włączone**.
   1. W obszarze **Opcje**Określ **Limit bezczynności sesji**.

    ![Limit bezczynnych sesji](./media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Na koniec aby połączyć to zachowanie z ustawieniem **automatycznego zamykania przy rozłączaniu** , należy wykonać czynności opisane w artykule How to: [enable Automatic Shutdown](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)Machines with Disconnect.

> [!WARNING]
> Po skonfigurowaniu tego ustawienia przy użyciu programu PowerShell do modyfikacji ustawienia rejestru bezpośrednio lub ręcznie przy użyciu edytora zasady grupy należy najpierw ponownie uruchomić maszynę wirtualną, aby ustawienia zaczęły obowiązywać.  Ponadto w przypadku skonfigurowania ustawienia przy użyciu rejestru Edytor zasady grupy nie zawsze jest odświeżany w celu odzwierciedlenia zmian w ustawieniach rejestru. Jednak ustawienie rejestru nadal działa zgodnie z oczekiwaniami i zostanie wyświetlona sesja RDP, gdy zostanie ona przełączona przez określony czas.

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