---
title: Przewodnik wdrażania FortiGate | Microsoft Docs
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i sieci VPN FortiGate SSL.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658450"
---
# <a name="fortigate-deployment-guide"></a>Przewodnik wdrażania FortiGate

## <a name="contents"></a>Zawartość

- Realizowanie licencji FortiGate
- Pobierz oprogramowanie układowe
- Wdróż maszynę wirtualną FortiGate
   - Ustaw publiczny adres IP Statuc i przypisz w pełni kwalifikowaną nazwę domeny
   - Utwórz nową regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego dla portu TCP
- Tworzenie niestandardowej aplikacji platformy Azure dla usługi FortiGate
- Przygotuj do dopasowania grupy
   - Tworzenie grup dla użytkowników
- Konfigurowanie maszyny wirtualnej FortiGate
   - Zainstaluj licencję
   - Aktualizowanie oprogramowania układowego
   - Zmień port zarządzania na TCP
   - Przekaż Azure Active Directory certyfikat podpisywania SAML
   - Przekazywanie i Konfigurowanie niestandardowego certyfikatu SSL
   - Wykonaj konfigurację wiersza polecenia
   - Tworzenie portali sieci VPN i zasad zapory
- Testowanie logowania przy użyciu platformy Azure

## <a name="redeeming-the-fortigate-license"></a>Realizowanie licencji FortiGate

Produkt zapory FortiGate nowej generacji jest dostępny jako maszyna wirtualna na platformie Azure IaaS. Istnieją dwa tryby licencjonowania dla tej maszyny wirtualnej —

- Płatność zgodnie z rzeczywistym użyciem
- Bring your own license (BYOL)

Partnerskie z Fortinet w celu zapewnienia wskazówek dotyczących bezpiecznego dostępu hybrydowego (SHA), Fortinet może zapewnić członkom zespołu usługi Azure AD dostęp do produkcji SHA z licencjami. W przypadkach, gdy nie podano żadnej licencji, wdrożenie PAYG również będzie działało.

W przypadkach, w których wydano licencję, Fortinet udostępnia kod rejestracyjny, który musi zostać zrealizowany w trybie online

![Fortigate SSL sieci VPN](registration-code.png)

1. Zarejestruj się w https://support.fortinet.com/
2. Po zarejestrowaniu Zaloguj się na https://support.fortinet.com/
3. Przejdź do **elementu zawartości** — > **zarejestruj/Aktywuj**
4. Wprowadź kod rejestracyjny dostarczony przez Fortinet
5. Określ kod rejestracji, wybierz **produkt będzie używany przez użytkownika innego niż** administrator, a następnie kliknij przycisk **dalej** .
6. Wprowadź opis produktu (np. FortiGate), ustaw partnera Fortinet jako **inny** > **firmy Microsoft** i kliknij przycisk **dalej** .
7. Zaakceptuj **umowę rejestracji produktu Fortinet** , a następnie kliknij przycisk **dalej** .
8. Zaakceptuj **warunki** i kliknij przycisk **Potwierdź**
9. Kliknij **plik licencji Pobierz** i Zapisz licencję na później


## <a name="download-firmware"></a>Pobierz oprogramowanie układowe

W momencie pisania, maszyna wirtualna platformy Azure FortiGate nie jest dostarczana z wersją oprogramowania układowego wymaganą do uwierzytelniania za pomocą protokołu SAML. Najnowszą wersję należy uzyskać od firmy Fortinet.

1. Logowanie w https://support.fortinet.com/
2. Przejdź do **pobierania** — **obrazy oprogramowania układowego** >
3. Kliknij przycisk **Pobierz** z prawej strony **Informacje o wersji**
4. Kliknij pozycję **V6.**
5. Kliknij pozycję **6.**
6. Kliknij pozycję **6,4.**
7. Pobierz **FGT_VM64_AZURE-V6-build1637-Fortinet. out** , klikając łącze **https** w tym samym wierszu
8. Zapisz plik w celu późniejszego


## <a name="deploy-the-fortigate-vm"></a>Wdróż maszynę wirtualną FortiGate

1. Przejdź do https://portal.azure.com subskrypcji i zaloguj się do niej, w której chcesz wdrożyć maszynę wirtualną Fortigate
2. Utwórz nową grupę zasobów lub Otwórz grupę zasobów, w której chcesz wdrożyć maszynę wirtualną FortiGate
3. Kliknij przycisk **Dodaj**
4. Wprowadź ciąg "Forti" w oknie dialogowym **Wyszukiwanie w witrynie Marketplace** i wybierz pozycję **Fortinet Fortigate nowej** **generacji zapory**
5. Wybierz plan oprogramowania (BYOL Jeśli masz licencję lub PAYG, jeśli nie), a następnie kliknij pozycję **Utwórz** .
6. Wypełnij konfigurację maszyny wirtualnej

    ![Fortigate SSL sieci VPN](virtual-machine.png)

7. Ustaw typ uwierzytelniania na **hasło** i podaj poświadczenia administracyjne dla maszyny wirtualnej
8. Kliknij przycisk **Przegląd + Utwórz**
9. Kliknij pozycję **Utwórz**
10. Poczekaj na zakończenie wdrożenia maszyny wirtualnej


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Ustaw publiczny adres IP Statuc i przypisz w pełni kwalifikowaną nazwę domeny

Aby zapewnić spójne środowisko użytkownika, należy ustawić publiczny adres IP przypisany do maszyny wirtualnej FortiGate, aby był przypisany statycznie. Ponadto mapowanie go do w pełni kwalifikowanej nazwy domeny jest również użyteczne z tego samego powodu.

_Ustawianie statycznego publicznego adresu IP_

1. Przejdź do https://portal.azure.com i Otwórz ustawienia dla maszyny wirtualnej Fortigate
2. Na ekranie **Przegląd** kliknij publiczny adres IP.

    ![Fortigate SSL sieci VPN](public-ip-address.png)

3. Kliknij pozycję **statyczny** , a następnie kliknij pozycję **Zapisz** .

_Przypisywanie w pełni kwalifikowanej nazwy domeny_

Jeśli posiadasz publiczną nazwę domeny routingu dla środowiska, w którym wdrażana jest maszyna wirtualna FortiGate, Utwórz rekord hosta (A) dla maszyny wirtualnej, który jest mapowany na publiczny adres IP, który jest przypisany statycznie powyżej.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Utwórz nową regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego dla portu TCP

1. Przejdź do https://portal.azure.com i Otwórz ustawienia dla maszyny wirtualnej Fortigate
2. Kliknij pozycję **Sieć** w menu po lewej stronie. Zostanie wyświetlony interfejs sieciowy i wyświetlone reguły portów przychodzących
3. Kliknij pozycję **Dodaj regułę portu wejściowego**
4. Utwórz nową regułę portu ruchu przychodzącego dla protokołu TCP 8443

    ![Fortigate SSL sieci VPN](port-rule.png)

5. Kliknij przycisk **Dodaj**


## <a name="create-a-custom-azure-app-for-fortigate"></a>Tworzenie niestandardowej aplikacji platformy Azure dla usługi FortiGate

1. Przejdź do https://portal.azure.com i Otwórz blok Azure Active Directory dla dzierżawy, który będzie dostarczać tożsamość dla logowania Fortigate
2. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu po lewej stronie
3. Kliknij pozycję **Nowa aplikacja** .
4. Kliknij pozycję **aplikacja spoza galerii**
5. Podaj nazwę (np. FortiGate), a następnie kliknij przycisk **Dodaj** .
6. W menu po lewej stronie kliknij pozycję **Użytkownicy i grupy** .
7. Dodaj użytkowników, którzy będą mogli się zalogować, i kliknij przycisk **Przypisz**
8. Kliknij pozycję **Logowanie jednokrotne** w menu po lewej stronie
9. Kliknij pozycję **SAML**
10. W obszarze **Podstawowa konfiguracja SAML** kliknij ołówek, aby edytować konfigurację
11. Konfigurowanie
    - Identyfikator (identyfikator jednostki) do `https://<address>/remote/saml/metadata`
    - Adres URL odpowiedzi (adres URL usługi Konsumenckej potwierdzenia) do  `https://<address>/remote/saml/login`
    - Adres URL wylogowywania do `https://<address>/remote/saml/logout`

    Gdzie `address` to nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate

    Zapisz każdy z tych adresów URL do późniejszego użycia —

    - Identyfikator jednostki
    - Adres URL odpowiedzi
    - Adres URL wylogowywania
12. Kliknij pozycję **Zapisz**
13. Zamknij podstawową konfigurację języka SAML
14. W obszarze **3 — certyfikat podpisywania SAML** , Pobierz **certyfikat (base64)** i Zapisz go później
15. W obszarze **4 — Konfigurowanie (nazwa aplikacji)** , skopiuj adres URL logowania platformy Azure, identyfikator usługi Azure AD i adres URL wylogowania platformy Azure i Zapisz je w celu późniejszego
    - Adres URL logowania platformy Azure
    - Identyfikator usługi Azure AD
    - Adres URL wylogowywania platformy Azure
16. W obszarze **2 — atrybuty użytkownika i oświadczenia** , kliknij ołówek, aby edytować konfigurację
17. Kliknij pozycję **Dodaj nowe** zgłoszenie
18. Ustaw nazwę **użytkownika**
19. Ustaw atrybut Source na **User. userPrincipalName**
20. Kliknij pozycję **Zapisz**
21. Kliknij pozycję **Dodaj zgłoszenie do grupy**
22. Wybierz **wszystkie grupy**
23. Zaznacz opcję **Dostosuj nazwę żądania grupy**
24. Ustaw nazwę na **grupę**
25. Kliknij pozycję **Zapisz**


## <a name="prepare-for-group-matching"></a>Przygotuj do dopasowania grupy

FortiGate umożliwia korzystanie z różnych środowisk portalu użytkowników po zalogowaniu się na podstawie przynależności do grupy. Na przykład może istnieć jedno środowisko dla grupy marketingowej i innej dla grupy finanse.

Skonfiguruj ją w następujący sposób:

### <a name="create-groups-for-users"></a>Tworzenie grup dla użytkowników

1. Przejdź do https://portal.azure.com i Otwórz blok Azure Active Directory dla dzierżawy, który będzie dostarczać tożsamość dla logowania Fortigate
2. Klikanie **grup**
3. Kliknij pozycję **Nowa grupa**
4. Utwórz grupę z
    - Typ grupy = zabezpieczenia
    - Nazwa grupy = `a meaningful name`
    - Opis grupy = `a meaningful description for the group`
    - Typ członkostwa = przypisany
    - Elementy członkowskie = `users for the user experience that will map to this group`
5. Powtórz kroki 3 i 4 dla każdego dodatkowego środowiska użytkownika
6. Po utworzeniu grup zaznacz każdą grupę i Zapisz identyfikator obiektu dla każdego z nich.
7. Zapisz te identyfikatory obiektów i grup na potrzeby późniejszej


## <a name="configure-the-fortigate-vm"></a>Konfigurowanie maszyny wirtualnej FortiGate

### <a name="install-the-license"></a>Zainstaluj licencję

1. Przejdź do strony `https://<address>`

    poniżej `address` znajduje się nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate

2. Kontynuuj poza wszystkimi błędami certyfikatów
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate
4. Jeśli wdrożenie używa modelu BYOL, zostanie wyświetlony monit o przekazanie licencji. Wybierz utworzony wcześniej plik licencji i przekaż go, kliknij przycisk **OK** , a następnie uruchom ponownie maszynę wirtualną Fortigate —

    ![Fortigate SSL sieci VPN](license.png)

5. Po ponownym uruchomieniu zaloguj się ponownie przy użyciu poświadczeń administratora, aby sprawdzić poprawność licencji.

### <a name="update-firmware"></a>Aktualizowanie oprogramowania układowego

1. Przejdź do strony `https://<address>`

    poniżej `address` znajduje się nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate

2. Kontynuuj poza wszystkimi błędami certyfikatów
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate
4. W menu po lewej stronie kliknij pozycję **system**
5. W menu po lewej stronie w obszarze System kliknij pozycję **oprogramowanie układowe** .
6. Na stronie Zarządzanie oprogramowaniem układowym kliknij przycisk **Przeglądaj** i wybierz pobrany wcześniej plik oprogramowania układowego
7. Zignoruj ostrzeżenie i kliknij kolejno pozycje **Konfiguracja i uaktualnienie kopii zapasowej** —

    ![Fortigate SSL sieci VPN](backup-configure-upgrade.png)

8. Kliknij przycisk **Kontynuuj** .
9. Po wyświetleniu monitu o zapisanie konfiguracji FortiGate (w pliku. conf) kliknij pozycję **Zapisz** .
10. Poczekaj na przekazanie oprogramowania układowego, aby można było go zastosować i dla maszyny wirtualnej FortiGate, aby przeprowadzić ponowny rozruch
11. Po ponownym uruchomieniu maszyny wirtualnej FortiGate Zaloguj się ponownie przy użyciu poświadczeń administratora
12. Po wyświetleniu monitu o przeprowadzenie instalacji pulpitu nawigacyjnego kliknij przycisk **dalej** .
13. Po rozpoczęciu filmu wideo samouczka kliknij przycisk **OK** .

### <a name="change-the-management-port-to-tcp"></a>Zmień port zarządzania na TCP

1. Przejdź do strony `https://<address>`

    poniżej `address` znajduje się nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate

2. Kontynuuj poza wszystkimi błędami certyfikatów
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate
4. W menu po lewej stronie kliknij pozycję **system**
5. W obszarze Ustawienia administracyjne zmień port HTTPS na **8443**
6. Kliknij przycisk **Zastosuj**
7. Po zastosowaniu zmiany przeglądarka spróbuje ponownie załadować stronę administracyjną, ale zakończy się niepowodzeniem. Od teraz w programie adres strony administracyjnej będzie `https://<address>`

    ![Fortigate SSL sieci VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>Przekaż Azure Active Directory certyfikat podpisywania SAML

1. Przejdź do strony `https://<address>`

    poniżej `address` znajduje się nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate

2. Kontynuuj poza wszystkimi błędami certyfikatów
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate
4. W menu po lewej stronie kliknij pozycję **system**
5. W obszarze System kliknij pozycję **Certyfikaty** .
6. Kliknij pozycję **Importuj** — > **certyfikat zdalny**
7. Przejdź do certyfikatu pobranego z niestandardowego wdrożenia aplikacji FortiGate w dzierżawie platformy Azure, wybierz go i kliknij przycisk **OK** .

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Przekazywanie i Konfigurowanie niestandardowego certyfikatu SSL

Można skonfigurować maszynę wirtualną FortiGate przy użyciu własnego certyfikatu SSL, który obsługuje nazwę FQDN, której używasz. Jeśli masz dostęp do certyfikatu SSL spakowanego z kluczem prywatnym w programie. Format PFX, może być używany w tym celu

1. Przejdź do strony `https://<address>`

    poniżej `address` znajduje się nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate

2. Kontynuuj poza wszystkimi błędami certyfikatów
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate
4. W menu po lewej stronie kliknij pozycję **system**
5. W obszarze System kliknij pozycję **Certyfikaty** .
6. Kliknij pozycję **Importuj** — **certyfikat lokalny** >
7. Kliknij pozycję **certyfikat PKCS #12**
8. Przejdź do. Plik PFX zawierający certyfikat SSL i klucz prywatny
9. Podaj. Hasło PFX
10. Podaj opisową nazwę certyfikatu
11. Kliknij przycisk **OK** .
12. W menu po lewej stronie kliknij pozycję **system**
13. W obszarze System kliknij pozycję **Ustawienia** .
14. W obszarze Ustawienia administracyjne Rozwiń listę rozwijaną obok pozycji certyfikat serwera HTTPS i wybierz certyfikat SSL zaimportowany powyżej
15. Kliknij przycisk **Zastosuj**
16. Zamknij okno przeglądarki, a następnie przejdź ponownie do `https://<address>`
17. Zaloguj się przy użyciu poświadczeń administratora FortiGate i obserwuj właściwy certyfikat SSL w użyciu.


### <a name="perform-command-line-configuration"></a>Wykonaj konfigurację wiersza polecenia

_Wykonaj konfigurację wiersza polecenia dla uwierzytelniania SAML_

1. Przejdź do https://portal.azure.com i Otwórz ustawienia dla maszyny wirtualnej Fortigate
2. W menu po lewej stronie kliknij pozycję **konsola szeregowa**
3. Logowanie się w konsoli szeregowej przy użyciu poświadczeń administratora maszyny wirtualnej FortiGate

    W następnym kroku wymagane są adresy URL zarejestrowane wcześniej. Tzn

    - Identyfikator jednostki
    - Adres URL odpowiedzi
    - Adres URL wylogowywania
    - Adres URL logowania platformy Azure
    - Identyfikator usługi Azure AD
    - Adres URL wylogowywania platformy Azure
4. W konsoli szeregowej wykonaj następujące polecenia:

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > Adres URL wylogowania platformy Azure zawiera element? Opis. Wymaga to specjalnej sekwencji klawiszy, aby można ją było prawidłowo dostarczyć do konsoli szeregowej FortiGate. Adres URL jest zazwyczaj-

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    Aby to umożliwić, w konsoli szeregowej, należy wpisać

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    Następnie wpisz CTRL + V,

    Następnie wklej resztę adresu URL w programie, aby zakończyć wiersz

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. Aby potwierdzić konfigurację, wykonaj polecenie —

    ```
    show user saml
    ```

_Wykonaj konfigurację wiersza polecenia dla dopasowania grupy_

1. Przejdź do https://portal.azure.com i Otwórz ustawienia dla maszyny wirtualnej Fortigate
2. W menu po lewej stronie kliknij pozycję **konsola szeregowa**
3. Logowanie się w konsoli szeregowej przy użyciu poświadczeń administratora maszyny wirtualnej FortiGate
4. W konsoli szeregowej wykonaj następujące polecenia:

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    Powtórz te polecenie z edycji `group 1 name` , ale dla każdej dodatkowej grupy, która będzie miała inne środowisko portalu w Fortigate

_Wykonaj konfigurację wiersza polecenia w celu przekroczenia limitu czasu uwierzytelniania_

1. Przejdź do https://portal.azure.com i Otwórz ustawienia dla maszyny wirtualnej Fortigate
2. W menu po lewej stronie kliknij pozycję **konsola szeregowa**
3. Logowanie się w konsoli szeregowej przy użyciu poświadczeń administratora maszyny wirtualnej FortiGate
4. W konsoli szeregowej wykonaj następujące polecenia:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Tworzenie portali sieci VPN i zasad zapory

1. Przejdź do strony `https://<address>`

    poniżej `address` znajduje się nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate

2. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate
3. W menu po lewej stronie kliknij pozycję **Sieć VPN** .
4. W obszarze Sieć VPN kliknij pozycję **portale protokołu SSL sieci VPN** .
5. Kliknij przycisk **Utwórz nowy**
6. Podaj nazwę (zazwyczaj dopasowuje ją do grupy platformy Azure używanej w celu zapewnienia niestandardowego środowiska portalu)
7. Kliknij znak plus ( **+** ) obok pozycji źródłowe pule adresów IP, wybierz pulę domyślną i kliknij przycisk **Zamknij** .
8. Dostosuj środowisko dla tej grupy. W przypadku testowania może to być dostosowanie komunikatu portalu i motywu. W tym miejscu można również utworzyć niestandardowe zakładki, które kierują użytkowników do zasobów wewnętrznych.
9. Kliknij przycisk **OK** .
10. Powtórz kroki od 5 do 9 dla każdej grupy platformy Azure, która będzie mieć niestandardowe środowisko portalu
11. W obszarze VPN kliknij pozycję **SSL-ustawienia sieci VPN** .
12. Kliknij znak plus ( **+** ) obok pozycji nasłuchiwanie na interfejsach
13. Wybierz pozycję **PORT1** , a następnie kliknij przycisk **Zamknij** .


14. Jeśli wcześniej zainstalowano niestandardowy certyfikat protokołu SSL, Zmień certyfikat serwera tak, aby używał niestandardowego certyfikatu protokołu SSL z menu rozwijanego.
15. W obszarze mapowanie uwierzytelniania/portalu kliknij pozycję **Utwórz nowe** .
16. Wybierz pierwszą grupę platformy Azure i dopasuj ją do portalu o tej samej nazwie
17. Kliknij przycisk **OK** .
18. Powtórz kroki od 15 do 17 dla każdej pary grup/Portal platformy Azure
19. W obszarze Uwierzytelnianie/mapowanie portalu Edytuj **wszystkich innych użytkowników/grupy**
20. Ustaw Portal na **pełny dostęp**
21. Kliknij przycisk **OK** .
22. Kliknij przycisk **Zastosuj**
23. Przewiń do góry strony Ustawienia protokołu SSL-VPN i kliknij ostrzeżenie **nie istnieją zasady protokołu SSL-VPN** 
     **. Kliknij tutaj, aby utworzyć nowe zasady SSL-VPN przy użyciu tych ustawień**
24. Podaj nazwę, taką jak **GRP sieci VPN**
25. Ustaw wychodzący interfejs na **port**
26. Kliknij pozycję **Źródło**
27. W obszarze adres zaznacz opcję **wszystkie**
28. W obszarze użytkownik Wybierz pierwszą grupę platformy Azure
29. Kliknij przycisk **Zamknij** .
30. Kliknij pozycję **miejsce docelowe**
31. W obszarze adres zwykle jest to sieć wewnętrzna. Wybierz login.microsoft.com do testowania
32. Kliknij przycisk **Zamknij** .
33. Kliknij pozycję **Usługa**
34. Kliknij **wszystko**
35. Kliknij przycisk **Zamknij** .
36. Kliknij przycisk **OK** .
37. W menu po lewej stronie kliknij pozycję **zasady & obiekty**
38. W obszarze zasady & obiekty kliknij pozycję **zasady zapory** .
39. Rozwiń węzeł **SSL-VPN Tunnel Interface (SSL. root) — port >**
40. Kliknij prawym przyciskiem myszy utworzone wcześniej zasady sieci VPN ( **VPN GRP 1** ) i wybierz polecenie **Kopiuj**
41. Kliknij prawym przyciskiem myszy w obszarze Zasady sieci VPN i wybierz polecenie **wklej** > **poniżej**
42. Edytuj nowe zasady, podając inną nazwę (Powiedz **VPN Grp2** ), a zmiana grupy dotyczy (inna grupa platformy Azure)
43. Kliknij prawym przyciskiem myszy nowe zasady i Ustaw stan na **włączone**


## <a name="test-sign-in-using-azure"></a>Testowanie logowania przy użyciu platformy Azure

1. Przy użyciu sesji przeglądarki w trybie prywatnym przejdź do `https://<address>` 
2. Logowanie powinno przekierować do Azure Active Directory w celu zalogowania
3. Po podaniu poświadczeń dla użytkownika, który został przypisany do aplikacji FortiGate w dzierżawie platformy Azure, powinien zostać wyświetlony odpowiedni Portal użytkownika

    ![Fortigate SSL sieci VPN](test-sign-in.png)
