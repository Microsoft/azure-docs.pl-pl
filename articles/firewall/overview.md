---
title: Co to jest usługa Azure Firewall?
description: Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 04/20/2021
ms.author: victorh
ms.openlocfilehash: 9ed46af7e4c62b2b7213b9e7a3d71c1b4776c806
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835348"
---
# <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

![Certyfikacja ICSA](media/overview/icsa-cert-firewall-small.png)

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa zapora jako usługa z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze.

![Omówienie zapory](media/overview/firewall-threat.png)

Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych. Usługa Azure Firewall korzysta ze statycznego publicznego adresu IP dla zasobów sieci wirtualnej, co umożliwia zewnętrznym zaporom identyfikowanie ruchu pochodzącego z sieci wirtualnej.  Usługa jest w pełni zintegrowana z usługą Azure Monitor na potrzeby rejestrowania i analiz.

Aby dowiedzieć się więcej Azure Firewall funkcji, zobacz [Azure Firewall funkcji](features.md).

## <a name="azure-firewall-premium-preview"></a>Azure Firewall Premium (wersja zapoznawcza)

Azure Firewall Premium to zapora nowej generacji z funkcjami wymaganymi w środowiskach ściśle poufnych i podlegających regulacjom. Te możliwości obejmują inspekcję TLS, adresy IP, filtrowanie adresów URL i kategorie sieci Web.

Aby dowiedzieć się więcej o Azure Firewall Premium w wersji zapoznawczej, zobacz [Azure Firewall Premium w wersji zapoznawczej.](premium-features.md)


Aby zobaczyć, jak skonfigurowano wersję zapoznawczą Usługi Firewall Premium w wersji Azure Portal, zobacz Azure Firewall [Premium Preview w oknie Azure Portal.](premium-portal.md)


## <a name="pricing-and-sla"></a>Cennik i umowa SLA

Aby Azure Firewall informacje o cenach, zobacz [Azure Firewall cennik.](https://azure.microsoft.com/pricing/details/azure-firewall/)

Aby uzyskać Azure Firewall sla, zobacz Azure Firewall SLA ( [Umowa SLA).](https://azure.microsoft.com/support/legal/sla/azure-firewall/)

## <a name="whats-new"></a>Co nowego

Aby dowiedzieć się, co nowego w systemie Azure Firewall, zobacz [Aktualizacje platformy Azure.](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall)


## <a name="known-issues"></a>Znane problemy

W usłudze Azure Firewall występują następujące znane problemy:

|Problem  |Description  |Ograniczanie ryzyka  |
|---------|---------|---------|
|Reguły filtrowania dla protokołów innych niż TCP/UDP (na przykład ICMP) nie działają dla ruchu powiązanego z Internetem|Reguły filtrowania sieci dla protokołów innych niż TCP/UDP nie działają z SNAT na publicznym adresie IP. Protokoły inne niż TCP/UDP są obsługiwane między podsieciami szprych i sieciami wirtualnymi.|Usługa Azure Firewall korzysta ze standardowego modułu równoważenia obciążenia, [który obecnie nie obsługuje funkcji SNAT dla protokołów IP](../load-balancer/load-balancer-overview.md). Poznaliśmy opcje obsługi tego scenariusza w przyszłej wersji.|
|Brak obsługi protokołu ICMP w programie PowerShell i interfejsie wiersza polecenia|Azure PowerShell i interfejs wiersza polecenia nie obsługują protokołu ICMP jako prawidłowego protokołu w zasadach sieci.|Nadal można używać protokołu ICMP za pośrednictwem portalu i interfejsu API REST. Pracujemy nad dodaniem wkrótce ICMP w programie PowerShell i interfejsie wiersza polecenia.|
|Tagi FQDN wymagają ustawienia protokołu i portu|Reguły aplikacji z tagami FQDN wymagają definicji portu: protokołu.|Jako wartości portu i protokołu można użyć wartości **https**. Pracujemy nad tym, aby to pole było opcjonalne, gdy są używane tagi FQDN.|
|Przenoszenie zapory do innej grupy zasobów lub subskrypcji nie jest obsługiwane|Przenoszenie zapory do innej grupy zasobów lub subskrypcji nie jest obsługiwane.|Obsługa tej funkcji jest w naszym harmonogramie działania. Aby przenieść zaporę do innej grupy zasobów lub subskrypcji, musisz usunąć bieżące wystąpienie i utworzyć je ponownie w nowej grupie zasobów lub subskrypcji.|
|Alerty analizy zagrożeń mogą być maskowane|Reguły sieci z miejscem docelowym 80/443 dla filtrowania ruchu wychodzącego maskują alerty analizy zagrożeń, gdy są skonfigurowane do trybu tylko alertów.|Utwórz filtrowanie ruchu wychodzącego dla 80/443 przy użyciu reguł aplikacji. Możesz też zmienić tryb analizy zagrożeń na Alert **i Odmów**.|
|Azure Firewall DNAT nie działa w przypadku prywatnych miejsc docelowych adresów IP|Azure Firewall DNAT jest ograniczona do danych wychodzących/przychodzących z Internetu. Technologia DNAT nie działa obecnie w przypadku prywatnych miejsc docelowych adresów IP. Na przykład szprycha do szprychy.|Jest to bieżące ograniczenie.|
|Nie można usunąć pierwszej konfiguracji publicznego adresu IP|Każdy Azure Firewall publiczny adres IP jest przypisywany do *konfiguracji adresu IP*.  Pierwsza konfiguracja adresu IP jest przypisywana podczas wdrażania zapory i zazwyczaj zawiera również odwołanie do podsieci zapory (chyba że została jawnie skonfigurowana inaczej za pośrednictwem wdrożenia szablonu). Nie można usunąć tej konfiguracji adresu IP, ponieważ spowoduje to cokolenie alokacji zapory. Nadal można zmienić lub usunąć publiczny adres IP skojarzony z tą konfiguracją adresu IP, jeśli zapora ma co najmniej jeden inny publiczny adres IP dostępny do użycia.|Jest to celowe.|
|Strefy dostępności można konfigurować tylko podczas wdrażania.|Strefy dostępności można konfigurować tylko podczas wdrażania. Nie można skonfigurować Strefy dostępności po wdrożeniu zapory.|Jest to celowe.|
|SNAT dla połączeń przychodzących|Oprócz DNAT połączenia za pośrednictwem publicznego adresu IP zapory (przychodzącego) są przekierowywały ruch sieciowy do jednego z prywatnych adresów IP zapory. To wymaganie jest obecnie wymagane (również dla aktywnych/aktywnych urządzeń WUS) w celu zapewnienia routingu symetrycznego.|Aby zachować oryginalne źródło dla protokołu HTTP/S, rozważ użycie [nagłówków XFF.](https://en.wikipedia.org/wiki/X-Forwarded-For) Na przykład użyj usługi takiej jak [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) lub [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) przed zaporą. Zaporę aplikacji internetowej można również dodać jako część Azure Front Door i utworzyć łańcuch z zaporą.
|Filtrowanie FQDN SQL obsługuje tylko w trybie serwera proxy (port 1433)|W Azure SQL Database, Azure Synapse Analytics i Azure SQL Managed Instance:<br><br>Filtrowanie WQDN SQL jest obsługiwane tylko w trybie serwera proxy (port 1433).<br><br>Na Azure SQL IaaS:<br><br>Jeśli używasz niestandardowych portów, możesz określić te porty w zasadach aplikacji.|W przypadku bazy danych SQL w trybie przekierowania (ustawienie domyślne w przypadku nawiązywania połączenia z poziomu platformy Azure) można zamiast tego filtrować dostęp przy użyciu tagu usługi SQL w ramach Azure Firewall sieci.
|Ruch wychodzący na porcie TCP 25 jest niedozwolone| Wychodzące połączenia SMTP, które używają portu TCP 25, są blokowane. Port 25 jest używany głównie do dostarczania nieuwierzytanych wiadomości e-mail. Jest to domyślne zachowanie platformy dla maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz Rozwiązywanie [problemów z wychodzącymi połączeniami SMTP na platformie Azure.](../virtual-network/troubleshoot-outbound-smtp-connectivity.md) Jednak w przeciwieństwie do maszyn wirtualnych obecnie nie można włączyć tej funkcji na Azure Firewall. Uwaga: aby zezwolić na uwierzytelniony protokół SMTP (port 587) lub SMTP za pośrednictwem portu innego niż 25, upewnij się, że skonfigurowano regułę sieci, a nie regułę aplikacji, ponieważ inspekcja SMTP nie jest obecnie obsługiwana.|Postępuj zgodnie z zalecaną metodą wysyłania wiadomości e-mail, zgodnie z dokumentacją w artykule na temat rozwiązywania problemów z smtp. Możesz też wykluczyć maszynę wirtualną, która wymaga wychodzącego dostępu SMTP, z trasy domyślnej do zapory. Zamiast tego skonfiguruj dostęp wychodzący bezpośrednio do Internetu.
|Wyczerpanie portów SNAT|Azure Firewall obecnie obsługuje 1024 porty na publiczny adres IP na wystąpienie zestawu skalowania maszyn wirtualnych zaplecza. Domyślnie istnieją dwa wystąpienia usługi VMSS.|Jest to ograniczenie SLB i stale szukamy możliwości zwiększenia limitów. W międzyczasie zaleca się skonfigurowanie wdrożeń Azure Firewall z co najmniej pięcioma publicznymi adresami IP dla wdrożeń podatnych na wyczerpanie SNAT. Powoduje to pięciokrotnie zwiększenie dostępnych portów SNAT. Przydziel z prefiksu adresu IP, aby uprościć uprawnienia podrzędne.|
|Funkcja DNAT nie jest obsługiwana z włączonym wymuszonym tunelowaniem|Zapory wdrożone z włączonym wymuszonym tunelowaniem nie mogą obsługiwać dostępu przychodzącego z Internetu z powodu routingu asymetrycznego.|Jest to spowodowane routingiem asymetrycznym. Ścieżka powrotna dla połączeń przychodzących przechodzi przez lokalną zaporę, która nie widziała nawiązanego połączenia.
|Wychodzący pasywny protokół FTP może nie działać w przypadku zapór z wieloma publicznymi adresami IP, w zależności od konfiguracji serwera FTP.|Pasywny protokół FTP ustanawia różne połączenia dla kanałów kontroli i danych. Gdy zapora z wieloma publicznymi adresami IP wysyła dane wychodzące, losowo wybiera jeden ze swoich publicznych adresów IP jako źródłowy adres IP. Protokół FTP może ulec awarii, gdy kanały danych i kanały sterowania używają różnych źródłowych adresów IP, w zależności od konfiguracji serwera FTP.|Planowana jest jawna konfiguracja SNAT. W międzyczasie można skonfigurować serwer FTP tak, aby akceptował dane i kanały kontroli z różnych źródłowych adresów IP (zobacz przykład dla [usług IIS).](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity) W takiej sytuacji możesz też rozważyć użycie jednego adresu IP.|
|Pasywny ruch przychodzący FTP może nie działać w zależności od konfiguracji serwera FTP |Pasywny protokół FTP ustanawia różne połączenia dla kanałów kontroli i danych. Połączenia przychodzące na Azure Firewall są SNAT do jednego z prywatnych adresów IP zapory w celu zapewnienia routingu symetrycznego. Protokół FTP może ulec awarii, gdy kanały danych i kontroli używają różnych źródłowych adresów IP, w zależności od konfiguracji serwera FTP.|Zachowywanie oryginalnego źródłowego adresu IP jest badane. W międzyczasie można skonfigurować serwer FTP tak, aby akceptował dane i kanały kontroli z różnych źródłowych adresów IP.|
|Metryka NetworkRuleHit nie ma wymiaru protokołu|Metryka ApplicationRuleHit umożliwia korzystanie z protokołu opartego na filtrowaniu, ale tej możliwości brakuje w odpowiedniej metryce NetworkRuleHit.|Poprawka jest badana.|
|Reguły NAT z portami od 64000 do 65535 nie są obsługiwane|Azure Firewall zezwala na każdy port z zakresu od 1 do 65535 w zasadach sieci i aplikacji, jednak reguły NAT obsługują tylko porty z zakresu od 1 do 63999.|Jest to bieżące ograniczenie.
|Aktualizacje konfiguracji mogą średnio potrwać pięć minut|Aktualizacja Azure Firewall konfiguracji może średnio potrwać od trzech do pięciu minut, a aktualizacje równoległe nie są obsługiwane.|Poprawka jest badana.|
|Azure Firewall używa nagłówków SNI TLS do filtrowania ruchu HTTPS i MSSQL|Jeśli przeglądarka lub oprogramowanie serwera nie obsługuje rozszerzenia wskaźnika nazwy serwera (SNI), nie można nawiązać połączenia za pośrednictwem Azure Firewall.|Jeśli oprogramowanie przeglądarki lub serwera nie obsługuje funkcji SNI, możesz kontrolować połączenie przy użyciu reguły sieci zamiast reguły aplikacji. Zobacz [Oznaczanie nazwy serwera](https://wikipedia.org/wiki/Server_Name_Indication) oprogramowania, które obsługuje funkcję SNI.|
|Niestandardowy system DNS nie działa z wymuszonym tunelowaniem|Jeśli włączono tunelowanie wymuszające, niestandardowy system DNS nie działa.|Poprawka jest badana.|
|Uruchamianie/zatrzymywanie nie działa z zaporą skonfigurowaną w trybie wymuszonego tunelowania|Uruchamianie/zatrzymywanie nie działa z zaporą platformy Azure skonfigurowaną w trybie wymuszonego tunelowania. Próba uruchomienia aplikacji Azure Firewall z wymuszonym tunelowaniem powoduje następujący błąd:<br><br>*Set-AzFirewall: Konfiguracji adresu IP zarządzania AzureFirewall FW-xx nie można dodać do istniejącej zapory. Jeśli chcesz użyć obsługi wymuszonego tunelowania, wdeń ponownie z konfiguracją adresu IP zarządzania. <br> StatusCode: 400 ReasonPhrase: Bad Request (Kod stanu: 400 <br> ReasonPhrase: Złe żądanie)*|Trwa badanie.<br><br>Aby obejść ten problem, możesz usunąć istniejącą zaporę i utworzyć nową zaporę z tym samymi parametrami.|
|Nie można dodać tagów zasad zapory przy użyciu portalu|Azure Firewall Policy ma ograniczenie obsługi poprawek, które uniemożliwia dodanie tagu przy użyciu Azure Portal. Zostanie wygenerowany następujący błąd: *Nie można zapisać tagów dla zasobu*.|Poprawka jest badana. Możesz też użyć polecenia cmdlet Azure PowerShell, `Set-AzFirewallPolicy` aby zaktualizować tagi.|
|Protokół IPv6 nie jest jeszcze obsługiwany|Jeśli dodasz adres IPv6 do reguły, zapora ulegnie awarii.|Używaj tylko adresów IPv4. Trwa badanie pomocy technicznej dotyczącej protokołu IPv6.|
|Aktualizowanie wielu grup adresów IP kończy się niepowodzeniem z błędem konfliktu.|Po zaktualizowaniu co najmniej dwóch grup IP podłączonych do tej samej zapory jeden z zasobów przechodzi w stan awarii.|Jest to znany problem/ograniczenie. <br><br>Aktualizacja grupy IPGroup wyzwala aktualizację wszystkich zapór, do których jest dołączona grupa IPGroup. Jeśli aktualizacja drugiej grupy IPGroup jest uruchomiona,  gdy zapora jest nadal w stanie Aktualizowanie, aktualizacja IPGroup kończy się niepowodzeniem.<br><br>Aby uniknąć awarii, grupy IPGroup dołączone do tej samej zapory muszą być aktualizowane po jednej na raz. Pozwól na wystarczającą ilość czasu między aktualizacjami, aby zapora wyestała ze *stanu* aktualizacji.| 


## <a name="next-steps"></a>Następne kroki

- [Szybki start: tworzenie zasad Azure Firewall zapory — szablon usługi ARM](../firewall-manager/quick-firewall-policy.md)
- [Szybki start: wdrażanie aplikacji Azure Firewall pomocą Strefy dostępności — szablon usługi ARM](deploy-template.md)
- [Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal](tutorial-firewall-deploy-portal.md)