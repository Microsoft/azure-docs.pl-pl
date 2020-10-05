---
title: 'Szybki Start: Tworzenie strefy Azure DNS i rekordu — Azure PowerShell'
titleSuffix: Azure DNS
description: Dowiedz się, jak utworzyć strefę i rekord DNS w usłudze Azure DNS. W tym szczegółowym przewodniku Szybki start pokazano, jak po raz pierwszy utworzyć strefę i rekord DNS przy użyciu programu Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7361f58247019584d7fb3d60cdd7f76b2e62a212
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91355486"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Szybki start — tworzenie strefy i rekordu usługi Azure DNS przy użyciu programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym przewodniku Szybki start utworzysz po raz pierwszy strefę i rekord DNS przy użyciu programu Azure PowerShell. Te kroki można również wykonać przy użyciu witryny [Azure Portal](dns-getstarted-portal.md) lub [interfejsu wiersza polecenia platformy Azure](dns-getstarted-cli.md). 

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby na koniec opublikować strefę DNS w Internecie, należy skonfigurować serwery nazw dla domeny. Poniżej opisano każdy z tych kroków.

Usługa Azure DNS obsługuje też tworzenie domen prywatnych. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia pierwszej prywatnej strefy DNS i pierwszego rekordu, zobacz [Rozpoczynanie pracy ze strefami prywatnymi usługi Azure DNS przy użyciu programu PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem strefy DNS należy utworzyć dla niej grupę zasobów:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Strefa DNS jest tworzona za pomocą polecenia cmdlet `New-AzDnsZone`. Poniższy przykład tworzy strefę DNS o nazwie *contoso. xyz* w grupie zasobów o nazwie Moja *resourceName*. Skorzystaj z tego przykładu, aby utworzyć strefę DNS, podstawiając własne wartości.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Zestawy rekordów są tworzone za pomocą polecenia cmdlet `New-AzDnsRecordSet`. W poniższym przykładzie jest tworzony rekord o nazwie względnej "www" w strefie DNS "contoso. xyz" w grupie zasobów "Moje zasoby". W pełni kwalifikowana nazwa zestawu rekordów to "www. contoso. xyz". Typ rekordu to "A" z adresem IP "10.10.10.10", a czas wygaśnięcia wynosi 3600 sekund.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>Wyświetlanie rekordów

Aby wyświetlić listę rekordów DNS w strefie, należy użyć:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>Testowanie rozpoznawania nazw

Po utworzeniu testowej strefy DNS z rekordem „A” możesz przetestować rozpoznawanie nazw za pomocą narzędzia o nazwie *nslookup*. 

**Aby przetestować rozpoznawanie nazw DNS:**

1. Uruchom następujące polecenie cmdlet, aby uzyskać listę serwerów nazw dla strefy:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. Skopiuj jedną z nazw serwerów nazw z danych wyjściowych poprzedniego kroku.

1. Otwórz wiersz polecenia i uruchom następujące polecenie:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Na przykład:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Powinna zostać wyświetlona treść podobna do tej na następującym ekranie:

   ![Zrzut ekranu przedstawia okno wiersza polecenia z n s poleceniem wyszukiwania i wartości dla opcji serwer, adres, nazwa i adres.](media/dns-getstarted-portal/nslookup.PNG)

Nazwa hosta **www \. contoso. xyz** jest rozpoznawana jako **10.10.10.10**, tak jak została skonfigurowana. Taki wynik potwierdza, że rozpoznawanie nazw działa poprawnie.

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów

Jeśli zasoby utworzone w tym przewodniku Szybki start nie są już potrzebne, możesz je usunąć, usuwając grupę zasobów:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu strefy i rekordu DNS po raz pierwszy przy użyciu programu Azure PowerShell możesz utworzyć rekordy dla aplikacji internetowej w domenie niestandardowej.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)

