---
title: Jak administrować Azure Cache for Redis
description: Dowiedz się, jak wykonywać zadania administracyjne, takie jak ponowne uruchamianie i planowanie aktualizacji Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7f8eb44ab301b211eaeb2dc3679c97714f91f0da
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833044"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Jak administrować Azure Cache for Redis
W tym temacie opisano sposób wykonywania [](#schedule-updates) zadań administracyjnych, takich jak ponowne [uruchamianie](#reboot) i planowanie aktualizacji dla Azure Cache for Redis wystąpień.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Ponowne uruchamianie
Blok **Ponowne** uruchamianie umożliwia ponowne uruchomienie jednego lub większej liczby węzłów pamięci podręcznej. Ta funkcja ponownego uruchamiania umożliwia przetestowanie aplikacji pod kątem odporności w przypadku awarii węzła pamięci podręcznej.

![Zrzut ekranu przedstawiający opcję menu Uruchom ponownie.](./media/cache-administration/redis-cache-administration-reboot.png)

Wybierz węzły do ponownego uruchomienia, a następnie kliknij pozycję **Uruchom ponownie.**

![Zrzut ekranu przedstawiający węzły, które można ponownie uruchomić.](./media/cache-administration/redis-cache-reboot.png)

Jeśli masz pamięć podręczną Premium z włączonym klastrem, możesz wybrać fragmenty pamięci podręcznej do ponownego uruchomienia.

![Ponowne uruchamianie](./media/cache-administration/redis-cache-reboot-cluster.png)

Aby ponownie uruchomić co najmniej jeden węzeł pamięci podręcznej, wybierz żądane węzły i kliknij pozycję **Uruchom ponownie.** Jeśli masz pamięć podręczną Premium z włączonym klastrem, wybierz odpowiednie fragmenty do ponownego uruchomienia, a następnie kliknij przycisk **Uruchom ponownie.** Po kilku minutach wybrane węzły zostaną ponownie uruchomiony i po kilku minutach ponownie przejdą do trybu online.

Wpływ na aplikacje klienckie różni się w zależności od tego, które węzły są ponownie uruchamiane.

* **Węzeł** główny — po ponownym uruchomieniu węzła podstawowego Azure Cache for Redis do węzła repliki i podniesie go do węzła podstawowego. Podczas tego trybu failover może wystąpić krótki interwał, w którym połączenia mogą nie powieść się z pamięcią podręczną.
* **Replika** — po ponownym uruchomieniu węzła repliki zwykle nie ma to wpływu na klientów pamięci podręcznej.
* **Zarówno podstawowa,** jak i replika — po ponownym uruchomieniu obu węzłów pamięci podręcznej wszystkie dane zostaną utracone w pamięci podręcznej, a połączenia z pamięcią podręczną nie powiodą się do momentu powrotu węzła podstawowego do trybu online. Jeśli skonfigurowano trwałość danych [,](cache-how-to-premium-persistence.md)najnowsza kopia zapasowa jest przywracana, gdy pamięć podręczna wróci do trybu online, ale wszystkie zapis w pamięci podręcznej, które wystąpiły po ostatniej kopii zapasowej, zostaną utracone.
* Węzły pamięci podręcznej w awarii **Premium** z włączonym klastrem — po ponownym uruchomieniu co najmniej jednego węzła pamięci podręcznej Premium z włączonym klastrem zachowanie wybranych węzłów jest takie samo jak w przypadku ponownego uruchomienia odpowiedniego węzła lub węzłów nieklaterowanej pamięci podręcznej.

## <a name="reboot-faq"></a>Często zadawane pytania dotyczące ponownego uruchamiania
* [Który węzeł należy ponownie uruchomić, aby przetestować moją aplikację?](#which-node-should-i-reboot-to-test-my-application)
* [Czy mogę ponownie uruchomić pamięć podręczną, aby wyczyścić połączenia klientów?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Czy w przypadku ponownego uruchomienia utracę dane z pamięci podręcznej?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Czy mogę ponownie uruchomić pamięć podręczną przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Który węzeł należy ponownie uruchomić, aby przetestować moją aplikację?
Aby przetestować odporność aplikacji na awarię węzła podstawowego pamięci podręcznej, uruchom ponownie **węzeł** główny. Aby przetestować odporność aplikacji na awarię węzła repliki, uruchom ponownie węzeł **repliki.** Aby przetestować odporność aplikacji na całkowitą awarię pamięci podręcznej, uruchom ponownie **oba węzły.**

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Czy mogę ponownie uruchomić pamięć podręczną, aby wyczyścić połączenia klientów?
Tak, jeśli ponownie uruchomisz pamięć podręczną, wszystkie połączenia klienta zostaną wyczyszone. Ponowne uruchomienie może być przydatne w przypadku, gdy wszystkie połączenia klienckie są używane z powodu błędu logiki lub usterki w aplikacji klienckiej. Każda warstwa cenowa ma różne [limity](cache-configure.md#default-redis-server-configuration) połączeń klienta dla różnych rozmiarów, a po osiągnięciu tych limitów nie są akceptowane żadne więcej połączeń klienckich. Ponowne uruchomienie pamięci podręcznej umożliwia wyczyszczenie wszystkich połączeń klienta.

> [!IMPORTANT]
> Jeśli ponownie uruchomisz pamięć podręczną w celu wyczyszczenia połączeń klientów, stackExchange.Redis automatycznie ponownie nawiąze połączenie, gdy węzeł Redis powróci do trybu online. Jeśli podstawowy problem nie zostanie rozwiązany, połączenia klienta mogą być nadal używane.
> 
> 



### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Czy w przypadku ponownego uruchomienia utracę dane z pamięci podręcznej?
W przypadku ponownego  rozruchu zarówno węzła głównego, jak i węzła repliki wszystkie dane w pamięci podręcznej (lub w tym fragmentie, jeśli używasz pamięci podręcznej Premium z włączonym klastrem) mogą zostać utracone, ale nie jest to gwarantowane.  Jeśli skonfigurowano trwałość danych [,](cache-how-to-premium-persistence.md)najnowsza kopia zapasowa zostanie przywrócona, gdy pamięć podręczna wróci do trybu online, ale zostaną utracone wszystkie zapis w pamięci podręcznej, które wystąpiły po zakończeniu tworzenia kopii zapasowej.

W przypadku ponownego uruchomienia tylko jednego z węzłów dane nie są zwykle utracone, ale nadal mogą być. Jeśli na przykład węzeł podstawowy zostanie ponownie uruchomiony i trwa zapis w pamięci podręcznej, dane z zapisu w pamięci podręcznej utracą. Inny scenariusz utraty danych może mieć miejsce w przypadku ponownego uruchomienia jednego węzła, a drugi w tym samym czasie z powodu awarii. Aby uzyskać więcej informacji o możliwych przyczynach utraty danych, zobacz [Co się stało z moimi danymi w uciece Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Czy mogę ponownie uruchomić pamięć podręczną przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, aby uzyskać instrukcje dotyczące programu PowerShell, zobacz [Aby ponownie uruchomić Azure Cache for Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Aktualizacje harmonogramu
Blok **Planowanie aktualizacji** umożliwia wyznaczenie okna obsługi dla wystąpienia pamięci podręcznej. Okno obsługi umożliwia kontrolowanie dni i godzin tygodnia, w których można aktualizować maszyny wirtualne hostowane w pamięci podręcznej. Azure Cache for Redis wszelkich starań, aby rozpocząć i zakończyć aktualizowanie oprogramowania serwera Redis w określonym przedziale czasu.

> [!NOTE] 
> Okno obsługi dotyczy aktualizacji serwera Redis i aktualizacji systemu operacyjnego maszyn wirtualnych hostowanych w pamięci podręcznej. Okno obsługi nie ma zastosowania do aktualizacji systemu operacyjnego hosta na hostach hostów maszyn wirtualnych pamięci podręcznej lub innych Azure Networking serwerach. W rzadkich przypadkach, gdy pamięci podręczne są hostowane w starszych modelach (można określić, czy pamięć podręczna znajduje się w starszym modelu, jeśli nazwa DNS pamięci podręcznej jest rozpoznawalna jako sufiks "cloudapp.net", "chinacloudapp.cn", "usgovcloudapi.net" lub "cloudapi.de"), okno obsługi nie będzie mieć zastosowania do aktualizacji systemu operacyjnego gościa.
>


![Aktualizacje harmonogramu](./media/cache-administration/redis-schedule-updates.png)

Aby określić okno obsługi, sprawdź żądane dni i określ godzinę rozpoczęcia okna obsługi dla każdego dnia, a następnie kliknij przycisk **OK.** Należy pamiętać, że czas okna obsługi jest w czasie UTC. 

Domyślne i minimalne okno obsługi aktualizacji wynosi pięć godzin. Tej wartości nie można skonfigurować z Azure Portal, ale można ją skonfigurować w programie PowerShell przy użyciu parametru polecenia `MaintenanceWindow` cmdlet [New-AzRedisCacheScheduleEntry.](/powershell/module/az.rediscache/new-azrediscachescheduleentry) Aby uzyskać więcej informacji, zobacz Czy mogę zarządzać zaplanowanymi aktualizacjami przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?

## <a name="schedule-updates-faq"></a>Często zadawane pytania dotyczące planowania aktualizacji
* [Kiedy są dostępne aktualizacje, jeśli nie używam funkcji planowania aktualizacji?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Jakiego typu aktualizacje są dokonywane podczas zaplanowanego okna obsługi?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Czy można zarządzać zaplanowanymi aktualizacjami przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Kiedy są dostępne aktualizacje, jeśli nie używam funkcji planowania aktualizacji?
Jeśli nie określisz okna obsługi, aktualizacje mogą być dokonywane w dowolnym momencie.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Jakiego typu aktualizacje są dokonywane podczas zaplanowanego okna obsługi?
Podczas zaplanowanego okna obsługi są dokonywane tylko aktualizacje serwera Redis. Okno obsługi nie ma zastosowania do aktualizacji platformy Azure ani aktualizacji systemu operacyjnego maszyny wirtualnej.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Czy można zarządzać zaplanowanymi aktualizacjami przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, możesz zarządzać zaplanowanymi aktualizacjami przy użyciu następujących poleceń cmdlet programu PowerShell:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o Azure Cache for Redis funkcji.

* [Azure Cache for Redis warstw usług](cache-overview.md#service-tiers)

