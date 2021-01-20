---
title: Edytor atrybutów synchronizacji chmury Azure AD Connect
description: W tym artykule opisano sposób korzystania z edytora atrybutów.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f1b1d792891b3ab8dabff42757d0e705e512879
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613796"
---
# <a name="azure-ad-connect-cloud-sync-attribute-mapping"></a>Mapowanie atrybutu synchronizacji chmury Azure AD Connect

Usługa Azure AD Connect Cloud Sync wprowadziła nową funkcję, która umożliwi łatwe Mapowanie atrybutów między obiektami lokalnych użytkowników/grup i obiektami w usłudze Azure AD.  Ta funkcja została dodana do konfiguracji synchronizacji w chmurze.

Domyślne mapowania atrybutów można dostosować zgodnie z potrzebami biznesowymi. W związku z tym można zmienić lub usunąć istniejące mapowania atrybutów lub utworzyć nowe mapowania atrybutów.  Aby zapoznać się z listą synchronizowanych atrybutów, zobacz [atrybuty, które są synchronizowane](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understanding-attribute-mapping-types"></a>Informacje o typach mapowań atrybutów
Mapowania atrybutów umożliwiają kontrolowanie sposobu, w jaki atrybuty są wypełniane w usłudze Azure AD.
Obsługiwane są cztery różne typy mapowania:

- **Direct** — atrybut docelowy jest wypełniany wartością atrybutu obiektu połączonego w usłudze AD.
- **Stała** — atrybut docelowy jest wypełniony określonym określonym ciągiem.
- **Expression** — atrybut target jest wypełniany w oparciu o wynik wyrażenia przypominającego skrypt.
  Aby uzyskać więcej informacji, zobacz [Pisanie wyrażeń do mapowania atrybutów](reference-expressions.md).
- **Brak** — atrybut docelowy nie został zmodyfikowany. Jeśli jednak atrybut target jest kiedykolwiek pusty, zostanie wypełniony wartością domyślną, którą określisz.

Podobnie jak te cztery podstawowe typy, niestandardowe mapowania atrybutów obsługują koncepcję opcjonalnego przypisania wartości **domyślnych** . Przypisanie wartości domyślnej gwarantuje, że atrybut docelowy zostanie wypełniony wartością, jeśli nie istnieje wartość w usłudze Azure AD lub w obiekcie docelowym. Najbardziej typową konfiguracją jest pozostawienie tej pustej.

## <a name="understanding-attribute-mapping-properties"></a>Informacje o właściwościach mapowania atrybutów

W poprzedniej sekcji wprowadzono już do właściwości Typ mapowania atrybutu.
Wraz z tą właściwością mapowania atrybutów obsługują również następujące atrybuty:

- **Atrybut źródłowy** — atrybut użytkownika z systemu źródłowego (przykład: Active Directory).
- **Attribute** — atrybut użytkownika w systemie docelowym (przykład: Azure Active Directory).
- **Wartość domyślna w przypadku wartości null (opcjonalnie)** — wartość, która zostanie przeniesiona do systemu docelowego, jeśli atrybut źródłowy ma wartość null. Ta wartość zostanie zainicjowana tylko wtedy, gdy użytkownik zostanie utworzony. "Wartość domyślna, gdy wartość null" nie zostanie zainicjowana podczas aktualizowania istniejącego użytkownika.  
- **Zastosuj to mapowanie**
  - **Zawsze** — Zastosuj to mapowanie zarówno dla akcji tworzenia i aktualizowania użytkownika.
  - **Tylko podczas tworzenia** — Zastosuj to mapowanie tylko w przypadku akcji tworzenia użytkownika.

> [!NOTE]
> W tym dokumencie opisano sposób użycia Azure Portal do mapowania atrybutów.  Aby uzyskać informacje na temat korzystania z programu Graph, zobacz [przekształcenia](how-to-transformation.md)

## <a name="using-attribute-mapping"></a>Używanie mapowania atrybutów

Aby skorzystać z nowej funkcji, wykonaj poniższe kroki.

1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
2.  Wybierz **Azure AD Connect**.
3.  Wybierz pozycję **Zarządzaj obsługą**.

    ![Zarządzanie obsługą administracyjną](media/how-to-configure/manage-1.png)

4. W obszarze **Konfiguracja** wybierz konfigurację.
5. Wybierz **pozycję kliknij, aby edytować mapowania**.  Spowoduje to otwarcie ekranu mapowania atrybutów.

    ![Dodawanie atrybutów](media/how-to-attribute-mapping/mapping-6.png)

6.  Kliknij pozycję **Dodaj atrybut**.

    ![Typ mapowania](media/how-to-attribute-mapping/mapping-1.png)

7. Wybierz **Typ mapowania**.  W tym przykładzie użyto wyrażenia.
8.  Wprowadź wyrażenie w polu.  W tym przykładzie używamy: `Replace([mail], "@contoso.com", , ,"", ,).`
9.  Wprowadź atrybut target.  W tym przykładzie używamy ExtensionAttribute15.
10. Wybierz, kiedy mają być stosowane, a następnie kliknij przycisk **Zastosuj** .

    ![Edytuj mapowania](media/how-to-attribute-mapping/mapping-2a.png)

11. Z powrotem na ekranie mapowania atrybutów powinien zostać wyświetlony nowy Mapowanie atrybutów.  
12. Kliknij pozycję **Zapisz schemat**.

    ![Zapisz schemat](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Testowanie mapowania atrybutów

Aby przetestować Mapowanie atrybutów, można użyć [inicjowania obsługi na żądanie](how-to-on-demand-provision.md).  Z 

1. W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
2. Wybierz **Azure AD Connect**.
3. Wybierz pozycję **Zarządzaj obsługą**.
4. W obszarze **Konfiguracja** wybierz konfigurację.
5. W obszarze **Weryfikuj** kliknij przycisk **Zainicjuj obsługę użytkownika** . 
6. Na ekranie aprowizacji na żądanie.  Wprowadź **nazwę wyróżniającą** użytkownika lub grupy, a następnie kliknij przycisk **Udostępnij** .  
7. Po jego zakończeniu powinien pojawić się ekran o powodzeniu i 4 zielone pola wyboru wskazujące, że został on pomyślnie zainicjowany.  

    ![Pomyślne zainicjowanie obsługi](media/how-to-attribute-mapping/mapping-4.png)

8. W obszarze **Wykonaj akcję** kliknij pozycję **Wyświetl szczegóły**.  Po prawej stronie powinien zostać wyświetlony synchronizowany nowy atrybut oraz zastosowane wyrażenie.

  ![Wykonaj akcję](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Następne kroki

- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)
- [Pisanie wyrażeń do mapowania atrybutów](reference-expressions.md)
- [Synchronizowane atrybuty](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
