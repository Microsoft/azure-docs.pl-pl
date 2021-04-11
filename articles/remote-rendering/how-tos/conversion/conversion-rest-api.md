---
title: Interfejs API REST konwersji zasobów
description: Opisuje sposób konwersji elementu zawartości za pomocą interfejsu API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e3db4b9c9b4a05142f1327f681b067748cb1a2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951644"
---
# <a name="use-the-model-conversion-rest-api"></a>Używanie interfejsu API REST konwersji modelu

Usługa [konwersji modelu](model-conversion.md) jest kontrolowana za pomocą [interfejsu API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Ten interfejs API może służyć do tworzenia konwersji, uzyskiwania właściwości konwersji i wyświetlania listy istniejących konwersji.

## <a name="rest-api-reference"></a>Dokumentacja interfejsu API REST

Dokumentację referencyjną interfejsu API REST renderowania zdalnego można znaleźć [tutaj](/rest/api/mixedreality/2021-01-01/remoterendering)i [tutaj](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).

Udostępniamy skrypt programu PowerShell w [repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) w folderze *scripts* o nazwie *Conversion.ps1*, który demonstruje korzystanie z naszej usługi. Skrypt i jego konfigurację są opisane tutaj: [przykładowe skrypty programu PowerShell](../../samples/powershell-example-scripts.md). Udostępniamy również zestawy SDK dla [platformy .NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) i środowiska [Java]( https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md).

## <a name="next-steps"></a>Następne kroki

- [Używanie usługi Azure Blob Storage do konwersji modelu](blob-storage.md)
- [Konwersja modelu](model-conversion.md)