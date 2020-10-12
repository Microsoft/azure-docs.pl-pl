---
title: Zainstaluj Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL | Microsoft Docs
description: W tym temacie opisano aktualizację Azure AD Connect, która umożliwia instalację przy użyciu konta, które ma tylko uprawnienia właściciela bazy danych SQL.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f082ec896bf0542b63c8c1d0257679681334050
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85358671"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalowanie programu Azure AD Connect za pomocą delegowanych uprawnień administratora usługi SQL
Przed najnowszą Azure AD Connect kompilacją administracyjną, podczas wdrażania konfiguracji wymaganych przez program SQL nie jest obsługiwany.  Użytkownicy, którzy chcą zainstalować Azure AD Connect potrzebują uprawnień administratora serwera na serwerze SQL Server.

W najnowszej wersji Azure AD Connect aprowizacji bazy danych można teraz wykonać poza pasmem przez administratora SQL, a następnie zainstalować go przez administratora Azure AD Connect z prawami właściciela bazy danych.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby skorzystać z tej funkcji, należy pamiętać, że istnieje kilka ruchomych części, a każdy z nich może mieć innego administratora w organizacji.  Poniższa tabela zawiera podsumowanie poszczególnych ról i ich obowiązków związanych z wdrażaniem Azure AD Connect za pomocą tej funkcji.

|Rola|Opis|
|-----|-----|
|Administrator domeny lub lasu usługi AD|Tworzy konto usługi na poziomie domeny, które jest używane przez Azure AD Connect do uruchamiania usługi synchronizacji.  Aby uzyskać więcej informacji o kontach usług, zobacz [konta i uprawnienia](reference-connect-accounts-permissions.md).
|Administrator SQL|Tworzy bazę danych ADSync i przyznaje uprawnienia dostępu logowanie i dbo do administratora Azure AD Connect oraz konta usługi utworzonego przez administratora domeny/lasu.|
Azure AD Connect administrator|Instaluje Azure AD Connect i określa konto usługi podczas instalacji niestandardowej.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Procedura instalowania Azure AD Connect przy użyciu delegowanych uprawnień SQL
Aby udostępnić bazę danych poza pasmem i zainstalować Azure AD Connect z uprawnieniami właściciela bazy danych, wykonaj następujące czynności.

>[!NOTE]
>Chociaż nie jest to wymagane, **zdecydowanie zaleca** się wybranie sortowania Latin1_General_CI_AS podczas tworzenia bazy danych.


1. Administrator SQL powinien utworzyć bazę danych ADSync przy użyciu sekwencji sortowania bez uwzględniania wielkości liter **(Latin1_General_CI_AS)**.  Baza danych musi mieć nazwę **ADSync**.  W przypadku zainstalowania Azure AD Connect model odzyskiwania, poziom zgodności i typ zawierania są aktualizowane do odpowiednich wartości.  Jednak sekwencja sortowania musi być ustawiona poprawnie przez administratora SQL, w przeciwnym razie Azure AD Connect będzie blokować instalację.  Aby odzyskać skojarzenia zabezpieczeń, należy usunąć i ponownie utworzyć bazę danych.
 
   ![Sortowanie](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Przyznaj administratorowi Azure AD Connect i kontu usługi domeny następujące uprawnienia:
   - Identyfikator logowania SQL 
   - prawa **właściciela bazy danych (dbo)** .
 
   ![Uprawnienia](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect nie obsługuje nazw logowania z członkostwem zagnieżdżonym.  Oznacza to, że konto administratora Azure AD Connect i konto usługi domeny muszą być połączone z nazwą logowania, która ma przyznane prawa dbo.  Po prostu nie może być członkiem grupy, która jest przypisana do nazwy logowania z prawami właściciela bazy danych.

3. Wyślij wiadomość e-mail do administratora Azure AD Connect, wskazując serwer SQL i nazwę wystąpienia, które mają być używane podczas instalowania Azure AD Connect.

## <a name="additional-information"></a>Dodatkowe informacje
Po zainicjowaniu obsługi bazy danych administrator Azure AD Connect może instalować i konfigurować synchronizację lokalną w ich wygodie.

Jeśli administrator SQL został przywrócony z poprzedniej Azure AD Connect kopii zapasowej, należy zainstalować nowy serwer Azure AD Connect przy użyciu istniejącej bazy danych. Aby uzyskać więcej informacji na temat instalowania Azure AD Connect z istniejącą bazą danych, zobacz [instalowanie Azure AD Connect przy użyciu istniejącej bazy danych ADSync](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Następne kroki
- [Rozpoczynanie pracy z programem Azure AD Connect przy użyciu ustawień ekspresowych](how-to-connect-install-express.md)
- [Niestandardowa instalacja programu Azure AD Connect](how-to-connect-install-custom.md)
- [Instalowanie programu Azure AD Connect przy użyciu istniejącej bazy danych programu ADSync](how-to-connect-install-existing-database.md)  
