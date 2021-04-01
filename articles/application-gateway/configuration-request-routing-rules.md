---
title: Konfiguracja reguł routingu żądań Application Gateway platformy Azure
description: W tym artykule opisano sposób konfigurowania reguł routingu żądania Application Gateway platformy Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 047ce9b33836e2c23a37b1383942323d7c382485
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397539"
---
# <a name="application-gateway-request-routing-rules"></a>Reguły routingu żądań Application Gateway

Podczas tworzenia bramy aplikacji przy użyciu Azure Portal należy utworzyć regułę domyślną (*RULE1*). Ta reguła wiąże domyślny odbiornik (*appGatewayHttpListener*) z domyślną pulą zaplecza (*appGatewayBackendPool*) i domyślnymi ustawieniami http zaplecza (*appGatewayBackendHttpSettings*). Po utworzeniu bramy można edytować ustawienia reguły domyślnej lub utworzyć nowe reguły.

## <a name="rule-type"></a>Typ reguły

Podczas tworzenia reguły wybiera się między [ *podstawową* a *opartą na ścieżce*](./application-gateway-components.md#request-routing-rules).

- Wybierz pozycję podstawowa, jeśli chcesz przesłać dalej wszystkie żądania na skojarzonym odbiorniku (na przykład *blog <i></i> . contoso.com/ \* )* do jednej puli zaplecza.
- Wybierz pozycję oparta na ścieżce, jeśli chcesz kierować żądania z określonych ścieżek URL do określonych pul zaplecza. Wzorzec ścieżki jest stosowany tylko do ścieżki adresu URL, a nie do parametrów zapytania.

### <a name="order-of-processing-rules"></a>Kolejność reguł przetwarzania

W przypadku jednostki SKU w wersji 1 i v2 wzorzec dopasowywania żądań przychodzących jest przetwarzany w kolejności, w jakiej ścieżki są wyświetlane na mapie ścieżki URL reguły opartej na ścieżce. Jeśli żądanie pasuje do wzorca w co najmniej dwóch ścieżkach w mapie ścieżki, ścieżka, która jest wymieniona jako pierwsza jest dopasowana. A żądanie jest przekazywane do zaplecza, który jest skojarzony z tą ścieżką.

## <a name="associated-listener"></a>Skojarzony odbiornik

Skojarz odbiornik z regułą, tak aby *reguła routingu żądań* skojarzona z odbiornikiem była oceniana w celu określenia puli zaplecza, do której ma zostać rozesłane żądanie.

## <a name="associated-back-end-pool"></a>Skojarzona Pula zaplecza

Skojarz z regułą Pula zaplecza zawierającą elementy docelowe zaplecza, które obsługują żądania odbierane przez odbiornik.

 - W przypadku podstawowej reguły dozwolony jest tylko jedna pula zaplecza. Wszystkie żądania na skojarzonym odbiorniku są przekazywane do tej puli zaplecza.

 - Dla reguły opartej na ścieżce Dodaj wiele pul zaplecza odpowiadających każdej ścieżce URL. Żądania zgodne z podaną ścieżką URL są przekazywane do odpowiedniej puli zaplecza. Ponadto Dodaj domyślną pulę zaplecza. Żądania, które nie pasują do żadnej ścieżki URL w regule, są przekazywane do tej puli.

## <a name="associated-back-end-http-setting"></a>Skojarzone ustawienie protokołu HTTP zaplecza

Dodaj ustawienia protokołu HTTP zaplecza dla każdej reguły. Żądania są kierowane z bramy aplikacji do obiektów docelowych zaplecza przy użyciu numeru portu, protokołu i innych informacji określonych w tym ustawieniu.

W przypadku podstawowej reguły dozwolone jest tylko jedno ustawienie HTTP zaplecza. Wszystkie żądania na skojarzonym odbiorniku są przekazywane do odpowiednich obiektów docelowych zaplecza przy użyciu tego ustawienia protokołu HTTP.

Dla reguły opartej na ścieżce Dodaj wiele ustawień protokołu HTTP zaplecza, które odpowiadają każdej ścieżce URL. Żądania zgodne ze ścieżką URL w tym ustawieniu są przekazywane do odpowiednich obiektów docelowych zaplecza przy użyciu ustawień HTTP, które odpowiadają każdej ścieżce URL. Ponadto Dodaj domyślne ustawienie protokołu HTTP. Żądania, które nie pasują do żadnej ścieżki URL w tej regule, są przekazywane do domyślnej puli zaplecza przy użyciu domyślnego ustawienia protokołu HTTP.

## <a name="redirection-setting"></a>Ustawienie przekierowania

W przypadku skonfigurowania przekierowania dla podstawowej reguły wszystkie żądania na skojarzonym odbiorniku są przekierowywane do obiektu docelowego. To jest przekierowanie *globalne* . Jeśli przekierowanie jest skonfigurowane dla reguły opartej na ścieżce, przekierowywane są tylko żądania w określonym obszarze witryny. Przykładem jest obszar koszyka zakupów, który jest oznaczany przez */Cart/ \**. Jest to przekierowanie *oparte na ścieżce* .

Aby uzyskać więcej informacji na temat przekierowań, zobacz [Omówienie przekierowania Application Gateway](redirect-overview.md).

### <a name="redirection-type"></a>Typ przekierowania

Wybierz wymagany typ przekierowania: *trwały (301)*, *tymczasowy (307)*, *znaleziono (302)* lub *Zobacz inne (303)*.

### <a name="redirection-target"></a>Cel przekierowania

Wybierz inny odbiornik lub lokację zewnętrzną jako cel przekierowania.

#### <a name="listener"></a>Odbiornik

Wybierz odbiornik jako miejsce docelowe przekierowania, aby przekierować ruch z jednego odbiornika do innego na bramie. To ustawienie jest wymagane, jeśli chcesz włączyć przekierowywanie HTTP-to-HTTPS. Przekierowuje ruch z odbiornika źródłowego, który sprawdza przychodzące żądania HTTP do odbiornika docelowego, który sprawdza przychodzące żądania HTTPS. Można również dołączyć ciąg zapytania i ścieżkę z oryginalnego żądania w żądaniu, które jest przekazywane do docelowego przekierowania.

![Składnik Application Gateway — okno dialogowe](./media/configuration-overview/configure-redirection.png)

Aby uzyskać więcej informacji na temat przekierowania protokołu HTTP-HTTPS, zobacz:
- [Przekierowanie z protokołu HTTP do HTTPS przy użyciu Azure Portal](redirect-http-to-https-portal.md)
- [Przekierowanie z protokołu HTTP do HTTPS przy użyciu programu PowerShell](redirect-http-to-https-powershell.md)
- [Przekierowanie z protokołu HTTP do HTTPS przy użyciu interfejsu wiersza polecenia platformy Azure](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>Lokacja zewnętrzna

Wybierz opcję lokacja zewnętrzna, jeśli chcesz przekierować ruch do odbiornika skojarzonego z tą regułą do zewnętrznej lokacji. Możesz dołączyć ciąg zapytania z oryginalnego żądania w żądaniu, które jest przekazywane do docelowego przekierowania. Nie można przesłać dalej ścieżki do zewnętrznej witryny, która była w oryginalnym żądaniu.

Aby uzyskać więcej informacji na temat przekierowania, zobacz:
- [Przekierowywanie ruchu do lokacji zewnętrznej przy użyciu programu PowerShell](redirect-external-site-powershell.md)
- [Przekierowywanie ruchu do lokacji zewnętrznej przy użyciu interfejsu wiersza polecenia](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>Ponowne zapisywanie nagłówków HTTP i adresów URL

Korzystając z reguł ponownego zapisywania, można dodawać, usuwać lub aktualizować żądania HTTP (S) i nagłówki odpowiedzi oraz ścieżkę URL i parametry ciągu zapytania jako pakiety żądań i odpowiedzi przenoszone między pulami klienta i zaplecza za pośrednictwem bramy aplikacji.

W nagłówkach i parametrach adresu URL można ustawić wartości statyczne lub inne nagłówki i zmienne serwera. Pomaga to z ważnymi przypadkami użycia, takimi jak wyodrębnianie adresów IP klientów, usuwanie poufnych informacji o zapleczu, dodawanie dodatkowych zabezpieczeń i tak dalej.
Aby uzyskać więcej informacji, zobacz:

 - [Przegląd ponownego zapisywania nagłówków HTTP i adresów URL](rewrite-http-headers-url.md)
 - [Konfigurowanie ponownego zapisywania nagłówka HTTP](rewrite-http-headers-portal.md)
 - [Skonfiguruj ponowne zapisywanie adresów URL](rewrite-url-portal.md)

## <a name="next-steps"></a>Następne kroki

- [Informacje o ustawieniach protokołu HTTP](configuration-http-settings.md)