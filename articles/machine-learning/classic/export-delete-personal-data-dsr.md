---
title: 'ML Studio (klasyczny): Eksportuj & usuwanie danych — Azure'
description: Dane w produkcie przechowywane przez Azure Machine Learning Studio (klasyczne) są dostępne do eksportowania i usuwania za pomocą Azure Portal a także za pomocą uwierzytelnionych interfejsów API REST. Dostęp do danych telemetrycznych można uzyskać za pomocą portalu ochrony prywatności systemu Azure. W tym artykule pokazano, jak to zrobić.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: b2d797cc6cb45a76a61a235e7c4c66ac6f243580
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519868"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Eksportuj i usuwaj dane użytkownika w ramach produktu z Azure Machine Learning Studio (klasyczne)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  




Można usuwać lub eksportować dane w produkcie przechowywane przez Azure Machine Learning Studio (klasyczne) przy użyciu Azure Portal, programu Studio (klasycznego) interfejsu, programu PowerShell i uwierzytelnionych interfejsów API REST. Ten artykuł zawiera informacje o tym, jak to zrobić. 

Dostęp do danych telemetrycznych można uzyskać za pomocą portalu ochrony prywatności systemu Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Jakie rodzaje danych użytkownika są zbierane dla programu Studio (klasyczne)?

W przypadku tej usługi dane użytkownika składają się z informacji dotyczących użytkowników uprawnionych do uzyskiwania dostępu do obszarów roboczych i rekordów telemetrii interakcji użytkowników z usługą.

Istnieją dwa rodzaje danych użytkownika w Machine Learning Studio (klasyczny):
- **Dane konta osobistego:** Identyfikatory kont i adresy e-mail skojarzone z kontem.
- **Dane klienta:** Dane, które zostały przekazane do analizy.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Typy kont Studio (klasyczne) i sposób przechowywania danych

Istnieją trzy rodzaje kont w Machine Learning Studio (klasyczne). Rodzaj konta decyduje o sposobie przechowywania danych i sposobach ich usuwania lub eksportowania.

- **Obszar roboczy gościa** to bezpłatne, anonimowe konto. Zarejestruj się bez podawania poświadczeń, takich jak adres e-mail lub hasło.
    -  Dane są przeczyszczane po wygaśnięciu obszaru roboczego gościa.
    - Użytkownicy-Goście mogą eksportować dane klientów za pomocą interfejsu użytkownika, interfejsów API REST lub pakietu programu PowerShell.
- **Bezpłatny obszar roboczy** to bezpłatne konto, za pomocą którego logujesz się przy użyciu poświadczeń konto Microsoft — adresu e-mail i hasła.
    - Możesz eksportować i usuwać dane osobowe i klienta, które podlegają żądania praw podmiotu danych (DSR).
    - Dane klientów można eksportować za pomocą interfejsu użytkownika, interfejsów API REST lub pakietu programu PowerShell.
    - W przypadku bezpłatnych obszarów roboczych, które nie korzystają z kont usługi Azure AD, dane telemetryczne można eksportować za pomocą portalu ochrony prywatności.
    - Po usunięciu obszaru roboczego można usunąć wszystkie osobiste dane klientów.
- **Obszar roboczy w warstwie Standardowa** to płatne konto, do którego uzyskuje się dostęp przy użyciu poświadczeń logowania.
    - Możesz eksportować i usuwać dane Personal i klienta, które podlegają żądaniami DSR.
    - Dostęp do danych można uzyskać za pomocą portalu ochrony prywatności systemu Azure
    - Dane Personal i Customer można eksportować za pomocą interfejsu użytkownika, interfejsów API REST lub pakietu programu PowerShell.
    - Dane można usunąć w Azure Portal.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Usuwanie danych obszaru roboczego w programie Studio (klasyczne) 

### <a name="delete-individual-assets"></a>Usuwanie pojedynczych zasobów

Użytkownicy mogą usuwać zasoby w obszarze roboczym, zaznaczając je, a następnie wybierając przycisk Usuń.

![Usuwanie zasobów w Machine Learning Studio (klasyczny)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Usuwanie całego obszaru roboczego

Użytkownicy mogą również usunąć całe obszary robocze:
- Płatny obszar roboczy: usuwanie przez Azure Portal.
- Bezpłatny obszar roboczy: Użyj przycisku Usuń w okienku **Ustawienia** .

![Usuń bezpłatny obszar roboczy w Machine Learning Studio (klasyczny)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Eksportowanie danych z programu Studio (klasycznego) przy użyciu środowiska PowerShell
Użyj programu PowerShell, aby wyeksportować wszystkie informacje do przenośnego formatu z Azure Machine Learning Studio (klasycznego) przy użyciu poleceń. Aby uzyskać więcej informacji, zobacz artykuł [dotyczący modułu programu PowerShell dla Azure Machine Learning Studio (klasyczny)](powershell-module.md) .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dokumentację obejmującą usługi sieci Web i rozliczenia planu zobowiązań, zobacz [Azure Machine Learning Studio (klasyczny) Dokumentacja interfejsu API REST](/rest/api/machinelearning/).