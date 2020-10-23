---
title: Dodawanie niestandardowych atrybutów do samoobsługowego przepływu rejestracji — Azure AD
description: Dowiedz się więcej o dostosowywaniu atrybutów dla przepływów użytkownika samoobsługowego rejestrowania.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e64ab70fed13d4ca907b2bfb3aa448acdedc39e9
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441457"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>Definiowanie atrybutów niestandardowych dla przepływów użytkowników (wersja zapoznawcza)

> [!NOTE]
> Funkcja niestandardowych atrybutów użytkownika jest publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Dla każdej aplikacji mogą istnieć różne wymagania dotyczące informacji, które mają być zbierane podczas tworzenia konta. Usługa Azure AD zawiera wbudowany zestaw informacji przechowywanych w atrybutach, takich jak imię, nazwisko, miejscowość i kod pocztowy. Za pomocą usługi Azure AD można rozciągnąć zestaw atrybutów przechowywanych na koncie gościa, gdy użytkownik zewnętrzny zarejestruje się za pomocą przepływu użytkownika.

Możesz tworzyć niestandardowe atrybuty w Azure Portal i używać ich w przepływach użytkownika samoobsługowego tworzenia konta. Można również odczytywać i zapisywać te atrybuty przy użyciu [interfejsu API Microsoft Graph](../../active-directory-b2c/manage-user-accounts-graph-api.md). Interfejs API Microsoft Graph obsługuje tworzenie i aktualizowanie użytkownika z atrybutami rozszerzenia. Atrybuty rozszerzenia w interfejs API programu Graph są nazwane przy użyciu konwencji `extension_<extensions-app-id>_attributename` . Na przykład:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

`<extensions-app-id>`Jest on specyficzny dla Twojej dzierżawy. Aby znaleźć ten identyfikator, przejdź do Azure Active Directory > Rejestracje aplikacji > wszystkie aplikacje. Wyszukaj aplikację rozpoczynającą się od "AAD-Extensions-App" i wybierz ją. Na stronie Przegląd aplikacji Zanotuj identyfikator aplikacji (klienta).

## <a name="create-a-custom-attribute"></a>Tworzenie atrybutu niestandardowego

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi Azure AD.
2. W obszarze **usługi platformy Azure**wybierz pozycję **Azure Active Directory**.
3. W menu po lewej stronie wybierz pozycję **tożsamości zewnętrzne**.
4. Wybierz **niestandardowe atrybuty użytkownika (wersja zapoznawcza)**. Zostaną wyświetlone dostępne atrybuty użytkownika.

   ![Wybierz atrybuty użytkownika do rejestracji](media/user-flow-add-custom-attributes/user-attributes.png)

5. Aby dodać atrybut, wybierz pozycję **Dodaj**.
6. W okienku **Dodaj atrybut** wprowadź następujące wartości:

   - **Nazwa** — Podaj nazwę atrybutu niestandardowego (na przykład "Shoesize").
   - **Typ danych** — wybierz typ danych (**ciąg**, **Boolean**lub **int**).
   - **Opis** — opcjonalnie wprowadź opis atrybutu niestandardowego do użytku wewnętrznego. Ten opis nie jest widoczny dla użytkownika.

   ![Dodawanie atrybutu](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Wybierz pozycję **Utwórz**.

Atrybut niestandardowy jest teraz dostępny na liście atrybutów użytkownika i do użycia w przepływach użytkownika. Atrybut niestandardowy jest tworzony tylko za pierwszym razem, gdy jest używany w dowolnym przepływie użytkownika, a nie po dodaniu go do listy atrybutów użytkownika.

Po utworzeniu nowego użytkownika przy użyciu przepływu użytkownika, który używa nowo utworzonego atrybutu niestandardowego, można zbadać obiekt w [eksploratorze Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer). Na liście atrybutów zebranych w trakcie rejestracji na obiekcie użytkownika powinna być teraz widoczna **ShoeSize** . Możesz wywołać interfejs API programu Graph z aplikacji, aby pobrać dane z tego atrybutu po dodaniu go do obiektu użytkownika.

## <a name="next-steps"></a>Następne kroki

[Dodawanie przepływu użytkownika samoobsługowego rejestrowania do aplikacji](self-service-sign-up-user-flow.md)