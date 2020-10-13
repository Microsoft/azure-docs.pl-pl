---
title: Koncepcje Azure Load Balancer
description: Przegląd pojęć dotyczących Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 5c12b7d0bf9bed551e54ccb2e54a4674a8323a9c
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930921"
---
# <a name="azure-load-balancer-algorithm"></a>Algorytm Azure Load Balancer

Moduł równoważenia obciążenia oferuje kilka możliwości dla aplikacji UDP i TCP.

## <a name="load-balancing-algorithm"></a>Algorytm równoważenia obciążenia

Można utworzyć regułę równoważenia obciążenia, aby dystrybuować ruch z frontonu do puli zaplecza. Azure Load Balancer używa algorytmu wyznaczania wartości skrótu do dystrybucji przepływów przychodzących (nie bajtów). Moduł równoważenia obciążenia ponownie zapisuje nagłówki przepływów w wystąpieniach puli zaplecza. Serwer jest dostępny do odbierania nowych przepływów, gdy sonda kondycji wskazuje dobry punkt końcowy zaplecza.

Domyślnie moduł równoważenia obciążenia używa skrótu z pięcioma kolekcjami.

Skrót obejmuje:

- **Źródłowy adres IP**
- **Port źródłowy**
- **Docelowy adres IP**
- **Port docelowy**
- **Numer protokołu IP do mapowania przepływów na dostępne serwery**

Koligacja ze źródłowym adresem IP jest tworzona przy użyciu dwóch lub trzech krotek wartości skrótu. Pakiety tego samego przepływu docierają do tego samego wystąpienia, w którym znajduje się fronton o zrównoważonym obciążeniu.

Port źródłowy zmienia się, gdy klient uruchamia nowy przepływ z tego samego źródłowego adresu IP. W efekcie wartość skrótu z pięcioma krotką może spowodować, że ruch przejdzie do innego punktu końcowego zaplecza.
Aby uzyskać więcej informacji, zobacz [Konfigurowanie trybu dystrybucji dla Azure Load Balancer](./load-balancer-distribution-mode.md).

Na poniższej ilustracji przedstawiono dystrybucję opartą na skrótach:

![Dystrybucja oparta na skrótach](./media/load-balancer-overview/load-balancer-distribution.png)

*Rysunek: dystrybucja oparta na skrótach*

## <a name="application-independence-and-transparency"></a>Niezależność i przezroczystość aplikacji

Moduł równoważenia obciążenia nie współdziała bezpośrednio z protokołem TCP lub UDP ani warstwą aplikacji. Można obsługiwać dowolny scenariusz aplikacji TCP lub UDP. Moduł równoważenia obciążenia nie zamyka lub nie pochodzi z przepływów ani nie współdziała z ładunkiem przepływu. Moduł równoważenia obciążenia nie udostępnia funkcji bramy warstwy aplikacji. Uzgadnianie protokołu zawsze następuje bezpośrednio między klientem a wystąpieniem puli zaplecza. Odpowiedź na przepływ przychodzący to zawsze odpowiedź z maszyny wirtualnej. Po dostarczeniu przepływu do maszyny wirtualnej oryginalny źródłowy adres IP również jest zachowywany.

- Każdy punkt końcowy jest odpowiedzią na maszynę wirtualną. Na przykład uzgadnianie protokołu TCP następuje między klientem a wybraną maszyną wirtualną zaplecza. Odpowiedź na żądanie do frontonu jest odpowiedzią wygenerowaną przez maszynę wirtualną zaplecza. Po pomyślnym zweryfikowaniu łączności z frontonem sprawdzasz poprawność łączności w ramach co najmniej jednej maszyny wirtualnej zaplecza.
- Ładunki aplikacji są niewidoczne dla modułu równoważenia obciążenia. Można obsługiwać dowolną aplikację UDP lub TCP.
- Ponieważ moduł równoważenia obciążenia nie współdziała z ładunkiem TCP i zapewnia odciążanie protokołu TLS, można tworzyć kompleksowe zaszyfrowane scenariusze. Korzystanie z usługi równoważenia obciążenia uzyskuje duże skalowanie w poziomie dla aplikacji TLS przez zakończenie połączenia TLS na maszynie wirtualnej. Na przykład pojemność klucza sesji protokołu TLS jest ograniczona tylko przez typ i liczbę maszyn wirtualnych dodawanych do puli zaplecza.

## <a name="next-steps"></a>Następne kroki

- Zobacz [tworzenie usługa Load Balancer w warstwie Standardowa publicznej](quickstart-load-balancer-standard-public-portal.md) , aby rozpocząć korzystanie z usługi Load Balancer: Utwórz jedną, twórz maszyny wirtualne z zainstalowanym niestandardowym rozszerzeniem usług IIS i Zrównoważ obciążenie aplikacji sieci Web między maszynami wirtualnymi.
- Dowiedz się więcej o [Azure Load Balancer połączenia wychodzące](load-balancer-outbound-connections.md).
- Dowiedz się więcej o [Azure Load Balancer](load-balancer-overview.md).
- Dowiedz się więcej o [sondach kondycji](load-balancer-custom-probe-overview.md).
- Dowiedz się więcej na temat [diagnostyki usługa Load Balancer w warstwie Standardowa](load-balancer-standard-diagnostics.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
