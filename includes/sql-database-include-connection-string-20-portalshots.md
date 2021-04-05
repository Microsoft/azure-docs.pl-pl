---
title: Uzyskaj parametry połączenia z Azure Portal
description: Uzyskaj parametry połączenia z Azure Portal
keywords: połączenie SQL, parametry połączenia
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: b06004143d4a1ec5a2808e21b1f78833e410c195
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95553415"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Uzyskaj parametry połączenia z Azure Portal
Użyj [Azure Portal](https://portal.azure.com/) , aby uzyskać parametry połączenia, które są niezbędne do współpracy programu klienckiego z Azure SQL Database.

1. Wybierz pozycję **wszystkie usługi**  >  **bazy danych SQL**.

2. Wprowadź nazwę bazy danych w polu tekstowym filtr w lewym górnym rogu bloku **baz danych SQL** .

3. Wybierz wiersz dla bazy danych.

4. Gdy zostanie wyświetlony blok dla bazy danych, wygoda wizualizacji wybierz przyciski **Minimalizuj** , aby zwinąć bloki używane do przeglądania i filtrowania baz danych.

5. W bloku bazy danych wybierz pozycję **Pokaż parametry połączenia bazy danych**.

6. Skopiuj odpowiednie parametry połączenia. na przykład jeśli zamierzasz użyć biblioteki połączeń ADO.NET, skopiuj odpowiedni ciąg z karty **ADO.NET** .

    ![Kopiowanie parametrów połączenia ADO dla bazy danych][20-CopyAdoConnectionString]

7. Edytuj parametry połączenia zgodnie z wymaganiami. oznacza to, że można wstawić hasło do parametrów połączenia lub usunąć "@ &lt; servername &gt; " z nazwy użytkownika, jeśli nazwa użytkownika lub serwera jest zbyt długa.

8. W jednym formacie lub innym, wklej informacje o parametrach połączenia do kodu programu klienckiego.

Aby uzyskać więcej informacji, zobacz [parametry połączeń i pliki konfiguracji](/dotnet/framework/data/adonet/connection-strings-and-configuration-files).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->