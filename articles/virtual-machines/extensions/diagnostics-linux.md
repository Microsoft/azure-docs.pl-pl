---
title: Azure COMPUTE — rozszerzenie diagnostyczne systemu Linux
description: Jak skonfigurować rozszerzenie diagnostyczne systemu Linux (LAD) do zbierania metryk i zdarzeń dzienników z maszyn wirtualnych systemu Linux działających na platformie Azure.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 0ae6366acf270d762b1c15563bfec1b2eb2a1b8d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421077"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników

W tym dokumencie opisano wersję 3,0 i nowszą rozszerzenia diagnostycznego systemu Linux.

> [!IMPORTANT]
> Aby uzyskać informacje o wersji 2,3 i starszej, zobacz [ten dokument](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Wprowadzenie

Rozszerzenie diagnostyczne systemu Linux pomaga użytkownikowi monitorować kondycję maszyny wirtualnej z systemem Linux działającą w Microsoft Azure. Dostępne są następujące możliwości:

* Zbiera metryki wydajności systemu z maszyny wirtualnej i zapisuje je w określonej tabeli na wydzielonym koncie magazynu.
* Pobiera zdarzenia dzienników z dziennika systemowego i zapisuje je w określonej tabeli na wskazanym koncie magazynu.
* Umożliwia użytkownikom dostosowywanie zbieranych i przekazywanych metryk danych.
* Umożliwia użytkownikom Dostosowywanie obiektów dziennika systemu i poziomów ważności zdarzeń zbieranych i przekazywanych.
* Umożliwia użytkownikom przekazywanie określonych plików dziennika do wskazanej tabeli magazynu.
* Obsługuje wysyłanie metryk i zdarzeń dzienników do dowolnych punktów końcowych centrów danych i obiektów BLOB w formacie JSON na wskazanym koncie magazynu.

To rozszerzenie współpracuje z modelami wdrażania platformy Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalowanie rozszerzenia na maszynie wirtualnej

To rozszerzenie można włączyć za pomocą poleceń cmdlet Azure PowerShell, skryptów interfejsu wiersza polecenia platformy Azure, szablonów ARM lub Azure Portal. Aby uzyskać więcej informacji, zobacz [rozszerzenia funkcji](features-linux.md).

>[!NOTE]
>Niektóre składniki rozszerzenia maszyny wirtualnej diagnostyki są również dostarczane przy użyciu [rozszerzenia maszyny wirtualnej log Analytics](./oms-linux.md). Ze względu na tę architekturę mogą wystąpić konflikty w przypadku wystąpienia obu rozszerzeń w tym samym szablonie usługi ARM. Aby uniknąć tych konfliktów w czasie instalacji, należy użyć [ `dependsOn` dyrektywy](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) , aby upewnić się, że rozszerzenia są zainstalowane sekwencyjnie. Rozszerzenia można zainstalować w dowolnej kolejności.

Te instrukcje dotyczące instalacji i [pobieranie przykładowej konfiguracji](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) konfigurowania lad 3,0 do:

* Przechwyć i Zapisz te same metryki, które zostały dostarczone przez LAD 2,3;
* Przechwyć przydatny zestaw metryk systemu plików, nowy do LAD 3,0;
* Przechwyć domyślną kolekcję dziennika systemowego włączoną przez LAD 2,3;
* Włącz Azure Portal środowisko dla wykresów i alertów dotyczących metryk maszyny wirtualnej.

Konfiguracja do pobrania jest tylko przykładem; Zmodyfikuj go, aby odpowiadał Twoim potrzebom.

### <a name="supported-linux-distributions"></a>Obsługiwane dystrybucje systemu Linux

Rozszerzenie diagnostyczne systemu Linux obsługuje następujące dystrybucje i wersje. Lista dystrybucji i wersji ma zastosowanie tylko do obrazów dostawcy systemu Linux z zatwierdzona przez platformę Azure. Obrazy BYOL i BYOS innych firm, takie jak urządzenia, zazwyczaj nie są obsługiwane dla rozszerzenia diagnostycznego systemu Linux.

Dystrybucja, która wyświetla tylko wersje główne, na przykład Debian 7, jest również obsługiwana dla wszystkich wersji pomocniczych. Jeśli określono określoną wersję pomocniczą, obsługiwana jest tylko określona wersja; Jeśli zostanie dołączony znak "+", obsługiwane są wersje pomocnicze, które są równe lub większe niż określona wersja.

Obsługiwane dystrybucje i wersje:

- Ubuntu 18,04, 16,04, 14,04
- CentOS 7, 6.5 +
- Oracle Linux 7, 6.4 +
- OpenSUSE 13.1 +
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- RHEL 7, 6.7 +

### <a name="prerequisites"></a>Wymagania wstępne

* **Agent systemu Linux w wersji 2.2.0 lub nowszej**. Większość obrazów z galerii maszyn wirtualnych systemu Linux platformy Azure obejmuje wersję 2.2.7 lub nowszą. Uruchom `/usr/sbin/waagent -version` w celu potwierdzenia wersji zainstalowanej na maszynie wirtualnej. Jeśli na maszynie wirtualnej jest uruchomiona Starsza wersja agenta gościa, postępuj zgodnie z [tymi instrukcjami](./update-linux-agent.md) , aby je zaktualizować.
* **Interfejs wiersza polecenia platformy Azure**. [Skonfiguruj środowisko interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) na komputerze.
* Wget polecenie, jeśli nie jest jeszcze: Uruchom `sudo apt-get install wget` .
* Istniejąca subskrypcja platformy Azure i istniejące konto magazynu ogólnego przeznaczenia, w którym są przechowywane dane.  Konta magazynu ogólnego przeznaczenia obsługują magazyn tabel, który jest wymagany.  Konto magazynu obiektów BLOB nie będzie działało.
* Python 2

### <a name="python-requirement"></a>Wymagania dotyczące języka Python

Rozszerzenie diagnostyczne systemu Linux wymaga języka Python 2. Jeśli maszyna wirtualna używa dystrybucji, która domyślnie nie zawiera języka Python 2, należy ją zainstalować. Następujące przykładowe polecenia zainstalują środowisko Python 2 na różnych dystrybucje.    

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SZŁO `zypper install -y python2`

Plik wykonywalny python2 musi mieć alias do języka *Python*. Oto jedna z metod, których można użyć do ustawienia tego aliasu:

1. Uruchom następujące polecenie, aby usunąć istniejące aliasy.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Uruchom następujące polecenie, aby utworzyć alias.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Przykładowa instalacja

> [!NOTE]
> Dla którejkolwiek z przykładów Wprowadź poprawne wartości zmiennych w pierwszej sekcji przed uruchomieniem. 

Przykładowa konfiguracja pobrana w tych przykładach zbiera zestaw danych standardowych i wysyła je do magazynu tabel. Adres URL dla przykładowej konfiguracji i jej zawartości może ulec zmianie. W większości przypadków należy pobrać kopię pliku JSON ustawień portalu i dostosować ją do własnych potrzeb, a następnie utworzyć wszystkie szablony i Automatyzacja, korzystając z własnej wersji pliku konfiguracji, zamiast pobierać ten adres URL za każdym razem.

#### <a name="azure-cli-sample"></a>Przykład interfejsu wiersza polecenia platformy Azure

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-30-extension-on-the-vmss-instance"></a>Przykład interfejsu wiersza polecenia platformy Azure służący do instalowania rozszerzenia LAD 3,0 w wystąpieniu VMSS

```azurecli
#Set your Azure VMSS diagnostic variables correctly below
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VMSS resource ID. Replace storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
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

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>Aktualizowanie ustawień rozszerzenia

Po zmianie ustawień chronionych lub publicznych należy wdrożyć je na maszynie wirtualnej, uruchamiając to samo polecenie. W przypadku zmiany ustawień zaktualizowane ustawienia są wysyłane do rozszerzenia. LAD ponownie ładuje konfigurację i ponownie uruchamia się.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migracja z poprzednich wersji rozszerzenia

Najnowsza wersja rozszerzenia to **3,0**. **Wszystkie stare wersje (2. x) są przestarzałe i mogą być cofnięte w dniu 31 lipca 2018**.

> [!IMPORTANT]
> To rozszerzenie wprowadza istotne zmiany w konfiguracji rozszerzenia. Wprowadzono taką zmianę w celu poprawy bezpieczeństwa rozszerzenia; w związku z tym nie można utrzymywać zgodności z poprzednimi wersjami z 2. x. Ponadto Wydawca rozszerzenia dla tego rozszerzenia różni się od wydawcy dla wersji 2. x.
>
> Aby przeprowadzić migrację z 2. x do tej nowej wersji rozszerzenia, należy odinstalować stare rozszerzenie (pod nazwą starego wydawcy), a następnie zainstalować wersję 3 rozszerzenia.

Zalecenia:

* Zainstaluj rozszerzenie z włączoną funkcją automatycznego uaktualniania wersji pomocniczej.
  * W przypadku maszyn wirtualnych z klasycznym modelem wdrażania należy określić wartość "3. *" jako wersję, jeśli rozszerzenie jest instalowane za pośrednictwem interfejsu wiersza polecenia platformy Azure XPLAT lub programu PowerShell.
  * Na maszynach wirtualnych z modelem wdrażania Azure Resource Manager należy uwzględnić w szablonie wdrożenia maszyny wirtualnej "" włączoną flagą autoupgrademinorversion ": true".
* Użyj nowego/innego konta magazynu dla LAD 3,0. Istnieje kilka małych niezgodności między LAD 2,3 i LAD 3,0, które udostępniają konto problematycznych:
  * LAD 3,0 przechowuje zdarzenia dziennika systemowego w tabeli o innej nazwie.
  * CounterSpecifier ciągi dla `builtin` metryk różnią się w LAD 3,0.

## <a name="protected-settings"></a>Ustawienia chronione

Ten zestaw informacji o konfiguracji zawiera poufne informacje, które powinny być chronione przed widokiem publicznym, na przykład poświadczenia magazynu. Te ustawienia są przesyłane do i przechowywane przez rozszerzenie w postaci zaszyfrowanej.

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
storageAccountEndPoint | obowiązkowe Punkt końcowy identyfikujący chmurę, w której znajduje się konto magazynu. W przypadku braku tego ustawienia LAD domyślnie chmurę publiczną platformy Azure `https://core.windows.net` . Aby użyć konta magazynu na platformie Azure (Niemcy), Azure Government lub Chiny platformy Azure, ustaw tę wartość odpowiednio.
storageAccountSasToken | [Token sygnatury dostępu współdzielonego konta](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) dla usług BLOB i Table Services ( `ss='bt'` ), który ma zastosowanie do kontenerów i obiektów ( `srt='co'` ), które przyznają uprawnienia do dodawania, tworzenia, wyświetlania, aktualizacji i zapisu ( `sp='acluw'` ). *Nie* dołączaj wiodącego znaku zapytania (?).
mdsdHttpProxy | obowiązkowe Informacje serwera proxy HTTP, które są konieczne, aby umożliwić rozszerzeniu łączenie się z określonym kontem magazynu i punktem końcowym.
sinksConfig | obowiązkowe Szczegóły alternatywnych miejsc docelowych, w których można dostarczyć metryki i zdarzenia. Szczegółowe informacje dotyczące każdego ujścia danych obsługiwanego przez rozszerzenie znajdują się w poniższych sekcjach.

Aby uzyskać token sygnatury dostępu współdzielonego w ramach szablonu Menedżer zasobów, użyj funkcji **listAccountSas** . Aby zapoznać się z przykładowym szablonem, zobacz [przykład funkcji list](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Możesz łatwo skonstruować wymagany token sygnatury dostępu współdzielonego za pomocą Azure Portal.

1. Wybierz konto magazynu ogólnego przeznaczenia, do którego ma zostać zapisane rozszerzenie
1. Wybierz pozycję "sygnatura dostępu współdzielonego" z części ustawień menu po lewej stronie.
1. Wprowadź odpowiednie sekcje zgodnie z opisem wcześniej
1. Kliknij przycisk Generuj sygnaturę dostępu współdzielonego.

![Zrzut ekranu przedstawia stronę sygnatura dostępu współdzielonego z opcją Generuj S A S.](./media/diagnostics-linux/make_sas.png)

Skopiuj wygenerowany sygnaturę dostępu współdzielonego do pola storageAccountSasToken; Usuń wiodący znak zapytania ("?").

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

W tej opcjonalnej sekcji zdefiniowano dodatkowe miejsca docelowe, do których rozszerzenie wysyła zbierane informacje. Tablica "ujścia" zawiera obiekt dla każdego dodatkowego ujścia danych. Atrybut "Type" określa inne atrybuty w obiekcie.

Element | Wartość
------- | -----
name | Ciąg używany do odwoływania się do tego ujścia w innym miejscu konfiguracji rozszerzenia.
typ | Typ zdefiniowanego ujścia. Określa pozostałe wartości (jeśli istnieją) w wystąpieniach tego typu.

Wersja 3,0 rozszerzenia diagnostycznego systemu Linux obsługuje dwa typy ujścia: EventHub i JsonBlob.

#### <a name="the-eventhub-sink"></a>Ujścia EventHub

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

Wpis "adresie sasurl" zawiera pełny adres URL, łącznie z tokenem sygnatury dostępu współdzielonego, dla centrum zdarzeń, do którego powinny zostać opublikowane dane. LAD wymaga nazewnictwa sygnatury dostępu współdzielonego, które umożliwia wysłanie żądania. Przykład:

* Utwórz Event Hubsą przestrzeń nazw o nazwie `contosohub`
* Utwórz centrum zdarzeń w przestrzeni nazw o nazwie `syslogmsgs`
* Tworzenie zasad dostępu współdzielonego w centrum zdarzeń o nazwie `writer` , które umożliwia wysyłanie żądania

Jeśli utworzono sygnaturę dostępu współdzielonego do północy czasu UTC 1 stycznia 2018, wartość adresie sasurl może być:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Aby uzyskać więcej informacji o generowaniu i pobieraniu informacji o tokenach sygnatury dostępu współdzielonego dla Event Hubs, zobacz [Tę stronę sieci Web](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="the-jsonblob-sink"></a>Ujścia JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Dane kierowane do ujścia JsonBlob są przechowywane w obiektach Blob w usłudze Azure Storage. Każde wystąpienie elementu LAD tworzy obiekt BLOB co godzinę dla każdej nazwy ujścia. Każdy obiekt BLOB zawsze zawiera składniową tablicę JSON obiektu. Nowe wpisy są dodawane niepodzielnie do tablicy. Obiekty blob są przechowywane w kontenerze o takiej samej nazwie jak ujścia. Reguły usługi Azure Storage dla nazw kontenerów obiektów BLOB mają zastosowanie do nazw ujścia JsonBlob: od 3 do 63 znaków alfanumerycznych lub kresek ASCII o niższym poziomie.

## <a name="public-settings"></a>Ustawienia publiczne

Ta struktura zawiera różne bloki ustawień kontrolujących informacje zbierane przez rozszerzenie. Każde ustawienie jest opcjonalne. Jeśli określisz `ladCfg` , należy również określić `StorageAccount` .

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
StorageAccount | Nazwa konta magazynu, w którym dane są zapisywane przez rozszerzenie. Musi być taka sama jak nazwa określona w [ustawieniach chronionych](#protected-settings).
mdsdHttpProxy | obowiązkowe Analogicznie jak w [ustawieniach chronionych](#protected-settings). Wartość publiczna jest zastępowana przez wartość prywatną, jeśli jest ustawiona. Umieść ustawienia serwera proxy zawierające klucz tajny, taki jak hasło, w [ustawieniach chronionych](#protected-settings).

Pozostałe elementy są szczegółowo opisane w poniższych sekcjach.

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

Ta opcjonalna struktura kontroluje zbieranie metryk i dzienników na potrzeby dostarczania do usługi Azure Metrics oraz do innych obiektów ujścia danych. Należy określić jedną `performanceCounters` lub `syslogEvents` obie. Należy określić `metrics` strukturę.

Element | Wartość
------- | -----
eventVolume | obowiązkowe Określa liczbę partycji utworzonych w tabeli magazynu. Musi być jednym z `"Large"` , `"Medium"` lub `"Small"` . Jeśli nie zostanie określony, wartość domyślna to `"Medium"` .
sampleRateInSeconds | obowiązkowe Domyślny interwał między kolekcją nieprzetworzonych metryk (niezagregowanych). Najmniejszy obsługiwany częstotliwość próbkowania wynosi 15 sekund. Jeśli nie zostanie określony, wartość domyślna to `15` .

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
resourceId | Identyfikator zasobu Azure Resource Manager maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych, do którego należy maszyna wirtualna. To ustawienie należy również określić, jeśli w konfiguracji jest używany dowolny ujścia JsonBlob.
scheduledTransferPeriod | Częstotliwość, z jaką zagregowane metryki mają być obliczane i przekazywane do metryk platformy Azure, wyrażona jako przedział czasu 8601. Najmniejszy okres transferu to 60 sekund, czyli PT1M. Należy określić co najmniej jedną scheduledTransferPeriod.

Próbki metryk określonych w sekcji liczniki wydajności są zbierane co 15 sekund lub według częstotliwości próbkowania jawnie zdefiniowanej dla licznika. Jeśli zostanie wyświetlona wiele częstotliwości scheduledTransferPeriod (jak w przykładzie), każda agregacja jest obliczana niezależnie.

#### <a name="performancecounters"></a>Liczniki wydajności

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

Ta opcjonalna sekcja steruje kolekcją metryk. Próbki pierwotne są agregowane dla każdego [scheduledTransferPeriodu](#metrics) w celu utworzenia następujących wartości:

* średnia
* minimum
* maksimum
* Ostatnia zebrana wartość
* Liczba nieprzetworzonych próbek użytych do obliczenia agregacji

Element | Wartość
------- | -----
ujścia | obowiązkowe Rozdzielana przecinkami lista nazw zlewów, do których LAD wysyła zagregowane wyniki metryk. Wszystkie zagregowane metryki są publikowane w każdym z wymienionych zbiorników. Zobacz [sinksConfig](#sinksconfig). Przykład: `"EHsink1, myjsonsink"`.
typ | Identyfikuje rzeczywistego dostawcę metryki.
class | Wraz z "licznik" identyfikuje konkretną metrykę w przestrzeni nazw dostawcy.
counter | Wraz z "klasą" identyfikuje konkretną metrykę w przestrzeni nazw dostawcy.
counterSpecifier | Identyfikuje konkretną metrykę w przestrzeni nazw metryk platformy Azure.
rozgrzewa | obowiązkowe Wybiera określone wystąpienie obiektu, do którego jest stosowana Metryka, lub wybiera agregację we wszystkich wystąpieniach tego obiektu. Aby uzyskać więcej informacji, zobacz `builtin` definicje metryk.
sampleRate | JEST 8601 interwał, który ustawia szybkość, z jaką zbierane są nieprzetworzone próbki dla tej metryki. Jeśli nie zostanie ustawiona, interwał kolekcji jest ustawiany przez wartość [sampleRateInSeconds](#ladcfg). Najkrótsza obsługiwana częstotliwość próbkowania wynosi 15 sekund (PT15S).
unit | Powinien być jednym z następujących ciągów: "Count", "bajtów", "sek", "PERCENT", "CountPerSecond", "BytesPerSecond", "milisekundy". Definiuje jednostkę dla metryki. Odbiorcy zebranych danych oczekują wartości zebranych danych w celu dopasowania do tej jednostki. LAD ignoruje to pole.
displayName | Etykieta (w języku określonym przez skojarzone ustawienie regionalne), która ma zostać dołączona do tych danych w usłudze Azure Metrics. LAD ignoruje to pole.

CounterSpecifier jest dowolnym identyfikatorem. Odbiorcy metryk, takie jak Azure Portal wykresy i funkcja alertów, używają counterSpecifier jako klucza, który identyfikuje metrykę lub wystąpienie metryki. W przypadku `builtin` metryk zalecamy korzystanie z wartości counterSpecifier, które zaczynają się od `/builtin/` . W przypadku zbierania określonego wystąpienia metryki Zalecamy dołączenie identyfikatora wystąpienia do wartości counterSpecifier. Oto niektóre przykłady:

* `/builtin/Processor/PercentIdleTime` -Bezczynny czas średni dla wszystkich procesorów wirtualnych vCPU
* `/builtin/Disk/FreeSpace(/mnt)` -Wolne miejsce dla systemu plików/mnt
* `/builtin/Disk/FreeSpace` -Wolne miejsce średnie dla wszystkich zainstalowanych systemów plików

Ani LAD, ani Azure Portal nie oczekuje, że wartość counterSpecifier będzie zgodna z żadnym wzorcem. Spójne w sposobie konstruowania wartości counterSpecifier.

Po określeniu `performanceCounters` lad zawsze zapisuje dane w tabeli w usłudze Azure Storage. Możesz mieć te same dane, które są zapisywane w obiektach Blob JSON i/lub Event Hubs, ale nie można wyłączyć zapisywania danych w tabeli. Wszystkie wystąpienia rozszerzenia diagnostyki skonfigurowane do używania tej samej nazwy konta magazynu i punktu końcowego dodają metryki i dzienniki do tej samej tabeli. Jeśli zbyt wiele maszyn wirtualnych jest do zapisu w tej samej partycji tabeli, platforma Azure może ograniczać operacje zapisu do tej partycji. Ustawienie eventVolume powoduje, że wpisy mają być rozłożone na 1 (małe), 10 (średni) lub 100 (duże) różne partycje. Zwykle "średnie" jest wystarczające, aby zapewnić, że ruch nie jest ograniczany. Funkcja metryki platformy Azure Azure Portal używa danych z tej tabeli do tworzenia wykresów lub do wyzwalania alertów. Nazwa tabeli jest połączeniem tych ciągów:

* `WADMetrics`
* "ScheduledTransferPeriod" dla zagregowanych wartości przechowywanych w tabeli
* `P10DV2S`
* Data w postaci "RRRRMMDD", która zmienia się co 10 dni

Przykłady obejmują `WADMetricsPT1HP10DV2S20170410` i `WADMetricsPT1MP10DV2S20170609` .

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

Ta sekcja opcjonalna kontroluje zbieranie zdarzeń dzienników z dziennika systemowego. Jeśli sekcja zostanie pominięta, zdarzenia dziennika systemowego nie są przechwytywane.

Kolekcja syslogEventConfiguration ma jeden wpis dla każdego interesującego Cię obiektu dziennika systemowego. Jeśli minSeverity ma wartość "NONE" dla konkretnej funkcji lub jeśli ta funkcja nie występuje w elemencie w ogóle, żadne zdarzenia z tego obiektu nie są przechwytywane.

Element | Wartość
------- | -----
ujścia | Rozdzielana przecinkami lista nazw ujścia, do których są publikowane poszczególne zdarzenia dziennika. Wszystkie zdarzenia dzienników zgodne z ograniczeniami w syslogEventConfiguration są publikowane w każdym z wymienionych zbiorników. Przykład: "EHforsyslog"
facilityName | Nazwa funkcji dziennika systemowego (na przykład "LOG \_ User" lub "log \_ LOCAL0"). Pełną listę można znaleźć w sekcji "udogodnienie" [strony dziennik](http://man7.org/linux/man-pages/man3/syslog.3.html) systemu.
minSeverity | Poziom ważności dziennika systemu (na przykład "LOG \_ err" lub " \_ info log"). Pełną listę można znaleźć w sekcji "Level" [strony dziennika](http://man7.org/linux/man-pages/man3/syslog.3.html) systemu. Rozszerzenie przechwytuje zdarzenia wysyłane do obiektu na poziomie lub powyżej określonego poziomu.

Po określeniu `syslogEvents` lad zawsze zapisuje dane w tabeli w usłudze Azure Storage. Możesz mieć te same dane, które są zapisywane w obiektach Blob JSON i/lub Event Hubs, ale nie można wyłączyć zapisywania danych w tabeli. Zachowanie partycjonowania tej tabeli jest takie samo, jak opisano w temacie `performanceCounters` . Nazwa tabeli jest połączeniem tych ciągów:

* `LinuxSyslog`
* Data w postaci "RRRRMMDD", która zmienia się co 10 dni

Przykłady obejmują `LinuxSyslog20170410` i `LinuxSyslog20170609` .

### <a name="perfcfg"></a>perfCfg

Ta opcjonalna sekcja kontroluje wykonywanie dowolnych zapytań [OMI](https://github.com/Microsoft/omi) .

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
namespace | obowiązkowe Przestrzeń nazw OMI, w której należy wykonać zapytanie. Jeśli nie zostanie określony, wartością domyślną jest "root/SCX" wdrożoną przez [dostawców międzyplatformowych programu System Center](https://github.com/Microsoft/SCXcore).
query | Zapytanie OMI, które ma zostać wykonane.
table (stolik) | obowiązkowe Tabela usługi Azure Storage na wyznaczynym koncie magazynu (zobacz [Ustawienia chronione](#protected-settings)).
frequency | obowiązkowe Liczba sekund między wykonaniem zapytania. Wartość domyślna to 300 (5 minut); wartość minimalna to 15 sekund.
ujścia | obowiązkowe Rozdzielana przecinkami lista nazw dodatkowych obiektów ujścia, do których należy opublikować nieprzetworzone przykładowe wyniki metryki. Żadne agregacje tych nieprzetworzonych próbek nie są obliczane przez rozszerzenie ani za pomocą metryk platformy Azure.

Należy określić "Table" lub "ujścia" albo oba te elementy.

### <a name="filelogs"></a>fileLogs

Steruje przechwytywaniem plików dziennika. LAD przechwytuje nowe wiersze tekstu w miarę ich zapisywania do pliku i zapisuje je w wierszach tabeli i/lub w określonych ujściach (JsonBlob lub EventHub).

> [!NOTE]
> fileLogs są przechwytywane przez podskładnik klasy LAD o nazwie `omsagent` . W celu zbierania fileLogs należy upewnić się, że `omsagent` użytkownik ma uprawnienia do odczytu określonych plików, a także uprawnienia do wykonywania dla wszystkich katalogów w ścieżce do tego pliku. Możesz to sprawdzić, uruchamiając `sudo su omsagent -c 'cat /path/to/file'` po zainstalowaniu lad.

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
 — plik | Pełna nazwa ścieżki pliku dziennika do obserwowania i przechwycenia. Nazwa ścieżki musi mieć nazwę pojedynczego pliku; nie może to być nazwa katalogu ani zawierać symboli wieloznacznych. Konto użytkownika "omsagent" musi mieć dostęp do odczytu do ścieżki pliku.
table (stolik) | obowiązkowe Tabela usługi Azure Storage w wyznaczonym koncie magazynu (zgodnie z konfiguracją chronioną), do której zapisywane są nowe wiersze z "ogona" pliku.
ujścia | obowiązkowe Rozdzielana przecinkami lista nazw dodatkowych obiektów ujścia, do których są wysyłane wiersze dziennika.

Należy określić "Table" lub "ujścia" albo oba te elementy.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metryki obsługiwane przez wbudowanego dostawcę

Dostawca metryk wbudowanej jest źródłem metryk najbardziej interesujących dla szerokiego zbioru użytkowników. Te metryki należą do pięciu szerokich klas:

* Procesor
* Pamięć
* Sieć
* Filesystem
* Dysk

### <a name="builtin-metrics-for-the-processor-class"></a>metryki wbudowane dla klasy procesora

Klasa procesora metryk zawiera informacje o użyciu procesora w maszynie wirtualnej. Podczas agregowania wartości procentowych wynik jest średni dla wszystkich procesorów CPU. W przypadku maszyny wirtualnej z dwoma vCPUmi, jeśli jedna vCPU była 100% zajęta, a druga była 100% bezczynności, zgłoszone PercentIdleTime byłyby 50. Jeśli każda vCPU była zajęta w tym samym okresie przez 50%, raportowany wynik będzie również 50. W przypadku maszyny wirtualnej z czterema vCPU, gdy jeden vCPU 100% zajęty i pozostałe bezczynne, zgłoszone PercentIdleTime byłyby 75.

counter | Znaczenie
------- | -------
PercentIdleTime | Procent czasu w oknie agregacji, że procesor wykonał pętlę bezczynności jądra
PercentProcessorTime | Procent czasu wykonującego wątek niebędący bezczynny
PercentIOWaitTime | Procent czasu oczekiwania na zakończenie operacji we/wy
PercentInterruptTime | Procent czasu wykonywania przerw w działaniu sprzętu/oprogramowania i wywołań DPC (opóźnione wywołania procedur)
PercentUserTime | Czas braku bezczynności w oknie agregacji, procent czasu spędzony przez użytkownika w normalnym priorytecie
PercentNiceTime | Czas braku bezczynności, wartość procentowa należąca do obniżonego priorytetu (Nice)
PercentPrivilegedTime | Czas braku bezczynności (%) w trybie uprzywilejowanym (jądro)

Pierwsze cztery liczniki powinny mieć sumę do 100%. Ostatnie trzy liczniki również są sumowane do 100%; dzielą sumę PercentProcessorTime, PercentIOWaitTime i PercentInterruptTime.

Aby uzyskać pojedynczą metrykę agregowaną we wszystkich procesorach, ustaw wartość `"condition": "IsAggregate=TRUE"` . Aby uzyskać metrykę dla określonego procesora, na przykład drugi procesor logiczny dla maszyny wirtualnej z czterema vCPU, ustaw polecenie `"condition": "Name=\\"1\\""` . Liczby procesorów logicznych znajdują się w zakresie `[0..n-1]` .

### <a name="builtin-metrics-for-the-memory-class"></a>metryki wbudowane dla klasy pamięci

Klasa Memory metryk zawiera informacje o wykorzystaniu pamięci, stronicowaniu i zamianie.

counter | Znaczenie
------- | -------
AvailableMemory | Dostępna pamięć fizyczna w MiB
PercentAvailableMemory | Dostępna pamięć fizyczna jako procent całkowitej ilości pamięci
UsedMemory | Używana pamięć fizyczna (MiB)
PercentUsedMemory | Użycie pamięci fizycznej jako procent całkowitej ilości pamięci
PagesPerSec | Stronicowanie łącznie (odczyt/zapis)
PagesReadPerSec | Strony odczytane z magazynu zapasowego (plik wymiany, plik programu, zamapowany plik itp.)
PagesWrittenPerSec | Strony zapisywane w magazynie zapasowym (plik wymiany, zamapowany plik itp.)
AvailableSwap | Nieużywany obszar wymiany (MiB)
PercentAvailableSwap | Nieużywany obszar wymiany jako procent całkowitej wymiany
UsedSwap | Obszar wymiany w użyciu (MiB)
PercentUsedSwap | Przestrzeń wymiany w użyciu jako procent całkowitej wymiany

Ta klasa metryk ma tylko jedno wystąpienie. Atrybut "Condition" nie ma użytecznych ustawień i powinien zostać pominięty.

### <a name="builtin-metrics-for-the-network-class"></a>metryki wbudowane dla klasy sieci

Klasa sieci metryk zawiera informacje o aktywności sieciowej w poszczególnych interfejsach sieciowych od rozruchu. LAD nie ujawnia metryk przepustowości, które można pobrać z metryk hosta.

counter | Znaczenie
------- | -------
BytesTransmitted | Całkowita liczba bajtów wysłanych od rozruchu
BytesReceived | Całkowita liczba bajtów odebranych od rozruchu
BytesTotal | Całkowita liczba bajtów wysłanych lub odebranych od momentu rozruchu
PacketsTransmitted | Całkowita liczba pakietów wysłanych od rozruchu
PacketsReceived | Całkowita liczba odebranych pakietów od rozruchu
TotalRxErrors | Liczba błędów odbierania od rozruchu
TotalTxErrors | Liczba błędów przesyłania od rozruchu
TotalCollisions | Liczba kolizji zgłoszonych przez porty sieciowe od rozruchu

 Chociaż ta klasa jest wystąpieniem, LAD nie obsługuje przechwytywania metryk sieci zagregowanych ze wszystkich urządzeń sieciowych. Aby uzyskać metryki dla określonego interfejsu, takiego jak eth0, Set `"condition": "InstanceID=\\"eth0\\""` .

### <a name="builtin-metrics-for-the-filesystem-class"></a>metryki wbudowane dla klasy FileSystem

Klasa systemu plików metryk zawiera informacje o użyciu systemu plików. Wartości bezwzględne i procentowe są raportowane w miarę ich wyświetlania dla zwykłego użytkownika (nie katalogu głównego).

counter | Znaczenie
------- | -------
FreeSpace | Ilość dostępnego miejsca na dysku w bajtach
UsedSpace | Zajęte miejsce na dysku w bajtach
PercentFreeSpace | Procent wolnego miejsca
PercentUsedSpace | Zajęte miejsce (%)
PercentFreeInodes | Procent nieużywanych węzłów i
PercentUsedInodes | Procent przydzieloną (w użyciu) węzłów i zsumowany dla wszystkich systemów plików
BytesReadPerSecond | Odczytane bajty na sekundę
BytesWrittenPerSecond | Bajty zapisywane na sekundę
BytesPerSecond | Bajty odczytane lub zapisywana na sekundę
ReadsPerSecond | Operacje odczytu na sekundę
WritesPerSecond | Operacje zapisu na sekundę
TransfersPerSecond | Operacje odczytu lub zapisu na sekundę

Zagregowane wartości we wszystkich systemach plików można uzyskać przez ustawienie `"condition": "IsAggregate=True"` . Wartości dla określonego zainstalowanego systemu plików, na przykład "/mnt", można uzyskać przez ustawienie `"condition": 'Name="/mnt"'` . 

**Uwaga** : w przypadku korzystania z witryny Azure Portal zamiast JSON, prawidłowy warunek jest w postaci nazwa = "/mnt"

### <a name="builtin-metrics-for-the-disk-class"></a>metryki wbudowane dla klasy Disk

Klasa dysku metryk zawiera informacje o użyciu urządzenia dyskowego. Te statystyki dotyczą całego dysku. Jeśli na urządzeniu istnieje wiele systemów plików, liczniki dla tego urządzenia są efektywnie agregowane we wszystkich z nich.

counter | Znaczenie
------- | -------
ReadsPerSecond | Operacje odczytu na sekundę
WritesPerSecond | Operacje zapisu na sekundę
TransfersPerSecond | Łączna liczba operacji na sekundę
AverageReadTime | Średnia liczba sekund na operację odczytu
AverageWriteTime | Średnia sekund na operację zapisu
AverageTransferTime | Średnia liczba sekund na operację
AverageDiskQueueLength | Średnia liczba operacji na dyskach umieszczonych w kolejce
ReadBytesPerSecond | Liczba odczytanych bajtów na sekundę
WriteBytesPerSecond | Liczba bajtów zapisanych na sekundę
BytesPerSecond | Liczba bajtów odczytanych lub zapisywana na sekundę

Zagregowane wartości na wszystkich dyskach można uzyskać przez ustawienie `"condition": "IsAggregate=True"` . Aby uzyskać informacje dotyczące określonego urządzenia (na przykład/dev/sdf1), ustaw wartość `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="installing-and-configuring-lad-30"></a>Instalowanie i Konfigurowanie LAD 3,0

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Przy założeniu, że chronione ustawienia znajdują się w pliku ProtectedSettings.jsna, a Twoje informacje o konfiguracji publicznej są PublicSettings.jsna, uruchom następujące polecenie:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

W poleceniu przyjęto założenie, że używasz trybu zarządzania zasobami platformy Azure dla interfejsu wiersza polecenia platformy Azure. Aby skonfigurować LAD dla maszyn wirtualnych z modelem wdrożenia klasycznego (ASM), przełącz się do trybu "ASM" ( `azure config mode asm` ) i Pomiń nazwę grupy zasobów w poleceniu. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu wiersza polecenia dla wielu platform](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

### <a name="powershell"></a>PowerShell

Przy założeniu, że chronione ustawienia znajdują się w `$protectedSettings` zmiennej, a informacje o konfiguracji publicznej są w `$publicSettings` zmiennej, uruchom następujące polecenie:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="an-example-lad-30-configuration"></a>Przykładowa konfiguracja LAD 3,0

W oparciu o powyższe definicje przedstawiono przykładową konfigurację rozszerzenia LAD 3,0 z objaśnieniem. Aby zastosować ten przykład do przypadku, należy użyć własnej nazwy konta magazynu, tokenu SAS konta i tokenów SAS EventHubs.

> [!NOTE]
> W zależności od tego, czy używasz interfejsu wiersza polecenia platformy Azure lub programu PowerShell, aby zainstalować LAD, metoda udostępniania ustawień publicznych i chronionych różni się. Jeśli używasz interfejsu wiersza polecenia platformy Azure, Zapisz następujące ustawienia, aby ProtectedSettings.jsi PublicSettings.jsdo użycia z przykładowym powyższym poleceniem. Jeśli używasz programu PowerShell, Zapisz ustawienia w `$protectedSettings` i `$publicSettings` uruchamiając polecenie `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Ustawienia chronione

Skonfigurowane ustawienia chronione:

* konto magazynu
* pasujący token SAS konta
* kilka zlewów (JsonBlob lub EventHubs z tokenami SAS)

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

Te ustawienia publiczne powodują LAD:

* Przekaż metryki czasu procesora (%) i zużyte miejsca na dysku do `WADMetrics*` tabeli
* Przekazywanie komunikatów z funkcji dziennika systemowego "User" i ważności "info" do `LinuxSyslog*` tabeli
* Przekaż nieprzetworzone wyniki zapytania OMI (PercentProcessorTime i PercentIdleTime) do nazwanej `LinuxCPU` tabeli
* Przekaż dołączone wiersze `/var/log/myladtestlog` do `MyLadTestLog` tabeli

W każdym przypadku dane są również przekazywane do:

* Magazyn obiektów blob platformy Azure (nazwa kontenera jest tak zdefiniowany w ujścia JsonBlob)
* Punkt końcowy EventHubs (określony w ujścia EventHubs)

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

`resourceId`W konfiguracji musi być zgodna z konfiguracją maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.

* Wykresy metryk i alerty platformy Azure wiedzą, że identyfikator zasobu maszyny wirtualnej, nad którą pracujesz. Oczekiwane jest znalezienie danych dla maszyny wirtualnej przy użyciu identyfikatora resourceId klucza wyszukiwania.
* Jeśli używasz automatycznego skalowania platformy Azure, identyfikator zasobu w konfiguracji skalowania automatycznego musi być zgodny z identyfikatorem resourceId używanym przez LAD.
* Identyfikator resourceId jest wbudowany w nazwy JsonBlobs pisanych przez LAD.

## <a name="view-your-data"></a>Wyświetlanie danych

Użyj Azure Portal, aby wyświetlić dane wydajności lub ustawić alerty:

![Zrzut ekranu przedstawia Azure Portal z wybraną metryką ilość miejsca na dysku i wykresem wyjściowym.](./media/diagnostics-linux/graph_metrics.png)

`performanceCounters`Dane są zawsze przechowywane w tabeli usługi Azure Storage. Interfejsy API usługi Azure Storage są dostępne dla wielu języków i platform.

Dane wysyłane do ujścia JsonBlob są przechowywane w obiektach Blob na koncie magazynu o nazwie w [ustawieniach chronionych](#protected-settings). Można korzystać z danych obiektów BLOB przy użyciu dowolnych interfejsów API Blob Storage platformy Azure.

Ponadto można używać tych narzędzi interfejsu użytkownika do uzyskiwania dostępu do danych w usłudze Azure Storage:

* Program Visual Studio Eksplorator serwera.
* [Zrzut ekranu przedstawia kontenery i tabele w Eksplorator usługi Azure Storage.](https://azurestorageexplorer.codeplex.com/ "Eksplorator usługi Azure Storage").

Ta migawka sesji Eksplorator usługi Microsoft Azure Storage zawiera wygenerowane tabele i kontenery usługi Azure Storage ze prawidłowej konfiguracji rozszerzenia LAD 3,0 na testowej maszynie wirtualnej. Obraz nie jest dokładnie zgodny z [konfiguracją przykładu LAD 3,0](#an-example-lad-30-configuration).

![image (obraz)](./media/diagnostics-linux/stg_explorer.png)

Zapoznaj się z odpowiednią [dokumentacją EventHubs](../../event-hubs/event-hubs-about.md) , aby dowiedzieć się, jak korzystać z komunikatów opublikowanych w punkcie końcowym EventHubs.

## <a name="next-steps"></a>Następne kroki

* Utwórz alerty metryk w [Azure monitor](../../azure-monitor/platform/alerts-classic-portal.md) dla zbieranych metryk.
* Utwórz [wykresy monitorowania](../../azure-monitor/platform/data-platform.md) dla metryk.
* Dowiedz się, jak [utworzyć zestaw skalowania maszyn wirtualnych](../linux/tutorial-create-vmss.md) przy użyciu metryk do sterowania skalowaniem automatycznym.
