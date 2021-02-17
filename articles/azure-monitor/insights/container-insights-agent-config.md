---
title: Konfigurowanie Azure Monitor na potrzeby zbierania danych przez agentów kontenerów | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania Azure Monitor dla agenta kontenerów w celu sterowania kolekcją strumienia stdout/stderr i zmiennych środowiskowych.
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: f21b841bc129012b684d2a1c59eb72989fe9e0e0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561729"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurowanie zbierania danych agenta dla usługi Azure Monitor kontenerów

Azure Monitor kontenerów zbiera zmienne stdout, stderr i środowiskowe z obciążeń kontenera wdrożonych do zarządzanych klastrów Kubernetes z poziomu agenta kontenera. Ustawienia zbierania danych agentów można skonfigurować, tworząc niestandardowe Kubernetes ConfigMaps w celu kontrolowania tego środowiska. 

W tym artykule pokazano, jak utworzyć ConfigMap i skonfigurować zbieranie danych zgodnie z wymaganiami.

>[!NOTE]
>W przypadku usługi Azure Red Hat OpenShift plik szablonu ConfigMap jest tworzony w przestrzeni nazw *OpenShift-Azure-Logging* . 
>

## <a name="configmap-file-settings-overview"></a>ConfigMap — Omówienie ustawień plików

Udostępniany jest plik ConfigMap szablonu, który umożliwia łatwe edytowanie go przy użyciu dostosowań bez konieczności tworzenia go od podstaw. Przed rozpoczęciem należy zapoznać się z dokumentacją Kubernetes dotyczącą [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) i zapoznać się z tematem jak tworzyć, konfigurować i wdrażać ConfigMaps. Pozwoli to na filtrowanie stderr i stdout dla przestrzeni nazw lub całego klastra oraz zmiennych środowiskowych dla dowolnego kontenera uruchomionego we wszystkich/wszystkich węzłach w klastrze.

>[!IMPORTANT]
>Minimalna wersja agenta obsługiwana w celu zbierania zmiennych stdout, stderr i środowiskowych z obciążeń kontenerów to ciprod06142019 lub nowszy. Aby sprawdzić wersję agenta, z karty **węzeł** wybierz węzeł, a następnie w okienku właściwości wartość komentarza właściwości **tag obrazu agenta** . Aby uzyskać dodatkowe informacje na temat wersji agenta i elementów uwzględnionych w każdej wersji, zobacz [Informacje o wersji agenta](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Ustawienia zbierania danych

W poniższej tabeli opisano ustawienia, które można skonfigurować w celu kontrolowania zbierania danych:

| Klucz | Typ danych | Wartość | Opis |
|--|--|--|--|
| `schema-version` | Ciąg (z uwzględnieniem wielkości liter) | wersjach | Jest to wersja schematu używana przez agenta<br> podczas analizowania tego ConfigMap.<br> Obecnie obsługiwana wersja schematu to v1.<br> Modyfikowanie tej wartości nie jest obsługiwane i będzie<br> odrzucono, gdy ConfigMap jest oceniane. |
| `config-version` | Ciąg |  | Program obsługuje możliwość śledzenia wersji tego pliku konfiguracji w systemie/repozytorium kontroli źródła.<br> Maksymalna dozwolona liczba znaków wynosi 10, a wszystkie inne znaki są obcinane. |
| `[log_collection_settings.stdout] enabled =` | Wartość logiczna | true lub false | Ta funkcja kontroluje, czy jest włączone zbieranie dzienników strumienia stdout. Gdy jest ustawiona na `true` i żadne obszary nazw nie są wykluczone dla zbierania dzienników stdout<br> ( `log_collection_settings.stdout.exclude_namespaces` ustawienie poniżej) dzienniki stdout będą zbierane ze wszystkich kontenerów we wszystkich węzłach w klastrze. Jeśli nie zostanie określony w ConfigMaps,<br> wartość domyślna to `enabled = true` . |
| `[log_collection_settings.stdout] exclude_namespaces =` | Ciąg | Tablica rozdzielona przecinkami | Tablica przestrzeni nazw Kubernetes, dla których dzienniki stdout nie będą zbierane. To ustawienie ma zastosowanie tylko wtedy, gdy<br> `log_collection_settings.stdout.enabled`<br> jest ustawiony na `true` .<br> Jeśli nie zostanie określony w ConfigMap, wartość domyślna to<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Wartość logiczna | true lub false | Ta funkcja kontroluje, czy jest włączona kolekcja dzienników kontenera stderr.<br> Gdy jest ustawiona na `true` i żadne obszary nazw nie są wykluczone dla zbierania dzienników stdout<br> ( `log_collection_settings.stderr.exclude_namespaces` ustawienie) dzienniki stderr będą zbierane ze wszystkich kontenerów w ramach wszystkich zasobników/węzłów w klastrze.<br> Jeśli nie zostanie określony w ConfigMaps, wartość domyślna to<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | Ciąg | Tablica rozdzielona przecinkami | Tablica przestrzeni nazw Kubernetes, dla których dzienniki stderr nie będą zbierane.<br> To ustawienie ma zastosowanie tylko wtedy, gdy<br> `log_collection_settings.stdout.enabled` jest ustawiony na `true` .<br> Jeśli nie zostanie określony w ConfigMap, wartość domyślna to<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Wartość logiczna | true lub false | To ustawienie steruje kolekcją zmiennych środowiskowych<br> między wszystkimi zasobnikami/węzłami w klastrze<br> i wartości domyślne do, `enabled = true` gdy nie zostanie określony<br> w ConfigMaps.<br> Jeśli kolekcja zmiennych środowiskowych jest włączona globalnie, można ją wyłączyć dla określonego kontenera<br> przez ustawienie zmiennej środowiskowej<br> `AZMON_COLLECT_ENV` na **wartość false** przy użyciu ustawienia pliku dockerfile lub w [pliku konfiguracji dla elementu pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) w sekcji **env:** .<br> Jeśli zbieranie zmiennych środowiskowych jest wyłączone globalnie, nie można włączyć kolekcji dla określonego kontenera (oznacza to, że jedynym przesłonięciem, który można zastosować na poziomie kontenera, jest wyłączenie kolekcji, gdy jest ona już włączona globalnie). |
| `[log_collection_settings.enrich_container_logs] enabled =` | Wartość logiczna | true lub false | To ustawienie kontroluje wzbogacanie dziennika kontenera w celu wypełnienia wartości nazwy i właściwości obrazu<br> dla każdego rekordu dziennika zapisanego w tabeli ContainerLog dla wszystkich dzienników kontenerów w klastrze.<br> Domyślnie nie jest `enabled = false` określony w ConfigMap. |
| `[log_collection_settings.collect_all_kube_events]` | Wartość logiczna | true lub false | To ustawienie umożliwia zbieranie zdarzeń polecenia dla wszystkich typów.<br> Domyślnie zdarzenia polecenia z typem *Normal* nie są zbierane. Gdy to ustawienie jest ustawione na `true` , *normalne* zdarzenia nie są już filtrowane i są zbierane wszystkie zdarzenia.<br> Domyślnie jest to ustawienie `false` . |

### <a name="metric-collection-settings"></a>Ustawienia zbierania metryk

W poniższej tabeli opisano ustawienia, które można skonfigurować w celu kontrolowania kolekcji metryk:

| Klucz | Typ danych | Wartość | Opis |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Wartość logiczna | true lub false | To ustawienie umożliwia zbieranie metryk użycia trwałych woluminów (PV) w przestrzeni nazw polecenia-system. Domyślnie metryki użycia dla woluminów trwałych z trwałymi oświadczeniami woluminów w przestrzeni nazw polecenia-system nie są zbierane. Gdy to ustawienie jest ustawione na wartość `true` , zbierane są metryki użycia PV dla wszystkich przestrzeni nazw. Domyślnie jest to ustawienie `false` . |

ConfigMaps jest globalną listą, a do agenta może być zastosowany tylko jeden ConfigMap. Nie można ConfigMaps kolekcji.

## <a name="configure-and-deploy-configmaps"></a>Konfigurowanie i wdrażanie ConfigMaps

Wykonaj następujące kroki, aby skonfigurować i wdrożyć plik konfiguracyjny ConfigMap w klastrze.

1. Pobierz [plik Template CONFIGMAP YAML](https://aka.ms/container-azm-ms-agentconfig) i Zapisz go jako Container-AZM-MS-agentconfig. YAML. 

   > [!NOTE]
   > Ten krok nie jest wymagany podczas pracy z usługą Azure Red Hat OpenShift, ponieważ szablon ConfigMap już istnieje w klastrze.

2. Edytuj plik YAML ConfigMap przy użyciu dostosowań, aby zbierać zmienne stdout, stderr i/lub środowiskowe. Jeśli edytujesz plik ConfigMap YAML dla usługi Azure Red Hat OpenShift, najpierw uruchom polecenie, `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Aby otworzyć plik w edytorze tekstu.

    - Aby wykluczyć określone przestrzenie nazw dla zbierania dzienników stdout, należy skonfigurować klucz/wartość przy użyciu następującego przykładu: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]` .
    
    - Aby wyłączyć kolekcję zmiennych środowiskowych dla określonego kontenera, należy ustawić klucz/wartość, `[log_collection_settings.env_var] enabled = true` Aby włączyć kolekcje zmiennych globalnie, a następnie wykonać kroki opisane [tutaj](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) , aby ukończyć konfigurację dla określonego kontenera.
    
    - Aby wyłączyć zbieranie dzienników stderr dla całego klastra, należy skonfigurować klucz/wartość przy użyciu następującego przykładu: `[log_collection_settings.stderr] enabled = false` .

3. W przypadku klastrów innych niż Azure Red Hat OpenShift Utwórz ConfigMap, uruchamiając następujące polecenie polecenia kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` w przypadku klastrów innych niż Azure Red Hat OpenShift. 
    
    Przykład: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    W przypadku usługi Azure Red Hat OpenShift Zapisz zmiany w edytorze.

Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" created` .

## <a name="verify-configuration"></a>Weryfikuj konfigurację

Aby sprawdzić, czy konfiguracja została pomyślnie zastosowana do klastra innego niż Azure Red Hat OpenShift, użyj następującego polecenia, aby przejrzeć dzienniki z agenta pod: `kubectl logs omsagent-fdf58 -n kube-system` . Jeśli wystąpiły błędy konfiguracji z omsagentch, w danych wyjściowych zostaną wyświetlone błędy podobne do następujących:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Błędy związane z zastosowaniem zmian konfiguracji są również dostępne do przeglądu. Następujące opcje są dostępne, aby wykonać dodatkowe Rozwiązywanie problemów z zmianami konfiguracji:

- Z dzienników agenta pod użyciem tego samego `kubectl logs` polecenia. 

    >[!NOTE]
    >To polecenie nie ma zastosowania do klastra Red Hat OpenShift platformy Azure.
    > 

- Z dzienników na żywo. Dzienniki na żywo pokazują błędy podobne do następujących:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Z tabeli **KubeMonAgentEvents** w obszarze roboczym log Analytics. Dane są wysyłane co godzinę z ważnością *błędu* w przypadku błędów konfiguracji. Jeśli nie ma żadnych błędów, wpis w tabeli będzie zawierał dane z *informacjami* o ważności, które nie zgłaszają błędów. Właściwość **Tags** zawiera więcej informacji na temat identyfikatora kontenera i, na których wystąpił błąd, a także pierwszego wystąpienia, ostatniego wystąpienia i liczby w ciągu ostatniej godziny.

- Korzystając z rozwiązania Azure Red Hat OpenShift, Sprawdź dzienniki omsagent, przeszukując tabelę **ContainerLog** , aby sprawdzić, czy jest włączone zbieranie dzienników na platformie Azure.

Po naprawieniu błędów w ConfigMap klastrów innych niż Azure Red Hat OpenShift Zapisz plik YAML i Zastosuj zaktualizowany ConfigMaps, uruchamiając polecenie: `kubectl apply -f <configmap_yaml_file.yaml` . W przypadku rozwiązania Azure Red Hat OpenShift należy edytować i zapisać zaktualizowane ConfigMaps, uruchamiając polecenie:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Stosowanie zaktualizowanych ConfigMap

Jeśli wdrożono już ConfigMap w klastrach innych niż Azure Red Hat OpenShift i chcesz ją zaktualizować przy użyciu nowszej konfiguracji, możesz edytować poprzednio używany plik ConfigMap, a następnie zastosować go przy użyciu tego samego polecenia jak poprzednio `kubectl apply -f <configmap_yaml_file.yaml` . W przypadku rozwiązania Azure Red Hat OpenShift należy edytować i zapisać zaktualizowane ConfigMaps, uruchamiając polecenie:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verifying-schema-version"></a>Weryfikowanie wersji schematu

Obsługiwane wersje schematu konfiguracji są dostępne jako adnotacja pod (wersje schematu) na omsagent pod. Można je wyświetlić za pomocą następującego polecenia polecenia kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Dane wyjściowe będą wyświetlane podobnie jak w przypadku następujących wersji schematu adnotacji:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Następne kroki

- Azure Monitor kontenerów nie zawiera wstępnie zdefiniowanego zestawu alertów. Zapoznaj się z tematem [tworzenie alertów dotyczących wydajności za pomocą Azure monitor dla kontenerów](./container-insights-log-alerts.md) , aby dowiedzieć się, jak utworzyć zalecane alerty dotyczące wysokiego użycia procesora i pamięci, aby zapewnić obsługę procesów i procedur operacyjnych DevOps.

- Po włączeniu monitorowania w celu zbierania informacji o kondycji i użyciu zasobów AKS lub hybrydowego klastra oraz obciążeń działających na nich można dowiedzieć się, [jak używać](container-insights-analyze.md) Azure monitor do kontenerów.

- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby zobaczyć wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do tworzenia alertów, wizualizacji lub analizowania klastrów.