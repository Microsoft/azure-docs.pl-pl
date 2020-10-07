---
title: Konfigurowanie odzyskiwania po awarii maszyny wirtualnej platformy Azure VMware na platformie Azure za pomocą Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn wirtualnych rozwiązań VMware platformy Azure przy użyciu Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: dd9d36e0f51c96a177b8bd310b75f64c948fc034
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814596"
---
# <a name="set-up-disaster-recovery-to-azure-for-azure-vmware-solution-vms"></a>Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych rozwiązań VMware platformy Azure

W tym artykule opisano, jak włączyć replikację maszyn wirtualnych rozwiązań VMware platformy Azure, na potrzeby odzyskiwania po awarii na platformie Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .

Jest to trzeci samouczek z serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn wirtualnych rozwiązań VMware platformy Azure. W poprzednim samouczku [przygotowano środowisko rozwiązania Azure VMware](avs-tutorial-prepare-avs.md) do odzyskiwania po awarii na platformie Azure.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Skonfiguruj ustawienia replikacji źródłowej oraz serwer konfiguracji Site Recovery w chmurze prywatnej rozwiązania Azure VMware
> * Skonfiguruj ustawienia elementu docelowego replikacji.
> * Tworzenie zasad replikacji.
> * Włącz replikację maszyny wirtualnej VMware.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułem w sekcji jak to zrobić w spisie treści Site Recovery.

## <a name="before-you-start"></a>Przed rozpoczęciem

Wykonaj poprzednie samouczki:

1. Upewnij się, że [skonfigurowano platformę Azure](avs-tutorial-prepare-azure.md) pod kątem odzyskiwania po awarii na platformie Azure.
2. Wykonaj następujące [kroki](avs-tutorial-prepare-avs.md) , aby przygotować wdrożenie rozwiązania Azure VMware do odzyskiwania po awarii na platformie Azure.
3. W tym samouczku pokazano, jak replikować pojedynczą maszynę wirtualną. W przypadku wdrażania wielu maszyn wirtualnych należy użyć [narzędzia planista wdrażania](https://aka.ms/asr-deployment-planner). [Dowiedz się więcej](site-recovery-deployment-planner.md) na temat tego narzędzia.
4. W tym samouczku są stosowane różne opcje, które można wykonywać w różny sposób:
    - Ten samouczek używa szablonu komórki jajowe do utworzenia maszyny wirtualnej VMware serwera konfiguracji. Jeśli z jakiegoś powodu nie możesz tego zrobić, postępuj zgodnie z [tymi instrukcjami](physical-manage-configuration-server.md) , aby ręcznie skonfigurować serwer konfiguracji.
    - W tym samouczku Site Recovery automatycznie pobiera i instaluje MySQL na serwerze konfiguracji. Jeśli wolisz, możesz ją skonfigurować ręcznie. [Dowiedz się więcej](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Wybieranie celu ochrony

1. W obszarze **Magazyny usługi Recovery Services** wybierz nazwę magazynu. W tym scenariuszu jest używany magazyn **ContosoVMVault**.
2. W obszarze **Wprowadzenie** wybierz pozycję Site Recovery. Następnie wybierz pozycję **Przygotowywanie infrastruktury**.
3. W obszarze **cel ochrony**, gdzie znajdują się  >  **maszyny**, wybierz pozycję **lokalna**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W obszarze **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**. Następnie wybierz przycisk **OK**.



## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

W środowisku źródłowym potrzebna jest pojedyncza maszyna, o wysokiej dostępności, do hostowania następujących lokalnych składników Site Recovery:

- **Serwer konfiguracji**: serwer konfiguracji koordynuje komunikację między chmurą prywatną i platformą Azure VMware, a następnie zarządza replikacją danych.
- **Serwer przetwarzania**: serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do konta magazynu pamięci podręcznej na platformie Azure. Serwer przetwarzania instaluje również agenta usługi mobilności na maszynach wirtualnych, które mają być replikowane, i wykonuje automatyczne odnajdywanie maszyn wirtualnych rozwiązań VMware platformy Azure.
- **Główny serwer docelowy**: główny serwer docelowy obsługuje dane replikacji podczas powrotu po awarii z platformy Azure.


Wszystkie te składniki są instalowane razem na pojedynczym komputerze z rozwiązaniem VMware platformy Azure, który jest znany jako *serwer konfiguracji*. Domyślnie w przypadku odzyskiwania po awarii rozwiązania VMware firmy Azure konfigurujemy serwer konfiguracji jako maszynę wirtualną VMware o wysokiej dostępności. W tym celu Pobierz przygotowaną szablon aplikacji Open Virtualization (komórki jajowe) i zaimportuj szablon do programu VMware w celu utworzenia maszyny wirtualnej.

- Najnowsza wersja serwera konfiguracji jest dostępna w portalu. Można go również pobrać bezpośrednio z [Centrum pobierania Microsoft](https://aka.ms/asrconfigurationserver).
- Jeśli z jakiegoś powodu nie można skonfigurować maszyny wirtualnej przy użyciu szablonu komórki jajowe, postępuj zgodnie z poniższymi [instrukcjami](physical-manage-configuration-server.md) , aby ręcznie skonfigurować serwer konfiguracji.
- Licencja świadczona z szablonem OVF to licencja ewaluacyjna ważna przez 180 dni. System Windows działający na maszynie wirtualnej musi być aktywowany przy użyciu wymaganej licencji.


### <a name="download-the-vm-template"></a>Pobieranie szablonu maszyny wirtualnej

1. W magazynie przejdź do pozycji **Przygotowanie infrastruktury**  >  **Źródło**.
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
4. Pobierz szablon komórki jajowe dla serwera konfiguracji.



## <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware


1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **plik** wybierz polecenie **Wdróż szablon OVF** , aby uruchomić **Kreatora wdrażania szablonu OVF**.

     ![Zrzut ekranu przedstawiający polecenie Wdróż szablon OVF w kliencie programu VMWare vSphere.](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. Na stronie **Select source** (Wybierz źródło) wprowadź lokalizację pobranego szablonu OVF.
4. Na stronie **Review details** (Przejrzyj szczegóły) wybierz pozycję **Next** (Dalej).
5. Na stronach **Select name and folder** (Wybierz nazwę i folder) oraz **Select configuration** (Wybierz konfigurację) zaakceptuj ustawienia domyślne.
6. Aby uzyskać najlepszą wydajność, na stronie **Select storage** (Wybierz magazyn) wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Select virtual disk format** (Wybierz format dysku wirtualnego).
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Po osiągnięciu stanu **Gotowe do ukończenia** w celu skonfigurowania maszyny wirtualnej przy użyciu ustawień domyślnych wybierz pozycje **Power on after deployment** (Włącz po wdrożeniu) > **Finish** (Zakończ).

   > [!TIP]
   > Jeśli chcesz dodać dodatkową kartę sieciową, usuń zaznaczenie pola wyboru **Włącz po**  >  **zakończeniu**wdrażania. Domyślnie szablon zawiera jedną kartę sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

## <a name="add-an-additional-adapter"></a>Dodawanie karty sieciowej

Jeśli chcesz dodać dodatkową kartę sieciową do serwera konfiguracji, Dodaj ją przed zarejestrowaniem serwera w magazynie. Po zarejestrowaniu nie można dodawać kart sieciowych.

1. Kliknij prawym przyciskiem myszy maszynę wirtualną na liście w kliencie vSphere, a następnie wybierz pozycję **Edytuj ustawienia**.
2. Na stronie **Hardware** (Sprzęt) wybierz pozycje **Add** > **Ethernet Adapter** (Dodaj, Karta Ethernet). Następnie wybierz pozycję **Dalej**.
3. Wybierz typ karty i sieć.
4. Aby nawiązać połączenie z wirtualną kartą sieciową po włączeniu maszyny wirtualnej, zaznacz pole **Połącz po włączeniu**. Wybierz pozycję **Dalej** > **Zakończ**. Następnie wybierz przycisk **OK**.


## <a name="register-the-configuration-server"></a>Rejestrowanie serwera konfiguracji

Po skonfigurowaniu serwera konfiguracji należy zarejestrować go w magazynie.

1. W konsoli klienta VMware vSphere Włącz maszynę wirtualną.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się na maszynie wirtualnej jako administrator.
4. Po pierwszym zalogowaniu w ciągu kilku sekund zostanie uruchomione narzędzie do konfiguracji usługi Azure Site Recovery.
5. Wprowadź nazwę używaną do zarejestrowania serwera konfiguracji w usłudze Site Recovery. Następnie wybierz pozycję **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji. Upewnij się, że odpowiednie [role](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) są przypisane do tego użytkownika.
7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Ponownie zaloguj się do maszyny. W ciągu kilku sekund zostanie automatycznie uruchomiony kreator zarządzania serwerem konfiguracji.


### <a name="configure-settings-and-add-the-vmware-server"></a>Konfiguracja ustawień i dodawanie serwera VMware

Zakończ Konfigurowanie i rejestrowanie serwera konfiguracji. Przed kontynuowaniem upewnij się, że wszystkie [wymagania wstępne](vmware-azure-deploy-configuration-server.md#prerequisites) zostały spełnione, aby można było pomyślnie skonfigurować serwer konfiguracji.


1. W Kreatorze zarządzania serwerem konfiguracji wybierz pozycję **Instalacja łączność**. Z listy rozwijanej wybierz najpierw kartę sieciową używaną przez serwer przetwarzania w celu odnajdywania i instalacji wypychanej usługi mobilności na maszynach źródłowych, a następnie wybierz kartę sieciową używaną przez serwer konfiguracji do łączności z platformą Azure. Następnie wybierz przycisk **Zapisz**. Po skonfigurowaniu tego ustawienia nie można go zmienić.
2. W obszarze **Wybierz magazyn Recovery Services**wybierz swoją subskrypcję platformy Azure i odpowiednią grupę zasobów i magazyn.
3. Na stronie **Instalowanie oprogramowania innych firm** zaakceptuj umowę licencyjną. Wybierz pozycję **Pobierz i zainstaluj**, aby zainstalować program MySQL Server. Jeśli w ścieżce została umieszczona baza danych MySQL, ten krok można pominąć. Dowiedz się [więcej](vmware-azure-deploy-configuration-server.md#configure-settings)
4. W obszarze **Weryfikowanie konfiguracji urządzenia** zostaną zweryfikowane wymagania wstępne, a następnie będzie można kontynuować.
5. W obszarze **Skonfiguruj poświadczenia serwera vCenter Server/vSphere ESXi** wprowadź nazwę FQDN bądź adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
6. Wprowadź poświadczenia użytkownika, za pomocą których serwer konfiguracji będzie łączył się z serwerem VMware. Upewnij się, że nazwa użytkownika i hasło są prawidłowe, oraz że użytkownik należy do grupy administratorów na maszynie wirtualnej, która ma być chroniona. Przy użyciu tych poświadczeń usługa Site Recovery automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Kontynuuj**.
7. W obszarze **Skonfiguruj poświadczenia maszyny wirtualnej** wprowadź nazwę użytkownika i hasło, które będą używane do automatycznego instalowania usługi Mobility na maszynach wirtualnych po włączeniu replikacji.
    - W przypadku maszyn z systemem Windows konto wymaga uprawnień administratora lokalnego na replikowanych maszynach.
    - W przypadku systemu Linux należy podać dane superużytkownika.
8. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.
9. Po zakończeniu rejestracji Otwórz Azure Portal i sprawdź, czy serwer konfiguracji i serwer VMware znajdują się na liście **Recovery Services magazynie**  >  **Zarządzaj**  >  **Site Recovery Infrastructure**  >  **serwerami konfiguracji**infrastruktury Site Recovery.


Po zarejestrowaniu serwera konfiguracji program Site Recovery nawiązuje połączenie z serwerami VMware przy użyciu określonych ustawień i odnajduje maszyny wirtualne.

> [!NOTE]
> Zanim nazwa konta pojawi się w portalu, może minąć 15 minut lub więcej. Aby natychmiast zaktualizować, wybierz pozycję **serwery konfiguracji**  >  ***Nazwa serwera***  >  **Odśwież serwer**.

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Wybierz pozycję **Przygotuj**  >  **cel**infrastruktury. Wybierz subskrypcję platformy Azure, której chcesz użyć. Firma Microsoft korzysta z modelu Resource Manager.
2. Site Recovery sprawdza, czy istnieje co najmniej jedna sieć wirtualna. Powinny one być dostępne po skonfigurowaniu składników Azure zgodnie z [pierwszym samouczkiem](tutorial-prepare-azure.md) z serii.

   ![Zrzut ekranu przedstawiający opcje elementu docelowego przygotowywania infrastruktury >.](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Wyszukaj i wybierz **Recovery Services magazynów**.
2. Wybierz magazyn usług Recovery Services (**ContosoVMVault** w ramach tego samouczka).
3. Aby utworzyć zasady replikacji, wybierz pozycję **Site Recovery**  >  **zasady replikacji**infrastruktury  >  **+ zasady replikacji**.
4. W obszarze **Tworzenie zasad replikacji** wprowadź nazwę zasad. W tym scenariuszu są używane zasady **VMwareRepPolicy**.
5. W polu **Wartość progowa celu punktu odzyskiwania** użyj domyślnej wartości 60 minut. Ta wartość określa częstość tworzenia punktów odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą spowoduje wygenerowanie alertu.
6. W polu **Przechowywanie punktu odzyskiwania** określ czas przechowywania każdego punktu odzyskiwania. Na potrzeby tego samouczka wybierz 72 godziny. Replikowane maszyny wirtualne można odzyskać do dowolnego punktu w oknie przechowywania.
7. W polu **Częstotliwość wykonywania migawek na poziomie aplikacji** określ, jak często są tworzone migawki na poziomie aplikacji. W tym scenariuszu jest używana wartość domyślna 60 minut. Wybierz pozycję **OK**, aby utworzyć zasady.

   ![Zrzut ekranu przedstawiający opcje tworzenia zasad replikacji.](./media/vmware-azure-tutorial/replication-policy.png)

- Zasady zostaną automatycznie skojarzone z serwerem konfiguracji.
- Domyślnie są też automatycznie tworzone odpowiadające im zasady powrotu po awarii. Na przykład jeśli zasady replikacji noszą nazwę **rep-policy**, zasady powrotu po awarii będą nosić nazwę **rep-policy-failback**. Te zasady są używane wyłącznie po zainicjowaniu powrotu po awarii z platformy Azure.

Uwaga: w scenariuszu VMware-to-Azure migawka spójna z awarią jest podejmowana co 5 minut.

## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację dla maszyn wirtualnych w następujący sposób:

1. Wybierz pozycję **Replikuj**  >  **Źródło**aplikacji.
2. W polu **Źródło** wybierz pozycję **Lokalne**i wybierz serwer konfiguracji z listy **Lokalizacja źródła**.
3. W obszarze **Typ maszyny** wybierz pozycję **Maszyny wirtualne**.
4. W obszarze **vCenter/vSphere Hypervisor** wybierz hosta vSphere lub serwer vCenter zarządzający tym hostem.
5. Wybierz serwer przetwarzania (domyślnie instalowany na maszynie wirtualnej serwera konfiguracji). Następnie wybierz przycisk **OK**. Stan kondycji każdego serwera przetwarzania jest wskazany zgodnie z zalecanymi limitami i innymi parametrami. Wybierz serwer przetwarzania w dobrej kondycji. Nie można wybrać serwera przetwarzania [krytycznego](vmware-physical-azure-monitor-process-server.md#process-server-alerts) . Możesz [rozwiązywać problemy i](vmware-physical-azure-troubleshoot-process-server.md) rozwiązywać te błędy **albo** skonfigurować [serwer przetwarzania skalowalny](vmware-azure-set-up-process-server-scale.md)w poziomie.
6. W obszarze **Cel** wybierz subskrypcję i grupę zasobów, w której chcesz utworzyć maszyny wirtualne w trybie failover. Stosujemy model wdrażania korzystający z usługi Resource Manager.
7. Wybierz sieć platformy Azure i podsieć, z którą nawiążą połączenie maszyny wirtualne platformy Azure, gdy zostaną uruchomione po przejściu do trybu failover.
8. Wybierz opcję **Konfiguruj teraz dla wybranych maszyn**, aby zastosować ustawienia sieci do wszystkich maszyn wirtualnych, na których ma zostać włączona replikacja. Wybierz pozycję **Konfiguruj później** , aby wybrać sieć platformy Azure na maszynę.
9. W obszarze **Virtual Machines**  >  **Wybierz pozycję Maszyny wirtualne**zaznacz każdą maszynę, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie wybierz przycisk **OK**. Jeśli nie możesz wyświetlić/wybrać konkretnej maszyny wirtualnej, [Dowiedz się więcej](https://aka.ms/doc-plugin-VM-not-showing) o rozwiązywaniu problemu.
10. W obszarze **Właściwości**  >  **Konfigurowanie właściwości**wybierz konto, które ma być używane przez serwer przetwarzania w celu automatycznego zainstalowania usługi mobilności na maszynie.
11. W obszarze **Ustawienia replikacji**  >  **Skonfiguruj ustawienia replikacji**Sprawdź, czy wybrano odpowiednie zasady replikacji.
12. Wybierz pozycję **Włącz replikację**. Po włączeniu replikacji maszyny wirtualnej usługa Site Recovery instaluje usługę Mobility.
13. Postęp zadania **Włącz ochronę** można śledzić w obszarze **Ustawienia**  >  **zadania**  >  **Site Recovery zadania**. Po uruchomieniu zadania **finalizowania ochrony** i zakończeniu generowania punktów odzyskiwania maszyna jest gotowa do przejścia w tryb failover.
14. Zastosowanie zmian i wyświetlenie ich w portalu może potrwać 15 minut lub dłużej.
15. Aby monitorować dodawane maszyny wirtualne, sprawdź ostatni znaleziony czas dla maszyn wirtualnych w obszarze **serwery konfiguracji**  >  **ostatni kontakt na stronie**. Aby dodać maszyny wirtualne, nie czekając na zaplanowane odnajdywanie, wyróżnij serwer konfiguracji (nie wybieraj go), a następnie wybierz pozycję **Odśwież**.

## <a name="next-steps"></a>Następne kroki
Po włączeniu replikacji uruchom przechodzenie do szczegółów, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> [!div class="nextstepaction"]
> [Uruchamianie próbnego odzyskiwania](avs-tutorial-dr-drill-azure.md)
