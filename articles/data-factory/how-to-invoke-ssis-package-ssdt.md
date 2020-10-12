---
title: Wykonaj pakiety SSIS z SSDT
description: Dowiedz się, jak uruchamiać pakiety usług SSIS na platformie Azure z SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: fb5b5cb0ac4a9ace7b5de5e92308da58fd2b1fec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504948"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Wykonywanie pakietów SSIS na platformie Azure z SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano funkcję projektów SQL Server Integration Services (SSIS) z obsługą platformy Azure w narzędziach SQL Server Data Tools (SSDT). Pozwala ona ocenić zgodność w chmurze pakietów SSIS i uruchamiać je w Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF). Ta funkcja umożliwia testowanie istniejących pakietów przed podjęciem & Shift/migrowanie ich na platformę Azure lub opracowywanie nowych pakietów do uruchamiania na platformie Azure.

Za pomocą tej funkcji można dołączyć nowo utworzone/istniejące Azure-SSIS IR do projektów SSIS, a następnie wykonać na nim pakiety.  Obsługujemy uruchamianie pakietów do wdrożenia w katalogu usług SSIS (SSISDB) hostowanego przez serwer Azure SQL Database lub wystąpienie zarządzane w modelu wdrażania projektu. Obsługujemy również uruchamianie pakietów do wdrożenia w systemie plików/Azure Files/SQL Server Database (MSDB) hostowanych przez wystąpienie zarządzane Azure SQL w modelu wdrażania pakietów. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tej funkcji, Pobierz i zainstaluj najnowsze [SSDT z rozszerzeniem](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)Project SSIS dla programu Visual Studio (vs). Alternatywnie można również pobrać i zainstalować najnowszą SSDT jako autonomiczny Instalator z tego [miejsca](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Azure — Włączanie projektów SSIS

### <a name="creating-new-azure-enabled-ssis-projects"></a>Tworzenie nowych projektów usług SSIS obsługujących platformę Azure

W witrynie SSDT można tworzyć nowe projekty SSIS z obsługą platformy Azure przy użyciu szablonu **projektu usług Integration Services (z obsługą platformy Azure)** .

   ![Nowy projekt SSIS obsługujący platformę Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Po utworzeniu projektu z obsługą platformy Azure zostanie wyświetlony monit o połączenie z usługą SSIS w Azure Data Factory.

   ![Monit Azure-SSIS IR łączenia](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Jeśli chcesz od razu nawiązać połączenie z Azure-SSIS IR, zobacz [łączenie z Azure-SSIS IR](#connectssisir) , aby uzyskać więcej szczegółów. Możesz również nawiązać połączenie później, klikając prawym przyciskiem myszy węzeł projektu w Eksplorator rozwiązań oknie SSDT, aby wyskakujące menu. Następnie wybierz pozycję **Połącz z usługą SSIS w Azure Data Factory** element w programie **SSIS w Azure Data Factory** podmenu.

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Platforma Azure — Włączanie istniejących projektów SSIS

W przypadku istniejących projektów usług SSIS można je włączyć na platformie Azure, wykonując następujące czynności:

1. Kliknij prawym przyciskiem myszy węzeł projektu w Eksplorator rozwiązań oknie SSDT, aby wyskakujące menu. Następnie wybierz element **projektu z obsługą platformy Azure** w programie **SSIS w Azure Data Factory** podmenu, aby uruchomić **Kreatora projektu z obsługą platformy Azure**.

   ![Azure — Włączanie istniejącego projektu SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. Na stronie **wybierz konfigurację programu Visual Studio** wybierz swoją istniejącą konfigurację programu vs, aby zastosować ustawienia wykonywania pakietu na platformie Azure. Możesz również utworzyć nowy, jeśli jeszcze tego nie zrobiono, zobacz [Tworzenie nowej konfiguracji programu vs](https://docs.microsoft.com/visualstudio/ide/how-to-create-and-edit-configurations?view=vs-2019). Zaleca się, aby w środowiskach lokalnych i chmurowych było co najmniej dwie różne konfiguracje programu VS, dzięki czemu można włączyć projekt do konfiguracji chmury. W ten sposób, jeśli znasz swój projekt lub pakiety, możesz przypisać różne wartości do parametrów projektu lub pakietu w czasie wykonywania w oparciu o różne środowiska wykonawcze (na komputerze lokalnym lub na platformie Azure). Na przykład zobacz [przełączanie środowisk wykonywania pakietów](#switchenvironment).

   ![Wybierz konfigurację programu Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Platforma Azure — włączenie istniejących projektów usług SSIS wymaga ustawienia wersji serwera docelowego jako najnowszej obsługiwanej przez Azure-SSIS IR. Azure-SSIS IR jest obecnie oparta na **SQL Server 2017**. Upewnij się, że pakiety nie zawierają dodatkowych składników, które nie są obsługiwane w SQL Server 2017. Upewnij się również, że wszystkie zgodne dodatkowe składniki zostały również zainstalowane na Azure-SSIS IR za pośrednictwem instalacji niestandardowych, zobacz [dostosowywanie Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). Wybierz przycisk **dalej** , aby kontynuować.

   ![Przełącz wersję serwera docelowego](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Zobacz [łączenie się z Azure-SSIS IR](#connectssisir) , aby zakończyć łączenie projektu z Azure-SSIS IR.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Połącz projekty z obsługą platformy Azure z usługą SSIS w Azure Data Factory

Łącząc swoje projekty z platformą Azure z usługami SSIS w podajniku APD, można przekazać pakiety do Azure Files i uruchamiać je na Azure-SSIS IR. Można to zrobić, wykonując następujące czynności:

1. Na stronie **wprowadzenie do programu SSIS w ramach podajnika APD** Przejrzyj wprowadzenie i wybierz przycisk **dalej** , aby kontynuować.

   ![Wprowadzenie do programu SSIS w ramach podajnika APD](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Na stronie **Wybierz środowisko IR usług SSIS w podajniku APD** wybierz istniejący zestaw apd i Azure-SSIS IR, aby uruchomić pakiety. Możesz również utworzyć nowe, jeśli nie masz żadnego z nich.
   - Aby wybrać istniejący Azure-SSIS IR, najpierw wybierz odpowiednią subskrypcję platformy Azure i ADF.
   - W przypadku wybrania istniejącego podajnika APD, który nie ma żadnych Azure-SSIS IR, wybierz przycisk **Utwórz program SSIS IR** , aby utworzyć nowy w portalu ADF. Po utworzeniu możesz powrócić do tej strony, aby wybrać nowy Azure-SSIS IR.
   - Jeśli wybierzesz istniejącą subskrypcję platformy Azure, która nie ma żadnego podajnika APD, wybierz przycisk **Utwórz IR podczerwieni** , aby uruchomić **Kreatora tworzenia Integration Runtime**. W Kreatorze możesz wprowadzić wydaną lokalizację i prefiks, aby automatycznie utworzyć nową grupę zasobów platformy Azure, Data Factory i SSIS IR w Twoim imieniu, o nazwie w następującym wzorcu: **YourPrefix-RG/DF/IR-YourCreationTime**. Po utworzeniu możesz powrócić do tej strony, aby wybrać nowy ADF i Azure-SSIS IR.

   ![Wybierz środowisko IR usług SSIS w usłudze ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Na stronie **Wybieranie usługi Azure Storage** Wybierz istniejące konto usługi Azure Storage, aby przekazywać pakiety do Azure Files. Możesz również utworzyć nowy, jeśli nie masz żadnego z nich.
   - Aby wybrać istniejące konto usługi Azure Storage, najpierw wybierz odpowiednią subskrypcję platformy Azure.
   - Jeśli wybierzesz tę samą Azure-SSIS IR subskrypcję platformy Azure, która nie ma konta usługi Azure Storage, wybierz przycisk **Utwórz usługę Azure Storage** . Automatycznie utworzymy nową nazwę w Twoim imieniu w tej samej lokalizacji co Azure-SSIS IR o nazwie przez połączenie prefiksu nazwy Azure-SSIS IR i daty jego utworzenia. Po utworzeniu możesz powrócić do tej strony, aby wybrać nowe konto usługi Azure Storage.
   - Jeśli wybierzesz inną subskrypcję platformy Azure, która nie ma konta usługi Azure Storage, wybierz przycisk **Utwórz usługę Azure Storage** , aby utworzyć nowy element na Azure Portal. Po utworzeniu możesz powrócić do tej strony, aby wybrać nowe konto usługi Azure Storage.

   ![Wybieranie pozycji Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Wybierz przycisk **Połącz** , aby zakończyć łączenie projektu z Azure-SSIS IR. Zostanie wyświetlone wybrane Azure-SSIS IR i konto usługi Azure Storage w węźle **połączone zasoby platformy Azure** w Eksplorator rozwiązań oknie SSDT. Będziemy również regularnie odświeżać i wyświetlać stan Azure-SSIS IR. Możesz zarządzać Azure-SSIS IR, klikając prawym przyciskiem myszy węzeł, aby wyskakujące menu, a następnie wybierając element **Start\Stop\Manage** , który przeprowadzi Cię do portalu ADF.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Ocena project\packages SSIS na potrzeby wykonywania na platformie Azure

### <a name="assessing-single-or-multiple-packages"></a>Ocenianie jednego lub wielu pakietów

Przed wykonaniem pakietów na platformie Azure można ocenić je, aby wystawiać potencjalne problemy ze zgodnością w chmurze. Obejmują one blokady migracji i dodatkowe informacje, o których należy wiedzieć. 
-  Dostępne są opcje oceny pojedynczych pakietów jeden przez jeden lub wszystkie pakiety w tym samym czasie w ramach projektu.

   ![Oceń pakiet](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![Ocenianie projektu](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  W oknie **Raport z oceny** SSDT można znaleźć wszystkie potencjalne problemy ze zgodnością w chmurze, które są wymienione, z których każdy ma szczegółowy opis i zalecenia. Możesz również wyeksportować raport oceny do pliku CSV, który może być współużytkowany z każdą osobą, która powinna ograniczyć te problemy. 

   ![Raport z oceny](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>Pomijanie reguł oceny

Po upewnieniu się, że niektóre potencjalne problemy ze zgodnością z chmurą nie mają zastosowania lub zostały odpowiednio skorygowane w Twoich pakietach, można pominąć odpowiednie reguły oceny, które je wskazują. Spowoduje to zredukowanie szumu w kolejnych raportach oceny.
-  Wybierz łącze **Pomiń regułę oceny** w oknie **raport oceny** SSDT, aby wyskakujące okno **ustawień pomijania reguł oceny** , w którym można wybrać reguły oceny do wykluczenia.

   ![Ustawienia pomijania reguł oceny](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  Alternatywnie kliknij prawym przyciskiem myszy węzeł projektu w Eksplorator rozwiązań oknie SSDT, aby wyskakujące menu. Wybierz pozycję **Ustawienia z obsługą platformy Azure** w programie **SSIS w** podmenu Azure Data Factory, aby wypróbować okno zawierające strony właściwości projektu. Wybierz właściwość **pomijanie identyfikatorów reguł oceny** w sekcji **Ustawienia z włączoną obsługą platformy Azure** . Na koniec wybierz przycisk wielokropka (**...**), aby wypróbować okno **ustawień pomijania reguł oceny** , w którym można wybrać reguły oceny do pominięcia.

   ![Ustawienia z obsługą platformy Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Ustawienia pomijania reguł oceny za pomocą ustawień z obsługą platformy Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>Wykonywanie pakietów SSIS na platformie Azure

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Konfigurowanie ustawień z obsługą platformy Azure

Przed wykonaniem pakietów na platformie Azure można skonfigurować dla nich ustawienia z obsługą platformy Azure. Na przykład możesz włączyć uwierzytelnianie systemu Windows na Azure-SSIS IR, aby uzyskać dostęp do magazynów danych lokalnych/w chmurze, wykonując następujące czynności:

1. Kliknij prawym przyciskiem myszy węzeł projektu w Eksplorator rozwiązań oknie SSDT, aby wyskakujące menu. Następnie wybierz pozycję **Ustawienia z obsługą platformy Azure** w programie **SSIS w** podmenu Azure Data Factory, aby wypróbować okno zawierające strony właściwości projektu.

   ![Ustawienia z obsługą platformy Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Wybierz pozycję **Włącz Właściwość uwierzytelniania systemu Windows** w sekcji **Ustawienia usługi Azure Enabled** , a następnie w menu rozwijanym wybierz pozycję **prawda** . Następnie wybierz właściwość **poświadczenia uwierzytelniania systemu Windows** , a następnie wybierz przycisk wielokropka (**...**), aby wypróbować okno **poświadczenia uwierzytelniania systemu Windows** .

   ![Włącz uwierzytelnianie systemu Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Wprowadź poświadczenia uwierzytelniania systemu Windows. Na przykład w celu uzyskania dostępu do Azure Files można `Azure` odpowiednio wprowadzić, `YourStorageAccountName` i `YourStorageAccountKey` dla **domeny**, **nazwy użytkownika**i **hasła**.

   ![Poświadczenia uwierzytelniania systemu Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Rozpoczynanie wykonywania pakietów

Po nawiązaniu połączenia z projektami z obsługą platformy Azure z usługą SSIS w ramach podajnika APD, ocenianiu ich zgodności z chmurą i eliminowania potencjalnych problemów można wykonać/przetestować pakiety na Azure-SSIS IR.
-  Wybierz przycisk **Uruchom** na pasku narzędzi SSDT, aby rozwinąć menu. Następnie wybierz pozycję **wykonaj w elemencie platformy Azure** .

   ![Wykonaj na platformie Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Alternatywnie kliknij prawym przyciskiem myszy węzeł pakietu w Eksplorator rozwiązań oknie SSDT, aby wyskakujące menu. Następnie wybierz pozycję **Wykonaj pakiet w elemencie platformy Azure** .

   ![Wykonaj pakiet na platformie Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Wykonywanie pakietów na platformie Azure wymaga uruchomionego Azure-SSIS IR, więc jeśli Azure-SSIS IR zostanie zatrzymana, okno dialogowe zostanie wyświetlona, aby je uruchomić. Bez czasu instalacji niestandardowej ten proces powinien zostać zakończony w ciągu 5 minut, ale może potrwać około 20-30 minut w przypadku Azure-SSIS IR dołączenia do sieci wirtualnej. Po wykonaniu pakietów na platformie Azure Możesz zatrzymać Azure-SSIS IR, aby zarządzać jego uruchomionym kosztem, klikając prawym przyciskiem myszy jego węzeł w oknie Eksplorator rozwiązań SSDT, aby wyskakujące menu, a następnie wybierając element **Start\Stop\Manage** , który przeprowadzi Cię do portalu ADF.

### <a name="using-execute-package-task"></a>Korzystanie z zadania Execute Package

Jeśli pakiety zawierają zadania wykonywania pakietów, które odwołują się do pakietów podrzędnych przechowywanych w lokalnych systemach plików, wykonaj następujące dodatkowe czynności:

1. Przekaż pakiety podrzędne do Azure Files przy użyciu tego samego konta usługi Azure Storage połączonego z projektami i uzyskaj nową ścieżkę Universal Naming Convention (UNC), np. `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. Zastąp ścieżkę pliku pakietów podrzędnych w Menedżerze połączeń plików zadania wykonywania pakietów z nową ścieżką UNC
   - Jeśli maszyna lokalna z systemem SSDT nie może uzyskać dostępu do nowej ścieżki UNC, możesz wprowadzić ją w panelu Właściwości Menedżera połączeń plików
   - Alternatywnie można użyć zmiennej dla ścieżki pliku do przypisania właściwej wartości w czasie wykonywania

Jeśli pakiety zawierają zadania wykonywania pakietów, które odwołują się do pakietów podrzędnych w tym samym projekcie, nie trzeba wykonywać żadnych dodatkowych kroków.

### <a name="switching-package-protection-level"></a>Przełączanie poziomu ochrony pakietu

Wykonywanie pakietów SSIS na platformie Azure nie **EncryptSensitiveWithUserKey**obsługuje / poziomów ochrony EncryptSensitiveWithUserKey**EncryptAllWithUserKey** . W związku z tym, jeśli Twoje pakiety są skonfigurowane do korzystania z nich, tymczasowo przekonwertujemy **EncryptSensitiveWithPassword**je na / odpowiednio na poziomach ochrony EncryptSensitiveWithPassword**EncryptAllWithPassword** . Będziemy również losowo generować hasła szyfrowania podczas przekazywania pakietów do Azure Files do wykonania na Azure-SSIS IR.

> [!NOTE]
> Jeśli pakiety zawierają zadania pakietu Execute odwołujące się do pakietów podrzędnych skonfigurowanych do korzystania z poziomów ochrony **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** , należy ręcznie ponownie skonfigurować te pakiety podrzędne, **EncryptSensitiveWithPassword**aby / przed wykonaniem pakietów użyć odpowiednio poziomów ochrony EncryptSensitiveWithPassword**EncryptAllWithPassword** .

Jeśli pakiety są już skonfigurowane do korzystania z **EncryptSensitiveWithPassword** / poziomów ochrony EncryptSensitiveWithPassword**EncryptAllWithPassword** , zachowamy je bez zmian. Będziemy nadal losowo generować hasła szyfrowania podczas przekazywania pakietów do Azure Files w celu wykonania na Azure-SSIS IR.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> Przełączanie środowisk wykonywania pakietów

W przypadku Sparametryzuj projektu/pakietów w modelu wdrażania projektu można utworzyć wiele konfiguracji programu VS, aby przełączyć środowiska wykonywania pakietów. W ten sposób można przypisywać wartości specyficzne dla środowiska do parametrów projektu/pakietu w czasie wykonywania. Zaleca się, aby w środowiskach lokalnych i chmurowych było co najmniej dwie różne konfiguracje programu VS, dzięki czemu można je włączyć do konfiguracji chmury. Oto przykładowy krok po kroku przełączania środowisk wykonywania pakietów między komputerem lokalnym i platformą Azure:

1. Załóżmy, że pakiet zawiera zadanie systemu plików, które Ustawia atrybuty pliku. Po uruchomieniu na komputerze lokalnym ustawia on atrybuty pliku przechowywanego w lokalnym systemie plików. Gdy uruchomisz go na Azure-SSIS IR, chcesz ustawić atrybuty pliku przechowywanego w Azure Files. Najpierw utwórz parametr Package typu String i nadaj mu nazwę **FilePath** , aby pomieścić wartość docelowej ścieżki pliku.

   ![Utwórz parametr pakietu](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Następnie na stronie **Ogólne** okna **edytora zadań systemu plików** Sparametryzuj Właściwość **SourceVariable** w sekcji **połączenie źródłowe** za pomocą parametru pakietu **FilePath** . 

   ![Połączenie ze źródłem Sparametryzuj](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Domyślnie istnieje już istniejąca konfiguracja programu VS dla wykonań pakietów w środowisku lokalnym o nazwie **programowanie**. Utwórz nową konfigurację programu VS dla wykonań pakietów w środowisku chmury o nazwie **Azure**, zobacz [Tworzenie nowej konfiguracji programu vs](https://docs.microsoft.com/visualstudio/ide/how-to-create-and-edit-configurations?view=vs-2019), jeśli jeszcze tego nie zrobiono.

4. Podczas przeglądania parametrów pakietu wybierz przycisk **Dodaj parametry do konfiguracji** , aby otworzyć okno **Zarządzanie wartościami parametrów** dla pakietu. Następnie przypisz różne wartości docelowej ścieżki pliku do parametru pakietu **FilePath** w obszarze konfiguracje **opracowywania** i **platformy Azure** .

   ![Przypisywanie wartości parametrów](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure — Włącz projekt względem konfiguracji chmury, zobacz [Azure — Włączanie istniejących projektów usług SSIS](#azureenableproject), jeśli jeszcze tego nie zrobiono. Następnie skonfiguruj ustawienia z obsługą platformy Azure, aby włączyć uwierzytelnianie systemu Windows dla Azure-SSIS IR dostępu Azure Files, zobacz [Konfigurowanie ustawień z obsługą platformy Azure](#azureenabledsettings), jeśli jeszcze tego nie zrobiono.

6. Wykonaj pakiet na platformie Azure. Możesz przełączyć środowisko wykonywania pakietu z powrotem na maszynę lokalną, wybierając konfigurację **programistyczną** .

   ![Przełącz konfigurację programu Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>Korzystanie z pliku konfiguracji pakietu

Jeśli używasz plików konfiguracji pakietów w modelu wdrażania pakietów, możesz przypisać wartości specyficzne dla środowiska do właściwości pakietu w czasie wykonywania. Automatycznie przekażemy te pliki do swoich pakietów do Azure Files wykonywania na Azure-SSIS IR.

### <a name="checking-package-execution-logs"></a>Sprawdzanie dzienników wykonywania pakietów

Po rozpoczęciu wykonywania pakietu będziemy formatować i wyświetlać dzienniki w oknie **postępu** SSDT. W przypadku długotrwałego pakietu Okresowo aktualizujemy dzienniki w ciągu kilku minut. Możesz natychmiast anulować wykonywanie pakietu, wybierając przycisk **Zatrzymaj** na pasku narzędzi SSDT. Możesz również tymczasowo znaleźć dane pierwotne dzienników w następującej ścieżce UNC: `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , ale wyczyścimy ją po upływie jednego dnia.

## <a name="current-limitations"></a>Bieżące ograniczenia

-  SSDT z obsługą platformy Azure obsługuje tylko regiony w chmurze komercyjnej/globalnej i nie wspierają teraz regionów dla instytucji rządowych/krajowych.

## <a name="next-steps"></a>Następne kroki

Po zakończeniu korzystania z pakietów na platformie Azure z usługi SSDT można je wdrożyć i uruchamiać jako działania dotyczące wykonywania pakietów SSIS w potokach ADF, zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w potokach ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
