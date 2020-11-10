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
ms.author: alsin
ms.openlocfilehash: c1200121d1c768a3fdddd7749184d7f8b5c98a96
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413109"
---
# <a name="preview-azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Wersja zapoznawcza: Korzyść użycia hybrydowego platformy Azure — jak ma to zastosowanie do Linux Virtual Machines

## <a name="overview"></a>Omówienie

Korzyść użycia hybrydowego platformy Azure umożliwia łatwiejsze Migrowanie lokalnych maszyn wirtualnych Red Hat Enterprise Linux (RHEL) i SUSE Linux Enterprise Server (SLES) na platformę Azure przy użyciu własnej istniejącej subskrypcji Red Hat lub SUSE. Korzystając z tej korzyści, płacisz tylko za koszty infrastruktury maszyny wirtualnej, ponieważ opłata za oprogramowanie jest objęta subskrypcją usługi RHEL lub SLES. Korzyść ma zastosowanie do wszystkich obrazów RHEL i SLES Marketplace z opcją płatność zgodnie z rzeczywistym użyciem.

> [!IMPORTANT]
> Korzyść użycia hybrydowego platformy Azure dla maszyn wirtualnych z systemem Linux jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="benefit-description"></a>Opis korzyści

Za pośrednictwem Korzyść użycia hybrydowego platformy Azure można łatwiej migrować lokalne serwery RHEL i SLES na platformę Azure, konwertując istniejące maszyny wirtualne RHEL i SLES PAYG na platformę Azure, aby przenieść rozliczenia własnej subskrypcji (BYOS). Zazwyczaj maszyny wirtualne wdrożone z obrazów PAYG na platformie Azure będą naliczane opłaty za infrastrukturę oraz opłaty za oprogramowanie. Za pomocą Korzyść użycia hybrydowego platformy Azure maszyny wirtualne PAYG można przekonwertować na model rozliczeń BYOS bez konieczności ponownego wdrażania, unikając ryzyka przestoju.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Korzyść użycia hybrydowego platformy Azure wizualizacji kosztów na maszynach wirtualnych z systemem Linux.":::

Po umożliwieniu skorzystania z usługi RHEL lub SLES maszyny wirtualnej nie będą już naliczane opłaty za dodatkowy koszt oprogramowania zwykle ponoszony na maszynę wirtualną PAYG. Zamiast tego maszyna wirtualna rozpocznie emitowanie opłaty za BYOS, która obejmuje tylko opłaty za sprzęt i opłaty za oprogramowanie.

Jeśli wolisz, możesz również skonwertować maszynę wirtualną, na której włączono korzyść z powrotem do modelu rozliczania PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Zakres Korzyść użycia hybrydowego platformy Azure uprawnień dla maszyn wirtualnych z systemem Linux

Korzyść użycia hybrydowego platformy Azure jest dostępny dla wszystkich obrazów programu RHEL i SLES w portalu Marketplace. Korzyść nie jest jeszcze dostępna dla obrazów RHEL lub SLES Marketplace.

Wystąpienia zarezerwowane, dedykowane hosty i korzyści z używania hybrydowej bazy danych SQL nie kwalifikują się do Korzyść użycia hybrydowego platformy Azure, jeśli jest już używana korzyść z maszynami wirtualnymi z systemem Linux.

## <a name="how-to-get-started"></a>Jak zacząć

Korzyść użycia hybrydowego platformy Azure jest obecnie w fazie wersji zapoznawczej dla maszyn wirtualnych z systemem Linux. Po uzyskaniu dostępu do wersji zapoznawczej można włączyć korzystanie z Azure Portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="preview"></a>Wersja zapoznawcza

W tej fazie można uzyskać dostęp do korzyści, wypełniając formularz w [tym miejscu](https://aka.ms/ahb-linux-form). Po wypełnieniu formularza subskrypcja platformy Azure zostanie włączona z korzyścią, a użytkownik otrzyma potwierdzenie od firmy Microsoft w ciągu trzech dni roboczych.

### <a name="red-hat-customers"></a>Klienci firmy Red Hat

1.    Wypełnij formularz żądania wersji zapoznawczej powyżej
1.    Zarejestruj się w [programie Red Hat Cloud Access](https://aka.ms/rhel-cloud-access)
1.    Włącz subskrypcję platformy Azure na potrzeby dostępu do chmury i Włącz subskrypcje zawierające maszyny wirtualne, dla których chcesz korzystać z usługi
1.    Zastosuj korzyść dla istniejących maszyn wirtualnych za pośrednictwem Azure Portal lub interfejsu wiersza polecenia platformy Azure
1.    Opcjonalnie Zarejestruj maszyny wirtualne otrzymujące korzyść przy użyciu oddzielnego źródła aktualizacji (przełączane maszyny wirtualne mogą pozostać dołączone do [RHUI](../workloads/redhat/redhat-rhui.md) lub zarejestrowane za pośrednictwem RHSM)

### <a name="suse-customers"></a>Klienci SUSE

1.    Wypełnij formularz żądania wersji zapoznawczej powyżej
1.    Rejestrowanie w programie SUSE Public Cloud
1.    Zastosuj korzyść dla istniejących maszyn wirtualnych za pośrednictwem Azure Portal lub interfejsu wiersza polecenia platformy Azure
1.    Zarejestruj swoje maszyny wirtualne otrzymujące korzyść przy użyciu oddzielnego źródła aktualizacji

### <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Włącz i Wyłącz korzyść w Azure Portal

Możesz włączyć korzyść dla istniejących maszyn wirtualnych, odwiedzając blok **Konfiguracja** i wykonując czynności opisane w tym miejscu. Możesz umożliwić korzystanie z nowych maszyn wirtualnych podczas tworzenia maszyny wirtualnej.

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
Stan AHB maszyny wirtualnej może być wyświetlany na trzy sposoby: sprawdzanie w portalu, korzystanie z interfejsu wiersza polecenia platformy Azure lub korzystanie z usługi Azure Instance Metadata Service (Azure IMDS).


### <a name="portal"></a>Portal

Wyświetl blok konfiguracja i sprawdź stan licencji, aby sprawdzić, czy AHB jest włączony dla maszyny wirtualnej.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W `az vm get-instance-view` tym celu można użyć polecenia. Wyszukaj w odpowiedzi pole LicenseType. Jeśli pole LicenseType istnieje, a wartość to "RHEL_BYOS" lub "SLES_BYOS", na maszynie wirtualnej jest włączona korzyść.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Z poziomu samej maszyny wirtualnej możesz wysyłać zapytania do IMDS zaświadczania metadanych, aby określić wartość LicenseType maszyny wirtualnej. Wartość LicenseType elementu "RHEL_BYOS" lub "SLES_BYOS" wskazuje, że na maszynie wirtualnej jest włączona korzyść. Dowiedz się więcej o zaświadczeniu metadanych [tutaj](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Zgodność

### <a name="red-hat"></a>Red Hat

Aby można było używać Korzyść użycia hybrydowego platformy Azure dla maszyn wirtualnych RHEL, należy najpierw zarejestrować program w systemie Red Hat Cloud Access. Można to zrobić za pośrednictwem witryny Red Hat Cloud Access tutaj. Po włączeniu korzyści na maszynie wirtualnej należy zarejestrować maszynę wirtualną przy użyciu własnego źródła aktualizacji przy użyciu programu Red Hat Subscription Manager lub usługi Red Hat satelitarnej. Rejestracja w poszukiwaniu aktualizacji zapewni, że pozostanie w stanie obsługiwanym.

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
| "Subskrypcja nie jest zarejestrowana w wersji zapoznawczej systemu Linux dla Korzyść użycia hybrydowego platformy Azure. Aby uzyskać instrukcje krok po kroku, zobacz https://aka.ms/ahb-linux " | Wypełnij formularz, https://aka.ms/ahb-linux-form Aby zarejestrować się w wersji zapoznawczej systemu Linux dla korzyść użycia hybrydowego platformy Azure.
| "Akcja nie może zostać ukończona, ponieważ nasze rekordy pokazują, że nie udało Ci się pomyślnie włączyć usługi Red Hat Cloud Access w ramach subskrypcji platformy Azure..." | Aby można było korzystać z zalet z maszynami wirtualnymi RHEL, należy najpierw zarejestrować subskrypcje platformy Azure przy użyciu usługi Red Hat Cloud Access. Odwiedź ten link, aby dowiedzieć się więcej o rejestrowaniu subskrypcji platformy Azure na potrzeby usługi Red Hat Cloud Access
|"Opcja dla Korzyść użycia hybrydowego platformy Azure nie jest wyświetlana w portalu" | Jest to znany problem dotyczący maszyn wirtualnych RHEL i SLES utworzonych z galerii obrazów udostępnionych, migawek lub przechwyconych obrazów PAYG. W takim przypadku należy użyć kroków interfejsu wiersza polecenia opisanych w sekcji "[Włączanie i wyłączanie korzyści w interfejsie wiersza polecenia platformy Azure](#enable-and-disable-the-benefit-in-the-azure-cli)". Aby wyświetlić stan AHB, użyj polecenia ` az vm get-instance-view -g MyResourceGroup -n MyVm` .|

## <a name="next-steps"></a>Następne kroki
* Zacznij korzystać z wersji zapoznawczej, wypełniając formularz w [tym miejscu](https://aka.ms/ahb-linux-form).