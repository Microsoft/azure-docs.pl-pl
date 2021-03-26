---
title: Maszyny wirtualne Korzyść użycia hybrydowego platformy Azure i Linux
description: Dowiedz się, jak Korzyść użycia hybrydowego platformy Azure może pomóc w zaoszczędzeniu pieniędzy na maszynach wirtualnych z systemem Linux działających na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 73747222b9131fa85ae6ac01c9dedd5b0bbe1d63
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543413"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Jak Korzyść użycia hybrydowego platformy Azure ma zastosowanie w przypadku maszyn wirtualnych z systemem Linux

Korzyść użycia hybrydowego platformy Azure jest korzyścią dla licencjonowania, która pomaga znacząco obniżyć koszty uruchamiania maszyn wirtualnych Red Hat Enterprise Linux (RHEL) i SUSE Linux Enterprise Server (SLES) w chmurze. Dzięki tym korzyściom płacisz tylko za koszty infrastruktury maszyny wirtualnej, ponieważ subskrypcja usługi RHEL lub SLES obejmuje opłatę za oprogramowanie. Korzyść jest dostępna dla wszystkich obrazów RHEL i SLES Marketplace z opcją płatność zgodnie z rzeczywistym użyciem.

Korzyść użycia hybrydowego platformy Azure dla maszyn wirtualnych z systemem Linux jest teraz publicznie dostępna.

## <a name="benefit-description"></a>Opis korzyści

Za pośrednictwem Korzyść użycia hybrydowego platformy Azure można migrować lokalne serwery RHEL i SLES na platformę Azure, konwertując istniejące maszyny wirtualne RHEL i SLES PAYG na platformę Azure, aby przenieść rozliczenia własnej subskrypcji (BYOS). Zazwyczaj maszyny wirtualne wdrożone z obrazów PAYG na platformie Azure będą pobierać opłaty za infrastrukturę i opłaty za oprogramowanie. Za pomocą Korzyść użycia hybrydowego platformy Azure maszyny wirtualne PAYG można przekonwertować na model rozliczeń BYOS bez ponownego wdrożenia, aby uniknąć ryzyka przestoju.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Korzyść użycia hybrydowego platformy Azure wizualizacji kosztów na maszynach wirtualnych z systemem Linux.":::

Po włączeniu korzyści na maszynie wirtualnej RHEL lub SLES nie będą już naliczane opłaty za dodatkową opłatą za oprogramowanie, zazwyczaj ponoszone na maszynę wirtualną PAYG. Zamiast tego maszyna wirtualna zacznie naliczać opłatę za BYOS, która obejmuje tylko opłaty za sprzęt i opłaty za oprogramowanie.

Możesz również wybrać konwersję maszyny wirtualnej, na której włączono korzyść z powrotem do modelu rozliczania PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Zakres Korzyść użycia hybrydowego platformy Azure uprawnień dla maszyn wirtualnych z systemem Linux

Korzyść użycia hybrydowego platformy Azure jest dostępny dla wszystkich obrazów RHEL i SLES PAYG z witryny Azure Marketplace. Korzyść nie jest jeszcze dostępna dla obrazów RHEL lub SLES BYOS lub obrazów niestandardowych w witrynie Azure Marketplace.

Wystąpienia dedykowanego hosta platformy Azure i korzyści z używania hybrydowych danych SQL nie są uprawnione do Korzyść użycia hybrydowego platformy Azure, jeśli już używasz korzyści z maszynami wirtualnymi z systemem Linux.

## <a name="get-started"></a>Rozpoczęcie pracy

### <a name="red-hat-customers"></a>Klienci firmy Red Hat

Korzyść użycia hybrydowego platformy Azure dla RHEL jest dostępny dla klientów Red Hat, którzy spełniają oba te kryteria:

- Masz aktywne lub nieużywane subskrypcje RHEL, które kwalifikują się do użycia na platformie Azure
- Włączono co najmniej jedną z tych subskrypcji do użycia na platformie Azure przy użyciu programu [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

> [!IMPORTANT]
> Upewnij się, że w programie [dostępu do chmury](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) włączono poprawną subskrypcję.

Aby zacząć korzystać z zalet firmy Red Hat:

1. Włącz co najmniej jedną kwalifikującą się subskrypcje RHEL do użycia na platformie Azure przy użyciu [interfejsu klienta usługi Red Hat Cloud Access](https://access.redhat.com/management/cloud).

   W przypadku subskrypcji platformy Azure, które podano podczas procesu włączania dostępu w chmurze Red Hat, będzie można używać funkcji Korzyść użycia hybrydowego platformy Azure.
1. Zastosuj Korzyść użycia hybrydowego platformy Azure do dowolnych istniejących maszyn wirtualnych RHEL PAYG i nowych maszyn wirtualnych RHEL, które są wdrażane z obrazów PAYG usługi Azure Marketplace. Możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby umożliwić korzystanie z tej korzyści.
1. Postępuj zgodnie z zalecanymi [następnymi krokami](https://access.redhat.com/articles/5419341) dotyczącymi konfigurowania źródeł aktualizacji dla maszyn wirtualnych RHEL oraz wytycznych dotyczących zgodności subskrypcji RHEL.


### <a name="suse-customers"></a>Klienci SUSE

Aby zacząć korzystać z korzyści dla SUSE:

1. Zarejestruj się w programie firmy SUSE w chmurze publicznej.
1. Zastosuj korzyść do nowo utworzonych lub istniejących maszyn wirtualnych za pośrednictwem Azure Portal lub interfejsu wiersza polecenia platformy Azure.
1. Zarejestruj swoje maszyny wirtualne otrzymujące korzyść przy użyciu oddzielnego źródła aktualizacji.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Włącz i Wyłącz korzyść w Azure Portal

Możesz włączyć korzyść dla istniejących maszyn wirtualnych, odwiedzając opcję **konfiguracji** po lewej stronie i wykonując czynności opisane w tym miejscu. Możesz umożliwić korzystanie z nowych maszyn wirtualnych podczas tworzenia maszyny wirtualnej.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>Azure Portal przykład, aby włączyć korzyść dla istniejącej maszyny wirtualnej:
1. Odwiedź witrynę [Microsoft Azure Portal](https://portal.azure.com/)
1. Przejdź do strony "Tworzenie maszyny wirtualnej" w portalu.
 ![AHB podczas tworzenia maszyny wirtualnej](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. Kliknij pole wyboru, aby włączyć konwersję AHB i korzystać z licencji usługi Cloud Access.
 ![AHB podczas tworzenia maszyny wirtualnej](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. Tworzenie maszyny wirtualnej po kolejnym zestawie instrukcji
1. Sprawdź blok **Konfiguracja** i zobaczysz opcję włączona. 
![Blok konfiguracji AHB po utworzeniu](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>Azure Portal przykład, aby włączyć korzyść podczas tworzenia maszyny wirtualnej:
1. Odwiedź witrynę [Microsoft Azure Portal](https://portal.azure.com/)
1. Otwórz stronę maszyny wirtualnej, na której chcesz zastosować konwersję.
1. Przejdź do opcji **Konfiguracja** po lewej stronie. Zostanie wyświetlona sekcja Licencjonowanie. Aby włączyć konwersję AHB, zaznacz przycisk radiowy "tak" i zaznacz pole wyboru potwierdzenia.
![Blok konfiguracji AHB po utworzeniu](./media/azure-hybrid-benefit/create-configuration-blade.png)

>[!NOTE]
> Jeśli utworzono **migawkę niestandardową** lub **udostępniony obraz (SIG)** obrazu z witryny Marketplace RHEL lub SLES PAYG, możesz użyć interfejsu wiersza polecenia platformy Azure, aby włączyć korzyść użycia hybrydowego platformy Azure. Jest to znane ograniczenie i obecnie nie ma żadnych osi czasu, aby zapewnić tę możliwość w witrynie Azure Portal.

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Włączanie i wyłączanie korzyści w interfejsie wiersza polecenia platformy Azure

Możesz użyć polecenia, `az vm update` Aby zaktualizować istniejące maszyny wirtualne. W przypadku maszyn wirtualnych RHEL Uruchom polecenie z `--license-type` parametrem `RHEL_BYOS` . W przypadku maszyn wirtualnych SLES Uruchom polecenie z `--license-type` parametrem `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Przykład interfejsu wiersza polecenia, aby włączyć korzyść
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Przykład interfejsu wiersza polecenia do wyłączania korzyści
Aby wyłączyć korzyść, użyj `--license-type` wartości `None` :

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Przykład interfejsu wiersza polecenia, aby włączyć korzyść na dużej liczbie maszyn wirtualnych
Aby zapewnić korzyść na wielu maszynach wirtualnych, można użyć `--ids` parametru w interfejsie wiersza polecenia platformy Azure:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

W poniższych przykładach przedstawiono dwie metody uzyskiwania listy identyfikatorów zasobów: jeden na poziomie grupy zasobów i jeden na poziomie subskrypcji.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>Zastosuj Korzyść użycia hybrydowego platformy Azure podczas tworzenia maszyny wirtualnej
Oprócz zastosowania Korzyść użycia hybrydowego platformy Azure do istniejących maszyn wirtualnych z opcją płatność zgodnie z rzeczywistym użyciem można wywołać ją podczas tworzenia maszyny wirtualnej. Korzyści wynikające z tego są Threefold:
- Można obsługiwać zarówno maszyny wirtualne PAYG, jak i BYOS, korzystając z tego samego obrazu i procesu.
- Umożliwia ona zmiany trybu licencjonowania w przyszłości, coś nie jest dostępne z BYOS obrazem lub w przypadku przenoszenia własnej maszyny wirtualnej.
- Maszyna wirtualna zostanie domyślnie połączona z usługą Red Hat Update Infrastructure (RHUI), aby zapewnić jej aktualność i bezpieczeństwo. Zaktualizowany mechanizm można zmienić po wdrożeniu w dowolnym momencie.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Sprawdź stan Korzyść użycia hybrydowego platformy Azure maszyny wirtualnej
Korzyść użycia hybrydowego platformy Azure stan maszyny wirtualnej można wyświetlić za pomocą interfejsu wiersza polecenia platformy Azure lub usługi Azure Instance Metadata Service.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

`az vm get-instance-view`W tym celu można użyć polecenia. Poszukaj `licenseType` pola w odpowiedzi. Jeśli `licenseType` pole istnieje, a wartość to `RHEL_BYOS` lub, na `SLES_BYOS` maszynie wirtualnej jest włączona korzyść.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Z poziomu maszyny wirtualnej możesz wysyłać zapytania dotyczące zaświadczania metadanych na platformie Azure Instance Metadata Service, aby określić wartość maszyny wirtualnej `licenseType` . `licenseType`Wartość `RHEL_BYOS` lub `SLES_BYOS` będzie wskazywać, że na maszynie wirtualnej jest włączona korzyść. [Dowiedz się więcej o zaświadczeniu metadanych](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>Zgodność

### <a name="red-hat"></a>Red Hat

Klienci korzystający z usługi Korzyść użycia hybrydowego platformy Azure for RHEL akceptują standardowe [postanowienia prawne](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) i [zasady zachowania poufności informacji](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) powiązane z ofertami RHEL w portalu Azure Marketplace.

Klienci używający Korzyść użycia hybrydowego platformy Azure dla RHEL mają trzy opcje zapewniania aktualizacji oprogramowania i poprawek do tych maszyn wirtualnych:

- [Infrastruktura aktualizacji Red Hat](../workloads/redhat/redhat-rhui.md) (opcja domyślna)
- Serwer satelitarny Red Hat
- Menedżer subskrypcji Red Hat

Klienci, którzy wybierają opcję RHUI, mogą w dalszym ciągu używać RHUI jako głównego źródła aktualizacji dla Korzyść użycia hybrydowego platformy Azure maszyn wirtualnych RHEL bez dołączania subskrypcji RHEL do tych maszyn wirtualnych. Klienci, którzy wybierają opcję RHUI, są odpowiedzialni za zapewnienie zgodności z subskrypcją RHEL.

Klienci, którzy wybierają serwer satelitarny Red Hat lub Red Hat Subscription Manager, powinni usunąć konfigurację RHUI, a następnie dołączyć subskrypcję RHEL z włączoną obsługą chmury do swoich Korzyść użycia hybrydowego platformy Azure maszyn wirtualnych RHEL.  

Aby uzyskać więcej informacji na temat zgodności subskrypcji Red Hat, aktualizacji oprogramowania i źródeł dla Korzyść użycia hybrydowego platformy Azure maszyn wirtualnych RHEL, zobacz [artykuł Red Hat dotyczący używania subskrypcji RHEL z korzyść użycia hybrydowego platformy Azure](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Aby użyć Korzyść użycia hybrydowego platformy Azure dla maszyn wirtualnych SLES i uzyskać informacje dotyczące przechodzenia z SLES PAYG do BYOS lub przechodzenia z SLES BYOS do PAYG, zobacz [SUSE Linux Enterprise i korzyść użycia hybrydowego platformy Azure](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/). 

## <a name="azure-hybrid-benefit-on-reserved-instances-is-in-preview"></a>Korzyść użycia hybrydowego platformy Azure na wystąpieniach zarezerwowanych jest w wersji zapoznawczej

Azure Reservations (Azure Reserved Virtual Machine Instances) pomaga zaoszczędzić pieniądze przez zatwierdzenie planów jednego roku lub trzech lat dla wielu produktów. Więcej informacji o [wystąpieniach zarezerwowanych](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)można znaleźć tutaj. Korzyść użycia hybrydowego platformy Azure jest dostępny w wersji zapoznawczej dla [zarezerwowanych wystąpień maszyn wirtualnych (RIs)](https://review.docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations#charges-covered-by-reservation). Oznacza to, że w przypadku zakupu kosztów obliczeniowych z obniżoną stawką przy użyciu systemu RI można zastosować korzyść AHB na kosztach licencjonowania dla RHEL i SUSE. Kroki związane z zastosowaniem korzyści AHB dla wystąpienia RI pozostają dokładnie takie same, jak w przypadku zwykłej maszyny wirtualnej.
![AHB dla usług RIs](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Jeśli zakupione zostały już rezerwacje oprogramowania RHEL lub SUSE PAYG w portalu Azure Marketplace, poczekaj na ukończenie zastrzeżeń przed użyciem Korzyść użycia hybrydowego platformy Azure.


## <a name="frequently-asked-questions"></a>Często zadawane pytania
*P: Czy można użyć typu licencji `RHEL_BYOS` z SLES obrazem lub odwrotnie?*

Odp.: nie. Próba wprowadzenia typu licencji, która nieprawidłowo pasuje do dystrybucji działającej na maszynie wirtualnej, nie spowoduje aktualizacji żadnych metadanych rozliczeń. Jeśli jednak przypadkowo wprowadzisz niewłaściwy typ licencji, zaktualizowanie maszyny wirtualnej do poprawnego typu licencji nadal umożliwi korzystanie z tej korzyści.

*Pytanie: zarejestrowano w systemie Red Hat Cloud Access, ale nadal nie można włączyć korzyści na maszynach wirtualnych RHEL. Co mam zrobić?*

Odp.: może upłynąć trochę czasu na rejestrację subskrypcji usługi Red Hat Cloud Access w firmie Red Hat na platformie Azure. Jeśli po jednym dniu roboczym nadal widzisz błąd, skontaktuj się z pomocą techniczną firmy Microsoft.

*P: wdrożono maszynę wirtualną za pomocą RHEL BYOS ". Czy mogę przekonwertować rozliczenia na te obrazy z BYOS na PAYG?*

Odp.: nie. Korzyść użycia hybrydowego platformy Azure obsługuje konwersję tylko w przypadku obrazów z opcją płatność zgodnie z rzeczywistym użyciem.

*P: przekazano mój własny obraz RHEL z lokalnego (za pośrednictwem Azure Migrate, Azure Site Recovery lub w inny sposób) do platformy Azure. Czy mogę przekonwertować rozliczenia na te obrazy z BYOS na PAYG?*

Odp.: nie. Funkcja Korzyść użycia hybrydowego platformy Azure jest obecnie dostępna tylko dla obrazów RHEL i SLES w portalu Azure Marketplace. 

*P: przekazano mój własny obraz RHEL z lokalnego (za pośrednictwem Azure Migrate, Azure Site Recovery lub w inny sposób) do platformy Azure. Czy muszę korzystać z Korzyść użycia hybrydowego platformy Azure?*

Odp.: nie. RHEL obrazy są już uznawane za BYOS i opłaty są naliczone wyłącznie za koszty infrastruktury platformy Azure. Użytkownik jest odpowiedzialny za koszty subskrypcji RHEL, tak samo jak w przypadku środowisk lokalnych. 

*P: Czy można używać Korzyść użycia hybrydowego platformy Azure na maszynach wirtualnych wdrożonych z obrazów platformy Azure Marketplace RHEL i SLES SAP?*

Odp.: tak, możesz. Można użyć typu licencji `RHEL_BYOS` dla maszyn wirtualnych RHEL i `SLES_BYOS` dla konwersji maszyn wirtualnych wdrożonych z obrazów platformy Azure Marketplace RHEL i SLES SAP.

*P: Czy można używać Korzyść użycia hybrydowego platformy Azure w zestawach skalowania maszyn wirtualnych dla RHEL i SLES?*

Odp.: tak, Korzyść użycia hybrydowego platformy Azure w zestawach skalowania maszyn wirtualnych dla RHEL i SLES jest w wersji zapoznawczej. Możesz [dowiedzieć się więcej na temat tej korzyści i sposobu używania jej w tym miejscu](https://docs.microsoft.com/azure/virtual-machine-scale-sets/azure-hybrid-benefit-linux-vmss). 

*P: Czy można używać Korzyść użycia hybrydowego platformy Azure na wystąpieniach zarezerwowanych dla RHEL i SLES?*

Odp.: tak, Korzyść użycia hybrydowego platformy Azure w wystąpieniu zarezerwowanym dla RHEL i SLES jest w wersji zapoznawczej. Możesz [dowiedzieć się więcej na temat tej korzyści i sposobu używania jej w tym miejscu](#azure-hybrid-benefit-on-reserved-instances-is-in-preview).

*P: Czy można używać Korzyść użycia hybrydowego platformy Azure na maszynie wirtualnej wdrożonej dla SQL Server na obrazach RHEL?*

Odp.: nie. Nie ma planu obsługi tych maszyn wirtualnych.

*P: Czy można użyć Korzyść użycia hybrydowego platformy Azure w mojej subskrypcji RHEL Virtual Data Center?*

Odp.: nie. VDC nie jest obsługiwana na platformie Azure, łącznie z AHB.  
 

## <a name="common-problems"></a>Typowe problemy
W tej sekcji wymieniono typowe problemy, które można napotkać, oraz kroki umożliwiające rozwiązanie tego problemu.

| Błąd | Ograniczanie ryzyka |
| ----- | ---------- |
| "Akcja nie może zostać ukończona, ponieważ nasze rekordy pokazują, że nie udało Ci się pomyślnie włączyć usługi Red Hat Cloud Access w ramach subskrypcji platformy Azure..." | Aby korzystać z zalet z maszynami wirtualnymi RHEL, musisz najpierw [zarejestrować subskrypcje platformy Azure przy użyciu usługi Red Hat Cloud Access](https://access.redhat.com/management/cloud).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak tworzyć i aktualizować maszyny wirtualne oraz dodawać typy licencji (RHEL_BYOS, SLES_BYOS) dla Korzyść użycia hybrydowego platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/vm)
