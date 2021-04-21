---
title: Azure Synapse Analytics szyfrowania
description: Artykuł wyjaśniacy szyfrowanie w Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 6ddafb0e76799e3d8011232534c505f97c79b22e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751134"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Szyfrowanie obszarów roboczych usługi Azure Synapse Analytics

W tym artykule opisano:
* Szyfrowanie danych w spoczynku w Synapse Analytics roboczych.
* Konfiguracja obszarów roboczych usługi Synapse w celu włączenia szyfrowania przy użyciu klucza zarządzanego przez klienta.
* Zarządzanie kluczami używanymi do szyfrowania danych w obszarach roboczych.

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie danych w spoczynku

Kompletne rozwiązanie do szyfrowania danych w spoczynku gwarantuje, że dane nigdy nie są utrwalane w postaci niezaszyfrowej. Podwójne szyfrowanie danych w spoczynku ogranicza zagrożenia związane z dwiema oddzielnymi warstwami szyfrowania w celu ochrony przed naruszeniami zabezpieczeń dowolnej pojedynczej warstwy. Azure Synapse Analytics oferuje drugą warstwę szyfrowania danych w obszarze roboczym przy użyciu klucza zarządzanego przez klienta. Ten klucz jest zabezpieczony w Azure Key Vault [,](../../key-vault/general/overview.md)co pozwala przejąć na własność zarządzanie kluczami i ich rotację.

Pierwsza warstwa szyfrowania dla usług platformy Azure jest włączona przy użyciu kluczy zarządzanych przez platformę. Domyślnie dyski i dane platformy Azure na kontach usługi Azure Storage są automatycznie szyfrowane podczas magazynowania. Aby dowiedzieć się więcej na temat sposobu szyfrowania w usłudze Microsoft Azure, zobacz [Omówienie usługi Azure Encryption.](../../security/fundamentals/encryption-overview.md)

## <a name="azure-synapse-encryption"></a>Azure Synapse szyfrowania

Ta sekcja pomoże Ci lepiej zrozumieć, w jaki sposób szyfrowanie kluczy zarządzane przez klienta jest włączone i wymuszane w obszarach roboczych usługi Synapse. To szyfrowanie używa istniejących lub nowych kluczy wygenerowanych w Azure Key Vault. Pojedynczy klucz służy do szyfrowania wszystkich danych w obszarze roboczym. Obszary robocze synapse obsługują klucze RSA z kluczami o rozmiarze 2048 i 3072 bajtów.

> [!NOTE]
> Obszary robocze synapse nie obsługują szyfrowania przy użyciu Elliptic-Curve kryptografii (ECC).

Dane w następujących składnikach usługi Synapse są szyfrowane przy użyciu klucza zarządzanego przez klienta skonfigurowanego na poziomie obszaru roboczego:
* Pule SQL
 * Dedykowane pule SQL
 * Bez serwera pul SQL
* Pule platformy Apache Spark
* Azure Data Factory środowiska Integration Runtime, potoki, zestawy danych.

## <a name="workspace-encryption-configuration"></a>Konfiguracja szyfrowania obszaru roboczego

Obszary robocze można skonfigurować tak, aby podczas tworzenia obszaru roboczego włączały podwójne szyfrowanie za pomocą klucza zarządzanego przez klienta. Wybierz opcję "Włącz podwójne szyfrowanie przy użyciu klucza zarządzanego przez klienta" na karcie "Zabezpieczenia" podczas tworzenia nowego obszaru roboczego. Możesz wprowadzić identyfikator URI identyfikatora klucza lub wybrać z listy magazynów kluczy w tym **samym regionie** co obszar roboczy. Sama Key Vault musi mieć **włączoną ochronę przed przeczyszczaniem.**

> [!IMPORTANT]
> Ustawienia konfiguracji podwójnego szyfrowania nie można zmienić po utworzeniu obszaru roboczego.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Na tym diagramie przedstawiono opcję, która należy wybrać, aby włączyć podwójne szyfrowanie obszaru roboczego przy użyciu klucza zarządzanego przez klienta.":::

### <a name="key-access-and-workspace-activation"></a>Aktywacja dostępu do klucza i obszaru roboczego

Model Azure Synapse szyfrowania za pomocą kluczy zarządzanych przez klienta obejmuje obszar roboczy, który uzyskuje dostęp do kluczy w u Azure Key Vault w celu szyfrowania i odszyfrowywania zgodnie z potrzebami. Klucze są dostępne dla obszaru roboczego za pośrednictwem zasad dostępu lub Azure Key Vault [RBAC.](../../key-vault/general/rbac-guide.md) Podczas udzielania uprawnień za pośrednictwem zasad dostępu usługi Azure Key Vault wybierz opcję ["Tylko aplikacja"](../../key-vault/general/security-overview.md#key-vault-authentication-options) podczas tworzenia zasad (wybierz tożsamość zarządzaną obszaru roboczego i nie dodawaj jej jako autoryzowanej aplikacji).

 Tożsamość zarządzana obszaru roboczego musi mieć przyznane uprawnienia, których potrzebuje w magazynie kluczy, zanim będzie można aktywować obszar roboczy. Takie etapowe podejście do aktywacji obszaru roboczego zapewnia, że dane w obszarze roboczym są szyfrowane przy użyciu klucza zarządzanego przez klienta. Należy pamiętać, że szyfrowanie można włączyć lub wyłączyć dla dedykowanych pul SQL — każda pula nie jest domyślnie włączona dla szyfrowania.

#### <a name="permissions"></a>Uprawnienia

Aby szyfrować lub odszyfrowywać dane w spoczynku, tożsamość zarządzana obszaru roboczego musi mieć następujące uprawnienia:
* WrapKey (aby wstawić klucz do Key Vault podczas tworzenia nowego klucza).
* Odszyfruj klucz (aby uzyskać klucz do odszyfrowywania).
* Pobierz (aby odczytać publiczną część klucza)

#### <a name="workspace-activation"></a>Aktywacja obszaru roboczego

Po utworzeniu obszaru roboczego (z włączonym podwójnym szyfrowaniem) pozostanie on w stanie "Oczekiwanie", dopóki aktywacja nie powiedzie się. Obszar roboczy należy aktywować, aby można było w pełni korzystać z wszystkich funkcji. Na przykład nową dedykowaną pulę SQL można utworzyć tylko po zakończeniu aktywacji. Przyznaj tożsamości zarządzanej obszaru roboczego dostęp do magazynu kluczy, a następnie kliknij link aktywacji w obszarze roboczym Azure Portal transparentu. Po pomyślnym zakończeniu aktywacji obszar roboczy jest gotowy do użycia z gwarancją, że wszystkie dane w nim są chronione za pomocą klucza zarządzanego przez klienta. Jak wspomniano wcześniej, aby aktywacja powiodła się, magazyn kluczy musi mieć włączoną ochronę przed przeczyszczaniem.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Na tym diagramie przedstawiono transparent z linkiem aktywacji dla obszaru roboczego.":::


### <a name="manage-the-workspace-customer-managed-key"></a>Zarządzanie kluczem zarządzanym przez klienta obszaru roboczego 

Klucz zarządzany przez klienta używany do szyfrowania danych można zmienić na stronie **Szyfrowanie** w Azure Portal. W tym miejscu możesz również wybrać nowy klucz przy użyciu identyfikatora klucza lub wybrać magazyny kluczy, do których masz dostęp w tym samym regionie co obszar roboczy. Jeśli wybierzesz klucz w innym magazynie kluczy niż używany wcześniej, przyznaj tożsamości zarządzanej obszaru roboczego uprawnienia "Pobierz", "Opakuj" i "Odpakuj" w nowym magazynie kluczy. Obszar roboczy zweryfikuje swój dostęp do nowego magazynu kluczy, a wszystkie dane w obszarze roboczym zostaną ponownie zaszyfrowane przy użyciu nowego klucza.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Na tym diagramie przedstawiono sekcję Szyfrowanie obszaru roboczego w Azure Portal.":::

>[!IMPORTANT]
>Podczas zmieniania klucza szyfrowania obszaru roboczego należy zachować klucz, dopóki nie zastąpisz go w obszarze roboczym nowym kluczem. Ma to na celu umożliwienie odszyfrowywania danych przy użyciu starego klucza, zanim zostanie on ponownie zaszyfrowany przy użyciu nowego klucza.

Zasady usługi Azure Key Vault dotyczące automatycznej, okresowej rotacji kluczy lub akcji na kluczach mogą spowodować utworzenie nowych wersji kluczy. Możesz ponownie zaszyfrować wszystkie dane w obszarze roboczym przy użyciu najnowszej wersji aktywnego klucza. Aby ponownie zaszyfrować, zmień klucz w Azure Portal na klucz tymczasowy, a następnie wróć do klucza, który ma być używany do szyfrowania. Aby na przykład zaktualizować szyfrowanie danych przy użyciu najnowszej wersji klucza aktywnego Klucz1, zmień klucz zarządzany przez klienta obszaru roboczego na klucz tymczasowy Klucz2. Poczekaj na zakończenie szyfrowania za pomocą klucza Key2. Następnie przełącz klucz zarządzany przez klienta obszaru roboczego z powrotem na klucz Key1-data w obszarze roboczym zostanie ponownie zaszyfrowany przy użyciu najnowszej wersji klucza Key1.

> [!NOTE]
> Azure Synapse Analytics aktywnie pracuje nad dodawaniem funkcji automatycznego ponownego szyfrowania danych po utworzeniu nowych wersji kluczy. Dopóki ta funkcja nie będzie dostępna, aby zapewnić spójność w obszarze roboczym, wymusz ponowne szyfrowanie danych przy użyciu procesu opisanego powyżej.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>Program SQL Transparent Data Encryption kluczami zarządzanymi przez usługę

Program SQL Transparent Data Encryption (TDE) jest dostępny dla dedykowanych pul SQL w obszarach roboczych, w których *nie* włączono podwójnego szyfrowania. W tym typie obszaru roboczego klucz zarządzany przez usługę jest używany do zapewnienia podwójnego szyfrowania danych w dedykowanych pulach SQL. Funkcję TDE z kluczem zarządzanym przez usługę można włączyć lub wyłączyć dla poszczególnych dedykowanych pul SQL.

## <a name="next-steps"></a>Następne kroki

[Implementowanie ochrony szyfrowania dla obszarów roboczych usługi Synapse przy użyciu wbudowanych zasad platformy Azure](../policy-reference.md)

