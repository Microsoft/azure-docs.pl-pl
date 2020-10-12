---
title: Opis parsera Digital bliźniaczych reprezentacji model | Microsoft Docs
description: Jako deweloper, Dowiedz się, jak używać analizatora DTDL do sprawdzania poprawności modeli
author: rido-min
ms.author: rmpablos
ms.date: 04/29/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: peterpr
ms.openlocfilehash: 20c4452a32c791f33e08c883d8cec89a345ab188
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87352435"
---
# <a name="understand-the-digital-twins-model-parser"></a>Opis parsera modeli usługi Digital Twins

Digital bliźniaczych reprezentacji Definition Language (DTDL) jest opisany w [specyfikacji DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Użytkownicy mogą używać pakietu NuGet _parsera modeli bliźniaczych reprezentacji Digital_ do sprawdzania poprawności i wysyłania zapytań do modelu zdefiniowanego w wielu plikach.

## <a name="install-the-dtdl-model-parser"></a>Instalowanie parsera modeli DTDL

Analizator jest dostępny w NuGet.org z IDENTYFIKATORem: [Microsoft. Azure. DigitalTwins. parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Aby zainstalować parser, użyj dowolnego zgodnego Menedżera pakietów NuGet, takiego jak ten w programie Visual Studio lub `dotnet` interfejsie wiersza polecenia.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

## <a name="use-the-parser-to-validate-a-model"></a>Weryfikowanie modelu przy użyciu analizatora

Model, który ma zostać zweryfikowany, może składać się z co najmniej jednego interfejsu opisanego w plikach JSON. Analizatora można użyć do załadowania wszystkich plików w danym folderze i użycia analizatora do walidacji wszystkich plików jako całości, w tym wszystkich odwołań między plikami:

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

1. Sprawdź, czy występują błędy walidacji. Jeśli analizator odnajdzie błędy, zgłasza komunikat `AggregateException` z listą szczegółowych komunikatów o błędach:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (AggregateException ae)
    {
        foreach (var e in ae.InnerExceptions)
        {
            Console.WriteLine(e.Message);
        }
    }
    ```

1. Sprawdź `Model` . Jeśli sprawdzanie poprawności zakończy się powodzeniem, można użyć interfejsu API parsera modelu do sprawdzenia modelu. Poniższy fragment kodu przedstawia sposób iteracji wszystkich modeli przeanalizowanych i wyświetla istniejące właściwości:

    ```csharp
    foreach (var m in parseResult)
    {
        Console.WriteLine(m.Key);
        foreach (var item in m.Value.AsEnumerable<DTEntityInfo>())
        {
            var p = item as DTInterfaceInfo;
            if (p!=null)
            {
                Console.WriteLine($"\t{p.Id}");
                Console.WriteLine($"\t{p.Description.FirstOrDefault()}");
            }
            Console.WriteLine("--------------");
        }
    }
    ```

## <a name="next-steps"></a>Następne kroki

Interfejs API parsera modeli przeglądany w tym artykule umożliwia automatyzację lub Weryfikowanie zadań, które są zależne od modeli DTDL. Na przykład można dynamicznie skompilować interfejs użytkownika z informacji w modelu.
