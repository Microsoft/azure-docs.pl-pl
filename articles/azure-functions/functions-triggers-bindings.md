---
title: Wyzwalacze i powiązania w Azure Functions
description: Dowiedz się, jak używać wyzwalaczy i powiązań do łączenia funkcji platformy Azure ze zdarzeniami online i usługami w chmurze.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8648a52c58929983070b9a89c8fe946b89d37385
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739408"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Pojęcia powiązań i wyzwalaczy usługi Azure Functions

W tym artykule poznasz pojęcia wysokiego poziomu dotyczące wyzwalaczy i powiązań funkcji.

Wyzwalacze są przyczyną uruchomienia funkcji. Wyzwalacz definiuje sposób wywoływania funkcji, a funkcja musi mieć dokładnie jeden wyzwalacz. Wyzwalacze mają skojarzone dane, które często są dostarczane jako ładunek funkcji. 

Powiązanie z funkcją to sposób deklaratywnego łączenia innego zasobu z funkcją; Powiązania mogą być połączone jako *powiązania wejściowe,* powiązania *wyjściowe* lub oba te powiązania. Dane z powiązań są podawane do funkcji jako parametry.

Możesz mieszać i dopasowywać różne powiązania, aby dostosować je do własnych potrzeb. Powiązania są opcjonalne, a funkcja może mieć jedno lub wiele powiązań wejściowych i/lub wyjściowych.

Wyzwalacze i powiązania pozwalają uniknąć twardego skodytowania dostępu do innych usług. Funkcja będzie odbierać dane (np. zawartość komunikatu kolejki) w parametrach funkcji. Możesz wysłać dane (np. w celu utworzenia komunikatu kolejki) przy użyciu wartości zwracanej przez funkcję. 

Rozważmy następujące przykłady implementacji różnych funkcji.

| Przykładowy scenariusz | Wyzwalacz | Powiązanie wejściowe | Powiązanie wyjściowe |
|-------------|---------|---------------|----------------|
| Zostanie wyświetlony nowy komunikat w kolejce, który uruchamia funkcję zapisu w innej kolejce. | Kolejki<sup>*</sup> | *Brak* | Kolejki<sup>*</sup> |
|Zaplanowane zadanie odczytuje Blob Storage zawartości i tworzy nowy Cosmos DB dokumentu. | Czasomierz | Blob Storage | Cosmos DB |
|Plik Event Grid jest używany do odczytywania obrazu z Blob Storage i dokumentu z Cosmos DB do wysyłania wiadomości e-mail. | Event Grid | Blob Storage i Cosmos DB | SendGrid |
| Element webhook, który używa Microsoft Graph do aktualizowania arkusza programu Excel. | HTTP | *Brak* | Microsoft Graph |

<sup>\*</sup> Reprezentuje różne kolejki

Te przykłady nie są wyczerpujące, ale zostały podane, aby zilustrować, jak można używać razem wyzwalaczy i powiązań.

###  <a name="trigger-and-binding-definitions"></a>Definicje wyzwalaczy i powiązań

Wyzwalacze i powiązania są definiowane inaczej w zależności od języka programowania.

| Język | Wyzwalacze i powiązania są konfigurowane przez... |
|-------------|--------------------------------------------|
| Biblioteka klas języka C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dekorowanie metod i parametrów za pomocą atrybutów języka C# |
| Java | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dekorowanie metod i parametrów za pomocą adnotacji języka Java  | 
| JavaScript/PowerShell/Python/TypeScript | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;aktualizowanie [function.jsna](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

W przypadku języków, które są function.js, portal udostępnia interfejs użytkownika do dodawania powiązań na **karcie Integracja.** Plik można również edytować bezpośrednio w portalu na karcie **Kod i testowanie** funkcji. Visual Studio Code można łatwo dodać powiązanie do pliku [function.js,](functions-develop-vs-code.md?tabs=nodejs#add-a-function-to-your-project) korzystając z wygodnego zestawu monitów. 

W środowiskach .NET i Java typ parametru definiuje typ danych dla danych wejściowych. Na przykład użyj funkcji , aby powiązać z tekstem wyzwalacza kolejki, tablicę bajtów do odczytu jako binarną i typ niestandardowy w celu cokołu serializacji `string` do obiektu. Ponieważ funkcje biblioteki klas .NET i  funkcje języka Java nie są zależnefunction.jsna definicjach powiązań, nie można ich tworzyć ani edytować w portalu. Edytowanie portalu w języku C# jest oparte na skrypcie języka C#, który używafunction.js *na* zamiast atrybutów.

Aby dowiedzieć się więcej na temat dodawania powiązań do istniejących funkcji, zobacz Connect functions to Azure services using bindings (Łączenie funkcji z usługami [platformy Azure przy użyciu powiązań).](add-bindings-existing-function.md)

W przypadku języków, które są typowane dynamicznie, takich jak JavaScript, użyj właściwości w `dataType` *function.jspliku.* Aby na przykład odczytać zawartość żądania HTTP w formacie binarnym, ustaw `dataType` wartość `binary` :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Inne opcje dla `dataType` to `stream` i `string` .

## <a name="binding-direction"></a>Kierunek powiązania

Wszystkie wyzwalacze i powiązania mają `direction` właściwość wfunction.js[pliku:](./functions-reference.md)

- W przypadku wyzwalaczy kierunek jest zawsze `in`
- Powiązania wejściowe i wyjściowe używają `in` powiązań i `out`
- Niektóre powiązania obsługują specjalny kierunek `inout` . Jeśli używasz narzędzia `inout` , tylko edytor **zaawansowany** jest dostępny za **pośrednictwem karty Integracja** w portalu.

Gdy używasz atrybutów [w](functions-dotnet-class-library.md) bibliotece klas do konfigurowania wyzwalaczy i powiązań, kierunek jest podany w konstruktorze atrybutu lub wywnioskował typ parametru.

## <a name="add-bindings-to-a-function"></a>Dodawanie powiązań do funkcji

Funkcję można połączyć z innymi usługami przy użyciu powiązań wejściowych lub wyjściowych. Dodaj powiązanie, dodając jego określone definicje do funkcji. Aby dowiedzieć się, jak to zrobić, zobacz [Dodawanie powiązań do istniejącej funkcji w Azure Functions](add-bindings-existing-function.md).  

## <a name="supported-bindings"></a>Obsługiwane powiązania

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Aby uzyskać informacje o tym, które powiązania są w wersji zapoznawczej lub zostały zatwierdzone do użycia w środowisku produkcyjnym, zobacz [Obsługiwane języki](supported-languages.md).

## <a name="bindings-code-examples"></a>Przykłady kodu powiązań

Skorzystaj z poniższej tabeli, aby znaleźć przykłady określonych typów powiązań, które pokazują, jak pracować z powiązaniami w funkcjach. Najpierw wybierz kartę języka odpowiadającą projektowi. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>Powiązania niestandardowe

Można tworzyć niestandardowe powiązania danych wejściowych i wyjściowych. Powiązania muszą być authored in .NET, ale mogą być używane z dowolnego obsługiwanego języka. Aby uzyskać więcej informacji na temat tworzenia powiązań niestandardowych, zobacz [Tworzenie niestandardowych powiązań wejściowych i wyjściowych](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings).

## <a name="resources"></a>Zasoby
- [Wyrażenia i wzorce powiązania](./functions-bindings-expressions-patterns.md)
- [Używanie wartości zwracanej funkcji platformy Azure](./functions-bindings-return-value.md)
- [Jak zarejestrować wyrażenie powiązania](./functions-bindings-register.md)
- Testowania:
  - [Strategie testowania kodu w usłudze Azure Functions](functions-test-a-function.md)
  - [Ręczne uruchamianie funkcji niewyzwalanej przez protokół HTTP](functions-manually-run-non-http.md)
- [Obsługa błędów powiązań](./functions-bindings-errors.md)

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Rejestrowanie Azure Functions powiązań](./functions-bindings-register.md)
