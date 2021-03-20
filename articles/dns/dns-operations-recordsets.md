---
title: Zarządzanie rekordami DNS w Azure DNS przy użyciu Azure PowerShell | Microsoft Docs
description: Zarządzanie zestawami rekordów DNS i rekordami na Azure DNS podczas hostowania domeny na Azure DNS. Wszystkie polecenia programu PowerShell dla operacji na zestawach rekordów i rekordach.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: f4e713f54ab4702b21763dc9fc6c7b606f94a945
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011595"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Zarządzanie rekordami i zestawami nazw DNS w Azure DNS przy użyciu Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Klasyczny interfejs wiersza polecenia platformy Azure](./dns-operations-recordsets-cli.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-recordsets-cli.md)
> * [Program PowerShell](dns-operations-recordsets.md)

W tym artykule pokazano, jak zarządzać rekordami DNS dla strefy DNS przy użyciu Azure PowerShell. Rekordy DNS można także zarządzać za pomocą międzyplatformowego [interfejsu wiersza polecenia platformy Azure](dns-operations-recordsets-cli.md) lub [Azure Portal](dns-operations-recordsets-portal.md).

W przykładach w tym artykule przyjęto założenie, że [zainstalowano już Azure PowerShell, zalogowano się i utworzono strefę DNS](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Wprowadzenie

Przed utworzeniem rekordów DNS w usłudze Azure DNS należy najpierw zrozumieć, w jaki sposób usługa Azure DNS organizuje rekordy DNS w zestawy rekordów DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Aby uzyskać więcej informacji na temat rekordów DNS w usłudze Azure DNS, zobacz [Strefy i rekordy DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Utwórz nowy rekord DNS

Jeśli nowy rekord ma taką samą nazwę i typ jak istniejący rekord, należy [dodać go do istniejącego zestawu rekordów](#add-a-record-to-an-existing-record-set). Jeśli nowy rekord ma inną nazwę i typ do wszystkich istniejących rekordów, należy utworzyć nowy zestaw rekordów. 

### <a name="create-a-records-in-a-new-record-set"></a>Utwórz rekordy "A" w nowym zestawie rekordów

Zestawy rekordów są tworzone za pomocą polecenia cmdlet `New-AzDnsRecordSet`. Podczas tworzenia zestawu rekordów należy określić nazwę zestawu rekordów, strefę, czas wygaśnięcia (TTL), typ rekordu i rekordy, które mają zostać utworzone.

Parametry używane do dodawania rekordów do zestawu rekordów różnią się w zależności od typu zestawu rekordów. Na przykład podczas korzystania z zestawu rekordów typu "A" należy określić adres IP przy użyciu parametru `-IPv4Address` . Inne parametry są używane dla innych typów rekordów. Aby uzyskać szczegółowe informacje, zobacz dodatkowe przykłady typów rekordów.

Poniższy przykład tworzy zestaw rekordów o nazwie względnej "www" w strefie DNS "contoso.com". W pełni kwalifikowana nazwa zestawu rekordów to "www.contoso.com". Typ rekordu to "A", a czas wygaśnięcia wynosi 3600 sekund. Zestaw rekordów zawiera pojedynczy rekord z adresem IP "1.2.3.4".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Aby utworzyć zestaw rekordów w "wierzchołku" strefy (w tym przypadku "contoso.com"), użyj nazwy zestawu rekordów " \@ " (z wyjątkiem cudzysłowów):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Jeśli musisz utworzyć zestaw rekordów zawierający więcej niż jeden rekord, najpierw Utwórz tablicę lokalną i Dodaj rekordy, a następnie Przekaż tablicę w `New-AzDnsRecordSet` następujący sposób:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) mogą służyć do kojarzenia danych specyficznych dla aplikacji z każdym zestawem rekordów jako par klucz-wartość. Poniższy przykład pokazuje, jak utworzyć zestaw rekordów z dwoma wpisami metadanych: "Dept = finanse" i "Environment = Production".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS obsługuje również zestawy rekordów "Empty", które mogą działać jako symbol zastępczy w celu zarezerwowania nazwy DNS przed utworzeniem rekordów DNS. Puste zestawy rekordów są widoczne na płaszczyźnie kontroli Azure DNS, ale pojawiają się na serwerach nazw Azure DNS. Poniższy przykład tworzy pusty zestaw rekordów:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Tworzenie rekordów innych typów

Po zapoznaniu się z szczegółowymi informacjami na temat tworzenia rekordów "A" w poniższych przykładach pokazano, jak utworzyć rekordy innych typów rekordów obsługiwanych przez Azure DNS.

W każdym przypadku pokazujemy, jak utworzyć zestaw rekordów zawierający pojedynczy rekord. Wcześniejsze przykłady dla rekordów "A" można dostosować do tworzenia zestawów rekordów innych typów zawierających wiele rekordów, z metadanymi lub do tworzenia pustych zestawów rekordów.

Nie oferujemy przykładu tworzenia zestawu rekordów SOA, ponieważ SOAs są tworzone i usuwane z każdej strefy DNS i nie można ich tworzyć ani usuwać oddzielnie. Można jednak [zmodyfikować rekord SOA, jak pokazano w późniejszym przykładzie](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów AAAA z pojedynczym rekordem

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów CAA z pojedynczym rekordem

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów CNAME z pojedynczym rekordem

> [!NOTE]
> Standardy DNS nie zezwalają na rekordy CNAME na wierzchołku strefy ( `-Name '@'` ) ani nie zezwalają na zestawy rekordów zawierające więcej niż jeden rekord.
> 
> Aby uzyskać więcej informacji, zobacz [rekordy CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów MX z pojedynczym rekordem

W tym przykładzie używamy nazwy zestawu rekordów " \@ " do utworzenia rekordu MX w wierzchołku strefy (w tym przypadku "contoso.com").


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów NS z pojedynczym rekordem

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów PTR z pojedynczym rekordem

W tym przypadku "my-arpa-zone.com" reprezentuje strefę wyszukiwania wstecznego programu ARPA reprezentującą zakres adresów IP. Każdy rekord PTR w tej strefie odnosi się do adresu IP w tym zakresie adresów IP. Nazwa rekordu "10" jest ostatnim oktetem adresu IP w tym zakresie adresów IP reprezentowanego przez ten rekord.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów SRV z pojedynczym rekordem

Podczas tworzenia [zestawu rekordów SRV](dns-zones-records.md#srv-records)Określ *\_ usługę* i *\_ Protokół* w nazwie zestawu rekordów. \@Podczas tworzenia zestawu rekordów SRV w wierzchołku strefy nie trzeba uwzględniać elementu "" w nazwie zestawu rekordów.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów TXT z pojedynczym rekordem

Poniższy przykład pokazuje, jak utworzyć rekord TXT. Aby uzyskać więcej informacji na temat maksymalnej długości ciągu obsługiwanej w rekordach TXT, zobacz [txt Records](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Pobierz zestaw rekordów

Aby pobrać istniejący zestaw rekordów, użyj `Get-AzDnsRecordSet` . To polecenie cmdlet zwraca obiekt lokalny reprezentujący zestaw rekordów w Azure DNS.

Podobnie jak w przypadku `New-AzDnsRecordSet` , określona nazwa zestawu rekordów musi być nazwą *względną* , co oznacza, że musi wykluczać nazwę strefy. Należy również określić typ rekordu oraz strefę zawierającą zestaw rekordów.

Poniższy przykład pokazuje, jak pobrać zestaw rekordów. W tym przykładzie strefa jest określona za pomocą `-ZoneName` `-ResourceGroupName` parametrów i.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatywnie można także określić strefę przy użyciu obiektu strefy, przekazaną za pomocą `-Zone` parametru.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Lista zestawów rekordów

Można również użyć, `Get-AzDnsZone` Aby wyświetlić listę zestawów rekordów w strefie, pomijając `-Name` Parametry i/lub `-RecordType` .

Poniższy przykład zwraca wszystkie zestawy rekordów w strefie:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Poniższy przykład pokazuje, jak można pobrać wszystkie zestawy rekordów danego typu przez określenie typu rekordu podczas pomijania nazwy zestawu rekordów:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Aby pobrać wszystkie zestawy rekordów o danej nazwie, w różnych typach rekordów, należy pobrać wszystkie zestawy rekordów, a następnie filtrować wyniki:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

We wszystkich powyższych przykładach strefa może być określona za pomocą `-ZoneName` `-ResourceGroupName` parametrów i (jak pokazano) lub przez określenie obiektu strefy:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Dodawanie rekordu do istniejącego zestawu rekordów

Aby dodać rekord do istniejącego zestawu rekordów, wykonaj następujące trzy kroki:

1. Pobierz istniejący zestaw rekordów

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Dodaj nowy rekord do lokalnego zestawu rekordów. Jest to operacja poza wierszem.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Zatwierdź zmianę z powrotem do usługi Azure DNS. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Użycie `Set-AzDnsRecordSet` *zamienia* istniejący zestaw rekordów w Azure DNS (i wszystkie zawarte w nim rekordy) z określonym zestawem rekordów. [Testy ETag](dns-zones-records.md#etags) są używane do zapewnienia, że zmiany współbieżne nie są zastępowane. Aby pominąć te testy, można użyć opcjonalnego `-Overwrite` przełącznika.

Ta sekwencja operacji może być również *potoku*, co oznacza, że obiekt zestawu rekordów jest przekazywany przy użyciu potoku zamiast przekazywania go jako parametr:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Powyższy przykład pokazuje, jak dodać rekord "A" do istniejącego zestawu rekordów typu "A". Podobna sekwencja operacji służy do dodawania rekordów do zestawów rekordów innych typów, podstawiania `-Ipv4Address` parametru z `Add-AzDnsRecordConfig` innymi parametrami specyficznymi dla każdego typu rekordu. Parametry dla każdego typu rekordu są takie same jak w przypadku `New-AzDnsRecordConfig` polecenia cmdlet, jak pokazano w dodatkowych przykładach typu rekordu powyżej.

Zestawy rekordów typu "CNAME" lub "SOA" nie mogą zawierać więcej niż jednego rekordu. To ograniczenie wynika ze standardów DNS. Nie jest to ograniczenie Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Usuwanie rekordu z istniejącego zestawu rekordów

Proces usuwania rekordu z zestawu rekordów jest podobny do procesu umożliwiającego dodanie rekordu do istniejącego zestawu rekordów:

1. Pobierz istniejący zestaw rekordów

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Usuń rekord z lokalnego obiektu zestawu rekordów. Jest to operacja poza wierszem. Rekord, który jest usuwany, musi być dokładnym dopasowaniem do istniejącego rekordu we wszystkich parametrach.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Zatwierdź zmianę z powrotem do usługi Azure DNS. Użyj opcjonalnego `-Overwrite` przełącznika, aby pominąć [Sprawdzanie ETag](dns-zones-records.md#etags) dla współbieżnych zmian.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Użycie powyższej sekwencji w celu usunięcia ostatniego rekordu z zestawu rekordów nie powoduje usunięcia zestawu rekordów, a tym samym pozostawienie pustego zestawu rekordów. Aby całkowicie usunąć zestaw rekordów, zobacz [usuwanie zestawu rekordów](#delete-a-record-set).

Podobnie jak w przypadku dodawania rekordów do zestawu rekordów, sekwencja operacji usuwania zestawu rekordów może być również potokiem:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Różne typy rekordów są obsługiwane przez przekazanie odpowiednich parametrów specyficznych dla typu do `Remove-AzDnsRecordSet` . Parametry dla każdego typu rekordu są takie same jak w przypadku `New-AzDnsRecordConfig` polecenia cmdlet, jak pokazano w dodatkowych przykładach typu rekordu powyżej.


## <a name="modify-an-existing-record-set"></a>Modyfikuj istniejący zestaw rekordów

Procedura modyfikowania istniejącego zestawu rekordów jest podobna do kroków wykonywanych podczas dodawania lub usuwania rekordów z zestawu rekordów:

1. Pobierz istniejący zestaw rekordów przy użyciu polecenia `Get-AzDnsRecordSet` .
2. Zmodyfikuj obiekt lokalnego zestawu rekordów według:
    * Dodawanie lub usuwanie rekordów
    * Zmiana parametrów istniejących rekordów
    * Zmiana metadanych zestawu rekordów i czasu wygaśnięcia (TTL)
3. Zatwierdź zmiany przy użyciu `Set-AzDnsRecordSet` polecenia cmdlet. Spowoduje to *zastąpienie* istniejącego zestawu rekordów w Azure DNS z określonym zestawem rekordów.

W przypadku korzystania z programu `Set-AzDnsRecordSet` [operacje sprawdzania ETag](dns-zones-records.md#etags) są używane do zapewnienia, że zmiany współbieżne nie są zastępowane. Aby pominąć te testy, można użyć opcjonalnego `-Overwrite` przełącznika.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Aby zaktualizować rekord w istniejącym zestawie rekordów

W tym przykładzie zmienimy adres IP istniejącego rekordu "A":

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Aby zmodyfikować rekord SOA

Nie można dodawać ani usuwać rekordów z automatycznie utworzonego rekordu SOA w wierzchołku strefy ( `-Name "@"` włącznie ze znakami cudzysłowu). Można jednak zmodyfikować dowolny z parametrów w rekordzie SOA (z wyjątkiem "host") i zestawem rekordów TTL.

Poniższy przykład pokazuje, jak zmienić właściwość *poczty e-mail* rekordu SOA:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Aby zmodyfikować rekordy NS w wierzchołku strefy

Rekord NS ustawiony w wierzchołku strefy jest automatycznie tworzony przy użyciu każdej strefy DNS. Zawiera nazwy serwerów nazw Azure DNS przypisanych do strefy.

Do tego zestawu rekordów NS można dodać kolejne serwery nazw, aby obsługiwać domeny współpracujące z więcej niż jednym dostawcą DNS. Możesz również zmodyfikować czas wygaśnięcia i metadane dla tego zestawu rekordów. Nie można jednak usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw Azure DNS.

Należy zauważyć, że ma to zastosowanie tylko do zestawu rekordów NS w wierzchołku strefy. Inne zestawy rekordów NS w strefie (używane do delegowania stref podrzędnych) można modyfikować bez ograniczenia.

Poniższy przykład pokazuje, jak dodać dodatkowy serwer nazw do zestawu rekordów NS na stronie wierzchołka strefy:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Aby zmodyfikować metadane zestawu rekordów

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) mogą służyć do kojarzenia danych specyficznych dla aplikacji z każdym zestawem rekordów jako par klucz-wartość.

Poniższy przykład pokazuje, jak zmodyfikować metadane istniejącego zestawu rekordów:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Usuwanie zestawu rekordów

Zestawy rekordów można usuwać za pomocą `Remove-AzDnsRecordSet` polecenia cmdlet. Usunięcie zestawu rekordów spowoduje również usunięcie wszystkich rekordów w zestawie rekordów.

> [!NOTE]
> Nie można usunąć zestawów rekordów SOA i NS przy wierzchołku strefy ( `-Name '@'` ).  Azure DNS utworzone automatycznie podczas tworzenia strefy i automatycznie usuwa je po usunięciu strefy.

Poniższy przykład pokazuje, jak usunąć zestaw rekordów. W tym przykładzie określono jawnie nazwę zestawu rekordów, typ zestawu rekordów, nazwę strefy i grupę zasobów.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatywnie zestaw rekordów może być określony przez nazwę i typ oraz strefę określoną przy użyciu obiektu:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Trzecią opcją jest określenie samego zestawu rekordów przy użyciu obiektu zestawu rekordów:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

W przypadku określenia zestawu rekordów, który ma zostać usunięty przy użyciu obiektu zestawu rekordów, [testy ETag](dns-zones-records.md#etags) są używane w celu zapewnienia, że zmiany współbieżne nie są usuwane. Aby pominąć te testy, można użyć opcjonalnego `-Overwrite` przełącznika.

Obiekt zestawu rekordów może również być potokiem, a nie do przekazywania jako parametr:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Monity o potwierdzenie

Polecenia cmdlet `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` i `Remove-AzDnsRecordSet` obsługują monity o potwierdzenie.

Każde polecenie cmdlet monituje o potwierdzenie, jeśli `$ConfirmPreference` zmienna preferencji programu PowerShell ma wartość `Medium` lub mniejszą. Ponieważ wartość domyślna `$ConfirmPreference` to is `High` , te monity nie są podane podczas korzystania z domyślnych ustawień programu PowerShell.

Bieżące ustawienie `$ConfirmPreference` można zastąpić przy użyciu parametru `-Confirm`. W przypadku wybrania elementów `-Confirm` lub `-Confirm:$True` polecenie cmdlet monituje o potwierdzenie przed uruchomieniem. W przypadku wybrania elementu `-Confirm:$False` polecenie cmdlet nie monituje o potwierdzenie. 

Aby uzyskać więcej informacji na temat elementów `-Confirm` i `$ConfirmPreference`, zobacz [About Preference Variables (Informacje o zmiennych preferencji)](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [strefach i rekordach w Azure DNS](dns-zones-records.md).
<br>
Dowiedz się [, jak chronić strefy i rekordy](dns-protect-zones-recordsets.md) przy użyciu Azure DNS.
<br>
Zapoznaj się z [dokumentacją programu Azure DNS PowerShell](/powershell/module/az.dns).