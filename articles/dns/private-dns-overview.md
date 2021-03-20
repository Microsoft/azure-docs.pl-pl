---
title: Co to jest prywatna strefa DNS platformy Azure?
description: W tym artykule Rozpocznij pracę z omówieniem prywatnej usługi hostingu DNS na Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 540c4d6f2dde64cef9b5795d635ac6259bfc69c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87985635"
---
# <a name="what-is-azure-private-dns"></a>Co to jest prywatna strefa DNS platformy Azure?

System nazw domen (DNS) jest odpowiedzialny za tłumaczenie (lub rozpoznanie) nazwy usługi na adres IP.  Azure DNS to usługa hostingu dla domen DNS, która zapewnia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Oprócz obsługi domen DNS dostępnych w Internecie Azure DNS również obsługuje prywatne strefy DNS.

Usługa Azure Prywatna strefa DNS zapewnia niezawodną, bezpieczną usługę DNS do zarządzania i rozpoznawania nazw domen w sieci wirtualnej bez konieczności dodawania niestandardowego rozwiązania DNS. Używając prywatnych stref DNS, możesz użyć własnych niestandardowych nazw domen, a nie dostępnych już dzisiaj nazw udostępnianych przez platformę Azure. Użycie niestandardowych nazw domen ułatwia dostosowanie architektury sieci wirtualnej do potrzeb organizacji. Zapewnia rozpoznawanie nazw dla maszyn wirtualnych w sieci wirtualnej i między sieciami wirtualnymi. Ponadto można skonfigurować nazwy stref z widokiem Split-Horizon, który umożliwia prywatnym i publicznym strefom DNS Udostępnianie nazwy.

Aby rozwiązać rekordy prywatnej strefy DNS z sieci wirtualnej, należy połączyć sieć wirtualną ze strefą. Połączone sieci wirtualne mają pełny dostęp i mogą rozpoznać wszystkie rekordy DNS opublikowane w strefie prywatnej. Dodatkowo można również włączyć autorejestrację w łączu sieci wirtualnej. Jeśli włączysz autorejestrację w łączu sieci wirtualnej, rekordy DNS dla maszyn wirtualnych w tej sieci wirtualnej są zarejestrowane w strefie prywatnej. Gdy Autorejestracja jest włączona, Azure DNS aktualizuje także rekordy strefy za każdym razem, gdy maszyna wirtualna jest tworzona, zmienia jej adres IP lub został usunięty.

![Omówienie systemu DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Najlepszym rozwiązaniem jest korzystanie z domeny *lokalnej* dla prywatnej strefy DNS. Nie wszystkie systemy operacyjne obsługują ten system.

## <a name="benefits"></a>Korzyści

Usługa Azure Prywatna strefa DNS zapewnia następujące korzyści:

* **Eliminuje konieczność stosowania niestandardowych rozwiązań DNS**. Wcześniej wielu klientów utworzyła niestandardowe rozwiązania DNS w celu zarządzania strefami DNS w ich sieci wirtualnej. Można teraz zarządzać strefami DNS przy użyciu natywnej infrastruktury platformy Azure, która eliminuje obciążenie związane z tworzeniem niestandardowych rozwiązań DNS i zarządzaniem nimi.

* **Używaj wszystkich typów wspólnych rekordów DNS**. Azure DNS obsługuje rekordy, AAAA, CNAME, MX, PTR, SOA, SRV i TXT.

* **Automatyczne Zarządzanie rekordami nazw hostów**. Wraz z hostem niestandardowych rekordów DNS platforma Azure automatycznie obsługuje rekordy nazw hostów dla maszyn wirtualnych w określonych sieciach wirtualnych. W tym scenariuszu można zoptymalizować używane nazwy domen bez konieczności tworzenia niestandardowych rozwiązań DNS ani modyfikowania aplikacji.

* **Rozpoznawanie nazwy hosta między sieciami wirtualnymi**. W przeciwieństwie do nazw hostów udostępnianych przez platformę Azure, prywatne strefy DNS mogą być współużytkowane przez sieci wirtualne. Ta funkcja upraszcza scenariusze między sieciami i odnajdywaniem usług, takie jak Komunikacja równorzędna sieci wirtualnych.

* **Znane narzędzia i środowisko użytkownika**. Aby zmniejszyć krzywą uczenia, ta usługa używa dobrze ustanowionych narzędzi Azure DNS (Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure, szablonów Azure Resource Manager i interfejsu API REST).

* **Obsługa systemu DNS Split-Horizon**. Za pomocą Azure DNS można utworzyć strefy o tej samej nazwie, która rozwiązuje różne odpowiedzi z sieci wirtualnej i publicznego Internetu. Typowym scenariuszem dla usługi DNS Split-Horizon jest zapewnienie dedykowanej wersji usługi do użycia w sieci wirtualnej.

* **Dostępne we wszystkich regionach świadczenia usługi Azure**. Funkcja stref prywatnych Azure DNS jest dostępna we wszystkich regionach świadczenia usługi Azure w chmurze publicznej platformy Azure.

## <a name="capabilities"></a>Możliwości

Azure DNS zapewnia następujące możliwości:

* **Automatyczna rejestracja maszyn wirtualnych z sieci wirtualnej, która jest połączona ze strefą prywatną z włączoną funkcją autorejestrowania**. Maszyny wirtualne są rejestrowane (dodawane) do strefy prywatnej jako rekordy wskazujące ich prywatne adresy IP. Po usunięciu maszyny wirtualnej w łączu sieci wirtualnej z włączoną funkcją automatycznego rejestrowania Azure DNS również automatycznie usuwa odpowiedni rekord DNS ze połączonej strefy prywatnej.

* **Rozwiązanie DNS do przodu jest obsługiwane w sieciach wirtualnych, które są połączone ze strefą prywatną**. W przypadku rozpoznawania nazw DNS między sieciami wirtualnymi nie ma żadnej bezpośredniej zależności, która umożliwia komunikację między sieciami wirtualnymi. Można jednak użyć równorzędnych sieci wirtualnych dla innych scenariuszy (na przykład ruchu HTTP).

* **Odwrotne wyszukiwanie DNS jest obsługiwane w zakresie sieci wirtualnej**. Odwrotne wyszukiwanie DNS dla prywatnego adresu IP w ramach sieci wirtualnej przypisanej do strefy prywatnej zwraca nazwę FQDN, która zawiera nazwy hosta/rekordu i nazwa strefy jako sufiks.

## <a name="other-considerations"></a>Inne zagadnienia

Azure DNS ma następujące ograniczenia:

* Określona Sieć wirtualna może być połączona tylko z jedną strefą prywatną, jeśli jest włączona automatyczna rejestracja rekordów DNS maszyn wirtualnych. Można jednak połączyć wiele sieci wirtualnych z pojedynczą strefą DNS.
* Odwrotny serwer DNS działa tylko w przypadku prywatnych przestrzeni adresów IP w połączonej sieci wirtualnej
* Zwrotny serwer DNS dla prywatnego adresu IP dla połączonej sieci wirtualnej zwraca *Internal.cloudapp.NET* jako domyślny sufiks dla maszyny wirtualnej. W przypadku sieci wirtualnych, które są połączone ze strefą prywatną z włączoną funkcją autorejestracji, odwrotny serwer DNS dla prywatnego adresu IP zwraca dwie nazwy FQDN: jeden z domyślną sufiks *Internal.cloudapp.NET* i drugi z sufiksem strefy prywatnej.
* Warunkowe przekazywanie nie jest obecnie obsługiwane natywnie. Aby włączyć rozwiązanie między platformą Azure i sieciami lokalnymi, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 
## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach, zobacz [Cennik usługi Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć strefę prywatną w Azure DNS przy użyciu [Azure PowerShell](./private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](./private-dns-getstarted-cli.md).

* Zapoznaj się z typowymi [scenariuszami stref prywatnych](./private-dns-scenarios.md) , które mogą być realizowane przy użyciu stref prywatnych w Azure DNS.

* Często zadawane pytania i odpowiedzi dotyczące stref prywatnych w Azure DNS, w tym określonych zachowań, których można oczekiwać w przypadku niektórych rodzajów operacji, zapoznaj się z tematem [prywatna strefa DNS często zadawane pytania](./dns-faq-private.md).

* Informacje o strefach i rekordach DNS zawiera temat [Omówienie stref i rekordów DNS](dns-zones-records.md).

* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
