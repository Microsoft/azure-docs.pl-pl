---
title: Uruchamianie pakietu SSIS za pomocą działania Wykonaj pakiet SSIS
description: W tym artykule opisano sposób uruchamiania pakietu usług integracji programu SQL Server (SSIS) w potoku usługi Azure Data Factory przy użyciu działania Wykonywanie pakietu SSIS.
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
ms.openlocfilehash: f505313b37d5289a5af10c40ede7f376eab4841d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605956"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Uruchamianie pakietu SSIS za pomocą działania Wykonaj pakiet SSIS w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób uruchamiania pakietu usług integracji programu SQL Server (SSIS) w potoku usługi Azure Data Factory przy użyciu działania Wykonywanie pakietu SSIS. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utwórz środowisko uruchomieniowe integracji platformy Azure-SSIS (IR), jeśli nie masz go już, postępując zgodnie z instrukcjami krok po kroku w [samouczku: inicjowanie obsługi administracyjnej usługi Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Uruchamianie pakietu w witrynie Azure portal
W tej sekcji użyj interfejsu użytkownika fabryki danych (UI) lub aplikacji, aby utworzyć potok fabryki danych z wykonaniem pakietu SSIS, który uruchamia pakiet SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Tworzenie potoku z działaniem Wykonaj pakiet SSIS
W tym kroku służy data factory interfejsu użytkownika lub aplikacji do utworzenia potoku. Dodaj wykonanie pakietu SSIS działania do potoku i skonfigurować go do uruchamiania pakietu SSIS. 

1. W omówieniu usługi Data Factory lub na stronie głównej w witrynie Azure portal wybierz kafelek **Monitor & autora,** aby uruchomić interfejs użytkownika fabryki danych lub aplikację na osobnej karcie. 

   ![Strona główna usługi Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**. 

   ![Strona Wprowadzenie](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. W **przyborniku Działania** rozwiń pozycję **Ogólne**. Następnie przeciągnij działanie **Wykonaj pakiet SSIS** na powierzchnię projektanta potoku. 

   ![Przeciągnij działanie Wykonaj pakiet SSIS na powierzchnię projektanta](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Na karcie **Ogólne** dla działania Wykonywanie pakietu SSIS podaj nazwę i opis działania. Ustaw opcjonalne wartości **limitu czasu** i **ponów próbę.**

   ![Ustawianie właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Na karcie **Ustawienia** dla działania Wykonywanie pakietu SSIS wybierz identyfikator IR azure-SSIS, w którym chcesz uruchomić pakiet. Jeśli pakiet używa uwierzytelniania systemu Windows do uzyskiwania dostępu do magazynów danych (na przykład serwerów SQL lub udziałów plików lokalnie lub usługi Azure Files), zaznacz pole wyboru **Uwierzytelnianie systemu Windows.** Wprowadź wartości poświadczeń wykonania pakietu w polach Domain , Username i Password **(Nazwa** **użytkownika**i **hasło).** 

    Alternatywnie można użyć wpisów tajnych przechowywanych w magazynie kluczy platformy Azure jako ich wartości. W tym celu zaznacz pole wyboru **AZURE KEY VAULT** obok odpowiednich poświadczeń. Wybierz lub edytuj istniejącą usługę połączony z magazynem kluczy lub utwórz nową. Następnie wybierz tajną nazwę lub wersję dla wartości poświadczeń.

    Podczas tworzenia lub edytowania usługi połączonej magazynu kluczy można zaznaczyć lub edytować istniejący magazyn kluczy lub utworzyć nową. Jeśli nie zostało to jeszcze zrobione, należy udzielić firmie Data Factory zarządzanego dostępu do tożsamości do magazynu kluczy. Możesz również wprowadzić swoje wpisy tajne `<Key vault linked service name>/<secret name>/<secret version>`bezpośrednio w następującym formacie: . Jeśli do uruchomienia pakietu potrzebujesz 32-bitowego środowiska uruchomieniowego, zaznacz pole wyboru **32-bitowe środowisko wykonawcze.**

   W przypadku **lokalizacji pakietu**wybierz **opcję SSISDB**, **System plików (pakiet)**, **System plików (projekt)** lub **Pakiet osadzony**. Jeśli wybierzesz **SSISDB** jako lokalizację pakietu, która jest automatycznie wybierana, jeśli usługa Azure-SSIS IR została aprowizowana przy pomocą katalogu SSIS (SSISDB) hostowanego przez serwer bazy danych SQL azure lub wystąpienie zarządzane, określ pakiet do uruchomienia, który został wdrożony w Usłudze SSISDB. 

    Jeśli usługa Azure-SSIS IR jest uruchomiona, a pole wyboru **Ręczne wpisy** jest wyczyszczone, przejrzyj i wybierz istniejące foldery, projekty, pakiety lub środowiska z usługi SSISDB. Wybierz **odśwież,** aby pobrać nowo dodane foldery, projekty, pakiety lub środowiska z usługi SSISDB, aby były dostępne do przeglądania i zaznaczania. Aby przeglądać lub wybrać środowiska dla wykonywania pakietów, należy wcześniej skonfigurować projekty, aby dodać te środowiska jako odwołania z tych samych folderów w obszarze SSISDB. Aby uzyskać więcej informacji, zobacz [Tworzenie i mapowanie środowisk SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   Dla **poziomu rejestrowania**wybierz wstępnie zdefiniowany zakres rejestrowania dla wykonania pakietu. Zaznacz pole wyboru **Dostosowane,** jeśli chcesz wprowadzić niestandardową nazwę rejestrowania. 

   ![Ustawianie właściwości na karcie Ustawienia — Automatyczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Jeśli usługa Azure-SSIS IR nie jest uruchomiona lub zaznaczone jest pole wyboru **Ręczne wpisy,** wprowadź ścieżki `<folder name>/<project name>/<package name>.dtsx` pakietu `<folder name>/<environment name>`i środowiska z usługi SSISDB bezpośrednio w następujących formatach: i .

   ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Jeśli jako lokalizację pakietu zostanie wybrana opcja **System plików (pakiet),** która jest automatycznie wybierana, jeśli usługa Azure-SSIS IR została aprowizowana bez usługi SSISDB, określ pakiet do uruchomienia, udostępniając ścieżkę unc (Universal Naming Convention) do pliku pakietu (`.dtsx`) w polu ścieżka **pakietu.** Na przykład jeśli przechowujesz pakiet w usłudze `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`Azure Files, jego ścieżka pakietu jest . 
   
   Jeśli pakiet zostanie skonfigurowany w oddzielnym pliku, należy również podać ścieżkę`.dtsConfig`UNC do pliku konfiguracyjnego ( ) w polu **Ścieżka konfiguracji.** Na przykład jeśli przechowujesz konfigurację w usłudze Azure Files, jej ścieżka konfiguracji to `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Jeśli jako lokalizację pakietu zostanie wybrana wybrana wybrana zostanie wybrana usługa **System plików (projekt),** określ pakiet do uruchomienia, udostępniając ścieżkę UNC do pliku projektu (`.ispac`) w polu **Ścieżka projektu** i plik pakietu (`.dtsx`) z projektu w polu Nazwa **pakietu.** Na przykład jeśli projekt jest przechowywany w usłudze Azure Files, jego ścieżka projektu to `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Następnie określ poświadczenia, aby uzyskać dostęp do plików projektu, pakietu lub konfiguracji. Jeśli wcześniej wprowadzono wartości dla poświadczeń wykonania pakietu (zobacz poprzednie), można ich ponownie użyć, zaznaczając pole wyboru **Poświadczenia wykonywania pakietu.** W przeciwnym razie wprowadź wartości poświadczeń dostępu do pakietu w polach Domain , Username i Password **(Domena**, **nazwa użytkownika**i **Hasło).** Na przykład jeśli przechowujesz projekt, pakiet lub konfigurację `Azure`w usłudze `<storage account name>`Azure Files, `<storage account key>`domeną jest nazwa użytkownika, a hasło jest . 

   Alternatywnie można użyć wpisów tajnych przechowywanych w magazynie kluczy jako ich wartości (patrz poprzedni). Poświadczenia te są używane do uzyskiwania dostępu do pakietu i pakietów podrzędnych w execute package task, wszystkie z ich własnej ścieżki lub tego samego projektu, a także konfiguracje, które obejmują te określone w pakietach. 

   Jeśli jako lokalizację pakietu zostanie wybrana **wybrana talokowanie,** przeciągnij i upuść pakiet, aby uruchomić lub **przekazać** go z folderu plików do dostarczonego pola. Pakiet zostanie automatycznie skompresowany i osadzony w ładunku aktywności. Po osadzaniu możesz **później pobrać** pakiet do edycji. Można również **parametryzować** osadzony pakiet, przypisując go do parametru potoku, który może być używany w wielu działaniach, a tym samym optymalizacji rozmiaru ładunku potoku. Jeśli twój osadzony pakiet nie jest zaszyfrowany i wykryjemy użycie w nim zadania wykonywania pakietu, pole wyboru **Wykonaj zadanie pakietu** zostanie automatycznie zaznaczone, a odpowiednie pakiety podrzędne z ich odwołaniami do systemu plików zostaną automatycznie dodane, aby również je osadzić. Jeśli nie możemy wykryć użycia zadania wykonywania pakietu, musisz ręcznie zaznaczyć pole wyboru **Wykonaj zadanie pakietu** i dodać odpowiednie pakiety podrzędne z ich odwołaniami do systemu plików jeden po drugim, aby również je osadzić. Jeśli pakiety podrzędne używają odwołań do programu SQL Server, upewnij się, że program SQL Server jest dostępny dla usługi Azure-SSIS IR.  Użycie odwołań do projektu dla pakietów podrzędnych jest obecnie nieobsługiwał.
   
   ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Jeśli podczas tworzenia pakietu za pomocą narzędzi SQL Server Data Tools użyto poziomu ochrony **EncryptAllWithPassword** lub **EncryptSensitiveWithPassword,** wprowadź wartość hasła w polu **Hasło szyfrowania.** Alternatywnie można użyć klucza tajnego przechowywanego w magazynie kluczy jako jego wartości (zobacz poprzedni). Jeśli użyto poziomu ochrony **EncryptSensitiveWithUserKey,** ponownie wejdź poufne wartości do plików konfiguracyjnych lub na kartach **Parametry SSIS,** **Menedżery połączeń**lub **Zastępowanie właściwości** (patrz później). 

   Jeśli użyto poziom ochrony **EncryptAllWithUserKey,** jest nieobsługiwane. Należy ponownie skonfigurować pakiet, aby użyć innego poziomu ochrony za `dtutil` pośrednictwem sql server data tools lub narzędzia wiersza polecenia. 
   
   Dla **poziomu rejestrowania**wybierz wstępnie zdefiniowany zakres rejestrowania dla wykonania pakietu. Zaznacz pole wyboru **Dostosowane,** jeśli chcesz wprowadzić niestandardową nazwę rejestrowania. Jeśli chcesz rejestrować wykonania pakietu poza przy użyciu standardowych dostawców dziennika, które mogą być określone w pakiecie, określ folder dziennika, podając jego ścieżkę UNC w polu **Ścieżka rejestrowania.** Na przykład jeśli przechowujesz dzienniki w usłudze `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`Azure Files, ścieżka rejestrowania jest . Podfolder jest tworzony w tej ścieżce dla każdego pojedynczego pakietu uruchamianego i nazwany po identyfikatorze uruchamiania działania Execute SSIS Package, w którym pliki dziennika są generowane co pięć minut. 
   
   Na koniec określ poświadczenia, aby uzyskać dostęp do folderu dziennika. Jeśli wcześniej wprowadzono wartości poświadczeń dostępu do pakietu (zobacz poprzednie), można ich ponownie użyć, zaznaczając pole wyboru **Poświadczenia dostępu do pakietu.** W przeciwnym razie wprowadź wartości poświadczeń dostępu do rejestrowania w polach Domain , Username i Password **(Domena**, **nazwa użytkownika**i **Hasło).** Na przykład jeśli przechowujesz dzienniki w usłudze Azure Files, domeną jest `Azure`, nazwa użytkownika jest `<storage account name>`, a hasło jest `<storage account key>`. 

    Alternatywnie można użyć wpisów tajnych przechowywanych w magazynie kluczy jako ich wartości (patrz poprzedni). Te poświadczenia są używane do przechowywania dzienników. 
   
   W przypadku wszystkich wcześniej wymienionych ścieżek UNC w pełni kwalifikowana nazwa pliku musi zawierać mniej niż 260 znaków. Nazwa katalogu musi być mniejsza niż 248 znaków.

1. Na karcie **Parametry SSIS** dla działania Wykonywanie pakietu SSIS, jeśli jest uruchomiona usługa Azure-SSIS IR, **SSISDB** jest zaznaczona jako lokalizacja pakietu, a pole wyboru **Ręczne wpisy** na karcie **Ustawienia** jest wyczyszczone, istniejące parametry SSIS w wybranym projekcie lub pakiecie z usługi SSISDB są wyświetlane w celu przypisania do nich wartości. W przeciwnym razie można je wprowadzić jeden po drugim, aby przypisać do nich wartości ręcznie. Upewnij się, że istnieją i są poprawnie wprowadzone do wykonania pakietu, aby zakończyć się pomyślnie. 
   
   Jeśli poziom ochrony **EncryptSensitiveWithUserKey** podczas tworzenia pakietu za pośrednictwem sql server data tools i **system plików (pakiet)** lub **system plików (projekt)** jest wybrany jako lokalizacja pakietu, należy również ponownie wjechać poufnych parametrów, aby przypisać wartości do nich w plikach konfiguracyjnych lub na tej karcie. 
   
   Podczas przypisywania wartości do parametrów można dodawać zawartość dynamiczną przy użyciu wyrażeń, funkcji, zmiennych systemowych fabryki danych oraz parametrów lub zmiennych potoku fabryki danych. Alternatywnie można użyć wpisów tajnych przechowywanych w magazynie kluczy jako ich wartości (patrz poprzedni).

   ![Ustawianie właściwości na karcie Parametry SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Na karcie **Menedżerowie połączeń** dla działania Wykonywanie pakietu SSIS, jeśli jest uruchomiona usługa Azure-SSIS IR, **usługa SSISDB** jest zaznaczona jako lokalizacja pakietu, a pole wyboru **Ręczne wpisy** na karcie **Ustawienia** jest wyczyszczone, istniejące menedżery połączeń w wybranym projekcie lub pakiecie z usługi SSISDB są wyświetlane w celu przypisania wartości do ich właściwości. W przeciwnym razie można wprowadzić je jeden po drugim, aby ręcznie przypisać wartości do ich właściwości. Upewnij się, że istnieją i są poprawnie wprowadzone do wykonania pakietu, aby zakończyć się pomyślnie. 
   
   Jeśli poziom ochrony **EncryptSensitiveWithUserKey** został użyty podczas tworzenia pakietu za pośrednictwem sql server data tools i **system plików (pakiet)** lub **system plików (projekt)** jest wybrany jako lokalizacja pakietu, należy również ponownie wjechać na poufnych właściwości menedżera połączeń, aby przypisać wartości do nich w plikach konfiguracyjnych lub na tej karcie. 
   
   Podczas przypisywania wartości do właściwości menedżera połączeń można dodawać zawartość dynamiczną przy użyciu wyrażeń, funkcji, zmiennych systemowych fabryki danych oraz parametrów lub zmiennych potoku fabryki danych. Alternatywnie można użyć wpisów tajnych przechowywanych w magazynie kluczy jako ich wartości (patrz poprzedni).

   ![Ustawianie właściwości na karcie Menedżerowie połączeń](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Na karcie **Zastępowanie właściwości** dla działania Wykonywanie pakietu SSIS wprowadź ścieżki istniejących właściwości w wybranym pakiecie jeden po drugim, aby ręcznie przypisać do nich wartości. Upewnij się, że istnieją i są poprawnie wprowadzone do wykonania pakietu, aby zakończyć się pomyślnie. Na przykład, aby zastąpić wartość zmiennej użytkownika, wprowadź jej ścieżkę w następującym formacie: `\Package.Variables[User::<variable name>].Value`. 
   
   Jeśli poziom ochrony **EncryptSensitiveWithUserKey** podczas tworzenia pakietu za pośrednictwem sql server data tools i **system plików (pakiet)** lub **system plików (projekt)** jest wybrany jako lokalizacja pakietu, należy również ponownie wjechać poufnych właściwości przypisać wartości do nich w plikach konfiguracyjnych lub na tej karcie. 
   
   Podczas przypisywania wartości do właściwości można dodawać zawartość dynamiczną przy użyciu wyrażeń, funkcji, zmiennych systemowych fabryki danych oraz parametrów lub zmiennych potoku fabryki danych.

   ![Ustawianie właściwości na karcie Zastępowanie właściwości](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Wartości przypisane w plikach konfiguracyjnych i na karcie **Parametry SSIS** można zastąpić za pomocą kart **Menedżery połączeń** lub **Zastępowanie właściwości.** Wartości przypisane na karcie **Menedżerowie połączeń** można również zastąpić za pomocą karty **Zastępowanie właściwości.**

1. Aby sprawdzić poprawność konfiguracji potoku, wybierz **pozycję Sprawdź poprawność** na pasku narzędzi. Aby zamknąć raport sprawdzania **>>** poprawności **rurociągu,** wybierz opcję .

1. Aby opublikować potok w fabryce danych, wybierz pozycję **Publikuj wszystko**. 

### <a name="run-the-pipeline"></a>Uruchamianie potoku
W tym kroku wyzwolić uruchomienie potoku. 

1. Aby wyzwolić uruchomienie potoku, wybierz **wyzwalacz** na pasku narzędzi i wybierz pozycję **Wyzwalaj teraz**. 

   ![Wyzwól teraz](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. W oknie **Uruchomienie potoku** wybierz pozycję **Zakończ**. 

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Widać uruchomić potoku i jego stan wraz z innymi informacjami, takimi jak **czas rozpoczęcia wykonywania.** Aby odświeżyć widok, wybierz pozycję **Odśwież**.

   ![Uruchomienia potoków](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Wybierz link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Widzisz tylko jedno działanie uruchomione, ponieważ potok ma tylko jedno działanie. Jest to wykonanie pakietu SSIS działania.

   ![Uruchomienia działania](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Uruchom następującą kwerendę względem bazy danych SSISDB na serwerze SQL, aby sprawdzić, czy pakiet został wykonany. 

   ```sql
   select * from catalog.executions
   ```

   ![Weryfikowanie wykonań pakietów](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Można również uzyskać identyfikator wykonania SSISDB z danych wyjściowych przebiegu działania potoku i użyć identyfikatora, aby sprawdzić bardziej kompleksowe dzienniki wykonywania i komunikaty o błędach w programie SQL Server Management Studio.

   ![Pobierz identyfikator wykonania.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planowanie potoku za pomocą wyzwalacza

Można również utworzyć zaplanowany wyzwalacz dla potoku, tak aby potok był uruchamiany zgodnie z harmonogramem, na przykład co godzinę lub codziennie. Na przykład zobacz [Tworzenie fabryki danych — interfejs użytkownika fabryki danych](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Uruchamianie pakietu za pomocą programu PowerShell
W tej sekcji używasz programu Azure PowerShell do utworzenia potoku fabryki danych z działaniem Wykonaj pakiet SSIS, który uruchamia pakiet SSIS. 

Zainstaluj najnowsze moduły programu Azure PowerShell, postępując zgodnie z instrukcjami krok po kroku w [temacie Jak zainstalować i skonfigurować program Azure PowerShell.](/powershell/azure/install-az-ps)

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Tworzenie fabryki danych za pomocą usługi Azure-SSIS IR
Można użyć istniejącej fabryki danych, która ma już aprowizowana usługa Azure-SSIS IR lub utworzyć nową fabrykę danych za pomocą usługi Azure-SSIS IR. Postępuj zgodnie z instrukcjami krok po kroku w [samouczku: Wdrażanie pakietów SSIS na platformie Azure za pośrednictwem programu PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Tworzenie potoku z działaniem Wykonaj pakiet SSIS 
W tym kroku utworzysz potok z działaniem Wykonaj pakiet SSIS. Działanie uruchamia pakiet SSIS. 

1. Utwórz plik JSON o nazwie *RunSSISPackagePipeline.json* w folderze *C:\ADF\RunSSISPackage* z zawartością podobną do poniższego przykładu.

   > [!IMPORTANT]
   > Zastąp nazwy obiektów, opisy i ścieżki, wartości właściwości lub parametrów, hasła i inne wartości zmiennych przed zapisaniem pliku. 
    
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

   Aby wykonać pakiety przechowywane w systemach plików, udziałach plików lub usłudze Azure Files, wprowadź wartości dla pakietu i właściwości lokalizacji dziennika w następujący sposób:

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

   Aby wykonać pakiety w projektach przechowywanych w systemach plików, udziałach plików lub usłudze Azure Files, wprowadź wartości właściwości lokalizacji pakietu w następujący sposób:

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

   Aby wykonać pakiety osadzone, wprowadź wartości właściwości lokalizacji pakietu w następujący sposób:

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

2. W programie Azure PowerShell przełącz się do folderu *C:\ADF\RunSSISPackage.*

3. Aby utworzyć potok **RunSSISPackagePipeline,** uruchom polecenie cmdlet **Set-AzDataFactoryV2Pipeline.**

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
Użyj polecenia cmdlet **Invoke-AzDataFactoryV2Pipeline** do uruchomienia potoku. Polecenie cmdlet zwraca identyfikator uruchomienia potoku w celu monitorowania w przyszłości.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku

Uruchom następujący skrypt programu PowerShell, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych. Skopiuj lub wklej następujący skrypt w oknie programu PowerShell, a następnie wybierz pozycję Enter. 

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

Potoku można również monitorować za pomocą witryny Azure portal. Aby uzyskać instrukcje krok po kroku, zobacz [Monitorowanie potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planowanie potoku za pomocą wyzwalacza
W poprzednim kroku uruchomiono potok na żądanie. Można również utworzyć wyzwalacz harmonogramu, aby uruchomić potok zgodnie z harmonogramem, na przykład co godzinę lub codziennie.

1. Utwórz plik JSON o nazwie *MyTrigger.json* w folderze *C:\ADF\RunSSISPackage* z następującą zawartością: 
        
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
    
1. W programie Azure PowerShell przełącz się do folderu *C:\ADF\RunSSISPackage.*
1. Uruchom polecenie cmdlet **Set-AzDataFactoryV2Trigger,** które tworzy wyzwalacz. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Domyślnie wyzwalacz jest w stanie zatrzymania. Uruchom wyzwalacz, uruchamiając polecenie cmdlet **Start-AzDataFactoryV2Trigger.** 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Upewnij się, że wyzwalacz jest uruchamiany przez uruchomienie polecenia cmdlet **Get-AzDataFactoryV2Trigger.** 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Uruchom następujące polecenie po następnej godzinie. Na przykład jeśli bieżąca godzina wynosi 15:25 CZASU UTC, uruchom polecenie o godzinie 16:00 CZASU UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Uruchom następującą kwerendę względem bazy danych SSISDB na serwerze SQL, aby sprawdzić, czy pakiet został wykonany. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Następne kroki
Zobacz następujący wpis w blogu:
- [Zmodernizuj i rozszerzyj przepływy pracy ETL/ELT za pomocą działań SSIS w potokach usługi Azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
