---
title: Tożsamości zarządzane dla zasobów platformy Azure za pomocą usługi Service Bus
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uzyskiwania dostępu za pomocą encji usługi Azure Service Bus (kolejki, tematy i subskrypcje).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 46a1db94d576174b837a40c646fcf9e082e339c8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461620"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Azure Service Bus
[Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) to funkcja między platformami Azure, która umożliwia utworzenie bezpiecznej tożsamości skojarzonej z wdrożeniem, w ramach którego uruchamia się kod aplikacji. Następnie można skojarzyć tę tożsamość z rolami kontroli dostępu, które udzielają uprawnień niestandardowych dostępu do określonych zasobów platformy Azure, których potrzebuje aplikacja.

Za pomocą tożsamości zarządzanych platforma Azure zarządza tą tożsamością środowiska uruchomieniowego. Nie trzeba przechowywać i chronić klucze dostępu w kodzie aplikacji lub konfiguracji, dla samej tożsamości lub zasobów, które są potrzebne do uzyskania dostępu. Aplikacja kliencka usługi Service Bus działająca wewnątrz aplikacji usługi Azure App Service lub na maszynie wirtualnej z włączonymi jednostkami zarządzanymi dla obsługi zasobów platformy Azure nie musi obsługiwać reguł i kluczy sygnatury dostępu Współdzielonego ani żadnych innych tokenów dostępu. Aplikacja kliencka potrzebuje tylko adresu punktu końcowego obszaru nazw usługi Service Bus. Gdy aplikacja łączy, usługa Service Bus wiąże kontekst zarządzanej jednostki z klientem w operacji, która jest pokazana w przykładzie w dalszej części tego artykułu. Po skojarzeniu z tożsamością zarządzaną klient usługi Service Bus może wykonać wszystkie autoryzowane operacje. Autoryzacja jest udzielana przez skojarzenie jednostki zarządzanej z rolami usługi Service Bus. 

## <a name="overview"></a>Omówienie
Gdy podmiot zabezpieczeń (użytkownik, grupa lub aplikacja) próbuje uzyskać dostęp do jednostki usługi Service Bus, żądanie musi być autoryzowane. Dzięki usłudze Azure AD dostęp do zasobu jest procesem dwuetapowym. 

 1. Po pierwsze tożsamość podmiotu zabezpieczeń jest uwierzytelniona i zwracany jest token OAuth 2.0. Nazwa zasobu, aby `https://servicebus.azure.net`zażądać tokenu jest .
 1. Następnie token jest przekazywany jako część żądania do usługi Service Bus, aby autoryzować dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawiera token dostępu OAuth 2.0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w jednostce platformy Azure, takiej jak maszyna wirtualna Azure, zestaw skalowania maszyny wirtualnej lub aplikacja funkcji platformy Azure, może użyć tożsamości zarządzanej, aby uzyskać dostęp do zasobów. 

Krok autoryzacji wymaga przypisania co najmniej jednej roli RBAC do podmiotu zabezpieczeń. Usługa Azure Service Bus udostępnia role RBAC, które obejmują zestawy uprawnień dla zasobów usługi Service Bus. Role, które są przypisane do podmiotu zabezpieczeń określić uprawnienia, które podmiot zabezpieczeń będzie mieć. Aby dowiedzieć się więcej na temat przypisywania ról RBAC do usługi Azure Service Bus, zobacz [Wbudowane role RBAC dla usługi Azure Service Bus](#built-in-rbac-roles-for-azure-service-bus). 

Aplikacje natywne i aplikacje sieci web, które żądają usługi Service Bus, mogą również autoryzować za pomocą usługi Azure AD. W tym artykule pokazano, jak zażądać tokenu dostępu i używać go do autoryzowania żądań dotyczących zasobów usługi Service Bus. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC dla praw dostępu
Usługa Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC).](../role-based-access-control/overview.md) Usługa Azure Service Bus definiuje zestaw wbudowanych ról RBAC, które obejmują typowe zestawy uprawnień używanych do uzyskiwania dostępu do jednostek usługi Service Bus i można również zdefiniować niestandardowe role dostępu do danych.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure udziela dostępu do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp może być ograniczony do poziomu subskrypcji, grupy zasobów lub obszaru nazw usługi Service Bus. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, jednostką usługi aplikacji lub tożsamością zarządzaną dla zasobów platformy Azure.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Wbudowane role RBAC dla usługi Azure Service Bus
W przypadku usługi Azure Service Bus zarządzanie obszarami nazw i wszystkimi powiązanymi zasobami za pośrednictwem witryny Azure portal i interfejsu API zarządzania zasobami platformy Azure jest już chronione przy użyciu modelu *kontroli dostępu opartego na rolach* (RBAC). Platforma Azure udostępnia poniższe wbudowane role RBAC do autoryzowania dostępu do obszaru nazw usługi Service Bus:

- [Właściciel danych usługi Azure Service Bus:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)umożliwia dostęp do danych do obszaru nazw usługi Service Bus i jej jednostek (kolejki, tematy, subskrypcje i filtry)
- [Nadawca danych usługi Azure Service Bus:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)użyj tej roli, aby dać dostęp do usługi Service Bus i jej jednostek.
- [Odbiorca danych usługi Azure Service Bus:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)Użyj tej roli, aby dać dostęp do obszaru nazw usługi Service Bus i jego jednostek. 

## <a name="resource-scope"></a>Zakres zasobu 
Przed przypisaniem roli RBAC do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że zawsze najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, na których można mieć dostęp do zasobów usługi Service Bus, zaczynając od najwęższego zakresu:

- **Kolejka,** **temat**lub **subskrypcja**: Przypisanie roli ma zastosowanie do określonej encji usługi Service Bus. Obecnie portal Azure portal nie obsługuje przypisywania użytkowników/grup/tożsamości zarządzanych do ról RBAC usługi Service Bus na poziomie subskrypcji. Oto przykład użycia polecenia interfejsu wiersza polecenia platformy Azure: [az-role-assignment-create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) w celu przypisania tożsamości do roli RBAC usługi Service Bus: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Obszar nazw usługi Service Bus:** Przypisanie roli obejmuje całą topologię usługi Service Bus w obszarze nazw i do skojarzonej z nią grupy odbiorców.
- **Grupa zasobów:** Przypisanie roli ma zastosowanie do wszystkich zasobów usługi Service Bus w grupie zasobów.
- **Subskrypcja:** Przypisanie roli ma zastosowanie do wszystkich zasobów usługi Service Bus we wszystkich grupach zasobów w ramach subskrypcji.

> [!NOTE]
> Należy pamiętać, że przydziały ról RBAC może potrwać do pięciu minut do propagacji. 

Aby uzyskać więcej informacji na temat definiowania ról wbudowanych, zobacz [Rozumienie definicji ról](../role-based-access-control/role-definitions.md#management-and-data-operations). Aby uzyskać informacje dotyczące tworzenia niestandardowych ról RBAC, zobacz [Tworzenie ról niestandardowych dla kontroli dostępu opartej na rolach platformy Azure](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie Wirtualnej
Aby można było używać tożsamości zarządzanych dla zasobów platformy Azure do autoryzowania zasobów usługi Service Bus z maszyny Wirtualnej, należy najpierw włączyć tożsamości zarządzane dla zasobów platformy Azure na maszynie Wirtualnej. Aby dowiedzieć się, jak włączyć tożsamości zarządzane dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Biblioteki klientów usługi Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Udziel uprawnień do tożsamości zarządzanej w usłudze Azure AD
Aby autoryzować żądanie do usługi Service Bus z tożsamości zarządzanej w aplikacji, należy najpierw skonfigurować ustawienia kontroli dostępu opartej na rolach (RBAC) dla tej tożsamości zarządzanej. Usługa Azure Service Bus definiuje role RBAC, które obejmują uprawnienia do wysyłania i odczytywania z usługi Service Bus. Gdy rola RBAC jest przypisana do tożsamości zarządzanej, tożsamość zarządzana otrzymuje dostęp do jednostek usługi Service Bus w odpowiednim zakresie.

Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [Uwierzytelnianie i autoryzowanie za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Service Bus](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Używanie usługi Service Bus z tożsamościami zarządzanymi dla zasobów platformy Azure
Aby użyć usługi Service Bus z tożsamościami zarządzanymi, należy przypisać tożsamość roli i odpowiedniego zakresu. Procedura w tej sekcji używa prostej aplikacji, która działa w ramach tożsamości zarządzanej i uzyskuje dostęp do zasobów usługi Service Bus.

W tym miejscu używamy przykładowej aplikacji sieci web hostowanej w [usłudze Azure App Service.](https://azure.microsoft.com/services/app-service/) Aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji sieci web, zobacz [Tworzenie ASP.NET podstawowej aplikacji sieci Web](../app-service/app-service-web-get-started-dotnet.md) na platformie Azure

Po utworzeniu aplikacji wykonaj następujące kroki: 

1. Przejdź do **ustawień** i wybierz **pozycję Tożsamość**. 
1. Wybierz **stan,** który ma być **włączony**. 
1. Wybierz pozycję **Zapisz**, aby zapisać ustawienie. 

    ![Tożsamość zarządzana dla aplikacji sieci web](./media/service-bus-managed-service-identity/identity-web-app.png)

Po włączeniu tego ustawienia w usłudze Azure Active Directory (Azure AD) zostanie utworzona nowa tożsamość usługi i skonfigurowana do hosta usługi App Service.

Teraz przypisz tę tożsamość usługi do roli w wymaganym zakresie w zasobach usługi Service Bus.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Aby przypisać role RBAC za pomocą witryny Azure portal
Aby przypisać rolę do obszaru nazw usługi Service Bus, przejdź do obszaru nazw w witrynie Azure portal. Wyświetl ustawienia kontroli dostępu (IAM) zasobu i postępuj zgodnie z poniższymi instrukcjami, aby zarządzać przypisaniami ról:

> [!NOTE]
> Poniższe kroki przypisuje rolę tożsamości usługi do obszarów nazw usługi Service Bus. Można wykonać te same kroki, aby przypisać rolę w innych obsługiwanych zakresach (grupa zasobów i subskrypcja). 
> 
> [Utwórz obszar nazw usługi Service Bus Messaging,](service-bus-create-namespace-portal.md) jeśli jej nie masz. 

1. W witrynie Azure portal przejdź do obszaru nazw usługi Service Bus i wyświetl **omówienie** obszaru nazw. 
1. Wybierz **pozycję Kontrola dostępu (IAM)** w lewym menu, aby wyświetlić ustawienia kontroli dostępu dla obszaru nazw usługi Service Bus.
1.  Wybierz kartę **Przypisania ról,** aby wyświetlić listę przypisań ról.
3.  Wybierz **pozycję Dodaj,** aby dodać nową rolę.
4.  Na stronie **Dodawanie przypisania roli** wybierz role usługi Azure Service Bus, które chcesz przypisać. Następnie wyszukaj, aby zlokalizować zarejestrowaną tożsamość usługi, aby przypisać rolę.
    
    ![Dodawanie strony przypisania roli](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Wybierz pozycję **Zapisz**. Tożsamość, do której przypisano rolę, jest wyświetlana na liście w ramach tej roli. Na przykład na poniższej ilustracji pokazano, że tożsamość usługi ma właściciela usługi Azure Service Bus Data.
    
    ![Tożsamość przypisana do roli](./media/service-bus-managed-service-identity/role-assigned.png)

Po przypisaniu roli aplikacja sieci web będzie miała dostęp do jednostek usługi Service Bus w zdefiniowanym zakresie. 

### <a name="run-the-app"></a>Uruchomienie aplikacji

Teraz zmodyfikuj domyślną stronę utworzonej aplikacji ASP.NET. Możesz użyć kodu aplikacji internetowej z [tego repozytorium GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Strona Default.aspx to strona docelowa. Kod można znaleźć w pliku Default.aspx.cs. Wynikiem jest minimalna aplikacja sieci web z kilkoma polami wprowadzania i z przyciskami **wysyłania** i **odbierania,** które łączą się z usługą Service Bus w celu wysyłania lub odbierania wiadomości.

Należy zauważyć, jak [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obiekt jest inicjowany. Zamiast używać dostawcy tokenu dostępu współdzielonego (SAS) kod tworzy dostawcę `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` tokenu dla tożsamości zarządzanej za pomocą wywołania. W związku z tym nie ma żadnych wpisów tajnych do zachowania i używania. Przepływ kontekstu tożsamości zarządzanej do usługi Service Bus i uzgadniania autoryzacji są automatycznie obsługiwane przez dostawcę tokenu. Jest to model prostszy niż przy użyciu sygnatury dostępu Współdzielonego.

Po dokonaniu tych zmian, opublikować i uruchomić aplikację. Poprawne dane publikowania można łatwo uzyskać, pobierając, a następnie importując profil publikowania w programie Visual Studio:

![Pobierz profil publikowania](./media/service-bus-managed-service-identity/msi3.png)
 
Aby wysyłać lub odbierać wiadomości, wprowadź nazwę obszaru nazw i nazwę utworzonej encji. Następnie kliknij przycisk **Wyślij** lub **odbierz**.


> [!NOTE]
> - Tożsamość zarządzana działa tylko w środowisku platformy Azure, w usługach aplikacji, maszynach wirtualnych platformy Azure i zestawach skalowania. W przypadku aplikacji platformy .NET biblioteka microsoft.Azure.Services.AppAuthentication, która jest używana przez pakiet NuGet usługi Service Bus, zapewnia abstrakcję za pomocą tego protokołu i obsługuje środowisko rozwoju lokalnego. Ta biblioteka umożliwia również testowanie kodu lokalnie na komputerze deweloperskim przy użyciu konta użytkownika z programu Visual Studio, interfejsu wiersza polecenia 2.0 platformy Azure lub zintegrowanego uwierzytelniania usługi Active Directory. Aby uzyskać więcej informacji na temat lokalnych opcji rozwoju za pomocą tej biblioteki, zobacz [Uwierzytelnianie usługi do usługi w usłudze Azure Key Vault przy użyciu platformy .NET](../key-vault/general/service-to-service-authentication.md).  
> 
> - Obecnie tożsamości zarządzane nie działają z gniazdami wdrażania usługi App Service.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
