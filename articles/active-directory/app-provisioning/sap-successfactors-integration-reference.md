---
title: Dokumentacja integracji Azure Active Directory i SAP SuccessFactors
description: Techniczne głębokie szczegółowe do aprowizacji rozwiązań SAP SuccessFactors-HR
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: ed97600ca1802629f81f93f4f51c92ad4b1c9bd1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256225"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Jak Azure Active Directory aprowizacji integruje się z usługą SAP SuccessFactors 

[Azure Active Directory usługa aprowizacji użytkowników](../app-provisioning/user-provisioning.md) integruje się z programem [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) , aby zarządzać cyklem życia tożsamości użytkowników. Azure Active Directory oferuje trzy wstępnie skompilowane integracje: 

* [SuccessFactors do Active Directory lokalnego aprowizacji użytkowników](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors Azure Active Directory aprowizacji użytkowników](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [SuccessFactors zapisywania zwrotnego](../saas-apps/sap-successfactors-writeback-tutorial.md)

W tym artykule wyjaśniono, jak działa integracja oraz jak można dostosować zachowanie aprowizacji dla różnych scenariuszy kadr. 

## <a name="establishing-connectivity"></a>Ustanawianie łączności 
Usługa Azure AD Provisioning używa uwierzytelniania podstawowego do łączenia się z punktami końcowymi interfejsów API interfejsu OData dla pracowników. Podczas konfigurowania aplikacji inicjowania obsługi SuccessFactors, użyj parametru *adresu URL dzierżawy* w sekcji *poświadczenia administratora* , aby skonfigurować [adres URL centrum danych interfejsu API](https://apps.support.sap.com/sap/support/knowledge/en/2215682). 

Aby dodatkowo zabezpieczyć łączność między usługą Azure AD Provisioning i SuccessFactors, możesz dodać zakresy adresów IP usługi Azure AD na liście dozwolonych adresów IP SuccessFactors przy użyciu kroków opisanych poniżej:

1. Pobierz [najnowsze zakresy adresów IP](https://www.microsoft.com/download/details.aspx?id=56519) dla chmury publicznej platformy Azure 
1. Otwórz plik i Wyszukaj tag **usługi azureactivedirectory** 

   >[!div class="mx-imgBorder"] 
   >![Zakres adresów IP usługi Azure AD](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Skopiuj wszystkie zakresy adresów IP wymienione w elemencie *addressPrefixes* i użyj zakresu, aby skompilować listę ograniczeń adresów IP.
1. Przetłumacz wartości CIDR na zakresy adresów IP.  
1. Zaloguj się do portalu administracyjnego SuccessFactors, aby dodać zakresy adresów IP do listy dozwolonych. Zapoznaj się z [uwagą do pomocy technicznej SAP 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200). Teraz możesz [wprowadzić zakresy adresów IP](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) w tym narzędziu. 

## <a name="supported-entities"></a>Obsługiwane jednostki
Dla każdego użytkownika w SuccessFactors usługa Azure AD Provisioning pobiera następujące jednostki. Każda jednostka jest rozwinięta przy użyciu interfejsu API OData *$expand* parametru zapytania. Zapoznaj się z poniższą kolumną *reguła pobierania* . Niektóre jednostki są domyślnie rozwinięte, podczas gdy niektóre jednostki są rozszerzane tylko wtedy, gdy określony atrybut jest obecny w mapowaniu. 

| \# | Jednostka SuccessFactors                  | Węzeł OData     | Reguła pobierania |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *węzeł główny*                  | Always (Zawsze)           |
| 2  | PerPersonal                            | personalInfoNav              | Always (Zawsze)           |
| 3  | PerPhone                               | phoneNav                     | Always (Zawsze)           |
| 4  | PerEmail                               | emailNav                     | Always (Zawsze)           |
| 5  | EmpEmployment                          | employmentNav                | Always (Zawsze)           |
| 6  | Użytkownik                                   | employmentNav/userNav        | Always (Zawsze)           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | Always (Zawsze)           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | Always (Zawsze)           |
| 9  | Menedżer użytkownika                         | employmentNav/userNav/Manager/empInfo | Always (Zawsze)  |
| 10 | FOCompany                              | employmentNav/jobInfoNav/companyNav | Tylko wtedy `company` , gdy `companyId` atrybut lub jest mapowany |
| 11 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | Tylko wtedy `department` , gdy `departmentId` atrybut lub jest mapowany |
| 12 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | Tylko wtedy `businessUnit` , gdy `businessUnitId` atrybut lub jest mapowany |
| 13 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | Tylko wtedy `costCenter` , gdy `costCenterId` atrybut lub jest mapowany |
| 14 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | Tylko wtedy `division` , gdy `divisionId` atrybut lub jest mapowany |
| 15 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | Tylko wtedy `jobCode` , gdy `jobCodeId` atrybut lub jest mapowany |
| 16 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | Tylko wtedy, gdy `payGrade` atrybut jest zamapowany |
| 17 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | Tylko wtedy, gdy `location` atrybut jest zamapowany |
| 18 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | Jeśli mapowanie zawiera jeden z następujących atrybutów: `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 19 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | Tylko wtedy, gdy `eventReason` atrybut jest zamapowany |
| 20 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | Tylko wtedy, gdy `assignmentType` jest zamapowany |
| 21 | Lista wyboru                | employmentNav/jobInfoNav/employmentTypeNav | Tylko wtedy, gdy `employmentType` jest zamapowany |
| 22 | Lista wyboru EmployeeClass                 | employmentNav/jobInfoNav/employeeClassNav | Tylko wtedy, gdy `employeeClass` jest zamapowany |
| 23 | Lista wyboru EmplStatus                    | employmentNav/jobInfoNav/emplStatusNav | Tylko wtedy, gdy `emplStatus` jest zamapowany |
| 24 | Lista wyboru przypisania                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | Tylko wtedy, gdy `assignmentType` jest zamapowany |

## <a name="how-full-sync-works"></a>Jak działa pełna synchronizacja
W oparciu o Mapowanie atrybutów podczas pełnej synchronizacji usługa aprowizacji usługi Azure AD wysyła następujące zapytanie interfejsu API OData, aby pobrać efektywne dane wszystkich aktywnych użytkowników. 

> [!div class="mx-tdCol2BreakAll"]
>| Parametr | Opis |
>| ----------|-------------|
>| Host interfejsu API OData | Dołącza HTTPS do *adresu URL dzierżawy*. Przykład: `https://api4.successfactors.com` |
>| Punkt końcowy interfejsu API OData | `/odata/v2/PerPerson` |
>| Parametr zapytania $format OData | `json` |
>| Parametr zapytania $filter OData | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| Parametr zapytania $expand OData | Ta wartość parametru zależy od zamapowanych atrybutów. Przykład: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| Parametr zapytania customPageSize OData | `100` |

> [!NOTE]
> Podczas pierwszej początkowej pełnej synchronizacji usługa Azure AD Provisioning nie ściąga nieaktywnych/zakończonych danych procesu roboczego.

Dla każdego użytkownika SuccessFactors usługa aprowizacji szuka konta w miejscu docelowym (Active Directory Azure AD/lokalnie) przy użyciu pasującego atrybutu zdefiniowanego w mapowaniu. Na przykład: Jeśli *personIdExternal* mapuje do *IDPracownika* i jest ustawiony jako pasujący atrybut, usługa aprowizacji używa wartości *personIdExternal* , aby wyszukać użytkownika z filtrem *IDPracownika* . W przypadku znalezienia dopasowania użytkownika program aktualizuje atrybuty obiektu docelowego. Jeśli nie zostanie znalezione dopasowanie, spowoduje to utworzenie nowego wpisu w miejscu docelowym. 

Aby sprawdzić poprawność danych zwróconych przez punkt końcowy interfejsu API protokołu OData dla określonego programu `personIdExternal` , zaktualizuj `SuccessFactorsAPIEndpoint` w ZAPYTANIU interfejsu API poniżej adres URL serwera centrum danych interfejsu API i użyj narzędzia, takiego jak program [Poster](https://www.postman.com/downloads/) , aby wywołać zapytanie. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>Jak działa synchronizacja przyrostowa

Po pełnej synchronizacji usługa Azure AD Provisioning przechowuje `LastExecutionTimestamp` i używa jej do tworzenia zapytań różnicowych na potrzeby pobierania zmian przyrostowych. Atrybuty sygnatur czasowych obecne w każdej jednostce SuccessFactors, takie jak `lastModifiedDateTime` ,, `startDate` `endDate` , i `latestTerminationDate` , są oceniane, aby sprawdzić, czy zmiana jest między `LastExecutionTimestamp` i `CurrentExecutionTime` . Jeśli tak, to zmiana wpisu jest uznawana za obowiązującą i przetworzoną do synchronizacji. 

## <a name="reading-attribute-data"></a>Odczytywanie danych atrybutów

Gdy usługa Azure AD Provisioning Service SuccessFactors kwerend, Pobiera zestaw wyników JSON. Zestaw wyników JSON zawiera wiele atrybutów przechowywanych w centrali Employee. Domyślnie schemat aprowizacji jest skonfigurowany do pobierania tylko podzbioru tych atrybutów. 

Aby pobrać dodatkowe atrybuty, wykonaj następujące czynności:
    
1. Przejdź do strony **aplikacje dla przedsiębiorstw**  ->  **SuccessFactors**  ->    ->  — umożliwia **Edytowanie**  ->  **atrybutów** aprowizacji.
1. Przewiń w dół i kliknij pozycję **Pokaż opcje zaawansowane**.
1. Kliknij pozycję **Edytuj listę atrybutów dla SuccessFactors**. 

   > [!NOTE] 
   > Jeśli opcja **Edytuj listę atrybutów dla opcji SuccessFactors** nie jest wyświetlana w Azure Portal, użyj adresu URL, *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* Aby uzyskać dostęp do strony. 

1. Kolumna **wyrażenie interfejsu API** w tym widoku wyświetla wyrażenia wykryto używane przez łącznik.

   >[!div class="mx-imgBorder"] 
   >![Wyrażenie API-Expression](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. Można edytować istniejącą wartość wykryto lub dodać nowy atrybut z prawidłowym wyrażeniem wykryto do schematu. 

W następnej sekcji znajduje się lista typowych scenariuszy edytowania wartości wykryto. 

## <a name="handling-different-hr-scenarios"></a>Obsługa różnych scenariuszy kadr

Wykryto to język zapytania dla formatu JSON, który jest podobny do XPath dla XML. Podobnie jak XPath, wykryto umożliwia wyodrębnianie i filtrowanie danych z ładunku JSON.

Za pomocą transformacji wykryto można dostosować zachowanie aplikacji do aprowizacji usługi Azure AD w celu pobrania atrybutów niestandardowych i obsługi scenariuszy, takich jak ponowne zatrudnienie, konwersja procesów roboczych i przypisanie globalne. 

W tej sekcji opisano, jak można dostosować aplikację aprowizacji dla następujących scenariuszy kadr: 
* [Pobieranie dodatkowych atrybutów](#retrieving-additional-attributes)
* [Pobieranie atrybutów niestandardowych](#retrieving-custom-attributes)
* [Obsługa scenariusza konwersji procesów roboczych](#handling-worker-conversion-scenario)
* [Obsługa scenariusza rezatrudniania](#handling-rehire-scenario)
* [Obsługa globalnego scenariusza przypisania](#handling-global-assignment-scenario)
* [Scenariusz obsługi zadań współbieżnych](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>Pobieranie dodatkowych atrybutów

Domyślny schemat aplikacji aprowizacji usługi Azure AD SuccessFactors jest dostarczany ze [wstępnie zdefiniowanymi atrybutami](sap-successfactors-attribute-reference.md). Aby dodać więcej atrybutów SuccessFactors do schematu aprowizacji, wykonaj czynności wymienione poniżej: 

1. Użyj poniższego zapytania OData, aby pobrać dane dla prawidłowego użytkownika testowego z planu Employee Central. 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. Określanie jednostki centralnej pracownika skojarzonej z atrybutem
   * Jeśli atrybut jest częścią jednostki *EmpEmployment* , należy poszukać atrybutu w węźle *employmentNav* . 
   * Jeśli atrybut jest częścią jednostki *użytkownika* , należy poszukać atrybutu w węźle *employmentNav/userNav* .
   * Jeśli atrybut jest częścią jednostki *EmpJob* , należy poszukać atrybutu w węźle *employmentNav/jobInfoNav* . 
1. Utwórz ścieżkę JSON skojarzoną z atrybutem i Dodaj ten nowy atrybut do listy atrybutów SuccessFactors. 
   * Przykład 1: Załóżmy, że chcesz dodać atrybut *okToRehire*, który jest częścią jednostki *employmentNav* , a następnie użyć wykryto  `$.employmentNav.results[0].okToRehire`
   * Przykład 2: Załóżmy, że chcesz dodać atrybut *strefy czasowej*, która jest częścią jednostki *userNav* , a następnie użyć wykryto `$.employmentNav.results[0].userNav.timeZone`
   * Przykład 3: Załóżmy, że chcesz dodać atrybut *flsaStatus*, który jest częścią jednostki *jobInfoNav* , a następnie użyć wykryto `$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
1. Zapisz schemat. 
1. Ponownie rozpocznij Inicjowanie obsługi administracyjnej.

### <a name="retrieving-custom-attributes"></a>Pobieranie atrybutów niestandardowych

Domyślnie następujące atrybuty niestandardowe są wstępnie zdefiniowane w aplikacji do aprowizacji usługi Azure AD SuccessFactors: 
* *custom01-custom15* z jednostki użytkownika (userNav)
* *customString1-customString15* z jednostki EmpEmployment (employmentNav) o nazwie *empNavCustomString1-empNavCustomString15*
* *customString1-customString15* z jednostki EmpJobInfo (jobInfoNav) o nazwie *empJobNavCustomString1-empNavJobCustomString15*

Załóżmy, że w wystąpieniu klasy Employee Central, atrybut *customString35* w *EmpJobInfo* przechowuje opis lokalizacji. Chcesz przepływać tę wartość do Active Directory atrybutu *physicalDeliveryOfficeName* . Aby skonfigurować Mapowanie atrybutów dla tego scenariusza, wykonaj czynności podane poniżej: 

1. Edytuj listę atrybutów SuccessFactors, aby dodać nowy atrybut o nazwie *empJobNavCustomString35*.
1. Ustaw wyrażenie interfejsu API wykryto dla tego atrybutu jako: `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. Zapisz i ponownie załaduj zmiany mapowania w Azure Portal.  
1. W bloku mapowanie atrybutu zamapuj *empJobNavCustomString35* na *physicalDeliveryOfficeName*.
1. Zapisz mapowanie.

Rozszerzanie tego scenariusza: 
* Jeśli chcesz zmapować atrybut *custom35* z jednostki *User* , użyj wykryto `$.employmentNav.results[0].userNav.custom35`
* Jeśli chcesz zmapować atrybut *customString35* z jednostki *EmpEmployment* , użyj wykryto `$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>Obsługa scenariusza konwersji procesów roboczych

Konwersja procesu roboczego to proces konwersji istniejącego pracownika pełnego czasu na wykonawcę lub wykonawcę na cały czas. W tym scenariuszu pracownik centralny dodaje nową jednostkę *EmpEmployment* oraz nową jednostkę *użytkownika* dla tej samej jednostki *osoby* . Jednostka *użytkownika* zagnieżdżona w poprzedniej jednostce *EmpEmployment* ma ustawioną wartość null. Aby obsłużyć ten scenariusz w taki sposób, aby nowe dane dotyczące zatrudnienia były widoczne podczas konwersji, można zbiorczo zaktualizować schemat aplikacji aprowizacji za pomocą czynności wymienionych poniżej:  

1. Otwórz blok Mapowanie atrybutów aplikacji SuccessFactors Provisioning. 
1. Przewiń w dół i kliknij pozycję **Pokaż opcje zaawansowane**.
1. Kliknij link **Przejrzyj tutaj schemat** , aby otworzyć Edytor schematu. 

   >![Zrzut ekranu przedstawia link Przejrzyj swój schemat, który otwiera Edytor schematu.](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. Kliknij link **pobierania** , aby zapisać kopię schematu przed edycją. 

   >![Zrzut ekranu przedstawia Edytor schematu z opcją Pobierz wybierz, aby zapisać kopię schematu.](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. W edytorze schematu naciśnij klawisze CTRL-H, aby otworzyć kontrolkę Znajdowanie-zamienianie.
1. W polu tekstowym Znajdź skopiuj i wklej wartość `$.employmentNav.results[0]`
1. W polu tekstowym Zamień skopiuj wartość i wklej ją `$.employmentNav.results[?(@.userNav != null)]` . Zwróć uwagę na odstępy wokół `!=` operatora, co jest ważne dla pomyślnego przetwarzania wyrażenia wykryto. 
   >![Find-Replace-Conversion](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. Kliknij opcję "Zastąp wszystko", aby zaktualizować schemat. 
1. Zapisz schemat. 
1. Powyższy proces aktualizuje wszystkie wyrażenia wykryto w następujący sposób: 
   * Stary wykryto: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Nowy wykryto: `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. Ponownie rozpocznij Inicjowanie obsługi administracyjnej. 

### <a name="handling-rehire-scenario"></a>Obsługa scenariusza rezatrudniania

Zwykle istnieją dwie opcje procesu ponownego zatrudniania: 
* Opcja 1. Tworzenie nowego profilu osoby w centrali Employee
* Opcja 2: wielokrotne użycie profilu istniejącej osoby w centrali Employee

Jeśli proces HR używa opcji 1, nie są wymagane żadne zmiany w schemacie aprowizacji. Jeśli proces HR używa opcji 2, wówczas pracownik centralny dodaje nową jednostkę *EmpEmployment* oraz nową jednostkę *użytkownika* dla tej samej jednostki *osoby* . W przeciwieństwie do scenariusza konwersji jednostka *użytkownika* w poprzedniej jednostce *EmpEmployment* nie ma ustawionej wartości null. 

Aby obsłużyć ten scenariusz Revision (opcja 2), dzięki czemu najnowsze dane o zatrudnieniu są wyświetlane na potrzeby przejmowania profilów, można zbiorczo zaktualizować schemat aprowizacji aplikacji, wykonując czynności opisane poniżej:  

1. Otwórz blok Mapowanie atrybutów aplikacji SuccessFactors Provisioning. 
1. Przewiń w dół i kliknij pozycję **Pokaż opcje zaawansowane**.
1. Kliknij link **Przejrzyj tutaj schemat** , aby otworzyć Edytor schematu.   
1. Kliknij link **pobierania** , aby zapisać kopię schematu przed edycją.   
1. W edytorze schematu naciśnij klawisze CTRL-H, aby otworzyć kontrolkę Znajdowanie-zamienianie.
1. W polu tekstowym Znajdź skopiuj i wklej wartość `$.employmentNav.results[0]`
1. W polu tekstowym Zamień skopiuj wartość i wklej ją `$.employmentNav.results[-1:]` . To wyrażenie wykryto zwraca najnowszy rekord *EmpEmployment* .   
1. Kliknij opcję "Zastąp wszystko", aby zaktualizować schemat. 
1. Zapisz schemat. 
1. Powyższy proces aktualizuje wszystkie wyrażenia wykryto w następujący sposób: 
   * Stary wykryto: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Nowy wykryto: `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. Ponownie rozpocznij Inicjowanie obsługi administracyjnej. 

Ta zmiana schematu obsługuje również scenariusz konwersji procesów roboczych. 

### <a name="handling-global-assignment-scenario"></a>Obsługa globalnego scenariusza przypisania

Gdy użytkownik w centrali jest przetwarzany dla przypisania globalnego, SuccessFactors dodaje nową jednostkę *EmpEmployment* i ustawia *ASSIGNMENTCLASS* na "ga". Tworzy również nową jednostkę *użytkownika* . W ten sposób użytkownik ma teraz:
* Jedna   +  Jednostka *użytkownika* EmpEmployment, która odnosi się do przypisywania macierzystego z *assignmentClassem* ustawionym na "St" i 
* Inna   +  Jednostka *użytkownika* EmpEmployment, która odnosi się do przypisania globalnego z *assignmentClassem* ustawionym na "ga"

Aby pobrać atrybuty należące do profilu standardowego przypisania i globalnego przypisania, wykonaj czynności opisane poniżej: 

1. Otwórz blok Mapowanie atrybutów aplikacji SuccessFactors Provisioning. 
1. Przewiń w dół i kliknij pozycję **Pokaż opcje zaawansowane**.
1. Kliknij link **Przejrzyj tutaj schemat** , aby otworzyć Edytor schematu.   
1. Kliknij link **pobierania** , aby zapisać kopię schematu przed edycją.   
1. W edytorze schematu naciśnij klawisze CTRL-H, aby otworzyć kontrolkę Znajdowanie-zamienianie.
1. W polu tekstowym Znajdź skopiuj i wklej wartość `$.employmentNav.results[0]`
1. W polu tekstowym Zamień skopiuj wartość i wklej ją `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
1. Kliknij opcję "Zastąp wszystko", aby zaktualizować schemat. 
1. Zapisz schemat. 
1. Powyższy proces aktualizuje wszystkie wyrażenia wykryto w następujący sposób: 
   * Stary wykryto: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Nowy wykryto: `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. Załaduj ponownie blok mapowania atrybutów aplikacji. 
1. Przewiń w dół i kliknij pozycję **Pokaż opcje zaawansowane**.
1. Kliknij pozycję **Edytuj listę atrybutów dla SuccessFactors**.
1. Dodaj nowe atrybuty, aby pobrać globalne dane przypisywania. Na przykład: Jeśli chcesz pobrać nazwę działu skojarzoną z globalnym profilem przypisania, możesz dodać atrybut *globalAssignmentDepartment* z wyrażeniem wykryto ustawionym na `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
1. Teraz można przepływać obie wartości działu do atrybutów Active Directory lub wybiórczo przepływać wartość przy użyciu mapowania wyrażenia. Przykład: poniższe wyrażenie ustawia wartość atrybutu *działu* AD na *globalAssignmentDepartment* , jeśli jest obecny, w przeciwnym wypadku ustawia wartość *działu* skojarzonego z przypisaniem standardowym. 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. Zapisz mapowanie. 
1. Ponownie rozpocznij Inicjowanie obsługi administracyjnej. 

### <a name="handling-concurrent-jobs-scenario"></a>Scenariusz obsługi zadań współbieżnych

Gdy użytkownik w centrali Employee ma współbieżne/wielokrotne zadania, istnieją dwie *EmpEmployment* i jednostki *użytkownika* z *assignmentClassem* ustawionym na "St". Aby pobrać atrybuty należące do obu zadań, wykonaj czynności opisane poniżej: 

1. Otwórz blok Mapowanie atrybutów aplikacji SuccessFactors Provisioning. 
1. Przewiń w dół i kliknij pozycję **Pokaż opcje zaawansowane**.
1. Kliknij pozycję **Edytuj listę atrybutów dla SuccessFactors**.
1. Załóżmy, że chcesz ściągnąć dział skojarzony z zadaniem 1 i 2. Wstępnie zdefiniowany *Wydział* atrybutów pobiera już wartość działu dla pierwszego zadania. Można zdefiniować nowy atrybut o nazwie *secondJobDepartment* i ustawić wyrażenie wykryto na `$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
1. Teraz można przepływać obie wartości działu do atrybutów Active Directory lub wybiórczo przepływać wartość przy użyciu mapowania wyrażenia. 
1. Zapisz mapowanie. 
1. Ponownie rozpocznij Inicjowanie obsługi administracyjnej. 

## <a name="writeback-scenarios"></a>Scenariusze zapisywania zwrotnego

W tej sekcji omówiono różne scenariusze zapisywania zwrotnego. Zaleca się użycie metod konfiguracji w oparciu o sposób konfiguracji poczty e-mail i numeru telefonu w SuccessFactors.

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>Obsługiwane scenariusze zapisywania zwrotnego telefonu i poczty e-mail 

| \# | Wymagania dotyczące scenariusza | Podstawowy adres e-mail <br> wartość flagi | Telefon służbowy <br> wartość flagi podstawowej | Telefon komórkowy <br> wartość flagi podstawowej | Telefon służbowy <br> mapowanie | Telefon komórkowy <br> mapowanie |
|--|--|--|--|--|--|--|
| 1 | * Ustaw służbowe wiadomości e-mail jako podstawowe. <br> * Nie ustawiaj numerów telefonów. | true | true | fałsz | \[Nie ustawiono\] | \[Nie ustawiono\] | 
| 2 | * W SuccessFactors, służbowe wiadomości e-mail i telefon służbowy są podstawowe <br> * Zawsze przepływaj numer telefonu usługi Azure AD do telefonu służbowego i telefonu komórkowego do telefonów komórkowych. | true | true | fałsz | telephoneNumber | telefon komórkowy | 
| 3 | * W SuccessFactors, służbowe wiadomości e-mail i telefon komórkowy są podstawowe <br> * Zawsze przepływaj numer telefonu usługi Azure AD do telefonu służbowego i do telefonu komórkowego | true | fałsz | true |  telephoneNumber | telefon komórkowy | 
| 4 | * W firmowym adresie e-mail SuccessFactors jest podstawowy <br> * W usłudze Azure AD Sprawdź, czy jest obecny numer telefonu służbowego (jeśli istnieje), a następnie sprawdź, czy numer telefonu komórkowego jest również obecny, Oznacz służbowe numery telefonów jako podstawowe tylko wtedy, gdy nie ma numeru telefonu komórkowego. | true | Użycie mapowania wyrażeń: `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | Użycie mapowania wyrażeń: `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | telefon komórkowy | 
| 5 | * W programie SuccessFactors Business e-mail i telefon służbowy jest podstawowy. <br> * W usłudze Azure AD, jeśli masz dostęp do urządzeń przenośnych, ustaw ją jako telefon służbowy, a w przeciwnym razie użyj numeru telefonu. | true | true | fałsz | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[Nie ustawiono\] | 

* Jeśli nie istnieje mapowanie numeru telefonu w mapowaniu atrybutów zapisu, wówczas tylko poczta e-mail zostanie uwzględniona na stronie zapisywanie zwrotne.
* Podczas nowego dołączania do zatrudnienia w centrum roboczym, służbowy adres e-mail i numer telefonu mogą nie być dostępne. W przypadku ustawienia firmowej poczty e-mail i telefonu służbowego jako podstawowego jest obowiązkowy podczas dołączania, można ustawić fikcyjną wartość dla telefonu służbowego i poczty e-mail podczas tworzenia nowego zatrudnienia, który ostatecznie zostanie zaktualizowany przez aplikację do zapisu.
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>Nieobsługiwane scenariusze zapisywania zwrotnego telefonu i poczty e-mail

* W przypadku pracowników centralnych, podczas dołączania osobistej poczty e-mail i telefonu osobistego jest ustawiony jako podstawowy. Aplikacja do zapisywania zwrotnego nie może przełączać tego ustawienia i ustawiać firmowej poczty e-mail i telefonu służbowego jako podstawowego.
* W programie Employee Central, telefon służbowy jest ustawiony jako podstawowy. Aplikacja do zapisywania zwrotnego nie może zmienić tego ustawienia i ustawić telefon komórkowy jako podstawowy.
* Aplikacja do zapisywania zwrotnego nie może odczytać bieżących ustawień flagi podstawowej i użyć tych samych wartości dla operacji zapisu. Wartości flag skonfigurowanych w mapowaniu atrybutów będą zawsze używane. 

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak skonfigurować SuccessFactors do inicjowania obsługi administracyjnej Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Dowiedz się, jak skonfigurować zapisywanie zwrotne do SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Dowiedz się więcej o obsługiwanych atrybutach SuccessFactors na potrzeby inicjowania obsługi przychodzącej](sap-successfactors-attribute-reference.md)










