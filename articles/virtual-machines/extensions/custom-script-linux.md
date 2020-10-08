---
title: Uruchamianie niestandardowego rozszerzenia skryptu na maszynach wirtualnych z systemem Linux na platformie Azure
description: Automatyzowanie zadań konfiguracyjnych maszyn wirtualnych z systemem Linux przy użyciu rozszerzenia niestandardowego skryptu v2
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: 2dbfc2173f6631aff2d65c770a5204bbd72d3ed1
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818810"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Używanie rozszerzenia niestandardowego skryptu platformy Azure w wersji 2 z maszynami wirtualnymi z systemem Linux
Rozszerzenie skryptu niestandardowego wersja 2 pobiera i uruchamia skrypty na maszynach wirtualnych platformy Azure. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalacji oprogramowania lub innych zadań związanych z konfiguracją/zarządzaniem. Skrypty można pobrać z usługi Azure Storage lub innej dostępnej lokalizacji w Internecie lub można je udostępnić do środowiska uruchomieniowego rozszerzenia. 

Rozszerzenie niestandardowego skryptu integruje się z szablonami Azure Resource Manager. Można go również uruchomić przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub usługi Azure Virtual Machines REST.

W tym artykule szczegółowo przedstawiono sposób korzystania z rozszerzenia niestandardowego skryptu z interfejsu wiersza polecenia platformy Azure oraz uruchamiania rozszerzenia przy użyciu szablonu Azure Resource Manager. Ten artykuł zawiera również procedurę rozwiązywania problemów z systemami Linux.


Istnieją dwa rozszerzenia niestandardowego skryptu systemu Linux:
* Wersja 1 — Microsoft. OSTCExtensions. CustomScriptForLinux
* Wersja 2 — Microsoft. Azure. Extensions. CustomScript

Przełącz nowe i istniejące wdrożenia, aby zamiast tego użyć nowej wersji 2. Nowa wersja jest przeznaczona do użycia bezpośrednio w miejsce starszej. Dlatego migracja jest tak łatwa jak zmiana nazwy i wersji — nie trzeba zmieniać konfiguracji rozszerzenia.


### <a name="operating-system"></a>System operacyjny

Rozszerzenie niestandardowego skryptu dla systemu Linux zostanie uruchomione na obsługiwanym rozszerzeniu system operacyjny rozszerzenia, aby uzyskać więcej informacji, zobacz ten [artykuł](../linux/endorsed-distros.md).

### <a name="script-location"></a>Lokalizacja skryptu

Możesz użyć rozszerzenia, aby użyć poświadczeń usługi Azure Blob Storage w celu uzyskania dostępu do usługi Azure Blob Storage. Alternatywnie lokalizacja skryptu może być dowolna, w której maszyna wirtualna może kierować do tego punktu końcowego, na przykład w usłudze GitHub, na wewnętrznym serwerze plików itp.

### <a name="internet-connectivity"></a>Łączność z Internetem
Jeśli musisz pobrać skrypt zewnętrzny, taki jak GitHub lub Azure Storage, należy otworzyć dodatkową zaporę/porty sieciowej grupy zabezpieczeń. Jeśli na przykład skrypt znajduje się w usłudze Azure Storage, możesz zezwolić na dostęp za pomocą tagów usługi Azure sieciowej grupy zabezpieczeń dla [magazynu](../../virtual-network/security-overview.md#service-tags).

Jeśli skrypt znajduje się na serwerze lokalnym, może być konieczne otwarcie dodatkowych portów zapory/sieciowych grup zabezpieczeń.

### <a name="tips-and-tricks"></a>Porady i wskazówki
* Najwyższy współczynnik błędów dla tego rozszerzenia jest spowodowany błędami składni w skrypcie. Sprawdź, czy skrypt działa bezbłędnie, a także umieść dodatkowe rejestrowanie w skrypcie, aby ułatwić wyszukiwanie miejsca, w którym wystąpił błąd.
* Pisz skrypty idempotentne, aby w razie ich ponownego uruchomienia przez przypadek więcej niż jeden raz nie nastąpiły zmiany systemu.
* Upewnij się, że skrypty nie wymagają wprowadzania danych przez użytkownika po ich uruchomieniu.
* Aby skrypt mógł zostać uruchomiony, może być 90 min, co spowoduje niepowodzenie aprowizacji rozszerzenia.
* Nie należy umieszczać ponownych uruchomień wewnątrz skryptu, co spowoduje problemy z innymi zainstalowanymi rozszerzeniami i po ponownym uruchomieniu, rozszerzenie nie będzie kontynuowane po ponownym uruchomieniu. 
* Nie zaleca się uruchamiania skryptu, który spowoduje zatrzymanie lub aktualizację agenta maszyny wirtualnej. Może to spowodować pozostawienie rozszerzenia w stanie przejścia i prowadzić do przekroczenia limitu czasu.
* Jeśli masz skrypt, który spowoduje ponowne uruchomienie komputera, zainstaluj aplikacje i Uruchom skrypty itp. Należy zaplanować ponowny rozruch przy użyciu zadania firmy CRONUS lub użyć narzędzi takich jak DSC lub Chef Puppet.
* Rozszerzenie spowoduje uruchomienie skryptu tylko raz, aby uruchomić skrypt na każdym rozruchu, a następnie użyć [obrazu Cloud-init](../linux/using-cloud-init.md)  i użyć [skryptów dla modułu rozruchu](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) . Alternatywnie możesz użyć skryptu, aby utworzyć systemową jednostkę usługi.
* Do maszyny wirtualnej może być zastosowana tylko jedna wersja rozszerzenia. W celu uruchomienia drugiego skryptu niestandardowego należy usunąć rozszerzenie niestandardowego skryptu i ponownie zastosować je do zaktualizowanego skryptu. 
* Jeśli chcesz zaplanować uruchomienie skryptu, użyj rozszerzenia, aby utworzyć zadanie firmy cronus. 
* W trakcie działania skryptu będziesz widzieć tylko stan „przechodzenie” z witryny Azure Portal lub interfejsu wiersza polecenia. Jeśli chcesz więcej częste aktualizacje stanu uruchomionego skryptu, musisz utworzyć własne rozwiązanie.
* Niestandardowe rozszerzenie skryptu nie obsługuje natywnie serwerów proxy, jednak można użyć narzędzia transferu plików, które obsługuje serwery proxy w skrypcie, na przykład *zwinięcie*. 
* Należy pamiętać, że nie są to domyślne lokalizacje katalogów, od których mogą polegać skrypty lub polecenia, i które obsługują tę logikę.
*  Podczas wdrażania niestandardowego skryptu w wystąpieniach VMSS produkcyjnych zaleca się wdrożenie za pośrednictwem szablonu JSON i przechowywanie konta magazynu skryptów, w którym masz kontrolę nad tokenem sygnatury dostępu współdzielonego. 


## <a name="extension-schema"></a>Schemat rozszerzenia

Konfiguracja rozszerzenia niestandardowego skryptu określa elementy, takie jak lokalizacja skryptu i polecenie do uruchomienia. Tę konfigurację można zapisać w plikach konfiguracji, określić ją w wierszu polecenia lub określić w szablonie Azure Resource Manager. 

Poufne dane można przechowywać w chronionej konfiguracji, która jest szyfrowana i odszyfrowywana tylko wewnątrz maszyny wirtualnej. Konfiguracja chroniona jest przydatna, gdy polecenie wykonywania zawiera wpisy tajne, takie jak hasło.

Te elementy powinny być traktowane jako dane poufne i określone w konfiguracji ustawień chronionych przez rozszerzenia. Dane ustawienia chronionego rozszerzenia maszyny wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
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
       "fileUris": ["https://.."],
       "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> Właściwość managedIdentity **nie może** być używana w połączeniu z właściwościami StorageAccountName lub storageAccountKey

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publisher | Microsoft. COMPUTE. Extensions | ciąg |
| typ | CustomScript | ciąg |
| typeHandlerVersion | 2.1 | int |
| fileUris (np.) | `https://github.com/MyProject/Archive/MyPythonScript.py` | array |
| Sekcji commandtoexecute (np.) | MyPythonScript.py Python \<my-param1> | ciąg |
| skrypt | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo = | ciąg |
| skipDos2Unix (np.) | fałsz | boolean |
| Sygnatura czasowa (np.) | 123456789 | 32-bitowa liczba całkowita |
| storageAccountName (np.) | examplestorageacct | ciąg |
| storageAccountKey (np.) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | ciąg |
| managedIdentity (np.) | {} lub {"clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232"} lub {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | Obiekt JSON |

### <a name="property-value-details"></a>Szczegóły wartości właściwości
* `apiVersion`: Najbardziej aktualne apiVersion można znaleźć za pomocą [Eksplorator zasobów](https://resources.azure.com/) lub z interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenie `az provider list -o json`
* `skipDos2Unix`: (opcjonalne, wartość logiczna) Pomiń dos2unix konwersję adresów URL lub skryptu plików opartych na skryptach.
* `timestamp` (opcjonalnie, 32-bitową liczbę całkowitą) Użyj tego pola tylko do wyzwalania ponownego uruchomienia skryptu przez zmianę wartości tego pola.  Dopuszczalna jest dowolna wartość całkowita; musi on być inny niż Poprzednia wartość.
* `commandToExecute`: (**wymagane** , jeśli skrypt nie jest ustawiony, String) skrypt punktu wejścia do wykonania. Użyj tego pola zamiast tego, jeśli polecenie zawiera wpisy tajne, takie jak hasła.
* `script`: (**wymagane** , jeśli sekcji commandtoexecute nie został ustawiony, String) skrypt kodowany algorytmem Base64 (i opcjonalnie gzip'ed) wykonywany przez/bin/sh.
* `fileUris`: (opcjonalne, tablica ciągów) adresy URL dla plików do pobrania.
* `storageAccountName`: (opcjonalnie, ciąg) nazwa konta magazynu. W przypadku określenia poświadczeń magazynu wszystkie `fileUris` muszą być adresami URL dla obiektów blob platformy Azure.
* `storageAccountKey`: (opcjonalnie, String) klucz dostępu konta magazynu
* `managedIdentity`: (opcjonalnie obiekt JSON) [zarządzana tożsamość](../../active-directory/managed-identities-azure-resources/overview.md) do pobierania plików
  * `clientId`: (opcjonalnie, String) identyfikator klienta zarządzanej tożsamości
  * `objectId`: (opcjonalnie, String) identyfikator obiektu tożsamości zarządzanej


Następujące wartości można ustawić w ustawieniach publicznych lub chronionych, rozszerzenie odrzuci każdą konfigurację, w której poniższe wartości są ustawione zarówno w ustawieniach publicznych, jak i chronionych.
* `commandToExecute`
* `script`
* `fileUris`

Korzystanie z ustawień publicznych może być przydatne w przypadku debugowania, ale zdecydowanie zaleca się używanie ustawień chronionych.

Ustawienia publiczne są wysyłane w postaci zwykłego tekstu do maszyny wirtualnej, na której skrypt zostanie wykonany.  Ustawienia chronione są szyfrowane przy użyciu klucza znanego tylko na platformie Azure i maszynie wirtualnej. Ustawienia są zapisywane na maszynie wirtualnej w miarę ich wysyłania, tj. Jeśli ustawienia zostały zaszyfrowane, są zapisywane w postaci zaszyfrowanej na maszynie wirtualnej. Certyfikat używany do odszyfrowywania zaszyfrowanych wartości jest przechowywany na maszynie wirtualnej i używany do odszyfrowywania ustawień (w razie potrzeby) w czasie wykonywania.

#### <a name="property-skipdos2unix"></a>Właściwość: skipDos2Unix

Wartość domyślna to false, co oznacza, że **jest** wykonywana konwersja dos2unix.

Poprzednia wersja CustomScript, Microsoft. OSTCExtensions. CustomScriptForLinux, automatycznie konwertuje pliki DOS do plików systemu UNIX przez przetłumaczenie `\r\n` na `\n` . To tłumaczenie nadal istnieje i jest domyślnie włączone. Ta konwersja jest stosowana do wszystkich plików pobranych z fileUris lub ustawienia skryptu w oparciu o dowolne z poniższych kryteriów.

* Jeśli rozszerzenie jest jednym z `.sh` , `.txt` , `.py` lub `.pl` zostanie przekonwertowane. Ustawienie skryptu będzie zawsze zgodne z tymi kryteriami, ponieważ zakłada się, że skrypt jest wykonywany przy użyciu/bin/sh i jest zapisywany jako script.sh na maszynie wirtualnej.
* Jeśli plik zaczyna się od `#!` .

Konwersję dos2unix można pominąć, ustawiając wartość skipDos2Unix na true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Właściwość: skrypt

CustomScript obsługuje wykonywanie skryptu zdefiniowanego przez użytkownika. Ustawienia skryptu umożliwiające połączenie sekcji commandtoexecute i fileUris w jedno ustawienie. Zamiast konieczności konfigurowania pliku do pobrania z usługi Azure Storage lub GitHub, można po prostu zakodować skrypt jako ustawienie. Skrypt może służyć do wymiany sekcji commandtoexecute i fileUris.

Skrypt **musi** być kodowany algorytmem Base64.  Skrypt można **Opcjonalnie** gzip'ed. Ustawienia skryptu można używać w ustawieniach publicznych lub chronionych. Maksymalny rozmiar danych parametru skryptu to 256 KB. Jeśli skrypt przekracza ten rozmiar, nie zostanie on wykonany.

Na przykład, mając następujący skrypt zapisany w pliku/script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Poprawne ustawienie skryptu CustomScript zostanie skonstruowane przez pobranie danych wyjściowych następującego polecenia.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skrypt można opcjonalnie gzip'ed, aby bardziej zmniejszyć rozmiar (w większości przypadków). (CustomScript Autowykrywanie użycia kompresji gzip).

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript używa następującego algorytmu do wykonania skryptu.

 1. Potwierdź, że długość wartości skryptu nie przekracza 256 KB.
 1. kodowanie w formacie base64 wartości skryptu
 1. _próba_ gunzip zdekodowaną wartość Base64
 1. Napisz zdekodowaną (i opcjonalnie nieskompresowaną) wartość na dysk (/var/lib/waagent/Custom-Script/#/Script.sh)
 1. Wykonaj skrypt przy użyciu _/bin/sh-c/var/lib/waagent/Custom-Script/#/Script.sh.

####  <a name="property-managedidentity"></a>Właściwość: managedIdentity
> [!NOTE]
> Ta właściwość **musi** być określona tylko w ustawieniach chronionych.

CustomScript (wersja 2,1 lub nowszy) obsługuje [tożsamość zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md) do pobierania plików z adresów URL określonych w ustawieniu "fileUris". Umożliwia CustomScript dostęp do prywatnych obiektów blob lub kontenerów usługi Azure Storage bez konieczności przekazywania wpisów tajnych, takich jak tokeny SAS lub klucze kont magazynu.

Aby można było użyć tej funkcji, użytkownik musi dodać tożsamość przypisaną przez [system](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) lub [przypisanej do użytkownika](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) do maszyny wirtualnej lub VMSS, gdzie oczekiwano CustomScript, i [przyznać zarządzanej tożsamości dostęp do kontenera lub obiektu BLOB usługi Azure Storage](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Aby użyć tożsamości przypisanej do systemu na docelowej maszynie wirtualnej/VMSS, ustaw wartość pola "managedidentity" na pusty obiekt JSON. 

> Przykład:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Aby użyć tożsamości przypisanej przez użytkownika na docelowej maszynie wirtualnej/VMSS, należy skonfigurować pole "managedidentity" z IDENTYFIKATORem klienta lub IDENTYFIKATORem obiektu tożsamości zarządzanej.

> Przykłady:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> Właściwość managedIdentity **nie może** być używana w połączeniu z właściwościami StorageAccountName lub storageAccountKey

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Schemat JSON opisany w poprzedniej sekcji można użyć w szablonie Azure Resource Manager, aby uruchomić rozszerzenie niestandardowego skryptu podczas wdrażania szablonu Azure Resource Manager. Przykładowy szablon, który zawiera rozszerzenie niestandardowego skryptu, można znaleźć w witrynie [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
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
>W tych nazwach właściwości jest rozróżniana wielkość liter. Aby uniknąć problemów z wdrażaniem, użyj nazw, jak pokazano poniżej.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Jeśli używasz interfejsu wiersza polecenia platformy Azure do uruchamiania niestandardowego rozszerzenia skryptu, Utwórz plik lub pliki konfiguracyjne. Musisz mieć co najmniej wartość "sekcji commandtoexecute".

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcjonalnie można określić ustawienia w poleceniu jako ciąg sformatowany w formacie JSON. Pozwala to określić konfigurację podczas wykonywania i bez oddzielnego pliku konfiguracji.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Przykłady interfejsu wiersza polecenia platformy Azure

#### <a name="public-configuration-with-script-file"></a>Konfiguracja publiczna z plikiem skryptu

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Po uruchomieniu rozszerzenia skryptu niestandardowego skrypt jest tworzony lub pobierany do katalogu podobnego do poniższego przykładu. Dane wyjściowe polecenia są również zapisywane w tym katalogu w `stdout` `stderr` plikach i.

```bash
/var/lib/waagent/custom-script/download/0/
```

Aby rozwiązać problem, najpierw sprawdź dziennik agenta systemu Linux, upewnij się, że rozszerzenie zostało uruchomione, sprawdź:

```bash
/var/log/waagent.log 
```

Należy wyszukać wykonanie rozszerzenia, będzie wyglądać następująco:

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

Niektóre punkty do uwagi:
1. Wartość enable jest uruchamiana, gdy polecenie zostanie uruchomione.
2. Pobieranie odnosi się do pobierania pakietu rozszerzenia CustomScript z platformy Azure, a nie plików skryptów określonych w fileUris.


Rozszerzenie skryptu platformy Azure generuje dziennik, który można znaleźć tutaj:

```bash
/var/log/azure/custom-script/handler.log
```

Należy poszukać poszczególnych wykonań, co będzie wyglądać następująco:

```output
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
* Uruchamianie polecenia enable jest tym dziennikiem
* Ustawienia przesłane do rozszerzenia
* Rozszerzenie pobiera plik i wynik.
* Polecenie jest uruchamiane i wynik.

Możesz również pobrać stan wykonywania rozszerzenia niestandardowego skryptu, w tym rzeczywiste argumenty przekazane jako przy `commandToExecute` użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Dane wyjściowe wyglądają następująco:

```output
[
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/customscript",
    "resourceGroup": "rgname",
    "settings": {
      "commandToExecute": "sh script.sh > ",
      "fileUris": [
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh",
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh"
      ]
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "2.0",
    "virtualMachineExtensionType": "CustomScript"
  },
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/OmsAgentForLinux",
    "instanceView": null,
    "location": "eastus",
    "name": "OmsAgentForLinux",
    "protectedSettings": null,
    "provisioningState": "Succeeded",
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "resourceGroup": "rgname",
    "settings": {
      "workspaceId": "workspaceid"
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "1.0",
    "virtualMachineExtensionType": "OmsAgentForLinux"
  }
]
```

## <a name="next-steps"></a>Następne kroki
Aby wyświetlić kod, bieżące problemy i wersje, zobacz [repozytorium niestandardowe-Script-Extension-Linux](https://github.com/Azure/custom-script-extension-linux).
