---
title: Niewłaściwy zestaw użytkowników jest zainicjowany do aplikacji w galerii usługi Azure AD
description: Dowiedz się, jak dowiedzieć się, dlaczego do aplikacji jest inicjowany inny zestaw użytkowników niż oczekiwano
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2018
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 5a109f1a06e7ee8aff3e455c009217ff670d2781
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84782215"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>W aplikacji z galerii usługi Azure AD jest inicjowany niewłaściwy zestaw użytkowników

Użytkowników, którzy są obsługiwani do aplikacji, są przede wszystkim oparte na tym, którzy użytkownicy i które grupy zostali **przypisani** do aplikacji.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak sprawdzić, którzy użytkownicy i które grupy zostali przypisani do aplikacji w ramach Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Przypisywanie użytkownika bezpośrednio jako administrator

Aby bezpośrednio przypisać jednego lub większą liczbę użytkowników do aplikacji, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8. Aby otworzyć okienko **Dodaj przypisanie** , kliknij przycisk **Dodaj** na górze listy **Użytkownicy i grupy** .

9. Kliknij selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. Wpisz **pełną nazwę** lub **adres e-mail** użytkownika, którego chcesz przypisać, w polu Wyszukaj **według nazwy lub adresu e-mail** .

11. Umieść kursor nad **użytkownikiem** na liście, aby odsłonić **pole wyboru**. Kliknij pole wyboru obok zdjęcia lub logo profilu użytkownika, aby dodać użytkownika do **wybranej** listy.

12. **Opcjonalne:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, wpisz inną **pełną nazwę** lub **adres e-mail** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz** , aby dodać do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **roli wybierz** w okienku **Dodaj przypisanie** , aby wybrać rolę, która ma zostać przypisana do wybranych użytkowników.

15. Kliknij przycisk **Przypisz** , aby przypisać aplikację do wybranych użytkowników.

Jeśli obsługa administracyjna została skonfigurowana i jest już uruchomiona dla aplikacji, nowi użytkownicy powinni być obsługiwani do aplikacji w ciągu około 10 minut. Szczegółowe informacje znajdują się w **dziennikach inspekcji** .

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Przypisywanie grupy bezpośrednio do aplikacji jako administrator

Aby bezpośrednio przypisać jedną lub więcej grup do aplikacji, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8. Aby otworzyć okienko **Dodaj przypisanie** , kliknij przycisk **Dodaj** na górze listy **Użytkownicy i grupy** .

9. Kliknij selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. Wpisz **pełną nazwę** grupy, którą chcesz przypisać, w polu Wyszukaj **według nazwy lub adresu e-mail** .

11. Umieść kursor nad **grupą** na liście, aby odsłonić **pole wyboru**. Kliknij pole wyboru obok zdjęcia lub logo profilu grupy, aby dodać użytkownika do **wybranej** listy.

12. **Opcjonalne:** Jeśli chcesz **dodać więcej niż jedną grupę**, wpisz inną **pełną nazwę grupy** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tę grupę do **wybranej** listy.

13. Po zakończeniu wybierania grup kliknij przycisk **Wybierz** , aby dodać do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **roli wybierz** w okienku **Dodaj przypisanie** , aby wybrać rolę, która ma zostać przypisana do wybranych grup.

15. Kliknij przycisk **Przypisz** , aby przypisać aplikację do wybranych grup.

Jeśli obsługa administracyjna została skonfigurowana i jest już uruchomiona dla aplikacji, w ciągu około 10 minut należy zainicjować obsługę administracyjną w aplikacji nowych użytkowników należących do tej grupy. Szczegółowe informacje znajdują się w **dziennikach inspekcji** .

>[!IMPORTANT]
>Inicjowanie obsługi administracyjnej nazw grup i grup, oprócz członków, jeśli są obsługiwane w przypadku niektórych aplikacji. Tę funkcję można włączyć lub wyłączyć, włączając lub wyłączając **Mapowanie** dla obiektów grupy wyświetlanych na karcie **aprowizacji** . 
>
>

Jeśli włączono grupy aprowizacji, należy sprawdzić mapowania atrybutów, aby upewnić się, że odpowiednie pole jest używane dla "zgodnego identyfikatora". Ten pasujący Identyfikator może być nazwą wyświetlaną lub aliasem adresu e-mail. Grupa i jej elementy członkowskie nie są obsługiwane, jeśli właściwość pasująca jest pusta lub nie została wypełniona dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki
[Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS przy użyciu Azure Active Directory](user-provisioning.md)
