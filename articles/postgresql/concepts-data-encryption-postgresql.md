---
title: Szyfrowanie danych z kluczem zarządzanym przez klienta — Azure Database for PostgreSQL — pojedynczy serwer
description: Azure Database for PostgreSQL szyfrowanie danych za pomocą jednego serwera z kluczem zarządzanym przez klienta umożliwia Bring Your Own Key (BYOK) na potrzeby ochrony danych w spoczynku. Umożliwia to również organizacjom rozdzielanie obowiązków związanych z zarządzaniem kluczami i danymi.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 730d12558e413d96909914d06187d0d5f89ec661
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567524"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>Azure Database for PostgreSQL szyfrowanie danych na jednym serwerze z kluczem zarządzanym przez klienta

Usługa Azure PostgreSQL wykorzystuje [szyfrowanie usługi Azure Storage](../storage/common/storage-service-encryption.md) , aby domyślnie szyfrować dane przy użyciu kluczy zarządzanych przez firmę Microsoft. W przypadku użytkowników usługi Azure PostgreSQL jest to bardzo podobna do Transparent Data Encryption (TDE) w innych bazach danych, takich jak SQL Server. Wiele organizacji wymaga pełnej kontroli dostępu do danych przy użyciu klucza zarządzanego przez klienta. Szyfrowanie danych za pomocą kluczy zarządzanych przez klienta dla Azure Database for PostgreSQL pojedynczy serwer umożliwia korzystanie z własnego klucza (BYOK) na potrzeby ochrony danych w spoczynku. Umożliwia to również organizacjom rozdzielanie obowiązków związanych z zarządzaniem kluczami i danymi. W przypadku szyfrowania zarządzanego przez klienta odpowiadasz za cykl życia klucza, uprawnienia do użycia klucza i inspekcje operacji na kluczach oraz w pełni kontrolujesz te elementy.

Szyfrowanie danych z kluczami zarządzanymi przez klienta dla Azure Database for PostgreSQL pojedynczego serwera, jest ustawiany na poziomie serwera. Dla danego serwera klucz zarządzany przez klienta o nazwie klucz szyfrowania klucza (KEK) jest używany do szyfrowania klucza szyfrowania danych używanego przez usługę. KEK jest kluczem asymetrycznym przechowywanym w wystąpieniu [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) klienta i zarządzanym przez klienta. Klucz szyfrowania klucza (KEK) i klucz szyfrowania danych (w tym artykule) opisano szczegółowo w dalszej części tego artykułu.

Key Vault to oparty na chmurze zewnętrzny system zarządzania kluczami. Jest ona wysoce dostępna i zapewnia skalowalny, bezpieczny magazyn dla kluczy kryptograficznych RSA, opcjonalnie obsługiwane przez program FIPS 140-2 Level 2 sprawdzone sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). Nie zezwala na bezpośredni dostęp do przechowywanego klucza, ale zapewnia usługi szyfrowania i odszyfrowywania do autoryzowanych jednostek. Key Vault może wygenerować klucz, zaimportować go lub [przetransferować z lokalnego urządzenia HSM](../key-vault/keys/hsm-protected-keys.md).

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach świadczenia usługi Azure, w których Azure Database for PostgreSQL jeden serwer obsługuje warstwy cenowe "Ogólnego przeznaczenia" i "zoptymalizowane pod kątem pamięci". Aby uzyskać inne ograniczenia, zapoznaj się z sekcją [ograniczenia](concepts-data-encryption-postgresql.md#limitations) .

## <a name="benefits"></a>Korzyści

Szyfrowanie danych za pomocą kluczy zarządzanych przez klienta dla Azure Database for PostgreSQL pojedynczego serwera zapewnia następujące korzyści:

* Dostęp do danych jest w pełni kontrolowany przez użytkownika przez możliwość usuwania klucza i uzyskiwania dostępu do bazy danych 
*    Pełna kontrola nad cyklem życia klucza, włącznie z rotacją klucza do dopasowania do zasad firmowych
*    Centralne zarządzanie i organizacja kluczy w Azure Key Vault
*    Możliwość wdrożenia separacji obowiązków między biurami zabezpieczeń i administratorami systemów i administratorów systemu

## <a name="terminology-and-description"></a>Terminologia i opis

**Klucz szyfrowania danych (unencryption)**: symetryczny klucz AES256 używany do szyfrowania partycji lub bloku danych. Szyfrowanie każdego bloku danych przy użyciu innego klucza sprawia, że ataki analizy kryptograficznej są trudniejsze. Dostęp do DEKs jest wymagany przez dostawcę zasobów lub wystąpienie aplikacji, które szyfruje i odszyfrowuje określony blok. Gdy zastąpisz klucz szyfrowania danych za pomocą nowego klucza, tylko dane w skojarzonym bloku muszą zostać ponownie zaszyfrowane przy użyciu nowego klucza.

**Klucz szyfrowania klucza (KEK)**: klucz szyfrowania używany do szyfrowania DEKs. Element KEK, który nigdy nie pozostawia Key Vault umożliwia zaszyfrowanie i kontrola DEKs. Jednostka, która ma dostęp do KEK, może różnić się od jednostki, która wymaga tego klucza. Ponieważ KEK jest wymagany do odszyfrowania DEKs, KEK jest efektywnie jednym punktem, przez który DEKs może być skutecznie usunięty przez usunięcie KEK.

DEKs szyfrowany za pomocą KEKs są przechowywane oddzielnie. Tylko jednostka z dostępem do KEK może odszyfrować te DEKs. Aby uzyskać więcej informacji, zobacz [zabezpieczenia w szyfrowaniu](../security/fundamentals/encryption-atrest.md)w stanie spoczynku.

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Jak działa szyfrowanie danych za pomocą klucza zarządzanego przez klienta

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png" alt-text="Diagram przedstawiający przegląd Bring Your Own Key":::

Aby serwer PostgreSQL mógł używać kluczy zarządzanych przez klienta przechowywanych w Key Vault na potrzeby szyfrowania bazy danych programu, administrator Key Vault ma następujące prawa dostępu do serwera:

* **Pobierz**: w celu pobrania publicznej części i właściwości klucza w magazynie kluczy.
* **wrapKey**: aby można było zaszyfrować klucz szyfrowania danych. Zaszyfrowany klucz szyfrowania danych jest przechowywany w Azure Database for PostgreSQL.
* **unwrapKey**: aby można było odszyfrować klucz szyfrowania danych. Do szyfrowania/odszyfrowywania danych Azure Database for PostgreSQL musi zostać zaszyfrowany/odszyfrowany.

Administrator magazynu kluczy może również [włączyć rejestrowanie Key Vault zdarzeń inspekcji](../azure-monitor/insights/key-vault-insights-overview.md), aby mogły być później poddane inspekcji.

Gdy serwer jest skonfigurowany do korzystania z klucza zarządzanego przez klienta przechowywanego w magazynie kluczy, serwer wysyła do magazynu kluczy, w którym są szyfrowane. Key Vault zwraca zaszyfrowany klucz szyfrowania danych, który jest przechowywany w bazie. Analogicznie, jeśli jest to konieczne, serwer wysyła chronione szyfrowanie szyfrowania danych do magazynu kluczy w celu odszyfrowania. Audytorzy mogą używać Azure Monitor do przeglądania dzienników zdarzeń inspekcji Key Vault, jeśli rejestrowanie jest włączone.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Wymagania dotyczące konfigurowania szyfrowania danych dla Azure Database for PostgreSQL pojedynczego serwera

Poniżej przedstawiono wymagania dotyczące konfigurowania Key Vault:

* Key Vault i Azure Database for PostgreSQL pojedynczy serwer musi należeć do tej samej dzierżawy Azure Active Directory (Azure AD). Między dzierżawcami Key Vault i interakcje serwera nie są obsługiwane. Późniejsze przeniesienie zasobu Key Vault wymaga ponownego skonfigurowania szyfrowania danych.
* Magazyn kluczy musi być ustawiony na 90 dni dla "dni do przechowywania usuniętych magazynów". Jeśli istniejący magazyn kluczy został skonfigurowany z niższym numerem, należy utworzyć nowy magazyn kluczy, ponieważ nie będzie można go zmodyfikować po utworzeniu.
* Włącz funkcję usuwania nietrwałego w magazynie kluczy, aby chronić przed utratą danych w przypadku, gdy zostanie usunięte wystąpienie przypadkowe (lub Key Vault). Zasoby usunięte nietrwale są przechowywane przez 90 dni, chyba że użytkownik odzyskuje lub przeczyści je w międzyczasie. Akcje odzyskania i przeczyszczania mają własne uprawnienia skojarzone z zasadami dostępu Key Vault. Funkcja usuwania nietrwałego jest domyślnie wyłączona, ale można ją włączyć za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure (należy zauważyć, że nie można włączyć jej za pomocą Azure Portal). 
* Włącz ochronę przed Przeczyszczaniem w celu wymuszenia obowiązkowego okresu przechowywania dla usuniętych magazynów i obiektów magazynu
* Udziel Azure Database for PostgreSQL jednoserwerowego dostępu do magazynu kluczy za pomocą uprawnień Get, wrapKey i unwrapKey przy użyciu unikatowej tożsamości zarządzanej. W Azure Portal unikatowa tożsamość "usługa" jest automatycznie tworzona, gdy szyfrowanie danych jest włączone na pojedynczym serwerze PostgreSQL. Aby uzyskać szczegółowe instrukcje krok po kroku, jeśli używasz Azure Portal, zobacz [szyfrowanie danych dla Azure Database for PostgreSQL pojedynczego serwera przy użyciu Azure Portal](howto-data-encryption-portal.md) .

Poniżej przedstawiono wymagania dotyczące konfigurowania klucza zarządzanego przez klienta:

* Klucz zarządzany przez klienta, który ma być używany na potrzeby szyfrowania, może być tylko asymetryczny, RSA 2048.
* Data aktywacji klucza (jeśli jest ustawiona) musi być datą i godziną w przeszłości. Data wygaśnięcia (jeśli jest ustawiona) musi być przyszłą datą i godziną.
* Klucz musi być w stanie *włączony* .
* W przypadku [importowania istniejącego klucza](/rest/api/keyvault/ImportKey/ImportKey) do magazynu kluczy upewnij się, że jest on udostępniany w obsługiwanych formatach plików ( `.pfx` , `.byok` , `.backup` ).

## <a name="recommendations"></a>Zalecenia

W przypadku korzystania z szyfrowania danych przy użyciu klucza zarządzanego przez klienta poniżej przedstawiono zalecenia dotyczące konfigurowania Key Vault:

* Ustaw blokadę zasobów Key Vault, aby określić, kto może usunąć ten krytyczny zasób i zapobiegać przypadkowemu lub nieautoryzowanemu usuwaniu.
* Włącz inspekcję i raportowanie dla wszystkich kluczy szyfrowania. Key Vault udostępnia dzienniki, które można łatwo wstrzyknąć do innych informacji o zabezpieczeniach i narzędzi do zarządzania zdarzeniami. Azure Monitor Log Analytics to jeden przykład usługi, która jest już zintegrowana.
* Upewnij się, że Key Vault i Azure Database for PostgreSQL pojedynczy serwer znajduje się w tym samym regionie, aby zapewnić szybszy dostęp do zawijania danych i odpakowywanie operacji.
* Zablokuj Magazyn kluczy platformy Azure tylko do **prywatnego punktu końcowego i wybranych sieci** i Zezwalaj tylko *zaufanym usługom firmy Microsoft* na Zabezpieczanie zasobów.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="Trusted-Service-with-AKV":::

Poniżej przedstawiono zalecenia dotyczące konfigurowania klucza zarządzanego przez klienta:

* Przechowuj kopię klucza zarządzanego przez klienta w bezpiecznym miejscu lub wykorzystaj ją w usłudze Escrow.

* Jeśli Key Vault generuje klucz, Utwórz kopię zapasową klucza przed użyciem klucza po raz pierwszy. Możesz przywrócić kopię zapasową tylko do Key Vault. Aby uzyskać więcej informacji na temat polecenia Backup, zobacz [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Niedostępny warunek klucza zarządzanego przez klienta

W przypadku konfigurowania szyfrowania danych przy użyciu klucza zarządzanego przez klienta w programie Key Vault dostęp do tego klucza jest wymagany, aby serwer mógł pozostać w trybie online. Jeśli serwer utraci dostęp do klucza zarządzanego przez klienta w Key Vault, serwer rozpoczyna odrzucanie wszystkich połączeń w ciągu 10 minut. Serwer wystawia odpowiedni komunikat o błędzie i zmieni stan serwera na *niedostępny*. Niektóre z przyczyn, dla których serwer może dotrzeć do tego stanu:

* Jeśli zostanie utworzony serwer przywracania punktu w czasie dla Azure Database for PostgreSQL jednego serwera, który ma włączone szyfrowanie danych, nowo utworzony serwer będzie w stanie *niedostępnym* . Możesz naprawić stan serwera za pomocą [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) lub [interfejsu wiersza polecenia](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* Jeśli utworzymy replikę odczytu dla Azure Database for PostgreSQL pojedynczego serwera, na którym włączono szyfrowanie danych, serwer repliki będzie w stanie *niedostępnym* . Możesz naprawić stan serwera za pomocą [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) lub [interfejsu wiersza polecenia](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers).
* W przypadku usunięcia magazynu kluczy Azure Database for PostgreSQL pojedynczy serwer nie będzie mógł uzyskać dostępu do klucza i przejdzie do stanu *niedostępności* . Odzyskaj [Key Vault](../key-vault/general/key-vault-recovery.md) i ponownie Zweryfikuj szyfrowanie danych, aby *udostępnić serwer.*
* Jeśli klucz zostanie usunięty z magazynu kluczy, Azure Database for PostgreSQL pojedynczy serwer nie będzie mógł uzyskać dostępu do klucza i przejdzie do stanu *niedostępności* . Odzyskaj [klucz](../key-vault/general/key-vault-recovery.md) i ponownie Zweryfikuj szyfrowanie danych, aby *udostępnić serwer.*
* Jeśli klucz zapisany w magazynie kluczy platformy Azure wygaśnie, klucz stanie się nieprawidłowy, a Azure Database for PostgreSQL pojedynczy serwer przejdzie w stan *niedostępności* . Zwiększ datę wygaśnięcia klucza przy użyciu [interfejsu wiersza polecenia](/cli/azure/keyvault/key#az-keyvault-key-set-attributes) , a następnie ponownie Zweryfikuj szyfrowanie danych, aby udostępnić *serwer.*

### <a name="accidental-key-access-revocation-from-key-vault"></a>Przypadkowe odwoływanie dostępu do klucza z Key Vault

Może się zdarzyć, że ktoś mający wystarczające prawa dostępu do Key Vault przypadkowo wyłącza dostęp serwera do klucza przez:

* Odwoływanie uprawnień Get, wrapKey i unwrapKey magazynu kluczy z serwera.
* Usuwanie klucza.
* Usuwanie magazynu kluczy.
* Zmiana reguł zapory magazynu kluczy.

* Usuwanie tożsamości zarządzanej serwera w usłudze Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitoruj klucz zarządzany przez klienta w Key Vault

Aby monitorować stan bazy danych i włączyć alerty na potrzeby utraty dostępu do funkcji ochrony za pomocą szyfrowania danych, skonfiguruj następujące funkcje platformy Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md): niedostępna baza danych, która utraciła dostęp do klucza klienta, jest wyświetlana jako "niedostępna" po odmowie pierwszego połączenia z bazą danych.
* [Dziennik aktywności](../service-health/alerts-activity-log-service-notifications-portal.md): Jeśli dostęp do klucza klienta w Key Vault zarządzanym przez klienta nie powiedzie się, wpisy zostaną dodane do dziennika aktywności. Można przywrócić dostęp najszybciej, jak to możliwe, w przypadku tworzenia alertów dla tych zdarzeń.

* [Grupy akcji](../azure-monitor/platform/action-groups.md): Zdefiniuj te grupy w celu wysyłania powiadomień i alertów na podstawie Twoich preferencji.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Przywracanie i replikowanie z kluczem zarządzanym klienta w Key Vault

Po zaszyfrowaniu pojedynczego serwera Azure Database for PostgreSQL z kluczem zarządzanym przez klienta przechowywanego w Key Vault, nowo utworzona kopia serwera zostanie również zaszyfrowana. Tę nową kopię można wykonać za pomocą operacji w trybie lokalnym lub z możliwością przywracania geograficznego albo za pomocą replik odczytu. Jednak kopię można zmienić w celu odzwierciedlenia klucza zarządzanego nowego klienta na potrzeby szyfrowania. Gdy klucz zarządzany przez klienta zostanie zmieniony, stare kopie zapasowe serwera zaczynają korzystać z najnowszego klucza.

Aby uniknąć problemów podczas konfigurowania szyfrowania danych zarządzanych przez klienta podczas przywracania lub tworzenia repliki odczytu, należy wykonać następujące kroki na serwerach podstawowych i przywróconych/repliki:

* Zainicjuj proces przywracania lub odczytywania repliki z podstawowego Azure Database for PostgreSQL jednego serwera.
* Zachowaj nowo utworzony serwer (przywrócony/Replica) w stanie niedostępnym, ponieważ jego unikatowa tożsamość nie otrzymała jeszcze uprawnień do Key Vault.
* Na serwerze przywrócenia/repliki ponownie sprawdź poprawność klucza zarządzanego przez klienta w ustawieniach szyfrowania danych. Gwarantuje to, że nowo utworzony serwer ma uprawnienia zawijania i odwinięcia klucza przechowywanego w Key Vault.

## <a name="limitations"></a>Ograniczenia

W przypadku Azure Database for PostgreSQL obsługa szyfrowania danych magazynowanych przy użyciu klucza zarządzanego przez klientów (CMK) ma kilka ograniczeń —

* Obsługa tej funkcji jest ograniczona do warstw cenowych **ogólnego przeznaczenia** i **zoptymalizowanych pod kątem pamięci** .
* Ta funkcja jest obsługiwana tylko w regionach i na serwerach, które obsługują magazyn o pojemności do 16 TB. Listę regionów świadczenia usługi Azure obsługujących magazyn o wartości do 16TB można znaleźć w sekcji magazyn w dokumentacji [poniżej](concepts-pricing-tiers.md#storage) .

    > [!NOTE]
    > - Wszystkie nowe serwery PostgreSQL utworzone w wymienionych powyżej regionach obsługują szyfrowanie z kluczami Menedżera **klienta.** Serwer przywrócony do punktu w czasie (kopie) lub odczytana replika nie będzie kwalifikować, chociaż w teorii są "nowe".
    > - Aby sprawdzić, czy serwer aprowizacji obsługuje do 16TB, możesz przejść do bloku warstwa cenowa w portalu i zobaczyć maksymalny rozmiar magazynu obsługiwany przez serwer aprowizacji. Jeśli można przenieść suwak do 4 TB, serwer może nie obsługiwać szyfrowania z użyciem kluczy zarządzanych przez klienta. Jednak dane są szyfrowane przy użyciu kluczy zarządzanych przez usługę przez cały czas. AskAzureDBforPostgreSQL@service.microsoft.comJeśli masz jakieś pytania, skontaktuj się z Tobą.

* Szyfrowanie jest obsługiwane tylko przy użyciu klucza kryptograficznego RSA 2048.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak skonfigurować szyfrowanie danych za pomocą klucza zarządzanego przez klienta dla usługi Azure Database for PostgreSQL na jednym serwerze przy użyciu Azure Portal](howto-data-encryption-portal.md).
