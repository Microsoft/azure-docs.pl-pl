---
title: Uruchamianie skryptów niestandardowych na maszynach wirtualnych z systemem Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań konfiguracji maszyny Wirtualnej systemu Linux za pomocą niestandardowego rozszerzenia skryptu w wersji 2
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: roiyz
ms.openlocfilehash: b9bc3ef0cf5dd54802d32058afb904800c364c19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64725231"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Azure niestandardowy skrypt rozszerzenia w wersji 2 za pomocą maszyn wirtualnych systemu Linux
Rozszerzenie niestandardowego skryptu w wersji 2 pobiera i uruchamia skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrażaniu, instalacja oprogramowania lub innych zadań konfiguracji i zarządzania nimi. Skrypty można pobrać z usługi Azure Storage lub w innej lokalizacji internet dostępny, lub można udostępnić je w czasie wykonywania rozszerzenia. 

Rozszerzenie niestandardowego skryptu można zintegrować z szablonami usługi Azure Resource Manager. Można również uruchomić przy użyciu wiersza polecenia platformy Azure, programu PowerShell lub interfejsu API REST dla maszyn wirtualnych platformy Azure.

Ten artykuł szczegółowo opisuje sposób korzystania z rozszerzenia niestandardowego skryptu z wiersza polecenia platformy Azure oraz sposobu uruchamiania rozszerzenia za pomocą szablonu usługi Azure Resource Manager. Ten artykuł zawiera również kroki rozwiązywania problemów w systemach Linux.


Istnieją dwa rozszerzenia skryptu niestandardowego systemu Linux:
* Wersja 1 — Microsoft.OSTCExtensions.CustomScriptForLinux
* Wersja 2 — Microsoft.Azure.Extensions.CustomScript

Przełącz nowych i istniejących wdrożeń, aby zamiast tego użyj nowej wersji 2. Nowa wersja ma być zamiennikiem. W związku z tym migracja jest równie proste jak zmiana nazwy i wersji, nie trzeba zmienić konfigurację rozszerzenia.


### <a name="operating-system"></a>System operacyjny

Rozszerzenie niestandardowego skryptu dla systemu Linux będą uruchamiane na rozszerzenie rozszerzenia obsługiwane systemu operacyjnego, aby uzyskać więcej informacji, zobacz [artykułu](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Lokalizacja skryptu

Rozszerzenia można użyć na potrzeby dostępu do usługi Azure Blob storage poświadczeń magazynu obiektów Blob platformy Azure. Alternatywnie lokalizacja skryptu może być dowolnego miejsca, tak długo, jak maszyna wirtualna może kierować do tego punktu końcowego, takich jak GitHub, serwer wewnętrzny plików itp.

### <a name="internet-connectivity"></a>Łączność z Internetem
Jeśli musisz pobrać skrypt zewnętrznie takich jak GitHub lub usługi Azure Storage, następnie dodatkowe zapory i sieci grupy zabezpieczeń muszą być otwarte porty. Na przykład jeśli skrypt znajduje się w usłudze Azure Storage, można zezwolić na dostęp, za pomocą tagów usługi sieciowej grupy zabezpieczeń platformy Azure dla [magazynu](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Jeśli skrypt znajduje się na serwerze lokalnym, a następnie nadal potrzebujesz dodatkowych Zapora/sieci zabezpieczeń grupy portów muszą być otwarte.

### <a name="tips-and-tricks"></a>Porady i wskazówki
* Najwyższy współczynnik błędów dla tego rozszerzenia jest spowodowany błędami składni w skrypcie. Sprawdź, czy skrypt działa bezbłędnie, a także umieść dodatkowe rejestrowanie w skrypcie, aby ułatwić wyszukiwanie miejsca, w którym wystąpił błąd.
* Pisz skrypty idempotentne, aby w razie ich ponownego uruchomienia przez przypadek więcej niż jeden raz nie nastąpiły zmiany systemu.
* Upewnij się, że skrypty są wymagane dane wejściowe użytkownika podczas uruchamiania.
* 90 minut mogą uzyskać skrypt do uruchomienia, nic dłużej spowoduje niepowodzenie aprowizacji rozszerzenia.
* Nie należy umieszczać wewnątrz skrypt jest uruchamiany ponownie, spowoduje to problemy z innymi rozszerzeniami, które są instalowane i po ponownym uruchomieniu, rozszerzenie nie będzie kontynuowana po ponownym uruchomieniu. 
* Jeśli masz skrypt, który powoduje ponowne uruchomienie, zainstaluj aplikacje i uruchom skrypty itp. Należy zaplanować ponowny rozruch przy użyciu zadania Cron lub narzędzi, takich jak DSC lub Chef, Puppet rozszerzenia.
* Rozszerzenie tylko uruchamianie skryptu, jeden raz, jeśli chcesz uruchomić skrypt w każdym rozruchu, wówczas można użyć [obraz pakietu cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) i użyj [skryptów na rozruch](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modułu. Alternatywnie służy skrypt tworzenia jednostki usługi Systemd.
* Chcąc Zaplanuj, kiedy skrypt będzie uruchamiany, należy użyć rozszerzenia do tworzenia zadania Cron. 
* W trakcie działania skryptu będziesz widzieć tylko stan „przechodzenie” z witryny Azure Portal lub interfejsu wiersza polecenia. Chcąc częstsze aktualizacje stanu uruchamianie skryptu, musisz utworzyć własne rozwiązanie.
* Rozszerzenie niestandardowego skryptu nie obsługuje natywnie serwery proxy, jednak można użyć narzędzie do transferu plików, który obsługuje serwery proxy w ramach skryptu, takie jak *Curl*. 
* Należy pamiętać, innego niż domyślny katalog lokalizacji, które mogą polegać Twoje skrypty lub polecenia, masz logikę obsługującą to.



## <a name="extension-schema"></a>Schemat rozszerzenia

Określa konfigurację rozszerzenia niestandardowego skryptu, elementów, takich jak lokalizacja skryptu i polecenia do uruchomienia. Możesz zapisać tę konfigurację w plikach konfiguracji, określ go w wierszu polecenia lub je określić w szablonie usługi Azure Resource Manager. 

Poufne dane można przechowywać w chronionym konfiguracji, która zostaje zaszyfrowany i odszyfrowane tylko wewnątrz maszyny wirtualnej. Chronionych konfiguracja jest użyteczna w przypadku, gdy polecenia wykonywania zawiera wpisy tajne, takie jak hasło.

Tych elementów powinien być traktowane jako poufne dane i określony w konfiguracji chronione ustawienia rozszerzeń. Dane platformy Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest zaszyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej.

```json
{
  "name": "config-app",
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute.Extensions | string |
| type | CustomScript | string |
| typeHandlerVersion | 2.0 | int |
| fileUris (np.) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (np.) | python MyPythonScript.py \<my-param1> | string |
| script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix (np.) | false | boolean |
| timestamp (np.) | 123456789 | 32-bitowa liczba całkowita |
| storageAccountName (np.) | examplestorageacct | string |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Szczegóły dotyczące wartości właściwości
* `skipDos2Unix`: (opcjonalne, wartość logiczna) Pomiń konwersji dos2unix adresów URL opartych na skryptach plików lub skryptu.
* `timestamp` Użycie (opcjonalne, 32-bitowa liczba całkowita), w tym polu tylko do wyzwolenia ponownie uruchom skrypt, zmieniając wartość tego pola.  Dowolna liczba całkowita jest dopuszczalne; tylko musi być inna niż poprzednia wartość.
  * `commandToExecute`: (**wymagane** Jeśli skrypt nie został ustawiony, ciąg) skrypt punktu wejścia do wykonania. Zamiast tego użyj tego pola, jeśli polecenie zawiera wpisy tajne, takie jak hasła.
* `script`: (**wymagane** przypadku commandToExecute ciągu) zakodowane w formacie base64 (i opcjonalnie gzip'ed) skrypt wykonywany przez/bin/sh.
* `fileUris`: (opcjonalne, tablicy ciągów) adresy URL plików mają być pobrane.
* `storageAccountName`: (opcjonalnie, ciąg) nazwa konta magazynu. Jeśli określisz poświadczeń magazynu wszystkich `fileUris` muszą być adresami URL dla obiektów blob platformy Azure.
* `storageAccountKey`: (opcjonalnie, ciąg) klucz dostępu konta magazynu


Można ustawić następujące wartości w ustawieniach publiczną lub chronioną, rozszerzenie spowoduje odrzucenie wszystkich konfiguracji, gdzie poniższe wartości są ustawione w ustawieniach zarówno publiczne i chronione.
* `commandToExecute`
* `script`
* `fileUris`

Za pomocą ustawień publicznego, może być przydatne do debugowania, ale zdecydowanie zalecane jest używanie chronionych ustawień.

Ustawienia publicznego są wysyłane w postaci zwykłego tekstu do maszyny Wirtualnej, gdy skrypt zostanie wykonany.  Chronione ustawienia są szyfrowane przy użyciu klucza znanego tylko platformy Azure i maszyny Wirtualnej. Ustawienia są zapisywane do maszyny Wirtualnej, ponieważ zostały wysłane, czyli jeśli były szyfrowane ustawienia są zapisywane zaszyfrowane na maszynie Wirtualnej. Certyfikat używany do odszyfrowania zaszyfrowanych wartości przechowywane na maszynie Wirtualnej i jest używany do odszyfrowywania ustawienia (w razie potrzeby) w czasie wykonywania.

#### <a name="property-skipdos2unix"></a>Właściwości: skipDos2Unix

Wartość domyślna to false, co oznacza, że konwersja dos2unix **jest** wykonywane.

Poprzednią wersję CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, będzie automatycznie przekonwertować plików systemu DOS plików systemu UNIX, tłumaczenia `\r\n` do `\n`. Tłumaczenie nadal istnieje i jest domyślnie włączone. Ta konwersja jest stosowany do wszystkich plików pobranych z fileUris lub ustawienia skryptu oparte na dowolnych z następujących kryteriów.

* Jeśli rozszerzenie jest jednym z `.sh`, `.txt`, `.py`, lub `.pl` zostanie przekonwertowany. Ustawienia skryptu zawsze będą zgodne te kryteria, ponieważ zakłada, że skrypt wykonywany z /bin/sh i zostanie zapisany jako script.sh na maszynie Wirtualnej.
* Jeśli plik, który rozpoczyna się od `#!`.

Konwersja dos2unix można było pominąć, ustawiając skipDos2Unix na wartość true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Właściwości: skrypt

CustomScript obsługuje wykonywanie skryptu zdefiniowanego przez użytkownika. Ustawienia skryptu, aby połączyć commandToExecute i fileUris do pojedynczego ustawienia. Zamiast pliku do pobrania z usługi Azure storage lub GitHub gist instalacji, możesz po prostu zakodować skrypt jako ustawienie. Skrypt może służyć do zastąpionego commandToExecute i fileUris.

Skrypt **musi** być zakodowany base64.  Skrypt można **opcjonalnie** można gzip'ed. Ustawienie skryptów może być używane w ustawieniach publiczne lub chronione. Maksymalny rozmiar danych parametru skryptu to 256 KB. Jeśli skrypt przekroczy ten rozmiar nie można wykonać.

Na przykład, biorąc pod uwagę następujący skrypt, zapisane /script.sh/ pliku.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Poprawne ustawienie CustomScript skrypt może konstruować, korzystając z danych wyjściowych następującego polecenia.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skrypt można opcjonalnie gzip'ed, aby jeszcze bardziej ograniczyć rozmiar (w większości przypadków). (CustomScript automatycznie wykryje użycie kompresję gzip.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript domen używa następującego algorytmu do uruchomienia skryptu.

 1. Potwierdź, że długość wartości skryptu nie przekracza 256 KB.
 1. Base64 — dekodowanie wartość skryptu
 1. _Próba_ do gunzip base64 zdekodować wartości
 1. Zapisz wartość zdekodowany (i opcjonalnie zdekompresowany) na dysku (/ var/lib/waagent/custom-script/#/script.sh)
 1. Uruchom skrypt przy użyciu /var/lib/waagent/custom-script/#/script.sh _ / bin/sh - c.


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON szczegółowo opisane w poprzedniej sekcji może służyć w szablonie usługi Azure Resource Manager do rozszerzenia niestandardowego skryptu są uruchamiane podczas wdrażania szablonu usługi Azure Resource Manager. Przykładowy szablon, który zawiera rozszerzenie niestandardowego skryptu można znaleźć w tym miejscu [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


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
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Nazwy tych właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazwy, jak pokazano poniżej.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Podczas korzystania z wiersza polecenia platformy Azure do uruchamiania rozszerzenia niestandardowego skryptu, Utwórz plik konfiguracji lub plików. Jako minimum konieczne jest posiadanie "commandToExecute".

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcjonalnie można określić ustawienia w poleceniu jako ciąg w formacie JSON. Dzięki temu konfiguracji należy określić podczas wykonywania i bez osobnego pliku konfiguracji.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Przykłady interfejsu wiersza polecenia platformy Azure

#### <a name="public-configuration-with-script-file"></a>Publiczna Konfiguracja przy użyciu pliku skryptu

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Usługa Azure polecenia interfejsu wiersza polecenia:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Konfiguracja publiczna z żadnego pliku skryptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Usługa Azure polecenia interfejsu wiersza polecenia:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Pliki konfiguracji publiczne i chronione

Plik konfiguracji publicznego umożliwia określenie pliku skryptu identyfikatora URI. Plik konfiguracji chronionych umożliwia określenie polecenia do uruchomienia.

Plik konfiguracji publicznej:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Plik konfiguracji chronionych:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Usługa Azure polecenia interfejsu wiersza polecenia:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Po uruchomieniu rozszerzenia niestandardowego skryptu skryptu jest utworzony lub pobrany do katalogu, który jest podobny do poniższego przykładu. Dane wyjściowe polecenia jest także zapisywane do tego katalogu w `stdout` i `stderr` plików.

```bash
/var/lib/waagent/custom-script/download/0/
```

Rozwiązywanie problemów, najpierw sprawdź w dzienniku agenta systemu Linux, upewnij się, rozszerzenie zostanie wykonane, sprawdź:

```bash
/var/log/waagent.log 
```

Wykonywanie rozszerzenia należy szukać, będzie on wyglądać mniej więcej tak:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Niektóre informacje, które należy zwrócić uwagę:
1. Włącz to podczas uruchamiania polecenia.
2. Pobierania związana z oczekiwaną Pobieranie pakietu rozszerzenia CustomScript z platformy Azure, nie pliki skryptów określone w fileUris.


Rozszerzenie skryptu platformy Azure generuje dziennik, który można znaleźć tutaj:

```bash
/var/log/azure/custom-script/handler.log
```

Powinien wyglądać do wykonywania pojedynczych, będzie on wyglądać mniej więcej tak:
```text
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```
W tym miejscu możesz zobaczyć:
* Włącz uruchamianie polecenia jest ten dziennik
* Ustawienia przekazane do rozszerzenia
* Rozszerzenie, pobierania pliku i wynik tego obiektu.
* Polecenia są uruchamiane i wynik.

Możesz również pobrać stanu wykonywania rozszerzenia niestandardowego skryptu za pomocą wiersza polecenia platformy Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Dane wyjściowe wyglądają podobnie jak następujący tekst:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Kolejne kroki
Aby wyświetlić kod, bieżących problemach i wersji, zobacz [repozytorium niestandardowego skryptu — rozszerzenie linux](https://github.com/Azure/custom-script-extension-linux).

