---
title: Twórz i uruchamiaj zadania w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Zadania IoT Central platformy Azure umożliwiają obsługę zbiorczych funkcji zarządzania urządzeniami, takich jak aktualizowanie właściwości lub wykonywanie polecenia.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: dec9abc38bc0354ef3d22994a7988bfb006f5769
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84609746"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Tworzenie i uruchamianie zadania w aplikacji IoT Central platformy Azure

Za pomocą IoT Central Microsoft Azure można zarządzać podłączonymi urządzeniami w odpowiedniej skali przy użyciu zadań. Zadania umożliwiają wykonywanie aktualizacji zbiorczych do właściwości urządzeń i uruchamianie poleceń. W tym artykule pokazano, jak rozpocząć pracę z zadaniami w swojej aplikacji.

## <a name="create-and-run-a-job"></a>Tworzenie i uruchamianie zadania

W tej sekcji pokazano, jak utworzyć i uruchomić zadanie. Pokazuje, jak ustawić próg światła dla grupy urządzeń z bramą logistyczną.

1. Przejdź do **zadania** w okienku po lewej stronie.

2. Wybierz pozycję **+ Nowy** , aby utworzyć nowe zadanie:

    ![Utwórz nowe zadanie](./media/howto-run-a-job/create-new-job.png)

3. Wprowadź nazwę i opis, aby zidentyfikować tworzone zadanie.

4. Wybierz docelową grupę urządzeń, do której chcesz zastosować zadanie. Liczba urządzeń, do których odnosi się konfiguracja zadania, znajduje się w sekcji **Podsumowanie** .

5. Następnie wybierz właściwość w **chmurze**, **Właściwość**lub **polecenie** jako typ zadania do skonfigurowania. Aby skonfigurować konfigurację zadania **Właściwości** , wybierz właściwość i ustaw jej nową wartość. Aby skonfigurować **polecenie**, wybierz polecenie, które ma zostać uruchomione. Zadanie właściwości można ustawić wiele właściwości:

    ![Konfigurowanie zadania](./media/howto-run-a-job/configure-job.png)

6. Po utworzeniu zadania wybierz pozycję **Uruchom** lub **Zapisz**. Zadanie zostanie wyświetlone na stronie główne **zadania** . Na tej stronie można zobaczyć aktualnie uruchomione zadanie i historię wszystkich poprzednio uruchomionych lub zapisanych zadań. Zapisane zadanie można otworzyć ponownie w dowolnym momencie, aby kontynuować jego edytowanie lub uruchomić:

    ![Wyświetl zadanie](./media/howto-run-a-job/view-job.png)

    > [!NOTE]
    > Możesz wyświetlić 30-dniową historię poprzednio wykonywanych zadań.

7. Aby uzyskać przegląd zadania, wybierz z listy zadanie do wyświetlenia. To omówienie zawiera szczegóły zadania, urządzenia i wartości stanu urządzenia. Z tego omówienia można także wybrać opcję **Pobierz szczegóły zadania** , aby pobrać plik CSV zawierający szczegóły zadania, w tym urządzenia i ich wartości stanu. Te informacje mogą być przydatne podczas rozwiązywania problemów:

    ![Wyświetlanie stanu urządzenia](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Zarządzanie zadaniami

Aby zatrzymać jedno z uruchomionych zadań, otwórz je i wybierz pozycję **Zatrzymaj**. Stan zadania zmienia się, aby odzwierciedlić zadanie zostało zatrzymane. Sekcja **podsumowania** zawiera listę urządzeń, które zostały ukończone, zakończyły się niepowodzeniem lub nadal oczekują.

Aby uruchomić zadanie, które jest aktualnie zatrzymane, zaznacz je, a następnie wybierz polecenie **Uruchom**. Stan zadania zmienia się w celu odzwierciedlenia, że zadanie jest teraz uruchomione ponownie. Sekcja **podsumowania** kontynuuje aktualizację przy użyciu najnowszego postępu.

![Zarządzaj zadaniem](./media/howto-run-a-job/manage-job.png)

## <a name="copy-a-job"></a>Kopiowanie zadania

Aby skopiować jedno z istniejących zadań, wybierz je na stronie **zadania** i wybierz pozycję **Kopiuj**. Zostanie wyświetlona kopia konfiguracji zadania, którą można edytować, a w polu Nazwa zadania zostanie dodana **kopia** . Możesz zapisać lub uruchomić nowe zadanie:

![Kopiuj zadanie](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Wyświetlanie stanu zadania

Po utworzeniu zadania kolumna **stan** będzie aktualizowana przy użyciu najnowszego komunikatu o stanie zadania. Poniższa tabela zawiera listę możliwych wartości stanu:

| Komunikat o stanie       | Stan znaczenie                                          |
| -------------------- | ------------------------------------------------------- |
| Ukończone            | To zadanie zostało wykonane na wszystkich urządzeniach.              |
| Niepowodzenie               | To zadanie nie powiodło się i nie zostało w pełni wykonane na urządzeniach.  |
| Oczekiwanie              | To zadanie jeszcze nie rozpoczęło wykonywania na urządzeniach.         |
| Działanie              | To zadanie jest obecnie wykonywane na urządzeniach.             |
| Zatrzymano              | To zadanie zostało ręcznie zatrzymane przez użytkownika.           |

Po komunikacie o stanie następuje przegląd urządzeń w zadaniu. Poniższa tabela zawiera listę możliwych wartości stanu urządzenia:

| Komunikat o stanie       | Stan znaczenie                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Sukces            | Liczba urządzeń, na których zadanie zostało pomyślnie wykonane.       |
| Niepowodzenie               | Liczba urządzeń, na których wykonywanie zadania nie powiodło się.       |

### <a name="view-the-device-status-values"></a>Wyświetlanie wartości stanu urządzenia

Aby wyświetlić stan zadania i wszystkie urządzenia, których to dotyczy, Otwórz zadanie. Obok każdej nazwy urządzenia zostanie wyświetlony jeden z następujących komunikatów o stanie:

| Komunikat o stanie       | Stan znaczenie                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Ukończone            | Zadanie wykonane na tym urządzeniu.                                     |
| Niepowodzenie               | Nie można wykonać zadania na tym urządzeniu. Komunikat o błędzie zawiera więcej informacji.  |
| Oczekiwanie              | Zadanie nie zostało jeszcze wykonane na tym urządzeniu.                                   |

Aby pobrać plik CSV zawierający szczegóły zadania oraz listę urządzeń i ich wartości stanu, wybierz pozycję **Pobierz**.

### <a name="filter-the-list-of-devices"></a>Filtrowanie listy urządzeń

Listę urządzeń można filtrować na stronie Szczegóły zadania, wybierając ikonę filtru. Można filtrować według **identyfikatora urządzenia** lub pól **stanu** :

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Filtrowanie listy urządzeń":::

### <a name="customize-columns-in-the-device-list"></a>Dostosowywanie kolumn na liście urządzeń

Możesz wybrać dodatkowe kolumny do wyświetlenia na liście urządzeń, wybierając ikonę opcji kolumny:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Opcje kolumn":::

Zostanie wyświetlone okno dialogowe umożliwiające wybranie kolumn, które mają być wyświetlane na liście urządzeń. Wybierz kolumny, które chcesz wyświetlić, wybierz ikonę strzałki w prawo, a następnie wybierz przycisk **OK**. Aby zaznaczyć wszystkie dostępne kolumny, zaznacz pole wyboru **Wybierz wszystko**:

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Selektor kolumn — okno dialogowe":::

Wybrane kolumny są wyświetlane na liście urządzeń:

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Wybieranie kolumn":::

Wybrane kolumny są utrwalane w sesji użytkownika lub między sesjami użytkowników, które mają dostęp do aplikacji.

## <a name="rerun-jobs"></a>Uruchom ponownie zadania

Możesz ponownie uruchomić zadanie, które ma urządzenia, które uległy awarii. Wybierz pozycję **Uruchom ponownie**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Uruchom ponownie zadanie":::

Wprowadź nazwę i opis zadania, a następnie wybierz pozycję **Uruchom ponownie zadanie**. Zostanie przesłane nowe zadanie, aby ponowić akcję na urządzeniach zakończonych niepowodzeniem:

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Ponowne uruchamianie urządzeń zakończonych niepowodzeniem":::

> [!NOTE]
> Nie można jednocześnie wykonać więcej niż pięciu zadań z aplikacji IoT Central.

> [!NOTE]
> Po zakończeniu zadania i usunięciu urządzenia znajdującego się na liście urządzeń, wpis urządzenia będzie wyświetlany jako usunięty w polu Nazwa urządzenia i łącze Szczegóły urządzenia są niedostępne dla usuniętego urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć zadania w aplikacji IoT Central platformy Azure, Oto kilka następnych kroków:

- [Zarządzanie urządzeniami](howto-manage-devices.md)
- [Wersja szablonu urządzenia](howto-version-device-template.md)
