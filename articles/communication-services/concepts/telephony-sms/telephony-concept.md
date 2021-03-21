---
title: Koncepcje integracji telefonii PSTN dla usług Azure Communications Services
description: Dowiedz się, jak zintegrować możliwości wywoływania sieci PSTN w aplikacji usługi Azure Communications Services.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d5e4920dcc422e848266f35c8a59175b5149b924
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492748"
---
# <a name="telephony-concepts"></a>Koncepcje dotyczące telefonii

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Korzystając z usług Azure Communications Services, można dodawać do aplikacji usługi telefonii i sieci PSTN. Ta strona zawiera podsumowanie najważniejszych koncepcji i możliwości związanych z telefonem. Zapoznaj się z [biblioteką wywołującą](../../quickstarts/voice-video-calling/calling-client-samples.md) , aby dowiedzieć się więcej o określonych językach i możliwościach biblioteki klienta.

## <a name="overview-of-telephony"></a>Omówienie telefonii
Za każdym razem, gdy użytkownicy współpracują z tradycyjnym numerem telefonu, wywołania są obsługiwane przez telefonię PSTN (publiczne przełączane sieci telefoniczne). Aby móc tworzyć i odbierać wywołania PSTN, należy dodać funkcje telefonii do zasobu usług Azure Communications Services. W takim przypadku sygnalizowanie i nośniki używają kombinacji technologii opartych na protokole IP i PSTN do łączenia użytkowników. Usługi komunikacyjne oferują dwa dyskretne sposoby docierania do sieci PSTN: wywołanie w chmurze platformy Azure i interfejs SIP.

### <a name="azure-cloud-calling"></a>Usługa Azure Cloud Call

Łatwy sposób dodawania łączności PSTN do aplikacji lub usługi, w tym przypadku, firma Microsoft jest dostawcą usługi odpływ. Możesz kupić numery bezpośrednio od firmy Microsoft. Azure Cloud Call to wszystko w chmurze rozwiązanie do obsługi usług komunikacyjnych. Jest to najprostsza opcja, która łączy ACS z publiczną siecią telefoniczną (PSTN), aby umożliwić nawiązywanie połączeń stacjonarnych i telefonów komórkowych na całym świecie. W przypadku tej opcji firma Microsoft działa jak Twój przewoźnik sieci PSTN, jak pokazano na poniższym diagramie:

![Diagram wywoływania w chmurze platformy Azure.](../media/telephony-concept/azure-calling-diagram.png)

Jeśli otrzymasz odpowiedź "tak" na następujące polecenie, usługa Azure Cloud Call to odpowiednie rozwiązanie:
- Usługa Azure Cloud Call jest dostępna w Twoim regionie.
- Nie ma potrzeby zachowywania bieżącego operatora PSTN.
- Chcesz użyć dostępu zarządzanego przez firmę Microsoft do sieci PSTN.

Z tą opcją:
- Otrzymujesz numery bezpośrednio od firmy Microsoft i możesz wywoływać telefony na całym świecie.
- Nie jest wymagane wdrożenie ani konserwacja lokalnego wdrożenia — ponieważ usługa Azure Cloud Call działa poza usługami Azure Communications Services.
- Uwaga: w razie potrzeby można połączyć obsługiwany kontroler graniczny sesji (SBC) za pomocą interfejsu SIP w celu współdziałania z urządzeniami PBXs, analogowymi i innymi urządzeniami telefonicznymi innych firm, które są obsługiwane przez tę firmę.

Ta opcja wymaga nieprzerwanego połączenia z usługami Azure Communications Services.

### <a name="sip-interface"></a>Interfejs SIP

Za pomocą tej opcji można połączyć starszą lokalną telefonię i wybranego usługodawcę z usługą Azure Communications Services. Udostępnia ona funkcje połączeń PSTN do aplikacji ACS, nawet jeśli usługa Azure Cloud Call nie jest dostępna w Twoim kraju/regionie. 

![Diagram interfejsu SIP.](../media/telephony-concept/sip-interface-diagram.png)

Jeśli otrzymasz odpowiedź "tak" na dowolne z poniższych pytań, interfejs SIP jest odpowiednim rozwiązaniem:

- Chcesz używać usług ACS z możliwościami wywoływania PSTN.
- Musisz zachować bieżący przewoźnik sieci PSTN.
- Chcesz mieszać Routing, z pewnymi wywołaniami, które przechodzą przez usługę Azure Cloud Call, niektóre za pośrednictwem operatora.
- Musisz współpracować z PBXs i/lub sprzętem innych firm, takich jak strony narzutów, urządzenia analogowe i tak dalej.

Z tą opcją:

- Możesz połączyć własne obsługiwane usługi SBC z usługami Azure Communications Services bez konieczności dodatkowego oprogramowania lokalnego.
- Można użyć dosłownie dowolnego operatora telefonii z usługą ACS.
- Można skonfigurować tę opcję i zarządzać nią lub można ją skonfigurować i zarządzać nią przez przewoźnika lub partnera (Jeśli ta opcja jest dostępna dla operatora lub partnera).
- Można skonfigurować współdziałanie między urządzeniami telefonii, takimi jak urządzenia PBX i analogowe innych firm, oraz usługi ACS.

Ta opcja wymaga następujących czynności:

- Nieprzerwane połączenie z platformą Azure.
- Wdrażanie i obsługa obsługiwanej SBC.
- Kontrakt z przewoźnikiem innej firmy. (O ile nie zostanie wdrożony jako opcja w celu zapewnienia połączenia z urządzeniem PBX, urządzeniami analogowymi lub innym sprzętem telefonii dla użytkowników korzystających z usług komunikacyjnych).

## <a name="next-steps"></a>Następne kroki

### <a name="conceptual-documentation"></a>Dokumentacja dotycząca pojęć

- [Typy numerów telefonów w usłudze Azure Communications Services](./plan-solution.md)
- [Planowanie interfejsu SIP](./sip-interface-infrastructure.md)
- [Cennik](../pricing.md)

### <a name="quickstarts"></a>Przewodniki Szybki start

- [Pobierz numer telefonu](../../quickstarts/telephony-sms/get-phone-number.md)
- [Wywołanie telefonu](../../quickstarts/voice-video-calling/pstn-call.md)
