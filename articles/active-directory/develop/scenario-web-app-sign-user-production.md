---
title: Przenoszenie aplikacji sieci Web, która loguje użytkowników do środowiska produkcyjnego | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje użytkowników (Przenieś do środowiska produkcyjnego)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c7abad31c9936729b8d9c19ed2efcb841ac103ca
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756236"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Aplikacja internetowa, która loguje użytkowników: Przenieś do środowiska produkcyjnego

Teraz, gdy wiesz już, jak uzyskać token do wywoływania interfejsów API sieci Web, Dowiedz się, jak przenieść go do środowiska produkcyjnego.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

> [!NOTE]
> Gdy użytkownicy logują się do aplikacji sieci Web po raz pierwszy, będą musieli wyrazić zgodę. Jednak w niektórych organizacjach użytkownicy mogą zobaczyć komunikat podobny do następującego:
>
> *Nazwa użytkownika musi mieć uprawnienia dostępu do zasobów w organizacji, które mogą przyznawać tylko Administratorzy. Poproś administratora o przyznanie uprawnień do tej aplikacji, zanim będzie można jej używać.*
>
> Wynika to z faktu, że administrator dzierżawy **wyłączył** możliwość wyrażania zgody przez użytkowników. W takim przypadku należy skontaktować się z administratorami dzierżawy, aby umożliwić administratorowi zgodę na zakresy wymagane przez aplikację.

## <a name="same-site"></a>Ta sama witryna

Upewnij się, że rozumiesz potencjalne problemy z nowymi wersjami przeglądarki Chrome: [jak obsłużyć zmiany plików cookie SameSite w przeglądarce Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md).

Pakiet NuGet Microsoft. Identity. Web obsługuje najczęstsze problemy z SameSite.

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Głębokie szczegółowe: samouczek aplikacji sieci Web ASP.NET Core

Dowiedz się więcej na temat innych sposobów logowania użytkowników przy użyciu tego samouczka ASP.NET Core: 

[Zezwól aplikacjom sieci Web na logowanie użytkowników i wywoływanie interfejsów API za pomocą platformy tożsamości firmy Microsoft dla deweloperów](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Ten samouczek progresywny zawiera kod gotowy do użycia w środowisku produkcyjnym dla aplikacji sieci Web, w tym sposób dodawania logowania przy użyciu kont w programie:

- Twoja organizacja
- Wiele organizacji
- Konta służbowe lub osobiste konta Microsoft
- [Azure AD B2C](../../active-directory-b2c/overview.md)
- Chmury narodowe

## <a name="sample-code-java-web-app"></a>Przykładowy kod: aplikacja internetowa Java

Dowiedz się więcej o aplikacji sieci Web Java z tego przykładu w witrynie GitHub: 

[Aplikacja sieci Web w języku Java, która loguje użytkowników z platformą tożsamości firmy Microsoft i wywołuje Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>Następne kroki

Gdy aplikacja sieci Web loguje się do użytkowników, może wywoływać interfejsy API sieci Web w imieniu zalogowanych użytkowników. Wywoływanie interfejsów API sieci Web z aplikacji sieci Web to obiekt w następującym scenariuszu: [aplikacja sieci Web, która wywołuje interfejsy API sieci Web](scenario-web-app-call-api-overview.md).