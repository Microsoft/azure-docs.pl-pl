---
title: Szybki start na platformie Azure — uruchamianie zadania usługi Batch — portal
description: Dowiedz się, jak za pomocą Azure Portal utworzyć konto w usłudze Batch, pulę węzłów obliczeniowych oraz zadanie, które uruchamia podstawowe zadania w puli.
ms.topic: quickstart
ms.date: 07/03/2018
ms.custom: mvc
ms.openlocfilehash: 4857b9ffb38cf48678f793284afaaf3bbefb7e42
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114160"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Szybki start: uruchamianie pierwszego zadania usługi Batch w witrynie Azure Portal

Ten przewodnik Szybki start przedstawia sposób tworzenia konta usługi Batch, *puli* węzłów obliczeniowych (maszyn wirtualnych) i *zadania*, które uruchamia podstawowe *zadania podrzędne* w puli, za pomocą witryny Azure Portal. Po ukończeniu tego przewodnika Szybki start będziesz rozumieć kluczowe pojęcia związane z usługą Batch, co pozwoli na wypróbowanie tej usługi z bardziej realistycznymi obciążeniami na większą skalę.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-batch-account"></a>Tworzenie konta usługi Batch

Wykonaj następujące kroki, aby utworzyć przykładowe konto usługi Batch do celów testowych. Konto usługi Batch jest wymagane w celu tworzenia pul i zadań. Jak pokazano poniżej, z kontem usługi Batch możesz powiązać konto usługi Azure Storage. Chociaż nie jest ono wymagane na potrzeby tego przewodnika Szybki start, konto magazynu jest przydatne do wdrażania aplikacji oraz przechowywania danych wejściowych i wyjściowych w przypadku większości rzeczywistych obciążeń.


1. Wybierz pozycję **Utwórz zasób** > **obliczeniowy** > **usługi Batch**. 

   ![Usługa Batch w witrynie Marketplace][marketplace_portal]

2. Wprowadź wartości w polach **Nazwa konta** i **Grupa zasobów**. Nazwa konta musi być unikatowa w obrębie wybranej **lokalizacji** platformy Azure i musi zawierać od 3 do 24 znaków, przy czym mogą to być tylko małe litery oraz cyfry. 

3. W polu **Konto magazynu** wybierz istniejące konto magazynu lub utwórz nowe.

4. Pozostaw wartości domyślne pozostałych ustawień, a następnie kliknij przycisk **Utwórz**, aby utworzyć konto.

   ![Tworzenie konta usługi Batch][account_portal]  

Po wyświetleniu komunikatu **Wdrażanie zakończyło się pomyślnie** przejdź do konta usługi Batch w portalu.

## <a name="create-a-pool-of-compute-nodes"></a>Tworzenie puli węzłów obliczeniowych

Teraz, gdy masz już konto usługi Batch, utwórz przykładową pulę węzłów obliczeniowych systemu Windows do celów testowych. Pula na potrzeby tego krótkiego przykładu składa się z 2 węzłów z obrazem systemu Windows Server 2012 R2 pochodzącym z witryny Azure Marketplace.


1. Na koncie wsadowym wybierz pozycję **Pule** > **Dodaj**.

2. W polu **Identyfikator puli** podaj wartość *mypool*. 

3. W obszarze **System operacyjny** wybierz następujące ustawienia (możesz eksplorować pozostałe opcje).
  
   |Ustawienie  |Wartość  |
   |---------|---------|
   |**Typ obrazu**|Witryna Marketplace (Linux/Windows)|
   |**Dawc**     |MicrosoftWindowsServer|
   |**Oferta**     |WindowsServer|
   |**Magazyn**     |2012-R2-Datacenter-smalldisk|

   ![Wybór systemu operacyjnego puli][pool_os] 

4. Przewiń w dół, aby wprowadzić wartości ustawień **Rozmiar węzła** i **Skala**. Sugerowany rozmiar węzłów oferuje dobry kompromis między wydajnością a kosztem na potrzeby tego krótkiego przykładu.
  
   |Ustawienie  |Wartość  |
   |---------|---------|
   |**Warstwa cenowa węzła**     |Standardowa_A1|
   |**Docelowe węzły dedykowane**     |2|

   ![Wybór rozmiaru puli][pool_size] 

5. Pozostaw wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**, aby utworzyć pulę.

Usługa Batch tworzy pulę natychmiast, ale przydzielenie i uruchomienie węzłów obliczeniowych może potrwać kilka minut. W tym czasie **Stan alokacji** puli będzie miał wartość **Zmiana rozmiaru**. Gdy trwa zmiana rozmiaru puli, możesz kontynuować samouczek, tworząc zadanie i zadania podrzędne. 

![Pula w stanie Zmiana rozmiaru][pool_resizing]

Po kilku minutach stan puli zmieni się na **Stały** i węzły zostaną uruchomione. Wybierz pozycję **Węzły**, aby sprawdzić stan węzłów. Gdy węzeł ma stan **Bezczynny**, jest gotowy do uruchamiania zadań podrzędnych. 

## <a name="create-a-job"></a>Tworzenie zadania

Teraz, gdy masz już pulę, utwórz zadanie, które zostanie w niej uruchomione. Zadanie usługi Batch to logiczna grupa zawierająca co najmniej jedno zadanie podrzędne. Zadanie uwzględnia wspólne ustawienia zadań podrzędnych, takie jak priorytet i pula, w której zadania podrzędne mają być uruchamiane. Początkowo zadanie nie zawiera zadań podrzędnych. 

1. W widoku konta wsadowego wybierz kolejno pozycje **zadania** > **Dodaj**. 

2. W polu **Identyfikator zadania** wprowadź wartość *myjob*. W polu **Pula** wybierz pozycję *mypool*. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**.

   ![Tworzenie zadania][job_create]

Po utworzeniu zadania zostanie otwarta strona **Zadania podrzędne**.

## <a name="create-tasks"></a>Tworzenie zadań podrzędnych

Teraz utwórz przykładowe zadania podrzędne, które zostaną uruchomione w zadaniu. Zazwyczaj tworzy się wiele zadań podrzędnych, które usługa Batch umieszcza w kolejce i dystrybuuje w celu uruchomienia w węzłach obliczeniowych. W tym przykładzie utworzysz dwa identyczne zadania podrzędne. Każde zadanie podrzędne uruchamia wiersz polecenia w celu wyświetlenia zmiennych środowiskowych usługi Batch w węźle obliczeniowym, a następnie czeka 90 sekund. 

Podczas korzystania z usługi Batch aplikację lub skrypt określa się w wierszu polecenia. Usługa Batch udostępnia kilka sposobów wdrażania aplikacji i skryptów w węzłach obliczeniowych. 

Aby utworzyć pierwsze zadanie podrzędne:

1. Wybierz pozycję **Dodaj**.

2. W polu **Identyfikator zadania podrzędnego** wprowadź wartość *mytask*. 

3. W polu **Wiersz polecenia** wprowadź wartość `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**.

   ![Tworzenie zadania podrzędnego][task_create]

Po utworzeniu zadania podrzędnego usługa Batch umieszcza je w kolejce w celu uruchomienia w puli. Zadanie podrzędne zostanie uruchomione, gdy odpowiedni węzeł stanie się dostępny.

Aby utworzyć drugie zadanie podrzędne, wróć do kroku 1. Wprowadź inną wartość w polu **Identyfikator zadania podrzędnego**, ale podaj identyczny wiersza polecenia. Jeśli pierwsze zadanie podrzędne jest nadal uruchomione, usługa Batch uruchomi drugie zadanie podrzędne w drugim węźle w puli.

## <a name="view-task-output"></a>Wyświetlanie danych wyjściowych zadania podrzędnego

Powyższe przykładowe zadania podrzędne zostaną ukończone w ciągu kilku minut. Aby wyświetlić dane wyjściowe ukończonego zadania podrzędnego, wybierz pozycję **Pliki w węźle**, a następnie wybierz plik `stdout.txt`. Ten plik zawiera standardowe dane wyjściowe zadania podrzędnego. Zawartość jest podobna do następującej:

![Wyświetlanie danych wyjściowych zadania podrzędnego][task_output]

Zawartość obejmuje zmienne środowiskowe usługi Azure Batch, które są ustawione w węźle. Podczas tworzenia własnych zadań i zadań podrzędnych usługi Batch możesz odwoływać się do tych zmiennych środowiskowych w wierszach polecenia zadań podrzędnych oraz w aplikacjach i skryptach uruchamianych przez wiersze polecenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz kontynuować naukę z samouczkami i przykładami usługi Batch, skorzystaj z konta usługi Batch i powiązanego konta magazynu, które zostały utworzone w tym przewodniku Szybki start. Za samo konto usługi Batch nie są naliczane opłaty.

Opłaty za pulę są naliczane, dopóki węzły działają, nawet jeśli nie zostały zaplanowane żadne zadania. Gdy pula nie jest już potrzebna, usuń ją. W widoku konta wybierz pozycję **Pule** i nazwę puli. Następnie wybierz pozycję **Usuń**.  W przypadku usunięcia puli usuwane są również wszystkie dane wyjściowe zadań podrzędnych w węzłach. 

Gdy grupa zasobów, konto usługi Batch i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. W tym celu zaznacz grupę zasobów konta usługi Batch i wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono konto usługi Batch, pulę usługi Batch i zadanie usługi Batch. Zadanie uruchomiło przykładowe zadania podrzędne i wyświetlono dane wyjściowe utworzone w jednym z węzłów. Teraz, gdy już rozumiesz kluczowe pojęcia związane z usługą Batch, możesz wypróbować tę usługę z bardziej realistycznymi obciążeniami na większą skalę. Aby dowiedzieć się więcej o usłudze Azure Batch, kontynuuj naukę w ramach samouczków usługi Azure Batch. 

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Batch](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png
