---
title: Nawiązywanie połączenia z maszyną wirtualną SQL Server na platformie Azure (wersja klasyczna) | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z usługą SQL Server działającą na maszynie wirtualnej na platformie Azure. W tym temacie jest stosowany klasyczny model wdrażania. Scenariusze różnią się w zależności od konfiguracji sieci i lokalizacji klienta.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental: true
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: a4b63735509c0d1c755eced8277d18b2a535457b
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078446"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Łączenie z maszyną wirtualną programu SQL Server na platformie Azure (wdrażanie klasyczne)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)
> * [Klasyczny](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Omówienie
W tym temacie opisano sposób nawiązywania połączenia z wystąpieniem SQL Server uruchomionym na maszynie wirtualnej platformy Azure. Obejmuje ona niektóre [Ogólne scenariusze łączności](#connection-scenarios) , a następnie zawiera [szczegółowe instrukcje dotyczące konfigurowania łączności SQL Server na maszynie wirtualnej platformy Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Jeśli używasz maszyn wirtualnych Menedżer zasobów, zobacz [nawiązywanie połączenia z maszyną wirtualną SQL Server na platformie Azure przy użyciu Menedżer zasobów](../../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md).

## <a name="connection-scenarios"></a>Scenariusze połączeń
Sposób, w jaki klient nawiązuje połączenie z SQL Server uruchomionym na maszynie wirtualnej, różni się w zależności od lokalizacji klienta i konfiguracji komputera/sieci. Scenariusze obejmują:

* [Połącz z SQL Server w tej samej usłudze w chmurze](#connect-to-sql-server-in-the-same-cloud-service)
* [Nawiązywanie połączenia z SQL Server za pośrednictwem Internetu](#connect-to-sql-server-over-the-internet)
* [Połącz z SQL Server w tej samej sieci wirtualnej](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Przed nawiązaniem połączenia z dowolną z tych metod, należy wykonać [kroki opisane w tym artykule, aby skonfigurować łączność](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Połącz z SQL Server w tej samej usłudze w chmurze
W tej samej usłudze w chmurze można utworzyć wiele maszyn wirtualnych. Aby zrozumieć ten scenariusz maszyn wirtualnych, zobacz [jak połączyć maszyny wirtualne z siecią wirtualną lub usługą w chmurze](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service). W tym scenariuszu klient na jednej maszynie wirtualnej próbuje nawiązać połączenie z usługą SQL Server działającą na innej maszynie wirtualnej w tej samej usłudze w chmurze.

**W tym** scenariuszu można nawiązać połączenie przy użyciu **nazwy** maszyny wirtualnej (podanej **także w portalu** ). Jest to nazwa podana dla maszyny wirtualnej podczas jej tworzenia. Jeśli na przykład nazwa maszyny wirtualnej SQL **mysqlvm**, maszyna wirtualna w tej samej usłudze w chmurze może użyć następujących parametrów połączenia do nawiązania połączenia:

```config
"Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

### <a name="connect-to-sql-server-over-the-internet"></a>Nawiązywanie połączenia z SQL Server za pośrednictwem Internetu
Jeśli chcesz nawiązać połączenie z aparatem bazy danych SQL Server z Internetu, musisz utworzyć punkt końcowy maszyny wirtualnej dla przychodzącej komunikacji TCP. Ten krok konfiguracji platformy Azure powoduje kierowanie przychodzącego ruchu portów TCP do portu TCP, który jest dostępny dla maszyny wirtualnej.

Aby nawiązać połączenie za pośrednictwem Internetu, należy użyć nazwy DNS i numeru portu punktu końcowego maszyny wirtualnej (skonfigurowany w dalszej części tego artykułu). Aby znaleźć nazwę DNS, przejdź do Azure Portal i wybierz pozycję **maszyny wirtualne (klasyczne)**. Następnie wybierz swoją maszynę wirtualną. **Nazwa DNS** jest wyświetlana w sekcji **Przegląd** .

Rozważmy na przykład klasyczną maszynę wirtualną o nazwie **mysqlvm** z nazwą DNS **mysqlvm7777.cloudapp.NET** i punktem końcowym maszyny wirtualnej **57500**. Przy założeniu prawidłowo skonfigurowanej łączności następujące parametry połączenia mogą służyć do uzyskiwania dostępu do maszyny wirtualnej z dowolnego miejsca w Internecie:

```config
"Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

Chociaż te parametry połączenia umożliwiają łączność klientów przez Internet, nie oznacza to, że każdy może nawiązać połączenie z SQL Server. Klienci zewnętrzni muszą mieć poprawną nazwę użytkownika i hasło. Aby uzyskać dodatkowe zabezpieczenia, nie używaj dobrze znanego portu 1433 dla punktu końcowego publicznej maszyny wirtualnej. A jeśli to możliwe, rozważ dodanie listy ACL w punkcie końcowym, aby ograniczyć ruch tylko do klientów, którym zezwalasz. Aby uzyskać instrukcje dotyczące korzystania z list ACL z punktami końcowymi, zobacz [Zarządzanie listą kontroli dostępu w punkcie końcowym](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> W przypadku korzystania z tej techniki w celu komunikowania się z SQL Server wszystkie dane wychodzące z centrum danych platformy Azure podlegają normalnym [cennikowi wychodzących transferów](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Połącz z SQL Server w tej samej sieci wirtualnej
[Virtual Network](../../../virtual-network/virtual-networks-overview.md) umożliwia wykonywanie dodatkowych scenariuszy. Możesz połączyć maszyny wirtualne w tej samej sieci wirtualnej, nawet jeśli te maszyny wirtualne znajdują się w różnych usługach w chmurze. Za pomocą [sieci VPN typu lokacja-lokacja](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)można utworzyć architekturę hybrydową, która łączy maszyny wirtualne z lokalnymi sieciami i maszynami.

Sieci wirtualne umożliwiają również dołączanie maszyn wirtualnych platformy Azure do domeny. Przyłączanie do domeny jest jedynym sposobem korzystania z uwierzytelniania systemu Windows z SQL Server. Inne scenariusze połączeń wymagają uwierzytelniania SQL z nazwami użytkowników i hasłami.

Jeśli planujesz skonfigurowanie środowiska domeny i uwierzytelniania systemu Windows, nie musisz konfigurować publicznego punktu końcowego ani uwierzytelniania i logowania SQL. W tym scenariuszu można nawiązać połączenie z wystąpieniem SQL Server, określając nazwę maszyny wirtualnej SQL Server w parametrach połączenia. W poniższym przykładzie przyjęto założenie, że uwierzytelnianie systemu Windows zostało skonfigurowane i że użytkownik uzyskał dostęp do wystąpienia SQL Server.

```config
"Server=mysqlvm;Integrated Security=true"
```

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Kroki konfigurowania SQL Server łączności na maszynie wirtualnej platformy Azure
Poniższe kroki pokazują, jak nawiązać połączenie z wystąpieniem SQL Server za pośrednictwem Internetu przy użyciu SQL Server Management Studio (SSMS). Jednak te same kroki mają zastosowanie do udostępnienia SQL Server maszynie wirtualnej dla aplikacji, działającej zarówno lokalnie, jak i na platformie Azure.

Aby można było połączyć się z wystąpieniem SQL Server z innej maszyny wirtualnej lub z Internetu, należy wykonać następujące zadania:

* [Utwórz punkt końcowy TCP dla maszyny wirtualnej](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Otwieranie portów TCP w zaporze systemu Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Konfigurowanie programu SQL Server do nasłuchiwania w protokole TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Konfigurowanie programu SQL Server do uwierzytelniania w trybie mieszanym](#configure-sql-server-for-mixed-mode-authentication)
* [Tworzenie identyfikatora logowania do uwierzytelniania w programie SQL Server](#create-sql-server-authentication-logins)
* [Określ nazwę DNS maszyny wirtualnej](#determine-the-dns-name-of-the-virtual-machine)
* [Nawiązywanie połączenia z aparatem bazy danych z innego komputera](#connect-to-the-database-engine-from-another-computer)

Ścieżka połączenia jest podsumowana przez następujący Diagram:

![Nawiązywanie połączenia z maszyną wirtualną SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Następne kroki
Jeśli planujesz również używać Zawsze włączone grupy dostępności do zapewnienia wysokiej dostępności i odzyskiwania po awarii, należy rozważyć implementację odbiornika. Klienci bazy danych nawiązują połączenie z odbiornikiem, a nie bezpośrednio z jednym z wystąpień SQL Server. Odbiornik kieruje klientów do repliki podstawowej w grupie dostępności. Aby uzyskać więcej informacji, zobacz [Konfigurowanie odbiornika ILB na potrzeby zawsze włączone grupy dostępności na platformie Azure](../classic/ps-sql-int-listener.md).

Ważne jest zapoznanie się ze wszystkimi najlepszymi rozwiązaniami w zakresie zabezpieczeń dotyczącymi SQL Server uruchomionych na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące zabezpieczeń programu SQL Server w usłudze Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/security-considerations-best-practices.md).

[Zbadaj ścieżkę szkoleniową](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) dla programu SQL Server na maszynach wirtualnych Azure. 

Aby poznać inne tematy związane z uruchamianiem SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

