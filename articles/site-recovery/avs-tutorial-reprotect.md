---
title: Ponowne włączanie ochrony maszyn wirtualnych platformy Azure w chmurze prywatnej rozwiązania Azure VMware za pomocą Azure Site Recovery
description: Dowiedz się, jak ponownie chronić maszyny wirtualne rozwiązań VMware platformy Azure po przejściu do trybu failover na platformie Azure przy użyciu Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 80ff2f3f3d5fdcf61770889dcdaaf075941b90ff
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814593"
---
# <a name="reprotect-from-azure-to-azure-vmware-solution-private-cloud"></a>Ponowne włączanie ochrony z platformy Azure do chmury prywatnej w rozwiązaniu VMware platformy Azure

Po przejściu w [tryb failover](avs-tutorial-failover.md) maszyn wirtualnych rozwiązania Azure VMware do platformy Azure pierwszy krok w przypadku powrotu po awarii do chmury prywatnej rozwiązania Azure VMware to ponowne włączenie ochrony maszyn wirtualnych platformy Azure, które zostały utworzone podczas pracy w trybie failover. W tym artykule opisano, jak to zrobić. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Wykonaj kroki opisane w [tym artykule](vmware-azure-prepare-failback.md) , aby przygotować się do ponownej ochrony i powrotu po awarii, w tym konfigurowania serwera przetwarzania na platformie Azure oraz głównego serwera docelowego chmury platformy Azure VMware oraz konfigurowania sieci VPN typu lokacja-lokacja lub ExpressRoute prywatnej komunikacji równorzędnej na potrzeby powrotu po awarii.
2. Upewnij się, że serwer konfiguracji chmury prywatnej platformy Azure VMware jest uruchomiony i połączony z platformą Azure. Podczas powrotu po awarii maszyna wirtualna musi znajdować się w bazie danych serwera konfiguracji. W przeciwnym razie powrót po awarii nie powiedzie się.
3. Usuń wszystkie migawki na głównym serwerze docelowym rozwiązania Azure VMware w chmurze prywatnej. Ochrona nie będzie zadziałała, jeśli istnieją migawki.  Migawki na maszynie wirtualnej są automatycznie scalane podczas zadania ponownego włączania ochrony.
4. W przypadku ponownego włączania ochrony maszyn wirtualnych zebranych w grupie replikacji w celu zapewnienia spójności wielu maszyn wirtualnych upewnij się, że wszystkie mają ten sam system operacyjny (Windows lub Linux) i upewnij się, że wdrażany główny serwer docelowy ma ten sam typ systemu operacyjnego. Wszystkie maszyny wirtualne w grupie replikacji muszą korzystać z tego samego głównego serwera docelowego.
5. Otwórz [wymagane porty](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) na potrzeby powrotu po awarii.
6. Upewnij się, że vCenter Server jest połączony przed powrotem po awarii. W przeciwnym razie odłączanie dysków i dołączenie ich z powrotem do maszyny wirtualnej nie powiedzie się.
7. Jeśli serwer vCenter zarządza maszynami wirtualnymi, do których nastąpi powrót po awarii, upewnij się, że masz wymagane uprawnienia. W przypadku przeprowadzania odnajdywania vCenter użytkownika tylko do odczytu i ochrony maszyn wirtualnych ochrona kończy się powodzeniem, a tryb failover działa. Jednak podczas samoochrony tryb failover nie powiedzie się, ponieważ nie można odnaleźć magazynów danych i nie jest on wyświetlany podczas ochrony. Aby rozwiązać ten problem, możesz zaktualizować poświadczenia vCenter przy użyciu [odpowiedniego konta/uprawnień](avs-tutorial-prepare-avs.md#prepare-an-account-for-automatic-discovery), a następnie ponownie wykonać zadanie. 
8. Jeśli używasz szablonu do tworzenia maszyn wirtualnych, upewnij się, że każda maszyna wirtualna ma własny identyfikator UUID dla dysków. Jeśli identyfikator UUID maszyny wirtualnej rozwiązania VMware platformy Azure jest niezgodna z identyfikatorem UUID głównego serwera docelowego, ponieważ obie zostały utworzone na podstawie tego samego szablonu, ochrona nie powiedzie się. Wdróż z innego szablonu.
9. W przypadku powrotu po awarii do alternatywnej vCenter Server upewnij się, że są odnajdywane nowe vCenter Server i główny serwer docelowy. Zazwyczaj jeśli nie są to magazyny danych, są niedostępne lub nie są widoczne w ponownej **ochronie**.
10. Sprawdź następujące scenariusze, w których nie można przeprowadzić powrotu po awarii:
    - Jeśli używasz wersji bezpłatnej ESXi 5,5 lub bezpłatnej funkcji hypervisor vSphere 6. Uaktualnij do innej wersji.
    - Jeśli masz serwer fizyczny z systemem Windows Server 2008 R2 z dodatkiem SP1.
    - Maszyny wirtualne VMware nie mogą powrócić po awarii do funkcji Hyper-V.
    - Zmigrowane maszyny wirtualne.
    - Maszyna wirtualna, która została przeniesiona do innej grupy zasobów.
    - Replika maszyny wirtualnej platformy Azure, która została usunięta.
    - Replika maszyny wirtualnej platformy Azure, która nie jest chroniona.
10. [Zapoznaj się z typami powrotu po awarii](concepts-types-of-failback.md) , których można użyć — odzyskiwanie oryginalnej lokalizacji i odzyskiwanie lokalizacji alternatywnej.


## <a name="enable-reprotection"></a>Włącz ponownie ochronę

Włącz replikację. Można ponownie włączyć ochronę określonych maszyn wirtualnych lub plan odzyskiwania:

- W przypadku ponownego włączenia ochrony planu odzyskiwania należy podać wartości dla każdej chronionej maszyny.
- Jeśli maszyny wirtualne należą do grupy replikacji w celu zapewnienia spójności z obsługą wiele maszyn wirtualnych, można je ponownie chronić przy użyciu planu odzyskiwania. Maszyny wirtualne w grupie replikacji muszą korzystać z tego samego głównego serwera docelowego

>[!NOTE]
>Ilość danych wysyłanych z platformy Azure do Erstwhile źródła podczas ponownego włączania ochrony, może być dowolna od 0 bajtów i sumą rozmiaru dysku dla wszystkich chronionych maszyn, i nie może być obliczana.

### <a name="before-you-start"></a>Przed rozpoczęciem

- Po uruchomieniu maszyny wirtualnej na platformie Azure po przejściu do trybu failover Agent może zarejestrować się ponownie na serwerze konfiguracji (do 15 minut). W tym czasie nie będzie można ponownie włączyć ochrony i zostanie wyświetlony komunikat o błędzie informujący, że Agent nie jest zainstalowany. W takim przypadku poczekaj kilka minut, a następnie ponownie Włącz ochronę.
- Jeśli chcesz zakończyć przywracanie maszyny wirtualnej platformy Azure do istniejącej maszyny wirtualnej rozwiązania VMware platformy Azure, zainstaluj magazyny danych maszyny wirtualnej z dostępem do odczytu/zapisu na hoście ESXi głównego serwera docelowego.
- Jeśli chcesz powrócić po awarii do lokalizacji alternatywnej, na przykład jeśli maszyna wirtualna rozwiązania Azure VMware nie istnieje, wybierz dysk przechowywania i magazyn danych, które są skonfigurowane dla głównego serwera docelowego. Po powrocie po awarii do chmury prywatnej rozwiązania VMware firmy Azure maszyny wirtualne w planie ochrony powrotu po awarii używają tego samego magazynu danych co główny serwer docelowy. Nowa maszyna wirtualna jest następnie tworzona w programie vCenter.

Włącz ponownie ochronę w następujący sposób:

1. Wybierz **Vault**pozycję  >  **zreplikowane elementy**magazynu. Kliknij prawym przyciskiem myszy maszynę wirtualną, która przełączona w tryb failover, a następnie wybierz pozycję **Włącz ponownie ochronę**. Lub z przycisków poleceń wybierz maszynę, a następnie wybierz pozycję **Włącz ponownie ochronę**.
2. Upewnij się, że wybrano kierunek ochrony na **platformie Azure do lokalnego** .
3. Na **głównym serwerze docelowym** i **serwerze przetwarzania**wybierz lokalny główny serwer docelowy i serwer przetwarzania.  
4. W obszarze **Magazyn**danych wybierz magazyn danych, do którego chcesz odzyskać dyski w rozwiązaniu VMware platformy Azure. Ta opcja jest używana, gdy maszyna wirtualna rozwiązania Azure VMware jest usuwana i konieczne jest utworzenie nowych dysków. Ta opcja jest ignorowana, jeśli dyski już istnieją. Nadal trzeba określić wartość.
5. Wybierz dysk przechowywania.
6. Zasady powrotu po awarii są wybierane automatycznie.
7. Wybierz **przycisk OK** , aby rozpocząć reochronę.

    ![Okno dialogowe Ponowne włączanie ochrony](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Zadanie rozpoczyna replikację maszyny wirtualnej platformy Azure do chmury prywatnej rozwiązania Azure VMware. Na karcie **Zadania** można śledzić postęp.
    - Po pomyślnym zakończeniu ochrony maszyna wirtualna przechodzi w stan chroniony.
    - Maszyna wirtualna rozwiązania VMware platformy Azure jest wyłączona podczas włączania ochrony. Pomaga to zapewnić spójność danych podczas replikacji.
    - Po zakończeniu ponownej ochrony nie należy włączać maszyny wirtualnej rozwiązania VMware platformy Azure.
   

## <a name="next-steps"></a>Następne kroki

- Jeśli napotkasz jakiekolwiek problemy, zapoznaj się z [artykułem dotyczącym rozwiązywania problemów](vmware-azure-troubleshoot-failback-reprotect.md).
- Po włączeniu ochrony maszyn wirtualnych platformy Azure można [uruchomić powrót po awarii](avs-tutorial-failback.md). Powrót po awarii zamyka maszynę wirtualną platformy Azure i uruchamia maszynę wirtualną rozwiązania VMware platformy Azure. Oczekiwano pewnego przestoju dla aplikacji i wybierz odpowiedni czas powrotu po awarii.


