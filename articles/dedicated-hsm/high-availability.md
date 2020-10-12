---
title: Wysoka dostępność — dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Poznaj podstawowe zagadnienia dotyczące wysokiej dostępności dedykowanego modułu HSM platformy Azure. Ten artykuł zawiera przykład.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 8f8fa2f12825fe88218fe7033a1721cb49fc7335
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88189858"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Dedykowana wysoka dostępność platformy Azure

Dedykowany moduł HSM platformy Azure jest przypięty przez centra danych o wysokiej dostępności firmy Microsoft. Jednak każde centrum danych o wysokiej dostępności jest podatne na zlokalizowane awarie i w skrajnych okolicznościach, w przypadku awarii poziomu regionalnego. Firma Microsoft wdraża urządzenia HSM w różnych centrach danych w regionie w celu zapewnienia, że obsługa wielu urządzeń nie prowadzi do udostępniania jednego stojaka. Dodatkowy poziom wysokiej dostępności można osiągnąć, kojarząc te sprzętowych modułów zabezpieczeń w centrach danych w regionie przy użyciu funkcji grupy HA firmy Gemalto. Istnieje również możliwość sparowania urządzeń w różnych regionach w celu rozbudowy regionalnego trybu failover w sytuacji odzyskiwania po awarii. Dzięki tej konfiguracji wielowarstwowej wysokiej dostępności dowolny błąd urządzenia zostanie automatycznie rozkierowany do działania aplikacji. Wszystkie centra danych mają również urządzenia zapasowe i składniki w lokacji, tak aby wszystkie urządzenia, które uległy awarii, mogły zostać zastąpione w odpowiednim czasie.

## <a name="high-availability-example"></a>Przykład wysokiej dostępności

Informacje na temat konfigurowania urządzeń HSM w celu zapewnienia wysokiej dostępności na poziomie oprogramowania są dostępne w podręczniku administratora firmy Gemalto Luna Network HSM. Ten dokument jest dostępny na  [stronie modułu HSM firmy Gemalto](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).

Na poniższym diagramie przedstawiono architekturę o wysokiej dostępności. Używa wielu urządzeń w regionie i wielu urządzeń sparowanych w osobnym regionie. W tej architekturze są stosowane co najmniej cztery urządzenia HSM i wirtualne składniki sieciowe.

![Diagram wysokiej dostępności](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Następne kroki

Zaleca się, aby wszystkie kluczowe pojęcia dotyczące usługi, takie jak wysoka dostępność i zabezpieczenia, były dobrze zrozumiałe przed zainicjowaniem obsługi urządzeń oraz projektem lub wdrożeniem aplikacji.
Dalsze tematy dotyczące poziomu koncepcji:

* [Architektura wdrożenia](deployment-architecture.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowanie](monitoring.md)

Aby uzyskać szczegółowe informacje dotyczące konfigurowania urządzeń HSM pod kątem wysokiej dostępności, zapoznaj się z sekcją firmy Gemalto Customer Support Portal dla przewodników administratora i zobacz sekcję 6.
