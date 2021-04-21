---
title: Samouczek — planowanie zadania ACR
description: W tym samouczku dowiesz się, jak uruchomić zadanie Azure Container Registry zgodnie ze zdefiniowanym harmonogramem, ustawiając co najmniej jeden wyzwalacz czasomierza
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: fa80bcbd318266a86c5bec08c9ee60fc0d22a10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780862"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Samouczek: uruchamianie zadania ACR zgodnie ze zdefiniowanym harmonogramem

W tym samouczku pokazano, jak uruchamiać zadanie [ACR zgodnie](container-registry-tasks-overview.md) z harmonogramem. Zaplanuj zadanie, ustawiając co najmniej jeden *wyzwalacz czasomierza*. Wyzwalaczy czasomierza można używać samodzielnie lub w połączeniu z innymi wyzwalaczami zadań.

Z tego samouczka dowiesz się więcej o planowaniu zadań i:

> [!div class="checklist"]
> * Tworzenie zadania z wyzwalaczem czasomierza
> * Zarządzanie wyzwalaczami czasomierza

Planowanie zadania jest przydatne w następujących scenariuszach:

* Uruchamianie obciążenia kontenera na potrzeby zaplanowanych operacji konserwacji. Na przykład uruchom konteneryzowana aplikację, aby usunąć niepotrzebnych obrazów z rejestru.
* Uruchamianie zestawu testów na obrazie produkcyjnym w trakcie dnia roboczego w ramach monitorowania witryny na żywo.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Informacje o planowaniu zadania

* **Wyzwalacz z wyrażeniem cron** — wyzwalacz czasomierza dla zadania używa wyrażenia *cron*. Wyrażenie jest ciągiem z pięcioma polami określającymi minutę, godzinę, dzień, miesiąc i dzień tygodnia wyzwalające zadanie. Obsługiwane są częstotliwości maksymalnie raz na minutę.

  Na przykład wyrażenie wyzwala zadanie w południe czasu `"0 12 * * Mon-Fri"` UTC w każdym dniu tygodnia. Szczegółowe [informacje można](#cron-expressions) znaleźć w dalszej części tego artykułu.
* **Wiele wyzwalaczy czasomierza** — dodawanie wielu czasomierzy do zadania jest dozwolone, o ile harmonogramy się różnią.
    * Określ wiele wyzwalaczy czasomierza podczas tworzenia zadania lub dodaj je później.
    * Opcjonalnie nadaj wyzwalaczom nazwę, aby ułatwić zarządzanie, lub zadania usługi ACR domyślne nazwy wyzwalaczy.
    * Jeśli harmonogram czasomierza nakłada się na siebie w zadania usługi ACR wyzwala zadanie w zaplanowanym czasie dla każdego czasomierza.
* **Inne wyzwalacze zadań —** w zadaniu wyzwalanych czasomierzem [](container-registry-tutorial-build-task.md) można również włączyć wyzwalacze oparte na zatwierdzeniu kodu źródłowego lub aktualizacji [obrazu podstawowego.](container-registry-tutorial-base-image-update.md) Podobnie jak w przypadku innych zadań ACR, zaplanowane zadanie [można również][az-acr-task-run] uruchomić ręcznie.

## <a name="create-a-task-with-a-timer-trigger"></a>Tworzenie zadania z wyzwalaczem czasomierza

### <a name="task-command"></a>Task , polecenie

Najpierw wypełnij następującą zmienną środowiskową powłoki wartością odpowiednią dla Twojego środowiska. Ten krok nie jest ściśle wymagany, ale trochę ułatwia wykonywanie przedstawionych w tym samouczku wielowierszowych poleceń interfejsu wiersza polecenia platformy Azure. Jeśli nie wypełnisz zmiennej środowiskowej, musisz ręcznie zastąpić każdą wartość wszędzie tam, gdzie jest wyświetlana w przykładowych poleceniach.

[![Uruchamianie osadzane](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

Podczas tworzenia zadania za pomocą polecenia [az acr task create][az-acr-task-create] możesz opcjonalnie dodać wyzwalacz czasomierza. Dodaj parametr `--schedule` i przekaż wyrażenie cron dla czasomierza.

W prostym przykładzie następujące zadanie wyzwala uruchamianie obrazu z Microsoft Container Registry codziennie o `hello-world` godzinie 21:00 czasu UTC. Zadanie jest uruchamiane bez kontekstu kodu źródłowego.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Uruchom polecenie [az acr task show,][az-acr-task-show] aby sprawdzić, czy wyzwalacz czasomierza jest skonfigurowany. Domyślnie wyzwalacz aktualizacji obrazu podstawowego jest również włączony.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>Wyzwalanie zadania

Ręcznie wyzwolij zadanie za pomocą [az acr task run,][az-acr-task-run] aby upewnić się, że zostało ono prawidłowo skonfigurowane:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Jeśli kontener zostanie uruchomiony pomyślnie, dane wyjściowe będą podobne do następujących. Dane wyjściowe są skrócone w celu pokazania kluczowych kroków

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Po zaplanowanym czasie uruchom [polecenie az acr task list-runs,][az-acr-task-list-runs] aby sprawdzić, czy czasomierz wyzwolił zadanie zgodnie z oczekiwaniami:

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

Gdy czasomierz powiedzie się, dane wyjściowe będą podobne do następujących:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Zarządzanie wyzwalaczami czasomierza

Użyj poleceń [az acr task timer,][az-acr-task-timer] aby zarządzać wyzwalaczami czasomierza dla zadania ACR.

### <a name="add-or-update-a-timer-trigger"></a>Dodawanie lub aktualizowanie wyzwalacza czasomierza

Po utworzeniu zadania opcjonalnie dodaj wyzwalacz czasomierza za pomocą polecenia [az acr task timer add.][az-acr-task-timer-add] W poniższym przykładzie dodano nazwę wyzwalacza *czasomierza timer2* do utworzonego wcześniej czasomierza timertask.  Ten czasomierz wyzwala zadanie codziennie o godzinie 10:30 czasu UTC.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Zaktualizuj harmonogram istniejącego wyzwalacza lub zmień jego stan za pomocą polecenia [az acr task timer update.][az-acr-task-timer-update] Na przykład zaktualizuj wyzwalacz o nazwie *timer2,* aby wyzwolić zadanie o godzinie 11:30 czasu UTC:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Lista wyzwalaczy czasomierza

Polecenie [az acr task timer list][az-acr-task-timer-list] pokazuje wyzwalacze czasomierza ustawione dla zadania:

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
```

Przykładowe dane wyjściowe:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Usuwanie wyzwalacza czasomierza

Użyj polecenia [az acr task timer remove,][az-acr-task-timer-remove] aby usunąć wyzwalacz czasomierza z zadania. Poniższy przykład usuwa wyzwalacz *timer2* z *timertask*:

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Wyrażenia Cron

zadania usługi ACR używa biblioteki [NCronTab](https://github.com/atifaziz/NCrontab) do interpretowania wyrażeń cron. Obsługiwane wyrażenia w zadania usługi ACR mają pięć wymaganych pól oddzielonych białymi spacjami:

`{minute} {hour} {day} {month} {day-of-week}`

Strefa czasowa używana z wyrażeniami cron jest Uniwersalny czas koordynowany (UTC). Godziny są w formacie 24-godzinnym.

> [!NOTE]
> zadania usługi ACR nie obsługuje pola `{second}` lub `{year}` w wyrażeniach cron. Jeśli skopiujemy wyrażenie cron używane w innym systemie, pamiętaj o usunięciu tych pól, jeśli są one używane.

Każde pole może mieć jeden z następujących typów wartości:

|Typ  |Przykład  |Po wyzwoleniu  |
|---------|---------|---------|
|Konk. |<nobr>`"5 * * * *"`</nobr>|co godzinę o 5 minut po godzinie|
|Wszystkie wartości ( `*` )|<nobr>`"* 5 * * *"`</nobr>|co minutę godziny, począwszy od godziny 5:00 czasu UTC (60 razy dziennie)|
|Zakres ( `-` operator)|<nobr>`"0 1-3 * * *"`</nobr>|3 razy dziennie o godzinie 1:00, 2:00 i 3:00 czasu UTC|
|Zestaw wartości ( `,` operator)|<nobr>`"20,30,40 * * * *"`</nobr>|3 razy na godzinę, 20 minut, 30 minut i 40 minut po godzinie|
|Wartość interwału ( `/` operator)|<nobr>`"*/10 * * * *"`</nobr>|6 razy na godzinę, co 10 minut, 20 minut i tak dalej, po ostatniej godzinie

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Przykłady Cron

|Przykład|Po wyzwoleniu  |
|---------|---------|
|`"*/5 * * * *"`|raz na pięć minut|
|`"0 * * * *"`|raz w górnej części każdej godziny|
|`"0 */2 * * *"`|raz na dwie godziny|
|`"0 9-17 * * *"`|raz na godzinę od 9:00 do 17:00 UTC|
|`"30 9 * * *"`|codziennie o godz. 9:30 UTC|
|`"30 9 * * 1-5"`|o godzinie 9:30 czasu UTC każdego dnia tygodnia|
|`"30 9 * Jan Mon"`|o 9:30 UTC w każdy poniedziałek w styczniu|

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć wszystkie zasoby utworzone w tej serii samouczków, w tym rejestr kontenerów lub rejestry, wystąpienie kontenera, magazyn kluczy i jednostkę usługi, wydaj następujące polecenia:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano sposób tworzenia Azure Container Registry, które są automatycznie wyzwalane przez czasomierz. 

Aby uzyskać przykład użycia zaplanowanego zadania do czyszczenia repozytoriów w rejestrze, zobacz Automatyczne przeczyszczanie obrazów z rejestru [kontenerów platformy Azure.](container-registry-auto-purge.md)

Przykłady zadań wyzwalanych przez zatwierdzenia kodu źródłowego lub aktualizacje obrazu podstawowego można znaleźć w innych artykułach z [zadania usługi ACR samouczka.](container-registry-tutorial-quick-task.md)



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az_acr_task_timer_add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az_acr_task_timer_remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az_acr_task_timer_list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az_acr_task_timer_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
