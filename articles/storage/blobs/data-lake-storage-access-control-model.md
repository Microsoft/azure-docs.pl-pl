---
title: Model kontroli dostępu dla Azure Data Lake Storage Gen2 | Microsoft Docs
description: Dowiedz się, jak skonfigurować dostęp na poziomie kontenera, katalogu i pliku na kontach z hierarchiczną przestrzenią nazw.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.openlocfilehash: e5f62456b85791bad5bb66f3abf67e523558d76e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650392"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Model kontroli dostępu w Azure Data Lake Storage Gen2

Data Lake Storage Gen2 obsługuje następujące mechanizmy autoryzacji:

- Autoryzacja klucza współdzielonego
- Autoryzacja sygnatury dostępu współdzielonego (SAS)
- Kontrola dostępu oparta na rolach (Azure RBAC)
- Listy kontroli dostępu (ACL)

[Autoryzacja klucza udostępnionego i sygnatury dostępu współdzielonego](#shared-key-and-shared-access-signature-sas-authorization) udziela użytkownikowi (lub aplikacji) bez konieczności posiadania tożsamości w Azure Active Directory (Azure AD). Te dwie formy uwierzytelniania nie mają wpływu na usługi Azure RBAC i listy kontroli dostępu.

Usługa Azure RBAC i lista ACL wymagają, aby użytkownik (lub aplikacja) miał tożsamość w usłudze Azure AD.  Dzięki funkcji RBAC platformy Azure można udzielić dostępu "Gruby ziarnisty" do danych konta magazynu, takiego jak dostęp do odczytu lub zapisu do **wszystkich** danych na koncie magazynu, podczas gdy listy kontroli dostępu umożliwiają udzielenie "szczegółowego" dostępu, takiego jak zapis do określonego katalogu lub pliku.  

Ten artykuł koncentruje się na RBAC i listach kontroli dostępu platformy Azure oraz sposobie, w jaki system szacuje je ze sobą w celu podejmowania decyzji dotyczących autoryzacji zasobów konta magazynu.

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>Kontrola dostępu oparta na rolach (Azure RBAC)

Funkcja RBAC platformy Azure używa przypisań ról do stosowania zestawów uprawnień do [podmiotów zabezpieczeń](../../role-based-access-control/overview.md#security-principal). Podmiot zabezpieczeń to obiekt, który reprezentuje użytkownika, grupę, jednostkę usługi lub zarządzaną tożsamość, która jest zdefiniowana w Azure Active Directory (AD). Zestaw uprawnień może dać podmiotowi zabezpieczeń "duże ziarno" poziom dostępu, taki jak dostęp do odczytu lub zapisu do **wszystkich** danych na koncie magazynu lub **wszystkich** danych w kontenerze. 

Poniższe role zezwalają podmiotowi zabezpieczeń na dostęp do danych na koncie magazynu.

|Rola|Opis|
|--|--|
| [Właściciel danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Pełny dostęp do kontenerów i danych magazynu obiektów BLOB. Ten dostęp pozwala podmiotowi zabezpieczeń ustawić właściciela elementu i zmodyfikować listy ACL wszystkich elementów. |
| [Współautor danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Dostęp do odczytu, zapisu i usuwania kontenerów obiektów BLOB Storage i obiektów BLOB. Ten dostęp nie zezwala podmiotowi zabezpieczeń na ustawienie własności elementu, ale może modyfikować listę kontroli dostępu elementów należących do podmiotu zabezpieczeń. |
| [Czytelnik danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | Odczytywanie i wyświetlanie listy kontenerów i obiektów blob magazynu obiektów BLOB. |

Role, takie jak [właściciel](../../role-based-access-control/built-in-roles.md#owner), [współautor](../../role-based-access-control/built-in-roles.md#contributor), [czytelnik](../../role-based-access-control/built-in-roles.md#reader)i [konto magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor) , umożliwiają podmiotowi zabezpieczeń Zarządzanie kontem magazynu, ale nie zapewniają dostępu do danych w ramach tego konta. Jednak te role (z wyłączeniem **czytnika**) mogą uzyskać dostęp do kluczy magazynu, które mogą być używane w różnych narzędziach klienta, aby uzyskać dostęp do danych.

## <a name="access-control-lists-acls"></a>Listy kontroli dostępu (ACL)

Listy ACL umożliwiają stosowanie "bardziej precyzyjnego" ziarna dostępu do katalogów i plików. *Lista ACL* to konstrukcja z uprawnieniami, która zawiera serię *wpisów listy ACL*. Każdy wpis listy ACL kojarzy podmiot zabezpieczeń z poziomem dostępu.  Aby dowiedzieć się więcej, zobacz [listy kontroli dostępu (ACL) w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="how-permissions-are-evaluated"></a>Jak są oceniane uprawnienia

Podczas autoryzacji opartej na zabezpieczeniach głównych uprawnienia są oceniane w następującej kolejności.

: jeden: &nbsp; &nbsp; przypisania ról platformy Azure są oceniane jako pierwsze i mają pierwszeństwo przed wszelkimi przypisaniami listy ACL.

: dwa: &nbsp; &nbsp; Jeśli operacja jest w pełni autoryzowana na podstawie przypisywania ról platformy Azure, listy ACL nie są oceniane wcale.

: trzy: &nbsp; &nbsp; Jeśli operacja nie została w pełni autoryzowana, są oceniane listy ACL.

> [!div class="mx-imgBorder"]
> ![przepływ uprawnień w usłudze Data Lake Storage](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

Ze względu na sposób, w jaki uprawnienia dostępu są oceniane przez system, **nie można** użyć listy ACL w celu **ograniczenia** dostępu, który został już udzielony przez przypisanie roli. Wynika to z faktu, że system najpierw szacuje przypisania ról platformy Azure, a jeśli przypisanie przyznaje wystarczające uprawnienia dostępu, listy ACL są ignorowane. 

Na poniższym diagramie przedstawiono przepływ uprawnień dla trzech typowych operacji: wyświetlanie zawartości katalogu, odczytywanie pliku i zapisywanie pliku.

> [!div class="mx-imgBorder"]
> ![przykład przepływu uprawnień w usłudze Data Lake Storage](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>Tabela uprawnień: łączenie RBAC i listy ACL platformy Azure

W poniższej tabeli przedstawiono sposób łączenia ról platformy Azure i wpisów listy kontroli dostępu, dzięki czemu podmiot zabezpieczeń może wykonywać operacje wymienione w kolumnie **operacja** . W tej tabeli przedstawiono kolumnę, która reprezentuje każdy poziom fikcyjnej hierarchii katalogów. Istnieje kolumna katalogu głównego kontenera ( `/` ), podkatalog o nazwie **Oregon**, podkatalog katalogu Oregon o nazwie **Portland** oraz plik tekstowy w katalogu w Portland o nazwie **Data.txt**. Pojawiające się w tych kolumnach są [krótkimi](data-lake-storage-access-control.md#short-forms-for-permissions) reprezentacjami wpisów listy kontroli dostępu wymaganych do udzielenia uprawnień.  _Nie dotyczy (nie ma zastosowania_) pojawia się w kolumnie, Jeśli wpis listy ACL nie jest wymagany do wykonania operacji.

|    Operacja             | Przypisana rola platformy Azure               |    /        | Oregon     | Biura | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Odczytaj Data.txt            |   Właściciel danych obiektu blob usługi Storage        | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |  
|                          |   Współautor danych obiektu blob usługi Storage  | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Czytelnik danych obiektu blob usługi Storage       | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Brak                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Dołącz do Data.txt       |   Właściciel danych obiektu blob usługi Storage        | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Współautor danych obiektu blob usługi Storage  | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Czytelnik danych obiektu blob usługi Storage       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   Brak                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Usuń Data.txt          |   Właściciel danych obiektu blob usługi Storage        | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Współautor danych obiektu blob usługi Storage  | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Czytelnik danych obiektu blob usługi Storage       | `--X`    | `--X`    | `-WX`     | Nie dotyczy    |
|                          |   Brak                           | `--X`    | `--X`    | `-WX`     | Nie dotyczy    |
| Utwórz Data.txt          |   Właściciel danych obiektu blob usługi Storage        | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Współautor danych obiektu blob usługi Storage  | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Czytelnik danych obiektu blob usługi Storage       | `--X`    | `--X`    | `-WX`     | Nie dotyczy    |
|                          |   Brak                           | `--X`    | `--X`    | `-WX`     | Nie dotyczy    |
| Staw                   |   Właściciel danych obiektu blob usługi Storage        | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Współautor danych obiektu blob usługi Storage  | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Czytelnik danych obiektu blob usługi Storage       | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Brak                           | `R-X`    | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
| /Oregon/listy            |   Właściciel danych obiektu blob usługi Storage        | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Współautor danych obiektu blob usługi Storage  | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Czytelnik danych obiektu blob usługi Storage       | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Brak                           | `--X`    | `R-X`    | NIE DOTYCZY       | NIE DOTYCZY    |
| /Oregon/Portland/listy   |   Właściciel danych obiektu blob usługi Storage        | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Współautor danych obiektu blob usługi Storage  | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Czytelnik danych obiektu blob usługi Storage       | NIE DOTYCZY      | NIE DOTYCZY      | NIE DOTYCZY       | NIE DOTYCZY    |
|                          |   Brak                           | `--X`    | `--X`    | `R-X`     | Nie dotyczy    |


> [!NOTE] 
> Aby wyświetlić zawartość kontenera w Eksplorator usługi Azure Storage, podmioty zabezpieczeń muszą [zalogować się do Eksplorator usługi Storage za pomocą usługi Azure AD](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#add-a-resource-via-azure-ad), a (co najmniej) mieć dostęp do odczytu (R--) do folderu głównego ( `\` ) kontenera. Ten poziom uprawnień daje im możliwość wyświetlania zawartości folderu głównego. Jeśli nie chcesz, aby zawartość folderu głównego była widoczna, możesz przypisać im rolę [czytelnik](../../role-based-access-control/built-in-roles.md#reader) . Dzięki tej roli będzie można wyświetlić listę kontenerów na koncie, ale nie zawartość kontenera. Następnie można udzielić dostępu do określonych katalogów i plików przy użyciu list ACL.   

## <a name="security-groups"></a>Grupy zabezpieczeń

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Limity przypisań ról i listy ACL na platformie Azure

Korzystanie z grup zmniejsza maksymalną liczbę przypisań ról na subskrypcję i maksymalną liczbę wpisów listy ACL na plik lub katalog. W poniższej tabeli opisano te limity.

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>Autoryzacja klucza współdzielonego i sygnatury dostępu współdzielonego (SAS)

Azure Data Lake Storage Gen2 obsługuje również metody uwierzytelniania [klucza współużytkowanego](/rest/api/storageservices/authorize-with-shared-key) i [sygnatury](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) dostępu współdzielonego. Charakterystyka tych metod uwierzytelniania polega na tym, że żadna tożsamość nie jest skojarzona z obiektem wywołującym i dlatego nie można wykonać autoryzacji podmiotu zabezpieczeń na podstawie uprawnień.

W przypadku klucza współużytkowanego obiekt wywołujący skutecznie uzyskuje dostęp "Administrator", co oznacza pełny dostęp do wszystkich operacji na wszystkich zasobach, takich jak dane, Ustawianie właściciela i zmienianie list kontroli dostępu.

Tokeny sygnatury dostępu współdzielonego zawierają dozwolone uprawnienia jako część tokenu. Uprawnienia zawarte w tokenie SAS są skutecznie stosowane do wszystkich decyzji dotyczących autoryzacji, ale nie są wykonywane żadne dodatkowe sprawdzenia listy ACL.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat list kontroli dostępu, zobacz  [listy kontroli dostępu (ACL) w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).