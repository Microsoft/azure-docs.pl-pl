---
title: Konfigurowanie ustawień użycia w laboratoriach Azure Lab Services
description: Dowiedz się, jak skonfigurować liczbę studentów dla laboratorium, zarejestrować je w laboratorium, kontrolować liczbę godzin, przez które mogą korzystać z maszyny wirtualnej, i nie tylko.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: e768c74d338cf21eb56660fe3790fc1f0f3ec80d
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434553"
---
# <a name="add-and-manage-lab-users"></a>Dodawanie użytkowników laboratorium i zarządzanie nimi

W tym artykule opisano sposób dodawania użytkowników uczniów do laboratorium, rejestrowania ich w laboratorium, kontrolowania liczby dodatkowych godzin, w których mogą korzystać z maszyny wirtualnej (VM) i nie tylko. 

## <a name="add-users-to-a-lab"></a>Dodawanie użytkowników do laboratorium

W tej sekcji dodasz uczniów do laboratorium ręcznie lub przez przekazanie pliku CSV. Wykonaj następujące czynności:

1. W lewym okienku wybierz pozycję **Użytkownicy**. 

    Domyślnie opcja **Ogranicz dostęp** jest włączona i, chyba że znajdują się na liście użytkowników, uczniowie nie mogą zarejestrować się w laboratorium, nawet jeśli mają link do rejestracji. Tylko użytkownicy z listy mogą rejestrować się w laboratorium przy użyciu wysyłanego linku rejestracji. Podczas tej procedury dodasz użytkowników do listy. Alternatywnie można wyłączyć opcję **Ogranicz dostęp**, dzięki czemu uczniowie mogą zarejestrować się w laboratorium, o ile mają link do rejestracji. 

1. W górnej części okienka **Użytkownicy** wybierz pozycję **Dodaj użytkowników**, a następnie wybierz pozycję **Dodaj przy użyciu adresu e-mail**. 

    ![Przycisk "Dodaj użytkowników"](./media/how-to-configure-student-usage/add-users-button.png)

1. W okienku **Dodaj użytkowników** wprowadź adresy e-mail uczniów w osobnych wierszach lub w pojedynczej linii oddzielone średnikami. 

    ![Dodawanie adresów e-mail użytkowników](./media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Wybierz pozycję **Zapisz**. 

    Na liście zostaną wyświetlone adresy e-mail i Stany bieżących użytkowników, bez względu na to, czy są one zarejestrowane w laboratorium, czy nie. 

    ![Lista użytkowników](./media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Gdy uczniowie zostaną zarejestrowani w laboratorium, na liście zostaną wyświetlone ich nazwy. Nazwa wyświetlana na liście jest zbudowana przy użyciu imion i nazwisk uczniów w Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Dodawanie użytkowników przez przekazanie pliku CSV

Możesz również dodać użytkowników, przekazując plik CSV zawierający ich adresy e-mail. 

Plik tekstowy CSV służy do przechowywania danych tabelarycznych rozdzielanych przecinkami (CSV) (liczb i tekstu). Zamiast przechowywania informacji w polach kolumny (na przykład w arkuszach kalkulacyjnych), plik CSV przechowuje informacje oddzielone przecinkami. Każdy wiersz w pliku CSV będzie mieć taką samą liczbę pól "oddzielonych przecinkami". Program Excel umożliwia łatwe tworzenie i edytowanie plików CSV.

1. W programie Microsoft Excel Utwórz plik CSV, który zawiera listę adresów e-mail uczniów w jednej kolumnie.

    ![Lista użytkowników w pliku CSV](./media/how-to-configure-student-usage/csv-file-with-users.png)

1. W górnej części okienka **Użytkownicy** wybierz pozycję **Dodaj użytkowników**, a następnie wybierz pozycję **Przekaż plik CSV**.

    ![Przycisk "Przekaż wolumin CSV"](./media/how-to-configure-student-usage/upload-csv-button.png)

1. Wybierz plik CSV zawierający adresy e-mail uczniów, a następnie wybierz pozycję **Otwórz**.

    W oknie **Dodawanie użytkowników** zostanie wyświetlona lista adresów e-mail z pliku CSV. 

    ![Okno "Dodawanie użytkowników" z adresami e-mail z pliku CSV](./media/how-to-configure-student-usage/add-users-window.png)

1. Wybierz pozycję **Zapisz**. 

1. W okienku **Użytkownicy** Wyświetl listę dodanych uczniów. 

    ![Lista dodanych użytkowników w okienku "Użytkownicy"](./media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Wyślij zaproszenia do użytkowników

Aby wysłać link rejestracji do nowych użytkowników, należy użyć jednej z następujących metod. 

Jeśli opcja **Ogranicz dostęp** jest włączona dla laboratorium, do zarejestrowania się w laboratorium będzie można użyć linku rejestracji. Ta opcja jest domyślnie włączona. 

### <a name="invite-all-users"></a>Zapraszanie wszystkich użytkowników

Ta metoda pokazuje, jak wysyłać wiadomości e-mail za pomocą linku rejestracji i opcjonalnej wiadomości do wszystkich uczniów z listy.

1. W okienku **Użytkownicy** wybierz pozycję **Zaproś wszystkie**. 

    ![Przycisk "Zaproś wszystkie"](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. W oknie **wysyłanie zaproszenia** pocztą e-mail wprowadź opcjonalny komunikat, a następnie wybierz pozycję **Wyślij**. 

    Wiadomość e-mail automatycznie zawiera link rejestracji. Aby pobrać i zapisać łącze rejestracji oddzielnie, wybierz wielokropek (**...**) w górnej części okienka **Użytkownicy** , a następnie wybierz pozycję link do **rejestracji**. 

    ![Okno "wysyłanie linku do rejestracji za pośrednictwem poczty e-mail"](./media/tutorial-setup-classroom-lab/send-email.png)

    Kolumna **zaproszenie** na liście **Użytkownicy** wyświetla stan zaproszenia dla każdego dodanego użytkownika. Stan powinien zostać zmieniony na **wysłanie** , a następnie do **\<date> wysłania**. 

### <a name="invite-selected-users"></a>Zaproś wybranych użytkowników

Ta metoda pokazuje, jak zaprosić tylko wybranych uczniów i uzyskać link do rejestracji, który można udostępniać innym osobom.

1. W okienku **Użytkownicy** wybierz studenta lub wielu uczniów z listy. 

1. W wierszu wybranego ucznia wybierz ikonę **koperty** lub na pasku narzędzi wybierz pozycję **Zaproś**. 

    ![Zaproś wybranych użytkowników](./media/how-to-configure-student-usage/invite-selected-users.png)

1. W oknie **wysyłanie zaproszenia** pocztą e-mail wprowadź opcjonalny **komunikat**, a następnie wybierz pozycję **Wyślij**. 

    ![Wyślij wiadomość e-mail do wybranych użytkowników](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    W okienku **Użytkownicy** jest wyświetlany stan tej operacji w kolumnie **zaproszenie** tabeli. Wiadomość e-mail z zaproszeniem zawiera link rejestracji, którego uczniowie mogą użyć do zarejestrowania się w laboratorium.

## <a name="get-the-registration-link"></a>Pobierz łącze rejestracji

W tej sekcji można uzyskać link rejestracji z portalu i wysłać go przy użyciu własnej aplikacji poczty e-mail. 

1. W okienku **Użytkownicy** wybierz pozycję **link do rejestracji**.

    ![Link rejestracji ucznia](./media/how-to-configure-student-usage/registration-link-button.png)

1. W oknie **Rejestracja użytkownika** wybierz pozycję **Kopiuj**, a następnie wybierz pozycję **gotowe**. 

    ![Okno "Rejestracja użytkownika"](./media/how-to-configure-student-usage/registration-link.png)

    Link zostanie skopiowany do Schowka. 
    
1. W aplikacji poczty e-mail wklej link rejestracji, a następnie Wyślij wiadomość e-mail do ucznia, aby student mógł zarejestrować się dla klasy. 

## <a name="view-registered-users"></a>Wyświetl zarejestrowanych użytkowników

1. Przejdź do witryny sieci Web [Azure Lab Services](https://labs.azure.com) . 
1. Wybierz pozycję **Zaloguj**, a następnie wprowadź swoje poświadczenia. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft.
1. Na stronie **Moje laboratoria** wybierz laboratorium, którego użycie ma być śledzone. 
1. W lewym okienku wybierz pozycję **Użytkownicy** lub wybierz kafelek **Użytkownicy** . 

    W okienku **Użytkownicy** zostanie wyświetlona lista studentów, którzy zostali zarejestrowani w laboratorium.  

    ![Lista zarejestrowanych użytkowników](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Ustawianie przydziałów dla użytkowników

Możesz ustawić limit przydziału godzin dla każdego ucznia, wykonując następujące czynności: 

1. W okienku **Użytkownicy** wybierz pozycję **przydział na użytkownika: \<number> godziny** na pasku narzędzi. 
1. W oknie **przydział dla użytkownika** Określ liczbę godzin, które chcesz nadać każdemu uczniowi poza zaplanowanym czasem klasy, a następnie wybierz pozycję **Zapisz**.

    ![Okno "przydziały na użytkownika"](./media/how-to-configure-student-usage/quota-per-user.png)    

    Zmienione wartości są teraz wyświetlane **\<number of hours> na pasku narzędzi** i na liście Użytkownicy, jak pokazano poniżej:

    ![Liczba godzin przydziału na użytkownika](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Zaplanowana godzina działania maszyn wirtualnych](how-to-create-schedules.md) nie jest zgodna z limitem przydziału przydzielonym uczniom. Przydział dotyczy czasu poza godzinami zaplanowanymi, które student spędza na maszynach wirtualnych. 

## <a name="set-additional-quotas-for-specific-users"></a>Ustawianie dodatkowych przydziałów dla określonych użytkowników

Można określić limity przydziału dla niektórych uczniów wykraczających poza typowe limity przydziału, które zostały ustawione dla wszystkich użytkowników w poprzedniej sekcji. Jeśli na przykład jako instruktora ustawisz limit przydziału dla wszystkich uczniów na 10 godzin i ustawisz dodatkowy limit przydziału wynoszący 5 godzin dla określonego ucznia, Student uzyska 15 (10 + 5) godzin przydziału. Jeśli zmienisz wspólny limit przydziału później na, powiedzmy 15, Student uzyska 20 (15 + 5) godzin przydziału. Należy pamiętać, że ten całkowity limit przydziału jest poza zaplanowanym terminem. Czas spędzony przez student na maszynie wirtualnej laboratorium w trakcie zaplanowanego czasu nie jest liczony do tego przydziału. 

Aby ustawić dodatkowe przydziały, wykonaj następujące czynności:

1. W okienku **Użytkownicy** wybierz student z listy, a następnie wybierz pozycję **Dostosuj przydział** na pasku narzędzi. 

    ![Przycisk "Dopasuj przydział"](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. W polu **Dostosuj limit przydziału \<selected user or users email address> dla** wprowadź liczbę dodatkowych godzin laboratorium, które chcesz przyznać wybranym uczniom lub uczniom, a następnie wybierz pozycję **Zastosuj**. 

    !["Dopasuj przydział..." Dział](./media/how-to-configure-student-usage/additional-quota.png)

    W kolumnie **użycie** zostanie wyświetlony zaktualizowany przydział dla wybranych uczniów. 

    ![Nowe użycie dla użytkownika](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Konta uczniów

Aby dodać uczniów do laboratorium zajęć, użyj swoich kont e-mail. Studenci mogą mieć następujące typy kont e-mail:

- Konto e-mail ucznia udostępniane przez wystąpienie Azure Active Directory University.
- Konto e-mail w domenie firmy Microsoft, takie jak *Outlook.com*, *hotmail.com*, *MSN.com* lub *Live.com*.
- Konto e-mail inne niż firmy Microsoft, na przykład udostępniane przez usługi Yahoo! lub Google. Jednak te typy kont muszą być połączone z konto Microsoft.
- Konto usługi GitHub. To konto musi być połączone z konto Microsoft.

### <a name="use-a-non-microsoft-email-account"></a>Użyj konta e-mail innego niż Microsoft

Studenci mogą korzystać z kont e-mail innych niż firmy Microsoft w celu zarejestrowania i zalogowania się do laboratorium zajęć.  Rejestracja wymaga jednak najpierw utworzenia konto Microsoft, która jest połączona ze swoim adresem e-mail firmy innej niż Microsoft.

Wielu uczniów może już mieć konto Microsoft, które są połączone ze swoimi adresami e-mail spoza firmy Microsoft. Na przykład studenci mają już konto Microsoft, jeśli używały swojego adresu e-mail z innymi produktami lub usługami firmy Microsoft, takimi jak Office, Skype, OneDrive lub Windows.  

Gdy studenci używają linku rejestracji do zalogowania się do klasy, zostanie wyświetlony monit o podanie adresu e-mail i hasła. Studenci, którzy próbują zalogować się przy użyciu niekonto Microsoft, które nie są połączone z konto Microsoft, otrzymają następujący komunikat o błędzie: 

![Komunikat o błędzie podczas logowania](./media/how-to-configure-student-usage/cant-find-account.png)

Oto link dla uczniów, aby [zarejestrować się w celu uzyskania konto Microsoft](http://signup.live.com).  

> [!IMPORTANT]
> Gdy uczniowie logują się do laboratorium z klasą, nie będą mieć opcji tworzenia konto Microsoft. Z tego powodu zaleca się dołączenie tego linku do http://signup.live.com rejestracji, w wiadomości e-mail z rejestracją w laboratorium zajęć, która jest wysyłana do uczniów, którzy korzystają z kont innych niż Microsoft.

### <a name="use-a-github-account"></a>Korzystanie z konta usługi GitHub

Studenci mogą także użyć istniejącego konta usługi GitHub do zarejestrowania się i zalogować się do laboratorium zajęć. Jeśli masz już konto Microsoft połączony z kontem usługi GitHub, uczniowie mogą się zalogować i podać hasło, jak pokazano w poprzedniej sekcji. 

Jeśli konto usługi GitHub nie zostało jeszcze połączone z konto Microsoft, można wykonać następujące czynności:

1. Wybierz łącze **Opcje logowania** , jak pokazano poniżej:

    ![Łącze "Opcje logowania"](./media/how-to-configure-student-usage/signin-options.png)

1. W oknie **Opcje logowania** wybierz pozycję **Zaloguj się za pomocą usługi GitHub**.

    ![Link "Zaloguj się za pomocą usługi GitHub"](./media/how-to-configure-student-usage/signin-github.png)

    Po wyświetleniu monitu uczniowie tworzą konto Microsoft, które są połączone ze swoim kontem usługi GitHub. Łączenie odbywa się automatycznie po wybraniu **pozycji dalej**. Są one następnie natychmiast zalogowane i połączone z laboratorium klas.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Eksportowanie listy użytkowników do pliku CSV

1. Przejdź do okienka **Użytkownicy** .
1. Na pasku narzędzi wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Eksportuj plik CSV**. 

    ![Przycisk "Eksportuj plik CSV"](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- Dla administratorów: [Tworzenie kont laboratorium i zarządzanie nimi](how-to-manage-lab-accounts.md)
- Dla właścicieli laboratorium: [Tworzenie i Zarządzanie laboratoriami](how-to-manage-classroom-labs.md) oraz [Konfigurowanie i publikowanie szablonów](how-to-create-manage-template.md)
- Dla użytkowników laboratorium: [laboratoria dostępu](how-to-use-classroom-lab.md)
