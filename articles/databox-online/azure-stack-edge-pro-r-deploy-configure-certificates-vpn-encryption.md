---
title: Samouczek dotyczący konfigurowania certyfikatów dla urządzeń z Azure Stack EDGE Pro R w Azure Portal | Microsoft Docs
description: Samouczek dotyczący wdrażania Azure Stack EDGE Pro R instruuje użytkownika o skonfigurowaniu certyfikatów na urządzeniu fizycznym.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: abea192f209b968c6842e985620c067c93990687
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548152"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>Samouczek: Konfigurowanie certyfikatów dla Azure Stack EDGE Pro R

W tym samouczku opisano, jak można skonfigurować certyfikaty dla urządzenia z Azure Stack EDGE Pro R przy użyciu lokalnego interfejsu użytkownika sieci Web.

Czas poświęcony na ten krok może się różnić w zależności od wybranej opcji i sposobu ustanowienia przepływu certyfikatów w środowisku.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfigurowanie certyfikatów dla urządzenia fizycznego
> * Konfigurowanie sieci VPN
> * Konfigurowanie szyfrowania w czasie spoczynku

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia z systemem Azure Stack EDGE Pro R upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-install.md).
* Jeśli planujesz przeprowadzenie własnych certyfikatów:
    - Certyfikaty powinny być gotowe w odpowiednim formacie, łącznie z certyfikatem łańcucha podpisywania. Aby uzyskać szczegółowe informacje na temat certyfikatu, przejdź do obszaru [Zarządzanie certyfikatami](azure-stack-edge-gpu-manage-certificates.md)



## <a name="configure-certificates-for-device"></a>Konfigurowanie certyfikatów dla urządzenia

1. Na stronie **Certyfikaty** skonfigurujesz certyfikaty. W zależności od tego, czy zmieniono nazwę urządzenia lub domenę DNS na stronie **urządzenia** , można wybrać jedną z następujących opcji certyfikatów.

    - Jeśli nie zmieniono nazwy urządzenia lub domeny DNS we wcześniejszym kroku, można pominąć ten krok i przejść do następnego kroku. Urządzenie automatycznie wygenerowało certyfikaty z podpisem własnym, które zaczynają się od. 

    - W przypadku zmiany nazwy urządzenia lub domeny DNS zobaczysz, że stan certyfikatów będzie wyświetlany jako **nieprawidłowy**. 

        ![Lokalny interfejs użytkownika sieci Web "certyfikaty" Strona 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        Wybierz certyfikat, aby wyświetlić szczegóły stanu.

        ![Lokalny interfejs użytkownika sieci Web "certyfikaty" — Strona 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        Wynika to z faktu, że certyfikaty nie odzwierciedlają zaktualizowanej nazwy urządzenia i domeny DNS (które są używane w polu Nazwa podmiotu i alternatywny podmiot). Aby pomyślnie aktywować urządzenie, można przenieść własne podpisane certyfikaty punktów końcowych i odpowiednie łańcuchy podpisywania. Najpierw należy dodać łańcuch podpisywania, a następnie przekazać certyfikaty punktów końcowych. Aby uzyskać więcej informacji, przejdź do pozycji [przenoszenie własnych certyfikatów na urządzeniu Azure Stack EDGE Pro R](#bring-your-own-certificates).

    - Jeśli zmieniono nazwę urządzenia lub domenę DNS, a użytkownik nie przyniesie własnych certyfikatów, **Aktywacja zostanie zablokowana**.

    
#### <a name="bring-your-own-certificates"></a>Korzystanie z własnego certyfikatu

Wykonaj następujące kroki, aby dodać własne certyfikaty, w tym łańcuch podpisywania.

1. Aby przekazać certyfikat, na stronie **certyfikat** wybierz pozycję **+ Dodaj certyfikat**.

    ![Lokalny interfejs użytkownika sieci Web "certyfikaty" — Strona 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. Najpierw przekaż łańcuch podpisywania i wybierz pozycję **weryfikuj & Dodaj**.

    ![Strona lokalnego interfejsu użytkownika sieci Web "certyfikaty" 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. Teraz można przekazać inne certyfikaty. Można na przykład przekazać Azure Resource Manager i certyfikaty punktów końcowych usługi BLOB Storage.

    ![Strona "certyfikaty" w lokalnym interfejsie użytkownika sieci Web](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    Możesz również przekazać certyfikat lokalnego interfejsu użytkownika sieci Web. Po przekazaniu tego certyfikatu konieczne będzie uruchomienie przeglądarki i wyczyszczenie pamięci podręcznej. Następnie należy nawiązać połączenie z lokalnym interfejsem użytkownika sieci Web urządzenia.  

    ![Lokalny interfejs użytkownika sieci Web "certyfikaty", Strona 7](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    Możesz również przekazać certyfikat węzła.

    ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Na koniec można przekazać certyfikat sieci VPN.

    ![Lokalny interfejs użytkownika sieci Web "certyfikaty" Strona 9](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    W dowolnym momencie możesz wybrać certyfikat i wyświetlić szczegóły, aby upewnić się, że są one zgodne z przekazanym certyfikatem.

    Strona certyfikat powinna zostać zaktualizowana w celu odzwierciedlenia nowo dodanych certyfikatów.

    ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > Poza chmurą publiczną platformy Azure w przypadku wszystkich konfiguracji chmury (Azure Government lub Azure Stack) należy wprowadzić certyfikaty łańcucha podpisywania.

1. Wybierz pozycję **< z powrotem, aby** rozpocząć.

## <a name="configure-vpn"></a>Konfigurowanie sieci VPN

1. Na kafelku **zabezpieczenia** wybierz pozycję **Konfiguruj** dla sieci VPN.

    ![Strona lokalnego interfejsu użytkownika sieci Web "VPN"](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    Aby skonfigurować sieć VPN, należy najpierw upewnić się, że wszystkie niezbędne konfiguracje zostały wykonane na platformie Azure. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie wymagań wstępnych](azure-stack-edge-placeholder.md) i [Konfigurowanie zasobów platformy Azure dla sieci VPN](azure-stack-edge-placeholder.md). Po zakończeniu tego procesu można skonfigurować konfigurację w lokalnym interfejsie użytkownika.
    
    1. Na stronie sieć VPN wybierz pozycję **Konfiguruj**.
    2. W bloku **Konfigurowanie sieci VPN** :

    - Włącz **Ustawienia sieci VPN**.
    - Podaj wspólny **klucz tajny sieci VPN**. Jest to klucz współużytkowany podany podczas tworzenia obiektu połączenia sieci VPN platformy Azure.
    - Podaj adres **IP bramy sieci VPN** . Jest to adres IP bramy sieci lokalnej platformy Azure.
    - W obszarze **Grupa PFS** wybierz opcję **Brak**. 
    - Dla **grupy DH** wybierz pozycję **group2**.
    - W przypadku **metody integralności protokołu IPSec** wybierz pozycję **SHA256**.
    - W przypadku **stałych transformacji IPseccipher** wybierz opcję **GCMAES256**.
    - W przypadku **stałych transformacji uwierzytelniania protokołu IPSec** wybierz pozycję **GCMAES256**.
    - W przypadku **metody szyfrowania IKE** wybierz pozycję **AES256**.
    - Wybierz przycisk **Zastosuj**.

        ![Konfigurowanie lokalnego interfejsu użytkownika 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. Aby przekazać plik konfiguracji trasy sieci VPN, wybierz pozycję **Przekaż**. 
    
        ![Konfigurowanie lokalnego interfejsu użytkownika 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - Przejdź do pliku *JSON* konfiguracji sieci VPN pobranego w systemie lokalnym w poprzednim kroku.
        - Wybierz region jako region platformy Azure skojarzony z urządzeniem, siecią wirtualną i bramami.
        - Wybierz przycisk **Zastosuj**.
    
            ![Konfigurowanie lokalnego interfejsu użytkownika 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. Aby dodać trasy specyficzne dla klienta, skonfiguruj zakresy adresów IP, które mają być dostępne tylko przy użyciu sieci VPN. 
    
        - W obszarze **zakresy adresów IP, które mają być dostępne tylko przy użyciu sieci VPN**, wybierz pozycję **Konfiguruj**.
        - Podaj prawidłowy zakres adresów IPv4 i wybierz pozycję **Dodaj**. Powtórz kroki, aby dodać inne zakresy.
        - Wybierz przycisk **Zastosuj**.
    
            ![Konfigurowanie lokalnego interfejsu użytkownika 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. Wybierz pozycję **< z powrotem, aby** rozpocząć.

## <a name="configure-encryption-at-rest"></a>Konfigurowanie szyfrowania w czasie spoczynku

1. Na kafelku **zabezpieczenia** wybierz pozycję **Konfiguruj** , aby włączyć szyfrowanie na poziomie spoczynku. To ustawienie jest wymagane, a do momentu jego pomyślnego skonfigurowania nie można aktywować urządzenia. 

    Po umieszczeniu obrazu na urządzeniach w fabryce włączone jest szyfrowanie za pomocą funkcji BitLocker na poziomie woluminu. Po otrzymaniu urządzenia należy skonfigurować szyfrowanie w usłudze Rest. Pula magazynów i woluminy są ponownie tworzone i można udostępnić klucze funkcji BitLocker, aby włączyć szyfrowanie w spoczynku i utworzyć drugą warstwę szyfrowania dla danych — w stanie spoczynku.

1. W okienku **szyfrowanie w czasie spoczynku** Podaj 32 znak o długości długiej Base-64 zakodowany. Jest to jednorazowa konfiguracja, a ten klucz jest używany do ochrony rzeczywistego klucza szyfrowania. Możesz wybrać opcję automatycznego generowania tego klucza lub wprowadzić ją.

    ![Okienko lokalnego interfejsu użytkownika sieci Web "szyfrowanie w stanie spoczynku"](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    Klucz jest zapisywany w pliku klucza na stronie **szczegółów chmury** po aktywowaniu urządzenia.

1. Wybierz przycisk **Zastosuj**. Ta operacja trwa kilka minut, a stan operacji jest wyświetlany na kafelku **zabezpieczenia** .

    ![Strona "szyfrowanie na poziomie lokalnego interfejsu użytkownika sieci Web"](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. Po wyświetleniu stanu jako **ukończono** wybierz pozycję **< z powrotem, aby rozpocząć**.

Urządzenie jest teraz gotowe do aktywacji. 


## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfigurowanie certyfikatów dla urządzenia fizycznego
> * Konfigurowanie sieci VPN
> * Konfigurowanie szyfrowania w czasie spoczynku

Aby dowiedzieć się, jak aktywować urządzenie Azure Stack Edge w programie R, zobacz:

> [!div class="nextstepaction"]
> [Aktywuj urządzenie Azure Stack Edge R](./azure-stack-edge-pro-r-deploy-activate.md)
