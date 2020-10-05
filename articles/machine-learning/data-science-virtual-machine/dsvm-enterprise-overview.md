---
title: Analiza zespołu i środowisko AI
titleSuffix: Azure Data Science Virtual Machine
description: Wzorce wdrażania Data Science VM w środowisku zespołu przedsiębiorstwa.
keywords: głębokie uczenie się, AI, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, analiza geoprzestrzenna, proces nauki o danych zespołowych
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: b1357c9bb125cb881ac4aa6dd31c9dcaf53954f0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87919943"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Analiza zespołu oparta na Data Science Virtual Machineach i środowisko AI 
[Data Science Virtual Machine](overview.md) (DSVM) zapewnia rozbudowane środowisko na platformie Azure z wbudowanym oprogramowaniem do sztucznej analizy (AI) i analizy danych.

Tradycyjnie DSVM jest używany jako indywidualny pulpit analityczny. Indywidualni analitykowie danych uzyskują produktywność dzięki udostępnionemu, wstępnie skompilowanemu środowisku analitycznemu. Ponieważ duże zespoły analityczne planują środowiska dla swoich analityków danych i deweloperów systemu AI, jednym z motywów cyklicznych jest udostępniona infrastruktura analityczna do tworzenia i eksperymentowania. Ta infrastruktura jest zarządzana zgodnie z zasadami IT dla przedsiębiorstw, które ułatwiają współpracę i spójność w ramach zespołów do nauki i analizy danych.

Infrastruktura udostępniona umożliwia lepsze wykorzystanie środowiska do analizy. Niektóre organizacje wywołują chmurową infrastrukturę analizy danych na podstawie zespołu *.* Umożliwia ona analitykom danych dostęp do różnych zasobów danych w celu szybkiego zrozumienia danych. To środowisko piaskownicy pomaga również analitykom danych uruchamiać eksperymenty, sprawdzać poprawność i tworzyć modele predykcyjne bez wpływu na środowisko produkcyjne.

Ponieważ DSVM działa na poziomie infrastruktury platformy Azure, Administratorzy IT mogą łatwo skonfigurować DSVM do działania zgodnie z zasadami INFORMATYCZNymi przedsiębiorstwa. DSVM zapewnia pełną elastyczność wdrażania różnych architektur udostępniania, jednocześnie zapewniając dostęp do zasobów danych firmowych w kontrolowany sposób.

W tej sekcji omówiono niektóre wzorce i wskazówki, których można użyć do wdrożenia DSVM jako infrastruktury analizy danych w oparciu o zespół. Ponieważ bloki konstrukcyjne dla tych wzorców pochodzą z infrastruktury platformy Azure jako usługi (IaaS), mają zastosowanie do dowolnych maszyn wirtualnych platformy Azure. Ta seria artykułów koncentruje się na zastosowaniu standardowych funkcji infrastruktury platformy Azure do DSVM.

Kluczowymi blokami konstrukcyjnymi środowiska analizy zespołu przedsiębiorstwa są:

* [Skalowanie automatyczne puli DSVMs](dsvm-pools.md)
* [Wspólna tożsamość i dostęp do obszaru roboczego z dowolnego DSVMs w puli](dsvm-common-identity.md)
* [Bezpieczny dostęp do źródeł danych](dsvm-secure-access-keys.md)


Ta seria zawiera wskazówki i wskaźniki dla każdego z powyższych tematów. Nie dotyczy to wszystkich zagadnień i wymagań dotyczących wdrażania DSVMs w dużych konfiguracjach przedsiębiorstwa. Oto kilka innych zasobów platformy Azure, których można użyć podczas implementowania wystąpień DSVM w przedsiębiorstwie:

* [Bezpieczeństwo sieci](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Monitorowanie](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) i [Zarządzanie](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Rejestrowanie i przeprowadzanie inspekcji](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Ustawienie zasad i wymuszanie](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Szyfrowanie](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview)
* [Odnajdywanie i zarządzanie danymi](https://docs.microsoft.com/azure/data-catalog/)

Na koniec [centrum architektury platformy Azure](https://docs.microsoft.com/azure/architecture/) zawiera szczegółową kompleksową architekturę i modele umożliwiające tworzenie infrastruktury analitycznej opartej na chmurze i zarządzanie nią.