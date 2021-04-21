---
title: Zbieranie & analizowania dzienników zasobów
description: Dowiedz się, jak wysyłać dzienniki zasobów i dane zdarzeń z grup kontenerów Azure Container Instances do Azure Monitor dzienników
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: e46a1df65a4cfe5d10a58704aff485aa2834b55f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763923"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Rejestrowanie grup kontenerów i wystąpień przy użyciu Azure Monitor kontenerów

Obszary robocze usługi Log Analytics zapewniają scentralizowaną lokalizację do przechowywania i wykonywania zapytań dotyczących danych dzienników nie tylko z zasobów platformy Azure, ale także zasobów lokalnych i zasobów w innych chmurach. Azure Container Instances wbudowaną obsługę wysyłania dzienników i danych zdarzeń do Azure Monitor dzienników.

Aby wysłać dziennik grupy kontenerów i dane zdarzeń do Azure Monitor, określ istniejący identyfikator obszaru roboczego usługi Log Analytics i klucz obszaru roboczego podczas konfigurowania grupy kontenerów. 

W poniższych sekcjach opisano sposób tworzenia grupy kontenerów z włączonym rejestrowaniem oraz wykonywanie zapytań o dzienniki. Możesz również [zaktualizować grupę kontenerów przy](container-instances-update.md) użyciu identyfikatora obszaru roboczego i klucza obszaru roboczego, aby włączyć rejestrowanie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Obecnie dane zdarzeń z wystąpień kontenerów systemu Linux można wysyłać tylko do usługi Log Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć rejestrowanie w wystąpieniach kontenera, potrzebne są następujące elementy:

* [Obszar roboczy usługi Log Analytics](../azure-monitor/logs/quick-create-workspace.md)
* [Interfejs poziomu wywołania platformy Azure](/cli/azure/install-azure-cli) (lub usługa [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>Uzyskiwanie poświadczeń usługi Log Analytics

Usługa Azure Container Instances wymaga uprawnień do wysyłania danych do obszaru roboczego usługi Log Analytics. Aby nadać to uprawnienie i włączyć rejestrowanie, należy podać identyfikator obszaru roboczego usługi Log Analytics i jeden z jego kluczy (podstawowy lub pomocniczy) podczas tworzenia grupy kontenerów.

Aby uzyskać identyfikator obszaru roboczego analizy dzienników i klucz podstawowy:

1. W witrynie Azure Portal przejdź do obszaru roboczego usługi Log Analytics
1. W **obszarze Ustawienia** wybierz pozycję Zarządzanie **agentami**
1. Zwróć uwagę na następujące elementy:
   * **Identyfikator obszaru roboczego**
   * **Klucz podstawowy**

## <a name="create-container-group"></a>Tworzenie grupy kontenerów

Teraz, gdy masz identyfikator obszaru roboczego analizy dzienników i klucz podstawowy, możesz utworzyć grupę kontenerów z włączonym rejestrowaniem.

W poniższych przykładach pokazano dwa sposoby tworzenia grupy kontenerów, która składa się z jednego kontenera [fluentd:][fluentd] interfejs wiersza polecenia platformy Azure i interfejs wiersza polecenia platformy Azure przy użyciu szablonu YAML. Kontener fluentd tworzy kilka wierszy danych wyjściowych w konfiguracji domyślnej. Ponieważ te dane wyjściowe są wysyłane do obszaru roboczego usługi Log Analytics, są przydatne do przedstawiania wyświetlania i wykonywania zapytań przez dzienniki.

### <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby przeprowadzić wdrożenie przy użyciu wiersza polecenia platformy Azure, należy określić parametry `--log-analytics-workspace` i `--log-analytics-workspace-key` w poleceniu [az container create][az-container-create]. Przed uruchomieniem następującego polecenia należy zastąpić te dwie wartości obszaru roboczego wartościami uzyskanymi w poprzednim kroku (i zaktualizować nazwę grupy zasobów).

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Wdrażanie za pomocą kodu YAML

Użyj tej metody, jeśli chcesz wdrożyć grupy kontenerów za pomocą kodu YAML. Poniższy kod YAML definiuje grupę kontenerów z jednym kontenerem. Skopiuj kod YAML do nowego pliku, a następnie zastąp parametry `LOG_ANALYTICS_WORKSPACE_ID` i `LOG_ANALYTICS_WORKSPACE_KEY` przy użyciu wartości uzyskanych w poprzednim kroku. Zapisz plik jako **deploy-aci.yaml**.

```yaml
apiVersion: 2019-12-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Następnie wykonaj następujące polecenie, aby wdrożyć grupę kontenerów. Zastąp `myResourceGroup` wartością grupy zasobów w subskrypcji (lub najpierw utwórz grupę zasobów o nazwie "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Zaraz po wykonaniu polecenia powinna pojawić się odpowiedź z usługi Azure zawierająca szczegóły wdrożenia.

## <a name="view-logs"></a>Wyświetlanie dzienników

Po wdrożeniu grupy kontenerów, może upłynąć kilka minut (do 10), zanim pierwsze wpisy dziennika będą widoczne w witrynie Azure Portal. 

Aby wyświetlić dzienniki grupy kontenerów w `ContainerInstanceLog_CL` tabeli:

1. W witrynie Azure Portal przejdź do obszaru roboczego usługi Log Analytics
1. W **obszarze Ogólne** wybierz pozycję **Dzienniki**  
1. Wpisz następujące zapytanie: `ContainerInstanceLog_CL | limit 50`
1. Wybierz pozycję **Uruchom**

Zapytanie powinno wyświetlić kilka wyników. Jeśli na początku nie widzisz żadnych wyników, zaczekaj  kilka minut, a następnie wybierz przycisk Uruchom, aby ponownie wykonać zapytanie. Domyślnie wpisy dziennika są wyświetlane w **formacie** tabeli. Następnie możesz rozwijać wiersze, aby wyświetlić zawartość poszczególnych wpisów dziennika.

![Wyniki przeszukiwania dzienników w witrynie Azure Portal][log-search-01]

## <a name="view-events"></a>Wyświetlanie zdarzeń

Zdarzenia dla wystąpień kontenerów można również wyświetlać w Azure Portal. Zdarzenia obejmują czas utworzenia wystąpienia i czas jego rozpoczęcia. Aby wyświetlić dane zdarzenia w `ContainerEvent_CL` tabeli:

1. W witrynie Azure Portal przejdź do obszaru roboczego usługi Log Analytics
1. W **obszarze Ogólne** wybierz pozycję **Dzienniki**  
1. Wpisz następujące zapytanie: `ContainerEvent_CL | limit 50`
1. Wybierz pozycję **Uruchom**

Zapytanie powinno wyświetlić kilka wyników. Jeśli na początku nie widzisz żadnych wyników, zaczekaj  kilka minut, a następnie wybierz przycisk Uruchom, aby ponownie wykonać zapytanie. Domyślnie wpisy są wyświetlane w **formacie** tabeli. Następnie możesz rozwinąć wiersz, aby wyświetlić zawartość pojedynczego wpisu.

![Wyniki wyszukiwania zdarzeń w Azure Portal][log-search-02]

## <a name="query-container-logs"></a>Wykonywanie zapytań dla dzienników kontenerów

Dzienniki usługi Azure Monitor obejmują rozszerzony [język zapytań][query_lang] do ściągania informacji nawet z tysięcy wierszy danych wyjściowych dziennika.

Podstawową strukturą zapytania jest tabela źródłowa (w tym artykule lub ), po której następuje seria operatorów oddzielonych znakiem `ContainerInstanceLog_CL` `ContainerEvent_CL` potoku ( `|` ). Można połączyć kilka operatorów, aby dostosować wyniki i wykonać funkcje zaawansowane.

Aby wyświetlić przykładowe wyniki zapytania, wklej następujące zapytanie w polu tekstowym zapytania i wybierz przycisk **Uruchom,** aby wykonać zapytanie. To zapytanie wyświetla wszystkie wpisy dziennika, których pole „Message” zawiera słowo "warn":

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

Bardziej złożone zapytania są również obsługiwane. Na przykład to zapytanie wyświetla tylko wpisy dziennika dla grupy kontenerów "mycontainergroup001" wygenerowane w ciągu ostatniej godziny:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Następne kroki

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Aby uzyskać więcej informacji dotyczących wykonywania zapytań dla dzienników i konfigurowania alertów w dziennikach usługi Azure Monitor, zobacz:

* [Omówienie przeszukiwania dzienników w Azure Monitor dziennikach](../azure-monitor/logs/log-query-overview.md)
* [Unified alerts in Azure Monitor](../azure-monitor/alerts/alerts-overview.md) (Ujednolicone alerty na platformie Azure Monitor)


### <a name="monitor-container-cpu-and-memory"></a>Monitorowanie procesora i pamięci kontenera

Aby uzyskać informacje na temat monitorowania procesora wystąpienia i zasobów pamięci kontenera, zobacz temat:

* [Monitorowanie zasobów kontenerów w usłudze Azure Container Instances](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: /azure/data-explorer/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create