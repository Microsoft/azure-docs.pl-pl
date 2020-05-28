---
title: Uruchamianie pakietu usług SSIS za pomocą działania wykonywania pakietu SSIS
description: W tym artykule opisano sposób uruchamiania pakietu SQL Server Integration Services (SSIS) w potoku Azure Data Factory przy użyciu działania wykonaj pakiet SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: d82f1d4c24820801e31c2b67b9d590367d89485c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84114765"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Uruchamianie pakietu SSIS za pomocą działania Wykonaj pakiet SSIS w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób uruchamiania pakietu SQL Server Integration Services (SSIS) w potoku Azure Data Factory przy użyciu działania wykonaj pakiet SSIS. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utwórz środowisko Azure-SSIS Integration Runtime (IR), jeśli jeszcze go nie masz, postępując zgodnie z instrukcjami krok po kroku w [samouczku: Inicjowanie obsługi Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Uruchom pakiet w Azure Portal
W tej sekcji użyjesz interfejsu użytkownika Data Factory (UI) lub aplikacji do utworzenia potoku Data Factory za pomocą działania wykonaj pakiet SSIS, które uruchamia pakiet SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Tworzenie potoku za pomocą działania wykonywania pakietu SSIS
W tym kroku użyjesz interfejsu użytkownika Data Factory lub aplikacji w celu utworzenia potoku. Dodaj do potoku działanie wykonaj pakiet SSIS i skonfiguruj je do uruchamiania pakietu usług SSIS. 

1. Na stronie Przegląd Data Factory lub Strona główna w Azure Portal wybierz kafelek **Tworzenie monitora &** , aby uruchomić Data Factory interfejs użytkownika lub aplikację na osobnej karcie. 

   ![Strona główna Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**. 

   ![Strona Wprowadzenie](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. W przyborniku **działania** rozwiń pozycję **Ogólne**. Następnie przeciągnij działanie **Wykonaj pakiet SSIS** do powierzchni projektanta potoku. 

   ![Przeciągnij działanie wykonaj pakiet SSIS na powierzchnię projektanta](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Na karcie **Ogólne** dla działania wykonaj pakiet SSIS Podaj nazwę i opis działania. Ustaw opcjonalny **limit czasu** i **Ponów** wartości.

   ![Ustawianie właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Na karcie **Ustawienia** dla działania wykonaj pakiet SSIS wybierz Azure-SSIS IR, w którym chcesz uruchomić pakiet. Jeśli pakiet używa uwierzytelniania systemu Windows w celu uzyskania dostępu do magazynów danych (na przykład serwerów SQL lub udziałów plików lokalnych lub Azure Files), zaznacz pole wyboru **uwierzytelnianie systemu Windows** . Wprowadź wartości w polach **domena**, **Nazwa użytkownika**i **hasło** . 

    Alternatywnie możesz użyć wpisów tajnych przechowywanych w magazynie kluczy platformy Azure jako ich wartości. Aby to zrobić, zaznacz pole wyboru **Magazyn kluczy Azure** obok odpowiedniego poświadczenia. Wybierz lub edytuj istniejącą połączoną usługę magazynu kluczy lub Utwórz nową. Następnie wybierz nazwę lub wersję wpisu tajnego dla wartości poświadczenia.

    Podczas tworzenia lub edytowania połączonej usługi magazynu kluczy możesz wybrać lub edytować istniejący magazyn kluczy lub utworzyć nowy. Upewnij się, że Data Factory zarządzana tożsamość do magazynu kluczy, jeśli jeszcze tego nie zrobiono. Możesz również wprowadzić wpisy tajne bezpośrednio w następującym formacie: `<Key vault linked service name>/<secret name>/<secret version>` . Jeśli pakiet wymaga 32-bitowego środowiska uruchomieniowego do uruchomienia, zaznacz pole wyboru **32-bitowe środowisko uruchomieniowe** .

   W **obszarze Lokalizacja pakietu**wybierz pozycję **SSISDB**, **system plików (pakiet)**, **system plików (projekt)** lub **pakiet osadzony**. W przypadku wybrania opcji **SSISDB** jako lokalizacji pakietu, która jest automatycznie wybierana, jeśli Azure-SSIS IR została zainicjowana przy użyciu hostowanego SQL Database lub wystąpienia zarządzanego SQL katalogu usług SSIS, określ pakiet do uruchomienia, który został wdrożony w SSISDB. 

    Jeśli Azure-SSIS IR jest uruchomiona, a pole wyboru **wpisy ręczne** jest wyczyszczone, Przeglądaj i wybierz istniejące foldery, projekty, pakiety lub środowiska z SSISDB. Wybierz pozycję **Odśwież** , aby pobrać nowo dodane foldery, projekty, pakiety lub środowiska z SSISDB, aby były dostępne do przeglądania i wyboru. Aby przeglądać lub wybierać środowiska na potrzeby wykonywania pakietów, musisz wcześniej skonfigurować projekty, aby dodać te środowiska jako odwołania z tych samych folderów w obszarze SSISDB. Aby uzyskać więcej informacji, zobacz [Tworzenie i mapowanie środowisk SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   W obszarze **poziom rejestrowania**Wybierz wstępnie zdefiniowany zakres rejestrowania dla wykonywania pakietu. Zaznacz pole wyboru **dostosowany** , jeśli chcesz wprowadzić zamiast niej dostosowanej nazwy rejestrowania. 

   ![Ustawianie właściwości na karcie Ustawienia — automatyczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Jeśli Azure-SSIS IR nie jest uruchomiona lub jest zaznaczone pole wyboru **wpisy ręczne** , wprowadź swoje ścieżki pakietu i środowiska z SSISDB bezpośrednio w następujących formatach: `<folder name>/<project name>/<package name>.dtsx` i `<folder name>/<environment name>` .

   ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Jeśli wybierzesz opcję **system plików (pakiet)** jako lokalizację pakietu, która jest automatycznie wybierana, jeśli Azure-SSIS IR została zainicjowana bez SSISDB, określ pakiet do uruchomienia, podając ścieżkę Universal NAMING Convention (UNC) do pliku pakietu ( `.dtsx` ) w polu **ścieżka pakietu** . Jeśli na przykład przechowujesz swój pakiet w Azure Files, jego ścieżka pakietu to `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` . 
   
   W przypadku skonfigurowania pakietu w osobnym pliku należy również podać ścieżkę UNC do pliku konfiguracji ( `.dtsConfig` ) w polu **ścieżka konfiguracji** . Jeśli na przykład przechowujesz konfigurację w Azure Files, jej Ścieżka konfiguracyjna to `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   W przypadku wybrania opcji **system plików (projekt)** jako lokalizacji pakietu Określ pakiet do uruchomienia, podając ścieżkę UNC do pliku projektu ( `.ispac` ) w polu **ścieżka projektu** i plik pakietu ( `.dtsx` ) z projektu w polu **Nazwa pakietu** . Na przykład jeśli projekt jest przechowywany w Azure Files, jego ścieżka projektu to `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` .

   ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Następnie określ poświadczenia, aby uzyskać dostęp do plików projektu, pakietu lub konfiguracji. Jeśli wcześniej wprowadzono wartości poświadczeń wykonywania pakietu (zobacz poprzedni), możesz użyć ich ponownie, zaznaczając pole wyboru **poświadczenia wykonywania pakietu** . W przeciwnym razie wprowadź wartości poświadczeń dostępu do pakietu w polach **domena**, **Nazwa użytkownika**i **hasło** . Na przykład w przypadku przechowywania projektu, pakietu lub konfiguracji w Azure Files domena to, `Azure` Nazwa użytkownika `<storage account name>` , a hasło to `<storage account key>` . 

   Alternatywnie możesz użyć wpisów tajnych przechowywanych w magazynie kluczy jako ich wartości (zobacz poprzedni). Te poświadczenia są używane do uzyskiwania dostępu do pakietu i pakietów podrzędnych w zadaniu pakietu, wszystkie z własnej ścieżki lub tego samego projektu, a także konfiguracje, które obejmują te określone w pakietach. 

   Jeśli wybierzesz opcję **pakiet osadzony** jako lokalizację pakietu, przeciągnij i upuść swój pakiet, aby uruchomić lub **przekazać** go z folderu plików do dostępnego pola. Pakiet zostanie automatycznie skompresowany i osadzony w ładunku aktywności. Po osadzeniu można **pobrać** pakiet później do edycji. Możesz również **Sparametryzuj** osadzony pakiet, przypisując go do parametru potoku, który może być używany w wielu działaniach, a tym samym zoptymalizować rozmiar ładunku potoku. Jeśli osadzony pakiet nie jest szyfrowany i wykryjesz w nim zadanie wykonaj pakiet, zostanie zaznaczone pole wyboru **Wykonaj zadanie wykonania pakietu** , a odpowiednie pakiety podrzędne z odwołaniami do systemu plików zostaną automatycznie dodane. Jeśli nie można wykryć użycia zadania wykonaj pakiet, musisz ręcznie zaznaczyć pole wyboru **Wykonaj zadanie pakietu** i dodać odpowiednie pakiety podrzędne wraz z ich systemem plików, do których można je również osadzić. Jeśli pakiety podrzędne używają odwołań SQL Server, upewnij się, że SQL Server jest dostępny w Azure-SSIS IR.  Użycie odwołań projektu dla pakietów podrzędnych nie jest obecnie obsługiwane.
   
   ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Jeśli używasz poziomu ochrony **EncryptAllWithPassword** lub **EncryptSensitiveWithPassword** podczas tworzenia pakietu za pomocą narzędzi SQL Server Data Tools, wprowadź wartość hasła w polu **hasło szyfrowania** . Alternatywnie możesz użyć wpisu tajnego przechowywanego w magazynie kluczy jako jego wartości (zobacz poprzedni). W przypadku użycia poziomu ochrony **EncryptSensitiveWithUserKey** należy ponownie wprowadzić wartości poufne w plikach konfiguracyjnych lub na kartach **Parametry usług SSIS**, **menedżerów połączeń**lub **zastąpień właściwości** (zobacz nowsze). 

   Jeśli użyto poziomu ochrony **EncryptAllWithUserKey** , jest on nieobsługiwany. Należy ponownie skonfigurować pakiet, aby użyć innego poziomu ochrony za pośrednictwem narzędzi SQL Server Data Tools lub `dtutil` narzędzia wiersza polecenia. 
   
   W obszarze **poziom rejestrowania**Wybierz wstępnie zdefiniowany zakres rejestrowania dla wykonywania pakietu. Zaznacz pole wyboru **dostosowany** , jeśli chcesz wprowadzić zamiast niej dostosowanej nazwy rejestrowania. Jeśli chcesz rejestrować wykonywanie pakietów poza przy użyciu standardowych dostawców dzienników, które można określić w pakiecie, określ folder dziennika, podając jego ścieżkę UNC w polu **ścieżka rejestrowania** . Na przykład w przypadku przechowywania dzienników w Azure Files ścieżka rejestrowania to `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . W tej ścieżce zostanie utworzony podfolder dla każdego pojedynczego pakietu, który jest uruchamiany po IDENTYFIKATORze uruchomienia działania pakietu SSIS, w którym pliki dziennika są generowane co pięć minut. 
   
   Na koniec określ poświadczenia, aby uzyskać dostęp do folderu dziennika. Jeśli wcześniej wprowadzono wartości poświadczeń dostępu do pakietu (zobacz poprzedni), możesz użyć ich ponownie, zaznaczając pole wyboru **poświadczenia dostępu do pakietu** . W przeciwnym razie wprowadź wartości poświadczeń dostępu do rejestrowania w polach **domena**, **Nazwa użytkownika**i **hasło** . Jeśli na przykład przechowujesz dzienniki w Azure Files, domena ma `Azure` nazwę, `<storage account name>` a hasło to `<storage account key>` . 

    Alternatywnie możesz użyć wpisów tajnych przechowywanych w magazynie kluczy jako ich wartości (zobacz poprzedni). Te poświadczenia są używane do przechowywania dzienników. 
   
   We wszystkich wyżej wymienionych ścieżkach UNC w pełni kwalifikowana nazwa pliku musi być krótsza niż 260 znaków. Nazwa katalogu musi być krótsza niż 248 znaków.

1. Na karcie **parametry programu SSIS** dla działania wykonywania pakietu SSIS, jeśli Azure-SSIS IR jest uruchomiona, **SSISDB** jest wybrane jako lokalizacja pakietu, a pole wyboru **wpisy ręczne** na karcie **Ustawienia** jest wyczyszczone, istniejące parametry usług SSIS w wybranym projekcie lub pakiecie z SSISDB są wyświetlane, aby przypisać do nich wartości. W przeciwnym razie możesz wprowadzić je pojedynczo, aby przypisać do nich wartości ręcznie. Upewnij się, że istnieją i zostały poprawnie wprowadzone w celu pomyślnego wykonania pakietu. 
   
   Jeśli podczas tworzenia pakietu użyto poziomu ochrony **EncryptSensitiveWithUserKey** za pomocą narzędzi SQL Server Data Tools i **system plików (pakiet)** lub **system plików (projekt)** jest wybrany jako lokalizacja pakietu, należy również ponownie wprowadzić poufne parametry, aby przypisać do nich wartości w plikach konfiguracyjnych lub na tej karcie. 
   
   W przypadku przypisywania wartości do parametrów można dodać zawartość dynamiczną przy użyciu wyrażeń, funkcji, Data Factory zmiennych systemowych Data Factory i parametrów potoku lub zmiennych. Alternatywnie możesz użyć wpisów tajnych przechowywanych w magazynie kluczy jako ich wartości (zobacz poprzedni).

   ![Ustawianie właściwości na karcie Parametry SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Na karcie **menedżerowie połączeń** dla działania wykonaj pakiet SSIS, jeśli Azure-SSIS IR jest uruchomiona, **SSISDB** jest wybrane jako lokalizacja pakietu, a pole wyboru **wpisy ręczne** na karcie **Ustawienia** zostanie wyczyszczone, istniejące Menedżery połączeń w wybranym projekcie lub pakiecie z SSISDB są wyświetlane, aby przypisać wartości do ich właściwości. W przeciwnym razie możesz wprowadzić je pojedynczo, aby przypisać wartości do swoich właściwości ręcznie. Upewnij się, że istnieją i zostały poprawnie wprowadzone w celu pomyślnego wykonania pakietu. 
   
   Jeśli podczas tworzenia pakietu użyto poziomu ochrony **EncryptSensitiveWithUserKey** za pomocą narzędzi SQL Server Data Tools i **system plików (pakiet)** lub **system plików (projekt)** jest wybrany jako lokalizacja pakietu, należy również ponownie wprowadzić poufne właściwości Menedżera połączeń, aby przypisać do nich wartości w plikach konfiguracyjnych lub na tej karcie. 
   
   W przypadku przypisywania wartości do właściwości Menedżera połączeń można dodać zawartość dynamiczną przy użyciu wyrażeń, funkcji, Data Factory zmiennych systemowych Data Factory i parametrów potoku lub zmiennych. Alternatywnie możesz użyć wpisów tajnych przechowywanych w magazynie kluczy jako ich wartości (zobacz poprzedni).

   ![Ustawianie właściwości na karcie menedżerów połączeń](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Na karcie **Zastępowanie właściwości** dla działania wykonaj pakiet SSIS wprowadź ścieżki istniejących właściwości w wybranym pakiecie po jednym, aby przypisać do nich wartości ręcznie. Upewnij się, że istnieją i zostały poprawnie wprowadzone w celu pomyślnego wykonania pakietu. Na przykład aby zastąpić wartość zmiennej użytkownika, wprowadź jej ścieżkę w następującym formacie: `\Package.Variables[User::<variable name>].Value` . 
   
   Jeśli podczas tworzenia pakietu użyto poziomu ochrony **EncryptSensitiveWithUserKey** za pomocą narzędzi SQL Server Data Tools i **system plików (pakiet)** lub **system plików (projekt)** jest wybrany jako lokalizacja pakietu, należy również ponownie wprowadzić poufne właściwości, aby przypisać do nich wartości w plikach konfiguracyjnych lub na tej karcie. 
   
   W przypadku przypisywania wartości do właściwości można dodać zawartość dynamiczną przy użyciu wyrażeń, funkcji, Data Factory zmiennych systemowych Data Factory i parametrów potoku lub zmiennych.

   ![Ustawianie właściwości na karcie Zastępowanie właściwości](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Wartości przypisane do plików konfiguracji i na karcie **Parametry usług SSIS** można zastąpić przy użyciu kart **menedżerów połączeń** lub **zastąpień właściwości** . Wartości przypisane na karcie **menedżerów połączeń** mogą być również zastępowane przy użyciu karty **zastąpień właściwości** .

1. Aby sprawdzić poprawność konfiguracji potoku, wybierz pozycję **Weryfikuj** na pasku narzędzi. Aby zamknąć **raport weryfikacji potoku**, wybierz opcję **>>** .

1. Aby opublikować potok do Data Factory, wybierz pozycję **Opublikuj wszystko**. 

### <a name="run-the-pipeline"></a>Uruchamianie potoku
W tym kroku zostanie wyzwolone uruchomienie potoku. 

1. Aby wyzwolić uruchomienie potoku, wybierz pozycję **Wyzwól** na pasku narzędzi, a następnie wybierz pozycję **Wyzwól teraz**. 

   ![Wyzwól teraz](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. W oknie **Uruchomienie potoku** wybierz pozycję **Zakończ**. 

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Zobaczysz uruchomienie potoku i jego stan wraz z innymi informacjami, takimi jak godzina **rozpoczęcia przebiegu** . Aby odświeżyć widok, wybierz pozycję **Odśwież**.

   ![Uruchomienia potoków](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Wybierz link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Zobaczysz tylko jedno uruchomienie działania, ponieważ potok ma tylko jedno działanie. To działanie wykonywania pakietu SSIS.

   ![Uruchomienia działania](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Uruchom następujące zapytanie względem bazy danych SSISDB w programie SQL Server, aby sprawdzić, czy pakiet został uruchomiony. 

   ```sql
   select * from catalog.executions
   ```

   ![Weryfikuj wykonania pakietu](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Możesz również uzyskać identyfikator wykonywania SSISDB z danych wyjściowych uruchomienia działania potoku i użyć identyfikatora, aby sprawdzić bardziej kompleksowe dzienniki wykonywania i komunikaty o błędach w SQL Server Management Studio.

   ![Pobierz identyfikator wykonania.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planowanie potoku przy użyciu wyzwalacza

Możesz również utworzyć zaplanowany wyzwalacz dla potoku, aby potok działał zgodnie z harmonogramem, na przykład co godzinę lub codziennie. Aby zapoznać się z przykładem, zobacz temat [Tworzenie fabryki danych — Data Factory interfejsu użytkownika](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Uruchamianie pakietu przy użyciu programu PowerShell
W tej sekcji użyjesz Azure PowerShell, aby utworzyć potok Data Factory za pomocą działania wykonywania pakietu SSIS uruchamiającego pakiet usług SSIS. 

Zainstaluj najnowsze moduły Azure PowerShell, postępując zgodnie z instrukcjami krok po kroku w temacie [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Tworzenie fabryki danych przy użyciu Azure-SSIS IR
Możesz użyć istniejącej fabryki danych, która ma już zainicjowaną Azure-SSIS IR, lub utworzyć nową fabrykę danych z Azure-SSIS IR. Postępuj zgodnie z instrukcjami krok po kroku w [samouczku: Wdrażanie pakietów usług SSIS na platformie Azure za pośrednictwem programu PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Tworzenie potoku za pomocą działania wykonywania pakietu SSIS 
W tym kroku utworzysz potok z działaniem wykonaj pakiet SSIS. Działanie uruchamia pakiet SSIS. 

1. Utwórz plik JSON o nazwie *RunSSISPackagePipeline. JSON* w folderze *C:\ADF\RunSSISPackage* o zawartości podobnej do poniższego przykładu.

   > [!IMPORTANT]
   > Przed zapisaniem pliku Zastąp nazwy obiektów, opisy i ścieżki, wartości właściwości lub parametrów, hasła i inne wartości zmiennych. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Aby wykonać pakiety przechowywane w systemach plików, w udziałach plików lub Azure Files, wprowadź wartości właściwości pakietu i lokalizacji dziennika w następujący sposób:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                           },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Aby wykonać pakiety w ramach projektów przechowywanych w systemach plików, udziałach plików lub Azure Files, wprowadź wartości dla właściwości lokalizacja pakietu w następujący sposób:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Aby wykonać pakiety osadzone, wprowadź wartości właściwości lokalizacja pakietu w następujący sposób:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. W Azure PowerShell przejdź do folderu *C:\ADF\RunSSISPackage* .

3. Aby utworzyć potok **RunSSISPackagePipeline**, uruchom polecenie cmdlet **Set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Oto przykładowe dane wyjściowe:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Uruchamianie potoku
Użyj polecenia cmdlet **Invoke-AzDataFactoryV2Pipeline** , aby uruchomić potok. Polecenie cmdlet zwraca identyfikator uruchomienia potoku w celu monitorowania w przyszłości.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku

Uruchom następujący skrypt programu PowerShell, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych. Skopiuj lub wklej następujący skrypt w oknie programu PowerShell, a następnie wybierz klawisz ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Potok można również monitorować przy użyciu Azure Portal. Instrukcje krok po kroku znajdują się w sekcji [monitorowanie potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planowanie potoku przy użyciu wyzwalacza
W poprzednim kroku uruchomiono potok na żądanie. Możesz również utworzyć wyzwalacz harmonogramu, aby uruchomić potok zgodnie z harmonogramem, na przykład co godzinę lub codziennie.

1. Utwórz plik JSON o nazwie C:\ADF\RunSSISPackage *. JSON* w folderze *C:\ADF\RunSSISPackage* o następującej zawartości: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. W Azure PowerShell przejdź do folderu *C:\ADF\RunSSISPackage* .
1. Uruchom polecenie cmdlet **Set-AzDataFactoryV2Trigger** , które tworzy wyzwalacz. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Domyślnie wyzwalacz jest w stanie zatrzymanym. Uruchom wyzwalacz, uruchamiając polecenie cmdlet **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Upewnij się, że wyzwalacz został uruchomiony, uruchamiając polecenie cmdlet **Get-AzDataFactoryV2Trigger** . 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Uruchom następujące polecenie po następnej godzinie. Na przykład, jeśli bieżący czas to 3:25 PM UTC, uruchom polecenie o godzinie 4 PM (UTC). 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Uruchom następujące zapytanie względem bazy danych SSISDB w programie SQL Server, aby sprawdzić, czy pakiet został uruchomiony. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Następne kroki
Zobacz następujący wpis w blogu:
- [Modernizowanie i zwiększanie przepływów pracy ETL/ELT przy użyciu działań SSIS w potokach Azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
