---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67182933"
---
Podobnie jak w przypadku tokenów dostępu, jeśli token usługi Azure AD nie jest ustawiony, należy obsłużyć zdarzenie TokenRequired lub zaimplementować metodę tokenRequired w protokole delegowanym.

Możesz obsłużyć zdarzenie synchronicznie, ustawiając właściwość dla argumentów zdarzenia.
