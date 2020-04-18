---
title: Dostosowywanie mapowania atrybutów usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jakie mapowania atrybutów dla aplikacji SaaS w usłudze Azure Active Directory umożliwiają ich modyfikowanie w celu zaspokojenia potrzeb biznesowych.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfee19e9cfd1def71ebad82c2210ffc10146c896
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639739"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Dostosowywanie mapowania atrybutów inicjowania obsługi administracyjnej użytkowników dla aplikacji SaaS w usłudze Azure Active Directory

Usługa Microsoft Azure AD zapewnia obsługę inicjowania obsługi administracyjnej użytkowników do aplikacji SaaS innych firm, takich jak Salesforce, G Suite i innych. Jeśli włączysz inicjowanie obsługi administracyjnej użytkowników dla aplikacji SaaS innej firmy, portal Azure kontroluje swoje wartości atrybutów za pomocą mapowań atrybutów.

Istnieje wstępnie skonfigurowany zestaw atrybutów i mapowań atrybutów między obiektami użytkowników usługi Azure AD a obiektami użytkownika każdej aplikacji SaaS. Niektóre aplikacje zarządzają innymi typami obiektów wraz z użytkownikami, takimi jak Grupy.

Domyślne mapowania atrybutów można dostosować do potrzeb firmy. Można więc zmienić lub usunąć istniejące mapowania atrybutów lub utworzyć nowe mapowania atrybutów.

## <a name="editing-user-attribute-mappings"></a>Edytowanie mapowań atrybutów użytkownika

Wykonaj następujące kroki, aby uzyskać dostęp do funkcji **Mapowania** inicjowania obsługi administracyjnej użytkownika:

1. Zaloguj się do [portalu usługi Azure Active Directory](https://aad.portal.azure.com).
1. Wybierz **aplikacje enterprise** z lewego okienka. Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji dodanych z galerii.
1. Wybierz dowolną aplikację, aby załadować okienko zarządzania aplikacjami, w którym możesz wyświetlać raporty i zarządzać ustawieniami aplikacji.
1. Wybierz **pozycję Inicjowanie obsługi administracyjnej,** aby zarządzać ustawieniami inicjowania obsługi administracyjnej konta użytkownika dla wybranej aplikacji.
1. Rozwiń **mapowania,** aby wyświetlić i edytować atrybuty użytkownika, które przepływają między usługą Azure AD a aplikacją docelową. Jeśli aplikacja docelowa obsługuje go, ta sekcja umożliwia opcjonalnie skonfigurować inicjowanie obsługi administracyjnej grup i kont użytkowników.

   ![Wyświetlanie i edytowanie atrybutów użytkowników za pomocą mapowań](./media/customize-application-attributes/21.png)

1. Wybierz konfigurację **Mapowania,** aby otworzyć powiązany ekran **Mapowania atrybutów.** Niektóre mapowania atrybutów są wymagane przez aplikację SaaS do poprawnego działania. W przypadku wymaganych atrybutów funkcja **Usuń** jest niedostępna.

   ![Konfigurowanie mapowania atrybutów dla aplikacji za pomocą mapowania atrybutów](./media/customize-application-attributes/22.png)

   Na tym zrzucie ekranu widać, że atrybut **Nazwa użytkownika** obiektu zarządzanego w salesforce jest wypełniona wartością **userPrincipalName** połączonego obiektu usługi Azure Active Directory.

1. Wybierz istniejące **mapowanie atrybutów,** aby otworzyć ekran **Edytowanie atrybutu.** W tym miejscu można edytować atrybuty użytkownika, które przepływają między usługą Azure AD a aplikacją docelową.

   ![Edytowanie atrybutów za pomocą atrybutu Edytowania](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Opis typów mapowania atrybutów

Za pomocą mapowań atrybutów można kontrolować sposób wypełniania atrybutów w aplikacji SaaS innej firmy.
Obsługiwane są cztery różne typy mapowania:

- **Bezpośredni** — atrybut docelowy jest wypełniany wartością atrybutu połączonego obiektu w usłudze Azure AD.
- **Stała** — atrybut docelowy jest wypełniany określonym ciągiem określonym.
- **Wyrażenie** — atrybut docelowy jest wypełniany na podstawie wyniku wyrażenia podobnego do skryptu.
  Aby uzyskać więcej informacji, zobacz [Pisanie wyrażeń dla mapowań atrybutów w usłudze Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **Brak** — atrybut docelowy pozostaje niezmodyfikowany. Jeśli jednak atrybut docelowy jest zawsze pusty, jest wypełniany wartością domyślną określoną.

Wraz z tymi czterema typami podstawowymi niestandardowe mapowania atrybutów obsługują koncepcję opcjonalnego **przypisania** wartości domyślnej. Domyślne przypisanie wartości zapewnia, że atrybut docelowy jest wypełniany wartością, jeśli nie ma wartości w usłudze Azure AD lub w obiekcie docelowym. Najczęstszą konfiguracją jest pozostawienie tego pustego pola.

### <a name="understanding-attribute-mapping-properties"></a>Opis właściwości mapowania atrybutów

W poprzedniej sekcji zostały już wprowadzone do właściwości typu mapowania atrybutów.
Wraz z tą właściwością mapowania atrybutów obsługują również następujące atrybuty:

- **Atrybut source** — atrybut użytkownika z systemu źródłowego (np. usługa Azure Active Directory).
- **Atrybut docelowy** — atrybut użytkownika w systemie docelowym (przykład: ServiceNow).
- **Wartość domyślna, jeśli null (opcjonalnie)** — wartość, która zostanie przekazana do systemu docelowego, jeśli atrybut źródłowy ma wartość null. Ta wartość będzie aprowizowana tylko wtedy, gdy użytkownik jest tworzony. "Wartość domyślna, gdy null" nie zostaną zainicjowane podczas aktualizowania istniejącego użytkownika. Jeśli na przykład chcesz aprowizować wszystkich istniejących użytkowników w systemie docelowym z określonym tytułem zadania (gdy jest null w systemie źródłowym), można użyć następującego [wyrażenia:](../app-provisioning/functions-for-customizing-application-data.md)Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Upewnij się, aby zastąpić "Wartość domyślna" z tym, co chcesz aprowizować, gdy null w systemie źródłowym. 
- **Dopasuj obiekty przy użyciu tego atrybutu** — czy to mapowanie powinno służyć do jednoznacznej identyfikacji użytkowników między systemami źródłowymi i docelowymi. Zazwyczaj jest ustawiona na userPrincipalName lub atrybut poczty w usłudze Azure AD, który jest zazwyczaj mapowane do pola nazwy użytkownika w aplikacji docelowej.
- **Pierwszeństwo dopasowania** — można ustawić wiele pasujących atrybutów. Gdy istnieje wiele, są one oceniane w kolejności zdefiniowanej przez to pole. Jak tylko zostanie znaleziony dopasowania, nie dalsze pasujące atrybuty są oceniane.
- **Zastosuj to mapowanie**
  - **Zawsze** — zastosuj to mapowanie zarówno dla tworzenia użytkownika, jak i akcji aktualizacji.
  - **Tylko podczas tworzenia** — zastosuj to mapowanie tylko dla akcji tworzenia użytkownika.

## <a name="matching-users-in-the-source-and-target--systems"></a>Dopasowywanie użytkowników w systemach źródłowych i docelowych
Usługę inicjowania obsługi administracyjnej usługi Azure AD można wdrożyć zarówno w scenariuszach "greenfield" (w których użytkownicy nie wyjdą z systemu docelowego) i scenariuszy "brownfield" (w których użytkownicy już istnieją w systemie docelowym). Aby obsługiwać oba scenariusze, usługa inicjowania obsługi administracyjnej używa koncepcji pasujących atrybutów. Pasujące atrybuty pozwalają określić, jak jednoznacznie zidentyfikować użytkownika w źródle i dopasować użytkownika w miejscu docelowym. W ramach planowania wdrożenia zidentyfikuj atrybut, który może służyć do jednoznacznej identyfikacji użytkownika w systemach źródłowych i docelowych. Warto pamiętać:

- **Pasujące atrybuty powinny być unikatowe:** Klienci często używają atrybutów, takich jak userPrincipalName, mail lub identyfikator obiektu jako pasującego atrybutu.
- **Wiele atrybutów może być użytych jako pasujących atrybutów:** Można zdefiniować wiele atrybutów, które mają być oceniane podczas dopasowywania użytkowników i kolejność, w jakiej są one oceniane (zdefiniowane jako pasowanie pierwszeństwo w interfejsie użytkownika). Jeśli na przykład zdefiniujesz trzy atrybuty jako pasujące atrybuty, a użytkownik jest unikatowo dopasowany po ocenie dwóch pierwszych atrybutów, usługa nie oceni trzeciego atrybutu. Usługa oceni pasujące atrybuty w określonej kolejności i zatrzyma ocenę po znalezieniu dopasowania.  
- **Wartość w źródle i celu nie muszą być dokładnie zgodne:** Wartość w docelowych może być kilka prostych funkcji wartości w źródle. Tak, można mieć emailAddress atrybut w źródle i userPrincipalName w docelowych i dopasować przez funkcję atrybutu emailAddress, który zastępuje niektóre znaki z pewną stałą wartością.  
- **Dopasowanie na podstawie kombinacji atrybutów nie jest obsługiwane:** Większość aplikacji nie obsługuje zapytań na podstawie dwóch właściwości. W związku z tym nie jest możliwe dopasowanie na podstawie kombinacji atrybutów. Możliwe jest oszacowanie pojedynczych właściwości po drugim.
- **Wszyscy użytkownicy muszą mieć wartość dla co najmniej jednego pasującego atrybutu:** Jeśli zdefiniujesz jeden pasujący atrybut, wszyscy użytkownicy muszą mieć wartość dla tego atrybutu w systemie źródłowym. Jeśli na przykład zdefiniujesz userPrincipalName jako pasujący atrybut, wszyscy użytkownicy muszą mieć userPrincipalName. Jeśli zdefiniujesz wiele pasujących atrybutów (np. extensionAttribute1 i mail), nie wszyscy użytkownicy muszą mieć ten sam pasujący atrybut. Jeden użytkownik może mieć extensionAttribute1 ale nie poczta, podczas gdy inny użytkownik może mieć pocztę, ale bez rozszerzeniaAttribute1. 
- **Aplikacja docelowa musi obsługiwać filtrowanie pasującego atrybutu:** Deweloperzy aplikacji zezwalają na filtrowanie podzbioru atrybutów w interfejsie API użytkownika lub grupy. W przypadku aplikacji w galerii firma Wew. Podczas zmiany domyślnego atrybutu dopasowania dla aplikacji docelowej, sprawdź dokumentację interfejsu API innej firmy, aby upewnić się, że atrybut może być filtrowany.  

## <a name="editing-group-attribute-mappings"></a>Edytowanie mapowań atrybutów grupy

Wybrana liczba aplikacji, takich jak ServiceNow, Box i G Suite, obsługuje możliwość aprowizowania obiektów grupy i obiektów użytkownika. Obiekty grupy mogą zawierać właściwości grupy, takie jak nazwy wyświetlane i aliasy e-mail, wraz z członkami grupy.

![Przykład pokazuje ServiceNow z aprowizowanymi obiektami Grupa i Użytkownik](./media/customize-application-attributes/24.png)

Inicjowanie obsługi administracyjnej grupy można opcjonalnie włączyć lub wyłączyć, wybierając mapowanie grup w obszarze Mapowania i **ustawiając opcję Włącz opcję,** którą chcesz na ekranie **Mapowanie atrybutów.** **Mappings**

Atrybuty aprowizacji jako część Group obiektów można dostosować w taki sam sposób jak User obiektów, opisane wcześniej. 

> [!TIP]
> Inicjowanie obsługi administracyjnej obiektów grupy (właściwości i członków) jest odrębną koncepcją od [przypisywania grup](../manage-apps/assign-user-or-group-access-portal.md) do aplikacji. Istnieje możliwość przypisania grupy do aplikacji, ale tylko aprowizować obiekty użytkownika zawarte w grupie. Inicjowanie obsługi administracyjnej obiektów pełnej grupy nie jest wymagane do używania grup w przydziałach.

## <a name="editing-the-list-of-supported-attributes"></a>Edytowanie listy obsługiwanych atrybutów

Atrybuty użytkownika obsługiwane dla danej aplikacji są wstępnie skonfigurowane. Większość interfejsów API zarządzania użytkownikami większości aplikacji nie obsługuje odnajdowania schematu. Dlatego usługa inicjowania obsługi administracyjnej usługi Azure AD nie jest w stanie dynamicznie generować listę obsługiwanych atrybutów przez nawiązywanie wywołań do aplikacji.

Jednak niektóre aplikacje obsługują atrybuty niestandardowe, a usługa inicjowania obsługi administracyjnej usługi Azure AD może odczytywać i zapisywać atrybuty niestandardowe. Aby wprowadzić ich definicje w witrynie Azure Portal, zaznacz pole wyboru **Pokaż opcje zaawansowane** u dołu ekranu **Mapowanie atrybutów,** a następnie wybierz **pozycję Edytuj listę atrybutów dla** aplikacji.

Aplikacje i systemy obsługujące dostosowywanie listy atrybutów obejmują:

- SalesForce
- ServiceNow
- Workday
- Usługa Azure Active Directory (obsługiwane są[odwołania do interfejsu API interfejsu MICROSOFT Graph REST w wersji 1.0](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) i niestandardowe rozszerzenia katalogów)
- Aplikacje obsługujące [scim 2.0](https://tools.ietf.org/html/rfc7643), gdzie należy dodać atrybuty zdefiniowane w [schemacie podstawowym](https://tools.ietf.org/html/rfc7643)

> [!NOTE]
> Edytowanie listy obsługiwanych atrybutów jest zalecane tylko dla administratorów, którzy dostosowali schemat swoich aplikacji i systemów i mają z pierwszej ręki wiedzę na temat sposobu definiowania ich atrybutów niestandardowych. Czasami wymaga to znajomości interfejsów API i narzędzi deweloperskich dostarczonych przez aplikację lub system.

Podczas edytowania listy obsługiwanych atrybutów dostępne są następujące właściwości:

- **Nazwa** — nazwa systemowa atrybutu, zgodnie z definicją w schemacie obiektu docelowego.
- **Typ** — typ danych, które przechowuje atrybut, zgodnie z definicją w schemacie obiektu docelowego, który może być jednym z następujących typów:
  - *Binary* - Atrybut zawiera dane binarne.
  - *Wartość logiczna* — atrybut zawiera wartość Prawda lub Fałsz.
  - *DateTime* — atrybut zawiera ciąg daty.
  - *Integer* - Atrybut zawiera całkowitą.
  - *Odwołanie* — atrybut zawiera identyfikator, który odwołuje się do wartości przechowywanej w innej tabeli w aplikacji docelowej.
  - *Ciąg* - Atrybut zawiera ciąg tekstowy.
- **Klucz podstawowy?** - Czy atrybut jest zdefiniowany jako pole klucza podstawowego w schemacie obiektu docelowego.
- **Wymagane?** - Czy atrybut jest wymagane do wypełniania w aplikacji docelowej lub systemu.
- **Wiele wartości?** - Czy atrybut obsługuje wiele wartości.
- **Dokładny przypadek?** - Czy wartości atrybutów są oceniane w sposób uwzględniający wielkość liter.
- **Wyrażenie interfejsu API** — nie należy używać, chyba że jest to poinstruowane przez dokumentację dla określonego łącznika inicjowania obsługi administracyjnej (takiego jak Workday).
- **Atrybut obiektu, do którego istnieje** odwołanie — jeśli jest to atrybut typu odwołania, to to menu umożliwia wybranie tabeli i atrybutu w aplikacji docelowej zawierającej wartość skojarzoną z atrybutem. Na przykład jeśli masz atrybut o nazwie "Dział", którego przechowywana wartość odwołuje się do obiektu w osobnej tabeli "Działy", należy wybrać "Departments.Name". Tabele odwołań i podstawowe pola identyfikatorów obsługiwane dla danej aplikacji są wstępnie skonfigurowane i obecnie nie można ich edytować za pomocą witryny Azure Portal, ale można je edytować za pomocą [interfejsu API programu Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Inicjowanie obsługi administracyjnej niestandardowego atrybutu rozszerzenia do aplikacji zgodnej ze scm
SCIM RFC definiuje podstawowy schemat użytkownika i grupy, a jednocześnie umożliwia rozszerzenia schematu, aby spełnić potrzeby aplikacji. Aby dodać atrybut niestandardowy do aplikacji SCIM:
   1. Zaloguj się do [portalu usługi Azure Active Directory](https://aad.portal.azure.com), wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz aplikację, a następnie wybierz pozycję **Inicjowanie obsługi administracyjnej**.
   2. W obszarze **Mapowania**wybierz obiekt (użytkownika lub grupę), dla którego chcesz dodać atrybut niestandardowy.
   3. U dołu strony wybierz pozycję **Pokaż opcje zaawansowane**.
   4. Wybierz **pozycję Edytuj listę atrybutów dla Aplikacji AppName**.
   5. U dołu listy atrybutów wprowadź informacje o atrybucie niestandardowym w podanych polach. Następnie wybierz pozycję **Dodaj atrybut**.

W przypadku aplikacji SCIM nazwa atrybutu musi być zgodna ze wzorcem pokazanym w poniższym przykładzie. "CustomExtensionName" i "CustomAttribute" można dostosować zgodnie z wymaganiami aplikacji, na przykład:  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 
 * urn:ietf:params:scim:schemas:extension:2.0:CustomExtensionName:CustomAttribute  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User.CustomAttributeName:value

Te instrukcje mają zastosowanie tylko do aplikacji z obsługą SCIM. Aplikacje takie jak ServiceNow i Salesforce nie są zintegrowane z usługą Azure AD przy użyciu narzędzia SCIM i dlatego nie wymagają tego określonego obszaru nazw podczas dodawania atrybutu niestandardowego.

Atrybuty niestandardowe nie mogą być atrybutami referencyjnymi ani atrybutami wielowartościowymi. Niestandardowe atrybuty rozszerzenia wielu wartości są obecnie obsługiwane tylko dla aplikacji w galerii.  
 
**Przykładowa reprezentacja użytkownika z atrybutem rozszerzenia:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Inicjowanie obsługi administracyjnej roli aplikacji SCIM
Skorzystaj z poniższych czynności, aby aprowizować role dla użytkownika do aplikacji. Należy zauważyć, że poniższy opis jest specyficzny dla niestandardowych aplikacji SCIM. W przypadku aplikacji galerii, takich jak Salesforce i ServiceNow, użyj wstępnie zdefiniowanych mapowań ról. Punktory poniżej opisują sposób przekształcania Atrybutu AppRoleAssignments do formatu, jakiego oczekuje aplikacja.

- Mapowanie appRoleAssignment w usłudze Azure AD do roli w aplikacji wymaga przekształcenia atrybutu przy użyciu [wyrażenia.](../app-provisioning/functions-for-customizing-application-data.md) Atrybut appRoleAssignment **nie powinien być mapowany bezpośrednio** do atrybutu roli bez użycia wyrażenia do analizowania szczegółów roli. 

- **Znak SingleAppRoleAssignment** 
  - **Kiedy stosować:** Użyj wyrażenia SingleAppRoleAssignment, aby aprowizować pojedynczą rolę dla użytkownika i określić rolę podstawową. 
  - **Jak skonfigurować:** Użyj kroków opisanych powyżej, aby przejść do strony mapowania atrybutów i użyć wyrażenia SingleAppRoleAssignment do mapowania atrybutu role. Istnieją trzy atrybuty roli do wyboru: (role[primary eq "True"].display, roles[primary eq "True].type i roles[primary eq "True"].value). Można dołączyć dowolne lub wszystkie atrybuty roli w mapowaniach. Jeśli chcesz dołączyć więcej niż jeden, wystarczy dodać nowe mapowanie i uwzględnić go jako atrybut docelowy.  
  
  ![Dodaj znak SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Rzeczy do rozważenia**
    - Upewnij się, że wiele ról nie są przypisane do użytkownika. Nie możemy zagwarantować, która rola zostanie udostępniona.
    
  - **Przykładowe dane wyjściowe** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsKomplex** 
  - **Kiedy stosować:** Użyj AppRoleAssignmentsComplex wyrażenie do aprowizowania wielu ról dla użytkownika. 
  - **Jak skonfigurować:** Edytuj listę obsługiwanych atrybutów, jak opisano powyżej, aby dołączyć nowy atrybut ról: 
  
    ![Dodawanie ról](./media/customize-application-attributes/add-roles.png)<br>

    Następnie użyj AppRoleAssignmentsComplex wyrażenie do mapowania do atrybutu roli niestandardowej, jak pokazano na obrazku poniżej:

    ![Dodawanie approleassignmentsKomplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Rzeczy do rozważenia**
    - Wszystkie role będą aprowizacji jako podstawowe = false.
    - POST zawiera typ roli. Żądanie PATCH nie zawiera typu. Pracujemy nad wysłaniem typu zarówno w żądaniach POST, jak i PATCH.
    
  - **Przykładowe dane wyjściowe** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Inicjowanie obsługi administracyjnej atrybutu wielowartowiowego
Niektóre atrybuty, takie jak numery telefonów i wiadomości e-mail, są atrybutami wielowartościowymi, w których może być konieczne określenie różnych typów numerów telefonów lub wiadomości e-mail. Użyj poniższego wyrażenia dla atrybutów wielowartościowych. Umożliwia określenie typu atrybutu i mapowanie tego do odpowiedniego atrybutu użytkownika usługi Azure AD dla wartości. 

* phoneNumbers[wpisz eq "praca"].wartość
* phoneNumbers[wpisz eq "mobile"].wartość
* phoneNumbers[wpisz eq "fax"].wartość

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Przywracanie domyślnych atrybutów i mapowań atrybutów

Jeśli trzeba rozpocząć od nowa i zresetować istniejące mapowania z powrotem do stanu domyślnego, można zaznaczyć pole wyboru **Przywróć domyślne mapowania** i zapisać konfigurację. W ten sposób ustawia wszystkie mapowania i filtry zakresu tak, jakby aplikacja została właśnie dodana do dzierżawy usługi Azure AD z galerii aplikacji.

Wybranie tej opcji skutecznie wymusi ponowną synchronizację wszystkich użytkowników podczas uruchamiania usługi inicjowania obsługi administracyjnej.

> [!IMPORTANT]
> Zdecydowanie zaleca się, aby **stan inicjowania obsługi administracyjnej** został ustawiony na **Wyłączone** przed wywołaniem tej opcji.

## <a name="what-you-should-know"></a>Co należy wiedzieć

- Usługa Microsoft Azure AD zapewnia wydajną implementację procesu synchronizacji. W zainicjowanym środowisku tylko obiekty wymagające aktualizacji są przetwarzane podczas cyklu synchronizacji.
- Aktualizowanie mapowań atrybutów ma wpływ na wydajność cyklu synchronizacji. Aktualizacja konfiguracji mapowania atrybutów wymaga ponownej oceny wszystkich obiektów zarządzanych.
- Zalecaną najlepszą praktyką jest utrzymanie liczby kolejnych zmian w mapowaniach atrybutów na minimalnym poziomie.
- Dodawanie atrybutu zdjęcia do aprowizowania do aplikacji nie jest obecnie obsługiwane, ponieważ nie można określić formatu synchronizacji zdjęcia. Możesz poprosić o tę funkcję w [udź.u](https://feedback.azure.com/forums/169401-azure-active-directory)
- Atrybut IsSoftDeleted jest często częścią mapowania domyślne dla aplikacji. IsSoftdeleted może być true w jednym z czterech scenariuszy (użytkownik jest poza zakresem ze względu na nieprzypisane z aplikacji, użytkownik jest poza zakresem ze względu na nie spełnia filtr zakresu, użytkownik został usunięty nietrwałe w usłudze Azure AD lub właściwości AccountEnabled jest ustawiona na false na użytkownika). 
- Usługa inicjowania obsługi administracyjnej usługi Azure AD nie obsługuje inicjowania obsługi administracyjnej wartości null

## <a name="next-steps"></a>Następne kroki

- [Automatyzacja inicjowania obsługi administracyjnej/anulowania obsługi administracyjnej aplikacji SaaS](user-provisioning.md)
- [Pisanie wyrażeń dla mapowań atrybutów](../app-provisioning/functions-for-customizing-application-data.md)
- [Filtry zakresu do inicjowania obsługi administracyjnej użytkowników](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](use-scim-to-provision-users-and-groups.md)
- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
