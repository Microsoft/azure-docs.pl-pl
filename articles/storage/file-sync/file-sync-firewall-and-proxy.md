---
title: Azure File Sync lokalne ustawienia zapory i serwera proxy | Microsoft Docs
description: Informacje Azure File Sync o lokalnych ustawieniach serwera proxy i zapory. Przejrzyj szczegóły konfiguracji dla portów, sieci i specjalnych połączeń z platformą Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b2c77c20bfb6fff60f2242d1ac2dad7b3fc9f6fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796894"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Ustawienia serwera proxy i zapory usługi Azure File Sync
Azure File Sync serwery lokalne z usługą Azure Files, umożliwiając synchronizację wielu lokacji i funkcje warstw w chmurze. W związku z tym serwer lokalnie musi być połączony z Internetem. Administrator IT musi wybrać najlepszą ścieżkę dla serwera, aby uzyskać dostęp do usług w chmurze platformy Azure.

Ten artykuł zawiera szczegółowe informacje o określonych wymaganiach i opcjach dostępnych w celu pomyślnego i bezpiecznego połączenia serwera z Azure File Sync.

Zalecamy [przeczytanie Azure File Sync zagadnienia dotyczące sieci](file-sync-networking-overview.md) przed przeczytaniem tego przewodnika.

## <a name="overview"></a>Omówienie
Azure File Sync działa jako usługa orkiestracji między systemem Windows Server, twoim udziałem plików platformy Azure i kilkoma innymi usługami platformy Azure w celu synchronizowania danych zgodnie z opisem w grupie synchronizacji. Aby Azure File Sync działały prawidłowo, należy skonfigurować serwery do komunikowania się z następującymi usługami platformy Azure:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Usługi uwierzytelniania

> [!Note]  
> Agent Azure File Sync w systemie Windows Server inicjuje wszystkie żądania do usług w chmurze, co powoduje, że trzeba uwzględniać tylko ruch wychodzący z perspektywy zapory. <br /> Żadna usługa platformy Azure nie inicjuje połączenia z Azure File Sync agenta.

## <a name="ports"></a>Porty
Azure File Sync przenosi dane plików i metadane wyłącznie za pośrednictwem protokołu HTTPS i wymaga otwartego ruchu wychodzącego na porcie 443.
W związku z tym cały ruch jest szyfrowany.

## <a name="networks-and-special-connections-to-azure"></a>Sieci i połączenia specjalne z platformą Azure
Agent Azure File Sync nie ma żadnych wymagań dotyczących specjalnych kanałów, takich jak [usługa ExpressRoute](../../expressroute/expressroute-introduction.md)itp. do platformy Azure.

Azure File Sync będzie działać przy użyciu dowolnych dostępnych środków, które umożliwiają dostęp do platformy Azure, automatycznie dostosowując się do różnych cech sieci, takich jak przepustowość, opóźnienie, a także oferując kontrolę administratora w celu dostrajania. Nie wszystkie funkcje są obecnie dostępne. Jeśli chcesz skonfigurować określone zachowanie, daj nam znać za pośrednictwem Azure Files [UserVoice.](https://feedback.azure.com/forums/217298-storage?category_id=180670)

## <a name="proxy"></a>Serwer proxy
Azure File Sync obsługuje ustawienia serwera proxy specyficzne dla aplikacji i komputera.

**Ustawienia serwera proxy specyficzne dla aplikacji umożliwiają** konfigurację serwera proxy specjalnie Azure File Sync ruchu. Ustawienia serwera proxy specyficzne dla aplikacji są obsługiwane w wersji 4.0.1.0 lub nowszej agenta i można je skonfigurować podczas instalacji agenta lub za pomocą polecenia cmdlet programu Set-StorageSyncProxyConfiguration PowerShell.

Polecenia programu PowerShell służące do konfigurowania ustawień serwera proxy specyficznych dla aplikacji:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Jeśli na przykład serwer proxy wymaga uwierzytelnienia przy użyciu nazwy użytkownika i hasła, uruchom następujące polecenia programu PowerShell:

```powershell
# IP address or name of the proxy server.
$Address="127.0.0.1"  

# The port to use for the connection to the proxy.
$Port=8080

# The user name for a proxy.
$UserName="user_name" 

# Please type or paste a string with a password for the proxy.
$SecurePassword = Read-Host -AsSecureString

$Creds = New-Object System.Management.Automation.PSCredential ($UserName, $SecurePassword)

# Please verify that you have entered the password correctly.
Write-Host $Creds.GetNetworkCredential().Password

Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"

Set-StorageSyncProxyConfiguration -Address $Address -Port $Port -ProxyCredential $Creds
```
**Ustawienia serwera proxy dla całej** maszyny są niewidoczne Azure File Sync agenta, ponieważ cały ruch serwera jest przekierowyny przez serwer proxy.

Aby skonfigurować ustawienia serwera proxy dla maszyny, wykonaj następujące czynności: 

1. Konfigurowanie ustawień serwera proxy dla aplikacji .NET 

   - Edytuj te dwa pliki:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Dodaj sekcję <system.net> w plikach machine.config (poniżej <system.serviceModel> sekcji).  Zmień adres 127.0.01:8888 na adres IP i port serwera proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Ustawianie ustawień serwera proxy WinHTTP 

   > [!Note]  
   > Istnieje kilka metod (WPAD, plik PAC, netsh itp.) do konfigurowania systemu Windows Server do korzystania z serwera proxy. Poniższe kroki obejmują sposób konfigurowania ustawień serwera proxy przy użyciu narzędzia netsh, ale obsługiwana jest każda metoda wymieniona w dokumentacji Konfigurowanie ustawień serwera proxy w systemie [Windows.](https://docs.microsoft.com/troubleshoot/windows-server/networking/configure-proxy-server-settings)


   - Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień lub w programie PowerShell, aby wyświetlić istniejące ustawienie serwera proxy:   

     netsh winhttp show proxy

   - Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień lub w programie PowerShell, aby ustawić ustawienie serwera proxy (zmień wartość 127.0.01:8888 na adres IP i port serwera proxy):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Uruchom ponownie usługę agenta synchronizacji magazynu, uruchamiając następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień lub w programie PowerShell: 

      net stop filesyncsvc

      Uwaga: Usługa agenta synchronizacji magazynu (filesyncsvc) zostanie automatycznie uruchamiana po zatrzymaniu.

## <a name="firewall"></a>Firewall
Jak wspomniano w poprzedniej sekcji, port 443 musi być otwarty dla ruchu wychodzącego. Na podstawie zasad w centrum danych, gałęzi lub regionie może być konieczne lub wymagane dalsze ograniczenie ruchu przez ten port do określonych domen.

W poniższej tabeli opisano domeny wymagane do komunikacji:

| Usługa | Punkt końcowy chmury publicznej | Azure Government punkt końcowy | Użycie |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Każde wywołanie użytkownika (takie jak program PowerShell) przechodzi do/za pośrednictwem tego adresu URL, w tym początkowego wywołania rejestracji serwera. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager muszą być wykonane przez uwierzytelnionego użytkownika. Aby osiągnąć sukces, ten adres URL jest używany do uwierzytelniania użytkowników. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | W ramach wdrażania Azure File Sync zostanie utworzona główna Azure Active Directory subskrypcji. Ten adres URL jest w tym celu używany. Ten podmiot zabezpieczeń służy do delegowania minimalnego zestawu praw do usługi Azure File Sync service. Użytkownik wykonujący początkową konfigurację Azure File Sync musi być uwierzytelnionym użytkownikiem z uprawnieniami właściciela subskrypcji. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Użyj adresu URL publicznego punktu końcowego. | Ten adres URL jest uzyskiwany przez bibliotekę uwierzytelniania usługi Active Directory używaną przez interfejs Azure File Sync rejestracji serwera do logowania administratora. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Gdy serwer pobiera plik, ten serwer wykonuje ten ruch danych wydajniej podczas bezpośredniej rozmowy z udziałem plików platformy Azure na koncie magazynu. Serwer ma klucz sygnatury dostępu współdzielonego, który zezwala tylko na dostęp do docelowego udziału plików. |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | Po początkowej rejestracji serwera serwer otrzymuje regionalny adres URL dla wystąpienia Azure File Sync w tym regionie. Serwer może używać adresu URL do bezpośredniej i wydajnej komunikacji z wystąpieniem obsługym jego synchronizacji. |
| **Microsoft PKI** |  https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | Po zainstalowaniu Azure File Sync klienta adres URL PKI jest używany do pobierania certyfikatów pośrednich wymaganych do komunikowania się z usługą Azure File Sync i udziałem plików platformy Azure. Adres URL OCSP służy do sprawdzania stanu certyfikatu. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | Po zainstalowaniu Azure File Sync agenta Microsoft Update url są używane do pobierania Azure File Sync aktualizacji agenta. |

> [!Important]
> Zezwalając na ruch &ast; do afs.azure.net, ruch jest możliwy tylko do usługi synchronizacji. Nie ma żadnych innych usługi firmy Microsoft tej domeny.
> Zezwalając na ruch do one.microsoft.com, ruch do więcej niż tylko usługi synchronizacji &ast; jest możliwy z serwera. W poddomenach usługi firmy Microsoft dostępnych jest o wiele więcej aplikacji.

Jeśli rozszerzenie .afs.azure.net lub .one.microsoft.com jest zbyt szerokie, można ograniczyć komunikację serwera, zezwalając na komunikację tylko jawnie wystąpieniom regionalnym usługi &ast; &ast; Azure Files Sync. Wystąpienia do wybrania zależą od regionu usługi synchronizacji magazynu, w którym wdrożono i zarejestrowano serwer. Ten region w poniższej tabeli ma nazwę "Podstawowy adres URL punktu końcowego".

Ze względu na ciągłość działania i odzyskiwanie po awarii (BCDR, business continuity and disaster recovery) można było określić udziały plików platformy Azure na koncie magazynu globalnie nadmiarowego (GRS). W takim przypadku udziały plików platformy Azure przejdą w tryb fail over w sparowanych regionach w przypadku długotrwałej awarii regionalnej. Azure File Sync używa tych samych par regionalnych co magazyn. Dlatego jeśli używasz kont magazynu GRS, musisz włączyć dodatkowe adresy URL, aby umożliwić serwerowi srozmowę ze sparowany region na potrzeby Azure File Sync. W poniższej tabeli jest to "region sparowany". Ponadto istnieje adres URL profilu usługi Traffic Manager, który również należy włączyć. Dzięki temu ruch sieciowy może być bezproblemowo ponownie przekierowywana do sparowanego regionu w przypadku pracy awaryjnej i nosi nazwę "Adres URL odnajdywania" w poniższej tabeli.

| Chmura  | Region (Region) | Adres URL podstawowego punktu końcowego | Region sparowany | Adres URL odnajdywania |
|--------|--------|----------------------|---------------|---------------|
| Publiczne |Australia Wschodnia | https: \/ /australiaeast01.afs.azure.net<br>https: \/ /kailani-aue.one.microsoft.com | Australia Południowo-Wschodnia | https: \/ /tm-australiaeast01.afs.azure.net<br>https: \/ /tm-kailani-aue.one.microsoft.com |
| Publiczne |Australia Południowo-Wschodnia | https: \/ /australiasoutheast01.afs.azure.net<br>https: \/ /kailani-aus.one.microsoft.com | Australia Wschodnia | https: \/ /tm-australiasoutheast01.afs.azure.net<br>https: \/ /tm-kailani-aus.one.microsoft.com |
| Publiczne | Brazylia Południowa | https: \/ /brazilsouth01.afs.azure.net | South Central US | https: \/ /tm-brazilsouth01.afs.azure.net |
| Publiczne | Kanada Środkowa | https: \/ /canadacentral01.afs.azure.net<br>https: \/ /kailani-cac.one.microsoft.com | Kanada Wschodnia | https: \/ /tm-canadacentral01.afs.azure.net<br>https: \/ /tm-kailani-cac.one.microsoft.com |
| Publiczne | Kanada Wschodnia | https: \/ /canadaeast01.afs.azure.net<br>https: \/ /kailani-cae.one.microsoft.com | Kanada Środkowa | https: \/ /tm-canadaeast01.afs.azure.net<br>https: \/ /tm-kailani.cae.one.microsoft.com |
| Publiczne | Indie Środkowe | https: \/ /centralindia01.afs.azure.net<br>https: \/ /kailani-cin.one.microsoft.com | Indie Południowe | https: \/ /tm-centralindia01.afs.azure.net<br>https: \/ /tm-kailani-cin.one.microsoft.com |
| Publiczne | Central US | https: \/ /centralus01.afs.azure.net<br>https: \/ /kailani-cus.one.microsoft.com | Wschodnie stany USA 2 | https: \/ /tm-centralus01.afs.azure.net<br>https: \/ /tm-kailani-cus.one.microsoft.com |
| Publiczne | Azja Wschodnia | https: \/ /eastasia01.afs.azure.net<br>https: \/ /kailani11.one.microsoft.com | Southeast Asia | https: \/ /tm-eastasia01.afs.azure.net<br>https: \/ /tm-kailani11.one.microsoft.com |
| Publiczne | East US | https: \/ /eastus01.afs.azure.net<br>https: \/ /kailani1.one.microsoft.com | Zachodnie stany USA | https: \/ /tm-eastus01.afs.azure.net<br>https: \/ /tm-kailani1.one.microsoft.com |
| Publiczne | Wschodnie stany USA 2 | https: \/ /eastus201.afs.azure.net<br>https: \/ /kailani-ess.one.microsoft.com | Central US | https: \/ /tm-eastus201.afs.azure.net<br>https: \/ /tm-kailani-ess.one.microsoft.com |
| Publiczne | Niemcy Północne | https: \/ /germanynorth01.afs.azure.net | Niemcy Zachodnio-środkowe | https: \/ /tm-germanywestcentral01.afs.azure.net |
| Publiczne | Niemcy Zachodnio-środkowe | https: \/ /germanywestcentral01.afs.azure.net | Niemcy Północne | https: \/ /tm-germanynorth01.afs.azure.net |
| Publiczne | Japonia Wschodnia | https: \/ /japaneast01.afs.azure.net | Japonia Zachodnia | https: \/ /tm-japaneast01.afs.azure.net |
| Publiczne | Japonia Zachodnia | https: \/ /japanwest01.afs.azure.net | Japonia Wschodnia | https: \/ /tm-japanwest01.afs.azure.net |
| Publiczne | Korea Środkowa | https: \/ /koreacentral01.afs.azure.net/ | Korea Południowa | https: \/ /tm-koreacentral01.afs.azure.net/ |
| Publiczne | Korea Południowa | https: \/ /koreasouth01.afs.azure.net/ | Korea Środkowa | https: \/ /tm-koreasouth01.afs.azure.net/ |
| Publiczne | Północno-środkowe stany USA | https: \/ /northcentralus01.afs.azure.net | South Central US | https: \/ /tm-northcentralus01.afs.azure.net |
| Publiczne | Europa Północna | https: \/ /northeurope01.afs.azure.net<br>https: \/ /kailani7.one.microsoft.com | West Europe | https: \/ /tm-northeurope01.afs.azure.net<br>https: \/ /tm-kailani7.one.microsoft.com |
| Publiczne | South Central US | https: \/ /southcentralus01.afs.azure.net | Północno-środkowe stany USA | https: \/ /tm-southcentralus01.afs.azure.net |
| Publiczne | Indie Południowe | https: \/ /southindia01.afs.azure.net<br>https: \/ /kailani-sin.one.microsoft.com | Indie Środkowe | https: \/ /tm-southindia01.afs.azure.net<br>https: \/ /tm-kailani-sin.one.microsoft.com |
| Publiczne | Southeast Asia | https: \/ /southeastasia01.afs.azure.net<br>https: \/ /kailani10.one.microsoft.com | Azja Wschodnia | https: \/ /tm-southeastasia01.afs.azure.net<br>https: \/ /tm-kailani10.one.microsoft.com |
| Publiczne | Korea Północna | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net | Szwajcaria Zachodnia | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net |
| Publiczne | Europa Zachodnia | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net | Korea Północna | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net |
| Publiczne | Południowe Zjednoczone Królestwo | https: \/ /uksouth01.afs.azure.net<br>https: \/ /kailani-uks.one.microsoft.com | Zachodnie Zjednoczone Królestwo | https: \/ /tm-uksouth01.afs.azure.net<br>https: \/ /tm-kailani-uks.one.microsoft.com |
| Publiczne | Zachodnie Zjednoczone Królestwo | https: \/ /ukwest01.afs.azure.net<br>https: \/ /kailani-ukw.one.microsoft.com | Południowe Zjednoczone Królestwo | https: \/ /tm-ukwest01.afs.azure.net<br>https: \/ /tm-kailani-ukw.one.microsoft.com |
| Publiczne | Zachodnio-środkowe stany USA | https: \/ /westcentralus01.afs.azure.net | Zachodnie stany USA 2 | https: \/ /tm-westcentralus01.afs.azure.net |
| Publiczne | West Europe | https: \/ /westeurope01.afs.azure.net<br>https: \/ /kailani6.one.microsoft.com | Europa Północna | https: \/ /tm-westeurope01.afs.azure.net<br>https: \/ /tm-kailani6.one.microsoft.com |
| Publiczne | Zachodnie stany USA | https: \/ /westus01.afs.azure.net<br>https: \/ /kailani.one.microsoft.com | East US | https: \/ /tm-westus01.afs.azure.net<br>https: \/ /tm-kailani.one.microsoft.com |
| Publiczne | Zachodnie stany USA 2 | https: \/ /westus201.afs.azure.net | Zachodnio-środkowe stany USA | https: \/ /tm-westus201.afs.azure.net |
| Instytucje rządowe | US Gov Arizona | https: \/ /usgovarizona01.afs.azure.us | US Gov Teksas | https: \/ /tm-usgovarizona01.afs.azure.us |
| Instytucje rządowe | US Gov Teksas | https: \/ /usgovtexas01.afs.azure.us | US Gov Arizona | https: \/ /tm-usgovtexas01.afs.azure.us |

- Jeśli używasz kont magazynu lokalnie nadmiarowego (LRS) lub strefowo nadmiarowego (ZRS), wystarczy włączyć adres URL wymieniony w obszarze "Podstawowy adres URL punktu końcowego".

- Jeśli używasz kont magazynu globalnie nadmiarowego (GRS), włącz trzy adresy URL.

**Przykład:** Wdrożysz usługę synchronizacji magazynu w usłudze `"West US"` i zarejestrujesz w nim serwer. Adresy URL umożliwiające serwerowi komunikowanie się w tym przypadku to:

> - https: \/ /westus01.afs.azure.net (podstawowy punkt końcowy: Zachodnie stany USA)
> - https: \/ /eastus01.afs.azure.net (sparowany region trybu fail over: Wschodnie usa)
> - https: \/ /tm-westus01.afs.azure.net (adres URL odnajdywania regionu podstawowego)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Lista zezwalań dla Azure File Sync adresów IP
Azure File Sync obsługuje użycie [tagów usługi](../../virtual-network/service-tags-overview.md), które reprezentują grupę prefiksów adresów IP dla danej usługi platformy Azure. Tagi usług umożliwiają tworzenie reguł zapory umożliwiających komunikację z Azure File Sync usługą. Tag usługi dla Azure File Sync to `StorageSyncService` .

Jeśli używasz usługi Azure File Sync platformie Azure, możesz użyć nazwy tagu usługi bezpośrednio w sieciowej grupie zabezpieczeń, aby zezwolić na ruch. Aby dowiedzieć się więcej o tym, jak to zrobić, zobacz [Sieciowe grupy zabezpieczeń](../../virtual-network/network-security-groups-overview.md).

Jeśli używasz lokalnego Azure File Sync, możesz użyć interfejsu API tagu usługi, aby uzyskać określone zakresy adresów IP dla listy zezwalań zapory. Istnieją dwie metody uzyskiwania tych informacji:

- Bieżąca lista zakresów adresów IP dla wszystkich usług platformy Azure, które obsługują tagi usług, jest publikowana co tydzień w Centrum pobierania Microsoft w postaci dokumentu JSON. Każda chmura platformy Azure ma własny dokument JSON z zakresami adresów IP odpowiednimi dla tej chmury:
    - [Publiczna platforma Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Wersja platformy Azure dla administracji USA](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Chiny na platformie Azure](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure (Niemcy)](https://www.microsoft.com/download/details.aspx?id=57064)
- Interfejs API odnajdywania tagów usług (wersja zapoznawcza) umożliwia programowe pobieranie bieżącej listy tagów usługi. W wersji zapoznawczej interfejs API odnajdywania tagów usług może zwracać informacje, które są mniej aktualne niż informacje zwrócone z dokumentów JSON opublikowanych w Centrum pobierania Microsoft. Powierzchni interfejsu API można używać zgodnie z preferencjami automatyzacji:
    - [Interfejs API REST](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Interfejs wiersza polecenia platformy Azure](/cli/azure/network#az_network_list_service_tags)

Ponieważ interfejs API odnajdywania tagów usług nie jest aktualizowany tak często, jak dokumenty JSON publikowane w Centrum pobierania Microsoft, zalecamy użycie dokumentu JSON w celu zaktualizowania listy zezwalań zapory lokalnej. Można to zrobić w następujący sposób:

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

Następnie możesz zaktualizować zaporę przy użyciu zakresów adresów IP `$ipAddressRanges` w programie . Sprawdź witrynę internetową zapory/urządzenia sieciowego, aby uzyskać informacje na temat sposobu aktualizowania zapory.

## <a name="test-network-connectivity-to-service-endpoints"></a>Testowanie łączności sieciowej z punktami końcowymi usługi
Po zarejestrowaniu serwera w usłudze Azure File Sync polecenia cmdlet Test-StorageSyncNetworkConnectivity i ServerRegistration.exe mogą służyć do testowania komunikacji ze wszystkimi punktami końcowymi (adresami URL) specyficznym dla tego serwera. To polecenie cmdlet może pomóc w rozwiązywaniu problemów, gdy nieukończona komunikacja uniemożliwia serwerowi pełną pracę z usługą Azure File Sync i może służyć do dostrajania konfiguracji serwera proxy i zapory.

Aby uruchomić test łączności sieciowej, zainstaluj agenta Azure File Sync w wersji 9.1 lub nowszej i uruchom następujące polecenia programu PowerShell:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Podsumowanie i ograniczenie ryzyka
Listy znajdujące się wcześniej w tym dokumencie zawierają adresy URL, z Azure File Sync się obecnie komunikują. Zapory muszą mieć możliwość zezwalania na ruch wychodzący do tych domen. Firma Microsoft stara się aktualizować tę listę.

Skonfigurowanie reguł zapory ograniczających domenę może być miarą w celu zwiększenia bezpieczeństwa. Jeśli są używane te konfiguracje zapory, należy pamiętać, że adresy URL zostaną dodane, a nawet mogą ulec zmianie w czasie. Okresowo sprawdzaj ten artykuł.

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrażania usługi Azure File Sync](file-sync-planning.md)
- [Wdrażanie usługi Azure File Sync](file-sync-deployment-guide.md)
- [Monitorowanie usługi Azure File Sync](file-sync-monitoring.md)
