---
title: Łączenie platformy Azure ExpressRoute z usługą Oracle Cloud Infrastructure | Microsoft Docs
description: Łączenie platformy Azure ExpressRoute z usługą Oracle Cloud Infrastructure (OCI) FastConnect w celu włączenia rozwiązań aplikacji Oracle dla wielu chmur
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 03/16/2020
ms.author: rogardle
ms.reviewer: cynthn
ms.openlocfilehash: 2717ba307cac82465e0c5df996ef3b668af5e7d4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963712"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Skonfiguruj bezpośrednie połączenie między platformą Azure a infrastrukturą chmurową Oracle  

Aby utworzyć [zintegrowane środowisko z obsługą chmury](oracle-oci-overview.md), firma Microsoft i oprogramowanie Oracle oferują bezpośrednie połączenie między platformą Azure i usługą Oracle Cloud Infrastructure (OCI) za pośrednictwem usług [ExpressRoute](../../../expressroute/expressroute-introduction.md) i [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Za pośrednictwem połączeń ExpressRoute i FastConnect klienci mogą mieć małe opóźnienia, wysoką przepływność i prywatną łączność bezpośrednią między dwiema chmurami.

> [!IMPORTANT]
> Firma Oracle poświadczy te aplikacje do uruchamiania na platformie Azure w przypadku korzystania z rozwiązania Azure Cloud Interconnect w chmurze na 2020.
> * Pakiet E-Business
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * Aplikacje sieci sprzedaży firmy Oracle
> * Zarządzanie finansowe Oracle Hyperion

Na poniższej ilustracji przedstawiono ogólne omówienie połączeń:

![Połączenie sieciowe między chmurami](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Aby nawiązać połączenie między platformą Azure a OCI, musisz mieć aktywną subskrypcję platformy Azure i aktywną dzierżawę OCI.

* Połączenie jest możliwe tylko wtedy, gdy lokalizacja komunikacji równorzędnej usługi Azure ExpressRoute znajduje się w pobliżu lub w tej samej lokalizacji komunikacji równorzędnej co OCI FastConnect. Zobacz [dostępność regionów](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Konfigurowanie bezpośredniej łączności między ExpressRoute i FastConnect

1. Utwórz standardowy obwód usługi ExpressRoute w ramach subskrypcji platformy Azure w grupie zasobów. 
    * Podczas tworzenia ExpressRoute wybierz pozycję **Oracle Cloud FastConnect** jako dostawcę usług. Aby utworzyć obwód ExpressRoute, zobacz [Przewodnik krok po kroku](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Obwód usługi Azure ExpressRoute zapewnia szczegółowe opcje przepustowości, a FastConnect obsługuje 1, 2, 5 lub 10 GB/s. W związku z tym zalecane jest wybranie jednej z tych opcji dopasowania przepustowości w obszarze ExpressRoute.

    ![Utwórz obwód ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Zanotuj **klucz usługi** ExpressRoute. Musisz podać klucz podczas konfigurowania obwodu FastConnect.

    ![Klucz usługi ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Opłaty za ExpressRoute będą naliczane natychmiast po zainicjowaniu obwodu usługi ExpressRoute (nawet jeśli **stan dostawcy** **nie jest zainicjowany**).

1. Wydzielenie z dwóch prywatnych przestrzeni adresowych IP/30, które nie nakładają się na przestrzeń adresową IP sieci wirtualnej platformy Azure lub sieci wirtualnej OCI. Odwołujemy się do pierwszej przestrzeni adresów IP jako podstawowej przestrzeni adresowej, a druga przestrzeń adresów IP jako pomocnicza przestrzeń adresowa. Zanotuj adresy wymagane podczas konfigurowania obwodu FastConnect.
1. Utwórz bramę dynamicznej routingu (DRG). Będzie to potrzebne podczas tworzenia obwodu FastConnect. Aby uzyskać więcej informacji, zobacz dokumentację [dynamicznej bramy routingu](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) .
1. Utwórz obwód FastConnect w ramach dzierżawy firmy Oracle. Aby uzyskać więcej informacji, zobacz [dokumentację firmy Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * W obszarze Konfiguracja FastConnect wybierz pozycję **Microsoft Azure: ExpressRoute** jako dostawcę.
    * Wybierz bramę dynamicznego routingu, która została zainicjowana w poprzednim kroku.
    * Wybierz przepustowość, która ma zostać zainicjowana. W celu uzyskania optymalnej wydajności przepustowość musi być zgodna z przepustowością wybraną podczas tworzenia obwodu usługi ExpressRoute.
    * W **kluczu usługi dostawcy** Wklej klucz usługi ExpressRoute.
    * Użyj pierwszej/30 prywatnej przestrzeni adresów IP używać miejsca w poprzednim kroku dla **podstawowego adresu IP protokołu BGP** i drugiej/30 prywatnej przestrzeni adresów IP dla **POMOCNICZego adresu IP protokołu BGP** .
        * Przypisz pierwszy możliwy do użycia adres dla adresów IP Oracle (podstawowy i pomocniczy), a drugi adres adres IP protokołu BGP klienta (z perspektywy FastConnect). Pierwszy użyteczny adres IP to drugi adres IP w przestrzeni adresowej/30 (pierwszy adres IP jest zastrzeżony przez firmę Microsoft).
    * Kliknij pozycję **Utwórz**.
1. Dokończ łączenie FastConnect z wirtualną siecią chmurową w ramach dzierżawy Oracle za pośrednictwem bramy routingu dynamicznego przy użyciu tabeli tras.
1. Przejdź do platformy Azure i upewnij się, że **stan dostawcy** dla obwodu usługi ExpressRoute został zmieniony na **zainicjowany** i że Zainicjowano obsługę komunikacji równorzędnej typu **Azure Private** . Jest to wymaganie wstępne dla następujących kroków.

    ![Stan dostawcy ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Kliknij prywatną komunikację równorzędną **platformy Azure** . Szczegóły dotyczące komunikacji równorzędnej zostaną automatycznie skonfigurowane na podstawie informacji wprowadzonych podczas konfigurowania obwodu FastConnect.

    ![Ustawienia prywatnej komunikacji równorzędnej](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Łączenie sieci wirtualnej z usługą ExpressRoute

1. Utwórz sieć wirtualną i bramę sieci wirtualnej, jeśli jeszcze tego nie zrobiono. Aby uzyskać szczegółowe informacje, zobacz [Przewodnik krok po kroku](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Skonfiguruj połączenie między bramą sieci wirtualnej i obwodem usługi ExpressRoute, wykonując [skrypt Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) lub wykonując polecenie programu PowerShell w celu [skonfigurowania FastPath ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Po zakończeniu konfiguracji sieci można sprawdzić poprawność konfiguracji, klikając pozycję **Pobierz rekordy ARP** i **Pobierz tabelę tras** w bloku prywatnej komunikacji równorzędnej ExpressRoute w Azure Portal.

## <a name="automation"></a>Automatyzacja

Firma Microsoft stworzyła skrypty Terraform, aby umożliwić automatyczne wdrażanie połączeń sieciowych. Przed wykonaniem skryptów Terraform należy przeprowadzić uwierzytelnienie na platformie Azure, ponieważ wymagają one odpowiednich uprawnień w ramach subskrypcji platformy Azure. Uwierzytelnianie można wykonać przy użyciu jednostki [usługi Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) lub przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją Terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Skrypty Terraform i pokrewna dokumentacja do wdrożenia połączenia wzajemnego można znaleźć w tym [repozytorium GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitorowanie

Zainstalowanie agentów w obu chmurach umożliwia monitorowanie wydajności kompleksowej sieci za pomocą usługi Azure [Network Performance Monitor (npm)](../../../expressroute/how-to-npm.md) . Program NPM ułatwia identyfikowanie problemów z siecią i pomaga je wyeliminować.

## <a name="delete-the-interconnect-link"></a>Usuwanie linku programu InterConnect

Aby można było usunąć połączenie, należy wykonać następujące czynności w określonej kolejności. Niewykonanie tej czynności spowoduje powstanie obwodu ExpressRoute o stanie "Niepowodzenie".

1. Usuń połączenie ExpressRoute. Usuń połączenie, klikając ikonę **usuwania** na stronie połączenia. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#clean-up-resources).
1. Usuń z programu Oracle FastConnect z poziomu konsoli programu Oracle Cloud.
1. Po usunięciu obwodu programu Oracle FastConnect można usunąć obwód usługi Azure ExpressRoute.

W tym momencie proces usuwania i anulowania aprowizacji został ukończony.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat połączenia międzychmurowego między OCI i platformą Azure, zapoznaj się z [dokumentacją firmy Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Za pomocą [skryptów Terraform](https://aka.ms/azureociinterconnecttf) można wdrażać infrastrukturę dla aplikacji przeznaczonych dla programu Oracle za pośrednictwem platformy Azure i konfigurować połączenia sieciowe. 
