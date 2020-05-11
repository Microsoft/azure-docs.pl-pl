---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006487"
---
## <a name="setting-up-the-library"></a>Konfigurowanie biblioteki

Wywołaj `Start()` , aby umożliwić sesji przetwarzania danych środowiska.

Aby obsłużyć zdarzenia zgłoszone przez sesję, `delegate` należy ustawić właściwość sesji na obiekt, taki jak widok. Ten obiekt musi implementować `SSCCloudSpatialAnchorSessionDelegate` protokół.
