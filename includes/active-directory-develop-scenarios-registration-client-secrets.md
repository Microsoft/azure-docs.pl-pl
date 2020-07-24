---
title: Plik dyrektywy include
description: Dołączanie pliku do poufnych informacji scenariusza klienta strony docelowe (demon, aplikacja sieci Web, interfejs API sieci Web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102876"
---
## <a name="register-secrets-or-certificates"></a>Rejestrowanie wpisów tajnych lub certyfikatów

Podobnie jak w przypadku każdej poufnej aplikacji klienckiej, należy zarejestrować klucz tajny lub certyfikat. Wpisy tajne aplikacji można zarejestrować za pomocą interakcyjnego środowiska w [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) lub przy użyciu narzędzi wiersza polecenia (na przykład programu PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Rejestrowanie kluczy tajnych klienta przy użyciu portalu rejestracji aplikacji

Zarządzanie poświadczeniami klienta odbywa się na stronie **certyfikaty & wpisy tajne** aplikacji:

![Strona certyfikatów & Secret](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- *Wpis tajny klienta* można utworzyć, wybierając pozycję **Nowy wpis tajny klienta** w rejestracji aplikacji w Azure Portal. Podczas tworzenia klucza tajnego klienta _należy_ zarejestrować ciąg tajny przed przejściem do okienka **Certyfikaty &** wpisy tajne. Ciąg tajny nigdy nie jest ponownie wyświetlany.
- Podczas rejestracji aplikacji należy użyć przycisku **Przekaż certyfikat** w celu przekazania certyfikatu. Usługa Azure AD obsługuje tylko certyfikaty, które są bezpośrednio zarejestrowane w aplikacji i nie obserwują łańcuchów certyfikatów.

Aby uzyskać szczegółowe informacje, zobacz [Szybki Start: Konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do interfejsów API sieci Web | Dodaj poświadczenia do aplikacji](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).

### <a name="register-client-secrets-by-using-powershell"></a>Rejestrowanie wpisów tajnych klienta przy użyciu programu PowerShell

Alternatywnie możesz zarejestrować swoją aplikację w usłudze Azure AD przy użyciu narzędzi wiersza polecenia. Przykład " [Active-Directory-dotnetcore-demo-v2"](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) pokazuje, jak zarejestrować klucz tajny aplikacji lub certyfikat z aplikacją usługi Azure AD:

- Aby uzyskać szczegółowe informacje na temat rejestrowania wpisu tajnego aplikacji, zobacz [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Aby uzyskać szczegółowe informacje na temat rejestrowania certyfikatu w aplikacji, zobacz [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
