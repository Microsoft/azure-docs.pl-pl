---
title: Aplikacja do łączenia wystąpienia zarządzanego
description: W tym artykule omówiono sposób łączenia aplikacji z Azure SQL Database wystąpieniem zarządzanym.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: dc945dec20a15b02287e50bcbef4be0d860c892f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773661"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Łączenie aplikacji z wystąpieniem zarządzanym usługi Azure SQL Database

Dzisiaj możesz wybrać wiele opcji podczas decydowania, jak i gdzie hostować aplikację.

Możesz wybrać opcję hostowania aplikacji w chmurze przy użyciu Azure App Service lub niektórych opcji zintegrowanych sieci wirtualnej platformy Azure, takich jak Azure App Service Environment, maszyna wirtualna, zestaw skalowania maszyn wirtualnych. Możesz również zastosować podejście do chmury hybrydowej i utrzymywać swoje aplikacje lokalnie.

Niezależnie od dokonanego wyboru możesz połączyć go z wystąpieniem zarządzanym.  

![Wysoka dostępność](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Łączenie aplikacji w tej samej sieci wirtualnej

Ten scenariusz jest najprostszy. Maszyny wirtualne wewnątrz sieci wirtualnej mogą łączyć się ze sobą bezpośrednio nawet wtedy, gdy znajdują się w różnych podfolderach. Oznacza to, że wszystko, co jest potrzebne do połączenia aplikacji w środowisku aplikacji platformy Azure lub na maszynie wirtualnej, ma odpowiednio ustawiać parametry połączenia.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Łączenie aplikacji w innej sieci wirtualnej

Ten scenariusz jest nieco bardziej skomplikowany, ponieważ wystąpienie zarządzane ma prywatny adres IP w własnej sieci wirtualnej. Aby nawiązać połączenie, aplikacja musi mieć dostęp do sieci wirtualnej, w której wdrożono wystąpienie zarządzane. Dlatego najpierw należy nawiązać połączenie między aplikacją i siecią wirtualną wystąpienia zarządzanego. Aby ten scenariusz działał, sieci wirtualnych nie musi znajdować się w tej samej subskrypcji.

Dostępne są dwie opcje łączenia sieci wirtualnych:

- [Wirtualne sieci równorzędne platformy Azure](../virtual-network/virtual-network-peering-overview.md)
- Brama sieci VPN typu sieć wirtualna-sieć wirtualna ([Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [interfejs wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Opcja komunikacji równorzędnej jest preferowana, ponieważ Komunikacja równorzędna używa sieci szkieletowej firmy Microsoft, w związku z czym nie istnieje zauważalna różnica w opóźnieniu między maszynami wirtualnymi w komunikacji równorzędnej i w tej samej sieci wirtualnej. Wirtualne sieci równorzędne są ograniczone do sieci w tym samym regionie.  

> [!IMPORTANT]
> Scenariusz wirtualnej komunikacji równorzędnej dla wystąpienia zarządzanego jest ograniczony do sieci w tym samym regionie ze względu na [ograniczenia globalnej Virtual Network komunikacji równorzędnej](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Więcej informacji można znaleźć w sekcji dotyczącej [często zadawanych pytań dotyczących usługi Azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . 

## <a name="connect-an-on-premises-application"></a>Nawiązywanie połączenia z aplikacją lokalną

Dostęp do wystąpienia zarządzanego można uzyskać tylko za pomocą prywatnego adresu IP. Aby można było uzyskać do niego dostęp z lokalnego, należy nawiązać połączenie lokacja-lokacja między aplikacją i siecią wirtualną wystąpienia zarządzanego.

Istnieją dwie opcje łączenia się z siecią lokalną do sieci wirtualnej platformy Azure:

- Połączenie sieci VPN typu lokacja-lokacja ([Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [interfejs wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- Połączenie [ExpressRoute](../expressroute/expressroute-introduction.md)  

Jeśli lokalne połączenie z platformą Azure zostało pomyślnie ustanowione i nie można nawiązać połączenia z wystąpieniem zarządzanym, sprawdź, czy Zapora ma otwarte połączenie wychodzące na porcie SQL 1433 oraz 11000-11999 zakres portów do przekierowania.

## <a name="connect-an-application-on-the-developers-box"></a>Połącz aplikację w usłudze box deweloperów

Do wystąpienia zarządzanego można uzyskać dostęp tylko za pośrednictwem prywatnego adresu IP, dlatego w celu uzyskania dostępu do niego z poziomu dewelopera należy najpierw nawiązać połączenie między polem dewelopera i siecią wirtualną wystąpienia zarządzanego. W tym celu należy skonfigurować połączenie typu punkt-lokacja z siecią wirtualną przy użyciu natywnego uwierzytelniania certyfikatu platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia punkt-lokacja w celu nawiązania połączenia z wystąpieniem zarządzanym Azure SQL Database z komputera lokalnego](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Nawiązywanie połączenia z siecią lokalną za pomocą komunikacji równorzędnej sieci wirtualnych

Innym scenariuszem wdrożonym przez klientów jest to, że Brama sieci VPN jest zainstalowana w oddzielnej sieci wirtualnej i w ramach subskrypcji z jednego wystąpienia zarządzanego hostingu. Dwie sieci wirtualne są następnie połączone za pomocą komunikacji równorzędnej. Poniższy przykładowy diagram architektury przedstawia, w jaki sposób można go zaimplementować.

![Komunikacja równorzędna sieci wirtualnych](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Po skonfigurowaniu podstawowej infrastruktury należy zmodyfikować niektóre ustawienia, aby VPN Gateway mogli zobaczyć adresy IP w sieci wirtualnej, która hostuje wystąpienie zarządzane. W tym celu wprowadź następujące bardzo szczegółowe zmiany w obszarze **ustawienia komunikacji równorzędnej**.

1. W sieci wirtualnej, która hostuje bramę VPN Gateway, przejdź do **komunikacji równorzędnej**, następnie do połączenia sieci równorzędnej z wystąpieniami zarządzanymi, a następnie kliknij przycisk **Zezwalaj na tranzyt bramy**.
2. W sieci wirtualnej, która obsługuje wystąpienie zarządzane, przejdź do **komunikacji równorzędnej**, następnie do VPN Gateway połączenia sieci wirtualnej równorzędnej, a następnie kliknij opcję **Użyj bram zdalnych**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Połącz aplikację hostowaną Azure App Service

Do wystąpienia zarządzanego można uzyskać dostęp tylko za pośrednictwem prywatnego adresu IP, dlatego w celu uzyskania dostępu do niego z Azure App Service należy najpierw nawiązać połączenie między aplikacją a siecią wirtualną wystąpienia zarządzanego. Zobacz [Integrowanie aplikacji z usługą Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).  

Aby uzyskać informacje o rozwiązywaniu problemów, zobacz [Rozwiązywanie problemów z sieci wirtualnych i aplikacjami](../app-service/web-sites-integrate-with-vnet.md#troubleshooting) Jeśli nie można nawiązać połączenia, spróbuj [zsynchronizować konfigurację sieci](sql-database-managed-instance-sync-network-configuration.md).

Szczególnym przypadkiem łączenia Azure App Service z wystąpieniem zarządzanym jest zintegrowanie Azure App Service z siecią równorzędną do sieci wirtualnej wystąpienia zarządzanego. W takim przypadku wymagane jest skonfigurowanie następującej konfiguracji:

- Sieć wirtualna wystąpienia zarządzanego nie może mieć bramy  
- Sieć wirtualna wystąpienia zarządzanego musi mieć ustawioną opcję Użyj bram zdalnych
- Komunikacja równorzędna sieci wirtualnej musi mieć ustawioną opcję Zezwalaj na tranzyt bramy

Ten scenariusz przedstawiono na poniższym diagramie:

![Zintegrowana Komunikacja równorzędna aplikacji](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>Funkcja integracji sieci wirtualnej nie integruje aplikacji z siecią wirtualną, która ma bramę ExpressRoute. Nawet jeśli Brama ExpressRoute jest skonfigurowana w trybie współistnienia, integracja sieci wirtualnej nie działa. Jeśli musisz uzyskać dostęp do zasobów za pomocą połączenia usługi ExpressRoute, możesz użyć App Service Environment, które działa w sieci wirtualnej.

## <a name="troubleshooting-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Aby rozwiązać problemy z łącznością, zapoznaj się z następującymi tematami:

- Jeśli nie można nawiązać połączenia z wystąpieniem zarządzanym z maszyny wirtualnej platformy Azure w tej samej sieci wirtualnej, ale innej podsieci, sprawdź, czy w podsieci maszyny wirtualnej jest ustawiona sieciowa Grupa zabezpieczeń, która może blokować dostęp. Należy również pamiętać, że należy otworzyć połączenie wychodzące na porcie SQL 1433, a także porty z zakresu 11000-11999, ponieważ są one potrzebne do łączenia się w ramach granicy platformy Azure.
- Upewnij się, że propagacja BGP jest **włączona** dla tabeli tras skojarzonej z siecią wirtualną.
- W przypadku korzystania z sieci VPN P2S Sprawdź konfigurację w Azure Portal, aby zobaczyć, czy są wyświetlane numery ruchu przychodzącego **/** wychodzącego. Liczby niezerowe wskazują, że platforma Azure będzie kierować ruchem do/z lokalnego.

   ![numery ruchu przychodzącego/wychodzącego](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Sprawdź, czy maszyna kliencka (z uruchomionym klientem sieci VPN) ma wpisy trasy dla wszystkich sieci wirtualnych, do których należy uzyskać dostęp. Trasy są przechowywane w `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` .

   ![Route. txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Jak pokazano na tym obrazie, istnieją dwa wpisy dla każdej sieci wirtualnej i trzeci wpis dla punktu końcowego sieci VPN, który jest skonfigurowany w portalu.

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

- W przypadku używania komunikacji równorzędnej sieci wirtualnych upewnij się, że wykonano instrukcje dotyczące ustawienia [Zezwalaj na tranzyt bramy i używanie bram zdalnych](#connect-from-on-premises-with-vnet-peering).

- W przypadku używania komunikacji równorzędnej sieci wirtualnych do łączenia Azure App Service hostowanej aplikacji, a sieć wirtualna wystąpienia zarządzanego ma publiczny zakres adresów IP, upewnij się, że ustawienia aplikacji hostowanej umożliwiają kierowanie ruchu wychodzącego do publicznych sieci IP. Postępuj zgodnie z instrukcjami w ramach [integracji regionalnej sieci wirtualnej](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration).

## <a name="required-versions-of-drivers-and-tools"></a>Wymagane wersje sterowników i narzędzi

Następujące minimalne wersje narzędzi i sterowników są zalecane, jeśli chcesz połączyć się z wystąpieniem zarządzanym:

| Sterownik/narzędzie | Wersja |
| --- | --- |
|.NET Framework | 4.6.1 (lub .NET Core) |
|Sterownik ODBC| v17 |
|Sterownik PHP| 5.2.0 |
|Sterownik JDBC| 6.4.0 |
|Sterownik Node.js| 2.1.1 |
|Sterownik OLEDB| 18.0.2.0 |
|SSMS| 18,0 lub [więcej](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) lub więcej |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o wystąpieniu zarządzanym, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md).
- Samouczek pokazujący, jak utworzyć nowe wystąpienie zarządzane, można znaleźć w temacie [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
