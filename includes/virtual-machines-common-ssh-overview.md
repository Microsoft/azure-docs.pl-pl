---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "71168632"
---
## <a name="overview-of-ssh-and-keys"></a>Omówienie protokołów SSH i kluczy

[SSH](https://www.ssh.com/ssh/) to protokół połączenia szyfrowanego, który umożliwia bezpieczne logowanie za pośrednictwem niezabezpieczonych połączeń. SSH to domyślny protokół połączeń dla maszyn wirtualnych z systemem Linux hostowanych na platformie Azure. Mimo że protokół SSH zapewnia szyfrowane połączenie, używanie haseł z połączeniami SSH nadal pozostawia maszynę wirtualną narażoną na ataki lub zgadywanie haseł. Bezpieczniejsza i preferowana metoda łączenia się z maszyną wirtualną przy użyciu protokołu SSH polega na użyciu pary kluczy publiczny-prywatny, znanej również jako *klucze SSH*. 

* *Klucz publiczny* jest umieszczany na maszynie wirtualnej z systemem Linux lub dowolnej innej usłudze, która ma być używana z kryptografią klucza publicznego.

* *Klucz prywatny* pozostaje w systemie lokalnym. Klucz prywatny należy chronić. Nie należy go udostępniać.

W przypadku korzystania z klienta SSH do nawiązywania połączenia z maszyną wirtualną z systemem Linux (która ma klucz publiczny) zdalna maszyna wirtualna testuje klienta, aby upewnić się, że dysponuje kluczem prywatnym. Jeśli klient ma klucz prywatny, zostanie mu udzielony dostęp do maszyny wirtualnej. 

W zależności od zasad zabezpieczeń organizacji można użyć pojedynczej pary kluczy publiczny-prywatny, aby uzyskać dostęp do wielu maszyn wirtualnych i usług platformy Azure. Nie potrzebujesz oddzielnej pary kluczy dla każdej maszyny wirtualnej lub usługi, do której chcesz uzyskać dostęp. 

Klucz publiczny może być współużytkowany z każdą osobą, ale tylko ty (lub Twoja lokalna infrastruktura zabezpieczeń) powinna mieć swój klucz prywatny.