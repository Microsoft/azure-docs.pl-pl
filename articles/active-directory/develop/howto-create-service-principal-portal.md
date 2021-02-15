---
title: Tworzenie aplikacji usługi Azure AD & jednostki usługi w portalu
titleSuffix: Microsoft identity platform
description: Utwórz nową aplikację Azure Active Directory & jednostki usługi, aby zarządzać dostępem do zasobów za pomocą kontroli dostępu opartej na rolach w Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: 8a4a4153261b93b3b17641e8561962c274570bd0
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104216"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Instrukcje: używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów

W tym artykule opisano sposób tworzenia nowej aplikacji Azure Active Directory (Azure AD) i nazwy głównej usługi, która może być używana z kontrolą dostępu opartą na rolach. W przypadku aplikacji, usług hostowanych lub zautomatyzowanych narzędzi, które wymagają dostępu lub modyfikacji zasobów, można utworzyć tożsamość aplikacji. Ta tożsamość jest określana jako jednostka usługi. Dostęp do zasobów jest ograniczony przez role przypisane do jednostki usługi, co zapewnia kontrolę nad zasobami, do których można uzyskać dostęp oraz na którym poziomie. Ze względów bezpieczeństwa zawsze zaleca się używanie jednostek usługi ze zautomatyzowanymi narzędziami, zamiast zezwalać im na logowanie za pomocą tożsamości użytkownika.

W tym artykule pokazano, jak za pomocą portalu utworzyć nazwę główną usługi w Azure Portal. Koncentruje się na aplikacji z jedną dzierżawą, w której aplikacja jest przeznaczona do działania tylko w jednej organizacji. Zwykle używasz aplikacji z jedną dzierżawą dla aplikacji biznesowych, które działają w organizacji.  Możesz również [użyć Azure PowerShell, aby utworzyć jednostkę usługi](howto-authenticate-service-principal-powershell.md).

> [!IMPORTANT]
> Zamiast tworzyć jednostkę usługi, rozważ użycie zarządzanych tożsamości dla zasobów platformy Azure dla Twojej tożsamości aplikacji. Jeśli kod jest uruchamiany w usłudze obsługującej zarządzane tożsamości i uzyskuje dostęp do zasobów, które obsługują uwierzytelnianie usługi Azure AD, tożsamości zarządzane są dla Ciebie lepszym rozwiązaniem. Aby dowiedzieć się więcej na temat tożsamości zarządzanych dla zasobów platformy Azure, w tym usług, które są obecnie obsługiwane, zobacz temat [co to są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

## <a name="app-registration-app-objects-and-service-principals"></a>Rejestracja aplikacji, obiekty aplikacji i jednostki usługi
Nie ma możliwości bezpośredniego tworzenia nazwy głównej usługi przy użyciu Azure Portal.  Po zarejestrowaniu aplikacji za pomocą Azure Portal, obiekt aplikacji i nazwa główna usługi są tworzone automatycznie w katalogu macierzystym lub dzierżawie.  Aby uzyskać więcej informacji na temat relacji między rejestracją aplikacji, obiektami aplikacji i jednostkami usługi, Odczytaj [aplikacje i obiekty główne usługi w Azure Active Directory](app-objects-and-service-principals.md).

## <a name="permissions-required-for-registering-an-app"></a>Uprawnienia wymagane do zarejestrowania aplikacji

Musisz mieć wystarczające uprawnienia, aby zarejestrować aplikację w dzierżawie usługi Azure AD i przypisać ją do aplikacji w ramach subskrypcji platformy Azure.

### <a name="check-azure-ad-permissions"></a>Sprawdź uprawnienia usługi Azure AD

1. Wybierz pozycję **Azure Active Directory**.
1. Zanotuj rolę użytkownika. Jeśli masz rolę **użytkownika** , musisz upewnić się, że użytkownicy niebędący administratorami mogą rejestrować aplikacje.

   ![Znajdź rolę. Jeśli jesteś użytkownikiem, upewnij się, że użytkownicy niebędący administratorami mogą rejestrować aplikacje](./media/howto-create-service-principal-portal/view-user-info.png)

1. W okienku po lewej stronie wybierz pozycję **Ustawienia użytkownika**.
1. Sprawdź ustawienie **rejestracje aplikacji** . Tę wartość można ustawić tylko przez administratora. W przypadku wybrania **opcji tak** każdy użytkownik w dzierżawie usługi Azure AD może zarejestrować aplikację.

Jeśli ustawienie rejestracje aplikacji ma wartość **nie**, tylko użytkownicy z rolą administratora mogą rejestrować te typy aplikacji. Zobacz [dostępne role](../roles/permissions-reference.md#available-roles) i [uprawnienia roli](../roles/permissions-reference.md#role-permissions) , aby dowiedzieć się więcej na temat dostępnych ról administratorów i określonych uprawnień w usłudze Azure AD, które są nadawane każdej roli. Jeśli Twoje konto ma przypisaną rolę użytkownika, ale ustawienie rejestracji aplikacji jest ograniczone do użytkowników administracyjnych, poproszenie administratora o przypisanie jednej z ról administratora, które mogą tworzyć wszystkie aspekty rejestracji aplikacji i zarządzać nimi, lub umożliwić użytkownikom rejestrowanie aplikacji.

### <a name="check-azure-subscription-permissions"></a>Sprawdź uprawnienia subskrypcji platformy Azure

W ramach subskrypcji platformy Azure Twoje konto musi mieć `Microsoft.Authorization/*/Write` dostęp, aby przypisać rolę do aplikacji usługi AD. Ta akcja jest wykonywana za pośrednictwem roli [Właściciel](../../role-based-access-control/built-in-roles.md#owner) lub [Administrator dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator). Jeśli Twoje konto ma przypisaną rolę **współautor** , nie masz wystarczających uprawnień. Wystąpił błąd podczas próby przypisania jednostki usługi do roli.

Aby sprawdzić uprawnienia do subskrypcji:

1. Wyszukaj i wybierz pozycję **subskrypcje** lub wybierz pozycję **subskrypcje** na stronie **głównej** .

   ![Wyszukaj](./media/howto-create-service-principal-portal/select-subscription.png)

1. Wybierz subskrypcję, w której chcesz utworzyć nazwę główną usługi.

   ![Wybierz subskrypcję do przypisania](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Jeśli nie widzisz subskrypcji, której szukasz, wybierz pozycję **Filtr subskrypcje globalne**. Upewnij się, że wybrano subskrypcję dla portalu.

1. Wybierz pozycję **Moje uprawnienia**. Następnie wybierz **pozycję kliknij tutaj, aby wyświetlić pełne szczegóły dostępu dla tej subskrypcji**.

   ![Wybierz subskrypcję, w której chcesz utworzyć nazwę główną usługi](./media/howto-create-service-principal-portal/view-details.png)

1. Wybierz pozycję **Wyświetl** w **przypisaniach ról** , aby wyświetlić przypisane role i określić, czy masz odpowiednie uprawnienia do przypisywania roli do aplikacji usługi AD. Jeśli nie, skontaktuj się z administratorem subskrypcji, aby dodać Cię do roli administratora dostępu użytkownika. Na poniższej ilustracji użytkownik ma przypisaną rolę właściciela, co oznacza, że użytkownik ma odpowiednie uprawnienia.

   ![Ten przykład pokazuje, że użytkownik ma przypisaną rolę właściciela](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Rejestrowanie aplikacji w usłudze Azure AD i tworzenie jednostki usługi

Przejdźmy bezpośrednio do tworzenia tożsamości. Jeśli napotkasz problem, sprawdź [wymagane uprawnienia](#permissions-required-for-registering-an-app) , aby upewnić się, że konto może utworzyć tożsamość.

1. Zaloguj się do konta platformy Azure za pomocą <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Wybierz pozycję **Azure Active Directory**.
1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Nadaj nazwę aplikacji. Wybierz obsługiwany typ konta, który określa, kto może korzystać z aplikacji. W obszarze **Identyfikator URI przekierowania** wybierz pozycję **Sieć Web** dla typu aplikacji, którą chcesz utworzyć. Wprowadź identyfikator URI, do którego jest wysyłany token dostępu. Nie można utworzyć poświadczeń dla [aplikacji natywnej](../manage-apps/application-proxy-configure-native-client-application.md). Nie można użyć tego typu dla zautomatyzowanej aplikacji. Po ustawieniu wartości wybierz pozycję **zarejestruj**.

   ![Wpisz nazwę aplikacji](./media/howto-create-service-principal-portal/create-app.png)

Utworzono aplikację usługi Azure AD i nazwę główną usługi.

## <a name="assign-a-role-to-the-application"></a>Przypisywanie roli do aplikacji

Aby uzyskać dostęp do zasobów w ramach subskrypcji, musisz przypisać rolę do aplikacji. Zdecyduj, która rola oferuje odpowiednie uprawnienia dla aplikacji. Aby dowiedzieć się więcej na temat dostępnych ról, zobacz [role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Zakres można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone do niższych poziomów zakresu. Na przykład dodanie aplikacji do roli *czytelnik* dla grupy zasobów oznacza, że może ona odczytać grupę zasobów i wszystkie zawarte w niej zasoby.

1. W Azure Portal wybierz poziom zakresu, do którego chcesz przypisać aplikację. Aby na przykład przypisać rolę w zakresie subskrypcji, Wyszukaj i wybierz pozycję **subskrypcje** lub wybierz pozycję **subskrypcje** na stronie **głównej** .

   ![Na przykład Przypisz rolę w zakresie subskrypcji](./media/howto-create-service-principal-portal/select-subscription.png)

1. Wybierz określoną subskrypcję, do której ma zostać przypisana aplikacja.

   ![Wybierz subskrypcję do przypisania](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Jeśli nie widzisz subskrypcji, której szukasz, wybierz pozycję **Filtr subskrypcje globalne**. Upewnij się, że wybrano subskrypcję dla portalu.

1. Wybierz pozycję **Kontrola dostępu (IAM)** .
1. Wybierz pozycję **Dodaj przypisanie roli**.
1. Wybierz rolę, którą chcesz przypisać do aplikacji. Na przykład, aby zezwolić aplikacji na wykonywanie akcji takich jak **ponowny rozruch**, **Uruchamianie** i **Zatrzymywanie** wystąpień, wybierz rolę **współautor** .  Przeczytaj więcej na temat [dostępnych ról](../../role-based-access-control/built-in-roles.md) domyślnie aplikacje usługi Azure AD nie są wyświetlane w dostępnych opcjach. Aby znaleźć aplikację, wyszukaj ją i wybierz ją.

   ![Wybierz rolę, która ma zostać przypisana do aplikacji](./media/howto-create-service-principal-portal/select-role.png)

1. Wybierz pozycję **Zapisz** , aby zakończyć Przypisywanie roli. Aplikacja zostanie wyświetlona na liście użytkowników z rolą dla tego zakresu.

Nazwa główna usługi została skonfigurowana. Możesz rozpocząć korzystanie z niego do uruchamiania skryptów lub aplikacji. Aby zarządzać **jednostką** usługi (uprawnienia, uprawnienia użytkowników, którzy wyraził zgodę, sprawdzić uprawnienia, zobacz informacje logowania i inne), przejdź do pozycji aplikacje dla przedsiębiorstw.

W następnej sekcji pokazano, jak uzyskać wartości, które są konieczne podczas logowania programowo.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>Pobieranie wartości identyfikatora dzierżawy i aplikacji na potrzeby logowania

Podczas programowego logowania należy przekazać identyfikator dzierżawy do żądania uwierzytelnienia i identyfikatora aplikacji.  Wymagany jest również certyfikat lub klucz uwierzytelniania (opisany w następnej sekcji). Aby uzyskać te wartości, wykonaj następujące kroki:

1. Wybierz pozycję **Azure Active Directory**.
1. W obszarze **rejestracje aplikacji** w usłudze Azure AD wybierz aplikację.
1. Skopiuj identyfikator katalogu (dzierżawy) i Zapisz go w kodzie aplikacji.

    ![Skopiuj katalog (identyfikator dzierżawy) i Zapisz go w kodzie aplikacji](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    Identyfikator katalogu (dzierżawcy) można również znaleźć na stronie Omówienie katalogu domyślnego.

1. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji.

   ![Kopiowanie identyfikatora aplikacji (klienta)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>Uwierzytelnianie: dwie opcje

Istnieją dwa typy uwierzytelniania dostępne dla podmiotów usługi: uwierzytelnianie oparte na hasłach (klucz tajny aplikacji) i uwierzytelnianie oparte na certyfikatach. *Zalecamy używanie certyfikatu*, ale można również utworzyć klucz tajny aplikacji.

### <a name="option-1-upload-a-certificate"></a>Opcja 1: przekazywanie certyfikatu

Jeśli masz istniejący certyfikat, możesz go użyć.  Opcjonalnie można utworzyć certyfikat z podpisem własnym *tylko do celów testowych*. Aby utworzyć certyfikat z podpisem własnym, Otwórz program PowerShell i uruchom polecenie [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) z poniższymi parametrami, aby utworzyć certyfikat w magazynie certyfikatów użytkownika na komputerze:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Wyeksportuj ten certyfikat do pliku za pomocą przystawki [Zarządzanie certyfikatem użytkownika](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC dostępnej w panelu sterowania systemu Windows.

1. Z menu **Start** wybierz polecenie **Uruchom** , a następnie wprowadź **certmgr. msc**.

   Zostanie wyświetlone narzędzie Menedżer certyfikatów dla bieżącego użytkownika.

1. Aby wyświetlić certyfikaty, w obszarze **Certyfikaty — bieżący użytkownik** w okienku po lewej stronie rozwiń katalog **osobisty** .
1. Kliknij prawym przyciskiem myszy utworzony certyfikat, wybierz polecenie **wszystkie zadania — >Eksportuj**.
1. Postępuj zgodnie z instrukcjami Kreatora eksportu certyfikatów.  Nie Eksportuj klucza prywatnego i Eksportuj do programu. Plik CER.

Aby przekazać certyfikat:

1. Wybierz pozycję **Azure Active Directory**.
1. W obszarze **rejestracje aplikacji** w usłudze Azure AD wybierz aplikację.
1. Wybierz pozycję **certyfikaty & wpisy tajne**.
1. Wybierz pozycję **Przekaż certyfikat** i wybierz certyfikat (istniejący certyfikat lub wyeksportowany certyfikat z podpisem własnym).

    ![Wybierz pozycję Przekaż certyfikat i wybierz ten, który chcesz dodać.](./media/howto-create-service-principal-portal/upload-cert.png)

1. Wybierz pozycję **Dodaj**.

Po zarejestrowaniu certyfikatu w aplikacji w portalu rejestracji aplikacji, należy włączyć kod aplikacji klienta, aby użyć certyfikatu.

### <a name="option-2-create-a-new-application-secret"></a>Opcja 2: Tworzenie nowego klucza tajnego aplikacji

Jeśli zdecydujesz się nie używać certyfikatu, możesz utworzyć nowy klucz tajny aplikacji.

1. Wybierz pozycję **Azure Active Directory**.
1. W obszarze **rejestracje aplikacji** w usłudze Azure AD wybierz aplikację.
1. Wybierz pozycję **certyfikaty & wpisy tajne**.
1. Wybierz pozycję wpisy **tajne klienta — > nowego klucza tajnego klienta**.
1. Podaj opis klucza tajnego i czas trwania. Po zakończeniu wybierz pozycję **Dodaj**.

   Po zapisaniu klucza tajnego klienta zostanie wyświetlona wartość wpisu tajnego klienta. Skopiuj tę wartość, ponieważ nie będzie można pobrać klucza później. W celu zalogowania się jako aplikacja podaj wartość klucza z IDENTYFIKATORem aplikacji. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

   ![Skopiuj wartość klucza tajnego, ponieważ nie można pobrać jej później](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Konfigurowanie zasad dostępu do zasobów
Należy pamiętać, że konieczne może być skonfigurowanie dodatkowych uprawnień do zasobów wymaganych przez aplikację. Na przykład należy również [zaktualizować zasady dostępu magazynu kluczy](../../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) , aby zapewnić aplikacji dostęp do kluczy, wpisów tajnych lub certyfikatów.

1. W <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>przejdź do magazynu kluczy i wybierz pozycję **zasady dostępu**.
1. Wybierz pozycję **Dodaj zasady dostępu**, a następnie wybierz uprawnienia Key, Secret i Certificate, które chcesz udzielić aplikacji.  Wybierz nazwę główną usługi utworzoną wcześniej.
1. Wybierz pozycję **Dodaj** , aby dodać zasady dostępu, a następnie pozycję **Zapisz** , aby zatwierdzić zmiany.
    ![Dodawanie zasad dostępu](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [utworzyć jednostkę usługi za pomocą Azure PowerShell](howto-authenticate-service-principal-powershell.md).
* Aby dowiedzieć się więcej o określaniu zasad zabezpieczeń, zobacz [Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Aby uzyskać listę dostępnych akcji, które można udzielić lub odmówić użytkownikom, zobacz [Azure Resource Manager operacje dostawcy zasobów](../../role-based-access-control/resource-provider-operations.md).
* Aby uzyskać informacje na temat pracy z rejestracjami aplikacji przy użyciu **Microsoft Graph**, zobacz Dokumentacja interfejsu API [aplikacji](/graph/api/resources/application) .
