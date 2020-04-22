---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687052"
---
1. Wybierz połączenie komunikacji równorzędnej, które chcesz włączyć dla usługi równorzędnej platformy Azure. Następnie wybierz **...**  >  **Edytuj połączenie**.
    > [!div class="mx-imgBorder"]
    > ![Połączenie komunikacji równorzędnej Edytuj połączenie](../media/setup-direct-modify-editconnection.png)
1. W obszarze **Użyj dla usługi komunikacji**wybierz pozycję **Włączone,** a następnie wybierz pozycję **Zapisz**.
    > [!div class="mx-imgBorder"]
    > ![Połączenie komunikacji równorzędnej Włącz usługę komunikacji równorzędnej](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Na **ekranie Przegląd** zobaczysz szczegóły wdrożenia. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.
    > [!div class="mx-imgBorder"]
    > ![Wdrożenie jest zakończone](../media/setup-direct-modify-overview-deployment-complete.png)

1. W okienku **Zarejestrowane prefiksy** wybierz pozycję **Dodaj zarejestrowany prefiks**.
    > [!div class="mx-imgBorder"]
    > ![Dodaj zarejestrowany prefiks](../media/setup-direct-modify-add-registered-prefix.png)
1. Zarejestruj prefiks, wybierając **nazwę** i **prefiks** oraz wybierając **pozycję Zapisz**.
    > [!div class="mx-imgBorder"]
    >  ![Zarejestruj prefiks](../media/setup-direct-modify-register-a-prefix.png) 

1. Po utworzeniu prefiksu widać go na liście **zarejestrowanych prefiksów**. Wybierz **nazwę** prefiksu, aby wyświetlić więcej szczegółów.
    > [!div class="mx-imgBorder"]
    > ![Zarejestrowane prefiksy i połączenia](../media/setup-direct-modify-registered-prefixes.png)
1. Na zarejestrowanej stronie prefiksu zobaczysz pełne szczegóły, które zawierają **klucz Prefiks** dla każdego prefiksu. Ten klucz musi być dostarczony do klienta przydzielonego tego prefiksu od usługodawcy zewnętrznego dostawcy. Klient może następnie zarejestrować swój prefiks w ramach subskrypcji przy użyciu tego klucza.
    > [!div class="mx-imgBorder"]
    > ![Prefiks z kluczem prefiksu](../media/setup-direct-modify-registered-prefix-detail.png)