---
title: Konfigurowanie laboratorium na potrzeby zajęć przy użyciu usługi Azure Lab Services | Microsoft Docs
description: W tym samouczku użyjesz Azure Lab Services, aby skonfigurować laboratorium zajęć z maszynami wirtualnymi, które są używane przez uczniów w klasie.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 1155646d31ddb8a0a3abce025acde5c4cb645f54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336738"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Samouczek: konfigurowanie laboratorium na potrzeby zajęć 
W tym samouczku skonfigurujesz laboratorium na potrzeby zajęć z maszynami wirtualnymi używanymi przez uczniów podczas zajęć.  

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie laboratorium na potrzeby zajęć
> * Dodawanie użytkowników do laboratorium
> * Ustaw harmonogram dla laboratorium
> * Wyślij wiadomość e-mail z zaproszeniem do uczniów

## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku skonfigurujesz laboratorium z maszynami wirtualnymi dla klasy. Aby skonfigurować laboratorium zajęć na koncie laboratorium, musisz być członkiem jednej z tych ról na koncie laboratorium: właściciel, twórca laboratorium lub współautor. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do roli właściciela. W związku z tym możesz użyć konta użytkownika, które zostało użyte do utworzenia konta laboratorium, aby utworzyć laboratorium zajęć. 

Poniżej przedstawiono typowy przepływ pracy podczas korzystania z Azure Lab Services:

1. Twórca konta laboratorium dodaje innych użytkowników do roli **twórcy laboratorium** . Na przykład twórca konta laboratorium/administrator dodaje wykładowców do roli **twórca laboratorium** , aby mogli tworzyć laboratoria dla ich klas. 
2. Następnie nauczyciele tworzą laboratoria z maszynami wirtualnymi dla ich klas i wysyłają linki rejestracji do uczniów w klasie. 
3. Studenci używają linku rejestracji otrzymywanego od wykładowców, aby zarejestrować się w laboratorium. Po zarejestrowaniu się studenci mogą używać maszyn wirtualnych w laboratoriach do wykonywania zadań na zajęciach lub pracy domowej. 

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć
W tym kroku utworzysz laboratorium dla swojej klasy na platformie Azure. 

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). Należy pamiętać, że program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. Wybierz pozycję **nowe laboratorium**. 
    
    ![Zrzut ekranu pokazujący "Azure Lab Services" z wybranym przyciskiem "nowy Lab".](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** laboratorium i wybierz pozycję **dalej**.  

        ![Tworzenie laboratorium na potrzeby zajęć](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. Na stronie **poświadczenia maszyny wirtualnej** Określ domyślne poświadczenia dla wszystkich maszyn wirtualnych w laboratorium. Określ **nazwę** i **hasło** użytkownika, a następnie wybierz przycisk **dalej**.  

        ![Nowe okno laboratorium](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Zanotuj nazwę użytkownika i hasło. Nie zostaną one ponownie wyświetlone.
    3. Na stronie **zasady laboratorium** wybierz pozycję **Zakończ**. 

        ![Przydział dla każdego użytkownika](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Powinien zostać wyświetlony następujący ekran pokazujący stan tworzenia szablonu maszyny wirtualnej. Ta operacja może potrwać do 20 minut. 

    ![Stan tworzenia szablonu maszyny wirtualnej](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na stronie **szablon** wykonaj następujące czynności: te kroki są **opcjonalne** dla tego samouczka.

    1. Nawiąż połączenie z maszyną wirtualną szablonu, wybierając pozycję **Połącz**. Jeśli jest to maszyna wirtualna z szablonem systemu Linux, wybierz, czy chcesz nawiązać połączenie przy użyciu protokołu SSH, czy RDP (Jeśli włączono protokół RDP).
    3. Zainstaluj i Skonfiguruj oprogramowanie wymagane dla danej klasy na maszynie wirtualnej z szablonem. 
    4. **Zatrzymaj** maszynę wirtualną szablonu.  

    > [!NOTE]
    > Na maszynach wirtualnych szablonowych naliczane są **koszty** , dlatego należy się upewnić, że maszyna wirtualna jest ZAMKNIĘTA, gdy nie jest potrzebna do uruchomienia. 

## <a name="publish-the-template-vm"></a>Publikowanie maszyny wirtualnej szablonu
Ten krok polega na opublikowaniu szablonu maszyny wirtualnej. Podczas publikowania szablonu maszyny wirtualnej Azure Lab Services tworzy maszyny wirtualne w laboratorium przy użyciu szablonu. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon.

1. Na stronie **szablon** wybierz pozycję **Publikuj** na pasku narzędzi. 

    ![Przycisk Publikuj szablon](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Nie można cofnąć publikowania szablonu. 
2. Na stronie **szablon publikacji** wprowadź liczbę maszyn wirtualnych, które chcesz utworzyć w laboratorium, a następnie wybierz pozycję **Publikuj**. 

    ![Szablon publikacji — liczba maszyn wirtualnych](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Zobaczysz **stan publikowania** szablonu na stronie. Ten proces może potrwać do godziny. 

    ![Publikowanie szablonu — postęp](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Zaczekaj na zakończenie publikowania, a następnie przejdź do strony **puli maszyn wirtualnych** , wybierając pozycję **maszyny** wirtualne w menu po lewej stronie lub wybierając pozycję kafelek **maszyny wirtualne** . Sprawdź, czy są widoczne maszyny wirtualne o stanie **Nie przypisano**. Te maszyny wirtualne nie zostały jeszcze przypisane do uczniów. Powinny mieć stan **Zatrzymano**. Z poziomu tej strony możesz uruchomić maszynę wirtualną ucznia, połączyć się z maszyną wirtualną, zatrzymać maszynę wirtualną i usunąć maszynę wirtualną. Maszyny wirtualne możesz uruchomić na tej stronie lub pozwolić, aby zrobili to uczniowie. 

    ![Maszyny wirtualne w stanie Zatrzymano](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Gdy nauczycieli włącza maszynę wirtualną ucznia, nie ma to żadnego limitu przydziału dla ucznia. Przydział dla użytkownika określa liczbę godzin laboratorium dostępnych dla użytkownika poza zaplanowanym czasem klasy. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Ustawianie przydziałów dla użytkowników](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Ustaw harmonogram dla laboratorium
Utwórz zaplanowane zdarzenie dla laboratorium, aby maszyny wirtualne w laboratorium były automatycznie uruchamiane/zatrzymywane w określonych godzinach. Limit przydziału użytkownika (domyślnie: 10 godzin) określony wcześniej to dodatkowy czas przypisany do każdego użytkownika poza tym zaplanowanym czasem. 

1. Przejdź do strony **harmonogramy** , a następnie wybierz pozycję **Dodaj wydarzenie zaplanowane** na pasku narzędzi. 

    ![Zrzut ekranu pokazujący przycisk "Dodaj zaplanowane zdarzenie" na stronie "harmonogramy".](./media/how-to-create-schedules/add-schedule-button.png)
2. Na stronie **Dodawanie zaplanowanego zdarzenia** wykonaj następujące czynności:
    1. Upewnij się, że w polu **Standardowy** wybrano **Typ zdarzenia**.  
    2. Wybierz **datę początkową** klasy. 
    4. Wybierz **godzinę rozpoczęcia** uruchamiania maszyn wirtualnych.
    5. Wybierz **czas zatrzymania** , w którym maszyny wirtualne mają zostać zamknięte. 
    6. Wybierz **strefę czasową** dla określonych godzin rozpoczęcia i zakończenia. 
3. Na tej samej stronie **Dodawanie zaplanowanego zdarzenia** wybierz bieżący harmonogram w sekcji **powtarzanie** .  

    ![Przycisk dodawania harmonogramu na stronie harmonogramów](./media/how-to-create-schedules/select-current-schedule.png)
5. W oknie dialogowym **powtarzanie** wykonaj następujące czynności:
    1. Upewnij się, że dla **każdego tygodnia** jest ustawiona wartość pole **powtarzanie** . 
    2. Wybierz dni, w których harmonogram ma obowiązywać. W poniższym przykładzie wybrano Monday-Friday. 
    3. Wybierz **datę zakończenia** harmonogramu.
    8. Wybierz pozycję **Zapisz**. 

        ![Ustaw harmonogram powtarzania](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Teraz na stronie **Dodawanie zaplanowanego zdarzenia** dla **notatek (opcjonalnie)** wprowadź dowolny opis lub uwagi dotyczące harmonogramu. 
4. Na stronie **Dodawanie zaplanowanego zdarzenia** wybierz pozycję **Zapisz**. 

    ![Harmonogram tygodniowy](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Przejdź do daty rozpoczęcia w kalendarzu, aby sprawdzić, czy harmonogram został ustawiony.
    
    ![Zaplanuj w kalendarzu](./media/how-to-create-schedules/schedule-calendar.png)

    Aby uzyskać więcej informacji na temat tworzenia harmonogramów dla klasy i zarządzania nimi, zobacz [Tworzenie harmonogramu dla laboratoriów zajęć i zarządzanie nim](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Dodawanie użytkowników do laboratorium

1. W menu po lewej stronie wybierz pozycję **Użytkownicy**. Domyślnie włączona jest opcja **Ogranicz dostęp**. Gdy to ustawienie jest włączone, a użytkownik nie znajduje się na liście użytkowników, nie może zarejestrować się w laboratorium nawet przy użyciu linku rejestracji. Tylko użytkownicy na liście mogą zarejestrować się w laboratorium przy użyciu przesłanego linku rejestracji. Podczas tej procedury dodasz użytkowników do listy. Możesz też wyłączyć opcję **Ogranicz dostęp**, co umożliwi użytkownikom zarejestrowanie się w laboratorium, pod warunkiem, że mają link rejestracji. 
2. Wybierz pozycję **Dodaj użytkowników** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przy użyciu adresu e-mail**. 

    ![Przycisk Dodaj użytkowników](./media/how-to-configure-student-usage/add-users-button.png)
1. Na stronie **Dodawanie użytkowników** wpisz adresy e-mail użytkowników w osobnych wierszach lub w jednym wierszu, rozdzielając je średnikami. 

    ![Dodawanie adresów e-mail użytkowników](./media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Wybierz pozycję **Zapisz**. Na liście zostaną wyświetlone adresy e-mail użytkowników oraz ich stan rejestracji. 

    ![Lista użytkowników](./media/how-to-configure-student-usage/users-list-new.png)

    Nazwy użytkowników na liście zostaną wyświetlone po ich zarejestrowaniu do laboratorium. 
    

## <a name="send-invitation-emails-to-users"></a>Wyślij wiadomości e-mail z zaproszeniem do użytkowników

1. Przejdź do widoku **Użytkownicy** , jeśli nie masz już na stronie, a następnie wybierz pozycję **Zaproś wszystkie** na pasku narzędzi. 

    ![Wybierz uczniów](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Na stronie **wysyłanie zaproszenia** pocztą e-mail wprowadź opcjonalną wiadomość, a następnie wybierz pozycję **Wyślij**. Wiadomość e-mail automatycznie zawiera link rejestracji. Możesz uzyskać ten link rejestracji, wybierając pozycję **... (wielokropek)** na pasku narzędzi i w **łączu rejestracji**. 

    ![Wyślij link rejestracji za pośrednictwem poczty e-mail](./media/tutorial-setup-classroom-lab/send-email.png)
4. Stan **zaproszenia** zostanie wyświetlony na liście **Użytkownicy** . Stan powinien zostać zmieniony na **wysłanie** , a następnie do ** &lt; daty &gt; wysłania**. 

    Aby uzyskać więcej informacji na temat dodawania uczniów do klasy i zarządzania ich użyciem laboratorium, zobacz [jak skonfigurować użycie ucznia](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzysz laboratorium dla swojej klasy na platformie Azure. Aby dowiedzieć się, jak uczeń może uzyskać dostęp do maszyny wirtualnej w laboratorium przy użyciu linku rejestracyjnego, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z maszyną wirtualną w laboratorium używanym podczas zajęć](tutorial-connect-virtual-machine-classroom-lab.md)

