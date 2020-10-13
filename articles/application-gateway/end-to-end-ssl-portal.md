---
title: Konfigurowanie kompleksowego szyfrowania TLS przy użyciu portalu
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak używać Azure Portal do tworzenia bramy aplikacji z kompleksowym szyfrowaniem TLS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 33240d1f44d2f26569791f72a3d5fc3a6656a757
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808035"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Konfigurowanie kompleksowego protokołu TLS przy użyciu Application Gateway z portalem

W tym artykule opisano, jak używać Azure Portal do konfigurowania kompleksowego szyfrowania Transport Layer Security (TLS), znanego wcześniej jako szyfrowanie SSL (SSL) za pośrednictwem jednostki SKU platformy Azure Application Gateway v1.

> [!NOTE]
> Jednostka SKU Application Gateway v2 wymaga zaufanych certyfikatów głównych do włączenia konfiguracji kompleksowej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby skonfigurować kompleksową transkonfigurację TLS przy użyciu bramy aplikacji, wymagany jest certyfikat dla bramy. Certyfikaty są również wymagane dla serwerów zaplecza. Certyfikat bramy służy do uzyskiwania klucza symetrycznego w zgodności ze specyfikacją protokołu TLS. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. 

Aby kompleksowe szyfrowanie TLS było możliwe, w bramie aplikacji musi być dozwolony prawidłowy serwer zaplecza. Aby umożliwić ten dostęp, Przekaż certyfikat publiczny serwerów zaplecza, nazywany również certyfikatami uwierzytelniania (v1) lub zaufanymi certyfikatami głównymi (v2), do bramy aplikacji. Dodanie certyfikatu zapewnia, że Brama aplikacji komunikuje się tylko ze znanymi wystąpieniami zaplecza. Ta konfiguracja zabezpiecza kompleksową komunikację.

Aby dowiedzieć się więcej, zobacz [Omówienie kończenia protokołu TLS i kompleksowej usługi TLS z Application Gateway](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Utwórz nową bramę aplikacji z kompleksowym protokołem TLS

Aby utworzyć nową bramę aplikacji z kompleksowym szyfrowaniem TLS, należy najpierw włączyć zakończenie protokołu TLS podczas tworzenia nowej bramy aplikacji. Ta akcja włącza szyfrowanie TLS na potrzeby komunikacji między klientem a bramą aplikacji. Następnie należy umieścić na liście bezpiecznych adresatów certyfikaty dla serwerów zaplecza w ustawieniach protokołu HTTP. Ta konfiguracja umożliwia szyfrowanie protokołu TLS na potrzeby komunikacji między bramą aplikacji a serwerami zaplecza. Pozwala to na kompleksowe szyfrowanie protokołu TLS.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Włącz przerywanie TLS podczas tworzenia nowej bramy aplikacji

Aby dowiedzieć się więcej, zobacz [Włączanie zakończenia protokołu TLS podczas tworzenia nowej bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Dodawanie uwierzytelniania/certyfikatów głównych serwerów zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Ustawienia http** z menu po lewej stronie. Platforma Azure automatycznie utworzyła domyślne ustawienie HTTP **appGatewayBackendHttpSettings**podczas tworzenia bramy aplikacji. 

3. Wybierz pozycję **appGatewayBackendHttpSettings**.

4. W obszarze **Protokół**wybierz pozycję **https**. Zostanie wyświetlone okienko **certyfikatów uwierzytelniania zaplecza lub zaufanych certyfikatów głównych** .

5. Wybierz pozycję**Utwórz nowy**.

6. W polu **Nazwa** wprowadź odpowiednią nazwę.

7. Wybierz plik certyfikatu w polu **Przekaż certyfikat CER** .

   W przypadku bram aplikacji Standard i WAF (v1) należy przekazać klucz publiczny certyfikatu serwera zaplecza w formacie CER.

   ![Dodawanie certyfikatu](./media/end-to-end-ssl-portal/addcert.png)

   W przypadku Standard_v2 i WAF_v2 bram aplikacji należy przekazać certyfikat główny certyfikatu serwera zaplecza w formacie CER. Jeśli certyfikat zaplecza jest wystawiany przez dobrze znany urząd certyfikacji, można zaznaczyć pole wyboru **Użyj dobrego certyfikatu urzędu certyfikacji** , a następnie nie trzeba przekazywać certyfikatu.

   ![Dodaj zaufany certyfikat główny](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Certyfikat główny](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Wybierz pozycję **Zapisz**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Włączanie kompleksowego protokołu TLS dla istniejącej bramy aplikacji

Aby skonfigurować istniejącą bramę aplikacji z kompleksowym szyfrowaniem TLS, należy najpierw włączyć w odbiorniku zakończenie protokołu TLS. Ta akcja włącza szyfrowanie TLS na potrzeby komunikacji między klientem i bramą aplikacji. Następnie należy umieścić te certyfikaty dla serwerów zaplecza w ustawieniach protokołu HTTP na liście bezpiecznych adresatów. Ta konfiguracja umożliwia szyfrowanie protokołu TLS na potrzeby komunikacji między bramą aplikacji a serwerami zaplecza. Pozwala to na kompleksowe szyfrowanie protokołu TLS.

Musisz użyć odbiornika z protokołem HTTPS i certyfikatem do włączenia zakończenia protokołu TLS. Możesz użyć istniejącego odbiornika, który spełnia te warunki, lub utworzyć nowy odbiornik. Jeśli wybierzesz poprzednią opcję, możesz zignorować następujące polecenie "Włącz zakończenie protokołu TLS w istniejącej bramie aplikacji" i przejść bezpośrednio do sekcji "Dodawanie uwierzytelniania/zaufanych certyfikatów głównych dla serwerów zaplecza".

W przypadku wybrania tej opcji należy zastosować kroki opisane w poniższej procedurze.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Włączanie zakończenia protokołu TLS w istniejącej bramie aplikacji

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **detektory** z menu po lewej stronie.

3. W zależności od potrzeb wybierz opcję odbiornik **podstawowy** lub **wiele witryn** .

4. W obszarze **Protokół**wybierz pozycję **https**. Zostanie wyświetlone okienko dla **certyfikatu** .

5. Przekaż certyfikat PFX, który ma zostać użyty do zakończenia protokołu TLS między klientem a bramą aplikacji.

   > [!NOTE]
   > Do celów testowych można użyć certyfikatu z podpisem własnym. Nie jest to jednak zalecane w przypadku obciążeń produkcyjnych, ponieważ są one trudniejsze do zarządzania i nie są całkowicie bezpieczne. Aby uzyskać więcej informacji, zobacz [Tworzenie certyfikatu z](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)podpisem własnym.

6. Dodaj inne wymagane ustawienia dla **odbiornika**, w zależności od wymagań.

7. Wybierz opcję **OK**, aby zapisać.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Dodawanie uwierzytelniania/zaufanych certyfikatów głównych serwerów zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Ustawienia http** z menu po lewej stronie. Certyfikaty można umieścić w istniejącym ustawieniu HTTP zaplecza na liście bezpiecznych adresatów lub utworzyć nowe ustawienie protokołu HTTP. (W następnym kroku certyfikat dla domyślnego ustawienia HTTP, **appGatewayBackendHttpSettings**, zostanie dodany do listy bezpiecznych adresatów).

3. Wybierz pozycję **appGatewayBackendHttpSettings**.

4. W obszarze **Protokół**wybierz pozycję **https**. Zostanie wyświetlone okienko **certyfikatów uwierzytelniania zaplecza lub zaufanych certyfikatów głównych** . 

5. Wybierz pozycję**Utwórz nowy**.

6. W polu **Nazwa** wprowadź odpowiednią nazwę.

7. Wybierz plik certyfikatu w polu **Przekaż certyfikat CER** .

   W przypadku bram aplikacji Standard i WAF (v1) należy przekazać klucz publiczny certyfikatu serwera zaplecza w formacie CER.

   ![Dodawanie certyfikatu](./media/end-to-end-ssl-portal/addcert.png)

   W przypadku Standard_v2 i WAF_v2 bram aplikacji należy przekazać certyfikat główny certyfikatu serwera zaplecza w formacie CER. Jeśli certyfikat zaplecza jest wystawiony przez dobrze znany urząd certyfikacji, można zaznaczyć pole wyboru **Użyj certyfikatu dobrze znanego urzędu certyfikacji** , a następnie nie trzeba przekazywać certyfikatu.

   ![Dodaj zaufany certyfikat główny](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)
