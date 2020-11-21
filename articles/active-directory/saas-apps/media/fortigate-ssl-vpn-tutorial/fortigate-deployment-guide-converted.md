---
title: Przewodnik wdrażania FortiGate | Microsoft Docs
description: Skonfiguruj i pracuj z systemem Fortinet FortiGate nowej generacji zapory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: cdaa6a9601452100ab90ef8b0f2191002f256b74
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025529"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>Przewodnik wdrażania maszyn wirtualnych FortiGate platformy Azure

Korzystając z tego przewodnika wdrażania, dowiesz się, jak skonfigurować i współpracować z produktem FortiGate nowej generacji zapory wdrożonym jako maszynę wirtualną platformy Azure. Ponadto skonfigurujesz aplikację FortiGate protokołu SSL sieci VPN usługi Azure AD Gallery, aby zapewnić Uwierzytelnianie sieci VPN za pośrednictwem Azure Active Directory.

## <a name="redeem-the-fortigate-license"></a>Zrealizuj licencję FortiGate

Produkt zapory FortiGate nowej generacji jest dostępny jako maszyna wirtualna w infrastrukturze platformy Azure jako usługa (IaaS). Dla tej maszyny wirtualnej są dostępne dwa tryby licencjonowania: płatność zgodnie z rzeczywistym użyciem i BYOL.

Jeśli zakupiono licencję FortiGate od firmy Fortinet do użycia z opcją wdrożenia maszyny wirtualnej BYOL, Zrealizuj ją na stronie aktywacji produktu firmy Fortinet — https://support.fortinet.com . Plik z uzyskaną licencją będzie miał rozszerzenie. lic.

## <a name="download-firmware"></a>Pobierz oprogramowanie układowe

W momencie pisania, maszyna wirtualna platformy Azure FortiGate nie jest dostarczana z wersją oprogramowania układowego wymaganą do uwierzytelniania za pomocą protokołu SAML. Najnowszą wersję należy uzyskać od firmy Fortinet.

1. Zaloguj się pod adresem https://support.fortinet.com/ .
2. Przejdź do pozycji **Pobierz**  >  **obrazy oprogramowania układowego**.
3. Na prawo od **informacji o wersji** wybierz pozycję **Pobierz**.
4. Wybierz pozycję **v 6.00**  >  **6,4**  >  **6.4.2**.
5. Pobierz **FGT_VM64_AZURE-V6-build1723-Fortinet. out** , wybierając łącze **https** w tym samym wierszu.
6. Zapisz plik do nowszej wersji.

## <a name="deploy-the-fortigate-vm"></a>Wdróż maszynę wirtualną FortiGate

1. Przejdź do Azure Portal i zaloguj się do subskrypcji, w której zostanie wdrożona maszyna wirtualna FortiGate.
2. Utwórz nową grupę zasobów lub Otwórz grupę zasobów, w której zostanie wdrożona maszyna wirtualna FortiGate.
3. Wybierz pozycję **Dodaj**.
4. W obszarze **Wyszukaj w portalu Marketplace** wprowadź *Forti*. Wybierz pozycję **Fortinet Fortigate nowej zapory**.
5. Wybierz plan oprogramowania (Jeśli masz licencję lub z opcją płatność zgodnie z rzeczywistym użyciem, jeśli nie jest to możliwe). Wybierz pozycję **Utwórz**.
6. Wypełnij konfigurację maszyny wirtualnej.

    ![Zrzut ekranu przedstawiający tworzenie maszyny wirtualnej.](virtual-machine.png)

7. Ustaw **Typ uwierzytelniania** na **hasło** i podaj poświadczenia administracyjne dla maszyny wirtualnej.
8. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**.
9. Poczekaj na zakończenie wdrożenia maszyny wirtualnej.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Ustaw statyczny publiczny adres IP i przypisz w pełni kwalifikowaną nazwę domeny

Aby zapewnić spójne środowisko użytkownika, ustaw publiczny adres IP przypisany do maszyny wirtualnej FortiGate, aby był przypisany statycznie. Ponadto należy zamapować ją na w pełni kwalifikowaną nazwę domeny (FQDN).

1. Przejdź do Azure Portal i Otwórz ustawienia dla maszyny wirtualnej FortiGate.
2. Na ekranie **Przegląd** wybierz publiczny adres IP.

    ![Zrzut ekranu przedstawiający Fortigate SSL sieci VPN.](public-ip-address.png)

3. Wybierz pozycję Zapisz **statyczny**  >  **Save**.

Jeśli posiadasz publiczną nazwę domeny routingu dla środowiska, w którym jest wdrażana maszyna wirtualna FortiGate, Utwórz rekord hosta (A) dla maszyny wirtualnej. Ten rekord jest mapowany na poprzedni publiczny adres IP, który jest przypisany statycznie.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>Utwórz nową regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego dla portu TCP 8443

1. Przejdź do Azure Portal i Otwórz ustawienia dla maszyny wirtualnej FortiGate.
2. W menu po lewej stronie wybierz pozycję **Sieć**. Zostanie wyświetlony interfejs sieciowy i są wyświetlane reguły portów ruchu przychodzącego.
3. Wybierz pozycję **Dodaj regułę portu wejściowego**.
4. Utwórz nową regułę portu ruchu przychodzącego dla protokołu TCP 8443.

    ![Zrzut ekranu przedstawiający dodanie reguły zabezpieczeń dla ruchu przychodzącego.](port-rule.png)

5. Wybierz pozycję **Dodaj**.

## <a name="create-a-second-virtual-nic-for-the-vm"></a>Utwórz drugą wirtualną kartę sieciową dla maszyny wirtualnej

Aby udostępnić zasoby wewnętrzne użytkownikom, należy dodać drugą wirtualną kartę sieciową do maszyny wirtualnej FortiGate. Virtual Network na platformie Azure, w której znajduje się wirtualna karta sieciowa, musi mieć połączenie z obsługą routingu do tych zasobów wewnętrznych.

1. Przejdź do Azure Portal i Otwórz ustawienia dla maszyny wirtualnej FortiGate.
2. Jeśli maszyna wirtualna FortiGate nie została już zatrzymana, wybierz pozycję **Zatrzymaj** i poczekaj na zamknięcie maszyny wirtualnej.
3. W menu po lewej stronie wybierz pozycję **Sieć**.
4. Wybierz pozycję **Dołącz interfejs sieciowy**.
5. Wybierz pozycję **Utwórz i Dołącz interfejs sieciowy**.
6. Skonfiguruj właściwości nowego interfejsu sieciowego, a następnie wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający tworzenie interfejsu sieciowego.](new-network-interface.png)

7. Uruchom maszynę wirtualną FortiGate.


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
4. W menu po lewej stronie wybierz **System** pozycję  >  **oprogramowanie układowe** systemu.
5. W obszarze **Zarządzanie oprogramowaniem układowym** wybierz pozycję **Przeglądaj**, a następnie wybierz pobrany wcześniej plik oprogramowania układowego.
6. Zignoruj ostrzeżenie i wybierz pozycję **Konfiguracja i uaktualnienie kopii zapasowej**.

    ![Zrzut ekranu zarządzania oprogramowaniem układowym.](backup-configure-upgrade.png)

7. Wybierz opcję **Kontynuuj**.
8. Po wyświetleniu monitu o zapisanie konfiguracji FortiGate (w pliku. conf) wybierz pozycję **Zapisz**.
9. Poczekaj, aż oprogramowanie układowe zostanie przekazane i zastosowane. Poczekaj na ponowne uruchomienie maszyny wirtualnej FortiGate.
10. Po ponownym uruchomieniu maszyny wirtualnej FortiGate Zaloguj się ponownie przy użyciu poświadczeń administratora.
11. Gdy zostanie wyświetlony monit o skonfigurowanie pulpitu nawigacyjnego, wybierz pozycję **dalej**.
12. Po rozpoczęciu filmu wideo samouczka wybierz **przycisk OK**.

### <a name="change-the-management-port-to-tcp-8443"></a>Zmień port zarządzania na TCP 8443

1. Przejdź do adresu `https://<address>`. W tym miejscu `<address>` jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate.

2. Kontynuuj poprzednie Błędy certyfikatów.
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate.
4. W menu po lewej stronie wybierz pozycję **system**.
5. W obszarze **Ustawienia administracyjne** zmień port HTTPS na **8443**, a następnie wybierz pozycję **Zastosuj**.
6. Po zastosowaniu zmiany przeglądarka spróbuje ponownie załadować stronę administracyjną, ale kończy się niepowodzeniem. Od teraz na stronie administrowania jest adres `https://<address>:8443` .

    ![Zrzut ekranu przedstawiający zdalny certyfikat przekazywania.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Przekaż certyfikat podpisywania SAML usługi Azure AD

1. Przejdź do adresu `https://<address>:8443`. W tym miejscu `<address>` jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate.

2. Kontynuuj poprzednie Błędy certyfikatów.
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate.
4. W menu po lewej stronie wybierz **System** pozycję  >  **Certyfikaty** systemu.
5. Wybierz pozycję **Importuj**  >  **certyfikat zdalny**.
6. Przejdź do certyfikatu pobranego z niestandardowego wdrożenia aplikacji FortiGate w dzierżawie platformy Azure. Zaznacz go, a następnie wybierz **przycisk OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Przekazywanie i Konfigurowanie niestandardowego certyfikatu SSL

Możesz chcieć skonfigurować maszynę wirtualną FortiGate przy użyciu własnego certyfikatu SSL, który obsługuje nazwę FQDN, której używasz. Jeśli masz dostęp do certyfikatu SSL spakowanego przy użyciu klucza prywatnego w formacie PFX, można go użyć do tego celu.

1. Przejdź do adresu `https://<address>:8443`. W tym miejscu `<address>` jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate.

2. Kontynuuj poprzednie Błędy certyfikatów.
3. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate.
4. W menu po lewej stronie wybierz **System** pozycję  >  **Certyfikaty** systemu.
5. Wybierz pozycję **Importuj** certyfikat  >  **lokalny certyfikat**  >  **PKCS #12**.
6. Przejdź do. Plik PFX zawierający certyfikat SSL i klucz prywatny.
7. Podaj. Hasło PFX i zrozumiała Nazwa certyfikatu. Następnie wybierz przycisk **OK**.
8. W menu po lewej stronie wybierz **System** pozycję  >  **Ustawienia** systemowe.
9. W obszarze **Ustawienia administracyjne** rozwiń listę obok pozycji **certyfikat serwera https** i wybierz certyfikat SSL zaimportowany wcześniej.
10. Wybierz pozycję **Zastosuj**.
11. Zamknij okno przeglądarki i przejdź do `https://<address>:8443` .
12. Zaloguj się przy użyciu poświadczeń administratora FortiGate. Powinien być teraz widoczny prawidłowy certyfikat SSL w użyciu.

### <a name="configure-authentication-timeout"></a>Konfigurowanie limitu czasu uwierzytelniania

1. Przejdź do Azure Portal i Otwórz ustawienia dla maszyny wirtualnej FortiGate.
2. W menu po lewej stronie wybierz pozycję **konsola szeregowa**.
3. Zaloguj się w konsoli szeregowej przy użyciu poświadczeń administratora maszyny wirtualnej FortiGate.
4. W konsoli szeregowej Uruchom następujące polecenia:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>Upewnij się, że interfejsy sieciowe uzyskują adresy IP

1. Przejdź do adresu `https://<address>:8443`. W tym miejscu `<address>` jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate.

2. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate.
3. W menu po lewej stronie wybierz pozycję **Sieć**.
4. W obszarze Sieć wybierz pozycję **interfejsy**.
5. Sprawdź PORT1 (interfejs zewnętrzny) i PORT2 (wewnętrzny interfejs), aby upewnić się, że uzyskują adresy IP z właściwej podsieci platformy Azure.
    a. Jeśli żaden z portów nie uzyska adresu IP z podsieci (za pośrednictwem protokołu DHCP), kliknij prawym przyciskiem myszy port i wybierz polecenie **Edytuj**.
    b. W obszarze tryb adresowania upewnij się, że wybrano opcję **DHCP** .
    c. Seelct **OK**.

    ![Zrzut ekranu przedstawiający adresowanie interfejsu sieciowego.](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>Upewnij się, że maszyna wirtualna FortiGate ma poprawną trasę do lokalnych zasobów firmy

Wieloadresowe maszyny wirtualne platformy Azure mają wszystkie interfejsy sieciowe w tej samej sieci wirtualnej (ale prawdopodobnie są to oddzielne podsieci). Często oznacza to, że oba interfejsy sieciowe mają połączenie z lokalnymi zasobami firmowymi publikowanymi za pośrednictwem usługi FortiGate. Z tego powodu konieczne jest utworzenie niestandardowych wpisów tras, które gwarantują wyjście ruchu z poprawnego interfejsu, gdy zostaną wykonane żądania dotyczące lokalnych zasobów firmowych.

1. Przejdź do adresu `https://<address>:8443`. W tym miejscu `<address>` jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej Fortigate.

2. Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiGate.
3. W menu po lewej stronie wybierz pozycję **Sieć**.
4. W obszarze Sieć wybierz pozycję **trasy statyczne**.
5. Wybierz pozycję **Utwórz nowy**.
6. Obok pozycji miejsce docelowe wybierz **podsieć**.
7. W obszarze podsieć Określ informacje o podsieci, w których znajdują się lokalne zasoby firmowe (np. 10.1.0.0/255.255.255.0).
8. Obok pozycji adres bramy Określ bramę w podsieci platformy Azure, w której jest połączona PORT2 (np. zwykle jest to wartość 1, taka jak 10.6.1.1)
9. Obok pozycji interfejs wybierz wewnętrzny interfejs sieciowy, PORT2
10. Wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający Konfigurowanie trasy.](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>Konfigurowanie sieci VPN FortiGate SSL

Wykonaj kroki opisane w temacie https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial
