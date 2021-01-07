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
ms.openlocfilehash: ceb560a3f0c56a13b9f8da6c867f513b2b08e59b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97962230"
---
# <a name="azure-instance-metadata-service-imds"></a>Instance Metadata Service platformy Azure (IMDS)

Usługa Azure Instance Metadata Service (IMDS) zawiera informacje o aktualnie uruchomionych wystąpieniach maszyn wirtualnych. Służy do zarządzania maszynami wirtualnymi i ich konfigurowania.
Te informacje obejmują jednostki SKU, magazyn, konfiguracje sieci i nadchodzące zdarzenia konserwacji. Aby uzyskać pełną listę dostępnych danych, zobacz [Podsumowanie kategorii punktów końcowych](#endpoint-categories).

IMDS jest dostępny dla uruchomionych wystąpień maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych. Wszystkie punkty końcowe obsługują maszyny wirtualne utworzone i zarządzane przy użyciu [Azure Resource Manager](/rest/api/resources/). Do obsługi maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania są obsługiwane tylko część zaświadczonej kategorii i sieci. Zaświadczanie punktu końcowego działa tylko w ograniczonym zakresie.

IMDS to interfejs API REST, który jest dostępny z dobrze znanym adresem IP bez obsługi routingu ( `169.254.169.254` ). Możesz uzyskać do niego dostęp tylko z poziomu maszyny wirtualnej. Komunikacja między maszyną wirtualną i IMDS nigdy nie opuszcza hosta.
Klienci korzystający z protokołu HTTP pomijają serwery proxy sieci Web w ramach maszyny wirtualnej podczas wykonywania zapytań do IMDS i traktują te `169.254.169.254` same dane co [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Użycie

### <a name="access-azure-instance-metadata-service"></a>Dostęp do usługi Azure Instance Metadata Service

Aby uzyskać dostęp do usługi IMDS, Utwórz maszynę wirtualną na podstawie [Azure Resource Manager](/rest/api/resources/) lub [Azure Portal](https://portal.azure.com)i użyj następujących przykładów.
Aby uzyskać więcej przykładów, zobacz [przykłady metadanych wystąpienia platformy Azure](https://github.com/microsoft/azureimds).

Oto przykładowy kod umożliwiający pobranie wszystkich metadanych wystąpienia. Aby uzyskać dostęp do określonego źródła danych, zobacz [Kategorie punktów końcowych](#endpoint-categories) , aby zapoznać się z omówieniem wszystkich dostępnych funkcji.

**Żądanie**

> [!IMPORTANT]
> Ten przykład pomija serwery proxy. Podczas wykonywania zapytania dotyczącego IMDS **należy** ominąć serwery proxy. Aby uzyskać dodatkowe informacje, zobacz [serwery proxy](#proxies) .

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

---

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Zabezpieczenia i uwierzytelnianie

Instance Metadata Service jest dostępny tylko w ramach uruchomionego wystąpienia maszyny wirtualnej w adresie IP bez obsługi routingu. Maszyny wirtualne są ograniczone do współdziałania z metadanymi i funkcjami, które odnoszą się do siebie. Interfejs API jest tylko HTTP i nigdy nie opuszcza hosta.

W celu zapewnienia, że żądania są bezpośrednio przeznaczone do IMDS i zapobiegać niezamierzonym lub niechcianym przekierowaniu żądań, żądania:
- **Musi** zawierać nagłówek `Metadata: true`
- **Nie** może zawierać `X-Forwarded-For` nagłówka

Wszystkie żądania, które nie spełniają **obu** tych wymagań, zostaną odrzucone przez usługę.

> [!IMPORTANT]
> IMDS **nie** jest kanałem dla poufnych danych. Interfejs API jest nieuwierzytelniony i otwarty dla wszystkich procesów na maszynie wirtualnej. Informacje uwidocznione za pomocą tej usługi powinny być traktowane jako informacje udostępnione wszystkim aplikacjom działającym w ramach maszyny wirtualnej.

## <a name="proxies"></a>Proxy

IMDS nie jest przeznaczona do użycia za serwerem proxy i **nie** jest to obsługiwane. Większość klientów HTTP zapewnia opcję wyłączania serwerów proxy na żądaniach, a tej funkcji należy używać podczas komunikacji z usługą IMDS. Aby uzyskać szczegółowe informacje, zapoznaj się z dokumentacją klienta.

> [!IMPORTANT]
> Nawet jeśli nie znasz żadnej konfiguracji serwera proxy w danym środowisku, **nadal musisz zastąpić wszystkie domyślne ustawienia serwera proxy klienta**. Konfiguracje serwera proxy mogą być wykrywane automatycznie i niepowodzenie pomijania takich konfiguracji ujawnia Outrage ryzyka, w przypadku których konfiguracja maszyny zostanie zmieniona w przyszłości.

## <a name="rate-limiting"></a>Rate limiting (Ograniczanie szybkości)

Ogólnie rzecz biorąc, żądania do IMDS są ograniczone do 5 żądań na sekundę. Żądania przekraczające wartość progową zostaną odrzucone z 429 odpowiedziami. Żądania do kategorii [tożsamości zarządzanej](#managed-identity) są ograniczone do 20 żądań na sekundę i 5 współbieżnych żądań.

## <a name="http-verbs"></a>Zlecenia HTTP

Obecnie obsługiwane są następujące zlecenia HTTP:

| Czasownik | Opis |
|------|-------------|
| `GET` | Pobierz żądany zasób

## <a name="parameters"></a>Parametry

Punkty końcowe mogą obsługiwać wymagane i/lub opcjonalne parametry. Aby uzyskać szczegółowe informacje, zobacz [schemat](#schema) i dokumentacja określonego punktu końcowego.

### <a name="query-parameters"></a>Parametry zapytania

Punkty końcowe IMDS obsługują parametry ciągu zapytania HTTP. Przykład: 

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
Parametry odpowiadające indeksom/kluczom, które zostałyby użyte do wyszukania obiektu JSON, były powiązane z przeanalizowanymi reprezentacjami.

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

Jeśli chcemy odfiltrować odpowiedź tylko do właściwości COMPUTE, wyślemy żądanie: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Podobnie, jeśli chcemy odfiltrować do właściwości zagnieżdżonej lub konkretnego elementu tablicy, utrzymujemy klucze do dołączania: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
filtruje do pierwszego elementu z `Network.interface` właściwości i zwraca:

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
> Filtrowanie do węzła liścia `format=json` nie działa. Dla tych zapytań `format=text` należy jawnie określić, ponieważ format domyślny to JSON.

## <a name="schema"></a>Schemat

### <a name="data-format"></a>Format danych

Domyślnie IMDS zwraca dane w formacie JSON ( `Content-Type: application/json` ). Jednak punkty końcowe obsługujące filtrowanie odpowiedzi (zobacz [Parametry tras](#route-parameters)) obsługują również format `text` .

Aby uzyskać dostęp do formatu niedomyślnej odpowiedzi, należy określić żądany format jako parametr ciągu zapytania w żądaniu. Przykład:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

W odpowiedziach JSON wszystkie elementy pierwotne będą typu `string` , a brakujące lub nieodpowiednie wartości są zawsze uwzględniane, ale zostaną ustawione na pusty ciąg.

### <a name="versioning"></a>Przechowywanie wersji

IMDS jest w wersji i określanie wersji interfejsu API w żądaniu HTTP jest obowiązkowe. Jedynym wyjątkiem od tego wymagania jest punkt końcowy [wersji](#versions) , którego można użyć do dynamicznego pobrania dostępnych wersji interfejsu API.

W miarę dodawania nowszych wersji nadal można uzyskać dostęp do starszych wersji, jeśli skrypty są zależne od określonych formatów danych.

Jeśli nie określisz wersji, zostanie wyświetlony komunikat o błędzie z listą najnowszych obsługiwanych wersji:
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

> [!NOTE]
> Wersja 2020-10-01 jest obecnie wdrażana i może nie być jeszcze dostępna w każdym regionie.

### <a name="swagger"></a>Swagger

Pełna Definicja struktury Swagger dla programu IMDS jest dostępna pod adresem: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Dostępność regionalna

Usługa jest **ogólnie dostępna** we wszystkich chmurach platformy Azure.

## <a name="root-endpoint"></a>Główny punkt końcowy

Głównym punktem końcowym jest `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Kategorie punktów końcowych

Interfejs API IMDS zawiera wiele kategorii punktów końcowych reprezentujących różne źródła danych, z których każdy zawiera jeden lub więcej punktów końcowych. Aby uzyskać szczegółowe informacje, zobacz każdą kategorię.

| Katalog główny kategorii | Opis | Wprowadzona wersja |
|---------------|-------------|--------------------|
| `/metadata/attested` | Zobacz [zaświadczone dane](#attested-data) | 2018-10-01
| `/metadata/identity` | Zobacz [tożsamość zarządzana za pośrednictwem IMDS](#managed-identity) | 2018-02-01
| `/metadata/instance` | Zobacz [metadane wystąpienia](#instance-metadata) | 2017-04-02
| `/metadata/scheduledevents` | Zobacz [Scheduled Events za pośrednictwem IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Zobacz [wersje](#versions) | Nie dotyczy

## <a name="versions"></a>Wersje

> [!NOTE]
> Ta funkcja została wydana wraz z wersją 2020-10-01, która jest obecnie wdrażana i może nie być jeszcze dostępna w każdym regionie.

### <a name="list-api-versions"></a>Wyświetl wersje interfejsu API

Zwraca zestaw obsługiwanych wersji interfejsu API.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parametry

Brak (ten punkt końcowy jest bez wersji).

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

### <a name="get-vm-metadata"></a>Pobieranie metadanych maszyny wirtualnej

Przedstawia ważne metadane dla wystąpienia maszyny wirtualnej, w tym obliczenia, Sieć i magazyn. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parametry

| Nazwa | Wymagane/Opcjonalne | Opis |
|------|-------------------|-------------|
| `api-version` | Wymagane | Wersja używana do obsługi żądania.
| `format` | Obowiązkowe | Format ( `json` lub `text` ) odpowiedzi. * Uwaga: mogą być wymagane w przypadku używania parametrów żądania

Ten punkt końcowy obsługuje filtrowanie odpowiedzi za pośrednictwem [parametrów trasy](#route-parameters).

#### <a name="response"></a>Reakcja

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Podział schematu:

**Środowisko obliczeniowe**

| Dane | Opis | Wprowadzona wersja |
|------|-------------|--------------------|
| `azEnvironment` | Środowisko platformy Azure, w którym jest uruchomiona maszyna wirtualna | 2018-10-01
| `customData` | Ta funkcja jest obecnie wyłączona. Ta dokumentacja zostanie zaktualizowana, gdy zostanie udostępniona | 2019-02-01
| `isHostCompatibilityLayerVm` | Określa, czy maszyna wirtualna jest uruchamiana na warstwie zgodności hosta | 2020-06-01
| `licenseType` | Typ licencji dla [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Ta funkcja jest obecna tylko w przypadku maszyn wirtualnych z obsługą AHB | 2020-09-01
| `location` | Region platformy Azure, w którym działa maszyna wirtualna | 2017-04-02
| `name` | Nazwa maszyny wirtualnej | 2017-04-02
| `offer` | Informacje o ofercie dla obrazu maszyny wirtualnej i dostępne tylko dla obrazów wdrożonych z galerii obrazów platformy Azure | 2017-04-02
| `osProfile.adminUsername` | Określa nazwę konta administratora | 2020-07-15
| `osProfile.computerName` | Określa nazwę komputera | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Określa, czy uwierzytelnianie hasła jest wyłączone. Ta prezentacja jest obecna tylko dla maszyn wirtualnych z systemem Linux | 2020-10-01
| `osType` | System Linux lub Windows | 2017-04-02
| `placementGroupId` | [Grupa umieszczania](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) zestawu skalowania maszyn wirtualnych | 2017-08-01
| `plan` | [Planowanie](/rest/api/compute/virtualmachines/createorupdate#plan) zawierające nazwę, produkt i wydawcę maszyny wirtualnej, jeśli jest to obraz portalu Azure Marketplace | 2018-04-02
| `platformUpdateDomain` |  [Aktualizuj domenę](../articles/virtual-machines/manage-availability.md) , w której działa maszyna wirtualna | 2017-04-02
| `platformFaultDomain` | [Domena błędów](../articles/virtual-machines/manage-availability.md) , w której działa maszyna wirtualna | 2017-04-02
| `provider` | Dostawca maszyny wirtualnej | 2018-10-01
| `publicKeys` | [Kolekcja kluczy publicznych](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) przypisanych do maszyny wirtualnej i ścieżek | 2018-04-02
| `publisher` | Wydawca obrazu maszyny wirtualnej | 2017-04-02
| `resourceGroupName` | [Grupa zasobów](../articles/azure-resource-manager/management/overview.md) dla maszyny wirtualnej | 2017-08-01
| `resourceId` | W [pełni kwalifikowany](/rest/api/resources/resources/getbyid) identyfikator zasobu | 2019-03-11
| `sku` | Określona jednostka SKU dla obrazu maszyny wirtualnej | 2017-04-02
| `securityProfile.secureBootEnabled` | Określa, czy na maszynie wirtualnej jest włączony bezpieczny rozruch z interfejsem UEFI | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Określa, czy wirtualny moduł TPM (TPM) jest włączony na maszynie wirtualnej | 2020-06-01
| `storageProfile` | Zobacz Profil magazynu poniżej | 2019-06-01
| `subscriptionId` | Subskrypcja platformy Azure dla maszyny wirtualnej | 2017-08-01
| `tags` | [Tagi](../articles/azure-resource-manager/management/tag-resources.md) dla maszyny wirtualnej  | 2017-08-01
| `tagsList` | Tagi sformatowane jako tablica JSON dla łatwiejszego analizowania programistycznego  | 2019-06-04
| `version` | Wersja obrazu maszyny wirtualnej | 2017-04-02
| `vmId` | [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny wirtualnej | 2017-04-02
| `vmScaleSetName` | [Nazwa zestawu skalowania maszyn wirtualnych](../articles/virtual-machine-scale-sets/overview.md) w zestawie skalowania maszyn wirtualnych | 2017-12-01
| `vmSize` | [Rozmiar maszyny wirtualnej](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Strefa dostępności](../articles/availability-zones/az-overview.md) maszyny wirtualnej | 2017-12-01

**Profil magazynu**

Profil magazynu maszyny wirtualnej jest podzielony na trzy kategorie: odwołanie do obrazu, dysk systemu operacyjnego i dyski z danymi.

Obiekt odwołanie do obrazu zawiera następujące informacje o obrazie systemu operacyjnego:

| Dane | Opis |
|------|-------------|
| `id` | Identyfikator zasobu
| `offer` | Oferta platformy lub obrazu z witryny Marketplace
| `publisher` | Wydawca obrazu
| `sku` | Jednostka SKU obrazu
| `version` | Wersja platformy lub obrazu witryny Marketplace

Obiekt dysku systemu operacyjnego zawiera następujące informacje na temat dysku systemu operacyjnego używanego przez maszynę wirtualną:

| Dane | Opis |
|------|-------------|
| `caching` | Wymagania dotyczące buforowania
| `createOption` | Informacje na temat sposobu tworzenia maszyny wirtualnej
| `diffDiskSettings` | Ustawienia dysku tymczasowych
| `diskSizeGB` | Rozmiar dysku w GB
| `image`   | Wirtualny dysk twardy obrazu użytkownika źródłowego
| `lun`     | Numer jednostki logicznej dysku
| `managedDisk` | Parametry dysku zarządzanego
| `name`    | Nazwa dysku
| `vhd`     | Wirtualny dysk twardy
| `writeAcceleratorEnabled` | Czy writeAccelerator jest włączona na dysku

Tablica dyski danych zawiera listę dysków danych podłączonych do maszyny wirtualnej. Każdy obiekt dysku danych zawiera następujące informacje:

Dane | Opis |
-----|-------------|
| `caching` | Wymagania dotyczące buforowania
| `createOption` | Informacje na temat sposobu tworzenia maszyny wirtualnej
| `diffDiskSettings` | Ustawienia dysku tymczasowych
| `diskSizeGB` | Rozmiar dysku w GB
| `encryptionSettings` | Ustawienia szyfrowania dysku
| `image` | Wirtualny dysk twardy obrazu użytkownika źródłowego
| `managedDisk` | Parametry dysku zarządzanego
| `name` | Nazwa dysku
| `osType` | Typ systemu operacyjnego znajdującego się na dysku
| `vhd` | Wirtualny dysk twardy
| `writeAcceleratorEnabled` | Czy writeAccelerator jest włączona na dysku

**Sieć**

| Dane | Opis | Wprowadzona wersja |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Lokalny adres IPv4 maszyny wirtualnej | 2017-04-02
| `ipv4.publicIpAddress` | Publiczny adres IPv4 maszyny wirtualnej | 2017-04-02
| `subnet.address` | Adres podsieci maszyny wirtualnej | 2017-04-02
| `subnet.prefix` | Prefiks podsieci, przykład 24 | 2017-04-02
| `ipv6.ipAddress` | Lokalny adres IPv6 maszyny wirtualnej | 2017-04-02
| `macAddress` | Adres MAC maszyny wirtualnej | 2017-04-02

**Tagi maszyn wirtualnych**

Tagi maszyn wirtualnych są dołączone do interfejsu API wystąpienia w punkcie końcowym wystąpienia/obliczenia/Tagi.
Tagi mogły zostać zastosowane do maszyny wirtualnej platformy Azure, aby logicznie zorganizować je w taksonomię. Tagi przypisane do maszyny wirtualnej można pobrać przy użyciu poniższego żądania.

`tags`Pole jest ciągiem zawierającym znaczniki rozdzielane średnikami. Ten wynik może być problemem, jeśli w samych tagach użyto średników. Jeśli parser jest Zapisano w celu programistycznego wyodrębnienia tagów, należy zastanowić się nad tym `tagsList` polem. `tagsList`Pole jest tablicą JSON bez ograniczników i w związku z tym ułatwia analizowanie.


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Przykład 1: śledzenie maszyny wirtualnej działającej na platformie Azure

Jako usługodawca może być konieczne śledzenie liczby maszyn wirtualnych korzystających z oprogramowania lub agentów, którzy muszą śledzić unikatowość maszyny wirtualnej. Aby można było uzyskać unikatowy identyfikator dla maszyny wirtualnej, użyj `vmId` pola z instance Metadata Service.

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"| ConvertTo-Json
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

W niektórych scenariuszach umieszczanie różnych replik danych ma podstawowe znaczenie. Na przykład [umieszczenie repliki](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) systemu plików HDFS lub umieszczenie kontenera za pośrednictwem programu [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) może wymagać znajomości, `platformFaultDomain` a `platformUpdateDomain` maszyna wirtualna jest uruchomiona.
Można również użyć [strefy dostępności](../articles/availability-zones/az-overview.md) , aby wystąpienia mogły podejmować te decyzje.
Możesz wysyłać zapytania o dane bezpośrednio za pośrednictwem IMDS.

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" | ConvertTo-Json
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

#### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Przykład 3: Uzyskaj więcej informacji na temat maszyny wirtualnej w przypadku pomocy technicznej

Jako dostawca usług możesz uzyskać informacje o pomocy technicznej, w których chcesz poznać więcej informacji o maszynie wirtualnej. Prośba o udostępnienie metadanych obliczeniowych przez klienta może stanowić podstawowe informacje dla specjalistów pomocy technicznej dotyczące rodzaju maszyny wirtualnej na platformie Azure.

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
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
    "sku": "Windows-Server-2012-R2-Datacenter",
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
            "osType": "Linux",
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

#### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Przykład 4: uzyskiwanie środowiska platformy Azure, w którym jest uruchomiona maszyna wirtualna

Platforma Azure ma rozmaite suwerenne chmury, takie jak [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Czasami konieczne jest środowisko platformy Azure, aby podejmować pewne decyzje dotyczące środowiska uruchomieniowego. Poniższy przykład pokazuje, jak można to osiągnąć.

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text" | ConvertTo-Json
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


#### <a name="sample-5-retrieve-network-information"></a>Przykład 5: pobieranie informacji o sieci

**Żądanie**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json
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

#### <a name="sample-6-retrieve-public-ip-address"></a>Przykład 6: pobieranie publicznego adresu IP

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text" | ConvertTo-Json
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Zaświadczone dane

### <a name="get-attested-data"></a>Pobierz zaświadczone dane

IMDS pomaga zapewnić gwarancję, że dostarczone dane pochodzą z platformy Azure. Firma Microsoft podpisuje część tych informacji, więc można potwierdzić, że obraz w witrynie Azure Marketplace jest używany przez Ciebie na platformie Azure.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parametry

| Nazwa | Wymagane/Opcjonalne | Opis |
|------|-------------------|-------------|
| `api-version` | Wymagane | Wersja używana do obsługi żądania.
| `nonce` | Opcjonalne | 10-cyfrowy ciąg, który służy jako kryptograficzny identyfikator jednorazowy. Jeśli nie podano wartości, IMDS używa bieżącej sygnatury czasowej UTC.

#### <a name="response"></a>Reakcja

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> Ze względu na mechanizm buforowania IMDS, można zwrócić wcześniej buforowaną wartość nonce.

Obiekt BLOB sygnatury jest podpisanym przez [PKCS7](https://aka.ms/pkcs7)wersją dokumentu. Zawiera certyfikat używany do podpisywania wraz z konkretnymi szczegółami dotyczącymi maszyn wirtualnych.

W przypadku maszyn wirtualnych utworzonych przy użyciu Azure Resource Manager dokument zawiera `vmId` , `sku` ,,, `nonce` `subscriptionId` `timeStamp` w celu utworzenia i wygaśnięcia dokumentu oraz planuje informacje o obrazie. Informacje o planie są wypełniane tylko dla obrazów w portalu Azure Marketplace.

W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania tylko `vmId` jest to wypełniane. Można wyodrębnić certyfikat z odpowiedzi i użyć go w celu potwierdzenia, że odpowiedź jest prawidłowa i pochodzi z platformy Azure.

Zdekodowany dokument zawiera następujące pola:

| Dane | Opis | Wprowadzona wersja |
|------|-------------|--------------------|
| `licenseType` | Typ licencji dla [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Ta funkcja jest obecna tylko w przypadku maszyn wirtualnych z obsługą AHB. | 2020-09-01
| `nonce` | Ciąg, który może być opcjonalnie dostarczony z żądaniem. Jeśli nie `nonce` podano tego, jest używana bieżąca koordynowana sygnatura czasowa. | 2018-10-01
| `plan` | [Plan obrazu portalu Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Zawiera identyfikator planu (nazwę), obraz produktu lub ofertę (produkt) oraz identyfikator wydawcy (wydawcy). | 2018-10-01
| `timestamp.createdOn` | Sygnatura czasowa UTC dla momentu utworzenia podpisanego dokumentu | 2018-20-01
| `timestamp.expiresOn` | Sygnatura czasowa UTC dla momentu wygaśnięcia podpisanego dokumentu | 2018-10-01
| `vmId` | [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny wirtualnej | 2018-10-01
| `subscriptionId` | Subskrypcja platformy Azure dla maszyny wirtualnej | 2019-04-30
| `sku` | Określona jednostka SKU dla obrazu maszyny wirtualnej | 2019-11-01

> [!NOTE]
> W przypadku maszyn wirtualnych z systemem klasycznym (innym niż Azure Resource Manager) należy wypełnić tylko identyfikator maszyny wirtualnej.

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

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Przykład 1: Weryfikowanie, czy maszyna wirtualna jest uruchomiona na platformie Azure

Dostawcy w witrynie Azure Marketplace chcą mieć pewność, że ich oprogramowanie jest licencjonowane do uruchamiania tylko na platformie Azure. Jeśli ktoś skopiuje wirtualny dysk twardy do środowiska lokalnego, musi być w stanie wykryć ten plik. Dzięki IMDS te dostawcy mogą uzyskać podpisane dane, które gwarantują odpowiedź tylko z platformy Azure.

> [!NOTE]
> Ten przykład wymaga zainstalowania narzędzia JQ.

**Walidacja**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Sprawdź, czy sygnatura pochodzi z Microsoft Azure i sprawdź, czy w łańcuchu certyfikatów występują błędy.

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

Sprawdź, czy sygnatura pochodzi z Microsoft Azure i sprawdź, czy w łańcuchu certyfikatów występują błędy.

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
> Ze względu na mechanizm buforowania IMDS, można zwrócić wcześniej buforowaną `nonce` wartość.

`nonce`W polu w podpisanym dokumencie można porównać, jeśli podano `nonce` parametr w początkowym żądaniu.

> [!NOTE]
> Certyfikat dla chmury publicznej i każdej z nich jest inny.

| Chmura | Certyfikat |
|-------|-------------|
| [Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/) | *. metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *. metadata.azure.us
| [Azure w Chinach — 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *. metadata.azure.cn
| [Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/) | *. metadata.microsoftazure.de

> [!NOTE]
> Certyfikaty mogą nie być dokładnie zgodne z `metadata.azure.com` chmurą publiczną. Z tego powodu weryfikacja certyfikacji powinna zezwalać na wspólną nazwę z dowolnej `.metadata.azure.com` poddomeny.

W przypadkach, gdy nie można pobrać certyfikatu pośredniego z powodu ograniczeń sieci podczas weryfikacji, można przypiąć certyfikat pośredni. Usługa Azure gromadzi informacje o certyfikatach, które są standardową zasadą infrastruktury kluczy publicznych. Przypięte certyfikaty należy zaktualizować, gdy nastąpi przerzucanie. Za każdym razem, gdy planowana jest zmiana aktualizacji certyfikatu pośredniego, blog platformy Azure zostanie zaktualizowany i klienci platformy Azure zostaną powiadomieni. 

Certyfikaty pośrednie można znaleźć w [repozytorium PKI](https://www.microsoft.com/pki/mscorp/cps/default.htm). Certyfikaty pośrednie dla każdego regionu mogą być różne.

> [!NOTE]
> Certyfikat pośredni dla usługi Azure Chiny dla Chin będzie pochodzą z globalnego głównego urzędu certyfikacji DigiCert zamiast Baltimore.
Jeśli przypięto certyfikaty pośrednie dla Chin platformy Azure w ramach zmiany urzędu łańcucha głównego, należy zaktualizować certyfikaty pośrednie.


## <a name="managed-identity"></a>Tożsamość zarządzana

Tożsamość zarządzana przypisana przez system można włączyć na maszynie wirtualnej. Do maszyny wirtualnej można także przypisać co najmniej jedną tożsamość zarządzaną przez użytkownika.
Następnie można zażądać tokenów dla tożsamości zarządzanych z IMDS. Te tokeny służą do uwierzytelniania za pomocą innych usług platformy Azure, takich jak Azure Key Vault.

Aby uzyskać szczegółowe instrukcje dotyczące włączania tej funkcji, zobacz [pozyskiwanie tokenu dostępu](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events"></a>Zaplanowane zdarzenia
Stan zaplanowanych zdarzeń można uzyskać za pomocą IMDS. Następnie użytkownik może określić zestaw akcji do uruchomienia w przypadku tych zdarzeń. Aby uzyskać więcej informacji, zobacz [zaplanowane zdarzenia dla systemu Linux](../articles/virtual-machines/linux/scheduled-events.md) lub [zaplanowanych zdarzeń w systemie Windows](../articles/virtual-machines/windows/scheduled-events.md).

## <a name="sample-code-in-different-languages"></a>Przykładowy kod w różnych językach

W poniższej tabeli przedstawiono przykłady wywoływania IMDS przy użyciu różnych języków w ramach maszyny wirtualnej:

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

Jeśli nie odnaleziono elementu danych lub nieprawidłowo sformułowane żądanie, Instance Metadata Service zwraca standardowe błędy HTTP. Przykład:

| Kod stanu HTTP | Przyczyna |
|------------------|--------|
| `200 OK` | Żądanie zakończyło się pomyślnie.
| `400 Bad Request` | Brak `Metadata: true` nagłówka lub brak parametru `format=json` podczas wykonywania zapytania o węzeł liścia
| `404 Not Found` | Żądany element nie istnieje
| `405 Method Not Allowed` | Metoda HTTP (Verb) nie jest obsługiwana w punkcie końcowym.
| `410 Gone` | Spróbuj ponownie za jakiś czas przez maksymalnie 70 sekund
| `429 Too Many Requests` | Przekroczono [limity szybkości](#rate-limiting) interfejsu API
| `500 Service Error` | Ponów próbę za jakiś czas

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pojawia się błąd `400 Bad Request, Required metadata header not specified` . Co to znaczy?**

IMDS wymaga, `Metadata: true` Aby nagłówek został przesłany w żądaniu. Przekazanie tego nagłówka w wywołaniu REST umożliwia dostęp do IMDS.

**Dlaczego nie otrzymuję informacji o obliczeniach dla mojej maszyny wirtualnej?**

Obecnie IMDS obsługuje tylko wystąpienia utworzone przy użyciu Azure Resource Manager.

**Moja maszyna wirtualna została utworzona przez Azure Resource Manager jakiś czas temu. Dlaczego nie widzę informacji o metadanych obliczeń?**

Jeśli maszyna wirtualna została utworzona po wrzesień 2016, Dodaj [tag](../articles/azure-resource-manager/management/tag-resources.md) , aby rozpocząć wyświetlanie metadanych obliczeniowych. Jeśli maszyna wirtualna została utworzona przed 2016 września, Dodaj lub usuń rozszerzenia lub dyski danych do wystąpienia maszyny wirtualnej w celu odświeżenia metadanych.

**Dlaczego nie widzę wszystkich danych wypełnionych dla nowej wersji?**

Jeśli maszyna wirtualna została utworzona po wrzesień 2016, Dodaj [tag](../articles/azure-resource-manager/management/tag-resources.md) , aby rozpocząć wyświetlanie metadanych obliczeniowych. Jeśli maszyna wirtualna została utworzona przed 2016 września, Dodaj lub usuń rozszerzenia lub dyski danych do wystąpienia maszyny wirtualnej w celu odświeżenia metadanych.

**Dlaczego otrzymuję błąd `500 Internal Server Error` lub `410 Resource Gone` ?**

Ponów żądanie. Aby uzyskać więcej informacji, zobacz [Obsługa błędów przejściowych](/azure/architecture/best-practices/transient-faults). Jeśli problem będzie nadal występować, Utwórz problem z pomocą techniczną w Azure Portal dla maszyny wirtualnej.

**Czy ta funkcja będzie działała dla wystąpień zestawu skalowania maszyn wirtualnych?**

Tak, IMDS jest dostępny dla wystąpień zestawu skalowania maszyn wirtualnych.

**Zaktualizowaliśmy moje Tagi w zestawach skalowania maszyn wirtualnych, ale nie są one wyświetlane w wystąpieniach (w przeciwieństwie do maszyn wirtualnych z pojedynczym wystąpieniem). Czy coś się nie robi?**

Obecnie Tagi dla zestawów skalowania maszyn wirtualnych są widoczne tylko dla maszyny wirtualnej po ponownym uruchomieniu, odniesieniu obrazu lub zmianie dysku na wystąpienie.

**Dlaczego upłynął limit czasu żądania dla mojego wywołania usługi?**

Wywołania metadanych muszą pochodzić z podstawowego adresu IP przypisanego do podstawowej karty sieciowej maszyny wirtualnej. Ponadto w przypadku zmiany tras w lokalnej tabeli routingu maszyny wirtualnej musi istnieć trasa dla adresu 169.254.169.254/32.

#### <a name="windows"></a>[Windows](#tab/windows/)

1. Zrzuć lokalną tabelę routingu i poszukaj wpisu IMDS. Przykład:
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
1. Sprawdź, czy trasa istnieje dla `169.254.169.254` i zanotuj odpowiedni interfejs sieciowy (na przykład `172.16.69.7` ).
1. Zrzuć konfigurację interfejsu i Znajdź interfejs, który odnosi się do tego, do którego odwołuje się tabela routingu, zwracając adres MAC (fizyczny).
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
1. Upewnij się, że interfejs odpowiada podstawowej karcie sieciowej i podstawowemu adresowi IP maszyny wirtualnej. Podstawowa karta sieciowa i adres IP można znaleźć, przeglądając konfigurację sieci w Azure Portal lub sprawdzając ją przy użyciu interfejsu wiersza polecenia platformy Azure. Zwróć uwagę na prywatne adresy IP (i adres MAC, jeśli używasz interfejsu wiersza polecenia). Oto przykład interfejsu wiersza polecenia programu PowerShell:
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
1. Jeśli nie są zgodne, zaktualizuj tabelę routingu, tak aby podstawowa karta sieciowa i adres IP były wskazywane.

#### <a name="linux"></a>[Linux](#tab/linux/)

 1. Zrzuć lokalną tabelę routingu za pomocą polecenia, takiego jak `netstat -r` i Wyszukaj wpis IMDS (np.):
    ```console
    ~$ netstat -r
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
    default         _gateway        0.0.0.0         UG        0 0          0 eth0
    168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
    169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
    172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
    ```
1. Sprawdź, czy trasa istnieje dla `169.254.169.254` i zanotuj odpowiedni interfejs sieciowy (np. `eth0` ).
1. Zrzuć konfigurację interfejsu dla odpowiedniego interfejsu w tabeli routingu (Zwróć uwagę na to, że dokładna nazwa pliku konfiguracji może się różnić)
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
1. Jeśli używasz dynamicznego adresu IP, Zanotuj adres MAC. Jeśli używasz statycznego adresu IP, możesz zanotować wymienione adresy IP i/lub adres MAC.
1. Upewnij się, że interfejs odpowiada podstawowej karcie sieciowej i podstawowemu adresowi IP maszyny wirtualnej. Podstawowa karta sieciowa i adres IP można znaleźć, przeglądając konfigurację sieci w Azure Portal lub sprawdzając ją przy użyciu interfejsu wiersza polecenia platformy Azure. Zwróć uwagę na prywatne adresy IP (i adres MAC, jeśli używasz interfejsu wiersza polecenia). Oto przykład interfejsu wiersza polecenia programu PowerShell:
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
1. Jeśli nie są zgodne, zaktualizuj tabelę routingu w taki sposób, aby podstawowa karta sieciowa/adres IP były wskazywane.

---

**Klaster trybu failover w systemie Windows Server**

Podczas wykonywania zapytania dotyczącego usługi IMDS z klastrem trybu failover czasami konieczne jest dodanie trasy do tabeli routingu. Oto kroki tej procedury:

1. Otwórz wiersz polecenia z uprawnieniami administratora.

1. Uruchom następujące polecenie i Zanotuj adres interfejsu dla miejsca docelowego sieci ( `0.0.0.0` ) w tabeli tras IPv4.

```bat
route print
```

> [!NOTE]
> Następujące przykładowe dane wyjściowe pochodzą z maszyny wirtualnej z systemem Windows Server z włączoną funkcją klaster trybu failover. Dla uproszczenia dane wyjściowe zawierają tylko tabelę tras IPv4.

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

Uruchom następujące polecenie i użyj adresu interfejsu dla miejsca docelowego sieci ( `0.0.0.0` ), czyli ( `10.0.1.10` ) w tym przykładzie.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="support"></a>Pomoc techniczna

Jeśli nie możesz uzyskać odpowiedzi na metadane po wielu próbach, możesz utworzyć problem z pomocą techniczną w Azure Portal.

## <a name="product-feedback"></a>Opinia dotycząca produktu

Możesz podać Opinie i pomysły dotyczące produktu w naszym kanale opinii użytkowników w obszarze Virtual Machines > Instance Metadata Service tutaj: https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627

## <a name="next-steps"></a>Następne kroki

[Uzyskaj token dostępu dla maszyny wirtualnej](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Zaplanowane zdarzenia dla systemu Linux](../articles/virtual-machines/linux/scheduled-events.md)

[Zaplanowane zdarzenia dla systemu Windows](../articles/virtual-machines/windows/scheduled-events.md)
