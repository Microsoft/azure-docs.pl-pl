---
title: Samouczek — Planowanie zadania ACR
description: W tym samouczku dowiesz się, jak uruchomić zadanie Azure Container Registry zgodnie ze zdefiniowanym harmonogramem, ustawiając jeden lub więcej wyzwalaczy czasomierza
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 13a4ccac4ea97538583c1c063a6dc61e4d25686a
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030615"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Samouczek: uruchamianie zadania ACR zgodnie ze zdefiniowanym harmonogramem

W tym samouczku przedstawiono sposób uruchamiania [zadania ACR](container-registry-tasks-overview.md) zgodnie z harmonogramem. Zaplanuj zadanie, konfigurując jeden lub więcej *wyzwalaczy czasomierza*. Wyzwalacze czasomierza mogą być używane samodzielnie lub w połączeniu z innymi wyzwalaczami zadań.

W tym samouczku dowiesz się więcej na temat planowania zadań i:

> [!div class="checklist"]
> * Tworzenie zadania z wyzwalaczem czasomierza
> * Zarządzaj wyzwalaczami czasomierza

Planowanie zadania jest przydatne w scenariuszach takich jak następujące:

* Uruchom obciążenie kontenera dla zaplanowanych operacji konserwacji. Na przykład Uruchom aplikację z kontenerem w celu usunięcia niepotrzebnych obrazów z rejestru.
* Uruchom zestaw testów w obrazie produkcyjnym w trakcie pracy w ramach monitorowania na żywo.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Informacje o planowaniu zadania

* **Wyzwalacz z wyrażeniem firmy CRONUS** — wyzwalacz czasomierza dla zadania używa *wyrażenia firmy CRONUS*. Wyrażenie jest ciągiem zawierającym pięć pól określające minuty, godzinę, dzień, miesiąc i dzień tygodnia, w których ma zostać wyzwolone zadanie. Obsługiwane są częstotliwości do raz na minutę.

  Na przykład wyrażenie `"0 12 * * Mon-Fri"` wyzwala zadanie o godzinie UTC dla każdego dnia tygodnia. Zobacz [szczegóły](#cron-expressions) w dalszej części tego artykułu.
* **Wyzwalacze wielu czasomierzy** — Dodawanie wielu czasomierzy do zadania jest dozwolone, o ile harmonogramy różnią się.
    * Określ wyzwalacze wielu czasomierzy podczas tworzenia zadania lub Dodaj je później.
    * Opcjonalnie Nazwij wyzwalacze, aby ułatwić zarządzanie, lub ACR zadania będą podawać domyślne nazwy wyzwalaczy.
    * Jeśli harmonogramy czasomierze nakładają się na siebie, ACR zadania wyzwalają zadanie w zaplanowanym czasie dla każdego czasomierza.
* **Inne Wyzwalacze zadań** — w zadaniu wyzwalanym czasomierzem można również włączyć wyzwalacze na podstawie [zatwierdzeń kodu źródłowego](container-registry-tutorial-build-task.md) lub [aktualizacji obrazu podstawowego](container-registry-tutorial-base-image-update.md). Podobnie jak w przypadku innych zadań ACR, można również [ręcznie uruchomić][az-acr-task-run] zaplanowane zadanie.

## <a name="create-a-task-with-a-timer-trigger"></a>Tworzenie zadania z wyzwalaczem czasomierza

### <a name="task-command"></a>Zadanie — polecenie

Najpierw Wypełnij następującą zmienną środowiskową powłoki wartością odpowiednią dla danego środowiska. Ten krok nie jest ściśle wymagany, ale trochę ułatwia wykonywanie przedstawionych w tym samouczku wielowierszowych poleceń interfejsu wiersza polecenia platformy Azure. Jeśli zmienna środowiskowa nie zostanie wypełniona, należy ręcznie zastąpić każdą wartość w dowolnym miejscu w przykładowych poleceniach.

[![Uruchamianie osadzane](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

Podczas tworzenia zadania za pomocą polecenia [AZ ACR Task Create][az-acr-task-create] można opcjonalnie dodać wyzwalacz czasomierza. Dodaj `--schedule` parametr i przekaż wyrażenie firmy CRONUS dla czasomierza.

W prostym przykładzie następujące zadanie wyzwala uruchamianie `hello-world` obrazu z witryny Microsoft Container Registry codziennie o godzinie 21:00 czasu UTC. Zadanie jest uruchamiane bez kontekstu kodu źródłowego.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Uruchom polecenie [AZ ACR Task show][az-acr-task-show] , aby zobaczyć, że wyzwalacz czasomierza został skonfigurowany. Domyślnie wyzwalacz aktualizacji obrazu podstawowego jest również włączony.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>Wyzwalanie zadania

Wyzwól zadanie ręcznie przy użyciu [AZ ACR Task Run][az-acr-task-run] , aby upewnić się, że zostało prawidłowo skonfigurowane:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Jeśli kontener zostanie uruchomiony pomyślnie, dane wyjściowe są podobne do następujących. Dane wyjściowe są skondensowane w celu pokazania kluczowych kroków

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

Po upływie zaplanowanego czasu Uruchom polecenie [AZ ACR Task List-][az-acr-task-list-runs] Run, aby sprawdzić, czy czasomierz wyzwolił zadanie zgodnie z oczekiwaniami:

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

Po pomyślnym zakończeniu czasomierza dane wyjściowe są podobne do następujących:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Zarządzaj wyzwalaczami czasomierza

Użyj polecenia [AZ ACR Task Timer][az-acr-task-timer] , aby zarządzać wyzwalaczami czasomierza dla zadania ACR.

### <a name="add-or-update-a-timer-trigger"></a>Dodawanie lub aktualizowanie wyzwalacza czasomierza

Po utworzeniu zadania opcjonalnie Dodaj wyzwalacz czasomierza przy użyciu polecenia [AZ ACR Task Timer Add][az-acr-task-timer-add] . Poniższy przykład dodaje nazwę wyzwalacza czasomierza *timer2* do *timertask* utworzonego wcześniej. Ten czasomierz wyzwala zadanie codziennie o godzinie 10:30 czasu UTC.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Aktualizacja harmonogramu istniejącego wyzwalacza lub zmiana jego stanu za pomocą polecenia [AZ ACR Task Timer Update][az-acr-task-timer-update] . Na przykład zaktualizuj wyzwalacz o nazwie *timer2* , aby wyzwolić zadanie o godzinie 11:30 czasu UTC:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Lista wyzwalaczy czasomierza

Polecenie [AZ ACR Task Timer list][az-acr-task-timer-list] wyświetla wyzwalacze czasomierza skonfigurowane dla zadania:

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

Użyj polecenia [AZ ACR Task Timer Remove][az-acr-task-timer-remove] , aby usunąć wyzwalacz czasomierza z zadania. Poniższy przykład usuwa wyzwalacz *timer2* z *timertask*:

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Wyrażenia firmy CRONUS

Zadania ACR używają biblioteki [NCronTab](https://github.com/atifaziz/NCrontab) do interpretowania wyrażeń firmy cronus. Obsługiwane wyrażenia w zadaniach ACR mają pięć wymaganych pól rozdzielonych odstępem:

`{minute} {hour} {day} {month} {day-of-week}`

Strefa czasowa używana z wyrażeniami firmy CRONUS jest uniwersalnym czasem koordynowanym (UTC). Godziny są w formacie 24-godzinnym.

> [!NOTE]
> Zadania ACR nie obsługują `{second}` `{year}` pola lub w wyrażeniach firmy cronus. Jeśli skopiujesz wyrażenie firmy CRONUS używane w innym systemie, pamiętaj, aby usunąć te pola, jeśli są używane.

Każde pole może mieć jeden z następujących typów wartości:

|Typ  |Przykład  |Po wyzwoleniu  |
|---------|---------|---------|
|Określona wartość |<nobr>`"5 * * * *"`</nobr>|co godzinę w ciągu 5 minut od godziny|
|Wszystkie wartości ( `*` )|<nobr>`"* 5 * * *"`</nobr>|co minutę godziny od 5:00 czasu UTC (60 razy dziennie)|
|Zakres ( `-` operator)|<nobr>`"0 1-3 * * *"`</nobr>|3 razy dziennie, o godzinie 1:00, 2:00 i 3:00 UTC|
|Zestaw wartości ( `,` operator)|<nobr>`"20,30,40 * * * *"`</nobr>|3 razy na godzinę, co 20 minut, 30 minut i 40 minut po godzinie|
|Wartość interwału ( `/` operator)|<nobr>`"*/10 * * * *"`</nobr>|6 razy na godzinę, przy 10 minutach, 20 minutach itd., Ostatnia godzina

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Przykłady dla firmy CRONUS

|Przykład|Po wyzwoleniu  |
|---------|---------|
|`"*/5 * * * *"`|co pięć minut|
|`"0 * * * *"`|raz w górnej części co godzinę|
|`"0 */2 * * *"`|co dwie godziny|
|`"0 9-17 * * *"`|co godzinę od 9:00 do 17:00 czasu UTC|
|`"30 9 * * *"`|Codziennie o 9:30 czasu UTC|
|`"30 9 * * 1-5"`|o godzinie 9:30 UTC każdego dnia tygodnia|
|`"30 9 * Jan Mon"`|o 9:30 czasu UTC co poniedziałek w styczniu|

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć wszystkie zasoby utworzone w tej serii samouczków, łącznie z rejestrem kontenerów lub rejestrami, wystąpieniem kontenera, magazynem kluczy i jednostką usługi, należy wydać następujące polecenia:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia Azure Container Registry zadań, które są automatycznie wyzwalane przez czasomierz. 

Przykład użycia zaplanowanego zadania do czyszczenia repozytoriów w rejestrze można znaleźć w temacie [Automatyczne przeczyszczanie obrazów z usługi Azure Container Registry](container-registry-auto-purge.md).

Przykłady zadań wyzwalanych przez zatwierdzenia kodu źródłowego lub aktualizacje obrazu podstawowego można znaleźć w innych artykułach w [serii samouczków zadań ACR](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
