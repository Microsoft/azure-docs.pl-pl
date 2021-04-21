---
title: Transparent Data Encryption (TDE) zarządzane przez klienta
description: Bring Your Own Key (BYOK) dla Transparent Data Encryption (TDE) z Azure Key Vault dla SQL Database i Azure Synapse Analytics. Omówienie funkcji TDE z byOK, korzyści, sposób jej działania, zagadnienia i zalecenia.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 02/01/2021
ms.openlocfilehash: b812a3feaa900914ef5f16f2f72270d9b6008371
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753025"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Funkcja Transparent Data Encryption usługi Azure SQL przy użyciu klucza zarządzanego przez klienta
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Transparent Data Encryption [(TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) z kluczem zarządzanym przez klienta umożliwia scenariusz rozwiązania Bring Your Own Key (BYOK) w zakresie ochrony danych magazynowany i umożliwia organizacjom implementowanie podziału obowiązków w zakresie zarządzania kluczami i danymi. W przypadku przezroczystego szyfrowania danych zarządzanego przez klienta klient jest odpowiedzialny za zarządzanie cyklem życia klucza (tworzenie, przekazywanie, rotację, usuwanie), uprawnienia do użycia klucza i inspekcję operacji na kluczach oraz w pełni nimi steruje.

W tym scenariuszu klucz używany do szyfrowania klucza szyfrowania bazy danych (DEK, Database Encryption Key), nazywanego funkcją ochrony TDE, jest zarządzanym przez klienta kluczem asymetrycznym przechowywanym w zewnętrznym systemie zarządzania kluczami zarządzanymi przez klienta [Azure Key Vault (AKV,](../../key-vault/general/security-overview.md)cloud-based external key management). Key Vault jest wysoce dostępnym i skalowalnym bezpiecznym magazynem kluczy kryptograficznych RSA, opcjonalnie zabezpieczony przez sprzętowe moduły zabezpieczeń (HSM) zweryfikowane w wersji FIPS 140-2 poziom 2. Nie zezwala na bezpośredni dostęp do przechowywanego klucza, ale zapewnia usługi szyfrowania/odszyfrowywania przy użyciu klucza dla autoryzowanych jednostek. Klucz może być generowany przez magazyn kluczy, importowany lub przesyłany do magazynu kluczy z urządzenia [HSM.](../../key-vault/keys/hsm-protected-keys.md)

W Azure SQL Database i Azure Synapse Analytics funkcji ochrony TDE jest ustawiana na poziomie serwera i jest dziedziczona przez wszystkie zaszyfrowane bazy danych skojarzone z tym serwerem. W Azure SQL Managed Instance funkcji ochrony TDE jest ustawiana na poziomie wystąpienia i jest dziedziczona przez wszystkie zaszyfrowane bazy danych w tym wystąpieniu. Termin *serwer odnosi* się zarówno do serwera w usługach SQL Database i Azure Synapse, jak i do wystąpienia zarządzanego w SQL Managed Instance tym dokumencie, chyba że określono inaczej.

> [!IMPORTANT]
> W przypadku osób korzystających z funkcji TDE zarządzanych przez usługę, które chcą rozpocząć korzystanie z funkcji TDE zarządzanej przez klienta, dane pozostają zaszyfrowane podczas przełączania i nie ma żadnych przestojów ani ponownego szyfrowania plików bazy danych. Przełączenie z klucza zarządzanego przez usługę na klucz zarządzany przez klienta wymaga tylko ponownego szyfrowania klucza szyfrowania danych, co jest operacją szybką i online.

> [!NOTE]
> <a id="doubleencryption"></a> Aby zapewnić Azure SQL klientom dwie warstwy szyfrowania danych w spoczynku, jest wycofywowane szyfrowanie infrastruktury (przy użyciu algorytmu szyfrowania AES-256) z kluczami zarządzanymi przez platformę. Zapewnia to dodatkową warstwę szyfrowania danych spoczynku oraz szyfrowanie TDE z kluczami zarządzanymi przez klienta, które są już dostępne. W Azure SQL Database i wystąpienia zarządzanego wszystkie bazy danych, w tym baza danych master i inne systemowe bazy danych, będą szyfrowane po włączeniu szyfrowania infrastruktury. Obecnie klienci muszą zażądać dostępu do tej funkcji. Jeśli interesuje Cię ta funkcja, skontaktuj się AzureSQLDoubleEncryptionAtRest@service.microsoft.com z .

## <a name="benefits-of-the-customer-managed-tde"></a>Zalety zarządzanego przez klienta środowiska TDE

Zarządzanie TDE przez klienta zapewnia następujące korzyści dla klienta:

- Pełna i szczegółowa kontrola użycia funkcji ochrony TDE i zarządzania nimi;

- Przezroczystość użycia funkcji ochrony TDE;

- Możliwość wdrożenia podziału obowiązków w zakresie zarządzania kluczami i danymi w organizacji;

- Key Vault może odwołać uprawnienia dostępu do klucza, aby uniemożliwić dostęp do zaszyfrowanej bazy danych;

- Centralne zarządzanie kluczami w układzie AKV;

- Większe zaufanie klientów końcowych, ponieważ usługa AKV została zaprojektowana w taki sposób, aby firma Microsoft nie widziała ani nie wyodrębniała kluczy szyfrowania;

## <a name="how-customer-managed-tde-works"></a>Jak działa zarządzanie TDE przez klienta

![Konfigurowanie i działanie zarządzanego przez klienta programu TDE](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

Aby serwer mógł używać funkcji ochrony TDE przechowywanej w usłudze AKV do szyfrowania klucza szyfrowania klucza, administrator magazynu kluczy musi nadać następujące prawa dostępu do serwera przy użyciu jego unikatowej tożsamości usługi Azure Active Directory (Azure AD):

- **get** — do pobierania części publicznej i właściwości klucza w Key Vault

- **wrapKey** — aby móc chronić (szyfrować) klucz szyfrowania danych

- **unwrapKey** — aby można było wyłączyć ochronę (odszyfrowywanie) klucza szyfrowania danych

Administrator magazynu kluczy może również [włączyć rejestrowanie zdarzeń](../../azure-monitor/insights/key-vault-insights-overview.md)inspekcji magazynu kluczy, aby można było je później poddać inspekcji.

Gdy serwer jest skonfigurowany do używania funkcji ochrony TDE z usługi AKV, wysyła klucz szyfrowania każdej bazy danych z włączoną funkcją TDE do magazynu kluczy w celu szyfrowania. Magazyn kluczy zwraca zaszyfrowany klucz szyfrowania danych, który jest następnie przechowywany w bazie danych użytkownika.

W razie potrzeby serwer wysyła chroniony klucz szyfrowania danych do magazynu kluczy w celu odszyfrowania.

Audytorzy mogą używać Azure Monitor do przeglądania dzienników AuditEvent magazynu kluczy, jeśli rejestrowanie jest włączone.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Wymagania dotyczące konfigurowania usługi TDE zarządzanej przez klienta

### <a name="requirements-for-configuring-akv"></a>Wymagania dotyczące konfigurowania aplikacji AKV

- Magazyn kluczy i SQL Database/wystąpienie zarządzane muszą należeć do tej samej Azure Active Directory dzierżawy. Interakcje między dzierżawami magazynu kluczy i serwera nie są obsługiwane. Aby później przenieść zasoby, należy ponownie skonfigurować program TDE z akv. Dowiedz się więcej o [przenoszeniu zasobów.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

- [Funkcja usuwania nie soft-delete](../../key-vault/general/soft-delete-overview.md) musi być włączona w magazynie kluczy, aby chronić przed przypadkowym usunięciem klucza (lub magazynu kluczy). Zasoby usunięte nie softnie są przechowywane przez 90 dni, chyba że zostaną odzyskane lub usunięte przez klienta w międzyczasie. Akcje *odzyskiwania* *i przeczyszczania* mają własne uprawnienia skojarzone z zasadami dostępu magazynu kluczy. Funkcja usuwania nie soft-delete jest domyślnie wyłączona i można ją włączyć za pomocą [programu PowerShell](../../key-vault/general/key-vault-recovery.md?tabs=azure-powershell) lub [interfejsu wiersza polecenia](../../key-vault/general/key-vault-recovery.md?tabs=azure-cli). Nie można go włączyć za pośrednictwem Azure Portal.  

- Przyznaj serwerowi lub wystąpieniu zarządzanemu dostęp do magazynu kluczy (get, wrapKey, unwrapKey) przy użyciu Azure Active Directory klucza. Podczas korzystania z Azure Portal tożsamość usługi Azure AD jest tworzona automatycznie. W przypadku korzystania z programu PowerShell lub interfejsu wiersza polecenia tożsamość usługi Azure AD musi zostać jawnie utworzona, a ukończenie powinno zostać zweryfikowane. Aby uzyskać szczegółowe instrukcje krok po kroku dotyczące korzystania z programu PowerShell, zobacz Configure [TDE with BYOK](transparent-data-encryption-byok-configure.md) (Konfigurowanie funkcji TDE przy użyciu funkcji [BY SQL Managed Instance OK) i Configure TDE with BYOK](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md) (Konfigurowanie funkcji TDE przy użyciu funkcji BYOK), aby uzyskać szczegółowe instrukcje krok po kroku.

- W przypadku korzystania z zapory z usługą AKV należy włączyć opcję Zezwalaj na *usługi firmy Microsoft, aby obejść zaporę.*

### <a name="requirements-for-configuring-tde-protector"></a>Wymagania dotyczące konfigurowania funkcji ochrony TDE

- Funkcji ochrony TDE może być tylko asymetryczne, RSA lub RSA HSM klucza. Obsługiwane długości kluczy to 2048 bajtów i 3072 bajty.

- Data aktywacji klucza (jeśli jest ustawiona) musi być datą i godziną w przeszłości. Data wygaśnięcia (jeśli jest ustawiona) musi być przyszłą datą i godziną.

- Klucz musi mieć stan *Włączone.*

- Jeśli importujesz istniejący klucz do magazynu kluczy, upewnij się, że jest on dostępny w obsługiwanych formatach plików ( `.pfx` `.byok` , lub `.backup` ).

> [!NOTE]
> Azure SQL obsługuje teraz używanie klucza RSA przechowywanego w zarządzanym hsm jako funkcji ochrony TDE. Ta funkcja jest dostępna w publicznej **wersji zapoznawczej**. Azure Key Vault HSM to w pełni zarządzana, wysoce dostępna, zgodna ze standardami usługa w chmurze z jedną dzierżawą, która umożliwia ochronę kluczy kryptograficznych dla aplikacji w chmurze przy użyciu modułów HSM zweryfikowanych w trybie FIPS 140-2 poziom 3. Dowiedz się więcej o [zarządzanych modułach HSM.](../../key-vault/managed-hsm/index.yml)


## <a name="recommendations-when-configuring-customer-managed-tde"></a>Zalecenia dotyczące konfigurowania usługi TDE zarządzanej przez klienta

### <a name="recommendations-when-configuring-akv"></a>Zalecenia podczas konfigurowania aplikacji AKV

- Skojarz łącznie co najwyżej 500 Ogólnego przeznaczenia lub 200 baz danych Krytyczne dla działania firmy z magazynem kluczy w ramach jednej subskrypcji, aby zapewnić wysoką dostępność, gdy serwer uzyskuje dostęp do funkcji ochrony TDE w magazynie kluczy. Te wartości są oparte na doświadczeniu i udokumentowane w [limitach usługi Key Vault.](../../key-vault/general/service-limits.md) W tym przypadku celem jest zapobieganie problemom po zakończeniu pracy serwera w trybu failover, ponieważ spowoduje to wyzwolenie tylu kluczowych operacji względem magazynu, ile jest baz danych na tym serwerze.

- Ustaw blokadę zasobu w magazynie kluczy, aby kontrolować, kto może usuwać ten zasób krytyczny i zapobiegać przypadkowemu lub nieautoryzowanemu usunięciu. Dowiedz się więcej o [blokadach zasobów.](../../azure-resource-manager/management/lock-resources.md)

- Włącz inspekcję i raportowanie dla wszystkich kluczy szyfrowania: magazyn kluczy udostępnia dzienniki, które można łatwo wstrzyknąć do innych narzędzi do zarządzania informacjami i zdarzeniami zabezpieczeń. Operations Management Suite [Log Analytics](../../azure-monitor/insights/key-vault-insights-overview.md) to jeden z przykładów usługi, która jest już zintegrowana.

- Połącz każdy serwer z dwoma magazynami kluczy, które znajdują się w różnych regionach i zawierają ten sam materiał klucza, aby zapewnić wysoką dostępność zaszyfrowanych baz danych. Oznacz tylko klucz z magazynu kluczy w tym samym regionie jako element ochrony TDE. System automatycznie przełączy się do magazynu kluczy w regionie zdalnym, jeśli na magazyn kluczy w tym samym regionie ma wpływ błąd.

### <a name="recommendations-when-configuring-tde-protector"></a>Zalecenia dotyczące konfigurowania funkcji ochrony TDE

- Zachowaj kopię funkcji ochrony TDE w bezpiecznym miejscu lub umieść ją w usłudze escrow.

- Jeśli klucz jest generowany w magazynie kluczy, utwórz kopię zapasową klucza przed pierwszym użyciem klucza w u usługach AKV. Kopię zapasową można przywrócić tylko Azure Key Vault kopii zapasowej. Dowiedz się więcej o [poleceniu Backup-AzKeyVaultKey.](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Utwórz nową kopię zapasową za każdym razem, gdy w kluczu zostaną wprowadzone jakiekolwiek zmiany (na przykład atrybuty klucza, tagi, ACL).

- **Zachowaj poprzednie wersje** klucza w magazynie kluczy podczas rotacji kluczy, aby można było przywrócić starsze kopie zapasowe bazy danych. Po zmianie ochrony TDE dla bazy danych stare  kopie zapasowe bazy danych nie są aktualizowane w celu używania najnowszej funkcji ochrony TDE. W czasie przywracania każda kopia zapasowa wymaga ochrony TDE, za pomocą których została zaszyfrowana w czasie tworzenia. Rotacje kluczy można wykonać zgodnie z instrukcjami podanymi w te [tematu Rotate the Transparent Data Encryption Protector Using PowerShell (Obracanie Transparent Data Encryption ochrony przy użyciu programu PowerShell).](transparent-data-encryption-byok-key-rotation.md)

- Zachowaj wszystkie wcześniej używane klucze w usłudze AKV nawet po przełączeniu na klucze zarządzane przez usługę. Gwarantuje to, że kopie zapasowe bazy danych można przywrócić za pomocą funkcji ochrony TDE przechowywanych w u usługi AKV.  Ochrony TDE utworzone za pomocą Azure Key Vault muszą być przechowywane, dopóki nie zostaną utworzone wszystkie pozostałe przechowywane kopie zapasowe za pomocą kluczy zarządzanych przez usługę. Tworzenie kopii zapasowych tych kluczy do odzyskania przy użyciu [narzędzia Backup-AzKeyVaultKey.](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Aby usunąć potencjalnie naruszony klucz podczas zdarzenia związanego z bezpieczeństwem bez ryzyka utraty danych, wykonaj kroki opisane w teście Usuwanie klucza, który może mieć [złamane zabezpieczenia.](transparent-data-encryption-byok-remove-tde-protector.md)

## <a name="inaccessible-tde-protector"></a>Niedostępna ochrony TDE

Gdy przezroczyste szyfrowanie danych jest skonfigurowane do używania klucza zarządzanego przez klienta, wymagany jest ciągły dostęp do funkcji ochrony TDE, aby baza danych stała się w trybie online. Jeśli serwer utraci dostęp do funkcji ochrony TDE zarządzanej przez klienta w użytą usługę AKV, w ciągu maksymalnie 10 minut baza danych zacznie odmawiać wszystkich połączeń za pomocą odpowiedniego komunikatu o błędzie i zmieni swój stan na *Niedostępny.* Jedyną akcją dozwoloną dla bazy danych w stanie Niedostępny jest jej usunięcie.

> [!NOTE]
> Jeśli baza danych jest niedostępna ze względu na sporadyczne przerwy w działaniu sieci, nie jest wymagana żadna akcja, a bazy danych będą automatycznie wracać do trybu online.

Po przywróceniu dostępu do klucza przywrócenie bazy danych w tryb online wymaga dodatkowego czasu i czynności, które mogą się różnić w zależności od czasu, który upłynął bez dostępu do klucza i rozmiaru danych w bazie danych:

- Jeśli dostęp do klucza zostanie przywrócony w ciągu 8 godzin, baza danych zostanie automatycznie zasyłana w ciągu następnej godziny.

- Jeśli dostęp do klucza zostanie przywrócony po upływie więcej niż 8 godzin, automatyczne zasłanianie nie jest możliwe, a przywrócenie bazy danych wymaga dodatkowych kroków w portalu i może zająć znaczną ilość czasu w zależności od rozmiaru bazy danych. Po powrocie bazy danych do trybu online wcześniej skonfigurowane ustawienia na poziomie serwera, takie jak konfiguracja grupy trybu [failover,](auto-failover-group-overview.md) historia przywracania do punktu w czasie i tagi, **zostaną utracone.** W związku z tym zaleca się zaimplementowanie systemu powiadomień, który pozwala zidentyfikować i rozwiązać podstawowe problemy z dostępem do klucza w ciągu 8 godzin.

Poniżej znajduje się widok dodatkowych kroków wymaganych w portalu do powrotu niedostępnej bazy danych do trybu online.

![Niedostępna baza danych funkcji BYOK dla funkcji TDE](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>Przypadkowe odwołanie dostępu funkcji ochrony TDE

Może się zdarzyć, że ktoś z wystarczającymi uprawnieniami dostępu do magazynu kluczy przypadkowo wyłączy dostęp serwera do klucza przez:

- odwołanie uprawnień *get,* *wrapKey* i *unwrapKey* magazynu kluczy z serwera

- usuwanie klucza

- usuwanie magazynu kluczy

- zmienianie reguł zapory magazynu kluczy

- usuwanie tożsamości zarządzanej serwera w programie Azure Active Directory

Dowiedz się więcej [o typowych przyczynach](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current&preserve-view=true#common-errors-causing-databases-to-become-inaccessible)niedostępności bazy danych.

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitorowanie zarządzanego przez klienta środowiska TDE

Aby monitorować stan bazy danych i włączyć alerty dotyczące utraty dostępu funkcji ochrony TDE, skonfiguruj następujące funkcje platformy Azure:

- [Azure Resource Health](../../service-health/resource-health-overview.md). Niedostępna baza danych, która utraciła dostęp do funkcji ochrony TDE, będzie pokazywana jako "Niedostępna" po odmowie pierwszego połączenia z bazą danych.
- [Dziennik aktywności,](../../service-health/alerts-activity-log-service-notifications-portal.md) gdy dostęp do funkcji ochrony TDE w zarządzanym przez klienta magazynie kluczy kończy się niepowodzeniem, wpisy są dodawane do dziennika aktywności.  Utworzenie alertów dla tych zdarzeń umożliwi jak najszybciej przywrócenie dostępu.
- Grupy [akcji](../../azure-monitor/alerts/action-groups.md) można zdefiniować w celu wysyłania powiadomień i alertów na podstawie twoich preferencji, na przykład poczty e-mail/wiadomości SMS/wypychania/głosu, aplikacji logiki, urządzenia webhook, itsm lub automatyzacji runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Tworzenie i przywracanie kopii zapasowej bazy danych za pomocą zarządzanego przez klienta programu TDE

Gdy baza danych jest szyfrowana przy użyciu funkcji TDE przy użyciu klucza z usługi Key Vault, wszystkie nowo wygenerowane kopie zapasowe są również szyfrowane przy użyciu tej samej funkcji ochrony TDE. Po zmianie funkcji ochrony TDE stare kopie zapasowe bazy danych nie są aktualizowane w celu **używania** najnowszej funkcji ochrony TDE.

Aby przywrócić kopię zapasową zaszyfrowaną za pomocą funkcji ochrony TDE z Key Vault, upewnij się, że materiał klucza jest dostępny dla serwera docelowego. Dlatego zalecamy zachowywanie wszystkich starych wersji funkcji ochrony TDE w magazynie kluczy, aby można było przywracać kopie zapasowe bazy danych.

> [!IMPORTANT]
> W dowolnym momencie może być nie więcej niż jeden zestaw funkcji ochrony TDE dla serwera. Jest to klucz oznaczony znakiem "Oznacz klucz jako domyślny element ochrony TDE" w bloku Azure Portal szyfrowania. Jednak wiele dodatkowych kluczy można połączyć z serwerem bez oznaczania ich jako ochrony TDE. Te klucze nie są używane do ochrony klucza szyfrowania danych, ale mogą być używane podczas przywracania z kopii zapasowej, jeśli plik kopii zapasowej jest zaszyfrowany za pomocą klucza z odpowiadającym mu odciskiem palca.

Jeśli klucz wymagany do przywrócenia kopii zapasowej nie jest już dostępny dla serwera docelowego, podczas przywracania zostanie zwrócony następujący komunikat o błędzie: "Serwer docelowy nie ma dostępu do wszystkich URI usługi AKV utworzonych między i `<Servername>` \<Timestamp #1> \<Timestamp #2> . Ponów operację po przywróceniu wszystkich URI akv".

Aby go uniknąć, uruchom polecenie cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) dla serwera docelowego lub [polecenie Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) dla docelowego wystąpienia zarządzanego, aby zwrócić listę dostępnych kluczy i zidentyfikować brakujące. Aby mieć pewność, że można przywrócić wszystkie kopie zapasowe, upewnij się, że serwer docelowy przywracania ma dostęp do wszystkich potrzebnych kluczy. Te klucze nie muszą być oznaczone jako ochrony TDE.

Aby dowiedzieć się więcej na temat odzyskiwania kopii zapasowej dla SQL Database, zobacz Recover a database in SQL Database (Odzyskiwanie bazy [danych w programie SQL Database](recovery-using-backups.md)). Aby dowiedzieć się więcej na temat odzyskiwania kopii zapasowej dla dedykowanej puli SQL Azure Synapse Analytics, zobacz Recover a dedicated SQL pool (Odzyskiwanie [dedykowanej puli SQL).](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md) Aby SQL Server tworzenia/przywracania kopii zapasowej bazy danych za pomocą SQL Managed Instance, zobacz [Szybki start:](../managed-instance/restore-sample-database-quickstart.md) przywracanie bazy danych do SQL Managed Instance

Dodatkowa uwagę w przypadku plików dziennika: Kopii zapasowej plików dziennika pozostają zaszyfrowane przy użyciu oryginalnej ochrony TDE, nawet jeśli została obrócona i baza danych jest teraz przy użyciu nowej funkcji ochrony TDE.  Podczas przywracania oba klucze będą potrzebne do przywrócenia bazy danych.  Jeśli plik dziennika używa funkcji ochrony TDE przechowywanej w usłudze Azure Key Vault, ten klucz będzie potrzebny podczas przywracania, nawet jeśli baza danych została zmieniona w celu używania funkcji TDE zarządzanej przez usługę w międzyczasie.

## <a name="high-availability-with-customer-managed-tde"></a>Wysoka dostępność przy użyciu funkcji TDE zarządzanej przez klienta

Nawet w przypadkach, gdy nie ma skonfigurowanej nadmiarowości geograficznej dla serwera, zdecydowanie zaleca się skonfigurowanie serwera do używania dwóch różnych magazynów kluczy w dwóch różnych regionach przy użyciu tego samego materiału klucza. Klucz w pomocniczym magazynie kluczy w innym regionie nie powinien być oznaczony jako ochrony TDE i nie jest nawet dozwolony. Jeśli występuje błąd wpływający na podstawowy magazyn kluczy, a tylko wtedy system automatycznie przełączy się na inny połączony klucz z tym samym odciskiem palca w pomocniczym magazynie kluczy, jeśli istnieje. Należy jednak pamiętać, że przełączenie nie nastąpi, jeśli funkcji ochrony TDE nie będzie można uzyskać z powodu odwołanych praw dostępu lub usunięcia klucza lub magazynu kluczy, ponieważ może to oznaczać, że klient celowo chciał ograniczyć serwerowi dostęp do klucza. Aby zapewnić ten sam materiał klucza do dwóch magazynów kluczy w różnych regionach, można utworzyć klucz poza magazynem kluczy i zaimportować go do obu magazynów kluczy. 

Alternatywnie można to zrobić, generując klucz przy użyciu magazynu kluczy podstawowych kolokowanych w tym samym regionie co serwer i klonując klucz do magazynu kluczy w innym regionie świadczenia usługi Azure. Użyj polecenia cmdlet [Backup-AzKeyVaultKey,](/powershell/module/az.keyvault/Backup-AzKeyVaultKey) aby pobrać klucz w formacie zaszyfrowanym z magazynu kluczy podstawowych, a następnie użyj polecenia cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) i określ magazyn kluczy w drugim regionie, aby sklonować klucz. Alternatywnie możesz użyć Azure Portal kopii zapasowej i przywracania klucza. Operacja tworzenia/przywracania kopii zapasowej klucza jest dozwolona tylko między magazynami kluczy w ramach tej samej subskrypcji platformy Azure i [obszarem geograficznym platformy Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)  

![Single-Server ha](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geograficzne dr i zarządzane przez klienta TDE

W [scenariuszach z aktywną replikacją](active-geo-replication-overview.md) geograficzną i grupami trybu [failover](auto-failover-group-overview.md) każdy serwer wymaga oddzielnego magazynu kluczy, który musi być kolokowany z serwerem w tym samym regionie świadczenia usługi Azure. Klient jest odpowiedzialny za zachowanie spójności materiału klucza w magazynach kluczy, dzięki czemu pomocniczy obszar geograficzny jest zsynchronizowany i może przejąć użycie tego samego klucza z lokalnego magazynu kluczy, jeśli podstawowy stanie się niedostępny z powodu awarii w regionie i wyzwolenia trybu failover. Można skonfigurować maksymalnie cztery usługi sekundnikowe, a łańcuch (osoby drugiego serwera) nie jest obsługiwany.

Aby uniknąć problemów podczas ustanawiania lub podczas replikacji geograficznej z powodu niekompletnego materiału klucza, należy przestrzegać tych reguł podczas konfigurowania funkcji TDE zarządzanej przez klienta:

- Wszystkie magazyny kluczy, których dotyczy problem, muszą mieć takie same właściwości i takie same prawa dostępu dla odpowiednich serwerów.

- Wszystkie magazyny kluczy, których to dotyczy, muszą zawierać identyczny materiał klucza. Dotyczy to nie tylko bieżącego funkcji ochrony TDE, ale także wszystkich poprzednich funkcji ochrony TDE, które mogą być używane w plikach kopii zapasowej.

- Zarówno początkową konfigurację, jak i rotację ochrony TDE należy najpierw wykonać na pomocniczym, a następnie w podstawowym.

![Grupy trybu failover i geo-dr](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

Aby przetestować tryb failover, wykonaj kroki opisane w przeglądzie [aktywnej replikacji geograficznej.](active-geo-replication-overview.md) Testowanie trybu failover należy przeprowadzać regularnie, aby sprawdzić, SQL Database ma uprawnienia dostępu do obu magazynów kluczy.

## <a name="next-steps"></a>Następne kroki

Możesz również sprawdzić następujące przykładowe skrypty programu PowerShell dla typowych operacji wykonywanych przy użyciu środowiska TDE zarządzanego przez klienta:

- [Obracanie Transparent Data Encryption ochrony SQL Database pomocą programu PowerShell](transparent-data-encryption-byok-key-rotation.md)

- [Usuwanie ochrony Transparent Data Encryption (TDE) dla SQL Database przy użyciu programu PowerShell](transparent-data-encryption-byok-remove-tde-protector.md)

- [Zarządzanie Transparent Data Encryption w SQL Managed Instance przy użyciu własnego klucza przy użyciu programu PowerShell](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)