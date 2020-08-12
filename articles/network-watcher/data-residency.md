---
title: Zamieszkania danych na platformie Azure Network Watcher | Microsoft Docs
description: Informacje o miejscu zamieszkania usługi Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: dff5519c1b36a0a7738cb2529c2f4460ecf61e48
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117839"
---
# <a name="data-residency-for-azure-network-watcher"></a>Zamieszkania danych dla Network Watcher platformy Azure
Usługa Azure Network Watcher nie przechowuje danych klienta z wyjątkiem usługi monitor połączeń (wersja zapoznawcza).


## <a name="connection-monitor-preview-data-residency"></a>Dane z miejsca zamieszkania (wersja zapoznawcza) monitora połączeń
Usługa *monitor połączeń (wersja zapoznawcza)* przechowuje dane klientów. Te dane są automatycznie przechowywane przez Network Watcher w jednym regionie. *Monitor połączeń (wersja zapoznawcza)* automatycznie spełnia wymagania dotyczące miejsca zamieszkania danych w regionie, w tym określone w [Centrum zaufania](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Zamieszkać dane Singapur

Na platformie Azure funkcja umożliwiająca przechowywanie danych klienta w jednym regionie jest obecnie dostępna tylko w regionie Azja Południowo-Wschodnia (Singapur) Azja i Pacyfik geograficznej. W przypadku wszystkich innych regionów dane klienta są przechowywane w lokalizacji geograficznej. Aby uzyskać więcej informacji, zobacz [Centrum zaufania](https://azuredatacentermap.azurewebsites.net/).

> [!Note]
> **Wcześniejsza replikacja** Klienci mogą przechowywać adresy IP użytkowników końcowych za pomocą swojego wystąpienia Network Watcher, aby Network Watcher mogli monitorować zmiany dotyczące liczby, opóźnień i topologii sieci związane z adresami IP użytkowników końcowych. Te adresy IP użytkowników końcowych mogą być klasyfikowane jako dane klienta. Od 15 lipca 2020 Network Watcher przechowywane te dane w jednym regionie. (Dane klienta nie są już replikowane do HK). Te dane nie są już replikowane do HK. Dane klienta są i szyfrowane w stanie spoczynku.
> 
> Jeśli dane klienta były dostępne dla osób trzecich, które umożliwią podmiotom trzecim poznanie adresu IP, ale nie udzielą osobie trzeciej dostępu do komputera lub urządzenia skojarzonego z tym adresem IP. Network Watcher uważa, że żadne osoby trzecie nie uzyskały dostępu do danych klienta w HK.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z omówieniem [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
