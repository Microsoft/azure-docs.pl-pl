---
title: Widoki bazy danych usługi Azure łańcucha bloków Workbench
description: Omówienie dostępnych widoków bazy danych SQL usługi Azure łańcucha bloków Workbench w wersji zapoznawczej.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 6c7fba590c9c8951e1862c28cb24dbde52c8da8d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625538"
---
# <a name="azure-blockchain-workbench-database-views"></a>Widoki bazy danych usługi Azure łańcucha bloków Workbench

Usługa Azure łańcucha bloków Workbench w wersji zapoznawczej dostarcza dane z rozproszonych ksiąg do bazy danych usług SQL DB w *łańcuchu* . Baza danych między łańcuchami umożliwia korzystanie z programu SQL i istniejących narzędzi, takich jak [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms), w celu współdziałania z danymi łańcucha bloków.

Usługa Azure łańcucha bloków Workbench udostępnia zestaw widoków bazy danych, które zapewniają dostęp do danych, które będą pomocne podczas wykonywania zapytań. Te widoki są silnie nieznormalizowane, aby ułatwić szybkie rozpoczęcie tworzenia raportów, analiz i w inny sposób korzystać z danych łańcucha bloków z istniejących narzędzi i bez konieczności ponownego uczenia się personelu bazy danych.

Ta sekcja zawiera omówienie widoków bazy danych i zawartych w nich danych.

> [!NOTE]
> Wszystkie bezpośrednie użycie tabel bazy danych znajdujących się w bazie danych poza tymi widokami, gdy jest to możliwe, nie jest obsługiwane.
>

## <a name="vwapplication"></a>vwApplication

Ten widok zawiera szczegółowe informacje o **aplikacjach** , które zostały przekazane do usługi Azure łańcucha bloków Workbench.

| Nazwa                             | Typ          | Może mieć wartość null | Opis                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                  | nvarchar (50)  | Nie          | Nazwa aplikacji |
| ApplicationDescription           | nvarchar (255) | Tak         | Opis aplikacji |
| ApplicationDisplayName           | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika |
| ApplicationEnabled               | bit           | Nie          | Określa, czy aplikacja jest obecnie włączona<br /> **Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają na łańcucha bloków i dane dotyczące tych kontraktów pozostają w bazie danych. |
| UploadedDtTm                     | datetime2 (7)  | Nie          | Data i godzina przekazania kontraktu |
| UploadedByUserId                 | int           | Nie          | Identyfikator użytkownika, który przesłał aplikację |
| UploadedByUserExternalId         | nvarchar (255) | Nie          | Identyfikator zewnętrzny użytkownika, który przesłał aplikację. Domyślnie ten identyfikator jest użytkownikiem z Azure Active Directory dla konsorcjum.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Nie          | Identyfikuje bieżący stan procesu aprowizacji dla użytkownika. Możliwe wartości: <br />0 — użytkownik został utworzony przez interfejs API<br />1 — klucz został skojarzony z użytkownikiem w bazie danych<br />2 — użytkownik jest w pełni zainicjowany                         |
| UploadedByUserFirstName          | nvarchar (50)  | Tak         | Imię i nazwisko użytkownika, który przesłał umowę |
| UploadedByUserLastName           | nvarchar (50)  | Tak         | Nazwisko użytkownika, który przesłał umowę |
| UploadedByUserEmailAddress       | nvarchar (255) | Tak         | Adres e-mail użytkownika, który przesłał umowę |

## <a name="vwapplicationrole"></a>vwApplicationRole

Ten widok zawiera szczegółowe informacje dotyczące ról, które zostały zdefiniowane w aplikacjach Workbench usługi Azure łańcucha bloków.

Na przykład w aplikacji do *transferu zasobów* można zdefiniować role, takie jak *kupujący* i role *sprzedawcy* .

| Nazwa                   | Typ             | Może mieć wartość null | Opis                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Nie          | Unikatowy identyfikator aplikacji           |
| ApplicationName        | nvarchar (50)     | Nie          | Nazwa aplikacji                       |
| ApplicationDescription | nvarchar (255)    | Tak         | Opis aplikacji                  |
| ApplicationDisplayName | nvarchar (255)    | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika      |
| RoleId                 | int              | Nie          | Unikatowy identyfikator roli w aplikacji |
| RoleName               | nvarchar50)      | Nie          | Nazwa roli                              |
| RoleDescription        | Opis (255) | Tak         | Opis roli                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Ten widok zawiera szczegółowe informacje na temat ról zdefiniowanych w aplikacjach Azure łańcucha bloków Workbench i skojarzonych z nimi użytkowników.

W aplikacji do *przenoszenia zasobów* , na przykład *Jan Kowalski* , może być skojarzony z rolą *kupca* .

| Nazwa                       | Typ          | Może mieć wartość null | Opis                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Nie          | Unikatowy identyfikator aplikacji                                                                                                                                                                                               |
| ApplicationName            | nvarchar (50)  | Nie          | Nazwa aplikacji                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar (255) | Tak         | Opis aplikacji                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Nie          | Unikatowy identyfikator roli w aplikacji                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Nie          | Nazwa roli                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar (255) | Tak         | Opis roli                                                                                                                                                                                                             |
| UserId                     | int           | Nie          | Identyfikator użytkownika skojarzonego z rolą |
| UserExternalId             | nvarchar (255) | Nie          | Identyfikator zewnętrzny użytkownika, który jest skojarzony z rolą. Domyślnie ten identyfikator jest użytkownikiem z Azure Active Directory dla konsorcjum.                                                                     |
| UserProvisioningStatus     | int           | Nie          | Identyfikuje bieżący stan procesu aprowizacji dla użytkownika. Możliwe wartości: <br />0 — użytkownik został utworzony przez interfejs API<br />1 — klucz został skojarzony z użytkownikiem w bazie danych<br />2 — użytkownik jest w pełni zainicjowany |
| UserFirstName              | nvarchar (50)  | Tak         | Imię i nazwisko użytkownika, który jest skojarzony z rolą |
| UserLastName               | nvarchar (255) | Tak         | Nazwisko użytkownika, który jest skojarzony z rolą |
| UserEmailAddress           | nvarchar (255) | Tak         | Adres e-mail użytkownika, który jest skojarzony z rolą |

## <a name="vwconnectionuser"></a>vwConnectionUser

Ten widok zawiera szczegółowe informacje o połączeniach zdefiniowanych w usłudze Azure łańcucha bloków Workbench i skojarzonych z nimi użytkownikach. Dla każdego połączenia ten widok zawiera następujące dane:

-   Szczegóły skojarzonej księgi
-   Informacje o skojarzonym użytkowniku

| Nazwa                     | Typ          | Może mieć wartość null | Opis                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Nie          | Unikatowy identyfikator połączenia w usłudze Azure łańcucha bloków Workbench |
| ConnectionEndpointUrl    | nvarchar (50)  | Nie          | Adres URL punktu końcowego dla połączenia |
| ConnectionFundingAccount | nvarchar (255) | Tak         | Konto finansowania skojarzone z połączeniem, jeśli ma zastosowanie |
| LedgerId                 | int           | Nie          | Unikatowy identyfikator dla księgi |
| Nr księgi               | nvarchar (50)  | Nie          | Nazwa księgi |
| LedgerDisplayName        | nvarchar (255) | Nie          | Nazwa księgi, która ma być wyświetlana w interfejsie użytkownika |
| UserId                   | int           | Nie          | Identyfikator użytkownika skojarzonego z połączeniem |
| UserExternalId           | nvarchar (255) | Nie          | Identyfikator zewnętrzny użytkownika, który jest skojarzony z połączeniem. Domyślnie ten identyfikator jest użytkownikiem z Azure Active Directory dla konsorcjum. |
| UserProvisioningStatus   | int           | Nie          |Identyfikuje bieżący stan procesu aprowizacji dla użytkownika. Możliwe wartości: <br />0 — użytkownik został utworzony przez interfejs API<br />1 — klucz został skojarzony z użytkownikiem w bazie danych<br />2 — użytkownik jest w pełni zainicjowany |
| UserFirstName            | nvarchar (50)  | Tak         | Imię i nazwisko użytkownika, który jest skojarzony z połączeniem |
| UserLastName             | nvarchar (255) | Tak         | Nazwisko użytkownika, który jest skojarzony z połączeniem |
| UserEmailAddress         | nvarchar (255) | Tak         | Adres e-mail użytkownika, który jest skojarzony z połączeniem |

## <a name="vwcontract"></a>vwContract

Ten widok zawiera szczegółowe informacje dotyczące wdrożonych kontraktów. W przypadku każdego kontraktu ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Skojarzona implementacja księgi dla funkcji
-   Szczegóły dotyczące użytkownika, który zainicjował akcję
-   Szczegóły dotyczące bloku łańcucha bloków i transakcji

| Nazwa                                     | Typ           | Może mieć wartość null | Opis                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Nie          | Unikatowy identyfikator połączenia w usłudze Azure łańcucha bloków Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar (50)   | Nie          | Adres URL punktu końcowego dla połączenia |
| ConnectionFundingAccount                 | nvarchar (255)  | Tak         | Konto finansowania skojarzone z połączeniem, jeśli ma zastosowanie |
| LedgerId                                 | int            | Nie          | Unikatowy identyfikator dla księgi |
| Nr księgi                               | nvarchar (50)   | Nie          | Nazwa księgi |
| LedgerDisplayName                        | nvarchar (255)  | Nie          | Nazwa księgi, która ma być wyświetlana w interfejsie użytkownika |
| ApplicationId                            | int            | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                          | nvarchar (50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName                   | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                       | bit            | Nie          | Określa, czy aplikacja jest obecnie włączona.<br /> **Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają na łańcucha bloków i dane dotyczące tych kontraktów pozostają w bazie danych.  |
| WorkflowId                               | int            | Nie          | Unikatowy identyfikator przepływu pracy skojarzonego z umową |
| WorkflowName                             | nvarchar (50)   | Nie          | Nazwa przepływu pracy skojarzonego z umową |
| WorkflowDisplayName                      | nvarchar (255)  | Nie          | Nazwa przepływu pracy skojarzonego z umową wyświetlaną w interfejsie użytkownika |
| WorkflowDescription                      | nvarchar (255)  | Tak         | Opis przepływu pracy skojarzonego z umową |
| ContractCodeId                           | int            | Nie          | Unikatowy identyfikator kodu kontraktu skojarzonego z umową |
| ContractFileName                         | int            | Nie          | Nazwa pliku zawierającego kod inteligentnego kontraktu dla tego przepływu pracy. |
| ContractUploadedDtTm                     | int            | Nie          | Data i godzina przekazania kodu umowy |
| ContractId                               | int            | Nie          | Unikatowy identyfikator kontraktu |
| ContractProvisioningStatus               | int            | Nie          | Określa bieżący stan procesu aprowizacji kontraktu. Możliwe wartości: <br />0 — kontrakt został utworzony przez interfejs API w bazie danych<br />1 — kontrakt został wysłany do księgi<br />2 — kontrakt został pomyślnie wdrożony w księdze<br />3 lub 4 — nie można wdrożyć kontraktu w księdze<br />5 — kontrakt został pomyślnie wdrożony w księdze <br /><br />Począwszy od wersji 1,5, obsługiwane są wartości od 0 do 5. W celu zapewnienia zgodności z poprzednimi wersjami w bieżącej wersji widok **vwContractV0** jest dostępny, który obsługuje tylko wartości od 0 do 2. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | Adres e-mail użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserId                 | int            | Nie          | Identyfikator zewnętrzny użytkownika, który wdrożył kontrakt. Domyślnie ten identyfikator jest identyfikatorem GUID reprezentującym identyfikator Azure Active Directory użytkownika.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar (255)  | Nie          | Identyfikator zewnętrzny użytkownika, który wdrożył kontrakt. Domyślnie ten identyfikator jest identyfikatorem GUID reprezentującym identyfikator Azure Active Directory użytkownika.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Nie          | Identyfikuje bieżący stan procesu aprowizacji dla użytkownika. Możliwe wartości: <br />0 — użytkownik został utworzony przez interfejs API<br />1 — klucz został skojarzony z użytkownikiem w bazie danych <br />2 — użytkownik jest w pełni zainicjowany                     |
| ContractDeployedByUserFirstName          | nvarchar (50)   | Tak         | Imię i nazwisko użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserLastName           | nvarchar (255)  | Tak         | Nazwisko użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserEmailAddress       | nvarchar (255)  | Tak         | Adres e-mail użytkownika, który wdrożył kontrakt |

## <a name="vwcontractaction"></a>vwContractAction

Ten widok przedstawia większość informacji związanych z akcjami podejmowanymi na umowach i ma na celu ułatwienie obsługi typowych scenariuszy raportowania. Dla każdej podjętej akcji ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Skojarzona funkcja kontraktu inteligentnego i definicja parametru
-   Skojarzona implementacja księgi dla funkcji
-   Określone wartości wystąpień podane dla parametrów
-   Szczegóły dotyczące użytkownika, który zainicjował akcję
-   Szczegóły dotyczące bloku łańcucha bloków i transakcji

| Nazwa                                     | Typ          | Może mieć wartość null | Opis                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                          | nvarchar (50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName                   | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                       | bit           | Nie          | To pole określa, czy aplikacja jest obecnie włączona. Uwaga: Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają na łańcucha bloków i dane dotyczące tych kontraktów pozostają w bazie danych.                                                  |
| WorkflowId                               | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| WorkflowName                             | nvarchar (50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName                      | nvarchar (255) | Nie          | Nazwa przepływu pracy do wyświetlania w interfejsie użytkownika |
| WorkflowDescription                      | nvarchar (255) | Tak         | Opis przepływu pracy |
| ContractId                               | int           | Nie          | Unikatowy identyfikator kontraktu |
| ContractProvisioningStatus               | int           | Nie          | Określa bieżący stan procesu aprowizacji kontraktu. Możliwe wartości: <br />0 — kontrakt został utworzony przez interfejs API w bazie danych<br />1 — kontrakt został wysłany do księgi<br />2 — kontrakt został pomyślnie wdrożony w księdze<br />3 lub 4 — nie można wdrożyć kontraktu w księdze<br />5 — kontrakt został pomyślnie wdrożony w księdze <br /><br />Począwszy od wersji 1,5, obsługiwane są wartości od 0 do 5. W celu zapewnienia zgodności z poprzednimi wersjami w bieżącej wersji widok **vwContractActionV0** jest dostępny, który obsługuje tylko wartości od 0 do 2. |
| ContractCodeId                           | int           | Nie          | Unikatowy identyfikator implementacji kodu umowy |
| ContractLedgerIdentifier                 | nvarchar (255) | Tak         | Unikatowy identyfikator skojarzony ze wdrożoną wersją inteligentnego kontraktu dla konkretnej księgi rozproszonej. Na przykład Ethereum. |
| ContractDeployedByUserId                 | int           | Nie          | Unikatowy identyfikator użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserFirstName          | nvarchar (50)  | Tak         | Imię i nazwisko użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserLastName           | nvarchar (255) | Tak         | Nazwisko użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserExternalId         | nvarchar (255) | Nie          | Zewnętrzny identyfikator użytkownika, który wdrożył kontrakt. Domyślnie ten identyfikator jest identyfikatorem GUID, który reprezentuje swoją tożsamość w Azure Active Directory konsorcjum.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar (255) | Tak         | Adres e-mail użytkownika, który wdrożył kontrakt |
| WorkflowFunctionId                       | int           | Nie          | Unikatowy identyfikator funkcji przepływu pracy |
| WorkflowFunctionName                     | nvarchar (50)  | Nie          | Nazwa funkcji |
| WorkflowFunctionDisplayName              | nvarchar (255) | Nie          | Nazwa funkcji, która ma być wyświetlana w interfejsie użytkownika |
| WorkflowFunctionDescription              | nvarchar (255) | Nie          | Opis funkcji |
| ContractActionId                         | int           | Nie          | Unikatowy identyfikator akcji kontraktu |
| ContractActionProvisioningStatus         | int           | Nie          | Określa bieżący stan procesu aprowizacji dla akcji kontraktu. Możliwe wartości: <br />0 — akcja kontraktu została utworzona przez interfejs API w bazie danych<br />1 — Akcja kontraktu została wysłana do księgi<br />2 — akcja kontraktu została pomyślnie wdrożona w księdze<br />3 lub 4 — nie można wdrożyć kontraktu w księdze<br />5 — kontrakt został pomyślnie wdrożony w księdze <br /><br />Począwszy od wersji 1,5, obsługiwane są wartości od 0 do 5. W celu zapewnienia zgodności z poprzednimi wersjami w bieżącej wersji widok **vwContractActionV0** jest dostępny, który obsługuje tylko wartości od 0 do 2. |
| ContractActionTimestamp                  | DateTime (2, 7) | Nie          | Sygnatura czasowa akcji kontraktu |
| ContractActionExecutedByUserId           | int           | Nie          | Unikatowy identyfikator użytkownika, który wykonał akcję kontraktu |
| ContractActionExecutedByUserFirstName    | int           | Tak         | Imię i nazwisko użytkownika, który wykonał akcję kontraktu |
| ContractActionExecutedByUserLastName     | nvarchar (50)  | Tak         | Nazwisko użytkownika, który wykonał akcję kontraktu |
| ContractActionExecutedByUserExternalId   | nvarchar (255) | Tak         | Zewnętrzny identyfikator użytkownika, który wykonał akcję kontraktu. Domyślnie ten identyfikator jest identyfikatorem GUID, który reprezentuje swoją tożsamość w Azure Active Directory konsorcjum. |
| ContractActionExecutedByUserEmailAddress | nvarchar (255) | Tak         | Adres e-mail użytkownika, który wykonał akcję kontraktu |
| WorkflowFunctionParameterId              | int           | Nie          | Unikatowy identyfikator parametru funkcji |
| WorkflowFunctionParameterName            | nvarchar (50)  | Nie          | Nazwa parametru funkcji |
| WorkflowFunctionParameterDisplayName     | nvarchar (255) | Nie          | Nazwa parametru funkcji, który ma być wyświetlany w interfejsie użytkownika |
| WorkflowFunctionParameterDataTypeId      | int           | Nie          | Unikatowy identyfikator dla typu danych skojarzonego z parametrem funkcji przepływu pracy |
| WorkflowParameterDataTypeName            | nvarchar (50)  | Nie          | Nazwa typu danych skojarzonego z parametrem funkcji przepływu pracy |
| ContractActionParameterValue             | nvarchar (255) | Nie          | Wartość parametru przechowywanego w kontrakcie inteligentnym |
| BlockHash                                | nvarchar (255) | Tak         | Skrót bloku |
| BlockNumber                              | int           | Tak         | Numer bloku w księdze |
| BlockTimestamp                           | DateTime (2, 7) | Tak         | Sygnatura czasowa bloku |
| TransactionId                            | int           | Nie          | Unikatowy identyfikator transakcji |
| TransactionFrom                          | nvarchar (255) | Tak         | Strona, która pochodziła z transakcji |
| TransactionTo                            | nvarchar (255) | Tak         | Strona, która była transakcyjna |
| TransactionHash                          | nvarchar (255) | Tak         | Skrót transakcji |
| TransactionIsWorkbenchTransaction        | bit           | Tak         | Bit, który określa, czy transakcja jest transakcją usługi Azure łańcucha bloków Workbench |
| TransactionProvisioningStatus            | int           | Tak         | Określa bieżący stan procesu aprowizacji transakcji. Możliwe wartości: <br />0 — transakcja została utworzona przez interfejs API w bazie danych<br />1 — transakcja została wysłana do księgi<br />2 — transakcja została pomyślnie wdrożona w księdze                 |
| TransactionValue                         | Liczba dziesiętna (32, 2) | Tak         | Wartość transakcji |

## <a name="vwcontractproperty"></a>vwContractProperty

Ten widok przedstawia większość informacji związanych z właściwościami skojarzonymi z umową i jest zaprojektowana tak, aby ułatwiać wspólne scenariusze raportowania. Dla każdej podjętej właściwości ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Szczegóły dotyczące użytkownika, który wdrożył przepływ pracy
-   Skojarzona Definicja właściwości kontraktu inteligentnego
-   Określone wartości wystąpień dla właściwości
-   Szczegóły właściwości stan kontraktu

| Nazwa                               | Typ          | Może mieć wartość null | Opis                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                    | nvarchar (50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName             | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                 | bit           | Nie          | Określa, czy aplikacja jest obecnie włączona.<br />**Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają na łańcucha bloków i dane dotyczące tych kontraktów pozostają w bazie danych.                      |
| WorkflowId                         | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| WorkflowName                       | nvarchar (50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName                | nvarchar (255) | Nie          | Nazwa przepływu pracy wyświetlanego w interfejsie użytkownika |
| WorkflowDescription                | nvarchar (255) | Tak         | Opis przepływu pracy |
| ContractId                         | int           | Nie          | Unikatowy identyfikator kontraktu |
| ContractProvisioningStatus         | int           | Nie          | Określa bieżący stan procesu aprowizacji kontraktu. Możliwe wartości: <br />0 — kontrakt został utworzony przez interfejs API w bazie danych<br />1 — kontrakt został wysłany do księgi<br />2 — kontrakt został pomyślnie wdrożony w księdze<br />3 lub 4 — nie można wdrożyć kontraktu w księdze<br />5 — kontrakt został pomyślnie wdrożony w księdze <br /><br />Począwszy od wersji 1,5, obsługiwane są wartości od 0 do 5. W celu zapewnienia zgodności z poprzednimi wersjami w bieżącej wersji widok **vwContractPropertyV0** jest dostępny, który obsługuje tylko wartości od 0 do 2. |
| ContractCodeId                     | int           | Nie          | Unikatowy identyfikator implementacji kodu umowy |
| ContractLedgerIdentifier           | nvarchar (255) | Tak         | Unikatowy identyfikator skojarzony ze wdrożoną wersją inteligentnego kontraktu dla konkretnej księgi rozproszonej. Na przykład Ethereum. |
| ContractDeployedByUserId           | int           | Nie          | Unikatowy identyfikator użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Tak         | Imię i nazwisko użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserLastName     | nvarchar (255) | Tak         | Nazwisko użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserExternalId   | nvarchar (255) | Nie          | Zewnętrzny identyfikator użytkownika, który wdrożył kontrakt. Domyślnie ten identyfikator jest identyfikatorem GUID, który reprezentuje swoją tożsamość w Azure Active Directory konsorcjum |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Tak         | Adres e-mail użytkownika, który wdrożył kontrakt |
| WorkflowPropertyId                 | int           |             | Unikatowy identyfikator właściwości przepływu pracy |
| WorkflowPropertyDataTypeId         | int           | Nie          | Identyfikator typu danych właściwości |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Nie          | Nazwa typu danych właściwości |
| WorkflowPropertyName               | nvarchar (50)  | Nie          | Nazwa właściwości przepływu pracy |
| WorkflowPropertyDisplayName        | nvarchar (255) | Nie          | Nazwa wyświetlana właściwości przepływu pracy |
| WorkflowPropertyDescription        | nvarchar (255) | Tak         | Opis właściwości |
| ContractPropertyValue              | nvarchar (255) | Nie          | Wartość właściwości dla kontraktu |
| StateName                          | nvarchar (50)  | Tak         | Jeśli ta właściwość zawiera stan kontraktu, jest to nazwa wyświetlana stanu. Jeśli nie jest on skojarzony ze stanem, wartość będzie równa null. |
| StateDisplayName                   | nvarchar (255) | Nie          | Jeśli ta właściwość zawiera stan, jest to nazwa wyświetlana stanu. Jeśli nie jest on skojarzony ze stanem, wartość będzie równa null. |
| StateValue                         | nvarchar (255) | Tak         | Jeśli ta właściwość zawiera stan, jest to wartość stanu. Jeśli nie jest on skojarzony ze stanem, wartość będzie równa null. |

## <a name="vwcontractstate"></a>vwContractState

Ten widok przedstawia większość informacji związanych ze stanem konkretnej kontraktu i ma na celu ułatwienie obsługi typowych scenariuszy raportowania. Każdy rekord w tym widoku zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Szczegóły dotyczące użytkownika, który wdrożył przepływ pracy
-   Skojarzona Definicja właściwości kontraktu inteligentnego
-   Szczegóły właściwości stan kontraktu

| Nazwa                               | Typ          | Może mieć wartość null | Opis                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                    | nvarchar (50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName             | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                 | bit           | Nie          | Określa, czy aplikacja jest obecnie włączona.<br />**Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają na łańcucha bloków i dane dotyczące tych kontraktów pozostają w bazie danych. |
| WorkflowId                         | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| WorkflowName                       | nvarchar (50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName                | nvarchar (255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| WorkflowDescription                | nvarchar (255) | Tak         | Opis przepływu pracy |
| ContractLedgerImplementationId     | nvarchar (255) | Tak         | Unikatowy identyfikator skojarzony ze wdrożoną wersją inteligentnego kontraktu dla konkretnej księgi rozproszonej. Na przykład Ethereum. |
| ContractId                         | int           | Nie          | Unikatowy identyfikator kontraktu |
| ContractProvisioningStatus         | int           | Nie          |Określa bieżący stan procesu aprowizacji kontraktu. Możliwe wartości: <br />0 — kontrakt został utworzony przez interfejs API w bazie danych<br />1 — kontrakt został wysłany do księgi<br />2 — kontrakt został pomyślnie wdrożony w księdze<br />3 lub 4 — nie można wdrożyć kontraktu w księdze<br />5 — kontrakt został pomyślnie wdrożony w księdze <br /><br />Począwszy od wersji 1,5, obsługiwane są wartości od 0 do 5. W celu zapewnienia zgodności z poprzednimi wersjami w bieżącej wersji widok **vwContractStateV0** jest dostępny, który obsługuje tylko wartości od 0 do 2. |
| ConnectionId                       | int           | Nie          | Unikatowy identyfikator wystąpienia usługi łańcucha bloków, do którego jest wdrożony przepływ pracy |
| ContractCodeId                     | int           | Nie          | Unikatowy identyfikator implementacji kodu umowy |
| ContractDeployedByUserId           | int           | Nie          | Unikatowy identyfikator użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserExternalId   | nvarchar (255) | Nie          | Zewnętrzny identyfikator użytkownika, który wdrożył kontrakt. Domyślnie ten identyfikator jest identyfikatorem GUID, który reprezentuje swoją tożsamość w Azure Active Directory konsorcjum. |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Tak         | Imię i nazwisko użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserLastName     | nvarchar (255) | Tak         | Nazwisko użytkownika, który wdrożył kontrakt |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Tak         | Adres e-mail użytkownika, który wdrożył kontrakt |
| WorkflowPropertyId                 | int           | Nie          | Unikatowy identyfikator właściwości przepływu pracy |
| WorkflowPropertyDataTypeId         | int           | Nie          | Identyfikator typu danych właściwości przepływu pracy |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Nie          | Nazwa typu danych właściwości przepływu pracy |
| WorkflowPropertyName               | nvarchar (50)  | Nie          | Nazwa właściwości przepływu pracy |
| WorkflowPropertyDisplayName        | nvarchar (255) | Nie          | Nazwa wyświetlana właściwości, która ma być wyświetlana w interfejsie użytkownika |
| WorkflowPropertyDescription        | nvarchar (255) | Tak         | Opis właściwości |
| ContractPropertyValue              | nvarchar (255) | Nie          | Wartość właściwości przechowywanej w kontrakcie |
| StateName                          | nvarchar (50)  | Tak         | Jeśli ta właściwość zawiera stan, nazwa wyświetlana stanu. Jeśli nie jest on skojarzony ze stanem, wartość będzie równa null. |
| StateDisplayName                   | nvarchar (255) | Nie          | Jeśli ta właściwość zawiera stan, jest to nazwa wyświetlana stanu. Jeśli nie jest on skojarzony ze stanem, wartość będzie równa null. |
| StateValue                         | nvarchar (255) | Tak         | Jeśli ta właściwość zawiera stan, jest to wartość stanu. Jeśli nie jest on skojarzony ze stanem, wartość będzie równa null. |

## <a name="vwuser"></a>vwUser

Ten widok zawiera szczegółowe informacje o członkach konsorcjum, które są udostępniane do korzystania z usługi Azure łańcucha bloków Workbench. Domyślnie dane są wypełniane początkową obsługą użytkownika.

| Nazwa               | Typ          | Może mieć wartość null | Opis                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID (Identyfikator)                 | int           | Nie          | Unikatowy identyfikator użytkownika |
| ExternalID         | nvarchar (255) | Nie          | Identyfikator zewnętrzny użytkownika. Domyślnie ten identyfikator jest identyfikatorem GUID reprezentującym identyfikator Azure Active Directory użytkownika. |
| ProvisioningStatus | int           | Nie          |Identyfikuje bieżący stan procesu aprowizacji dla użytkownika. Możliwe wartości: <br />0 — użytkownik został utworzony przez interfejs API<br />1 — klucz został skojarzony z użytkownikiem w bazie danych<br />2 — użytkownik jest w pełni zainicjowany |
| FirstName (Imię)          | nvarchar (50)  | Tak         | Imię użytkownika |
| LastName (Nazwisko)           | nvarchar (50)  | Tak         | Nazwisko użytkownika |
| EmailAddress (Adres e-mail)       | nvarchar (255) | Tak         | Adres e-mail użytkownika |

## <a name="vwworkflow"></a>vwWorkflow

Ten widok przedstawia szczegóły podstawowych metadanych przepływu pracy oraz funkcji i parametrów przepływu pracy. Zaprojektowana na potrzeby raportowania, zawiera również metadane dotyczące aplikacji skojarzonej z przepływem pracy. Ten widok zawiera dane z wielu tabel źródłowych, które ułatwiają raportowanie przepływów pracy. Dla każdego przepływu pracy ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Informacje o stanie uruchomienia skojarzonego przepływu pracy

| Nazwa                              | Typ          | Może mieć wartość null | Opis                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                   | nvarchar (50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName            | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                | bit           | Nie          | Określa, czy aplikacja jest włączona |
| WorkflowId                        | int           | Tak         | Unikatowy identyfikator przepływu pracy |
| WorkflowName                      | nvarchar (50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName               | nvarchar (255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika |
| WorkflowDescription               | nvarchar (255) | Tak         | Opis przepływu pracy. |
| WorkflowConstructorFunctionId     | int           | Nie          | Identyfikator funkcji przepływu pracy, która służy jako Konstruktor dla przepływu pracy |
| WorkflowStartStateId              | int           | Nie          | Unikatowy identyfikator stanu |
| WorkflowStartStateName            | nvarchar (50)  | Nie          | Nazwa stanu |
| WorkflowStartStateDisplayName     | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika dla stanu |
| WorkflowStartStateDescription     | nvarchar (255) | Tak         | Opis stanu przepływu pracy |
| WorkflowStartStateStyle           | nvarchar (50)  | Tak         | Ta wartość określa wartość procentową ukończenia przepływu pracy w tym stanie |
| WorkflowStartStateValue           | int           | Nie          | Wartość stanu |
| WorkflowStartStatePercentComplete | int           | Nie          | Opis tekstowy, który zawiera wskazówki dla klientów na temat sposobu renderowania tego stanu w interfejsie użytkownika. Obsługiwane Stany obejmują *sukces* i *Niepowodzenie* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Ten widok przedstawia szczegóły podstawowych metadanych przepływu pracy oraz funkcji i parametrów przepływu pracy. Zaprojektowana na potrzeby raportowania, zawiera również metadane dotyczące aplikacji skojarzonej z przepływem pracy. Ten widok zawiera dane z wielu tabel źródłowych, które ułatwiają raportowanie przepływów pracy. Dla każdej funkcji przepływu pracy ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Szczegóły funkcji przepływu pracy

| Nazwa                                 | Typ          | Może mieć wartość null | Opis                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName                      | nvarchar (50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName               | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika |
| ApplicationEnabled                   | bit           | Nie          | Określa, czy aplikacja jest włączona |
| WorkflowId                           | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| WorkflowName                         | nvarchar (50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName                  | nvarchar (255) | Nie          | Nazwa przepływu pracy wyświetlanego w interfejsie użytkownika |
| WorkflowDescription                  | nvarchar (255) | Tak         | Opis przepływu pracy |
| WorkflowFunctionId                   | int           | Nie          | Unikatowy identyfikator funkcji |
| WorkflowFunctionName                 | nvarchar (50)  | Tak         | Nazwa funkcji |
| WorkflowFunctionDisplayName          | nvarchar (255) | Nie          | Nazwa funkcji, która ma być wyświetlana w interfejsie użytkownika |
| WorkflowFunctionDescription          | nvarchar (255) | Tak         | Opis funkcji przepływu pracy |
| WorkflowFunctionIsConstructor        | bit           | Nie          | Określa, czy funkcja przepływu pracy jest konstruktorem przepływu pracy |
| WorkflowFunctionParameterId          | int           | Nie          | Unikatowy identyfikator parametru funkcji |
| WorkflowFunctionParameterName        | nvarchar (50)  | Nie          | Nazwa parametru funkcji |
| WorkflowFunctionParameterDisplayName | nvarchar (255) | Nie          | Nazwa parametru funkcji, który ma być wyświetlany w interfejsie użytkownika |
| WorkflowFunctionParameterDataTypeId  | int           | Nie          | Unikatowy identyfikator typu danych skojarzonego z parametrem funkcji przepływu pracy |
| WorkflowParameterDataTypeName        | nvarchar (50)  | Nie          | Nazwa typu danych skojarzonego z parametrem funkcji przepływu pracy |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Ten widok przedstawia właściwości zdefiniowane dla przepływu pracy. Dla każdej właściwości ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Szczegóły właściwości przepływu pracy

| Nazwa                         | Typ          | Może mieć wartość null | Opis                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName              | nvarchar (50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName       | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika |
| ApplicationEnabled           | bit           | Nie          | Określa, czy aplikacja jest obecnie włączona.<br />**Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają na łańcucha bloków i dane dotyczące tych kontraktów pozostają w bazie danych. |
| WorkflowId                   | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| WorkflowName                 | nvarchar (50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName          | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana dla przepływu pracy w interfejsie użytkownika |
| WorkflowDescription          | nvarchar (255) | Tak         | Opis przepływu pracy |
| WorkflowPropertyID           | int           | Nie          | Unikatowy identyfikator właściwości przepływu pracy |
| WorkflowPropertyName         | nvarchar (50)  | Nie          | Nazwa właściwości. |
| WorkflowPropertyDescription  | nvarchar (255) | Tak         | Opis właściwości |
| WorkflowPropertyDisplayName  | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika |
| WorkflowPropertyWorkflowId   | int           | Nie          | Identyfikator przepływu pracy, z którym skojarzona jest ta właściwość. |
| WorkflowPropertyDataTypeId   | int           | Nie          | Identyfikator typu danych zdefiniowanego dla właściwości |
| WorkflowPropertyDataTypeName | nvarchar (50)  | Nie          | Nazwa typu danych zdefiniowanego dla właściwości |
| WorkflowPropertyIsState      | bit           | Nie          | To pole określa, czy ta właściwość przepływu pracy zawiera stan przepływu pracy |

## <a name="vwworkflowstate"></a>vwWorkflowState

Ten widok przedstawia właściwości skojarzone z przepływem pracy. W przypadku każdego kontraktu ten widok zawiera następujące dane:

-   Skojarzona definicja aplikacji
-   Skojarzona definicja przepływu pracy
-   Informacje o stanie przepływu pracy

| Nazwa                         | Typ          | Może mieć wartość null | Opis                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nie          | Unikatowy identyfikator aplikacji |
| ApplicationName              | nvarchar (50)  | Nie          | Nazwa aplikacji |
| ApplicationDisplayName       | nvarchar (255) | Nie          | Opis aplikacji |
| ApplicationEnabled           | bit           | Nie          | Określa, czy aplikacja jest obecnie włączona.<br />**Uwaga:** Mimo że aplikacja może być odzwierciedlona jako wyłączona w bazie danych, skojarzone kontrakty pozostają na łańcucha bloków i dane dotyczące tych kontraktów pozostają w bazie danych. |
| WorkflowId                   | int           | Nie          | Unikatowy identyfikator przepływu pracy |
| WorkflowName                 | nvarchar (50)  | Nie          | Nazwa przepływu pracy |
| WorkflowDisplayName          | nvarchar (255) | Nie          | Nazwa wyświetlana w interfejsie użytkownika dla przepływu pracy |
| WorkflowDescription          | nvarchar (255) | Tak         | Opis przepływu pracy |
| WorkflowStateID              | int           | Nie          | Unikatowy identyfikator stanu |
| WorkflowStateName            | nvarchar (50)  | Nie          | Nazwa stanu |
| WorkflowStateDisplayName     | nvarchar (255) | Nie          | Nazwa, która ma być wyświetlana w interfejsie użytkownika dla stanu |
| WorkflowStateDescription     | nvarchar (255) | Tak         | Opis stanu przepływu pracy |
| WorkflowStatePercentComplete | int           | Nie          | Ta wartość określa wartość procentową ukończenia przepływu pracy w tym stanie |
| WorkflowStateValue           | nvarchar (50)  | Nie          | Wartość stanu |
| WorkflowStateStyle           | nvarchar (50)  | Nie          | Opis tekstowy, który zawiera wskazówki dla klientów na temat sposobu renderowania tego stanu w interfejsie użytkownika. Obsługiwane Stany obejmują *sukces* i *Niepowodzenie* |
