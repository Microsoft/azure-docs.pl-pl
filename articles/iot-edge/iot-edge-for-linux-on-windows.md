---
title: Co to jest Azure IoT Edge dla systemu Linux w systemie Windows | Microsoft Docs
description: Przegląd modułów IoT Edge systemu Linux na urządzeniach z systemem Windows 10
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 330eaf5c12372347917e9f3a4aeafb6a2088c592
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492578"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Co to jest usługa Azure IoT Edge dla systemu Linux w systemie Windows (wersja zapoznawcza)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge dla systemu Linux w systemie Windows umożliwia uruchamianie kontenerów zwirtualizowanych z systemem Linux obok aplikacji systemu Windows w ramach wdrożeń usługi Windows IoT. Firmy korzystające z usługi Windows IoT w celu zarządzania urządzeniami brzegowymi mogą teraz korzystać z rozwiązań analitycznych natywnych w chmurze utworzonych w systemie Linux.

IoT Edge dla systemu Linux w systemie Windows działa, uruchamiając maszynę wirtualną z systemem Linux na urządzeniu z systemem Windows. Maszyna wirtualna z systemem Linux jest wstępnie zainstalowana z IoT Edge środowiska uruchomieniowego. Wszystkie moduły IoT Edge wdrożone na urządzeniu w ramach maszyny wirtualnej. Tymczasem aplikacje systemu Windows uruchomione na urządzeniu hosta Windows mogą komunikować się z modułami uruchomionymi na maszynie wirtualnej z systemem Linux.

Zacznij [korzystać](how-to-install-iot-edge-on-windows.md) z wersji zapoznawczej dzisiaj.

>[!NOTE]
>Rozważ przeprowadzenie naszej [ankiety produktu](https://aka.ms/AzEFLOW-Registration) , aby pomóc nam w ulepszaniu Azure IoT Edge dla systemu Linux w systemie Windows w oparciu o IoT Edgee i cele. Możesz również użyć tej ankiety, aby zarejestrować się w przyszłości Azure IoT Edge dla systemu Linux w usłudze anonse w systemie Windows.

## <a name="components"></a>Składniki

IoT Edge dla systemu Linux w systemie Windows korzysta z następujących składników, aby umożliwić uruchamianie obciążeń systemu Linux i Windows równolegle między sobą i komunikować się bezproblemowo:

* **Maszyna wirtualna z systemem Linux, na której działa Azure IoT Edge**: maszyna wirtualna z systemem Linux oparta na [Mariner](https://github.com/microsoft/CBL-Mariner) systemu operacyjnego firmy Microsoft w pierwszej kolejności, została skompilowana przy użyciu środowiska uruchomieniowego IoT Edge i sprawdzona jako środowisko obsługiwane przez warstwę 1 dla obciążeń IoT Edge.

* **Centrum administracyjne systemu Windows**: rozszerzenie IoT Edge centrum administracyjnego systemu Windows ułatwia instalację, konfigurację i diagnostykę IoT Edge na maszynie wirtualnej z systemem Linux. Centrum administracyjne systemu Windows może wdrożyć IoT Edge dla systemu Linux w systemie Windows na urządzeniu lokalnym lub połączyć się z urządzeniami docelowymi i zarządzać nimi zdalnie.

* **Microsoft Update**: integracja z usługą Microsoft Update zachowuje składniki środowiska uruchomieniowego systemu Windows, maszynę wirtualną z systemem Mariner Linux i IoT Edge Aktualności.

![System Windows i maszyna wirtualna z systemem Linux są uruchamiane równolegle, podczas gdy centrum administracyjne systemu Windows kontroluje oba składniki](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Dwukierunkowa komunikacja między procesem systemu Windows a maszyną wirtualną z systemem Linux oznacza, że procesy systemu Windows mogą udostępniać interfejsy użytkownika lub sprzętowe serwery proxy dla obciążeń uruchomionych w kontenerach systemu Linux.

## <a name="samples"></a>Samples

IoT Edge dla systemu Linux w systemie Windows podkreśla współdziałanie między składnikami systemu Linux i Windows.

Przykłady, które demonstrują komunikację między aplikacjami systemu Windows i modułami IoT Edge, można znaleźć w artykule [przykłady IoT systemu Windows 10](https://github.com/microsoft/Windows-IoT-Samples).

## <a name="public-preview"></a>Publiczna wersja zapoznawcza

IoT Edge dla systemu Linux w systemie Windows jest obecnie w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Procesy instalacji i zarządzania mogą być inne niż w przypadku ogólnie dostępnych funkcji.

## <a name="support"></a>Pomoc techniczna

Skorzystaj z pomocy technicznej IoT Edge i kanałów opinii, aby uzyskać pomoc dotyczącą IoT Edge dla systemu Linux w systemie Windows.

**Błędy raportowania** — usterki można zgłaszać na [stronie problemy](https://github.com/azure/iotedge/issues) projektu typu "open source" IoT Edge. Usterki dotyczące Azure IoT Edge dla systemu Linux w systemie Windows można zgłosić na [stronie problemy z iotedge-eFlow](https://github.com/azure/iotedge-eflow/issues).

**Zespół pomocy technicznej firmy Microsoft** — użytkownicy z [planem pomocy](https://azure.microsoft.com/support/plans/) technicznej mogą skontaktować się z zespołem pomocy technicznej firmy Microsoft, tworząc bilet pomocy technicznej bezpośrednio z [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Żądania funkcji** — produkt Azure IoT Edge śledzi żądania funkcji za pośrednictwem [strony użytkownika](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="next-steps"></a>Następne kroki

Obejrzyj [IoT Edge dla systemu Linux w systemie Windows 10 IoT Enterprise](https://aka.ms/EFLOWPPC9) , aby uzyskać więcej informacji oraz przykładową akcję.

Wykonaj kroki opisane w temacie [Instalowanie i udostępnianie Azure IoT Edge dla systemu Linux na urządzeniu z systemem Windows](how-to-install-iot-edge-on-windows.md) , aby skonfigurować urządzenie z IoT Edge dla systemu Linux w systemie Windows.
