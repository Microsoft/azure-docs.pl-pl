---
title: Moduł IoT Edge języka C# dla Azure Stack Edge Pro z procesorem GPU | Microsoft Docs
description: Dowiedz się, jak opracowywać IoT Edge języka C#, który można wdrożyć na urządzeniu Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 1aab6fa7a2ea659b489a2e65e2a6a79070edc6b3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873769"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-azure-stack-edge-pro"></a>Opracowywanie modułu języka C# IoT Edge przenoszenia plików na Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób tworzenia modułu IoT Edge wdrożenia za pomocą Azure Stack Edge Pro urządzenia. Azure Stack Edge Pro to rozwiązanie magazynu, które umożliwia przetwarzanie danych i wysyłanie ich za pośrednictwem sieci na platformę Azure.

Możesz użyć modułów Azure IoT Edge z Azure Stack Edge Pro, aby przekształcić dane przenoszone na platformę Azure. Moduł używany w tym artykule implementuje logikę kopiowania pliku z udziału lokalnego do udziału w chmurze Azure Stack Edge Pro urządzeniu.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz rejestr kontenerów do przechowywania modułów i zarządzania nimi (obrazy platformy Docker).
> * Utwórz moduł IoT Edge do wdrożenia na Azure Stack Edge Pro urządzeniu.


## <a name="about-the-iot-edge-module"></a>Informacje o IoT Edge modułu

Twoje Azure Stack Edge Pro może wdrażać i uruchamiać IoT Edge modułów. Moduły brzegowe to zasadniczo kontenery platformy Docker, które wykonują określone zadanie, takie jak pozyskowanie komunikatu z urządzenia, przekształcenie komunikatu lub wysłanie komunikatu do IoT Hub. W tym artykule utworzysz moduł, który kopiuje pliki z udziału lokalnego do udziału w chmurze na Azure Stack Edge Pro urządzeniu.

1. Pliki są zapisywane w lokalnym udziałze na Azure Stack Edge Pro urządzeniu.
2. Generator zdarzeń pliku tworzy zdarzenie pliku dla każdego pliku zapisanego w lokalnym udziału. Zdarzenia pliku są również generowane podczas modyfikacji pliku. Zdarzenia plików są następnie wysyłane do usługi IoT Edge Hub (w IoT Edge uruchomieniowym).
3. Moduł IoT Edge przetwarza zdarzenie pliku, aby utworzyć obiekt zdarzenia pliku, który również zawiera ścieżkę względną dla pliku. Moduł generuje ścieżkę bezwzględną przy użyciu względnej ścieżki pliku i kopiuje plik z udziału lokalnego do udziału w chmurze. Następnie moduł usuwa plik z udziału lokalnego.

![Jak Azure IoT Edge działa na Azure Stack Edge Pro](./media/azure-stack-edge-gpu-create-iot-edge-module/how-module-works-1.png)

Gdy plik znajduje się w udziałach w chmurze, jest automatycznie przekazywany na konto usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- Urządzenie Azure Stack Edge Pro uruchomione.

    - Urządzenie ma również skojarzony zasób IoT Hub zasobów.
    - Urządzenie ma skonfigurowaną rolę obliczeniową usługi Edge.
    Aby uzyskać więcej informacji, przejdź do [tematu Configure compute](azure-stack-edge-j-series-deploy-configure-compute.md#configure-compute) for your Azure Stack Edge Pro (Konfigurowanie zasobów obliczeniowych dla Azure Stack Edge Pro).<!--Update link?-->

- Następujące zasoby programowe:

    - [Program Visual Studio Code](https://code.visualstudio.com/)
    - [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
    - [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
    - [Zestaw .NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).
    - [Program Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Może być trzeba utworzyć konto, aby pobrać i zainstalować oprogramowanie.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Usługa Azure Container Registry to rejestr prywatny platformy Docker na platformie Azure, w którym można przechowywać prywatne obrazy kontenerów Docker i zarządzać nimi. Dwie popularne usługi rejestru platformy Docker dostępne w chmurze to Azure Container Registry i Docker Hub. W tym artykule użyto Container Registry.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Wybierz **pozycję Utwórz zasób > Kontenery > Container Registry**. Kliknij pozycję **Utwórz**.
3. Zapewnić:

   1. Unikatowa **nazwa rejestru na** platformie Azure, która zawiera od 5 do 50 znaków alfanumerycznych.
   2. Wybierz **subskrypcję**.
   3. Utwórz nową lub wybierz istniejącą **grupę zasobów.**
   4. Wybierz **lokalizację**. Zalecamy, aby ta lokalizacja była taka sama jak lokalizacja skojarzona z Azure Stack Edge zasobem.
   5. Przełącz pozycję **Administrator** na wartość **Włącz**.
   6. Ustaw dla wersji SKU wartość **Podstawowa.**

      ![Tworzenie rejestru kontenerów](./media/azure-stack-edge-gpu-create-iot-edge-module/create-container-registry-1.png)
 
4. Wybierz przycisk **Utwórz**.
5. Po utworzeniu rejestru kontenerów przejdź do niego i wybierz pozycję **Klucze dostępu**.

    ![Uzyskiwanie kluczy dostępu](./media/azure-stack-edge-gpu-create-iot-edge-module/get-access-keys-1.png)
 
6. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Te wartości będą później używane do publikowania obrazu platformy Docker w rejestrze i dodawania poświadczeń rejestru do Azure IoT Edge uruchomieniowego.


## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu usługi IoT Edge

Poniższe kroki tworzą projekt IoT Edge na podstawie zestawu SDK platformy .NET Core 2.1. Projekt używa Visual Studio Code i Azure IoT Edge rozszerzenia.

### <a name="create-a-new-solution"></a>Tworzenie nowego rozwiązania

Utwórz szablon rozwiązania w języku C#, który można dostosować przy użyciu własnego kodu.

1. W Visual Studio Code wybierz pozycję View > Command Palette (Wyświetl **paletę** poleceń), aby VS Code paletę poleceń.
2. W palecie poleceń wprowadź i uruchom polecenie **Azure: zaloguj się**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.
3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie:

    1. Wybierz folder, w którym chcesz utworzyć rozwiązanie.
    2. Podaj nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**.
    
        ![Tworzenie nowego rozwiązania 1](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-1.png)

    3. Wybierz **pozycję Moduł C#** jako szablon modułu.
    4. Zastąp domyślną nazwę modułu nazwą, którą chcesz przypisać. W tym przypadku jest to **FileCopyModule.**
    
        ![Tworzenie nowego rozwiązania 2](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-2.png)

    5. Określ rejestr kontenerów utworzony w poprzedniej sekcji jako repozytorium obrazów dla pierwszego modułu. Zastąp ciąg **localhost:5000** skopiowaną wartością serwera logowania.

        Ostateczny ciąg wygląda `<Login server name>/<Module name>` następująco: . W tym przykładzie ciąg to : `mycontreg2.azurecr.io/filecopymodule` .

        ![Tworzenie nowego rozwiązania 3](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-3.png)

4. Przejdź do **folderu > Otwórz folder.**

    ![Tworzenie nowego rozwiązania 4](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-4.png)

5. Przejdź do utworzonego wcześniej folderu **EdgeSolution** i wskaż go. Okno VS Code ładuje obszar roboczy IoT Edge rozwiązania z pięcioma składnikami najwyższego poziomu. Nie będziesz edytować folderu, pliku `.vscode` **gitignore,** pliku **env** ani pliku deployment.template.js** w tym artykule.
    
    Jedynym modyfikowanym składnikiem jest folder modules. Ten folder zawiera kod języka C# dla modułu i pliki platformy Docker służące do kompilowania modułu jako obrazu kontenera.

    ![Tworzenie nowego rozwiązania 5](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W eksploratorze VS Code otwórz **moduły, > FileCopyModule > Program.cs.**
2. W górnej części przestrzeni **nazw FileCopyModule** dodaj następujące instrukcje using dla typów, które są używane później. **Microsoft.Azure.Devices.Client.Transport.Mqtt** to protokół do wysyłania komunikatów do IoT Edge Hub.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Dodaj zmienne **InputFolderPath** **i OutputFolderPath** do klasy Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Bezpośrednio po poprzednim kroku dodaj klasę **FileEvent,** aby zdefiniować treść komunikatu.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. W metodzie **Init** kod tworzy i konfiguruje obiekt **ModuleClient.** Ten obiekt umożliwia modułowi łączenie się z lokalnym środowiskiem uruchomieniowym Azure IoT Edge przy użyciu protokołu MQTT w celu wysyłania i odbierania komunikatów. Parametry połączenia używane w metodzie Init są dostarczane do modułu za pośrednictwem środowiska uruchomieniowego usługi IoT Edge. Kod rejestruje wywołanie zwrotne FileCopy w celu odbierania komunikatów z centrum IoT Edge za pośrednictwem punktu **końcowego input1.** Zastąp **metodę Init** poniższym kodem.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Usuń kod metody **PipeMessage,** a następnie wstaw kod dla metody **FileCopy**.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub.
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Zapisz ten plik.
8. Możesz również [pobrać istniejący przykładowy kod](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) dla tego projektu. Następnie możesz zweryfikować plik, który został zapisany względem pliku **program.cs** w tym przykładzie.

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod do fileCopyModule w celu kopiowania plików z udziału lokalnego do udziału w chmurze. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. W programie VSCode przejdź do > Terminal, aby otworzyć nowy Visual Studio Code zintegrowany terminal.
2. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu.

    `docker login <ACR login server> -u <ACR username>`

    Użyj serwera logowania i nazwy użytkownika skopiowanych z rejestru kontenerów.

    ![Kompilowanie i wypychanie IoT Edge rozwiązania](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Po wyświetleniu monitu o hasło należy podać hasło. Wartości serwera logowania, nazwy użytkownika i hasła można  również pobrać z kluczy dostępu w rejestrze kontenerów w Azure Portal.
 
3. Po poświadczeniu możesz wypchnąć obraz modułu do rejestru kontenerów platformy Azure. W Eksploratorze VS Code kliknij prawym przyciskiem myszy plik **module.js** a następnie wybierz polecenie **Skompilowanie** i wypychanie IoT Edge rozwiązania .

    ![Kompilowanie i wypychanie IoT Edge 2](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Po skompilowania Visual Studio Code uruchamia dwa polecenia w zintegrowanym terminalu: docker build i docker push. Te dwa polecenia kompilują kod, konteneryzują plik CSharpModule.dll i wypychają go do rejestru kontenerów określonego podczas inicjowania rozwiązania.

    Zostanie wyświetlony monit o wybranie platformy modułu. Wybierz *pozycję amd64 odpowiadającą* systemowi Linux.

    ![Wybieranie platformy](./media/azure-stack-edge-gpu-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Obsługiwane są tylko moduły systemu Linux.

    Może zostać wyświetlony następujące ostrzeżenie, które można zignorować:

    *Program.cs(77,44): ostrzeżenie CS1998: Ta metoda asynchroniczna nie ma operatorów "await" i będzie uruchamiana synchronicznie. Rozważ użycie operatora "await", aby poczekać na nieblokowe wywołania interfejsu API, lub "await Task.Run(...)", aby wykonać pracę związaną z procesorem CPU w wątku w tle.*

4. Pełny adres obrazu kontenera możesz wyświetlić za pomocą tagu w zintegrowanym terminalu programu VS Code. Adres obrazu jest zbudowany na podstawie informacji, które są w module.jspliku w formacie `<repository>:<version>-<platform>` . W przypadku tego artykułu powinien on wyglądać jak `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64` .

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć i uruchomić ten moduł na Azure Stack Edge Pro, zobacz kroki opisane w tece [Dodawanie modułu.](azure-stack-edge-j-series-deploy-configure-compute.md#add-a-module)<!--Update link?-->
