---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67183090"
---
Jeśli występują problemy z połączeniem, sprawdź następujące elementy:

- Jeśli wyeksportowano certyfikat klienta przy użyciu **Kreatora eksportu certyfikatów**, upewnij się, że został on wyeksportowany jako plik pfx i wybrano opcję **Jeśli jest to możliwe, dołącz wszystkie certyfikaty ze ścieżki certyfikacji**. Podczas eksportowania z użyciem tej wartości eksportowane są również informacje o certyfikacie głównym. Po zainstalowaniu certyfikatu na komputerze klienckim instalowany jest również certyfikat główny w pliku pfx. Aby sprawdzić, czy certyfikat główny został zainstalowany, otwórz pozycję **Zarządzaj certyfikatami użytkowników** i wybierz pozycję **Zaufane główne urzędy certyfikacji\Certyfikaty**. Sprawdź, czy na liście znajduje się certyfikat główny, który musi być obecny, aby uwierzytelnianie działało.

- Jeśli użyto certyfikatu wydanego przez rozwiązanie dla przedsiębiorstwa proponowane przez urząd certyfikacji i nie możesz przeprowadzić uwierzytelnienia, sprawdź kolejność uwierzytelniania w certyfikacie klienta. Sprawdź kolejność elementów na liście uwierzytelniania, dwukrotnie klikając certyfikat klienta, wybierając kartę **Szczegóły**, a następnie wybierając pozycję **Ulepszone użycie klucza**. Upewnij się, że pierwszym elementem na liście jest *Uwierzytelnienie klienta*. Jeśli tak nie jest, certyfikat klienta należy wydać na podstawie szablonu użytkownika mającego *Uwierzytelnienie klienta* jako pierwszy element na liście.

- Aby uzyskać dodatkowe informacje dotyczące rozwiązywania problemów z połączeniami typu punkt-lokacja, zobacz [Troubleshoot P2S connections (Rozwiązywanie problemów dotyczących połączeń typu punkt-lokacja)](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).