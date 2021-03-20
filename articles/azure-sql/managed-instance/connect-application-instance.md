---
title: Łączenie aplikacji z wystąpieniem zarządzanym SQL
titleSuffix: Azure SQL Managed Instance
description: W tym artykule omówiono sposób łączenia aplikacji z wystąpieniem zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 3106ec1a45b307207624e5cd077a222acccc83d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692831"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Łączenie aplikacji z wystąpieniem zarządzanym usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dzisiaj możesz wybrać wiele opcji podczas decydowania, jak i gdzie hostować aplikację.

Możesz wybrać opcję hostowania aplikacji w chmurze, korzystając z Azure App Service lub niektórych opcji zintegrowanych z siecią wirtualną platformy Azure, takich jak Azure App Service Environment, Virtual Machines platformy Azure i zestawy skalowania maszyn wirtualnych. Możesz również zastosować podejście do chmury hybrydowej i utrzymywać swoje aplikacje lokalnie.

Niezależnie od dokonanego wyboru możesz połączyć go z wystąpieniem zarządzanym usługi Azure SQL. 

![Wysoka dostępność](./media/connect-application-instance/application-deployment-topologies.png)

W tym artykule opisano sposób łączenia aplikacji z usługą Azure SQL Managed Instance w wielu różnych scenariuszach aplikacji. 

## <a name="connect-inside-the-same-vnet"></a>Połącz wewnątrz tej samej sieci wirtualnej

Najprostszym scenariuszem jest łączenie aplikacji w ramach tej samej sieci wirtualnej co wystąpienie zarządzane SQL. Maszyny wirtualne wewnątrz sieci wirtualnej mogą łączyć się ze sobą bezpośrednio nawet wtedy, gdy znajdują się w różnych podsieciach. Oznacza to, że wszystko, co jest potrzebne do połączenia aplikacji wewnątrz App Service Environment lub maszyny wirtualnej, ma odpowiednio ustawiać parametry połączenia.  

## <a name="connect-inside-a-different-vnet"></a>Łączenie wewnątrz innej sieci wirtualnej

Połączenie aplikacji znajdującej się w innej sieci wirtualnej z wystąpienia zarządzanego SQL jest nieco bardziej skomplikowane, ponieważ wystąpienie zarządzane SQL ma prywatne adresy IP w własnej sieci wirtualnej. Aby nawiązać połączenie, aplikacja musi mieć dostęp do sieci wirtualnej, w której wdrożono wystąpienie zarządzane SQL. Należy więc nawiązać połączenie między aplikacją a siecią wirtualną wystąpienia zarządzanego SQL. Aby ten scenariusz działał, sieci wirtualne nie muszą znajdować się w tej samej subskrypcji.

Istnieją dwie opcje łączenia sieci wirtualnych:

- [Komunikacja równorzędna sieci wirtualnej platformy Azure](../../virtual-network/virtual-network-peering-overview.md)
- Brama sieci VPN typu sieć wirtualna-sieć wirtualna ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [interfejs wiersza polecenia platformy Azure](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Komunikacja równorzędna jest preferowana, ponieważ używa sieci szkieletowej firmy Microsoft, dlatego z punktu widzenia łączności nie ma zauważalnej różnicy opóźnienia między maszynami wirtualnymi w wirtualnej sieci równorzędnej i w tej samej sieci wirtualnej. Komunikacja równorzędna sieci wirtualnych jest obsługiwana między sieciami w tym samym regionie. Globalne wirtualne sieci równorzędne są również obsługiwane z ograniczeniami opisanymi w poniższej uwadze.  

> [!IMPORTANT]
> [W dniu 9/22/2020 ogłoszono globalne wirtualne sieci równorzędne dla nowo utworzonych klastrów wirtualnych](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Oznacza to, że globalne wirtualne sieci równorzędne są obsługiwane dla wystąpień zarządzanych SQL utworzonych w pustych podsieciach, a także dla wszystkich kolejnych wystąpień zarządzanych utworzonych w tych podsieciach. W przypadku wszystkich innych obsługi komunikacji równorzędnej usługi SQL Managed Instances jest ograniczone do sieci w tym samym regionie ze względu na [ograniczenia globalnej komunikacji równorzędnej sieci wirtualnej](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Więcej informacji można znaleźć w sekcji dotyczącej [często zadawanych pytań dotyczących usługi Azure Virtual Networks](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . 

## <a name="connect-from-on-premises"></a>Nawiązywanie połączenia z lokalnego 

Możesz również połączyć aplikację lokalną z wystąpieniem zarządzanym SQL. Dostęp do wystąpienia zarządzanego SQL można uzyskać tylko za pomocą prywatnego adresu IP. Aby można było uzyskać do niego dostęp z lokalnego, należy nawiązać połączenie lokacja-lokacja między aplikacją a siecią wirtualną wystąpienia zarządzanego SQL.

Istnieją dwie opcje łączenia lokalnego z siecią wirtualną platformy Azure:

- Połączenie sieci VPN typu lokacja-lokacja ([Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [interfejs wiersza polecenia platformy Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- Połączenie [usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md)  

Jeśli nawiązanie połączenia lokalnego z platformą Azure powiodło się i nie można nawiązać połączenia z wystąpieniem zarządzanym SQL, sprawdź, czy Zapora ma otwarte połączenie wychodzące na porcie SQL 1433 oraz 11000-11999 zakres portów do przekierowania.

## <a name="connect-the-developer-box"></a>Połącz pole dewelopera

Istnieje również możliwość połączenia Twojego dewelopera z wystąpieniem zarządzanym SQL. Do wystąpienia zarządzanego SQL można uzyskać dostęp tylko za pośrednictwem prywatnego adresu IP, dlatego w celu uzyskania dostępu do niego z poziomu dewelopera musisz najpierw nawiązać połączenie między polem dewelopera i siecią wirtualną wystąpienia zarządzanego SQL. W tym celu należy skonfigurować połączenie typu punkt-lokacja z siecią wirtualną przy użyciu natywnego uwierzytelniania certyfikatu platformy Azure. Aby uzyskać więcej informacji, zobacz  [Konfigurowanie połączenia punkt-lokacja w celu nawiązania połączenia z wystąpieniem zarządzanym usługi Azure SQL na komputerze lokalnym](point-to-site-p2s-configure.md).

## <a name="connect-with-vnet-peering"></a>Nawiązywanie połączenia przy użyciu komunikacji równorzędnej sieci wirtualnych

Innym scenariuszem wdrożonym przez klientów jest to, że Brama sieci VPN jest zainstalowana w oddzielnej sieci wirtualnej i subskrypcji z jednego hostingu wystąpienia zarządzanego SQL. Dwie sieci wirtualne są następnie połączone za pomocą komunikacji równorzędnej. Poniższy przykładowy diagram architektury przedstawia, w jaki sposób można go zaimplementować.

![Komunikacja równorzędna sieci wirtualnej](./media/connect-application-instance/vnet-peering.png)

Po skonfigurowaniu podstawowej infrastruktury należy zmodyfikować niektóre ustawienia, aby Brama sieci VPN widziała adresy IP w sieci wirtualnej, która hostuje wystąpienie zarządzane SQL. W tym celu wprowadź następujące bardzo szczegółowe zmiany w obszarze **ustawienia komunikacji równorzędnej**.

1. W sieci wirtualnej, która hostuje bramę sieci VPN, przejdź do obszaru **komunikacji równorzędnej**, przejdź do połączenia wirtualnej sieci równorzędnej dla wystąpienia zarządzanego SQL, a następnie kliknij przycisk **Zezwalaj na tranzyt bramy**.
2. W sieci wirtualnej, która hostuje wystąpienie zarządzane SQL, przejdź do obszaru **komunikacji równorzędnej**, przejdź do połączenia sieci wirtualnej komunikacji równorzędnej dla bramy sieci VPN, a następnie kliknij opcję **Użyj bram zdalnych**.

## <a name="connect-azure-app-service"></a>Połącz Azure App Service 

Możesz również połączyć aplikację hostowaną przez Azure App Service. Do wystąpienia zarządzanego SQL można uzyskać dostęp tylko za pośrednictwem prywatnego adresu IP, dlatego w celu uzyskania dostępu do niego z Azure App Service należy najpierw nawiązać połączenie między aplikacją a siecią wirtualną wystąpienia zarządzanego SQL. Zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../../app-service/web-sites-integrate-with-vnet.md).  

Aby uzyskać informacje na temat rozwiązywania problemów, zobacz [Rozwiązywanie problemów dotyczących sieci wirtualnych i aplikacji](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting) Jeśli nie można nawiązać połączenia, spróbuj [zsynchronizować konfigurację sieci](azure-app-sync-network-configuration.md).

Szczególnym przypadkiem łączenia Azure App Service z wystąpieniem zarządzanym SQL jest zintegrowanie Azure App Service z siecią równorzędną do sieci wirtualnej wystąpienia zarządzanego SQL. W takim przypadku wymagane jest skonfigurowanie następującej konfiguracji:

- Sieć wirtualna wystąpienia zarządzanego SQL nie może mieć bramy  
- Sieć wirtualna wystąpienia zarządzanego SQL musi mieć `Use remote gateways` ustawioną opcję
- Równorzędna Sieć wirtualna musi mieć `Allow gateway transit` ustawioną opcję

Ten scenariusz przedstawiono na poniższym diagramie:

![Zintegrowana Komunikacja równorzędna aplikacji](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>Funkcja integracji sieci wirtualnej nie integruje aplikacji z siecią wirtualną, która ma bramę ExpressRoute. Nawet jeśli Brama ExpressRoute jest skonfigurowana w trybie współistnienia, integracja z siecią wirtualną nie działa. Jeśli musisz uzyskać dostęp do zasobów za pomocą połączenia usługi ExpressRoute, możesz użyć App Service Environment, które działa w sieci wirtualnej.

## <a name="troubleshooting-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Aby rozwiązać problemy z łącznością, zapoznaj się z następującymi tematami:

- Jeśli nie można nawiązać połączenia z wystąpieniem zarządzanym SQL z maszyny wirtualnej platformy Azure w tej samej sieci wirtualnej, ale innej podsieci, sprawdź, czy w podsieci maszyny wirtualnej jest ustawiona sieciowa Grupa zabezpieczeń, która może blokować dostęp. Ponadto Otwórz połączenie wychodzące na porcie SQL 1433, a także porty z zakresu 11000-11999, ponieważ są one zbędne do łączenia się w ramach granicy platformy Azure.
- Upewnij się, że propagacja protokołu BGP jest **włączona** dla tabeli tras skojarzonej z siecią wirtualną.
- W przypadku korzystania z sieci VPN P2S Sprawdź konfigurację w Azure Portal, aby zobaczyć, czy są wyświetlane numery ruchu przychodzącego **/** wychodzącego. Liczby niezerowe wskazują, że platforma Azure będzie kierować ruchem do/z lokalnego.

   ![numery ruchu przychodzącego/wychodzącego](./media/connect-application-instance/ingress-egress-numbers.png)

- Sprawdź, czy maszyna kliencka (z uruchomionym klientem sieci VPN) ma wpisy trasy dla wszystkich sieci wirtualnych, do których należy uzyskać dostęp. Trasy są przechowywane w `%AppData%\Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` .

   ![route.txt](./media/connect-application-instance/route-txt.png)

   Jak pokazano na tym obrazie, istnieją dwa wpisy dla każdej z tych sieci wirtualnych i trzeci wpis dla punktu końcowego sieci VPN, który jest skonfigurowany w portalu.

   Innym sposobem sprawdzenia tras jest za pomocą następującego polecenia. Dane wyjściowe pokazują trasy do różnych podsieci:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Jeśli używasz komunikacji równorzędnej sieci wirtualnej, upewnij się, że wykonano instrukcje dotyczące ustawienia [Zezwalaj na tranzyt bramy i używanie bram zdalnych](#connect-from-on-premises).

- Jeśli używasz komunikacji równorzędnej sieci wirtualnej, aby połączyć aplikację hostowaną Azure App Service, a sieć wirtualna wystąpienia zarządzanego SQL ma publiczny zakres adresów IP, upewnij się, że ustawienia hostowanej aplikacji umożliwiają kierowanie ruchu wychodzącego do publicznych sieci IP. Postępuj zgodnie z instrukcjami zawartymi w temacie [integracja z regionalną siecią wirtualną](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration).

## <a name="required-versions-of-drivers-and-tools"></a>Wymagane wersje sterowników i narzędzi

Jeśli chcesz połączyć się z wystąpieniem zarządzanym SQL, zaleca się zastosowanie następujących minimalnych wersji narzędzi i sterowników:

| Sterownik/narzędzie | Wersja |
| --- | --- |
|.NET Framework | 4.6.1 (lub .NET Core) |
|Sterownik ODBC| v17 |
|Sterownik PHP| 5.2.0 |
|Sterownik JDBC| 6.4.0 |
|Sterownik Node.js| 2.1.1 |
|Sterownik OLEDB| 18.0.2.0 |
|SSMS| 18,0 lub [więcej](/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) lub więcej |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o wystąpieniu zarządzanym SQL, zobacz [co to jest wystąpienie zarządzane SQL?](sql-managed-instance-paas-overview.md)
- Samouczek pokazujący, jak utworzyć nowe wystąpienie zarządzane, można znaleźć w temacie [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).