---
title: Azure Stack graniczny R — Omówienie Microsoft Docs
description: Opisuje Azure Stack Edge mini R, rozwiązanie magazynu dla aplikacji wojskowych korzystających z przenośnego urządzenia fizycznego z baterią do transferu za pośrednictwem sieci Wi-Fi na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: 7c28eb604de4f6e23f37b6ca2fcab3fdc976f237
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125206"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>Co to jest Azure Stack Edge — mini R?

Azure Stack Edge mini R to niezwykle przenośne, wzmocnione i brzegowe urządzenie obliczeniowe przeznaczone do użycia w środowiskach Harsh. Azure Stack Edge mini R jest dostarczany jako rozwiązanie typu "sprzęt jako usługa". Firma Microsoft dostarcza urządzenie zarządzane przez chmurę, które działa jako brama magazynu sieciowego i ma wbudowaną jednostkę przetwarzania wizji (VPU), która umożliwia przyspieszenie AI-inferencing.

Ten artykuł zawiera omówienie rozwiązania typu mini R w Azure Stack Edge, kluczowych możliwości i scenariuszy, w których można wdrożyć to urządzenie.


## <a name="key-capabilities"></a>Najważniejsze możliwości

Azure Stack Edge mini R ma następujące możliwości:

|Możliwość |Opis  |
|---------|---------|
|Sprzęt wzporny| Sprzęt z urządzeniami nieprzeznaczonymi do środowiska Harsh.|
|Ultra Portable| Ultra przenośny i oparty na bateriach współczynnik formy.|
|Zarządzane przez chmurę|Urządzenia i usługi są zarządzane za pośrednictwem Azure Portal.|
|Obciążenia obliczeń brzegowych|Umożliwia analizowanie, przetwarzanie i filtrowanie danych.<br>Obsługuje maszyny wirtualne i obciążenia kontenerów. |
|Przyspieszone inferencing AI| Włączone przez procesor Intel Movidius wyposażono X VPU.|
|Przewodowe i bezprzewodowe | Umożliwia używanie przewodowych i bezprzewodowych transferów danych.|
|Dostęp do danych     | Bezpośredni dostęp do danych z usług Azure Storage Blob i Azure Files przy użyciu interfejsów API w chmurze w celu dodatkowego przetwarzania danych w chmurze. Lokalna pamięć podręczna na urządzeniu służy do szybkiego dostępu do ostatnio używanych plików.|
|Tryb rozłączenia|  Można opcjonalnie zarządzać urządzeniami i usługami za pośrednictwem Centrum Azure Stack. Wdrażaj, uruchamiaj i Zarządzaj aplikacjami w trybie offline. <br> Tryb bez połączenia obsługuje scenariusze przekazywania w trybie offline.|
|Obsługiwane protokoły transferu plików      |Obsługa standardowych protokołów SMB, NFS i REST na potrzeby pozyskiwania danych. <br> Aby uzyskać więcej informacji na temat obsługiwanych wersji, przejdź do pozycji [Azure Stack Edge mini R System Requirements](azure-stack-edge-gpu-system-requirements.md).|
|Odświeżanie danych     | Możliwość odświeżania plików lokalnych za pomocą najnowszych wersji z chmury.|
|Podwójne szyfrowanie    | Użycie dysku samoszyfrującego zapewnia pierwszą warstwę szyfrowania. Sieć VPN zapewnia drugą warstwę szyfrowania. Obsługa funkcji BitLocker do lokalnego szyfrowania danych i bezpiecznego transferu danych do chmury za pośrednictwem *protokołu HTTPS* .|
|Ograniczanie przepustowości| Ograniczanie użycia przepustowości w godzinach szczytu.|

## <a name="use-cases"></a>Przypadki zastosowań

Poniżej znajdują się różne scenariusze, w których do szybkiej Machine Learning (ML) inferencing na granicy i przetwarzania danych przed wysłaniem ich do platformy Azure można użyć Azure Stack Edge.

- **Wnioskowanie o Azure Machine Learning** — z Azure Stack Edge w systemie, można uruchamiać modele ml, aby uzyskać szybkie wyniki, które mogą być przetwarzane przed wysłaniem danych do chmury. Pełny zestaw danych można opcjonalnie przesłać, aby kontynuować ponowne uczenie i ulepszanie modeli ML. Aby uzyskać więcej informacji na temat korzystania z wydajnych modeli sprzętowych platformy Azure na urządzeniu z systemem Azure Stack Edge mini R, zobacz [wdrażanie przyspieszone modele sprzętu Azure ml w Azure Stack Edge mini r](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- Wstępnie **Przetwarzaj** dane przekształcania danych za pomocą opcji obliczeniowych, takich jak kontenery czy maszyny wirtualne, przed wysłaniem ich do platformy Azure w celu utworzenia bardziej funkcjonalnego zestawu danych. Wstępne przetwarzanie umożliwia:

    - Agregowanie danych.
    - Zmodyfikuj dane, na przykład aby usunąć dane osobowe.
    - Podzbiór danych w celu zoptymalizowania magazynu i przepustowości lub do dalszej analizy.
    - Analizowanie zdarzeń IoT i reagowanie na nie.

- **Transferowanie danych za pośrednictwem sieci do platformy Azure** — Użyj Azure Stack Edge mini R, aby łatwo i szybko przenieść dane na platformę Azure w celu umożliwienia dalszych obliczeń i analiz lub w celach archiwalnych.

## <a name="components"></a>Składniki

Rozwiązanie typu mini R Azure Stack Edge obejmuje Azure Stack zasobów brzegowych, Azure Stack Edge mini R porne, Ultra przenośne urządzenie fizyczne i lokalny interfejs użytkownika sieci Web.

* Urządzenie **fizyczne urządzenia fizycznego R Azure Stack Edge** — niezwykle przenośne, niewzmocnione, obliczeniowe i magazynujące dostarczone przez firmę Microsoft. Urządzenie ma baterię z dołączaniem i waży mniej niż 7 funtów.

    ![Urządzenie w Azure Stack Edge mini R](media/azure-stack-edge-mini-r-overview/perspective-view-1.png)

* **Zasób graniczny Azure Stack** — zasób w Azure Portal, który umożliwia zarządzanie niewytrzymałym urządzeniem z systemem i Azure Stack Edge za pośrednictwem interfejsu sieci Web, do którego można uzyskać dostęp z różnych lokalizacji geograficznych. Użyj zasobu brzegowego Azure Stack, aby tworzyć i zarządzać zasobami, wyświetlać i zarządzać urządzeniami oraz alertami oraz zarządzać udziałami.  

* **Azure Stack Edge** — lokalny interfejs użytkownika sieci Web w przeglądarce — na urządzeniu z systemem Azure Stack Edge mini r w sposób przeznaczony głównie do wstępnej konfiguracji urządzenia. Użyj lokalnego interfejsu użytkownika sieci Web, aby uruchomić diagnostykę, Zamknij i uruchom ponownie urządzenie Azure Stack EDGE Pro, wyświetl dzienniki kopiowania i skontaktuj się z pomoc techniczna firmy Microsoft, aby wysłać żądanie obsługi.


## <a name="region-availability"></a>Dostępność w danym regionie

Azure Stack Edge urządzenie fizyczne, zasób platformy Azure i docelowe konto magazynu, do którego są przesyłane dane, nie muszą znajdować się w tym samym regionie.

- **Dostępność zasobów** — Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, przejdź do pozycji [dostępne produkty platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Dostępność urządzenia** — aby zapoznać się z listą wszystkich krajów, w których jest dostępne urządzenie Azure Stack Edge mini r, przejdź do sekcji dostępność na karcie Azure Stacke mini r karty, aby uzyskać [Cennik usługi Azure Stack Edge](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeMiniR).

- **Docelowe konta magazynu** — konta magazynu, w których są przechowywane dane, są dostępne we wszystkich regionach platformy Azure. Regiony, w których przechowywane są konta magazynu Azure Stack dane programu Edge mini R powinny znajdować się w pobliżu lokalizacji urządzenia w celu uzyskania optymalnej wydajności. Duża odległość konta magazynu od urządzenia powoduje długie opóźnienia i mniejszą wydajność.


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [wymagania systemowe w Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md).