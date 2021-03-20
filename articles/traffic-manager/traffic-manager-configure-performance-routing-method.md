---
title: Konfigurowanie metody routingu ruchu sieciowego przy użyciu usługi Azure Traffic Manager | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania Traffic Manager w celu kierowania ruchu do punktu końcowego z najniższym opóźnieniem
services: traffic-manager
manager: twooley
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: 46d71a38b79f449084f7353527f2dfb05d5b92a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994914"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurowanie metody routingu ruchu o wydajności

Metoda routingu ruchu wydajności umożliwia kierowanie ruchu do punktu końcowego z najniższym opóźnieniem z sieci klienta. Zwykle centrum danych o najniższym opóźnieniu jest najbliższe odległości geograficznej. Ta metoda routingu ruchu nie może uwzględniać zmian w czasie rzeczywistym w konfiguracji lub obciążenia sieci.

##  <a name="to-configure-performance-routing-method"></a>Aby skonfigurować metodę routingu wydajności

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu Wyszukaj **Traffic Manager profile** , a następnie kliknij nazwę profilu, dla którego chcesz skonfigurować metodę routingu.
3. W bloku **Traffic Manager profilu** Sprawdź, czy istnieją zarówno usługi w chmurze, jak i witryny sieci Web, które mają zostać uwzględnione w konfiguracji.
4. W sekcji **Ustawienia** kliknij pozycję **Konfiguracja**, a następnie w bloku **Konfiguracja** wykonaj następujące czynności:
    1. W obszarze **Ustawienia metody routingu ruchu** w obszarze **Metoda routingu** wybierz pozycję **wydajność**.
    2. Ustaw **Ustawienia monitora punktu końcowego** identyczne dla wszystkich punktów końcowych w ramach tego profilu w następujący sposób:
        1. Wybierz odpowiedni **Protokół** i określ numer **portu** . 
        2. Dla **ścieżki** wpisz ukośnik */* . Aby monitorować punkty końcowe, należy określić ścieżkę i nazwę pliku. Ukośnik "/" jest prawidłowym wpisem ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (domyślnie).
        3. W górnej części strony kliknij pozycję **Zapisz**.
5.  Przetestuj zmiany w konfiguracji w następujący sposób:
    1.  Na pasku wyszukiwania portalu Wyszukaj nazwę profilu Traffic Manager i kliknij profil Traffic Manager w wyświetlonych wynikach.
    2.  W bloku profil **Traffic Manager** kliknij pozycję **Przegląd**.
    3.  W bloku **profil Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu Traffic Manager. Może to być używane przez dowolnego klienta (na przykład przez przechodzenie do niego przy użyciu przeglądarki sieci Web) do kierowania do prawego punktu końcowego określonego przez typ routingu. W takim przypadku wszystkie żądania są kierowane do punktu końcowego z najniższym opóźnieniem z sieci klienta.
6. Po zakończeniu działania profilu Traffic Manager Edytuj rekord DNS na autorytatywnym serwerze DNS, aby wskazywał nazwę domeny firmowej na nazwę domeny Traffic Manager.

![Konfigurowanie metody routingu ruchu sieciowego przy użyciu Traffic Manager][1]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [priorytetowej metodzie routingu](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [testować Traffic Manager ustawienia](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png