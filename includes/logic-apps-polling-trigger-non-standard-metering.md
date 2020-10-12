---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74793448"
---
Aby oszacować dokładniejsze koszty zużycia, należy wziąć pod uwagę potencjalną liczbę komunikatów lub zdarzeń, które mogą się pojawić w danym dniu, zamiast wykonywać obliczenia tylko na podstawie interwału sondowania. Gdy zdarzenie lub komunikat spełnia kryteria wyzwalacza, wiele wyzwalaczy natychmiast spróbuje odczytać wszystkie inne oczekujące zdarzenia lub komunikaty, które spełniają kryteria. To zachowanie oznacza, że nawet w przypadku wybrania dłuższego interwału sondowania uruchamiany jest wyzwalacz oparty na liczbie oczekujących zdarzeń lub komunikatów, które kwalifikują się do uruchomienia przepływów pracy. Wyzwalacze zgodne z tym zachowaniem obejmują Azure Service Bus i centrum zdarzeń platformy Azure.

Załóżmy na przykład, że ustawisz wyzwalacz, który sprawdza punkt końcowy codziennie. Gdy wyzwalacz sprawdza punkt końcowy i znajdzie 15 zdarzeń, które spełniają kryteria, wyzwalacz wyzwala i uruchamia odpowiedni przepływ pracy 15 razy. Logic Apps Gazomierze wszystkie akcje wykonywane przez te 15 przepływów pracy, w tym żądania wyzwalacza. Aby obliczyć potencjalne koszty, wypróbuj [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/).