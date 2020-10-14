---
title: Zarządzanie rolami bazy danych i użytkownikami w Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak zarządzać rolami bazy danych i użytkownikami na serwerze Analysis Services na platformie Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 56f98d41fc73cdd2be0923de66a5af09c875a050
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013597"
---
# <a name="manage-database-roles-and-users"></a>Zarządzanie rolami i użytkownikami bazy danych

Na poziomie bazy danych modelu wszyscy użytkownicy muszą należeć do roli. Role definiują użytkowników z określonymi uprawnieniami dla bazy danych modelu. Wszyscy użytkownicy lub grupy zabezpieczeń dodani do roli muszą mieć konto w dzierżawie usługi Azure AD w tej samej subskrypcji co serwer programu. 

Sposób definiowania ról różni się w zależności od używanego narzędzia, ale efekt jest taki sam.

Uprawnienia roli obejmują:
*  **Administratorzy** — użytkownicy mają pełne uprawnienia do bazy danych programu. Role bazy danych z uprawnieniami administratora różnią się od administratorów serwerów.
*  **Proces** — użytkownicy mogą łączyć się z bazą danych i wykonywać na niej operacje przetwarzania oraz analizować dane modelu bazy danych.
*  Użytkownicy **odczytu** mogą używać aplikacji klienckiej do nawiązywania połączeń z modelem i analizowania danych z bazy danych.

Podczas tworzenia projektu modelu tabelarycznego tworzysz role i Dodaj użytkowników lub grupy do tych ról przy użyciu menedżera ról w programie Visual Studio z projektami Analysis Services. W przypadku wdrożenia na serwerze użyj SQL Server Management Studio (SSMS), [Analysis Services poleceń cmdlet programu PowerShell](/analysis-services/powershell/analysis-services-powershell-reference)lub [języka skryptów tabelarycznych](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (TMSL), aby dodać lub usunąć role i członków użytkowników.

Podczas dodawania **grupy zabezpieczeń**Użyj `obj:groupid@tenantid` .

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Aby dodać role i użytkowników w programie Visual Studio lub zarządzać nimi  
  
1.  W **Eksploratorze modeli tabelarycznych**kliknij prawym przyciskiem myszy pozycję **role**.  
  
2.  W **Menedżerze ról**kliknij pozycję **Nowy**.  
  
3.  Wpisz nazwę roli.  
  
     Domyślnie nazwa roli domyślnej jest przyrostowo numerowany dla każdej nowej roli. Zaleca się wpisanie nazwy, która jasno identyfikuje typ elementu członkowskiego, na przykład kierowników finansów lub specjalistów ds. kadr.  
  
4.  Wybierz jedno z następujących uprawnień:  
  
    |Uprawnienie|Opis|  
    |----------------|-----------------|  
    |**Brak**|Członkowie nie mogą odczytywać ani modyfikować schematu modelu i nie mogą wykonywać zapytań dotyczących danych.|  
    |**Odczyt**|Członkowie mogą wykonywać zapytania dotyczące danych (na podstawie filtrów wierszy), ale nie mogą modyfikować schematu modelu.|  
    |**Odczyt i proces**|Członkowie mogą wykonywać zapytania dotyczące danych (na podstawie filtrów na poziomie wiersza) i uruchamiać proces i przetwarzać wszystkie operacje, ale nie mogą modyfikować schematu modelu.|  
    |**Proces**|Członkowie mogą uruchamiać proces i przetwarzać wszystkie operacje. Nie można odczytać lub zmodyfikować schematu modelu i nie można wykonać zapytania dotyczącego danych.|  
    |**Administrator**|Członkowie mogą modyfikować schemat modelu i wykonywać zapytania dotyczące wszystkich danych.|   
  
5.  Jeśli tworzona rola ma uprawnienia Odczyt lub odczyt i przetwarzanie, można dodać filtry wierszy przy użyciu formuły języka DAX. Kliknij kartę **filtry wierszy** , a następnie wybierz tabelę, a następnie kliknij pole **Filtr języka DAX** , a następnie wpisz formułę języka DAX.
  
6.  Kliknij pozycję **Członkowie**  >  **Dodaj zewnętrzny**.  
  
8.  W obszarze **Dodawanie zewnętrznego elementu członkowskiego**wprowadź użytkowników lub grupy w dzierżawie usługi Azure AD według adresu e-mail. Po kliknięciu przycisku OK i zamknięciu menedżera ról, role i członkowie roli są wyświetlane w Eksploratorze modelu tabelarycznego. 
 
     ![Role i użytkownicy w Eksploratorze modeli tabelarycznych](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Wdróż na serwerze Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Aby dodać role i użytkowników w programie SSMS lub zarządzać nimi

Aby dodać role i użytkowników do wdrożonej bazy danych modelu, należy połączyć się z serwerem jako administrator serwera lub już w roli bazy danych z uprawnieniami administratora.

1. W obiekcie Exporer kliknij prawym przyciskiem myszy pozycję **role**  >  **Nowa rola**.

2. W obszarze **Utwórz rolę**wprowadź nazwę i opis roli.

3. Wybierz uprawnienie.

   |Uprawnienie|Opis|  
   |----------------|-----------------|  
   |**Pełna kontrola (Administrator)**|Członkowie mogą modyfikować schemat modelu, proces i wykonywać zapytania dotyczące wszystkich danych.| 
   |**Przetwarzanie bazy danych**|Członkowie mogą uruchamiać proces i przetwarzać wszystkie operacje. Nie można zmodyfikować schematu modelu i nie można wykonać zapytania dotyczącego danych.|  
   |**Odczyt**|Członkowie mogą wykonywać zapytania dotyczące danych (na podstawie filtrów wierszy), ale nie mogą modyfikować schematu modelu.|  
  
4. Kliknij pozycję **członkostwo**, a następnie wprowadź użytkownika lub grupę w dzierżawie usługi Azure AD według adresu e-mail.

     ![Dodaj użytkownika](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Jeśli tworzona rola ma uprawnienie Odczyt, można dodać filtry wierszy przy użyciu formuły języka DAX. Kliknij pozycję **filtry wierszy**, zaznacz tabelę, a następnie wpisz formułę języka DAX w polu **Filtr języka DAX** . 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Aby dodać role i użytkowników przy użyciu skryptu TMSL

Skrypt TMSL można uruchomić w oknie XMLA w programie SSMS lub przy użyciu programu PowerShell. Użyj polecenia [CreateOrReplace](/analysis-services/tmsl/createorreplace-command-tmsl) i obiektu [Roles](/analysis-services/tmsl/roles-object-tmsl) .

**Przykładowy skrypt TMSL**

W tym przykładzie użytkownik zewnętrzny i Grupa są dodawani do roli analityk z uprawnieniami odczyt dla bazy danych SalesBI. Zarówno użytkownik zewnętrzny, jak i Grupa muszą znajdować się w tej samej dzierżawie usługi Azure AD.

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

Moduł [SqlServer](/analysis-services/powershell/analysis-services-powershell-reference) udostępnia specyficzne dla zadania polecenia cmdlet zarządzania bazami danych i polecenie cmdlet Invoke-ASCmd ogólnego przeznaczenia, które akceptuje zapytanie lub skrypt języka skryptów tabelarycznych (TMSL). Następujące polecenia cmdlet służą do zarządzania rolami bazy danych i użytkownikami.
  
|Polecenie cmdlet|Opis|
|------------|-----------------| 
|[Add-RoleMember](/powershell/module/sqlserver/Add-RoleMember)|Dodaj członka do roli bazy danych.| 
|[Remove-RoleMember](/powershell/module/sqlserver/remove-rolemember)|Usuń członka z roli bazy danych.|   
|[Invoke-ASCmd](/powershell/module/sqlserver/invoke-ascmd)|Wykonaj skrypt TMSL.|

## <a name="row-filters"></a>Filtry wierszy  

Filtry wierszy definiują, które wiersze w tabeli mogą być badane przez członków określonej roli. Filtry wierszy są definiowane dla każdej tabeli w modelu przy użyciu formuł języka DAX.  
  
Filtry wierszy można definiować tylko dla ról z uprawnieniami odczyt i odczyt i przetwarzanie. Domyślnie jeśli filtr wiersza nie jest zdefiniowany dla określonej tabeli, członkowie mogą wykonywać zapytania dotyczące wszystkich wierszy w tabeli, chyba że filtrowanie krzyżowe jest stosowane z innej tabeli.
  
 Filtry wierszy wymagają Formuły języka DAX, która musi oszacować wartość PRAWDA/FAŁSZ, aby zdefiniować wiersze, do których mogą być wysyłane zapytania przez członków tej konkretnej roli. Nie można zbadać wierszy nieuwzględnionych w formule języka DAX. Na przykład tabela Customers z następującym wyrażeniem filtry wierszy, *= klienci [Country] = "USA"*, członkowie roli Sales mogą widzieć tylko klientów w USA.  
  
Filtry wierszy dotyczą określonych wierszy i powiązanych wierszy. Gdy tabela ma wiele relacji, filtry stosują zabezpieczenia dla aktywnej relacji. Filtry wierszy są przecinane z innymi plikami wierszy zdefiniowanymi dla powiązanych tabel, na przykład:  
  
|Tabela|Wyrażenie języka DAX|  
|-----------|--------------------|  
|Region|= Region [Country] = "USA"|  
|ProductCategory|= ProductCategory [nazwa] = "rowery"|  
|Transakcje|= Transakcje [Year] = 2016|  
  
 Efektem netto jest to, że członkowie mogą wykonywać zapytania dotyczące wierszy danych, w których klient znajduje się w Stanach Zjednoczonych, Kategoria produktu to rowery, a rok to 2016. Użytkownicy nie mogą wykonywać zapytań dotyczących transakcji poza stanem USA, transakcji, które nie są rowery ani transakcji, które nie znajdują się w 2016, chyba że są członkami innej roli, która przyznaje te uprawnienia.
  
 Możesz użyć filtru, *= false ()*, aby odmówić dostępu do wszystkich wierszy dla całej tabeli.

## <a name="next-steps"></a>Następne kroki

  [Zarządzanie administratorami serwera](analysis-services-server-admins.md)   
  [Zarządzanie usługami Azure Analysis Services przy użyciu programu PowerShell](analysis-services-powershell.md)  
  [Dokumentacja języka skryptów tabelarycznych (TMSL)](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)