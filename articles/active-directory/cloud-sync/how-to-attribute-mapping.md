---
title: Mapowanie atrybutów w Azure AD Connect synchronizacji w chmurze
description: W tym artykule opisano sposób użycia funkcji synchronizacji chmury Azure AD Connect do mapowania atrybutów.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555214"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Mapowanie atrybutów w Azure AD Connect synchronizacji w chmurze

Za pomocą funkcji synchronizacji w chmurze usługi Azure Active Directory (Azure AD) Połącz się z mapowaniem atrybutów między lokalnymi obiektami użytkowników lub grup oraz obiektami w usłudze Azure AD. Ta możliwość została dodana do konfiguracji synchronizacji w chmurze.

Można dostosować (zmienić, usunąć lub utworzyć) domyślne mapowania atrybutów zgodnie z potrzebami biznesowymi. Aby zapoznać się z listą synchronizowanych atrybutów, zobacz [atrybuty synchronizowane z Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understand-types-of-attribute-mapping"></a>Opis typów mapowania atrybutów
Mapowanie atrybutu umożliwia kontrolowanie sposobu, w jaki atrybuty są wypełniane w usłudze Azure AD. Usługa Azure AD obsługuje cztery typy mapowania:

- **Direct**: atrybut target jest wypełniany wartością atrybutu obiektu połączonego w Active Directory.
- **Stała**: atrybut docelowy jest wypełniany określonym ciągiem, który określisz.
- **Wyrażenie**: atrybut target jest wypełniany w oparciu o wynik wyrażenia przypominającego skrypt. Aby uzyskać więcej informacji, zobacz [Pisanie wyrażeń do mapowania atrybutów w Azure Active Directory](reference-expressions.md).
- **Brak**: atrybut docelowy nie został zmodyfikowany. Jeśli jednak atrybut target jest kiedykolwiek pusty, zostanie wypełniony wartością domyślną, którą określisz.

Wraz z tymi typami podstawowymi mapowania atrybutów niestandardowych obsługują pojęcie opcjonalnego przypisania wartości *domyślnych* . Przypisanie wartości domyślnej gwarantuje, że atrybut docelowy zostanie wypełniony wartością, jeśli usługa Azure AD lub obiekt docelowy nie ma wartości. Najbardziej typową konfiguracją jest pozostawienie tej pustej.

## <a name="understand-properties-of-attribute-mapping"></a>Informacje o właściwościach mapowania atrybutów

Wraz z właściwością typ mapowania atrybutów obsługują następujące atrybuty:

- **Atrybut źródłowy**: atrybut użytkownika z systemu źródłowego (przykład: Active Directory).
- **Atrybut target**: atrybut User w systemie docelowym (przykład: Azure Active Directory).
- **Wartość domyślna w przypadku wartości null (opcjonalnie)**: wartość, która zostanie przeniesiona do systemu docelowego, jeśli atrybut źródłowy ma wartość null. Ta wartość zostanie zainicjowana tylko wtedy, gdy użytkownik zostanie utworzony. Nie zostanie ona zainicjowana, gdy aktualizujesz istniejącego użytkownika.  
- **Zastosuj to mapowanie**:
  - **Zawsze**: Zastosuj to mapowanie zarówno w przypadku akcji tworzenia przez użytkownika, jak i aktualizacji.
  - **Tylko podczas tworzenia**: Zastosuj to mapowanie tylko w przypadku akcji tworzenia przez użytkownika.

> [!NOTE]
> W tym artykule opisano sposób użycia Azure Portal do mapowania atrybutów.  Aby uzyskać informacje na temat korzystania z Microsoft Graph, zobacz [przekształcenia](how-to-transformation.md).

## <a name="add-an-attribute-mapping"></a>Dodawanie mapowania atrybutów

Aby skorzystać z nowej możliwości, wykonaj następujące kroki:

1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
2.  Wybierz **Azure AD Connect**.
3.  Wybierz pozycję **Zarządzaj synchronizacją w chmurze**.

    ![Zrzut ekranu przedstawiający link do zarządzania synchronizacją w chmurze.](media/how-to-install/install-6.png)

4. W obszarze **Konfiguracja** wybierz konfigurację.
5. Wybierz **pozycję kliknij, aby edytować mapowania**.  To łącze powoduje otwarcie ekranu **mapowania atrybutów** .

    ![Zrzut ekranu przedstawiający link do dodawania atrybutów.](media/how-to-attribute-mapping/mapping-6.png)

6.  Wybierz pozycję **Dodaj atrybut**.

    ![Zrzut ekranu pokazujący przycisk dodawania atrybutu wraz z listami atrybutów i typów mapowania.](media/how-to-attribute-mapping/mapping-1.png)

7. Wybierz typ mapowania. W tym przykładzie używane jest **wyrażenie**.
8. Wprowadź wyrażenie w polu. W tym przykładzie używamy `Replace([mail], "@contoso.com", , ,"", ,)` .
9. Wprowadź atrybut target. W tym przykładzie korzystamy z usługi **ExtensionAttribute15**.
10. Wybierz, kiedy zastosować to mapowanie, a następnie wybierz pozycję **Zastosuj**.

    ![Zrzut ekranu pokazujący wypełniono pola do tworzenia mapowania atrybutów.](media/how-to-attribute-mapping/mapping-2a.png)

11. Z powrotem na ekranie **mapowania atrybutów** powinien zostać wyświetlony nowy Mapowanie atrybutów.  
12. Wybierz pozycję **Zapisz schemat**.

    ![Zrzut ekranu pokazujący przycisk Zapisz schemat.](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Testowanie mapowania atrybutów

Aby przetestować Mapowanie atrybutów, można użyć [inicjowania obsługi na żądanie](how-to-on-demand-provision.md): 

1. W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
2. Wybierz **Azure AD Connect**.
3. Wybierz pozycję **Zarządzaj obsługą**.
4. W obszarze **Konfiguracja** wybierz konfigurację.
5. W obszarze **Weryfikuj** wybierz przycisk **Udostępnij użytkownikowi** . 
6. Na ekranie **udostępnianie na żądanie** wprowadź nazwę wyróżniającą użytkownika lub grupy, a następnie wybierz przycisk **Udostępnij** . 

   Na ekranie zostanie wyświetlona wartość trwa inicjowanie obsługi administracyjnej.

   ![Zrzut ekranu pokazujący, że inicjowanie obsługi jest w toku.](media/how-to-attribute-mapping/mapping-4.png)

8. Po zakończeniu aprowizacji zostanie wyświetlony ekran z czterema zielonymi znacznikami wyboru. 

   W obszarze **Wykonaj akcję** wybierz pozycję **Wyświetl szczegóły**. Po prawej stronie powinien zostać wyświetlony synchronizowany nowy atrybut oraz zastosowane wyrażenie.

   ![Zrzut ekranu pokazujący pomyślne i eksportowe szczegóły.](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Następne kroki

- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)
- [Pisanie wyrażeń do mapowania atrybutów](reference-expressions.md)
- [Atrybuty synchronizowane z usługą Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
