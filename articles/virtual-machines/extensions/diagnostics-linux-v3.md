---
title: Azure Compute — rozszerzenie diagnostyczne systemu Linux 3.0
description: Jak skonfigurować rozszerzenie diagnostyki systemu Linux (LAD) 3.0 platformy Azure w celu zbierania metryk i rejestrować zdarzenia z maszyn wirtualnych z systemem Linux uruchomionych na platformie Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.openlocfilehash: fe03bbfb33f3637eecc4e68f24846c929dad5fa4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479257"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Monitorowanie metryk i dzienników za pomocą rozszerzenia diagnostycznego systemu Linux 3.0

W tym dokumencie opisano wersję 3.0 i nowsze rozszerzenia diagnostycznego systemu Linux (LAD).

> [!IMPORTANT]
> Aby uzyskać informacje o wersji 2.3 i starszych, zobacz Monitor the performance and diagnostic data of a Linux VM (Monitorowanie danych wydajności i diagnostyki [maszyny wirtualnej z systemem Linux).](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)

## <a name="introduction"></a>Wprowadzenie

Rozszerzenie diagnostyczne systemu Linux ułatwia użytkownikowi monitorowanie kondycji maszyny wirtualnej z systemem Linux, która działa na Microsoft Azure. Ma następujące możliwości:

* Zbiera metryki wydajności systemu z maszyny wirtualnej i zapisuje je w określonej tabeli na wyznaczonym koncie magazynu.
* Pobiera zdarzenia dziennika z dziennika systemowego i zapisuje je w określonej tabeli na wyznaczonym koncie magazynu.
* Umożliwia użytkownikom dostosowywanie metryk danych, które są zbierane i przekazywane.
* Umożliwia użytkownikom dostosowywanie funkcji usługi Syslog i poziomów ważności zdarzeń, które są zbierane i przekazywane.
* Umożliwia użytkownikom przekazywanie określonych plików dziennika do wyznaczonej tabeli magazynu.
* Obsługuje wysyłanie metryk i zdarzeń dziennika do dowolnych Azure Event Hubs końcowych i obiektów blob w formacie JSON na wyznaczonym koncie magazynu.

To rozszerzenie działa z obu modelami wdrażania platformy Azure.

## <a name="install-the-extension-on-a-vm"></a>Instalowanie rozszerzenia na maszynie wirtualnej

Rozszerzenie można włączyć przy użyciu poleceń cmdlet Azure PowerShell, skryptów interfejsu wiersza polecenia platformy Azure, szablonów usługi Azure Monitor zasobów (szablonów usługi ARM) lub Azure Portal. Aby uzyskać więcej informacji, zobacz [Extensions features (Funkcje rozszerzeń).](features-linux.md)

>[!NOTE]
>Niektóre składniki rozszerzenia maszyny wirtualnej LAD są również dostarczane w rozszerzeniu maszyny wirtualnej [usługi Log Analytics.](./oms-linux.md) Z powodu tej architektury mogą wystąpić konflikty, jeśli oba rozszerzenia są wystąpienia w tym samym szablonie usługi ARM. 
>
>Aby uniknąć konfliktów czasu instalacji, użyj [ `dependsOn` dyrektywy ,](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) aby upewnić się, że rozszerzenia są instalowane sekwencyjnie. Rozszerzenia można zainstalować w obu kolejności.

Te instrukcje instalacji i [przykładowa konfiguracja do](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) pobrania w celu skonfigurowania pakietu LAD 3.0 w celu:

* Przechwyć i przechowuj te same metryki co w programie LAD 2.3.
* Przechwyć przydatny zestaw metryk systemu plików. Ta funkcja jest nowa w programie LAD 3.0.
* Przechwyć domyślną kolekcję syslog z włączoną obsługą LAD 2.3.
* Włącz środowisko Azure Portal tworzenia wykresów i alertów dotyczących metryk maszyny wirtualnej.

Konfiguracja do pobrania jest tylko przykładem. Zmodyfikuj go, aby odpowiadał Twoim potrzebom.

### <a name="supported-linux-distributions"></a>Obsługiwane dystrybucje systemu Linux

LAD obsługuje następujące dystrybucje i wersje. Lista dystrybucji i wersji ma zastosowanie tylko do obrazów dostawców systemu Linux zatwierdzonych przez platformę Azure. Rozszerzenie na ogół nie obsługuje obrazów BYOL i BYOS innych firm, takich jak urządzenia.

Dystrybucja, która wyświetla tylko wersje główne, takie jak Debian 7, jest również obsługiwana dla wszystkich wersji pomocniczych. Jeśli określono wersję pomocniczą, obsługiwana jest tylko ta wersja. Jeśli zostanie dołączony znak plus (+), obsługiwane są wersje pomocnicze równe lub nowsze niż określona wersja.

Obsługiwane dystrybucje i wersje:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4 lub więcej
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Wymagania wstępne

* **Agent systemu Linux na platformie Azure w wersji 2.2.0 lub nowszej.** Większość obrazów galerii maszyn wirtualnych platformy Azure z systemem Linux zawiera wersję 2.2.7 lub nowszą. Uruchom , `/usr/sbin/waagent -version` aby potwierdzić, że wersja jest zainstalowana na maszynie wirtualnej. Jeśli na maszynie wirtualnej jest uruchomiona starsza wersja, [zaktualizuj agenta gościa](./update-linux-agent.md).
* Interfejs **wiersza polecenia platformy Azure**. W razie potrzeby [skonfiguruj środowisko interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) na swojej maszynie.
* Polecenie **wget**. Jeśli jeszcze go nie masz, uruchom `sudo apt-get install wget` .
* Istniejąca **subskrypcja platformy Azure.** 
* Istniejące konto **magazynu ogólnego przeznaczenia do** przechowywania danych. Konta magazynu ogólnego przeznaczenia muszą obsługiwać magazyn tabel. Konto magazynu obiektów blob nie będzie działać.
* **Python 2.**

### <a name="python-requirement"></a>Wymaganie dotyczące języka Python

Rozszerzenie diagnostyczne systemu Linux wymaga języka Python 2. Jeśli maszyna wirtualna używa dystrybucji, która domyślnie nie zawiera języka Python 2, należy ją zainstalować. Następujące przykładowe polecenia instalują język Python 2 w różnych dystrybucjach:   

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- Suse: `zypper install -y python2`

Plik `python2` wykonywalny musi być aliasem dla języka *python.* Oto jedna z metod ustawienia tego aliasu:

1. Uruchom następujące polecenie, aby usunąć wszystkie istniejące aliasy.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Uruchom następujące polecenie, aby utworzyć alias.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Przykładowa instalacja

Przykładowa konfiguracja pobrana w poniższych przykładach zbiera zestaw standardowych danych i wysyła je do magazynu tabel. Adres URL przykładowej konfiguracji i jej zawartości może ulec zmianie. 

W większości przypadków należy pobrać kopię pliku JSON ustawień portalu i dostosować ją do swoich potrzeb. Następnie użyj szablonów lub własnej automatyzacji, aby użyć dostosowanej wersji pliku konfiguracji zamiast pobierania z adresu URL za każdym razem.

> [!NOTE]
> W poniższych przykładach przed uruchomieniem kodu wprowadź poprawne wartości zmiennych w pierwszej sekcji. 
#### <a name="azure-cli-sample"></a>Przykład interfejsu wiersza polecenia platformy Azure

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>Przykład interfejsu wiersza polecenia platformy Azure w celu zainstalowania wersji LAD 3.0 w wystąpieniu zestawu skalowania maszyn wirtualnych

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>Aktualizowanie ustawień rozszerzenia

Po zmianie ustawień chronionych lub publicznych wdychuj je na maszynie wirtualnej, uruchamiając to samo polecenie. Jeśli ustawienia zostały zmienione, aktualizacje są wysyłane do rozszerzenia. LAD ponownie ładuje konfigurację i uruchamia się ponownie.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migrowanie z poprzednich wersji rozszerzenia

Najnowsza wersja rozszerzenia to *4.0.* 

> [!IMPORTANT]
> To rozszerzenie wprowadza istotne zmiany w konfiguracji. Jedna z takich zmian poprawiła bezpieczeństwo rozszerzenia, dlatego nie można było zachować zgodności z poprzednimi wersjami w wersji 2.x. Ponadto wydawca rozszerzenia dla tego rozszerzenia różni się od wydawcy dla wersji 2.x.
>
> Aby przeprowadzić migrację z wersji 2.x do nowej wersji, najpierw odinstaluj stare rozszerzenie (pod starą nazwą wydawcy). Następnie zainstaluj wersję 3.

Zalecenia:

* Zainstaluj rozszerzenie z włączonym automatycznym uaktualnieniem wersji pomocniczej.
  * W przypadku maszyn wirtualnych klasycznego modelu wdrażania określ wersję, jeśli instalujesz rozszerzenie za pomocą interfejsu wiersza polecenia XPLAT platformy `3.*` Azure lub programu PowerShell.
  * Na Azure Resource Manager maszyn wirtualnych modelu wdrażania uwzględnij w `"autoUpgradeMinorVersion": true` szablonie wdrażania maszyny wirtualnej.
* Użyj nowego lub innego konta magazynu dla funkcji LAD 3.0. LAD 2.3 i LAD 3.0 mają kilka małych niezgodności, które sprawiają, że udostępnianie konta jest kłopotliwe:
  * LAD 3.0 przechowuje zdarzenia syslog w tabeli, która ma inną nazwę.
  * Ciągi `counterSpecifier` dla metryk różnią się w przypadku `builtin` LAD 3.0.

## <a name="protected-settings"></a>Ustawienia chronione

Ten zestaw informacji o konfiguracji zawiera poufne informacje, które powinny być chronione przed widokiem publicznym. Zawiera na przykład poświadczenia magazynu. Te ustawienia są przesyłane do rozszerzenia i przechowywane przez nie w postaci zaszyfrowanej.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Nazwa | Wartość
---- | -----
storageAccountName | Nazwa konta magazynu, w którym dane są zapisywane przez rozszerzenie.
storageAccountEndPoint | (Opcjonalnie) Punkt końcowy, który identyfikuje chmurę, w której znajduje się konto magazynu. Jeśli to ustawienie jest nieobecne, domyślnym ustawieniem LAD jest chmura publiczna Azure, `https://core.windows.net` . Aby użyć konta magazynu na platformie Azure w Niemczech, Azure Government lub Azure (Chiny) — 21Vianet, ustaw tę wartość zgodnie z potrzebami.
storageAccountSasToken | Token [sygnatury dostępu współdzielonego](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) konta dla usług obiektów blob i tabel ( `ss='bt'` ). Dotyczy to kontenerów i obiektów ( `srt='co'` ). Przyznaje uprawnienia do dodawania, tworzenia, tworzenia, tworzenia, aktualizowania i zapisu ( `sp='acluw'` ). Nie *dołączaj* wiodącego znaku zapytania (?).
mdsdHttpProxy | (Opcjonalnie) Informacje serwera proxy HTTP, których rozszerzenie potrzebuje do nawiązania połączenia z określonym kontem magazynu i punktem końcowym.
sinksConfig | (Opcjonalnie) Szczegóły alternatywnych miejsc docelowych, do których można dostarczać metryki i zdarzenia. Poniższe sekcje zawierają szczegółowe informacje o każdym ujściu danych obsługiwanym przez rozszerzenie.

Aby uzyskać token SAS w szablonie usługi ARM, użyj `listAccountSas` funkcji . Aby uzyskać przykładowy szablon, zobacz [Lista przykładowych funkcji](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Wymagany token SAS można utworzyć za pomocą Azure Portal:

1. Wybierz konto magazynu ogólnego przeznaczenia, na którym chcesz zapisać rozszerzenie.
1. W menu po lewej stronie w obszarze **Ustawienia** wybierz pozycję **Sygnatura dostępu współdzielona.**
1. Dokonaj wyboru zgodnie z wcześniejszym opisem.
1. Wybierz pozycję **Generuj sygnaturę dostępu współdzielonego.**

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Zrzut ekranu przedstawiający stronę Sygnatura dostępu współdzielona z przyciskiem Generuj S A S.":::

Skopiuj wygenerowaną sygnaturę dostępu współdzielonego do `storageAccountSasToken` pola . Usuń wiodący znak zapytania (?).

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Opcjonalna `sinksConfig` sekcja definiuje więcej miejsc docelowych, do których rozszerzenie wysyła informacje, które zbiera. Tablica `sink` zawiera obiekt dla każdego dodatkowego ujścia danych. Atrybut `type` określa inne atrybuty w obiekcie .

Element | Wartość
------- | -----
name | Ciąg, który odwołuje się do tego ujścia w innym miejscu w konfiguracji rozszerzenia.
typ | Definiowany typ ujścia. Określa inne wartości (jeśli są) w wystąpieniach tego typu.

LAD w wersji 3.0 obsługuje dwa typy ujścia: `EventHub` i `JsonBlob` .

#### <a name="eventhub-sink"></a>Ujścia eventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

Wpis `"sasURL"` zawiera pełny adres URL, w tym token SYGNATURY dostępu współdzielonego, dla centrum zdarzeń, w którym powinny zostać opublikowane dane. Funkcja LAD wymaga sygnatury dostępu współdzielonego, aby nazwać zasady, które umożliwiają wysłanie oświadczenia. 

Na przykład:

* Utwórz przestrzeń Azure Event Hubs nazw o nazwie `contosohub` .
* Utwórz centrum zdarzeń w przestrzeni nazw o nazwie `syslogmsgs` .
* Utwórz zasady dostępu współdzielone w centrum zdarzeń, które włącza oświadczenie wysyłania. Nadaj zasadom nazwę `writer` .

Jeśli sygnatura dostępu współdzielonego jest dobra do północy czasu UTC 1 stycznia 2018 r., wartość sygnatury dostępu współdzielonego może wyglądać podobnie do tego przykładu:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Aby uzyskać więcej informacji na temat generowania i pobierania informacji na temat tokenów SAS dla Event Hubs, zobacz [Generowanie tokenu SAS.](/rest/api/eventhub/generate-sas-token#powershell)

#### <a name="jsonblob-sink"></a>Ujścia JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Dane kierowane do `JsonBlob` ujścia są przechowywane w obiektach blob w usłudze Azure Storage. Każde wystąpienie LAD tworzy obiekt blob co godzinę dla każdej nazwy ujścia. Każdy obiekt blob zawsze zawiera prawidłową synaktywnie tablicę obiektów JSON. Nowe wpisy są niepodzielnie dodawane do tablicy. 

Obiekty blob są przechowywane w kontenerze o takiej samej nazwie jak ujścia. Reguły usługi Azure Storage dla nazw kontenerów obiektów blob mają zastosowanie do nazw `JsonBlob` ujść. Nazwa musi mieć od 3 do 63 małych alfanumerycznych znaków ASCII lub łączników.

## <a name="public-settings"></a>Ustawienia publiczne

Struktura ustawień publicznych zawiera różne bloki ustawień, które kontrolują informacje zbierane przez rozszerzenie. Każde ustawienie jest opcjonalne. W przypadku określenia `ladCfg` wartości należy również określić wartość `StorageAccount` .

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Element | Wartość
------- | -----
StorageAccount | Nazwa konta magazynu, w którym dane są zapisywane przez rozszerzenie. Musi to być nazwa określona w [chronionych ustawieniach](#protected-settings).
mdsdHttpProxy | (Opcjonalnie) Tak samo jak w [ustawieniach chronionych](#protected-settings). Wartość publiczna jest zastępowany przez wartość prywatną, jeśli jest ustawiona. Umieść ustawienia serwera proxy zawierające klucz tajny, taki jak hasło, w [chronionych ustawieniach](#protected-settings).

Poniższe sekcje zawierają szczegółowe informacje dotyczące pozostałych elementów.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Struktura `ladCfg` jest opcjonalna. Kontroluje zbieranie metryk i dzienników, które są dostarczane do usługi Azure Monitor Metrics i innych ujść danych. Należy określić:

* Albo `performanceCounters` `syslogEvents` lub oba. 
* `metrics`Struktura.

Element | Wartość
------- | -----
eventVolume | (Opcjonalnie) Steruje liczbą partycji utworzonych w tabeli magazynu. Musi to być `"Large"` , `"Medium"` lub `"Small"` . Jeśli wartość nie zostanie określona, wartością domyślną jest `"Medium"` .
sampleRateInSeconds | (Opcjonalnie) Domyślny interwał między kolekcją nieprzetworzonych (niezagregowanych) metryk. Najmniejsza obsługiwana szybkość próbkowania wynosi 15 sekund. Jeśli wartość nie zostanie określona, wartością domyślną jest `15` .

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Element | Wartość
------- | -----
resourceId | Identyfikator Azure Resource Manager maszyny wirtualnej lub zestawu skalowania, do którego należy maszyna wirtualna. To ustawienie należy również określić, jeśli w konfiguracji `JsonBlob` jest używane jakiekolwiek ujścia.
scheduledTransferPeriod | Częstotliwość, z jaką metryki agregowane są obliczane i przesyłane do Azure Monitor Metryki. Częstotliwość jest wyrażona jako interwał czasu IS 8601. Najmniejszy okres transferu wynosi 60 sekund, czyli PT1M. Określ co najmniej `scheduledTransferPeriod` jeden .

Próbki metryk określonych w sekcji są zbierane co 15 sekund lub z szybkością próbkowania `performanceCounters` jawnie zdefiniowaną dla licznika. Jeśli pojawi `scheduledTransferPeriod` się wiele częstotliwości, jak w przykładzie, każda agregacja jest obliczana niezależnie.

#### <a name="performancecounters"></a>Performancecounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Opcjonalna `performanceCounters` sekcja steruje kolekcją metryk. Próbki pierwotne są agregowane dla każdego z [`scheduledTransferPeriod`](#metrics) nich w celu uzyskania tych wartości:

* Średnia
* Minimum
* Maksimum
* Ostatnio zebrana wartość
* Liczba próbek pierwotnych używanych do obliczania agregacji

Element | Wartość
------- | -----
Zlewozmywaki | (Opcjonalnie) Rozdzielana przecinkami lista nazw ujść, do których LAD wysyła zagregowane wyniki metryk. Wszystkie zagregowane metryki są publikowane w każdym wymienionym ujściu. Przykład: `"EHsink1, myjsonsink"`. Aby uzyskać więcej informacji, zobacz [`sinksConfig`](#sinksconfig).
typ | Identyfikuje rzeczywistego dostawcę metryki.
class | Razem z `"counter"` programem identyfikuje określoną metrykę w przestrzeni nazw dostawcy.
counter | Razem z `"class"` programem identyfikuje określoną metrykę w przestrzeni nazw dostawcy.
counterSpecifier | Identyfikuje określoną metrykę w przestrzeni nazw Azure Monitor Metrics.
Warunek | (Opcjonalnie) Wybiera określone wystąpienie obiektu, do którego odnosi się metryka. Lub wybiera agregację we wszystkich wystąpieniach tego obiektu. 
sampleRate | Interwał IS 8601, który określa szybkość zbierania próbek pierwotnych dla tej metryki. Jeśli wartość nie jest ustawiona, interwał kolekcji jest ustawiany przez wartość [`sampleRateInSeconds`](#ladcfg) . Najkrótsza obsługiwana szybkość próbkowania to 15 sekund (PT15S).
unit | Definiuje jednostkę dla metryki. Powinien być jednym z tych ciągów: `"Count"` , , , , , , , `"Bytes"` `"Seconds"` `"Percent"` `"CountPerSecond"` `"BytesPerSecond"` `"Millisecond"` . Odbiorcy zebranych danych oczekują, że zebrane wartości danych będą zgodne z tą jednostką. LAD ignoruje to pole.
displayName | Etykieta, która ma zostać dołączona do danych w Azure Monitor Metryki. Ta etykieta jest w języku określonym przez skojarzone ustawienie ustawień regionalnych. LAD ignoruje to pole.

Jest `counterSpecifier` dowolnym identyfikatorem. Użytkownicy metryk, takich jak Azure Portal wykresy i alerty, używają jako "klucza", który identyfikuje metrykę lub `counterSpecifier` wystąpienie metryki. 

W `builtin` przypadku metryk zalecamy `counterSpecifier` wartości, które zaczynają się od `/builtin/` . Jeśli zbierasz określone wystąpienie metryki, zalecamy dołączenie identyfikatora wystąpienia do `counterSpecifier` wartości. 

Oto kilka przykładów:

* `/builtin/Processor/PercentIdleTime` - Średni czas bezczynności dla wszystkich procesorów wirtualnych
* `/builtin/Disk/FreeSpace(/mnt)` - Wolne miejsce w `/mnt` systemie plików
* `/builtin/Disk/FreeSpace` - Wolne miejsce uśrednione we wszystkich zainstalowanych systemach plików

LAD i Azure Portal nie oczekują, że `counterSpecifier` wartość będzie odpowiadać żadneowi wzorcowi. Spójnie konstruuj `counterSpecifier` wartości.

Po określeniu `performanceCounters` wartości LAD zawsze zapisuje dane w tabeli w usłudze Azure Storage. Te same dane można zapisywać w obiektach blob JSON, w Event Hubs lub obu tych danych. Nie można jednak wyłączyć przechowywania danych w tabeli. 

Wszystkie wystąpienia aplikacji LAD, które używają tej samej nazwy konta magazynu i punktu końcowego, dodają swoje metryki i dzienniki do tej samej tabeli. Jeśli zbyt wiele maszyn wirtualnych zapisuje się w tej samej partycji tabeli, platforma Azure może ograniczać zapis w tej partycji. 

To `eventVolume` ustawienie powoduje rozłożenie wpisów na 1 (małe), 10 (średnie) lub 100 (duże) partycje. Zwykle średnie partycje są wystarczające, aby uniknąć ograniczania ruchu. 

Funkcja Azure Monitor Metrics Azure Portal używa danych w tej tabeli do tworzenia grafów lub wyzwalania alertów. Nazwa tabeli jest połączenia tych ciągów:

* `WADMetrics`
* wartość `"scheduledTransferPeriod"` zagregowanych wartości przechowywanych w tabeli
* `P10DV2S`
* Data w postaci "RRRRMMDD", która zmienia się co 10 dni

Przykłady to `WADMetricsPT1HP10DV2S20170410` i `WADMetricsPT1MP10DV2S20170609` .

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Opcjonalna `syslogEvents` sekcja kontroluje zbieranie zdarzeń dziennika z dziennika systemowego. Jeśli sekcja zostanie pominięta, zdarzenia syslog nie są w ogóle przechwytywane.

Kolekcja `syslogEventConfiguration` ma jeden wpis dla każdej interesującej funkcji dziennika systemowego. Jeśli obiekt jest dla konkretnego obiektu lub jeśli w ogóle nie pojawia się w elemencie , nie są przechwytywane żadne zdarzenia z tego `minSeverity` `"NONE"` obiektu.

Element | Wartość
------- | -----
Zlewozmywaki | Rozdzielana przecinkami lista nazw ujść, do których są publikowane poszczególne zdarzenia dziennika. Wszystkie zdarzenia dziennika zgodne z ograniczeniami w `syslogEventConfiguration` programie są publikowane w każdym z wymienionych ujść. Przykład: `"EHforsyslog"`
facilityName | Nazwa obiektu syslog, taka jak `"LOG_USER"` lub `"LOG\LOCAL0"` . Aby uzyskać więcej informacji, zobacz sekcję "Facility" (Obiekt) na [stronie "syslog man".](http://man7.org/linux/man-pages/man3/syslog.3.html)
minSeverity (minimalna wartość) | Poziom ważności syslog, taki jak `"LOG_ERR"` lub `"LOG_INFO"` . Aby uzyskać więcej informacji, zobacz sekcję "Poziom" na [stronie syslog man](http://man7.org/linux/man-pages/man3/syslog.3.html). Rozszerzenie przechwytuje zdarzenia wysyłane do obiektu na określonym poziomie lub powyżej.

Po określeniu `syslogEvents` wartości LAD zawsze zapisuje dane w tabeli w usłudze Azure Storage. Te same dane można zapisywać w obiektach blob JSON, w Event Hubs lub obu tych danych. Nie można jednak wyłączyć przechowywania danych w tabeli. 

Zachowanie partycjonowania dla tabeli jest takie samo jak opisane dla `performanceCounters` . Nazwa tabeli jest czeniem tych ciągów:

* `LinuxSyslog`
* Data w postaci "RRRRMMDD", która zmienia się co 10 dni

Przykłady obejmują `LinuxSyslog20170410` i `LinuxSyslog20170609` .

### <a name="perfcfg"></a>perfCfg

Sekcja `perfCfg` jest opcjonalna. Steruje uruchamianiem dowolnych zapytań [open management infrastructure (OMI).](https://github.com/Microsoft/omi)

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Element | Wartość
------- | -----
namespace | (Opcjonalnie) Przestrzeń nazw OMI, w której powinno zostać uruchomione zapytanie. Jeśli nie zostanie on nieokreślony, wartość domyślna to `"root/scx"` . Jest on implementowany przez [System Center międzyplatformowych.](https://github.com/Microsoft/SCXcore)
query | Zapytanie OMI do uruchomienia.
tabela | (Opcjonalnie) Tabela usługi Azure Storage na wyznaczonym koncie magazynu. Aby uzyskać więcej informacji, zobacz [Chronione ustawienia](#protected-settings).
frequency | (Opcjonalnie) Liczba sekund między przebiegami zapytania. Wartość domyślna to 300 (5 minut). Minimalna wartość to 15 sekund.
Zlewozmywaki | (Opcjonalnie) Rozdzielana przecinkami lista nazw większej liczby ujść, w których powinny zostać opublikowane nieprzetworzone wyniki metryki przykładowej. Żadna agregacja tych próbek pierwotnych nie jest obliczana przez rozszerzenie lub Azure Monitor Metryki.

Należy `"table"` określić albo albo oba te `"sinks"` wartości.

### <a name="filelogs"></a>fileLogs

Sekcja `fileLogs` kontroluje przechwytywanie plików dziennika. LAD przechwytuje nowe wiersze tekstowe, gdy są zapisywane w pliku. Zapisuje je w wierszach tabeli i/lub dowolnych określonych ujściach ( `JsonBlob` lub `EventHub` ).

> [!NOTE]
> Są `fileLogs` przechwytywane przez podskładnik LAD o nazwie `omsagent` . Aby zebrać `fileLogs` plik , upewnij `omsagent` się, że użytkownik ma uprawnienia do odczytu dla plików, które określisz. Użytkownik musi również mieć uprawnienia do wykonywania we wszystkich katalogach w ścieżce do tego pliku. Po zainstalowaniu LAD, można sprawdzić uprawnienia, uruchamiając `sudo su omsagent -c 'cat /path/to/file'` .

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Element | Wartość
------- | -----
 — plik | Pełna nazwa ścieżki pliku dziennika, który ma być obserwowany i przechwytywany. Nazwa ścieżki musi mieć nazwę pojedynczego pliku. Nie może on nazywać katalogu ani zawierać symboli wieloznacznych. Konto `omsagent` użytkownika musi mieć dostęp do odczytu do ścieżki pliku.
tabela | (Opcjonalnie) Tabela usługi Azure Storage, w której są zapisywane nowe wiersze z "tail" pliku. Tabela musi znajdować się na wyznaczonym koncie magazynu określonym w chronionej konfiguracji. 
Zlewozmywaki | (Opcjonalnie) Rozdzielana przecinkami lista nazw większej liczby ujść, do których są wysyłane wiersze dziennika.

Należy `"table"` określić albo , albo oba te `"sinks"` wartości.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metryki obsługiwane przez wbudowanego dostawcę

Dostawca metryk jest źródłem metryk, które są najbardziej interesujące `builtin` dla szerokiego zestawu użytkowników. Te metryki można ujednać w pięciu szerokich klasach:

* Procesor
* Pamięć
* Sieć
* System plików
* Dysk

### <a name="builtin-metrics-for-the-processor-class"></a>builtin metrics for the Processor class (Metryki wbudowane dla klasy procesora)

Klasa procesora metryk zawiera informacje o użyciu procesora na maszynie wirtualnej. Po zagregowaniu wartości procentowych wynikiem jest średnia dla wszystkich procesorów CPU. 

Jeśli na maszynie wirtualnej z dwoma procesorami wirtualnym jeden procesor wirtualny jest w 100% zajęty, a drugi jest w 100% bezczynny, zgłaszany jest `PercentIdleTime` 50. Jeśli każdy procesor vCPU jest zajęty przez 50 procent w tym samym okresie, zgłoszony wynik wynosi również 50. W przypadku maszyny wirtualnej z czterema procesorami wirtualnmi, gdy jeden procesor wirtualny jest w 100% zajęty, a pozostałe są bezczynne, raport `PercentIdleTime` wynosi 75.

Licznik | Znaczenie
------- | -------
PercentIdleTime | Procent czasu w oknie agregacji, przez który procesory uruchomiły pętlę bezczynności jądra
PercentProcessorTime | Procent czasu uruchamiania wątku bezczynnych
PercentIOWaitTime | Procent czasu oczekiwania na zakończenie operacji we/wy
PercentInterruptTime | Procent czasu działania przerwań sprzętowych lub programowych oraz dpc (odroczone wywołania procedur)
PercentUserTime | Czas braku bezczynności w oknie agregacji, procent czasu spędzonego w trybie użytkownika przy normalnym priorytecie
Percent Wylicz czas | W przypadku czasu bezczynności wartość procentowa wydatków na niższym (dobrze) priorytecie
PercentPrivilegedTime | W przypadku czasu bezczynności wartość procentowa wydatków w trybie uprzywilejowanym (jądro)

Pierwsze cztery liczniki powinny sumować się do 100 procent. Trzy ostatnie liczniki również sumuje się do 100 procent. Te trzy liczniki ujednoliają sumę `PercentProcessorTime` wartości `PercentIOWaitTime` , i `PercentInterruptTime` .

Aby agregować pojedynczą metrykę dla wszystkich procesorów, ustaw wartość `"condition": "IsAggregate=TRUE"` . Aby uzyskać metrykę dla określonego procesora, takiego jak drugi procesor logiczny maszyny wirtualnej z czterema procesorami wirtualnymi, ustaw parametr `"condition": "Name=\\"1\\""` . Liczby procesorów logicznych znajdują się w zakresie `[0..n-1]` .

### <a name="builtin-metrics-for-the-memory-class"></a>wbudowane metryki dla klasy Memory

Klasa Memory metryk zawiera informacje dotyczące użycia pamięci, stronicowania i zamiany.

Licznik | Znaczenie
------- | -------
AvailableMemory | Dostępna pamięć fizyczna w programie MiB
PercentAvailableMemory | Dostępna pamięć fizyczna jako procent całkowitej pamięci
UsedMemory | Pamięć fizyczna w użyciu (MiB)
PercentUsedMemory | Użycie pamięci fizycznej jako procent całkowitej ilości pamięci
PagesPerSec | Łączna liczba stronicowania (odczyt/zapis)
PagesReadPerSec | Strony odczytane z magazynu zapasowego, takie jak plik wymiany, plik programu i zamapowany plik
PagesWrittenPerSec | Strony zapisane w magazynie kopii zapasowej, takie jak plik wymiany i zamapowany plik
AvailableSwap | Nieużywane miejsce wymiany (MiB)
PercentAvailableSwap | Nieużywana przestrzeń wymiany jako wartość procentowa całkowitej wymiany
UsedSwap | Przestrzeń wymiany w użyciu (MiB)
PercentUsedSwap | Przestrzeń wymiany w użyciu jako procent całkowitej zamiany

Ta klasa metryk ma tylko jedno wystąpienie. Atrybut `"condition"` nie ma przydatnych ustawień i należy go pominąć.

### <a name="builtin-metrics-for-the-network-class"></a>metryki wbudowane dla klasy Network

Klasa sieci metryk zawiera informacje o aktywności sieci w poszczególnych interfejsach sieciowych od momentu uruchomienia. 

LAD nie uwidacznia metryk przepustowości. Te metryki można uzyskać z metryk hosta.

Licznik | Znaczenie
------- | -------
BytesTransmitted | Całkowita liczba bajtów wysłanych od uruchomienia
Bytesreceived | Całkowita liczba bajtów odebranych od uruchomienia
BytesTotal | Całkowita liczba bajtów wysłanych lub odebranych od uruchomienia
PacketsTransmitted | Łączna liczba pakietów wysłanych od uruchomienia
PacketsReceived | Łączna liczba pakietów odebranych od uruchomienia
TotalRxErrors | Liczba błędów odbierania od czasu uruchomienia
TotalTxErrors | Liczba błędów przesyłania od momentu uruchomienia
TotalCollisions | Liczba kolizji zgłoszonych przez porty sieciowe od momentu uruchomienia

Mimo że klasa Network jest wystąpieniem, LAD nie obsługuje przechwytywania metryk sieci zagregowanych na wszystkich urządzeniach sieciowych. Aby uzyskać metryki dla określonego interfejsu, takiego jak eth0, ustaw wartość `"condition": "InstanceID=\\"eth0\\""` .

### <a name="builtin-metrics-for-the-file-system-class"></a>metryki wbudowane dla klasy system plików

Klasa systemu plików metryk zawiera informacje o użyciu systemu plików. Wartości bezwzględne i procentowe są zgłaszane w sposób, w jaki byłyby wyświetlane dla zwykłego użytkownika (nie dla użytkownika głównego).

Licznik | Znaczenie
------- | -------
FreeSpace | Dostępne miejsce na dysku w bajtach
UsedSpace (Przestrzeń używana) | Używane miejsce na dysku w bajtach
PercentFreeSpace | Procentowe wolne miejsce
PercentUsedSpace | Procent użytego miejsca
PercentFreeInodes | Procent nieużywanych węzłów indeksu (i-węzły)
PercentUsedInodes | Procent przydzielonych i-węzłów (w użyciu) zsumowanych we wszystkich systemach plików
BytesReadPerSecond | Liczba bajtów odczytanych na sekundę
BytesWrittenPerSecond | Liczba bajtów zapisywanych na sekundę
BajtyPerSecond | Liczba bajtów odczytanych lub zapisywanych na sekundę
ReadsPerSecond | Operacje odczytu na sekundę
WritesPerSecond | Operacje zapisu na sekundę
TransferPerSecond | Operacje odczytu lub zapisu na sekundę

Zagregowane wartości można uzyskać we wszystkich systemach plików, ustawiając wartość `"condition": "IsAggregate=True"` . Pobierz wartości dla określonego zainstalowanego systemu plików, takiego jak `"/mnt"` , przez ustawienie `"condition": 'Name="/mnt"'` . 

> [!NOTE]
> Jeśli pracujesz w edycie Azure Portal JSON, formularz pola warunku to `Name='/mnt'` .

### <a name="builtin-metrics-for-the-disk-class"></a>wbudowane metryki dla klasy Dysk

Klasa Disk metryk zawiera informacje o użyciu urządzenia dysku. Te statystyki dotyczą całego dysku. 

Gdy urządzenie ma wiele systemów plików, liczniki dla tego urządzenia są w praktyce agregowane we wszystkich systemach plików.

Licznik | Znaczenie
------- | -------
ReadsPerSecond | Operacje odczytu na sekundę
WritesPerSecond | Operacje zapisu na sekundę
TransferPerSecond | Łączna liczba operacji na sekundę
AverageReadTime | Średnia liczba sekund na operację odczytu
AverageWriteTime | Średnia liczba sekund na operację zapisu
AverageTransferTime | Średnia liczba sekund na operację
AverageDiskQueueLength | Średnia liczba operacji na dysku w kolejce
ReadBytesPerSecond | Liczba bajtów odczytanych na sekundę
WriteBytesPerSecond | Liczba bajtów zapisywanych na sekundę
BytesPerSecond | Liczba bajtów odczytanych lub zapisywanych na sekundę

Zagregowane wartości na wszystkich dyskach można uzyskać, ustawiając wartość `"condition": "IsAggregate=True"` . Aby uzyskać informacje dotyczące określonego urządzenia (na przykład `/dev/sdf1` ), ustaw wartość `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="install-and-configure-lad-30"></a>Instalowanie i konfigurowanie pakietu LAD 3.0

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Jeśli chronione ustawienia znajdują się w pliku *ProtectedSettings.js,* a informacje o konfiguracji publicznej znajdują sięPublicSettings.js *na* komputerze , uruchom następujące polecenie.

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Polecenie zakłada, że używasz trybu Azure Resource Manager wiersza polecenia platformy Azure. Aby skonfigurować LAD dla maszyn wirtualnych klasycznego modelu wdrażania, przełącz się do trybu "asm" ( ) i pomiń nazwę grupy zasobów `azure config mode asm` w poleceniu . 

Aby uzyskać więcej informacji, zobacz dokumentację [międzyplatformowego interfejsu wiersza polecenia](/cli/azure/authenticate-azure-cli).

### <a name="powershell"></a>PowerShell

Jeśli chronione ustawienia znajdują się w zmiennej , a informacje o konfiguracji publicznej znajdują się w zmiennej `$protectedSettings` `$publicSettings` , uruchom to polecenie:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>Przykładowa konfiguracja LAD 3.0

Na podstawie powyższych definicji ta sekcja zawiera przykładową konfigurację rozszerzenia LAD 3.0 i pewne wyjaśnienia. Aby zastosować ten przykład do twojego przypadku, użyj własnej nazwy konta magazynu, tokenu SAS konta i Event Hubs tokenów SAS.

> [!NOTE]
> W zależności od tego, czy używasz interfejsu wiersza polecenia platformy Azure, czy programu PowerShell do instalowania pakietu LAD, metoda dostarczania ustawień publicznych i chronionych różni się: 
>
> * Jeśli używasz interfejsu wiersza polecenia platformy Azure, zapisz następujące ustawienia, abyProtectedSettings.js *iPublicSettings.js,* aby użyć poprzedniego przykładowego polecenia.  
> * Jeśli używasz programu PowerShell, zapisz następujące ustawienia w plikach `$protectedSettings` i `$publicSettings` , uruchamiając plik `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Ustawienia chronione

Chronione ustawienia konfigurują:

* Konto magazynu.
* Zgodny token SAS konta.
* Kilka ujść ( `JsonBlob` lub `EventHub` z tokenami SAS).

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Ustawienia publiczne

Ustawienia publiczne powodują, że LAD:

* Przekaż metryki procentu czasu procesora i metryki używanego miejsca na dysku do `WADMetrics*` tabeli.
* Przekazywanie komunikatów ze funkcji i ważności syslog `"user"` `"info"` do `LinuxSyslog*` tabeli.
* Przekaż nieprzetworzone wyniki zapytania OMI ( `PercentProcessorTime` i `PercentIdleTime` ) do nazwanej `LinuxCPU` tabeli.
* Przekaż dołączone wiersze w pliku `/var/log/myladtestlog` do `MyLadTestLog` tabeli.

W każdym przypadku dane są również przekazywane do:

* Azure Blob Storage. Nazwa kontenera jest zdefiniowana w `JsonBlob` ujściu.
* Punkt Event Hubs, jak określono w `EventHub` ujściu.

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

Wartość `resourceId` w konfiguracji musi być taka, jak w przypadku maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.

* Wykresy metryk platformy Azure i alerty zna informacje o maszynie wirtualnej, nad której `resourceId` pracujesz. Oczekuje ona znalezienia danych dla maszyny wirtualnej przy użyciu `resourceId` klucza wyszukiwania.
* Jeśli używasz usługi Azure Autoscale, w konfiguracji autoskalowania musi być ona dopasowana do używanej `resourceId` `resourceId` przez funkcję LAD.
* Obiekt `resourceId` jest wbudowany w nazwy obiektów blob JSON napisanych przez LAD.

## <a name="view-your-data"></a>Wyświetlanie danych

Użyj Azure Portal, aby wyświetlić dane wydajności lub ustawić alerty:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Zrzut ekranu przedstawia Azure Portal. Wybrana jest opcja Używane miejsce na dysku w metryce. Zostanie pokazany wynikowy wykres.":::

Dane `performanceCounters` są zawsze przechowywane w tabeli usługi Azure Storage. Interfejsy API usługi Azure Storage są dostępne dla wielu języków i platform.

Dane wysyłane do `JsonBlob` ujść są przechowywane w obiektach blob na koncie magazynu o nazwie w [chronionych ustawieniach](#protected-settings). Dane obiektów blob można używać przy użyciu dowolnych interfejsów API Azure Blob Storage.

Możesz również użyć tych narzędzi interfejsu użytkownika, aby uzyskać dostęp do danych w usłudze Azure Storage:

* Visual Studio Eksplorator serwera
* [Eksplorator usługi Azure Storage](https://azurestorageexplorer.codeplex.com/)

Poniższy zrzut ekranu przedstawiający sesję Eksplorator usługi Azure Storage usługi Azure Storage z poprawnie skonfigurowanego rozszerzenia LAD 3.0 na testowej maszynie wirtualnej. Obraz nie jest dokładnie taki sam jak [przykładowa konfiguracja LAD 3.0.](#example-lad-30-configuration)

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Zrzut ekranu przedstawia Eksplorator usługi Azure Storage.":::


Aby uzyskać więcej informacji o tym, jak korzystać z komunikatów opublikowanych w Event Hubs końcowym, zobacz odpowiednią [dokumentację Event Hubs .](../../event-hubs/event-hubs-about.md)

## <a name="next-steps"></a>Następne kroki

* W [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md)utwórz alerty dotyczące zbieranych metryk.
* Tworzenie [wykresów monitorowania](../../azure-monitor/data-platform.md) dla metryk.
* [Utwórz zestaw skalowania maszyn wirtualnych](../linux/tutorial-create-vmss.md) przy użyciu metryk do kontrolowania skalowania automatycznego.
