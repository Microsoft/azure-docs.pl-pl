---
title: Dodaj powiązania do istniejącej funkcji w Azure Functions
description: Dowiedz się, jak dodać powiązania do istniejącej funkcji w projekcie Azure Functions.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 23b7b05f9a9f9da5a48511ee555e3b6184a74179
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654155"
---
# <a name="add-bindings-to-an-existing-function-in-azure-functions"></a>Dodaj powiązania do istniejącej funkcji w Azure Functions

Podczas tworzenia funkcji w projekcie zostanie dodany kod wyzwalacza charakterystyczny dla języka z zestawu szablonów wyzwalacza. Jeśli chcesz połączyć funkcję z innymi usługami przy użyciu powiązań wejściowych lub wyjściowych, musisz dodać określone definicje powiązań w funkcji. Aby dowiedzieć się więcej na temat powiązań, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

## <a name="local-development"></a>Programowanie lokalne       

Podczas tworzenia funkcji lokalnie należy zaktualizować kod funkcji, aby dodać powiązania. Używanie Visual Studio Code może ułatwić Dodawanie powiązań do funkcji.  

### <a name="visual-studio-code"></a>Visual Studio Code

Gdy używasz Visual Studio Code do tworzenia funkcji, a funkcja używa function.jsna pliku, rozszerzenie Azure Functions może automatycznie dodać powiązanie do istniejącego function.jsna pliku. Aby dowiedzieć się więcej, zobacz [Dodawanie powiązań wejściowych i wyjściowych](functions-develop-vs-code.md#add-input-and-output-bindings).   

### <a name="manually-add-bindings-based-on-examples"></a>Ręcznie Dodaj powiązania na podstawie przykładów

Podczas dodawania powiązania do istniejącej funkcji należy zaktualizować zarówno kod funkcji, jak i function.jsw pliku konfiguracji, jeśli jest używany przez język. Zarówno Biblioteka klas .NET, jak i funkcje języka Java używają atrybutów zamiast function.jsna, dlatego należy je zaktualizować.

Skorzystaj z poniższej tabeli, aby znaleźć przykłady określonych typów powiązań, których można użyć w celu przeprowadzenia aktualizacji istniejącej funkcji. Najpierw wybierz kartę językową, która odpowiada Twojemu projektowi. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Azure Portal

Podczas tworzenia funkcji w [Azure Portal](https://portal.azure.com)należy dodać powiązania danych wejściowych i wyjściowych na karcie **integracja** dla danej funkcji. Nowe powiązania są dodawane do function.jsw pliku lub do atrybutów metod, w zależności od języka. W poniższych artykułach przedstawiono przykłady dodawania powiązań do istniejącej funkcji w portalu:

+ [Powiązanie danych wyjściowych magazynu kolejki](functions-integrate-storage-queue-output-binding.md)
+ [Azure Cosmos DB powiązanie danych wyjściowych](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>Następne kroki

+ [Pojęcia powiązań i wyzwalaczy usługi Azure Functions](functions-triggers-bindings.md)
