---
title: Nazwy serwerów aliasów Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak utworzyć aliasy nazw serwerów Azure Analysis Services. Następnie użytkownicy mogą połączyć się z serwerem przy użyciu krótszej nazwy aliasu zamiast nazwy serwera.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 753ac449418b78fe02640f1b70b94b5b3d7f2be5
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019039"
---
# <a name="alias-server-names"></a>Nazwy serwerów aliasów

Używając aliasu nazwy serwera, użytkownicy mogą łączyć się z serwerem Azure Analysis Services przy użyciu krótszego *aliasu* zamiast nazwy serwera. Podczas nawiązywania połączenia z aplikacji klienckiej alias jest określany jako punkt końcowy przy użyciu formatu protokołu **link://** . Punkt końcowy zwraca rzeczywistą nazwę serwera w celu nawiązania połączenia.

Nazwy serwerów aliasów są dobre dla:

- Migrowanie modeli między serwerami bez wpływu na użytkowników. 
- Przyjazne nazwy serwerów są łatwiejsze dla użytkowników. 
- Kierowanie użytkowników do różnych serwerów w różnych porach dnia. 
- Kierowanie użytkowników w różnych regionach do wystąpień, które znajdują się geograficznie bliżej, tak jak w przypadku korzystania z usługi Azure Traffic Manager. 

Każdy punkt końcowy HTTPS, który zwraca poprawną nazwę serwera Azure Analysis Services może być używany jako alias. Punkt końcowy musi obsługiwać protokół HTTPS przez port 443, a port nie może być określony w identyfikatorze URI.

![Alias przy użyciu formatu linku](media/analysis-services-alias/aas-alias-browser.png)

Podczas nawiązywania połączenia z poziomu klienta nazwa serwera aliasu jest wprowadzana przy użyciu formatu protokołu **link://** . Na przykład w Power BI Desktop:

![Połączenie Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Tworzenie aliasu

Aby utworzyć alias punktu końcowego, można użyć dowolnej metody, która zwraca prawidłową nazwę serwera Azure Analysis Services. Na przykład odwołanie do pliku na platformie Azure Blob Storage zawierające rzeczywistą nazwę serwera lub tworzenie i publikowanie aplikacji ASP.NET Web Forms.

W tym przykładzie aplikacja formularzy sieci Web ASP.NET jest tworzona w programie Visual Studio. Odwołanie do strony i kontrolka użytkownika zostaną usunięte ze strony Default. aspx. Zawartość default. aspx jest po prostu następująca Dyrektywa Page:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Zdarzenie Page_Load w Default.aspx.cs używa metody Response. Write () do zwrócenia nazwy serwera Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Zobacz też

[Biblioteki klienckie](/analysis-services/client-libraries?view=azure-analysis-services-current)   
[Łączenie z programu Power BI Desktop](analysis-services-connect-pbi.md)