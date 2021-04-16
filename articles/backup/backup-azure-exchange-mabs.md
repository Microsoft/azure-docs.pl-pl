---
title: Back up Exchange server with Azure Backup Server
description: Dowiedz się, jak wrócić do kopii zapasowej serwera Programu Exchange Azure Backup użyciu Azure Backup Server
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: f3a7fae5a1f5ec933c015546ddf2bdb2898e3904
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515502"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Back up an Exchange server to Azure with Azure Backup Server

W tym artykule opisano sposób konfigurowania Microsoft Azure Backup Server (MABS) w celu kopii zapasowej serwera Microsoft Exchange na platformie Azure.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że program Azure Backup Server zainstalowany [i przygotowany.](backup-azure-microsoft-azure-backup.md)

## <a name="mabs-protection-agent"></a>Agent ochrony mabs

Aby zainstalować agenta ochrony usługi MABS na serwerze Exchange, wykonaj następujące kroki:

1. Upewnij się, że zapory są poprawnie skonfigurowane. Zobacz [Konfigurowanie wyjątków zapory dla agenta](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Zainstaluj agenta na serwerze Programu Exchange, wybierając pozycję Zarządzanie **agentami >,** > zainstalować w programie MABS konsola administratora. Aby [uzyskać szczegółowe instrukcje, zobacz Instalowanie agenta ochrony mabs.](/system-center/dpm/deploy-dpm-protection-agent)

## <a name="create-a-protection-group-for-the-exchange-server"></a>Tworzenie grupy ochrony dla serwera Exchange

1. Na stronie konsola administratora MABS wybierz pozycję **Ochrona,** a następnie wybierz pozycję Nowy na wstążce narzędzi, aby otworzyć **kreatora Tworzenie nowej grupy** ochrony. 
2. Na **ekranie** powitalnym kreatora wybierz pozycję **Dalej.**
3. Na **ekranie Wybierz typ grupy ochrony** wybierz pozycję **Serwery, a** następnie wybierz pozycję **Dalej.**
4. Wybierz bazę danych serwera Exchange, którą chcesz chronić, a następnie wybierz pozycję **Dalej.**

   > [!NOTE]
   > Jeśli chronisz program Exchange 2013, sprawdź wymagania wstępne programu [Exchange 2013.](/system-center/dpm/back-up-exchange)
   >
   >

    W poniższym przykładzie wybrano bazę danych programu Exchange 2010.

    ![Wybieranie członków grupy](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Wybierz metodę ochrony danych.

    Nadaj grupie ochrony nazwę , a następnie wybierz obie następujące opcje:

   * Chcę uzyskać krótkoterminową ochronę przy użyciu dysku.
   * Chcę chronić w trybie online.
6. Wybierz opcję **Dalej**.
7. Wybierz opcję **Uruchom program Eseutil,** aby sprawdzić integralność danych, jeśli chcesz sprawdzić integralność baz danych programu Exchange Server.

    Po wybraniu tej opcji sprawdzanie spójności kopii zapasowej zostanie uruchomione w uchu MABS, aby uniknąć ruchu we/wy generowanego przez uruchomienie polecenia **eseutil** na serwerze programu Exchange.

   > [!NOTE]
   > Aby użyć tej opcji, należy skopiować pliki Ese.dll i Eseutil.exe do katalogu C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin na serwerze USŁUGI MABS. W przeciwnym razie zostanie wyzwolony następujący błąd:  
   > ![Błąd eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Wybierz opcję **Dalej**.
9. Wybierz bazę danych dla opcji **Kopiuj kopię zapasową,** a następnie wybierz pozycję **Dalej.**

   > [!NOTE]
   > Jeśli nie wybierzesz opcji "Pełna kopia zapasowa" dla co najmniej jednej kopii bazy danych daG, dzienniki nie zostaną obcięte.
   >
   >
10. Skonfiguruj cele **krótkoterminowej kopii zapasowej,** a następnie wybierz pozycję **Dalej.**
11. Przejrzyj dostępne miejsce na dysku, a następnie wybierz pozycję **Dalej.**
12. Wybierz czas, o którym serwer MABS utworzy replikację początkową, a następnie wybierz pozycję **Dalej.**
13. Wybierz opcje sprawdzania spójności, a następnie wybierz pozycję **Dalej.**
14. Wybierz bazę danych, której kopię zapasową chcesz wrócić do platformy Azure, a następnie wybierz pozycję **Dalej.** Na przykład:

    ![Określanie danych ochrony online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Zdefiniuj harmonogram **dla Azure Backup**, a następnie wybierz pozycję **Dalej.** Na przykład:

    ![Określanie harmonogramu tworzenia kopii zapasowych online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Uwaga Punkty odzyskiwania online są oparte na ekspresowych pełnych punktach odzyskiwania. W związku z tym należy zaplanować punkt odzyskiwania online po upływie czasu określonego dla ekspresowego pełnego punktu odzyskiwania.
    >
    >
16. Skonfiguruj zasady przechowywania **dla** Azure Backup , a następnie wybierz pozycję **Dalej.**
17. Wybierz opcję replikacji online i wybierz pozycję **Dalej.**

    Jeśli masz dużą bazę danych, tworzenie początkowej kopii zapasowej za pośrednictwem sieci może zająć dużo czasu. Aby uniknąć tego problemu, możesz utworzyć kopię zapasową w trybie offline.  

    ![Określanie zasad przechowywania w trybie online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Potwierdź ustawienia, a następnie wybierz **pozycję Utwórz grupę.**
19. Wybierz pozycję **Zamknij**.

## <a name="recover-the-exchange-database"></a>Odzyskiwanie bazy danych programu Exchange

1. Aby odzyskać bazę danych programu Exchange, wybierz **pozycję Odzyskiwanie** w awarii konsola administratora MABS.
2. Znajdź bazę danych programu Exchange, którą chcesz odzyskać.
3. Wybierz punkt odzyskiwania online z *listy rozwijanej* Czas odzyskiwania.
4. Wybierz **pozycję Odzyskaj,** aby uruchomić Kreatora **odzyskiwania.**

W przypadku punktów odzyskiwania online istnieje pięć typów odzyskiwania:

* **Odzyskiwanie do oryginalnej lokalizacji programu Exchange Server:** Dane zostaną odzyskane na oryginalny serwer Programu Exchange.
* **Odzyskiwanie do innej bazy danych na serwerze Exchange:** Dane zostaną odzyskane do innej bazy danych na innym serwerze Exchange.
* **Odzyskiwanie do bazy danych odzyskiwania:** Dane zostaną odzyskane do bazy danych odzyskiwania programu Exchange (RDB).
* **Skopiuj do folderu sieciowego:** Dane zostaną odzyskane do folderu sieciowego.
* **Kopiuj na taśmę:** Jeśli masz bibliotekę taśm lub autonomiczną taśmę podłączona i skonfigurowana w uchwale MABS, punkt odzyskiwania zostanie skopiowany na taśmę bezpłatną.

    ![Wybieranie replikacji online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Następne kroki

* [Azure Backup — często zadawane pytania](backup-azure-backup-faq.yml)
