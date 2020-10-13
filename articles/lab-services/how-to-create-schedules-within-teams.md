---
title: Tworzenie harmonogramów Azure Lab Services w zespołach
description: Dowiedz się, jak tworzyć harmonogramy usług Lab w ramach zespołów.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 5578a2f63a4d6f58998a54560299a4356ed65997
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946749"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Tworzenie harmonogramów usług Lab i zarządzanie nimi w ramach zespołów

Harmonogramy umożliwiają skonfigurowanie laboratorium klasy w taki sposób, aby maszyny wirtualne w laboratorium były automatycznie uruchamiane i zamykane w określonym czasie. Można zdefiniować harmonogram jednorazowy lub cykliczny. Poniższe procedury umożliwiają tworzenie harmonogramów dla laboratorium zajęć i zarządzanie nimi: 

Oto, jak harmonogramy mają wpływ na maszyny wirtualne laboratorium: 

- Maszyna wirtualna szablonu nie jest dołączona do harmonogramów. 
- Uruchomiono tylko przypisane maszyny wirtualne. Oznacza to, że jeśli komputer nie zostanie przejęty przez użytkownika końcowego (student), maszyna nie zostanie uruchomiona w zaplanowanych godzinach. 
- Wszystkie maszyny wirtualne (bez względu na to, czy zostały przejęte przez użytkownika) są zatrzymane na podstawie harmonogramu laboratorium. 

> [!IMPORTANT]
> Zaplanowany czas działania maszyn wirtualnych nie jest uwzględniany w stosunku do przydziału przydzielonego dla użytkownika. Limit przydziału jest przeznaczony dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

## <a name="createeditdelete-a-schedule-for-the-lab"></a>Tworzenie/Edytowanie/usuwanie harmonogramu dla laboratorium

Utwórz zaplanowane zdarzenie dla laboratorium, aby maszyny wirtualne w laboratorium były automatycznie uruchamiane/zatrzymywane w określonych godzinach. Określony wcześniej limit przydziału użytkownika to dodatkowy czas przypisany do każdego użytkownika poza zaplanowanym czasem. 

1. Przejdź do strony **harmonogramy** , a następnie wybierz pozycję **Dodaj zaplanowane zdarzenie** na pasku narzędzi (u góry/lewo okna). 
1. Dla harmonogramu ustaw następujące parametry:
    1. Upewnij się, że w polu **Standardowy** wybrano **Typ zdarzenia**. Wybierz pozycję **Rozpocznij tylko** , aby określić tylko godzinę rozpoczęcia dla maszyn wirtualnych. Wybierz pozycję **Zatrzymaj tylko** , aby określić tylko czas zatrzymania dla maszyn wirtualnych. 
    1. Określ **datę rozpoczęcia**.
    1. Określ **godzinę rozpoczęcia** uruchamiania maszyn wirtualnych.
    1. Określ **czas zatrzymania** , w którym maszyny wirtualne mają zostać zamknięte. 
    1. Określ **strefę czasową** dla określonego czasu rozpoczęcia i zakończenia. 
    1. W sekcji **Repeat (Powtarzaj** ) zaznacz **co tydzień** lub **nigdy**. 
    1. W przypadku **notatek (opcjonalnie)** wprowadź dowolny opis lub uwagi dotyczące harmonogramu. 
1. Kliknij przycisk **Zapisz**. 

### <a name="view-schedules-in-calendar"></a>Wyświetl harmonogramy w kalendarzu

W kalendarzu widoczne są planowane daty i godziny. Wybierz przycisk **dzisiaj** w prawym górnym rogu, aby przełączyć się na bieżącą datę w kalendarzu. Wybierz **strzałkę w lewo** , aby przełączyć się do poprzedniego tygodnia i **strzałki w prawo** , aby przejść do następnego tygodnia w kalendarzu. 

### <a name="edit-a-schedule"></a>Edytowanie harmonogramu

Po wybraniu wyróżnionego harmonogramu w kalendarzu widoczne są przyciski umożliwiające **Edytowanie** lub **usuwanie** harmonogramu. 

Na stronie **Edytowanie zaplanowanego zdarzenia** możesz zaktualizować harmonogram i wybrać pozycję **Zapisz**. 

### <a name="delete-a-schedule"></a>Usuwanie harmonogramu

1. Aby usunąć harmonogram, wybierz wyróżniony harmonogram w kalendarzu, a następnie wybierz przycisk ikony kosza (Usuń):
1. W oknie dialogowym **usuwanie zaplanowanego zdarzenia** wybierz pozycję **tak** , aby potwierdzić usunięcie. 

## <a name="automatic-shutdown-and-disconnect-settings"></a>Ustawienia automatycznego zamykania i rozłączania

W górnej części strony zobaczysz link do ustawień **Autozamykania** .

Możesz włączyć kilka funkcji kontroli kosztów automatycznego zamykania, aby aktywnie uniknąć dodatkowych kosztów, gdy maszyny wirtualne nie są aktywnie używane. Kombinacja następujących trzech funkcji automatycznego zamykania i rozłączania przechwytuje większość przypadków, w których użytkownicy przypadkowo opuszczają maszyny wirtualne z systemem:
 
- Automatyczne rozłączanie użytkowników z maszyn wirtualnych uznawanych za bezczynne przez system operacyjny.
- Automatycznie zamykaj maszyny wirtualne podczas odłączania użytkowników.
- Automatycznie Zamykaj uruchomione maszyny wirtualne, ale użytkownicy nie łączą się.

Aby uzyskać więcej informacji, kliknij ikonę *informacji* obok pozycji Opcje w ustawieniach.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Korzystanie z Azure Lab Services w programie Teams — Omówienie](lab-services-within-teams-overview.md)
- [Rozpocznij pracę i Utwórz laboratorium usług Lab Services dla zespołów](how-to-get-started-create-lab-within-teams.md)
- [Zarządzanie listami użytkowników usług Lab Services z zespołów](how-to-manage-user-lists-within-teams.md)
- [Zarządzanie pulą maszyn wirtualnych w usługach Lab Services z zespołów](how-to-manage-vm-pool-within-teams.md)
- [Dostęp do maszyny wirtualnej (widoku ucznia) w usługach laboratoryjnych z zespołów](how-to-access-vm-for-students-within-teams.md)