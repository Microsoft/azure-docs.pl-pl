---
title: Omówienie tagów usługi Zapora platformy Azure
description: Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74168693"
---
# <a name="azure-firewall-service-tags"></a>Tagi usługi Zapora platformy Azure

Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić adresów IP uwzględnionych w tagu. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie.

Tagów usługi Zapora platformy Azure można używać w polu docelowym reguły sieciowe. Można ich użyć zamiast konkretnych adresów IP.

## <a name="supported-service-tags"></a>Obsługiwane Tagi usług

Zobacz [grupy zabezpieczeń](../virtual-network/security-overview.md#service-tags) , aby uzyskać listę tagów usługi dostępnych do użycia w regułach sieci zapory platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat reguł zapory platformy Azure, zobacz [logika przetwarzania reguł zapory platformy Azure](rule-processing.md).