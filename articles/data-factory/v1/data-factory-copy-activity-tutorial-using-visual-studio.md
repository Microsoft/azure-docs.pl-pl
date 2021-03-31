---
title: 'Samouczek: tworzenie potoku za pomocą działania kopiowania przy użyciu programu Visual Studio '
description: Ten samouczek zawiera instrukcje tworzenia potoku usługi Azure Data Factory za pomocą działania kopiowania przy użyciu programu Visual Studio.
author: linda33wj
ms.service: data-factory
ms.custom: vs-azure
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f6ff31c3c0d37078f8dfbd934593adb4b6a6ef4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377112"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Samouczek: tworzenie potoku za pomocą działania kopiowania przy użyciu programu Visual Studio
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [Program PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [Interfejs API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [Interfejs API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md). 

W tym artykule dowiesz się, jak używać Microsoft Visual Studio do tworzenia fabryki danych z potokiem, który kopiuje dane z usługi Azure Blob Storage do Azure SQL Database. Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, przed wykonaniem instrukcji z tego samouczka zapoznaj się z artykułem [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md).   

W tym samouczku opisano tworzenie potoku z jednym działaniem (Działanie kopiowania). Działanie kopiowania kopiuje dane z obsługiwanego magazynu danych do obsługiwanego magazynu danych ujścia. Aby zapoznać się z listą magazynów danych obsługiwanych jako źródła i ujścia, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Działanie jest obsługiwane przez globalnie dostępną usługę, która może kopiować dane między różnymi magazynami danych w sposób bezpieczny, niezawodny i skalowalny. Więcej informacji o działaniu kopiowania znajduje się w temacie [działania dotyczące przenoszenia danych](data-factory-data-movement-activities.md).

Potok może obejmować więcej niż jedno działanie. Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [wielu działań w potoku](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE] 
> Potok danych przedstawiony w tym samouczku kopiuje dane ze źródłowego do docelowego magazynu danych. Aby zapoznać się z samouczkiem dotyczącym przekształcania danych za pomocą usługi Azure Data Factory, zobacz [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Samouczek: Tworzenie potoku przekształcającego dane przy użyciu klastra Hadoop).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Przeczytanie artykułu [Omówienie samouczka](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) oraz wykonanie kroków **wymagań wstępnych**.       
2. Aby utworzyć wystąpienia usługi Data Factory, użytkownik musi być członkiem roli [współautora usługi Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.
3. Na komputerze muszą być zainstalowane następujące elementy: 
   * Visual Studio 2013 lub Visual Studio 2015
   * Pobierz zestaw Azure SDK dla programu Visual Studio 2013 lub Visual Studio 2015. Przejdź do [strony plików do pobrania Azure](https://azure.microsoft.com/downloads/) i kliknij pozycję **VS 2013** lub **VS 2015** w sekcji **.NET**.
   * Pobierz najnowszą wtyczkę usługi Azure Data Factory dla programu Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) lub [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Możesz także zaktualizować wtyczkę, wykonując następujące czynności: w menu kliknij kolejno opcje **Narzędzia**  ->  **rozszerzenia i aktualizacje**  ->  **online**  ->  **Galeria Visual Studio**  ->  **Microsoft Azure Data Factory Tools for Visual Studio**  ->  **Update**.

## <a name="steps"></a>Kroki

Poniżej przedstawiono kroki do wykonania w ramach tego samouczka:

1. Utworzenie **połączonych usług** w fabryce danych. Ten krok polega na utworzeniu dwóch połączonych usług: Azure Storage i Azure SQL Database. 

    Polecenie AzureStorageLinkedService łączy konto usługi Azure Storage z fabryką danych. W ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) utworzono kontener i przekazano dane na to konto magazynu.   

    Linki AzureSqlLinkedService Azure SQL Database do fabryki danych. W tej bazie danych są przechowywane dane skopiowane z magazynu obiektów blob. W ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) w tej bazie danych została utworzona tabela SQL.     

2. Utworzenie wejściowych i wyjściowych **zestawów danych** w fabryce danych.  

    Połączona usługa magazynu Azure określa parametry połączenia, z których korzysta usługa Data Factory w czasie wykonywania, aby połączyć się z kontem magazynu Azure. Natomiast wejściowy zestaw danych obiektów blob określa kontener oraz folder, który zawiera dane wejściowe.  

    Analogicznie, Azure SQL Database połączona usługa określa parametry połączenia, których usługa Data Factory używa w czasie wykonywania w celu nawiązania połączenia z Azure SQL Database. Wyjściowy zestaw danych tabeli SQL określa tabelę w bazie danych, do której są kopiowane dane z magazynu obiektów blob.

3. Utworzenie **potoku** w fabryce danych. W tym kroku jest tworzony potok za pomocą działania kopiowania.   

    Działanie kopiowania kopiuje dane z obiektu BLOB w magazynie obiektów blob platformy Azure do tabeli w Azure SQL Database. Działania kopiowania w potoku można użyć do kopiowania danych z dowolnego obsługiwanego źródła do dowolnej obsługiwanej lokalizacji docelowej. Listę obsługiwanych magazynów danych można znaleźć w artykule [Działania związane z przenoszeniem danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats).

4. Utworzenie **fabryki danych** Azure podczas wdrażania jednostek usługi Data Factory (połączone usługi, zestawy danych/tabele i potoki). 

## <a name="create-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Uruchom program **Visual Studio 2015**. Kliknij pozycję **Plik**, wskaż polecenie **Nowy** i kliknij pozycję **Projekt**. Powinno zostać wyświetlone okno dialogowe **Nowy projekt**.  

2. W oknie dialogowym **Nowy projekt** wybierz szablon **DataFactory** i kliknij pozycję **Pusty projekt usługi Fabryka danych**.  

    ![Okno dialogowe Nowy projekt](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Określ nazwę projektu oraz lokalizację i nazwę rozwiązania, a następnie kliknij przycisk **OK**.

    ![Eksplorator rozwiązań](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Tworzenie połączonych usług

Połączone usługi tworzy się w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tym samouczku nie przedstawiono korzystania z żadnych usług obliczeniowych, takich jak Azure HDInsight czy Azure Data Lake Analytics. Zostają użyte dwa magazyny danych typu Azure Storage (źródło) i Azure SQL Database (lokalizacja docelowa). 

Tak więc tworzy się dwie połączone usługi: AzureStorage i AzureSqlDatabase.  

Połączona usługa Azure Storage łączy konto magazynu Azure z fabryką danych. Na tym koncie magazynu utworzono kontener i przekazano na nie dane w ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

Linki połączonej usługi Azure SQL Azure SQL Database do fabryki danych. W tej bazie danych są przechowywane dane skopiowane z magazynu obiektów blob. Tabelę emp w tej bazie danych utworzono w ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Połączone usługi łączą magazyny danych lub usługi obliczeniowe z fabryką danych Azure. Artykuł [supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Obsługiwane magazyny danych) zawiera listę wszystkich źródeł i ujść obsługiwanych przez działanie kopiowania. Artykuł [compute linked services](data-factory-compute-linked-services.md) (Obliczanie połączonych usług) zawiera listę usług obliczeniowych obsługiwanych przez usługę Data Factory. Ten samouczek nie obejmuje używania żadnej usługi obliczeniowej. 

### <a name="create-the-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Połączone usługi**, wskaż polecenie **Dodaj** i kliknij opcję **Nowy element**.      

2. W oknie dialogowym **Dodawanie nowego elementu** wybierz z listy pozycję **Połączona usługa Azure Storage** i kliknij przycisk **Dodaj**. 

    ![Nowa połączona usługa](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)

3. Zastąp wartości `<accountname>` i `<accountkey>`* nazwą konta magazynu Azure oraz jego kluczem. 

    ![Połączona usługa Azure Storage](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Zapisz plik **AzureStorageLinkedService1.json**.

    Aby uzyskać więcej informacji na temat właściwości JSON w definicji połączonej usługi, zobacz artykuł dotyczący [łącznika usługi Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-the-azure-sql-linked-service"></a>Tworzenie połączonej usługi SQL Azure

1. Kliknij ponownie prawym przyciskiem myszy węzeł **Połączone usługi** w **Eksploratorze rozwiązań**, wskaż polecenie **Dodaj** i kliknij opcję **Nowy element**. 
2. Tym razem wybierz pozycję **Połączona usługa SQL Azure** i kliknij przycisk **Dodaj**. 
3. W **AzureSqlLinkedService1.jsw pliku** Zastąp wartości `<servername>` , `<databasename>` , `<username@servername>` i `<password>` z nazwami serwera, bazy danych, konta użytkownika i hasła.    
4. Zapisz plik **AzureSqlLinkedService1.json**. 

    Aby uzyskać więcej informacji o tych właściwościach JSON, zobacz artykuł dotyczący [łącznika usługi Azure SQL Database](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Tworzenie zestawów danych

W poprzednim kroku zostały utworzone połączone usługi, aby połączyć konto usługi Azure Storage i Azure SQL Database z fabryką danych. W tym kroku zostaną zdefiniowane dwa zestawy danych o nazwach InputDataset i OutputDataset zawierające dane wejściowe i wyjściowe przechowywane w magazynach danych, do których odwołują się usługi AzureStorageLinkedService1 i AzureSqlLinkedService1.

Połączona usługa magazynu Azure określa parametry połączenia, z których korzysta usługa Data Factory w czasie wykonywania, aby połączyć się z kontem magazynu Azure. Natomiast wejściowy zestaw danych obiektów blob (InputDataset) określa kontener oraz folder, który zawiera dane wejściowe.  

Analogicznie, Azure SQL Database połączona usługa określa parametry połączenia, których usługa Data Factory używa w czasie wykonywania w celu nawiązania połączenia z Azure SQL Database. Wyjściowy zestaw danych tabeli SQL (OutputDataset) określa tabelę w bazie danych, do której są kopiowane dane z magazynu obiektów blob. 

### <a name="create-input-dataset"></a>Tworzenie wejściowego zestawu danych

W tym kroku opisano tworzenie zestawu danych o nazwie InputDataset wskazującego na plik obiektów blob (emp.txt) w katalogu głównym kontenera obiektów blob (adftutorial) w usłudze Azure Storage reprezentowany przez połączoną usługę AzureStorageLinkedService1. Jeśli nie określisz wartości obiektu fileName lub ją pominiesz, dane ze wszystkich obiektów blob w folderze wejściowym zostaną skopiowane do lokalizacji docelowej. W tym samouczku wartość obiektu fileName jest określona. 

Używamy tutaj terminu „tabele” zamiast „zestawy danych”. Tabela jest prostokątnym zestawem danych i jest jedynym obsługiwanym obecnie typem zestawu danych. 

1. Kliknij prawym przyciskiem myszy pozycję **Tabele** w **Eksploratorze rozwiązań**, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**.
2. W oknie dialogowym **Dodawanie nowego elementu** wybierz pozycję **Obiekt blob platformy Azure** i kliknij przycisk **Dodaj**.   
3. Zastąp tekst kodu JSON następującym tekstem i zapisz plik **AzureBlobLocation1.json**. 

   ```json   
   {
    "name": "InputDataset",
    "properties": {
      "structure": [
        {
          "name": "FirstName",
          "type": "String"
        },
        {
          "name": "LastName",
          "type": "String"
        }
      ],
      "type": "AzureBlob",
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
        "format": {
          "type": "TextFormat",
          "columnDelimiter": ","
        }
      },
      "external": true,
      "availability": {
        "frequency": "Hour",
        "interval": 1
      }
    }
   }
   ``` 
    Poniższa tabela zawiera opis właściwości kodu JSON użytych w tym fragmencie kodu:

    | Właściwość | Opis |
    |:--- |:--- |
    | typ | Właściwość Type jest ustawiona na wartość **AzureBlob** , ponieważ dane znajdują się w anAzure BLOB Storage. |
    | linkedServiceName | Odwołuje się do **AzureStorageLinkedService** , który youcreated wcześniej. |
    | folderPath | Określa **kontener** obiektów blob i **folder** thatcontains wejściowe obiekty blob. W tym samouczku adftutorial jest kontenerem obiektów BLOB andfolder jest folderem głównym. | 
    | fileName | Ta właściwość jest opcjonalna. Jeśli pominięto tę właściwość, wszystkie filesfrom folderPath są pobierane. W tym samouczku **emp.txt** jest określony dla nazwy pliku, więc tylko ten plik jest pobierany do przetwarzania. |
    | format -> type |Plik wejściowy jest w formacie tekstowym, więc używamy **formatu TextFormat**. |
    | columnDelimiter | Kolumny w pliku wejściowym są rozdzielane przez **commacharacter ( `,` )**. |
    | frequency/interval | Częstotliwość jest ustawiona na wartość **godzina** , a interwał to setto **1**, co oznacza, że wycinki wejściowe są dostępne **co godzinę**. W otherwords Usługa Data Factory szuka danych wejściowych co godzinę w RootFolder kontenera obiektów BLOB (**adftutorial**) określonym przez użytkownika. Szuka thedata w czasie rozpoczęcia i zakończenia potoku, nie przed ani po thesetimes.  |
    | external | Ta właściwość ma **wartość true** , jeśli dane nie są generowane przez potok bythis. Dane wejściowe w tym samouczku są w pliku emp.txt, który nie został wygenerowany przez ten potok, więc ustawimy dla tej właściwości wartość true. |

    Aby uzyskać więcej informacji o tych właściwościach JSON, zobacz [artykuł dotyczący łącznika obiektu blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties).   

### <a name="create-output-dataset"></a>Tworzenie wyjściowego zestawu danych

W tym kroku tworzony jest wyjściowy zestaw danych o nazwie **OutputDataset**. Ten zestaw danych wskazuje tabelę SQL w Azure SQL Database reprezentowaną przez **AzureSqlLinkedService1**. 

1. Ponownie kliknij prawym przyciskiem myszy pozycję **Tabele** w **Eksploratorze rozwiązań**, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**.
2. W oknie dialogowym **Dodawanie nowego elementu** wybierz pozycję **Usługa SQL Azure** i kliknij przycisk **Dodaj**. 
3. Zastąp tekst kodu JSON następującym kodem JSON i zapisz plik **AzureSqlTableLocation1.json**.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService1",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
        }
    }
    ```

    Poniższa tabela zawiera opis właściwości kodu JSON użytych w tym fragmencie kodu:

    | Właściwość | Opis |
    |:--- |:--- |
    | typ | Właściwość Type jest ustawiona na wartość **wartość azuresqltable** , ponieważ dane są kopiowane do tabeli w Azure SQL Database. |
    | linkedServiceName | Odnosi się do utworzonego wcześniej elementu **AzureSqlLinkedService**. |
    | tableName | Określa **tabelę** , do której są kopiowane dane. | 
    | frequency/interval | Właściwość frequency (częstotliwość) jest ustawiona na wartość **Hour** (Godzina), a wartość interwału wynosi **1**, co oznacza, że wycinki wyjściowe są tworzone **co godzinę** między godziną rozpoczęcia i zakończenia potoku, a nie przed tą godziną lub po niej.  |

    Tabela emp bazy danych zawiera trzy kolumny — **ID**, **FirstName** i **LastName**. ID to kolumna tożsamości, więc należy określić tylko wartości **FirstName** i **LastName**.

    Aby uzyskać więcej informacji o tych właściwościach JSON, zobacz [artykuł dotyczący łącznika usługi Azure SQL](data-factory-azure-sql-connector.md#dataset-properties).

## <a name="create-pipeline"></a>Tworzenie potoku

W tym kroku opisano tworzenie potoku za pomocą **działania kopiowania**, w którym parametr **InputDataset** jest używany jako dane wejściowe, a parametr **OutputDataset** jako dane wyjściowe.

Obecnie harmonogram jest prowadzony przy użyciu wyjściowego zestawu danych. W tym samouczku wyjściowy zestaw danych jest konfigurowany do tworzenia wycinka co godzinę. Potok ma godzinę rozpoczęcia i zakończenia, między którymi następuje jeden dzień różnicy (dokładnie 24 godziny). Potok tworzy więc 24 wycinki wyjściowego zestawu danych. 

1. Kliknij prawym przyciskiem myszy pozycję **Potoki** w **Eksploratorze rozwiązań**, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**.  
2. Wybierz pozycję **Potok kopiowania danych** w oknie dialogowym **Dodawanie nowego elementu** i kliknij przycisk **Dodaj**. 
3. Zastąp kod JSON poniższym kodem JSON i zapisz plik **CopyActivity1.json**.

   ```json
  {
   "name": "ADFTutorialPipeline",
   "properties": {
     "description": "Copy data from a blob to Azure SQL table",
     "activities": [
       {
         "name": "CopyFromBlobToSQL",
         "type": "Copy",
         "inputs": [
           {
             "name": "InputDataset"
           }
         ],
         "outputs": [
           {
             "name": "OutputDataset"
           }
         ],
         "typeProperties": {
           "source": {
             "type": "BlobSource"
           },
           "sink": {
             "type": "SqlSink",
             "writeBatchSize": 10000,
             "writeBatchTimeout": "60:00:00"
           }
         },
         "Policy": {
           "concurrency": 1,
           "executionPriorityOrder": "NewestFirst",
           "style": "StartOfInterval",
           "retry": 0,
           "timeout": "01:00:00"
         }
       }
     ],
     "start": "2017-05-11T00:00:00Z",
     "end": "2017-05-12T00:00:00Z",
     "isPaused": false
   }
  }
    ```   
   - W sekcji działań jest tylko jedno działanie, którego parametr **type** (typ) został ustawiony na wartość **Copy**. Więcej informacji o działaniu kopiowania znajduje się w artykule dotyczącym [działań związanych z przenoszeniem danych](data-factory-data-movement-activities.md). W rozwiązaniach usługi Data Factory można również użyć [działań dotyczących przekształcania danych](data-factory-data-transformation-activities.md).
   - Dane wejściowe dla działania mają ustawienie **InputDataset**, a dane wyjściowe — **OutputDataset**. 
   - W sekcji **typeProperties** parametr **BlobSource** został określony jako typ źródłowy, a parametr **SqlSink** został określony jako typ ujścia. Aby uzyskać pełną listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia, zobacz informacje dotyczące [obsługiwanych magazynów danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aby dowiedzieć się, jak używać określonego obsługiwanego magazynu danych jako źródła/ujścia, kliknij link w tabeli.  
     
     Zastąp wartość właściwości **start** datą bieżącą, a wartość **end** datą jutrzejszą. Możesz określić tylko część daty i pominąć część godziny parametru data/godzina. Na przykład „2016-02-03” jest odpowiednikiem „2016-02-03T00:00:00Z”.
     
     Zarówno data/godzina rozpoczęcia, jak i data/godzina zakończenia muszą być w [formacie ISO](https://en.wikipedia.org/wiki/ISO_8601). Przykładowo: 2016-10-14T16:32:41Z. Czas **end** jest opcjonalny, ale w tym samouczku zostanie użyty. 
     
     Jeśli nie określisz wartości właściwości **End** , zostanie ona obliczona jako "**Start + 48 godzin**". Aby uruchomić potok bezterminowo, określ **9999-09-09** jako wartość właściwości **end**.
     
     W powyższym przykładzie występują 24 wycinki danych, gdyż poszczególne wycinki są generowane co godzinę.

     Opisy właściwości JSON w definicji potoku znajdują się w artykule dotyczącym [tworzenia potoków](data-factory-create-pipelines.md). Opisy właściwości JSON w definicji działania kopiowania znajdują się w artykule dotyczącym [działań związanych z przenoszeniem danych](data-factory-data-movement-activities.md). Opisy właściwości JSON obsługiwanych przez BlobSource można znaleźć w [artykule dotyczącym łącznika usługi Azure Blob](data-factory-azure-blob-connector.md). Opisy właściwości JSON obsługiwanych przez SqlSink można znaleźć w artykule [dotyczącym łącznika usługi Azure SQL Database](data-factory-azure-sql-connector.md).

## <a name="publishdeploy-data-factory-entities"></a>Publikowanie/wdrażanie jednostek usługi Fabryka danych
W tym kroku publikowane są utworzone wcześniej obiekty usługi Data Factory (połączone usługi, zestawy danych i potok). Należy również określić nazwę nowej fabryki danych, która zostanie utworzona w celu przechowywania tych obiektów.  

1. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie kliknij polecenie **Publikuj**. 
2. Jeśli zostanie wyświetlone okno dialogowe **Logowanie na koncie Microsoft** wprowadź poświadczenia dla konta z subskrypcją Azure i kliknij przycisk **Zaloguj**.
3. Powinno zostać wyświetlone następujące okno dialogowe:
   
   ![Okno dialogowe publikowania](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. Na stronie konfigurowania fabryki danych wykonaj następujące czynności: 
   
   1. Zaznacz opcję **Utwórz nową fabrykę danych**.
   2. Wprowadź wartość **VSTutorialFactory** w polu **Nazwa**.  
      
      > [!IMPORTANT]
      > Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli podczas publikowania wystąpi błąd dotyczący nazwy fabryki danych, zmień nazwę fabryki danych (np. twojanazwaVSTutorialFactory) i spróbuj opublikować ją ponownie. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.        
      > 
      > 
   3. Wybierz swoją subskrypcję platformy Azure w polu **Subskrypcja**.
      
      > [!IMPORTANT]
      > Jeśli nie jest widoczna żadna subskrypcja, upewnij się, że do logowania zostało użyte konto o uprawnieniach administratora lub współadministratora subskrypcji.  
      > 
      > 
   4. Wybierz **grupę zasobów** dla fabryki danych, która ma być utworzona. 
   5. Wybierz **region** dla fabryki danych. Na liście rozwijanej są wyświetlane tylko regiony obsługiwane przez usługę Data Factory.
   6. Kliknij przycisk **Dalej**, aby przejść na stronę **Publikowanie elementów**.
      
       ![Strona konfiguracji fabryki danych](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. Na stronie **Publikowanie elementów** upewnij się, że wszystkie jednostki usługi Fabryka danych zostały wybrane, i kliknij przycisk **Dalej**, aby przejść na stronę **Podsumowanie**.
   
   ![Strona publikowania elementów](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Przejrzyj podsumowanie i kliknij przycisk **Dalej**, aby rozpocząć proces wdrożenia oraz wyświetlić stronę **Stan wdrożenia**.
   
   ![Strona publikowania podsumowania](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. Na stronie **Stan wdrożenia** powinien zostać wyświetlony stan procesu wdrożenia. Po zakończeniu wdrożenia kliknij przycisk Zakończ.
 
   ![Strona stanu wdrożenia](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Pamiętaj o następujących kwestiach: 

* Jeśli zostanie wyświetlony komunikat o błędzie: „Subskrypcja nie jest zarejestrowana w celu używania przestrzeni nazw Microsoft.DataFactory”, wykonaj jedną z następujących czynności i spróbuj opublikować ponownie: 
  
  * W programie Azure PowerShell uruchom następujące polecenie, aby zarejestrować dostawcę usługi Fabryka danych. 

  ```powershell    
  Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
  Można uruchomić następujące polecenie, aby potwierdzić, że dostawca usługi Data Factory jest zarejestrowany. 
    
  ```powershell
  Get-AzResourceProvider
    ```
  * Zaloguj się przy użyciu subskrypcji Azure do [portalu Azure](https://portal.azure.com) i przejdź do bloku Fabryka danych lub utwórz fabrykę danych w portalu Azure. Ta akcja powoduje automatyczne zarejestrowanie dostawcy.
* W przyszłości nazwa fabryki danych może zostać zarejestrowana jako nazwa DNS, a wówczas stanie się widoczna publicznie.

> [!IMPORTANT]
> Aby utworzyć wystąpienia Data Factory, musisz być administratorem/współadministratorem subskrypcji platformy Azure

## <a name="monitor-pipeline"></a>Monitorowanie potoku
Przejdź do strony głównej fabryki danych:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Więcej usług** w menu po lewej stronie i kliknij pozycję **Fabryki danych**.

  ![Przeglądanie fabryk danych](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. Zacznij wpisywać nazwę fabryki danych.

  ![Nazwa fabryki danych](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. Kliknij fabrykę danych na liście wyników, aby wyświetlić stronę główną fabryki danych.

  ![Strona główna fabryki danych](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. Aby uzyskać instrukcje dotyczące monitorowania potoku i zestawów danych utworzonych w ramach tego samouczka, zobacz temat [Monitorowanie zestawów danych i potoku](data-factory-monitor-manage-pipelines.md). Obecnie program Visual Studio nie obsługuje monitorowania potoków usługi Data Factory. 

## <a name="summary"></a>Podsumowanie
W tym samouczku przedstawiono tworzenie fabryki danych Azure w celu kopiowania danych z obiektu blob platformy Azure do Azure SQL Database. Program Visual Studio został użyty do utworzenia fabryki danych, połączonych usług, zestawów danych oraz potoku. Główne kroki opisane w tym samouczku:  

1. Tworzenie **fabryki danych** Azure.
2. Tworzenie **połączonych usług**:
   1. Połączona usługa **Azure Storage** w celu połączenia z kontem usługi Azure Storage, na którym przechowywane są dane wejściowe.     
   2. Połączona usługa **Azure SQL** do łączenia bazy danych, która przechowuje dane wyjściowe. 
3. Tworzenie **zestawów danych** opisujących dane wejściowe i wyjściowe dla potoków.
4. Tworzenie **potoku** za pomocą **działania kopiowania**, w którym źródłem jest element **BlobSource**, a ujściem element **SqlSink**. 

Aby dowiedzieć się, jak używać działania programu Hive w usłudze HDInsight do przekształcania danych przy użyciu klastra usługi Azure HDInsight, zobacz [Samouczek: kompilowanie pierwszego potoku w celu przekształcenia danych przy użyciu klastra Hadoop](data-factory-build-your-first-pipeline.md).

Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Szczegółowe informacje znajdują się w artykule [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Planowanie i wykonywanie w usłudze Data Factory). 

## <a name="view-all-data-factories-in-server-explorer"></a>Wyświetlanie wszystkich fabryk danych w Eksploratorze serwera
W tej sekcji opisano, jak korzystać z Eksploratora serwera w programie Visual Studio, aby wyświetlić wszystkie fabryki danych w subskrypcji Azure i utworzyć projekt programu Visual Studio na podstawie istniejącej fabryki danych. 

1. W programie **Visual Studio** kliknij w menu pozycję **Widok**, a następnie kliknij pozycję **Eksplorator serwera**.
2. W oknie Eksploratora serwera rozwiń węzeł **Azure**, a następnie węzeł **Fabryka danych**. Jeśli zostanie wyświetlony monit **Zaloguj się do programu Visual Studio**, wprowadź **konto** skojarzone z subskrypcją Azure i kliknij przycisk **Kontynuuj**. Wprowadź **hasło** i kliknij przycisk **Zaloguj**. Program Visual Studio podejmie próbę uzyskania informacji na temat wszystkich fabryk danych Azure w ramach danej subskrypcji. Stan tej operacji zostanie wyświetlony w oknie **Lista zadań usługi Data Factory**.

    ![Eksplorator serwera](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>Tworzenie projektu programu Visual Studio dla istniejącej fabryki danych

- Kliknij prawym przyciskiem myszy fabrykę danych w Eksploratorze serwera i wybierz opcję **Eksportuj fabrykę danych do nowego projektu**, aby utworzyć projekt w programie Visual Studio na podstawie istniejącej fabryki danych.

  ![Eksportowanie fabryki danych do projektu programu VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Aktualizacja narzędzi usługi Fabryka danych dla programu Visual Studio
Aby zaktualizować narzędzia usługi Fabryka danych Azure dla programu Visual Studio, wykonaj następujące czynności:

1. W menu kliknij pozycję **Narzędzia** i wybierz pozycję **Rozszerzenia i aktualizacje**. 
2. Wybierz pozycję **Aktualizacje** w lewym okienku, a następnie wybierz pozycję **Galeria Visual Studio**.
3. Wybierz pozycję **Narzędzia usługi Fabryka danych Azure dla programu Visual Studio** i kliknij przycisk **Aktualizuj**. Jeśli ta pozycja nie jest wyświetlana, masz już najnowszą wersję narzędzi. 

## <a name="use-configuration-files"></a>Korzystanie z plików konfiguracji
Plików konfiguracji w programie Visual Studio można użyć do konfigurowania właściwości połączonych usług/tabel/potoków w różny sposób dla poszczególnych środowisk.

Weź pod uwagę poniższą definicję kodu JSON dotyczącą połączonej usługi Azure Storage. Chcesz określić parametr **connectionString** za pomocą różnych wartości elementów accountname i accountkey dla różnych środowisk (tworzenia/testowania/produkcji), w których wdrażasz jednostki usługi Fabryka danych. Możesz uzyskać takie zachowanie, stosując oddzielny plik konfiguracji dla każdego środowiska.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Dodawanie pliku konfiguracji
Dodaj plik konfiguracji dla każdego środowiska, wykonując następujące czynności:   

1. Kliknij prawym przyciskiem myszy projekt usługi Fabryka danych w rozwiązaniu Visual Studio, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**.
2. Wybierz pozycję **Konfiguracja** z listy zainstalowanych szablonów po lewej stronie, wybierz opcję **Plik konfiguracji**, wprowadź **nazwę** pliku konfiguracji, a następnie kliknij przycisk **Dodaj**.

    ![Dodawanie pliku konfiguracji](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Dodaj parametry konfiguracji oraz ich wartości w poniższym formacie:

  ```json
  {
      "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
      "AzureStorageLinkedService1": [
          {
              "name": "$.properties.typeProperties.connectionString",
              "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
          }
      ],
      "AzureSqlLinkedService1": [
          {
              "name": "$.properties.typeProperties.connectionString",
              "value":  "Server=tcp:<logical SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
          }
      ]
  }
    ```

    W tym przykładzie opisano konfigurację właściwości connectionString połączonej usługi Azure Storage oraz połączonej usługi SQL Azure. Zwróć uwagę, że do określania nazwy jest używana składnia [JsonPath](https://goessner.net/articles/JsonPath/).   

    Jeśli kod JSON zawiera właściwość obejmującą tablicę wartości, jak pokazano poniżej:  

  ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Skonfiguruj właściwości, jak pokazano w następującym pliku konfiguracji (użyj indeksowania rozpoczynającego się od zera):

  ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Nazwy właściwości zawierające spacje
Jeśli nazwa właściwości zawiera spacje, użyj nawiasów kwadratowych, jak pokazano w poniższym przykładzie (Database server name):

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Wdrożenie rozwiązania przy użyciu konfiguracji
Podczas publikowania jednostek usługi Fabryka danych Azure w programie VS możesz określić konfigurację, której chcesz użyć dla tej operacji publikowania.

Aby opublikować jednostki w projekcie usługi Azure Data Factory przy użyciu pliku konfiguracji:   

1. Kliknij prawym przyciskiem myszy projekt usługi Fabryka danych i kliknij polecenie **Publikuj**, aby wyświetlić okno dialogowe **Publikowanie elementów**.
2. Wybierz istniejącą fabrykę danych lub określ wartości do tworzenia fabryki danych na stronie **Konfigurowanie fabryki danych** i kliknij przycisk **Dalej**.   
3. Na stronie **Publikowanie elementów** dla pola **Wybierz konfigurację wdrożenia** zostanie wyświetlona lista rozwijana z dostępnymi konfiguracjami.

    ![Wybieranie pliku konfiguracji](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Wybierz **plik konfiguracji**, którego chcesz użyć, i kliknij przycisk **Dalej**.
5. Upewnij się, że nazwa pliku JSON jest wyświetlana na stronie **Podsumowanie**, i kliknij przycisk **Dalej**.
6. Kliknij przycisk **Zakończ** po zakończeniu operacji wdrożenia.

Podczas wdrażania wartości z pliku konfiguracji służą do ustawiania wartości właściwości w plikach JSON przed wdrożeniem jednostek w usłudze Azure Data Factory.   

## <a name="use-azure-key-vault"></a>Korzystanie z rozwiązania Azure Key Vault
Przekazywanie poufnych danych (na przykład parametrów połączeń) do repozytorium kodu jest niezalecane, a często nawet sprzeczne z zasadami zabezpieczeń. Zobacz przykład [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFSecurePublish) w witrynie GitHub, aby dowiedzieć się więcej o przechowywaniu poufnych informacji w usłudze Azure Key Vault i korzystaniu z nich podczas publikowania jednostek usługi Data Factory. Rozszerzenie Secure Publish dla programu Visual Studio umożliwia przechowywanie kluczy tajnych w usłudze Key Vault, a w połączonych usługach/konfiguracjach wdrażania są określane tylko odwołania do tych kluczy. Te odwołania są rozpoznawane podczas publikowania jednostek usługi Data Factory na platformie Azure. Te pliki można następnie przekazać do repozytorium źródłowego bez ujawniania jakichkolwiek kluczy tajnych.


## <a name="next-steps"></a>Następne kroki
W tym samouczku użyto magazynu obiektów blob platformy Azure jako źródłowego magazynu danych i Azure SQL Database jako docelowy magazyn danych w operacji kopiowania. Poniższa tabela zawiera listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i lokalizacje docelowe: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Aby uzyskać informacje dotyczące kopiowania danych do/z magazynu danych, kliknij link do magazynu danych w tabeli.
