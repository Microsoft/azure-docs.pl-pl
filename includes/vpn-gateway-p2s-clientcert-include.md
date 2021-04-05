---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: 34986ac80a309bcfd495e5782496ba560f84c5f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041558"
---
Każdy komputer kliencki, który połączysz z siecią wirtualną przy użyciu połączenia punkt-lokacja, musi mieć zainstalowany certyfikat klienta. Należy wygenerować go na podstawie certyfikatu głównego i zainstalować na każdym komputerze klienckim. Jeśli nie zainstalujesz prawidłowego certyfikatu klienta, uwierzytelnianie zakończy się niepowodzeniem, gdy klient spróbuje nawiązać połączenie z siecią wirtualną.

Można wygenerować unikatowy certyfikat dla każdego klienta lub można użyć tego samego certyfikatu dla wielu klientów. Zaletą generowania unikatowych certyfikatów klienta jest możliwość odwołania pojedynczego certyfikatu. W przeciwnym razie, jeśli wielu klientów korzysta z tego samego certyfikatu klienta do uwierzytelniania i odwołasz go, konieczne będzie wygenerowanie i zainstalowanie nowych certyfikatów dla każdego klienta korzystającego z tego certyfikatu.

Certyfikaty klienta można wygenerować, posługując się następującymi metodami:

* **Certyfikat przedsiębiorstwa:**

  * Jeśli używasz rozwiązania z certyfikatem przedsiębiorstwa, wygeneruj certyfikat klienta przy użyciu nazwy pospolitej formatu wartości *nazwa \@ yourdomain.com*. Użyj tego formatu zamiast formatu *nazwa domeny\nazwa użytkownika*.

  * Upewnij się, że certyfikat klienta jest oparty na szablonie certyfikatu użytkownika, którego pierwszym elementem na liście użytkownika jest *Uwierzytelnienie klienta*. Sprawdź certyfikat, dwukrotnie go klikając i wyświetlając obszar **Ulepszone użycie klucza** na karcie **Szczegóły**.

* **Certyfikat główny z podpisem własnym:** Wykonaj kroki opisane w jednym z następujących artykułów certyfikatów P2S, aby utworzone certyfikaty klienta były zgodne z połączeniami P2S.

  Gdy certyfikat klienta jest generowany na podstawie certyfikatu głównego z podpisem własnym, jest on automatycznie instalowany na komputerze użytym do jego wygenerowania. Aby zainstalować certyfikat klienta na innym komputerze klienckim, wyeksportuj go jako plik pfx razem z całym łańcuchem certyfikatów. Spowoduje to utworzenie pliku pfx zawierającego informacje o certyfikacie głównym, który jest wymagany do uwierzytelnienia klienta.

  Kroki opisane w tych artykułach generują zgodny certyfikat klienta, który można następnie wyeksportować i dystrybuować.

  * [Instrukcje programu Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): w przypadku tych instrukcji do generowania certyfikatów wymagany jest system Windows 10 i program PowerShell. Wygenerowane certyfikaty można zainstalować na dowolnym obsługiwanym kliencie typu punkt-lokacja.

  * [Instrukcje MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Użyj MakeCert, jeśli nie masz dostępu do komputera z systemem Windows 10 w celu wygenerowania certyfikatów. Mimo iż narzędzie MakeCert jest przestarzałe, przy jego użyciu można nadal generować certyfikaty. Możesz zainstalować wygenerowane certyfikaty na dowolnym obsługiwanym kliencie typu punkt-lokacja.

  * [Instrukcje dotyczące systemu Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).