---
title: plik dołączania
description: plik dołączania
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73491445"
---
1. Zlokalizuj utworzoną wirtualną sieć WAN. Na stronie wirtualna sieć WAN w obszarze **łączność** wybierz pozycję **centra**.
2. Na stronie centra wybierz pozycję **+ nowe centrum** , aby otworzyć stronę **Tworzenie wirtualnego centrum** .
3. Na karcie Tworzenie **podstawy** strony **wirtualnego centrum** wykonaj następujące pola:

   ![Podstawy](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Podstawy")

    **Szczegóły projektu**

   * Region (wcześniej nazywany lokalizacją)
   * Nazwa
   * Prywatna przestrzeń adresowa centrum. Minimalną przestrzenią adresową jest/24, aby utworzyć centrum, co oznacza, że każdy zakres od/25 do/32 spowoduje wystąpienie błędu podczas tworzenia.
4. Wybierz **kartę ExpressRoute**.

5. Na karcie **ExpressRoute** wypełnij następujące pola:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Wybierz pozycję **tak** , aby utworzyć bramę **ExpressRoute** .
   * Wybierz z listy rozwijanej wartość **jednostki skalowania bramy** .
6. Wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić poprawność.
7. Wybierz pozycję **Utwórz** , aby utworzyć centrum. Po 30 minutach **Odśwież** , aby wyświetlić centrum na stronie **centra** . Wybierz pozycję **Przejdź do zasobu** , aby przejść do zasobu.