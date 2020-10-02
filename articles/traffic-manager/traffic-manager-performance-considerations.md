---
title: Zagadnienia dotyczące wydajności Traffic Manager platformy Azure | Microsoft Docs
description: Informacje na temat wydajności Traffic Manager i sposobu testowania wydajności witryny sieci Web przy użyciu Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: f080f5011311101ed8f9faa2ee807c030a048409
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653047"
---
# <a name="performance-considerations-for-traffic-manager"></a>Zagadnienia dotyczące wydajności dla usługi Traffic Manager

Na tej stronie opisano zagadnienia dotyczące wydajności przy użyciu Traffic Manager. Poniżej przedstawiono przykładowy scenariusz:

Masz wystąpienia witryny sieci Web w regionach Zachodnie i EastAsia. W przypadku jednego z wystąpień Sprawdzanie kondycji sondy usługi Traffic Manager nie powiodło się. Ruch aplikacji jest kierowany do regionu w dobrej kondycji. Jest to oczekiwane przejście w tryb failover, ale wydajność może być problemem w zależności od opóźnienia ruchu, który jest teraz Podróżujący do odległego regionu.

## <a name="performance-considerations-for-traffic-manager"></a>Zagadnienia dotyczące wydajności dla usługi Traffic Manager

Jedynym wpływem na wydajność, który Traffic Manager może mieć witryna sieci Web, jest początkowe wyszukiwanie DNS. Żądanie DNS dotyczące nazwy profilu Traffic Manager jest obsługiwane przez serwer główny usługi Microsoft DNS, który hostuje strefę trafficmanager.net. Traffic Manager wypełnia i regularnie aktualizuje serwery główne DNS firmy Microsoft na podstawie zasad Traffic Manager i wyników sondowania. Nawet podczas początkowego wyszukiwania DNS żadne zapytania DNS nie są wysyłane do Traffic Manager.

Traffic Manager składa się z kilku składników: serwerów nazw DNS, usługi interfejsu API, warstwy magazynu i usługi monitorowania punktów końcowych. Jeśli składnik usługi Traffic Manager ulegnie awarii, nie ma wpływu na nazwę DNS skojarzoną z Twoim profilem Traffic Manager. Rekordy na serwerach DNS firmy Microsoft pozostaną niezmienione. Jednak monitorowanie punktów końcowych i aktualizowanie DNS nie są wykonywane. W związku z tym Traffic Manager nie może zaktualizować usługi DNS w taki sposób, aby wskazywała witrynę trybu failover, gdy lokacja główna ulegnie awarii.

Rozpoznawanie nazw DNS jest szybkie, a wyniki są buforowane. Szybkość początkowej wyszukiwania DNS zależy od serwerów DNS używanych przez klienta do rozpoznawania nazw. Zwykle klient może zakończyć wyszukiwanie DNS w ciągu ~ 50 ms. Wyniki wyszukiwania są buforowane na czas trwania czasu wygaśnięcia (TTL) DNS. Domyślny czas wygaśnięcia dla Traffic Manager wynosi 300 sekund.

Ruch nie przechodzi przez Traffic Manager. Po zakończeniu wyszukiwania DNS klient ma adres IP dla wystąpienia witryny sieci Web. Klient łączy się bezpośrednio z tym adresem i nie przechodzi przez Traffic Manager. Wybrane zasady Traffic Manager nie mają wpływu na wydajność systemu DNS. Jednak Metoda routingu wydajności może mieć negatywny wpływ na działanie aplikacji. Na przykład jeśli zasady przekierowują ruch z Ameryka Północna do wystąpienia hostowanego w Azji, opóźnienie sieci dla tych sesji może być problemem z wydajnością.

## <a name="measuring-traffic-manager-performance"></a>Mierzenie wydajności Traffic Manager

Istnieje kilka witryn sieci Web, których można użyć do zrozumienia wydajności i zachowania profilu Traffic Manager. Wiele z tych witryn jest bezpłatnych, ale mogą mieć ograniczenia. Niektóre lokacje oferują ulepszone monitorowanie i raportowanie z opłatą.

Narzędzia w tych lokacjach mierzą opóźnienia DNS i wyświetlają rozpoznane adresy IP dla lokalizacji klientów na całym świecie. Większość z tych narzędzi nie buforuje wyników DNS. W związku z tym narzędzia pokazują pełne wyszukiwanie DNS za każdym razem, gdy test jest wykonywany. Gdy testujesz się z własnego klienta, w czasie trwania czasu wygaśnięcia TTL występuje tylko pełna wydajność wyszukiwania DNS.

## <a name="sample-tools-to-measure-dns-performance"></a>Przykładowe narzędzia do mierzenia wydajności systemu DNS

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS oferuje wiele narzędzi do oceny wydajności. W narzędziu do porównywania DNS można zobaczyć, jak długo trwa rozpoznawanie nazwy DNS i jak porównuje się z innymi dostawcami usług DNS.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Jednym z najprostszych narzędzi jest WebSitePulse. Wprowadź adres URL, aby zobaczyć czas rozpoznawania nazw DNS, pierwszy bajt, ostatni bajt i inne statystyki wydajności. Można wybrać jedną z trzech różnych lokalizacji testowych. W tym przykładzie widać, że pierwsze wykonanie wskazuje, że wyszukiwanie DNS zajmuje 0,204 sek.

    ![Zrzut ekranu pokazujący narzędzie "WebSitePulse" z wyróżnionym wynikiem wyszukiwania "DNS".](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Ponieważ wyniki są przechowywane w pamięci podręcznej, drugi test dla tego samego Traffic Managerego punktu końcowego wyszukiwanie DNS zajmie 0,002 sek.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Monitor syntetyczny aplikacji urzędu certyfikacji](https://asm.ca.com/en/checkit.php)

    Ta lokacja wcześniej znana jako narzędzie do sprawdzania myszy w witrynie sieci Web, w której jest wyświetlany czas rozpoznawania nazw DNS jednocześnie z wielu regionów geograficznych. Wprowadź adres URL, aby zobaczyć czas rozpoznawania nazw DNS, czas połączenia i szybkość z kilku lokalizacji geograficznych. Użyj tego testu, aby sprawdzić, która usługa hostowana jest zwracana dla różnych lokalizacji na całym świecie.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    To narzędzie zapewnia statystykę wydajności dla każdego elementu strony sieci Web. Karta Analiza strony pokazuje procent czasu poświęcony na wyszukiwanie DNS.

* [Co to jest mój system DNS?](https://www.whatsmydns.net/)

    Ta witryna wykonuje wyszukiwanie DNS z 20 różnych lokalizacji i wyświetla wyniki na mapie.

* [Interfejs sieci Web Dig](https://www.digwebinterface.com)

    Ta lokacja zawiera bardziej szczegółowe informacje DNS, w tym CNAME i rekordy. Upewnij się, że w obszarze Opcje jest zaznaczone pole wyboru "Koloruj dane wyjściowe" i "statystyki", a następnie wybierz pozycję "wszystkie" w obszarze serwery nazw.

## <a name="next-steps"></a>Następne kroki

[Informacje o metodach routingu ruchu Traffic Manager](traffic-manager-routing-methods.md)

[Testowanie ustawień usługi Traffic Manager](traffic-manager-testing-settings.md)

[Operacje w usłudze Traffic Manager (dokumentacja interfejsu API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Polecenia cmdlet usługi Azure Traffic Manager](https://docs.microsoft.com/powershell/module/az.trafficmanager)

