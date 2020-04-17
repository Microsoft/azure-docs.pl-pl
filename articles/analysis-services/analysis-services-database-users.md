---
title: Zarządzanie rolami i użytkownikami baz danych w usługach Azure Analysis Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak zarządzać rolami bazy danych i użytkownikami na serwerze usług Analysis Services na platformie Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b7e3cc2b9d35eafcb875efa167821a8e9ad80146
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454207"
---
# <a name="manage-database-roles-and-users"></a>Zarządzanie rolami i użytkownikami bazy danych

Na poziomie modelowej bazy danych wszyscy użytkownicy muszą należeć do roli. Role definiują użytkowników z określonymi uprawnieniami dla bazy danych modelu. Każdy użytkownik lub grupa zabezpieczeń dodana do roli musi mieć konto w dzierżawie usługi Azure AD w tej samej subskrypcji co serwer. 

Sposób definiowania ról różni się w zależności od używanego narzędzia, ale efekt jest taki sam.

Uprawnienia do roli obejmują:
*  **Administrator** — użytkownicy mają pełne uprawnienia do bazy danych. Role bazy danych z uprawnieniami administratora różnią się od administratorów serwera.
*  **Proces** — użytkownicy mogą łączyć się z operacjami procesu i wykonywać je w bazie danych oraz analizować dane bazy danych modelu.
*  **Odczyt** — użytkownicy mogą używać aplikacji klienckiej do łączenia się i analizowania danych bazy danych modelu.

Podczas tworzenia projektu modelu tabelaryczne, można utworzyć role i dodać użytkowników lub grup do tych ról przy użyciu Menedżera ról w programie Visual Studio z analysis services projektów. Po wdrożeniu na serwerze użyj polecenia cmdlet programu SQL Server Management Studio (SSMS), [Analysis Services PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference)lub [Języka skryptów modelu tabelaryjskiego](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (TMSL), aby dodać lub usunąć role i członków użytkownika.

Podczas dodawania **grupy** `obj:groupid@tenantid`zabezpieczeń użyj .

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Aby dodać role i użytkowników w programie Visual Studio lub zarządzać nimi  
  
1.  W **Eksploratorze modeli tabelaryczne**kliknij prawym przyciskiem myszy **pozycję Role**.  
  
2.  W **Menedżerze ról**kliknij pozycję **Nowy**.  
  
3.  Wpisz nazwę roli.  
  
     Domyślnie nazwa roli domyślnej jest stopniowo numerowana dla każdej nowej roli. Zaleca się wpisanie nazwy, która wyraźnie identyfikuje typ elementu członkowskiego, na przykład menedżerów finansowych lub specjalistów od zasobów ludzkich.  
  
4.  Wybierz jedno z następujących uprawnień:  
  
    |Uprawnienie|Opis|  
    |----------------|-----------------|  
    |**Brak**|Członkowie nie mogą odczytać lub zmodyfikować schematu modelu i nie mogą wysyłać zapytań o dane.|  
    |**Odczyt**|Członkowie mogą wysyłać zapytania do danych (na podstawie filtrów wierszy), ale nie mogą modyfikować schematu modelu.|  
    |**Odczyt i proces**|Członkowie mogą wysyłać zapytania do danych (na podstawie filtrów na poziomie wiersza) i uruchamiać operacje Proces i Przetwarzanie wszystkich, ale nie mogą modyfikować schematu modelu.|  
    |**Proces**|Członkowie mogą uruchamiać operacje Proces i Przetwarzanie wszystkich. Nie można odczytać lub zmodyfikować schematu modelu i nie można zbadać danych.|  
    |**Administratora**|Członkowie mogą modyfikować schemat modelu i badać wszystkie dane.|   
  
5.  Jeśli wywoływana rola ma uprawnienie Odczyt lub Odczyt i proces, możesz dodać filtry wierszy przy użyciu formuły języka DAX. Kliknij kartę **Filtry wierszy,** a następnie wybierz tabelę, a następnie kliknij pole **Filtr języka DAX,** a następnie wpisz formułę języka DAX.
  
6.  Kliknij pozycję **Członkowie** > **Dodaj elementy zewnętrzne**.  
  
8.  W **obszarze Dodaj członka zewnętrznego**wprowadź użytkowników lub grupy w dzierżawie usługi Azure AD według adresu e-mail. Po kliknięciu przycisku OK i zamknięciu Menedżera ról role i elementy członkowskie roli są wyświetlane w Eksploratorze modelu tabelaryjskiego. 
 
     ![Role i użytkownicy w Eksploratorze modeli tabelaryjskich](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Wdrażanie na serwerze usług Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Aby dodać role i użytkowników w ssms lub zarządzać nimi

Aby dodać role i użytkowników do wdrożonej bazy danych modelu, musisz być połączony z serwerem jako administrator serwera lub już w roli bazy danych z uprawnieniami administratora.

1. W obszarze Obiekt Exporer kliknij prawym przyciskiem myszy **pozycję Role** > **Nowa rola**.

2. W **polu Utwórz rolę**wprowadź nazwę i opis roli.

3. Wybierz uprawnienie.

   |Uprawnienie|Opis|  
   |----------------|-----------------|  
   |**Pełna kontrola (administrator)**|Członkowie mogą modyfikować schemat modelu, proces i może wysyłać zapytania do wszystkich danych.| 
   |**Przetwarzanie bazy danych**|Członkowie mogą uruchamiać operacje Proces i Przetwarzanie wszystkich. Nie można zmodyfikować schematu modelu i nie można zbadać danych.|  
   |**Odczyt**|Członkowie mogą wysyłać zapytania do danych (na podstawie filtrów wierszy), ale nie mogą modyfikować schematu modelu.|  
  
4. Kliknij **pozycję Członkostwo**, a następnie wprowadź użytkownika lub grupę w dzierżawie usługi Azure AD według adresu e-mail.

     ![Dodawanie użytkownika](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Jeśli wywoływana rola ma uprawnienie Odczyt, możesz dodać filtry wierszy przy użyciu formuły języka DAX. Kliknij **pozycję Filtry wierszy**, zaznacz tabelę, a następnie wpisz formułę języka DAX w polu **Filtr języka DAX.** 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Aby dodać role i użytkowników przy użyciu skryptu TMSL

Skrypt TMSL można uruchomić w oknie XMLA w programie SSMS lub za pomocą programu PowerShell. Użyj polecenia [CreateOrReplace](https://docs.microsoft.com/analysis-services/tmsl/createorreplace-command-tmsl) i obiektu [Roles.](https://docs.microsoft.com/analysis-services/tmsl/roles-object-tmsl)

**Przykładowy skrypt TMSL**

W tym przykładzie użytkownik zewnętrzny B2B i grupa są dodawane do roli Analityk z uprawnieniami odczytu dla bazy danych SalesBI. Zarówno użytkownik zewnętrzny, jak i grupa muszą znajdować się w tej samej dzierżawie usługi Azure AD.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Aby dodać role i użytkowników przy użyciu programu PowerShell

Moduł [SqlServer](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) udostępnia polecenia cmdlet zarządzania bazą danych specyficzne dla zadania i ogólne przeznaczenie Invoke-ASCmd poleceń cmdlet, który akceptuje tabular model skryptów języka (TMSL) kwerendy lub skryptu. Następujące polecenia cmdlet są używane do zarządzania rolami bazy danych i użytkownikami.
  
|Polecenie cmdlet|Opis|
|------------|-----------------| 
|[Numer dodania roli](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Dodawanie członka do roli bazy danych.| 
|[Usuń-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Usuwanie członka z roli bazy danych.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Wykonanie skryptu TMSL.|

## <a name="row-filters"></a>Filtry wierszy  

Filtry wierszy określają, które wiersze w tabeli mogą być wyszukiwane przez członków określonej roli. Filtry wierszy są definiowane dla każdej tabeli w modelu przy użyciu formuł języka DAX.  
  
Filtry wierszy można zdefiniować tylko dla ról z uprawnieniami Odczytu i Odczytu i Procesu. Domyślnie, jeśli filtr wierszy nie jest zdefiniowany dla określonej tabeli, członkowie mogą wysyłać zapytania do wszystkich wierszy w tabeli, chyba że filtrowanie krzyżowe ma zastosowanie z innej tabeli.
  
 Filtry wierszy wymagają formuły języka DAX, która musi być obliczona na wartość PRAWDA/FAŁSZ, aby zdefiniować wiersze, które mogą być wyszukiwane przez członków tej konkretnej roli. Nie można wyszukiwać wierszy nieuwzględnianych w formule języka DAX. Na przykład w tabeli Klienci z następującym wyrażeniem filtrów wiersza *, =Klienci [Kraj] = "USA"*, członkowie roli Sprzedaż mogą widzieć tylko klientów w USA.  
  
Filtry wierszy mają zastosowanie do określonych wierszy i powiązanych wierszy. Gdy tabela ma wiele relacji, filtry stosują zabezpieczenia dla aktywnej relacji. Filtry wierszy są przecinane z innymi filerami wierszy zdefiniowanymi dla powiązanych tabel, na przykład:  
  
|Tabela|Wyrażenie języka DAX|  
|-----------|--------------------|  
|Region|=Region[Kraj]="USA"|  
|ProductCategory|=Kategoria produktu[Nazwa]="Rowery"|  
|Transakcje|=Transakcje[Rok]=2016|  
  
 Efekt netto jest elementy członkowskie mogą wysyłać zapytania wierszy danych, gdzie klient jest w USA, kategoria produktu to rowery, a rok 2016. Użytkownicy nie mogą wysyłać zapytań do transakcji poza STANAMI Zjednoczonymi, transakcji, które nie są rowerami, ani transakcji nie w 2016 r., chyba że są członkami innej roli, która przyznaje te uprawnienia.
  
 Za pomocą filtru *=FALSE()* można odmówić dostępu do wszystkich wierszy dla całej tabeli.

## <a name="next-steps"></a>Następne kroki

  [Zarządzanie administratorami serwerów](analysis-services-server-admins.md)   
  [Zarządzanie usługami Azure Analysis Services przy użyciu programu PowerShell](analysis-services-powershell.md)  
  [Odwołanie do języka skryptów modelu tabelaryjskiego (TMSL)](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)

