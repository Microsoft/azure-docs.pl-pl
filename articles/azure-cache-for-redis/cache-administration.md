---
title: Jak administrować usługą Azure cache for Redis
description: Dowiedz się, jak wykonywać zadania administracyjne, takie jak ponowne uruchamianie i Planowanie aktualizacji usługi Azure cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 0a79b0b5b5f21d1c75fec6b062f1ca91cfe9dd1f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219203"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Jak administrować usługą Azure cache for Redis
W tym temacie opisano sposób wykonywania zadań administracyjnych, takich jak [Ponowne uruchamianie](#reboot) i [Planowanie aktualizacji](#schedule-updates) pamięci podręcznej platformy Azure dla wystąpień Redis.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Ponowne uruchamianie
Blok **ponowne uruchomienie** umożliwia ponowne uruchomienie jednego lub większej liczby węzłów pamięci podręcznej. Ta funkcja ponownego uruchamiania umożliwia testowanie aplikacji pod kątem odporności w przypadku awarii węzła pamięci podręcznej.

![Zrzut ekranu, który podświetla opcję menu ponowny rozruch.](./media/cache-administration/redis-cache-administration-reboot.png)

Wybierz węzły do ponownego uruchomienia, a następnie kliknij przycisk **Uruchom ponownie**.

![Zrzut ekranu pokazujący, które węzły można uruchomić ponownie.](./media/cache-administration/redis-cache-reboot.png)

Jeśli masz pamięć podręczną Premium z włączoną obsługą klastrowania, możesz wybrać fragmentów pamięci podręcznej, aby przeprowadzić ponowny rozruch.

![Ponowne uruchamianie](./media/cache-administration/redis-cache-reboot-cluster.png)

Aby ponownie uruchomić co najmniej jeden węzeł pamięci podręcznej, wybierz odpowiednie węzły, a następnie kliknij przycisk **Uruchom ponownie**. Jeśli masz pamięć podręczną Premium z włączoną obsługą klastrowania, wybierz żądaną fragmentów do ponownego uruchomienia, a następnie kliknij przycisk **Uruchom ponownie**. Po kilku minutach wybrane węzły zostaną przełączone ponownie, a następnie ponownie w trybie online kilka minut później.

Wpływ na aplikacje klienckie różni się w zależności od tego, które węzły zostały ponownie rozruchowe.

* **Wzorzec** — po ponownym uruchomieniu węzła podstawowego usługa Azure cache for Redis przechodzi w tryb failover do węzła repliki i promuje go do podstawowego. Podczas pracy w trybie failover może istnieć krótki interwał, w którym połączenia mogą kończyć się niepowodzeniem w pamięci podręcznej.
* **Replika** — po ponownym uruchomieniu węzła repliki zwykle nie ma to wpływu na klientów pamięci podręcznej.
* **Zarówno podstawowy, jak i replika** — w przypadku ponownego uruchomienia obu węzłów pamięci podręcznej wszystkie dane są tracone w pamięci podręcznej i połączenia z pamięcią podręczną kończą się niepowodzeniem do momentu powrotu do trybu online węzła podstawowego Jeśli skonfigurowano [trwałość danych](cache-how-to-premium-persistence.md), najnowsza kopia zapasowa zostanie przywrócona, gdy pamięć podręczna wróci do trybu online, ale wszystkie zapisy pamięci podręcznej, które wystąpiły po ostatniej kopii zapasowej, zostaną utracone.
* **Węzły pamięci podręcznej Premium z włączoną obsługą klastrowania** — po ponownym uruchomieniu co najmniej jednego węzła pamięci podręcznej Premium z włączoną obsługą klastrowania zachowanie dla wybranych węzłów jest takie samo jak w przypadku ponownego uruchomienia odpowiedniego węzła lub węzłów nieklastrowanej pamięci podręcznej.

## <a name="reboot-faq"></a>Często zadawane pytania dotyczące ponownego uruchamiania
* [Który węzeł należy ponownie uruchomić w celu przetestowania mojej aplikacji?](#which-node-should-i-reboot-to-test-my-application)
* [Czy mogę ponownie uruchomić pamięć podręczną, aby wyczyścić połączenia klienckie?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Czy po ponownym uruchomieniu utracisz dane z mojej pamięci podręcznej?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Czy mogę uruchomić ponownie pamięć podręczną przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Który węzeł należy ponownie uruchomić w celu przetestowania mojej aplikacji?
Aby przetestować odporność aplikacji przed awarią podstawowego węzła pamięci podręcznej, należy ponownie uruchomić węzeł **główny** . Aby przetestować odporność aplikacji przed awarią węzła repliki, należy ponownie uruchomić węzeł **repliki** . Aby przetestować odporność aplikacji przed całkowitą awarią pamięci podręcznej, należy ponownie uruchomić **oba** węzły.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Czy mogę ponownie uruchomić pamięć podręczną, aby wyczyścić połączenia klienckie?
Tak, po ponownym uruchomieniu pamięci podręcznej wszystkie połączenia klientów są usuwane. Ponowny rozruch może być przydatny w przypadku, gdy wszystkie połączenia klientów są używane z powodu błędu logiki lub usterki w aplikacji klienckiej. Każda warstwa cenowa ma inne [limity połączeń klientów](cache-configure.md#default-redis-server-configuration) dla różnych rozmiarów, a po osiągnięciu tych limitów nie są akceptowane żadne dalsze połączenia z klientami. Ponowne uruchamianie pamięci podręcznej umożliwia wyczyszczenie wszystkich połączeń klientów.

> [!IMPORTANT]
> W przypadku ponownego uruchomienia pamięci podręcznej w celu wyczyszczenia połączeń klientów StackExchange. Redis automatycznie ponownie nawiązuje połączenie, gdy węzeł Redis będzie w trybie online. Jeśli podstawowy problem nie zostanie rozwiązany, połączenia klienta mogą być nadal używane.
> 
> 



### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Czy po ponownym uruchomieniu utracisz dane z mojej pamięci podręcznej?
W przypadku ponownego uruchomienia zarówno węzła **głównego** , jak i **repliki** , wszystkie dane w pamięci podręcznej (lub w tym fragmentu, jeśli używasz pamięci podręcznej Premium z włączoną obsługą klastrowania) mogą zostać utracone, ale nie jest to gwarantowane. Jeśli skonfigurowano [trwałość danych](cache-how-to-premium-persistence.md), najnowsza kopia zapasowa zostanie przywrócona, gdy pamięć podręczna wróci do trybu online, ale wszystkie zapisy pamięci podręcznej, które wystąpiły po wykonaniu kopii zapasowej, zostaną utracone.

W przypadku ponownego uruchomienia tylko jednego z węzłów dane nie są zwykle tracone, ale mogą być dostępne. Na przykład jeśli węzeł podstawowy zostanie uruchomiony ponownie, a zapis pamięci podręcznej jest w toku, dane z zapisu w pamięci podręcznej zostaną utracone. Innym scenariuszem utraty danych może być ponowny rozruch jednego węzła, a inny węzeł zostanie przeszedł w dół ze względu na awarię w tym samym czasie. Aby uzyskać więcej informacji na temat możliwych przyczyn utraty danych, zobacz [co się stało z danymi w usłudze Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Czy mogę uruchomić ponownie pamięć podręczną przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, aby uzyskać instrukcje dotyczące programu PowerShell, zobacz [Aby ponownie uruchomić pamięć podręczną platformy Azure dla Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Aktualizacje harmonogramu
Blok **harmonogram aktualizacji** umożliwia wyznaczenie okna obsługi dla wystąpienia pamięci podręcznej. Okno obsługi umożliwia kontrolowanie dni i godzin tygodnia, w których można aktualizować maszyny wirtualne obsługujące pamięć podręczną. Usługa Azure cache for Redis będzie najlepszym wysiłkiem do rozpoczęcia i zakończenia aktualizowania oprogramowania serwera Redis w określonym przedziale czasu, który określisz.

> [!NOTE] 
> Okno obsługi dotyczy aktualizacji serwera Redis i aktualizacji systemu operacyjnego maszyn wirtualnych obsługujących pamięć podręczną. Okno obsługi nie ma zastosowania do aktualizacji systemu operacyjnego hosta na hostach, na których znajdują się maszyny wirtualne pamięci podręcznej lub inne składniki sieci platformy Azure. W rzadkich przypadkach, w których pamięci podręczne są hostowane w starszych modelach (można określić, czy pamięć podręczna znajduje się w starszym modelu, jeśli nazwa DNS pamięci podręcznej jest rozpoznawana jako sufiks "cloudapp.net", "chinacloudapp.cn", "usgovcloudapi.net" lub "cloudapi.de"), okno obsługi nie ma zastosowania do aktualizacji systemu operacyjnego gościa.
>


![Aktualizacje harmonogramu](./media/cache-administration/redis-schedule-updates.png)

Aby określić okno obsługi, sprawdź wymagane dni i określ czas rozpoczęcia okna obsługi dla każdego dnia, a następnie kliknij przycisk **OK**. Należy pamiętać, że czas okna obsługi jest w formacie UTC. 

Ustawienie domyślne i minimalne okno obsługi aktualizacji to pięć godzin. Tej wartości nie można skonfigurować na podstawie Azure Portal, ale możesz ją skonfigurować w programie PowerShell przy użyciu `MaintenanceWindow` parametru polecenia cmdlet [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) . Aby uzyskać więcej informacji, zobacz mogę zarządzać zaplanowanymi aktualizacjami przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?

## <a name="schedule-updates-faq"></a>Zaplanuj często zadawane pytania dotyczące aktualizacji
* [Kiedy aktualizacje są wykonywane, jeśli nie używam funkcji Zaplanuj aktualizacje?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Jakiego typu aktualizacje są wykonywane w oknie zaplanowanej konserwacji?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Czy mogę zarządzać zaplanowanymi aktualizacjami za pomocą programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Kiedy aktualizacje są wykonywane, jeśli nie używam funkcji Zaplanuj aktualizacje?
Jeśli nie określisz okna obsługi, aktualizacje można wykonać w dowolnym momencie.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Jakiego typu aktualizacje są wykonywane w oknie zaplanowanej konserwacji?
W oknie zaplanowanej konserwacji są wykonywane tylko aktualizacje serwera Redis. Okno obsługi nie ma zastosowania do aktualizacji lub aktualizacji platformy Azure dla systemu operacyjnego maszyny wirtualnej.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Czy mogę zarządzać zaplanowanymi aktualizacjami za pomocą programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, możesz zarządzać zaplanowanymi aktualizacjami przy użyciu następujących poleceń cmdlet programu PowerShell:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

* [Pamięć podręczna platformy Azure dla warstw usług Redis](cache-overview.md#service-tiers)

