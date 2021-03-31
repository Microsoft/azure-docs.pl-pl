---
title: Szyfrowanie analizy Synapse Azure
description: Artykuł objaśniający szyfrowanie w usłudze Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: d4bc59a9bd5299698bff9949aaaa881fbdf385ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100526276"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Szyfrowanie obszarów roboczych usługi Azure Synapse Analytics

W tym artykule opisano:
* Szyfrowanie danych magazynowanych w obszarach roboczych Synapse Analytics.
* Konfiguracja obszarów roboczych Synapse, aby włączyć szyfrowanie przy użyciu klucza zarządzanego przez klienta.
* Zarządzanie kluczami używanymi do szyfrowania danych w obszarach roboczych.

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie danych magazynowanych

Kompletne rozwiązanie do szyfrowania w czasie spoczynku gwarantuje, że dane nigdy nie są utrwalane w postaci niezaszyfrowanej. Podwójne szyfrowanie danych w spoczynku zmniejsza zagrożenie przy użyciu dwóch oddzielnych warstw szyfrowania, aby chronić przed naruszeniem jakichkolwiek pojedynczych warstw. Usługa Azure Synapse Analytics oferuje drugą warstwę szyfrowania danych w obszarze roboczym z kluczem zarządzanym przez klienta. Ten klucz jest chroniony w [Azure Key Vault](../../key-vault/general/overview.md), co umożliwia przejęcie na własność zarządzania kluczami i rotacją.

Pierwsza warstwa szyfrowania usług platformy Azure jest włączona z kluczami zarządzanymi na platformie. Domyślnie dyski platformy Azure i dane na kontach usługi Azure Storage są szyfrowane automatycznie. Dowiedz się więcej o tym, jak szyfrowanie jest używane w Microsoft Azure w [omówieniu usługi Azure Encryption](../../security/fundamentals/encryption-overview.md).

## <a name="azure-synapse-encryption"></a>Szyfrowanie Synapse platformy Azure

Ta sekcja pomoże lepiej zrozumieć, w jaki sposób szyfrowanie kluczy zarządzane przez klienta jest włączone i wymuszane w obszarach roboczych Synapse. To szyfrowanie używa istniejących kluczy lub nowych kluczy generowanych w Azure Key Vault. Pojedynczy klucz służy do szyfrowania wszystkich danych w obszarze roboczym. Obszary robocze Synapse obsługują klucze RSA z kluczami o rozmiarach bajtów 2048 i 3072.

> [!NOTE]
> Obszary robocze Synapse nie obsługują szyfrowania przy użyciu kluczy kryptografii Elliptic-Curve (ECC).

Dane w następujących składnikach Synapse są szyfrowane za pomocą klucza zarządzanego przez klienta skonfigurowanego na poziomie obszaru roboczego:
* Pule SQL
 * Dedykowane pule SQL
 * Pule SQL bezserwerowe
* Pule platformy Apache Spark
* Azure Data Factory środowiska Integration Runtime, potoków, zestawów danych.

## <a name="workspace-encryption-configuration"></a>Konfiguracja szyfrowania obszaru roboczego

Obszary robocze można skonfigurować tak, aby włączyć podwójne szyfrowanie z kluczem zarządzanym przez klienta w momencie tworzenia obszaru roboczego. Wybierz opcję "Włącz podwójne szyfrowanie przy użyciu klucza zarządzanego przez klienta" na karcie "zabezpieczenia" podczas tworzenia nowego obszaru roboczego. Możesz wprowadzić identyfikator URI identyfikatora klucza lub wybrać z listy magazynów kluczy w **tym samym regionie** , w którym znajduje się obszar roboczy. Sam Key Vault musi mieć **włączoną ochronę przed czyszczeniem**.

> [!IMPORTANT]
> Nie można zmienić ustawienia konfiguracji podwójnego szyfrowania po utworzeniu obszaru roboczego.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Ten diagram przedstawia opcję, która musi zostać wybrana, aby umożliwić obszarowi roboczemu podwójne szyfrowanie z kluczem zarządzanym przez klienta.":::

### <a name="key-access-and-workspace-activation"></a>Dostęp do klucza i aktywacja obszaru roboczego

Model szyfrowania Azure Synapse z kluczami zarządzanymi przez klienta obejmuje obszar roboczy z dostępem do kluczy w Azure Key Vault do szyfrowania i odszyfrowywania w razie potrzeby. Klucze są dostępne dla obszaru roboczego za pomocą zasad dostępu lub [Azure Key Vault dostępu RBAC](../../key-vault/general/rbac-guide.md). Podczas udzielania uprawnień przy użyciu zasad dostępu Azure Key Vault wybierz opcję ["tylko aplikacja"](../../key-vault/general/secure-your-key-vault.md#key-vault-authentication-options) podczas tworzenia zasad (Wybierz zarządzaną tożsamość obszaru roboczego i nie dodawaj jej jako autoryzowaną aplikację).

 Aby można było aktywować obszar roboczy, tożsamość zarządzana obszaru roboczego musi mieć przyznane uprawnienia wymagane w magazynie kluczy. Ta stopniowa podejście do aktywacji obszaru roboczego gwarantuje, że dane w obszarze roboczym są szyfrowane za pomocą klucza zarządzanego przez klienta. Należy pamiętać, że szyfrowanie można włączyć lub wyłączyć dla dedykowanych pul SQL-dla każdej puli nie jest domyślnie włączona obsługa szyfrowania.

#### <a name="permissions"></a>Uprawnienia

Aby można było szyfrować lub odszyfrowywać dane przechowywane, tożsamość zarządzana w obszarze roboczym musi mieć następujące uprawnienia:
* WrapKey (aby wstawić klucz do Key Vault podczas tworzenia nowego klucza).
* UnwrapKey (Aby uzyskać klucz do odszyfrowania).
* Pobierz (aby odczytać publiczną część klucza)

#### <a name="workspace-activation"></a>Aktywacja obszaru roboczego

Po utworzeniu obszaru roboczego (z włączonym podwójnym szyfrowaniem) pozostanie w stanie "oczekiwanie" do momentu pomyślnego aktywacji. Aby można było w pełni korzystać ze wszystkich funkcji, należy aktywować obszar roboczy. Można na przykład utworzyć nową dedykowaną pulę SQL po pomyślnym zakończeniu aktywacji. Udziel obszarowi roboczemu dostępu do tożsamości zarządzanej do magazynu kluczy i kliknij link aktywacja w obszarze roboczym Azure Portal baner. Po pomyślnym zakończeniu aktywacji obszar roboczy jest gotowy do użycia z gwarancją, że wszystkie dane w niej są chronione kluczem zarządzanym przez klienta. Jak wspomniano wcześniej, Magazyn kluczy musi mieć włączoną ochronę przed przeczyszczeniem, aby aktywacja powiodła się.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Ten diagram przedstawia transparent z linkiem aktywacji dla obszaru roboczego.":::


### <a name="manage-the-workspace-customer-managed-key"></a>Zarządzanie obszarem roboczym z kluczem zarządzanym przez klienta 

Można zmienić klucz zarządzany przez klienta używany do szyfrowania danych ze strony **szyfrowanie** w Azure Portal. Tutaj możesz wybrać nowy klucz przy użyciu identyfikatora klucza lub wybrać z magazynów kluczy, do których masz dostęp, w tym samym regionie, w którym znajduje się obszar roboczy. W przypadku wybrania klucza w innym magazynie kluczy niż poprzednio używane, Udziel uprawnienia "Pobierz", "Zawijanie" i "odpakowywanie" dla nowego magazynu kluczy. Obszar roboczy sprawdzi swój dostęp do nowego magazynu kluczy, a wszystkie dane w obszarze roboczym zostaną ponownie zaszyfrowane przy użyciu nowego klucza.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Ten diagram przedstawia sekcję szyfrowanie obszaru roboczego w Azure Portal.":::

>[!IMPORTANT]
>Podczas zmiany klucza szyfrowania obszaru roboczego należy zachować klucz do momentu zastąpienia go w obszarze roboczym nowym kluczem. Pozwala to na odszyfrowywanie danych przy użyciu starego klucza przed ponownym zaszyfrowaniem go przy użyciu nowego klucza.

Zasady magazynów kluczy platformy Azure na potrzeby automatycznego, okresowego obrotu kluczy lub akcji w kluczach mogą spowodować utworzenie nowych kluczowych wersji. Można wybrać ponowne zaszyfrowanie wszystkich danych w obszarze roboczym przy użyciu najnowszej wersji aktywnego klucza. Aby-ponownie zaszyfrować, Zmień klucz w Azure Portal na klucz tymczasowy, a następnie przełącz się z powrotem do klucza, który ma być używany do szyfrowania. Przykładowo w celu zaktualizowania szyfrowania danych przy użyciu najnowszej wersji usługi Active Key Klucz1 Zmień klucz zarządzany przez klienta na klucz tymczasowy, klucz2. Poczekaj na zakończenie szyfrowania z klucz2. Następnie Przełącz ponownie klucz zarządzany przez klienta z powrotem do Klucz1 — dane w obszarze roboczym zostaną zaszyfrowane przy użyciu najnowszej wersji programu Klucz1.

> [!NOTE]
> Usługa Azure Synapse Analytics aktywnie pracuje nad dodawaniem funkcji, aby automatycznie ponownie szyfrować dane po utworzeniu nowych wersji kluczy. Do momentu udostępnienia tej funkcji, aby zapewnić spójność w obszarze roboczym, należy wymusić ponowne szyfrowanie danych przy użyciu powyższego procesu.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>Transparent Data Encryption SQL z kluczami zarządzanymi przez usługę

Program SQL Transparent Data Encryption (TDE) jest dostępny dla dedykowanych pul SQL w obszarach roboczych, dla których *nie* włączono szyfrowania podwójnego. W tym typie obszaru roboczego klucz zarządzany przez usługę jest używany w celu zapewnienia podwójnego szyfrowania danych w dedykowanych pulach SQL. TDE z kluczem zarządzanym przez usługę można włączyć lub wyłączyć dla indywidualnych dedykowanych pul SQL.

## <a name="next-steps"></a>Następne kroki

[Zaimplementuj ochronę szyfrowania dla obszarów roboczych Synapse za pomocą wbudowanych zasad platformy Azure](../policy-reference.md)

