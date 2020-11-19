---
title: Maszyny wirtualne Korzyść użycia hybrydowego platformy Azure i Linux
description: Korzyść użycia hybrydowego platformy Azure pozwala zaoszczędzić pieniądze na maszynach wirtualnych z systemem Linux działających na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: a26a618d4f1bd9900136561d95c21c1ecbaadfaa
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916478"
---
# <a name="azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Korzyść użycia hybrydowego platformy Azure — jak ma zastosowanie do Linux Virtual Machines

## <a name="overview"></a>Omówienie

Korzyść użycia hybrydowego platformy Azure umożliwia łatwiejsze Migrowanie lokalnych maszyn wirtualnych Red Hat Enterprise Linux (RHEL) i SUSE Linux Enterprise Server (SLES) na platformę Azure przy użyciu własnej istniejącej subskrypcji Red Hat lub SUSE. Korzystając z tej korzyści, płacisz tylko za koszty infrastruktury maszyny wirtualnej, ponieważ opłata za oprogramowanie jest objęta subskrypcją usługi RHEL lub SLES. Korzyść ma zastosowanie do wszystkich obrazów RHEL i SLES Marketplace z opcją płatność zgodnie z rzeczywistym użyciem.

> [!IMPORTANT]
> Korzyść użycia hybrydowego platformy Azure dla maszyn wirtualnych z systemem Linux jest teraz dostępna publicznie


## <a name="benefit-description"></a>Opis korzyści

Za pośrednictwem Korzyść użycia hybrydowego platformy Azure można łatwiej migrować lokalne serwery RHEL i SLES na platformę Azure, konwertując istniejące maszyny wirtualne RHEL i SLES PAYG na platformę Azure, aby przenieść rozliczenia własnej subskrypcji (BYOS). Zazwyczaj maszyny wirtualne wdrożone z obrazów PAYG na platformie Azure będą naliczane opłaty za infrastrukturę oraz opłaty za oprogramowanie. Za pomocą Korzyść użycia hybrydowego platformy Azure maszyny wirtualne PAYG można przekonwertować na model rozliczeń BYOS bez konieczności ponownego wdrażania, unikając ryzyka przestoju.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Korzyść użycia hybrydowego platformy Azure wizualizacji kosztów na maszynach wirtualnych z systemem Linux.":::

Po umożliwieniu skorzystania z usługi RHEL lub SLES maszyny wirtualnej nie będą już naliczane opłaty za dodatkowy koszt oprogramowania zwykle ponoszony na maszynę wirtualną PAYG. Zamiast tego maszyna wirtualna rozpocznie emitowanie opłaty za BYOS, która obejmuje tylko opłaty za sprzęt i opłaty za oprogramowanie.

Jeśli wolisz, możesz również skonwertować maszynę wirtualną, na której włączono korzyść z powrotem do modelu rozliczania PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Zakres Korzyść użycia hybrydowego platformy Azure uprawnień dla maszyn wirtualnych z systemem Linux

Korzyść użycia hybrydowego platformy Azure jest dostępny dla wszystkich obrazów programu RHEL i SLES w portalu Marketplace. Korzyść nie jest jeszcze dostępna dla obrazów RHEL lub SLES Marketplace.

Wystąpienia zarezerwowane, dedykowane hosty i korzyści z używania hybrydowej bazy danych SQL nie kwalifikują się do Korzyść użycia hybrydowego platformy Azure, jeśli jest już używana korzyść z maszynami wirtualnymi z systemem Linux.

## <a name="how-to-get-started"></a>Jak zacząć

### <a name="red-hat-customers"></a>Klienci firmy Red Hat

Korzyść użycia hybrydowego platformy Azure dla RHEL jest dostępny dla klientów, którzy mają aktywne/nieużywane subskrypcje RHEL, których można używać na platformie Azure i którzy włączyli jedną lub więcej z tych subskrypcji do użycia na platformie Azure przy użyciu programu [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) . 

1.  Włącz co najmniej jedną kwalifikującą się subskrypcje RHEL do użycia na platformie Azure przy użyciu [interfejsu klienta usługi Red Hat Cloud Access](https://access.redhat.com/management/cloud).
1.  Subskrypcje platformy Azure podane w procesie włączania dostępu w chmurze Red Hat będą mogły korzystać z funkcji Korzyść użycia hybrydowego platformy Azure.
1.  Zastosuj Korzyść użycia hybrydowego platformy Azure do dowolnych istniejących maszyn wirtualnych RHEL PAYG, a także wszelkich nowych maszyn wirtualnych RHEL, które są wdrażane na podstawie obrazów z witryny Azure Marketplace.
1.  Postępuj zgodnie z zalecanymi [następnymi krokami](https://access.redhat.com/articles/5419341) dotyczącymi konfigurowania źródeł aktualizacji dla maszyn wirtualnych RHEL oraz wytycznych dotyczących zgodności subskrypcji RHEL.


### <a name="suse-customers"></a>Klienci SUSE

1.    Rejestrowanie w programie SUSE Public Cloud
1.    Zastosuj korzyść dla istniejących maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure
1.    Zarejestruj swoje maszyny wirtualne otrzymujące korzyść przy użyciu oddzielnego źródła aktualizacji


### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Włączanie i wyłączanie korzyści w interfejsie wiersza polecenia platformy Azure

Aby zaktualizować istniejące maszyny wirtualne, możesz użyć polecenia "AZ VM Update". W przypadku maszyn wirtualnych RHEL Uruchom polecenie z parametrem--Type licencji "RHEL_BYOS". W przypadku maszyn wirtualnych SLES Uruchom polecenie z parametrem--Type licencji "SLES_BYOS".

#### <a name="cli-example-to-enable-the-benefit"></a>Przykład interfejsu wiersza polecenia, aby włączyć korzyść:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>Przykład interfejsu wiersza polecenia do wyłączania korzyści:
Aby wyłączyć korzyść, użyj wartości "none" w typie licencji
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Przykład interfejsu wiersza polecenia, aby włączyć korzyść na dużej liczbie maszyn wirtualnych
Aby zapewnić korzyść na wielu maszynach wirtualnych, możesz użyć `--ids` parametru w interfejsie wiersza polecenia platformy Azure.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

W poniższych przykładach przedstawiono dwie metody uzyskiwania listy identyfikatorów zasobów — jeden na poziomie grupy zasobów, jeden na poziomie subskrypcji.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>Sprawdź stan AHB maszyny wirtualnej
Stan AHB maszyny wirtualnej można wyświetlić na dwa sposoby: za pomocą interfejsu wiersza polecenia platformy Azure lub przy użyciu usługi Azure Instance Metadata Service (Azure IMDS).


### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W `az vm get-instance-view` tym celu można użyć polecenia. Wyszukaj w odpowiedzi pole LicenseType. Jeśli pole LicenseType istnieje, a wartość to "RHEL_BYOS" lub "SLES_BYOS", na maszynie wirtualnej jest włączona korzyść.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Z poziomu samej maszyny wirtualnej możesz wysyłać zapytania do IMDS zaświadczania metadanych, aby określić wartość LicenseType maszyny wirtualnej. Wartość LicenseType elementu "RHEL_BYOS" lub "SLES_BYOS" wskazuje, że na maszynie wirtualnej jest włączona korzyść. Dowiedz się więcej o zaświadczeniu metadanych [tutaj](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Zgodność

### <a name="red-hat"></a>Red Hat

Klienci korzystający z Korzyść użycia hybrydowego platformy Azure dla RHEL akceptują standardowe [warunki prawne](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) i [zasady zachowania poufności informacji](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) powiązane z ofertami RHEL w portalu Azure Marketplace.

Klienci korzystający z programu Korzyść użycia hybrydowego platformy Azure for RHEL mają trzy opcje zapewniania aktualizacji oprogramowania i poprawek do tych maszyn wirtualnych:

1.  [Infrastruktura aktualizacji Red Hat (RHUI)](../workloads/redhat/redhat-rhui.md) (opcja domyślna)
1.  Serwer satelitarny Red Hat
1.  Menedżer subskrypcji Red Hat

Klienci wybierający opcję RHUI mogą w dalszym ciągu używać RHUI jako głównego źródła aktualizacji dla maszyn wirtualnych AHB RHEL, bez dołączania subskrypcji RHEL do tych maszyn wirtualnych.  Klienci wybierający opcję RHUI są odpowiedzialni za zapewnienie zgodności z subskrypcją RHEL.

Klienci wybierający serwer Red Hat satelitarnych lub Red Hat Subscription Manager powinni usunąć konfigurację RHUI, a następnie dołączyć subskrypcję RHEL z włączoną obsługą chmury do swoich maszyn wirtualnych AHB RHEL.  

Więcej informacji na temat zgodności subskrypcji Red Hat, aktualizacji oprogramowania i źródeł dla maszyn wirtualnych AHB RHEL można znaleźć [tutaj](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Aby można było używać Korzyść użycia hybrydowego platformy Azure dla maszyn wirtualnych SLES, należy najpierw zarejestrować program w chmurze publicznej firmy SUSE. Więcej informacji na temat programu znajdziesz tutaj. Gdy zakupiono subskrypcje SUSE, należy zarejestrować maszyny wirtualne przy użyciu tych subskrypcji w ramach własnych źródeł aktualizacji za pomocą centrum klienta SUSE, serwera narzędzia do zarządzania subskrypcjami lub programu SUSE Manager.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
*P: Czy można użyć typu licencji "RHEL_BYOS" z obrazem SLES lub na odwrót?*

Odp.: nie można. Próba wprowadzenia typu licencji, która nieprawidłowo pasuje do dystrybucji uruchomionego na maszynie wirtualnej, nie spowoduje aktualizacji żadnych metadanych rozliczeń. Jeśli jednak przypadkowo wprowadzisz niewłaściwy typ licencji, zaktualizowanie maszyny wirtualnej do poprawnego typu licencji nadal umożliwi korzystanie z tej korzyści.

*Pytanie: zarejestrowano w systemie Red Hat Cloud Access, ale nadal nie można włączyć korzyści na maszynach wirtualnych RHEL. Co mam zrobić?*

Odp.: może upłynąć trochę czasu na rejestrację subskrypcji usługi Red Hat Cloud Access w firmie Red Hat na platformie Azure. Jeśli po jednym dniu roboczym nadal widzisz błąd, skontaktuj się z pomocą techniczną firmy Microsoft.

## <a name="common-issues"></a>Typowe problemy
Ta sekcja zawiera listę typowych problemów, które mogą zostać napotkane, oraz czynności zaradcze.

| Błąd | Ograniczanie ryzyka |
| ----- | ---------- |
| "Akcja nie może zostać ukończona, ponieważ nasze rekordy pokazują, że nie udało Ci się pomyślnie włączyć usługi Red Hat Cloud Access w ramach subskrypcji platformy Azure..." | Aby można było korzystać z zalet z maszynami wirtualnymi RHEL, należy najpierw zarejestrować subskrypcje platformy Azure przy użyciu usługi Red Hat Cloud Access. Odwiedź ten link, aby dowiedzieć się więcej o rejestrowaniu subskrypcji platformy Azure na potrzeby usługi Red Hat Cloud Access

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak tworzyć i aktualizować maszyny wirtualne oraz dodawać typy licencji (RHEL_BYOS, SLES_BYOS) dla Korzyść użycia hybrydowego platformy Azure przy użyciu [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest&preserve-view=true)
