---
title: 'Szybki Start: pierwsze zapytanie programu PowerShell'
description: W tym przewodniku szybki start wykonaj kroki umożliwiające włączenie modułu Graf zasobów Azure PowerShell i uruchomienie pierwszego zapytania.
ms.date: 08/10/2020
ms.topic: quickstart
ms.openlocfilehash: 4cc7eb524060f2a797077f38321ae9ce6c9cf267
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88056418"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Azure PowerShell

Pierwszym krokiem do korzystania z usługi Azure Resource Graph jest zainstalowanie modułu dla programu Azure PowerShell. Ten przewodnik Szybki start przeprowadzi Cię przez proces dodawania modułu do instalacji programu Azure PowerShell.

Po zakończeniu tego procesu będziesz mieć moduł dodany do wybranej instalacji programu Azure PowerShell i uruchomisz swoje pierwsze zapytanie usługi Resource Graph.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>Dodaj moduł usługi Resource Graph

Aby włączyć program Azure PowerShell do wykonywania zapytań do usługi Azure Resource Graph, musisz dodać moduł. Tego modułu można używać z zainstalowanym lokalnie programem PowerShell, z [Azure Cloud Shell](https://shell.azure.com)lub z [obrazem platformy Docker programu PowerShell](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Wymagania podstawowe

Moduł usługi Azure Resource Graph wymaga następującego oprogramowania:

- Program Azure PowerShell w wersji 1.0.0 lub nowszej. Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/powershell/azure/install-az-ps).

- Modułu PowerShellGet w wersji 2.0.1 lub nowszej. Jeśli jeszcze nie został on zainstalowany lub zaktualizowany, postępuj zgodnie z [tymi instrukcjami](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Instalowanie modułu

Moduł usługi Resource Graph dla programu PowerShell to **Az.ResourceGraph**.

1. Za pomocą monitu **administracyjnego** programu PowerShell uruchom następujące polecenie:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Sprawdź, czy moduł został zaimportowany, a to Najnowsza wersja (0.7.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Teraz, gdy moduł programu Azure PowerShell został dodany do Twojego wybranego środowiska, nadszedł czas na wypróbowanie prostego zapytania usługi Resource Graph. Zapytanie zwraca pierwsze pięć zasobów platformy Azure o **nazwie** i **typie zasobu** każdego zasobu.

1. Uruchom swoje pierwsze zapytanie usługi Azure Resource Graph za pomocą polecenia cmdlet `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Ponieważ to przykładowe zapytanie nie udostępnia modyfikatora sortowania takiego jak `order by`, wielokrotne uruchomienie tego zapytania będzie prawdopodobnie zwracało inny zestaw zasobów dla każdego żądania.

1. Zaktualizuj zapytanie, dodając modyfikator `order by` do właściwości **Name**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie porządkuje je.

1. Zaktualizuj zapytanie, aby najpierw wykonywało polecenie `order by` w celu sortowania według właściwości **Name**, a następnie polecenie `limit` w celu ograniczenia do pięciu pierwszych wyników:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Gdy ostateczne zapytanie jest uruchamiane kilka razy, przy założeniu, że nic w środowisku nie zmienia się, zwracane wyniki są spójne i uporządkowane według właściwości **nazwy** , ale nadal są ograniczone do pięciu pierwszych wyników.

> [!NOTE]
> Jeśli zapytanie nie zwraca wyników z subskrypcji, do której już masz dostęp, należy zauważyć, że `Search-AzGraph` polecenie cmdlet domyślnie obsługuje subskrypcje w kontekście domyślnym. Aby wyświetlić listę identyfikatorów subskrypcji, które są częścią kontekstu domyślnego, uruchom tę opcję `(Get-AzContext).Account.ExtendedProperties.Subscriptions` , jeśli chcesz przeszukać wszystkie subskrypcje, do których masz dostęp, ale można ustawić PSDefaultParameterValues dla `Search-AzGraph` polecenia cmdlet, uruchamiając polecenie `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć moduł usługi Resource Graph ze środowiska programu Azure PowerShell, możesz to zrobić za pomocą następującego polecenia:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Nie spowoduje to usunięcia pobranego wcześniej pliku modułu. Powoduje tylko usunięcie go z uruchomionej sesji programu PowerShell.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start dodano moduł grafu zasobów do środowiska Azure PowerShell i zostanie uruchomione pierwsze zapytanie. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)