---
title: Zapora IP dla kont usługi Azure Cosmos
description: Dowiedz się, jak zabezpieczyć Azure Cosmos DB dane przy użyciu zasad kontroli dostępu IP do obsługi zapory.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "66241081"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Zapora bazująca na adresach IP w usłudze Azure Cosmos DB

Aby zabezpieczyć dane przechowywane na koncie, Azure Cosmos DB obsługuje model autoryzacji oparty na kluczu tajnym, który wykorzystuje silne kod uwierzytelniania wiadomości oparte na wykorzystaniu algorytmu (HMAC). Ponadto Azure Cosmos DB obsługuje kontrolę dostępu opartą na protokole IP na potrzeby obsługi zapory przychodzącej. Ten model jest podobny do reguł zapory tradycyjnego systemu bazy danych i zapewnia dodatkowy poziom zabezpieczeń dla Twojego konta. Za pomocą zapór można skonfigurować konto platformy Azure Cosmos, aby było dostępne tylko z zatwierdzonego zestawu maszyn i/lub usług w chmurze. Dostęp do danych przechowywanych w bazie danych usługi Azure Cosmos z tych zatwierdzonych zestawów maszyn i usług będzie nadal wymagał, aby obiekt wywołujący mógł przedstawić prawidłowy token autoryzacji.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Kontrola dostępu do adresu IP — Omówienie

Domyślnie konto usługi Azure Cosmos jest dostępne z Internetu, o ile do żądania dołączono prawidłowy token autoryzacji. Aby skonfigurować kontrolę dostępu opartą na zasadach IP, użytkownik musi podać zestaw adresów IP lub zakresy adresów IP w formularzu CIDR (bezklasowe Inter-Domain Routing), który będzie uwzględniony jako lista dozwolonych adresów IPv4 klienta, aby uzyskać dostęp do danego konta usługi Azure Cosmos. Po zastosowaniu tej konfiguracji wszystkie żądania pochodzące z maszyn spoza tej listy dozwolonych otrzymują odpowiedź 403 (dostęp zabroniony). W przypadku korzystania z zapory IP zaleca się umożliwienie Azure Portal dostępu do Twojego konta. Dostęp jest wymagany, aby umożliwić korzystanie z Eksploratora danych oraz pobieranie metryk dla konta, które są wyświetlane na Azure Portal. W przypadku korzystania z Eksploratora danych, oprócz zezwalania Azure Portal na dostęp do konta, należy również zaktualizować ustawienia zapory, aby dodać bieżący adres IP do reguł zapory. Należy pamiętać, że zmiany w zaporze mogą zająć do 15 min. 

Zaporę opartą na protokole IP można połączyć z kontrolą dostępu do podsieci i sieci wirtualnej. Łącząc je, można ograniczyć dostęp do dowolnego źródła, które ma publiczny adres IP i/lub z określonej podsieci w sieci wirtualnej. Aby dowiedzieć się więcej o korzystaniu z funkcji kontroli dostępu opartej na podsieci i sieci wirtualnej, zobacz [dostęp Azure Cosmos DB zasobów z sieci wirtualnych](vnet-service-endpoint.md).

Podsumowując, token autoryzacji jest zawsze wymagany do uzyskania dostępu do konta usługi Azure Cosmos. Jeśli nie skonfigurowano zapory IP i listy Access Control sieci wirtualnej (ACL), do konta usługi Azure Cosmos można uzyskać dostęp przy użyciu tokenu autoryzacji. Po skonfigurowaniu na koncie usługi Azure Cosmos zapory IP lub listy ACL sieci wirtualnej lub obu z nich są dostępne tylko żądania pochodzące z określonych źródeł (i z tokenem autoryzacji). 

## <a name="next-steps"></a>Następne kroki

Następnie możesz skonfigurować zaporę IP lub punkt końcowy usługi sieci wirtualnej dla swojego konta, korzystając z następujących dokumentów:

* [Jak skonfigurować zaporę IP dla konta usługi Azure Cosmos](how-to-configure-firewall.md)
* [Dostęp do zasobów Azure Cosmos DB z sieci wirtualnych](vnet-service-endpoint.md)
* [Jak skonfigurować punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos](how-to-configure-vnet-service-endpoint.md)




