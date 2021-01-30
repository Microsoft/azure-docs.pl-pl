---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214459"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>Konfigurowanie dostawcy odcisku palca czujnika

Zaczniemy od utworzenia i skonfigurowania dostawcy odcisku palca czujnika. Dostawca odcisków palca czujnika będzie miał ostrożne odczytywanie czujników specyficznych dla platformy na urządzeniu i konwertowanie ich odczytów na wspólną reprezentację używaną przez sesję zakotwiczenia w chmurze.

> [!IMPORTANT]
> [Upewnij](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability) się, że na twojej platformie są dostępne czujniki, które są włączane.