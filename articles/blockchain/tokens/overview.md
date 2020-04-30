---
title: Co to są tokeny usługi Azure łańcucha bloków
description: Tokeny łańcucha bloków platformy Azure to platforma jako usługa (PaaS) do wystawiania tokenów i zarządzania nimi.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: e7c106ffee8ea06c7c0ce738aa8be0aba96a38ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79240282"
---
# <a name="what-is-azure-blockchain-tokens"></a>Co to są tokeny usługi Azure Blockchain?

[!INCLUDE [Preview note](./includes/preview.md)]

Tokeny łańcucha bloków platformy Azure to platforma jako usługa (PaaS) służąca do standaryzacji wystawiania tokenów i zarządzania nimi w różnych księgach łańcucha bloków na platformie Azure.

Za pomocą tokenów łańcucha bloków platformy Azure można tworzyć standardowe tokeny dla rozwiązania łańcucha bloków przy użyciu wstępnie skompilowanego szablonu tokenu. Możesz również utworzyć własny szablon tokenu za pomocą usługi. Po utworzeniu Użyj tokenów usługi Azure łańcucha bloków, aby nawiązać połączenie i wydać tokeny w łańcucha bloków. Po wydaniu można następnie zarządzać tokenami w wielu sieciach łańcucha bloków.

## <a name="templates"></a>Szablony

Użyj tokenów usługi Azure łańcucha bloków, aby wybrać szablon wstępnie skompilowanego tokenu lub utworzyć własny szablon tokenu. Tokeny usługi Azure łańcucha bloków obsługuje możliwość tworzenia szablonów tokenów, które umożliwiają tworzenie własnego szablonu tokenu na podstawie obsługiwanych zachowań. Szablonów tokenów można używać w przypadku większości rozwiązań łańcucha bloków, ponieważ są one mapowane na najczęściej używane tokeny. Możesz zacząć od szablonu, personalizować go i wdrażać tokeny dla rozwiązania.

Aby uzyskać więcej informacji na temat szablonów tokenów usługi Azure łańcucha bloków, zobacz [Szablony tokenów usługi Azure łańcucha bloków](templates.md).

## <a name="management"></a>Zarządzanie

Tokeny usługi Azure łańcucha bloków zapewniają zarządzanie Azure Portal i interfejsy API w celu nawiązania połączenia z istniejącą siecią łańcucha bloków. Obecnie można nawiązać połączenie z [usługą Azure łańcucha bloków](../service/overview.md) lub z inną rodziną Ethereum łańcucha bloków.

Po nawiązaniu połączenia z jedną lub wieloma sieciami łańcucha bloków można używać interfejsów API tokenów usługi Azure łańcucha bloków do wystawiania tokenów i zarządzania nimi do użycia w rozwiązaniu łańcucha bloków. Korzystając z interfejsów API, można zintegrować zarządzanie tokenami w aplikacjach i logice biznesowej. Można na przykład użyć interfejsu API REST do zarządzania tokenami zamiast zarządzać tokenami bezpośrednio w łańcucha bloków.

## <a name="blockchains-and-accounts"></a>Blockchains i konta

Tokeny usługi Azure łańcucha bloków zapewniają zarządzanie Azure Portal i interfejsy API w celu tworzenia nowych grup i nowych kont łańcucha bloków w połączonych sieciach łańcucha bloków. Nowe konta można utworzyć bezpośrednio w połączonych sieciach, a tokeny usługi Azure łańcucha bloków zarządza kluczami prywatnymi konta w Twoim imieniu. Korzystając z grup, można grupować różne konta łańcucha bloków z wielu sieci i zarządzać kontrolą dostępu za pośrednictwem grup.

Aby uzyskać więcej informacji na temat zarządzania kontami usługi Azure łańcucha bloków, zobacz [Zarządzanie kontami tokenów usługi Azure łańcucha bloków](account-management.md).

## <a name="token-taxonomy-framework"></a>Struktura taksonomii tokenów

Tokeny łańcucha bloków platformy Azure są oparte na opartych na standardach podstawach o nazwie platforma taksonomii tokenów (TTF). TTF to zestaw elementów dostarczanych utworzonych na podstawie grupy roboczej tokenu [taksonomii TTI (tokeny](https://entethalliance.org/participate/token-taxonomy-initiative/) ). Grupa robocza TTI definiuje taksonomię biznesową dla tokenów i ich zachowań, które mogą być stosowane do wszystkich głównych ksiąg, w tym Ethereum, kworum, przewodów i sieci szkieletowej. Celem grupy roboczej jest utworzenie struktury, która ma na celu ujednolicenie użycia tokenów z perspektywy biznesowej w celu utworzenia rozwiązań opartych na programie upraszczanie i zdemokratyzuj proces. Dzięki temu, że branża definiuje te tokeny i ich zachowanie na poziomie firmy, szczegółowa implementacja tokenów jest oddzielna od logiki biznesowej, która operuje na tokenach.

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

W przypadku usługi Azure łańcucha bloków News odwiedź [blog usługi Azure łańcucha bloków](https://azure.microsoft.com/blog/topics/blockchain/) , aby zachować aktualność w zakresie usług łańcucha bloków i uzyskać informacje od zespołu inżynierów ds. platformy Azure łańcucha bloków.

Aby uzyskać dostęp do wersji zapoznawczej usługi Azure łańcucha bloków, [skontaktuj się z zespołem usługi Azure łańcucha bloków Tokens](https://aka.ms/PreviewForm).

Aby przekazać opinie o produkcie lub zażądać nowych funkcji, Opublikuj głos na pomysł za pośrednictwem [forum opinii platformy Azure dla usługi łańcucha bloków](https://aka.ms/blockchainuservoice).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o dostępnych [szablonach tokenów usługi Azure łańcucha bloków](templates.md).
