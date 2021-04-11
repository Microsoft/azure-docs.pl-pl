---
title: Jak działa usługa Azure Traffic Manager | Microsoft Docs
description: Ten artykuł pomoże Ci zrozumieć, jak Traffic Manager kieruje ruchem w celu uzyskania wysokiej wydajności i dostępności aplikacji sieci Web
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: duau
ms.openlocfilehash: 376aa04228113c56f0f797f737833802c9eca021
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029492"
---
# <a name="how-traffic-manager-works"></a>Jak działa Traffic Manager

Usługa Azure Traffic Manager umożliwia kontrolowanie dystrybucji ruchu między punktami końcowymi aplikacji. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią.

Traffic Manager zapewnia dwie kluczowe korzyści:

- Rozkład ruchu zgodnie z jedną z kilku [metod routingu ruchu](traffic-manager-routing-methods.md)
- [Ciągłe monitorowanie kondycji punktów końcowych](traffic-manager-monitoring.md) i automatyczne przełączanie awaryjne w przypadku niepowodzenia punktów końcowych

Klient, który próbuje nawiązać połączenie z usługą, musi najpierw przekształcić nazwę DNS usługi na adres IP. Następnie klient łączy się z tym adresem IP, aby uzyskać dostęp do usługi.

**Najważniejszym punktem, aby zrozumieć, jest to, że Traffic Manager działa na poziomie systemu DNS, który znajduje się w warstwie aplikacji (warstwa 7).**  Traffic Manager używa systemu DNS do kierowania klientów do określonych punktów końcowych usługi na podstawie reguł metody routingu ruchu. Klienci łączą się **bezpośrednio** z wybranym punktem końcowym. Traffic Manager nie jest serwerem proxy ani bramą. Traffic Manager nie widzi ruchu przechodzącego między klientem a usługą.

## <a name="traffic-manager-example"></a>Przykład Traffic Manager

Firma Contoso Corp opracowała nowy portal dla partnerów. Adres URL dla tego portalu to `https://partners.contoso.com/login.aspx` . Aplikacja jest hostowana w trzech regionach platformy Azure. Aby zwiększyć dostępność i zmaksymalizować wydajność globalną, wykorzystuje Traffic Manager do dystrybucji ruchu klientów do najbliższego dostępnego punktu końcowego.

Aby osiągnąć tę konfigurację, wykonaj następujące czynności:

1. Wdróż trzy wystąpienia swojej usługi. Nazwy DNS tych wdrożeń to "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" i "contoso-asia.cloudapp.net".
1. Utwórz profil Traffic Manager o nazwie "contoso.trafficmanager.net" i skonfiguruj go tak, aby korzystał z metody routingu ruchu "Performance" w trzech punktach końcowych.
1. Skonfiguruj swoją nazwę domeny znaczącym "partners.contoso.com", aby wskazywała na "contoso.trafficmanager.net", przy użyciu rekordu CNAME DNS.

![Traffic Manager Konfiguracja DNS][1]

> [!NOTE]
> W przypadku korzystania z domeny znaczącym z usługą Azure Traffic Manager należy użyć rekordu CNAME do wskazywania nazwy domeny znaczącym do nazwy domeny Traffic Manager. Standardy DNS nie umożliwiają utworzenia rekordu CNAME w "wierzchołku" (lub korzeniu) domeny. W tym celu nie można utworzyć rekordu CNAME dla elementu "contoso.com" (czasami nazywanego "nie" domeną "). Można utworzyć rekord CNAME dla domeny w lokalizacji "contoso.com", na przykład "www.contoso.com". Aby obejść to ograniczenie, zalecamy Hostowanie domeny DNS na [Azure DNS](../dns/dns-overview.md) i używanie [rekordów aliasów](../dns/tutorial-alias-tm.md) w celu wskazywania profilu usługi Traffic Manager. Alternatywnie możesz użyć prostego przekierowania HTTP, aby skierować żądania dla "contoso.com" do alternatywnej nazwy, takiej jak "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Jak klienci łączą się za pomocą Traffic Manager

Kontynuując z poprzedniego przykładu, gdy klient zażąda strony `https://partners.contoso.com/login.aspx` , klient wykona następujące czynności w celu rozpoznania nazwy DNS i ustanowienia połączenia:

![Ustanawianie połączenia przy użyciu Traffic Manager][2]

1. Klient wysyła zapytanie DNS do skonfigurowanej cyklicznej usługi DNS w celu rozpoznania nazwy "partners.contoso.com". Cykliczna usługa DNS, czasami nazywana usługą "lokalna usługa DNS", nie hostuje bezpośrednio domen DNS. Klient jest wyłączony i nie ładuje pracy kontaktując się z różnymi autorytatywnymi usługami DNS w Internecie, które są konieczne do rozpoznania nazwy DNS.
2. Aby można było rozpoznać nazwę DNS, cykliczna usługa DNS znajduje serwery nazw dla domeny "contoso.com". Następnie kontaktuje się z tymi serwerami nazw, aby zażądać rekordu DNS "partners.contoso.com". Serwery DNS contoso.com zwracają rekord CNAME wskazujący contoso.trafficmanager.net.
3. Następnie cykliczna usługa DNS znajduje serwery nazw dla domeny "trafficmanager.net", które są udostępniane przez usługę Traffic Manager platformy Azure. Następnie wysyła żądanie dotyczące rekordu DNS "contoso.trafficmanager.net" do tych serwerów DNS.
4. Traffic Manager serwery nazw odbierają żądanie. Wybierają punkt końcowy na podstawie:

    - Skonfigurowany stan każdego punktu końcowego (wyłączone punkty końcowe nie są zwracane)
    - Bieżąca kondycja każdego punktu końcowego określona przez Traffic Manager sprawdzenia kondycji. Aby uzyskać więcej informacji, zobacz [Traffic Manager monitorowania punktów końcowych](traffic-manager-monitoring.md).
    - Wybrana metoda routingu ruchu. Aby uzyskać więcej informacji, zobacz [Traffic Manager metod routingu](traffic-manager-routing-methods.md).

5. Wybrany punkt końcowy jest zwracany jako inny rekord CNAME DNS. W takim przypadku powiadom nas o tym, że contoso-eu.cloudapp.net jest zwracany.
6. Następnie cykliczna usługa DNS znajduje serwery nazw dla domeny "cloudapp.net". Kontaktuje się z tymi serwerami nazw, aby zażądać rekordu DNS "contoso-eu.cloudapp.net". Zwracany jest rekord DNS "A" zawierający adres IP punktu końcowego usługi opartej na Unii Europejskiej.
7. Rekursywna usługa DNS konsoliduje wyniki i zwraca pojedynczą odpowiedź DNS do klienta.
8. Klient odbiera wyniki DNS i łączy się z podanym adresem IP. Klient łączy się z punktem końcowym usługi aplikacji bezpośrednio, a nie za pomocą Traffic Manager. Ponieważ jest to punkt końcowy HTTPS, Klient wykonuje niezbędną uzgadnianie SSL/TLS, a następnie wysyła żądanie HTTP GET dla strony "/login.aspx".

Rekursywna usługa DNS buforuje odpowiedzi DNS, które odbiera. Program rozpoznawania nazw DNS na urządzeniu klienckim buforuje również wynik. Buforowanie umożliwia szybsze odbieranie zapytań DNS przy użyciu danych z pamięci podręcznej, a nie wykonywanie zapytań dotyczących innych serwerów nazw. Czas trwania pamięci podręcznej zależy od właściwości "czas wygaśnięcia" (TTL) każdego rekordu DNS. Krótsze wartości powodują szybsze wygaśnięcie pamięci podręcznej, a tym samym więcej operacji rundy do serwerów nazw Traffic Manager. Więcej wartości oznacza, że może upłynąć dłużej, że ruch nie przeszedł do punktu końcowego. Traffic Manager umożliwia skonfigurowanie czasu wygaśnięcia w Traffic Manager odpowiedzi DNS na wartość równą 0 sekund i maksymalnie 2 147 483 647 sekund (maksymalny zakres zgodny ze standardem [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)), co pozwala wybrać wartość, która najlepiej równoważy potrzebom aplikacji.

## <a name="faqs"></a>Często zadawane pytania

* [Jakiego adresu IP używa Traffic Manager?](./traffic-manager-faqs.md#what-ip-address-does-traffic-manager-use)

* [Jakiego rodzaju ruch może być kierowany przy użyciu Traffic Manager?](./traffic-manager-faqs.md#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Czy Traffic Manager obsługuje sesje "Sticky"?](./traffic-manager-faqs.md#does-traffic-manager-support-sticky-sessions)

* [Dlaczego podczas korzystania z Traffic Manager pojawia się błąd HTTP?](./traffic-manager-faqs.md#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Jaki jest wpływ na wydajność korzystania z Traffic Manager?](./traffic-manager-faqs.md#what-is-the-performance-impact-of-using-traffic-manager)

* [Jakich protokołów aplikacji można używać z Traffic Manager?](./traffic-manager-faqs.md#what-application-protocols-can-i-use-with-traffic-manager)

* [Czy można użyć Traffic Manager z "owies" nazwą domeny?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-a-naked-domain-name)

* [Czy Traffic Manager uwzględnić adres podsieci klienta podczas obsługi zapytań DNS?](./traffic-manager-faqs.md#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Co to jest czas wygaśnięcia systemu DNS i jak ma to wpływ na moich użytkowników?](./traffic-manager-faqs.md#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Jak wysoka lub niska można ustawić czas wygaśnięcia dla Traffic Manager odpowiedzi?](./traffic-manager-faqs.md#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Jak zrozumieć ilość zapytań przesyłanych do mojego profilu?](./traffic-manager-faqs.md#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat Traffic Manager [monitorowania punktów końcowych i automatycznego przełączania do trybu failover](traffic-manager-monitoring.md).

Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md)Traffic Manager.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png
