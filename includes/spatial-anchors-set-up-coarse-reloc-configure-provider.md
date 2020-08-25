---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: a3db5535e65e036ab10c35b7bc066a6286a9cad7
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "76545221"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>Konfigurowanie dostawcy odcisku palca czujnika

Zaczniemy od utworzenia i skonfigurowania dostawcy odcisku palca czujnika. Dostawca odcisków palca czujnika będzie miał ostrożne odczytywanie czujników specyficznych dla platformy na urządzeniu i konwertowanie ich odczytów na wspólną reprezentację używaną przez sesję zakotwiczenia w chmurze.