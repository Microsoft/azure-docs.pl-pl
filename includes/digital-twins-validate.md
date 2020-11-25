---
author: baanders
description: Dołącz plik opisujący sposób weryfikacji modeli bliźniaczych reprezentacji cyfrowych platformy Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 58ae9c187f50bb7eb3f1ccc2a618275885121267
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027380"
---
> [!TIP]
> Po utworzeniu modelu zaleca się zweryfikowanie modeli w trybie offline przed przekazaniem ich do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Dostępny jest przykład niezależny od języka do sprawdzania poprawności dokumentów modelu, aby upewnić się, że DTDL jest poprawny przed przekazaniem go do wystąpienia. Znajduje się tutaj: [**DTDL moduł sprawdzania poprawności**](/samples/azure-samples/dtdl-validator/dtdl-validator).

* Przykład modułu sprawdzania DTDL jest oparty na bibliotece analizatora oprogramowania .NET DTDL, która jest dostępna w NuGet jako biblioteka po stronie klienta: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Możesz również bezpośrednio korzystać z biblioteki, aby zaprojektować własne rozwiązanie do sprawdzania poprawności. W przypadku korzystania z biblioteki parserów upewnij się, że używasz wersji zgodnej z wersją, którą obsługuje usługa Azure Digital bliźniaczych reprezentacji. Obecnie jest to wersja *3.12.4*.

Więcej informacji na temat przykładowego modułu sprawdzania poprawności i biblioteki parsera, w tym przykłady użycia, można znaleźć w temacie [*How to: Parse and Validate models*](../articles/digital-twins/how-to-parse-models.md).