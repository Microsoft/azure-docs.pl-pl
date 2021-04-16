---
title: Zarządzanie laboratoriami w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak utworzyć i skonfigurować laboratorium na potrzeby zajęć, wyświetlić wszystkie laboratoria, udostępnić link rejestracji użytkownikowi laboratorium lub usunąć laboratorium.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c6acb9609abac15b9ff92250e3d5d44c585881cc
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481807"
---
# <a name="manage-labs-in-azure-lab-services"></a>Zarządzanie laboratoriami w Azure Lab Services 
W tym artykule opisano sposób tworzenia i usuwania laboratorium na potrzeby zajęć. Pokazano w nim również, jak wyświetlić wszystkie laboratoria na koncie laboratorium. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, musisz być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Właściciel laboratorium może dodać innych użytkowników do roli Twórca laboratorium, wykonując kroki opisane w następującym artykule: [Dodawanie użytkownika do roli twórcy laboratorium](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com).
1. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Wybierz lub wprowadź **identyfikator użytkownika,** który jest członkiem **roli** Twórca laboratorium na koncie laboratorium, a następnie wprowadź hasło. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
1. Wybierz **pozycję Nowe laboratorium.** 
    
    ![Tworzenie laboratorium na potrzeby zajęć](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** dla swojego laboratorium. 
    1. Wybierz **rozmiar maszyn wirtualnych potrzebnych** dla klasy. Aby uzyskać listę dostępnych rozmiarów, zobacz [sekcję Rozmiary maszyn](#vm-sizes) wirtualnych. 
    1. Wybierz obraz **maszyny wirtualnej,** którego chcesz użyć w laboratorium na potrzeby zajęć. Jeśli wybierzesz obraz systemu Linux, zobaczysz opcję włączenia połączenia **pulpitu zdalnego**. Aby uzyskać szczegółowe informacje, [zobacz Włączanie połączenia pulpitu zdalnego dla systemu Linux.](how-to-enable-remote-desktop-linux.md)

        Jeśli zalogowano się przy użyciu poświadczeń właściciela konta laboratorium, zobaczysz opcję włączenia większej liczby obrazów dla laboratorium. Aby uzyskać więcej informacji, zobacz Enable images at the time of lab creation (Włączanie [obrazów w czasie tworzenia laboratorium).](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)
    1. Przejrzyj **łączną cenę za godzinę** wyświetlaną na stronie. 
    1. Wybierz pozycję **Zapisz**.

        ![Zrzut ekranu przedstawiający okno "Nowe laboratorium".](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Jeśli konto laboratorium zostało skonfigurowane tak, aby zezwolić twórcy laboratorium na wybranie opcji lokalizacji laboratorium, jest dostępna opcja wyboru lokalizacji [laboratorium.](allow-lab-creator-pick-lab-location.md) 
4. Na stronie **Poświadczenia maszyny wirtualnej** określ domyślne poświadczenia dla wszystkich maszyn wirtualnych w laboratorium.
    1. Określ **nazwę użytkownika** dla wszystkich maszyn wirtualnych w laboratorium.
    2. Podaj **hasło** użytkownika. 

        > [!IMPORTANT]
        > Zanotuj nazwę użytkownika i hasło. Nie zostaną one ponownie wyświetlone.
    3. Wyłącz **opcję Użyj tego samego hasła dla wszystkich maszyn wirtualnych,** jeśli chcesz, aby uczniowie ustawiali własne hasła. Ten krok jest **opcjonalny.** 

        Nauczyciel może użyć tego samego hasła dla wszystkich maszyn wirtualnych w laboratorium lub zezwolić uczniom na ustawianie haseł dla swoich maszyn wirtualnych. Domyślnie to ustawienie jest włączone dla wszystkich obrazów systemów Windows i Linux z wyjątkiem systemu Ubuntu. Po wybraniu maszyny **wirtualnej** z systemem Ubuntu to ustawienie jest wyłączone, dlatego podczas pierwszego logowania uczniowie będą monitować o ustawienie hasła.  

        ![Okno nowego laboratorium](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Następnie wybierz pozycję **Dalej** na **stronie Poświadczenia maszyny wirtualnej.** 
5. Na stronie **Zasady laboratorium** wykonaj następujące czynności:
    1. Wprowadź liczbę godzin wyznaczoną dla każdego użytkownika **(limit** przydziału dla każdego użytkownika) poza zaplanowaną godziną laboratorium. 
    2. W przypadku **opcji Automatyczne zamykanie maszyn** wirtualnych określ, czy maszyna wirtualna ma być automatycznie wyłączana po rozłączeniu użytkownika. Można również określić, jak długo maszyna wirtualna powinna czekać na ponowne nawiązanie połączenia przez użytkownika przed jego automatycznie zamknięciem. Aby uzyskać więcej informacji, zobacz [Włączanie automatycznego zamykania maszyn wirtualnych po rozłączeniu](how-to-enable-shutdown-disconnect.md).
    3. Następnie wybierz pozycję **Finish** (Zakończ). 

        ![Limit przydziału dla każdego użytkownika](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Powinien zostać wyświetlony następujący ekran, który pokazuje stan tworzenia maszyny wirtualnej szablonu. Tworzenie szablonu w laboratorium trwa maksymalnie 20 minut. 

    ![Stan tworzenia maszyny wirtualnej szablonu](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na stronie **Szablon** wykonaj następujące kroki: Te kroki są **opcjonalne** w przypadku tego samouczka.

    1. Nawiąż połączenie z maszyną wirtualną szablonu, wybierając pozycję **Połącz**. Jeśli jest to maszyna wirtualna szablonu systemu Linux, możesz wybrać, czy chcesz nawiązać połączenie przy użyciu połączenia SSH, czy pulpitu zdalnego z graficznym interfejsem użytkownika.  Do korzystania z pulpitu zdalnego z graficznym interfejsem użytkownika jest wymagana dodatkowa konfiguracja. Aby uzyskać więcej informacji, zobacz [Włączanie graficznego pulpitu zdalnego](how-to-use-remote-desktop-linux-student.md) dla maszyn wirtualnych z systemem Linux.
    1. Wybierz **pozycję Resetuj** hasło, aby zresetować hasło maszyny wirtualnej. 
    1. Zainstaluj i skonfiguruj oprogramowania na maszynie wirtualnej szablonu. 
    1. **Zatrzymaj** maszynę wirtualną.  
    1. Wprowadź **opis** szablonu.
9.  Na **stronie** Szablon wybierz pozycję **Publikuj** na pasku narzędzi. 

    ![Przycisk Publikuj szablon](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Nie można cofnąć publikowania szablonu. 
10. Na stronie **Publikowanie szablonu** wprowadź liczbę maszyn wirtualnych, które chcesz utworzyć w laboratorium, a następnie wybierz pozycję **Opublikuj.** 

    ![Publikowanie szablonu — liczba maszyn wirtualnych](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Na stronie **zostanie wyświetlony stan** publikowania szablonu. Ten proces może potrwać do godziny. 

    ![Publikowanie szablonu — postęp](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Przejdź do strony **Pula maszyn wirtualnych,** wybierając pozycję Maszyny wirtualne w menu po lewej stronie lub wybierając kafelek Maszyny wirtualne. Sprawdź, czy są widoczne maszyny wirtualne o stanie **Nie przypisano**. Te maszyny wirtualne nie zostały jeszcze przypisane do uczniów. Powinny mieć stan **Zatrzymano**. Z poziomu tej strony możesz uruchomić maszynę wirtualną ucznia, połączyć się z maszyną wirtualną, zatrzymać maszynę wirtualną i usunąć maszynę wirtualną. Maszyny wirtualne możesz uruchomić na tej stronie lub pozwolić, aby zrobili to uczniowie. 

    ![Maszyny wirtualne w stanie Zatrzymano](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Na tej stronie są wykonywane następujące zadania (nie należy wykonywać tych kroków w tym samouczku). Te kroki mają na celu wyłącznie Twoje informacje): 
    
    1. Aby zmienić pojemność laboratorium (liczbę maszyn wirtualnych w laboratorium), wybierz pozycję **Pojemność laboratorium** na pasku narzędzi.
    2. Aby uruchomić wszystkie maszyny wirtualne jednocześnie, wybierz pozycję **Uruchom wszystkie** na pasku narzędzi. 
    3. Aby uruchomić określoną maszynę wirtualną, wybierz strzałkę w dół w obszarze **Stan,** a następnie wybierz pozycję **Uruchom.** Maszynę wirtualną można również uruchomić, wybierając maszynę wirtualną w pierwszej kolumnie, a następnie wybierając pozycję **Uruchom** na pasku narzędzi.                

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych  

| Rozmiar | Rdzenie | Pamięć RAM | Opis | 
| ---- | ----- | --- | ----------- | 
| Mały | 2 | 3,5 GB | Ten rozmiar najlepiej sprawdza się w przypadku wiersza polecenia, otwierania przeglądarki internetowej, serwerów internetowych o niskim natężeniu ruchu, małych i średnich baz danych. |
| Śred. | 4 | 7 GB | Ten rozmiar jest najbardziej odpowiedni dla relacyjnych baz danych, buforowania w pamięci i analizy | 
| Średni (wirtualizacja zagnieżdżona) | 4 | 16 GB | Ten rozmiar najlepiej nadaje się do relacyjnych baz danych, buforowania w pamięci i analizy. Ten rozmiar obsługuje również zagnieżdżone wirtualizacji. <p>Tego rozmiaru można używać w scenariuszach, w których każdy uczeń potrzebuje wielu maszyn wirtualnych. Nauczyciele mogą używać zagnieżdżonych wirtualizacji do skonfigurowania kilku zagnieżdżonych maszyn wirtualnych o małym rozmiarze wewnątrz maszyny wirtualnej. </p> |
| Mały procesor GPU (obliczenia) | 6 | 56 GB | <p>Ten rozmiar jest najbardziej odpowiedni dla aplikacji intensywnie wymagających obliczeń i intensywnie obciążanych siecią, takich jak aplikacje sztucznej inteligencji i uczenia głębokiego.</p><p>Azure Lab Services automatycznie instaluje i konfiguruje niezbędne sterowniki procesora GPU podczas tworzenia laboratorium z obrazami procesora GPU. </p> | 
| Mały procesor GPU (wizualizacja) | 6 | 56 GB | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier i kodowania przy użyciu platform takich jak OpenGL i DirectX. | 
| Duży | 8 | 16 GB | Ten rozmiar jest najbardziej odpowiedni dla aplikacji, które wymagają szybszych procesorów CPU, lepszej wydajności dysku lokalnego, dużych baz danych i dużych pamięci podręcznych. |
| Duże (wirtualizacja zagnieżdżona) | 8 | 32 GB | Ten rozmiar jest najbardziej odpowiedni dla aplikacji, które wymagają szybszych procesorów CPU, lepszej wydajności dysku lokalnego, dużych baz danych i dużych pamięci podręcznych. Ten rozmiar obsługuje również zagnieżdżone wirtualizacji. |  
| Średni procesor GPU (wizualizacja) | 12 | 112 GB | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier i kodowania przy użyciu platform takich jak OpenGL i DirectX. | 

> [!NOTE]
> Niektóre z tych rozmiarów maszyn wirtualnych mogą nie być na liście podczas tworzenia laboratorium na potrzeby zajęć. Lista jest wypełniana na podstawie bieżącej pojemności lokalizacji laboratorium. Jeśli twórca konta laboratorium umożliwia twórcom laboratorium wybranie lokalizacji laboratorium [,](allow-lab-creator-pick-lab-location.md)możesz spróbować wybrać inną lokalizację laboratorium i sprawdzić, czy rozmiar maszyny wirtualnej jest dostępny. 

## <a name="view-all-labs"></a>Wyświetlanie wszystkich laboratoriów

1. Przejdź do [Azure Lab Services portalu.](https://labs.azure.com)
1. Wybierz pozycję **Zaloguj się**. Wybierz lub wprowadź identyfikator **użytkownika,** który jest członkiem roli **Twórca** laboratorium na koncie laboratorium, a następnie wprowadź hasło. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 

    [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]
1. Upewnij się, że widzisz wszystkie laboratoria na wybranym koncie laboratorium. Na kafelku laboratorium zobaczysz liczbę maszyn wirtualnych w laboratorium i limit przydziału dla każdego użytkownika (poza zaplanowanym czasem).

    ![Wszystkie laboratoria](./media/how-to-manage-classroom-labs/all-labs.png)
1. Użyj listy rozwijanej u góry, aby wybrać inne konto laboratorium. Laboratoria są dostępne na wybranym koncie laboratorium. 

## <a name="delete-a-classroom-lab"></a>Usuwanie laboratorium na potrzeby zajęć

1. Na kafelku laboratorium wybierz pozycję trzy kropki (...) w rogu, a następnie wybierz pozycję **Usuń**. 

    ![Przycisk Usuń](./media/how-to-manage-classroom-labs/delete-button.png)
1. W **oknie dialogowym Usuwanie** laboratorium wybierz pozycję **Usuń,** aby kontynuować usuwanie. 

## <a name="switch-to-another-classroom-lab"></a>Przełączanie do innego laboratorium na potrzeby zajęć

Aby przełączyć się do innego laboratorium na potrzeby zajęć z bieżącego, wybierz listę rozwijaną laboratoriów na koncie laboratorium u góry.

![Wybierz laboratorium z listy rozwijanej u góry](./media/how-to-manage-classroom-labs/switch-lab.png)

Możesz również utworzyć nowe laboratorium przy użyciu nowego **laboratorium** na tej liście rozwijanej. 

> [!NOTE]
> Do zarządzania laboratoriami można również użyć modułu Az.LabServices programu PowerShell (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz stronę [główną Az.LabServices w witrynie GitHub.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)

Aby przełączyć się na inne konto laboratorium, wybierz menu rozwijane obok konta laboratorium i wybierz inne konto laboratorium. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium uzyskaj dostęp do laboratoriów](how-to-use-classroom-lab.md)

