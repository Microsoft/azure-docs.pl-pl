---
title: Przewodnik Szybki Start platformy Azure — uruchamianie pierwszego zadania usługi Batch w Azure Portal
description: W tym przewodniku szybki start pokazano, jak używać Azure Portal do tworzenia konta wsadowego, puli węzłów obliczeniowych i zadania, które uruchamia podstawowe zadania w puli.
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: mvc
ms.openlocfilehash: 1234a932a732cdb6fda1c412a423ae0b1ea089e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184019"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Szybki start: uruchamianie pierwszego zadania usługi Batch w witrynie Azure Portal

Rozpocznij pracę z Azure Batch przy użyciu Azure Portal do tworzenia konta usługi Batch, puli węzłów obliczeniowych (maszyn wirtualnych) i zadania, które uruchamia zadania w puli. Po ukończeniu tego przewodnika Szybki Start zrozumiesz kluczowe pojęcia związane z usługą Batch i wszystko jest gotowe do wypróbowania partii z bardziej realistycznymi obciążeniami w większej skali.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-batch-account"></a>Tworzenie konta usługi Batch

Wykonaj następujące kroki, aby utworzyć przykładowe konto usługi Batch do celów testowych. Konto usługi Batch jest wymagane w celu tworzenia pul i zadań. Jak pokazano poniżej, z kontem usługi Batch możesz powiązać konto usługi Azure Storage. Chociaż nie jest ono wymagane na potrzeby tego przewodnika Szybki start, konto magazynu jest przydatne do wdrażania aplikacji oraz przechowywania danych wejściowych i wyjściowych w przypadku większości rzeczywistych obciążeń.

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **usługi Batch**. 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Zrzut ekranu usługi Batch w portalu Azure Marketplace.":::

1. W polu **Grupa zasobów** wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów.

1. Wprowadź wartość dla **nazwy konta**. Ta nazwa musi być unikatowa w ramach wybranej **lokalizacji** platformy Azure. Może zawierać tylko małe litery i cyfry i musi zawierać od 3-24 znaków.

1. W obszarze **konto magazynu** Wybierz istniejące konto magazynu lub Utwórz nowe.

1. Nie zmieniaj żadnych innych ustawień. Wybierz pozycję **Recenzja + Utwórz**, a następnie wybierz pozycję **Utwórz** , aby utworzyć konto w usłudze Batch.

Po wyświetleniu komunikatu **wdrożenie zakończyło się pomyślnie** przejdź do utworzonego konta programu Batch.

## <a name="create-a-pool-of-compute-nodes"></a>Tworzenie puli węzłów obliczeniowych

Teraz, gdy masz już konto usługi Batch, utwórz przykładową pulę węzłów obliczeniowych systemu Windows do celów testowych. Pula tego szybkiego przykładu składa się z dwóch węzłów z uruchomionym obrazem systemu Windows Server 2019 z portalu Azure Marketplace.

1. Na koncie wsadowym wybierz pozycję **Pule**  >  **Dodaj**.

1. W polu **Identyfikator puli** podaj wartość *mypool*.

1. W obszarze **System operacyjny** wybierz następujące ustawienia (możesz eksplorować pozostałe opcje).
  
   |Ustawienie  |Wartość  |
   |---------|---------|
   |**Typ obrazu**|Marketplace|
   |**Wydawca**     |MicrosoftWindowsServer|
   |**Oferta**     |WindowsServer|
   |**Magazyn**     |2019 — Datacenter-Core-smalldisk|

1. Przewiń w dół, aby wprowadzić wartości ustawień **Rozmiar węzła** i **Skala**. Sugerowany rozmiar węzłów oferuje dobry kompromis między wydajnością a kosztem na potrzeby tego krótkiego przykładu.
  
   |Ustawienie  |Wartość  |
   |---------|---------|
   |**Warstwa cenowa węzła**     |Standardowa a1|
   |**Docelowe węzły dedykowane**     |2|

1. Pozostaw wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**, aby utworzyć pulę.

Usługa Batch tworzy pulę natychmiast, ale przydzielenie i uruchomienie węzłów obliczeniowych może potrwać kilka minut. W tym czasie **Stan alokacji** puli będzie miał wartość **Zmiana rozmiaru**. Gdy trwa zmiana rozmiaru puli, możesz kontynuować samouczek, tworząc zadanie i zadania podrzędne.

Po kilku minutach stan alokacji zmieni się na **stabilny**, a węzły zostaną uruchomione. Aby sprawdzić stan węzłów, zaznacz pulę, a następnie wybierz pozycję **węzły**. Gdy węzeł ma stan **Bezczynny**, jest gotowy do uruchamiania zadań podrzędnych.

## <a name="create-a-job"></a>Tworzenie zadania

Teraz, gdy masz już pulę, utwórz zadanie, które zostanie w niej uruchomione. Zadanie wsadowe to logiczna grupa co najmniej jednego zadania. Zadanie uwzględnia wspólne ustawienia zadań podrzędnych, takie jak priorytet i pula, w której zadania podrzędne mają być uruchamiane. Początkowo zadanie nie zawiera zadań podrzędnych.

1. W widoku konta wsadowego wybierz kolejno pozycje **zadania**  >  **Dodaj**.

1. W polu **Identyfikator zadania** wprowadź wartość *myjob*. W polu **Pula** wybierz pozycję *mypool*. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**.

## <a name="create-tasks"></a>Tworzenie zadań

Teraz wybierz zadanie, aby otworzyć stronę **zadania** . W tym miejscu utworzysz przykładowe zadania do uruchomienia w ramach zadania. Zazwyczaj tworzysz wiele zadań, które kolejek i dystrybuuje w usłudze Batch do uruchomienia w węzłach obliczeniowych. W tym przykładzie utworzysz dwa identyczne zadania podrzędne. Każde zadanie podrzędne uruchamia wiersz polecenia w celu wyświetlenia zmiennych środowiskowych usługi Batch w węźle obliczeniowym, a następnie czeka 90 sekund.

Podczas korzystania z usługi Batch aplikację lub skrypt określa się w wierszu polecenia. Usługa Batch udostępnia kilka sposobów wdrażania aplikacji i skryptów w węzłach obliczeniowych.

Aby utworzyć pierwsze zadanie podrzędne:

1. Wybierz pozycję **Dodaj**.

1. W polu **Identyfikator zadania podrzędnego** wprowadź wartość *mytask*.

1. W polu **Wiersz polecenia** wprowadź wartość `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Pozostaw wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Prześlij**.

Po utworzeniu zadania podrzędnego usługa Batch umieszcza je w kolejce w celu uruchomienia w puli. Zadanie podrzędne zostanie uruchomione, gdy odpowiedni węzeł stanie się dostępny.

Aby utworzyć drugie zadanie, powtórz powyższe kroki. Wprowadź inną wartość w polu **Identyfikator zadania podrzędnego**, ale podaj identyczny wiersza polecenia. Jeśli pierwsze zadanie podrzędne jest nadal uruchomione, usługa Batch uruchomi drugie zadanie podrzędne w drugim węźle w puli.

## <a name="view-task-output"></a>Wyświetlanie danych wyjściowych zadania podrzędnego

Utworzone przykładowe zadania zostaną wykonane w ciągu kilku minut. Aby wyświetlić dane wyjściowe wykonanego zadania, wybierz zadanie, a następnie wybierz pozycję **pliki w węźle**. Wybierz plik, `stdout.txt` Aby wyświetlić standardowe dane wyjściowe zadania. Zawartość jest podobna do następującej:

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="Zrzut ekranu przedstawiający dane wyjściowe z wykonanego zadania.":::

Zawartość obejmuje zmienne środowiskowe usługi Azure Batch, które są ustawione w węźle. Podczas tworzenia własnych zadań i zadań podrzędnych usługi Batch możesz odwoływać się do tych zmiennych środowiskowych w wierszach polecenia zadań podrzędnych oraz w aplikacjach i skryptach uruchamianych przez wiersze polecenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz kontynuować naukę z samouczkami i przykładami usługi Batch, skorzystaj z konta usługi Batch i powiązanego konta magazynu, które zostały utworzone w tym przewodniku Szybki start. Za samo konto usługi Batch nie są naliczane opłaty.

Opłaty za pulę są naliczane, dopóki węzły działają, nawet jeśli nie zostały zaplanowane żadne zadania. Gdy pula nie jest już potrzebna, usuń ją. W widoku konta wybierz pozycję **Pule** i nazwę puli. Następnie wybierz pozycję **Usuń**.  W przypadku usunięcia puli usuwane są również wszystkie dane wyjściowe zadań podrzędnych w węzłach.

Gdy grupa zasobów, konto usługi Batch i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. W tym celu zaznacz grupę zasobów konta usługi Batch i wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono konto usługi Batch, pulę usługi Batch i zadanie usługi Batch. Zadanie uruchomiło przykładowe zadania podrzędne i wyświetlono dane wyjściowe utworzone w jednym z węzłów. Teraz, gdy już rozumiesz kluczowe pojęcia związane z usługą Batch, możesz wypróbować tę usługę z bardziej realistycznymi obciążeniami na większą skalę. Aby dowiedzieć się więcej o usłudze Azure Batch, kontynuuj naukę w ramach samouczków usługi Azure Batch.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Batch](./tutorial-parallel-dotnet.md)
