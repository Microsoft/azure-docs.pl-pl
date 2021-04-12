---
title: Samouczek dotyczący konfigurowania certyfikatów dla urządzeń Azure Stack Edge mini R w Azure Portal | Microsoft Docs
description: Samouczek dotyczący wdrażania Azure Stack Edge mini R instruuje użytkownika o skonfigurowaniu certyfikatów na urządzeniu fizycznym.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 42863c942314d3cb76c7536edc79b08e132f8fa2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063002"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>Samouczek: Konfigurowanie certyfikatów, sieci VPN, szyfrowania dla Azure Stack Edge mini R

W tym samouczku opisano sposób konfigurowania certyfikatów, sieci VPN i szyfrowania w trybie REST dla urządzenia Azure Stack Edge mini R przy użyciu lokalnego interfejsu użytkownika sieci Web.

Czas poświęcony na ten krok może się różnić w zależności od wybranej opcji i sposobu ustanowienia przepływu certyfikatów w środowisku.

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfigurowanie certyfikatów dla urządzenia fizycznego
> * Konfigurowanie sieci VPN
> * Skonfiguruj szyfrowanie w spoczynku

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia z systemem Azure Stack Edge mini R upewnij się, że:

* Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w temacie [Install Azure Stack Edge mini R](azure-stack-edge-mini-r-deploy-install.md).

* Jeśli planujesz przeprowadzenie własnych certyfikatów:
    - Certyfikaty powinny być gotowe w odpowiednim formacie, łącznie z certyfikatem łańcucha podpisywania. Aby uzyskać szczegółowe informacje na temat certyfikatu, przejdź do obszaru [Zarządzanie certyfikatami](azure-stack-edge-gpu-manage-certificates.md)

    - Jeśli urządzenie zostało wdrożone w Azure Government lub Azure Government Secret lub Azure Government najwyższej poufnej chmurze i nie zostało wdrożone w chmurze publicznej platformy Azure, przed aktywowaniem urządzenia jest wymagany certyfikat łańcucha podpisywania. 
    Aby uzyskać szczegółowe informacje na temat certyfikatu, przejdź do obszaru [Zarządzanie certyfikatami](azure-stack-edge-gpu-manage-certificates.md).


## <a name="configure-certificates-for-device"></a>Konfigurowanie certyfikatów dla urządzenia

1. Na stronie **Certyfikaty** skonfigurujesz certyfikaty. W zależności od tego, czy zmieniono nazwę urządzenia lub domenę DNS na stronie **urządzenia** , można wybrać jedną z następujących opcji certyfikatów.

    - Jeśli domyślna nazwa urządzenia lub domyślna domena DNS nie została zmieniona we wcześniejszym kroku i nie chcesz przenosić własnych certyfikatów, możesz pominąć ten krok i przejść do następnego kroku. Urządzenie automatycznie wygenerowało certyfikaty z podpisem własnym, które zaczynają się od. 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - W przypadku zmiany nazwy urządzenia lub domeny DNS zobaczysz, że stan certyfikatów będzie wyświetlany jako **nieprawidłowy**. 

        ![Lokalny interfejs użytkownika sieci Web "certyfikaty" Strona 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        Wybierz certyfikat, aby wyświetlić szczegóły stanu.

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        Stan certyfikatu jest **nieprawidłowy** , ponieważ certyfikaty nie odzwierciedlają zaktualizowanej nazwy urządzenia i domeny DNS (które są używane w nazwie podmiotu i alternatywie podmiotu). Aby pomyślnie aktywować urządzenie, można przenieść własne podpisane certyfikaty punktów końcowych i odpowiednie łańcuchy podpisywania. Najpierw należy dodać łańcuch podpisywania, a następnie przekazać certyfikaty punktów końcowych. Aby uzyskać więcej informacji, przejdź do pozycji [Przenieś własne certyfikaty na urządzeniu Azure Stack Edge mini R](#bring-your-own-certificates).


    - Jeśli zmieniono nazwę urządzenia lub domenę DNS, a użytkownik nie przyniesie własnych certyfikatów, **Aktywacja zostanie zablokowana**.


#### <a name="bring-your-own-certificates"></a>Korzystanie z własnego certyfikatu

Łańcuch podpisywania został już dodany we wcześniejszym kroku na tym urządzeniu. Teraz można przekazać certyfikaty punktu końcowego, certyfikat węzła, certyfikat lokalnego interfejsu użytkownika i certyfikat sieci VPN. Wykonaj następujące kroki, aby dodać własne certyfikaty.

1. Aby przekazać certyfikat, na stronie **certyfikat** wybierz pozycję **+ Dodaj certyfikat**.

    [![Lokalny interfejs użytkownika sieci Web "certyfikaty" — Strona 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. Można przekazać inne certyfikaty. Można na przykład przekazać Azure Resource Manager i certyfikaty punktów końcowych usługi BLOB Storage.

    ![Strona "certyfikaty" w lokalnym interfejsie użytkownika sieci Web](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. Możesz również przekazać certyfikat lokalnego interfejsu użytkownika sieci Web. Po przekazaniu tego certyfikatu konieczne będzie uruchomienie przeglądarki i wyczyszczenie pamięci podręcznej. Następnie należy nawiązać połączenie z lokalnym interfejsem użytkownika sieci Web urządzenia.  

    ![Lokalny interfejs użytkownika sieci Web "certyfikaty", Strona 7](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. Możesz również przekazać certyfikat węzła.


    ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. Na koniec można przekazać certyfikat sieci VPN.
        
    ![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. W dowolnym momencie możesz wybrać certyfikat i wyświetlić szczegóły, aby upewnić się, że są one zgodne z przekazanym certyfikatem.

    [![Lokalny interfejs użytkownika sieci Web "certyfikaty" Strona 9](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    Strona certyfikat powinna zostać zaktualizowana w celu odzwierciedlenia nowo dodanych certyfikatów.

    [![Strona "certyfikaty" lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > Poza chmurą publiczną platformy Azure w przypadku wszystkich konfiguracji chmury (Azure Government lub Azure Stack Hub muszą zostać wprowadzone certyfikaty łańcucha podpisywania).


## <a name="configure-vpn"></a>Konfigurowanie sieci VPN

1. Na kafelku **zabezpieczenia** wybierz pozycję **Konfiguruj** dla sieci VPN. 

    Aby skonfigurować sieć VPN, należy najpierw upewnić się, że wszystkie niezbędne konfiguracje zostały wykonane na platformie Azure. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie sieci VPN za pośrednictwem programu PowerShell dla urządzenia Azure Stack Edge mini R](azure-stack-edge-placeholder.md). Po zakończeniu tego procesu można skonfigurować konfigurację w lokalnym interfejsie użytkownika.
    
    1. Na stronie sieć VPN wybierz pozycję **Konfiguruj**.
        ![Konfigurowanie lokalnego interfejsu użytkownika sieci VPN 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. W bloku **Konfigurowanie sieci VPN** :

        1. Podaj książkę telefoniczną jako dane wejściowe.
        2. Podaj plik JSON zakresu adresów IP usługi Azure Data Center jako dane wejściowe. Pobierz ten plik z: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
        3. Wybierz **wschód** jako region.
        4. Wybierz przycisk **Zastosuj**.

        ![Konfigurowanie lokalnego interfejsu użytkownika sieci VPN 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. Skonfiguruj zakresy adresów IP, które mają być dostępne tylko za pośrednictwem sieci VPN. 
    
        - W obszarze **zakresy adresów IP, które mają być dostępne tylko przy użyciu sieci VPN**, wybierz pozycję **Konfiguruj**.
        - Wprowadź zakres adresów IPv4 sieci wirtualnej, który został wybrany dla Virtual Network platformy Azure.
        - Wybierz przycisk **Zastosuj**.
    
        ![Konfigurowanie lokalnego interfejsu użytkownika sieci VPN 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

Urządzenie jest teraz gotowe do zaszyfrowania. Skonfiguruj szyfrowanie w stanie spoczynku.


## <a name="enable-encryption"></a>Włącz szyfrowanie

1. Na kafelku **zabezpieczenia** wybierz pozycję **Konfiguruj** , aby włączyć szyfrowanie na poziomie spoczynku. To ustawienie jest wymagane, a do momentu jego pomyślnego skonfigurowania nie można aktywować urządzenia. 

    ![Lokalny interfejs użytkownika sieci Web "szyfrowanie w spoczynku" Strona 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    Po umieszczeniu obrazu na urządzeniach w fabryce włączone jest szyfrowanie za pomocą funkcji BitLocker na poziomie woluminu. Po otrzymaniu urządzenia należy skonfigurować szyfrowanie w usłudze Rest. Pula magazynów i woluminy są ponownie tworzone i można udostępnić klucze funkcji BitLocker, aby włączyć szyfrowanie w spoczynku i utworzyć drugą warstwę szyfrowania dla danych — w stanie spoczynku.

1. W okienku **szyfrowanie w czasie spoczynku** wprowadź 32 znaków Long (AES-256 bit) Base-64 zakodowany klucz. Jest to jednorazowa konfiguracja, a ten klucz jest używany do ochrony rzeczywistego klucza szyfrowania. 

    ![Lokalny interfejs użytkownika sieci Web "szyfrowanie w spoczynku" Strona 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    Możesz również wybrać opcję automatycznego generowania tego klucza.

    ![Lokalny interfejs użytkownika sieci Web "szyfrowanie w spoczynku" Strona 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Wybierz przycisk **Zastosuj**. Ta operacja trwa kilka minut, a stan operacji jest wyświetlany na kafelku **zabezpieczenia** .

    ![Lokalny interfejs użytkownika sieci Web — szyfrowanie na stronie 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Po wyświetleniu stanu jako **ukończono** Wróć do pozycji **wprowadzenie**.

Urządzenie jest teraz gotowe do aktywacji.

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Konfigurowanie certyfikatów dla urządzenia fizycznego
> * Konfigurowanie sieci VPN
> * Skonfiguruj szyfrowanie w spoczynku

Aby dowiedzieć się, jak aktywować urządzenie z usługą Azure Stack Edge mini R, zobacz:

> [!div class="nextstepaction"]
> [Aktywuj urządzenie z urządzeniem Azure Stack Edge mini R](./azure-stack-edge-mini-r-deploy-activate.md)
