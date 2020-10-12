---
title: Dodaj sondy kondycji do AKSych/zasobników
description: Ten artykuł zawiera informacje na temat dodawania sond kondycji (gotowość i/lub czas życia) do AKSch z Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8c8b8b0090877db7abc8fae0e44f928e8b10dcf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808006"
---
# <a name="add-health-probes-to-your-service"></a>Dodawanie sond kondycji do usługi
Domyślnie kontroler transferu danych w ramach udostępniania protokołu HTTP GET dla uwidocznionych zasobników.
Właściwości sondy można dostosować, dodając [gotowość lub sondę na żywo](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) do swojej `deployment` / `pod` specyfikacji.

## <a name="with-readinessprobe-or-livenessprobe"></a>Z `readinessProbe` lub `livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Dokumentacja interfejsu API Kubernetes:
* [Sondy kontenera](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [Akcja narzędzia HttpGet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe` i `livenessProbe` są obsługiwane w przypadku skonfigurowania programu `httpGet` .
> * Sondowanie na porcie innym niż ten uwidoczniony w obszarze pod nie jest obecnie obsługiwane.
> * `HttpHeaders`, `InitialDelaySeconds` , `SuccessThreshold` nie są obsługiwane.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Bez `readinessProbe` ani `livenessProbe`
Jeśli powyższe sondy nie zostaną podane, kontroler transferu danych przychodzących przyjmuje założenie, że usługa jest dostępna `Path` dla `backend-path-prefix` adnotacji lub `path` określonego w `ingress` definicji usługi.

## <a name="default-values-for-health-probe"></a>Wartości domyślne sondy kondycji
Dla każdej właściwości, która nie może zostać wywnioskowana przez sondę gotowości/na żywo, ustawiane są wartości domyślne.

| Application Gateway Właściwość sondy | Wartość domyślna |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |