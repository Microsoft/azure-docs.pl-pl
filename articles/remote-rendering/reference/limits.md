---
title: Ograniczenia
description: Ograniczenia dotyczące kodu dla funkcji zestawu SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6a1a51ee09422607ae1392704add4d49d3367d57
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759051"
---
# <a name="limitations"></a>Ograniczenia

Niektóre funkcje mają rozmiar, liczbę lub inne ograniczenia.

## <a name="azure-frontend"></a>Fronton platformy Azure

* Łączna liczba wystąpień AzureFrontend na proces: 16.
* Łączna liczba wystąpień AzureSession na AzureFrontend: 16.

## <a name="objects"></a>Obiekty

* Łączna liczba dozwolonych obiektów pojedynczego typu (Entity, CutPlaneComponent itp.): 16 777 215.
* Łączna liczba aktywnych płaszczyzn wycinania: 8.

## <a name="materials"></a>Materiały

* Łączna liczba dozwolonych materiałów w elemencie zawartości: 65 535.

## <a name="overall-number-of-polygons"></a>Ogólna liczba wielokątów

Dozwolona liczba wielokątów dla wszystkich załadowanych modeli zależy od rozmiaru maszyny wirtualnej, która została przeniesiona do [interfejsu API REST zarządzania sesją](../how-tos/session-rest-api.md#create-a-session):

| Rozmiar maszyny wirtualnej | Maksymalna liczba wielokątów |
|:--------|:------------------|
|Standardowa| 20 000 000 |
|tytułu| bez limitu |


## <a name="platform-limitations"></a>Ograniczenia dotyczące platformy

**Windows 10 Desktop**

* PLATFORMY UWP/x86 jest jedyną obsługiwaną platformą platformy UWP. PLATFORMY UWP/x64 nie jest obsługiwane.

**Urządzenie HoloLens 2**

* Funkcja [renderowania z aparatu PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) nie jest obsługiwana.
