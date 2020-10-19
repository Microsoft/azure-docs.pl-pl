---
title: Azure Backup Server i program DPM — często zadawane pytania
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące serwera Microsoft Azure Backup (serwera usługi MAB) i programu DPM (Data Protection Manager).
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: cd974181c78586f915dad6cc6ed39d9255a1456b
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172582"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server i DPM — często zadawane pytania

## <a name="general-questions"></a>Pytania ogólne

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Backup Server i programu DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Czy można używać programu Azure Backup Server do tworzenia kopii zapasowej z odzyskiwaniem systemu od zera (BMR) serwera fizycznego?

Tak.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Czy można zarejestrować serwer w wielu magazynach?

Nie. Serwer programu DPM lub Azure Backup może być zarejestrowany tylko w jednym magazynie.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Czy mogę użyć programu DPM do tworzenia kopii zapasowych aplikacji w Azure Stack?

Nie. Azure Backup do ochrony Azure Stack, Azure Backup nie obsługuje tworzenia kopii zapasowych Azure Stack aplikacji przy użyciu programu DPM.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Czy jeśli zainstalowano Azure Backup agenta do ochrony moich plików i folderów, można zainstalować program System Center DPM w celu tworzenia kopii zapasowych obciążeń lokalnych na platformie Azure?

Tak. Należy jednak najpierw skonfigurować program DPM, a następnie zainstalować agenta Azure Backup.  Zainstalowanie składników w tej kolejności zapewnia, że Agent Azure Backup współpracuje z programem DPM. Zainstalowanie agenta przed zainstalowaniem programu DPM nie jest zalecane lub nie jest obsługiwane.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Dlaczego nie mogę dodać zewnętrznego serwera programu DPM po zainstalowaniu pakietem UR7 i najnowszego agenta Azure Backup?

W przypadku serwerów programu DPM ze źródłami danych, które są chronione w chmurze (przy użyciu pakietu zbiorczego aktualizacji wcześniejszego niż pakiet zbiorczy aktualizacji 7), należy odczekać co najmniej jeden dzień po zainstalowaniu programu pakietem UR7 i najnowszego agenta Azure Backup, aby rozpocząć **Dodawanie zewnętrznego serwera DPM**. Do przekazania metadanych grup ochrony programu DPM na platformę Azure jest wymagany okres jednodniowy. Metadane grupy ochrony są przekazywane po raz pierwszy przez nocne zadanie.

### <a name="are-there-recommendations-for-configuring-exclusions-for-antivirus-software"></a>Czy istnieją zalecenia dotyczące konfigurowania wykluczeń dla oprogramowania antywirusowego?

Tak, zaleca się skonfigurowanie wykluczenia oprogramowania antywirusowego. Wyjątki dla programu DPM można znaleźć [w artykule uruchamianie oprogramowania antywirusowego na serwerze DPM](/system-center/dpm/run-antivirus-server). Wyjątki dla serwera usługi MAB można znaleźć w temacie [Konfigurowanie oprogramowania antywirusowego serwera serwera usługi MAB](backup-azure-mabs-troubleshoot.md#configure-antivirus-for-mabs-server).

## <a name="vmware-and-hyper-v-backup"></a>Kopia zapasowa oprogramowania VMware i funkcji Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Czy można tworzyć kopie zapasowe serwerów VMware vCenter na platformie Azure?

Tak. Za pomocą Azure Backup Server można tworzyć kopie zapasowe hostów VMware vCenter Server i ESXi na platformie Azure.

- [Dowiedz się więcej](backup-mabs-protection-matrix.md) o obsługiwanych wersjach.
- [Wykonaj następujące kroki](backup-azure-backup-server-vmware.md) , aby utworzyć kopię zapasową serwera VMware.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Czy muszę mieć oddzielną licencję na odzyskanie pełnego lokalnego klastra VMware/Hyper-V?

Nie potrzebujesz oddzielnej licencji na oprogramowanie VMware/funkcja Hyper-V.

- Jeśli jesteś klientem programu System Center, użyj programu System Center Data Protection Manager (DPM) do ochrony maszyn wirtualnych VMware.
- Jeśli nie jesteś klientem programu System Center, możesz użyć Azure Backup Server (płatność zgodnie z rzeczywistym użyciem), aby chronić maszyny wirtualne VMware.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Czy można odzyskać element programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu funkcji SQL AlwaysOn (z ochroną na dysku)?

Tak, element można odzyskać do oryginalnej witryny programu SharePoint.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Czy można odzyskać bazę danych programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu funkcji SQL AlwaysOn?

Ponieważ bazy danych programu SharePoint są skonfigurowane w funkcji SQL AlwaysOn, nie można ich modyfikować, chyba że grupa dostępności zostanie usunięta. W związku z tym program DPM nie może przywrócić bazy danych do oryginalnej lokalizacji. Bazę danych SQL Server można odzyskać do innego wystąpienia SQL Server.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi często zadawanymi pytaniami:

- [Dowiedz się więcej](backup-support-matrix-mabs-dpm.md) na temat macierzy obsługi Azure Backup Server i programu DPM.
- [Dowiedz się więcej](backup-azure-mabs-troubleshoot.md) na temat wytycznych dotyczących rozwiązywania problemów dotyczących Azure Backup Server i programu DPM.