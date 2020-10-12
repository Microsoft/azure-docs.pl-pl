---
title: plik dołączany
description: Dołączanie pliku do poufnych informacji scenariusza klienta strony docelowe (demon, aplikacja sieci Web, interfejs API sieci Web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89436475"
---
## <a name="add-a-client-secret-or-certificate"></a>Dodawanie klucza tajnego klienta lub certyfikatu

Podobnie jak w przypadku każdej poufnej aplikacji klienckiej, należy dodać wpis tajny lub certyfikat do działania jako *poświadczenia* tej aplikacji, aby można było uwierzytelnić się jako sam, bez interakcji z użytkownikiem.

Poświadczenia można dodać do rejestracji aplikacji klienckiej za pomocą [Azure Portal](#add-client-credentials-by-using-the-azure-portal) lub przy użyciu narzędzia wiersza polecenia, takiego jak [PowerShell](#add-client-credentials-by-using-powershell).

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Dodawanie poświadczeń klienta przy użyciu Azure Portal

Aby dodać poświadczenia do rejestracji aplikacji poufnej aplikacji klienta, wykonaj kroki opisane w [przewodniku szybki start: Zarejestruj aplikację na platformie tożsamości firmy Microsoft](../articles/active-directory/develop/quickstart-register-app.md) dla typu poświadczenia, które chcesz dodać:

* [Dodawanie klucza tajnego klienta](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [Dodawanie certyfikatu](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>Dodawanie poświadczeń klienta przy użyciu programu PowerShell

Alternatywnie można dodać poświadczenia podczas rejestrowania aplikacji na platformie tożsamości firmy Microsoft przy użyciu programu PowerShell.

Przykład kodu [Active-Directory-dotnetcore-demona-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) w witrynie GitHub pokazuje, jak dodać klucz tajny aplikacji lub certyfikat podczas rejestrowania aplikacji:

- Aby uzyskać szczegółowe informacje na temat dodawania **klucza tajnego klienta** przy użyciu programu PowerShell, zobacz [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Aby uzyskać szczegółowe informacje na temat dodawania **certyfikatu** przy użyciu programu PowerShell, zobacz [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
