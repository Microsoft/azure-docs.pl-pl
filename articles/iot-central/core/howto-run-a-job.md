---
title: Twórz i uruchamiaj zadania w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Zadania IoT Central platformy Azure umożliwiają obsługę zbiorczych funkcji zarządzania urządzeniami, takich jak aktualizowanie właściwości lub uruchamianie polecenia.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 406881a9131aae35b91dcab248745bb426cecf0e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797840"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Tworzenie i uruchamianie zadania w aplikacji IoT Central platformy Azure

Możesz użyć IoT Central Microsoft Azure, aby zarządzać połączonymi urządzeniami na dużą skalę za pomocą zadań. Zadania umożliwiają wykonywanie aktualizacji zbiorczych do właściwości urządzeń i uruchamianie poleceń. W tym artykule pokazano, jak zacząć korzystać z zadań w swojej aplikacji.

## <a name="create-and-run-a-job"></a>Tworzenie i uruchamianie zadania

W tej sekcji pokazano, jak utworzyć i uruchomić zadanie w formie ustawienia progu światła dla grupy urządzeń bramy logistycznej.

1. W okienku po lewej stronie wybierz pozycję **zadania**.

2. Wybierz pozycję **+ Nowe**.

   ![Zrzut ekranu pokazujący wybrane opcje tworzenia zadania.](./media/howto-run-a-job/create-new-job.png)

3. Wprowadź nazwę i opis, aby zidentyfikować tworzone zadanie.

4. Wybierz docelową grupę urządzeń, do której chcesz zastosować zadanie. Liczba urządzeń, do których odnosi się konfiguracja zadania, znajduje się w sekcji **Podsumowanie** .

5. Wybierz **Właściwość w chmurze**, **Właściwość**lub **polecenie** jako typ zadania do skonfigurowania. 

   Aby skonfigurować konfigurację zadania **Właściwości** , wybierz właściwość i ustaw jej nową wartość. Aby skonfigurować konfigurację zadania **polecenia** , wybierz polecenie, które ma zostać uruchomione. Zadanie właściwości można ustawić wiele właściwości.

   ![Zrzut ekranu pokazujący wybory dla tworzenia zadania właściwości o nazwie Ustaw próg światła.](./media/howto-run-a-job/configure-job.png)

6. Wybierz pozycję **Uruchom** lub **Zapisz**. Zadanie zostanie wyświetlone na stronie główne **zadania** . Na tej stronie można zobaczyć aktualnie uruchomione zadanie i historię wszystkich poprzednio uruchomionych lub zapisanych zadań. Zapisane zadanie można otworzyć w dowolnym momencie, aby kontynuować jego edytowanie lub uruchomić.

   ![Zrzut ekranu pokazujący nazwę, stan i opis utworzonego zadania.](./media/howto-run-a-job/view-job.png)

   > [!NOTE]
   > Możesz wyświetlić 30-dniową historię poprzednio wykonywanych zadań.

7. Wybierz zapisane zadanie i uruchom je, wybierając przycisk **Run (Uruchom** ). 

   Zostanie wyświetlone okno dialogowe **Uruchamianie zadania?** . Potwierdź, wybierając przycisk **Uruchom zadanie** . 

   ![Zrzut ekranu przedstawiający okno dialogowe, które potwierdza, że chcesz uruchomić zadanie.](./media/howto-run-a-job/run-job.png)

8. Zadanie przechodzi przez fazy oczekujące, uruchomione i ukończone. Szczegóły wykonania zadania zawierają metryki wyników, szczegóły czasu trwania oraz siatkę listy urządzeń. 

   Z tego przeglądu można także wybrać pozycję **Dziennik wyników** , aby pobrać plik CSV zawierający szczegóły zadania, w tym urządzenia i ich wartości stanu. Te informacje mogą być przydatne podczas rozwiązywania problemów.

   ![Zrzut ekranu przedstawiający stan urządzenia.](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Zarządzanie zadaniami

Aby zatrzymać uruchomione zadanie, otwórz je i wybierz pozycję **Zatrzymaj**. Stan zadania zmieni się, aby odzwierciedlić, że zadanie zostało zatrzymane. Sekcja **podsumowania** pokazuje, które urządzenia zostały ukończone, zakończyły się niepowodzeniem lub nadal oczekują.

![Zrzut ekranu pokazujący uruchomione zadanie i przycisk służący do zatrzymywania zadania.](./media/howto-run-a-job/manage-job.png)

Gdy zadanie jest w stanie zatrzymania, możesz wybrać pozycję **Kontynuuj** , aby wznowić działanie zadania. Stan zadania zmieni się, aby odzwierciedlić, że zadanie jest teraz uruchomione ponownie. Sekcja **podsumowania** jest nadal aktualizowana o najnowszym postępie.

![Zrzut ekranu pokazujący zatrzymane zadanie i przycisk służący do kontynuowania zadania.](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Kopiowanie zadania

Aby skopiować jedno z istniejących zadań, wybierz je na stronie **zadania** i wybierz pozycję **szczegóły zadania**. Zostanie wyświetlona strona **szczegóły zadania** . 

![Zrzut ekranu przedstawiający stronę szczegółów zadania.](./media/howto-run-a-job/job-details.png)

Wybierz polecenie **Kopiuj**.

![Zrzut ekranu pokazujący przycisk kopiowania.](./media/howto-run-a-job/job-details-copy.png)

Zostanie wyświetlona kopia konfiguracji zadania, którą można edytować, a w polu Nazwa zadania zostanie dodana **kopia** . Możesz zapisać lub uruchomić nowe zadanie.

![Zrzut ekranu pokazujący kopię konfiguracji zadania.](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Wyświetlanie stanu zadania

Po utworzeniu zadania kolumna **stan** zostaje zaktualizowana o ostatni komunikat o stanie dla tego zadania. Poniższa tabela zawiera listę możliwych wartości stanu zadania:

| Komunikat o stanie       | Stan znaczenie                                          |
| -------------------- | ------------------------------------------------------- |
| Ukończone            | To zadanie zostało uruchomione na wszystkich urządzeniach.              |
| Niepowodzenie               | To zadanie nie powiodło się i nie zostało w pełni uruchomione na urządzeniach.  |
| Oczekiwanie              | To zadanie nie zostało jeszcze uruchomione na urządzeniach.         |
| Uruchomienie              | To zadanie jest obecnie uruchomione na urządzeniach.             |
| Zatrzymano              | Użytkownik ręcznie zatrzymał to zadanie.           |

Po komunikacie o stanie następuje przegląd urządzeń w zadaniu. Poniższa tabela zawiera listę możliwych wartości stanu urządzenia:

| Komunikat o stanie       | Stan znaczenie                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Powodzenie            | Liczba urządzeń, na których zadanie zostało pomyślnie uruchomione.       |
| Niepowodzenie               | Liczba urządzeń, na których uruchomienie zadania nie powiodło się.       |

Aby wyświetlić stan zadania i wszystkie urządzenia, których to dotyczy, Otwórz zadanie. Obok każdej nazwy urządzenia zostanie wyświetlony jeden z następujących komunikatów o stanie:

| Komunikat o stanie       | Stan znaczenie                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Ukończone            | Zadanie zostało uruchomione na tym urządzeniu.                                     |
| Niepowodzenie               | Nie można uruchomić zadania na tym urządzeniu. Komunikat o błędzie zawiera więcej informacji.  |
| Oczekiwanie              | Zadanie nie zostało jeszcze uruchomione na tym urządzeniu.                                   |

Aby pobrać plik CSV zawierający szczegóły zadania oraz listę urządzeń i ich wartości stanu, wybierz pozycję **Pobierz**.

## <a name="filter-the-device-list"></a>Filtrowanie listy urządzeń

Listę urządzeń można filtrować na stronie **szczegóły zadania** , wybierając ikonę filtru. Można filtrować według **identyfikatora urządzenia** lub pola **stanu** .

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Zrzut ekranu pokazujący opcje filtrowania listy urządzeń.":::

## <a name="customize-columns-in-the-device-list"></a>Dostosowywanie kolumn na liście urządzeń

Możesz wybrać dodatkowe kolumny do wyświetlenia na liście urządzeń, wybierając ikonę opcji kolumny.

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Zrzut ekranu pokazujący ikonę opcji kolumn.":::

Okno dialogowe umożliwia wybranie kolumn, które mają być wyświetlane na liście urządzeń. Wybierz kolumny, które chcesz wyświetlić, wybierz strzałkę w prawo, a następnie wybierz przycisk **OK**. Aby zaznaczyć wszystkie dostępne kolumny, zaznacz pole wyboru **Wybierz wszystko**.

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe wybierania kolumn do wyświetlenia.":::

Wybrane kolumny pojawią się na liście urządzeń.

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Zrzut ekranu pokazujący wybrane kolumny na liście urządzeń.":::

Wybrane kolumny są utrwalane w sesji użytkownika lub między sesjami użytkowników, które mają dostęp do aplikacji.

## <a name="rerun-jobs"></a>Uruchom ponownie zadania

Możesz ponownie uruchomić zadanie, które ma urządzenia, które uległy awarii. Wybierz pozycję **Uruchom ponownie w przypadku niepowodzenia**.

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Zrzut ekranu pokazujący przycisk służący do uruchamiania zadania na urządzeniach zakończonych niepowodzeniem.":::

Wprowadź nazwę i opis zadania, a następnie wybierz pozycję **Uruchom ponownie zadanie**. Zostanie przesłane nowe zadanie, aby ponowić akcję na urządzeniach zakończonych niepowodzeniem.

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe umożliwiające oddziałanie nieudanych urządzeń.":::

> [!NOTE]
> Nie można uruchomić więcej niż pięć zadań jednocześnie w aplikacji IoT Central platformy Azure.
>
> Po zakończeniu zadania i usunięciu urządzenia znajdującego się na liście urządzeń, wpis urządzenia zostanie wyświetlony jako usunięty w nazwie urządzenia. Link szczegóły nie jest dostępny dla usuniętego urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć zadania w aplikacji IoT Central platformy Azure, Oto kilka następnych kroków:

- [Zarządzanie urządzeniami](howto-manage-devices.md)
- [Wersja szablonu urządzenia](howto-version-device-template.md)
