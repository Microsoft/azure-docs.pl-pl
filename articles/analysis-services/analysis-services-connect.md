---
title: Łączenie z serwerami Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie i pobrać dane z serwera Analysis Services na platformie Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f1b4e56dfeae2d4b7826f3ad30bc5eb4b8672b85
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361476"
---
# <a name="connecting-to-servers"></a>Łączenie z serwerami

W tym artykule opisano nawiązywanie połączenia z serwerem za pomocą aplikacji do modelowania i zarządzania danymi, takich jak SQL Server Management Studio (SSMS) lub Visual Studio z projektami Analysis Services lub z aplikacjami raportowania klienta, takimi jak Microsoft Excel, Power BI Desktop lub aplikacje niestandardowe. Połączenia z Azure Analysis Services korzystają z protokołu HTTPS.

## <a name="client-libraries"></a>Biblioteki klienta

[Pobierz najnowsze biblioteki klienckie](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)

Wszystkie połączenia z serwerem, niezależnie od typu, wymagają zaktualizowanych bibliotek klienta biblioteki AMO, ADOMD.NET i OLEDB do łączenia się z serwerem Analysis Services i interfejsem. W przypadku programów SSMS, Visual Studio, Excel 2016 i nowszych oraz Power BI najnowsze biblioteki klienckie są instalowane lub aktualizowane za pomocą comiesięcznych wersji. Jednak w niektórych przypadkach możliwe, że aplikacja może nie mieć najnowszej wersji. Na przykład gdy zasady opóźnią aktualizacje lub aktualizacje pakietu Office 365 są w odłożonym kanale.

> [!NOTE]
> Biblioteki klienckie nie mogą łączyć się z Azure Analysis Services za pomocą serwerów proxy, które wymagają nazwy użytkownika i hasła. 

## <a name="server-name"></a>Nazwa serwera

Podczas tworzenia serwera Analysis Services na platformie Azure należy określić unikatową nazwę i region, w którym ma zostać utworzony serwer. Podczas określania nazwy serwera w ramach połączenia schemat nazewnictwa serwera to:

```
<protocol>://<region>/<servername>
```
 Gdzie protokół jest ciągiem **asazure**, region jest identyfikatorem URI, w którym został utworzony serwer (na przykład westus.asazure.Windows.NET), a ServerName to nazwa unikatowego serwera w regionie.

### <a name="get-the-server-name"></a>Pobierz nazwę serwera

W **Azure Portal** > Server > **Przegląd**  >  **Nazwa serwera**, Skopiuj całą nazwę serwera. Jeśli inni użytkownicy w organizacji nawiązują połączenie z tym serwerem, możesz udostępnić tę nazwę serwera. Podczas określania nazwy serwera należy użyć całej ścieżki.

![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Protokół dla regionu Wschodnie stany USA 2 to **aspaaseastus2**.

## <a name="connection-string"></a>Parametry połączenia

Podczas nawiązywania połączenia z Azure Analysis Services przy użyciu modelu obiektów tabelarycznych należy użyć następujących formatów parametrów połączenia:

###### <a name="integrated-azure-active-directory-authentication"></a>Zintegrowane uwierzytelnianie Azure Active Directory

Uwierzytelnianie zintegrowane pobiera Azure Active Directory pamięć podręczną poświadczeń, jeśli jest dostępna. W przeciwnym razie zostanie wyświetlone okno logowanie do platformy Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Azure Active Directory uwierzytelniania przy użyciu nazwy użytkownika i hasła

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Uwierzytelnianie systemu Windows (zabezpieczenia zintegrowane)

Użyj konta systemu Windows z uruchomionym bieżącym procesem.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Nawiązywanie połączenia przy użyciu pliku odc

W przypadku starszych wersji programu Excel użytkownicy mogą łączyć się z serwerem Azure Analysis Services przy użyciu pliku połączenia danych pakietu Office (odc). Aby dowiedzieć się więcej, zobacz [Tworzenie pliku połączenia danych pakietu Office (odc)](analysis-services-odc.md).


## <a name="next-steps"></a>Następne kroki

[Łączenie z programem Excel](analysis-services-connect-excel.md)    
[Połącz z Power BI](analysis-services-connect-pbi.md)   
[Zarządzanie serwerem](analysis-services-manage.md)   

