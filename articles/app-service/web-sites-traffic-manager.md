---
title: Sterowanie ruchem za pomocą Traffic Manager
description: Poznaj najlepsze rozwiązania dotyczące konfigurowania Traffic Manager platformy Azure, gdy integrujesz ją z Azure App Service.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 040f84288c66f4506919e775b9ea41324b617cfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80437893"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Kontrolowanie ruchu Azure App Service przy użyciu usługi Azure Traffic Manager
> [!NOTE]
> Ten artykuł zawiera informacje podsumowujące dotyczące Microsoft Azure Traffic Manager, które odnoszą się do Azure App Service. Więcej informacji na temat platformy Azure Traffic Manager można znaleźć, odwiedzając linki na końcu tego artykułu.
> 
> 

## <a name="introduction"></a>Wprowadzenie
Usługi Azure Traffic Manager możesz użyć do kontrolowania dystrybucji żądań klientów internetowych kierowanych do aplikacji w usłudze Azure App Service. Gdy punkty końcowe usługi App Service są dodawane do profilu usługi Azure Traffic Manager, usługa Azure Traffic Manager śledzi stan aplikacji usługi App Service (uruchomiona, zatrzymana lub usunięta), dzięki czemu można zdecydować, który z tych punktów końcowych powinien odbierać ruch.

## <a name="routing-methods"></a>Metody routingu
Na platformie Azure Traffic Manager są stosowane cztery różne metody routingu. Te metody są opisane na poniższej liście, ponieważ odnoszą się do Azure App Service.

* **[Priorytet](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** Użyj aplikacji głównej dla całego ruchu i podaj kopie zapasowe w przypadku, gdy podstawowy lub aplikacje kopii zapasowej są niedostępne.
* **[Ważone](../traffic-manager/traffic-manager-routing-methods.md#weighted):** Dystrybuuj ruch między zestawami aplikacji, a nawet zgodnie z wagami definiowanymi przez użytkownika.
* **[Wydajność](../traffic-manager/traffic-manager-routing-methods.md#performance):** gdy aplikacje znajdują się w różnych lokalizacjach geograficznych, użyj aplikacji "najbliższy" w warunkach najmniejszego opóźnienia sieci.
* **[Geograficzna](../traffic-manager/traffic-manager-routing-methods.md#geographic):** kierowanie użytkowników do określonych aplikacji na podstawie lokalizacji geograficznej, z której pochodzą zapytanie DNS. 

Aby uzyskać więcej informacji, zobacz [Traffic Manager metod routingu](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Profile App Service i Traffic Manager
Aby skonfigurować kontrolę nad ruchem App Service aplikacji, utworzysz profil w usłudze Azure Traffic Manager, który korzysta z jednej z czterech metod równoważenia obciążenia opisanej wcześniej, a następnie Dodaj punkty końcowe (w tym przypadku App Service), dla których chcesz kontrolować ruch do profilu. Stan aplikacji (uruchomiony, zatrzymany lub usunięty) jest regularnie przekazywany do profilu, dzięki czemu usługa Azure Traffic Manager może odpowiednio kierować ruchem.

W przypadku korzystania z usługi Azure Traffic Manager z platformą Azure należy pamiętać o następujących kwestiach:

* W przypadku wdrożeń tylko dla aplikacji w tym samym regionie App Service już zapewnia funkcje pracy w trybie failover i działania okrężnego bez względu na tryb aplikacji.
* W przypadku wdrożeń w tym samym regionie, które używają App Service w połączeniu z inną usługą w chmurze platformy Azure, można połączyć oba typy punktów końcowych, aby umożliwić scenariusze hybrydowe.
* Można określić tylko jeden App Service punkt końcowy dla regionu w profilu. Po wybraniu aplikacji jako punktu końcowego dla jednego regionu pozostałe aplikacje w tym regionie staną się niedostępne do wyboru dla tego profilu.
* App Service punkty końcowe określone w profilu Traffic Manager platformy Azure pojawiają się w sekcji **nazwy domen** na stronie Konfigurowanie dla aplikacji w profilu, ale nie można do niej konfigurować.
* Po dodaniu aplikacji do profilu w **adresie URL witryny** na pulpicie nawigacyjnym strony portalu aplikacji zostanie wyświetlony adres URL domeny niestandardowej aplikacji, jeśli został on ustawiony. W przeciwnym razie zostanie wyświetlony adres URL profilu Traffic Manager (na przykład `contoso.trafficmanager.net` ). Zarówno bezpośrednia nazwa domeny aplikacji, jak i adres URL Traffic Manager są widoczne na stronie konfigurowania aplikacji w sekcji **nazwy domen** .
* Niestandardowe nazwy domen działają zgodnie z oczekiwaniami, ale oprócz dodawania ich do aplikacji należy również skonfigurować mapę DNS tak, aby wskazywała adres URL Traffic Manager. Aby uzyskać informacje na temat sposobu konfigurowania domeny niestandardowej dla aplikacji App Service, zobacz [Konfigurowanie niestandardowej nazwy domeny w Azure App Service z integracją Traffic Manager](configure-domain-traffic-manager.md).
* Do profilu usługi Azure Traffic Manager można dodawać tylko aplikacje znajdujące się w trybie Standard lub Premium.
* Dodanie aplikacji do profilu Traffic Manager powoduje ponowne uruchomienie aplikacji.

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z omówieniem koncepcyjnym i technicznym Traffic Manager platformy Azure, zobacz [Traffic Manager omówienie](../traffic-manager/traffic-manager-overview.md).


