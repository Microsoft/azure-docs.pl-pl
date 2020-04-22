---
title: Łączenie się z serwerami usług Azure Analysis Services| Dokumenty firmy Microsoft
description: Dowiedz się, jak łączyć się z serwerem usług Analysis Services na platformie Azure i ujmować dane z serwera usług Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cc671629934d80b3e727ca69f9026f534d05e160
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676646"
---
# <a name="connecting-to-servers"></a>Łączenie z serwerami

W tym artykule opisano łączenie się z serwerem przy użyciu aplikacji do modelowania danych i zarządzania, takich jak SQL Server Management Studio (SSMS) lub Visual Studio z projektami analysis services lub z aplikacjami raportowania klienta, takimi jak Microsoft Excel, Power BI Desktop lub aplikacje niestandardowe. Połączenia z usługami Azure Analysis Services używają protokołu HTTPS.

## <a name="client-libraries"></a>Biblioteki klienta

[Pobierz najnowsze biblioteki klientów](analysis-services-data-providers.md)

Wszystkie połączenia z serwerem, niezależnie od typu, wymagają zaktualizowanych bibliotek klienta AMO, ADOMD.NET i OLEDB, aby połączyć się z serwerem usług Analysis Services i połączyć się z serwerem usług Analysis Services. W przypadku ssms, programu Visual Studio, programu Excel 2016 i nowszych oraz usługi Power BI najnowsze biblioteki klienckie są instalowane lub aktualizowane za pomocą wersji miesięcznych. Jednak w niektórych przypadkach jest możliwe, że aplikacja może nie mieć najnowszych. Na przykład, gdy zasady opóźniają aktualizacje lub aktualizacje usługi Office 365 znajdują się na kanale odroczonym.

> [!NOTE]
> Biblioteki klienckie nie mogą łączyć się z usługami Azure Analysis Services za pośrednictwem serwerów proxy, które wymagają nazwy użytkownika i hasła. 

## <a name="server-name"></a>Nazwa serwera

Podczas tworzenia serwera usług Analysis Services na platformie Azure, należy określić unikatową nazwę i region, w którym ma zostać utworzony serwer. Podczas określania nazwy serwera w połączeniu schemat nazewnictwa serwera jest:

```
<protocol>://<region>/<servername>
```
 Gdzie protokół jest ciąg **asazure**, region jest Uri, gdzie serwer został utworzony (na przykład westus.asazure.windows.net) i nazwa serwera jest nazwą unikatowego serwera w regionie.

### <a name="get-the-server-name"></a>Pobierz nazwę serwera

W **witrynie Azure portal** > nazwa serwera > **przeglądu** > **serwera**, skopiuj całą nazwę serwera. Jeśli inni użytkownicy w organizacji również łączą się z tym serwerem, możesz udostępnić im tę nazwę serwera. Podczas określania nazwy serwera należy użyć całej ścieżki.

![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Protokół dla regionu Wschodnich Stanów Zjednoczonych 2 to **aspaaseastus2**.

## <a name="connection-string"></a>Parametry połączenia

Podczas łączenia się z usługami Azure Analysis Services przy użyciu modelu obiektów tabelaryczne należy użyć następujących formatów ciągu połączenia:

###### <a name="integrated-azure-active-directory-authentication"></a>Zintegrowane uwierzytelnianie usługi Azure Active Directory

Zintegrowane uwierzytelnianie pobiera pamięć podręczną poświadczeń usługi Azure Active Directory, jeśli jest dostępna. Jeśli nie, zostanie wyświetlone okno logowania platformy Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Uwierzytelnianie usługi Azure Active Directory z nazwą użytkownika i hasłem

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Uwierzytelnianie systemu Windows (zintegrowane zabezpieczenia)

Użyj konta systemu Windows z uruchomionym bieżącym procesem.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Łączenie się za pomocą pliku odc

W przypadku starszych wersji programu Excel użytkownicy mogą łączyć się z serwerem usług Azure Analysis Services przy użyciu pliku połączenia danych pakietu Office (odc). Aby dowiedzieć się więcej, zobacz [Tworzenie pliku połączenia danych pakietu Office (odc).](analysis-services-odc.md)


## <a name="next-steps"></a>Następne kroki

[Łączenie się z programem Excel](analysis-services-connect-excel.md)    
[Łączenie się z siecią Power BI](analysis-services-connect-pbi.md)   
[Zarządzanie serwerem](analysis-services-manage.md)   

