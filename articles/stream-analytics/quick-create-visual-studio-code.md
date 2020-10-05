---
title: Szybki Start — Tworzenie zadania Azure Stream Analytics w programie Visual Studio Code
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę przez utworzenie zadania Stream Analytics, skonfigurowanie danych wejściowych i wyjściowych oraz zdefiniowanie zapytania z Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 01/18/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 5ba47522f483b6c9b2a03e99f3608c58e916e010
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947726"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Szybki Start: Tworzenie zadania Azure Stream Analytics w Visual Studio Code (wersja zapoznawcza)

W tym przewodniku szybki start pokazano, jak utworzyć i uruchomić zadanie Azure Stream Analytics przy użyciu rozszerzenia narzędzi Azure Stream Analytics Tools for Visual Studio Code. Przykładowe zadanie odczytuje dane przesyłane strumieniowo z urządzenia IoT Hub platformy Azure. Zdefiniujesz zadanie obliczające średnią temperaturę, gdy wynosi ona ponad 27°, i zapisujące wynikowe zdarzenia wyjściowe w magazynie obiektów blob.

> [!NOTE]
> Narzędzia Visual Studio i Visual Studio Code Tools nie obsługują zadań w regionach Chiny Wschodnie, Chiny Północne, Niemczech centralnych i Niemczech północnych.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

* Zainstaluj [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>Zainstaluj rozszerzenie narzędzi Azure Stream Analytics

1. Otwórz program Visual Studio Code.

2. W obszarze **rozszerzenia** w lewym okienku wyszukaj pozycję **Stream Analytics** i wybierz pozycję **zainstaluj** w rozszerzeniu **narzędzia Azure Stream Analytics** .

3. Po zainstalowaniu rozszerzenia Sprawdź, czy w **włączonych rozszerzeniach**są widoczne **narzędzia Azure Stream Analytics** .

   ![Narzędzia Azure Stream Analytics w obszarze włączone rozszerzenia w programie Visual Studio Code](./media/quick-create-visual-studio-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>Aktywuj rozszerzenie narzędzi Azure Stream Analytics

1. Wybierz ikonę **platformy Azure** na pasku działania Visual Studio Code. W obszarze **Stream Analytics** na pasku bocznym wybierz pozycję **Zaloguj się do platformy Azure**.

   ![Zaloguj się do platformy Azure w Visual Studio Code](./media/quick-create-visual-studio-code/azure-sign-in.png)

2. Gdy użytkownik jest zalogowany, nazwa konta platformy Azure zostanie wyświetlona na pasku stanu w lewym dolnym rogu okna Visual Studio Code.

> [!NOTE]
> Rozszerzenie narzędzi Azure Stream Analytics Tools automatycznie zaloguje Cię w następnym czasie, jeśli nie wylogujesz się. Jeśli Twoje konto ma uwierzytelnianie dwuskładnikowe, zalecamy korzystanie z uwierzytelniania przy użyciu telefonu zamiast numeru PIN.
> Jeśli masz problemy z wyświetlaniem zasobów, wylogowanie się i ponowne zalogowanie jest zwykle pomocne. Aby się wylogować, wprowadź polecenie `Azure: Sign Out` .

## <a name="prepare-the-input-data"></a>Przygotowywanie danych wejściowych

Przed zdefiniowaniem zadania Stream Analytics należy przygotować dane, które później są skonfigurowane jako dane wejściowe zadania. Aby przygotować dane wejściowe wymagane przez zadanie, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Utwórz zasób**  >  **Internet rzeczy**  >  **IoT Hub**.

3. W okienku **IoT Hub** wprowadź następujące informacje:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Subskrypcja  | \<Your subscription\> |  Wybierz subskrypcję platformy Azure, której chcesz użyć. |
   |Grupa zasobów   |   asaquickstart-resourcegroup  |   Wybierz pozycję **Utwórz nową** i wprowadź nazwę nowej grupy zasobów dla swojego konta. |
   |Region  |  \<Select the region that is closest to your users\> | Wybierz lokalizację geograficzną, w której będzie można hostować Centrum IoT. Użyj lokalizacji znajdującej się najbliżej użytkowników. |
   |Nazwa centrum IoT Hub  | MyASAIoTHub  |   Wybierz nazwę Centrum IoT Hub.   |

   ![Tworzenie centrum IoT](./media/quick-create-visual-studio-code/create-iot-hub.png)

4. Wybierz pozycję **Dalej: Ustaw rozmiar i skalę**.

5. Wybierz wybór **cen i warstwy skalowania**. Na potrzeby tego przewodnika Szybki start wybierz warstwę **F1 — Bezpłatna**, jeśli jest ona nadal dostępna w ramach subskrypcji. Jeśli warstwa bezpłatna jest niedostępna, użyj najniższej dostępnej warstwy. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Rozmiar i skalowanie Centrum IoT Hub](./media/quick-create-visual-studio-code/iot-hub-size-and-scale.png)

6. Wybierz pozycję **Przeglądanie + tworzenie**. Przejrzyj informacje o Centrum IoT Hub i wybierz pozycję **Utwórz**. Proces tworzenia centrum IoT Hub może potrwać kilka minut. Postęp można monitorować w okienku **powiadomienia** .

7. W menu nawigacyjnym Centrum IoT wybierz pozycję **Dodaj** w obszarze **urządzenia IoT**. Dodaj identyfikator dla **identyfikatora urządzenia**i wybierz pozycję **Zapisz**.

   ![Dodawanie urządzenia do centrum IoT Hub](./media/quick-create-visual-studio-code/add-device-iot-hub.png)

8. Po utworzeniu urządzenia Otwórz urządzenie z listy **urządzenia IoT** . Skopiuj ciąg w **parametrach połączenia (klucz podstawowy)** i Zapisz go w Notatniku do późniejszego użycia.

   ![Kopiuj parametry połączenia urządzenia usługi IoT Hub](./media/quick-create-visual-studio-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>Uruchamianie symulatora IoT

1. Otwórz symulator [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) w nowej karcie lub nowym oknie przeglądarki.

2. Zastąp symbol zastępczy w wierszu 15 ciągiem połączenia urządzenia usługi IoT Hub zapisanym wcześniej.

3. Wybierz pozycję **Uruchom**. Wyniki powinny zawierać dane czujnika i komunikaty wysyłane do centrum IoT Hub.

   ![Symulator usługi Azure IoT online Raspberry Pi z danymi wyjściowymi](./media/quick-create-visual-studio-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Tworzenie magazynu obiektów blob

1. W lewym górnym rogu Azure Portal wybierz pozycję **Utwórz zasób**  >  **Magazyn**  >  **konto magazynu**.

2. W okienku **Utwórz konto magazynu** wprowadź nazwę konta magazynu, lokalizację i grupę zasobów. Wybierz tę samą lokalizację i grupę zasobów co utworzony przez Ciebie Centrum IoT. Następnie wybierz pozycję **Przegląd + Utwórz** , aby utworzyć konto.

   ![Tworzenie konta magazynu](./media/quick-create-visual-studio-code/create-storage-account.png)

3. Po utworzeniu konta magazynu wybierz kafelek **obiekty blob** w okienku **Przegląd** .

   ![Omówienie kont magazynu](./media/quick-create-visual-studio-code/blob-storage.png)

4. Na stronie **Blob Service** wybierz pozycję **Kontener** i podaj nazwę swojego kontenera, na przykład **container1**. Pozostaw **publiczny poziom dostępu** jako **prywatny (brak dostępu anonimowego)** i wybierz **przycisk OK**.

   ![Tworzenie kontenera obiektów blob](./media/quick-create-visual-studio-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Tworzenie projektu usługi Stream Analytics

1. W Visual Studio Code wybierz **kombinację klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń. Następnie wprowadź **ASA** i wybierz **ASA: Utwórz nowy projekt**.

   ![Tworzenie nowego projektu](./media/quick-create-visual-studio-code/create-new-project.png)

2. Wprowadź nazwę projektu, na przykład **myASAproj**, i wybierz folder dla projektu.

    ![Utwórz nazwę projektu](./media/quick-create-visual-studio-code/create-project-name.png)

3. Nowy projekt zostanie dodany do obszaru roboczego. Projekt Stream Analytics składa się z trzech folderów **Inputs**: wejść **, Output**i **Functions**. Zawiera także skrypt zapytania **(*. asaql)**, **JobConfig.jsw** pliku i **asaproj.jsw** pliku konfiguracji.

    **asaproj.jsw** pliku konfiguracji zawiera informacje dotyczące danych wejściowych, wyjściowych i pliku konfiguracji zadania, które są konieczne do przesłania zadania Stream Analytics do platformy Azure.

    ![Stream Analytics plików projektu w Visual Studio Code](./media/quick-create-visual-studio-code/asa-project-files.png)

> [!Note]
> Gdy dodajesz dane wejściowe i wyjściowe z palety poleceń, odpowiednie ścieżki są dodawane do **asaproj.js** automatycznie. Jeśli dodasz lub usuniesz dane wejściowe lub wyjściowe bezpośrednio na dysku, musisz ręcznie dodać je lub usunąć z **asaproj.js**. Możesz wybrać opcję umieszczania danych wejściowych i wyjściowych w jednym miejscu, a następnie odwoływać się do nich w różnych zadaniach, określając ścieżki w każdej **asaproj.js** w pliku.

## <a name="define-the-transformation-query"></a>Definiowanie zapytania przekształcenia

1. Otwórz **myASAproj. asaql** z folderu projektu.

2. Dodaj następujące zapytanie:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="define-a-live-input"></a>Definiowanie danych wejściowych na żywo

1. Kliknij prawym przyciskiem myszy folder **Inputs** w projekcie Stream Analytics. Następnie wybierz pozycję **ASA: Dodaj dane wejściowe** z menu kontekstowego.

    ![Dodawanie danych wejściowych z folderu Inputs](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

    Lub wybierz **kombinację klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń i wprowadzić **ASA: Dodaj dane wejściowe**.

   ![Dodawanie Stream Analytics danych wejściowych w Visual Studio Code](./media/quick-create-visual-studio-code/add-input.png)

2. Wybierz **IoT Hub** dla typu danych wejściowych.

   ![Wybierz pozycję IoT Hub jako opcję wejściową](./media/quick-create-visual-studio-code/iot-hub.png)

3. Jeśli dodano dane wejściowe z palety poleceń, wybierz skrypt kwerendy Stream Analytics, który będzie używać danych wejściowych. Należy ją automatycznie wypełnić ścieżką pliku **myASAproj. asaql**.

   ![Wybierz skrypt Stream Analytics w Visual Studio Code](./media/quick-create-visual-studio-code/asa-script.png)

4. Wybierz **pozycję Wybierz z subskrypcji platformy Azure** z menu rozwijanego.

    ![Wybierz z subskrypcji](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Edytuj nowo wygenerowaną **IoTHub1.jsw** pliku przy użyciu następujących wartości. Zachowaj wartości domyślne dla pól, które nie są wymienione w tym miejscu.

   |Ustawienie|Sugerowana wartość|Opis|
   |-------|---------------|-----------|
   |Nazwa|Dane wejściowe|Wprowadź nazwę identyfikującą dane wejściowe zadania.|
   |IotHubNamespace|MyASAIoTHub|Wybierz lub wprowadź nazwę Centrum IoT. Nazwy centrów IoT są wykrywane automatycznie, jeśli są tworzone w tej samej subskrypcji.|
   |SharedAccessPolicyName|iothubowner| |

   Możesz użyć funkcji CodeLens, aby pomóc wprowadzić ciąg, wybrać z listy rozwijanej lub zmienić tekst bezpośrednio w pliku. Poniższy zrzut ekranu przedstawia **wybór z subskrypcji** jako przykładu. Poświadczenia są wyświetlane na liście i zapisywane w lokalnym programie Credential Manager.

   ![Konfigurowanie danych wejściowych w Visual Studio Code](./media/quick-create-visual-studio-code/configure-input.png)

   ![Skonfiguruj wartość wejściową w Visual Studio Code](./media/quick-create-visual-studio-code/configure-input-value.png)

## <a name="preview-input"></a>Podgląd danych wejściowych

Wybierz pozycję **Podgląd danych** w **IoTHub1.jsna** początku wiersza. Niektóre dane wejściowe zostaną pobrane z Centrum IoT Hub i wyświetlone w oknie podglądu. Ten proces może potrwać trochę czasu.

 ![Podgląd danych wejściowych na żywo](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-an-output"></a>Definiowanie danych wyjściowych

1. Wybierz **kombinację klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń. Następnie wprowadź **ASA: Dodawanie danych wyjściowych**.

   ![Dodaj dane wyjściowe Stream Analytics w Visual Studio Code](./media/quick-create-visual-studio-code/add-output.png)

2. Wybierz **BLOB Storage** dla typu ujścia.

3. Wybierz skrypt kwerendy Stream Analytics, który będzie używać tych danych wejściowych.

4. Wprowadź nazwę pliku wyjściowego jako **BlobStorage**.

5. Edytuj **BlobStorage** , korzystając z następujących wartości. Zachowaj wartości domyślne dla pól, które nie są wymienione w tym miejscu. Użyj funkcji CodeLens, aby pomóc wybrać z listy rozwijanej lub wprowadzić ciąg.

   |Ustawienie|Sugerowana wartość|Opis|
   |-------|---------------|-----------|
   |Nazwa|Dane wyjściowe| Wprowadź nazwę identyfikującą dane wyjściowe zadania.|
   |Konto magazynu|asaquickstartstorage|Wybierz lub wprowadź nazwę konta magazynu. Nazwy kont magazynu są wykrywane automatycznie, jeśli są tworzone w tej samej subskrypcji.|
   |Kontener|container1|Wybierz istniejący kontener utworzony na koncie magazynu.|
   |Wzorzec ścieżki|output|Wprowadź nazwę ścieżki pliku, który ma zostać utworzony w kontenerze.|

   ![Konfigurowanie danych wyjściowych w Visual Studio Code](./media/quick-create-visual-studio-code/configure-output.png)

## <a name="compile-the-script"></a>Kompilowanie skryptu

Kompilacja skryptów sprawdza składnię i generuje Azure Resource Manager szablonów wdrożenia automatycznego.

Istnieją dwa sposoby wyzwalania kompilacji skryptu:

- Wybierz skrypt z obszaru roboczego, a następnie Skompiluj z palety poleceń.

   ![Użyj palety poleceń Visual Studio Code, aby skompilować skrypt](./media/quick-create-visual-studio-code/compile-script1.png)

- Kliknij prawym przyciskiem myszy skrypt i wybierz polecenie **ASA: Kompiluj skrypt**.

    ![Kliknij prawym przyciskiem myszy skrypt Stream Analytics, aby skompilować](./media/quick-create-visual-studio-code/compile-script2.png)

Po skompilowaniu można znaleźć dwa wygenerowane szablony Azure Resource Manager w folderze **Deploy** projektu. Te dwa pliki są używane do automatycznego wdrażania.

![Stream Analytics szablonów wdrażania w Eksploratorze plików](./media/quick-create-visual-studio-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Prześlij zadanie Stream Analytics do platformy Azure

1. W oknie Edytor skryptów skryptu zapytania wybierz pozycję **Prześlij do platformy Azure**.

   ![Wybierz z tekstu subskrypcji w Edytorze skryptów](./media/quick-create-visual-studio-code/submit-job.png)

2. Wybierz swoją subskrypcję z listy rozwijanej.

3. Wybierz **pozycję Wybierz zadanie**. Następnie wybierz pozycję **Utwórz nowe zadanie**.

4. Wprowadź nazwę zadania, **myASAjob**. Następnie postępuj zgodnie z instrukcjami, aby wybrać grupę zasobów i lokalizację.

5. Wybierz pozycję **Prześlij do platformy Azure**. Dzienniki można znaleźć w oknie danych wyjściowych. 

6. Po utworzeniu zadania można je zobaczyć w **eksploratorze Stream Analytics**.

    ![Wymienione zadanie w Eksploratorze Stream Analytics](./media/quick-create-visual-studio-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych

1. Otwórz **Stream Analytics Explorer** w Visual Studio Code i Znajdź swoje zadanie, **myASAJob**.

2. Kliknij prawym przyciskiem myszy nazwę zadania. Następnie wybierz pozycję **Rozpocznij** z menu kontekstowego.

   ![Uruchom zadanie Stream Analytics w Visual Studio Code](./media/quick-create-visual-studio-code/start-asa-job-vs-code.png)

3. Wybierz **teraz** w oknie podręcznym, aby uruchomić zadanie.

4. Należy pamiętać, że stan zadania został zmieniony na **uruchomiony**. Kliknij prawym przyciskiem myszy nazwę zadania i wybierz pozycję **Otwórz widok zadania w portalu** , aby wyświetlić metryki zdarzeń danych wejściowych i wyjściowych. Ta akcja może potrwać kilka minut.

5. Aby wyświetlić wyniki, otwórz magazyn obiektów BLOB w rozszerzeniu Visual Studio Code lub Azure Portal.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, zadanie przesyłania strumieniowego i wszystkie powiązane zasoby. Usunięcie zadania pozwala uniknąć rozliczania jednostek przesyłania strumieniowego zużywanych przez zadanie. 

Jeśli planujesz użyć tego zadania w przyszłości, możesz je zatrzymać i uruchomić ponownie później. Jeśli nie zamierzasz używać tego zadania ponownie, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start:

1. W menu po lewej stronie w Azure Portal wybierz pozycję **grupy zasobów** , a następnie wybierz nazwę utworzonego zasobu.  

2. Na stronie grupy zasobów wybierz pozycję **Usuń**. W polu tekstowym wprowadź nazwę zasobu do usunięcia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono proste zadanie Stream Analytics przy użyciu Visual Studio Code. Możesz również wdrożyć zadania Stream Analytics przy użyciu [Azure Portal](stream-analytics-quick-create-portal.md), [programu PowerShell](stream-analytics-quick-create-powershell.md)i [programu Visual Studio](stream-analytics-quick-create-vs.md).

Aby dowiedzieć się więcej o Azure Stream Analytics narzędziach dla Visual Studio Code, przejdź do następujących artykułów:

* [Testowanie Stream Analytics zapytań lokalnie z przykładowymi danymi przy użyciu Visual Studio Code](visual-studio-code-local-run.md)

* [Przetestuj Azure Stream Analytics zadania lokalnie, aby uzyskać aktywne dane wejściowe z Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Używanie Visual Studio Code do wyświetlania zadań Azure Stream Analytics](visual-studio-code-explore-jobs.md)

* [Konfigurowanie potoków ciągłej integracji/ciągłego wdrażania przy użyciu pakietu npm](setup-cicd-vs-code.md)
