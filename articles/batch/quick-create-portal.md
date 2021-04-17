---
title: Przewodnik Szybki start platformy Azure — uruchamianie pierwszego zadania usługi Batch w Azure Portal
description: W tym przewodniku Szybki start pokazano, jak za pomocą Azure Portal utworzyć konto usługi Batch, pulę węzłów obliczeniowych i zadanie, które uruchamia podstawowe zadania podrzędne w puli.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 3333097b4bd55173725aa33bc4bfbae318510cf1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538541"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Szybki start: uruchamianie pierwszego zadania usługi Batch w witrynie Azure Portal

Rozpoczynanie pracy z Azure Batch przy użyciu usługi Azure Portal do tworzenia konta usługi Batch, puli węzłów obliczeniowych (maszyn wirtualnych) i zadania, które uruchamia zadania podrzędne w puli. Po ukończeniu tego przewodnika Szybki start zrozumiesz kluczowe pojęcia dotyczące usługi Batch i wszystko będzie gotowe do wypróbowania usługi Batch z bardziej realistycznymi obciążeniami na większą skalę.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-batch-account"></a>Tworzenie konta usługi Batch

Wykonaj następujące kroki, aby utworzyć przykładowe konto usługi Batch do celów testowych. Konto usługi Batch jest wymagane w celu tworzenia pul i zadań. Jak pokazano poniżej, z kontem usługi Batch możesz powiązać konto usługi Azure Storage. Chociaż nie jest ono wymagane na potrzeby tego przewodnika Szybki start, konto magazynu jest przydatne do wdrażania aplikacji oraz przechowywania danych wejściowych i wyjściowych w przypadku większości rzeczywistych obciążeń.

1. W [chmurze Azure Portal](https://portal.azure.com)pozycję **Utwórz zasób**  >  **Obliczeniowa**  >  **usługa Batch.** 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Zrzut ekranu przedstawiający usługę Batch w Azure Marketplace.":::

1. W polu **Grupa zasobów** wybierz pozycję **Utwórz nową** i wprowadź nazwę grupy zasobów.

1. Wprowadź wartość w **polach Nazwa konta.** Ta nazwa musi być unikatowa w wybranej **lokalizacji platformy** Azure. Może zawierać tylko małe litery i cyfry oraz musi zawierać od 3 do 24 znaków.

1. W **obszarze Konto** magazynu wybierz istniejące konto magazynu lub utwórz nowe.

1. Nie zmieniaj żadnych innych ustawień. Wybierz **pozycję Przejrzyj i utwórz,** a następnie wybierz pozycję **Utwórz,** aby utworzyć konto usługi Batch.

Gdy pojawi się komunikat Wdrażanie **zakończyło** się pomyślnie, przejdź do utworzonego konta usługi Batch.

## <a name="create-a-pool-of-compute-nodes"></a>Tworzenie puli węzłów obliczeniowych

Teraz, gdy masz już konto usługi Batch, utwórz przykładową pulę węzłów obliczeniowych systemu Windows do celów testowych. Pula w tym szybkim przykładzie składa się z dwóch węzłów z obrazem systemu Windows Server 2019 z Azure Marketplace.

1. Na koncie usługi Batch wybierz pozycję **Pule**  >  **Dodaj**.

1. W polu **Identyfikator puli** podaj wartość *mypool*.

1. W obszarze **System operacyjny** wybierz następujące ustawienia (możesz eksplorować pozostałe opcje).
  
   |Ustawienie  |Wartość  |
   |---------|---------|
   |**Typ obrazu**|Marketplace|
   |**Wydawca**     |microsoftwindowsserver|
   |**Oferta**     |windowsserver|
   |**Numer jednostki magazynowej**     |2019-datacenter-core-smalldisk|

1. Przewiń w dół, aby wprowadzić wartości ustawień **Rozmiar węzła** i **Skala**. Sugerowany rozmiar węzłów oferuje dobry kompromis między wydajnością a kosztem na potrzeby tego krótkiego przykładu.
  
   |Ustawienie  |Wartość  |
   |---------|---------|
   |**Warstwa cenowa węzła**     |Standardowa A1|
   |**Docelowe węzły dedykowane**     |2|

1. Pozostaw wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**, aby utworzyć pulę.

Usługa Batch tworzy pulę natychmiast, ale przydzielenie i uruchomienie węzłów obliczeniowych może potrwać kilka minut. W tym czasie **Stan alokacji** puli będzie miał wartość **Zmiana rozmiaru**. Gdy trwa zmiana rozmiaru puli, możesz kontynuować samouczek, tworząc zadanie i zadania podrzędne.

Po kilku minutach stan alokacji zmieni się **na Stały** i węzły zostaną rozpoczęcie. Aby sprawdzić stan węzłów, wybierz pulę, a następnie wybierz pozycję **Węzły**. Gdy węzeł ma stan **Bezczynny**, jest gotowy do uruchamiania zadań podrzędnych.

## <a name="create-a-job"></a>Tworzenie zadania

Teraz, gdy masz już pulę, utwórz zadanie, które zostanie w niej uruchomione. Zadanie usługi Batch to logiczna grupa co najmniej jednego zadania. Zadanie uwzględnia wspólne ustawienia zadań podrzędnych, takie jak priorytet i pula, w której zadania podrzędne mają być uruchamiane. Początkowo zadanie nie zawiera zadań podrzędnych.

1. W widoku konta usługi Batch wybierz pozycję **Zadania**  >  **Dodaj**.

1. W polu **Identyfikator zadania** wprowadź wartość *myjob*. W polu **Pula** wybierz pozycję *mypool*. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**.

## <a name="create-tasks"></a>Tworzenie zadań

Teraz wybierz zadanie, aby otworzyć **stronę** Zadania. W tym miejscu utworzysz przykładowe zadania do uruchomienia w zadaniu. Zazwyczaj tworzy się wiele zadań podrzędnych, które usługa Batch kolejkuje i dystrybuuje w celu uruchomienia w węzłach obliczeniowych. W tym przykładzie utworzysz dwa identyczne zadania podrzędne. Każde zadanie podrzędne uruchamia wiersz polecenia w celu wyświetlenia zmiennych środowiskowych usługi Batch w węźle obliczeniowym, a następnie czeka 90 sekund.

Podczas korzystania z usługi Batch aplikację lub skrypt określa się w wierszu polecenia. Usługa Batch udostępnia kilka sposobów wdrażania aplikacji i skryptów w węzłach obliczeniowych.

Aby utworzyć pierwsze zadanie podrzędne:

1. Wybierz pozycję **Dodaj**.

1. W polu **Identyfikator zadania podrzędnego** wprowadź wartość *mytask*.

1. W polu **Wiersz polecenia** wprowadź wartość `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Zachowaj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Prześlij.**

Po utworzeniu zadania podrzędnego usługa Batch umieszcza je w kolejce w celu uruchomienia w puli. Zadanie podrzędne zostanie uruchomione, gdy odpowiedni węzeł stanie się dostępny.

Aby utworzyć drugie zadanie, powtórz powyższe kroki. Wprowadź inną wartość w polu **Identyfikator zadania podrzędnego**, ale podaj identyczny wiersza polecenia. Jeśli pierwsze zadanie podrzędne jest nadal uruchomione, usługa Batch uruchomi drugie zadanie podrzędne w drugim węźle w puli.

## <a name="view-task-output"></a>Wyświetlanie danych wyjściowych zadania podrzędnego

Utworzone przykładowe zadania zostaną ukończone w ciągu kilku minut. Aby wyświetlić dane wyjściowe ukończonego zadania, wybierz zadanie, a następnie wybierz pozycję **Pliki w węźle**. Wybierz plik, `stdout.txt` aby wyświetlić standardowe dane wyjściowe zadania. Zawartość jest podobna do następującej:

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="Zrzut ekranu przedstawiający dane wyjściowe ukończonego zadania.":::

Zawartość obejmuje zmienne środowiskowe usługi Azure Batch, które są ustawione w węźle. Podczas tworzenia własnych zadań i zadań podrzędnych usługi Batch możesz odwoływać się do tych zmiennych środowiskowych w wierszach polecenia zadań podrzędnych oraz w aplikacjach i skryptach uruchamianych przez wiersze polecenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz kontynuować naukę z samouczkami i przykładami usługi Batch, skorzystaj z konta usługi Batch i powiązanego konta magazynu, które zostały utworzone w tym przewodniku Szybki start. Za samo konto usługi Batch nie są naliczane opłaty.

Opłaty za pulę są naliczane, dopóki węzły działają, nawet jeśli nie zostały zaplanowane żadne zadania. Gdy pula nie jest już potrzebna, usuń ją. W widoku konta wybierz pozycję **Pule** i nazwę puli. Następnie wybierz pozycję **Usuń**.  W przypadku usunięcia puli usuwane są również wszystkie dane wyjściowe zadań podrzędnych w węzłach.

Gdy grupa zasobów, konto usługi Batch i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. W tym celu zaznacz grupę zasobów konta usługi Batch i wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono konto usługi Batch, pulę usługi Batch i zadanie usługi Batch. Zadanie uruchomiło przykładowe zadania podrzędne i wyświetlono dane wyjściowe utworzone w jednym z węzłów. Teraz, gdy już rozumiesz kluczowe pojęcia związane z usługą Batch, możesz wypróbować tę usługę z bardziej realistycznymi obciążeniami na większą skalę. Aby dowiedzieć się więcej o usłudze Azure Batch, kontynuuj naukę w ramach samouczków usługi Azure Batch.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Batch](./tutorial-parallel-dotnet.md)
