---
title: Udostępnianie prywatnej usługi linku w wirtualnej sieci WAN
titleSuffix: Azure Virtual WAN
description: Kroki konfigurowania prywatnego linku w wirtualnej sieci WAN
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913759"
---
# <a name="use-private-link-in-virtual-wan"></a>Korzystanie z prywatnego linku w wirtualnej sieci WAN

[Łącze prywatne platformy Azure](../private-link/private-link-overview.md) to technologia, która umożliwia łączenie ofert typu "platforma jako usługa" na platformie Azure przy użyciu połączenia z prywatnym adresem IP przez ujawnienie [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md). Za pomocą wirtualnej sieci WAN platformy Azure można wdrożyć prywatny punkt końcowy w jednej z sieci wirtualnych podłączonych do dowolnego koncentratora wirtualnego. Zapewnia to łączność z innymi sieciami wirtualnymi lub gałęziami połączonymi z tą samą wirtualną siecią WAN.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach w tym artykule przyjęto założenie, że wirtualna sieć WAN została już wdrożona z co najmniej jednym koncentratorem, a także przynajmniej dwie sieci wirtualne połączone z wirtualną siecią WAN.

Aby utworzyć nową wirtualną sieć WAN i nowe centrum, wykonaj czynności opisane w następujących artykułach:

* [Tworzenie wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Tworzenie koncentratora](virtual-wan-site-to-site-portal.md#hub)
* [Łączenie sieci wirtualnej z koncentratorem](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>Utwórz prywatny punkt końcowy linku

Można utworzyć prywatny punkt końcowy linku dla wielu różnych usług. W tym przykładzie będziemy używać Azure SQL Database. Więcej informacji o sposobie tworzenia prywatnego punktu końcowego dla Azure SQL Database można znaleźć w [przewodniku szybki start: Tworzenie prywatnego punktu końcowego przy użyciu Azure Portal](../private-link/create-private-endpoint-portal.md). Na poniższej ilustracji przedstawiono konfigurację sieci Azure SQL Database:

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="Utwórz link prywatny" lightbox="./media/howto-private-link/create-private-link.png":::

Po utworzeniu Azure SQL Database można sprawdzić adres IP prywatnego punktu końcowego, przeglądając prywatne punkty końcowe:

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="prywatne punkty końcowe" lightbox="./media/howto-private-link/endpoints.png":::

Klikając utworzony przez siebie prywatny punkt końcowy, powinien zostać wyświetlony jego prywatny adres IP, a także jego w pełni kwalifikowaną nazwę domeny (FQDN). Należy zauważyć, że prywatny punkt końcowy ma adres IP w zakresie sieci wirtualnej, w której został wdrożony (10.1.3.0/24):

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="Punkt końcowy SQL" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>Sprawdź łączność z tej samej sieci wirtualnej

W tym przykładzie sprawdzimy łączność z Azure SQL Database z maszyny wirtualnej Ubuntu z zainstalowanymi narzędziami MS SQL. Pierwszy krok polega na sprawdzeniu, czy rozpoznawanie nazw DNS działa, a Azure SQL Database w pełni kwalifikowana nazwa domeny jest rozpoznawana jako prywatny adres IP w tej samej sieci wirtualnej, w której został wdrożony prywatny punkt końcowy (10.1.3.0/24):

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Jak widać w poprzednich danych wyjściowych, nazwa FQDN `wantest.database.windows.net` jest zamapowana na `wantest.privatelink.database.windows.net` , że prywatna strefa DNS utworzona w prywatnym punkcie końcowym zostanie rozpoznana jako prywatny adres IP `10.1.3.228` . Przechodzenie do prywatnej strefy DNS potwierdzi, że istnieje rekord A dla prywatnego punktu końcowego zamapowanego na prywatny adres IP:

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="Strefa DNS" lightbox="./media/howto-private-link/dns-zone.png":::

Po sprawdzeniu poprawnego rozpoznawania nazw DNS można spróbować nawiązać połączenie z bazą danych:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

Jak widać, korzystamy z specjalnego zapytania SQL, które daje nam źródłowy adres IP, który serwer SQL widzi od klienta. W takim przypadku serwer widzi klienta z prywatnym adresem IP ( `10.1.3.75` ), co oznacza, że ruch przechodzi z sieci wirtualnej bezpośrednio do prywatnego punktu końcowego.

Należy pamiętać, że należy ustawić zmienne `username` i dopasować je do `password` poświadczeń zdefiniowanych w Azure SQL Database, aby przykłady w tym przewodniku działały.

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>Nawiązywanie połączenia z innej sieci wirtualnej

Teraz, gdy jedna sieć wirtualna w wirtualnej sieci WAN platformy Azure ma łączność z prywatnym punktem końcowym, wszystkie inne sieci wirtualnych i gałęzie połączone z wirtualną siecią WAN mogą również mieć do niej dostęp. Musisz zapewnić łączność za pośrednictwem dowolnych modeli obsługiwanych przez wirtualną sieć WAN platformy Azure, takich jak [dowolny scenariusz](scenario-any-to-any.md) lub [scenariusz sieci wirtualnej usługi udostępnionej](scenario-shared-services-vnet.md), aby nazwać dwa przykłady.

Po nawiązaniu połączenia między siecią wirtualną lub gałęzią sieci wirtualnej, w której został wdrożony prywatny punkt końcowy, należy skonfigurować rozpoznawanie nazw DNS:

* W przypadku nawiązywania połączenia z prywatnym punktem końcowym z sieci wirtualnej można użyć tej samej strefy prywatnej, która została utworzona przy użyciu Azure SQL Database.
* W przypadku nawiązywania połączenia z prywatnym punktem końcowym z gałęzi (sieci VPN typu lokacja-lokacja, sieci VPN typu punkt-lokacja lub ExpressRoute) należy używać lokalnego rozpoznawania nazw DNS.

W tym przykładzie będziemy łączyć się z innej sieci wirtualnej, więc najpierw dołączymy prywatną strefę DNS do nowej sieci wirtualnej, aby jej obciążenia mogły rozwiązać Azure SQL Database w pełni kwalifikowaną nazwę domeny na prywatny adres IP. Odbywa się to poprzez łączenie prywatnej strefy DNS z nową siecią wirtualną:

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="Łącze DNS" lightbox="./media/howto-private-link/dns-link.png":::

Teraz dowolna maszyna wirtualna w dołączonej sieci wirtualnej powinna poprawnie rozwiązać Azure SQL Database FQDN do prywatnego adresu IP linku prywatnego:

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Aby sprawdzić, czy ta sieć wirtualna (10.1.1.0/24) ma połączenie z oryginalną siecią wirtualną, w której skonfigurowano prywatny punkt końcowy (10.1.3.0/24), można sprawdzić obowiązującą tabelę tras na dowolnej maszynie wirtualnej w sieci VNet:

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="efektywne trasy" lightbox="./media/howto-private-link/effective-routes.png":::

Jak widać, istnieje trasa wskazująca, że sieć wirtualna 10.1.3.0/24 została wprowadzona przez bramę Virtual Network w wirtualnej sieci WAN platformy Azure. Teraz możemy przetestować łączność z bazą danych:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

W tym przykładzie pokazano, jak utworzyć prywatny punkt końcowy w jednym z sieci wirtualnych podłączonych do wirtualnej sieci WAN, która zapewnia łączność z resztą sieci wirtualnych i gałęzi w wirtualnej sieci WAN.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
