---
title: Dodawanie atrybutów niestandardowych dla przepływów użytkowników usługi Azure AD
description: Dowiedz się więcej o dostosowywaniu atrybutów dla przepływów użytkownika samoobsługowego rejestrowania.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f187cc47d9c64c8257cc097734fa41e10629f1c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597450"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>Definiowanie atrybutów niestandardowych dla przepływów użytkowników (wersja zapoznawcza)
|     |
| --- |
| Funkcja niestandardowych atrybutów użytkownika jest publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.|
|     |

Dla każdej aplikacji mogą istnieć różne wymagania dotyczące informacji, które mają być zbierane podczas tworzenia konta. Usługa Azure AD zawiera wbudowany zestaw informacji przechowywanych w atrybutach, takich jak imię, nazwisko, miejscowość i kod pocztowy. Za pomocą usługi Azure AD można rozciągnąć zestaw atrybutów przechowywanych na koncie gościa, gdy użytkownik zewnętrzny zarejestruje się za pomocą przepływu użytkownika.

Możesz tworzyć niestandardowe atrybuty w Azure Portal i używać ich w przepływach użytkownika samoobsługowego tworzenia konta. Można również odczytywać i zapisywać te atrybuty przy użyciu [interfejsu API Microsoft Graph](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api). Interfejs API Microsoft Graph obsługuje tworzenie i aktualizowanie użytkownika z atrybutami rozszerzenia. Atrybuty rozszerzenia w interfejs API programu Graph są nazwane przy użyciu konwencji `extension_<Application-client-id>_attributename` . Na przykład:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Możesz znaleźć `<Application-client-id>` na stronie **rejestracje aplikacji** obok **identyfikatora aplikacji (klienta)**. Ten identyfikator jest specyficzny dla Twojej dzierżawy.

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

7. Wybierz przycisk **Utwórz**.

Atrybut niestandardowy jest teraz dostępny na liście atrybutów użytkownika i do użycia w przepływach użytkownika. Atrybut niestandardowy jest tworzony tylko za pierwszym razem, gdy jest używany w dowolnym przepływie użytkownika, a nie po dodaniu go do listy atrybutów użytkownika.

Po utworzeniu nowego użytkownika przy użyciu przepływu użytkownika, który używa nowo utworzonego atrybutu niestandardowego, można zbadać obiekt w [eksploratorze Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer). Na liście atrybutów zebranych podczas rejestracji można teraz zobaczyć **ShoeSize** i zobaczyć je w tokenie wysyłanym z powrotem do aplikacji. Aby dowiedzieć się, jak uwzględnić te oświadczenia w tokenie wysyłanym z powrotem do aplikacji, zobacz [Konfigurowanie opcjonalnych oświadczeń rozszerzenia katalogu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-directory-extension-optional-claims)

## <a name="next-steps"></a>Następne kroki

[Dodawanie przepływu użytkownika samoobsługowego rejestrowania do aplikacji](self-service-sign-up-user-flow.md)