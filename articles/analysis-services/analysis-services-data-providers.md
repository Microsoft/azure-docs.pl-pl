---
title: Azure Analysis Services biblioteki klienckie | Microsoft Docs
description: Zawiera opis bibliotek klienckich wymaganych przez aplikacje klienckie i narzędzia do łączenia Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8c02be378febacc4db0b077a3be69339ff9710a0
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300913"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Biblioteki klienckie do łączenia się z Analysis Services

Biblioteki klienckie są niezbędne dla aplikacji i narzędzi klienckich do łączenia się z serwerami Analysis Services. Aplikacje klienckie firmy Microsoft, takie jak Power BI Desktop, Excel, SQL Server Management Studio (SSMS) i Analysis Services Extension dla programu Visual Studio, instalują wszystkie trzy biblioteki klienckie i aktualizują je wraz z regularnymi aktualizacjami aplikacji. W niektórych przypadkach może być konieczne zainstalowanie nowszych wersji bibliotek klienckich. Niestandardowe aplikacje klienckie wymagają również zainstalowania bibliotek klienckich.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Pobierz najnowsze biblioteki klienckie (Instalator Windows)  

|Pobierz  |Wersja produktu  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.42.26    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.42.26       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   19.2.0.2    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    19.2.0.2     |

## <a name="amo-and-adomd-nuget-packages"></a>Biblioteki AMO i ADOMD (pakiety NuGet)

Biblioteki ADOMD Management Objects (AMO) i klienckie usługi są dostępne jako pakiety instalowalne z [NuGet.org](https://www.nuget.org/). Analysis Services Zaleca się przeprowadzenie migracji do odwołań NuGet zamiast używania Instalator Windows. 

|Pakiet  | Wersja produktu  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    19.2.0.2     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   19.2.0.2      |

Zestawy pakietów NuGet AssemblyVersion są zgodne z wersją semantyki: główna. Średni. Wysłana. Pakiet NuGet odwołuje się do załadowania oczekiwanej wersji, nawet jeśli istnieje inna wersja w pamięci GAC (z powodu instalacji MSI). Poprawka jest zwiększana dla każdej wersji. Wersje biblioteki AMO i ADOMD są utrzymywane w synchronizacji.

## <a name="understanding-client-libraries"></a>Informacje o bibliotekach klienta

Analysis Services wykorzystywać trzy biblioteki klienckie, znane także jako dostawcy danych. ADOMD.NET i Analysis Services Management Objects (AMO) są zarządzanymi bibliotekami klienckimi. Analysis Services OLE DB Provider (MSOLAP DLL) jest natywną biblioteką klienta. Zwykle wszystkie trzy są instalowane w tym samym czasie. **Azure Analysis Services wymaga najnowszych wersji wszystkich trzech bibliotek**. 

Aplikacje klienckie firmy Microsoft, takie jak Power BI Desktop i Excel, instalują wszystkie trzy biblioteki klienckie i aktualizują je, gdy są dostępne nowe wersje. W zależności od wersji lub częstotliwości aktualizacji niektóre biblioteki klienckie mogą nie być najnowszymi wersjami wymaganymi przez Azure Analysis Services. To samo dotyczy aplikacji niestandardowych lub innych interfejsów, takich jak AsCmd, TOM czy ADOMD.NET. Aplikacje te wymagają ręcznej lub programowo instalacji bibliotek. Biblioteki klienckie do instalacji ręcznej są zawarte w pakietach SQL Server Feature Pack jako pakiety dystrybucyjne. Jednak te biblioteki klienckie są powiązane z wersją SQL Server i mogą być nieaktualne.  

Biblioteki klienckie dla połączeń klientów różnią się od dostawców danych wymaganych do nawiązania połączenia z serwerem Azure Analysis Services ze źródłem danych. Aby dowiedzieć się więcej o połączeniach ze źródłem danych, zobacz [połączenia ze źródłami danych](analysis-services-datasource.md).

## <a name="client-library-types"></a>Typy bibliotek klienta

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB Provider (MSOLAP) 

 Analysis Services OLE DB Provider (MSOLAP) to natywna Biblioteka kliencka dla Analysis Services połączeń z bazą danych. Jest on używany pośrednio przez zarówno ADOMD.NET, jak i AMO, co umożliwia delegowanie żądań połączeń do dostawcy danych. Dostawcę OLE DB można również wywołać bezpośrednio z poziomu kodu aplikacji.  
  
 Analysis Services OLE DB Provider jest instalowana automatycznie przez większość narzędzi i aplikacji klienckich używanych do uzyskiwania dostępu do Analysis Services baz danych. Należy ją zainstalować na komputerach używanych do uzyskiwania dostępu do danych Analysis Services.  
  
 Dostawcy OLE DB są często określeni w parametrach połączenia. Analysis Services parametry połączenia używają innej nomenklatury do odwoływania się do dostawcy OLE DB: MSOLAP. \<version> . bibliotece.

### <a name="amo"></a>AMO  

 Biblioteka AMO jest zarządzaną biblioteką kliencką służącą do administrowania serwerem i definicji danych. Jest ona instalowana i używana przez narzędzia i aplikacje klienckie. Na przykład SQL Server Management Studio (SSMS) używa usługi AMO do nawiązywania połączenia z Analysis Services. Połączenie korzystające z biblioteki AMO jest zwykle minimalne, składające się z `"data source=\<servername>"` . Po nawiązaniu połączenia użyj interfejsu API do pracy z kolekcjami baz danych i obiektami głównymi. Zarówno program Visual Studio, jak i narzędzie SSMS używają biblioteki AMO do nawiązywania połączenia z wystąpieniem Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET to zarządzana Biblioteka klienta danych służąca do wykonywania zapytań dotyczących danych Analysis Services. Jest ona instalowana i używana przez narzędzia i aplikacje klienckie. 
  
 Podczas łączenia się z bazą danych właściwości parametrów połączenia dla wszystkich trzech bibliotek są podobne. Prawie wszystkie parametry połączenia zdefiniowane dla ADOMD.NET przy użyciu [Microsoft. AnalysisServices. klienta AdomdClient. AdomdConnection. ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) działają również dla biblioteki AMO i Analysis Services OLE DB Provider (MSOLAP). Aby dowiedzieć się więcej, zobacz [właściwości parametrów połączenia &#40;Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="how-to-determine-client-library-version"></a><a name="bkmk_LibUpdate"></a>Jak określić wersję biblioteki klienta   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Przejdź do witryny `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Jeśli masz więcej niż jeden folder, wybierz wyższą liczbę.
  
2.  Kliknij prawym przyciskiem myszy pozycję właściwości **MSOLAP. dll**  >  **Properties**  >  **szczegóły**. Jeśli nazwa pliku to msolap140. dll, jego wersja jest starsza niż najnowsza i powinna zostać uaktualniona.
    
    ![Szczegóły biblioteki klienta](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Przejdź do witryny `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Jeśli masz więcej niż jeden folder, wybierz wyższą liczbę.
2. Kliknij prawym przyciskiem myszy pozycję **Microsoft. AnalysisServices**  >  **Properties**  >  **szczegóły**.  

### <a name="adomd"></a>ADOMD

1. Przejdź do witryny `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Jeśli masz więcej niż jeden folder, wybierz wyższą liczbę.
2. Kliknij prawym przyciskiem myszy właściwości **Microsoft. AnalysisServices. klienta AdomdClient**  >  **Properties**  >  **szczegóły**.  


## <a name="next-steps"></a>Następne kroki
[Łączenie z programem Excel](analysis-services-connect-excel.md)    
[Łączenie z usługą Power BI](analysis-services-connect-pbi.md)
