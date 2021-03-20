---
title: Nazwa FQDN infrastruktury dla zapory platformy Azure
description: Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74168979"
---
# <a name="infrastructure-fqdns"></a>Infrastrukturalne nazwy FQDN

Usługa Azure Firewall zawiera wbudowaną kolekcję reguł dla nazw FQDN infrastruktury, które domyślnie są dozwolone. Te nazwy FQDN są specyficzne dla platformy i nie można ich używać do innych celów. 

W wbudowanej kolekcji reguł znajdują się następujące usługi:

- Dostęp obliczeniowy do repozytorium obrazów platformy magazynu (PIR)
- Dostęp do magazynu stanu dysków zarządzanych
- Diagnostyka Azure i rejestrowanie (MDS)

## <a name="overriding"></a>Zastępuje 

Tę wbudowaną kolekcję reguł infrastruktury można zastąpić, tworząc ostatnią kolekcję reguł aplikacji, która jest przetwarzana jako Ostatnia. Zawsze będzie ona przetwarzana przed kolekcją reguł infrastruktury. Wszystko, co nie znajduje się w kolekcji reguł infrastruktury, zostanie odrzucone domyślnie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md).