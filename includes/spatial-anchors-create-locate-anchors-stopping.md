---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006412"
---
## <a name="pause-reset-or-stop-the-session"></a>Wstrzymywanie, Resetowanie lub zatrzymywanie sesji

Aby zatrzymać sesję tymczasowo, można wywołać `Stop()` . Wykonanie tej czynności spowoduje zatrzymanie wszelkich obserwatorów i przetwarzania środowiska, nawet w przypadku wywołania `ProcessFrame()` . Następnie można wywołać, `Start()` Aby wznowić przetwarzanie. Podczas wznawiania są zachowywane dane środowiska już przechwycone w sesji.
