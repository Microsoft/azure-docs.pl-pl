---
title: Omówienie konfiguracji Application Gateway platformy Azure
description: W tym artykule opisano sposób konfigurowania składników usługi Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652001"
---
# <a name="application-gateway-configuration-overview"></a>Przegląd konfiguracji Application Gateway

Usługa Azure Application Gateway obejmuje kilka składników, które można skonfigurować na różne sposoby w różnych scenariuszach. W tym artykule opisano sposób konfigurowania każdego składnika.

![Wykres przepływu składników Application Gateway](./media/configuration-overview/configuration-overview1.png)

Ten obraz przedstawia aplikację, która ma trzy detektory. Pierwsze dwa są odbiornikami wielolokacjowymi dla `http://acme.com/*` i `http://fabrikam.com/*` , odpowiednio. Oba nasłuchują na porcie 80. Trzecia to podstawowy odbiornik, który ma kompleksowe zakończenie Transport Layer Security (TLS), wcześniej znane jako zakończenie SSL (SSL).

## <a name="infrastructure"></a>Infrastruktura

Infrastruktura Application Gateway obejmuje sieć wirtualną, podsieci, sieciowe grupy zabezpieczeń i trasy zdefiniowane przez użytkownika.

Aby uzyskać więcej informacji, zobacz [Application Gateway konfiguracja infrastruktury](configuration-infrastructure.md).



## <a name="front-end-ip-address"></a>Adres IP frontonu

Bramę aplikacji można skonfigurować tak, aby zawierała publiczny adres IP, prywatny adres IP lub oba te elementy. Publiczny adres IP jest wymagany w przypadku hostowania zaplecza, do którego klienci muszą uzyskać dostęp za pośrednictwem Internetu za pośrednictwem wirtualnego adresu IP (VIP) dostępnego z Internetu.

Aby uzyskać więcej informacji, zobacz [Application Gateway konfiguracji adresu IP frontonu](configuration-front-end-ip.md).

## <a name="listeners"></a>Odbiorniki

Odbiornik jest jednostką logiczną, która sprawdza przychodzące żądania połączeń przy użyciu portu, protokołu, hosta i adresu IP. Podczas konfigurowania odbiornika należy wprowadzić wartości pasujące do odpowiednich wartości w żądaniu przychodzącym na bramie.

Aby uzyskać więcej informacji, zobacz [Application Gateway konfiguracja odbiornika](configuration-listeners.md).

## <a name="request-routing-rules"></a>Żądanie reguł rozsyłania

Podczas tworzenia bramy aplikacji przy użyciu Azure Portal należy utworzyć regułę domyślną (*RULE1*). Ta reguła wiąże domyślny odbiornik (*appGatewayHttpListener*) z domyślną pulą zaplecza (*appGatewayBackendPool*) i domyślnymi ustawieniami http zaplecza (*appGatewayBackendHttpSettings*). Po utworzeniu bramy można edytować ustawienia reguły domyślnej lub utworzyć nowe reguły.

Aby uzyskać więcej informacji, zobacz [Application Gateway reguły routingu żądań](configuration-request-routing-rules.md).

## <a name="http-settings"></a>Ustawienia protokołu HTTP

Brama aplikacji kieruje ruch do serwerów zaplecza przy użyciu konfiguracji określonej w tym miejscu. Po utworzeniu ustawienia protokołu HTTP należy je skojarzyć z co najmniej jedną regułą routingu żądania.

Aby uzyskać więcej informacji, zobacz [Application Gateway konfiguracja ustawień http](configuration-http-settings.md).

## <a name="back-end-pool"></a>Pula zaplecza

Pulę zaplecza można wskazać na cztery typy elementów członkowskich zaplecza: konkretną maszynę wirtualną, zestaw skalowania maszyn wirtualnych, adres IP/nazwa FQDN lub Usługa aplikacji. 

Po utworzeniu puli zaplecza należy ją skojarzyć z co najmniej jedną regułą routingu żądania. Należy również skonfigurować sondy kondycji dla każdej puli zaplecza na bramie aplikacji. Gdy spełniony jest warunek reguły routingu żądania, Brama aplikacji przekazuje ruch do serwerów w dobrej kondycji (zgodnie z sondami kondycji) w odpowiedniej puli zaplecza.

## <a name="health-probes"></a>Sondy kondycji

Brama aplikacji domyślnie monitoruje kondycję wszystkich zasobów w zapleczu. Jednak zdecydowanie zalecamy utworzenie niestandardowej sondy dla każdego ustawienia HTTP zaplecza, aby uzyskać większą kontrolę nad monitorowaniem kondycji. Aby dowiedzieć się, jak skonfigurować sondę niestandardową, zobacz [niestandardowe ustawienia sondy kondycji](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Po utworzeniu niestandardowej sondy kondycji należy ją skojarzyć z ustawieniem HTTP zaplecza. Niestandardowa Sonda nie będzie monitorować kondycji puli zaplecza, chyba że odpowiednie ustawienie protokołu HTTP jest jawnie skojarzone z odbiornikiem przy użyciu reguły.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już o składnikach Application Gateway, możesz:

- [Tworzenie bramy aplikacji w Azure Portal](quick-create-portal.md)
- [Tworzenie bramy aplikacji przy użyciu programu PowerShell](quick-create-powershell.md)
- [Tworzenie bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
