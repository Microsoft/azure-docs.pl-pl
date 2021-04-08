---
title: Łączniki w interfejsie użytkownika usługi Azure AD Synchronization Service Manager | Microsoft Docs "
description: Zapoznaj się z kartą łączniki w Synchronization Service Manager Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: d215c2e200308664f24daa28a0054c8f1bcfc09c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91319881"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Używanie łączników z synchronizacją Azure AD Connect Service Manager

![Zrzut ekranu, który pokazuje Service Manager synchronizacji.](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Karta łączniki służy do zarządzania wszystkimi systemami, do których jest podłączony aparat synchronizacji.

## <a name="connector-actions"></a>Akcje łącznika
| Akcja | Komentarz |
| --- | --- |
| Utwórz |Nie używaj. Aby nawiązać połączenie z dodatkowymi lasami usługi AD, użyj Kreatora instalacji programu. |
| Właściwości |Używany do filtrowania domen i jednostek organizacyjnych. |
| [Usuwanie](#delete) |Służy do usuwania danych w obszarze łącznika lub do usuwania połączenia z lasem. |
| [Skonfiguruj profile uruchamiania](#configure-run-profiles) |Poza filtrowaniem domeny nic nie należy konfigurować. Za pomocą tej akcji można wyświetlić profile przebiegów, które zostały już skonfigurowane. |
| Uruchom |Służy do uruchamiania jednokrotnego przebiegu profilu. |
| Stop |Wyłącza łącznik aktualnie z uruchomionym profilem. |
| Eksportuj łącznik |Nie używaj. |
| Importuj łącznik |Nie używaj. |
| Aktualizuj łącznik |Nie używaj. |
| Odśwież schemat |Odświeża buforowany schemat. Zamiast tego należy użyć opcji w Kreatorze instalacji, ponieważ powoduje to również aktualizację reguł synchronizacji. |
| [Wyszukaj w obszarze łącznika](#search-connector-space) |Służy do znajdowania obiektów oraz do wykonywania działań w ramach obiektu i jego danych przez system. |

### <a name="delete"></a>Usuń
Akcja usuwania jest używana dla dwóch różnych rzeczy.  
![Zrzut ekranu pokazujący okno "Usuwanie łącznika" z wybraną opcją "Usuń tylko przestrzeń łącznika".](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Opcja **Usuń spację łącznika usuwa tylko** wszystkie dane, ale zachowuje konfigurację.

Opcja **Usuń łącznik i spacja łącznika** usuwa dane i konfigurację. Ta opcja jest używana, gdy nie chcesz już łączyć się z lasem.

Obie opcje synchronizują wszystkie obiekty i aktualizują obiekty Metaverse. Ta akcja jest długotrwałą operacją.

### <a name="configure-run-profiles"></a>Skonfiguruj profile uruchamiania
Ta opcja umożliwia wyświetlenie profilów uruchamiania skonfigurowanych dla łącznika.

![Zrzut ekranu pokazujący okno "Konfigurowanie profilów uruchamiania" z wybraną pozycją "import zmian".](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Wyszukaj w obszarze łącznika
Akcja obszaru łącznika wyszukiwania jest przydatna do znajdowania obiektów i rozwiązywania problemów z danymi.

![Zrzut ekranu przedstawiający okno "obszar łącznika wyszukiwania".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Zacznij od wybrania **zakresu**. Można wyszukiwać na podstawie danych (RDN, DN, kotwicy, poddrzewa) lub stanu obiektu (wszystkie inne opcje).  
![Zrzut ekranu pokazujący menu rozwijane "zakres".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Jeśli na przykład przeszukasz Sub-Tree, uzyskasz wszystkie obiekty w jednej jednostce organizacyjnej.  
![Zrzut ekranu przedstawiający przykład wyszukiwania "poddrzewo".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Z tej siatki możesz wybrać obiekt, wybrać **Właściwości** i [postępować](tshoot-connect-object-not-syncing.md) od obszaru łącznika źródłowego, za pośrednictwem funkcji Metaverse oraz do docelowego miejsca łącznika.

### <a name="changing-the-ad-ds-account-password"></a>Zmienianie hasła konta usług AD DS
Jeśli zmienisz hasło konta, usługa synchronizacji nie będzie już mogła importować/eksportować zmian do lokalnej usługi AD.   Mogą wystąpić następujące problemy:

- Krok importu/eksportu dla łącznika usługi AD kończy się niepowodzeniem z błędem "No-Start-Credentials".
- W obszarze Podgląd zdarzeń systemu Windows dziennik zdarzeń aplikacji zawiera błąd o IDENTYFIKATORze 6000, a komunikat "contoso.com" agenta zarządzania nie powiodło się, ponieważ poświadczenia są nieprawidłowe.

Aby rozwiązać ten problem, zaktualizuj konto użytkownika AD DS przy użyciu następujących elementów:


1. Uruchom Synchronization Service Manager (Uruchom usługę synchronizacji →).
</br>![Service Manager synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Przejdź do karty **Łączniki** .
3. Wybierz łącznik usługi AD skonfigurowany do korzystania z konta AD DS.
4. W obszarze Akcje wybierz pozycję **Właściwości**.
5. W podręcznym oknie dialogowym wybierz pozycję Połącz z lasem Active Directory:
6. Nazwa lasu wskazuje odpowiedni dla lokalnej usługi AD.
7. Nazwa użytkownika wskazuje konto AD DS używane na potrzeby synchronizacji.
8. Wprowadź nowe hasło konta AD DS w polu tekstowym hasło ![ Azure AD Connect narzędzia synchronizacji klucza szyfrowania](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Kliknij przycisk OK, aby zapisać nowe hasło i ponownie uruchomić usługę synchronizacji, aby usunąć stare hasło z pamięci podręcznej pamięci.



## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji Azure AD Connect](how-to-connect-sync-whatis.md) .

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
