---
title: Zarządzanie szablonem laboratorium klasy w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak utworzyć szablon laboratorium klasowego i zarządzać nim w Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be84a05fd8b278f5176cd768191d10a20fc719e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434281"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Tworzenie szablonu klasy i zarządzanie nim w Azure Lab Services
Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Możesz skonfigurować maszynę wirtualną szablonu dokładnie tak jak chcesz dla użytkowników laboratorium. Możesz podać nazwę i opis szablonu, które będą widoczne dla użytkowników laboratorium. Następnie należy opublikować szablon w celu udostępnienia dla użytkowników laboratorium wystąpień z szablonową maszyną wirtualną. Gdy opublikujesz szablon, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu tego szablonu. Liczba maszyn wirtualnych utworzonych w ramach tego procesu jest taka sama jak maksymalna liczba użytkowników, którzy mogą korzystać z laboratorium. Tę liczbę można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon.

W tym artykule opisano sposób tworzenia maszyny wirtualnej szablonu i zarządzania nią w laboratorium z klasą Azure Lab Services. 

> [!NOTE]
> Podczas tworzenia laboratorium jest tworzona maszyna wirtualna z szablonem, ale nie została ona uruchomiona. Można go uruchomić, nawiązać z nim połączenie i zainstalować wszystkie wstępnie wymagane oprogramowanie dla laboratorium, a następnie opublikować je. Po opublikowaniu szablonu maszyna wirtualna jest automatycznie zamykana, jeśli nie zostało to zrobione. 
> 
> Na maszynach wirtualnych szablonowych naliczane są **koszty** , dlatego należy się upewnić, że maszyna wirtualna jest ZAMKNIĘTA, gdy nie jest potrzebna do uruchomienia. 


## <a name="set-or-update-template-title-and-description"></a>Ustawianie lub aktualizowanie tytułu i opisu szablonu
Wykonaj następujące kroki, aby ustawić tytuł i opis po raz pierwszy, i zaktualizuj je później. 

1. Na stronie **szablon** wprowadź nowy **tytuł** dla laboratorium.  
2. Wprowadź nowy **Opis** szablonu. Gdy przenosisz fokus z pola tekstowego, zostanie ono automatycznie zapisane. 

    ![Nazwa i opis szablonu](./media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Aktualizowanie maszyny wirtualnej szablonu
Aby zaktualizować maszynę wirtualną szablonu, wykonaj następujące czynności.  

1. Zaczekaj na uruchomienie szablonu VM, a następnie wybierz pozycję **Połącz z szablonem** na pasku narzędzi, aby połączyć się z SZABLONową maszyną wirtualną, a następnie postępuj zgodnie z instrukcjami. Jeśli jest to maszyna z systemem Windows, zostanie wyświetlona opcja pobrania pliku RDP. 
1. Po nawiązaniu połączenia z szablonem i dokonaniu zmian nie będzie on już miał takiej samej konfiguracji jak maszyny wirtualne ostatnio opublikowane dla użytkowników. Zmiany szablonów nie zostaną odzwierciedlone na istniejących maszynach wirtualnych użytkowników do momentu ponownego opublikowania.

    ![Łączenie z maszyną wirtualną szablonu](./media/how-to-create-manage-template/connect-template-vm.png)
    
1. Zainstaluj wszelkie oprogramowanie wymagane, aby uczniowie mogli ukończyć laboratorium (na przykład program Visual Studio, Eksplorator usługi Azure Storage itp.). 
1. Zakończ połączenie (zamknij sesję pulpitu zdalnego) z maszyną wirtualną szablonu. 
1. **Zatrzymaj** maszynę wirtualną szablonu, wybierając pozycję **Zatrzymaj szablon**. 
1. Wykonaj kroki opisane w następnej sekcji, aby **opublikować** zaktualizowany szablon maszyny wirtualnej. 

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
## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium, dostęp do laboratoriów](how-to-use-classroom-lab.md)
