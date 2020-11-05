---
title: Wprowadzenie do Azure Lab Services
description: W tym artykule opisano, jak rozpocząć pracę z Azure Lab Services.
ms.topic: article
ms.date: 10/02/2020
ms.openlocfilehash: 33e052931b0c3bd1bb1434b7eeefeed7a2a7ceab
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380291"
---
# <a name="get-started-with-lab-services"></a>Wprowadzenie do usług laboratoryjnych 

Jako student, możesz użyć Azure Lab Services, aby uzyskać dostęp do standardowych programów wymaganych przez programy badawcze na maszynach wirtualnych. 

Nauczyciele muszą wiedzieć, jak nauczyć się uczniów, aby mogli korzystać z Azure Lab Services w swojej instrukcji za poorednictwem sprzętu wystawionego od jednego do jednego studenta.

Ten artykuł zawiera informacje dotyczące personelu nauczania w zakresie uzyskiwania dostępu do Azure Lab Services i zarządzania nimi.

## <a name="overview"></a>Omówienie

Co to jest maszyna wirtualna i jak to działa?

Maszyna wirtualna (VM) to środowisko wirtualne, które działa jako komputer wirtualny. Maszyny wirtualne mają własny procesor, pamięć i magazyn. Maszyny wirtualne stanowią podstawę dla prawdziwej maszyny i mogą zapewnić użytkownikom dostęp do systemów operacyjnych i oprogramowania bez konieczności posiadania ich na własnym urządzeniu. Azure Lab Services zapewnia studentom dostęp do maszyn wirtualnych i nawigowanie po nich oraz do zarządzania nimi. 

Aby uzyskać więcej informacji, zobacz [Tworzenie laboratoriów zajęć i zarządzanie nimi](how-to-manage-classroom-labs.md).

## <a name="lab-dashboards"></a>Pulpity nawigacyjne laboratorium

Pulpity nawigacyjne dla laboratoriów stacjonarnych w Azure Lab Services zawierają migawkę różnych aspektów konkretnego laboratorium, w tym informacje o maszynie wirtualnej, liczbę przypisanych i nieprzypisanych maszyn wirtualnych, liczbę zarejestrowanych i niezarejestrowanego użytkownika oraz informacje o harmonogramach laboratorium. 

> [!NOTE]
> Chociaż większość aspektów administracyjnych pulpitu nawigacyjnego i [witryny sieci web Azure Lab Services](https://labs.azure.com/) będzie widoczna dla nauczycieli, uprawnienia specyficzne dla Twojej roli mogą mieć wpływ na możliwość modyfikowania określonych kryteriów na pulpicie nawigacyjnym. Jeśli wystąpi problem z konkretną konfigurację laboratorium, skontaktuj się z administratorem CTE.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="Portal Azure Lab Services":::

1. Przejdź do [witryny sieci Web usługi Azure Lab Services](https://labs.azure.com/)i zaloguj się.
1. Wybierz laboratorium.
1. Po lewej stronie okna zobaczysz **pulpit nawigacyjny** . Kliknij pozycję **pulpit nawigacyjny** , aby zobaczyć kilka kafelków na pulpicie nawigacyjnym.
1. Poniżej znajdują się kafelki z **Rozliczeniami &** , istnieją również kafelki dotyczące szablonów, pul maszyn wirtualnych, użytkowników i harmonogramów, które pozwalają modyfikować aspekty i wyświetlać więcej szczegółowych informacji na temat laboratorium zajęć.

    1. Szablon — opisuje datę utworzenia szablonu i ostatnią publikację. 
    1. Pula maszyn wirtualnych — liczba przypisanych i nieprzypisanych maszyn wirtualnych.
    1. Użytkownicy — liczba zarejestrowanych użytkowników i użytkowników, którzy zostali dodani do laboratorium, ale nie są zarejestrowani.
    1. Harmonogramy — wyświetla nadchodzące zaplanowane zdarzenia dla laboratorium oraz link, aby wyświetlić więcej zdarzeń.

Aby uzyskać więcej informacji, zobacz [Korzystanie z pulpitu nawigacyjnego](use-dashboard.md).

## <a name="quota-hours"></a>Godziny przydziału

Studenci mogą uzyskać dostęp do swoich maszyn wirtualnych w dowolnym momencie podczas zaplanowanej klasy czasu bez wpływu na ich godziny przydziału. Godziny przydziału są ustawiane dla całego semestru i określają liczbę godzin, przez studenta mogą korzystać z maszyny wirtualnej poza regularnie zaplanowanym czasem klasy.

8 godzin na tydzień, resetuje od niedzieli — nie skumulowany.

Aby uzyskać więcej informacji, zobacz [Ustawianie limitu przydziału](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="automatic-shut-down"></a>Automatyczne zamykanie

Aby pomóc w utrzymaniu kosztów i zaoszczędzeniu godzin przydziału dla uczniów, automatyczne zamykanie zostanie włączone dla naszych laboratoriów. Automatyczne zamykanie spowoduje wyłączenie maszyn wirtualnych po okresie braku aktywności (bez danych wejściowych myszy lub klawiatury). Automatyczne zamykanie działa w dwóch etapach, a pierwszy student zostanie odłączony od maszyny wirtualnej po okresie braku aktywności. W tym momencie maszyna wirtualna nadal **działa** i uczniowie mogą nawiązywać połączenia. Po rozłączeniu z innym okresem braku aktywności maszyna wirtualna zostanie zamknięta.

Automatyczne zamykanie jest ważnym narzędziem do oszczędzania kosztów, ale w związku z tym w przypadku zapisywania pracy i renderowania dużych plików projektu są one dostępne jako wyzwanie dla studentów. Jeśli uczniowie są często rozłączane, a maszyny wirtualne są zbyt szybko wyłączone. Skontaktuj się z administratorem CTE. 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie automatycznego zamykania maszyn wirtualnych dla konta laboratorium](how-to-configure-lab-accounts.md).

## <a name="managing-virtual-machines"></a>Zarządzanie maszynami wirtualnymi

Zarządzanie laboratorium umożliwia nauczycielom Sterowanie elementami, takimi jak pojemność laboratoryjna (liczba maszyn wirtualnych dostępnych dla studentów) oraz ręczne uruchamianie, zatrzymywanie lub resetowanie maszyn wirtualnych. Nauczyciele mogą również łączyć się z maszynami wirtualnymi, aby korzystać z interfejsu ucznia, uzyskiwać dostęp do plików i rozwiązywać problemy z oprogramowaniem lub maszyną wirtualną.

Najważniejszym znaczeniem do zapamiętania podczas zarządzania naszymi maszynami wirtualnymi jest to, że kiedykolwiek komputer jest **uruchomiony** , są ponoszone koszty nawet wtedy, gdy żaden z nich nie jest podłączony do maszyny wirtualnej.

### <a name="manually-starting-vms"></a>Ręczne uruchamianie maszyn wirtualnych

1. Na stronie **Pula maszyn wirtualnych** możesz uruchomić wszystkie maszyny wirtualne w laboratorium, klikając przycisk **Rozpocznij wszystko** w górnej części strony.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Uruchamianie maszyn wirtualnych":::
1. Poszczególne maszyny wirtualne można uruchomić, klikając przełącznik stanu. 

    Przełącznik zostanie odczytany, **rozpoczynając** od uruchomienia maszyny wirtualnej, a następnie **uruchomiona** po uruchomieniu maszyny wirtualnej.
1. Możesz również wybrać liczbę maszyn wirtualnych przy użyciu czeków po lewej stronie **nazwy** kolumny. 

    Po wybraniu żądanych maszyn wirtualnych kliknij przycisk **Start** w górnej części ekranu.
1. Po uruchomieniu można kliknąć przycisk **Zatrzymaj wszystko** , aby zatrzymać wszystkie maszyny wirtualne.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Zatrzymywanie maszyn wirtualnych":::

### <a name="stopping-and-resetting-vms"></a>Zatrzymywanie i resetowanie maszyn wirtualnych

* Można zatrzymać poszczególne maszyny wirtualne, klikając przełącznik stanu.
* Możesz również zatrzymać wiele maszyn wirtualnych, używając testów i klikając przycisk "Zatrzymaj" w górnej części ekranu.

Jeśli student ma problemy z nawiązywaniem połączenia z maszyną wirtualną lub maszyna wirtualna musi zostać zresetowana z dowolnego powodu, można użyć funkcji Reset.
1. Aby zresetować co najmniej jedną maszynę wirtualną, wybierz ją przy użyciu sprawdzenia, a następnie kliknij przycisk **Resetuj** w górnej części strony.
1. W oknie podręcznym kliknij pozycję **Zresetuj**.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="Zresetuj maszynę wirtualną":::

    > [!NOTE]
    > Włączenie maszyny wirtualnej ucznia nie wpłynie na przydział dla ucznia. Limity przydziału dla użytkowników określają liczbę godzin Lab dostępnych dla użytkownika poza zaplanowanym czasem klasy.

### <a name="connect-to-virtual-machines"></a>Połącz z Virtual Machines

Nauczyciele mogą łączyć się z maszyną wirtualną ucznia, o ile jest ona włączona, a student nie jest połączony z maszyną wirtualną. Łącząc się z maszyną wirtualną, będziesz mieć dostęp do plików lokalnych na maszynie wirtualnej i pomóc uczniom w rozwiązywaniu problemów.

1. Aby nawiązać połączenie z maszyną wirtualną ucznia, umieść wskaźnik myszy na maszynie wirtualnej na liście, a następnie kliknij przycisk **Połącz** . 
1. Następnie postępuj zgodnie z przewodnikiem wprowadzającym dla uczniów dla Chromebooks, Mac lub komputerów

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Przycisk połączenia z maszyną wirtualną ucznia":::

## <a name="add-and-manage-lab-users"></a>Dodawanie użytkowników laboratorium i zarządzanie nimi

Nauczyciele mogą dodawać użytkowników uczniów do laboratorium i monitorować ich limity godzinowe. 

### <a name="add-users-by-email-address"></a>Dodaj użytkowników według adresu e-mail

1. W [witrynie sieci Web usługi Azure Lab Services](https://labs.azure.com/) kliknij pozycję **Użytkownicy** po lewej stronie okna.
1. W górnej części okna kliknij pozycję **Dodaj użytkowników** i wybierz pozycję **Dodaj przy użyciu adresu e-mail**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-button.png" alt-text="Przycisk &quot;Dodaj użytkowników&quot;":::
1. W okienku **Dodaj użytkowników** , które pojawia się po prawej stronie, wprowadź adresy e-mail uczniów w oddzielnych wierszach lub w pojedynczym wierszu oddzielone średnikami.
1. Kliknij przycisk **Zapisz**.

    :::image type="content" source="./media/get-started-manage-labs/add-students.png" alt-text="Dodawanie uczniów do laboratorium":::
1. Lista użytkowników zostanie teraz zaktualizowana o wiadomości e-mail, status, zaproszenie i godziny przydziału.

    Po zarejestrowaniu uczniów dla laboratorium, ich nazwy zostaną zaktualizowane przy użyciu imion i nazwisk z katalogu MP.

    > [!NOTE]
    > Opcja Włącz opcję Ogranicz dostęp jest włączona dla użytkowników. Oznacza to, że tylko użytkownicy z listą mogą zarejestrować się w laboratorium przy użyciu wysyłanego linku rejestracji.

### <a name="add-users-using-a-spreadsheet"></a>Dodawanie użytkowników przy użyciu arkusza kalkulacyjnego 

Możesz również dodać użytkowników, przekazując plik CSV zawierający ich adresy e-mail.

1. W programie Microsoft Excel Utwórz plik CSV, który zawiera listę adresów e-mail uczniów w jednej kolumnie.
1. W [witrynie sieci web Azure Lab Services](https://labs.azure.com/)w górnej części strony **Użytkownicy** kliknij przycisk **Dodaj użytkowników** .
1. Wybierz pozycję **Przekaż wolumin CSV**.
1. Wybierz plik CSV zawierający adresy e-mail uczniów, a następnie kliknij przycisk **Otwórz**.

    :::image type="content" source="./media/get-started-manage-labs/add-users-spreadsheet.png" alt-text="Dodawanie użytkowników przy użyciu arkusza kalkulacyjnego":::
1. Wiadomości e-mail będą teraz wyświetlane w oknie po prawej stronie. Kliknij przycisk **Zapisz**.

    :::image type="content" source="./media/get-started-manage-labs/register-users.png" alt-text="Rejestrowanie użytkowników":::

### <a name="register-users"></a>Rejestrowanie użytkowników

Po dodaniu użytkowników do laboratorium konieczne będzie zarejestrowanie się w celu uzyskania dostępu do maszyn wirtualnych. Można to zrobić przez Zapraszanie użytkowników z portalu usług sieci Web platformy Azure, który wyśle wiadomość e-mail zawierającą link rejestracji dla laboratorium. Lub przez skopiowanie i wklejenie linku rejestracji do wiadomości e-mail lub innej formy komunikacji z uczniami.

1. Na stronie **Użytkownicy** wybierz studenta lub wielu uczniów z listy.

    W wierszu wybranego ucznia wybierz ikonę koperty na liście lub kliknij pozycję **Zaproś** w górnej części ekranu.

    :::image type="content" source="./media/get-started-manage-labs/send-invitation.png" alt-text="Wysyłanie zaproszenia":::
    
    W oknie **wysyłanie zaproszenia** pocztą e-mail wprowadź opcjonalny komunikat (na przykład nazwę użytkownika i hasło) do uczniów, a następnie kliknij przycisk **Wyślij**. 
    
    :::image type="content" source="./media/get-started-manage-labs/send-invitation-mail.png" alt-text="Wyślij zaproszenie pocztą e-mail":::

    Alternatywnie, z tej samej strony **Użytkownicy** , można kliknąć przycisk **rejestracja** w górnej części ekranu. 

    :::image type="content" source="./media/get-started-manage-labs/registration-link.png" alt-text="Link do rejestracji użytkownika":::
    
    Skopiuj link rejestracji z pola tekstowego i wklej go do poczty e-mail lub preferowanego narzędzia do bezpiecznej obsługi komunikatów.  
    
    :::image type="content" source="./media/get-started-manage-labs/user-registration.png" alt-text="Wyślij rejestrację użytkownika":::

Po zaproszeniu użytkowników za pomocą Azure Portal lub udostępnieniu linku będzie można monitorować użytkowników, którzy zostali pomyślnie zarejestrowani na stronie **Użytkownicy** w kolumnie **stan** . 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal używać zasobów utworzonych w tym przewodniku Szybki Start, Usuń zasoby.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)