---
title: Obciążenia Red Hat na platformie Azure — omówienie | Microsoft Docs
description: Dowiedz się więcej o ofercie produktów Red Hat dostępnych na platformie Azure.
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 7394cb50010bddddf8f8eff4b4f04eaf4d3231b6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87052111"
---
# <a name="red-hat-workloads-on-azure"></a>Obciążenia Red Hat na platformie Azure

Obciążenia Red Hat są obsługiwane przez różne oferty na platformie Azure. Obrazy Red Hat Enterprise Linux (RHEL) są podstawą obciążeń RHEL, podobnie jak infrastruktura aktualizacji Red Hat (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Obrazy Red Hat Enterprise Linux

Platforma Azure oferuje szeroką ofertę obrazów RHEL na platformie Azure. Te obrazy są udostępniane przez dwa różne modele licencjonowania: płatność zgodnie z rzeczywistym użyciem i przenoszenie własnych subskrypcji (BYOS). Nowe obrazy RHEL na platformie Azure są publikowane, gdy nowe wersje RHEL zostały wydane i zaktualizowane w całym cyklu życia, zgodnie z potrzebami.

### <a name="pay-as-you-go-images"></a>Obrazy z opcją płatność zgodnie z rzeczywistym użyciem

Platforma Azure oferuje różne obrazy z opcją płatność zgodnie z rzeczywistym użyciem. Te obrazy są prawidłowo uprawnione do RHEL i są dołączone do źródła aktualizacji (infrastruktura aktualizacji Red Hat). Te obrazy pobierają opłaty za usługę Premium za RHEL i aktualizacje. RHEL z wynagrodzeniem z płatność zgodnie z rzeczywistym użyciem obejmują:

* Standardowa RHEL.
* RHEL dla SAP.
* RHEL for SAP z usługami wysokiej dostępności i aktualizacji.

Możesz chcieć użyć obrazów z płatnością zgodnie z rzeczywistym użyciem, jeśli nie chcesz martwić się o płatność za właściwą liczbę subskrypcji.

### <a name="red-hat-gold-images"></a>Obrazy Red Hat Gold

Platforma Azure oferuje również obrazy Red Hat Gold ( `rhel-byos` ). Te obrazy mogą być przydatne dla klientów, którzy mają istniejące subskrypcje Red Hat i chcą korzystać z nich na platformie Azure. Musisz włączyć istniejące subskrypcje Red Hat dla usługi Red Hat Cloud Access, aby móc używać ich na platformie Azure. Dostęp do tych obrazów jest udzielany automatycznie po włączeniu subskrypcji Red Hat w celu uzyskania dostępu do chmury i spełnienia wymagań dotyczących uprawnień. Dzięki tym obrazom klient może uniknąć podwójnego rozliczania, które mogą być naliczane z używania obrazów z płatnością zgodnie z rzeczywistym użyciem.
* Dowiedz się, jak [włączyć subskrypcję Red Hat dla dostępu do chmury przy użyciu platformy Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs).
* Dowiedz się [, jak znaleźć obrazy Red Hat Gold w Azure Portal, interfejsie wiersza polecenia platformy Azure lub w poleceniu cmdlet programu PowerShell](./byos.md).

> [!NOTE]
> Podwójne rozliczenie jest naliczane, gdy użytkownik płaci dwa razy w przypadku subskrypcji RHEL. Ten scenariusz zwykle występuje, gdy klient korzysta z programu Red Hat Subscription-Manager w celu dołączenia uprawnień do maszyny wirtualnej z płatnością zgodnie z rzeczywistym użyciem. Na przykład klient używający usługi Subscription Manager do dołączania uprawnień do pakietów SAP na RHEL obraz z płatnością zgodnie z rzeczywistym użyciem jest pośrednio podwójnie rozliczany, ponieważ płacą dwa razy dla RHEL. Są one płatne jednokrotnie przy użyciu opłaty za usługę płatność zgodnie z rzeczywistym użyciem oraz za pomocą ich subskrypcji SAP. Ten scenariusz nie ma na celu BYOS użytkowników obrazu.

### <a name="generation-2-images"></a>Obrazy generacji 2

Maszyny wirtualne generacji 2 zapewniają nowe funkcje w porównaniu z maszynami wirtualnymi generacji 1. Aby uzyskać więcej informacji, zobacz [dokumentację generacji 2](../../linux/generation-2.md). Różnica klucza z perspektywy obrazu RHEL polega na tym, że maszyny wirtualne 2. generacji używają interfejsu UEFI zamiast systemu BIOS. Używają one również tabeli partycji GUID (GPT) zamiast głównego rekordu rozruchowego (MBR) w czasie rozruchu. Użycie GPT pozwala na, między innymi, rozmiary dysków systemu operacyjnego o rozmiarze większym niż 2 TB. Ponadto [maszyny wirtualne z serii Mv2](../../mv2-series.md) są uruchamiane tylko na obrazach generacji 2.

Obrazy generacji RHEL 2 są dostępne w portalu Azure Marketplace. Poszukaj "Gen2" w jednostce SKU obrazu na liście wszystkich obrazów, które są wyświetlane podczas korzystania z interfejsu wiersza polecenia platformy Azure. Przejdź do karty **Zaawansowane** w procesie wdrażania maszyny wirtualnej, aby wdrożyć maszynę wirtualną generacji 2.

## <a name="red-hat-update-infrastructure"></a>Infrastruktura aktualizacji systemu Red Hat

Platforma Azure udostępnia infrastrukturę aktualizacji Red Hat tylko dla maszyn wirtualnych z opcją płatność zgodnie z rzeczywistym użyciem. RHUI jest skutecznym dublowaniem Red Hat sieci CDN, ale jest dostępny tylko dla maszyn wirtualnych z systemem Azure z opcją płatność zgodnie z rzeczywistym użyciem. Masz dostęp do odpowiednich pakietów w zależności od tego, który obraz RHEL został wdrożony. Na przykład obraz RHEL dla oprogramowania SAP ma dostęp do pakietów SAP oprócz podstawowych pakietów RHEL.

### <a name="rhui-update-behavior"></a>Zachowanie aktualizacji RHUI

Obrazy RHEL połączone z aktualizacją RHUI domyślnie do najnowszej wersji pomocniczej RHEL w przypadku `yum update` uruchomienia. Takie zachowanie oznacza, że maszyna wirtualna z systemem RHEL 7,4 może zostać uaktualniona do RHEL 7,7, jeśli `yum update` operacja zostanie uruchomiona na tym komputerze. To zachowanie jest zaprojektowane dla RHUI. Aby wyeliminować to zachowanie uaktualnienia, przełączać się od zwykłych repozytoriów RHEL do [rozszerzonych repozytoriów pomocy technicznej aktualizacji](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [obrazach RHEL na platformie Azure](./redhat-images.md).
* Dowiedz się więcej o [infrastrukturze aktualizacji Red Hat](./redhat-rhui.md).
* Dowiedz się więcej na temat [oferty Red Hat Gold Image ( `rhel-byos` )](./byos.md).
