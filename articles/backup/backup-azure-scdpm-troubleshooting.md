---
title: Rozwiązywanie problemów z programem System Center Data Protection Manager
description: W tym artykule Odkryj rozwiązania problemów, które mogą wystąpić podczas korzystania z programu System Center Data Protection Manager.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: 84646f34d905b570855f655465529eb19b717cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86513816"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Rozwiązywanie problemów z programem System Center Data Protection Manager

W tym artykule opisano rozwiązania problemów, które mogą wystąpić podczas korzystania z Data Protection Manager.

Najnowsze informacje o wersji programu System Center Data Protection Manager można znaleźć w [dokumentacji programu System Center](/system-center/dpm/dpm-release-notes). Więcej informacji na temat obsługi Data Protection Manager można znaleźć w [tej macierzy](/system-center/dpm/dpm-protection-matrix).

## <a name="error-replica-is-inconsistent"></a>Błąd: replika jest niespójna

Replika może być niespójna z następujących powodów:

- Zadanie tworzenia repliki zakończy się niepowodzeniem.
- Występują problemy z dziennikiem zmian.
- Mapa bitowa filtru na poziomie woluminu zawiera błędy.
- Maszyna źródłowa zostanie nieoczekiwanie zamknięta.
- Przepełnienie dziennika synchronizacji.
- Replika jest naprawdę niespójna.

Aby rozwiązać ten problem, wykonaj następujące czynności:

- Aby usunąć stan niespójności, Uruchom sprawdzanie spójności ręcznie lub Zaplanuj codzienne sprawdzanie spójności.
- Upewnij się, że korzystasz z najnowszej wersji programu Microsoft Azure Backup Server i Data Protection Manager.
- Upewnij się, że ustawienie **spójności automatycznej** jest włączone.
- Spróbuj ponownie uruchomić usługi przy użyciu wiersza polecenia. Użyj `net stop dpmra` polecenia po `net start dpmra` .
- Upewnij się, że spełniasz wymagania dotyczące łączności sieciowej i przepustowości.
- Sprawdź, czy maszyna źródłowa została nieoczekiwanie zamknięta.
- Upewnij się, że dysk jest w dobrej kondycji i że jest wystarczająca ilość miejsca na replikę.
- Upewnij się, że nie ma żadnych zduplikowanych zadań kopii zapasowych uruchomionych współbieżnie.

## <a name="error-online-recovery-point-creation-failed"></a>Błąd: Tworzenie punktu odzyskiwania w trybie online nie powiodło się

Aby rozwiązać ten problem, wykonaj następujące czynności:

- Upewnij się, że korzystasz z najnowszej wersji agenta Azure Backup.
- Spróbuj ręcznie utworzyć punkt odzyskiwania w obszarze zadań ochrona.
- Upewnij się, że w źródle danych jest uruchamiane sprawdzanie spójności.
- Upewnij się, że spełniasz wymagania dotyczące łączności sieciowej i przepustowości.
- Gdy dane repliki są w stanie niespójnym, Utwórz punkt odzyskiwania dysku tego źródła danych.
- Upewnij się, że replika jest obecna i nie istnieje.
- Upewnij się, że replika ma wystarczającą ilość miejsca, aby utworzyć dziennik numeru sekwencji aktualizacji (USN).

## <a name="error-unable-to-configure-protection"></a>Błąd: nie można skonfigurować ochrony

Ten błąd występuje, gdy serwer Data Protection Manager nie może skontaktować się z serwerem chronionym.

Aby rozwiązać ten problem, wykonaj następujące czynności:

- Upewnij się, że korzystasz z najnowszej wersji agenta Azure Backup.
- Upewnij się, że istnieje połączenie (sieć/Zapora/serwer proxy) między serwerem Data Protection Manager a serwerem chronionym.
- W przypadku ochrony programu SQL Server należy się upewnić, że właściwość **logowania właściwości** grupy  >  **NT** wykaże włączenie ustawienia **sysadmin** .

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Błąd: serwer nie jest zarejestrowany jako określony w pliku poświadczeń magazynu

Ten błąd występuje podczas procesu odzyskiwania danych serwera Data Protection Manager/Azure Backup. Plik poświadczeń magazynu używany w procesie odzyskiwania nie należy do magazynu Recovery Services dla serwera Data Protection Manager/Azure Backup.

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Pobierz plik poświadczeń magazynu z magazynu Recovery Services, do którego zarejestrowano serwer Data Protection Manager/Azure Backup.
2. Spróbuj zarejestrować serwer w magazynie przy użyciu ostatnio pobranego pliku poświadczeń magazynu.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Błąd: Brak danych odzyskiwalnych lub wybranego serwera, który nie jest serwerem Data Protection Manager

Ten błąd występuje z następujących powodów:

- W magazynie Recovery Services nie są zarejestrowane żadne inne serwery Data Protection Manager/Azure Backup.
- Serwery nie przesłały jeszcze metadanych.
- Wybrany serwer nie jest serwerem Data Protection Manager/Azure Backup.

Jeśli do magazynu Recovery Services są zarejestrowane inne serwery Data Protection Manager/Azure Backup, wykonaj następujące kroki, aby rozwiązać ten problem:

1. Upewnij się, że jest zainstalowany najnowszy Agent Azure Backup.
2. Po upewnieniu się, że jest zainstalowany najnowszy Agent, odczekaj jeden dzień przed rozpoczęciem procesu odzyskiwania. Zadanie tworzenia kopii zapasowej w porze nocnej przekazuje metadane dla wszystkich chronionych kopii zapasowych do chmury. Dane kopii zapasowej są następnie dostępne do odzyskania.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Błąd: podane hasło szyfrowania nie jest zgodne z hasłem dla serwera

Ten błąd występuje podczas procesu szyfrowania podczas odzyskiwania danych serwera Data Protection Manager/Azure Backup. Hasło szyfrowania używane w procesie odzyskiwania nie jest zgodne z hasłem szyfrowania serwera. W związku z tym Agent nie może odszyfrować danych, a odzyskiwanie nie powiedzie się.

> [!IMPORTANT]
> Jeśli zapomnisz lub utracisz hasło szyfrowania, nie ma innych metod odzyskiwania danych. Jedyną opcją jest ponowne wygenerowanie hasła. Użyj nowego hasła, aby zaszyfrować przyszłe dane kopii zapasowej.
>
> Podczas odzyskiwania danych należy zawsze podać to samo hasło szyfrowania, które jest skojarzone z serwerem Data Protection Manager/Azure Backup.
>
