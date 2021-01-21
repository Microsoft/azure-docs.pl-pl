---
title: Inicjowanie obsługi administracyjnej na żądanie w Azure AD Connect synchronizacji w chmurze
description: W tym artykule opisano funkcję aprowizacji na żądanie.
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
ms.openlocfilehash: 6ac186d4b460165605ccf0fc53bdb0b691348bf3
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622528"
---
# <a name="azure-ad-connect-cloud-sync-on-demand-provisioning"></a>Inicjowanie obsługi administracyjnej na żądanie w Azure AD Connect synchronizacji w chmurze

Usługa Azure AD Connect Cloud Sync wprowadziła nową funkcję, która umożliwi testowanie zmian w konfiguracji, stosując te zmiany do pojedynczego użytkownika.  Można go użyć do sprawdzenia poprawności i sprawdzenia, czy zmiany wprowadzone do konfiguracji zostały zastosowane prawidłowo i są prawidłowo zsynchronizowane z usługą Azure AD.  

> [!IMPORTANT] 
> W przypadku korzystania z aprowizacji na żądanie, filtry zakresu nie są stosowane do wybranego użytkownika.  Oznacza to, że można korzystać z aprowizacji na żądanie dla użytkowników, którzy znajdują się poza określonymi jednostkami organizacyjnymi.


## <a name="using-on-demand-provisioning"></a>Korzystanie z aprowizacji na żądanie
Aby skorzystać z nowej funkcji, wykonaj poniższe kroki.


1.  W witrynie Azure Portal wybierz pozycję **Azure Active Directory**.
2.  Wybierz **Azure AD Connect**.
3.  Wybierz pozycję **Zarządzaj synchronizacją w chmurze**.

    ![Zarządzanie obsługą administracyjną](media/how-to-install/install-6.png)
4. W obszarze **Konfiguracja** wybierz konfigurację.
5. W obszarze **Weryfikuj** kliknij przycisk **Zainicjuj obsługę użytkownika** . 

 ![Inicjowanie obsługi administracyjnej użytkownika](media/how-to-on-demand-provision/on-demand-2.png)

6. Na ekranie aprowizacji na żądanie.  Wprowadź **nazwę wyróżniającą** użytkownika, a następnie kliknij przycisk **Udostępnij** .  
 
 ![Inicjowanie obsługi na żądanie](media/how-to-on-demand-provision/on-demand-3.png)
7. Po jego zakończeniu powinien pojawić się ekran o powodzeniu i 4 zielone pola wyboru wskazujące, że został on pomyślnie zainicjowany.  Wszystkie błędy pojawią się po lewej stronie.

  ![Powodzenie](media/how-to-on-demand-provision/on-demand-4.png)

Teraz możesz przyjrzeć się użytkownikowi i określić, czy zmiany wprowadzone w konfiguracji zostały zastosowane.  W pozostałej części niniejszego dokumentu opisano poszczególne sekcje, które są wyświetlane w szczegółach pomyślnie zsynchronizowanych użytkowników.

## <a name="import-user-details"></a>Importuj szczegóły użytkownika
Ta sekcja zawiera informacje o użytkowniku, który został zaimportowany z Active Directory.  Ten użytkownik będzie wyglądać przed zainicjowaniem obsługi administracyjnej w usłudze Azure AD.  Kliknij link **Wyświetl szczegóły** , aby wyświetlić te informacje.

![Importuj użytkownika](media/how-to-on-demand-provision/on-demand-5.png)

Korzystając z tych informacji, można zobaczyć różne atrybuty i ich wartości, które zostały zaimportowane.  Jeśli utworzono Mapowanie atrybutów niestandardowych, będzie można zobaczyć wartość w tym miejscu.
![Importuj szczegóły użytkownika](media/how-to-on-demand-provision/on-demand-6.png)

## <a name="determine-if-user-is-in-scope-details"></a>Określanie, czy szczegóły użytkownika są w zakresie
Ta sekcja zawiera informacje o tym, czy użytkownik, który został zaimportowany do usługi Azure AD, znajduje się w zakresie.  Kliknij link **Wyświetl szczegóły** , aby wyświetlić te informacje.

![Zakres użytkownika](media/how-to-on-demand-provision/on-demand-7.png)

Korzystając z tych informacji, można wyświetlić dodatkowe informacje o zakresie użytkowników.

![Szczegóły zakresu użytkownika](media/how-to-on-demand-provision/on-demand-10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>Dopasuj użytkownika do szczegółów systemu źródłowego i docelowego
Ta sekcja zawiera informacje o tym, czy użytkownik już istnieje w usłudze Azure AD i czy ma miejsce przyłączenie zamiast aprowizacji nowego użytkownika.  Kliknij link **Wyświetl szczegóły** , aby wyświetlić te informacje.
![Wyświetl szczegóły](media/how-to-on-demand-provision/on-demand-8.png)

Korzystając z tych informacji, można sprawdzić, czy znaleziono dopasowanie, czy też utworzyć nowy użytkownik.

![Informacje o użytkowniku](media/how-to-on-demand-provision/on-demand-11.png)

W dopasowanych szczegółach zostanie wyświetlony komunikat z jedną z trzech następujących operacji.  Są to:
- Tworzenie — użytkownik jest tworzony w usłudze Azure AD
- Aktualizacja — użytkownik jest aktualizowany na podstawie zmiany wprowadzonej w konfiguracji
- Usuwanie — użytkownik jest usuwany z usługi Azure AD.

W zależności od typu wykonywanej operacji komunikat będzie się różnić.

## <a name="perform-action-details"></a>Wykonaj szczegóły akcji
Ta sekcja zawiera informacje o użytkowniku, który został zainicjowany lub wyeksportowany do usługi Azure AD po zastosowaniu konfiguracji.  Jest to wynik, który użytkownik będzie wyglądać po zainicjowaniu obsługi administracyjnej w usłudze Azure AD.  Kliknij link **Wyświetl szczegóły** , aby wyświetlić te informacje.
![Wykonaj szczegóły akcji](media/how-to-on-demand-provision/on-demand-9.png)

Korzystając z tych informacji, po zastosowaniu konfiguracji można zobaczyć wartości atrybutów.  Czy wyglądają podobnie jak te, które zostały zaimportowane lub są inne?  Czy konfiguracja jest stosowana pomyślnie?  

Dzięki temu będzie można śledzić przekształcenia atrybutów podczas ich przechodzenia przez chmurę i do dzierżawy usługi Azure AD.

![atrybut śledzenia](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Następne kroki 

- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)
- [Jak zainstalować Azure AD Connect synchronizacji w chmurze](how-to-install.md)
 