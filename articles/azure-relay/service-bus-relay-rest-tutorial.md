---
title: 'Samouczek: samouczek REST przy użyciu Azure Relay'
description: 'Samouczek: Tworzenie aplikacji hosta Azure Relay, która uwidacznia Interfejs REST.'
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 0620f55650d0e4da0cd7a616649df952f3017455
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88922331"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Samouczek: samouczek REST platformy Azure WCF Relay

W tym samouczku opisano sposób tworzenia aplikacji hosta Azure Relay, która uwidacznia Interfejs REST. Interfejs REST umożliwia klientowi sieci Web, takiemu jak przeglądarka sieci Web, uzyskiwanie dostępu do interfejsów API usługi Service Bus za pomocą żądań HTTP.

W tym samouczku jest używany model programowania REST programu Windows Communication Foundation (WCF) do konstruowania usługi REST na Azure Relay. Aby uzyskać więcej informacji, zobacz [model programowania REST WCF](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) i [projektowanie i implementowanie usług](/dotnet/framework/wcf/designing-and-implementing-services).

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
>
> * Zainstaluj wymagania wstępne dla tego samouczka.
> * Utwórz przestrzeń nazw przekaźnika.
> * Zdefiniuj kontrakt usługi WCF oparty na interfejsie REST.
> * Zaimplementuj kontrakt usługi WCF oparty na protokole REST.
> * Hostowanie i uruchamianie usługi WCF opartej na protokole REST.
> * Uruchom i przetestuj usługę.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .
* [Program Visual Studio 2015 lub nowszy](https://www.visualstudio.com). W przykładach w tym samouczku użyto programu Visual Studio 2019.
* Zestaw Azure SDK dla platformy .NET. Zainstaluj go ze [strony pobierania zestawu SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Tworzenie przestrzeni nazw przekaźnika

Aby rozpocząć korzystanie z funkcji przekazywania na platformie Azure, należy najpierw utworzyć przestrzeń nazw usługi. Przestrzeń nazw zapewnia kontener określania zakresu na potrzeby adresowania zasobów platformy Azure w aplikacji. Postępuj zgodnie z [instrukcjami podanymi w tym miejscu](relay-create-namespace-portal.md), aby utworzyć przestrzeń nazw przekazywania.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definiowanie kontraktu usługi WCF opartego na interfejsie REST do użycia z usługą Azure Relay

Podczas tworzenia usługi w stylu REST WCF należy zdefiniować kontrakt. Kontrakt określa operacje obsługiwane przez hosta. Operacja usługi jest podobna do metody usługi sieci Web. Zdefiniuj kontrakt z interfejsem C++, C# lub Visual Basic. Każda metoda w interfejsie odpowiada określonej operacji usługi. Zastosuj atrybut [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) do każdego interfejsu i zastosuj atrybut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) do każdej operacji. 

> [!TIP]
> Jeśli metoda w interfejsie z [atrybutem ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) nie ma [atrybutu OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), ta metoda nie jest ujawniana. Kod używany do wykonywania tych zadań pojawia się w przykładzie poniżej procedury.

Główną różnicą między kontraktem programu WCF a kontraktem w stylu REST jest dodanie właściwości do [atrybutu OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Ta właściwość umożliwia mapowanie metody w interfejsie na metodę po drugiej stronie interfejsu. W tym przykładzie używa atrybutu [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) do łączenia metody z `HTTP GET` . Takie podejście umożliwia Service Bus dokładne pobieranie i interpretowanie poleceń wysyłanych do interfejsu.

### <a name="to-create-a-contract-with-an-interface"></a>Aby utworzyć kontrakt z interfejsem

1. Uruchom Microsoft Visual Studio jako administrator. Aby to zrobić, kliknij prawym przyciskiem myszy ikonę programu Visual Studio, a następnie wybierz polecenie **Uruchom jako administrator**.
1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt** wybierz pozycję **aplikacja konsoli (.NET Framework)** dla języka C# i wybierz pozycję **dalej**.
1. Nazwij projekt *ImageListener*. Użyj domyślnej **lokalizacji**, a następnie wybierz pozycję **Utwórz**.

   W przypadku projektu C# Program Visual Studio tworzy plik *programu. cs* . Ta klasa zawiera pustą metodę `Main()` wymaganą do prawidłowej kompilacji projektu aplikacji konsolowej.

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **ImageListener** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.
1. Wybierz pozycję **Przeglądaj**, a następnie wyszukaj i wybierz pozycję **windowsazure. ServiceBus**. Wybierz pozycję **Zainstaluj** i zaakceptuj warunki użytkowania.

    Ten krok powoduje dodanie odwołań do Service Bus i *System.ServiceModel.dll*. Ten pakiet automatycznie dodaje odwołania do bibliotek Service Bus i programu WCF `System.ServiceModel` .

1. Jawnie Dodaj odwołanie do `System.ServiceModel.Web.dll` projektu. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **odwołania** w folderze projektu, a następnie wybierz polecenie **Dodaj odwołanie**.
1. W obszarze **Dodaj odwołanie** wybierz pozycję **Struktura** i wprowadź *System. ServiceModel. Web* in **Search**. Zaznacz pole wyboru **System.ServiceModel.Web** i kliknij przycisk **OK**.

Następnie wprowadź następujące zmiany kodu w projekcie:

1. Dodaj następujące `using` instrukcje w górnej części pliku *program. cs* .

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) jest przestrzenią nazw umożliwiającą programowy dostęp do podstawowych funkcji usługi WCF. WCF Relay używa wielu obiektów i atrybutów WCF do definiowania kontraktów usługi. Ta przestrzeń nazw jest używana w większości aplikacji do przekazywania.
    * [System. ServiceModel. Channels](/dotnet/api/system.servicemodel.channels) ułatwia zdefiniowanie kanału, który jest obiektem, za pomocą którego komunikujesz się z Azure Relay i przeglądarką sieci Web klienta.
    * [System. ServiceModel. Web](/dotnet/api/system.servicemodel.web) zawiera typy, które umożliwiają tworzenie aplikacji opartych na sieci Web.

1. Zmień nazwę `ImageListener` przestrzeni nazw na `Microsoft.ServiceBus.Samples` .

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Bezpośrednio po otwierającym nawiasie klamrowym deklaracji przestrzeni nazw Zdefiniuj nowy interfejs o nazwie `IImageContract` i Zastosuj `ServiceContractAttribute` atrybut do interfejsu z wartością `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1` . 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    Wartość przestrzeni nazw różni się od przestrzeni nazw używanej w kodzie. Wartość przestrzeni nazw jest unikatowym identyfikatorem dla tego kontraktu i powinna zawierać informacje o wersji. Aby uzyskać więcej informacji, zobacz [Service Versioning](/dotnet/framework/wcf/service-versioning) (Obsługa wersji usług). Jawne określenie przestrzeni nazw zapobiega dodawaniu domyślnej wartości przestrzeni nazw do nazwy kontraktu.

1. W `IImageContract` interfejsie Zadeklaruj metodę dla jednej operacji, którą `IImageContract` kontrakt ujawnia w interfejsie, i Zastosuj `OperationContract` atrybut do metody, którą chcesz uwidocznić w ramach publicznego kontraktu Service Bus.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. W `OperationContract` atrybucie Dodaj `WebGet` wartość.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Dodanie `WebGet` wartości umożliwia usłudze przekaźnikowej kierowanie żądań HTTP GET do `GetImage` i translację wartości zwracanych do `GetImage` `HTTP GETRESPONSE` odpowiedzi. W dalszej części tego samouczka użyjesz przeglądarki sieci Web, aby uzyskać dostęp do tej metody i wyświetlić obraz w przeglądarce.

1. Bezpośrednio po definicji `IImageContract` zadeklaruj kanał dziedziczący z interfejsów `IImageContract` i `IClientChannel`.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Kanał jest obiektem usługi WCF, za pomocą którego usługa i klient przekazują do siebie informacje. Później utworzysz kanał w aplikacji hosta. Azure Relay następnie używa tego kanału do przekazywania żądań HTTP GET z przeglądarki do `GetImage` implementacji. Przekaźnik używa także kanału, aby pobrać `GetImage` wartość zwracaną i przetłumaczyć ją na `HTTP GETRESPONSE` przeglądarkę klienta.

1. Wybierz pozycję **kompilacja** Kompiluj  >  **rozwiązanie** , aby potwierdzić dokładność pracy wykonanej do tej pory.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Przykład definiujący kontrakt WCF Relay

Poniższy kod przedstawia podstawowy interfejs, który definiuje kontrakt WCF Relay.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>Implementowanie kontraktu usługi WCF opartego na protokole REST

Aby utworzyć usługę WCF Relay w stylu REST, należy najpierw utworzyć kontrakt przy użyciu interfejsu. Następnym krokiem jest zaimplementowanie interfejsu. Ta procedura obejmuje utworzenie klasy o nazwie `ImageService` implementującej interfejs zdefiniowany przez użytkownika `IImageContract` . Po wdrożeniu kontraktu należy skonfigurować interfejs przy użyciu pliku *App.config* . Plik konfiguracji zawiera niezbędne informacje dotyczące aplikacji. Te informacje obejmują nazwę usługi, nazwę kontraktu i typ protokołu, który jest używany do komunikacji z usługą przekaźnika. Kod używany do wykonywania tych zadań pojawia się w przykładzie poniżej procedury.

Zgodnie z poprzednimi krokami istnieje niewiele różnic między implementacją kontraktu w stylu REST i kontraktem WCF Relay.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Aby zaimplementować kontrakt usługi Service Bus oparty na interfejsie REST

1. Utwórz nową klasę o nazwie `ImageService` bezpośrednio po definicji interfejsu `IImageContract`. Klasa `ImageService` implementuje interfejs `IImageContract`.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Podobnie jak w przypadku innych implementacji interfejsów, można zaimplementować definicję w innym pliku. Jednak w przypadku tego samouczka implementację umieszczono w tym samym pliku, w którym znajduje się definicja interfejsu i metoda `Main()`.

1. Zastosuj atrybut [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) do klasy, `IImageService` Aby wskazać, że Klasa jest implementacją kontraktu WCF.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Jak wspomniano wcześniej, ta przestrzeń nazw nie jest tradycyjną przestrzenią nazw. Jest ona częścią architektury WCF, która identyfikuje kontrakt. Aby uzyskać więcej informacji, zobacz [nazwy kontraktów danych](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Dodaj obraz *jpg* do projektu. Ten plik jest obrazem wyświetlanym przez usługę w przeglądarce odbiorczej.

   1. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj**.
   1. Następnie wybierz pozycję **istniejący element**.
   1. Użyj opcji **Dodaj istniejący element** , aby przejść do odpowiedniej jpg, a następnie wybierz pozycję **Dodaj**. Podczas dodawania pliku wybierz pozycję **wszystkie pliki** z listy rozwijanej obok pozycji **Nazwa pliku**.

   W pozostałej części tego samouczka założono, że nazwa obrazu jest *image.jpg*. Jeśli masz inny plik, musisz zmienić jego nazwę lub zmienić swój kod, aby zrekompensować.

1. Aby upewnić się, że uruchomiona usługa może znaleźć plik obrazu, w **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy plik obrazu, a następnie wybierz polecenie **Właściwości**. W obszarze **Właściwości** ustaw opcję **Kopiuj do katalogu wyjściowego** na wartość **Kopiuj, jeśli nowszy**.

1. Użyj procedury w programie, [Aby utworzyć kontrakt z interfejsem](#to-create-a-contract-with-an-interface) , aby dodać odwołanie do zestawu *System.Drawing.dll* do projektu.

1. Dodaj następujące skojarzone `using` instrukcje:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. W `ImageService` klasie Dodaj następujący Konstruktor, który ładuje mapę bitową i przygotowuje go do wysłania do przeglądarki klienta:

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. Bezpośrednio po poprzednim kodzie Dodaj następującą `GetImage` metodę w `ImageService` klasie, aby zwrócić komunikat http, który zawiera obraz.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Ta implementacja używa `MemoryStream` do pobrania obrazu i przygotowania go do przesyłania strumieniowego do przeglądarki. Zaczyna się ona od zera, deklaruje zawartość strumienia jako *jpg* i przesyła strumieniowo informacje.

1. Wybierz pozycję **kompilacja** Kompiluj  >  **rozwiązanie**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Aby zdefiniować konfigurację uruchamiania usługi sieci Web w usłudze Service Bus

1. W **Eksplorator rozwiązań** kliknij dwukrotnie pozycję **App.config** , aby otworzyć plik w edytorze programu Visual Studio.

    Plik *App.config* zawiera nazwę usługi, punkt końcowy i powiązanie. Punkt końcowy jest lokalizacją Azure Relay udostępnia klientom i hostom komunikację ze sobą. Powiązanie jest typem protokołu, który jest używany do komunikacji. Główną różnicą jest to, że skonfigurowany punkt końcowy usługi odwołuje się do powiązania [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) .

1. Element XML `<system.serviceModel>` jest elementem usługi WCF definiującym co najmniej jedną usługę. W tym miejscu służy do definiowania nazwy usługi i punktu końcowego. W dolnej części `<system.serviceModel>` elementu, ale nadal w `<system.serviceModel>` , Dodaj `<bindings>` element, który ma następującą zawartość:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Ta zawartość definiuje powiązania używane w aplikacji. Można zdefiniować wiele powiązań, ale dla tego samouczka jest definiowana tylko jeden.

    Poprzedni kod definiuje WCF Relay powiązanie [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) z `relayClientAuthenticationType` ustawionym na `None` . To ustawienie wskazuje, że punkt końcowy korzystający z tego powiązania nie wymaga poświadczeń klienta.

1. Po elemencie `<bindings>` dodaj element `<services>`. Podobnie jak w przypadku powiązań, w pojedynczym pliku konfiguracji można zdefiniować wiele usług. Jednak w tym samouczku definiowana jest tylko jedna.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Ta zawartość konfiguruje usługę, która używa poprzednio zdefiniowanego ustawienia domyślnego `webHttpRelayBinding` . Używa również domyślnego `sbTokenProvider` , który jest zdefiniowany w następnym kroku.

1. Po `<services>` elemencie Utwórz `<behaviors>` element o następującej zawartości, zastępując `SAS_KEY` go kluczem sygnatury dostępu współdzielonego (SAS). Aby uzyskać klucz sygnatury dostępu współdzielonego z [Azure Portal][Azure portal], zobacz [pobieranie poświadczeń zarządzania](service-bus-relay-tutorial.md#get-management-credentials).

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. W *App.config* w `<appSettings>` elemencie Zastąp całą wartość parametrów połączenia parametrami połączenia, które zostały wcześniej uzyskane z portalu.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Wybierz pozycję **Kompiluj**  >  **kompilację rozwiązania** , aby skompilować całe rozwiązanie.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Przykład implementujący kontrakt usługi WCF oparty na protokole REST

Poniższy kod przedstawia kontrakt i implementację usługi dla usługi opartej na protokole REST działającej na Service Bus przy użyciu `WebHttpRelayBinding` powiązania.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Poniższy przykład przedstawia plik *App.config* skojarzony z usługą.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Hostowanie usługi WCF opartej na protokole REST w celu używania Azure Relay

W tej sekcji opisano sposób uruchamiania usługi sieci Web przy użyciu aplikacji konsolowej z WCF Relay. Kompletna lista kodu zapisywana w tej sekcji jest wyświetlana w przykładzie poniżej procedury.

### <a name="to-create-a-base-address-for-the-service"></a>Aby utworzyć podstawowy adres usługi

1. W `Main()` deklaracji funkcji Utwórz zmienną do przechowywania przestrzeni nazw projektu. Pamiętaj, aby zamienić na `yourNamespace` nazwę przestrzeni nazw przekaźnika, która została wcześniej utworzona.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Usługa Service Bus używa przestrzeni nazw do utworzenia unikatowego identyfikatora URI.

1. Utwórz wystąpienie identyfikatora `Uri` dla podstawowego adresu usługi, która jest oparta na tej przestrzeni nazw.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Aby utworzyć i skonfigurować hosta usługi sieci Web

Nadal w programie `Main()` Utwórz hosta usługi sieci Web przy użyciu adresu URI utworzonego wcześniej w tej sekcji.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

Host usługi jest obiektem usługi WCF tworzącym wystąpienie aplikacji hosta. Ten przykład przekazuje go do typu hosta, który ma zostać utworzony, `ImageService` a także adresu, pod którym chcesz uwidocznić aplikację hosta.

### <a name="to-run-the-web-service-host"></a>Aby uruchomić hosta usługi sieci Web

1. W programie `Main()` Dodaj następujący wiersz, aby otworzyć usługę.

    ```csharp
    host.Open();
    ```

    Usługa jest teraz uruchomiona.

1. Wyświetl komunikat z informacją, że usługa jest uruchomiona, oraz informacją o sposobie zatrzymania usługi.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Po zakończeniu zamknij hosta usługi.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Przykład kontraktu i implementacji usługi

Poniższy przykład zawiera kontrakt usługi i implementację z poprzednich kroków samouczka i hostuje usługę w aplikacji konsolowej. Skompiluj następujący kod do pliku wykonywalnego o nazwie *ImageListener.exe*.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>Uruchamianie i testowanie usługi

Po utworzeniu rozwiązania należy wykonać następujące polecenie, aby uruchomić aplikację:

1. Wybierz klawisz F5 lub przejdź do lokalizacji pliku wykonywalnego, *ImageListener\bin\Debug\ImageListener.exe*, aby uruchomić usługę. Kontynuuj działanie aplikacji, ponieważ jest ona wymagana w następnym kroku.
1. Skopiuj i wklej adres z wiersza polecenia do przeglądarki, aby wyświetlić obraz.
1. Gdy skończysz, wybierz pozycję Enter w oknie wiersza polecenia, aby zamknąć aplikację.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz wbudowaną aplikację, która korzysta z usługi Azure Relay, zobacz następujące artykuły, aby dowiedzieć się więcej:

* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Uwidacznianie lokalnej usługi WCF REST na kliencie zewnętrznym przy użyciu usługi Azure WCF Relay](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
