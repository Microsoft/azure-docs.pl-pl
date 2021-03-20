---
title: Ponowna konfiguracja na platformie Azure Service Fabric
description: Informacje o konfiguracjach replik usługi stanowej i procesach ponownej konfiguracji Service Fabric są stosowane w celu zachowania spójności i dostępności podczas zmiany.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75609999"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Ponowna konfiguracja na platformie Azure Service Fabric
*Konfiguracja* jest definiowana jako repliki i ich role dla partycji usługi stanowej.

*Ponowna konfiguracja* jest procesem przechodzenia jednej konfiguracji do innej konfiguracji. Wprowadza zmianę zestawu replik dla partycji usługi stanowej. Stara konfiguracja jest nazywana *poprzednią konfiguracją (komputer)*, a nowa konfiguracja jest nazywana *bieżącą konfiguracją (DW)*. Protokół ponownej konfiguracji na platformie Azure Service Fabric zachowuje spójność i utrzymuje dostępność podczas wprowadzania zmian w zestawie replik.

Menedżer trybu failover inicjuje ponowne konfiguracje w odpowiedzi na różne zdarzenia w systemie. Na przykład jeśli podstawowy zakończy się niepowodzeniem, ponowna konfiguracja zostanie zainicjowana, aby podwyższyć poziom aktywnego elementu pomocniczego do podstawowego. Inny przykład jest odpowiedzią na uaktualnienia aplikacji, gdy może być konieczne przeniesienie podstawowego do innego węzła w celu uaktualnienia węzła.

## <a name="reconfiguration-types"></a>Typy ponownej konfiguracji
Ponowne konfiguracje mogą być klasyfikowane do dwóch typów:

- Ponowne konfiguracje, w których zmiana podstawowa:
    - **Tryb failover**: przełączenie w tryb failover powoduje ponowną konfigurację w odpowiedzi na awarię działającego elementu podstawowego.
    - **SwapPrimary**: zamiany są rekonfiguracjami, w których Service Fabric muszą przenieść uruchomiony podstawowy z jednego węzła do drugiego, zazwyczaj w odpowiedzi na Równoważenie obciążenia lub uaktualnienie.

- Ponowne konfiguracje, w których nie jest zmieniana wartość podstawowa.

## <a name="reconfiguration-phases"></a>Etapy ponownej konfiguracji
Ponowna konfiguracja jest wykonywana w kilku fazach:

- **Phase0**: Ta faza ma miejsce w przypadku ponownej konfiguracji wymiany, w której bieżący podstawowy przeniesie stan do nowego podstawowego i przejść do aktywnego pomocniczego.

- **Faza fazy: Ta** faza odbywa się podczas ponownej konfiguracji, w której jest zmieniana wartość podstawowa. W tej fazie Service Fabric identyfikuje poprawną podstawową między bieżącymi replikami. Ta faza nie jest wymagana podczas ponownej konfiguracji wymiany podstawowego, ponieważ nowy podstawowy został już wybrany. 

- Faza **2**: w tej fazie Service Fabric zapewnia, że wszystkie dane są dostępne w większości replik bieżącej konfiguracji.

Istnieje kilka innych faz, które są przeznaczone tylko do użytku wewnętrznego.

## <a name="stuck-reconfigurations"></a>Zablokowane ponowne konfiguracje
Ponowne konfiguracje mogą zostać *zablokowane* z różnych powodów. Poniżej wymieniono niektóre typowe przyczyny:

- **Repliki w dół**: niektóre fazy ponownej konfiguracji wymagają większości replik w konfiguracji.
- **Problemy z siecią lub komunikacją**: ponowne konfiguracje wymagają połączenia sieciowego między różnymi węzłami.
- **Błędy interfejsu API**: protokół ponownej konfiguracji wymaga, aby implementacje usług kończyły niektóre interfejsy API. Na przykład nieuznawanie tokenu anulowania w niezawodnej usłudze powoduje zablokowanie SwapPrimary ponownych konfiguracji.

Za pomocą raportów kondycji ze składników systemu, takich jak System.FM, system. RA i system. Rd, można zdiagnozować, gdzie jest zastosowana ponowna konfiguracja. Na [stronie raport o kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) opisano te raporty kondycji.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pojęć Service Fabric, zobacz następujące artykuły:

- [Cykl życia interfejsu Reliable Services — C#](service-fabric-reliable-services-lifecycle.md)
- [Raporty kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md)
