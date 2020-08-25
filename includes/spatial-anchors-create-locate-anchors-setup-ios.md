---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "83006487"
---
## <a name="setting-up-the-library"></a>Konfigurowanie biblioteki

Wywołaj `Start()` , aby umożliwić sesji przetwarzania danych środowiska.

Aby obsłużyć zdarzenia zgłoszone przez sesję, należy ustawić `delegate` Właściwość sesji na obiekt, taki jak widok. Ten obiekt musi implementować `SSCCloudSpatialAnchorSessionDelegate` protokół.
