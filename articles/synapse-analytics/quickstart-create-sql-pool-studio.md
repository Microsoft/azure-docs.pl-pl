---
title: 'Szybki Start: Tworzenie puli Synapse SQL przy użyciu programu Synapse Studio'
description: Utwórz nową pulę SQL Synapse przy użyciu programu Synapse Studio, wykonując czynności opisane w tym przewodniku.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 806831ac5e965afcd076066f4baa498297a43a3e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740532"
---
# <a name="quickstart-create-a-synapse-sql-pool-using-synapse-studio"></a>Szybki Start: Tworzenie puli Synapse SQL przy użyciu programu Synapse Studio

Usługa Azure Synapse Analytics oferuje różne aparaty analityczne, które ułatwiają pozyskiwanie, przekształcanie, modelowanie i analizowanie danych. Pula SQL oferuje możliwości obliczeniowe i magazynowe oparte na języku T-SQL. Po utworzeniu puli SQL w obszarze roboczym usługi Synapse dane można ładować, modelować, przetwarzać i dostarczać w celu szybszego wglądu w informacje analityczne.

W tym przewodniku szybki start opisano kroki tworzenia puli SQL w obszarze roboczym Synapse przy użyciu programu Synapse Studio.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Obszar roboczy Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="navigate-to-the-synapse-workspace"></a>Przejdź do obszaru roboczego Synapse

1. Przejdź do obszaru roboczego Synapse, w którym zostanie utworzona Pula SQL, wpisując nazwę usługi (lub nazwę zasobu bezpośrednio) na pasku wyszukiwania.

    ![Azure Portal pasku wyszukiwania z obszarami roboczymi Synapse, które zostały wpisane.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Z listy obszarów roboczych wpisz nazwę (lub część nazwy) obszaru roboczego, który ma zostać otwarty. W tym przykładzie użyjemy obszaru roboczego o nazwie **contosoanalytics** .

    ![Lista przefiltrowanych obszarów roboczych Synapse, które zawierają nazwę contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Uruchamianie programu Synapse Studio

1. W obszarze roboczym przegląd wybierz pozycję **Uruchom program Synapse Studio** , aby otworzyć lokalizację, w której zostanie utworzona Pula SQL. Wpisz nazwę usługi lub nazwę zasobu bezpośrednio na pasku wyszukiwania.

    ![Azure Portal Omówienie obszaru roboczego Synapse z wyróżnioną opcją Uruchom Synapse Studio.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-sql-pool-in-synapse-studio"></a>Tworzenie puli SQL w programie Synapse Studio

1. Na stronie głównej programu Synapse Studio przejdź do **centrum zarządzania** w lewym okienku nawigacji, wybierając ikonę **Zarządzaj** .

    ![Strona główna programu Synapse Studio z wyróżnioną sekcją Centrum zarządzania.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Raz w centrum zarządzania przejdź do sekcji **Pule SQL** , aby wyświetlić bieżącą listę pul SQL dostępnych w obszarze roboczym.

    ![Synapse Studio Management Hub z wybraną pozycją nawigacyjną pule SQL](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Wybierz pozycję **+ nowe** polecenie, a zostanie wyświetlony Kreator tworzenia nowej puli SQL. 

    ![Synapse Studio Management Hub lista pul SQL.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Wprowadź następujące szczegóły na karcie **podstawowe** :

    | Ustawienie | Sugerowana wartość | Opis |
    | :------ | :-------------- | :---------- |
    | **Nazwa puli SQL** | contosoedw | Jest to nazwa, która będzie dostępna w puli SQL. |
    | **Poziom wydajności** | DW100c | Ustaw wartość na najmniejszy rozmiar, aby zmniejszyć koszty dla tego przewodnika Szybki Start |

    ![Pule SQL Utwórz kartę podstawowe informacje.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Należy zauważyć, że istnieją określone ograniczenia dotyczące nazw, które mogą być używane przez pule SQL. Nazwy nie mogą zawierać znaków specjalnych, muszą składać się z co najwyżej 15 znaków, nie zawierać słów zarezerwowanych i być unikatowe w obszarze roboczym.

4. Na następnej karcie **Ustawienia dodatkowe** wybierz pozycję **Brak** , aby zainicjować obsługę administracyjną puli SQL bez danych. Pozostaw domyślne sortowanie jako wybrane.

    ![Utwórz przepływ puli SQL — karta Ustawienia dodatkowe.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Nie będziemy teraz dodawać żadnych tagów, więc następnie wybierz pozycję **Recenzja + Utwórz** .

1. Na karcie **Recenzja + tworzenie** upewnij się, że szczegóły są poprawne na podstawie wprowadzonych wcześniej elementów, a następnie naciśnij pozycję **Utwórz** . 

    ![Utwórz przepływ w puli SQL — Przejrzyj kartę Ustawienia.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. W tym momencie przepływ aprowizacji zasobów zostanie uruchomiony.

1. Po zakończeniu aprowizacji przechodzenie z powrotem do obszaru roboczego będzie zawierać nowy wpis dla nowo utworzonej puli SQL.

    ![Przepływ tworzenia puli SQL — Inicjowanie obsługi zasobów.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Po utworzeniu puli SQL będzie ona dostępna w obszarze roboczym do ładowania danych, przetwarzania strumieni, odczytywania z usługi Lake itp.

## <a name="clean-up-sql-pools-using-synapse-studio"></a>Czyszczenie pul SQL przy użyciu programu Synapse Studio    

Wykonaj poniższe kroki, aby usunąć pulę SQL z obszaru roboczego przy użyciu programu Synapse Studio.
> [!WARNING]
> Usunięcie puli SQL spowoduje usunięcie aparatu analitycznego z obszaru roboczego. Nie będzie już można łączyć się z pulą, a wszystkie zapytania, potoki, skrypty używające tej puli SQL nie będą już działać.

Jeśli chcesz usunąć pulę SQL, wykonaj następujące czynności:

1. Przejdź do pul SQL w centrum zarządzania w programie Synapse Studio.
1. Wybierz wielokropek w puli SQL do usunięcia (w tym przypadku **contosoedw** ), aby wyświetlić polecenia dla puli SQL:

    ![Lista pul SQL z wybraną ostatnio utworzoną pulą.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Naciśnij klawisz **delete** .
1. Potwierdź usunięcie i naciśnij przycisk **Usuń** .
1. Po pomyślnym zakończeniu procesu Pula SQL nie będzie już wyświetlana w obszarze zasoby obszaru roboczego.

## <a name="next-steps"></a>Następne kroki 
- Zobacz [Szybki Start: Tworzenie notesu Apache Spark](quickstart-apache-spark-notebook.md).
- Zobacz [Szybki Start: Tworzenie puli Synapse SQL przy użyciu Azure Portal](quickstart-create-sql-pool-portal.md).
