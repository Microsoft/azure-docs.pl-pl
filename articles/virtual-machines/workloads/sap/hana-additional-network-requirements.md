---
title: Dodatkowe wymagania dotyczące sieci dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Dodatkowe wymagania dotyczące sieci dla SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 44e9712466734c0e42fd2bea05c5110cbff6924b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964800"
---
# <a name="additional-network-requirements-for-large-instances"></a>Dodatkowe wymagania dotyczące sieci dla dużych wystąpień

Dodatkowe wymagania dotyczące sieci mogą być dostępne w ramach wdrożenia dużych wystąpień SAP HANA na platformie Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Dodawanie większej liczby adresów IP lub podsieci

Użyj Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure w przypadku dodawania większej liczby adresów IP lub podsieci.

Dodaj nowy zakres adresów IP jako nowy zakres do przestrzeni adresowej sieci wirtualnej, zamiast generować nowy zagregowany zakres. Prześlij tę zmianę do firmy Microsoft. Umożliwia to nawiązanie połączenia z tym nowym zakresem adresów IP z jednostkami dużej instancji HANA w kliencie. Możesz otworzyć żądanie pomocy technicznej platformy Azure, aby pobrać nową przestrzeń adresową sieci wirtualnej. Po otrzymaniu potwierdzenia wykonaj następne kroki.

Aby utworzyć dodatkową podsieć z Azure Portal, zobacz [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Aby utworzyć jedną z poziomu programu PowerShell, zobacz [Tworzenie sieci wirtualnej przy użyciu programu PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Dodaj sieci wirtualne

Po nawiązaniu połączenia z jedną lub wieloma sieciami wirtualnymi platformy Azure możesz połączyć dodatkowe SAP HANA na platformie Azure (duże wystąpienia). Najpierw Prześlij żądanie pomocy technicznej platformy Azure. W tym żądaniu należy uwzględnić konkretne informacje identyfikujące konkretne wdrożenie platformy Azure. Uwzględnij również zakres przestrzeni adresów IP lub zakresy przestrzeni adresów sieci wirtualnej platformy Azure. SAP HANA w usłudze Microsoft Service Management udostępnia informacje niezbędne do połączenia dodatkowych sieci wirtualnych i usługi Azure ExpressRoute. Dla każdej sieci wirtualnej potrzebny jest unikatowy klucz autoryzacji do łączenia się z obwodem usługi ExpressRoute z dużymi wystąpieniami platformy HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Zwiększ przepustowość obwodu ExpressRoute

Zapoznaj się z SAP HANA w usłudze Microsoft Service Management. Jeśli poradzisz zwiększyć przepustowość SAP HANA na platformie Azure (duże wystąpienia), Utwórz żądanie pomocy technicznej platformy Azure. (Możesz poprosić o zwiększenie przepustowości pojedynczego obwodu do maksymalnie 10 GB/s). Po zakończeniu operacji otrzymasz powiadomienie. nie musisz wykonywać żadnych innych czynności, aby umożliwić wyższą szybkość działania na platformie Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Dodaj dodatkowy obwód ExpressRoute

Zapoznaj się z SAP HANA w usłudze Microsoft Service Management. Jeśli zalecisz dodanie dodatkowego obwodu usługi ExpressRoute, Utwórz żądanie pomocy technicznej platformy Azure (w tym żądanie pobrania informacji o autoryzacji w celu nawiązania połączenia z nowym obwodem). Przed wykonaniem żądania należy zdefiniować przestrzeń adresową używaną w sieciach wirtualnych. SAP HANA w usłudze Microsoft Service Management może zapewnić autoryzację.

Po utworzeniu nowego obwodu i zakończeniu SAP HANA w konfiguracji zarządzania usługami firmy Microsoft zostanie wyświetlone powiadomienie z informacjami, które należy wykonać. Nie można połączyć sieci wirtualnych platformy Azure z tym dodatkowym obwodem, jeśli są już połączone z innym SAP HANA na platformie Azure (duże wystąpienie) obwód ExpressRoute w tym samym regionie świadczenia usługi Azure.

## <a name="delete-a-subnet"></a>Usuwanie podsieci

Aby usunąć podsieć sieci wirtualnej, można użyć Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Jeśli zakres adresów IP sieci wirtualnej platformy Azure lub przestrzeń adresowa była zagregowanym zakresem, nie ma żadnych monitów dla Ciebie z firmą Microsoft. (Pamiętaj jednak, że sieć wirtualna nadal propaguje przestrzeń adresową trasy BGP obejmującą usuniętą podsieć). Można zdefiniować zakres adresów sieci wirtualnej platformy Azure lub przestrzeń adresową jako wiele zakresów adresów IP, z których jedna została przypisana do usuniętej podsieci. Pamiętaj, aby usunąć ten obszar z przestrzeni adresowej sieci wirtualnej. Następnie poinformuj SAP HANA w usłudze Microsoft Service Management, aby usunąć ją z zakresów, które SAP HANA na platformie Azure (duże wystąpienia) mogą komunikować się z usługą.

Aby uzyskać więcej informacji, zobacz [usuwanie podsieci](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Usuwanie sieci wirtualnej

Aby uzyskać więcej informacji, zobacz [usuwanie sieci wirtualnej](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

SAP HANA w usłudze Microsoft Service Management usuwa istniejące autoryzacje na SAP HANA na platformie Azure (duże wystąpienia) obwodu ExpressRoute. Usuwa również zakres adresów IP sieci wirtualnej platformy Azure lub przestrzeń adresową na potrzeby komunikacji z dużymi wystąpieniami HANA.

Po usunięciu sieci wirtualnej Otwórz żądanie pomocy technicznej platformy Azure, aby podać zakres przestrzeni adresów IP lub zakresy do usunięcia.

Aby upewnić się, że usuniesz wszystko, Usuń połączenie ExpressRoute, bramę sieci wirtualnej, publiczny adres IP bramy sieci wirtualnej i sieć wirtualną.

## <a name="delete-an-expressroute-circuit"></a>Usuwanie obwodu ExpressRoute

Aby usunąć dodatkowe SAP HANA na platformie Azure (duże wystąpienia) obwodu usługi ExpressRoute, Otwórz żądanie pomocy technicznej platformy Azure z SAP HANA w usłudze Microsoft Service Management. Żądaj, aby obwód został usunięty. W ramach subskrypcji platformy Azure możesz w razie potrzeby usunąć lub utrzymywać sieć wirtualną. Należy jednak usunąć połączenie między obwodem usługi ExpressRoute duże wystąpienia HANA i połączoną bramą sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

- [Jak zainstalować i skonfigurować oprogramowanie SAP HANA (duże wystąpienia) na platformie Azure](hana-installation.md)
