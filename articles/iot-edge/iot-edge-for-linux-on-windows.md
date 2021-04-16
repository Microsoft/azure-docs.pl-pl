---
title: Co to jest Azure IoT Edge dla systemu Linux w systemie Windows | Microsoft Docs
description: Omówienie uruchamiania modułów programu Linux IoT Edge na Windows 10 urządzeniach
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 3c7fd6c842d465dd5af5257628044666f10f2ece
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538209"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Co to jest usługa Azure IoT Edge dla systemu Linux w systemie Windows (wersja zapoznawcza)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge dla systemu Linux w systemie Windows umożliwia uruchamianie konteneryzowanych obciążeń systemu Linux obok aplikacji systemu Windows we wdrożeniach IoT systemu Windows. Firmy, które korzystają z systemu Windows IoT do zasilania swoich urządzeń brzegowych, mogą teraz korzystać z rozwiązań analitycznych natywnych dla chmury, które są wbudowane w system Linux.

IoT Edge dla systemu Linux w systemie Windows działa, uruchamiając maszynę wirtualną z systemem Linux na urządzeniu z systemem Windows. Maszyna wirtualna z systemem Linux jest wstępnie zainstalowana ze IoT Edge uruchomieniowym. Wszystkie IoT Edge wdrożone na urządzeniu są uruchamiane na maszynie wirtualnej. W międzyczasie aplikacje systemu Windows uruchomione na urządzeniu hosta z systemem Windows mogą komunikować się z modułami uruchomionymi na maszynie wirtualnej z systemem Linux.

[Już dziś możesz](how-to-install-iot-edge-on-windows.md) rozpocząć pracę z podglądem.

>[!NOTE]
>Rozważ udział w naszej [ankiecie dotyczącej](https://aka.ms/AzEFLOW-Registration) produktu, aby pomóc nam ulepszyć Azure IoT Edge dla systemu Linux w systemie Windows na podstawie IoT Edge i celów. Możesz również użyć tej ankiety, aby zarejestrować się w celu korzystania z przyszłych Azure IoT Edge dla systemu Linux w systemie Windows.

## <a name="components"></a>Składniki

IoT Edge dla systemu Linux w systemie Windows używa następujących składników, aby umożliwić uruchamianie ze sobą obciążeń systemów Linux i Windows oraz bezproblemową komunikację:

* Maszyna wirtualna z systemem Linux z systemem **Azure IoT Edge:** maszyna wirtualna z systemem Linux oparta na pierwszym firmowym systemie operacyjnym [Rozwiązaniar](https://github.com/microsoft/CBL-Mariner) firmy Microsoft została zbudowana przy użyciu środowiska uruchomieniowego usługi IoT Edge i zweryfikowana jako środowisko obsługiwane przez warstwę 1 dla IoT Edge operacyjnych.

* **Windows Admin Center:** Rozszerzenie IoT Edge dla usługi Windows Admin Center ułatwia instalację, konfigurację i diagnostykę IoT Edge na maszynie wirtualnej z systemem Linux. Windows Admin Center wdrożyć IoT Edge dla systemu Linux w systemie Windows na urządzeniu lokalnym lub połączyć się z urządzeniami docelowymi i zarządzać nimi zdalnie.

* **Microsoft Update:** integracja z Microsoft Update zapewnia, że składniki środowiska uruchomieniowego systemu Windows, maszyna wirtualna z systemem Linux i IoT Edge są aktualne.

![System Windows i maszyna wirtualna z systemem Linux działają równolegle, podczas gdy Windows Admin Center steruje obydwómi składnikami](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Dwukierunkowa komunikacja między procesem systemu Windows a maszyną wirtualną z systemem Linux oznacza, że procesy systemu Windows mogą dostarczać interfejsy użytkownika lub sprzętowe serwery proxy dla obciążeń uruchamianych w kontenerach systemu Linux.

## <a name="samples"></a>Samples

IoT Edge dla systemu Linux w systemie Windows kładzie nacisk na współdziałanie składników systemów Linux i Windows.

Przykłady demonstrują komunikację między aplikacjami systemu Windows i modułami IoT Edge, zobacz [Przykłady EFLOW & Windows 10 IoT](https://aka.ms/AzEFLOW-Samples).

## <a name="public-preview"></a>Publiczna wersja zapoznawcza

IoT Edge dla systemu Linux w systemie Windows jest obecnie w publicznej [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Procesy instalacji i zarządzania mogą się różnić od ogólnie dostępnych funkcji.

## <a name="support"></a>Pomoc techniczna

Skorzystaj z kanałów IoT Edge pomocy technicznej i opinii, aby uzyskać pomoc IoT Edge dla systemu Linux w systemie Windows.

**Zgłaszanie usterek** — usterki można zgłaszać na [stronie](https://github.com/azure/iotedge/issues) problemów IoT Edge projektu open source. Usterki związane Azure IoT Edge dla systemu Linux w systemie Windows można zgłaszać na [stronie problemów iotedge-eflow](https://aka.ms/AzEFLOW-Issues).

**Zespół pomocy technicznej firmy Microsoft** — użytkownicy, którzy mają [plan](https://azure.microsoft.com/support/plans/) pomocy technicznej, mogą angażować zespół pomocy technicznej firmy Microsoft, tworząc bilet pomocy technicznej bezpośrednio z Azure Portal [.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)

**Żądania funkcji** — Azure IoT Edge śledzi żądania funkcji za pośrednictwem strony [User Voice produktu.](https://feedback.azure.com/forums/907045-azure-iot-edge)

## <a name="next-steps"></a>Następne kroki

Zobacz [IoT Edge dla systemu Linux w Windows 10 IoT Enterprise,](https://aka.ms/EFLOWPPC9) aby uzyskać więcej informacji i przykład w działaniu.

Wykonaj kroki opisane w te [tematu Instalowanie](how-to-install-iot-edge-on-windows.md) i aprowiz Azure IoT Edge dla systemu Linux na urządzeniu z systemem Windows, aby skonfigurować urządzenie z systemem IoT Edge dla systemu Linux w systemie Windows.
