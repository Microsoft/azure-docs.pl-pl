---
title: Alternatywy dla samodzielnego hostingu portalu dla deweloperów
titleSuffix: Azure API Management
description: Dowiedz się więcej o alternatywnych podejściach, których możesz użyć podczas samodzielnego hostuj portal deweloperów w usłudze Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: cb14ecd05c1590667ac9b5618b3f0de9da30ce96
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741871"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>Alternatywne podejścia do portalu dla deweloperów samodzielnie hostów

Istnieje kilka alternatywnych metod, które można poznać podczas [samodzielnego hostowanie portalu dla deweloperów:](developer-portal-self-host.md)

* Użyj kompilacji produkcyjnych projektanta i wydawcy.

* Użyj aplikacji funkcji platformy Azure, aby opublikować portal.

* Aby skrócić czas ładowania stron, przed plikami portalu Content Delivery Network (CDN).

Ten artykuł zawiera informacje na temat każdej z tych metod. 

Jeśli jeszcze tego nie zrobiono, [](developer-portal-self-host.md#step-1-set-up-local-environment) skonfiguruj środowisko lokalne dla najnowszej wersji portalu dla deweloperów.

## <a name="build-for-production"></a>Kompilowanie na platformie produkcyjnej

Jeśli chcesz hostować środowisko deweloperskie portalu w trybie online na potrzeby współpracy, użyj kompilacji produkcyjnych projektanta i wydawcy. Kompilacje produkcyjne obejmują pliki, wykluczają mapy źródłowe itp.

Utwórz pakiet w `./dist/designer` katalogu, uruchamiając polecenie :

```sh
npm run build-designer
```

Wynikiem jest aplikacja jednostronicowa, więc nadal można ją wdrożyć na statycznym hoście internetowym, takim jak Azure Blob Storage statyczna witryna internetowa.

Podobnie umieść skompilowanego i zoptymalizowanego wydawcę w `./dist/publisher` folderze :

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>Publikowanie portalu za pomocą aplikacji funkcji

Uruchom krok publikowania w chmurze jako alternatywę dla wykonywania go lokalnie.

Aby zaimplementować publikowanie za pomocą aplikacji funkcji platformy Azure, potrzebne są następujące wymagania wstępne:

- [Utwórz funkcję platformy Azure.](../azure-functions/functions-create-first-azure-function.md) Funkcja musi być funkcją języka JavaScript.
- Zainstaluj Azure Functions Core Tools:
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>Krok 1. Konfigurowanie magazynu wyjściowego

Przekazywanie zawartości bezpośrednio do hostowej witryny internetowej ("$web" magazynu wyjściowego) zamiast folderu lokalnego. Skonfiguruj tę zmianę w `./src/config.publish.json` pliku:

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>Krok 2. Kompilowanie i wdrażanie aplikacji funkcji

W folderze znajduje się przykładowa funkcja wyzwalacza `./examples` HTTP. Aby go skompilować i umieścić w `./dist/function` , uruchom następujące polecenie:

```sh
npm run build-function
```

Następnie zaloguj się do interfejsu wiersza polecenia platformy Azure i wdaj go:

```sh
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

Po wdrożeniu można wywołać ją za pomocą wywołania HTTP:

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>Hosting i cdn

W [przypadku samodzielnego hostowanie portalu dla](developer-portal-self-host.md) deweloperów zalecamy użycie konta usługi Azure Storage do hostowanie witryny internetowej. Pliki można jednak publikować za pośrednictwem dowolnego rozwiązania, w tym usług dostawców hostingu.

Aby skrócić czas ładowania stron, można również Content Delivery Network (CDN). Zalecamy używanie [Azure CDN](https://azure.microsoft.com/services/cdn/).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o portalu dla deweloperów:

- [Portal deweloperów usługi Azure API Management — omówienie](api-management-howto-developer-portal.md)
