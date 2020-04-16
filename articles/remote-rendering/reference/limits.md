---
title: Ograniczenia
description: Ograniczenia kodu dla funkcji SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417695"
---
# <a name="limitations"></a>Ograniczenia

Wiele funkcji ma rozmiar, liczbę lub inne ograniczenia.

## <a name="azure-frontend"></a>Azure Frontend

* Całkowita azurefrontend wystąpień na proces: 16.
* Całkowita usługa AzureSession wystąpień na AzureFrontend: 16.

## <a name="objects"></a>Obiekty

* Całkowita dopuszczalna liczba obiektów pojedynczego typu (Entity, CutPlaneComponent itp.): 16 777 215.
* Całkowita dopuszczalna aktywna płaszczyzna cięcia: 8.

## <a name="materials"></a>Materiały

* Łączna wartość dopuszczalnych materiałów w aktach trwałych: 65 535.

## <a name="overall-number-of-polygons"></a>Ogólna liczba wielokątów

Dopuszczalna liczba wielokątów dla wszystkich załadowanych modeli zależy od rozmiaru maszyny Wirtualnej przekazywanej do [interfejsu API REST zarządzania sesją:](../how-tos/session-rest-api.md#create-a-session)

| Rozmiar maszyny wirtualnej | Maksymalna liczba wielokątów |
|:--------|:------------------|
|Standardowych| 20 milionów |
|Premium| bez limitu |


## <a name="platform-limitations"></a>Ograniczenia platformy

**Pulpit systemu Windows 10**

* Wdrożenie "PC Standalone" z Unity nie jest obsługiwane. Zamiast tego należy użyć platformy uniwersalnej systemu wyurzcie.Use UWP instead.
* Platforma uniwersalna systemu Windows/x86 jest jedyną obsługiwana platformą platformy platformy uniwersalnej systemu Windows. Platforma uniwersalna systemupos m.in.

**Okręg wyborczy Hololens 2**

* [Funkcja renderowania z kamery fotowoltaiczowej](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) nie jest obsługiwana.
