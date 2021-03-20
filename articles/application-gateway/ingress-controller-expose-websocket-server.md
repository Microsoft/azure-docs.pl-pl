---
title: Uwidacznianie serwera WebSocket Application Gateway
description: Ten artykuł zawiera informacje na temat udostępniania serwerowi WebSocket Application Gateway z kontrolerem transferu danych przychodzących dla klastrów AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 68d4ff7e4617136e4c58ce672f34de56e46f0229
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85207791"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Uwidacznianie serwera WebSocket Application Gateway

Zgodnie z opisem w dokumentacji Application Gateway v2 — [zapewnia natywną obsługę protokołów WebSocket i http/2](features.md#websocket-and-http2-traffic). Należy pamiętać, że dla obu Application Gateway i Kubernetesal — nie istnieje konfigurowalne ustawienie użytkownika umożliwiające wybiórcze Włączanie lub wyłączanie obsługi protokołu WebSocket.

W poniższym rozmieszczeniu Kubernetes YAML przedstawiono konfigurację minimalną używaną do wdrożenia serwera WebSocket, która jest taka sama jak wdrażanie zwykłego serwera sieci Web:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Uwzględniając, że wszystkie wymagania wstępne są spełnione i masz Application Gateway kontrolowane przez ruch przychodzący Kubernetes w AKS, wdrożenie powyżej spowoduje, że serwer WebSockets będzie narażony na port 80 Application Gateway publicznego adresu IP i `ws.contoso.com` domeny.

Następujące polecenie zwinięcie spowoduje przetestowanie wdrożenia serwera WebSocket:
```shell
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Sondy kondycji protokołu WebSocket

Jeśli w danym wdrożeniu nie zdefiniowano jawnie sond kondycji, Application Gateway podejmie próbę pobrania HTTP w punkcie końcowym serwera WebSocket.
W zależności od implementacji serwera (w[tym miejscu](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) mogą być wymagane określone nagłówki protokołu WebSocket ( `Sec-Websocket-Version` na przykład).
Ponieważ Application Gateway nie dodaje nagłówków protokołu WebSocket, najprawdopodobniej odpowiedź sondy kondycji Application Gateway z serwera WebSocket `400 Bad Request` .
W efekcie Application Gateway oznacza to, że Twoje wartości są oznaczone jako w złej kondycji, co w efekcie będzie powodować `502 Bad Gateway` dla odbiorców serwera WebSocket.
Aby tego uniknąć, może być konieczne dodanie procedury obsługi HTTP GET na potrzeby kontroli kondycji na serwerze ( `/health` na przykład zwracające wartość `200 OK` ).
