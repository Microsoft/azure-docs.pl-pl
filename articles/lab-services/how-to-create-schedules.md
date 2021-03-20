---
title: Tworzenie harmonogramu dla laboratoriów w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak tworzyć harmonogramy dla laboratoriów w Azure Lab Services, aby maszyny wirtualne w laboratoriach były uruchamiane i zamykane w określonym czasie.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2a827c3d9f3022cb7d27ee43c9c95227c44f97e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434029"
---
# <a name="create-and-manage-schedules-for-labs-in-azure-lab-services"></a>Twórz harmonogramy dla laboratoriów w Azure Lab Services i zarządzaj nimi 
Harmonogramy umożliwiają skonfigurowanie laboratorium klasy w taki sposób, aby maszyny wirtualne w laboratorium były automatycznie uruchamiane i zamykane w określonym czasie. Można zdefiniować harmonogram jednorazowy lub cykliczny. Poniższe procedury umożliwiają tworzenie harmonogramów dla laboratorium zajęć i zarządzanie nimi: 

> [!IMPORTANT]
> Zaplanowany czas działania maszyn wirtualnych nie jest uwzględniany w stosunku do [przydziału przydzielonego dla użytkownika](how-to-configure-student-usage.md#set-quotas-for-users). Limit przydziału jest przeznaczony dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

## <a name="set-a-schedule-for-the-lab"></a>Ustaw harmonogram dla laboratorium
Utwórz zaplanowane zdarzenie dla laboratorium, aby maszyny wirtualne w laboratorium były automatycznie uruchamiane/zatrzymywane w określonych godzinach. Określony wcześniej limit przydziału użytkownika to dodatkowy czas przypisany do każdego użytkownika poza zaplanowanym czasem. 

> [!NOTE]
> Zanim zaczniemy, Oto, jak harmonogramy mają wpływ na maszyny wirtualne laboratorium: 
>- Maszyna wirtualna szablonu nie jest dołączona do harmonogramów. 
>- Uruchomiono tylko przypisane maszyny wirtualne. Oznacza to, że jeśli komputer nie zostanie przejęty przez użytkownika końcowego (student), maszyna nie zostanie uruchomiona w zaplanowanych godzinach. 
>- Wszystkie maszyny wirtualne (bez względu na to, czy zostały przejęte przez użytkownika) są zatrzymane na podstawie harmonogramu laboratorium. 

1. Przejdź do strony **harmonogramy** , a następnie wybierz pozycję **Dodaj wydarzenie zaplanowane** na pasku narzędzi. 

    ![Zrzut ekranu przedstawiający stronę "Harmonogram" Azure Lab Services z wybranym przyciskiem Dodaj harmonogram.](./media/how-to-create-schedules/add-schedule-button.png)
2. Upewnij się, że w polu **Standardowy** wybrano **Typ zdarzenia**. Wybierz pozycję **Rozpocznij tylko** , aby określić tylko godzinę rozpoczęcia dla maszyn wirtualnych. Wybierz pozycję **Zatrzymaj tylko** , aby określić tylko czas zatrzymania dla maszyn wirtualnych. 
7. W sekcji **Repeat (powtarzanie** ) wybierz bieżący harmonogram. 

    ![Przycisk dodawania harmonogramu na stronie harmonogramów](./media/how-to-create-schedules/select-current-schedule.png)
5. W oknie dialogowym **powtarzanie** wykonaj następujące czynności:
    1. Upewnij się, że dla **każdego tygodnia** jest ustawiona wartość pole **powtarzanie** . 
    3. Określ **datę rozpoczęcia**.
    4. Określ **godzinę rozpoczęcia** uruchamiania maszyn wirtualnych.
    5. Określ **czas zatrzymania** , w którym maszyny wirtualne mają zostać zamknięte. 
    6. Określ **strefę czasową** dla określonego czasu rozpoczęcia i zakończenia. 
    2. Wybierz dni, w których harmonogram ma obowiązywać. W poniższym przykładzie wybrano Monday-Thursday. 
    8. Wybierz pozycję **Zapisz**. 

        ![Ustaw harmonogram powtarzania](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Teraz na stronie **Dodawanie zaplanowanego zdarzenia** dla **notatek (opcjonalnie)** wprowadź dowolny opis lub uwagi dotyczące harmonogramu. 
4. Na stronie **Dodawanie zaplanowanego zdarzenia** wybierz pozycję **Zapisz**. 

    ![Harmonogram tygodniowy](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Wyświetl harmonogramy w kalendarzu
W widoku kalendarza można zobaczyć zaplanowane daty i godziny, jak pokazano na poniższej ilustracji:

![Harmonogramy w widoku kalendarza](./media/how-to-create-schedules/schedules-calendar.png)

Wybierz przycisk **dzisiaj** w prawym górnym rogu, aby przełączyć się na bieżącą datę w kalendarzu. Wybierz **strzałkę w lewo** , aby przełączyć się do poprzedniego tygodnia i **strzałki w prawo** , aby przejść do następnego tygodnia w kalendarzu. 

## <a name="edit-a-schedule"></a>Edytowanie harmonogramu
Po wybraniu wyróżnionego harmonogramu w kalendarzu widoczne są przyciski umożliwiające **Edytowanie** lub **usuwanie** harmonogramu. 

![Edytuj stronę harmonogramu](./media/how-to-create-schedules/schedule-edit-button.png)

Na stronie **Edytowanie zaplanowanego zdarzenia** możesz zaktualizować harmonogram i wybrać pozycję **Zapisz**. 

## <a name="delete-a-schedule"></a>Usuwanie harmonogramu

1. Aby usunąć harmonogram, wybierz wyróżniony harmonogram w kalendarzu, a następnie wybierz przycisk ikony kosza (Usuń):

    ![Przycisk Usuń na pasku narzędzi](./media/how-to-create-schedules/schedule-delete-button.png)
2. W oknie dialogowym **usuwanie zaplanowanego zdarzenia** wybierz pozycję **tak** , aby potwierdzić usunięcie. 



## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As an admin, create and manage lab accounts (Tworzenie kont laboratoriów i zarządzanie nimi jako administrator)](how-to-manage-lab-accounts.md)
- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium, dostęp do laboratoriów](how-to-use-classroom-lab.md)
