---
title: Konfigurowanie urządzenia migracji platformy Azure dla funkcji Hyper-V
description: Dowiedz się, jak skonfigurować urządzenie migracji platformy Azure do oceny i migracji maszyn wirtualnych funkcji Hyper-V.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 77c13a3a8c87d116bd0863324d28669185c53c84
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538294"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Konfigurowanie urządzenia dla maszyn wirtualnych funkcji Hyper-V

W tym artykule należy skonfigurować urządzenie migracji platformy Azure do oceny maszyn wirtualnych funkcji Hyper-V za pomocą narzędzia [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

Urządzenie migracji platformy Azure to [lekkie](migrate-appliance.md) urządzenie używane przez usługę Azure Migrate:Server Assessment/Migration do odnajdywania lokalnych maszyn wirtualnych z programem Hyper V i wysyłania metadanych/danych dotyczących wydajności maszyn wirtualnych na platformę Azure.

Urządzenie można wdrożyć przy użyciu kilku metod:

- Skonfiguruj na maszynie wirtualnej funkcji Hyper-V przy użyciu pobranego dysku VHD. Jest to metoda opisana w tym artykule.
- Skonfiguruj na maszynie wirtualnej funkcji Hyper-V lub na komputerze fizycznym za pomocą skryptu instalatora programu PowerShell. [Ta metoda](deploy-appliance-script.md) powinna być używana, jeśli nie można skonfigurować maszyny Wirtualnej przy użyciu dysku wirtualnego lub jeśli jesteś w usłudze Azure Government.

Po utworzeniu urządzenia można sprawdzić, czy można połączyć się z oceną migracji:serwera platformy Azure, skonfigurować go po raz pierwszy i zarejestrować go w projekcie migracji platformy Azure.

## <a name="appliance-deployment-vhd"></a>Wdrażanie urządzeń (VHD)

Aby skonfigurować urządzenie przy użyciu szablonu VHD:

- Pobierz skompresowany dysk VHD funkcji Hyper-V z witryny Azure portal.
- Utwórz urządzenie i sprawdź, czy można połączyć się z oceną serwera migracji platformy Azure.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj go w projekcie migracji platformy Azure.

## <a name="download-the-vhd"></a>Pobierz dysk VHD

Pobierz spakowany szablon VHD dla urządzenia.

1. W **obszarze** > Cele migracji**Serwery** > **usługi Azure Migrate: Ocena serwera**kliknij przycisk **Odkryj**.
2. W **discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, kliknij **przycisk Tak, z Hyper-V**.
3. Kliknij **przycisk Pobierz,** aby pobrać plik VHD.

    ![Pobieranie maszyny wirtualnej](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Weryfikowanie zabezpieczeń

Przed wdrożeniem pliku jest bezpieczny, zanim go wdrożysz.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla dysku VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  W przypadku urządzenia w wersji 2.19.11.12 wygenerowany skrót powinien być zgodny z tymi [ustawieniami.](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security)




## <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i utwórz maszynę wirtualną.

1. Wyodrębnij spakowany plik VHD do folderu na hoście funkcji Hyper-V, który będzie obsługiwał maszynę wirtualną urządzenia. Wyodrębnione są trzy foldery.
2. Otwórz Menedżera funkcji Hyper-V. W **akcji**kliknij pozycję **Importuj maszynę wirtualną**.

    ![Wdrażanie dysku VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. W Kreatorze importu maszyny wirtualnej > **przed rozpoczęciem**kliknij przycisk **Dalej**.
3. W **obszarze Znajdź folder**określ folder zawierający wyodrębniony dysk VHD. Następnie kliknij przycisk **Dalej**.
1. W **obszarze Wybierz maszynę wirtualną**kliknij przycisk **Dalej**.
2. W **2014 r. kliknij**pozycję **Kopiuj maszynę wirtualną (utwórz nowy unikatowy identyfikator).** Następnie kliknij przycisk **Dalej**.
3. W **obszarze Wybierz miejsce docelowe**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
4. W **folderach magazynu**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
5. W **obszarze Wybierz sieć**określ przełącznik wirtualny, którego będzie używać maszyna wirtualna. Przełącznik wymaga łączności z Internetem, aby wysłać dane na platformę Azure.
6. W **podsumowaniu**przejrzyj ustawienia. Następnie kliknij przycisk **Zakończ**.
7. W Menedżerze funkcji Hyper-V > **maszyn wirtualnych**uruchom maszynę wirtualną.


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może łączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych.](migrate-appliance.md#government-cloud-urls)

## <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy. Jeśli urządzenie zostanie wdrożone przy użyciu skryptu zamiast dysku wirtualnego, pierwsze dwa kroki w procedurze nie mają zastosowania.

1. W programie Hyper-V Manager > **maszyn wirtualnych**kliknij prawym przyciskiem myszy maszynę wirtualną > **Connect**.
2. Podaj język, strefę czasową i hasło urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może połączyć się z maszyną wirtualną, i otwórz adres URL aplikacji internetowej urządzenia: **https:// nazwę urządzenia lub adres*IP:* 44368**.

   Alternatywnie możesz otworzyć aplikację z pulpitu urządzenia, klikając skrót do aplikacji.
1. W aplikacji sieci web > **Konfigurowanie wymagań wstępnych**wykonaj następujące czynności:
    - **Licencja**: Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
    - **Łączność:** Aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij pozycję **Ustawienia serwera proxy**i określ adres http://ProxyIPAddress http://ProxyFQDNserwera proxy i port nasłuchiwania w formularzu lub .
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu:** czas jest weryfikowany. Czas na urządzeniu powinien być zsynchronizowany z czasem internetowym, aby odnajdowanie maszyn wirtualnych działało poprawnie.
    - **Zainstaluj aktualizacje:** Ocena programu Azure Migrate Server sprawdza, czy urządzenie ma zainstalowane najnowsze aktualizacje.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie za pomocą usługi Azure Migrate

1. Kliknij **pozycję Zaloguj**się . Jeśli nie jest wyświetlany, upewnij się, że wyłączono blokowanie wyskakujących wyskakujących w przeglądarce.
2. Na nowej karcie zaloguj się przy użyciu poświadczeń platformy Azure.
    - Zaloguj się przy użyciu swojej nazwy użytkownika i hasła.
    - Logowanie za pomocą numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu wróć do aplikacji sieci web.
4. Wybierz subskrypcję, w której został utworzony projekt migracji platformy Azure. Następnie wybierz projekt.
5. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
6. Kliknij **pozycję Zarejestruj**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegowanie poświadczeń dla identyfikatorów VHD SMB

Jeśli używasz wirtualnych identyfikatorów na małych i średnich przedsiębiorstwach, należy włączyć delegowanie poświadczeń z urządzenia do hostów funkcji Hyper-V. Aby to zrobić z urządzenia:

1. Na maszynie Wirtualnej urządzenia uruchom to polecenie. HyperVHost1/HyperVHost2 są przykładowymi nazwami hostów.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Alternatywnie, należy to zrobić w edytorze lokalnych zasad grupy na urządzeniu:
    - W**okolicy konfiguracji komputera** **lokalnego** > kliknij pozycję**Delegowanie poświadczeń****systemu** >  **szablonów administracyjnych** > .
    - Kliknij dwukrotnie pozycję **Zezwalaj na delegowanie nowych poświadczeń**i wybierz pozycję **Włączone**.
    - W **obszarze Opcje**kliknij pozycję **Pokaż**i dodaj do listy każdy host funkcji Hyper-V, który chcesz odkryć, z **wsmanem/** jako prefiksem.
    - W **obszarze Delegowanie poświadczeń**kliknij dwukrotnie pozycję **Zezwalaj na delegowanie nowych poświadczeń za pomocą uwierzytelniania serwera tylko dla usługi NTLM**. Ponownie dodaj do listy każdy host funkcji Hyper-V, który chcesz odkryć, z **wsman/** jako prefiksem.

## <a name="start-continuous-discovery"></a>Rozpocznij ciągłe odnajdowanie

Połącz się z urządzenia do hostów lub klastrów funkcji Hyper-V i rozpocznij odnajdowanie maszyn wirtualnych.

1. W **obszarze Nazwa użytkownika** i **Hasło**określ poświadczenia konta, których urządzenie będzie używać do odnajdywać maszyny wirtualne. Określ przyjazną nazwę poświadczeń i kliknij przycisk **Zapisz szczegóły**.
2. Kliknij **pozycję Dodaj hosta**i określ szczegóły hosta/klastra funkcji Hyper-V.
3. Kliknij pozycję **Validate** (Waliduj). Po weryfikacji wyświetlana jest liczba maszyn wirtualnych, które można odnajdować na każdym hoście/klastrze.
    - Jeśli sprawdzanie poprawności nie powiedzie się dla hosta, przejrzyj błąd, najeżdżając kursorem na ikonę w kolumnie **Stan.** Rozwiązywanie problemów i sprawdzanie poprawności ponownie.
    - Aby usunąć hosty lub klastry, zaznacz > **Usuń**.
    - Nie można usunąć określonego hosta z klastra. Można usunąć tylko cały klaster.
    - Można dodać klaster, nawet jeśli występują problemy z określonymi hostami w klastrze.
4. Po weryfikacji kliknij przycisk **Zapisz i rozpocznij odnajdowanie,** aby rozpocząć proces odnajdywania.

To rozpoczyna odnajdowanie. Trwa około 15 minut dla metadanych odnalezionych maszyn wirtualnych do wyświetlenia w witrynie Azure portal.

## <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po zakończeniu odnajdywania można sprawdzić, czy maszyny wirtualne są wyświetlane w portalu.

1. Otwórz pulpit nawigacyjny migracji platformy Azure.
2. W **obszarze Migracja platformy Azure — serwery, strona** > **Migracja platformy Azure: Ocena serwera,** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.


## <a name="next-steps"></a>Następne kroki

Wypróbuj [ocenę funkcji Hyper-V](tutorial-assess-hyper-v.md) za pomocą oceny serwera migracji usługi Azure.
