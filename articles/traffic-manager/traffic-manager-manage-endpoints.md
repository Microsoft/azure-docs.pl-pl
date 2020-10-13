---
title: Zarządzanie punktami końcowymi w usłudze Azure Traffic Manager | Microsoft Docs
description: W tym artykule omówiono dodawanie, usuwanie, włączanie i wyłączanie punktów końcowych w usłudze Azure Traffic Manager.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: duau
ms.openlocfilehash: fc6e8bcda17f9d788a430ed763bd9f8702eaf250
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89392922"
---
# <a name="add-disable-enable-or-delete-endpoints"></a>Dodawanie, usuwanie, włączanie i wyłączanie punktów końcowych

Funkcja Web Apps w usłudze Azure App Service udostępnia już routing ruchu w trybie failover i działania okrężnego dla witryn internetowych w centrum danych niezależnie od trybu witryny internetowej. Korzystając z usługi Azure Traffic Manager, można określić routing ruchu w trybie failover i działania okrężnego dla witryn sieci Web i usług w chmurze w różnych centrach danych. Pierwszym krokiem niezbędnym do obsługi tej funkcjonalności jest dodanie punktu końcowego usługi w chmurze lub witryny sieci Web do usługi Traffic Manager.

Można również wyłączyć poszczególne punkty końcowe, które są częścią profilu usługi Traffic Manager. Wyłączenie punktu końcowego pozostawia go jako część profilu, ale profil działa tak, jakby punkt końcowy nie był w nim uwzględniony. Ta akcja przydaje się do tymczasowego usunięcia punktu końcowego, który jest w trybie konserwacji lub jest ponownie wdrażany. Gdy punkt końcowy zostanie ponownie uruchomiony, można go włączyć.

> [!NOTE]
> Wyłączenie punktu końcowego nie ma nic wspólnego z jego stanem wdrożenia na platformie Azure. Punkt końcowy w dobrej kondycji będzie nadal działać i będzie mógł odbierać ruch nawet po wyłączeniu w usłudze Traffic Manager. Ponadto wyłączenie punktu końcowego w jednym profilu nie wpływa na jego stan w innym profilu.

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>Aby dodać usługę w chmurze lub punkt końcowy usługi App Service do profilu usługi Traffic Manager

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Korzystając z paska wyszukiwania portalu, wyszukaj nazwę **profilu usługi Traffic Manager**, który chcesz zmodyfikować, a następnie kliknij profil usługi Traffic Manager w wyświetlonych wynikach wyszukiwania.
3. W bloku **profilu usługi Traffic Manager**, w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**.
4. W wyświetlonym bloku **Punkty końcowe** kliknij pozycję **Dodaj**.
5. W bloku **Dodawanie punktu końcowego** wykonaj następujące czynności:
    1. Dla opcji **Typ** kliknij pozycję **Punkt końcowy platformy Azure**.
    2. W polu **Nazwa** podaj nazwę dla tego punktu końcowego.
    3. Dla opcji **Typ zasobu docelowego** z listy rozwijanej wybierz odpowiedni typ zasobu.
    4. W przypadku opcji **Zasób docelowy** kliknij selektor **Wybierz...**, aby wyświetlić listę zasobów w ramach tej samej subskrypcji w bloku **Zasoby**. W wyświetlonym bloku **Zasób** wybierz usługę, którą chcesz dodać jako pierwszy punkt końcowy.
    5. Dla opcji **Priorytet** wybierz wartość **1**. Spowoduje to kierowanie całego ruchu do tego punktu końcowego, jeśli jest on w dobrej kondycji.
    6. Pozycję **Dodaj jako wyłączone** pozostaw niezaznaczoną.
    7. Kliknij przycisk **OK** .
6.  Powtórz kroki 4 i 5, aby dodać kolejny punkt końcowy platformy Azure. Dla tego punktu końcowego ustaw opcję **Priorytet** na wartość **2**.
7.  Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w bloku **profilu usługi Traffic Manager** ze stanem monitorowania **Online**.

> [!NOTE]
> Po dodaniu lub usunięciu punktu końcowego z profilu za pomocą metody routingu ruchu dla trybu *failover* lista priorytetów pracy w trybie failover może nie być uporządkowana w sposób pożądany. Kolejność na liście priorytetów pracy w trybie failover można dostosować na stronie konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie routingu ruchu dla trybu failover](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Aby wyłączyć punkt końcowy

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Korzystając z paska wyszukiwania portalu, wyszukaj nazwę **profilu usługi Traffic Manager**, który chcesz zmodyfikować, a następnie kliknij profil usługi Traffic Manager w wyświetlonych wynikach wyszukiwania.
3. W bloku **profilu usługi Traffic Manager**, w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**. 
4. Kliknij punkt końcowy, który chcesz wyłączyć.
5. W bloku **Punkt końcowy** zmień stan punktu końcowego na **Wyłączony**, a następnie kliknij przycisk **Zapisz**.
6. Klienty w dalszym ciągu będą wysyłać ruch do punktu końcowego na czas wygaśnięcia (TTL). Czas wygaśnięcia można zmienić na stronie konfiguracji profilu usługi Traffic Manager.

## <a name="to-enable-an-endpoint"></a>Aby włączyć punkt końcowy

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Korzystając z paska wyszukiwania portalu, wyszukaj nazwę **profilu usługi Traffic Manager**, który chcesz zmodyfikować, a następnie kliknij profil usługi Traffic Manager w wyświetlonych wynikach wyszukiwania.
3. W bloku **profilu usługi Traffic Manager**, w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**. 
4. Kliknij punkt końcowy, który chcesz włączyć.
5. W bloku **Punkt końcowy** zmień stan punktu końcowego na **Włączony**, a następnie kliknij przycisk **Zapisz**.
6. Klienty w dalszym ciągu będą wysyłać ruch do punktu końcowego na czas wygaśnięcia (TTL). Czas wygaśnięcia można zmienić na stronie konfiguracji profilu usługi Traffic Manager.

## <a name="to-delete-an-endpoint"></a>Aby usunąć punkt końcowy

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Korzystając z paska wyszukiwania portalu, wyszukaj nazwę **profilu usługi Traffic Manager**, który chcesz zmodyfikować, a następnie kliknij profil usługi Traffic Manager w wyświetlonych wynikach wyszukiwania.
3. W bloku **profilu usługi Traffic Manager**, w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**. 
4. Kliknij punkt końcowy, który chcesz usunąć.
5. W bloku **punkt końcowy** kliknij pozycję **Usuń** .


## <a name="next-steps"></a>Następne kroki

* [Zarządzanie profilami usługi Traffic Manager](traffic-manager-manage-profiles.md)
* [Konfigurowanie metod routingu](traffic-manager-configure-routing-method.md)
* [Rozwiązywanie problemów ze stanem obniżonej wydajności usługi Traffic Manager](traffic-manager-troubleshooting-degraded.md)
* [Zagadnienia dotyczące wydajności usługi Traffic Manager](traffic-manager-performance-considerations.md)
* [Operacje w usłudze Traffic Manager (dokumentacja interfejsu API REST)](https://go.microsoft.com/fwlink/p/?LinkID=313584)

