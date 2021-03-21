---
title: Używanie parametru JSON content-type dla par kluczy/wartości
titleSuffix: Azure App Configuration
description: Dowiedz się, jak używać typu zawartości JSON dla wartości klucza
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 19de46bc87b72ada221c63e36e87d0545304d344
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122157"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>Wykorzystanie typu zawartości do przechowywania wartości klucza JSON w konfiguracji aplikacji

Dane są przechowywane w konfiguracji aplikacji jako wartości klucza, gdzie wartości są traktowane jako typ ciągu domyślnie. Można jednak określić typ niestandardowy, wykorzystując Właściwość Content-Type skojarzoną z każdą wartością klucz-wartość, dzięki czemu można zachować oryginalny typ danych lub mieć zachowywać się inaczej w zależności od typu zawartości.


## <a name="overview"></a>Omówienie

W obszarze Konfiguracja aplikacji można użyć typu nośnika JSON jako typu zawartości klucza, aby skorzystać z korzyści, takich jak:
- **Prostsze zarządzanie danymi**: zarządzanie kluczowymi wartościami, takimi jak tablice, stanie się znacznie łatwiejsze w Azure Portal.
- **Ulepszony eksport danych**: typy pierwotne, tablice i obiekty JSON zostaną zachowane podczas eksportowania danych.
- **Natywna obsługa dostawcy konfiguracji aplikacji**: klucz-wartości z zawartością JSON — typ będzie działał prawidłowo, gdy są używane przez biblioteki dostawcy konfiguracji aplikacji w aplikacjach.

#### <a name="valid-json-content-type"></a>Prawidłowy typ zawartości JSON

Typy nośników, zgodnie z definicją w [tym miejscu](https://www.iana.org/assignments/media-types/media-types.xhtml), można przypisać do typu zawartości skojarzonego z każdą wartością klucza.
Typ nośnika składa się z typu i podtypu. Jeśli typ to `application` i podtyp (lub sufiks) `json` , typ nośnika będzie uznawany za prawidłowy typ zawartości JSON.
Przykłady prawidłowych typów zawartości JSON:

- application/json
- Aplikacja/działanie + JSON
- application/vnd. Foobar + JSON; charset = utf-8

#### <a name="valid-json-values"></a>Prawidłowe wartości JSON

Gdy klucz-wartość ma typ zawartości JSON, jego wartość musi być w prawidłowym formacie JSON, aby klienci mogli prawidłowo je przetworzyć. W przeciwnym razie klienci mogą kończyć się niepowodzeniem lub powracać i traktować jako format ciągu.
Oto przykłady prawidłowych wartości JSON:

- "John Doe"
- 723
- fałsz
- null
- "2020-01-01T12:34:56.789 Z"
- [1, 2, 3, 4]
- {"ObjectSetting": {"cel": {"default": true, "Level": "informacje"}}}

> [!NOTE]
> W pozostałej części tego artykułu dowolna wartość klucza w konfiguracji aplikacji, która ma prawidłowy typ zawartości JSON i prawidłowa wartość JSON, będzie określana jako **wartość klucza JSON**. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> * Utwórz wartości klucza JSON w konfiguracji aplikacji.
> * Importuj wartości klucza JSON z pliku JSON.
> * Wyeksportuj wartości klucza JSON do pliku JSON.
> * Korzystaj z wartości klucza JSON w aplikacjach.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten samouczek wymaga wersji 2.10.0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-an-app-configuration-store"></a>Tworzenie magazynu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>Utwórz wartości klucza JSON w konfiguracji aplikacji

Wartości klucza JSON można utworzyć przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub przez zaimportowanie z pliku JSON. W tej sekcji znajdziesz instrukcje dotyczące tworzenia tych samych wartości klucza JSON przy użyciu wszystkich trzech metod.

### <a name="create-json-key-values-using-azure-portal"></a>Utwórz wartości klucza JSON przy użyciu Azure Portal

Przejdź do magazynu konfiguracji aplikacji, a następnie wybierz pozycję **Eksplorator konfiguracji**  >  **Utwórz**  >  **klucz-wartość** , aby dodać następujące pary klucz-wartość:

| Klucz | Wartość | Typ zawartości |
|---|---|---|
| Ustawienia: BackgroundColor | Znacznika | application/json |
| Ustawienia: FontSize | 24 | application/json |
| Ustawienia: UseDefaultRouting | fałsz | application/json |
| Ustawienia: BlockedUsers | null | application/json |
| Ustawienia: ReleaseDate | "2020-08-04T12:34:56.789 Z" | application/json |
| Ustawienia: RolloutPercentage | [25, 50, 75100] | application/json |
| Ustawienia: rejestrowanie | {"Test": {"Level": "debug"}, "prod": {"Level": "Warning"}} | application/json |

Pozostaw pustą **etykietę** i wybierz pozycję **Zastosuj**.

### <a name="create-json-key-values-using-azure-cli"></a>Tworzenie wartości klucza JSON przy użyciu interfejsu wiersza polecenia platformy Azure

Następujące polecenia spowodują utworzenie wartości klucza JSON w magazynie konfiguracji aplikacji. Zamień `<appconfig_name>` na nazwę magazynu konfiguracji aplikacji.

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> Jeśli używasz interfejsu wiersza polecenia platformy Azure lub Azure Cloud Shell, aby utworzyć wartości klucza JSON, podana wartość musi być ciągiem JSON o zmienionym znaczeniu.

### <a name="import-json-key-values-from-a-file"></a>Importowanie wartości klucza JSON z pliku

Utwórz plik JSON o nazwie `Import.json` z następującą zawartością i zaimportuj go jako wartości klucza do konfiguracji aplikacji:

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> `--depth`Argument jest używany do spłaszczania danych hierarchicznych z pliku do par klucz-wartość. W tym samouczku określono głębię pokazującą, że można również przechowywać obiekty JSON jako wartości w konfiguracji aplikacji. Jeśli głębokość nie zostanie określona, obiekty JSON zostaną domyślnie spłaszczone na najniższym poziomie.

Utworzone wartości klucza JSON powinny wyglądać następująco w temacie Konfiguracja aplikacji:

![Magazyn konfiguracji zawierający wartości klucza JSON](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>Eksportowanie wartości klucza JSON do pliku

Jedną z głównych korzyści wynikających z używania wartości klucza JSON jest możliwość zachowania oryginalnego typu danych wartości podczas eksportowania. Jeśli klucz-wartość w konfiguracji aplikacji nie ma typu zawartości JSON, jego wartość będzie traktowana jako ciąg. 

Należy wziąć pod uwagę te kluczowe wartości bez typu zawartości JSON:

| Klucz | Wartość | Typ zawartości |
|---|---|---|
| Ustawienia: FontSize | 24 | |
| Ustawienia: UseDefaultRouting | fałsz | |

Po wyeksportowaniu tych wartości klucza do pliku JSON wartości zostaną wyeksportowane jako ciągi:
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

Jednak w przypadku eksportowania wartości klucza JSON do pliku wszystkie wartości będą zachować oryginalny typ danych. Aby to sprawdzić, należy wyeksportować wartości klucza z konfiguracji aplikacji do pliku JSON. Zobaczysz, że wyeksportowany plik ma taką samą zawartość jak `Import.json` wcześniej zaimportowany plik.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> Jeśli magazyn konfiguracji aplikacji zawiera pewne wartości klucza bez typu zawartości JSON, zostaną one również wyeksportowane do tego samego pliku w formacie ciągu. Jeśli chcesz wyeksportować tylko wartości klucza JSON, Przypisz unikatową etykietę lub prefiks do wartości klucza JSON i użyj filtrowania etykiet lub prefiksów podczas eksportowania.


## <a name="consuming-json-key-values-in-applications"></a>Zużywanie wartości klucza JSON w aplikacjach

Najprostszym sposobem korzystania z wartości klucza JSON w aplikacji jest użycie bibliotek dostawcy konfiguracji aplikacji. W przypadku bibliotek dostawców nie trzeba implementować specjalnej obsługi wartości klucza JSON w aplikacji. Zostaną one przeanalizowane i przekonwertowane, aby odpowiadały konfiguracji natywnej aplikacji.

Na przykład, jeśli w konfiguracji aplikacji jest następująca kluczowa wartość:

| Klucz | Wartość | Typ zawartości |
|---|---|---|
| Ustawienia | {"FontSize": 24, "UseDefaultRouting": false} | application/json |

Konfiguracja aplikacji .NET będzie miała następujące kluczowe wartości:

| Klucz | Wartość |
|---|---|
| Ustawienia: FontSize | 24 |
| Ustawienia: UseDefaultRouting | fałsz |

Dostęp do nowych kluczy można uzyskać bezpośrednio lub można [powiązać wartości konfiguracji z wystąpieniami obiektów .NET](/aspnet/core/fundamentals/configuration/#bind-hierarchical-configuration-data-using-the-options-pattern).


> [!Important]
> Natywna obsługa wartości klucza JSON jest dostępna w dostawcy konfiguracji platformy .NET w wersji 4.0.0 (lub nowszej). Aby uzyskać więcej informacji, zobacz sekcję [*następne kroki*](#next-steps) .

Jeśli używasz zestawu SDK lub interfejsu API REST do odczytywania wartości kluczy z konfiguracji aplikacji, na podstawie typu zawartości, aplikacja jest odpowiedzialna za analizowanie wartości klucza JSON i wartości.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z wartości klucza JSON w magazynie konfiguracji aplikacji, Utwórz aplikację do konsumowania następujących wartości klucza:

* [ASP.NET Core — Szybki Start](./quickstart-aspnet-core-app.md)
    * Wymaganie wstępne: [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) Package v 4.0.0 lub nowszego.

* [.NET Core — Szybki Start](./quickstart-dotnet-core-app.md)
    * Wymaganie wstępne: [Microsoft.Extensions.Configwersja. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) pakiet v 4.0.0 lub nowszy.
