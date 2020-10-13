---
title: Azure File Sync lokalne ustawienia zapory i serwera proxy | Microsoft Docs
description: Informacje na temat Azure File Sync lokalnych ustawień serwera proxy i zapory. Przejrzyj szczegóły konfiguracji portów, sieci i połączeń specjalnych z platformą Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e4f011d9286a0685f1b091b930155db969407423
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87903718"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Ustawienia serwera proxy i zapory usługi Azure File Sync
Azure File Sync nawiązuje połączenie z serwerami lokalnymi w celu Azure Files, włączając synchronizację z obsługą wielolokacją i funkcjami obsługi warstw w chmurze. W związku z tym serwer lokalny musi być połączony z Internetem. Administrator IT musi zdecydować najlepszą ścieżkę serwera, aby uzyskać dostęp do usług Azure Cloud Services.

W tym artykule zawarto szczegółowe informacje o określonych wymaganiach i opcjach dostępnych w celu pomyślnego i bezpiecznego połączenia Twojego serwera z Azure File Sync.

Zalecamy zapoznanie się z [zagadnieniami dotyczącymi sieci Azure File Sync](storage-sync-files-networking-overview.md) przed przeczytaniem tego przewodnika.

## <a name="overview"></a>Omówienie
Azure File Sync pełni rolę usługi aranżacji między serwerem systemu Windows, udziałem plików platformy Azure i kilkoma innymi usługami platformy Azure, aby synchronizować dane zgodnie z opisem w grupie synchronizacji. Aby program Azure File Sync działał prawidłowo, należy skonfigurować serwery do komunikowania się z następującymi usługami platformy Azure:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Usługi uwierzytelniania

> [!Note]  
> Agent Azure File Sync w systemie Windows Server inicjuje wszystkie żądania do usług w chmurze, które w wyniku uwzględniają tylko ruch wychodzący z perspektywy zapory. <br /> Żadna usługa platformy Azure nie inicjuje połączenia z agentem Azure File Sync.

## <a name="ports"></a>Porty
Azure File Sync przenosi dane plików i metadane wyłącznie za pośrednictwem protokołu HTTPS i wymaga otwartego portu 443.
W wyniku tego cały ruch jest szyfrowany.

## <a name="networks-and-special-connections-to-azure"></a>Sieci i specjalne połączenia z platformą Azure
Agent Azure File Sync nie ma wymagań dotyczących specjalnych kanałów, takich jak [ExpressRoute](../../expressroute/expressroute-introduction.md)itp., na platformę Azure.

Azure File Sync będzie działała za pośrednictwem dowolnych środków, które umożliwiają dostęp do platformy Azure, automatyczne dostosowanie do różnych właściwości sieci, takich jak przepustowość, opóźnienie, a także oferowanie kontroli administracyjnej w celu dostosowania. W tej chwili nie wszystkie funkcje są dostępne. Jeśli chcesz skonfigurować określone zachowanie, daj nam znać za pośrednictwem [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Serwer proxy
Azure File Sync obsługuje ustawienia serwera proxy specyficzne dla aplikacji i komputera.

**Ustawienia serwera proxy specyficzne dla aplikacji** umożliwiają konfigurowanie serwera proxy przeznaczonego dla Azure File Sync ruchu sieciowego. Ustawienia serwera proxy specyficzne dla aplikacji są obsługiwane w programie Agent w wersji 4.0.1.0 lub nowszej i można je skonfigurować podczas instalacji agenta lub przy użyciu polecenia cmdlet programu Set-StorageSyncProxyConfiguration PowerShell.

Polecenia programu PowerShell służące do konfigurowania ustawień serwera proxy specyficznych dla aplikacji:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Ustawienia serwera proxy dla maszyn** są niewidoczne dla agenta Azure File Sync, ponieważ cały ruch serwera jest kierowany przez serwer proxy.

Aby skonfigurować ustawienia serwera proxy dla maszyny, wykonaj następujące czynności: 

1. Konfigurowanie ustawień serwera proxy dla aplikacji platformy .NET 

   - Edytuj te dwa pliki:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Dodaj sekcję <system.net> w plikach machine.config (poniżej sekcji <system. serviceModel>).  Zmień 127.0.01:8888 na adres IP i port serwera proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Ustawianie ustawień serwera proxy WinHTTP 

   - Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień lub programie PowerShell, aby wyświetlić istniejące ustawienie serwera proxy:   

     netsh WinHTTP show proxy

   - Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień lub w programie PowerShell, aby ustawić ustawienie serwera proxy (Zmień 127.0.01:8888 na adres IP i port serwera proxy):  

     netsh WinHTTP Set proxy 127.0.0.1:8888

3. Uruchom ponownie usługę agenta synchronizacji magazynu, uruchamiając następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień lub programie PowerShell: 

      polecenie net stop filesyncsvc

      Uwaga: usługa agenta synchronizacji magazynu (filesyncsvc) zostanie uruchomiona ponownie po zatrzymaniu.

## <a name="firewall"></a>Zapora
Jak wspomniano w poprzedniej sekcji, port 443 musi być otwarty dla ruchu wychodzącego. W oparciu o zasady w centrum danych, rozgałęzieniu lub regionie, może być konieczne lub wymagane dalsze ograniczenie ruchu przez ten port do określonych domen.

W poniższej tabeli opisano wymagane domeny do komunikacji:

| Usługa | Punkt końcowy chmury publicznej | Azure Government punkt końcowy | Użycie |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Każde wywołanie użytkownika (na przykład program PowerShell) przechodzi do/za pomocą tego adresu URL, łącznie z początkowym wywołaniem rejestracji serwera. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Wywołania Azure Resource Manager muszą być wykonywane przez uwierzytelnionego użytkownika. Aby pomyślnie, ten adres URL jest używany do uwierzytelniania użytkowników. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | W ramach wdrażania Azure File Sync zostanie utworzona jednostka usługi w Azure Active Directory subskrypcji. Ten adres URL jest używany dla tego programu. Ten podmiot zabezpieczeń jest używany do delegowania minimalnego zestawu praw do usługi Azure File Sync. Użytkownik wykonujący początkową konfigurację Azure File Sync musi być uwierzytelnionym użytkownikiem z uprawnieniami właściciela subskrypcji. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Użyj publicznego adresu URL punktu końcowego. | Do tego adresu URL uzyskuje się dostęp za pośrednictwem biblioteki uwierzytelniania Active Directory, która jest używana przez interfejs użytkownika rejestracji serwera Azure File Sync do logowania się do administratora. |
| **Azure Storage** | &ast;. core.windows.net | &ast;. core.usgovcloudapi.net | Gdy serwer pobiera plik, serwer przeprowadzi bardziej wydajne przenoszenie danych podczas rozmowy bezpośrednio z udziałem plików platformy Azure na koncie magazynu. Serwer ma klucz sygnatury dostępu współdzielonego, który zezwala tylko na dostęp do udziału plików. |
| **Azure File Sync** | &ast;. one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | Po początkowej rejestracji serwera serwer otrzymuje regionalny adres URL dla wystąpienia usługi Azure File Sync w tym regionie. Serwer może używać adresu URL do bezpośredniego i wydajnego komunikowania się z wystąpieniem obsługującym jego synchronizację. |
| **Infrastruktura PKI firmy Microsoft** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Po zainstalowaniu agenta Azure File Sync, adres URL infrastruktury PKI jest używany do pobierania certyfikatów pośrednich wymaganych do komunikowania się z usługą Azure File Sync i udziałem plików platformy Azure. Adres URL protokołu OCSP służy do sprawdzania stanu certyfikatu. |

> [!Important]
> Gdy zezwalasz na ruch do &ast; . AFS.Azure.NET, ruch jest możliwy tylko dla usługi synchronizacji. Nie ma innych usług firmy Microsoft korzystających z tej domeny.
> W przypadku zezwolenia na ruch do &ast; . one.Microsoft.com ruch do więcej niż tylko usługi synchronizacji jest możliwy z serwera. W poddomenach poddomen jest dostępnych wiele usług firmy Microsoft.

Jeśli &ast; . AFS.Azure.NET lub &ast; . one.Microsoft.com jest zbyt szeroki, można ograniczyć komunikację serwera, zezwalając na komunikację tylko z jawnymi wystąpieniami regionalnymi usługi synchronizacji Azure Files. Jakie wystąpienia są zależne od regionu usługi synchronizacji magazynu wdrożonej i zarejestrowanej na serwerze. Ten region jest nazywany "podstawowym adresem URL punktu końcowego" w poniższej tabeli.

Ze względu na ciągłość działania i odzyskiwanie po awarii (BCDR), możesz określić udziały plików platformy Azure na koncie magazynu globalnie nadmiarowy (GRS). W takim przypadku udziały plików platformy Azure zostaną przełączone w tryb failover do sparowanego regionu w przypadku długotrwałej awarii regionalnej. Azure File Sync używa tych samych regionalnych par jako magazynu. W przypadku korzystania z kont magazynu GRS należy włączyć dodatkowe adresy URL, aby umożliwić serwerowi komunikowanie się z sparowanym regionem Azure File Sync. Poniższa tabela wywołuje ten "sparowany region". Ponadto istnieje adres URL profilu Menedżera ruchu, który musi zostać włączony. Dzięki temu ruch sieciowy może być bezproblemowo kierowany do sparowanego regionu w przypadku przełączenia w tryb failover i nosi nazwę "adres URL odnajdywania" w poniższej tabeli.

| Chmura  | Region | Podstawowy adres URL punktu końcowego | Region sparowany | Adres URL odnajdywania |
|--------|--------|----------------------|---------------|---------------|
| Public |Australia Wschodnia | https: \/ /australiaeast01.AFS.Azure.NET<br>https: \/ /Kailani-Aue.one.Microsoft.com | Australia Południowo-Wschodnia | https: \/ /TM-australiaeast01.AFS.Azure.NET<br>https: \/ /TM-Kailani-Aue.one.Microsoft.com |
| Public |Australia Południowo-Wschodnia | https: \/ /australiasoutheast01.AFS.Azure.NET<br>https: \/ /Kailani-aus.one.Microsoft.com | Australia Wschodnia | https: \/ /TM-australiasoutheast01.AFS.Azure.NET<br>https: \/ /TM-Kailani-aus.one.Microsoft.com |
| Public | Brazil South | https: \/ /brazilsouth01.AFS.Azure.NET | South Central US | https: \/ /TM-brazilsouth01.AFS.Azure.NET |
| Public | Kanada Środkowa | https: \/ /canadacentral01.AFS.Azure.NET<br>https: \/ /Kailani-CAC.one.Microsoft.com | Kanada Wschodnia | https: \/ /TM-canadacentral01.AFS.Azure.NET<br>https: \/ /TM-Kailani-CAC.one.Microsoft.com |
| Public | Kanada Wschodnia | https: \/ /canadaeast01.AFS.Azure.NET<br>https: \/ /Kailani-CAE.one.Microsoft.com | Kanada Środkowa | https: \/ /TM-canadaeast01.AFS.Azure.NET<br>https: \/ /TM-Kailani.CAE.one.Microsoft.com |
| Public | Indie Środkowe | https: \/ /centralindia01.AFS.Azure.NET<br>https: \/ /Kailani-CIN.one.Microsoft.com | Indie Południowe | https: \/ /TM-centralindia01.AFS.Azure.NET<br>https: \/ /TM-Kailani-CIN.one.Microsoft.com |
| Public | Central US | https: \/ /centralus01.AFS.Azure.NET<br>https: \/ /Kailani-CUS.one.Microsoft.com | Wschodnie stany USA 2 | https: \/ /TM-centralus01.AFS.Azure.NET<br>https: \/ /TM-Kailani-CUS.one.Microsoft.com |
| Public | Azja Wschodnia | https: \/ /eastasia01.AFS.Azure.NET<br>https: \/ /kailani11.one.Microsoft.com | Southeast Asia | https: \/ /TM-eastasia01.AFS.Azure.NET<br>https: \/ /TM-kailani11.one.Microsoft.com |
| Public | East US | https: \/ /eastus01.AFS.Azure.NET<br>https: \/ /kailani1.one.Microsoft.com | Zachodnie stany USA | https: \/ /TM-eastus01.AFS.Azure.NET<br>https: \/ /TM-kailani1.one.Microsoft.com |
| Public | Wschodnie stany USA 2 | https: \/ /eastus201.AFS.Azure.NET<br>https: \/ /Kailani-ESS.one.Microsoft.com | Central US | https: \/ /TM-eastus201.AFS.Azure.NET<br>https: \/ /TM-Kailani-ESS.one.Microsoft.com |
| Public | Japan East | https: \/ /japaneast01.AFS.Azure.NET | Japonia Zachodnia | https: \/ /TM-japaneast01.AFS.Azure.NET |
| Public | Japonia Zachodnia | https: \/ /japanwest01.AFS.Azure.NET | Japan East | https: \/ /TM-japanwest01.AFS.Azure.NET |
| Public | Korea Środkowa | https: \/ /koreacentral01.AFS.Azure.NET/ | Korea Południowa | https: \/ /TM-koreacentral01.AFS.Azure.NET/ |
| Public | Korea Południowa | https: \/ /koreasouth01.AFS.Azure.NET/ | Korea Środkowa | https: \/ /TM-koreasouth01.AFS.Azure.NET/ |
| Public | Północno-środkowe stany USA | https: \/ /northcentralus01.AFS.Azure.NET | South Central US | https: \/ /TM-northcentralus01.AFS.Azure.NET |
| Public | Europa Północna | https: \/ /northeurope01.AFS.Azure.NET<br>https: \/ /kailani7.one.Microsoft.com | West Europe | https: \/ /TM-northeurope01.AFS.Azure.NET<br>https: \/ /TM-kailani7.one.Microsoft.com |
| Public | South Central US | https: \/ /southcentralus01.AFS.Azure.NET | Północno-środkowe stany USA | https: \/ /TM-southcentralus01.AFS.Azure.NET |
| Public | Indie Południowe | https: \/ /southindia01.AFS.Azure.NET<br>https: \/ /Kailani-Sin.one.Microsoft.com | Indie Środkowe | https: \/ /TM-southindia01.AFS.Azure.NET<br>https: \/ /TM-Kailani-Sin.one.Microsoft.com |
| Public | Southeast Asia | https: \/ /southeastasia01.AFS.Azure.NET<br>https: \/ /kailani10.one.Microsoft.com | Azja Wschodnia | https: \/ /TM-southeastasia01.AFS.Azure.NET<br>https: \/ /TM-kailani10.one.Microsoft.com |
| Public | Południowe Zjednoczone Królestwo | https: \/ /uksouth01.AFS.Azure.NET<br>https: \/ /Kailani-UKS.one.Microsoft.com | Zachodnie Zjednoczone Królestwo | https: \/ /TM-uksouth01.AFS.Azure.NET<br>https: \/ /TM-Kailani-UKS.one.Microsoft.com |
| Public | Zachodnie Zjednoczone Królestwo | https: \/ /ukwest01.AFS.Azure.NET<br>https: \/ /Kailani-UKW.one.Microsoft.com | Południowe Zjednoczone Królestwo | https: \/ /TM-ukwest01.AFS.Azure.NET<br>https: \/ /TM-Kailani-UKW.one.Microsoft.com |
| Public | Zachodnio-środkowe stany USA | https: \/ /westcentralus01.AFS.Azure.NET | Zachodnie stany USA 2 | https: \/ /TM-westcentralus01.AFS.Azure.NET |
| Public | West Europe | https: \/ /westeurope01.AFS.Azure.NET<br>https: \/ /kailani6.one.Microsoft.com | Europa Północna | https: \/ /TM-westeurope01.AFS.Azure.NET<br>https: \/ /TM-kailani6.one.Microsoft.com |
| Public | Zachodnie stany USA | https: \/ /westus01.AFS.Azure.NET<br>https: \/ /Kailani.one.Microsoft.com | East US | https: \/ /TM-westus01.AFS.Azure.NET<br>https: \/ /TM-Kailani.one.Microsoft.com |
| Public | Zachodnie stany USA 2 | https: \/ /westus201.AFS.Azure.NET | Zachodnio-środkowe stany USA | https: \/ /TM-westus201.AFS.Azure.NET |
| Instytucje rządowe | US Gov Arizona | https: \/ /usgovarizona01.AFS.Azure.us | US Gov Teksas | https: \/ /TM-usgovarizona01.AFS.Azure.us |
| Instytucje rządowe | US Gov Teksas | https: \/ /usgovtexas01.AFS.Azure.us | US Gov Arizona | https: \/ /TM-usgovtexas01.AFS.Azure.us |

- W przypadku korzystania z kont magazynu lokalnie nadmiarowego (LRS) lub strefy nadmiarowe (ZRS) należy włączyć tylko adres URL wymieniony w obszarze "podstawowy adres URL punktu końcowego".

- Jeśli używasz kont magazynu GRS (Globally nadmiarowe), Włącz trzy adresy URL.

**Przykład:** Należy wdrożyć usługę synchronizacji magazynu w `"West US"` systemie i zarejestrować serwer. Adresy URL umożliwiające serwerowi komunikowanie się w tym przypadku są następujące:

> - https: \/ /westus01.AFS.Azure.NET (podstawowy punkt końcowy: zachodnie stany USA)
> - https: \/ /eastus01.AFS.Azure.NET (sparowany region w trybie failover: Wschodnie stany USA)
> - https: \/ /TM-westus01.AFS.Azure.NET (adres URL odnajdywania regionu podstawowego)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Lista dozwolonych adresów IP Azure File Sync
Azure File Sync obsługuje używanie [tagów usługi](../../virtual-network/service-tags-overview.md), które reprezentują grupę prefiksów adresów IP dla danej usługi platformy Azure. Za pomocą tagów usługi można tworzyć reguły zapory, które umożliwiają komunikację z usługą Azure File Sync. Tag usługi dla Azure File Sync ma wartość `StorageSyncService` .

Jeśli używasz Azure File Sync w ramach platformy Azure, możesz użyć nazwy tagu usługi bezpośrednio w sieciowej grupie zabezpieczeń, aby zezwolić na ruch. Aby dowiedzieć się więcej o tym, jak to zrobić, zobacz [Network Security Groups](../../virtual-network/security-overview.md).

Jeśli używasz Azure File Sync lokalnie, możesz użyć interfejsu API znacznika usługi, aby uzyskać określone zakresy adresów IP dla listy dozwolonych dla zapory. Istnieją dwie metody uzyskiwania tych informacji:

- Bieżąca lista zakresów adresów IP dla wszystkich usług platformy Azure obsługujących znaczniki usług jest publikowana co tydzień w centrum pobierania Microsoft w formie dokumentu JSON. Każda Chmura platformy Azure ma własny dokument JSON z zakresami adresów IP istotnymi dla tej chmury:
    - [Usługa Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Wersja platformy Azure dla administracji USA](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Chiny platformy Azure](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure (Niemcy)](https://www.microsoft.com/download/details.aspx?id=57064)
- Interfejs API odnajdowania tagów usługi (wersja zapoznawcza) umożliwia programistyczne pobieranie bieżącej listy tagów usługi. W wersji zapoznawczej interfejs API odnajdowania tagów usługi może zwracać informacje, które są mniej aktualne niż zwracane przez informacje z dokumentów JSON opublikowanych w centrum pobierania Microsoft. Możesz użyć powierzchni interfejsu API na podstawie preferencji automatyzacji:
    - [Interfejs API REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

Ponieważ interfejs API odnajdowania tagów usługi nie jest aktualizowany tak często, jak dokumenty JSON opublikowane w centrum pobierania Microsoft, zalecamy użycie dokumentu JSON w celu zaktualizowania listy dozwolonych zapór lokalnych. Można to zrobić w następujący sposób:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

Następnie można użyć zakresów adresów IP w programie, `$ipAddressRanges` Aby zaktualizować zaporę. Aby uzyskać informacje na temat aktualizowania zapory, Sprawdź witrynę sieci Web zapory/urządzenia sieciowego.

## <a name="test-network-connectivity-to-service-endpoints"></a>Testowanie łączności sieciowej z punktami końcowymi usługi
Gdy serwer jest zarejestrowany w usłudze Azure File Sync, Test-StorageSyncNetworkConnectivity polecenie cmdlet i ServerRegistration.exe może służyć do testowania komunikacji ze wszystkimi punktami końcowymi (URL) specyficznymi dla tego serwera. To polecenie cmdlet może pomóc w rozwiązywaniu problemów w przypadku niekompletnej komunikacji uniemożliwia serwerowi pełną pracę z Azure File Sync i można go użyć do dostosowania konfiguracji serwera proxy i zapory.

Aby uruchomić test łączności sieciowej, Zainstaluj agenta Azure File Sync w wersji 9,1 lub nowszej i uruchom następujące polecenia programu PowerShell:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Podsumowanie i ograniczenie ryzyka
Listy znajdujące się wcześniej w tym dokumencie zawierają adresy URL, Azure File Sync obecnie komunikują się z usługą. Zapory muszą mieć możliwość zezwalania na ruch wychodzący do tych domen. Firma Microsoft dokłada starań, aby zachować tę listę.

Skonfigurowanie ograniczania dostępu do domeny reguły zapory może być miarą w celu zwiększenia bezpieczeństwa. Jeśli są używane te konfiguracje zapory, należy pamiętać, że adresy URL zostaną dodane i nawet mogą ulec zmianie w czasie. Sprawdzaj okresowo ten artykuł.

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
