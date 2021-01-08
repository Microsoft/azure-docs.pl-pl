---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 743d7d46474b4ba55df50398f29cbdb964b5ff08
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978773"
---
## <a name="trusted-microsoft-services"></a>Zaufane usługi firmy Microsoft
Po włączeniu ustawienia **Zezwalaj zaufanym usługom firmy Microsoft na pominięcie tej zapory** zostanie udzielony dostęp do zasobów Event Hubs za pomocą następujących usług.

| Usługa zaufana | Obsługiwane scenariusze użycia | 
| --------------- | ------------------------- | 
| Azure Event Grid | Umożliwia Azure Event Grid wysyłanie zdarzeń do centrów zdarzeń w przestrzeni nazw Event Hubs. Należy również wykonać następujące czynności: <ul><li>Włączanie tożsamości przypisanej do systemu dla tematu lub domeny</li><li>Dodawanie tożsamości do roli nadawca danych usługi Azure Event Hubs w przestrzeni nazw Event Hubs</li><li>Następnie należy skonfigurować subskrypcję zdarzeń korzystającą z centrum zdarzeń jako punkt końcowy do korzystania z tożsamości przypisanej do systemu.</li></ul> <p>Aby uzyskać więcej informacji, zobacz [dostarczanie zdarzeń przy użyciu tożsamości zarządzanej](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor (ustawienia diagnostyczne) | Umożliwia Azure Monitor wysyłanie informacji diagnostycznych do centrów zdarzeń w przestrzeni nazw Event Hubs. Azure Monitor można odczytywać z centrum zdarzeń, a także zapisywać dane w centrum zdarzeń. |
| Usługa Azure Stream Analytics | Zezwala na zadanie Azure Stream Analytics odczytywać dane z ([dane wejściowe](../articles/stream-analytics/stream-analytics-add-inputs.md)) lub zapisywać dane do centrów zdarzeń ([output](../articles/stream-analytics/event-hubs-output.md)) w Event Hubs przestrzeni nazw. <p>**Ważne**: zadanie Stream Analytics powinno być skonfigurowane do korzystania z **tożsamości zarządzanej** w celu uzyskania dostępu do centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych do uzyskiwania dostępu do centrum zdarzeń z zadania Azure Stream Analytics (wersja zapoznawcza)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Azure IoT Hub | Umożliwia IoT Hub wysyłanie komunikatów do centrów zdarzeń w przestrzeni nazw centrum zdarzeń. Należy również wykonać następujące czynności: <ul><li>Włączanie tożsamości przypisanej do systemu dla Centrum IoT Hub</li><li>Dodaj tożsamość do roli nadawca danych usługi Azure Event Hubs w przestrzeni nazw Event Hubs.</li><li>Następnie skonfiguruj IoT Hub używające centrum zdarzeń jako niestandardowego punktu końcowego do korzystania z uwierzytelniania opartego na tożsamości.</li></ul>
