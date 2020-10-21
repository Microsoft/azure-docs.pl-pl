---
title: Rozwiązywanie problemów z replikacją w przypadku migracji maszyn wirtualnych VMware bez agentów
description: Uzyskaj pomoc dotyczącą błędów cyklu replikacji
author: anvar-ms
ms.manager: bsiva
ms.author: anvar
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 2b653a0abbe89686c764a6a0885720cc746975c8
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314727"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>Rozwiązywanie problemów z replikacją w przypadku migracji maszyn wirtualnych VMware bez agentów

W tym artykule opisano niektóre typowe problemy i konkretne błędy, które mogą wystąpić w przypadku replikowania lokalnych maszyn wirtualnych VMware Azure Migrate przy użyciu metody bezagent migracji serwera.

Podczas replikowania maszyny wirtualnej VMware przy użyciu metody replikacji bez wykorzystania agentów dane z maszyny wirtualnej&#39;dysków (VMDK) są replikowane do dysków zarządzanych repliki w ramach subskrypcji platformy Azure. Gdy replikacja jest uruchamiana dla maszyny wirtualnej, cykl replikacji początkowej odbywa się w przypadku replikowania pełnych kopii dysków. Po zakończeniu replikacji początkowej cykle przyrostowej replikacji są planowane okresowo w celu przetransferowania wszelkich zmian, które wystąpiły od czasu poprzedniego cyklu replikacji.

Czasami może się okazać, że cykle replikacji kończy się niepowodzeniem dla maszyny wirtualnej. Te błędy mogą wystąpić z powodu problemów z konfiguracją sieci lokalnej w celu wypróbowania w Azure Migrate zaplecza usługi w chmurze. W tym artykule będziemy:

 - Pokazuje, jak można monitorować stan replikacji i rozwiązywać błędy.
 - Wyświetl listę niektórych często występujących błędów replikacji i Zaproponuj dodatkowe kroki, aby je skorygować.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Monitoruj stan replikacji za pomocą Azure Portal

Wykonaj następujące kroki, aby monitorować stan replikacji dla maszyn wirtualnych:

  1. Przejdź do strony serwery w Azure Migrate na Azure Portal.
  2. Przejdź do strony "replikowanie maszyn", klikając pozycję "replikowanie serwerów" na kafelku Migracja serwera.
  3. Zostanie wyświetlona lista serwerów replikowanych wraz z dodatkowymi informacjami, takimi jak stan, kondycja, czas ostatniej synchronizacji itd. Kolumna kondycja wskazuje bieżącą kondycję replikacji maszyny wirtualnej. Wartość "krytyczna" lub "ostrzeżenie" w kolumnie kondycji zwykle wskazuje, że poprzedni cykl replikacji maszyny wirtualnej nie powiódł się. Aby uzyskać więcej szczegółów, kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie wybierz pozycję "Szczegóły błędu". Strona szczegóły błędu zawiera informacje na temat błędu i dodatkowe informacje dotyczące rozwiązywania problemów. Zobaczysz również link "ostatnie zdarzenia", którego można użyć, aby przejść do strony zdarzenia dla maszyny wirtualnej.
  4. Kliknij pozycję "ostatnie zdarzenia", aby zobaczyć poprzednie błędy cyklu replikacji dla maszyny wirtualnej. Na stronie zdarzenia Znajdź najnowsze zdarzenie typu "cykl replikacji nie powiodło się" lub "cykl replikacji nie powiódł się dla dysku" dla maszyny wirtualnej.
  5. Kliknij zdarzenie, aby zrozumieć możliwe przyczyny błędu i zalecane czynności zaradcze. Skorzystaj z informacji podanych w celu rozwiązania problemu i skorygowania błędu.
    
## <a name="common-replication-errors"></a>Typowe błędy replikacji

W tej sekcji opisano niektóre typowe błędy i sposoby ich rozwiązywania.

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>Wystąpił błąd operacji Key Vault podczas próby replikowania maszyn wirtualnych

**Błąd:** "Key Vault operacji nie powiodła się. Operacja: Konfigurowanie zarządzanego konta magazynu, Key Vault: nazwa magazynu kluczy, konto magazynu: nazwa konta magazynu nie powiodła się z powodu błędu: "

**Błąd:** "Key Vault operacji nie powiodła się. Operacja: generowanie definicji sygnatury dostępu współdzielonego, Key Vault: nazwa magazynu kluczy, konto magazynu: nazwa konta magazynu nie powiodła się z powodu błędu: "

![Usługa Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

Ten błąd zazwyczaj występuje, ponieważ zasady dostępu użytkownika dla Key Vault nie dają obecnie zalogowanemu użytkownikowi uprawnień niezbędnych do skonfigurowania kont magazynu, które mają być Key Vault zarządzane. Aby sprawdzić zasady dostępu użytkowników w magazynie kluczy, przejdź do strony Magazyn kluczy w portalu dla magazynu kluczy i wybierz pozycję Zasady dostępu 

Gdy w portalu zostanie utworzony magazyn kluczy, dodaje także zasady dostępu użytkownika udzielające aktualnie zalogowanych uprawnień użytkowników w celu skonfigurowania kont magazynu, które mają być Key Vault zarządzane. Może to się nie powieść z dwóch powodów

- Zalogowany użytkownik jest obiektem zdalnym w dzierżawie platformy Azure dla klientów (subskrypcja CSP — a zalogowany użytkownik jest administratorem partnera). Obejście w tym przypadku polega na usunięciu magazynu kluczy, wylogowaniu z portalu, a następnie zalogowaniu się przy użyciu konta użytkownika z dzierżawy klientów (a nie z podmiotem zdalnego), a następnie ponów próbę wykonania operacji. Partner programu CSP zazwyczaj będzie miał konto użytkownika w ramach klientów Azure Active Directory dzierżawy, których mogą używać. Jeśli nie można utworzyć nowego konta użytkownika dla siebie w dzierżawie klientów Azure Active Directory, zaloguj się do portalu jako nowy użytkownik, a następnie ponów operację replikacji. Używane konto musi mieć uprawnienia administratora lub współautora i dostępu użytkownika przyznane do konta w grupie zasobów (Migrowanie grupy zasobów projektu)

- W przeciwnym razie może się zdarzyć, że jeden użytkownik (Użytkownik1) próbował skonfigurować replikację początkowo i napotkał błąd, ale Magazyn kluczy został już utworzony (a zasady dostępu użytkownika zostały odpowiednio przypisane do tego użytkownika). Teraz w późniejszym momencie inny użytkownik (do) próbuje skonfigurować replikację, ale operacja Konfiguruj zarządzane konto magazynu lub Generuj definicję SAS nie powiedzie się, ponieważ w magazynie kluczy nie ma zasad dostępu do zasobów.

**Rozwiązanie**: Aby obejść ten problem, należy utworzyć zasady dostępu użytkownika dla konta w magazynie kluczy Granted, aby skonfigurować zarządzane konto magazynu i generować definicje SAS. Wartość można wykonać w Azure PowerShell przy użyciu poniższych poleceń cmdlet:

$userPrincipalId = $ (Get-AzureRmADUser-UserPrincipalName "user2_email_address"). #C1

Set-AzureRmKeyVaultAccessPolicy-Magazynname "PermissionsToStorage"-ObjectId $userPrincipalId-"Get, list, DELETE, Set, Update, regeneratekey, getsas, listsas, deletesas, setsas, Recover, Backup, Restore, przeczyszczanie


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**Identyfikator błędu:** 181008

**Komunikat o błędzie:** Maszyna wirtualna: VMName. Błąd: Napotkano zdarzenie limitu czasu "DisposeArtefactsTimeout" w stanie & "[" Gateway. Service. StateMachine. SnapshotReplication. SnapshotReplicationEngine + WaitingForArtefactsDisposalPreCycle "(" WaitingForArtefactsDisposalPreCycle ")]".

**Możliwe przyczyny:**

Składnik próbujący zreplikować dane na platformę Azure jest wyłączony lub nie odpowiada. Możliwe przyczyny:

- Usługa bramy uruchomiona na urządzeniu Azure Migrate nie działa.
- W usłudze Gateway występują problemy z łącznością z kontem magazynu Service Bus/centrum zdarzeń/urządzenia.

**Zidentyfikowanie dokładnej przyczyny dla DisposeArtefactsTimedOut i odpowiedniego rozwiązania:**

1. Upewnij się, że urządzenie usługi Azure Migrate jest uruchomione.
2. Sprawdź, czy Usługa bramy jest uruchomiona na urządzeniu:
   1.  Zaloguj się do urządzenia Azure Migrate przy użyciu pulpitu zdalnego i wykonaj następujące czynności.

   2.  Otwórz przystawkę MMC usług firmy Microsoft (Uruchom > Services. msc) i sprawdź, czy jest uruchomiona usługa Microsoft Azure Gateway Service. Jeśli usługa jest zatrzymana lub nie jest uruchomiona, uruchom usługę. Alternatywnie możesz otworzyć wiersz polecenia lub program PowerShell i wykonać następujące czynności: "net start asrgwy"

3. Sprawdź, czy występują problemy z łącznością między urządzeniem Azure Migrate a kontem magazynu urządzenia: 

    Po pobraniu AzCopy na urządzeniu Azure Migrate Uruchom następujące polecenie:
    
    _AzCopy — https://[Account]. blob. Core. Windows. NET/[Container]? SYGNATUR_
    
    **Kroki prowadzące do uruchomienia testu porównawczego wydajności:**
    
      1. [Pobierz](../storage/common/storage-use-azcopy-v10.md) AzCopy
        
      2. Poszukaj konta magazynu urządzenia w grupie zasobów. Konto magazynu ma nazwę podobną do migrategwsa \* \* \* \* \* \* \* \* \* \* . Jest to wartość parametru [Account] w powyższym poleceniu.
        
      3. Wyszukaj swoje konto magazynu w Azure Portal. Upewnij się, że subskrypcja używana do wyszukiwania jest tą samą subskrypcją (subskrypcją docelową), w której utworzono konto magazynu. Przejdź do kontenerów w sekcji usługi BLOB Service. Kliknij pozycję + kontener i Utwórz kontener. Pozostaw publiczny poziom dostępu do domyślnej zaznaczonej wartości.
        
      4. Przejdź do pozycji sygnatura dostępu współdzielonego w obszarze Ustawienia. Wybierz kontener w obszarze "dozwolone typy zasobów". Kliknij pozycję Generuj sygnaturę dostępu współdzielonego i parametry połączenia. Skopiuj wartość sygnatury dostępu współdzielonego.
        
      5. Wykonaj powyższe polecenie w wierszu polecenia, zastępując konto, kontener, sygnaturę dostępu współdzielonego wartościami uzyskanymi w krokach 2, 3 i 4.
        
      Alternatywnie możesz [pobrać](https://go.microsoft.com/fwlink/?linkid=2138967) usługę Azure Storage na urządzenie i spróbować przekazać 10 obiektów BLOB o wartości ~ 64 MB do kont magazynu. Jeśli nie ma problemu, przekazywanie powinno zakończyć się pomyślnie.
        
    **Rozwiązanie:** Jeśli ten test zakończy się niepowodzeniem,&#39;s problem z siecią. Skontaktuj się z lokalnym zespołem sieci, aby sprawdzić problemy z łącznością. Zazwyczaj może istnieć kilka ustawień zapory, które powodują błędy.
    
4.  Sprawdź, czy występują problemy z łącznością między urządzeniem Azure Migrate i Service Bus:

    Ten test sprawdza, czy urządzenie Azure Migrate może komunikować się z Azure Migrate zaplecza usługi w chmurze. Urządzenie komunikuje się z zapleczem usługi za pomocą kolejek komunikatów Service Bus i centrum zdarzeń. Aby sprawdzić poprawność łączności z urządzenia do Service Bus, [Pobierz](https://go.microsoft.com/fwlink/?linkid=2139104) Eksploratora Service Bus, spróbuj nawiązać połączenie z urządzeniem Service Bus i wykonaj polecenie Wyślij komunikat/Odbierz wiadomość. Jeśli nie ma problemu, powinien on zostać pomyślnie zakończony.

    **Kroki prowadzące do uruchomienia testu:**

    1. Kopiuj parametry połączenia z Service Bus, które zostały utworzone w projekcie migracji
    2. Otwórz Eksploratora Service Bus
    3. Przejdź do pliku, a następnie połącz
    4. Wklej parametry połączenia i kliknij przycisk Połącz.
    5. Spowoduje to otwarcie Service Bus przestrzeni nazw
    6. Wybierz Snapshot Manager w temacie. Kliknij prawym przyciskiem myszy Snapshot Manager, wybierz pozycję "Odbierz komunikaty" > wybierz opcję "wgląd", a następnie kliknij przycisk OK.
    7. Jeśli połączenie zostanie nawiązane, zobaczysz komunikat "[x] odebrane" w danych wyjściowych konsoli. Jeśli połączenie nie powiedzie się, zostanie wyświetlony komunikat z informacją, że połączenie nie powiodło się
    
    **Rozwiązanie:** Jeśli ten test zakończy się niepowodzeniem, wystąpił problem z siecią. Skontaktuj się z lokalnym zespołem sieci, aby sprawdzić problemy z łącznością. Zazwyczaj może istnieć kilka ustawień zapory, które powodują błędy.

5. Problemy z łącznością między urządzeniem Azure Migrate i Azure Key Vault:

    Ten test sprawdza, czy występują problemy z łącznością między urządzeniem Azure Migrate i Azure Key Vault. Key Vault służy do zarządzania dostępem do konta magazynu używanego na potrzeby replikacji.
    
    **Kroki umożliwiające sprawdzenie łączności:**
    
    1. Pobierz identyfikator URI Key Vault z listy zasobów w grupie zasobów odpowiadającej Azure Migrate Project.
    
    1. Otwórz program PowerShell na urządzeniu Azure Migrate i uruchom następujące polecenie:
    
    _Test-NetConnection Key Vault URI-P 443_
    
    To polecenie próbuje nawiązać połączenie TCP i zwróci wynik.
    
     - W danych wyjściowych zaznacz pole "_TcpTestSucceeded_". Jeśli wartość jest_równa "true_", nie występuje problem z łącznością między urządzeniem Azure Migrate i Azure Key Vault. Jeśli wartość jest równa "false", występuje problem z łącznością.
    
    **Rozwiązanie:** Jeśli ten test zakończy się niepowodzeniem, wystąpił problem z łącznością między urządzeniem Azure Migrate i Azure Key Vault. Skontaktuj się z lokalnym zespołem sieci, aby sprawdzić problemy z łącznością. Zazwyczaj może istnieć kilka ustawień zapory, które powodują błędy.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**Identyfikator błędu:** 1011

**Komunikat o błędzie:** Przekazywanie danych dla DiskPath dysku, DiskId maszyny wirtualnej VMName; Identyfikator maszyny wirtualnej nie został ukończony w oczekiwanym czasie.

Ten błąd zazwyczaj wskazuje, że urządzenie Azure Migrate wykonujące replikację nie może nawiązać połączenia z usługą Azure Cloud Services lub replikacja postępuje powoli z powodu przekroczenia limitu czasu cyklu replikacji.

Możliwe przyczyny:

- Urządzenie Azure Migrate nie działa.
- Usługa bramy replikacji na urządzeniu nie jest uruchomiona.
- W usłudze bramy replikacji występują problemy z łącznością z jednym z następujących składników usługi platformy Azure, które są używane na potrzeby replikacji: Service Bus/centrum zdarzeń/konto magazynu usługi Azure cache/Azure Key Vault.
- Usługa bramy jest ograniczana na poziomie programu vCenter podczas próby odczytu dysku.

**Identyfikowanie głównej przyczyny i rozwiązywanie problemu:**

1. Upewnij się, że urządzenie usługi Azure Migrate jest uruchomione.
2. Sprawdź, czy Usługa bramy jest uruchomiona na urządzeniu:
   1.  Zaloguj się do urządzenia Azure Migrate przy użyciu pulpitu zdalnego i wykonaj następujące czynności.

   2.  Otwórz przystawkę MMC usług firmy Microsoft (Uruchom > Services. msc) i sprawdź, czy jest uruchomiona usługa Microsoft Azure Gateway Service. Jeśli usługa jest zatrzymana lub nie jest uruchomiona, uruchom usługę. Alternatywnie możesz otworzyć wiersz polecenia lub program PowerShell i wykonać następujące czynności: "net start asrgwy".


3. **Sprawdź, czy występują problemy z łącznością między urządzeniem Azure Migrate a kontem magazynu pamięci podręcznej:** 

    Po pobraniu AzCopy na urządzeniu Azure Migrate Uruchom następujące polecenie:
    
    _AzCopy — https://[Account]. blob. Core. Windows. NET/[Container]? SYGNATUR_
    
    **Kroki prowadzące do uruchomienia testu porównawczego wydajności:**
    
      1. [Pobierz](../storage/common/storage-use-azcopy-v10.md) AzCopy
        
      2. Poszukaj konta magazynu urządzenia w grupie zasobów. Konto magazynu ma nazwę podobną do migratelsa \* \* \* \* \* \* \* \* \* \* . Jest to wartość parametru [Account] w powyższym poleceniu.
        
      3. Wyszukaj swoje konto magazynu w Azure Portal. Upewnij się, że subskrypcja używana do wyszukiwania jest tą samą subskrypcją (subskrypcją docelową), w której utworzono konto magazynu. Przejdź do kontenerów w sekcji usługi BLOB Service. Kliknij pozycję + kontener i Utwórz kontener. Pozostaw publiczny poziom dostępu do domyślnej zaznaczonej wartości.
        
      4. Przejdź do pozycji sygnatura dostępu współdzielonego w obszarze Ustawienia. Wybierz kontener w obszarze "dozwolone typy zasobów". Kliknij pozycję Generuj sygnaturę dostępu współdzielonego i parametry połączenia. Skopiuj wartość sygnatury dostępu współdzielonego.
        
      5. Wykonaj powyższe polecenie w wierszu polecenia, zastępując konto, kontener, sygnaturę dostępu współdzielonego wartościami uzyskanymi w krokach 2, 3 i 4.
        
      Alternatywnie możesz [pobrać](https://go.microsoft.com/fwlink/?linkid=2138967) usługę Azure Storage na urządzenie i spróbować przekazać 10 obiektów BLOB o wartości ~ 64 MB do kont magazynu. Jeśli nie ma problemu, przekazywanie powinno zakończyć się pomyślnie.
        
    **Rozwiązanie:** Jeśli ten test zakończy się niepowodzeniem,&#39;s problem z siecią. Skontaktuj się z lokalnym zespołem sieci, aby sprawdzić problemy z łącznością. Zazwyczaj może istnieć kilka ustawień zapory, które powodują błędy.
                
4.  **Problemy z łącznością między urządzeniem Azure Migrate i Azure Service Bus:**

    Ten test sprawdzi, czy urządzenie Azure Migrate może komunikować się z Azure Migrate zaplecza usługi w chmurze. Urządzenie komunikuje się z zapleczem usługi za pomocą kolejek komunikatów Service Bus i centrum zdarzeń. Aby sprawdzić poprawność łączności z urządzenia do Service Bus, [Pobierz](https://go.microsoft.com/fwlink/?linkid=2139104) Eksploratora Service Bus, spróbuj nawiązać połączenie z urządzeniem Service Bus i wykonaj polecenie Wyślij komunikat/Odbierz wiadomość. Jeśli nie ma problemu, powinien on zostać pomyślnie zakończony.

    **Kroki prowadzące do uruchomienia testu:**
    
    1. Skopiuj parametry połączenia z Service Bus, który został utworzony w grupie zasobów odpowiadającej Azure Migrate projektu
    
    1. Otwórz Eksploratora Service Bus
    
    1. Przejdź do pliku > Connect
    
    1. Wklej parametry połączenia skopiowane w kroku 1, a następnie kliknij przycisk Połącz.
    
    1. Spowoduje to otwarcie Service Bus przestrzeni nazw.
    
    1. Wybierz Snapshot Manager w temacie w przestrzeni nazw. Kliknij prawym przyciskiem myszy Snapshot Manager, wybierz pozycję "Odbierz komunikaty" > wybierz opcję "wgląd", a następnie kliknij przycisk OK.
    
    Jeśli połączenie zostanie nawiązane, zobaczysz komunikat "[x] odebrane" w danych wyjściowych konsoli. Jeśli połączenie nie powiedzie się, zostanie wyświetlony komunikat z informacją, że połączenie nie powiodło się.
    
    **Rozwiązanie:** Jeśli ten test zakończy się niepowodzeniem, wystąpił problem z łącznością między urządzeniem Azure Migrate i Service Bus. Zapoznaj się z lokalnym zespołem sieci, aby sprawdzić te problemy z łącznością. Zazwyczaj może istnieć kilka ustawień zapory, które powodują błędy.
    
 5. **Problemy z łącznością między urządzeniem Azure Migrate i Azure Key Vault:**

    Ten test sprawdza, czy występują problemy z łącznością między urządzeniem Azure Migrate i Azure Key Vault. Key Vault służy do zarządzania dostępem do konta magazynu używanego na potrzeby replikacji.
    
    **Kroki umożliwiające sprawdzenie łączności:**
    
    1. Pobierz identyfikator URI Key Vault z listy zasobów w grupie zasobów odpowiadającej Azure Migrate Project.
    
    1. Otwórz program PowerShell na urządzeniu Azure Migrate i uruchom następujące polecenie:
    
    _Test-NetConnection Key Vault URI-P 443_
    
    To polecenie próbuje nawiązać połączenie TCP i zwróci wynik.
    
    1. W danych wyjściowych zaznacz pole "_TcpTestSucceeded_". Jeśli wartość jest_równa "true_", nie występuje problem z łącznością między urządzeniem Azure Migrate i Azure Key Vault. Jeśli wartość jest równa "false", występuje problem z łącznością.
    
    **Rozwiązanie:** Jeśli ten test zakończy się niepowodzeniem, wystąpił problem z łącznością między urządzeniem Azure Migrate i Azure Key Vault. Skontaktuj się z lokalnym zespołem sieci, aby sprawdzić problemy z łącznością. Zazwyczaj może istnieć kilka ustawień zapory, które powodują błędy.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>Napotkano błąd podczas próby pobrania zmienionych bloków

Komunikat o błędzie: "Napotkano błąd podczas próby pobrania bloków zmiany"

Metoda replikacji bez wykorzystania agentów używa technologii CBT (Data Tracking) do replikowania danych na platformę Azure. CBT umożliwia śledzenie i replikowanie tylko bloków, które uległy zmianie od czasu ostatniego cyklu replikacji. Ten błąd występuje, jeśli śledzenie zmienionych bloków dla replikującej maszyny wirtualnej zostanie zresetowane lub jeśli plik funkcji śledzenia zmienionych bloków jest uszkodzony.

Ten błąd można rozwiązać na dwa sposoby:

- Jeśli wybrano opcję "automatycznie naprawiaj replikację", wybierając opcję "tak" podczas wyzwalania replikacji maszyny wirtualnej, narzędzie spróbuje je naprawić. Kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie wybierz pozycję "Napraw replikację".
- Jeśli nie wybrano opcji "automatycznie naprawiaj replikację" lub powyższy krok nie zadziałało, następnie Zatrzymaj replikację maszyny wirtualnej, [Zresetuj zmieniono śledzenie blokady](https://go.microsoft.com/fwlink/?linkid=2139203) na maszynie wirtualnej, a następnie skonfiguruj ponownie replikację.

Taki znany problem, który może spowodować zresetowanie CBT maszyny wirtualnej w VMware vSphere 5,5 w programie [VMware KB 2048201: Zmiana śledzenia bloków](https://go.microsoft.com/fwlink/?linkid=2138888) jest resetowana po operacji vMotion magazynu w vSphere 5. x. Jeśli korzystasz z programu VMware vSphere 5.5, upewnij się, że zastosowano aktualizacje opisane w tym artykule bazy wiedzy.

Alternatywnie możesz [resetować śledzenie zablokowanych bloków programu VMware na maszynie wirtualnej przy użyciu programu VMware PowerCLI.

## <a name="an-internal-error-occurred"></a>Wystąpił błąd wewnętrzny

Czasami może wystąpić błąd spowodowany problemami w środowisku lub interfejsie API programu VMware. Zidentyfikowano następujący zestaw błędów jako błędy związane ze środowiskiem programu VMware. Te błędy mają stały format.

_Komunikat o błędzie: Wystąpił błąd wewnętrzny. [Komunikat o błędzie]_

Na przykład: komunikat o błędzie: Wystąpił błąd wewnętrzny. [Wykryto nieprawidłową konfigurację migawki].

W poniższej sekcji przedstawiono niektóre często spotykane błędy oprogramowania VMware i sposoby ich ograniczania.

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>Komunikat o błędzie: Wystąpił błąd wewnętrzny. [Serwer odmówił połączenia]

Problem jest znanym problemem VMware i występuje w VDDK 6,7. Należy zatrzymać usługę bramy uruchomioną na urządzeniu Azure Migrate, [pobrać aktualizację z programu VMware KB](https://go.microsoft.com/fwlink/?linkid=2138889)i ponownie uruchomić usługę bramy.

Procedura zatrzymania usługi bramy:

1. Naciśnij klawisze Windows + R, Otwórz Services. msc. Kliknij pozycję "Microsoft Azure usługę bramy" i zatrzymaj ją.
2. Alternatywnie możesz otworzyć wiersz polecenia lub program PowerShell i wykonać następujące czynności: net stop asrgwy. Zaczekaj, aż zostanie wyświetlony komunikat, że usługa nie jest już uruchomiona.

Kroki umożliwiające uruchomienie usługi bramy:

1. Naciśnij klawisze Windows + R, Otwórz Services. msc. Kliknij prawym przyciskiem myszy pozycję "Microsoft Azure usługę bramy" i uruchom ją.
2. Alternatywnie możesz otworzyć wiersz polecenia lub program PowerShell i wykonać następujące czynności: net start asrgwy.

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>Komunikat o błędzie: Wystąpił błąd wewnętrzny. ["Wykryto nieprawidłową konfigurację migawki."]

Jeśli masz maszynę wirtualną z wieloma dyskami, ten błąd może wystąpić w przypadku usunięcia dysku z maszyny wirtualnej. Aby rozwiązać ten problem, zapoznaj się z procedurą opisaną w [tym artykule](https://go.microsoft.com/fwlink/?linkid=2138890)dotyczącym programu VMware.

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>Komunikat o błędzie: Wystąpił błąd wewnętrzny. [Generuj niezawieszoną migawkę]

Ten problem występuje, gdy generacja migawek przestanie odpowiadać. W przypadku wystąpienia tego problemu można zobaczyć, że zadanie tworzenia migawki zostanie zatrzymane o 95% lub 99%. Zapoznaj się z tym oprogramowaniem [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138969) , aby rozwiązać ten problem.

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>Komunikat o błędzie: Wystąpił błąd wewnętrzny. [Nie można skonsolidować dysków w maszynie wirtualnej _[przyczyny]_]

Podczas konsolidowania dysków po zakończeniu cyklu replikacji operacja kończy się niepowodzeniem. Postępuj zgodnie z instrukcjami w [oprogramowaniu VMware KB](https://go.microsoft.com/fwlink/?linkid=2138970) , wybierając odpowiednią _przyczynę_ rozwiązania problemu.

Następujące błędy występują podczas operacji związanych z migawką VMware — tworzenie, usuwanie lub konsolidowanie dysków kończy się niepowodzeniem. Postępuj zgodnie ze wskazówkami w następnej sekcji, aby skorygować błędy:

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>Komunikat o błędzie: Wystąpił błąd wewnętrzny. [Inne zadanie jest już w toku]

Ten problem występuje, gdy w tle są uruchomione zadania maszyny wirtualnej powodujące konflikt lub gdy zadanie w vCenter Server przekracza limit czasu. Postępuj zgodnie z rozwiązaniem podanym w następującej rozdzielczości [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138891).

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>Komunikat o błędzie: Wystąpił błąd wewnętrzny. [Operacja nie jest dozwolona w bieżącym stanie]

Ten problem występuje, gdy vCenter Server agenci zarządzania przestaną działać. Aby rozwiązać ten problem, zapoznaj się z rozwiązaniem w następującej rozdzielczości [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138971).

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>Komunikat o błędzie: Wystąpił błąd wewnętrzny. [Nieprawidłowy rozmiar dysku migawki]

Jest to znany problem dotyczący oprogramowania VMware, w którym rozmiar dysku wskazany przez migawkę zmieni się na zero. Postępuj zgodnie z rozdzielczością podaną w [oprogramowaniu VMware KB](https://go.microsoft.com/fwlink/?linkid=2138972).

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>Komunikat o błędzie: Wystąpił błąd wewnętrzny. [Alokacja pamięci nie powiodła się. Za mało pamięci.]

Dzieje się tak, gdy bufor hosta NFC ma za mało pamięci. Aby rozwiązać ten problem, należy przenieść maszynę wirtualną (COMPUTE vMotion) na innego hosta, który ma bezpłatne zasoby.

## <a name="next-steps"></a>Następne kroki

Kontynuuj replikację maszyny wirtualnej i przeprowadź [migrację testową](./tutorial-migrate-vmware.md#run-a-test-migration).