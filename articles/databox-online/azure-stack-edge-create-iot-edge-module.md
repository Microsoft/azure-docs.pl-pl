---
title: Moduł IoT Edge C# dla Azure Stack EDGE Pro | Microsoft Docs
description: Dowiedz się, jak opracowywać moduł IoT Edge w języku C#, który można wdrożyć na Azure Stack EDGE Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/06/2019
ms.author: alkohli
ms.custom: devx-track-csharp
ms.openlocfilehash: 96a6692524eca3a2845d648ab3df2932d00ce823
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951149"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-with-azure-stack-edge-pro"></a>Opracowywanie modułu IoT Edge C# w celu przenoszenia plików przy użyciu Azure Stack EDGE Pro

W tym artykule opisano sposób tworzenia modułu IoT Edge na potrzeby wdrożenia przy użyciu urządzenia z Azure Stack EDGE Pro. Azure Stack EDGE Pro to rozwiązanie magazynu, które pozwala na przetwarzanie danych i wysyłanie ich przez sieć do platformy Azure.

Za pomocą modułów Azure IoT Edge za pomocą Azure Stack Edge w firmie Pro można przekształcić dane w miarę ich przenoszenia na platformę Azure. Moduł używany w tym artykule implementuje logikę kopiowania pliku z udziału lokalnego do udziału w chmurze na urządzeniu Azure Stack EDGE Pro.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Utwórz rejestr kontenerów w celu przechowywania modułów (obrazów platformy Docker) i zarządzania nimi.
> * Utwórz moduł IoT Edge, który ma zostać wdrożony na urządzeniu z systemem Azure Stack EDGE Pro. 


## <a name="about-the-iot-edge-module"></a>Informacje o module IoT Edge

Twoje urządzenie Azure Stack EDGE Pro może wdrażać i uruchamiać moduły IoT Edge. Moduły brzegowe są zasadniczo kontenerami Docker, które wykonują określone zadanie, takie jak pozyskiwanie komunikatów z urządzenia, przekształcanie komunikatu lub wysyłanie komunikatu do IoT Hub. W tym artykule opisano tworzenie modułu, który kopiuje pliki z udziału lokalnego do udziału w chmurze na urządzeniu Azure Stack EDGE Pro.

1. Pliki są zapisywane w udziale lokalnym na urządzeniu z Azure Stack Edge.
2. Generator zdarzeń pliku tworzy zdarzenie pliku dla każdego pliku zapisywanego w udziale lokalnym. Zdarzenia plików są również generowane, gdy plik zostanie zmodyfikowany. Zdarzenia plików są następnie wysyłane do centrum IoT Edge (w IoT Edge Runtime).
3. Moduł niestandardowy IoT Edge przetwarza zdarzenie pliku, aby utworzyć obiekt zdarzenia pliku, który zawiera również ścieżkę względną dla pliku. Moduł generuje ścieżkę bezwzględną przy użyciu względnej ścieżki pliku i kopiuje plik z udziału lokalnego do udziału w chmurze. Następnie moduł usunie plik z udziału lokalnego.

![Jak działa moduł Azure IoT Edge na Azure Stack EDGE Pro](./media/azure-stack-edge-create-iot-edge-module/how-module-works-1.png)

Gdy plik znajduje się w udziale w chmurze, zostanie automatycznie przekazany do konta usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- Urządzenie, na którym działa Azure Stack Edge.

    - Urządzenie ma także skojarzony zasób IoT Hub.
    - Na urządzeniu skonfigurowano rolę obliczeń brzegowych.
    Aby uzyskać więcej informacji, przejdź do pozycji [Konfigurowanie obliczeń](azure-stack-edge-deploy-configure-compute.md#configure-compute) dla Azure Stack Edge.

- Następujące zasoby programistyczne:

    - [Program Visual Studio Code](https://code.visualstudio.com/)
    - [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
    - [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
    - [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Program Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Może być konieczne utworzenie konta w celu pobrania i zainstalowania oprogramowania.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Usługa Azure Container Registry to rejestr prywatny platformy Docker na platformie Azure, w którym można przechowywać prywatne obrazy kontenerów Docker i zarządzać nimi. Dwie popularne usługi rejestru platformy Docker dostępne w chmurze to Azure Container Registry i Docker Hub. W tym artykule jest wykorzystywany Container Registry.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Wybierz pozycję **Utwórz zasób > kontenery > Container Registry**. Kliknij pozycję **Utwórz**.
3. Oferować

   1. Unikatowa **Nazwa rejestru** na platformie Azure, która zawiera od 5 do 50 znaków alfanumerycznych.
   2. Wybierz **subskrypcję**.
   3. Utwórz nową lub wybierz istniejącą **grupę zasobów**.
   4. Wybierz **lokalizację**. Firma Microsoft zaleca, aby ta lokalizacja była taka sama, jak skojarzona z Azure Stack brzegową zasobów.
   5. Przełącz pozycję **Administrator** na wartość **Włącz**.
   6. Ustaw jednostkę SKU na **podstawową**.

      ![Tworzenie rejestru kontenerów](./media/azure-stack-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Wybierz pozycję **Utwórz**.
5. Po utworzeniu rejestru kontenerów przejdź do niego i wybierz pozycję **Klucze dostępu**.

    ![Pobierz klucze dostępu](./media/azure-stack-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Te wartości są używane później do publikowania obrazu platformy Docker w rejestrze i dodawania poświadczeń rejestru do środowiska uruchomieniowego Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu usługi IoT Edge

Poniższe kroki tworzą projekt modułu IoT Edge na podstawie zestawu .NET Core 2,1 SDK. Projekt używa Visual Studio Code i Azure IoT Edge rozszerzenia.

### <a name="create-a-new-solution"></a>Tworzenie nowego rozwiązania

Utwórz szablon rozwiązania w języku C#, który można dostosować przy użyciu własnego kodu.

1. W Visual Studio Code wybierz pozycję **wyświetl > paletę poleceń** , aby otworzyć paletę poleceń vs Code.
2. W palecie poleceń wprowadź i uruchom polecenie **Azure: zaloguj się**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.
3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie:

    1. Wybierz folder, w którym chcesz utworzyć rozwiązanie.
    2. Podaj nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**.
    
        ![Utwórz nowe rozwiązanie 1](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Wybierz **moduł C#** jako szablon modułu.
    4. Zastąp domyślną nazwę modułu nazwą, którą chcesz przypisać, w tym przypadku jest to **FileCopyModule**.
    
        ![Utwórz nowe rozwiązanie 2](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Określ rejestr kontenerów utworzony w poprzedniej sekcji jako repozytorium obrazów dla pierwszego modułu. Zastąp ciąg **localhost:5000** skopiowaną wartością serwera logowania.

        Końcowy ciąg wygląda następująco `<Login server name>/<Module name>` . W tym przykładzie ciąg: `mycontreg2.azurecr.io/filecopymodule` .

        ![Utwórz nowe rozwiązanie 3](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-3.png)

4. Przejdź do **pliku > Otwórz folder**.

    ![Utwórz nowe rozwiązanie 4](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-4.png)

5. Przeglądaj i wskaż utworzony wcześniej folder **EdgeSolution** . Okno VS Code ładuje obszar roboczy rozwiązania IoT Edge z pięcioma składnikami najwyższego poziomu. Nie będziesz edytować folderu **. programu vscode** , pliku **GITIGNORE** , pliku **ENV** ani **deployment.template.js** w tym artykule.
    
    Jedynym modyfikowanym składnikiem jest folder moduły. Ten folder zawiera kod C# dla modułu i plików platformy Docker, aby skompilować moduł jako obraz kontenera.

    ![Utwórz nowe rozwiązanie 5](./media/azure-stack-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W Eksploratorze VS Code Otwórz **moduł > FileCopyModule > program.cs**.
2. W górnej części **przestrzeni nazw FileCopyModule**Dodaj następujące instrukcje using dla typów, które są używane później. **Microsoft. Azure. Devices. Client. transport. MQTT** to protokół służący do wysyłania komunikatów do centrum IoT Edge.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Dodaj zmienną **InputFolderPath** i **OutputFolderPath** do klasy program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Bezpośrednio po poprzednim kroku Dodaj klasę **FileEvent** , aby zdefiniować treść wiadomości.

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

5. W **metodzie init**kod tworzy i konfiguruje obiekt **ModuleClient** . Ten obiekt umożliwia modułowi łączenie się z lokalnymi Azure IoT Edge środowiska uruchomieniowego przy użyciu protokołu MQTT w celu wysyłania i odbierania komunikatów. Parametry połączenia używane w metodzie Init są dostarczane do modułu za pośrednictwem środowiska uruchomieniowego usługi IoT Edge. Kod rejestruje wywołanie zwrotne operacja FileCopy, aby odbierać komunikaty z Centrum IoT Edge za pośrednictwem punktu końcowego **INPUT1** . Zastąp **metodę init** poniższym kodem.

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

6. Usuń kod dla **metody PipeMessage** i w jego miejscu Wstaw kod dla **operacja FileCopy**.

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
8. Możesz również [pobrać istniejący przykład kodu](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) dla tego projektu. Następnie można sprawdzić poprawność pliku zapisanego w pliku **program.cs** w tym przykładzie.

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod do FileCopyModule w celu skopiowania plików z udziału lokalnego do udziału w chmurze. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. W programu vscode przejdź do pozycji Terminal > nowy terminal, aby otworzyć nowy Visual Studio Code zintegrowany terminal.
2. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu.

    `docker login <ACR login server> -u <ACR username>`

    Użyj serwera logowania i nazwy użytkownika skopiowanej z rejestru kontenerów.

    ![Rozwiązanie do kompilowania i wypychania IoT Edge](./media/azure-stack-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Po wyświetleniu monitu o podanie hasła Podaj hasło. Możesz również pobrać wartości dla opcji serwer logowania, nazwa użytkownika i hasło z **kluczy dostępu** w rejestrze kontenerów w Azure Portal.
 
3. Po dostarczeniu poświadczeń można wypchnąć obraz modułu do usługi Azure Container Registry. W Eksploratorze VS Code kliknij prawym przyciskiem myszy **module.jsna** pliku i wybierz polecenie **Kompiluj i wypchnij IoT Edge rozwiązanie**.

    ![Kompiluj i wypchnij IoT Edge rozwiązanie 2](./media/azure-stack-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Po poinformowaniu Visual Studio Code o skompilowaniu rozwiązania są uruchamiane dwa polecenia w zintegrowanym terminalu: kompilacja Docker i narzędzie Docker push. Te dwa polecenia kompilują kod, konteneryzują plik CSharpModule.dll i wypychają go do rejestru kontenerów określonego podczas inicjowania rozwiązania.

    Zostanie wyświetlony monit o wybranie platformy modułu. Wybierz pozycję *amd64* odpowiadający systemowi Linux.

    ![Wybierz platformę](./media/azure-stack-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Obsługiwane są tylko moduły systemu Linux.

    Może zostać wyświetlone następujące ostrzeżenie:

    *Program. cs (77, 44): ostrzeżenie CS1998: Ta metoda asynchroniczna nie zawiera operatorów "await" i zostanie uruchomiona synchronicznie. Rozważ użycie operatora "await" do oczekiwania na nieblokujące wywołania interfejsu API lub "await Task. Run (...)" w celu wykonania pracy związanej z PROCESORem w wątku w tle.*

4. Pełny adres obrazu kontenera możesz wyświetlić za pomocą tagu w zintegrowanym terminalu programu VS Code. Adres obrazu jest tworzony na podstawie informacji znajdujących się w module.jsw pliku z formatem `<repository>:<version>-<platform>` . W tym artykule powinien wyglądać tak `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64` .

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć i uruchomić ten moduł w Azure Stack EDGE Pro, zobacz kroki opisane w temacie [Dodawanie modułu](azure-stack-edge-deploy-configure-compute.md#add-a-module).
