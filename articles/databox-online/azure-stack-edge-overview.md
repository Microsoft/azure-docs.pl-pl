---
title: Microsoft Azure Stack EDGE Pro — Omówienie | Microsoft Docs
description: Opisuje Azure Stack EDGE Pro, rozwiązanie magazynu, które korzysta z urządzenia fizycznego na potrzeby transferu sieciowego na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/15/2021
ms.author: alkohli
ms.openlocfilehash: 3973235991a16d118b47d7289f3a1825621a9023
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574683"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>Co to jest Azure Stack Edge Pro z układem FPGA?

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

Azure Stack EDGE Pro with FPGA to urządzenie obliczeniowe z systemem AI z możliwością transferu danych w sieci. Ten artykuł zawiera omówienie rozwiązań Azure Stack EDGE Pro with FPGA, korzyści, kluczowych możliwości i scenariuszy wdrażania.

Azure Stack EDGE Pro with FPGA to rozwiązanie typu "sprzęt jako usługa". Firma Microsoft dostarcza urządzenie zarządzane przez chmurę z wbudowaną, programowalną bramą Gateway Array (FPGA), która umożliwia szybsze działanie systemu AI-inferencing i ma wszystkie możliwości bramy magazynu sieciowego.

Azure Data Box Edge jest oznaczany jako Azure Stack Edge.

## <a name="use-cases"></a>Przypadki zastosowań

Poniżej przedstawiono różne scenariusze, w których Azure Stack EDGE Pro można używać do szybkiego Machine Learning (ML) inferencing na granicy i przetwarzania wstępnego danych przed wysłaniem ich do platformy Azure.

- **Wnioskowanie z Azure Machine Learning** -with Azure Stack EDGE Pro umożliwia uruchamianie modeli ml w celu uzyskania szybkich wyników, na które można wykonywać działania przed wysłaniem danych do chmury. Pełny zestaw danych można opcjonalnie przesłać, aby kontynuować ponowne uczenie i ulepszanie modeli ML. Aby uzyskać więcej informacji na temat korzystania z wydajnych modeli sprzętu Azure ML na urządzeniu Azure Stack EDGE Pro, zobacz [wdrażanie przyspieszanych modeli sprzętu Azure ml na Azure Stack EDGE Pro](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

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

* **Urządzenie fizyczne w Azure Stack EDGE Pro**: serwer montowany w stojaku o rozmiarze 1U dostarczony przez firmę Microsoft, który można skonfigurować do wysyłania danych do platformy Azure.
    
* **Azure Stack krawędź zasobu**: zasób w Azure Portal, który umożliwia zarządzanie urządzeniem z systemem Azure Stack Edge z poziomu interfejsu sieci Web, do którego można uzyskać dostęp z różnych lokalizacji geograficznych. Zasób Azure Stack Edge służy do tworzenia zasobów i zarządzania nimi, zarządzania udziałami oraz wyświetlania urządzeń i alertów oraz zarządzania nimi.
  
   <!--[The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

   Ponieważ Azure Stack EDGE Pro zbliża się do końca okresu istnienia, nie są wypełniane żadne zamówienia dla nowych urządzeń Azure Stack brzegowych Pro. Jeśli jesteś nowym klientem, zalecamy zapoznanie się z tematem korzystanie z urządzeń z systemem Azure Stack EDGE Pro-GPU dla obciążeń. Aby uzyskać więcej informacji, przejdź do pozycji [co to jest Azure Stack EDGE Pro z procesorem GPU](azure-stack-edge-gpu-overview.md). Aby uzyskać informacje na temat określania kolejności Azure Stack EDGE Pro z urządzeniem GPU, przejdź do obszaru [Tworzenie nowego zasobu dla Azure Stack EDGE Pro-GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

   Jeśli jesteś istniejącym klientem, możesz nadal utworzyć nowy zasób Azure Stack Edge w wersji Pro, jeśli zachodzi potrzeba zamienienia lub zresetowania istniejącego urządzenia Azure Stack EDGE Pro. Aby uzyskać instrukcje, przejdź do [pozycji Utwórz zamówienie na potrzeby urządzenia z usługą Azure Stack EDGE Pro](azure-stack-edge-deploy-prep.md#create-new-resource-for-existing-device).

* **Azure Stack Edge — lokalny interfejs użytkownika sieci Web** — Użyj lokalnego interfejsu użytkownika sieci Web, aby uruchomić diagnostykę, zamknąć i ponownie uruchomić urządzenie Azure Stack EDGE Pro, wyświetlić dzienniki kopiowania i skontaktować się z pomoc techniczna firmy Microsoft w celu zarejestrowania żądania obsługi.

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Aby uzyskać informacje na temat korzystania z interfejsu użytkownika opartego na sieci Web, przejdź do strony [sieci Web przy użyciu interfejsu użytkownika w celu administrowania Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostępność w danym regionie

Azure Stack Edge urządzenie fizyczne, zasób platformy Azure i docelowe konto magazynu, do którego są przesyłane dane, nie muszą znajdować się w tym samym regionie.

- **Dostępność zasobów** — Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, zobacz Dostępność [produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack EDGE Pro można także wdrożyć w chmurze Azure Government. Aby uzyskać więcej informacji, zobacz [co to jest Azure Government?](../azure-government/documentation-government-welcome.md).
    
- **Docelowe konta magazynu** — konta magazynu, w których są przechowywane dane, są dostępne we wszystkich regionach platformy Azure. Regiony, w których przechowywane są konta magazynu Azure Stack dane brzegowe Pro, powinny znajdować się w pobliżu lokalizacji urządzenia w celu uzyskania optymalnej wydajności. Duża odległość konta magazynu od urządzenia powoduje długie opóźnienia i mniejszą wydajność.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [wymagania systemowe Azure Stack Edge](azure-stack-edge-system-requirements.md).
- Zapoznaj się z [limitami Azure Stack krawędzi Pro](azure-stack-edge-limits.md).
- Wdróż [Azure Stack EDGE Pro](azure-stack-edge-deploy-prep.md) w Azure Portal.