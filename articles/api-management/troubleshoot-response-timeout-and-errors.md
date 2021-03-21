---
title: Rozwiązywanie problemów z limitami czasu i błędami odpowiedzi klienta przy użyciu API Management
description: Rozwiązywanie sporadycznych błędów połączeń i pokrewnych problemów z opóźnieniami w API Management
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 6cace4a02c8d45cacbbc34e9778b5c4a78ada27f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576513"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>Rozwiązywanie problemów z limitami czasu i błędami odpowiedzi klienta przy użyciu API Management

Ten artykuł pomaga rozwiązywać problemy z sporadycznymi błędami połączeń i powiązanymi problemami z opóźnieniami w [usłudze Azure API Management](./api-management-key-concepts.md). W tym artykule podano informacje i rozwiązywanie problemów z wyczerpaniem portów adresów źródłowych (NAT). Jeśli potrzebujesz więcej pomocy, skontaktuj się z ekspertami platformy Azure w ramach [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/) lub Prześlij żądanie pomocy technicznej z pomocą [techniczną platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="symptoms"></a>Objawy

Aplikacje klienckie wywołujące interfejsy API za pomocą usługi API Management (APIM) mogą mieć co najmniej jeden z następujących objawów:

* Sporadyczne błędy HTTP 500
* Komunikaty o błędach limitu czasu

Objawy te występują jako wystąpienia `BackendConnectionFailure` w [dziennikach zasobów Azure monitor](../azure-monitor/essentials/resource-logs.md).

## <a name="cause"></a>Przyczyna

Ten wzorzec symptomów często występuje ze względu na limity portów translatora adresów sieciowych (NAT) za pomocą usługi APIM.

Za każdym razem, gdy klient wywołuje jeden z interfejsów API APIM, usługa Azure API Management Service otworzy port przychodzący, aby uzyskać dostęp do interfejsu API zaplecza. Jak opisano w [połączeniach wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md), platforma Azure korzysta z translatora adresów sieciowych (NAT) i Load Balancer (nieujawnione klientom) do komunikowania się z punktami końcowymi poza platformą Azure w publicznej przestrzeni adresów IP, a także punkty końcowe wewnętrzne dla platformy Azure, które nie korzystają z [punktów końcowych usługi Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). Ta sytuacja dotyczy tylko interfejsów API zaplecza udostępnianych na publicznych adresach IP.

Każde wystąpienie usługi API Management otrzymuje wstępnie przydzieloną liczbę portów przydziałów adresów sieciowych. Ten limit wpływa na otwieranie połączeń z tym samym hostem i kombinacją portów. Porty współruchowe są używane, gdy powtórzone wywołania tego samego adresu i kombinacji portów. Po wydaniu portu dla podsieci adresów sieciowych port jest dostępny do ponownego użycia w razie potrzeby. Moduł równoważenia obciążenia sieci platformy Azure ponownie przejmuje porty protokołu reportowego z zamkniętych połączeń dopiero po czterech minutach.

Szybkie pomyślne żądania klientów do interfejsów API mogą wyczerpać wstępnie przydzieloną liczbę portów przydziałów adresów sieciowych, jeśli te porty nie zostały zamknięte i są wystarczająco szybko przetwarzane, uniemożliwiając usłudze APIM przetwarzanie żądań klientów w odpowiednim czasie.

## <a name="mitigations-and-solutions"></a>Środki zaradcze i rozwiązania

Rozwiązywanie problemu z wyczerpaniem portów przez podzbiór danych wymaga, aby diagnozować i zoptymalizować wydajność usług zaplecza.

Ogólne strategie rozwiązywania problemów z wyczerpaniem portów podłączania adresów sieciowych są omawiane w [temacie Usuwanie błędów połączeń wychodzących](../load-balancer/troubleshoot-outbound-connection.md) z dokumentacji *Azure Load Balancer* . Te strategie mają zastosowanie do API Management.

### <a name="scale-your-apim-instance"></a>Skalowanie wystąpienia APIM

Każde wystąpienie API Management ma przydzieloną liczbę portów w oparciu o jednostki APIM. Możesz przydzielić dodatkowe porty podsiecie adresów sieciowych, skalując wystąpienie API Management przy użyciu dodatkowych jednostek. Aby uzyskać więcej informacji, zobacz [skalowanie usługi API Management](upgrade-and-scale.md#scale-your-api-management-service)

> [!NOTE]
> Użycie portu przez przydziały adresów sieciowych nie jest obecnie dostępne jako Metryka dla jednostek API Management Skalowanie automatyczne.

### <a name="use-multiple-ips-for-your-backend-urls"></a>Używanie wielu adresów IP dla adresów URL zaplecza

Każde połączenie z wystąpienia usługi APIM na ten sam docelowy adres IP i port docelowy usługi wewnętrznej bazy danych będzie używać portu podłączania w celu obsługi różnych przepływów ruchu. Bez innych portów dla ruchu zwrotnego z usługi w tle, APIM nie może oddzielić jednej odpowiedzi od innej.

Ponieważ porty źródłowego protokołu IP mogą być ponownie używane, jeśli docelowy adres URL lub port docelowy różni się od siebie, inny sposób, aby uniknąć wyczerpania portów w ramach usługi zaplecza, jest używany z wieloma adresami IP w przypadku adresów w Twoich adresach

Aby uzyskać więcej informacji, zobacz [Azure Load Balancer proxy wychodzące](../load-balancer/load-balancer-outbound-connections.md).

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>Umieść swoją usługę APIM i zaplecza w tej samej sieci wirtualnej

Jeśli interfejs API zaplecza jest hostowany w usłudze platformy Azure, która obsługuje *punkty końcowe usługi* , takie jak App Service, można uniknąć problemów z wyczerpaniem portów podłączania ruchu, umieszczając wystąpienie APIM i usługę zaplecza w tej samej sieci wirtualnej i korzystając z [punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md) lub [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md). W przypadku korzystania ze wspólnej sieci wirtualnej i umieszczania punktów końcowych usługi w podsieci integracji ruch wychodzący z wystąpienia APIM do tych usług pomija Internet, w ten sposób unikając ograniczeń portów przyłączania. Podobnie, jeśli korzystasz z sieci wirtualnej i prywatnych punktów końcowych, nie będziesz mieć żadnych problemów z portem przychodzącego ruchu źródłowego dla tego miejsca docelowego.

Aby uzyskać szczegółowe informacje, zobacz [jak używać platformy Azure API Management z sieciami wirtualnymi](api-management-using-with-vnet.md) i [integrować App Service z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md).

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>Umieść APIM w sieci wirtualnej i Roześlij wywołania wychodzące do zapory platformy Azure

Podobnie jak w przypadku umieszczania usług APIM i zaplecza w sieci wirtualnej, możesz użyć zapory platformy Azure z usługą APIM, a następnie skierować wychodzące wywołania APIM do zapory platformy Azure. Między APIM i zaporą platformy Azure (znajdującą się w tej samej sieci wirtualnej) nie są wymagane żadne porty. W przypadku połączeń z przydziałami do usług zaplecza Zapora platformy Azure ma 64 000 dostępnych portów, znacznie większej ilości niż jest przypisana do wystąpień APIM.

Więcej informacji można znaleźć w dokumentacji [zapory platformy Azure](../firewall/overview.md) .

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>Rozważ buforowanie odpowiedzi i inne dostosowanie wydajności zaplecza

Innym możliwym środkiem zaradczym jest zwiększenie czasu przetwarzania interfejsów API zaplecza. Jednym ze sposobów jest skonfigurowanie niektórych interfejsów API z buforowaniem odpowiedzi w celu zmniejszenia opóźnień między aplikacjami klienckimi wywołującymi interfejs API i obciążenie zaplecza APIM.

Aby uzyskać więcej informacji, zobacz [Dodawanie buforowania w celu poprawy wydajności API Management platformy Azure](api-management-howto-cache.md).

### <a name="consider-implementing-access-restriction-policies"></a>Rozważ zaimplementowanie zasad ograniczeń dostępu

Jeśli jest to zrozumiałe dla scenariusza biznesowego, można zaimplementować zasady ograniczeń dostępu dla produktu API Management. Na przykład `rate-limit-by-key` można użyć zasad, aby uniemożliwić użycie interfejsu API w poszczególnych kluczach przez ograniczenie częstotliwości wywołań w określonym przedziale czasu.

Aby uzyskać więcej informacji, zobacz [zasady ograniczeń dostępu API Management](api-management-access-restriction-policies.md) .

## <a name="see-also"></a>Zobacz też

* [Azure Load Balancer: Rozwiązywanie problemów z błędami połączeń wychodzących](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: Rozwiązywanie problemów sporadycznie wychodzące błędy połączeń](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
