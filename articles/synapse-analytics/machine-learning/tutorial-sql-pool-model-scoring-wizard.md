---
title: 'Samouczek: Kreator oceniania modelu uczenia maszynowego dla pul SQL'
description: Samouczek dotyczący sposobu używania Kreatora oceniania modelu uczenia maszynowego do wzbogacania danych w Synapse pule SQL
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 8e92ff75bb6a9757c06de3561a385cbcbb7f75ba
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019974"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-for-synapse-sql-pools"></a>Samouczek: Kreator oceniania modelu uczenia maszynowego dla pul Synapse SQL

Dowiedz się, jak łatwo wzbogacać dane w pulach SQL za pomocą predykcyjnych modeli uczenia maszynowego.  Modele, które są tworzone przez analityków danych, są teraz łatwo dostępne dla specjalistów ds. danych na potrzeby analizy predykcyjnej. Specjalista ds. danych w Synapse może po prostu wybrać model z rejestru modeli Azure Machine Learning, aby wdrożyć w Synapse pule SQL i uruchamiać przewidywania w celu wzbogacania danych.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> - Uczenie modelu uczenia maszynowego i rejestrowanie modelu w rejestrze Azure Machine Learning model
> - Używanie Kreatora oceniania SQL do uruchamiania prognoz w puli SQL Synapse

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar roboczy Synapse Analytics](../get-started-create-workspace.md) z kontem magazynu ADLS Gen2 skonfigurowanym jako magazyn domyślny. Musisz być **współautorem danych obiektów blob magazynu** dla systemu plików ADLS Gen2, z którym pracujesz.
- Synapse pulę SQL w obszarze roboczym analizy Synapse. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie puli SQL Synapse](../quickstart-create-sql-pool-studio.md).
- Azure Machine Learning połączoną usługę w obszarze roboczym analizy Synapse. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie połączonej usługi Azure Machine Learning w Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="train-a-model-in-azure-machine-learning"></a>Uczenie modelu w Azure Machine Learning

Przed rozpoczęciem upewnij się, że Twoja wersja programu **skryptu sklearn** to 0.20.3.

Przed uruchomieniem wszystkich komórek w notesie Sprawdź, czy wystąpienie obliczeniowe jest uruchomione.

![Weryfikuj AML COMPUTE](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Przejdź do obszaru roboczego Azure Machine Learning.

1. Pobierz [predykcyjne wskazówki dotyczące NYC taksówki. ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Uruchom obszar roboczy Azure Machine Learning w programie [Azure Machine Learning Studio](https://ml.azure.com).

1. Przejdź do **notesów** , a następnie kliknij pozycję **Przekaż pliki**, wybierz pozycję "przewidywanie NYCych wskazówek dotyczących ipynb", które zostały pobrane i przekazane.
   ![Przekazywanie pliku](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Po przekazaniu i otwarciu notesu kliknij pozycję **Uruchom wszystkie komórki**.

   Jedna z komórek może zakończyć się niepowodzeniem i zażądać uwierzytelnienia na platformie Azure. Zachowaj efekt dla tego elementu w danych wyjściowych w komórce i Uwierzytelnij się w przeglądarce, wykonując link i wprowadzając kod. Następnie uruchom ponownie Notes.

1. Notes będzie szkolić model ONNX i zarejestrować go w MLFlow. Przejdź do pozycji **modele** , aby sprawdzić, czy nowy model jest poprawnie zarejestrowany.
   ![Model w rejestrze](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. Uruchomienie notesu spowoduje również wyeksportowanie danych testowych do pliku CSV. Pobierz plik CSV do systemu lokalnego. Później należy zaimportować plik CSV do puli SQL i użyć danych do przetestowania modelu.

   Plik CSV jest tworzony w tym samym folderze, w którym znajduje się plik notesu. Kliknij przycisk "Odśwież" w Eksploratorze plików, jeśli nie widzisz go od razu.

   ![Plik CSV](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-sql-scoring-wizard"></a>Uruchom prognozowanie za pomocą Kreatora oceniania SQL

1. Otwórz obszar roboczy Synapse za pomocą programu Synapse Studio.

1. Przejdź do **danych**  ->  konta**połączonego**  ->  **magazynu**. Przekaż `test_data.csv` na domyślne konto magazynu.

   ![Przekazywanie danych](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Przejdź do **projektowania**  ->  **skryptów SQL**. Utwórz nowy skrypt SQL do załadowania `test_data.csv` do puli SQL.

   > [!NOTE]
   > Zaktualizuj adres URL pliku w tym skrypcie przed jego uruchomieniem.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Ładowanie danych do puli SQL](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Przejdź do **Data**  ->  **obszaru roboczego**dane. Otwórz Kreatora oceniania bazy danych SQL, klikając prawym przyciskiem myszy tabelę puli SQL. Wybierz **Machine Learning**  ->  **wzbogacania z istniejącym modelem**.

   > [!NOTE]
   > Opcja Uczenie maszynowe nie jest wyświetlana, jeśli nie utworzono połączonej usługi dla Azure Machine Learning (zobacz **wymagania wstępne** na początku tego samouczka).

   ![Opcja Machine Learning](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Wybierz połączony obszar roboczy Azure Machine Learning w polu listy rozwijanej. Spowoduje to załadowanie listy modeli uczenia maszynowego z rejestru modeli wybranego obszaru roboczego Azure Machine Learning. Obecnie obsługiwane są tylko modele ONNX, więc spowoduje to wyświetlenie tylko modeli ONNX.

1. Wybierz właśnie przeszkolony model, a następnie kliknij przycisk **Kontynuuj**.

   ![Wybierz model Azure Machine Learning](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Następnie zamapuj kolumny tabeli na dane wejściowe modelu i określ dane wyjściowe modelu. Jeśli model jest zapisywany w formacie MLFlow, a podpis modelu zostanie wypełniony, mapowanie zostanie wykonane automatycznie dla Ciebie przy użyciu logiki opartej na podobieństwie nazw. Interfejs obsługuje również ręczne mapowanie.

   Kliknij przycisk **Kontynuuj**.

   ![Mapowanie tabeli na model](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. Wygenerowany kod T-SQL jest opakowany wewnątrz procedury składowanej. Dlatego należy podać nazwę procedury składowanej. Plik binarny modelu, w tym metadane (wersja, opis itp.), będzie fizycznie kopiowany z Azure Machine Learning do tabeli puli SQL. W związku z tym należy określić tabelę, w której ma zostać zapisany model. Możesz wybrać opcję "Użyj istniejącej tabeli" lub "Utwórz nową tabelę". Po zakończeniu kliknij pozycję **Wdróż model + Otwórz Edytor** , aby wdrożyć model i wygenerować skrypt predykcyjny języka T-SQL.

   ![Utwórz procedurę](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Po wygenerowaniu skryptu kliknij przycisk "Uruchom", aby wykonać ocenę i uzyskać przewidywania.

   ![Przewidywania uruchamiania](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Tworzenie nowej Azure Machine Learning połączonej usługi w programie Synapse](quickstart-integrate-azure-machine-learning.md)
- [Możliwości Machine Learning na platformie Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)](what-is-machine-learning.md)
