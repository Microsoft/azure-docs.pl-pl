---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016387"
---
## <a name="overview-of-ssh-and-keys"></a>Omówienie protokołów SSH i kluczy

[SSH](https://www.ssh.com/ssh/) jest protokołem szyfrowanego połączenia, który zapewnia bezpieczne logowanie za pośrednictwem niezabezpieczonych połączeń. SSH to domyślny protokół połączeń dla maszyn wirtualnych z systemem Linux hostowanych na platformie Azure. Mimo że protokół SSH zapewnia szyfrowane połączenie, używanie haseł z połączeniami SSH nadal pozostawia maszynę wirtualną zagrożoną atakami na rachunek. Zalecamy łączenie się z maszyną wirtualną za pośrednictwem protokołu SSH przy użyciu pary kluczy publiczny-prywatny, znanych także jako *klucze SSH*. 

- *Klucz publiczny* jest umieszczany na maszynie wirtualnej z systemem Linux.

- *Klucz prywatny* pozostaje w systemie lokalnym. Klucz prywatny należy chronić. Nie należy go udostępniać.

Gdy używasz klienta SSH do nawiązywania połączenia z maszyną wirtualną z systemem Linux (która ma klucz publiczny), zdalna maszyna wirtualna testuje klienta, aby upewnić się, że ma prawidłowy klucz prywatny. Jeśli klient ma klucz prywatny, zostanie mu udzielony dostęp do maszyny wirtualnej. 

W zależności od zasad zabezpieczeń organizacji można użyć pojedynczej pary kluczy publiczny-prywatny, aby uzyskać dostęp do wielu maszyn wirtualnych i usług platformy Azure. Nie potrzebujesz oddzielnej pary kluczy dla każdej maszyny wirtualnej lub usługi, do której chcesz uzyskać dostęp. 

Klucz publiczny może być współużytkowany z każdą osobą, ale tylko ty (lub Twoja lokalna infrastruktura zabezpieczeń) powinna mieć dostęp do Twojego klucza prywatnego.