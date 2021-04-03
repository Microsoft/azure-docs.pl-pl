---
title: Jak uzyskać dostęp do laboratorium w usłudze Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak zarejestrować się w laboratorium zajęć, wyświetlić wszystkie laboratoria, do których można uzyskać dostęp, uruchomić/zatrzymać maszynę wirtualną w laboratorium i połączyć się z maszyną wirtualną.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 6d5585082473a363df5f25f331475536544ae828
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433142"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak uzyskać dostęp do laboratorium w usłudze Azure Lab Services
W tym artykule opisano sposób rejestracji w laboratorium zajęć, wyświetlania wszystkich laboratoriów, do których można uzyskać dostęp, uruchamiania/zatrzymywania maszyny wirtualnej w laboratorium oraz nawiązywania połączenia z maszyną wirtualną. 

## <a name="register-to-the-lab"></a>Zarejestruj się w laboratorium

1. Przejdź do **adresu URL rejestracji** otrzymanego z nauczycieli. Po ukończeniu rejestracji nie musisz używać adresu URL rejestracji. Zamiast tego użyj adresu URL: [https://labs.azure.com](https://labs.azure.com) . Program Internet Explorer 11 nie jest jeszcze obsługiwany. 

    ![Zarejestruj się w laboratorium](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. Zaloguj się do usługi przy użyciu konta służbowego, aby ukończyć rejestrację. 

    > [!NOTE]
    > Do korzystania z Azure Lab Services jest wymagany konto Microsoft. Jeśli próbujesz zalogować się do portalu przy użyciu innych konto Microsoft, takich jak Yahoo lub konta Google, postępuj zgodnie z instrukcjami, aby utworzyć konto Microsoft, który zostanie połączony z niekonto Microsoft. Następnie postępuj zgodnie z instrukcjami, aby ukończyć proces rejestracji. 
1. Po zarejestrowaniu upewnij się, że widzisz maszyny wirtualne dla laboratorium, do którego masz dostęp. 

    ![Dostępne maszyny wirtualne](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. Zaczekaj, aż maszyna wirtualna będzie gotowa. Na kafelku maszyny wirtualnej Zwróć uwagę na następujące pola:
    1. W górnej części kafelka zobaczysz **nazwę laboratorium**.
    1. Po prawej stronie zostanie wyświetlona ikona reprezentująca **system operacyjny (OS)** maszyny wirtualnej. W tym przykładzie jest to system operacyjny Windows. 
    1. Zobaczysz ikonę/przyciski w dolnej części kafelka, aby uruchomić/zatrzymać maszynę wirtualną, a następnie nawiązać połączenie z maszyną wirtualną. 
    1. Na prawo od przycisków zobaczysz stan maszyny wirtualnej. Upewnij się, że stan maszyny wirtualnej jest **zatrzymany**.

        ![Maszyna wirtualna w stanie zatrzymania](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Uruchamianie lub zatrzymywanie maszyny wirtualnej
1. **Uruchom** maszynę wirtualną, wybierając pierwszy przycisk, jak pokazano na poniższej ilustracji. Ten proces zajmuje trochę czasu.  

    ![Uruchamianie maszyny wirtualnej](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Upewnij się, że stan maszyny wirtualnej to **uruchomiono**. 

    ![Maszyna wirtualna w stanie uruchomienia](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Zauważ, że ikona pierwszego przycisku została zmieniona, aby reprezentować operację **zatrzymania** . Możesz wybrać ten przycisk, aby zatrzymać maszynę wirtualną. 

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

1. Wybierz drugi przycisk, jak pokazano na poniższej ilustracji, aby **nawiązać połączenie** z maszyną wirtualną laboratorium. 

    ![Łączenie z maszyną wirtualną](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Wykonaj jedną z następujących czynności: 
    1. W przypadku maszyn wirtualnych z **systemem Windows** Zapisz plik **RDP** na dysku twardym. Otwórz plik RDP, aby nawiązać połączenie z maszyną wirtualną. Użyj **nazwy użytkownika** i **hasła** uzyskanych z nauczycieli, aby zalogować się na komputerze. 
    3. W przypadku maszyn wirtualnych z **systemem Linux** można użyć protokołu **SSH** lub **RDP** (jeśli jest włączony), aby połączyć się z nimi. Aby uzyskać więcej informacji, zobacz [Włączanie usługi Podłączanie pulpitu zdalnego dla komputerów z systemem Linux](how-to-enable-remote-desktop-linux.md). 
    1. Jeśli używasz **komputera Mac** do nawiązywania połączenia z maszyną wirtualną laboratorium, postępuj zgodnie z instrukcjami w następnej sekcji. 

## <a name="progress-bar"></a>Pasek postępu 
Pasek postępu na kafelku pokazuje liczbę godzin użycia w odniesieniu do liczby godzin [przypisanych](how-to-configure-student-usage.md#set-quotas-for-users) do użytkownika. Ten czas jest dodatkowym czasem przydzielonym do czasu zaplanowanym dla laboratorium. Kolor paska postępu i tekstu pod paskiem postępu różnią się w zależności od następujących scenariuszy:

- Jeśli klasa jest w toku (w ramach harmonogramu klasy), pasek postępu jest wyszarzony do reprezentowania godzin przydziału nie jest używany. 

    ![Pasek postępu w kolorze szarym](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Jeśli limit przydziału nie zostanie przypisany (zero godzin), tekst **dostępny podczas klas** jest pokazywany zamiast paska postępu. 
    
    ![Stan, gdy nie ustawiono limitu przydziału](./media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Jeśli wykorzystano **limit przydziału**, kolor paska postępu jest **czerwony**. 

    ![Pasek postępu w kolorze czerwonym](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- Kolor paska postępu jest **niebieski** , gdy jest on poza zaplanowanym terminem dla laboratorium i został użyty jakiś czas przydziału. 

    ![Pasek postępu w kolorze niebieskim](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-labs"></a>Wyświetl wszystkie laboratoria
Po zarejestrowaniu się w laboratoriach można wyświetlić wszystkie laboratoria, wykonując następujące czynności: 

1. Przejdź do [https://labs.azure.com](https://labs.azure.com) . Program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Zaloguj się w usłudze przy użyciu konta użytkownika, którego użyto do zarejestrowania się do laboratorium. 
3. Upewnij się, że są wyświetlone wszystkie laboratoria, do których masz dostęp. 

    ![Wyświetlanie wszystkich laboratoriów](./media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
 