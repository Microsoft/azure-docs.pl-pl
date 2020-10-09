---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67182933"
---
Podobnie jak w przypadku tokenów dostępu, jeśli token usługi Azure AD nie jest ustawiony, należy obsłużyć zdarzenie TokenRequired lub zaimplementować metodę tokenRequired w protokole delegowanym.

Możesz obsłużyć zdarzenie synchronicznie, ustawiając właściwość dla argumentów zdarzenia.
