---
title: Informacje o cenach wirtualnych sieci WAN
titleSuffix: Azure Virtual WAN
description: W tym artykule opisano typowe pytania dotyczące cen wirtualnych sieci WAN
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 13451291544f704000ab61d41ed5014fa69298e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517692"
---
# <a name="about-virtual-wan-pricing"></a>Informacje o cenach wirtualnych sieci WAN

Wirtualna sieć WAN platformy Azure oferuje wiele usług sieciowych i zabezpieczeń w ujednoliconej strukturze. Jest on oparty na architekturze gwiazdy, gdzie centra są zarządzane przez firmę Microsoft z różnymi usługami udostępnianymi w centrum, takich jak sieci VPN, ExpressRoute, VPN użytkownika (punkt-lokacja), Zapora, routing itp.

Cena każdej usługi w wirtualnej sieci WAN jest naliczana. W związku z tym nie można zasugerować pojedynczej ceny dla wirtualnej sieci WAN. [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) udostępnia mechanizm umożliwiający uzyskanie kosztów, który jest oparty na usługach udostępnianych w wirtualnej sieci WAN. W tym artykule opisano często zadawane pytania dotyczące cen wirtualnych sieci WAN.

>[!NOTE]
>Aby uzyskać aktualne informacje o cenach, zobacz [Cennik usługi Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/). Opłaty między centrami (między centrami a centrami) nie są wyświetlane na stronie cennika wirtualnej sieci WAN, ponieważ podlegają opłatom za usługi związane z [transferem danych Inter-Region na platformie Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="common-pricing-questions"></a><a name="questions"></a>Często zadawane pytania dotyczące cen

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>Co to jest jednostka skalowania?

**Jednostka skalowania** zapewnia jednostkę zagregowanej pojemności sieci typu lokacja-lokacja (S2S), punkt-lokacja (P2S) i EXPRESSROUTE (er) w koncentratorze wirtualnym. Na przykład:

* **1 jednostka skalowania sieci VPN S2S** implikuje łączną pojemność bramy sieci VPN 500 MB/s (dwa wystąpienia są wdrażane w celu odporności) w koncentratorze wirtualnym o kosztach $0.361/godzinę.
* **1 jednostka skalowania er** oznacza łączną liczbę 2 GB/s bramy modułu tworzenia w koncentratorze wirtualnym.
* **5 jednostek skalowania er** będzie oznaczać całkowitą 10 GB/s bramy er w ramach sieci wirtualnej koncentratora wirtualnego w cenie $0.42 * 5/HR. Funkcja ER zwiększa wartość $0,25/HR od szóstej do 10 jednostek skali.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>Co to jest jednostka połączenia?

**Jednostka połączenia** ma zastosowanie do każdego lokalnego punktu końcowego/innego niż Microsoft, który nawiązuje połączenie z bramami platformy Azure. W przypadku sieci VPN typu lokacja-lokacja ta wartość implikuje rozgałęzienia. W przypadku sieci VPN użytkownika (punkt-lokacja) ta wartość oznacza użytkowników zdalnych. W przypadku ExpressRoute ta wartość oznacza połączenia obwodu usługi ExpressRoute.<br>Na przykład:

* Jedno połączenie z gałęzią łączące się z siecią VPN platformy Azure w ramach kosztów koncentratora wirtualnego $0,05/HR. W związku z tym 100 połączenia rozgałęzienia łączące się z usługą Azure Virtual Hub byłyby kosztem $0,05 * 100/HR.

* Dwa połączenia obwodów ExpressRoute łączące się z koncentratorem wirtualnym byłyby kosztem $0,05 * 2/HR.

* Trzy połączenia użytkownika zdalnego łączące się z bramą P2S usługi Azure Virtual Hub byłyby kosztem $0.03 * 3/HR.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>Jak są obliczane opłaty za transfer danych?

* Żaden ruch wprowadzający platformę Azure nie jest naliczany. Ruch wychodzący z platformy Azure (za pośrednictwem sieci VPN, ExpressRoute lub połączeń sieci VPN typu punkt-lokacja) podlega standardowym [opłatom za transfer danych platformy Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

* W przypadku opłat za transfer danych między wirtualnym koncentratorem sieci WAN a zdalnym koncentratorem lub siecią wirtualną w innym regionie niż koncentrator źródłowy opłaty za transfer danych dotyczą ruchu wychodzącego z centrum. Przykład: ruch wychodzący z centrum Wschodnie stany USA będzie obciążany opłatami $0,02/GB do centralnych centrów USA. Nie jest naliczana opłata za ruch wprowadzający do centrum zachodnie stany USA. Cały ruch z centrum do koncentratora podlega opłatom za Inter-Region (wewnętrzne/międzypunktowe) opłaty za [transfer danych platformy Azure](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Jaka jest różnica między standardową opłatą centrum a standardową opłatą za przetwarzanie centrum?

Wirtualna sieć WAN znajduje się w dwóch wersjach:

* **Podstawowa wirtualna sieć WAN**, w której użytkownicy mogą wdrażać wiele centrów i korzystać z połączenia sieci VPN typu lokacja-lokacja. Podstawowa wirtualna sieć WAN nie ma zaawansowanych możliwości, takich jak w pełni przełączane centra, łączność ExpressRoutea, Sieć VPN użytkownika/połączenie sieci VPN typu punkt-lokacja, łączność przechodnia między sieciami wirtualnymi, łączność VPN i ExpressRoute transport lub Zapora platformy Azure itp. Nie istnieje opłata podstawowa ani opłata za przetwarzanie danych dla centrów w podstawowej wirtualnej sieci WAN.

* **Standardowa wirtualna sieć WAN** oferuje zaawansowane możliwości, takie jak w pełni przełączane centra, łączność ExpressRoutea, Sieć VPN typu punkt-lokacja, łączność między sieciami wirtualnymi, połączenie między sieciami VPN i połączeniami, Zapora platformy Azure itp. Każdy Routing koncentratora wirtualnego jest dostarczany przez router, który umożliwia korzystanie z wielu usług w koncentratorze wirtualnym. Istnieje podstawowa opłata za centrum, która jest naliczana w cenie $0,25/HR. Istnieje również opłata za przetwarzanie danych w routerze koncentratora wirtualnego na potrzeby łączności między sieciami wirtualnymi. Zobacz poniższą ilustrację.

 Na poniższym **przykładzie** przedstawiono dwie sieci wirtualnych, Sieć wirtualna 1 i Sieć wirtualna 2. Załóżmy, że dane o 1 GB/s są wysyłane z maszyny wirtualnej w sieci wirtualnej 1 do innej maszyny wirtualnej w sieci wirtualnej 2. Obowiązują następujące opłaty:

* Opłata podstawowa centrum wirtualnego $0,25/HR

* Opłata za przetwarzanie danych koncentratora wirtualnego dla $0,02/GB przez 1 GB/s

**Przykład**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="Przykład":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).

* W przypadku bieżących cen zobacz [wirtualne sieci WAN — Cennik](https://azure.microsoft.com/pricing/details/virtual-wan/).
