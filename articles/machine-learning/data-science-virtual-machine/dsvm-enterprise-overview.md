---
title: Analiza zespołów i środowisko sztucznej inteligencji
titleSuffix: Azure Data Science Virtual Machine
description: Wzorce wdrażania maszyny Wirtualnej nauki o danych w środowisku zespołu przedsiębiorstwa.
keywords: głębokie uczenie się, sztuczna inteligencja, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, analityka geoprzestrzenna, proces nauki o danych zespołowych
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 1f3a24afb2238c2448cb19b3889467a1b1819724
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460549"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Analiza zespołowa oparta na maszynach wirtualnych do nauki danych i środowisko sztucznej inteligencji 
[Maszyna wirtualna do nauki o danych](overview.md) (DSVM) zapewnia bogate środowisko na platformie Azure, z wbudowanym oprogramowaniem do sztucznej inteligencji (AI) i analizy danych.

Tradycyjnie dsvm był używany jako indywidualny pulpit analityczny. Poszczególni analitycy danych zyskują produktywność dzięki temu wspólnemu, wstępnie utworzonemu środowisku analitycznym. Ponieważ duże zespoły analityczne planują środowiska dla swoich analityków danych i deweloperów sztucznej inteligencji, jednym z cyklicznych tematów jest udostępniona infrastruktura analityczna do tworzenia i eksperymentowania. Ta infrastruktura jest zarządzana zgodnie z zasadami IT dla przedsiębiorstw, które ułatwiają również współpracę i spójność między zespołami analityki danych i analityki.

Współużytkowana infrastruktura umożliwia lepsze wykorzystanie it środowiska analitycznego. Niektóre organizacje nazywają infrastrukturę analizy danych/analityki zespołowej *izolowanymi analizami.* Umożliwia analitykom danych dostęp do różnych zasobów danych w celu szybkiego zrozumienia danych. To środowisko piaskownicy pomaga również analitykom danych uruchamiać eksperymenty, sprawdzać hipotezy i tworzyć modele predykcyjne bez wpływu na środowisko produkcyjne.

Ponieważ dsvm działa na poziomie infrastruktury platformy Azure, administratorzy IT można łatwo skonfigurować DSVM do pracy zgodnie z zasadami IT przedsiębiorstwa. DSVM oferuje pełną elastyczność we wdrażaniu różnych architektur udostępniania, a jednocześnie oferuje dostęp do firmowych zasobów danych w kontrolowany sposób.

W tej sekcji omówiono niektóre wzorce i wskazówki, których można użyć do wdrożenia dsvm jako infrastruktury do nauki o danych zespołowej. Ponieważ bloki konstrukcyjne dla tych wzorców pochodzą z infrastruktury platformy Azure jako usługi (IaaS), mają zastosowanie do wszystkich maszyn wirtualnych platformy Azure. Ta seria artykułów koncentruje się na stosowaniu tych standardowych możliwości infrastruktury platformy Azure do dsvm.

Kluczowe elementy konstrukcyjne środowiska analizy zespołu przedsiębiorstwa obejmują:

* [Pula śmigał automatycznie dsvms](dsvm-pools.md)
* [Wspólna tożsamość i dostęp do obszaru roboczego z dowolnego dsvms w puli](dsvm-common-identity.md)
* [Bezpieczny dostęp do źródeł danych](dsvm-secure-access-keys.md)


Ta seria zawiera wskazówki i wskaźniki dla każdego z powyższych tematów. Nie obejmuje wszystkich zagadnień i wymagań dotyczących wdrażania dsvm w dużych konfiguracjach przedsiębiorstwa. Oto kilka innych zasobów platformy Azure, których można używać podczas implementowania wystąpień DSVM w przedsiębiorstwie:

* [Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Monitorowanie](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) i [zarządzanie](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Rejestrowanie i przeprowadzanie inspekcji](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Ustalanie i egzekwowanie zasad](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Szyfrowanie](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview.md)
* [Odnajdowanie danych i zarządzanie nimi](https://docs.microsoft.com/azure/data-catalog/)

Na koniec [Centrum architektury platformy Azure](https://docs.microsoft.com/azure/architecture/) udostępnia szczegółową architekturę end-to-end i modele do tworzenia i zarządzania infrastrukturą analityczną opartą na chmurze.