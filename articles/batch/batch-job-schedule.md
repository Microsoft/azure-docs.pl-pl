---
title: Planowanie zadań
description: Użyj planowania zadań, aby zarządzać zadaniami.
ms.topic: how-to
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: 7da3c78e00f5d7e41a5396603cf4885a50cb6e5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89146355"
---
# <a name="schedule-jobs-for-efficiency"></a>Planowanie zadań pod kątem wydajności

Planowanie zadań wsadowych umożliwia określenie priorytetów zadań, które mają być uruchamiane w pierwszej kolejności, przy jednoczesnym uwzględnieniu zadań, które mają zależności od innych zadań. Planując zadania, możesz mieć pewność, że używasz najmniejszej ilości zasobów. Węzły mogą zostać zlikwidowane, gdy nie jest to potrzebne, zadania, które są zależne od innych zadań, są przypadające niemal w czasie, optymalizując przepływy pracy. Wykonywane jest tylko jedno zadanie w czasie. Nowy nie zostanie uruchomiony do momentu ukończenia poprzedniej. Można ustawić Autouzupełnianie dla zadania. 

## <a name="benefit-of-job-scheduling"></a>Korzyść w planowaniu zadań

Korzystanie z planowania zadań polega na tym, że można określić harmonogram tworzenia zadań. Zadania zaplanowane przy użyciu zadania Menedżera zadań są skojarzone z zadaniem. Zadanie Menedżera zadań spowoduje utworzenie zadań dla tego zadania. W tym celu zadanie Menedżera zadań musi być uwierzytelniane przy użyciu konta wsadowego. Użyj AZ_BATCH_AUTHENTICATION_TOKEN tokenu dostępu. Token zezwoli na dostęp do pozostałej części zadania. 

## <a name="use-the-portal-to-schedule-a-job"></a>Planowanie zadania przy użyciu portalu

   1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

   2. Wybierz konto wsadowe, w którym chcesz zaplanować zadania.

   3. Wybierz pozycję **Dodaj** , aby utworzyć nowy harmonogram zadań i wypełnij **formularz podstawowy**.



![Zaplanuj zadanie][1]

**Identyfikator harmonogramu zadań**: unikatowy identyfikator tego harmonogramu zadań.

**Nazwa wyświetlana**: Nazwa wyświetlana zadania nie musi być unikatowa, ale ma maksymalną długość 1024 znaków.

**Nie uruchamiaj do**: określa najwcześniejszy czas uruchomienia zadania. Jeśli ta wartość nie zostanie ustawiona, harmonogram będzie gotowy do natychmiastowego uruchamiania zadań.

**Nie uruchamiaj po**: żadne zadania nie są uruchamiane po upływie określonego czasu. Jeśli nie określisz czasu, tworzysz cykliczny harmonogram zadań, który pozostaje aktywny, dopóki nie zostanie on jawnie zamknięty.

**Interwał cyklu**: można określić ilość czasu między zadaniami. Możesz mieć tylko jedno zadanie w określonym czasie, więc jeśli jest to czas, aby utworzyć nowe zadanie w ramach harmonogramu zadań, ale poprzednie zadanie jest nadal uruchomione, usługa Batch nie utworzy nowego zadania, dopóki nie zakończy się poprzednie zadanie.  

**Okno uruchamiania**: w tym miejscu należy określić interwał czasu, rozpoczynając od momentu, w którym harmonogram wskazuje, że zadanie powinno zostać utworzone. Jeśli bieżące zadanie nie zakończy się w oknie, następne zadanie nie zostanie uruchomione.

W dolnej części formularza Basic określisz pulę, w której ma zostać uruchomione zadanie. Aby znaleźć informacje o IDENTYFIKATORze puli, wybierz pozycję **Aktualizuj**. 

![Określ pulę][2]


**Identyfikator puli**: Zidentyfikuj pulę, w której zostanie uruchomione zadanie.

**Zadanie konfiguracji zadania**: wybierz opcję **Aktualizuj** , aby nazwać zadanie Menedżera zadań oraz zadania przygotowania i zwolnienia zadania, jeśli są używane.

**Priorytet**: nadaj zadanie priorytetu.

**Maksymalny czas zegara ściany**: Ustaw maksymalną ilość czasu, przez jaką zadanie może zostać uruchomione. Jeśli nie zakończy się w przedziale czasowym, partia kończy zadanie. Jeśli nie ustawisz tej opcji, nie ma limitu czasu dla tego zadania.

**Maksymalna liczba ponownych prób wykonania zadania**: Określ, ile razy można ponowić próbę wykonania zadania maksymalnie cztery razy. Ta wartość nie jest taka sama jak liczba ponownych prób wywołania interfejsu API.

**Po zakończeniu wszystkich zadań**: wartość domyślna nie jest akcją.

**Gdy zadanie nie powiedzie się**: domyślnie nie ma żadnej akcji. Zadanie kończy się niepowodzeniem, jeśli liczba ponownych prób jest wyczerpana lub wystąpił błąd podczas uruchamiania zadania. 

Po wybraniu opcji **Zapisz**, jeśli przejdziesz do **harmonogramów zadań** w okienku nawigacji po lewej stronie, możesz śledzić wykonywanie zadania, wybierając pozycję **Informacje o wykonywaniu**.


## <a name="for-more-information"></a>Więcej informacji

Aby zarządzać zadaniem przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [AZ Batch Job-Schedule](/cli/azure/batch/job-schedule).

## <a name="next-steps"></a>Następne kroki

[Utwórz zależności zadań, aby uruchamiać zadania, które są zależne od innych zadań](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


