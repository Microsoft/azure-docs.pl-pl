---
author: baanders
description: Dołącz plik opisujący sposób weryfikacji modeli bliźniaczych reprezentacji cyfrowych platformy Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: da42f847bdd57c4dae0cde673c9c9e08d51a758d
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998310"
---
> [!TIP]
> Po utworzeniu modelu zaleca się zweryfikowanie modeli w trybie offline przed przekazaniem ich do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Dostępny jest przykład niezależny od języka do sprawdzania poprawności dokumentów modelu, aby upewnić się, że DTDL jest poprawny przed przekazaniem go do wystąpienia. Znajduje się tutaj: [**DTDL moduł sprawdzania poprawności**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

* Przykład modułu sprawdzania DTDL jest oparty na bibliotece analizatora oprogramowania .NET DTDL, która jest dostępna w NuGet jako biblioteka po stronie klienta: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Możesz również bezpośrednio korzystać z biblioteki, aby zaprojektować własne rozwiązanie do sprawdzania poprawności. W przypadku korzystania z biblioteki parserów upewnij się, że używasz wersji zgodnej z wersją, którą obsługuje usługa Azure Digital bliźniaczych reprezentacji. W trakcie okresu zapoznawczego jest to wersja *3.12.4*.

Więcej informacji na temat przykładowego modułu sprawdzania poprawności i biblioteki parsera, w tym przykłady użycia, można znaleźć w temacie [*How to: Parse and Validate models*](../articles/digital-twins/how-to-parse-models.md).