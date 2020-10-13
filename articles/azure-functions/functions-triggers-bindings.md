---
title: Wyzwalacze i powiązania w Azure Functions
description: Dowiedz się, jak używać wyzwalaczy i powiązań, aby połączyć funkcję platformy Azure ze zdarzeniami online i usługami w chmurze.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: e00fd3d1dac0a18ac7f7377e08ae8d20ae132c56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91652629"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Pojęcia powiązań i wyzwalaczy usługi Azure Functions

Ten artykuł zawiera informacje o pojęciach dotyczących wysokiego poziomu otaczających wyzwalacze i powiązania funkcji.

Wyzwalacze to przyczyny uruchomienia funkcji. Wyzwalacz definiuje sposób wywoływania funkcji, a funkcja musi mieć dokładnie jeden wyzwalacz. Wyzwalacze mają skojarzone dane, które często są dostarczane jako ładunek funkcji. 

Powiązanie z funkcją jest sposobem deklaratywnego łączenia innego zasobu z funkcją; powiązania mogą być połączone jako *powiązania wejściowe*, *powiązania wyjściowe*lub oba te elementy. Dane z powiązań są podawane do funkcji jako parametry.

Możesz mieszać i dopasowywać różne powiązania, aby dostosować je do własnych potrzeb. Powiązania są opcjonalne, a funkcja może mieć jedno lub wiele powiązań wejściowych i/lub wyjściowych.

Wyzwalacze i powiązania pozwalają uniknąć zakodowana dostępu do innych usług. Funkcja będzie odbierać dane (np. zawartość komunikatu kolejki) w parametrach funkcji. Możesz wysłać dane (np. w celu utworzenia komunikatu kolejki) przy użyciu wartości zwracanej przez funkcję. 

Rozważmy następujące przykłady sposobu implementacji różnych funkcji.

| Przykładowy scenariusz | Wyzwalacz | Powiązanie danych wejściowych | Powiązanie danych wyjściowych |
|-------------|---------|---------------|----------------|
| Zostanie wyświetlony nowy komunikat w kolejce, który uruchamia funkcję do zapisu w innej kolejce. | Niej<sup>*</sup> | *Brak* | Niej<sup>*</sup> |
|Zaplanowane zadanie odczytuje Blob Storage zawartość i tworzy nowy dokument Cosmos DB. | Czasomierz | Blob Storage | Cosmos DB |
|Event Grid jest używany do odczytywania obrazu z Blob Storage i dokumentu z Cosmos DB do wysłania wiadomości e-mail. | Event Grid | Blob Storage i Cosmos DB | SendGrid |
| Element webhook, który używa Microsoft Graph do aktualizowania arkusza programu Excel. | HTTP | *Brak* | Microsoft Graph |

<sup>\*</sup> Reprezentuje różne kolejki

Te przykłady nie są wyczerpujące, ale są dostarczane w celu zilustrowania, jak można używać wyzwalaczy i powiązań jednocześnie.

###  <a name="trigger-and-binding-definitions"></a>Definicje wyzwalacza i powiązania

Wyzwalacze i powiązania są zdefiniowane inaczej w zależności od podejścia do programowania.

| Platforma | Wyzwalacze i powiązania są konfigurowane przez... |
|-------------|--------------------------------------------|
| Biblioteka klas języka C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dekorowania nazwy metod i parametrów przy użyciu atrybutów języka C# |
| Wszystkie inne (w tym Azure Portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Aktualizowanie [function.jsna](./functions-reference.md) ([schemat](http://json.schemastore.org/function)) |

Portal udostępnia interfejs użytkownika dla tej konfiguracji, ale można edytować plik bezpośrednio, otwierając **Edytor zaawansowany** dostępny za pośrednictwem karty **integracja** funkcji.

W programie .NET typ parametru definiuje typ danych danych wejściowych. Na przykład, użyj, `string` Aby powiązać z tekstem wyzwalacza kolejki, tablicę bajtową do odczytu jako Binary i typ niestandardowy do deserializacji do obiektu.

W przypadku języków, które są dynamicznie wpisywane, takich jak JavaScript, użyj `dataType` właściwości w *function.js* pliku. Na przykład, aby odczytać zawartość żądania HTTP w formacie binarnym, ustaw opcję `dataType` na `binary` :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Inne opcje dla programu `dataType` to `stream` i `string` .

## <a name="binding-direction"></a>Kierunek powiązania

Wszystkie wyzwalacze i powiązania mają `direction` Właściwość w [function.js](./functions-reference.md) w pliku:

- W przypadku wyzwalaczy kierunek jest zawsze `in`
- Powiązania danych wejściowych i wyjściowych używają `in` i `out`
- Niektóre powiązania obsługują specjalny kierunek `inout` . Jeśli używasz programu `inout` , tylko **Edytor zaawansowany** jest dostępny za pośrednictwem karty **integracja** w portalu.

W przypadku używania [atrybutów w bibliotece klas](functions-dotnet-class-library.md) do konfigurowania wyzwalaczy i powiązań, kierunek jest udostępniany w konstruktorze atrybutu lub wywnioskowany na podstawie typu parametru.

## <a name="add-bindings-to-a-function"></a>Dodawanie powiązań do funkcji

Możesz połączyć funkcję z innymi usługami przy użyciu powiązań wejściowych lub wyjściowych. Dodawanie powiązania przez dodanie jego określonych definicji do funkcji. Aby dowiedzieć się, jak to zrobić, zobacz [Dodawanie powiązań do istniejącej funkcji w Azure Functions](add-bindings-existing-function.md).  

## <a name="supported-bindings"></a>Obsługiwane powiązania

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Aby uzyskać informacje o tym, które powiązania są w wersji zapoznawczej lub są zatwierdzone do użycia w środowisku produkcyjnym, zobacz [obsługiwane języki](supported-languages.md).

## <a name="bindings-code-examples"></a>Przykłady kodu powiązań

Skorzystaj z poniższej tabeli, aby znaleźć przykłady określonych typów powiązań, które pokazują, jak korzystać z powiązań w swoich funkcjach. Najpierw wybierz kartę językową, która odpowiada Twojemu projektowi. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="resources"></a>Zasoby
- [Wyrażenia i wzorce powiązań](./functions-bindings-expressions-patterns.md)
- [Korzystanie z wartości zwracanej przez funkcję platformy Azure](./functions-bindings-return-value.md)
- [Jak zarejestrować wyrażenie powiązania](./functions-bindings-register.md)
- Testowy
  - [Strategie testowania kodu w usłudze Azure Functions](functions-test-a-function.md)
  - [Ręczne uruchamianie funkcji niewyzwalanej przez protokół HTTP](functions-manually-run-non-http.md)
- [Obsługa błędów powiązań](./functions-bindings-errors.md)

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Rejestrowanie rozszerzeń powiązań Azure Functions](./functions-bindings-register.md)
