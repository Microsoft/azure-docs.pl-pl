---
title: Zawsze włączona Grupa dostępności z usługą DH2i DxEnterprise działającą na platformie Azure Virtual Machines opartej na systemie Linux
description: Użyj DH2i DxEnterprise jako Menedżera klastra, aby uzyskać wysoką dostępność z grupą dostępności na SQL Server on Linux Azure Virtual Machines
ms.date: 03/04/2021
ms.service: virtual-machines-linux
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 0500f4143ad7cbdaaa8406af2b242e0d40b1caa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102227400"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Samouczek — Konfigurowanie grupy dostępności z trzema węzłami przy użyciu usługi DH2i DxEnterprise działającej na platformie Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym samouczku wyjaśniono, jak SQL Server skonfigurować grupę dostępności w usłudze DH2i DxEnterprise działającą na platformie Azure Virtual Machines. 

Aby uzyskać więcej informacji na temat DxEnterprise, zobacz [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/).

> [!NOTE]
> Firma Microsoft obsługuje przenoszenie danych, grupę dostępności i składniki SQL Server. Skontaktuj się z DH2i, aby uzyskać pomoc techniczną dotyczącą dokumentacji klastra DH2i DxEnterprise w celu zarządzania klastrem i kworum.
 

Ten samouczek składa się z następujących kroków:

> [!div class="checklist"]
> * Zainstaluj SQL Server na wszystkich maszynach wirtualnych platformy Azure, które będą częścią grupy dostępności.
> * Zainstaluj program DxEnterprise na wszystkich maszynach wirtualnych i skonfiguruj klaster DxEnterprise.
> * Utwórz hosty wirtualne, aby zapewnić obsługę trybu failover i wysoką dostępność, Dodaj grupę dostępności i bazę danych do grupy dostępności.
> * Utwórz wewnętrzny moduł równoważenia obciążenia platformy Azure dla odbiornika grupy dostępności (opcjonalnie).
> * Wykonaj ręczną lub automatyczną pracę awaryjną.

W tym samouczku zamierzamy skonfigurować klaster DxEnterprise przy użyciu [interfejsu użytkownika klienta DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/). Opcjonalnie można również skonfigurować klaster przy użyciu interfejsu wiersza polecenia [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) . W tym przykładzie użyto czterech maszyn wirtualnych. Trzy z tych maszyn wirtualnych mają uruchomioną Ubuntu 18,04 i są częścią klastra z trzema węzłami. Na czwartej maszynie wirtualnej jest uruchomiony system Windows 10 z narzędziem DxAdmin, aby zarządzać klastrem i go konfigurować.

## <a name="prerequisites"></a>Wymagania wstępne

- Utwórz cztery maszyny wirtualne na platformie Azure. Postępuj zgodnie z [przewodnikiem Szybki Start: Tworzenie maszyny wirtualnej z systemem Linux w Azure Portal](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) artykule, aby utworzyć maszyny wirtualne z systemem Linux. Podobnie w przypadku tworzenia maszyny wirtualnej opartej na systemie Windows postępuj zgodnie z [przewodnikiem Szybki Start: Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) artykule.
- Zainstaluj program .NET 3,1 na wszystkich maszynach wirtualnych opartych na systemie Linux, które będą częścią klastra. Postępuj zgodnie z instrukcjami opisanymi [tutaj](https://docs.microsoft.com/dotnet/core/install/linux) na podstawie wybranego systemu operacyjnego Linux.
- Wymagana jest prawidłowa licencja DxEnterprise z włączonymi funkcjami zarządzania grupami dostępności. Aby uzyskać więcej informacji, zobacz [DxEnterprise bezpłatna wersja próbna](https://dh2i.com/trial/) dotycząca sposobu uzyskania bezpłatnej wersji próbnej.

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>Zainstaluj SQL Server na wszystkich maszynach wirtualnych platformy Azure, które będą częścią grupy dostępności

W tym samouczku skonfigurujemy klaster oparty na systemie Linux należący do grupy dostępności. Postępuj zgodnie z dokumentacją [SQL Server instalacji w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview#install) na podstawie wyboru platformy Linux. Zalecamy również zainstalowanie [narzędzi SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) dla tego samouczka.
 
> [!NOTE]
> Upewnij się, że wybrany system operacyjny Linux jest powszechną dystrybucją obsługiwaną przez oba [DH2i DxEnterprise (patrz minimalna sekcja wymagania systemowe)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) i [Microsoft SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-release-notes-2019#supported-platforms).
>
> W tym przykładzie używamy Ubuntu 18,04, który jest obsługiwany przez DH2i DxEnterprise i Microsoft SQL Server.

W tym samouczku nie będziemy instalować SQL Server na maszynie wirtualnej z systemem Windows, ponieważ ten węzeł nie będzie częścią klastra i służy tylko do zarządzania klastrem za pomocą DxAdmin.

Po wykonaniu tego kroku należy zainstalować narzędzia SQL Server i [SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) (opcjonalnie) na wszystkich trzech maszynach wirtualnych opartych na systemie Linux, które będą uczestniczyć w grupie dostępności.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>Zainstaluj DxEnterprise na wszystkich maszynach wirtualnych i skonfiguruj klaster

W tym kroku będziemy instalować DH2i DxEnterprise dla systemu Linux na trzech maszynach wirtualnych z systemem Linux. W poniższej tabeli opisano rolę, jaką odgrywa każdy serwer w klastrze:

| Liczba maszyn wirtualnych | Rola DxEnterprise DH2i | Rola repliki grupy dostępności Microsoft SQL Server |
|--|--|--|
| 1 | Węzeł klastra — oparte na systemie Linux | Podstawowe |
| 1 | Węzeł klastra — oparte na systemie Linux | Zatwierdzanie synchroniczne |
| 1 | Węzeł klastra — oparte na systemie Linux | Zatwierdzanie synchroniczne |
| 1 | Klient DxAdmin | NA |


Aby zainstalować DxEnterprise w trzech węzłach z systemem Linux, należy postępować zgodnie z dokumentacją DH2i DxEnterprise w oparciu o wybrany system operacyjny Linux. Zainstaluj DxEnterprise przy użyciu jednej z metod wymienionych poniżej.

- **Ubuntu**
    - [Przewodnik Szybki start instalacji repozytorium](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Przewodnik Szybki start rozszerzenia](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Przewodnik Szybki start obrazu witryny Marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [Przewodnik Szybki start instalacji repozytorium](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Przewodnik Szybki start rozszerzenia](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Przewodnik Szybki start obrazu witryny Marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Aby zainstalować tylko narzędzie klienta DxAdmin na maszynie wirtualnej z systemem Windows, należy użyć [interfejsu użytkownika klienta DxAdmin Przewodnik Szybki Start](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/).

Po wykonaniu tego kroku należy utworzyć klaster DxEnterprise na maszynach wirtualnych z systemem Linux, a klient DxAdmin zainstalowany na komputerze klienckim z systemem Windows. 

> [!NOTE]
> Możesz również utworzyć klaster z trzema węzłami, w którym jeden z węzłów zostanie dodany jako *tryb tylko do konfiguracji*, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes) , aby włączyć automatyczne przełączanie do trybu failover. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>Tworzenie hostów wirtualnych w celu zapewnienia obsługi trybu failover i wysokiej dostępności

W tym kroku utworzymy hosta wirtualnego, grupę dostępności, a następnie dodasz bazę danych za pomocą interfejsu użytkownika DxAdmin.   

> [!NOTE]
> W tym kroku wystąpienia SQL Server zostaną uruchomione ponownie, aby włączyć opcję zawsze włączone. 

Połącz się z komputerem klienckim z systemem Windows, na którym działa program DxAdmin, aby połączyć się z klastrem utworzonym w powyższym kroku. Postępuj zgodnie z krokami opisanymi w [grupach dostępności MSSQL z DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) , aby włączyć opcję zawsze włączone i utworzyć hosta wirtualnego i grupę dostępności. 

> [!TIP]
> Przed dodaniem baz danych upewnij się, że baza danych została utworzona i jest wykonywana kopia zapasowa w podstawowym wystąpieniu SQL Server.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>Tworzenie wewnętrznego modułu równoważenia obciążenia platformy Azure dla odbiornika (opcjonalnie)

W tym opcjonalnym kroku można utworzyć i skonfigurować moduł równoważenia obciążenia platformy Azure, który zawiera adresy IP dla odbiorników grupy dostępności. Aby uzyskać więcej informacji na Azure Load Balancer, zapoznaj się z [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Aby skonfigurować odbiornik modułu równoważenia obciążenia i grupy dostępności platformy Azure przy użyciu DxAdmin, postępuj zgodnie z [Przewodnik Szybki start Azure Load Balancer](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/)DxEnterprise.

Po wykonaniu tego kroku należy utworzyć odbiornik grupy dostępności i zmapować go na wewnętrzny moduł równoważenia obciążenia platformy Azure.

## <a name="test-manual-or-automatic-failover"></a>Ręczne lub automatyczne przełączenie w tryb failover

W przypadku testu automatycznego trybu failover można przejść do obszaru i wycofać replikę podstawową (wyłączyć maszynę wirtualną z Azure Portal). Spowoduje to replikację nagłej niedostępności węzła podstawowego. Oczekiwane zachowanie to:
- Menedżer klastra promuje jedną z replik pomocniczych w grupie dostępności do podstawowego.
- Nieudana replika podstawowa automatycznie łączy się z klastrem po utworzeniu kopii zapasowej. Menedżer klastra promuje go do repliki pomocniczej.

 
Możesz również wykonać ręczną pracę awaryjną, wykonując następujące czynności:

1. Nawiązywanie połączenia z klastrem za pośrednictwem DxAdmin   
1. Rozwiń hosta wirtualnego dla grupy dostępności
1. Kliknij prawym przyciskiem myszy węzeł docelowy/replikę pomocniczą i wybierz pozycję **Rozpocznij hosting na elemencie członkowskim** , aby zainicjować tryb failover 

Aby uzyskać więcej informacji na temat większej liczby operacji w programie DxEnterprise, uzyskaj dostęp do [Podręcznika administratora DxEnterprise](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) i [podręcznika DxCLI DxEnterprise](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [grupach dostępności w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-availability-group-overview)
- [Szybki Start: Tworzenie maszyny wirtualnej z systemem Linux w Azure Portal](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)
- [Szybki start: tworzenie maszyny wirtualnej z systemem Windows w witrynie Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)
- [Obsługiwane platformy dla SQL Server 2019 w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)