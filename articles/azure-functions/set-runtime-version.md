---
title: Jak kierować do Azure Functions środowiska uruchomieniowego
description: Azure Functions obsługuje wiele wersji środowiska uruchomieniowego. Dowiedz się, jak określić wersję środowiska uruchomieniowego aplikacji funkcji hostowanej na platformie Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ca107ec2f0ce04bf7b1eae3a98087217c267d33d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830920"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak kierować do Azure Functions środowiska uruchomieniowego

Aplikacja funkcji jest uruchamiana w określonej wersji Azure Functions uruchomieniowego. Istnieją trzy wersje główne: [3.x, 2.x i 1.x.](functions-versions.md) Domyślnie aplikacje funkcji są tworzone w wersji 3.x środowiska uruchomieniowego. W tym artykule wyjaśniono, jak skonfigurować aplikację funkcji na platformie Azure do uruchamiania w witrynie z określoną wersją. Aby uzyskać informacje na temat konfigurowania lokalnego środowiska projektowego dla określonej wersji, zobacz Code [and test Azure Functions locally](functions-run-local.md).

Sposób ręcznego kierowania określonej wersji zależy od tego, czy używasz systemu Windows, czy Linux.

## <a name="automatic-and-manual-version-updates"></a>Automatyczne i ręczne aktualizacje wersji

_Ta sekcja nie ma zastosowania w przypadku uruchamiania aplikacji funkcji w [systemie Linux.](#manual-version-updates-on-linux)_

Azure Functions umożliwia określenie określonej wersji środowiska uruchomieniowego w systemie Windows przy użyciu `FUNCTIONS_EXTENSION_VERSION` ustawienia aplikacji w aplikacji funkcji. Aplikacja funkcji jest przechowywana w określonej wersji główne, dopóki jawnie nie zdecydujesz się przejść do nowej wersji. Jeśli określisz tylko wersję główną, aplikacja funkcji zostanie automatycznie zaktualizowana do nowych wersji pomocniczych środowiska uruchomieniowego, gdy staną się dostępne. Nowe wersje pomocnicze nie powinny wprowadzać zmian przerywanych. 

Jeśli określisz wersję pomocniczą (na przykład "2.0.12345"), aplikacja funkcji zostanie przypięta do tej konkretnej wersji do momentu jawnej zmiany. Starsze wersje pomocnicze są regularnie usuwane ze środowiska produkcyjnego. Jeśli wersja pomocnicza zostanie usunięta, aplikacja funkcji wróci do działania w najnowszej wersji, a nie w wersji ustawionej w pliku `FUNCTIONS_EXTENSION_VERSION` . W związku z tym należy szybko rozwiązać wszelkie problemy z aplikacją funkcji, które wymagają określonej wersji pomocniczej. Następnie możesz wrócić do określania wersji docelowej wersji docelowej. Usunięcia wersji pomocniczej są ogłaszane w [App Service anonsach](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> Jeśli przypniesz do określonej wersji Azure Functions, a następnie spróbujesz opublikować na platformie Azure przy użyciu usługi Visual Studio, zostanie wyświetlone okno dialogowe z monitem o aktualizację do najnowszej wersji lub anulowanie publikowania. Aby tego uniknąć, dodaj `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` właściwość w `.csproj` pliku .

Gdy nowa wersja jest publicznie dostępna, monit w portalu umożliwia przejście do tej wersji. Po przeniesieniu do nowej wersji zawsze możesz użyć `FUNCTIONS_EXTENSION_VERSION` ustawienia aplikacji, aby wrócić do poprzedniej wersji.

W poniższej tabeli przedstawiono `FUNCTIONS_EXTENSION_VERSION` wartości poszczególnych wersji głównych w celu włączenia aktualizacji automatycznych:

| Wersja główna | `FUNCTIONS_EXTENSION_VERSION` Wartość |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Zmiana wersji środowiska uruchomieniowego powoduje ponowne uruchomienie aplikacji funkcji.

>[!NOTE]
>Aplikacje funkcji .NET przypięte w celu rezygnacji z automatycznego `~2.0` uaktualniania do programu .NET Core 3.1. Aby dowiedzieć się więcej, [zobacz Zagadnienia dotyczące funkcji w wersji 2.x.](functions-dotnet-class-library.md#functions-v2x-considerations)  

## <a name="view-and-update-the-current-runtime-version"></a>Wyświetlanie i aktualizowanie bieżącej wersji środowiska uruchomieniowego

_Ta sekcja nie ma zastosowania w przypadku uruchamiania aplikacji funkcji w [systemie Linux.](#manual-version-updates-on-linux)_

Możesz zmienić wersję środowiska uruchomieniowego używaną przez aplikację funkcji. Ze względu na potencjalne zmiany przerywania można zmienić wersję środowiska uruchomieniowego tylko przed utworzeniem jakichkolwiek funkcji w aplikacji funkcji. 

> [!IMPORTANT]
> Mimo że wersja środowiska uruchomieniowego jest określana przez ustawienie, tę zmianę należy wprowadzić tylko w Azure Portal, a nie bezpośrednio `FUNCTIONS_EXTENSION_VERSION` zmieniając ustawienie. Jest to spowodowane tym, że portal weryfikuje zmiany i wprowadza inne powiązane zmiany zgodnie z potrzebami.

# <a name="portal"></a>[Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Za pomocą Azure Portal nie można zmienić wersji środowiska uruchomieniowego dla aplikacji funkcji, która już zawiera funkcje.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

Możesz również wyświetlić i ustawić obiekt z `FUNCTIONS_EXTENSION_VERSION` interfejsu wiersza polecenia platformy Azure.  

Za pomocą interfejsu wiersza polecenia platformy Azure wyświetl bieżącą wersję środowiska uruchomieniowego za pomocą polecenia [az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

W tym kodzie zastąp `<function_app>` nazwą swojej aplikacji funkcji. Zastąp `<my_resource_group>` również nazwą grupy zasobów dla aplikacji funkcji. 

Zostanie wyświetlony następujący wynik, który został obcięty `FUNCTIONS_EXTENSION_VERSION` w celu zapewnienia przejrzystości:

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

Ustawienie w aplikacji funkcji można zaktualizować `FUNCTIONS_EXTENSION_VERSION` za pomocą polecenia az [functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Zastąp `<FUNCTION_APP>` nazwą swojej aplikacji funkcji. Zastąp `<RESOURCE_GROUP>` również nazwą grupy zasobów dla aplikacji funkcji. Ponadto zastąp `<VERSION>` konkretną wersją lub `~3` , lub `~2` `~1` .

Wybierz **pozycję Wypróbuj w** poprzednim przykładzie kodu, aby uruchomić polecenie w Azure Cloud Shell . [](../cloud-shell/overview.md) Możesz również uruchomić interfejs wiersza polecenia [platformy Azure lokalnie,](/cli/azure/install-azure-cli) aby wykonać to polecenie. W przypadku uruchamiania lokalnego należy najpierw uruchomić [az login,](/cli/azure/reference-index#az_login) aby się zalogować.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić środowisko Azure Functions uruchomieniowe, użyj następującego polecenia cmdlet: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Zastąp `<FUNCTION_APP>` nazwą aplikacji funkcji i `<RESOURCE_GROUP>` . Bieżąca wartość ustawienia `FUNCTIONS_EXTENSION_VERSION` jest zwracana w tabeli skrótów.

Użyj następującego skryptu, aby zmienić środowisko uruchomieniowe usługi Functions:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Tak jak poprzednio zastąp nazwą aplikacji funkcji, a zastąp nazwą `<FUNCTION_APP>` `<RESOURCE_GROUP>` grupy zasobów. Zastąp również `<VERSION>` konkretną wersję lub wersję główną, taką jak `~2` lub `~3` . Zaktualizowaną wartość ustawienia można sprawdzić `FUNCTIONS_EXTENSION_VERSION` w zwracanej tabeli skrótów. 

---

Aplikacja funkcji zostanie ponownie uruchomiona po wymusieniu zmiany ustawienia aplikacji.

## <a name="manual-version-updates-on-linux"></a>Ręczne aktualizacje wersji w systemie Linux

Aby przypiąć aplikację funkcji systemu Linux do określonej wersji hosta, należy określić adres URL obrazu w polu "LinuxFxVersion" w konfiguracji witryny. Na przykład: jeśli chcemy przypiąć aplikację funkcji węzła 10 do hostów w wersji 3.0.13142 —

W **przypadku usługi App Service dla systemu Linux/elastycznych aplikacji Premium** — ustaw wartość `LinuxFxVersion` `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice` .

W **przypadku aplikacji zużycia systemu Linux** — ustaw wartość `LinuxFxVersion` `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10` .

# <a name="portal"></a>[Portal](#tab/portal)

Wyświetlanie i modyfikowanie ustawień konfiguracji witryny dla aplikacji funkcji nie jest obsługiwane w Azure Portal. Zamiast tego użyj interfejsu wiersza polecenia platformy Azure.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

Możesz wyświetlić i ustawić interfejs wiersza polecenia `LinuxFxVersion` platformy Azure.  

Aby wyświetlić bieżącą wersję środowiska uruchomieniowego, użyj polecenia [z poleceniem az functionapp config show.](/cli/azure/functionapp/config)

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group> --query 'linuxFxVersion' -o tsv
```

W tym kodzie zastąp `<function_app>` nazwą swojej aplikacji funkcji. Zastąp `<my_resource_group>` również nazwą grupy zasobów dla aplikacji funkcji. Zwracana jest `linuxFxVersion` bieżąca wartość .

Aby zaktualizować `linuxFxVersion` ustawienie w aplikacji funkcji, użyj polecenia az [functionapp config set.](/cli/azure/functionapp/config)

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Zastąp `<FUNCTION_APP>` nazwą swojej aplikacji funkcji. Zastąp `<RESOURCE_GROUP>` również nazwą grupy zasobów dla aplikacji funkcji. Ponadto zastąp `<LINUX_FX_VERSION>` wartość wartością określonego obrazu, jak opisano powyżej.

To polecenie można uruchomić z [Azure Cloud Shell,](../cloud-shell/overview.md) wybierając polecenie **Wypróbuj w** poprzednim przykładzie kodu. Możesz również użyć interfejsu wiersza [polecenia platformy Azure lokalnie,](/cli/azure/install-azure-cli) aby wykonać to polecenie po wykonaniu [polecenia az login](/cli/azure/reference-index#az_login) w celu zalogowania.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Azure PowerShell nie można użyć do ustawienia `linuxFxVersion` w tej chwili. Zamiast tego użyj interfejsu wiersza polecenia platformy Azure.

---

Aplikacja funkcji jest uruchamiana ponownie po wymusieniu zmiany konfiguracji witryny.

> [!NOTE]
> W przypadku aplikacji uruchomionych w planie Zużycie ustawienie określonego obrazu może zwiększyć czas `LinuxFxVersion` zimnego rozpoczęcia. Jest to spowodowane tym, że przypinanie do określonego obrazu uniemożliwia u usługom Functions korzystanie z pewnych optymalizacji zimnego startu. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Docelowe środowisko uruchomieniowe 2.0 w lokalnym środowisku dewelopera](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zobacz Informacje o wersji dla wersji środowiska uruchomieniowego](https://github.com/Azure/azure-webjobs-sdk-script/releases)
