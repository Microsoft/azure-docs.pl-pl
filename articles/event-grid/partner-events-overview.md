---
title: Zdarzenia partnerskie Azure Event Grid
description: Wysyłaj zdarzenia z partnerów Event Grid SaaS i PaaS bezpośrednio do usług platformy Azure z Azure Event Grid.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 31a5fe611871eb4734b6a68e3818592028ebc75c
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506150"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Zdarzenia partnerskie w Azure Event Grid (wersja zapoznawcza)
Funkcja **zdarzenia partnera** umożliwia dostawcy SaaS innych firm publikowanie zdarzeń z usług, dzięki czemu klienci mogą subskrybować te zdarzenia. Ta funkcja oferuje środowisko pierwszej firmy dla źródeł zdarzeń innych firm, ujawniając typ [tematu](concepts.md#topics) , **temat partnera**. Subskrybenci tworzą subskrypcje tego tematu, aby korzystać z zdarzeń. Zawiera również czysty model pub-sub, oddzielając problemy i własność zasobów używanych przez wydawców zdarzeń i subskrybentów.

> [!NOTE]
> Jeśli jesteś nowym przy użyciu Event Grid, zobacz [Omówienie](overview.md), [koncepcje](concepts.md)i [programy obsługi zdarzeń](event-handlers.md).

## <a name="what-is-partner-events-to-a-publisher"></a>Co to są zdarzenia partnerskie dla wydawcy?
W przypadku wydawcy zdarzeń funkcja zdarzeń partnerskich umożliwia wydawcom wykonywanie następujących zadań:

- Dołączanie źródeł zdarzeń do Event Grid
- Utwórz przestrzeń nazw (punkt końcowy), do której mogą publikować zdarzenia
- Tworzenie tematów partnerskich na platformie Azure, którzy są właścicielami i używają zdarzeń

## <a name="what-is-partner-events-to-a-subscriber"></a>Co to są zdarzenia partnerskie na subskrybencie?
Na subskrybencie funkcja zdarzenia partnerskie umożliwia im tworzenie tematów partnerskich na platformie Azure, aby używać zdarzeń ze źródeł zdarzeń innych firm. Użycie zdarzeń jest realizowane przez tworzenie subskrypcji zdarzeń wysyłających zdarzenia (push) do programu obsługi zdarzeń subskrybenta.

## <a name="why-should-i-use-partner-events"></a>Dlaczego warto używać zdarzeń partnerskich?
Jeśli masz co najmniej jedno z poniższych wymagań, możesz chcieć użyć zdarzeń partnerskich.

### <a name="for-publishers"></a>Dla wydawców

- Chcesz, aby Twoje zdarzenia były dostępne na platformie Azure. Użytkownicy mogą filtrować i kierować te zdarzenia za pomocą tematów partnerskich i subskrypcji zdarzeń, których są właścicielami i którymi zarządza. Można użyć innych metod integracji, takich jak [Tematy](custom-topics.md) i [domeny](event-domains.md). Ale nie pozwalają na czystą separację zasobów (tematów partnerskich), zarządzanie i rozliczenia między wydawcami i subskrybentami. Ponadto takie podejście zapewnia bardziej intuicyjne środowisko użytkownika, które ułatwia odnajdywanie i Używanie tematów partnerskich.
- Aby opublikować zdarzenia w jednym punkcie końcowym, należy użyć punktu końcowego przestrzeni nazw. I chcesz, aby można było filtrować te zdarzenia tak, aby były dostępne tylko podzbiór z nich. 
- Chcesz mieć wgląd w metryki związane z opublikowanymi zdarzeniami.
- Chcesz użyć schematu [zdarzeń w chmurze 1,0](https://cloudevents.io/) dla zdarzeń.

### <a name="for-subscribers"></a>Dla subskrybentów

- Chcesz subskrybować zdarzenia od [wydawców innych](#available-third-party-event-publishers) firm i obsłużyć zdarzenia przy użyciu programów obsługi zdarzeń, które znajdują się na platformie Azure lub w innym miejscu.
- Aby przetwarzać zdarzenia ze źródeł innych firm, należy skorzystać z bogatego zestawu funkcji routingu i [miejsc docelowych i programów obsługi zdarzeń](overview.md#event-handlers) . 
- Chcesz zaimplementować luźno powiązane architektury, w których program obsługi subskrybenta/zdarzenia nie rozpoznaje istnienia używanej brokera komunikatów. 
- Wymagany jest odporny mechanizm dostarczania wypychania z obsługą ponownych prób wysyłania oraz co najmniej raz na semantykę.
- Chcesz użyć schematu [zdarzeń w chmurze 1,0](https://cloudevents.io/) dla zdarzeń. 


## <a name="available-third-party-event-publishers"></a>Dostępni wydawcy zdarzeń innych firm
Wydawca zdarzeń innych firm musi przechodzić przez [proces](partner-onboarding-overview.md) dołączania, zanim subskrybenci będą mogli zacząć zużywać swoje zdarzenia. 

Jeśli jesteś subskrybentem i chcesz, aby usługa innej firmy mogła ujawniać swoje zdarzenia za pomocą Event Grid, 

### <a name="auth0"></a>Auth0
**Rozwiązanie Auth0** jest pierwszym dostępnym wydawcą partnerów. Możesz utworzyć [temat partnera rozwiązanie Auth0](auth0-overview.md) , aby połączyć swoje konta rozwiązanie Auth0 i Azure. Ta Integracja umożliwia reagowanie na zdarzenia rozwiązanie Auth0, rejestrowanie ich i monitorowanie w czasie rzeczywistym. Aby ją wypróbować, zobacz [integracja Azure Event Grid z usługą Auto0](auth0-how-to.md)

Jeśli chcesz, aby usługa innej firmy mogła ujawniać swoje zdarzenia za pomocą Event Grid, Prześlij pomysł do [portalu głosowego użytkownika](https://feedback.azure.com/forums/909934-azure-event-grid).
 
## <a name="resources-managed-by-event-publishers"></a>Zasoby zarządzane przez wydawców zdarzeń
Wydawcy zdarzeń tworzą następujące zasoby i zarządzają nimi:

### <a name="partner-registration"></a>Rejestracja partnera
Rejestracja zawiera ogólne informacje powiązane z wydawcą. Definiuje on typ tematu partnera, który jest wyświetlany w Azure Portal jako opcję, gdy użytkownicy próbują utworzyć temat partnera. Wydawca może uwidocznić więcej niż jeden typ tematu partnera, aby dopasować go do potrzeb subskrybentów. Oznacza to, że Wydawca może utworzyć oddzielne rejestracje (typy tematów partnerskich) dla zdarzeń z różnych usług. Na przykład w przypadku usługi Human Resources (HR) Wydawca może zdefiniować temat partnera dla zdarzeń, takich jak przydziały pracownika, promowany przez pracownika i pracownika po lewej stronie. 

Należy pamiętać o następujących kwestiach:

- Widoczne są tylko rejestracje partnerów zatwierdzone przez platformę Azure. 
- Rejestracje są globalne. Oznacza to, że nie są one skojarzone z określonym regionem świadczenia usługi Azure.
- Rejestracja jest zasobem opcjonalnym. Jednak zaleca się utworzenie rejestracji (w przypadku wydawcy). Umożliwia użytkownikom odnajdywanie tematów na stronie **Tworzenie tematu partnera** w [Azure Portal](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic). Następnie użytkownik może wybrać typy zdarzeń (na przykład pracownik przyłączony, pracownik w lewo itd.) podczas tworzenia subskrypcji zdarzeń.

### <a name="namespace"></a>Przestrzeń nazw
Podobnie jak w przypadku [niestandardowych tematów](custom-topics.md) i [domen](event-domains.md), przestrzeń nazw partnera jest regionalnym punktem końcowym do publikowania zdarzeń. Jest to za poorednictwem przestrzeni nazw, które wydawcy tworzą kanały zdarzeń i zarządzają nimi. Przestrzeń nazw działa również jako zasób kontenera dla kanałów zdarzeń.

### <a name="event-channels"></a>Kanały zdarzeń
Kanał zdarzenia to dublowany zasób w temacie partnera. Gdy wydawca tworzy kanał zdarzeń w subskrypcji platformy Azure wydawcy, tworzy również temat partnera w ramach subskrypcji platformy Azure na subskrybencie. Operacje wykonywane względem kanału zdarzeń (z wyjątkiem GET) zostaną zastosowane do odpowiedniego tematu partnera subskrybenta, nawet po usunięciu. Jednak tylko tematy partnerskie to rodzaje zasobów, na których można skonfigurować subskrypcje i dostarczanie zdarzeń.

## <a name="resources-managed-by-subscribers"></a>Zasoby zarządzane przez subskrybentów 
Subskrybenci mogą używać tematów partnerskich zdefiniowanych przez wydawcę i są jedynym typem zasobów, które widzi i którymi zarządza. Po utworzeniu tematu partnera użytkownik będący subskrybentem może tworzyć subskrypcje zdarzeń definiujące reguły filtru do [miejsc docelowych lub programów obsługi zdarzeń](overview.md#event-handlers). W przypadku subskrybentów, tematu partnera i skojarzonych z nim subskrypcji zdarzeń zapewniają takie same bogate możliwości jak [niestandardowe tematy](custom-topics.md) i powiązane z nimi subskrypcje, z jedną istotną różnicą: Tematy partnerskie obsługują tylko [schemat zdarzeń w chmurze 1,0](cloudevents-schema.md), który zapewnia bogatszy zestaw możliwości niż inne obsługiwane schematy.

Na poniższej ilustracji przedstawiono przepływ operacji na płaszczyźnie kontroli.

:::image type="content" source="./media/partner-events-overview/partner-control-plane-flow.png" alt-text="Zdarzenia partnerskie — przepływ płaszczyzny kontroli":::

1. Wydawca tworzy **rejestrację partnera**. Rejestracje partnerów są globalne. Oznacza to, że nie są one skojarzone z określonym regionem świadczenia usługi Azure. Ta czynność jest opcjonalna.
1. Wydawca tworzy **przestrzeń nazw partnera** w określonym regionie.
1. Gdy subskrybent 1 podejmie próbę utworzenia tematu partnera, **kanał zdarzenia** , kanał zdarzenia 1, zostanie najpierw utworzony w ramach subskrypcji platformy Azure wydawcy.
1. Następnie w ramach subskrypcji platformy Azure na subskrybencie zostanie utworzony temat dotyczący **partnera** 1. Subskrybent musi aktywować temat partnera. 
1. Subskrybent 1 tworzy **subskrypcję Azure Logic Apps** w temacie dotyczącym partnera 1.
1. Subskrybent 1 tworzy **subskrypcję usługi Azure Blob Storage** w temacie dotyczącym partnera 1. 
1. Gdy subskrybent 2 podejmie próbę utworzenia tematu partnera, w pierwszej kolejności zostanie utworzony nowy **kanał zdarzenia** , kanał usługi Event 2. 
1. Następnie w ramach subskrypcji platformy Azure w drugim subskrybencie zostanie utworzony **temat partnera** 2. Subskrybent musi aktywować temat partnera. 
1. Subskrybent 2 tworzy **subskrypcję Azure Functions** dla tematu partnera 2. 

## <a name="pricing"></a>Cennik
Tematy dotyczące partnerów są rozliczone według liczby operacji wykonanych podczas korzystania z Event Grid. Aby uzyskać więcej informacji na temat wszystkich typów operacji, które są używane jako podstawa rozliczeń i szczegółowych informacji o cenach, zobacz [Event Grid Cennik](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="limits"></a>Limity
Aby uzyskać szczegółowe informacje na temat limitów dla partnerów, zobacz [limity usługi Event Grid](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) .


## <a name="next-steps"></a>Następne kroki

- [Rozwiązanie Auth0 partnera](auth0-overview.md)
- [Jak korzystać z tematu partnera rozwiązanie Auth0](auth0-how-to.md)
- [Zostań partnerem Event Grid](partner-onboarding-overview.md)