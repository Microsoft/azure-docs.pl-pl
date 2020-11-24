---
title: Jak nawiązać połączenie telefoniczne z Twilio (.NET) | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie telefoniczne z usługą interfejsu API Twilio na platformie Azure. Przykłady kodu zapisywane w środowisku .NET.
services: ''
documentationcenter: .net
author: mimckitt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: mimckitt
ms.custom: devx-track-dotnet
ms.openlocfilehash: 22e0a19c0e2f5b29b35ebf43a3f250959c9b636d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95521125"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Jak nawiązać połączenie telefoniczne za pomocą Twilio w roli sieci Web na platformie Azure
W tym przewodniku pokazano, jak za pomocą programu Twilio utworzyć wywołanie ze strony sieci Web hostowanej na platformie Azure. Aplikacja wynikowa monituje użytkownika o wywołanie przy użyciu podanej liczby i komunikatu, jak pokazano na poniższym zrzucie ekranu.

![Formularz wywołania platformy Azure z użyciem Twilio i ASP.NET][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Wymagania wstępne
Aby użyć kodu w tym temacie, należy wykonać następujące czynności:

1. Uzyskaj konto Twilio i token uwierzytelniania z [konsoli Twilio][twilio_console]. Aby rozpocząć pracę z usługą Twilio, zarejestruj się pod adresem [https://www.twilio.com/try-twilio][try_twilio] . Cennik można obliczyć pod adresem [https://www.twilio.com/pricing][twilio_pricing] . Informacje o interfejsie API udostępnianym przez Twilio można znaleźć w temacie [https://www.twilio.com/voice/api][twilio_api] .
2. Dodaj *bibliotekę Twilio .NET* do roli sieci Web. Zobacz, **Aby dodać biblioteki Twilio do projektu roli sieci Web**, w dalszej części tego tematu.

Należy zapoznać się z tworzeniem podstawowej [roli sieci Web na platformie Azure][azure_webroles_get_started].

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Instrukcje: Tworzenie formularza sieci Web na potrzeby wywołania
<a id="use_nuget"></a>Aby dodać biblioteki Twilio do projektu roli sieci Web:

1. Otwórz rozwiązanie w programie Visual Studio.
2. Kliknij prawym przyciskiem myszy pozycję **odwołania**.
3. Kliknij pozycję **Zarządzaj pakietami NuGet**.
4. Kliknij pozycję **online**.
5. W polu Wyszukaj online wpisz *Twilio*.
6. Kliknij przycisk **Instaluj** w pakiecie Twilio.

Poniższy kod pokazuje, jak utworzyć formularz sieci Web, aby pobrać dane użytkownika w celu utworzenia wywołania. W tym przykładzie zostanie utworzona rola sieci Web ASP.NET o nazwie **TwilioCloud** .

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Instrukcje: Tworzenie kodu w celu wywołania
Poniższy kod, który jest wywoływany, gdy użytkownik ukończy formularz, tworzy komunikat wywołania i generuje wywołanie. W tym przykładzie kod jest uruchamiany w procedurze obsługi zdarzeń onkliknięcia przycisku w formularzu. (Użyj konta Twilio i tokenu uwierzytelniania zamiast wartości zastępczych przypisanych do `accountSID` i `authToken` w poniższym kodzie).

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

Zostało wykonane wywołanie i zostanie wyświetlony punkt końcowy Twilio, wersja interfejsu API i stan wywołania. Poniższy zrzut ekranu przedstawia dane wyjściowe z przykładowego przebiegu.

![Odpowiedź na wywołanie platformy Azure przy użyciu Twilio i ASP.NET][twilio_dotnet_basic_form_output]

Więcej informacji na temat TwiML można znaleźć pod adresem [https://www.twilio.com/docs/api/twiml][twiml] . Więcej informacji &lt; na temat mówią &gt; i innych zleceń Twilio można znaleźć pod adresem [https://www.twilio.com/docs/api/twiml/say][twilio_say] .

## <a name="next-steps"></a><a id="nextsteps"></a>Następne kroki
Ten kod został dostarczony, aby pokazać podstawowe funkcje przy użyciu Twilio w roli sieci Web ASP.NET na platformie Azure. Przed wdrożeniem na platformie Azure w środowisku produkcyjnym warto dodać więcej obsługi błędów lub innych funkcji. Na przykład:

* Zamiast korzystać z formularza sieci Web, można użyć usługi Azure Blob Storage lub wystąpienia Azure SQL Database do przechowywania numerów telefonów i wywoływać tekst. Aby uzyskać informacje o używaniu obiektów BLOB na platformie Azure, zobacz [jak używać usługi Azure Blob Storage w programie .NET][howto_blob_storage_dotnet]. Aby uzyskać informacje dotyczące korzystania z SQL Database, zobacz [jak używać Azure SQL Database w aplikacjach .NET][howto_sql_azure_dotnet].
* Można użyć, `RoleEnvironment.getConfigurationSettings` Aby pobrać identyfikator konta Twilio i token uwierzytelniania z ustawień konfiguracji wdrożenia, a nie na podstawie twardej wartości w formularzu. Aby uzyskać informacje o `RoleEnvironment` klasie, zobacz [przestrzeń nazw Microsoft. windowsazure. serviceruntime][azure_runtime_ref_dotnet].
* Zapoznaj się ze wskazówkami dotyczącymi zabezpieczeń Twilio na stronie [https://www.twilio.com/docs/security][twilio_docs_security] .
* Dowiedz się więcej o usłudze Twilio pod adresem [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a><a name="seealso"></a>Zobacz także
* [Jak korzystać z funkcji Twilio na potrzeby obsługi głosu i programu SMS z platformy Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: /previous-versions/azure/reference/ee741722(v=azure.100)
[azure_webroles_get_started]: ./cloud-services/cloud-services-dotnet-get-started.md