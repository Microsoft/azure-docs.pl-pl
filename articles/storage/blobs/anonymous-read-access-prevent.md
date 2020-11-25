---
title: Zapobiegaj Anonimowemu dostępowi do odczytu do kontenerów i obiektów BLOB
titleSuffix: Azure Storage
description: Dowiedz się, jak analizować anonimowe żądania na koncie magazynu oraz jak zapobiegać Anonimowemu dostępowi do całego konta magazynu lub dla danego kontenera.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 01a5c696a41b9361c35e7af90f68088acea2944b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913780"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Zapobiegaj Anonimowemu dostępowi do odczytu do kontenerów i obiektów BLOB

Anonimowy publiczny dostęp do odczytu do kontenerów i obiektów BLOB w usłudze Azure Storage to wygodny sposób udostępniania danych, ale może również stanowić zagrożenie bezpieczeństwa. Ważne jest, aby w rozsądny sposób zarządzać dostępem anonimowym i zrozumieć, jak oszacować anonimowy dostęp do danych. Złożoność operacyjna, błąd ludzki lub złośliwe ataki na dane, które są publicznie dostępne, mogą powodować kosztowne naliczanie danych. Firma Microsoft zaleca włączenie dostępu anonimowego tylko wtedy, gdy jest to konieczne w scenariuszu aplikacji.

Domyślnie publiczny dostęp do danych obiektów BLOB jest zawsze zabroniony. Jednak domyślna konfiguracja konta magazynu zezwala użytkownikowi z odpowiednimi uprawnieniami do konfigurowania publicznego dostępu do kontenerów i obiektów BLOB na koncie magazynu. W celu zwiększenia bezpieczeństwa można uniemożliwić dostęp publiczny do konta magazynu niezależnie od ustawień dostępu publicznego dla poszczególnych kontenerów. Niezezwalanie na publiczny dostęp do konta magazynu uniemożliwia użytkownikowi włączenie dostępu publicznego do kontenera na koncie. Firma Microsoft zaleca, aby nie zezwalać na publiczny dostęp do konta magazynu, chyba że wymaga tego scenariusz. Niezezwalanie na dostęp publiczny pomaga uniknąć naruszeń danych spowodowanych przez niepożądany dostęp anonimowy.

Jeśli nie dołączysz publicznego dostępu do obiektów BLOB dla konta magazynu, usługa Azure Storage odrzuci wszystkie anonimowe żądania do tego konta. Gdy dostęp publiczny jest niedozwolony dla konta, kontenery w tym koncie nie mogą zostać skonfigurowane do dostępu publicznego. Wszystkie kontenery, które zostały już skonfigurowane do dostępu publicznego, nie będą już akceptować żądań anonimowych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów BLOB](anonymous-read-access-configure.md).

W tym artykule opisano sposób korzystania z platformy przeciągania (wykrywania-korygowania-Audit-ładu) w celu ciągłego zarządzania dostępem publicznym do kont magazynu.

## <a name="detect-anonymous-requests-from-client-applications"></a>Wykrywaj anonimowe żądania z aplikacji klienckich

Jeśli użytkownik nie zezwala na publiczny dostęp do odczytu dla konta magazynu, ryzyko odrzuca żądania do kontenerów i obiektów blob, które są aktualnie skonfigurowane do dostępu publicznego. Niezezwalanie na dostęp publiczny do konta magazynu zastępuje ustawienia dostępu publicznego dla poszczególnych kontenerów na tym koncie magazynu. Gdy dostęp publiczny jest niedozwolony dla konta magazynu, wszelkie przyszłe żądania anonimowe do tego konta będą kończyć się niepowodzeniem.

Aby zrozumieć, jak nie zezwalać na dostęp publiczny do aplikacji klienckich, firma Microsoft zaleca włączenie rejestrowania i metryk dla tego konta oraz analizowanie wzorców żądań anonimowych w przedziale czasu. Użyj metryk, aby określić liczbę żądań anonimowych do konta magazynu, a następnie użyj dzienników, aby określić, które kontenery są dostępne anonimowo.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Monitoruj anonimowe żądania za pomocą Eksplorator metryk

Aby śledzić anonimowe żądania na koncie magazynu, Użyj usługi Azure Eksplorator metryk w Azure Portal. Aby uzyskać więcej informacji na temat Eksplorator metryk, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../../azure-monitor/platform/metrics-getting-started.md).

Wykonaj następujące kroki, aby utworzyć metrykę, która śledzi żądania anonimowe:

1. W witrynie Azure Portal przejdź do swojego konta magazynu. W sekcji **monitorowanie** wybierz pozycję **metryki**.
1. Wybierz pozycję **Dodaj metrykę**. W oknie dialogowym **Metryka** określ następujące wartości:
    1. Pozostaw pole zakres jako nazwę konta magazynu.
    1. Ustaw **przestrzeń nazw metryki** na *obiekt BLOB*. Ta Metryka będzie zgłaszać żądania tylko do magazynu obiektów BLOB.
    1. Ustaw pole **Metryka** na *transakcje*.
    1. Ustaw pole **agregacji** jako *sumowanie*.

    Nowa Metryka będzie zawierać sumę liczby transakcji z magazynem obiektów BLOB w danym przedziale czasu. Wynikowa Metryka zostanie wyświetlona, jak pokazano na poniższej ilustracji:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania metryki do sumowania transakcji obiektów BLOB":::

1. Następnie wybierz przycisk **Dodaj filtr** , aby utworzyć filtr metryk dla żądań anonimowych.
1. W oknie dialogowym **Filtr** określ następujące wartości:
    1. Ustaw wartość **Właściwości** na *uwierzytelnianie*.
    1. Ustaw wartość pola **operator** na znak równości (=).
    1. Ustaw wartość pola **wartości** *anonimowe*.
1. W prawym górnym rogu wybierz przedział czasu, w którym ma zostać wyświetlona Metryka. Możesz również wskazać, jak szczegółowy jest agregacja żądań, określając interwały w dowolnym miejscu od 1 minuty do 1 miesiąca.

Po skonfigurowaniu metryki anonimowe żądania pojawią się na wykresie. Na poniższej ilustracji przedstawiono anonimowe żądania agregowane w ciągu ostatnich 30 minut.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Zrzut ekranu przedstawiający zagregowane żądania anonimowe względem magazynu obiektów BLOB":::

Istnieje również możliwość skonfigurowania reguły alertu w celu powiadomienia użytkownika o wprowadzeniu pewnej liczby żądań anonimowych do konta magazynu. Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów metryk i zarządzanie nimi przy użyciu Azure monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Analizuj dzienniki, aby identyfikować kontenery otrzymujące anonimowe żądania

Dzienniki usługi Azure Storage przechwytują szczegółowe informacje o żądaniach dotyczących konta magazynu, w tym o sposobie autoryzacji żądania. Można analizować dzienniki, aby określić, które kontenery otrzymują anonimowe żądania.

Aby rejestrować żądania na koncie usługi Azure Storage w celu ocenienia żądań anonimowych, możesz użyć rejestrowania w usłudze Azure Storage w Azure Monitor (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [monitorowanie usługi Azure Storage](./monitor-blob-storage.md).

Rejestrowanie w usłudze Azure Storage w Azure Monitor obsługuje używanie zapytań dzienników do analizowania danych dziennika. Aby wykonywać zapytania dotyczące dzienników, możesz użyć obszaru roboczego usługi Azure Log Analytics. Aby dowiedzieć się więcej o zapytaniach dziennika, zobacz [Samouczek: Rozpoczynanie pracy z zapytaniami log Analytics](../../azure-monitor/log-query/log-analytics-tutorial.md).

> [!NOTE]
> Wersja zapoznawcza usługi Azure Storage w Azure Monitor jest obsługiwana tylko w chmurze publicznej platformy Azure. Chmury rządowe nie obsługują rejestrowania w usłudze Azure Storage za pomocą Azure Monitor.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Utwórz ustawienie diagnostyczne w Azure Portal

Aby rejestrować dane usługi Azure Storage za pomocą Azure Monitor i analizować je za pomocą usługi Azure Log Analytics, należy najpierw utworzyć ustawienie diagnostyczne wskazujące typy żądań i usługi magazynu, dla których mają być rejestrowane dane. Aby utworzyć ustawienie diagnostyczne w Azure Portal, wykonaj następujące kroki:

1. Zarejestruj się w [usłudze Azure Storage w wersji zapoznawczej Azure monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Utwórz nowy obszar roboczy Log Analytics w subskrypcji zawierającej konto usługi Azure Storage. Po skonfigurowaniu rejestrowania dla konta magazynu dzienniki będą dostępne w obszarze roboczym Log Analytics. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W sekcji monitorowanie wybierz pozycję **Ustawienia diagnostyczne (wersja zapoznawcza)**.
1. Wybierz pozycję **obiekt BLOB** , aby rejestrować żądania skierowane do magazynu obiektów BLOB.
1. Wybierz pozycję **Dodaj ustawienia diagnostyczne**.
1. Podaj nazwę dla ustawienia diagnostyki.
1. W obszarze **szczegóły kategorii** w sekcji **Dziennik** wybierz typy żądań do zarejestrowania. Wszystkie żądania anonimowe będą żądaniami odczytu, więc wybierz pozycję **StorageRead** , aby przechwytywać anonimowe żądania.
1. W obszarze **szczegóły miejsca docelowego** wybierz pozycję **Wyślij do log Analytics**. Wybierz swoją subskrypcję i utworzony wcześniej obszar roboczy Log Analytics, jak pokazano na poniższej ilustracji.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia ustawień diagnostycznych żądań rejestrowania":::

Po utworzeniu ustawienia diagnostycznego żądania kierowane do konta magazynu są następnie rejestrowane zgodnie z tym ustawieniem. Aby uzyskać więcej informacji, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników zasobów i metryk na platformie Azure](../../azure-monitor/platform/diagnostic-settings.md).

Aby uzyskać informacje na temat pól dostępnych w dziennikach usługi Azure Storage w Azure Monitor, zobacz [dzienniki zasobów (wersja zapoznawcza)](./monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Dzienniki zapytań dla żądań anonimowych

Dzienniki usługi Azure Storage w Azure Monitor obejmują typ autoryzacji, który został użyty do wysłania żądania do konta magazynu. W zapytaniu dziennika odfiltruj Właściwość **AuthenticationType** , aby wyświetlić anonimowe żądania.

Aby pobrać dzienniki z ostatnich 7 dni dla żądań anonimowych względem usługi BLOB Storage, Otwórz obszar roboczy Log Analytics. Następnie wklej następujące zapytanie do nowego zapytania dziennika i uruchom je:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

Możesz również skonfigurować regułę alertu na podstawie tego zapytania, aby powiadomić o anonimowych żądaniach. Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Koryguj anonimowy dostęp publiczny

Po przeprowadzeniu oceny żądań anonimowych do kontenerów i obiektów BLOB na koncie magazynu można podjąć działania w celu ograniczenia lub uniemożliwienia dostępu publicznego. Jeśli niektóre kontenery na koncie magazynu mogą być dostępne na potrzeby dostępu publicznego, można skonfigurować ustawienie dostępu publicznego dla każdego kontenera na koncie magazynu. Ta opcja zapewnia największą kontrolę nad dostępem publicznym. Aby uzyskać więcej informacji, zobacz [Ustawianie publicznego poziomu dostępu dla kontenera](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

W celu zwiększenia bezpieczeństwa można uniemożliwić dostęp publiczny do całego konta magazynu. Ustawienie dostępu publicznego dla konta magazynu zastępuje poszczególne ustawienia kontenerów w ramach tego konta. Jeśli nie zezwolisz na dostęp publiczny do konta magazynu, wszystkie kontenery, które są skonfigurowane do zezwalania na dostęp publiczny, nie będą już anonimowo dostępne. Aby uzyskać więcej informacji, zobacz [Zezwalaj lub nie Zezwalaj na publiczny dostęp do odczytu dla konta magazynu](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

Jeśli Twój scenariusz wymaga, aby niektóre kontenery były dostępne na potrzeby dostępu publicznego, może być zalecane przeniesienie tych kontenerów i ich obiektów BLOB do kont magazynu, które są zarezerwowane dla dostępu publicznego. Następnie można uniemożliwić dostęp publiczny do innych kont magazynu.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Sprawdź, czy publiczny dostęp do obiektu BLOB jest niedozwolony

Aby sprawdzić, czy dostęp publiczny do określonego obiektu BLOB jest niedozwolony, możesz spróbować pobrać obiekt BLOB za pośrednictwem jego adresu URL. Jeśli pobieranie powiedzie się, obiekt BLOB jest nadal dostępny publicznie. Jeśli obiekt BLOB nie jest publicznie dostępny, ponieważ dostęp publiczny został niedozwolony dla konta magazynu, zostanie wyświetlony komunikat o błędzie informujący o tym, że dostęp publiczny nie jest dozwolony na tym koncie magazynu.

Poniższy przykład pokazuje, jak za pomocą programu PowerShell próbować pobrać obiekt BLOB za pomocą adresu URL. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Sprawdź, czy modyfikowanie ustawienia dostępu publicznego kontenera nie jest dozwolone

Aby sprawdzić, czy nie można zmodyfikować ustawienia dostępu publicznego kontenera po niedozwolonym dostępie publicznym dla konta magazynu, możesz spróbować zmodyfikować to ustawienie. Zmiana publicznego ustawienia dostępu kontenera zakończy się niepowodzeniem, jeśli dostęp publiczny jest niedozwolony dla konta magazynu.

Poniższy przykład pokazuje, jak za pomocą programu PowerShell próbować zmienić ustawienie dostępu publicznego kontenera. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Upewnij się, że tworzenie kontenera z włączonym dostępem publicznym nie jest dozwolone

Jeśli dostęp publiczny jest niedozwolony dla konta magazynu, nie będziesz w stanie utworzyć nowego kontenera z włączonym dostępem publicznym. Aby to sprawdzić, możesz spróbować utworzyć kontener z włączonym dostępem publicznym.

Poniższy przykład pokazuje, jak za pomocą programu PowerShell próbować utworzyć kontener z włączonym dostępem publicznym. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>Sprawdź ustawienia dostępu publicznego dla wielu kont

Aby sprawdzić ustawienia dostępu publicznego na zestawie kont magazynu z optymalną wydajnością, można użyć Eksploratora Azure Resource Graph w Azure Portal. Aby dowiedzieć się więcej o korzystaniu z Eksploratora grafów zasobów, zobacz [Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph](../../governance/resource-graph/first-query-portal.md).

Uruchomienie następującego zapytania w Eksploratorze grafu zasobów zwraca listę kont magazynu i wyświetla ustawienia dostępu publicznego dla każdego konta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

## <a name="use-azure-policy-to-audit-for-compliance"></a>Użyj Azure Policy, aby przeprowadzić inspekcję zgodności

Jeśli masz dużą liczbę kont magazynu, możesz chcieć przeprowadzić inspekcję, aby upewnić się, że te konta są skonfigurowane tak, aby uniemożliwić dostęp publiczny. Aby przeprowadzić inspekcję zestawu kont magazynu pod kątem zgodności, użyj Azure Policy. Azure Policy to usługa, za pomocą której można tworzyć i przypisywać zasady stosujące reguły do zasobów platformy Azure oraz zarządzać nimi. Azure Policy pomaga zachować zgodność tych zasobów ze standardami firmy i umowami dotyczącymi poziomu usług. Aby uzyskać więcej informacji, zobacz [omówienie Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Tworzenie zasad z efektem inspekcji

Azure Policy obsługuje efekty, które określają, co się dzieje, gdy reguła zasad zostanie oceniona względem zasobu. Efekt inspekcji tworzy ostrzeżenie, gdy zasób nie jest zgodny, ale nie zatrzymuje żądania. Aby uzyskać więcej informacji na temat efektów, zobacz [opis efektów Azure Policy](../../governance/policy/concepts/effects.md).

Aby utworzyć zasady z efektem inspekcji dla ustawienia dostępu publicznego dla konta magazynu z Azure Portal, wykonaj następujące kroki:

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
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
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

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="Zrzut ekranu przedstawiający raport zgodności dla zasad inspekcji dla dostępu publicznego obiektu BLOB":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>Użyj Azure Policy, aby wymusić autoryzowany dostęp

Azure Policy obsługuje zarządzanie chmurą dzięki zapewnieniu, że zasoby platformy Azure są zgodne z wymaganiami i standardami. Aby zapewnić, że konta magazynu w organizacji zezwalają tylko na autoryzowane żądania, można utworzyć zasady, które uniemożliwiają utworzenie nowego konta magazynu z ustawieniem dostępu publicznego, które zezwala na żądania anonimowe. Te zasady będą również zapobiegać wszystkim zmianom konfiguracji istniejącego konta, jeśli ustawienia dostępu publicznego dla tego konta nie są zgodne z zasadami.

Zasady wymuszania używają efektu Odmów, aby zapobiec żądaniu, które mogłoby utworzyć lub zmodyfikować konto magazynu, aby zezwolić na dostęp publiczny. Aby uzyskać więcej informacji na temat efektów, zobacz [opis efektów Azure Policy](../../governance/policy/concepts/effects.md).

Aby utworzyć zasady z efektem odmowy dla ustawienia dostępu publicznego, które zezwala na żądania anonimowe, wykonaj te same czynności, które opisano w temacie [Use Azure Policy, aby przeprowadzić inspekcję pod kątem zgodności](#use-azure-policy-to-audit-for-compliance), ale podaj następujący kod JSON w sekcji **Klasa policyrule** definicji zasad:

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
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Po utworzeniu zasad z efektem odmowy i przypisaniu go do zakresu użytkownik nie może utworzyć konta magazynu, które zezwala na dostęp publiczny. Użytkownik nie może wprowadzać żadnych zmian konfiguracji na istniejącym koncie magazynu, które obecnie zezwala na dostęp publiczny. Próba wykonania tej czynności spowoduje wystąpienie błędu. Ustawienie dostępu publicznego dla konta magazynu musi mieć wartość **Fałsz** , aby kontynuować tworzenie lub Konfigurowanie konta.

Na poniższej ilustracji przedstawiono błąd występujący w przypadku próby utworzenia konta magazynu, które zezwala na dostęp publiczny (domyślnie dla nowego konta), gdy zasady z efektem Odmów wymagają niedozwolonego dostępu publicznego.

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="Zrzut ekranu przedstawiający błąd występujący podczas tworzenia konta magazynu w celu naruszenia zasad":::

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów BLOB](anonymous-read-access-configure.md)
- [Anonimowe uzyskiwanie dostępu do publicznych kontenerów i obiektów BLOB przy użyciu platformy .NET](anonymous-read-access-client.md)