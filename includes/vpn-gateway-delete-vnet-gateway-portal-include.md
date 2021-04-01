---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 91dc6ba0bcd455aefe9de2efdff2feb20938152d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376393"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Krok 1. przechodzenie do bramy sieci wirtualnej

1. W [Azure Portal](https://portal.azure.com)przejdź do **wszystkich zasobów**. 
2. Aby otworzyć stronę Brama sieci wirtualnej, przejdź do bramy sieci wirtualnej i kliknij, aby ją zaznaczyć. 

### <a name="step-2-delete-connections"></a>Krok 2. usuwanie połączeń

1. Na stronie bramy sieci wirtualnej kliknij pozycję **połączenia** , aby wyświetlić wszystkie połączenia z bramą.
2. Kliknij **"..."** w wierszu nazwy połączenia, a następnie wybierz pozycję **Usuń** z listy rozwijanej.
3. Kliknij przycisk **tak** , aby potwierdzić, że chcesz usunąć połączenie. Jeśli masz wiele połączeń, Usuń każde połączenie.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Krok 3. Usuwanie bramy sieci wirtualnej

Należy pamiętać, że w przypadku konfiguracji P2S dla tej sieci wirtualnej oprócz konfiguracji S2S, usunięcie bramy sieci wirtualnej spowoduje automatyczne odłączenie wszystkich klientów P2S bez ostrzeżenia.

1. Na stronie Brama sieci wirtualnej kliknij pozycję **Przegląd**.
2. Na stronie **Przegląd** kliknij przycisk **Usuń** , aby usunąć bramę.
