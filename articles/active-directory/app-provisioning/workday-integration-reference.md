---
title: Dokumentacja integracji Azure Active Directory i produktu Workday
description: Szczegółowee techniczne głębokie w dniach roboczych — obsługa administracyjna oparta na HR
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 02/09/2021
ms.author: chmutali
ms.openlocfilehash: 2b1a43ee6b13d32c0eaed92538cf9c25405e061b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104335"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Jak Azure Active Directory aprowizacji integruje się z dniem roboczym

[Azure Active Directory usługi aprowizacji użytkowników](../app-provisioning/user-provisioning.md) integrują się z usługą [Workday HCM](https://www.workday.com) , aby zarządzać cyklem życia tożsamości użytkowników. Azure Active Directory oferuje trzy wstępnie skompilowane integracje: 

* [Inicjowanie obsługi administracyjnej użytkowników w środowisku lokalnym Active Directory](../saas-apps/workday-inbound-tutorial.md)
* [Dzień roboczy do Azure Active Directory aprowizacji użytkowników](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday Writeback](../saas-apps/workday-writeback-tutorial.md)

W tym artykule wyjaśniono, jak działa integracja oraz jak można dostosować zachowanie aprowizacji dla różnych scenariuszy kadr. 

## <a name="establishing-connectivity"></a>Ustanawianie łączności 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Ograniczanie dostępu do interfejsu API platformy Workday do punktów końcowych usługi Azure AD
Usługa Azure AD Provisioning używa uwierzytelniania podstawowego, aby nawiązać połączenie z punktami końcowymi interfejsu API usług sieci Web w programie Workday.  

Aby zapewnić lepszą ochronę łączności między usługą Azure AD Provisioning i produktem Workday, można ograniczyć dostęp, aby wyznaczeni użytkownicy systemu integracyjnego miały dostęp tylko do interfejsów API usługi Azure AD. Skontaktuj się z administratorem programu Workday, aby dokończyć następującą konfigurację w dzierżawie produktu Workday. 

1. Pobierz [najnowsze zakresy adresów IP](https://www.microsoft.com/download/details.aspx?id=56519) dla chmury publicznej platformy Azure. 
1. Otwórz plik i Wyszukaj tag **usługi azureactivedirectory** 

   >[!div class="mx-imgBorder"] 
   >![Zakres adresów IP usługi Azure AD](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Skopiuj wszystkie zakresy adresów IP wymienione w elemencie *addressPrefixes* i użyj zakresu, aby skompilować listę adresów IP.
1. Zaloguj się do portalu administracyjnego produktu Workday. 
1. Aby utworzyć nowy zakres adresów IP dla centrów danych platformy Azure, można uzyskać dostęp do zadania **Zachowaj zakresy adresów IP** . Określ zakresy adresów IP (przy użyciu notacji CIDR) jako listę rozdzieloną przecinkami.  
1. Aby utworzyć nowe zasady uwierzytelniania, uzyskaj dostęp do zadania **Zarządzaj zasadami uwierzytelniania** . W zasadach uwierzytelniania Użyj listy dozwolonych uwierzytelnień, aby określić zakres adresów IP usługi Azure AD i grupę zabezpieczeń, dla których będzie dozwolony dostęp z tego zakresu adresów IP. Zapisz zmiany. 
1. Uzyskaj dostęp do zadania **Aktywuj wszystkie oczekujące zmiany zasad uwierzytelniania** , aby potwierdzić zmiany.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>Ograniczanie dostępu do danych procesu roboczego w programie Workday przy użyciu grup zabezpieczeń z ograniczeniami

Domyślne kroki służące do [konfigurowania użytkownika systemu integracji produktu Workday](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) przyznają dostęp do pobierania wszystkich użytkowników w dzierżawie w usłudze Workday. W niektórych scenariuszach integracji można ograniczyć dostęp, tak aby użytkownicy należący do określonych organizacji nadzorczych mogli zwrócić Get_Workers wywołanie interfejsu API i przetworzone przez łącznik usługi Azure AD Workday. 

To wymaganie można spełnić przez pracę z administratorem platformy Workday i Konfigurowanie grup zabezpieczeń ograniczonego systemu integracji. Aby uzyskać więcej informacji o tym, jak to zrobić, zapoznaj *się z* artykułem dotyczącym [tej społeczności](https://community.workday.com/forums/customer-questions/620393)

Ta strategia ograniczania dostępu przy użyciu ograniczonych ISSG (grup zabezpieczeń systemu integracji) jest szczególnie przydatna w następujących scenariuszach: 
* **Scenariusz wdrażania etapowego**: masz dużą dzierżawę produktu Workday i planujesz przeprowadzenie etapowego wdrażania usługi Azure AD do zautomatyzowanej aprowizacji. W tym scenariuszu zamiast wykluczania użytkowników, którzy nie znajdują się w zakresie bieżącej fazy z filtrami określania zakresu usługi Azure AD, zalecamy skonfigurowanie ograniczonych ISSG tak, aby tylko pracownicy w zakresie mieli dostęp do usługi Azure AD.
* **Scenariusz wielu zadań aprowizacji**: masz dużą dzierżawę w usłudze Workday i wiele domen usługi AD, które obsługują różne jednostki biznesowe/dział/firmę. Aby zapewnić obsługę tej topologii, należy uruchomić wiele dni roboczych z zadaniami aprowizacji usługi Azure AD przy użyciu poszczególnych zadań. W tym scenariuszu zamiast używać filtrów określania zakresu usługi Azure AD do wykluczania danych procesu roboczego zalecamy skonfigurowanie ograniczonych ISSG, tak aby tylko odpowiednie dane procesu roboczego były widoczne dla usługi Azure AD. 

### <a name="workday-test-connection-query"></a>Zapytanie dotyczące połączenia testowego produktu Workday

W celu przetestowania łączności z produktem Workday usługa Azure AD wyśle następujące żądanie dotyczące usług sieci Web w programie *Get_Workers* Workday. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>Jak działa pełna synchronizacja

**Pełna synchronizacja** w kontekście inicjowania obsługi opartej na programie Workday odnosi się do procesu pobierania wszystkich tożsamości z produktu Workday i określania, jakie reguły aprowizacji mają być stosowane do każdego obiektu procesu roboczego. Pełna synchronizacja jest wykonywana po włączeniu aprowizacji po raz pierwszy, a także po *ponownym uruchomieniu inicjowania obsługi administracyjnej* z Azure Portal lub przy użyciu interfejsów API programu Graph. 

Usługa Azure AD wysyła następujące żądanie usług sieci Web *Get_Workers* Workday, aby pobrać dane procesu roboczego. Zapytanie wyszukuje dziennik transakcji dla programu Workday dla wszystkich wpisów z obowiązującymi procesami roboczymi, które odpowiadają działaniu pełnej synchronizacji. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
Węzeł *Response_Group* służy do określania, które atrybuty procesu roboczego pobrać z produktu Workday. Opis każdej flagi w węźle *Response_Group* można znaleźć w [dokumentacji interfejsu API Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)produktu Workday. 

Niektóre wartości flag określone w węźle *Response_Group* są obliczane na podstawie atrybutów skonfigurowanych w aplikacji do aprowizacji w dniu Workday usługi Azure AD. Zapoznaj się z sekcją w obszarze *obsługiwane jednostki* , aby uzyskać kryteria używane do ustawiania wartości flag. 

Odpowiedź *Get_Workers* z produktu Workday dla powyższego zapytania obejmuje liczbę rekordów procesów roboczych i liczbę stron.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
Aby pobrać następną stronę zestawu wyników, następna kwerenda *Get_Workers* określa numer strony jako parametr w *Response_Filter*.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Usługa Azure AD Provisioning przetwarza każdą stronę i wykonuje iterację przez wszystkich efektywnych pracowników podczas pełnej synchronizacji. Dla każdego wpisu procesu roboczego zaimportowanego z produktu Workday:
* [Wyrażenie XPath](workday-attribute-reference.md) jest stosowane do pobierania wartości atrybutów z produktu Workday.
* Są stosowane mapowania atrybutów i reguły dopasowywania. 
* Usługa określa, jakie operacje należy wykonać w celu (Azure AD/AD). 

Po zakończeniu przetwarzania zostanie zapisany znacznik czasu skojarzony z początkiem pełnej synchronizacji jako znak wodny. Ten znak wodny służy jako punkt początkowy cyklu synchronizacji przyrostowej. 

## <a name="how-incremental-sync-works"></a>Jak działa synchronizacja przyrostowa

Po pełnej synchronizacji usługa Azure AD Provisioning przechowuje `LastExecutionTimestamp` i używa jej do tworzenia zapytań różnicowych w celu pobierania zmian przyrostowych. Podczas synchronizacji przyrostowej usługa Azure AD wysyła następujące typy zapytań do produktu Workday: 

* [Zapytanie o ręczne aktualizacje](#query-for-manual-updates)
* [Zapytanie o aktualne aktualizacje i zakończenia](#query-for-effective-dated-updates-and-terminations)
* [Zapytanie o zatrudnienie w przyszłości](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>Zapytanie o ręczne aktualizacje

Poniższe *Get_Workers* żądania zapytań dotyczących ręcznych aktualizacji, które wystąpiły między ostatnim wykonaniem a bieżącym czasem wykonania. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>Zapytanie o aktualne aktualizacje i zakończenia

Poniższe *Get_Workers* żądania dla aktualizacji z datą obowiązywania, które wystąpiły od czasu ostatniego wykonania. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>Zapytanie o zatrudnienie w przyszłości

Jeśli którykolwiek z powyższych zapytań zwróci zatrudnienie w przyszłości, następujące żądanie *Get_Workers* służy do pobierania informacji na temat nowego zatrudnienia w przyszłości. Atrybut *WID* nowego zatrudnienia jest używany do przeszukiwania, a Data wprowadzenia jest ustawiana na datę i godzinę zatrudnienia. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>Pobieranie atrybutów danych procesu roboczego

Interfejs API *Get_Workers* może zwracać różne zestawy danych skojarzone z procesem roboczym. W zależności od [wyrażeń interfejsu API XPath](workday-attribute-reference.md) skonfigurowanych w schemacie aprowizacji usługa Azure AD Provisioning określa zestawy danych do pobrania z produktu Workday. Odpowiednio flagi *Response_Group* są ustawiane w żądaniu *Get_Workers* . 

Poniższa tabela zawiera wskazówki dotyczące konfiguracji mapowania do użycia w celu pobrania określonego zestawu danych. 

| \# | Jednostka produktu Workday                       | Uwzględnione domyślnie | Wzorzec XPATH, który ma zostać określony w mapowaniu, aby można było pobrać jednostki inne niż domyślne             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | Dane osobowe                        | Tak                 | WD: dane procesu roboczego \_ /WD: \_ dane osobowe                                             |
| 2  | Dane dotyczące zatrudnienia                      | Tak                 | WD: dane procesu roboczego \_ /WD: dane o zatrudnieniu \_                                           |
| 3  | Dodatkowe dane zadania                  | Tak                 | WD: dane procesu roboczego \_ /WD: dane o zatrudnieniu \_ /WD: \_ zadanie danych zadania procesu roboczego \_ \[ @wd:Primary \_ = 0\]|
| 4  | Dane organizacji                    | Tak                 | WD: dane procesu roboczego \_ /WD: \_ dane organizacji                                         |
| 5  | Dane łańcucha zarządzania                | Tak                 | WD: dane procesu roboczego \_ /WD: \_ dane łańcucha zarządzania \_                                    |
| 6  | Organizacja nadzorcza             | Tak                 | ORGANU                                                                 |
| 7  | Firma                              | Tak                 | Przedsiębiorstwo                                                                     |
| 8  | Jednostka biznesowa                        | Nie                  | "jednostka BIZNESowa \_ "                                                              |
| 9  | Hierarchia jednostek firmy              | Nie                  | " \_ \_ Hierarchia jednostek biznesowej"                                                   |
| 10 | Hierarchia firmy                    | Nie                  | " \_ Hierarchia firmy"                                                          |
| 11 | Cost Center                          | Nie                  | " \_ centrum kosztów"                                                                |
| 12 | Hierarchia centrum kosztów                | Nie                  | " \_ \_ Hierarchia centrum kosztów"                                                     |
| 13 | Środków                                 | Nie                  | ŚRODKÓW                                                                        |
| 14 | Hierarchia Funduszu                       | Nie                  | " \_ Hierarchia funduszu"                                                             |
| 15 | Prezent                                 | Nie                  | PREZENT                                                                        |
| 16 | Hierarchia prezentów                       | Nie                  | " \_ Hierarchia prezentów"                                                             |
| 17 | Uprawnienie                                | Nie                  | DAWAĆ                                                                       |
| 18 | Przyznaj hierarchię                      | Nie                  | "Udziel \_ hierarchii"                                                            |
| 19 | Hierarchia lokacji biznesowej              | Nie                  | " \_ \_ Hierarchia lokacji biznesowej"                                                   |
| 20 | Organizacja macierzy                  | Nie                  | OBSZARZE                                                                      |
| 21 | Grupa wynagrodzeń                            | Nie                  | " \_ Grupa wynagrodzeń"                                                                  |
| 22 | Programy                             | Nie                  | PROGRAMU                                                                    |
| 23 | Hierarchia programu                    | Nie                  | " \_ Hierarchia programu"                                                          |
| 24 | Region (Region)                               | Nie                  | " \_ Hierarchia regionów"                                                           |
| 25 | Hierarchia lokalizacji                   | Nie                  | " \_ Hierarchia lokalizacji"                                                         |
| 26 | Dane aprowizacji konta            | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane aprowizacji konta \_                                |
| 27 | Dane sprawdzania w tle                | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane sprawdzania w tle \_                                    |
| 28 | Dane uprawnień do korzyści             | Nie                  | WD: dane procesu roboczego \_ /WD: korzyści dotyczące \_ uprawnień \_                                 |
| 29 | Dane dotyczące rejestracji korzyści              | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane rejestracji korzyści \_                                  |
| 30 | Dane kariery                          | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane kariery                                               |
| 31 | Dane kompensacji                    | Nie                  | WD: dane procesu roboczego \_ /WD: dane dotyczące kompensacji \_                                         |
| 32 | Warunkowe dane urzędu skarbowego | Nie                  | WD: dane procesu roboczego \_ /WD: warunkowy \_ \_ urząd podatkowy procesu roboczego \_ \_ \_ \_ dane typu       |
| 33 | Dane elementu deweloperskiego                | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane elementu deweloperskiego \_                                    |
| 34 | Dane umów pracowników              | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane umów pracowniczych \_                                  |
| 35 | Dane przeglądu pracownika                 | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane przeglądu pracownika \_                                     |
| 36 | Opinia otrzymała dane               | Nie                  | WD: dane procesu roboczego \_ /WD: dane zwrotne zostały \_ odebrane \_                                   |
| 37 | Dane celu procesu roboczego                     | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane celu procesu roboczego \_                                         |
| 38 | Dane zdjęć                           | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane zdjęć                                                |
| 39 | Dane kwalifikacji                   | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane kwalifikacji                                        |
| 40 | Powiązane dane osób                 | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane osób pokrewnych \_                                     |
| 41 | Dane roli                            | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane roli                                                 |
| 42 | Dane dotyczące umiejętności                           | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane dotyczące umiejętności                                                |
| 43 | Dane profilu sukcesu              | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane profilu \_ sukcesu                                  |
| 44 | Dane oceny talent               | Nie                  | WD: dane procesu roboczego \_ /WD: talent \_ oceny wydajności \_                                   |
| 45 | Dane konta użytkownika                    | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane konta użytkownika \_                                        |
| 46 | Dane dokumentu procesu roboczego                 | Nie                  | WD: dane procesu roboczego \_ /WD: \_ dane dokumentu procesu roboczego \_                                     |

>[!NOTE]
>Każda jednostka produktu Workday wymieniona w tabeli jest chroniona przez **zasady zabezpieczeń domeny** w usłudze Workday. Jeśli nie możesz pobrać żadnego atrybutu skojarzonego z jednostką po ustawieniu odpowiedniego wyrażenia XPATH, skontaktuj się z administratorem platformy Workday, aby upewnić się, że odpowiednie zasady zabezpieczeń domeny zostały skonfigurowane dla użytkownika systemu integracji skojarzonego z aplikacją aprowizacji. Na przykład aby pobrać *dane dotyczące umiejętności*, *należy uzyskać dostęp* do danych procesu roboczego domeny produktu Workday *: umiejętności i doświadczenia*. 

Poniżej przedstawiono kilka przykładów, w których można zwiększyć integrację z usługą Workday w celu spełnienia określonych wymagań. 

**Przykład 1**

Załóżmy, że chcesz pobrać następujące zestawy danych z produktu Workday i korzystać z nich w regułach aprowizacji:

* Centrum kosztu
* Hierarchia centrum kosztów
* Grupa wynagrodzeń

Powyższe zestawy danych nie są domyślnie uwzględniane. Aby pobrać te zestawy danych:
1. Zaloguj się do Azure Portal i Otwórz aplikację Workday do usługi AD/Azure AD User aprowizacji. 
1. W bloku aprowizacji Edytuj mapowania i Otwórz listę atrybutów Workday z sekcji Zaawansowane. 
1. Dodaj następujące definicje atrybutów i oznacz je jako "wymagane". Te atrybuty nie będą zamapowane na żadnym atrybucie w usługach AD i Azure AD. Służą one tylko jako sygnały łącznika, aby pobrać informacje o centrum kosztu, hierarchii centrum kosztów i grupie wynagrodzenia. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Nazwa atrybutu | Wyrażenie interfejsu API XPATH |
     >|---|---|
     >| CostCenterHierarchyFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = "Organization_Type_ID"] = "COST_CENTER_HIERARCHY"]/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = "Organization_Type_ID"] = "COST_CENTER"]/WD: Organization_Data/WD: Organization_Code/Text () |
     >| PayGroupFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = "Organization_Type_ID"] = "PAY_GROUP"]/WD: Organization_Data/WD: Organization_Reference_ID/Text () |

1. Gdy zestaw danych centrum kosztów i grup wynagrodzeń jest dostępny w odpowiedzi *Get_Workers* , możesz użyć poniższych wartości XPath, aby pobrać nazwę centrum kosztu, kod centrum kosztów i grupę wynagrodzeń. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Nazwa atrybutu | Wyrażenie interfejsu API XPATH |
     >|---|---|
     >| CostCenterName  | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = "centrum kosztów"]/WD: Organization_Name/Text () |
     >| CostCenterCode | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = "centrum kosztów"]/WD: Organization_Code/Text () |
     >| PayGroup | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = "Grupa wynagrodzeń"]/WD: Organization_Name/Text () |

**Przykład 2**

Załóżmy, że chcesz pobrać Certyfikaty skojarzone z użytkownikiem. Te informacje są dostępne w ramach zestawu *danych kwalifikacji* . Aby uzyskać ten zestaw danych jako część odpowiedzi *Get_Workers* , użyj następującego wyrażenia XPath: 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**Przykład 3**

Załóżmy, że chcesz pobrać *grupy aprowizacji* przypisane do procesu roboczego. Te informacje są dostępne w ramach zestawu *danych aprowizacji konta* . Aby uzyskać ten zestaw danych jako część odpowiedzi *Get_Workers* , użyj następującego wyrażenia XPath: 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="handling-different-hr-scenarios"></a>Obsługa różnych scenariuszy kadr

### <a name="retrieving-international-job-assignments-and-secondary-job-details"></a>Pobieranie międzynarodowych przydziałów zadań i szczegółów zadania podrzędnego

Domyślnie łącznik Workday Pobiera atrybuty skojarzone z zadaniem podstawowym procesu roboczego. Łącznik obsługuje również pobieranie *dodatkowych danych zadania* skojarzonych z przypisaniami zadań międzynarodowych lub zadaniami pomocniczymi. 

Wykonaj poniższe kroki, aby pobrać atrybuty skojarzone z międzynarodowymi przypisaniami zadań: 

1. Ustaw adres URL połączenia Workday używa interfejsu API usługi sieci Web Workday w wersji 30,0 lub nowszej. Odpowiednio ustaw [poprawne wartości XPath](workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) w aplikacji do aprowizacji w programie Workday. 
1. Użyj selektora `@wd:Primary_Job=0` w `Worker_Job_Data` węźle, aby pobrać poprawny atrybut. 
   * **Przykład 1:** Aby uzyskać `SecondaryBusinessTitle` użycie wyrażenia XPath `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Title/text()`
   * **Przykład 2:** Aby uzyskać `SecondaryBusinessLocation` użycie wyrażenia XPath `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Location_Reference/@wd:Descriptor`

 

## <a name="next-steps"></a>Następne kroki

* [Informacje dotyczące konfigurowania programu Workday do Active Directory aprowizacji](../saas-apps/workday-inbound-tutorial.md)
* [Dowiedz się, jak skonfigurować zapisywanie zwrotne w usłudze Workday](../saas-apps/workday-writeback-tutorial.md)
* [Dowiedz się więcej na temat obsługiwanych atrybutów produktu Workday na potrzeby inicjowania obsługi przychodzącej](workday-attribute-reference.md)

