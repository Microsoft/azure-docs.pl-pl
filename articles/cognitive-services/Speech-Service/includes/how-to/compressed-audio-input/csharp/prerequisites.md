---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421736"
---
Obsługa skompresowanego dźwięku jest zaimplementowana za pomocą [GStreamer](https://gstreamer.freedesktop.org). Ze względów licencyjnych pliki binarne GStreamer nie są kompilowane i połączone z zestawem SDK mowy. Deweloperzy muszą zainstalować kilka zależności i wtyczek, zobacz [Instalowanie w systemie Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Pliki binarne Gstreamer muszą znajdować się w ścieżce systemowej, aby sdk mowy można załadować pliki binarne gstreamer w czasie wykonywania. Jeśli sdk mowy jest w stanie znaleźć libgstreamer-1.0-0.dll w czasie wykonywania oznacza, że pliki binarne gstreamer znajdują się w ścieżce systemowej.

