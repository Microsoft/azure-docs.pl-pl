---
title: Uruchamianie notesu datakostks z działaniem
description: Dowiedz się, jak można użyć działania notesu usługi Databricks w usłudze Azure Data Factory w celu uruchomienia notesu usługi Databricks w klastrze zadań usługi Databricks.
ms.service: data-factory
ms.topic: tutorial
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 03/12/2018
ms.openlocfilehash: 20253954035798187f28c18c8207c114d27bc9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374086"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Uruchamianie notesu usługi Databricks za pomocą działania notesu usługi Databricks w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku w witrynie Azure Portal utworzysz potok usługi Azure Data Factory, który wykona notes usługi Databricks w klastrze zadań usługi Databricks. Podczas wykonywania parametry usługi Azure Data Factory będą również przekazywane do notesu usługi Databricks.

Ten samouczek obejmuje następujące procedury:

  - Tworzenie fabryki danych.

  - Tworzenie potoku, który używa działania notesu usługi Databricks.

  - Wyzwalanie uruchomienia potoku

  - Monitorowanie uruchomienia potoku.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

Poniższy klip wideo zawiera jedenastominutowe wprowadzenie i demonstrację tej funkcji:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Wymagania wstępne

  - **Azure Databricks obszar roboczy**. [Utwórz obszar roboczy usługi Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) lub użyj istniejącego. Najpierw utworzysz notes Python w obszarze roboczym usługi Azure Databricks. Następnie wykonasz notes i przekażesz do niego parametry przy użyciu usługi Azure Data Factory.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1.  Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.

1.  Wybierz pozycję **Utwórz zasób** w menu po lewej stronie, a następnie pozycje **Analiza** i **Data Factory**.

    ![Tworzenie nowej fabryki danych](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  W okienku **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**.

    Nazwa fabryki danych Azure musi być *globalnie unikatowa*. Jeśli zostanie wyświetlony poniższy błąd, zmień nazwę fabryki danych. (Na przykład użyj **\<yourname\> ADFTutorialDataFactory**). Artykuł [Usługa Data Factory — reguły nazewnictwa](./naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.

    ![Udostępnianie nazwy nowej fabryki danych](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych.

1.  W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
    
    - Wybierz pozycję **Użyj istniejącej** i wybierz istniejącą grupę zasobów z listy rozwijanej.
    
    - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.

    W niektórych krokach w tym przewodniku szybki start przyjęto założenie, że dla grupy zasobów jest używana nazwa **ADFTutorialResourceGroup** . Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).

1.  W obszarze **Wersja** wybierz pozycję **V2**.

1.  W obszarze **Lokalizacja** wybierz lokalizację fabryki danych.

    Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (na przykład Azure Storage lub Azure SQL Database) i jednostki obliczeniowe (takie jak HDInsight) używane przez usługę Data Factory mogą znajdować się w innych regionach.
1.  Wybierz przycisk **Utwórz**.


1.  Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**. Wybierz kafelek **Tworzenie i monitorowanie**, aby na osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Data Factory.

    ![Uruchamianie aplikacji interfejsu użytkownika usługi Data Factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W tej sekcji utworzysz połączoną usługę Databricks. Ta połączona usługa zawiera informacje o połączeniu z klastrem usługi Databricks:

### <a name="create-an-azure-databricks-linked-service"></a>Tworzenie połączonej usługi Azure Databricks

1.  Na stronie **Wprowadzenie** przejdź do karty **Edycja** na lewym panelu.

    ![Edytowanie nowej połączonej usługi](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Wybierz pozycję **Połączenia** w dolnej części okna, a następnie wybierz pozycję **+ Nowy**.
    
    ![Tworzenie nowego połączenia](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  W oknie **Nowa połączona usługa** wybierz pozycje **Compute** \> **Azure Databricks**, a następnie pozycję **Kontynuuj**.
    
    ![Określanie połączonej usługi Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  W oknie **Nowa połączona usługa** wykonaj następujące czynności:
    
    1.  W obszarze **Nazwa** wprowadź **_AzureDatabricks \_ LinkedService_**
    
    1.  Wybierz odpowiedni **obszar roboczy usługi Databricks**, w którym uruchomisz notes.

    1.  W pozycji **Wybierz klaster** wybierz opcję **Nowy klaster zadań**.
    
    1.  Dane w polu **Domena/region** powinny zostać wypełnione automatycznie.

    1.  W obszarze **Token dostępu** wygeneruj token na podstawie miejsca pracy usługi Azure Databricks. Procedurę można znaleźć [tutaj](https://docs.databricks.com/api/latest/authentication.html#generate-token).

    1.  W polu **Wersja klastra** wybierz opcję **4.2** (z oprogramowaniem Apache Spark 2.3.1 i Scala 2.11).

    1.  Na potrzeby tego samouczka w polu **Typ węzła klastra** wybierz opcję **Standard\_D3\_v2** w kategorii **Ogólnego przeznaczenia (HDD)**. 
    
    1.  W polu **Procesy robocze** podaj wartość **2**.
    
    1.  Wybierz **zakończenie**

        ![Kończenie tworzenia połączonej usługi](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Tworzenie potoku

1.  Wybierz **+** przycisk (znak plus), a następnie wybierz pozycję **potok** w menu.

    ![Przyciski do tworzenia nowego potoku](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Utwórz **parametr** do użycia w **potoku**. Później przekażesz ten parametr do działania notesu usługi Databricks. W pustym potoku kliknij kartę **Parametry**, a następnie wybierz pozycję **Nowy** i wpisz nazwę „**nazwa**”.

    ![Tworzenie nowego parametru](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Tworzenie parametru nazwa](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  W przyborniku **Działania** rozwiń pozycję **Databricks**. Przeciągnij działanie **Notes** z przybornika **Działania** na powierzchnię projektanta potoku.

    ![Przeciąganie notesu na powierzchnię projektanta](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  We właściwościach okna działania **Notes** **usługi Databricks** u dołu wykonaj następujące czynności:

    a. Przejdź do karty **Azure Databricks**.

    b. Wybierz usługę **AzureDatabricks\_LinkedService** (utworzoną w ramach poprzedniej procedury).

    c. Przejdź do karty **Ustawienia**.

    c. Użyj polecenia Przeglądaj, aby wybrać wartość dla pozycji **Ścieżka notesu** usługi Databricks. W tym miejscu utworzymy notes i określimy ścieżkę. Ścieżkę notesu można uzyskać, wykonując kilka następnych kroków.

       1. Uruchom obszar roboczy usługi Azure Databricks.

       1. Utwórz **nowy folder** w miejscu pracy i nadaj mu nazwę **adftutorial**.

          ![Tworzenie nowego folderu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Utwórz nowy Notes](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python), wywołaj **go w folderze** **adftutorial** , a następnie kliknij przycisk **Utwórz.**

          ![Tworzenie nowego notesu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Ustawianie właściwości nowego notesu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. W nowo utworzonym notesie „mynotebook” dodaj następujący kod:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           y = dbutils.widgets.get("input")
           print ("Param -\'input':")
           print (y)
           ```

           ![Tworzenie widżetów parametrów](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. W tym przypadku **Ścieżka notesu** to **/adftutorial/mynotebook**.

1.  Przełącz się z powrotem do **narzędzia tworzenia interfejsu użytkownika usługi Data Factory**. Przejdź do karty **Ustawienia** w obszarze **Działanie notesu Notes1**.

    a.  **Dodaj parametr** do działania notesu. Użyj tego samego parametru, który został dodany wcześniej do **potoku**.

       ![Dodawanie parametru](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  Nazwij parametr jako **dane wejściowe** i podaj wartość jako potok wyrażeń **\@ (). Parameters. Name**.

1.  Aby zweryfikować potok, wybierz przycisk **Weryfikuj** na pasku narzędzi. Aby zamknąć okno walidacji, wybierz **\>\>** przycisk (Strzałka w prawo).

    ![Weryfikowanie potoku](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Wybierz pozycję **Opublikuj wszystkie**. Interfejs użytkownika usługi Data Factory publikuje jednostki (połączone usług i potok) do usługi Azure Data Factory.

    ![Publikowanie nowych jednostek fabryki danych](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku

Wybierz pozycję **Wyzwól** na pasku narzędzi, a następnie wybierz pozycję **Wyzwól teraz**.

![Wybieranie polecenia Wyzwól teraz](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

W oknie dialogowym **Uruchamianie potoku** zostanie wyświetlona prośba o podanie parametru **nazwa**. Jako parametru użyj w tym miejscu wartości **/path/filename**. Kliknij przycisk **Zakończ.**

![Podawanie wartości parametru nazwa](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1.  Przejdź do karty **monitorowanie** . Upewnij się, że zobaczysz uruchomienie potoku. Utworzenie klastra zadań usługi Databricks, w którym jest wykonywany notes, trwa około 5–8 minut.

    ![Monitorowanie potoku](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Okresowo wybieraj pozycję **Odśwież**, aby sprawdzić stan uruchomienia potoku.

1.  Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz pozycję **Wyświetl uruchomienia działań** w kolumnie **Akcje**.

    ![Wyświetlanie uruchomień działań](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Aby wrócić do widoku uruchomień potoków, możesz wybrać link **Potoki** u góry.

## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych

Po zalogowaniu się do **obszaru roboczego usługi Azure Databricks** możesz przejść na kartę **Klastry**, gdzie jest wyświetlany stan **zadania**. Dostępne wartości to *Oczekiwanie na wykonanie, Uruchomione i Zakończone*.

![Wyświetlanie klastra zadań i zadania](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Aby uzyskać więcej szczegółów, możesz kliknąć **nazwę zadania**. Po pomyślnym uruchomieniu można sprawdzić poprawność przekazanych parametrów i dane wyjściowe notesu Python.

![Wyświetlanie szczegółów uruchomienia i danych wyjściowych](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>Następne kroki

Potok w tym przykładzie wyzwala działanie notesu usługi Databricks i przekazuje do niego parametr. W tym samouczku omówiono:

  - Tworzenie fabryki danych.

  - Tworzenie potoku, który używa działania notesu usługi Databricks.

  - Wyzwalanie uruchomienia potoku

  - Monitorowanie uruchomienia potoku.