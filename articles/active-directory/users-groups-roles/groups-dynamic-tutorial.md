---
title: Dodawanie użytkowników do grupy dynamicznej — samouczek — Azure AD | Microsoft Docs
description: W tym samouczku użyjesz grup z regułami członkostwa użytkownika, aby automatycznie dodawać lub usuwać użytkowników
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 382f3b59142aee7ddfbec4aceb153a174874ac1a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74027104"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Samouczek: automatyczne dodawanie lub usuwanie członków grupy

W usłudze Azure Active Directory (Azure AD) możesz automatycznie dodawać użytkowników do grup zabezpieczeń lub grup usługi Office 365 oraz ich usuwać, dzięki czemu nie musisz zawsze robić tego ręcznie. Po zmianie dowolnych właściwości użytkownika lub urządzenia usługa Azure AD ocenia wszystkie reguły dynamicznego członkostwa w grupach w Twojej dzierżawie, aby sprawdzić, czy zmiana powinna spowodować dodanie lub usunięcie użytkowników.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Utwórz automatycznie wypełnioną grupę użytkowników-Gości z firmy partnerskiej
> * Przypisywanie licencji do grupy w celu umożliwienia użytkownikom-gościom uzyskanie dostępu do funkcji specyficznych dla partnera
> * Dodatek: zabezpieczanie grupy **Wszyscy użytkownicy** przez usunięcie użytkowników-gości, aby na przykład umożliwić członkom organizacji dostęp do witryn przeznaczonych wyłącznie dla użytkowników wewnętrznych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja wymaga jednej licencji Azure AD — wersja Premium jako Administrator globalny dzierżawy. Jeśli go nie masz, w usłudze Azure AD wybierz pozycję **licencje** > **produkty** > **Wypróbuj/Kup**.

Nie musisz przypisywać licencji użytkownikom, aby mogli być członkami grup dynamicznych. W dzierżawie potrzebna jest tylko taka minimalna liczba dostępnych licencji usługi Azure AD — wersja Premium P1, aby objęci byli nimi wszyscy tacy użytkownicy. 

## <a name="create-a-group-of-guest-users"></a>Tworzenie grupy użytkowników-gości

Najpierw należy utworzyć grupę dla użytkowników-gości z jednej firmy partnerskiej. Potrzebują oni specjalnej licencji, a więc często najlepszym rozwiązaniem jest utworzenie grupy do tego celu.

1. Zaloguj się do Azure Portal (https://portal.azure.com) za pomocą konta, które jest administratorem globalnym dla dzierżawy.
2. Wybierz kolejno pozycje **Azure Active Directory** > **grupy** > **Nowa grupa**.
   ![Wybierz polecenie, aby rozpocząć nową grupę](./media/groups-dynamic-tutorial/new-group.png)
3. W bloku **Grupa**:
  
   * Wybierz pozycję **zabezpieczenia** jako typ grupy.
   * Wprowadź `Guest users Contoso` nazwę i opis grupy.
   * Zmień **Typ członkostwa** na **użytkownika dynamicznego**.
   
4. Wybierz pozycję **właściciele** i w bloku **Dodawanie właścicieli** Wyszukaj wszystkich żądanych właścicieli. Kliknij wybranych właścicieli, aby dodać je do zaznaczenia.
5. Kliknij przycisk **Wybierz** , aby zamknąć blok **Dodawanie właścicieli** .  
6. W polu **dynamiczne elementy członkowskie użytkownika** wybierz opcję **Edytuj zapytanie dynamiczne** .
7. W bloku **Reguły członkostwa dynamicznego**:

   * W polu **Właściwość** Kliknij istniejącą wartość, a następnie wybierz pozycję **UserType**. 
   * Upewnij się, że pole **operator** ma **wartość** zaznaczone.  
   * Wybierz pole **wartość** i wprowadź **gościa**. 
   * Kliknij hiperlink **Dodaj wyrażenie** , aby dodać kolejny wiersz.
   * W polu **i/lub** wybierz pozycję **i**.
   * W polu **Właściwość** wybierz pozycję **NazwaFirmy**.
   * Upewnij się, że pole **operator** ma **wartość** zaznaczone.
   * W polu **wartość** wpisz **contoso**.
   * Kliknij przycisk **Zapisz** , aby zamknąć blok **reguły członkostwa dynamicznego** .
   
8. W bloku **Grupy** wybierz polecenie **Utwórz**, aby utworzyć grupę.

## <a name="assign-licenses"></a>Przypisywanie licencji

Po utworzeniu nowej grupy możesz przypisać licencje wymagane dla tych użytkowników partnerów.

1. W usłudze Azure AD wybierz pozycję **Licencje**, aby wybrać co najmniej jedną licencję, a następnie wybierz polecenie **Przypisz**.
2. Wybierz pozycję **Użytkownicy i grupy**, a następnie wybierz grupę **Guest users Contoso** i zapisz zmiany.
3. Pozycja **Opcje przypisania** umożliwia włączanie i wyłączanie planów usług, których licencje wybrano. Po wprowadzeniu zmian kliknij przycisk **OK**, aby je zapisać.
4. Aby zakończyć przypisywanie, w dolnej części okienka **Przypisz licencję** kliknij pozycję **Przypisz**.

## <a name="remove-guests-from-all-users-group"></a>Usuwanie gości z grupy Wszyscy użytkownicy

Możliwe, że Twoim ostatecznym celem jako administratora jest przypisanie wszystkich użytkowników-gości do odpowiednich grup według firmy. Teraz możesz również zmienić grupę **Wszyscy użytkownicy** tak, aby była zarezerwowana tylko dla użytkowników będących członkami Twojej dzierżawy. Dzięki temu możesz używać jej do przypisywania aplikacji i licencji, które są specyficzne dla Twojej organizacji macierzystej.

   ![Zmienianie grupy Wszyscy użytkownicy na grupę wyłącznie dla członków](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

**Aby usunąć grupę użytkowników-gości**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dzierżawy.
2. Wybierz **Azure Active Directory** > **grupy**Azure Active Directory. Wybierz grupę **Guest users Contoso**, wybierz symbol wielokropka (...), a następnie wybierz polecenie **Usuń**. Podczas usuwania grupy zostaną usunięte wszystkie przypisane licencje.

**Aby przywrócić grupę Wszyscy użytkownicy**
1. Wybierz **Azure Active Directory** > **grupy**Azure Active Directory. Wybierz nazwę grupy **Wszyscy użytkownicy**, aby otworzyć grupę.
1. Wybierz pozycję **Reguły członkostwa dynamicznego**, wyczyść tekst w regule, a następnie wybierz polecenie **Zapisz**.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie grupy użytkowników-gości
> * Przypisywanie licencji do nowej grupy
> * Zmienianie grupy Wszyscy użytkownicy na grupę wyłącznie dla członków

Przejdź do kolejnego artykułu, aby dowiedzieć się więcej na temat podstaw licencjonowania opartego na grupach
> [!div class="nextstepaction"]
> [Podstawy licencjonowania opartego na grupach](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



