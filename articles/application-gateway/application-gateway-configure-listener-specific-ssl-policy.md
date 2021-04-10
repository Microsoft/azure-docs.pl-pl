---
title: Konfigurowanie zasad protokołu SSL specyficznych dla odbiornika na platformie Azure Application Gateway za pośrednictwem portalu
description: Informacje dotyczące konfigurowania zasad protokołu SSL specyficznych dla odbiornika na Application Gateway za pomocą portalu
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231576"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>Konfigurowanie zasad protokołu SSL specyficznych dla odbiornika na Application Gateway za pośrednictwem portalu (wersja zapoznawcza)

W tym artykule opisano sposób użycia Azure Portal w celu skonfigurowania zasad protokołu SSL specyficznych dla odbiornika na Application Gateway. Zasady protokołu SSL dla konkretnych odbiorników umożliwiają skonfigurowanie określonych odbiorników do używania różnych zasad protokołu SSL. Nadal będzie można ustawić domyślne zasady protokołu SSL, które będą używane przez wszystkie odbiorniki, chyba że zostaną zastąpione przez zasady SSL specyficzne dla odbiornika. 

> [!NOTE]
> Tylko Standard_v2 i WAF_v2 jednostki SKU obsługują zasady dotyczące odbiorników, ponieważ zasady charakterystyczne dla odbiorników są częścią profilów SSL, a profile SSL są obsługiwane tylko w przypadku bram v2. 



Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-new-application-gateway"></a>Utwórz nowy Application Gateway

Najpierw utwórz nowy Application Gateway jak zwykle za pośrednictwem portalu — nie trzeba wykonywać żadnych dodatkowych kroków w celu skonfigurowania zasad protokołu SSL specyficznych dla odbiornika. Aby uzyskać więcej informacji na temat tworzenia Application Gateway w portalu, zapoznaj się z [samouczkiem szybki start dla portalu](./quick-create-portal.md).

## <a name="set-up-a-listener-specific-ssl-policy"></a>Konfigurowanie zasad protokołu SSL specyficznych dla odbiornika

Aby skonfigurować zasady protokołu SSL dla konkretnych odbiorników, należy najpierw przejść do karty **Ustawienia protokołu SSL (wersja zapoznawcza)** w portalu i utworzyć nowy profil SSL. Po utworzeniu profilu SSL zostaną wyświetlone dwie karty: **uwierzytelnianie klienta** i **zasady SSL**. Karta **zasady SSL** służy do konfigurowania zasad protokołu SSL specyficznych dla odbiornika. Karta **uwierzytelnianie klienta** zawiera informacje o przekazywaniu certyfikatów klienta na potrzeby wzajemnego uwierzytelniania — Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania wzajemnego](./mutual-authentication-portal.md).

> [!NOTE]
> Zalecamy korzystanie z protokołu TLS 1,2 jako protokołu TLS 1,2 w przyszłości będzie to możliwe. 

1. Wyszukaj **Application Gateway** w portalu, wybierz pozycję **Application Gateways** i kliknij istniejące Application Gateway.

2. Wybierz pozycję **Ustawienia protokołu SSL (wersja zapoznawcza)** w menu po lewej stronie.

3. Kliknij znak plus obok pozycji **Profile SSL** u góry, aby utworzyć nowy profil protokołu SSL.

4. Wprowadź nazwę w polu **Nazwa profilu protokołu SSL**. W tym przykładzie dzwonimy do naszego profilu SSL *applicationGatewaySSLProfile*. 

5. Przejdź do karty **zasady protokołu SSL** i zaznacz pole wyboru **Włącz zasady protokołu SSL dla określonego odbiornika** . 

6. Skonfiguruj zasady protokołu SSL dla konkretnych odbiorników, uwzględniając odpowiednie wymagania. Można wybrać między wstępnie zdefiniowanymi zasadami protokołu SSL i dostosować własne zasady SSL. Aby uzyskać więcej informacji na temat zasad protokołu SSL, zobacz [Omówienie zasad protokołu SSL](./application-gateway-ssl-policy-overview.md). Zalecamy korzystanie z protokołu TLS 1,2

7. Wybierz pozycję **Dodaj** , aby zapisać.

    > [!NOTE]
    > Nie trzeba konfigurować uwierzytelniania klienta w profilu SSL, aby skojarzyć go z odbiornikiem. Można skonfigurować tylko uwierzytelnianie klienta lub tylko określone zasady protokołu SSL lub skonfigurować je w profilu SSL.  

    ![Dodaj zasady protokołu SSL specyficzne dla odbiornika do profilu SSL](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>Kojarzenie profilu SSL z odbiornikiem

Po utworzeniu profilu SSL z zasadami protokołu SSL specyficznymi dla odbiornika musimy skojarzyć profil SSL z odbiornikiem, aby ustawić w akcji zasady dotyczące odbiornika. 

1. Przejdź do istniejącego Application Gateway. Jeśli wykonano powyższe kroki, nie trzeba wykonywać żadnych czynności w tym miejscu. 

2. Wybierz pozycję **detektory** z menu po lewej stronie. 

3. Kliknij pozycję **Dodaj odbiornik** , jeśli nie masz jeszcze skonfigurowanego odbiornika https. Jeśli masz już odbiornik HTTPS, kliknij go z listy. 

4. Podaj **nazwę odbiornika**, **adres IP frontonu**, **port**, **Protokół** i inne **Ustawienia protokołu HTTPS** , aby dopasować je do swoich wymagań.

5. Zaznacz pole wyboru **Włącz profil protokołu SSL** , aby można było wybrać profil protokołu SSL, który ma zostać skojarzony z odbiornikiem. 

6. Wybierz utworzony profil SSL z listy rozwijanej. W tym przykładzie wybieramy profil SSL, który został utworzony na podstawie wcześniejszych kroków: *applicationGatewaySSLProfile*. 

7. Kontynuuj Konfigurowanie pozostałej części odbiornika zgodnie z wymaganiami. 

8. Kliknij przycisk **Dodaj** , aby zapisać nowy odbiornik z przypisanym do niego profilem SSL. 

    ![Skojarz profil SSL z nowym odbiornikiem](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)