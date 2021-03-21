---
title: Omówienie tagów usługi Zapora platformy Azure
description: Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 83e9a96573bbc72e0afff61cc0f151f95b081e30
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97031583"
---
# <a name="azure-firewall-service-tags"></a>Tagi usługi Zapora platformy Azure

Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić adresów IP uwzględnionych w tagu. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie.

Tagów usługi Zapora platformy Azure można używać w polu docelowym reguły sieciowe. Można ich użyć zamiast konkretnych adresów IP.

## <a name="supported-service-tags"></a>Obsługiwane Tagi usług

Listę tagów usługi, które są dostępne do użycia w regułach sieci zapory platformy Azure, można znaleźć w temacie [Tagi usługi sieci wirtualnej](../virtual-network/service-tags-overview.md#available-service-tags) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat reguł zapory platformy Azure, zobacz [logika przetwarzania reguł zapory platformy Azure](rule-processing.md).
