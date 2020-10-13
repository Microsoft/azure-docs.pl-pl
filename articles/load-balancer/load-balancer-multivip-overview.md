---
title: Wiele frontonów — Azure Load Balancer
description: Korzystając z tej ścieżki szkoleniowej, Rozpocznij pracę z omówieniem wielu frontonów na Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 2e27b168087966701fb53cc8df19d264861257d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448103"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Wiele frontonów dla Azure Load Balancer

Azure Load Balancer umożliwia Równoważenie obciążenia usług na wielu portach, wielu adresach IP lub obu. W celu zrównoważenia przepływów między zestawem maszyn wirtualnych można użyć definicji publicznych i wewnętrznych modułów równoważenia obciążenia.

W tym artykule opisano podstawowe informacje dotyczące tej możliwości, ważnych koncepcji i ograniczeń. Jeśli zamierzasz tylko uwidocznić usługi na jednym adresie IP, możesz znaleźć uproszczone instrukcje dotyczące konfiguracji [publicznych](load-balancer-get-started-internet-portal.md) lub [wewnętrznych](load-balancer-get-started-ilb-arm-portal.md) modułów równoważenia obciążenia. Dodawanie wielu frontonów jest przyrostem do jednej konfiguracji frontonu. Korzystając z koncepcji w tym artykule, można w dowolnym momencie rozszerzyć uproszczoną konfigurację.

Podczas definiowania Azure Load Balancer konfiguracja frontonu i puli zaplecza są połączone z regułami. Sonda kondycji, do której odwołuje się reguła, służy do określania, w jaki sposób Nowe przepływy są wysyłane do węzła w puli zaplecza. Fronton (alias VIP) jest definiowany przez 3-krotkę składającą się z adresu IP (publicznego lub wewnętrznego), protokołu transportowego (UDP lub TCP) i numeru portu z reguły równoważenia obciążenia. Pula zaplecza jest kolekcją konfiguracji protokołu IP maszyn wirtualnych (część zasobu karty sieciowej), która odwołuje się do puli zaplecza Load Balancer.

Poniższa tabela zawiera kilka przykładowych konfiguracji frontonu:

| Fronton | Adres IP | protokol | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

W tabeli przedstawiono cztery różne frontony. Frontony #1, #2 i #3 są pojedynczymi frontonami z wieloma regułami. Ten sam adres IP jest używany, ale port lub protokół różni się dla każdego frontonu. Frontony #1 i #4 są przykładem wielu frontonów, w których ten sam protokół frontonu i port są ponownie używane dla wielu frontonów.

Azure Load Balancer zapewnia elastyczność w definiowaniu reguł równoważenia obciążenia. Reguła deklaruje, jak adres i port frontonu są mapowane na adres docelowy i port w zapleczu. Czy porty zaplecza są ponownie używane między regułami, zależy od typu reguły. Każdy typ reguły ma określone wymagania, które mogą mieć wpływ na konfigurację hosta i projekt sondy. Istnieją dwa typy reguł:

1. Reguła domyślna bez użycia portu zaplecza
2. Reguła zmiennoprzecinkowych adresów IP, w której są ponownie używane porty zaplecza

Azure Load Balancer umożliwia mieszanie obu typów reguł w tej samej konfiguracji modułu równoważenia obciążenia. Moduł równoważenia obciążenia może używać ich jednocześnie dla danej maszyny wirtualnej lub dowolnej kombinacji, o ile są one przestrzegane przez ograniczenia reguły. Wybór typu reguły zależy od wymagań aplikacji i złożoności obsługi tej konfiguracji. Należy oszacować, które typy reguł najlepiej sprawdzają się w danym scenariuszu.

Te scenariusze są bardziej szczegółowe, zaczynając od zachowania domyślnego.

## <a name="rule-type-1-no-backend-port-reuse"></a>#1 typu reguły: nie jest używany żaden port zaplecza

![Ilustracja przedstawiająca wiele frontonów z zielonym i fioletowym frontonem](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

W tym scenariuszu frontony są konfigurowane w następujący sposób:

| Fronton | Adres IP | protokol | port |
| --- | --- | --- | --- |
| ![zielony fronton](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Purpurowy fronton](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP jest miejscem docelowym przepływu przychodzącego. W puli zaplecza każda maszyna wirtualna uwidacznia żądaną usługę na unikatowym porcie dla adresu DIP. Ta usługa jest skojarzona z frontonem za pomocą definicji reguły.

Definiujemy dwie reguły:

| Reguła | Fronton mapy | Do puli zaplecza |
| --- | --- | --- |
| 1 |![zielony fronton](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![zielone zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![zielone zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![Adres VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![purpurowe zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![purpurowe zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Pełne mapowanie w Azure Load Balancer jest teraz następujące:

| Reguła | Adres IP frontonu | protokol | port | Element docelowy | port |
| --- | --- | --- | --- | --- | --- |
| ![Zielona reguła](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Adres IP DIP |80 |
| ![Reguła purpurowa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Adres IP DIP |81 |

Każda reguła musi generować przepływ z unikatową kombinacją docelowego adresu IP i portu docelowego. Przez zróżnicowanie portu docelowego przepływu, wiele reguł może dostarczać przepływy do tego samego DIP na różnych portach.

Sondy kondycji są zawsze kierowane do dedykowanego adresu IP maszyny wirtualnej. Musisz się upewnić, że sonda odzwierciedla kondycję maszyny wirtualnej.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Typ reguły #2: ponowne użycie portu zaplecza przy użyciu zmiennoprzecinkowego adresu IP

Azure Load Balancer zapewnia elastyczność ponownego użycia portu frontonu dla wielu frontonów, niezależnie od użytego typu reguły. Ponadto niektóre scenariusze aplikacji wolą lub wymagają, aby ten sam port był używany przez wiele wystąpień aplikacji na jednej maszynie wirtualnej w puli zaplecza. Typowe przykłady ponownego użycia portów obejmują klastrowanie dla wysokiej dostępności, sieciowych urządzeń wirtualnych i udostępnianie wielu punktów końcowych protokołu TLS bez ponownego szyfrowania.

Jeśli chcesz ponownie użyć portu zaplecza dla wielu reguł, musisz włączyć przestawne adresy IP w definicji reguły.

"Pływający adres IP" to terminologia platformy Azure dla części tego, co jest znane jako bezpośrednie zwrócenie serwera (DSR). DSR składa się z dwóch części: topologii przepływu i schematu mapowania adresów IP. Na poziomie platformy Azure Load Balancer zawsze działa w topologii przepływu DSR, niezależnie od tego, czy jest włączony swobodny adres IP. Oznacza to, że wychodząca część przepływu jest zawsze poprawnie zapisywana do przepływu bezpośrednio z powrotem do źródła.

W przypadku domyślnego typu reguły platforma Azure udostępnia tradycyjny schemat mapowania adresów IP równoważenia obciążenia na potrzeby łatwego użytkowania. Włączenie pływającego adresu IP powoduje zmianę schematu mapowania adresów IP, aby umożliwić dodatkową elastyczność, jak wyjaśniono poniżej.

Na poniższym diagramie przedstawiono tę konfigurację:

![Ilustracja przedstawiająca wiele frontonów z zielonym i fioletowym frontonem przy użyciu DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

W tym scenariuszu wszystkie maszyny wirtualne w puli zaplecza mają trzy interfejsy sieciowe:

* DIP: wirtualna karta sieciowa skojarzona z MASZYNą wirtualną (Konfiguracja IP zasobu karty interfejsu sieciowego platformy Azure)
* Fronton 1: interfejs sprzężenia zwrotnego w systemie operacyjnym gościa skonfigurowany przy użyciu adresu IP frontonu 1
* Fronton 2: interfejs sprzężenia zwrotnego w systemie operacyjnym gościa skonfigurowany przy użyciu adresu IP frontonu 2

W przypadku każdej maszyny wirtualnej w puli zaplecza Uruchom następujące polecenia w wierszu polecenia systemu Windows.

Aby uzyskać listę nazw interfejsów, które znajdują się na maszynie wirtualnej, wpisz następujące polecenie:

```console
netsh interface show interface 
```

Dla karty sieciowej maszyny wirtualnej (zarządzanej przez platformę Azure) wpisz następujące polecenie:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
```

(Zastąp wartość InterfaceName nazwą tego interfejsu)

Dla każdego dodanego interfejsu sprzężenia zwrotnego Powtórz następujące polecenia:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
```

(Zastąp wartość InterfaceName nazwą tego interfejsu sprzężenia zwrotnego)

```console
netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
```

(Zastąp wartość InterfaceName nazwą tego interfejsu sprzężenia zwrotnego)

> [!IMPORTANT]
> Konfiguracja interfejsów sprzężenia zwrotnego jest wykonywana w systemie operacyjnym gościa. Ta konfiguracja nie jest wykonywana ani zarządzana przez platformę Azure. Bez tej konfiguracji reguły nie będą działać. Definicje sond kondycji używają adresu DIP maszyny wirtualnej, a nie interfejsu sprzężenia zwrotnego reprezentującego fronton DSR. W związku z tym usługa musi dostarczyć odpowiedzi na port DIP, który odzwierciedla stan usługi oferowanej w interfejsie sprzężenia zwrotnego reprezentującym fronton DSR.


Załóżmy, że konfiguracja frontonu jest taka sama jak w poprzednim scenariuszu:

| Fronton | Adres IP | protokol | port |
| --- | --- | --- | --- |
| ![zielony fronton](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Purpurowy fronton](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Definiujemy dwie reguły:

| Reguła | Fronton | Mapowanie na pulę zaplecza |
| --- | --- | --- |
| 1 |![Zielona reguła](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![zielone zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (w VM1 i VM2) |
| 2 |![Reguła purpurowa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![purpurowe zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (w VM1 i VM2) |

W poniższej tabeli przedstawiono pełne mapowanie modułu równoważenia obciążenia:

| Reguła | Adres IP frontonu | protokol | port | Element docelowy | port |
| --- | --- | --- | --- | --- | --- |
| ![Zielona reguła](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Analogicznie jak fronton (65.52.0.1) |Analogicznie jak fronton (80) |
| ![Reguła purpurowa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Analogicznie jak fronton (65.52.0.2) |Analogicznie jak fronton (80) |

Miejscem docelowym przepływu ruchu przychodzącego jest adres IP frontonu w interfejsie sprzężenia zwrotnego w maszynie wirtualnej. Każda reguła musi generować przepływ z unikatową kombinacją docelowego adresu IP i portu docelowego. Zmieniając docelowy adres IP przepływu, możliwe jest ponowne użycie portów na tej samej maszynie wirtualnej. Usługa jest udostępniana usłudze równoważenia obciążenia przez powiązanie jej z adresem IP i portem odpowiedniego interfejsu sprzężenia zwrotnego.

Należy zauważyć, że w tym przykładzie nie jest zmieniany port docelowy. Mimo że jest to przepływający scenariusz IP, Azure Load Balancer obsługuje także Definiowanie reguły, aby ponownie zapisać port docelowy zaplecza i uczynić go innym względem portu docelowego frontonu.

Typ reguły zmiennoprzecinkowy adres IP jest podstawą kilku wzorców konfiguracji usługi równoważenia obciążenia. Jednym z przykładów, które jest obecnie dostępne, jest funkcja [SQL AlwaysOn z wieloma odbiornikami](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) konfiguracji. W miarę upływu czasu będzie można udokumentować więcej takich scenariuszy.

## <a name="limitations"></a>Ograniczenia

* Wiele konfiguracji frontonu jest obsługiwanych tylko z maszynami wirtualnymi IaaS.
* W przypadku reguły zmiennoprzecinkowych adresów IP aplikacja musi używać podstawowej konfiguracji protokołu IP dla przepływów ruchu wychodzącego. Jeśli aplikacja jest powiązana z adresem IP frontonu skonfigurowanym w interfejsie sprzężenia zwrotnego w systemie operacyjnym gościa, ruch wychodzący z platformy Azure nie jest dostępny do ponownego zapisania przepływu wychodzącego i przepływ kończy się niepowodzeniem.  Przejrzyj [scenariusze wychodzące](load-balancer-outbound-connections.md).
* Zmienny adres IP nie jest obecnie obsługiwany w konfiguracjach pomocniczych adresów IP dla wewnętrznych scenariuszy równoważenia obciążenia.
* Publiczne adresy IP mają wpływ na rozliczenia. Aby uzyskać więcej informacji, zobacz [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Obowiązują limity subskrypcji. Aby uzyskać więcej informacji, zobacz [limity usług](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) w celu uzyskania szczegółów.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [połączenia wychodzące](load-balancer-outbound-connections.md) , aby zrozumieć wpływ wielu frontonów na zachowanie połączenia wychodzącego.
