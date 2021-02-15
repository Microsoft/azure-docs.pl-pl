---
title: Analiza zespołu i środowisko AI
titleSuffix: Azure Data Science Virtual Machine
description: Wzorce wdrażania Data Science VM w środowisku zespołu przedsiębiorstwa.
keywords: głębokie uczenie się, AI, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, analiza geoprzestrzenna, proces nauki o danych zespołowych
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: a22b253081a6695758a737a506620f2f56e90227
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519698"
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

* [Bezpieczeństwo sieci](../../security/fundamentals/network-overview.md)
* [Monitorowanie](../../azure-monitor/insights/monitor-vm-azure.md) i [Zarządzanie](../../virtual-machines/maintenance-and-updates.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json%252c%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json%253ftoc%253d%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rejestrowanie i przeprowadzanie inspekcji](../../security/fundamentals/log-audit.md)
* [Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](../../role-based-access-control/overview.md)
* [Ustawienie zasad i wymuszanie](../../governance/policy/overview.md)
* [Oprogramowanie chroniące przed złośliwym kodem](../../security/fundamentals/antimalware.md)
* [Szyfrowanie](../../virtual-machines/windows/disk-encryption-overview.md)
* [Odnajdywanie i zarządzanie danymi](../../data-catalog/index.yml)

Na koniec [centrum architektury platformy Azure](/azure/architecture/) zawiera szczegółową kompleksową architekturę i modele umożliwiające tworzenie infrastruktury analitycznej opartej na chmurze i zarządzanie nią.