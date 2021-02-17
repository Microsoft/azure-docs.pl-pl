---
title: Ochrona obciążeń dla obciążeń Kubernetes
description: Dowiedz się, jak używać zestawu Azure Security Center zalecenia dotyczące zabezpieczeń ochrony obciążeń Kubernetes
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/16/2021
ms.author: memildin
ms.openlocfilehash: f024457ed9f20e6bab0852a53ab6a60ac85ed25a
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546265"
---
# <a name="protect-your-kubernetes-workloads"></a>Ochrona obciążeń Kubernetes

Na tej stronie opisano, jak używać Azure Security Center zestawu zaleceń dotyczących zabezpieczeń przeznaczonych dla ochrony obciążeń Kubernetes.

Dowiedz się więcej o tych funkcjach w [temacie dotyczącym ochrony obciążeń przy użyciu funkcji Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

Security Center oferuje więcej funkcji zabezpieczeń kontenerów po włączeniu usługi Azure Defender. W szczególności:

- Skanuj rejestry kontenerów pod kątem luk w zabezpieczeniach w [usłudze Azure Defender dla rejestrów kontenerów](defender-for-container-registries-introduction.md)
- Otrzymuj alerty wykrywania zagrożeń w czasie rzeczywistym dla klastrów K8s [Azure Defender dla Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> Aby zapoznać się z listą *wszystkich* zaleceń dotyczących zabezpieczeń, które mogą pojawić się w przypadku klastrów Kubernetes i węzłów, zobacz [sekcję obliczenia](recommendations-reference.md#recs-compute) w tabeli referencyjnej rekomendacji.



## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólna dostępność (GA)|
|Wpisaną|Bezpłatna|
|Wymagane role i uprawnienia:|**Właściciel** lub **administrator zabezpieczeń** , który edytuje przypisanie<br>**Czytnik** do wyświetlania zaleceń|
|Wymagania dotyczące środowiska:|Wymagany jest Kubernetes w wersji 1.14 (lub nowszej)<br>Brak zasobu PodSecurityPolicy (stary model PSP) w klastrach<br>Węzły systemu Windows nie są obsługiwane|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||


## <a name="set-up-your-workload-protection"></a>Konfigurowanie ochrony obciążeń

Azure Security Center obejmuje pakiet zaleceń, które są dostępne po zainstalowaniu **dodatku Azure Policy dla Kubernetes**.

### <a name="step-1-deploy-the-add-on"></a>Krok 1. wdrażanie dodatku

Aby skonfigurować zalecenia, zainstaluj  **dodatek Azure Policy dla Kubernetes**. 

- Ten dodatek można wdrożyć ponownie, jak wyjaśniono w temacie [Włączanie obsługi autoaprowizacji rozszerzeń](security-center-enable-data-collection.md#enable-auto-provisioning-of-extensions). Gdy funkcja autoaprowizacji dla dodatku jest ustawiona na wartość "on", rozszerzenie jest domyślnie włączone we wszystkich istniejących i przyszłych klastrach (które spełniają wymagania instalacji dodatku).

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Instalowanie dodatku zasad dla Kubernetes za pomocą narzędzia automatycznej aprowizacji Security Center":::

- Aby ręcznie wdrożyć dodatek:

    1. Na stronie zalecenia Wyszukaj zalecenie "**Azure Policy dodatek dla Kubernetes powinien być zainstalowany i włączony w klastrach**". 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="Zalecenie * * Azure Policy dodatku dla Kubernetes powinny być zainstalowane i włączone w klastrach * *":::

        > [!TIP]
        > Zalecenie jest zawarte w pięciu różnych kontrolach zabezpieczeń i nie ma znaczenia, która z nich została wybrana w następnym kroku.

    1. Z dowolnego z formantów zabezpieczeń wybierz zalecenie, aby zobaczyć zasoby, na których można zainstalować dodatek.
    1. Wybierz odpowiedni klaster i **skoryguj** go.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="Strona szczegółów rekomendacji dla * * Azure Policy dodatku dla Kubernetes powinna być zainstalowana i włączona w klastrach * *":::

### <a name="step-2-view-and-configure-the-bundle-of-13-recommendations"></a>Krok 2. Wyświetl i skonfiguruj pakiet 13 zaleceń

1. Około 30 minut po zakończeniu instalacji dodatku Security Center pokazuje stan kondycji klastrów dla następujących zaleceń, każdy w odpowiedniej kontroli zabezpieczeń, jak pokazano poniżej:

    > [!TIP]
    > Niektóre zalecenia mają parametry, które muszą być dostosowane za pośrednictwem Azure Policy, aby efektywnie korzystać z nich. Na przykład aby skorzystać z **obrazów kontenerów rekomendacji, należy wdrożyć tylko z zaufanych rejestrów**, należy zdefiniować zaufane rejestry.
    > 
    > Jeśli nie wprowadzisz wymaganych parametrów dla zaleceń, które wymagają konfiguracji, Twoje obciążenia będą wyświetlane jako w złej kondycji.

    | Nazwa zalecenia                                                         | Kontrola zabezpieczeń                         | Wymagana konfiguracja |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | Należy wymusić limity procesora CPU i pamięci kontenera                          | Ochrona aplikacji przed atakiem DDoS | Nie                     |
    | Należy unikać używania kontenerów uprzywilejowanych                                     | Zarządzanie dostępem i uprawnieniami            | Nie                     |
    | Niezmienny (tylko do odczytu) główny system plików powinien zostać wymuszony dla kontenerów     | Zarządzanie dostępem i uprawnieniami            | Nie                     |
    | Należy unikać kontenera z eskalacją uprawnień                       | Zarządzanie dostępem i uprawnieniami            | Nie                     |
    | Należy unikać uruchamiania kontenerów jako użytkownik główny                           | Zarządzanie dostępem i uprawnieniami            | Nie                     |
    | Należy unikać obsługi kontenerów chronionych przestrzeni nazw hosta              | Zarządzanie dostępem i uprawnieniami            | Nie                     |
    | W przypadku kontenerów powinny być wymuszane najmniej uprzywilejowane funkcje systemu Linux.       | Zarządzanie dostępem i uprawnieniami            | **Tak**                |
    | Użycie instalacji woluminów HostPath na początku powinno być ograniczone do znanej listy    | Zarządzanie dostępem i uprawnieniami            | **Tak**                |
    | Kontenery powinny nasłuchiwać tylko na dozwolonych portach                              | Ogranicz nieautoryzowany dostęp do sieci     | **Tak**                |
    | Usługi powinny nasłuchiwać tylko na dozwolonych portach                                | Ogranicz nieautoryzowany dostęp do sieci     | **Tak**                |
    | Użycie sieci i portów hosta powinno być ograniczone                     | Ogranicz nieautoryzowany dostęp do sieci     | **Tak**                |
    | Zastępowanie lub wyłączanie profilowania AppArmor profil powinien być ograniczony | Korygowanie konfiguracji zabezpieczeń        | **Tak**                |
    | Obrazy kontenerów powinny być wdrażane tylko z zaufanych rejestrów            | Koryguj luki w zabezpieczeniach                | **Tak**                |
    |||


1. Aby zalecenia z parametrami muszą być dostosowane, należy ustawić parametry:

    1. Z menu Security Center wybierz pozycję **zasady zabezpieczeń**.
    1. Wybierz odpowiednią subskrypcję.
    1. W sekcji **Security Center Policy default** wybierz pozycję **Wyświetl zasady obowiązujące**.
    1. Wybierz pozycję "domyślna wartość ASC".
    1. Otwórz kartę **Parametry** i zmodyfikuj wartości zgodnie z potrzebami.
    1. Wybierz pozycję **Przejrzyj i zapisz**.
    1. Wybierz pozycję **Zapisz**.


1. Aby wymusić dowolne zalecenia, 

    1. Otwórz stronę szczegóły rekomendacji i wybierz pozycję **Odmów**:

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Opcja odmowy dla parametru Azure Policy":::

        Spowoduje to otwarcie okienka, w którym ustawisz zakres. 

    1. Po ustawieniu zakresu wybierz pozycję **Zmień na Odmów**.

1. Aby zobaczyć, które zalecenia dotyczą klastrów:

    1. Otwórz stronę [spisu](asset-inventory.md) zasobów Security Center i Użyj filtru typu zasobów do **usług Kubernetes Services**.

    1. Wybierz klaster, aby zbadać i przejrzeć dostępne zalecenia. 

1. Podczas wyświetlania rekomendacji z zestawu ochrony obciążeń zobaczysz liczbę uwzględnionych zasobników ("Kubernetes Components") na liście obok klastra. Aby zapoznać się z listą określonych zasobników, wybierz klaster, a następnie wybierz pozycję **podejmij akcję**.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Wyświetlanie odpowiednich zasobników dla rekomendacji K8s"::: 

1. Aby przetestować wymuszanie, użyj dwóch poniższych wdrożeń Kubernetes:

    - Jeden z nich to wdrożenie w dobrej kondycji, zgodne z pakietem zaleceń dotyczących ochrony obciążeń.
    - Druga dotyczy wdrożenia w złej kondycji, które nie jest zgodne z *żadnym* z zaleceń.

    Wdróż pliki przykład. YAML jako-is lub użyj ich jako odniesienia do korygowania własnych obciążeń (krok VIII)  


## <a name="healthy-deployment-example-yaml-file"></a>Przykład wdrażania w dobrej kondycji plik YAML

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>Przykład wdrożenia w złej kondycji. YAML plik

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania ochrony obciążeń Kubernetes. 

Inne powiązane materiały można znaleźć na następujących stronach: 

- [Security Center zalecenia dotyczące obliczeń](recommendations-reference.md#recs-compute)
- [Alerty dla poziomu klastra AKS](alerts-reference.md#alerts-akscluster)
- [Alerty na poziomie hosta kontenera](alerts-reference.md#alerts-containerhost)