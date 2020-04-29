---
title: AZ wiosenn Cloud
description: Zarządzanie chmurą wiosenną platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80298778"
---
# <a name="az-spring-cloud"></a>AZ Sprężyna — chmura

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Zarządzanie chmurą wiosenną platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

>[!Note]
> Chmura Wiosenna platformy Azure jest obecnie w wersji zapoznawczej.  Te polecenia mogą zostać zmienione lub usunięte w przyszłej wersji.

| AZ Sprężyna — chmura |  |
|------|------:|
| [AZ Sprężyna — tworzenie w chmurze](#az-spring-cloud-create) | Utwórz wystąpienie chmury wiosennej na platformie Azure. |
| [AZ Sprężyna — usuwanie w chmurze](#az-spring-cloud-delete) | Usuń wystąpienie chmury wiosennej na platformie Azure. |
| [AZ Sprężyna-Cloud list](#az-spring-cloud-list) | Wyświetl listę wszystkich wystąpień chmury wiosennej na platformie Azure w danej grupie zasobów, w przeciwnym razie Utwórz listę identyfikatorów subskrypcji. |
| [AZ Sprężyna — pokaz w chmurze](#az-spring-cloud-show) | Pokaż szczegóły chmury wiosennej platformy Azure. |

| AZ Sprężyna — aplikacja w chmurze | Polecenia do zarządzania aplikacjami w chmurze Azure wiosennej.  |
| ---- | ----: |
| [AZ Sprężyna — Tworzenie aplikacji w chmurze](#az-spring-cloud-app-create) | Utwórz nową aplikację przy użyciu domyślnego wdrożenia w chmurze Azure wiosennej. |
| [AZ Sprężyna — usuwanie aplikacji w chmurze](#az-spring-cloud-app-delete) | Usuń aplikację w chmurze Azure wiosennej. |
| [AZ Sprężyna — wdrażanie aplikacji w chmurze](#az-spring-cloud-app-deploy) | Wdróż z kodu źródłowego lub wstępnie skompilowanego pliku binarnego w aplikacji i zaktualizuj powiązane konfiguracje. |
| [AZ Wiosenna — lista aplikacji w chmurze](#az-spring-cloud-app-list) | Wyświetl listę wszystkich aplikacji w chmurze Azure wiosennej. |
| [AZ Sprężyna — ponowne uruchamianie aplikacji w chmurze](#az-spring-cloud-app-restart) | Ponowne uruchamianie wystąpień aplikacji przy użyciu ustawień domyślnych wdrożenia produkcyjnego. |
| [AZ Wiosenna — skalowanie aplikacji w chmurze](#az-spring-cloud-app-scale) | Ręczne skalowanie aplikacji lub jej wdrożeń. |
| [AZ wiosenn-Cloud App Set-Deployment](#az-spring-cloud-app-set-deployment) | Ustawianie wdrożenia produkcyjnego aplikacji. |
| [AZ wiosenn-Cloud App Show](#az-spring-cloud-app-show) | Pokaż szczegóły aplikacji w chmurze Azure wiosennej. |
| [AZ wiosenn-Cloud App Show-Deploy-log](#az-spring-cloud-app-show-deploy-log) | Pokaż dzienniki kompilacji dla najnowszego wdrożenia ze źródła. Domyślnie wdrożenie produkcyjne. |
| [Uruchom AZ wiosenn-Cloud App](#az-spring-cloud-app-start) | Uruchom wystąpienia aplikacji przy użyciu ustawień domyślnych wdrożenia produkcyjnego. |
| [AZ Wiosenna — zatrzymywanie aplikacji w chmurze](#az-spring-cloud-app-stop) | Zatrzymaj wystąpienia aplikacji przy użyciu ustawień domyślnych wdrożenia produkcyjnego. |
| [AZ Wiosenna — aktualizacja aplikacji w chmurze](#az-spring-cloud-app-update) | Zaktualizuj konfigurację określonej aplikacji. |

| AZ Sprężyna — powiązanie aplikacji w chmurze | Polecenia służące do zarządzania powiązaniami przy użyciu usługi Azure Data Services.  Aby te ustawienia zaczęły obowiązywać, należy ponownie uruchomić aplikację. |
| --- | ---: |
| [AZ Wiosenna — lista powiązań aplikacji w chmurze](#az-spring-cloud-app-binding-list) | Wyświetl wszystkie powiązania usługi w aplikacji. |
| [AZ Sprężyna — Usuwanie powiązania aplikacji w chmurze](#az-spring-cloud-app-binding-remove) | Usuń powiązanie usługi z aplikacji. |
| [AZ Sprężyna — Pokaż powiązanie aplikacji w chmurze](#az-spring-cloud-app-binding-show) | Pokaż szczegóły powiązania usługi. |
| [AZ Wiosenna Cosmos — Dodawanie powiązania aplikacji w chmurze](#az-spring-cloud-app-binding-cosmos-add) | Powiąż usługę Azure CosmosDB z aplikacją. |
| [AZ Wiosenna — Cosmos aktualizacja powiązania aplikacji](#az-spring-cloud-app-binding-cosmos-update) | Zaktualizuj powiązanie usługi Azure CosmosDB. |
| [AZ wiosna aplikacji w chmurze powiązanie MySQL Add](#az-spring-cloud-app-binding-mysql-add) | Powiąż Azure Database for MySQL z aplikacją. |
| [AZ wiosna aplikacji w chmurze — aktualizacja MySQL](#az-spring-cloud-app-binding-mysql-update) | Aktualizowanie powiązania usługi Azure Database for MySQL. |
| [AZ Wiosenna Redis — Dodawanie powiązania aplikacji w chmurze](#az-spring-cloud-app-binding-redis-add) | Powiąż pamięć podręczną platformy Azure dla Redis z aplikacją. |
| [AZ Wiosenna — Redis aktualizacja powiązania aplikacji](#az-spring-cloud-app-binding-redis-update) | Aktualizowanie powiązania usługi Azure cache for Redis. |

| AZ Sprężyna — wdrażanie aplikacji w chmurze | Polecenia służące do zarządzania cyklem życia wdrożenia aplikacji w chmurze Azure wiosennej. |
| --- | ---: |
| [AZ Sprężyna — wdrażanie aplikacji w chmurze](#az-spring-cloud-app-deployment-create) | Utwórz wdrożenie przejściowe dla aplikacji. |
| [AZ Sprężyna — usuwanie wdrożenia aplikacji w chmurze](#az-spring-cloud-app-deployment-delete) | Usuń wdrożenie aplikacji. |
| [AZ Sprężyna — lista wdrożenia aplikacji w chmurze](#az-spring-cloud-app-deployment-list) | Wyświetl listę wszystkich wdrożeń w aplikacji. |
| [AZ wiosenne — pokazywanie wdrożenia aplikacji w chmurze](#az-spring-cloud-app-deployment-show) | Pokaż szczegóły wdrożenia. |

| AZ Sprężyna — konfiguracja w chmurze — serwer | Polecenia służące do zarządzania serwerem konfiguracji chmury Azure wiosennej. |
| --- | ---: |
| [AZ Sprężyna — konfiguracja w chmurze — serwer Wyczyść](#az-spring-cloud-config-server-clear) | Wymaż wszystkie ustawienia na serwerze konfiguracji. |
| [AZ wiosenn-Cloud config-server set](#az-spring-cloud-config-server-set) | Zdefiniuj serwer konfiguracji na podstawie pliku YAML. |
| [AZ wiosenn-Cloud config-Server show](#az-spring-cloud-config-server-show) | Pokaż konfigurację serwera konfiguracji. |
| [AZ Sprężyna — konfiguracja serwera Git w chmurze](#az-spring-cloud-config-server-git-set) | Zdefiniuj właściwości git dla serwera konfiguracji.  Poprzednie wartości zostaną nadpisywane. |
| [AZ sprężyn-Cloud config Server git repozytorium Dodawanie](#az-spring-cloud-config-server-git-repo-add) | Dodaj nową konfigurację repozytorium git do serwera konfiguracji. |
| [AZ sprężyn-Cloud config Server list repozytorium git](#az-spring-cloud-config-server-git-repo-list) | Wyświetl listę wszystkich konfiguracji repozytorium git dla serwera konfiguracji. |
| [AZ sprężyn-Cloud config Server repozytorium git Remove](#az-spring-cloud-config-server-git-repo-remove) | Usuń określone repozytorium git z serwera konfiguracji. |

| AZ sprężyn-Cloud test-Endpoint | Polecenia umożliwiające zarządzanie testowaniem punktów końcowych w chmurze Azure wiosennej |
| --- | ---: |
| [AZ sprężyn-Cloud test-Endpoint Disable](#az-spring-cloud-test-endpoint-disable) | Wyłącz punkt końcowy testu. |
| [AZ sprężyn-Cloud test-Endpoint Enable](#az-spring-cloud-test-endpoint-enable) | Włącz punkt końcowy testu. |
| [AZ sprężyn-Cloud test-Endpoint List](#az-spring-cloud-test-endpoint-list) | Wyświetl klucze punktów końcowych testów. |
| [AZ sprężyn-Cloud test-Endpoint Renew-Key](#az-spring-cloud-test-endpoint-renew-key) | Wygeneruj ponownie klucz test-Endpoint. |

## <a name="az-spring-cloud-create"></a>AZ Sprężyna — tworzenie w chmurze

Utwórz nową aplikację przy użyciu domyślnego wdrożenia w chmurze Azure wiosennej.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa dla tego wystąpienia chmury Azure wiosennej. |
| --Resource-Group-g | Określa grupę zasobów dla tej aplikacji.  Skonfiguruj grupę domyślną przy użyciu`az configure --defaults group=<name>` |

| Parametry opcjonalne | |
| --- | ---: |
| --Location-l | Określa lokalizację serwera dla tej aplikacji.  Znajdowanie prawidłowych lokalizacji przy użyciu`az account list-locations` |
| --No-wait | Nie należy wykonywać długotrwałych operacji.

### <a name="examples"></a>Przykłady

Tworzenie nowej chmury Azure wiosny w zachodnim regionie

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>AZ Sprężyna — usuwanie w chmurze

Usuń wystąpienie chmury wiosennej na platformie Azure.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa wystąpienia chmury wiosennej platformy Azure do usunięcia. |
| --Resource-Group-g | Nazwa grupy zasobów, do której należy chmura sprężynowa platformy Azure. |

| Parametry opcjonalne | |
| --- | ---: |
| -nie — czekaj | Nie czekaj na zakończenie długotrwałych operacji. |

### <a name="example"></a>Przykład

Usuń wystąpienie chmury wiosennej platformy Azure o nazwie "Moja usługa" z elementu "Moja resourceName".

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>AZ Sprężyna-Cloud list

Wyświetl listę wszystkich wystąpień chmury wiosennej platformy Azure skojarzonych z daną grupą zasobów. Jeśli grupa zasobów nie zostanie określona, należy podać identyfikator subskrypcji.

```azurecli
az spring-cloud list --resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --Resource-Group-g | Nazwa grupy zasobów. |

## <a name="az-spring-cloud-show"></a>AZ Sprężyna — pokaz w chmurze

Pokaż szczegóły określonego wystąpienia chmury Azure wiosennej.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa wystąpienia chmury wiosennej na platformie Azure. |
| --Resource-Group-g | Nazwa grupy zasobów, do której należy wystąpienie chmury Azure wiosennej.

## <a name="az-spring-cloud-app-create"></a>AZ Sprężyna — Tworzenie aplikacji w chmurze

Utwórz nową aplikację w chmurze Azure wiosennej.

```azurecli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --CPU | Liczba rdzeni wirtualnych na wystąpienie.  Wartość domyślna: 1. |
| --Enable-persistent-Storage | Wartość logiczna.  W przypadku wartości true program instaluje dysk 50 GB z domyślną ścieżką. |
| --instance-Count | Liczba wystąpień.  Wartość domyślna: 1. |
| --is-Public | Wartość logiczna.  W przypadku wartości true przypisuje domenę publiczną. |
| --pamięć | Liczba GB pamięci na wystąpienie.  Wartość domyślna: 1. |

### <a name="examples"></a>Przykłady

Utwórz aplikację z domyślną konfiguracją.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Utwórz publicznie dostępną aplikację z 3 wystąpieniami.  Każde wystąpienie ma 3 GB pamięci i 2 rdzenie procesora CPU.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>AZ Sprężyna — usuwanie aplikacji w chmurze

Usuwa aplikację w chmurze Azure wiosennej.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deploy"></a>AZ Sprężyna — wdrażanie aplikacji w chmurze

Wdróż aplikację w chmurze Azure wiosennej na podstawie kodu źródłowego lub wstępnie skompilowanego pliku binarnego, a następnie zaktualizuj powiązane konfiguracje.

```azurecli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --CPU | Liczba wirtualnych rdzeni CPI na wystąpienie. |
| --Deployment-d | Nazwa istniejącego wdrożenia aplikacji.  Jeśli nie zostanie określony, wartością domyślną jest wdrożenie produkcyjne. |
| --ENV | Zmienne środowiskowe rozdzielane spacjami w formacie "klucz [= wartość]". |
| --instance-Count | Liczba wystąpień. |
| --jar-Path | Jeśli jest podany, wdróż plik JAR na podstawie danej ścieżki. W przeciwnym razie Wdróż bieżący folder jako element pułapki. |
| --JVM-Options | Ciąg zawierający opcje JVM.  Użyj wyrażenia "=" zamiast "", aby uniknąć błędów analizy powłoki. Np. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --pamięć | Liczba GB pamięci na wystąpienie. |
| --No-wait | Nie czekaj na zakończenie długotrwałych operacji. |
| --Runtime-Version | Wersja języka wykonawczego używanego w aplikacji.  Dozwolone wartości: `Java_11`, `Java_8`. |
| --Target-module | Moduł podrzędny do wdrożenia.  Wymagane w przypadku kompilowania wielu pakietów jar z kodu źródłowego. |
| --Version | Wersja wdrożenia.  Bez zmian, jeśli nie została ustawiona. |

### <a name="examples"></a>Przykłady

Wdróż kod źródłowy w aplikacji. Spowoduje to spakowanie bieżącego katalogu, skompilowanie pliku binarnego za pomocą usługi kompilacji Pivoting, a następnie wdrożenie go w aplikacji.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Wdróż wstępnie utworzony plik JAR w aplikacji przy użyciu opcji JVM i zmiennych środowiskowych.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Wdróż kod źródłowy w określonym wdrożeniu aplikacji.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>AZ Wiosenna — lista aplikacji w chmurze

Wyświetl listę wszystkich aplikacji w wystąpieniu usługi Azure wiosny Cloud.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Parametry wymagane | |
| --- | ---: |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-restart"></a>AZ Sprężyna — ponowne uruchamianie aplikacji w chmurze

Ponowne uruchamianie wystąpień aplikacji.  Wartość domyślna to wdrożenie produkcyjne.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --Deployment-d | Nazwa istniejącego wdrożenia aplikacji.  Jeśli nie zostanie określony, wartością domyślną jest wdrożenie produkcyjne. |
| --No-wait | Nie czekaj na zakończenie długotrwałych operacji. |

## <a name="az-spring-cloud-app-scale"></a>AZ Wiosenna — skalowanie aplikacji w chmurze

Ręczne skalowanie aplikacji lub jej wdrożeń.

```azurecli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --CPU | Liczba rdzeni wirtualnych procesora CPU na wystąpienie aplikacji. |
| --Deployment-d | Nazwa istniejącego wdrożenia aplikacji.  Jeśli nie zostanie określony, wartością domyślną jest wdrożenie produkcyjne. |
| --instance-Count | Liczba wystąpień tej aplikacji. |
| --pamięć | Liczba GB pamięci na wystąpienie aplikacji. |
| --No-wait | Nie czekaj na zakończenie długotrwałych operacji. |

### <a name="examples"></a>Przykłady

Skaluj w górę aplikację do 4 rdzeni procesora i 8 GB pamięci na wystąpienie.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Skalowanie wdrożenia aplikacji do 5 wystąpień.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>AZ wiosenn-Cloud App Set-Deployment

Ustaw opcje konfiguracji dla wdrożenia produkcyjnego aplikacji.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --Deployment-d | Nazwa istniejącego wdrożenia aplikacji. |
| --Name-n | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --No-wait | Nie czekaj na zakończenie długotrwałych operacji. |

### <a name="examples"></a>Przykłady

Zamień wdrożenie przejściowe aplikacji do środowiska produkcyjnego.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>AZ wiosenn-Cloud App Show

Pokaż szczegóły aplikacji w chmurze Azure wiosennej.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>AZ wiosenn-Cloud App Show-Deploy-log

Pokaż dziennik kompilacji ostatniego wdrożenia z kodu źródłowego.  Wartość domyślna to środowisko produkcyjne.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --Deployment-d | Nazwa istniejącego wdrożenia aplikacji.  Wartość domyślna to środowisko produkcyjne. |

## <a name="az-spring-cloud-app-start"></a>Uruchom AZ wiosenn-Cloud App

Uruchamia wystąpienia aplikacji.  Wartość domyślna to środowisko produkcyjne.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --Deployment-d | Nazwa istniejącego wdrożenia aplikacji.  Wartość domyślna to środowisko produkcyjne. |
| --No-wait | Nie czekaj na zakończenie długotrwałych operacji. |

## <a name="az-spring-cloud-app-stop"></a>AZ Wiosenna — zatrzymywanie aplikacji w chmurze

Zatrzymaj wystąpienia aplikacji.  Wartość domyślna to środowisko produkcyjne.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --Deployment-d | Nazwa istniejącego wdrożenia aplikacji.  Wartość domyślna to środowisko produkcyjne. |
| --No-wait | Nie czekaj na zakończenie długotrwałych operacji. |

## <a name="az-spring-cloud-app-update"></a>AZ Wiosenna — aktualizacja aplikacji w chmurze

Aktualizowanie przechowywanej konfiguracji aplikacji.

```azurecli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Parametry wymagane | |
| --- | ---: |
| --Name-n | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --Deployment-d | Nazwa istniejącego wdrożenia aplikacji.  Wartość domyślna to środowisko produkcyjne. |
| --Enable-persistent-Storage | Typu.  W przypadku wartości true Zainstaluj dysk 50 GB z domyślną ścieżką. |
| --ENV | Zmienne środowiskowe rozdzielane spacjami w formacie "klucz [= wartość]". |
| --is-Public | Typu.  W przypadku wartości true Przypisz domenę publiczną do aplikacji. |
| --JVM-Options | Ciąg zawierający opcje JVM.  Użyj wyrażenia "=" zamiast "", aby uniknąć błędów analizy powłoki. Np. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --No-wait | Nie czekaj na zakończenie długotrwałych operacji. |
| --Runtime-Version | Wersja języka wykonawczego używanego w aplikacji.  Dozwolone wartości: `Java_11`, `Java_8`. |

### <a name="example"></a>Przykład

Dodaj zmienną środowiskową dla aplikacji.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>AZ Wiosenna — lista powiązań aplikacji w chmurze

Wyświetl wszystkie powiązania usługi w aplikacji.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-remove"></a>AZ Sprężyna — Usuwanie powiązania aplikacji w chmurze

Usuń powiązanie usługi z aplikacji.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Name | Nazwa powiązania usługi, które ma zostać usunięte. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-show"></a>AZ Sprężyna — Pokaż powiązanie aplikacji w chmurze

Pokaż szczegóły powiązania usługi.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Name | Nazwa powiązania usługi. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>AZ Wiosenna Cosmos — Dodawanie powiązania aplikacji w chmurze

Powiąż Azure Cosmos DB z aplikacją.

```azurecli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --Typ interfejsu API | Określ typ interfejsu API przy użyciu jednej z następujących wartości: Cassandra, Gremlin, Mongo, SQL, Table. |
| --Aplikacja | Nazwa aplikacji. |
| --Name | Nazwa powiązania usługi. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

|Parametry opcjonalne | |
| --- | ---: |
| --Collection-Name | Nazwa kolekcji.  Wymagane w przypadku korzystania z Gremlin. |
| --Nazwa bazy danych | Nazwa bazy danych.  Wymagane w przypadku korzystania z Mongo, SQL i Gremlin. |
| --Key-Space | Cassandra.  Wymagane w przypadku korzystania z Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>AZ Wiosenna — Cosmos aktualizacja powiązania aplikacji

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Name | Nazwa powiązania usługi. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

|Parametry opcjonalne | |
| --- | ---: |
| --Collection-Name | Nazwa kolekcji.  Wymagane w przypadku korzystania z Gremlin. |
| --Nazwa bazy danych | Nazwa bazy danych.  Wymagane w przypadku korzystania z Mongo, SQL i Gremlin. |
| --Key-Space | Cassandra.  Wymagane w przypadku korzystania z Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>AZ wiosna aplikacji w chmurze powiązanie MySQL Add

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Nazwa bazy danych | Nazwa bazy danych. |
| --klucz | Klucz interfejsu API usługi. |
| --Name | Nazwa powiązania usługi. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Resource-ID | Identyfikator zasobu platformy Azure dla usługi, z którą ma zostać utworzone powiązanie. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |
| --username | Nazwa użytkownika do uzyskiwania dostępu do bazy danych. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>AZ wiosna aplikacji w chmurze — aktualizacja MySQL

Zaktualizuj połączenie powiązania usługi dla aplikacji do Azure Database for MySQL.

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Name | Nazwa powiązania usługi. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --Nazwa bazy danych | Nazwa bazy danych. |
| --klucz | Klucz interfejsu API usługi. |
| --username | Nazwa użytkownika do uzyskiwania dostępu do bazy danych. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>AZ Wiosenna Redis — Dodawanie powiązania aplikacji w chmurze

Powiąż pamięć podręczną platformy Azure dla Redis z aplikacją.

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Name | Nazwa powiązania usługi. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Resource-ID | Identyfikator zasobu platformy Azure dla usługi, z którą chcesz utworzyć powiązanie. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --Disable-SSL | Wyłącz protokół TLS. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>AZ Wiosenna — Redis aktualizacja powiązania aplikacji

Zaktualizuj powiązanie usługi dla pamięci podręcznej platformy Azure dla Redis.

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Name | Nazwa powiązania usługi. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --Disable-SSL | Wyłącz protokół TLS. |

## <a name="az-spring-cloud-app-deployment-create"></a>AZ Sprężyna — wdrażanie aplikacji w chmurze

Utwórz wdrożenie przejściowe dla aplikacji.

Aby wdrożyć kod lub zaktualizować ustawienia do istniejącego wdrożenia, użyj `az spring-cloud app deploy --deployment <staging-deployment>` lub "AZ wiosenn-Cloud App Update--Deployment. <staging deployment>

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Name | Nazwa powiązania usługi. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --CPU | Liczba wirtualnych rdzeni procesora CPU na wystąpienie.  Ustawienie domyślne: 1 |
| --ENV | Zmienne środowiskowe rozdzielane spacjami w formacie "klucz [= wartość]". |
| --instance-Count | Liczba wystąpień. Wartość domyślna: 1. |
| --jar-Path | Jeśli jest podany, wdróż plik JAR.  W przeciwnym razie Wdróż bieżący folder jako element pułapki. |
| --JVM-Options | Ciąg zawierający opcje JVM.  Użyj wyrażenia "=" zamiast "", aby uniknąć błędów analizy powłoki. Np. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --pamięć | Liczba GB pamięci na wystąpienie. |
| --No-wait | Nie czekaj na zakończenie długotrwałych operacji. |
| --Runtime-Version | Wersja języka wykonawczego używanego w aplikacji.  Dozwolone wartości: `Java_11`, `Java_8`. |
| --Skip-klon-Settings | Utwórz wdrożenie przejściowe, klonowanie bieżących ustawień wdrożenia produkcyjnego. |
| --Target-module | Moduł podrzędny do wdrożenia.  Wymagane w przypadku kompilowania wielu pakietów jar z kodu źródłowego. |
| --Version | Wersja wdrożenia.  Bez zmian, jeśli nie została ustawiona. |

### <a name="examples"></a>Przykłady

Wdróż kod źródłowy w nowym wdrożeniu aplikacji.  Spowoduje to spakowanie bieżącego katalogu, skompilowanie pliku binarnego przy użyciu systemu kompilacji wystawnej, a następnie wdrożenie.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Wdróż wstępnie zbudowany plik JAR w aplikacji z opcjami JVM i zmiennymi środowiskowymi.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>AZ Sprężyna — usuwanie wdrożenia aplikacji w chmurze

Usuń wdrożenie aplikacji.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Name | Nazwa wdrożenia. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-list"></a>AZ Sprężyna — lista wdrożenia aplikacji w chmurze

Wyświetl listę wszystkich wdrożeń w aplikacji.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-show"></a>AZ wiosenne — pokazywanie wdrożenia aplikacji w chmurze

Pokaż szczegóły wdrożenia.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Parametry wymagane | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Name | Nazwa wdrożenia. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Service-s | Nazwa chmury wiosennej platformy Azure.  Usługę domyślną można skonfigurować przy użyciu programu `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-config-server-clear"></a>AZ Sprężyna — konfiguracja w chmurze — serwer Wyczyść

Wymaż wszystkie ustawienia konfiguracji na serwerze konfiguracji.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --Name | Nazwa chmury wiosennej platformy Azure. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-set"></a>AZ wiosenn-Cloud config-server set

Skonfiguruj ustawienia konfiguracji na serwerze konfiguracji przy użyciu pliku YAML.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Parametry wymagane | |
| --- | ---: |
| --config-plik | Ścieżka pliku do manifestu YAML dla konfiguracji serwera konfiguracji. |
| --Name | Nazwa chmury wiosennej platformy Azure. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --No-wait | Nie należy wykonywać długotrwałych operacji.

## <a name="az-spring-cloud-config-server-show"></a>AZ wiosenn-Cloud config-Server show

Pokaż ustawienia serwera konfiguracji.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --Name | Nazwa chmury wiosennej platformy Azure. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-git-set"></a>AZ Sprężyna — konfiguracja w chmurze — zestaw serwera git

Ustaw właściwości git dla serwera konfiguracji.  Spowoduje to zastąpienie wszystkich istniejących właściwości usługi git.

```azurecli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Parametry wymagane | |
| --- | ---: |
| --Name | Nazwa chmury wiosennej platformy Azure. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --URI | Identyfikator URI dodanej konfiguracji. |

| Parametry opcjonalne | |
| --- | ---: |
| --Odłóż | Tymczasowo przechowuj obiekt w lokalnej pamięci podręcznej, zamiast wysyłać do platformy Azure.  Służy `az cache` do wyświetlania/czyszczenia. |
| --Host-Key | Klucz hosta dla dodanej konfiguracji. |
| --Host-Key-Algorithm | Algorytm klucza hosta dla dodanej konfiguracji. |
| --Etykieta | Etykieta dodanej konfiguracji. |
| --password | Hasło do dodanej konfiguracji. |
| --klucz prywatny | Klucz prywatny dodanej konfiguracji. |
| --Search-Paths | Wyszukaj ścieżki dodanej konfiguracji.  Używaj ograniczników przecinków dla wielu ścieżek. |
| --Strict-Host-Key-sprawdzij | Włącza dokładne sprawdzanie klucza hosta dodanej konfiguracji. |
| --username | Nazwa użytkownika dodanej konfiguracji. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>AZ Sprężyna — konfiguracja w chmurze — Dodawanie repozytorium git

```azurecli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Parametry wymagane | |
| --- | ---: |
| --Name | Nazwa chmury wiosennej platformy Azure. |
| --repo-Name | Identyfikator URI repozytorium. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --URI | Identyfikator URI dodanej konfiguracji. |

| Parametry opcjonalne | |
| --- | ---: |
| --Odłóż | Tymczasowo przechowuj obiekt w lokalnej pamięci podręcznej, zamiast wysyłać do platformy Azure.  Służy `az cache` do wyświetlania/czyszczenia. |
| --Host-Key | Klucz hosta dla dodanej konfiguracji. |
| --Host-Key-Algorithm | Algorytm klucza hosta dla dodanej konfiguracji. |
| --Etykieta | Etykieta dodanej konfiguracji. |
| --password | Hasło do dodanej konfiguracji. |
| --wzorzec | Wzorzec dla repozytorium.  Używaj ograniczników przecinków dla wielu ścieżek.|
| --klucz prywatny | Klucz prywatny dodanej konfiguracji. |
| --Search-Paths | Wyszukaj ścieżki dodanej konfiguracji.  Używaj ograniczników przecinków dla wielu ścieżek. |
| --Strict-Host-Key-sprawdzij | Włącza dokładne sprawdzanie klucza hosta dodanej konfiguracji. |
| --username | Nazwa użytkownika dodanej konfiguracji. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>AZ wiosenn-Cloud config-Server repozytorium git list

Wyświetl listę wszystkich repozytoriów Git zdefiniowanych na serwerze konfiguracji

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Parametry wymagane | |
| --- | ---: |
| --Name | Nazwa chmury wiosennej platformy Azure. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --Odłóż | Tymczasowo przechowuj obiekt w lokalnej pamięci podręcznej, zamiast wysyłać do platformy Azure.  Służy `az cache` do wyświetlania/czyszczenia. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>AZ Sprężyna — konfiguracja w chmurze — usuwanie repozytorium git

Usuń istniejącą konfigurację repozytorium git z serwera konfiguracji.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Parametry wymagane | |
| --- | ---: |
| --Name | Nazwa chmury wiosennej platformy Azure. |
| --repo-Name | Identyfikator URI repozytorium. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --Odłóż | Tymczasowo przechowuj obiekt w lokalnej pamięci podręcznej, zamiast wysyłać do platformy Azure.  Służy `az cache` do wyświetlania/czyszczenia. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>AZ sprężyn-Cloud test-Endpoint Disable

Wyłącz test punktu końcowego chmury wiosennej platformy Azure

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --Name | Nazwa chmury wiosennej platformy Azure. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>AZ sprężyn-Cloud test-Endpoint Enable

Włącz punkt końcowy testu dla chmury wiosennej platformy Azure. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Parametry wymagane | |
| --- | ---: |
| --Name | Nazwa chmury wiosennej platformy Azure. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-list"></a>AZ sprężyn-Cloud test-Endpoint List 

Wyświetl listę dostępnych klawiszy punktów końcowych testów dla chmury wiosennej platformy Azure.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Parametry wymagane | |
| --- | ---: |
| --Name | Nazwa chmury wiosennej platformy Azure. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |

| Parametry opcjonalne | |
| --- | ---: |
| --Aplikacja | Nazwa aplikacji. |
| --Deployment-d | Nazwa istniejącego wdrożenia aplikacji.  Jeśli nie zostanie określony, wartością domyślną jest produkcja. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>AZ sprężyn-Cloud test-Endpoint Renew-Key

Wygeneruj ponownie klucz punktu końcowego testu dla chmury sieci platformy Azure.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Parametry wymagane | |
| --- | ---: |
| --Name | Nazwa chmury wiosennej platformy Azure. |
| --Resource-Group-g | Nazwa grupy zasobów.  Grupę domyślną można skonfigurować przy użyciu `az configure --defaults group=<name>`. |
| --Wpisz | Typ klucza punktu końcowego testu.  Dozwolone wartości: podstawowe, pomocnicze. |
