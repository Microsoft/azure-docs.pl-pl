---
title: Rozwiązanie VMware firmy Azure według CloudSimple września 2020 Update
description: Ten artykuł zawiera informacje o tym, czego można oczekiwać podczas tej operacji konserwacji i zmian w chmurze prywatnej.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89447977"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Rozwiązanie VMware firmy Azure według CloudSimple września 2020 Update

Na wrzesień zostanie przeprowadzona ważna aktualizacja usługi rozwiązań VMware platformy Azure. Powiadomienie e-mail wysyłane w ramach konserwacji będzie obejmować oś czasu konserwacji. Ten artykuł zawiera informacje o tym, czego można oczekiwać podczas tej operacji konserwacji i zmian w chmurze prywatnej.

> [!NOTE]
> Jest to niezakłócone uaktualnienie. Podczas uaktualniania może zostać wyświetlony jeden z elementów nadmiarowych.

## <a name="vmware-infrastructure-upgrade"></a>Uaktualnianie infrastruktury VMware

Infrastruktura programu VMware w chmurze prywatnej zostanie zaktualizowana do nowszej wersji. Obejmuje to aktualizacje dla składników vCenter, ESXi, NSX-T i hybrydowego rozszerzenia chmury (w przypadku wdrożenia) w chmurze prywatnej.

Podczas uaktualniania do chmury prywatnej zostanie dodany nowy węzeł, zanim węzeł zostanie umieszczony w trybie konserwacji dla operacji uaktualniania. Dzięki temu pojemność chmury prywatnej i dostępność chmury prywatnej jest zachowywana w trakcie procesu uaktualniania. Podczas uaktualniania składników programu VMware mogą pojawić się alarmy wyświetlane w interfejsie użytkownika programu vCenter. Alarmy są częścią operacji konserwacyjnych wykonywanych przez zespół operacyjny usługi.

**Wersja składnika**

- ESXi 6,7 U3
- vCenter 6.7 U3
- Sieci vSAN 6,7
- NSX Data Center 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>Aktualizacje centrum danych

Ta aktualizacja obejmuje aktualizacje infrastruktury centrum danych. Aktualizacje, które nie są zakłócane, zostaną wykonane w okresie konserwacji. Podczas procesu aktualizacji będzie zauważalna zmniejszona nadmiarowość. Alerty mogą być generowane w ramach infrastruktury programu VMware w chmurze prywatnej, obwodów usługi ExpressRoute, połączeń GlobalReach oraz wszelkich urządzeń sieci VPN typu lokacja-lokacja związanych z jedną z tych łączy. Jest to normalne w trakcie aktualizacji, ponieważ składniki zostaną ponownie uruchomione w ramach aktualizacji.

-   Jeśli sieć VPN typu lokacja-lokacja jest wdrażana jako pojedyncze wystąpienie (inne niż HA), może być konieczne ponowne nawiązanie połączenia sieci VPN.

-   W przypadku korzystania z połączenia sieci VPN typu punkt-lokacja konieczne będzie ponowne nawiązanie połączenia z siecią VPN.

## <a name="post-update"></a>Po aktualizacji

Po zakończeniu aktualizacji powinny być widoczne nowsze wersje składników programu VMware. Jeśli zauważysz jakiekolwiek problemy lub masz pytania, skontaktuj się z naszym zespołem pomocy technicznej, otwierając [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
