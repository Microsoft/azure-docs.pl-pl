---
title: Przegląd Microsoft Azure Stack EDGE Pro R | Microsoft Docs
description: W tym artykule opisano Azure Stacke urządzenia brzegowe Pro R, rozwiązanie magazynu dla aplikacji wojskowych korzystających z urządzenia fizycznego na potrzeby transferu sieciowego na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 40fa1bc776c5cd457e57cf170f629b6cf92800fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585999"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>Co to jest Azure Stack EDGE Pro R?

Azure Stack Edge w wersji Pro R to rozwiązanie, które jest przeznaczone do użycia w środowiskach Harsh. Azure Stack EDGE Pro R jest dostarczany jako rozwiązanie typu "sprzęt jako usługa". Firma Microsoft dostarcza urządzenie zarządzane przez chmurę, które działa jako brama magazynu sieciowego i ma wbudowaną jednostkę przetwarzania graficznego (GPU), która umożliwia przyspieszenie inferencing.

Ten artykuł zawiera omówienie rozwiązania Azure Stack Edge w wersji R, kluczowych możliwości i scenariuszy, w których można wdrożyć to urządzenie.


## <a name="key-capabilities"></a>Najważniejsze możliwości

Azure Stack EDGE Pro R ma następujące możliwości:

|Możliwość |Opis  |
|---------|---------|
|Sprzęt wzporny| Sprzęt klasy serwerów z założeniami przeznaczony dla środowisk Harsh. Urządzenie zawarte w przenośnym przypadku tranzytowym. |
|Zarządzane przez chmurę     |Urządzenia i usługi są zarządzane za pośrednictwem Azure Portal.|
|Obciążenia obliczeń brzegowych   |Umożliwia analizowanie, przetwarzanie i filtrowanie danych. Obsługuje maszyny wirtualne i obciążenia kontenerów.|
|Przyspieszone inferencing AI| Włączony przez procesor GPU T4.|
|Dostęp do danych     | Bezpośredni dostęp do danych z usług Azure Storage Blob i Azure Files przy użyciu interfejsów API w chmurze w celu dodatkowego przetwarzania danych w chmurze. Lokalna pamięć podręczna na urządzeniu służy do szybkiego dostępu do ostatnio używanych plików.|
|Tryb rozłączenia| Można opcjonalnie zarządzać urządzeniami i usługami za pośrednictwem Centrum Azure Stack. Wdrażaj, uruchamiaj i Zarządzaj aplikacjami w trybie offline. <br> Tryb bez połączenia obsługuje scenariusze przekazywania w trybie offline.|
|Obsługiwane protokoły transferu plików     |Obsługa standardowych protokołów SMB, NFS i REST na potrzeby pozyskiwania danych. <br> Aby uzyskać więcej informacji na temat obsługiwanych wersji, przejdź do pozycji [Azure Stack Edge system wymagania systemowe](azure-stack-edge-gpu-system-requirements.md).|
|Odświeżanie danych     | Możliwość odświeżania plików lokalnych za pomocą najnowszych wersji z chmury.|
|Podwójne szyfrowanie    | Użycie dysków z szyfrowaniem własnym zapewnia pierwszą warstwę szyfrowania. Sieć VPN zapewnia drugą warstwę szyfrowania. Obsługa funkcji BitLocker do lokalnego szyfrowania danych i bezpiecznego transferu danych do chmury za pośrednictwem *protokołu HTTPS* .|
|Ograniczanie przepustowości| Ograniczanie użycia przepustowości w godzinach szczytu.|

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>Przypadki zastosowań

Poniżej przedstawiono różne scenariusze, w których Azure Stack EDGE Pro R może służyć do szybkiego Machine Learning (ML) inferencing na granicy i przetwarzania wstępnego danych przed wysłaniem ich do platformy Azure.

- **Wnioskowanie z Azure Machine Learning** -with Azure Stack EDGE Pro R umożliwia uruchamianie modeli ml w celu uzyskania szybkich wyników, na które można wykonywać działania przed wysłaniem danych do chmury. Pełny zestaw danych można opcjonalnie przesłać, aby kontynuować ponowne uczenie i ulepszanie modeli ML. Aby uzyskać więcej informacji na temat korzystania z wydajnych modeli sprzętowych platformy Azure na urządzeniu z systemem Azure Stack EDGE Pro R, zobacz [wdrażanie przyspieszone modele sprzętu Azure ml w Azure Stack EDGE Pro R](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- Wstępnie **Przetwarzaj** dane przekształceń danych przed wysłaniem ich do platformy Azure w celu utworzenia bardziej funkcjonalnego zestawu danych. Wstępne przetwarzanie umożliwia:

    - Agregowanie danych.
    - Zmodyfikuj dane, na przykład aby usunąć dane osobowe.
    - Podzbiór danych w celu zoptymalizowania magazynu i przepustowości lub do dalszej analizy.
    - Analizowanie zdarzeń IoT i reagowanie na nie.

- **Transferowanie danych za pośrednictwem sieci na platformę Azure** — Użyj Azure Stack EDGE Pro R, aby łatwo i szybko przetransferować dane na platformę Azure w celu umożliwienia dalszych obliczeń i analiz lub w celach archiwalnych.

## <a name="components"></a>Składniki

Rozwiązanie do Azure Stack EDGE Pro R obejmuje Azure Stack zasobów brzegowych, Azure Stack Edge w programie, urządzenie fizyczne i lokalny interfejs użytkownika sieci Web.

- **Azure Stack Edge urządzenie fizyczne Pro R** -a 1-węzłowe urządzenie obliczeniowe i magazynowe zawarte w przypadku nieograniczonego przypadku tranzytu. Dostępny jest również opcjonalny zasilacz awaryjny (UPS).

    ![Urządzenie Azure Stack Edge R 1 — węzeł](media/azure-stack-edge-pro-r-overview/device-image-1.png)

- **Azure Stack Edge** — zasób w Azure Portal, który umożliwia zarządzanie niewytrzymałym, Azure Stackm urządzeniem Pro R z poziomu interfejsu sieci Web, do którego można uzyskać dostęp z różnych lokalizacji geograficznych. Użyj zasobu brzegowego Azure Stack, aby tworzyć i zarządzać zasobami, wyświetlać i zarządzać urządzeniami oraz alertami oraz zarządzać udziałami.  

- **Azure Stack Edge — lokalny interfejs użytkownika sieci Web** w przeglądarce, na podstawie usługi Azure Stack EDGE Pro na urządzeniu, które jest przeznaczone głównie do wstępnej konfiguracji urządzenia. Użyj lokalnego interfejsu użytkownika sieci Web, aby uruchomić diagnostykę, Zamknij i uruchom ponownie urządzenie Azure Stack EDGE Pro, wyświetl dzienniki kopiowania i skontaktuj się z pomoc techniczna firmy Microsoft, aby wysłać żądanie obsługi.


## <a name="region-availability"></a>Dostępność w danym regionie

Azure Stack Edge urządzenie fizyczne Pro R, zasób platformy Azure i docelowe konto magazynu, do którego są przesyłane dane, nie muszą znajdować się w tym samym regionie.

- **Dostępność zasobów** — Aby uzyskać listę wszystkich regionów, w których jest dostępny zasób Azure Stack Edge, przejdź do pozycji [dostępne produkty platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Dostępność urządzenia** — aby zapoznać się z listą wszystkich krajów, w których dostępne jest urządzenie Azure Stack EDGE Pro r, przejdź do sekcji **dostępność** na karcie **Azure Stack EDGE Pro r** dla [Azure Stack cennika usługi Pro r](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeProR).

- **Docelowe konta magazynu** — konta magazynu, w których są przechowywane dane, są dostępne we wszystkich regionach platformy Azure. Regiony, w których przechowywane są konta magazynu Azure Stack dane brzegowe Pro R powinny znajdować się w pobliżu lokalizacji urządzenia w celu uzyskania optymalnej wydajności. Duża odległość konta magazynu od urządzenia powoduje długie opóźnienia i mniejszą wydajność.

Usługa Edge Azure Stack jest usługą nieregionalną. Aby uzyskać więcej informacji, zobacz [regiony i strefy dostępności na platformie Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Usługa Edge Azure Stack nie jest zależna od określonego regionu platformy Azure, dzięki czemu można odporna na przerwy w działaniu strefy i w całym regionie.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [wymagania systemowe Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md).
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->
