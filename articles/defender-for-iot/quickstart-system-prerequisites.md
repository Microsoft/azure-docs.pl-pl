---
title: Wymagania wstępne systemu
description: Pobierz wymagania wstępne systemu wymagane do uruchomienia usługi Azure Defender dla IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 4b5db049e6d1cfe76bdd0d5cd6d7360e0b98bad0
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489815"
---
# <a name="system-prerequisites"></a>Wymagania wstępne systemu
W tym artykule wymieniono wymagania wstępne dotyczące uruchamiania usługi Azure Defender for IoT.

## <a name="minimum-requirements"></a>Minimalne wymagania

- Przełączniki sieciowe obsługujące monitorowanie ruchu przez port OBEJMUJĄcy.
- Urządzenia sprzętowe dla czujników NTA.
- Rola współautora subskrypcji platformy Azure. Jest to wymagane tylko podczas dołączania w celu zdefiniowania zatwierdzonych urządzeń i połączenia z platformą Azure.
- Rola **współautor** usługi Azure IoT Hub (warstwa Bezpłatna lub standardowa) dla zarządzania połączonego z chmurą. Upewnij się, że funkcja **Azure Defender for IoT** jest włączona.
- W przypadku pomocy technicznej usługi Defender-IoT-Micro-Agent usługa Defender for IoT obsługuje coraz większą listę urządzeń i platform. Zapoznaj się z [listą obsługiwanych platform](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Obsługiwane regiony usługi

Usługa Defender for IoT kieruje cały ruch ze wszystkich regionów Europy do regionalnego centrum danych Europa Zachodnia. Kieruje ruch ze wszystkich pozostałych regionów do centralnie regionalnych centrów USA.

Aby uzyskać więcej informacji, zobacz [IoT Hub obsługiwanych regionów](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="see-also"></a>Zobacz też

- [Identyfikowanie wymaganych urządzeń](how-to-identify-required-appliances.md)
- [Informacje o usłudze Azure Defender dla sieci IoT — konfiguracja](how-to-set-up-your-network.md)
