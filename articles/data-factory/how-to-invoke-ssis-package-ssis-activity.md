---
title: Uruchamianie pakietu usług SSIS za pomocą działania wykonywania pakietu SSIS
description: W tym artykule opisano sposób uruchamiania pakietu SQL Server Integration Services (SSIS) w potoku Azure Data Factory przy użyciu działania wykonaj pakiet SSIS.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: 40e7d5c11a2ebc62e59c3d5d20dfefe18a33f9a7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100391613"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Uruchamianie pakietu SSIS za pomocą działania Wykonaj pakiet SSIS w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób uruchamiania pakietu SQL Server Integration Services (SSIS) w potoku Azure Data Factory przy użyciu działania wykonaj pakiet SSIS. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utwórz środowisko Azure-SSIS Integration Runtime (IR), jeśli jeszcze go nie masz, postępując zgodnie z instrukcjami krok po kroku w [samouczku: Inicjowanie obsługi Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md).

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

   Wybierz obiekt działania wykonaj pakiet SSIS, aby skonfigurować jego właściwości **Ogólne**, **Ustawienia**, **parametry programu SSIS**, **menedżerów połączeń** i **zastąpień** .

#### <a name="general-tab"></a>Karta Ogólne

Na karcie **Ogólne** działania wykonaj pakiet SSIS wykonaj następujące czynności.

![Ustawianie właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. W polu **Nazwa** wprowadź nazwę działania Uruchom pakiet usług SSIS.

   1. W polu **Opis** wprowadź opis działania wykonywania pakietu usług SSIS.

   1. W polu **limit czasu** wprowadź maksymalną ilość czasu uruchamiania działania pakietu SSIS. Wartość domyślna to 7 dni, format to D. HH: MM: SS.

   1. Aby **ponowić próbę**, wprowadź maksymalną liczbę ponownych prób dla działania wykonywania pakietu usług SSIS.

   1. Dla **interwału ponawiania prób** wprowadź liczbę sekund między kolejnymi próbami ponowienia działania pakietu usług SSIS. Wartość domyślna to 30 sekund.

   1. Zaznacz pole wyboru **bezpieczne dane wyjściowe** , aby określić, czy dane wyjściowe działania wykonywania pakietu usług SSIS mają zostać wykluczone z rejestrowania.

   1. Zaznacz pole wyboru **bezpieczne wprowadzanie** , aby wybrać, czy chcesz wykluczyć dane wejściowe działania wykonywania pakietu SSIS z rejestrowania.

#### <a name="settings-tab"></a>Karta Ustawienia

Na karcie **Ustawienia** działania wykonaj pakiet SSIS wykonaj następujące czynności.

![Ustawianie właściwości na karcie Ustawienia — automatyczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. W obszarze **Azure-SSIS IR** wybierz wyznaczono Azure-SSIS IR, aby uruchomić działanie wykonywania pakietu SSIS.

   1. W polu **Opis** wprowadź opis działania wykonywania pakietu usług SSIS.

   1. Zaznacz pole wyboru **uwierzytelnianie systemu Windows** , aby wybrać, czy chcesz używać uwierzytelniania systemu Windows w celu uzyskiwania dostępu do magazynów danych, takich jak serwery SQL/udziały plików w środowisku lokalnym lub Azure Files.
   
      Jeśli zaznaczysz to pole wyboru, wprowadź wartości w polach **domena**, **Nazwa użytkownika** i **hasło** dla poświadczeń wykonywania pakietu. Na przykład w celu uzyskania dostępu do Azure Files domena ma `Azure` nazwę username, `<storage account name>` a hasło to `<storage account key>` .

      Alternatywnie możesz użyć wpisów tajnych przechowywanych w Azure Key Vault jako ich wartości. Aby to zrobić, zaznacz pole wyboru **Magazyn kluczy platformy Azure** obok nich. Wybierz lub edytuj istniejącą połączoną usługę magazynu kluczy lub Utwórz nową. Następnie wybierz nazwę i wersję wpisu tajnego dla swojej wartości. Podczas tworzenia lub edytowania połączonej usługi magazynu kluczy możesz wybrać lub edytować istniejący magazyn kluczy lub utworzyć nowy. Upewnij się, że Data Factory zarządzana tożsamość do magazynu kluczy, jeśli jeszcze tego nie zrobiono. Wpis tajny można również wprowadzić bezpośrednio w następującym formacie: `<key vault linked service name>/<secret name>/<secret version>` .
      
   1. Zaznacz pole wyboru **32-bitowe środowiska uruchomieniowego** , aby określić, czy pakiet wymaga 32-bitowego środowiska uruchomieniowego do uruchomienia.

   1. W **obszarze Lokalizacja pakietu** wybierz **pozycję SSISDB**, **system plików (pakiet)**, **system plików (projekt)**, **pakiet osadzony** lub **Magazyn pakietów**. 

##### <a name="package-location-ssisdb"></a>Lokalizacja pakietu: SSISDB

**SSISDB** jako lokalizację pakietu jest automatycznie wybierana, jeśli Azure-SSIS IR została zainicjowana przy użyciu wykazu usług SSIS (SSISDB) hostowanego przez Azure SQL Database Server/Managed instance lub można wybrać ją samodzielnie. Jeśli jest wybrana, wykonaj następujące czynności.

   1. Jeśli Azure-SSIS IR jest uruchomiona, a pole wyboru **wpisy ręczne** jest wyczyszczone, Przeglądaj i wybierz istniejące foldery, projekty, pakiety i środowiska z SSISDB. Wybierz pozycję **Odśwież** , aby pobrać nowo dodane foldery, projekty, pakiety lub środowiska z SSISDB, aby były dostępne do przeglądania i wyboru. Aby przeglądać i wybierać środowiska dla wykonań pakietów, musisz wcześniej skonfigurować projekty, aby dodać te środowiska jako odwołania z tych samych folderów w obszarze SSISDB. Aby uzyskać więcej informacji, zobacz [Tworzenie i mapowanie środowisk SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

   1. W obszarze **poziom rejestrowania** Wybierz wstępnie zdefiniowany zakres rejestrowania dla wykonywania pakietu. Zaznacz pole wyboru **dostosowany** , jeśli chcesz wprowadzić zamiast niej dostosowanej nazwy rejestrowania. 

   1. Jeśli Azure-SSIS IR nie jest uruchomiona lub jest zaznaczone pole wyboru **wpisy ręczne** , wprowadź swoje ścieżki pakietu i środowiska z SSISDB bezpośrednio w następujących formatach: `<folder name>/<project name>/<package name>.dtsx` i `<folder name>/<environment name>` .

      ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>Lokalizacja pakietu: system plików (pakiet)

**System plików (pakiet)** , ponieważ lokalizacja pakietu jest automatycznie wybierana, jeśli Azure-SSIS IR została zainicjowana bez SSISDB lub możesz wybrać ją samodzielnie. Jeśli jest wybrana, wykonaj następujące czynności.

![Ustawianie właściwości na karcie Ustawienia system plików (pakiet)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. Określ pakiet do uruchomienia, podając ścieżkę Universal Naming Convention (UNC) do pliku pakietu (z `.dtsx` ) w polu **ścieżka pakietu** . Możesz przeglądać i wybierać pakiet, wybierając pozycję **Przeglądaj magazyn plików** lub ręcznie wprowadzić jego ścieżkę. Jeśli na przykład przechowujesz swój pakiet w Azure Files, jego ścieżka to `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` . 
   
   1. W przypadku skonfigurowania pakietu w osobnym pliku należy również podać ścieżkę UNC do pliku konfiguracji (z `.dtsConfig` ) w polu **ścieżka konfiguracji** . Możesz przeglądać i wybierać konfigurację, wybierając pozycję **Przeglądaj magazyn plików** lub ręcznie wprowadzić jej ścieżkę. Jeśli na przykład przechowujesz konfigurację w Azure Files, jej ścieżka to `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Określ poświadczenia, aby uzyskać dostęp do pakietu i plików konfiguracji. Jeśli wcześniej wprowadzono wartości poświadczeń wykonywania pakietu (dla **uwierzytelniania systemu Windows**), możesz użyć ich ponownie, zaznaczając pole wyboru **poświadczenia wykonywania pakietu** . W przeciwnym razie wprowadź wartości poświadczeń dostępu do pakietu w polach **domena**, **Nazwa użytkownika** i **hasło** . Jeśli na przykład przechowujesz pakiet i konfigurację w Azure Files, domena to `Azure` , nazwa użytkownika `<storage account name>` , a hasło to `<storage account key>` . 

      Alternatywnie możesz użyć wpisów tajnych przechowywanych w Azure Key Vault jako ich wartości. Aby to zrobić, zaznacz pole wyboru **Magazyn kluczy platformy Azure** obok nich. Wybierz lub edytuj istniejącą połączoną usługę magazynu kluczy lub Utwórz nową. Następnie wybierz nazwę i wersję wpisu tajnego dla swojej wartości. Podczas tworzenia lub edytowania połączonej usługi magazynu kluczy możesz wybrać lub edytować istniejący magazyn kluczy lub utworzyć nowy. Upewnij się, że Data Factory zarządzana tożsamość do magazynu kluczy, jeśli jeszcze tego nie zrobiono. Wpis tajny można również wprowadzić bezpośrednio w następującym formacie: `<key vault linked service name>/<secret name>/<secret version>` . 

      Te poświadczenia są również używane do uzyskiwania dostępu do pakietów podrzędnych w zadaniu pakietu Execute, do którego odwołuje się ich własna ścieżka i inne konfiguracje określone w pakietach. 

   1. Jeśli używasz poziomu ochrony **EncryptAllWithPassword** lub **EncryptSensitiveWithPassword** podczas tworzenia pakietu za pośrednictwem SQL Server Data Tools (SSDT), wprowadź wartość hasła w polu **hasło szyfrowania** . Alternatywnie możesz użyć klucza tajnego przechowywanego w Azure Key Vault jako jego wartość (Zobacz powyżej).
      
      W przypadku użycia poziomu ochrony **EncryptSensitiveWithUserKey** należy ponownie wprowadzić wartości poufne w plikach konfiguracyjnych lub na kartach **Parametry usług SSIS**, **menedżerów połączeń** lub **zastąpień właściwości** (zobacz poniżej).
      
      Jeśli użyto poziomu ochrony **EncryptAllWithUserKey** , jest on nieobsługiwany. Należy ponownie skonfigurować pakiet, aby użyć innego poziomu ochrony za pośrednictwem SSDT lub `dtutil` narzędzia wiersza polecenia. 

   1. W obszarze **poziom rejestrowania** Wybierz wstępnie zdefiniowany zakres rejestrowania dla wykonywania pakietu. Zaznacz pole wyboru **dostosowany** , jeśli chcesz wprowadzić zamiast niej dostosowanej nazwy rejestrowania. 
   
   1. Jeśli chcesz rejestrować wykonywanie pakietów poza przy użyciu standardowych dostawców dzienników, które można określić w pakiecie, określ folder dziennika, podając jego ścieżkę UNC w polu **ścieżka rejestrowania** . Można przeglądać i wybierać foldery dzienników, wybierając pozycję **Przeglądaj magazyn plików** lub ręcznie wprowadzić ścieżkę. Na przykład w przypadku przechowywania dzienników w Azure Files ścieżka rejestrowania to `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Podfolder jest tworzony w tej ścieżce dla każdego pojedynczego uruchomienia pakietu o nazwie po IDENTYFIKATORze uruchomienia działania pakietu SSIS i w którym pliki dziennika są generowane co pięć minut. 
   
   1. Określ poświadczenia, aby uzyskać dostęp do folderu dziennika. Jeśli wcześniej wprowadzono wartości poświadczeń dostępu do pakietu (patrz powyżej), można użyć ich ponownie, zaznaczając pole wyboru **poświadczenia dostępu do pakietu** . W przeciwnym razie wprowadź wartości poświadczeń dostępu do rejestrowania w polach **domena**, **Nazwa użytkownika** i **hasło** . Jeśli na przykład przechowujesz dzienniki w Azure Files, domena ma `Azure` nazwę, `<storage account name>` a hasło to `<storage account key>` . Alternatywnie możesz użyć wpisów tajnych przechowywanych w Azure Key Vault jako ich wartości (Zobacz powyżej).
   
We wszystkich wyżej wymienionych ścieżkach UNC w pełni kwalifikowana nazwa pliku musi być krótsza niż 260 znaków. Nazwa katalogu musi być krótsza niż 248 znaków.

##### <a name="package-location-file-system-project"></a>Lokalizacja pakietu: system plików (projekt)

W przypadku wybrania opcji **system plików (projekt)** jako lokalizacji pakietu wykonaj następujące czynności.

![Ustawianie właściwości na karcie Ustawienia system plików (projekt)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. Określ pakiet do uruchomienia, podając ścieżkę UNC do pliku projektu (z `.ispac` ) w polu **ścieżka projektu** i plik pakietu (z `.dtsx` ) z projektu w polu **Nazwa pakietu** . Możesz przeglądać i wybierać projekt, wybierając pozycję **Przeglądaj magazyn plików** lub ręcznie wprowadzić jego ścieżkę. Na przykład, Jeśli przechowujesz projekt w Azure Files, jego ścieżka to `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` .

   1. Określ poświadczenia, aby uzyskać dostęp do plików projektu i pakietów. Jeśli wcześniej wprowadzono wartości poświadczeń wykonywania pakietu (dla **uwierzytelniania systemu Windows**), możesz użyć ich ponownie, zaznaczając pole wyboru **poświadczenia wykonywania pakietu** . W przeciwnym razie wprowadź wartości poświadczeń dostępu do pakietu w polach **domena**, **Nazwa użytkownika** i **hasło** . Jeśli na przykład przechowujesz projekt i pakiet w Azure Files, domena to, `Azure` Nazwa użytkownika `<storage account name>` , a hasło to `<storage account key>` . 

      Alternatywnie możesz użyć wpisów tajnych przechowywanych w Azure Key Vault jako ich wartości. Aby to zrobić, zaznacz pole wyboru **Magazyn kluczy platformy Azure** obok nich. Wybierz lub edytuj istniejącą połączoną usługę magazynu kluczy lub Utwórz nową. Następnie wybierz nazwę i wersję wpisu tajnego dla swojej wartości. Podczas tworzenia lub edytowania połączonej usługi magazynu kluczy możesz wybrać lub edytować istniejący magazyn kluczy lub utworzyć nowy. Upewnij się, że Data Factory zarządzana tożsamość do magazynu kluczy, jeśli jeszcze tego nie zrobiono. Wpis tajny można również wprowadzić bezpośrednio w następującym formacie: `<key vault linked service name>/<secret name>/<secret version>` . 

      Te poświadczenia są również używane do uzyskiwania dostępu do pakietów podrzędnych w zadaniu pakietu Execute, do którego odwołuje się ten sam projekt. 

   1. Jeśli podczas tworzenia pakietu za pośrednictwem SSDT użyto poziomu ochrony **EncryptAllWithPassword** lub **EncryptSensitiveWithPassword** , wprowadź wartość hasła w polu **hasło szyfrowania** . Alternatywnie możesz użyć klucza tajnego przechowywanego w Azure Key Vault jako jego wartość (Zobacz powyżej).
      
      W przypadku użycia poziomu ochrony **EncryptSensitiveWithUserKey** należy ponownie wprowadzić wartości poufne na kartach **SSIS**, **menedżerów połączeń** lub **zastąpień właściwości** (zobacz poniżej).
      
      Jeśli użyto poziomu ochrony **EncryptAllWithUserKey** , jest on nieobsługiwany. Należy ponownie skonfigurować pakiet, aby użyć innego poziomu ochrony za pośrednictwem SSDT lub `dtutil` narzędzia wiersza polecenia. 

   1. W obszarze **poziom rejestrowania** Wybierz wstępnie zdefiniowany zakres rejestrowania dla wykonywania pakietu. Zaznacz pole wyboru **dostosowany** , jeśli chcesz wprowadzić zamiast niej dostosowanej nazwy rejestrowania. 
   
   1. Jeśli chcesz rejestrować wykonywanie pakietów poza przy użyciu standardowych dostawców dzienników, które można określić w pakiecie, określ folder dziennika, podając jego ścieżkę UNC w polu **ścieżka rejestrowania** . Można przeglądać i wybierać foldery dzienników, wybierając pozycję **Przeglądaj magazyn plików** lub ręcznie wprowadzić ścieżkę. Na przykład w przypadku przechowywania dzienników w Azure Files ścieżka rejestrowania to `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Podfolder jest tworzony w tej ścieżce dla każdego pojedynczego uruchomienia pakietu o nazwie po IDENTYFIKATORze uruchomienia działania pakietu SSIS i w którym pliki dziennika są generowane co pięć minut. 
   
   1. Określ poświadczenia, aby uzyskać dostęp do folderu dziennika. Jeśli wcześniej wprowadzono wartości poświadczeń dostępu do pakietu (patrz powyżej), można użyć ich ponownie, zaznaczając pole wyboru **poświadczenia dostępu do pakietu** . W przeciwnym razie wprowadź wartości poświadczeń dostępu do rejestrowania w polach **domena**, **Nazwa użytkownika** i **hasło** . Jeśli na przykład przechowujesz dzienniki w Azure Files, domena ma `Azure` nazwę, `<storage account name>` a hasło to `<storage account key>` . Alternatywnie możesz użyć wpisów tajnych przechowywanych w Azure Key Vault jako ich wartości (Zobacz powyżej).
   
We wszystkich wyżej wymienionych ścieżkach UNC w pełni kwalifikowana nazwa pliku musi być krótsza niż 260 znaków. Nazwa katalogu musi być krótsza niż 248 znaków.

##### <a name="package-location-embedded-package"></a>Lokalizacja pakietu: osadzony pakiet

W przypadku wybrania opcji **pakiet osadzony** jako lokalizacji pakietu wykonaj następujące czynności.

![Ustawianie właściwości na karcie Ustawienia — pakiet osadzony](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. Przeciągnij i upuść plik pakietu (z `.dtsx` ) lub **Przekaż** go z folderu plików do dostarczonego pola. Pakiet zostanie automatycznie skompresowany i osadzony w ładunku aktywności. Po osadzeniu można **pobrać** pakiet później do edycji. Możesz również **Sparametryzuj** osadzony pakiet, przypisując go do parametru potoku, który może być używany w wielu działaniach, a tym samym zoptymalizować rozmiar ładunku potoku. Osadzanie plików projektu (z `.ispac` ) nie jest obecnie obsługiwane, więc nie można użyć parametrów SSIS/menedżerów połączeń z zakresem na poziomie projektu w pakietach osadzonych.
   
   1. Jeśli osadzony pakiet nie jest szyfrowany i wykryjemy użycie zadania Execute Package (EPT), pole wyboru **Wykonaj zadanie pakietu** zostanie automatycznie zaznaczone, a pakiety podrzędne, do których odwołuje się ścieżka systemu plików, zostaną automatycznie dodane, więc można je również osadzić.
   
      Jeśli nie można wykryć używania EPT, należy ręcznie zaznaczyć pole wyboru **Wykonaj zadanie pakietu** i dodać pakiety podrzędne, do których odwołuje się ścieżka systemu plików, aby można je było również osadzić. Jeśli pakiety podrzędne są przechowywane w bazie danych SQL Server Database (MSDB), nie można ich osadzić, dlatego należy się upewnić, że Azure-SSIS IR mogą uzyskać dostęp do bazy MSDB w celu pobrania ich przy użyciu SQL Server odwołań. Osadzanie plików projektu (z `.ispac` ) nie jest obecnie obsługiwane, więc nie można używać odwołań opartych na projekcie dla pakietów podrzędnych.
   
   1. Jeśli podczas tworzenia pakietu za pośrednictwem SSDT użyto poziomu ochrony **EncryptAllWithPassword** lub **EncryptSensitiveWithPassword** , wprowadź wartość hasła w polu **hasło szyfrowania** . 
   
      Alternatywnie możesz użyć wpisu tajnego przechowywanego w Azure Key Vault jako jego wartości. Aby to zrobić, zaznacz pole wyboru **Magazyn kluczy platformy Azure** obok niego. Wybierz lub edytuj istniejącą połączoną usługę magazynu kluczy lub Utwórz nową. Następnie wybierz nazwę i wersję wpisu tajnego dla swojej wartości. Podczas tworzenia lub edytowania połączonej usługi magazynu kluczy możesz wybrać lub edytować istniejący magazyn kluczy lub utworzyć nowy. Upewnij się, że Data Factory zarządzana tożsamość do magazynu kluczy, jeśli jeszcze tego nie zrobiono. Wpis tajny można również wprowadzić bezpośrednio w następującym formacie: `<key vault linked service name>/<secret name>/<secret version>` .
      
      W przypadku użycia poziomu ochrony **EncryptSensitiveWithUserKey** należy ponownie wprowadzić wartości poufne w plikach konfiguracyjnych lub na kartach **Parametry usług SSIS**, **menedżerów połączeń** lub **zastąpień właściwości** (zobacz poniżej).
      
      Jeśli użyto poziomu ochrony **EncryptAllWithUserKey** , jest on nieobsługiwany. Należy ponownie skonfigurować pakiet, aby użyć innego poziomu ochrony za pośrednictwem SSDT lub `dtutil` narzędzia wiersza polecenia.

   1. W obszarze **poziom rejestrowania** Wybierz wstępnie zdefiniowany zakres rejestrowania dla wykonywania pakietu. Zaznacz pole wyboru **dostosowany** , jeśli chcesz wprowadzić zamiast niej dostosowanej nazwy rejestrowania. 
   
   1. Jeśli chcesz rejestrować wykonywanie pakietów poza przy użyciu standardowych dostawców dzienników, które można określić w pakiecie, określ folder dziennika, podając jego ścieżkę UNC w polu **ścieżka rejestrowania** . Można przeglądać i wybierać foldery dzienników, wybierając pozycję **Przeglądaj magazyn plików** lub ręcznie wprowadzić ścieżkę. Na przykład w przypadku przechowywania dzienników w Azure Files ścieżka rejestrowania to `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Podfolder jest tworzony w tej ścieżce dla każdego pojedynczego uruchomienia pakietu o nazwie po IDENTYFIKATORze uruchomienia działania pakietu SSIS i w którym pliki dziennika są generowane co pięć minut. 
   
   1. Określ poświadczenia, aby uzyskać dostęp do folderu dziennika, wprowadzając ich wartości w polach **domena**, **Nazwa użytkownika** i **hasło** . Jeśli na przykład przechowujesz dzienniki w Azure Files, domena ma `Azure` nazwę, `<storage account name>` a hasło to `<storage account key>` . Alternatywnie możesz użyć wpisów tajnych przechowywanych w Azure Key Vault jako ich wartości (Zobacz powyżej).
   
We wszystkich wyżej wymienionych ścieżkach UNC w pełni kwalifikowana nazwa pliku musi być krótsza niż 260 znaków. Nazwa katalogu musi być krótsza niż 248 znaków.

##### <a name="package-location-package-store"></a>Lokalizacja pakietu: Magazyn pakietów

W przypadku wybrania opcji **Magazyn pakietów** jako lokalizacji pakietu wykonaj następujące czynności.

![Ustawianie właściwości na karcie Ustawienia — magazyn pakietów](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. W obszarze **Nazwa magazynu pakietów** wybierz istniejący magazyn pakietów, który jest dołączony do Azure-SSIS IR.

   1. Określ swój pakiet do uruchomienia, podając jego ścieżkę (bez `.dtsx` ) z wybranego magazynu pakietów w polu **ścieżka pakietu** . Jeśli wybrany magazyn pakietów znajduje się w systemie plików/Azure Files, możesz przeglądać i wybierać pakiet, wybierając pozycję **Przeglądaj magazyn plików**. w przeciwnym razie możesz wprowadzić ścieżkę w formacie `<folder name>\<package name>` . Możesz również zaimportować nowe pakiety do wybranego magazynu pakietów za pośrednictwem SQL Server Management Studio (SSMS) podobnego do [starszego magazynu pakietów SSIS](/sql/integration-services/service/package-management-ssis-service). Aby uzyskać więcej informacji, zobacz [Zarządzanie pakietami SSIS za pomocą magazynów pakietów Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md).

   1. W przypadku skonfigurowania pakietu w osobnym pliku należy podać ścieżkę UNC do pliku konfiguracji (z `.dtsConfig` ) w polu **ścieżka konfiguracji** . Możesz przeglądać i wybierać konfigurację, wybierając pozycję **Przeglądaj magazyn plików** lub ręcznie wprowadzić jej ścieżkę. Jeśli na przykład przechowujesz konfigurację w Azure Files, jej ścieżka to `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Zaznacz pole wyboru **poświadczenia dostępu do konfiguracji** , aby wybrać, czy chcesz określić poświadczenia, aby uzyskać dostęp do pliku konfiguracji osobno. Jest to zbędny, gdy wybrany magazyn pakietów znajduje się na bazie danych SQL Server Database (MSDB) hostowanej przez wystąpienie zarządzane usługi Azure SQL lub nie przechowuje również pliku konfiguracji.
   
      Jeśli wcześniej wprowadzono wartości poświadczeń wykonywania pakietu (dla **uwierzytelniania systemu Windows**), możesz użyć ich ponownie, zaznaczając pole wyboru **poświadczenia wykonywania pakietu** . W przeciwnym razie wprowadź wartości w polach **domena**, **Nazwa użytkownika** i **hasło** . Jeśli na przykład przechowujesz konfigurację w Azure Files, domena to, `Azure` Nazwa użytkownika `<storage account name>` , a hasło to `<storage account key>` . 

      Alternatywnie możesz użyć wpisów tajnych przechowywanych w Azure Key Vault jako ich wartości. Aby to zrobić, zaznacz pole wyboru **Magazyn kluczy platformy Azure** obok nich. Wybierz lub edytuj istniejącą połączoną usługę magazynu kluczy lub Utwórz nową. Następnie wybierz nazwę i wersję wpisu tajnego dla swojej wartości. Podczas tworzenia lub edytowania połączonej usługi magazynu kluczy możesz wybrać lub edytować istniejący magazyn kluczy lub utworzyć nowy. Upewnij się, że Data Factory zarządzana tożsamość do magazynu kluczy, jeśli jeszcze tego nie zrobiono. Wpis tajny można również wprowadzić bezpośrednio w następującym formacie: `<key vault linked service name>/<secret name>/<secret version>` .

   1. Jeśli podczas tworzenia pakietu za pośrednictwem SSDT użyto poziomu ochrony **EncryptAllWithPassword** lub **EncryptSensitiveWithPassword** , wprowadź wartość hasła w polu **hasło szyfrowania** . Alternatywnie możesz użyć klucza tajnego przechowywanego w Azure Key Vault jako jego wartość (Zobacz powyżej).
      
      W przypadku użycia poziomu ochrony **EncryptSensitiveWithUserKey** należy ponownie wprowadzić wartości poufne w plikach konfiguracyjnych lub na kartach **Parametry usług SSIS**, **menedżerów połączeń** lub **zastąpień właściwości** (zobacz poniżej).
      
      Jeśli użyto poziomu ochrony **EncryptAllWithUserKey** , jest on nieobsługiwany. Należy ponownie skonfigurować pakiet, aby użyć innego poziomu ochrony za pośrednictwem SSDT lub `dtutil` narzędzia wiersza polecenia. 

   1. W obszarze **poziom rejestrowania** Wybierz wstępnie zdefiniowany zakres rejestrowania dla wykonywania pakietu. Zaznacz pole wyboru **dostosowany** , jeśli chcesz wprowadzić zamiast niej dostosowanej nazwy rejestrowania. 
   
   1. Jeśli chcesz rejestrować wykonywanie pakietów poza przy użyciu standardowych dostawców dzienników, które można określić w pakiecie, określ folder dziennika, podając jego ścieżkę UNC w polu **ścieżka rejestrowania** . Można przeglądać i wybierać foldery dzienników, wybierając pozycję **Przeglądaj magazyn plików** lub ręcznie wprowadzić ścieżkę. Na przykład w przypadku przechowywania dzienników w Azure Files ścieżka rejestrowania to `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Podfolder jest tworzony w tej ścieżce dla każdego pojedynczego uruchomienia pakietu o nazwie po IDENTYFIKATORze uruchomienia działania pakietu SSIS i w którym pliki dziennika są generowane co pięć minut. 
   
   1. Określ poświadczenia, aby uzyskać dostęp do folderu dziennika, wprowadzając ich wartości w polach **domena**, **Nazwa użytkownika** i **hasło** . Jeśli na przykład przechowujesz dzienniki w Azure Files, domena ma `Azure` nazwę, `<storage account name>` a hasło to `<storage account key>` . Alternatywnie możesz użyć wpisów tajnych przechowywanych w Azure Key Vault jako ich wartości (Zobacz powyżej).
   
We wszystkich wyżej wymienionych ścieżkach UNC w pełni kwalifikowana nazwa pliku musi być krótsza niż 260 znaków. Nazwa katalogu musi być krótsza niż 248 znaków.

#### <a name="ssis-parameters-tab"></a>Karta parametrów SSIS

Na karcie **parametry programu SSIS** działania wykonaj pakiet SSIS wykonaj następujące czynności.

![Ustawianie właściwości na karcie Parametry SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. Jeśli Azure-SSIS IR jest uruchomiony, **SSISDB** jest wybrane jako lokalizacja pakietu, a pole wyboru **wpisy ręczne** na karcie **Ustawienia** jest wyczyszczone, istniejące parametry usług SSIS w wybranym projekcie i pakiecie z SSISDB są wyświetlane, aby przypisać do nich wartości. W przeciwnym razie możesz wprowadzić je pojedynczo, aby przypisać do nich wartości ręcznie. Upewnij się, że istnieją i zostały poprawnie wprowadzone w celu pomyślnego wykonania pakietu. 
   
   1. Jeśli został użyty poziom ochrony **EncryptSensitiveWithUserKey** podczas tworzenia pakietu za pośrednictwem SSDT i **system plików (pakiet)**, **system plików (projekt)**, **osadzony pakiet** lub **Magazyn pakietów** jest wybrany jako lokalizacja pakietu, należy również ponownie wprowadzić poufne parametry, aby przypisać do nich wartości na tej karcie. 
   
W przypadku przypisywania wartości do parametrów można dodać zawartość dynamiczną przy użyciu wyrażeń, funkcji, Data Factory zmiennych systemowych Data Factory i parametrów potoku lub zmiennych.

Alternatywnie możesz użyć wpisów tajnych przechowywanych w Azure Key Vault jako ich wartości. Aby to zrobić, zaznacz pole wyboru **Magazyn kluczy platformy Azure** obok nich. Wybierz lub edytuj istniejącą połączoną usługę magazynu kluczy lub Utwórz nową. Następnie wybierz nazwę i wersję wpisu tajnego dla swojej wartości. Podczas tworzenia lub edytowania połączonej usługi magazynu kluczy możesz wybrać lub edytować istniejący magazyn kluczy lub utworzyć nowy. Upewnij się, że Data Factory zarządzana tożsamość do magazynu kluczy, jeśli jeszcze tego nie zrobiono. Wpis tajny można również wprowadzić bezpośrednio w następującym formacie: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="connection-managers-tab"></a>Karta menedżerowie połączeń

Na karcie **Menedżer połączeń** w działaniu wykonaj pakiet SSIS wykonaj następujące czynności.

![Ustawianie właściwości na karcie menedżerów połączeń](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. Jeśli Azure-SSIS IR jest uruchomiony, **SSISDB** jest wybrane jako lokalizacja pakietu, a pole wyboru **wpisy ręczne** na karcie **Ustawienia** zostanie wyczyszczone, istniejące Menedżery połączeń w wybranym projekcie i pakiet z SSISDB są wyświetlane, aby przypisać wartości do ich właściwości. W przeciwnym razie możesz wprowadzić je pojedynczo, aby przypisać wartości do swoich właściwości ręcznie. Upewnij się, że istnieją i zostały poprawnie wprowadzone w celu pomyślnego wykonania pakietu. 
   
      Możesz uzyskać prawidłowy **zakres**, **nazwę** i nazwy **Właściwości** dla dowolnego Menedżera połączeń, otwierając pakiet, który zawiera go w SSDT. Po otwarciu pakietu wybierz odpowiedniego Menedżera połączeń, aby wyświetlić nazwy i wartości wszystkich właściwości w oknie **Właściwości** SSDT. Korzystając z tych informacji, można zastąpić wartości właściwości Menedżera połączeń w czasie wykonywania. 

      ![Pobierz właściwości Menedżera połączeń z SSDT](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      Na przykład bez modyfikowania oryginalnego pakietu w programie SSDT można przekształcić swoje lokalne przepływy danych działające w SQL Server na przepływy danych w chmurze działające w usłudze SSIS IR w usłudze ADF, zastępując wartości właściwości **ConnectByProxy**, **ConnectionString** i **ConnectUsingManagedIdentity** w istniejących menedżerach połączeń w czasie wykonywania.
      
      Te zastąpienia w czasie wykonywania mogą włączyć Self-Hosted IR (SHIR) jako serwer proxy dla usług SSIS IR podczas uzyskiwania dostępu do danych lokalnych, zobacz [Konfigurowanie SHIR jako proxy dla usług SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)i Azure SQL Database/połączenia wystąpienia zarządzanego przy użyciu najnowszego sterownika MSOLEDBSQL, który z kolei umożliwia uwierzytelnianie za pomocą usługi Azure Active Directory (AAD) z tożsamością zarządzaną [przez](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)usługę ADF.

      ![Ustawianie właściwości z SSDT na karcie menedżerów połączeń](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. Jeśli używasz poziomu ochrony **EncryptSensitiveWithUserKey** podczas tworzenia pakietu za pośrednictwem usługi SSDT i **system plików (pakiet)**, **system plików (projekt)**, **osadzony pakiet** lub **Magazyn pakietów** jest wybrany jako lokalizacja pakietu, należy również ponownie wprowadzić poufne właściwości Menedżera połączeń, aby przypisać do nich wartości na tej karcie. 

W przypadku przypisywania wartości do właściwości Menedżera połączeń można dodać zawartość dynamiczną przy użyciu wyrażeń, funkcji, Data Factory zmiennych systemowych Data Factory i parametrów potoku lub zmiennych. 

Alternatywnie możesz użyć wpisów tajnych przechowywanych w Azure Key Vault jako ich wartości. Aby to zrobić, zaznacz pole wyboru **Magazyn kluczy platformy Azure** obok nich. Wybierz lub edytuj istniejącą połączoną usługę magazynu kluczy lub Utwórz nową. Następnie wybierz nazwę i wersję wpisu tajnego dla swojej wartości. Podczas tworzenia lub edytowania połączonej usługi magazynu kluczy możesz wybrać lub edytować istniejący magazyn kluczy lub utworzyć nowy. Upewnij się, że Data Factory zarządzana tożsamość do magazynu kluczy, jeśli jeszcze tego nie zrobiono. Wpis tajny można również wprowadzić bezpośrednio w następującym formacie: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="property-overrides-tab"></a>Karta zastąpień właściwości

Na karcie **Zastępowanie właściwości** działania wykonaj pakiet SSIS wykonaj następujące czynności.

![Ustawianie właściwości na karcie Zastępowanie właściwości](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. Wprowadź ścieżki istniejących właściwości w wybranym pakiecie po jednym przez jedną, aby ręcznie przypisać do nich wartości. Upewnij się, że istnieją i zostały poprawnie wprowadzone w celu pomyślnego wykonania pakietu. Na przykład aby zastąpić wartość zmiennej użytkownika, wprowadź jej ścieżkę w następującym formacie: `\Package.Variables[User::<variable name>].Value` . 

      Możesz uzyskać poprawną **ścieżkę właściwości** dla dowolnej właściwości pakietu, otwierając pakiet, który zawiera go w SSDT. Po otwarciu pakietu zaznacz właściwość przepływ sterowania i **konfiguracje** w oknie **Właściwości** SSDT. Następnie wybierz przycisk wielokropka (**...**) obok właściwości **konfiguracje** , aby otworzyć **organizatora konfiguracje pakietów** , która jest zwykle używana do [tworzenia konfiguracji pakietów w modelu wdrażania pakietów](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations). 

      ![Pobierz właściwości pakietu z właściwości SSDT-Configurations](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      W oknie Kreator **konfiguracji pakietu** zaznacz pole wyboru **Włącz konfiguracje pakietów** i przycisk **Dodaj...** , aby otworzyć **Kreatora konfiguracji pakietu**. 
      
      W **Kreatorze konfiguracji pakietu** wybierz element **pliku konfiguracji XML** w menu rozwijanym **Typ konfiguracji** i przycisk **Określ ustawienia konfiguracji bezpośrednio** , wprowadź nazwę pliku konfiguracji, a następnie wybierz przycisk **dalej >** . 

      ![Pobieranie właściwości pakietu z SSDT — Organizator konfiguracji](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      Na koniec wybierz właściwości pakietu, których ścieżkę chcesz uzyskać, i przycisk **dalej >** .  Teraz możesz zobaczyć, skopiować & wkleić żądane ścieżki właściwości pakietu i zapisać je w pliku konfiguracji. Korzystając z tych informacji, można zastąpić wartości właściwości pakietu w czasie wykonywania. 

      ![Pobieranie właściwości pakietu z SSDT-Configuration Wizard](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. Jeśli został użyty poziom ochrony **EncryptSensitiveWithUserKey** podczas tworzenia pakietu za pośrednictwem SSDT i **system plików (pakiet)**, **system plików (projekt)**, **osadzony** pakiet lub **Magazyn pakietów** jest wybrany jako lokalizacja pakietu, należy również ponownie wprowadzić właściwości poufnego pakietu, aby przypisać do nich wartości na tej karcie. 
   
W przypadku przypisywania wartości do właściwości pakietu można dodać zawartość dynamiczną przy użyciu wyrażeń, funkcji, Data Factory zmiennych systemowych Data Factory i parametrów potoku lub zmiennych.

Wartości przypisane do plików konfiguracji i na karcie **Parametry usług SSIS** można zastąpić przy użyciu kart **menedżerów połączeń** lub **zastąpień właściwości** . Wartości przypisane na karcie **menedżerów połączeń** mogą być również zastępowane przy użyciu karty **zastąpień właściwości** .

Aby sprawdzić poprawność konfiguracji potoku, wybierz pozycję **Weryfikuj** na pasku narzędzi. Aby zamknąć **raport weryfikacji potoku**, wybierz opcję **>>** .

Aby opublikować potok do Data Factory, wybierz pozycję **Opublikuj wszystko**. 

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
Możesz użyć istniejącej fabryki danych, która ma już zainicjowaną Azure-SSIS IR, lub utworzyć nową fabrykę danych z Azure-SSIS IR. Postępuj zgodnie z instrukcjami krok po kroku w [samouczku: Wdrażanie pakietów usług SSIS na platformie Azure za pośrednictwem programu PowerShell](./tutorial-deploy-ssis-packages-azure-powershell.md).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Tworzenie potoku za pomocą działania wykonywania pakietu SSIS 
W tym kroku utworzysz potok z działaniem wykonaj pakiet SSIS. Działanie uruchamia pakiet SSIS. 

1. Utwórz plik JSON o nazwie `RunSSISPackagePipeline.json` w `C:\ADF\RunSSISPackage` folderze o zawartości podobnej do poniższego przykładu.

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
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
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

   Aby wykonać pakiety przechowywane w systemie plików/Azure Files, wprowadź wartości właściwości pakietu i lokalizacji dziennika w następujący sposób:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
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
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
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

   Aby wykonać pakiety w ramach projektów przechowywanych w systemie plików/Azure Files, wprowadź wartości właściwości lokalizacji pakietu w następujący sposób:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
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

   Aby wykonać pakiety przechowywane w magazynach pakietów, wprowadź wartości właściwości pakietu i lokalizacji konfiguracji w następujący sposób:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
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
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
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

2. W Azure PowerShell przejdź do `C:\ADF\RunSSISPackage` folderu.

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

1. Utwórz plik JSON o nazwie `MyTrigger.json` w `C:\ADF\RunSSISPackage` folderze o następującej zawartości: 
        
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
    
1. W Azure PowerShell przejdź do `C:\ADF\RunSSISPackage` folderu.
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