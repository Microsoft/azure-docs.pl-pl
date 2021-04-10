---
title: 'Azure AD Connect: konta i uprawnienia | Microsoft Docs'
description: W tym temacie opisano używane i utworzone konta oraz wymagane uprawnienia.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f7466931537745fb188a3bdb05646bff19912e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466256"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Konta i uprawnienia

## <a name="accounts-used-for-azure-ad-connect"></a>Konta używane do Azure AD Connect

![Przegląd kont](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect używa 3 kont w celu synchronizowania informacji z Active Directory lokalnych lub systemu Windows Server do Azure Active Directory.  Są to następujące konta:

- **Konto łącznika AD DS**: służy do odczytu i zapisu informacji w systemie Windows Server Active Directory

- **Konto usługi ADSync**: używane do uruchamiania usługi synchronizacji i uzyskiwania dostępu do bazy danych SQL

- **Konto łącznika usługi Azure AD**: używane do zapisywania informacji w usłudze Azure AD

Oprócz tych trzech kont używanych do uruchamiania Azure AD Connect należy również zainstalować Azure AD Connect następujące dodatkowe konta.  Są to:

- **Konto administratora lokalnego**: Administrator instalujący Azure AD Connect i mający uprawnienia administratora lokalnego na komputerze.

- **AD DS konto administratora przedsiębiorstwa**: Opcjonalnie można utworzyć "konto łącznika AD DS" powyżej.

- **Konto administratora globalnego usługi Azure AD**: służy do tworzenia konta łącznika usługi Azure AD i konfigurowania usługi Azure AD.  Konta administratora globalnego można wyświetlić w Azure Portal.  Zobacz [Lista przypisań ról usługi Azure AD](../../active-directory/roles/view-assignments.md).

- **Konto SQL SA (opcjonalnie)**: służy do tworzenia bazy danych ADSync w przypadku korzystania z pełnej wersji SQL Server.  Ta SQL Server może być lokalna lub zdalna dla Azure AD Connect instalacji.  To konto może być tym samym kontem co administrator przedsiębiorstwa.  Inicjowanie obsługi bazy danych może być teraz wykonywane poza pasmem przez administratora SQL, a następnie instalowane przez administratora Azure AD Connect z prawami właściciela bazy danych.  Aby uzyskać więcej informacji na ten temat, zobacz [instalowanie Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> Od kompilacji 1.4. # # #. # nie jest już obsługiwane używanie administratora przedsiębiorstwa lub konta administratora domeny jako konta łącznika AD DS.  Jeśli podczas określania **użycia istniejącego konta** zostanie podjęta próba wprowadzenia konta administratora przedsiębiorstwa lub administratora domeny, zostanie wyświetlony komunikat o błędzie.

> [!NOTE]
> Jest on obsługiwany do zarządzania kontami administracyjnymi używanymi w Azure AD Connect z lasu administracyjnego ESAE (znany również jako "Red Forest").
> Dedykowane lasy administracyjne pozwalają organizacjom hostować konta administratora, stacje robocze i grupy w środowisku zapewniającym silniejsze opcje zabezpieczeń niż te używane w środowisku produkcyjnym.
> Aby dowiedzieć się więcej na temat dedykowanych lasów administracyjnych, zapoznaj się z [podejściem do projektowania lasów administracyjnych ESAE](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> Rola administratora globalnego nie jest wymagana po początkowej konfiguracji i jedynym wymaganym koncie będzie konto roli **konta synchronizacji katalogów** . Nie musi to oznaczać, że chcesz po prostu usunąć konto z rolą administratora globalnego. Lepiej jest zmienić rolę na mniej wydajną rolę, ponieważ całkowite usunięcie konta może spowodować problemy, jeśli kiedykolwiek trzeba ponownie uruchomić kreatora. Zmniejszając uprawnienie roli, można zawsze ponownie podwyższyć poziom uprawnień, jeśli trzeba będzie ponownie użyć Kreatora Azure AD Connect. 

## <a name="installing-azure-ad-connect"></a>Instalowanie Azure AD Connect
Kreator instalacji Azure AD Connect oferuje dwie różne ścieżki:

* W ustawieniach ekspresowych Kreator wymaga większej liczby uprawnień.  Jest to tak, aby można było łatwo skonfigurować konfigurację, bez konieczności tworzenia użytkowników ani konfigurowania uprawnień.
* W ustawieniach niestandardowych Kreator oferuje więcej opcji i opcji. Istnieją jednak pewne sytuacje, w których należy upewnić się, że masz odpowiednie uprawnienia.



## <a name="express-settings-installation"></a>Instalacja ustawień ekspresowych
W ustawieniach ekspresowych Kreator instalacji monituje o następujące czynności:

  - AD DS poświadczenia administratora przedsiębiorstwa
  - Poświadczenia administratora globalnego usługi Azure AD

### <a name="ad-ds-enterprise-admin-credentials"></a>AD DS poświadczenia administratora przedsiębiorstwa
Konto administratora AD DS Enterprise służy do konfigurowania Active Directory lokalnych. Te poświadczenia są używane tylko podczas instalacji i nie są używane po zakończeniu instalacji. Administrator przedsiębiorstwa, a nie administrator domeny, musi upewnić się, że uprawnienia w Active Directory mogą być ustawione we wszystkich domenach.

W przypadku uaktualniania z narzędzia DirSync poświadczenia administratorów przedsiębiorstwa AD DS są używane do resetowania hasła dla konta używanego przez narzędzie DirSync. Potrzebne są również poświadczenia administratora globalnego usługi Azure AD.

### <a name="azure-ad-global-admin-credentials"></a>Poświadczenia administratora globalnego usługi Azure AD
Te poświadczenia są używane tylko podczas instalacji i nie są używane po zakończeniu instalacji. Służy do tworzenia konta łącznika usługi Azure AD używanego do synchronizowania zmian w usłudze Azure AD. Konto umożliwia również synchronizację jako funkcję w usłudze Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Konto łącznika AD DS wymagane uprawnienia dla ustawień ekspresowych
Konto łącznika AD DS jest tworzone do odczytu i zapisu w systemie Windows Server AD i ma następujące uprawnienia, gdy są tworzone przez ustawienia ekspresowe:

| Uprawnienie | Sposób użycia |
| --- | --- |
| <li>Replikowanie zmian w katalogu</li><li>Replikuj wszystkie zmiany katalogu |Synchronizacja skrótów haseł |
| Użytkownik odczyt/zapis wszystkich właściwości |Importowanie i wymiana hybrydowa |
| Odczytaj/Zapisz wszystkie właściwości iNetOrgPerson |Importowanie i wymiana hybrydowa |
| Odczytaj/Zapisz grupę właściwości |Importowanie i wymiana hybrydowa |
| Odczytaj/Zapisz wszystkie właściwości kontaktu |Importowanie i wymiana hybrydowa |
| Resetowanie hasła |Przygotowanie do włączenia zapisywania zwrotnego haseł |

### <a name="express-installation-wizard-summary"></a>Podsumowanie kreatora instalacji ekspresowej

![Instalacja ekspresowa](./media/reference-connect-accounts-permissions/express.png)

Poniżej znajduje się podsumowanie stron kreatora instalacji ekspresowej, zebranych poświadczeń i ich użycia.

| Strona Kreatora | Zebrane poświadczenia | Wymagane uprawnienia | Używane dla |
| --- | --- | --- | --- |
| Nie dotyczy |Użytkownik uruchamiający Kreatora instalacji |Administrator serwera lokalnego |<li>Tworzy konto usługi ADSync, które jest używane jako do uruchamiania usługi synchronizacji. |
| Łączenie z usługą Azure AD |Poświadczenia katalogu usługi Azure AD |Rola administratora globalnego w usłudze Azure AD |<li>Włączanie synchronizacji w katalogu usługi Azure AD.</li>  <li>Tworzenie konta łącznika usługi Azure AD używanego do przeprowadzania operacji synchronizacji w usłudze Azure AD.</li> |
| Łączenie z usługami AD DS |Poświadczenia Active Directory lokalnego |Członek grupy Administratorzy przedsiębiorstwa (EA) w Active Directory |<li>Tworzy konto łącznika AD DS w Active Directory i udziela do niego uprawnień. To utworzone konto służy do odczytywania i zapisywania informacji o katalogu podczas synchronizacji.</li> |


## <a name="custom-installation-settings"></a>Ustawienia instalacji niestandardowej

W przypadku instalacji ustawień niestandardowych Kreator oferuje więcej opcji i opcji. 

### <a name="custom-installation-wizard-summary"></a>Podsumowanie kreatora instalacji niestandardowej

Poniżej znajduje się podsumowanie stron kreatora instalacji niestandardowej, zebranych poświadczeń i ich użycia.

![Zrzut ekranu pokazujący strony Kreatora instalacji niestandardowej.](./media/reference-connect-accounts-permissions/customize.png)

| Strona Kreatora | Zebrane poświadczenia | Wymagane uprawnienia | Używane dla |
| --- | --- | --- | --- |
| Nie dotyczy |Użytkownik uruchamiający Kreatora instalacji |<li>Administrator serwera lokalnego</li><li>W przypadku korzystania z pełnego SQL Server użytkownik musi być administratorem systemu (SA) w SQL</li> |Domyślnie program tworzy konto lokalne, które jest używane jako konto usługi aparatu synchronizacji. Konto jest tworzone tylko wtedy, gdy administrator nie określił określonego konta. |
| Instalowanie usług synchronizacji, opcja konta usługi |Poświadczenia konta użytkownika lokalnego lub usługi AD |Użytkownik, uprawnienia są udzielane przez Kreatora instalacji |Jeśli administrator określi konto, to konto jest używane jako konto usługi synchronizacji. |
| Łączenie z usługą Azure AD |Poświadczenia katalogu usługi Azure AD |Rola administratora globalnego w usłudze Azure AD |<li>Włączanie synchronizacji w katalogu usługi Azure AD.</li>  <li>Tworzenie konta łącznika usługi Azure AD używanego do przeprowadzania operacji synchronizacji w usłudze Azure AD.</li> |
| Podłączanie katalogów |Poświadczenia Active Directory lokalnych dla każdego lasu połączonego z usługą Azure AD |Uprawnienia są zależne od tego, jakie funkcje są włączane, i można je znaleźć w temacie Tworzenie konta łącznika AD DS |To konto służy do odczytywania i zapisywania informacji o katalogu podczas synchronizacji. |
| Serwery usług AD FS |W przypadku każdego serwera na liście Kreator zbiera poświadczenia, gdy poświadczenia logowania użytkownika uruchamianego przez kreatora są niewystarczające do nawiązania połączenia |Administrator domeny |Instalacja i konfiguracja roli serwera AD FS. |
| Serwery proxy aplikacji sieci Web |W przypadku każdego serwera na liście Kreator zbiera poświadczenia, gdy poświadczenia logowania użytkownika uruchamianego przez kreatora są niewystarczające do nawiązania połączenia |Administrator lokalny na maszynie docelowej |Instalacja i konfiguracja roli serwera WAP. |
| Poświadczenia zaufania serwera proxy |Poświadczenia zaufania usługi federacyjnej (poświadczenia, których używa serwer proxy do rejestracji certyfikatu zaufania z usług FS |Konto domeny będące administratorem lokalnym serwera AD FS |Początkowa Rejestracja certyfikatu zaufania usług FS — WAP |
| AD FS stronie konta usługi "Użyj opcji konta użytkownika domeny" |Poświadczenia konta użytkownika usługi AD |Użytkownik domeny |Konto użytkownika usługi Azure AD, którego poświadczenia są podane, jest używane jako konto logowania usługi AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Utwórz konto łącznika AD DS

>[!IMPORTANT]
>Został wprowadzony nowy moduł programu PowerShell o nazwie ADSyncConfig. PSM1 z kompilacją **1.1.880.0** (wydanej w sierpniu 2018) zawierającym kolekcję poleceń cmdlet, które ułatwiają skonfigurowanie prawidłowych uprawnień Active Directory dla konta łącznika usługi Azure AD DS.
>
>Aby uzyskać więcej informacji, zobacz [Azure AD Connect: Configure AD DS Connector Account uprawnienie](how-to-connect-configure-ad-ds-connector-account.md)

Konto określone na stronie **Połącz katalogi** musi znajdować się w Active Directory przed rozpoczęciem instalacji.  Azure AD Connect w wersji 1.1.524.0 lub nowszej ma opcję umożliwiającą kreatorowi Azure AD Connect utworzenie **konta łącznika AD DS** używanego do nawiązywania połączenia z Active Directory.  

Musi także mieć przyznane wymagane uprawnienia. Kreator instalacji nie weryfikuje uprawnień i wszelkie problemy są dostępne tylko podczas synchronizacji.

Wymagane uprawnienia są zależne od funkcji opcjonalnych, które można włączyć. Jeśli masz wiele domen, uprawnienia muszą zostać przyznane dla wszystkich domen w lesie. Jeśli nie włączysz żadnej z tych funkcji, uprawnienia **użytkownika domeny** domyślnej są wystarczające.

| Cecha | Uprawnienia |
| --- | --- |
| Funkcja MS-DS-ConsistencyGuid |Uprawnienia do zapisu w atrybucie MS-DS-ConsistencyGuid udokumentowanym w [koncepcji projektowania — przy użyciu MS-ds-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizacja skrótów haseł |<li>Replikowanie zmian w katalogu</li>  <li>Replikuj wszystkie zmiany katalogu |
| Wdrożenie hybrydowe programu Exchange |Uprawnienia do zapisu w odniesieniu do atrybutów przedstawionych w funkcji [zapisywania zwrotnego hybrydowego programu Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) dla użytkowników, grup i kontaktów. |
| Folder publiczny poczty programu Exchange |Uprawnienia Odczyt do atrybutów przedstawionych w [folderze publicznym poczty programu Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) dla folderów publicznych. | 
| Zapisywanie zwrotne haseł |Uprawnienia do zapisu w odniesieniu do atrybutów opisanych w temacie [wprowadzenie do zarządzania hasłami](../authentication/tutorial-enable-sspr-writeback.md) dla użytkowników. |
| Zapisywanie zwrotne urządzeń |Uprawnienia przyznane za pomocą skryptu programu PowerShell zgodnie z opisem w funkcji [zapisywania zwrotnego urządzeń](how-to-connect-device-writeback.md). |
| Zapisywanie zwrotne grup |Pozwala na zapisywanie zwrotne **grup Microsoft 365** w lesie z zainstalowanym programem Exchange.|

## <a name="upgrade"></a>Uaktualnienie
W przypadku uaktualniania z jednej wersji Azure AD Connect do nowej wersji wymagane są następujące uprawnienia:

>[!IMPORTANT]
>Począwszy od 1.1.484 kompilacji, Azure AD Connect wprowadzono błąd regresji, który wymaga uprawnień administratora systemu do uaktualnienia bazy danych SQL.  Ta usterka została poprawiona w kompilacji 1.1.647.  W przypadku uaktualniania do tej kompilacji wymagane są uprawnienia administratora systemu.  Uprawnienia dbo nie są wystarczające.  Jeśli podjęto próbę uaktualnienia Azure AD Connect bez uprawnień administratora systemu, uaktualnienie zakończy się niepowodzeniem, a Azure AD Connect nie będzie działać prawidłowo.  Firma Microsoft wie o tym i działa prawidłowo.


| Główne | Wymagane uprawnienia | Sposób użycia |
| --- | --- | --- |
| Użytkownik uruchamiający Kreatora instalacji |Administrator serwera lokalnego |Aktualizowanie plików binarnych. |
| Użytkownik uruchamiający Kreatora instalacji |Członek ADSyncAdmins |Wprowadź zmiany w regułach synchronizacji i innej konfiguracji. |
| Użytkownik uruchamiający Kreatora instalacji |W przypadku korzystania z pełnego programu SQL Server: DBO (lub podobna) bazy danych aparatu synchronizacji |Wprowadzanie zmian na poziomie bazy danych, takich jak aktualizowanie tabel przy użyciu nowych kolumn. |

## <a name="more-about-the-created-accounts"></a>Więcej informacji o utworzonych kontach
### <a name="ad-ds-connector-account"></a>Konto łącznika usług AD DS
W przypadku korzystania z ustawień ekspresowych konto jest tworzone w Active Directory używanym do synchronizacji. Utworzone konto znajduje się w domenie głównej lasu w kontenerze Użytkownicy i ma swoją nazwę prefiksową **MSOL_**. Konto jest tworzone przy użyciu długiego hasła złożonego, które nie wygasa. Jeśli masz zasady dotyczące haseł w domenie, upewnij się, że dla tego konta są dozwolone długie i złożone hasła.

![Konto usługi AD](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

W przypadku korzystania z ustawień niestandardowych użytkownik jest odpowiedzialny za utworzenie konta przed rozpoczęciem instalacji.  Zobacz Tworzenie konta łącznika AD DS.

### <a name="adsync-service-account"></a>Konto usługi ADSync
Usługa synchronizacji może działać na różnych kontach. Może działać w ramach **konta usługi wirtualnej** (VSA), **konta usługi zarządzanego przez grupę** (gMSA/autonomiczne) lub zwykłego konta użytkownika. Obsługiwane opcje zostały zmienione z 2017 kwietnia w przypadku nawiązywania nowej instalacji. W przypadku uaktualnienia z wcześniejszej wersji Azure AD Connect te dodatkowe opcje są niedostępne.

| Typ konta | Opcja instalacji | Opis |
| --- | --- | --- |
| [Konto usługi wirtualnej](#virtual-service-account) | Express i Custom, 2017 kwietnia i nowszych | Jest to opcja używana dla wszystkich instalacji ekspresowej, z wyjątkiem instalacji na kontrolerze domeny. W przypadku opcji Custom jest to opcja domyślna, chyba że jest używana inna opcja. |
| [Konto usługi zarządzane przez grupę](#group-managed-service-account) | Niestandardowe, 2017 kwietnia i nowsze | Jeśli używasz zdalnego programu SQL Server, zalecamy użycie konta usługi zarządzanego przez grupę. |
| [Konto użytkownika](#user-account) | Express i Custom, 2017 kwietnia i nowszych | Konto użytkownika poprzedzone AAD_ jest tworzone tylko podczas instalacji, gdy jest zainstalowany w systemie Windows Server 2008 i zainstalowany na kontrolerze domeny. |
| [Konto użytkownika](#user-account) | Express i Custom, 2017 marca i wcześniejszych | Konto lokalne z prefiksem AAD_ jest tworzone podczas instalacji. W przypadku korzystania z instalacji niestandardowej można określić inne konto. |

Jeśli używasz programu Connect z kompilacją z 2017 marca lub wcześniejszych, nie należy resetować hasła na koncie usługi, ponieważ system Windows niszczy klucze szyfrowania ze względów bezpieczeństwa. Nie można zmienić konta na inne konto bez ponownej instalacji Azure AD Connect. W przypadku uaktualnienia do kompilacji z 2017 kwietnia lub nowszej, jest ona obsługiwana, aby zmienić hasło konta usługi, ale nie można zmienić użytego konta.

> [!Important]
> Konto usługi można ustawić tylko przy pierwszej instalacji. Zmiana konta usługi po zakończeniu instalacji nie jest obsługiwana.

Jest to tabela domyślnych, zalecanych i obsługiwanych opcji dla konta usługi synchronizacji.

Legenda:

- Opcja **pogrubiona** wskazuje opcję domyślną i w większości przypadków zalecaną opcją.
- *Kursywa* wskazuje zalecaną opcję, jeśli nie jest opcją domyślną.
- 2008 — opcja domyślna w przypadku instalacji w systemie Windows Server 2008
- Opcja niepogrubiona — obsługiwana
- Konto lokalne — konto użytkownika lokalnego na serwerze
- Konto domeny — konto użytkownika domeny
- Autonomiczne — [Autonomiczne zarządzane konto usługi](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA — [konto usługi zarządzanej przez grupę](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Niestandardowy | Zdalne SQL</br>Niestandardowy |
| --- | --- | --- | --- |
| **komputer przyłączony do domeny** | **ATRYBUTU**</br>Konto lokalne (2008) | **ATRYBUTU**</br>Konto lokalne (2008)</br>Konto lokalne</br>Konto domeny</br>Autonomiczne, gMSA | **gMSA**</br>Konto domeny |
| **Kontroler domeny** | **Konto domeny** | *gMSA*</br>**Konto domeny**</br>Autonomiczne| *gMSA*</br>**Konto domeny**|

#### <a name="virtual-service-account"></a>Konto usługi wirtualnej
Konto usługi wirtualnej jest specjalnym typem konta, które nie ma hasła i jest zarządzane przez system Windows.

![Zrzut ekranu przedstawiający konto usługi wirtualnej (VSA).](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

Atrybut VSA ma być używany w scenariuszach, w których aparat synchronizacji i SQL znajdują się na tym samym serwerze. Jeśli używasz zdalnego SQL, zalecamy użycie konta usługi zarządzanej przez grupę.

Ta funkcja wymaga systemu Windows Server 2008 R2 lub nowszego. W przypadku zainstalowania Azure AD Connect w systemie Windows Server 2008, instalacja będzie powracać do korzystania z [konta użytkownika](#user-account) .

#### <a name="group-managed-service-account"></a>Konto usługi zarządzane przez grupę
Jeśli używasz zdalnego programu SQL Server, zalecamy użycie **konta usługi zarządzanego przez grupę**. Aby uzyskać więcej informacji na temat przygotowywania Active Directory dla konta usługi zarządzanego przez grupę, zobacz [Omówienie kont usług zarządzanych przez grupę](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)).

Aby użyć tej opcji, na stronie [Zainstaluj wymagane składniki](how-to-connect-install-custom.md#install-required-components) wybierz opcję **Użyj istniejącego konta usługi**, a następnie wybierz pozycję **zarządzane konto usługi**.  
![ATRYBUTU](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Jest również obsługiwane w przypadku korzystania z [autonomicznego zarządzanego konta usługi](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10)). Jednak mogą one być używane tylko na komputerze lokalnym i nie ma korzyści z używania ich przez domyślne konto usługi wirtualnej.

Ta funkcja wymaga systemu Windows Server w wersji 2012 lub nowszej. Jeśli konieczne jest użycie starszego systemu operacyjnego i użycie zdalnego programu SQL, należy użyć [konta użytkownika](#user-account).

#### <a name="user-account"></a>Konto użytkownika
Konto usługi lokalnej jest tworzone przez Kreatora instalacji (o ile nie zostanie określone konto do użycia w ustawieniach niestandardowych). Konto jest poprzedzone **AAD_** i używane dla rzeczywistej usługi synchronizacji do uruchomienia jako. W przypadku zainstalowania Azure AD Connect na kontrolerze domeny konto zostanie utworzone w domenie. Konto usługi **AAD_** musi znajdować się w domenie, jeśli:
   - używasz serwera zdalnego z programem SQL Server
   - używasz serwera proxy wymagającego uwierzytelniania

![Konto usługi synchronizacji](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Konto jest tworzone przy użyciu długiego hasła złożonego, które nie wygasa.

To konto służy do przechowywania haseł dla innych kont w bezpieczny sposób. Te inne hasła kont są przechowywane w bazie danych. Klucze prywatne dla kluczy szyfrowania są chronione za pomocą szyfrowania klucza tajnego usług kryptograficznych za pomocą interfejsu API ochrony danych systemu Windows (DPAPI).

W przypadku korzystania z pełnego SQL Server konto usługi jest obiektem DBO utworzonej bazy danych dla aparatu synchronizacji. Usługa nie będzie działać zgodnie z założeniami innych uprawnień. Zostanie również utworzona nazwa logowania SQL.

Konto ma również przyznane uprawnienia do plików, kluczy rejestru i innych obiektów związanych z aparatem synchronizacji.

### <a name="azure-ad-connector-account"></a>Konto łącznika usługi Azure AD
Konto w usłudze Azure AD jest tworzone na potrzeby użycia usługi synchronizacji. To konto może być identyfikowane przez jego nazwę wyświetlaną.

![Zrzut ekranu przedstawiający konto usługi Azure AD.](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Nazwę serwera, na którym konto jest używane, można zidentyfikować w drugiej części nazwy użytkownika. Na obrazie nazwa serwera to DC1. W przypadku serwerów przemieszczania każdy serwer ma swoje własne konto.

Konto jest tworzone przy użyciu długiego hasła złożonego, które nie wygasa. Przyznano specjalne **konta synchronizacji katalogów** ról, które mają tylko uprawnienia do wykonywania zadań synchronizacji katalogów. Nie można udzielić specjalnej wbudowanej roli poza kreatorem Azure AD Connect. Azure Portal pokazuje to konto **użytkownikowi** roli.

W usłudze Azure AD obowiązuje limit 20 kont usługi synchronizacji. Aby uzyskać listę istniejących kont usługi Azure AD w usłudze Azure AD, uruchom następujące polecenie cmdlet programu PowerShell dla usługi Azure AD: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Aby usunąć nieużywane konta usługi Azure AD, uruchom następujące polecenie cmdlet programu PowerShell dla usługi Azure AD: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Aby można było użyć powyższych poleceń programu PowerShell, należy zainstalować [moduł Azure Active Directory PowerShell for Graph](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module) i nawiązać połączenie z wystąpieniem usługi Azure AD za pomocą polecenia [Connect-AzureAD](/powershell/module/azuread/connect-azuread)

Aby uzyskać dodatkowe informacje na temat sposobu zarządzania lub resetowania hasła dla konta łącznika usługi Azure AD, zobacz [Zarządzanie kontem Azure AD Connect](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>Dokumentacja pokrewna
Jeśli nie odczytano dokumentacji dotyczącej [integrowania tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md), Poniższa tabela zawiera linki do powiązanych tematów.

|Temat |Link|  
| --- | --- |
|Pobieranie programu Azure AD Connect | [Pobieranie programu Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalowanie przy użyciu ustawień ekspresowych | [Ekspresowa instalacja programu Azure AD Connect](how-to-connect-install-express.md)|
|Instalowanie przy użyciu ustawień dostosowanych | [Niestandardowa instalacja programu Azure AD Connect](./how-to-connect-install-custom.md)|
|Uaktualnianie przy użyciu narzędzia DirSync | [Uaktualnienie przy użyciu narzędzia Azure AD Sync (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Po instalacji | [Weryfikowanie instalacji i przypisywanie licencji](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
