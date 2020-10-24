---
title: Hostowanie stref wyszukiwania wstecznego DNS w Azure DNS
description: Dowiedz się, jak za pomocą Azure DNS hostować strefy wyszukiwania wstecznego DNS dla zakresów adresów IP
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 6a0aebc727233cdd838f3e1bf8eeb5cd247b9836
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489680"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Hostowanie stref wyszukiwania wstecznego DNS w Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym artykule wyjaśniono, jak hostować strefy wyszukiwania wstecznego DNS dla przypisanych zakresów adresów IP w Azure DNS. Zakresy adresów IP reprezentowane przez strefy wyszukiwania wstecznego muszą być przypisane do organizacji, zazwyczaj przez usługodawcę internetowego.

Aby skonfigurować odwrotny serwer DNS dla adresu IP należącego do platformy Azure przypisanego do usługi platformy Azure, zobacz [Konfigurowanie odwrotnego systemu DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).

Przed przeczytaniem tego artykułu należy zapoznać się z [omówieniem odwrotnego systemu DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).

W tym artykule omówiono procedurę tworzenia pierwszej strefy DNS wyszukiwania wstecznego i rekordu przy użyciu Azure Portal, Azure PowerShell, klasycznego interfejsu wiersza polecenia platformy Azure lub interfejsu wiersza polecenia platformy Azure.

## <a name="create-a-reverse-lookup-dns-zone"></a>Tworzenie strefy DNS wyszukiwania wstecznego

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. W menu **centrum** wybierz pozycję **nowe**  >  **sieci**, a następnie wybierz pozycję **strefa DNS**.

   ![Wybór "strefy DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. W okienku **Tworzenie strefy DNS** Nazwij strefę DNS. Nazwa strefy jest przygotowana inaczej w przypadku prefiksów IPv4 i IPv6. Aby nazwać strefę, użyj instrukcji dotyczących [protokołu IPv4](#ipv4) lub [IPv6](#ipv6) . Po zakończeniu wybierz pozycję **Utwórz** , aby utworzyć strefę.

### <a name="ipv4"></a>Protokół IPv4

Nazwa strefy wyszukiwania wstecznego IPv4 jest określana na podstawie zakresu adresów IP, który reprezentuje. Powinien być w następującym formacie: `<IPv4 network prefix in reverse order>.in-addr.arpa` . Przykłady można znaleźć [w temacie Omówienie odwrotnego systemu DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Gdy tworzysz strefy wyszukiwania wstecznego DNS bez klas w Azure DNS, musisz użyć łącznika (), `-` a nie ukośnika ( `/` ) w nazwie strefy.
>
> Na przykład dla zakresu adresów IP 192.0.2.128/26 należy użyć `128-26.2.0.192.in-addr.arpa` jako nazwy strefy zamiast `128/26.2.0.192.in-addr.arpa` .
>
> Chociaż standardy DNS obsługują obie metody, Azure DNS nie obsługuje nazw stref DNS, które zawierają znaki ukośnika ( `/` ).

Poniższy przykład pokazuje, jak utworzyć strefę DNS wstecznej klasy C o nazwie `2.0.192.in-addr.arpa` w Azure DNS za pomocą Azure Portal:

 ![Zrzut ekranu pokazujący sposób tworzenia strefy odwrotnej DNS klasy C o nazwie 2.0.192.in-addr. arpa w Azure DNS przy użyciu Azure Portal.](./media/dns-reverse-dns-hosting/figure2.png)

**Lokalizacja grupy zasobów** określa lokalizację grupy zasobów. Nie ma to wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

W poniższych przykładach pokazano, jak wykonać to zadanie przy użyciu Azure PowerShell i interfejsu wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>Protokół IPv6

Nazwa strefy wyszukiwania wstecznego IPv6 powinna mieć następującą postać: `<IPv6 network prefix in reverse order>.ip6.arpa` .  Przykłady można znaleźć [w temacie Omówienie odwrotnego systemu DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md#ipv6).


Poniższy przykład pokazuje, jak utworzyć strefę wyszukiwania wstecznego DNS IPv6 o nazwie `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` w Azure DNS za pośrednictwem Azure Portal:

 ![Okienko "Tworzenie strefy DNS" z wypełnionymi polami](./media/dns-reverse-dns-hosting/figure3.png)

**Lokalizacja grupy zasobów** określa lokalizację grupy zasobów. Nie ma to wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

W poniższych przykładach pokazano, jak wykonać to zadanie przy użyciu Azure PowerShell i interfejsu wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegowanie strefy wyszukiwania wstecznego DNS

Po utworzeniu strefy wyszukiwania wstecznego DNS musisz upewnić się, że strefa jest delegowana ze strefy nadrzędnej. Delegowanie DNS umożliwia procesowi rozpoznawania nazw DNS znalezienie serwerów nazw, które obsługują strefę wyszukiwania wstecznego DNS. Te serwery nazw mogą następnie odpowiedzieć na kwerendy odwrotne DNS dla adresów IP w zakresie adresów.

W przypadku stref wyszukiwania do przodu proces delegowania strefy DNS jest opisany w obszarze [delegowanie domeny do Azure DNS](dns-delegate-domain-azure-dns.md). Delegowanie stref wyszukiwania wstecznego działa w ten sam sposób. Jedyną różnicą jest to, że należy skonfigurować serwery nazw przy użyciu usługodawcy internetowego, który podał zakres adresów IP, a nie rejestratora nazw domen.

## <a name="create-a-dns-ptr-record"></a>Tworzenie rekordu PTR systemu DNS

### <a name="ipv4"></a>Protokół IPv4

Poniższy przykład przeprowadzi Cię przez proces tworzenia rekordu PTR w odwrotnej strefie DNS w Azure DNS. Aby uzyskać informacje o innych typach rekordów oraz sposobie modyfikowania istniejących rekordów, zobacz [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (Zarządzanie rekordami i zestawami rekordów DNS przy użyciu witryny Azure Portal).

1. W górnej części okienka **strefa DNS** wybierz pozycję **+ zestaw rekordów** , aby otworzyć okienko **Dodawanie zestawu rekordów** .

   ![Zrzut ekranu przedstawiający okienko strefy DNS ze strzałką wskazującą na przycisk + zestaw rekordów.](./media/dns-reverse-dns-hosting/figure4.png)

1. Nazwa zestawu rekordów dla rekordu PTR musi być resztą adresu IPv4 w odwrotnej kolejności. 

   W tym przykładzie pierwsze trzy oktety są już wypełnione jako część nazwy strefy (. 2.0.192). W związku z tym w polu **Nazwa** jest dostarczany tylko ostatni oktet. Na przykład możesz nazwać rekord zestaw **15** dla zasobu, którego adres IP jest 192.0.2.15.  
1. W obszarze **Typ**wybierz pozycję **PTR**.  
1. W polu **nazwa domeny**wprowadź w pełni kwalifikowaną nazwę domeny (FQDN) zasobu, który używa tego adresu IP.
1. Wybierz pozycję **OK** u dołu okienka, aby utworzyć rekord DNS.

   ![Okienko "Dodawanie zestawu rekordów" z wypełnionymi polami](./media/dns-reverse-dns-hosting/figure5.png)

W poniższych przykładach pokazano, jak wykonać to zadanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>Protokół IPv6

Poniższy przykład przeprowadzi Cię przez proces tworzenia nowego rekordu PTR. Aby uzyskać informacje o innych typach rekordów oraz sposobie modyfikowania istniejących rekordów, zobacz [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (Zarządzanie rekordami i zestawami rekordów DNS przy użyciu witryny Azure Portal).

1. W górnej części okienka **strefa DNS** wybierz pozycję **+ zestaw rekordów** , aby otworzyć okienko **Dodawanie zestawu rekordów** .

   ![Przycisk służący do tworzenia zestawu rekordów](./media/dns-reverse-dns-hosting/figure6.png)

2. Nazwa zestawu rekordów dla rekordu PTR musi być resztą adresu IPv6 w odwrotnej kolejności. Nie może zawierać żadnej kompresji zerowej. 

   W tym przykładzie pierwsze 64 bitów protokołu IPv6 jest już wypełnione jako część nazwy strefy (0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2. IP6. arpa). W związku z tym w polu **Nazwa** są podane tylko ostatnie 64 bitów. Ostatnie 64 bitów adresu IP są wprowadzane w odwrotnej kolejności, z kropką jako ogranicznikiem między każdą liczbą szesnastkową. Można na przykład nazwać rekord **5.0.4.9. f. a. 1. c. b. 0.1.4.2.5. f** dla zasobu, którego adres IP to 2001:0db8: ABDC: 0000: f524:10bc: 1af9:405e.  
3. W obszarze **Typ**wybierz pozycję **PTR**.  
4. W polu **nazwa domeny wprowadź nazwę**FQDN zasobu, który używa adresu IP.
5. Wybierz pozycję **OK** u dołu okienka, aby utworzyć rekord DNS.

![Zrzut ekranu pokazujący okienko "Dodawanie zestawu rekordów" z strzałką wskazującą wartość w polu Typ.](./media/dns-reverse-dns-hosting/figure7.png)

W poniższych przykładach pokazano, jak wykonać to zadanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Wyświetlanie rekordów

Aby wyświetlić utworzone rekordy, przejdź do strefy DNS w Azure Portal. W dolnej części okienka **strefa DNS** można zobaczyć rekordy dla strefy DNS. Powinny zostać wyświetlone domyślne rekordy NS i SOA oraz wszystkie nowe rekordy, które zostały utworzone. Rekordy NS i SOA są tworzone w każdej strefie. 

### <a name="ipv4"></a>Protokół IPv4

W okienku **strefa DNS** są wyświetlane rekordy PTR rekordu IPv4:

![Okienko "strefa DNS" z rekordami IPv4](./media/dns-reverse-dns-hosting/figure8.png)

W poniższych przykładach pokazano, jak wyświetlić rekordy PTR przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>Protokół IPv6

W okienku **strefa DNS** są wyświetlane rekordy PTR systemu IPv6:

![Okienko "strefa DNS" z rekordami IPv6](./media/dns-reverse-dns-hosting/figure9.png)

W poniższych przykładach pokazano, jak wyświetlić rekordy przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Często zadawane pytania

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Czy można hostować strefy wyszukiwania wstecznego DNS dla moich bloków IP przypisanych przez usługodawcę internetowego na Azure DNS?

Tak. Hostowanie stref wyszukiwania wstecznego (ARPA) dla własnych zakresów adresów IP w Azure DNS jest w pełni obsługiwane.

Utwórz strefę wyszukiwania wstecznego w Azure DNS zgodnie z opisem w tym artykule, a następnie skontaktuj się z usługodawcą internetowym w celu [delegowania strefy](dns-domain-delegation.md). Następnie można zarządzać rekordami PTR dla każdego wyszukiwania wstecznego w taki sam sposób jak w przypadku innych typów rekordów.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Ile wynosi koszt strefy wyszukiwania wstecznego DNS?

Obsługa strefy wyszukiwania wstecznego DNS dla bloku IP przypisanego przez usługodawcę internetowego w Azure DNS jest naliczana według [standardowych stawek za Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Czy można hostować strefy wyszukiwania wstecznego DNS dla adresów IPv4 i IPv6 w Azure DNS?

Tak. W tym artykule wyjaśniono, jak utworzyć strefy wyszukiwania wstecznego DNS IPv4 i IPv6 w Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Czy mogę zaimportować istniejącą strefę wyszukiwania wstecznego DNS?

Tak. Możesz użyć interfejsu wiersza polecenia platformy Azure, aby zaimportować istniejące strefy DNS do Azure DNS. Ta metoda działa w przypadku stref wyszukiwania do przodu i stref wyszukiwania wstecznego.

Aby uzyskać więcej informacji, zobacz [Importowanie i eksportowanie pliku strefy DNS przy użyciu interfejsu wiersza polecenia platformy Azure](dns-import-export.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat odwrotnej usługi DNS, zobacz [odwrotne wyszukiwanie DNS w witrynie Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się, jak [zarządzać rekordami odwrotnego systemu DNS dla usług platformy Azure](dns-reverse-dns-for-azure-services.md).
