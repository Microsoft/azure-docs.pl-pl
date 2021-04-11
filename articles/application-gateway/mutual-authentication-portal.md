---
title: Konfigurowanie uwierzytelniania wzajemnego na platformie Azure Application Gateway za pomocą portalu
description: Informacje dotyczące konfigurowania Application Gateway w celu uwierzytelniania wzajemnego za poorednictwem portalu
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231515"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>Konfigurowanie uwierzytelniania wzajemnego za pomocą Application Gateway za pomocą portalu (wersja zapoznawcza)

W tym artykule opisano sposób użycia Azure Portal w celu skonfigurowania uwierzytelniania wzajemnego na Application Gateway. Uwierzytelnianie wzajemne polega na tym, Application Gateway uwierzytelnia klienta wysyłającego żądanie przy użyciu przekazanego certyfikatu klienta do Application Gateway. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby można było skonfigurować uwierzytelnianie wzajemne przy użyciu Application Gateway, do bramy należy przekazać certyfikat klienta. Certyfikat klienta będzie używany do weryfikacji certyfikatu, który zostanie Application Gateway przez klienta. Do celów testowych można użyć certyfikatu z podpisem własnym. Nie jest to jednak zalecane w przypadku obciążeń produkcyjnych, ponieważ są one trudniejsze do zarządzania i nie są całkowicie bezpieczne. 

Aby dowiedzieć się więcej, szczególnie o rodzaju certyfikatów klienta, które można przekazać, zobacz [Omówienie uwierzytelniania wzajemnego za pomocą Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-new-application-gateway"></a>Utwórz nowy Application Gateway

Najpierw utwórz nowy Application Gateway jak zwykle za pomocą portalu — nie trzeba wykonywać żadnych dodatkowych kroków w celu włączenia uwierzytelniania wzajemnego. Aby uzyskać więcej informacji na temat tworzenia Application Gateway w portalu, zapoznaj się z [samouczkiem szybki start dla portalu](./quick-create-portal.md).

## <a name="configure-mutual-authentication"></a>Konfigurowanie uwierzytelniania wzajemnego 

Aby skonfigurować istniejące Application Gateway przy użyciu wzajemnego uwierzytelniania, należy najpierw przejść do karty **Ustawienia protokołu SSL (wersja zapoznawcza)** w portalu i utworzyć nowy profil SSL. Po utworzeniu profilu SSL zostaną wyświetlone dwie karty: **uwierzytelnianie klienta** i **zasady SSL**. Na karcie **uwierzytelnianie klienta** zostanie przekazana liczba certyfikatów klienta. Karta **zasady SSL** służy do konfigurowania zasad protokołu SSL specyficznych dla odbiorników — Aby uzyskać więcej informacji, zapoznaj się z tematem [Konfigurowanie zasad protokołu SSL określonych przez odbiornik](./application-gateway-configure-listener-specific-ssl-policy.md).

> [!IMPORTANT]
> Upewnij się, że cały łańcuch certyfikatów klienta urzędu certyfikacji został przekazany w jednym pliku, i tylko jeden łańcuch na plik.

1. Wyszukaj **Application Gateway** w portalu, wybierz pozycję **Application Gateways** i kliknij istniejące Application Gateway.

2. Wybierz pozycję **Ustawienia protokołu SSL (wersja zapoznawcza)** w menu po lewej stronie.

3. Kliknij znak plus obok pozycji **Profile SSL** u góry, aby utworzyć nowy profil protokołu SSL.

4. Wprowadź nazwę w polu **Nazwa profilu protokołu SSL**. W tym przykładzie dzwonimy do naszego profilu SSL *applicationGatewaySSLProfile*. 

5. Pozostań na karcie **uwierzytelnianie klienta** . Przekaż certyfikat PEM, którego zamierzasz używać do wzajemnego uwierzytelniania między klientem a Application Gateway przy użyciu przycisku **Przekaż nowy certyfikat** . 

    Aby uzyskać więcej informacji na temat wyodrębniania zaufanych łańcuchów certyfikatów urzędu certyfikacji klienta do przekazywania w tym miejscu, zobacz [Jak wyodrębnić łańcuch certyfikatów zaufanego urzędu certyfikacji klienta](./mutual-authentication-certificate-management.md).

   > [!NOTE]
   > Jeśli nie jest to Twój pierwszy profil SSL, a inne certyfikaty klienta zostały przekazane do Application Gateway, możesz wybrać ponowne użycie istniejącego certyfikatu na bramie za pośrednictwem menu rozwijanego. 

6. Upewnij się, że pole **nazwa wyróżniająca wystawcy certyfikatu klienta** ma być sprawdzane tylko wtedy, gdy chcesz, aby Application Gateway zweryfikować nazwę wyróżniającą bezpośrednie wystawcy certyfikatu klienta. 

7. Rozważ dodanie określonych zasad odbiornika. Zobacz instrukcje dotyczące [konfigurowania zasad protokołu SSL specyficznych dla odbiornika](./application-gateway-configure-listener-specific-ssl-policy.md).

8. Wybierz pozycję **Dodaj** , aby zapisać.
    > [!div class="mx-imgBorder"]
    > ![Dodaj uwierzytelnianie klienta do profilu SSL](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>Kojarzenie profilu SSL z odbiornikiem

Po utworzeniu profilu SSL z konfiguracją wzajemnego uwierzytelniania musimy skojarzyć profil SSL z odbiornikiem, aby zakończyć konfigurowanie uwierzytelniania wzajemnego. 

1. Przejdź do istniejącego Application Gateway. Jeśli wykonano powyższe kroki, nie trzeba wykonywać żadnych czynności w tym miejscu. 

2. Wybierz pozycję **detektory** z menu po lewej stronie. 

3. Kliknij pozycję **Dodaj odbiornik** , jeśli nie masz jeszcze skonfigurowanego odbiornika https. Jeśli masz już odbiornik HTTPS, kliknij go z listy. 

4. Podaj **nazwę odbiornika**, **adres IP frontonu**, **port**, **Protokół** i inne **Ustawienia protokołu HTTPS** , aby dopasować je do swoich wymagań.

5. Zaznacz pole wyboru **Włącz profil protokołu SSL** , aby można było wybrać profil protokołu SSL, który ma zostać skojarzony z odbiornikiem. 

6. Wybierz właśnie utworzony profil SSL z listy rozwijanej. W tym przykładzie wybieramy profil SSL, który został utworzony na podstawie wcześniejszych kroków: *applicationGatewaySSLProfile*. 

7. Kontynuuj Konfigurowanie pozostałej części odbiornika zgodnie z wymaganiami. 

8. Kliknij przycisk **Dodaj** , aby zapisać nowy odbiornik z przypisanym do niego profilem SSL. 

    > [!div class="mx-imgBorder"]
    > ![Skojarz profil SSL z nowym odbiornikiem](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>Odnów wygasłe certyfikaty urzędu certyfikacji klienta

W przypadku wygaśnięcia certyfikatu urzędu certyfikacji klienta możesz zaktualizować certyfikat na bramie, wykonując następujące czynności: 

1. Przejdź do Application Gateway i przejdź do karty **Ustawienia protokołu SSL (wersja zapoznawcza)** w menu po lewej stronie. 
 
1. Wybierz istniejące profile SSL z wygasłym certyfikatem klienta. 
 
1. Wybierz pozycję **Przekaż nowy certyfikat** na karcie **uwierzytelnianie klienta** i przekaż nowy certyfikat klienta. 
 
1. Wybierz ikonę kosza obok wygasłego certyfikatu. Spowoduje to usunięcie skojarzenia tego certyfikatu z profilu SSL. 

1. Powtórz kroki 2-4 powyżej z dowolnym innym profilem SSL, który używa tego samego wygasłego certyfikatu klienta. Będziesz mieć możliwość wybrania nowego certyfikatu przekazanego w kroku 3 z menu rozwijanego w innych profilach SSL.

## <a name="next-steps"></a>Następne kroki

- [zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)