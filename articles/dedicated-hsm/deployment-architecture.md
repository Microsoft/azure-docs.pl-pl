---
title: Architektura wdrożenia — dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Podstawowe zagadnienia dotyczące projektowania w przypadku korzystania z dedykowanego modułu HSM platformy Azure jako części architektury aplikacji
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 6a0767b077886337331f24b15715247006f3fe2c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94888899"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Architektura wdrożenia usługi Azure Dedicated HSM

Dedykowany moduł HSM platformy Azure udostępnia magazyn kluczy kryptograficznych na platformie Azure. Spełnia rygorystyczne wymagania w zakresie zabezpieczeń. Klienci będą mogli korzystać z dedykowanego modułu HSM platformy Azure, jeśli:

* Musi być zgodna z certyfikatem FIPS 140-2 Level 3
* Wymagaj, aby mieli wyłączny dostęp do modułu HSM
* powinna mieć pełną kontrolę nad swoimi urządzeniami

Sprzętowych modułów zabezpieczeń są dystrybuowane w centrach danych firmy Microsoft i mogą być łatwo udostępniane jako para urządzeń jako podstawa rozwiązania o wysokiej dostępności. Mogą być również wdrażane między regionami w celu odporności na awarie. Regiony z dedykowanym modułem HSM są obecnie dostępne na [stronie produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=azure-dedicated-hsm). 

Każdy region ma Stojaki modułu HSM wdrożone w dwóch niezależnych centrach danych lub co najmniej dwóch niezależnych strefach dostępności. Na przykład Republika Południowej Azja Wschodnia ma trzy strefy dostępności i Wschodnie stany USA 2 mają dwa. Istnieje łącznie osiem regionów w Europie, Azji i USA, które oferują dedykowaną usługę HSM i te zmiany w miarę dodawania nowych stojaków modułu HSM w nowych regionach. Aby uzyskać więcej informacji na temat regionów świadczenia usługi Azure, zapoznaj się z  [informacjami o oficjalnych regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
Niektóre czynniki projektowe dla dowolnego dedykowanego rozwiązania HSM są lokalizacjami/opóźnieniami, wysoką dostępnością i obsługą dla innych aplikacji rozproszonych.

## <a name="device-location"></a>Lokalizacja urządzenia

Optymalna lokalizacja urządzenia HSM znajduje się w najbliższym sąsiedztwie w aplikacjach wykonujących operacje kryptograficzne. Opóźnienie w regionie powinno być w milisekundach jednocyfrowych. Opóźnienie między regionami może wynosić od 5 do 10 razy większe niż to możliwe.

## <a name="high-availability"></a>Wysoka dostępność

Aby zapewnić wysoką dostępność, klient musi używać dwóch urządzeń HSM w regionie skonfigurowanym przy użyciu oprogramowania Thanles jako pary o wysokiej dostępności. Ten typ wdrożenia zapewnia dostępność kluczy, jeśli pojedyncze urządzenie napotka problem uniemożliwiający przetworzenie kluczowych operacji. Znacznie zmniejsza to ryzyko podczas przeprowadzania konserwacji przerwania/naprawy, takich jak wymiana zasilacza. Ważne jest, aby projekt był uwzględniany dla dowolnego rodzaju awarii poziomu regionalnego. Awarie poziomów regionalnych mogą wystąpić w przypadku klęsk żywiołowych, takich jak huragany), powodzi lub ziemi. Te typy zdarzeń powinny być skorygowane przez aprowizacji urządzeń HSM w innym regionie. Urządzenia wdrożone w innym regionie mogą łączyć się ze sobą za pośrednictwem konfiguracji oprogramowania firmy Thales. Oznacza to, że minimalne wdrożenie rozwiązania o wysokiej dostępności i odporności na awarie to cztery urządzenia HSM w dwóch regionach. Lokalna nadmiarowość i nadmiarowość w różnych regionach mogą służyć jako linia bazowa w celu dodania kolejnych wdrożeń urządzeń HSM w celu zapewnienia obsługi opóźnień, pojemności lub spełnienia innych wymagań specyficznych dla aplikacji.

## <a name="distributed-application-support"></a>Obsługa aplikacji rozproszonych

Dedykowane urządzenia HSM są zwykle wdrażane w celu obsługi aplikacji, które muszą wykonywać operacje związane z magazynem kluczy i pobieraniem kluczy. Dedykowane urządzenia modułu HSM mają 10 partycji do obsługi niezależnych aplikacji. Lokalizacja urządzenia powinna być oparta na całościowym widoku wszystkich aplikacji, które muszą korzystać z usługi.

## <a name="next-steps"></a>Następne kroki

Po ustaleniu architektury wdrożenia większość działań konfiguracyjnych do wdrożenia tej architektury zostanie udostępniona przez firmy Thales. Obejmuje to konfigurację urządzeń oraz scenariusze integracji aplikacji. Aby uzyskać więcej informacji, Skorzystaj z portalu [obsługi klienta firmy Thales](https://supportportal.gemalto.com/csm/) i Pobierz przewodniki dotyczące administracji i konfiguracji. W witrynie partnerskiej firmy Microsoft są dostępne różne przewodniki dotyczące integracji.
Zaleca się, aby wszystkie kluczowe pojęcia dotyczące usługi, takie jak wysoka dostępność i zabezpieczenia, były dobrze zrozumiałe przed zainicjowaniem obsługi urządzeń lub projektem aplikacji i wdrożeniem.
Dalsze tematy dotyczące poziomu koncepcji:

* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowanie](monitoring.md)
