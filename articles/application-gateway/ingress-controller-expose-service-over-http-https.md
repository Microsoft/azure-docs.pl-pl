---
title: Uwidacznianie usługi AKS za pośrednictwem protokołu HTTP lub HTTPS przy użyciu Application Gateway
description: Ten artykuł zawiera informacje dotyczące sposobu uwidaczniania usługi AKS za pośrednictwem protokołu HTTP lub HTTPS przy użyciu Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3b816ddc0eccf8c406cfed37d6bfc594e27d3629
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85850360"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Uwidacznianie usługi AKS za pośrednictwem protokołu HTTP lub HTTPS przy użyciu Application Gateway 

Te samouczki ułatwiają zilustrowanie użycia [zasobów Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) w celu udostępnienia przykładowej usługi Kubernetes za [Application Gateway](https://azure.microsoft.com/services/application-gateway/) pośrednictwem protokołu HTTP lub https.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstalowano `ingress-azure` Wykres Helm.
  - [**Wdrożenie Greenfield**](ingress-controller-install-new.md): Jeśli zaczynasz od podstaw, zapoznaj się z tymi instrukcjami dotyczącymi instalacji, które opisują kroki wdrażania klastra AKS z Application Gateway i zainstalowania w klastrze AKS kontrolera usługi Application.
  - [**Wdrożenie brownfield**](ingress-controller-install-existing.md): Jeśli masz istniejący klaster AKS i Application Gateway, zapoznaj się z tymi instrukcjami, aby zainstalować w klastrze AKS kontroler danych wejściowych bramy aplikacji.
- Jeśli chcesz używać protokołu HTTPS w tej aplikacji, będzie potrzebny certyfikat x509 i jego klucz prywatny.

## <a name="deploy-guestbook-application"></a>Wdróż `guestbook` aplikację

Aplikacja księgi Gościa to kanoniczna aplikacja Kubernetes, która składa się z frontonu interfejsu użytkownika sieci Web, zaplecza i bazy danych Redis. Domyślnie program `guestbook` uwidacznia swoją aplikację za pomocą usługi o nazwie `frontend` na porcie `80` . Bez Kubernetes zasobów przychodzących usługa nie jest dostępna spoza klastra AKS. W celu uzyskania dostępu do aplikacji za pośrednictwem protokołów HTTP i HTTPS będziemy korzystać z aplikacji i skonfigurować zasoby transferu danych przychodzących.

Postępuj zgodnie z poniższymi instrukcjami, aby wdrożyć aplikację Księgi Gości.

1. Pobierz `guestbook-all-in-one.yaml` z tego [miejsca](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Wdróż `guestbook-all-in-one.yaml` w KLASTRZE AKS, uruchamiając

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Teraz `guestbook` aplikacja została wdrożona.

## <a name="expose-services-over-http"></a>Uwidacznianie usług za pośrednictwem protokołu HTTP

W celu udostępnienia aplikacji księgi gościa będziemy używać następujących zasobów przychodzących:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Ten ruch przychodzący spowoduje udostępnienie `frontend` usługi `guestbook-all-in-one` wdrożenia jako domyślnego zaplecza Application Gateway.

Zapisz powyżej zasób transferu danych przychodzących jako `ing-guestbook.yaml` .

1. Wdróż `ing-guestbook.yaml` przez uruchomienie:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Sprawdź dziennik transferu danych przychodzących na potrzeby stanu wdrożenia.

Teraz `guestbook` aplikacja powinna być dostępna. Możesz to sprawdzić, odwiedzając adres publiczny Application Gateway.

## <a name="expose-services-over-https"></a>Uwidacznianie usług za pośrednictwem protokołu HTTPS

### <a name="without-specified-hostname"></a>Bez określonej nazwy hosta

Bez określenia nazwy hosta usługa księgi gościa będzie dostępna na wszystkich nazwach hostów wskazujących na bramę aplikacji.

1. Przed wdrożeniem transferu danych przychodzących należy utworzyć klucz tajny Kubernetes, aby hostować certyfikat i klucz prywatny. Wpis tajny Kubernetes można utworzyć, uruchamiając

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Zdefiniuj następujące przychodzące. W obszarze ruchu przychodzącego Określ nazwę wpisu tajnego w `secretName` sekcji.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Zastąp wartość `<guestbook-secret-name>` w powyższym przychodzącym zasobem przy użyciu nazwy klucza tajnego. Zapisz powyżej zasób transferu danych przychodzących w nazwie pliku `ing-guestbook-tls.yaml` .

1. Wdrożenie w usłudze YAML

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Sprawdź dziennik transferu danych przychodzących na potrzeby stanu wdrożenia.

Teraz `guestbook` aplikacja będzie dostępna zarówno dla protokołu HTTP, jak i https.

### <a name="with-specified-hostname"></a>Z określoną nazwą hosta

Można również określić nazwę hosta dla ruchu przychodzącego w celu ustawienia konfiguracji i usług TLS.
Określając nazwę hosta, usługa księgi gościa będzie dostępna tylko na określonym hoście.

1. Zdefiniuj następujące przychodzące.
    W obszarze ruch przychodzący Określ nazwę wpisu tajnego w `secretName` sekcji i odpowiednio Zamień nazwę hosta w `hosts` sekcji.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Wdróż `ing-guestbook-tls-sni.yaml` przez uruchomienie

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Sprawdź dziennik transferu danych przychodzących na potrzeby stanu wdrożenia.

Teraz `guestbook` aplikacja będzie dostępna zarówno dla protokołu HTTP, jak i https na określonym hoście ( `<guestbook.contoso.com>` w tym przykładzie).

## <a name="integrate-with-other-services"></a>Integracja z innymi usługami

Poniższe usługi wejściowe umożliwią dodanie dodatkowych ścieżek do tego ruchu przychodzącego i przekierowanie tych ścieżek do innych usług:

```yaml
apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: guestbook
    annotations:
      kubernetes.io/ingress.class: azure/application-gateway
  spec:
    rules:
    - http:
        paths:
        - path: </other/*>
          backend:
            serviceName: <other-service>
            servicePort: 80
        - backend:
            serviceName: frontend
            servicePort: 80
```
