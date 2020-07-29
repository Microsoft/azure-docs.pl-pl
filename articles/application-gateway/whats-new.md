---
title: Co nowego w usłudze Azure Application Gateway
description: Dowiedz się, co nowego w usłudze Azure Application Gateway, takich jak Najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 06/10/2020
ms.author: victorh
ms.openlocfilehash: 6ea0b28f5e0f23e0f8bb30e8fe2b0f792bce0709
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287441"
---
# <a name="whats-new-in-azure-application-gateway"></a>Co nowego w usłudze Azure Application Gateway?

Usługa Azure Application Gateway jest regularnie aktualizowana. Aby zaktualizować najnowsze zmiany, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe

## <a name="new-features"></a>Nowe funkcje

|Cechy  |Opis  |Data dodania  |
|---------|---------|---------|
| Nazwy hostów symboli wieloznacznych w odbiornikach (wersja zapoznawcza) | Teraz można definiować symbole wielowitrynowe, a maksymalnie 5 nazw hostów na odbiornik. Aby dowiedzieć się więcej, zobacz [symbole wieloznaczne nazw hostów w odbiorniku (wersja zapoznawcza)](multiple-site-overview.md#wildcard-host-names-in-listener-preview) | Lipiec 2020 r. |
| Ponowne zapisywanie adresów URL (wersja zapoznawcza) | Teraz można ponownie napisać ścieżkę URL i parametry ciągu zapytania przy użyciu ponownego zapisywania adresu URL. Zobacz [Zapisywanie nagłówków HTTP i adresów URL](rewrite-http-headers-url.md) , aby dowiedzieć się więcej i zobacz artykuł ponowne [Zapisywanie adresu URL w Application Gateway przy użyciu Azure Portal](rewrite-url-portal.md) do przewodnika krok po kroku. | Lipiec 2020 r. |
| Port niestandardowy dla sond kondycji | Jednostka SKU Application Gateway v2 oferuje teraz możliwość zapewnienia niestandardowego portu w konfiguracji sondy kondycji. Aby uzyskać więcej informacji, zobacz [Omówienie sondy kondycji](application-gateway-probe-overview.md) | Lipiec 2020 r. |
| Application Gateway AKS (wersja zapoznawcza) |Application Gateway można teraz wdrożyć kontroler transferu danych przychodzących jako natywny dodatek AKS w jednym wierszu za pomocą interfejsu wiersza polecenia platformy Azure. Jest to dodatek AKS umożliwiający AGIC stanie się w pełni zarządzaną usługą, a mimo to działa w klastrze AKS klienta. Aby uzyskać więcej informacji, zobacz [AGIC dodatków](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). |Czerwiec 2020 r. |
| Trasy zdefiniowane przez użytkownika (UDR) w wersji 2 (wersja zapoznawcza) |Trasy zdefiniowane przez użytkownika są teraz obsługiwane w niektórych scenariuszach dotyczących jednostek SKU Application Gateway v2. Aby uzyskać więcej informacji, zobacz [Omówienie konfiguracji Application Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet). |Marzec 2020 r. |
|Zmiany plików cookie koligacji |Gdy jest włączona koligacja oparta na plikach cookie, Application Gateway wprowadza inny identyczny plik cookie o nazwie *ApplicationGatewayAffinityCORS* , a także istniejący plik cookie ApplicationGatewayAffinity. *ApplicationGatewayAffinityCORS* ma dwa dodatkowe atrybuty (*SameSite = None). Zabezpiecz*), aby sesje programu Sticky Notes były utrzymywane nawet w przypadku żądań między źródłami. Aby uzyskać więcej informacji, zobacz [Application Gateway koligacja oparta na plikach cookie](configuration-overview.md#cookie-based-affinity) . |Luty 2020 r. |
|Udoskonalenia sondy |W przypadku ulepszeń niestandardowych w Application Gateway wersji 2 jednostki SKU mamy uproszczoną [konfigurację sondowania](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku), usprawnienia [testów kondycji zaplecza na żądanie](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) i dodaliśmy [więcej informacji diagnostycznych](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) ułatwiających rozwiązywanie problemów z kondycją zaplecza.  |Październik 2019 r. |
|Więcej metryk |Dodaliśmy następujące nowe metryki pomocne w monitorowaniu jednostki SKU Application Gateway v2: [metryki dotyczące chronometrażu](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), stan odpowiedzi zaplecza, bajty odebrane, Wysłane bajty, protokół TLS klienta i bieżące jednostki obliczeniowe. Zobacz [metryki obsługiwane przez jednostkę SKU Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Sierpień 2019 r. |
|Reguły niestandardowe zapory aplikacji internetowej |Application Gateway WAF_v2 obsługuje teraz Tworzenie reguł niestandardowych. Zobacz [Application Gateway reguł niestandardowych](custom-waf-rules-overview.md). |Czerwiec 2019 r. |
|Skalowanie automatyczne, nadmiarowość stref, obsługa statycznych adresów VIP |Ogólna dostępność dla jednostki SKU w wersji 2, która obsługuje skalowanie automatyczne, nadmiarowość stref, podnoszenie wydajności, statyczne adresy VIP, Key Vault, ponowne zapisywanie nagłówka. Zobacz [Application Gateway dokumentacji skalowania](application-gateway-autoscaling-zone-redundant.md)automatycznego. |Kwiecień 2019 r. |
|Integracja Key Vault |Application Gateway teraz obsługuje integrację z Key Vault (w publicznej wersji zapoznawczej) dla certyfikatów serwera, które są dołączone do odbiorników z obsługą protokołu HTTPS. Zobacz [zakończenie protokołu TLS z certyfikatami Key Vault](key-vault-certs.md). |Kwiecień 2019 r. |
|Nagłówek CRUD/ponowne zapisywanie     |Teraz można ponownie napisać nagłówki HTTP. Zobacz [Samouczek: Utwórz bramę aplikacji i ponownie Napisz nagłówki HTTP,](tutorial-http-header-rewrite-powershell.md) Aby uzyskać więcej informacji.|Grudzień 2018 r.|
|WAF konfiguracja i lista wykluczeń     |Dodaliśmy więcej opcji, które ułatwiają konfigurowanie WAF i zmniejszanie fałszywych wyników pozytywnych. Aby uzyskać więcej informacji, zobacz [limity rozmiaru żądań zapory aplikacji sieci Web i listy wykluczeń](application-gateway-waf-configuration.md).|Grudzień 2018 r.|
|Skalowanie automatyczne, nadmiarowość stref, obsługa statycznych adresów VIP      |W przypadku wersji 2 jednostki SKU wprowadzono wiele udoskonaleń, takich jak Skalowanie automatyczne, zwiększona wydajność i wiele innych. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Application Gateway?](overview.md) .|Wrzesień 2018 r.|
|Opróżnianie połączeń     |Opróżnianie połączeń pozwala bezpiecznie usunąć członków z pul zaplecza. Aby uzyskać więcej informacji, zobacz [opróżnianie połączenia](features.md#connection-draining).|Wrzesień 2018 r.|
|Niestandardowe strony błędów     |Ze stronami błędów niestandardowych można utworzyć stronę błędu w formacie reszty witryn sieci Web. Aby włączyć tę funkcję, zobacz [tworzenie Application Gateway niestandardowych stron błędów](custom-error.md).|Wrzesień 2018 r.|
|Udoskonalenia metryk     |Lepszy widok stanu Application Gateway można uzyskać przy użyciu ulepszonych metryk. Aby włączyć metryki na Application Gateway, zobacz temat [kondycja zaplecza, dzienniki diagnostyczne i metryki dla Application Gateway](application-gateway-diagnostics.md).|Czerwiec 2018 r.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Application Gateway, zobacz [co to jest usługa azure Application Gateway?](overview.md)
