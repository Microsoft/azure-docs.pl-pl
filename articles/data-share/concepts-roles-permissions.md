---
title: Role i wymagania dotyczące usługi Azure Data Share
description: Dowiedz się więcej o uprawnieniach wymaganych do udostępniania i odbierania danych przy użyciu udziału danych platformy Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: efb86dbcbe7619ff6727c5e7374835dc3fc7d731
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220503"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Role i wymagania dotyczące usługi Azure Data Share 

W tym artykule opisano role i uprawnienia wymagane do udostępniania i odbierania danych przy użyciu usługi udziału danych platformy Azure. 

## <a name="roles-and-requirements"></a>Role i wymagania

Za pomocą usługi Azure Data Share można udostępniać dane bez wymieniania poświadczeń między dostawcą danych i konsumentem. Usługa Azure Data Share używa tożsamości zarządzanych (wcześniej znanych jako MSIs) do uwierzytelniania w usłudze Azure Data Store. 

Tożsamość zarządzana zasobu udziału danych platformy Azure musi mieć przyznany dostęp do magazynu danych platformy Azure. Usługa udostępniania danych platformy Azure używa tej tożsamości zarządzanej do odczytu i zapisu danych na potrzeby udostępniania opartego na migawce oraz do ustanawiania symbolicznego linku do udostępniania miejscowego. 

Aby udostępnić lub odebrać dane z magazynu danych platformy Azure, użytkownik musi mieć co najmniej następujące uprawnienia. W przypadku udostępniania opartego na języku SQL wymagane są dodatkowe uprawnienia.

* Uprawnienie do zapisu w magazynie danych platformy Azure. Zwykle to uprawnienie istnieje w roli **współautor** .
* Uprawnienie do tworzenia przypisania roli w magazynie danych Azure. Zazwyczaj uprawnienia do tworzenia przypisań ról istnieją w roli **właściciela** , roli administrator dostępu użytkowników lub roli niestandardowej z firmą Microsoft. uprawnienia do przypisywania ról/uprawnień do zapisu. To uprawnienie nie jest wymagane, jeśli zarządzana tożsamość zasobu udziału danych ma już dostęp do magazynu danych platformy Azure. W poniższej tabeli znajduje się wymagana rola.

Poniżej znajduje się Podsumowanie ról przypisanych do zarządzanej tożsamości zasobu udział danych:

|**Typ magazynu danych**|**Źródłowy magazyn danych Dostawca danych**|**Docelowy magazyn danych odbiorcy danych**|
|---|---|---|
|Azure Blob Storage| Czytelnik danych obiektu blob usługi Storage | Współautor danych obiektu blob usługi Storage
|Azure Data Lake Gen1 | Właściciel | Nieobsługiwane
|Azure Data Lake Gen2 | Czytelnik danych obiektu blob usługi Storage | Współautor danych obiektu blob usługi Storage
|Klaster usługi Azure Data Explorer | Współautor | Współautor
|

W przypadku udostępniania opartego na języku SQL użytkownik musi zostać utworzony przez dostawcę zewnętrznego w Azure SQL Database z taką samą nazwą jak zasób udziału danych platformy Azure. Do utworzenia tego użytkownika wymagane jest uprawnienie administratora Azure Active Directory. Poniżej znajduje się Podsumowanie uprawnień wymaganych przez użytkownika programu SQL.

|**Typ SQL Database**|**Dostawca danych uprawnienie użytkownika SQL**|**Uprawnienie użytkownika SQL dla danych klienta**|
|---|---|---|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics (dawniej SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|

### <a name="data-provider"></a>Dostawca danych

Aby dodać zestaw danych w udziale danych platformy Azure, zarządzana tożsamość zasobu udziału danych dostawcy musi mieć przyznane uprawnienia dostępu do źródłowego magazynu danych platformy Azure. Na przykład w przypadku konta magazynu zarządzana tożsamość zasobu udziału danych jest przyznana roli czytnika danych obiektu blob magazynu. 

Jest to wykonywane automatycznie przez usługę udziału danych platformy Azure, gdy użytkownik dodaje zestaw danych za pośrednictwem Azure Portal i użytkownik ma odpowiednie uprawnienia. Na przykład użytkownik jest właścicielem magazynu danych platformy Azure lub jest członkiem roli niestandardowej, która ma przypisane uprawnienie Microsoft. Autoryzacja/przydziały/uprawnienia do zapisu. 

Użytkownik może również mieć właściciela magazynu danych platformy Azure, dodając tożsamość zarządzaną zasobu udział danych do magazynu danych platformy Azure ręcznie. Tę akcję należy wykonać tylko raz dla zasobu udziału danych.

Aby ręcznie utworzyć przypisanie roli dla tożsamości zarządzanej zasobu udział danych, wykonaj poniższe czynności.  

1. Przejdź do magazynu danych platformy Azure.
1. Wybierz pozycję **Access Control (IAM)**.
1. Wybierz pozycję **Dodaj przypisanie roli**.
1. W obszarze *rola*wybierz rolę w powyższej tabeli przypisania roli (na przykład dla konta magazynu wybierz pozycję *czytnik danych magazynu obiektów BLOB*).
1. W obszarze *Wybierz*wpisz nazwę zasobu udziału danych platformy Azure.
1. Kliknij pozycję *Zapisz*.

Aby dowiedzieć się więcej na temat przypisywania ról, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment). Jeśli udostępniasz dane przy użyciu interfejsów API REST, możesz utworzyć przypisanie roli przy użyciu interfejsu API, przywołując [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu API REST](../role-based-access-control/role-assignments-rest.md). 

W przypadku źródeł opartych na języku SQL użytkownik musi zostać utworzony przez dostawcę zewnętrznego w SQL Database z taką samą nazwą jak zasób udziału danych platformy Azure podczas nawiązywania połączenia z usługą SQL Database przy użyciu uwierzytelniania Azure Active Directory. Ten użytkownik musi mieć przyznane uprawnienie *db_datareader* . Przykładowy skrypt wraz z innymi wymaganiami wstępnymi dotyczącymi udostępniania danych opartych na języku SQL można znaleźć w samouczku [Azure SQL Database lub Synapse Analytics](how-to-share-from-sql.md) . 

### <a name="data-consumer"></a>Odbiorca danych
Aby otrzymywać dane, tożsamość zarządzana zasobu udziału danych klienta musi mieć przyznane dostęp do docelowego magazynu danych platformy Azure. Na przykład w przypadku konta magazynu zarządzana tożsamość zasobu udziału danych jest przyznana roli współautor danych obiektu blob magazynu. 

Jest to wykonywane automatycznie przez usługę udziału danych platformy Azure, jeśli użytkownik określi docelowy magazyn danych za pośrednictwem Azure Portal, a użytkownik ma odpowiednie uprawnienia. Na przykład użytkownik jest właścicielem magazynu danych platformy Azure lub jest członkiem roli niestandardowej, która ma przypisane uprawnienie Microsoft. Autoryzacja/przydziały/uprawnienia do zapisu. 

Użytkownik może również mieć właściciela magazynu danych platformy Azure, dodając tożsamość zarządzaną zasobu udział danych do magazynu danych platformy Azure ręcznie. Tę akcję należy wykonać tylko raz dla zasobu udziału danych.

Aby ręcznie utworzyć przypisanie roli dla tożsamości zarządzanej zasobu udział danych, wykonaj poniższe czynności. 

1. Przejdź do magazynu danych platformy Azure.
1. Wybierz pozycję **Access Control (IAM)**.
1. Wybierz pozycję **Dodaj przypisanie roli**.
1. W obszarze *rola*wybierz rolę w powyższej tabeli przypisania roli (na przykład dla konta magazynu wybierz pozycję *czytnik danych magazynu obiektów BLOB*).
1. W obszarze *Wybierz*wpisz nazwę zasobu udziału danych platformy Azure.
1. Kliknij pozycję *Zapisz*.

Aby dowiedzieć się więcej na temat przypisywania ról, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment). Jeśli otrzymujesz dane przy użyciu interfejsów API REST, możesz utworzyć przypisanie roli przy użyciu interfejsu API, przywołując [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu API REST](../role-based-access-control/role-assignments-rest.md). 

W przypadku obiektów docelowych opartych na języku SQL użytkownik musi zostać utworzony przez dostawcę zewnętrznego w SQL Database z taką samą nazwą jak zasób udziału danych platformy Azure podczas nawiązywania połączenia z usługą SQL Database przy użyciu uwierzytelniania Azure Active Directory. Ten użytkownik musi mieć przyznane *db_datareader, db_datawriter db_ddladmin* uprawnienia. Przykładowy skrypt wraz z innymi wymaganiami wstępnymi dotyczącymi udostępniania danych opartych na języku SQL można znaleźć w samouczku [Azure SQL Database lub Synapse Analytics](how-to-share-from-sql.md) . 

## <a name="resource-provider-registration"></a>Rejestracja dostawcy zasobów 

Może być konieczne ręczne zarejestrowanie dostawcy zasobów Microsoft. datashare w ramach subskrypcji platformy Azure w następujących scenariuszach: 

* Przeglądanie zaproszenia udziału danych platformy Azure po raz pierwszy w dzierżawie platformy Azure
* Udostępnianie danych z magazynu danych platformy Azure w innej subskrypcji platformy Azure z zasobu udziału danych platformy Azure
* Odbieranie danych do magazynu danych platformy Azure w innej subskrypcji platformy Azure z zasobu udziału danych platformy Azure

Wykonaj następujące kroki, aby zarejestrować dostawcę zasobów Microsoft. datashare w ramach subskrypcji platformy Azure. Do zarejestrowania dostawcy zasobów potrzebny jest dostęp *współautora* do subskrypcji platformy Azure.

1. W Azure Portal przejdź do **subskrypcji**.
1. Wybierz subskrypcję używaną dla udziału danych platformy Azure.
1. Kliknij pozycję **dostawcy zasobów**.
1. Wyszukaj firmę Microsoft. datashare.
1. Kliknij pozycję **Zarejestruj**.
 
Aby dowiedzieć się więcej na temat dostawcy zasobów, zapoznaj się z tematem [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o rolach na platformie Azure — [Omówienie definicji ról](../role-based-access-control/role-definitions.md)