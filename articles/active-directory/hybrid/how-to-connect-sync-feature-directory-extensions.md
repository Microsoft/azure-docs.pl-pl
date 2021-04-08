---
title: 'Azure AD Connect Synchronize: rozszerzenia katalogów | Microsoft Docs'
description: W tym temacie opisano funkcję rozszerzeń katalogów w Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d4152783129fa1c5950d6cf6287332bf90d32a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97976881"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect Synchronize: rozszerzenia katalogów
Możesz użyć rozszerzeń katalogów, aby rozszerzać schemat w Azure Active Directory (Azure AD) z własnymi atrybutami z Active Directory lokalnych. Ta funkcja umożliwia tworzenie aplikacji biznesowych przez konsumowanie atrybutów, które nadal są zarządzane lokalnie. Te atrybuty mogą być używane przez [rozszerzenia](/graph/extensibility-overview
). Dostępne atrybuty można wyświetlić za pomocą [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer). Za pomocą tej funkcji można także tworzyć grupy dynamiczne w usłudze Azure AD.

W obecnym przypadku żadne obciążenie Microsoft 365 nie korzysta z tych atrybutów.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Dostosuj atrybuty, które mają być synchronizowane z usługą Azure AD

Można skonfigurować dodatkowe atrybuty, które mają być synchronizowane w ścieżce ustawień niestandardowych w Kreatorze instalacji.

> [!NOTE]
> W Azure AD Connect wersjach wcześniejszych niż 1.2.65.0, w polu wyszukiwania dla **dostępnych atrybutów** jest rozróżniana wielkość liter.

![Kreator rozszerzenia schematu](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Instalacja pokazuje następujące atrybuty, które są prawidłowymi kandydatami:

* Typy obiektów użytkowników i grup
* Atrybuty jednowartościowe: String, Boolean, Integer, Binary
* Atrybuty wielowartościowe: String, Binary


>[!NOTE]
> Po Azure AD Connect zsynchronizowany atrybut Active Directory o wartości wielowartościowej do usługi Azure AD jako rozszerzenie atrybutu wielowartościowego możliwe jest dołączenie atrybutu do żądania SAML. Nie jest jednak możliwe korzystanie z tych danych za poorednictwem wywołania interfejsu API.

Lista atrybutów jest odczytana z pamięci podręcznej schematu, która została utworzona podczas instalacji Azure AD Connect. Jeśli schemat Active Directory został rozszerzony o dodatkowe atrybuty, należy [odświeżyć schemat](how-to-connect-installation-wizard.md#refresh-directory-schema) , zanim te nowe atrybuty są widoczne.

Obiekt w usłudze Azure AD może mieć do 100 atrybutów dla rozszerzeń katalogów. Maksymalna długość to 250 znaków. Jeśli wartość atrybutu jest dłuższa, aparat synchronizacji obcina go.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Zmiany konfiguracji w usłudze Azure AD wykonane przez Kreatora

Podczas instalacji Azure AD Connect aplikacja jest zarejestrowana, w której te atrybuty są dostępne. Ta aplikacja jest widoczna w Azure Portal. Jego nazwa to zawsze **aplikacja rozszerzenia schematu dzierżawy**.

![Aplikacja rozszerzenia schematu](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Upewnij się, że wybrano pozycję **wszystkie aplikacje** , aby wyświetlić tę aplikację.

Atrybuty są poprzedzone **rozszerzeniem {identyfikator \_ aplikacji} \_**. Identyfikator aplikacji ma taką samą wartość dla wszystkich atrybutów w dzierżawie usługi Azure AD. Ta wartość będzie potrzebna dla wszystkich innych scenariuszy w tym temacie.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Wyświetlanie atrybutów przy użyciu interfejsu API Microsoft Graph

Te atrybuty są teraz dostępne za pośrednictwem interfejsu API Microsoft Graph przy użyciu [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> W interfejsie API Microsoft Graph należy poproszony o zwrócenie atrybutów. Jawnie wybierz następujące atrybuty: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division` .
>
> Aby uzyskać więcej informacji, zobacz [Microsoft Graph: Użyj parametrów zapytania](/graph/query-parameters#select-parameter).

>[!NOTE]
> Nie jest obsługiwana synchronizacja wartości atrybutów z AADConnect do atrybutów rozszerzenia, które nie są tworzone przez AADConnect. Wykonanie tej operacji może spowodować problemy z wydajnością i nieoczekiwane wyniki. Tylko atrybuty rozszerzenia, które są tworzone, jak pokazano w powyższym przykładzie, są obsługiwane na potrzeby synchronizacji.

## <a name="use-the-attributes-in-dynamic-groups"></a>Używanie atrybutów w grupach dynamicznych

Jednym z bardziej przydatnych scenariuszy jest użycie tych atrybutów w zabezpieczeniach dynamicznych lub w grupach Microsoft 365.

1. Utwórz nową grupę w usłudze Azure AD. Nadaj mu dobrą nazwę i upewnij się, że **Typ członkostwa** to **użytkownik dynamiczny**.

   ![Zrzut ekranu z nową grupą](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Wybierz, aby **dodać zapytanie dynamiczne**. Jeśli Przyjrzyj się właściwościom, te atrybuty rozszerzone nie będą widoczne. Należy je najpierw dodać. Kliknij przycisk **Pobierz niestandardowe właściwości rozszerzenia**, wprowadź identyfikator aplikacji, a następnie kliknij przycisk **Odśwież właściwości**.

   ![Zrzut ekranu, na którym dodano rozszerzenia katalogu](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Otwórz listę rozwijaną właściwości i zwróć uwagę, że dodane atrybuty są teraz widoczne.

   ![Zrzut ekranu z nowymi atrybutami widocznymi w interfejsie użytkownika](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Wypełnij wyrażenie, aby dostosować je do swoich wymagań. W naszym przykładzie reguła jest ustawiona na **(User.extension_9d98ed114c4840d298fad781915f27e4_division-EQ "Sales and Marketing")**.

4. Po utworzeniu grupy Udziel usługi Azure AD trochę czasu, aby wypełnić członków, a następnie przejrzyj członków.

   ![Zrzut ekranu z elementami członkowskimi w grupie dynamicznej](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji Azure AD Connect](how-to-connect-sync-whatis.md) .

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
