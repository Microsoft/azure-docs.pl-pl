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
ms.openlocfilehash: 3f90edd5729ff5229be09bc3798082c33bdeead2
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632105"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Obsługa uwierzytelniania bezhaseł przez przeglądarkę FIDO2

Azure Active Directory zezwala na używanie [kluczy zabezpieczeń FIDO2](./concept-authentication-passwordless.md#fido2-security-keys) jako urządzenia bezhaseł. Dostępność uwierzytelniania FIDO2 dla kont Microsoft została [ogłoszona w 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Zgodnie z opisem w ogłoszeniu, pewne funkcje opcjonalne i rozszerzenia do specyfikacji FIDO2 CTAP muszą zostać zaimplementowane do obsługi bezpiecznego uwierzytelniania przy użyciu kont firmy Microsoft i Azure Active Directory. Na poniższym diagramie przedstawiono, które przeglądarki i kombinacje systemów operacyjnych obsługują uwierzytelnianie bezhasłem przy użyciu kluczy uwierzytelniania FIDO2 z Azure Active Directory.

## <a name="supported-browsers"></a>Obsługiwane przeglądarki

W tej tabeli przedstawiono obsługę uwierzytelniania Azure Active Directory (Azure AD) i kont Microsoft (MSA). Konta Microsoft są tworzone przez konsumentów dla usług takich jak Xbox, Skype lub Outlook.com. Obsługiwane typy urządzeń obejmują **porty USB**, komunikacja zbliżeniowa (**NFC**) i niewielka energia Bluetooth (**beli**).

| System operacyjny | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | Funkcja NFC | BELI | USB | Funkcja NFC | BELI | USB | Funkcja NFC | BELI |
| **Windows**  | ![Program Chrome obsługuje połączenia USB w systemie Windows dla kont usługi AAD.][y] | ![Program Chrome obsługuje funkcję NFC w systemie Windows dla kont usługi AAD.][y] | ![Program Chrome obsługuje usługi w systemie Windows dla kont usługi AAD.][y] | ![Program Edge obsługuje połączenia USB w systemie Windows dla kont usługi AAD.][y] | ![Program Edge obsługuje funkcję NFC w systemie Windows dla kont usługi AAD.][y] | ![Program Edge obsługuje usługi w systemie Windows dla kont usługi AAD.][y] | ![Firefox obsługuje konta usługi AAD w systemie Windows.][y] | ![Program Firefox obsługuje usługi NFC w systemie Windows dla kont usługi AAD.][y] | ![Firefox obsługuje usługi w systemie Windows dla kont usługi AAD.][y] |
| **macOS**  | ![Program Chrome obsługuje połączenia USB w systemie macOS dla kont usługi AAD.][y] | ![Program Chrome nie obsługuje funkcji NFC w systemie macOS dla kont usługi AAD.][n] | ![Program Chrome nie obsługuje macOS dla kont usługi AAD.][n] | ![Program Edge obsługuje połączenia USB w systemie macOS dla kont usługi AAD.][y] | ![Usługa Edge nie obsługuje funkcji NFC w systemie macOS dla kont usługi AAD.][n] | ![Usługa Edge nie obsługuje macOS na kontach usługi AAD.][n] | ![Program Firefox nie obsługuje portów USB w systemie macOS dla kont usługi AAD.][n] | ![Program Firefox nie obsługuje funkcji NFC w systemie macOS dla kont usługi AAD.][n] | ![Program Firefox nie obsługuje macOS na kontach usługi AAD.][n] |
| **Linux**  | ![Program Chrome obsługuje połączenia USB w systemie Linux dla kont usługi AAD.][y] | ![Program Chrome nie obsługuje funkcji NFC w systemie Linux dla kont usługi AAD.][n] | ![Program Chrome nie obsługuje usługi beli w systemie Linux dla kont usługi AAD.][n] | ![Usługa Edge nie obsługuje portów USB w systemie Linux dla kont usługi AAD.][n] | ![Usługa Edge nie obsługuje funkcji NFC w systemie Linux dla kont usługi AAD.][n] | ![Usługa Edge nie obsługuje usługi beli w systemie Linux dla kont usługi AAD.][n] | ![Program Firefox nie obsługuje portów USB w systemie Linux dla kont usługi AAD.][n] | ![Program Firefox nie obsługuje funkcji NFC w systemie Linux dla kont usługi AAD.][n] | ![Program Firefox nie obsługuje usługi beli w systemie Linux dla kont usługi AAD.][n] |



## <a name="unsupported-browsers"></a>Nieobsługiwane przeglądarki

Następujące kombinacje systemów operacyjnych i przeglądarek nie są obsługiwane, ale trwa badanie w przyszłości. Jeśli chcesz zobaczyć inny system operacyjny i obsługę przeglądarki, wystaw opinię za pomocą narzędzia opinii o produkcie w dolnej części strony.

| System operacyjny | Przeglądarka |
| ---- | ---- |
| iOS | Safari, Brave |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>Minimalna wersja przeglądarki

Poniżej przedstawiono minimalne wymagania dotyczące wersji przeglądarki. 

| Przeglądarka | Minimalna wersja |
| ---- | ---- |
| Chrome | 76 |
| Edge | Windows 10 w wersji 1903<sup>1</sup> |
| Firefox | 66 |

<sup>1</sup> Wszystkie wersje nowych, opartych na chromie Fido2 pomocy technicznej Microsoft Edge. Dodano obsługę programu Microsoft Edge w wersji starszej niż 1903.

## <a name="next-steps"></a>Następne kroki
[Włącz logowanie przy użyciu klucza zabezpieczeń bezhasło (wersja zapoznawcza)](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
