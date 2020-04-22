---
title: Szybki start — tworzenie zadania usługi Azure Stream Analytics w programie Visual Studio Code
description: Ten przewodnik Szybki start pokazuje, jak rozpocząć, tworząc zadanie usługi Stream Analytics, konfigurując dane wejściowe i wyjściowe oraz definiując kwerendę za pomocą programu Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 01/18/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: fe666359b9ed2773f615ff496f4032bda5e74dc2
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767770"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Szybki start: tworzenie zadania usługi Azure Stream Analytics w programie Visual Studio Code (wersja zapoznawcza)

Ten przewodnik Szybki start pokazuje, jak utworzyć i uruchomić zadanie usługi Azure Stream Analytics przy użyciu rozszerzenia Narzędzia analizy usługi Azure Stream Analytics dla kodu programu Visual Studio. Przykładowe zadanie odczytuje dane przesyłania strumieniowego z urządzenia usługi Azure IoT Hub. Zdefiniujesz zadanie obliczające średnią temperaturę, gdy wynosi ona ponad 27°, i zapisujące wynikowe zdarzenia wyjściowe w magazynie obiektów blob.

> [!NOTE]
> Narzędzia Programu Visual Studio i Visual Studio Code nie obsługują zadań w regionach Chiny Wschód, Chiny Północne, Niemcy Środkowe i Niemcy NorthEast.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

* Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>Instalowanie rozszerzenia narzędzi azure stream analytics

1. Otwórz program Visual Studio Code.

2. W **rozszerzeń** w lewym okienku wyszukaj **usługę Stream Analytics** i wybierz pozycję **Zainstaluj** w rozszerzeniu Narzędzia azure **stream analytics.**

3. Po zainstalowaniu rozszerzenia sprawdź, czy **narzędzia Azure Stream Analytics Tools** są widoczne w **opcjach Włączone rozszerzenia**.

   ![Narzędzia usługi Azure Stream Analytics Tools w obszarze włączonych rozszerzeń w programie Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>Aktywowanie rozszerzenia narzędzi azure stream analytics

1. Wybierz ikonę **platformy Azure** na pasku aktywności Kod programu Visual Studio. W obszarze **Analiza strumienia** na pasku bocznym wybierz pozycję **Zaloguj się na platformie Azure**.

   ![Logowanie się do platformy Azure w programie Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. Po zalogowaniu się nazwa konta platformy Azure pojawia się na pasku stanu w lewym dolnym rogu okna kod programu Visual Studio.

> [!NOTE]
> Rozszerzenie narzędzia Azure Stream Analytics Tools automatycznie podpisze Cię następnym razem, jeśli nie wylogujesz się. Jeśli twoje konto ma uwierzytelnianie dwuskładnikowe, zalecamy użycie uwierzytelniania telefonicznego zamiast numeru PIN.
> Jeśli masz problemy z wyświetlaniem zasobów aukcji, wylogowanie się i ponowne zalogowanie zwykle pomaga. Aby się wylogować, wprowadź polecenie `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>Przygotowywanie danych wejściowych

Przed zdefiniowaniem zadania usługi Stream Analytics należy przygotować dane, które są później skonfigurowane jako dane wejściowe zadania. Aby przygotować dane wejściowe, których wymaga zadanie, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz **pozycję Utwórz zasób** > Centrum**IoT Hub**internetu**rzeczy** > .

3. W okienku **IoT Hub** wprowadź następujące informacje:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Subskrypcja  | \<Twoja subskrypcja\> |  Wybierz subskrypcję platformy Azure, której chcesz użyć. |
   |Grupa zasobów   |   asaquickstart-resourcegroup  |   Wybierz pozycję **Utwórz nową** i wprowadź nazwę nowej grupy zasobów dla swojego konta. |
   |Region  |  \<Wybierz region, który jest najbliżej Twoich użytkowników\> | Wybierz lokalizację geograficzną, w której możesz hostować centrum IoT Hub. Użyj lokalizacji znajdującej się najbliżej użytkowników. |
   |Nazwa centrum IoT Hub  | MyASAIoTHub  |   Wybierz nazwę centrum IoT Hub.   |

   ![Tworzenie centrum IoT Hub](./media/quick-create-vs-code/create-iot-hub.png)

4. Wybierz pozycję **Dalej: Ustaw rozmiar i skalę**.

5. Dokonaj wyboru **warstwy Cennik i skala**. Na potrzeby tego przewodnika Szybki start wybierz warstwę **F1 — Bezpłatna**, jeśli jest ona nadal dostępna w ramach subskrypcji. Jeśli warstwa bezpłatna jest niedostępna, użyj najniższej dostępnej warstwy. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Rozmiar i skalowanie koncentratora IoT](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Wybierz pozycję **Przegląd + utwórz**. Przejrzyj informacje o centrum IoT i wybierz pozycję **Utwórz**. Proces tworzenia centrum IoT Hub może potrwać kilka minut. Postęp w okienku **Powiadomienia** można monitorować.

7. W menu nawigacyjnym centrum IoT wybierz polecenie **Dodaj** w obszarze **Urządzenia IoT**. Dodaj identyfikator **identyfikatora urządzenia**i wybierz pozycję **Zapisz**.

   ![Dodawanie urządzenia do centrum IoT](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Po utworzeniu urządzenia otwórz urządzenie z listy **urządzeń IoT.** Skopiuj ciąg w **ciągu połączenia (klucz podstawowy)** i zapisz go w notatniku, aby użyć go później.

   ![Kopiowanie ciągu połączenia urządzenia koncentratora IoT](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>Uruchamianie symulatora IoT

1. Otwórz symulator [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) w nowej karcie lub nowym oknie przeglądarki.

2. Zastąp symbol zastępczy w wierszu 15 ciągiem połączenia urządzenia ioT, który został zapisany wcześniej.

3. Wybierz pozycję **Uruchom**. Dane wyjściowe powinny wyświetlać dane czujnika i komunikaty, które są wysyłane do centrum IoT Hub.

   ![Raspberry Pi Azure IoT Online Simulator z wyjściem](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Tworzenie magazynu obiektów blob

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz** > **konto magazynu****magazynu** > zasobów .

2. W okienku **Utwórz konto magazynu** wprowadź nazwę konta magazynu, lokalizację i grupę zasobów. Wybierz tę samą lokalizację i grupę zasobów co utworzony węzeł IoT Hub. Następnie wybierz **pozycję Przejrzyj + utwórz,** aby utworzyć konto.

   ![Tworzenie konta magazynu](./media/quick-create-vs-code/create-storage-account.png)

3. Po utworzeniu konta magazynu wybierz kafelek **obiektów blob** w okienku **Przegląd.**

   ![Omówienie kont magazynu](./media/quick-create-vs-code/blob-storage.png)

4. Na stronie **Blob Service** wybierz pozycję **Kontener** i podaj nazwę swojego kontenera, na przykład **container1**. Pozostaw **poziom dostępu publicznego** jako **prywatny (bez dostępu anonimowego)** i wybierz **przycisk OK**.

   ![Tworzenie kontenera obiektów blob](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Tworzenie projektu usługi Stream Analytics

1. W programie Visual Studio Code wybierz **klawisze Ctrl+Shift+P,** aby otworzyć paletę poleceń. Następnie wprowadź **ASA** i wybierz **ASA: Utwórz nowy projekt**.

   ![Tworzenie nowego projektu](./media/quick-create-vs-code/create-new-project.png)

2. Wprowadź nazwę projektu, na przykład **myASAproj,** i wybierz folder dla projektu.

    ![Tworzenie nazwy projektu](./media/quick-create-vs-code/create-project-name.png)

3. Nowy projekt zostanie dodany do obszaru roboczego. Projekt usługi Stream Analytics składa się z trzech folderów: **Wejścia,** **Wyjścia**i **Funkcje**. Posiada również skrypt kwerendy **(*.asaql),** plik **JobConfig.json** i plik konfiguracyjny **asaproj.json.**

    Plik konfiguracyjny **asaproj.json** zawiera dane wejściowe, dane wyjściowe i informacje o pliku konfiguracji zadania potrzebne do przesłania zadania usługi Stream Analytics na platformie Azure.

    ![Pliki projektu usługi Stream Analytics w programie Visual Studio Code](./media/quick-create-vs-code/asa-project-files.png)

> [!Note]
> Podczas dodawania danych wejściowych i wyjściowych z palety poleceń odpowiednie ścieżki są automatycznie dodawane do **asaproj.json.** Jeśli dodasz lub usuniesz dane wejściowe lub wyjściowe na dysku bezpośrednio, musisz ręcznie dodać lub usunąć je z **pliku asaproj.json**. Można umieścić dane wejściowe i wyjściowe w jednym miejscu, a następnie odwoływać się do nich w różnych zadaniach, określając ścieżki w każdym pliku **asaproj.json.**

## <a name="define-the-transformation-query"></a>Definiowanie zapytania przekształcenia

1. Otwórz **plik myASAproj.asaql** z folderu projektu.

2. Dodaj następujące zapytanie:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="define-a-live-input"></a>Definiowanie danych wejściowych na żywo

1. Kliknij prawym przyciskiem myszy folder **Inputs** w projekcie usługi Stream Analytics. Następnie wybierz **ASA: Dodaj dane wejściowe** z menu kontekstowego.

    ![Dodawanie danych wejściowych z folderu Wejścia](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

    Możesz też wybrać **klawisze Ctrl+Shift+P,** aby otworzyć paletę poleceń i wprowadzić **asa: Dodaj wejście**.

   ![Dodawanie danych wejściowych usługi Stream Analytics w programie Visual Studio Code](./media/quick-create-vs-code/add-input.png)

2. Wybierz centrum **IoT dla** typu wejściowego.

   ![Wybierz koncentrator IoT jako opcję wprowadzania](./media/quick-create-vs-code/iot-hub.png)

3. Jeśli dodano dane wejściowe z palety poleceń, wybierz skrypt zapytania usługi Stream Analytics, który będzie używał danych wejściowych. Powinien być automatycznie wypełniany ścieżką pliku do **myASAproj.asaql**.

   ![Wybieranie skryptu usługi Stream Analytics w programie Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Z menu rozwijanego wybierz pozycję **Wybierz subskrypcje platformy Azure.**

    ![Wybierz z subskrypcji](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Edytuj nowo wygenerowany plik **IoTHub1.json** z następującymi wartościami. Zachowaj wartości domyślne dla pól, które nie zostały wymienione w tym miejscu.

   |Ustawienie|Sugerowana wartość|Opis|
   |-------|---------------|-----------|
   |Nazwa|Dane wejściowe|Wprowadź nazwę, aby zidentyfikować dane wejściowe zadania.|
   |Obszar IotHubNamespace|MyASAIoTHub|Wybierz lub wprowadź nazwę centrum IoT Hub. Nazwy centrum IoT są automatycznie wykrywane, jeśli są tworzone w tej samej subskrypcji.|
   |SharedAccessPolicyName|iothubowner| |

   Funkcja CodeLens ułatwia wprowadzanie ciągu, wybieranie z listy rozwijanej lub zmianę tekstu bezpośrednio w pliku. Poniższy zrzut ekranu przedstawia **Wybierz z subskrypcji** jako przykład. Poświadczenia są automatycznie wyświetlane i zapisywane w lokalnym menedżerze poświadczeń.

   ![Konfigurowanie danych wejściowych w programie Visual Studio Code](./media/quick-create-vs-code/configure-input.png)

   ![Konfigurowanie wartości wejściowej w programie Visual Studio Code](./media/quick-create-vs-code/configure-input-value.png)

## <a name="preview-input"></a>Podgląd danych wejściowych

Wybierz **opcję Podgląd danych** w pliku **IoTHub1.json** z górnej linii. Niektóre dane wejściowe zostaną pobrane z centrum IoT i pokazane w oknie podglądu. Ten proces może trochę potrwać.

 ![Podgląd danych wejściowych na żywo](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-an-output"></a>Definiowanie danych wyjściowych

1. Wybierz **klawisze Ctrl+Shift+P,** aby otworzyć paletę poleceń. Następnie wprowadź **ASA: Dodaj wyjście**.

   ![Dodawanie danych wyjściowych usługi Stream Analytics w programie Visual Studio Code](./media/quick-create-vs-code/add-output.png)

2. Wybierz **magazyn obiektów blob** dla typu ujścia.

3. Wybierz skrypt zapytania usługi Stream Analytics, który będzie używał tego danych wejściowych.

4. Wprowadź nazwę pliku wyjściowego jako **BlobStorage**.

5. Edytuj **BlobStorage** przy użyciu następujących wartości. Zachowaj wartości domyślne dla pól, które nie zostały wymienione w tym miejscu. Funkcja CodeLens ułatwia wybieranie z listy rozwijanej lub wprowadzanie ciągu.

   |Ustawienie|Sugerowana wartość|Opis|
   |-------|---------------|-----------|
   |Nazwa|Dane wyjściowe| Wprowadź nazwę, aby zidentyfikować dane wyjściowe zadania.|
   |Konto magazynu|asaquickstartstorage|Wybierz lub wprowadź nazwę swojego konta magazynu. Nazwy kont magazynu są automatycznie wykrywane, jeśli są tworzone w tej samej subskrypcji.|
   |Kontener|container1|Wybierz istniejący kontener utworzony na koncie magazynu.|
   |Wzorzec ścieżki|output|Wprowadź nazwę ścieżki pliku, który ma zostać utworzony w kontenerze.|

   ![Konfigurowanie danych wyjściowych w programie Visual Studio Code](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Kompilowanie skryptu

Kompilacja skryptu sprawdza składnię i generuje szablony usługi Azure Resource Manager do automatycznego wdrażania.

Istnieją dwa sposoby wyzwalania kompilacji skryptów:

- Wybierz skrypt z obszaru roboczego, a następnie skompiluj z palety poleceń.

   ![Użyj palety poleceń kodu programu Visual Studio do skompilowania skryptu](./media/quick-create-vs-code/compile-script1.png)

- Kliknij prawym przyciskiem myszy skrypt i wybierz polecenie **ASA: Compile Script**.

    ![Kliknij prawym przyciskiem myszy skrypt usługi Stream Analytics w celu skompilowania](./media/quick-create-vs-code/compile-script2.png)

Po kompilacji można znaleźć dwa wygenerowane szablony usługi Azure Resource Manager w folderze **Wdrażanie** projektu. Te dwa pliki są używane do automatycznego wdrażania.

![Szablony wdrażania usługi Stream Analytics w Eksploratorze plików](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Przesyłanie zadania usługi Stream Analytics do platformy Azure

1. W oknie edytora skryptów skryptu kwerendy wybierz pozycję **Prześlij na platformę Azure**.

   ![Wybierz z tekstu subskrypcji w edytorze skryptów](./media/quick-create-vs-code/submit-job.png)

2. Wybierz subskrypcję z listy podręcznej.

3. Wybierz **pozycję Wybierz zadanie**. Następnie wybierz pozycję **Utwórz nowe zadanie**.

4. Wpisz nazwę swojej pracy, **myASAjob**. Następnie postępuj zgodnie z instrukcjami, aby wybrać grupę zasobów i lokalizację.

5. Wybierz **pozycję Prześlij na platformę Azure**. Dzienniki można znaleźć w oknie danych wyjściowych. 

6. Po utworzeniu zadania można je wyświetlić w **Eksploratorze analizy strumieniowej**.

    ![Wymienione zadanie w Eksploratorze analizy strumienia](./media/quick-create-vs-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych

1. Otwórz **Eksploratora analizy strumienia** w programie Visual Studio Code i znajdź swoją **pracę, myASAJob**.

2. Kliknij prawym przyciskiem myszy nazwę zadania. Następnie wybierz przycisk **Start** z menu kontekstowego.

   ![Uruchamianie zadania usługi Stream Analytics w programie Visual Studio Code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Wybierz **pozycję Teraz** w wyskakującym oknie, aby rozpocząć zadanie.

4. Należy zauważyć, że stan zadania został zmieniony na **Uruchomiony**. Kliknij prawym przyciskiem myszy nazwę zadania i wybierz pozycję **Otwórz widok zadania w portalu,** aby wyświetlić metryki zdarzeń wejściowych i wyjściowych. Ta akcja może potrwać kilka minut.

5. Aby wyświetlić wyniki, otwórz magazyn obiektów blob w rozszerzeniu kodu programu Visual Studio lub w witrynie Azure portal.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, usuń grupę zasobów, zadanie przesyłania strumieniowego i wszystkie powiązane zasoby. Usunięcie zadania pozwala uniknąć rozliczeń jednostek przesyłania strumieniowego, które zużywa zadanie. 

Jeśli planujesz używać zadania w przyszłości, możesz je zatrzymać i ponownie uruchomić później. Jeśli nie zamierzasz ponownie używać tego zadania, użyj następujących kroków, aby usunąć wszystkie zasoby utworzone w tym przewodniku Szybki start:

1. Z lewego menu w witrynie Azure portal wybierz **grup zasobów,** a następnie wybierz nazwę utworzonego zasobu.  

2. Na stronie grupy zasobów wybierz pozycję **Usuń**. Wprowadź nazwę zasobu do usunięcia w polu tekstowym, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono proste zadanie usługi Stream Analytics przy użyciu programu Visual Studio Code. Można również wdrożyć zadania usługi Stream Analytics przy użyciu [witryny Azure Portal](stream-analytics-quick-create-portal.md), [PowerShell](stream-analytics-quick-create-powershell.md)i [Visual Studio](stream-analytics-quick-create-vs.md).

Aby dowiedzieć się więcej o narzędziach Azure Stream Analytics Tools for Visual Studio Code, przejdź do następujących artykułów:

* [Testowanie zapytań usługi Stream Analytics lokalnie z przykładowymi danymi przy użyciu kodu programu Visual Studio](visual-studio-code-local-run.md)

* [Testowanie zadań usługi Azure Stream Analytics lokalnie względem danych wejściowych na żywo za pomocą kodu programu Visual Studio](visual-studio-code-local-run-live-input.md)

* [Wyświetlanie zadań usługi Azure Stream Analytics za pomocą kodu programu Visual Studio](visual-studio-code-explore-jobs.md)

* [Konfigurowanie potoków ciągłej integracji/ciągłego wdrażania przy użyciu pakietu npm](setup-cicd-vs-code.md)
