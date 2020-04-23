---
title: Uwierzytelnianie tożsamości zarządzanej za pomocą usługi Azure Active Directory
description: Ten artykuł zawiera informacje dotyczące uwierzytelniania tożsamości zarządzanej za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Azure Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: dfc60fbc03021e72dccc0f60a7ac34d204ef6df9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025190"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Event Hubs
Usługa Azure Event Hubs obsługuje uwierzytelnianie usługi Azure Active Directory (Azure AD) z [zarządzanymi tożsamościami zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md) Tożsamości zarządzane dla zasobów platformy Azure mogą autoryzować dostęp do zasobów usługi Event Hubs przy użyciu poświadczeń usługi Azure AD z aplikacji uruchomionych w maszynach wirtualnych platformy Azure (maszyny wirtualne), aplikacje funkcji, zestawy skalowania maszyny wirtualnej i inne usługi. Korzystając z zarządzanych tożsamości dla zasobów platformy Azure wraz z uwierzytelnianiem usługi Azure AD, można uniknąć przechowywania poświadczeń w aplikacjach uruchamianych w chmurze.

W tym artykule pokazano, jak autoryzować dostęp do Centrum zdarzeń przy użyciu tożsamości zarządzanej z maszyny Wirtualnej platformy Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie Wirtualnej
Aby można było używać tożsamości zarządzanych dla zasobów platformy Azure do autoryzowania zasobów usługi Event Hubs z maszyny Wirtualnej, należy najpierw włączyć tożsamości zarządzane dla zasobów platformy Azure na maszynie Wirtualnej. Aby dowiedzieć się, jak włączyć tożsamości zarządzane dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Biblioteki klientów usługi Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Udziel uprawnień do tożsamości zarządzanej w usłudze Azure AD
Aby autoryzować żądanie usługi Event Hubs z tożsamości zarządzanej w aplikacji, należy najpierw skonfigurować ustawienia kontroli dostępu opartej na rolach (RBAC) dla tej tożsamości zarządzanej. Usługa Azure Event Hubs definiuje role RBAC, które obejmują uprawnienia do wysyłania i odczytywania z centrów zdarzeń. Gdy rola RBAC jest przypisana do tożsamości zarządzanej, tożsamość zarządzana otrzymuje dostęp do danych Centrum zdarzeń w odpowiednim zakresie.

Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [Uwierzytelnianie przy użyciu usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Event Hubs](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Korzystanie z usługi Event Hubs przy użyciu tożsamości zarządzanych
Aby używać centrum zdarzeń z tożsamościami zarządzanymi, należy przypisać tożsamość roli i odpowiedniego zakresu. Procedura w tej sekcji używa prostej aplikacji, która działa w ramach tożsamości zarządzanej i uzyskuje dostęp do zasobów Usługi Event Hubs.

W tym miejscu używamy przykładowej aplikacji sieci web hostowanej w [usłudze Azure App Service.](https://azure.microsoft.com/services/app-service/) Aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji sieci web, zobacz [Tworzenie ASP.NET podstawowej aplikacji sieci Web](../app-service/app-service-web-get-started-dotnet.md) na platformie Azure

Po utworzeniu aplikacji wykonaj następujące kroki: 

1. Przejdź do **ustawień** i wybierz **pozycję Tożsamość**. 
1. Wybierz **stan,** który ma być **włączony**. 
1. Wybierz pozycję **Zapisz**, aby zapisać ustawienie. 

    ![Tożsamość zarządzana dla aplikacji sieci web](./media/authenticate-managed-identity/identity-web-app.png)

Po włączeniu tego ustawienia w usłudze Azure Active Directory (Azure AD) zostanie utworzona nowa tożsamość usługi i skonfigurowana do hosta usługi App Service.

Teraz przypisz tę tożsamość usługi do roli w wymaganym zakresie w zasobach usługi Event Hubs.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Aby przypisać role RBAC za pomocą witryny Azure portal
Aby przypisać rolę do zasobów usługi Event Hubs, przejdź do tego zasobu w witrynie Azure Portal. Wyświetl ustawienia kontroli dostępu (IAM) zasobu i postępuj zgodnie z poniższymi instrukcjami, aby zarządzać przypisaniami ról:

> [!NOTE]
> Poniższe kroki przypisuje rolę tożsamości usługi do obszarów nazw Centrum zdarzeń. Można wykonać te same kroki, aby przypisać rolę o zakresie do dowolnego zasobu Centrum zdarzeń. 

1. W witrynie Azure portal przejdź do obszaru nazw centrum zdarzeń i wyświetl **omówienie** obszaru nazw. 
1. Wybierz **pozycję Kontrola dostępu (IAM)** w lewym menu, aby wyświetlić ustawienia kontroli dostępu dla centrum zdarzeń.
1.  Wybierz kartę **Przypisania ról,** aby wyświetlić listę przypisań ról.
3.  Wybierz **pozycję Dodaj,** aby dodać nową rolę.
4.  Na stronie **Dodawanie przypisania roli** wybierz role centrum zdarzeń, które chcesz przypisać. Następnie wyszukaj, aby zlokalizować zarejestrowaną tożsamość usługi, aby przypisać rolę.
    
    ![Dodawanie strony przypisania roli](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Wybierz pozycję **Zapisz**. Tożsamość, do której przypisano rolę, jest wyświetlana na liście w ramach tej roli. Na przykład na poniższej ilustracji pokazano, że tożsamość usługi ma właściciela danych usługi Event Hubs.
    
    ![Tożsamość przypisana do roli](./media/authenticate-managed-identity/role-assigned.png)

Po przypisaniu roli aplikacja sieci web będzie miała dostęp do zasobów centrum zdarzeń w zdefiniowanym zakresie. 

### <a name="test-the-web-application"></a>Testowanie aplikacji internetowej
1. Utwórz obszar nazw centrów zdarzeń i centrum zdarzeń. 
2. Wdrażanie aplikacji sieci web na platformie Azure. Zobacz poniższą sekcję z kartami, aby uzyskać łącza do aplikacji sieci web w usłudze GitHub. 
3. Upewnij się, że SendReceive.aspx jest ustawiony jako domyślny dokument dla aplikacji sieci web. 
3. Włącz **tożsamość** aplikacji sieci web. 
4. Przypisz tę tożsamość do roli **Właściciel danych usługi Event Hubs** na poziomie obszaru nazw lub centrum zdarzeń. 
5. Uruchom aplikację sieci Web, wprowadź nazwę obszaru nazw i nazwę centrum zdarzeń, komunikat i wybierz pozycję **Wyślij**. Aby odebrać zdarzenie, wybierz **opcję Odbierz**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Usługa Azure.Messaging.EventHubs (najnowsza)](#tab/latest)
Teraz możesz uruchomić aplikację internetową i skierować przeglądarkę na przykładową stronę aspx. Przykładową aplikację sieci web, która wysyła i odbiera dane z zasobów Usługi Event Hubs, można znaleźć w [repozytorium GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Zainstaluj najnowszy pakiet z [programu NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)i rozpocznij wysyłanie zdarzeń do centrów zdarzeń przy użyciu **funkcji EventHubProducerClient** i odbieranie zdarzeń przy użyciu **aplikacji EventHubConsumerClient**. 

> [!NOTE]
> W przypadku przykładu Java, który używa tożsamości zarządzanej do publikowania zdarzeń w Centrum zdarzeń, zobacz [Publikowanie zdarzeń przy użyciu przykładu tożsamości platformy Azure w usłudze GitHub.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (starsza wersja)](#tab/old)
Teraz możesz uruchomić aplikację internetową i skierować przeglądarkę na przykładową stronę aspx. Przykładową aplikację sieci web, która wysyła i odbiera dane z zasobów Usługi Event Hubs, można znaleźć w [repozytorium GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Zainstaluj najnowszy pakiet z [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)i rozpocznij wysyłanie i odbieranie danych z centrum zdarzeń przy użyciu EventHubClient, jak pokazano w poniższym kodzie: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Event Hubs dla platformy Kafka
Aplikacje Apache Kafka służy do wysyłania wiadomości i odbierania wiadomości z usługi Azure Event Hubs przy użyciu identyfikatora OAuth tożsamości zarządzanej. Zobacz poniższy przykład w usłudze GitHub: [Event Hubs for Kafka — wysyłanie i odbieranie wiadomości przy użyciu identyfikatora zarządzanego OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Samples
- **Przykłady witryn Azure.Messaging.EventHubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Przykłady witryn Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Te przykłady używają starej biblioteki **Microsoft.Azure.EventHubs,** ale można łatwo zaktualizować go do korzystania z najnowszej biblioteki **Azure.Messaging.EventHubs.** Aby przenieść przykład ze starej biblioteki do nowej, zobacz [Przewodnik do migracji z witryny Microsoft.Azure.EventHubs do usługi Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Ten przykład został zaktualizowany, aby użyć najnowszej biblioteki **Azure.Messaging.EventHubs.**
- [Centra zdarzeń dla platformy Kafka — wysyłanie i odbieranie wiadomości przy użyciu identyfikatora zarządzanego OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Następne kroki
- Zobacz następujący artykuł, aby dowiedzieć się więcej o tożsamościach zarządzanych dla zasobów platformy Azure: [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Zobacz następujące artykuły pokrewne:
    - [Uwierzytelnij żądania do usługi Azure Event Hubs z aplikacji przy użyciu usługi Azure Active Directory](authenticate-application.md)
    - [Uwierzytelnij żądania w centrach zdarzeń platformy Azure przy użyciu podpisów dostępu współdzielonego](authenticate-shared-access-signature.md)
    - [Autoryzowanie dostępu do zasobów usługi Event Hubs przy użyciu usługi Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autoryzowanie dostępu do zasobów Centrum zdarzeń przy użyciu podpisów dostępu współdzielonego](authorize-access-shared-access-signature.md)