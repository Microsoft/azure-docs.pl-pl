---
title: 'Azure AD Connect: konto usługi ADSync | Microsoft Docs'
description: W tym temacie opisano konto usługi ADSync i przedstawiono najlepsze rozwiązania dotyczące konta.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb23d79caa6964c3f61fbb84c8b8f229f475b8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722161"
---
# <a name="adsync-service-account"></a>Konto usługi ADSync
Azure AD Connect instaluje usługę lokalną, która organizuje synchronizację między Active Directory i Azure Active Directory.  Usługa synchronizacji synchronizacji Microsoft Azure AD (ADSync) jest uruchamiana na serwerze w środowisku lokalnym.  Poświadczenia usługi są domyślnie ustawiane w instalacjach ekspresowych, ale mogą być dostosowane do wymagań bezpieczeństwa organizacji.  Te poświadczenia nie są używane do nawiązywania połączeń z lokalnymi lasami lub Azure Active Directory.

Wybranie konta usługi ADSync to ważna decyzja dotycząca planowania przed zainstalowaniem Azure AD Connect.  Każda próba zmiany poświadczeń po zakończeniu instalacji spowoduje niepowodzenie uruchomienia usługi, utratę dostępu do bazy danych synchronizacji i niepowodzenie uwierzytelniania przy użyciu podłączonych katalogów (platformy Azure i AD DS).  Żadna synchronizacja nie zostanie wykonana do momentu przywrócenia oryginalnych poświadczeń.

Usługa synchronizacji może działać na różnych kontach. Może działać w ramach konta usługi wirtualnej (VSA), zarządzanego konta usługi (gMSA/autonomiczne) lub zwykłego konta użytkownika. Obsługiwane opcje zostały zmienione wraz z wydaniem 2017 kwietnia i 2021 marca Azure AD Connect podczas przeprowadzania nowej instalacji. W przypadku uaktualnienia z wcześniejszej wersji Azure AD Connect te dodatkowe opcje są niedostępne. 


|Typ konta|Opcja instalacji|Opis| 
|-----|------|-----|
|Konto usługi wirtualnej|Express i Custom, 2017 kwietnia i nowszych| Wirtualne konto usługi jest używane dla wszystkich instalacji ekspresowej, z wyjątkiem instalacji na kontrolerze domeny. W przypadku korzystania z instalacji niestandardowej jest to opcja domyślna, chyba że jest używana inna opcja.| 
|Zarządzane konto usługi|Niestandardowe, 2017 kwietnia i nowsze|Jeśli używasz zdalnej SQL Server, zalecamy użycie konta usługi zarządzanego przez grupę. |
|Zarządzane konto usługi|Express i Custom, 2021 marca i nowszych|Autonomiczne zarządzane konto usługi z prefiksem ADSyncMSA_ jest tworzone podczas instalacji dla instalacji ekspresowej, gdy jest zainstalowany na kontrolerze domeny. W przypadku korzystania z instalacji niestandardowej jest to opcja domyślna, chyba że jest używana inna opcja.|
|Konto użytkownika|Express i Custom, 2017 kwietnia do 2021 marca|Konto użytkownika poprzedzone prefiksem AAD_ jest tworzone podczas instalacji dla instalacji ekspresowych po zainstalowaniu na kontrolerze domeny. W przypadku korzystania z instalacji niestandardowej jest to opcja domyślna, chyba że jest używana inna opcja.|
|Konto użytkownika|Express i Custom, 2017 marca i wcześniejszych|Konto użytkownika poprzedzone AAD_ jest tworzone podczas instalacji dla instalacji ekspresowej. W przypadku korzystania z instalacji niestandardowej można określić inne konto.| 

>[!IMPORTANT]
> Jeśli używasz programu Connect z kompilacją z 2017 marca lub wcześniejszych, nie należy resetować hasła na koncie usługi, ponieważ system Windows niszczy klucze szyfrowania ze względów bezpieczeństwa. Nie można zmienić konta na inne konto bez ponownej instalacji Azure AD Connect. W przypadku uaktualnienia do kompilacji z 2017 kwietnia lub nowszej, jest ona obsługiwana, aby zmienić hasło konta usługi, ale nie można zmienić użytego konta. 

> [!IMPORTANT]
> Konto usługi można ustawić tylko przy pierwszej instalacji. Zmiana konta usługi po zakończeniu instalacji nie jest obsługiwana. Jeśli chcesz zmienić hasło do konta usługi, jest to obsługiwane i instrukcje można znaleźć [tutaj](how-to-connect-sync-change-serviceacct-pass.md).

Poniżej znajduje się tabela z domyślnymi, zalecanymi i obsługiwanymi opcjami dla konta usługi synchronizacji. 

Legenda: 

- Opcja **pogrubiona** wskazuje opcję domyślną i w większości przypadków zalecaną opcją. 
- *Kursywa* wskazuje zalecaną opcję, jeśli nie jest opcją domyślną. 
- Opcja niepogrubiona — obsługiwana 
- Konto lokalne — konto użytkownika lokalnego na serwerze 
- Konto domeny — konto użytkownika domeny 
- Autonomiczne — [Autonomiczne zarządzane konto usługi](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA — [konto usługi zarządzanej przez grupę](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

 ||**LocalDB </br> Express**|**LocalDB/LocalSQL </br> Custom**|**Zdalne </br> niestandardowe SQL**|
|-----|-----|-----|-----|
|**komputer przyłączony do domeny**|**ATRYBUTU**|**ATRYBUTU**</br> *Autonomiczne*</br> *gMSA*</br> Konto lokalne</br> Konto domeny| *gMSA* </br>Konto domeny|
|Kontroler domeny| **Autonomiczne**|**Autonomiczne** </br>*gMSA*</br> Konto domeny|*gMSA*</br>Konto domeny| 

## <a name="virtual-service-account"></a>Konto usługi wirtualnej 

Konto usługi wirtualnej jest specjalnym typem zarządzanego konta lokalnego, które nie ma hasła i jest automatycznie zarządzane przez system Windows. 

 ![Konto usługi wirtualnej](media/concept-adsync-service-account/account-1.png)

Konto usługi wirtualnej jest przeznaczone do użycia w scenariuszach, w których aparat synchronizacji i SQL znajdują się na tym samym serwerze. Jeśli używasz zdalnego SQL, zalecamy użycie konta usługi zarządzanej przez grupę. 

Nie można używać konta usługi wirtualnej na kontrolerze domeny z powodu problemów z [interfejsem API ochrony danych systemu Windows (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx) . 

## <a name="managed-service-account"></a>Zarządzane konto usługi 

Jeśli używasz zdalnej SQL Server, zalecamy użycie konta usługi zarządzanego przez grupę. Aby uzyskać więcej informacji na temat przygotowywania Active Directory dla konta usługi zarządzanego przez grupę, zobacz [Omówienie kont usług zarządzanych przez grupę](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)). 

Aby użyć tej opcji, na stronie [Zainstaluj wymagane składniki](how-to-connect-install-custom.md#install-required-components) wybierz opcję **Użyj istniejącego konta usługi**, a następnie wybierz pozycję **zarządzane konto usługi**. 

 ![zarządzane konto usługi](media/concept-adsync-service-account/account-2.png)

Jest również obsługiwane w przypadku korzystania z autonomicznego zarządzanego konta usługi. Jednak mogą one być używane tylko na komputerze lokalnym i nie ma korzyści z używania ich przez domyślne konto usługi wirtualnej. 

### <a name="auto-generated-standalone-managed-service-account"></a>Automatycznie generowane Autonomiczne zarządzane konto usługi 

W przypadku zainstalowania Azure AD Connect na kontrolerze domeny, Kreator instalacji utworzy Autonomiczne zarządzane konto usługi (chyba że zostanie określone konto do użycia w ustawieniach niestandardowych). Konto jest poprzedzone **ADSyncMSA_** i używane dla rzeczywistej usługi synchronizacji do uruchomienia jako. 

To konto jest zarządzanym kontem domeny, które nie ma hasła i jest automatycznie zarządzane przez system Windows. 

To konto jest przeznaczone do użycia w scenariuszach, w których aparat synchronizacji i SQL znajdują się na kontrolerze domeny. 

## <a name="user-account"></a>Konto użytkownika 

Konto usługi lokalnej jest tworzone przez Kreatora instalacji (o ile nie zostanie określone konto do użycia w ustawieniach niestandardowych). Konto jest poprzedzone AAD_ i używane dla rzeczywistej usługi synchronizacji do uruchomienia jako. W przypadku zainstalowania Azure AD Connect na kontrolerze domeny konto zostanie utworzone w domenie. Konto usługi AAD_ musi znajdować się w domenie, jeśli: 
- używasz zdalnego serwera z systemem SQL Server 
- używasz serwera proxy wymagającego uwierzytelniania 

 ![konto użytkownika](media/concept-adsync-service-account/account-3.png)

Konto jest tworzone przy użyciu długiego hasła złożonego, które nie wygasa. 

To konto służy do przechowywania haseł dla innych kont w bezpieczny sposób. Te inne hasła kont są przechowywane w bazie danych. Klucze prywatne dla kluczy szyfrowania są chronione za pomocą szyfrowania klucza tajnego usług kryptograficznych za pomocą interfejsu API ochrony danych systemu Windows (DPAPI). 

W przypadku korzystania z pełnego SQL Server konto usługi jest obiektem DBO utworzonej bazy danych dla aparatu synchronizacji. Usługa nie będzie działać zgodnie z żadnym innym uprawnieniem. Zostanie również utworzona nazwa logowania SQL. 

Konto ma również przyznane uprawnienia do plików, kluczy rejestru i innych obiektów związanych z aparatem synchronizacji. 


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
