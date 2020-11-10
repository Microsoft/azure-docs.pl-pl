---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/05/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bb6f2281cc115904db6babcf31f1af387b254d48
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426572"
---
## <a name="trusted-microsoft-services"></a>Zaufane usługi firmy Microsoft
Po włączeniu ustawienia **Zezwalaj zaufanym usługom firmy Microsoft na pominięcie tej zapory** zostanie udzielony dostęp do zasobów Service Bus za pomocą następujących usług.

| Usługa zaufana | Obsługiwane scenariusze użycia | 
| --------------- | ------------------------- | 
| Azure Event Grid | Umożliwia Azure Event Grid wysyłanie zdarzeń do kolejek lub tematów w przestrzeni nazw Service Bus. Należy również wykonać następujące czynności: <ul><li>Włączanie tożsamości przypisanej do systemu dla tematu lub domeny</li><li>Dodawanie tożsamości do roli nadawca danych Azure Service Bus w przestrzeni nazw Service Bus</li><li>Następnie należy skonfigurować subskrypcję zdarzeń korzystającą z kolejki Service Bus lub tematu jako punkt końcowy do korzystania z tożsamości przypisanej do systemu.</li></ul> <p>Aby uzyskać więcej informacji, zobacz [dostarczanie zdarzeń przy użyciu tożsamości zarządzanej](../articles/event-grid/managed-service-identity.md)</p>|
