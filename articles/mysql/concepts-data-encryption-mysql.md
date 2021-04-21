---
title: Szyfrowanie danych za pomocą klucza zarządzanego przez klienta — Azure Database for MySQL
description: Azure Database for MySQL danych za pomocą klucza zarządzanego przez klienta umożliwia Bring Your Own Key (BYOK) w celu ochrony danych w spoczynku. Umożliwia to również organizacjom rozdzielanie obowiązków związanych z zarządzaniem kluczami i danymi.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 809827fc61cde3c8077484dbe2a5be5037609cd8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748552"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Azure Database for MySQL danych za pomocą klucza zarządzanego przez klienta

Szyfrowanie danych za pomocą kluczy zarządzanych przez klienta w usłudze Azure Database for MySQL umożliwia korzystanie z własnego klucza (Bring Your Own Key, BYOK) na potrzeby ochrony danych w spoczynku. Umożliwia to również organizacjom rozdzielanie obowiązków związanych z zarządzaniem kluczami i danymi. W przypadku szyfrowania zarządzanego przez klienta odpowiadasz za cykl życia klucza, uprawnienia do użycia klucza i inspekcje operacji na kluczach oraz w pełni kontrolujesz te elementy.

Szyfrowanie danych za pomocą kluczy zarządzanych przez klienta Azure Database for MySQL, jest ustawiane na poziomie serwera. Dla danego serwera klucz zarządzany przez klienta, nazywany kluczem szyfrowania klucza (KEK), jest używany do szyfrowania klucza szyfrowania danych (DEK) używanego przez usługę. Klucz szyfrowania kluczy jest kluczem asymetrycznym przechowywanym w wystąpieniu zarządzanym przez klienta [Azure Key Vault](../key-vault/general/security-overview.md) klienta. Klucz szyfrowania klucza (KEK) i klucz szyfrowania danych (DEK) opisano bardziej szczegółowo w dalszej części tego artykułu.

Key Vault to oparty na chmurze zewnętrzny system zarządzania kluczami. Jest wysoce dostępna i zapewnia skalowalny, bezpieczny magazyn kluczy kryptograficznych RSA, opcjonalnie zabezpieczony przez sprzętowe moduły zabezpieczeń (HSM) zweryfikowane w wersji FIPS 140-2 poziom 2. Nie zezwala na bezpośredni dostęp do przechowywanego klucza, ale zapewnia autoryzowanym jednostkom usługi szyfrowania i odszyfrowywania. Key Vault wygenerować klucz, zaimportować go lub przenieść z [lokalnego urządzenia HSM.](../key-vault/keys/hsm-protected-keys.md)

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach świadczenia usługi Azure, Azure Database for MySQL obsługuje warstwy cenowe "Ogólnego przeznaczenia" i "Zoptymalizowane pod kątem pamięci". Aby uzyskać informacje o innych ograniczeniach, zapoznaj się z [sekcją ograniczenia.](concepts-data-encryption-mysql.md#limitations)

## <a name="benefits"></a>Korzyści

Szyfrowanie danych za pomocą kluczy zarządzanych przez klienta Azure Database for MySQL zapewnia następujące korzyści:

* Dostęp do danych jest w pełni kontrolowany przez użytkownika przez możliwość usunięcia klucza i uniemożliwienia dostępu do bazy danych 
* Pełna kontrola nad cyklem życia klucza, w tym rotacja klucza w celu dostosowania go do zasad firmowych
* Centralne zarządzanie kluczami i ich organizacja w Azure Key Vault
* Możliwość wdrożenia podziału obowiązków między pracowników ochrony oraz administratorów baz danych i administratorów systemu


## <a name="terminology-and-description"></a>Terminologia i opis

**Klucz szyfrowania danych (DEK):** symetryczny klucz AES256 używany do szyfrowania partycji lub bloku danych. Szyfrowanie każdego bloku danych przy użyciu innego klucza utrudnia ataki analizy kryptograficzne. Dostawca zasobów lub wystąpienie aplikacji, które szyfruje i odszyfrowuje określony blok, musi mieć dostęp do deksów. Jeśli zastąpisz klucz szyfrowania danych nowym kluczem, tylko dane w skojarzonym bloku muszą zostać ponownie zaszyfrowane przy użyciu nowego klucza.

**Klucz szyfrowania klucza (KEK):** klucz szyfrowania używany do szyfrowania dek. KEK, który nigdy Key Vault, umożliwia szyfrowanie i kontrolowanie samych deksów. Jednostka, która ma dostęp do KEK może być inny niż jednostka, która wymaga DEK. Ponieważ klucz szyfrowania kluczy jest wymagany do odszyfrowania kluczy szyfrowania kluczy, klucz szyfrowania kluczy jest efektywnie pojedynczym punktem, za pomocą którego można skutecznie usunąć klucze szyfrowania kluczy przez usunięcie klucza szyfrowania kluczy.

Dek, szyfrowane za pomocą KEK, są przechowywane oddzielnie. Tylko jednostka z dostępem do klucza szyfrowania kluczy może odszyfrować te dek. Aby uzyskać więcej informacji, zobacz Security in encryption at rest (Zabezpieczenia [szyfrowania danych w spoczynku).](../security/fundamentals/encryption-atrest.md)

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>Jak działa szyfrowanie danych za pomocą klucza zarządzanego przez klienta

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="Diagram przedstawiający przegląd Bring Your Own Key":::

Aby serwer MySQL mógł używać kluczy zarządzanych przez klienta przechowywanych w programie Key Vault do szyfrowania klucza szyfrowania danych, administrator Key Vault zapewnia następujące prawa dostępu do serwera:

* **get**: do pobierania części publicznej i właściwości klucza w magazynie kluczy.
* **wrapKey:** aby można było zaszyfrować klucz szyfrowania danych. Zaszyfrowany kod DEK jest przechowywany w Azure Database for MySQL.
* **unwrapKey:** aby można było odszyfrować klucz szyfrowania danych. Azure Database for MySQL odszyfrowany klucz szyfrowania danych do szyfrowania/odszyfrowywania danych

Administrator magazynu kluczy może również włączyć rejestrowanie zdarzeń [Key Vault inspekcji,](../azure-monitor/insights/key-vault-insights-overview.md)aby można było je później poddać inspekcji.

Gdy serwer jest skonfigurowany do używania klucza zarządzanego przez klienta przechowywanego w magazynie kluczy, wysyła klucz szyfrowania do magazynu kluczy w celu szyfrowania. Key Vault zwraca zaszyfrowany DEK, który jest przechowywany w bazie danych użytkownika. Podobnie w razie potrzeby serwer wysyła chroniony klucz szyfrowania danych do magazynu kluczy w celu odszyfrowania. Audytorzy mogą używać Azure Monitor do przeglądania dzienników Key Vault inspekcji, jeśli rejestrowanie jest włączone.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Wymagania dotyczące konfigurowania szyfrowania danych dla Azure Database for MySQL

Poniżej przedstawiono wymagania dotyczące konfigurowania Key Vault:

* Key Vault i Azure Database for MySQL muszą należeć do tej samej Azure Active Directory dzierżawy usługi (Azure AD). Interakcje między Key Vault i serwera nie są obsługiwane. Przeniesienie Key Vault zasobów wymaga ponownego skonfigurowania szyfrowania danych.
* Włącz funkcję [usuwania nie](../key-vault/general/soft-delete-overview.md) soft w magazynie kluczy z okresem przechowywania ustawionym na **90** dni, aby chronić przed utratą danych w przypadku przypadkowego usunięcia klucza (lub Key Vault). Zasoby, które nie zostały usunięte, są domyślnie przechowywane przez 90 dni, chyba że okres przechowywania jest jawnie ustawiony na <=90 dni. Akcje odzyskiwania i przeczyszczania mają własne uprawnienia skojarzone w zasadach Key Vault dostępu. Funkcja usuwania nie programowego jest domyślnie wyłączona, ale można ją włączyć za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure (pamiętaj, że nie można jej włączyć za pośrednictwem Azure Portal).
* Włącz funkcję [Przeczyść ochronę](../key-vault/general/soft-delete-overview.md#purge-protection) w magazynie kluczy z okresem przechowywania ustawionym **na 90 dni.** Ochronę przed przeczyszczaniem można włączyć tylko po włączeniu usuwania nie programowego. Można ją włączona za pomocą interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Gdy ochrona przed przeczyszczaniem jest wł., magazynu lub obiektu w stanie usunięcia nie można przeczyścić, dopóki nie minął okres przechowywania. Magazyny i obiekty, które nie zostały usunięte, nadal można odzyskać, zapewniając, że zostaną przestrzegane zasady przechowywania. 
* Przyznaj Azure Database for MySQL do magazynu kluczy przy użyciu uprawnień get, wrapKey i unwrapKey przy użyciu jego unikatowej tożsamości zarządzanej. W Azure Portal jest automatycznie tworzona unikatowa tożsamość usługi po włączeniu szyfrowania danych w programie MySQL. Zobacz [Konfigurowanie szyfrowania danych dla programu MySQL,](howto-data-encryption-portal.md) aby uzyskać szczegółowe instrukcje krok po kroku, gdy używasz Azure Portal.

Poniżej przedstawiono wymagania dotyczące konfigurowania klucza zarządzanego przez klienta:

* Klucz zarządzany przez klienta do szyfrowania klucza szyfrowania danych może być tylko asymetryczny, RSA 2048.
* Data aktywacji klucza (jeśli jest ustawiona) musi być datą i godziną w przeszłości. Data wygaśnięcia nie jest ustawiona.
* Klucz musi być w stanie *Włączone.*
* Klucz musi mieć usuwanie [nieukońcowe](../key-vault/general/soft-delete-overview.md) z okresem przechowywania ustawionym na **90 dni.** W ten sposób niejawnie ustawiany jest wymagany atrybut klucza recoveryLevel: "Recoverable". Jeśli okres przechowywania został ustawiony na < 90 dni, dla ustawienia recoveryLevel: "CustomizedRecoverable" (Dostosowanyodzyskowanie) nie jest wymagane, dlatego upewnij się, że ustawiono okres przechowywania na **90 dni.**
* Klucz musi mieć [włączoną ochronę przed przeczyszczaniem.](../key-vault/general/soft-delete-overview.md#purge-protection)
* Jeśli importujesz [istniejący klucz](/rest/api/keyvault/ImportKey/ImportKey) do magazynu kluczy, upewnij się, że jest on dostępny w obsługiwanych formatach plików ( , , `.pfx` `.byok` `.backup` ).

## <a name="recommendations"></a>Zalecenia

Jeśli używasz szyfrowania danych przy użyciu klucza zarządzanego przez klienta, poniżej znajdują się zalecenia dotyczące konfigurowania Key Vault:

* Ustaw blokadę zasobu na Key Vault, aby kontrolować, kto może usuwać ten krytyczny zasób i zapobiegać przypadkowemu lub nieautoryzowanemu usunięciu.
* Włącz inspekcję i raportowanie dla wszystkich kluczy szyfrowania. Key Vault udostępnia dzienniki, które można łatwo wstrzyknąć do innych narzędzi do zarządzania informacjami i zdarzeniami zabezpieczeń. Azure Monitor usługi Log Analytics jest jednym z przykładów usługi, która jest już zintegrowana.
* Upewnij się, Key Vault i Azure Database for MySQL znajdują się w tym samym regionie, aby zapewnić szybszy dostęp do operacji zawijania I odpakowania.
* Zablokuj usługę Azure KeyVault tylko do prywatnego punktu końcowego i wybranych sieci, zezwalaj na zabezpieczanie zasobów tylko zaufanym usługom firmy *Microsoft.* 

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

Poniżej znajdują się zalecenia dotyczące konfigurowania klucza zarządzanego przez klienta:

* Przechowuj kopię klucza zarządzanego przez klienta w bezpiecznym miejscu lub przekłać go do usługi escrow.

* Jeśli Key Vault klucz, utwórz kopię zapasową klucza przed użyciem klucza po raz pierwszy. Kopię zapasową można przywrócić tylko do Key Vault. Aby uzyskać więcej informacji o poleceniu kopii zapasowej, [zobacz Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Warunek niedostępności klucza zarządzanego przez klienta

W przypadku konfigurowania szyfrowania danych za pomocą klucza zarządzanego przez klienta w programie Key Vault serwer musi mieć ciągły dostęp do tego klucza, aby serwer pozostał w trybie online. Jeśli serwer utraci dostęp do klucza zarządzanego przez klienta w Key Vault, serwer rozpocznie odmawianie wszystkich połączeń w ciągu 10 minut. Serwer wysyła odpowiedni komunikat o błędzie i zmienia stan serwera na *Niedostępny.* Niektóre z powodów, dla których serwer może osiągnąć ten stan, to:

* Jeśli utworzymy serwer przywracania do punktu w czasie dla serwera Azure Database for MySQL z włączonym szyfrowaniem danych, nowo utworzony serwer będzie w stanie *Niedostępny.* Ten problem można rozwiązać za pomocą [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) wiersza [polecenia lub interfejsu wiersza polecenia.](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)
* Jeśli utworzymy replikę do odczytu dla twojej Azure Database for MySQL z włączonym szyfrowaniem danych, serwer repliki będzie w stanie *Niedostępny.* Ten problem można rozwiązać za pomocą [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) wiersza [polecenia lub interfejsu wiersza polecenia.](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)
* Jeśli usuniesz usługę KeyVault, Azure Database for MySQL nie będzie mógł uzyskać dostępu do klucza i przechodzi w *stan Niedostępny.* [Odzyskaj Key Vault](../key-vault/general/key-vault-recovery.md) i ponownie sprawdź szyfrowanie danych, aby udostępnić *serwer .*
* Jeśli usuniemy klucz z funkcji KeyVault, Azure Database for MySQL nie będzie mógł uzyskać dostępu do klucza i przeniesie się do *stanu* Niedostępność. [Odzyskaj klucz](../key-vault/general/key-vault-recovery.md) i ponownie sprawdź szyfrowanie danych, aby udostępnić *serwer .*
* Jeśli klucz przechowywany w usłudze Azure KeyVault wygaśnie, klucz stanie się nieprawidłowy, a klucz Azure Database for MySQL stanie się *niedostępny.* Rozszerz datę wygaśnięcia klucza przy [użyciu](/cli/azure/keyvault/key#az-keyvault-key-set-attributes) interfejsu wiersza polecenia, a następnie ponownie przeszukaj szyfrowanie danych, aby udostępnić *serwer*.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Przypadkowe odwołanie dostępu do klucza z Key Vault

Może się zdarzyć, że osoba z wystarczającymi uprawnieniami dostępu Key Vault przypadkowo wyłączy dostęp serwera do klucza przez:

* Odwoływanie uprawnień get, wrapKey i unwrapKey magazynu kluczy z serwera.
* Usuwanie klucza.
* Usuwanie magazynu kluczy.
* Zmiana reguł zapory magazynu kluczy.
* Usuwanie tożsamości zarządzanej serwera w usłudze Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitorowanie klucza zarządzanego przez klienta w Key Vault

Aby monitorować stan bazy danych i włączyć alerty dotyczące utraty dostępu funkcji ochrony funkcji Transparent Data Encryption, skonfiguruj następujące funkcje platformy Azure:

* [Azure Resource Health:](../service-health/resource-health-overview.md)Niedostępna baza danych, która utraciła dostęp do klucza klienta, jest wyświetlana jako "Niedostępna" po odmowie pierwszego połączenia z bazą danych.
* [Dziennik aktywności:](../service-health/alerts-activity-log-service-notifications-portal.md)w przypadku niepowodzenia dostępu do klucza klienta w zarządzanym przez klienta Key Vault wpisy są dodawane do dziennika aktywności. Możesz przywrócić dostęp tak szybko, jak to możliwe, jeśli tworzysz alerty dla tych zdarzeń.

* [Grupy akcji:](../azure-monitor/alerts/action-groups.md)zdefiniuj te grupy w celu wysyłania powiadomień i alertów na podstawie Twoich preferencji.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Przywracanie i replikowanie przy użyciu klucza zarządzanego klienta w Key Vault

Po Azure Database for MySQL kluczem zarządzanym klienta przechowywanym w Key Vault, każda nowo utworzona kopia serwera jest również szyfrowana. Możesz utworzyć nową kopię za pomocą operacji przywracania lokalnego lub geograficznego albo replik do odczytu. Kopię można jednak zmienić w celu odzwierciedlenia klucza zarządzanego nowego klienta na potrzeby szyfrowania. Po zmianie klucza zarządzanego przez klienta stare kopie zapasowe serwera zaczynają używać najnowszego klucza.

Aby uniknąć problemów podczas konfigurowania szyfrowania danych zarządzanego przez klienta podczas przywracania lub tworzenia repliki do odczytu, należy wykonać następujące kroki na serwerze źródłowym i przywróconym/repliki:

* Zainicjuj proces przywracania lub odczytu repliki ze źródła Azure Database for MySQL.
* Zachowaj nowo utworzony serwer (przywrócony/replikowy) w stanie niedostępnym, ponieważ jego unikatowa tożsamość nie ma jeszcze uprawnień do Key Vault.
* Na przywróconym serwerze/serwerze repliki ponownie sprawdź klucz zarządzany przez klienta w ustawieniach szyfrowania danych, aby upewnić się, że nowo utworzony serwer ma nadany uprawnienia do opakowania i odpakowania klucza przechowywanego w Key Vault.

## <a name="limitations"></a>Ograniczenia

Na Azure Database for MySQL obsługa szyfrowania danych w spoczynku przy użyciu klucza zarządzanego (CMK) klientów ma kilka ograniczeń :

* Obsługa tej funkcji jest ograniczona **do Ogólnego przeznaczenia** i zoptymalizowane **pod kątem** pamięci.
* Ta funkcja jest obsługiwana tylko w regionach i na serwerach, które obsługują magazyn o pojemności do 16 TB. Aby uzyskać listę regionów świadczenia usługi Azure, w których jest dostępny magazyn o pojemności do 16 TB, zapoznaj się z sekcją dotyczącą magazynu w dokumentacji [tutaj](concepts-pricing-tiers.md#storage)

    > [!NOTE]
    > - Wszystkie nowe serwery MySQL utworzone w regionach wymienionych powyżej, obsługa szyfrowania przy użyciu kluczy menedżera klienta jest **dostępna.** Serwer przywracania do punktu w czasie lub replika do odczytu nie będą kwalifikować się, chociaż teoretycznie są one "nowe".
    > - Aby sprawdzić, czy aprowizowany serwer obsługuje rozmiar do 16 TB, możesz przejść do bloku warstwy cenowej w portalu i wyświetlić maksymalny rozmiar magazynu obsługiwany przez aprowizowany serwer. Jeśli możesz przesunąć suwak do 4 TB, serwer może nie obsługiwać szyfrowania za pomocą kluczy zarządzanych przez klienta. Jednak dane są przez cały czas szyfrowane przy użyciu kluczy zarządzanych przez usługę. Jeśli masz jakieś pytania, skontaktuj się AskAzureDBforMySQL@service.microsoft.com z toem.

* Szyfrowanie jest obsługiwane tylko przy użyciu klucza kryptograficznego RSA 2048.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować szyfrowanie danych za pomocą klucza zarządzanego przez klienta dla usługi Azure Database for MySQL przy użyciu interfejsu [wiersza Azure Portal](howto-data-encryption-portal.md) i interfejsu wiersza polecenia [platformy Azure.](howto-data-encryption-cli.md)
