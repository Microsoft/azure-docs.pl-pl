---
title: Samouczek — Konfigurowanie routingu opartego na rozważonej obruchowej w usłudze Azure Traffic Manager
description: W tym samouczku wyjaśniono, jak równoważyć obciążenie ruchu przy użyciu metody okrężnej w Traffic Manager
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
ms.openlocfilehash: dff7d4ec02c5a17b51d73b9d81f93984b95a7d22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401354"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Samouczek: Konfigurowanie metody routingu ruchu ważonego w Traffic Manager

Typowym wzorcem metody routingu ruchu jest zapewnienie zestawu identycznych punktów końcowych, takich jak usługi w chmurze i witryny sieci Web, a także wysyłanie ruchu do każdego z nich. Poniższe kroki przedstawiają sposób konfigurowania tego typu metody routingu ruchu.

> [!NOTE]
> Usługa Azure Web App już zapewnia funkcję równoważenia obciążenia z działaniem okrężnym dla witryn sieci Web w regionie świadczenia usługi Azure (co może obejmować wiele centrów danych). Traffic Manager umożliwia dystrybucję ruchu między witrynami sieci Web w różnych centrach danych.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Aby skonfigurować metodę routingu ruchu ważonego

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu Wyszukaj **Traffic Manager profile** , a następnie kliknij nazwę profilu, dla którego chcesz skonfigurować metodę routingu.
3. W bloku **Traffic Manager profilu** Sprawdź, czy istnieją zarówno usługi w chmurze, jak i witryny sieci Web, które mają zostać uwzględnione w konfiguracji.
4. W sekcji **Ustawienia** kliknij pozycję **Konfiguracja**, a następnie w bloku **Konfiguracja** wykonaj następujące czynności:
    1. W przypadku **ustawień metody routingu ruchu**Sprawdź, czy metoda routingu ruchu jest **ważona**. Jeśli nie, kliknij przycisk **ważone** z listy rozwijanej.
    2. Ustaw **Ustawienia monitora punktu końcowego** identyczne dla wszystkich punktów końcowych w ramach tego profilu w następujący sposób:
        1. Wybierz odpowiedni **Protokół**i określ numer **portu** . 
        2. Dla **ścieżki** wpisz ukośnik */* . Aby monitorować punkty końcowe, należy określić ścieżkę i nazwę pliku. Ukośnik "/" jest prawidłowym wpisem ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (domyślnie).
        3. W górnej części strony kliknij pozycję **Zapisz**.
5. Przetestuj zmiany w konfiguracji w następujący sposób:
    1.  Na pasku wyszukiwania portalu Wyszukaj nazwę profilu Traffic Manager i kliknij profil Traffic Manager w wyświetlonych wynikach.
    2.  W bloku profil **Traffic Manager** kliknij pozycję **Przegląd**.
    3.  W bloku **profil Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu Traffic Manager. Może to być używane przez dowolnego klienta (na przykład przez przechodzenie do niego przy użyciu przeglądarki sieci Web) do kierowania do prawego punktu końcowego określonego przez typ routingu. W takim przypadku wszystkie żądania są kierowane do każdego punktu końcowego w sposób okrężny.
6. Po zakończeniu działania profilu Traffic Manager Edytuj rekord DNS na autorytatywnym serwerze DNS, aby wskazywał nazwę domeny firmowej na nazwę domeny Traffic Manager.

![Konfigurowanie metody routingu ruchu ważonego za pomocą Traffic Manager][1]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [metodzie routingu ruchu priorytetowego](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [metodzie routingu ruchu wydajnościowego](traffic-manager-configure-performance-routing-method.md).
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [testować Traffic Manager ustawienia](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
