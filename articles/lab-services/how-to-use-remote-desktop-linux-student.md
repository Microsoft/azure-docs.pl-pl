---
title: Nawiązywanie połączenia z maszyną wirtualną z systemem Linux w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak korzystać z usług pulpitu zdalnego dla maszyn wirtualnych z systemem Linux w laboratorium w Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d13868477ff2e3378d87d7785789a7498ed17e59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85443421"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Nawiązywanie połączenia z maszynami wirtualnymi z systemem Linux w laboratorium z klasą Azure Lab Services
W tym artykule pokazano, jak uczniowie mogą łączyć się z maszyną wirtualną z systemem Linux w laboratorium przy użyciu:
- Terminal SSH (Secure Shell Protocol)
- Interfejs GUI (graficzny interfejs użytkownika) pulpit zdalny

> [!IMPORTANT] 
> Protokół SSH jest konfigurowany automatycznie, aby zarówno uczniowie, jak i instruktorzy mogą SSH do maszyn wirtualnych z systemem Linux bez żadnej dodatkowej konfiguracji. Jeśli jednak uczniowie muszą nawiązać połączenie z użyciem graficznego interfejsu użytkownika, może być konieczne wykonanie dodatkowych czynności konfiguracyjnych.  Aby uzyskać szczegółowe informacje, zobacz [Włączanie usługi pulpit zdalny dla maszyn wirtualnych z systemem Linux](how-to-enable-remote-desktop-linux.md).

## <a name="connect-to-the-student-vm-using-ssh"></a>Nawiązywanie połączenia z maszyną wirtualną uczniów przy użyciu protokołu SSH

1. Gdy student loguje się bezpośrednio do portalu Labs ( `https://labs.azure.com` ) lub przy użyciu linku do rejestracji ( `https://labs.azure.com/register/<registrationCode>` ), zostanie wyświetlony kafelek dla każdego laboratorium, do którego student ma dostęp. 
   
1. Na kafelku Przełącz przycisk, aby uruchomić maszynę wirtualną, jeśli jest ona w stanie zatrzymania. 

2. Wybierz pozycję **Połącz**. Zobaczysz dwie opcje połączenia z maszyną wirtualną: **SSH** i **RDP**.

    ![Maszyna wirtualna ucznia — opcje połączeń](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. Wybierz opcję **SSH** , aby wyświetlić okno dialogowe **łączenie z maszyną wirtualną** :  

    ![Parametry połączenia SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Kliknij przycisk **kopiowania** obok pola tekstowego, aby skopiować informacje o połączeniu SSH do Schowka. 

5. Zapisz informacje o połączeniu SSH, takie jak w konsoli tekstu, aby można było użyć tych informacji o połączeniu w następnym kroku.

6. Z poziomu terminalu SSH [(jak tutaj](https://www.putty.org/)) Połącz się z maszyną wirtualną.

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>Nawiązywanie połączenia z maszyną wirtualną uczniów przy użyciu klasycznego interfejsu użytkownika
Instruktor może zdecydować się na skonfigurowanie maszyn wirtualnych, aby studenci mogli również łączyć się za pomocą pulpitu zdalnego GUI.  W takim przypadku uczniowie muszą sprawdzić, czy chcesz połączyć się z maszynami wirtualnymi za pomocą aplikacji klienckiej **pulpit zdalny Microsoft (RDP)** czy **x2go** .  Obie te aplikacje umożliwiają studentowi zdalne nawiązywanie połączenia z maszyną wirtualną i Wyświetlanie graficznego pulpitu systemu Linux na komputerze lokalnym.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Nawiązywanie połączenia z maszyną wirtualną ucznia przy użyciu Pulpit zdalny Microsoft (RDP)
Studenci mogą używać Pulpit zdalny Microsoft (RDP) do nawiązywania połączenia z maszynami wirtualnymi z systemem Linux po skonfigurowaniu ich laboratorium przy użyciu pakietów RDP i GUI dla środowiska pulpitu graficznego systemu Linux (np. OFICERów, pulpit Xfce itd.). Poniżej przedstawiono kroki, które należy wykonać, aby nawiązać połączenie: 

1. Na kafelku maszyny wirtualnej upewnij się, że maszyna wirtualna jest uruchomiona, a następnie kliknij przycisk **Połącz**. Zobaczysz dwie opcje połączenia z maszyną wirtualną: **SSH** i **RDP**.

    ![Maszyna wirtualna ucznia — opcje połączeń](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. Wybierz opcję **RDP** .  Gdy plik RDP zostanie pobrany na komputer, Zapisz go na maszynie wirtualnej.

3. W przypadku łączenia się z komputera z systemem Windows zazwyczaj jest już zainstalowany i skonfigurowany klient Pulpit zdalny Microsoft (RDP).  W związku z tym wystarczy kliknąć plik RDP, aby go otworzyć i uruchomić sesję zdalną.

    Zamiast tego, jeśli łączysz się z komputera Mac lub Chromebook, zapoznaj się z następującymi krokami:
   - [Nawiązywanie połączenia z maszyną wirtualną przy użyciu protokołu RDP na komputerze Mac](connect-virtual-machine-mac-remote-desktop.md).
   - [Nawiąż połączenie z maszyną wirtualną przy użyciu protokołu RDP na Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).  

### <a name="connect-to-the-student-vm-using-x2go"></a>Nawiązywanie połączenia z maszyną wirtualną ucznia przy użyciu X2Go
Studenci mogą używać X2Go do nawiązywania połączenia z maszynami wirtualnymi z systemem Linux, gdy ich instruktor konfiguruje laboratorium za pomocą X2Go i pakietów graficznego interfejsu użytkownika dla środowiska pulpitu graficznego systemu Linux (np. OFICERów, pulpit Xfce itd.).

Studenci muszą dowiedzieć się, w jakim środowisku pulpitu graficznego systemu Linux zainstalowano jego instruktora.  Te informacje są wymagane w następnych krokach w celu nawiązania połączenia przy użyciu klienta programu X2Go.

1. Zainstaluj [klienta programu x2go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) na komputerze lokalnym.

1. Postępuj zgodnie z instrukcjami w [pierwszej sekcji](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) , aby skopiować informacje o połączeniu SSH dla maszyny wirtualnej.  Te informacje są potrzebne do nawiązania połączenia przy użyciu klienta X2Go.

1. Po uzyskaniu informacji o połączeniu SSH Otwórz klienta programu x2go i wybierz pozycję **sesja**  >  **Nowa sesja**.
   ![X2Go Utwórz nową sesję](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. Wprowadź wartości w okienku **preferencji sesji** na podstawie informacji o połączeniu SSH.  Na przykład informacje o połączeniu będą wyglądać podobnie do tego:

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    Korzystając z tego przykładu, wprowadzane są następujące wartości:

   - **Nazwa sesji** — Określ nazwę, taką jak nazwa maszyny wirtualnej.
   - **Host** — Identyfikator maszyny wirtualnej; na przykład **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** .
   - **Logowanie** — nazwa użytkownika dla maszyny wirtualnej; na przykład **student**.
   - **Port SSH** — unikatowy port przypisany do maszyny wirtualnej; na przykład **12345**.
   - **Typ sesji** — Wybierz środowisko pulpitu graficznego systemu Linux, dla którego instruktor SKONFIGUROWAł maszynę wirtualną.  Musisz uzyskać te informacje od instruktora.

    Na koniec kliknij przycisk **OK** , aby utworzyć sesję.

    ![Preferencje sesji X2Go](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  Kliknij sesję w okienku po prawej stronie.

    ![X2Go Rozpocznij nową sesję](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > Jeśli zostanie wyświetlony monit z podobnym komunikatem, wybierz pozycję **tak** , aby kontynuować wprowadzanie hasła: **nie można ustalić autentyczności hosta "[ `00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com` ]: 12345".  Odcisk palca klucza ECDSA to SHA256:00000000000000000000000000000000000000000000. Czy na pewno chcesz kontynuować łączenie (tak/nie)?**

2. Po wyświetleniu monitu wprowadź hasło i kliknij przycisk **OK**.  Teraz będziesz zdalnie połączony ze środowiskiem graficznym GUI maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak włączyć funkcję Podłączanie pulpitu zdalnego dla maszyn wirtualnych z systemem Linux w laboratorium z klasą, zobacz temat [Włączanie usług pulpitu zdalnego dla komputerów z systemem Linux](how-to-enable-remote-desktop-linux.md). 

