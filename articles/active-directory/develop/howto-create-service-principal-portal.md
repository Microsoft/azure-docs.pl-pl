---
title: Tworzenie aplikacji usługi Azure AD & jednostki usługi w portalu
titleSuffix: Microsoft identity platform
description: Utwórz nową jednostkę Azure Active Directory aplikacji & do zarządzania dostępem do zasobów za pomocą kontroli dostępu opartej na rolach w usłudze Azure Resource Manager.
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
ms.openlocfilehash: 3ccc340727a437b3b1e953ea5e742ecdf7f21d40
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814086"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Instrukcje: używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów

W tym artykule pokazano, jak utworzyć nową aplikację usługi Azure Active Directory (Azure AD), która może być używana z kontrolą dostępu opartą na rolach. Jeśli masz aplikacje, usługi hostowane lub zautomatyzowane narzędzia, które muszą uzyskać dostęp do zasobów lub je zmodyfikować, możesz utworzyć tożsamość dla aplikacji. Ta tożsamość jest określana jako jednostka usługi. Dostęp do zasobów jest ograniczony przez role przypisane do jednostki usługi, co zapewnia kontrolę nad tym, do których zasobów można uzyskać dostęp i na jakim poziomie. Ze względów bezpieczeństwa zawsze zaleca się używanie jednostek usługi ze zautomatyzowanymi narzędziami, zamiast zezwalać im na logowanie za pomocą tożsamości użytkownika.

W tym artykule pokazano, jak za pomocą portalu utworzyć jednostkę usługi w Azure Portal. Koncentruje się on na aplikacji z jedną dzierżawą, w której aplikacja jest przeznaczona do uruchamiania tylko w jednej organizacji. Aplikacje z jedną dzierżawą są zwykle używane w przypadku aplikacji biznesowych uruchamianych w organizacji.  Możesz również użyć [Azure PowerShell, aby utworzyć jednostkę usługi](howto-authenticate-service-principal-powershell.md).

> [!IMPORTANT]
> Zamiast tworzyć jednostkę usługi, rozważ użycie tożsamości zarządzanych dla zasobów platformy Azure dla tożsamości aplikacji. Jeśli kod działa w usłudze, która obsługuje tożsamości zarządzane i uzyskuje dostęp do zasobów, które obsługują uwierzytelnianie usługi Azure AD, tożsamości zarządzane są dla Ciebie lepszym rozwiązaniem. Aby dowiedzieć się więcej o tożsamościach zarządzanych dla zasobów platformy Azure, w tym o usługach, które ją obecnie obsługują, zobacz Co to są tożsamości zarządzane [dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

## <a name="app-registration-app-objects-and-service-principals"></a>Rejestracja aplikacji, obiekty aplikacji i jednostki usługi
Nie ma możliwości bezpośredniego utworzenia jednostki usługi przy użyciu Azure Portal.  Podczas rejestrowania aplikacji za pośrednictwem Azure Portal obiekt aplikacji i jednostkę usługi są automatycznie tworzone w katalogu macierzystym lub dzierżawie.  Aby uzyskać więcej informacji na temat relacji między rejestracją aplikacji, obiektami aplikacji i jednostkami usługi, przeczytaj temat Application and service principal objects in Azure Active Directory (Obiekty aplikacji i jednostki usługi [w usłudze Azure Active Directory](app-objects-and-service-principals.md).

## <a name="permissions-required-for-registering-an-app"></a>Uprawnienia wymagane do zarejestrowania aplikacji

Musisz mieć wystarczające uprawnienia, aby zarejestrować aplikację w dzierżawie usługi Azure AD i przypisać do aplikacji rolę w subskrypcji platformy Azure.

### <a name="check-azure-ad-permissions"></a>Sprawdzanie uprawnień usługi Azure AD

1. Wybierz pozycję **Azure Active Directory**.
1. Zanotuj swoją rolę. Jeśli masz rolę **Użytkownik,** upewnij się, że aplikacje mogą rejestrować osoby inne niż administratorzy.

   ![Znajdź swoją rolę. Jeśli jesteś użytkownikiem, upewnij się, że osoby niebędący administratorami mogą rejestrować aplikacje](./media/howto-create-service-principal-portal/view-user-info.png)

1. W okienku po lewej stronie wybierz **pozycję Ustawienia użytkownika.**
1. Sprawdź **Rejestracje aplikacji** konfiguracji. Tę wartość może ustawić tylko administrator. W przypadku ustawienia na **wartość Tak** każdy użytkownik w dzierżawie usługi Azure AD może zarejestrować aplikację.

Jeśli ustawienie rejestracji aplikacji jest ustawione na wartość **Nie**, tylko użytkownicy z rolą administratora mogą rejestrować te typy aplikacji. Zobacz [Role wbudowane usługi Azure AD, aby dowiedzieć](../roles/permissions-reference.md#all-roles) się więcej o dostępnych rolach administratora i określonych uprawnieniach w usłudze Azure AD, które są nadane każdej roli. Jeśli twoje konto ma przypisaną rolę Użytkownik, ale ustawienie rejestracji aplikacji jest ograniczone do administratorów, poproś administratora o przypisanie Ci jednej z ról administratora, która może tworzyć wszystkie aspekty rejestracji aplikacji i zarządzać nimi, lub o umożliwienie użytkownikom rejestrowania aplikacji.

### <a name="check-azure-subscription-permissions"></a>Sprawdzanie uprawnień subskrypcji platformy Azure

W ramach subskrypcji platformy Azure Twoje konto musi mieć `Microsoft.Authorization/*/Write` dostęp, aby przypisać rolę do aplikacji usługi AD. Ta akcja jest wykonywana za pośrednictwem roli [Właściciel](../../role-based-access-control/built-in-roles.md#owner) lub [Administrator dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator). Jeśli Twoje konto ma przypisaną **rolę Współautor,** nie masz odpowiednich uprawnień. Podczas próby przypisania roli do jednostki usługi wystąpi błąd.

Aby sprawdzić uprawnienia subskrypcji:

1. Wyszukaj i wybierz **pozycję Subskrypcje** lub wybierz **pozycję Subskrypcje** na **stronie głównej.**

   ![Wyszukaj](./media/howto-create-service-principal-portal/select-subscription.png)

1. Wybierz subskrypcję, w której chcesz utworzyć jednostkę usługi.

   ![Wybieranie subskrypcji do przypisania](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Jeśli nie widzisz szukanych subskrypcji, wybierz **filtr subskrypcji globalnych.** Upewnij się, że wybrano wybraną subskrypcję dla portalu.

1. Wybierz **pozycję Moje uprawnienia.** Następnie wybierz pozycję **Kliknij tutaj, aby wyświetlić pełne szczegóły dostępu dla tej subskrypcji.**

   ![Wybierz subskrypcję, w której chcesz utworzyć jednostkę usługi](./media/howto-create-service-principal-portal/view-details.png)

1. Wybierz **pozycję** Wyświetl **w przypisaniach ról,** aby wyświetlić przypisane role i określić, czy masz odpowiednie uprawnienia do przypisywania roli do aplikacji usługi AD. Jeśli nie, poproś administratora subskrypcji o dodanie Cię do roli administratora dostępu użytkowników. Na poniższej ilustracji użytkownik ma przypisaną rolę Właściciel, co oznacza, że użytkownik ma odpowiednie uprawnienia.

   ![W tym przykładzie pokazano, że użytkownikowi przypisano rolę właściciela](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="register-an-application-with-azure-ad-and-create-a-service-principal"></a>Rejestrowanie aplikacji w usłudze Azure AD i tworzenie jednostki usługi

Przejdźmy od razu do tworzenia tożsamości. W przypadku wystąpienia problemu sprawdź [](#permissions-required-for-registering-an-app) wymagane uprawnienia, aby upewnić się, że konto może utworzyć tożsamość.

1. Zaloguj się do konta platformy Azure za pomocą <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Wybierz pozycję **Azure Active Directory**.
1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz **pozycję Nowa rejestracja.**
1. Nazwij aplikację. Wybierz obsługiwany typ konta, który określa, kto może używać aplikacji. W **obszarze Redirect URI**(Adres URI przekierowania) wybierz pozycję Web **(Sieć Web)** dla typu aplikacji, którą chcesz utworzyć. Wprowadź adres URI, do którego jest wysyłany token dostępu. Nie można utworzyć poświadczeń dla aplikacji [natywnej](../manage-apps/application-proxy-configure-native-client-application.md). Tego typu nie można używać w przypadku aplikacji zautomatyzowanej. Po ustawieniu wartości wybierz pozycję **Zarejestruj**.

   ![Wpisz nazwę aplikacji](./media/howto-create-service-principal-portal/create-app.png)

Utworzono aplikację usługi Azure AD i jednostkę usługi.

> [!NOTE]
> W usłudze Azure AD można zarejestrować wiele aplikacji o tej samej nazwie, ale aplikacje muszą mieć różne identyfikatory aplikacji (klienta).

## <a name="assign-a-role-to-the-application"></a>Przypisywanie roli do aplikacji

Aby uzyskać dostęp do zasobów w ramach subskrypcji, musisz przypisać rolę do aplikacji. Zdecyduj, która rola oferuje odpowiednie uprawnienia dla aplikacji. Aby dowiedzieć się więcej o dostępnych rolach, zobacz [Role wbudowane platformy Azure.](../../role-based-access-control/built-in-roles.md)

Zakres można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone do niższych poziomów zakresu. Na przykład dodanie aplikacji do roli *Czytelnik* dla grupy zasobów oznacza, że może odczytywać grupę zasobów i wszystkie zasoby, które zawiera.

1. W Azure Portal wybierz poziom zakresu, do których chcesz przypisać aplikację. Aby na przykład przypisać rolę w zakresie subskrypcji, wyszukaj i wybierz pozycję Subskrypcje lub wybierz pozycję Subskrypcje **na** **stronie głównej.**

   ![Na przykład przypisz rolę w zakresie subskrypcji](./media/howto-create-service-principal-portal/select-subscription.png)

1. Wybierz określoną subskrypcję, do których chcesz przypisać aplikację.

   ![Wybieranie subskrypcji do przypisania](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Jeśli nie widzisz subskrypcji, którego szukasz, wybierz **filtr subskrypcji globalnych**. Upewnij się, że wybrano wybraną subskrypcję dla portalu.

1. Wybierz pozycję **Kontrola dostępu (IAM)** .
1. Wybierz pozycję **Dodaj przypisanie roli**.
1. Wybierz rolę, którą chcesz przypisać do aplikacji. Aby na przykład umożliwić aplikacji wykonywanie akcji,  takich jak ponowne uruchomienie **,** **uruchamianie** i zatrzymywanie wystąpień, wybierz rolę **Współautor.**  Dowiedz się więcej o [dostępnych rolach](../../role-based-access-control/built-in-roles.md) Domyślnie aplikacje usługi Azure AD nie są wyświetlane w dostępnych opcjach. Aby znaleźć aplikację, wyszukaj nazwę i wybierz ją.

   ![Wybieranie roli, która ma zostać przypisana do aplikacji](./media/howto-create-service-principal-portal/select-role.png)

1. Wybierz **pozycję** Zapisz, aby zakończyć przypisywanie roli. Aplikacja zostanie wyświetlona na liście użytkowników z rolą dla tego zakresu.

Twoja jednostka usługi jest ustawiona. Możesz zacząć używać go do uruchamiania skryptów lub aplikacji. Aby zarządzać jednostką usługi (uprawnienia, uprawnienia wyrażane przez użytkownika, zobacz, którzy użytkownicy wyraziły zgodę, przejrzyj uprawnienia, zobacz informacje dotyczące logowania i nie tylko), przejdź do tematu **Aplikacje dla przedsiębiorstw.**

W następnej sekcji pokazano, jak uzyskać wartości, które są potrzebne podczas programowego logowania.

## <a name="get-tenant-and-app-id-values-for-signing-in"></a>Uzyskiwanie wartości dzierżawy i identyfikatora aplikacji na celu zalogowanie się

Podczas programowego logowania przekaż identyfikator dzierżawy wraz z żądaniem uwierzytelniania i identyfikatorem aplikacji.  Potrzebny jest również certyfikat lub klucz uwierzytelniania (opisany w poniższej sekcji). Aby uzyskać te wartości, wykonaj następujące kroki:

1. Wybierz pozycję **Azure Active Directory**.
1. Z **Rejestracje aplikacji** w usłudze Azure AD wybierz aplikację.
1. Skopiuj identyfikator katalogu (dzierżawy) i przechowuj go w kodzie aplikacji.

    ![Skopiuj katalog (identyfikator dzierżawy) i przechowuj go w kodzie aplikacji](./media/howto-create-service-principal-portal/copy-tenant-id.png)

    Identyfikator katalogu (dzierżawy) można również znaleźć na stronie przeglądu katalogu domyślnego.

1. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji.

   ![Kopiowanie identyfikatora aplikacji (klienta)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="authentication-two-options"></a>Uwierzytelnianie: dwie opcje

Istnieją dwa typy uwierzytelniania dostępne dla jednostki usługi: uwierzytelnianie oparte na hasłach (klucz tajny aplikacji) i uwierzytelnianie oparte na certyfikatach. *Zalecamy użycie certyfikatu*, ale można również utworzyć klucz tajny aplikacji.

### <a name="option-1-upload-a-certificate"></a>Opcja 1. Przekazywanie certyfikatu

Możesz użyć istniejącego certyfikatu, jeśli go masz.  Opcjonalnie można utworzyć certyfikat z podpisem własnym tylko do *celów testowych.* Aby utworzyć certyfikat z podpisem własnym, otwórz program PowerShell i uruchom plik [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) z następującymi parametrami, aby utworzyć certyfikat w magazynie certyfikatów użytkownika na komputerze:

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Wyeksportuj ten certyfikat do pliku przy [użyciu](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) przystawki MMC Zarządzanie certyfikatem użytkownika dostępnej z witryny windows Panel sterowania.

1. Wybierz **polecenie Uruchom** z menu **Start,** a następnie wprowadź **polecenie certmgr.msc.**

   Zostanie wyświetlone narzędzie Menedżer certyfikatów dla bieżącego użytkownika.

1. Aby wyświetlić certyfikaty, w obszarze **Certyfikaty — bieżący użytkownik** w okienku po lewej stronie rozwiń katalog Osobiste. 
1. Kliknij prawym przyciskiem myszy utworzony certyfikat i wybierz polecenie **Eksportuj** wszystkie zadania >pozycję .
1. Postępuj zgodnie z śledzenia kreatora eksportu certyfikatów.  Nie eksportuj klucza prywatnego i eksportuj go do pliku . Plik CER.

Aby przekazać certyfikat:

1. Wybierz pozycję **Azure Active Directory**.
1. Z **Rejestracje aplikacji** w usłudze Azure AD wybierz aplikację.
1. Wybierz **pozycję Certyfikaty i & tajne.**
1. Wybierz **pozycję Przekaż certyfikat** i wybierz certyfikat (istniejący lub wyeksportowany certyfikat z podpisem własnym).

    ![Wybierz pozycję Przekaż certyfikat i wybierz ten, który chcesz dodać](./media/howto-create-service-principal-portal/upload-cert.png)

1. Wybierz pozycję **Dodaj**.

Po zarejestrowaniu certyfikatu w aplikacji w portalu rejestracji aplikacji włącz kod aplikacji klienckiej, aby użyć certyfikatu.

### <a name="option-2-create-a-new-application-secret"></a>Opcja 2. Tworzenie nowego tajnego kluczem aplikacji

Jeśli nie chcesz używać certyfikatu, możesz utworzyć nowy klucz tajny aplikacji.

1. Wybierz pozycję **Azure Active Directory**.
1. Z **Rejestracje aplikacji** w usłudze Azure AD wybierz aplikację.
1. Wybierz **pozycję Certyfikaty & wpisów tajnych.**
1. Wybierz **pozycję Client secrets -> New client secret (Wpisy tajne klienta —**> klucz tajny nowego klienta).
1. Podaj opis tajnego i czas trwania. Gdy wszystko będzie gotowe, wybierz pozycję **Dodaj**.

   Po zapisaniu tajnego klienta zostanie wyświetlona wartość tego tajnego klienta. Skopiuj tę wartość, ponieważ nie będzie można później pobrać klucza. Należy podać wartość klucza z identyfikatorem aplikacji, aby zalogować się jako aplikacja. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

   ![Skopiuj wartość w kluczu tajnym, ponieważ nie będzie można jej później pobrać](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Konfigurowanie zasad dostępu do zasobów
Należy pamiętać, że może być konieczne skonfigurowanie dodatkowych uprawnień do zasobów, do których aplikacja musi uzyskać dostęp. Na przykład należy również [zaktualizować](../../key-vault/general/security-features.md#privileged-access) zasady dostępu magazynu kluczy, aby zapewnić aplikacji dostęp do kluczy, wpisów tajnych lub certyfikatów.

1. W <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>przejdź do magazynu kluczy i wybierz pozycję **Zasady dostępu.**
1. Wybierz **pozycję Dodaj zasady dostępu,** a następnie wybierz uprawnienia klucza, klucza tajnego i certyfikatu, które chcesz przyznać aplikacji.  Wybierz utworzoną wcześniej jednostkę usługi.
1. Wybierz **pozycję Dodaj,** aby dodać zasady dostępu, a następnie pozycję **Zapisz,** aby zatwierdzić zmiany.
    ![Dodawanie zasad dostępu](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, [jak używać Azure PowerShell do tworzenia jednostki usługi.](howto-authenticate-service-principal-powershell.md)
* Aby dowiedzieć się więcej na temat określania zasad zabezpieczeń, zobacz [Azure role-based access control (Azure RBAC) (Kontrola](../../role-based-access-control/role-assignments-portal.md)dostępu oparta na rolach na platformie Azure ).
* Aby uzyskać listę dostępnych akcji, które można przyznać lub odmówić użytkownikom, [zobacz Azure Resource Manager Resource Provider .](../../role-based-access-control/resource-provider-operations.md)
* Aby uzyskać informacje na temat pracy z rejestracjami aplikacji przy **użyciu Microsoft Graph**, zobacz [dokumentacja](/graph/api/resources/application) interfejsu API aplikacji.
