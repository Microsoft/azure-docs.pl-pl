---
title: Uwidacznianie serwera WebSocket Application Gateway
description: Ten artykuł zawiera informacje na temat udostępniania serwerowi WebSocket Application Gateway z kontrolerem transferu danych przychodzących dla klastrów AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79297836"
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
```sh
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
W zależności od implementacji serwera (w[tym miejscu](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) mogą być wymagane określone nagłówki protokołu WebSocket (`Sec-Websocket-Version` na przykład).
Ponieważ Application Gateway nie dodaje nagłówków protokołu WebSocket, najprawdopodobniej odpowiedź sondy kondycji Application Gateway z serwera WebSocket `400 Bad Request`.
W efekcie Application Gateway oznacza to, że Twoje wartości są oznaczone jako w złej kondycji, co w `502 Bad Gateway` efekcie będzie powodować dla odbiorców serwera WebSocket.
Aby tego uniknąć, może być konieczne dodanie procedury obsługi HTTP GET na potrzeby kontroli kondycji na serwerze (`/health` na przykład zwracające wartość `200 OK`).
