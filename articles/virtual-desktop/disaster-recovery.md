---
title: Konfigurowanie planu odzyskiwania po awarii pulpitu wirtualnego systemu Windows — Azure
description: Jak skonfigurować ciągłość biznesową i plan odzyskiwania po awarii dla wdrożenia pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 968f82a143872fd282222539ab71a70db488a20d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91935767"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>Konfigurowanie planu ciągłości działania i odzyskiwania po awarii

Aby zapewnić bezpieczeństwo danych organizacji, konieczne może być zastosowanie strategii ciągłości działania i odzyskiwania po awarii (BCDR). BCDR strategia pozwala zachować swoje aplikacje i obciążać je podczas planowanej i nieplanowanej usługi lub przerwy w działaniu platformy Azure.

Pulpit wirtualny systemu Windows oferuje BCDR dla usługi pulpitów wirtualnych systemu Windows, aby zachować metadane klienta w trakcie awarii. Gdy w regionie wystąpi awaria, składniki infrastruktury usług zostaną przełączone w tryb failover do lokalizacji dodatkowej i nadal działają normalnie. Nadal można uzyskać dostęp do metadanych związanych z usługą, a użytkownicy nadal mogą łączyć się z dostępnymi hostami. Połączenia użytkowników końcowych pozostają w trybie online, dopóki środowisko dzierżawy lub hosty pozostaną dostępne.

Aby upewnić się, że użytkownicy nadal mogą nawiązywać połączenia podczas przestoju regionu, należy replikować ich maszyny wirtualne w innej lokalizacji. W trakcie awarii lokacja główna zostanie przełączona w tryb failover do replikowanych maszyn wirtualnych w lokalizacji pomocniczej. Użytkownicy mogą nadal uzyskiwać dostęp do aplikacji z lokalizacji pomocniczej bez przeszkód. W oparciu o replikację maszyny wirtualnej należy zachować dostęp do tożsamości użytkowników w dodatkowej lokalizacji. W przypadku korzystania z kontenerów profilów należy również je replikować. Na koniec upewnij się, że Twoje aplikacje biznesowe, które opierają się na danych w lokalizacji głównej, mogą działać w trybie failover z pozostałymi danymi.

Podsumowując, aby zapewnić użytkownikom łączność podczas przestoju, należy wykonać następujące czynności w następującej kolejności:

- Replikowanie maszyn wirtualnych w dodatkowej lokalizacji.
- W przypadku korzystania z kontenerów profilów Skonfiguruj replikację danych w dodatkowej lokalizacji.
- Upewnij się, że tożsamości użytkowników skonfigurowane w lokalizacji podstawowej są dostępne w dodatkowej lokalizacji.
- Upewnij się, że wszystkie aplikacje biznesowe polegające na danych w lokalizacji podstawowej są przenoszone do trybu failover w lokacji dodatkowej.

## <a name="vm-replication"></a>Replikacja maszyny wirtualnej

Najpierw należy replikować maszyny wirtualne do lokalizacji pomocniczej. Twoje opcje są zależne od tego, jak są skonfigurowane maszyny wirtualne:

- Wszystkie maszyny wirtualne można skonfigurować zarówno dla pul hostów w puli, jak i osobistej, przy użyciu Azure Site Recovery. W przypadku tej metody należy skonfigurować tylko jedną pulę hostów i powiązane z nią grupy i obszary robocze aplikacji.
- Nową pulę hostów można utworzyć w regionie trybu failover, pozostawiając jednocześnie wszystkie zasoby w lokalizacji trybu failover. W przypadku tej metody należy skonfigurować nowe grupy i obszary robocze aplikacji w regionie trybu failover. Następnie można użyć planu Azure Site Recovery, aby włączyć pule hostów.
- Można utworzyć pulę hostów wypełnioną przez maszyny wirtualne wbudowane w regiony podstawowe i w trybie failover, jednocześnie pozostawiając maszyny wirtualne w regionie trybu failover. W takim przypadku wystarczy skonfigurować jedną pulę hostów i powiązane z nią grupy i obszary robocze aplikacji. Można użyć planu Azure Site Recovery, aby włączyć pule hostów za pomocą tej metody.

Zalecamy używanie [Azure Site Recovery](../site-recovery/site-recovery-overview.md) do zarządzania replikowaniem maszyn wirtualnych w innych lokalizacjach platformy Azure zgodnie z opisem w temacie [Architektura odzyskiwania po awarii platformy Azure na platformę Azure](../site-recovery/azure-to-azure-architecture.md). Szczególnie zalecamy używanie Azure Site Recovery dla osobistych pul hostów, ponieważ Azure Site Recovery obsługuje [jednostki SKU oparte na serwerze i klientach](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

Jeśli używasz Azure Site Recovery, nie będzie konieczne ręczne zarejestrowanie tych maszyn wirtualnych. Agent pulpitu wirtualnego systemu Windows na pomocniczej maszynie wirtualnej automatycznie użyje najnowszego tokenu zabezpieczającego w celu nawiązania połączenia z wystąpieniem najbliższej usługi. Maszyna wirtualna (Host sesji) w lokalizacji pomocniczej automatycznie stanie się częścią puli hostów. Użytkownik końcowy będzie musiał ponownie nawiązać połączenie podczas procesu, ale niezależnie od tego, czy nie ma żadnych innych operacji ręcznych.

Jeśli podczas przestoju występują połączenia z użytkownikami, aby administrator mógł rozpocząć pracę w trybie failover w regionie pomocniczym, należy zakończyć połączenia użytkowników w bieżącym regionie.

Aby rozłączyć użytkowników w systemie Windows Virtual Desktop (klasyczny), uruchom następujące polecenie cmdlet:

```powershell
Invoke-RdsUserSessionLogoff
```

Aby rozłączyć użytkowników w systemie Windows Virtual Desktop w wersji zintegrowanej z platformą Azure, Uruchom to polecenie cmdlet:

```powershell
Remove-AzWvdUserSession
```

Po wylogowaniu wszystkich użytkowników w regionie podstawowym można przełączyć maszyny wirtualne w tryb failover w regionie podstawowym i umożliwić użytkownikom łączenie się z maszynami wirtualnymi w regionie pomocniczym. Aby uzyskać więcej informacji o tym, jak działa ten proces, zobacz [replikowanie maszyn wirtualnych platformy Azure do innego regionu platformy Azure](../site-recovery/azure-to-azure-how-to-enable-replication.md).

## <a name="virtual-network"></a>Sieć wirtualna

Następnie należy wziąć pod uwagę łączność sieciową podczas przestoju. Musisz się upewnić, że skonfigurowano sieć wirtualną (VNET) w regionie pomocniczym. Jeśli użytkownicy muszą uzyskać dostęp do zasobów lokalnych, należy skonfigurować tę sieć wirtualną do uzyskiwania do nich dostępu. Połączenia lokalne można nawiązywać za pomocą sieci VPN, ExpressRoute lub wirtualnej sieci WAN.

Zalecamy używanie Azure Site Recovery, aby skonfigurować sieć wirtualną w regionie trybu failover, ponieważ zachowuje ona ustawienia sieci podstawowej i nie wymaga komunikacji równorzędnej.

## <a name="user-identities"></a>Tożsamości użytkowników

Następnie upewnij się, że kontroler domeny jest dostępny w dodatkowej lokalizacji. 

Istnieją trzy sposoby utrzymywania dostępu do kontrolera domeny:

   - Mieć kontroler domena usługi Active Directory w lokalizacji dodatkowej
   - Korzystanie z kontrolera domena usługi Active Directory lokalnego
   - Replikowanie kontrolera domena usługi Active Directory przy użyciu [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)

## <a name="user-and-app-data"></a>Dane użytkownika i aplikacji

W przypadku korzystania z kontenerów profilów następnym krokiem jest skonfigurowanie replikacji danych w dodatkowej lokalizacji. Masz pięć opcji przechowywania profilów FSLogix:

   - Bezpośrednie miejsca do magazynowania (S2D)
   - Dyski sieciowe (maszyna wirtualna z dodatkowymi dyskami)
   - Azure Files
   - Azure NetApp Files
   - Pamięć podręczna chmury na potrzeby replikacji

Aby uzyskać więcej informacji, zobacz [Opcje magazynu dla kontenerów profilów FSLogix na pulpicie wirtualnym systemu Windows](store-fslogix-profile.md).

W przypadku konfigurowania odzyskiwania po awarii dla profilów są dostępne następujące opcje:

   - Skonfiguruj natywną replikację platformy Azure (na przykład Azure Files replikacji konta magazynu w warstwie Standardowa, replikacji Azure NetApp Files lub synchronizacji Azure Files dla serwerów plików).
    
     >[!NOTE]
     >Replikacja NetApp jest uruchamiana automatycznie po jej ustawieniu. Korzystając z planów Azure Site Recovery, można dodawać skrypty wstępne i skrypty do trybu failover w celu przełączenia zasobów innych niż maszyny wirtualne do zasobów usługi Azure Storage.

   - Skonfiguruj pamięć podręczną w chmurze FSLogix dla danych aplikacji i użytkowników.
   - Skonfiguruj odzyskiwanie po awarii dla danych aplikacji tylko w celu zapewnienia dostępu do danych o kluczowym znaczeniu dla firmy przez cały czas. Za pomocą tej metody można pobrać dane użytkownika po zakończeniu awarii.

Zapoznaj się z tematem Konfigurowanie FSLogix w celu skonfigurowania odzyskiwania po awarii dla każdej opcji.

### <a name="fslogix-configuration"></a>Konfiguracja FSLogix

Agent FSLogix może obsługiwać wiele lokalizacji profilów w przypadku konfigurowania wpisów rejestru dla FSLogix.

Aby skonfigurować wpisy rejestru:

1. Otwórz **Edytor rejestru**.
2. Przejdź do **komputera**  >  **HKEY_LOCAL_MACHINE**  >    >    >  **Profile** FSLogix oprogramowania.
   
     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu okna Profile w Edytorze rejestru. Wybrano VHDLocation.](media/regedit-profiles.png)

3. Kliknij prawym przyciskiem myszy pozycję **VHDLocations** i wybierz polecenie **Edytuj wiele ciągów**.

     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający okno Edytuj wiele ciągów. Dane wartości wyświetlają lokalizacje Centrual US i Wschodnie stany USA.](media/multi-string-edit.png)

4. W polu **dane wartości** wprowadź lokalizacje, których chcesz użyć.
5. Gdy skończysz, wybierz opcję **OK**.

Jeśli pierwsza lokalizacja jest niedostępna, Agent FSLogix automatycznie przejdzie w tryb failover na sekundę i tak dalej.

Zalecamy skonfigurowanie agenta FSLogix z ścieżką do lokalizacji pomocniczej w regionie głównym. Po zamknięciu lokalizacji podstawowej Agent FLogix zostanie zreplikowany w ramach replikacji Azure Site Recovery maszyny wirtualnej. Gdy zreplikowane maszyny wirtualne będą gotowe, agent automatycznie podejmie próbę ścieżki do regionu pomocniczego.

Załóżmy na przykład, że maszyny wirtualne hosta sesji głównej znajdują się w regionie Środkowe stany USA, ale kontener profilu znajduje się w regionie Środkowe stany USA ze względu na wydajność.

W takim przypadku należy skonfigurować agenta FSLogix z ścieżką do magazynu w środkowych stanach USA. Maszyny wirtualne hosta sesji można skonfigurować tak, aby były replikowane w regionie zachodnie stany USA. Gdy ścieżka do centralnych Stanów Zjednoczonych nie powiedzie się, Agent podejmie próbę utworzenia nowej ścieżki dla magazynu w regionie zachodnie stany USA.

### <a name="s2d"></a>WŁĄCZENIU

Ponieważ funkcja S2D obsługuje replikację między regionami wewnętrznie, nie trzeba ręcznie konfigurować ścieżki pomocniczej.

### <a name="network-drives-vm-with-extra-drives"></a>Dyski sieciowe (maszyna wirtualna z dodatkowymi dyskami)

W przypadku replikowania maszyn wirtualnych magazynu sieciowego przy użyciu Azure Site Recovery takich jak maszyny wirtualne hosta sesji, odzyskiwanie zachowuje tę samą ścieżkę, co oznacza, że nie trzeba ponownie konfigurować FSlogix.

### <a name="azure-files"></a>Azure Files

Azure Files obsługuje replikację asynchroniczną między regionami, którą można określić podczas tworzenia konta magazynu. Jeśli asynchroniczny charakter Azure Files obejmuje już cele odzyskiwania po awarii, nie trzeba wykonywać dodatkowych czynności konfiguracyjnych.

Jeśli potrzebna jest replikacja synchroniczna w celu zminimalizowania utraty danych, zalecamy użycie usługi FSLogix w pamięci podręcznej w chmurze.

>[!NOTE]
>Ta sekcja nie obejmuje mechanizmu uwierzytelniania trybu failover dla Azure Files.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Dowiedz się więcej na temat Azure NetApp Files [tworzenia komunikacji równorzędnej replikacji dla Azure NetApp Files](../azure-netapp-files/cross-region-replication-create-peering.md).

## <a name="app-dependencies"></a>Zależności aplikacji

Na koniec upewnij się, że wszystkie aplikacje biznesowe, które opierają się na danych znajdujących się w regionie podstawowym, mogą przechodzić w tryb failover do lokalizacji dodatkowej. Należy również skonfigurować ustawienia, które aplikacje muszą działać w nowej lokalizacji. Na przykład jeśli jedna z aplikacji jest zależna od zaplecza SQL, upewnij się, że baza danych SQL jest replikowana w lokalizacji pomocniczej. Aplikację należy skonfigurować tak, aby korzystała z lokalizacji dodatkowej w ramach procesu przełączania do trybu failover lub konfiguracji domyślnej. Zależności aplikacji można modelować w planach Azure Site Recovery. Aby dowiedzieć się więcej, zobacz [Informacje o planach odzyskiwania](../site-recovery/recovery-plan-overview.md).

## <a name="disaster-recovery-testing"></a>Testowanie odzyskiwania po awarii

Po zakończeniu konfigurowania odzyskiwania po awarii należy przetestować swój plan, aby upewnić się, że działa.

Poniżej przedstawiono kilka sugestii dotyczących testowania planu:

- Jeśli testowa maszyna wirtualna ma dostęp do Internetu, zajmie ona wszystkie istniejące hosty sesji w celu uzyskania nowych połączeń, ale wszystkie istniejące połączenia z oryginalnym hostem sesji pozostaną aktywne. Upewnij się, że administrator, który uruchomił test, zarejestruje wszystkich aktywnych użytkowników przed przetestowaniem planu. 
- W oknie obsługi należy przeprowadzać pełne testy odzyskiwania po awarii, aby nie zakłócać użytkowników. Dla testu można także użyć puli hostów w środowisku walidacji. 
- Upewnij się, że test obejmuje wszystkie aplikacje o krytycznym znaczeniu dla firmy.
- Zalecamy przełączenie w tryb failover tylko do 100 maszyn wirtualnych. Jeśli masz więcej maszyn wirtualnych, zalecamy ich przełączenie w tryb failover w partiach 10 minut.

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania dotyczące sposobu, aby zapewnić bezpieczeństwo danych, oprócz planowania awarii, zapoznaj się z naszym [przewodnikiem](security-guide.md)dotyczącym zabezpieczeń.