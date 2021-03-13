---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 8c77efe9e3e301573b032d1dc1dd32bb4a5ab1a1
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417734"
---
Obsługa skompresowanego dźwięku jest implementowana przy użyciu [GStreamer](https://gstreamer.freedesktop.org). Ze względów licencjonowania GStreamer pliki binarne nie są kompilowane i połączone z zestawem Speech SDK. Deweloperzy muszą zainstalować kilka zależności i wtyczek, zobacz [Instalowanie w systemie Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) lub [Instalowanie w systemie Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). Pliki binarne GStreamer muszą znajdować się w ścieżce systemowej, aby zestaw mowy SDK mógł ładować pliki binarne podczas środowiska uruchomieniowego. Na przykład w systemie Windows, jeśli zestaw mowy SDK jest w stanie znaleźć `libgstreamer-1.0-0.dll` lub `gstreamer-1.0-0.dll` (dla najnowszej GStreamer) w czasie wykonywania, oznacza to, że pliki binarne GStreamer znajdują się w ścieżce systemowej.

