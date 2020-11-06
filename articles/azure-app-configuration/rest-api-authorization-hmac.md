---
title: Interfejs API REST usługi Azure App Configuration — autoryzacja HMAC
description: Użyj algorytmu HMAC do autoryzacji względem konfiguracji aplikacji platformy Azure przy użyciu interfejsu API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 8d1f8a17f51711cc5c10567797e1224f96eb7630
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424158"
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
