---
title: Alerty metryk z Azure Monitor dla kontenerów
description: W tym artykule opisano zalecane alerty metryk dostępne w programie Azure Monitor for Containers w publicznej wersji zapoznawczej.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 83394faf3d7296522151b815bddd910d47e45d24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619954"
---
# <a name="recommended-metric-alerts-preview-from-azure-monitor-for-containers"></a>Zalecane alerty metryk (wersja zapoznawcza) z Azure Monitor dla kontenerów

Aby otrzymywać alerty dotyczące problemów z zasobami systemu, gdy występują szczytowe zapotrzebowanie i działają blisko pojemności, z Azure Monitor dla kontenerów utworzysz alert dziennika na podstawie danych wydajności przechowywanych w dziennikach Azure Monitor. Azure Monitor dla kontenerów zawiera teraz wstępnie skonfigurowane reguły alertów metryk dla klastra Kubernetes z obsługą usługi Azure ARC, który jest w publicznej wersji zapoznawczej.

W tym artykule omówiono środowisko i przedstawiono wskazówki dotyczące konfigurowania tych reguł alertów i zarządzania nimi.

Jeśli nie masz doświadczenia w Azure Monitor alertów, zobacz [Omówienie alertów w usłudze Microsoft Azure](../platform/alerts-overview.md) przed rozpoczęciem. Aby dowiedzieć się więcej na temat alertów metryk, zobacz [alerty metryk w Azure monitor](../platform/alerts-metric-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem Potwierdź następujące kwestie:

* Metryki niestandardowe są dostępne tylko w ramach podzestawów regionów świadczenia usługi Azure. Lista obsługiwanych regionów jest udokumentowana w [obsługiwanych regionach](../platform/metrics-custom-overview.md#supported-regions).

* Aby zapewnić obsługę alertów dotyczących metryk i wprowadzenia dodatkowych metryk, wymagana minimalna wersja agenta to **Microsoft/OMS: ciprod05262020** for AKS i **Microsoft/OMS: Ciprod09252020** for Azure ARC z włączonym klastrem Kubernetes.

    Aby sprawdzić, czy w klastrze działa nowsza wersja agenta, możesz wykonać następujące działania:

    * Uruchom polecenie: `kubectl describe <omsagent-pod-name> --namespace=kube-system` . W wyświetlonym stanie Zwróć uwagę na wartość w obszarze **obraz** dla omsagent w sekcji *kontenery* w danych wyjściowych. 
    * Na karcie **węzły** wybierz węzeł klastra i w okienku **Właściwości** po prawej stronie Sprawdź wartość w obszarze **tag obrazu agenta**.

    Wartość pokazana dla AKS powinna być w wersji **ciprod05262020** lub nowszej. Wartość pokazana dla klastra Kubernetes z obsługą usługi Azure Arc powinna być w wersji **ciprod09252020** lub nowszej. Jeśli klaster ma starszą wersję, zapoznaj się z tematem [jak uaktualnić agenta Azure monitor for Containers](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster) , aby poznać procedurę pobierania najnowszej wersji.

    Aby uzyskać więcej informacji dotyczących wydania agenta, zobacz [Historia wydania agentów](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). Aby sprawdzić, czy metryki są zbierane, możesz użyć Eksploratora metryk Azure Monitor i sprawdzić, czy w **przestrzeni nazw metryk** znajduje się lista **szczegółowych** informacji. Jeśli tak jest, możesz rozpocząć Konfigurowanie alertów. Jeśli nie widzisz żadnych zebranych metryk, nazwa główna usługi klastra lub plik MSI nie ma wystarczających uprawnień. Aby sprawdzić, czy nazwa SPN lub plik MSI jest członkiem roli **wydawcy metryk monitorowania** , wykonaj kroki opisane w sekcji [uaktualnianie dla klastra za pomocą interfejsu wiersza polecenia platformy Azure](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli) , aby potwierdzić i ustawić przypisanie roli.

## <a name="alert-rules-overview"></a>Przegląd reguł alertów

Aby otrzymywać alerty dotyczące tego, co się stało, Azure Monitor dla kontenerów obejmują następujące alerty metryk dla klastrów Kubernetes z włączonym AKS i Azure ARC:

|Nazwa| Opis |Domyślny próg |
|----|-------------|------------------|
|Średni czas procesora (%) |Oblicza średni czas użycia procesora na kontener.|Gdy średnie użycie procesora CPU na kontener jest większe niż 95%.| 
|Średnia pamięć zestawu roboczego kontenera% |Oblicza średnią ilość pamięci zestawu roboczego użytą na kontener.|Gdy średnie użycie pamięci zestawu roboczego na kontener jest większe niż 95%. |
|Średnie użycie CPU (%) |Oblicza średni czas użycia procesora CPU na węzeł. |Gdy średnie użycie procesora CPU przez węzeł przekracza 80% |
|Średnie użycie dysku (%) |Oblicza średnie użycie dysku dla węzła.|Gdy użycie dysku dla węzła jest większe niż 80%. |
|Średnia pamięć zestawu roboczego% |Oblicza średnią pamięć zestawu roboczego dla węzła. |Gdy średnia ilość pamięci zestawu roboczego dla węzła jest większa niż 80%. |
|Ponowne uruchamianie liczby kontenerów |Oblicza liczbę ponownych uruchomień kontenerów. | Gdy ponowne uruchomienia kontenera są większe niż 0. |
|Liczba zakończonych niepowodzeniem |Oblicza, czy którykolwiek z nich jest w stanie niepowodzenia.|Gdy liczba numerów w stanie niepowodzenia jest większa niż 0. |
|Stan węzła nobieżniing |Oblicza, czy którykolwiek węzeł jest w stanie "nobieżnie".|Gdy liczba węzłów w stanie "nobieżnie" jest większa niż 0. |
|OOM skasowane kontenery |Oblicza liczbę zamkniętych kontenerów OOM. |Gdy liczba zamkniętych kontenerów OOM jest większa niż 0. |
|% Zasobników gotowych |Oblicza średni stan gotowości dla zasobników. |Gdy stan gotowości jest mniejszy niż 80%.|
|Liczba ukończonych zadań |Oblicza liczbę zadań ukończonych ponad sześć godzin temu. |Gdy liczba starych zadań starszych niż 6 godzin jest większa niż 0.|

Istnieją wspólne właściwości dla wszystkich tych reguł alertów:

* Wszystkie reguły alertów są oparte na metrykach.

* Wszystkie reguły alertów są domyślnie wyłączone.

* Wszystkie reguły alertów są oceniane raz na minutę i wyglądają z powrotem w ciągu ostatnich 5 minut danych.

* Reguły alertów nie mają domyślnie przypisanej grupy akcji. Możesz dodać [grupę akcji](../platform/action-groups.md) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji podczas edytowania reguły alertu.

* Możesz zmodyfikować próg reguł alertów, edytując je bezpośrednio. Należy jednak zapoznać się ze wskazówkami podanymi w każdej regule alertu Przed zmodyfikowaniem jej progu.

Następujące metryki oparte na alertach mają unikatowe cechy zachowania w porównaniu z innymi metrykami:

* Metryka *completedJobsCount* jest wysyłana tylko wtedy, gdy istnieją zadania ukończone ponad sześć godzin temu.

* Metryka *containerRestartCount* jest wysyłana tylko wtedy, gdy są ponownie uruchamiane kontenery.

* Metryka *oomKilledContainerCount* jest wysyłana tylko wtedy, gdy istnieją OOM kontenerów.

* metryki *cpuExceededPercentage*, *memoryRssExceededPercentage*i *memoryWorkingSetExceededPercentage* są wysyłane, gdy wartości z zestawu roboczego procesora CPU, pamięci RSS i pamięci przekraczają skonfigurowany próg (domyślny próg to 95%). Te progi mają wyłączny próg warunku alertu określony dla odpowiedniej reguły alertu. Znaczenie, jeśli chcesz zbierać te metryki i analizować je za pomocą [Eksploratora metryk](../platform/metrics-getting-started.md), zalecamy skonfigurowanie progu do wartości mniejszej niż wartość progowa alertu. Konfiguracja dotycząca ustawień kolekcji dla ich progów wykorzystania zasobów kontenera może zostać przesłonięta w pliku ConfigMaps w sekcji `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` . Aby uzyskać szczegółowe informacje dotyczące konfigurowania pliku konfiguracji ConfigMap, zobacz sekcję [Konfigurowanie metryk alertów ConfigMaps](#configure-alertable-metrics-in-configmaps) .

## <a name="metrics-collected"></a>Zebrane metryki

Następujące metryki są włączone i zbierane, o ile nie określono inaczej, w ramach tej funkcji:

|Przestrzeń nazw metryki |Metryka |Opis |
|---------|----|------------|
|Szczegółowe informacje. kontenery/węzły |cpuUsageMillicores |Użycie procesora CPU w millicores przez hosta.|
|Szczegółowe informacje. kontenery/węzły |cpuUsagePercentage |Procent użycia procesora CPU według węzła.|
|Szczegółowe informacje. kontenery/węzły |memoryRssBytes |Wykorzystanie pamięci RSS w bajtach przez hosta.|
|Szczegółowe informacje. kontenery/węzły |memoryRssPercentage |Procent użycia pamięci RSS przez hosta.|
|Szczegółowe informacje. kontenery/węzły |memoryWorkingSetBytes |Użycie zestawu roboczego pamięci w bajtach przez hosta.|
|Szczegółowe informacje. kontenery/węzły |memoryWorkingSetPercentage |Procent użycia zestawu roboczego pamięci wg hosta.|
|Szczegółowe informacje. kontenery/węzły |nodesCount |Liczba węzłów według stanu.|
|Szczegółowe informacje. kontenery/węzły |diskUsedPercentage |Procent dysku używanego w węźle przez urządzenie.|
|Szczegółowe informacje. kontenery/zasobniki |podCount |Liczba zliczeń według kontrolera, przestrzeni nazw, węzła i fazy.|
|Szczegółowe informacje. kontenery/zasobniki |completedJobsCount |Liczba ukończonych zadań: próg konfigurowalnego starszego użytkownika (domyślnie sześć godzin) przez kontroler, Kubernetes przestrzeń nazw. |
|Szczegółowe informacje. kontenery/zasobniki |restartingContainerCount |Liczba ponownych uruchomień kontenera przez kontroler, Kubernetes przestrzeń nazw.|
|Szczegółowe informacje. kontenery/zasobniki |oomKilledContainerCount |Liczba kontenerów OOMkilled według kontrolera, Kubernetes przestrzeni nazw.|
|Szczegółowe informacje. kontenery/zasobniki |podReadyPercentage |Procent wartości w stanie gotowe przez kontroler, Kubernetes przestrzeń nazw.|
|Szczegółowe informacje. kontenery/kontenery |cpuExceededPercentage |Procent użycia procesora CPU w przypadku kontenerów przekraczających wartość progową konfigurowalną przez użytkownika (wartość domyślna to 95,0) według nazwy kontenera, nazwy kontrolera, Kubernetes nazw, pod nazwą.<br> Gromadzenia  |
|Szczegółowe informacje. kontenery/kontenery |memoryRssExceededPercentage |Wartość procentowa RSS pamięci dla kontenerów przekraczająca wartość progową konfigurowalną przez użytkownika (wartość domyślna to 95,0) według nazwy kontenera, nazwy kontrolera, Kubernetes przestrzeni nazw, pod nazwą.|
|Szczegółowe informacje. kontenery/kontenery |memoryWorkingSetExceededPercentage |Procent zestawu roboczego pamięci dla kontenerów przekraczających wartość progową konfigurowalną przez użytkownika (wartość domyślna to 95,0) według nazwy kontenera, nazwy kontrolera, Kubernetes przestrzeni nazw, pod nazwą.|

## <a name="enable-alert-rules"></a>Włącz reguły alertów

Wykonaj następujące kroki, aby włączyć alerty metryk w Azure Monitor z Azure Portal. Aby włączyć korzystanie z szablonu Menedżer zasobów, zobacz [Włączanie przy użyciu szablonu Menedżer zasobów](#enable-with-a-resource-manager-template).

### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

Ta sekcja zawiera szczegółowe instrukcje dotyczące włączania alertu metryki Azure Monitor dla kontenerów (wersja zapoznawcza) z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Dostęp do funkcji alertu metryki usługi Azure Monitor dla kontenerów (wersja zapoznawcza) jest dostępny bezpośrednio w klastrze AKS, wybierając pozycję **szczegółowe** dane w okienku po lewej stronie Azure Portal.

3. Na pasku poleceń wybierz pozycję **zalecane alerty**.

    ![Opcja zalecane alerty w Azure Monitor dla kontenerów](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. Okienko właściwości **zalecane alerty** zostanie automatycznie wyświetlone po prawej stronie. Domyślnie wszystkie reguły alertów na liście są wyłączone. Po wybraniu opcji **Włącz**reguła alertu zostanie utworzona, a nazwa reguły zostanie zaktualizowana w celu uwzględnienia linku do zasobu alertu.

    ![Okienko właściwości zalecane alerty](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    Po wybraniu przełącznika **enable/disable** w celu włączenia alertu zostanie utworzona reguła alertu, a nazwa reguły zostanie zaktualizowana w celu uwzględnienia linku do rzeczywistego zasobu alertu.

    ![Włącz regułę alertu](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. Reguły alertów nie są skojarzone z [grupą akcji](../platform/action-groups.md) w celu powiadomienia użytkowników o wyzwoleniu alertu. Wybierz pozycję **nie przypisano żadnej grupy akcji** i na stronie **grupy akcji** Określ istniejącą lub Utwórz grupę akcji, wybierając pozycję **Dodaj** lub **Utwórz**.

    ![Wybierz grupę akcji](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>Włączanie przy użyciu szablonu Menedżer zasobów

Można użyć Azure Resource Manager szablonu i pliku parametrów do utworzenia uwzględnionych alertów metryk w Azure Monitor.

Podstawowe kroki są następujące:

1. Pobierz jeden lub wszystkie dostępne szablony opisujące sposób tworzenia alertu z usługi [GitHub](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM).

2. Utwórz [plik parametrów](../../azure-resource-manager/templates/parameter-files.md) w formacie JSON i użyj go w celu ustawienia wartości wymaganych do utworzenia reguły alertu.

3. Wdróż szablon na podstawie Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="deploy-through-azure-portal"></a>Wdrażanie za Azure Portal

1. Pobierz i Zapisz w folderze lokalnym, Azure Resource Manager szablonu i pliku parametrów, aby utworzyć regułę alertu przy użyciu następujących poleceń:

2. Aby wdrożyć dostosowany szablon za pośrednictwem portalu, wybierz pozycję **Utwórz zasób** na podstawie [Azure Portal](https://portal.azure.com).

3. Wyszukaj **szablon**, a następnie wybierz pozycję **Template Deployment**.

4. Wybierz przycisk **Utwórz**.

5. Zobaczysz kilka opcji tworzenia szablonu, wybierz opcję **Kompiluj własny szablon w edytorze**.

6. Na **stronie Edytuj szablon**wybierz pozycję **Załaduj plik** , a następnie wybierz plik szablonu.

7. Na stronie **Edytuj szablon** wybierz pozycję **Zapisz**.

8. Na stronie **wdrożenie niestandardowe** określ poniższe opcje, a następnie po zakończeniu wybierz pozycję **Kup** , aby wdrożyć szablon i utworzyć regułę alertu.

    * Grupa zasobów
    * Lokalizacja
    * Nazwa alertu
    * Identyfikator zasobu klastra

#### <a name="deploy-with-azure-powershell-or-cli"></a>Wdrażanie przy użyciu Azure PowerShell lub interfejsu wiersza polecenia

1. Pobierz i Zapisz w folderze lokalnym, Azure Resource Manager szablonu i pliku parametrów, aby utworzyć regułę alertu przy użyciu następujących poleceń:

2. Alert dotyczący metryki można utworzyć przy użyciu pliku szablonu i parametrów przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

    Korzystanie z programu Azure PowerShell

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    Korzystanie z interfejsu wiersza polecenia platformy Azure

    ```azurecli
    az login

    az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >Alert dotyczący metryki można utworzyć w innej grupie zasobów dla zasobu docelowego, dlatego zalecamy użycie tej samej grupy zasobów co zasób docelowy.

## <a name="edit-alert-rules"></a>Edytuj reguły alertów

Można wyświetlać reguły alertów Azure Monitor dla kontenerów i zarządzać nimi, aby edytować jego próg lub skonfigurować [grupę akcji](../platform/action-groups.md) dla klastra AKS. Chociaż można wykonać te działania z Azure Portal i interfejsu wiersza polecenia platformy Azure, można to zrobić również bezpośrednio z klastra AKS w Azure Monitor for Containers.

1. Na pasku poleceń wybierz pozycję **zalecane alerty**.

2. Aby zmodyfikować próg, w okienku **zalecane alerty** wybierz opcję włączony alert. W obszarze **Edytuj regułę**wybierz **kryteria alertów** , które chcesz edytować.

    * Aby zmodyfikować próg reguły alertu, wybierz **warunek**.
    * Aby określić istniejącą lub utworzyć grupę akcji, wybierz opcję **Dodaj** lub **Utwórz** w obszarze **Grupa akcji**

Aby wyświetlić alerty utworzone dla zasad włączonych, w okienku **zalecane alerty** wybierz pozycję **Wyświetl w obszarze alerty**. Nastąpi przekierowanie do menu alertu dla klastra AKS, w którym można zobaczyć wszystkie alerty, które są aktualnie utworzone dla danego klastra.

## <a name="configure-alertable-metrics-in-configmaps"></a>Konfigurowanie metryk z alertami w ConfigMaps

Wykonaj następujące kroki, aby skonfigurować plik konfiguracyjny ConfigMap w celu zastąpienia domyślnych progów wykorzystania zasobów kontenera. Te kroki dotyczą tylko następujących metryk z alertami.

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*

1. Edytuj plik YAML ConfigMap w sekcji `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` .

2. Aby zmodyfikować próg *cpuExceededPercentage* na 90% i rozpocząć zbieranie tej metryki po spełnieniu i przekroczeniu tego progu, Skonfiguruj plik ConfigMap przy użyciu poniższego przykładu.

    ```
    container_cpu_threshold_percentage = 90.0
    # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
    container_memory_rss_threshold_percentage = 95.0
    # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
    container_memory_working_set_threshold_percentage = 95.0
    ```

3. Uruchom następujące polecenie polecenia kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` .

    Przykład: `kubectl apply -f container-azm-ms-agentconfig.yaml`.

Zmiana konfiguracji może potrwać kilka minut, zanim zostanie ona uwzględniona, a wszystkie omsagent zostaną uruchomione ponownie. Ponowne uruchomienie jest ponownym uruchomieniem dla wszystkich omsagentch, a nie wszystkich ponownych uruchomień w tym samym czasie. Po zakończeniu ponownych uruchomień zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik: `configmap "container-azm-ms-agentconfig" created` .

## <a name="next-steps"></a>Następne kroki

- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby zobaczyć wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do tworzenia alertów, wizualizacji lub analizowania klastrów.

- Aby dowiedzieć się więcej na temat Azure Monitor i sposobu monitorowania innych aspektów klastra Kubernetes, zobacz [View Kubernetesing Performance Cluster](container-insights-analyze.md).
