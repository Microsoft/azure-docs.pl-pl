---
title: Back up an Exchange server via System Center DPM
description: Dowiedz się, jak w programie DPM w programie Azure Backup 2012 R2 System Center 2012 R2
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: a89c37b8447b318c44faf0d4e0b1921d305e7f59
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519395"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Tworzenie kopii zapasowych serwera programu Exchange do usługi Azure Backup przy użyciu programu System Center 2012 R2 DPM

W tym artykule opisano sposób konfigurowania serwera programu System Center 2012 R2 Data Protection Manager (DPM) w celu skonfigurowania kopii zapasowej serwera Microsoft Exchange na Azure Backup.  

## <a name="updates"></a>Aktualizacje

Aby pomyślnie zarejestrować serwer DPM w programie Azure Backup, należy zainstalować najnowszy pakiet zbiorczy aktualizacji dla programu DPM w wersji System Center 2012 R2 i najnowszą wersję programu Azure Backup Agent. Pobierz najnowszy pakiet zbiorczy aktualizacji z katalogu [firmy Microsoft.](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)

> [!NOTE]
> Na przykładach w tym artykule zainstalowano wersję 2.0.8719.0 agenta programu Azure Backup, a w programie DPM w wersji 2012 R2 zainstalowano zbiorczy System Center update rollup 6.
>
>

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, [](backup-azure-dpm-introduction.md#prerequisites-and-limitations) że zostały spełnione wszystkie wymagania wstępne Microsoft Azure Backup do ochrony obciążeń. Wymagania wstępne są następujące:

* Magazyn kopii zapasowych w witrynie platformy Azure został utworzony.
* Poświadczenia agenta i magazynu zostały pobrane na serwer programu DPM.
* Agent jest zainstalowany na serwerze programu DPM.
* Poświadczenia magazynu zostały użyte do zarejestrowania serwera DPM.
* Jeśli chronisz program Exchange 2016, uaktualnij program do wersji DPM 2012 R2 UR9 lub nowszej.

## <a name="dpm-protection-agent"></a>Agent ochrony programu DPM

Aby zainstalować agenta ochrony programu DPM na serwerze Exchange, wykonaj następujące kroki:

1. Upewnij się, że zapory są prawidłowo skonfigurowane. Zobacz [Konfigurowanie wyjątków zapory dla agenta](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Zainstaluj agenta na serwerze Exchange, wybierając pozycję Zarządzanie **> agentów > zainstalować w** programie Konsola administratora programu DPM. Aby [uzyskać szczegółowe instrukcje,](/system-center/dpm/deploy-dpm-protection-agent) zobacz Instalowanie agenta ochrony programu DPM.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Tworzenie grupy ochrony dla serwera Exchange

1. W Konsola administratora programu DPM wybierz pozycję **Ochrona,** a następnie wybierz pozycję **Nowy** na wstążce narzędzi, aby otworzyć **kreatora Tworzenie nowej grupy** ochrony.
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

    Nadaj grupie ochrony nazwę, a następnie wybierz obie następujące opcje:

   * Chcę uzyskać krótkoterminową ochronę przy użyciu dysku.
   * Chcę chronić w trybie online.
6. Wybierz opcję **Dalej**.
7. Wybierz opcję **Uruchom program Eseutil, aby** sprawdzić integralność danych, jeśli chcesz sprawdzić integralność baz danych programu Exchange Server.

    Po wybraniu tej opcji na serwerze DPM zostanie uruchomione sprawdzanie spójności kopii zapasowej, aby uniknąć ruchu we/wy generowanego przez uruchomienie polecenia **eseutil** na serwerze exchange.

   > [!NOTE]
   > Aby użyć tej opcji, należy skopiować pliki Ese.dll i Eseutil.exe do katalogu C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin na serwerze DPM. W przeciwnym razie zostanie wyzwolony następujący błąd:  
   > ![Błąd eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Wybierz opcję **Dalej**.
9. Wybierz bazę danych dla opcji **Kopiuj kopię zapasową,** a następnie wybierz pozycję **Dalej.**

   > [!NOTE]
   > Jeśli nie wybierzesz opcji "Pełna kopia zapasowa" dla co najmniej jednej kopii daG bazy danych, dzienniki nie zostaną obcięte.
   >
   >
10. Skonfiguruj cele **krótkoterminowej kopii zapasowej,** a następnie wybierz pozycję **Dalej.**
11. Przejrzyj dostępne miejsce na dysku, a następnie wybierz pozycję **Dalej.**
12. Wybierz czas, o którym serwer programu DPM utworzy replikację początkową, a następnie wybierz pozycję **Dalej.**
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

1. Aby odzyskać bazę danych programu Exchange, wybierz **pozycję Odzyskiwanie** w Konsola administratora programu DPM.
2. Znajdź bazę danych programu Exchange, którą chcesz odzyskać.
3. Wybierz punkt odzyskiwania online z *listy rozwijanej* Czas odzyskiwania.
4. Wybierz **pozycję Odzyskaj,** aby uruchomić Kreatora **odzyskiwania.**

W przypadku punktów odzyskiwania online istnieje pięć typów odzyskiwania:

* **Odzyskiwanie do oryginalnej lokalizacji programu Exchange Server:** Dane zostaną odzyskane na oryginalny serwer Programu Exchange.
* **Odzyskiwanie do innej bazy danych na serwerze Exchange:** Dane zostaną odzyskane do innej bazy danych na innym serwerze Exchange.
* **Odzyskiwanie do bazy danych odzyskiwania:** Dane zostaną odzyskane do bazy danych odzyskiwania programu Exchange (RDB).
* **Skopiuj do folderu sieciowego:** Dane zostaną odzyskane do folderu sieciowego.
* **Kopiuj na taśmę:** Jeśli masz bibliotekę taśm lub autonomiczną taśmę podłączona i skonfigurowana na serwerze DPM, punkt odzyskiwania zostanie skopiowany na taśmę bezpłatną.

    ![Wybieranie replikacji online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Następne kroki

* [Azure Backup — często zadawane pytania](backup-azure-backup-faq.yml)
