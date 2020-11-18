---
title: Co to jest usługa Azure Firewall?
description: Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperfq1
ms.date: 11/10/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: e714e88e47ec20adec44a104c659d03e62d8010a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658387"
---
# <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

![Certyfikat ICSA](media/overview/icsa-cert-firewall-small.png)

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa zapora jako usługa z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze.

![Omówienie zapory](media/overview/firewall-threat.png)

Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych. Usługa Azure Firewall korzysta ze statycznego publicznego adresu IP dla zasobów sieci wirtualnej, co umożliwia zewnętrznym zaporom identyfikowanie ruchu pochodzącego z sieci wirtualnej.  Usługa jest w pełni zintegrowana z usługą Azure Monitor na potrzeby rejestrowania i analiz.

## <a name="features"></a>Funkcje

Aby dowiedzieć się więcej o funkcjach zapory platformy Azure, zobacz [funkcje zapory platformy Azure](features.md).

## <a name="pricing-and-sla"></a>Cennik i Umowa SLA

Aby uzyskać informacje o cenach zapory platformy Azure, zobacz [Cennik usługi Azure firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

Informacje o umowie SLA zapory platformy Azure można znaleźć w temacie [Umowa SLA zapory platformy Azure](https://azure.microsoft.com/support/legal/sla/azure-firewall/).

## <a name="whats-new"></a>Co nowego

Aby dowiedzieć się, co nowego w zaporze platformy Azure, zobacz [aktualizacje platformy Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall).


## <a name="known-issues"></a>Znane problemy

W usłudze Azure Firewall występują następujące znane problemy:

|Problem  |Description  |Ograniczanie ryzyka  |
|---------|---------|---------|
Reguły filtrowania dla protokołów innych niż TCP/UDP (na przykład ICMP) nie działają dla ruchu powiązanego z Internetem|Reguły filtrowania sieci dla protokołów innych niż TCP/UDP nie działają z przystawcy do publicznego adresu IP. Protokoły inne niż TCP/UDP są obsługiwane między podsieciami szprych i sieciami wirtualnymi.|Usługa Azure Firewall korzysta ze standardowego modułu równoważenia obciążenia, [który obecnie nie obsługuje funkcji SNAT dla protokołów IP](../load-balancer/load-balancer-overview.md). Szukamy opcji obsługi tego scenariusza w przyszłej wersji.|
|Brak obsługi protokołu ICMP w programie PowerShell i interfejsie wiersza polecenia|Azure PowerShell i interfejs wiersza polecenia nie obsługują protokołu ICMP jako prawidłowego protokołu w regułach sieci.|Nadal jest możliwe używanie protokołu ICMP jako protokołu za pośrednictwem portalu i interfejsu API REST. Pracujemy nad dodaniem protokołu ICMP w programie PowerShell i interfejsie wiersza polecenia.|
|Tagi FQDN wymagają ustawienia protokołu i portu|Reguły aplikacji ze znacznikami FQDN wymagają portu: Definicja protokołu.|Jako wartości portu i protokołu można użyć wartości **https**. Pracujemy nad tym, aby to pole było opcjonalne, gdy używane są Tagi FQDN.|
|Przeniesienie zapory do innej grupy zasobów lub subskrypcji nie jest obsługiwane|Przeniesienie zapory do innej grupy zasobów lub subskrypcji nie jest obsługiwane.|Obsługa tej funkcji jest w naszym harmonogramie działania. Aby przenieść zaporę do innej grupy zasobów lub subskrypcji, musisz usunąć bieżące wystąpienie i utworzyć je ponownie w nowej grupie zasobów lub subskrypcji.|
|Alerty analizy zagrożeń mogą zostać zamaskowane|Reguły sieciowe z lokalizacją docelową 80/443 dla wyzwalanych masek filtrowania zdarzeń analizy zagrożeń po skonfigurowaniu trybu tylko alertu.|Utwórz filtrowanie wychodzące dla 80/443 przy użyciu reguł aplikacji. Lub zmień tryb analizy zagrożeń na **alert i Odmów**.|
|Zapora platformy Azure DNAT nie działa w przypadku prywatnych miejsc docelowych IP|Obsługa usługi Azure firewall DNAT jest ograniczona do Internetu/ruchu przychodzącego. DNAT aktualnie nie działa w przypadku prywatnych miejsc docelowych IP. Na przykład satelity.|Jest to bieżące ograniczenie.|
|Nie można usunąć pierwszej konfiguracji publicznego adresu IP|Każdy publiczny adres IP zapory platformy Azure jest przypisywany do *konfiguracji adresu IP*.  Pierwsza konfiguracja protokołu IP jest przypisywana podczas wdrażania zapory, a także zazwyczaj zawiera odwołanie do podsieci zapory (chyba że jest inaczej skonfigurowana za pośrednictwem wdrożenia szablonu). Nie można usunąć tej konfiguracji protokołu IP, ponieważ spowodowałoby to cofnięcie alokacji zapory. Nadal można zmienić lub usunąć publiczny adres IP skojarzony z tą konfiguracją IP, Jeśli Zapora ma co najmniej jeden publiczny adres IP do użycia.|Jest to celowe.|
|Strefy dostępności można skonfigurować tylko podczas wdrażania.|Strefy dostępności można skonfigurować tylko podczas wdrażania. Nie można skonfigurować Strefy dostępności po wdrożeniu zapory.|Jest to celowe.|
|Przychodzący translator adresów sieciowych dla połączeń przychodzących|Oprócz DNAT połączenia za pośrednictwem publicznego adresu IP (przychodzącego) zapory są podłączony do jednego z prywatnych IP zapory. Ten wymóg jest obecny (również w przypadku usługi Active/Active urządzeń WUS) w celu zapewnienia symetrycznego routingu.|Aby zachować oryginalne źródło dla protokołu HTTP/S, rozważ użycie nagłówków [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) . Na przykład użyj usługi, takiej jak [Azure Front drzwiczk](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) lub [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) przed zaporą. Możesz również dodać WAF w ramach frontonu i łańcucha do zapory platformy Azure.
|Obsługa filtrowania nazw FQDN programu SQL tylko w trybie proxy (port 1433)|W przypadku Azure SQL Database, usługi Azure Synapse Analytics i wystąpienia zarządzanego Azure SQL:<br><br>Filtrowanie nazwy FQDN SQL jest obsługiwane tylko w trybie proxy (port 1433).<br><br>W przypadku usługi Azure SQL IaaS:<br><br>Jeśli używasz portów niestandardowych, możesz określić te porty w regułach aplikacji.|W przypadku usługi SQL w trybie przekierowania (wartość domyślna w przypadku nawiązywania połączenia z poziomu platformy Azure) można zamiast tego filtrować dostęp przy użyciu znacznika usługi SQL w ramach reguł sieci zapory platformy Azure.
|Ruch wychodzący na porcie TCP 25 jest niedozwolony| Wychodzące połączenia SMTP używające portu 25 TCP są blokowane. Port 25 jest używany głównie do dostarczania nieuwierzytelnionych wiadomości e-mail. Jest to domyślne zachowanie platformy dla maszyn wirtualnych. Aby uzyskać więcej informacji, Zobacz więcej [Rozwiązywanie problemów z łącznością wychodzącą SMTP na platformie Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Jednak w przeciwieństwie do maszyn wirtualnych nie jest obecnie możliwe włączenie tej funkcji w zaporze platformy Azure. Uwaga: aby zezwolić na uwierzytelnionego protokołu SMTP (port 587) lub SMTP przez port inny niż 25, upewnij się, że skonfigurowano regułę sieciową, a nie regułę aplikacji, ponieważ Inspekcja SMTP nie jest w tej chwili obsługiwana.|Postępuj zgodnie z zalecaną metodą wysyłania wiadomości e-mail zgodnie z opisem w artykule dotyczącym rozwiązywania problemów z protokołem SMTP. Lub Wyklucz maszynę wirtualną wymagającą dostępu wychodzącego SMTP z domyślnej trasy do zapory. Zamiast tego należy skonfigurować dostęp wychodzący bezpośrednio do Internetu.
|Aktywne FTP nie jest obsługiwane|Usługa Active FTP została wyłączona w zaporze platformy Azure w celu ochrony przed atakami za pośrednictwem protokołu FTP za pomocą polecenia FTP PORT.|Zamiast tego można użyć pasywnego protokołu FTP. Należy nadal jawnie otwierać porty TCP 20 i 21 w zaporze.
|Metryka wykorzystania portów przez przytranslatora adresów sieciowych pokazuje 0%|Metryka wykorzystania portów w zaporze platformy Azure może wskazywać na użycie 0% nawet wtedy, gdy są używane porty. W takim przypadku użycie metryki jako części metryki kondycji zapory zapewnia niepoprawny wynik.|Ten problem został rozwiązany, a wdrażanie w środowisku produkcyjnym jest celem 2020 maja. W niektórych przypadkach ponowne wdrożenie zapory rozwiązuje problem, ale nie jest to spójne. Jako obejście pośrednie Użyj stanu kondycji zapory tylko w celu wyszukania *stanu = obniżona* wartość, a nie dla *stanu = zła kondycja*. W przypadku wyczerpania portów zostanie wyświetlony stan *obniżonej wydajności*. *Nie* działa w przyszłości, gdy jest więcej metryk, które mają wpływ na kondycję zapory.
|DNAT nie jest obsługiwana z włączonym wymuszonym tunelowaniem|Zapory wdrożone z włączonym wymuszonym tunelowaniem nie mogą obsługiwać przychodzącego dostępu z Internetu z powodu asymetrycznego routingu.|Jest to spowodowane projektem routingu asymetrycznego. Ścieżka zwrotna dla połączeń przychodzących odbywa się za pośrednictwem lokalnej zapory, która nie widziała ustanowionego połączenia.
|Ruch wychodzący pasywnego protokołu FTP może nie funkcjonować w przypadku zapór z wieloma publicznymi adresami IP, w zależności od konfiguracji serwera FTP.|Pasywne FTP ustanawia różne połączenia dla kanałów kontroli i danych. Gdy Zapora z wieloma publicznymi adresami IP wysyła dane wychodzące, losowo wybiera jeden z jego publicznych adresów IP dla źródłowego adresu IP. Usługa FTP może zakończyć się niepowodzeniem, gdy kanały danych i kontroli używają różnych źródłowych adresów IP, w zależności od konfiguracji serwera FTP.|Zaplanowano jawną konfigurację translatora adresów sieciowych. W międzyczasie można skonfigurować serwer FTP do akceptowania kanałów danych i kontroli z różnych źródłowych adresów IP (zobacz [przykład dla usług IIS](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)). Alternatywnie Rozważ użycie pojedynczego adresu IP w tej sytuacji.|
|Przychodzące pasywne FTP może nie funkcjonować w zależności od konfiguracji serwera FTP |Pasywne FTP ustanawia różne połączenia dla kanałów kontroli i danych. Połączenia przychodzące w zaporze platformy Azure są podłączony do jednego z prywatnych adresów IP zapory, aby zapewnić symetryczne Routing. Usługa FTP może zakończyć się niepowodzeniem, gdy kanały danych i kontroli używają różnych źródłowych adresów IP, w zależności od konfiguracji serwera FTP.|Trwa badanie oryginalnego źródłowego adresu IP. W międzyczasie można skonfigurować serwer FTP do akceptowania kanałów danych i kontroli z różnych źródłowych adresów IP.|
|W metryce NetworkRuleHit brakuje wymiaru protokołu|Metryka ApplicationRuleHit umożliwia filtrowanie na podstawie protokołu, ale w odpowiedniej metryce NetworkRuleHit nie ma tej możliwości.|Trwa badanie poprawki.|
|Reguły NAT z portami z zakresu od 64000 do 65535 nie są obsługiwane|Zapora platformy Azure zezwala na dowolny port w zakresie 1-65535 w regułach sieci i aplikacji, jednak reguły NAT obsługują tylko porty w zakresie 1-63999.|Jest to bieżące ograniczenie.
|Aktualizacje konfiguracji mogą mieć średnio pięć minut|Aktualizacja konfiguracji zapory platformy Azure może trwać od trzech do pięciu minut, a aktualizacje równoległe nie są obsługiwane.|Trwa badanie poprawki.|
|Zapora platformy Azure używa nagłówków SNI TLS do filtrowania ruchu HTTPS i MSSQL|Jeśli oprogramowanie przeglądarki lub serwera nie obsługuje rozszerzenia wskaźnika nazwy serwera (SNI), nie będzie można nawiązać połączenia za pomocą zapory platformy Azure.|Jeśli oprogramowanie przeglądarki lub serwera nie obsługuje SNI, może być możliwe sterowanie połączeniem przy użyciu reguły sieci zamiast reguły aplikacji. Zobacz [oznaczanie nazwy serwera](https://wikipedia.org/wiki/Server_Name_Indication) oprogramowania, które obsługuje SNI.|
|Niestandardowy serwer DNS nie działa z wymuszonym tunelowaniem|Jeśli Wymuszone tunelowanie jest włączone, niestandardowa usługa DNS nie działa.|Trwa badanie poprawki.|
|Nowy publiczny adres IP obsługa wielu Strefy dostępności|Nie można dodać nowego publicznego adresu IP podczas wdrażania zapory z dwiema strefami dostępności (1 i 2, 2 i 3 lub 1 i 3)|Jest to ograniczenie zasobów publicznego adresu IP.|
|Uruchomienie/zatrzymanie nie działa z zaporą skonfigurowaną w trybie tunelowania wymuszonego|Uruchomienie/zatrzymanie nie działa z zaporą platformy Azure skonfigurowaną w trybie tunelowania wymuszonego. Próba uruchomienia zapory platformy Azure z skonfigurowanym wymuszonym tunelowaniem powoduje następujący błąd:<br><br>*Set-AzFirewall: AzureFirewall PD-XX Konfiguracja protokołu IP nie może zostać dodana do istniejącej zapory. Wdróż ponownie z konfiguracją protokołu IP zarządzania, jeśli chcesz użyć wymuszonego tunelowania. <br> StatusCode: 400 <br> ReasonPhrase: złe żądanie*|W trakcie badania.<br><br>Aby obejść ten sposób, można usunąć istniejącą zaporę i utworzyć nową z tymi samymi parametrami.|
|Nie można dodać tagów zasad zapory przy użyciu portalu|Zasada zapory platformy Azure ma ograniczenie obsługi poprawek, które uniemożliwia dodanie znacznika przy użyciu Azure Portal. Generowany jest następujący błąd: nie *można zapisać tagów dla zasobu*.|Trwa badanie poprawki. Alternatywnie możesz użyć polecenia cmdlet Azure PowerShell, `Set-AzFirewallPolicy` Aby zaktualizować Tagi.


## <a name="next-steps"></a>Następne kroki

- [Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal](tutorial-firewall-deploy-portal.md)
- [Wdrażanie usługi Azure Firewall przy użyciu szablonu](deploy-template.md)
- [Tworzenie środowiska testowego usługi Azure Firewall](scripts/sample-create-firewall-test.md)