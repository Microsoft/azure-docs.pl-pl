---
title: SERWERA usługi MAB (Azure Backup Server) v3 UR1 Protection Matrix
description: Ten artykuł zawiera informacje o wszystkich obciążeniach, typach danych i instalacjach, które Azure Backup Server chronić.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: d37d51ee781dfbc5bcd56fa3158a622202a979c6
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754185"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>SERWERA usługi MAB (Azure Backup Server) v3 UR1 Protection Matrix

W tym artykule wymieniono różne serwery i obciążenia, które można chronić za pomocą Azure Backup Server. W poniższej macierzy przedstawiono, co może być chronione za pomocą Azure Backup Server.

Skorzystaj z następującej macierzy dla serwera usługi MAB v3 UR1:

* Obciążenia — typ obciążenia technologii.

* Wersja — obsługiwana wersja serwera usługi MAB dla obciążeń.

* Instalacja serwera usługi MAB — komputer/lokalizacja, w której chcesz zainstalować serwera usługi MAB.

* Ochrona i odzyskiwanie — lista szczegółowych informacji o obciążeniach, takich jak obsługiwane kontenery magazynu lub obsługiwane wdrożenie.

>[!NOTE]
>Obsługa 32-bitowego agenta ochrony jest przestarzała z serwera usługi MAB v3 UR1. Zobacz [32-bitowe przestarzałe Agent ochrony](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Macierz obsługi ochrony

Poniższe sekcje zawierają szczegółowe informacje na temat macierzy obsługi ochrony dla serwera usługi MAB:

* [Tworzenie kopii zapasowej aplikacji](#applications-backup)
* [Kopia zapasowa maszyny wirtualnej](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Tworzenie kopii zapasowej aplikacji

| **Obciążenie**               | **Wersja**                                                  | **Instalacja Azure Backup Server**                       | **Obsługiwane Azure Backup Server** | **Ochrona i odzyskiwanie**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Komputery klienckie (64-bitowe) | System Windows 10                                                  | Serwer fizyczny  <br><br>    Maszyna wirtualna z funkcją Hyper-V   <br><br>   Maszyna wirtualna VMware | UR1 V3                            | Wolumin, udział, folder, pliki, deduplikowane woluminy   <br><br>   Chronione woluminy muszą mieć system plików NTFS. Systemy plików FAT i FAT32 nie są obsługiwane.  <br><br>    Woluminy muszą mieć co najmniej 1 GB. Azure Backup Server używa Usługa kopiowania woluminów w tle (VSS) do tworzenia migawek danych, a migawka działa tylko wtedy, gdy rozmiar woluminu wynosi co najmniej 1 GB. |
| Serwery (64-bitowe)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Maszyna wirtualna platformy Azure (gdy obciążenie działa jako maszyna wirtualna platformy Azure)  <br><br>    Serwer fizyczny  <br><br>    Maszyna wirtualna z funkcją Hyper-V <br><br>     Maszyna wirtualna VMware  <br><br>    Azure Stack | UR1 V3                            | Wolumin, udział, folder, plik <br><br>    Woluminy z deduplikacją (tylko NTFS)  <br><br>   Stan systemu i system bez systemu operacyjnego (nieobsługiwane, gdy obciążenie działa jako maszyna wirtualna platformy Azure) |
| Serwery (64-bitowe)          | Windows Server 2008 R2 z dodatkiem SP1, Windows Server 2008 z dodatkiem SP2 (należy zainstalować [platformę Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)) | Serwer fizyczny  <br><br>    Maszyna wirtualna z funkcją Hyper-V  <br><br>      Maszyna wirtualna VMware  <br><br>   Azure Stack | UR1 V3                            | Wolumin, udział, folder, plik, stan systemu/bez oprogramowania        |
| SQL Server                | SQL Server 2019, 2017, 2016 i [obsługiwane](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016)przez program sps, 2014 i obsługiwane przez niego [SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | Serwer fizyczny  <br><br>     Maszyna wirtualna z funkcją Hyper-V   <br><br>     Maszyna wirtualna VMware  <br><br>   Maszyna wirtualna platformy Azure (gdy obciążenie działa jako maszyna wirtualna platformy Azure)  <br><br>     Azure Stack | UR1 V3                            | Wszystkie scenariusze wdrażania: baza danych       <br><br>  SERWERA usługi MAB v3 UR1 obsługuje tworzenie kopii zapasowych baz danych SQL za pośrednictwem woluminów ReFS                  |
| Exchange                   | Exchange 2019, 2016                                         | Serwer fizyczny   <br><br>   Maszyna wirtualna z funkcją Hyper-V  <br><br>      Maszyna wirtualna VMware  <br><br>   Azure Stack  <br><br>    Maszyna wirtualna platformy Azure (gdy obciążenie działa jako maszyna wirtualna platformy Azure) | UR1 V3                            | Ochrona (wszystkie scenariusze wdrażania): autonomiczny serwer programu Exchange, baza danych w grupie dostępności bazy danych (DAG)  <br><br>    Odzyskiwanie (wszystkie scenariusze wdrażania): skrzynka pocztowa, bazy danych skrzynek pocztowych w grupie DAG    <br><br>  Tworzenie kopii zapasowej programu Exchange za pośrednictwem systemu plików ReFS jest obsługiwane z serwera usługi MAB v3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 z najnowszymi programem SPs                       | Serwer fizyczny  <br><br>    Maszyna wirtualna z funkcją Hyper-V <br><br>    Maszyna wirtualna VMware  <br><br>   Maszyna wirtualna platformy Azure (gdy obciążenie działa jako maszyna wirtualna platformy Azure)   <br><br>   Azure Stack | UR1 V3                            | Ochrona (wszystkie scenariusze wdrażania): Farma, zawartość serwera frontonu sieci Web  <br><br>    Odzyskiwanie (wszystkie scenariusze wdrażania): Farma, baza danych, aplikacja sieci Web, plik lub element listy, wyszukiwanie programu SharePoint, serwer frontonu sieci Web  <br><br>    Ochrona farmy programu SharePoint korzystającej z funkcji AlwaysOn SQL Server 2012 dla baz danych zawartości nie jest obsługiwana. |

## <a name="vm-backup"></a>Kopia zapasowa maszyny wirtualnej

| **Obciążenie**                                                 | **Wersja**                                             | **Instalacja Azure Backup Server**                      | **Obsługiwane Azure Backup Server** | **Ochrona i odzyskiwanie**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Host funkcji Hyper-V — Agent ochrony serwera usługi MAB na serwerze hosta funkcji Hyper-V, klastrze lub maszynie wirtualnej | Windows Server 2019, 2016, 2012 R2, 2012               | Serwer fizyczny  <br><br>    Maszyna wirtualna z funkcją Hyper-V <br><br>    Maszyna wirtualna VMware | UR1 V3                             | Ochrona: komputery funkcji Hyper-V, udostępnione woluminy klastra (CSV)  <br><br>    Odzyskiwanie: maszyna wirtualna, odzyskiwanie plików i folderów na poziomie elementu jest dostępna tylko dla systemu Windows, woluminów i wirtualnych dysków twardych |
| Maszyny wirtualne VMware                                                  | VMware Server 5,5, 6,0 lub 6,5, 6,7 (wersja licencjonowana) | Maszyna wirtualna funkcji Hyper-V  <br><br>   Maszyna wirtualna VMware         | UR1 V3                             | Ochrona: maszyny wirtualne VMware na udostępnionych woluminach klastra (CSV), NFS i SAN   <br><br>     Odzyskiwanie: maszyna wirtualna, odzyskiwanie plików i folderów na poziomie elementu jest dostępna tylko dla systemu Windows, woluminów i wirtualnych dysków twardych <br><br>    Oprogramowania vApps VMware nie są obsługiwane. |

## <a name="linux"></a>Linux

| **Obciążenie** | **Wersja**                               | **Instalacja Azure Backup Server**                      | **Obsługiwane Azure Backup Server** | **Ochrona i odzyskiwanie**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | System Linux uruchomiony jako gość [funkcji Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) lub [VMware](backup-azure-backup-server-vmware.md) | Serwer fizyczny, lokalna maszyna wirtualna funkcji Hyper-V, maszyna wirtualna z systemem Windows w oprogramowaniu VMWare | UR1 V3                             | Funkcja Hyper-V musi być uruchomiona w systemie Windows Server 2012 R2, Windows Server 2016 lub Windows Server 2019. Ochrona: Cała maszyna wirtualna   <br><br>   Odzyskiwanie: cała maszyna wirtualna   <br><br>    Obsługiwane są tylko migawki zgodne z plikami.    <br><br>   Aby zapoznać się z pełną listą obsługiwanych dystrybucji systemu Linux i wersji, zapoznaj się z artykułem [Linux na temat dystrybucji zatwierdzonym przez platformę Azure](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Pomoc techniczna platformy Azure ExpressRoute

Możesz tworzyć kopie zapasowe danych za pośrednictwem usługi Azure ExpressRoute za pomocą publicznej komunikacji równorzędnej (dostępne dla starych obwodów) i komunikacji równorzędnej firmy Microsoft. Tworzenie kopii zapasowej za pośrednictwem prywatnej komunikacji równorzędnej nie jest obsługiwane.

Przy użyciu publicznej komunikacji równorzędnej: Upewnij się, że dostęp do następujących domen/adresów:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

W przypadku komunikacji równorzędnej firmy Microsoft wybierz następujące usługi/regiony i odpowiednie wartości społeczności:

* Azure Active Directory (12076:5060)
* Region Microsoft Azure (zgodnie z lokalizacją magazynu Recovery Services)
* Azure Storage (zgodnie z lokalizacją magazynu Recovery Services)

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące routingu ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>Publiczna Komunikacja równorzędna jest przestarzała dla nowych obwodów.

## <a name="cluster-support"></a>Obsługa klastrów

Azure Backup Server może chronić dane w następujących aplikacjach klastrowanych:

* Serwery plików

* SQL Server

* Funkcja Hyper-V — w przypadku ochrony klastra funkcji Hyper-V przy użyciu skalowalnego w poziomie agenta ochrony serwera usługi MAB nie można dodać dodatkowej ochrony dla chronionych obciążeń funkcji Hyper-V.

* Program Exchange Server — Azure Backup Server może chronić nieudostępniane klastry dysków dla obsługiwanych wersji programu Exchange Server (replikacji ciągłej klastra), a także chronić program Exchange Server skonfigurowany do lokalnej replikacji ciągłej.

* SQL Server-Azure Backup Server nie obsługuje tworzenia kopii zapasowych SQL Server baz danych hostowanych na udostępnionych woluminach klastra (CSV).

Azure Backup Server może chronić obciążenia klastra, które znajdują się w tej samej domenie co serwer serwera usługi MAB i w domenie podrzędnej lub zaufanej. Jeśli chcesz chronić źródła danych w niezaufanych domenach lub grupach roboczych, Użyj uwierzytelniania przy użyciu protokołu NTLM lub certyfikatu dla jednego serwera lub uwierzytelniania certyfikatu tylko dla klastra.
