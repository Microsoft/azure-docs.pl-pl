---
title: Zapobiegaj autoryzacji za pomocą klucza współużytkowanego (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Aby wymagać od klientów używania usługi Azure AD do autoryzacji żądań, można nie zezwalać na żądania do konta magazynu autoryzowanego za pomocą klucza współużytkowanego (wersja zapoznawcza).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/20/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: ce0ea938cac4afa043b8770a4d6a98f08ec145ec
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484893"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Zapobiegaj autoryzacji klucza współużytkowanego dla konta usługi Azure Storage (wersja zapoznawcza)

Każde bezpieczne żądanie do konta usługi Azure Storage musi być autoryzowane. Domyślnie żądania mogą być autoryzowane przy użyciu poświadczeń usługi Azure Active Directory (Azure AD) lub klucza dostępu do konta na potrzeby autoryzacji klucza współużytkowanego. Z tych dwóch typów autoryzacji usługa Azure AD zapewnia najważniejsze zabezpieczenia i łatwość użycia w porównaniu z kluczem współdzielonym i jest zalecana przez firmę Microsoft. Aby wymagać od klientów używania usługi Azure AD do autoryzacji żądań, można nie zezwalać na żądania do konta magazynu autoryzowanego za pomocą klucza współużytkowanego (wersja zapoznawcza).

Jeśli nie zezwolisz na autoryzację klucza współużytkowanego dla konta magazynu, usługa Azure Storage odrzuci wszystkie kolejne żądania do tego konta, które są autoryzowane przy użyciu kluczy dostępu do konta. Tylko zabezpieczone żądania, które są autoryzowane z usługą Azure AD, będą się kończyć powodzeniem. Aby uzyskać więcej informacji o korzystaniu z usługi Azure AD, zobacz [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory](storage-auth-aad.md).

> [!WARNING]
> Usługa Azure Storage obsługuje autoryzację usługi Azure AD tylko w przypadku żądań do obiektów blob i queue storage. Jeśli użytkownik nie zezwala na autoryzację za pomocą klucza współdzielonego dla konta magazynu, żądania do Azure Files lub magazynu tabel, które używają autoryzacji klucza wspólnego, zakończą się niepowodzeniem.
>
> W trakcie okresu zapoznawczego żądania kierowane do Azure Files lub magazynu tabel, które używają tokenów sygnatury dostępu współdzielonego, które zostały wygenerowane przy użyciu kluczy dostępu do konta, zakończą się pomyślnie, gdy autoryzacja klucza współużytkowanego jest niedozwolona. Aby uzyskać więcej informacji, zobacz [Informacje o wersji zapoznawczej](#about-the-preview).
>
> Nie zezwalanie na dostęp do klucza współużytkowanego dla konta magazynu nie ma wpływu na połączenia SMB z Azure Files.
>
> Firma Microsoft zaleca Migrowanie wszelkich Azure Files lub danych magazynu tabel do oddzielnego konta magazynu przed uniemożliwieniem dostępu do konta za pośrednictwem klucza współużytkowanego lub niestosowanie tego ustawienia do kont magazynu, które obsługują obciążenia Azure Files lub magazynu tabel.

W tym artykule opisano sposób wykrywania żądań wysyłanych za pomocą autoryzacji klucza wspólnego oraz sposobu korygowania autoryzacji klucza współużytkowanego dla konta magazynu. Aby dowiedzieć się, jak zarejestrować się w wersji zapoznawczej, zobacz [Informacje o wersji zapoznawczej](#about-the-preview).

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>Wykrywanie typu autoryzacji używanego przez aplikacje klienckie

W przypadku uniemożliwienia autoryzacji klucza współużytkowanego dla konta magazynu żądania od klientów, którzy korzystają z kluczy dostępu do konta na potrzeby autoryzacji klucza wspólnego, zakończą się niepowodzeniem. Aby dowiedzieć się, jak nie zezwalać na stosowanie autoryzacji klucza współużytkowanego, należy włączyć rejestrowanie i metryki dla konta magazynu. Następnie można analizować wzorce żądań na koncie w określonym czasie, aby określić, jak żądania są autoryzowane.

Użyj metryk, aby określić liczbę żądań, które otrzymuje konto magazynu, które są autoryzowane przy użyciu klucza współużytkowanego lub sygnatury dostępu współdzielonego (SAS). Użyj dzienników, aby określić, którzy klienci wysyłają te żądania.

Aby uzyskać więcej informacji na temat interpretacji żądań z sygnaturą dostępu współdzielonego w ramach wersji zapoznawczej, zobacz [Informacje o wersji zapoznawczej](#about-the-preview).

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>Monitoruj liczbę żądań autoryzowanych za pomocą klucza współużytkowanego

Aby śledzić sposób autoryzacji żądań do konta magazynu, Użyj usługi Azure Eksplorator metryk w Azure Portal. Aby uzyskać więcej informacji na temat Eksplorator metryk, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../../azure-monitor/platform/metrics-getting-started.md).

Wykonaj następujące kroki, aby utworzyć metrykę, która śledzi żądania wykonane z kluczem udostępnionym lub SYGNATURą dostępu współdzielonego:

1. W witrynie Azure Portal przejdź do swojego konta magazynu. W sekcji **monitorowanie** wybierz pozycję **metryki**.
1. Wybierz pozycję **Dodaj metrykę**. W oknie dialogowym **Metryka** określ następujące wartości:
    1. Pozostaw pole **zakres** jako nazwę konta magazynu.
    1. Ustaw **przestrzeń nazw metryki** na *konto*. Ta Metryka będzie zgłaszać wszystkie żądania do konta magazynu.
    1. Ustaw pole **Metryka** na *transakcje*.
    1. Ustaw pole **agregacji** jako *sumowanie*.

    Nowa Metryka będzie zawierać sumę liczby transakcji dla konta magazynu w danym przedziale czasu. Wynikowa Metryka zostanie wyświetlona, jak pokazano na poniższej ilustracji:

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania metryki do sumowania transakcji wykonanych za pomocą klucza współużytkowanego lub SAS":::

1. Następnie wybierz przycisk **Dodaj filtr** , aby utworzyć filtr metryki dla typu autoryzacji.
1. W oknie dialogowym **Filtr** określ następujące wartości:
    1. Ustaw wartość **Właściwości** na *uwierzytelnianie*.
    1. Ustaw wartość pola **operator** na znak równości (=).
    1. W polu **wartości** wybierz pozycję *klucz konta* i *sygnatura dostępu współdzielonego*.
1. W prawym górnym rogu wybierz zakres czasu, dla którego chcesz wyświetlić metrykę. Możesz również wskazać, jak szczegółowy jest agregacja żądań, określając interwały w dowolnym miejscu od 1 minuty do 1 miesiąca. Na przykład ustaw **zakres czasu** na 30 dni i **stopień szczegółowości czasu** na 1 dzień, aby zobaczyć żądania agregowane według dnia w ciągu ostatnich 30 dni.

Po skonfigurowaniu metryki żądania kierowane do konta magazynu rozpoczną się na wykresie. Na poniższej ilustracji przedstawiono żądania autoryzowane za pomocą klucza współużytkowanego lub z tokenem SAS. Żądania są agregowane dziennie w ciągu ostatnich 30 dni.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="Zrzut ekranu przedstawiający zagregowane żądania autoryzowane za pomocą klucza współużytkowanego":::

Istnieje również możliwość skonfigurowania reguły alertu w celu powiadomienia użytkownika o określonej liczbie żądań autoryzowanych za pomocą klucza współużytkowanego dla konta magazynu. Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów metryk i zarządzanie nimi przy użyciu Azure monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>Analizuj dzienniki, aby identyfikować klientów, którzy autoryzują żądania z kluczem udostępnionym lub SYGNATURą dostępu współdzielonego

Dzienniki usługi Azure Storage przechwytują szczegółowe informacje o żądaniach dotyczących konta magazynu, w tym o sposobie autoryzacji żądania. Można analizować dzienniki, aby określić klientów, którzy autoryzują żądania z użyciem klucza współużytkowanego lub tokenu SAS.

Aby rejestrować żądania na koncie usługi Azure Storage w celu ocenienia sposobu ich autoryzacji, możesz użyć usługi Azure Storage w Azure Monitor (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [monitorowanie usługi Azure Storage](../blobs/monitor-blob-storage.md).

Rejestrowanie w usłudze Azure Storage w Azure Monitor obsługuje używanie zapytań dzienników do analizowania danych dziennika. Aby wykonywać zapytania dotyczące dzienników, możesz użyć obszaru roboczego usługi Azure Log Analytics. Aby dowiedzieć się więcej o zapytaniach dziennika, zobacz [Samouczek: Rozpoczynanie pracy z zapytaniami log Analytics](../../azure-monitor/log-query/log-analytics-tutorial.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Utwórz ustawienie diagnostyczne w Azure Portal

Aby rejestrować dane usługi Azure Storage za pomocą Azure Monitor i analizować je za pomocą usługi Azure Log Analytics, należy najpierw utworzyć ustawienie diagnostyczne wskazujące typy żądań i usługi magazynu, dla których mają być rejestrowane dane. Aby utworzyć ustawienie diagnostyczne w Azure Portal, wykonaj następujące kroki:

1. Zarejestruj się w [usłudze Azure Storage w wersji zapoznawczej Azure monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Utwórz nowy obszar roboczy Log Analytics w subskrypcji zawierającej konto usługi Azure Storage lub Użyj istniejącego Log Analytics obszaru roboczego. Po skonfigurowaniu rejestrowania dla konta magazynu dzienniki będą dostępne w obszarze roboczym Log Analytics. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W sekcji monitorowanie wybierz pozycję **Ustawienia diagnostyczne (wersja zapoznawcza)**.
1. Wybierz usługę Azure Storage, dla której chcesz rejestrować żądania. Na przykład wybierz **obiekt BLOB** , aby rejestrować żądania do magazynu obiektów BLOB.
1. Wybierz pozycję **Dodaj ustawienia diagnostyczne**.
1. Podaj nazwę dla ustawienia diagnostyki.
1. W obszarze **szczegóły kategorii** w sekcji **Dziennik** wybierz pozycję **StorageRead**, **StorageWrite** i **StorageDelete** , aby rejestrować wszystkie żądania danych do wybranej usługi.
1. W obszarze **szczegóły miejsca docelowego** wybierz pozycję **Wyślij do log Analytics**. Wybierz swoją subskrypcję i utworzony wcześniej obszar roboczy Log Analytics, jak pokazano na poniższej ilustracji.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia ustawień diagnostycznych żądań rejestrowania":::

Możesz utworzyć ustawienia diagnostyczne dla każdego typu zasobu usługi Azure Storage na koncie magazynu.

Po utworzeniu ustawienia diagnostycznego żądania kierowane do konta magazynu są następnie rejestrowane zgodnie z tym ustawieniem. Aby uzyskać więcej informacji, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników zasobów i metryk na platformie Azure](../../azure-monitor/platform/diagnostic-settings.md).

Aby uzyskać informacje na temat pól dostępnych w dziennikach usługi Azure Storage w Azure Monitor, zobacz [dzienniki zasobów (wersja zapoznawcza)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>Dzienniki zapytań dla żądań z użyciem klucza współużytkowanego lub SYGNATURy dostępu współdzielonego

Dzienniki usługi Azure Storage w Azure Monitor obejmują typ autoryzacji, który został użyty do wysłania żądania do konta magazynu. Aby pobrać dzienniki dla żądań wykonanych w ciągu ostatnich siedmiu dni, które były autoryzowane za pomocą klucza współużytkowanego lub SAS, Otwórz obszar roboczy Log Analytics. Następnie wklej następujące zapytanie do nowego zapytania dziennika i uruchom je. To zapytanie wyświetla dziesięć adresów IP, które są najczęściej wysyłane żądania z użyciem klucza współużytkowanego lub SYGNATURy dostępu współdzielonego:

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

Możesz również skonfigurować regułę alertu na podstawie tego zapytania, aby poinformować użytkownika o żądaniach autoryzowanych za pomocą klucza współużytkowanego lub SYGNATURy dostępu współdzielonego. Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-authorization-via-shared-key"></a>Koryguj autoryzację za pomocą klucza współużytkowanego

Po przeanalizowaniu sposobu autoryzacji żądań do konta magazynu możesz podjąć działania, aby uniemożliwić dostęp za pośrednictwem klucza współużytkowanego. Najpierw należy zaktualizować wszystkie aplikacje, które używają autoryzacji klucza współużytkowanego do korzystania z usługi Azure AD. Dzienniki i metryki można monitorować zgodnie z opisem w artykule [Wykrywanie typu autoryzacji używanego przez aplikacje klienckie](#detect-the-type-of-authorization-used-by-client-applications) do śledzenia przejścia. Aby uzyskać więcej informacji na temat korzystania z usługi Azure AD z danymi obiektów blob i kolejek, zobacz [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory](storage-auth-aad.md).

Jeśli masz pewność, że możesz bezpiecznie odrzucać żądania autoryzowane za pomocą klucza współużytkowanego, możesz ustawić **wartość false** dla właściwości **AllowSharedKeyAccess** dla konta magazynu.

Właściwość **AllowSharedKeyAccess** nie jest domyślnie ustawiona i nie zwraca wartości, dopóki nie zostanie jawnie ustawiona. Konto magazynu zezwala na żądania autoryzowane przy użyciu klucza współużytkowanego, gdy wartość właściwości jest **równa null** lub jeśli jest **true**.

> [!WARNING]
> Jeśli dowolni klienci uzyskują dostęp do danych na koncie magazynu za pomocą klucza współużytkowanego, firma Microsoft zaleca, aby przeprowadzić migrację tych klientów do usługi Azure AD przed zezwoleniem na dostęp do klucza dostępu do konta magazynu.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby nie zezwalać na autoryzację klucza współużytkowanego dla konta magazynu w Azure Portal, wykonaj następujące czynności:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W obszarze **Ustawienia** Znajdź ustawienie **Konfiguracja** .
1. Ustaw opcję **Zezwalaj na dostęp do klucza wspólnego** na **wyłączony**.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="Zrzut ekranu przedstawiający sposób niezezwalania na dostęp do klucza udostępnionego dla konta":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uniemożliwić autoryzację klucza współużytkowanego dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.9.1 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Następnie skonfiguruj Właściwość **allowSharedKeyAccess** dla nowego lub istniejącego konta magazynu.

Poniższy przykład pokazuje, jak ustawić właściwość **allowSharedKeyAccess** przy użyciu interfejsu wiersza polecenia platformy Azure. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
$storage_account_id=$(az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowSharedKeyAccess=false

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowSharedKeyAccess \
    --output tsv
```

---

Po odrzuceniu autoryzacji klucza udostępniania żądanie do konta magazynu z autoryzacją klucza wspólnego zakończy się niepowodzeniem z kodem błędu 403 (dostęp zabroniony). Usługa Azure Storage zwraca błąd wskazujący, że autoryzacja na podstawie klucza nie jest dozwolona na koncie magazynu.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>Sprawdź, czy dostęp do klucza wspólnego jest niedozwolony

Aby sprawdzić, czy autoryzacja klucza współużytkowanego nie jest już dozwolona, możesz spróbować wywołać operację danych przy użyciu klucza dostępu do konta. Poniższy przykład próbuje utworzyć kontener przy użyciu klucza dostępu. To wywołanie zakończy się niepowodzeniem, gdy autoryzacja klucza współużytkowanego nie jest dozwolona dla konta magazynu. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!NOTE]
> Żądania anonimowe nie są autoryzowane i będą działać, jeśli skonfigurowano konto magazynu i kontener dla anonimowego publicznego dostępu do odczytu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów BLOB](../blobs/anonymous-read-access-configure.md).

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>Sprawdź ustawienie dostępu do klucza wspólnego dla wielu kont

Aby sprawdzić ustawienie dostępu do klucza udostępnionego w zestawie kont magazynu o optymalnej wydajności, można użyć Eksploratora Azure Resource Graph w Azure Portal. Aby dowiedzieć się więcej o korzystaniu z Eksploratora grafów zasobów, zobacz [Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph](../../governance/resource-graph/first-query-portal.md).

Uruchomienie następującego zapytania w Eksploratorze grafu zasobów zwraca listę kont magazynu i wyświetla ustawienia dostępu do klucza wspólnego dla każdego konta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>Informacje o tym, jak nie zezwalanie na klucz współużytkowany ma wpływ na tokeny SAS

Gdy klucz współużytkowany jest niedozwolony dla konta magazynu, usługa Azure Storage obsługuje tokeny SYGNATURy dostępu współdzielonego na podstawie typu sygnatury dostępu współdzielonego i usługi, która jest przeznaczona dla żądania. W poniższej tabeli przedstawiono sposób autoryzowania każdego typu sygnatury dostępu współdzielonego oraz sposób obsługi przez usługę Azure Storage tego skojarzenia zabezpieczeń, gdy właściwość **AllowSharedKeyAccess** konta magazynu ma **wartość false**.

| Typ sygnatury dostępu współdzielonego | Typ autoryzacji | Zachowanie, gdy AllowSharedKeyAccess ma wartość false |
|-|-|-|
| Sygnatura dostępu współdzielonego użytkownika (tylko magazyn obiektów BLOB) | Azure AD | Żądanie jest dozwolone. Firma Microsoft zaleca korzystanie z funkcji dostępu współdzielonego delegowania użytkowników w celu zapewnienia bezpieczeństwa. |
| SAS usługi | Klucz wspólny | Odmowa żądania dla magazynu obiektów BLOB. Żądanie jest dozwolone dla magazynu kolejek i tabel oraz dla Azure Files. Aby uzyskać więcej informacji, zobacz temat [żądania z tokenami SAS są dozwolone dla kolejek, tabel i plików, gdy AllowSharedKeyAccess ma wartość false](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) w sekcji **Informacje o wersji zapoznawczej** . |
| SAS konta | Klucz wspólny | Odmowa żądania dla magazynu obiektów BLOB. Żądanie jest dozwolone dla magazynu kolejek i tabel oraz dla Azure Files. Aby uzyskać więcej informacji, zobacz temat [żądania z tokenami SAS są dozwolone dla kolejek, tabel i plików, gdy AllowSharedKeyAccess ma wartość false](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) w sekcji **Informacje o wersji zapoznawczej** . |

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Rozważ zgodność z innymi narzędziami i usługami platformy Azure

Wiele usług platformy Azure korzysta z autoryzacji klucza współużytkowanego do komunikowania się z usługą Azure Storage. Jeśli nie zezwalasz na autoryzację klucza współużytkowanego dla konta magazynu, te usługi nie będą mogły uzyskać dostępu do danych na tym koncie, a Twoje aplikacje mogą mieć negatywny wpływ.

Niektóre narzędzia platformy Azure oferują możliwość korzystania z autoryzacji usługi Azure AD w celu uzyskania dostępu do usługi Azure Storage. W poniższej tabeli wymieniono popularne narzędzia i uwagi dotyczące platformy Azure, dzięki którym można autoryzować żądania do usługi Azure Storage za pomocą usługi Azure AD.

| Narzędzie platformy Azure | Autoryzacja usługi Azure AD do usługi Azure Storage |
|-|-|
| Azure Portal | Obsługiwane. Aby uzyskać informacje na temat autoryzacji konta usługi Azure AD z poziomu Azure Portal, zobacz [Wybieranie metody autoryzacji dostępu do danych obiektów BLOB w Azure Portal](../blobs/authorize-data-operations-portal.md). |
| Narzędzie AzCopy | Obsługiwane w przypadku usługi BLOB Storage. Aby uzyskać informacje na temat autoryzacji operacji AzCopy, zobacz [Wybieranie sposobu dostarczania poświadczeń autoryzacji](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) w dokumentacji AzCopy. |
| Eksplorator usługi Azure Storage | Obsługiwane tylko w przypadku usługi BLOB Storage i tylko Azure Data Lake Storage Gen2. Dostęp do usługi queue storage w usłudze Azure AD nie jest obsługiwany. Upewnij się, że wybrano prawidłową dzierżawę usługi Azure AD. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z Eksplorator usługi Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) |
| Azure PowerShell | Obsługiwane. Aby uzyskać informacje na temat sposobu autoryzacji poleceń programu PowerShell dla operacji obiektu BLOB lub kolejki w usłudze Azure AD, zobacz [Uruchamianie poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów BLOB](../blobs/authorize-data-operations-powershell.md) lub [uruchamiania poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych kolejki](../queues/authorize-data-operations-powershell.md). |
| Interfejs wiersza polecenia platformy Azure | Obsługiwane. Aby uzyskać informacje na temat sposobu autoryzacji poleceń interfejsu wiersza polecenia platformy Azure z usługą Azure AD w celu uzyskania dostępu do danych obiektów blob i kolejek, zobacz [Uruchamianie poleceń interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektów blob lub](../blobs/authorize-data-operations-cli.md) |
| Azure IoT Hub | Obsługiwane. Aby uzyskać więcej informacji, zobacz [IoT Hub obsługa sieci wirtualnych](../../iot-hub/virtual-network-support.md). |
| Azure Cloud Shell | Azure Cloud Shell jest zintegrowaną powłoką w Azure Portal. Azure Cloud Shell hostuje pliki trwałości w udziale plików platformy Azure na koncie magazynu. Te pliki staną się niedostępne, jeśli autoryzacja klucza współdzielonego jest niedozwolona dla tego konta magazynu. Aby uzyskać więcej informacji, zobacz [łączenie magazynu Microsoft Azure plików](../../cloud-shell/overview.md#connect-your-microsoft-azure-files-storage). <br /><br /> Aby uruchomić polecenia w Azure Cloud Shell, aby zarządzać kontami magazynu, dla których dostęp do klucza wspólnego jest niedozwolony, najpierw upewnij się, że masz przyznane odpowiednie uprawnienia do tych kont za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC). Aby uzyskać więcej informacji, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?](../../role-based-access-control/overview.md) |

## <a name="about-the-preview"></a>Informacje o wersji zapoznawczej

Wersja zapoznawcza dotycząca niedozwolonej autoryzacji klucza współużytkowanego jest dostępna w chmurze publicznej platformy Azure. Jest on obsługiwany w przypadku kont magazynu, które używają tylko modelu wdrażania Azure Resource Manager. Aby uzyskać informacje o tym, które konta magazynu korzystają z modelu wdrażania Azure Resource Manager, zobacz [typy kont magazynu](storage-account-overview.md#types-of-storage-accounts).

Aby zarejestrować się w wersji zapoznawczej, zobacz [usługa Azure Storage zezwala na dostęp do udostępnionej wersji zapoznawczej](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUN1o4TUtUUzZBV0JYVlhKQ1FITDlVUUU0Ui4u).

> [!IMPORTANT]
> Ta wersja zapoznawcza jest przeznaczona wyłącznie do użytku nieprodukcyjnego.

Wersja zapoznawcza zawiera ograniczenia opisane w poniższych sekcjach.

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>Metryki i rejestrowanie raportuje wszystkie żądania z sygnaturą dostępu współdzielonego bez względu na to, jak są autoryzowane

Usługa Azure Metrics i rejestrowanie w Azure Monitor nie rozróżniają różnych typów sygnatur dostępu współdzielonego w wersji zapoznawczej. Filtr **sygnatury dostępu współdzielonego** w usłudze Azure Eksplorator metryk i pole **sygnatura dostępu współdzielonego** w usłudze Azure Storage w Azure monitor oba żądania raportów, które są autoryzowane z dowolnego typu SAS. Różne typy sygnatur dostępu współdzielonego są jednak autoryzowane inaczej i zachowują się inaczej po niedozwolonym dostępie do klucza wspólnego:

- Token SYGNATURy dostępu współdzielonego usługi lub token SYGNATURy dostępu współdzielonego konta jest autoryzowany za pomocą klucza współużytkowanego i nie będzie dozwolony dla żądania do magazynu obiektów blob, gdy właściwość **AllowSharedKeyAccess** ma wartość **false**.
- Sygnatura dostępu współdzielonego delegowania użytkowników jest autoryzowana w usłudze Azure AD i będzie dozwolona na żądanie do magazynu obiektów blob, gdy właściwość **AllowSharedKeyAccess** ma wartość **false**.

Podczas oceniania ruchu do konta magazynu należy pamiętać, że metryki i dzienniki zgodnie z opisem w artykule [Wykrywanie typu autoryzacji używanego przez aplikacje klienckie](#detect-the-type-of-authorization-used-by-client-applications) mogą obejmować żądania wysyłane za pomocą sygnatury dostępu współdzielonego delegowanego przez użytkownika. Aby uzyskać więcej informacji o tym, jak usługa Azure Storage reaguje na sygnaturę dostępu współdzielonego, gdy właściwość **AllowSharedKeyAccess** ma **wartość false**, zobacz temat [jak nie zezwalać na używanie klucza współdzielonego na tokeny SAS](#understand-how-disallowing-shared-key-affects-sas-tokens).

### <a name="requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false"></a>Żądania z tokenami SAS są dozwolone dla kolejek, tabel i plików, gdy AllowSharedKeyAccess ma wartość false

Gdy dostęp do klucza wspólnego jest niedozwolony dla konta magazynu w wersji zapoznawczej, sygnatury dostępu współdzielonego, które są przeznaczone dla zasobów kolejki, tabeli lub Azure Files, nadal są dozwolone. To ograniczenie dotyczy tokenów sygnatury dostępu współdzielonego usługi i tokenów SAS konta. Oba typy sygnatur dostępu współdzielonego są autoryzowane za pomocą klucza współużytkowanego.

## <a name="next-steps"></a>Następne kroki

- [Autoryzowanie dostępu do danych w usłudze Azure Storage](storage-auth.md)
- [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory](storage-auth-aad.md)
- [Autoryzacja przy użyciu klucza wspólnego](/rest/api/storageservices/authorize-with-shared-key)