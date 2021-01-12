---
title: Wymuś minimalną wymaganą wersję Transport Layer Security (TLS) dla żądań przychodzących
titleSuffix: Azure Storage
description: Skonfiguruj konto magazynu, aby wymagać minimalnej wersji Transport Layer Security (TLS) dla klientów wysyłających żądania do usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: e5ab583330b46b8f53223500076aa04780e6deac
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108725"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>Wymuś minimalną wymaganą wersję Transport Layer Security (TLS) dla żądań kierowanych do konta magazynu

Komunikacja między aplikacją kliencką a kontem usługi Azure Storage jest zaszyfrowana przy użyciu usługi Transport Layer Security (TLS). TLS jest standardowym protokołem kryptograficznym, który zapewnia prywatność i integralność danych między klientami i usługami przez Internet. Aby uzyskać więcej informacji na temat protokołu TLS, zobacz [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Usługa Azure Storage obecnie obsługuje trzy wersje protokołu TLS: 1,0, 1,1 i 1,2. Usługa Azure Storage używa protokołu TLS 1,2 na publicznych punktach końcowych HTTPS, ale protokoły TLS 1,0 i TLS 1,1 są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami

Domyślnie konta usługi Azure Storage pozwalają klientom wysyłać i odbierać dane przy użyciu najstarszej wersji protokołu TLS, TLS 1,0 lub nowszej. Aby wymusić bardziej rygorystyczne środki bezpieczeństwa, można skonfigurować konto magazynu, aby wymagać, aby klienci wysyłali i odbierali dane przy użyciu nowszej wersji protokołu TLS. Jeśli konto magazynu wymaga minimalnej wersji protokołu TLS, wszystkie żądania wykonane ze starszą wersją zakończą się niepowodzeniem.

W tym artykule opisano, jak używać platformy przeciągania (wykrywania-korygowania-Audit-ładu) do ciągłego zarządzania bezpiecznym protokołem TLS dla kont magazynu.

Aby uzyskać informacje dotyczące sposobu określania określonej wersji protokołu TLS podczas wysyłania żądania z aplikacji klienckiej, zobacz [konfigurowanie Transport Layer Security (TLS) dla aplikacji klienckiej](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Wykrywanie wersji protokołu TLS używanej przez aplikacje klienckie

Po wymuszeniu minimalnej wersji protokołu TLS dla konta magazynu ryzyko odrzucenia żądań od klientów wysyłających dane przy użyciu starszej wersji protokołu TLS. Aby zrozumieć, jak konfigurowanie minimalnej wersji protokołu TLS może mieć wpływ na aplikacje klienckie, firma Microsoft zaleca włączenie rejestrowania dla konta usługi Azure Storage i przeanalizowanie dzienników po upływie interwału czasu w celu wykrycia, które wersje aplikacji klienckich TLS są używane.

Aby rejestrować żądania na koncie usługi Azure Storage i określać wersję protokołu TLS używaną przez klienta, można użyć rejestrowania w usłudze Azure Storage w Azure Monitor (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [monitorowanie usługi Azure Storage](../blobs/monitor-blob-storage.md).

Rejestrowanie w usłudze Azure Storage w Azure Monitor obsługuje używanie zapytań dzienników do analizowania danych dziennika. Aby wykonywać zapytania dotyczące dzienników, możesz użyć obszaru roboczego usługi Azure Log Analytics. Aby dowiedzieć się więcej o zapytaniach dziennika, zobacz [Samouczek: Rozpoczynanie pracy z zapytaniami log Analytics](../../azure-monitor/log-query/log-analytics-tutorial.md).

Aby rejestrować dane usługi Azure Storage za pomocą Azure Monitor i analizować je za pomocą usługi Azure Log Analytics, należy najpierw utworzyć ustawienie diagnostyczne wskazujące typy żądań i usługi magazynu, dla których mają być rejestrowane dane. Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek i tabel. Aby utworzyć ustawienie diagnostyczne w Azure Portal, wykonaj następujące kroki:

1. Utwórz nowy obszar roboczy Log Analytics w subskrypcji zawierającej konto usługi Azure Storage. Po skonfigurowaniu rejestrowania dla konta magazynu dzienniki będą dostępne w obszarze roboczym Log Analytics. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W sekcji monitorowanie wybierz pozycję **Ustawienia diagnostyczne (wersja zapoznawcza)**.
1. Wybierz usługę Azure Storage, dla której chcesz rejestrować żądania. Na przykład wybierz **obiekt BLOB** , aby rejestrować żądania do magazynu obiektów BLOB.
1. Wybierz pozycję **Dodaj ustawienia diagnostyczne**.
1. Podaj nazwę dla ustawienia diagnostyki.
1. W obszarze **szczegóły kategorii** w sekcji **Dziennik** wybierz typy żądań do zarejestrowania. Można rejestrować żądania odczytu, zapisu i usuwania. Na przykład wybranie **StorageRead** i **StorageWrite** będzie rejestrować żądania odczytu i zapisu do wybranej usługi.
1. W obszarze **szczegóły miejsca docelowego** wybierz pozycję **Wyślij do log Analytics**. Wybierz swoją subskrypcję i utworzony wcześniej obszar roboczy Log Analytics, jak pokazano na poniższej ilustracji.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia ustawień diagnostycznych żądań rejestrowania":::

Po utworzeniu ustawienia diagnostycznego żądania kierowane do konta magazynu są następnie rejestrowane zgodnie z tym ustawieniem. Aby uzyskać więcej informacji, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników zasobów i metryk na platformie Azure](../../azure-monitor/platform/diagnostic-settings.md).

Aby uzyskać informacje na temat pól dostępnych w dziennikach usługi Azure Storage w Azure Monitor, zobacz [dzienniki zasobów (wersja zapoznawcza)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Wysyłanie zapytań o zarejestrowane żądania według wersji protokołu TLS

Dzienniki usługi Azure Storage w Azure Monitor obejmują wersję protokołu TLS używaną do wysyłania żądania do konta magazynu. Użyj właściwości **TlsVersion** , aby sprawdzić wersję protokołu TLS zarejestrowanego żądania.

Aby określić liczbę żądań dla magazynu obiektów blob z różnymi wersjami protokołu TLS w ciągu ostatnich siedmiu dni, Otwórz obszar roboczy Log Analytics. Następnie wklej następujące zapytanie do nowego zapytania dziennika i uruchom je. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Wyniki przedstawiają liczbę żądań wykonanych z każdą wersją protokołu TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Zrzut ekranu przedstawiający wyniki zapytania usługi log Analytics w celu zwrócenia wersji protokołu TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Zapytanie o zarejestrowane żądania według adresu IP i nagłówka agenta użytkownika wywołującego

Dzienniki usługi Azure Storage w Azure Monitor zawierają również adres IP i nagłówek agenta użytkownika wywołującego, aby ułatwić ocenę, które aplikacje klienckie uzyskują dostęp do konta magazynu. Można analizować te wartości, aby zdecydować, czy należy zaktualizować aplikacje klienckie, aby korzystały z nowszej wersji protokołu TLS, czy też jeśli nie są wysyłane z użyciem minimalnej wersji protokołu TLS.

Aby określić klientów, którzy przetworzyli żądania z użyciem wersji TLS starszej niż TLS 1,2 w ciągu ostatnich siedmiu dni, wklej następujące zapytanie do nowego zapytania dziennika i uruchom je. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>Koryguj zagrożenia bezpieczeństwa przy użyciu minimalnej wersji protokołu TLS

Jeśli masz pewność, że ruch od klientów korzystających ze starszych wersji protokołu TLS jest minimalny lub że jest akceptowalny do niepowodzenia żądań z użyciem starszej wersji protokołu TLS, możesz zacząć wymuszać użycie minimalnej wersji protokołu TLS na koncie magazynu. Wymaganie, aby klienci korzystali z minimalnej wersji protokołu TLS, aby żądania dotyczące konta magazynu były częścią strategii, aby zminimalizować zagrożenia dla bezpieczeństwa danych.

> [!IMPORTANT]
> Jeśli używasz usługi, która nawiązuje połączenie z usługą Azure Storage, upewnij się, że usługa korzysta z odpowiedniej wersji protokołu TLS do wysyłania żądań do usługi Azure Storage przed ustawieniem minimalnej wymaganej wersji dla konta magazynu.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>Konfigurowanie minimalnej wersji protokołu TLS dla konta magazynu

Aby skonfigurować minimalną wersję protokołu TLS dla konta magazynu, ustaw wersję **MinimumTlsVersion** dla konta. Ta właściwość jest dostępna dla wszystkich kont magazynu utworzonych za pomocą modelu wdrażania Azure Resource Manager. Aby uzyskać więcej informacji o modelu wdrażania Azure Resource Manager, zobacz temat [konto magazynu — Omówienie](storage-account-overview.md).

Właściwość **MinimumTlsVersion** nie jest domyślnie ustawiona i nie zwraca wartości, dopóki nie zostanie jawnie ustawiona.  Jeśli wartość właściwości jest **równa null**, konto magazynu będzie zezwalać na żądania wysyłane z użyciem protokołu TLS w wersji 1,0 lub nowszej.

# <a name="portal"></a>[Portal](#tab/portal)

Podczas tworzenia konta magazynu przy użyciu Azure Portal minimalna wersja protokołu TLS jest domyślnie ustawiona na 1,2.

Aby skonfigurować minimalną wersję protokołu TLS dla istniejącego konta magazynu z Azure Portal, wykonaj następujące czynności:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Wybierz ustawienie **konfiguracji** .
1. W obszarze **minimalna wersja protokołu TLS** Użyj listy rozwijanej, aby wybrać minimalną wersję protokołu TLS wymaganą do uzyskania dostępu do danych na tym koncie magazynu, jak pokazano na poniższej ilustracji.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania minimalnej wersji protokołu TLS w Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby skonfigurować minimalną wersję protokołu TLS dla konta magazynu za pomocą programu PowerShell, zainstaluj [Azure PowerShell w wersji 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) lub nowszej. Następnie skonfiguruj Właściwość **MinimumTLSVersion** dla nowego lub istniejącego konta magazynu. Prawidłowe wartości dla **MinimumTlsVersion** to `TLS1_0` , `TLS1_1` i `TLS1_2` .

Poniższy przykład tworzy konto magazynu i ustawia **MinimumTLSVersion** na TLS 1,1, a następnie aktualizuje konto i ustawia **MinimumTLSVersion** na TLS 1,2. Przykład pobiera również wartość właściwości w każdym przypadku. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby skonfigurować minimalną wersję protokołu TLS dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.9.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Następnie skonfiguruj Właściwość **minimumTlsVersion** dla nowego lub istniejącego konta magazynu. Prawidłowe wartości dla **minimumTlsVersion** to `TLS1_0` , `TLS1_1` i `TLS1_2` .

Poniższy przykład tworzy konto magazynu i ustawia **minimumTLSVersion** na TLS 1,1. Następnie aktualizuje konto i ustawia właściwość **minimumTLSVersion** na TLS 1,2. Przykład pobiera również wartość właściwości w każdym przypadku. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[Szablon](#tab/template)

Aby skonfigurować minimalną wersję protokołu TLS dla konta magazynu z szablonem, Utwórz szablon z właściwością **MinimumTLSVersion** ustawioną na wartość `TLS1_0` , `TLS1_1` lub `TLS1_2` . Poniższe kroki opisują sposób tworzenia szablonu w Azure Portal.

1. W Azure Portal wybierz pozycję **Utwórz zasób**.
1. W obszarze **Wyszukaj w portalu Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **Enter**.
1. Wybierz **Template Deployment (Wdróż przy użyciu szablonów niestandardowych) (wersja zapoznawcza)**, wybierz pozycję **Utwórz**, a następnie wybierz opcję **Kompiluj własny szablon w edytorze**.
1. W edytorze szablonów wklej poniższy kod JSON, aby utworzyć nowe konto i ustawić minimalną wersję protokołu TLS na TLS 1,2. Pamiętaj, aby zastąpić symbole zastępcze w nawiasach kątowe własnymi wartościami.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Zapisz szablon.
1. Określ parametr grupy zasobów, a następnie wybierz przycisk **Recenzja + Utwórz** , aby wdrożyć szablon i utworzyć konto magazynu przy użyciu skonfigurowanej właściwości **MinimumTLSVersion** .

---

> [!NOTE]
> Po zaktualizowaniu minimalnej wersji protokołu TLS dla konta magazynu może upłynąć do 30 sekund, zanim zmiana zostanie w pełni rozpropagowana.

Skonfigurowanie minimalnej wersji protokołu TLS wymaga wersji 2019-04-01 lub nowszej dostawcy zasobów usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [interfejs API REST dostawcy zasobów usługi Azure Storage](/rest/api/storagerp/).

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>Sprawdź minimalną wymaganą wersję protokołu TLS dla wielu kont

Aby sprawdzić minimalną wymaganą wersję protokołu TLS w zestawie kont magazynu z optymalną wydajnością, można użyć Eksploratora Azure Resource Graph w Azure Portal. Aby dowiedzieć się więcej o korzystaniu z Eksploratora grafów zasobów, zobacz [Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph](../../governance/resource-graph/first-query-portal.md).

Uruchomienie następującego zapytania w Eksploratorze grafu zasobów zwraca listę kont magazynu i wyświetla minimalną wersję protokołu TLS dla każdego konta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>Testowanie minimalnej wersji protokołu TLS z poziomu klienta

Aby sprawdzić, czy minimalna wymagana wersja protokołu TLS dla konta magazynu zabrania wywołań wykonanych za pomocą starszej wersji, można skonfigurować klienta do korzystania ze starszej wersji protokołu TLS. Aby uzyskać więcej informacji o konfigurowaniu klienta do korzystania z określonej wersji protokołu TLS, zobacz [konfigurowanie Transport Layer Security (TLS) dla aplikacji klienckiej](transport-layer-security-configure-client-version.md).

Gdy klient uzyskuje dostęp do konta magazynu przy użyciu wersji TLS, która nie spełnia wymagań dotyczących minimalnej wersji protokołu TLS skonfigurowanej dla tego konta, usługa Azure Storage zwraca kod błędu 400 (Nieprawidłowe żądanie) i komunikat informujący o tym, że używana wersja protokołu TLS nie jest dozwolona w przypadku wysyłania żądań do tego konta magazynu.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Użyj Azure Policy, aby przeprowadzić inspekcję zgodności

Jeśli masz dużą liczbę kont magazynu, możesz wykonać inspekcję, aby upewnić się, że wszystkie konta są skonfigurowane dla minimalnej wersji protokołu TLS wymaganej przez organizację. Aby przeprowadzić inspekcję zestawu kont magazynu pod kątem zgodności, użyj Azure Policy. Azure Policy to usługa, za pomocą której można tworzyć i przypisywać zasady stosujące reguły do zasobów platformy Azure oraz zarządzać nimi. Azure Policy pomaga zachować zgodność tych zasobów ze standardami firmy i umowami dotyczącymi poziomu usług. Aby uzyskać więcej informacji, zobacz [omówienie Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Tworzenie zasad z efektem inspekcji

Azure Policy obsługuje efekty, które określają, co się dzieje, gdy reguła zasad zostanie oceniona względem zasobu. Efekt inspekcji tworzy ostrzeżenie, gdy zasób nie jest zgodny, ale nie zatrzymuje żądania. Aby uzyskać więcej informacji na temat efektów, zobacz [opis efektów Azure Policy](../../governance/policy/concepts/effects.md).

Aby utworzyć zasady z efektem inspekcji dla minimalnej wersji protokołu TLS z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal przejdź do usługi Azure Policy.
1. W sekcji **Tworzenie** wybierz pozycję **definicje**.
1. Wybierz pozycję **Dodaj definicję zasad** , aby utworzyć nową definicję zasad.
1. W polu **Lokalizacja definicji** wybierz przycisk **więcej** , aby określić, gdzie znajduje się zasób zasady inspekcji.
1. Określ nazwę zasad. Opcjonalnie można określić opis i kategorię.
1. W obszarze **reguła zasad** Dodaj następującą definicję zasad do sekcji **Klasa policyrule** .

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
              "equals": "TLS1_2"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Zapisz zasady.

### <a name="assign-the-policy"></a>Przypisywanie zasad

Następnie przypisz zasady do zasobu. Zakres zasad odpowiada ten zasób i wszystkie znajdujące się w nim zasoby. Aby uzyskać więcej informacji na temat przypisywania zasad, zobacz [Azure Policy struktury przypisywania](../../governance/policy/concepts/assignment-structure.md).

Aby przypisać zasady do Azure Portal, wykonaj następujące kroki:

1. W Azure Portal przejdź do usługi Azure Policy.
1. W sekcji **Tworzenie** wybierz pozycję **przypisania**.
1. Wybierz pozycję **Przypisz zasady** , aby utworzyć nowe przypisanie zasad.
1. Dla pola **zakres** wybierz zakres przypisania zasad.
1. W polu **Definicja zasad** wybierz przycisk **więcej** , a następnie wybierz zasady zdefiniowane w poprzedniej sekcji z listy.
1. Podaj nazwę przypisania zasad. Opis jest opcjonalny.
1. Pozostaw ustawienie **wymuszania zasad** ustawiony na *włączone*. To ustawienie nie ma wpływu na zasady inspekcji.
1. Wybierz pozycję **Przegląd + Utwórz** , aby utworzyć przypisanie.

### <a name="view-compliance-report"></a>Wyświetl raport zgodności

Po przypisaniu zasad można wyświetlić raport o zgodności. Raport zgodności dla zasad inspekcji zawiera informacje o tym, które konta magazynu nie są zgodne z zasadami. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie danych dotyczących zgodności z zasadami](../../governance/policy/how-to/get-compliance-data.md).

Po utworzeniu przypisania zasad może upłynąć kilka minut, zanim raport zgodności stanie się dostępny.

Aby wyświetlić raport zgodności w Azure Portal, wykonaj następujące kroki:

1. W Azure Portal przejdź do usługi Azure Policy.
1. Wybierz pozycję **zgodność**.
1. Filtruje wyniki dla nazwy przypisania zasad utworzonego w poprzednim kroku. Raport przedstawia, ile zasobów nie jest zgodnych z zasadami.
1. Możesz przejść do raportu, aby uzyskać dodatkowe szczegóły, w tym listę kont magazynu, które nie są zgodne.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="Zrzut ekranu przedstawiający raport zgodności dla zasad inspekcji dla minimalnej wersji protokołu TLS":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Użyj Azure Policy, aby wymusić minimalną wersję protokołu TLS

Azure Policy obsługuje zarządzanie chmurą dzięki zapewnieniu, że zasoby platformy Azure są zgodne z wymaganiami i standardami. Aby wymusić minimalne wymagania dotyczące wersji protokołu TLS dla kont magazynu w organizacji, można utworzyć zasady, które uniemożliwiają utworzenie nowego konta magazynu, które ustawia minimalny wymóg protokołu TLS dla starszej wersji protokołu TLS niż ta, która jest określana przez zasady. Te zasady będą również zapobiegać wszystkim zmianom konfiguracji istniejącego konta, jeśli ustawienie minimalnej wersji protokołu TLS dla tego konta nie jest zgodne z zasadami.

Zasady wymuszania używają efektu Odmów, aby zapobiec żądaniu, które mogłoby utworzyć lub zmodyfikować konto magazynu, aby minimalna wersja protokołu TLS nie była już zgodna ze standardami organizacji. Aby uzyskać więcej informacji na temat efektów, zobacz [opis efektów Azure Policy](../../governance/policy/concepts/effects.md).

Aby utworzyć zasady z efektem odmowy dla minimalnej wersji protokołu TLS, która jest niższa niż TLS 1,2, wykonaj te same kroki opisane w temacie [Use Azure Policy, aby przeprowadzić inspekcję pod kątem zgodności](#use-azure-policy-to-audit-for-compliance), ale podaj następujący kod JSON w sekcji **Klasa policyrule** definicji zasad:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
          "equals": "TLS1_2"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Po utworzeniu zasad z efektem odmowy i przypisaniu go do zakresu użytkownik nie może utworzyć konta magazynu o minimalnej wersji protokołu TLS starszej niż 1,2. Użytkownik nie może wprowadzać żadnych zmian konfiguracji na istniejącym koncie magazynu, które obecnie wymaga minimalnej wersji protokołu TLS starszej niż 1,2. Próba wykonania tej czynności spowoduje wystąpienie błędu. Wymagana minimalna wersja protokołu TLS dla konta magazynu musi być ustawiona na 1,2, aby kontynuować tworzenie lub Konfigurowanie konta.

Na poniższej ilustracji przedstawiono błąd występujący w przypadku próby utworzenia konta magazynu z minimalną wersją protokołu TLS ustawioną na TLS 1,0 (wartość domyślna dla nowego konta), gdy zasady z efektem odmowy wymagają ustawienia minimalnej wersji protokołu TLS na TLS 1,2.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="Zrzut ekranu przedstawiający błąd występujący podczas tworzenia konta magazynu w celu naruszenia zasad":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>Uprawnienia wymagane do wymagania minimalnej wersji protokołu TLS

Aby ustawić właściwość **MinimumTlsVersion** dla konta magazynu, użytkownik musi mieć uprawnienia do tworzenia kont magazynu i zarządzania nimi. Role kontroli dostępu opartej na rolach (Azure RBAC), które udostępniają te uprawnienia, obejmują akcję **Microsoft. Storage/storageAccounts/Write** lub **Microsoft. Storage \* /storageAccounts/* _. Role wbudowane z tą akcją obejmują:

- Rola [właściciela](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- Rola [współautor](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- Rola [współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Te role nie zapewniają dostępu do danych na koncie magazynu za pośrednictwem Azure Active Directory (Azure AD). Obejmują one jednak _ * Microsoft. Storage/storageAccounts/ListKeys/Action * *, które przyznaje dostęp do kluczy dostępu do konta. Za pomocą tego uprawnienia użytkownik może korzystać z kluczy dostępu do konta w celu uzyskania dostępu do wszystkich danych na koncie magazynu.

Przypisania ról muszą być ograniczone do poziomu konta magazynu lub nowszego, aby umożliwić użytkownikowi wymaganie minimalnej wersji protokołu TLS dla konta magazynu. Aby uzyskać więcej informacji na temat zakresu roli, zobacz [Opis zakresu kontroli RBAC platformy Azure](../../role-based-access-control/scope-overview.md).

Należy zachować ostrożność, aby ograniczyć przypisanie tych ról tylko do tych, które wymagają możliwości utworzenia konta magazynu lub aktualizacji jego właściwości. Użyj zasady najniższych uprawnień, aby upewnić się, że użytkownicy mają najmniejsze uprawnienia, których potrzebują do wykonywania swoich zadań. Aby uzyskać więcej informacji na temat zarządzania dostępem za pomocą usługi Azure RBAC, zobacz [najlepsze rozwiązania dotyczące kontroli RBAC platformy Azure](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Administrator usług ról klasycznych administrator i Co-Administrator obejmujący odpowiednik roli [właściciela](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager. Rola **właściciela** obejmuje wszystkie akcje, więc użytkownik z jedną z tych ról administracyjnych może również tworzyć konta magazynu i zarządzać nimi. Aby uzyskać więcej informacji, zobacz Role [administratora subskrypcji klasycznej, role platformy Azure i role administratorów usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="network-considerations"></a>Kwestie dotyczące sieci

Gdy klient wysyła żądanie do konta magazynu, najpierw nawiązuje połączenie z publicznym punktem końcowym konta magazynu przed przetworzeniem żądań. Ustawienie minimalnej wersji protokołu TLS jest sprawdzane po nawiązaniu połączenia. Jeśli żądanie używa starszej wersji protokołu TLS niż określona przez ustawienie, połączenie będzie nadal się powieść, ale żądanie zakończy się niepowodzeniem. Aby uzyskać więcej informacji na temat publicznych punktów końcowych usługi Azure Storage, zobacz [składnia identyfikatora URI zasobu](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie Transport Layer Security (TLS) dla aplikacji klienckiej](transport-layer-security-configure-client-version.md)
- [Zalecenia dotyczące zabezpieczeń usługi BLOB Storage](../blobs/security-recommendations.md)
