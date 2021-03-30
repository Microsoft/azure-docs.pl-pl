---
title: Tworzenie kopii zapasowej serwera programu Exchange przy użyciu programu System Center DPM
description: Dowiedz się, jak utworzyć kopię zapasową serwera programu Exchange w celu Azure Backup przy użyciu programu System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: ee89af311619922fa6ca585381d70ca66955f36a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91271651"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Tworzenie kopii zapasowych serwera programu Exchange do usługi Azure Backup przy użyciu programu System Center 2012 R2 DPM

W tym artykule opisano sposób konfigurowania serwera programu System Center 2012 R2 Data Protection Manager (DPM) do tworzenia kopii zapasowej serwera programu Microsoft Exchange w celu Azure Backup.  

## <a name="updates"></a>Aktualizacje

Aby pomyślnie zarejestrować serwer programu DPM przy użyciu Azure Backup, należy zainstalować najnowszy pakiet zbiorczy aktualizacji dla programu System Center 2012 R2 DPM i najnowszą wersję agenta Azure Backup. Pobierz najnowszy pakiet zbiorczy aktualizacji z [wykazu Microsoft](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> W przykładach w tym artykule jest zainstalowana wersja 2.0.8719.0 agenta Azure Backup i pakiet zbiorczy aktualizacji 6 został zainstalowany w programie System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że spełniono wszystkie [wymagania wstępne](backup-azure-dpm-introduction.md#prerequisites-and-limitations) dotyczące używania Microsoft Azure Backup ochrony obciążeń. Wymagania wstępne obejmują następujące elementy:

* Utworzono magazyn kopii zapasowych w witrynie platformy Azure.
* Poświadczenia agenta i magazynu zostały pobrane na serwer programu DPM.
* Agent jest zainstalowany na serwerze programu DPM.
* Poświadczenia magazynu zostały użyte do zarejestrowania serwera programu DPM.
* W przypadku ochrony programu Exchange 2016 należy przeprowadzić uaktualnienie do programu DPM 2012 R2 UR9 lub nowszego.

## <a name="dpm-protection-agent"></a>Agent ochrony DPM

Aby zainstalować agenta ochrony programu DPM na serwerze Exchange, wykonaj następujące kroki:

1. Upewnij się, że zapory są prawidłowo skonfigurowane. Zobacz [Configure firewall Exceptions for the Agent](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Zainstaluj agenta na serwerze programu Exchange, wybierając pozycję **zarządzanie > agenci > Zainstaluj** w Konsola administratora programu DPM. Szczegółowe instrukcje znajdują się w temacie [Install the DPM Protection Agent](/system-center/dpm/deploy-dpm-protection-agent) .

## <a name="create-a-protection-group-for-the-exchange-server"></a>Tworzenie grupy ochrony dla serwera programu Exchange

1. W Konsola administratora programu DPM wybierz pozycję **Ochrona**, a następnie wybierz pozycję **Nowy** na Wstążce narzędzi, aby otworzyć kreatora **tworzenia nowej grupy ochrony** .
2. Na ekranie **powitalnym** kreatora wybierz pozycję **dalej**.
3. Na ekranie **Wybierz typ grupy ochrony** wybierz pozycję **serwery** , a następnie kliknij przycisk **dalej**.
4. Wybierz bazę danych programu Exchange Server, którą chcesz chronić, a następnie wybierz pozycję **dalej**.

   > [!NOTE]
   > Jeśli chronisz program Exchange 2013, sprawdź [wymagania wstępne programu exchange 2013](/system-center/dpm/back-up-exchange).
   >
   >

    W poniższym przykładzie wybrano bazę danych programu Exchange 2010.

    ![Wybierz członków grupy](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Wybierz metodę ochrony danych.

    Nadaj nazwę grupie ochrony, a następnie wybierz obie następujące opcje:

   * Chcę uzyskać krótkoterminową ochronę za pomocą dysku.
   * Chcę chronić w trybie online.
6. Wybierz opcję **Dalej**.
7. Wybierz opcję **Uruchom program Eseutil, aby sprawdzić integralność danych** , jeśli chcesz sprawdzić integralność baz danych programu Exchange Server.

    Po wybraniu tej opcji sprawdzanie spójności kopii zapasowej zostanie uruchomione na serwerze programu DPM, aby uniknąć ruchu we/wy wygenerowanego przez uruchomienie polecenia **eseutil** na serwerze Exchange.

   > [!NOTE]
   > Aby użyć tej opcji, należy skopiować pliki Ese.dll i Eseutil.exe do katalogu C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin na serwerze DPM. W przeciwnym razie zostanie wyzwolony następujący błąd:  
   > ![błąd Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Wybierz opcję **Dalej**.
9. Wybierz bazę danych **kopii zapasowej**, a następnie wybierz przycisk **dalej**.

   > [!NOTE]
   > Jeśli nie wybrano opcji "pełna kopia zapasowa" dla co najmniej jednej kopii DAG bazy danych, dzienniki nie będą obcinane.
   >
   >
10. Skonfiguruj cele **krótkoterminowej kopii zapasowej**, a następnie wybierz przycisk **dalej**.
11. Sprawdź ilość dostępnego miejsca na dysku, a następnie wybierz przycisk **dalej**.
12. Wybierz czas, w którym serwer programu DPM utworzy replikację początkową, a następnie wybierz przycisk **dalej**.
13. Wybierz opcje sprawdzania spójności, a następnie wybierz przycisk **dalej**.
14. Wybierz bazę danych, dla której chcesz utworzyć kopię zapasową na platformie Azure, a następnie wybierz pozycję **dalej**. Na przykład:

    ![Określ dane ochrony w trybie online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Zdefiniuj harmonogram **Azure Backup** a następnie wybierz przycisk **dalej**. Na przykład:

    ![Określ harmonogram kopii zapasowych online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Uwaga punkty odzyskiwania online są oparte na ekspresowych pełnych punktach odzyskiwania. W związku z tym należy zaplanować punkt odzyskiwania online po upływie czasu określonego dla ekspresowego pełnego odzyskiwania.
    >
    >
16. Skonfiguruj zasady przechowywania dla **Azure Backup**, a następnie wybierz przycisk **dalej**.
17. Wybierz opcję replikacja online i wybierz pozycję **dalej**.

    W przypadku dużej bazy danych utworzenie początkowej kopii zapasowej za pośrednictwem sieci może zająć dużo czasu. Aby uniknąć tego problemu, można utworzyć kopię zapasową offline.  

    ![Określ zasady przechowywania danych online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Potwierdź ustawienia, a następnie wybierz pozycję **Utwórz grupę**.
19. Wybierz pozycję **Zamknij**.

## <a name="recover-the-exchange-database"></a>Odzyskiwanie bazy danych programu Exchange

1. Aby odzyskać bazę danych programu Exchange, wybierz pozycję **odzyskiwanie** w Konsola administratora programu DPM.
2. Znajdź bazę danych programu Exchange, którą chcesz odzyskać.
3. Z listy rozwijanej *godzina odzyskiwania* wybierz punkt odzyskiwania online.
4. Wybierz pozycję **Odzyskaj** , aby uruchomić **Kreatora odzyskiwania**.

W przypadku punktów odzyskiwania online istnieje pięć typów odzyskiwania:

* **Odzyskaj do oryginalnej lokalizacji serwera Exchange:** Dane zostaną odzyskane do oryginalnego serwera programu Exchange.
* **Odzyskaj do innej bazy danych na serwerze programu Exchange:** Dane zostaną odzyskane do innej bazy danych na innym serwerze Exchange.
* **Odzyskaj do bazy danych odzyskiwania:** Dane zostaną odzyskane do bazy danych odzyskiwania programu Exchange (RDB).
* **Kopiuj do folderu sieciowego:** Dane zostaną odzyskane do folderu sieciowego.
* **Kopiuj na taśmę:** Jeśli masz bibliotekę taśm lub autonomiczną stację taśm podłączoną i skonfigurowaną na serwerze programu DPM, punkt odzyskiwania zostanie skopiowany na wolne taśmy.

    ![Wybierz replikację online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Następne kroki

* [Azure Backup często zadawane pytania](backup-azure-backup-faq.md)
