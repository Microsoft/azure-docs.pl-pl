---
title: Twórz i uruchamiaj zadania w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Zadania IoT Central platformy Azure umożliwiają obsługę zbiorczych funkcji zarządzania urządzeniami, takich jak aktualizowanie właściwości lub uruchamianie polecenia.
ms.service: iot-central
services: iot-central
author: philmea
ms.author: philmea
ms.date: 11/19/2020
ms.topic: how-to
ms.openlocfilehash: 19d8738790b5634b9de989fa94edac6a542f85f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94917345"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Tworzenie i uruchamianie zadania w aplikacji IoT Central platformy Azure

Korzystając z usługi Azure IoT Central, możesz zarządzać połączonymi urządzeniami w odpowiedniej skali za pomocą zadań. Zadania umożliwiają wykonywanie aktualizacji zbiorczych do właściwości urządzeń i chmury oraz uruchamianie poleceń. W tym artykule pokazano, jak zacząć korzystać z zadań w swojej aplikacji.

## <a name="create-and-run-a-job"></a>Tworzenie i uruchamianie zadania

Poniższy przykład pokazuje, jak utworzyć i uruchomić zadanie w celu ustawienia progu światła dla grupy urządzeń z bramą logistyczną. Kreator zadania służy do tworzenia i uruchamiania zadań. Zadanie do uruchomienia można wykonać później.

1. W okienku po lewej stronie wybierz pozycję **zadania**.

1. Wybierz pozycję **+ nowe zadanie**.

1. Na stronie **Konfigurowanie zadania** wprowadź nazwę i opis, aby zidentyfikować tworzone zadanie.

1. Wybierz docelową grupę urządzeń, do której chcesz zastosować zadanie. Można sprawdzić, ile urządzeń dotyczy dana konfiguracja zadania poniżej wybranej **grupy urządzeń** .

1. Wybierz **Właściwość w chmurze**, **Właściwość** lub **polecenie** jako **Typ zadania**:

    Aby skonfigurować zadanie **Właściwości** , wybierz właściwość i ustaw jej nową wartość. Aby skonfigurować zadanie **polecenia** , wybierz polecenie, które ma zostać uruchomione. Zadanie właściwości można ustawić wiele właściwości.

    :::image type="content" source="media/howto-run-a-job/configure-job.png" alt-text="Zrzut ekranu pokazujący wybory dla tworzenia zadania właściwości o nazwie Ustaw próg światła":::

    Wybierz pozycję **Zapisz i wyjdź** , aby dodać zadanie do listy zapisanych zadań na stronie **zadania** . Później możesz powrócić do zadania z listy zapisanych zadań.

1. Wybierz przycisk **dalej** , aby przejść do strony **Opcje dostarczania** . Na stronie **Opcje dostarczania** można ustawić opcje dostarczania dla tego zadania: **partie** i **próg anulowania**.

    Partie umożliwiają rozłożenie zadań dla dużej liczby urządzeń. Zadanie jest podzielone na wiele partii, a każda partia zawiera podzestaw urządzeń. Partie są umieszczane w kolejce i uruchamiane w sekwencji.

    Próg anulowania umożliwia automatyczne anulowanie zadania, jeśli liczba błędów przekracza limit ustawiony. Próg może dotyczyć wszystkich urządzeń w zadaniu lub do poszczególnych partii.

    :::image type="content" source="media/howto-run-a-job/job-wizard-delivery-options.png" alt-text="Zrzut ekranu przedstawiający stronę opcji dostarczania Kreatora zadań":::

1. Wybierz pozycję **dalej** , aby przejść do strony **harmonogram** . Strona **harmonogram** umożliwia włączenie harmonogramu uruchamiania zadania w przyszłości:

    Wybierz opcję cyklu dla harmonogramu. Można skonfigurować zadanie do uruchomienia:

    * Jednorazowa
    * Dzienna
    * Tygodniowa

    Ustaw datę i godzinę rozpoczęcia zaplanowanego zadania. Data i godzina są specyficzne dla strefy czasowej, a nie czasu lokalnego na urządzeniu.

    Aby zakończyć cykliczny harmonogram, wybierz:

    * **W tym dniu** Ustaw datę zakończenia harmonogramu.
    * **Po** określeniu liczby ponownych uruchomień zadania.

    Zaplanowane zadania są zawsze uruchamiane na urządzeniach w grupie urządzeń, nawet jeśli członkostwo w grupie urządzeń zmienia się w czasie.

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule.png" alt-text="Zrzut ekranu przedstawiający stronę opcji harmonogramu Kreatora zadań":::

1. Wybierz pozycję **dalej** , aby przejść do strony **Przegląd** . Na stronie **Przegląd** są wyświetlane szczegóły konfiguracji zadania. Wybierz pozycję **harmonogram** , aby zaplanować zadanie:

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule-review.png" alt-text="Zrzut ekranu przedstawiający stronę przegląd Kreatora zaplanowanych zadań":::

1. Na stronie Szczegóły zadania są wyświetlane informacje o zaplanowanych zadaniach. Po uruchomieniu zaplanowanego zadania zostanie wyświetlona lista wystąpień zadań. Zaplanowane wykonanie zadania jest również częścią **ostatniej 30-dniowej** listy zadań.

    Na tej stronie można wyznaczać **harmonogram** zadania lub **edytować** zaplanowane zadanie. Możesz powrócić do zaplanowanego zadania z listy zaplanowanych zadań.

    :::image type="content" source="media/howto-run-a-job/job-schedule-details.png" alt-text="Zrzut ekranu przedstawiający stronę szczegółów zaplanowanych zadań":::

1. W kreatorze zadania możesz zrezygnować z planowania zadania i uruchomić je od razu. Poniższy zrzut ekranu przedstawia zadanie bez harmonogramu, który jest gotowy do natychmiastowego uruchomienia. Wybierz pozycję **Uruchom** , aby uruchomić zadanie:

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule-immediate.png" alt-text="Zrzut ekranu przedstawiający stronę przeglądu Kreatora zadań":::

1. Zadanie przechodzi przez etapy *oczekujące*, *uruchomione* i *ukończone* . Szczegóły wykonania zadania zawierają metryki wyników, szczegóły czasu trwania oraz siatkę listy urządzeń.

    Po zakończeniu zadania możesz wybrać pozycję **Dziennik wyników** , aby pobrać plik CSV zawierający szczegóły zadania, w tym urządzenia i ich wartości stanu. Te informacje mogą być przydatne podczas rozwiązywania problemów.

    :::image type="content" source="media/howto-run-a-job/download-details.png" alt-text="Zrzut ekranu przedstawiający stan urządzenia":::

1. Zadanie zostanie wyświetlone na liście **ostatnie 30 dni** na stronie **zadania** . Ta strona zawiera aktualnie uruchomione zadania i historię wszystkich poprzednio uruchomionych lub zapisanych zadań.

    > [!NOTE]
    > Możesz wyświetlić 30-dniową historię poprzednio wykonywanych zadań.

## <a name="manage-jobs"></a>Zarządzanie zadaniami

Aby zatrzymać uruchomione zadanie, otwórz je i wybierz pozycję **Zatrzymaj**. Stan zadania zmieni się, aby odzwierciedlić, że zadanie zostało zatrzymane. Sekcja **podsumowania** pokazuje, które urządzenia zostały ukończone, zakończyły się niepowodzeniem lub nadal oczekują.

:::image type="content" source="media/howto-run-a-job/manage-job.png" alt-text="Zrzut ekranu pokazujący uruchomione zadanie i przycisk służący do zatrzymywania zadania":::

Gdy zadanie jest w stanie zatrzymania, możesz wybrać pozycję **Kontynuuj** , aby wznowić działanie zadania. Stan zadania zmieni się, aby odzwierciedlić, że zadanie jest teraz uruchomione ponownie. Sekcja **podsumowania** kontynuuje aktualizację przy użyciu najnowszego postępu.

:::image type="content" source="media/howto-run-a-job/stopped-job.png" alt-text="Zrzut ekranu pokazujący zatrzymane zadanie i przycisk służący do kontynuowania zadania":::

## <a name="copy-a-job"></a>Kopiowanie zadania

Aby skopiować istniejące zadanie, wybierz wykonane zadanie. Wybierz pozycję **Kopiuj** na stronie Szczegóły zadania lub strony szczegółów zadania:

:::image type="content" source="media/howto-run-a-job/job-details-copy.png" alt-text="Zrzut ekranu pokazujący przycisk kopiowania":::

Zostanie wyświetlona kopia konfiguracji zadania, którą można edytować, a w polu Nazwa zadania zostanie dodana **kopia** .

## <a name="view-job-status"></a>Wyświetlanie stanu zadania

Po utworzeniu zadania kolumna **stan** będzie aktualizowana przy użyciu komunikatu o stanie ostatniego zadania. Poniższa tabela zawiera listę możliwych wartości *stanu zadania* :

| Komunikat o stanie       | Stan znaczenie                                          |
| -------------------- | ------------------------------------------------------- |
| Ukończone            | To zadanie zostało uruchomione na wszystkich urządzeniach.              |
| Niepowodzenie               | To zadanie nie powiodło się i nie zostało w pełni uruchomione na urządzeniach.  |
| Oczekiwanie              | To zadanie nie zostało jeszcze uruchomione na urządzeniach.         |
| Uruchomienie              | To zadanie jest obecnie uruchomione na urządzeniach.             |
| Zatrzymano              | Użytkownik ręcznie zatrzymał to zadanie.           |
| Anulowane             | To zadanie zostało anulowane, ponieważ próg ustawiony na stronie **Opcje dostarczania** został przekroczony. |

Po komunikacie o stanie następuje przegląd urządzeń w zadaniu. Poniższa tabela zawiera listę możliwych wartości *stanu urządzenia* :

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

Aby pobrać plik CSV zawierający szczegóły zadania oraz listę urządzeń i ich wartości stanu, wybierz pozycję **Dziennik wyników**.

## <a name="filter-the-device-list"></a>Filtrowanie listy urządzeń

Listę urządzeń można filtrować na stronie **szczegóły zadania** , wybierając ikonę filtru. Można filtrować według **identyfikatora urządzenia** lub pola **stanu** :

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Zrzut ekranu pokazujący opcje filtrowania listy urządzeń.":::

## <a name="customize-columns-in-the-device-list"></a>Dostosowywanie kolumn na liście urządzeń

Możesz dodać kolumny do listy urządzeń, wybierając ikonę opcji kolumny:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Zrzut ekranu pokazujący ikonę opcji kolumn.":::

Za pomocą okna dialogowego **Opcje kolumny** możesz wybrać kolumny listy urządzeń. Wybierz kolumny, które chcesz wyświetlić, wybierz strzałkę w prawo, a następnie wybierz przycisk **OK**. Aby zaznaczyć wszystkie dostępne kolumny, wybierz **pozycję Zaznacz wszystko**. Wybrane kolumny pojawią się na liście urządzeń.

Wybrane kolumny są utrwalane w sesji użytkownika lub między sesjami użytkowników, które mają dostęp do aplikacji.

## <a name="rerun-jobs"></a>Uruchom ponownie zadania

Możesz ponownie uruchomić zadanie, które ma urządzenia, które uległy awarii. Wybierz pozycję **Uruchom ponownie przy nieudanych próbach**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Zrzut ekranu pokazujący przycisk służący do uruchamiania zadania na urządzeniach zakończonych niepowodzeniem.":::

Wprowadź nazwę i opis zadania, a następnie wybierz pozycję **Uruchom ponownie zadanie**. Zostanie przesłane nowe zadanie, aby ponowić akcję na urządzeniach zakończonych niepowodzeniem.

> [!NOTE]
> Nie można uruchomić więcej niż pięć zadań jednocześnie w aplikacji IoT Central platformy Azure.
>
> Po zakończeniu zadania i usunięciu urządzenia znajdującego się na liście urządzeń, wpis urządzenia zostanie wyświetlony jako usunięty w nazwie urządzenia. Link szczegóły nie jest dostępny dla usuniętego urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć zadania w aplikacji IoT Central platformy Azure, Oto kilka następnych kroków:

- [Zarządzanie urządzeniami](howto-manage-devices.md)
- [Wersja szablonu urządzenia](howto-version-device-template.md)
