---
title: Interfejs API REST usługi Azure App Configuration — autoryzacja HMAC
description: Użyj algorytmu HMAC do autoryzacji względem konfiguracji aplikacji platformy Azure przy użyciu interfejsu API REST
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3746fcf06aea48c9c80696b634d6323b9cb21822
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932663"
---
# <a name="hmac-authorization---rest-api-reference"></a>Autoryzacja HMAC — dokumentacja interfejsu API REST

Gdy jest używane uwierzytelnianie HMAC, operacje należą do jednej z dwóch kategorii, odczytu lub zapisu. Klucze dostępu do odczytu i zapisu przyznają uprawnienia do wywoływania wszystkich operacji. Klucze dostępu tylko do odczytu przyznają uprawnienia do wywoływania tylko operacji odczytu. Czy klucz dostępu jest tylko do odczytu, czy jest określany przez właściwość Read-Write `readOnly` . Każda próba wykonania żądania zapisu z użyciem klucza dostępu tylko do odczytu spowoduje, że żądanie nie zostanie autoryzowane.

## <a name="obtaining-access-keys"></a>Uzyskiwanie kluczy dostępu

Specyfikacja opisująca klucze dostępu i interfejs API służący do ich uzyskiwania [opisano](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json)szczegółowo w specyfikacji dostawcy zasobów konfiguracji aplikacji platformy Azure. Klucze dostępu są uzyskiwane za pośrednictwem operacji "ConfigurationStores_ListKeys".

## <a name="errors"></a>błędy

```http
HTTP/1.1 403 Forbidden
```

**Przyczyna:** Klucz dostępu używany do uwierzytelniania żądania nie zapewnia wymaganych uprawnień do wykonania żądanej operacji.
**Rozwiązanie:** Uzyskaj klucz dostępu, który zapewnia uprawnienia do wykonywania żądanych operacji i użyj go do uwierzytelnienia żądania.
