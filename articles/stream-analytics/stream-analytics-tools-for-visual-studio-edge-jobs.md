---
title: Azure Stream Analytics zadań brzegowych w programie Visual Studio
description: W tym artykule opisano sposób tworzenia, debugowania i tworzenia Stream Analytics na zadaniach IoT Edge przy użyciu narzędzi Stream Analytics Tools for Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 09151ea0fe3d419401d576149f6655b8cdc09f8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019962"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Opracowywanie zadań Stream Analytics Edge przy użyciu narzędzi Visual Studio Tools

W ramach tego samouczka nauczysz się korzystać z narzędzi Stream Analytics Tools for Visual Studio. Dowiesz się, jak tworzyć, debugować i tworzyć Stream Analyticse zadania brzegowe. Po utworzeniu i przetestowaniu zadania możesz przejść do Azure Portal, aby wdrożyć je na urządzeniach. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące wymagania wstępne:

* Zainstaluj [program Visual studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual studio 2015](https://www.visualstudio.com/vs/older-downloads/)lub [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Obsługiwane są wersje Enterprise (Ultimate/Premium), Professional i Community. Wersja Express nie jest obsługiwana.  

* Postępuj zgodnie z [instrukcjami instalacji](stream-analytics-tools-for-visual-studio-edge-jobs.md) , aby zainstalować narzędzia Stream Analytics Tools for Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Tworzenie projektu Stream Analytics Edge 

W programie Visual Studio wybierz pozycję **plik**  >  **Nowy**  >  **projekt**. Przejdź do listy **Szablony** po lewej stronie > rozwiń węzeł **Azure Stream Analytics**  >  **Stream Analytics Edge**  >  **Azure Stream Analytics krawędź aplikacji**. Podaj nazwę, lokalizację i nazwę rozwiązania dla projektu, a następnie wybierz **przycisk OK**.

![Nowy projekt Stream Analytics Edge w programie Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Po utworzeniu projektu przejdź do **Eksplorator rozwiązań** , aby wyświetlić hierarchię folderów.

![Widok Eksploratora rozwiązań Stream Analytics zadania brzegowego](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Wybierz prawidłową subskrypcję

1. Z menu **Widok** programu Visual Studio wybierz pozycję **Eksplorator serwera**.  

2. Kliknij prawym przyciskiem myszy pozycję **Azure** > wybierz pozycję **Połącz z subskrypcją Microsoft Azure** > a następnie zaloguj się przy użyciu konta platformy Azure.

## <a name="define-inputs"></a>Definiowanie danych wejściowych

1. Na **Eksplorator rozwiązań** **rozwiń węzeł dane wejściowe,** który powinien zostać wyświetlony na wejściu o nazwie **EdgeInput.jsna**. Kliknij dwukrotnie, aby wyświetlić jego ustawienia.  

2. Ustaw typ źródła na **strumień danych**. Następnie ustaw wartość Source na **Hub Edge**, format serializacji zdarzenia na **JSON** i kodowanie na **UTF8**. Opcjonalnie możesz zmienić nazwę **aliasu wejściowego**, pozostawićmy ją jako przeznaczoną dla tego przykładu. W przypadku zmiany nazwy aliasu wejściowego Użyj nazwy określonej podczas definiowania zapytania. Wybierz pozycję **Zapisz**, aby zapisać ustawienia.  
   ![Konfiguracja danych wejściowych zadania Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definiowanie danych wyjściowych

1. W **Eksplorator rozwiązań** rozwiń węzeł Outputs ( **dane wyjściowe)** powinien zostać wyświetlony komunikat o nazwie **EdgeOutput.jsna stronie**. Kliknij dwukrotnie, aby wyświetlić jego ustawienia.  

2. Upewnij się, że ustawiono opcję ujścia, aby wybrać **centrum brzegowe**, ustawić format serializacji zdarzenia na **JSON**, ustawić kodowanie na **UTF8** i ustawić **tablicę** formatu. Opcjonalnie można zmienić nazwę **aliasu danych wyjściowych**, pozostawićmy ją jako przeznaczoną dla tego przykładu. W przypadku zmiany nazwy aliasu wyjściowego Użyj nazwy podanej podczas definiowania zapytania. Wybierz pozycję **Zapisz**, aby zapisać ustawienia. 
   ![Konfiguracja wyjściowa Stream Analytics zadania](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definiowanie zapytania przekształcenia

Stream Analytics zadania wdrożone w środowiskach Stream Analytics IoT Edge obsługują większość [Stream Analytics dokumentacji języka zapytań](/stream-analytics-query/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). Jednak następujące operacje nie są jeszcze obsługiwane w przypadku Stream Analytics zadań brzegowych: 


|**Kategoria**  | **Polecenie**  |
|---------|---------|
|Inne operatory | <ul><li>PODZIEL NA PARTYCJE</li><li>SYGNATURA CZASOWA PRZEZ</li><li>Funkcja zdefiniowana przez użytkownika w języku JavaScript</li><li>Agregacje zdefiniowane przez użytkownika (UDA)</li><li>GetMetadataPropertyValue</li><li>Używanie więcej niż 14 wartości zagregowanych w jednym kroku</li></ul>   |

Po utworzeniu zadania Stream Analytics Edge w portalu kompilator automatycznie wyświetli ostrzeżenie, jeśli nie używasz obsługiwanego operatora.

W programie Visual Studio Zdefiniuj następujące zapytanie transformacji w edytorze zapytań (**plik Script. asaql**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Przetestuj zadanie lokalnie

Aby przetestować zapytanie lokalnie, należy przekazać przykładowe dane. Możesz uzyskać przykładowe dane, pobierając dane rejestracji z [repozytorium GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) i zapisując je na komputerze lokalnym. 

1. Aby przekazać przykładowe dane, kliknij prawym przyciskiem myszy **EdgeInput.jsw** pliku i wybierz polecenie **Dodaj lokalne dane wejściowe**  

2. W oknie podręcznym > **przeglądać** przykładowe dane ze ścieżki lokalnej > wybierz pozycję **Zapisz**.
   ![Lokalna konfiguracja danych wejściowych w programie Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Plik o nazwie **local_EdgeInput.json** jest automatycznie dodawany do folderu Inputs.  
4. Można uruchomić je lokalnie lub przesłać na platformę Azure. Aby przetestować zapytanie, wybierz pozycję **Uruchom lokalnie**.  
   ![Opcje przebiegu zadania Stream Analytics w programie Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. Okno wiersza polecenia przedstawia stan zadania. Po pomyślnym uruchomieniu zadania tworzony jest folder "2018-02-23-11-31-42" w ścieżce folderu projektu "Visual Studio 2015 \ Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Przejdź do ścieżki folderu, aby wyświetlić wyniki w folderze lokalnym:

   Możesz również zalogować się do Azure Portal i sprawdzić, czy zadanie zostało utworzone. 

   ![Folder wyników zadania Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Prześlij zadanie na platformę Azure

1. Przed przesłaniem zadania do platformy Azure musisz nawiązać połączenie z subskrypcją platformy Azure. Otwórz > **Eksplorator serwera** kliknij prawym przyciskiem myszy pozycję **Azure**  >  **Connect, aby Microsoft Azure subskrypcję** > zalogować się do subskrypcji platformy Azure.  

2. Aby przesłać zadanie na platformę Azure, przejdź do edytora zapytań, > wybierz pozycję **Prześlij do platformy Azure**.  

3. Zostanie otwarte okno podręczne. Wybierz aktualizację istniejącego zadania Stream Analytics Edge lub Utwórz nowe. Po zaktualizowaniu istniejącego zadania zastąpi ono całą konfigurację zadania, w tym scenariuszu zostanie opublikowane nowe zadanie. Wybierz pozycję **Utwórz nowe zadanie Azure Stream Analytics** > wprowadź nazwę zadania, taką jak **MyASAEdgeJob** > wybierz wymaganą **subskrypcję**, **grupę zasobów** i **lokalizację** > wybierz pozycję **Prześlij**.

   ![Prześlij zadanie Stream Analytics do platformy Azure z programu Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Teraz zadanie Stream Analytics Edge zostało utworzone. Możesz zapoznać się z [samouczkiem Uruchamianie zadań w IoT Edge](stream-analytics-edge.md) , aby dowiedzieć się, jak wdrożyć go na urządzeniach. 

## <a name="manage-the-job"></a>Zarządzanie zadaniem 

Możesz wyświetlić stan zadania i diagram zadania z Eksplorator serwera. W **Stream Analytics** w **Eksplorator serwera** rozwiń pozycję subskrypcja i grupę zasobów, w której wdrożono zadanie Stream Analytics Edge. Możesz wyświetlić MyASAEdgejob z **utworzonym** stanem. Rozwiń węzeł zadania, a następnie kliknij go dwukrotnie, aby otworzyć widok zadań.

![Opcje zarządzania zadaniami Eksploratora serwera](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Okno widok zadania udostępnia operacje, takie jak odświeżanie zadania, usuwanie zadania i otwieranie zadania z Azure Portal.

![Diagram zadań i inne opcje w programie Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Następne kroki

* [Więcej informacji na temat Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [IoT Edge — samouczek](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Wyślij opinię do zespołu przy użyciu tej ankiety](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u)