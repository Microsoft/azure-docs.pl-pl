---
title: Łączenie usługi Azure ExpressRoute z infrastrukturą Oracle Cloud | Dokumenty firmy Microsoft
description: Połączenie usługi Azure ExpressRoute z usługą Oracle Cloud Infrastructure (OCI) FastConnect w celu umożliwienia rozwiązań aplikacji Oracle między chmurami
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: borisb
ms.openlocfilehash: 70556cbbfefd6ad22ef96ee16065209031ea456c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683755"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Konfigurowanie bezpośrednich połączeń między platformą Azure a infrastrukturą Oracle Cloud Infrastructure  

Aby stworzyć [zintegrowane środowisko wielochmurowe,](oracle-oci-overview.md)firmy Microsoft i Oracle oferują bezpośrednie połączenia między platformą Azure a infrastrukturą Oracle Cloud Infrastructure (OCI) za pośrednictwem [usług ExpressRoute](../../../expressroute/expressroute-introduction.md) i [FastConnect.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) Dzięki połączeniu typu ExpressRoute i FastConnect klienci mogą doświadczyć małych opóźnień, dużej przepustowości i prywatnej łączności bezpośredniej między dwiema chmurami.

> [!IMPORTANT]
> Oracle zaświadczy te aplikacje do uruchomienia na platformie Azure podczas korzystania z rozwiązania Azure / Oracle Cloud interconnect do maja 2020 r.
> * Pakiet e-biznesowy
> * JD Edwards EnterpriseOne
> * Peoplesoft
> * Aplikacje Oracle Retail
> * Zarządzanie finansami Oracle Hyperion

Na poniższej ilustracji przedstawiono ogólny przegląd połączeń wzajemnych:

![Połączenie sieciowe między chmurami](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Aby ustanowić łączność między platformą Azure i OCI, musisz mieć aktywną subskrypcję platformy Azure i aktywny najem OCI.

* Łączność jest możliwa tylko wtedy, gdy lokalizacja komunikacji równorzędnej usługi Azure ExpressRoute znajduje się w pobliżu lub w tej samej lokalizacji komunikacji równorzędnej co usługa OCI FastConnect. Zobacz [Dostępność regionu](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Konfigurowanie bezpośredniej łączności między programem ExpressRoute a fastconnect

1. Utwórz standardowy obwód usługi ExpressRoute w ramach subskrypcji platformy Azure w ramach grupy zasobów. 
    * Podczas tworzenia usługi ExpressRoute wybierz **Oracle Cloud FastConnect** jako dostawcę usług. Aby utworzyć obwód usługi ExpressRoute, zobacz [przewodnik krok po kroku](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Obwód usługi Azure ExpressRoute udostępnia szczegółowe opcje przepustowości, podczas gdy fastconnect obsługuje 1, 2, 5 lub 10 Gb/s. W związku z tym zaleca się, aby wybrać jedną z tych opcji pasujących przepustowości w obszarze Usługi ExpressRoute.

    ![Tworzenie obwodu usługi ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Note down your ExpressRoute **Service key**. Podczas konfigurowania obwodu FastConnect należy podać klucz.

    ![Klucz usługi Usługi usługi ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Opłaty za usługi ExpressRoute zostaną obciążone natychmiast po zainicjowaniu obwodu usługi ExpressRoute (nawet jeśli **stan dostawcy** nie jest **aprowizowany).**

1. Wykroij dwie prywatne przestrzenie adresów IP o powierzchni /30, które nie pokrywają się z siecią wirtualną platformy Azure lub przestrzenią adresów IP sieci wirtualnej OCI. Pierwszą przestrzeń adresową IP będziemy odnosić jako podstawową przestrzeń adresową, a drugą przestrzeń adresową IP jako pomocniczą przestrzeń adresową. Zanotuj adresy, które są potrzebne podczas konfigurowania obwodu FastConnect.
1. Utwórz dynamiczną bramę routingu (DRG). Będzie to potrzebne podczas tworzenia obwodu FastConnect. Aby uzyskać więcej informacji, zobacz dokumentację [bramy routingu dynamicznego.](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)
1. Utwórz obwód FastConnect pod najemcą Oracle. Aby uzyskać więcej informacji, zobacz [dokumentację Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * W obszarze Konfiguracja fastconnect wybierz pozycję **Microsoft Azure: ExpressRoute** jako dostawcę.
    * Wybierz bramę routingu dynamicznego zainicjowany w poprzednim kroku.
    * Wybierz przepustowość, która ma być aprowizowana. Aby uzyskać optymalną wydajność, przepustowość musi być zgodna z przepustowością wybraną podczas tworzenia obwodu usługi ExpressRoute.
    * W **kluczu usługi dostawcy**wklej klucz usługi Usługi ExpressRoute.
    * Użyj pierwszej /30 prywatnej przestrzeni adresów IP wyrzeźbionej w poprzednim kroku dla **podstawowego adresu IP BGP** i drugiej /30 prywatnej przestrzeni adresowej IP dla pomocniczego adresu **IP BGP.**
        * Przypisz pierwszy użyteczny adres dwóch zakresów dla adresu IP Oracle BGP (podstawowy i pomocniczy) oraz drugi adres do adresu IP BGP klienta (z perspektywy fastconnect). Pierwszy użyteczny adres IP to drugi adres IP w przestrzeni adresowej /30 (pierwszy adres IP jest zarezerwowany przez firmę Microsoft).
    * Kliknij przycisk **Utwórz**.
1. Ukończ połączenie fastconnect z wirtualną siecią chmurową w ramach dzierżawy Oracle za pośrednictwem bramy routingu dynamicznego przy użyciu tabeli tras.
1. Przejdź do platformy Azure i upewnij się, że **stan dostawcy** dla obwodu usługi ExpressRoute został zmieniony na **Aprowizowana** i że komunikacja równorzędna typu **Azure private** została aprowizowana. Jest to warunek wstępny dla następujących kroków.

    ![Stan dostawcy usługi ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Kliknij prywatną komunikację równorzędnych **platformy Azure.** Zobaczysz, że szczegóły komunikacji równorzędnej zostały automatycznie skonfigurowane na podstawie informacji wprowadzonych podczas konfigurowania obwodu FastConnect.

    ![Ustawienia prywatnej komunikacji równorzędnej](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Łączenie sieci wirtualnej z usługą ExpressRoute

1. Utwórz sieć wirtualną i bramę sieci wirtualnej, jeśli jeszcze tego nie zrobiłeś. Szczegółowe informacje można znaleźć w [przewodniku krok po kroku](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Skonfiguruj połączenie między bramą sieci wirtualnej a obwodem usługi ExpressRoute, wykonując [skrypt Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) lub wykonując polecenie programu PowerShell w celu skonfigurowania programu [ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Po zakończeniu konfiguracji sieci można zweryfikować ważność konfiguracji, klikając **pozycję Pobierz rekordy ARP** i **Pobierz tabelę marszruty** w bloku Komunikacji prywatnej usługi ExpressRoute w witrynie Azure portal.

## <a name="automation"></a>Automatyzacja

Firma Microsoft utworzyła skrypty Terraform, aby umożliwić automatyczne wdrażanie sieci łączenia. Skrypty Terraform muszą uwierzytelnić się przy użyciu platformy Azure przed wykonaniem, ponieważ wymagają odpowiednich uprawnień do subskrypcji platformy Azure. Uwierzytelnianie można wykonać przy użyciu [jednostki usługi Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) lub przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [dokumentację terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Skrypty Terraform i powiązana dokumentacja do wdrożenia połączenia wzajemnego można znaleźć w tym [repozytorium GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitorowanie

Instalując agentów w obu chmurach, można korzystać z usługi Azure [Network Performance Monitor (NPM)](../../../expressroute/how-to-npm.md) do monitorowania wydajności sieci end-to-end. Npm pomaga łatwo zidentyfikować problemy z siecią i pomaga je wyeliminować.

## <a name="delete-the-interconnect-link"></a>Usuwanie łącza łączącego

Aby usunąć połączenie, należy wykonać następujące kroki w określonej kolejności. Niezastosowanie się do tego spowoduje "stan awarii" obwód usługi ExpressRoute.

1. Usuń połączenie Usługi ExpressRoute. Usuń połączenie, klikając ikonę **Usuń** na stronie połączenia. Aby uzyskać więcej informacji, zobacz [dokumentację usługi ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Usuń oracle FastConnect z konsoli Oracle Cloud Console.
1. Po usunięciu obwodu Oracle FastConnect można usunąć obwód Azure ExpressRoute.

W tym momencie proces usuwania i usuwania obsługi administracyjnej został zakończony.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat połączenia między chmurami między OCI i Azure, zobacz [dokumentację Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Skrypty Terraform umożliwia [wdrażanie](https://aka.ms/azureociinterconnecttf) infrastruktury dla ukierunkowanych aplikacji Oracle za pośrednictwem platformy Azure i konfigurowanie sieci połączonych. 
