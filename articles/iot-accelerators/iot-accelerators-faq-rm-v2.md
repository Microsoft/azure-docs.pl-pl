---
title: Akcelerator rozwiązania do monitorowania zdalnego — często zadawane pytania — Azure | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące akceleratorów rozwiązań do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 64391d7f5a7b1a295be7e404d27e5cbe8c691eb2
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079092"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Często zadawane pytania dotyczące akceleratora rozwiązań do zdalnego monitorowania

Zobacz również ogólne [często zadawane pytania](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Ile kosztuje nowe rozwiązanie do zdalnego monitorowania?

Nowy akcelerator rozwiązania oferuje dwie opcje wdrożenia:

* *Podstawowa* opcja przeznaczona dla deweloperów poszukująca mniejszego kosztu rozwoju lub klientów poszukujących pokazów lub weryfikacji koncepcji.
* *Standardowa* opcja przeznaczona dla przedsiębiorstw, które chcą wdrożyć infrastrukturę przygotowaną do produkcji.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Jak upewnić się, że mogę utrzymać swoje koszty podczas opracowywania rozwiązania?

Oprócz udostępniania dwóch wdrożeń, nowe rozwiązanie do zdalnego monitorowania ma ustawienie umożliwiające włączenie lub wyłączenie wszystkich symulowanych urządzeń na żądanie. Wyłączenie symulacji zmniejsza ilość danych pozyskanych w rozwiązaniu, a tym samym łączny koszt.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Jaka jest różnica między podstawowymi a standardowymi opcjami wdrażania? Jak mogę podjąć decyzję między dwoma opcjami wdrażania?

Każda opcja wdrażania odpowiada różnym potrzebom. Podstawowe wdrożenie zostało zaprojektowane w celu rozpoczęcia i opracowania koncepcji i niewielkich pilotażów. Zapewnia ona ulepszoną architekturę z minimalnymi wymaganymi zasobami i niższymi kosztami. Standardowe wdrożenie zostało zaprojektowane w celu kompilowania i dostosowywania rozwiązania gotowego do produkcji oraz zapewnia wdrożenie z wymaganymi elementami. W celu zapewnienia niezawodności i skalowania mikrousługi aplikacji są kompilowane jako kontenery Docker i wdrażane przy użyciu programu Orchestrator (domyślnie Kubernetes). Program Orchestrator jest odpowiedzialny za wdrażanie i skalowanie aplikacji oraz zarządzanie nią. Należy wybrać opcję w zależności od bieżących potrzeb. W zależności od etapu projektu możesz użyć jednej, drugiej lub kombinacji obu tych elementów.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Jak mogę skonfigurować mapę dynamiczną na pulpicie nawigacyjnym?

Aby uzyskać więcej informacji, zobacz [uaktualnianie klucza mapy, aby wyświetlić urządzenia na mapie dynamicznej](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Gdzie można znaleźć informacje o poprzedniej wersji rozwiązania do zdalnego monitorowania?

Poprzednia wersja akceleratora rozwiązania do zdalnego monitorowania była znana jako IoT Suite wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego. Zarchiwizowaną dokumentację można znaleźć pod adresem [https://docs.microsoft.com/previous-versions/azure/iot-suite/](/previous-versions/azure/iot-suite/) .

### <a name="next-steps"></a>Następne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Poznaj możliwości akceleratora rozwiązania do monitorowania zdalnego](quickstart-remote-monitoring-deploy.md)
* [Omówienie akceleratora rozwiązania do konserwacji predykcyjnej](./iot-accelerators-predictive-walkthrough.md)
* [Wdróż Akcelerator rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](../iot-fundamentals/iot-security-ground-up.md)