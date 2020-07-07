---
title: Tworzenie kopii zapasowej programu Exchange Server z Azure Backup Server
description: Dowiedz się, jak utworzyć kopię zapasową serwera programu Exchange w celu Azure Backup przy użyciu Azure Backup Server
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 1d7d28d813df82a5e1ea0fe424bba2ef5a9a2684
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80421346"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Tworzenie kopii zapasowej serwera programu Exchange na platformie Azure za pomocą Azure Backup Server

W tym artykule opisano sposób konfigurowania serwera Microsoft Azure Backup (serwera usługi MAB) w celu utworzenia kopii zapasowej programu Microsoft Exchange Server na platformie Azure.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że Azure Backup Server jest [zainstalowana i przygotowana](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agent ochrony serwera usługi MAB

Aby zainstalować agenta ochrony serwera usługi MAB na serwerze Exchange, wykonaj następujące kroki:

1. Upewnij się, że zapory są prawidłowo skonfigurowane. Zobacz [Configure firewall Exceptions for the Agent](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Zainstaluj agenta na serwerze Exchange, klikając pozycję **zarządzanie > agenci > Zainstaluj** w serwera usługi MAB Konsola administratora. Szczegółowe instrukcje można znaleźć w temacie [Install the serwera usługi MAB Protection Agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) .

## <a name="create-a-protection-group-for-the-exchange-server"></a>Tworzenie grupy ochrony dla serwera programu Exchange

1. W konsola administratora serwera usługi MAB kliknij pozycję **Ochrona**, a następnie kliknij przycisk **Nowy** na Wstążce narzędzi, aby otworzyć kreatora **tworzenia nowej grupy ochrony** .
2. Na ekranie **powitalnym** kreatora kliknij przycisk **dalej**.
3. Na ekranie **Wybierz typ grupy ochrony** wybierz pozycję **serwery** , a następnie kliknij przycisk **dalej**.
4. Wybierz bazę danych programu Exchange Server, którą chcesz chronić, a następnie kliknij przycisk **dalej**.

   > [!NOTE]
   > W przypadku ochrony programu Exchange 2013 Sprawdź [wymagania wstępne programu exchange 2013](https://docs.microsoft.com/system-center/dpm/back-up-exchange?view=sc-dpm-2016).
   >
   >

    W poniższym przykładzie wybrano bazę danych programu Exchange 2010.

    ![Wybierz członków grupy](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Wybierz metodę ochrony danych.

    Nadaj nazwę grupie ochrony, a następnie wybierz obie następujące opcje:

   * Chcę uzyskać krótkoterminową ochronę za pomocą dysku.
   * Chcę chronić w trybie online.
6. Kliknij przycisk **Dalej**.
7. Wybierz opcję **Uruchom program Eseutil, aby sprawdzić integralność danych** , jeśli chcesz sprawdzić integralność baz danych programu Exchange Server.

    Po wybraniu tej opcji sprawdzanie spójności kopii zapasowej zostanie uruchomione na serwera usługi MAB, aby uniknąć ruchu we/wy wygenerowanego przez uruchomienie polecenia **eseutil** na serwerze Exchange.

   > [!NOTE]
   > Aby użyć tej opcji, należy skopiować pliki Ese.dll i Eseutil.exe do katalogu C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin na serwerze serwera usługi MAB. W przeciwnym razie zostanie wyzwolony następujący błąd:  
   > ![błąd Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kliknij przycisk **Dalej**.
9. Wybierz bazę danych **kopii zapasowej**, a następnie kliknij przycisk **dalej**.

   > [!NOTE]
   > Jeśli nie wybierzesz opcji "pełna kopia zapasowa" dla co najmniej jednej kopii DAG bazy danych, dzienniki nie zostaną obcięte.
   >
   >
10. Skonfiguruj cele **krótkoterminowej kopii zapasowej**, a następnie kliknij przycisk **dalej**.
11. Sprawdź ilość dostępnego miejsca na dysku, a następnie kliknij przycisk **dalej**.
12. Wybierz czas, w którym serwer serwera usługi MAB utworzy replikację początkową, a następnie kliknij przycisk **dalej**.
13. Wybierz opcje sprawdzania spójności, a następnie kliknij przycisk **dalej**.
14. Wybierz bazę danych, dla której chcesz utworzyć kopię zapasową na platformie Azure, a następnie kliknij przycisk **dalej**. Na przykład:

    ![Określ dane ochrony w trybie online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Zdefiniuj harmonogram **Azure Backup**, a następnie kliknij przycisk **dalej**. Na przykład:

    ![Określ harmonogram kopii zapasowych online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Uwaga punkty odzyskiwania online są oparte na ekspresowych pełnych punktach odzyskiwania. W związku z tym należy zaplanować punkt odzyskiwania online po upływie czasu określonego dla ekspresowego pełnego odzyskiwania.
    >
    >
16. Skonfiguruj zasady przechowywania dla **Azure Backup**, a następnie kliknij przycisk **dalej**.
17. Wybierz opcję replikacja online, a następnie kliknij przycisk **dalej**.

    W przypadku dużej bazy danych utworzenie początkowej kopii zapasowej za pośrednictwem sieci może zająć dużo czasu. Aby uniknąć tego problemu, można utworzyć kopię zapasową offline.  

    ![Określ zasady przechowywania danych online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Potwierdź ustawienia, a następnie kliknij przycisk **Utwórz grupę**.
19. Kliknij przycisk **Zamknij**.

## <a name="recover-the-exchange-database"></a>Odzyskiwanie bazy danych programu Exchange

1. Aby odzyskać bazę danych programu Exchange, kliknij pozycję **odzyskiwanie** w Konsola administratora serwera usługi MAB.
2. Znajdź bazę danych programu Exchange, którą chcesz odzyskać.
3. Z listy rozwijanej *godzina odzyskiwania* wybierz punkt odzyskiwania online.
4. Kliknij przycisk **Odzyskaj** , aby uruchomić **Kreatora odzyskiwania**.

W przypadku punktów odzyskiwania online istnieje pięć typów odzyskiwania:

* **Odzyskaj do oryginalnej lokalizacji serwera Exchange:** Dane zostaną odzyskane do oryginalnego serwera programu Exchange.
* **Odzyskaj do innej bazy danych na serwerze programu Exchange:** Dane zostaną odzyskane do innej bazy danych na innym serwerze Exchange.
* **Odzyskaj do bazy danych odzyskiwania:** Dane zostaną odzyskane do bazy danych odzyskiwania programu Exchange (RDB).
* **Kopiuj do folderu sieciowego:** Dane zostaną odzyskane do folderu sieciowego.
* **Kopiuj na taśmę:** Jeśli masz bibliotekę taśm lub autonomiczną stację taśm dołączoną i skonfigurowaną w systemie serwera usługi MAB, punkt odzyskiwania zostanie skopiowany na wolne taśmy.

    ![Wybierz replikację online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Następne kroki

* [Azure Backup często zadawane pytania](backup-azure-backup-faq.md)
