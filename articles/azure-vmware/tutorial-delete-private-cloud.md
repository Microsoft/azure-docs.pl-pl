---
title: Samouczek — usuwanie chmury prywatnej rozwiązania Azure VMware
description: Dowiedz się, jak usunąć chmurę prywatną rozwiązania VMware firmy Azure, która nie jest już potrzebna.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 453e7a3316c342cd724a951eafea0ae9fa045506
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462103"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Samouczek: usuwanie chmury prywatnej rozwiązania Azure VMware

Jeśli masz chmurę prywatną rozwiązania VMware platformy Azure, która nie jest już potrzebna, możesz ją usunąć. Chmura prywatna obejmuje izolowaną domenę sieciową, co najmniej jeden inicjowany klaster vSphere na dedykowanych hostach serwerów oraz kilka maszyn wirtualnych. Po usunięciu chmury prywatnej zostaną usunięte wszystkie maszyny wirtualne, ich dane i klastry. Dedykowane hosty rozwiązań VMware platformy Azure są bezpiecznie czyszczone i zwracane do puli bezpłatna. Zajęta przestrzeń adresowa sieci również jest usuwana.  

> [!CAUTION]
> Usuwanie chmury prywatnej jest operacją nieodwracalną. Po usunięciu chmury prywatnej dane nie mogą zostać odzyskane, ponieważ przerywa wszystkie uruchomione obciążenia i składniki oraz niszczy wszystkie dane chmury prywatnej i ustawienia konfiguracji, w tym publiczne adresy IP.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli wymagane są maszyny wirtualne i ich dane później, przed usunięciem chmury prywatnej należy wykonać kopię zapasową danych.  Nie ma możliwości odzyskania maszyn wirtualnych i ich danych.


## <a name="delete-the-private-cloud"></a>Usuń chmurę prywatną

1. Dostęp do konsoli rozwiązań VMware platformy Azure znajduje się w [Azure Portal](https://portal.azure.com).

2. Wybierz chmurę prywatną, która ma zostać usunięta.
 
3. Wprowadź nazwę chmury prywatnej i wybierz opcję **tak**. 

>[!NOTE]
>Proces usuwania trwa kilka godzin.  
