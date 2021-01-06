---
title: Jak docelowa Azure Functions wersje środowiska uruchomieniowego
description: Azure Functions obsługuje wiele wersji środowiska uruchomieniowego. Dowiedz się, jak określić wersję środowiska uruchomieniowego aplikacji funkcji hostowanej na platformie Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 46bf7849888033b2bbb7e9b9669ee3eae4de10e9
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916528"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak docelowa Azure Functions wersje środowiska uruchomieniowego

Aplikacja funkcji jest uruchamiana w określonej wersji środowiska uruchomieniowego Azure Functions. Istnieją trzy wersje główne: [1. x, 2. x i 3. x](functions-versions.md). Domyślnie aplikacje funkcji są tworzone w wersji 3. x środowiska uruchomieniowego. W tym artykule wyjaśniono, jak skonfigurować aplikację funkcji na platformie Azure do uruchamiania w wybranej wersji. Informacje o sposobie konfigurowania lokalnego środowiska projektowego dla określonej wersji znajdują się w temacie [kod i test Azure Functions lokalnie](functions-run-local.md).

Sposób ręcznego określania określonej wersji zależy od tego, czy korzystasz z systemu Windows, czy Linux.

## <a name="automatic-and-manual-version-updates"></a>Aktualizacje automatyczne i ręczne

_Ta sekcja nie ma zastosowania w przypadku uruchamiania aplikacji funkcji [w systemie Linux](#manual-version-updates-on-linux)._

Azure Functions umożliwia określanie konkretnej wersji środowiska uruchomieniowego w systemie Windows przy użyciu `FUNCTIONS_EXTENSION_VERSION` Ustawienia aplikacji w aplikacji funkcji. Aplikacja funkcji jest przechowywana w określonej wersji głównej, dopóki użytkownik nie zdecyduje się na przejście do nowej wersji. Jeśli określisz tylko wersję główną, aplikacja funkcji zostanie automatycznie zaktualizowana do nowej wersji pomocniczej środowiska uruchomieniowego, gdy staną się dostępne. Nowe wersje pomocnicze nie powinny wprowadzać drobnych zmian. 

W przypadku określenia wersji pomocniczej (na przykład "2.0.12345") aplikacja funkcji jest przypięta do tej konkretnej wersji, dopóki nie zostanie ona jawnie zmieniona. Starsze wersje pomocnicze są regularnie usuwane ze środowiska produkcyjnego. Po takim wystąpieniu aplikacja funkcji jest uruchamiana w najnowszej wersji, a nie w wersji ustawionej w programie `FUNCTIONS_EXTENSION_VERSION` . W związku z tym należy szybko rozwiązać wszelkie problemy z aplikacją funkcji wymagającą określonej wersji pomocniczej, aby można było użyć wersji głównej. Usuwanie wersji pomocniczej są ogłaszane w [anonsach App Service](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> Jeśli przywrócisz do określonej wersji głównej Azure Functions, a następnie spróbujesz opublikować ją na platformie Azure przy użyciu programu Visual Studio, zostanie wyświetlone okno dialogowe z monitem o aktualizację do najnowszej wersji lub anulowanie publikacji. Aby tego uniknąć, należy dodać `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` Właściwość w `.csproj` pliku.

Gdy nowa wersja jest publicznie dostępna, w portalu zostanie wyświetlony monit o przeniesienie do tej wersji. Po przejściu do nowej wersji zawsze możesz użyć `FUNCTIONS_EXTENSION_VERSION` Ustawienia aplikacji, aby wrócić do poprzedniej wersji.

W poniższej tabeli przedstawiono `FUNCTIONS_EXTENSION_VERSION` wartości dla każdej wersji głównej, aby włączyć aktualizacje automatyczne:

| Wersja główna | `FUNCTIONS_EXTENSION_VERSION` wartościami |
| ------------- | ----------------------------------- |
| wersji  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Zmiana wersji środowiska uruchomieniowego powoduje ponowne uruchomienie aplikacji funkcji.

## <a name="view-and-update-the-current-runtime-version"></a>Wyświetl i zaktualizuj bieżącą wersję środowiska uruchomieniowego

_Ta sekcja nie ma zastosowania w przypadku uruchamiania aplikacji funkcji [w systemie Linux](#manual-version-updates-on-linux)._

Możesz zmienić wersję środowiska uruchomieniowego używaną przez aplikację funkcji. Ze względu na potencjalną zmianę można zmienić wersję środowiska uruchomieniowego przed utworzeniem jakichkolwiek funkcji w aplikacji funkcji. 

> [!IMPORTANT]
> Mimo że wersja środowiska uruchomieniowego jest określana na podstawie `FUNCTIONS_EXTENSION_VERSION` Ustawienia, należy wprowadzić tę zmianę w Azure Portal, a nie przez zmianę ustawienia bezpośrednio. Dzieje się tak, ponieważ Portal sprawdza poprawność zmian i wprowadza inne powiązane zmiany stosownie do potrzeb.

# <a name="portal"></a>[Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Przy użyciu Azure Portal nie można zmienić wersji środowiska uruchomieniowego dla aplikacji funkcji, która zawiera już funkcje.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

Możesz również wyświetlać i konfigurować `FUNCTIONS_EXTENSION_VERSION` za pomocą interfejsu wiersza polecenia platformy Azure.  

Korzystając z interfejsu wiersza polecenia platformy Azure, Wyświetl bieżącą wersję środowiska uruchomieniowego za pomocą poleceń [AZ functionapp config AppSettings Set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

W tym kodzie Zamień na `<function_app>` nazwę aplikacji funkcji. Zastąp także `<my_resource_group>` nazwą grupy zasobów dla aplikacji funkcji. 

Zobaczysz `FUNCTIONS_EXTENSION_VERSION` Poniższy wynik, który został obcięty do przejrzystości:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

Możesz zaktualizować `FUNCTIONS_EXTENSION_VERSION` Ustawienia w aplikacji funkcji za pomocą polecenia [AZ functionapp config AppSettings Set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Zamień `<FUNCTION_APP>` na nazwę aplikacji funkcji. Zastąp także `<RESOURCE_GROUP>` nazwą grupy zasobów dla aplikacji funkcji. Ponadto Zastąp `<VERSION>` wartość określoną wersją lub `~3` , `~2` lub `~1` .

Możesz uruchomić to polecenie z [Azure Cloud Shell](../cloud-shell/overview.md) , wybierając pozycję **Wypróbuj** w poprzednim przykładzie kodu. Możesz również użyć [interfejsu wiersza polecenia platformy Azure lokalnie](/cli/azure/install-azure-cli) , aby wykonać to polecenie po wykonaniu polecenia [AZ login](/cli/azure/reference-index#az-login) , aby się zalogować.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić środowisko uruchomieniowe Azure Functions, użyj następującego polecenia cmdlet: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Zamień `<FUNCTION_APP>` na nazwę aplikacji funkcji i `<RESOURCE_GROUP>` . Bieżąca wartość `FUNCTIONS_EXTENSION_VERSION` Ustawienia jest zwracana w tabeli skrótów.

Aby zmienić środowisko uruchomieniowe funkcji, użyj następującego skryptu:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Tak jak wcześniej, Zastąp ciąg `<FUNCTION_APP>` nazwą swojej aplikacji funkcji i `<RESOURCE_GROUP>` nazwą grupy zasobów. Ponadto Zastąp `<VERSION>` wartość określoną wersją lub wersją główną, taką jak `~2` lub `~3` . Zaktualizowaną wartość ustawienia można sprawdzić `FUNCTIONS_EXTENSION_VERSION` w tabeli zwracanych skrótów. 

---

Aplikacja funkcji zostanie ponownie uruchomiona po wprowadzeniu zmian w ustawieniach aplikacji.

## <a name="manual-version-updates-on-linux"></a>Aktualizacje wersji ręcznej w systemie Linux

Aby przypiąć aplikację funkcji systemu Linux do określonej wersji hosta, należy określić adres URL obrazu w polu "LinuxFxVersion" w konfiguracji lokacji. Na przykład: Jeśli chcemy przypiąć aplikację funkcji Node 10 w celu wypowiedzenia wersji hosta 3.0.13142-

W przypadku **usługi App Service/elastycznych aplikacji Premium** — ustaw wartość `LinuxFxVersion` `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice` .

W przypadku **aplikacji do użycia** dla systemu Linux — ustaw wartość `LinuxFxVersion` `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10` .


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli-linux)

Można wyświetlać i konfigurować `LinuxFxVersion` za pomocą interfejsu wiersza polecenia platformy Azure.  

Korzystając z interfejsu wiersza polecenia platformy Azure, Wyświetl bieżącą wersję środowiska uruchomieniowego, używając [AZ functionapp config show](/cli/azure/functionapp/config) .

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group>
```

W tym kodzie Zamień na `<function_app>` nazwę aplikacji funkcji. Zastąp także `<my_resource_group>` nazwą grupy zasobów dla aplikacji funkcji. 

Zobaczysz `linuxFxVersion` Poniższy wynik, który został obcięty do przejrzystości:

```output
{
  ...

  "kind": null,
  "limits": null,
  "linuxFxVersion": <LINUX_FX_VERSION>,
  "loadBalancing": "LeastRequests",
  "localMySqlEnabled": false,
  "location": "West US",
  "logsDirectorySizeLimit": 35,
   ...
}
```

Możesz zaktualizować `linuxFxVersion` Ustawienia w aplikacji funkcji za pomocą polecenia [AZ functionapp config Set](/cli/azure/functionapp/config) .

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Zamień `<FUNCTION_APP>` na nazwę aplikacji funkcji. Zastąp także `<RESOURCE_GROUP>` nazwą grupy zasobów dla aplikacji funkcji. Należy również zastąpić `<LINUX_FX_VERSION>` wartościami opisanymi powyżej.

Możesz uruchomić to polecenie z [Azure Cloud Shell](../cloud-shell/overview.md) , wybierając pozycję **Wypróbuj** w poprzednim przykładzie kodu. Możesz również użyć [interfejsu wiersza polecenia platformy Azure lokalnie](/cli/azure/install-azure-cli) , aby wykonać to polecenie po wykonaniu polecenia [AZ login](/cli/azure/reference-index#az-login) , aby się zalogować.


Podobnie aplikacja funkcji jest uruchamiana ponownie po wprowadzeniu zmian w konfiguracji lokacji.

> [!NOTE]
> Zwróć uwagę, że ustawienie `LinuxFxVersion` na adres URL obrazu bezpośrednio dla aplikacji do użycia spowoduje wyróżnienie ich od symboli zastępczych i innych optymalizacji zimnego startu.

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kierowanie środowiska uruchomieniowego 2,0 w lokalnym środowisku programistycznym](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zobacz informacje o wersji dla wersji środowiska uruchomieniowego](https://github.com/Azure/azure-webjobs-sdk-script/releases)
