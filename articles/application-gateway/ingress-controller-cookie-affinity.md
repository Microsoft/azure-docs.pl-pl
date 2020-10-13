---
title: Włącz koligację opartą na plikach cookie przy użyciu Application Gateway
description: Ten artykuł zawiera informacje na temat włączania koligacji opartej na plikach cookie przy użyciu Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3af2705fedbb9c19d4f128e8e997d3fa73f8b5a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807968"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Włącz koligację opartą na plikach cookie przy użyciu Application Gateway
Zgodnie z opisem w [dokumentacji usługi Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)Application Gateway obsługuje koligację opartą na plikach cookie, co oznacza, że może kierować kolejny ruch z sesji użytkownika do tego samego serwera w celu przetworzenia.

## <a name="example"></a>Przykład
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
