---
title: Uwierzytelnianie aplikacji w celu uzyskania dostępu do Azure Service Bus jednostek
description: Ten artykuł zawiera informacje o uwierzytelnianiu aplikacji przy użyciu Azure Active Directory w celu uzyskania dostępu do Azure Service Bus jednostek (kolejek, tematów itp.).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: c4e19c0ab26d491ba0b95159e274383431aefaee
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518232"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Uwierzytelnianie i Autoryzowanie aplikacji przy użyciu Azure Active Directory dostępu do Azure Service Bus jednostek
Azure Service Bus obsługuje używanie Azure Active Directory (Azure AD) do autoryzacji żądań Service Bus jednostek (kolejek, tematów, subskrypcji lub filtrów). Korzystając z usługi Azure AD, możesz użyć kontroli dostępu opartej na rolach (Azure RBAC), aby przyznać uprawnienia podmiotowi zabezpieczeń, który może być użytkownikiem, grupą lub jednostką usługi aplikacji. Aby dowiedzieć się więcej o rolach i przypisaniach ról, zobacz [opis różnych ról](../role-based-access-control/overview.md).

## <a name="overview"></a>Omówienie
Gdy podmiot zabezpieczeń (użytkownik, Grupa lub aplikacja) próbuje uzyskać dostęp do jednostki Service Bus, żądanie musi być autoryzowane. W przypadku usługi Azure AD dostęp do zasobu jest procesem dwuetapowym. 

 1. Najpierw jest uwierzytelniana tożsamość podmiotu zabezpieczeń i zwracany jest token OAuth 2,0. Nazwa zasobu do żądania tokenu to `https://servicebus.azure.net` .
 1. Następnie token jest przesyłany w ramach żądania do usługi Service Bus, aby autoryzować dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak maszyna wirtualna platformy Azure, zestaw skalowania maszyn wirtualnych lub aplikacja funkcji platformy Azure, może używać tożsamości zarządzanej do uzyskiwania dostępu do zasobów. Aby dowiedzieć się, jak uwierzytelniać żądania wysyłane przez zarządzaną tożsamość do usługi Service Bus, zobacz [uwierzytelnianie dostępu do zasobów Azure Service Bus za pomocą Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](service-bus-managed-service-identity.md). 

Krok autoryzacji wymaga, aby co najmniej jedna rola platformy Azure była przypisana do podmiotu zabezpieczeń. Azure Service Bus udostępnia role platformy Azure, które obejmują zestawy uprawnień dla Service Bus zasobów. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia, które będą miały. Aby dowiedzieć się więcej na temat przypisywania ról platformy Azure do Azure Service Bus, zobacz [role wbudowane platformy Azure dla Azure Service Bus](#azure-built-in-roles-for-azure-service-bus). 

Aplikacje natywne i aplikacje sieci Web, które wysyłają żądania do Service Bus mogą również autoryzować się z usługą Azure AD. W tym artykule przedstawiono sposób żądania tokenu dostępu i używania go do autoryzacji żądań dotyczących zasobów Service Bus. 


## <a name="assigning-azure-roles-for-access-rights"></a>Przypisywanie ról platformy Azure na potrzeby praw dostępu
Azure Active Directory (Azure AD) autoryzują prawa dostępu do zabezpieczonych zasobów za pomocą [usługi Azure RBAC](../role-based-access-control/overview.md). Azure Service Bus definiuje zestaw wbudowanych ról platformy Azure, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do jednostek Service Bus, a także definiuje role niestandardowe na potrzeby uzyskiwania dostępu do danych.

Gdy rola platformy Azure zostanie przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów lub przestrzeni nazw Service Bus. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Wbudowane role platformy Azure dla Azure Service Bus
W przypadku Azure Service Bus Zarządzanie przestrzeniami nazw i wszystkimi powiązanymi zasobami za pośrednictwem Azure Portal i interfejsu API usługi Azure Resource Management jest już chronione przy użyciu modelu RBAC platformy Azure. Platforma Azure udostępnia następujące wbudowane role platformy Azure umożliwiające autoryzowanie dostępu do Service Bus przestrzeni nazw:

- [Azure Service Bus właściciel danych](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): umożliwia dostęp do danych Service Bus przestrzeni nazw i jej jednostek (kolejek, tematów, subskrypcji i filtrów)
- [Azure Service Bus nadawcy danych](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Ta rola daje dostęp do Service Bus przestrzeni nazw i jej jednostek.
- [Azure Service Bus odbiorca danych](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Ta rola umożliwia uzyskanie dostępu do Service Bus przestrzeni nazw i jej jednostek. 

## <a name="resource-scope"></a>Zakres zasobu 
Przed przypisaniem roli platformy Azure do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, w których można określić zakres dostępu do zasobów Service Bus, rozpoczynając od najwęższego zakresu:

- **Kolejka**, **temat**lub **subskrypcja**: przypisanie roli dotyczy konkretnej jednostki Service Bus. Obecnie Azure Portal nie obsługuje przypisywania użytkowników/grup/tożsamości zarządzanych do Service Bus ról platformy Azure na poziomie subskrypcji. 
- **Przestrzeń nazw Service Bus**: przypisanie roli obejmuje całą topologię Service Bus w przestrzeni nazw oraz do skojarzonej z nią grupy odbiorców.
- **Grupa zasobów**: przypisanie roli dotyczy wszystkich zasobów Service Bus w grupie zasobów.
- **Subskrypcja**: przypisanie roli dotyczy wszystkich zasobów Service Bus we wszystkich grupach zasobów w subskrypcji.

> [!NOTE]
> Należy pamiętać, że propagacja ról platformy Azure może potrwać do 5 minut. 

Aby uzyskać więcej informacji na temat sposobu definiowania wbudowanych ról, zobacz [Omówienie definicji ról](../role-based-access-control/role-definitions.md#management-and-data-operations). Aby uzyskać informacje na temat tworzenia ról niestandardowych platformy Azure, zobacz [role niestandardowe platformy Azure](../role-based-access-control/custom-roles.md).


## <a name="assign-azure-roles-using-the-azure-portal"></a>Przypisywanie ról platformy Azure przy użyciu Azure Portal  
Aby dowiedzieć się więcej na temat zarządzania dostępem do zasobów platformy Azure przy użyciu usługi Azure RBAC i Azure Portal, zobacz [ten artykuł](..//role-based-access-control/role-assignments-portal.md). 

Po ustaleniu odpowiedniego zakresu przypisania roli przejdź do tego zasobu w Azure Portal. Wyświetl ustawienia kontroli dostępu (IAM) dla zasobu i postępuj zgodnie z tymi instrukcjami, aby zarządzać przypisaniami ról:

> [!NOTE]
> Kroki opisane poniżej przypisuje rolę do przestrzeni nazw Service Bus. Możesz wykonać te same czynności, aby przypisać rolę do innych obsługiwanych zakresów (grupy zasobów, subskrypcji itp.).

1. W [Azure Portal](https://portal.azure.com/)przejdź do przestrzeni nazw Service Bus. Wybierz pozycję **Access Control (IAM)** w menu po lewej stronie, aby wyświetlić ustawienia kontroli dostępu dla przestrzeni nazw. Jeśli musisz utworzyć Service Bus przestrzeni nazw, postępuj zgodnie z instrukcjami podanymi w tym artykule: [Tworzenie przestrzeni nazw dla Service Bus komunikatów](service-bus-create-namespace-portal.md).

    ![Z menu po lewej stronie wybierz pozycję Access Control](./media/authenticate-application/select-access-control-menu.png)
1. Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról. Na pasku narzędzi wybierz przycisk **Dodaj** , a następnie wybierz pozycję **Dodaj przypisanie roli**. 

    ![Przycisk Dodaj na pasku narzędzi](./media/authenticate-application/role-assignments-add-button.png)
1. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności:
    1. Wybierz **rolę Service Bus** , którą chcesz przypisać. 
    1. Wyszukaj w celu zlokalizowania **podmiotu zabezpieczeń** (użytkownika, grupy, nazwy głównej usługi), do którego ma zostać przypisana rola.
    1. Wybierz pozycję **Zapisz** , aby zapisać przypisanie roli. 

        ![Przypisywanie roli do użytkownika](./media/authenticate-application/assign-role-to-user.png)
    4. Tożsamość, do której przypisano rolę, jest wyświetlana na liście w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono, że użytkownicy platformy Azure znajdują się w roli właściciela danych Azure Service Bus. 
        
        ![Użytkownik na liście](./media/authenticate-application/user-in-list.png)

Możesz wykonać podobne kroki, aby przypisać rolę zakres do grupy zasobów lub subskrypcji. Po zdefiniowaniu roli i jej zakresu można przetestować to zachowanie przy użyciu [przykładów w witrynie GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Uwierzytelnianie przy użyciu aplikacji
Główną zaletą korzystania z usługi Azure AD z Service Bus jest to, że Twoje poświadczenia nie muszą już być przechowywane w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2,0 z platformy tożsamości firmy Microsoft. Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupę lub nazwę główną usługi), na którym działa aplikacja. W przypadku pomyślnego uwierzytelnienia usługa Azure AD zwraca token dostępu do aplikacji, a następnie może użyć tokenu dostępu do autoryzowania żądań do Azure Service Bus.

W poniższych sekcjach pokazano, jak skonfigurować aplikację natywną lub aplikację sieci Web do uwierzytelniania za pomocą platformy Microsoft Identity platform 2,0. Aby uzyskać więcej informacji o platformie Microsoft Identity platform 2,0, zobacz [Microsoft Identity platform (v 2.0) — Omówienie](../active-directory/develop/v2-overview.md).

Aby zapoznać się z omówieniem przepływu przyznawania kodu OAuth 2,0, zobacz temat [Autoryzuj dostęp do aplikacji sieci web Azure Active Directory przy użyciu przepływu przyznawania kodu oauth 2,0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi Azure AD
Pierwszym krokiem korzystania z usługi Azure AD w celu autoryzowania jednostek Service Bus jest zarejestrowanie aplikacji klienckiej przy użyciu dzierżawy usługi Azure AD z [Azure Portal](https://portal.azure.com/). Po zarejestrowaniu aplikacji klienckiej podaj informacje o aplikacji do usługi AD. Następnie usługa Azure AD udostępnia identyfikator klienta (nazywany również IDENTYFIKATORem aplikacji), którego można użyć do skojarzenia aplikacji z usługą Azure AD Runtime. Aby dowiedzieć się więcej o IDENTYFIKATORze klienta, zobacz [obiekty główne aplikacji i usługi w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Na poniższych ilustracjach przedstawiono procedurę rejestrowania aplikacji sieci Web:

![Rejestrowanie aplikacji](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Jeśli aplikacja jest zarejestrowana jako aplikacja natywna, można określić dowolny prawidłowy identyfikator URI dla identyfikatora URI przekierowania. W przypadku aplikacji natywnych ta wartość nie musi być rzeczywistym adresem URL. W przypadku aplikacji sieci Web identyfikator URI przekierowania musi być prawidłowym identyfikatorem URI, ponieważ określa adres URL, na który są udostępniane tokeny.

Po zarejestrowaniu aplikacji zobaczysz **Identyfikator aplikacji (klienta)** w obszarze **Ustawienia**:

![Identyfikator aplikacji zarejestrowanej aplikacji](./media/authenticate-application/application-id.png)

Aby uzyskać więcej informacji na temat rejestrowania aplikacji w usłudze Azure AD, zobacz [Integrowanie aplikacji z Azure Active Directory](../active-directory/develop/quickstart-register-app.md).

> [!IMPORTANT]
> Zanotuj **TenantId** i identyfikator **aplikacji**. Te wartości będą potrzebne do uruchomienia aplikacji.

### <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta   
Aplikacja wymaga klucza tajnego klienta, aby potwierdzić swoją tożsamość podczas żądania tokenu. Aby dodać klucz tajny klienta, wykonaj następujące kroki.

1. Przejdź do swojej rejestracji aplikacji w Azure Portal, jeśli nie jesteś jeszcze na stronie.
1. W menu po lewej stronie wybierz pozycję **certyfikaty & wpisy tajne** .
1. W obszarze wpisy **tajne klienta**wybierz pozycję **Nowy wpis tajny klienta** , aby utworzyć nowy wpis tajny.

    ![Nowy wpis tajny klienta — przycisk](./media/authenticate-application/new-client-secret-button.png)
1. Podaj opis wpisu tajnego i wybierz żądany interwał wygaśnięcia, a następnie wybierz pozycję **Dodaj**.

    ![Dodaj stronę klucza tajnego klienta](./media/authenticate-application/add-client-secret-page.png)
1. Natychmiast skopiuj wartość nowego wpisu tajnego do bezpiecznej lokalizacji. Wartość Fill jest wyświetlana tylko raz.

    ![Klucz tajny klienta](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Uprawnienia dla interfejsu API Service Bus
Jeśli aplikacja jest aplikacją konsolową, należy zarejestrować aplikację natywną i dodać uprawnienia interfejsu API do **programu Microsoft. ServiceBus** do **wymaganego zestawu uprawnień** . Natywne aplikacje potrzebują także **identyfikatora URI przekierowania** w usłudze Azure AD, który służy jako identyfikator; Identyfikator URI nie musi być miejscem docelowym sieci. Użyj `https://servicebus.microsoft.com` tego przykładu, ponieważ przykładowy kod korzysta już z tego identyfikatora URI.

### <a name="client-libraries-for-token-acquisition"></a>Biblioteki klienckie do pozyskiwania tokenów  
Po zarejestrowaniu aplikacji i przyznaniu jej uprawnień do wysyłania/odbierania danych w Azure Service Bus możesz dodać kod do aplikacji, aby uwierzytelnić podmiot zabezpieczeń i uzyskać token OAuth 2,0. Aby uwierzytelnić i uzyskać token, można użyć jednej z [bibliotek uwierzytelniania platformy tożsamości firmy Microsoft](../active-directory/develop/reference-v2-libraries.md) lub innej biblioteki typu open source, która obsługuje OpenID Connect lub Connect 1,0. Aplikacja może następnie użyć tokenu dostępu do autoryzowania żądania względem Azure Service Bus.

Aby zapoznać się z listą scenariuszy, w których są obsługiwane tokeny, zobacz sekcję [scenariusze](https://aka.ms/msal-net-scenarios) w [bibliotece Microsoft Authentication Library (MSAL) dla](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) repozytorium usługi .NET GitHub.

## <a name="sample-on-github"></a>Przykład w witrynie GitHub
Zapoznaj się z poniższym przykładem w witrynie GitHub: [kontroli dostępu opartej na rolach na platformie Azure dla Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Użyj opcji **logowania klucza tajnego klienta** , a nie opcji **logowania użytkownika interaktywnego** . Jeśli używasz opcji tajnego klienta, nie widzisz okna podręcznego. Aplikacja korzysta z identyfikatora dzierżawy i identyfikatora aplikacji na potrzeby uwierzytelniania. 

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Przed uruchomieniem przykładu, edytuj plik **app.config** i, w zależności od danego scenariusza, ustaw następujące wartości:

- `tenantId`: Ustaw wartość **TenantId** .
- `clientId`: Ustaw wartość dla wartości identyfikator **aplikacji** .
- `clientSecret`: Jeśli chcesz zalogować się przy użyciu klucza tajnego klienta, utwórz go w usłudze Azure AD. Ponadto Użyj aplikacji sieci Web lub interfejsu API zamiast aplikacji natywnej. Ponadto Dodaj aplikację w obszarze **Access Control (IAM)** w wcześniej utworzonej przestrzeni nazw.
- `serviceBusNamespaceFQDN`: Ustaw pełną nazwę DNS nowo utworzonej Service Bus przestrzeni nazw; na przykład `example.servicebus.windows.net` .
- `queueName`: Ustaw na nazwę utworzonej kolejki.
- Identyfikator URI przekierowania określony w aplikacji w poprzednich krokach.

Po uruchomieniu aplikacji konsolowej zostanie wyświetlony monit o wybranie scenariusza. Wybierz pozycję **Logowanie interakcyjne użytkownika** , wpisując jej numer i naciskając klawisz ENTER. Aplikacja wyświetli okno logowania, pyta o zgodę na dostęp do Service Bus, a następnie używa usługi do uruchamiania w ramach scenariusza wysyłania/odbierania przy użyciu tożsamości logowania.


## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej na temat usługi Azure RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md)?
- Aby dowiedzieć się, jak przypisywać przypisania ról platformy Azure i zarządzać nimi za pomocą Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST, zobacz następujące artykuły:
    - [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
    - [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu API REST](../role-based-access-control/role-assignments-rest.md)
    - [Dodawanie i usuwanie przypisań ról platformy Azure przy użyciu szablonów Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy.

- [Przykłady kontroli RBAC na platformie Azure Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
- [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
- [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)