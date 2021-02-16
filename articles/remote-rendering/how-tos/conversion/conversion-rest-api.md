---
title: Interfejs API REST konwersji zasobów
description: Opisuje sposób konwersji elementu zawartości za pomocą interfejsu API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 4f8ac72e2b598a6c7631d691cc1bfb82cdba7599
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530267"
---
# <a name="use-the-model-conversion-rest-api"></a>Używanie interfejsu API REST konwersji modelu

Usługa [konwersji modelu](model-conversion.md) jest kontrolowana za pomocą [interfejsu API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Ten interfejs API może służyć do tworzenia konwersji, uzyskiwania właściwości konwersji i wyświetlania listy istniejących konwersji.

## <a name="rest-api-reference"></a>Dokumentacja interfejsu API REST

Dokumentację referencyjną interfejsu API REST renderowania zdalnego można znaleźć [tutaj](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering)i [tutaj](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).

Udostępniamy skrypt programu PowerShell w [repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) w folderze *scripts* o nazwie *Conversion.ps1*, który demonstruje korzystanie z naszej usługi. Skrypt i jego konfigurację są opisane tutaj: [przykładowe skrypty programu PowerShell](../../samples/powershell-example-scripts.md). Udostępniamy również zestawy SDK dla [platform .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java i Python.

## <a name="next-steps"></a>Następne kroki

- [Używanie usługi Azure Blob Storage do konwersji modelu](blob-storage.md)
- [Konwersja modelu](model-conversion.md)
