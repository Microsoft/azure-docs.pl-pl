---
title: Zarządzanie laboratoriami klas w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak utworzyć i skonfigurować laboratorium zajęć, wyświetlić wszystkie laboratoria zajęć, udostępnić link rejestracji użytkownikowi laboratorium lub usunąć laboratorium.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 1a251f668508ee0b727c5e2ace65d1ea4eb5123e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91251681"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Zarządzanie pracownią w Azure Lab Services 
W tym artykule opisano sposób tworzenia i usuwania laboratorium zajęć. Przedstawiono w nim również sposób wyświetlania wszystkich laboratoriów zajęć na koncie laboratorium. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, musisz być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Właściciel laboratorium może dodać innych użytkowników do roli Twórca laboratorium, wykonując kroki opisane w następującym artykule: [Dodawanie użytkownika do roli twórcy laboratorium](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). Program Internet Explorer 11 nie jest jeszcze obsługiwany. 
1. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Wybierz lub wprowadź **Identyfikator użytkownika** , który jest członkiem roli **twórca laboratorium** na koncie laboratorium, a następnie wprowadź hasło. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
1. Wybierz pozycję **nowe laboratorium**. 
    
    ![Tworzenie laboratorium na potrzeby zajęć](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** dla swojego laboratorium. 
    1. Wybierz **rozmiar maszyn wirtualnych** , które są potrzebne dla klasy. Aby uzyskać listę dostępnych rozmiarów, zobacz sekcję [rozmiary maszyn wirtualnych](#vm-sizes) . 
    1. Wybierz **obraz maszyny wirtualnej** , który ma być używany dla laboratorium z klasą. W przypadku wybrania obrazu systemu Linux zostanie wyświetlona opcja **włączenia połączenia z pulpitem zdalnym**. Aby uzyskać szczegółowe informacje, zobacz [Włączanie usługi Podłączanie pulpitu zdalnego dla systemu Linux](how-to-enable-remote-desktop-linux.md).

        Jeśli zalogowano się przy użyciu poświadczeń właściciela konta laboratorium, zobaczysz opcję umożliwiającą włączenie większej liczby obrazów dla laboratorium. Aby uzyskać więcej informacji, zobacz [Włączanie obrazów podczas tworzenia laboratorium](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    1. Przejrzyj **łączną cenę za godzinę** wyświetlaną na stronie. 
    1. Wybierz pozycję **Zapisz**.

        ![Zrzut ekranu przedstawiający okno "nowe laboratorium".](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Zostanie wyświetlona opcja wyboru lokalizacji laboratorium, jeśli konto laboratorium zostało skonfigurowane tak, aby [umożliwić twórcę laboratorium wybranie opcji lokalizacji laboratorium](allow-lab-creator-pick-lab-location.md) . 
4. Na stronie **poświadczenia maszyny wirtualnej** Określ domyślne poświadczenia dla wszystkich maszyn wirtualnych w laboratorium.
    1. Określ **nazwę użytkownika** dla wszystkich maszyn wirtualnych w laboratorium.
    2. Podaj **hasło** użytkownika. 

        > [!IMPORTANT]
        > Zanotuj nazwę użytkownika i hasło. Nie zostaną one ponownie wyświetlone.
    3. Wyłącz opcję **Użyj tego samego hasła dla wszystkich maszyn wirtualnych** , jeśli chcesz, aby studenci ustawili swoje hasła. Ten krok jest **opcjonalny**. 

        Nauczycieli może wybrać użycie tego samego hasła dla wszystkich maszyn wirtualnych w laboratorium lub zezwolić uczniom na ustawianie haseł dla swoich maszyn wirtualnych. Domyślnie to ustawienie jest włączone dla wszystkich obrazów systemów Windows i Linux z wyjątkiem Ubuntu. W przypadku wybrania maszyny wirtualnej **Ubuntu** to ustawienie jest wyłączone, więc uczniowie będą monitowani o ustawienie hasła podczas pierwszego logowania.  

        ![Nowe okno laboratorium](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Następnie wybierz przycisk **dalej** na stronie **poświadczenia maszyny wirtualnej** . 
5. Na stronie **zasady laboratorium** wykonaj następujące czynności:
    1. Wprowadź liczbę godzin przypisanych dla każdego użytkownika (**limit przydziału dla każdego użytkownika**) poza zaplanowanym terminem dla laboratorium. 
    2. Dla opcji **automatycznego zamykania maszyn wirtualnych** Określ, czy maszyna wirtualna ma być automatycznie zamykana po rozłączeniu użytkownika. Możesz również określić, jak długo maszyna wirtualna ma czekać, aż użytkownik ponownie nawiąże połączenie przed automatycznym zamknięciem.. Aby uzyskać więcej informacji, zobacz [Włączanie automatycznego zamykania maszyn wirtualnych przy rozłączaniu](how-to-enable-shutdown-disconnect.md).
    3. Następnie wybierz pozycję **Finish** (Zakończ). 

        ![Przydział dla każdego użytkownika](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Powinien zostać wyświetlony następujący ekran pokazujący stan tworzenia szablonu maszyny wirtualnej. Tworzenie szablonu w laboratorium trwa maksymalnie 20 minut. 

    ![Stan tworzenia szablonu maszyny wirtualnej](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na stronie **szablon** wykonaj następujące czynności: te kroki są **opcjonalne** dla tego samouczka.

    1. Nawiąż połączenie z maszyną wirtualną szablonu, wybierając pozycję **Połącz**. Jeśli jest to maszyna wirtualna z szablonem systemu Linux, możesz wybrać, czy chcesz nawiązać połączenie za pomocą pulpitu zdalnego SSH, czy z graficznym interfejsem użytkownika.  Do korzystania z pulpitu zdalnego GUI jest wymagana dodatkowa konfiguracja. Aby uzyskać więcej informacji, zobacz [Włączanie graficznego pulpitu zdalnego dla maszyn wirtualnych z systemem Linux](how-to-use-remote-desktop-linux-student.md) .
    1. Wybierz pozycję **zresetuj hasło** , aby zresetować hasło dla maszyny wirtualnej. 
    1. Zainstaluj i skonfiguruj oprogramowania na maszynie wirtualnej szablonu. 
    1. **Zatrzymaj** maszynę wirtualną.  
    1. Wprowadź **opis** szablonu.
9.  Na stronie **szablon** wybierz pozycję **Publikuj** na pasku narzędzi. 

    ![Przycisk Publikuj szablon](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Nie można cofnąć publikowania szablonu. 
10. Na stronie **szablon publikacji** wprowadź liczbę maszyn wirtualnych, które chcesz utworzyć w laboratorium, a następnie wybierz pozycję **Publikuj**. 

    ![Szablon publikacji — liczba maszyn wirtualnych](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Zobaczysz **stan publikowania** szablonu na stronie. Ten proces może potrwać do godziny. 

    ![Publikowanie szablonu — postęp](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Przejdź do strony **puli maszyn wirtualnych** , wybierając pozycję Maszyny wirtualne w menu po lewej stronie lub wybierając pozycję kafelek maszyny wirtualne. Sprawdź, czy są widoczne maszyny wirtualne o stanie **Nie przypisano**. Te maszyny wirtualne nie zostały jeszcze przypisane do uczniów. Powinny mieć stan **Zatrzymano**. Z poziomu tej strony możesz uruchomić maszynę wirtualną ucznia, połączyć się z maszyną wirtualną, zatrzymać maszynę wirtualną i usunąć maszynę wirtualną. Maszyny wirtualne możesz uruchomić na tej stronie lub pozwolić, aby zrobili to uczniowie. 

    ![Maszyny wirtualne w stanie Zatrzymano](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Na tej stronie wykonaj następujące zadania (nie wykonuj tych kroków w samouczku. Te kroki dotyczą tylko Twoich informacji.): 
    
    1. Aby zmienić pojemność laboratorium (liczbę maszyn wirtualnych w laboratorium), wybierz pozycję **pojemność laboratorium** na pasku narzędzi.
    2. Aby jednocześnie uruchomić wszystkie maszyny wirtualne, wybierz pozycję **Rozpocznij wszystko** na pasku narzędzi. 
    3. Aby uruchomić konkretną maszynę wirtualną, wybierz strzałkę w dół w obszarze **stan**, a następnie wybierz polecenie **Uruchom**. Możesz również uruchomić maszynę wirtualną, wybierając maszynę wirtualną w pierwszej kolumnie, a następnie wybierając pozycję **Rozpocznij** na pasku narzędzi.                

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych  

| Rozmiar | Rdzenie | Pamięć RAM | Opis | 
| ---- | ----- | --- | ----------- | 
| Mała | 2 | 3,5 GB | Ten rozmiar najlepiej nadaje się w przypadku wiersza polecenia, otwierania przeglądarki sieci Web, serwerów sieci Web o małym ruchu, małych i średnich baz danych. |
| Średniaa | 4 | 7 GB | Ten rozmiar jest najlepiej dostosowany do relacyjnych baz danych, buforowania w pamięci i analiz | 
| Średni (Wirtualizacja zagnieżdżona) | 4 | 16 GB | Ten rozmiar jest najlepiej dostosowany do relacyjnych baz danych, buforowania w pamięci i analizy. Ten rozmiar obsługuje również wirtualizację zagnieżdżoną. <p>Tego rozmiaru można używać w scenariuszach, w których każdy student potrzebuje wielu maszyn wirtualnych. Wykładowcy mogą używać wirtualizacji zagnieżdżonej, aby skonfigurować kilka małych, zagnieżdżonych maszyn wirtualnych w ramach maszyny wirtualnej. </p> |
| Mały procesor GPU (obliczenia) | 6 | 56 GB | <p>Ten rozmiar najlepiej nadaje się w przypadku aplikacji intensywnie korzystających z mocy obliczeniowej i intensywnie korzystających z sieci, takich jak sztuczne inteligencje i aplikacje</p><p>Azure Lab Services automatycznie instaluje i konfiguruje niezbędne sterowniki procesora GPU podczas tworzenia laboratorium z obrazami procesora GPU. </p> | 
| Mały procesor GPU (wizualizacja) | 6 | 56 GB | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX. | 
| Duża | 8 | 16 GB | Ten rozmiar najlepiej nadaje się w przypadku aplikacji wymagających szybszych procesorów CPU, lepszej wydajności dysków lokalnych, dużych baz danych i dużych pamięci podręcznych pamięci. |
| Duże (Wirtualizacja zagnieżdżona) | 8 | 32 GB | Ten rozmiar najlepiej nadaje się w przypadku aplikacji wymagających szybszych procesorów CPU, lepszej wydajności dysków lokalnych, dużych baz danych i dużych pamięci podręcznych pamięci. Ten rozmiar obsługuje również wirtualizację zagnieżdżoną. |  
| Średni procesor GPU (wizualizacja) | 12 | 112 GB | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX. | 

> [!NOTE]
> Niektóre z tych rozmiarów maszyn wirtualnych mogą nie być widoczne na liście podczas tworzenia laboratorium zajęć. Lista jest wypełniana na podstawie bieżącej pojemności lokalizacji laboratorium. Jeśli twórca konta laboratorium [umożliwia twórcom laboratorium wybranie lokalizacji dla laboratorium](allow-lab-creator-pick-lab-location.md), możesz spróbować wybrać inną lokalizację dla laboratorium i sprawdzić, czy rozmiar maszyny wirtualnej jest dostępny. 


## <a name="view-all-classroom-labs"></a>Wyświetl wszystkie pracownice klasy
1. Przejdź do [portalu Azure Lab Services](https://labs.azure.com).
2. Wybierz pozycję **Zaloguj się**. Wybierz lub wprowadź **Identyfikator użytkownika** , który jest członkiem roli **twórca laboratorium** na koncie laboratorium, a następnie wprowadź hasło. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. Upewnij się, że wszystkie laboratoria zostały wyświetlone na wybranym koncie laboratorium. Na kafelku laboratorium zobaczysz liczbę maszyn wirtualnych w laboratorium i limit przydziału dla każdego użytkownika (poza zaplanowanym czasem).

    ![Wszystkie laboratoria](./media/how-to-manage-classroom-labs/all-labs.png)
3. Użyj listy rozwijanej u góry, aby wybrać inne konto laboratorium. Zobaczysz laboratoria na wybranym koncie laboratorium. 

## <a name="delete-a-classroom-lab"></a>Usuwanie laboratorium zajęć
1. Na kafelku laboratorium wybierz trzy kropki (...) w rogu, a następnie wybierz pozycję **Usuń**. 

    ![Przycisk Usuń](./media/how-to-manage-classroom-labs/delete-button.png)
3. W oknie dialogowym **usuwanie laboratorium** wybierz pozycję **Usuń** , aby kontynuować usuwanie. 

## <a name="switch-to-another-classroom-lab"></a>Przełącz do innego laboratorium zajęć
Aby przełączyć się do innego laboratorium zajęć z bieżącego, wybierz listę rozwijaną laboratoriów na koncie laboratorium w górnej części.

![Wybierz z listy rozwijanej Lab (laboratorium) w górnej części](./media/how-to-manage-classroom-labs/switch-lab.png)

Możesz również utworzyć nowe laboratorium przy użyciu **nowego laboratorium** z tej listy rozwijanej. 

> [!NOTE]
> Możesz również użyć polecenia AZ. LabServices PowerShell (wersja zapoznawcza), aby zarządzać laboratoriami. Aby uzyskać więcej informacji, zobacz [stronę główną AZ. LabServices w witrynie GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Aby przełączyć się na inne konto laboratorium, wybierz listę rozwijaną obok konta laboratorium i wybierz inne konto laboratorium. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)

