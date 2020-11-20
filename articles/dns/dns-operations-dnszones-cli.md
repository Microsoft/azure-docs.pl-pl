---
title: Zarządzanie strefami DNS w Azure DNS — interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: Strefami DNS można zarządzać przy użyciu interfejsu wiersza polecenia platformy Azure. W tym artykule pokazano, jak aktualizować, usuwać i tworzyć strefy DNS na Azure DNS.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 469fdbced4601c6ffb4b6aa35e8f943bcb5dde1b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965786"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Jak zarządzać Strefy DNS w Azure DNS za pomocą interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Program PowerShell](dns-operations-dnszones.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-dnszones-cli.md)


W tym przewodniku pokazano, jak zarządzać strefami DNS przy użyciu międzyplatformowego interfejsu wiersza polecenia platformy Azure, który jest dostępny dla systemów Windows, Mac i Linux. Możesz również zarządzać strefami DNS przy użyciu [Azure PowerShell](dns-operations-dnszones.md) lub Azure Portal.

W tym przewodniku zawarto specjalne strefy DNS. Aby uzyskać informacje na temat używania interfejsu wiersza polecenia platformy Azure do zarządzania strefami prywatnymi w Azure DNS, zobacz [wprowadzenie do Azure DNS Private Zones przy użyciu interfejsu wiersza polecenia platformy Azure](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Wprowadzenie

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure dla usługi Azure DNS

### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

* Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure, który jest dostępny dla systemów Windows, Linux i MAC. Więcej informacji znajduje się w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Zaloguj się do swojego konta platformy Azure

Otwórz okno konsoli i uwierzytelnij się przy użyciu swoich poświadczeń. Aby uzyskać więcej informacji, zobacz [Logowanie do platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```
az login
```

### <a name="select-the-subscription"></a>Wybierz subskrypcję

Sprawdź subskrypcje dostępne na koncie.

```
az account list
```

Wybierz subskrypcję platformy Azure do użycia.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature"></a>Opcjonalnie: Aby zainstalować/użyć funkcji Azure DNS Private Zones
Funkcja strefy prywatnej Azure DNS jest dostępna za pośrednictwem rozszerzenia interfejsu wiersza polecenia platformy Azure. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi „dns” 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Usługa Azure Resource Manager wymaga, żeby wszystkie grupy zasobów miały lokalizację. Będzie ona używana jako domyślna lokalizacja zasobów w danej grupie. Ponieważ jednak wszystkie zasoby DNS są globalne, a nie regionalne, wybór lokalizacji grupy zasobów nie ma wpływu na usługę Azure DNS.

Ten krok można pominąć, jeśli używasz istniejącej grupy zasobów.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Uzyskiwanie pomocy

Wszystkie poleceń interfejsu wiersza polecenia platformy Azure odnoszące się do Azure DNS zaczynają się od `az network dns` . Pomoc jest dostępna dla każdego polecenia przy użyciu `--help` opcji (krótka wersja `-h` ).  Przykład:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Do tworzenia strefy DNS służy polecenie `az network dns zone create`. Aby uzyskać pomoc, zobacz `az network dns zone create -h`.

Poniższy przykład tworzy strefę DNS o nazwie *contoso.com* w grupie zasobów o nazwie Moja *resourceName*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Aby utworzyć strefę DNS ze znacznikami

Poniższy przykład pokazuje, jak utworzyć strefę DNS z dwoma [Azure Resource Manager tagami](dns-zones-records.md#tags), *Project = Demonstracja* i *ENV = test*, przy użyciu `--tags` parametru (Short Form `-t` ):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Pobieranie strefy DNS

Aby pobrać strefę DNS, użyj `az network dns zone show` . Aby uzyskać pomoc, zobacz `az network dns zone show --help`.

Poniższy przykład zwraca *contoso.com* strefy DNS i skojarzone z nią dane *z grupy zasobów*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

Odpowiedzią jest poniższy przykład.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Należy pamiętać, że rekordy DNS nie są zwracane przez polecenie `az network dns zone show`. Aby wyświetlić listę rekordów DNS, użyj polecenia `az network dns record-set list`.


## <a name="list-dns-zones"></a>Wyświetlanie listy stref DNS

Aby wyliczyć strefy DNS, należy użyć polecenia `az network dns zone list`. Aby uzyskać pomoc, zobacz `az network dns zone list --help`.

Określenie grupy zasobów powoduje wyświetlenie listy tylko następujących stref w grupie zasobów:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Pominięcie grupy zasobów powoduje wyświetlenie listy wszystkich stref w subskrypcji:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Aktualizowanie strefy DNS

Zmiany w zasobie strefy DNS można wprowadzić przy użyciu polecenia `az network dns zone update`. Aby uzyskać pomoc, zobacz `az network dns zone update --help`.

To polecenie nie powoduje aktualizacji żadnego z zestawów rekordów DNS w strefie (zobacz [Jak zarządzać rekordami DNS](dns-operations-recordsets-cli.md)). Służy ono wyłącznie do aktualizacji właściwości samego zasobu strefy. Te właściwości są obecnie ograniczone do [Azure Resource Manager "tagów"](dns-zones-records.md#tags) dla zasobu strefy.

Poniższy przykład pokazuje, jak zaktualizować znaczniki w strefie DNS. Istniejące Tagi są zastępowane przez określoną wartość.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Usuwanie strefy DNS

Strefy DNS można usunąć przy użyciu polecenia `az network dns zone delete`. Aby uzyskać pomoc, zobacz `az network dns zone delete --help`.

> [!NOTE]
> Usunięcie strefy DNS powoduje również usunięcie wszystkich rekordów DNS w strefie. Tej operacji nie można cofnąć. Jeśli strefa DNS jest używana, po jej usunięciu usługi korzystające z tej strefy będą kończyć się niepowodzeniem.
>
>Aby zapobiec przypadkowemu usunięciu strefy, zobacz [jak chronić strefy i rekordy DNS](dns-protect-zones-recordsets.md).

To polecenie wyświetla monit o potwierdzenie. Opcjonalny przełącznik `--yes` pomija ten monit.

Poniższy przykład pokazuje, jak usunąć strefę *contoso.com* *z grupy zasobów*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak zarządzać zestawami rekordów i rekordami](./dns-getstarted-cli.md) w strefie DNS.

Dowiedz się, jak [delegować domenę do Azure DNS](dns-domain-delegation.md).