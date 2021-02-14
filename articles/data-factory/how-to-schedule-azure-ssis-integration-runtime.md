---
title: Jak zaplanować Azure-SSIS Integration Runtime
description: W tym artykule opisano sposób planowania uruchamiania i zatrzymywania Azure-SSIS Integration Runtime przy użyciu Azure Data Factory.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 40c3b8ab228c93fd4c681281d89d16f88ddf30f1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384371"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>How to start and stop Azure-SSIS Integration Runtime on a schedule (Jak uruchamiać i zatrzymywać środowisko Azure-SSIS Integration Runtime według harmonogramu)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób planowania uruchamiania i zatrzymywania Azure-SSIS Integration Runtime (IR) przy użyciu funkcji Azure Data Factory (ADF). Azure-SSIS IR to zasób obliczeniowy ADF przeznaczony do wykonywania pakietów SQL Server Integration Services (SSIS). Z uruchomionym Azure-SSIS IR jest związany koszt. W związku z tym zazwyczaj chcesz uruchomić swój IR tylko wtedy, gdy musisz wykonać pakiety usług SSIS na platformie Azure i zatrzymać środowisko IR, gdy nie jest już potrzebne. Do [ręcznego uruchamiania lub zatrzymywania środowiska IR](manage-azure-ssis-integration-runtime.md)można użyć interfejsu użytkownika ADF/app lub Azure PowerShell.

Alternatywnie możesz utworzyć działania sieci Web w potokach ADF, aby rozpocząć lub zatrzymać pracę w ramach harmonogramu, np. rozpoczynając od rano przed wykonaniem codziennych obciążeń ETL i zatrzymywać je po południu po zakończeniu.  Możesz również utworzyć łańcuch aktywności pakietu SSIS dla programu między dwoma działaniami sieci Web, które uruchamiają i zatrzymują środowisko IR, dzięki czemu Twoje środowisko IR rozpocznie się i zatrzyma na żądanie, w czasie przed wykonaniem pakietu lub po nim. Aby uzyskać więcej informacji o działaniu pakietu usług SSIS, zobacz artykuł [uruchamianie pakietu SSIS za pomocą działania wykonywania pakietu SSIS w potoku usługi ADF](how-to-invoke-ssis-package-ssis-activity.md) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli jeszcze nie zainicjowano obsługi administracyjnej Azure-SSIS IR, zainicjuj go, postępując zgodnie z instrukcjami podanymi w [samouczku](./tutorial-deploy-ssis-packages-azure.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Tworzenie i planowanie potoków ADF, które zaczynają i lub zatrzymują Azure-SSIS IR
W tej sekcji przedstawiono sposób używania działań sieci Web w potokach APD do uruchamiania/zatrzymywania Azure-SSIS IR zgodnie z harmonogramem lub uruchamiania & zatrzymywania go na żądanie. Poprowadzimy Cię przez proces tworzenia trzech potoków: 

1. Pierwszy potok zawiera działanie sieci Web, które uruchamia Azure-SSIS IR. 
2. Drugi potok zawiera działanie sieci Web, które uniemożliwia Azure-SSIS IR.
3. Trzeci potok zawiera działanie wykonywania pakietu SSIS w łańcuchu między dwoma działaniami sieci Web, które uruchamiają/zatrzymują Azure-SSIS IR. 

Po utworzeniu i przetestowaniu tych potoków można utworzyć wyzwalacz harmonogramu i skojarzyć go z dowolnym potokiem. Wyzwalacz harmonogramu definiuje harmonogram uruchamiania skojarzonego potoku. 

Na przykład można utworzyć dwa wyzwalacze. pierwszy plan jest uruchamiany codziennie o godzinie 6 AM i skojarzony z pierwszym potoku, podczas gdy druga z nich jest planowana do uruchomienia codziennie o godzinie 6 PM i skojarzona z drugim potokiem.  W ten sposób masz okres od 6 do 6 PM codziennie, gdy działa środowisko IR, gotowe do wykonywania codziennych obciążeń ETL.  

Jeśli utworzysz trzeci wyzwalacz, który ma być uruchamiany codziennie o północy i skojarzony z trzecią potoku, ten potok będzie uruchamiany o północy co dzień, uruchamiając go tuż przed wykonaniem pakietu, a następnie wykonując pakiet, a natychmiast po wykonaniu tego działania po zakończeniu tworzenia pakietu, dzięki czemu nie będzie działać Idly.

### <a name="create-your-adf"></a>Tworzenie ADF

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).    
2. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![Nowy->Fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Na stronie **Nowa fabryka danych** wprowadź **wartość myazuressisdatafactory** w polu **Nazwa**. 
      
   ![Strona Nowa fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Nazwa Twojego ADF musi być globalnie unikatowa. Jeśli wystąpi Poniższy błąd, Zmień nazwę Twojego ADF (np. Twojanazwamyazuressisdatafactory) i spróbuj utworzyć ją ponownie. Zobacz artykuł [reguły nazewnictwa Data Factory](naming-rules.md) , aby dowiedzieć się więcej o regułach nazewnictwa artefaktów ADF.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Wybierz swoją **subskrypcję** platformy Azure, w której chcesz utworzyć ADF. 
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę nowej grupy zasobów.   
         
   Aby dowiedzieć się więcej na temat grup zasobów, zobacz temat [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/management/overview.md) .
   
6. W obszarze **wersja** wybierz pozycję **v2** .
7. W polu **Lokalizacja** wybierz jedną z lokalizacji obsługiwanych do tworzenia APD z listy rozwijanej.
8. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
9. Kliknij pozycję **Utwórz**.
10. Na pulpicie nawigacyjnym platformy Azure zostanie wyświetlony następujący kafelek ze stanem: **wdrażanie Data Factory**. 

    ![kafelek Wdrażanie fabryki danych](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Po zakończeniu tworzenia zostanie wyświetlona strona ADF, jak pokazano poniżej.
   
    ![Strona główna fabryki danych](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Kliknij pozycję **utwórz & Monitoruj** , aby uruchomić interfejs użytkownika/aplikację ADF na osobnej karcie.

### <a name="create-your-pipelines"></a>Tworzenie potoków

1. Na **stronie Wprowadzenie** wybierz pozycję **Utwórz potok**. 

   ![Strona Wprowadzenie](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. W przyborniku **działania** rozwiń menu **ogólne** i przeciągnij & upuść działanie **sieci Web** na powierzchnię projektanta potoku. Na karcie **Ogólne** okna właściwości działania Zmień nazwę działania na **startMyIR**. Przejdź do karty **Ustawienia** i wykonaj następujące czynności.

    1. W polu **adres URL** wprowadź następujący adres URL dla interfejsu API REST, który rozpoczyna się Azure-SSIS IR, zamieniając `{subscriptionId}` ,, `{resourceGroupName}` `{factoryName}` i `{integrationRuntimeName}` z rzeczywistymi wartościami dla środowiska IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Alternatywnie, możesz również skopiować & wkleić identyfikator zasobu środowiska IR ze strony monitorowania w interfejsie użytkownika/aplikację ADF, aby zastąpić następującą część powyższego adresu URL: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![Identyfikator zasobu IR podczerwieni dla APD](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. W obszarze **Metoda** wybierz pozycję **post**. 
    3. W obszarze **treść** wprowadź `{"message":"Start my IR"}` . 
    4. W obszarze **uwierzytelnianie** wybierz pozycję plik **MSI** , aby użyć tożsamości zarządzanej dla Twojego ADF, zobacz temat [tożsamość zarządzana dla Data Factory](./data-factory-service-identity.md) artykułu, aby uzyskać więcej informacji.
    5. W obszarze **zasób** wpisz polecenie `https://management.azure.com/` .
    
       ![Harmonogram działania sieci Web APD w usłudze SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Sklonuj pierwszy potok, aby utworzyć drugi, zmieniając nazwę działania na **stopMyIR** i zastępując następujące właściwości.

    1. W polu **adres URL** wprowadź następujący adres URL dla interfejsu API REST, który kończy Azure-SSIS IR, zamieniając wartości,, `{subscriptionId}` `{resourceGroupName}` `{factoryName}` i `{integrationRuntimeName}` z rzeczywistymi wartościami dla środowiska IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. W obszarze **treść** wprowadź `{"message":"Stop my IR"}` . 

4. Utwórz trzeci potok, przeciągnij & upuść działanie **Wykonaj pakiet SSIS** z przybornika **działania** na powierzchnię projektanta potoku i skonfiguruj go zgodnie z instrukcjami w artykule [wywoływanie pakietu SSIS za pomocą działania wykonaj pakiet SSIS w](how-to-invoke-ssis-package-ssis-activity.md) ramach usługi ADF.  Alternatywnie możesz użyć działania **procedury składowanej** , a następnie skonfigurować go zgodnie z instrukcjami w artykule [wywoływanie pakietu SSIS za pomocą działania procedury składowanej w](how-to-invoke-ssis-package-stored-procedure-activity.md) ramach usługi ADF.  Następnie należy utworzyć łańcuch aktywności pakietu SSIS/procedury składowanej między dwoma działaniami sieci Web, które uruchamiają/zatrzymują środowisko IR, podobnie jak te działania sieci Web w pierwszych/drugim potokach.

   ![Aktywność sieci Web w ramach funkcji APD na żądanie SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Przypisz do siebie tożsamość zarządzaną dla roli **WSPÓŁAUTOR** APD, dzięki czemu działania sieci Web w jego potokach mogą wywołać interfejs API REST w celu uruchomienia/zatrzymania zainicjowanego przez urząd skarbowy platformy Azure-SSIS.  Na stronie ADF w Azure Portal kliknij pozycję **Kontrola dostępu (IAM)**, kliknij pozycję **+ Dodaj przypisanie roli**, a następnie w bloku **Dodaj przypisanie roli** wykonaj następujące czynności.

    1. W obszarze **rola** wybierz pozycję **współautor**. 
    2. W obszarze **Przypisywanie dostępu do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**. 
    3. W obszarze **Wybierz** Wyszukaj nazwę ADF i wybierz ją. 
    4. Kliknij pozycję **Zapisz**.
    
   ![Przypisanie roli tożsamości zarządzanego przez funkcję ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Zweryfikuj swój ADF i wszystkie ustawienia potoku, klikając pozycję **Weryfikuj wszystko/Sprawdź poprawność** na pasku narzędzi fabryki/potoku. Zamknij **dane wyjściowe walidacji fabryki/potoku** , klikając **>>** przycisk.  

   ![Weryfikowanie potoku](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Testowanie przebiegu potoków

1. Wybierz pozycję **Uruchom test** na pasku narzędzi dla każdego potoku i Zobacz okno **danych wyjściowych** w dolnym okienku. 

   ![Przebieg testowy](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Aby przetestować trzeci potok, uruchom SQL Server Management Studio (SSMS). W oknie **łączenie z serwerem** wykonaj następujące czynności. 

    1. W obszarze **Nazwa serwera** wprowadź **&lt; nazwę serwera &gt; . Database.Windows.NET**.
    2. Wybierz **opcje >>**.
    3. W obszarze **Połącz z bazą danych** wybierz pozycję **SSISDB**.
    4. Wybierz pozycję **Połącz**. 
    5. Rozwiń węzeł **katalogi usług Integration Services**  ->  **SSISDB** — > > > > **projekty** w folderze.  
    6. Kliknij prawym przyciskiem myszy określony pakiet usług SSIS do uruchomienia i wybierz pozycję **raporty**  ->  **standardowe raporty**  ->  **wszystkie wykonania**. 
    7. Sprawdź, czy uruchomiono. 

   ![Sprawdź przebieg pakietu SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Planowanie potoków

Teraz, gdy potok działa zgodnie z oczekiwaniami, można utworzyć wyzwalacze, aby uruchomić je w określonym cadences. Aby uzyskać szczegółowe informacje na temat kojarzenia wyzwalaczy z potokami, zobacz [wyzwalanie potoku zgodnie z harmonogramem](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) .

1. Na pasku narzędzi potoku wybierz pozycję **wyzwalacz** i wybierz pozycję **Nowy/Edytuj**. 

   ![Zrzut ekranu, który podświetla opcję menu Nowy/Edycja > wyzwalacza.](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. W okienku **Dodaj wyzwalacze** wybierz pozycję **+ Nowy**.

   ![Dodawanie wyzwalaczy — nowy](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. W oknie **nowe wyzwalacze** wykonaj następujące czynności: 

    1. W polu **Nazwa** wprowadź nazwę wyzwalacza. W poniższym przykładzie **Uruchom codziennie** jest nazwą wyzwalacza. 
    2. W obszarze **Typ** wybierz pozycję **harmonogram**. 
    3. W polu **Data rozpoczęcia (UTC)** wprowadź datę i godzinę rozpoczęcia w formacie UTC. 
    4. W polu **cykl** wprowadź wartość erze dla wyzwalacza. W poniższym przykładzie jest to **codziennie** jeden raz. 
    5. Na **koniec** wybierz pozycję **Brak Zakończ** lub wprowadź datę i godzinę zakończenia po wybraniu pozycji **Data**. 
    6. Wybierz pozycję **aktywowany** , aby aktywować wyzwalacz natychmiast po opublikowaniu całego ustawienia ADF. 
    7. Wybierz opcję **Dalej**.

   ![Wyzwalanie — > nowe/Edycja](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Na stronie **Parametry uruchomienia wyzwalacza** Przejrzyj wszystkie ostrzeżenia i wybierz pozycję **Zakończ**. 
5. Opublikuj wszystkie ustawienia ADF, wybierając pozycję **Opublikuj wszystko** na pasku narzędzi fabryka. 

   ![Publikuj wszystko](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Monitoruj potoki i Wyzwalacze w Azure Portal

1. Aby monitorować uruchomienia wyzwalacza i uruchomienia potoku, Użyj karty **monitor** po lewej stronie interfejsu użytkownika/aplikacji funkcji ADF. Aby uzyskać szczegółowe instrukcje, zobacz artykuł [monitorowanie potoku](quickstart-create-data-factory-portal.md#monitor-the-pipeline) .

   ![Uruchomienia potoków](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz pierwszy link (**Wyświetl uruchomienia działania**) w kolumnie **Akcje** . W przypadku trzeciego potoku zobaczysz trzy uruchomienia działania, po jednym dla każdego połączonego działania w potoku (działanie sieci Web, aby uruchomić środowisko IR, działanie procedury składowanej w celu wykonania pakietu oraz działanie sieci Web, aby zatrzymać środowisko IR). Aby wyświetlić ponownie uruchomienia potoku, wybierz link **potoki** u góry.

   ![Uruchomienia działania](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Aby wyświetlić uruchomienia wyzwalacza, wybierz pozycję **wyzwalacz uruchomienia** z listy rozwijanej w obszarze **uruchomienia potoku** u góry. 

   ![Uruchomienia wyzwalacza](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Monitorowanie potoków i wyzwalaczy przy użyciu programu PowerShell

Użyj skryptów, takich jak poniższe przykłady, aby monitorować potoki i wyzwalacze.

1. Pobierz stan uruchomienia potoku.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Pobierz informacje o wyzwalaczu.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Pobierz stan uruchomienia wyzwalacza.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Tworzenie i planowanie elementu runbook usługi Azure Automation uruchamiającego/zatrzymującego środowisko Azure-SSIS IR

W tej sekcji dowiesz się, jak utworzyć Azure Automation element Runbook, który wykonuje skrypt programu PowerShell, rozpoczynając/zatrzymując Azure-SSIS IR zgodnie z harmonogramem.  Jest to przydatne, gdy chcesz wykonać dodatkowe skrypty przed uruchomieniem/zatrzymywaniem środowiska IR na potrzeby przetwarzania wstępnego/końcowego.

### <a name="create-your-azure-automation-account"></a>Utwórz konto Azure Automation

Jeśli nie masz już konta Azure Automation, utwórz je, postępując zgodnie z instrukcjami w tym kroku. Aby uzyskać szczegółowe instrukcje, zobacz artykuł [Tworzenie konta Azure Automation](../automation/automation-quickstart-create-account.md) . W ramach tego kroku utworzysz konto **Uruchom jako platformy Azure** (nazwę główną usługi w Azure Active Directory) i przypiszesz mu rolę **współautor** w subskrypcji platformy Azure. Upewnij się, że jest to ta sama subskrypcja, która zawiera Twój ADF z użyciem usługi Azure SSIS IR. Azure Automation będzie używać tego konta do uwierzytelniania w celu Azure Resource Manager i działania w swoich zasobach. 

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika/aplikacja usługi ADF jest obsługiwana tylko w przeglądarkach sieci Web Microsoft Edge i Google Chrome.
2. Zaloguj się do [Azure Portal](https://portal.azure.com/).    
3. Wybierz pozycję **Nowy** w menu po lewej stronie, wybierz pozycję **monitorowanie + zarządzanie**, a następnie wybierz pozycję **Automatyzacja**. 

   ![Zrzut ekranu, który podświetla opcję automatyzacji > Monitorowanie + zarządzanie.](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. W okienku **Dodawanie konta usługi Automation** wykonaj następujące czynności.

    1. W obszarze **Nazwa** wprowadź nazwę konta Azure Automation. 
    2. W obszarze **subskrypcja** wybierz subskrypcję z funkcją ADF z Azure-SSIS IR. 
    3. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów, lub **Użyj istniejącej** do wybrania istniejącej. 
    4. W obszarze **Lokalizacja** wybierz lokalizację konta Azure Automation. 
    5. Potwierdź **utworzenie konta Uruchom jako platformy Azure** jako **tak**. Zostanie utworzona jednostka usługi w Azure Active Directory i zostanie przypisana rola **współautor** w subskrypcji platformy Azure.
    6. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby trwale wyświetlić ją na pulpicie nawigacyjnym platformy Azure. 
    7. Wybierz przycisk **Utwórz**. 

   ![> Monitorowanie + zarządzanie — Automatyzacja >](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Zobaczysz stan wdrożenia konta Azure Automation na pulpicie nawigacyjnym i powiadomieniach platformy Azure. 
    
   ![Wdrażanie automatyzacji](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Zostanie wyświetlona strona główna konta Azure Automation po pomyślnym utworzeniu. 

   ![Strona główna usługi Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importuj moduły ADF

1. W menu po lewej stronie wybierz pozycję **moduły** w obszarze **zasoby udostępnione** i sprawdź, czy na liście modułów znajduje się polecenie **AZ. DataFactory**  +  **AZ. profile** .

   ![Weryfikowanie wymaganych modułów](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Jeśli nie masz przeglądarki **AZ. DataFactory**, przejdź do Galeria programu PowerShell dla [modułu AZ. DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/), wybierz pozycję **Wdróż do Azure Automation**, wybierz konto Azure Automation, a następnie wybierz przycisk **OK**. Wróć do pozycji Wyświetl **moduły** w obszarze **udostępnione zasoby** w menu po lewej stronie i zaczekaj, aż zobaczysz **stan** " **AZ. DataFactory** module został zmieniony na **dostępne**.

    ![Weryfikowanie modułu Data Factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Jeśli nie masz przeglądarki **AZ. profile**, przejdź do Galeria programu PowerShell dla [modułu AZ. profile module](https://www.powershellgallery.com/packages/Az.profile/), wybierz pozycję **Wdróż do Azure Automation**, wybierz konto Azure Automation, a następnie wybierz przycisk **OK**. Wróć do pozycji Wyświetl **moduły** w obszarze **udostępnione zasoby** w menu po lewej stronie i zaczekaj, aż zobaczysz **stan** modułu **AZ. profile** , który został zmieniony na **dostępny**.

    ![Weryfikowanie modułu profilu](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Tworzenie elementu Runbook programu PowerShell

W poniższej sekcji przedstawiono procedurę tworzenia elementu Runbook programu PowerShell. Skrypt skojarzony z elementem Runbook uruchamia/kończy Azure-SSIS IR na podstawie polecenia określonego dla parametru **operacji** . Ta sekcja nie zawiera pełnych informacji dotyczących tworzenia elementu Runbook. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie elementu Runbook](../automation/automation-quickstart-create-runbook.md) .

1. Przejdź do karty **elementy Runbook** i wybierz pozycję **+ Dodaj element Runbook** z paska narzędzi. 

   ![Zrzut ekranu, który podświetla przycisk + Dodaj element Runbook.](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Wybierz pozycję **Utwórz nowy element Runbook** i wykonaj następujące czynności: 

    1. W obszarze **Nazwa** wprowadź **StartStopAzureSsisRuntime**.
    2. W obszarze **Typ elementu Runbook** wybierz pozycję **PowerShell**.
    3. Wybierz przycisk **Utwórz**.
    
   ![Dodaj przycisk elementu Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Skopiuj & wklej następujący skrypt programu PowerShell do okna skryptu elementu Runbook. Zapisz element Runbook, a następnie opublikuj go za pomocą przycisków **Zapisz** i **Opublikuj** na pasku narzędzi. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Edytuj element Runbook programu PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Przetestuj element Runbook, wybierając przycisk **Start** na pasku narzędzi. 

   ![Przycisk uruchamiania elementu Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. W okienku **Start Runbook** wykonaj następujące czynności: 

    1. W polu **Nazwa grupy zasobów** wprowadź nazwę grupy zasobów, w której znajduje się ADF, przy użyciu Azure-SSIS IR. 
    2. W polu **Nazwa fabryki danych** wprowadź nazwę usługi ADF z Azure-SSIS IR. 
    3. W polu **AZURESSISNAME** wprowadź nazwę Azure-SSIS IR. 
    4. W obszarze **operacja** wprowadź polecenie **Start**. 
    5. Wybierz przycisk **OK**.  

   ![Uruchom okno elementu Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. W oknie zadania wybierz pozycję kafelek **wyjściowy** . W oknie dane wyjściowe poczekaj na komunikat **# # # # # zakończony # #** # # # po wyświetleniu monitu **# # # # # rozpoczynającego # #**# # #. Uruchamianie Azure-SSIS IR trwa około 20 minut. Zamknij okno **zadania** i wróć do okna **elementu Runbook** .

   ![Zrzut ekranu, który podświetla kafelek dane wyjściowe.](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Powtórz dwa poprzednie kroki przy użyciu opcji **Zatrzymaj** jako wartość dla **operacji**. Uruchom ponownie element Runbook, wybierając przycisk **Start** na pasku narzędzi. Wprowadź nazwę grupy zasobów, ADF i Azure-SSIS IR. W obszarze **operacja** wpisz **stop**. W oknie dane wyjściowe poczekaj na komunikat **# # # # # zakończony # #** # # # po wyświetleniu monitu **# # # # # Zatrzymywanie # #**# # #. Zatrzymywanie Azure-SSIS IR nie trwa od momentu jego uruchomienia. Zamknij okno **zadania** i wróć do okna **elementu Runbook** .

8. Możesz również wyzwolić element Runbook za pomocą elementu webhook, który można utworzyć, wybierając element menu elementów **webhook** lub zgodnie z harmonogramem, który można utworzyć, wybierając element menu **harmonogramy** w sposób opisany poniżej.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Utwórz harmonogramy dla elementu Runbook, aby uruchomić/zatrzymać Azure-SSIS IR

W poprzedniej sekcji utworzono Azure Automation element Runbook, który może uruchamiać lub zatrzymywać Azure-SSIS IR. W tej sekcji utworzysz dwa harmonogramy dla elementu Runbook. Podczas konfigurowania pierwszego harmonogramu należy określić pozycję **Rozpocznij** dla **operacji**. Podobnie podczas konfigurowania drugiego należy określić wartość **Zatrzymaj** dla **operacji**. Aby uzyskać szczegółowe instrukcje dotyczące tworzenia harmonogramów, zobacz artykuł [Tworzenie harmonogramu](../automation/shared-resources/schedules.md#create-a-schedule) .

1. W oknie **Runbook** wybierz pozycję **harmonogramy**, a następnie wybierz pozycję **+ Dodaj harmonogram** na pasku narzędzi. 

   ![Środowisko IR Azure SSIS — rozpoczęte](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. W okienku **Planowanie elementu Runbook** wykonaj następujące czynności: 

    1. Wybierz pozycję **Połącz harmonogram z elementem Runbook**. 
    2. Wybierz pozycję **Utwórz nowy harmonogram**.
    3. W okienku **Nowy harmonogram** wpisz **Rozpocznij pracę** w środowisku IR dla **nazwy**. 
    4. Dla pozycji **Rozpocznij** Wprowadź godzinę, która jest późniejsza niż bieżąca godzina. 
    5. W obszarze **cykl** wybierz pozycję **cykliczne**. 
    6. Dla opcji **Powtarzaj co** wprowadź **1** i wybierz **dzień**. 
    7. Wybierz przycisk **Utwórz**. 

   ![Harmonogram uruchamiania środowiska IR platformy Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Przejdź do opcji **Parametry i Uruchom ustawienia** kartę. Określ grupę zasobów, ADF i nazwy Azure-SSIS IR. W obszarze **operacja** wprowadź polecenie **Start** i wybierz polecenie **OK**. Ponownie wybierz **przycisk OK** , aby **wyświetlić stronę harmonogram** dla elementu Runbook. 

   ![Zrzut ekranu, który podświetla pole operacji.](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Powtórz dwa poprzednie kroki, aby utworzyć harmonogram o nazwie **Zatrzymaj środowisko IR codziennie**. Wprowadź czas, który jest co najmniej 30 minut po upływie czasu określonego dla **codziennego harmonogramu dla uruchomienia IR** . W obszarze **operacja** wpisz **stop** i wybierz **przycisk OK**. Ponownie wybierz **przycisk OK** , aby **wyświetlić stronę harmonogram** dla elementu Runbook. 

5. W oknie **elementu Runbook** wybierz pozycję **zadania** w menu po lewej stronie. Zadania utworzone przez harmonogramy powinny być widoczne w określonym czasie i ich Stanach. Możesz zobaczyć szczegóły zadania, takie jak jego dane wyjściowe, podobnie jak w przypadku przetestowania elementu Runbook. 

   ![Harmonogram dla gwiazdy do środowiska Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Po zakończeniu testowania Wyłącz harmonogramy, edytując je. Wybierz pozycję **harmonogramy** w menu po lewej stronie, wybierz pozycję **Uruchom IR codziennie/Zatrzymaj środowisko IR codziennie** i wybierz pozycję **nie** , aby **włączyć**. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujący wpis w blogu:
-   [Modernizowanie i zwiększanie przepływów pracy ETL/ELT przy użyciu działań SSIS w potokach ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Zobacz następujące artykuły wchodzące w skład usług SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Wdrażanie, uruchamianie i monitorowanie pakietu usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure (Łączenie z wykazem usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure (Planowanie wykonywania pakietów na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)