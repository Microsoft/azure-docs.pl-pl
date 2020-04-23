---
title: Tworzenie pamięci podręcznej platformy Azure HPC
description: Jak utworzyć wystąpienie pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: efa9037b345cdfc5f165e9c5e0c1831ea97b52ed
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106495"
---
# <a name="create-an-azure-hpc-cache"></a>Tworzenie pamięci podręcznej platformy Azure HPC

Użyj Azure Portal, aby utworzyć pamięć podręczną.

![zrzut ekranu przedstawiający przegląd pamięci podręcznej w Azure Portal z przyciskiem Utwórz u dołu](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definiuj podstawowe szczegóły

![zrzut ekranu strony szczegółów projektu w Azure Portal](media/hpc-cache-create-basics.png)

W obszarze **szczegóły projektu**wybierz subskrypcję i grupę zasobów, w której będzie hostowana pamięć podręczna. Upewnij się, że subskrypcja znajduje się na liście [dostęp](hpc-cache-prereqs.md#azure-subscription) .

W obszarze **Szczegóły usługi**Ustaw nazwę pamięci podręcznej i inne atrybuty:

* Lokalizacja — wybierz jeden z [obsługiwanych regionów](hpc-cache-overview.md#region-availability).
* Sieć wirtualna — możesz wybrać istniejącą lub utworzyć nową sieć wirtualną.
* Podsieć — wybierz lub Utwórz podsieć z co najmniej 64 adresami IP (/24), które będą używane tylko dla tego wystąpienia pamięci podręcznej platformy Azure HPC.

## <a name="set-cache-capacity"></a>Ustawianie pojemności pamięci podręcznej
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stronie **pamięć podręczna** należy ustawić pojemność pamięci podręcznej. Wartości ustawione w tym miejscu określają ilość danych, które mogą być przechowywane w pamięci podręcznej i jak szybko mogą obsłużyć żądania klientów.

Pojemność ma także wpływ na koszt pamięci podręcznej.

Wybierz pojemność, ustawiając te dwie wartości:

* Maksymalna szybkość transferu danych w pamięci podręcznej (przepływność), w GB/s
* Ilość miejsca do magazynowania przydzieloną dla danych w pamięci podręcznej w TB

Wybierz jedną z dostępnych wartości przepływności i rozmiar pamięci podręcznej.

Należy pamiętać, że Rzeczywista szybkość transferu danych zależy od obciążenia, szybkości sieci i typu miejsca docelowego magazynu. Wybrane wartości ustawiają maksymalną przepływność dla całego systemu pamięci podręcznej, ale niektóre z nich są używane do zadań dodatkowych. Na przykład jeśli klient żąda pliku, który nie jest już przechowywany w pamięci podręcznej, lub jeśli plik jest oznaczony jako przestarzały, pamięć podręczna będzie używać niektórych przepływności do pobrania z magazynu zaplecza.

Pamięć podręczna platformy Azure HPC zarządza, które pliki są buforowane i wstępnie załadowane, aby zmaksymalizować szybkość trafień pamięci podręcznej. Zawartość pamięci podręcznej jest stale oceniana i pliki są przenoszone do magazynu długoterminowego, gdy są one rzadziej używane. Wybierz rozmiar pamięci podręcznej, który może wygodnie przechowywać aktywny zestaw plików roboczych z dodatkowym miejscem dla metadanych i innych obciążeń.

![zrzut ekranu strony zmiany wielkości pamięci podręcznej](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Włącz szyfrowanie Azure Key Vault (opcjonalnie)

Jeśli pamięć podręczna znajduje się w regionie, który obsługuje zarządzane przez klienta klucze szyfrowania, zostanie wyświetlona strona **klucze szyfrowania dysku** między kartami **pamięci podręcznej** a **tagami** . Od czasu publikacji ta opcja jest obsługiwana w regionach Wschodnie stany USA, Południowo-środkowe stany USA i zachodnie stany USA 2.

Jeśli chcesz zarządzać kluczami szyfrowania używanymi z magazynem pamięci podręcznej, podaj informacje o Azure Key Vault na stronie **kluczy szyfrowania dysku** . Magazyn kluczy musi znajdować się w tym samym regionie i w tej samej subskrypcji co pamięć podręczna.

Możesz pominąć tę sekcję, jeśli nie są potrzebne klucze zarządzane przez klienta. Platforma Azure domyślnie szyfruje dane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby dowiedzieć się więcej, przeczytaj artykuł [szyfrowanie usługi Azure Storage](../storage/common/storage-service-encryption.md) .

> [!NOTE]
>
> * Po utworzeniu pamięci podręcznej nie można zmienić między kluczami zarządzanymi przez firmę Microsoft i kluczami zarządzanymi przez klienta.
> * Po utworzeniu pamięci podręcznej należy autoryzować ją w celu uzyskania dostępu do magazynu kluczy. Kliknij przycisk **Włącz szyfrowanie** na stronie **Przegląd** pamięci podręcznej, aby włączyć szyfrowanie. Wykonaj ten krok w ciągu 90 minut od utworzenia pamięci podręcznej.
> * Dyski pamięci podręcznej są tworzone po tej autoryzacji. Oznacza to, że początkowa godzina tworzenia pamięci podręcznej jest krótka, ale pamięć podręczna nie będzie gotowa do użycia przez dziesięć minut lub dłużej po udzieleniu zgody na dostęp.

Aby uzyskać pełny opis procesu szyfrowania klucza zarządzanego przez klienta, przeczytaj artykuł [Korzystanie z kluczy szyfrowania zarządzanych przez klienta dla pamięci podręcznej platformy Azure HPC](customer-keys.md).

![zrzut ekranu strony kluczy szyfrowania z wybraną opcją "zarządzane przez klienta" i pola magazynu kluczy pokazujące](media/create-encryption.png)

Wybierz pozycję **zarządzane przez klienta** , aby wybrać szyfrowanie klucza zarządzanego przez klienta. Zostaną wyświetlone pola Specyfikacja magazynu kluczy. Wybierz Azure Key Vault, którego chcesz użyć, a następnie wybierz klucz i wersję, która ma być używana dla tej pamięci podręcznej. Klucz musi być 2048-bitowym kluczem RSA. Na tej stronie można utworzyć nowy magazyn kluczy, klucz lub wersję klucza.

Po utworzeniu pamięci podręcznej należy autoryzować ją do korzystania z usługi magazynu kluczy. Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem autoryzuj Azure Key Vault szyfrowanie z pamięci podręcznej](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)

## <a name="add-resource-tags-optional"></a>Dodaj Tagi zasobów (opcjonalnie)

Na stronie **Tagi** można dodać [Tagi zasobów](https://go.microsoft.com/fwlink/?linkid=873112) do wystąpienia pamięci podręcznej platformy Azure HPC.

## <a name="finish-creating-the-cache"></a>Zakończ tworzenie pamięci podręcznej

Po skonfigurowaniu nowej pamięci podręcznej kliknij kartę **Recenzja + tworzenie** . Portal sprawdza poprawność wybranych opcji i pozwala przejrzeć wybrane opcje. Jeśli wszystko jest poprawne, kliknij przycisk **Utwórz**.

Tworzenie pamięci podręcznej zajmie około 10 minut. Postęp można śledzić w panelu powiadomienia Azure Portal.

![zrzut ekranu przedstawiający tworzenie stron "wdrażanie" i "powiadomienia" w portalu](media/hpc-cache-deploy-status.png)

Po zakończeniu tworzenia zostanie wyświetlone powiadomienie z linkiem do nowego wystąpienia usługi Azure HPC cache, a pamięć podręczna zostanie wyświetlona na liście **zasobów** subskrypcji.

![zrzut ekranu wystąpienia pamięci podręcznej platformy Azure HPC w Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Jeśli pamięć podręczna korzysta z kluczy szyfrowania zarządzanych przez klienta, pamięć podręczna może pojawić się na liście zasobów, zanim stan wdrożenia zostanie zmieniony na ukończone. Gdy tylko stan pamięci podręcznej **oczekuje na klucz** , możesz [autoryzować go](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) do korzystania z magazynu kluczy.

## <a name="next-steps"></a>Następne kroki

Gdy pamięć podręczna zostanie wyświetlona na liście **zasobów** , możesz przejść do następnego kroku.

* [Zdefiniuj cele magazynu](hpc-cache-add-storage.md) , aby zapewnić dostęp do pamięci podręcznej do źródeł danych.
* W przypadku korzystania z kluczy szyfrowania zarządzanych przez klienta należy [autoryzować Azure Key Vault szyfrowanie](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) z poziomu strony Przegląd pamięci podręcznej, aby ukończyć konfigurację pamięci podręcznej. Ten krok należy wykonać, aby można było dodać magazyn. Aby uzyskać szczegółowe informacje, przeczytaj artykuł [Używanie kluczy szyfrowania zarządzanych przez klienta](customer-keys.md) .
