---
title: Włączanie graficznego pulpitu zdalnego dla systemu Linux w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak włączyć pulpit zdalny dla maszyn wirtualnych z systemem Linux w laboratorium w Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 34c940fec388bb0e79ab5e1db9be6d52fb223873
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647957"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Włączanie graficznego pulpitu zdalnego dla maszyn wirtualnych z systemem Linux w Azure Lab Services
W tym artykule przedstawiono sposób wykonywania następujących zadań:

- Włączanie graficznych sesji pulpitu zdalnego dla maszyny wirtualnej z systemem Linux
- Jak nawiązać połączenie z maszyną wirtualną z systemem Linux przy użyciu protokołu RDP (Remote Desktop Protocol) lub X2Go klientów usług pulpitu zdalnego

## <a name="set-up-graphical-remote-desktop-solution"></a>Skonfiguruj graficzne rozwiązanie pulpitu zdalnego
Po utworzeniu laboratorium z obrazu systemu **Linux** automatycznie konfigurowany jest dostęp **SSH** (Secure Shell), dzięki czemu instruktor może połączyć się z szablonową maszyną wirtualną z wiersza polecenia przy użyciu protokołu SSH.  Podobnie po opublikowaniu szablonu maszyny wirtualnej studenci mogą również łączyć się z maszynami wirtualnymi przy użyciu protokołu SSH.

Aby nawiązać połączenie z maszyną wirtualną z systemem Linux przy **użyciu graficznego** interfejsu użytkownika, zalecamy użycie **protokołu RDP** lub **x2go**.  Obie te opcje wymagają, aby instruktor wykonał kilka dodatkowych czynności konfiguracyjnych na maszynie wirtualnej szablonu:

### <a name="rdp-setup"></a>Konfiguracja protokołu RDP
Aby można było korzystać z protokołu RDP, instruktor musi:
  - Włącz Podłączanie pulpitu zdalnego; jest to konieczne, aby otworzyć port maszyny wirtualnej dla protokołu RDP.
  - Zainstaluj serwer usług pulpitu zdalnego RDP.
  - Zainstaluj środowisko pulpitu graficznego systemu Linux (takie jak oficer, pulpit Xfce itd.).

### <a name="x2go-setup"></a>Konfiguracja X2Go
Aby użyć X2Go, instruktor musi:
- Zainstaluj serwer usług pulpitu zdalnego X2Go.
- Zainstaluj środowisko pulpitu graficznego systemu Linux (takie jak oficer, pulpit Xfce itd.).

X2Go używa tego samego portu, który jest już włączony dla protokołu SSH.  W związku z tym nie jest wymagana dodatkowa konfiguracja, aby otworzyć port na maszynie wirtualnej dla X2Go.

> [!NOTE]
> W niektórych przypadkach, takich jak Ubuntu LTS 18,04, X2Go zapewnia lepszą wydajność.  W przypadku użycia protokołu RDP i powiadomienia podczas współpracy ze środowiskiem graficznym, rozważ podjęcie próby X2Go, ponieważ może to poprawić wydajność.

> [!IMPORTANT]
>  Niektóre obrazy z witryny Marketplace mają już zainstalowane graficzne środowisko pulpitu i serwer usług pulpitu zdalnego.  Na przykład [Data Science Virtual Machine dla systemu Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) ma już [zainstalowany serwer pulpit Xfce i x2go, który umożliwia akceptowanie połączeń klienckich](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>Włącz Podłączanie pulpitu zdalnego dla protokołu RDP

Ten krok jest wymagany tylko w celu nawiązania połączenia przy użyciu protokołu RDP.  Jeśli zamiast tego planujesz używać X2Go, możesz przejść do następnej sekcji, ponieważ X2Go używa portu SSH.

1.  Podczas tworzenia laboratorium instruktor może **włączyć Podłączanie pulpitu zdalnego**.  Instruktor musi **włączyć** tę opcję, aby otworzyć port na maszynie wirtualnej z systemem Linux, która jest wymagana dla sesji pulpitu zdalnego RDP.  W przeciwnym razie, jeśli ta opcja zostanie **wyłączona**, zostanie otwarty tylko port dla protokołu SSH.
  
    ![Zrzut ekranu pokazujący okno "nowe laboratorium" z opcją "Włącz Podłączanie pulpitu zdalnego".](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. W oknie dialogowym **Włączanie komunikatu Podłączanie pulpitu zdalnego** wybierz pozycję **Kontynuuj z pulpit zdalny**. 

    ![Włączanie połączenia pulpitu zdalnego dla obrazu systemu Linux](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>Zainstaluj protokół RDP lub X2Go

Po utworzeniu laboratorium instruktor musi upewnić się, że na maszynie wirtualnej szablonu zainstalowano graficzne środowisko pulpitu i serwer usług pulpitu zdalnego.  Instruktorzy muszą najpierw połączyć się z szablonową maszyną wirtualną przy użyciu protokołu SSH, aby zainstalować pakiety dla:
- Serwer usług pulpitu zdalnego RDP lub X2Go.
- Graficzne środowisko komputerowe, takie jak oficer, pulpit Xfce itp.

Po skonfigurowaniu usługi instruktor może połączyć się z szablonową maszyną wirtualną przy użyciu klienta **pulpit zdalny Microsoft (RDP)** lub klienta **x2go** .

Wykonaj poniższe kroki, aby skonfigurować maszynę wirtualną szablonu:

1. Jeśli widzisz przycisk **Dostosuj szablon** na pasku narzędzi, wybierz go. Następnie wybierz pozycję **Kontynuuj** w oknie dialogowym **Dostosuj szablon** . Ta akcja uruchamia szablon maszyny wirtualnej.  

    ![Dostosuj szablon](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. Po rozpoczęciu pracy szablonu można wybrać pozycję **Połącz szablon** , a następnie **nawiązać połączenie za pośrednictwem protokołu SSH** na pasku narzędzi. 

    ![Nawiązywanie połączenia z szablonem za pośrednictwem protokołu RDP po utworzeniu laboratorium](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. Zostanie wyświetlone okno dialogowe **Połącz z maszyną wirtualną** . Wybierz przycisk **Kopiuj** obok pola tekstowego, aby skopiować go do Schowka. Zapisz informacje o połączeniu SSH. Aby nawiązać połączenie z maszyną wirtualną, Użyj tych informacji o połączeniu z terminalu SSH [(jak tutaj](https://www.putty.org/)).
 
    ![Parametry połączenia SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Zainstaluj protokół RDP lub X2Go wraz z wybranym środowiskiem graficznym.  Zapoznaj się z następującymi instrukcjami:
    - [Instalowanie i Konfigurowanie protokołu RDP](../virtual-machines/linux/use-remote-desktop.md)
    - [Instalowanie i Konfigurowanie X2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Nawiązywanie połączenia z szablonową maszyną wirtualną za pomocą graficznego interfejsu użytkownika

Po skonfigurowaniu maszyny wirtualnej z szablonem instruktor może nawiązać połączenie za pośrednictwem graficznego interfejsu użytkownika przy użyciu klienta **pulpit zdalny Microsoft (RDP)** lub klienta **x2go** .  Klient, którego używasz, zależy od tego, czy protokół RDP lub X2Go jest skonfigurowany jako serwer usług pulpitu zdalnego na maszynie wirtualnej szablonu.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Klient Pulpit zdalny Microsoft (RDP)

Klient Pulpit zdalny Microsoft (RDP) służy do nawiązywania połączenia z szablonową maszyną wirtualną, która ma skonfigurowany protokół RDP.  Klienta Pulpit zdalny można używać w systemach Windows, Chromebooks, Mac i innych.  Aby uzyskać więcej informacji, zapoznaj się z artykułem na [pulpit zdalny klientów](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) .

Postępuj zgodnie z poniższymi instrukcjami w zależności od typu komputera używanego do nawiązywania połączenia z szablonową maszyną wirtualną:

- Windows
  1. Kliknij pozycję **Połącz z szablonem** na pasku narzędzi laboratorium i wybierz pozycję **Połącz za pośrednictwem protokołu RDP** , aby nawiązać połączenie z szablonową maszyną wirtualną. 
  1. Zapisz plik RDP i użyj go do nawiązania połączenia z szablonową maszyną wirtualną przy użyciu klienta Pulpit zdalny. 
  1. Zwykle klient Pulpit zdalny jest już zainstalowany i skonfigurowany w systemie Windows.  W związku z tym wystarczy kliknąć plik RDP, aby go otworzyć i uruchomić sesję zdalną.

- Mac
  1. Kliknij pozycję **Połącz z szablonem** na pasku narzędzi laboratorium, a następnie wybierz pozycję **Połącz za pośrednictwem protokołu RDP** , aby zapisać plik RDP.  
  1. Następnie zapoznaj się z artykułem jak połączyć się z [maszyną wirtualną przy użyciu protokołu RDP na komputerze Mac](connect-virtual-machine-mac-remote-desktop.md).

- Chromebook
  1. Kliknij pozycję **Połącz z szablonem** na pasku narzędzi laboratorium, a następnie wybierz pozycję **Połącz za pośrednictwem protokołu RDP** , aby zapisać plik RDP.  
  1. Następnie zapoznaj się z artykułem jak połączyć się z [maszyną wirtualną przy użyciu protokołu RDP na Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="x2go-client"></a>Klient X2Go

Klient X2Go służy do łączenia się z maszyną wirtualną szablonu, która ma skonfigurowany X2Go.  Korzystając z informacji o połączeniu SSH szablonu maszyny wirtualnej, wykonaj kroki opisane w artykule jak [nawiązać połączenie z maszyną wirtualną przy użyciu x2go](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go).

## <a name="next-steps"></a>Następne kroki
Gdy instruktor skonfiguruje protokół RDP lub X2Go na swojej maszynie wirtualnej z szablonem i opublikuje, uczniowie mogą łączyć się z maszynami wirtualnymi za pośrednictwem pulpitu zdalnego GUI lub protokołu SSH.

Aby uzyskać więcej informacji, zobacz:
 - [Nawiązywanie połączenia z maszyną wirtualną z systemem Linux](how-to-use-remote-desktop-linux-student.md)