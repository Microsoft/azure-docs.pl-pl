---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 98866a4f06df0380d52d1aee3eede8aa2f70aaed
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588137"
---
Usługa Azure Instance Metadata Service (IMDS) zawiera informacje o aktualnie uruchomionych wystąpieniach maszyn wirtualnych. Umożliwia zarządzanie maszynami wirtualnymi i ich konfigurowanie.
Te informacje obejmują sku, magazyn, konfiguracje sieci i nadchodzące zdarzenia konserwacji. Aby uzyskać pełną listę dostępnych danych, zobacz [Podsumowanie kategorii punktów końcowych](#endpoint-categories).

Usługi IMDS są dostępne dla uruchomionych wystąpień maszyn wirtualnych i wystąpień zestawu skalowania maszyn wirtualnych. Wszystkie punkty końcowe obsługują maszyny wirtualne utworzone i zarządzane przy [użyciu](/rest/api/resources/)Azure Resource Manager . Tylko kategoria Attested i część Sieć kategorii Wystąpienie obsługują maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania. Attestowany punkt końcowy robi to tylko w ograniczonym zakresie.

IMDS to interfejs API REST dostępny pod dobrze znanym, nieroutowalnym adresem IP ( `169.254.169.254` ). Dostęp do niego można uzyskać tylko z poziomu maszyny wirtualnej. Komunikacja między maszyną wirtualną a usługą IMDS nigdy nie opuszcza hosta.
Klienci HTTP mogą pomijać internetowe proxy na maszynie wirtualnej podczas wykonywania zapytań o usługę IMDS i traktować je `169.254.169.254` tak samo jak [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Użycie

### <a name="access-azure-instance-metadata-service"></a>Uzyskiwanie dostępu do usługi Azure Instance Metadata Service

Aby uzyskać dostęp do usługi [](/rest/api/resources/) IMDS, utwórz maszynę wirtualną z Azure Resource Manager lub Azure Portal [i](https://portal.azure.com)skorzystaj z poniższych przykładów.
Aby uzyskać więcej przykładów, [zobacz Przykłady metadanych wystąpienia platformy Azure.](https://github.com/microsoft/azureimds)

Oto przykładowy kod, który pobiera wszystkie metadane dla wystąpienia. Aby uzyskać dostęp do określonego źródła danych, zobacz [Kategorie punktów końcowych,](#endpoint-categories) aby uzyskać przegląd wszystkich dostępnych funkcji.

**Żądanie**

> [!IMPORTANT]
> W tym przykładzie pomijane są proxy. Podczas **wykonywania zapytań** o IMDS należy pominąć proxy. Aby [uzyskać dodatkowe informacje, zobacz](#proxies) proxy.

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Następująca przykładowa odpowiedź jest dość wydrukowana, aby była czytelna.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Zabezpieczenia i uwierzytelnianie

Adres Instance Metadata Service jest dostępny tylko z poziomu uruchomionego wystąpienia maszyny wirtualnej pod adresem IP bez routowania. Maszyny wirtualne są ograniczone do interakcji z metadanymi/funkcjami, które odnoszą się do siebie. Interfejs API jest tylko http i nigdy nie opuszcza hosta.

Aby zapewnić, że żądania są bezpośrednio przeznaczone dla usług IMDS i zapobiec niezamierzonemu lub niepożądanemu przekierowaniu żądań, żądania:
- **Musi** zawierać nagłówek `Metadata: true`
- Nie **może zawierać** `X-Forwarded-For` nagłówka

Wszystkie żądania, które nie spełniają **obu** tych wymagań, zostaną odrzucone przez usługę.

> [!IMPORTANT]
> ImDS nie **jest kanałem** dla poufnych danych. Interfejs API jest nieuwierzyszany i otwarty dla wszystkich procesów na maszynie wirtualnej. Informacje udostępniane za pośrednictwem tej usługi powinny być traktowane jako informacje udostępnione wszystkim aplikacjom uruchomionym na maszynie wirtualnej.

## <a name="proxies"></a>Serwerów proxy

Usługa IMDS **nie jest** przeznaczona do pracy za serwerem proxy i nie jest obsługiwana. Większość klientów HTTP umożliwia wyłączenie serwerów proxy w żądaniach, a ta funkcja musi być wykorzystywana podczas komunikacji z usługami IMDS. Aby uzyskać szczegółowe informacje, zapoznaj się z dokumentacją klienta.

> [!IMPORTANT]
> Nawet jeśli nie znasz żadnej konfiguracji serwera proxy w swoim środowisku, nadal musisz zastąpić wszystkie domyślne ustawienia **serwera proxy klienta**. Konfiguracje serwera proxy mogą być wykrywane automatycznie, a pominięcie takich konfiguracji naraża na ryzyko awarii, jeśli konfiguracja maszyny zostanie zmieniona w przyszłości.

## <a name="rate-limiting"></a>Rate limiting (Ograniczanie szybkości)

Ogólnie rzecz biorąc, żądania do imds są ograniczone do 5 żądań na sekundę. Żądania przekraczające ten próg zostaną odrzucone z 429 odpowiedziami. Żądania do kategorii [Tożsamość zarządzana](#managed-identity) są ograniczone do 20 żądań na sekundę i 5 żądań współbieżnych.

## <a name="http-verbs"></a>Czasowniki HTTP

Obecnie obsługiwane są następujące czasowniki HTTP:

| Czasownik | Opis |
|------|-------------|
| `GET` | Pobieranie żądanego zasobu

## <a name="parameters"></a>Parametry

Punkty końcowe mogą obsługiwać wymagane i/lub opcjonalne parametry. Aby [uzyskać szczegółowe](#schema) informacje, zobacz schemat i dokumentację konkretnego punktu końcowego.

### <a name="query-parameters"></a>Parametry zapytania

Punkty końcowe IMDS obsługują parametry ciągu zapytania HTTP. Na przykład: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Określa parametry:

| Nazwa | Wartość |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Żądania ze zduplikowanymi nazwami parametrów zapytania zostaną odrzucone.

### <a name="route-parameters"></a>Parametry trasy

W przypadku niektórych punktów końcowych, które zwracają większe obiekty blob JSON, obsługujemy dołączanie parametrów trasy do punktu końcowego żądania w celu odfiltrowania do podzbioru odpowiedzi: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
Parametry odpowiadają indeksom/kluczom, które byłyby używane do chodu do obiektu JSON podczas interakcji z analizą reprezentacji.

Na przykład `/metatadata/instance` zwraca obiekt JSON:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

Jeśli chcemy odfiltrować odpowiedź tylko do właściwości compute, wyślemy żądanie: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Podobnie, jeśli chcemy filtrować do zagnieżdżonych właściwości lub określonego elementu tablicy, będziemy nadal dołączać klucze: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
Filtruje do pierwszego elementu z `Network.interface` właściwości i zwraca:

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> Filtrowanie do węzła liścia `format=json` nie działa. W przypadku tych `format=text` zapytań należy jawnie określić, ponieważ domyślny format to JSON.

## <a name="schema"></a>Schemat

### <a name="data-format"></a>Format danych

Domyślnie funkcja IMDS zwraca dane w formacie JSON ( `Content-Type: application/json` ). Jednak punkty końcowe, które obsługują filtrowanie odpowiedzi (zobacz [Parametry trasy](#route-parameters)), obsługują również format `text` .

Aby uzyskać dostęp do formatu odpowiedzi inne niż domyślny, określ żądany format jako parametr ciągu zapytania w żądaniu. Na przykład:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

W odpowiedziach JSON wszystkie typy pierwotne będą typu , a brakujące lub niestosowalne wartości są zawsze uwzględniane, ale zostaną ustawione `string` na pusty ciąg.

### <a name="versioning"></a>Przechowywanie wersji

Usługa IMDS jest wersjonarowana, a określenie wersji interfejsu API w żądaniu HTTP jest obowiązkowe. Jedynym wyjątkiem od tego wymagania jest punkt końcowy [wersji,](#versions) którego można użyć do dynamicznego pobierania dostępnych wersji interfejsu API.

W związku z dodaniu nowszej wersji można nadal uzyskiwać dostęp do starszych wersji w celu zapewnienia zgodności, jeśli skrypty mają zależności od określonych formatów danych.

Jeśli nie określisz wersji, wystąpi błąd z listą najnowszych obsługiwanych wersji:
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>Obsługiwane wersje interfejsu API
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01
- 2021-01-01

### <a name="swagger"></a>Swagger

Pełna definicja swagger dla imds jest dostępna w: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Dostępność w regionach

Usługa jest ogólnie **dostępna we** wszystkich chmurach platformy Azure.

## <a name="root-endpoint"></a>Główny punkt końcowy

Główny punkt końcowy to `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Kategorie punktów końcowych

Interfejs API IMDS zawiera wiele kategorii punktów końcowych reprezentujących różne źródła danych, z których każda zawiera co najmniej jeden punkt końcowy. Aby uzyskać szczegółowe informacje, zobacz poszczególne kategorie.

| Katalog główny kategorii | Opis | Wprowadzona wersja |
|---------------|-------------|--------------------|
| `/metadata/attested` | Zobacz [Attested Data (Dane attestowane)](#attested-data) | 2018-10-01
| `/metadata/identity` | Zobacz Tożsamość [zarządzana za pośrednictwem usługi IMDS](#managed-identity) | 2018-02-01
| `/metadata/instance` | Zobacz [Instance Metadata (Metadane wystąpienia)](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | Zobacz [Pobieranie Load Balancer danych za pośrednictwem usługi IMDS](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | Zobacz Scheduled Events [za pośrednictwem usługi IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Zobacz [wersje](#versions) | Nie dotyczy

## <a name="versions"></a>Wersje

> [!NOTE]
> Ta funkcja została wydana wraz z wersją 2020-10-01, która jest obecnie w trakcie jej wycofywania i może nie być jeszcze dostępna w każdym regionie.

### <a name="list-api-versions"></a>Lista wersji interfejsu API

Zwraca zestaw obsługiwanych wersji interfejsu API.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parametry

Brak (ten punkt końcowy jest niewersydowany).

#### <a name="response"></a>Reakcja

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Metadane wystąpienia

### <a name="get-vm-metadata"></a>Uzyskiwanie metadanych maszyny wirtualnej

Uwidacznia ważne metadane dla wystąpienia maszyny wirtualnej, w tym obliczenia, sieć i magazyn. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parametry

| Nazwa | Wymagane/Opcjonalne | Opis |
|------|-------------------|-------------|
| `api-version` | Wymagane | Wersja używana do obsługi żądania.
| `format` | Opcjonalnie* | Format ( `json` lub `text` ) odpowiedzi. *Uwaga: może być wymagana w przypadku korzystania z parametrów żądania

Ten punkt końcowy obsługuje filtrowanie odpowiedzi za pomocą [parametrów trasy](#route-parameters).

#### <a name="response"></a>Reakcja

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Podział schematu:

**Środowisko obliczeniowe**

| Dane | Opis | Wprowadzona wersja |
|------|-------------|--------------------|
| `azEnvironment` | Środowisko platformy Azure, w którym działa maszyna wirtualna | 2018-10-01
| `customData` | Ta funkcja jest przestarzała i wyłączona w [uowie IMDS.](#frequently-asked-questions) Został on nadsyłany przez `userData` | 2019-02-01
| `evictionPolicy` | Określa sposób [eksmisji](../articles/virtual-machines/spot-vms.md) maszyny wirtualnej usługi Spot. | 2020-12-01
| `isHostCompatibilityLayerVm` | Określa, czy maszyna wirtualna działa w warstwie zgodności hosta | 2020-06-01
| `licenseType` | Typ licencji dla [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Ta funkcja jest dostępna tylko dla maszyn wirtualnych z obsługą usługi AHB | 2020-09-01
| `location` | Region świadczenia usługi Azure, w których jest uruchomiona maszyna wirtualna | 2017-04-02
| `name` | Nazwa maszyny wirtualnej | 2017-04-02
| `offer` | Informacje o ofercie dla obrazu maszyny wirtualnej i są dostępne tylko dla obrazów wdrożonych z galerii obrazów platformy Azure | 2017-04-02
| `osProfile.adminUsername` | Określa nazwę konta administratora | 2020-07-15
| `osProfile.computerName` | Określa nazwę komputera | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Określa, czy uwierzytelnianie hasłem jest wyłączone. Dotyczy to tylko maszyn wirtualnych z systemem Linux | 2020-10-01
| `osType` | Linux lub Windows | 2017-04-02
| `placementGroupId` | [Grupa umieszczania](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) zestawu skalowania maszyn wirtualnych | 2017-08-01
| `plan` | [Planowanie](/rest/api/compute/virtualmachines/createorupdate#plan) zawierające nazwę, produkt i wydawcę dla maszyny wirtualnej, jeśli jest to Azure Marketplace obrazu | 2018-04-02
| `platformUpdateDomain` |  [Aktualizowanie domeny,](../articles/virtual-machines/availability.md) w która jest uruchomiona maszyna wirtualna | 2017-04-02
| `platformFaultDomain` | [Domena błędów, w](../articles/virtual-machines/availability.md) których jest uruchomiona maszyna wirtualna | 2017-04-02
| `priority` | Priorytet maszyny wirtualnej. Aby [uzyskać](../articles/virtual-machines/spot-vms.md) więcej informacji, zapoznaj się z tematem Maszyny wirtualne spot | 2020-12-01
| `provider` | Dostawca maszyny wirtualnej | 2018-10-01
| `publicKeys` | [Kolekcja kluczy publicznych](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) przypisanych do maszyny wirtualnej i ścieżek | 2018-04-02
| `publisher` | Wydawca obrazu maszyny wirtualnej | 2017-04-02
| `resourceGroupName` | [Grupa zasobów](../articles/azure-resource-manager/management/overview.md) dla maszyny wirtualnej | 2017-08-01
| `resourceId` | W [pełni kwalifikowany](/rest/api/resources/resources/getbyid) identyfikator zasobu | 2019-03-11
| `sku` | Konk o określonej sku dla obrazu maszyny wirtualnej | 2017-04-02
| `securityProfile.secureBootEnabled` | Określa, czy na maszynie wirtualnej jest włączony bezpieczny rozruch UEFI | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Określa, czy maszyna wirtualna Trusted Platform Module wirtualnej (TPM) jest włączona | 2020-06-01
| `storageProfile` | Zobacz profil magazynu poniżej | 2019-06-01
| `subscriptionId` | Subskrypcja platformy Azure dla maszyny wirtualnej | 2017-08-01
| `tags` | [Tagi](../articles/azure-resource-manager/management/tag-resources.md) dla maszyny wirtualnej  | 2017-08-01
| `tagsList` | Tagi sformatowane jako tablica JSON w celu ułatwienia programowej analizy  | 2019-06-04
| `userData` | Zestaw danych określony podczas tworzenia maszyny wirtualnej do użycia podczas aprowizowania lub po jego zakończeniu (zakodowany w formacie Base64)  | 2021-01-01
| `version` | Wersja obrazu maszyny wirtualnej | 2017-04-02
| `vmId` | [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) maszyny wirtualnej | 2017-04-02
| `vmScaleSetName` | [Zestaw skalowania maszyn wirtualnych](../articles/virtual-machine-scale-sets/overview.md) Nazwa zestawu skalowania maszyn wirtualnych | 2017-12-01
| `vmSize` | [Rozmiar maszyny wirtualnej](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Strefa dostępności](../articles/availability-zones/az-overview.md) maszyny wirtualnej | 2017-12-01

**Profil magazynu**

Profil magazynu maszyny wirtualnej jest podzielony na trzy kategorie: odwołanie do obrazu, dysk systemu operacyjnego i dyski danych.

Obiekt odwołania do obrazu zawiera następujące informacje o obrazie systemu operacyjnego:

| Dane | Opis |
|------|-------------|
| `id` | Identyfikator zasobu
| `offer` | Oferta obrazu platformy lub witryny Marketplace
| `publisher` | Wydawca obrazów
| `sku` | Obraz sku
| `version` | Wersja obrazu platformy lub witryny Marketplace

Obiekt dysku systemu operacyjnego zawiera następujące informacje o dysku systemu operacyjnego używanym przez maszynę wirtualną:

| Dane | Opis |
|------|-------------|
| `caching` | Wymagania dotyczące buforowania
| `createOption` | Informacje na temat sposobu tworzenia maszyny wirtualnej
| `diffDiskSettings` | Ustawienia dysku efemery
| `diskSizeGB` | Rozmiar dysku w GB
| `image`   | Wirtualny dysk twardy obrazu użytkownika źródłowego
| `lun`     | Numer jednostki logicznej dysku
| `managedDisk` | Parametry dysku zarządzanego
| `name`    | Nazwa dysku
| `vhd`     | Wirtualny dysk twardy
| `writeAcceleratorEnabled` | Czy funkcja writeAccelerator jest włączona na dysku

Macierz dysków danych zawiera listę dysków danych dołączonych do maszyny wirtualnej. Każdy obiekt dysku danych zawiera następujące informacje:

Dane | Opis |
-----|-------------|
| `caching` | Wymagania dotyczące buforowania
| `createOption` | Informacje na temat sposobu tworzenia maszyny wirtualnej
| `diffDiskSettings` | Ustawienia dysku efemery
| `diskSizeGB` | Rozmiar dysku w GB
| `encryptionSettings` | Ustawienia szyfrowania dysku
| `image` | Wirtualny dysk twardy obrazu użytkownika źródłowego
| `managedDisk` | Parametry dysku zarządzanego
| `name` | Nazwa dysku
| `osType` | Typ systemu operacyjnego uwzględnionego na dysku
| `vhd` | Wirtualny dysk twardy
| `writeAcceleratorEnabled` | Określa, czy na dysku jest włączony program writeAccelerator

**Sieć**

| Dane | Opis | Wprowadzona wersja |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Lokalny adres IPv4 maszyny wirtualnej | 2017-04-02
| `ipv4.publicIpAddress` | Publiczny adres IPv4 maszyny wirtualnej | 2017-04-02
| `subnet.address` | Adres podsieci maszyny wirtualnej | 2017-04-02
| `subnet.prefix` | Prefiks podsieci, przykład 24 | 2017-04-02
| `ipv6.ipAddress` | Lokalny adres IPv6 maszyny wirtualnej | 2017-04-02
| `macAddress` | Adres MAC maszyny wirtualnej | 2017-04-02

### <a name="get-user-data"></a>Uzyskiwanie danych użytkownika

Podczas tworzenia nowej maszyny wirtualnej można określić zestaw danych, które mają być używane podczas aprowizowania maszyny wirtualnej lub po jej zakończeniu, i pobrać go za pośrednictwem usługi IMDS. Aby skonfigurować dane użytkownika, skorzystaj z szablonu szybkiego [startu](https://aka.ms/ImdsUserDataArmTemplate)tutaj. W poniższym przykładzie pokazano, jak pobrać te dane za pomocą imds.

> [!NOTE]
> Ta funkcja jest wydawana wraz z wersją i zależy od aktualizacji platformy Azure, która jest obecnie w trakcie jej wycofywania i może nie być jeszcze dostępna `2021-01-01` w każdym regionie.

> [!NOTE]
> Uwaga o zabezpieczeniach: Program IMDS jest otwarty dla wszystkich aplikacji na maszynie wirtualnej. Poufne dane nie powinny być umieszczane w danych użytkownika.


#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

---


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Przykład 1: Śledzenie maszyny wirtualnej działającej na platformie Azure

Jako dostawca usług możesz wymagać śledzenia liczby maszyn wirtualnych z uruchomionym oprogramowaniem lub agentów, którzy muszą śledzić unikatowość maszyny wirtualnej. Aby uzyskać unikatowy identyfikator maszyny wirtualnej, użyj `vmId` pola z Instance Metadata Service.

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Odpowiedź**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>Przykład 2: umieszczanie różnych replik danych

W niektórych scenariuszach umieszczanie różnych replik danych ma kluczowe znaczenie. Na przykład [umieszczanie replik systemu plików HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) lub umieszczanie kontenerów za pośrednictwem [orkiestratora](https://kubernetes.io/docs/user-guide/node-selection/) może wymagać informacji o tym, że maszyna wirtualna `platformFaultDomain` jest `platformUpdateDomain` uruchomiona.
Możesz również użyć [Strefy dostępności](../articles/availability-zones/az-overview.md) dla wystąpień, aby podejmować te decyzje.
Zapytania o te dane można zapytania bezpośrednio za pośrednictwem usługi IMDS.

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Odpowiedź**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>Przykład 3: uzyskiwanie tagów maszyn wirtualnych

Tagi maszyn wirtualnych są uwzględniane w interfejsie API wystąpienia w punkcie końcowym wystąpienia/obliczeń/tagów.
Tagi mogą zostać zastosowane do maszyny wirtualnej platformy Azure w celu logicznego zorganizowania ich w taksonomię. Tagi przypisane do maszyny wirtualnej można pobrać przy użyciu poniższego żądania.

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Odpowiedź**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

Pole `tags` jest ciągiem z tagami rozdzielonych średnikami. Te dane wyjściowe mogą stanowić problem, jeśli w tagach są używane średniki. Jeśli parser jest napisany w celu programowego wyodrębnienia tagów, należy polegać na `tagsList` polu . Pole `tagsList` jest tablicą JSON bez ograniczników, co ułatwia analizowanie. Listę tagów przypisaną do maszyny wirtualnej można pobrać przy użyciu poniższego żądania.

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Odpowiedź**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>Przykład 4: uzyskiwanie dodatkowych informacji na temat maszyny wirtualnej podczas obsługi

Jako dostawca usług możesz otrzymać połączenie z pomocą techniczną, w którym chcesz uzyskać więcej informacji na temat maszyny wirtualnej. Poproszę klienta o udostępnienie metadanych obliczeń, aby uzyskać podstawowe informacje dla specjalistów pomocy technicznej na temat rodzaju maszyny wirtualnej na platformie Azure.

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Następująca przykładowa odpowiedź jest dość wydrukowana, aby była czytelna.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>Przykład 5. Uzyskiwanie środowiska platformy Azure, w którym działa maszyna wirtualna

Platforma Azure ma różne suwerenne [chmury, takie Azure Government](https://azure.microsoft.com/overview/clouds/government/). Czasami środowisko platformy Azure jest potrzebne, aby podjąć pewne decyzje dotyczące środowiska uruchomieniowego. W poniższym przykładzie pokazano, jak można osiągnąć to zachowanie.

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Odpowiedź**

```
AzurePublicCloud
```

Chmura i wartości środowiska platformy Azure są wymienione tutaj.

| Chmura | Środowisko platformy Azure |
|-------|-------------------|
| [Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure w Chinach — 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>Przykład 6: pobieranie informacji o sieci

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Odpowiedź**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>Przykład 7: pobieranie publicznego adresu IP

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Dane attestowane

### <a name="get-attested-data"></a>Uzyskiwanie danych attestowanych

Usługa IMDS pomaga zagwarantować, że dostarczone dane pochodzą z platformy Azure. Firma Microsoft podpisuje część tych informacji, aby można było potwierdzić, że obraz w Azure Marketplace jest tym, który jest uruchomiony na platformie Azure.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parametry

| Nazwa | Wymagane/Opcjonalne | Opis |
|------|-------------------|-------------|
| `api-version` | Wymagane | Wersja używana do obsługi żądania.
| `nonce` | Opcjonalne | Ciąg 10-cyfrowy, który służy jako kryptograficzny ciąg. Jeśli żadna wartość nie zostanie podany, IMDS używa bieżącego znacznika czasu UTC.

#### <a name="response"></a>Reakcja

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> Ze względu na mechanizm buforowania USŁUGI IMDS może zostać zwrócona wcześniej buforowana wartość typu nonce.

Obiekt blob podpisu jest wersją dokumentu z podpisem [pkcs7.](https://aka.ms/pkcs7) Zawiera certyfikat używany do podpisywania wraz z określonymi szczegółami specyficznym dla maszyny wirtualnej.

W przypadku maszyn wirtualnych utworzonych przy użyciu Azure Resource Manager dokument zawiera elementy , , , służące do tworzenia i wygasania dokumentu oraz informacje o `vmId` `sku` `nonce` `subscriptionId` `timeStamp` planie obrazu. Informacje o planie są wypełniane tylko dla Azure Marketplace obrazów.

W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania wypełnianie jest `vmId` gwarantowane tylko dla typu . Możesz wyodrębnić certyfikat z odpowiedzi i użyć go do potwierdzenia, że odpowiedź jest prawidłowa i pochodzi z platformy Azure.

Zdekodowany dokument zawiera następujące pola:

| Dane | Opis | Wprowadzona wersja |
|------|-------------|--------------------|
| `licenseType` | Typ licencji dla [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Ta funkcja jest dostępna tylko dla maszyn wirtualnych z obsługą usługi AHB. | 2020-09-01
| `nonce` | Ciąg, który może być opcjonalnie dostarczony wraz z żądaniem. Jeśli nie `nonce` został podany, jest używany Uniwersalny czas koordynowany znacznik czasu. | 2018-10-01
| `plan` | Plan [Azure Marketplace obrazów.](/rest/api/compute/virtualmachines/createorupdate#plan) Zawiera identyfikator planu (nazwę), obraz produktu lub ofertę (produkt) i identyfikator wydawcy (wydawcę). | 2018-10-01
| `timestamp.createdOn` | Sygnatura czasowa UTC utworzenia podpisanego dokumentu | 2018-20-01
| `timestamp.expiresOn` | Znacznik czasu UTC wygaśnięcia podpisanego dokumentu | 2018-10-01
| `vmId` | [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) maszyny wirtualnej | 2018-10-01
| `subscriptionId` | Subskrypcja platformy Azure dla maszyny wirtualnej | 2019-04-30
| `sku` | Konkcyjna sku dla obrazu maszyny wirtualnej | 2019-11-01

> [!NOTE]
> W przypadku klasycznych maszyn wirtualnych (innych Azure Resource Manager) wypełnianie tylko identyfikatorów vmId jest gwarantowane.

Przykładowy dokument:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Przykład 1: sprawdzanie, czy maszyna wirtualna jest uruchomiona na platformie Azure

Dostawcy w Azure Marketplace chcą mieć pewność, że ich oprogramowanie jest licencjonowane tylko na platformie Azure. Jeśli ktoś kopiuje wirtualny dysk twardy do środowiska lokalnego, dostawca musi być w stanie to wykryć. Za pośrednictwem usługi IMDS ci dostawcy mogą uzyskać podpisane dane, które gwarantują odpowiedzi tylko z platformy Azure.

> [!NOTE]
> Ten przykład wymaga zainstalowania narzędzia jq.

**Walidacja**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Sprawdź, czy podpis pochodzi z Microsoft Azure i sprawdź łańcuch certyfikatów pod uwagę błędów.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Wyniki**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Sprawdź, czy podpis pochodzi z Microsoft Azure, i sprawdź łańcuch certyfikatów pod uwagę błędów.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> Ze względu na mechanizm buforowania IMDS może zostać zwrócona wcześniej `nonce` buforowana wartość.

Wartość `nonce` w podpisanym dokumencie można porównać, jeśli w początkowym żądaniu podano `nonce` parametr .

> [!NOTE]
> Certyfikat dla chmury publicznej i każdej suwerennej chmury będzie inny.

| Chmura | Certyfikat |
|-------|-------------|
| [Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure w Chinach — 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> Certyfikaty mogą nie mieć dokładnego dopasowania do `metadata.azure.com` chmury publicznej. Z tego powodu walidacja certyfikacji powinna zezwalać na nazwę pospolitą z dowolnej `.metadata.azure.com` poddomeny.

W przypadkach, gdy nie można pobrać certyfikatu pośredniego z powodu ograniczeń sieci podczas walidacji, można przypiąć certyfikat pośredni. Platforma Azure przejmuje certyfikaty, co jest standardową praktyką PKI. Należy zaktualizować przypięte certyfikaty w przypadku przerolowania. Za każdym razem, gdy jest planowana zmiana w celu zaktualizowania certyfikatu pośredniego, blog platformy Azure jest aktualizowany, a klienci platformy Azure są powiadamiani. 

Certyfikaty pośrednie można znaleźć w [repozytorium PKI.](https://www.microsoft.com/pki/mscorp/cps/default.htm) Certyfikaty pośrednie dla każdego z regionów mogą być różne.

> [!NOTE]
> Certyfikat pośredni dla usługi Azure (Chiny) — 21Vianet będzie pochodzić z globalnego głównego urzędu certyfikacji firmy DigiCert, a nie z baltimore.
W przypadku przypiętych certyfikatów pośrednich dla platformy Azure (Chiny) w ramach zmiany urzędu łańcucha głównego należy zaktualizować certyfikaty pośrednie.


## <a name="managed-identity"></a>Tożsamość zarządzana

Tożsamość zarządzaną przypisaną przez system można włączyć na maszynie wirtualnej. Do maszyny wirtualnej można również przypisać co najmniej jedną tożsamość zarządzaną przypisaną przez użytkownika.
Następnie możesz zażądać tokenów dla tożsamości zarządzanych z usługi IMDS. Użyj tych tokenów do uwierzytelniania w innych usługach platformy Azure, takich jak Azure Key Vault.

Aby uzyskać szczegółowe instrukcje dotyczące włączania tej funkcji, [zobacz Uzyskiwanie tokenu dostępu.](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

## <a name="load-balancer-metadata"></a>Load Balancer metadanych
Gdy umieszczasz wystąpienia maszyny wirtualnej lub zestawu maszyn wirtualnych za usługą Azure usługa Load Balancer w warstwie Standardowa, możesz użyć usługi IMDS do pobrania metadanych związanych z usługą równoważenia obciążenia i wystąpieniami. Aby uzyskać więcej informacji, zobacz [Pobieranie informacji dotyczących równoważenia obciążenia.](../articles/load-balancer/instance-metadata-service-load-balancer.md)

## <a name="scheduled-events"></a>Zaplanowane zdarzenia
Stan zaplanowanych zdarzeń można uzyskać przy użyciu usługi IMDS. Następnie użytkownik może określić zestaw akcji do uruchomienia na tych zdarzeniach. Aby uzyskać więcej informacji, zobacz Scheduled events for Linux (Zaplanowane [zdarzenia dla systemu Linux)](../articles/virtual-machines/linux/scheduled-events.md) [lub Scheduled events for Windows (Zaplanowane zdarzenia dla systemu Windows).](../articles/virtual-machines/windows/scheduled-events.md)


## <a name="sample-code-in-different-languages"></a>Przykładowy kod w różnych językach

W poniższej tabeli wymieniono przykłady wywoływania usługi IMDS przy użyciu różnych języków wewnątrz maszyny wirtualnej:

| Język | Przykład |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Przejdź | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Błędy i debugowanie

Jeśli nie znaleziono elementu danych lub źle sformułowane żądanie, Instance Metadata Service zwraca standardowe błędy HTTP. Na przykład:

| Kod stanu HTTP | Przyczyna |
|------------------|--------|
| `200 OK` | Żądanie powiodło się.
| `400 Bad Request` | Brak `Metadata: true` nagłówka lub brakującego `format=json` parametru podczas wykonywania zapytania w węźle liścia
| `404 Not Found` | Żądany element nie istnieje
| `405 Method Not Allowed` | Metoda HTTP (czasownik) nie jest obsługiwana w punkcie końcowym.
| `410 Gone` | Ponów próbę po pewnym czasie przez maksymalnie 70 sekund
| `429 Too Many Requests` | Przekroczono [limity](#rate-limiting) szybkości interfejsu API
| `500 Service Error` | Ponów próbę po pewnym czasie

## <a name="frequently-asked-questions"></a>Często zadawane pytania

- Występuje błąd `400 Bad Request, Required metadata header not specified` . Co to oznacza?
  - ImDS wymaga, aby `Metadata: true` nagłówek został przekazany w żądaniu. Przekazanie tego nagłówka w wywołaniu REST umożliwia dostęp do usług IMDS.

- Dlaczego nie mogę uzyskać informacji o obliczeniach dla mojej maszyny wirtualnej?
  - Obecnie usługi IMDS obsługują tylko wystąpienia utworzone za pomocą Azure Resource Manager.

- Moja maszyna wirtualna została utworzona Azure Resource Manager jakiś czas temu. Dlaczego nie widzę informacji o metadanych obliczeń?
  - Jeśli maszyna wirtualna została utworzona po wrześniu 2016 r., dodaj [tag,](../articles/azure-resource-manager/management/tag-resources.md) aby rozpocząć wyświetlanie metadanych obliczeniowych. Jeśli maszyna wirtualna została utworzona przed wrześniem 2016 r., dodaj lub usuń rozszerzenia lub dyski danych do wystąpienia maszyny wirtualnej, aby odświeżyć metadane.

- Czy dane użytkownika są takie same jak dane niestandardowe?
  - Dane użytkownika oferują funkcje podobne do danych niestandardowych, dzięki czemu można przekazywać własne metadane do wystąpienia maszyny wirtualnej. Różnica polega na tym, że dane użytkownika są pobierane za pośrednictwem usługi IMDS i są trwałe przez cały okres istnienia wystąpienia maszyny wirtualnej. Istniejąca funkcja danych niestandardowych będzie nadal działać zgodnie z opisem w [tym artykule.](https://docs.microsoft.com/azure/virtual-machines/custom-data) Dane niestandardowe można jednak uzyskać tylko za pośrednictwem lokalnego folderu systemowego, a nie za pośrednictwem systemu IMDS.

- Dlaczego nie widzę wszystkich danych wypełnionych dla nowej wersji?
  - Jeśli maszyna wirtualna została utworzona po wrześniu 2016 r., dodaj [tag,](../articles/azure-resource-manager/management/tag-resources.md) aby rozpocząć wyświetlanie metadanych obliczeniowych. Jeśli maszyna wirtualna została utworzona przed wrześniem 2016 r., dodaj lub usuń rozszerzenia lub dyski danych do wystąpienia maszyny wirtualnej, aby odświeżyć metadane.

- Dlaczego występuje błąd `500 Internal Server Error` lub `410 Resource Gone` ?
  - Ponów próbę żądania. Aby uzyskać więcej informacji, zobacz [Obsługa błędów przejściowych.](/azure/architecture/best-practices/transient-faults) Jeśli problem będzie się powtarzać, utwórz problem z pomocą techniczną w Azure Portal dla maszyny wirtualnej.

- Czy będzie to działać w przypadku wystąpień zestawu skalowania maszyn wirtualnych?
  - Tak, usługi IMDS są dostępne dla wystąpień zestawu skalowania maszyn wirtualnych.

- Zaktualizowano tagi w zestawach skalowania maszyn wirtualnych, ale nie są one wyświetlane w wystąpieniach (w przeciwieństwie do maszyn wirtualnych pojedynczego wystąpienia). Czy coś jest nie tak?
  - Obecnie tagi dla zestawów skalowania maszyn wirtualnych są wyświetlane dla maszyny wirtualnej tylko podczas ponownego uruchamiania, ponownego uruchamiania lub zmiany dysku w wystąpieniu.

- Dlaczego nie widzę szczegółowych informacji o sku dla mojej maszyny `instance/compute` wirtualnej?
  - W przypadku obrazów niestandardowych utworzonych na podstawie Azure Marketplace platforma Azure nie zachowuje informacji o sku dla obrazu niestandardowego ani szczegółów dla żadnych maszyn wirtualnych utworzonych na podstawie obrazu niestandardowego. Jest to projektowane i dlatego nie jest on ujmowany w szczegóły maszyny `instance/compute` wirtualnej.

- Dlaczego uchyliło mi się moje żądanie dla mojego wywołania usługi?
  - Wywołania metadanych muszą pochodzić z podstawowego adresu IP przypisanego do podstawowej karty sieciowej maszyny wirtualnej. Ponadto w przypadku zmiany tras w lokalnej tabeli routingu maszyny wirtualnej musi znajdować się trasa dla adresu 169.254.169.254/32.

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. Zrzuć lokalną tabelę routingu i poszukaj wpisu IMDS. Na przykład:
        ```console
        > route print
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
            168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
        169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
        ... (continues) ...
        ```
    1. Sprawdź, czy istnieje trasa `169.254.169.254` dla , i zanotuj odpowiedni interfejs sieciowy (na przykład `172.16.69.7` ).
    1. Zrzuć konfigurację interfejsu i znajdź interfejs, który odpowiada interfejsowi przywoływującemu w tabeli routingu, za notując adres MAC (fizyczny).
        ```console
        > ipconfig /all
        ... (continues) ...
        Ethernet adapter Ethernet:

        Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
        Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
        Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
        DHCP Enabled. . . . . . . . . . . : Yes
        Autoconfiguration Enabled . . . . : Yes
        Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
        IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        ... (continues) ...
        ```
    1. Upewnij się, że interfejs odpowiada podstawowej karty sieciowej maszyny wirtualnej i podstawowemu adresowi IP. Podstawową kartę sieciową i adres IP można znaleźć, patrząc na konfigurację sieci w witrynie Azure Portal lub korzystając z interfejsu wiersza polecenia platformy Azure. Zanotuj prywatne adres IP (i adres MAC, jeśli używasz interfejsu wiersza polecenia). Oto przykład interfejsu wiersza polecenia programu PowerShell:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: wintest767 True 00-0D-3A-E5-1C-C0
        ```
    1. Jeśli nie są zgodne, zaktualizuj tabelę routingu tak, aby docelowo były podstawowa karta sieciowa i adres IP.

    ### <a name="linux"></a>[Linux](#tab/linux/)

    1. Zrzuć lokalną tabelę routingu za pomocą polecenia , takiego jak i poszukaj wpisu `netstat -r` IMDS (np.):
        ```console
        ~$ netstat -r
        Kernel IP routing table
        Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
        default         _gateway        0.0.0.0         UG        0 0          0 eth0
        168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
        169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
        172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
        ```
    1. Sprawdź, czy istnieje trasa `169.254.169.254` dla i zanotuj odpowiedni interfejs sieciowy (np. `eth0` ).
    1. Zrzuć konfigurację interfejsu dla odpowiedniego interfejsu w tabeli routingu (zwróć uwagę na dokładną nazwę pliku konfiguracji może się różnić)
        ```console
        ~$ cat /etc/netplan/50-cloud-init.yaml
        network:
        ethernets:
            eth0:
                dhcp4: true
                dhcp4-overrides:
                    route-metric: 100
                dhcp6: false
                match:
                    macaddress: 00:0d:3a:e4:c7:2e
                set-name: eth0
        version: 2
        ```
    1. Jeśli używasz dynamicznego adresu IP, zanotuj adres MAC. Jeśli używasz statycznego adresu IP, możesz zanotuj wymienione adresy IP i/lub adresy MAC.
    1. Upewnij się, że interfejs odpowiada podstawowej karty sieciowej i podstawowemu adresowi IP maszyny wirtualnej. Podstawową kartę sieciową i adres IP można znaleźć, patrząc na konfigurację sieci w witrynie Azure Portal lub korzystając z interfejsu wiersza polecenia platformy Azure. Zanotuj prywatne adres IP (i adres MAC, jeśli używasz interfejsu wiersza polecenia). Oto przykład interfejsu wiersza polecenia programu PowerShell:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: ipexample606 True 00-0D-3A-E4-C7-2E
        ```
    1. Jeśli nie są zgodne, zaktualizuj tabelę routingu tak, aby była celem podstawowej karty sieciowej/adresu IP.

    ---

- Klaster trybu failover w systemie Windows Server
  - W przypadku wykonywania zapytań o usługę IMDS za pomocą klastra trybu failover czasami konieczne jest dodanie trasy do tabeli routingu. Oto kroki tej procedury:

    1. Otwórz wiersz polecenia z uprawnieniami administratora.

    1. Uruchom następujące polecenie i zanotuj adres interfejsu dla lokalizacji docelowej sieci ( ) w tabeli tras `0.0.0.0` IPv4.

    ```bat
    route print
    ```

    > [!NOTE]
    > Poniższe przykładowe dane wyjściowe są wyprowadzane z maszyny wirtualnej z systemem Windows Server z włączonym klastrem trybu failover. Dla uproszczenia dane wyjściowe zawierają tylko tabelę tras IPv4.

    ```
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
            10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
            10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
    127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
        169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
    169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
            224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
    ```

    Uruchom następujące polecenie i użyj adresu interfejsu dla lokalizacji docelowej sieci ( `0.0.0.0` ), czyli ( ) w tym `10.0.1.10` przykładzie.

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## <a name="support"></a>Pomoc techniczna

Jeśli nie możesz uzyskać odpowiedzi metadanych po wielu próbach, możesz utworzyć problem z pomocą techniczną w Azure Portal.

## <a name="product-feedback"></a>Opinia dotycząca produktu

Opinie i pomysły dotyczące produktów możesz przekazać do naszego kanału opinii użytkowników w Virtual Machines > Instance Metadata Service [tutaj](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627)

## <a name="next-steps"></a>Następne kroki

- [Uzyskiwanie tokenu dostępu dla maszyny wirtualnej](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Zaplanowane zdarzenia dla systemu Linux](../articles/virtual-machines/linux/scheduled-events.md)

- [Zaplanowane zdarzenia dla systemu Windows](../articles/virtual-machines/windows/scheduled-events.md)
