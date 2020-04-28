---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67182982"
---
**1. w jaki sposób klienci będą powiadamiani o wycofaniu zestawu SDK?**

Firma Microsoft zapewni 12-miesięczne powiadomienie z wyprzedzeniem dotyczące końca obsługi zestawu SDK w celu ułatwienia przejścia do obsługiwanego zestawu SDK. Dodatkowo klienci będą powiadamiani za pośrednictwem różnych kanałów komunikacji — portal zarządzania platformy Azure, Centrum deweloperów, wpis w blogu i bezpośrednią komunikację z przypisanymi administratorami usługi.

**2. czy klienci mogą tworzyć aplikacje przy użyciu "do" Azure Cosmos DB zestawu SDK w okresie 12-miesięcznym?** 

Tak. klienci będą mieć pełny dostęp do tworzenia, wdrażania i modyfikowania aplikacji przy użyciu zestawu SDK Azure Cosmos DB wycofanych w ciągu 12-miesięcznego okresu prolongaty. W ciągu 12-miesięcznego okresu prolongaty klienci są zachęcani do migracji do nowszej obsługiwanej wersji zestawu SDK programu Azure Cosmos DB, zgodnie z potrzebami.

**3. czy klienci mogą tworzyć i modyfikować aplikacje przy użyciu wycofanego Azure Cosmos DB zestawu SDK po upływie 12-miesięcznego okresu powiadomień?**

Po upływie 12-miesięcznego okresu powiadomień zestaw SDK zostanie wycofany. Każdy dostęp do Azure Cosmos DB przez aplikacje korzystające z wycofanego zestawu SDK nie będzie dozwolony przez platformę Azure Cosmos DB. Ponadto firma Microsoft nie będzie świadczyć pomocy technicznej dotyczącej wycofanego zestawu SDK.

**4. co się stanie z aplikacjami uruchomionymi przez klienta, które używają nieobsługiwanej wersji Azure Cosmos DB SDK?**

Wszystkie próby nawiązania połączenia z usługą Azure Cosmos DB z wycofaną wersją zestawu SDK zostaną odrzucone. 

**5. czy nowe funkcje i funkcje zostaną zastosowane do wszystkich niewycofanych zestawów SDK?**

Nowe funkcje i funkcje zostaną dodane tylko do nowych wersji. Jeśli używasz starej, niewycofanej wersji zestawu SDK, żądania do Azure Cosmos DB będą nadal działać jak wcześniej, ale nie będziesz mieć dostępu do żadnych nowych funkcji.  

**6. co należy zrobić, jeśli nie mogę zaktualizować aplikacji przed datą wycięcia?**

Zalecamy przeprowadzenie uaktualnienia do najnowszego zestawu SDK tak szybko, jak to możliwe. Po oznakowaniu zestawu SDK dla wycofania będzie można zaktualizować aplikację za 12 miesięcy. Jeśli z jakiegoś powodu nie można ukończyć aktualizacji aplikacji w tym przedziale czasu, skontaktuj się z [zespołem Cosmos DB](mailto:askcosmosdb@microsoft.com) i poproś o pomoc przed datą końcową.

