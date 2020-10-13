---
title: Przewodnik wdrażania FortiGate | Microsoft Docs
description: Skonfiguruj i pracuj z systemem Fortinet FortiGate nowej generacji zapory.
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
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273334"
---
# <a name="fortigate-deployment-guide"></a>Przewodnik wdrażania FortiGate

Korzystając z tego przewodnika wdrażania, dowiesz się, jak skonfigurować i współpracować z programem zapory FortiGate nowej generacji.

## <a name="redeem-the-fortigate-license"></a>Zrealizuj licencję FortiGate

Produkt zapory FortiGate nowej generacji jest dostępny jako maszyna wirtualna w infrastrukturze platformy Azure jako usługa (IaaS). Istnieją dwa tryby licencjonowania dla tej maszyny wirtualnej: płatność zgodnie z rzeczywistym użyciem i przeniesienie własnej licencji.

Fortinet mogą zapewnić członkom Azure Active Directory (Azure AD) "dostęp do produkcji bezpiecznego hybrydowego dostępu do zasobów (SHA)" z licencjami. W przypadkach, gdy nie podano żadnej licencji, wdrożenie z opcją płatność zgodnie z rzeczywistym użyciem również będzie działało.

W przypadku wystawienia licencji Fortinet zawiera kod rejestracyjny, który należy zrealizować w trybie online.

![Zrzut ekranu przedstawiający kod rejestracji w sieci VPN FortiGate SSL.](registration-code.png)

1. Zarejestruj się pod adresem https://support.fortinet.com/ .
2. Po zarejestrowaniu Zaloguj się pod adresem https://support.fortinet.com/ .
3. Przejdź do **pozycji**  >  **Rejestr zasobów/Aktywuj**.
4. Wprowadź kod rejestracyjny dostarczony przez Fortinet.
5. Określ kod rejestracji, wybierz **produkt będzie używany przez użytkownika innego niż**administrator, a następnie wybierz przycisk **dalej**.
6. Wprowadź opis produktu (na przykład Fortigate), ustaw partnera Fortinet jako **inne**  >  **firmy Microsoft**, a następnie wybierz przycisk **dalej**.
7. Zaakceptuj **umowę rejestracji produktu Fortinet**, a następnie wybierz pozycję **dalej**.
8. Zaakceptuj **warunki** i wybierz pozycję **Potwierdź**.
9. Wybierz pozycję **Pobierz plik licencji**i Zapisz licencję do nowszej wersji.


## <a name="download-firmware"></a>Pobierz oprogramowanie układowe

Maszyna wirtualna platformy Azure FortiGate nie jest obecnie dostarczana z wersją oprogramowania układowego wymaganą do uwierzytelniania za pomocą protokołu SAML. Najnowszą wersję należy uzyskać od firmy Fortinet.

1. Zaloguj się pod adresem https://support.fortinet.com/ .
2. Przejdź do pozycji **Pobierz**  >  **obrazy oprogramowania układowego**.
3. Na prawo od **informacji o wersji**wybierz pozycję **Pobierz**.
4. Wybierz pozycję **V6.** > **ust.** > **6,4.**.
5. Pobierz **FGT_VM64_AZURE-V6-build1637-Fortinet. out** , wybierając łącze **https** w tym samym wierszu.
6. Zapisz plik do nowszej wersji.


## <a name="deploy-the-fortigate-vm"></a>Wdróż maszynę wirtualną FortiGate

1. Przejdź do https://portal.azure.com i zaloguj się do subskrypcji, w której chcesz wdrożyć maszynę wirtualną Fortigate.
2. Utwórz nową grupę zasobów lub Otwórz grupę zasobów, w której chcesz wdrożyć maszynę wirtualną FortiGate.
3. Wybierz pozycję **Dodaj**.
4. W obszarze **Wyszukaj w portalu Marketplace**wprowadź *Forti*. Wybierz pozycję **Fortinet Fortigate nowej zapory**.
5. Wybierz plan oprogramowania (Jeśli masz licencję lub z opcją płatność zgodnie z rzeczywistym użyciem, jeśli nie jest to możliwe). Wybierz przycisk **Utwórz**.
6. Wypełnij konfigurację maszyny wirtualnej.

    ![Zrzut ekranu przedstawiający tworzenie maszyny wirtualnej.](virtual-machine.png)

7. Ustaw **Typ uwierzytelniania** na **hasło**i podaj poświadczenia administracyjne dla maszyny wirtualnej.
8. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**.
9. Poczekaj na zakończenie wdrożenia maszyny wirtualnej.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Ustaw statyczny publiczny adres IP i przypisz w pełni kwalifikowaną nazwę domeny

Aby zapewnić spójne środowisko użytkownika, ustaw publiczny adres IP przypisany do maszyny wirtualnej FortiGate, aby był przypisany statycznie. Ponadto należy zamapować ją na w pełni kwalifikowaną nazwę domeny (FQDN).

1. Przejdź do https://portal.azure.com , a następnie otwórz ustawienia dla maszyny wirtualnej Fortigate.
2. Na ekranie **Przegląd** wybierz publiczny adres IP.

    ![Zrzut ekranu przedstawiający Fortigate SSL sieci VPN.](public-ip-address.png)

3. Wybierz pozycję Zapisz **statyczny**  >  **Save**.

Jeśli posiadasz publiczną nazwę domeny routingu dla środowiska, w którym jest wdrażana maszyna wirtualna FortiGate, Utwórz rekord hosta (A) dla maszyny wirtualnej. Ten rekord jest mapowany na poprzedni publiczny adres IP, który jest przypisany statycznie.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Utwórz nową regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego dla portu TCP

1. Przejdź do https://portal.azure.com , a następnie otwórz ustawienia dla maszyny wirtualnej Fortigate.
2. W menu po lewej stronie wybierz pozycję **Sieć**. Zostanie wyświetlony interfejs sieciowy i są wyświetlane reguły portów ruchu przychodzącego.
3. Wybierz pozycję **Dodaj regułę portu wejściowego**.
4. Utwórz nową regułę portu ruchu przychodzącego dla protokołu TCP 8443.

    ![Zrzut ekranu przedstawiający dodanie reguły zabezpieczeń dla ruchu przychodzącego.](port-rule.png)

5. Wybierz pozycję **Dodaj**.


## <a name="create-a-custom-azure-app-for-fortigate"></a>Tworzenie niestandardowej aplikacji platformy Azure dla usługi FortiGate

1. Przejdź do https://portal.azure.com , a następnie otwórz okienko usługi Azure AD dla dzierżawy, która będzie dostarczać tożsamość dla logowania Fortigate.
2. W menu po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw**.
3. Wybierz **nową**  >  **aplikację nie galerii**aplikacji.
4. Wprowadź nazwę (na przykład FortiGate), a następnie wybierz pozycję **Dodaj**.
5. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.
6. Dodaj użytkowników, którzy będą mogli się zalogować, a następnie wybierz pozycję **Przypisz**.
7. W menu po lewej stronie wybierz pozycję **Logowanie jednokrotne**.
8. Wybierz pozycję **SAML**.
9. W obszarze **Podstawowa konfiguracja SAML**wybierz ikonę ołówka, aby edytować konfigurację.
10. Skonfiguruj następujące elementy:
    - **Identyfikator (identyfikator jednostki)** `https://<address>/remote/saml/metadata` .
    - **Adres URL odpowiedzi (adres URL usługi odbiorcy potwierdzenia)** ma być  `https://<address>/remote/saml/login` .
    - **Adres URL wylogowania** `https://<address>/remote/saml/logout` .

    `<address>` jest nazwą FQDN lub publicznym adresem IP przypisanym do maszyny wirtualnej FortiGate.

11. Zapisz każdy z tych adresów URL do późniejszego użycia: identyfikator jednostki, adres URL odpowiedzi i adres URL wylogowywania.
12. Wybierz pozycję **Zapisz**i Zamknij **podstawową konfigurację protokołu SAML**.
13. W obszarze **3 — certyfikat podpisywania SAML**, Pobierz **certyfikat (base64)** i Zapisz go później.
14. W obszarze **4 — Konfigurowanie (nazwa aplikacji)**, skopiuj adres URL logowania do platformy Azure, identyfikator usługi Azure AD i adres URL wylogowania platformy Azure, a następnie Zapisz je na później.
15. W obszarze **2 — atrybuty użytkownika i oświadczenia**wybierz ikonę ołówka, aby edytować konfigurację.
16. Wybierz pozycję **Dodaj nowe zastrzeżenie**i Ustaw nazwę na **nazwa_użytkownika**.
17. Ustaw atrybut Source na **User. userPrincipalName**.
18. Wybierz pozycję **Zapisz**  >  **Dodaj grupę Przejmij**  >  **wszystkie grupy**.
19. Wybierz pozycję **Dostosuj nazwę żądania grupy**, a następnie ustaw nazwę na **Grupuj**.
20. Wybierz pozycję **Zapisz**.


## <a name="prepare-for-group-matching"></a>Przygotuj do dopasowania grupy

FortiGate umożliwia korzystanie z różnych środowisk portalu użytkowników po zalogowaniu się na podstawie przynależności do grupy. Na przykład może istnieć jedno środowisko dla grupy marketingowej i innej dla grupy finanse. Poniżej przedstawiono sposób tworzenia grup dla użytkowników:

1. Przejdź do https://portal.azure.com , a następnie otwórz okienko usługi Azure AD dla dzierżawy, która będzie dostarczać tożsamość dla logowania Fortigate.
2. Wybierz pozycję **grupy**  >  **Nowa grupa**.
3. Utwórz grupę z następującymi szczegółami:
    - Typ grupy = zabezpieczenia
    - Nazwa grupy = `a meaningful name`
    - Opis grupy = `a meaningful description for the group`
    - Typ członkostwa = przypisany
    - Elementy członkowskie = `users for the user experience that will map to this group`
4. Powtórz kroki 3 i 4, aby zapoznać się z dodatkowymi środowiskami użytkownika.
5. Po utworzeniu grup zaznacz każdą grupę i Zapisz dla nich **Identyfikator obiektu** .
6. Zapisz te identyfikatory obiektów i grup w przyszłości.


## <a name="configure-the-fortigate-vm"></a>Konfigurowanie maszyny wirtualnej FortiGate

W poniższych sekcjach opisano sposób konfigurowania maszyny wirtualnej FortiGate.

### <a name="install-the-license"></a>Zainstaluj licencję

1. Przejdź do adresu `https://<address>`. W tym miejscu `<address>` jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate.

2. Kontynuuj poprzednie Błędy certyfikatów.
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate.
4. Jeśli wdrożenie korzysta z modelu "Przenieś własną licencję", zobaczysz monit o przekazanie licencji. Wybierz utworzony wcześniej plik licencji i przekaż go. Wybierz **przycisk OK** , a następnie uruchom ponownie maszynę wirtualną Fortigate.

    ![Zrzut ekranu licencji maszyny wirtualnej FortiGate.](license.png)

5. Po ponownym uruchomieniu zaloguj się ponownie przy użyciu poświadczeń administratora, aby sprawdzić poprawność licencji.

### <a name="update-firmware"></a>Aktualizowanie oprogramowania układowego

1. Przejdź do adresu `https://<address>`. W tym miejscu `<address>` jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate.

2. Kontynuuj poprzednie Błędy certyfikatów.
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate.
4. W menu po lewej stronie wybierz **System**pozycję  >  **oprogramowanie układowe**systemu.
5. W obszarze **Zarządzanie oprogramowaniem układowym**wybierz pozycję **Przeglądaj**, a następnie wybierz pobrany wcześniej plik oprogramowania układowego.
6. Zignoruj ostrzeżenie i wybierz pozycję **Konfiguracja i uaktualnienie kopii zapasowej**.

    ![Zrzut ekranu zarządzania oprogramowaniem układowym.](backup-configure-upgrade.png)

7. Wybierz pozycję **Continue** (Kontynuuj).
8. Po wyświetleniu monitu o zapisanie konfiguracji FortiGate (w pliku. conf) wybierz pozycję **Zapisz**.
9. Poczekaj, aż oprogramowanie układowe zostanie przekazane i zastosowane. Poczekaj na ponowne uruchomienie maszyny wirtualnej FortiGate.
10. Po ponownym uruchomieniu maszyny wirtualnej FortiGate Zaloguj się ponownie przy użyciu poświadczeń administratora.
11. Gdy zostanie wyświetlony monit o skonfigurowanie pulpitu nawigacyjnego, wybierz pozycję **dalej**.
12. Po rozpoczęciu filmu wideo samouczka wybierz **przycisk OK**.

### <a name="change-the-management-port-to-tcp"></a>Zmień port zarządzania na TCP

1. Przejdź do adresu `https://<address>`. W tym miejscu `<address>` jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate.

2. Kontynuuj poprzednie Błędy certyfikatów.
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate.
4. W menu po lewej stronie wybierz pozycję **system**.
5. W obszarze **Ustawienia administracyjne**zmień port HTTPS na **8443**, a następnie wybierz pozycję **Zastosuj**.
6. Po zastosowaniu zmiany przeglądarka spróbuje ponownie załadować stronę administracyjną, ale kończy się niepowodzeniem. Od teraz na stronie administrowania jest adres `https://<address>` .

    ![Zrzut ekranu przedstawiający zdalny certyfikat przekazywania.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Przekaż certyfikat podpisywania SAML usługi Azure AD

1. Przejdź do adresu `https://<address>`. W tym miejscu `<address>` jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate.

2. Kontynuuj poprzednie Błędy certyfikatów.
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate.
4. W menu po lewej stronie wybierz **System**pozycję  >  **Certyfikaty**systemu.
5. Wybierz pozycję **Importuj**  >  **certyfikat zdalny**.
6. Przejdź do certyfikatu pobranego z niestandardowego wdrożenia aplikacji FortiGate w dzierżawie platformy Azure. Zaznacz go, a następnie wybierz **przycisk OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Przekazywanie i Konfigurowanie niestandardowego certyfikatu SSL

Możesz chcieć skonfigurować maszynę wirtualną FortiGate przy użyciu własnego certyfikatu SSL, który obsługuje nazwę FQDN, której używasz. Jeśli masz dostęp do certyfikatu SSL spakowanego przy użyciu klucza prywatnego w formacie PFX, można go użyć do tego celu.

1. Przejdź do adresu `https://<address>`. W tym miejscu `<address>` jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate.

2. Kontynuuj poprzednie Błędy certyfikatów.
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate.
4. W menu po lewej stronie wybierz **System**pozycję  >  **Certyfikaty**systemu.
5. Wybierz pozycję **Importuj**certyfikat  >  **lokalny certyfikat**  >  **PKCS #12**.
6. Przejdź do. Plik PFX zawierający certyfikat SSL i klucz prywatny.
7. Podaj. Hasło PFX i zrozumiała Nazwa certyfikatu. Następnie wybierz przycisk **OK**.
8. W menu po lewej stronie wybierz **System**pozycję  >  **Ustawienia**systemowe.
9. W obszarze **Ustawienia administracyjne**rozwiń listę obok pozycji **certyfikat serwera https**i wybierz certyfikat SSL zaimportowany wcześniej.
10. Wybierz przycisk **Zastosuj**.
11. Zamknij okno przeglądarki i przejdź do `https://<address>` .
12. Zaloguj się przy użyciu poświadczeń administratora FortiGate. Powinien być teraz widoczny prawidłowy certyfikat SSL w użyciu.


### <a name="perform-command-line-configuration"></a>Wykonaj konfigurację wiersza polecenia

Poniższe sekcje zawierają opis kroków dla różnych konfiguracji przy użyciu wiersza polecenia.

#### <a name="for-saml-authentication"></a>Na potrzeby uwierzytelniania SAML

1. Przejdź do https://portal.azure.com , a następnie otwórz ustawienia dla maszyny wirtualnej Fortigate.
2. W menu po lewej stronie wybierz pozycję **konsola szeregowa**.
3. Zaloguj się w konsoli szeregowej przy użyciu poświadczeń administratora maszyny wirtualnej FortiGate. W następnym kroku wymagane są adresy URL zarejestrowane wcześniej:
    - Identyfikator jednostki
    - Adres URL odpowiedzi
    - Adres URL wylogowywania
    - Adres URL logowania platformy Azure
    - Identyfikator usługi Azure AD
    - Adres URL wylogowywania platformy Azure
4. W konsoli szeregowej Uruchom następujące polecenia:

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
    > Adres URL wylogowania platformy Azure zawiera `?` znak. Wymaga to specjalnej sekwencji klawiszy, aby można ją było prawidłowo dostarczyć do konsoli szeregowej FortiGate. Zwykle jest to adres URL `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1` . Aby zapewnić tę wartość w konsoli szeregowej, wpisz:
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    Następnie wpisz CTRL + V i wklej resztę adresu URL w, aby ukończyć wiersz: 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. Aby potwierdzić konfigurację, uruchom następujące polecenie:

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>Dla dopasowania grupy

1. Przejdź do https://portal.azure.com , a następnie otwórz ustawienia dla maszyny wirtualnej Fortigate.
2. W menu po lewej stronie wybierz pozycję **konsola szeregowa**.
3. Zaloguj się w konsoli szeregowej przy użyciu poświadczeń administratora maszyny wirtualnej FortiGate.
4. W konsoli szeregowej Uruchom następujące polecenia:

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

    Dla każdej dodatkowej grupy, która będzie miała inne środowisko portalu w FortiGate, Powtórz te polecenia (Zaczynając od drugiego wiersza kodu).

#### <a name="for-authentication-timeout"></a>Limit czasu uwierzytelniania

1. Przejdź do https://portal.azure.com , a następnie otwórz ustawienia dla maszyny wirtualnej Fortigate.
2. W menu po lewej stronie wybierz pozycję **konsola szeregowa**.
3. Zaloguj się w konsoli szeregowej przy użyciu poświadczeń administratora maszyny wirtualnej FortiGate.
4. W konsoli szeregowej Uruchom następujące polecenia:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Tworzenie portali sieci VPN i zasad zapory

1. Przejdź do adresu `https://<address>`. W tym miejscu `<address>` jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate.

2. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate.
3. W menu po lewej stronie wybierz kolejno opcje **VPN**  >  **SSL-VPN portale**  >  **Utwórz nowy**.
6. Podaj nazwę (zazwyczaj dopasowuje ją do grupy platformy Azure używanej w celu zapewnienia niestandardowego środowiska portalu).
7. Wybierz znak plus ( **+** ) obok pozycji **źródłowe pule adresów IP**, wybierz pulę domyślną, a następnie wybierz pozycję **Zamknij**.
8. Dostosuj środowisko dla tej grupy. W przypadku testowania może to być dostosowanie komunikatu portalu i motywu. W tym miejscu można również utworzyć niestandardowe zakładki, które kierują użytkowników do zasobów wewnętrznych.
9. Kliknij przycisk **OK**.
10. Powtórz kroki od 5-9 dla każdej grupy platformy Azure, która będzie mieć niestandardowe środowisko portalu.
11. W obszarze Sieć VPN wybierz pozycję **Ustawienia protokołu SSL i sieci VPN**.
12. Wybierz znak plus ( **+** ) obok pozycji **nasłuchiwanie na interfejsach**, wybierz pozycję **PORT1**, a następnie wybierz pozycję **Zamknij**.
14. Jeśli wcześniej zainstalowano niestandardowy certyfikat protokołu SSL, Zmień **certyfikat serwera** tak, aby używał niestandardowego certyfikatu protokołu SSL z menu rozwijanego.
15. W obszarze **Mapowanie uwierzytelniania/portalu**wybierz pozycję **Utwórz nowy**. Wybierz pierwszą grupę platformy Azure i dopasuj ją do portalu o tej samej nazwie. Następnie wybierz przycisk **OK**.
18. Powtórz kroki 15-17 dla każdej pary grup platformy Azure i portalu.
19. W obszarze **uwierzytelnianie/mapowanie portalu**Edytuj **wszystkich innych użytkowników/grupy**.
20. Ustaw Portal na **pełny dostęp**, a następnie wybierz pozycję **OK**  >  **Zastosuj**.
23. Przewiń w górę strony **Ustawienia protokołu SSL-VPN** i wybierz pozycję **nie istnieją zasady protokołu SSL-VPN. Kliknij tutaj, aby utworzyć nowe zasady SSL-VPN przy użyciu tych ustawień.**
24. Podaj nazwę, taką jak **GRP sieci VPN**. Następnie ustaw **wychodzący interfejs** na **port**, a następnie wybierz pozycję **Źródło**.
27. W obszarze **adres**zaznacz opcję **wszystkie**.
28. W obszarze **użytkownik**wybierz pierwszą grupę platformy Azure.
29. Wybierz pozycję **Zamknij**  >  **miejsce docelowe**. W obszarze **adres**zwykle jest to sieć wewnętrzna. Wybierz **login.Microsoft.com** do testowania.
32. Wybierz pozycję **Zamknij**  >  **usługę**  >  **wszystkie**. Następnie wybierz pozycję **Zamknij**  >  **OK**.
37. W menu po lewej stronie wybierz pozycję **zasady & obiekty**  >  **zasady zapory**.
39. Rozwiń **Port SSL-VPN Tunnel Interface (SSL. root)**  >  **port**.
40. Kliknij prawym przyciskiem myszy utworzone wcześniej zasady sieci VPN (**VPN GRP 1**) i wybierz polecenie **Kopiuj**.
41. Kliknij prawym przyciskiem myszy pozycję Zasady sieci VPN, a następnie wybierz polecenie **Wklej**  >  **poniżej**.
42. Edytuj nowe zasady, podając inną nazwę (na przykład **VPN Grp2**). Zmień również grupę, do której odnosi się (do innej grupy platformy Azure).
43. Kliknij prawym przyciskiem myszy nowe zasady i Ustaw stan na **włączone**.


## <a name="test-sign-in-by-using-azure"></a>Testowanie logowania przy użyciu platformy Azure

1. Korzystając z sesji przeglądarki w trybie prywatnym, przejdź do `https://<address>` .  
2. Logowanie powinno zostać przekierowanie do usługi Azure AD w celu zalogowania się.
3. Po podaniu poświadczeń dla użytkownika, który został przypisany do aplikacji FortiGate w dzierżawie platformy Azure, powinien zostać wyświetlony odpowiedni Portal użytkownika.

    ![Zrzut ekranu przedstawiający sieć VPN FortiGate SSL](test-sign-in.png)
