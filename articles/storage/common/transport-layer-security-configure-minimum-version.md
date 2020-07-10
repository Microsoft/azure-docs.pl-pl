---
title: Skonfiguruj minimalną wymaganą wersję Transport Layer Security (TLS) dla konta magazynu
titleSuffix: Azure Storage
description: Skonfiguruj konto magazynu, aby wymagać minimalnej wersji Transport Layer Security (TLS) dla klientów wysyłających żądania do usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209864"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>Skonfiguruj minimalną wymaganą wersję Transport Layer Security (TLS) dla konta magazynu

Komunikacja między aplikacją kliencką a kontem usługi Azure Storage jest zaszyfrowana przy użyciu usługi Transport Layer Security (TLS). TLS jest standardowym protokołem kryptograficznym, który zapewnia prywatność i integralność danych między klientami i usługami przez Internet. Aby uzyskać więcej informacji na temat protokołu TLS, zobacz [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security).

Usługa Azure Storage obecnie obsługuje trzy wersje protokołu TLS: 1,0, 1,1 i 1,2. TLS 1,2 to najbezpieczniejsza wersja protokołu TLS. Usługa Azure Storage używa protokołu TLS 1,2 na publicznych punktach końcowych HTTPs, ale protokoły TLS 1,0 i TLS 1,1 są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami

Domyślnie konta usługi Azure Storage pozwalają klientom wysyłać i odbierać dane przy użyciu najstarszej wersji protokołu TLS, TLS 1,0 lub nowszej. Aby wymusić bardziej rygorystyczne środki bezpieczeństwa, można skonfigurować konto magazynu, aby wymagać, aby klienci wysyłali i odbierali dane przy użyciu nowszej wersji protokołu TLS. Jeśli konto magazynu wymaga minimalnej wersji protokołu TLS, wszelkie żądania wykonane ze starszą wersją zakończą się niepowodzeniem.

W tym artykule opisano sposób konfigurowania konta magazynu w celu wymagania, aby klienci wysyłali żądania z użyciem minimalnej wersji protokołu TLS. Aby uzyskać informacje dotyczące sposobu określania określonej wersji protokołu TLS podczas wysyłania żądania z aplikacji klienckiej, zobacz [konfigurowanie Transport Layer Security (TLS) dla aplikacji klienckiej](transport-layer-security-configure-client-version.md).

## <a name="detect-the-tls-version-used-by-client-applications"></a>Wykrywanie wersji protokołu TLS używanej przez aplikacje klienckie

Po wymuszeniu minimalnej wersji protokołu TLS dla konta magazynu użytkownik jest narażony na Odrzucanie żądań od klientów wysyłających dane przy użyciu wcześniejszej wersji protokołu TLS. Aby zrozumieć, jak konfigurowanie minimalnej wersji protokołu TLS może mieć wpływ na aplikacje klienckie, firma Microsoft zaleca włączenie rejestrowania dla konta usługi Azure Storage i przeanalizowanie dzienników po upływie interwału czasu, aby określić, które wersje aplikacji klienckich TLS są używane.

Aby rejestrować żądania na koncie usługi Azure Storage i określać wersję protokołu TLS używaną przez klienta, można użyć rejestrowania w usłudze Azure Storage w Azure Monitor (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [monitorowanie usługi Azure Storage](monitor-storage.md).

Rejestrowanie w usłudze Azure Storage w Azure Monitor obsługuje używanie zapytań dzienników do analizowania danych dziennika. Aby wykonywać zapytania dotyczące dzienników, możesz użyć obszaru roboczego usługi Azure Log Analytics. Aby dowiedzieć się więcej o zapytaniach dziennika, zobacz [Samouczek: Rozpoczynanie pracy z zapytaniami log Analytics](../../azure-monitor/log-query/get-started-portal.md).

Aby rejestrować dane usługi Azure Storage za pomocą Azure Monitor i analizować je za pomocą usługi Azure Log Analytics, należy najpierw utworzyć ustawienie diagnostyczne wskazujące typy żądań i usługi magazynu, dla których mają być rejestrowane dane. Aby utworzyć ustawienie diagnostyczne w Azure Portal, wykonaj następujące kroki:

1. Zarejestruj się w [usłudze Azure Storage w wersji zapoznawczej Azure monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Utwórz nowy obszar roboczy Log Analytics w subskrypcji zawierającej konto usługi Azure Storage. Po skonfigurowaniu rejestrowania dla konta magazynu dzienniki będą dostępne w obszarze roboczym Log Analytics. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W sekcji monitorowanie wybierz pozycję **Ustawienia diagnostyczne (wersja zapoznawcza)**.
1. Wybierz usługę Azure Storage, dla której chcesz rejestrować żądania. Na przykład wybierz **obiekt BLOB** , aby rejestrować żądania do magazynu obiektów BLOB.
1. Wybierz pozycję **Dodaj ustawienie diagnostyczne**.
1. Podaj nazwę dla ustawienia diagnostyki.
1. W obszarze **szczegóły kategorii**w sekcji **Dziennik** wybierz typy żądań do zarejestrowania. Można rejestrować żądania odczytu, zapisu i usuwania. Na przykład wybranie **StorageRead** i **StorageWrite** będzie rejestrować żądania odczytu i zapisu do wybranej usługi.
1. W obszarze **szczegóły miejsca docelowego**wybierz pozycję **Wyślij do log Analytics**. Wybierz swoją subskrypcję i utworzony wcześniej obszar roboczy Log Analytics, jak pokazano na poniższej ilustracji.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia ustawień diagnostycznych żądań rejestrowania":::

Po utworzeniu ustawienia diagnostycznego żądania kierowane do konta magazynu są następnie rejestrowane zgodnie z tym ustawieniem. Aby uzyskać więcej informacji, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników zasobów i metryk na platformie Azure](../../azure-monitor/platform/diagnostic-settings.md).

Aby uzyskać informacje na temat pól dostępnych w dziennikach usługi Azure Storage w Azure Monitor, zobacz [dzienniki zasobów (wersja zapoznawcza)](monitor-storage-reference.md#resource-logs-preview).

### <a name="query-logged-requests-by-tls-version"></a>Wysyłanie zapytań o zarejestrowane żądania według wersji protokołu TLS

Dzienniki usługi Azure Storage w Azure Monitor obejmują wersję protokołu TLS używaną do wysyłania żądania do konta magazynu. Użyj właściwości **TlsVersion** , aby sprawdzić wersję protokołu TLS zarejestrowanego żądania.

Aby pobrać dzienniki z ostatnich 7 dni i określić liczbę żądań dotyczących usługi BLOB Storage z każdą wersją protokołu TLS, Otwórz obszar roboczy Log Analytics. Następnie wklej następujące zapytanie do nowego zapytania dziennika i uruchom je. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

Wyniki przedstawiają liczbę żądań wykonanych z każdą wersją protokołu TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Zrzut ekranu przedstawiający wyniki zapytania usługi log Analytics w celu zwrócenia wersji protokołu TLS":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Zapytanie o zarejestrowane żądania według adresu IP i nagłówka agenta użytkownika wywołującego

Dzienniki usługi Azure Storage w Azure Monitor zawierają również adres IP i nagłówek agenta użytkownika wywołującego, aby ułatwić ocenę, które aplikacje klienckie uzyskują dostęp do konta magazynu. Można analizować te wartości, aby zdecydować, czy należy zaktualizować aplikacje klienckie, aby korzystały z nowszej wersji protokołu TLS, czy też jeśli nie są wysyłane z użyciem minimalnej wersji protokołu TLS.

Aby pobrać dzienniki z ostatnich 7 dni i określić klientów, którzy wykonali żądania z użyciem wersji TLS przed protokołem TLS 1,2, wklej następujące zapytanie do nowego zapytania dziennika i uruchom je. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>Skonfiguruj minimalną wersję protokołu TLS dla konta

Aby skonfigurować minimalną wersję protokołu TLS dla konta magazynu, użyj Azure Portal lub interfejsu wiersza polecenia platformy Azure w celu ustawienia wersji **minimumTlsVersion** dla tego konta. Ta właściwość jest dostępna dla wszystkich kont magazynu utworzonych za pomocą modelu wdrażania Azure Resource Manager. Aby uzyskać więcej informacji, zobacz temat [konto magazynu — Omówienie](storage-account-overview.md).

# <a name="portal"></a>[Portal](#tab/portal)

Aby skonfigurować minimalną wersję protokołu TLS dla konta magazynu z Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Wybierz ustawienie **konfiguracji** .
1. W obszarze **minimalna wersja protokołu TLS**Użyj listy rozwijanej, aby wybrać minimalną wersję protokołu TLS wymaganą do uzyskania dostępu do danych na tym koncie magazynu, jak pokazano na poniższej ilustracji.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania minimalnej wersji protokołu TLS w Azure Portal":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby skonfigurować minimalną wersję protokołu TLS dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, należy najpierw uzyskać identyfikator zasobu dla konta magazynu, wywołując polecenie [AZ Resource show](/cli/azure/resource#az-resource-show) . Następnie Wywołaj polecenie [AZ Resource Update](/cli/azure/resource#az-resource-update) , aby ustawić właściwość **minimumTlsVersion** dla konta magazynu. Prawidłowe wartości dla **minimumTlsVersion** to `TLS1_0` , `TLS1_1` i `TLS1_2` .

W poniższym przykładzie ustawiono minimalną wersję protokołu TLS na 1,2. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> Po zaktualizowaniu minimalnej wersji protokołu TLS dla konta magazynu może upłynąć do 30 sekund, zanim zmiana zostanie w pełni rozpropagowana.

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>Sprawdź minimalną wymaganą wersję protokołu TLS dla konta

Aby określić minimalną wymaganą wersję protokołu TLS skonfigurowaną dla konta magazynu, należy sprawdzić Właściwość Azure Resource Manager **minimumTlsVersion** . Aby sprawdzić tę właściwość dla dużej liczby kont magazynu jednocześnie, użyj Eksploratora Azure Resource Graph.

Właściwość **minimumTlsVersion** nie jest domyślnie ustawiona i nie zwraca wartości, dopóki nie zostanie jawnie ustawiona. Konto magazynu domyślnie zezwala na żądania wysyłane z użyciem protokołu TLS w wersji 1,0 lub nowszej, jeśli wartość właściwości jest równa null.

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>Sprawdź minimalną wymaganą wersję protokołu TLS dla jednego konta magazynu

Aby sprawdzić minimalną wymaganą wersję protokołu TLS dla jednego konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Resource show](/cli/azure/resource#az-resource-show) i zapytanie dla właściwości **minimumTlsVersion** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>Sprawdź minimalną wymaganą wersję protokołu TLS dla zestawu kont magazynu

Aby sprawdzić minimalną wymaganą wersję protokołu TLS w zestawie kont magazynu z optymalną wydajnością, można użyć Eksploratora Azure Resource Graph w Azure Portal. Aby dowiedzieć się więcej o korzystaniu z Eksploratora grafów zasobów, zobacz [Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph](/azure/governance/resource-graph/first-query-portal).

Uruchomienie następującego zapytania w Eksploratorze grafu zasobów zwraca listę kont magazynu i wyświetla minimalną wersję protokołu TLS dla każdego konta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>Testowanie minimalnej wersji protokołu TLS z poziomu klienta

Aby sprawdzić, czy minimalna wymagana wersja protokołu TLS dla konta magazynu zabrania wywołań wykonanych w starszej wersji, można skonfigurować klienta do korzystania ze starszej wersji protokołu TLS. Aby uzyskać więcej informacji o konfigurowaniu klienta do korzystania z określonej wersji protokołu TLS, zobacz [konfigurowanie Transport Layer Security (TLS) dla aplikacji klienckiej](transport-layer-security-configure-client-version.md).

Gdy klient uzyskuje dostęp do konta magazynu przy użyciu wersji TLS, która nie spełnia wymagań dotyczących minimalnej wersji protokołu TLS skonfigurowanej dla tego konta, usługa Azure Storage zwraca kod błędu 400 (Nieprawidłowe żądanie) i komunikat informujący o tym, że używana wersja protokołu TLS nie jest dozwolona w przypadku wysyłania żądań do tego konta magazynu.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie Transport Layer Security (TLS) dla aplikacji klienckiej](transport-layer-security-configure-client-version.md)
- [Zalecenia dotyczące zabezpieczeń usługi BLOB Storage](../blobs/security-recommendations.md)
