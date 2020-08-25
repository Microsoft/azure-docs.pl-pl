---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "67182933"
---
Podobnie jak w przypadku tokenów dostępu, jeśli token usługi Azure AD nie jest ustawiony, należy obsłużyć zdarzenie TokenRequired lub zaimplementować metodę tokenRequired w protokole delegowanym.

Możesz obsłużyć zdarzenie synchronicznie, ustawiając właściwość dla argumentów zdarzenia.
