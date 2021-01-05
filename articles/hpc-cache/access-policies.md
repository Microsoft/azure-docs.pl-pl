---
title: Używanie zasad dostępu w pamięci podręcznej platformy Azure HPC
description: Tworzenie i stosowanie niestandardowych zasad dostępu w celu ograniczenia dostępu klientów do miejsc docelowych magazynu w pamięci podręcznej Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/28/2020
ms.author: v-erkel
ms.openlocfilehash: 795b194eb7cd31e633128c22ddffe808b32e07da
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802420"
---
# <a name="use-client-access-policies"></a>Korzystanie z zasad dostępu klienta

W tym artykule wyjaśniono, jak utworzyć i zastosować niestandardowe zasady dostępu klienta dla obiektów docelowych magazynu.

Zasady dostępu klienta kontrolują sposób, w jaki klienci mogą łączyć się z eksportami docelowymi magazynu. Można kontrolować elementy, takie jak squash root i dostęp do odczytu/zapisu na poziomie hosta lub sieci.

Zasady dostępu są stosowane do ścieżki przestrzeni nazw, co oznacza, że można użyć różnych zasad dostępu dla dwóch różnych eksportów w systemie magazynu NFS.

Ta funkcja jest przeznaczona dla przepływów pracy, w których należy kontrolować, w jaki sposób różne grupy klientów uzyskują dostęp do obiektów docelowych magazynu.

Jeśli nie potrzebujesz dokładnej kontroli nad dostępem do miejsca docelowego magazynu, możesz użyć domyślnych zasad lub dostosować zasady domyślne przy użyciu dodatkowych reguł.

## <a name="create-a-client-access-policy"></a>Tworzenie zasad dostępu klienta

Użyj strony **zasady dostępu klienta** w Azure Portal, aby utworzyć zasady i zarządzać nimi. <!-- is there AZ CLI for this? -->

[![zrzut ekranu przedstawiający stronę zasady dostępu klienta. Zdefiniowano kilka zasad, a niektóre z nich są rozwinięte w celu wyświetlenia ich reguł](media/policies-overview.png)](media/policies-overview.png#lightbox)

Wszystkie zasady składają się z reguł. Reguły są stosowane do hostów w kolejności od najmniejszego zakresu (hosta) do największego (domyślnego). Zostanie zastosowana pierwsza reguła zgodna z regułą, a późniejsze reguły są ignorowane.

Aby utworzyć nowe zasady dostępu, kliknij przycisk **+ Dodaj zasady dostępu** w górnej części listy. Nadaj nazwę nowemu zasadom dostępu i wprowadź co najmniej jedną regułę.

![zrzut ekranu przedstawiający blok edytowanie zasad dostępu z wypełnionymi wieloma regułami. Kliknij przycisk OK, aby zapisać regułę.](media/add-policy.png)

W pozostałej części tej sekcji opisano wartości, których można użyć w regułach.

### <a name="scope"></a>Zakres

Termin zakres i filtr adresów współpracują ze sobą w celu zdefiniowania klientów, których dotyczy reguła.

Użyj ich, aby określić, czy reguła ma zastosowanie do pojedynczego klienta (hosta), zakresu adresów IP (sieci) czy wszystkich klientów (domyślnie).

Wybierz odpowiednią wartość **zakresu** dla reguły:

* **Host** — reguła dotyczy pojedynczego klienta
* **Sieć** — reguła dotyczy klientów w zakresie adresów IP.
* **Domyślne** — reguła ma zastosowanie do wszystkich klientów.

Reguły w zasadach są oceniane w tej kolejności. Gdy żądanie instalacji klienta pasuje do jednej reguły, inne zostaną zignorowane.

### <a name="address-filter"></a>Filtr adresów

Wartość **filtru adres** określa, którzy klienci są zgodni z regułą.

Jeśli ustawisz zakres na **host**, możesz określić tylko jeden adres IP w filtrze. W przypadku ustawienia zakresu **domyślnego** nie można wprowadzić żadnych adresów IP w polu **filtr adresu** , ponieważ zakres domyślny pasuje do wszystkich klientów.

Określ adres IP lub zakres adresów dla tej reguły. Użyj notacji CIDR (przykład: 0.1.0.0/16), aby określić zakres adresów.

### <a name="access-level"></a>Poziom dostępu

Ustaw uprawnienia, aby przyznać klientom pasujące do zakresu i filtru.

Opcje to **Odczyt/zapis**, **tylko do odczytu** lub **Brak dostępu**.

### <a name="suid"></a>SUID

Zaznacz pole **suid** , aby zezwalać na dostęp do plików w magazynie do ustawiania identyfikatorów użytkowników w przypadku dostępu.

SUID zazwyczaj jest używany do tymczasowego zwiększenia uprawnień użytkownika, dzięki czemu użytkownik może wykonać zadanie powiązane z tym plikiem.

### <a name="submount-access"></a>Dostęp do podinstalowania

Zaznacz to pole wyboru, aby umożliwić określonym klientom bezpośrednie Instalowanie podkatalogów eksportu.

### <a name="root-squash"></a>Squash główny

Zdecyduj, czy ustawić squash głównych dla klientów zgodnych z tą regułą.

Ta wartość umożliwia squash katalogu głównego na poziomie eksportu magazynu. Można również [ustawić squash root na poziomie pamięci podręcznej](configuration.md#configure-root-squash).

W przypadku włączenia squash głównego należy również ustawić jedną z następujących opcji dla identyfikatora anonimowego użytkownika:

* **-2** (nikt)
* **65534** (nikt)
* **-1** (brak dostępu)
* **65535** (brak dostępu)
* **0** (nieuprzywilejowany katalog główny)

## <a name="update-access-policies"></a>Aktualizowanie zasad dostępu

Zasady dostępu można edytować i usuwać z tabeli na stronie **zasady dostępu klienta** .

Kliknij nazwę zasad, aby otworzyć ją do edycji.

Aby usunąć zasady, zaznacz pole wyboru obok jego nazwy na liście, a następnie kliknij przycisk **Usuń** w górnej części listy. Nie można usunąć zasad o nazwie "default".

> [!NOTE]
> Nie można usunąć używanych zasad dostępu. Przed ponowieniem próby usunięcia zasad usuń je ze wszystkich ścieżek przestrzeni nazw.

## <a name="next-steps"></a>Następne kroki

* Zastosuj zasady dostępu w ścieżkach przestrzeni nazw dla obiektów docelowych magazynu. Przeczytaj temat [Konfigurowanie zagregowanej przestrzeni nazw](add-namespace-paths.md) , aby dowiedzieć się, jak.
