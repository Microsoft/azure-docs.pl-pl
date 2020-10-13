---
title: Odwołanie do atrybutu SAP SuccessFactors
description: Dowiedz się, jakie atrybuty z SuccessFactors są obsługiwane przez inicjowanie obsługi SuccessFactors-HR
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: ef2da377c7720cfb7b431d1ce0fed56656a2b8c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87808511"
---
# <a name="sap-successfactors-attribute-reference"></a>Odwołanie do atrybutu SAP SuccessFactors

W tym artykule znajdziesz informacje na temat:

- [SuccessFactors obiektów i atrybutów](#supported-successfactors-entities-and-attributes)
- [Domyślne mapowanie atrybutów](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Obsługiwane jednostki i atrybuty SuccessFactors

W poniższej tabeli przechwycono listę atrybutów SuccessFactors uwzględnionych domyślnie w następujących dwóch aplikacjach aprowizacji:

- [SuccessFactors Active Directory aprowizacji użytkowników](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors do aprowizacji użytkowników usługi Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

Zapoznaj się z informacjami dotyczącymi [integracji oprogramowania SAP SuccessFactors](./sap-successfactors-integration-reference.md#retrieving-additional-attributes) , aby zwiększyć schemat dodatkowych atrybutów. 

| \# | Jednostka SuccessFactors                  | SuccessFactors — atrybut     | Typ operacji |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Odczyt           |
| 2  | PerPerson                              | personId                     | Odczyt           |
| 3  | PerPerson                              | perPersonUuid                | Odczyt           |
| 4  | PerPersonal                            | displayName                  | Odczyt           |
| 5  | PerPersonal                            | firstName                    | Odczyt           |
| 6  | PerPersonal                            | płeć                       | Odczyt           |
| 7  | PerPersonal                            | lastName                     | Odczyt           |
| 8  | PerPersonal                            | middleName                   | Odczyt           |
| 9  | PerPersonal                            | preferowany                | Odczyt           |
| 10 | Użytkownik                                   | addressLine1                 | Odczyt           |
| 11 | Użytkownik                                   | addressLine2                 | Odczyt           |
| 12 | Użytkownik                                   | addressLIne3                 | Odczyt           |
| 13 | Użytkownik                                   | businessPhone                | Odczyt           |
| 14 | Użytkownik                                   | cellPhone                    | Odczyt           |
| 15 | Użytkownik                                   | city                         | Odczyt           |
| 16 | Użytkownik                                   | country                      | Odczyt           |
| 17 | Użytkownik                                   | custom01                     | Odczyt           |
| 18 | Użytkownik                                   | custom02                     | Odczyt           |
| 19 | Użytkownik                                   | custom03                     | Odczyt           |
| 20 | Użytkownik                                   | custom04                     | Odczyt           |
| 21 | Użytkownik                                   | custom05                     | Odczyt           |
| 22 | Użytkownik                                   | custom06                     | Odczyt           |
| 23 | Użytkownik                                   | custom07                     | Odczyt           |
| 24 | Użytkownik                                   | custom08                     | Odczyt           |
| 25 | Użytkownik                                   | custom09                     | Odczyt           |
| 26 | Użytkownik                                   | custom10                     | Odczyt           |
| 27 | Użytkownik                                   | custom11                     | Odczyt           |
| 28 | Użytkownik                                   | custom12                     | Odczyt           |
| 29 | Użytkownik                                   | custom13                     | Odczyt           |
| 30 | Użytkownik                                   | custom14                     | Odczyt           |
| 31 | Użytkownik                                   | empId                        | Odczyt           |
| 32 | Użytkownik                                   | homePhone                    | Odczyt           |
| 33 | Użytkownik                                   | jobFamily                    | Odczyt           |
| 34 | Użytkownik                                   | pseudonim                     | Odczyt           |
| 35 | Użytkownik                                   | stan                        | Odczyt           |
| 36 | Użytkownik                                   | timeZone                     | Odczyt           |
| 37 | Użytkownik                                   | nazwa użytkownika                     | Odczyt           |
| 38 | Użytkownik                                   | Kod pocztowy                      | Odczyt           |
| 39 | PerPhone                               | areaCode                     | Odczyt           |
| 40 | PerPhone                               | countryCode                  | Odczyt           |
| 41 | PerPhone                               | rozszerzenie                    | Odczyt           |
| 42 | PerPhone                               | phoneNumber                  | Odczyt           |
| 43 | PerPhone                               | numer telefonu                    | Odczyt           |
| 44 | PerEmail                               | emailAddress                 | Odczyt, zapis    |
| 45 | PerEmail                               | adres e-mail                    | Odczyt           |
| 46 | EmpEmployment                          | firstDateWorked              | Odczyt           |
| 47 | EmpEmployment                          | lastDateWorked               | Odczyt           |
| 48 | EmpEmployment                          | userId                       | Odczyt           |
| 49 | EmpEmployment                          | isContingentWorker           | Odczyt           |
| 50 | EmpJob                                 | countryOfCompany             | Odczyt           |
| 51 | EmpJob                                 | emplStatus                   | Odczyt           |
| 52 | EmpJob                                 | endDate                      | Odczyt           |
| 53 | EmpJob                                 | startDate                    | Odczyt           |
| 54 | EmpJob                                 | Stanowiska                     | Odczyt           |
| 55 | EmpJob                                 | pozycja                     | Odczyt           |
| 65 | EmpJob                                 | customString13               | Odczyt           |
| 56 | EmpJob                                 | managerId                    | Odczyt           |
| 57 | EmpJob \. BusinessUnit                   | businessUnit                 | Odczyt           |
| 58 | EmpJob \. BusinessUnit                   | businessUnitId               | Odczyt           |
| 59 | \.Firma EmpJob                        | company                      | Odczyt           |
| 60 | \.Firma EmpJob                        | companyId                    | Odczyt           |
| 61 | EmpJob \. firmy \. CountryOfRegistration | twoCharCountryCode           | Odczyt           |
| 62 | EmpJob \. CostCenter                     | costCenter                   | Odczyt           |
| 63 | EmpJob \. CostCenter                     | costCenterId                 | Odczyt           |
| 64 | EmpJob \. CostCenter                     | costCenterDescription        | Odczyt           |
| 65 | \.Dział EmpJob                     | działu,                   | Odczyt           |
| 66 | \.Dział EmpJob                     | departmentId                 | Odczyt           |
| 67 | EmpJob \.                       | przegrod                     | Odczyt           |
| 68 | EmpJob \.                       | divisionId                   | Odczyt           |
| 69 | EmpJob \. JobCode                        | jobCode                      | Odczyt           |
| 70 | EmpJob \. JobCode                        | jobCodeId                    | Odczyt           |
| 71 | \.Lokalizacja EmpJob                       | Lokalizacja                 | Odczyt           |
| 72 | \.Lokalizacja EmpJob                       | officeLocationAddress        | Odczyt           |
| 73 | \.Lokalizacja EmpJob                       | officeLocationCity           | Odczyt           |
| 74 | \.Lokalizacja EmpJob                       | officeLocationCustomString4  | Odczyt           |
| 75 | \.Lokalizacja EmpJob                       | officeLocationZipCode        | Odczyt           |
| 76 | EmpJob \. PayGrade                       | payGrade                     | Odczyt           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Odczyt           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Odczyt           |

## <a name="default-attribute-mapping"></a>Domyślne mapowanie atrybutów

Poniższa tabela zawiera domyślne mapowanie atrybutów między SuccessFactorsmi wymienionymi powyżej i atrybutami AD/Azure AD. W bloku "mapowanie" aplikacji aprowizacji usługi Azure AD można zmodyfikować to mapowanie domyślne w celu uwzględnienia atrybutów z powyższej listy. 

| \# | Jednostka SuccessFactors                  | SuccessFactors — atrybut | Domyślne mapowanie atrybutów AD/Azure AD   | Przetwarzanie uwagi                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | IDPracownika                              | Używane jako pasujący atrybut                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Nie zamapowano \- użyte jako zakotwiczenie źródła\] | Podczas synchronizacji początkowej usługa aprowizacji łączy personUuid z istniejącym objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | Nie dotyczy                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | Nie dotyczy                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | Nie dotyczy                                                                                           |
| 6  | Użytkownik                                   | addressLine1             | streetAddress                           | Nie dotyczy                                                                                           |
| 7  | Użytkownik                                   | city                     | l                                       | Nie dotyczy                                                                                           |
| 8  | Użytkownik                                   | country                  | co                                      | Nie dotyczy                                                                                           |
| 9  | Użytkownik                                   | stan                    | st                                      | Nie dotyczy                                                                                           |
| 10 | Użytkownik                                   | nazwa użytkownika                 | samAccountName                          | Nie dotyczy                                                                                           |
| 11 | Użytkownik                                   | Kod pocztowy                  | Pocztowy                              | Nie dotyczy                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail (poczta)                                    | Nie dotyczy                                                                                           |
| 13 | EmpJob                                 | Stanowiska                 | title                                   | Nie dotyczy                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | Nie dotyczy                                                                                           |
| 15 | EmpJob \. firmy \. CountryOfRegistration | twoCharCountryCode       | c                                       | Nie dotyczy                                                                                           |
| 16 | \.Dział EmpJob                     | działu,               | działu,                              | Nie dotyczy                                                                                           |
| 17 | EmpJob \.                       | przegrod                 | company                                 | Nie dotyczy                                                                                           |
| 18 | \.Lokalizacja EmpJob                       | officeLocationAddress    | streetAddress                           | Nie dotyczy                                                                                           |
| 19 | \.Lokalizacja EmpJob                       | officeLocationZipCode    | Pocztowy                              | Nie dotyczy                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Jeśli activeEmploymentsCount = 0, wyłącz account\.                                           |
