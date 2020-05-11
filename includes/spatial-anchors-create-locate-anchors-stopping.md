---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006412"
---
## <a name="pause-reset-or-stop-the-session"></a>Wstrzymywanie, Resetowanie lub zatrzymywanie sesji

Aby zatrzymać sesję tymczasowo, można wywołać `Stop()`. Wykonanie tej czynności spowoduje zatrzymanie wszelkich obserwatorów i przetwarzania środowiska, nawet w przypadku `ProcessFrame()`wywołania. Następnie można wywołać `Start()` , aby wznowić przetwarzanie. Podczas wznawiania są zachowywane dane środowiska już przechwycone w sesji.
