---
title: Skonfiguruj odzyskiwanie po awarii funkcji Hyper-V do lokacji dodatkowej za pomocą Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych funkcji Hyper-V między własnymi lokalnymi lokacjami przy użyciu usługi Azure Site Recovery.
ms.service: site-recovery
ms.topic: how-to
ms.date: 11/14/2019
ms.openlocfilehash: 11d8ff3eafa046245f5e852cb3ec7a57db103555
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581244"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V do dodatkowej lokacji lokalnej

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

W tym artykule pokazano, jak skonfigurować odzyskiwanie po awarii do lokacji dodatkowej dla lokalnych maszyn wirtualnych z funkcją Hyper-V zarządzanych w chmurach programu System Center Virtual Machine Manager (VMM). W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Przygotowywanie lokalnych serwerów VMM i hostów funkcji Hyper-V
> * Tworzenie magazynu usługi Recovery Services dla usługi Site Recovery 
> * Konfigurowanie źródłowych i docelowych środowisk replikacji. 
> * Konfigurowanie mapowania sieci 
> * Tworzenie zasad replikacji
> * Włączanie replikacji maszyny wirtualnej


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten scenariusz:

- Zapoznaj się ze [składnikami i architekturą scenariusza](hyper-v-vmm-architecture.md).
- Upewnij się, że serwery VMM i hosty funkcji Hyper-V są zgodne z [wymaganiami obsługi technicznej](hyper-v-vmm-secondary-support-matrix.md).
- Sprawdź, czy maszyny wirtualne, które chcesz replikować, są zgodne z [obsługą techniczną replikowanych maszyn](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Przygotuj serwery VMM do mapowania sieci.

### <a name="prepare-for-network-mapping"></a>Przygotowanie do mapowania sieci

[Mapowanie sieci](hyper-v-vmm-network-mapping.md) mapuje między lokalnymi sieciami maszyn wirtualnych programu VMM w chmurach źródłowych i docelowych. Mapowanie wykonuje następujące czynności:

- Łączy maszyny wirtualne z odpowiednimi docelowymi sieciami maszyn wirtualnych po przejściu w tryb failover. 
- Optymalnie umieszcza repliki maszyn wirtualnych na docelowych serwerach hostów funkcji Hyper-V. 
- Jeśli nie skonfigurujesz mapowania sieci, maszyny wirtualne repliki nie będą połączone z siecią maszyny wirtualnej po przejściu w tryb failover.

Przygotuj program VMM w następujący sposób:

1. Upewnij się, że masz [sieci logiczne programu VMM](/system-center/vmm/network-logical) na źródłowych i docelowych serwerach VMM.
    - Sieć logiczna na serwerze źródłowym powinna być skojarzona z chmurą źródłową, w której znajdują się hosty funkcji Hyper-V.
    - Sieć logiczna na serwerze docelowym powinna być skojarzona z chmurą docelową.
1. Upewnij się, że masz [sieci maszyn wirtualnych](/system-center/vmm/network-virtual) na źródłowych i docelowych serwerach VMM. Sieci maszyn wirtualnych powinny być połączone z siecią logiczną w każdej lokalizacji.
2. Połącz maszyny wirtualne na źródłowych hostach funkcji Hyper-V ze źródłową siecią maszyny wirtualnej. 


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Wybieranie celu ochrony

Wybierz, co chcesz replikować, i miejsce, do którego chcesz przeprowadzać replikację.

1. Kliknij **Site Recovery**  >  **krok 1: przygotowanie**  >  **celu ochrony** infrastruktury.
2. Wybierz pozycję **Do lokacji odzyskiwania**, a następnie wybierz pozycję **Tak, przy użyciu funkcji Hyper-V**.
3. Wybierz pozycję **Tak**, aby wskazać, że używasz programu VMM do zarządzania hostami funkcji Hyper-V.
4. Wybierz pozycję **Tak**, jeśli masz pomocniczy serwer VMM. Jeśli wdrażasz replikację między chmurami na pojedynczym serwerze VMM, kliknij pozycję **Nie**. Następnie kliknij przycisk **OK**.


## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Zainstaluj dostawcę usługi Azure Site Recovery na serwerach VMM oraz odnajdź i zarejestruj serwery w magazynie.

1. Kliknij pozycję **Przygotuj**  >  **Źródło** infrastruktury.
2. W obszarze **Przygotowywanie źródła** kliknij pozycję **+ VMM**, aby dodać serwer programu VMM.
3. W obszarze **Dodaj serwer** sprawdź, czy pozycja **Serwer System Center VMM** pojawia się jako **Typ serwera**.
4. Pobierz plik instalacyjny dostawcy usługi Azure Site Recovery.
5. Pobierz klucz rejestracji. Jest to potrzebne podczas instalacji dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Zrzut ekranu przedstawiający opcje pobierania dostawcy i klucza rejestracji.](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Zainstaluj dostawcę na każdym serwerze VMM. Nie musisz niczego jawnie instalować na hostach funkcji Hyper-V.

### <a name="install-the-azure-site-recovery-provider"></a>Instalowanie dostawcy usługi Azure Site Recovery

1. Uruchom plik Instalatora dostawcy na każdym serwerze VMM. Jeśli program VMM jest wdrażany w klastrze, zainstaluj go po raz pierwszy w następujący sposób:
    -  Zainstaluj dostawcę na aktywnym węźle i ukończ instalację, aby zarejestrować serwer VMM w magazynie.
    - Następnie zainstaluj dostawcę w pozostałych węzłach. Na węzłach klastra powinna być uruchomiona ta sama wersja dostawcy.
2. Instalator uruchamia kilka kontroli wymagań wstępnych i żąda uprawnienia do zatrzymania usługi programu VMM. Usługa programu VMM zostanie automatycznie ponownie uruchomiona po zakończeniu instalacji. Jeśli instalujesz w klastrze program VMM, zostanie wyświetlony monit o zatrzymanie roli klastra.
3. W usłudze **Microsoft Update** można włączyć określenie, czy aktualizacje dostawcy są instalowane zgodnie z Twoimi zasadami usługi Microsoft Update.
4. W obszarze **Instalacja** zaakceptuj lub zmodyfikuj domyślną lokalizację instalacji i kliknij przycisk **Zainstaluj**.
5. Po zakończeniu instalacji kliknij przycisk **Zarejestruj**, aby zarejestrować serwer w magazynie.

    ![Zrzut ekranu przedstawiający ekran instalacja dostawcy, w tym lokalizację instalacji.](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. Zweryfikuj, że w polu **Nazwa magazynu** jest wpisana nazwa magazynu, w którym serwer zostanie zarejestrowany. Kliknij przycisk **Dalej**.
7. W obszarze **Połączenie serwera proxy** określ, jak dostawca uruchomiony na serwerze VMM łączy się z platformą Azure.
   - Możesz określić, czy dostawca powinien łączyć się bezpośrednio z Internetem, czy też za pośrednictwem serwera proxy. Określ ustawienia serwera proxy odpowiednio do potrzeb.
   - W przypadku użycia serwera proxy konto Uruchom jako programu VMM (DRAProxyAccount) jest automatycznie tworzone przy użyciu określonych poświadczeń serwera proxy. Skonfiguruj serwer proxy tak, aby to konto mogło być pomyślnie uwierzytelnione. Ustawienia konta Uruchom jako można modyfikować w konsoli programu VMM > **Ustawienia**  >  **zabezpieczeń**  >  **konta Uruchom jako**.
   - Uruchom ponownie usługę programu VMM, aby zaktualizować zmiany.
8. W oknie **Klucz rejestracji** wybierz klucz, który został pobrany i skopiowany na serwer VMM.
9. Ustawienie szyfrowania nie ma znaczenia w przypadku tego scenariusza. 
10. W polu **Nazwa serwera** wprowadź przyjazną nazwę identyfikującą serwer VMM w magazynie. W klastrze określ nazwę roli klastra VMM.
11. W oknie **Synchronizacja metadanych chmury** określ, czy chcesz synchronizować metadane dla wszystkich chmur na serwerze programu VMM. To działanie ma miejsce tylko raz na każdym serwerze. Jeśli nie chcesz synchronizować wszystkich chmur, nie zaznaczaj tego ustawienia. Możesz synchronizować poszczególne chmury indywidualnie we właściwościach chmury w konsoli programu VMM.
12. Kliknij przycisk **Dalej**, aby ukończyć proces. Po rejestracji usługa Site Recovery pobiera metadane z serwera programu VMM. Serwer jest wyświetlany na **serwerach**  >  **programu VMM** w magazynie.
13. Gdy serwer pojawi się w magazynie **, w obszarze** źródło  >  **przygotowanie** źródła wybierz serwer VMM i wybierz chmurę, w której znajduje się host funkcji Hyper-V. Następnie kliknij przycisk **OK**.


## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz docelowy serwer VMM i chmurę:

1. Kliknij pozycję **Przygotuj**  >  **miejsce docelowe** infrastruktury i wybierz docelowy serwer programu VMM.
2. Zostaną wyświetlone chmury programu VMM, które są zsynchronizowane z usługą Site Recovery. Wybierz chmurę docelową.

   ![Zrzut ekranu przedstawiający docelowy serwer VMM i wybrane w chmurze.](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

Przed rozpoczęciem upewnij się, że wszystkie hosty, które korzystają z zasad, mają ten sam system operacyjny. Jeśli na hostach są uruchomione różne wersje systemu Windows Server, konieczne będzie wiele zasad replikacji.

1. Aby utworzyć nowe zasady replikacji, kliknij kolejno pozycje **Przygotuj infrastruktury**  >  **Ustawienia replikacji**  >  **+ Utwórz i skojarz**.
2. W obszarze **Utwórz i skojarz zasady** Określ nazwę zasad. Typem źródłowym i docelowym powinien być **Hyper-V**.
3. W obszarze **Wersja hosta funkcji Hyper-V** wybierz system operacyjny uruchomiony na hoście.
4. W pozycji **Typ uwierzytelniania** i **Port uwierzytelniania** określ, jak ruch jest uwierzytelniany między serwerami podstawowymi i odzyskiwania hostów funkcji Hyper-V.
    - Wybierz pozycję **Certyfikat**, chyba że masz już działające środowisko protokołu Kerberos. Usługa Azure Site Recovery automatycznie skonfiguruje certyfikaty do uwierzytelniania protokołu HTTPS. Nie musisz wykonywać żadnych czynności ręcznie.
    - Domyślnie porty 8083 i 8084 (dla certyfikatów) zostaną otwarte w zaporze systemu Windows na serwerach hostów funkcji Hyper-V.
    - Jeśli wybierzesz pozycję **Kerberos**, bilet protokołu Kerberos będzie używany do wzajemnego uwierzytelniania serwerów hosta. Protokół Kerberos jest odpowiedni tylko dla serwerów hostów funkcji Hyper-V uruchomionych w systemie Windows Server 2012 R2 lub nowszym.
1. W obszarze **częstotliwość kopiowania** Określ, jak często mają być replikowane dane przyrostowe po replikacji początkowej (co 30 sekund, 5 lub 15 minut).
2. W obszarze **Przechowywanie punktu odzyskiwania** określ (w godzinach), jak długie będzie okno przechowywania dla każdego punktu odzyskiwania. Zreplikowane maszyny można odzyskać do dowolnego punktu w tym oknie.
3. W obszarze **Częstotliwość wykonywania migawek na poziomie aplikacji** określ, jak często (1–12 godzin) są tworzone punkty odzyskiwania zawierające migawki spójne na poziomie aplikacji. Funkcja Hyper-V wykorzystuje dwa typy migawek:
    - **Standardowa migawka**: jest przyrostową migawką całej maszyny wirtualnej.
    - **Migawka spójna na poziomie aplikacji**: jest tworzona w określonym momencie i obejmuje dane aplikacji zawarte w maszynie wirtualnej. Usługa kopiowania woluminów w tle (VSS) zapewnia, że aplikacje znajdują się w stanie spójności podczas wykonywania migawki. Włączanie migawek spójnych na poziomie aplikacji ma wpływ na wydajność aplikacji na źródłowych maszynach wirtualnych. Ustaw wartość, która jest mniejsza od liczby skonfigurowanych dodatkowych punktów odzyskiwania.
4. W obszarze **Kompresja transferu danych** określ, czy przesyłane dane replikacji powinny podlegać kompresji.
5. Wybierz pozycję **Usuń replikę maszyny wirtualnej**, aby określić, że replika maszyny wirtualnej powinna zostać usunięta w razie wyłączenia ochrony źródłowej maszyny wirtualnej. Jeśli włączysz to ustawienie, po wyłączeniu ochrony dla źródłowej maszyny wirtualnej zostanie ona usunięta z konsoli usługi Site Recovery, ustawienia usługi Site Recovery dla programu VMM zostaną usunięte z konsoli programu VMM i replika zostanie usunięta.
6. W obszarze **Metoda replikacji początkowej**, jeśli przeprowadzasz replikację za pośrednictwem sieci, określ, czy rozpocząć replikację początkowa, czy też ją zaplanować. Aby oszczędzić przepustowość sieci, warto zaplanować ją poza godzinami dużego obciążenia. Następnie kliknij przycisk **OK**.

     ![Zrzut ekranu przedstawiający opcje zasad replikacji.](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Nowe zasady zostaną automatycznie skojarzone z chmurą programu VMM. W obszarze **Zasady replikacji** kliknij przycisk **OK**. 


## <a name="enable-replication"></a>Włączanie replikacji

1. Kliknij pozycję **Replikuj**  >  **Źródło** aplikacji. 
2. W obszarze **Źródło** wybierz serwer VMM i chmurę, w której znajdują się hosty funkcji Hyper-V do zreplikowania. Następnie kliknij przycisk **OK**.
3. W obszarze **Docelowy** sprawdź pomocniczy serwer programu VMM i chmurę.
4. W obszarze **Maszyny wirtualne** wybierz z listy maszyny wirtualne, które chcesz chronić.


Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po zakończeniu zadania **Finalizuj ochronę** replikacja początkowa zostanie zakończona, a maszyna wirtualna będzie gotowa do pracy w trybie failover.

## <a name="next-steps"></a>Następne kroki

[Uruchamianie próbnego odzyskiwania po awarii](hyper-v-vmm-test-failover.md)
