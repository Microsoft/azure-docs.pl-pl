---
title: Szyfrowanie danych w pamięci podręcznej HPC usługi Azure za pomocą kluczy obsługi klienta
description: Jak używać usługi Azure Key Vault z pamięcią podręczną HPC Azure do kontrolowania dostępu do kluczy szyfrowania zamiast używania domyślnych kluczy szyfrowania zarządzanych przez firmę Microsoft
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a31979763dd1ab5d8f289deef0e30cce27bb0df4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538871"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Używanie kluczy szyfrowania zarządzanych przez klienta dla pamięci podręcznej HPC usługi Azure

Za pomocą usługi Azure Key Vault można kontrolować własność kluczy używanych do szyfrowania danych w pamięci podręcznej HPC usługi Azure. W tym artykule wyjaśniono, jak używać kluczy zarządzanych przez klienta do szyfrowania danych pamięci podręcznej.

> [!NOTE]
> Wszystkie dane przechowywane na platformie Azure, w tym na dyskach pamięci podręcznej, są domyślnie szyfrowane w spoczynku przy użyciu kluczy zarządzanych przez firmę Microsoft. Wystarczy wykonać kroki opisane w tym artykule, jeśli chcesz zarządzać kluczami używanymi do szyfrowania danych.

Ta funkcja jest dostępna tylko w tych regionach platformy Azure:

* Wschodnie stany USA
* Południowo-środkowe stany USA
* Zachodnie stany USA 2

Istnieją trzy kroki, aby włączyć szyfrowanie kluczy zarządzane przez klienta dla pamięci podręcznej HPC usługi Azure:

1. Skonfiguruj magazyn kluczy platformy Azure do przechowywania kluczy.
1. Podczas tworzenia pamięci podręcznej HPC platformy Azure wybierz szyfrowanie kluczy zarządzane przez klienta i określ magazyn kluczy i klucz do użycia.
1. Po utworzeniu pamięci podręcznej należy autoryzować ją do uzyskania dostępu do magazynu kluczy.

Szyfrowanie nie jest całkowicie skonfigurowane dopiero po autoryzacji go z nowo utworzonej pamięci podręcznej (krok 3). Jest to spowodowane koniecznością przekazania tożsamości pamięci podręcznej do magazynu kluczy, aby uczynić ją autoryzowanym użytkownikiem. Nie można tego zrobić przed utworzeniem pamięci podręcznej, ponieważ tożsamość nie istnieje, dopóki nie zostanie utworzona pamięć podręczna.

Po utworzeniu pamięci podręcznej nie można zmieniać między kluczami zarządzanymi przez klienta a kluczami zarządzanymi przez firmę Microsoft. Jeśli jednak pamięć podręczna używa kluczy zarządzanych przez klienta, można [zmienić](#update-key-settings) klucz szyfrowania, wersję klucza i magazyn kluczy w razie potrzeby.

## <a name="understand-key-vault-and-key-requirements"></a>Opis wymagań dotyczących magazynu kluczy i kluczy

Magazyn kluczy i klucz musi spełniać te wymagania, aby pracować z pamięcią podręczną HPC platformy Azure.

Właściwości magazynu kluczy:

* **Subskrypcja** — użyj tej samej subskrypcji, która jest używana dla pamięci podręcznej.
* **Region** — magazyn kluczy musi znajdować się w tym samym regionie co pamięć podręczna HPC platformy Azure.
* **Warstwa cenowa** — warstwa standardowa jest wystarczająca do użycia z pamięcią podręczną HPC platformy Azure.
* **Usuwanie nietrwałe** — pamięć podręczna HPC platformy Azure włączy usuwanie nietrwałe, jeśli nie jest jeszcze skonfigurowana w magazynie kluczy.
* **Ochrona przed przeczyszczaniem** — ochrona przed przeczyszczaniem musi być włączona.
* **Zasady dostępu** — ustawienia domyślne są wystarczające.
* **Łączność sieciowa** — pamięć podręczna HPC usługi Azure musi mieć dostęp do magazynu kluczy niezależnie od wybranych ustawień punktu końcowego.

Kluczowe właściwości:

* **Typ klucza** - RSA
* **Rozmiar klucza RSA** - 2048
* **Włączone** — tak

Uprawnienia dostępu do magazynu kluczy:

* Użytkownik, który tworzy pamięć podręczną HPC platformy Azure musi mieć uprawnienia równoważne [roli współautora usługi Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor). Te same uprawnienia są potrzebne do konfigurowania usługi Azure Key Vault i zarządzania nią.

  Przeczytaj [secure access do magazynu kluczy, aby](../key-vault/key-vault-secure-your-key-vault.md) uzyskać więcej informacji.

## <a name="1-set-up-azure-key-vault"></a>1. Konfigurowanie usługi Azure Key Vault

Przed utworzeniem pamięci podręcznej można skonfigurować magazyn kluczy i klucz lub wykonać ją w ramach tworzenia pamięci podręcznej. Upewnij się, że te zasoby spełniają wymagania opisane [powyżej](#understand-key-vault-and-key-requirements).

W czasie tworzenia pamięci podręcznej należy określić magazyn, klucz i wersję klucza do użycia do szyfrowania pamięci podręcznej.

Przeczytaj [dokumentację usługi Azure Key Vault, aby](../key-vault/key-vault-overview.md) uzyskać szczegółowe informacje.

> [!NOTE]
> Usługa Azure Key Vault musi używać tej samej subskrypcji i znajdować się w tym samym regionie co pamięć podręczna HPC platformy Azure. Użyj jednego z obsługiwanych regionów wymienionych na początku tego artykułu.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Tworzenie pamięci podręcznej z włączonymi kluczami zarządzanymi przez klienta

Podczas tworzenia pamięci podręcznej HPC usługi Azure należy określić źródło klucza szyfrowania. Postępuj zgodnie z instrukcjami w [obszarze Tworzenie pamięci podręcznej HPC platformy Azure](hpc-cache-create.md)i określ przechowalnię kluczy i klucz na stronie **Klucze szyfrowania dysku.** Podczas tworzenia pamięci podręcznej można utworzyć nowy magazyn kluczy i klucz.

> [!TIP]
> Jeśli strona **Klucze szyfrowania dysku** nie jest wyświetlana, upewnij się, że pamięć podręczna znajduje się w jednym z obsługiwanych regionów.

Użytkownik, który tworzy pamięć podręczną, musi mieć uprawnienia równe [roli współautora usługi Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor) lub wyższej.

1. Kliknij przycisk, aby włączyć klucze zarządzane prywatnie. Po zmianie tego ustawienia zostaną wyświetlone ustawienia magazynu kluczy.

1. Kliknij **pozycję Wybierz przechowalnię kluczy,** aby otworzyć stronę wyboru klucza. Wybierz lub utwórz magazyn kluczy i klucz do szyfrowania danych na dyskach tej pamięci podręcznej.

   Jeśli usługa Azure Key Vault nie jest wyświetlana na liście, sprawdź następujące wymagania:

   * Czy pamięć podręczna w tej samej subskrypcji co magazyn kluczy?
   * Czy pamięć podręczna w tym samym regionie co magazyn kluczy?
   * Czy istnieje łączność sieciowa między witryną Azure portal a magazynem kluczy?

1. Po wybraniu przechowalni wybierz pojedynczy klucz z dostępnych opcji lub utwórz nowy klucz. Klucz musi być 2048-bitowy klucz RSA.

1. Określ wersję wybranego klucza. Dowiedz się więcej o przechowywanie wersji w [dokumentacji usługi Azure Key Vault](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning).

Kontynuuj z pozostałymi specyfikacjami i utwórz pamięć podręczną zgodnie z opisem w [obszarze Tworzenie pamięci podręcznej HPC platformy Azure](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Autoryzuj szyfrowanie usługi Azure Key Vault z pamięci podręcznej
<!-- header is linked from create article, update if changed -->

Po kilku minutach nowa pamięć podręczna HPC platformy Azure pojawi się w witrynie Azure Portal. Przejdź do **strony Przegląd,** aby autoryzować go do dostępu do usługi Azure Key Vault i włączyć szyfrowanie kluczy zarządzane przez klienta. (Pamięć podręczna może pojawić się na liście zasobów przed wyczyszczeniu komunikatów "wdrażanie w toku").

Ten dwuetapowy proces jest konieczny, ponieważ wystąpienie pamięci podręcznej HPC platformy Azure wymaga tożsamości do przekazania do usługi Azure Key Vault w celu autoryzacji. Tożsamość pamięci podręcznej nie istnieje dopiero po zakończeniu początkowych kroków tworzenia.

> [!NOTE]
> Należy autoryzować szyfrowanie w ciągu 90 minut po utworzeniu pamięci podręcznej. Jeśli ten krok nie zostanie ukończony, limit czasu pamięci podręcznej i zakończy się niepowodzeniem. Nie powiodło się pamięci podręcznej musi zostać ponownie utworzony, nie można naprawić.

Pamięć podręczna pokazuje stan **Oczekiwanie na klucz**. Kliknij przycisk **Włącz szyfrowanie** u góry strony, aby autoryzować pamięć podręczną w celu uzyskania dostępu do określonego magazynu kluczy.

![zrzut ekranu przedstawiający stronę przeglądu pamięci podręcznej w portalu z wyróżnieniem na przycisku Włącz szyfrowanie (górny wiersz) i Stan: Oczekiwanie na klucz](media/waiting-for-key.png)

Kliknij **pozycję Włącz szyfrowanie,** a następnie kliknij przycisk **Tak,** aby autoryzować pamięć podręczną do używania klucza szyfrowania. Ta akcja umożliwia również ochronę usuwania nietrwałego i przeczyszczaj (jeśli nie jest już włączona) w magazynie kluczy.

![zrzut ekranu przedstawiający stronę przeglądu pamięci podręcznej w portalu z komunikatem banerowym u góry z prośbą o włączenie szyfrowania przez kliknięcie przycisku tak](media/enable-keyvault.png)

Po żądaniu dostępu do magazynu kluczy w pamięci podręcznej można utworzyć i zaszyfrować dyski, które przechowują buforowane dane.

Po autoryzacji szyfrowania pamięć podręczna HPC firmy Azure przechodzi przez kilka minut konfiguracji w celu utworzenia zaszyfrowanych dysków i powiązanej infrastruktury.

## <a name="update-key-settings"></a>Aktualizowanie ustawień klucza

Możesz zmienić magazyn kluczy, klucz lub wersję klucza dla pamięci podręcznej z witryny Azure portal. Kliknij łącze Ustawienia **szyfrowania** pamięci podręcznej, aby otworzyć stronę **Ustawienia klucza klienta.** (Nie można zmienić pamięci podręcznej między kluczami zarządzanymi przez klienta a kluczami zarządzanymi przez system).

![zrzut ekranu przedstawiający stronę "Ustawienia kluczy klienta", do którego dotarła kliknięcie pozycji Ustawienia > szyfrowanie ze strony pamięci podręcznej w witrynie Azure portal](media/change-key-click.png)

Kliknij łącze **Zmień klucz,** a następnie kliknij pozycję **Zmień przechowalnię kluczy, klucz lub wersję,** aby otworzyć selektor kluczy.

![zrzut ekranu przedstawiający stronę "wybierz klucz z usługi Azure Key Vault" z trzema selektorami rozwijanym, aby wybrać przechowalnię kluczy, klucz i wersję](media/select-new-key.png)

Magazyny kluczy w tej samej subskrypcji i tym samym regionie co ta pamięć podręczna są wyświetlane na liście.

Po wybraniu nowych wartości klucza szyfrowania kliknij przycisk **Wybierz**. Zostanie wyświetlona strona potwierdzenia z nowymi wartościami. Kliknij **przycisk Zapisz,** aby sfinalizować zaznaczenie.

![zrzut ekranu przedstawiający stronę potwierdzenia z przyciskiem Zapisz w lewym górnym rogu](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Dowiedz się więcej o kluczach zarządzanych przez klienta na platformie Azure

W tych artykułach wyjaśniono więcej na temat używania usługi Azure Key Vault i kluczy zarządzanych przez klienta do szyfrowania danych na platformie Azure:

* [Omówienie szyfrowania magazynu platformy Azure](../storage/common/storage-service-encryption.md)
* [Szyfrowanie dysków za pomocą kluczy zarządzanych](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) przez klienta — dokumentacja dotycząca korzystania z usługi Azure Key Vault i dysków zarządzanych, która jest podobna do procesu używanego w pamięci podręcznej HPC usługi Azure

## <a name="next-steps"></a>Następne kroki

Po utworzeniu pamięci podręcznej HPC platformy Azure i autoryzowaniu szyfrowania opartego na programie Key Vault kontynuuj konfigurowanie pamięci podręcznej, dając jej dostęp do źródeł danych.

* [Dodawanie lokalizacji docelowych magazynu](hpc-cache-add-storage.md)
