---
title: Tworzenie niestandardowych deserializacji platformy .NET na potrzeby Azure Stream Analytics zadań w chmurze przy użyciu Visual Studio Code
description: W tym samouczku przedstawiono sposób tworzenia niestandardowej deserializacji platformy .NET na potrzeby zadania w chmurze Azure Stream Analytics przy użyciu Visual Studio Code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 1813fb222bca74f355fec52252ce3d77fef06e5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013927"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Tworzenie niestandardowych deserializacji platformy .NET dla Azure Stream Analytics w Visual Studio Code

Azure Stream Analytics ma [wbudowaną obsługę trzech formatów danych](stream-analytics-parsing-json.md): JSON, CSV i Avro. Za pomocą niestandardowych deserializacji platformy .NET można odczytywać dane z innych formatów, takich jak [bufor protokołu](https://developers.google.com/protocol-buffers/), [obligacje](https://github.com/Microsoft/bond) i inne formaty zdefiniowane przez użytkownika dla zadań w chmurze.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Niestandardowe deserializacji platformy .NET w Visual Studio Code

Możesz tworzyć, testować i debugować niestandardowe deserializatory .NET dla zadania Azure Stream Analytics w chmurze przy użyciu Visual Studio Code.

### <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [zestaw SDK platformy .NET Core](https://dotnet.microsoft.com/download) i ponownie uruchom Visual Studio Code.

* Skorzystaj z tego [przewodnika Szybki Start](quick-create-visual-studio-code.md) , aby dowiedzieć się, jak utworzyć zadanie Stream Analytics przy użyciu Visual Studio Code.

### <a name="create-a-custom-deserializer"></a>Tworzenie deserializatora niestandardowego

1. Otwórz Terminal i uruchom następujące polecenie, aby utworzyć bibliotekę klas .NET w Visual Studio Code dla niestandardowego deserializacji o nazwie **ProtobufDeserializer**.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. Przejdź do katalogu projektu **ProtobufDeserializer** i zainstaluj pakiety NuGet [Microsoft. Azure. StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) i [Google. protobuf](https://www.nuget.org/packages/Google.Protobuf/) .

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. Dodaj [klasę MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) i [klasę MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) do projektu.

4. Kompiluj projekt **ProtobufDeserializer** .

### <a name="add-an-azure-stream-analytics-project"></a>Dodaj projekt Azure Stream Analytics

1. Otwórz Visual Studio Code i wybierz **kombinację klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń. Następnie wprowadź ASA i wybierz **ASA: Utwórz nowy projekt**. Nadaj mu nazwę **ProtobufCloudDeserializer**.

### <a name="configure-a-stream-analytics-job"></a>Konfigurowanie zadania Stream Analytics

1. Kliknij dwukrotnie **JobConfig.js**. Użyj konfiguracji domyślnych, z wyjątkiem następujących ustawień:

   |Ustawienie|Sugerowana wartość|
   |-------|---------------|
   |Zasób ustawień magazynu globalnego|Wybierz źródło danych z bieżącego konta|
   |Subskrypcja ustawień magazynu globalnego| < subskrypcję >|
   |Konto magazynu ustawień globalnych magazynu| < konta magazynu >|
   |Konto magazynu ustawień CustomCodeStorage|< konta magazynu >|
   |Kontener ustawień CustomCodeStorage|< kontener magazynu >|

2. W obszarze folder **danych wejściowych** Otwórz **input.jsna**. Wybierz pozycję **Dodaj dane wejściowe na żywo** i Dodaj dane wejściowe z usługi Azure Data Lake Storage Gen2/BLOB Storage, wybierz **pozycję Wybierz z subskrypcji platformy Azure**. Użyj konfiguracji domyślnych, z wyjątkiem następujących ustawień:

   |Ustawienie|Sugerowana wartość|
   |-------|---------------|
   |Nazwa|Dane wejściowe|
   |Subskrypcja|< subskrypcję >|
   |Konto magazynu|< konta magazynu >|
   |Kontener|< kontener magazynu >|
   |Typ serializacji|Wybierz **niestandardowe**|
   |SerializationProjectPath|Wybierz opcję **Wybierz ścieżkę projektu biblioteki** z CodeLens i wybierz projekt biblioteki **ProtobufDeserializer** utworzony w poprzedniej sekcji. Wybieranie **projektu kompilacji** w celu skompilowania projektu|
   |SerializationClassName|Wybierz pozycję **Wybierz klasę deserializacji** z CodeLens, aby automatycznie wypełnić nazwę klasy i ścieżkę biblioteki DLL|
   |Nazwa klasy|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="Dodaj niestandardowe dane wejściowe deserializacji.":::

3. Dodaj następujące zapytanie do pliku **ProtobufCloudDeserializer. asaql** .

   ```sql
   SELECT * FROM Input
   ```

4. Pobierz [przykładowy plik wejściowy protobuf](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). W folderze **dane wejściowe** kliknij prawym przyciskiem myszy pozycję **input.jsna** i wybierz pozycję **Dodaj lokalne dane wejściowe**. Następnie kliknij dwukrotnie **local_input1.jsna** i użyj konfiguracji domyślnych, z wyjątkiem następujących ustawień.

   |Ustawienie|Sugerowana wartość|
   |-------|---------------|
   |Wybierz ścieżkę do pliku lokalnego|Kliknij pozycję CodeLens, aby wybrać < ścieżkę pliku pobranego przykładowego pliku protobuf>|

### <a name="execute-the-stream-analytics-job"></a>Wykonywanie zadania Stream Analytics

1. Otwórz **ProtobufCloudDeserializer. asaql** i wybierz pozycję **Uruchom lokalnie** z programu CodeLens, a następnie wybierz pozycję **Użyj lokalnych danych wejściowych** z listy rozwijanej.

2. Obserwuj wyniki na karcie **wyniki** w widoku diagramu zadań po prawej stronie. Możesz również kliknąć kroki na diagramie zadań, aby wyświetlić pośredni wynik. Więcej szczegółów można znaleźć [w sekcji debugowanie lokalne przy użyciu diagramu zadań](debug-locally-using-job-diagram-vs-code.md).

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="Sprawdź wynik uruchomienia lokalnego.":::

Pomyślnie zaimplementowano Deserializator niestandardowy dla zadania Stream Analytics! W tym samouczku przetestowano Deserializator niestandardowy lokalnie z lokalnymi danymi wejściowymi. Można go również przetestować [przy użyciu danych wejściowych na żywo w chmurze](visual-studio-code-local-run-live-input.md). W przypadku uruchamiania zadania w chmurze należy prawidłowo skonfigurować dane wejściowe i wyjściowe. Następnie Prześlij zadanie do platformy Azure z Visual Studio Code, aby uruchomić zadanie w chmurze przy użyciu właśnie zaimplementowanego deserializacji.

### <a name="debug-your-deserializer"></a>Debugowanie deserializacji

Deserializacji platformy .NET można debugować lokalnie w taki sam sposób, jak w przypadku debugowania standardowego kodu platformy .NET. 

1. Dodaj punkty przerwania w funkcji .NET.

2. Kliknij pozycję **Uruchom** na pasku działania Visual Studio Code a następnie wybierz pozycję **Utwórz launch.jsna pliku**.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="Utwórz plik uruchamiania.":::

   Wybierz pozycję **ProtobufCloudDeserializer** , a następnie **Azure Stream Analytics** z listy rozwijanej.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="Utwórz plik uruchamiania 2.":::

   Edytuj **launch.js** pliku, aby zastąpić plik <ASAScript> . asaql z ProtobufCloudDeserializer. asaql.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="Skonfiguruj plik uruchamiania.":::

3. Naciśnij klawisz **F5**, aby uruchomić debugowanie. Zgodnie z oczekiwaniami program będzie zatrzymywać się w punktach przerwania. Działa to zarówno w przypadku lokalnych danych wejściowych, jak i na żywo.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="Debuguj niestandardową Deserializator.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, zadanie przesyłania strumieniowego i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć opłat za jednostki przesyłania strumieniowego zużywane przez zadanie. Jeśli planujesz użyć zadania w przyszłości, możesz je zatrzymać i uruchomić ponownie później, gdy będzie potrzebne. Jeśli nie zamierzasz w przyszłości korzystać z tego zadania, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego samouczka:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal wybierz pozycję **Grupy zasobów**, a następnie wybierz nazwę utworzonego zasobu.  

2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób implementacji niestandardowego deserializacji platformy .NET na potrzeby serializacji danych wejściowych buforu protokołu. Aby dowiedzieć się więcej na temat tworzenia deserializatorów niestandardowych, przejdź do następującego artykułu:

> [!div class="nextstepaction"]
> * [Tworzenie różnych deserializacji platformy .NET dla zadań Azure Stream Analytics](custom-deserializer-examples.md)
> * [Przetestuj Azure Stream Analytics zadania lokalnie z danymi wejściowymi na żywo przy użyciu Visual Studio Code](visual-studio-code-local-run-live-input.md)