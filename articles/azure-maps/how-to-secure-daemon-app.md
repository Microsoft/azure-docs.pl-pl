---
title: Jak zabezpieczyć aplikację demona
titleSuffix: Azure Maps
description: Użyj Azure Portal, aby zarządzać uwierzytelnianiem w celu skonfigurowania zaufanej aplikacji demona.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2dd04f404330a6c86e2df09da610e16ba9b721f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895651"
---
# <a name="secure-a-daemon-application"></a>Zabezpieczanie aplikacji demona

Poniższy przewodnik dotyczy procesów w tle, czasomierzy i zadań, które są hostowane w zaufanym i zabezpieczonym środowisku. Przykładami mogą być zadania sieci Web platformy Azure, aplikacje funkcji platformy Azure, usługi systemu Windows i inne niezawodne usługi w tle.

> [!Tip]
> Firma Microsoft zaleca wdrożenie Azure Active Directory (Azure AD) i kontroli dostępu opartej na rolach (RBAC) na platformie Azure dla aplikacji produkcyjnych. Aby zapoznać się z omówieniem pojęć, zobacz [Azure Maps Authentication](./azure-maps-authentication.md).

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>Scenariusz: uwierzytelnianie klucza wspólnego

Po utworzeniu konta Azure Maps są generowane klucze podstawowe i pomocnicze. Zalecamy używanie klucza podstawowego jako klucza subskrypcji podczas [korzystania z uwierzytelniania za pomocą klucza współużytkowanego do wywoływania Azure Maps](./azure-maps-authentication.md#shared-key-authentication). Klucza pomocniczego można używać w scenariuszach, takich jak stopniowe zmiany klawiszy. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie w Azure Maps](./azure-maps-authentication.md).

### <a name="securely-store-shared-key"></a>Bezpiecznie przechowuj klucz współużytkowany

Klucz podstawowy i pomocniczy umożliwiają autoryzację wszystkich interfejsów API dla konta usługi Maps. Aplikacje powinny przechowywać klucze w bezpiecznym magazynie, takim jak Azure Key Vault. Aplikacja musi pobrać klucz współużytkowany jako klucz tajny Azure Key Vault, aby uniknąć przechowywania klucza wspólnego w postaci zwykłego tekstu w konfiguracji aplikacji. Aby dowiedzieć się, jak skonfigurować Azure Key Vault, zobacz [Azure Key Vault Przewodnik dla deweloperów](../key-vault/general/developers-guide.md).

Poniższe kroki przedstawiają ten proces:

1. Utwórz Azure Key Vault.
2. Utwórz jednostkę usługi Azure AD, tworząc rejestrację aplikacji lub tożsamość zarządzaną. utworzony podmiot zabezpieczeń jest odpowiedzialny za dostęp do Azure Key Vault.
3. Przypisz dostęp do nazwy głównej usługi do uprawnienia do kluczy tajnych klucza platformy Azure `Get` .
4. Tymczasowo Przypisz uprawnienie dostępu do kluczy tajnych `Set` jako dewelopera.
5. Ustaw klucz współużytkowany w wpisach tajnych Key Vault i odwołuje się do identyfikatora tajnego jako konfiguracji aplikacji demona i Usuń `Set` odpowiednie uprawnienia.
6. Zaimplementuj uwierzytelnianie usługi Azure AD w aplikacji demona, aby pobrać klucz tajny klucza wspólnego z Azure Key Vault.
7. Utwórz żądanie interfejsu API REST Azure Maps przy użyciu klucza współużytkowanego.

> [!Tip]
> Jeśli aplikacja jest hostowana w środowisku platformy Azure, należy zaimplementować zarządzaną tożsamość, aby zmniejszyć koszty i złożoność zarządzania wpisem tajnym w celu uwierzytelnienia w Azure Key Vault. [Aby nawiązać połączenie za pośrednictwem tożsamości zarządzanej](../key-vault/general/tutorial-net-create-vault-azure-web-app.md), zobacz następujący samouczek Azure Key Vault.

Aplikacja demona jest odpowiedzialna za pobieranie klucza wspólnego z bezpiecznego magazynu. Implementacja z Azure Key Vault wymaga uwierzytelniania za pomocą usługi Azure AD w celu uzyskania dostępu do klucza tajnego. Zamiast tego zachęcamy do bezpośredniego uwierzytelniania usługi Azure AD w celu Azure Maps w wyniku dodatkowych wymagań dotyczących złożoności i działania związanych z uwierzytelnianiem za pomocą klucza współużytkowanego.

> [!IMPORTANT]
> Aby uprościć ponowne generowanie kluczy, zalecamy korzystanie z jednego klucza w przypadku aplikacji. Aplikacje mogą następnie ponownie wygenerować nieużywany klucz i wdrożyć nowy ponownie wygenerowany klucz w zabezpieczonym magazynie tajnym, takim jak Azure Key Vault.

## <a name="scenario-azure-ad-role-based-access-control"></a>Scenariusz: Kontrola dostępu oparta na rolach w usłudze Azure AD

Po utworzeniu konta Azure Maps `x-ms-client-id` wartość Azure Maps jest obecna na stronie Szczegóły uwierzytelniania Azure Portal. Ta wartość reprezentuje konto, które będzie używane na potrzeby żądań interfejsu API REST. Ta wartość powinna być przechowywana w konfiguracji aplikacji i pobierana przed przystąpieniem do wykonywania żądań HTTP. Celem tego scenariusza jest umożliwienie aplikacji demona uwierzytelnianie w usłudze Azure AD i wywoływanie Azure Maps interfejsów API REST.

> [!Tip]
> Zalecamy hosting na platformie Azure Virtual Machines, Virtual Machine Scale Sets lub App Services, aby umożliwić korzystanie ze składników tożsamości zarządzanej.

### <a name="daemon-hosted-on-azure-resources"></a>Demon hostowany w zasobach platformy Azure

W przypadku uruchamiania w zasobach platformy Azure Skonfiguruj tożsamości zarządzane przez platformę Azure, aby włączyć niskie koszty i minimalne nakłady związane z zarządzaniem poświadczeniami 

Zobacz [Omówienie tożsamości zarządzanych](../active-directory/managed-identities-azure-resources/overview.md) , aby umożliwić aplikacji dostęp do zarządzanej tożsamości.

Zalety tożsamości zarządzanej:

* Uwierzytelnianie kryptografii klucza publicznego zarządzanego certyfikatu x509 systemu Azure.
* Zabezpieczenia usługi Azure AD z certyfikatami x509 zamiast wpisów tajnych klienta.
* Platforma Azure zarządza i odnawia wszystkie certyfikaty skojarzone z zarządzanym zasobem tożsamości.
* Uproszczone zarządzanie działaniem poświadczeń przez usunięcie wszelkich potrzeb dotyczących usługi bezpiecznego magazynu tajnego, takiej jak Azure Key Vault. 

### <a name="daemon-hosted-on-non-azure-resources"></a>Demon hostowany w zasobach innych niż Azure

W przypadku korzystania z tożsamości zarządzanych w środowisku innym niż Azure nie są dostępne. W związku z tym należy skonfigurować jednostkę usługi za pomocą rejestracji aplikacji usługi Azure AD dla aplikacji demona.

1. W Azure Portal na liście usług platformy Azure wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**  >  **Nowa rejestracja**.  

    > [!div class="mx-imgBorder"]
    > ![Rejestrowanie aplikacji](./media/how-to-manage-authentication/app-registration.png)

2. Jeśli aplikacja została już zarejestrowana, przejdź do następnego kroku. Jeśli aplikacja nie została zarejestrowana, wprowadź **nazwę**, wybierz **Typ konta pomocy technicznej**, a następnie wybierz pozycję **zarejestruj**.  

    > [!div class="mx-imgBorder"]
    > ![Szczegóły rejestracji aplikacji](./media/how-to-manage-authentication/app-create.png)

3. Aby przypisać delegowane uprawnienia interfejsu API do Azure Maps, przejdź do aplikacji. Następnie w obszarze **rejestracje aplikacji** wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**. W obszarze interfejsy API, które są **wykorzystywane przez moją organizację**, Wyszukaj i wybierz **Azure Maps**.

    > [!div class="mx-imgBorder"]
    > ![Dodawanie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/app-permissions.png)

4. Zaznacz pole wyboru obok pozycji **dostęp Azure Maps**, a następnie wybierz pozycję **Dodaj uprawnienia**.

    > [!div class="mx-imgBorder"]
    > ![Wybieranie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/select-app-permissions.png)

5. Wykonaj następujące kroki, aby utworzyć klucz tajny klienta lub skonfigurować certyfikat.

    * Jeśli aplikacja używa uwierzytelniania serwera lub aplikacji, na stronie rejestracji aplikacji przejdź do pozycji **certyfikaty & wpisy tajne**. Następnie Przekaż certyfikat klucza publicznego lub Utwórz hasło, wybierając pozycję **Nowy wpis tajny klienta**.

        > [!div class="mx-imgBorder"]
        > ![Tworzenie klucza tajnego klienta](./media/how-to-manage-authentication/app-keys.png)

    * Po wybraniu opcji **Dodaj** skopiuj klucz tajny i Zapisz go bezpiecznie w usłudze, takiej jak Azure Key Vault. Zapoznaj się z [przewodnikiem dewelopera Azure Key Vault](../key-vault/general/developers-guide.md) , aby bezpiecznie przechowywać certyfikat lub klucz tajny. Ten wpis tajny będzie używany do uzyskiwania tokenów z usługi Azure AD.

        > [!div class="mx-imgBorder"]
        > ![Dodawanie klucza tajnego klienta](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>Udziel dostępu opartego na rolach dla aplikacji demona do Azure Maps

*Kontrolę dostępu opartą na rolach na platformie Azure (RBAC)* można przypisywać do co najmniej jednej definicji roli Azure Maps utworzonej tożsamości zarządzanej lub nazwy głównej usługi. Aby wyświetlić definicje ról platformy Azure, które są dostępne dla Azure Maps, przejdź do obszaru **Kontrola dostępu (IAM)**. Wybierz pozycję **role**, a następnie wyszukaj role zaczynające się od *Azure Maps*. Te role Azure Maps są rolami, do których można udzielić dostępu.

> [!div class="mx-imgBorder"]
> ![Wyświetl dostępne role](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. Przejdź do swojego **konta Azure Maps**. Wybierz pozycję **Kontrola dostępu (IAM)** > **Przypisania ról**.

    > [!div class="mx-imgBorder"]
    > ![Udzielanie dostępu przy użyciu funkcji RBAC platformy Azure](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na karcie **przypisania ról** **Dodaj** przypisanie roli. 

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia przydziały rzutowania z dodatkiem Dodaj.](./media/how-to-manage-authentication/add-role-assignment.png)

3. Wybierz wbudowaną definicję roli Azure Maps, na przykład **Azure Maps czytnika danych** lub **Azure Maps współautor danych**. W obszarze **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi** lub tożsamość zarządzana z przypisaną przez **użytkownika** zarządzaną tożsamością  /  . Wybierz podmiot zabezpieczeń. Następnie wybierz pozycję **Zapisz**.

    > [!div class="mx-imgBorder"]
    > ![Jak dodać przypisanie roli](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. Można potwierdzić, że przypisanie roli zostało zastosowane na karcie przypisanie roli.

## <a name="request-token-with-managed-identity"></a>Token żądania z tożsamością zarządzaną

Po skonfigurowaniu zarządzanej tożsamości dla zasobu hostingu Użyj zestawu Azure SDK lub interfejsu API REST, aby uzyskać token dla Azure Maps, zobacz szczegóły dotyczące [uzyskiwania tokenu dostępu](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md). Zgodnie z przewodnikiem oczekuje się, że token dostępu zostanie zwrócony, który może być używany w żądaniach interfejsu API REST.

## <a name="request-token-with-application-registration"></a>Zażądaj tokenu z rejestracją aplikacji

Po zarejestrowaniu aplikacji i skojarzeniu jej z Azure Maps można zażądać tokenów dostępu.

* Identyfikator zasobu usługi Azure AD `https://atlas.microsoft.com/`
* Identyfikator aplikacja usługi Azure AD
* Identyfikator dzierżawy usługi Azure AD
* Klucz tajny klienta rejestracji aplikacja usługi Azure AD

Żądanie:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

Odpowiedź:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

Aby uzyskać bardziej szczegółowe przykłady, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](../active-directory/develop/authentication-vs-authorization.md).

## <a name="next-steps"></a>Następne kroki

Znajdź metryki użycia interfejsu API dla konta usługi Azure Maps:
> [!div class="nextstepaction"]
> [Wyświetlanie metryk użycia](how-to-view-api-usage.md)

Zapoznaj się z przykładami, które pokazują, jak zintegrować usługę Azure AD z Azure Maps:
> [!div class="nextstepaction"]
> [Przykłady Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)