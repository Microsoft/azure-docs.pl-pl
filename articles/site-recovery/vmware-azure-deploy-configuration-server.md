---
title: Wdróż serwer konfiguracji w Azure Site Recovery
description: W tym artykule opisano sposób wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii oprogramowania VMware przy użyciu Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: a8ceb3df68ebe42f83c70ed62327bf59c0dfc225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359819"
---
# <a name="deploy-a-configuration-server"></a>Wdrażanie serwera konfiguracji

Lokalny serwer konfiguracji jest wdrażany w przypadku używania [Azure Site Recovery](site-recovery-overview.md) na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Serwer konfiguracji koordynuje komunikację między lokalnym programem VMware i platformą Azure. Umożliwia również zarządzanie replikacją danych. W tym artykule przedstawiono kroki niezbędne do wdrożenia serwera konfiguracji w przypadku replikowania maszyn wirtualnych VMware na platformę Azure. Jeśli trzeba skonfigurować serwer konfiguracji do replikacji serwera fizycznego, zobacz [Konfigurowanie serwera konfiguracji na potrzeby odzyskiwania po awarii serwerów fizycznych na platformę Azure](physical-azure-set-up-source.md).

> [!TIP]
> Aby dowiedzieć się więcej o roli serwera konfiguracji w ramach architektury Azure Site Recovery, zobacz [Architektura odzyskiwania po awarii oprogramowania VMware na platformę Azure](vmware-azure-architecture.md).

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Wdrażanie serwera konfiguracji za pomocą szablonu komórki jajowe

Serwer konfiguracji należy skonfigurować jako maszynę wirtualną VMware o wysokiej dostępności z niektórymi minimalnymi wymaganiami dotyczącymi sprzętu i wielkości. W przypadku wygodnego i łatwego wdrożenia Site Recovery udostępnia szablon do pobrania aplikacja Open Virtualization (komórki jajowe) do skonfigurowania serwera konfiguracji, który spełnia wszystkie wymagane wymagania wymienione w tym miejscu.

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono podsumowanie minimalnych wymagań sprzętowych dotyczących serwera konfiguracji.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Wymagania dotyczące uprawnień Azure Active Directory

Użytkownik musi mieć jeden z następujących uprawnień ustawionych w Azure Active Directory (Azure AD), aby zarejestrować serwer konfiguracji za pomocą usług Azure Site Recovery Services.

1. Użytkownik musi mieć rolę dewelopera aplikacji, aby utworzyć aplikację.
    - Aby sprawdzić, zaloguj się do Azure Portal.</br>
    - Przejdź do **Azure Active Directory**  >  **ról i administratorów**.</br>
    - Sprawdź, czy rola dewelopera aplikacji jest przypisana do użytkownika. W przeciwnym razie należy użyć użytkownika z tym uprawnieniem lub skontaktować się z [administratorem, aby włączyć uprawnienie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md#assign-roles).
    
2. Jeśli nie można przypisać roli dewelopera aplikacji, upewnij się, że flaga **Użytkownicy mogą rejestrować aplikacje** **, tak aby** użytkownik mógł utworzyć tożsamość. Aby włączyć te uprawnienia:
    - Zaloguj się w witrynie Azure Portal.
    - Przejdź do pozycji **Azure Active Directory**  >  **Ustawienia użytkownika**.
    - W obszarze rejestracje aplikacji **Użytkownicy mogą rejestrować aplikacje**, a następnie wybrać opcję **tak**.

      ![AD_application_permission platformy Azure](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federation Services *nie jest obsługiwana*. Użyj konta zarządzanego za pomocą [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="download-the-template"></a>Pobieranie szablonu

1. W magazynie przejdź do pozycji **Przygotowanie infrastruktury**  >  **Źródło**.
2. W obszarze **Przygotowywanie źródła** wybierz pozycję **+Serwer konfiguracji**.
3. W obszarze **Dodawanie serwera** sprawdź, czy w sekcji **Typ serwera** jest widoczna pozycja **Serwer konfiguracji dla oprogramowania VMware**.
4. Pobierz szablon komórki jajowe dla serwera konfiguracji.

   > [!TIP]
   >Możesz również pobrać najnowszą wersję szablonu serwera konfiguracji bezpośrednio z [Centrum pobierania Microsoft](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> Licencja udostępniona z szablonem komórki jajowe to licencja ewaluacyjna, która jest ważna przez 180 dni. Po upływie tego czasu musisz uzyskać licencję.

## <a name="import-the-template-in-vmware"></a>Importowanie szablonu do programu VMware

1. Zaloguj się do serwera VMware vCenter lub hosta vSphere ESXi przy użyciu klienta VMware vSphere.
2. W menu **plik** wybierz polecenie **Wdróż szablon OVF** , aby uruchomić kreatora **wdrażania szablonu OVF** .

     ![Wdróż szablon OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Na stronie **Select source** (Wybierz źródło) wprowadź lokalizację pobranego szablonu OVF.
4. Na stronie **Review details** (Przejrzyj szczegóły) wybierz pozycję **Next** (Dalej).
5. Na stronach **Select name and folder** (Wybierz nazwę i folder) oraz **Select configuration** (Wybierz konfigurację) zaakceptuj ustawienia domyślne.
6. Aby uzyskać najlepszą wydajność, na stronie **Select storage** (Wybierz magazyn) wybierz pozycję **Thick Provision Eager Zeroed** w sekcji **Select virtual disk format** (Wybierz format dysku wirtualnego). Użycie opcji alokowania elastycznego może wpłynąć na wydajność serwera konfiguracji.
7. Na pozostałych stronach kreatora zaakceptuj ustawienia domyślne.
8. Na stronie **Ready to complete** (Gotowe do ukończenia):

    * Aby skonfigurować maszynę wirtualną przy użyciu ustawień domyślnych, wybierz pozycję **Włącz po**  >  **zakończeniu** wdrożenia.
    * Aby dodać dodatkowy interfejs sieciowy, wyczyść pole wyboru **Włącz po wdrożeniu**, a następnie wybierz pozycję **Zakończ**. Domyślnie szablon serwera konfiguracji jest wdrażany z jedną kartą sieciową. Kolejne karty sieciowe można dodać po wdrożeniu.

> [!IMPORTANT]
> Nie należy zmieniać konfiguracji zasobów, takich jak pamięć, rdzenie i ograniczenia procesora CPU, ani modyfikować ani usuwać zainstalowanych usług lub plików na serwerze konfiguracji po wdrożeniu. Te typy zmian wpływają na rejestrację serwera konfiguracji przy użyciu usług platformy Azure i wydajności serwera konfiguracji.

## <a name="add-an-additional-adapter"></a>Dodawanie karty sieciowej

> [!NOTE]
> Dwie karty sieciowe są wymagane, jeśli planujesz zachować adresy IP maszyn źródłowych w trybie failover, a później chcesz wrócić do trybu lokalnego. Jedna karta sieciowa jest połączona z maszynami źródłowymi, a druga karta sieciowa jest używana na potrzeby łączności z platformą Azure.

Jeśli chcesz dodać dodatkową kartę sieciową do serwera konfiguracji, Dodaj ją przed zarejestrowaniem serwera w magazynie. Po zarejestrowaniu nie można dodawać kart sieciowych.

1. Kliknij prawym przyciskiem myszy maszynę wirtualną na liście w kliencie vSphere, a następnie wybierz pozycję **Edytuj ustawienia**.
2. Na stronie **Hardware** (Sprzęt) wybierz pozycje **Add** > **Ethernet Adapter** (Dodaj, Karta Ethernet). Następnie wybierz przycisk **Dalej**.
3. Wybierz typ karty i sieć.
4. Aby połączyć wirtualną kartę sieciową po włączeniu maszyny wirtualnej, wybierz pozycję **Połącz przy zasilaniu**. Następnie wybierz pozycję **dalej**  >  **Zakończ**  >  .

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Rejestrowanie serwera konfiguracji przy użyciu usług Azure Site Recovery Services

1. W konsoli klienta VMware vSphere Włącz maszynę wirtualną.
2. Maszyna wirtualna zostanie uruchomiona do środowiska instalacji systemu Windows Server 2016. Zaakceptuj umowę licencyjną i wprowadź hasło administratora.
3. Po zakończeniu instalacji zaloguj się na maszynie wirtualnej jako administrator.
4. Przy pierwszym logowaniu w ciągu kilku sekund zostanie uruchomione narzędzie do konfiguracji Azure Site Recovery.
5. Wprowadź nazwę używaną do zarejestrowania serwera konfiguracji w usłudze Site Recovery. Następnie wybierz przycisk **Dalej**.
6. Narzędzie sprawdza, czy maszyna wirtualna może połączyć się z platformą Azure. Po nawiązaniu połączenia wybierz pozycję **Zaloguj się**, aby zalogować się do subskrypcji platformy Azure.</br>
    a. Użyte poświadczenia muszą zapewniać dostęp do magazynu, w którym chcesz zarejestrować serwer konfiguracji.</br>
    b. Upewnij się, że wybrane konto użytkownika ma uprawnienia do tworzenia aplikacji na platformie Azure. Aby włączyć wymagane uprawnienia, postępuj zgodnie z zaleceniami w sekcji [Azure Active Directory wymagania dotyczące uprawnień](#azure-active-directory-permission-requirements).
7. Narzędzie wykonuje pewne zadania konfiguracyjne, a następnie wywołuje ponowne uruchomienie.
8. Ponownie zaloguj się do maszyny. Kreator zarządzania serwerem konfiguracji jest uruchamiany automatycznie w ciągu kilku sekund.

### <a name="configure-settings"></a>Konfigurowanie ustawień

1. W Kreatorze zarządzania serwerem konfiguracji wybierz pozycję **Instalacja łączność**. W polu rozwijanym najpierw wybierz kartę sieciową, która jest wykorzystywana przez serwer przetwarzania wbudowanego do odnajdywania i wypychania instalacji usługi mobilności na maszynach źródłowych. Następnie wybierz kartę sieciową używaną przez serwer konfiguracji do łączności z platformą Azure. Wybierz pozycję **Zapisz**. Po skonfigurowaniu tego ustawienia nie można go zmienić. Nie zmieniaj adresu IP serwera konfiguracji. Upewnij się, że adres IP przypisany do serwera konfiguracji to statyczny adres IP, a nie adres IP serwera DHCP.
2. Na stronie **Wybierz magazyn Recovery Services** Zaloguj się do Microsoft Azure przy użyciu poświadczeń użytych w kroku 6 [rejestracji serwera konfiguracji z usługami Azure Site Recovery Services](#register-the-configuration-server-with-azure-site-recovery-services).
3. Po zalogowaniu wybierz swoją subskrypcję platformy Azure i odpowiednią grupę zasobów i magazyn.

    > [!NOTE]
    > Po rejestracji nie ma możliwości zmiany magazynu usługi Recovery Services.
    > Zmiana magazynu usługi Recovery Services wymaga usunięcia skojarzenia z serwerem konfiguracji z bieżącego magazynu, a replikacja wszystkich chronionych maszyn wirtualnych na serwerze konfiguracji jest zatrzymana. Aby dowiedzieć się więcej, zobacz [Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii maszyny wirtualnej VMware](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. Na stronie **Instalowanie oprogramowania innych** firm:

    |Scenariusz   |Wymagane kroki  |
    |---------|---------|
    |Czy można pobrać i zainstalować MySQL ręcznie?     |  Tak. Pobierz aplikację MySQL, umieść ją w folderze **C:\Temp\ASRSetup**, a następnie zainstaluj ręcznie. Po zaakceptowaniu warunków i wybraniu opcji **Pobierz i zainstaluj** Portal jest *już zainstalowany*. Możesz przejść do następnego kroku.       |
    |Czy mogę uniknąć pobierania programu MySQL online?     |   Tak. Umieść aplikację instalatora MySQL w folderze **C:\Temp\ASRSetup**. Zaakceptuj warunki, wybierz pozycję **Pobierz i zainstaluj**, a Portal użyje Instalatora dodanego w celu zainstalowania aplikacji. Po zakończeniu instalacji przejdź do następnego kroku.    |
    |Chcę pobrać i zainstalować MySQL za Azure Site Recovery.    |  Zaakceptuj umowę licencyjną, a następnie wybierz pozycję **Pobierz i zainstaluj**. Po zakończeniu instalacji przejdź do następnego kroku.       |

5. Przed kontynuowaniem **Sprawdź poprawność konfiguracji urządzenia**. wymagania wstępne są weryfikowane.
6. Na stronie **Konfigurowanie serwera vCenter Server/VSphere ESXi** wprowadź nazwę FQDN lub adres IP serwera vCenter lub hosta vSphere, na którym znajdują się maszyny wirtualne, które chcesz replikować. Wprowadź port, na którym nasłuchuje serwer. Wprowadź przyjazną nazwę, która ma być używana dla serwera VMware w magazynie.
7. Wprowadź poświadczenia, za pomocą których serwer konfiguracji będzie łączył się z serwerem VMware. Przy użyciu tych poświadczeń usługa Site Recovery automatycznie odnajduje maszyny wirtualne VMware dostępne do replikacji. Wybierz pozycję **Dodaj**  >  **Kontynuuj**. Podane tutaj poświadczenia zostały zapisane lokalnie.
8. W obszarze **Skonfiguruj poświadczenia maszyny wirtualnej** wprowadź nazwę użytkownika i hasło maszyn wirtualnych, aby automatycznie zainstalować usługę mobilności podczas replikacji. W przypadku maszyn z **systemem Windows** konto musi mieć uprawnienia administratora lokalnego na maszynach, które mają być replikowane. W przypadku systemu **Linux** Podaj szczegóły konta głównego.
9. Aby ukończyć rejestrację, wybierz pozycję **Zakończ konfigurację**.
10. Po zakończeniu rejestracji Otwórz Azure Portal i sprawdź, czy serwer konfiguracji i serwer VMware znajdują się na liście **Recovery Services magazynie**  >  **Zarządzaj**  >    >  **serwerami konfiguracji** infrastruktury Site Recovery.

## <a name="upgrade-the-configuration-server"></a>Uaktualnij serwer konfiguracji

Aby uaktualnić serwer konfiguracji do najnowszej wersji, zobacz [Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii maszyny wirtualnej VMware](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Aby uzyskać instrukcje dotyczące uaktualniania wszystkich składników Site Recovery, zobacz [aktualizacje usługi w programie Site Recovery](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Zarządzanie serwerem konfiguracji

Aby uniknąć przerw w trwającej replikacji, należy się upewnić, że adres IP serwera konfiguracji nie zmienia się po zarejestrowaniu serwera konfiguracji w magazynie. Aby dowiedzieć się więcej na temat typowych zadań zarządzania serwerem konfiguracji, zobacz [Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii maszyny wirtualnej VMware](vmware-azure-manage-configuration-server.md).

## <a name="troubleshoot-deployment-issues"></a>Rozwiązywanie problemów dotyczących wdrożenia

Zapoznaj się z naszym [artykułem rozwiązywania problemów](vmware-azure-troubleshoot-configuration-server.md) , aby rozwiązać problemy z łącznością &.

## <a name="faqs"></a>Często zadawane pytania

* Jak długo licencja podana na serwerze konfiguracji została wdrożona za pomocą OVF? Co się stanie, jeśli nie ponownie aktywuję licencji?

    Licencja udostępniona z szablonem komórki jajowe to licencja ewaluacyjna ważna przez 180 dni. Przed wygaśnięciem należy aktywować licencję. W przeciwnym razie może to powodować częste zamykanie serwera konfiguracji i powodować przeszkodę w działaniach związanych z replikacją. Aby uzyskać więcej informacji, zobacz [Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii maszyny wirtualnej VMware](vmware-azure-manage-configuration-server.md#update-windows-license).

* Czy mogę użyć maszyny wirtualnej, na której jest zainstalowany serwer konfiguracji w różnych celach?

    Nie. Użyj maszyny wirtualnej wyłącznie do celów serwera konfiguracji. Upewnij się, że wszystkie specyfikacje wymienione w [wymaganiach wstępnych](#prerequisites) są zgodne z efektywnym zarządzaniem odzyskiwaniem po awarii.
* Czy mogę przełączyć magazyn już zarejestrowany na serwerze konfiguracji przy użyciu nowo utworzonego magazynu?

    Nie. Po zarejestrowaniu magazynu na serwerze konfiguracji nie można go zmienić.
* Czy można używać tego samego serwera konfiguracji do ochrony maszyn fizycznych i wirtualnych?

    Tak. Ten sam serwer konfiguracji może służyć do replikowania maszyn fizycznych i wirtualnych. Jednak maszyna fizyczna może się nie powracać do maszyny wirtualnej VMware.
* Co to jest cel serwera konfiguracji i gdzie jest używany?

    Aby dowiedzieć się więcej o serwerze konfiguracji i jego funkcjach, zobacz [Architektura replikacji oprogramowania VMware do platformy Azure](vmware-azure-architecture.md).
* Gdzie mogę znaleźć najnowszą wersję serwera konfiguracji?

    Aby uzyskać instrukcje dotyczące uaktualniania serwera konfiguracji za pomocą portalu, zobacz [uaktualnianie serwera konfiguracji](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Aby uzyskać instrukcje dotyczące uaktualniania wszystkich składników Site Recovery, zobacz [aktualizacje usługi w programie Site Recovery](./service-updates-how-to.md).
* Gdzie można pobrać hasło dla serwera konfiguracji?

    Aby pobrać hasło, zobacz [Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii maszyny wirtualnej VMware](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* Czy mogę zmienić hasło?

    Nie. Nie zmieniaj hasła serwera konfiguracji. Zmiana hasła przerywa replikację chronionych maszyn i prowadzi do krytycznego stanu kondycji.
* Gdzie można pobrać klucze rejestracji magazynu?

    W obszarze **Magazyn Recovery Services** wybierz pozycję **Zarządzaj**  >  **Site Recovery**  >  **konfiguracjami serwerów** infrastruktury. W obszarze **serwery** wybierz pozycję **Pobierz klucz rejestracji** , aby pobrać plik poświadczeń magazynu.
* Czy można sklonować istniejący serwer konfiguracji i używać go do organizowania replikacji?

    Nie. Użycie sklonowanego składnika serwera konfiguracji nie jest obsługiwane. Klonowanie serwera przetwarzania skalowalnego w poziomie jest również nieobsługiwanym scenariuszem. Klonowanie składników Site Recovery ma wpływ na trwającą replikację.

* Czy mogę zmienić adres IP serwera konfiguracji?

    Nie. Nie zmieniaj adresu IP serwera konfiguracji. Upewnij się, że wszystkie adresy IP przypisane do serwera konfiguracji to statyczne adresy IP, a nie adresy IP DHCP.
* Czy mogę skonfigurować serwer konfiguracji na platformie Azure?

    Skonfiguruj serwer konfiguracji w środowisku lokalnym, korzystając z bezpośredniego wglądu w dane za pomocą programu v-Center i minimalizując opóźnienia transferu danych. Zaplanowane kopie zapasowe serwera konfiguracji można wykonać na potrzeby [powrotu po awarii](vmware-azure-manage-configuration-server.md#failback-requirements).

* Czy mogę zmienić sterownik pamięci podręcznej na serwerze konfiguracji lub skalowalnym w poziomie serwerze przetwarzania?

    Nie, nie można zmienić sterownika pamięci podręcznej po zakończeniu konfiguracji.

Aby uzyskać więcej często zadawanych pytań na temat serwerów konfiguracji, zobacz [często zadawane pytania dotyczące serwera konfiguracji](vmware-azure-common-questions.md#configuration-server).

## <a name="next-steps"></a>Następne kroki

Skonfiguruj odzyskiwanie po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.