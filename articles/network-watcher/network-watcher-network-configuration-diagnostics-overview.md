---
title: Wprowadzenie do diagnostyki konfiguracji sieci na platformie Azure Network Watcher | Microsoft Docs
description: Ta strona zawiera omówienie diagnostyki konfiguracji Network Watcher sieci
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 4d308b8a1a589308e2481c8bb9e0dc9ec64be25b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996697"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Wprowadzenie do diagnostyki konfiguracji sieci w usłudze Azure Network Watcher

Narzędzie do diagnostyki konfiguracji sieci pomaga klientom zrozumieć, które przepływy ruchu będą dozwolone lub odrzucane na platformie Azure Virtual Network wraz ze szczegółowymi informacjami na temat debugowania. Może pomóc w zrozumieniu, czy reguły sieciowej grupy zabezpieczeń są prawidłowo skonfigurowane. 

## <a name="pre-requisites"></a>Wymagania wstępne
W przypadku korzystania z diagnostyki konfiguracji sieci należy włączyć Network Watcher w ramach subskrypcji. Zobacz [Tworzenie wystąpienia usługi Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create) , aby je włączyć.

## <a name="background"></a>Tło

- Twoje zasoby na platformie Azure są połączone za pośrednictwem sieci wirtualnych (sieci wirtualnych) i podsieci. Zabezpieczeniami tych sieci wirtualnych i podsieci można zarządzać przy użyciu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń).
- SIECIOWEJ grupy zabezpieczeń zawiera listę reguł zabezpieczeń, które zezwalają na ruch sieciowy lub odrzucają je do zasobów, do których jest podłączony. Sieciowych grup zabezpieczeń można kojarzyć z podsieciami, poszczególnymi maszynami wirtualnymi lub interfejsami sieciowymi podłączonymi do maszyn wirtualnych. 
- Wszystkie przepływy ruchu w sieci są oceniane przy użyciu reguł w odpowiednich sieciowej grupy zabezpieczeń.
- Reguły są oceniane na podstawie numeru priorytetu od najniższego do najwyższego. 

## <a name="how-does-network-configuration-diagnostic-work"></a>Jak działa Diagnostyka konfiguracji sieci? 

Dla danego przepływu narzędzie NCD uruchamia symulację przepływu i zwraca informację, czy przepływ będzie dozwolony (lub odrzucony) i szczegółowe informacje o regułach zezwalania/odmawiania przepływu.  Klienci muszą podać szczegóły przepływu, takie jak źródło, miejsce docelowe, protokół itp. Narzędzie zwraca czy ruch był dozwolony lub zabroniony, sieciowej grupy zabezpieczeń reguły, które zostały ocenione dla określonego przepływu i wyniki oceny dla każdej reguły.

## <a name="next-steps"></a>Następne kroki

Używanie diagnostyki konfiguracji sieci za pomocą innych interfejsów
 - [Interfejs API REST](https://docs.microsoft.com/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [Program PowerShell](https://docs.microsoft.com/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic?view=azps-4.6.1)
 - [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_run_configuration_diagnostic)

