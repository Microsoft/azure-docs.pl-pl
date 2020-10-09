---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006487"
---
## <a name="setting-up-the-library"></a>Konfigurowanie biblioteki

Wywołaj `Start()` , aby umożliwić sesji przetwarzania danych środowiska.

Aby obsłużyć zdarzenia zgłoszone przez sesję, należy ustawić `delegate` Właściwość sesji na obiekt, taki jak widok. Ten obiekt musi implementować `SSCCloudSpatialAnchorSessionDelegate` protokół.
