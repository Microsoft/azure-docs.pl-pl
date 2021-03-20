---
title: Opis parsera Digital bliźniaczych reprezentacji model | Microsoft Docs
description: Deweloperzy mogą dowiedzieć się, jak używać analizatora DTDL do sprawdzania poprawności modeli.
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d68abe8548dac3306228683e4b6ce8935a248ebc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92331791"
---
# <a name="understand-the-digital-twins-model-parser"></a>Opis parsera modeli usługi Digital Twins

Digital bliźniaczych reprezentacji Definition Language (DTDL) jest opisany w [specyfikacji DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Użytkownicy mogą używać pakietu NuGet _parsera modeli bliźniaczych reprezentacji Digital_ do sprawdzania poprawności i wysyłania zapytań do modelu zdefiniowanego w wielu plikach.

## <a name="install-the-dtdl-model-parser"></a>Instalowanie parsera modeli DTDL

Analizator jest dostępny w NuGet.org z IDENTYFIKATORem: [Microsoft. Azure. DigitalTwins. parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Aby zainstalować parser, użyj dowolnego zgodnego Menedżera pakietów NuGet, takiego jak ten w programie Visual Studio lub `dotnet` interfejsie wiersza polecenia.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> W momencie pisania wersja parsera to `3.12.5` .

## <a name="use-the-parser-to-validate-a-model"></a>Weryfikowanie modelu przy użyciu analizatora

Model może składać się z co najmniej jednego interfejsu opisanego w plikach JSON. Analizatora można użyć do załadowania wszystkich plików w danym folderze i użycia analizatora do walidacji wszystkich plików jako całości, w tym wszystkich odwołań między plikami:

1. Utwórz `IEnumerable<string>` z listą wszystkich zawartości modelu:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. Tworzenie wystąpienia `ModelParser` wywołania i `ParseAsync` :

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Sprawdź, czy występują błędy walidacji. Jeśli analizator odnajdzie błędy, zgłasza `ParsingException` listę błędów:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. Sprawdź `Model` . Jeśli sprawdzanie poprawności zakończy się powodzeniem, można użyć interfejsu API parsera modelu do sprawdzenia modelu. Poniższy fragment kodu przedstawia sposób iteracji wszystkich modeli przeanalizowanych i wyświetla istniejące właściwości:

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>Następne kroki

Interfejs API parsera modeli przeglądany w tym artykule umożliwia automatyzację lub Weryfikowanie zadań, które są zależne od modeli DTDL. Na przykład można dynamicznie skompilować interfejs użytkownika z informacji w modelu.
