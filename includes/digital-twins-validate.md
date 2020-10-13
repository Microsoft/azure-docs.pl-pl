---
author: baanders
description: Dołącz plik opisujący sposób weryfikacji modeli bliźniaczych reprezentacji cyfrowych platformy Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 0146660fa50f9a136cd82483200ca2e6c6cc0b89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87985884"
---
> [!TIP]
> Po utworzeniu modelu zaleca się zweryfikowanie modeli w trybie offline przed przekazaniem ich do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Dostępny jest przykład niezależny od języka do sprawdzania poprawności dokumentów modelu, aby upewnić się, że DTDL jest poprawna. Znajduje się tutaj: [**DTDL moduł sprawdzania poprawności**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

Przykład modułu sprawdzania DTDL jest oparty na bibliotece analizatora oprogramowania .NET DTDL, która jest dostępna w NuGet jako biblioteka po stronie klienta: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Możesz również bezpośrednio korzystać z biblioteki, aby zaprojektować własne rozwiązanie do sprawdzania poprawności. W przypadku korzystania z biblioteki parserów upewnij się, że używasz wersji zgodnej z wersją, którą obsługuje usługa Azure Digital bliźniaczych reprezentacji. W trakcie okresu zapoznawczego jest to wersja *3.7.0*.

Więcej informacji na temat przykładowego modułu sprawdzania poprawności i biblioteki parsera, w tym przykłady użycia, można znaleźć w temacie [*How to: Parse and Validate models*](../articles/digital-twins/how-to-parse-models.md).