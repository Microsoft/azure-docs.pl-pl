---
title: Szyfrowanie danych w pamięci podręcznej platformy Azure HPC przy użyciu kluczy, które są zarządzane przez klienta
description: Jak używać Azure Key Vault z pamięcią podręczną platformy Azure HPC w celu kontrolowania dostępu do klucza szyfrowania zamiast używania domyślnych kluczy szyfrowania zarządzanych przez firmę Microsoft
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: 8bce19e384e600e95b8306dcbba38652b4432bc9
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340533"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Korzystanie z kluczy szyfrowania zarządzanych przez klienta dla pamięci podręcznej platformy Azure HPC

Za pomocą Azure Key Vault można kontrolować własność kluczy używanych do szyfrowania danych w pamięci podręcznej Azure HPC. W tym artykule wyjaśniono, jak używać kluczy zarządzanych przez klienta do szyfrowania danych w pamięci podręcznej.

> [!NOTE]
> Wszystkie dane przechowywane na platformie Azure, w tym na dyskach pamięci podręcznej, są szyfrowane domyślnie przy użyciu kluczy zarządzanych przez firmę Microsoft. Należy wykonać czynności opisane w tym artykule, jeśli chcesz zarządzać kluczami używanymi do szyfrowania danych.

Pamięć podręczna Azure HPC jest również chroniona przez [szyfrowanie hosta maszyny wirtualnej](../virtual-machines/linux/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) na dyskach zarządzanych, które przechowują dane w pamięci podręcznej, nawet jeśli zostanie dodany klucz klienta dla dysków pamięci podręcznej. Dodanie klucza zarządzanego przez klienta do szyfrowania podwójnego zapewnia klientom dodatkowy poziom zabezpieczeń. Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem szyfrowanie po stronie serwera w usłudze Azure Disk Storage](../virtual-machines/linux/disk-encryption.md) .

Ta funkcja jest dostępna tylko w niektórych regionach świadczenia usługi Azure, w których dostępna jest pamięć podręczna Azure HPC. Aby uzyskać szczegółowe informacje, zobacz listę [dostępności regionów](hpc-cache-overview.md#region-availability) .

Należy wykonać trzy kroki, aby włączyć szyfrowanie klucza zarządzanego przez klienta dla pamięci podręcznej platformy Azure HPC:

1. Skonfiguruj Azure Key Vault do przechowywania kluczy.
1. Podczas tworzenia pamięci podręcznej platformy Azure HPC wybierz pozycję Szyfrowanie klucza zarządzanego przez klienta i określ Magazyn kluczy i klucz do użycia.
1. Po utworzeniu pamięci podręcznej Autoryzuj ją w celu uzyskania dostępu do magazynu kluczy.

Szyfrowanie nie jest całkowicie skonfigurowane, dopóki nie zostanie autoryzowane od nowo utworzonej pamięci podręcznej (krok 3). Jest to spowodowane tym, że musisz przekazać tożsamość pamięci podręcznej do magazynu kluczy, aby uczynić go autoryzowanym użytkownikiem. Nie można tego zrobić przed utworzeniem pamięci podręcznej, ponieważ tożsamość nie istnieje do momentu utworzenia pamięci podręcznej.

Po utworzeniu pamięci podręcznej nie można zmienić między kluczami zarządzanymi przez klienta i kluczami zarządzanymi przez firmę Microsoft. Jeśli jednak pamięć podręczna korzysta z kluczy zarządzanych przez klienta, w razie konieczności można [zmienić](#update-key-settings) klucz szyfrowania, wersję klucza i Magazyn kluczy.

## <a name="understand-key-vault-and-key-requirements"></a>Informacje o magazynie kluczy i kluczowych wymaganiach

Magazyn kluczy i klucz muszą spełniać te wymagania, aby można było korzystać z pamięci podręcznej platformy Azure HPC.

Właściwości magazynu kluczy:

* **Subskrypcja** — Użyj tej samej subskrypcji, która jest używana na potrzeby pamięci podręcznej.
* **Region** — Magazyn kluczy musi znajdować się w tym samym regionie co pamięć podręczna platformy Azure HPC.
* **Warstwa cenowa** — warstwa standardowa jest wystarczająca do użycia z pamięcią podręczną platformy Azure HPC.
* **Usuwanie nietrwałe** -pamięć podręczna Azure HPC umożliwia usuwanie nietrwałe, jeśli nie jest jeszcze skonfigurowane w magazynie kluczy.
* **Ochrona przed** przeczyszczaniem — należy włączyć ochronę przed czyszczeniem.
* **Zasady dostępu** — ustawienia domyślne są wystarczające.
* **Łączność sieciowa** — pamięć podręczna Azure HPC musi mieć dostęp do magazynu kluczy, niezależnie od wybranych ustawień punktu końcowego.

Właściwości klucza:

* **Typ klucza** — RSA
* **Rozmiar klucza RSA** -2048
* **Włączone** — tak

Uprawnienia dostępu do magazynu kluczy:

* Użytkownik tworzący pamięć podręczną Azure HPC musi mieć uprawnienia równoważne [roli współautor Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor). Te same uprawnienia są konieczne do konfigurowania Azure Key Vault i zarządzania nimi.

  Aby uzyskać więcej informacji [, przeczytaj bezpieczny dostęp do magazynu kluczy](../key-vault/general/secure-your-key-vault.md) .

## <a name="1-set-up-azure-key-vault"></a>1. Skonfiguruj Azure Key Vault

Można skonfigurować magazyn kluczy i klucz przed utworzeniem pamięci podręcznej lub zrobić to w ramach tworzenia pamięci podręcznej. Upewnij się, że te zasoby spełniają wymagania podane [powyżej](#understand-key-vault-and-key-requirements).

Podczas tworzenia pamięci podręcznej należy określić magazyn, klucz i wersję klucza do użycia na potrzeby szyfrowania pamięci podręcznej.

Zapoznaj się z [dokumentacją Azure Key Vault](../key-vault/general/overview.md) , aby uzyskać szczegółowe informacje.

> [!NOTE]
> Azure Key Vault musi używać tej samej subskrypcji i znajdować się w tym samym regionie co pamięć podręczna platformy Azure HPC. Upewnij się, że wybrany region [obsługuje funkcję klucze zarządzane przez klienta](hpc-cache-overview.md#region-availability).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Utwórz pamięć podręczną z włączonymi kluczami zarządzanymi przez klienta

Podczas tworzenia pamięci podręcznej platformy Azure HPC należy określić źródło klucza szyfrowania. Postępuj zgodnie z instrukcjami w temacie [Tworzenie pamięci podręcznej platformy Azure HPC](hpc-cache-create.md)i określ Magazyn kluczy i klucz na stronie **kluczy szyfrowania dysku** . Nowy magazyn kluczy i klucz można utworzyć podczas tworzenia pamięci podręcznej.

> [!TIP]
> Jeśli nie zostanie wyświetlona strona **klucze szyfrowania dysku** , upewnij się, że pamięć podręczna znajduje się w jednym z obsługiwanych regionów.

Użytkownik, który tworzy pamięć podręczną, musi mieć uprawnienia równe [roli współautor Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor) lub wyższą.

1. Kliknij przycisk, aby włączyć klucze zarządzane prywatnie. Po zmianie tego ustawienia zostaną wyświetlone ustawienia magazynu kluczy.

1. Kliknij pozycję **Wybierz magazyn kluczy** , aby otworzyć stronę wybór klucza. Wybierz lub Utwórz magazyn kluczy oraz klucz służący do szyfrowania danych na dyskach tej pamięci podręcznej.

   Jeśli na liście nie ma Azure Key Vault, sprawdź następujące wymagania:

   * Czy pamięć podręczna jest w tej samej subskrypcji co Magazyn kluczy?
   * Czy pamięć podręczna znajduje się w tym samym regionie co Magazyn kluczy?
   * Czy istnieje połączenie sieciowe między Azure Portal i magazynem kluczy?

1. Po wybraniu magazynu wybierz jeden z dostępnych opcji lub Utwórz nowy klucz. Klucz musi być 2048-bitowym kluczem RSA.

1. Określ wersję wybranego klucza. Więcej informacji o wersji znajduje się w [dokumentacji Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Kontynuuj pracę z pozostałymi specyfikacjami i Utwórz pamięć podręczną zgodnie z opisem w temacie [Tworzenie pamięci podręcznej platformy Azure HPC](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Autoryzuj szyfrowanie Azure Key Vault z pamięci podręcznej
<!-- header is linked from create article, update if changed -->

Po kilku minutach Nowa pamięć podręczna Azure HPC zostanie wyświetlona w Azure Portal. Przejdź do strony **Przegląd** , aby autoryzować ją do uzyskiwania dostępu do Azure Key Vault i włączania szyfrowania klucza zarządzanego przez klienta.

> [!TIP]
> Pamięć podręczna może pojawić się na liście zasobów przed wyczyszczeniem komunikatów "wdrażanie w toku". Sprawdź listę zasobów po minucie lub dwóch, zamiast czekać na powiadomienie o powodzeniu.

Ten dwuetapowy proces jest niezbędny, ponieważ wystąpienie pamięci podręcznej Azure HPC wymaga tożsamości do przekazania do Azure Key Vault na potrzeby autoryzacji. Tożsamość pamięci podręcznej nie istnieje do momentu ukończenia początkowych kroków tworzenia.

> [!NOTE]
> Po utworzeniu pamięci podręcznej należy autoryzować szyfrowanie w ciągu 90 minut. Jeśli ten krok nie zostanie wykonany, pamięć podręczna przekroczy limit czasu i zakończy się niepowodzeniem. Pamięć podręczna musi być ponownie utworzona, nie można jej naprawić.

Pamięć podręczna pokazuje stan **oczekiwania na klucz**. Kliknij przycisk **Włącz szyfrowanie** w górnej części strony, aby autoryzować pamięć podręczną do uzyskiwania dostępu do określonego magazynu kluczy.

![zrzut ekranu przedstawiający stronę przegląd pamięci podręcznej w portalu z wyróżnionym przyciskiem Włącz szyfrowanie (górny wiersz) i stan: oczekiwanie na klucz](media/waiting-for-key.png)

Kliknij pozycję **Włącz szyfrowanie** , a następnie kliknij przycisk **tak** , aby autoryzować pamięć podręczną do korzystania z klucza szyfrowania. Ta akcja włącza również funkcję trwałego usuwania i przeczyszczania ochrony (jeśli nie została jeszcze włączona) w magazynie kluczy.

![zrzut ekranu przedstawiający stronę przegląd pamięci podręcznej w portalu, z komunikatem transparent u góry, który prosi użytkownika o włączenie szyfrowania, klikając przycisk Tak.](media/enable-keyvault.png)

Gdy pamięć podręczna zażąda dostępu do magazynu kluczy, może utworzyć i zaszyfrować dyski przechowujące dane z pamięci podręcznej.

Po autoryzowaniu szyfrowania pamięć podręczna Azure HPC przejdzie przez kilka minut konfiguracji w celu utworzenia zaszyfrowanych dysków i powiązanej infrastruktury.

## <a name="update-key-settings"></a>Aktualizowanie ustawień klucza

Można zmienić Magazyn kluczy, klucz lub wersję klucza dla pamięci podręcznej z Azure Portal. Kliknij link ustawienia **szyfrowania** pamięci podręcznej, aby otworzyć stronę **Ustawienia klucza klienta** .

Nie można zmienić pamięci podręcznej między kluczami zarządzanymi przez klienta i kluczami zarządzanymi przez system.

![zrzut ekranu przedstawiający stronę "Ustawienia kluczy klienta", który został osiągnięty przez kliknięcie pozycji Ustawienia > szyfrowanie na stronie pamięci podręcznej w Azure Portal](media/change-key-click.png)

Kliknij link **Zmień klucz** , a następnie kliknij pozycję **Zmień Magazyn kluczy, klucz lub wersję** , aby otworzyć selektor klucza.

![zrzut ekranu przedstawiający stronę "Wybieranie klucza z Azure Key Vault" z trzema selektorami rozwijanymi umożliwiającymi wybranie magazynu kluczy, klucza i wersji](media/select-new-key.png)

Magazyny kluczy w tej samej subskrypcji i w tym samym regionie, co ta pamięć podręczna, są wyświetlane na liście.

Po wybraniu nowych wartości klucza szyfrowania kliknij przycisk **Wybierz**. Zostanie wyświetlona strona potwierdzenia z nowymi wartościami. Kliknij przycisk **Zapisz** , aby zakończyć wybór.

![zrzut ekranu przedstawiający stronę potwierdzenia z przyciskiem Zapisz w lewym górnym rogu](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Przeczytaj więcej na temat kluczy zarządzanych przez klienta na platformie Azure

W tych artykułach wyjaśniono więcej informacji na temat używania Azure Key Vault i kluczy zarządzanych przez klienta do szyfrowania danych na platformie Azure:

* [Omówienie szyfrowania usługi Azure Storage](../storage/common/storage-service-encryption.md)
* [Szyfrowanie dysków za pomocą kluczy zarządzanych przez klienta](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) — dokumentacja dotycząca korzystania z Azure Key Vault z dyskami zarządzanymi, która jest podobnym scenariuszem do pamięci podręcznej platformy Azure HPC

## <a name="next-steps"></a>Następne kroki

Po utworzeniu pamięci podręcznej platformy Azure HPC i autoryzowanego szyfrowania opartego na Key Vault, kontynuuj konfigurowanie pamięci podręcznej, zapewniając dostęp do źródeł danych.

* [Dodawanie lokalizacji docelowych magazynu](hpc-cache-add-storage.md)