---
title: Zarządzane tożsamości dla zasobów platformy Azure z Service Bus
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uzyskiwania dostępu do Azure Service Bus jednostek (kolejek, tematów i subskrypcji).
ms.topic: article
ms.date: 10/21/2020
ms.openlocfilehash: 1efcd3c48e7e4a431a0c72c4b3b84531b44e973e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425524"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Azure Service Bus
[Zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) to funkcja obejmująca wiele platform Azure, która umożliwia tworzenie bezpiecznej tożsamości skojarzonej z wdrożeniem, w ramach którego działa kod aplikacji. Następnie można powiązać tę tożsamość z rolami kontroli dostępu, które przyznają niestandardowe uprawnienia dostępu do określonych zasobów platformy Azure wymaganych przez aplikację.

Przy użyciu tożsamości zarządzanych platforma Azure zarządza tą tożsamością środowiska uruchomieniowego. Nie jest konieczne przechowywanie i ochrona kluczy dostępu w kodzie lub konfiguracji aplikacji, zarówno w przypadku tożsamości, jak i dla zasobów, do których należy uzyskać dostęp. Aplikacja kliencka Service Bus uruchomiona w aplikacji Azure App Service lub na maszynie wirtualnej z włączonymi jednostkami zarządzanymi dla obsługi zasobów platformy Azure nie musi obsługiwać reguł i kluczy SAS ani żadnych innych tokenów dostępu. Aplikacja kliencka wymaga tylko adresu punktu końcowego przestrzeni nazw wiadomości Service Bus. Gdy aplikacja nawiązuje połączenie, Service Bus powiązać kontekstu jednostki zarządzanej z klientem w operacji, która jest wyświetlana w przykładzie w dalszej części tego artykułu. Po skojarzeniu jej z tożsamością zarządzaną klient Service Bus może wykonać wszystkie autoryzowane operacje. Autoryzacja jest przyznawana przez skojarzenie jednostki zarządzanej z rolami Service Bus. 

## <a name="overview"></a>Omówienie
Gdy podmiot zabezpieczeń (użytkownik, Grupa lub aplikacja) próbuje uzyskać dostęp do jednostki Service Bus, żądanie musi być autoryzowane. W przypadku usługi Azure AD dostęp do zasobu jest procesem dwuetapowym. 

 1. Najpierw jest uwierzytelniana tożsamość podmiotu zabezpieczeń i zwracany jest token OAuth 2,0. Nazwa zasobu do żądania tokenu to `https://servicebus.azure.net` .
 1. Następnie token jest przesyłany w ramach żądania do usługi Service Bus, aby autoryzować dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak maszyna wirtualna platformy Azure, zestaw skalowania maszyn wirtualnych lub aplikacja funkcji platformy Azure, może używać tożsamości zarządzanej do uzyskiwania dostępu do zasobów. 

Krok autoryzacji wymaga, aby co najmniej jedna rola platformy Azure była przypisana do podmiotu zabezpieczeń. Azure Service Bus udostępnia role platformy Azure, które obejmują zestawy uprawnień dla Service Bus zasobów. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia, które będą miały. Aby dowiedzieć się więcej na temat przypisywania ról platformy Azure do Azure Service Bus, zobacz [role wbudowane platformy Azure dla Azure Service Bus](#azure-built-in-roles-for-azure-service-bus). 

Aplikacje natywne i aplikacje sieci Web, które wysyłają żądania do Service Bus mogą również autoryzować się z usługą Azure AD. W tym artykule przedstawiono sposób żądania tokenu dostępu i używania go do autoryzacji żądań dotyczących zasobów Service Bus. 


## <a name="assigning-azure-roles-for-access-rights"></a>Przypisywanie ról platformy Azure na potrzeby praw dostępu
Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md). Azure Service Bus definiuje zestaw wbudowanych ról platformy Azure, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do jednostek Service Bus, a także definiuje role niestandardowe na potrzeby uzyskiwania dostępu do danych.

Gdy rola platformy Azure zostanie przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów lub przestrzeni nazw Service Bus. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, główną usługą aplikacji lub zarządzaną tożsamością dla zasobów platformy Azure.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Wbudowane role platformy Azure dla Azure Service Bus
W przypadku Azure Service Bus Zarządzanie przestrzeniami nazw i wszystkimi powiązanymi zasobami za pośrednictwem Azure Portal i interfejsu API usługi Azure Resource Management jest już chronione przy użyciu modelu RBAC platformy Azure. Platforma Azure udostępnia następujące wbudowane role platformy Azure umożliwiające autoryzowanie dostępu do Service Bus przestrzeni nazw:

- [Azure Service Bus właściciel danych](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): umożliwia dostęp do danych Service Bus przestrzeni nazw i jej jednostek (kolejek, tematów, subskrypcji i filtrów)
- [Azure Service Bus nadawcy danych](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Ta rola daje dostęp do Service Bus przestrzeni nazw i jej jednostek.
- [Azure Service Bus odbiorca danych](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Ta rola umożliwia uzyskanie dostępu do Service Bus przestrzeni nazw i jej jednostek. 

## <a name="resource-scope"></a>Zakres zasobu 
Przed przypisaniem roli platformy Azure do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, w których można określić zakres dostępu do zasobów Service Bus, rozpoczynając od najwęższego zakresu:

- **Kolejka**, **temat**lub **subskrypcja**: przypisanie roli dotyczy konkretnej jednostki Service Bus. Obecnie Azure Portal nie obsługuje przypisywania użytkowników/grup/tożsamości zarządzanych do Service Bus ról platformy Azure na poziomie subskrypcji. Oto przykład użycia interfejsu wiersza polecenia platformy Azure: [AZ-role-Assign-Create](/cli/azure/role/assignment?#az-role-assignment-create) w celu przypisania tożsamości do Service Bus roli platformy Azure: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Przestrzeń nazw Service Bus**: przypisanie roli obejmuje całą topologię Service Bus w przestrzeni nazw oraz do skojarzonej z nią grupy odbiorców.
- **Grupa zasobów**: przypisanie roli dotyczy wszystkich zasobów Service Bus w grupie zasobów.
- **Subskrypcja**: przypisanie roli dotyczy wszystkich zasobów Service Bus we wszystkich grupach zasobów w subskrypcji.

> [!NOTE]
> Należy pamiętać, że propagacja ról platformy Azure może potrwać do 5 minut. 

Aby uzyskać więcej informacji na temat sposobu definiowania wbudowanych ról, zobacz [Omówienie definicji ról](../role-based-access-control/role-definitions.md#management-and-data-operations). Aby uzyskać informacje na temat tworzenia ról niestandardowych platformy Azure, zobacz [role niestandardowe platformy Azure](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie wirtualnej
Aby można było używać zarządzanych tożsamości dla zasobów platformy Azure w celu autoryzowania Service Bus zasobów z maszyny wirtualnej, należy najpierw włączyć zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej. Aby dowiedzieć się, jak włączyć zarządzane tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager biblioteki klienckie](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Przyznawanie uprawnień do zarządzanej tożsamości w usłudze Azure AD
Aby autoryzować żądanie do usługi Service Bus z poziomu zarządzanej tożsamości w aplikacji, najpierw Skonfiguruj ustawienia kontroli dostępu opartej na rolach (Azure RBAC) dla tej tożsamości zarządzanej. Azure Service Bus definiuje role platformy Azure, które obejmują uprawnienia do wysyłania i odczytywania z Service Bus. Po przypisaniu roli platformy Azure do zarządzanej tożsamości zarządzana tożsamość otrzymuje dostęp do Service Bus jednostek w odpowiednim zakresie.

Aby uzyskać więcej informacji na temat przypisywania ról platformy Azure, zobacz [uwierzytelnianie i autoryzacja przy użyciu Azure Active Directory w celu uzyskania dostępu do zasobów Service Bus](authenticate-application.md#azure-built-in-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Używanie Service Bus z tożsamościami zarządzanymi dla zasobów platformy Azure
Aby użyć Service Bus z tożsamościami zarządzanymi, należy przypisać tożsamość roli i odpowiedniego zakresu. Procedura opisana w tej sekcji używa prostej aplikacji, która działa w ramach zarządzanej tożsamości i uzyskuje dostęp do zasobów Service Bus.

Tutaj korzystamy z przykładowej aplikacji sieci Web hostowanej w [Azure App Service](https://azure.microsoft.com/services/app-service/). Aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji sieci Web, zobacz [Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure](../app-service/quickstart-dotnetcore.md)

Po utworzeniu aplikacji wykonaj następujące kroki: 

1. Przejdź do pozycji **Ustawienia** i wybierz pozycję **tożsamość**. 
1. Wybierz **stan** , który ma być **włączony**. 
1. Wybierz pozycję **Zapisz**, aby zapisać ustawienie. 

    ![Tożsamość zarządzana dla aplikacji sieci Web](./media/service-bus-managed-service-identity/identity-web-app.png)

Po włączeniu tego ustawienia zostanie utworzona nowa tożsamość usługi w Azure Active Directory (Azure AD) i skonfigurowana na hoście App Service.

> [!NOTE]
> W przypadku korzystania z tożsamości zarządzanej parametry połączenia powinny mieć format: `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=Managed Identity` .

Teraz Przypisz tę tożsamość usługi do roli w wymaganym zakresie w zasobach Service Bus.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Aby przypisać role platformy Azure przy użyciu Azure Portal
Aby przypisać rolę do przestrzeni nazw Service Bus, przejdź do przestrzeni nazw w Azure Portal. Wyświetl ustawienia Access Control (IAM) dla zasobu i postępuj zgodnie z tymi instrukcjami, aby zarządzać przypisaniami ról:

> [!NOTE]
> Poniższe kroki przypisują rolę tożsamości usługi do przestrzeni nazw Service Bus. Możesz wykonać te same czynności, aby przypisać rolę w innych obsługiwanych zakresach (grupy zasobów i subskrypcji). 
> 
> [Utwórz przestrzeń nazw Service Bus Messaging](service-bus-create-namespace-portal.md) , jeśli jej nie masz. 

1. W Azure Portal przejdź do przestrzeni nazw Service Bus i Wyświetl **Przegląd** dla przestrzeni nazw. 
1. Wybierz pozycję **Access Control (IAM)** w menu po lewej stronie, aby wyświetlić ustawienia kontroli dostępu dla przestrzeni nazw Service Bus.
1.  Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról.
3.  Wybierz pozycję **Dodaj** , aby dodać nową rolę.
4.  Na stronie **Dodawanie przypisania roli** wybierz role Azure Service Bus, które chcesz przypisać. Następnie wyszukaj w celu zlokalizowania tożsamości usługi, która została zarejestrowana w celu przypisania roli.
    
    ![Dodawanie strony przypisania roli](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Wybierz pozycję **Zapisz**. Tożsamość, do której przypisano rolę, jest wyświetlana na liście w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono tożsamość usługi ma Azure Service Bus właściciel danych.
    
    ![Tożsamość przypisana do roli](./media/service-bus-managed-service-identity/role-assigned.png)

Po przypisaniu roli aplikacja sieci Web będzie miała dostęp do Service Bus jednostek w ramach zdefiniowanego zakresu. 




### <a name="run-the-app"></a>Uruchamianie aplikacji
Teraz Zmodyfikuj domyślną stronę utworzonej aplikacji ASP.NET. Możesz użyć kodu aplikacji sieci Web z [tego repozytorium GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Domyślna strona. aspx to strona docelowa. Kod można znaleźć w pliku Default.aspx.cs. Wynikiem jest minimalna aplikacja sieci Web z kilkoma polami wprowadzania oraz za pomocą przycisków **Wyślij** i **Odbierz** łączących się z Service Bus wysyłania lub odbierania wiadomości.

Zauważ, jak obiekt [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) jest zainicjowany. Zamiast korzystać z dostawcy tokenów token dostępu współdzielonego (SAS), kod tworzy dostawcę tokenu dla tożsamości zarządzanej z `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` wywołaniem. W związku z tym nie ma żadnych wpisów tajnych do zachowania i używania. Przepływ tożsamości zarządzanej do Service Bus i uzgadnianie autoryzacji są automatycznie obsługiwane przez dostawcę tokenu. Jest to prostsze model niż używanie sygnatury dostępu współdzielonego.

Po wprowadzeniu tych zmian Opublikuj i uruchom aplikację. Poprawne publikowanie danych można łatwo uzyskać, pobierając i importując profil publikacji w programie Visual Studio:

![Pobierz profil publikowania](./media/service-bus-managed-service-identity/msi3.png)
 
Aby wysłać lub odebrać wiadomości, wprowadź nazwę przestrzeni nazw i nazwę utworzonej jednostki. Następnie kliknij opcję **Wyślij** lub **Odbierz**.


> [!NOTE]
> - Tożsamość zarządzana działa tylko w środowisku platformy Azure, w usługach App Services, maszynach wirtualnych platformy Azure i w zestawach skalowania. W przypadku aplikacji .NET Biblioteka Microsoft. Azure. Services. AppAuthentication, która jest używana przez pakiet NuGet Service Bus, zapewnia streszczenie tego protokołu i obsługuje lokalne środowisko programistyczne. Ta biblioteka umożliwia również testowanie kodu lokalnie na komputerze deweloperskim przy użyciu konta użytkownika z programu Visual Studio, interfejsu wiersza polecenia platformy Azure 2,0 lub Active Directory zintegrowanego uwierzytelniania. Aby uzyskać więcej informacji na temat lokalnych opcji tworzenia w tej bibliotece, zobacz [uwierzytelnianie między usługą w celu Azure Key Vault przy użyciu platformy .NET](../key-vault/general/service-to-service-authentication.md).  
> 
> - Obecnie zarządzane tożsamości nie działają w przypadku App Service miejsc wdrożenia.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)