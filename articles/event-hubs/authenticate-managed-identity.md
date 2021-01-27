---
title: Uwierzytelnianie tożsamości zarządzanej przy użyciu Azure Active Directory
description: Ten artykuł zawiera informacje dotyczące uwierzytelniania tożsamości zarządzanej przy użyciu Azure Active Directory dostępu do zasobów Event Hubs platformy Azure
ms.topic: conceptual
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2070cfd94b39a08afb86ffd3579f1116faac72d5
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805295"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs
Usługa Azure Event Hubs obsługuje uwierzytelnianie Azure Active Directory (Azure AD) z [tożsamościami zarządzanymi dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md). Zarządzane tożsamości dla zasobów platformy Azure mogą autoryzować dostęp do zasobów Event Hubs przy użyciu poświadczeń usługi Azure AD z aplikacji uruchomionych na platformie Azure Virtual Machines (maszyny wirtualne), aplikacji funkcji, Virtual Machine Scale Sets i innych usług. Korzystając z tożsamości zarządzanych dla zasobów platformy Azure wraz z uwierzytelnianiem w usłudze Azure AD, można uniknąć zapisywania poświadczeń z aplikacjami uruchomionymi w chmurze.

W tym artykule pokazano, jak autoryzować dostęp do centrum zdarzeń przy użyciu tożsamości zarządzanej z maszyny wirtualnej platformy Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie wirtualnej
Aby można było używać zarządzanych tożsamości dla zasobów platformy Azure w celu autoryzowania Event Hubs zasobów z maszyny wirtualnej, należy najpierw włączyć zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej. Aby dowiedzieć się, jak włączyć zarządzane tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager biblioteki klienckie](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Przyznawanie uprawnień do zarządzanej tożsamości w usłudze Azure AD
Aby autoryzować żądanie do Event Hubs usługi z tożsamości zarządzanej w aplikacji, najpierw Skonfiguruj ustawienia kontroli dostępu opartej na rolach (Azure RBAC) dla tej tożsamości zarządzanej. Usługa Azure Event Hubs definiuje role platformy Azure, które obejmują uprawnienia do wysyłania i odczytywania z Event Hubs. Gdy rola platformy Azure zostanie przypisana do zarządzanej tożsamości, zarządzana tożsamość otrzymuje dostęp do Event Hubs danych w odpowiednim zakresie.

Aby uzyskać więcej informacji na temat przypisywania ról platformy Azure, zobacz [uwierzytelnianie za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Korzystanie z usługi Event Hubs przy użyciu tożsamości zarządzanych
Aby użyć Event Hubs z tożsamościami zarządzanymi, należy przypisać rolę i odpowiedni zakres do tożsamości. Procedura opisana w tej sekcji używa prostej aplikacji, która działa w ramach zarządzanej tożsamości i uzyskuje dostęp do zasobów Event Hubs.

Tutaj korzystamy z przykładowej aplikacji sieci Web hostowanej w [Azure App Service](https://azure.microsoft.com/services/app-service/). Aby uzyskać instrukcje krok po kroku dotyczące tworzenia aplikacji sieci Web, zobacz [Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure](../app-service/quickstart-dotnetcore.md)

Po utworzeniu aplikacji wykonaj następujące kroki: 

1. Przejdź do pozycji **Ustawienia** i wybierz pozycję **tożsamość**. 
1. Wybierz **stan** , który ma być **włączony**. 
1. Wybierz pozycję **Zapisz**, aby zapisać ustawienie. 

    :::image type="content" source="./media/authenticate-managed-identity/identity-web-app.png" alt-text="Tożsamość zarządzana dla aplikacji sieci Web":::
4. W komunikacie z informacjami wybierz pozycję **tak** . 

    Po włączeniu tego ustawienia zostanie utworzona nowa tożsamość usługi w Azure Active Directory (Azure AD) i skonfigurowana na hoście App Service.

    Teraz Przypisz tę tożsamość usługi do roli w wymaganym zakresie w zasobach Event Hubs.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Aby przypisać role platformy Azure przy użyciu Azure Portal
Aby przypisać rolę do Event Hubs zasobów, przejdź do tego zasobu w Azure Portal. Wyświetl ustawienia Access Control (IAM) dla zasobu i postępuj zgodnie z tymi instrukcjami, aby zarządzać przypisaniami ról:

> [!NOTE]
> Poniższe kroki przypisują rolę tożsamości usługi do przestrzeni nazw Event Hubs. Możesz wykonać te same czynności, aby przypisać rolę zakresu do dowolnego Event Hubs zasobów. 

1. W Azure Portal przejdź do przestrzeni nazw Event Hubs i Wyświetl **Przegląd** dla przestrzeni nazw. 
1. Wybierz pozycję **Access Control (IAM)** w menu po lewej stronie, aby wyświetlić ustawienia kontroli dostępu dla centrum zdarzeń.
1.  Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról.
3.  Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Dodaj przypisanie roli** _.
4.  Na stronie _ *Dodaj przypisanie roli** wykonaj następujące czynności:
    1. W polu **rola** wybierz rolę Event Hubs, którą chcesz przypisać. W tym przykładzie jest to **właściciel danych Event Hubs platformy Azure**.
    1. W polu **Przypisz dostęp do** wybierz pozycję **App Service** w obszarze **przypisana przez system tożsamość zarządzana**. 
    1. Wybierz **subskrypcję** , w ramach której utworzono zarządzaną tożsamość aplikacji sieci Web.
    1. Wybierz **zarządzaną tożsamość** utworzonej aplikacji sieci Web. Nazwa domyślna tożsamości jest taka sama jak nazwa aplikacji sieci Web. 
    1. Następnie wybierz pozycję **Zapisz**. 
    
        ![Dodawanie strony przypisania roli](./media/authenticate-managed-identity/add-role-assignment-page.png)

    Po przypisaniu roli aplikacja sieci Web będzie miała dostęp do Event Hubs zasobów w ramach zdefiniowanego zakresu. 

    > [!NOTE]
    > Aby uzyskać listę usług, które obsługują tożsamości zarządzane, zobacz [usługi obsługujące zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="test-the-web-application"></a>Testowanie aplikacji internetowej
1. Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń. 
2. Wdróż aplikację sieci Web na platformie Azure. Zobacz następującą sekcję z kartami dotyczącą linków do aplikacji sieci Web w witrynie GitHub. 
3. Upewnij się, że SendReceive. aspx jest ustawiony jako dokument domyślny dla aplikacji sieci Web. 
3. Włącz **tożsamość** aplikacji sieci Web. 
4. Przypisz tę tożsamość do roli **właściciel danych Event Hubs** na poziomie przestrzeni nazw lub na poziomie centrum zdarzeń. 
5. Uruchom aplikację sieci Web, wprowadź nazwę przestrzeni nazw i nazwę centrum zdarzeń, komunikat, a następnie wybierz pozycję **Wyślij**. Aby odebrać zdarzenie, wybierz pozycję **Receive (odbieranie**). 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure. Messaging. EventHubs (Najnowsza wersja)](#tab/latest)
Teraz możesz uruchomić aplikację sieci Web i wskazać przeglądarkę na przykładową stronę aspx. Możesz znaleźć przykładową aplikację sieci Web, która wysyła i odbiera dane z Event Hubs zasobów w [repozytorium GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Zainstaluj najnowszy pakiet z programu [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)i Rozpocznij wysyłanie zdarzeń do Event Hubs przy użyciu **EventHubProducerClient** i otrzymywanie zdarzeń przy użyciu **EventHubConsumerClient**. 

> [!NOTE]
> Aby zapoznać się z przykładem Java korzystającym z tożsamości zarządzanej do publikowania zdarzeń w centrum zdarzeń, zobacz [Publikowanie zdarzeń za pomocą usługi Azure Identity Sample w witrynie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

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
Teraz możesz uruchomić aplikację sieci Web i wskazać przeglądarkę na przykładową stronę aspx. Możesz znaleźć przykładową aplikację sieci Web, która wysyła i odbiera dane z Event Hubs zasobów w [repozytorium GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Zainstaluj najnowszy pakiet z programu [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)i zacznij wysyłać do i odbierać dane z centrów zdarzeń przy użyciu EventHubClient, jak pokazano w poniższym kodzie: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Event Hubs dla platformy Kafka
Za pomocą aplikacji Apache Kafka można wysyłać wiadomości i odbierać komunikaty z platformy Azure Event Hubs przy użyciu tożsamości zarządzanej OAuth. Zapoznaj się z poniższym przykładem w witrynie GitHub: [Event Hubs for Kafka — wysyłanie i odbieranie komunikatów przy użyciu tożsamości zarządzanej OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Samples
- Przykłady dla **platformy Azure. Messaging. EventHubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Przykłady Microsoft. Azure. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Te przykłady używają starej biblioteki **Microsoft. Azure. EventHubs** , ale można ją łatwo zaktualizować do korzystania z najnowszej biblioteki **Azure. Messaging. EventHubs** . Aby przenieść przykład z używania starej biblioteki do nowej, zapoznaj się z [przewodnikiem migrowania z Microsoft. Azure. EventHubs do platformy Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Ten przykład został zaktualizowany, aby można było użyć najnowszej biblioteki **Azure. Messaging. EventHubs** .
- [Event Hubs Kafka — wysyłanie i odbieranie komunikatów przy użyciu tożsamości zarządzanej OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z następującym artykułem, aby dowiedzieć się więcej o zarządzanych tożsamościach dla zasobów platformy Azure: [jakie są zarządzane tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Zapoznaj się z następującymi artykułami:
    - [Uwierzytelniaj żądania do Event Hubs platformy Azure z aplikacji przy użyciu Azure Active Directory](authenticate-application.md)
    - [Uwierzytelnianie żądań na platformie Azure Event Hubs przy użyciu sygnatur dostępu współdzielonego](authenticate-shared-access-signature.md)
    - [Autoryzuj dostęp do zasobów Event Hubs przy użyciu Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autoryzuj dostęp do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego](authorize-access-shared-access-signature.md)
