---
title: Zarządzanie pakietami za pomocą magazynu pakietów Azure-SSIS Integration Runtime
description: Dowiedz się, jak zarządzać pakietami przy użyciu magazynu pakietów Azure-SSIS Integration Runtime.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 66fdd20e66360be7cdaa6d918e18ca09c413da07
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393840"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Zarządzanie pakietami za pomocą magazynu pakietów Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Aby podnieść & przenieść obciążenia SQL Server Integration Services lokalnych (SSIS) do chmury, możesz udostępnić Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF). Aby uzyskać więcej informacji, zobacz temat [Inicjowanie obsługi Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md). Azure-SSIS IR obsługuje:

- Uruchomione pakiety wdrożone do wykazu usług SSIS (SSISDB) hostowane przez Azure SQL Database Server/wystąpienie zarządzane (model wdrażania projektu)
- Uruchomione pakiety wdrożone w systemie plików, Azure Files lub SQL Server Database (MSDB) hostowane przez wystąpienie zarządzane Azure SQL (model wdrażania pakietów)

Korzystając z modelu wdrażania pakietów, możesz wybrać, czy chcesz zainicjować obsługę administracyjną Azure-SSIS IR w sklepach z pakietami. Zapewniają one warstwę zarządzania pakietami na poziomie systemu plików, Azure Files lub MSDB hostowaną przez wystąpienie zarządzane usługi Azure SQL. Magazyn pakietów Azure-SSIS IR umożliwia importowanie/eksportowanie/usuwanie/uruchamianie pakietów oraz monitorowanie/zatrzymywanie uruchomionych pakietów za pośrednictwem SQL Server Management Studio (SSMS) podobnego do [starszego magazynu pakietów SSIS](/sql/integration-services/service/package-management-ssis-service). 

## <a name="connect-to-azure-ssis-ir"></a>Połącz z Azure-SSIS IR

Po zainicjowaniu Azure-SSIS IR możesz nawiązać z nim połączenie, aby przeglądać jego magazyny pakietów w programie SSMS.

![Połącz z Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

W oknie **Eksplorator obiektów** narzędzia SSMS wybierz pozycję **Azure-SSIS Integration Runtime** z menu rozwijanego **Połącz** . Następnie zaloguj się do platformy Azure i wybierz odpowiednią subskrypcję, ADF i Azure-SSIS IR, która została zainicjowana z magazynem pakietów. Azure-SSIS IR pojawi się z **uruchomionymi pakietami** i węzłami **przechowywanych pakietów** poniżej. Rozwiń węzeł **przechowywane pakiety** , aby zobaczyć Twoje magazyny pakietów poniżej. Rozwiń magazyny pakietów, aby wyświetlić poniżej foldery i pakiety. Może zostać wyświetlony monit o wprowadzenie poświadczeń dostępu dla magazynów pakietów, jeśli program SSMS nie będzie mógł nawiązać połączenia automatycznie. Na przykład po rozwinięciu sklepu z pakietem w bazie danych MSDB może zostać wyświetlony monit o połączenie się z wystąpieniem zarządzanym usługi Azure SQL.

![Nawiązywanie połączenia z wystąpieniem zarządzanym usługi Azure SQL](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Zarządzanie folderami i pakietami

Po nawiązaniu połączenia z Azure-SSIS IRą w programie SSMS możesz kliknąć prawym przyciskiem myszy dowolne magazyny pakietów, foldery lub pakiety, aby wypróbować menu i wybrać pozycję **Nowy folder**, **zaimportować pakiet**, **wyeksportować pakiet**, **usunąć** lub **odświeżyć**.

   ![Zarządzanie folderami i pakietami](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Wybierz pozycję **Nowy folder** , aby utworzyć nowy folder dla importowanych pakietów.

   *  Wybierz pozycję **Importuj pakiet** , aby zaimportować pakiety z **systemu plików**, **SQL Server** (msdb) lub starszego **magazynu pakietów usług SSIS** do sklepu z pakietami.

      ![Importuj pakiet](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      W zależności od **lokalizacji pakietu** do zaimportowania wybierz odpowiedni  / **Typ uwierzytelniania** serwera, w razie potrzeby wprowadź poświadczenia dostępu, wybierz **ścieżkę pakietu**, a następnie wprowadź **nazwę nowego pakietu**. Podczas importowania pakietów nie można zmienić ich poziomu ochrony. Aby to zmienić, użyj narzędzi SQL Server Data Tools (SSDT) lub `dtutil` narzędzia wiersza polecenia.

      > [!NOTE]
      > Importowanie pakietów usług SSIS do magazynów pakietów Azure-SSIS IR można wykonać tylko jeden raz, a po prostu skopiują je do bazowego systemu lub Azure Files system plików, a jednocześnie zachowując zachowaną wersję SQL Server/SSIS. 
      >
      > Ponieważ Azure-SSIS IR jest obecnie oparta na **SQL Server 2017**, wykonywanie na niej pakietów z niższymi wersjami zostanie uaktualnione do pakietów SSIS 2017 w czasie wykonywania. Wykonywanie pakietów o wyższej wersji nie jest obsługiwane.
      >
      > Ponadto ze względu na to, że starsze magazyny pakietów SSIS są powiązane z konkretną SQL Server wersją i są dostępne tylko w programie SSMS dla tej wersji, pakiety o niższych wersjach w starszych magazynach pakietów SSIS muszą zostać wyeksportowane do systemu plików przy użyciu wyoznaczonej wersji programu SSMS przed ich zaimportowaniem do Azure-SSIS IR magazynów pakietów przy użyciu programu SSMS 2019 lub nowszego
      >
      > Alternatywnie, aby zaimportować wiele pakietów usług SSIS do magazynów pakietów Azure-SSIS IR podczas przełączania ich poziomu ochrony, można użyć narzędzia wiersza polecenia [dtutil](/sql/integration-services/dtutil-utility) , aby zapoznać się z tematem [wdrażanie wielu pakietów z dtutil](#deploying-multiple-packages-with-dtutil).

   *  Wybierz pozycję **Eksportuj pakiet** , aby wyeksportować pakiety z magazynu pakietów do **systemu plików**, **SQL Server** (msdb) lub starszego **magazynu pakietów usług SSIS**.

      ![Eksportuj pakiet](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      W zależności od **lokalizacji pakietu** do wyeksportowania wybierz odpowiedni  / **Typ uwierzytelniania** serwera, wprowadź poświadczenia dostępu w razie potrzeby, a następnie wybierz **ścieżkę pakietu**. Podczas eksportowania pakietów, jeśli są one zaszyfrowane, wprowadź hasła, aby je najpierw odszyfrować, a następnie możesz zmienić ich poziom ochrony, aby na przykład uniknąć przechowywania poufnych danych lub szyfrowania lub wszystkich danych przy użyciu klucza użytkownika lub hasła.

      > [!NOTE]
      > Eksportowanie pakietów SSIS z Azure-SSIS IR magazynów pakietów może odbywać się tylko jeden raz i nie przełączając ich poziomu ochrony, po prostu skopiują je przy zachowaniu ich wersji SQL Server/SSIS. w przeciwnym razie uaktualnienie zostanie uaktualnione do pakietów SSIS 2019 lub nowszych wersji.
      >
      > Ponieważ Azure-SSIS IR jest obecnie oparta na **SQL Server 2017**, wykonywanie na niej pakietów z niższymi wersjami zostanie uaktualnione do pakietów SSIS 2017 w czasie wykonywania. Wykonywanie pakietów o wyższej wersji nie jest obsługiwane.
      >
      > Alternatywnie, aby wyeksportować wiele pakietów usług SSIS z Azure-SSIS IR magazynów pakietów podczas przełączania ich poziomu ochrony, można użyć narzędzia wiersza polecenia [dtutil](/sql/integration-services/dtutil-utility) , aby zapoznać się z tematem [wdrażanie wielu pakietów z dtutil](#deploying-multiple-packages-with-dtutil).

   *  Wybierz pozycję **Usuń** , aby usunąć istniejące foldery/pakiety ze sklepu pakietu.

   *  Wybierz pozycję **Odśwież** , aby wyświetlić nowo dodane foldery/pakiety w sklepie pakietu.

## <a name="execute-packages"></a>Wykonaj pakiety

Po nawiązaniu połączenia z Azure-SSIS IR w programie SSMS kliknij prawym przyciskiem myszy dowolne przechowywane pakiety, aby wypróbować menu, a następnie wybierz pozycję **Uruchom pakiet**.  Spowoduje to otwarcie okna dialogowego **Narzędzie do wykonywania pakietów** , w którym można skonfigurować wykonywanie pakietów na Azure-SSIS IR jako działania pakietu SSIS w potokach ADF.

![Strony narzędzie do wykonywania pakietów 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Narzędzie do wykonywania pakietów stronach 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

Strony **Ogólne**, **konfiguracje**, **Opcje wykonywania** i **rejestrowania** okna dialogowego **Narzędzie do wykonywania pakietów** odpowiadają karcie  **Ustawienia** w ramach działania wykonaj pakiet SSIS. Na tych stronach można wprowadzić hasło szyfrowania dla pakietu i informacje o dostępie do pliku konfiguracji pakietu. Możesz również wprowadzić poświadczenia i właściwości wykonania pakietu, a także informacje o dostępie do folderu dziennika.  Strona **Ustawianie wartości** okna dialogowego **Narzędzie do wykonywania pakietów** odpowiada karcie **zastąpień właściwości** wykonywania pakietu SSIS, w której można wprowadzić istniejące właściwości pakietu do przesłonięcia. Aby uzyskać więcej informacji, zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w potokach ADF](./how-to-invoke-ssis-package-ssis-activity.md).

Po wybraniu przycisku **Wykonaj** nowy potok ADF z działaniem pakiet SSIS zostanie automatycznie wygenerowany i wyzwolony. Jeśli potok ADF z tymi samymi ustawieniami już istnieje, zostanie ponownie uruchomiony i nie zostanie wygenerowany nowy potok. Działanie potoku APD i Uruchom pakiet SSIS zostanie odpowiednio nazwane `Pipeline_SSMS_YourPackageName_HashString` i `Activity_SSMS_YourPackageName` .

![Przycisk narzędzie do wykonywania pakietów](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Działanie wykonywania pakietu SSIS](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Monitoruj i Zatrzymaj uruchomione pakiety

Po nawiązaniu połączenia z Azure-SSIS IRą w programie SSMS można rozwinąć węzeł **uruchomione pakiety** , aby zobaczyć aktualnie uruchomione pakiety poniżej.  Kliknij prawym przyciskiem myszy dowolne z nich, aby wyskakujące menu, a następnie wybierz pozycję **Zatrzymaj** lub **Odśwież**.

   ![Monitoruj i Zatrzymaj uruchomione pakiety](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Wybierz pozycję **Zatrzymaj** , aby anulować aktualnie uruchomiony potok ADF, który uruchamia pakiet jako działanie wykonaj pakiet SSIS.

   *  Wybierz pozycję **Odśwież** , aby wyświetlić nowo uruchomione pakiety z magazynów pakietów.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Monitorowanie Azure-SSIS IR i edytowanie magazynów pakietów

Po nawiązaniu połączenia z Azure-SSIS IR w programie SSMS kliknij prawym przyciskiem myszy, aby wyświetlić menu, a następnie wybierz pozycję **Przejdź do portalu Azure Data Factory** lub **Odśwież**.

   ![Przejdź do portalu ADF](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Wybierz pozycję **Przejdź do portalu Azure Data Factory** , aby otworzyć stronę **Integration Runtime** (centrum monitorowania ADF), w której można monitorować Azure-SSIS IR. Na kafelku **magazyny pakietów** można zobaczyć liczbę magazynów pakietów, które są dołączone do Azure-SSIS IR.  Wybranie tego numeru spowoduje wyskakujące okienko, w którym można edytować połączone usługi ADF, które przechowują informacje o dostępie do magazynów pakietów.

      ![Edytuj sklepy pakietu](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Wybierz pozycję **Odśwież** , aby wyświetlić nowo dodane foldery/pakiety w sklepach i uruchomionych pakietach z magazynów pakietów.

## <a name="deploying-multiple-packages-with-dtutil"></a>Wdrażanie wielu pakietów przy użyciu dtutil

Aby przekształcić & przeciągać lokalne obciążenia usług SSIS na usługi SSIS w ramach podajnika APD przy zachowaniu starszego modelu wdrażania pakietów, należy wdrożyć pakiety z systemu plików, bazy danych MSDB hostowanej przez SQL Server lub starsze magazyny pakietów usług SSIS w Azure Files, w bazie danych MSDB hostowanej przez wystąpienie zarządzane usługi Azure SQL lub w Azure-SSIS IR magazynach pakietów. W tym samym czasie należy również przełączyć swój poziom ochrony z szyfrowania przez klucz użytkownika na wartość niezaszyfrowaną lub szyfrowanie za pomocą hasła, jeśli jeszcze tego nie zrobiono.

Aby wdrożyć wiele pakietów w partiach, można użyć narzędzia wiersza polecenia [dtutil](/sql/integration-services/dtutil-utility) , które jest dostarczane z instalacją SQL Server/SSIS. Jest ona powiązana z konkretną wersją usług SSIS, więc jeśli używasz jej do wdrażania pakietów o niższych wersjach bez przełączania ich poziomu ochrony, zostanie ona po prostu skopiowana z zachowaniem ich wersji usług SSIS. Jeśli użyjesz go do wdrożenia i przełączenia ich poziomu ochrony w tym samym czasie, spowoduje to uaktualnienie ich do wersji usług SSIS.

 Ponieważ Azure-SSIS IR jest obecnie oparta na **SQL Server 2017**, wykonywanie na niej pakietów z niższymi wersjami zostanie uaktualnione do pakietów SSIS 2017 w czasie wykonywania. Wykonywanie pakietów o wyższej wersji nie jest obsługiwane.

W związku z tym, aby uniknąć uaktualnień w czasie wykonywania, wdrażanie pakietów do uruchamiania na Azure-SSIS IR w modelu wdrażania pakietów powinno być dtutil 2017, które są dostarczane z instalacją SQL Server/SSIS 2017. W tym celu możesz pobrać i zainstalować bezpłatnie [SQL Server/SSIS 2017 Developer Edition](https://go.microsoft.com/fwlink/?linkid=853016) . Po zainstalowaniu programu można znaleźć dtutil 2017 w tym folderze: `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` .

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>Wdrażanie wielu pakietów z systemu plików lokalnie w Azure Files z dtutil

 Aby wdrożyć wiele pakietów z systemu plików w Azure Files i przełączyć ich poziom ochrony w tym samym czasie, można uruchomić następujące polecenia w wierszu polecenia. Zamień wszystkie ciągi specyficzne dla Twojego przypadku.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

Aby uruchomić powyższe polecenia w pliku wsadowym, Zastąp `%f` ciąg `%%f` .

Aby wdrożyć wiele pakietów ze starszych wersji magazynów pakietów SSIS na serwerze plików w Azure Files i przełączyć ich poziom ochrony w tym samym czasie, można użyć tych samych poleceń, ale zamienić je na `YourLocalDrive:\...\YourPackageFolder` folder lokalny używany przez starsze magazyny pakietów SSIS: `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` . Na przykład jeśli starszy magazyn pakietów usług SSIS jest powiązany z SQL Server 2016, przejdź do `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` .  Możesz znaleźć wartość `YourSQLServerDefaultCompatibilityLevel` z [listy SQL Server domyślnych poziomów zgodności](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#arguments).

Jeśli skonfigurowano Azure-SSIS IR magazyny pakietów na Azure Files, wdrożone pakiety pojawią się w nich po nawiązaniu połączenia z Azure-SSIS IR w programie SSMS 2019 lub nowszym.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>Wdrażanie wielu pakietów z poziomu MSDB lokalnie w bazie MSDB na platformie Azure przy użyciu usługi dtutil

 Aby wdrożyć wiele pakietów z bazy danych MSDB hostowanej przez SQL Server lub starsze magazyny pakietów usług SSIS w oparciu o bazę danych msdb hostowaną przez wystąpienie zarządzane usługi Azure SQL i przełączyć poziom ochrony w tym samym czasie, możesz nawiązać połączenie z SQL Server w programie SSMS, klikając prawym przyciskiem myszy `Databases->System Databases->msdb` węzeł w **Eksplorator obiektów** programu SSMS, aby otworzyć **nowe okno zapytania** i uruchomić następujący skrypt T-SQL. Zamień wszystkie ciągi specyficzne dla Twojego przypadku:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Aby użyć prywatnego/publicznego punktu końcowego wystąpienia zarządzanego usługi Azure SQL, Zastąp `YourSQLManagedInstanceEndpoint` wartość `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` odpowiednio.

Skrypt generuje wiersze poleceń dtutil dla wszystkich pakietów w bazie MSDB, które można wybrać, skopiować & wklejanie i uruchamiać w wierszu polecenia.

![Generowanie wierszy poleceń dtutil](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

Jeśli skonfigurowano magazyny pakietu Azure-SSIS IR w oparciu o bazę danych MSDB, wdrożone pakiety pojawią się w nich podczas łączenia się z Azure-SSIS IR w programie SSMS 2019 lub nowszym.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>Wdrażanie wielu pakietów z poziomu MSDB lokalnie w Azure Files z dtutil

 Aby wdrożyć wiele pakietów z bazy danych MSDB hostowanej przez SQL Server lub starsze magazyny pakietów SSIS na podstawie bazy danych MSDB na Azure Files i przełączyć swój poziom ochrony w tym samym czasie, możesz nawiązać połączenie z SQL Server w programie SSMS, kliknij prawym przyciskiem myszy `Databases->System Databases->msdb` węzeł na **Eksplorator obiektów** programu SSMS, aby otworzyć **nowe okno zapytania** , a następnie uruchom następujący skrypt T-SQL. Zamień wszystkie ciągi specyficzne dla Twojego przypadku:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Skrypt generuje wiersze poleceń dtutil dla wszystkich pakietów w bazie MSDB, które można wybrać, skopiować & wklejanie i uruchamiać w wierszu polecenia.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Jeśli skonfigurowano Azure-SSIS IR magazyny pakietów na Azure Files, wdrożone pakiety pojawią się w nich po nawiązaniu połączenia z Azure-SSIS IR w programie SSMS 2019 lub nowszym.

## <a name="next-steps"></a>Następne kroki

Możesz ponownie uruchomić/edytować automatycznie generowane potoki APD przy użyciu działań wykonywania pakietów usług SSIS lub utworzyć nowe w portalu ADF. Aby uzyskać więcej informacji, zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w potokach ADF](./how-to-invoke-ssis-package-ssis-activity.md).