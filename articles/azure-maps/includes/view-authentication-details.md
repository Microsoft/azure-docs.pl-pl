---
title: Wyświetl szczegóły uwierzytelniania Azure Maps
description: Użyj Azure Portal, aby wyświetlić szczegóły uwierzytelniania Azure Maps.
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9146844a6b83f78ad99ef7cd1aec4b028daf3ff6
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988617"
---
Możesz wyświetlić szczegóły uwierzytelniania konta Azure Maps w Azure Portal. Na Twoim koncie w menu **Ustawienia** wybierz pozycję **uwierzytelnianie**.

![Szczegóły uwierzytelniania](../media/how-to-manage-authentication/how-to-view-auth.png)

Po utworzeniu konta Azure Maps `x-ms-client-id` wartość Azure Maps jest obecna na stronie Szczegóły uwierzytelniania Azure Portal. Ta wartość reprezentuje konto, które będzie używane na potrzeby żądań interfejsu API REST. Ta wartość powinna być przechowywana w konfiguracji aplikacji i pobierana przed przystąpieniem do wykonywania żądań HTTP podczas korzystania z uwierzytelniania usługi Azure AD z Azure Maps.
