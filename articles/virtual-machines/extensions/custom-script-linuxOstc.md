---
title: Uruchamianie niestandardowego rozszerzenia skryptu w wersji 1 (przestarzałe) na maszynach wirtualnych z systemem Linux na platformie Azure
description: Automatyzowanie zadań konfiguracyjnych maszyn wirtualnych z systemem Linux przy użyciu rozszerzenia niestandardowego skryptu v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: 8b2099a6a48c9393b6e9e8b983a4acac2933bf06
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973839"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Korzystanie z rozszerzenia niestandardowego skryptu platformy Azure w wersji 1 z maszynami wirtualnymi z systemem Linux

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Rozszerzenie skryptu niestandardowego wersja 1 pobiera i uruchamia skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innych zadań związanych z konfiguracją/zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub innej dostępnej lokalizacji w Internecie lub można je udostępnić do środowiska uruchomieniowego rozszerzenia.

Rozszerzenie niestandardowego skryptu integruje się z szablonami Azure Resource Manager. Można go również uruchomić przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell, Azure Portal lub API REST platformy Azure Virtual Machines.

W tym artykule szczegółowo przedstawiono sposób korzystania z rozszerzenia niestandardowego skryptu z interfejsu wiersza polecenia platformy Azure oraz uruchamiania rozszerzenia przy użyciu szablonu Azure Resource Manager. Ten artykuł zawiera również procedurę rozwiązywania problemów z systemami Linux.

Istnieją dwa rozszerzenia niestandardowego skryptu systemu Linux:

* Wersja 1 — Microsoft. OSTCExtensions. CustomScriptForLinux

* Wersja 2 — Microsoft. Azure. Extensions. CustomScript

Przełącz nowe i istniejące wdrożenia, aby użyć zamiast niego nowej wersji ([Microsoft. Azure. Extensions. CustomScript](custom-script-linux.md)). Nowa wersja jest przeznaczona do użycia bezpośrednio w miejsce starszej. Dlatego migracja jest tak łatwa jak zmiana nazwy i wersji — nie trzeba zmieniać konfiguracji rozszerzenia.

### <a name="operating-system"></a>System operacyjny

Obsługiwane dystrybucje systemu Linux:

* CentOS 6,5 i nowsze
* Debian 8 i nowsze
  * Debian 8,7 nie jest dostarczany z python2 w najnowszych obrazach, co powoduje przerwanie CustomScriptForLinux.
* FreeBSD
* OpenSUSE 13,1 i nowsze
* Oracle Linux 6,4 i nowsze
* SUSE Linux Enterprise Server 11 z dodatkiem SP3 lub nowszym
* Ubuntu 12,04 i nowsze

### <a name="script-location"></a>Lokalizacja skryptu

Możesz użyć rozszerzenia, aby użyć poświadczeń usługi Azure Blob Storage w celu uzyskania dostępu do usługi Azure Blob Storage. Alternatywnie lokalizacja skryptu może być dowolna, w której maszyna wirtualna może kierować do tego punktu końcowego, na przykład w usłudze GitHub, na wewnętrznym serwerze plików itp.

### <a name="internet-connectivity"></a>Łączność z Internetem

Jeśli musisz pobrać skrypt zewnętrzny, taki jak GitHub lub Azure Storage, należy otworzyć dodatkową zaporę/porty sieciowej grupy zabezpieczeń. Jeśli na przykład skrypt znajduje się w usłudze Azure Storage, możesz zezwolić na dostęp za pomocą tagów usługi Azure sieciowej grupy zabezpieczeń dla [magazynu](../../virtual-network/network-security-groups-overview.md#service-tags).

Jeśli skrypt znajduje się na serwerze lokalnym, może być konieczne otwarcie dodatkowych portów zapory/sieciowych grup zabezpieczeń.

### <a name="tips-and-tricks"></a>Porady i wskazówki

* Najwyższy współczynnik błędów dla tego rozszerzenia jest spowodowany błędami składni w skrypcie. Sprawdź, czy skrypt działa bezbłędnie, a także umieść dodatkowe rejestrowanie w skrypcie, aby ułatwić wyszukiwanie miejsca, w którym wystąpił błąd.
* Pisz skrypty idempotentne, aby w razie ich ponownego uruchomienia przez przypadek więcej niż jeden raz nie nastąpiły zmiany systemu.
* Upewnij się, że skrypty nie wymagają wprowadzania danych przez użytkownika po ich uruchomieniu.
* Aby skrypt mógł zostać uruchomiony, może być 90 min, co spowoduje niepowodzenie aprowizacji rozszerzenia.
* Nie należy umieszczać ponownych uruchomień wewnątrz skryptu, co spowoduje problemy z innymi zainstalowanymi rozszerzeniami i po ponownym uruchomieniu, rozszerzenie nie będzie kontynuowane po ponownym uruchomieniu. 
* Jeśli masz skrypt, który spowoduje ponowne uruchomienie komputera, zainstaluj aplikacje i Uruchom skrypty itp. Należy zaplanować ponowny rozruch przy użyciu zadania firmy CRONUS lub użyć narzędzi takich jak DSC lub Chef Puppet.
* Rozszerzenie spowoduje uruchomienie skryptu tylko raz, aby uruchomić skrypt na każdym rozruchu, a następnie użyć [obrazu Cloud-init](../linux/using-cloud-init.md)  i użyć [skryptów dla modułu rozruchu](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) . Alternatywnie możesz użyć skryptu, aby utworzyć systemową jednostkę usługi.
* Jeśli chcesz zaplanować uruchomienie skryptu, użyj rozszerzenia, aby utworzyć zadanie firmy cronus.
* W trakcie działania skryptu będziesz widzieć tylko stan „przechodzenie” z witryny Azure Portal lub interfejsu wiersza polecenia. Jeśli chcesz więcej częste aktualizacje stanu uruchomionego skryptu, musisz utworzyć własne rozwiązanie.
* Niestandardowe rozszerzenie skryptu nie obsługuje natywnie serwerów proxy, jednak można użyć narzędzia transferu plików, które obsługuje serwery proxy w skrypcie, na przykład *zwinięcie*.
* Należy pamiętać, że nie są to domyślne lokalizacje katalogów, od których mogą polegać skrypty lub polecenia, i które obsługują tę logikę.

## <a name="extension-schema"></a>Schemat rozszerzenia

Konfiguracja rozszerzenia niestandardowego skryptu określa elementy, takie jak lokalizacja skryptu i polecenie do uruchomienia. Tę konfigurację można zapisać w plikach konfiguracji, określić ją w wierszu polecenia lub określić w szablonie Azure Resource Manager. 

Poufne dane można przechowywać w chronionej konfiguracji, która jest szyfrowana i odszyfrowywana tylko wewnątrz maszyny wirtualnej. Konfiguracja chroniona jest przydatna, gdy polecenie wykonywania zawiera wpisy tajne, takie jak hasło.

Te elementy powinny być traktowane jako dane poufne i określone w konfiguracji ustawień chronionych przez rozszerzenia. Dane ustawienia chronionego rozszerzenia maszyny wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| publisher | Microsoft. OSTCExtensions | ciąg |
| typ | CustomScriptForLinux | ciąg |
| typeHandlerVersion | 1.5 | int |
| fileUris (np.) | `https://github.com/MyProject/Archive/MyPythonScript.py` | array |
| Sekcji commandtoexecute (np.) | MyPythonScript.py Python \<my-param1\> | ciąg |
| enableInternalDNSCheck | true | boolean |
| storageAccountName (np.) | examplestorageacct | ciąg |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | ciąg |

### <a name="property-value-details"></a>Szczegóły wartości właściwości

* `fileUris`: (opcjonalnie, tablica ciągów) Lista identyfikatorów URI skryptów
* `enableInternalDNSCheck`: (opcjonalnie, bool) domyślnie ma wartość true, ustawienie wartości false powoduje wyłączenie sprawdzania DNS.
* `commandToExecute`: (opcjonalnie, String) skrypt punktu wejścia do wykonania
* `storageAccountName`: (opcjonalnie, String) nazwa konta magazynu
* `storageAccountKey`: (opcjonalnie, String) klucz dostępu konta magazynu

Następujące wartości można ustawić w ustawieniach publicznych lub chronionych, ale nie można ustawić tych wartości w ustawieniach publicznych i chronionych.

* `commandToExecute`

Korzystanie z ustawień publicznych może być przydatne w przypadku debugowania, ale zdecydowanie zaleca się używanie ustawień chronionych.

Ustawienia publiczne są wysyłane w postaci zwykłego tekstu do maszyny wirtualnej, na której skrypt zostanie wykonany.  Ustawienia chronione są szyfrowane przy użyciu klucza znanego tylko na platformie Azure i maszynie wirtualnej. Ustawienia są zapisywane na maszynie wirtualnej w miarę ich wysyłania, tj. Jeśli ustawienia zostały zaszyfrowane, są zapisywane w postaci zaszyfrowanej na maszynie wirtualnej. Certyfikat używany do odszyfrowywania zaszyfrowanych wartości jest przechowywany na maszynie wirtualnej i używany do odszyfrowywania ustawień (w razie potrzeby) w czasie wykonywania.

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Schemat JSON opisany w poprzedniej sekcji można użyć w szablonie Azure Resource Manager, aby uruchomić rozszerzenie niestandardowego skryptu podczas wdrażania szablonu Azure Resource Manager.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>W tych nazwach właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazw, jak pokazano poniżej.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Jeśli używasz interfejsu wiersza polecenia platformy Azure do uruchamiania niestandardowego rozszerzenia skryptu, Utwórz plik lub pliki konfiguracyjne. Musisz mieć co najmniej wartość "sekcji commandtoexecute".

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Opcjonalnie można określić ustawienia w poleceniu jako ciąg sformatowany w formacie JSON. Pozwala to określić konfigurację podczas wykonywania i bez oddzielnego pliku konfiguracji.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Przykłady interfejsu wiersza polecenia platformy Azure

#### <a name="public-configuration-with-no-script-file"></a>Konfiguracja publiczna bez pliku skryptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Pliki konfiguracji publicznej i chronionej

Plik konfiguracji publicznej służy do określania identyfikatora URI pliku skryptu. Aby określić polecenie do uruchomienia, należy użyć chronionego pliku konfiguracji.

Plik konfiguracji publicznej:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Chroniony plik konfiguracji:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po uruchomieniu rozszerzenia skryptu niestandardowego skrypt jest tworzony lub pobierany do katalogu podobnego do poniższego przykładu. Dane wyjściowe polecenia są również zapisywane w tym katalogu w `stdout` `stderr` plikach i.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Aby rozwiązać problem, najpierw sprawdź dziennik agenta systemu Linux, upewnij się, że rozszerzenie zostało uruchomione, sprawdź:

```bash
/var/log/waagent.log
```

Należy wyszukać wykonanie rozszerzenia, będzie wyglądać następująco:

```output
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

Niektóre punkty do uwagi:

1. Wartość enable jest uruchamiana, gdy polecenie zostanie uruchomione.
1. Pobieranie odnosi się do pobierania pakietu rozszerzenia CustomScript z platformy Azure, a nie plików skryptów określonych w fileUris.
1. Możesz również sprawdzić, w którym pliku dziennika jest napisane `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

Następnym krokiem jest sprawdzenie pliku dziennika, jest to format:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Należy poszukać poszczególnych wykonań, co będzie wyglądać następująco:

```output
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
```

W tym miejscu możesz zobaczyć:

* Uruchamianie polecenia enable jest tym dziennikiem
* Ustawienia przesłane do rozszerzenia
* Rozszerzenie pobiera plik i wynik.
* Polecenie jest uruchamiane i wynik.

Możesz również pobrać stan wykonywania rozszerzenia niestandardowego skryptu za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Dane wyjściowe wyglądają następująco:

```output
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić kod, bieżące problemy i wersje, zobacz [repozytorium rozszerzeń CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).