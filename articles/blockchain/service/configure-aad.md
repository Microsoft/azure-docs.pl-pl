---
title: Konfigurowanie dostępu Azure Active Directory — usługa Azure łańcucha bloków
description: Jak skonfigurować usługę Azure łańcucha bloków z dostępem Azure Active Directory
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 337d01abc51d310d06aeea3427b770132be4824c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85208777"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Jak skonfigurować Azure Active Directory dostępu do usługi Azure łańcucha bloków Service

W tym artykule dowiesz się, jak udzielić dostępu i nawiązać połączenie z węzłami usługi Azure łańcucha bloków za pomocą identyfikatorów użytkowników, grup i aplikacji Azure Active Directory (Azure AD).

Usługa Azure AD zapewnia oparte na chmurze zarządzanie tożsamościami i umożliwia korzystanie z jednej tożsamości w całym przedsiębiorstwie i uzyskiwanie dostępu do aplikacji na platformie Azure. Usługa Azure łańcucha bloków Service jest zintegrowana z usługą Azure AD i oferuje korzyści, takie jak Federacja identyfikatorów, logowanie jednokrotne i uwierzytelnianie wieloskładnikowe.

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie składowej łańcucha bloków przy użyciu Azure Portal](create-member.md)

## <a name="grant-access"></a>Udzielanie dostępu

Można udzielić dostępu zarówno na poziomie elementu członkowskiego, jak i na poziomie węzła. Przyznanie praw dostępu na poziomie elementu członkowskiego spowoduje przyznanie dostępu wszystkim węzłom w ramach elementu członkowskiego.

### <a name="grant-member-level-access"></a>Przyznaj dostęp na poziomie elementu członkowskiego

Aby udzielić uprawnienia dostępu na poziomie elementu członkowskiego.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **kontroli dostępu (IAM) > dodaj > Dodaj przypisanie roli**.
1. Wybierz rolę **łańcucha bloków dostęp do węzła członkowskiego (wersja zapoznawcza)** i Dodaj obiekt usługi Azure AD ID, do którego chcesz udzielić dostępu. IDENTYFIKATORem obiektu usługi Azure AD może być:

    | Obiekt usługi Azure AD | Przykład |
    |-----------------|---------|
    | Użytkownik usługi Azure AD   | `kim@contoso.onmicrosoft.com` |
    | Grupa usługi Azure AD  | `sales@contoso.onmicrosoft.com` |
    | Identyfikator aplikacji  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Dodaj przypisanie roli](./media/configure-aad/add-role-assignment.png)

1. Wybierz pozycję **Zapisz**.

### <a name="grant-node-level-access"></a>Udzielanie dostępu na poziomie węzła

Możesz udzielić dostępu na poziomie węzła, przechodząc do węzła zabezpieczenia i klikając nazwę węzła, do którego chcesz udzielić dostępu.

Wybierz rolę łańcucha bloków dostęp do węzła członkowskiego (wersja zapoznawcza) i Dodaj obiekt usługi Azure AD ID, do którego chcesz udzielić dostępu.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie węzłów transakcji usługi Azure łańcucha bloków](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Nawiązywanie połączenia przy użyciu łącznika usługi Azure łańcucha bloków

Pobierz lub Sklonuj [Łącznik usługi Azure łańcucha bloków z witryny GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Postępuj zgodnie z sekcją szybkiego startu w **pliku Readme** , aby skompilować łącznik z kodu źródłowego.

### <a name="connect-using-an-azure-ad-user-account"></a>Nawiązywanie połączenia przy użyciu konta użytkownika usługi Azure AD

1. Uruchom następujące polecenie, aby uwierzytelnić się przy użyciu konta użytkownika usługi Azure AD. Zamień \<myAADDirectory\> na domenę usługi Azure AD. Na przykład `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Usługa Azure AD będzie monitował o poświadczenia.
1. Zaloguj się przy użyciu nazwy użytkownika i hasła.
1. Po pomyślnym uwierzytelnieniu lokalny serwer proxy nawiązuje połączenie z węzłem łańcucha bloków. Teraz możesz dołączyć klienta Geth z lokalnym punktem końcowym.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Nawiązywanie połączenia przy użyciu identyfikatora aplikacji

Wiele aplikacji jest uwierzytelnianych za pomocą usługi Azure AD przy użyciu identyfikatora aplikacji zamiast konta użytkownika usługi Azure AD.

Aby nawiązać połączenie z węzłem przy użyciu identyfikatora aplikacji, Zastąp **aadauthcode** z **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parametr | Opis |
|-----------|-------------|
| Identyfikator dzierżawy | Domena usługi Azure AD, na przykład `yourdomain.onmicrosoft.com`
| Identyfikator klienta | Identyfikator klienta zarejestrowanej aplikacji w usłudze Azure AD
| Klient-klucz tajny | Klucz tajny klienta zarejestrowanej aplikacji w usłudze Azure AD

Aby uzyskać więcej informacji na temat rejestrowania aplikacji w usłudze Azure AD, zobacz [How to: Use the Portal to the Azure AD role and Service Principal, która może uzyskiwać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Podłączanie urządzenia przenośnego lub przeglądarki tekstu

W przypadku urządzenia przenośnego lub przeglądarki opartej na tekście, w której nie można wyświetlić okna podręcznego uwierzytelniania usługi Azure AD, usługa Azure AD generuje jednorazowy kod dostępu. Możesz skopiować kod dostępu i kontynuować uwierzytelnianie usługi Azure AD w innym środowisku.

Aby wygenerować kod dostępu, Zastąp **aadauthcode** z **aaddevice**. Zamień \<myAADDirectory\> na domenę usługi Azure AD. Na przykład `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o zabezpieczeniach danych w usłudze Azure łańcucha bloków Service, zobacz [zabezpieczenia usługi Azure łańcucha bloków](data-security.md).