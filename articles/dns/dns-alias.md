---
title: Omówienie rekordów aliasów — Azure DNS
description: Ten artykuł zawiera informacje na temat obsługi rekordów aliasów w usłudze Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 8b2576669357aae7e5fe423515933c2ce4a23a7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954481"
---
# <a name="azure-dns-alias-records-overview"></a>Omówienie rekordów aliasów usługi Azure DNS

Rekordy aliasów Azure DNS są kwalifikacjami w zestawie rekordów DNS. Mogą odwoływać się do innych zasobów platformy Azure z poziomu strefy DNS. Na przykład można utworzyć zestaw rekordów aliasu, który odwołuje się do publicznego adresu IP platformy Azure, a nie rekordu A. Rekord aliasu zostanie ustawiony na dynamiczne wystąpienie usługi publicznego adresu IP platformy Azure. W związku z tym rekord aliasu zostanie bezproblemowo zaktualizowany podczas rozpoznawania nazw DNS.

Zestaw rekordów aliasu jest obsługiwany dla następujących typów rekordów w strefie Azure DNS: 

- A
- AAAA
- CNAME

> [!NOTE]
> Jeśli zamierzasz użyć rekordu aliasu dla typów rekordów A lub AAAA, aby wskazać [profil Traffic Manager platformy Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md) , musisz się upewnić, że profil Traffic Manager ma tylko [zewnętrzne punkty końcowe](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Należy podać adres IPv4 lub IPv6 dla zewnętrznych punktów końcowych w Traffic Manager. Nie można używać w punktach końcowych w pełni kwalifikowanych nazw domen (FQDN). Najlepiej używać statycznych adresów IP.

## <a name="capabilities"></a>Możliwości

- **Wskaż zasób publicznego adresu IP z zestawu rekordów A/AAAA systemu DNS.** Można utworzyć zestaw rekordów A/AAAA i ustawić dla niego rekord aliasu, aby wskazywał na publiczny zasób IP (Standard lub Basic). Zestaw rekordów DNS zmienia się automatycznie w przypadku zmiany lub usunięcia publicznego adresu IP. Zawieszonego rekordy DNS wskazujące na niepoprawne adresy IP.

   Istnieje bieżący limit 20 rekordów aliasów na zasób.

- **Wskaż profil Traffic Manager z zestawu rekordów A/AAAA/CNAME systemu DNS.** Można utworzyć zestaw rekordów A/AAAA lub CNAME i użyć rekordów aliasów, aby wskazać profil Traffic Manager. Jest to szczególnie przydatne, gdy trzeba kierować ruchem w wierzchołku strefy, ponieważ tradycyjne rekordy CNAME nie są obsługiwane dla wierzchołka strefy. Załóżmy na przykład, że profil Traffic Manager to myprofile.trafficmanager.net, a Twoja firmowa strefa DNS to contoso.com. Można utworzyć zestaw rekordów aliasu typu A/AAAA dla contoso.com (wierzchołk strefy) i wskazać myprofile.trafficmanager.net.
- **Wskaż punkt końcowy usługi Azure Content Delivery Network (CDN)**. Jest to przydatne w przypadku tworzenia statycznych witryn sieci Web przy użyciu usługi Azure Storage i Azure CDN.
- **Wskaż inny rekord DNS ustawiony w ramach tej samej strefy.** Rekordy aliasów mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład zestaw rekordów CNAME DNS może być aliasem dla innego zestawu rekordów CNAME. To rozwiązanie jest przydatne, jeśli chcesz, aby niektóre rekordy miały aliasy i niektóre inne niż aliasy.

## <a name="scenarios"></a>Scenariusze

Istnieje kilka typowych scenariuszy dotyczących rekordów aliasów.

### <a name="prevent-dangling-dns-records"></a>Zapobiegaj zawieszonego rekordów DNS

Typowy problem z tradycyjnymi rekordami DNS to zawieszonego rekordy. Na przykład rekordy DNS, które nie zostały zaktualizowane w celu odzwierciedlenia zmian adresów IP. Ten problem występuje szczególnie w przypadku typów rekordów/AAAA lub CNAME.

W przypadku tradycyjnego rekordu strefy DNS, jeśli docelowy adres IP lub rekord CNAME już nie istnieje, należy ręcznie zaktualizować skojarzony z nim rekord DNS. W niektórych organizacjach ręczna aktualizacja może nie nastąpić w czasie, ponieważ występują problemy z procesem lub separacja ról i skojarzonych poziomów uprawnień. Na przykład rola może mieć uprawnienia do usuwania adresu CNAME lub IP należącego do aplikacji. Ale nie ma wystarczających uprawnień do zaktualizowania rekordu DNS, który wskazuje te elementy docelowe. Opóźnienie aktualizowania rekordu DNS może potencjalnie spowodować przestoje dla użytkowników.

Rekordy aliasów uniemożliwiają zawieszonego odwołań przez ścisłe sprzęganie cyklu życia rekordu DNS z zasobem platformy Azure. Rozważmy na przykład rekord DNS, który jest kwalifikowany jako rekord aliasu, aby wskazywał na publiczny adres IP lub profil Traffic Manager. Jeśli usuniesz te zasoby bazowe, rekord aliasu DNS będzie pustym zestawem rekordów. Nie odwołuje się już do usuniętego zasobu.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Aktualizuj rekord DNS — Ustaw automatycznie, gdy zmienią się adresy IP aplikacji

Ten scenariusz jest podobny do poprzedniego. Być może aplikacja została przeniesiona lub bazowa maszyna wirtualna zostanie ponownie uruchomiona. Rekord aliasu zostanie automatycznie zaktualizowany w przypadku zmiany adresu IP dla zasobu publicznego adresu IP. Pozwala to uniknąć potencjalnych zagrożeń bezpieczeństwa związanych z kierowaniem użytkowników do innej aplikacji, która ma przypisany stary publiczny adres IP.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hostowanie aplikacji z równoważeniem obciążenia w wierzchołku strefy

Protokół DNS uniemożliwia przypisanie rekordów CNAME w wierzchołku strefy. Na przykład jeśli Twoja domena to contoso.com; rekordy CNAME można utworzyć dla somelabel.contoso.com; ale nie można utworzyć rekordu CNAME dla samego contoso.comu.
To ograniczenie powoduje problem dla właścicieli aplikacji, którzy mają aplikacje o zrównoważonym obciążeniu, które znajdują się za [usługą Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Ponieważ użycie profilu Traffic Manager wymaga utworzenia rekordu CNAME, nie jest możliwe wskazanie Traffic Manager profilu ze wierzchołka strefy.

Ten problem jest rozwiązywany przy użyciu rekordów aliasów. W przeciwieństwie do rekordów CNAME, rekordy aliasów są tworzone w wierzchołku strefy, a właściciele aplikacji mogą jej używać do wskazywania rekordu wierzchołka strefy do Traffic Manager profilu, który ma zewnętrzne punkty końcowe. Właściciele aplikacji wskazują ten sam profil Traffic Manager, który jest używany przez dowolną inną domenę w ramach strefy DNS.

Na przykład contoso.com i www \. contoso.com mogą wskazywać na ten sam profil Traffic Manager. Aby dowiedzieć się więcej o używaniu rekordów aliasów w profilach usługi Azure Traffic Manager, zobacz sekcję następne kroki.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Wierzchołk strefy punktu do Azure CDN punktów końcowych

Podobnie jak w przypadku profilu Traffic Manager, można również użyć rekordów aliasów do wskazywania wierzchołka strefy DNS do Azure CDN punktów końcowych. Jest to przydatne w przypadku tworzenia statycznych witryn sieci Web przy użyciu usługi Azure Storage i Azure CDN. Następnie można uzyskać dostęp do witryny sieci Web bez oczekiwania "www" na nazwę DNS.

Na przykład jeśli statyczna Witryna internetowa ma nazwę `www.contoso.com` , użytkownicy mogą uzyskać dostęp do witryny za pomocą programu bez konieczności dołączania sieci `contoso.com` www do nazwy DNS.

Jak opisano wcześniej, rekordy CNAME nie są obsługiwane w wierzchołku strefy. Dlatego nie można użyć rekordu CNAME, aby wskazywał contoso.com do punktu końcowego usługi CDN. Zamiast tego można użyć rekordu aliasu, aby wskazywał bezpośrednio na punkt końcowy usługi CDN.

> [!NOTE]
> Wskazanie wierzchołka strefy dla punktów końcowych usługi CDN dla Azure CDN z Akamai nie jest obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rekordów aliasów, zobacz następujące artykuły:

- [Samouczek: Konfigurowanie rekordu aliasu w celu odwoływania się do publicznego adresu IP platformy Azure](tutorial-alias-pip.md)
- [Samouczek: konfigurowanie rekordu aliasu w celu obsługi nazw domen wierzchołkowych przy użyciu usługi Traffic Manager](tutorial-alias-tm.md)
- [Często zadawane pytania na temat systemu DNS](./dns-faq.md#alias-records)