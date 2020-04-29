---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67182952"
---
## <a name="pause-reset-or-stop-the-session"></a>Wstrzymywanie, Resetowanie lub zatrzymywanie sesji

Aby zatrzymać sesję tymczasowo, można wywołać `Stop()`. Wykonanie tej czynności spowoduje zatrzymanie wszelkich obserwatorów i przetwarzania środowiska, nawet w przypadku wywołania ProcessFrame (). Następnie można wywołać `Start()` , aby wznowić przetwarzanie. Podczas wznawiania są zachowywane dane środowiska już przechwycone w sesji.
