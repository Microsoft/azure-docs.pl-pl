---
title: Dodawanie biblioteki usługi Azure urządzenia Kinect do projektu programu Visual Studio
description: Dowiedz się, jak dodać pakiet NuGet usługi Azure urządzenia Kinect do projektu programu Visual Studio.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, Visual Studio 2017, Visual Studio 2019, NuGet
ms.openlocfilehash: fd71f0d327b8c828cc9ddac5810757cccdffbcea
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359616"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Dodawanie biblioteki usługi Azure urządzenia Kinect do projektu programu Visual Studio

W tym artykule omówiono proces dodawania pakietu NuGet usługi Azure urządzenia Kinect do projektu programu Visual Studio.

## <a name="install-azure-kinect-nuget-package"></a>Zainstaluj pakiet NuGet usługi Azure urządzenia Kinect

Aby zainstalować pakiet NuGet usługi Azure urządzenia Kinect:

1. Szczegółowe instrukcje dotyczące instalowania pakietu NuGet w programie Visual Studio można znaleźć w [przewodniku szybki start: Instalowanie i używanie pakietu w programie Visual Studio](/nuget/quickstart/install-and-use-a-package-in-visual-studio).
2. Aby dodać pakiet, można użyć interfejsu użytkownika Menedżera pakietów, klikając prawym przyciskiem myszy pozycję odwołania i wybierając pozycję Zarządzaj pakietami NuGet z Eksplorator rozwiązań.
3. Wybierz [NuGet.org](https://www.nuget.org) jako źródło pakietu, wybierz kartę Przeglądaj i Wyszukaj `Microsoft.Azure.Kinect.Sensor` .
4. Wybierz ten pakiet z listy i zainstaluj.

## <a name="use-azure-kinect-nuget-package"></a>Użyj pakietu NuGet platformy Azure urządzenia Kinect

Po dodaniu pakietu Dodaj plik nagłówka zawiera kod źródłowy, taki jak:

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Teraz możesz przystąpić do kompilowania pierwszej aplikacji](build-first-app.md)