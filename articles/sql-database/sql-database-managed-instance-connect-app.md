---
title: Aplikacja do łączenia wystąpienia zarządzanego
description: W tym artykule omówiono sposób łączenia aplikacji z wystąpieniem zarządzanym usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 8d920fb7815e5a9fe30d8f3b4e40f36133d83222
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538090"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Łączenie aplikacji z wystąpieniem zarządzanym usługi Azure SQL Database

Dziś masz wiele możliwości wyboru przy podejmowaniu decyzji, jak i gdzie hostujesz aplikację.

Możesz obsługiwać aplikację w chmurze za pomocą usługi Azure App Service lub niektórych zintegrowanych opcji sieci wirtualnej platformy Azure, takich jak środowisko usługi Azure App Service, maszyna wirtualna, zestaw skalowania maszyny wirtualnej. Można również przyjąć podejście chmury hybrydowej i zachować aplikacje w środowisku lokalnym.

Niezależnie od dokonanego wyboru, można połączyć go z wystąpieniem zarządzanym.  

![wysoka dostępność](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Łączenie aplikacji wewnątrz tej samej sieci wirtualnej

Ten scenariusz jest najprostszy. Maszyny wirtualne wewnątrz sieci wirtualnej można połączyć się ze sobą bezpośrednio, nawet jeśli znajdują się one wewnątrz różnych podsieci. Oznacza to, że wszystko, czego potrzebujesz do połączenia aplikacji wewnątrz środowiska aplikacji platformy Azure lub maszyny wirtualnej jest ustawienie ciągu połączenia odpowiednio.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Łączenie aplikacji w innej sieci wirtualnej

Ten scenariusz jest nieco bardziej złożony, ponieważ wystąpienie zarządzane ma prywatny adres IP we własnej sieci wirtualnej. Aby nawiązać połączenie, aplikacja potrzebuje dostępu do sieci wirtualnej, w której jest wdrażane wystąpienie zarządzane. Dlatego najpierw należy nawiązać połączenie między aplikacją a siecią wirtualną wystąpienia zarządzanego. Sieci wirtualne nie muszą być w tej samej subskrypcji, aby ten scenariusz działał.

Istnieją dwie opcje łączenia sieci wirtualnych:

- [Wirtualne sieci równorzędne platformy Azure](../virtual-network/virtual-network-peering-overview.md)
- Brama sieci VPN sieci wirtualnej[(Azure portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Opcja komunikacji równorzędnej jest preferowana, ponieważ komunikacja równorzędna używa sieci szkieletowej firmy Microsoft, więc z punktu widzenia łączności nie ma zauważalnej różnicy w opóźnieniu między maszynami wirtualnymi w sieci wirtualnej równorzędnej i w tej samej sieci wirtualnej. Komunikacja równorzędna sieci wirtualnej jest ograniczona do sieci w tym samym regionie.  

> [!IMPORTANT]
> Scenariusz komunikacji równorzędnej sieci wirtualnej dla wystąpienia zarządzanego jest ograniczony do sieci w tym samym regionie ze względu [na ograniczenia komunikacji równorzędnej globalnej sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Zobacz również odpowiednią sekcję [usługi Azure Virtual Networks Często zadawane pytania](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) artykułu, aby uzyskać więcej informacji. 

## <a name="connect-an-on-premises-application"></a>Nawiązywanie połączenia z aplikacją lokalną

Wystąpienie zarządzane jest dostępne tylko za pośrednictwem prywatnego adresu IP. Aby uzyskać do niego dostęp z lokalnego, należy nawiązać połączenie lokacja lokacja między aplikacją a siecią wirtualną wystąpienia zarządzanego.

Istnieją dwie opcje łączenia się lokalnie z siecią wirtualną platformy Azure:

- Połączenie sieci VPN z lokacją[(Portal Azure,](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- Połączenie [usługi ExpressRoute](../expressroute/expressroute-introduction.md)  

Jeśli połączenie lokalne z platformą Azure zostało pomyślnie ustanowione i nie można ustanowić połączenia z wystąpieniem zarządzanym, sprawdź, czy zapora ma otwarte połączenie wychodzące na porcie SQL 1433 oraz w zakresie portów 11000-11999 w celu przekierowania.

## <a name="connect-an-application-on-the-developers-box"></a>Połącz aplikację w usłudze box deweloperów

Wystąpienie zarządzanego można uzyskać tylko za pośrednictwem prywatnego adresu IP, więc aby uzyskać do niego dostęp z pola dewelopera, najpierw należy nawiązać połączenie między pole dewelopera i sieci wirtualnej wystąpienia zarządzanego. Aby to zrobić, skonfiguruj połączenie typu punkt-lokacja z siecią wirtualną przy użyciu natywnego uwierzytelniania certyfikatów platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia typu punkt-lokacja w celu nawiązania połączenia z wystąpieniem zarządzanym bazy danych SQL platformy Azure z komputera lokalnego](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Łączenie się z lokalnego połączenia za pomocą komunikacji równorzędnej sieci wirtualnej

Innym scenariuszem zaimplementowanym przez klientów jest to, gdzie brama sieci VPN jest zainstalowana w oddzielnej sieci wirtualnej i subskrypcji z jednego hosta wystąpienia zarządzanego. Dwie sieci wirtualne są następnie równorzędne. Poniższy przykładowy diagram architektury pokazuje, jak można to zaimplementować.

![Komunikacja równorzędna sieci wirtualnych](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Po skonfigurowaniu podstawowej infrastruktury należy zmodyfikować pewne ustawienie, aby brama sieci VPN mogła zobaczyć adresy IP w sieci wirtualnej, w której znajduje się wystąpienie zarządzane. Aby to zrobić, należy wprowadzić następujące bardzo konkretne zmiany w **ustawieniach komunikacji równorzędnej**.

1. W sieci wirtualnej, która obsługuje bramę sieci VPN, przejdź do **strony Równorzędne**, a następnie do połączenia sieci wirtualnej z elementami zarządzanych wystąpienia, a następnie kliknij pozycję **Zezwalaj na przesyłanie bramy**.
2. W sieci wirtualnej, która obsługuje wystąpienie zarządzane, przejdź do **strony Równorzędne**, a następnie do połączenia sieci wirtualnej sieci wirtualnej równorzędnej bramy sieci VPN, a następnie kliknij pozycję **Użyj bram zdalnych**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Łączenie hostowanych aplikacji usługi Azure App Service

Wystąpienie zarządzanego można uzyskać tylko za pośrednictwem prywatnego adresu IP, więc aby uzyskać do niego dostęp z usługi Azure App Service, należy najpierw nawiązać połączenie między aplikacją a siecią wirtualną wystąpienia zarządzanego. Zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md).  

Aby zapoznać się z problemami, zobacz [Rozwiązywanie problemów z sieciami wirtualnymi i aplikacjami](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Jeśli nie można ustanowić połączenia, spróbuj [zsynchronizować konfigurację sieci .](sql-database-managed-instance-sync-network-configuration.md)

Szczególnym przypadkiem łączenia usługi Azure App Service z wystąpieniem zarządzanym jest zintegrowanie usługi Azure App Service z siecią równorzędną z siecią wirtualną wystąpienia zarządzanego. W takim przypadku należy skonfigurować następującą konfigurację:

- Sieci wirtualnej wystąpienia zarządzanego nie może mieć bramy  
- Sieci wirtualnej wystąpienia zarządzanego musi mieć ustawioną opcję Użyj bram zdalnych
- Sieć wirtualna komunikacji równorzędnej musi mieć ustawioną opcję Zezwalaj na tranzyt bramy

Ten scenariusz jest zilustrowany na poniższym diagramie:

![zintegrowana komunikacja równorzędna aplikacji](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>Funkcja integracji sieci wirtualnej nie integruje aplikacji z siecią wirtualną, która ma bramę usługi ExpressRoute. Nawet jeśli brama usługi ExpressRoute jest skonfigurowana w trybie współistnienia, integracja sieci wirtualnej nie działa. Jeśli chcesz uzyskać dostęp do zasobów za pośrednictwem połączenia usługi ExpressRoute, możesz użyć środowiska usługi app service, który jest uruchamiany w sieci wirtualnej.

## <a name="troubleshooting-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Aby rozwiązać problemy z łącznością, zapoznaj się z następującymi problemami:

- Jeśli nie można połączyć się z wystąpieniem zarządzanym z maszyny wirtualnej platformy Azure w tej samej sieci wirtualnej, ale w innej podsieci, sprawdź, czy w podsieci maszyny wirtualnej ustawiono sieciową grupę zabezpieczeń, która może blokować dostęp. Ponadto należy pamiętać, że należy otworzyć połączenie wychodzące na porcie SQL 1433, a także porty w zakresie 11000-11999, ponieważ są one potrzebne do łączenia się za pośrednictwem przekierowania wewnątrz granicy platformy Azure.
- Upewnij się, że propagacja BGP jest **ustawiona** na Włączone dla tabeli marszruty skojarzonej z siecią wirtualną.
- Jeśli używasz sieci VPN P2S, sprawdź konfigurację w witrynie Azure portal, aby sprawdzić, czy widzisz numery **transferu danych przychodzących/wychodzących.** Liczby inne niż zero wskazują, że platforma Azure kieruje ruch do/z lokalnego.

   ![numery wychodzące/wychodzące](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Sprawdź, czy na komputerze klienckim (który jest uruchomiony klient sieci VPN) ma wpisy trasy dla wszystkich sieci wirtualnych, które należy uzyskać dostęp. Trasy są przechowywane `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`w pliku .

   ![plik route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Jak pokazano na tym obrazie, istnieją dwa wpisy dla każdej sieci wirtualnej zaangażowanych i trzeci wpis dla punktu końcowego sieci VPN, który jest skonfigurowany w portalu.

   Innym sposobem sprawdzenia tras jest następujące polecenie. Dane wyjściowe pokazują trasy do różnych podsieci:

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

- Jeśli korzystasz z komunikacji równorzędnej sieci wirtualnej, upewnij się, że użytkownik postępował zgodnie z instrukcjami ustawiania zezwalania na [przesyłanie bramy i używanie bram zdalnych](#connect-from-on-premises-with-vnet-peering).

- Jeśli do łączenia aplikacji hostowanych usługi Azure App Service jest używanie komunikacji równorzędnej sieci wirtualnej, a sieć wirtualna wystąpienia zarządzanego ma zakres publicznego adresu IP, upewnij się, że hostowane ustawienia aplikacji zezwalają na kierowanie ruchu wychodzącego do publicznych sieci IP. Postępuj zgodnie z instrukcjami w [regionalnej integracji sieci wirtualnej](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration).

## <a name="required-versions-of-drivers-and-tools"></a>Wymagane wersje sterowników i narzędzi

Jeśli chcesz połączyć się z wystąpieniem zarządzanym, zalecane są następujące minimalne wersje narzędzi i sterowników:

| Sterownik/narzędzie | Wersja |
| --- | --- |
|.NET Framework | 4.6.1 (lub .NET Core) |
|Sterownik ODBC| wersja 17 |
|Sterownik PHP| 5.2.0 |
|Sterownik JDBC| 6.4.0 |
|Sterownik Node.js| 2.1.1 |
|Sterownik OLEDB| 18.0.2.0 |
|SSMS| 18.0 lub [więcej](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) lub więcej |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o [wystąpieniu zarządzanym,](sql-database-managed-instance.md)zobacz Co to jest wystąpienie zarządzane .
- Aby zapoznać się z samouczkiem przedstawiającym sposób tworzenia nowego wystąpienia [zarządzanego,](sql-database-managed-instance-get-started.md)zobacz Tworzenie wystąpienia zarządzanego .
