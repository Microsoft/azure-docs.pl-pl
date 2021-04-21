---
title: Tworzenie testu Internet Analyzer przy użyciu interfejsu wiersza polecenia | Microsoft Docs
description: W tym artykule dowiesz się, jak utworzyć pierwszy test Internet Analyzer przy użyciu interfejsu wiersza polecenia platformy Azure.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 46caae53ed81de335c2b9d5ddbd3fd7f89424fdd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780419"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Tworzenie testu Internet Analyzer interfejsu wiersza polecenia (wersja zapoznawcza)

Istnieją dwa sposoby tworzenia zasobu Internet Analyzer — za [pomocą interfejsu Azure Portal](internet-analyzer-create-test-portal.md) interfejsu wiersza polecenia. Ta sekcja ułatwia tworzenie nowego zasobu Azure Internet Analyzer interfejsu wiersza polecenia. 


> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Publiczna wersja zapoznawcza jest dostępna do użycia globalnie; Jednak magazyn danych jest ograniczony do zachodnich stanów *USA 2* w okresie zapoznawczym.

## <a name="object-model"></a>Model obiektu
Interfejs Internet Analyzer udostępnia następujące typy zasobów:
* **Testy** — test porównuje wydajność użytkownika końcowego dwóch internetowych punktów końcowych (A i B) w czasie.
* **Profile** — testy są tworzone w Internet Analyzer profilu. Profile umożliwiają grupowanie powiązanych testów; Jeden profil może zawierać jeden lub więcej testów.
* **Wstępnie skonfigurowane punkty końcowe —** skonfigurowaliśmy punkty końcowe z różnymi konfiguracjami (regiony, technologie przyspieszania itp.). W testach możesz użyć dowolnego ze wstępnie skonfigurowanych punktów końcowych.
* **Karty wyników —** karta wyników zawiera szybkie i znaczące podsumowania wyników pomiarów. Zapoznaj się z [tematem Interpreting your Scorecard (Interpretowanie karty wyników).](internet-analyzer-scorecard.md)
* **Szereg czasowy** — szereg czasowy pokazuje, jak metryka zmienia się w czasie.

## <a name="profile-and-test-creation"></a>Tworzenie profilu i testowania
1. Uzyskaj Internet Analyzer w wersji zapoznawczej, korzystając z Jak mogę uczestniczyć w wersji **zapoznawczej?** instrukcje z artykułu [Azure Internet Analyzer CZĘSTO ZADAWANE PYTANIA.](internet-analyzer-faq.md)
2. [Zainstaluj interfejs wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)
3. Uruchom polecenie `login` , aby uruchomić sesję interfejsu wiersza polecenia:
    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, zrobi to i załaduje stronę logowania platformy Azure.
    W przeciwnym razie otwórz stronę przeglądarki pod https://aka.ms/devicelogin adresem i wprowadź kod autoryzacji wyświetlany w terminalu.

4. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

5. Wybierz swój identyfikator subskrypcji, dla których udzielono dostępu do Internet Analyzer publicznej wersji zapoznawczej.

    Po zalogowaniu zostanie wyświetlona lista subskrypcji skojarzonych z kontem platformy Azure. Informacje o subskrypcji w `isDefault: true` programie to aktualnie aktywowana subskrypcja po zalogowaniu. Aby wybrać inną subskrypcję, użyj [polecenia az account set](/cli/azure/account#az_account_set) z identyfikatorem subskrypcji, na który chcesz się przełączyć. Aby uzyskać więcej informacji na temat wyboru subskrypcji, zobacz Use multiple Azure subscriptions (Korzystanie [z wielu subskrypcji platformy Azure).](/cli/azure/manage-azure-subscriptions-azure-cli)

    Istnieją sposoby logowania nieinterakcyjnego, które opisano szczegółowo w artykule [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

6. **[Opcjonalnie]** Utwórz nową grupę zasobów platformy Azure:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Zainstaluj rozszerzenie interfejsu wiersza polecenia Internet Analyzer Azure:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Utwórz nowy profil Internet Analyzer aplikacji:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Lista wszystkich wstępnie skonfigurowanych punktów końcowych dostępnych dla nowo utworzonego profilu:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Utwórz nowy test w nowo utworzonym profilu InternetAnalyzer:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    W powyższym poleceniu przyjęto założenie, że zarówno , jak i hostują obraz o jednym `www.contoso.com` `www.microsoft.com` pikselu[ (trans.gif](https://fpc.msedge.net/apc/trans.gif)) w ścieżkach niestandardowych. Jeśli ścieżka obiektu nie zostanie jawnie określona, Internet Analyzer będzie domyślnie używać jako ścieżki obiektu, w którym wstępnie skonfigurowane punkty końcowe hostują obraz o jednym `/apc/trans.gif` pikselu. Należy również zauważyć, że nie trzeba określać schematu (https/http); Internet Analyzer obsługuje tylko punkty końcowe HTTPS, więc zakłada się, że protokół HTTPS.

11. Nowy test powinien zostać wyświetlony w Internet Analyzer profilu:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Przykładowe dane wyjściowe:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Aby rozpocząć generowanie pomiarów, plik JavaScript wskazywany przez element **scriptFileUri** testu musi być osadzony w aplikacji internetowej. Szczegółowe instrukcje można znaleźć na [stronie Osadzanie Internet Analyzer klienta.](internet-analyzer-embed-client.md)

13. Postęp testu można monitorować, śledząc jego wartość "status":
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Zebrane wyniki testu można sprawdzić, generując dla niego czasy lub karty wyników:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Następne kroki

* Przejrzyj Internet Analyzer [wiersza polecenia,](/cli/azure/ext/internet-analyzer/internet-analyzer) aby uzyskać pełną listę obsługiwanych poleceń i przykładów użycia.
* Przeczytaj często [zadawane Internet Analyzer odpowiedzi.](internet-analyzer-faq.md)
* Dowiedz się więcej o osadzaniu klienta [Internet Analyzer i](internet-analyzer-embed-client.md) tworzeniu [niestandardowego punktu końcowego.](internet-analyzer-custom-endpoint.md)