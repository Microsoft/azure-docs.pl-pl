---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f469664c716ecef6b82de2befa40b33f253e229f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627718"
---
1. Zlokalizuj utworzoną wirtualną sieć WAN. Na stronie wirtualna sieć WAN w obszarze **łączność** wybierz pozycję **centra**.
2. Na stronie **centra** wybierz pozycję **+ nowe centrum** , aby otworzyć stronę **Tworzenie wirtualnego centrum** .

    ![Zrzut ekranu przedstawia okienko tworzenie koncentratora wirtualnego z wybraną kartą podstawowe.](./media/virtual-wan-tutorial-hub-include/basics.png "Podstawy")
3. Na karcie Tworzenie **podstawy** strony **wirtualnego centrum** wykonaj następujące pola:

   * **Region** (wcześniej nazywany lokalizacją)
   * **Nazwa**
   * **Prywatna przestrzeń adresowa centrum** — minimalna przestrzeń adresowa to/24 w celu utworzenia centrum. W przypadku użycia dowolnego elementu z zakresu od/25 do/32 podczas tworzenia wystąpi błąd. Nie musisz jawnie planować przestrzeni adresowej podsieci dla usług w koncentratorze wirtualnym. Ponieważ wirtualna sieć WAN platformy Azure jest usługą zarządzaną, tworzy odpowiednie podsieci w koncentratorze wirtualnym dla różnych bram/usług (na przykład bramy sieci VPN, bramy ExpressRoute, bramy sieci VPN typu punkt-lokacja, Zapora, routing itp.).
4. Wybierz pozycję **Dalej: lokacja-lokacja**.

    ![Zrzut ekranu przedstawia okienko tworzenie wirtualnego centrum z wybraną lokacją lokacja.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Lokacja-lokacja")

5. Na karcie **lokacja-lokacja** wykonaj następujące pola:

   * Wybierz pozycję **tak** , aby utworzyć sieć VPN typu lokacja-lokacja.
   * Nie można edytować pola numer AS.
   * Wybierz z listy rozwijanej wartość **jednostki skalowania bramy** . Jednostka skalowania umożliwia wybranie zagregowanej przepływności bramy sieci VPN tworzonej w koncentratorze wirtualnym w celu połączenia z lokacjami programu. Jeśli wybierzesz 1 jednostkę skalowania = 500 MB/s, oznacza to, że zostaną utworzone dwa wystąpienia nadmiarowości, z których każda będzie mieć maksymalną przepływność wynoszącą 500 MB/s. Na przykład jeśli masz pięć rozgałęzień, każda z nich wykonuje 10 MB/s w rozgałęzieniu, będzie potrzebna zagregowana liczba 50 MB/s na końcu. Planowanie zagregowanej pojemności bramy sieci VPN platformy Azure należy wykonać po ocenie pojemności wymaganej do obsługi liczby gałęzi do centrum.
6. Wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić poprawność.
7. Wybierz pozycję **Utwórz** , aby utworzyć centrum. Po 30 minutach **Odśwież** , aby wyświetlić centrum na stronie **centra** . Wybierz pozycję **Przejdź do zasobu** , aby przejść do zasobu.
