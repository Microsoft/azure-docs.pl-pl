---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183515"
---
Usługa i typ subskrypcji decydują o liczbie zapytań, które możesz wykonywać na sekundę. Upewnij się, że Twoja aplikacja zawiera logikę niezbędną do nieprzekraczania tego limitu przydziału. W przypadku osiągnięcia lub przekroczenia limitu liczby zapytań na sekundę żądanie kończy się niepowodzeniem i jest zwracany kod stanu HTTP 429. Odpowiedź zawiera nagłówek `Retry-After`, która wskazuje, jak długo należy zaczekać przed wysłaniem kolejnego żądania.

## <a name="denial-of-service-versus-throttling"></a>Odmowa usługi a ograniczanie przepustowości

Usługa odróżnia atak typu „odmowa usługi” (DoS) od naruszenia liczby zapytań na sekundę. Jeśli usługa podejrzewa atak DoS, żądanie kończy się powodzeniem (kod stanu HTTP to 200 OK). Treść odpowiedzi jest jednak pusta.