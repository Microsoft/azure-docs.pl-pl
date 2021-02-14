---
title: Samouczek — usuwanie chmury prywatnej rozwiązania Azure VMware
description: Dowiedz się, jak usunąć chmurę prywatną rozwiązania VMware firmy Azure, która nie jest już potrzebna.
ms.topic: tutorial
ms.date: 02/09/2021
ms.openlocfilehash: b11b8f902691db4bd71fd3f52aaa67d46efea643
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101710"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Samouczek: usuwanie chmury prywatnej rozwiązania Azure VMware

Jeśli masz chmurę prywatną rozwiązania VMware platformy Azure, która nie jest już potrzebna, możesz ją usunąć. Chmura prywatna obejmuje izolowaną domenę sieciową, co najmniej jeden inicjowany klaster vSphere na dedykowanych hostach serwerów oraz kilka maszyn wirtualnych. Po usunięciu chmury prywatnej zostaną usunięte wszystkie maszyny wirtualne, ich dane i klastry. Dedykowane hosty są bezpiecznie czyszczone i zwracane do puli bezpłatna. Domena sieciowa obsługiwana dla klienta również jest usuwana.  

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
