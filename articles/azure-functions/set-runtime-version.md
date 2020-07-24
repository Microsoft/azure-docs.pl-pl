---
title: Jak docelowa Azure Functions wersje środowiska uruchomieniowego
description: Azure Functions obsługuje wiele wersji środowiska uruchomieniowego. Dowiedz się, jak określić wersję środowiska uruchomieniowego aplikacji funkcji hostowanej na platformie Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 3d4e40af1ba1e28bc9e9a433872e1315ffbe7747
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079659"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak docelowa Azure Functions wersje środowiska uruchomieniowego

Aplikacja funkcji jest uruchamiana w określonej wersji środowiska uruchomieniowego Azure Functions. Istnieją trzy wersje główne: [1. x, 2. x i 3. x](functions-versions.md). Domyślnie aplikacje funkcji są tworzone w wersji 3. x środowiska uruchomieniowego. W tym artykule wyjaśniono, jak skonfigurować aplikację funkcji na platformie Azure do uruchamiania w wybranej wersji. Informacje o sposobie konfigurowania lokalnego środowiska projektowego dla określonej wersji znajdują się w temacie [kod i test Azure Functions lokalnie](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Aktualizacje automatyczne i ręczne

Azure Functions umożliwia kierowanie do określonej wersji środowiska uruchomieniowego przy użyciu `FUNCTIONS_EXTENSION_VERSION` Ustawienia aplikacji w aplikacji funkcji. Aplikacja funkcji jest przechowywana w określonej wersji głównej, dopóki użytkownik nie zdecyduje się na przejście do nowej wersji.

Jeśli określisz tylko wersję główną, aplikacja funkcji zostanie automatycznie zaktualizowana do nowej wersji pomocniczej środowiska uruchomieniowego, gdy staną się dostępne. Nowe wersje pomocnicze nie powinny wprowadzać drobnych zmian. W przypadku określenia wersji pomocniczej (na przykład "2.0.12345") aplikacja funkcji jest przypięta do tej konkretnej wersji, dopóki nie zostanie ona jawnie zmieniona.

> [!NOTE]
> Jeśli przywrócisz do określonej wersji Azure Functions, a następnie spróbujesz opublikować ją na platformie Azure przy użyciu programu Visual Studio, zostanie wyświetlone okno dialogowe z monitem o aktualizację do najnowszej wersji lub anulowanie publikacji. Aby tego uniknąć, należy dodać `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` Właściwość w `.csproj` pliku.

Gdy nowa wersja jest publicznie dostępna, w portalu zostanie wyświetlony monit o przeniesienie do tej wersji. Po przejściu do nowej wersji zawsze możesz użyć `FUNCTIONS_EXTENSION_VERSION` Ustawienia aplikacji, aby wrócić do poprzedniej wersji.

W poniższej tabeli przedstawiono `FUNCTIONS_EXTENSION_VERSION` wartości dla każdej wersji głównej, aby włączyć aktualizacje automatyczne:

| Wersja główna | `FUNCTIONS_EXTENSION_VERSION`wartościami |
| ------------- | ----------------------------------- |
| wersji  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Zmiana wersji środowiska uruchomieniowego powoduje ponowne uruchomienie aplikacji funkcji.

## <a name="view-and-update-the-current-runtime-version"></a>Wyświetl i zaktualizuj bieżącą wersję środowiska uruchomieniowego

Możesz zmienić wersję środowiska uruchomieniowego używaną przez aplikację funkcji. Ze względu na potencjalną zmianę można zmienić wersję środowiska uruchomieniowego przed utworzeniem jakichkolwiek funkcji w aplikacji funkcji. 

> [!IMPORTANT]
> Mimo że wersja środowiska uruchomieniowego jest określana na podstawie `FUNCTIONS_EXTENSION_VERSION` Ustawienia, należy wprowadzić tę zmianę w Azure Portal, a nie przez zmianę ustawienia bezpośrednio. Dzieje się tak, ponieważ Portal sprawdza poprawność zmian i wprowadza inne powiązane zmiany stosownie do potrzeb.

### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Przy użyciu Azure Portal nie można zmienić wersji środowiska uruchomieniowego dla aplikacji funkcji, która zawiera już funkcje.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>W interfejsie wiersza polecenia platformy Azure

Możesz również wyświetlać i konfigurować `FUNCTIONS_EXTENSION_VERSION` za pomocą interfejsu wiersza polecenia platformy Azure.

>[!NOTE]
>Ze względu na to, że wersja środowiska uruchomieniowego może mieć wpływ na inne ustawienia, należy zmienić wersję w portalu. Portal automatycznie wykonuje inne potrzebne aktualizacje, takie jak wersja Node.js i stos środowiska uruchomieniowego, gdy zmieniasz wersje środowiska uruchomieniowego.  

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
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Zamień `<function_app>` na nazwę aplikacji funkcji. Zastąp także `<my_resource_group>` nazwą grupy zasobów dla aplikacji funkcji. Ponadto Zastąp `<version>` wartość prawidłową wersją środowiska uruchomieniowego 1. x lub `~2` wersji 2. x.

Możesz uruchomić to polecenie z [Azure Cloud Shell](../cloud-shell/overview.md) , wybierając pozycję **Wypróbuj** w poprzednim przykładzie kodu. Możesz również użyć [interfejsu wiersza polecenia platformy Azure lokalnie](/cli/azure/install-azure-cli) , aby wykonać to polecenie po wykonaniu polecenia [AZ login](/cli/azure/reference-index#az-login) , aby się zalogować.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kierowanie środowiska uruchomieniowego 2,0 w lokalnym środowisku programistycznym](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zobacz informacje o wersji dla wersji środowiska uruchomieniowego](https://github.com/Azure/azure-webjobs-sdk-script/releases)
