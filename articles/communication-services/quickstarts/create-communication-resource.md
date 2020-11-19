---
title: Szybki Start — tworzenie zasobów i zarządzanie nimi w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku szybki start dowiesz się, jak utworzyć pierwszy zasób usługi Azure Communications Services i zarządzać nim.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-net
ms.openlocfilehash: a93ac3b5d988be33c0f27726a75b1006f990d1da
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886103"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>Szybki Start: Tworzenie zasobów usług komunikacyjnych i zarządzanie nimi

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Rozpocznij pracę z usługami Azure Communications Services, udostępniając pierwszy zasób usług komunikacyjnych. Zasoby usług komunikacyjnych mogą być obsługiwane za pomocą Azure Portal lub z biblioteki klienta zarządzania platformy .NET. Biblioteka klienta zarządzania umożliwia tworzenie, konfigurowanie, aktualizowanie i usuwanie zasobów oraz interfejsów za pomocą usługi [Azure Resource Manager](../../azure-resource-manager/management/overview.md)platformy Azure do wdrażania i zarządzania. Wszystkie funkcje dostępne w bibliotekach klienta są dostępne w Azure Portal. 

> [!WARNING]
> Należy pamiętać, że dostępność usług komunikacyjnych jest ograniczona do lokalizacji geograficznej USA w trakcie publicznej wersji zapoznawczej. Należy również pamiętać, że nie można przesłać zasobów komunikacyjnych do innej subskrypcji w ramach publicznej wersji zapoznawczej.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

## <a name="access-your-connection-strings-and-service-endpoints"></a>Uzyskiwanie dostępu do parametrów połączenia i punktów końcowych usługi

Parametry połączeń umożliwiają bibliotekom klienckim usług komunikacyjnych łączenie się i uwierzytelnianie na platformie Azure. Możesz uzyskać dostęp do parametrów połączenia usług komunikacyjnych i punktów końcowych usługi z Azure Portal lub programowo przy użyciu Azure Resource Manager interfejsów API. 

Po przejściu do zasobu usług komunikacyjnych wybierz pozycję **klucze** z menu nawigacji i skopiuj wartości **parametrów połączenia** lub **punktów końcowych** w celu użycia przez biblioteki klienta usług komunikacyjnych. Należy pamiętać, że masz dostęp do kluczy podstawowych i pomocniczych. Może to być przydatne w scenariuszach, w których chcesz zapewnić tymczasowy dostęp do zasobów usług komunikacyjnych do środowiska innego lub przejściowego.

:::image type="content" source="./media/key.png" alt-text="Zrzut ekranu przedstawiający stronę klucza usług komunikacyjnych.":::

## <a name="store-your-connection-string"></a>Przechowywanie parametrów połączenia

Biblioteki klienckie usług komunikacyjnych używają parametrów połączenia do autoryzacji żądań wysyłanych do usług komunikacyjnych. Istnieje kilka opcji przechowywania parametrów połączenia:

* Aplikacja działająca na komputerze stacjonarnym lub na urządzeniu może przechowywać parametry połączenia w pliku **app.config** lub **web.config** . Dodaj parametry połączenia do sekcji **AppSettings** w tych plikach.
* Aplikacja działająca w Azure App Service może przechowywać parametry połączenia w [ustawieniach aplikacji App Service](../../app-service/configure-common.md). Dodaj parametry połączenia do sekcji **parametry połączeń** karty Ustawienia aplikacji w portalu.
* Parametry połączenia można przechowywać w [Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md).
* Jeśli uruchamiasz aplikację lokalnie, możesz chcieć przechowywać parametry połączenia w zmiennej środowiskowej.

### <a name="store-your-connection-string-in-an-environment-variable"></a>Przechowywanie parametrów połączenia w zmiennej środowiskowej

Aby skonfigurować zmienną środowiskową, Otwórz okno konsoli i wybierz system operacyjny z poniższych kart. Zamień `<yourconnectionstring>` na rzeczywiste parametry połączenia.

#### <a name="windows"></a>[Windows](#tab/windows)

Otwórz okno konsoli i wprowadź następujące polecenie:

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

Po dodaniu zmiennej środowiskowej być może trzeba będzie ponownie uruchomić działające programy, które muszą odczytywać zmienną środowiskową, w tym okno konsoli. Na przykład jeśli używasz programu Visual Studio jako edytora, uruchom ponownie program Visual Studio przed uruchomieniem przykładu.

#### <a name="macos"></a>[macOS](#tab/unix)

Edytuj **zshrc** i Dodaj zmienną środowiskową:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.zshrc` z okna konsoli, aby zmiany zostały uwzględnione. Jeśli zmienna środowiskowa została utworzona przy użyciu środowiska IDE, może być konieczne zamknięcie i ponowne otwarcie edytora, środowiska IDE lub powłoki w celu uzyskania dostępu do zmiennej.

#### <a name="linux"></a>[Linux](#tab/linux)

Edytuj **.bash_profile** i Dodaj zmienną środowiskową:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bash_profile` z okna konsoli, aby zmiany zostały uwzględnione. Jeśli zmienna środowiskowa została utworzona przy użyciu środowiska IDE, może być konieczne zamknięcie i ponowne otwarcie edytora, środowiska IDE lub powłoki w celu uzyskania dostępu do zmiennej.

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

Jeśli po usunięciu zasobów masz przypisane numery telefonów, numery telefonów zostaną automatycznie wydane z zasobu w tym samym czasie. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start zawarto informacje na temat wykonywania tych instrukcji:

> [!div class="checklist"]
> * Tworzenie zasobu usług Communication Services
> * Konfigurowanie lokalizacji i znaczników zasobów
> * Uzyskaj dostęp do kluczy dla tego zasobu
> * Usuń zasób

> [!div class="nextstepaction"]
> [Utwórz tokeny dostępu pierwszego użytkownika](access-tokens.md)