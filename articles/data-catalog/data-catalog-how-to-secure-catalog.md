---
title: Jak zabezpieczyć dostęp do Azure Data Catalog
description: W tym artykule opisano sposób zabezpieczania wykazu danych i jego zasobów danych w programie Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 537254d7fb153e182be8ece13675a357a9a3e24e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674822"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Jak zabezpieczyć dostęp do wykazu danych i zasobów danych

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

> [!IMPORTANT]
> Ta funkcja jest dostępna tylko w wersji Standard programu Azure Data Catalog.

Azure Data Catalog pozwala określić, kto może uzyskiwać dostęp do wykazu danych i jakie operacje (rejestrować, dodawać adnotacje, przejąć własność), które mogą być wykonywane na metadanych w wykazie. 

## <a name="catalog-users-and-permissions"></a>Użytkownicy i uprawnienia katalogu

Aby nadać użytkownikowi lub grupie dostęp do wykazu danych i ustawić uprawnienia:

1. Na [stronie głównej wykazu danych](https://www.azuredatacatalog.com)kliknij pozycję **Ustawienia** na pasku narzędzi.

   ![Przycisk ustawień strony głównej Azure Data Catalog](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Na stronie Ustawienia rozwiń sekcję **Użytkownicy katalogu** .

   ![Przycisk Dodaj Azure Data Catalog użytkowników](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Kliknij pozycję **Dodaj**.

4. Wprowadź w pełni kwalifikowaną **nazwę użytkownika** lub nazwę **grupy zabezpieczeń** w Azure Active Directory (AAD) skojarzonej z wykazem. Jeśli dodajesz więcej niż jednego użytkownika lub grupę, użyj przecinka (",") jako separatora.

   ![Azure Data Catalog użytkownicy lub grupy](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Naciśnij klawisz **Enter** lub **Tab** z pola tekstowego. 

6. Upewnij się, że wszystkie uprawnienia (**Dodawanie adnotacji**, **Rejestrowanie** i **przejęcie na własność**) są domyślnie przypisane do tych użytkowników lub grup. Oznacza to, że użytkownik lub Grupa może [rejestrować zasoby danych]( data-catalog-how-to-register.md), [dodawać adnotacje do zasobów danych]( data-catalog-how-to-annotate.md)i [przejąć własność zasobów danych]( data-catalog-how-to-manage.md). 

   ![Azure Data Catalog użytkownicy — uprawnienia domyślne](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Aby nadać użytkownikowi lub grupie tylko dostęp do odczytu do wykazu, wyczyść opcję **Adnotuj** dla tego użytkownika lub grupy. Gdy to zrobisz, użytkownik lub grupa nie będzie mógł dodawać adnotacji do zasobów danych w wykazie, ale mogą je przeglądać. 

8. Aby odmówić, aby użytkownik lub Grupa rejestrował zasoby danych, wyczyść opcję **zarejestruj** dla tego użytkownika lub grupy.

9. Aby uniemożliwić użytkownikowi przejęcie własności zasobu danych, wyczyść opcję **Przejmij własność** dla tego użytkownika lub grupy. 

10. Aby usunąć użytkownika/grupę z użytkowników wykazu, kliknij przycisk **x** dla użytkownika/grupy w dolnej części listy. 

   ![Użytkownicy katalogu Azure Data Catalog — Usuwanie ikony użytkownika X](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Zalecamy dodanie grup zabezpieczeń do użytkowników wykazu zamiast bezpośredniego dodawania użytkowników i przypisywania uprawnień. Następnie Dodaj użytkowników do grup zabezpieczeń, które pasują do ich ról i wymagane przez nich dostęp do wykazu.

## <a name="special-considerations"></a>Uwagi specjalne

- Uprawnienia przypisane do grup zabezpieczeń to dodatki. Załóżmy, że użytkownik należy do dwóch grup. Jedna grupa ma uprawnienia do dodawania adnotacji, a inna grupa nie ma uprawnień do dodawania adnotacji. Następnie użytkownik ma uprawnienia do dodawania adnotacji. 
- Uprawnienia przypisane jawnie do użytkownika zastępują uprawnienia przypisane do grup, do których należy użytkownik. W poprzednim przykładzie Załóżmy, że użytkownik został jawnie dodany do katalogu użytkowników wykazu i nie przypisał uprawnień do dodawania adnotacji. Użytkownik nie może dodawać adnotacji do zasobów danych nawet wtedy, gdy użytkownik jest członkiem grupy, która ma uprawnienia do dodawania adnotacji.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md)
