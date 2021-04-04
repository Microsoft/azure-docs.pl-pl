---
title: Importowanie i eksportowanie pliku strefy domeny — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure DNS
description: Dowiedz się, jak importować i eksportować plik strefy DNS do Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure
services: dns
author: rohinkoul
ms.service: dns
ms.date: 7/30/2020
ms.author: rohink
ms.topic: how-to
ms.openlocfilehash: e2b998432f6c4417da0242d86347ed43acb5071a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968234"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importowanie i eksportowanie pliku strefy systemu DNS za pomocą interfejsu wiersza polecenia platformy Azure

W tym artykule omówiono sposób importowania i eksportowania plików stref DNS dla Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="introduction-to-dns-zone-migration"></a>Wprowadzenie do migracji strefy DNS

Plik strefy DNS jest plikiem tekstowym zawierającym szczegóły każdego rekordu systemu nazw domen (DNS) w strefie. Jest on zgodny z formatem standardowym, co umożliwia przesyłanie rekordów DNS między systemami DNS. Korzystanie z pliku strefy to szybka, niezawodna i wygodna metoda transferu strefy DNS do lub z Azure DNS.

Azure DNS obsługuje importowanie i eksportowanie plików strefy przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure. Importowanie pliku strefy **nie** jest obecnie obsługiwane przez Azure PowerShell lub Azure Portal.

Interfejs wiersza polecenia platformy Azure to międzyplatformowe narzędzie do zarządzania usługami platformy Azure. Jest ona dostępna dla platform systemów Windows, Mac i Linux ze [strony plików do pobrania platformy Azure](https://azure.microsoft.com/downloads/). Obsługa wielu platform jest ważna w przypadku importowania i eksportowania plików stref, ponieważ najbardziej typowe oprogramowanie serwera nazw, [bind](https://www.isc.org/downloads/bind/), zazwyczaj działa w systemie Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Uzyskaj istniejący plik strefy DNS

Przed zaimportowaniem pliku strefy DNS do Azure DNS należy uzyskać kopię pliku strefy. Źródło tego pliku zależy od lokalizacji, w której jest obecnie hostowana strefa DNS.

* Jeśli strefa DNS jest hostowana przez usługę partnera (taką jak rejestrator domeny, dedykowany dostawca hostingu DNS lub alternatywny dostawca usług w chmurze), ta usługa powinna zapewnić możliwość pobrania pliku strefy DNS.
* Jeśli strefa DNS jest hostowana w systemie DNS systemu Windows, domyślnym folderem dla plików strefy jest **%SystemRoot%\System32\Dns**. Pełna ścieżka do każdego pliku strefy jest również wyświetlana na karcie **Ogólne** konsoli DNS.
* Jeśli strefa DNS jest hostowana przy użyciu funkcji BIND, lokalizacja pliku strefy dla każdej strefy jest określona w pliku konfiguracji powiązania **o nazwie. conf**.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Zaimportuj plik strefy DNS do Azure DNS

Importowanie pliku strefy tworzy nową strefę w Azure DNS, jeśli jeszcze nie istnieje. Jeśli strefa już istnieje, zestawy rekordów w pliku strefy muszą zostać scalone z istniejącymi zestawami rekordów.

### <a name="merge-behavior"></a>Zachowanie scalania

* Domyślnie są scalane istniejące i nowe zestawy rekordów. Identyczne rekordy w scalonym zestawie rekordów są deduplikowane.
* Gdy zestawy rekordów są scalane, używany jest czas wygaśnięcia (TTL) istniejących zestawów rekordów.
* Parametry "SOA" urzędu certyfikacji (z wyjątkiem `host` ) są zawsze pobierane z zaimportowanego pliku strefy. Podobnie w przypadku ustawienia rekordu serwera nazw w wierzchołku strefy czas wygaśnięcia jest zawsze pobierany z zaimportowanego pliku strefy.
* Zaimportowany rekord CNAME nie zastępuje istniejącego rekordu CNAME o tej samej nazwie.  
* Gdy występuje konflikt między rekordem CNAME a innym rekordem o tej samej nazwie, ale innym typie (niezależnie od tego, który jest istniejący lub nowy), istniejący rekord jest zachowywany. 

### <a name="additional-information-about-importing"></a>Dodatkowe informacje na temat importowania

Poniższe uwagi zawierają dodatkowe szczegółowe informacje techniczne o procesie importowania strefy.

* `$TTL`Dyrektywa jest opcjonalna i jest obsługiwana. Gdy nie `$TTL` zostanie określona żadna dyrektywa, rekordy bez jawnego czasu wygaśnięcia są importowane jako domyślny czas wygaśnięcia wynoszący 3600 sekund. Gdy dwa rekordy w tym samym zestawie rekordów określają różne TTLs, używana jest dolna wartość.
* `$ORIGIN`Dyrektywa jest opcjonalna i jest obsługiwana. Gdy `$ORIGIN` wartość nie jest ustawiona, używana jest domyślnie Nazwa strefy określona w wierszu polecenia (plus ".").
* `$INCLUDE`Dyrektywy i `$GENERATE` nie są obsługiwane.
* Te typy rekordów są obsługiwane: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV i TXT.
* Rekord SOA jest tworzony automatycznie przez Azure DNS podczas tworzenia strefy. Podczas importowania pliku strefy wszystkie parametry SOA są pobierane z pliku strefy, *z wyjątkiem* `host` parametru. Ten parametr używa wartości dostarczonej przez Azure DNS. Jest to spowodowane tym, że ten parametr musi odwoływać się do podstawowego serwera nazw dostarczonego przez Azure DNS.
* Rekord serwera nazw ustawiony w wierzchołku strefy również jest tworzony automatycznie przez Azure DNS podczas tworzenia strefy. Zaimportowany jest tylko czas wygaśnięcia tego zestawu rekordów. Te rekordy zawierają nazwy serwerów podane przez Azure DNS. Dane rekordu nie są zastępowane przez wartości zawarte w zaimportowanym pliku strefy.
* W publicznej wersji zapoznawczej Azure DNS obsługuje tylko rekordy TXT z pojedynczym ciągiem. Rekordy wielociągów TXT są łączone i obcinane do 255 znaków.

### <a name="cli-format-and-values"></a>Format i wartości interfejsu wiersza polecenia

Format polecenia platformy Azure w celu zaimportowania strefy DNS to:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Wartości:

* `<resource group>` jest nazwą grupy zasobów dla strefy w Azure DNS.
* `<zone name>` jest nazwą strefy.
* `<zone file name>` jest ścieżką/nazwą pliku strefy do zaimportowania.

Jeśli strefa o tej nazwie nie istnieje w grupie zasobów, zostanie utworzona dla Ciebie. Jeśli strefa już istnieje, zaimportowane zestawy rekordów są scalane z istniejącymi zestawami rekordów. 

### <a name="step-1-import-a-zone-file"></a>Krok 1. Importuj plik strefy

Aby zaimportować plik strefy dla strefy **contoso.com**.

1. Jeśli jeszcze tego nie zrobiono, należy utworzyć Menedżer zasobów grupę zasobów.

    ```azurecli
    az group create --resource-group myresourcegroup -l westeurope
    ```

2. Do zaimportowania strefy **contoso.com** z pliku **contoso.com.txt** do nowej strefy DNS **w grupie zasobów zasób,** zostanie uruchomione polecenie `az network dns zone import` .<BR>To polecenie ładuje plik strefy i przeanalizuje go. Polecenie wykonuje serię poleceń w usłudze Azure DNS, aby utworzyć strefę i wszystkie zestawy rekordów w strefie. Polecenie raportuje postęp w oknie konsoli wraz z wszelkimi błędami lub ostrzeżeniami. Ponieważ zestawy rekordów są tworzone w serii, importowanie dużych plików stref może potrwać kilka minut.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Krok 2. Weryfikowanie strefy

Aby sprawdzić strefę DNS po zaimportowaniu pliku, można użyć jednej z następujących metod:

* Można wyświetlić listę rekordów przy użyciu następującego polecenia interfejsu CLI platformy Azure:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Możesz wyświetlić listę rekordów za pomocą polecenia interfejsu CLI platformy Azure `az network dns record-set ns list` .
* Można użyć, `nslookup` Aby sprawdzić rozpoznawanie nazw dla rekordów. Ze względu na to, że strefa nie jest jeszcze delegowana, należy jawnie określić poprawne Azure DNS serwerów nazw. Poniższy przykład pokazuje, jak pobrać nazwy serwerów nazw przypisane do strefy. Przedstawiono w nim również zapytania dotyczące rekordu "www" przy użyciu programu `nslookup` .

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>Krok 3. Aktualizowanie delegowania DNS

Po sprawdzeniu, czy strefa została zaimportowana prawidłowo, należy zaktualizować delegowanie DNS w taki sposób, aby wskazywały serwery nazw Azure DNS. Aby uzyskać więcej informacji, zobacz artykuł [Aktualizacja delegowania DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Eksportowanie pliku strefy DNS z Azure DNS

Format polecenia platformy Azure w celu wyeksportowania strefy DNS to:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Wartości:

* `<resource group>` jest nazwą grupy zasobów dla strefy w Azure DNS.
* `<zone name>` jest nazwą strefy.
* `<zone file name>` jest ścieżką/nazwą pliku strefy do wyeksportowania.

Tak jak w przypadku importowania strefy najpierw musisz się zalogować, wybrać subskrypcję i skonfigurować interfejs wiersza polecenia platformy Azure do używania trybu Menedżer zasobów.

### <a name="to-export-a-zone-file"></a>Aby wyeksportować plik strefy

Aby wyeksportować istniejącą strefę Azure DNS **contoso.com** **w grupie zasobów zasób do** **contoso.com.txt** pliku (w bieżącym folderze), uruchom polecenie `azure network dns zone export` . To polecenie wywołuje usługę Azure DNS do wyliczania zestawów rekordów w strefie i eksportuje wyniki do pliku strefy zgodnej z POWIĄZANIEm.

```azurecli
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak zarządzać zestawami rekordów i rekordami](./dns-getstarted-cli.md) w strefie DNS.

* Dowiedz się, jak [delegować domenę do Azure DNS](dns-domain-delegation.md).