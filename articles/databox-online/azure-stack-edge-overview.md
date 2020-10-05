---
title: Microsoft Azure Stack EDGE Pro — Omówienie | Microsoft Docs
description: Opisuje Azure Stack EDGE Pro, rozwiązanie magazynu, które korzysta z urządzenia fizycznego na potrzeby transferu sieciowego na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/09/2020
ms.author: alkohli
ms.openlocfilehash: 7030030699668b3d316743955dabfb2cc175f6e1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90893874"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>Co to jest Azure Stack Edge Pro z układem FPGA?

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Azure Stack EDGE Pro with FPGA to urządzenie obliczeniowe z systemem AI z możliwością transferu danych w sieci. Ten artykuł zawiera omówienie Azure Stack EDGE Pro with FPGA rozwiązanie, korzyści, kluczowe możliwości i scenariusze, w których można wdrożyć to urządzenie.

Azure Stack EDGE Pro with FPGA to rozwiązanie typu "sprzęt jako usługa". Firma Microsoft dostarcza urządzenie zarządzane przez chmurę z wbudowaną, programowalną bramą Gateway Array (FPGA), która umożliwia szybsze działanie systemu AI-inferencing i ma wszystkie możliwości bramy magazynu sieciowego. 

## <a name="use-cases"></a>Przypadki zastosowań

Poniżej przedstawiono różne scenariusze, w których Azure Stack EDGE Pro można używać do szybkiego Machine Learning (ML) inferencing na granicy i przetwarzania wstępnego danych przed wysłaniem ich do platformy Azure.

- **Wnioskowanie z Azure Machine Learning** -with Azure Stack EDGE Pro umożliwia uruchamianie modeli ml w celu uzyskania szybkich wyników, na które można wykonywać działania przed wysłaniem danych do chmury. Pełny zestaw danych można opcjonalnie przesłać, aby kontynuować ponowne uczenie i ulepszanie modeli ML. Aby uzyskać więcej informacji na temat korzystania z wydajnych modeli sprzętu Azure ML na urządzeniu Azure Stack EDGE Pro, zobacz [wdrażanie przyspieszanych modeli sprzętu Azure ml na Azure Stack EDGE Pro](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- Wstępnie **Przetwarzaj** dane przekształceń danych przed wysłaniem ich do platformy Azure w celu utworzenia bardziej funkcjonalnego zestawu danych. Wstępne przetwarzanie umożliwia: 

    - Agregowanie danych.
    - Zmodyfikuj dane, na przykład aby usunąć dane osobowe.
    - Podzbiór danych w celu zoptymalizowania magazynu i przepustowości lub do dalszej analizy.
    - Analizowanie zdarzeń IoT i reagowanie na nie. 

- **Transferowanie danych za pośrednictwem sieci na platformę Azure** — używanie Azure Stack EDGE Pro do łatwego i szybkiego przenoszenia danych do platformy Azure w celu umożliwienia dalszych obliczeń i analiz lub w celach archiwalnych. 

## <a name="key-capabilities"></a>Najważniejsze możliwości

Azure Stack EDGE Pro ma następujące możliwości:

|Możliwość |Opis  |
|---------|---------|
|Przyspieszone inferencing AI| Włączone przez wbudowaną FPGA.|
|Obliczenia       |Umożliwia analizowanie, przetwarzanie i filtrowanie danych.|
|Wysoka wydajność | Obliczenia o wysokiej wydajności i transfery danych.|
|Dostęp do danych     | Bezpośredni dostęp do danych z usług Azure Storage Blob i Azure Files przy użyciu interfejsów API w chmurze w celu dodatkowego przetwarzania danych w chmurze. Lokalna pamięć podręczna na urządzeniu służy do szybkiego dostępu do ostatnio używanych plików.|
|Zarządzane przez chmurę     |Urządzenia i usługi są zarządzane za pośrednictwem Azure Portal.  |
|Przekazywanie w trybie offline     | Tryb bez połączenia obsługuje scenariusze przekazywania w trybie offline.|
|Obsługiwane protokoły     | Obsługa standardowych protokołów SMB i NFS do pozyskiwania danych. <br> Aby uzyskać więcej informacji na temat obsługiwanych wersji, zobacz [Azure Stack Edge — wymagania systemowe](azure-stack-edge-system-requirements.md).|
|Odświeżanie danych     | Możliwość odświeżania plików lokalnych za pomocą najnowszych wersji z chmury.|
|Szyfrowanie    | Obsługa funkcji BitLocker w celu lokalnego szyfrowania danych i zabezpieczania transferu danych do chmury za pośrednictwem protokołu *https*.|
|Ograniczanie przepustowości| Ograniczanie użycia przepustowości w godzinach szczytu.|
|ExpressRoute | Dodano zabezpieczenia przez ExpressRoute. Użyj konfiguracji komunikacji równorzędnej, w której ruch z urządzeń lokalnych do punktów końcowych magazynu w chmurze jest przekazywany przez ExpressRoute. Aby uzyskać więcej informacji, zobacz [Omówienie usługi ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Składniki

Rozwiązanie Azure Stack EDGE Pro składa się z Azure Stack zasobów brzegowych, Azure Stackgo urządzenia fizycznego w programie EDGE Pro oraz lokalnego interfejsu użytkownika sieci Web.

* **Azure Stack Edge urządzenie fizyczne** — serwer z zainstalowanym stojakiem o rozmiarze 1U dostarczony przez firmę Microsoft, który można skonfigurować do wysyłania danych do platformy Azure.
    
* **Azure Stack Edge** — zasób w Azure Portal, który umożliwia zarządzanie urządzeniem z systemem Azure Stack Edge z poziomu interfejsu sieci Web, do którego można uzyskać dostęp z różnych lokalizacji geograficznych. Użyj zasobu brzegowego Azure Stack, aby tworzyć i zarządzać zasobami, wyświetlać i zarządzać urządzeniami oraz alertami oraz zarządzać udziałami.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Aby uzyskać więcej informacji, przejdź do [pozycji Utwórz zamówienie dla urządzenia z usługą Azure Stack EDGE Pro](azure-stack-edge-deploy-prep.md#create-a-new-resource).

* **Azure Stack Edge — lokalny interfejs użytkownika sieci Web** — Użyj lokalnego interfejsu użytkownika sieci Web, aby uruchomić diagnostykę, zamknąć i ponownie uruchomić urządzenie Azure Stack EDGE Pro, wyświetlić dzienniki kopiowania i skontaktować się z pomoc techniczna firmy Microsoft w celu zarejestrowania żądania obsługi.

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Aby uzyskać informacje na temat korzystania z interfejsu użytkownika opartego na sieci Web, przejdź do strony [sieci Web przy użyciu interfejsu użytkownika w celu administrowania Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostępność w danym regionie

Azure Stack Edge urządzenie fizyczne, zasób platformy Azure i docelowe konto magazynu, do którego są przesyłane dane, nie muszą znajdować się w tym samym regionie.

- **Dostępność zasobów** — Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, zobacz Dostępność [produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack EDGE Pro można także wdrożyć w chmurze Azure Government. Aby uzyskać więcej informacji, zobacz [co to jest Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Docelowe konta magazynu** — konta magazynu, w których są przechowywane dane, są dostępne we wszystkich regionach platformy Azure. Regiony, w których przechowywane są konta magazynu Azure Stack dane brzegowe Pro, powinny znajdować się w pobliżu lokalizacji urządzenia w celu uzyskania optymalnej wydajności. Duża odległość konta magazynu od urządzenia powoduje długie opóźnienia i mniejszą wydajność.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [wymagania systemowe Azure Stack Edge](azure-stack-edge-system-requirements.md).
- Zapoznaj się z [limitami Azure Stack krawędzi Pro](azure-stack-edge-limits.md).
- Wdróż [Azure Stack EDGE Pro](azure-stack-edge-deploy-prep.md) w Azure Portal.
