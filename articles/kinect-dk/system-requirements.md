---
title: Wymagania systemowe zestawu SDK czujnika usługi Azure urządzenia Kinect
description: Zapoznaj się z wymaganiami systemowymi dotyczącymi zestawu Azure urządzenia Kinect sensor SDK w systemach Windows i Linux.
author: qm13
ms.author: quentinm
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: article
keywords: Azure, urządzenia Kinect, wymagania systemowe, procesor CPU, procesor GPU, USB, konfiguracja, instalacja, minimum, wymagania
ms.openlocfilehash: 558c1b9ca264874fa808aeba5f1e8a809a8f722d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656978"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Wymagania systemowe zestawu SDK czujnika usługi Azure urządzenia Kinect

Ten dokument zawiera szczegółowe informacje o wymaganiach systemowych wymaganych do zainstalowania zestawu SDK czujnika i pomyślnym wdrożeniu usługi Azure urządzenia Kinect DK.

## <a name="supported-operating-systems-and-architectures"></a>Obsługiwane systemy operacyjne i architektury

- Windows 10 kwiecień 2018 (wersja 1803, kompilacja systemu operacyjnego 17134) wersja (x64) lub nowsza
- Linux Ubuntu 18,04 (x64), ze sterownikiem procesora GPU używającym OpenGLv 4.4 lub nowszej wersji

Zestaw SDK czujnika jest dostępny dla systemu Windows API (Win32) dla natywnych aplikacji systemu Windows C/C++. Zestaw SDK nie jest obecnie dostępny dla aplikacji platformy UWP. Usługa Azure urządzenia Kinect DK nie jest obsługiwana dla systemu Windows 10 w trybie S.

## <a name="development-environment-requirements"></a>Wymagania dotyczące środowiska deweloperskiego

Aby współtworzyć platformę do tworzenia zestawów SDK, odwiedź witrynę [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK).

## <a name="minimum-host-pc-hardware-requirements"></a>Minimalne wymagania sprzętowe komputera hosta

Wymagania dotyczące sprzętu hosta komputera są zależne od częstotliwości/rozdzielczości ramki/czujnika na komputerze-hoście. Zalecana minimalna konfiguracja zestawu SDK czujnika dla systemu Windows to:

- Siódmy &reg; procesor Intel CoreTM i3 (Dwurdzeniowy 2,4 GHz z procesorem GPU lub szybszy)
- 4 GB pamięci
- Dedykowany port USB3
- Obsługa sterowników grafiki dla OpenGL 4,4 lub DirectX 11,0

Niższe lub starsze procesory CPU mogą również współpracować w zależności od przypadków użycia.

Wydajność różni się również między systemami operacyjnymi Windows/Linux i sterownikami grafiki w użyciu.

## <a name="body-tracking-host-pc-hardware-requirements"></a>Śledzenie treści — wymagania sprzętowe komputera hosta

Wymagania hosta komputera śledzącego treść są bardziej rygorystyczne niż ogólne wymagania hosta komputera. Zalecana konfiguracja zestawu SDK śledzenia treści minimalnej dla systemu Windows to:

- Siódmy &reg; procesor Intel CoreTM i5 (Quad Core 2,4 GHz lub szybszy)
- 4 GB pamięci
- NVIDIA GEFORCE GTX 1050 lub równoważny
- Dedykowany port USB3

Zalecana minimalna konfiguracja zakłada, K4A_DEPTH_MODE_NFOV_UNBINNED tryb głębokości na 30fps śledzenia 5 osób. Niższe lub starsze procesory CPU i NVIDIA GPU mogą również współpracować w zależności od przypadków użycia.

## <a name="usb3"></a>USB3

Istnieją znane problemy ze zgodnością dotyczące kontrolerów hosta USB. Więcej informacji na temat [rozwiązywania problemów](troubleshooting.md#usb3-host-controller-compatibility) można znaleźć na stronie

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure urządzenia Kinect DK](about-azure-kinect-dk.md)

- [Konfigurowanie zestawu Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Skonfiguruj śledzenie treści usługi Azure urządzenia Kinect](body-sdk-setup.md)
