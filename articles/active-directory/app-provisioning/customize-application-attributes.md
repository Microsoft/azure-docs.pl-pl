---
title: Dostosowywanie mapowań atrybutów usługi Azure AD | Microsoft Docs
description: Dowiedz się, jakie mapowania atrybutów dla aplikacji SaaS w Azure Active Directory są sposobami ich modyfikacji w celu zaspokajania potrzeb firmy.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: kenwith
ms.openlocfilehash: cac7b169232bb43ba1b1893b59dac81ce4c39c49
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233887"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Dostosowywanie mapowania atrybutów aprowizacji użytkowników dla aplikacji SaaS w Azure Active Directory

Microsoft Azure AD zapewnia pomoc techniczną dla aprowizacji użytkowników do aplikacji SaaS innych firm, takich jak Salesforce, G Suite i innych. W przypadku włączenia aprowizacji użytkowników dla aplikacji SaaS innej firmy Azure Portal kontroluje wartości atrybutów za pomocą mapowań atrybutów.

Istnieje wstępnie skonfigurowany zestaw atrybutów i mapowań atrybutów między obiektami użytkowników usługi Azure AD i obiektami użytkowników aplikacji SaaS. Niektóre aplikacje zarządzają innymi typami obiektów razem z użytkownikami, takimi jak grupy.

Domyślne mapowania atrybutów można dostosować zgodnie z potrzebami biznesowymi. W związku z tym można zmienić lub usunąć istniejące mapowania atrybutów lub utworzyć nowe mapowania atrybutów.

## <a name="editing-user-attribute-mappings"></a>Edytowanie atrybutów użytkownika — mapowania

Wykonaj następujące kroki, aby uzyskać dostęp do funkcji **mapowania** dotyczącej aprowizacji użytkowników:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com).
1. W okienku po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw** . Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji, które zostały dodane z galerii.
1. Wybierz dowolną aplikację, aby załadować okienko zarządzania aplikacjami, w którym można wyświetlać raporty i zarządzać ustawieniami aplikacji.
1. Wybierz opcję **aprowizacji** , aby zarządzać ustawieniami aprowizacji konta użytkownika dla wybranej aplikacji.
1. Rozwiń węzeł **mapowania** , aby wyświetlić i edytować atrybuty użytkownika, które przepływają między usługą Azure AD a aplikacją docelową. Jeśli aplikacja docelowa obsługuje tę funkcję, w tej sekcji można opcjonalnie skonfigurować obsługę administracyjną grup i kont użytkowników.

   ![Używanie mapowań do wyświetlania i edytowania atrybutów użytkownika](./media/customize-application-attributes/21.png)

1. Wybierz konfigurację **mapowań** , aby otworzyć ekran mapowanie powiązanego **atrybutu** . Niektóre mapowania atrybutów są wymagane przez aplikację SaaS do poprawnego działania. W przypadku wymaganych atrybutów funkcja **usuwania** jest niedostępna.

   ![Używanie mapowania atrybutów do konfigurowania mapowań atrybutów dla aplikacji](./media/customize-application-attributes/22.png)

   Na tym zrzucie ekranu można zobaczyć, że atrybut **username** obiektu zarządzanego w usłudze Salesforce jest wypełniony wartością **userPrincipalName** połączonego obiektu Azure Active Directory.

1. Wybierz istniejące **Mapowanie atrybutu** , aby otworzyć ekran **Edytuj atrybut** . W tym miejscu można edytować atrybuty użytkownika, które przepływają między usługą Azure AD a aplikacją docelową.

   ![Edytowanie atrybutów użytkownika przy użyciu atrybutu Edytuj](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Informacje o typach mapowań atrybutów

Mapowania atrybutów umożliwiają kontrolowanie sposobu, w jaki atrybuty są wypełniane w aplikacji SaaS innej firmy.
Obsługiwane są cztery różne typy mapowania:

- **Direct** — atrybut docelowy jest wypełniany wartością atrybutu obiektu połączonego w usłudze Azure AD.
- **Stała** — atrybut docelowy jest wypełniony określonym określonym ciągiem.
- **Expression** — atrybut target jest wypełniany w oparciu o wynik wyrażenia przypominającego skrypt.
  Aby uzyskać więcej informacji, zobacz [Pisanie wyrażeń dla Attribute-Mappings w Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **Brak** — atrybut docelowy nie został zmodyfikowany. Jeśli jednak atrybut target jest kiedykolwiek pusty, zostanie wypełniony wartością domyślną, którą określisz.

Podobnie jak te cztery podstawowe typy, niestandardowe mapowania atrybutów obsługują koncepcję opcjonalnego przypisania wartości **domyślnych** . Przypisanie wartości domyślnej gwarantuje, że atrybut docelowy zostanie wypełniony wartością, jeśli nie istnieje wartość w usłudze Azure AD lub w obiekcie docelowym. Najbardziej typową konfiguracją jest pozostawienie tej pustej.

### <a name="understanding-attribute-mapping-properties"></a>Informacje o właściwościach mapowania atrybutów

W poprzedniej sekcji wprowadzono już do właściwości Typ mapowania atrybutu.
Wraz z tą właściwością mapowania atrybutów obsługują również następujące atrybuty:

- **Atrybut źródłowy** — atrybut użytkownika z systemu źródłowego (przykład: Azure Active Directory).
- **Attribute** — atrybut użytkownika w systemie docelowym (przykład: usługi ServiceNow).
- **Wartość domyślna w przypadku wartości null (opcjonalnie)** — wartość, która zostanie przeniesiona do systemu docelowego, jeśli atrybut źródłowy ma wartość null. Ta wartość zostanie zainicjowana tylko wtedy, gdy użytkownik zostanie utworzony. "Wartość domyślna, gdy wartość null" nie zostanie zainicjowana podczas aktualizowania istniejącego użytkownika. Jeśli na przykład chcesz zainicjować obsługę administracyjną wszystkich istniejących użytkowników w systemie docelowym przy użyciu określonego tytułu zadania (gdy ma on wartość null w systemie źródłowym), możesz użyć następującego [wyrażenia](../app-provisioning/functions-for-customizing-application-data.md): Switch (isexist ([stanowiska]), "DefaultValue", "true", [stanowiska]). Pamiętaj o zamianie "wartości domyślnej" na to, co chcesz udostępnić, gdy wartość jest równa null w systemie źródłowym. 
- **Dopasowywanie obiektów przy użyciu tego atrybutu** — określa, czy mapowanie ma być używane do unikatowego identyfikowania użytkowników między systemami źródłowymi i docelowymi. Zwykle jest ona ustawiana w atrybucie userPrincipalName lub mail w usłudze Azure AD, która jest zwykle mapowana na pole username w aplikacji docelowej.
- **Priorytet dopasowania** — można ustawić wiele pasujących atrybutów. Jeśli istnieje wiele, są one oceniane w kolejności zdefiniowanej przez to pole. Po znalezieniu dopasowania nie są oceniane żadne dalsze pasujące atrybuty. Chociaż można ustawić dowolną liczbę pasujących atrybutów, należy rozważyć, czy atrybuty używane jako pasujące atrybuty są naprawdę unikatowe i muszą być zgodne z atrybutami. Zazwyczaj klienci mają 1 lub 2 pasujące atrybuty w konfiguracji. 
- **Zastosuj to mapowanie**
  - **Zawsze** — Zastosuj to mapowanie zarówno dla akcji tworzenia i aktualizowania użytkownika.
  - **Tylko podczas tworzenia** — Zastosuj to mapowanie tylko w przypadku akcji tworzenia użytkownika.

## <a name="matching-users-in-the-source-and-target--systems"></a>Dopasowanie użytkowników w systemach źródłowych i docelowych
Usługę Azure AD Provisioning można wdrożyć w obu scenariuszach "greenfield" (w przypadku których użytkownicy nie opuszczają w systemie docelowym) i scenariuszy "brownfield" (w których użytkownicy już istnieją w systemie docelowym). Aby zapewnić obsługę obu scenariuszy, usługa aprowizacji używa koncepcji pasujących atrybutów. Zgodne atrybuty umożliwiają określenie sposobu unikatowego identyfikowania użytkownika w źródle i dopasowania go do użytkownika w miejscu docelowym. W ramach planowania wdrożenia Zidentyfikuj atrybut, którego można użyć do unikatowego identyfikowania użytkownika w systemie źródłowym i docelowym. Kwestie do uwagi:

- **Zgodne atrybuty powinny być unikatowe:** Klienci często używają atrybutów, takich jak userPrincipalName, mail lub ID obiektu, jako pasującego atrybutu.
- **Można użyć wielu atrybutów jako pasujących atrybutów:** Można zdefiniować wiele atrybutów do oceny w przypadku dopasowywania użytkowników i kolejności, w której są oceniane (zdefiniowane jako pierwszeństwo w interfejsie użytkownika). Jeśli na przykład zdefiniujesz trzy atrybuty jako pasujące atrybuty, a użytkownik zostanie jednoznacznie dopasowany po przeprowadzeniu oceny pierwszych dwóch atrybutów, usługa nie będzie szacować trzeciego atrybutu. Usługa oceni pasujące atrybuty w podanej kolejności i Zatrzymaj ocenę, gdy zostanie znalezione dopasowanie.  
- **Wartość w źródle i miejscu docelowym nie musi dokładnie pasować:** Wartość w elemencie docelowym może być prostą funkcją wartości w źródle. Tak więc jeden może mieć atrybut emailAddress w źródle i userPrincipalName w elemencie docelowym i pasować przez funkcję atrybutu emailAddress, który zastępuje niektóre znaki niepewną wartością stałą.  
- **Dopasowanie na podstawie kombinacji atrybutów nie jest obsługiwane:** Większość aplikacji nie obsługuje wykonywania zapytań na podstawie dwóch właściwości. W związku z tym nie jest możliwe dopasowanie w oparciu o kombinację atrybutów. Istnieje możliwość oszacowania jednego z pojedynczych właściwości.
- **Wszyscy użytkownicy muszą mieć wartość dla co najmniej jednego zgodnego atrybutu:** Jeśli zdefiniujesz jeden pasujący atrybut, wszyscy użytkownicy muszą mieć wartość dla tego atrybutu w systemie źródłowym. Jeśli na przykład definiujesz wartość userPrincipalName jako pasujący atrybut, wszyscy użytkownicy muszą mieć element userPrincipalName. Jeśli zdefiniujesz wiele pasujących atrybutów (np. extensionAttribute1 i mail), nie wszyscy użytkownicy muszą mieć ten sam pasujący atrybut. Jeden użytkownik może mieć extensionAttribute1, ale nie pocztą e-mail, ale inny użytkownik może korzystać z poczty e-mail, ale nie extensionAttribute1. 
- **Aplikacja docelowa musi obsługiwać filtrowanie na zgodnym atrybucie:** Deweloperzy aplikacji umożliwiają filtrowanie podzestawu atrybutów w INTERFEJSie użytkownika lub grupy użytkowników. W przypadku aplikacji w galerii upewnij się, że domyślne mapowanie atrybutów dotyczy atrybutu, który interfejs API aplikacji docelowej obsługuje filtrowanie. Podczas zmiany domyślnego atrybutu dopasowywania dla aplikacji docelowej Sprawdź dokumentację interfejsu API innej firmy, aby upewnić się, że atrybut może być filtrowany.  

## <a name="editing-group-attribute-mappings"></a>Edytowanie atrybutów grupy — mapowania

Wybrana liczba aplikacji, takich jak usługi ServiceNow, Box i G Suite, umożliwia obsługę administracyjną obiektów grup i obiektów użytkowników. Obiekty grupy mogą zawierać właściwości grupy, takie jak nazwy wyświetlane i aliasy poczty e-mail, wraz z członkami grupy.

![Przykład pokazuje usługi ServiceNow z zainicjowaną grupą i obiektami użytkownika](./media/customize-application-attributes/24.png)

Inicjowanie obsługi grup można opcjonalnie włączyć lub wyłączyć, wybierając mapowanie grupy w obszarze **mapowania** , a ustawienie opcji ma być **włączone** na ekranie **Mapowanie atrybutu** .

Atrybuty, które są obsługiwane jako część obiektów grupy można dostosować w taki sam sposób jak obiekty użytkownika, opisane wcześniej. 

> [!TIP]
> Inicjowanie obsługi obiektów grup (właściwości i członków) jest odrębnym pojęciem [przypisywania grup](../manage-apps/assign-user-or-group-access-portal.md) do aplikacji. Możliwe jest przypisanie grupy do aplikacji, ale tylko udostępnianie obiektów użytkowników zawartych w grupie. Nie jest wymagane Inicjowanie obsługi obiektów w grupach w przypisaniach.

## <a name="editing-the-list-of-supported-attributes"></a>Edytowanie listy obsługiwanych atrybutów

Atrybuty użytkownika obsługiwane przez daną aplikację są wstępnie skonfigurowane. Interfejsy API zarządzania użytkownikami w większości zastosowań nie obsługują odnajdywania schematów. W związku z tym usługa Azure AD Provisioning nie może dynamicznie wygenerować listy obsługiwanych atrybutów przez wykonywanie wywołań do aplikacji.

Niektóre aplikacje obsługują jednak atrybuty niestandardowe, a usługa Azure AD Provisioning może odczytywać i zapisywać w atrybutach niestandardowych. Aby wprowadzić definicje do Azure Portal, zaznacz pole wyboru **Pokaż opcje zaawansowane** u dołu ekranu **mapowania atrybutów** , a następnie wybierz pozycję **Edytuj listę atrybutów dla** swojej aplikacji.

Aplikacje i systemy obsługujące Dostosowywanie listy atrybutów obejmują:

- SalesForce
- ServiceNow
- Dzień roboczy do Active Directory/Workday do Azure Active Directory
- SuccessFactors do Azure Active Directory Active Directory/SuccessFactors
- Azure Active Directory (obsługiwane są[domyślne atrybuty usługi Azure AD interfejs API programu Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity) i niestandardowe rozszerzenia katalogu)
- Aplikacje obsługujące [standard scim 2,0](https://tools.ietf.org/html/rfc7643), w których atrybuty zdefiniowane w [podstawowym schemacie](https://tools.ietf.org/html/rfc7643) muszą być dodane
- W przypadku Azure Active Directory zapisywania zwrotnego w dniach Workday lub SuccessFactors jest obsługiwane aktualizowanie odpowiednich metadanych dla obsługiwanych atrybutów (XPATH i wykryto), ale nie jest to obsługiwane w przypadku dodawania nowych atrybutów Workday lub SuccessFactors poza tymi zawartymi w schemacie domyślnym


> [!NOTE]
> Edytowanie listy obsługiwanych atrybutów jest zalecane tylko dla administratorów, którzy dostosowali schemat aplikacji i systemów, i posiadają pierwszą wiedzę o sposobie definiowania ich atrybutów niestandardowych. Czasami wymaga to znajomości interfejsów API i narzędzi programistycznych udostępnianych przez aplikację lub system.

Podczas edytowania listy obsługiwanych atrybutów są dostępne następujące właściwości:

- **Nazwa** — Nazwa systemowa atrybutu, zgodnie z definicją w schemacie obiektu docelowego.
- **Typ** — typ danych przechowywanych w atrybutach, zgodnie z definicją w schemacie obiektu docelowego, który może być jednym z następujących typów:
  - Atrybut *Binary* zawiera dane binarne.
  - Wartość *logiczna* -Attribute zawiera wartości true lub false.
  - *Właściwość DateTime* -Attribute zawiera ciąg daty.
  - Wartość *całkowita* — atrybut zawiera liczbę całkowitą.
  - Atrybut *Reference* -Attribute zawiera identyfikator, który odwołuje się do wartości przechowywanej w innej tabeli w aplikacji docelowej.
  - Atrybut *String* zawiera ciąg tekstowy.
- **Klucz podstawowy?** -Czy atrybut jest zdefiniowany jako pole klucza podstawowego w schemacie obiektu docelowego.
- **Wymagane?** -Czy atrybut jest wymagany do wypełnienia w aplikacji lub systemie docelowym.
- **Wiele wartości?** -Czy atrybut obsługuje wiele wartości.
- **Dokładnie przypadek?** -Czy wartości atrybutów są oceniane w sposób uwzględniający wielkość liter.
- **Wyrażenie interfejsu API** — nie używaj, o ile nie zostanie to zrobione przez dokumentację określonego łącznika aprowizacji (na przykład Workday).
- **Atrybut obiektu przywoływany** — jeśli jest to atrybut typu referencyjnego, to menu umożliwia wybranie tabeli i atrybutu w aplikacji docelowej zawierającej wartość skojarzoną z atrybutem. Na przykład jeśli masz atrybut o nazwie "Department", którego przechowywana wartość odwołuje się do obiektu w oddzielnej tabeli "działS", wybierz pozycję "Departments.Name". Tabele odwołań i pola identyfikatora podstawowego obsługiwane przez daną aplikację są wstępnie skonfigurowane i obecnie nie można ich edytować za pomocą Azure Portal, ale można je edytować przy użyciu [interfejsu API Microsoft Graph](/graph/api/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Inicjowanie obsługi niestandardowego atrybutu rozszerzenia w aplikacji zgodnej z standard scim
Standard scim RFC definiuje podstawowy schemat użytkownika i grupy, a także umożliwia obsługę rozszerzeń schematu w celu spełnienia wymagań aplikacji. Aby dodać atrybut niestandardowy do aplikacji Standard scim:
   1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com), wybierz pozycję **aplikacje dla przedsiębiorstw** , wybierz aplikację, a następnie wybierz pozycję **Inicjowanie obsługi**.
   2. W obszarze **mapowania** wybierz obiekt (użytkownika lub grupę), dla którego chcesz dodać atrybut niestandardowy.
   3. W dolnej części strony wybierz pozycję **Pokaż opcje zaawansowane**.
   4. Wybierz pozycję **Edytuj listę atrybutów dla elementu nazwa_aplikacji**.
   5. W dolnej części listy atrybutów wprowadź informacje o atrybucie niestandardowym w udostępnionych polach. Następnie wybierz pozycję **Dodaj atrybut**.

W przypadku aplikacji Standard scim nazwa atrybutu musi być zgodna ze wzorcem przedstawionym w poniższym przykładzie. Parametry "CustomExtensionName" i "CustomAttribute" można dostosować zgodnie z wymaganiami aplikacji, na przykład: urn: IETF: params: Standard scim: schematy: Extension: CustomExtensionName: 2.0: User: CustomAttribute 

Te instrukcje dotyczą tylko aplikacji z obsługą Standard scim. Aplikacje, takie jak usługi ServiceNow i Salesforce, nie są zintegrowane z usługą Azure AD przy użyciu Standard scim i dlatego nie wymagają tej konkretnej przestrzeni nazw podczas dodawania atrybutu niestandardowego.

Atrybuty niestandardowe nie mogą być atrybutami referencyjnymi ani atrybutami wielowartościowymi. Niestandardowe atrybuty rozszerzenia wielowartościowego są obecnie obsługiwane tylko w przypadku aplikacji w galerii.  
 
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
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User": {
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


## <a name="provisioning-a-role-to-a-scim-app"></a>Inicjowanie obsługi administracyjnej roli w aplikacji Standard scim
Wykonaj poniższe kroki, aby zainicjować obsługę ról dla użytkownika w aplikacji. Należy zauważyć, że opis poniżej dotyczy niestandardowych aplikacji Standard scim. W przypadku aplikacji galerii, takich jak Salesforce i usługi ServiceNow, należy użyć wstępnie zdefiniowanych mapowań ról. Punktory poniżej opisują sposób przekształcania atrybutu AppRoleAssignments w format oczekiwany przez aplikację.

- Mapowanie appRoleAssignment w usłudze Azure AD do roli w aplikacji wymaga przekształcenia atrybutu przy użyciu [wyrażenia](../app-provisioning/functions-for-customizing-application-data.md). Atrybut appRoleAssignment **nie powinien być mapowany bezpośrednio** do atrybutu roli bez użycia wyrażenia w celu przeanalizowania szczegółów roli. 

- **SingleAppRoleAssignment** 
  - **Kiedy używać:** Użyj wyrażenia SingleAppRoleAssignment, aby zainicjować obsługę pojedynczej roli dla użytkownika i określić rolę podstawową. 
  - **Jak skonfigurować:** Wykonaj kroki opisane powyżej, aby przejść do strony mapowania atrybutów i użyć wyrażenia SingleAppRoleAssignment do mapowania atrybutu role. Istnieją trzy atrybuty roli do wyboru: (role [podstawowa EQ "true"]. Display, role [Primary EQ "true]. Type i Roles [Primary EQ" true "]. Value). Możesz uwzględnić dowolne lub wszystkie atrybuty roli w mapowaniu. Jeśli chcesz dołączyć więcej niż jeden, wystarczy dodać nowe mapowanie i dołączyć je jako atrybut docelowy.  
  
  ![Dodaj SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Rzeczy do rozważenia**
    - Upewnij się, że wiele ról nie jest przypisanych do użytkownika. Nie możemy zagwarantować, która rola zostanie zainicjowana.
    
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
  
- **AppRoleAssignmentsComplex** 
  - **Kiedy używać:** Użyj wyrażenia AppRoleAssignmentsComplex, aby zainicjować obsługę wielu ról dla użytkownika. 
  - **Jak skonfigurować:** Edytuj listę obsługiwanych atrybutów, jak opisano powyżej, aby uwzględnić nowy atrybut dla ról: 
  
    ![Dodawanie ról](./media/customize-application-attributes/add-roles.png)<br>

    Następnie użyj wyrażenia AppRoleAssignmentsComplex, aby zamapować na niestandardowy atrybut roli, jak pokazano na poniższej ilustracji:

    ![Dodaj AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Rzeczy do rozważenia**
    - Wszystkie role będą obsługiwane jako podstawowe = fałszywe.
    - WPIS zawiera typ roli. Żądanie PATCH nie zawiera typu. Pracujemy nad wysłaniem typu w żądaniach POST i PATCH.
    
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

  


## <a name="provisioning-a-multi-value-attribute"></a>Inicjowanie obsługi atrybutu wielowartościowego
Niektóre atrybuty, takie jak numery telefonu i wiadomości e-mail, są atrybutami wielowartościowymi, w których może być konieczne określenie różnych typów numerów telefonów lub wiadomości e-mail. Użyj poniższego wyrażenia dla atrybutów wielowartościowych. Umożliwia określenie typu atrybutu i mapy do odpowiedniego atrybutu użytkownika usługi Azure AD dla tej wartości. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* numer telefonu [typ EQ "Fax"]. wartość

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

Jeśli trzeba zacząć od początku i zresetować istniejące mapowania z powrotem do stanu domyślnego, można zaznaczyć pole wyboru **Przywróć domyślne mapowania** i zapisać konfigurację. W ten sposób ustawia się wszystkie mapowania i filtry zakresu, tak jakby aplikacja została dodana do dzierżawy usługi Azure AD z galerii aplikacji.

Wybranie tej opcji spowoduje skuteczną ponowną synchronizację wszystkich użytkowników, gdy usługa aprowizacji jest uruchomiona.

> [!IMPORTANT]
> Przed wywołaniem tej opcji zdecydowanie zalecamy ustawienie **stanu aprowizacji** na **wyłączony** .

## <a name="what-you-should-know"></a>Co należy wiedzieć

- Microsoft Azure AD zapewnia wydajną implementację procesu synchronizacji. W środowisku zainicjowanym tylko obiekty wymagające aktualizacji są przetwarzane podczas cyklu synchronizacji.
- Aktualizacja mapowań atrybutów ma wpływ na wydajność cyklu synchronizacji. Aktualizacja konfiguracji mapowania atrybutów wymaga ponownego oszacowania wszystkich zarządzanych obiektów.
- Zalecanym najlepszym rozwiązaniem jest pozostawienie w minimalnym zakresie liczby kolejnych zmian mapowań atrybutów.
- Dodawanie atrybutu Photo do aprowizacji do aplikacji nie jest obecnie obsługiwane, ponieważ nie można określić formatu synchronizacji zdjęcia. Możesz zażądać funkcji na [głos użytkownika](https://feedback.azure.com/forums/169401-azure-active-directory)
- Atrybut IsSoftDeleted jest często częścią domyślnych mapowań aplikacji. IsSoftdeleted może być spełniony w jednym z czterech scenariuszy (użytkownik jest poza zakresem, ponieważ nie jest przypisany do aplikacji, użytkownik jest poza zakresem ze względu na niespełnienie filtru określania zakresu, użytkownik został usunięty z usługi Azure AD lub właściwość AccountEnabled jest ustawiona na wartość false dla użytkownika). Nie zaleca się usuwania atrybutu IsSoftDeleted z mapowań atrybutów.
- Usługa Azure AD Provisioning nie obsługuje inicjowania obsługi wartości null.
- Klucz podstawowy, zazwyczaj "ID", nie powinien być uwzględniany jako atrybut docelowy w mapowaniu atrybutów. 
- Atrybut role zwykle musi być mapowany przy użyciu wyrażenia, a nie bezpośredniego mapowania. Więcej szczegółów dotyczących mapowania ról znajduje się w powyższej sekcji. 
- Chociaż można wyłączyć grupy z mapowań, wyłączenie użytkowników nie jest obsługiwane. 

## <a name="next-steps"></a>Następne kroki

- [Automatyzacja aprowizacji użytkowników/anulowania obsługi administracyjnej w aplikacjach SaaS](user-provisioning.md)
- [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
- [Filtry zakresu dla aprowizacji użytkowników](define-conditional-rules-for-provisioning-user-accounts.md)
- [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](use-scim-to-provision-users-and-groups.md)
- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
