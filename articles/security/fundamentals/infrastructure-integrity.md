---
title: Integralność infrastruktury platformy Azure
description: Dowiedz się więcej na temat integralności infrastruktury platformy Azure oraz czynności podejmowanych przez firmę Microsoft, takich jak skanowanie wirusów w kompilacjach składników oprogramowania.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: b0df14dfcf66f53e01989a27a95afa5e19fcb2fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87543643"
---
# <a name="azure-infrastructure-integrity"></a>Integralność infrastruktury platformy Azure

## <a name="software-installation"></a>Instalacja oprogramowania
Wszystkie składniki w stosie oprogramowania, które są zainstalowane w środowisku platformy Azure, są niestandardowe skompilowane po procesie Microsoft Security Development Lifecycle (SDL). Wszystkie składniki oprogramowania, w tym obrazy systemu operacyjnego (OS) i SQL Database, są wdrażane w ramach procesu zarządzania zmianami i Release Management. System operacyjny uruchomiony na wszystkich węzłach jest dostosowaną wersją systemu Windows Server 2008 lub Windows Server 2012. Dokładna wersja jest wybierana przez kontroler sieci szkieletowej (FC) zgodnie z rolą, która ma zostać odtworzona dla systemu operacyjnego. Ponadto system operacyjny hosta nie zezwala na instalację żadnych nieautoryzowanych składników oprogramowania.

Niektóre składniki platformy Azure są wdrażane jako klienci platformy Azure na maszynie wirtualnej gościa działającej w systemie operacyjnym gościa.

## <a name="virus-scans-on-builds"></a>Skanowanie wirusów w kompilacjach
Kompilacje składnika oprogramowania platformy Azure (w tym systemu operacyjnego) muszą zostać przeskanowane przy użyciu narzędzia antywirusowego Endpoint Protection. Każde skanowanie antywirusowe tworzy dziennik w skojarzonym katalogu kompilacji, zawierający szczegóły dotyczące skanowania i wyników skanowania. Skanowanie antywirusowe jest częścią kodu źródłowego kompilacji dla każdego składnika na platformie Azure. Kod nie jest przenoszony do produkcji bez czystego i udanego skanowania antywirusowego. W przypadku wystąpienia jakichkolwiek problemów kompilacja zostaje zamrożona, a następnie przejdzie do zespołów zabezpieczeń w firmie Microsoft Security, aby określić, gdzie "nieautoryzowany" kod został wprowadzony.

## <a name="closed-and-locked-environment"></a>Zamknięte i zablokowane środowisko
Domyślnie węzły infrastruktury platformy Azure i maszyny wirtualne gościa nie mają utworzonych kont użytkownika. Ponadto domyślne konta administratorów systemu Windows również są wyłączone. Administratorzy usługi Azure Live Support mogą przy użyciu odpowiedniego uwierzytelniania zalogować się do tych maszyn i administrować siecią produkcyjną platformy Azure w celu naprawy awaryjnej.

## <a name="azure-sql-database-authentication"></a>Uwierzytelnianie w usłudze Azure SQL Database
Podobnie jak w przypadku każdej implementacji SQL Server, zarządzanie kontami użytkowników musi być ściśle kontrolowane. Azure SQL Database obsługuje tylko uwierzytelnianie SQL Server. Aby uzupełnić model zabezpieczeń danych klienta, należy również użyć kont użytkowników z silnymi hasłami i skonfigurować je przy użyciu określonych praw.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Listy ACL i zapory między siecią firmową firmy Microsoft a klastrem platformy Azure
Listy kontroli dostępu (ACL) i zapory między platformą usługi i siecią firmową firmy Microsoft chronią wystąpienia SQL Database od nieautoryzowanego dostępu do niejawnego programu. Dodatkowo tylko użytkownicy z zakresów adresów IP z sieci firmowej firmy Microsoft mogą uzyskać dostęp do punktu końcowego zarządzania platformą Windows Fabric.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>Listy ACL i zapory między węzłami w klastrze SQL Database
Jako dodatkowa ochrona w ramach strategii obrony, listy ACL i zapory zostały zaimplementowane między węzłami w klastrze SQL Database. Cała komunikacja w ramach klastra platformy Windows Fabric oraz cały uruchomiony kod jest zaufana.

## <a name="custom-monitoring-agents"></a>Agenci niestandardowego monitorowania
SQL Database korzysta z niestandardowych agentów monitorowania (MAs), nazywanych również licznikami alarmowymi, do monitorowania kondycji klastra SQL Database.

## <a name="web-protocols"></a>Protokoły sieci Web

### <a name="role-instance-monitoring-and-restart"></a>Monitorowanie i ponowne uruchamianie wystąpienia roli
System Azure zapewnia, że wszystkie wdrożone, uruchomione role (w sieci Web lub w celu przetwarzania zaplecza) są objęte monitorowaniem kondycji, aby zapewnić efektywne i wydajne dostarczanie usług, dla których Zainicjowano obsługę administracyjną. Jeśli rola stanie się zła w złej kondycji, na podstawie krytycznego błędu w aplikacji, która jest hostowana, lub podstawowego problemu konfiguracji w ramach samego wystąpienia roli, FC wykrywa problem w ramach wystąpienia roli i inicjuje stan naprawczy.

### <a name="compute-connectivity"></a>Łączność obliczeniowa
Platforma Azure zapewnia dostępność wdrożonej aplikacji lub usługi za pośrednictwem standardowych protokołów opartych na sieci Web. Wirtualne wystąpienia ról sieci Web dostępnych z Internetu mają zewnętrzną łączność z Internetem i są dostępne bezpośrednio dla użytkowników sieci Web. Aby chronić czułość i integralność operacji wykonywanych przez role procesów roboczych w imieniu wystąpień wirtualnych z publicznie dostępnymi wystąpieniami, wirtualne wystąpienia ról procesów roboczych przetwarzania zaplecza mają zewnętrzną łączność internetową, ale nie są dostępne bezpośrednio przez zewnętrznych użytkowników sieci Web.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat sposobu zabezpieczania infrastruktury platformy Azure przez firmę Microsoft, zobacz:

- [Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacji platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Azure SQL Database funkcje zabezpieczeń](infrastructure-sql.md)
- [Operacje produkcyjne platformy Azure i zarządzanie nimi](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Ochrona danych klienta platformy Azure](protection-customer-data.md)
