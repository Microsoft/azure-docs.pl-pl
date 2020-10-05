---
title: Szybki Start — Konfigurowanie śledzenia treści usługi Azure urządzenia Kinect
description: W tym przewodniku szybki start utworzysz zestaw SDK śledzenia treści dla usługi Azure urządzenia Kinect.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: urządzenia Kinect, Azure, czujnik, dostęp, Głębokość, zestaw SDK, treść, śledzenie, wspólne, konfiguracja, cuda, NVIDIA
ms.openlocfilehash: 2cf4c1097730f88fc4bd66c28e1bdddd7fea8640
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277983"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>Szybki Start: Konfigurowanie śledzenia treści usługi Azure urządzenia Kinect

Ten przewodnik Szybki Start przeprowadzi Cię przez proces pobierania śledzenia treści działającej na platformie Azure urządzenia Kinect DK.

## <a name="system-requirements"></a>Wymagania systemowe

Zestaw SDK śledzenia treści wymaga zainstalowanego procesora graficznego NVIDIA na komputerze-hoście. Zalecane wymagania dotyczące komputera hosta śledzenia treści są opisane na stronie [wymagania systemowe](system-requirements.md) .

## <a name="install-software"></a>Zainstalowanie oprogramowania

### <a name="install-the-latest-nvidia-driver"></a>[Zainstaluj najnowszy sterownik NVIDIA](https://www.nvidia.com/Download/index.aspx?lang=en-us)

Pobierz i zainstaluj najnowszą wersję sterownika NVIDIA dla karty graficznej. Starsze sterowniki mogą nie być zgodne z plikami binarnymi CUDA rozpowszechnianymi za pomocą zestawu SDK śledzenia treści.

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[Pakiet redystrybucyjny języka Visual C++ dla programu Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

Pobierz i Zainstaluj Pakiet redystrybucyjny Visual C++ dla Visual Studio 2015. 

## <a name="set-up-hardware"></a>Konfigurowanie sprzętu

### <a name="set-up-azure-kinect-dk"></a>[Konfigurowanie zestawu Azure Kinect DK](set-up-azure-kinect-dk.md)

Uruchom [Podgląd usługi Azure urządzenia Kinect](azure-kinect-viewer.md) , aby sprawdzić, czy usługa Azure urządzenia Kinect DK została prawidłowo skonfigurowana.

## <a name="download-the-body-tracking-sdk"></a>Pobieranie zestawu Body Tracking SDK
 
1. Wybierz link, aby [pobrać zestaw SDK śledzenia treści](body-sdk-download.md)
2. Zainstaluj zestaw SDK śledzenia treści na komputerze.

## <a name="verify-body-tracking"></a>Weryfikuj śledzenie treści

Uruchom **Podgląd śledzenia treści usługi Azure urządzenia Kinect** , aby sprawdzić, czy zestaw SDK śledzenia treści został prawidłowo skonfigurowany. Przeglądarka jest instalowana z instalatorem Instalatora MSI SDK. Można go znaleźć w menu Start lub w `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe` .

Jeśli nie masz wystarczającej ilości procesora GPU i nadal chcesz przetestować wynik, możesz uruchomić **Podgląd śledzenia treści usługi Azure urządzenia Kinect** w wierszu polecenia za pomocą następującego polecenia: `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

Jeśli wszystko jest prawidłowo skonfigurowane, powinno zostać wyświetlone okno z chmurą punktu 3W i śledzonymi treściami.


![Śledzenie treści — przeglądarka 3D](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>Przykłady

Przykłady dotyczące korzystania z zestawu SDK śledzenia treści można znaleźć [tutaj](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Tworzenie pierwszej aplikacji śledzącej ciało](build-first-body-app.md)

