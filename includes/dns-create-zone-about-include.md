---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 74031a8dbc9b64d6a09533789eed1296ff334d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183892"
---
Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS.

Na przykład domena „contoso.com” może zawierać wiele rekordów DNS, takich jak „mail.contoso.com” (dla serwera poczty) i „www.contoso.com” (dla witryny sieci Web).

Podczas tworzenia strefy DNS w usłudze Azure DNS należy uwzględnić następujące kwestie:

* Nazwa strefy musi być unikatowa w obrębie grupy zasobów, a strefa nie może wcześniej istnieć. W przeciwnym razie operacja zakończy się niepowodzeniem.
* Danej nazwy strefy można użyć ponownie w innej grupie zasobów lub w ramach innej subskrypcji platformy Azure.
* W przypadku wielu stref o tej samej nazwie do każdego wystąpienia jest przypisywany inny adres serwera nazw. W rejestratorze nazw domen można skonfigurować tylko jeden zestaw adresów.

> [!NOTE]
> Nie musisz być właścicielem nazwy domeny, aby utworzyć strefę DNS z tą nazwą domeny w usłudze Azure DNS. Jednak musisz być właścicielem domeny, aby skonfigurować serwery nazw usługi Azure DNS jako poprawne serwery nazw dla nazwy domeny w rejestratorze nazw domen.
> 
> Aby uzyskać więcej informacji, zobacz [delegowanie domeny do Azure DNS](../articles/dns/dns-domain-delegation.md).