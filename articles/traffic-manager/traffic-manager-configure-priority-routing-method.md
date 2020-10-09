---
title: Samouczek — Konfigurowanie routingu ruchu priorytetowego za pomocą usługi Azure Traffic Manager
description: W tym samouczku wyjaśniono, jak skonfigurować metodę routingu ruchu priorytetowego w Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: 404338c3e36216833d39c3551ae2dee0be304d24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401014"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Samouczek: Konfigurowanie metody routingu ruchu priorytetowego w Traffic Manager

Niezależnie od trybu witryny sieci Web usługi Azure Websites już udostępniają funkcje pracy w trybie failover dla witryn sieci Web w centrum danych (nazywanych również regionem). Traffic Manager zapewnia tryb failover dla witryn sieci Web w różnych centrach danych.

Typowym wzorcem w celu przełączenia usługi do trybu failover jest wysyłanie ruchu do usługi podstawowej i udostępnianie zestawu identycznych usług kopii zapasowych na potrzeby pracy w trybie failover. Poniższe kroki wyjaśniają, jak skonfigurować tę priorytetyzację w tryb failover za pomocą usług Azure Cloud Services i witryn sieci Web:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Aby skonfigurować metodę routingu ruchu priorytetowego

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu Wyszukaj **Traffic Manager profile** , a następnie kliknij nazwę profilu, dla którego chcesz skonfigurować metodę routingu.
3. W bloku **Traffic Manager profilu** Sprawdź, czy istnieją zarówno usługi w chmurze, jak i witryny sieci Web, które mają zostać uwzględnione w konfiguracji.
4. W sekcji **Ustawienia** kliknij pozycję **Konfiguracja**, a następnie w bloku **Konfiguracja** wykonaj następujące czynności:
    1. W przypadku **ustawień metody routingu ruchu**Sprawdź, czy metoda routingu ruchu jest **priorytetowa**. Jeśli tak nie jest, na liście rozwijanej kliknij pozycję **priorytet** .
    2. Ustaw **Ustawienia monitora punktu końcowego** identyczne dla wszystkich punktów końcowych w ramach tego profilu w następujący sposób:
        1. Wybierz odpowiedni **Protokół**i określ numer **portu** . 
        2. Dla **ścieżki** wpisz ukośnik */* . Aby monitorować punkty końcowe, należy określić ścieżkę i nazwę pliku. Ukośnik "/" jest prawidłowym wpisem ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (domyślnie).
        3. W górnej części strony kliknij pozycję **Zapisz**.
5. W sekcji **Ustawienia** kliknij pozycję **punkty końcowe**.
6. W bloku **punkty końcowe** zapoznaj się z kolejnością priorytetów dla punktów końcowych. W przypadku wybrania metody routingu ruchu **priorytetowego** kolejność wybranych punktów końcowych jest ważna. Sprawdź kolejność priorytetów punktów końcowych.  Podstawowy punkt końcowy znajduje się na górze. Podwójne sprawdzenie kolejności wyświetlania. wszystkie żądania będą kierowane do pierwszego punktu końcowego, a jeśli Traffic Manager wykryje, że jest w złej kondycji, ruch zostanie automatycznie przekierowany do następnego punktu końcowego. 
7. Aby zmienić kolejność priorytetów punktu końcowego, kliknij punkt końcowy, a następnie w wyświetlonym bloku **punkt końcowy** kliknij pozycję **Edytuj** i w razie potrzeby zmień wartość **priorytetu** . 
8. Kliknij przycisk **Zapisz** , aby zapisać zmiany ustawienia punktu końcowego.
9. Po zakończeniu wprowadzania zmian w konfiguracji kliknij pozycję **Zapisz** w dolnej części strony.
10. Przetestuj zmiany w konfiguracji w następujący sposób:
    1.  Na pasku wyszukiwania portalu Wyszukaj nazwę profilu Traffic Manager i kliknij profil Traffic Manager w wyświetlonych wynikach.
    2.  W bloku profil **Traffic Manager** kliknij pozycję **Przegląd**.
    3.  W bloku **profil Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu Traffic Manager. Może to być używane przez dowolnego klienta (na przykład przez przechodzenie do niego przy użyciu przeglądarki sieci Web) do kierowania do prawego punktu końcowego określonego przez typ routingu. W takim przypadku wszystkie żądania są kierowane do pierwszego punktu końcowego, a jeśli Traffic Manager wykryje, że jest w złej kondycji, ruch automatycznie przejdzie w tryb failover do następnego punktu końcowego.
11. Po zakończeniu działania profilu Traffic Manager Edytuj rekord DNS na autorytatywnym serwerze DNS, aby wskazywał nazwę domeny firmowej na nazwę domeny Traffic Manager.

![Konfigurowanie metody routingu ruchu priorytetowego przy użyciu Traffic Manager][1]

## <a name="next-steps"></a>Następne kroki


- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [metodzie routingu wydajności](traffic-manager-configure-performance-routing-method.md).
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [testować Traffic Manager ustawienia](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png