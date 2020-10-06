---
title: Rozwiązywanie problemów z usługą Azure Data Share
description: Dowiedz się, jak rozwiązywać problemy z zaproszeniami i błędami podczas tworzenia lub otrzymywania udziałów danych za pomocą udziału danych platformy Azure.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 10/02/2020
ms.openlocfilehash: 620fe1e693a177123e166220ab94bbd74c4826ff
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761536"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Rozwiązywanie typowych problemów w usłudze Azure Data Share 

W tym artykule pokazano, jak rozwiązywać typowe problemy z udziałem danych platformy Azure. 

## <a name="azure-data-share-invitations"></a>Zaproszenia udziału danych platformy Azure 

W niektórych przypadkach, gdy nowy użytkownik kliknie opcję **Zaakceptuj zaproszenie** z zaproszenia e-mail, które zostało wysłane, może zostać wyświetlona pusta lista zaproszeń. 

![Brak zaproszeń](media/no-invites.png)

Może to być spowodowane jedną z następujących przyczyn:

* **Usługa Azure Data Share nie jest zarejestrowana jako dostawca zasobów żadnej subskrypcji platformy Azure w dzierżawie platformy Azure.** Ten problem wystąpi, jeśli w dzierżawie platformy Azure nie ma zasobu usługi Data Share. Gdy tworzysz zasób usługi Azure Data Share, powoduje to automatyczne zarejestrowanie dostawcy zasobów w subskrypcji platformy Azure. Możesz również ręcznie zarejestrować usługę Data Share, wykonując następujące kroki. Aby wykonać te kroki, musisz mieć rolę Współautor platformy Azure.

    1. W witrynie Azure Portal przejdź do sekcji **Subskrypcje**
    1. Wybierz subskrypcję, której chcesz użyć do utworzenia zasobu usługi Azure Data Share
    1. Kliknij pozycję **Dostawcy zasobów**
    1. Wyszukaj pozycję **Microsoft.DataShare**
    1. Kliknij pozycję **Zarejestruj** 

    Aby wykonać te kroki, musisz mieć [rolę współautor platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) w ramach subskrypcji platformy Azure. 

* **Zaproszenie jest wysyłane na alias adresu e-mail, a nie na adres e-mail logowania do platformy Azure.** Jeśli zarejestrowano usługę Azure Data Share lub już utworzono zasób usługi Data Share w dzierżawie platformy Azure, ale zaproszenie nadal nie jest widoczne, być może dostawca wprowadził jako adresata alias adresu e-mail, a nie adres e-mail logowania do platformy Azure. Skontaktuj się z dostawcą danych i upewnij się, że zaproszenie zostało wysłane na adres e-mail logowania do platformy Azure, a nie na alias adresu e-mail.

* **Zaproszenie zostało już zaakceptowane.** Link w wiadomości e-mail prowadzi do strony Zaproszenie do usługi Data Share w witrynie Azure Portal, na której znajduje się lista zawierająca tylko oczekujące zaproszenia. Jeśli zaproszenie zostało już zaakceptowane, nie będzie wyświetlane na stronie Zaproszenie do usługi Data Share. Przejdź do zasobu usługi Data Share, którego użyto w celu zaakceptowania zaproszenia, aby wyświetlić odebrane udziały i skonfigurować ustawienia docelowego klastra usługi Azure Data Explorer.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Błąd podczas tworzenia lub otrzymywania nowego udziału

"Nie można dodać zestawów danych"

"Nie można zmapować zestawów danych"

"Nie można udzielić dostępu do zasobów udziału danych x do wartości y"

"Nie masz odpowiednich uprawnień do x"

"Nie można dodać uprawnień do zapisu dla konta udziału danych platformy Azure do co najmniej jednego wybranego zasobu"

Jeśli podczas tworzenia nowego udziału lub mapowania zestawów danych pojawi się którykolwiek z powyższych błędów, może to wynikać z niewystarczających uprawnień do usługi Azure Data Store. Zobacz [role i wymagania](concepts-roles-permissions.md) dotyczące wymaganych uprawnień. 

Musisz mieć uprawnienia do zapisu, aby udostępniać lub odbierać dane z magazynu danych platformy Azure, który zwykle istnieje w roli współautor. 

Jeśli tworzysz lub otrzymujesz dane z magazynu danych platformy Azure po raz pierwszy, musisz również mieć uprawnienie *Microsoft. Autoryzacja/przydziały ról/* uprawnienia do zapisu, które zwykle istnieje w roli właściciela. Nawet jeśli utworzono zasób magazynu danych platformy Azure, nie jest on automatycznie tworzony jako właściciel zasobu. Dzięki poprawnym uprawnieniu usługa Udostępnianie danych platformy Azure automatycznie udziela dostępu tożsamości zarządzanej przez zasób udział danych do magazynu danych. Wykonanie tego procesu może potrwać kilka minut. Jeśli wystąpi awaria z powodu tego opóźnienia, spróbuj ponownie za kilka minut.

Udostępnianie oparte na języku SQL wymaga dodatkowych uprawnień. Aby uzyskać szczegółową listę wymagań wstępnych, zobacz [udostępnianie ze źródeł SQL](how-to-share-from-sql.md) .

## <a name="snapshot-failed"></a>Migawka nie powiodła się
Migawka może zakończyć się niepowodzeniem z różnych powodów. Szczegółowy komunikat o błędzie można znaleźć, klikając pozycję czas rozpoczęcia migawki, a następnie stan każdego zestawu danych. Poniżej przedstawiono przyczyny niepowodzenia migawek:

* Udział danych nie ma uprawnień do odczytu ze źródłowego magazynu danych lub zapisu do docelowego magazynu danych. Zobacz [role i wymagania,](concepts-roles-permissions.md) Aby uzyskać szczegółowe wymagania dotyczące uprawnień. Jeśli tworzysz migawkę po raz pierwszy, może upłynąć kilka minut, zanim zasób udostępniania danych uzyska dostęp do magazynu danych platformy Azure. Poczekaj kilka minut i spróbuj ponownie.
* Połączenie udostępniania danych ze źródłem lub docelowym magazynem danych jest blokowane przez zaporę.
* Udostępniony zestaw danych lub źródłowy lub docelowy magazyn z danymi jest usuwany.
* W przypadku udostępniania SQL typy danych nie są obsługiwane przez proces migawki ani docelowy magazyn danych. Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [udostępnianie ze źródeł SQL](how-to-share-from-sql.md#supported-data-types) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) . 

Aby dowiedzieć się, jak odbierać dane, przejdź do samouczka [akceptowanie i odbieranie danych](subscribe-to-data-share.md) .

