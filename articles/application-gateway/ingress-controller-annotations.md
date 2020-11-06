---
title: Application Gateway adnotacje kontrolera transferu danych przychodzących
description: Ten artykuł zawiera dokumentację dotyczącą adnotacji specyficznych dla kontrolera Application Gateway transferu danych przychodzących.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 4f570f9f18f9c9d484a9bc9c1a5c64d42dbdc714
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397454"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Adnotacje dla Application Gateway kontroler danych przychodzących 

## <a name="introductions"></a>Wprowadzenia

Zasób Kubernetes Ingres może być oznaczony za pomocą dowolnej pary klucz/wartość. AGIC opiera się na adnotacjach do funkcji programu Application Gateway, których nie można skonfigurować za pośrednictwem transferu danych przychodzących YAML. Adnotacje przychodzące są stosowane do wszystkich ustawień HTTP, pul zaplecza i odbiorników pochodzących z zasobów przychodzących.

## <a name="list-of-supported-annotations"></a>Lista obsługiwanych adnotacji

Aby zasób transferu danych przychodzących był obserwowany przez AGIC, **należy dodać do niego adnotację** `kubernetes.io/ingress.class: azure/application-gateway` . Dopiero wtedy AGIC będzie działała z zapytaniem o dane wejściowe.

| Klucz adnotacji | Typ wartości | Wartość domyślna | Dozwolone wartości
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` s | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` s | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Prefiks ścieżki zaplecza

Ta adnotacja umożliwia zapisanie ścieżki zaplecza określonej w zasobie transferu danych przychodzących z prefiksem określonym w tej adnotacji. Dzięki temu użytkownicy mogą ujawniać usługi, których punkty końcowe są inne niż nazwy punktów końcowych używane do udostępniania usługi w zasobie transferu danych przychodzących.

### <a name="usage"></a>Użycie

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Przykład

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
W powyższym przykładzie zdefiniowano zasób transferu danych przychodzących o nazwie `go-server-ingress-bkprefix` z adnotacją `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` . Adnotacja informuje bramę aplikacji o utworzeniu ustawienia HTTP, które będzie miało przesłonięcie prefiksu ścieżki dla ścieżki `/hello` do `/test/` .

> [!NOTE] 
> W powyższym przykładzie zdefiniowano tylko jedną regułę. Jednakże adnotacje mają zastosowanie do całego zasobu związanego z ruchem przychodzącym, więc jeśli użytkownik określił wiele reguł, prefiks ścieżki zaplecza zostanie skonfigurowany dla każdej z określonych ścieżek. W takim przypadku, jeśli użytkownik chce mieć różne reguły z różnymi prefiksami ścieżki (nawet dla tej samej usługi), trzeba zdefiniować różne zasoby związane z ruchem przychodzącym.

## <a name="tls-redirect"></a>Przekierowanie protokołu TLS

Application Gateway [można skonfigurować tak](./redirect-overview.md) , aby automatycznie przekierowywać adresy URL http do ich odpowiedników https. Gdy ta adnotacja jest obecna, a protokół TLS jest prawidłowo skonfigurowany, Kubernetes transferu danych przychodzących utworzy [regułę routingu z konfiguracją przekierowania](./redirect-http-to-https-portal.md#add-a-routing-rule-with-a-redirection-configuration) i zastosuje zmiany do Application Gateway. Utworzone przekierowanie będzie HTTP `301 Moved Permanently` .

### <a name="usage"></a>Użycie

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Przykład

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Opróżnianie połączenia

`connection-draining`: Ta adnotacja umożliwia użytkownikom określenie, czy należy włączyć opróżnianie połączenia.
`connection-draining-timeout`: Ta adnotacja pozwala użytkownikom na określenie limitu czasu, po upływie którego Application Gateway zakończy żądania do opróżniania punktu końcowego zaplecza.

### <a name="usage"></a>Użycie

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Przykład

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Koligacja oparta na plikach cookie

Ta adnotacja pozwala określić, czy ma zostać włączona koligacja oparta na plikach cookie.

### <a name="usage"></a>Użycie

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Przykład

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Limit czasu żądania

Ta adnotacja pozwala określić limit czasu żądania (w sekundach), po upływie którego Application Gateway żądanie zakończy się niepowodzeniem, jeśli odpowiedź nie zostanie odebrana.

### <a name="usage"></a>Użycie

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Przykład

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Użyj prywatnego adresu IP

Ta adnotacja pozwala określić, czy ten punkt końcowy ma zostać ujawniony w prywatnym adresie IP Application Gateway.

> [!NOTE]
> * Application Gateway nie obsługuje wielu adresów IP na tym samym porcie (przykład: 80/443). Ruch `appgw.ingress.kubernetes.io/use-private-ip: "false"` przychodzący z adnotacją i inne z opcją with `appgw.ingress.kubernetes.io/use-private-ip: "true"` `HTTP` spowoduje niepowodzenie AGIC podczas aktualizowania Application Gateway.
> * W przypadku Application Gateway, które nie mają prywatnego adresu IP, Ingresses z `appgw.ingress.kubernetes.io/use-private-ip: "true"` zostanie zignorowane. Spowoduje to odzwierciedlenie w dziennikach kontrolera i zdarzeń związanych z transferem danych przychodzących dla tych ingresses z `NoPrivateIP` ostrzeżeniem.


### <a name="usage"></a>Użycie
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Przykład
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Protokół zaplecza

Ta adnotacja umożliwia określenie protokołu, który Application Gateway powinien być używany podczas rozmowy z zasobnikami. Obsługiwane protokoły: `http` , `https`

> [!NOTE]
> * W przypadku, gdy certyfikaty z podpisem własnym są obsługiwane w Application Gateway, obecnie AGIC tylko w `https` przypadku korzystania z certyfikatu podpisanego przez dobrze znany urząd certyfikacji.
> * Upewnij się, że nie używasz portu 80 z protokołem HTTPS i portem 443 z protokołem HTTP w przypadku zasobników.

### <a name="usage"></a>Użycie
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Przykład
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```