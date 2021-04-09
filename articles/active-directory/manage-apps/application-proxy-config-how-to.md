---
title: Jak skonfigurować aplikację serwera proxy aplikacji | Microsoft Docs
description: Dowiedz się, jak utworzyć i skonfigurować aplikację serwera proxy aplikacji w kilku prostych krokach
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/18/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f1488986dc38bba89a5b9a109a444b5f0d81f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255207"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Jak skonfigurować aplikację serwera proxy aplikacji

Ten artykuł pomaga zrozumieć, jak skonfigurować aplikację serwera proxy aplikacji w usłudze Azure AD w celu udostępnienia aplikacji lokalnych w chmurze.

## <a name="recommended-documents"></a>Zalecane dokumenty

Aby dowiedzieć się więcej o początkowych konfiguracjach i tworzeniu aplikacji serwera proxy aplikacji za pomocą portalu administracyjnego, postępuj zgodnie z [serwer proxy aplikacji usługi Azure AD publikowanie aplikacji za pomocą platformy Azure](application-proxy-add-on-premises-application.md).

Aby uzyskać szczegółowe informacje na temat konfigurowania łączników, zobacz [Włączanie serwera proxy aplikacji w Azure Portal](application-proxy-add-on-premises-application.md).

Aby uzyskać informacje dotyczące przekazywania certyfikatów i używania domen niestandardowych, zobacz [Praca z domenami niestandardowymi w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Tworzenie aplikacji/Ustawianie adresów URL

Jeśli korzystasz z kroków w temacie [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md) i wystąpi błąd podczas tworzenia aplikacji, zobacz szczegóły błędu, aby uzyskać informacje i sugestie dotyczące sposobu naprawy aplikacji. Większość komunikatów o błędach zawiera sugerowaną poprawkę. Aby uniknąć typowych błędów, należy sprawdzić:

- Jesteś administratorem z uprawnieniami do tworzenia aplikacji serwera proxy aplikacji
- Wewnętrzny adres URL jest unikatowy
- Zewnętrzny adres URL jest unikatowy
- Adresy URL zaczynają się od http lub https i kończą się znakiem "/"
- Adres URL powinien być nazwą domeny, a nie adresem IP

Komunikat o błędzie powinien być wyświetlany w prawym górnym rogu podczas tworzenia aplikacji. Możesz również wybrać ikonę powiadomienia, aby wyświetlić komunikaty o błędach.

![Pokazuje, gdzie znaleźć monit o powiadomienie w Azure Portal](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfiguruj łączniki/grupy łączników

Jeśli masz trudności z skonfigurowaniem aplikacji z powodu ostrzeżenia dotyczące łączników i grup łączników, zobacz instrukcje dotyczące włączania serwera proxy aplikacji w celu uzyskania szczegółowych informacji na temat pobierania łączników. Jeśli chcesz dowiedzieć się więcej na temat łączników, zobacz [dokumentację łączników](application-proxy-connectors.md).

Jeśli łączniki są nieaktywne, oznacza to, że nie mogą nawiązać połączenia z usługą. Często jest to spowodowane tym, że wszystkie wymagane porty nie są otwarte. Aby wyświetlić listę wymaganych portów, zapoznaj się z sekcją wymagania wstępne w dokumentacji Włączanie serwera proxy aplikacji.

## <a name="upload-certificates-for-custom-domains"></a>Przekazywanie certyfikatów dla domen niestandardowych

Domeny niestandardowe pozwalają określić domenę zewnętrznych adresów URL. Aby można było używać domen niestandardowych, należy przekazać certyfikat dla tej domeny. Aby uzyskać informacje na temat korzystania z domen niestandardowych i certyfikatów, zobacz [Praca z domenami niestandardowymi w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).

Jeśli napotykasz problemy z przekazywaniem certyfikatu, poszukaj komunikatów o błędach w portalu, aby uzyskać dodatkowe informacje na temat problemu z certyfikatem. Typowe problemy z certyfikatami obejmują:

- Wygasły certyfikat
- Certyfikat jest podpisany z podpisem własnym
- Brak klucza prywatnego w certyfikacie

Komunikat o błędzie jest wyświetlany w prawym górnym rogu podczas próby przekazania certyfikatu. Możesz również wybrać ikonę powiadomienia, aby wyświetlić komunikaty o błędach.

## <a name="next-steps"></a>Następne kroki

[Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)
