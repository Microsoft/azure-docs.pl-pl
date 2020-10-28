---
title: Nawiązywanie połączenia z maszyną wirtualną SQL Server (Menedżer zasobów) | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z SQL Serverą maszyną wirtualną na platformie Azure. W tym temacie jest stosowany klasyczny model wdrażania. Scenariusze różnią się w zależności od konfiguracji sieci i lokalizacji klienta.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f8bee990074debf09cc9bfd19f96470a029b50c9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793130"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Nawiązywanie połączenia z maszyną wirtualną SQL Server na platformie Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>Omówienie

W tym temacie opisano sposób nawiązywania połączenia z programem SQL Server na maszynie wirtualnej platformy Azure. Obejmuje ona niektóre [Ogólne scenariusze łączności](#connection-scenarios) , a następnie zawiera [kroki w portalu umożliwiające zmianę ustawień łączności](#change). Jeśli musisz rozwiązać problemy lub skonfigurować łączność poza portalem, zapoznaj się z [konfiguracją ręczną](#manual) na końcu tego tematu. 

Jeśli wolisz korzystać z pełnego przewodnika po aprowizacji i łączności, zobacz temat [Inicjowanie obsługi administracyjnej SQL Server maszyny wirtualnej na platformie Azure](create-sql-vm-portal.md).

## <a name="connection-scenarios"></a>Scenariusze połączeń

Sposób, w jaki klient nawiązuje połączenie z maszyną wirtualną SQL Server, różni się w zależności od lokalizacji klienta i konfiguracji sieci.

Jeśli zainicjujesz maszynę wirtualną SQL Server w Azure Portal, będziesz mieć możliwość określenia typu **łączności z programem SQL** .

![Opcja publicznej łączności SQL podczas aprowizacji](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

Dostępne są następujące opcje łączności:

| Opcja | Opis |
|---|---|
| **Publiczne** | Połącz się z SQL Server przez Internet. |
| **Prywatne** | Połącz się z SQL Server w tej samej sieci wirtualnej. |
| **LAN** | Połącz się z SQL Server lokalnie na tej samej maszynie wirtualnej. | 

W poniższych sekcjach opisano opcje **publiczne** i **prywatne** bardziej szczegółowo.

## <a name="connect-to-sql-server-over-the-internet"></a>Nawiązywanie połączenia z SQL Server za pośrednictwem Internetu

Jeśli chcesz nawiązać połączenie z aparatem bazy danych SQL Server z Internetu, wybierz opcję **publiczny** dla typu **łączności SQL** w portalu podczas aprowizacji. Portal automatycznie wykonuje następujące czynności:

* Włącza protokół TCP/IP dla SQL Server.
* Konfiguruje regułę zapory, aby otworzyć SQL Server Port TCP (domyślnie 1433).
* Włącza uwierzytelnianie SQL Server wymagane na potrzeby dostępu publicznego.
* Konfiguruje sieciowe grupy zabezpieczeń na maszynie wirtualnej na cały ruch TCP na porcie SQL Server.

> [!IMPORTANT]
> W przypadku obrazów maszyn wirtualnych dla SQL Server Developer i wersji Express nie jest automatycznie włączany protokół TCP/IP. W przypadku wersji Developer i Express należy użyć Configuration Manager SQL Server, aby [ręcznie włączyć protokół TCP/IP](#manualtcp) po utworzeniu maszyny wirtualnej.

Dowolny klient z dostępem do Internetu może połączyć się z wystąpieniem SQL Server, określając publiczny adres IP maszyny wirtualnej lub dowolną etykietę DNS przypisaną do tego adresu IP. Jeśli port SQL Server to 1433, nie trzeba określać go w parametrach połączenia. Poniższe parametry połączenia nawiązują połączenie z maszyną wirtualną SQL z etykietą DNS `sqlvmlabel.eastus.cloudapp.azure.com` przy użyciu uwierzytelniania SQL (można również użyć publicznego adresu IP).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Mimo że ten ciąg umożliwia łączność klientów przez Internet, nie oznacza to, że każdy może nawiązać połączenie z wystąpieniem SQL Server. Klienci zewnętrzni muszą używać prawidłowej nazwy użytkownika i hasła. Jednak w celu dodatkowego zabezpieczenia można uniknąć dobrze znanego portu 1433. Na przykład jeśli skonfigurowano SQL Server do nasłuchiwania na porcie 1500 i ustanowienia odpowiednich reguł zapory i sieciowej grupy zabezpieczeń, można nawiązać połączenie, dołączając numer portu do nazwy serwera. Poniższy przykład modyfikuje poprzednią wartość przez dodanie niestandardowego numeru portu, **1500** do nazwy serwera:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Podczas wykonywania zapytań w SQL Server na maszynie wirtualnej przez Internet wszystkie dane wychodzące z centrum danych platformy Azure podlegają normalnym [cennikom wychodzących transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Nawiązywanie połączenia z SQL Server w sieci wirtualnej

Po wybraniu opcji **prywatny** dla typu **łączności SQL** w portalu platforma Azure skonfiguruje większość ustawień identycznych z **publiczną** . Różnica polega na tym, że nie istnieje reguła sieciowej grupy zabezpieczeń zezwalająca na ruch zewnętrzny na porcie SQL Server (domyślnie 1433).

> [!IMPORTANT]
> W przypadku obrazów maszyn wirtualnych dla SQL Server Developer i wersji Express nie jest automatycznie włączany protokół TCP/IP. W przypadku wersji Developer i Express należy użyć Configuration Manager SQL Server, aby [ręcznie włączyć protokół TCP/IP](#manualtcp) po utworzeniu maszyny wirtualnej.

Połączenie prywatne jest często używane w połączeniu z [siecią wirtualną](../../../virtual-network/virtual-networks-overview.md), co umożliwia wykonywanie kilku scenariuszy. Możesz połączyć maszyny wirtualne w tej samej sieci wirtualnej, nawet jeśli te maszyny wirtualne znajdują się w różnych grupach zasobów. Za pomocą [sieci VPN typu lokacja-lokacja](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)można utworzyć architekturę hybrydową, która łączy maszyny wirtualne z lokalnymi sieciami i maszynami.

Sieci wirtualne umożliwiają również dołączanie maszyn wirtualnych platformy Azure do domeny. Jest to jedyny sposób używania uwierzytelniania systemu Windows do SQL Server. Inne scenariusze połączeń wymagają uwierzytelniania SQL z nazwami użytkowników i hasłami.

Przy założeniu, że w sieci wirtualnej skonfigurowano system DNS, można nawiązać połączenie z wystąpieniem SQL Server, SQL Server określając nazwę komputera maszyny wirtualnej w parametrach połączenia. W poniższym przykładzie przyjęto również założenie, że uwierzytelnianie systemu Windows zostało skonfigurowane i że użytkownikowi udzielono dostępu do wystąpienia SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a> Zmień ustawienia łączności SQL

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W Azure Portal można zmienić ustawienia łączności dla maszyny wirtualnej SQL Server.

1. W Azure Portal wybierz pozycję **SQL Virtual** Machines.

2. Wybierz maszynę wirtualną SQL Server.

3. W obszarze **Ustawienia** wybierz pozycję **zabezpieczenia** .

4. Zmień **poziom łączności SQL** na wymagane ustawienie. Opcjonalnie możesz użyć tego obszaru, aby zmienić port SQL Server lub ustawienia uwierzytelniania SQL.

   ![Zmień łączność SQL](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Poczekaj kilka minut, aż aktualizacja zostanie ukończona.

   ![Powiadomienie o aktualizacji maszyny wirtualnej SQL](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a> Włączanie protokołu TCP/IP dla wersji Developer i Express

W przypadku zmiany ustawień łączności SQL Server platforma Azure nie włącza automatycznie protokołu TCP/IP dla wersji SQL Server Developer i Express. W poniższych krokach omówiono, jak ręcznie włączyć protokół TCP/IP w celu zdalnego nawiązania połączenia przy użyciu adresu IP.

Najpierw Połącz się z SQL Server maszyną wirtualną za pomocą pulpitu zdalnego.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Następnie Włącz protokół TCP/IP przy użyciu **SQL Server Configuration Manager** .

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Nawiązywanie połączenia z programem SSMS

Poniższe kroki pokazują, jak utworzyć opcjonalną etykietę DNS dla maszyny wirtualnej platformy Azure, a następnie połączyć się z SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a> Ręczna konfiguracja i rozwiązywanie problemów

Mimo że Portal udostępnia opcje automatycznego konfigurowania łączności, warto wiedzieć, jak ręcznie skonfigurować łączność. Zrozumienie wymagań może również pomóc w rozwiązywaniu problemów.

W poniższej tabeli przedstawiono wymagania dotyczące łączenia się z SQL Server na maszynie wirtualnej platformy Azure.

| Wymaganie | Opis |
|---|---|
| [Włącz tryb uwierzytelniania SQL Server](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | Uwierzytelnianie SQL Server jest potrzebne do zdalnego nawiązywania połączenia z maszyną wirtualną, o ile nie skonfigurowano Active Directory w sieci wirtualnej. |
| [Tworzenie identyfikatora logowania SQL](/sql/relational-databases/security/authentication-access/create-a-login) | Jeśli używasz uwierzytelniania SQL, potrzebujesz logowania SQL z nazwą użytkownika i hasłem, które ma także uprawnienia do docelowej bazy danych. |
| [Włącz protokół TCP/IP](#manualtcp) | SQL Server musi zezwalać na połączenia za pośrednictwem protokołu TCP. |
| [Włącz regułę zapory dla portu SQL Server](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Zapora na maszynie wirtualnej musi zezwalać na ruch przychodzący na porcie SQL Server (domyślnie 1433). |
| [Tworzenie reguły sieciowej grupy zabezpieczeń dla protokołu TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Musisz zezwolić na odbieranie przez maszynę wirtualną ruchu na SQL Server Port (domyślnie 1433), jeśli chcesz nawiązać połączenie za pośrednictwem Internetu. Połączenia lokalne i wirtualne tylko w sieci nie wymagają tego. Jest to jedyny krok wymagany w Azure Portal. |

> [!TIP]
> Kroki opisane w powyższej tabeli są wykonywane podczas konfigurowania połączenia w portalu. Wykonaj te czynności tylko w celu potwierdzenia konfiguracji lub ręcznego skonfigurowania łączności dla SQL Server.

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić instrukcje aprowizacji wraz z tymi krokami łączności, zobacz [Inicjowanie obsługi administracyjnej SQL Server maszyny wirtualnej na platformie Azure](create-sql-vm-portal.md).

Aby poznać inne tematy związane z uruchamianiem SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server w usłudze Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).