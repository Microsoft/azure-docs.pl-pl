---
title: 'Szybki start: ładowanie danych do dedykowanej puli SQL przy użyciu działania kopiowania'
description: Użyj działania kopiowania potoku w Azure Synapse Analytics, aby załadować dane do dedykowanej puli SQL.
services: synapse-analytics
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 8c0ebebd98a34a82b464707b82a650a1825676be
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566253"
---
# <a name="quickstart-load-data-into-dedicated-sql-pool-using-the-copy-activity"></a>Szybki start: ładowanie danych do dedykowanej puli SQL przy użyciu działania kopiowania

Azure Synapse Analytics oferuje różne aparaty analityczne, które ułatwiają pozyskowanie, przekształcanie, modelowanie i analizowanie danych. Dedykowana pula SQL oferuje możliwości obliczeniowe i magazynowe oparte na języku T-SQL. Po utworzeniu dedykowanej puli SQL w obszarze roboczym usługi Synapse dane można ładować, modelować, przetwarzać i dostarczać w celu szybszego wglądu w dane analityczne.

Z tego przewodnika Szybki start dowiesz się, jak załadować dane z Azure SQL Database *do Azure Synapse Analytics*. Podobne kroki można wykonać, aby skopiować dane z innych typów magazynów danych. Ten podobny przepływ dotyczy również kopiowania danych dla innych źródeł i ujść.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: jeśli nie masz subskrypcji platformy Azure, przed [rozpoczęciem](https://azure.microsoft.com/free/) utwórz bezpłatne konto.
* Azure Synapse: utwórz obszar roboczy usługi Synapse przy użyciu usługi Azure Portal zgodnie z instrukcjami w przewodniku [Szybki start: Tworzenie obszaru roboczego usługi Synapse.](quickstart-create-workspace.md)
* Azure SQL Database: ten samouczek kopiuje dane z przykładowego zestawu danych Adventure Works LT w Azure SQL Database. Możesz utworzyć tę przykładową bazę danych w programie SQL Database, korzystając z instrukcji z tematu Create a sample database in Azure SQL Database (Tworzenie przykładowej bazy [danych w programie Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)). Możesz też użyć innych magazynów danych, korzystając z podobnych kroków.
* Konto usługi Azure Storage: usługa Azure Storage jest używana jako *obszar przejściowy* w operacji kopiowania. Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md).
* Azure Synapse Analytics: dedykowana pula SQL jest magazynem danych ujścia. Jeśli nie masz wystąpienia usługi Azure Synapse Analytics, zobacz [Tworzenie dedykowanej](quickstart-create-sql-pool-portal.md) puli SQL, aby uzyskać instrukcje tworzenia tej puli.

### <a name="navigate-to-the-synapse-studio"></a>Przejdź do Synapse Studio

Po utworzeniu obszaru roboczego synapse masz dwa sposoby otwierania Synapse Studio:

* Otwórz obszar roboczy synapse w [Azure Portal](https://ms.portal.azure.com/#home). Wybierz **pozycję Otwórz** na karcie Otwórz Synapse Studio w obszarze Wprowadzenie.
* Otwórz [Azure Synapse Analytics](https://web.azuresynapse.net/) i zaloguj się do obszaru roboczego.

W tym przewodniku Szybki start jako przykładu użyjemy obszaru roboczego o nazwie "adftest2020". Spowoduje to automatyczne przejście do strony Synapse Studio głównej.

![Synapse Studio strona główna](media/doc-common-process/synapse-studio-home.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W Azure Synapse Analytics połączona usługa służy do definiowania informacji o połączeniu z innymi usługami. W tej sekcji utworzysz następujące dwa rodzaje połączonych usług: Azure SQL Database i Azure Data Lake Storage Gen2 (ADLS Gen2).

1. Na Synapse Studio strony głównej wybierz kartę **Zarządzanie** w lewym obszarze nawigacji.
1. W obszarze Połączenia zewnętrzne wybierz pozycję Połączone usługi.
  
   ![Tworzenie nowej połączonej usługi](media/doc-common-process/new-linked-service.png)

1. Aby dodać połączona usługę, wybierz pozycję **Nowa.**
1. Wybierz **Azure SQL Database** z galerii, a następnie wybierz pozycję **Kontynuuj.** Możesz wpisać "sql" w polu wyszukiwania, aby filtrować łączniki.

   ![Tworzenie nowej Azure SQL Database połączonej usługi](media/quickstart-copy-activity-load-sql-pool/new-azure-sql-linked-service.png)

1. Na stronie Nowa połączona usługa wybierz nazwę serwera i nazwę bazy danych z listy rozwijanej, a następnie określ nazwę użytkownika i hasło. Kliknij **Test connection,** aby zweryfikować ustawienia, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie Azure SQL Database połączonej usługi](media/quickstart-copy-activity-load-sql-pool/azure-sql-linked-service-configuration.png)

1. Powtórz kroki 3–4, ale wybierz **Azure Data Lake Storage Gen2** zamiast tego z galerii. Na stronie Nowa połączona usługa wybierz nazwę konta magazynu z listy rozwijanej. Kliknij **Test connection,** aby zweryfikować ustawienia, a następnie wybierz pozycję **Utwórz**. 

   ![Konfigurowanie Azure Data Lake Storage Gen2](media/quickstart-copy-activity-load-sql-pool/adls-gen2-linked-service-configuration.png)
 
## <a name="create-a-pipeline"></a>Tworzenie potoku

Potok zawiera logiczny przepływ wykonywania zestawu działań. W tej sekcji utworzysz potok zawierający działanie kopiowania, które pozysuje dane z Azure SQL Database do dedykowanej puli SQL.

1. Przejdź do karty **Integracja.** Wybierz ikonę plusa obok nagłówka potoków i wybierz pozycję Potok.

   ![Tworzenie nowego potoku](media/doc-common-process/new-pipeline.png)

1. W *obszarze Przenoszenie i przekształcanie* w *okienku Działania* przeciągnij pozycję Kopiuj **dane** na kanwę potoku.
1. Wybierz działanie kopiowania i przejdź do karty Źródło. Wybierz **pozycję Nowy,** aby utworzyć nowy źródłowy zestaw danych.

   ![Tworzenie zestawu danych źródłowych](media/quickstart-copy-activity-load-sql-pool/new-source-dataset.png)

1. Wybierz **Azure SQL Database** jako magazyn danych, a następnie wybierz pozycję **Kontynuuj.**
1. W *okienku Ustawianie właściwości* wybierz Azure SQL Database utworzoną we wcześniejszym kroku. 
1. W obszarze Nazwa tabeli wybierz przykładową tabelę do użycia w następującym działaniu kopiowania. W tym przewodniku Szybki start jako przykładu używamy tabeli "SalesLT.Customer". 

   ![Konfigurowanie właściwości zestawu danych źródłowych](media/quickstart-copy-activity-load-sql-pool/source-dataset-properties.png)
1. Po **zakończeniu wybierz** przycisk OK.
1. Wybierz działanie kopiowania i przejdź do karty Ujścia. Wybierz **pozycję Nowy,** aby utworzyć nowy zestaw danych ujścia.
1. Wybierz **Azure Synapse pulę SQL jako** magazyn danych, a następnie wybierz pozycję **Kontynuuj.**
1. W  **okienku Ustaw właściwości** wybierz pulę usługi SQL Analytics utworzoną we wcześniejszym kroku. Jeśli piszesz do istniejącej tabeli, w obszarze *Nazwa tabeli* wybierz ją z listy rozwijanej. W przeciwnym razie zaznacz pole wyboru "Edytuj" i wprowadź nazwę nowej tabeli. Po **zakończeniu wybierz** przycisk OK.
1. W przypadku ustawień zestawu danych **ujścia włącz opcję Automatycznie twórz tabelę** w polu opcji Tabela.

   ![Włączanie automatycznego tworzenia](media/quickstart-copy-activity-load-sql-pool/auto-create-table.png)

1. Na stronie **Ustawienia** zaznacz pole wyboru Włącz **przejściowe.** Ta opcja ma zastosowanie, jeśli dane źródłowe nie są zgodne z technologią PolyBase. W **sekcji Ustawienia przejściowe** wybierz Azure Data Lake Storage Gen2 utworzoną we wcześniejszym kroku jako magazyn przejściowy. 

    Magazyn jest używany do przemieszczania danych przed ich ładowaniem do Azure Synapse Analytics przy użyciu technologii PolyBase. Po zakończeniu kopiowania dane pośrednie w Azure Data Lake Storage Gen2 zostaną automatycznie wyczyszczone.

   ![Włączanie trybu przejściowego](media/quickstart-copy-activity-load-sql-pool/staging-linked-service.png)

1. Aby zweryfikować potok, wybierz pozycję **Weryfikuj** na pasku narzędzi. W prawej części strony zostanie wyświetlony wynik danych wyjściowych weryfikacji potoku. 

## <a name="debug-and-publish-the-pipeline"></a>Debugowanie i publikowanie potoku

Po zakończeniu konfigurowania potoku możesz wykonać uruchomienie debugowania przed opublikowaniem artefaktów, aby sprawdzić, czy wszystko jest poprawne.

1. Aby debugować potok, wybierz na pasku narzędzi pozycję **Debuguj**. Na karcie **Dane wyjściowe** w dolnej części okna wyświetlany jest stan uruchomienia potoku. 

   ![Debugowanie potoku](media/quickstart-copy-activity-load-sql-pool/debugging-result.png)

1. Po zakończeniu uruchomienia potoku na górnym pasku narzędzi wybierz pozycję **Opublikuj wszystko.** Ta akcja publikuje utworzone jednostki (zestawy danych i potoki) w usłudze Synapse Analytics service.
1. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**. Aby wyświetlić komunikaty powiadomień, wybierz przycisk dzwonka w prawym górnym rogu. 


## <a name="trigger-and-monitor-the-pipeline"></a>Wyzwalanie i monitorowanie potoku

W tej sekcji ręcznie wyzwolisz potok opublikowany w poprzednim kroku. 

1. Wybierz **pozycję Dodaj wyzwalacz** na pasku narzędzi, a następnie wybierz pozycję **Wyzwolij teraz.** Na stronie **Uruchamianie potoku** wybierz przycisk **OK.**  
1. Przejdź do karty **Monitorowanie** znajdującej się na lewym pasku bocznym. Widoczne jest uruchomienie potoku, które zostało wyzwolone za pomocą wyzwalacza ręcznego. 
1. Po pomyślnym zakończeniu uruchomienia potoku  wybierz link w kolumnie Nazwa potoku, aby wyświetlić szczegóły uruchomienia działania lub ponownie uruchomić potok. W tym przykładzie istnieje tylko jedno działanie, więc lista zawiera tylko jeden wpis. 
1. Aby uzyskać szczegółowe informacje o operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Nazwa** działania. Możesz monitorować szczegóły, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, kroki wykonywania z odpowiednim czasem trwania i używane konfiguracje.

   ![Szczegóły działania](media/quickstart-copy-activity-load-sql-pool/activity-details.png)

1. Aby wrócić do widoku uruchomienia potoku, wybierz link Wszystkie uruchomienia **potoku** u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę.
1. Sprawdź, czy dane są poprawnie zapisane w dedykowanej puli SQL.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującym artykułem, aby dowiedzieć się więcej o Azure Synapse Analytics pomocy technicznej:

> [!div class="nextstepaction"]
> [Potok i działania](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Omówienie łącznika](../data-factory/connector-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [działanie Kopiuj](../data-factory/copy-activity-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)