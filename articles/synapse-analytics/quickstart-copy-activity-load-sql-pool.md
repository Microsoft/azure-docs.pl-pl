---
title: 'Szybki Start: aby załadować dane do dedykowanej puli SQL przy użyciu działania kopiowania'
description: Użyj działania kopiowania potoku w usłudze Azure Synapse Analytics, aby załadować dane do dedykowanej puli SQL.
services: synapse-analytics
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: synapse-analytics
ms.topic: quickstart
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 568c738284a3d1b54ba907a973139fbced11f139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222700"
---
# <a name="quickstart-load-data-into-dedicated-sql-pool-using-the-copy-activity"></a>Szybki Start: ładowanie danych do dedykowanej puli SQL przy użyciu działania kopiowania

Usługa Azure Synapse Analytics oferuje różne aparaty analityczne, które ułatwiają pozyskiwanie, przekształcanie, modelowanie i analizowanie danych. Dedykowana Pula SQL oferuje oparte na języku T-SQL możliwości obliczeniowe i magazynowe. Po utworzeniu dedykowanej puli SQL w obszarze roboczym usługi Synapse dane można ładować, modelować, przetwarzać i dostarczać w celu szybszego wglądu w informacje analityczne.

W tym przewodniku szybki start dowiesz się, jak *ładować dane z Azure SQL Database do usługi Azure Synapse Analytics*. Możesz wykonać podobne kroki, aby skopiować dane z innych typów magazynów danych. Ten podobny przepływ ma zastosowanie do kopiowania danych dla innych źródeł i ujścia.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Obszar roboczy usługi Azure Synapse: Tworzenie obszaru roboczego Synapse przy użyciu Azure Portal zgodnie z instrukcjami w [przewodniku szybki start: Tworzenie obszaru roboczego Synapse](quickstart-create-workspace.md).
* Azure SQL Database: w tym samouczku dane są kopiowane z przykładowego zestawu danych Adventure Works LT w Azure SQL Database. Tę przykładową bazę danych można utworzyć w SQL Database, postępując zgodnie z instrukcjami w temacie [Tworzenie przykładowej bazy danych w Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md). Można też użyć innych magazynów danych, wykonując podobne kroki.
* Konto usługi Azure Storage: usługa Azure Storage jest używana jako obszar *przejściowy* w operacji kopiowania. Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md).
* Azure Synapse Analytics: dedykowana Pula SQL jest używana jako magazyn danych ujścia. Jeśli nie masz wystąpienia usługi Azure Synapse Analytics, zapoznaj się z tematem [Tworzenie dedykowanej puli SQL](quickstart-create-sql-pool-portal.md) w celu wykonania czynności, aby ją utworzyć.

### <a name="navigate-to-the-synapse-studio"></a>Przejdź do Synapse Studio

Po utworzeniu obszaru roboczego Synapse masz dwa sposoby otwierania Synapse Studio:

* Otwórz obszar roboczy Synapse w [Azure Portal](https://ms.portal.azure.com/#home). Wybierz pozycję **Otwórz** na karcie Otwórz kartę Synapse Studio w sekcji wprowadzenie.
* Otwórz [usługę Azure Synapse Analytics](https://web.azuresynapse.net/) i zaloguj się do swojego obszaru roboczego.

W tym przewodniku szybki start użyjemy obszaru roboczego o nazwie "adftest2020". Spowoduje to automatyczne przejście na stronę główną programu Synapse Studio.

![Strona główna programu Synapse Studio](media/doc-common-process/synapse-studio-home.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W usłudze Azure Synapse Analytics, połączona Usługa polega na definiowaniu informacji o połączeniu z innymi usługami. W tej sekcji utworzysz następujące dwa rodzaje połączonych usług: Azure SQL Database i Azure Data Lake Storage Gen2 (ADLS Gen2) połączone usługi.

1. Na stronie głównej programu Synapse Studio wybierz kartę **Zarządzanie** na lewym pasku nawigacyjnym.
1. W obszarze połączenia zewnętrzne wybierz pozycję połączone usługi.
  
   ![Tworzenie nowej połączonej usługi](media/doc-common-process/new-linked-service.png)

1. Aby dodać połączoną usługę, wybierz pozycję **Nowy**.
1. Wybierz **Azure SQL Database** z galerii, a następnie wybierz pozycję **Kontynuuj**. Możesz wpisać ciąg "SQL" w polu wyszukiwania, aby filtrować łączniki.

   ![Tworzenie nowej połączonej usługi Azure SQL Database](media/quickstart-copy-activity-load-sql-pool/new-azure-sql-linked-service.png)

1. Na stronie Nowa połączona usługa wybierz nazwę serwera i nazwę bazy danych z listy rozwijanej, a następnie określ nazwę użytkownika i hasło. Kliknij przycisk **Test connection** , aby sprawdzić poprawność ustawień, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie połączonej usługi Azure SQL Database](media/quickstart-copy-activity-load-sql-pool/azure-sql-linked-service-configuration.png)

1. Powtórz kroki 3-4, ale zamiast z galerii wybierz pozycję **Azure Data Lake Storage Gen2** . Na stronie Nowa połączona usługa wybierz nazwę konta magazynu z listy rozwijanej. Kliknij przycisk **Test connection** , aby sprawdzić poprawność ustawień, a następnie wybierz pozycję **Utwórz**. 

   ![Konfigurowanie Azure Data Lake Storage Gen2](media/quickstart-copy-activity-load-sql-pool/adls-gen2-linked-service-configuration.png)
 
## <a name="create-a-pipeline"></a>Tworzenie potoku

Potok zawiera przepływ logiczny dla wykonywania zestawu działań. W tej sekcji utworzysz potok zawierający działanie kopiowania, które pobiera dane z Azure SQL Database do dedykowanej puli SQL.

1. Przejdź do karty **integracja** . Wybierz ikonę znaku plus obok nagłówka potoki i wybierz pozycję potok.

   ![Tworzenie nowego potoku](media/doc-common-process/new-pipeline.png)

1. W obszarze *Przenieś i Przekształć* w okienku *działania* przeciągnij pozycję **Kopiuj dane** na kanwę potoku.
1. Wybierz działanie Kopiuj i przejdź do karty Źródło. Wybierz pozycję **Nowy** , aby utworzyć nowy źródłowy zestaw danych.

   ![Tworzenie zestawu danych źródłowych](media/quickstart-copy-activity-load-sql-pool/new-source-dataset.png)

1. Wybierz **Azure SQL Database** jako magazyn danych, a następnie wybierz pozycję **Kontynuuj**.
1. W okienku *Ustawianie właściwości* wybierz połączoną usługę Azure SQL Database utworzoną w poprzednim kroku. 
1. W obszarze Nazwa tabeli wybierz przykładową tabelę, która ma być używana w następującym działaniu kopiowania. W tym przewodniku szybki start użyjemy tabeli "tabeli SalesLT. Customer" jako przykładu. 

   ![Konfigurowanie właściwości źródłowego zestawu danych](media/quickstart-copy-activity-load-sql-pool/source-dataset-properties.png)
1. Po zakończeniu wybierz **przycisk OK** .
1. Wybierz działanie kopiowania i przejdź do karty ujścia. Wybierz pozycję **Nowy** , aby utworzyć nowy zestaw danych ujścia.
1. Wybierz **dedykowaną pulę SQL Synapse platformy Azure** jako magazyn danych, a następnie wybierz pozycję **Kontynuuj**.
1. W okienku  **Ustawianie właściwości** wybierz pulę usługi SQL Analytics utworzoną w ramach wcześniejszego kroku. Jeśli zapisujesz do istniejącej tabeli, w obszarze *Nazwa tabeli* wybierz ją z listy rozwijanej. W przeciwnym razie zaznacz opcję "Edytuj" i wprowadź nową nazwę tabeli. Po zakończeniu wybierz **przycisk OK** .
1. Dla ustawień zestawu danych ujścia, Włącz **AutoCreate Table** w polu opcji tabeli.

   ![Włącz tworzenie AutoCreate](media/quickstart-copy-activity-load-sql-pool/auto-create-table.png)

1. Na stronie **Ustawienia** zaznacz pole wyboru **Włącz przemieszczanie**. Ta opcja ma zastosowanie, jeśli dane źródłowe nie są zgodne z bazą danych. W sekcji **Ustawienia przemieszczania** wybierz połączoną usługę Azure Data Lake Storage Gen2 utworzoną wcześniej jako magazyn tymczasowy. 

    Magazyn jest używany do przemieszczania danych przed załadowaniem ich do usługi Azure Synapse Analytics przy użyciu bazy. Po zakończeniu kopiowania zostanie automatycznie oczyszczone dane tymczasowe w Azure Data Lake Storage Gen2.

   ![Włączanie trybu przejściowego](media/quickstart-copy-activity-load-sql-pool/staging-linked-service.png)

1. Aby zweryfikować potok, wybierz pozycję **Weryfikuj** na pasku narzędzi. Wynik weryfikacji potoku zostanie wyświetlony po prawej stronie strony. 

## <a name="debug-and-publish-the-pipeline"></a>Debugowanie i publikowanie potoku

Po zakończeniu konfigurowania potoku możesz wykonać przebieg debugowania przed opublikowaniem artefaktów, aby upewnić się, że wszystkie elementy są poprawne.

1. Aby debugować potok, wybierz na pasku narzędzi pozycję **Debuguj**. Na karcie **Dane wyjściowe** w dolnej części okna wyświetlany jest stan uruchomienia potoku. 

   ![Debugowanie potoku](media/quickstart-copy-activity-load-sql-pool/debugging-result.png)

1. Po pomyślnym uruchomieniu potoku na górnym pasku narzędzi wybierz pozycję **Opublikuj wszystko**. Ta akcja powoduje opublikowanie jednostek (zestawów danych i potoków) utworzonych w usłudze Synapse Analytics.
1. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**. Aby wyświetlić komunikaty powiadomień, wybierz przycisk dzwonka w prawym górnym rogu. 


## <a name="trigger-and-monitor-the-pipeline"></a>Wyzwalanie i monitorowanie potoku

W tej sekcji ręcznie wyzwolimy potok opublikowany w poprzednim kroku. 

1. Na pasku narzędzi wybierz pozycję **Dodaj wyzwalacz** , a następnie wybierz pozycję **Wyzwól teraz**. Na stronie **uruchomienie potoku** wybierz pozycję **OK**.  
1. Przejdź do karty **monitorowanie** znajdującej się na lewym pasku bocznym. Widoczne jest uruchomienie potoku, które zostało wyzwolone za pomocą wyzwalacza ręcznego. 
1. Po pomyślnym zakończeniu przebiegu potoku wybierz link w kolumnie **Nazwa potoku** , aby wyświetlić szczegóły uruchomienia działania lub aby ponownie uruchomić potok. W tym przykładzie istnieje tylko jedno działanie, dlatego na liście jest widoczny tylko jeden wpis. 
1. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **szczegóły** (ikona okularów) w kolumnie **Nazwa działania** . Możesz monitorować szczegóły, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, etapy wykonywania z odpowiednim czasem trwania i używane konfiguracje.

   ![Szczegóły działania](media/quickstart-copy-activity-load-sql-pool/activity-details.png)

1. Aby przełączyć się z powrotem do widoku uruchomienia potoków, wybierz link **wszystkie uruchomienia potoku** u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę.
1. Sprawdź, czy dane są poprawnie zapisywane w dedykowanej puli SQL.


## <a name="next-steps"></a>Następne kroki

Przejdź do poniższego artykułu, aby dowiedzieć się więcej o obsłudze usługi Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Potok i działania](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Omówienie](../data-factory/connector-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  łącznika [Działanie kopiowania](../data-factory/copy-activity-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)