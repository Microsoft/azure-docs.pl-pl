---
title: Zabezpieczenia kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zabezpieczyć kontener
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 348fb301d1a36c8df405c641f7644889417b11ba
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545655"
---
## <a name="azure-cognitive-services-container-security"></a>Zabezpieczenia kontenera Cognitive Services platformy Azure

Bezpieczeństwo powinno być głównym punktem skupienia przy tworzeniu aplikacji. Znaczenie zabezpieczeń jest metryką dla sukcesu. W przypadku tworzenia architektury rozwiązania programowego, które zawiera Cognitive Services kontenerów, ważne jest, aby zrozumieć, jakie ograniczenia i możliwości są dostępne dla użytkownika. Aby uzyskać więcej informacji o zabezpieczeniach sieci, zobacz [Konfigurowanie platformy Azure Cognitive Services sieci wirtualnych][az-security].

> [!IMPORTANT]
> Domyślnie *nie ma żadnych zabezpieczeń* w interfejsie API kontenera Cognitive Services. Przyczyną jest to, że najczęściej kontener zostanie uruchomiony jako część elementu, który jest chroniony przez mostek sieciowy. Możliwe jest jednak włączenie uwierzytelniania, które działa identycznie z uwierzytelnianiem używanym podczas uzyskiwania dostępu do [Cognitive Services opartego na chmurze][request-authentication].

Na poniższym diagramie przedstawiono domyślne i **niebezpieczne** podejście:

![Zabezpieczenia kontenerów](../media/container-security.svg)

Jako alternatywne i *bezpieczne* podejście, odbiorcy Cognitive Services kontenerów mogą rozszerzyć kontener z składnikiem czołowym, utrzymując punkt końcowy kontenera jako prywatny. Rozważmy scenariusz, w którym korzystamy z [Istio][istio] jako bramy transferu danych przychodzących. Istio obsługuje uwierzytelnianie za pośrednictwem protokołu HTTPS/TLS i certyfikatu klienta. W tym scenariuszu fronton Istio ujawnia dostęp do kontenera, który przedstawia certyfikat klienta zatwierdzony wcześniej z Istio.

[Nginx][nginx] jest innym popularnym wyborem w tej samej kategorii. Zarówno Istio, jak i Nginx działają jako siatka usługi i oferują dodatkowe funkcje, takie jak równoważenie obciążenia, Routing i sterowanie szybkością.

### <a name="container-networking"></a>Sieć kontenerów

Kontenery Cognitive Services są wymagane do przesyłania informacji o pomiarach dotyczących rozliczeń. Jedynym wyjątkiem jest *kontenery w trybie offline* , ponieważ są one zgodne z inną metodą rozliczania. Niezezwolenie na wyświetlanie listy różnych kanałów sieciowych, na których zależą Cognitive Services kontenery, uniemożliwi pracę kontenera.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Zezwalaj na wyświetlanie listy domen i portów Cognitive Services

Host powinien zezwalać na listę **portów 443** i następujących domen:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Wyłącz dokładną inspekcję pakietów

> [Głębokie Inspekcja pakietów](https://en.wikipedia.org/wiki/Deep_packet_inspection) (dpi) to typ przetwarzania danych, który sprawdza szczegółowo dane wysyłane przez sieć komputerową i zazwyczaj wykonuje akcję przez zablokowanie, ponowne kierowanie lub zarejestrowanie odpowiednio.

Wyłącz wartość DPI w bezpiecznych kanałach, które kontenery Cognitive Services tworzyć na serwerach firmy Microsoft. Niewykonanie tej czynności uniemożliwi poprawne działanie kontenera.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
