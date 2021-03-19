---
title: Wymagaj uwierzytelniania wieloskładnikowego z niezaufanych sieci — Azure Active Directory
description: Dowiedz się, jak skonfigurować zasady dostępu warunkowego w usłudze Azure Active Directory (Azure AD) na potrzeby prób dostępu z niezaufanych sieci.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c020a9be7683bf045dbcc747dad3cb45058dd7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077680"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Instrukcje: wymaganie uwierzytelniania MFA w celu uzyskania dostępu z niezaufanych sieci z dostępem warunkowym   

Usługa Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca. Użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze, nie tylko z sieci organizacji, ale również z niezaufanej lokalizacji w Internecie. Typowym najlepszym rozwiązaniem w przypadku dostępu z niezaufanych sieci jest wymaganie uwierzytelniania wieloskładnikowego (MFA).

Ten artykuł zawiera informacje potrzebne do skonfigurowania zasad dostępu warunkowego, które wymagają uwierzytelniania wieloskładnikowego na potrzeby dostępu z niezaufanych sieci. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz [podstawowe pojęcia związane](overview.md) z dostępem warunkowym. 

## <a name="scenario-description"></a>Opis scenariusza

Aby określić równowagę między zabezpieczeniami i produktywnością, może być wystarczające, aby tylko hasło logowania z sieci organizacji było wymagane. Jednak w przypadku dostępu z niezaufanej lokalizacji sieciowej istnieje większe ryzyko, że logowania nie są wykonywane przez uprawnionych użytkowników. Aby rozwiązać ten problem, można zablokować dostęp z niezaufanych sieci. Alternatywnie można również wymagać uwierzytelniania wieloskładnikowego (MFA), aby uzyskać dodatkową gwarancję, że próba została podjęta przez uprawniony właściciel konta. 

Za pomocą dostępu warunkowego usługi Azure AD można rozwiązać ten wymóg przy użyciu jednej zasady, która udziela dostępu: 

- Do wybranych aplikacji w chmurze
- Dla wybranych użytkowników i grup  
- Wymaganie uwierzytelniania wieloskładnikowego 
- Gdy dostęp pochodzi z: 
   - Lokalizacja, która nie jest zaufana

## <a name="implementation"></a>Implementacja

Wyzwaniem tego scenariusza jest przetłumaczenie *dostępu z niezaufanej lokalizacji sieciowej* na warunek dostępu warunkowego. W zasadach dostępu warunkowego można skonfigurować [warunek lokalizacji](location-condition.md) , aby zająć się scenariuszami związanymi z lokalizacjami sieciowymi. Warunek lokalizacji umożliwia wybranie nazwanych lokalizacji, które są logicznymi grupami zakresów adresów IP, krajów i regionów.  

Zazwyczaj organizacja jest właścicielem co najmniej jednego zakresu adresów, na przykład 199.30.16.0-199.30.16.15.
Nazwaną lokalizację można skonfigurować przez:

- Określanie tego zakresu (199.30.16.0/28) 
- Przypisywanie nazwy opisowej, takiej jak **Sieć firmowa** 

Zamiast próbować definiować, które lokalizacje nie są zaufane, możesz:

- Uwzględnij dowolną lokalizację 

   :::image type="content" source="./media/untrusted-networks/02.png" alt-text="Zrzut ekranu okienka lokalizacji platformy Azure A D z opcją Skonfiguruj ustawioną na tak, widoczną kartę Dołącz oraz wybraną i wyróżnioną opcją dowolna lokalizacja." border="false":::

- Wyklucz wszystkie Zaufane lokalizacje 

   :::image type="content" source="./media/untrusted-networks/01.png" alt-text="Zrzut ekranu okienka lokalizacji platformy Azure A D, z ustawieniem ustaw wartość tak, widoczną kartę wykluczanie i wybraną opcją wszystkie Zaufane lokalizacje." border="false":::

## <a name="policy-deployment"></a>Wdrażanie zasad

Z podejściem opisanym w tym artykule można teraz skonfigurować zasady dostępu warunkowego dla niezaufanych lokalizacji. Aby upewnić się, że zasady działają zgodnie z oczekiwaniami, zalecanym najlepszym rozwiązaniem jest przetestowanie go przed wycofaniem do produkcji. Najlepiej użyć dzierżawy testowej, aby sprawdzić, czy nowe zasady działają zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [co to jest dostęp warunkowy w Azure Active Directory?](./overview.md)
