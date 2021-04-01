---
title: Zabezpieczenia sieciowe Azure Event Grid zasobów
description: W tym artykule opisano sposób używania tagów usługi dla ruchu wychodzącego, reguł zapory protokołu IP na potrzeby transferu danych przychodzących i prywatnych punktów końcowych przy użyciu Azure Event Grid.
author: VidyaKukke
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: vkukke
ms.openlocfilehash: 10c9b165041f0a4a1f09511f17bef3629353c3b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94917532"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Zabezpieczenia sieciowe Azure Event Grid zasobów
W tym artykule opisano sposób korzystania z następujących funkcji zabezpieczeń w Azure Event Grid: 

- Tagi usług dla ruchu wychodzącego
- Reguły zapory IP dla ruchu przychodzącego
- Prywatne punkty końcowe dla ruchu przychodzącego


## <a name="service-tags"></a>Tagi usługi
Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych. Aby uzyskać więcej informacji na temat tagów usługi, zobacz [Omówienie tagów usług](../virtual-network/service-tags-overview.md).

Za pomocą tagów usługi można definiować kontrolę dostępu do sieci w [grupach zabezpieczeń sieci](../virtual-network/network-security-groups-overview.md#security-rules) lub w [zaporze platformy Azure](../firewall/service-tags.md). Podczas tworzenia reguł zabezpieczeń należy używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład **AzureEventGrid**) w odpowiednim polu *źródłowym* lub *docelowym* reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić.

| Tag usługi | Przeznaczenie | Może korzystać z ruchu przychodzącego lub wychodzącego? | Może być regionalna? | Czy można używać z zaporą platformy Azure? |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Azure Event Grid. | Oba | Nie | Nie |


## <a name="ip-firewall"></a>Zapora IP 
Azure Event Grid obsługuje kontrolę dostępu opartą na protokole IP do publikowania w tematach i domenach. Za pomocą kontrolek opartych na protokole IP można ograniczyć wydawców do tematu lub domeny tylko do zestawu zatwierdzonego zestawu maszyn i usług w chmurze. Ta funkcja uzupełnia [mechanizmy uwierzytelniania](security-authentication.md) obsługiwane przez Event Grid.

Domyślnie temat i domena są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją tylko do zestawu adresów IP lub zakresów adresów IP w notacji [CIDR (bez klas Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Wydawcy pochodzące z dowolnego innego adresu IP będą odrzucani i otrzymają odpowiedź 403 (zabroniony).

Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania zapory IP dla tematów i domen, zobacz [Konfigurowanie zapory IP](configure-firewall.md).

## <a name="private-endpoints"></a>Prywatne punkty końcowe
Możesz użyć [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) , aby umożliwić bezpieczne wykonywanie zdarzeń bezpośrednio z sieci wirtualnej do Twoich tematów i domen za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md) bez pośrednictwa publicznego Internetu. Prywatny punkt końcowy jest specjalnym interfejsem sieciowym dla usługi platformy Azure w sieci wirtualnej. Gdy tworzysz prywatny punkt końcowy dla tematu lub domeny, zapewnia on bezpieczną łączność między klientami w sieci wirtualnej i zasobem Event Grid. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym a usługą Event Grid używa bezpiecznego linku prywatnego.

![Diagram architektury](./media/network-security/architecture-diagram.png)

Używanie prywatnych punktów końcowych dla zasobu Event Grid pozwala:

- Bezpieczny dostęp do tematu lub domeny z sieci wirtualnej przez sieć szkieletową firmy Microsoft w przeciwieństwie do publicznego Internetu.
- Bezpiecznie łącz się z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu sieci VPN lub usługi Express przy użyciu komunikacji równorzędnej.

W przypadku tworzenia prywatnego punktu końcowego tematu lub domeny w sieci wirtualnej do zatwierdzenia Właściciel zasobu jest wysyłana prośba o zgodę. Jeśli użytkownik żądający utworzenia prywatnego punktu końcowego jest również właścicielem zasobu, to żądanie zgody jest automatycznie zatwierdzane. W przeciwnym razie połączenie jest w stanie **oczekiwania** , dopóki nie zostanie zatwierdzone. Aplikacje w sieci wirtualnej mogą bezproblemowo łączyć się z usługą Event Grid za pośrednictwem prywatnego punktu końcowego, używając tych samych parametrów połączenia i mechanizmów autoryzacji, które mogą być używane w inny sposób. Właściciele zasobów mogą zarządzać żądaniami zgody i prywatnymi punktami końcowymi za pomocą karty **prywatne punkty końcowe** dla zasobu w Azure Portal.

### <a name="connect-to-private-endpoints"></a>Nawiązywanie połączenia z prywatnymi punktami końcowymi
Wydawcy w sieci wirtualnej korzystającej z prywatnego punktu końcowego powinni używać tych samych parametrów połączenia dla tematu lub domeny, co w przypadku klientów nawiązujących połączenie z publicznym punktem końcowym. Funkcja rozpoznawania nazw DNS automatycznie kieruje połączenia z sieci wirtualnej do tematu lub domeny za pośrednictwem prywatnego linku. Event Grid domyślnie tworzy [prywatną strefę DNS](../dns/private-dns-overview.md) dołączoną do sieci wirtualnej z niezbędną aktualizacją prywatnych punktów końcowych. Jeśli jednak używasz własnego serwera DNS, może być konieczne wprowadzenie dodatkowych zmian w konfiguracji DNS.

### <a name="dns-changes-for-private-endpoints"></a>Zmiany w systemie DNS dla prywatnych punktów końcowych
Podczas tworzenia prywatnego punktu końcowego rekord CNAME DNS dla zasobu zostanie zaktualizowany do aliasu w poddomenie z prefiksem `privatelink` . Domyślnie zostanie utworzona prywatna strefa DNS odpowiadająca poddomenie linku prywatnego. 

W przypadku rozwiązywania tematu lub adresu URL punktu końcowego domeny spoza sieci wirtualnej z prywatnym punktem końcowym jest on rozpoznawany jako publiczny punkt końcowy usługi. Rekordy zasobów DNS dla "tematu", po rozwiązaniu spoza sieci **wirtualnej** obsługującej prywatny punkt końcowy, będą:

| Nazwa                                          | Typ      | Wartość                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

Możesz odmówić dostępu do klienta lub kontrolować go poza siecią wirtualną za pośrednictwem publicznego punktu końcowego za pomocą [zapory IP](#ip-firewall). 

Po rozwiązaniu problemu z siecią wirtualną, w której jest przechowywany prywatny punkt końcowy, temat lub adres URL punktu końcowego domeny jest rozpoznawany jako adres IP prywatnego punktu końcowego. Rekordy zasobów DNS dla tematu "temat", po rozwiązaniu z wewnątrz sieci **wirtualnej** obsługującej prywatny punkt końcowy, będą:

| Nazwa                                          | Typ      | Wartość                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Takie podejście umożliwia dostęp do tematu lub domeny przy użyciu tych samych parametrów połączenia dla klientów w sieci wirtualnej obsługujących prywatne punkty końcowe oraz klientów spoza sieci wirtualnej.

Jeśli używasz niestandardowego serwera DNS w sieci, klienci mogą rozpoznać nazwę FQDN tematu lub domeny punktu końcowego jako adres IP prywatnego punktu końcowego. Skonfiguruj serwer DNS w celu delegowania poddomeny prywatnego linku do prywatnej strefy DNS dla sieci wirtualnej lub skonfiguruj rekordy A dla `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` za pomocą prywatnego adresu IP punktu końcowego.

Zalecaną nazwą strefy DNS jest `privatelink.eventgrid.azure.net` .

### <a name="private-endpoints-and-publishing"></a>Prywatne punkty końcowe i publikowanie

W poniższej tabeli opisano różne stany połączenia prywatnego punktu końcowego oraz efekty publikowania:

| Stan połączenia   |  Pomyślne opublikowanie (tak/nie) |
| ------------------ | -------------------------------|
| Approved (Zatwierdzono)           | Tak                            |
| Odrzucone           | Nie                             |
| Oczekiwanie            | Nie                             |
| Odłączony       | Nie                             |

Aby publikowanie zakończyło się pomyślnie, stan połączenia prywatnego punktu końcowego powinien zostać **zatwierdzony**. Jeśli połączenie zostanie odrzucone, nie można go zatwierdzić przy użyciu Azure Portal. Jedyną możliwością jest usunięcie połączenia i utworzenie nowego.

## <a name="pricing-and-quotas"></a>Cennik i przydziały
**Prywatne punkty końcowe** są dostępne zarówno w warstwach Podstawowa, jak i Premium Event Grid. Event Grid umożliwia tworzenie do 64 połączeń prywatnych punktów końcowych na temat lub domenę. 

Funkcja **Zapora IP** jest dostępna w warstwach Podstawowa i Premium Event Grid. Zezwalamy na tworzenie do 16 reguł zapory IP dla tematu lub domeny.

## <a name="next-steps"></a>Następne kroki
Można skonfigurować zaporę IP dla zasobu Event Grid, aby ograniczyć dostęp za pośrednictwem publicznej sieci Internet tylko z poziomu wybranych adresów IP lub zakresów adresów IP. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie zapory IP](configure-firewall.md).

Można skonfigurować prywatne punkty końcowe, aby ograniczyć dostęp tylko z wybranych sieci wirtualnych. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie prywatnych punktów końcowych](configure-private-endpoints.md).

Aby rozwiązać problemy z łącznością sieciową, zobacz [Rozwiązywanie problemów z łącznością sieciową](troubleshoot-network-connectivity.md)