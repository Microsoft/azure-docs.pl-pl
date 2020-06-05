---
title: Wersje, poprawki, &, uaktualnienia w ramach usługi Azure łańcucha bloków
description: Przegląd obsługiwanych wersji księgi w usłudze Azure łańcucha bloków Service, w tym zasady dotyczące poprawek systemów i uaktualnień zarządzanych przez użytkowników oraz zarządzanych przez użytkownika.
ms.date: 06/02/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: da7907c2c324932ba43863c1074cdff7d54d3827
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84430752"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Obsługiwane wersje finansów usługi Azure łańcucha bloków Service

Usługa Azure łańcucha bloków korzysta z opartej na Ethereum księgi [kworum](https://www.goquorum.com/developers) przeznaczonej do przetwarzania transakcji prywatnych w grupie znanych uczestników, identyfikowanej jako konsorcjum w usłudze Azure łańcucha bloków.

Obecnie usługa Azure łańcucha bloków obsługuje [kworum w wersji 2.5.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.5.0) i [Tessera Transaction Manager](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami

Przechowywanie wersji w kworum odbywa się poprzez wersje główne, pomocnicze i poprawki. Na przykład jeśli wersja kworum to 2.0.1, typ wydania zostanie skategoryzowany w następujący sposób:

|Duży | Mały  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Usługa Azure łańcucha bloków automatycznie aktualizuje wersje poprawki kworum do istniejących uruchomionych członków w ciągu 30 dni od udostępnienia kworum.

## <a name="availability-of-new-ledger-versions"></a>Dostępność nowych wersji księgi

Usługa Azure łańcucha bloków oferuje najnowsze i pomocnicze wersje księgi kworum w ciągu 60 dni od producenta kworum. Dla konsorcjów, które mogą wybierać z usług w przypadku aprowizacji nowych członków i konsorcjum, udostępniane są maksymalnie cztery pomocnicze wersje. Uaktualnianie z programu do wersji głównej lub pomocniczej nie jest obecnie obsługiwane. Na przykład jeśli korzystasz z wersji 2. x, uaktualnienie do wersji 3. x nie jest obecnie obsługiwane. Podobnie, jeśli korzystasz z wersji 2,2, uaktualnienie do wersji 2,3 nie jest obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki

[Limity w usłudze Azure łańcucha bloków Service](limits.md)
