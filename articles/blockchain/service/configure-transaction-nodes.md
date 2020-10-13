---
title: Konfigurowanie węzłów transakcji usługi Azure łańcucha bloków Service
description: Jak skonfigurować węzły transakcji usługi Azure łańcucha bloków
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: b3a4650977f1ad1a7a6967daa162adc8d2ef7bff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530390"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Konfigurowanie węzłów transakcji usługi Azure łańcucha bloków Service

Węzły transakcji są używane do wysyłania transakcji łańcucha bloków do usługi Azure łańcucha bloków Service za pomocą publicznego punktu końcowego. Domyślny węzeł transakcji zawiera klucz prywatny konta Ethereum zarejestrowany w łańcucha bloków i nie można go usunąć.

Aby wyświetlić szczegóły domyślnego węzła transakcji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure łańcucha bloków. Wybierz **węzły transakcji**.

    ![Wybierz domyślny węzeł transakcji](./media/configure-transaction-nodes/nodes.png)

    Szczegóły przeglądu obejmują publiczne adresy punktów końcowych i klucz publiczny.

## <a name="create-transaction-node"></a>Utwórz węzeł transakcji

Można dodać do dziewięciu dodatkowych węzłów transakcji do składowej łańcucha bloków, co łącznie 10 węzłów transakcji. Dodając węzły transakcji, można zwiększyć skalowalność lub rozłożyć obciążenie. Można na przykład mieć punkt końcowy węzła transakcji dla różnych aplikacji klienckich.

Aby dodać węzeł transakcji:

1. W Azure Portal przejdź do elementu członkowskiego usługi Azure łańcucha bloków i wybierz pozycję **węzły transakcji > Dodaj**.
1. Ukończ ustawienia dla nowego węzła transakcji.

    ![Dodaj węzeł transakcji](./media/configure-transaction-nodes/add-node.png)

    | Ustawienie | Opis |
    |---------|-------------|
    | Nazwa | Nazwa węzła transakcji. Nazwa służy do tworzenia adresu DNS dla punktu końcowego węzła transakcji. Na przykład `newnode-myblockchainmember.blockchain.azure.com`. Nazwy węzła nie można zmienić po jego utworzeniu. |
    | Hasło | Ustaw silne hasło. Użyj hasła, aby uzyskać dostęp do punktu końcowego węzła transakcji z uwierzytelnianiem podstawowym.

1. Wybierz przycisk **Utwórz**.

    Inicjowanie obsługi nowego węzła transakcji trwa około 10 minut. Dodatkowe węzły transakcji wiążą się z kosztami. Aby uzyskać więcej informacji o kosztach, zobacz [Cennik platformy Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Punkty końcowe

Węzły transakcji mają unikatową nazwę DNS i publiczne punkty końcowe.

Aby wyświetlić szczegóły punktu końcowego węzła transakcji:

1. W Azure Portal przejdź do jednego z węzłów transakcji członków usługi Azure łańcucha bloków, a następnie wybierz pozycję **Przegląd**.

    ![Przechwytywanie ekranu pokazuje przegląd węzłów transakcji dla elementu członkowskiego łańcucha bloków.](./media/configure-transaction-nodes/endpoints.png)

Punkty końcowe węzła transakcji są bezpieczne i wymagają uwierzytelniania. Można nawiązać połączenie z punktem końcowym transakcji przy użyciu uwierzytelniania usługi Azure AD, uwierzytelniania podstawowego HTTPS i przy użyciu klucza dostępu za pośrednictwem protokołu HTTPS lub protokołu WebSocket przez protokół TLS.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory kontroli dostępu

Punkty końcowe węzła transakcji usługi Azure łańcucha bloków obsługują uwierzytelnianie Azure Active Directory (Azure AD). Możesz udzielić użytkownikowi, grupie i dostępowi jednostki usługi Azure AD do punktu końcowego.

Aby udzielić kontroli dostępu usługi Azure AD do punktu końcowego:

1. W Azure Portal przejdź do elementu członkowskiego usługi Azure łańcucha bloków i wybierz pozycję **węzły transakcji > kontrola dostępu (IAM) > dodaj > Dodaj przypisanie roli**.
1. Utwórz nowe przypisanie roli dla użytkownika, grupy lub nazwy głównej usługi (ról aplikacji).

    ![Dodaj rolę IAM](./media/configure-transaction-nodes/add-role.png)

    | Ustawienie | Akcja |
    |---------|-------------|
    | Rola | Wybierz **właściciela**, **współautora**lub **czytelnika**.
    | Przypisz dostęp do | Wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**.
    | Wybierz | Wyszukaj użytkownika, grupę lub jednostkę usługi, którą chcesz dodać.

1. Wybierz pozycję **Zapisz** , aby dodać przypisanie roli.

Aby uzyskać więcej informacji na temat kontroli dostępu w usłudze Azure AD, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i Azure Portal](../../role-based-access-control/role-assignments-portal.md)

Aby uzyskać szczegółowe informacje na temat nawiązywania połączenia przy użyciu uwierzytelniania usługi Azure AD, zobacz [nawiązywanie połączenia z węzłem przy użyciu uwierzytelniania w usłudze AAD](configure-aad.md).

### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

W przypadku uwierzytelniania HTTPS Basic poświadczenia nazwy użytkownika i hasła są przesyłane w nagłówku HTTPS żądania do punktu końcowego.

Możesz wyświetlić szczegóły podstawowego punktu końcowego uwierzytelniania węzła transakcji w Azure Portal. Przejdź do jednego z węzłów transakcji członków usługi Azure łańcucha bloków, a następnie wybierz pozycję **uwierzytelnianie podstawowe** w obszarze Ustawienia.

![Uwierzytelnianie podstawowe](./media/configure-transaction-nodes/basic.png)

Nazwa użytkownika jest nazwą węzła i nie można jej zmienić.

Aby użyć adresu URL, Zastąp ciąg \<password\> hasłem ustawionym podczas aprowizacji węzła. Hasło można zaktualizować, wybierając pozycję **Resetuj hasło**.

### <a name="access-keys"></a>Klawisze dostępu

W przypadku uwierzytelniania za pomocą klucza dostępu klucz dostępu jest uwzględniany w adresie URL punktu końcowego. Po udostępnieniu węzła transakcji są generowane dwa klucze dostępu. Klucz dostępu może być używany do uwierzytelniania. Dwa klucze umożliwiają zmienianie i obracanie kluczy.

Można wyświetlić szczegóły klucza dostępu węzła transakcji i skopiować adresy punktów końcowych, które zawierają klucze dostępu. Przejdź do jednego z węzłów transakcji członków usługi Azure łańcucha bloków i wybierz pozycję **klucze dostępu** w obszarze Ustawienia.

### <a name="firewall-rules"></a>Reguły zapory

Reguły zapory umożliwiają ograniczenie adresów IP, które mogą próbować uwierzytelniać się w węźle transakcji.  Jeśli nie skonfigurowano żadnych reguł zapory dla węzła transakcji, nie będzie on dostępny dla żadnej ze stron.  

Aby wyświetlić reguły zapory węzła transakcji, przejdź do jednego z węzłów transakcji członków usługi Azure łańcucha bloków i wybierz pozycję **reguły zapory** w ustawieniach.

Reguły zapory można dodać, wprowadzając nazwę reguły, początkowy adres IP i końcowy adres IP w siatce **reguły zapory** .

![Reguły zapory](./media/configure-transaction-nodes/firewall-rules.png)

Aby włączyć:

* **Pojedynczy adres IP:** Skonfiguruj ten sam adres IP dla początkowych i końcowych adresów IP.
* **Zakres adresów IP:** Skonfiguruj początkowy i końcowy zakres adresów IP. Na przykład zakres zaczynający się od 10.221.34.0 i kończący się na 10.221.34.255 spowoduje włączenie całej podsieci 10.221.34.xxx.
* **Zezwalaj na wszystkie adresy IP:** Skonfiguruj początkowy adres IP na wartość 0.0.0.0 i końcowy adres IP na 255.255.255.255.

## <a name="connection-strings"></a>Parametry połączeń

Składnia parametrów połączenia dla węzła Transaction jest udostępniana na potrzeby uwierzytelniania podstawowego lub przy użyciu kluczy dostępu. Dostępne są parametry połączenia, w tym klucze dostępu za pośrednictwem protokołu HTTPS i usługi WebSockets.

Można wyświetlić parametry połączenia węzła transakcji i adresy punktów końcowych kopiowania. Przejdź do jednego z węzłów transakcji członków usługi Azure łańcucha bloków, a następnie wybierz pozycję **Parametry połączenia** w ustawieniach.

![Parametry połączeń](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod umożliwia szybkie włączenie połączenia z węzłem transakcji za pośrednictwem Web3, Nethereum, Web3js i Truffle.

Możesz wyświetlić przykładowy kod połączenia węzła transakcji i skopiować go do użycia z popularnymi narzędziami deweloperskimi. Przejdź do jednego z węzłów transakcji członków usługi Azure łańcucha bloków, a następnie wybierz pozycję **przykładowy kod** w obszarze Ustawienia.

Wybierz kartę Web3, Nethereum, Truffle lub Web3j, aby wyświetlić przykładowy kod, którego chcesz użyć.

![Przykładowy kod](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie węzłów transakcji przy użyciu interfejsu wiersza polecenia platformy Azure](manage-cli.md)
