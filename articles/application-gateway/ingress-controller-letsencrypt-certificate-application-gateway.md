---
title: Używanie certyfikatów LetsEncrypt.org z Application Gateway
description: Ten artykuł zawiera informacje na temat uzyskiwania certyfikatu z usługi LetsEncrypt.org i używania go na Application Gateway klastrach AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807028"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Używanie certyfikatów z LetsEncrypt.org na Application Gateway dla klastrów AKS

Ta sekcja służy do konfigurowania AKS do korzystania z [LetsEncrypt.org](https://letsencrypt.org/) i automatycznego uzyskiwania certyfikatu TLS/SSL dla domeny. Certyfikat zostanie zainstalowany na Application Gateway, co spowoduje zakończenie protokołu SSL/TLS dla klastra AKS. W opisanej tutaj konfiguracji jest używany dodatek Kubernetes [Menedżera certyfikatów](https://github.com/jetstack/cert-manager) , który automatyzuje tworzenie certyfikatów i zarządzanie nimi.

Wykonaj poniższe kroki, aby zainstalować [Menedżera certyfikatów](https://docs.cert-manager.io) w istniejącym klastrze AKS.

1. Wykres Helm

    Uruchom następujący skrypt, aby zainstalować `cert-manager` Wykres Helm. Spowoduje to:

    - Utwórz nową `cert-manager` przestrzeń nazw na AKS
    - Utwórz następujący CRDs: Certificate, Challenge, ClusterIssuer, Issuer, Order
    - Instalowanie wykresu Menedżera certyfikatów (z [docs.CERT-Manager.IO)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. Zasób ClusterIssuer

    Utwórz `ClusterIssuer` zasób. Jest to wymagane przez program `cert-manager` do reprezentowania `Lets Encrypt` urzędu certyfikacji, w którym zostaną uzyskane podpisane certyfikaty.

    Przy użyciu zasobu bez przestrzeni nazw `ClusterIssuer` Menedżer certyfikatów będzie wystawiał certyfikaty, które mogą być używane z wielu przestrzeni nazw. `Let’s Encrypt` używa protokołu ACME do sprawdzenia, czy podaną nazwę domeny i wystawiasz certyfikat. Więcej szczegółowych informacji na temat konfigurowania `ClusterIssuer` właściwości [znajdziesz tutaj](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer` nakazuje `cert-manager` wystawienie certyfikatów przy użyciu `Lets Encrypt` środowiska przejściowego używanego do testowania (certyfikat główny nieobecny w sklepie/magazyn zaufania klienta).

    Domyślny typ wyzwania w YAML poniżej to `http01` . Inne wyzwania są udokumentowane na [letsencrypt.org — typy wyzwania](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Aktualizacja `<YOUR.EMAIL@ADDRESS>` w YAML poniżej

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Wdróż aplikację

    Utwórz zasób transferu danych przychodzących, aby udostępnić `guestbook` aplikację przy użyciu Application Gateway z opcją szyfrowania certyfikatu.

    Upewnij się, że Application Gateway ma publiczną konfigurację adresu IP frontonu z nazwą DNS (przy użyciu `azure.com` domeny domyślnej lub Zainicjuj obsługę administracyjną `Azure DNS Zone` usługi i przypisz własną domenę niestandardową).
    Zwróć uwagę na adnotację `certmanager.k8s.io/cluster-issuer: letsencrypt-staging` , która informuje Menedżera certyfikatów, aby przetworzyć oznakowany zasób transferu danych przychodzących.

    > [!IMPORTANT] 
    > Aktualizacja `<PLACEHOLDERS.COM>` w YAML poniżej z własną domeną (lub Application Gateway, na przykład "KH-AKS-Ingress.westeurope.cloudapp.Azure.com")

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Po kilku sekundach można uzyskać dostęp do `guestbook` usługi za pośrednictwem adresu URL protokołu HTTPS Application Gateway przy użyciu automatycznie wystawionego certyfikatu **przemieszczania** `Lets Encrypt` .
    Przeglądarka może ostrzec użytkownika o nieprawidłowym urzędzie certyfikacji. Certyfikat przemieszczania jest wystawiony przez `CN=Fake LE Intermediate X1` . Wskazuje to, że system działał zgodnie z oczekiwaniami i jest gotowy do certyfikatu produkcyjnego.

4. Certyfikat produkcyjny

    Po pomyślnym skonfigurowaniu certyfikatu przemieszczania można przełączyć się na produkcyjny serwer xyz:
    1. Zastąp adnotację przemieszczania w zasobie transferu danych przychodzących przy użyciu: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Usuń istniejące przemieszczanie `ClusterIssuer` utworzone w poprzednim kroku i Utwórz nowe, zastępując serwer XYZ z CLUSTERISSUER YAML powyżej z `https://acme-v02.api.letsencrypt.org/directory`

5. Wygaśnięcie i odnowienie certyfikatu

    Przed `Lets Encrypt` wygaśnięciem certyfikatu `cert-manager` program automatycznie zaktualizuje certyfikat w magazynie Kubernetes Secret. W tym momencie Application Gateway kontroler transferu danych przychodzących będzie stosował zaktualizowany klucz tajny, do którego odwołuje się zasób transferu danych przychodzących, za pomocą którego można skonfigurować Application Gateway.
