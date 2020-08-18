---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 5c5ed4e1bbe54c642202c19e1beb61de94b4f751
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515299"
---
**1. w jaki sposób klienci będą powiadamiani o wycofaniu zestawu SDK?**

Firma Microsoft zapewni 12-miesięczne powiadomienie z góry, aby uzyskać pomoc techniczną dotyczącą wycofywanego zestawu SDK w celu ułatwienia przejścia do obsługiwanego zestawu SDK. Dodatkowo klienci będą powiadamiani za pośrednictwem różnych kanałów komunikacji — Azure Portal, aktualizacji platformy Azure i komunikacji bezpośredniej do przypisanych administratorów usługi.

**2. czy klienci mogą tworzyć aplikacje przy użyciu "do" Azure Cosmos DB zestawu SDK w okresie 12-miesięcznym?** 

Tak. klienci będą mieć pełny dostęp do tworzenia, wdrażania i modyfikowania aplikacji przy użyciu zestawu SDK, który zostanie wycofany Azure Cosmos DB w okresie 12-miesięcznym. W ciągu 12-miesięcznego okresu wypowiedzenia klientowi zaleca się migrację do nowszej obsługiwanej wersji programu Azure Cosmos DB SDK stosownie do potrzeb. 

**3. po dacie wycofania, co się dzieje z aplikacjami korzystającymi z nieobsługiwanego zestawu SDK Azure Cosmos DB?** 

Po dacie wycofania Azure Cosmos DB nie spowoduje już poprawek błędów, dodania nowych funkcji i zapewnienia pomocy technicznej dla wycofanych wersji zestawu SDK. Jeśli wolisz nie aktualizować, żądania wysyłane z wycofywanych wersji zestawu SDK będą nadal obsługiwane przez usługę Azure Cosmos DB. 

**4. które wersje zestawu SDK będą mieć najnowsze funkcje i aktualizacje?**

Nowe funkcje i aktualizacje zostaną dodane tylko do najnowszej wersji pomocniczej najnowszej obsługiwanej wersji zestawu SDK. Zalecane jest, aby zawsze używać najnowszej wersji, aby korzystać z nowych funkcji, ulepszeń wydajności i poprawek błędów. Jeśli używasz starej, niewycofanej wersji zestawu SDK, żądania do Azure Cosmos DB będą nadal działać, ale nie będziesz mieć dostępu do żadnych nowych funkcji.  

**5. co mam zrobić, jeśli nie mogę zaktualizować aplikacji przed datą wycięcia?**

Zalecamy przeprowadzenie uaktualnienia do najnowszego zestawu SDK tak szybko, jak to możliwe. Po oznakowaniu zestawu SDK do wycofania, będzie można zaktualizować swoją aplikację przez 12 miesięcy. Jeśli aktualizacja nie jest możliwa, żądania wysyłane z wycofywanych wersji zestawu SDK będą nadal obsługiwane przez Azure Cosmos DB, dzięki czemu uruchomione aplikacje będą nadal działać. Jednak Azure Cosmos DB nie będą już naprawiać błędów, dodawać nowych funkcji i zapewniać wsparcia dla wycofanych wersji zestawu SDK. 

Jeśli masz plan pomocy technicznej i potrzebujesz pomocy technicznej, [skontaktuj się z nami](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , podając bilet pomocy technicznej.
    


