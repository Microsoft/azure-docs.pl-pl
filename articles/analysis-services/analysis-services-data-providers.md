---
title: Biblioteki klientów usług Azure Analysis Services | Dokumenty firmy Microsoft
description: Zawiera opis bibliotek klienta wymaganych dla aplikacji klienckich i narzędzi do łączenia usług Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: be6ee594896384bce86d892c9a7811e09a0f4efd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676580"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Biblioteki klientów do łączenia się z usługami Analysis Services

Biblioteki klienckie są niezbędne do łączenia się aplikacji klienckich i narzędzi z serwerami usług Analysis Services. Aplikacje klienckie firmy Microsoft, takie jak Power BI Desktop, Excel, SQL Server Management Studio (SSMS) i Analysis Services— rozszerzenia projektów dla programu Visual Studio instalują wszystkie trzy biblioteki klienckie i aktualizują je wraz ze zwykłymi aktualizacjami aplikacji. W niektórych przypadkach może być konieczne zainstalowanie nowszych wersji bibliotek klienckich. Niestandardowe aplikacje klienckie wymagają również instalowania bibliotek klienckich.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Pobieranie najnowszych bibliotek klienckich (Instalator Windows)  

|Pobierz  |Wersja produktu  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.36.38    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.36.38       |
|[Amo](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.6.0.1    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.6.0.1     |

## <a name="amo-and-adomd-nuget-packages"></a>Pakiety AMO i ADOMD (Pakiety NuGet)

Obiekty zarządzania usługami Analysis Services (AMO) i biblioteki klienckie ADOMD są dostępne jako pakiety instalowalne z [NuGet.org](https://www.nuget.org/). Zaleca się migrację do odwołań NuGet zamiast używać Instalatora Windows. 

|Pakiet  | Wersja produktu  | 
|---------|---------|
|[Amo](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.6.0.1     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.6.0.1      |

Zestawy pakietów NuGet AssemblyVersion są zgodne z wersją semantyczną: MAJOR. Drobne. Patch. Odwołania NuGet załadować oczekiwaną wersję, nawet jeśli istnieje inna wersja w gac (wynikające z instalacji MSI). Patch jest zwiększany dla każdej wersji. Wersje AMO i ADOMD są synchronizowane.

## <a name="understanding-client-libraries"></a>Opis bibliotek klientów

Analysis Services wykorzystują trzy biblioteki klientów, znane również jako dostawcy danych. ADOMD.NET i Analysis Services Management Objects (AMO) są bibliotekami klientów zarządzanymi. Dostawca OLE DB usług Analysis Services (MSOLAP DLL) jest macierzystą biblioteką klienta. Zazwyczaj wszystkie trzy są instalowane w tym samym czasie. **Usługa Azure Analysis Services wymaga najnowszych wersji wszystkich trzech bibliotek.** 

Aplikacje klienckie firmy Microsoft, takie jak Power BI Desktop i Excel, instalują wszystkie trzy biblioteki klienckie i aktualizują je, gdy dostępne są nowe wersje. W zależności od wersji lub częstotliwości aktualizacji niektóre biblioteki klienckie mogą nie być najnowszymi wersjami wymaganymi przez usługi Azure Analysis Services. To samo dotyczy aplikacji niestandardowych lub innych interfejsów, takich jak AsCmd, TOM czy ADOMD.NET. Te aplikacje wymagają ręcznej lub programowej instalacji bibliotek. Biblioteki klienckie do ręcznej instalacji są zawarte w pakietach funkcji programu SQL Server jako pakiety dystrybucyjne. Jednak te biblioteki klienckie są powiązane z wersją programu SQL Server i mogą nie być najnowsze.  

Biblioteki klientów dla połączeń klientów różnią się od dostawców danych wymaganych do nawiązania połączenia z serwera usług Azure Analysis Services ze źródłem danych. Aby dowiedzieć się więcej o połączeniach ze źródłem danych, zobacz [Połączenia ze źródłem danych](analysis-services-datasource.md).

## <a name="client-library-types"></a>Typy bibliotek klientów

### <a name="analysis-services-ole-db-provider-msolap"></a>Dostawca ole db usług Analysis Services (MSOLAP) 

 Analysis Services OLE DB Provider (MSOLAP) to natywna biblioteka klientów dla połączeń bazy danych usług Analysis Services. Jest używany pośrednio przez ADOMD.NET i AMO, delegując żądania połączenia do dostawcy danych. Można również wywołać dostawcę OLE DB bezpośrednio z kodu aplikacji.  
  
 Dostawca OLE DB usług Analysis Services jest instalowany automatycznie przez większość narzędzi i aplikacji klienckich używanych do uzyskiwania dostępu do baz danych usług Analysis Services. Musi być zainstalowany na komputerach używanych do uzyskiwania dostępu do danych usług Analysis Services.  
  
 Dostawców OLE DB są często określone w parametry połączenia. Parametry połączenia usługi Analysis Services używają innej nomenklatury w celu odwoływania się do dostawcy ole DB: MSOLAP. \<wersja>.dll.

### <a name="amo"></a>Amo  

 AMO jest zarządzaną biblioteką klienta używaną do administrowania serwerem i definicji danych. Jest instalowany i używany przez narzędzia i aplikacje klienckie. Na przykład SQL Server Management Studio (SSMS) używa AMO do łączenia się z Analysis Services. Połączenie za pomocą AMO jest zazwyczaj `"data source=\<servername>"`minimalne, składające się z . Po nawiązaniu połączenia, należy użyć interfejsu API do pracy z kolekcji bazy danych i głównych obiektów. Zarówno Visual Studio, jak i SSMS używają AMO do łączenia się z wystąpieniem analysis services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET jest biblioteką klienta zarządzanych danych używaną do wykonywania zapytań o dane usług Analysis Services. Jest instalowany i używany przez narzędzia i aplikacje klienckie. 
  
 Podczas łączenia się z bazą danych właściwości ciągu połączenia dla wszystkich trzech bibliotek są podobne. Prawie każdy ciąg połączenia zdefiniowany dla ADOMD.NET przy użyciu [microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) działa również dla AMO i Analysis Services OLE DB Provider (MSOLAP). Aby dowiedzieć się więcej, zobacz [Właściwości ciągu połączenia &#40;&#41;usług Analysis Services ](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="how-to-determine-client-library-version"></a><a name="bkmk_LibUpdate"></a>Jak określić wersję biblioteki klienta   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Przejdź do pozycji `C:\Program Files\Microsoft Analysis Services\AS OLEDB\` (Plik > Nowy > Inny). Jeśli masz więcej niż jeden folder, wybierz wyższy numer.
  
2.  Kliknij prawym przyciskiem myszy polecenie **msolap.dll** > **Właściwości** > **szczegóły**. Jeśli nazwa pliku jest msolap140.dll, jest starsza niż najnowsza wersja i powinna zostać uaktualniona.
    
    ![Szczegóły biblioteki klienta](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>Amo

1. Przejdź do pozycji `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\` (Plik > Nowy > Inny). Jeśli masz więcej niż jeden folder, wybierz wyższy numer.
2. Kliknij prawym przyciskiem myszy pozycję Szczegóły właściwości **usługi Microsoft.AnalysisServices** > **Properties** > **.**  

### <a name="adomd"></a>ADOMD

1. Przejdź do pozycji `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\` (Plik > Nowy > Inny). Jeśli masz więcej niż jeden folder, wybierz wyższy numer.
2. Kliknij prawym przyciskiem myszy **pozycję Microsoft.AnalysisServices.AdomdClient** > **Properties** > **Details**.  


## <a name="next-steps"></a>Następne kroki
[Łączenie się z programem Excel](analysis-services-connect-excel.md)    
[Łączenie z usługą Power BI](analysis-services-connect-pbi.md)
