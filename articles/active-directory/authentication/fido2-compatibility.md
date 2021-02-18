---
title: Obsługa uwierzytelniania bezhaseł przez przeglądarkę FIDO2 | Azure Active Directory
description: Przeglądarki i kombinacje systemów operacyjnych obsługują uwierzytelnianie bezFIDO2owe w aplikacjach przy użyciu Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ed99338a10eb226823c4bd4857d812038ff632
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094505"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Obsługa uwierzytelniania bezhaseł przez przeglądarkę FIDO2

Azure Active Directory zezwala na używanie [kluczy zabezpieczeń FIDO2](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#fido2-security-keys) jako urządzenia bezhaseł. Dostępność uwierzytelniania FIDO2 dla kont Microsoft została [ogłoszona w 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Zgodnie z opisem w ogłoszeniu należy zaimplementować pewne opcjonalne funkcje i rozszerzenia dla specyfikacji FIDO2 CTAP, aby obsługiwały bezpieczne uwierzytelnianie przy użyciu kont firmy Microsoft i Azure Active Directory. Na poniższym diagramie przedstawiono, które przeglądarki i kombinacje systemów operacyjnych obsługują uwierzytelnianie bezhasłem przy użyciu kluczy uwierzytelniania FIDO2 z Azure Active Directory.

## <a name="supported-browsers"></a>Obsługiwane przeglądarki

W tej tabeli przedstawiono obsługę uwierzytelniania Azure Active Directory (Azure AD) i kont Microsoft (MSA). Konta Microsoft są tworzone przez konsumentów dla usług takich jak Xbox, Skype lub Outlook.com. Obsługiwane typy urządzeń obejmują **porty USB**, komunikacja zbliżeniowa (**NFC**) i niewielka energia Bluetooth (**beli**).

|  | Chrome |  |  | Edge |  |  | Firefox |  |  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | Funkcja NFC | BELI | USB | Funkcja NFC | BELI | USB | Funkcja NFC | BELI |
| **Windows**  | ![Program Chrome obsługuje połączenia USB w systemie Windows dla kont usługi AAD.][y] | ![Program Chrome obsługuje funkcję NFC w systemie Windows dla kont usługi AAD.][y] | ![Program Chrome obsługuje usługi w systemie Windows dla kont usługi AAD.][y] | ![Program Edge obsługuje połączenia USB w systemie Windows dla kont usługi AAD.][y] | ![Program Edge obsługuje funkcję NFC w systemie Windows dla kont usługi AAD.][y] | ![Program Edge obsługuje usługi w systemie Windows dla kont usługi AAD.][y] | ![Firefox obsługuje konta usługi AAD w systemie Windows.][y] | ![Program Firefox obsługuje usługi NFC w systemie Windows dla kont usługi AAD.][y] | ![Firefox obsługuje usługi w systemie Windows dla kont usługi AAD.][y] |
| **macOS**  | ![Program Chrome obsługuje połączenia USB w systemie macOS dla kont usługi AAD.][y] | ![Program Chrome nie obsługuje funkcji NFC w systemie macOS dla kont usługi AAD.][n] | ![Program Chrome nie obsługuje macOS dla kont usługi AAD.][n] | ![Program Edge obsługuje połączenia USB w systemie macOS dla kont usługi AAD.][y] | ![Usługa Edge nie obsługuje funkcji NFC w systemie macOS dla kont usługi AAD.][n] | ![Usługa Edge nie obsługuje macOS na kontach usługi AAD.][n] | ![Program Firefox nie obsługuje portów USB w systemie macOS dla kont usługi AAD.][n] | ![Program Firefox nie obsługuje funkcji NFC w systemie macOS dla kont usługi AAD.][n] | ![Program Firefox nie obsługuje macOS na kontach usługi AAD.][n] |
| **Linux**  | ![Program Chrome obsługuje połączenia USB w systemie Linux dla kont usługi AAD.][y] | ![Program Chrome nie obsługuje funkcji NFC w systemie Linux dla kont usługi AAD.][n] | ![Program Chrome nie obsługuje usługi beli w systemie Linux dla kont usługi AAD.][n] | ![Usługa Edge nie obsługuje portów USB w systemie Linux dla kont usługi AAD.][n] | ![Usługa Edge nie obsługuje funkcji NFC w systemie Linux dla kont usługi AAD.][n] | ![Usługa Edge nie obsługuje usługi beli w systemie Linux dla kont usługi AAD.][n] | ![Program Firefox nie obsługuje portów USB w systemie Linux dla kont usługi AAD.][n] | ![Program Firefox nie obsługuje funkcji NFC w systemie Linux dla kont usługi AAD.][n] | ![Program Firefox nie obsługuje usługi beli w systemie Linux dla kont usługi AAD.][n] |

## <a name="unsupported-browsers"></a>Nieobsługiwane przeglądarki

Następujące kombinacje systemów operacyjnych i przeglądarek nie są obsługiwane, ale trwa badanie w przyszłości. Jeśli chcesz zobaczyć dodatkową pomoc techniczną dla systemu operacyjnego i przeglądarki, wystaw opinię za pomocą narzędzia opinii o produkcie w dolnej części strony.

| System operacyjny | Przeglądarka |
| ---- | ---- |
| iOS | Safari, Brave |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="operating-system-versions-tested"></a>Przetestowane wersje systemu operacyjnego

Informacje w powyższej tabeli zostały przetestowane dla następujących wersji systemu operacyjnego.

| System operacyjny | Najnowsza przetestowana wersja |
| --- | --- |
| Windows | 20H2 systemu Windows 10 |
| macOS | OS X 11 Big Sur |
| Linux | Stacja robocza Fedora 32 |

## <a name="next-steps"></a>Następne kroki
[Włącz logowanie przy użyciu klucza zabezpieczeń bezhasło (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
