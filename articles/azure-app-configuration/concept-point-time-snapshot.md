---
title: Pobieranie par klucz-wartość z punktu w czasie
titleSuffix: Azure App Configuration
description: Pobieranie starych par klucz-wartość przy użyciu migawek do momentu w konfiguracji aplikacji platformy Azure
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: b706b5d5ec68daa10fd6eac237b7b7416764167b
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830113"
---
# <a name="point-in-time-snapshot"></a>Migawka punktu w czasie

Konfiguracja aplikacji platformy Azure zachowuje rejestr zmian wprowadzonych w wartościach klucza. Ten rekord zawiera oś czasu zmian klucza i wartości. Można odtworzyć historię dowolnej wartości klucza i w dowolnym momencie podać jej wartość przeszłą w okresie historii kluczy (7 dni dla magazynów w warstwie Bezpłatna lub 30 dni dla magazynów w warstwie Standardowa). Korzystając z tej funkcji, możesz "podróż czasowa" do tyłu i pobrać starą wartość klucza. Można na przykład odzyskać ustawienia konfiguracji używane przed ostatnim wdrożeniem, aby przywrócić poprzednią konfigurację aplikacji.

## <a name="key-value-retrieval"></a>Pobieranie wartości klucza

Za pomocą Azure Portal lub interfejsu wiersza polecenia można pobrać przeszłe wartości klucza. W interfejsie wiersza polecenia platformy Azure Użyj `az appconfig revision list` , dodając odpowiednie parametry, aby pobrać wymagane wartości.  Określ wystąpienie konfiguracji aplikacji platformy Azure, podając nazwę magazynu ( `--name <app-config-store-name>` ) lub korzystając z parametrów połączenia ( `--connection-string <your-connection-string>` ). Ogranicz dane wyjściowe, określając określony punkt w czasie ( `--datetime` ) i określając maksymalną liczbę elementów do zwrócenia ( `--top` ).

Jeśli nie masz zainstalowanego lokalnie interfejsu wiersza polecenia platformy Azure, możesz użyć Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pobierz wszystkie zapisane zmiany wartości kluczy.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name>.
```

Pobierz wszystkie zarejestrowane zmiany klucza `environment` oraz etykiet `test` i `prod` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

Pobierz wszystkie zarejestrowane zmiany w hierarchicznym obszarze klucza `environment:prod` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

Pobierz wszystkie zarejestrowane zmiany klucza `color` w określonym punkcie w czasie.

```azurecli-interactive
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Pobranie ostatnich 10 zarejestrowanych zmian do wartości kluczy i zwrócenie tylko wartości dla `key` , `label` i `last_modified` sygnatury czasowej.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej ASP.NET Core](./quickstart-aspnet-core-app.md)  
