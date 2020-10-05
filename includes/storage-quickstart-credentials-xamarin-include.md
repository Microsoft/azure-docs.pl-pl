---
title: plik dołączania
description: plik dołączania
services: storage
author: codemillmatt
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: masoucou
ms.custom: include file
ms.openlocfilehash: 02586d38903c60ba8982753ca0bd3e15192d5deb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "83006237"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiowanie poświadczeń z witryny Azure Portal

Gdy aplikacja Przykładowa wysyła żądanie do usługi Azure Storage, musi mieć autoryzację. Aby autoryzować żądanie, Dodaj poświadczenia konta magazynu do aplikacji jako parametry połączenia. Wyświetl poświadczenia konta magazynu, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Odszukaj konto magazynu.
3. W sekcji **Ustawienia** omówienia kont magazynu wybierz pozycję **Klucze dostępu**. W tym miejscu możesz przeglądać klucze dostępu do konta oraz pełne parametry połączenia dla każdego klucza.
4. Znajdź wartość **Parametry połączenia** w obszarze **key1** i wybierz przycisk **Kopiuj**, aby skopiować parametry połączenia. W następnym kroku dodasz wartość parametrów połączenia do zmiennej środowiskowej.

    ![Zrzut ekranu pokazujący sposób kopiowania parametrów połączenia z witryny Azure Portal](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

Po skopiowaniu parametrów połączenia Ustaw dla niego zmienną poziom klasy w pliku *MainPage.XAML.cs* . Otwórz *MainPaage.XAML.cs* i Znajdź `storageConnectionString` zmienną. Zamień `<yourconnectionstring>` na rzeczywiste parametry połączenia.

Oto kod:

```csharp
string storageConnectionString = "<yourconnectionstring>";
```