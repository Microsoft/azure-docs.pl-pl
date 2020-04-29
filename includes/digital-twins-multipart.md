---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
ms.custom: include file
ms.openlocfilehash: 0e7cb7e4aaa9862a2b4af51593c29793ea54dd14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77111239"
---
> [!NOTE]
> Żądania wieloczęściowe wymagają zwykle trzech kawałków:
> * Nagłówek **Content-Type** :
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **Dyspozycja zawartości**:
>   * `form-data; name="metadata"`
> * Zawartość pliku do przekazania
>
> **Typ zawartości** i **Dyspozycja zawartości** będą się różnić w zależności od scenariusza użycia.

Wieloczęściowe żądania można programistycznie wprowadzać programowo (za pomocą języka C#) za pomocą klienta REST lub narzędzia, [takiego jak program](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). Narzędzia klienta REST mogą mieć różne poziomy obsługi złożonych żądań wieloczęściowych. Ustawienia konfiguracji mogą być również nieco inne niż narzędzia do narzędzia. Sprawdź, które narzędzie jest najlepiej dopasowane do Twoich potrzeb.

> [!IMPORTANT]
> Wieloczęściowe żądania interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management mają zwykle dwie części:
> * Metadane obiektu BLOB (takie jak skojarzony typ MIME) zadeklarowane przez **Typ zawartości** i/lub **dyspozycję zawartości**
> * Zawartość obiektu BLOB, która obejmuje zawartość bez struktury pliku do przekazania
>
> Żadna z tych dwóch części nie jest wymagana w przypadku żądań **patch** . Oba są wymagane dla operacji **post** lub CREATE.

[Kod źródłowy przewodnika Szybki Start](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) zawiera pełne przykłady w języku C# przedstawiające sposób tworzenia wieloczęściowych żądań względem interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management.