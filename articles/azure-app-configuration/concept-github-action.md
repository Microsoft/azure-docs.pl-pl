---
title: Synchronizowanie repozytorium GitHub z konfiguracją aplikacji
description: Użyj akcji usługi GitHub, aby automatycznie aktualizować wystąpienie konfiguracji aplikacji podczas aktualizowania repozytorium GitHub.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 66d0e32e7dfdd5ab2abee5108ac8ce54c5222747
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87371825"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Synchronizowanie repozytorium GitHub z konfiguracją aplikacji

Zespoły, które chcą nadal korzystać z istniejących zasad kontroli źródła, mogą korzystać z akcji usługi GitHub w celu automatycznego synchronizowania repozytorium GitHub z magazynem konfiguracji aplikacji. Dzięki temu można wprowadzać zmiany w plikach konfiguracji w zwykły sposób, podczas uzyskiwania korzyści z konfiguracji aplikacji, takich jak: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Scentralizowana konfiguracja poza kodem <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Aktualizowanie konfiguracji bez ponownego wdrażania całej aplikacji <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integracja z usługami, takimi jak Azure App Service i Functions. 

[Przepływ pracy](https://help.github.com/articles/about-github-actions#workflow) akcji usługi GitHub definiuje zautomatyzowany proces w repozytorium GitHub. Akcja *synchronizacji konfiguracji aplikacji platformy Azure* wyzwala aktualizacje wystąpienia konfiguracji aplikacji, gdy zostaną wprowadzone zmiany w repozytorium źródłowym. Używa pliku YAML (. yml) znajdującego się w `/.github/workflows/` ścieżce repozytorium w celu zdefiniowania kroków i parametrów. Aktualizacje konfiguracji można wyzwalać podczas wypychania, przeglądania lub rozgałęziania plików konfiguracji aplikacji, tak jak w przypadku kodu aplikacji.

[Dokumentacja](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) usługi GitHub zawiera szczegółowy widok przepływów pracy i akcji usługi GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Włączanie akcji usługi GitHub w repozytorium
Aby rozpocząć korzystanie z tej akcji usługi GitHub, przejdź do repozytorium i wybierz kartę **Akcje** . Wybierz pozycję **Nowy przepływ pracy**, a następnie **samodzielnie Skonfiguruj przepływ pracy**. Na koniec Wyszukaj ciąg "Azure App Configuration Sync" w portalu Marketplace.
> [!div class="mx-imgBorder"]
> ![Wybierz kartę Akcja](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Wybierz akcję synchronizacji konfiguracji aplikacji](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synchronizuj pliki konfiguracji po wypchnięciu
Ta akcja synchronizuje pliki konfiguracji aplikacji platformy Azure po wypchnięciu zmiany do programu `appsettings.json` . Gdy deweloper wypycha zmianę do `appsettings.json` , Akcja synchronizacji konfiguracji aplikacji aktualizuje wystąpienie konfiguracji aplikacji o nowe wartości.

Pierwsza sekcja tego przepływu pracy określa, że akcja jest wyzwalana *w* przypadku *wypychania* zawierającego `appsettings.json` do gałęzi *głównej* . Druga sekcja zawiera zadania uruchamiane po wyzwoleniu akcji. Akcja sprawdza odpowiednie pliki i aktualizuje wystąpienie konfiguracji aplikacji przy użyciu parametrów połączenia przechowywanych jako wpis tajny w repozytorium.  Aby uzyskać więcej informacji o używaniu wpisów tajnych w usłudze GitHub, zobacz artykuł dotyczący tworzenia i używania zaszyfrowanych kluczy tajnych w witrynie [GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) .

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-strict-sync"></a>Użyj ścisłej synchronizacji
Domyślnie akcja usługi GitHub nie włącza trybu z ograniczeniami, co oznacza, że synchronizacja będzie dodawać tylko wartości klucza z pliku konfiguracji do wystąpienia konfiguracji aplikacji (nie zostaną usunięte pary klucz-wartość). Włączenie trybu z trybem Strict spowoduje, że pary klucz-wartość, które nie znajdują się w pliku konfiguracji, są usuwane z wystąpienia konfiguracji aplikacji, tak aby pasowały do pliku konfiguracji. W przypadku synchronizowania z wielu źródeł lub korzystania z Azure Key Vault z konfiguracją aplikacji należy użyć różnych prefiksów lub etykiet z ścisłą synchronizacją, aby uniknąć wymazywania ustawień konfiguracji z innych plików (Zobacz przykłady poniżej). 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```
## <a name="sync-multiple-files-in-one-action"></a>Synchronizuj wiele plików w jednej akcji 

Jeśli konfiguracja znajduje się w wielu plikach, można użyć poniższego wzorca, aby wyzwolić synchronizację w przypadku zmodyfikowania pliku. Ten wzorzec używa biblioteki globalizowania https://www.npmjs.com/package/glob . Należy pamiętać, że jeśli nazwa pliku konfiguracji zawiera przecinek, można użyć ukośnika odwrotnego, aby wypróbować przecinek. 

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'
      - 'appsettings2.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: '{appsettings.json,appsettings2.json}'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
```

## <a name="sync-by-prefix-or-label"></a>Synchronizuj według prefiksu lub etykiety
Określenie prefiksów lub etykiet w akcji synchronizacji spowoduje zsynchronizowanie tylko określonego zestawu. Jest to ważne w przypadku korzystania z ścisłej synchronizacji z wieloma plikami. W zależności od konfiguracji, prefiks lub etykieta mogą być skojarzone z każdym plikiem, a następnie każdy prefiks lub etykieta można synchronizować oddzielnie, aby nic nie zostało zastąpione. Zwykle prefiksy są używane dla różnych aplikacji lub usług, a etykiety są używane w różnych środowiskach. 

Synchronizuj według prefiksu: 

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          prefix: 'Prefix::'
```

Synchronizuj według etykiety: 

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          label: 'Label'

```

## <a name="use-a-dynamic-label-on-sync"></a>Użyj etykiety dynamicznej podczas synchronizacji
Poniższa akcja wstawia dynamiczną etykietę dla każdej synchronizacji, co gwarantuje, że każda Synchronizacja może być jednoznacznie zidentyfikowana i umożliwi zamapowanie zmian w kodzie na zmiany w konfiguracji.

Pierwsza sekcja tego przepływu pracy określa, że akcja jest wyzwalana *w* przypadku *wypychania* zawierającego `appsettings.json` do gałęzi *głównej* . Druga sekcja uruchamia zadanie, które tworzy unikatową etykietę aktualizacji konfiguracji na podstawie skrótu zatwierdzania. Następnie zadanie aktualizuje wystąpienie konfiguracji aplikacji przy użyciu nowych wartości i unikatowych etykiet dla tej aktualizacji.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-azure-key-vault-with-github-action"></a>Używanie Azure Key Vault z akcją GitHub
Deweloperzy korzystający z Azure Key Vault z AppConfiguration powinni używać dwóch oddzielnych plików, zazwyczaj appsettings.jsw i secretreferences.jsw systemie. secretreferences.json będzie zawierać adres URL klucza tajnego magazynu kluczy.

{"Tajemnica": "{ \" URI \" : \" https://myKeyVault.vault.azure.net/secrets/mySecret "} "}

Akcję GitHub można następnie skonfigurować tak, aby przeprowadzać ścisłą synchronizację na appsettings.js, a następnie nieścisłą synchronizację na secretreferences.js. Poniższy przykład wywoła synchronizację, gdy plik zostanie zaktualizowany:

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'
      - 'secretreferences.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          strict: true
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'secretreferences.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          contentType: 'application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8'

```

## <a name="use-max-depth-to-limit-github-action"></a>Użyj maksymalnej głębokości, aby ograniczyć akcję GitHub
Domyślnym zachowaniem dla zagnieżdżonych atrybutów JSON jest spłaszczenie całego obiektu.  Poniższy kod JSON definiuje tę parę klucz-wartość:

| Klucz | Wartość |
| --- | --- |
| Obiekt: wewnętrzny: InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Jeśli zagnieżdżony obiekt jest przeznaczony do wartości wypychanej do wystąpienia konfiguracji, można użyć wartości *głębokości* , aby zatrzymać spłaszczanie na odpowiedniej głębokości. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Ze względu na głębokość 2, Poniższy przykład zwraca teraz następującą parę klucz-wartość:

| Klucz | Wartość |
| --- | --- |
| Obiekt: wewnętrzny | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Informacje o danych wejściowych akcji
Parametry wejściowe określają dane używane przez akcję podczas środowiska uruchomieniowego.  Poniższa tabela zawiera parametry wejściowe akceptowane przez synchronizację konfiguracji aplikacji i oczekiwane wartości dla każdego z nich.  Aby uzyskać więcej informacji na temat danych wejściowych akcji usługi GitHub, zobacz [dokumentację](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)usługi GitHub.

> [!Note]
> W identyfikatorach wejściowych nie jest rozróżniana wielkość liter.


| Nazwa wejściowa | Wymagane? | Wartość |
|----|----|----|
| configurationFile | Tak | Ścieżka względna do pliku konfiguracji w repozytorium.  Wzorce globalizowania są obsługiwane i mogą zawierać wiele plików. |
| format | Tak | Format pliku konfiguracji.  Prawidłowe formaty to: JSON, YAML i właściwości. |
| Parametry połączenia | Tak | Parametry połączenia dla wystąpienia konfiguracji aplikacji. Parametry połączenia powinny być przechowywane jako wpis tajny w repozytorium GitHub, a w przepływie pracy powinna być użyta tylko nazwa klucza tajnego. |
| rozdzielając | Tak | Separator używany podczas spłaszczania pliku konfiguracji do par klucz-wartość.  Prawidłowe wartości to:. , ; : - _ __ / |
| prefiks | Nie | Prefiks, który ma zostać dodany do początku kluczy. |
| label | Nie | Etykieta użyta podczas ustawiania par klucz-wartość. Jeśli nie zostanie określony, zostanie użyta etykieta o wartości null. |
| ściśle | Nie | Wartość logiczna określająca, czy tryb Strict jest włączony. Wartość domyślna to false. |
| ścisł | Nie | Maksymalna głębokość spłaszczania pliku konfiguracji.  Głębokość musi być liczbą dodatnią.  Wartość domyślna nie będzie mieć maksymalnej głębokości. |
| tags | Nie | Określa zestaw tagów dla par klucz-wartość.  Oczekiwany format to skonwertowaneja postać obiektu JSON o następującym kształcie: {[propertyName: String]: String;} Każda właściwość name-value jest tagiem. |

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o akcji usługi GitHub dotyczącej synchronizacji konfiguracji aplikacji oraz sposobie jej użycia do automatyzowania aktualizacji wystąpienia konfiguracji aplikacji. Aby dowiedzieć się, jak usługa Azure App Configuration reaguje na zmiany w parach klucz-wartość, przejdź do następnego [artykułu](./concept-app-configuration-event.md).
