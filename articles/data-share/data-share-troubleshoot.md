---
title: Rozwiązywanie problemów z usługą Azure Data Share
description: Dowiedz się, jak rozwiązywać problemy z zaproszeniami i błędami podczas tworzenia lub otrzymywania udziałów danych za pomocą udziału danych platformy Azure.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/30/2020
ms.openlocfilehash: 2c67e2b72b7d35f5f7c8e9ccd136d7c10329f531
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513608"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Rozwiązywanie typowych problemów z udziałem danych platformy Azure 

W tym artykule pokazano, jak rozwiązywać typowe problemy z udziałem danych platformy Azure. 

## <a name="azure-data-share-invitations"></a>Zaproszenia udziału danych platformy Azure 

W niektórych przypadkach, gdy nowy użytkownik kliknie opcję **Zaakceptuj zaproszenie** z zaproszenia e-mail, które zostało wysłane, może zostać wyświetlona pusta lista zaproszeń. 

![Brak zaproszeń](media/no-invites.png)

Może to być spowodowane następującymi przyczynami:

* **Usługa udziału danych platformy Azure nie jest zarejestrowana jako dostawca zasobów żadnej subskrypcji platformy Azure w dzierżawie platformy Azure.** Ten problem wystąpi, jeśli w dzierżawie platformy Azure nie ma zasobu udostępniania danych. Podczas tworzenia zasobu udziału danych platformy Azure usługa automatycznie rejestruje dostawcę zasobów w ramach subskrypcji platformy Azure. Możesz również ręcznie zarejestrować usługę udostępniania danych, wykonując następujące kroki. Aby wykonać te czynności, musisz mieć rolę współautor platformy Azure.

    1. W Azure Portal przejdź do **subskrypcji**
    1. Wybierz subskrypcję, której chcesz użyć do utworzenia zasobu udziału danych platformy Azure
    1. Kliknij pozycję **dostawcy zasobów**
    1. Wyszukaj w usłudze **Microsoft. Datashare**
    1. Kliknij pozycję **zarejestruj** . 

    Aby wykonać te kroki, musisz mieć [rolę RBAC współautor platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) . 

* **Zaproszenie jest wysyłane na alias e-mail, a nie na adres e-mail logowania do platformy Azure.** Jeśli zarejestrowano usługę udziału danych platformy Azure lub utworzono już zasób udział danych w dzierżawie platformy Azure, ale nadal nie można zobaczyć zaproszenia, może to być spowodowane tym, że dostawca wprowadził alias e-mail jako adresat zamiast adresu e-mail logowania do platformy Azure. Skontaktuj się z dostawcą danych i upewnij się, że wyśle zaproszenie na adres e-mail logowania do platformy Azure, a nie alias e-mail.

* **Zaproszenie zostało już zaakceptowane.** Link w wiadomości e-mail prowadzi do strony zaproszenia udostępniania danych w Azure Portal, która zawiera tylko Oczekujące zaproszenia. Jeśli zaproszenie zostało już zaakceptowane, nie będzie już wyświetlane na stronie zaproszenia udostępniania danych. Przechodzenie do zasobu udostępniania danych, którego użyto do zaakceptowania zaproszenia do wyświetlania odebranych udziałów i skonfigurowania docelowego ustawienia klastra Eksplorator danych platformy Azure.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Błąd podczas tworzenia lub otrzymywania nowego udziału

"Nie można dodać zestawów danych"

"Nie można zmapować zestawów danych"

"Nie można udzielić dostępu do zasobów udziału danych x do wartości y"

"Nie masz odpowiednich uprawnień do x"

"Nie można dodać uprawnień do zapisu dla konta udziału danych platformy Azure do co najmniej jednego wybranego zasobu"

Jeśli podczas tworzenia nowego udziału lub mapowania zestawów danych pojawi się którykolwiek z powyższych błędów, może to wynikać z niewystarczających uprawnień do usługi Azure Data Store. Zobacz [role i wymagania](concepts-roles-permissions.md) dotyczące wymaganych uprawnień. 

Musisz mieć uprawnienia do zapisu, aby udostępniać lub odbierać dane z magazynu danych platformy Azure, który zwykle istnieje w roli współautor. 

Jeśli tworzysz lub otrzymujesz dane z magazynu danych platformy Azure po raz pierwszy, musisz również mieć uprawnienie *Microsoft. Autoryzacja/przydziały ról/* uprawnienia do zapisu, które zwykle istnieje w roli właściciela. Nawet jeśli utworzono zasób magazynu danych platformy Azure, nie jest on automatycznie tworzony jako właściciel zasobu. Dzięki poprawnym uprawnieniu usługa Udostępnianie danych platformy Azure automatycznie udziela dostępu tożsamości zarządzanej przez zasób udział danych do magazynu danych. Wykonanie tego procesu może potrwać kilka minut. Jeśli wystąpi awaria z powodu tego opóźnienia, spróbuj ponownie za kilka minut.

Udostępnianie oparte na języku SQL wymaga dodatkowych uprawnień. Szczegóły można znaleźć w temacie Rozwiązywanie problemów dotyczących udostępniania opartego na języku SQL.

## <a name="troubleshooting-sql-based-sharing"></a>Rozwiązywanie problemów z udostępnianiem opartym na języku SQL

"Użytkownik x nie istnieje w SQL Database"

Jeśli ten błąd wystąpi podczas dodawania zestawu danych z źródła opartego na języku SQL, może to być spowodowane faktem, że nie utworzono użytkownika dla tożsamości zarządzanej udziału danych platformy Azure w SQL Database.  Aby rozwiązać ten problem, uruchom następujący skrypt:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Jeśli ten błąd wystąpi podczas mapowania zestawu danych na obiekt docelowy oparty na języku SQL, może to być spowodowane faktem, że nie utworzono użytkownika dla tożsamości zarządzanej udziału danych platformy Azure na SQL Server.  Aby rozwiązać ten problem, uruchom następujący skrypt:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Należy pamiętać, że *<share_acc_name>* to nazwa zasobu udziału danych.      

Upewnij się, że zostały spełnione wszystkie wymagania wstępne wymienione w temacie [udostępnianie danych](share-your-data.md) i [akceptowanie i odbieranie danych](subscribe-to-data-share.md) .

## <a name="snapshot-failed"></a>Migawka nie powiodła się
Migawka może zakończyć się niepowodzeniem z różnych powodów. Szczegółowy komunikat o błędzie można znaleźć, klikając pozycję czas rozpoczęcia migawki, a następnie stan każdego zestawu danych. 

Jeśli komunikat o błędzie jest związany z uprawnieniem, sprawdź, czy usługa udostępniania danych ma wymagane uprawnienie. Szczegóły można znaleźć w temacie [role i wymagania](concepts-roles-permissions.md) . Jeśli tworzysz migawkę po raz pierwszy, może upłynąć kilka minut, zanim zasób udostępniania danych uzyska dostęp do magazynu danych platformy Azure. Poczekaj kilka minut i spróbuj ponownie.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) . 

Aby dowiedzieć się, jak odbierać dane, przejdź do samouczka [akceptowanie i odbieranie danych](subscribe-to-data-share.md) .

