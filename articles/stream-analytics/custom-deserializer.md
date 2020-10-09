---
title: Samouczek — niestandardowe deserializacji platformy .NET na potrzeby zadań w chmurze Azure Stream Analytics
description: W tym samouczku przedstawiono sposób tworzenia niestandardowej deserializacji platformy .NET dla zadania Azure Stream Analytics w chmurze przy użyciu programu Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 1fffeec1434cb066487bf383589554edec2e6a86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75443696"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Samouczek: niestandardowe deserializacji platformy .NET dla Azure Stream Analytics

Azure Stream Analytics ma [wbudowaną obsługę trzech formatów danych](stream-analytics-parsing-json.md): JSON, CSV i Avro. Za pomocą niestandardowych deserializacji platformy .NET można odczytywać dane z innych formatów, takich jak [bufor protokołu](https://developers.google.com/protocol-buffers/), [obligacje](https://github.com/Microsoft/bond) i inne formaty zdefiniowane przez użytkownika dla zadań chmurowych i brzegowych.

W tym samouczku przedstawiono sposób tworzenia niestandardowej deserializacji platformy .NET dla zadania Azure Stream Analytics w chmurze przy użyciu programu Visual Studio. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz niestandardową deserializatorę dla buforu protokołu.
> * Utwórz zadanie Azure Stream Analytics w programie Visual Studio.
> * Skonfiguruj zadanie Stream Analytics tak, aby korzystało z deserializatora niestandardowego.
> * Uruchom zadanie Stream Analytics lokalnie w celu przetestowania niestandardowego deserializacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Zainstaluj [program Visual studio 2017](https://www.visualstudio.com/downloads/) lub [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/). Obsługiwane są wersje Enterprise (Ultimate/Premium), Professional i Community. Wersja Express nie jest obsługiwana.

* [Zainstaluj narzędzia Stream Analytics dla programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md) lub zaktualizuj do najnowszej wersji. Obsługiwane są następujące wersje programu Visual Studio:
   * Visual Studio 2015
   * Visual Studio 2017

* Otwórz program **Cloud Explorer** w programie Visual Studio i zaloguj się do subskrypcji platformy Azure.

* Utwórz kontener na koncie usługi Azure Storage.
Tworzony kontener będzie używany do przechowywania zasobów związanych z zadaniem Stream Analytics. Jeśli masz już konto magazynu z istniejącymi kontenerami, możesz go użyć. Jeśli nie, musisz [utworzyć nowy kontener](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Tworzenie deserializatora niestandardowego

1. Otwórz program Visual Studio i wybierz pozycję **plik > nowy > projekt**. Wyszukaj **Stream Analytics** i wybierz **Azure Stream Analytics projekt deserializacji niestandardowego (.NET)**. Nadaj projektowi nazwę, taką jak **Deserializator protobuf**.

   ![Utwórz projekt biblioteki klas standardowych programu Visual Studio dotnet](./media/custom-deserializer/create-dotnet-library-project.png)

2. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **deserializacji protobuf** i wybierz polecenie **Zarządzaj pakietami NuGet** z menu. Następnie zainstaluj pakiety NuGet **Microsoft. Azure. StreamAnalytics** i **Google. protobuf** .

3. Dodaj [klasę MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) i [klasę MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) do projektu.

4. Kompiluj projekt **deserializacji protobuf** .

## <a name="add-an-azure-stream-analytics-project"></a>Dodaj projekt Azure Stream Analytics

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie **deserializacji protobuf** i wybierz polecenie **Dodaj > nowy projekt**. W obszarze **Azure Stream Analytics > Stream Analytics**wybierz pozycję **Azure Stream Analytics aplikacja**. Nadaj mu nazwę **ProtobufCloudDeserializer** i wybierz **przycisk OK**. 

2. Kliknij prawym przyciskiem myszy pozycję **odwołania** w projekcie **ProtobufCloudDeserializer** Azure Stream Analytics. W obszarze **projekty**Dodaj **Deserializator protobuf**. Powinna zostać automatycznie wypełniona.

## <a name="configure-a-stream-analytics-job"></a>Konfigurowanie zadania Stream Analytics

1. Kliknij dwukrotnie **JobConfig.js**. Użyj konfiguracji domyślnych, z wyjątkiem następujących ustawień:

   |Ustawienie|Sugerowana wartość|
   |-------|---------------|
   |Zasób ustawień magazynu globalnego|Wybierz źródło danych z bieżącego konta|
   |Subskrypcja ustawień magazynu globalnego| < subskrypcję >|
   |Konto magazynu ustawień globalnych magazynu| < konta magazynu >|
   |Zasób ustawień niestandardowego magazynu kodu|Wybierz źródło danych z bieżącego konta|
   |Konto magazynu ustawień niestandardowych magazynu kodu|< konta magazynu >|
   |Kontener ustawień niestandardowego magazynu kodu|< kontener magazynu >|

2. W obszarze **dane wejściowe**kliknij dwukrotnie pozycję **Input.json**. Użyj konfiguracji domyślnych, z wyjątkiem następujących ustawień:

   |Ustawienie|Sugerowana wartość|
   |-------|---------------|
   |Źródło|Blob Storage|
   |Zasób|Wybierz źródło danych z bieżącego konta|
   |Subskrypcja|< subskrypcję >|
   |Konto magazynu|< konta magazynu >|
   |Kontener|< kontener magazynu >|
   |Format serializacji zdarzeń|Inne (protobuf, XML, własnościowe...)|
   |Zasób|Załaduj z odwołania do projektu ASA lub CodeBehind|
   |Nazwa zestawu CSharp|ProtobufDeserializer.dll|
   |Nazwa klasy|MessageBodyProto.MessageBodyDeserializer|
   |Typ kompresji zdarzenia|Brak|

3. Dodaj następujące zapytanie do pliku **Script. asaql** .

   ```sql
   SELECT * FROM Input
   ```

4. Pobierz [przykładowy plik wejściowy protobuf](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). W folderze **dane wejściowe** kliknij prawym przyciskiem myszy pozycję **Input.jsna** i wybierz pozycję **Dodaj lokalne dane wejściowe**. Następnie kliknij dwukrotnie **local_Input.jsna** i skonfiguruj następujące ustawienia:

   |Ustawienie|Sugerowana wartość|
   |-------|---------------|
   |Alias danych wejściowych|Dane wejściowe|
   |Typ źródła|Strumień danych|
   |Format serializacji zdarzeń|Inne (protobuf, XML, własnościowe...)|
   |Nazwa zestawu CSharp|ProtobufDeserializer.dll|
   |Nazwa klasy|MessageBodyProto.MessageBodyDeserializer|
   |Ścieżka lokalnego pliku wejściowego|< ścieżkę pliku pobranego przykładowego pliku wejściowego protobuf>|

## <a name="execute-the-stream-analytics-job"></a>Wykonywanie zadania Stream Analytics

1. Otwórz **skrypt. asaql** i wybierz pozycję **Uruchom lokalnie**.

2. Obserwuj wyniki w **Stream Analytics wyników uruchamiania lokalnego**.

Pomyślnie zaimplementowano Deserializator niestandardowy dla zadania Stream Analytics! W tym samouczku przetestowano Deserializator niestandardowy lokalnie. W przypadku rzeczywistych danych należy prawidłowo skonfigurować dane wejściowe i wyjściowe. Następnie Prześlij zadanie na platformę Azure z programu Visual Studio, aby uruchomić zadanie w chmurze przy użyciu właśnie zaimplementowanego deserializacji.

## <a name="debug-your-deserializer"></a>Debugowanie deserializacji

Deserializacji platformy .NET można debugować lokalnie w taki sam sposób, jak w przypadku debugowania standardowego kodu platformy .NET. 

1. Dodaj punkty przerwania w funkcji.

2. Naciśnij klawisz **F5**, aby uruchomić debugowanie. Zgodnie z oczekiwaniami program będzie zatrzymywać się w punktach przerwania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, zadanie przesyłania strumieniowego i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć opłat za jednostki przesyłania strumieniowego zużywane przez zadanie. Jeśli planujesz użyć zadania w przyszłości, możesz je zatrzymać i uruchomić ponownie później, gdy będzie potrzebne. Jeśli nie zamierzasz w przyszłości korzystać z tego zadania, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego samouczka:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal wybierz pozycję **Grupy zasobów**, a następnie wybierz nazwę utworzonego zasobu.  

2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób implementacji niestandardowego deserializacji platformy .NET na potrzeby serializacji danych wejściowych buforu protokołu. Aby dowiedzieć się więcej na temat tworzenia deserializatorów niestandardowych, przejdź do następującego artykułu:

> [!div class="nextstepaction"]
> [Tworzenie różnych deserializacji platformy .NET dla zadań Azure Stream Analytics](custom-deserializer-examples.md)
