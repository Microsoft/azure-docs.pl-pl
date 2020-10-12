---
title: plik dołączany
titleSuffix: Azure
description: plik dołączany
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81687052"
---
1. Wybierz połączenie komunikacji równorzędnej, które chcesz włączyć dla usługi Komunikacja równorzędna Azure. Następnie wybierz pozycję **...**  >  **Edytuj połączenie**.
    > [!div class="mx-imgBorder"]
    > ![Edytowanie połączenia komunikacji równorzędnej](../media/setup-direct-modify-editconnection.png)
1. W obszarze **Użyj dla usługi Komunikacja równorzędna**wybierz pozycję **włączone** , a następnie wybierz pozycję **Zapisz**.
    > [!div class="mx-imgBorder"]
    > ![Połączenie komunikacji równorzędnej Włącz usługę komunikacji równorzędnej](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Na ekranie **Przegląd** są wyświetlane szczegóły wdrożenia. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.
    > [!div class="mx-imgBorder"]
    > ![Wdrożenie zostało ukończone](../media/setup-direct-modify-overview-deployment-complete.png)

1. W okienku **zarejestrowane prefiksy** wybierz pozycję **Dodaj zarejestrowany prefiks**.
    > [!div class="mx-imgBorder"]
    > ![Dodaj zarejestrowany prefiks](../media/setup-direct-modify-add-registered-prefix.png)
1. Zarejestruj prefiks, wybierając **nazwę** i **prefiks** i wybierając pozycję **Zapisz**.
    > [!div class="mx-imgBorder"]
    >  ![Rejestrowanie prefiksu](../media/setup-direct-modify-register-a-prefix.png) 

1. Po utworzeniu prefiksu zostanie on wyświetlony na liście **zarejestrowanych prefiksów**. Wybierz **nazwę** prefiksu, aby wyświetlić więcej szczegółów.
    > [!div class="mx-imgBorder"]
    > ![Zarejestrowane prefiksy i połączenia](../media/setup-direct-modify-registered-prefixes.png)
1. Na stronie zarejestrowano prefiksu widoczne są pełne szczegóły, w tym **klucz prefiksu** dla każdego prefiksu. Ten klucz musi być podany dla klienta przydzielony ten prefiks od usługodawcy internetowego dostawcy. Klient może następnie zarejestrować swój prefiks w ramach swojej subskrypcji przy użyciu tego klucza.
    > [!div class="mx-imgBorder"]
    > ![Prefiks z kluczem prefiksu](../media/setup-direct-modify-registered-prefix-detail.png)