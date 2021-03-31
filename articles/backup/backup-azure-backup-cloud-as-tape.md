---
title: Jak zastąpić infrastrukturę taśm
description: Dowiedz się, jak Azure Backup zapewnia semantykę podobną do taśmy, która umożliwia tworzenie kopii zapasowych i przywracanie danych na platformie Azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: 695cc2644521384527ecd871f3613a078e987aa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89378444"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Przenoszenie długoterminowego magazynu z taśmy do chmury platformy Azure

Klienci Azure Backup i System Center Data Protection Manager mogą:

* Utwórz kopię zapasową danych w harmonogramach, które najlepiej odpowiadają potrzebom organizacji.
* Przechowuj dane kopii zapasowej przez dłuższe okresy.
* Ustaw platformę Azure jako część długoterminowych potrzeb przechowywania (zamiast taśm).

W tym artykule wyjaśniono, jak klienci mogą włączyć zasady tworzenia kopii zapasowych i przechowywania danych. Klienci, którzy wykorzystują taśmy do rozwiązywania długoterminowych potrzeb związanych z przechowywaniem, mają teraz zaawansowaną i wydajną alternatywę w zakresie dostępności tej funkcji. Ta funkcja jest włączona w najnowszej wersji Azure Backup (która jest dostępna w [tym miejscu](https://aka.ms/azurebackup_agent)). Przed rozpoczęciem korzystania z programu DPM z usługą Azure Backup klienci programu System Center DPM muszą zaktualizować program do wersji co najmniej programu DPM 2012 R2 UR5.

## <a name="what-is-the-backup-schedule"></a>Co to jest harmonogram tworzenia kopii zapasowych?

Harmonogram tworzenia kopii zapasowych wskazuje częstotliwość operacji tworzenia kopii zapasowej. Na przykład ustawienia na poniższym ekranie wskazują, że kopie zapasowe są wykonywane codziennie o godzinie 18:00 i północy.

![Dzienny harmonogram](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Klienci mogą również zaplanować cotygodniowe wykonywanie kopii zapasowych. Na przykład ustawienia na poniższym ekranie wskazują, że kopie zapasowe są pobierane dla każdej alternatywnej niedzieli & środę o godzinie 9:10:30 i 1:10:00.

![Harmonogram tygodniowy](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Co to są zasady przechowywania?

Zasady przechowywania określają czas przechowywania kopii zapasowej. Zamiast określania "zasad płaskich" dla wszystkich punktów kopii zapasowych, klienci mogą określić różne zasady przechowywania na podstawie czasu wykonywania kopii zapasowej. Na przykład punkt kopii zapasowej dziennie, który służy jako punkt odzyskiwania, jest zachowywany przez 90 dni. Punkt kopii zapasowej wykonany na końcu każdego kwartału na potrzeby inspekcji jest zachowywany przez dłuższy czas.

![Zasady przechowywania](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Łączna liczba "punktów przechowywania" określonych w tych zasadach to 90 (dzienne punkty) + 40 (jeden kwartał przez 10 lat) = 130.

## <a name="example--putting-both-together"></a>Przykład — umieszczenie obu jednocześnie

![Przykładowy ekran](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Dzienne zasady przechowywania**: kopie zapasowe wykonywane codziennie są przechowywane przez siedem dni.
2. **Zasady przechowywania tygodniowego**: kopie zapasowe wykonane o północy i 6 PM Sobota są zachowywane przez cztery tygodnie.
3. **Zasady przechowywania miesięcznie**: kopie zapasowe wykonane o północy i 6 PM w ostatniej sobotę każdego miesiąca są zachowywane przez 12 miesięcy.
4. **Zasady przechowywania rocznego**: kopie zapasowe wykonane o północy w ostatniej sobotę każdej marca są zachowywane przez 10 lat.

Całkowita liczba "punktów przechowywania" (punkty, z których klient może przywrócić dane) na powyższym diagramie jest obliczana w następujący sposób:

* dwa punkty na dzień przez siedem dni = 14 punktów odzyskiwania
* dwa punkty na tydzień przez cztery tygodnie = 8 punktów odzyskiwania
* dwa punkty miesięcznie przez 12 miesięcy = 24 punkty odzyskiwania
* jeden punkt na rok na 10 lat = 10 punktów odzyskiwania

Łączna liczba punktów odzyskiwania to 56.

> [!NOTE]
> Za pomocą Azure Backup można utworzyć maksymalnie 9999 punktów odzyskiwania dla każdego chronionego wystąpienia. Chronione wystąpienie to komputer, serwer (fizyczny lub wirtualny) lub obciążenie, którego kopia zapasowa jest wykonywana na platformie Azure.
>

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Po wybraniu opcji **Modyfikuj** na poprzednim ekranie klienci mają większą elastyczność w określaniu harmonogramów przechowywania.

![Modyfikowanie okna zasad](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Backup, zobacz:

* [Wprowadzenie do usługi Azure Backup](./backup-overview.md)
* [Wypróbuj usługę Azure Backup](./backup-windows-with-mars-agent.md)
