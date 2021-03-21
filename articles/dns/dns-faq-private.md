---
title: Prywatna strefa DNS — Często zadawane pytania
description: W tym artykule zapoznaj się z często zadawanymi pytaniami dotyczącymi usługi Azure Prywatna strefa DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/15/2021
ms.author: rohink
ms.openlocfilehash: 3272f4fea4169fd5e91b1ba2b97ad72dfbb49080
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247390"
---
# <a name="azure-private-dns-faq"></a>Prywatna strefa DNS — Często zadawane pytania

Poniżej znajdują się często zadawane pytania dotyczące prywatnego systemu DNS platformy Azure.

## <a name="does-azure-dns-support-private-domains"></a>Czy Azure DNS obsługuje domeny prywatne?

Domeny prywatne są obsługiwane za pomocą funkcji strefy Prywatna strefa DNS platformy Azure. Strefy Prywatna strefa DNS są rozpoznawalne tylko w określonych sieciach wirtualnych. Aby uzyskać więcej informacji, zobacz [Omówienie](private-dns-overview.md).

Aby uzyskać informacje na temat innych wewnętrznych opcji DNS na platformie Azure, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Czy strefy Prywatna strefa DNS platformy Azure działają w regionach platformy Azure?

Tak. Strefy prywatne są obsługiwane w przypadku rozpoznawania nazw DNS między sieciami wirtualnymi w różnych regionach platformy Azure. Strefy prywatne działają nawet bez jawnego komunikacji równorzędnej z sieciami wirtualnymi. Wszystkie sieci wirtualne muszą być połączone z prywatną strefą DNS.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Czy połączenie z Internetem jest nawiązywane przez sieci wirtualne wymagane dla stref prywatnych?

Nie. Strefy prywatne działają razem z sieciami wirtualnymi. Są one używane do zarządzania domenami maszyn wirtualnych lub innych zasobów w ramach i między sieciami wirtualnymi. Łączność z Internetem nie jest wymagana do rozpoznawania nazw.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Czy można użyć tej samej strefy prywatnej dla kilku sieci wirtualnych do rozwiązania?

Tak. Można połączyć prywatną strefę DNS z tysiącami sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [limity Azure DNS](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Czy sieć wirtualna, która należy do innej subskrypcji, jest połączona ze strefą prywatną?

Tak. Musisz mieć uprawnienie do zapisu w sieciach wirtualnych i prywatnej strefie DNS. Uprawnienie do zapisu można udzielić kilku rolom platformy Azure. Na przykład rola współautor współautora sieci klasycznej ma uprawnienia do zapisu sieci wirtualnych i rola współautor Prywatna strefa DNS strefy ma uprawnienia do zapisu w prywatnych strefach DNS. Aby uzyskać więcej informacji na temat ról platformy Azure, zobacz [Kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Czy rekordy DNS maszyn wirtualnych automatycznie zarejestrowane w strefie prywatnej będą automatycznie usuwane po usunięciu maszyny wirtualnej?

Tak. Jeśli usuniesz maszynę wirtualną w połączonej sieci wirtualnej z włączoną funkcją automatycznego rejestrowania, zarejestrowane rekordy zostaną automatycznie usunięte.

## <a name="ive-reconfigured-the-os-in-my-virtual-machine-to-have-a-new-host-name-or-static-ip-address-why-dont-i-see-that-change-reflected-in-the-private-zone"></a>Ponownie skonfigurowano system operacyjny na mojej maszynie wirtualnej w taki sposób, aby miał nową nazwę hosta lub statyczny adres IP. Dlaczego nie widzę zmiany odzwierciedlonej w strefie prywatnej?

Rekordy strefy prywatnej są wypełniane przez usługę DHCP platformy Azure. komunikaty rejestracji klienta są ignorowane. Jeśli wyłączono obsługę klienta DHCP na maszynie wirtualnej przez skonfigurowanie statycznego adresu IP, zmiany nazwy hosta lub statycznego adresu IP na maszynie wirtualnej nie zostaną odzwierciedlone w strefie.

## <a name="i-have-configured-a-preferred-dns-suffix-in-my-windows-virtual-machine-why-are-my-records-still-registered-in-the-zone-linked-to-the-virtual-network"></a>Mam skonfigurowany preferowany sufiks DNS na maszynie wirtualnej z systemem Windows. Dlaczego moje rekordy są nadal rejestrowane w strefie połączonej z siecią wirtualną?

Usługa Azure DHCP ignoruje dowolny sufiks DNS, gdy rejestruje prywatną strefę DNS. Na przykład jeśli maszyna wirtualna jest skonfigurowana `contoso.com` jako sufiks podstawowej domeny DNS, ale sieć wirtualna jest połączona z `fabrikam.com` prywatną strefą DNS, rejestracja maszyny wirtualnej jest wyświetlana w `fabrikam.com` prywatnej strefie DNS.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Czy automatycznie zarejestrowano rekord maszyny wirtualnej w strefie prywatnej z połączonej sieci wirtualnej?

Tak. Automatycznie zarejestrowane rekordy DNS można zastąpić ręcznie utworzony rekord DNS w strefie. Poniższe pytania i odpowiedzi dotyczą tego tematu.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Co się stanie w przypadku próby ręcznego utworzenia nowego rekordu DNS w strefie prywatnej, która ma taką samą nazwę hosta, jak automatycznie zarejestrowana istniejąca maszyna wirtualna w połączonej sieci wirtualnej?

Spróbuj ręcznie utworzyć nowy rekord DNS w strefie prywatnej, która ma taką samą nazwę hosta jak istniejąca, automatycznie zarejestrowana maszyna wirtualna w połączonej sieci wirtualnej. Gdy to zrobisz, nowy rekord DNS zastępuje rekord automatycznie zarejestrowanej maszyny wirtualnej. Jeśli spróbujesz usunąć ręcznie utworzony rekord DNS z strefy, usuwanie zakończy się pomyślnie. Automatyczna rejestracja jest wykonywana ponownie, o ile maszyna wirtualna nadal istnieje i ma dołączony prywatny adres IP. Rekord DNS jest automatycznie tworzony w strefie.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Co się stanie po rozłączeniu połączonej sieci wirtualnej ze strefy prywatnej? Czy automatycznie zarejestrowane rekordy maszyn wirtualnych z sieci wirtualnej zostaną usunięte ze strefy?

Tak. Aby odłączyć połączoną sieć wirtualną ze strefy prywatnej, należy zaktualizować strefę DNS, aby usunąć skojarzone łącze sieci wirtualnej. W tym procesie rekordy maszyn wirtualnych, które zostały zarejestrowane automatycznie, są usuwane ze strefy.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Co się stanie po usunięciu połączonej sieci wirtualnej połączonej ze strefą prywatną? Czy trzeba ręcznie zaktualizować strefę prywatną w celu odłączenia sieci wirtualnej jako połączonej sieci wirtualnej ze strefy?

Nie. Po usunięciu połączonej sieci wirtualnej bez odłączenia jej od strefy prywatnej najpierw operacja usuwania zostanie zakończona, a linki do strefy DNS zostaną automatycznie wyczyszczone.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Czy rozpoznawanie nazw DNS przy użyciu domyślnej nazwy FQDN (internal.cloudapp.net) nadal działa nawet wtedy, gdy strefa prywatna (na przykład private.contoso.com) jest połączona z siecią wirtualną?

Tak. Strefy prywatne nie zastępują domyślnej strefy internal.cloudapp.net udostępnianej przez platformę Azure. Bez względu na to, czy korzystasz z internal.cloudapp.net opartego na platformie Azure, czy we własnej strefie prywatnej, użyj nazwy FQDN strefy, względem której chcesz rozwiązać ten problem.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Czy sufiks DNS maszyn wirtualnych w połączonej sieci wirtualnej zostanie zmieniony na tę strefę prywatną?

Nie. Sufiks DNS na maszynach wirtualnych w połączonej sieci wirtualnej pozostaje jako domyślny sufiks dostarczony przez platformę Azure ("*. internal.cloudapp.net"). Sufiks DNS można zmienić ręcznie na maszynach wirtualnych w ramach strefy prywatnej.
Aby uzyskać wskazówki dotyczące zmiany tego sufiksu, [Użyj dynamicznego systemu DNS do rejestrowania nazw hostów na własnym serwerze DNS](../virtual-network/virtual-networks-name-resolution-ddns.md#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Jakie są limity użycia dla Azure DNS stref prywatnych?

Szczegółowe informacje na temat limitów użycia dla Azure DNS stref prywatnych można znaleźć w [limitach Azure DNS](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits) .

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Dlaczego moje istniejące prywatne strefy DNS nie są wyświetlane w nowym środowisku portalu?

Jeśli istniejąca prywatna strefa DNS została utworzona przy użyciu interfejsu API w wersji zapoznawczej, należy przeprowadzić migrację tych stref do nowego modelu zasobów. Strefy Prywatna strefa DNS utworzone przy użyciu interfejsu API wersji zapoznawczej nie będą wyświetlane w nowym środowisku portalu. Aby uzyskać instrukcje dotyczące migracji do nowego modelu zasobów, zobacz poniżej.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Jak mogę zmigrować istniejące prywatne strefy DNS do nowego modelu?

Zdecydowanie zalecamy przeprowadzenie migracji do nowego modelu zasobów najszybciej, jak to możliwe. Starsza wersja modelu zasobów będzie obsługiwana, jednak dalsze funkcje nie będą opracowywane na tym modelu. W przyszłości zamierzamy zastąpić go nowym modelem zasobów. Aby uzyskać wskazówki dotyczące migrowania istniejących prywatnych stref DNS do nowego modelu zasobów, zobacz[Przewodnik migracji Azure DNS stref prywatnych](private-dns-migration-guide.md).

### <a name="does-azure-dns-private-zones-store-any-customer-content"></a>Czy Azure DNS strefy prywatne przechowują zawartość klienta?

Nie, Azure DNS strefy prywatne nie przechowują żadnej zawartości klienta.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o usłudze Azure Prywatna strefa DNS](private-dns-overview.md)
