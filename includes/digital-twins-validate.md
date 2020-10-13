---
author: baanders
description: Dołącz plik opisujący sposób weryfikacji modeli bliźniaczych reprezentacji cyfrowych platformy Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: fc7c31d1ab82e2e7edc74fb57233e8a0891fd113
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91974972"
---
> [!TIP]
> Po utworzeniu modelu zaleca się zweryfikowanie modeli w trybie offline przed przekazaniem ich do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Dostępny jest przykład niezależny od języka do sprawdzania poprawności dokumentów modelu, aby upewnić się, że DTDL jest poprawna. Znajduje się tutaj: [**DTDL moduł sprawdzania poprawności**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

Przykład modułu sprawdzania DTDL jest oparty na bibliotece analizatora oprogramowania .NET DTDL, która jest dostępna w NuGet jako biblioteka po stronie klienta: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Możesz również bezpośrednio korzystać z biblioteki, aby zaprojektować własne rozwiązanie do sprawdzania poprawności. W przypadku korzystania z biblioteki parserów upewnij się, że używasz wersji zgodnej z wersją, którą obsługuje usługa Azure Digital bliźniaczych reprezentacji. W trakcie okresu zapoznawczego jest to wersja *3.12.4*.

Więcej informacji na temat przykładowego modułu sprawdzania poprawności i biblioteki parsera, w tym przykłady użycia, można znaleźć w temacie [*How to: Parse and Validate models*](../articles/digital-twins/how-to-parse-models.md).