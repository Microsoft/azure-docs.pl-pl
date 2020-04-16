---
title: Jak zaplanować środowisko uruchomieniowe integracji azure-SSIS
description: W tym artykule opisano sposób planowania uruchamiania i zatrzymywania środowiska wykonawczego integracji platformy Azure-SSIS przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 8/2/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 39f758b779e7c4935feab2424be16b829db8e46b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399513"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>How to start and stop Azure-SSIS Integration Runtime on a schedule (Jak uruchamiać i zatrzymywać środowisko Azure-SSIS Integration Runtime według harmonogramu)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób planowania uruchamiania i zatrzymywania środowiska wykonawczego integracji platformy Azure-SSIS (IR) przy użyciu usługi Azure Data Factory (ADF). Azure-SSIS IR to zasób obliczeniowy usługi ADF przeznaczony do wykonywania pakietów usług integracji programu SQL Server (SSIS). Uruchamianie usługi Azure-SSIS IR ma z nim związane koszty. W związku z tym zazwyczaj chcesz uruchomić podczerwał tylko wtedy, gdy trzeba wykonać pakiety SSIS na platformie Azure i zatrzymać podczerwenie, gdy nie są już potrzebne. Za pomocą interfejsu użytkownika usługi ADF (UI)/app lub programu Azure PowerShell można [ręcznie uruchomić lub zatrzymać podczerwony](manage-azure-ssis-integration-runtime.md)).

Alternatywnie można utworzyć działania sieci Web w potokach usługi ADF, aby rozpocząć/zatrzymać podczerwień zgodnie z harmonogramem, na przykład uruchomienie go rano przed wykonaniem codziennych obciążeń ETL i zatrzymanie go po południu po ich zakończeniu.  Można również łańcuch execute SSIS działania pakietu między dwoma działaniami sieci Web, które rozpoczynają i zatrzymują i zatrzymać i zatrzymać iR rozpocznie/zatrzymać na żądanie, tylko w czasie przed/po wykonaniu pakietu. Aby uzyskać więcej informacji na temat wykonywania działania pakietu SSIS, zobacz [Uruchamianie pakietu SSIS przy użyciu działania Wykonywanie pakietu SSIS w artykule potoku usługi ADF.](how-to-invoke-ssis-package-ssis-activity.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie zostały już aprowizować azure-SSIS IR, aprowizować go, postępując zgodnie z instrukcjami w [samouczku](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Tworzenie i planowanie potoków usługi ADF, które rozpoczynają i zatrzymują usługę Azure-SSIS IR
W tej sekcji pokazano, jak używać działań sieci Web w potokach usługi ADF, aby rozpocząć/zatrzymać usługę Azure-SSIS IR zgodnie z harmonogramem lub rozpocząć & zatrzymać ją na żądanie. Poprowadzimy Cię do stworzenia trzech rurociągów: 

1. Pierwszy potok zawiera działanie sieci Web, które uruchamia usługę Azure-SSIS IR. 
2. Drugi potok zawiera działanie sieci Web, które zatrzymuje azure-SSIS IR.
3. Trzeci potok zawiera działanie Wykonaj pakiet SSIS połączone między dwoma działaniami sieci Web, które rozpoczynają/zatrzymują działanie usługi Azure-SSIS IR. 

Po utworzeniu i przetestowaniu tych potoków można utworzyć wyzwalacz harmonogramu i skojarzyć go z dowolnym potokiem. Wyzwalacz harmonogramu definiuje harmonogram uruchamiania skojarzonego potoku. 

Na przykład można utworzyć dwa wyzwalacze, pierwszy z nich jest zaplanowane do uruchomienia codziennie o 6 rano i skojarzone z pierwszym potoku, podczas gdy drugi jest zaplanowane do uruchomienia codziennie o godzinie 18:00 i skojarzone z drugim potoku.  W ten sposób masz okres od 6 RANO do 6 PM każdego dnia, gdy podczerwień jest uruchomiony, gotowy do wykonywania codziennych obciążeń ETL.  

Jeśli utworzysz trzeci wyzwalacz, który jest zaplanowany do uruchomienia codziennie o północy i skojarzone z trzeciego potoku, tego potoku będzie działać o północy każdego dnia, uruchomienie podczerwania tuż przed wykonaniem pakietu, następnie wykonanie pakietu i natychmiast zatrzymanie podczerwone tuż po wykonaniu pakietu, więc iR nie będzie uruchomiony idly.

### <a name="create-your-adf"></a>Tworzenie podajnika ADF

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).    
2. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![Nowy->Fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Na stronie **Nowa fabryka danych** wprowadź **myAzureSsisDataFactory** dla **name**. 
      
   ![Strona Nowa fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Nazwa podajnika ADF musi być unikatowa globalnie. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę podajnika ADF (np. yournameMyAzureSsisDataFactory) i spróbuj utworzyć go ponownie. Zobacz [fabryka danych — reguły nazewnictwa](naming-rules.md) artykuł, aby dowiedzieć się więcej o reguły nazewnictwa artefaktów usługi ADF.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Wybierz **subskrypcję** platformy Azure, w ramach której chcesz utworzyć podajnik ADF. 
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
   - Wybierz **pozycję Utwórz nowy**i wprowadź nazwę nowej grupy zasobów.   
         
   Aby dowiedzieć się więcej o grupach zasobów, zobacz [Zarządzanie zasobami platformy Azure za pomocą grup zasobów.](../azure-resource-manager/management/overview.md)
   
6. W przypadku **wersji**wybierz **opcję V2** .
7. W obszarze **Lokalizacja**wybierz jedną z lokalizacji obsługiwanych dla tworzenia podajnika ADF z listy rozwijanej.
8. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
9. Kliknij przycisk **Utwórz**.
10. Na pulpicie nawigacyjnym platformy Azure zobaczysz następujący kafelek o stanie: **Wdrażanie fabryki danych**. 

    ![kafelek Wdrażanie fabryki danych](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Po zakończeniu tworzenia można wyświetlić stronę ADF, jak pokazano poniżej.
   
    ![Strona główna fabryki danych](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Kliknij **pozycję Monitor & autora,** aby uruchomić interfejs użytkownika/aplikację ADF na osobnej karcie.

### <a name="create-your-pipelines"></a>Tworzenie potoków

1. Na stronie **Rozpocznijmy pracę** wybierz pozycję **Utwórz potok**. 

   ![Strona Wprowadzenie](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. W **przyborniku Działania** rozwiń rozwiń menu **Ogólne** i przeciągnij & upuść aktywność **sieci Web** na powierzchnię projektanta potoku. Na karcie **Ogólne** okna właściwości działania zmień nazwę działania na **startMyIR**. Przełącz się do karty **Ustawienia** i wykonaj następujące czynności.

    1. W przypadku **adresu URL**wprowadź następujący adres URL interfejsu API REST, który uruchamia usługę Azure-SSIS IR, zastępując `{subscriptionId}` `{resourceGroupName}`, `{factoryName}`oraz `{integrationRuntimeName}` z rzeczywistymi wartościami dla urządzenia IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Alternatywnie, możesz również skopiować & wkleić identyfikator zasobu urządzenia IR ze strony monitorowania w usłudze ADF UI/aplikacji, aby zastąpić następującą część powyższego adresu URL:`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![Identyfikator zasobu usługi ADF SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. W **ciekacz ,wybierz** **POZYCJĘ POST**. 
    3. W **Body**przypadku `{"message":"Start my IR"}`treści wprowadź . 
    4. W przypadku **uwierzytelniania**wybierz **pozycję MSI,** aby użyć tożsamości zarządzanej dla usługi ADF, zobacz [Tożsamość zarządzana dla fabryki danych](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) artykuł, aby uzyskać więcej informacji.
    5. W przypadku `https://management.azure.com/` **zasobu**wprowadź .
    
       ![Harmonogram aktywności sieci Web usługi ADF SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Sklonuj pierwszy potok, aby utworzyć drugi, zmieniając nazwę działania na **stopMyIR** i zastępując następujące właściwości.

    1. W przypadku **adresu URL**wprowadź następujący adres URL interfejsu API `{subscriptionId}`REST, który zatrzymuje usługę Azure-SSIS IR, zastępując , `{resourceGroupName}` `{factoryName}`oraz `{integrationRuntimeName}` rzeczywiste wartości dla urządzenia IR:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. W **Body**przypadku `{"message":"Stop my IR"}`treści wprowadź . 

4. Utwórz trzeci potok, przeciągnij & upuść działanie **Wykonaj pakiet SSIS** z zestawu narzędzi **Działania** na powierzchni projektanta potoku i skonfiguruj go zgodnie z instrukcjami w [wywołaniu pakietu SSIS przy użyciu działania Wykonywanie pakietu SSIS w artykule usługi ADF.](how-to-invoke-ssis-package-ssis-activity.md)  Alternatywnie można użyć działania **procedura składowana** zamiast i skonfigurować go zgodnie z instrukcjami w [wywołać pakiet SSIS przy użyciu działania procedury składowanej w artykule ADF.](how-to-invoke-ssis-package-stored-procedure-activity.md)  Następnie należy łańcuch wykonać pakiet SSIS/procedura składowana działania między dwoma działaniami sieci Web, które rozpoczynają/zatrzymują podczerwórkę, podobne do tych działań sieci Web w pierwszym/drugim potoku.

   ![Aktywność aktywność w sieci Web ADF na żądanie SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Przypisz tożsamość zarządzaną dla roli **współautora** usługi ADF do siebie, więc działania sieci Web w jego potokach można wywołać interfejs API REST, aby uruchomić/zatrzymać azure-SSIS IRs inicjowane w nim.  Na stronie ADF w witrynie Azure Portal kliknij pozycję **Kontrola dostępu (IAM)**, kliknij przycisk **+ Dodaj przypisanie roli**, a następnie w bloku Dodaj **przypisanie roli** wykonaj następujące czynności.

    1. W przypadku **opcji Rola**wybierz pozycję **Współautor**. 
    2. Aby **przypisać dostęp do**, wybierz **użytkownika, grupę lub jednostkę usługi Azure AD**. 
    3. W **polu Wybierz**wyszukaj nazwę podajnika ADF i wybierz ją. 
    4. Kliknij pozycję **Zapisz**.
    
   ![Przypisanie roli tożsamości zarządzanej usługi ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Sprawdź poprawność podajnika ADF i wszystkich ustawień potoku, klikając **pozycję Sprawdź poprawność wszystkich/Sprawdź poprawność** na pasku narzędzi fabrycznym/potoku. Zamknij **wyjście sprawdzania poprawności fabrycznej/potoku,** klikając **>>** przycisk.  

   ![Weryfikowanie potoku](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Przetestuj uruchomienie potoków

1. Wybierz **opcję Uruchom test** na pasku narzędzi dla każdego potoku i zobacz Okno dane **wyjściowe** w dolnym okienku. 

   ![Przebieg testowy](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Aby przetestować trzeci potok, uruchom program SQL Server Management Studio (SSMS). W oknie **Połącz z serwerem** wykonaj następujące czynności. 

    1. W przypadku **nazwy serwera**wprowadź ** &lt;nazwę&gt;serwera usługi Azure SQL Database .database.windows.net**.
    2. Wybierz **opcję Opcje >>**.
    3. Aby **połączyć się z bazą danych,** wybierz **SSISDB**.
    4. Wybierz przycisk **Połącz**. 
    5. Rozwiń **katalogi usług integracyjnych** -> **SSISDB** -> Folder -> **Projects** -> **Pakiety**> projektu SSIS . 
    6. Kliknij prawym przyciskiem myszy określony pakiet SSIS, aby uruchomić i wybrać **raporty** -> **standardowe Wszystkie** -> **wykonania**. 
    7. Sprawdź, czy został uruchomiony. 

   ![Weryfikowanie uruchomienia pakietu SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Zaplanuj swoje rurociągi

Teraz, gdy potoki działają zgodnie z oczekiwaniami, można utworzyć wyzwalacze, aby uruchomić je w określonych rytmach. Aby uzyskać szczegółowe informacje na temat kojarzenia wyzwalaczy z potokami, zobacz [Wyzwalanie potoku w](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) artykule harmonogramu.

1. Na pasku narzędzi potoku wybierz pozycję **Wyzwalacz** i wybierz pozycję **Nowy/Edytuj**. 

   ![Wyzwalacz -> Nowy/Edytuj](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. W okienku **Dodawanie wyzwalaczy** wybierz pozycję **+ Nowy**.

   ![Dodaj wyzwalacze - Nowy](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. W **okienku Nowy wyzwalacz** wykonaj następujące czynności: 

    1. W **ciemięgosce**wprowadź nazwę wyzwalacza. W poniższym przykładzie **Uruchom codziennie** jest nazwa wyzwalacza. 
    2. W obszarze **Typ**wybierz pozycję **Zaplanuj**. 
    3. W przypadku **daty rozpoczęcia (UTC)** wprowadź datę i godzinę rozpoczęcia w pliku UTC. 
    4. W przypadku **cyklu**wprowadź rytm wyzwalacza. W poniższym przykładzie jest **codziennie** raz. 
    5. W obszarze **Koniec**wybierz **pozycję Brak zakończenia** lub wprowadź datę i godzinę zakończenia po **wybraniu opcji Data.** 
    6. Wybierz **opcję Aktywowano,** aby aktywować wyzwalacz natychmiast po opublikowaniu całych ustawień podajnika ADF. 
    7. Wybierz opcję **Dalej**.

   ![Wyzwalacz -> Nowy/Edytuj](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Na stronie **Parametry uruchamiania wyzwalacza** przejrzyj dowolne ostrzeżenie i wybierz pozycję **Zakończ**. 
5. Opublikuj całe ustawienia podajnika ADF, wybierając **pozycję Opublikuj wszystko** na fabrycznym pasku narzędzi. 

   ![Opublikuj wszystko](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Monitorowanie potoków i wyzwalaczy w witrynie Azure portal

1. Aby monitorować przebiegi wyzwalaczy i przebiegi potoku, użyj karty **Monitor** po lewej stronie interfejsu użytkownika/aplikacji usługi ADF. Aby uzyskać szczegółowe kroki, zobacz Monitorowanie artykułu [potoku.](quickstart-create-data-factory-portal.md#monitor-the-pipeline)

   ![Uruchomienia potoków](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Aby wyświetlić przebiegi działania skojarzone z uruchomieniem potoku, wybierz pierwsze łącze **(Wyświetl przebiegi działania)** w kolumnie **Akcje.** Dla trzeciego potoku zostaną wyświetlone trzy uruchomienia działania, po jednym dla każdego działania łańcuchowego w potoku (działanie sieci Web, aby uruchomić działanie podczerwone, działanie procedury składowanej do wykonania pakietu i działanie sieci Web, aby zatrzymać podczerwone działanie). Aby wyświetlić uruchomień potoku ponownie, wybierz **łącze Potoki** u góry.

   ![Uruchomienia działania](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Aby wyświetlić uruchomienia wyzwalacza, wybierz **wyzwalacz uruchamia z** listy rozwijanej w obszarze **Przebiegi potoku** u góry. 

   ![Uruchomienia wyzwalacza](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Monitorowanie potoków i wyzwalaczy za pomocą programu PowerShell

Użyj skryptów, takich jak poniższe przykłady do monitorowania potoków i wyzwalaczy.

1. Uzyskaj stan uruchomienia potoku.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Uzyskaj informacje o wyzwalaczu.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Uzyskaj stan uruchomienia wyzwalacza.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Tworzenie i planowanie elementu runbook usługi Azure Automation uruchamiającego/zatrzymującego środowisko Azure-SSIS IR

W tej sekcji dowiesz się, aby utworzyć projekt owy usługi Azure Automation, który wykonuje skrypt programu PowerShell, uruchamianie/zatrzymywanie usługi Azure-SSIS IR zgodnie z harmonogramem.  Jest to przydatne, gdy chcesz wykonać dodatkowe skrypty przed/po uruchomieniu/zatrzymaniu podczerwieni dla przetwarzania przed/po.

### <a name="create-your-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

Jeśli nie masz jeszcze konta usługi Azure Automation, utwórz je, postępując zgodnie z instrukcjami w tym kroku. Aby uzyskać szczegółowe kroki, zobacz [Tworzenie konta usługi Azure Automation.](../automation/automation-quickstart-create-account.md) W ramach tego kroku utworzysz konto **Azure Run As** (podmiot usługi w usłudze Azure Active Directory) i przypiszesz mu rolę **współautora** w subskrypcji platformy Azure. Upewnij się, że jest to ta sama subskrypcja, która zawiera twój podajnik ADF z usługą Azure SSIS IR. Usługa Azure Automation użyje tego konta do uwierzytelniania w usłudze Azure Resource Manager i działania na podstawie zasobów. 

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika/aplikacja ADF jest obsługiwana tylko w przeglądarkach microsoft edge i google chrome.
2. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).    
3. Wybierz **pozycję Nowy** w menu po lewej stronie, wybierz pozycję Monitorowanie + **Zarządzanie**i wybierz **pozycję Automatyzacja**. 

   ![Nowy -> Monitoring + Zarządzanie -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. W **okienku Dodaj konto automatyzacji** wykonaj następujące czynności.

    1. W obszarze **Nazwa**wprowadź nazwę konta usługi Azure Automation. 
    2. W przypadku **subskrypcji**wybierz subskrypcję, która ma twój podajnik ADF z usługą Azure-SSIS IR. 
    3. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nowy,** aby utworzyć nową grupę zasobów, lub **Użyj istniejącej,** aby wybrać istniejącą. 
    4. W przypadku **lokalizacji**wybierz lokalizację dla swojego konta usługi Azure Automation. 
    5. Potwierdź **utwórz konto Azure Run jako konto** jako **tak**. Podmiot usługi zostanie utworzony w usłudze Azure Active Directory i przypisany rolę **współautora** w subskrypcji platformy Azure.
    6. Wybierz **pozycję Przypnij do pulpitu nawigacyjnego,** aby wyświetlić go na stałe na pulpicie nawigacyjnym platformy Azure. 
    7. Wybierz pozycję **Utwórz**. 

   ![Nowy -> Monitoring + Zarządzanie -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Stan wdrożenia konta usługi Azure Automation zostanie wyświetlony na pulpicie nawigacyjnym platformy Azure i powiadomieniach. 
    
   ![Wdrażanie automatyzacji](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Po pomyślnym utworzeniu strony głównej konta usługi Azure Automation zostanie wyświetlenie strony głównej konta usługi Azure Automation. 

   ![Strona główna automatyzacji](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importowanie modułów ADF

1. Wybierz **sekcję Moduły** w **obszarze ZASOBY UDOSTĘPNIONE** w menu po lewej stronie i sprawdź, czy na liście modułów znajduje się **Az.DataFactory** + **Az.Profile.**

   ![Weryfikowanie wymaganych modułów](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Jeśli nie masz **Az.DataFactory**, przejdź do Galerii programu PowerShell dla [modułu Az.DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/), wybierz **pozycję Wdrażanie w usłudze Azure Automation**, wybierz swoje konto usługi Azure Automation, a następnie wybierz przycisk **OK**. Wróć, aby wyświetlić **moduły** w sekcji **ZASOBY UDOSTĘPNIONE** w menu po lewej stronie i poczekaj, aż zobaczysz **status** modułu **Az.DataFactory** zmienionego na **Dostępny**.

    ![Sprawdź moduł Fabryka danych](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Jeśli nie masz **Az.Profile**, przejdź do galerii programu PowerShell dla [Az.Profile moduł](https://www.powershellgallery.com/packages/Az.profile/), wybierz **opcję Wdrażanie w usłudze Azure Automation**, wybierz swoje konto usługi Azure Automation, a następnie wybierz przycisk **OK**. Wróć, aby wyświetlić **sekcję Moduły** w **zasobach udostępnionych** w menu po lewej stronie i poczekaj, aż zobaczysz **stan** modułu **Az.Profile** zmienionego na **Dostępny**.

    ![Sprawdź moduł Profil](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Tworzenie runbooka programu PowerShell

W poniższej sekcji przedstawiono kroki tworzenia elementów runbook programu PowerShell. Skrypt skojarzony z systemem runbook albo uruchamia/zatrzymuje Azure-SSIS IR na podstawie polecenia, które określisz dla **parametru OPERATION.** W tej sekcji nie podano pełnych szczegółów dotyczących tworzenia ego księgi runbook. Aby uzyskać więcej informacji, zobacz Tworzenie artykułu [egoisty.](../automation/automation-quickstart-create-runbook.md)

1. Przełącz kartę **Do śmięty** i wybierz **+ Dodaj projekt runbook** z paska narzędzi. 

   ![Przycisk Dodawanie podręcznika runbooka](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Wybierz **pozycję Utwórz nowy projekt runbook** i wykonaj następujące czynności: 

    1. W **yjrz nazwę**wprowadź **StartStopAzureSsisRuntime**.
    2. W przypadku **typu eksmisja**wybierz pozycję **PowerShell**.
    3. Wybierz pozycję **Utwórz**.
    
   ![Przycisk Dodawanie podręcznika runbooka](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Skopiuj & wklej następujący skrypt programu PowerShell do okna skryptu życiowego. Zapisz, a następnie opublikuj swój projekt runbook za pomocą przycisków **Zapisz** i **Opublikuj** na pasku narzędzi. 

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

   ![Edytowanie podręcznika programu PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Przetestuj swój projekt uruchamiający, wybierając przycisk **Start** na pasku narzędzi. 

   ![Przycisk Rozpocznij pracę](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. W okienku **Uruchom program Uruchamiaj** wykonaj następujące czynności: 

    1. W obszarze **Nazwa grupy zasobów**wprowadź nazwę grupy zasobów, która ma podajnik ADF z usługą Azure-SSIS IR. 
    2. W przypadku **nazwy fabryki danych**wprowadź nazwę podajnika ADF za pomocą usługi Azure-SSIS IR. 
    3. W przypadku **usługi AZURESSISNAME**wprowadź nazwę usługi Azure-SSIS IR. 
    4. W przypadku **operacji**wprowadź **start**. 
    5. Kliknij przycisk **OK**.  

   ![Okno uruchamiania chyłkania](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. W oknie zadania wybierz kafelek **Wyjście.** W oknie danych wyjściowych poczekaj na wiadomość **##### Zakończona #####** po **wyświetleniach ##### Uruchamianie #####**. Uruchomienie usługi Azure-SSIS IR trwa około 20 minut. Zamknij okno **Zadania** i wróć do okna **Runbook.**

   ![Azure SSIS IR - rozpoczęty](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Powtórz poprzednie dwa kroki, używając **stop** jako wartości **dla OPERACJI**. Ponownie rozpocznij program runbook, wybierając przycisk **Start** na pasku narzędzi. Wprowadź nazwy grupy zasobów, podajnika ADF i usługi Azure-SSIS. W przypadku **operacji**wprowadź **stop**. W oknie danych wyjściowych poczekaj na wiadomość **##### Zakończona #####** po **wyświetleniach ##### Zatrzymanie #####**. Zatrzymywanie usługi Azure-SSIS IR nie trwa tak długo, jak jego uruchomienie. Zamknij okno **Zadania** i wróć do okna **Runbook.**

8. Można również wyzwolić element runbook za pomocą elementu webhook, który można utworzyć, wybierając element menu **Webhooks** lub harmonogram, który można utworzyć, wybierając pozycję menu **Harmonogramy,** jak określono poniżej.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Tworzenie harmonogramów dla ego łańca, aby rozpocząć/zatrzymać usługę Azure-SSIS IR

W poprzedniej sekcji utworzono swój projekt ujeżdny usługi Azure Automation, który można uruchomić lub zatrzymać Azure-SSIS IR. W tej sekcji utworzysz dwa harmonogramy dla ego księgi runbook. Podczas konfigurowania pierwszego harmonogramu należy określić **przycisk START** dla **operacji**. Podobnie podczas konfigurowania drugiego, należy określić **STOP** dla **operacji**. Aby uzyskać szczegółowe kroki tworzenia harmonogramów, zobacz Tworzenie artykułu [harmonogramu.](../automation/shared-resources/schedules.md#creating-a-schedule)

1. W oknie **Uruchomieniu** wybierz pozycję **Harmonogramy**i wybierz pozycję **+ Dodaj harmonogram** na pasku narzędzi. 

   ![Azure SSIS IR - rozpoczęty](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. W **okienku Harmonogram yjsk owy** wykonaj następujące czynności: 

    1. Wybierz **pozycję Połącz harmonogram z schematem ekscesowym**. 
    2. Wybierz **pozycję Utwórz nowy harmonogram**.
    3. W **okienku Nowy harmonogram** wprowadź codziennie **wpisaj uruchom podczerwony** dla **name**. 
    4. W przypadku **rozpoczynania**wprowadź godzinę, która jest kilka minut po bieżącym czasie. 
    5. W przypadku **cyklu**wybierz pozycję **Cykliczne**. 
    6. Dla **powtarzać co**, wprowadź **1** i wybierz **Dzień**. 
    7. Wybierz pozycję **Utwórz**. 

   ![Harmonogram uruchamiania usługi Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Przełącz się do **karty Ustawienia parametrów i uruchom go.** Określ nazwy podajnika ADF, ADF i Azure-SSIS. W przypadku **operacji**wprowadź **przycisk START** i wybierz **przycisk OK**. Wybierz **przycisk OK** ponownie, aby wyświetlić stronę **Harmonogramy** w zestawieniu. 

   ![Harmonogram wpatrywania się w usługę Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Powtórz poprzednie dwa kroki, aby utworzyć harmonogram o nazwie **Zatrzymaj IR codziennie**. Wprowadź czas, który jest co najmniej 30 minut po czasie określonym dla **uruchamiania ir harmonogram dzienny.** W przypadku **operacji**wprowadź **stop** i wybierz **PRZYCISK OK**. Wybierz **przycisk OK** ponownie, aby wyświetlić stronę **Harmonogramy** w zestawieniu. 

5. W oknie **Element runbook** wybierz pozycję **Zadania** w menu po lewej stronie. Zadania utworzone przez harmonogramy powinny być widoczne w określonych godzinach i ich stanach. Możesz zobaczyć szczegóły zadania, takie jak jego dane wyjściowe, podobne do tych, które zostały wyświetlone po przetestowaniu ego księgi runbook. 

   ![Harmonogram wpatrywania się w usługę Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Po zakończeniu testowania wyłącz harmonogramy, edytując je. Wybierz **pozycję Harmonogramy** w menu po lewej stronie, wybierz **opcję Uruchom podczerwone codziennie/Zatrzymaj podczerwone**i wybierz pozycję **Nie** dla **włączone .** 

## <a name="next-steps"></a>Następne kroki
Zobacz następujący wpis w blogu:
-   [Zmodernizuj i rozszerzyj przepływy pracy ETL/ELT o działania SSIS w potokach ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Zobacz następujące artykuły wchodzące w skład usług SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Wdrażanie, uruchamianie i monitorowanie pakietu usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure (Łączenie z wykazem usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure (Planowanie wykonywania pakietów na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
