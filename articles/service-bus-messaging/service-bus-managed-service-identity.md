---
title: Tożsamości zarządzane dla zasobów platformy Azure za pomocą Service Bus
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uzyskiwania dostępu Azure Service Bus jednostkami (kolejkami, tematami i subskrypcjami).
ms.topic: article
ms.date: 01/21/2021
ms.openlocfilehash: cac254ef6b57f1878620b1e3ca30e757d7f39a88
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529470"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Uwierzytelnianie tożsamości zarządzanej za pomocą Azure Active Directory dostępu do Azure Service Bus zasobów
[Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) to funkcja między platformami Azure, która umożliwia tworzenie bezpiecznej tożsamości skojarzonej z wdrożeniem, w ramach którego działa kod aplikacji. Następnie możesz skojarzyć ją z rolami kontroli dostępu, które przyznają niestandardowe uprawnienia dostępu do określonych zasobów platformy Azure wymaganych przez aplikację.

Za pomocą tożsamości zarządzanych platforma Azure zarządza tą tożsamością środowiska uruchomieniowego. Nie musisz przechowywać ani chronić kluczy dostępu w kodzie aplikacji ani konfiguracji, ani dla samej tożsamości, ani dla zasobów, do których musisz uzyskać dostęp. Aplikacja Service Bus uruchomiona wewnątrz aplikacji usługi Azure App Service lub na maszynie wirtualnej z włączoną obsługą jednostek zarządzanych dla zasobów platformy Azure nie musi obsługiwać reguł i kluczy sygnatur dostępu współdzielonego ani żadnych innych tokenów dostępu. Aplikacja klienacyjna potrzebuje tylko adresu punktu końcowego przestrzeni nazw Service Bus Messaging. Gdy aplikacja nawiązuje połączenie, Service Bus kontekst zarządzanej jednostki z klientem w operacji przedstawionej w przykładzie w dalszej części tego artykułu. Po skojarzoną z tożsamością zarządzaną klient Service Bus może wykonać wszystkie autoryzowane operacje. Autoryzacja jest udzielana przez skojarzenie jednostki zarządzanej z Service Bus rolami. 

## <a name="overview"></a>Omówienie
Gdy podmiot zabezpieczeń (użytkownik, grupa lub aplikacja) próbuje uzyskać dostęp do jednostki Service Bus, żądanie musi być autoryzowane. W usłudze Azure AD dostęp do zasobu jest procesem dwuetapowym. 

 1. Najpierw uwierzytelniana jest tożsamość podmiotu zabezpieczeń i jest zwracany token OAuth 2.0. Nazwa zasobu do żądania tokenu to `https://servicebus.azure.net` .
 1. Następnie token jest przekazywany jako część żądania do usługi Service Bus w celu autoryzowania dostępu do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawierało token dostępu OAuth 2.0 w czasie wykonywania. Jeśli aplikacja działa w ramach jednostki platformy Azure, takiej jak maszyna wirtualna platformy Azure, zestaw skalowania maszyn wirtualnych lub aplikacja funkcji platformy Azure, może uzyskać dostęp do zasobów przy użyciu tożsamości zarządzanej. 

Krok autoryzacji wymaga przypisania co najmniej jednej roli platformy Azure do podmiotu zabezpieczeń. Azure Service Bus udostępnia role platformy Azure, które obejmują zestawy uprawnień dla Service Bus zasobów. Role przypisane do podmiotu zabezpieczeń określają uprawnienia, które będzie mieć podmiot zabezpieczeń. Aby dowiedzieć się więcej na temat przypisywania ról platformy Azure do usługi Azure Service Bus, zobacz Wbudowane role [platformy Azure dla Azure Service Bus](#azure-built-in-roles-for-azure-service-bus). 

Aplikacje natywne i aplikacje internetowe, które Service Bus do usługi, można również autoryzować za pomocą usługi Azure AD. W tym artykule pokazano, jak zażądać tokenu dostępu i użyć go do autoryzowania żądań Service Bus zasobów. 


## <a name="assigning-azure-roles-for-access-rights"></a>Przypisywanie ról platformy Azure w celu uzyskania praw dostępu
Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem kontroli dostępu na podstawie ról [(RBAC)](../role-based-access-control/overview.md)platformy Azure. Azure Service Bus definiuje zestaw wbudowanych ról platformy Azure, które obejmują wspólne zestawy uprawnień używane do uzyskiwania dostępu do jednostek usługi Service Bus, a także można definiować role niestandardowe do uzyskiwania dostępu do danych.

Gdy rola platformy Azure jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure udziela dostępu do tych zasobów dla tego podmiotu zabezpieczeń. Zakres dostępu może być ograniczony do poziomu subskrypcji, grupy zasobów lub Service Bus nazw. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, jednostką usługi aplikacji lub tożsamością zarządzaną dla zasobów platformy Azure.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Wbudowane role platformy Azure dla Azure Service Bus
Na Azure Service Bus zarządzanie przestrzeniami nazw i wszystkimi powiązanymi zasobami za pośrednictwem interfejsu Azure Portal i interfejsu API zarządzania zasobami platformy Azure jest już chronione przy użyciu modelu RBAC platformy Azure. Platforma Azure udostępnia poniższe wbudowane role platformy Azure do autoryzowania dostępu do Service Bus nazw:

- [Azure Service Bus danych:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)umożliwia dostęp do danych Service Bus przestrzeni nazw i jej jednostek (kolejek, tematów, subskrypcji i filtrów)
- [Azure Service Bus data sender (Nadawca danych):](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)ta rola umożliwia wysyłanie dostępu do Service Bus nazw i jej jednostek.
- [Azure Service Bus Data Receiver:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)ta rola umożliwia uzyskanie dostępu do Service Bus nazw i jej jednostek. 

## <a name="resource-scope"></a>Zakres zasobu 
Przed przypisaniem roli platformy Azure do podmiotu zabezpieczeń określ zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, na których można określić zakres dostępu do Service Bus zasobów, począwszy od najwęższych zakresów:

- **Kolejka,** **temat** lub **subskrypcja:** przypisanie roli ma zastosowanie do określonej Service Bus jednostki. Obecnie usługa Azure Portal nie obsługuje przypisywania użytkowników/grup/tożsamości zarządzanych do Service Bus platformy Azure na poziomie subskrypcji. Oto przykład użycia polecenia interfejsu wiersza polecenia platformy Azure: [az-role-assignment-create](/cli/azure/role/assignment?#az-role-assignment-create) w celu przypisania tożsamości do Service Bus platformy Azure: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus nazw:** przypisanie roli obejmuje całą topologię Service Bus przestrzeni nazw i skojarzoną z nią grupą odbiorców.
- **Grupa zasobów:** Przypisanie roli ma zastosowanie do wszystkich Service Bus zasobów w grupie zasobów.
- **Subskrypcja:** Przypisanie roli ma zastosowanie do Service Bus zasobów we wszystkich grupach zasobów w subskrypcji.

> [!NOTE]
> Pamiętaj, że propagacja przypisań ról platformy Azure może potrwać do pięciu minut. 

Aby uzyskać więcej informacji na temat sposobu definiowania ról wbudowanych, zobacz [Understand role definitions (Opis definicji ról).](../role-based-access-control/role-definitions.md#management-and-data-operations) Aby uzyskać informacje na temat tworzenia ról niestandardowych platformy Azure, zobacz [Role niestandardowe platformy Azure.](../role-based-access-control/custom-roles.md)

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie wirtualnej
Zanim będzie można użyć tożsamości zarządzanych dla zasobów platformy Azure w celu autoryzowania zasobów Service Bus z maszyny wirtualnej, należy najpierw włączyć tożsamości zarządzane dla zasobów platformy Azure na maszynie wirtualnej. Aby dowiedzieć się, jak włączyć tożsamości zarządzane dla zasobów platformy Azure, zobacz jeden z tych artykułów:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager bibliotek klienckich](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Udzielanie uprawnień tożsamości zarządzanej w usłudze Azure AD
Aby autoryzować żądanie do usługi Service Bus z tożsamości zarządzanej w aplikacji, najpierw skonfiguruj ustawienia kontroli dostępu na podstawie ról (RBAC) platformy Azure dla tej tożsamości zarządzanej. Azure Service Bus definiuje role platformy Azure, które obejmują uprawnienia do wysyłania i odczytywania z Service Bus. Gdy rola platformy Azure jest przypisana do tożsamości zarządzanej, tożsamość zarządzana uzyskuje dostęp do Service Bus jednostek w odpowiednim zakresie.

Aby uzyskać więcej informacji na temat przypisywania ról platformy Azure, zobacz Authenticate [and authorize with Azure Active Directory for access to Service Bus resources (Uwierzytelnianie](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)i autoryzacja za pomocą usługi Azure Active Directory w celu uzyskania dostępu do Service Bus zasobów).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Używanie Service Bus tożsamościami zarządzanymi dla zasobów platformy Azure
Aby używać Service Bus tożsamościami zarządzanymi, musisz przypisać tożsamość do roli i odpowiedniego zakresu. Procedura w tej sekcji używa prostej aplikacji, która działa w ramach tożsamości zarządzanej i uzyskuje dostęp do Service Bus zasobów.

W tym miejscu używamy przykładowej aplikacji internetowej hostowanej [w](https://azure.microsoft.com/services/app-service/)Azure App Service . Aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji internetowej, zobacz Tworzenie aplikacji internetowej [ASP.NET Core na platformie Azure](../app-service/quickstart-dotnetcore.md)

Po utworzeniu aplikacji wykonaj następujące kroki: 

1. Przejdź do **ustawień i** wybierz pozycję **Tożsamość.** 
1. Wybierz stan **Wł.**.  
1. Wybierz pozycję **Zapisz**, aby zapisać ustawienie. 

    ![Tożsamość zarządzana dla aplikacji internetowej](./media/service-bus-managed-service-identity/identity-web-app.png)

Po włączeniu tego ustawienia nowa tożsamość usługi jest tworzona w usłudze Azure Active Directory (Azure AD) i konfigurowana na App Service hostie.

> [!NOTE]
> W przypadku korzystania z tożsamości zarządzanej ciąg połączenia powinien mieć format: `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=ManagedIdentity` .

Teraz przypisz tę tożsamość usługi do roli w wymaganym zakresie w Service Bus zasobów.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Aby przypisać role platformy Azure przy użyciu Azure Portal
Aby przypisać rolę do Service Bus nazw, przejdź do przestrzeni nazw w Azure Portal. Wyświetl ustawienia Access Control (Zarządzanie dostępem i dostępem i dostępem) dla zasobu i postępuj zgodnie z tymi instrukcjami, aby zarządzać przypisaniami ról:

> [!NOTE]
> Poniższe kroki przypisz rolę tożsamości usługi do twojej Service Bus nazw. Możesz wykonać te same kroki, aby przypisać rolę w innych obsługiwanych zakresach (grupa zasobów i subskrypcja). 
> 
> [Utwórz przestrzeń nazw Service Bus Messaging,](service-bus-create-namespace-portal.md) jeśli jej nie masz. 

1. W Azure Portal przejdź do swojej Service Bus nazw i wyświetl **Przegląd** dla przestrzeni nazw. 
1. Wybierz **Access Control (IAM)** w menu po lewej stronie, aby wyświetlić ustawienia kontroli dostępu dla Service Bus nazw.
1.  Wybierz **kartę Przypisania ról,** aby wyświetlić listę przypisań ról.
3.  Wybierz **pozycję Dodaj**, a następnie wybierz pozycję Dodaj **przypisanie roli.**
4.  Na stronie **Dodawanie przypisania roli** wykonaj następujące kroki:
    1. W **przypadku** roli wybierz Service Bus, którą chcesz przypisać. W tym przykładzie jest to Azure Service Bus **danych.**
    1. W polu **Przypisz dostęp do** wybierz pozycję **App Service** obszarze **Tożsamość zarządzana przypisana przez system.** 
    1. Wybierz **subskrypcję,** w której utworzono tożsamość zarządzaną dla aplikacji internetowej.
    1. Wybierz tożsamość **zarządzaną** dla utworzonej aplikacji internetowej. Domyślna nazwa tożsamości jest taka sama jak nazwa aplikacji internetowej. 
    1. Następnie wybierz pozycję **Zapisz**.
        
        ![Strona Dodawania przypisania roli](./media/service-bus-managed-service-identity/add-role-assignment-page.png)

    Po przypisaniu roli aplikacja internetowa będzie mieć dostęp do jednostek Service Bus w zdefiniowanym zakresie. 

    > [!NOTE]
    > Aby uzyskać listę usług, które obsługują tożsamości zarządzane, zobacz [Usługi, które obsługują tożsamości zarządzane dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

### <a name="run-the-app"></a>Uruchamianie aplikacji
Teraz zmodyfikuj domyślną stronę ASP.NET utworzonej aplikacji. Możesz użyć kodu aplikacji internetowej z [tego repozytorium GitHub.](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)  

Strona Default.aspx to strona docelowa. Kod można znaleźć w pliku Default.aspx.cs. Wynikiem jest minimalna aplikacja internetowa z kilkoma  polami  wprowadzania oraz przyciski wysyłania i odbierania, które łączą się z Service Bus do wysyłania lub odbierania komunikatów.

Zwróć uwagę na sposób inicjowania obiektu [MessagingFactory.](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) Zamiast używać dostawcy tokenu dostępu współdzielonego (SAS), kod tworzy dostawcę tokenu dla tożsamości zarządzanej za pomocą `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` wywołania . W związku z tym nie ma żadnych wpisów tajnych do zachowania i używania. Przepływ kontekstu tożsamości zarządzanej do usługi Service Bus i uściślinia autoryzacji są automatycznie obsługiwane przez dostawcę tokenów. Jest to prostszy model niż użycie sygnatury dostępu współdzielonego.

Po wymusieniu tych zmian opublikuj i uruchom aplikację. Poprawne dane publikowania można łatwo uzyskać, pobierając, a następnie importując profil publikowania w Visual Studio:

![Uzyskiwanie profilu publikowania](./media/service-bus-managed-service-identity/msi3.png)
 
Aby wysyłać lub odbierać komunikaty, wprowadź nazwę przestrzeni nazw i nazwę utworzonej jednostki. Następnie kliknij pozycję Wyślij **lub** **odbierz**.


> [!NOTE]
> - Tożsamość zarządzana działa tylko w środowisku platformy Azure, w usługach App Services, na maszynach wirtualnych platformy Azure i w zestawach skalowania. W przypadku aplikacji platformy .NET biblioteka Microsoft.Azure.Services.AppAuthentication, która jest używana przez pakiet NuGet programu Service Bus, zapewnia abstrakcję tego protokołu i obsługuje lokalne środowisko programizacji. Ta biblioteka umożliwia również testowanie kodu lokalnie na komputerze dewelopera przy użyciu konta użytkownika z programu Visual Studio, interfejsu wiersza polecenia platformy Azure 2.0 lub zintegrowanego uwierzytelniania usługi Active Directory. Aby uzyskać więcej informacji na temat opcji tworzenia oprogramowania lokalnego przy użyciu tej biblioteki, zobacz [Service-to-service authentication to Azure Key Vault using .NET (Uwierzytelnianie typu usługa-usługa w](/dotnet/api/overview/azure/service-to-service-authentication)celu Azure Key Vault przy użyciu programu .NET).  

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus komunikatów, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
