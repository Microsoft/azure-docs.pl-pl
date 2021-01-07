---
title: Rozwiązywanie problemów z usługą Azure Data Share
description: Dowiedz się, jak rozwiązywać problemy z zaproszeniami i błędami podczas tworzenia lub odbierania udziałów danych w udziale danych platformy Azure.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: 3aa1c0b8579bd37d2bb51cbde70997131c696813
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964511"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Rozwiązywanie typowych problemów z udziałem danych platformy Azure 

W tym artykule wyjaśniono, jak rozwiązywać typowe problemy w udziale danych platformy Azure. 

## <a name="azure-data-share-invitations"></a>Zaproszenia udziału danych platformy Azure 

W niektórych przypadkach, gdy nowi użytkownicy wybierają pozycję **Zaakceptuj zaproszenie** w wiadomości e-mail z zaproszeniem, mogą wyświetlić pustą listę zaproszeń. 

:::image type="content" source="media/no-invites.png" alt-text="Zrzut ekranu przedstawiający pustą listę zaproszeń.":::

Przyczyną tego problemu może być jedna z następujących przyczyn:

* **Usługa udziału danych platformy Azure nie jest zarejestrowana jako dostawca zasobów żadnej subskrypcji platformy Azure w dzierżawie platformy Azure.** Ten problem występuje, gdy dzierżawa platformy Azure nie ma zasobu udostępniania danych. 

    Gdy tworzysz zasób usługi Azure Data Share, powoduje to automatyczne zarejestrowanie dostawcy zasobów w subskrypcji platformy Azure. Usługę udostępniania danych można zarejestrować ręcznie, wykonując poniższe kroki. Aby wykonać te kroki, potrzebna jest [rola współautor](../role-based-access-control/built-in-roles.md#contributor) dla subskrypcji platformy Azure. 

    1. W witrynie Azure Portal przejdź do sekcji **Subskrypcje**.
    1. Wybierz subskrypcję, której chcesz użyć, aby utworzyć zasób udziału danych platformy Azure.
    1. Wybierz pozycję **dostawcy zasobów**.
    1. Wyszukaj **firmę Microsoft. datashare**.
    1. Wybierz pozycję **Zarejestruj**.

* **Zaproszenie jest wysyłane na alias e-mail, a nie w adresie e-mail logowania do platformy Azure.** Jeśli usługa Azure Data Share została już zarejestrowana lub utworzono zasób udziału danych w dzierżawie platformy Azure, ale nadal nie będzie można zobaczyć zaproszenia, alias poczty e-mail może być wyświetlany jako adresat. Skontaktuj się z dostawcą danych i upewnij się, że zaproszenie zostanie wysłane na adres e-mail logowania platformy Azure, a nie alias e-mail.

* **Zaproszenie zostało już zaakceptowane.** Link w wiadomości e-mail prowadzi do strony **zaproszenia udostępniania danych** w Azure Portal. Ta strona zawiera tylko Oczekujące zaproszenia. Akceptowane zaproszenia nie pojawiają się na stronie. Aby wyświetlić otrzymane udziały i skonfigurować ustawienie docelowego klastra Eksplorator danych platformy Azure, przejdź do zasobu udział danych, który został użyty do zaakceptowania zaproszenia.

## <a name="creating-and-receiving-shares"></a>Tworzenie i otrzymywanie udziałów

Podczas tworzenia nowego udziału, dodawania zestawów danych lub mapowania zestawów danych mogą pojawić się następujące błędy:

* Nie można dodać zestawów danych.
* Nie można zmapować zestawów danych.
* Nie można udzielić dostępu do zasobu w udziale danych x do wartości y.
* Nie masz odpowiednich uprawnień do x.
* Nie można dodać uprawnień do zapisu dla konta udziału danych platformy Azure do co najmniej jednego wybranego zasobu.

Jeśli masz niewystarczające uprawnienia do magazynu danych platformy Azure, może zostać wyświetlony jeden z tych błędów. Aby uzyskać więcej informacji, zobacz [role i wymagania](concepts-roles-permissions.md). 

Potrzebujesz uprawnienia do zapisu w celu udostępniania lub odbierania danych z magazynu danych platformy Azure. To uprawnienie jest zwykle częścią roli współautor. 

Jeśli udostępniasz dane lub otrzymujesz dane z magazynu danych Azure po raz pierwszy, musisz również mieć uprawnienie *Microsoft. Autoryzacja/przydziały ról/* uprawnienia do zapisu. To uprawnienie jest zwykle częścią roli właściciela. Nawet jeśli utworzono zasób magazynu danych platformy Azure, nie musisz być właścicielem zasobu. 

Jeśli masz odpowiednie uprawnienia, usługa Udostępnianie danych platformy Azure automatycznie zezwala na tożsamość zarządzaną zasobu udziału danych w celu uzyskania dostępu do magazynu danych. Ten proces może potrwać kilka minut. Jeśli wystąpi błąd z powodu tego opóźnienia, spróbuj ponownie za kilka minut.

Udostępnianie oparte na języku SQL wymaga dodatkowych uprawnień. Informacje o wymaganiach wstępnych znajdują się w temacie [udostępnianie ze źródeł SQL](how-to-share-from-sql.md).

## <a name="snapshots"></a>Migawki
Migawka może zakończyć się niepowodzeniem z różnych powodów. Otwórz szczegółowy komunikat o błędzie, wybierając czas rozpoczęcia migawki, a następnie stan każdego zestawu danych. 

Migawki często kończą się niepowodzeniem z następujących powodów:

* Udział danych nie ma uprawnień do odczytu ze źródłowego magazynu danych lub zapisu do docelowego magazynu danych. Aby uzyskać więcej informacji, zobacz [role i wymagania](concepts-roles-permissions.md). Jeśli tworzysz migawkę po raz pierwszy, zasób udział danych może potrzebować kilku minut, aby uzyskać dostęp do magazynu danych platformy Azure. Po kilku minutach spróbuj ponownie.
* Połączenie udostępniania danych z źródłowym magazynem danych lub docelowym magazynem danych jest blokowane przez zaporę.
* Został usunięty współużytkowany zestaw danych, magazyn z danymi źródłowymi lub docelowy magazyn danych.

W przypadku kont magazynu migawka może zakończyć się niepowodzeniem, ponieważ plik jest aktualizowany w źródle, gdy występuje migawka. W rezultacie plik 0-bajtowy może pojawić się w miejscu docelowym. Po aktualizacji w źródle migawki powinny się powieść.

W przypadku źródeł SQL migawka może zakończyć się niepowodzeniem z następujących powodów:

* Źródłowy skrypt SQL lub docelowy skrypt SQL, który przyznaje uprawnienia do udostępniania danych, nie został uruchomiony. W przypadku Azure SQL Database lub usługi Azure Synapse Analytics (dawniej Azure SQL Data Warehouse) skrypt jest uruchamiany przy użyciu uwierzytelniania SQL zamiast uwierzytelniania Azure Active Directory.  
* Magazyn danych źródłowych lub docelowy magazyn danych SQL został wstrzymany.
* Proces migawki lub docelowy magazyn danych nie obsługuje typów danych SQL. Aby uzyskać więcej informacji, zobacz [udostępnianie ze źródeł SQL](how-to-share-from-sql.md#supported-data-types).
* Źródłowy magazyn danych lub docelowy magazyn danych SQL jest zablokowany przez inne procesy. Udział danych platformy Azure nie blokuje tych magazynów danych. Jednak istniejące blokady w tych magazynach danych mogą sprawić, że migawka nie powiedzie się.
* Docelowa tabela SQL jest przywoływana przez ograniczenie klucza obcego. Jeśli w trakcie tworzenia migawki tabela docelowa ma taką samą nazwę jak tabela w danych źródłowych, udział danych platformy Azure porzuca tabelę i tworzy nową tabelę. Jeśli docelowa tabela SQL jest przywoływana przez ograniczenie klucza obcego, nie można usunąć tabeli.
* Zostanie wygenerowany docelowy plik CSV, ale dane nie mogą zostać odczytane w programie Excel. Ten problem może pojawić się, gdy źródłowa tabela SQL zawiera dane zawierające znaki inne niż angielskie. W programie Excel wybierz kartę **pobieranie danych** i wybierz plik CSV. Wybierz źródło pliku **65001: Unicode (UTF-8)**, a następnie Załaduj dane.

## <a name="updated-snapshot-schedules"></a>Zaktualizowane harmonogramy migawek
Gdy dostawca danych zaktualizuje harmonogram migawek dla wysłanego udziału, odbiorca danych musi wyłączyć poprzedni harmonogram migawek. Następnie Włącz zaktualizowany harmonogram migawek dla odebranego udziału. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) . 

Aby dowiedzieć się, jak odbierać dane, przejdź do samouczka [akceptowanie i odbieranie danych](subscribe-to-data-share.md) .