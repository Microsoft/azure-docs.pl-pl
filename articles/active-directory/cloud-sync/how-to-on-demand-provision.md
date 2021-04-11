---
title: Inicjowanie obsługi administracyjnej na żądanie w Azure AD Connect synchronizacji w chmurze
description: W tym artykule opisano sposób korzystania z funkcji synchronizacji z chmurą Azure AD Connect do testowania zmian konfiguracji.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554279"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Inicjowanie obsługi administracyjnej na żądanie w Azure AD Connect synchronizacji w chmurze

Za pomocą funkcji synchronizacji w chmurze usługi Azure Active Directory (Azure AD) Połącz się ze zmianami konfiguracji testu, stosując te zmiany do pojedynczego użytkownika. Ta obsługa na żądanie umożliwia zweryfikowanie i sprawdzenie, czy zmiany wprowadzone do konfiguracji zostały zastosowane prawidłowo i są prawidłowo zsynchronizowane z usługą Azure AD.  

> [!IMPORTANT] 
> W przypadku korzystania z aprowizacji na żądanie, filtry zakresu nie są stosowane do wybranego użytkownika. Można korzystać z aprowizacji na żądanie dla użytkowników, którzy znajdują się poza określonymi jednostkami organizacyjnymi.

## <a name="validate-a-user"></a>Weryfikowanie użytkownika
Aby skorzystać z aprowizacji na żądanie, wykonaj następujące kroki:

1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
2.  Wybierz **Azure AD Connect**.
3.  Wybierz pozycję **Zarządzaj synchronizacją w chmurze**.

    ![Zrzut ekranu przedstawiający link do zarządzania synchronizacją w chmurze.](media/how-to-install/install-6.png)
4. W obszarze **Konfiguracja** wybierz konfigurację.
5. W obszarze **Weryfikuj** wybierz przycisk **Udostępnij użytkownikowi** . 

   ![Zrzut ekranu pokazujący przycisk udostępniania użytkownika.](media/how-to-on-demand-provision/on-demand-2.png)

6. Na ekranie **udostępnianie na żądanie** wprowadź nazwę wyróżniającą użytkownika i wybierz przycisk **Udostępnij** .  
 
   ![Zrzut ekranu, na którym jest wyświetlana nazwa użytkownika i przycisk udostępniania.](media/how-to-on-demand-provision/on-demand-3.png)
7. Po zakończeniu aprowizacji zostanie wyświetlony ekran z czterema zielonymi znacznikami wyboru. Wszystkie błędy pojawiają się po lewej stronie.

   ![Zrzut ekranu przedstawiający Pomyślne inicjowanie obsługi.](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>Uzyskaj szczegółowe informacje o aprowizacji
Teraz można sprawdzić informacje o użytkowniku i określić, czy zmiany wprowadzone w konfiguracji zostały zastosowane. W dalszej części tego artykułu opisano poszczególne sekcje, które są wyświetlane w szczegółach pomyślnie zsynchronizowanych użytkowników.

### <a name="import-user"></a>Importuj użytkownika
Sekcja **Importowanie użytkownika** zawiera informacje o użytkowniku, który został zaimportowany z Active Directory. Ten użytkownik będzie wyglądać przed zainicjowaniem obsługi administracyjnej w usłudze Azure AD. Wybierz łącze **Wyświetl szczegóły** , aby wyświetlić te informacje.

![Zrzut ekranu przedstawiający przycisk służący do wyświetlania szczegółów dotyczących zaimportowanego użytkownika.](media/how-to-on-demand-provision/on-demand-5.png)

Korzystając z tych informacji, można zobaczyć różne atrybuty (i ich wartości), które zostały zaimportowane. Jeśli utworzono Mapowanie atrybutów niestandardowych, można zobaczyć wartość tutaj.

![Zrzut ekranu pokazujący szczegóły użytkownika.](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Określanie, czy użytkownik jest w zasięgu
Sekcja **Określanie, czy użytkownik znajduje się w zakresie** zawiera informacje o tym, czy użytkownik, który został zaimportowany do usługi Azure AD, znajduje się w zakresie. Wybierz łącze **Wyświetl szczegóły** , aby wyświetlić te informacje.

![Zrzut ekranu przedstawiający przycisk służący do wyświetlania szczegółowych informacji o zakresie użytkownika.](media/how-to-on-demand-provision/on-demand-7.png)

Korzystając z tych informacji, można sprawdzić, czy użytkownik znajduje się w zakresie.

![Zrzut ekranu pokazujący szczegóły zakresu użytkownika.](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Dopasuj użytkownika między systemem źródłowym i docelowym
Sekcja **Dopasuj użytkownika między systemem źródłowym i docelowym** zawiera informacje na temat tego, czy użytkownik już istnieje w usłudze Azure AD, oraz czy należy przyłączyć się zamiast aprowizacji nowego użytkownika. Wybierz łącze **Wyświetl szczegóły** , aby wyświetlić te informacje.

![Zrzut ekranu przedstawiający przycisk służący do wyświetlania szczegółów dotyczących dopasowanego użytkownika.](media/how-to-on-demand-provision/on-demand-8.png)

Korzystając z tych informacji, można sprawdzić, czy znaleziono dopasowanie, czy też utworzyć nowy użytkownik.

![Zrzut ekranu pokazujący informacje o użytkowniku.](media/how-to-on-demand-provision/on-demand-11.png)

Szczegóły dopasowania pokazują komunikat z jedną z trzech następujących operacji:
- **Tworzenie**: użytkownik jest tworzony w usłudze Azure AD.
- **Aktualizacja**: użytkownik jest aktualizowany na podstawie zmiany wprowadzonej w konfiguracji.
- **Usuwanie**: użytkownik jest usuwany z usługi Azure AD.

W zależności od typu wykonywanej operacji komunikat będzie się różnić.

### <a name="perform-action"></a>Wykonaj akcję
Sekcja **wykonywanie akcji** zawiera informacje o użytkowniku, który został zainicjowany lub wyeksportowany do usługi Azure AD po zastosowaniu konfiguracji. Jest to wynik, który użytkownik będzie wyglądać po zainicjowaniu obsługi administracyjnej w usłudze Azure AD. Wybierz łącze **Wyświetl szczegóły** , aby wyświetlić te informacje.

![Zrzut ekranu przedstawiający przycisk służący do wyświetlania szczegółowych informacji o wykonanej akcji.](media/how-to-on-demand-provision/on-demand-9.png)

Korzystając z tych informacji, można zobaczyć wartości atrybutów po zastosowaniu konfiguracji. Czy są one podobne do zaimportowanych lub innych? Czy konfiguracja została pomyślnie zastosowana?  

Ten proces umożliwia śledzenie przekształcenia atrybutów w miarę ich przechodzenia przez chmurę i do dzierżawy usługi Azure AD.

![Zrzut ekranu pokazujący szczegóły dotyczące śledzonych atrybutów.](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Następne kroki 

- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)
- [Instalowanie Azure AD Connect synchronizacji w chmurze](how-to-install.md)
 