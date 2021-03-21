---
title: 'Samouczek: Kreator oceniania modelu uczenia maszynowego dla dedykowanych pul SQL'
description: Samouczek dotyczący sposobu używania Kreatora oceniania modelu uczenia maszynowego do wzbogacania danych w dedykowanych pulach SQL.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: d8db9257ad6eed98b39cd2c9a52351f013453365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935230"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>Samouczek: Kreator oceniania modelu uczenia maszynowego (wersja zapoznawcza) dla dedykowanych pul SQL

Dowiedz się, jak łatwo wzbogacać dane w dedykowane pule SQL za pomocą predykcyjnych modeli uczenia maszynowego. Modele, które są tworzone przez analityków danych, są teraz łatwo dostępne dla specjalistów ds. danych na potrzeby analizy predykcyjnej. Specjalista ds. danych w usłudze Azure Synapse Analytics może po prostu wybrać model z rejestru modeli Azure Machine Learning, aby wdrożyć go w usłudze Azure Synapse pule SQL i uruchamiać przewidywania w celu wzbogacania danych.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> - Przeszkol model uczenia maszynowego i zarejestruj model w rejestrze modelu Azure Machine Learning.
> - Użyj Kreatora oceniania SQL, aby uruchomić przewidywania w dedykowanej puli SQL.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar roboczy usługi Azure Synapse Analytics](../get-started-create-workspace.md) z kontem magazynu Azure Data Lake Storage Gen2 skonfigurowanym jako magazyn domyślny. Musisz być *współautorem danych obiektów blob magazynu* w systemie plików Data Lake Storage Gen2, z którym pracujesz.
- Dedykowana Pula SQL w obszarze roboczym usługi Azure Synapse Analytics. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie dedykowanej puli SQL](../quickstart-create-sql-pool-studio.md).
- Azure Machine Learning połączoną usługę w obszarze roboczym analizy usługi Azure Synapse. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie połączonej usługi Azure Machine Learning w usłudze Azure Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="train-a-model-in-azure-machine-learning"></a>Uczenie modelu w Azure Machine Learning

Przed rozpoczęciem upewnij się, że Twoja wersja programu skryptu sklearn to 0.20.3.

Przed uruchomieniem wszystkich komórek w notesie Sprawdź, czy wystąpienie obliczeniowe jest uruchomione.

![Zrzut ekranu pokazujący weryfikację Azure Machine Learning obliczeń.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Przejdź do obszaru roboczego Azure Machine Learning.

1. Pobierz [predykcyjne wskazówki dotyczące NYC taksówki. ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Otwórz obszar roboczy Azure Machine Learning w [Azure Machine Learning Studio](https://ml.azure.com).

1. Przejdź do **notesu**  >  **Przekaż pliki**. Następnie wybierz pobrany i **przewidywalny plik IPYNB z wskazówki dotyczącej NYC taksówki** .
   ![Zrzut ekranu przedstawiający przycisk służący do przekazywania pliku.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Po przekazaniu i otwarciu notesu zaznacz opcję **Uruchom wszystkie komórki**.

   Jedna z komórek może się nie powieść i zażądać uwierzytelnienia na platformie Azure. Obejrzyj to w danych wyjściowych w komórce i Uwierzytelnij się w przeglądarce, wykonując link i wprowadzając kod. Następnie uruchom ponownie Notes.

1. Notes będzie szkolić model ONNX i zarejestrować go w MLflow. Przejdź do pozycji **modele** , aby sprawdzić, czy nowy model jest poprawnie zarejestrowany.
   ![Zrzut ekranu pokazujący model w rejestrze.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. Uruchomienie notesu spowoduje również wyeksportowanie danych testowych do pliku CSV. Pobierz plik CSV do systemu lokalnego. Później należy zaimportować plik CSV do dedykowanej puli SQL i użyć danych do przetestowania modelu.

   Plik CSV jest tworzony w tym samym folderze, w którym znajduje się plik notesu. Wybierz pozycję **Odśwież** w Eksploratorze plików, jeśli nie widzisz jej od razu.

   ![Zrzut ekranu pokazujący plik C S.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-the-sql-scoring-wizard"></a>Uruchamianie prognoz przy użyciu kreatora oceniania SQL

1. Otwórz obszar roboczy usługi Azure Synapse za pomocą programu Synapse Studio.

1. Przejdź do pozycji **dane**  >  **połączone**  >  **konta magazynu**. Przekaż `test_data.csv` na domyślne konto magazynu.

   ![Zrzut ekranu pokazujący wybory przekazywania danych.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Przejdź do **projektowania**  >  **skryptów SQL**. Utwórz nowy skrypt SQL do załadowania `test_data.csv` do dedykowanej puli SQL.

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

   ![Ładowanie danych do dedykowanej puli SQL](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Przejdź do   >  **obszaru roboczego** dane. Otwórz Kreatora oceniania bazy danych SQL, klikając prawym przyciskiem myszy dedykowaną tabelę puli SQL. Wybierz **Machine Learning**  >  **wzbogacania z istniejącym modelem**.

   > [!NOTE]
   > Opcja Uczenie maszynowe nie jest wyświetlana, jeśli nie utworzono połączonej usługi dla Azure Machine Learning. (Zobacz [wymagania wstępne](#prerequisites) na początku tego samouczka).

   ![Zrzut ekranu pokazujący opcję uczenie maszynowego.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Wybierz połączony obszar roboczy Azure Machine Learning w polu listy rozwijanej. Ten krok umożliwia załadowanie listy modeli uczenia maszynowego z rejestru modeli wybranego obszaru roboczego Azure Machine Learning. Obecnie obsługiwane są tylko modele ONNX, więc w tym kroku są wyświetlane tylko modele ONNX.

1. Wybierz właśnie przeszkolony model, a następnie wybierz pozycję **Kontynuuj**.

   ![Zrzut ekranu, który pokazuje wybór modelu Azure Machine Learning.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Mapuj kolumny tabeli na dane wejściowe modelu i określ dane wyjściowe modelu. Jeśli model jest zapisywany w formacie MLflow i zostanie wypełniony podpis modelu, mapowanie zostanie wykonane automatycznie dla Ciebie przy użyciu logiki opartej na podobieństwie nazw. Interfejs obsługuje również ręczne mapowanie.

   Wybierz opcję **Kontynuuj**.

   ![Zrzut ekranu przedstawiający Mapowanie tabeli-do-modelu.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. Wygenerowany kod T-SQL jest opakowany wewnątrz procedury składowanej. Dlatego należy podać nazwę procedury składowanej. Plik binarny modelu, w tym metadane (wersja, opis i inne informacje), będzie fizycznie kopiowany z Azure Machine Learning do dedykowanej tabeli puli SQL. W związku z tym należy określić tabelę, w której ma zostać zapisany model. 

   Możesz wybrać **istniejącą tabelę** lub **utworzyć nową**. Gdy skończysz, wybierz pozycję **Wdróż model + Otwórz skrypt** , aby wdrożyć model i wygenerować skrypt predykcyjny języka T-SQL.

   ![Zrzut ekranu pokazujący opcje tworzenia procedury składowanej.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Po wygenerowaniu skryptu wybierz pozycję **Uruchom** , aby wykonać ocenę i uzyskać przewidywania.

   ![Zrzut ekranu przedstawiający ocenianie i przewidywania.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Tworzenie nowej Azure Machine Learning połączonej usługi w usłudze Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Możliwości uczenia maszynowego w usłudze Azure Synapse Analytics](what-is-machine-learning.md)
