---
title: Włącz koligację opartą na plikach cookie przy użyciu Application Gateway
description: Ten artykuł zawiera informacje na temat włączania koligacji opartej na plikach cookie przy użyciu Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397420"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Włącz koligację opartą na plikach cookie przy użyciu Application Gateway
Zgodnie z opisem w [dokumentacji usługi Azure Application Gateway](./application-gateway-components.md#http-settings)Application Gateway obsługuje koligację opartą na plikach cookie, co oznacza, że może kierować kolejny ruch z sesji użytkownika do tego samego serwera w celu przetworzenia.

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