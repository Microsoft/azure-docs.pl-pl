---
title: Wdróż wystąpienie Prometheus w klastrze Red Hat OpenShift platformy Azure
description: Utwórz wystąpienie Prometheus w klastrze Red Hat OpenShift platformy Azure, aby monitorować metryki aplikacji.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: Prometheus, ARO, OpenShift, Metrics, Red Hat
ms.openlocfilehash: 42ed8c90b35eba57fdc3db1f0ed93d44cf9a5e41
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218616"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Wdrażanie autonomicznego wystąpienia Prometheus w klastrze Red Hat OpenShift platformy Azure

> [!IMPORTANT]
> Usługa Azure Red Hat OpenShift 3,11 zostanie wycofana 30 czerwca 2022. Obsługa tworzenia nowych klastrów usługi Azure Red Hat OpenShift 3,11 jest kontynuowana do 30 listopada 2020. Po wycofaniu pozostałe klastry usługi Azure Red Hat OpenShift 3,11 zostaną zamknięte, aby zapobiec występowaniu luk w zabezpieczeniach.
> 
> Postępuj zgodnie z tym przewodnikiem, aby [utworzyć klaster usługi Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Jeśli masz określone pytania, [skontaktuj się z nami](mailto:arofeedback@microsoft.com).

W tym artykule opisano sposób konfigurowania autonomicznego wystąpienia Prometheus, które korzysta z odnajdowania usług w klastrze Red Hat OpenShift platformy Azure.

> [!NOTE]
> Dostęp administratora klienta do klastra usługi Azure Red Hat OpenShift nie jest wymagany.

Konfiguracja docelowa:

- Jeden projekt (Prometheus-Project), który zawiera Prometheus i Alertmanager.
- Dwa projekty (App-Project1 i App-Project2), które zawierają aplikacje do monitorowania.

Pliki konfiguracji Prometheus należy przygotować lokalnie. Utwórz nowy folder do przechowywania. Pliki konfiguracji są przechowywane w klastrze jako wpisy tajne w przypadku późniejszego dodania do klastra tokenów tajnych.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Logowanie do klastra przy użyciu narzędzia OC

1. Otwórz przeglądarkę internetową, a następnie przejdź do konsoli sieci Web klastra ( https://openshift .* Identyfikator losowy*. *region*. azmosa.IO).
2. Zaloguj się przy użyciu poświadczeń platformy Azure.
3. Wybierz swoją nazwę użytkownika w prawym górnym rogu, a następnie wybierz **polecenie Kopiuj login**.
4. Wklej nazwę użytkownika do terminala, który będzie używany.

> [!NOTE]
> Aby sprawdzić, czy logujesz się do odpowiedniego klastra, uruchom `oc whoami -c` polecenie.

## <a name="prepare-the-projects"></a>Przygotuj projekty

Aby utworzyć projekty, uruchom następujące polecenia:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Możesz użyć `-n` parametru lub albo `--namespace` wybrać aktywny projekt, uruchamiając `oc project` polecenie.

## <a name="prepare-the-prometheus-configuration-file"></a>Przygotuj plik konfiguracji Prometheus
Utwórz plik Prometheus. yml, wprowadzając następującą zawartość:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Utwórz wpis tajny o nazwie prom, wprowadzając następującą konfigurację:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Plik Prometheus. yml to podstawowy plik konfiguracji Prometheus. Ustawia interwały i konfiguruje Autowykrywanie w trzech projektach (Prometheus-Project, App-Project1, App-Project2). W poprzednim pliku konfiguracyjnym punkty końcowe wykryte przez program są odporne na użycie protokołu HTTP bez uwierzytelniania.

Aby uzyskać więcej informacji o punktach końcowych wycinków, zobacz [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Przygotuj plik konfiguracji programu Alertmanager
Utwórz plik alertmanager. yml, wprowadzając następującą zawartość:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Utwórz klucz tajny o nazwie Prom-Alerts, wprowadzając następującą konfigurację:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager. yml to plik konfiguracji Menedżera alertów.

> [!NOTE]
> Aby sprawdzić dwa poprzednie kroki, uruchom `oc get secret -n prometheus-project` polecenie.

## <a name="start-prometheus-and-alertmanager"></a>Uruchamianie Prometheus i Alertmanager
Przejdź do [repozytorium OpenShift/Origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) i Pobierz szablon [Prometheus-Standalone. YAML](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) . Zastosuj szablon do Prometheus-Project, wprowadzając następującą konfigurację:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Plik Prometheus-Standalone. YAML to szablon OpenShift. Spowoduje to utworzenie wystąpienia Prometheus z serwerem proxy OAuth przed nim i wystąpieniem programu Alertmanager również zabezpieczony przy użyciu protokołu OAuth-proxy. W tym szablonie serwer proxy uwierzytelniania OAuth jest skonfigurowany tak, aby zezwalał każdemu użytkownikowi, który może "uzyskać" Przestrzeń nazw Prometheus-Project (zobacz `-openshift-sar` flagę).

> [!NOTE]
> Aby sprawdzić, czy prom StatefulSet ma równe i bieżące repliki liczb, uruchom `oc get statefulset -n prometheus-project` polecenie. Aby sprawdzić wszystkie zasoby w projekcie, uruchom `oc get all -n prometheus-project` polecenie.

## <a name="add-permissions-to-allow-service-discovery"></a>Dodaj uprawnienia, aby zezwolić na odnajdowanie usług

Utwórz plik Prometheus-sdrole. yml, wprowadzając następującą zawartość:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Aby zastosować szablon do wszystkich projektów, z których chcesz zezwolić na odnajdowanie usług, uruchom następujące polecenia:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Aby sprawdzić, czy rola i Rolabinding zostały utworzone prawidłowo, uruchom `oc get role` `oc get rolebinding` polecenia i.

## <a name="optional-deploy-example-application"></a>Opcjonalne: Wdróż przykładową aplikację

Wszystko działa, ale nie ma źródeł metryk. Przejdź do adresu URL Prometheus ( https://prom-prometheus-project.apps .* Identyfikator losowy*. *region*. azmosa.IO/). Można go znaleźć za pomocą następującego polecenia:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Pamiętaj, aby dodać prefiks https://do początku nazwy hosta.

Na stronie **odnajdywania usługi > stanu** będą wyświetlane aktywne elementy docelowe 0/0.

Aby wdrożyć przykładową aplikację, która udostępnia podstawowe metryki języka Python w punkcie końcowym/Metrics, uruchom następujące polecenia:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Nowe aplikacje powinny być wyświetlane jako prawidłowe elementy docelowe na stronie odnajdowania usługi w ciągu 30 sekund od wdrożenia.

Aby uzyskać więcej szczegółów, **Status**wybierz pozycję  >  **cele**stanu.

> [!NOTE]
> Dla każdego pomyślnie wypadków, Prometheus dodaje punkt danych w metryce w górę. W lewym górnym rogu wybierz pozycję **Prometheus** **, wprowadź wartość w polu** wyrażenie, a następnie wybierz pozycję **Execute (wykonaj**).

## <a name="next-steps"></a>Następne kroki

Do aplikacji można dodawać niestandardowe Instrumentacje Prometheus. Biblioteka kliencka Prometheus, która upraszcza przygotowywanie metryk Prometheus, jest gotowa dla różnych języków programowania.

Aby uzyskać więcej informacji, zobacz następujące biblioteki usługi GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Przejdź](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
