---
title: Monitorowanie alertów i raportów — często zadawane pytania
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące alertu monitorowania Azure Backup i raportów Azure Backup.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 4e2585009b531512e54ffa889a05ec50038dd619
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805858"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Alert monitorowania Azure Backup — często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące monitorowania i raportowania Azure Backup.

## <a name="configure-azure-backup-reports"></a>Konfigurowanie raportów usługi Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Jak mogę sprawdzić, czy dane raportowania zaczynają przepływać do obszaru roboczego Log Analytics (LA)?

Przejdź do skonfigurowanego obszaru roboczego LA. Przejdź do pozycji menu **dzienniki** i uruchom zapytanie `CoreAzureBackup | take 1` . Jeśli zobaczysz zwracany rekord, oznacza to, że dane rozpoczęły przepływ do obszaru roboczego. Początkowe wypychanie danych może potrwać do 24 godzin.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Jaka jest częstotliwość wypychania danych do obszaru roboczego LA?

Dane diagnostyczne z magazynu są napompowane do obszaru roboczego Log Analytics z pewnym opóźnieniem. Każde zdarzenie dociera do obszaru roboczego Log Analytics od 20 do 30 minut po wypchnięciu z magazynu Recovery Services. Poniżej znajdują się dalsze szczegółowe informacje na temat opóźnienia:

* W przypadku wszystkich rozwiązań alerty wbudowane w usłudze Backup są wypychane po ich utworzeniu. Tak więc zazwyczaj pojawiają się w obszarze roboczym Log Analytics po 20 – 30 minutach.
* W przypadku wszystkich rozwiązań zadania tworzenia kopii zapasowej na żądanie i zadania przywracania są wypychane po ich zakończeniu.
* W przypadku wszystkich rozwiązań z wyjątkiem kopii zapasowej SQL zaplanowane zadania tworzenia kopii zapasowej są wypychane zaraz po zakończeniu.
* W przypadku kopii zapasowej SQL, ponieważ kopie zapasowe dzienników mogą odbywać się co 15 minut, informacje dotyczące wszystkich ukończonych zadań tworzenia kopii zapasowej, w tym dzienników, są przetwarzane wsadowe i wypychane co 6 godzin.
* W przypadku wszystkich rozwiązań, inne informacje, takie jak element kopii zapasowej, zasady, punkty odzyskiwania, magazyn itd., są wypychane co najmniej raz dziennie.
* Zmiana konfiguracji kopii zapasowej (na przykład zmiana zasad lub zasad edycji) wyzwala wypychanie wszystkich powiązanych informacji o kopii zapasowej.

### <a name="how-long-can-i-retain-reporting-data"></a>Jak długo można przechowywać dane raportowania?

Po utworzeniu obszaru roboczego LA, możesz wybrać opcję zachowywania danych przez maksymalnie 2 lata. Domyślnie obszar roboczy LA zachowuje dane przez 31 dni.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Czy po skonfigurowaniu obszaru roboczego LA zobaczysz wszystkie moje dane w raportach?

 Wszystkie dane wygenerowane po skonfigurowaniu ustawień diagnostycznych są wypychane do obszaru roboczego LA i dostępne w raportach. Zadania w toku nie są wypychane na potrzeby raportowania. Po zakończeniu zadania lub niepowodzeniu zostanie ono wysłane do raportów.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Czy mogę wyświetlać raporty w magazynach i subskrypcjach?

Tak. możesz wyświetlać raporty w magazynach i subskrypcjach, a także w regionach. Twoje dane mogą znajdować się w jednym obszarze roboczym LA lub grupie z LA obszarów roboczych.

### <a name="can-i-view-reports-across-tenants"></a>Czy mogę wyświetlać raporty w ramach dzierżawców?

Jeśli jesteś użytkownikiem [usługi Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) z delegowanym dostępem do subskrypcji klientów lub obszarów roboczych La, możesz użyć raportów kopii zapasowych, aby wyświetlić dane we wszystkich dzierżawcach.

## <a name="recovery-services-vault"></a>Magazyn usługi Recovery Services

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak długo trwa Azure Backup stan zadania agenta do odzwierciedlenia w portalu?

Aby odzwierciedlić stan zadania agenta Azure Backup, Azure Portal może potrwać do 15 minut.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Ile czasu trwa wywołanie alertu po niepowodzeniu zadania tworzenia kopii zapasowej?

Alert jest zgłaszany w ciągu 20 minut od błędu Azure Backup.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Czy istnieje przypadek, w którym nie będzie wysyłana wiadomość e-mail, jeśli są skonfigurowane powiadomienia?

Tak. W następujących sytuacjach powiadomienia nie są wysyłane:

* Jeśli powiadomienia są konfigurowane co godzinę, a alert zostanie zgłoszony i rozwiązany w ciągu godziny
* Gdy zadanie zostało anulowane
* Jeśli drugie zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem, ponieważ oryginalne zadanie tworzenia kopii zapasowej jest w toku

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi często zadawanymi pytaniami:

* [Często zadawane pytania](backup-azure-vm-backup-faq.yml) dotyczące tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.
* [Często zadawane pytania](backup-azure-file-folder-backup-faq.md) dotyczące agenta usługi Azure Backup
