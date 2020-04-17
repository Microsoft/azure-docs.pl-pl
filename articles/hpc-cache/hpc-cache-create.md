---
title: Tworzenie pamięci podręcznej HPC platformy Azure
description: Jak utworzyć wystąpienie pamięci podręcznej HPC platformy Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: befbe2435a518b82cf5a3ab12e6129aa3ce5c22b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537982"
---
# <a name="create-an-azure-hpc-cache"></a>Tworzenie pamięci podręcznej HPC platformy Azure

Użyj witryny Azure portal, aby utworzyć pamięć podręczną.

![zrzut ekranu przedstawiający omówienie pamięci podręcznej w witrynie Azure portal z przyciskiem Utwórz u dołu](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definiowanie podstawowych szczegółów

![zrzut ekranu przedstawiający stronę szczegółów projektu w witrynie Azure portal](media/hpc-cache-create-basics.png)

W **sekcji Szczegóły projektu**wybierz grupę subskrypcji i zasobów, która będzie obsługiwać pamięć podręczną.

W **obszarze Szczegóły usługi**ustaw nazwę pamięci podręcznej i te inne atrybuty:

* Lokalizacja — wybierz jeden z [obsługiwanych regionów](hpc-cache-overview.md#region-availability).
* Sieć wirtualna — można wybrać istniejącą lub utworzyć nową sieć wirtualną.
* Podsieć — wybierz lub utwórz podsieć z co najmniej 64 adresami IP (/24), które będą używane tylko dla tego wystąpienia pamięci podręcznej HPC platformy Azure.

## <a name="set-cache-capacity"></a>Ustawianie pojemności pamięci podręcznej
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stronie **Pamięć podręczna** należy ustawić pojemność pamięci podręcznej. Wartości ustawione w tym miejscu określają, ile danych może przechowywać pamięć podręczna i jak szybko może obsługiwać żądania klientów.

Pojemność wpływa również na koszt pamięci podręcznej.

Wybierz pojemność, ustawiając te dwie wartości:

* Maksymalna szybkość transferu danych dla pamięci podręcznej (przepływność), w GB/sekundę
* Ilość miejsca przeznaczonego na dane w pamięci podręcznej w tb

Wybierz jedną z dostępnych wartości przepływności i rozmiary magazynu pamięci podręcznej.

Należy pamiętać, że rzeczywista szybkość transferu danych zależy od obciążenia, szybkości sieci i typu docelowych magazynów. Wartości, które wybierzesz ustawić maksymalną przepływność dla całego systemu pamięci podręcznej, ale niektóre z nich jest używany do zadań napowietrznych. Na przykład jeśli klient żąda pliku, który nie jest jeszcze przechowywany w pamięci podręcznej lub jeśli plik jest oznaczony jako przestarzały, pamięć podręczna używa niektórych jego przepływności, aby pobrać go z magazynu zaplecza.

Usługa Azure HPC Cache zarządza plikami, które są buforowane i wstępnie załadowane, aby zmaksymalizować współczynnik trafień w pamięci podręcznej. Zawartość pamięci podręcznej jest stale oceniana, a pliki są przenoszone do magazynu długoterminowego, gdy są rzadziej dostępne. Wybierz rozmiar magazynu pamięci podręcznej, który może wygodnie pomieścić aktywny zestaw plików roboczych z dodatkowym miejscem na metadane i inne obciążenie.

![zrzut ekranu przedstawiający stronę do zmiany rozmiaru pamięci podręcznej](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Włącz szyfrowanie usługi Azure Key Vault (opcjonalnie)

Jeśli pamięć podręczna znajduje się w regionie obsługującym klucze szyfrowania zarządzane przez klienta, między kartami **Klucze szyfrowania dysku** są wyświetlane między kartami **Pamięć podręczna** i **Znaczniki.** Od czasu publikacji ta opcja jest obsługiwana we wschodnich stanach USA, południowo-środkowych stanach USA i zachodnich stanach USA 2.

Jeśli chcesz zarządzać kluczami szyfrowania używanymi w magazynie pamięci podręcznej, podaj informacje usługi Azure Key Vault na stronie **Klucze szyfrowania dysku.** Magazyn kluczy musi znajdować się w tym samym regionie i w tej samej subskrypcji co pamięć podręczna.

Możesz pominąć tę sekcję, jeśli nie potrzebujesz kluczy zarządzanych przez klienta. Platforma Azure domyślnie szyfruje dane za pomocą kluczy zarządzanych przez firmę Microsoft. Przeczytaj [szyfrowanie magazynu platformy Azure,](../storage/common/storage-service-encryption.md) aby dowiedzieć się więcej.

> [!NOTE]
>
> * Nie można zmieniać między kluczami zarządzanymi przez firmę Microsoft a kluczami zarządzanymi przez klienta po utworzeniu pamięci podręcznej.
> * Po utworzeniu pamięci podręcznej należy ją autoryzować, aby uzyskać dostęp do magazynu kluczy. Kliknij przycisk **Włącz szyfrowanie** na stronie **Przegląd** pamięci podręcznej, aby włączyć szyfrowanie. Ten krok należy podjąć w ciągu 90 minut od utworzenia pamięci podręcznej.
> * Dyski pamięci podręcznej są tworzone po tej autoryzacji. Oznacza to, że początkowy czas tworzenia pamięci podręcznej jest krótki, ale pamięć podręczna nie będzie gotowa do użycia przez dziesięć minut lub dłużej po autoryzowaniu dostępu.

Aby uzyskać pełne wyjaśnienie procesu szyfrowania klucza zarządzanego przez klienta, przeczytaj [artykuł Użyj kluczy szyfrowania zarządzanych przez klienta dla pamięci podręcznej HPC usługi Azure](customer-keys.md).

![zrzut ekranu przedstawiający stronę kluczy szyfrowania z wybranymi polemi "zarządzane przez klienta" i polemi magazynu kluczy](media/create-encryption.png)

Wybierz **opcję Klient udało się** wybrać szyfrowanie klucza zarządzanego przez klienta. Pojawią się pola specyfikacji magazynu kluczy. Wybierz usługę Azure Key Vault do użycia, a następnie wybierz klucz i wersję do użycia w tej pamięci podręcznej. Klucz musi być 2048-bitowy klucz RSA. Z tej strony można utworzyć nową wersję magazynu kluczy, klucza lub klucza.

Po utworzeniu pamięci podręcznej należy autoryzować ją do korzystania z usługi magazynu kluczy. Przeczytaj [artykuł Autoryzuj szyfrowanie usługi Azure Key Vault z pamięci podręcznej,](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) aby uzyskać szczegółowe informacje.

## <a name="add-resource-tags-optional"></a>Dodawanie znaczników zasobów (opcjonalnie)

Strona **Tagi** umożliwia dodawanie [tagów zasobów](https://go.microsoft.com/fwlink/?linkid=873112) do wystąpienia pamięci podręcznej HPC platformy Azure.

## <a name="finish-creating-the-cache"></a>Zakończenie tworzenia pamięci podręcznej

Po skonfigurowaniu nowej pamięci podręcznej kliknij kartę **Recenzja + utwórz.** Portal sprawdza twoje wybory i umożliwia przeglądanie wyborów. Jeśli wszystko jest poprawne, kliknij przycisk **Utwórz**.

Tworzenie pamięci podręcznej trwa około 10 minut. Postęp można śledzić w panelu powiadomień witryny Azure portal.

![zrzut ekranu przedstawiający tworzenie pamięci podręcznej "wdrażanie w toku" i "powiadomienia" stron w portalu](media/hpc-cache-deploy-status.png)

Po zakończeniu tworzenia pojawi się powiadomienie z łączem do nowego wystąpienia pamięci podręcznej HPC platformy Azure, a pamięć podręczna pojawi się na liście **Zasoby** subskrypcji.

![zrzut ekranu przedstawiający wystąpienie usługi Azure HPC Cache w witrynie Azure portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Jeśli pamięć podręczna używa kluczy szyfrowania zarządzanych przez klienta, pamięć podręczna może pojawić się na liście zasobów przed zmianą stanu wdrożenia. Tak szybko, jak bufor jest stan **oczekiwania na klucz** można [autoryzować go](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) do korzystania z magazynu kluczy.

## <a name="next-steps"></a>Następne kroki

Po wyświetlenie pamięci podręcznej na liście **Zasoby** można przejść do następnego kroku.

* [Zdefiniuj obiekty docelowe magazynu,](hpc-cache-add-storage.md) aby zapewnić dostęp do pamięci podręcznej do źródeł danych.
* Jeśli używasz kluczy szyfrowania zarządzanych przez klienta, musisz [autoryzować szyfrowanie usługi Azure Key Vault](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) ze strony przeglądu pamięci podręcznej, aby ukończyć konfigurację pamięci podręcznej. Aby można było dodać magazyn, należy wykonać ten krok. Przeczytaj [artykuł Użyj kluczy szyfrowania zarządzanych przez klienta,](customer-keys.md) aby uzyskać szczegółowe informacje.
