---
title: Konfigurowanie urządzenia Azure Migrate dla oprogramowania VMware
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate do oceniania i migrowania serwerów w środowisku VMware.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: c2ffa85ed6cb007dd766d4517a86783d21d4913e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110512"
---
# <a name="set-up-an-appliance-for-servers-in-vmware-environment"></a>Konfigurowanie urządzenia dla serwerów w środowisku VMware

Postępuj zgodnie z tym artykułem, aby skonfigurować Azure Migrate urządzenie do oceny Azure Migrate za pomocą narzędzia do [odnajdywania i oceny](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) oraz dla migracji bez agentów przy użyciu narzędzia [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) Tool.

[Urządzenie Azure Migrate](migrate-appliance.md) jest lekkim urządzeniem używanym przez Azure Migrate: odnajdywanie i ocenianie oraz Migracja serwera w celu odnajdywania serwerów z systemem w vCenter Server, wysyłania metadanych konfiguracji serwera i wydajności do platformy Azure oraz do replikacji serwerów za pomocą migracji bez wykorzystania agentów.

Urządzenie można wdrożyć przy użyciu kilku metod:

- Utwórz serwer na vCenter Server przy użyciu pobranego szablonu komórki jajowe. Jest to metoda opisana w tym artykule.
- Skonfiguruj urządzenie na istniejącym serwerze przy użyciu skryptu Instalatora programu PowerShell. [Tej metody](deploy-appliance-script.md) należy użyć, jeśli nie można użyć szablonu komórki jajowe lub jeśli jesteś w Azure Government.

Po utworzeniu urządzenia sprawdź, czy może nawiązać połączenie z Azure Migrate: odnajdywanie i ocenianie, rejestrowanie go w projekcie i Konfigurowanie urządzenia w celu rozpoczęcia odnajdywania.

## <a name="deploy-with-ova"></a>Wdrażanie przy użyciu komórek jajowych

Aby skonfigurować urządzenie przy użyciu szablonu komórki jajowe:

1. Podaj nazwę urządzenia i Wygeneruj klucz projektu w portalu.
1. Pobierz plik szablonu komórki jajowe i zaimportuj go do vCenter Server. Sprawdź, czy komórki jajowe są bezpieczne.
1. Utwórz maszynę wirtualną z urządzeniem z pliku JAJOWE i sprawdź, czy może ona połączyć się z Azure Migrate.
1. Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie przy użyciu klucza projektu.

### <a name="1-generate-the-project-key"></a>1. Generowanie klucza projektu

1. W obszarze serwery **celów migracji**  >    >  **Azure Migrate: odnajdywanie i ocenianie** wybierz pozycję **odkryj**.
2. W obszarze **odnajdowanie serwerów**  >  **są zwirtualizowane serwery?** wybierz pozycję **tak, aby uzyskać VMware vSphere funkcji hypervisor**.
3. W obszarze **1: generowanie klucza projektu** Podaj nazwę urządzenia Azure Migrate, które zostanie skonfigurowane do odnajdywania serwerów w środowisku programu VMware. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
1. Kliknij pozycję **Generuj klucz** , aby rozpocząć tworzenie wymaganych zasobów platformy Azure. Nie zamykaj strony odnajdywania podczas tworzenia zasobów.
1. Po pomyślnym utworzeniu zasobów platformy Azure zostanie wygenerowany klucz projektu * *.
1. Skopiuj klucz, ponieważ będzie on potrzebny do ukończenia rejestracji urządzenia podczas jego konfiguracji.

### <a name="2-download-the-ova-template"></a>2. Pobierz szablon komórki jajowe

W **2: Pobierz urządzenie Azure Migrate**, wybierz opcję. Plik komórki jajowe i kliknij pozycję **Pobierz**.

### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy plik komórki jajowe jest bezpieczny:

1. Na serwerze, do którego pobrano plik, Otwórz okno polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku komórki jajowe:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Przykład użycia: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Sprawdź najnowsze wersje urządzeń i wartości skrótu:

    - W przypadku chmury publicznej platformy Azure:
    
        **Algorytm** | **Pobieranie** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74


### <a name="3-create-the-appliance-server"></a>3. Utwórz serwer urządzenia

Zaimportuj pobrany plik i Utwórz serwer w środowisku VMware

1. W konsoli klienta vSphere kliknij pozycję **plik**  >  **Wdróż OVF szablon**.
2. W Kreatorze wdrażania szablonu OVF > **Źródło** Określ lokalizację pliku komórek jajowych.
3. W polu **Nazwa** i **Lokalizacja** Określ przyjazną nazwę serwera. Wybierz obiekt spisu, w którym będzie hostowany serwer.
5. W obszarze **host/klaster** Określ hosta lub klaster, na którym będzie uruchamiany serwer programu.
6. W obszarze **Magazyn** określ miejsce docelowe magazynu dla serwera programu.
7. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
8. W polu **mapowanie sieci** Określ sieć, z którą zostanie nawiązane połączenie serwer. Sieć wymaga łączności z Internetem, aby można było wysyłać metadane do Azure Migrate.
9. Sprawdź poprawność ustawień, a następnie kliknij pozycję **Finish** (Zakończ).


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że serwer urządzenia może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .


### <a name="4-configure-the-appliance"></a>4. Skonfiguruj urządzenie

Skonfiguruj urządzenie po raz pierwszy.

> [!NOTE]
> Jeśli urządzenie zostanie skonfigurowane przy użyciu [**skryptu programu PowerShell**](deploy-appliance-script.md) zamiast pobranych komórek jajowych, pierwsze dwa kroki tej procedury nie są istotne.

1. W konsoli klienta vSphere kliknij prawym przyciskiem myszy serwer, a następnie wybierz polecenie **Otwórz konsolę**.
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę na dowolnym serwerze, który może nawiązać połączenie z serwerem urządzeń, a następnie otwórz adres URL menedżera konfiguracji urządzenia: `https://appliance name or IP address: 44368` .

   Alternatywnie można otworzyć program Configuration Manager z poziomu pulpitu serwera urządzeń, wybierając skrót do programu Configuration Manager.
1. Zaakceptuj **postanowienia licencyjne** i przeczytaj informacje o innych firmach.
1. W programie Configuration Manager > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
   - **Łączność**: Urządzenie sprawdza, czy serwer ma dostęp do Internetu. Jeśli serwer używa serwera proxy:
     - Kliknij pozycję **Konfiguruj serwer proxy** , aby określić adres serwera proxy w postaci `http://ProxyIPAddress` lub na `http://ProxyFQDN` porcie nasłuchu.
     - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
     - Obsługiwane są tylko serwery proxy HTTP.
     - Jeśli dodano szczegóły serwera proxy lub wyłączono serwer proxy i/lub uwierzytelnianie, kliknij przycisk **Zapisz** , aby ponownie uruchomić sprawdzanie łączności.
   - **Synchronizacja czasu**: czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby odnajdywanie działało prawidłowo.
   - **Zainstaluj aktualizacje**: urządzenie zapewnia zainstalowanie najnowszych aktualizacji. Po zakończeniu sprawdzania można kliknąć pozycję **Wyświetl usługi urządzenia** , aby zobaczyć stan i wersje usług uruchomionych na serwerze urządzeń.
   - **Zainstaluj VDDK**: Urządzenie sprawdza, czy zainstalowano pakiet VMware vSphere Virtual Disk Development Kit (VDDK). Jeśli nie jest zainstalowany, Pobierz VDDK 6,7 z programu VMware i Wyodrębnij zawartość pliku zip do określonej lokalizacji na urządzeniu, zgodnie z **instrukcjami dotyczącymi instalacji**.

     Migracja serwera Azure Migrate przy użyciu programu VDDK do replikowania serwerów podczas migracji na platformę Azure. 
1. Jeśli chcesz, możesz **ponownie uruchomić wymagania wstępne** w dowolnym momencie podczas konfigurowania urządzenia, aby sprawdzić, czy urządzenie spełnia wszystkie wymagania wstępne.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Panel 1 w Menedżerze konfiguracji urządzenia":::


## <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Wklej **klucz projektu** skopiowany z portalu. Jeśli nie masz klucza, przejdź do obszaru **odnajdywanie i ocena> odkryj> Zarządzaj istniejącymi urządzeniami**, wybierz nazwę urządzenia podaną w momencie generowania klucza i skopiuj odpowiedni klucz.
1. Do uwierzytelnienia w systemie Azure potrzebny będzie kod urządzenia. Kliknięcie przycisku **Zaloguj** spowoduje otwarcie modalnego kodu urządzenia, jak pokazano poniżej.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modalne wyświetlanie kodu urządzenia":::

1. Kliknij pozycję **Kopiuj kod & login** , aby skopiować kod urządzenia i otworzyć monit logowania platformy Azure na nowej karcie przeglądarki. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
1. Na nowej karcie Wklej kod urządzenia i zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
   
   Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. W przypadku nagłego zamknięcia karty logowania bez logowania, należy odświeżyć kartę przeglądarki w Menedżerze konfiguracji urządzenia, aby ponownie włączyć przycisk Zaloguj.
1. Po pomyślnym zalogowaniu Wróć do poprzedniej karty przy użyciu Menedżera konfiguracji urządzeń.
1. Jeśli konto użytkownika platformy Azure używane do rejestrowania ma odpowiednie uprawnienia do zasobów platformy Azure utworzonych podczas generowania klucza, Rejestracja urządzenia zostanie zainicjowana.
1. Po pomyślnym zarejestrowaniu urządzenia można wyświetlić szczegóły rejestracji, klikając pozycję **Wyświetl szczegóły**.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Panel 2 w Menedżerze konfiguracji urządzenia":::

## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

### <a name="provide-vcenter-server-details"></a>Podaj szczegóły vCenter Server

Urządzenie musi połączyć się z vCenter Server, aby odnaleźć dane konfiguracji i wydajności serwerów.

1. W **kroku 1: podaj poświadczenia vCenter Server**, kliknij pozycję **Dodaj poświadczenia** , aby określić przyjazną nazwę dla poświadczeń, Dodaj **nazwę użytkownika** i **hasło** do konta vCenter Server, które będzie używane przez urządzenie do odnajdywania serwerów z systemem w vCenter Server.
    - Należy skonfigurować konto z wymaganymi uprawnieniami, które opisano w tym artykule.
    - Jeśli chcesz przeznaczyć zakres odnajdywania do określonych obiektów VMware (vCenter Server centrach danych, klastrów, folderu klastrów, hostów, folderu hostów lub poszczególnych serwerów), zapoznaj się z instrukcjami w [tym artykule](set-discovery-scope.md) , aby ograniczyć konto używane przez Azure Migrate.
1. W **kroku 2: podaj vCenter Server Szczegóły**, kliknij pozycję **Dodaj źródło odnajdywania** , aby wybrać przyjazną nazwę dla poświadczeń z listy rozwijanej, określ **adres IP/nazwę FQDN** vCenter Server. Możesz pozostawić **port** domyślny (443) lub określić port niestandardowy, dla którego vCenter Server nasłuchiwanie i kliknąć przycisk **Zapisz**.
1. Po kliknięciu przycisku **Zapisz** Urządzenie spróbuje sprawdzić poprawność połączenia z vCenter Server przy użyciu podanych poświadczeń i wyświetlić **stan weryfikacji** w tabeli dotyczącej vCenter Server adres IP/nazwa FQDN.
1. Możesz ponownie **sprawdzić poprawność** łączności, aby vCenter Server kiedykolwiek przed rozpoczęciem odnajdywania.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel w Menedżerze konfiguracji urządzenia dla vCenter Server Szczegóły":::

### <a name="provide-server-credentials"></a>Podaj poświadczenia serwera

W **kroku 3: zapewnianie poświadczeń serwera do wykonywania spisu oprogramowania, analizy zależności bez agentów i odnajdywania SQL Server wystąpień i baz danych**, można wybrać opcję udostępnienia wielu poświadczeń serwera lub jeśli nie chcesz korzystać z tych funkcji, możesz pominąć ten krok i kontynuować odnajdywanie vCenter Server. Zamiar można zmienić w dowolnym momencie później.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Panel 3 w Menedżerze konfiguracji urządzenia dla szczegółów serwera":::


Jeśli chcesz korzystać z tych funkcji, możesz podać poświadczenia serwera, wykonując poniższe kroki. Urządzenie podejmie próbę automatycznego mapowania poświadczeń do serwerów w celu wykonania funkcji odnajdywania.

- Możesz dodać poświadczenia serwera, klikając przycisk **Dodaj poświadczenia** . Spowoduje to otwarcie modalnego, w którym można wybrać **Typ poświadczeń** z listy rozwijanej.
- Możesz podać domenę/system Windows (nienależące do domeny)/Linux (niedomenowe)/SQL Server poświadczenia uwierzytelniania. [Dowiedz się więcej](add-server-credentials.md) na temat podania poświadczeń i sposobu ich obsługi.
- Dla każdego typu poświadczeń należy określić przyjazną nazwę dla poświadczeń, dodać **nazwę użytkownika** i **hasło** , a następnie kliknąć przycisk **Zapisz**.
- W przypadku wybrania poświadczeń domeny należy również określić nazwę FQDN domeny. Nazwa FQDN jest wymagana do zweryfikowania autentyczności poświadczeń z Active Directory tej domeny.
- Zapoznaj się z [uprawnieniami wymaganymi](add-server-credentials.md#required-permissions) na koncie w celu odnalezienia zainstalowanych aplikacji, analizy zależności bez wykorzystania agentów lub odnajdywania wystąpień SQL Server i baz danych.
- Jeśli chcesz dodać jednocześnie wiele poświadczeń, kliknij pozycję **Dodaj więcej** , aby zapisać i dodać więcej poświadczeń.
- Po kliknięciu przycisku **Zapisz** lub **Dodaj więcej** urządzenie sprawdza poprawność poświadczeń domeny z Active Directory domeny w celu ich autentyczności. W tym celu należy unikać blokowania kont, gdy urządzenie wykonuje wiele iteracji w celu mapowania poświadczeń na odpowiednie serwery.
- W tabeli poświadczeń można zobaczyć **stan sprawdzania poprawności** dla wszystkich poświadczeń domeny. Zweryfikowane zostaną tylko poświadczenia domeny.
- Jeśli sprawdzanie poprawności zakończy się niepowodzeniem, można kliknąć pozycję stan **niepowodzenia** , aby zobaczyć błąd napotkany, a następnie kliknąć przycisk ponownie **Sprawdź poprawność poświadczeń** po rozwiązaniu problemu w celu ponownego zweryfikowania nieudanych poświadczeń domeny.
    :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Panel 3 w Menedżerze konfiguracji urządzenia na potrzeby udostępniania wielu poświadczeń":::

### <a name="start-discovery"></a>Rozpocznij odnajdywanie

1. Kliknij pozycję **Rozpocznij odnajdywanie**, aby uruchomić odnajdywanie vCenter Server. Po pomyślnym zainicjowaniu odnajdywania można sprawdzić stan odnajdywania dla vCenter Server adres IP/nazwa FQDN w tabeli sources.
1. Jeśli podano poświadczenia serwera, Spis oprogramowania (Odnajdywanie zainstalowanych aplikacji) zostanie automatycznie zainicjowany po zakończeniu odnajdywania vCenter Server. Spis oprogramowania jest wykonywany co 12 godzin.
1. [Spis oprogramowania](how-to-discover-applications.md) służy do identyfikowania wystąpień SQL Server uruchomionych na serwerach i korzystania z tych informacji, urządzenia próbuje nawiązać połączenie z wystąpieniami za pośrednictwem uwierzytelniania systemu Windows lub poświadczeń uwierzytelniania SQL Server dostarczonych na urządzeniu oraz zbierać dane w SQL Server bazach danych i ich właściwościach. Funkcja odnajdywania SQL jest przeprowadzana co 24 godziny.
1. Podczas tworzenia spisu oprogramowania poświadczenia dodanych serwerów będą powtarzane względem serwerów i sprawdzane pod kątem analizy zależności bez agenta. Można włączyć analizę zależności bez agenta dla serwerów z poziomu portalu. Tylko serwery, na których Walidacja zakończyła się powodzeniem, można wybrać, aby włączyć analizę zależności bez agenta.

Odnajdywanie działa w następujący sposób:

- Aby spis odnalezionych serwerów pojawił się w portalu, zajmie około 15 minut.
- Odnajdywanie zainstalowanych aplikacji może zająć trochę czasu. Czas trwania zależy od liczby odnalezionych serwerów. W przypadku serwerów z 500 na wykrytym magazynie w portalu Azure Migrate trwa około godziny.
- Po zakończeniu odnajdywania serwerów można włączyć analizę zależności bez agenta na serwerach z poziomu portalu.
- SQL Server wystąpienia i bazy danych zostaną uruchomione w portalu w ciągu 24 godzin od inicjacji odnajdywania.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkami dotyczącymi [oceny oprogramowania VMware](./tutorial-assess-vmware-azure-vm.md) i [migracji bez wykorzystania agentów](tutorial-migrate-vmware.md).
