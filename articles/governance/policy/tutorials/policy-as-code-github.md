---
title: 'Samouczek: implementowanie Azure Policy jako kodu w usłudze GitHub'
description: W tym samouczku zaimplementowano Azure Policy jako przepływ pracy kodu z użyciem operacji eksportowania, GitHub i przepływów pracy usługi GitHub
ms.date: 10/20/2020
ms.topic: tutorial
ms.openlocfilehash: 76a46adc3fc8efab4f7a2d6e656e83c2537dd037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92326158"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>Samouczek: implementowanie Azure Policy jako kodu w usłudze GitHub

Azure Policy jako kod przepływu pracy umożliwia zarządzanie definicjami zasad i przypisaniami jako kod, sterowanie cyklem życia aktualizacji tych definicji oraz automatyzowanie walidacji wyników zgodności. W ramach tego samouczka nauczysz się korzystać z funkcji Azure Policy w usłudze GitHub w celu utworzenia procesu cyklu życia. Do zadań tych należą:

> [!div class="checklist"]
> - Eksportowanie definicji zasad i przypisań do usługi GitHub
> - Obiekty zasad wypychania zaktualizowane w usłudze GitHub do platformy Azure
> - Wyzwalanie skanowania zgodności z poziomu akcji GitHub

Jeśli chcesz przypisać zasady w celu zidentyfikowania bieżącego stanu zgodności istniejących zasobów, w artykule szybki start wyjaśnimy, jak to zrobić.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- Przejrzyj [projekt Azure Policy jako przepływ pracy kodu](../concepts/policy-as-code.md) , aby zrozumieć wzorce projektowania używane w tym samouczku.

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Eksportowanie Azure Policy obiektów z Azure Portal

Aby wyeksportować definicję zasad z Azure Portal, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

1. Wybierz pozycję **definicje** po lewej stronie Azure Policy.

1. Użyj przycisku **Eksportuj definicje** lub wybierz wielokropek w wierszu definicji zasad, a następnie wybierz pozycję **Eksportuj definicję**.

1. Wybierz przycisk **Zaloguj się przy użyciu usługi GitHub** . Jeśli nie masz jeszcze uwierzytelnienia w usłudze GitHub w celu autoryzowania Azure Policy eksportowania zasobu, sprawdź, czy w nowym otwartym oknie są dostępne wymagania dotyczące [akcji GitHub](https://github.com/features/actions) , a następnie wybierz pozycję **Autoryzuj AzureGitHubActions** , aby kontynuować proces eksportowania. Po zakończeniu nowe okno zostanie automatycznie zamknięte.

1. Na karcie **podstawy** ustaw poniższe opcje, a następnie wybierz kartę **zasady** lub przycisk **Dalej: zasady** w dolnej części strony.

   - **Filtr repozytorium**: Ustaw _Moje repozytoria_ , aby wyświetlić tylko własne repozytoria lub _wszystkie_ repozytoria, aby zobaczyć, że masz dostęp do akcji usługi GitHub.
   - **Repozytorium**: Ustaw repozytorium, do którego chcesz wyeksportować zasoby Azure Policy.
   - **Gałąź**: Ustaw gałąź w repozytorium. Użycie gałęzi innej niż domyślna to dobry sposób na zweryfikowanie aktualizacji przed ich scaleniem w kodzie źródłowym.
   - **Katalog**: _folder poziomu głównego_ , do którego mają zostać wyeksportowane zasoby Azure Policy. Podfoldery w tym katalogu są tworzone na podstawie tego, jakie zasoby są eksportowane.

1. Na karcie **zasady** Ustaw zakres wyszukiwania, wybierając wielokropek i wybierz kombinację grup zarządzania, subskrypcji lub grup zasobów.
   
1. Użyj przycisku **Dodaj definicje zasad** , aby przeszukać zakres obiektów do wyeksportowania. W otwartym oknie Wybierz każdy obiekt do wyeksportowania. Filtrowanie zaznaczenia według pola wyszukiwania lub typu. Po wybraniu wszystkich obiektów do wyeksportowania Użyj przycisku **Dodaj** w dolnej części strony.

1. Dla każdego zaznaczonego obiektu wybierz odpowiednie opcje eksportu, takie jak _tylko definicje_ lub _Definicja i przypisania_ dla definicji zasad. Następnie wybierz kartę **Recenzja + eksport** lub przycisk **Dalej: recenzja + eksport** w dolnej części strony.

   > [!NOTE]
   > Jeśli wybrano opcję _Definicja opcji i przypisania_ , eksportowane są tylko przypisania zasad w zakresie ustawionym przez filtr po dodaniu definicji zasad.

1. Na karcie **Recenzja i eksportowanie** Sprawdź zgodność szczegółów, a następnie użyj przycisku **Eksportuj** w dolnej części strony.

1. Sprawdź repozytorium GitHub, gałąź i _folder poziomu głównego_ , aby zobaczyć, że wybrane zasoby zostały teraz wyeksportowane do kontroli źródła.

Zasoby Azure Policy są eksportowane do następującej struktury w wybranym repozytorium GitHub i _folderze poziomu głównego_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>Obiekty zasad wypychania zaktualizowane w usłudze GitHub do platformy Azure

1. Po wyeksportowaniu obiektów zasad tworzony jest również plik [przepływu pracy usługi GitHub](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) o nazwie `.github/workflows/manage-azure-policy-<randomLetters>.yml` .

   > [!NOTE]
   > Plik przepływu pracy w usłudze GitHub jest tworzony za każdym razem, gdy jest używany eksport. Każde wystąpienie pliku jest specyficzne dla opcji podczas tej akcji eksportowania.

1. Ten plik przepływu pracy używa akcji [zarządzaj Azure Policy](https://github.com/marketplace/actions/manage-azure-policy) , aby wypchnąć zmiany wprowadzone do wyeksportowanych obiektów zasad w repozytorium GitHub z powrotem do Azure Policy. Domyślnie akcja traktuje i synchronizuje tylko te pliki, które różnią się od istniejących na platformie Azure. Można również użyć `assignments` parametru w akcji, aby synchronizować tylko zmiany wprowadzone do określonych plików przydziału. Tego parametru można użyć do zastosowania przypisań zasad tylko dla określonego środowiska. Aby uzyskać więcej informacji, zobacz [plik readme dotyczący zarządzania repozytorium Azure Policy](https://github.com/Azure/manage-azure-policy).

1. Domyślnie przepływ pracy musi być wyzwalany ręcznie. Aby to zrobić, użyj **akcji** w serwisie GitHub, wybierz `manage-azure-policy-<randomLetters>` przepływ pracy, wybierz pozycję **Uruchom przepływ pracy**, a następnie ponownie **Uruchom przepływ pracy** .

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="Zrzut ekranu przedstawiający przyciski akcja, przepływ pracy i Uruchom przepływ pracy w interfejsie sieci Web usługi GitHub.":::

   > [!NOTE]
   > Plik przepływu pracy musi znajdować się w gałęzi domyślnej, aby można go było wykryć i uruchomić ręcznie.

1. Przepływ pracy synchronizuje zmiany wprowadzone w obiektach zasad z platformą Azure i zapewnia stan w dziennikach.

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="Zrzut ekranu przedstawiający przepływ pracy i zarejestrowane szczegóły w dziennikach.":::

1. Przepływ pracy dodaje także szczegółowe informacje w Azure Policy obiektów do `properties.metadata` śledzenia.

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="Zrzut ekranu definicji Azure Policy w Azure Portal aktualizowany za pomocą metadanych specyficznych dla akcji usługi GitHub.":::

### <a name="trigger-compliance-scans-using-github-action"></a>Wyzwalanie skanowania zgodności przy użyciu akcji GitHub

Za pomocą [akcji skanowania zgodności Azure Policy](https://github.com/marketplace/actions/azure-policy-compliance-scan) można wyzwolić skanowanie oceny zgodności na żądanie z [przepływu pracy](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) w usłudze GitHub dla jednego lub wielu zasobów, grup zasobów lub subskrypcji, a także zmienić ścieżkę przepływu pracy na podstawie stanu zgodności tych zasobów. Można również skonfigurować przepływ pracy do uruchamiania w zaplanowanym czasie w celu uzyskania najnowszego stanu zgodności w dogodnym czasie. Opcjonalnie ta akcja usługi GitHub umożliwia również generowanie raportu dotyczącego stanu zgodności skanowanych zasobów na potrzeby dalszej analizy lub do archiwizacji.

W poniższym przykładzie uruchomiono skanowanie zgodności dla subskrypcji. 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>Przegląd

W tym samouczku pomyślnie wykonano następujące czynności:

> [!div class="checklist"]
> - Eksportowanie definicji zasad i przypisań do usługi GitHub
> - Wypchnięcie obiektów zasad zaktualizowane w usłudze GitHub na platformę Azure
> - Wyzwolenie skanowania zgodności z akcji GitHub

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o strukturach definicji zasad, zapoznaj się z artykułem:

> [!div class="nextstepaction"]
> [Struktura definicji zasad platformy Azure](../concepts/definition-structure.md)