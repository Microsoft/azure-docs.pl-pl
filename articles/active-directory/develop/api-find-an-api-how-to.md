---
title: Znajdowanie interfejsu API dla aplikacji nieopracowanej przez użytkownika | Azure
description: Jak skonfigurować uprawnienia potrzebne do uzyskiwania dostępu do określonego interfejsu API w niestandardowej aplikacji usługi Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 28cfb3d8b09c9661d16ac6e7146c50e7043d913a
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581962"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Jak znaleźć konkretny interfejs API wymagany dla aplikacji niestandardowej

Dostęp do interfejsów API wymaga konfiguracji zakresów i ról dostępu. Jeśli chcesz uwidocznić interfejsy API sieci Web aplikacji zasobów dla aplikacji klienckich, skonfiguruj zakresy dostępu i role dla interfejsu API. Jeśli aplikacja kliencka ma uzyskiwać dostęp do internetowego interfejsu API, należy skonfigurować uprawnienia dostępu do interfejsu API w rejestracji aplikacji.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurowanie aplikacji zasobów na potrzeby uwidoczniania internetowych interfejsów API

Po udostępnieniu interfejsu API sieci Web interfejs API zostanie wyświetlony na liście **Wybierz interfejs API** podczas dodawania uprawnień do rejestracji aplikacji. Aby dodać zakresy dostępu, wykonaj kroki opisane w temacie [Konfigurowanie aplikacji do uwidaczniania interfejsów API sieci Web](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do interfejsów API sieci Web

Po dodaniu uprawnień do rejestracji aplikacji można **dodać dostęp do interfejsu API** do uwidocznionych interfejsów API sieci Web. Aby uzyskać dostęp do interfejsów API sieci Web, wykonaj kroki opisane w temacie [Konfigurowanie aplikacji klienckiej do uzyskiwania dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Następne kroki

- [Zrozumienie manifestu aplikacji Azure Active Directory](./reference-app-manifest.md)