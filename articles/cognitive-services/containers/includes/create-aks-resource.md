---
title: Tworzenie zasobu klastra usługi Azure Kubernetes Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób usługi Azure Kubernetes Service (AKS).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e7f5b6f3685a94b5497784360f8f12b22fb95012
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87298834"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Tworzenie zasobu klastra usługi Azure Kubernetes Service

1. Przejdź do [usługi Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)i wybierz pozycję **Utwórz**.

1. Na karcie **Podstawowe** wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |--|--|
    |Subskrypcja|Wybierz odpowiednią subskrypcję.|
    |Grupa zasobów|Wybierz dostępną grupę zasobów.|
    |Nazwa klastra Kubernetes|Wprowadź nazwę (małe litery).|
    |Region|Wybierz lokalizację w pobliżu.|
    |Wersja Kubernetes|Każda wartość jest oznaczona jako **(domyślnie)**.|
    |Prefiks nazwy DNS|Tworzony automatycznie, ale można przesłonić.|
    |Rozmiar węzła|Standardowa DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Liczba węzłów|Pozostaw suwak pod wartością domyślną.|

1. Na karcie **Pule węzłów** pozostaw **węzły wirtualne** i **zestawy skalowania maszyn** wirtualnych ustawione na ich wartości domyślne.
1. Na karcie **uwierzytelnianie** pozostaw pozycję Nazwa **główna usługi** i **Włącz opcję RBAC** ustawioną jako wartości domyślne.
1. Na karcie **Sieć** wprowadź następujące opcje:

    |Ustawienie|Wartość|
    |--|--|
    |Routing aplikacji protokołu HTTP|Nie|
    |Konfiguracja sieci|Podstawowy|

1. Na karcie **integracje** upewnij się, że w obszarze **monitorowanie kontenera** jest ustawiona wartość **włączone**, a następnie pozostaw **log Analyticsą przestrzeń roboczą** .
1. Na karcie **Tagi** pozostaw teraz puste pary nazwa/wartość.
1. Wybierz pozycję **Przejrzyj i Utwórz**.
1. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

> [!NOTE]
> Jeśli walidacja nie powiedzie się, może to być spowodowane błędem "Nazwa główna usługi". Wróć do karty **uwierzytelnianie** , a następnie wróć do pozycji **Przegląd + Utwórz**, gdzie Walidacja powinna zostać uruchomiona, a następnie zastąp.
