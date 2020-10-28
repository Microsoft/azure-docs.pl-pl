---
title: Tworzenie testu analizatora internetowego przy użyciu interfejsu wiersza polecenia | Microsoft Docs
description: W tym artykule dowiesz się, jak utworzyć pierwszy test analizatora internetowego przy użyciu interfejsu wiersza polecenia platformy Azure.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 23a76e4007965c263671554bbcd937dbf3b4b0a6
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676210"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Utwórz test analizatora internetowego przy użyciu interfejsu wiersza polecenia (wersja zapoznawcza)

Istnieją dwa sposoby tworzenia zasobów analizatora internetowego — przy użyciu [Azure Portal](internet-analyzer-create-test-portal.md) lub interfejsu wiersza polecenia. Ta sekcja ułatwia tworzenie nowego zasobu usługi Azure Internet Analyzer przy użyciu interfejsu wiersza polecenia. 


> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Publiczna wersja zapoznawcza jest dostępna do użycia globalnie; jednak magazyn danych jest ograniczony do *zachodnich Stanów Zjednoczonych 2* w trakcie okresu zapoznawczego.

## <a name="object-model"></a>Model obiektów
Interfejs wiersza polecenia programu Internet Analyzer udostępnia następujące typy zasobów:
* **Testy** — test porównuje wydajność użytkowników końcowych z dwoma punktami końcowymi internetowymi (a i B) w czasie.
* **Profile** — testy są tworzone w ramach profilu analizatora internetowego. Profile umożliwiają grupowanie powiązanych testów; pojedynczy profil może zawierać jeden lub więcej testów.
* Wstępnie **skonfigurowane punkty końcowe** — skonfigurowano punkty końcowe z różnymi konfiguracjami (regionami, technologiami przyspieszenia itp.). W testach można użyć dowolnego ze wstępnie skonfigurowanych punktów końcowych.
* **Karty wyników** — Karta wyników zapewnia szybkie i zrozumiałe podsumowania wyników pomiarów. Zapoznaj się z [interpretacją karty wyników](internet-analyzer-scorecard.md).
* **Szeregi czasowe** — seria czasowa pokazuje, jak zmienia się Metryka w czasie.

## <a name="profile-and-test-creation"></a>Tworzenie profilu i testu
1. Uzyskaj dostęp do usługi Internet Analyzer w wersji zapoznawczej, wykonując **Jak mogę uczestnictwo w wersji zapoznawczej** [usługi Azure Internet Analyzer — często zadawane pytania](internet-analyzer-faq.md).
2. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
3. Uruchom `login` polecenie, aby uruchomić sesję interfejsu wiersza polecenia:
    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure.
    W przeciwnym razie Otwórz stronę przeglądarki pod adresem https://aka.ms/devicelogin i wprowadź kod autoryzacji wyświetlany w terminalu.

4. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

5. Wybierz identyfikator subskrypcji, którym udzielono dostępu do publicznej wersji zapoznawczej analizatora internetowego.

    Po zalogowaniu zostanie wyświetlona lista subskrypcji skojarzonych z Twoim kontem platformy Azure. Informacje o subskrypcji `isDefault: true` są obecnie aktywowaną subskrypcją po zalogowaniu się. Aby wybrać inną subskrypcję, użyj polecenia [AZ Account Set](/cli/azure/account#az-account-set) z identyfikatorem subskrypcji, aby przełączyć się do. Aby uzyskać więcej informacji na temat wyboru subskrypcji, zobacz [Korzystanie z wielu subskrypcji platformy Azure](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).

    Istnieją sposoby logowania nieinterakcyjnego, które opisano szczegółowo w artykule [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

6. **[Opcjonalne]** Utwórz nową grupę zasobów platformy Azure:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Zainstaluj rozszerzenie Analizator internetowy interfejsu wiersza polecenia platformy Azure:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Utwórz nowy profil analizatora internetowego:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Wyświetl listę wszystkich wstępnie skonfigurowanych punktów końcowych dostępnych dla nowo utworzonego profilu:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Utwórz nowy test w ramach nowo utworzonego profilu InternetAnalyzer:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    W powyższym poleceniu założono, że obie `www.contoso.com` i `www.microsoft.com` obsługują obraz jednopikselowy ([trans.gif](https://fpc.msedge.net/apc/trans.gif)) pod ścieżkami niestandardowymi. Jeśli ścieżka obiektu nie została określona jawnie, Analizator Internetu będzie używać `/apc/trans.gif` jako ścieżki obiektu domyślnie, czyli gdy wstępnie skonfigurowane punkty końcowe obsługują obraz z pikselami. Należy również zauważyć, że nie trzeba określać schematu (https/http); Analizator Internetu obsługuje tylko punkty końcowe HTTPS, więc założono, że jest to protokół HTTPS.

11. Nowy test powinien pojawić się w profilu analizatora internetowego:
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

12. Aby rozpocząć generowanie pomiarów, plik JavaScript wskazany przez **scriptFileUri** testu musi być osadzony w aplikacji sieci Web. Szczegółowe instrukcje można znaleźć na stronie [klienta Osadź Internet Analyzer Client](internet-analyzer-embed-client.md) .

13. Postęp testu można monitorować, śledząc jego wartość "status":
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Wyniki testów można sprawdzić, generując dla nich szeregów czasowych lub karty wyników:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Następne kroki

* Przejrzyj [Informacje o interfejsie wiersza polecenia analizatora Internetu](/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest) , aby zapoznać się z pełną listą obsługiwanych poleceń i przykładów użycia.
* Przeczytaj [często zadawane pytania dotyczące analizatora Internetu](internet-analyzer-faq.md).
* Dowiedz się więcej o osadzaniu [klienta analizatora Internetu](internet-analyzer-embed-client.md) i tworzeniu [niestandardowego punktu końcowego](internet-analyzer-custom-endpoint.md).