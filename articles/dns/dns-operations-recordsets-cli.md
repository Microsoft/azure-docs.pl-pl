---
title: Zarządzanie rekordami DNS w Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure
description: Zarządzanie zestawami rekordów DNS i rekordami na Azure DNS podczas hostowania domeny na Azure DNS.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: how-to
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 2d3989b3c477a35d602f1ccf3e45d6f597f5d78d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011573"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Zarządzanie rekordami i zestawami nazw DNS w Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](dns-operations-recordsets-portal.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-recordsets-cli.md)
> * [Program PowerShell](dns-operations-recordsets.md)

W tym artykule pokazano, jak zarządzać rekordami DNS dla strefy DNS przy użyciu międzyplatformowego interfejsu wiersza polecenia platformy Azure, który jest dostępny dla systemów Windows, Mac i Linux. Rekordy DNS można także zarządzać za pomocą [Azure PowerShell](dns-operations-recordsets.md) lub [Azure Portal](dns-operations-recordsets-portal.md).

W przykładach w tym artykule założono, że [zainstalowano już interfejs wiersza polecenia platformy Azure, zalogowano się i utworzono strefę DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Wprowadzenie

Przed utworzeniem rekordów DNS w usłudze Azure DNS należy najpierw zrozumieć, w jaki sposób usługa Azure DNS organizuje rekordy DNS w zestawy rekordów DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Aby uzyskać więcej informacji na temat rekordów DNS w usłudze Azure DNS, zobacz [Strefy i rekordy DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Aby utworzyć rekord DNS, użyj `az network dns record-set <record-type> add-record` polecenia (gdzie `<record-type>` jest typem rekordu, tj. a, SRV, txt itp.) Aby uzyskać pomoc, zobacz `az network dns record-set --help` .

Podczas tworzenia rekordu należy określić nazwę grupy zasobów, nazwę strefy, nazwę zestawu rekordów, typ rekordu oraz szczegóły tworzonego rekordu. Określona nazwa zestawu rekordów musi być nazwą *względną* , co oznacza, że musi wykluczać nazwę strefy.

Jeśli zestaw rekordów jeszcze nie istnieje, to polecenie utworzy go dla Ciebie. Jeśli zestaw rekordów już istnieje, to polecenie doda określony rekord do istniejącego zestawu rekordów.

Jeśli jest tworzony nowy rekord, używany jest domyślny czas wygaśnięcia wynoszący 3600. Aby uzyskać instrukcje dotyczące używania różnych TTLs, zobacz [Tworzenie zestawu rekordów DNS](#create-a-dns-record-set).

Poniższy przykład tworzy rekord A o nazwie *www* w strefie *contoso.com* w grupie zasobów *MyResourceGroup*. Adres IP rekordu A to *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Aby utworzyć zestaw rekordów w wierzchołku strefy (w tym przypadku "contoso.com"), użyj nazwy rekordu " \@ ", w tym cudzysłowu:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Tworzenie zestawu rekordów DNS

W powyższych przykładach rekord DNS został dodany do istniejącego zestawu rekordów lub zestaw rekordów został utworzony *niejawnie*. Możesz również utworzyć zestaw rekordów *jawnie* przed dodaniem do niego rekordów. Azure DNS obsługuje zestawy rekordów "Empty", które mogą działać jako symbol zastępczy w celu zarezerwowania nazwy DNS przed utworzeniem rekordów DNS. Puste zestawy rekordów są widoczne na płaszczyźnie kontroli Azure DNS, ale nie są wyświetlane na serwerach nazw Azure DNS.

Zestawy rekordów są tworzone za pomocą `az network dns record-set <record-type> create` polecenia. Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> create --help`.

Tworzenie zestawu rekordów jawnie umożliwia określenie właściwości zestawu rekordów, takich jak [czas wygaśnięcia (TTL)](dns-zones-records.md#time-to-live) i metadane. [Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) mogą służyć do kojarzenia danych specyficznych dla aplikacji z każdym zestawem rekordów jako par klucz-wartość.

Poniższy przykład powoduje utworzenie pustego zestawu rekordów typu "A" z 60 sekund TTL przy użyciu `--ttl` parametru (krótka forma `-l` ):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

Poniższy przykład tworzy zestaw rekordów z dwoma wpisami metadanych "Dept = finanse" i "Environment = Production" przy użyciu `--metadata` parametru:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Po utworzeniu pustego zestawu rekordów rekordy można dodać, korzystając z procedury `azure network dns record-set <record-type> add-record` opisanej w temacie [Tworzenie rekordu DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Tworzenie rekordów innych typów

Po zapoznaniu się z szczegółowymi informacjami na temat tworzenia rekordów "A" w poniższych przykładach pokazano, jak utworzyć rekord innych typów rekordów obsługiwanych przez Azure DNS.

Parametry używane do określenia danych rekordu różnią się w zależności od typu rekordu. Na przykład w przypadku rekordu typu „A” należy podać adres IPv4 przy użyciu parametru `--ipv4-address <IPv4 address>`. Parametry dla każdego typu rekordu można wyświetlić przy użyciu `az network dns record-set <record-type> add-record --help` .

W każdym przypadku pokazujemy, jak utworzyć pojedynczy rekord. Rekord zostanie dodany do istniejącego zestawu rekordów lub zestaw rekordów został utworzony niejawnie. Aby uzyskać więcej informacji na temat jawnie tworzenia zestawów rekordów i parametrów zestawu rekordów, zobacz [Tworzenie zestawu rekordów DNS](#create-a-dns-record-set).

Nie oferujemy przykładu tworzenia zestawu rekordów SOA, ponieważ SOAs są tworzone i usuwane z każdej strefy DNS i nie można ich tworzyć ani usuwać oddzielnie. Można jednak [zmodyfikować rekord SOA, jak pokazano w późniejszym przykładzie](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>Utwórz rekord AAAA

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Tworzenie rekordu CAA

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Tworzenie rekordu CNAME

> [!NOTE]
> Standardy DNS nie zezwalają na rekordy CNAME na wierzchołku strefy ( `--Name "@"` ) ani nie zezwalają na zestawy rekordów zawierające więcej niż jeden rekord.
> 
> Aby uzyskać więcej informacji, zobacz [rekordy CNAME](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Tworzenie rekordu MX

W tym przykładzie używamy nazwy zestawu rekordów " \@ " do utworzenia rekordu MX w wierzchołku strefy (w tym przypadku "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Tworzenie rekordu NS

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Utwórz rekord PTR

W tym przypadku "my-arpa-zone.com" reprezentuje strefę programu ARPA reprezentującą zakres adresów IP. Każdy rekord PTR w tej strefie odnosi się do adresu IP w tym zakresie adresów IP.  Nazwa rekordu "10" jest ostatnim oktetem adresu IP w tym zakresie adresów IP reprezentowanego przez ten rekord.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Tworzenie rekordu SRV

Podczas tworzenia [zestawu rekordów SRV](dns-zones-records.md#srv-records)Określ *\_ usługę* i *\_ Protokół* w nazwie zestawu rekordów. \@Podczas tworzenia zestawu rekordów SRV w wierzchołku strefy nie trzeba uwzględniać elementu "" w nazwie zestawu rekordów.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Utwórz rekord TXT

Poniższy przykład pokazuje, jak utworzyć rekord TXT. Aby uzyskać więcej informacji na temat maksymalnej długości ciągu obsługiwanej w rekordach TXT, zobacz [txt Records](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Pobierz zestaw rekordów

Aby pobrać istniejący zestaw rekordów, użyj `az network dns record-set <record-type> show` . Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> show --help`.

Podobnie jak podczas tworzenia rekordu lub zestawu rekordów, określona nazwa zestawu rekordów musi być nazwą *względną* , co oznacza, że musi wykluczać nazwę strefy. Należy również określić typ rekordu, strefę zawierającą zestaw rekordów i grupę zasobów zawierającą strefę.

Poniższy przykład umożliwia pobranie rekordu *www* typu A ze strefy *contoso.com* *w grupie zasobów Grupa zasobu:*

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Lista zestawów rekordów

Wszystkie rekordy w strefie DNS można wyświetlić za pomocą `az network dns record-set list` polecenia. Aby uzyskać pomoc, zobacz `az network dns record-set list --help`.

Ten przykład zwraca wszystkie zestawy rekordów w strefie *contoso.com*, *w grupie zasobów Grupa zasobu,* niezależnie od nazwy lub typu rekordu:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Ten przykład zwraca wszystkie zestawy rekordów, które pasują do podanego typu rekordu (w tym przypadku rekordy "A"):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Dodawanie rekordu do istniejącego zestawu rekordów

Można użyć `az network dns record-set <record-type> add-record` obu tych metod do utworzenia rekordu w nowym zestawie rekordów lub dodania rekordu do istniejącego zestawu rekordów.

Aby uzyskać więcej informacji, zobacz [Tworzenie rekordu DNS](#create-a-dns-record) i [tworzenie rekordów innych typów](#create-records-of-other-types) powyżej.

## <a name="remove-a-record-from-an-existing-record-set"></a>Usuń rekord z istniejącego zestawu rekordów.

Aby usunąć rekord DNS z istniejącego zestawu rekordów, użyj `az network dns record-set <record-type> remove-record` . Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> remove-record -h`.

To polecenie usuwa rekord DNS z zestawu rekordów. W przypadku usunięcia ostatniego rekordu w zestawie rekordów zostanie również usunięty sam zestaw rekordów. Aby zamiast tego pozostawić pusty rekord, użyj `--keep-empty-record-set` opcji.

Należy określić rekord, który ma zostać usunięty, oraz strefę, z której należy ją usunąć, przy użyciu tych samych parametrów co podczas tworzenia rekordu przy użyciu `az network dns record-set <record-type> add-record` . Te parametry są opisane w artykule [Tworzenie rekordu DNS](#create-a-dns-record) i [tworzenie rekordów innych typów](#create-records-of-other-types) powyżej.

Poniższy przykład usuwa rekord A o wartości "1.2.3.4" z zestawu rekordów o nazwie *www* w strefie *contoso.com*, *w grupie zasobów Grupa zasobu.*

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Modyfikuj istniejący zestaw rekordów

Każdy zestaw rekordów zawiera [czas wygaśnięcia (TTL)](dns-zones-records.md#time-to-live), [metadane](dns-zones-records.md#tags-and-metadata)i rekordy DNS. W poniższych sekcjach wyjaśniono, jak zmodyfikować każdą z tych właściwości.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Aby zmodyfikować rekord A, AAAA, CAA, MX, NS, PTR, SRV lub TXT

Aby zmodyfikować istniejący rekord typu A, AAAA, CAA, MX, NS, PTR, SRV lub TXT, należy najpierw dodać nowy rekord, a następnie usunąć istniejący rekord. Aby uzyskać szczegółowe instrukcje dotyczące usuwania i dodawania rekordów, zobacz wcześniejsze sekcje tego artykułu.

Poniższy przykład pokazuje, jak zmodyfikować rekord "A" z adresu IP 1.2.3.4 na adres IP 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Nie można dodawać, usuwać ani modyfikować rekordów w automatycznie tworzonym rekordzie NS ustawionym w wierzchołku strefy ( `--Name "@"` łącznie ze znakami cudzysłowu). Dla tego zestawu rekordów Jedynymi dozwolonymi zmianami jest modyfikowanie wartości TTL i metadanych zestawu rekordów.

### <a name="to-modify-a-cname-record"></a>Aby zmodyfikować rekord CNAME

W przeciwieństwie do większości innych typów rekordów, zestaw rekordów CNAME może zawierać tylko jeden rekord.  W związku z tym nie można zastąpić bieżącej wartości przez dodanie nowego rekordu i usunięcie istniejącego rekordu, tak jak w przypadku innych typów rekordów.

Zamiast tego, aby zmodyfikować rekord CNAME, użyj polecenia `az network dns record-set cname set-record` . Aby uzyskać pomoc, zobacz `az network dns record-set cname set-record --help`

Przykład modyfikuje zestaw rekordów CNAME *www* w strefie *contoso.com*, *w grupie zasobów zasobu,* do punktu "www.fabrikam.NET" zamiast istniejącej wartości:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Aby zmodyfikować rekord SOA

W przeciwieństwie do większości innych typów rekordów, zestaw rekordów CNAME może zawierać tylko jeden rekord.  W związku z tym nie można zastąpić bieżącej wartości przez dodanie nowego rekordu i usunięcie istniejącego rekordu, tak jak w przypadku innych typów rekordów.

Zamiast tego, aby zmodyfikować rekord SOA, użyj polecenia `az network dns record-set soa update` . Aby uzyskać pomoc, zobacz `az network dns record-set soa update --help`.

W poniższym przykładzie pokazano, jak ustawić właściwość "email" rekordu SOA dla strefy *contoso.com* *w grupie zasobów*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Aby zmodyfikować rekordy NS w wierzchołku strefy

Rekord NS ustawiony w wierzchołku strefy jest automatycznie tworzony przy użyciu każdej strefy DNS. Zawiera nazwy serwerów nazw Azure DNS przypisanych do strefy.

Do tego zestawu rekordów NS można dodać kolejne serwery nazw, aby obsługiwać domeny współpracujące z więcej niż jednym dostawcą DNS. Możesz również zmodyfikować czas wygaśnięcia i metadane dla tego zestawu rekordów. Nie można jednak usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw Azure DNS.

Należy zauważyć, że ma to zastosowanie tylko do zestawu rekordów NS w wierzchołku strefy. Inne zestawy rekordów NS w strefie (używane do delegowania stref podrzędnych) można modyfikować bez ograniczenia.

Poniższy przykład pokazuje, jak dodać dodatkowy serwer nazw do zestawu rekordów NS na stronie wierzchołka strefy:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Aby zmodyfikować czas wygaśnięcia istniejącego zestawu rekordów

Aby zmodyfikować czas wygaśnięcia istniejącego zestawu rekordów, użyj `azure network dns record-set <record-type> update` . Aby uzyskać pomoc, zobacz `azure network dns record-set <record-type> update --help`.

Poniższy przykład pokazuje, jak zmodyfikować wartość TTL zestawu rekordów, w tym przypadku do 60 sekund:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Aby zmodyfikować metadane istniejącego zestawu rekordów

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) mogą służyć do kojarzenia danych specyficznych dla aplikacji z każdym zestawem rekordów jako par klucz-wartość. Aby zmodyfikować metadane istniejącego zestawu rekordów, użyj `az network dns record-set <record-type> update` . Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> update --help`.

Poniższy przykład pokazuje, jak zmodyfikować zestaw rekordów z dwoma wpisami metadanych "Dept = finanse" i "Environment = Production". Zwróć uwagę, że wszystkie istniejące metadane są *zastępowane* przez określone wartości.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Usuwanie zestawu rekordów

Zestawy rekordów można usuwać za pomocą `az network dns record-set <record-type> delete` polecenia. Aby uzyskać pomoc, zobacz `azure network dns record-set <record-type> delete --help`. Usunięcie zestawu rekordów spowoduje również usunięcie wszystkich rekordów w zestawie rekordów.

> [!NOTE]
> Nie można usunąć zestawów rekordów SOA i NS przy wierzchołku strefy ( `--name "@"` ).  Są one tworzone automatycznie podczas tworzenia strefy i są usuwane automatycznie po usunięciu strefy.

Poniższy przykład usuwa zestaw rekordów o nazwie *www* typu A z *contoso.com* strefy *w grupie zasobów grupy zasobu:*

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Zostanie wyświetlony monit o potwierdzenie operacji usuwania. Aby pominąć ten monit, użyj `--yes` przełącznika.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [strefach i rekordach w Azure DNS](dns-zones-records.md).
<br>
Dowiedz się [, jak chronić strefy i rekordy](dns-protect-zones-recordsets.md) przy użyciu Azure DNS.
