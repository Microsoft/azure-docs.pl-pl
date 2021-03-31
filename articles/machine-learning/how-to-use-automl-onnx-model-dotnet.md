---
title: Tworzenie prognoz przy użyciu modelu AutoML ONNX w programie .NET
description: Dowiedz się, jak tworzyć przewidywania przy użyciu modelu ONNX AutoML w środowisku .NET z ML.NET
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to, automl
ms.openlocfilehash: 4fb147dc5c57c3a98607a025f566fa583bf87460
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93358817"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>Tworzenie prognoz z modelem ONNX AutoML w środowisku .NET

W tym artykule dowiesz się, jak korzystać z zautomatyzowanego modelu neuronowych (AutoML) Open Network Exchange (ONNX) w celu wykonywania prognoz w aplikacji konsolowej .NET Core w języku C# za pomocą ML.NET.

[Ml.NET](/dotnet/machine-learning/) to platforma typu "open source", międzyplatformowa, dla ekosystemu .NET, która umożliwia uczenie i używanie niestandardowych modeli uczenia maszynowego w języku C# lub F #, a także za pomocą narzędzi do obsługi małych kodów, takich jak [Konstruktor modelu](/dotnet/machine-learning/automate-training-with-model-builder) i [interfejs wiersza polecenia ml.NET](/dotnet/machine-learning/automate-training-with-cli). Struktura jest również rozszerzalna i pozwala korzystać z innych popularnych platform uczenia maszynowego, takich jak TensorFlow i ONNX.

ONNX to format Open Source dla modeli AI. ONNX obsługuje współdziałanie między strukturami. Oznacza to, że możesz nauczyć model w jednym z wielu popularnych platform uczenia maszynowego, takich jak PyTorch, przekonwertować go na format ONNX i korzystać z modelu ONNX w różnych strukturach, takich jak ML.NET. Aby dowiedzieć się więcej, odwiedź witrynę [sieci Web ONNX](https://onnx.ai/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Zestaw .NET Core SDK 3,1 lub więcej](https://dotnet.microsoft.com/download)
- Edytor tekstu lub środowisko IDE (takie jak [Visual Studio](https://visualstudio.microsoft.com/vs/) lub [Visual Studio Code](https://code.visualstudio.com/Download))
- Model ONNX. Aby dowiedzieć się, jak szkolić model AutoML ONNX, zobacz następujący [Notes klasyfikacji marketingowej dla banku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).
- [Netron](https://github.com/lutzroeder/netron) (opcjonalnie)

## <a name="create-a-c-console-application"></a>Tworzenie aplikacji konsolowej w języku C#

W tym przykładzie użyto interfejs wiersza polecenia platformy .NET Core do skompilowania aplikacji, ale można wykonać te same zadania przy użyciu programu Visual Studio. Dowiedz się więcej na temat [interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools/).

1. Otwórz Terminal i Utwórz nową aplikację konsolową w języku C# .NET Core. W tym przykładzie nazwą aplikacji jest `AutoMLONNXConsoleApp` . Katalog jest tworzony za pomocą tej samej nazwy z zawartością aplikacji.

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. W terminalu przejdź do katalogu *AutoMLONNXConsoleApp* .

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>Dodaj pakiety oprogramowania

1. Zainstaluj pakiety NuGet **Microsoft.ml**, **Microsoft. ml. OnnxRuntime** i **Microsoft. ml. OnnxTransformer** przy użyciu interfejs wiersza polecenia platformy .NET Core.

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    Te pakiety zawierają zależności wymagane do użycia modelu ONNX w aplikacji .NET. ML.NET udostępnia interfejs API, który używa [środowiska uruchomieniowego ONNX](https://github.com/Microsoft/onnxruntime) do przewidywania.

1. Otwórz plik *program. cs* i Dodaj następujące `using` instrukcje w górnej części strony, aby odwołać się do odpowiednich pakietów.

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>Dodawanie odwołania do modelu ONNX

Aby Aplikacja konsolowa mogła uzyskać dostęp do modelu ONNX, należy dodać ją do katalogu wyjściowego kompilacji.  Aby dowiedzieć się więcej na temat typowych elementów programu MSBuild, zobacz Przewodnik po programie [MSBuild](/visualstudio/msbuild/common-msbuild-project-items).

Dodaj odwołanie do pliku modelu ONNX w aplikacji

1. Skopiuj model ONNX do głównego katalogu *AutoMLONNXConsoleApp* aplikacji.
1. Otwórz plik *AutoMLONNXConsoleApp. csproj* i Dodaj poniższą zawartość w `Project` węźle.

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    W takim przypadku nazwą pliku modelu ONNX jest *automl-model. ONNX*.

1. Otwórz plik *program. cs* i Dodaj następujący wiersz w `Program` klasie.

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>Zainicjuj MLContext

Wewnątrz `Main` metody `Program` klasy Utwórz nowe wystąpienie [`MLContext`](xref:Microsoft.ML.MLContext) .

```csharp
MLContext mlContext = new MLContext();
```

[`MLContext`](xref:Microsoft.ML.MLContext)Klasa jest punktem początkowym dla wszystkich operacji ml.NET, a inicjowanie `mlContext` tworzy nowe środowisko ml.NET, które może być współużytkowane przez cykl życia modelu. Jest to podobne, pojęciowo do DbContext w Entity Framework.

## <a name="define-the-model-data-schema"></a>Zdefiniuj schemat danych modelu

Model oczekuje danych wejściowych i wyjściowych w określonym formacie. ML.NET umożliwia definiowanie formatu danych za pośrednictwem klas. Czasami może już wiesz, jak wygląda ten format. W przypadkach, gdy nie znasz formatu danych, możesz użyć narzędzi, takich jak Netron, aby sprawdzić model ONNX.

Model używany w tym przykładzie używa danych z zestawu danych podróży z NYC TLC. Poniżej przedstawiono przykład danych:

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3,75|CRD|15,5|
|VTS|1|1|480|2,72|CRD|10,0|
|VTS|1|1|1680|7,8|CSH|26,5|

### <a name="inspect-the-onnx-model-optional"></a>Sprawdzanie modelu ONNX (opcjonalnie)

Użyj narzędzia, takiego jak Netron, aby sprawdzić dane wejściowe i wyjściowe modelu.

1. Otwórz Netron.
1. Na górnym pasku menu wybierz pozycję **plik > Otwórz** i użyj przeglądarki plików, aby wybrać model.
1. Zostanie otwarty model. Na przykład struktura modelu *automl-model. Onnx* wygląda następująco:

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML ONNX model":::

1. Zaznacz ostatni węzeł w dolnej części wykresu ( `variable_out1` w tym przypadku), aby wyświetlić metadane modelu. Dane wejściowe i wyjściowe na pasku bocznym pokazują oczekiwane dane wejściowe, wyjściowe i typy danych w modelu. Te informacje służą do definiowania schematu wejścia i wyjścia modelu.

### <a name="define-model-input-schema"></a>Zdefiniuj schemat wejściowy modelu

Utwórz nową klasę o nazwie `OnnxInput` z poniższymi właściwościami w pliku *program. cs* .

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

Każda z właściwości jest mapowana na kolumnę w zestawie danych. Właściwości są dalsze adnotacje z atrybutami.

Ten [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) atrybut pozwala określić, jak ml.NET powinna odwoływać się do kolumny podczas pracy z danymi. Na przykład mimo że `TripDistance` Właściwość stosuje się do standardowych konwencji nazewnictwa platformy .NET, model zna tylko kolumnę lub funkcję znaną jako `trip_distance` . Aby rozwiązać ten rozbieżność nazewnictwa, [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) atrybut mapuje `TripDistance` Właściwość na kolumnę lub funkcję według nazwy `trip_distance` .
  
W przypadku wartości numerycznych ML.NET operuje tylko na [`Single`](xref:System.Single) typach wartości. Jednak pierwotny typ danych niektórych kolumn to liczby całkowite. [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute)Atrybut mapuje typy między ONNX i ml.NET.

Aby dowiedzieć się więcej na temat atrybutów danych, zobacz [Przewodnik po załadowaniu danych ml.NET](/dotnet/machine-learning/how-to-guides/load-data-ml-net).

### <a name="define-model-output-schema"></a>Zdefiniuj schemat wyjściowy modelu

Po przetworzeniu danych dane wyjściowe są generowane w określonym formacie. Zdefiniuj schemat danych wyjściowych. Utwórz nową klasę o nazwie `OnnxOutput` z poniższymi właściwościami w pliku *program. cs* .

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

Podobnie jak w `OnnxInput` przypadku, użyj [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) atrybutu, aby zmapować `variable_out1` dane wyjściowe na bardziej opisową nazwę `PredictedFare` .

## <a name="define-a-prediction-pipeline"></a>Zdefiniuj potok predykcyjny

Potok w ML.NET jest zazwyczaj serią transformacji łańcuchowych, które działają na danych wejściowych w celu wygenerowania danych wyjściowych. Aby dowiedzieć się więcej na temat transformacji danych, zobacz [Przewodnik dotyczący transformacji danych ml.NET](/dotnet/machine-learning/resources/transforms).

1. Utwórz nową metodę o nazwie `GetPredictionPipeline` w `Program` klasie

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. Zdefiniuj nazwę kolumn wejściowych i wyjściowych. Dodaj następujący kod wewnątrz `GetPredictionPipeline` metody.

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. Zdefiniuj potok. [`IEstimator`](xref:Microsoft.ML.IEstimator%601)A zawiera plany operacji, danych wejściowych i wyjściowych w potoku.

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    W tym przypadku [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) jest jedynym przekształcaniem w potoku, który przyjmuje nazwy kolumn danych wejściowych i wyjściowych, a także ścieżkę do pliku modelu ONNX.

1. [`IEstimator`](xref:Microsoft.ML.IEstimator%601)Definiuje zestaw operacji, które mają być stosowane do danych. Operacje wykonywane na danych są nazywane [`ITransformer`](xref:Microsoft.ML.ITransformer) . Użyj `Fit` metody, aby utworzyć jedną z nich `onnxPredictionPipeline` .

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A)Metoda oczekuje [`IDataView`](xref:Microsoft.ML.IDataView) jako dane wejściowe do wykonania operacji na. [`IDataView`](xref:Microsoft.ML.IDataView)Jest sposobem reprezentowania danych w ml.NET przy użyciu formatu tabelarycznego. Ponieważ w tym przypadku potok jest używany tylko do prognozowania, można podać wartość pustą, [`IDataView`](xref:Microsoft.ML.IDataView) Aby uzyskać [`ITransformer`](xref:Microsoft.ML.ITransformer) niezbędne informacje o schemacie wejściowym i wyjściowym. Jest on [`ITransformer`](xref:Microsoft.ML.ITransformer) następnie zwracany do dalszych zastosowań w aplikacji.

    > [!TIP]
    > W tym przykładzie potok jest zdefiniowany i używany w tej samej aplikacji. Zaleca się jednak używanie oddzielnych aplikacji do definiowania i używania potoku do tworzenia prognoz. W ML.NET potoki można serializować i zapisać do dalszych zastosowań w innych aplikacjach użytkowników końcowych platformy .NET. Usługa ML.NET obsługuje różne cele wdrażania, takie jak aplikacje klasyczne, usługi sieci Web, aplikacje webassembly * i wiele innych. Aby dowiedzieć się więcej na temat zapisywania potoków, zobacz [Przewodnik dotyczący zapisywania i ładowania modeli ml.NET](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net).
    >
    > * Zestaw webassembly jest obsługiwany tylko w programie .NET Core 5 lub nowszym

1. Wewnątrz `Main` metody Wywołaj `GetPredictionPipeline` metodę z wymaganymi parametrami.

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>Tworzenie prognoz przy użyciu modelu

Teraz, gdy masz potok, czas na jego użycie do prognozowania. ML.NET zapewnia wygodny interfejs API do tworzenia prognoz dla jednego wystąpienia danych o nazwie [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) .

1. Wewnątrz `Main` metody Utwórz [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) za pomocą [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) metody.

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. Utwórz testowe dane wejściowe.

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. Użyj, `predictionEngine` Aby dokonać prognoz na podstawie nowych `testInput` danych przy użyciu [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) metody.

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. Wyprowadza wynik przewidywania do konsoli.

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. Użyj interfejs wiersza polecenia platformy .NET Core, aby uruchomić aplikację.

    ```dotnetcli
    dotnet run
    ```

    Wynik powinien wyglądać podobnie do następującego:

    ```text
    Predicted Fare: 15.621523
    ```

Aby dowiedzieć się więcej na temat tworzenia prognoz w programie ML.NET, zobacz [Przewodnik dotyczący korzystania z modelu do tworzenia prognoz](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net).

## <a name="next-steps"></a>Następne kroki

- [Wdróż model jako ASP.NET Core internetowy interfejs API](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [Wdróż swój model jako bezserwerową funkcję platformy .NET platformy Azure](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)