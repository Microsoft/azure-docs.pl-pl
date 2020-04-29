---
title: Konfigurowanie protokołów interfejsu API — Azure Event Grid IoT Edge | Microsoft Docs
description: Skonfiguruj protokoły interfejsu API udostępniane przez Event Grid w IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841814"
---
# <a name="configure-event-grid-api-protocols"></a>Konfigurowanie protokołów interfejsu API Event Grid

W tym przewodniku przedstawiono przykłady możliwych konfiguracji protokołów modułu Event Grid. Moduł Event Grid udostępnia interfejs API dla jego operacji zarządzania i środowiska uruchomieniowego. W poniższej tabeli przedstawiono protokoły i porty.

| Protocol (Protokół) | Port | Opis |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Domyślnie wyłączona. Przydatne tylko podczas testowania. Nieodpowiednie dla obciążeń produkcyjnych.
| HTTPS | 4438 | Domyślny

Wszystkie możliwe konfiguracje można znaleźć w przewodniku dotyczącym [zabezpieczeń i uwierzytelniania](security-authentication.md) .

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Uwidacznianie modułów HTTPS do IoT w tej samej sieci brzegowej

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Włączanie protokołu HTTPS do innych modułów IoT i obciążeń innych niż IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Sekcja **PortBindings** umożliwia mapowanie portów wewnętrznych do portów hosta kontenera. Ta funkcja umożliwia dostęp do modułu Event Grid spoza sieci kontenera IoT Edge, jeśli urządzenie usługi IoT Edge jest dostępne publicznie.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Uwidacznianie modułów HTTP i HTTPS w tej samej sieci brzegowej

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Włączanie protokołów HTTP i HTTPS do innych modułów IoT i obciążeń innych niż IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Domyślnie każdy moduł IoT jest częścią środowiska uruchomieniowego IoT Edge utworzonego przez sieć mostka. Umożliwia ona różne moduły IoT w tej samej sieci, aby komunikować się ze sobą. **PortBindings** umożliwia mapowanie wewnętrznego portu kontenera na maszynę hosta, co umożliwia wszystkim użytkownikom dostęp do portu modułu Event Grid z zewnątrz.

>[!IMPORTANT]
> Porty mogą być dostępne poza siecią IoT Edge, więc uwierzytelnianie klienta wymusza, kto rzeczywiście zezwala na wykonywanie wywołań do modułu.
