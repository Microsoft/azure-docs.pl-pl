---
title: Używanie kluczy mangowanych przez klienta do szyfrowania danych w Azure HPC Cache
description: Jak używać Azure Key Vault z Azure HPC Cache do kontrolowania dostępu do klucza szyfrowania zamiast używania domyślnych kluczy szyfrowania zarządzanych przez firmę Microsoft
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: 36ce494c7fd51a1341834d5c231e32e60c5a32b9
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751998"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Używanie kluczy szyfrowania zarządzanych przez klienta na potrzeby Azure HPC Cache

Za pomocą Azure Key Vault możesz kontrolować własność kluczy używanych do szyfrowania danych w Azure HPC Cache. W tym artykule wyjaśniono, jak używać kluczy zarządzanych przez klienta do szyfrowania danych w pamięci podręcznej.

> [!NOTE]
> Wszystkie dane przechowywane na platformie Azure, w tym na dyskach pamięci podręcznej, są domyślnie szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby zarządzać kluczami używanymi do szyfrowania danych, wystarczy wykonać kroki opisane w tym artykule.

Azure HPC Cache są również chronione [](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) przez szyfrowanie hosta maszyny wirtualnej na dyskach zarządzanych, na których są przechowywane dane w pamięci podręcznej, nawet jeśli dodasz klucz klienta dla dysków pamięci podręcznej. Dodanie klucza zarządzanego przez klienta na potrzeby podwójnego szyfrowania zapewnia dodatkowy poziom zabezpieczeń klientom z wysokimi potrzebami w zakresie zabezpieczeń. Aby uzyskać szczegółowe informacje, przeczytaj szyfrowanie po stronie serwera [usługi Azure Disk Storage.](../virtual-machines/disk-encryption.md)

Istnieją trzy kroki włączania szyfrowania kluczy zarządzanego przez klienta dla Azure HPC Cache:

1. Skonfiguruj konto Azure Key Vault przechowywania kluczy.
1. Podczas tworzenia Azure HPC Cache wybierz szyfrowanie klucza zarządzane przez klienta i określ magazyn kluczy oraz klucz do użycia.
1. Po utworzeniu pamięci podręcznej autoryzuj ją w celu uzyskania dostępu do magazynu kluczy.

Szyfrowanie nie zostanie całkowicie ustawione do momentu autoryzowania go z nowo utworzonej pamięci podręcznej (krok 3). Jest to spowodowane tym, że należy przekazać tożsamość pamięci podręcznej do magazynu kluczy, aby był autoryzowanym użytkownikiem. Nie można tego zrobić przed utworzeniem pamięci podręcznej, ponieważ tożsamość nie istnieje, dopóki pamięć podręczna nie zostanie utworzona.

Po utworzeniu pamięci podręcznej nie można zmienić między kluczami zarządzanymi przez klienta i kluczami zarządzanymi przez firmę Microsoft. Jeśli jednak pamięć podręczna używa kluczy [](#update-key-settings) zarządzanych przez klienta, można w razie potrzeby zmienić klucz szyfrowania, wersję klucza i magazyn kluczy.

## <a name="understand-key-vault-and-key-requirements"></a>Opis wymagań dotyczących magazynu kluczy i klucza

Magazyn kluczy i klucz muszą spełniać te wymagania, aby można było pracować z Azure HPC Cache.

Właściwości magazynu kluczy:

* **Subskrypcja** — użyj tej samej subskrypcji, która jest używana dla pamięci podręcznej.
* **Region** — magazyn kluczy musi znajdować się w tym samym regionie co Azure HPC Cache.
* **Warstwa cenowa** — warstwa Standardowa jest wystarczająca do użycia z Azure HPC Cache.
* **Usuwanie nie softne** — Azure HPC Cache włączyć usuwanie nie softowe, jeśli nie zostało to jeszcze skonfigurowane w magazynie kluczy.
* **Ochrona przed przeczyszczaniem** — ochrona przed przeczyszczaniem musi być włączona.
* **Zasady dostępu** — ustawienia domyślne są wystarczające.
* **Łączność** sieciowa — Azure HPC Cache musi mieć dostęp do magazynu kluczy, niezależnie od ustawień punktu końcowego.

Kluczowe właściwości:

* **Typ klucza** — RSA
* **Rozmiar klucza RSA** — 2048
* **Włączone** — tak

Uprawnienia dostępu do magazynu kluczy:

* Użytkownik, który tworzy Azure HPC Cache musi mieć uprawnienia równoważne roli [Key Vault współautora.](../role-based-access-control/built-in-roles.md#key-vault-contributor) Te same uprawnienia są potrzebne do skonfigurowania aplikacji i zarządzania Azure Key Vault.

  Przeczytaj [bezpieczny dostęp do magazynu kluczy,](../key-vault/general/security-overview.md) aby uzyskać więcej informacji.

## <a name="1-set-up-azure-key-vault"></a>1. Konfigurowanie Azure Key Vault

Magazyn kluczy i klucz można skonfigurować przed utworzeniem pamięci podręcznej lub w ramach tworzenia pamięci podręcznej. Upewnij się, że te zasoby spełniają wymagania opisane [powyżej.](#understand-key-vault-and-key-requirements)

Podczas tworzenia pamięci podręcznej należy określić magazyn, klucz i wersję klucza do użycia na użytek szyfrowania pamięci podręcznej.

Przeczytaj [dokumentację Azure Key Vault, aby](../key-vault/general/overview.md) uzyskać szczegółowe informacje.

> [!NOTE]
> Ta Azure Key Vault musi używać tej samej subskrypcji i znajdować się w tym samym regionie co Azure HPC Cache. Upewnij się, że region, który wybierzesz, [obsługuje oba produkty.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Tworzenie pamięci podręcznej z włączonymi kluczami zarządzanymi przez klienta

Podczas tworzenia klucza szyfrowania należy określić źródło Azure HPC Cache. Postępuj zgodnie z instrukcjami w [Azure HPC Cache](hpc-cache-create.md), a następnie określ magazyn kluczy i klucz na stronie **Klucze szyfrowania** dysków. Podczas tworzenia pamięci podręcznej można utworzyć nowy magazyn kluczy i klucz.

> [!TIP]
> Jeśli strona **Klucze szyfrowania dysków** nie jest wyświetlana, upewnij się, że pamięć podręczna znajduje się w jednym z [obsługiwanych regionów.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)

Użytkownik tworzący pamięć podręczną musi mieć uprawnienia równe Key Vault [roli współautora](../role-based-access-control/built-in-roles.md#key-vault-contributor) lub wyższego.

1. Kliknij przycisk , aby włączyć klucze zarządzane prywatnie. Po zmianie tego ustawienia zostaną wyświetlone ustawienia magazynu kluczy.

1. Kliknij **pozycję Wybierz magazyn kluczy,** aby otworzyć stronę wyboru klucza. Wybierz lub utwórz magazyn kluczy i klucz do szyfrowania danych na dyskach tej pamięci podręcznej.

   Jeśli twój Azure Key Vault nie jest wyświetlany na liście, sprawdź następujące wymagania:

   * Czy pamięć podręczna znajduje się w tej samej subskrypcji co magazyn kluczy?
   * Czy pamięć podręczna znajduje się w tym samym regionie co magazyn kluczy?
   * Czy istnieje łączność sieciowa między Azure Portal i magazynem kluczy?

1. Po wybraniu magazynu wybierz pojedynczy klucz z dostępnych opcji lub utwórz nowy klucz. Klucz musi być 2048-bitowym kluczem RSA.

1. Określ wersję wybranego klucza. Więcej informacji na temat wersji można znaleźć w [Azure Key Vault dokumentacji systemu](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Kontynuuj pracę z pozostałymi specyfikacjami i utwórz pamięć podręczną zgodnie z opisem w części [Tworzenie Azure HPC Cache](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Autoryzowanie Azure Key Vault szyfrowania danych z pamięci podręcznej
<!-- header is linked from create article, update if changed -->

Po kilku minutach w twoim Azure HPC Cache pojawi się nowy Azure Portal. Przejdź do strony **Przegląd,** aby autoryzować ją do uzyskiwania dostępu do Azure Key Vault i włączyć szyfrowanie kluczy zarządzane przez klienta.

> [!TIP]
> Pamięć podręczna może pojawić się na liście zasobów przed wyczyszczeniem komunikatów "wdrażanie w toku". Sprawdź listę zasobów po minucie lub dwóch zamiast czekać na powiadomienie o sukcesie.

Ten dwuetapowy proces jest niezbędny, ponieważ Azure HPC Cache wymaga tożsamości do przekazania do Azure Key Vault autoryzacji. Tożsamość pamięci podręcznej nie istnieje, dopóki nie zostaną ukończone jej początkowe kroki tworzenia.

> [!NOTE]
> Należy autoryzować szyfrowanie w ciągu 90 minut od utworzenia pamięci podręcznej. Jeśli ten krok nie zostanie ukończony, pamięć podręczna przejmie jego czas i zakończy się niepowodzeniem. Nieudaną pamięć podręczną należy ponownie utworzyć. Nie można jej naprawić.

Pamięć podręczna wyświetla stan **Oczekiwanie na klucz**. Kliknij przycisk **Włącz szyfrowanie** w górnej części strony, aby autoryzować pamięć podręczną do uzyskiwania dostępu do określonego magazynu kluczy.

![zrzut ekranu przedstawiający stronę przeglądu pamięci podręcznej w portalu z wyróżnieniami przycisków Włącz szyfrowanie (górny wiersz) i Stan: Oczekiwanie na klucz](media/waiting-for-key.png)

Kliknij **pozycję Włącz szyfrowanie,** a następnie kliknij przycisk **Tak,** aby autoryzować pamięć podręczną do używania klucza szyfrowania. Ta akcja włącza również ochronę usuwania nie soft i przeczyszczania (jeśli nie została jeszcze włączona) w magazynie kluczy.

![zrzut ekranu przedstawiający stronę przeglądu pamięci podręcznej w portalu z banerem u góry z prośbą o włączenie szyfrowania przez użytkownika, klikając pozycję Tak](media/enable-keyvault.png)

Gdy pamięć podręczna zażąda dostępu do magazynu kluczy, może tworzyć i szyfrować dyski przechowujące dane w pamięci podręcznej.

Po autoryzowaniu szyfrowania Azure HPC Cache instalacji w celu utworzenia zaszyfrowanych dysków i powiązanej infrastruktury.

## <a name="update-key-settings"></a>Aktualizowanie ustawień klucza

Możesz zmienić magazyn kluczy, klucz lub wersję klucza dla pamięci podręcznej z poziomu Azure Portal. Kliknij link Ustawienia szyfrowania pamięci **podręcznej,** aby otworzyć **stronę Ustawienia klucza klienta.**

Nie można zmienić pamięci podręcznej między kluczami zarządzanymi przez klienta i kluczami zarządzanymi przez system.

![zrzut ekranu przedstawiający stronę "Ustawienia kluczy klienta", do > ustawienia szyfrowania na stronie pamięci podręcznej w Azure Portal](media/change-key-click.png)

Kliknij link **Zmień klucz,** a następnie kliknij pozycję Zmień magazyn **kluczy, klucz lub wersję,** aby otworzyć selektor kluczy.

![zrzut ekranu przedstawiający stronę "wybieranie klucza z Azure Key Vault" z trzema selektorami listy rozwijanej w celu wybrania magazynu kluczy, klucza i wersji](media/select-new-key.png)

Magazyny kluczy w tej samej subskrypcji i tym samym regionie co ta pamięć podręczna są wyświetlane na liście.

Po wybraniu nowych wartości klucza szyfrowania kliknij pozycję **Wybierz**. Zostanie wyświetlona strona potwierdzenia z nowymi wartościami. Kliknij **przycisk Zapisz,** aby sfinalizować wybór.

![zrzut ekranu przedstawiający stronę potwierdzenia z przyciskiem Zapisz w lewym górnym rogu](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Dowiedz się więcej o kluczach zarządzanych przez klienta na platformie Azure

W tych artykułach wyjaśniono więcej na temat Azure Key Vault kluczy zarządzanych przez klienta do szyfrowania danych na platformie Azure:

* [Omówienie szyfrowania usługi Azure Storage](../storage/common/storage-service-encryption.md)
* [Szyfrowanie dysków za pomocą kluczy zarządzanych](../virtual-machines/disk-encryption.md#customer-managed-keys) przez klienta — dokumentacja dotycząca używania Azure Key Vault z dyskami zarządzanymi, co jest podobne do Azure HPC Cache

## <a name="next-steps"></a>Następne kroki

Po utworzeniu Azure HPC Cache i autoryzowanym szyfrowaniu opartym na Key Vault kontynuuj konfigurowanie pamięci podręcznej, zapewniając jej dostęp do źródeł danych.

* [Dodawanie lokalizacji docelowych magazynu](hpc-cache-add-storage.md)
