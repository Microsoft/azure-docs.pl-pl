---
title: Konsola szeregowa platformy Azure dla systemu Windows | Microsoft Docs
description: Bi-Directional konsoli szeregowej dla Virtual Machines platformy Azure i Virtual Machine Scale Sets przy użyciu przykładu systemu Windows.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 9cf3f9a1cd933526c5e376d232fa5acbc97fad47
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969725"
---
# <a name="azure-serial-console-for-windows"></a>Konsola szeregowa platformy Azure dla systemu Windows

Konsola szeregowa w Azure Portal zapewnia dostęp do konsoli opartej na tekście dla maszyn wirtualnych z systemem Windows i wystąpień zestawów skalowania maszyn wirtualnych. To połączenie szeregowe łączy się z portem seryjnym COM1 maszyny wirtualnej lub wystąpienia zestawu skalowania maszyn wirtualnych, zapewniając dostęp do niego niezależnie od stanu sieci lub systemu operacyjnego. Dostęp do konsoli szeregowej można uzyskać tylko przy użyciu Azure Portal i jest to dozwolone tylko dla tych użytkowników, którzy mają rolę dostępu współautora lub wyższą dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.

Konsola szeregowa działa w taki sam sposób w przypadku maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych. W tym dokumencie wszystkie wzmianki dotyczące maszyn wirtualnych będą niejawnie obejmować wystąpienia zestawu skalowania maszyn wirtualnych, chyba że określono inaczej.

Konsola szeregowa jest ogólnie dostępna w globalnych regionach platformy Azure i w publicznej wersji zapoznawczej w Azure Government. Nie jest jeszcze dostępna w chmurze platformy Azure w Chinach.

Aby uzyskać dokumentację konsoli szeregowej dla systemu Linux, zobacz [Azure serial Console for Linux](serial-console-linux.md).

> [!NOTE]
> Konsola szeregowa jest obecnie niezgodna z zarządzanym kontem magazynu diagnostyki rozruchu. Aby korzystać z konsoli szeregowej, upewnij się, że korzystasz z niestandardowego konta magazynu.


## <a name="prerequisites"></a>Wymagania wstępne

* W maszynie wirtualnej lub wystąpieniu zestawu skalowania maszyn wirtualnych musi być używany model wdrażania zarządzania zasobami. Wdrożenia klasyczne nie są obsługiwane.

- Twoje konto używające konsoli szeregowej musi mieć [rolę współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) dla maszyny wirtualnej i konta magazynu [diagnostyki rozruchu](boot-diagnostics.md) .

- Maszyna wirtualna lub wystąpienie zestawu skalowania maszyn wirtualnych muszą mieć użytkownika opartego na hasłach. Można go utworzyć za pomocą funkcji [resetowania hasła](../extensions/vmaccess.md#reset-password) rozszerzenia dostępu do maszyny wirtualnej. Wybierz pozycję **zresetuj hasło** w sekcji **Pomoc techniczna i rozwiązywanie problemów** .

* Maszyna wirtualna dla wystąpienia zestawu skalowania maszyn wirtualnych musi mieć włączoną [diagnostykę rozruchu](boot-diagnostics.md) .

    ![Ustawienia diagnostyki rozruchu](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Włącz funkcje konsoli szeregowej dla systemu Windows Server

> [!NOTE]
> Jeśli nie widzisz niczego w konsoli szeregowej, upewnij się, że Diagnostyka rozruchu jest włączona na maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Włączanie konsoli szeregowej w obrazach niestandardowych lub starszych
Nowsze obrazy systemu Windows Server na platformie Azure mają domyślnie włączoną [specjalną konsolę administracyjną](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) . Konsola SAC jest obsługiwana w wersjach systemu Windows Server, ale nie jest dostępna w wersjach klienta (na przykład Windows 10, Windows 8 lub Windows 7).

W przypadku starszych obrazów systemu Windows Server (utworzonych przed 2018 lutego) można automatycznie włączyć konsolę szeregową za pomocą funkcji polecenia uruchamiania Azure Portal. W Azure Portal wybierz pozycję **Uruchom polecenie**, a następnie wybierz polecenie o nazwie **EnableEMS** z listy.

![Uruchom listę poleceń](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Alternatywnie, aby ręcznie włączyć konsolę szeregową dla maszyn wirtualnych z systemem Windows/zestawu skalowania maszyn wirtualnych utworzonych przed luty 2018, wykonaj następujące kroki:

1. Nawiązywanie połączenia z maszyną wirtualną z systemem Windows przy użyciu Pulpit zdalny
1. W wierszu polecenia z uprawnieniami administracyjnymi Uruchom następujące polecenia:
    - `bcdedit /ems {current} on`lub `bcdedit /ems '{current}' on` Jeśli używasz programu PowerShell
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Aby włączyć konsolę SAC, należy ponownie uruchomić system.

    ![Konsola SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

W razie konieczności można również włączyć funkcję SAC w trybie offline:

1. Dołącz dysk z systemem Windows, dla którego chcesz, aby konsola SAC była skonfigurowana jako dysk z danymi istniejącej maszyny wirtualnej.

1. W wierszu polecenia z uprawnieniami administracyjnymi Uruchom następujące polecenia:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Jak mogę sprawdzić, czy konsola SAC jest włączona?

Jeśli [konsola SAC](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) nie jest włączona, konsola szeregowa nie będzie wyświetlać monitu dotyczącego konsoli SAC. W niektórych przypadkach są wyświetlane informacje o kondycji maszyny wirtualnej, a w innych przypadkach są puste. Jeśli używasz obrazu systemu Windows Server utworzonego przed lutym 2018, prawdopodobnie nie zostanie włączona konsola SAC.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Włączanie menu rozruchu systemu Windows w konsoli szeregowej

Jeśli konieczne jest włączenie wyświetlanych w konsoli szeregowej wierszy modułu ładującego rozruchu systemu Windows, można dodać do danych konfiguracji rozruchu następujące dodatkowe opcje. Aby uzyskać więcej informacji, zobacz [bcdedit](/windows-hardware/drivers/devtest/bcdedit--set).

1. Połącz się z maszyną wirtualną z systemem Windows lub wystąpieniem zestawu skalowania maszyn wirtualnych za pomocą Pulpit zdalny.

1. W wierszu polecenia z uprawnieniami administracyjnymi Uruchom następujące polecenia:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Uruchom ponownie system, aby umożliwić włączenie menu rozruchu

> [!NOTE]
> Limit czasu ustawiony dla menu Menedżera rozruchu do wyświetlania będzie miał wpływ na czas rozruchu systemu operacyjnego. Jeśli uważasz, że 10-sekundowa wartość limitu czasu jest zbyt krótka lub zbyt długa, ustaw ją na inną wartość.

## <a name="use-serial-console"></a>Korzystanie z konsoli szeregowej

### <a name="use-cmd-or-powershell-in-serial-console"></a>Używanie CMD lub PowerShell w konsoli szeregowej

1. Nawiąż połączenie z konsolą szeregową. W przypadku pomyślnego nawiązania połączenia zostanie wyświetlony monit **>SAC **:

   ![Nawiązywanie połączenia z konsolą SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)
1. Wprowadź, `cmd` Aby utworzyć kanał, który ma wystąpienie cmd.

1. Wprowadź `ch -si 1` lub naciśnij `<esc>+<tab>` klawisze skrótów, aby przełączyć się na kanał, na którym działa wystąpienie cmd.

1. Naciśnij klawisz **Enter**, a następnie wprowadź poświadczenia logowania z uprawnieniami administracyjnymi.

1. Po wprowadzeniu prawidłowych poświadczeń zostanie otwarte wystąpienie CMD.

1. Aby uruchomić wystąpienie programu PowerShell, wpisz `PowerShell` w wystąpieniu cmd, a następnie naciśnij klawisz **Enter**.

   ![Otwórz wystąpienie programu PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Korzystanie z konsoli szeregowej dla wywołań NMI
Przerwanie z maską (NMI) jest przeznaczone do tworzenia sygnałów, które nie będą ignorowane przez oprogramowanie na maszynie wirtualnej. Historycznie użyto NMIs do monitorowania problemów sprzętowych w systemach, które wymagały określonych czasów odpowiedzi. Obecnie programiści i Administratorzy systemu często używają NMI jako mechanizmu debugowania lub rozwiązywania problemów z nieodpowiadającymi systemami.

Konsola szeregowa może służyć do wysyłania NMI do maszyny wirtualnej platformy Azure przy użyciu ikony klawiatury na pasku poleceń. Po dostarczeniu NMI konfiguracja maszyny wirtualnej będzie kontrolować sposób reagowania systemu. System Windows można skonfigurować pod kątem awarii i utworzyć plik zrzutu pamięci podczas otrzymywania NMI.

![Wyślij NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Aby uzyskać informacje na temat konfigurowania systemu Windows w celu utworzenia pliku zrzutu awaryjnego po odebraniu NMI, zobacz [jak wygenerować plik zrzutu awaryjnego przy użyciu NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Używanie klawiszy funkcyjnych w konsoli szeregowej
Klucze funkcji są włączone do użycia w konsoli szeregowej na maszynach wirtualnych z systemem Windows. F8 na liście rozwijanej konsoli szeregowej zapewnia wygodę do łatwego wprowadzenia menu Zaawansowane ustawienia rozruchu, ale konsola szeregowa jest zgodna ze wszystkimi innymi kluczami funkcji. **Fn**  +  W zależności od komputera, z którego korzystasz z konsoli szeregowej, może być konieczne naciśnięcie klawisza Fn**F1** (lub F2, F3 itp.).

### <a name="use-wsl-in-serial-console"></a>Używanie WSL w konsoli szeregowej
Podsystem Windows dla systemu Linux (WSL) został włączony dla systemu Windows Server 2019 lub nowszego, dlatego można włączyć WSL do użycia w konsoli szeregowej, jeśli jest uruchomiony system Windows Server 2019 lub nowszy. Może to być korzystne w przypadku użytkowników, którzy mają również znajomość poleceń systemu Linux. Instrukcje dotyczące włączania WSL dla systemu Windows Server znajdują się w [przewodniku instalacji](/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Ponowne uruchamianie wystąpienia maszyny wirtualnej z systemem Windows/zestawu skalowania maszyn wirtualnych w konsoli szeregowej
Możesz zainicjować ponowne uruchomienie w konsoli szeregowej, przechodząc do przycisku energia, a następnie klikając pozycję "Uruchom ponownie maszynę wirtualną". Spowoduje to zainicjowanie ponownego uruchomienia maszyny wirtualnej, a w Azure Portal zostanie wyświetlone powiadomienie dotyczące ponownego uruchomienia.

Jest to przydatne w sytuacjach, w których możesz chcieć uzyskać dostęp do menu rozruchu bez opuszczania środowiska konsoli szeregowej.

![Ponowne uruchomienie konsoli szeregowej systemu Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Wyłącz konsolę seryjną
Domyślnie wszystkie subskrypcje mają włączony dostęp do konsoli szeregowej. Konsolę szeregową można wyłączyć na poziomie subskrypcji lub na maszynie wirtualnej/zestawie skalowania maszyn wirtualnych. Aby uzyskać szczegółowe instrukcje, zobacz [Włączanie i wyłączanie konsoli szeregowej platformy Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Zabezpieczenia Konsola szeregowa

### <a name="access-security"></a>Zabezpieczenia dostępu
Dostęp do konsoli szeregowej jest ograniczony do użytkowników, którzy mają rolę dostępu [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) lub wyższą dla maszyny wirtualnej. Jeśli dzierżawa Azure Active Directory wymaga uwierzytelniania wieloskładnikowego (MFA), dostęp do konsoli szeregowej będzie wymagał również usługi MFA, ponieważ dostęp do konsoli szeregowej odbywa się za pomocą [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpieczenia kanału
Wszystkie dane wysyłane z powrotem i do przodu są szyfrowane w sieci.

### <a name="audit-logs"></a>Dzienniki inspekcji
Wszystkie prawa dostępu do konsoli szeregowej są obecnie rejestrowane w dziennikach [diagnostyki rozruchu](./boot-diagnostics.md) maszyny wirtualnej. Dostęp do tych dzienników są własnością i są kontrolowane przez administratora maszyny wirtualnej platformy Azure.

> [!CAUTION]
> Żadne hasła dostępu do konsoli nie są rejestrowane. Jeśli jednak polecenia są uruchamiane w konsoli programu, zawierają hasła lub dane wyjściowe, wpisy tajne, nazwy użytkowników lub jakakolwiek inna forma informacji osobistych (dane osobowe), zostaną one zapisane w dziennikach diagnostyki rozruchu maszyny wirtualnej. Są one zapisywane wraz ze wszystkimi innymi widocznymi tekstami w ramach implementacji funkcji przewijania wstecz konsoli szeregowej. Te dzienniki są cykliczne i tylko osoby z uprawnieniami do odczytu dla konta magazynu diagnostyki mają do nich dostęp. Zaleca się jednak stosowanie Pulpit zdalny dla wszystkich elementów, które mogą zawierać tajemnice i/lub dane OSOBowe.

### <a name="concurrent-usage"></a>Współbieżne użycie
Jeśli użytkownik jest połączony z konsolą szeregową, a inny użytkownik pomyślnie zażąda dostępu do tej samej maszyny wirtualnej, pierwszy użytkownik zostanie odłączony, a drugi użytkownik nawiązał połączenie z tą samą sesją.

> [!CAUTION]
> Oznacza to, że użytkownik, który został odłączony, nie zostanie wylogowany. Możliwość wymuszenia wylogowania po rozłączeniu (przy użyciu mechanizmu SIGHUP lub podobnego) nadal jest w planie. W przypadku systemu Windows w konsoli SAC jest włączony automatyczny limit czasu. w przypadku systemu Linux można skonfigurować ustawienie limitu czasu terminalu.

## <a name="accessibility"></a>Ułatwienia dostępu
Ułatwienia dostępu to kluczowy fokus dla konsoli szeregowej platformy Azure. W tym celu upewnimy się, że konsola szeregowa jest dostępna dla osób z upośledzeniem programu Vision lub którzy mają słabą słuch, a także osoby, które mogą nie być w stanie korzystać z myszy.

### <a name="keyboard-navigation"></a>Nawigacja przy użyciu klawiatury
Użyj klawisza **Tab** na klawiaturze, aby przejść do interfejsu konsoli szeregowej z Azure Portal. Twoja lokalizacja zostanie wyróżniona na ekranie. Aby opuścić fokus okna konsoli szeregowej, naciśnij klawisz **Ctrl** + **F6** na klawiaturze.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Korzystanie z konsoli szeregowej z czytnikiem ekranu
Konsola szeregowa ma wbudowaną obsługę czytnika ekranu. Nawigowanie po włączeniu czytnika ekranu umożliwi odczytanie tekstu alternatywnego dla aktualnie wybranego przycisku na głos przez czytnik ekranu.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Typowe scenariusze uzyskiwania dostępu do konsoli szeregowej

Scenariusz          | Akcje w konsoli szeregowej
:------------------|:-----------------------------------------
Nieprawidłowe reguły zapory | Uzyskaj dostęp do konsoli szeregowej i napraw reguły zapory systemu Windows.
Uszkodzenie/sprawdzenie systemu plików | Uzyskaj dostęp do konsoli szeregowej i Odzyskaj system plików.
Problemy z konfiguracją RDP | Dostęp do konsoli szeregowej i zmiana ustawień. Aby uzyskać więcej informacji, zobacz [dokumentację protokołu RDP](/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
System blokowania sieci | Uzyskaj dostęp do konsoli szeregowej z Azure Portal, aby zarządzać systemem. Niektóre polecenia sieciowe są wymienione w [poleceniach systemu Windows: cmd i PowerShell](serial-console-cmd-ps-commands.md).
Korzystanie z programu inicjującego | Uzyskiwanie dostępu do danych BCD za pomocą konsoli szeregowej. Aby uzyskać więcej informacji, zobacz [Włączanie menu rozruchu systemu Windows w konsoli szeregowej](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Znane problemy
Mamy świadomość niektórych problemów z konsolą szeregową i systemem operacyjnym maszyny wirtualnej. Poniżej znajduje się lista tych problemów i kroków związanych z eliminowaniem maszyn wirtualnych z systemem Windows. Te problemy i środki zaradcze dotyczą zarówno maszyn wirtualnych, jak i wystąpień zestawów skalowania maszyn wirtualnych. Jeśli nie są one zgodne z wyświetlonym błędem, zobacz Typowe błędy usługi konsoli szeregowej w przypadku [typowych błędów konsoli szeregowej](./serial-console-errors.md).

Problem                             |   Ograniczanie ryzyka
:---------------------------------|:--------------------------------------------|
Naciśnięcie klawisza **Enter** po banerze połączenia nie spowoduje wyświetlenia monitu logowania. | Aby uzyskać więcej informacji, zobacz [naciśnięcie klawisza ENTER nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ten błąd może wystąpić, jeśli uruchamiasz niestandardową maszynę wirtualną, urządzenie z ograniczeniami lub konfigurację rozruchową, która powoduje, że system Windows nie może prawidłowo nawiązać połączenia z portem szeregowym. Ten błąd występuje również w przypadku korzystania z maszyny wirtualnej z systemem Windows 10, ponieważ na maszynach wirtualnych z systemem Windows Server skonfigurowano obsługę usług EMS.
Podczas nawiązywania połączenia z maszyną wirtualną z systemem Windows wyświetlane są tylko informacje o kondycji| Ten błąd występuje, jeśli Specjalna konsola administracyjna nie została włączona dla obrazu systemu Windows. Zobacz [Włączanie konsoli szeregowej w obrazach niestandardowych lub starszych,](#enable-the-serial-console-in-custom-or-older-images) Aby uzyskać instrukcje dotyczące ręcznego włączania konsoli SAC na maszynie wirtualnej z systemem Windows. Aby uzyskać więcej informacji, zobacz [sygnały kondycji systemu Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
Konsola SAC nie przyjmuje całego obszaru konsoli szeregowej w przeglądarce | Jest to znany problem dotyczący systemu Windows i emulatora terminalu. Śledzimy ten problem zarówno z zespołami, ale nie ma żadnych środków zaradczych.
Nie można wpisać w wierszu polecenia SAC, jeśli debugowanie jądra jest włączone. | Protokół RDP z maszyną wirtualną i uruchamiany `bcdedit /debug {current} off` z wiersza polecenia z podwyższonym poziomem uprawnień. Jeśli nie możesz użyć protokołu RDP, możesz zamiast tego dołączyć dysk systemu operacyjnego do innej maszyny wirtualnej platformy Azure i zmodyfikować go wraz z dyskiem danych przez uruchomienie `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` , a następnie ponownie Zastąp dysk.
Wklejanie do programu PowerShell w konsoli SAC skutkuje trzecią literą, jeśli oryginalna zawartość zawierała powtarzający się znak. | Aby obejść obejście, uruchom polecenie `Remove-Module PSReadLine` w celu zwolnienia modułu PSReadLine z bieżącej sesji. Ta akcja nie spowoduje usunięcia ani odinstalowania modułu.
Niektóre dane wejściowe z klawiatury generują dziwne dane wyjściowe SAC (na przykład **[A**, **[3 ~**). | Sekwencje unikowe [VT100](/windows/console/console-virtual-terminal-sequences) nie są obsługiwane przez monit konsoli SAC.
Wklejanie długich ciągów nie działa. | Konsola szeregowa ogranicza długość ciągów wklejonych do terminalu do 2048 znaków, aby zapobiec przeciążeniu przepustowości portu szeregowego.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytania. Jak mogę wysłać opinię?**

A. Prześlij opinię, tworząc problem w usłudze GitHub w witrynie https://aka.ms/serialconsolefeedback . Alternatywnie (mniej preferowany) można wysłać opinię za pośrednictwem azserialhelp@microsoft.com lub w kategorii maszyna wirtualna https://feedback.azure.com .

**Pytania. Czy konsola szeregowa obsługuje kopiowanie/wklejanie?**

A. Tak. Użyj **klawiszy CTRL** + **SHIFT** + **C** i **Ctrl** + **SHIFT** + **V** , aby skopiować i wkleić do terminalu.

**Pytania. Kto może włączyć lub wyłączyć konsolę szeregową dla mojej subskrypcji?**

A. Aby włączyć lub wyłączyć konsolę szeregową na poziomie całej subskrypcji, musisz mieć uprawnienia do zapisu dla subskrypcji. Role, które mają uprawnienia do zapisu, obejmują role administratora lub właściciela. Role niestandardowe mogą mieć również uprawnienia do zapisu.

**Pytania. Kto może uzyskać dostęp do konsoli szeregowej dla mojej maszyny wirtualnej?**

A. Aby maszyna wirtualna mogła uzyskać dostęp do konsoli szeregowej maszyny wirtualnej, musi mieć rolę współautora maszyn wirtualnych lub wyższą.

**Pytania. Moja konsola szeregowa nie wyświetla niczego, co mam zrobić?**

A. Obraz jest prawdopodobnie błędnie skonfigurowany na potrzeby dostępu do konsoli szeregowej. Aby uzyskać informacje o konfigurowaniu obrazu w celu włączenia konsoli szeregowej, zobacz [Włączanie konsoli szeregowej w obrazie niestandardowym lub starszym](#enable-the-serial-console-in-custom-or-older-images).

**Pytania. Czy konsola szeregowa jest dostępna dla zestawów skalowania maszyn wirtualnych?**

A. Tak! Zobacz [konsolę szeregowa dla Virtual Machine Scale Sets](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać szczegółowy przewodnik dotyczący poleceń CMD i PowerShell, których można użyć w konsoli SAC systemu Windows, zobacz [polecenia systemu Windows: cmd i PowerShell](serial-console-cmd-ps-commands.md).
* Konsola szeregowa jest również dostępna dla maszyn wirtualnych z [systemem Linux](serial-console-linux.md) .
* Dowiedz się więcej na temat [diagnostyki rozruchu](boot-diagnostics.md).