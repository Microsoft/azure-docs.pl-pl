---
title: Szybki Start — tworzenie zasobów i zarządzanie nimi w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku szybki start dowiesz się, jak utworzyć pierwszy zasób usługi Azure Communications Services i zarządzać nim.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-net
ms.openlocfilehash: a1bdb1b8ac1f545933a92e78fd13b745606f1da3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563785"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>Szybki Start: Tworzenie zasobów usług komunikacyjnych i zarządzanie nimi

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Rozpocznij pracę z usługami Azure Communications Services, udostępniając pierwszy zasób usług komunikacyjnych. Zasoby usług komunikacyjnych mogą być obsługiwane za pomocą [Azure Portal](https://portal.azure.com) lub zestawu .NET Management SDK. Zestaw SDK zarządzania i Azure Portal umożliwiają tworzenie, konfigurowanie, aktualizowanie i usuwanie zasobów i interfejsów przy użyciu [Azure Resource Manager](../../azure-resource-manager/management/overview.md), usługi wdrażania i zarządzania platformy Azure. Wszystkie funkcje dostępne w zestawach SDK są dostępne w Azure Portal. 


Rozpocznij pracę z usługami Azure Communications Services, udostępniając pierwszy zasób usług komunikacyjnych. Zasoby usług komunikacyjnych mogą być obsługiwane za pomocą [Azure Portal](https://portal.azure.com) lub zestawu .NET Management SDK. Zestaw SDK zarządzania i Azure Portal umożliwiają tworzenie, konfigurowanie, aktualizowanie i usuwanie zasobów i interfejsów przy użyciu [Azure Resource Manager](../../azure-resource-manager/management/overview.md), usługi wdrażania i zarządzania platformy Azure. Wszystkie funkcje dostępne w zestawach SDK są dostępne w Azure Portal.

> [!WARNING]
> Należy pamiętać, że podczas gdy usługi komunikacyjne są dostępne w wielu lokalizacje geograficzneach, w celu uzyskania numeru telefonu zasób musi mieć ustawioną lokalizację danych "US". Należy również pamiętać, że nie można przesłać zasobów komunikacyjnych do innej subskrypcji w ramach publicznej wersji zapoznawczej.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-azcli"
[!INCLUDE [Azure CLI](./includes/create-resource-azcli.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

## <a name="access-your-connection-strings-and-service-endpoints"></a>Uzyskiwanie dostępu do parametrów połączenia i punktów końcowych usługi

Parametry połączeń umożliwiają zestawom SDK usług komunikacyjnych łączenie się i uwierzytelnianie na platformie Azure. Możesz uzyskać dostęp do parametrów połączenia usług komunikacyjnych i punktów końcowych usługi z Azure Portal lub programowo przy użyciu Azure Resource Manager interfejsów API.

Po przejściu do zasobu usług komunikacyjnych wybierz pozycję **klucze** z menu nawigacji i skopiuj wartości **parametrów połączenia** lub **punktów końcowych** w celu użycia przez zestawy SDK usług komunikacyjnych. Należy pamiętać, że masz dostęp do kluczy podstawowych i pomocniczych. Może to być przydatne w scenariuszach, w których chcesz zapewnić tymczasowy dostęp do zasobów usług komunikacyjnych do środowiska innego lub przejściowego.

:::image type="content" source="./media/key.png" alt-text="Zrzut ekranu przedstawiający stronę klucza usług komunikacyjnych.":::

Możesz również uzyskać dostęp do kluczowych informacji przy użyciu interfejsu wiersza polecenia platformy Azure, takich jak grupa zasobów lub klucze dla określonego zasobu. 

Aby zalogować się, zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) i wykonaj następujące polecenie. Musisz podać swoje poświadczenia, aby nawiązać połączenie z kontem platformy Azure.
```azurecli
az login
```

Teraz możesz uzyskać dostęp do ważnych informacji o zasobach.
```azurecli
az communication list --resource-group "<resourceGroup>"

az communication list-key --name "<communicationName>" --resource-group "<resourceGroup>"
```

Jeśli chcesz wybrać określoną subskrypcję, możesz także określić ```--subscription``` flagę i podać identyfikator subskrypcji.
```
az communication list --resource-group  "resourceGroup>"  --subscription "<subscriptionID>"

az communication list-key --name "<communicationName>" --resource-group "resourceGroup>" --subscription "<subscriptionID>"
```

## <a name="store-your-connection-string"></a>Przechowywanie parametrów połączenia

Zestawy SDK usług komunikacyjnych używają parametrów połączenia do autoryzacji żądań wysyłanych do usług komunikacyjnych. Istnieje kilka opcji przechowywania parametrów połączenia:

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
